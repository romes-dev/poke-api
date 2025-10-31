# PokePWA — Requisitos do MVP & README (Windows)

Um aplicativo Expo + React Native minimalista, porém bonito, que roda em **Android/iOS/Web** e pode ser instalado como **PWA**. Ele lista os Pokémon da primeira geração (1–151), permite ao usuário **buscar por nome ou ID**, e mostra um **modal de detalhes** com imagem, tipos, atributos base, tamanho e habilidades — tudo a partir da **PokéAPI**.

---

## 1) Escopo do MVP

**Objetivo:** entregar um app de tela única com busca e visual polido que funcione offline como PWA (instalável em dispositivos móveis e desktop).

### 1.1 Funcionalidades essenciais (MVP)

* [x] **Listar Pokémon da Geração 1 (1–151)** com sprite, ID e nome.
* [x] **Busca** por nome (substring) **ou ID exata** (ex.: `25`).
* [x] **Modal de detalhes**: sprite, tipos, atributos base (HP/ATK/DEF etc.), altura/peso, habilidades.
* [x] **Imagens rápidas** via sprites CDN; detalhes carregados **sob demanda**.
* [x] **Pronto para PWA**: instalável, executa em modo standalone.
* [x] **UI responsiva** (smartphones, tablets pequenos, desktop web).

### 1.2 Visual agradável (baseline)

* Tema escuro com cor de destaque, cards arredondados, bordas sutis e sombras.
* Hierarquia tipográfica consistente (título, subtítulo, corpo, legendas).
* Transição suave do modal; barras de atributos simples.

### 1.3 Não funcionais

* Desempenho adequado em dispositivos de entrada.
* Não exige API key; uso responsável da API pública (evite polling agressivo).
* Acessibilidade básica (rótulos, áreas de toque ≥ 44px, contraste ≳ 4.5:1).

---

## 2) Stack tecnológica

* **Expo (Managed)** — base única para nativo e web.
* **React Native / React Native Web** — camada de UI.
* **PokéAPI** — fonte de dados REST (sem autenticação).
* **GitHub Pages** (ou Netlify/Vercel) — hospedagem web do PWA.

---

## 3) Configuração no Windows (do zero)

> Compatível com **Windows 10/11**.

### 3.1 Instalar dependências

* **Node.js LTS** (20.x ou 22.x): <https://nodejs.org>
* **Git**: <https://git-scm.com/download/win>
* (Opcional, para build Android) **Android Studio + SDK**: <https://developer.android.com/studio>

  * Android SDK Platform 35+
  * Android Virtual Device (AVD) se quiser emulador

> **Dica (Chocolatey):**

```powershell
# Execute uma vez no PowerShell como Administrador
Set-ExecutionPolicy Bypass -Scope Process -Force; \
  [System.Net.ServicePointManager]::SecurityProtocol = [System.Net.ServicePointManager]::SecurityProtocol -bor 3072; \
  iex ((New-Object System.Net.WebClient).DownloadString('https://community.chocolatey.org/install.ps1'))

choco install -y nodejs-lts git androidstudio
```

### 3.2 Verificar versões

```powershell
node -v    # v20.x ou v22.x
npm -v     # v10+
git --version
```

### 3.3 Instalar o Expo CLI

```powershell
npm i -g expo
```

---

## 4) Configuração do projeto

### 4.1 Criar o projeto

```powershell
expo init poke-pwa --template blank
cd poke-pwa
```

### 4.2 Instalar dependências locais (managed)

```powershell
npm i
```

### 4.3 Comandos de desenvolvimento

```powershell
npm run web      # abre no navegador
npm run android  # abre no Android (dispositivo/emulador)
npm start        # menu dev do Expo
```

> **Alertas de firewall**: autorize a rede local para o Metro bundler.

---

## 5) Estrutura de arquivos (MVP)

```
poke-pwa/
├─ assets/
│  ├─ icon.png
│  ├─ splash.png
│  └─ adaptive-icon.png
├─ App.js
├─ app.json
├─ package.json
├─ .gitignore
├─ README.md
└─ (Expo web build)
   └─ dist/           # criado por `npx expo export -p web`
```

**Arquivos-chave**

* `App.js` — toda a UI/lógica do MVP (lista, busca, modal, estilos).
* `app.json` — metadados de app/PWA (manifest, ícones, cores).
* `assets/` — ícones e splash (placeholders aceitos).
* `dist/` — pasta de build estático do PWA para deploy.

---

## 6) Detalhes de UI & UX

* **Header**: título do app + subtítulo curto (Gen‑1, dica de busca).
* **Campo de busca**: debounce 200–300 ms (opcional), ignora maiúsculas/minúsculas, reset completo com botão de limpar (opcional).
* **Item da lista**: sprite à esquerda, título `#ID Nome`, subtítulo pequeno.
* **Modal de detalhes**: linha superior com sprite + nome/ID + tipos; seções: *Atributos base* (barras), *Tamanho*, *Habilidades*; botão **Fechar** fixo.
* **Feedback**: spinners de carregamento para primeiro fetch e para detalhes.
* **Estado vazio**: “Nenhum Pokémon encontrado.” quando a busca não retorna.

---

## 7) Requisitos funcionais

1. **Buscar índice** em `https://pokeapi.co/api/v2/pokemon?limit=151&offset=0` no primeiro mount.
2. **Mapear ID** a partir de cada `result.url` (split por `/`).
3. **Imagens** via `https://raw.githubusercontent.com/PokeAPI/sprites/master/sprites/pokemon/{id}.png`.
4. **Busca**: filtro no cliente (substring por nome OU correspondência exata por ID).
5. **Abrir detalhes**: `GET pokemon.url` para obter `types`, `stats`, `height`, `weight`, `abilities`.
6. **Barras de atributos**: limitar largura a 100% para valores ≥ 100.
7. **Acessibilidade**: alternativas textuais, roles/aria labels no web, toques mínimos.
8. **Performance**: lista virtualizada; evitar re-renderizações (memo onde necessário).
9. **Resiliência**: try/catch nas chamadas de rede; mensagem básica de erro ao usuário quando falhar.

---

## 8) Requisitos não funcionais

* **Offline**: service worker do PWA cacheia o bundle estático; após carregado, a lista permanece disponível se já foi buscada (opcional: cache de detalhes em localStorage).
* **Segurança**: endpoints HTTPS; sem secrets armazenados.
* **Manutenibilidade**: código em único arquivo para o MVP; componentes podem ser extraídos depois.

---

## 9) Tokens de design sugeridos

* **Background**: `#0b1220`
* **Surface**: `#0f172a` / `#111827`
* **Borders**: `#243147` / `#27345a`
* **Texto primário**: `#ffffff`
* **Texto secundário**: `#cbd5e1` / `#9ca3af`
* **Acento**: `#60a5fa` (barras, botões)

> Depois você pode trocar pelas cores da sua instituição sem alterar o layout.

---

## 10) Específicos de PWA

* O export web do Expo gera automaticamente:

  * **Web App Manifest** (nome, ícones, cores)
  * **Service Worker** para cache de assets estáticos

* Garanta que `app.json` contenha:

  * `web.display: "standalone"`
  * `web.themeColor` e `web.backgroundColor`
  * ícones presentes em `/assets`

**Build do PWA**

```powershell
npx expo export -p web      # saída em ./dist
```

---

## 11) README — passo a passo (Windows)

Copie esta seção para o `README.md` do projeto.

### Pré-requisitos

* Windows 10/11
* Node.js LTS 20+/22+
* Git
* (Opcional) Android Studio + Android SDK (para testes Android)

### 1) Clonar ou criar

```powershell
# opção A: criar do zero
expo init poke-pwa --template blank
cd poke-pwa

# opção B: clonar um repositório existente
# git clone https://github.com/<voce>/poke-pwa.git
# cd poke-pwa
```

### 2) Instalar

```powershell
npm i
```

### 3) Executar em desenvolvimento

```powershell
npm run web       # abre no navegador (preview PWA)
# ou
npm run android   # requer dispositivo/emulador
```

> Se o navegador não abrir sozinho, acesse a URL exibida no terminal (geralmente <http://localhost:19006>).

### 4) Build do PWA (site estático)

```powershell
npx expo export -p web
# arquivos estáticos em ./dist
```

### 5) Deploy (GitHub Pages)

1. Crie um branch `gh-pages` **ou** configure o Pages apontando para `/dist` na main.
2. Faça push do conteúdo de `dist/` para `gh-pages`.
3. Nas configurações do repositório → **Pages** → selecione o branch `gh-pages` (root) → Salvar.
4. Abra a URL do Pages. Em mobile/desktop use **Instalar App / Adicionar à Tela Inicial**.

**Alternativa**: envie `dist/` para Netlify/Vercel.

### 6) Configuração

* Nome do app, ícones e cores: `app.json` → `expo.web` & `expo.splash`.
* Substitua os ícones placeholders em `/assets` quando possível.

### 7) Scripts

```json
{
  "start": "expo start",
  "android": "expo run:android",
  "ios": "expo run:ios",
  "web": "expo start --web",
  "build:web": "expo export -p web"
}
```

### 8) Troubleshooting (Windows)

* **Metro não consegue usar a porta**: mate o processo ou reinicie; tente `npx expo start -c` para limpar cache.
* **Tela branca**: abra o console dev; verifique erros de rede (CORS, offline). A PokéAPI pode aplicar rate-limit — tente novamente.
* **AVD não listado**: abra o Android Studio → Device Manager → crie um dispositivo Pixel (API 35) e inicialize.
* **Prompt de instalação ausente**: garanta que abriu o build de **/dist** em HTTPS. O `npm run web` não é um PWA completo; use o exportado.

---

## 12) Roadmap (pós-MVP)

* **Favoritos** (localStorage/AsyncStorage) e filtro de favoritos.
* **Filtros por tipo** (chips que consultam `https://pokeapi.co/api/v2/type/{name}`).
* **Paginação / scroll infinito** para >151.
* **Cache offline** de detalhes (LRU em localStorage).
* **Testes** (Jest + React Testing Library, testes de fumaça).
* **CI**: GitHub Actions para build & deploy no Pages a cada push na `main`.

---

## 13) Licença & cortesia com a API

* A PokéAPI é mantida pela comunidade. Cacheie resultados localmente e evite bombardear os endpoints.
* Considere incluir link para o site deles como atribuição e apoio.

---

### Apêndice A — `package.json` mínimo

```json
{
  "name": "poke-pwa",
  "version": "1.0.0",
  "private": true,
  "main": "node_modules/expo/AppEntry.js",
  "scripts": {
    "start": "expo start",
    "android": "expo run:android",
    "ios": "expo run:ios",
    "web": "expo start --web",
    "build:web": "expo export -p web"
  },
  "dependencies": {
    "expo": "^52.0.0",
    "expo-status-bar": "~2.0.0",
    "react": "^18.2.0",
    "react-dom": "^18.2.0",
    "react-native": "0.76.0",
    "react-native-web": "~0.19.13"
  }
}
```

### Apêndice B — Ícones (placeholders válidos no MVP)

* `assets/icon.png` — 1024×1024
* `assets/adaptive-icon.png` — 1024×1024 (safe area para máscara Android)
* `assets/splash.png` — 2000×3000 (ou semelhante, com área central segura)

> Gere rapidamente com qualquer editor de imagem; substitua depois para produção.

