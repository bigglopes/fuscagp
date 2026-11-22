# Fusca GP — Copa Clássicos · Resumo do Estado Atual do Projeto

**Última atualização:** 2026-11-22  
**Entregável pronto para jogar:** `C:\jogo_fuscas\index.html` (arquivo único autocontido, WebGPU puro)  
**Status da Verificação:** ✅ **APROVADO** em desempenho (GPU Timestamp Queries em 1920×1080: média `0,65 ms`, P99 `0,68 ms`, 0 frames > 11 ms, 0 erros de console/GPU) e inspeção visual em múltiplos ângulos de todos os 4 modelos.

---

## 1. Visão Geral Técnica

- **Renderização:** WebGPU puro + shaders WGSL (sem Three.js, Babylon.js ou qualquer engine externa).
- **100% Procedural:** Geometria 3D dos 4 veículos e 3 circuitos, texturas mipmapeadas (1024×1024), avatares cel-shaded, HUD, áudio dos motores e trilha sonora gerados em código na inicialização (zero arquivos externos).
- **Zero Alocações por Quadro (Zero GC):** Buffers de GPU por veículo, matrizes, vetores, estado de física/IA, quads de HUD e pool de 512 partículas todos pré-alocados em TypedArrays.
- **Pipeline Gráfico (PS3 HD Cel-Shaded · 6 Passes):** Shadow Map `2048×2048` PCF 9-tap → MRT 4× MSAA (HDR + G-Buffer) → Bloom HDR separável → Composição final com contornos Roberts Cross, ACES Tone Mapping, dithering 8-bit e HUD 2D.

---

## 2. Conteúdo e Mecânicas Implementadas

### 4 Modelos 3D Exclusivos, Pilotos e Física Própria
Todos os 4 modelos correm simultaneamente na pista, cada um com geometria 3D própria, rodas exclusivas, assinatura sonora de motor, comportamento de suspensão e atributos reais de física:

| # | Piloto | Modelo | Cor | Perfil | Aceleração | Velocidade Máx. | Frenagem | Destaque Especial |
| :-: | :--- | :--- | :--- | :--- | :-: | :-: | :-: | :--- |
| **1** | **LUCA** | **VW Fusca** | Azul Calcinha | Clássico Equilibrado | 8/10 (`1.03×`) | 8/10 (`50.8 m/s`) | 8/10 (`1.04×`) | Motor Boxer 1600, vidro dividido, calotas cromadas |
| **2** | **BIA** | **Fiat Uno** | Vermelho Cereja | Velocidade Máxima | 7/10 (`0.94×`) | **10/10 (`54.0 m/s`)** | 7/10 (`0.93×`) | Grade diagonal 5 barras, limpador único, aerofólio, rodas 5 raios, suspensão firme |
| **3** | **GUGA** | **Jipe 4×4** | Amarelo Safari | Arrancada & Off-Road | **10/10 (`1.18×`)** | 7/10 (`48.4 m/s`) | 8/10 (`1.02×`) | Grade 7 fendas, quebra-mato, santantônio, pneus cravudos, estepe + galão, **bônus real de tração na grama e brita** |
| **4** | **OTTO** | **Calhambeque** | Creme Vintage | Frenagem & Curva Pro | 8/10 (`1.00×`) | 7/10 (`49.0 m/s`) | **10/10 (`1.26×` + grip)** | Radiador em latão + manivela, buzina fon-fon, para-lamas contrastantes, baú de couro, rodas raiadas em madeira + faixa branca |

### 3 Circuitos Selecionáveis (Menu Iniciar)
1. **1. Autódromo Clássico (`AUTÓDROMO CLÁSSICO`)** — GP equilibrado sob céu azul de meio-dia, boxes de 2 andares, torre de cronometragem, pórtico de largada/chegada com semáforo, arquibancadas e árvores multi-camada.
2. **2. Super Speedway (`SUPER SPEEDWAY`)** — Oval inclinado de alta velocidade ao pôr do sol, muro contínuo amarelo/branco e estádio.
3. **3. Serra dos Fuscas (`SERRA DOS FUSCAS`)** — Circuito técnico de montanha com subidas até 24,4 m, manhã alpina enevoada, pinheiros e rochas graníticas.

### Áudio Procedural (Web Audio API)
- **Trilha sonora:** Techno arcade estilo anos 2000 (142 BPM) em destaque no mix.
- **4 Timbres de Motor Exclusivos:** Ronco Boxer refrigerado a ar (Fusca), 4 cilindros giro alto com leve assobio turbo (Uno), grave encorpado 4×4 (Jipe) e batida compassada vintage (Calhambeque), além de chiado de pneus e colisões.

### Controles
- **Menu Iniciar:** `↑ / ↓` Circuito · `← / →` Piloto & Carro · `Enter` / `Espaço` / `Z` ou clique em **Iniciar Corrida**
- **Na Corrida:** `← / →` Volante · `Z` Acelerar · `X` Frear / Ré · `R` Reiniciar · `M` Mudo · `ESC` Voltar ao Menu

---

## 3. Métricas de Verificação (WebGPU · 4 Modelos Simultâneos)

| Métrica | Amostras | Média | P95 | P99 | Máximo | > 11 ms |
| :--- | ---: | ---: | ---: | ---: | ---: | ---: |
| **GPU (`gpuMs`)** | 399 | **0,65 ms** | 0,67 ms | **0,68 ms** | **0,83 ms** | **0** |
| **Frame Pacing (`frameMs`)** | 398 | **5,56 ms** | 5,70 ms | **5,80 ms** | **5,80 ms** | **0** |

- **Erros de Console / WebGPU:** `0`
- **Contagem de Triângulos por Modelo:**
  - Fusca: `13.254` carroceria + `1.836` / roda
  - Uno: `2.512` carroceria + `1.490` / roda
  - Jipe: `5.584` carroceria + `1.536` / roda
  - Calhambeque: `8.162` carroceria + `2.016` / roda
