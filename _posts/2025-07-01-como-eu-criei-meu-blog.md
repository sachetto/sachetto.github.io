
---
layout: post
title: "Como Eu Criei Meu Blog de Desenvolvimento Usando Jekyll, Docker e WSL"
date: 2025-07-01 20:00:00 -0300
categories: tutorial jekyll docker wsl
---

Neste primeiro post, quero compartilhar como criei o meu **blog de desenvolvimento**, utilizando ferramentas open source e **sem gastar dinheiro com hospedagem ou domínio**.

O objetivo deste blog é documentar meu **processo de desenvolvimento de software livre**, e este post traz um passo a passo completo.

---

## 🚀 Tecnologias Utilizadas

- **GitHub Pages:** Hospedagem gratuita para sites estáticos.
- **Jekyll:** Gerador de sites estáticos utilizado pelo GitHub Pages.
- **WSL (Windows Subsystem for Linux):** Ambiente Linux rodando no Windows.
- **Docker:** Ambiente isolado para testar e rodar o Jekyll localmente.

---

## 🔧 1. Configuração do WSL e Ubuntu

Eu instalei o **WSL** e configurei o Ubuntu no Windows.

Passos:

```bash
wsl --install
```

Depois, instalei o Ubuntu pela Microsoft Store e atualizei os pacotes:

```bash
sudo apt update
sudo apt upgrade -y
```

---

## 🐳 2. Instalando o Docker no WSL

Dentro do Ubuntu no WSL, segui os passos oficiais do Docker:

```bash
sudo apt update
sudo apt install -y ca-certificates curl gnupg lsb-release

# Adicionei a chave oficial do Docker
sudo mkdir -p /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg

# Adicionei o repositório do Docker
echo   "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu   $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

# Instalei o Docker Engine
sudo apt update
sudo apt install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```

Testei o Docker rodando:

```bash
sudo docker run hello-world
```

Reiniciei a sessão WSL.

---

## ⚙️ 3. Criando o Ambiente Docker para o Jekyll

Em uma pasta (/home/sachetto/shared/repo/DokerJekyll) dentro do anbiente do Ubunto (WSL) eu criei um arquivo `Dockerfile` com o seguinte conteúdo:

```Dockerfile
FROM ubuntu:22.04

ENV DEBIAN_FRONTEND=noninteractive

RUN apt update && apt install -y ruby-full build-essential git curl nodejs npm

RUN gem install bundler jekyll

WORKDIR /site

EXPOSE 4000

CMD ["bash"]
```

---

### 🛠️ 4. Build da Imagem Docker

Dentro da pasta onde estava o Dockerfile:

```bash
sudo docker build -t jekyll-dev .
```

---

### ▶️ 5. Executando o Container em Modo Interativo

Rodei o container mapeando a porta do Jekyll e montando meu projeto no `/site`:

```bash
sudo docker run -it -p 4000:4000 -v /mnt/c/Users/sachetto/Documents/repo/my_page/sachetto.github.io:/site jekyll-dev
```

`/mnt/c` é onde está a minha unidade C:\\ dentro do ambiente Ubuntu, o resto do caminho é onde encontra-se o repositório do site. Todo o conteúdo aparecerá dentro do container na pasta `site`.

---

## 🌐 6. Instalando o Jekyll no Container e Rodando o Site

Dentro do container:

1. Iniciei o bundler:
   ```bash
   bundle init
   ```
2. Adicionei a gem do GitHub Pages (que já inclui Jekyll e os temas):
   ```bash
   echo 'gem "github-pages", group: :jekyll_plugins' >> Gemfile
   ```
3. Instalei as dependências:
   ```bash
   bundle install
   ```
4. Criei o site (esse comando sobrescreveu meu `_config.yml` inicial):
   ```bash
   bundle exec jekyll new . --force
   ```
5. Por fim, rodei o servidor:
   ```bash
   bundle exec jekyll serve --host 0.0.0.0
   ```

A página ficou acessível em:
```
http://localhost:4000
```

---

## ⚠️ Problema Inicial: Tema Sem Estilo Localmente

Como o GitHub Pages carrega automaticamente os temas no ambiente online, localmente eu precisei instalar a gem `github-pages` para que o tema aparecesse corretamente.

---

## 📁 7. Publicando no GitHub Pages

Após testar tudo localmente, criei o repositório no GitHub, fiz o push da branch `main`, e ativei o GitHub Pages.

A URL ficou:
```
https://seunomeusuario.github.io
```

---

## 🔍 8. Próximos Passos

Agora que o ambiente está pronto, vou começar a postar sobre meus projetos open source e processos de desenvolvimento.

Pretendo criar um repositório chamado **DockerJekyll** com o Dockerfile e scripts que utilizei aqui, para quem quiser repetir esse processo de forma fácil.

---

Se quiser criar o seu próprio blog e ficou com dúvidas, pode abrir uma issue no meu GitHub!
