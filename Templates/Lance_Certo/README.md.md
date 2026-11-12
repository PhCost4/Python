# Modelo Analítico · Lance Certo S.A.

Solução analítica reprodutível para diagnóstico de qualidade de dados, cálculo de KPIs financeiros e identificação de alavancas de receita (GMV e comissão) nos leilões da Lance Certo S.A. (jan–set/2026).

## Stack
- **Linguagem:** Python 3.13
- **Manipulação e Estatística:** Pandas, NumPy, SciPy (teste qui-quadrado)
- **Visualização:** Matplotlib, Seaborn
- **Ambiente:** Jupyter Notebook (`.ipynb`) e Excel (`.xlsx` como fonte somente leitura)

## Estrutura
```text
Case_B_Base_Lotes_Lance_Certo.xlsx   → base de dados bruta (somente leitura)
modelo_analitico_lance_certo.ipynb   → pipeline completo: diagnóstico, limpeza, KPIs e decisão
saidas/                              → diretório gerado automaticamente para exportações
```
O fluxo do notebook é dividido em etapas com responsabilidade única:
`1–3` (problema, KPIs e dicionário) · `4–5` (parâmetros e ambiente) · `6–7` (carga e diagnóstico de qualidade) · `8–9` (limpeza em cópia e engenharia de atributos) · `10` (análises, testes e recomendação).

## Como executar

### Ambiente local
```bash
python -m venv .venv
# Windows: .venv\Scripts\activate   |   Linux/Mac: source .venv/bin/activate
pip install pandas numpy matplotlib seaborn scipy openpyxl jupyter
jupyter notebook
```
Abra o arquivo `.ipynb`, ajuste a célula **4. Parâmetros e configurações** se necessário e execute todas as células (`Run All`).

## Regras do projeto
- **Imutabilidade da origem:** o arquivo `.xlsx` bruto nunca é alterado; todo tratamento ocorre em uma cópia (`df.copy()`).
- **Auditoria e rastreabilidade:** valores corrigidos preservam a coluna original e ganham flags de controle (`flag_comissao_corrigida`, `flag_arredondamento`).
- **Critério de outliers por segmento:** dispersões são avaliadas dentro de cada categoria (Z-score e IQR) para evitar viés de escala entre bens.

## Resumo da análise e principais achados

### Saneamento da base (Antes × Depois)
- **Duplicatas:** remoção de 60 registros exatamente repetidos (de 5.060 para 5.000 lotes únicos), eliminando R$ 367,7 mil em comissões fictícias.
- **Erro de escala na comissão:** correção de 15 vendas registradas com casa decimal deslocada (`10x` acima do percentual contratado), ajustando R$ 465,7 mil na receita total.
- **Resultado consolidado:** GMV real de **R$ 381,41 milhões** e receita de comissão de **R$ 25,90 milhões** (conversão média de **63,3%** e *take rate* de **6,79%**).

### Diagnóstico de negócio e decisão para 2027
- **Concentração de receita:** **Máquinas** (38,0%), **Imóveis** (26,3%) e **Veículos** (26,3%) respondem por **~91% de toda a comissão**. Imóveis lideram o retorno por lote ofertado (**R$ 17,7 mil/lote**), seguidos por Máquinas (**R$ 12,9 mil/lote**).
- **Gargalo de conversão por canal:** o canal **Presencial** converte significativamente menos que o **Online** em todas as categorias (**50% vs. 67%**), com o maior abismo em Imóveis (**16% vs. 37%**).
- **Direcionamento 2027:** priorizar a recuperação da taxa de conversão do canal Presencial (foco em Imóveis e Máquinas), investigar a queda operacional registrada em setembro nessas categorias e implantar travas de validação na origem do sistema para evitar duplicatas e erros de digitação de comissão.