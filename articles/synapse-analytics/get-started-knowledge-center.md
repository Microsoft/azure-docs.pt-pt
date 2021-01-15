---
title: 'Tutorial: Começar a explorar o Centro de Conhecimento sinapse'
description: Neste tutorial, você vai aprender a usar o Centro de Conhecimento sinapse.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: workspace
ms.topic: tutorial
ms.date: 11/16/2020
ms.openlocfilehash: 59364a0a2c1d1e411f41dc35dee812c8e920b72d
ms.sourcegitcommit: f5b8410738bee1381407786fcb9d3d3ab838d813
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/14/2021
ms.locfileid: "98209207"
---
# <a name="explore-the-synapse-knowledge-center"></a>Explore o Centro de Conhecimento sinapse

Neste tutorial, você vai aprender a usar o Synapse Studio Knowledge Center.

## <a name="getting-to-the-knowledge-center"></a>Chegar ao Centro de Conhecimento

Há duas formas de encontrar o Knowledge Center no Estúdio Synapse:

  1. No centro home, perto do topo-direito da página clique em **Learn**.
  2. Na barra de menus no topo, **clique?** e, em seguida, **Centro de Conhecimento**.

Escolha qualquer um dos métodos e abra o **Centro de Conhecimento.**

## <a name="overview"></a>Descrição geral

O **Centro de Conhecimento** permite-lhe fazer três coisas:
* **Utilize amostras imediatamente**. Se quiser um exemplo rápido de como funciona a Sinapse, escolha esta opção.
* **Procurar na galeria.** Esta opção permite-lhe ligar conjuntos de dados de amostra e adicionar código de amostra sob a forma de scripts, cadernos e oleodutos SQL.
* **Tour Synapse Studio**. Esta opção leva-o a uma breve visita às partes básicas do Synapse Studio. Isto é útil se nunca usou o Synapse Studio antes.

## <a name="exploring-blob-storage-with-serverless-sql-pool"></a>Explorando o armazenamento de bolhas com piscina SQL sem servidor

1. Vá ao Centro de **Conhecimento,** clique em **Utilizar imediatamente as amostras.**
1. Selecione **dados de consulta com SQL**.
1. Clique **na amostra de utilização.**
1. Um novo script SQL de amostra será aberto.
1. Percorra a primeira consulta (linhas 28 a 32) e selecione o texto de consulta.
1. Clique em Run (Executar). Só irá executar o código que selecionou.

## <a name="loading-more-nyc-taxi-data"></a>Carregar mais dados de táxi de NYC
1. Vá ao **Knowledge Center,** clique **na galeria Browse.**
1. Selecione o separador **de scripts SQL** na parte superior.
1. Selecione Carregar a amostra **de dados do Conjunto de Dados do Táxi de Nova Iorque,** clique em **Continuar**.
1. No **pool SQL,** escolha **uma piscina existente** e selecione **SQLPOOL1,** e selecione a base de dados **SQLPOOL1** que criou anteriormente.
1. Clique **em Abrir Script**.
1. Um novo script SQL de amostra será aberto.
1. Clique **em Executar**
1. Isto criará várias tabelas para todos os dados do Táxi nyc e carregá-los-á usando o comando T-SQL COPY. Se tivesse criado estas tabelas nos passos de arranque rápido anteriores, selecione e execute apenas código para CRIAR e COPIAR para tabelas que não existem.

    > [!NOTE] 
    > Ao utilizar a galeria de amostras para script SQL com uma piscina SQL dedicada (anteriormente SQL DW), você só poderá usar uma piscina SQL dedicada existente (anteriormente SQL DW).

## <a name="next-steps"></a>Passos seguintes

* [Introdução ao Azure Synapse Analytics](get-started.md)
* [Criar uma área de trabalho](quickstart-create-workspace.md)
* [Utilizar conjunto de SQL sem servidor](quickstart-sql-on-demand.md)
