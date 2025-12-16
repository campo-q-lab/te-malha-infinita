# TE Ponderar — Malha Infinita (Modo Laboratório)

Um experimento computacional mínimo para tornar a **Malha Q** observável em linguagem de laboratório.

Este repositório contém o simulador **TE Ponderar (v0.3)**, em Python (notebook `.ipynb`), que implementa uma **ontologia operacional** baseada em:
- **Distinções** (markers / “Q” como subconjuntos que particionam um domínio discreto `N`)
- **Relações** (edges / “REL” conectando markers em um grafo)
- Um **critério variacional** (ganho informacional vs. custo descritivo) para escolher o próximo evento

> Escopo: isto não é cosmologia nem simulação física completa.
> É um laboratório conceitual reproduzível para observar como “distinção + relação + custo”
> pode gerar (ou bloquear) crescimento estrutural, saturação e mudança de regime.

---

## Quickstart

### Rodar no Colab (recomendado)
Notebook principal:
- `TE_Ponderar_—_Modo_Laboratório_Malha_Infinita_v0_3.ipynb`

Link Colab:
- https://colab.research.google.com/drive/1DwqtXJGZ9TCu_7dSTt3JHwECyZq-1CM_?usp=sharing

Passos:
1) Abra o link  
2) Runtime → Run all (Executar tudo)  
3) Veja o log no console (e exporte para `*.txt` se `log_path` estiver definido)

### Rodar localmente (opcional)
Requisitos: Python 3.10+ e Jupyter


pip install jupyter
jupyter notebook

Abra o .ipynb e execute as células.

## Ideia central

A cada passo, o sistema escolhe o evento que maximiza:

**U = ΔI − C**

- **ΔI**: ganho de informação/estrutura  
- **C**: custo de descrever/instanciar a operação  

A execução para quando o melhor candidato não ultrapassa a barreira **`acceptU_min`**.

---

## Métrica informacional (I)

Nesta versão (**v0.3**), a métrica usada é:

**I = H + rho·log2(max(1, R)) + lambda_rel·log2(1 + E) + lambda_size·S**

Onde:

- **H**: entropia (bits) da distribuição dos blocos induzidos pelos *markers*  
- **R**: número de blocos (partição efetiva do domínio)  
- **E**: número de relações (arestas) no grafo **REL**  
- **S**: bônus leve por “base mínima” das ligações (heurística controlada, sem explosões artificiais)

---

## Eventos (candidatos)

O simulador testa candidatos e escolhe o melhor por **U**.

### SPLIT_RULE (regra determinística)
Cria um novo *marker* aplicando uma regra determinística ao maior bloco atual  
(ex.: `FIRST_HALF`, `EVERY_OTHER`, `MOD3_0`).

- tende a aumentar **H** e **R**  
- custo descritivo mais alto  

### SPLIT_RAND (divisão combinatória/aleatória)
Cria um *marker* escolhendo um subconjunto aleatório do maior bloco.

- custo cresce com **log2(C(n, k))** (combinatório)

### SINGLETON
Cria um *marker* com um único elemento do maior bloco.

- distinção extrema (tipicamente cara para o ganho que entrega)

### REL (relação entre *markers*)
Adiciona uma aresta entre dois *markers* ainda não conectados.

- custo baixo, crescendo lentamente com **log2(1 + E)**  
- pode render ganho pequeno, porém “barato” (ΔI modesto com C menor)

---

## Macro-métricas (saturação / mudança de linguagem)

Além de **H**, **R**, **E** e **I**, o notebook imprime métricas agregadas para acompanhar o regime global:

- **dens**: densidade do grafo **REL**  
  (ex.: `2E / (m(m-1))`, onde `m` = número de *markers*)
- **k_avg**: grau médio (`2E / m`)  
- **gcc**: fração do maior componente conectado (coesão global)  
- **Hnorm**: entropia normalizada  
  (aprox. `H / log2(R)` quando `R > 1`)

**Intuição:** quando listar “pares” perde utilidade, métricas agregadas passam a descrever melhor o estado.

---

## Regimes incluídos (v0.3)

- **A_strict**: `acceptU_min = 0.0`  
  Só aceita eventos com utilidade não-negativa.

- **A_soft**: `acceptU_min = -0.05`  
  Permite pequenos “investimentos” locais (U ligeiramente negativo) para atravessar gargalos.

- **B_soft_HIGH_REL**: **REL** 10× mais caro (`rel_base` e `rel_scale` ×10)  
  Testa como o regime muda quando relações ficam caras.

---

## Reprodutibilidade

Mantendo:

- o mesmo **seed** por regime,
- os mesmos parâmetros (`N`, `rho`, custos, lambdas),
- e a mesma versão do notebook,

o log deve ser reprodutível.

---

## Citação (provisório)

> Castro, A. Q. V. “TE Ponderar — Malha Infinita (Modo Laboratório)”, GitHub, v0.3, 2025.

(Para formalizar, use o arquivo **`CITATION.cff`** do repositório.)

---

## Licença

Licença ainda não definida (por padrão: direitos reservados).  
Para colaboração pública, inclua um arquivo **`LICENSE`**.
