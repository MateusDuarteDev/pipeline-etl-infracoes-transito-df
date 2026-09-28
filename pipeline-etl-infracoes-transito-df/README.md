# Pipeline ETL: Autos de Infração de Trânsito do DF

Pipeline em Python que extrai, limpa, transforma, valida e carrega cerca de **81 mil autos de infração de trânsito** do Distrito Federal (junho/2026), a partir dos Dados Abertos do GDF.

> Projeto acadêmico e de portfólio, desenvolvido com dados reais e públicos para praticar tratamento e análise de dados.

## Problemas de qualidade tratados

| Problema encontrado | Tratamento | Resultado |
|---|---|---|
| Rodovia escrita em vários formatos (`DF 001`, `RODOVIA DF-150, KM 4,6`, `001`...) | Extração e padronização com expressões regulares | **482 → 68** valores distintos |
| Km ausente em grande parte dos registros e embutido no texto da rodovia | Recuperação do km a partir do texto; km impossível (> 200) tratado como ausente | Km ausente de **69% → ~1%** |
| Mesmo código de infração com descrições diferentes | Descrição mais frequente de cada código como padrão | **195 → 143** descrições |
| Tipo de veículo com grafias diferentes (`AUTOMOVEL` × `Automóvel`) | Padronização de caixa e acentos | Categorias unificadas |
| Data e hora como texto | Conversão para `datetime` com validação | 0 datas inválidas |
| Sentido da via com variações | Padronização (`CRESCENTE`, `DECRESCENTE`) | Categorias unificadas |

## Etapas do pipeline

1. **Extract:** leitura do CSV com todas as colunas como texto, para controlar cada conversão.
2. **Diagnóstico:** medição de ausentes, duplicatas e variedade de valores antes de limpar.
3. **Clean:** tratamento de cada problema em colunas novas, mantendo as originais para comparação.
4. **Transform:** criação de variáveis (dia da semana, período do dia, fim de semana, grupo de infração) e agregações.
5. **Validação:** 10 checagens automáticas com `assert`.
6. **Load:** exportação do dataset tratado e do resumo por rodovia em CSV (compatível com Excel).

## Decisões de análise

- **Linhas idênticas (4.431, ou 5,5%) foram mantidas.** O arquivo não tem identificador do auto nem placa, e duas linhas iguais podem ser veículos diferentes no mesmo minuto e local. Remover essas linhas subestimaria as infrações. A remoção pode ser ativada com `REMOVER_DUPLICATAS = True`.
- **`CAMIONETA` e `CAMINHONETE` não foram unificados**, pois são categorias diferentes no Código de Trânsito Brasileiro.
- **Um registro sem gravidade e com código único foi mantido** como `NÃO INFORMADA`, em vez de descartado.

## Principais achados

- **Velocidade** responde por cerca de 50% dos autos (40.810 de 81.059); vias e faixas exclusivas somam cerca de 24%.
- **Três vias concentram cerca de 53% dos autos:** DF-075, DF-001 e DF-003.
- A **DF-075** lidera em volume (24,3%), mas só 5,7% dos seus autos são gravíssimos. Na **DF-001** e na **DF-003**, essa proporção passa de 23%.
- Na **madrugada**, cerca de 82% dos autos são por excesso de velocidade.
- Cerca de 72% dos autos ocorrem entre manhã e tarde.

## Limitações

- Os dados cobrem **um único mês**, então não é possível concluir sobre tendências ou sazonalidade.
- Os autos refletem **onde há fiscalização**, não necessariamente onde a infração é mais frequente. Uma via com muitos autos pode apenas ter mais radares.
- Sem identificador do auto, a decisão sobre linhas duplicadas é uma premissa, não uma comprovação.
- Vias como túneis e ligações nomeadas foram agrupadas em `OUTRAS VIAS`; podem ser detalhadas em uma próxima versão.

## Estrutura do repositório

```
├── README.md
├── requirements.txt
├── notebooks/
│   └── pipeline_etl_infracoes_df.ipynb
├── data/
│   └── README.md          (como baixar o dado bruto)
└── output/
```

## Como executar

1. Clone o repositório e entre na pasta:
   ```bash
   git clone https://github.com/MateusDuarteDev/pipeline-etl-infracoes-transito-df.git
   cd pipeline-etl-infracoes-transito-df
   ```
2. Instale as dependências (Python 3.9+):
   ```bash
   pip install -r requirements.txt
   ```
3. Baixe o CSV bruto seguindo as instruções de `data/README.md`.
4. Abra o notebook e execute todas as células:
   ```bash
   jupyter notebook notebooks/pipeline_etl_infracoes_df.ipynb
   ```

## Tecnologias

Python, Pandas, Matplotlib, expressões regulares (`re`), Jupyter Notebook.

## Fonte dos dados

Portal de Dados Abertos do Governo do Distrito Federal: https://www.dados.df.gov.br/pt/dataset#/infracoes-transito

## Autor

**Mateus Duarte Cavalcante**
[LinkedIn](https://www.linkedin.com/in/mateus-duarte-cavalcante/) · [GitHub](https://github.com/MateusDuarteDev)
