# Global Solution – MLAM | CitizenTech
## Moradia e desigualdade urbana: o que explica o valor dos imóveis nas comunidades?

Trabalho da disciplina **Machine Learning e Análise de Dados (MLAM)** – 2º semestre.

## 1. Integrantes

| Nome completo | RM |
|---|---|
| André Fujinaga | 569158 |
| Guilherme Belo | 570079 |

## 2. Problema e objetivo das análises

O custo da moradia é um dos principais desafios das cidades e comunidades. O trabalho investiga, com dados reais, como a **renda das famílias**, a **idade das construções** e as **condições de ocupação dos domicílios** (cômodos e pessoas por domicílio) se relacionam com o **valor mediano dos imóveis**, buscando apoiar diagnósticos de acessibilidade à moradia e o planejamento urbano no contexto do desafio **CitizenTech** (tema: Cidades).

Foram aplicados: distribuição normal de probabilidade, testes de hipóteses para a média (teste Z), análise bivariada, regressão linear simples e múltipla e regressão logística binária.

## 3. Base de dados

- **Nome:** California Housing (Censo dos EUA de 1990, agregado por comunidade/*block group*).
- **Origem:** Pace, R. K.; Barry, R. (1997). *Sparse spatial autoregressions*. Statistics & Probability Letters, 33(3), 291–297.
- **Endereço de acesso à base original:** https://raw.githubusercontent.com/ageron/handson-ml2/master/datasets/housing/housing.csv  
  (mesma base também disponível em `sklearn.datasets.fetch_california_housing`).
- **Dados reais:** não foram usados dados fictícios nem gerados artificialmente.
- **Registros:** 20.640 originais → **19.548** após a limpeza.
- **Arquivo da base utilizada nas análises:** `base_california_housing_limpa.csv`.

**Variáveis utilizadas (após conversão de unidades):**

| Variável | Descrição | Unidade |
|---|---|---|
| `valor_mil` | Valor mediano dos imóveis | mil US$ |
| `renda_mil` | Renda mediana anual das famílias | mil US$/ano |
| `idade_mediana` | Idade mediana das construções | anos |
| `comodos_dom` | Cômodos por domicílio | unidades |
| `pessoas_dom` | Pessoas por domicílio | pessoas |
| `populacao`, `latitude`, `longitude` | População e localização (descritivas) | – |

**Limpeza e transformações (documentadas e reproduzíveis pelo notebook):**
1. Renomeação das variáveis e conversão de unidades (valor em mil US$; renda em mil US$/ano).
2. Criação de `comodos_dom` (total de cômodos ÷ domicílios) e `pessoas_dom` (população ÷ domicílios).
3. Remoção de **992 registros censurados** no teto de US$ 500.001 (limite do censo).
4. Remoção de **100 registros** com valores extremos implausíveis (> 20 cômodos ou > 10 pessoas por domicílio).

## 4. Arquivos do repositório

```
├── README.md                              # este arquivo
├── GS_MLAM_2SEM_1.ipynb                   # notebook (código Python + saídas preservadas)
├── Relatorio_Estatistico_GS_MLAM.pdf      # relatório estatístico (item 07)
├── base_california_housing_limpa.csv      # base utilizada nas análises (item 08a)
└── figuras/                               # (opcional) gráficos fig_*.png gerados pelo notebook
```

> Se o notebook for renomeado no repositório, atualize o nome acima.

## 5. Como executar

1. Abra o notebook no **Google Colab** (Arquivo ▸ Abrir notebook ▸ GitHub, ou faça upload do `.ipynb`).
2. Execute **Ambiente de execução ▸ Executar tudo** (as células devem ser executadas em ordem).
3. A coleta baixa a base automaticamente da internet e tenta, em ordem, quatro fontes públicas da mesma base real; se todas falharem, o notebook é interrompido com erro (nunca gera dados fictícios).
4. A execução gera `base_california_housing_limpa.csv` e as figuras `fig_00` a `fig_09` (PNG) no diretório de trabalho.

Execução local (opcional): `pip install -r requirements.txt` (ver abaixo) e abrir o notebook no Jupyter/VS Code. A semente aleatória é fixa (`SEED = 42`), o que torna os resultados reproduzíveis.

## 6. Bibliotecas e dependências

- Python 3.10+
- `numpy`, `pandas`, `scipy`, `scikit-learn`, `matplotlib`, `seaborn`
- `requests` (usada na fonte alternativa de download)

Exemplo de `requirements.txt`:
```
numpy
pandas
scipy
scikit-learn
matplotlib
seaborn
requests
```
No Google Colab, todas já estão instaladas.

## 7. Síntese dos principais resultados

| Análise | Resultado principal |
|---|---|
| Normal (renda mediana) | μ = 36,77; σ = 15,68. P(X < média) = 0,5000 (equiprovável); P(X > mediana) = 0,5576 (provável) |
| Teste Z (a) – H₁: μ < μ₀, α = 6%, n = 20 | Z = 0,8559; Z crítico = −1,5548; p = 0,8040 → **não rejeita H₀** |
| Teste Z (b) – H₁: μ > μ₀, α = 9%, n = 25 | Z = −0,0177; Z crítico = 1,3408; p = 0,5071 → **não rejeita H₀** |
| Bivariada (renda × valor) | Covariância = 987,94; r = 0,6484 (moderada e positiva); valor = 44,4756 + 4,0180·renda; R² = 42,05%; F = 14.180,43 (p < 0,001) |
| Previsão simples | Renda de 50 mil US$/ano → valor ≈ 245,4 mil US$ (IP 95%: 100,4 a 390,4) |
| Regressão múltipla | valor = 129,0718 + 4,8133·renda + 1,3376·idade − 13,6228·cômodos − 27,2077·pessoas; R² = 52,77%, R² ajustado = 52,76%; F = 5.458,80 (p < 0,001); VIF ≤ 1,48 |
| Previsão múltipla | Renda 40, idade 30, 5 cômodos, 3 pessoas → valor ≈ 212,0 mil US$ |
| Regressão logística | Acurácia: treino 80,11% e **teste 79,23%** (linha de base 50,06%); sensibilidade 78,46%; especificidade 80,01%; precisão 79,65% |
| Classificação | Renda 60, idade 25, 6 cômodos, 2,5 pessoas → P(valor alto) = 97,14% → classe ALTO |

**Conclusão resumida:** a renda é o fator mais associado ao valor dos imóveis; ocupação do domicílio (pessoas por domicílio) tem associação negativa relevante; é possível classificar comunidades de valor alto ou baixo com ≈ 79% de acurácia. Os modelos ajudam a localizar comunidades cujo valor destoa do esperado, apoiando diagnósticos no CitizenTech.

## 8. Limitações identificadas

- Dados de 1990 e da Califórnia: não representam o Brasil nem o presente.
- Unidade de análise agregada (comunidade): risco de falácia ecológica.
- Valores acima de US$ 500 mil foram censurados e removidos, o que subestima o topo da distribuição e causa truncamento nos resíduos.
- Resíduos com heterocedasticidade e caudas pesadas; renda assimétrica (normalidade apenas aproximada); previsões negativas possíveis nos extremos.
- ~47% da variação do valor não é explicada pelas variáveis do modelo (ex.: localização, qualidade da construção, infraestrutura).
- Testes Z com amostras pequenas (n = 20 e 25); resultado depende da amostra sorteada.
- Associação não implica causalidade; com ~20 mil registros, quase todo efeito é estatisticamente significativo, devendo-se avaliar a relevância prática.

Mais detalhes, tabelas e gráficos no relatório `Relatorio_Estatistico_GS_MLAM.pdf`.
