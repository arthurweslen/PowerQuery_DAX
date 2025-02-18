## Como Criar Formatação Dinâmica para Medidas no Power BI

1. Crie uma medida qualquer.
2. No formato da medida, selecione a opção **"Dinâmico"**.
3. Preencha com a fórmula abaixo:

```dax
VAR _Valor = SELECTEDMEASURE()
VAR _Formato = 
    SWITCH(
        TRUE(),
        //Valores Negativos
        _Valor <= -1000000000000, "R$ #,#0,,,,00T; R$ #,#0,,,,.0 T",
        _Valor <= -1000000000, "R$ #,#0,,,.00B; R$ #,#0,,,.0 BI",
        _Valor <= -1000000, "R$ #,#0,,.00M; R$ #,#0,,.0 MI",
        _Valor <= -1000, "R$ #,#0,.00K; R$ #,#0,.0 K",
        
        //Valores Positivos        
        _Valor >= 1000000000000, "R$ #,#0,,,,.0 T",
        _Valor >= 1000000000, "R$ #,#0,,,.0 BI",
        _Valor >= 1000000, "R$ #,#0,,.0 MI",
        _Valor >= 1000, "R$ #,#0,.0 K",
        "R$ #,0.00;R$ #,0.00"
    )
RETURN
"""" & FORMAT(SELECTEDMEASURE(), _Formato) & """"

```
Abaixo é uma opção com a medida Sem o R$ e sem casas decimais

```dax
VAR _Valor = SELECTEDMEASURE()
VAR _Formato = 
    SWITCH(
        TRUE(),
        //Valores Negativos
        _Valor <= -1000000000000, "#0,,,,00T; #0,,,, T",
        _Valor <= -1000000000, "#0,,,.00B; #0,,, BI",
        _Valor <= -1000000, "#0,,.00M; #0,, MI",
        _Valor <= -1000, "#0,.00K; #0, K",
        
        //Valores Positivos        
        _Valor >= 1000000000000, "#0,,,, T",
        _Valor >= 1000000000, "#0,,, BI",
        _Valor >= 1000000, "#0,, MI",
        _Valor >= 1000, "#0, K",
        "0;0"
    )
RETURN
"""" & FORMAT(SELECTEDMEASURE(), _Formato) & """"

```
