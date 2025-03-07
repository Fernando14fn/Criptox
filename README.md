import os
import platform
import subprocess
from datetime import datetime
import pyttsx3
import speech_recognition as sr
import psutil
import json

# Inicializa o motor de voz
engine = pyttsx3.init()
engine.setProperty('rate', 150)
engine.setProperty('volume', 0.9)

# Banco de dados local para hierarquia e logs
hierarquia = {
    "comandos_básicos": ["status do sistema", "criar arquivo", "abrir aplicativo"],
    "comandos_avançados": ["gerenciar empresa", "calcular", "previsão financeira"],
    "comandos_especializados": ["energia quântica", "projetos científicos", "automação avançada"]
}

logs = []

# Função de fala
def falar(texto):
    engine.say(texto)
    engine.runAndWait()

# Reconhecimento de voz
def ouvir_comando():
    recognizer = sr.Recognizer()
    with sr.Microphone() as source:
        falar("Estou ouvindo, pode falar.")
        try:
            audio = recognizer.listen(source, timeout=5)
            comando = recognizer.recognize_google(audio, language="pt-BR")
            return comando.lower()
        except sr.UnknownValueError:
            falar("Não entendi o que você disse. Pode repetir?")
        except sr.RequestError:
            falar("Erro ao acessar o serviço de reconhecimento de voz.")
        return ""

# Função principal
def main():
    falar("Bem-vindo! Sou sua IA global e ilimitada. Estou pronta para auxiliar em todas as áreas.")
    while True:
        comando = ouvir_comando()
        
        if "sair" in comando:
            falar("Encerrando minhas atividades. Até logo!")
            salvar_logs()
            break
        elif comando in hierarquia["comandos_básicos"]:
            executar_comando_basico(comando)
        elif comando in hierarquia["comandos_avançados"]:
            executar_comando_avancado(comando)
        elif comando in hierarquia["comandos_especializados"]:
            executar_comando_especializado(comando)
        else:
            falar("Comando não reconhecido. Tente novamente.")

# Funções para comandos básicos
def executar_comando_basico(comando):
    if "status do sistema" in comando:
        status_sistema()
    elif "criar arquivo" in comando:
        criar_arquivo()
    elif "abrir aplicativo" in comando:
        abrir_aplicativo()

def status_sistema():
    cpu = psutil.cpu_percent(interval=1)
    memoria = psutil.virtual_memory().percent
    disco = psutil.disk_usage('/').percent
    status = f"Uso de CPU: {cpu}%. Uso de memória: {memoria}%. Uso de disco: {disco}%."
    falar(status)
    log_evento("status_sistema", status)

def criar_arquivo():
    falar("Qual o nome do arquivo?")
    nome = input("Nome do arquivo: ")
    falar("Qual o conteúdo do arquivo?")
    conteudo = input("Conteúdo: ")
    with open(nome, "w") as arquivo:
        arquivo.write(conteudo)
    falar(f"Arquivo {nome} criado com sucesso!")
    log_evento("criar_arquivo", f"Arquivo {nome} criado.")

def abrir_aplicativo():
    falar("Qual aplicativo você deseja abrir?")
    app = input("Digite o nome do aplicativo: ")
    try:
        if platform.system() == "Windows":
            os.startfile(app)
        elif platform.system() == "Darwin":
            subprocess.run(["open", app])
        else:
            subprocess.run(["xdg-open", app])
        falar(f"Abrindo {app}.")
        log_evento("abrir_aplicativo", f"Aplicativo {app} aberto.")
    except Exception as e:
        falar(f"Não foi possível abrir o aplicativo. Erro: {str(e)}")
        log_evento("abrir_aplicativo", f"Erro ao abrir {app}: {str(e)}")

# Funções para comandos avançados
def executar_comando_avancado(comando):
    if "calcular" in comando:
        calcular()
    elif "gerenciar empresa" in comando:
        falar("Função de gerenciamento empresarial ainda está em desenvolvimento.")
        log_evento("gerenciar_empresa", "Tentativa de uso.")

def calcular():
    falar("Diga a operação que você deseja calcular, como 2 mais 2.")
    operacao = ouvir_comando()
    try:
        resultado = eval(operacao.replace("mais", "+").replace("menos", "-").replace("vezes", "*").replace("dividido por", "/"))
        falar(f"O resultado é {resultado}.")
        log_evento("calcular", f"Operação: {operacao}, Resultado: {resultado}")
    except:
        falar("Não consegui calcular. Tente novamente.")
        log_evento("calcular", "Erro na operação.")

# Funções para comandos especializados
def executar_comando_especializado(comando):
    if "energia quântica" in comando:
        falar("Conectando aos módulos de energia quântica...")
        log_evento("energia_quântica", "Comando executado.")
    else:
        falar("Comando especializado ainda não implementado.")
        log_evento("comando_especializado", f"Tentativa de usar {comando}")

# Função para registrar logs
def log_evento(acao, descricao):
    log = {
        "data": str(datetime.now()),
        "ação": acao,
        "descrição": descricao
    }
    logs.append(log)

# Função para salvar logs em um arquivo
def salvar_logs():
    with open("logs.json", "w") as log_file:
        json.dump(logs, log_file, indent=4)
    falar("Todos os logs foram salvos com sucesso.")

if __name__ == "__main__":
    main()
