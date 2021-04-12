---
title: Renderejador de barras compósitos Azure Monitor
description: Saiba mais sobre toda a visualização do renderizador de barras compósito do Azure Monitor.
services: azure-monitor
author: lgayhardt
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 9/04/2020
ms.author: lagayhar
ms.openlocfilehash: 9a02299853174192c6963cbb382ceb1aa06ac088
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "101728608"
---
# <a name="composite-bar-renderer"></a>Renderizador de barras compósitos

O livro permite renderizar dados usando barra composta, uma barra composta por várias barras.

A imagem abaixo mostra a barra composta para o estado da base de dados que representa quantos servidores estão online, offline e em estado de recuperação.

![Screenshot da barra composta para o estado da base de dados.](./media/workbooks-composite-bar/database-status.png)

O renderizador de barras compósitos é suportado para grelhas, azulejos e visualizações de gráficos.

## <a name="adding-composite-bar-renderer"></a>Adicionando renderizador de barras compósitos

1. Mude o livro para editar o modo selecionando editar o item da barra de ferramentas *Editar.*
2. *Selecione Adicionar* e adicione *consulta*.
3. Desa *estade fonte de dados* para "JSON" e *Visualização* para "Grid".
4. Adicione os seguintes dados JSON.

```json
[
    {"sub":"X", "server": "A", "online": 20, "recovering": 3, "offline": 4, "total": 27},
    {"sub":"X", "server": "B", "online": 15, "recovering": 8, "offline": 5, "total": 28},
    {"sub":"Y", "server": "C", "online": 25, "recovering": 4, "offline": 5, "total": 34},
    {"sub":"Y", "server": "D", "online": 18, "recovering": 6, "offline": 9, "total": 33}
]
```

5. Fazer consulta.
6. Selecione **Definições de colunas** para abrir as definições.
7. Selecione "total" de *Colunas* e escolha "Barra Composta" para *renderizador de colunas*.
8. Defina as seguintes definições em *Definições de barra composta .*

| Nome da coluna | Cor        |
|-------------|--------------|
| online      | Green        |
| recuperação  | Amarelo       |
| offline     | Vermelho (Brilhante) |

9. Adicionar etiqueta:`["online"] of ["total"] are healthy`
10. Nas definições de coluna para online, offline e recuperação pode definir o renderizador de colunas para "Hidden" (Opcional).
11. Selecione *etiquetas* na parte superior e atualize a etiqueta para a coluna total como "Estado da base de dados" (Opcional).
12. Selecione em **Aplicar**

As definições da barra composta serão semelhantes à imagem abaixo:

![Screenshot das definições de coluna de barra composta com as definições acima descritas.](./media/workbooks-composite-bar/composite-bar-settings.png)

A barra composta com as definições acima:

![Screenshot da barra composta.](./media/workbooks-composite-bar/composite-bar.png)

## <a name="composite-bar-settings"></a>Configurações de barra composta

Selecione o nome da coluna e a cor correspondente para tornar essa coluna nessa cor como parte da barra composta. Pode inserir, apagar e mover filas para cima e para baixo.

### <a name="label"></a>Etiqueta

A etiqueta da barra composta é apresentada na parte superior da barra composta. Pode utilizar uma mistura de texto estático, colunas e parâmetro.  Se a etiqueta estiver vazia, o valor das colunas atuais é apresentado como o rótulo. No exemplo anterior, se deixarmos o campo de etiquetas preto, o valor das colunas totais seria apresentado.

Consulte as colunas com `["columnName"]` .

Consulte os parâmetros com `{paramName}` .

Tanto o nome da coluna como o nome do parâmetro são sensíveis ao caso. Também pode fazer das etiquetas um link selecionando "Faça este item como um link" e, em seguida, adicione definições de ligação.

### <a name="aggregation"></a>Agregação

As agregações são úteis para a árvore/grupo por visualizações. Os dados de uma coluna para a linha de grupo são decididos pelo conjunto de agregação para essa coluna. Existem três tipos de agregações aplicáveis às barras compostas: Nenhuma, Soma e Herdade.

Para adicionar definições de Grupo Por:

1. Nas definições de colunas, vá à coluna a que pretende adicionar definições.
2. Nas *definições de árvore/grupo por definições* sob *o tipo de árvore,* selecione Grupo **Por**
3. Selecione o campo pelo quais gostaria de agrupar.

![Screenshot do grupo por definições.](./media/workbooks-composite-bar/group-by-settings.png)

#### <a name="none"></a>Nenhum

Nenhuma agregação significa não apresentar resultados para a coluna para as linhas de grupo.

![Screenshot da barra composta sem agregação.](./media/workbooks-composite-bar/none.png)

#### <a name="sum"></a>Soma

Se a agregação for definida como Soma, então a coluna na linha de grupo mostrará a barra composta utilizando a soma das colunas utilizadas para a renderizar. A etiqueta utilizará igualmente a soma das colunas nele referidas.

No exemplo abaixo, offline e recuperação todos têm a agregação máxima definida para eles e a agregação para a coluna total é soma.

![Screenshot da barra composta com agregação de soma.](./media/workbooks-composite-bar/sum.png)

#### <a name="inherit"></a>Herdar

Se a agregação for definida como herda, então a coluna na linha de grupo mostrará a barra composta utilizando a agregação definida pelos utilizadores para as colunas utilizadas para a renderizar. As colunas utilizadas na etiqueta também utilizam o conjunto de agregação definido pelo utilizador. Se o renderizador de colunas atual for uma barra composta e for referenciado na etiqueta (como "total" no exemplo acima), então a soma é usada como agregação para essa coluna.

No exemplo abaixo, os online, offline e recuperação todos têm a agregação máxima definida para eles e a agregação para a coluna total é herdada.

![Screenshot da barra composta com agregação herdada.](./media/workbooks-composite-bar/inherit.png)

## <a name="sorting"></a>Ordenação

Para visualizações de grelha, a triagem das linhas para a coluna com o renderizador de barras compósito funciona com base no valor que é a soma das colunas utilizadas para tornar o computador de barra compósito dinamicamente. Nos exemplos anteriores, o valor utilizado para a triagem é a soma das colunas online, de recuperação e offline para essa linha em particular.

## <a name="tiles-visualization"></a>Visualização de azulejos

1. **Selecione Adicionar** e *adicionar consulta*.
2. Altere a fonte de dados para JSON introduza os dados a partir do [exemplo anterior](#adding-composite-bar-renderer).
3. Alterar a visualização para *Azulejos*.
4. Fazer consulta.
5. Selecione **Definições de azulejos**.
6. Selecione *Left* in Tile fields.
7. Introduza as definições abaixo nas *Definições de Campo*.
    1. Utilize a coluna: "servidor".
    2. Renderizador de colunas: "Texto".
8. Selecione *Bottom* nos campos de azulejos.
9. Introduza as definições abaixo nas *Definições de Campo*.
    1. Coluna de utilização: "total".
    2. Renderizador de colunas: "Barra composta".
    3. Introduza Definir as seguintes definições em "Configurações de barras compostas".

    | Nome da coluna | Cor        |
    |-------------|--------------|
    | online      | Green        |
    | recuperação  | Amarelo       |
    | offline     | Vermelho (Brilhante) |

    4. Adicionar etiqueta: `["online"] of ["total"] are healthy` .
10. Selecione **Aplicar**.

Definições de barra composta para azulejos:

![Screenshot das definições de azulejos de barra composta com as definições acima descritas.](./media/workbooks-composite-bar/tiles-settings.png)

A vista de bar composta para azulejos com as definições acima será assim:

![Screenshot de azulejos de barra compósito.](./media/workbooks-composite-bar/composite-bar-tiles.png)

## <a name="graphs-visualization"></a>Visualização de gráficos

Para fazer um renderizador de barras compósitos para visualização de gráficos (tipo Hive Clusters), siga as instruções abaixo.

1. **Selecione Adicionar** e *adicionar consulta*.
2. Altere a fonte de dados para JSON introduza os dados a partir do [exemplo anterior](#adding-composite-bar-renderer).
3. Alterar a visualização para *Gráficos*.
4. Fazer consulta.
5. Selecione **Configurações de gráficos**.
6. Selecione *o conteúdo do centro* nas definições do formato do nó.
7. Introduza as definições abaixo nas *Definições de Campo*.
    1. Coluna de utilização: "total".
    2. Renderizador de colunas: "Barra composta".
    3. Introduza as seguintes definições em *Definições de Barra Composta .*

    |Nome da coluna  |     Cor    |
    |-------------|--------------|
    | online      | Green        |
    | recuperação  | Amarelo       |
    | offline     | Vermelho (Brilhante) |

   4. Adicionar etiqueta: `["online"] of ["total"] are healthy` .
9. Introduza as definições abaixo nas *Definições de Layout*.
    1. Tipo de gráfico: **Aglomerados de Colmeias**.
    2. Seletor de identificação de nó: "servidor".
    3. Grupo por Campo: "Nenhum".
    4. Tamanho do nó: 100.
    5. Margem entre hexágonos: 5.
    6. Tipo de coloração: **Nenhum**.
1. Selecione **Aplicar**.
    
Definições de barras compostas para gráficos:

![Screenshot das definições de gráfico de barras compósitas com as definições acima descritas.](./media/workbooks-composite-bar/graphs-settings.png)

A vista de barras composta para gráfico com as definições acima será assim:

![Screenshot de gráficos de barras compósitas com aglomerados de colmeia.](./media/workbooks-composite-bar/composite-bar-graphs.png)

## <a name="next-steps"></a>Passos seguintes

* [Implementar](../visualize/workbooks-automate.md) livros de trabalho com o Gestor de Recursos Azure.
* [Controle](./workbooks-access-control.md) e partilhe o acesso aos seus recursos do livro.