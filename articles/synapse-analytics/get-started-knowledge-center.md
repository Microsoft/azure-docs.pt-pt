---
title: 'Tutorial: Começar a explorar o Centro de Conhecimento sinapse'
description: Neste tutorial, você vai aprender a usar o Centro de Conhecimento sinapse.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.topic: tutorial
ms.date: 09/15/2020
ms.openlocfilehash: c8aaef9c98e1fe44c73d4bd464bb1e52122e7a57
ms.sourcegitcommit: 7374b41bb1469f2e3ef119ffaf735f03f5fad484
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/16/2020
ms.locfileid: "90709679"
---
# <a name="explore-the-synapse-knowledge-center"></a>Explore o Centro de Conhecimento sinapse

Neste tutorial, você vai aprender a usar o Synapse Studio Knowledge Center.

## <a name="getting-to-the-knowledge-center"></a>Chegar ao Centro de Conhecimento

Há duas formas de encontrar o Knowledge Center no Estúdio Synapse:

  1. No hub Home, em links úteis, clique no primeiro link chamado **Knowledge Center**.
  2. Na barra de menus no topo, **clique?** e, em seguida,  **Centro de Conhecimento**.

Escolha qualquer um dos métodos e abra o **Centro de Conhecimento.**

## <a name="overview"></a>Descrição geral

O **Centro de Conhecimento** permite-lhe fazer três coisas:
* **Utilize amostras imediatamente**. Esta opção está otimizada para você ver a análise em ação o mais rápido possível. Se quiser um exemplo rápido de como funciona a Sinapse, escolha esta opção.
* **Amostra disponível do navegador.** Esta opção permite-lhe ligar conjuntos de dados de amostra e adicionar código de amostra sob a forma de scripts, cadernos e oleodutos SQL.
* **Estúdio Tour Synapse**. Esta opção leva-o a uma breve visita às partes básicas do Synapse Studio. Isto é útil se nunca usou o Synapse Studio antes.

## <a name="exploring-blob-storage-with-sql-on-demand"></a>Explorando o armazenamento de bolhas com SQL a pedido

1. O **Centro de Conhecimento,** clique em **Utilizar amostras imediatamente**
1. **Selecione dados de consulta com SQL** 
1. Clique **em Utilizar amostras imediatamente**
1. Criará um novo script SQL.
1. Percorra a primeira consulta (linhas 28 a 32) e selecione o texto de consulta
1. Clique em Run (Executar). Irá executar o texto que selecionou.

## <a name="loading-the-more-nyc-taxi-data"></a>Carregar mais dados de táxi de NYC

1. O **Knowledge Center,** clique em **procurar amostras disponíveis** 
1. Selecione o separador **de scripts SQL** no topo
1. Selecione **Carregar o conjunto de dados do Táxi de Nova Iorque**
1. Em **Entradas**, escolha **selecionar uma piscina existente** e selecione **SQLDB1**
1. Clique **em escrever aberto**
1. Aparecerá um novo script SQL.
1. Clique **em Executar**
1. Isto criará várias tabelas para todos os dados do Táxi nyc e carregá-los-á usando o comando T-SQL COPY.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Analise usando uma piscina SQL](get-started-analyze-sql-pool.md)
