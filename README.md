# Automa-es-Python-para-Seguran-a-e-Monitoramento

import re
import json
import time
import shutil
import unicodedata
from datetime import datetime
from pathlib import Path

import pandas as pd

from selenium import webdriver
from selenium.webdriver.common.by import By
from selenium.webdriver.chrome.options import Options
from selenium.webdriver.common.action_chains import ActionChains
from selenium.webdriver.support.ui import WebDriverWait
from selenium.webdriver.support import expected_conditions as EC


URL = "https://example.local/login"

USUARIO = "admin_user"
SENHA = "CHANGE_ME"

PASTA_DESTINO = Path("./exports")
PASTA_DOWNLOAD = Path("./downloads")

EMAILS_DESTINO = [
    """destinatario@example.com"""
]


def criar_driver():
    options = Options()
    options.add_argument("--start-maximized")
    options.add_argument("--ignore-certificate-errors")
    options.add_argument("--allow-running-insecure-content")

    prefs = {
        "download.default_directory": str(PASTA_DOWNLOAD),
        "download.prompt_for_download": False,
        "download.directory_upgrade": True,
        "safebrowsing.enabled": True,
    }

    options.add_experimental_option("prefs", prefs)

    driver = webdriver.Chrome(options=options)
    driver.maximize_window()
    return driver


def esperar(driver, segundos=30):
    return WebDriverWait(driver, segundos)


def normalizar_texto(txt):
    txt = str(txt or "").lower()
    txt = unicodedata.normalize("NFD", txt)
    txt = "".join(c for c in txt if unicodedata.category(c) != "Mn")
    return txt.strip()


def clicar_js(driver, elemento):
    driver.execute_script(
        "arguments[0].scrollIntoView({block:'center', inline:'center'});",
        elemento,
    )
    time.sleep(0.3)
    driver.execute_script("arguments[0].click();", elemento)


def mouse_move(driver, x, y, pausa=0):
    driver.execute_cdp_cmd("Input.dispatchMouseEvent", {
        "type": "mouseMoved",
        "x": x,
        "y": y,
        "button": "none"
    })
    if pausa:
        time.sleep(pausa)


def mouse_click(driver, x, y, pausa=0.5):
    mouse_move(driver, x, y, pausa=pausa)
    driver.execute_cdp_cmd("Input.dispatchMouseEvent", {
        "type": "mousePressed",
        "x": x,
        "y": y,
        "button": "left",
        "clickCount": 1
    })
    driver.execute_cdp_cmd("Input.dispatchMouseEvent", {
        "type": "mouseReleased",
        "x": x,
        "y": y,
        "button": "left",
        "clickCount": 1
    })
    time.sleep(1)


# Compatibilidade com nomes usados em versoes anteriores do script.
def mover_mouse(driver, x, y, pausa=0):
    mouse_move(driver, x, y, pausa=pausa)


def clicar_mouse(driver, x, y, pausa=0.5):
    mouse_click(driver, x, y, pausa=pausa)


def clicar_por_texto(driver, textos, timeout=15):
    if isinstance(textos, str):
        textos = [textos]

    textos_norm = [normalizar_texto(t) for t in textos]
    limite = time.time() + timeout

    while time.time() < limite:
        ok = driver.execute_script(
            """
            const alvos = arguments[0];

            function norm(txt) {
                return String(txt || "")
                    .toLowerCase()
                    .normalize("NFD")
                    .replace(/[\\u0300-\\u036f]/g, "")
                    .trim();
            }

            const els = [...document.querySelectorAll("button, a, div, span, li, p, label")];

            for (const el of els) {
                const texto = norm(el.innerText || el.textContent || "");
                const title = norm(el.getAttribute("title") || "");
                const aria = norm(el.getAttribute("aria-label") || "");
                const dataTitle = norm(el.getAttribute("data-title") || "");

                for (const alvo of alvos) {
                    if (
                        texto.includes(alvo) ||
                        title.includes(alvo) ||
                        aria.includes(alvo) ||
                        dataTitle.includes(alvo)
                    ) {
                        el.scrollIntoView({block: "center", inline: "center"});
                        el.click();
                        return true;
                    }
                }
            }

            return false;
            """,
            textos_norm,
        )

        if ok:
            return True

        time.sleep(0.5)

    return False


def fechar_popups(driver, tentativas=10):
    for _ in range(tentativas):
        fechou = False

        xpaths = [
            "//span[normalize-space()='Fechar']/ancestor::button[1]",
            "//button[contains(normalize-space(),'Fechar')]",
            "//span[normalize-space()='Cancelar']/ancestor::button[1]",
            "//button[contains(normalize-space(),'Cancelar')]",
            "//*[contains(@class,'el-message-box__close')]",
            "//*[contains(@class,'el-dialog__close')]",
            "//*[contains(@class,'el-icon-close')]",
            "//*[contains(@class,'is-close')]",
        ]

        for xpath in xpaths:
            try:
                elementos = driver.find_elements(By.XPATH, xpath)
                elementos = [e for e in elementos if e.is_displayed() and e.is_enabled()]

                if elementos:
                    driver.execute_script("arguments[0].click();", elementos[0])
                    time.sleep(1)
                    fechou = True
                    break
            except Exception:
                pass

        if not fechou:
            break


def login(driver):
    wait = esperar(driver, 40)

    driver.get(URL)
    time.sleep(5)

    fechar_popups(driver, tentativas=12)
    time.sleep(2)

    ja_logado = driver.find_elements(
        By.XPATH,
        "//*[contains(text(),'HikCentral Professional Web Client') "
        "or contains(text(),'Manutenção') "
        "or contains(text(),'Gestão de recursos') "
        "or contains(text(),'Todos os módulos')]",
    )

    campos_usuario = driver.find_elements(
        By.XPATH,
        "//input[contains(@placeholder,'utilizador') "
        "or contains(@placeholder,'Nome') "
        "or contains(@placeholder,'Usu') "
        "or contains(@placeholder,'user')]",
    )

    if ja_logado and not campos_usuario:
        print("Sistema já está aberto/logado. Continuando...")
        fechar_popups(driver, tentativas=12)
        return

    if not campos_usuario:
        print("Campo de login ainda não apareceu. Aguardando mais 10 segundos...")
        time.sleep(10)
        fechar_popups(driver, tentativas=12)

        campos_usuario = driver.find_elements(
            By.XPATH,
            "//input[contains(@placeholder,'utilizador') "
            "or contains(@placeholder,'Nome') "
            "or contains(@placeholder,'Usu') "
            "or contains(@placeholder,'user')]",
        )

    if not campos_usuario:
        print("Não encontrei tela de login. Provavelmente o sistema já está logado.")
        return

    campo_usuario = campos_usuario[0]
    campo_usuario.clear()
    campo_usuario.send_keys(USUARIO)

    campo_senha = wait.until(
        EC.presence_of_element_located(
            (
                By.XPATH,
                "//input[@type='password' "
                "or contains(@placeholder,'Palavra') "
                "or contains(@placeholder,'Senha') "
                "or contains(@placeholder,'password')]",
            )
        )
    )
    campo_senha.clear()
    campo_senha.send_keys(SENHA)

    botao_login = wait.until(
        EC.element_to_be_clickable(
            (
                By.XPATH,
                "//button[contains(.,'Iniciar') "
                "or contains(.,'Login') "
                "or contains(.,'Entrar')]",
            )
        )
    )
    botao_login.click()

    print("Login enviado. Aguardando 15 segundos...")
    time.sleep(15)

    fechar_popups(driver, tentativas=12)


def abrir_gestao_recursos(driver):
    fechar_popups(driver)

    print("Abrindo menu lateral por hover...")

    # Botao vermelho dos 4 quadradinhos no canto superior esquerdo.
    mouse_move(driver, 18, 70, pausa=1.5)

    texto = normalizar_texto(driver.execute_script("return document.body.innerText || ''"))
    if "gestao de recursos" not in texto:
        mouse_click(driver, 18, 70, pausa=0.8)
        time.sleep(1.5)

    # Tenta pelo texto do menu aberto. Se falhar, usa a coordenada da sua tela.
    ok = clicar_por_texto(driver, ["Gestão de recursos", "Gestao de recursos", "GestÃ£o de recursos"], timeout=5)

    if not ok:
        print("Clicando em Gestão de recursos por coordenada...")
        mouse_click(driver, 87, 170, pausa=0.8)

    time.sleep(8)
    fechar_popups(driver, tentativas=12)


def selecionar_area(driver):
    fechar_popups(driver)

    print("Clicando no icone Area...")

    # A funcao antiga tentava achar o item Area no DOM e parava com erro.
    # Aqui fazemos o clique direto no icone lateral da tela Gestao de recursos.
    time.sleep(3)
    mouse_click(driver, 20, 137, pausa=0.8)
    time.sleep(5)
    fechar_popups(driver, tentativas=12)


def arquivos_excel_download():
    pastas = [PASTA_DOWNLOAD, PASTA_DESTINO]
    arquivos = []
    for pasta in pastas:
        try:
            arquivos.extend(pasta.glob("*.xls*"))
        except Exception:
            pass
    return [
        a for a in arquivos
        if a.is_file()
        and not a.name.endswith(".crdownload")
        and not a.name.endswith(".tmp")
        and not a.name.startswith("~$")
    ]


def arquivo_mais_recente_antes():
    arquivos = arquivos_excel_download()
    if not arquivos:
        return None
    return max(arquivos, key=lambda p: p.stat().st_mtime)


def clicar_exportar(driver):
    fechar_popups(driver)

    print("Clicando em Exportar...")

    xpaths = [
        "//button[@title='Exportar']",
        "//button[contains(@title,'Export')]",
        "//*[contains(@class,'icomoon-common_export')]/ancestor::button[1]",
        "//*[contains(@class,'common_export')]/ancestor::button[1]",
        "//*[contains(@class,'export')]/ancestor::button[1]",
    ]

    for xpath in xpaths:
        try:
            elementos = driver.find_elements(By.XPATH, xpath)
            elementos = [e for e in elementos if e.is_displayed() and e.is_enabled()]
            if elementos:
                clicar_js(driver, elementos[0])
                time.sleep(2)
                fechar_popups(driver, tentativas=5)
                clicar_por_texto(driver, ["Exportar", "Excel", "Confirmar", "OK"], timeout=2)
                return True
        except Exception:
            pass

    # Coordenadas alternativas do botão exportar na barra superior da tela de câmera.
    for x, y in [(346, 116), (445, 124), (84, 116), (82, 62)]:
        try:
            mouse_click(driver, x, y, pausa=0.8)
            time.sleep(2)
            fechar_popups(driver, tentativas=5)
            clicar_por_texto(driver, ["Exportar", "Excel", "Confirmar", "OK"], timeout=2)
            return True
        except Exception:
            pass

    return False


def exportar_excel(driver):
    antes = arquivo_mais_recente_antes()
    inicio = time.time()

    fechar_popups(driver)
    ok = clicar_exportar(driver)
    if not ok:
        print("Aviso: não consegui confirmar o clique em Exportar; vou verificar se algum arquivo foi baixado mesmo assim.")

    print("Aguardando download do Excel...")

    limite = time.time() + 180

    while time.time() < limite:
        fechar_popups(driver, tentativas=2)

        # Enquanto existir .crdownload recente, aguarda terminar.
        parciais = list(PASTA_DOWNLOAD.glob("*.crdownload"))
        parciais_recentes = [p for p in parciais if p.stat().st_mtime >= inicio - 5]
        if parciais_recentes:
            time.sleep(2)
            continue

        arquivos = arquivos_excel_download()
        if arquivos:
            mais_recente = max(arquivos, key=lambda p: p.stat().st_mtime)
            if mais_recente.stat().st_mtime >= inicio - 5:
                time.sleep(2)
                return mais_recente
            if antes is not None and mais_recente.resolve() != antes.resolve() and mais_recente.stat().st_mtime > antes.stat().st_mtime:
                time.sleep(2)
                return mais_recente

        time.sleep(1)

    arquivos = arquivos_excel_download()
    if arquivos:
        mais_recente = max(arquivos, key=lambda p: p.stat().st_mtime)
        print("Download novo não foi detectado. Usando Excel mais recente encontrado:", mais_recente)
        return mais_recente

    raise TimeoutError("Download nao foi concluido dentro do tempo esperado.")


def mover_para_destino(arquivo):
    PASTA_DESTINO.mkdir(parents=True, exist_ok=True)

    data = datetime.now().strftime("%Y%m%d_%H%M%S")
    destino = PASTA_DESTINO / f"exportacao_monitoring_{data}{arquivo.suffix}"

    shutil.move(str(arquivo), str(destino))
    return destino


def achar_coluna(df, possibilidades):
    def limpar(txt):
        txt = normalizar_texto(txt)
        txt = re.sub(r"[^a-z0-9 ]", "", txt)
        return txt.strip()

    cols_limpas = {col: limpar(col) for col in df.columns}

    print("Colunas encontradas no Excel:")
    for col in df.columns:
        print("-", col)

    for termo in possibilidades:
        termo_limpo = limpar(termo)
        for col, col_limpa in cols_limpas.items():
            if termo_limpo in col_limpa or col_limpa in termo_limpo:
                return col

    return None

def extrair_uf(nome):
    if pd.isna(nome):
        return ""

    texto = str(nome).upper().strip()

    match = re.search(
        r"\b(AC|AL|AP|AM|BA|CE|DF|ES|GO|MA|MT|MS|MG|PA|PB|PR|PE|PI|RJ|RN|RS|RO|RR|SC|SP|SE|TO|SI)\b$",
        texto,
    )
    if match:
        return match.group(1)

    match = re.search(
        r"-\s*(AC|AL|AP|AM|BA|CE|DF|ES|GO|MA|MT|MS|MG|PA|PB|PR|PE|PI|RJ|RN|RS|RO|RR|SC|SP|SE|TO|SI)\b",
        texto,
    )
    if match:
        return match.group(1)

    return "N/I"


def normalizar_status(valor):
    texto = normalizar_texto(valor)

    if "online" in texto:
        return "Online"

    if "offline" in texto:
        return "Offline"

    return "Indefinido"


def processar_excel(arquivo_excel):
    bruto = pd.read_excel(arquivo_excel, header=None)

    linha_cabecalho = None

    for i in range(min(40, len(bruto))):
        valores = [normalizar_texto(x) for x in bruto.iloc[i].fillna("").tolist()]
        linha = " | ".join(valores)

        tem_nome_camera = "nome camera" in linha or "nome camara" in linha
        tem_estado = "estado online" in linha or "estado" in linha or "status" in linha
        tem_area = "area" in linha

        if tem_nome_camera and tem_estado and tem_area:
            linha_cabecalho = i
            break

    if linha_cabecalho is None:
        print("Primeiras linhas lidas do Excel:")
        for i in range(min(15, len(bruto))):
            print(i, "=>", " | ".join(str(x) for x in bruto.iloc[i].fillna("").tolist()))
        raise Exception("Nao encontrei a linha de cabecalho com Nome camara / Estado online / Area.")

    df = pd.read_excel(arquivo_excel, header=linha_cabecalho)
    df = df.dropna(axis=1, how="all")
    df = df.dropna(axis=0, how="all")

    primeira_coluna = df.columns[0]
    df = df[df[primeira_coluna].astype(str).str.strip() != str(primeira_coluna).strip()]

    col_camera = achar_coluna(df, ["Nome câmera", "Nome camera", "Nome câmara", "Nome camara"])
    col_dispositivo = achar_coluna(df, ["Nome disp.", "Nome disp", "Nome dispositivo", "Dispositivo"])
    col_status = achar_coluna(df, ["Estado online", "Estado", "Status"])
    col_area = achar_coluna(df, ["Área", "Area"])

    if col_status is None:
        raise Exception("Nao encontrei a coluna Estado online no Excel.")

    if col_area is None and col_dispositivo is None:
        raise Exception("Nao encontrei a coluna Area ou Nome disp. no Excel.")

    if col_camera is None:
        col_camera = df.columns[0]

    if col_area is not None:
        df["AGENCIA_NORMALIZADA"] = df[col_area].astype(str).str.strip()
    else:
        df["AGENCIA_NORMALIZADA"] = df[col_dispositivo].astype(str).str.strip()

    df["NOME_CAMERA_NORMALIZADO"] = df[col_camera].astype(str).str.strip()
    df["STATUS_NORMALIZADO"] = df[col_status].apply(normalizar_status)
    df["UF"] = df["AGENCIA_NORMALIZADA"].apply(extrair_uf)

    df["CAMERAS_NORMALIZADAS"] = 1

    total_cameras = int(len(df))
    cameras_online = int((df["STATUS_NORMALIZADO"] == "Online").sum())
    cameras_offline = int((df["STATUS_NORMALIZADO"] == "Offline").sum())

    unidades_offline = (
        df.loc[df["STATUS_NORMALIZADO"] == "Offline"]
        .groupby(["UF", "AGENCIA_NORMALIZADA"], dropna=False)["CAMERAS_NORMALIZADAS"]
        .sum()
        .reset_index()
    )

    return {
        "df": df,
        "total_cameras": total_cameras,
        "cameras_online": cameras_online,
        "cameras_offline": cameras_offline,
        "unidades_offline": unidades_offline,
    }

def comparar_com_relatorios_antigos(arquivo_atual, resultado_atual):
    arquivo_atual = Path(arquivo_atual).resolve()
    historicos = sorted(
        [p for p in PASTA_DESTINO.glob("exportacao_monitoring_*.xls*") if p.resolve() != arquivo_atual],
        key=lambda p: p.stat().st_mtime
    )
    df_atual = resultado_atual["df"].copy()

    def chave(row):
        agencia = normalizar_texto(row.get("AGENCIA_NORMALIZADA", ""))
        camera = normalizar_texto(row.get("NOME_CAMERA_NORMALIZADO", ""))
        return f"{agencia}|{camera}"

    def data_do_nome(path):
        m = re.search(r"exportacao_monitoring_(\d{8})_(\d{6})", path.stem)
        if not m:
            return datetime.fromtimestamp(path.stat().st_mtime).strftime("%d/%m/%Y %H:%M")
        return datetime.strptime(m.group(1) + m.group(2), "%Y%m%d%H%M%S").strftime("%d/%m/%Y %H:%M")

    def resumo_resultado(path, resultado):
        total = int(resultado.get("total_cameras", 0))
        online = int(resultado.get("cameras_online", 0))
        offline = int(resultado.get("cameras_offline", 0))
        disp = round((online / total * 100), 2) if total else 0.0
        return {"arquivo": path.name, "data": data_do_nome(path), "total": total, "online": online, "offline": offline, "disponibilidade": disp}

    serie = []
    resultados_antigos = []
    for arquivo in historicos:
        try:
            res = processar_excel(arquivo)
            resultados_antigos.append((arquivo, res))
            serie.append(resumo_resultado(arquivo, res))
        except Exception as erro:
            print(f"Não foi possível processar histórico {arquivo.name}: {erro}")

    serie.append(resumo_resultado(arquivo_atual, resultado_atual))
    atual_off = int(resultado_atual.get("cameras_offline", 0))
    atual_total = int(resultado_atual.get("total_cameras", 0))
    atual_online = int(resultado_atual.get("cameras_online", 0))

    comparativo = {
        "tem_historico": bool(resultados_antigos), "qtd_relatorios_antigos": len(resultados_antigos),
        "arquivo_base_anterior": "", "offline_anterior": 0, "offline_atual": atual_off,
        "online_atual": atual_online, "total_atual": atual_total, "recuperadas": 0,
        "continuam_offline": 0, "novas_offline": 0, "percentual_melhoria_anterior": 0.0,
        "percentual_melhoria_primeiro": 0.0, "percentual_melhoria_media": 0.0,
        "media_offline_historica": 0.0, "melhor_data": "", "melhor_offline": atual_off,
        "pior_data": "", "pior_offline": atual_off, "serie": serie,
    }
    if not resultados_antigos:
        return comparativo

    arquivo_anterior, resultado_anterior = resultados_antigos[-1]
    comparativo["arquivo_base_anterior"] = arquivo_anterior.name
    comparativo["offline_anterior"] = int(resultado_anterior.get("cameras_offline", 0))

    df_ant = resultado_anterior["df"].copy()
    df_ant["CHAVE_COMP"] = df_ant.apply(chave, axis=1)
    df_atual["CHAVE_COMP"] = df_atual.apply(chave, axis=1)
    ant_off_keys = set(df_ant.loc[df_ant["STATUS_NORMALIZADO"] == "Offline", "CHAVE_COMP"])
    atual_off_keys = set(df_atual.loc[df_atual["STATUS_NORMALIZADO"] == "Offline", "CHAVE_COMP"])
    status_atual = dict(zip(df_atual["CHAVE_COMP"], df_atual["STATUS_NORMALIZADO"]))
    recuperadas = [k for k in ant_off_keys if status_atual.get(k) == "Online"]
    continuam = [k for k in ant_off_keys if k in atual_off_keys]
    novas = [k for k in atual_off_keys if k not in ant_off_keys]
    offline_anterior = comparativo["offline_anterior"]
    comparativo["recuperadas"] = len(recuperadas)
    comparativo["continuam_offline"] = len(continuam)
    comparativo["novas_offline"] = len(novas)
    comparativo["percentual_melhoria_anterior"] = round((len(recuperadas) / offline_anterior * 100), 2) if offline_anterior else 0.0
    offline_primeiro = int(resultados_antigos[0][1].get("cameras_offline", 0))
    comparativo["percentual_melhoria_primeiro"] = round(((offline_primeiro - atual_off) / offline_primeiro * 100), 2) if offline_primeiro else 0.0
    valores_offline = [int(res.get("cameras_offline", 0)) for _, res in resultados_antigos]
    media_offline = sum(valores_offline) / len(valores_offline) if valores_offline else 0
    comparativo["media_offline_historica"] = round(media_offline, 2)
    comparativo["percentual_melhoria_media"] = round(((media_offline - atual_off) / media_offline * 100), 2) if media_offline else 0.0
    melhor = min(serie, key=lambda x: x["offline"])
    pior = max(serie, key=lambda x: x["offline"])
    comparativo["melhor_data"] = melhor["data"]
    comparativo["melhor_offline"] = melhor["offline"]
    comparativo["pior_data"] = pior["data"]
    comparativo["pior_offline"] = pior["offline"]
    return comparativo


def comparar_com_relatorio_anterior(arquivo_atual, resultado_atual):
    return comparar_com_relatorios_antigos(arquivo_atual, resultado_atual)


def gerar_txt(resultado):
    data_atualizacao = datetime.now().strftime("%d/%m/%Y %H:%M:%S")
    caminho = PASTA_DESTINO / "contagem_monitoring.txt"

    df = resultado["df"].copy()
    offline = df[df["STATUS_NORMALIZADO"] == "Offline"].copy()

    palavras_criticas = ["tesouraria", "taa", "ptz", "retaguarda"]

    def categoria_critica(nome):
        nome_norm = normalizar_texto(nome)
        achadas = [p.upper() for p in palavras_criticas if p in nome_norm]
        return ", ".join(achadas)

    offline["CRITICIDADE"] = offline["NOME_CAMERA_NORMALIZADO"].apply(categoria_critica)
    offline_criticas = offline[offline["CRITICIDADE"] != ""].copy()

    unidades_offline = resultado["unidades_offline"]

    linhas = [
        f"Atualizacao: {data_atualizacao}",
        "",
        f"Total de cameras: {resultado['total_cameras']}",
        f"Cameras online: {resultado['cameras_online']}",
        f"Cameras offline: {resultado['cameras_offline']}",
        f"Unidades offline: {len(unidades_offline)}",
        f"Cameras criticas offline (tesouraria/taa/ptz/retaguarda): {len(offline_criticas)}",
        "",
    ]

    comp = resultado.get("comparativo", {})
    if comp.get("tem_anterior"):
        linhas.extend([
            "COMPARATIVO COM RELATORIO ANTERIOR:",
            f"Relatorio anterior: {comp.get('arquivo_anterior', '')}",
            f"Offline anterior: {comp.get('offline_anterior', 0)}",
            f"Offline atual: {comp.get('offline_atual', 0)}",
            f"Cameras recuperadas: {comp.get('recuperadas', 0)}",
            f"Melhoria das cameras que estavam offline: {comp.get('percentual_melhoria', 0)}%",
            f"Novas cameras offline: {comp.get('novas_offline', 0)}",
            "",
        ])

    linhas.extend([
        "UNIDADES OFFLINE:",
        "",
    ])

    if unidades_offline.empty:
        linhas.append("Nenhuma unidade offline encontrada.")
    else:
        for _, row in unidades_offline.iterrows():
            linhas.append(
                f"{row['UF']} | {row['AGENCIA_NORMALIZADA']} | Cameras offline: {row['CAMERAS_NORMALIZADAS']}"
            )

    linhas.extend(["", "CAMERAS OFFLINE:", ""])

    if offline.empty:
        linhas.append("Nenhuma camera offline encontrada.")
    else:
        for _, row in offline.iterrows():
            criticidade = row.get("CRITICIDADE", "")
            marcador = f" | CRITICA: {criticidade}" if criticidade else ""
            linhas.append(
                f"{row['UF']} | {row['AGENCIA_NORMALIZADA']} | {row['NOME_CAMERA_NORMALIZADO']}{marcador}"
            )

    caminho.write_text("\\n".join(linhas), encoding="utf-8")
    return caminho


def gerar_dashboard_html(resultado):
    df = resultado["df"].copy()
    data_atualizacao = datetime.now().strftime("%d/%m/%Y %H:%M:%S")

    def categoria_critica(nome):
        nome_norm = normalizar_texto(nome)
        achadas = []
        if "ptz" in nome_norm:
            achadas.append("PTZ")
        if "tesouraria" in nome_norm:
            achadas.append("TESOURARIA")
        if "taa" in nome_norm:
            achadas.append("TAA")
        if not achadas and "camera" in nome_norm:
            achadas.append("CAMERA")
        return ", ".join(achadas)

    if "NOME_CAMERA_NORMALIZADO" not in df.columns:
        df["NOME_CAMERA_NORMALIZADO"] = ""
    df["CRITICIDADE"] = df["NOME_CAMERA_NORMALIZADO"].apply(categoria_critica)

    dados_json = df[["UF", "AGENCIA_NORMALIZADA", "NOME_CAMERA_NORMALIZADO", "STATUS_NORMALIZADO", "CAMERAS_NORMALIZADAS", "CRITICIDADE"]].to_json(orient="records", force_ascii=False)
    comparativo_json = json.dumps(resultado.get("comparativo", {}), ensure_ascii=False)
    caminho = PASTA_DESTINO / "dashboard_monitoring.html"

    html = """<!DOCTYPE html><html lang="pt-BR"><head><meta charset="UTF-8"><meta name="viewport" content="width=device-width, initial-scale=1.0"><title>Security Monitoring Dashboard - CLIENT</title><script src="https://cdn.jsdelivr.net/npm/chart.js"></script><style>
:root{--green:#006b3f;--green2:#00492d;--yellow:#f2b705;--red:#b42318;--redbg:#fee4e2;--soft:#e7f4ee;--bg:#f4f7f5;--card:#fff;--text:#17201b;--muted:#667085;--line:#dfe7e2;--shadow:0 10px 26px rgba(0,73,45,.10)}*{box-sizing:border-box}body{margin:0;background:linear-gradient(180deg,#fbfdfc,var(--bg));color:var(--text);font-family:Inter,Segoe UI,Arial,sans-serif}header{background:linear-gradient(135deg,var(--green2),var(--green));color:#fff;padding:22px 32px;border-bottom:5px solid var(--yellow)}.topbar{display:flex;justify-content:space-between;gap:18px;align-items:center;flex-wrap:wrap}.brand{display:flex;gap:14px;align-items:center}.mark{width:54px;height:54px;border-radius:15px;background:#fff;display:grid;place-items:center;color:var(--green);font-weight:900}.brand small{display:block;text-transform:uppercase;letter-spacing:.08em;opacity:.88;font-weight:700}.brand h1{margin:2px 0 0;font-size:25px}.meta{text-align:right;font-size:13px;opacity:.9}main{max-width:1500px;margin:0 auto;padding:24px 28px 42px}.toolbar{display:grid;grid-template-columns:1fr 1fr 1.5fr;gap:12px;margin-bottom:16px}.box,.card,.panel,.call{background:var(--card);border:1px solid var(--line);border-radius:14px;box-shadow:var(--shadow)}.box{padding:13px}label{display:block;color:var(--muted);font-size:11px;font-weight:800;text-transform:uppercase;margin-bottom:6px}select,input{width:100%;border:1px solid var(--line);border-radius:10px;padding:10px 11px;background:#fff;font-size:14px}.cards{display:grid;grid-template-columns:repeat(8,minmax(130px,1fr));gap:12px;margin-bottom:16px}.card{padding:15px;position:relative;overflow:hidden}.card:before{content:"";position:absolute;top:0;left:0;right:0;height:4px;background:linear-gradient(90deg,var(--green),var(--yellow))}.card h3{margin:0;color:var(--muted);font-size:12px;text-transform:uppercase}.card strong{display:block;font-size:26px;margin-top:7px;color:var(--green2)}.card small{display:block;color:var(--muted);margin-top:3px}.card.bad strong,.neg{color:var(--red)!important}.card.warn strong{color:#946200}.ico{display:inline-grid;place-items:center;width:26px;height:26px;border-radius:8px;background:var(--soft);color:var(--green2);font-weight:900;margin-bottom:7px}.bad .ico{background:var(--redbg);color:var(--red)}.warn .ico{background:#fff4cc;color:#946200}.callout{display:grid;grid-template-columns:repeat(4,1fr);gap:12px;margin-bottom:16px}.call{padding:15px;background:linear-gradient(135deg,var(--soft),#fff)}.call.bad{background:linear-gradient(135deg,var(--redbg),#fff)}.call.warn{background:linear-gradient(135deg,#fff4cc,#fff)}.call span{font-size:11px;color:var(--muted);font-weight:900;text-transform:uppercase}.call strong{display:block;margin-top:6px;font-size:20px;color:var(--green2)}.grid{display:grid;grid-template-columns:1fr 1fr;gap:16px}.panel{padding:16px}.panel.wide{grid-column:1/-1}.panel h2{margin:0 0 12px;font-size:18px;color:var(--green2)}.chart{height:310px}table{width:100%;border-collapse:collapse;font-size:13px}th{background:#f1f5f3;color:var(--green2);text-align:left;padding:10px;border-bottom:1px solid var(--line);font-size:11px;text-transform:uppercase}td{padding:10px;border-bottom:1px solid var(--line);vertical-align:top}.pill{display:inline-flex;border-radius:999px;padding:4px 8px;font-size:11px;font-weight:900}.pill.ok{background:var(--soft);color:var(--green2)}.pill.bad{background:var(--redbg);color:var(--red)}.pill.warn{background:#fff4cc;color:#946200}.footer{margin-top:16px;color:var(--muted);font-size:12px;text-align:center}@media(max-width:1150px){.cards,.grid,.callout,.toolbar{grid-template-columns:1fr}.meta{text-align:left}}
</style></head><body><header><div class="topbar"><div class="brand"><div class="mark">CLIENT</div><div><small>Enterprise - Operação CFTV</small><h1>Dashboard Gerencial de Câmeras</h1></div></div><div class="meta">Atualização: __DATE__<br>Base: exportação CFTV</div></div></header><main><section class="toolbar"><div class="box"><label>Estado</label><select id="filtroUf"><option value="">Todos</option></select></div><div class="box"><label>Visão</label><select id="filtroModo"><option value="">Todas as câmeras</option><option value="offline">Somente offline</option><option value="criticas">Críticas offline</option></select></div><div class="box"><label>Busca</label><input id="busca" type="search" placeholder="Agência, câmera, PTZ, TAA, tesouraria..."></div></section><section class="cards" id="indicadoresEmail"><div class="card"><span class="ico">T</span><h3>Total</h3><strong id="kTotal">0</strong><small>câmeras</small></div><div class="card"><span class="ico">ON</span><h3>Online</h3><strong id="kOnline">0</strong><small id="kOnlinePct">0%</small></div><div class="card bad"><span class="ico">OFF</span><h3>Offline</h3><strong id="kOffline">0</strong><small id="kOfflinePct">0%</small></div><div class="card warn"><span class="ico">AG</span><h3>Agências</h3><strong id="kAgOffline">0</strong><small>com offline</small></div><div class="card bad"><span class="ico">!</span><h3>Críticas</h3><strong id="kCriticas">0</strong><small>PTZ/TAA/Tesouraria</small></div><div class="card"><span class="ico">R</span><h3>Recuperadas</h3><strong id="kRecuperadas">0</strong><small>vs anterior</small></div><div class="card warn"><span class="ico">%</span><h3>Melhoria</h3><strong id="kMelhoria">0%</strong><small>offline histórico</small></div><div class="card"><span class="ico">D</span><h3>Disp.</h3><strong id="kDisp">0%</strong><small>geral</small></div></section><section class="callout"><div class="call bad"><span>Estado mais crítico</span><strong id="critica">-</strong><small id="criticaDetalhe">-</small></div><div class="call"><span>Comparativo histórico</span><strong id="histTitulo">Sem base</strong><small id="histDetalhe">-</small></div><div class="call warn"><span>Novas offline</span><strong id="novasOffline">0</strong><small>vs relatório anterior</small></div><div class="call"><span>Melhor marca histórica</span><strong id="melhorHistorico">-</strong><small id="melhorHistoricoDetalhe">-</small></div></section><section class="grid"><div class="panel"><h2>Online x Offline por Estado</h2><div class="chart"><canvas id="chartUfStack"></canvas></div></div><div class="panel"><h2>Evolução histórica de offline</h2><div class="chart"><canvas id="chartHistorico"></canvas></div></div><div class="panel wide"><h2>Comparativo Gerencial</h2><table><thead><tr><th>Relatórios antigos</th><th>Base anterior</th><th>Offline anterior</th><th>Offline atual</th><th>Recuperadas</th><th>Continuam offline</th><th>Novas offline</th><th>Melhoria vs média</th></tr></thead><tbody id="tbodyComparativo"></tbody></table></div><div class="panel wide"><h2>Câmeras Críticas Offline</h2><table><thead><tr><th>UF</th><th>Agência</th><th>Câmera</th><th>Indicador crítico</th></tr></thead><tbody id="tbodyCriticas"></tbody></table></div><div class="panel wide"><h2>Resumo por Estado</h2><table><thead><tr><th>UF</th><th>Online</th><th>Offline</th><th>Total</th><th>Disponibilidade</th><th>Avaliação</th></tr></thead><tbody id="tbodyUf"></tbody></table></div></section><div class="footer">Dashboard gerado automaticamente para avaliação gerencial do monitoramento CFTV.</div></main><script>
const dados = __DATA__; const comparativo = __COMP__; const filtroUf=document.getElementById('filtroUf'), filtroModo=document.getElementById('filtroModo'), busca=document.getElementById('busca'); let chartUfStack, chartHistorico;
function fmt(n){return Number(n||0).toLocaleString('pt-BR')} function pct(a,b){return b?(a/b*100):0} function fmtPct(n){return Number(n||0).toFixed(2)+'%'} function off(d){return d.STATUS_NORMALIZADO==='Offline'} function crit(d){return off(d)&&String(d.CRITICIDADE||'').trim()!==''} function uniq(a){return [...new Set(a)].filter(Boolean).sort()} function pill(d){if(d<95)return '<span class="pill bad">Crítico</span>'; if(d<99)return '<span class="pill warn">Atenção</span>'; return '<span class="pill ok">Dentro da meta</span>'}
function popular(){uniq(dados.map(d=>d.UF)).forEach(uf=>{let o=document.createElement('option');o.value=uf;o.textContent=uf;filtroUf.appendChild(o)})}
function base(){let uf=filtroUf.value, modo=filtroModo.value, q=busca.value.trim().toLowerCase();return dados.filter(d=>{if(uf&&d.UF!==uf)return false;if(modo==='offline'&&!off(d))return false;if(modo==='criticas'&&!crit(d))return false;if(q){let alvo=`${d.AGENCIA_NORMALIZADA||''} ${d.NOME_CAMERA_NORMALIZADO||''} ${d.CRITICIDADE||''}`.toLowerCase();if(!alvo.includes(q))return false}return true})}
function porUf(b){let m={};b.forEach(d=>{let uf=d.UF||'N/I';m[uf]??={uf,online:0,offline:0,total:0,criticas:0};m[uf].total++;if(off(d))m[uf].offline++;else m[uf].online++;if(crit(d))m[uf].criticas++});return Object.values(m).sort((a,b)=>b.offline-a.offline||b.criticas-a.criticas)}
function atualizar(){let b=base(), total=b.length, offline=b.filter(off), online=total-offline.length, criticas=b.filter(crit), agOff=new Set(offline.map(d=>d.AGENCIA_NORMALIZADA)).size, disp=pct(online,total), ufs=porUf(b);document.getElementById('kTotal').textContent=fmt(total);document.getElementById('kOnline').textContent=fmt(online);document.getElementById('kOffline').textContent=fmt(offline.length);document.getElementById('kAgOffline').textContent=fmt(agOff);document.getElementById('kCriticas').textContent=fmt(criticas.length);document.getElementById('kRecuperadas').textContent=fmt(comparativo.recuperadas||0);document.getElementById('kMelhoria').textContent=fmtPct(comparativo.percentual_melhoria_media||comparativo.percentual_melhoria_primeiro||0);document.getElementById('kDisp').textContent=fmtPct(disp);document.getElementById('kOnlinePct').textContent=fmtPct(disp);document.getElementById('kOfflinePct').textContent=fmtPct(pct(offline.length,total));let pior=ufs[0];if(pior){document.getElementById('critica').textContent=pior.uf;document.getElementById('criticaDetalhe').textContent=`${pior.offline} offline - ${fmtPct(pct(pior.offline,pior.total))} indisponível`}if(comparativo.tem_historico){document.getElementById('histTitulo').textContent=`${fmtPct(comparativo.percentual_melhoria_media||0)} vs média`;document.getElementById('histDetalhe').textContent=`${comparativo.qtd_relatorios_antigos} relatórios | média offline: ${comparativo.media_offline_historica}`;document.getElementById('novasOffline').textContent=fmt(comparativo.novas_offline||0);document.getElementById('melhorHistorico').textContent=fmt(comparativo.melhor_offline||0)+' offline';document.getElementById('melhorHistoricoDetalhe').textContent=comparativo.melhor_data||'-'}renderTabelas(criticas,ufs);renderGraficos(ufs)}
function renderTabelas(criticas,ufs){document.getElementById('tbodyComparativo').innerHTML=comparativo.tem_historico?`<tr><td>${comparativo.qtd_relatorios_antigos}</td><td>${comparativo.arquivo_base_anterior}</td><td>${fmt(comparativo.offline_anterior)}</td><td class="neg">${fmt(comparativo.offline_atual)}</td><td>${fmt(comparativo.recuperadas)}</td><td>${fmt(comparativo.continuam_offline)}</td><td class="neg">${fmt(comparativo.novas_offline)}</td><td><b>${fmtPct(comparativo.percentual_melhoria_media)}</b></td></tr>`:'<tr><td colspan="8">Nenhum relatório antigo encontrado.</td></tr>';document.getElementById('tbodyCriticas').innerHTML=criticas.length?criticas.map(d=>`<tr><td>${d.UF||''}</td><td>${d.AGENCIA_NORMALIZADA||''}</td><td><b>${d.NOME_CAMERA_NORMALIZADO||''}</b></td><td><span class="pill bad">${d.CRITICIDADE}</span></td></tr>`).join(''):'<tr><td colspan="4">Nenhuma câmera crítica offline.</td></tr>';document.getElementById('tbodyUf').innerHTML=ufs.length?ufs.map(r=>{let d=pct(r.online,r.total);return `<tr><td><b>${r.uf}</b></td><td>${fmt(r.online)}</td><td class="neg">${fmt(r.offline)}</td><td>${fmt(r.total)}</td><td><b>${fmtPct(d)}</b></td><td>${pill(d)}</td></tr>`}).join(''):'<tr><td colspan="6">Sem dados.</td></tr>'}
function renderGraficos(ufs){if(chartUfStack)chartUfStack.destroy();chartUfStack=new Chart(document.getElementById('chartUfStack'),{type:'bar',data:{labels:ufs.map(r=>r.uf),datasets:[{label:'Online',data:ufs.map(r=>r.online),backgroundColor:'#006b3f'},{label:'Offline',data:ufs.map(r=>r.offline),backgroundColor:'#b42318'}]},options:{maintainAspectRatio:false,responsive:true,scales:{y:{beginAtZero:true}}}});if(chartHistorico)chartHistorico.destroy();let s=comparativo.serie||[];chartHistorico=new Chart(document.getElementById('chartHistorico'),{type:'line',data:{labels:s.map(x=>x.data),datasets:[{label:'Offline',data:s.map(x=>x.offline),borderColor:'#b42318',backgroundColor:'rgba(180,35,24,.12)',fill:true,tension:.25},{label:'Online',data:s.map(x=>x.online),borderColor:'#006b3f',backgroundColor:'rgba(0,107,63,.08)',fill:false,tension:.25}]},options:{maintainAspectRatio:false,responsive:true}})}
[filtroUf,filtroModo,busca].forEach(el=>el.addEventListener('input',atualizar));popular();atualizar();</script></body></html>"""
    html = html.replace("__DATE__", data_atualizacao).replace("__DATA__", dados_json).replace("__COMP__", comparativo_json)
    caminho.write_text(html, encoding="utf-8")
    return caminho

def gerar_imagem_dashboard(arquivo_dashboard):
    imagem = PASTA_DESTINO / "dashboard_monitoring_email.png"

    options = Options()
    options.add_argument("--headless=new")
    options.add_argument("--disable-gpu")
    options.add_argument("--hide-scrollbars")
    options.add_argument("--window-size=1400,920")
    options.add_argument("--allow-file-access-from-files")

    driver_img = webdriver.Chrome(options=options)
    try:
        driver_img.get(Path(arquivo_dashboard).resolve().as_uri())
        time.sleep(5)

        altura = driver_img.execute_script(
            "return Math.max(document.body.scrollHeight, document.documentElement.scrollHeight, 1200);"
        )
        altura = min(int(altura), 980)
        driver_img.set_window_size(1400, altura)
        time.sleep(2)
        driver_img.save_screenshot(str(imagem))
    finally:
        driver_img.quit()

    return imagem


def enviar_email(arquivo_excel, arquivo_txt, arquivo_dashboard, resultado):
    try:
        imagem_dashboard = gerar_imagem_dashboard(arquivo_dashboard)
    except Exception as erro:
        print("Não foi possível gerar a imagem do dashboard.")
        print("Motivo:", erro)
        imagem_dashboard = None

    corpo_html = """
    <html>
    <body style="font-family: Arial, sans-serif; font-size: 14px; color: #1f2937;">
      <p>Prezados, Bom dia,</p>
      <p>Segue a atualização do relatório do CFTV, com as métricas para tomada de decisão e normalização do monitoramento.</p>
      {imagem}
      <p>Atenciosamente,</p>
    </body>
    </html>
    """

    imagem_html = ""
    if imagem_dashboard is not None:
        imagem_html = '<p><img src="cid:dashboard_monitoring_img" style="width:1100px; max-width:100%; border:1px solid #dfe7e2;"></p>'

    corpo_html = corpo_html.format(imagem=imagem_html)

    try:
        import win32com.client as win32

        outlook = win32.Dispatch("Outlook.Application")
        email = outlook.CreateItem(0)

        email.To = "; ".join(EMAILS_DESTINO)
        email.Subject = "Security Monitoring Dashboard - Atualizacao de cameras"
        email.HTMLBody = corpo_html

        conta_encontrada = False

        for conta in outlook.Session.Accounts:
            if conta.SmtpAddress.lower() == "destinatario@example.com":
                email.SendUsingAccount = conta
                conta_encontrada = True
                break

        if not conta_encontrada:
            print("A conta destinatario@example.com não foi encontrada no Outlook.")
            print("O e-mail sera enviado com a conta padrao configurada.")

        email.Attachments.Add(str(arquivo_excel))
        email.Attachments.Add(str(arquivo_dashboard))

        if imagem_dashboard is not None:
            anexo_img = email.Attachments.Add(str(imagem_dashboard))
            anexo_img.PropertyAccessor.SetProperty(
                "http://schemas.microsoft.com/mapi/proptag/0x3712001F",
                "dashboard_monitoring_img"
            )

        email.Send()
        print("E-mail enviado automaticamente com a imagem do dashboard no corpo.")
        return

    except Exception as erro:
        print("Não foi possível enviar o e-mail pelo Outlook.")
        print("Motivo:", erro)

    eml = PASTA_DESTINO / "email_monitoring_para_envio.eml"

    conteudo_eml = f"""From: destinatario@example.com
To: {'; '.join(EMAILS_DESTINO)}
Subject: Security Monitoring Dashboard - Atualizacao de cameras
Content-Type: text/html; charset="utf-8"

{corpo_html}

<p>Anexos para incluir manualmente:<br>
{arquivo_excel}<br>
{arquivo_dashboard}<br>
{imagem_dashboard if imagem_dashboard is not None else ''}</p>
"""

    eml.write_text(conteudo_eml, encoding="utf-8")

    print(f"Arquivo de e-mail gerado: {eml}")
    print("Inclua manualmente os anexos:")
    print(arquivo_excel)
    print(arquivo_dashboard)
    if imagem_dashboard is not None:
        print(imagem_dashboard)

def main():
    driver = criar_driver()

    try:
        print("Entrando no site...")
        login(driver)

        print("Abrindo Gestao de recursos...")
        abrir_gestao_recursos(driver)

        print("Clicando em Area...")
        selecionar_area(driver)

        print("Exportando Excel...")
        arquivo_baixado = exportar_excel(driver)

        print("Movendo arquivo para pasta destino...")
        arquivo_excel = mover_para_destino(arquivo_baixado)

        print("Processando Excel...")
        resultado = processar_excel(arquivo_excel)

        print("Comparando com relatório anterior...")
        resultado["comparativo"] = comparar_com_relatorios_antigos(arquivo_excel, resultado)

        print("Gerando TXT...")
        arquivo_txt = gerar_txt(resultado)

        print("Gerando dashboard HTML...")
        arquivo_dashboard = gerar_dashboard_html(resultado)

        print("Preparando e-mail pelo Outlook...")
        enviar_email(arquivo_excel, arquivo_txt, arquivo_dashboard, resultado)

        print("Processo concluido com sucesso.")
        print(f"Excel: {arquivo_excel}")
        print(f"TXT: {arquivo_txt}")
        print(f"Dashboard: {arquivo_dashboard}")

    finally:
        time.sleep(5)
        driver.quit()


if __name__ == "__main__":
    main()
