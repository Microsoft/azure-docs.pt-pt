---
title: 'Partição e Amostra: Referência do módulo'
titleSuffix: Azure Machine Learning
description: Aprenda a utilizar o módulo de partição e amostra em Azure Machine Learning para efetuar amostragem num conjunto de dados ou para criar divisórias a partir do seu conjunto de dados.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/22/2019
ms.openlocfilehash: 0392a05df485b45b1244f4542ed55af1837dca3d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79477515"
---
# <a name="partition-and-sample-module"></a>Módulo de partição e amostra

Este artigo descreve um módulo em Azure Machine Learning designer (pré-visualização).

Utilize o módulo de partilha e amostra para efetuar a amostragem num conjunto de dados ou para criar divisórias a partir do seu conjunto de dados.

A amostragem é uma ferramenta importante no machine learning porque permite reduzir o tamanho de um conjunto de dados mantendo a mesma relação de valores. Este módulo suporta várias tarefas relacionadas que são importantes na aprendizagem automática: 

- Dividir os seus dados em várias subsecções do mesmo tamanho. 

  Pode utilizar as divisórias para validação cruzada ou para atribuir casos a grupos aleatórios.

- Separar os dados em grupos e, em seguida, trabalhar com dados de um grupo específico. 

  Depois de atribuir casos aleatoriamente a diferentes grupos, poderá ter de modificar as funcionalidades que estão associadas aapenas a um grupo.

- A provar. 

  Pode extrair uma percentagem dos dados, aplicar amostras aleatórias ou escolher uma coluna para equilibrar o conjunto de dados e efetuar uma amostragem estratificada dos seus valores.

- Criar um conjunto de dados menor para testes. 

  Se tiver muitos dados, poderá querer utilizar apenas as primeiras linhas *n* durante a configuração do pipeline e, em seguida, mudar para utilizar o conjunto de dados completo quando construir o seu modelo. Também pode utilizar amostras para criar um conjunto de dados menor para uso no desenvolvimento.

## <a name="configure-the-module"></a>Configure o módulo

Este módulo suporta os seguintes métodos de divisão dos seus dados em divisórias ou para amostragem. Escolha primeiro o método e, em seguida, detete as opções adicionais que o método requer.

- Head
- Amostragem
- Atribuir a dobras
- Escolha dobrar

### <a name="get-top-n-rows-from-a-dataset"></a>Obtenha linhas TOP N a partir de um conjunto de dados

Utilize este modo para obter apenas as primeiras linhas *n.* Esta opção é útil se quiser testar um pipeline em um pequeno número de linhas, e você não precisa que os dados sejam equilibrados ou amostrados de forma alguma.

1. Adicione o módulo **de partilha e amostra** ao seu pipeline na interface e ligue o conjunto de dados.  

1. **Modo de partilha ou amostra**: Desloque esta opção para **cabeça**.

1. **Número de linhas para selecionar:** Introduza o número de linhas para devolver.

   O número de linhas deve ser um inteiro não negativo. Se o número de linhas selecionadas for maior do que o número de linhas no conjunto de dados, todo o conjunto de dados é devolvido.

1. Submeta o oleoduto.

O módulo produz um único conjunto de dados que contém apenas o número especificado de linhas. As linhas são sempre lidas a partir do topo do conjunto de dados.

### <a name="create-a-sample-of-data"></a>Criar uma amostra de dados

Esta opção suporta uma simples amostragem aleatória ou uma amostragem aleatória estratificada. É útil se quiser criar um conjunto de dados de amostra seletiva mais pequeno para testes.

1. Adicione o módulo **de partilha e amostra** ao seu pipeline e ligue o conjunto de dados.

1. **Modo de partilha ou amostra:** Desajuste esta opção para **A Amostragem**.

1. **Taxa de amostragem**: Introduza um valor entre 0 e 1. este valor especifica a percentagem de linhas do conjunto de dados de origem que deve ser incluído no conjunto de dados de saída.

   Por exemplo, se quiser apenas metade do `0.5` conjunto de dados original, indique que a taxa de amostragem deve ser de 50%.

   As linhas do conjunto de dados de entrada são baralhadas e seletivamente colocadas no conjunto de dados de saída, de acordo com o rácio especificado.

1. **Sementes aleatórias para amostragem**: Opcionalmente, introduza uma inteiro para usar como valor de semente.

   Esta opção é importante se quiser que as linhas sejam sempre divididas da mesma forma. O valor padrão é **0**, o que significa que uma semente inicial é gerada com base no relógio do sistema. Este valor pode levar a resultados ligeiramente diferentes cada vez que executa o gasoduto.

1. **Divisão estratificada para amostragem**: Selecione esta opção se for importante que as linhas do conjunto de dados sejam divididas uniformemente por alguma coluna de teclas antes de provar.

   Para a **coluna de teclas estratificação para amostragem,** selecione uma única coluna de *estratos* para utilizar ao dividir o conjunto de dados. As linhas do conjunto de dados são então divididas da seguinte forma:

   1. Todas as linhas de entrada são agrupadas (estratificadas) pelos valores na coluna de estratos especificada.

   1. As filas são baralhadas dentro de cada grupo.

   1. Cada grupo é adicionado seletivamente ao conjunto de dados de saída para satisfazer o rácio especificado.


1. Submeta o oleoduto.

   Com esta opção, o módulo produz um único conjunto de dados que contém uma amostra representativa dos dados. A parte restante e não amostrada do conjunto de dados não é de saída. 

## <a name="split-data-into-partitions"></a>Divida dados em divisórias

Utilize esta opção quando pretender dividir o conjunto de dados em subconjuntos dos dados. Esta opção também é útil quando você quer criar um número personalizado de dobras para validação cruzada, ou dividir linhas em vários grupos.

1. Adicione o módulo **de partilha e amostra** ao seu pipeline e ligue o conjunto de dados.

1. Para **o modo de partilha ou amostra,** selecione Atribuir a **dobras**.

1. **Utilize a substituição na divisória**: Selecione esta opção se pretender que a linha amostrada seja reposta na piscina de linhas para potencial reutilização. Como resultado, a mesma linha pode ser atribuída a várias dobras.

   Se não utilizar a substituição (a opção predefinida), a linha amostrada não é colocada de volta na piscina de linhas para potencial reutilização. Como resultado, cada linha pode ser atribuída a apenas uma dobra.

1. **Divisão aleatória**: Selecione esta opção se pretender que as linhas sejam atribuídas aleatoriamente a dobras.

   Se não selecionar esta opção, as linhas são atribuídas a dobras através do método round-robin.

1. **Sementes aleatórias**: Opcionalmente, introduza uma prótese para usar como valor de semente. Esta opção é importante se quiser que as linhas sejam sempre divididas da mesma forma. Caso contrário, o valor padrão de **0** significa que será utilizada uma semente de arranque aleatória.

1. **Especifique o método de partição**: Indique como pretende que os dados sejam distribuídos por cada partição, utilizando estas opções:

   - **Partição uniformemente**: Utilize esta opção para colocar um número igual de linhas em cada partição. Para especificar o número de divisórias de saída, introduza um número inteiro no **número de dobras especificadas para dividir uniformemente em** caixa.

   - **Partição com proporções personalizadas**: Utilize esta opção para especificar o tamanho de cada divisória como uma lista separada da víramida.

     Por exemplo, assuma que quer criar três divisórias. A primeira partição conterá 50% dos dados. As duas divisórias restantes conterão 25% dos dados. Na **lista de proporções separadas por caixa vírposta,** introduza estes números: **.5, .25, .25**.

     A soma de todos os tamanhos das divisórias deve somar exatamente 1.

     Se introduzir números que somam *menos de 1*, é criada uma partição extra para manter as restantes linhas. Por exemplo, se introduzir os valores **.2** e **.3,** é criada uma terceira partição para manter os restantes 50% de todas as linhas.
     
     Se introduzir números que somam mais de *1*, um erro é levantado quando executa o gasoduto.

1. **Divisão estratificada**: Selecione esta opção se quiser que as linhas sejam estratificadas quando divididas e, em seguida, escolha a coluna de _estratos_.

1. Submeta o oleoduto.

   Com esta opção, o módulo produz vários conjuntos de dados. Os conjuntos de dados são divididos de acordo com as regras que especificou.

### <a name="use-data-from-a-predefined-partition"></a>Utilizar dados de uma partição predefinida  

Utilize esta opção quando tiver dividido um conjunto de dados em várias divisórias e agora quer carregar cada partição por sua vez para posterior análise ou processamento.

1. Adicione o módulo **de partição e amostra** ao gasoduto.

1. Ligue o módulo à saída de um caso anterior de **Partição e Amostra**. Esta instância deve ter usado a opção **Atribuir a Folds** para gerar um número de divisórias.

1. **Modo de partição ou amostra**: Selecione **Pick Fold**.

1. **Especifique qual a dobra a amostrar**a partir de : Selecione uma divisória para utilizar introduzindo o seu índice. Os índices de partição são baseados em 1. Por exemplo, se dividisse o conjunto de dados em três partes, as divisórias teriam os índices 1, 2 e 3.

   Se introduzir um valor de índice inválido, é levantado um erro de tempo de design: "Error 0018: Dataset contém dados inválidos."

   Além de agrupar o conjunto de dados por dobras, pode separar o conjunto de dados em dois grupos: uma dobra-alvo, e tudo o resto. Para isso, introduza o índice de uma única dobra e, em seguida, selecione o complemento de opção **Escolha o complemento da dobra selecionada** para obter tudo menos os dados na dobra especificada.

1. Se estiver a trabalhar com várias divisórias, deve adicionar mais instâncias do módulo **de Partilha e Amostra** para lidar com cada partição.

   Por exemplo, o módulo **de partição e amostra** na segunda linha está definido para Atribuir a **Dobras**, e o módulo na terceira linha está definido para **pick Fold**.   

   ![Partição e amostra](./media/module/partition-and-sample.png)

1. Submeta o oleoduto.

   Com esta opção, o módulo produz um único conjunto de dados que contém apenas as linhas atribuídas a essa dobra.

> [!NOTE]
>  Não se pode ver as designações dobráveis diretamente. Só estão presentes nos metadados.

## <a name="next-steps"></a>Passos seguintes

Consulte o [conjunto de módulos disponíveis](module-reference.md) para o Azure Machine Learning. 