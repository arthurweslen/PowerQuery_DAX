## Como Criar Formatação Dinâmica para Medidas no Power BI

1. Crie uma medida qualquer.
2. No formato da medida, selecione a opção **"Dinâmico"**.
3. Preencha com a fórmula abaixo:

```dax
VAR _Valor = SELECTEDMEASURE()
VAR _Formato = 
    SWITCH(
        TRUE(),
        _Valor <= -1000000000, "R$ #,#0,,,.00B; R$ #,#0,,,.0 BI",
        _Valor <= -1000000, "R$ #,#0,,.00M; R$ #,#0,,.0 MI",
        _Valor <= -1000, "R$ #,#0,.00K; R$ #,#0,.0 K",
        _Valor >= 1000000000, "R$ #,#0,,,.0 BI",
        _Valor >= 1000000, "R$ #,#0,,.0 MI",
        _Valor >= 1000, "R$ #,#0,.0 K",
        "R$ #,0.00;R$ #,0.00"
    )
RETURN
"""" & FORMAT(SELECTEDMEASURE(), _Formato) & """"
