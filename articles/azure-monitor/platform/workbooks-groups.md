---
title: Grupos de livros de trabalho do monitor Azure
description: Como utilizar grupos em livros de trabalho do Monitor Azure.
services: azure-monitor
author: lgayhardt
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 05/06/2020
ms.author: lagayhar
ms.openlocfilehash: b6377cdcdb5816426eba62fdbef79eeb42659dcc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "82892085"
---
# <a name="how-to-use-groups-in-workbooks"></a>Como usar grupos em livros

Um item de grupo num livro permite-lhe, logicamente, agrupar um conjunto de passos num livro.

Grupos em livros são úteis para várias coisas:

- Layout
  - Em cenários em que pretende que os itens sejam organizados verticalmente, pode criar um grupo de itens que se empilharão e definirá o estilo do grupo como uma largura percentual em vez de definir a largura percentual em todos os itens individuais.
- Visibilidade
  - Em cenários em que pretende que muitos itens se escondam/mostrem juntos, pode definir a visibilidade de todo o grupo de itens, em vez de definir definições de visibilidade em cada item. Isto pode ser útil em modelos que usam separadores, pois pode usar um grupo como o conteúdo do separador, e todo o grupo pode ser escondido/mostrado com base num parâmetro definido pelo separador selecionado.
- Desempenho
  - Nos casos em que você tem um modelo muito grande com muitas secções ou separadores, você pode converter cada seção em seu próprio sub-modelo e usar grupos para carregar todos os sub-modelos dentro do modelo de nível superior. O conteúdo dos sub-modelos não será carregado ou executado até que um utilizador torne esses grupos visíveis. Saiba mais sobre [como dividir um grande modelo em muitos modelos.](#how-to-split-a-large-template-into-many-templates)

## <a name="using-groups"></a>Usando grupos

Para adicionar um grupo ao seu livro, *selecione Adicionar* e adicione *o grupo*.

![Selecione adicionar em seguida grupo.](./media/workbooks-groups/add-group.png)

Abaixo está uma imagem de um grupo em modo de leitura com dois itens no interior: um item de texto e um item de consulta.  

![Grupo em modo de leitura.](./media/workbooks-groups/groups-view.png)

Ao editar o livro, pode ver que estes dois itens estão dentro de um item de grupo:

![Grupo no modo de edição. ](./media/workbooks-groups/groups-edit.png)

Na imagem acima, o grupo encontra-se em modo de edição, mostrando que contém dois itens (dentro da área tracejada). Cada passo pode ser em modo de edição ou leitura, independente uns dos outros. Por exemplo, o passo de texto está no modo de edição enquanto o passo de consulta está no modo de leitura.

## <a name="scoping"></a>Escoar

Atualmente, um grupo é tratado como um novo âmbito no livro. Quaisquer parâmetros criados no grupo só são visíveis dentro do grupo. Isto também é verdade para a fusão, você só pode ver dados dentro do seu grupo ou ao nível dos pais.

## <a name="group-types"></a>Tipos de grupo

O item do grupo do livro permite-lhe adicionar um grupo de itens a um livro. Como autor de um livro, especifica qual o tipo de grupo que será. Existem dois tipos de grupos:

- Editável
  - O grupo no livro permite-lhe adicionar, remover ou editar o conteúdo dos itens do grupo. Isto é mais comumente usado para fins de layout e visibilidade.
- Do modelo
  - O grupo no livro carrega do conteúdo de outro modelo pelo seu ID. O conteúdo desse modelo é carregado e fundido no livro de trabalho em tempo de execução. No modo de edição, não é possível modificar nenhum dos conteúdos do grupo, uma vez que apenas carregarão novamente a partir do modelo da próxima vez que o artigo for carregado.  

## <a name="load-types"></a>Tipos de carga

Há algumas maneiras diferentes de que o conteúdo de um grupo pode ser carregado. Como autor de um livro, pode especificar quando e como o conteúdo de um grupo será carregado.

### <a name="lazy-the-default"></a>Preguiçoso (o padrão)

O grupo só carregará quando o artigo estiver visível. Isto permite que um grupo seja utilizado por itens de separador. Se o separador nunca for selecionado, o grupo nunca fica visível, pelo que o conteúdo não é carregado.

Para grupos criados a partir de um modelo, o conteúdo do modelo não é recuperado e os itens do grupo não são criados até que o grupo se torne visível. O utilizador verá os spinners de progresso para todo o grupo enquanto o conteúdo é recuperado.

### <a name="explicit"></a>Explícito

Neste modo é apresentado um botão onde o grupo estaria, e nenhum conteúdo é recuperado ou criado até que o utilizador clique explicitamente no botão para carregar o conteúdo. Isto é útil em cenários em que o conteúdo pode ser caro para calcular ou raramente usado. O autor pode especificar o texto para aparecer no botão.

Abaixo está uma imagem de definições de carga explícitas mostrando um botão configurado "Carregar mais".

![Definições explícitas de carga](./media/workbooks-groups/groups-explicitly-loaded.png)

O grupo antes de ser carregado no livro:

![Grupo explícito antes de ser carregado no livro](./media/workbooks-groups/groups-explicitly-loaded-before.png)

O grupo depois de ser carregado no livro:

![Grupo explícito depois de ser carregado em um livro](./media/workbooks-groups/groups-explicitly-loaded-after.png)

### <a name="always"></a>Sempre

Neste modo, o conteúdo do grupo é sempre carregado e criado assim que o livro de trabalho estiver carregado. Isto é mais utilizado quando se utiliza um grupo apenas para fins de layout, onde o conteúdo será sempre visível.

## <a name="using-templates-inside-a-group"></a>Usando modelos dentro de um grupo

Quando um grupo é configurado para carregar a partir de um modelo, esse conteúdo será carregado preguiçoso por padrão, ele só será carregado quando o grupo estiver visível.

Quando um modelo é carregado em um grupo, o livro tenta fundir quaisquer parâmetros declarados no modelo sendo carregados com parâmetros já existentes no grupo. Quaisquer parâmetros que já existam no livro com nomes idênticos serão fundidos fora do modelo que está a ser carregado. Se todos os parâmetros de um passo de parâmetro forem fundidos, todo o passo dos parâmetros desaparecerá.

### <a name="example-1-all-parameters-have-identical-names"></a>Exemplo 1: todos os parâmetros têm nomes idênticos

Considere um modelo que tem dois parâmetros no topo.

- `TimeRange` - um parâmetro de intervalo de tempo.
- `Filter` - um parâmetro de texto.

![Item dos parâmetros de edição: "parâmetros de nível superior"](./media/workbooks-groups/groups-top-level-params.png)

Em seguida, um item de grupo carrega um segundo modelo que tem os seus próprios dois parâmetros e um passo de texto, onde os parâmetros são nomeados os mesmos:

![Edição de um item de parâmetro para segundo modelo](./media/workbooks-groups/groups-merged-away.png)

Quando o segundo modelo for carregado no grupo, os parâmetros duplicados serão fundidos. Uma vez que todos os parâmetros são fundidos, o passo dos parâmetros internos também é fundido, resultando no grupo que contém apenas o passo de texto.

### <a name="example-2-one-parameter-has-an-identical-name"></a>Exemplo 2: um parâmetro tem um nome idêntico

Considere o modelo de um grupo que tem dois parâmetros no topo.

- `TimeRange` - um parâmetro de intervalo de tempo.
- `FilterB` - um parâmetro de texto, note que não é `Filter` como o modelo superior tinha.

![Edição de um item de grupo com o resultado de parâmetros fundidos](./media/workbooks-groups/groups-wont-merge-away.png)

Quando o modelo do item do grupo estiver carregado, o `TimeRange` parâmetro será fundido fora do grupo. Em seguida, o livro terá os parâmetros iniciais passo com `TimeRange` e , e o passo do parâmetro do grupo só `Filter` incluirá `FilterB`

![resultado de parâmetros que não se fundirão](./media/workbooks-groups/groups-wont-merge-away-result.png)

Se o modelo carregado tivesse contido `TimeRange` e `Filter` (em vez `FilterB` de), então o livro resultante teria um passo de parâmetros e um grupo com apenas o passo de texto restante.

## <a name="how-to-split-a-large-template-into-many-templates"></a>Como dividir um grande modelo em muitos modelos

Para melhorar o desempenho, é benéfico dividir um grande modelo em vários modelos menores que carregam algum conteúdo preguiçoso ou a pedido do utilizador. Isto torna a carga inicial mais rápida, uma vez que o modelo de nível superior pode ser muito menor.

Ao dividir um modelo em partes, você precisará efetivamente dividir o modelo em muitos modelos (sub-modelos) que todos funcionam individualmente. Assim, se o modelo de nível superior tem um `TimeRange` parâmetro que outros passos usam, o sub-modelo também terá de ter um passo de parâmetros que define um parâmetro com o nome exato. Isto permite que os sub-modelos funcionem de forma independente e permite-lhes carregar dentro de modelos maiores em grupos.

Para transformar um modelo maior em vários sub-modelos:

1.  Crie um novo grupo vazio perto do topo do livro, após os parâmetros partilhados. Este novo grupo acabará por se tornar um sub-modelo.
2. Crie uma cópia do passo dos parâmetros partilhados e, em seguida, use *mover-se em grupo* para mover a cópia para o grupo criado no passo 1. Este passo de parâmetros permitirá que o sub-modelo funcione independentemente do modelo exterior, e será fundido quando carregado dentro do modelo exterior.
    > [!NOTE]
    > O sub-modelo não precisa tecnicamente de ter estes parâmetros que se fundem se nunca pretende que os sub-modelos sejam visíveis por si mesmos. No entanto, será muito difícil editá-los ou depurar se precisar de o fazer mais tarde.

3. Mova cada item no livro que pretende estar no sub-modelo para o grupo criado no passo 1.
4. Se os passos individuais movidos no passo 3 tivessem visibilidades condicionais, isso tornar-se-á a visibilidade do grupo exterior (como usado em separadores). Retire-os dos itens dentro do grupo e adicione a definição de visibilidade ao próprio grupo. Guarde aqui para evitar perder alterações e/ou exportar e guarde uma cópia do conteúdo do json.
5. Se quiser que esse grupo seja carregado a partir de um modelo, pode utilizar o botão da barra de ferramentas *Editar* no grupo. Isto abrirá apenas o conteúdo desse grupo como um livro numa nova janela. Em seguida, pode guardá-lo conforme apropriado e fechar esta vista do livro (não feche o navegador, apenas essa vista para voltar ao livro anterior que estava a editar).
6. Em seguida, pode alterar o passo de grupo para carregar a partir do modelo e definir o campo de identificação do modelo para o livro/modelo que criou no passo 5. Para trabalhar com os IDs dos livros, a fonte precisa de ser um ID de recursos de um livro partilhado. Prima *a Carga* e o conteúdo desse grupo serão agora carregados a partir desse sub-modelo em vez de serem guardados dentro deste livro exterior.

## <a name="next-steps"></a>Passos seguintes
- [Visão geral dos livros](workbooks-overview.md)
- [Utilização de JSONPath com livros de trabalho](workbooks-jsonpath.md)