# 🛠️ Descrição Técnica da Estrutura do Código — Ominous Nebula

Este documento detalha a arquitetura, funções e sistemas que compõem o arquivo único do jogo **Ominous Nebula**.

## 🏗️ 1. Arquitetura do HTML & Estilização (CSS)

* **Design Responsivo & Imersivo:** O jogo utiliza `100vw` e `100vh` com `overflow: hidden`, ocupando a tela inteira sem barras de rolagem.

* **Filtros e HUD High-Tech:**
  * `.scanlines`: Aplica um gradiente CSS que simula uma tela CRT retrofuturista.
  * `#hud`: Exibe em tempo real o placar de pontos (`SYSTEM SCORE`) e a velocidade atual (`VELOCITY MACH`).
  * `.ship-selector`: Menu estilizado para alternar e selecionar os modelos 3D das naves.

## 🌐 2. Configuração do Engine 3D (Three.js)

A inicialização gráfica é controlada pela função `init()`:

* **Scene & Fog:** Cria a cena com fundo escuro (`0x050a18`) e névoa densa suave (`THREE.FogExp2`) para esconder o fim da pista no horizonte.
* **PerspectiveCamera:** Posicionada estrategicamente atrás da nave (`(0, 4, 12)`) apontando levemente para a frente (`(0, 0, -20)`).
* **WebGLRenderer:** Configurado com *Antialiasing* e mapa de sombras suaves (`THREE.PCFSoftShadowMap`).
* **Sistema de Iluminação (`setupLighting`):**
  * **AmbientLight:** Garante luz de fundo geral.
  * **DirectionalLight:** Projeta sombras suaves para dar profundidade aos obstáculos e à nave.
  * **PointLights (Ciano e Rosa):** Projetam luzes coloridas dinâmicas nas laterais da pista.

## 🛸 3. Modelagem Procedural de Naves 3D

Através da função `createShipModel(type)`, as naves são construídas utilizando geometrias compostas nativas do Three.js:

1. **Viper Spec (`'viper'`):**
   * Fuselagem cônica com cockpit de vidro refletivo (`MeshPhysicalMaterial`).
   * Asas duplas anguladas com canhões laser nas extremidades.

2. **Phantom (`'stealth'`):**
   * Estrutura gerada via vetor 2D extrudado (`THREE.ExtrudeGeometry`) no formato de asa delta furtiva.
   * Faixas brilhantes magenta nas bordas laterais.

3. **Titan Heavy (`'titan'`):**
   * Nave de assalto volumétrica com asas de ataque reforçadas.
   * Sistema de propulsão tripla na traseira com anel de energia laranja.

## 🛤️ 4. Ambiente & Cenário

* **Pista Procedural (`buildTrack`):** Criada via **Canvas 2D HTML5** dinâmico e convertida em textura 3D (`THREE.CanvasTexture`). A textura desloca a coordenada Y no loop principal para simular velocidade contínua.

* **Fundo Estelar & Nebulosas (`createNebulaBackground`):**
  * Domo tridimensional contendo 3.000 partículas/estrelas coloridas dispostas via `THREE.BufferGeometry`.
  * Malhas esféricas transparentes com textura *wireframe* simulando nuvens de nebulosas flutuantes.

## 🕹️ 5. Mecânicas de Jogo e Loop Principal

O ciclo de atualização contínuo é gerenciado pela função `animate()`:

* **Controle da Nave (`updateShipMovement`):**
  * Suporta teclado (`WASD` / Setas) e acompanhamento do ponteiro do mouse / tela sensível ao toque.
  * Calcula a inclinação lateral (*bank angle / tilt*) da nave no eixo Z proporcional ao deslocamento X.

* **Geração de Obstáculos (`spawnObstacle`):**
  * Gera aleatoriamente **Cristais**, **Barricadas** ou **Asteroides** ao fundo da pista.
  * Cada objeto possui velocidade de rotação própria e uma `PointLight` interna para destacar seu relevo.

* **Sistema de Colisão (Hitbox 3D):**
  * Utiliza caixas delimitadoras tridimensionais (`THREE.Box3`).
  * Atualiza os limites da nave e dos obstáculos a cada quadro e verifica intersecções via `.intersectsBox()`.

* **Rastro de Motores (`createEngineTrail`):**
  * Instancia pequenas partículas quadradas de luz atrás da nave que diminuem de tamanho e opacidade até desaparecerem.
