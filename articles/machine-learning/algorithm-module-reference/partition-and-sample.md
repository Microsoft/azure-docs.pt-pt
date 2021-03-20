---
title: 'Partição e Amostra: Referência do módulo'
titleSuffix: Azure Machine Learning
description: Aprenda a utilizar o módulo de partição e amostra no Azure Machine Learning para realizar amostragem num conjunto de dados ou para criar divisórias a partir do seu conjunto de dados.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/22/2019
ms.openlocfilehash: a1f0a0dff4eb8a1aad0cd5e142959a636827a541
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "90898486"
---
# <a name="partition-and-sample-module"></a>Módulo de partição e amostra

Este artigo descreve um módulo no designer de Aprendizagem automática Azure.

Utilize o módulo Partição e Amostra para efetuar a amostragem num conjunto de dados ou para criar divisórias a partir do seu conjunto de dados.

A amostragem é uma ferramenta importante na aprendizagem automática porque permite reduzir o tamanho de um conjunto de dados mantendo a mesma relação de valores. Este módulo suporta várias tarefas relacionadas que são importantes na aprendizagem automática: 

- Dividindo os seus dados em várias subsecções do mesmo tamanho. 

  Pode usar as divisórias para validação cruzada ou para atribuir casos a grupos aleatórios.

- Separar dados em grupos e, em seguida, trabalhar com dados de um grupo específico. 

  Depois de atribuir aleatoriamente casos a diferentes grupos, poderá ter de modificar as funcionalidades que estão associadas a apenas um grupo.

- A provar. 

  Pode extrair uma percentagem dos dados, aplicar amostragem aleatória ou escolher uma coluna para equilibrar o conjunto de dados e efetuar amostragem estratificada sobre os seus valores.

- Criar um conjunto de dados menor para testes. 

  Se tiver muitos dados, talvez queira utilizar apenas as primeiras linhas *n* durante a configuração do pipeline e, em seguida, mudar para usar o conjunto de dados completo quando construir o seu modelo. Também pode utilizar a amostragem para criar um conjunto de dados menor para utilização no desenvolvimento.

## <a name="configure-the-module"></a>Configure o módulo

Este módulo suporta os seguintes métodos para dividir os seus dados em divisórias ou para amostragem. Escolha primeiro o método e, em seguida, desa estale opções adicionais que o método requer.

- Head
- Amostragem
- Atribuir a dobras
- Escolha dobrar

### <a name="get-top-n-rows-from-a-dataset"></a>Obtenha linhas TOP N a partir de um conjunto de dados

Utilize este modo para obter apenas as primeiras linhas *n.* Esta opção é útil se quiser testar um oleoduto em um pequeno número de linhas, e não precisa que os dados sejam equilibrados ou amostrados de forma alguma.

1. Adicione o módulo **de partição e amostra** ao seu pipeline na interface e ligue o conjunto de dados.  

1. **Modo de partição ou amostra**: Desaje esta opção à **cabeça**.

1. **Número de linhas a selecionar**: Introduza o número de linhas a retornar.

   O número de linhas deve ser um número inteiro não negativo. Se o número de linhas selecionadas for maior do que o número de linhas no conjunto de dados, todo o conjunto de dados é devolvido.

1. Envie o oleoduto.

O módulo produz um único conjunto de dados que contém apenas o número especificado de linhas. As linhas são sempre lidas a partir do topo do conjunto de dados.

### <a name="create-a-sample-of-data"></a>Criar uma amostra de dados

Esta opção suporta uma amostragem aleatória simples ou uma amostragem aleatória estratificada. É útil se pretender criar um conjunto de dados de amostra representativa mais pequeno para testes.

1. Adicione o módulo **de partição e amostra** ao seu oleoduto e ligue o conjunto de dados.

1. **Modo de partição ou amostra**: Desaje esta opção à  **amostragem**.

1. **Taxa de amostragem**: Insira um valor entre 0 e 1. este valor especifica a percentagem de linhas do conjunto de dados de origem que devem ser incluídas no conjunto de dados de saída.

   Por exemplo, se quiser apenas metade do conjunto de dados original, `0.5` insira para indicar que a taxa de amostragem deve ser de 50%.

   As linhas do conjunto de dados de entrada são baralhadas e colocadas seletivamente no conjunto de dados de saída, de acordo com a relação especificada.

1. **Semente aleatória para amostragem**: Opcionalmente, introduza um inteiro para usar como valor de sementes.

   Esta opção é importante se quiser que as linhas sejam sempre divididas da mesma forma. O valor predefinido é **0,** o que significa que uma semente inicial é gerada com base no relógio do sistema. Este valor pode levar a resultados ligeiramente diferentes cada vez que executar o oleoduto.

1. **Divisão estratificada para amostragem**: Selecione esta opção se for importante que as linhas do conjunto de dados sejam divididas uniformemente por alguma coluna-chave antes da amostragem.

   Para **a coluna-chave de estratificação para amostragem,** selecione uma coluna de *estratos* única para utilizar ao dividir o conjunto de dados. As linhas do conjunto de dados são então divididas da seguinte forma:

   1. Todas as linhas de entrada são agrupadas (estratificadas) pelos valores na coluna de estratos especificados.

   1. As filas são baralhadas dentro de cada grupo.

   1. Cada grupo é adicionado seletivamente ao conjunto de dados de saída para cumprir a relação especificada.


1. Envie o oleoduto.

   Com esta opção, o módulo produz um único conjunto de dados que contém uma amostragem representativa dos dados. A parte restante não danificada do conjunto de dados não é de saída. 

## <a name="split-data-into-partitions"></a>Dividir dados em divisórias

Utilize esta opção quando pretender dividir o conjunto de dados em subconjuntos dos dados. Esta opção também é útil quando pretende criar um número personalizado de dobras para validação cruzada, ou dividir linhas em vários grupos.

1. Adicione o módulo **de partição e amostra** ao seu oleoduto e ligue o conjunto de dados.

1. Para **o modo de partição ou amostra,** selecione **Atribua-se a Dobras**.

1. **Utilize a substituição na partição**: Selecione esta opção se desejar que a linha amostrada seja colocada novamente no conjunto de linhas para uma reutilização potencial. Como resultado, a mesma linha pode ser atribuída a várias dobras.

   Se não utilizar a substituição (a opção predefinida), a linha amostrada não é colocada novamente no conjunto de linhas para uma reutilização potencial. Como resultado, cada linha pode ser atribuída a apenas uma dobra.

1. **Divisão aleatória**: Selecione esta opção se pretender que as linhas sejam atribuídas aleatoriamente a dobras.

   Se não selecionar esta opção, as linhas são atribuídas a dobras através do método de rodada-robin.

1. **Semente aleatória**: Opcionalmente, introduza um inteiro para usar como valor de sementes. Esta opção é importante se quiser que as linhas sejam sempre divididas da mesma forma. Caso contrário, o valor predefinido de **0** significa que uma semente inicial aleatória será utilizada.

1. **Especificar o método partitioner**: Indicar como pretende que os dados sejam repartidos a cada partição, utilizando estas opções:

   - **Partição uniforme:** Utilize esta opção para colocar um número igual de linhas em cada divisória. Para especificar o número de divisórias de saída, introduza um número inteiro no **número de dobras para dividir uniformemente em** caixa.

   - **Partição com proporções personalizadas**: Utilize esta opção para especificar o tamanho de cada partição como uma lista separada por vírgula.

     Por exemplo, assuma que quer criar três divisórias. A primeira partição conterá 50% dos dados. As duas divisórias restantes contêm 25% dos dados. Na **Lista de proporções separadas por caixa de vírgula,** insira estes números: **.5, .25, .25**.

     A soma de todos os tamanhos de partição deve somar exatamente 1.

     Se introduzir números que somam *menos de 1*, é criada uma partição extra para manter as restantes linhas. Por exemplo, se introduzir os valores **.2** e **.3,** é criada uma terceira partição para manter os restantes 50% de todas as linhas.
     
     Se introduzir números que somam *mais de 1*, é levantado um erro quando funciona o pipeline.

1. **Divisão estratificada**: Selecione esta opção se quiser que as linhas sejam estratificadas quando divididas e, em seguida, escolha a _coluna de estratos_.

1. Envie o oleoduto.

   Com esta opção, o módulo produz vários conjuntos de dados. Os conjuntos de dados são divididos de acordo com as regras especificadas.

### <a name="use-data-from-a-predefined-partition"></a>Utilize dados de uma partição predefinida  

Utilize esta opção quando tiver dividido um conjunto de dados em múltiplas divisórias e agora pretender carregar cada partição por sua vez para uma análise ou processamento posterior.

1. Adicione o módulo **de partição e amostra** ao oleoduto.

1. Ligue o módulo à saída de uma instância anterior de **Partição e Amostra**. Este caso deve ter usado a opção **Atribuir a Dobras** para gerar um número de divisórias.

1. **Modo de partição ou amostra**: Selecione **Pick Fold**.

1. **Especificar qual a dobra a recolher a partir de:** Selecione uma divisória a utilizar introduzindo o seu índice. Os índices de partição são baseados em 1. Por exemplo, se dividisse o conjunto de dados em três partes, as divisórias teriam os índices 1, 2 e 3.

   Se introduzir um valor de índice inválido, é levantado um erro de tempo de conceção: "Error 0018: Dataset contém dados inválidos."

   Além de agrupar o conjunto de dados por dobras, pode separar o conjunto de dados em dois grupos: uma dobra de destino e tudo o resto. Para isso, introduza o índice de uma única dobra e, em seguida, selecione o **complemento de** escolha da dobra selecionada para obter tudo menos os dados na dobra especificada.

1. Se estiver a trabalhar com várias divisórias, deve adicionar mais instâncias do módulo **de partição e amostra** para manusear cada partição.

   Por exemplo, o módulo **de partição e amostra** na segunda linha está definido para Atribuir a **Dobras**, e o módulo na terceira linha está definido para **Pick Fold**.   

   ![Partição e amostra](./media/module/partition-and-sample.png)

1. Envie o oleoduto.

   Com esta opção, o módulo produz um único conjunto de dados que contém apenas as linhas atribuídas a essa dobra.

> [!NOTE]
>  Não é possível ver as designações dobráveis diretamente. Só estão presentes nos metadados.

## <a name="next-steps"></a>Passos seguintes

Consulte o [conjunto de módulos disponíveis](module-reference.md) para Azure Machine Learning. 