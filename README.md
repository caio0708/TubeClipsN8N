# TubeClips - N8N

Um painel web para baixar vídeos do YouTube e cortá-los em clipes verticais (formato shorts/reels) com legendas dinâmicas, tudo controlado por um workflow do n8n.

![Imagem da Interface](https://i.imgur.com/gA8Fv3g.png)
*(Substitua este link por uma captura de tela real do seu `index.html` em ação)*

---

## 🚀 Sobre o Projeto

Este projeto é composto por duas partes principais:

1.  **Front-end (`index.html`):** Uma interface de usuário simples (painel) onde você pode colar um link do YouTube para baixar ou enviar um arquivo de vídeo local para cortar.
2.  **Back-end (Workflow n8n):** Um poderoso workflow do n8n que recebe as solicitações do front-end e faz todo o trabalho pesado:
    * **Baixar:** Usa `yt-dlp` para baixar o vídeo e suas legendas (`.srt`) do YouTube.
    * **Cortar:** Usa `ffmpeg` para cortar o vídeo principal e o arquivo de legenda em múltiplos intervalos.
    * **Formatar:** Converte os clipes para o formato vertical (1080x1920).
    * **Legendar:** Converte as legendas `.srt` para `.ass` e (assumindo a existência de um script customizado) as queima no vídeo com estilo dinâmico.

## ✨ Funcionalidades

* **Painel de Download:** Cole uma URL do YouTube para baixar o vídeo em MP4 e suas legendas `.srt` em português.
* **Painel de Corte:**
    * Faça upload de um arquivo de vídeo local (`.mp4`, `.mov`, `.mkv`, `.webm`).
    * Adicione múltiplos intervalos de corte (ex: `00:10:05` a `00:10:20`).
    * O back-end processa cada intervalo como um clipe separado.
* **Processamento de Mídia:**
    * Clipes são convertidos para o formato vertical 1080x1920 (ideal para Shorts, Reels, TikTok).
    * Legendas são cortadas e queimadas (hardsub) no vídeo final.

## 🛠️ Tecnologias Utilizadas

* **Front-end:** HTML5, CSS3, JavaScript (ES6+), Font Awesome.
* **Back-end:** n8n (Self-Hosted).
* **Ferramentas de CLI:**
    * `yt-dlp`: Para baixar mídias do YouTube.
    * `ffmpeg`: Para todo o processamento de áudio e vídeo (corte, formatação, legendas).
    * `node.js`: (Implícito) Para executar um script customizado de geração de legendas (`.ass`).

## ⚙️ Instalação e Configuração

Este projeto **exige uma configuração de ambiente específica** para que o front-end e o back-end conversem corretamente.

### 1. Pré-requisitos

Você precisa ter as seguintes ferramentas instaladas na máquina que executa o n8n:

* **n8n (Self-Hosted):** O workflow foi projetado para n8n self-hosted. Recomenda-se usar a [instalação via Docker](https://docs.n8n.io/hosting/installation/docker/).
* **FFmpeg:** A ferramenta de linha de comando `ffmpeg` deve estar instalada e acessível pelo n8n.
* **yt-dlp:** A ferramenta de linha de comando `yt-dlp` deve estar instalada e acessível pelo n8n.
* **Node.js:** Necessário para o workflow executar o script customizado de legendas (`gerarASS.js`).

### 2. Configuração do Back-end (n8n)

O workflow (`TUBE CLIPS PAINEL (Solucionado).json`) foi construído com caminhos de arquivo **absolutos** e **específicos**. Você **DEVE** adaptá-los para o seu ambiente.

#### A. Mapeamento de Volume (Docker)

O workflow usa dois estilos de caminho:
* `/data/`: Caminho interno do container Docker do n8n.
* `C:\Users\caiot\Downloads\`: Caminho do sistema operacional *host* (Windows).

Isso implica que o n8n está rodando em Docker com um **volume mapeado**. Ao iniciar seu container Docker do n8n, você deve mapear o seu diretório de downloads local para o diretório `/data` do container.

**Exemplo de `docker-compose.yml`:**

```yaml
version: "3.7"
services:
  n8n:
    image: n8nio/n8n
    restart: always
    ports:
      - "5678:5678"
    environment:
      # Permite que o n8n execute comandos locais (ffmpeg, yt-dlp)
      - N8N_EXECUTION_PROCESS=main
      - EXECUTIONS_DATA_PRUNE=true
    volumes:
      # Mapeia seu diretório local para /data do container
      # ATENÇÃO: Mude o caminho da esquerda para o SEU diretório de downloads
      - C:\Users\SeuUsuario\Downloads:/data
      - n8n_data:/home/node/.n8n

volumes:
  n8n_data:
