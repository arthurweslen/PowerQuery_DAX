``` f_MARGEM = 
VAR tbl = 
SUMMARIZE(
    f_TABELA_FATO,
    f_TABELA_FATO[Produto],
    f_TABELA_FATO[Ano],
    d_VERSAO[Arquivo],
    d_VERSAO[id_arquivo],
    f_TABELA_FATO[Indicador],
    d_CALENDARIO[Data],
    f_TABELA_FATO[Valor],
    f_TABELA_FATO[UnidadeExternodida],
    d_FILTRO[Indicador_N2])

VAR Real_Margem_Externo_Interno =
SELECTCOLUMNS(
      FILTER(
        tbl,
        (LEFT(d_VERSAO[Arquivo],2)="Ou" && d_VERSAO[id_arquivo]="Dados Atuais") &&
        f_TABELA_FATO[Ano]=CONVERT(YEAR(NOW())-1,STRING)  &&
        f_TABELA_FATO[Indicador] in 
            {"Resultado S/I Realizado Interno",
            "Resultado S/I Realizado Externo",
            "Resultado S/I Lucrativo Interno",
            "Resultado S/I Dados",
            "VoluExterno Realizado Externo",
            "VoluExterno Lucrativo Interno",
            "VoluExterno Dados",
            "VoluExterno Realizado Interno",
            "VoluExterno FI"
            }
        ),
    "ID",1,  
    "Produto", "Real "& f_TABELA_FATO[Ano] ,
    "Total", f_TABELA_FATO[Valor],
    "Arquivo", d_VERSAO[Arquivo],
    "Data",d_CALENDARIO[Data],    
    "Grupo", f_TABELA_FATO[Indicador],
    "Indicador", f_TABELA_FATO[Produto],
    "UnidadeExternodida", f_TABELA_FATO[UnidadeExternodida],
    "Indicador_N2", d_FILTRO[Indicador_N2]
)
VAR Real_Margem_FI =
SELECTCOLUMNS(
      FILTER(
        tbl,
        (LEFT(d_VERSAO[Arquivo],2)="Ou" && d_VERSAO[id_arquivo]="Dados Atuais") &&
        f_TABELA_FATO[Ano]=CONVERT(YEAR(NOW())-1,STRING)  &&
        f_TABELA_FATO[Produto] in {"Margem FI PowerBI","Margem FI Outros"}
        ),
    "ID",1,  
    "Produto", "Real "& f_TABELA_FATO[Ano] ,
    "Total", f_TABELA_FATO[Valor],
    "Arquivo", d_VERSAO[Arquivo],
    "Data",d_CALENDARIO[Data],    
    "Grupo", "Resultado S/I FI",
    "Indicador", f_TABELA_FATO[Produto],
    "UnidadeExternodida", f_TABELA_FATO[UnidadeExternodida],
    "Indicador_N2", d_FILTRO[Indicador_N2]
)

VAR Real_Margem = UNION(Real_Margem_Externo_Interno,Real_Margem_FI)
//=====

//=== Resultado Inesperado DO REAL ===
VAR Real_INESPERADO_Externo_Interno =
SELECTCOLUMNS(
      FILTER(
        tbl,
        (LEFT(d_VERSAO[Arquivo],2)="Ou" && d_VERSAO[id_arquivo]="Dados Atuais") &&
        f_TABELA_FATO[Ano]=CONVERT(YEAR(NOW())-1,STRING)  &&
        f_TABELA_FATO[Indicador] in {"Resultado Inesperado Realizado Interno","Resultado Inesperado Realizado Externo","Resultado Inesperado Lucrativo Interno", "Resultado Inesperado Dados"} ) 
        ,
    "ID",1,  
    "Produto", "Real "& f_TABELA_FATO[Ano] ,
    "Total", f_TABELA_FATO[Valor],
    "Arquivo", d_VERSAO[Arquivo],
    "Data",d_CALENDARIO[Data],    
    "Grupo", IF( f_TABELA_FATO[Indicador]=BLANK(),"Resultado Inesperado Real "& f_TABELA_FATO[Ano] , f_TABELA_FATO[Indicador]),
    "Indicador", f_TABELA_FATO[Produto],
    "UnidadeExternodida", f_TABELA_FATO[UnidadeExternodida],
    "Indicador_N2", d_FILTRO[Indicador_N2]
)

VAR Real_INESPERADO_FI =
SELECTCOLUMNS(
      FILTER(
        tbl,
        (LEFT(d_VERSAO[Arquivo],2)="Ou" && d_VERSAO[id_arquivo]="Dados Atuais") &&
        f_TABELA_FATO[Ano]=CONVERT(YEAR(NOW())-1,STRING)  &&
        f_TABELA_FATO[Produto] in {"Resultado Inesperado FI PowerBI","Resultado Inesperado FI Outros"})
        ,
    "ID",1,  
    "Produto", "Real "& f_TABELA_FATO[Ano] ,
    "Total", f_TABELA_FATO[Valor],
    "Arquivo", d_VERSAO[Arquivo],
    "Data",d_CALENDARIO[Data],    
    "Grupo", "Resultado Inesperado FI",
    "Indicador", f_TABELA_FATO[Produto],
    "UnidadeExternodida", f_TABELA_FATO[UnidadeExternodida],
    "Indicador_N2", d_FILTRO[Indicador_N2]
)

VAR Real_INESPERADO = UNION(Real_INESPERADO_Externo_Interno,Real_INESPERADO_FI)
// ==============

// === MARGEM DE CONTRIBUIÇÃO DO REALIZADO ===
VAR Real_Contribuicao_Externo_Interno =
SELECTCOLUMNS(
      FILTER(
        tbl,
        (LEFT(d_VERSAO[Arquivo],2)="Ou" && d_VERSAO[id_arquivo]="Dados Atuais") &&
        f_TABELA_FATO[Ano]=CONVERT(YEAR(NOW())-1,STRING)  &&
        f_TABELA_FATO[Indicador] <> "Outros Derivados" &&
        f_TABELA_FATO[Indicador] in {"Resultado Cooperado Realizado Interno","Resultado Cooperado Realizado Externo", "Resultado Cooperado Lucrativo Interno", "Resultado Cooperado Dados"} ) 
        ,
    "ID",1,  
    "Produto", "Real "& f_TABELA_FATO[Ano] ,
    "Total", f_TABELA_FATO[Valor],
    "Arquivo", d_VERSAO[Arquivo],
    "Data",d_CALENDARIO[Data],
    
    "Grupo", IF( f_TABELA_FATO[Indicador]=BLANK(),"Resultado Inesperado Real "& f_TABELA_FATO[Ano] , f_TABELA_FATO[Indicador]),
    "Indicador", f_TABELA_FATO[Produto],
    "UnidadeExternodida", f_TABELA_FATO[UnidadeExternodida],
    "Indicador_N2", d_FILTRO[Indicador_N2]
)

VAR Real_Contribuicao_FI =
SELECTCOLUMNS(
      FILTER(
        tbl,
        (LEFT(d_VERSAO[Arquivo],2)="Ou" && d_VERSAO[id_arquivo]="Dados Atuais") &&
        f_TABELA_FATO[Ano]=CONVERT(YEAR(NOW())-1,STRING)  &&
        f_TABELA_FATO[Indicador] <> "Outros Derivados" &&
         f_TABELA_FATO[Produto] in {"Resultado Cooperado FI PowerBI","Resultado Cooperado FI Outros"}),
    "ID",1,  
    "Produto", "Real "& f_TABELA_FATO[Ano] ,
    "Total", f_TABELA_FATO[Valor],
    "Arquivo", d_VERSAO[Arquivo],
    "Data",d_CALENDARIO[Data],
    
    "Grupo", "Resultado Cooperado FI",
    "Indicador", f_TABELA_FATO[Produto],
    "UnidadeExternodida", f_TABELA_FATO[UnidadeExternodida],
    "Indicador_N2", d_FILTRO[Indicador_N2]
)

VAR Real_Contribuicao = UNION(Real_Contribuicao_Externo_Interno,Real_Contribuicao_FI)
//=========

//=== Resultado S/I DO PE ===
VAR PE_Margem_Bruta_Externo_Interno =
SELECTCOLUMNS(
      FILTER(
        tbl,
        (LEFT(d_VERSAO[Arquivo],2)="PE" && d_VERSAO[id_arquivo]="PE Atual") &&
        f_TABELA_FATO[Ano]=CONVERT(YEAR(NOW()),STRING)  &&
        f_TABELA_FATO[Indicador] in 
             {"Resultado S/I Realizado Interno",
            "Resultado S/I Realizado Externo",
            "Resultado S/I Lucrativo Interno",
            "Resultado S/I Dados",
            "VoluExterno Realizado Externo",
            "VoluExterno Lucrativo Interno",
            "VoluExterno Dados",
            "VoluExterno Realizado Interno",
            "VoluExterno FI"
            }),
    "ID",2,    
    "Produto", "PE "& f_TABELA_FATO[Ano] ,
    "Total", f_TABELA_FATO[Valor],
    "Arquivo", d_VERSAO[Arquivo],
    "Data",d_CALENDARIO[Data],
    
    "Grupo", f_TABELA_FATO[Indicador],
    "Indicador", f_TABELA_FATO[Produto],
    "UnidadeExternodida", f_TABELA_FATO[UnidadeExternodida],
    "Indicador_N2", d_FILTRO[Indicador_N2]    
)

VAR PE_Margem_Bruta_FI =
SELECTCOLUMNS(
      FILTER(
        tbl,
        (LEFT(d_VERSAO[Arquivo],2)="PE" && d_VERSAO[id_arquivo]="PE Atual") &&
        f_TABELA_FATO[Ano]=CONVERT(YEAR(NOW()),STRING)  &&
        f_TABELA_FATO[Produto] in {"Margem FI PowerBI","Margem FI Outros"}),
    "ID",2,    
    "Produto", "PE "& f_TABELA_FATO[Ano] ,
    "Total", f_TABELA_FATO[Valor],
    "Arquivo", d_VERSAO[Arquivo],
    "Data",d_CALENDARIO[Data],
    
    "Grupo", "Resultado S/I FI",
    "Indicador", f_TABELA_FATO[Produto],
    "UnidadeExternodida", f_TABELA_FATO[UnidadeExternodida],
    "Indicador_N2", d_FILTRO[Indicador_N2]    
)

VAR PE_Margem_Bruta = UNION(PE_Margem_Bruta_Externo_Interno,PE_Margem_Bruta_FI)
//===============================

//=== MARGEM INESPERADO DO PE ===
VAR PE_Margem_INESPERADO_Externo_Interno =
SELECTCOLUMNS(
      FILTER(
        tbl,
        (LEFT(d_VERSAO[Arquivo],2)="PE" && d_VERSAO[id_arquivo]="PE Atual") &&
        f_TABELA_FATO[Ano]=CONVERT(YEAR(NOW()),STRING)  &&
        f_TABELA_FATO[Indicador] in {"Resultado Inesperado Realizado Interno","Resultado Inesperado Realizado Externo","Resultado Inesperado Lucrativo Interno", "Resultado Inesperado Dados"}   
        ),
    "ID",2,    
    "Produto", "PE "& f_TABELA_FATO[Ano] ,
    "Total", f_TABELA_FATO[Valor],
    "Arquivo", d_VERSAO[Arquivo],
    "Data",d_CALENDARIO[Data],
    
    "Grupo", f_TABELA_FATO[Indicador],
    "Indicador", f_TABELA_FATO[Produto],
    "UnidadeExternodida", f_TABELA_FATO[UnidadeExternodida],
    "Indicador_N2", d_FILTRO[Indicador_N2]    
)

VAR PE_Margem_INESPERADO_FI =
SELECTCOLUMNS(
      FILTER(
        tbl,
        (LEFT(d_VERSAO[Arquivo],2)="PE" && d_VERSAO[id_arquivo]="PE Atual") &&
        f_TABELA_FATO[Ano]=CONVERT(YEAR(NOW()),STRING)  &&
        f_TABELA_FATO[Produto] in {"Resultado Inesperado FI PowerBI","Resultado Inesperado FI Outros"})  
        ,
    "ID",2,    
    "Produto", "PE "& f_TABELA_FATO[Ano] ,
    "Total", f_TABELA_FATO[Valor],
    "Arquivo", d_VERSAO[Arquivo],
    "Data",d_CALENDARIO[Data],
    
    "Grupo","Resultado Inesperado FI",
    "Indicador", f_TABELA_FATO[Produto],
    "UnidadeExternodida", f_TABELA_FATO[UnidadeExternodida],
    "Indicador_N2", d_FILTRO[Indicador_N2]    
)
VAR PE_Margem_INESPERADO = UNION(PE_Margem_INESPERADO_Externo_Interno,PE_Margem_INESPERADO_FI)
//===========

// === MARGEM DE CONTRIBUIÇÃO DO PE ====
VAR PE_Margem_Contribuicao_Externo_Interno =
SELECTCOLUMNS(
      FILTER(
        tbl,
        (LEFT(d_VERSAO[Arquivo],2)="PE" && d_VERSAO[id_arquivo]="PE Atual") &&
        f_TABELA_FATO[Ano]=CONVERT(YEAR(NOW()),STRING)  &&
        f_TABELA_FATO[Indicador] in {"Resultado Cooperado Realizado Interno","Resultado Cooperado Realizado Externo", "Resultado Cooperado Lucrativo Interno", "Resultado Cooperado Dados"}) 
        ,
    "ID",2,    
    "Produto", "PE "& f_TABELA_FATO[Ano] ,
    "Total", f_TABELA_FATO[Valor],
    "Arquivo", d_VERSAO[Arquivo],
    "Data",d_CALENDARIO[Data],
    
    "Grupo", f_TABELA_FATO[Indicador],
    "Indicador", f_TABELA_FATO[Produto],
    "UnidadeExternodida", f_TABELA_FATO[UnidadeExternodida],
    "Indicador_N2", d_FILTRO[Indicador_N2]    
)

VAR PE_Margem_Contribuicao_FI =
SELECTCOLUMNS(
      FILTER(
        tbl,
        (LEFT(d_VERSAO[Arquivo],2)="PE" && d_VERSAO[id_arquivo]="PE Atual") &&
        f_TABELA_FATO[Ano]=CONVERT(YEAR(NOW()),STRING)  &&  
        f_TABELA_FATO[Produto] in {"Resultado Cooperado FI PowerBI","Resultado Cooperado FI Outros"})
        ,
    "ID",2,    
    "Produto", "PE "& f_TABELA_FATO[Ano] ,
    "Total", f_TABELA_FATO[Valor],
    "Arquivo", d_VERSAO[Arquivo],
    "Data",d_CALENDARIO[Data],
    
    "Grupo", "Resultado Cooperado FI",
    "Indicador", f_TABELA_FATO[Produto],
    "UnidadeExternodida", f_TABELA_FATO[UnidadeExternodida],
    "Indicador_N2", d_FILTRO[Indicador_N2]    
)
VAR PE_Margem_Contribuicao = UNION(PE_Margem_Contribuicao_Externo_Interno,PE_Margem_Contribuicao_FI)
//=====

//=== Resultado S/I DO OL ANTERIOR ===
 VAR OL_Ant_Margem_Bruta_Externo_Interno =
SELECTCOLUMNS(
      FILTER(
        tbl,
        (LEFT(d_VERSAO[Arquivo],2)="Ou" && d_VERSAO[id_arquivo]="OL Anterior") &&
        f_TABELA_FATO[Ano]=CONVERT(YEAR(NOW()),STRING)  &&
        f_TABELA_FATO[Indicador] in 
             {"Resultado S/I Realizado Interno",
            "Resultado S/I Realizado Externo",
            "Resultado S/I Lucrativo Interno",
            "Resultado S/I Dados",
            "VoluExterno Realizado Externo",
            "VoluExterno Lucrativo Interno",
            "VoluExterno Dados",
            "VoluExterno Realizado Interno",
            "VoluExterno FI"
            }),
    "ID",3,    
    "Produto", "OL Ant "& f_TABELA_FATO[Ano] ,
    "Total", f_TABELA_FATO[Valor],
    "Arquivo", d_VERSAO[Arquivo],
    "Data",d_CALENDARIO[Data],
    
    "Grupo", f_TABELA_FATO[Indicador],
    "Indicador", f_TABELA_FATO[Produto],
    "UnidadeExternodida", f_TABELA_FATO[UnidadeExternodida],
    "Indicador_N2", d_FILTRO[Indicador_N2]
)
 VAR OL_Ant_Margem_Bruta_FI =
SELECTCOLUMNS(
      FILTER(
        tbl,
        (LEFT(d_VERSAO[Arquivo],2)="Ou" && d_VERSAO[id_arquivo]="OL Anterior") &&
        f_TABELA_FATO[Ano]=CONVERT(YEAR(NOW()),STRING)  &&
        f_TABELA_FATO[Produto] in {"Margem FI PowerBI","Margem FI Outros"}),
    "ID",3,    
    "Produto", "OL Ant "& f_TABELA_FATO[Ano] ,
    "Total", f_TABELA_FATO[Valor],
    "Arquivo", d_VERSAO[Arquivo],
    "Data",d_CALENDARIO[Data],
    
    "Grupo", "Resultado S/I FI",
    "Indicador", f_TABELA_FATO[Produto],
    "UnidadeExternodida", f_TABELA_FATO[UnidadeExternodida],
    "Indicador_N2", d_FILTRO[Indicador_N2]
)
VAR OL_Ant_Margem_Bruta = UNION(OL_Ant_Margem_Bruta_Externo_Interno,OL_Ant_Margem_Bruta_FI)
//========

//=== Resultado Inesperado DO OL ANTERIOR ===
VAR OL_Ant_Margem_INESPERADO_Externo_Interno =
SELECTCOLUMNS(
      FILTER(
        tbl,
        (LEFT(d_VERSAO[Arquivo],2)="Ou" && d_VERSAO[id_arquivo]="OL Anterior") &&
        f_TABELA_FATO[Ano]=CONVERT(YEAR(NOW()),STRING)  &&
        f_TABELA_FATO[Indicador] in {"Resultado Inesperado Realizado Interno","Resultado Inesperado Realizado Externo","Resultado Inesperado Lucrativo Interno", "Resultado Inesperado Dados"} 
        ),
    "ID",3,    
    "Produto", "OL Ant "& f_TABELA_FATO[Ano] ,
    "Total", f_TABELA_FATO[Valor],
    "Arquivo", d_VERSAO[Arquivo],
    "Data",d_CALENDARIO[Data],
    
    "Grupo", f_TABELA_FATO[Indicador],
    "Indicador", f_TABELA_FATO[Produto],
    "UnidadeExternodida", f_TABELA_FATO[UnidadeExternodida],
    "Indicador_N2", d_FILTRO[Indicador_N2]
)

VAR OL_Ant_Margem_INESPERADO_FI =
SELECTCOLUMNS(
      FILTER(
        tbl,
        (LEFT(d_VERSAO[Arquivo],2)="Ou" && d_VERSAO[id_arquivo]="OL Anterior") &&
        f_TABELA_FATO[Ano]=CONVERT(YEAR(NOW()),STRING)  &&
        f_TABELA_FATO[Produto] in {"Resultado Inesperado FI PowerBI","Resultado Inesperado FI Outros"}
        ),
    "ID",3,    
    "Produto", "OL Ant "& f_TABELA_FATO[Ano] ,
    "Total", f_TABELA_FATO[Valor],
    "Arquivo", d_VERSAO[Arquivo],
    "Data",d_CALENDARIO[Data],
    
    "Grupo", "Resultado Inesperado FI",
    "Indicador", f_TABELA_FATO[Produto],
    "UnidadeExternodida", f_TABELA_FATO[UnidadeExternodida],
    "Indicador_N2", d_FILTRO[Indicador_N2]
)
VAR OL_Ant_Margem_INESPERADO = UNION(OL_Ant_Margem_INESPERADO_Externo_Interno,OL_Ant_Margem_INESPERADO_FI)
//==============

// ===== Resultado Cooperado DO OL ANTERIOR ======
VAR OL_Ant_Margem_Contribuicao_Interno_Externo =
SELECTCOLUMNS(
      FILTER(
        tbl,
        (LEFT(d_VERSAO[Arquivo],2)="Ou" && d_VERSAO[id_arquivo]="OL Anterior") &&
        f_TABELA_FATO[Ano]=CONVERT(YEAR(NOW()),STRING)  &&
        f_TABELA_FATO[Indicador] in {"Resultado Cooperado Realizado Interno","Resultado Cooperado Realizado Externo", "Resultado Cooperado Lucrativo Interno", "Resultado Cooperado Dados"}
        ),
    "ID",3,    
    "Produto", "OL Ant "& f_TABELA_FATO[Ano] ,
    "Total", f_TABELA_FATO[Valor],
    "Arquivo", d_VERSAO[Arquivo],
    "Data",d_CALENDARIO[Data],
    
    "Grupo", f_TABELA_FATO[Indicador],
    "Indicador", f_TABELA_FATO[Produto],
    "UnidadeExternodida", f_TABELA_FATO[UnidadeExternodida],
    "Indicador_N2", d_FILTRO[Indicador_N2]
)

VAR OL_Ant_Margem_Contribuicao_FI =
SELECTCOLUMNS(
      FILTER(
        tbl,
        (LEFT(d_VERSAO[Arquivo],2)="Ou" && d_VERSAO[id_arquivo]="OL Anterior") &&
        f_TABELA_FATO[Ano]=CONVERT(YEAR(NOW()),STRING)  &&
        f_TABELA_FATO[Produto] in {"Resultado Cooperado FI PowerBI","Resultado Cooperado FI Outros"}
        ),
    "ID",3,    
    "Produto", "OL Ant "& f_TABELA_FATO[Ano] ,
    "Total", f_TABELA_FATO[Valor],
    "Arquivo", d_VERSAO[Arquivo],
    "Data",d_CALENDARIO[Data],
    
    "Grupo", "Resultado Cooperado FI",
    "Indicador", f_TABELA_FATO[Produto],
    "UnidadeExternodida", f_TABELA_FATO[UnidadeExternodida],
    "Indicador_N2", d_FILTRO[Indicador_N2]
)

VAR OL_Ant_Margem_Contribuicao = UNION(OL_Ant_Margem_Contribuicao_Interno_Externo,OL_Ant_Margem_Contribuicao_FI)
//===========================


//=== Resultado S/I DO Dados Atuais ===
VAR OL_Atual_Margem_Bruta_Externo_Interno =
SELECTCOLUMNS(
      FILTER(
        tbl,
       (LEFT(d_VERSAO[Arquivo],2)="Ou" && d_VERSAO[id_arquivo]="Dados Atuais") &&
        f_TABELA_FATO[Ano]=CONVERT(YEAR(NOW()),STRING)  &&
        f_TABELA_FATO[Indicador] in 
             {"Resultado S/I Realizado Interno",
            "Resultado S/I Realizado Externo",
            "Resultado S/I Lucrativo Interno",
            "Resultado S/I Dados",
            "VoluExterno Realizado Externo",
            "VoluExterno Lucrativo Interno",
            "VoluExterno Dados",
            "VoluExterno Realizado Interno",
            "VoluExterno FI"
            }),
    "ID",4, 
    "Produto", "OL "& f_TABELA_FATO[Ano] ,
    "Total", f_TABELA_FATO[Valor],
    "Arquivo", d_VERSAO[Arquivo],
    "Data",d_CALENDARIO[Data],
    
    "Grupo", f_TABELA_FATO[Indicador],
    "Indicador", f_TABELA_FATO[Produto],
    "UnidadeExternodida", f_TABELA_FATO[UnidadeExternodida],
    "Indicador_N2", d_FILTRO[Indicador_N2]
)

VAR OL_Atual_Margem_Bruta_FI =
SELECTCOLUMNS(
      FILTER(
        tbl,
       (LEFT(d_VERSAO[Arquivo],2)="Ou" && d_VERSAO[id_arquivo]="Dados Atuais") &&
        f_TABELA_FATO[Ano]=CONVERT(YEAR(NOW()),STRING)  &&
        f_TABELA_FATO[Produto] in {"Margem FI PowerBI","Margem FI Outros"}),
    "ID",4, 
    "Produto", "OL "& f_TABELA_FATO[Ano] ,
    "Total", f_TABELA_FATO[Valor],
    "Arquivo", d_VERSAO[Arquivo],
    "Data",d_CALENDARIO[Data],
    
    "Grupo", "Resultado S/I FI",
    "Indicador", f_TABELA_FATO[Produto],
    "UnidadeExternodida", f_TABELA_FATO[UnidadeExternodida],
    "Indicador_N2", d_FILTRO[Indicador_N2]
)

VAR OL_Atual_Margem_Bruta = UNION(OL_Atual_Margem_Bruta_Externo_Interno,OL_Atual_Margem_Bruta_FI)
//=============

//=== Resultado Inesperado DO Dados Atuais ===
VAR OL_Atual_Margem_INESPERADO_Externo_Interno =
SELECTCOLUMNS(
      FILTER(
        tbl,
       (LEFT(d_VERSAO[Arquivo],2)="Ou" && d_VERSAO[id_arquivo]="Dados Atuais") &&
        f_TABELA_FATO[Ano]=CONVERT(YEAR(NOW()),STRING)  &&
        f_TABELA_FATO[Indicador] in {"Resultado Inesperado Realizado Interno","Resultado Inesperado Realizado Externo","Resultado Inesperado Lucrativo Interno", "Resultado Inesperado Dados"}
        ),
    "ID",4, 
    "Produto", "OL "& f_TABELA_FATO[Ano] ,
    "Total", f_TABELA_FATO[Valor],
    "Arquivo", d_VERSAO[Arquivo],
    "Data",d_CALENDARIO[Data],
    
    "Grupo", f_TABELA_FATO[Indicador],
    "Indicador", f_TABELA_FATO[Produto],
    "UnidadeExternodida", f_TABELA_FATO[UnidadeExternodida],
    "Indicador_N2", d_FILTRO[Indicador_N2]
)

VAR OL_Atual_Margem_INESPERADO_FI =
SELECTCOLUMNS(
      FILTER(
        tbl,
       (LEFT(d_VERSAO[Arquivo],2)="Ou" && d_VERSAO[id_arquivo]="Dados Atuais") &&
        f_TABELA_FATO[Ano]=CONVERT(YEAR(NOW()),STRING)  &&
        f_TABELA_FATO[Produto] in {"Resultado Inesperado FI PowerBI","Resultado Inesperado FI Outros"}
        ),
    "ID",4, 
    "Produto", "OL "& f_TABELA_FATO[Ano] ,
    "Total", f_TABELA_FATO[Valor],
    "Arquivo", d_VERSAO[Arquivo],
    "Data",d_CALENDARIO[Data],
    
    "Grupo", "Resultado Inesperado FI",
    "Indicador", f_TABELA_FATO[Produto],
    "UnidadeExternodida", f_TABELA_FATO[UnidadeExternodida],
    "Indicador_N2", d_FILTRO[Indicador_N2]
)

VAR OL_Atual_Margem_INESPERADO = UNION(OL_Atual_Margem_INESPERADO_Externo_Interno,OL_Atual_Margem_INESPERADO_FI)
//===========================================

// ==== Resultado Cooperado DO Dados Atuais ====
VAR OL_Atual_Margem_Contribuicao_Interno_Externo =
SELECTCOLUMNS(
      FILTER(
        tbl,
       (LEFT(d_VERSAO[Arquivo],2)="Ou" && d_VERSAO[id_arquivo]="Dados Atuais") &&
        f_TABELA_FATO[Ano]=CONVERT(YEAR(NOW()),STRING)  && 
        f_TABELA_FATO[Indicador] in  {"Resultado Cooperado Realizado Interno","Resultado Cooperado Realizado Externo", "Resultado Cooperado Lucrativo Interno", "Resultado Cooperado Dados"} 
        ),
    "ID",4, 
    "Produto", "OL "& f_TABELA_FATO[Ano] ,
    "Total", f_TABELA_FATO[Valor],
    "Arquivo", d_VERSAO[Arquivo],
    "Data",d_CALENDARIO[Data],
    
    "Grupo", f_TABELA_FATO[Indicador],
    "Indicador", f_TABELA_FATO[Produto],
    "UnidadeExternodida", f_TABELA_FATO[UnidadeExternodida],
    "Indicador_N2", d_FILTRO[Indicador_N2]
)

VAR OL_Atual_Margem_Contribuicao_FI =
SELECTCOLUMNS(
      FILTER(
        tbl,
       (LEFT(d_VERSAO[Arquivo],2)="Ou" && d_VERSAO[id_arquivo]="Dados Atuais") &&
        f_TABELA_FATO[Ano]=CONVERT(YEAR(NOW()),STRING)  && 
        f_TABELA_FATO[Produto] in {"Resultado Cooperado FI PowerBI","Resultado Cooperado FI Outros"}       
        ),
    "ID",4, 
    "Produto", "OL "& f_TABELA_FATO[Ano] ,
    "Total", f_TABELA_FATO[Valor],
    "Arquivo", d_VERSAO[Arquivo],
    "Data",d_CALENDARIO[Data],
    
    "Grupo","Resultado Cooperado FI",
    "Indicador", f_TABELA_FATO[Produto],
    "UnidadeExternodida", f_TABELA_FATO[UnidadeExternodida],
    "Indicador_N2", d_FILTRO[Indicador_N2]
)

VAR  OL_Atual_Margem_Contribuicao = UNION(OL_Atual_Margem_Contribuicao_Interno_Externo,OL_Atual_Margem_Contribuicao_FI)
//===========================

//=== Resultado S/I DO OL POSTERIOR ===
VAR OL_Posterior_Margem_Bruta_Externo_Interno =
SELECTCOLUMNS(
      FILTER(
        tbl,
       (LEFT(d_VERSAO[Arquivo],2)="Ou" && d_VERSAO[id_arquivo]="Dados Atuais") &&
        f_TABELA_FATO[Ano]=CONVERT(YEAR(NOW())+1,STRING)  &&
        f_TABELA_FATO[Indicador] in 
             {"Resultado S/I Realizado Interno",
            "Resultado S/I Realizado Externo",
            "Resultado S/I Lucrativo Interno",
            "Resultado S/I Dados",
            "VoluExterno Realizado Externo",
            "VoluExterno Lucrativo Interno",
            "VoluExterno Dados",
            "VoluExterno Realizado Interno",
            "VoluExterno FI"
            }),
    "ID",5,   
    "Produto", "OL "& f_TABELA_FATO[Ano] ,
    "Total", f_TABELA_FATO[Valor],
    "Arquivo", d_VERSAO[Arquivo],
    "Data",d_CALENDARIO[Data],
    
    "Grupo", f_TABELA_FATO[Indicador],
    "Indicador", f_TABELA_FATO[Produto],
    "UnidadeExternodida", f_TABELA_FATO[UnidadeExternodida],
    "Indicador_N2", d_FILTRO[Indicador_N2]
)

VAR OL_Posterior_Margem_Bruta_FI =
SELECTCOLUMNS(
      FILTER(
        tbl,
       (LEFT(d_VERSAO[Arquivo],2)="Ou" && d_VERSAO[id_arquivo]="Dados Atuais") &&
        f_TABELA_FATO[Ano]=CONVERT(YEAR(NOW())+1,STRING)  &&
        f_TABELA_FATO[Produto] in {"Margem FI PowerBI","Margem FI Outros"}),
    "ID",5,   
    "Produto", "OL "& f_TABELA_FATO[Ano] ,
    "Total", f_TABELA_FATO[Valor],
    "Arquivo", d_VERSAO[Arquivo],
    "Data",d_CALENDARIO[Data],
    
    "Grupo", "Resultado S/I FI",
    "Indicador", f_TABELA_FATO[Produto],
    "UnidadeExternodida", f_TABELA_FATO[UnidadeExternodida],
    "Indicador_N2", d_FILTRO[Indicador_N2]
)

VAR OL_Posterior_Margem_Bruta = UNION(OL_Posterior_Margem_Bruta_Externo_Interno,OL_Posterior_Margem_Bruta_FI)
//=========

// === Resultado Inesperado DO OL POSTERIOR === 
VAR OL_Posterior_Margem_INESPERADO_Externo_Interno =
SELECTCOLUMNS(
      FILTER(
        tbl,
       (LEFT(d_VERSAO[Arquivo],2)="Ou" && d_VERSAO[id_arquivo]="Dados Atuais") &&
        f_TABELA_FATO[Ano]=CONVERT(YEAR(NOW())+1,STRING)  &&
        f_TABELA_FATO[Indicador] in {"Resultado Inesperado Realizado Interno","Resultado Inesperado Realizado Externo","Resultado Inesperado Lucrativo Interno", "Resultado Inesperado Dados"}
        ),
    "ID",5,   
    "Produto", "OL "& f_TABELA_FATO[Ano] ,
    "Total", f_TABELA_FATO[Valor],
    "Arquivo", d_VERSAO[Arquivo],
    "Data",d_CALENDARIO[Data],
    
    "Grupo", f_TABELA_FATO[Indicador],
    "Indicador", f_TABELA_FATO[Produto],
    "UnidadeExternodida", f_TABELA_FATO[UnidadeExternodida],
    "Indicador_N2", d_FILTRO[Indicador_N2]
)

VAR OL_Posterior_Margem_INESPERADO_FI =
SELECTCOLUMNS(
      FILTER(
        tbl,
       (LEFT(d_VERSAO[Arquivo],2)="Ou" && d_VERSAO[id_arquivo]="Dados Atuais") &&
        f_TABELA_FATO[Ano]=CONVERT(YEAR(NOW())+1,STRING)  &&
        f_TABELA_FATO[Produto] in {"Resultado Inesperado FI PowerBI","Resultado Inesperado FI Outros"}
        ),
    "ID",5,   
    "Produto", "OL "& f_TABELA_FATO[Ano] ,
    "Total", f_TABELA_FATO[Valor],
    "Arquivo", d_VERSAO[Arquivo],
    "Data",d_CALENDARIO[Data],
    
    "Grupo", "Resultado Inesperado FI",
    "Indicador", f_TABELA_FATO[Produto],
    "UnidadeExternodida", f_TABELA_FATO[UnidadeExternodida],
    "Indicador_N2", d_FILTRO[Indicador_N2]
)

VAR OL_Posterior_Margem_INESPERADO = UNION(OL_Posterior_Margem_INESPERADO_Externo_Interno, OL_Posterior_Margem_INESPERADO_FI)
//====================================================
//=== Resultado Cooperado DO OL POSTERIOR ===
VAR OL_Posterior_Margem_Contribuicao_Externo_Interno =
SELECTCOLUMNS(
      FILTER(
        tbl,
       (LEFT(d_VERSAO[Arquivo],2)="Ou" && d_VERSAO[id_arquivo]="Dados Atuais") &&
        f_TABELA_FATO[Ano]=CONVERT(YEAR(NOW())+1,STRING)  &&
        f_TABELA_FATO[Indicador] in  {"Resultado Cooperado Realizado Interno","Resultado Cooperado Realizado Externo", "Resultado Cooperado Lucrativo Interno", "Resultado Cooperado Dados"}
        ),
    "ID",5,   
    "Produto", "OL "& f_TABELA_FATO[Ano] ,
    "Total", f_TABELA_FATO[Valor],
    "Arquivo", d_VERSAO[Arquivo],
    "Data",d_CALENDARIO[Data],
    
    "Grupo", f_TABELA_FATO[Indicador],
    "Indicador", f_TABELA_FATO[Produto],
    "UnidadeExternodida", f_TABELA_FATO[UnidadeExternodida],
    "Indicador_N2", d_FILTRO[Indicador_N2]
)

VAR OL_Posterior_Margem_Contribuicao_FI =
SELECTCOLUMNS(
      FILTER(
        tbl,
       (LEFT(d_VERSAO[Arquivo],2)="Ou" && d_VERSAO[id_arquivo]="Dados Atuais") &&
        f_TABELA_FATO[Ano]=CONVERT(YEAR(NOW())+1,STRING)  &&
         f_TABELA_FATO[Produto] in {"Resultado Cooperado FI PowerBI","Resultado Cooperado FI Outros"}
        ),
    "ID",5,   
    "Produto", "OL "& f_TABELA_FATO[Ano] ,
    "Total", f_TABELA_FATO[Valor],
    "Arquivo", d_VERSAO[Arquivo],
    "Data",d_CALENDARIO[Data],
    
    "Grupo", "Resultado Cooperado FI",
    "Indicador", f_TABELA_FATO[Produto],
    "UnidadeExternodida", f_TABELA_FATO[UnidadeExternodida],
    "Indicador_N2", d_FILTRO[Indicador_N2]
)

VAR OL_Posterior_Margem_Contribuicao = UNION(OL_Posterior_Margem_Contribuicao_Externo_Interno,OL_Posterior_Margem_Contribuicao_FI)
//====    

VAR resultado = 
UNION(
    Real_Margem,
    Real_INESPERADO,
    Real_Contribuicao,
    PE_Margem_Bruta,
    PE_Margem_INESPERADO,
    PE_Margem_Contribuicao,
    OL_Ant_Margem_Bruta,
    OL_Ant_Margem_INESPERADO,
    OL_Ant_Margem_Contribuicao,
    OL_Atual_Margem_Bruta,
    OL_Atual_Margem_INESPERADO,
    OL_Atual_Margem_Contribuicao,
    OL_Posterior_Margem_Bruta,
    OL_Posterior_Margem_INESPERADO,
    OL_Posterior_Margem_Contribuicao)
```
RETURN  resultado ```
