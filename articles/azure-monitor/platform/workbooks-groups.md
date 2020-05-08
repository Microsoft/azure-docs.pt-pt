---
title: Grupos de livros de monitorização azure
description: Como utilizar grupos em Livros de Trabalho do Monitor Azure.
services: azure-monitor
author: lgayhardt
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 05/06/2020
ms.author: lagayhar
ms.openlocfilehash: b6377cdcdb5816426eba62fdbef79eeb42659dcc
ms.sourcegitcommit: b396c674aa8f66597fa2dd6d6ed200dd7f409915
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/07/2020
ms.locfileid: "82892085"
---
# <a name="how-to-use-groups-in-workbooks"></a>Como usar grupos em livros de trabalho

Um item de grupo num livro permite-lhe, logicamente, agrupar um conjunto de passos num livro.

Grupos em livros de trabalho são úteis para várias coisas:

- Esquema
  - Nos cenários em que pretende que os itens sejam organizados verticalmente, pode criar um conjunto de itens que irão acumular e definir o estilo do grupo para ser uma largura percentual em vez de definir a largura percentual em todos os itens individuais.
- Visibilidade
  - Em cenários em que pretende que muitos itens se escondam/mostrem juntos, pode definir a visibilidade de todo o grupo de itens, em vez de definir definições de visibilidade em cada item individual. Isto pode ser útil em modelos que usam separadores, pois pode usar um grupo como o conteúdo do separador, e todo o grupo pode ser escondido/mostrado com base num parâmetro definido pelo separador selecionado.
- Desempenho
  - Nos casos em que você tem um modelo muito grande com muitas secções ou separadores, você pode converter cada secção em seu próprio sub-modelo e usar grupos para carregar todos os sub-modelos dentro do modelo de nível superior. O conteúdo dos sub-modelos não carregará ou funcionará até que um utilizador torne esses grupos visíveis. Saiba mais sobre [como dividir um grande modelo em muitos modelos.](#how-to-split-a-large-template-into-many-templates)

## <a name="using-groups"></a>Usando grupos

Para adicionar um grupo ao seu livro de trabalho, selecione *Adicionar* em seguida *Adicionar grupo*.

![Selecione adicionar e depois grupo.](./media/workbooks-groups/add-group.png)

Abaixo está uma imagem de um grupo em modo de leitura com dois itens dentro: um item de texto e um item de consulta.  

![Grupo em modo de leitura.](./media/workbooks-groups/groups-view.png)

Ao editar o livro, pode ver que esses dois itens estão realmente dentro de um item de grupo:

![Grupo em modo de edição. ](./media/workbooks-groups/groups-edit.png)

Na imagem acima, o grupo encontra-se em modo de edição, mostrando que contém dois itens (dentro da área tracejada). Cada passo pode estar em modo de edição ou leitura, independentemente uns dos outros. Por exemplo, o passo de texto está no modo de edição enquanto o passo de consulta está no modo de leitura.

## <a name="scoping"></a>Scoping

Atualmente, um grupo é tratado como um novo âmbito no livro. Quaisquer parâmetros criados no grupo só são visíveis dentro do grupo. Isto também é verdade para a fusão, só é possível ver dados dentro do seu grupo ou ao nível dos pais.

## <a name="group-types"></a>Tipos de grupo

O item do grupo de livros permite-lhe adicionar um conjunto de itens a um livro. Como autor de um livro, especifica qual será o tipo de grupo. Existem dois tipos de grupos:

- Editável
  - O grupo no livro permite-lhe adicionar, remover ou editar o conteúdo dos itens do grupo. Isto é mais comumente usado para fins de layout e visibilidade.
- A partir do modelo
  - O grupo no livro carrega do conteúdo de outro modelo pelo seu ID. O conteúdo desse modelo é carregado e fundido no livro em tempo de funcionar. No modo de edição, não é possível modificar nenhum dos conteúdos do grupo, uma vez que eles apenas carregarão novamente a partir do modelo da próxima vez que o item carregar.  

## <a name="load-types"></a>Tipos de carga

Há algumas maneiras diferentes de o conteúdo de um grupo poder ser carregado. Como autor de um livro, pode especificar quando e como o conteúdo de um grupo irá carregar.

### <a name="lazy-the-default"></a>Preguiçoso (o padrão)

O grupo só carregará quando o item estiver visível. Isto permite que um grupo seja utilizado por itens de separador. Se o separador nunca for selecionado, o grupo nunca se torna visível, pelo que o conteúdo não é carregado.

Para grupos criados a partir de um modelo, o conteúdo do modelo não é recuperado e os itens do grupo não são criados até que o grupo se torne visível. O utilizador verá os giradores de progresso para todo o grupo enquanto o conteúdo é recuperado.

### <a name="explicit"></a>Explícito

Neste modo é apresentado um botão onde o grupo estaria, e nenhum conteúdo é recuperado ou criado até que o utilizador clique explicitamente no botão para carregar o conteúdo. Isto é útil em cenários em que o conteúdo pode ser caro para calcular ou raramente usado. O autor pode especificar o texto a aparecer no botão.

Abaixo está uma imagem de definições explícitas de carga mostrando um botão configurado "Carregar mais".

![Definições explícitas de carga](./media/workbooks-groups/groups-explicitly-loaded.png)

O grupo antes de ser carregado no livro:

![Grupo explícito antes de ser carregado no livro](./media/workbooks-groups/groups-explicitly-loaded-before.png)

O grupo depois de ter sido carregado no livro:

![Grupo explícito depois de ser carregado em um livro](./media/workbooks-groups/groups-explicitly-loaded-after.png)

### <a name="always"></a>Sempre

Neste modo, o conteúdo do grupo é sempre carregado e criado assim que o livro carrega. Isto é mais frequentemente utilizado quando se utiliza um grupo apenas para fins de layout, onde o conteúdo será sempre visível.

## <a name="using-templates-inside-a-group"></a>Usando modelos dentro de um grupo

Quando um grupo estiver configurado para carregar a partir de um modelo, esse conteúdo será carregado preguiçoso por padrão, só irá carregar quando o grupo estiver visível.

Quando um modelo é carregado em um grupo, o livro tenta fundir quaisquer parâmetros declarados no modelo sendo carregado com parâmetros já existentes no grupo. Quaisquer parâmetros que já existam no livro com nomes idênticos serão fundidos fora do modelo que está a ser carregado. Se todos os parâmetros num passo de parâmetro forem fundidos, todo o passo dos parâmetros desaparecerá.

### <a name="example-1-all-parameters-have-identical-names"></a>Exemplo 1: todos os parâmetros têm nomes idênticos

Considere um modelo que tem dois parâmetros no topo.

- `TimeRange`- um parâmetro de intervalo de tempo.
- `Filter`- um parâmetro de texto.

![Item dos parâmetros de edição: "parâmetros de nível superior"](./media/workbooks-groups/groups-top-level-params.png)

Em seguida, um item de grupo carrega um segundo modelo que tem os seus próprios dois parâmetros e um passo de texto, onde os parâmetros são nomeados o mesmo:

![Editar um item de parâmetro para o segundo modelo](./media/workbooks-groups/groups-merged-away.png)

Quando o segundo modelo for carregado no grupo, os parâmetros duplicados serão fundidos. Uma vez que todos os parâmetros são fundidos, o passo dos parâmetros internos também é fundido, resultando no grupo que contém apenas o passo de texto.

### <a name="example-2-one-parameter-has-an-identical-name"></a>Exemplo 2: um parâmetro tem um nome idêntico

Considere o modelo de um grupo que tem dois parâmetros no topo.

- `TimeRange`- um parâmetro de intervalo de tempo.
- `FilterB`- um parâmetro de texto, `Filter` note que não é como o modelo superior tinha.

![Editar um item de grupo com o resultado de parâmetros fundidos](./media/workbooks-groups/groups-wont-merge-away.png)

Quando o modelo do grupo estiver carregado, `TimeRange` o parâmetro será fundido fora do grupo. Em seguida, o livro terá os `TimeRange` `Filter`parâmetros iniciais passo com e , e o passo parâmetro do grupo só incluirá`FilterB`

![resultado de parâmetros que não se vão fundir](./media/workbooks-groups/groups-wont-merge-away-result.png)

Se o modelo carregado `TimeRange` `Filter` tivesse contido `FilterB`e (em vez de), então o livro resultante teria um passo de parâmetros e um grupo com apenas o passo de texto restante.

## <a name="how-to-split-a-large-template-into-many-templates"></a>Como dividir um grande modelo em muitos modelos

Para melhorar o desempenho, é benéfico dividir um modelo grande em vários modelos menores que carrega algum conteúdo preguiçoso ou a pedido do utilizador. Isto torna a carga inicial mais rápida, uma vez que o modelo de nível superior pode ser muito menor.

Ao dividir um modelo em partes, terá efetivamente de dividir o modelo em muitos modelos (sub-modelos) que todos funcionam individualmente. Assim, se o modelo de `TimeRange` nível superior tiver um parâmetro que outros passos usam, o sub-modelo também terá de ter um passo de parâmetros que define um parâmetro com o nome exato. Isto permite que os sub-modelos funcionem de forma independente e permite-lhes carregar dentro de modelos maiores em grupos.

Para transformar um modelo maior em vários sub-modelos:

1.  Crie um novo grupo vazio perto do topo do livro, após os parâmetros partilhados. Este novo grupo acabará por se tornar um sub-modelo.
2. Crie uma cópia do passo dos parâmetros partilhados e, em seguida, use *mover-se para* o grupo para mover a cópia para o grupo criado no passo 1. Este passo de parâmetros permitirá que o sub-modelo funcione independentemente do modelo exterior, e será fundido quando carregado dentro do modelo exterior.
    > [!NOTE]
    > O sub-modelo não precisa tecnicamente de ter estes parâmetros que são fundidos se nunca pretender que os sub-modelos sejam visíveis por si mesmos. No entanto, vai torná-los muito difíceis de editar ou depurar se precisar fazê-lo mais tarde.

3. Mova cada item no livro que pretende estar no sub-modelo para o grupo criado no passo 1.
4. Se os passos individuais movidos no passo 3 tivessem visibilidadecondicional, isso tornar-se-á a visibilidade do grupo exterior (como usado em separadores). Retire-os dos itens dentro do grupo e adicione essa definição de visibilidade ao próprio grupo. Guarde aqui para evitar perder alterações e/ou exportar e guarde uma cópia do conteúdo json.
5. Se quiser que esse grupo seja carregado a partir de um modelo, pode utilizar o botão de barra de *ferramentas Editar* no grupo. Isto abrirá apenas o conteúdo desse grupo como um livro numa nova janela. Em seguida, pode guardá-lo como apropriado e fechar esta vista de livro (não feche o navegador, apenas essa vista para voltar ao livro anterior que estava a editar).
6. Em seguida, pode alterar o passo de grupo para carregar a partir do modelo e definir o campo de identificação do modelo para o livro/modelo que criou no passo 5. Para trabalhar com iDs de livros, a fonte precisa de ser um ID de recursos de livro partilhado. Pressione *a Carga* e o conteúdo desse grupo será agora carregado a partir desse sub-modelo em vez de guardado dentro deste livro exterior.

## <a name="next-steps"></a>Passos seguintes
- [Visão geral dos livros](workbooks-overview.md)
- [Utilização de JSONPath com livros de trabalho](workbooks-jsonpath.md)