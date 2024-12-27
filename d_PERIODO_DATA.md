## Função DAX: `d_PERIODO_1`

Este código cria uma tabela dinâmica com diferentes agrupamentos temporais, incluindo Mensal, Trimestral, Anual e Year-to-Date (YTD).

**• tbl:** Agrupa a tabela d_CALENDARIO por colunas importantes, como ano.mes, Ano, Mês, Trimestre_ANO, e Trimestre_NUM.<br>
**• Meses:** Cria um agrupamento mensal.<br>
**• Trimestres:** Cria um agrupamento trimestral.<br>
**• Anos:** Cria um agrupamento anual.<br>
**• YTD_base** e **• YTD_Final:** Criam agrupamentos Year-to-Date (YTD) com base no ano e mês da tabela d_CALENDARIO.
Resultado: Une todos os agrupamentos em uma única tabela.

### Código:

```dax
d_PERIODO_1 = 
VAR tbl =
SUMMARIZE(
        d_CALENDARIO,
        d_CALENDARIO[ano.mes],
        d_CALENDARIO[Ano],
        d_CALENDARIO[Mês],
        d_CALENDARIO[Trimestre_ANO],
        d_CALENDARIO[Trimestre_NUM]
    )
VAR Meses = 
SELECTCOLUMNS(
    tbl,
    "ano.mes", d_CALENDARIO[ano.mes],
    "Seleção", FORMAT(DATE(d_CALENDARIO[Ano], d_CALENDARIO[Mês], 1), "YY-MMM"),
    "Grupamento", "Mensal",
    "Ordem", 100 * d_CALENDARIO[Ano] + d_CALENDARIO[Mês]
)
VAR Trimestres = 
SELECTCOLUMNS(
    tbl,
    "ano.mes", d_CALENDARIO[ano.mes],
    "Seleção", d_CALENDARIO[Trimestre_ANO],
    "Grupamento", "Trimestral",
    "Ordem", 100 * d_CALENDARIO[Ano] + 10 * d_CALENDARIO[Trimestre_NUM] + 10
)
VAR Anos =
SELECTCOLUMNS(
    tbl,
    "ano.mes", d_CALENDARIO[ano.mes],
    "Seleção", d_CALENDARIO[Ano],
    "Grupamento", "Anual",
    "Ordem", 100 * d_CALENDARIO[Ano]
)

VAR YTD_base =
GENERATE(
        tbl,
        VAR ano = d_CALENDARIO[Ano]
        VAR mes = d_CALENDARIO[Mês]
        RETURN
        CALCULATETABLE(
            SELECTCOLUMNS(
                SUMMARIZE(
                    d_CALENDARIO,
                    d_CALENDARIO[Ano],
                    d_CALENDARIO[Mês]
                ),
                "@Selecao", FORMAT(DATE(d_CALENDARIO[Ano], d_CALENDARIO[Mês], 1), "M\\MYY"),
                "@Ordem", 1000 * d_CALENDARIO[Ano] + d_CALENDARIO[Mês]
            ),
            REMOVEFILTERS(),
            d_CALENDARIO[Ano] = ano,
            d_CALENDARIO[Mês] >= mes            
        )
    )
VAR YTD_Final =
SELECTCOLUMNS(
    YTD_base,
    "ano.mes", d_CALENDARIO[ano.mes],
    "Seleção", [@Selecao],
    "Grupamento", "YTD",
    "Ordem", [@Ordem]
)

VAR Resultado = 
UNION(
    Meses,
    Trimestres,
    Anos,
    YTD_Final
)
RETURN Resultado
