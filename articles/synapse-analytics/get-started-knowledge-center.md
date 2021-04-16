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
ms.date: 04/04/2021
ms.openlocfilehash: f4cc631bd3ff05dc63566677ec96ef0360d362c9
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/15/2021
ms.locfileid: "107517356"
---
# <a name="explore-the-synapse-knowledge-center"></a>Explore o Centro de Conhecimento sinapse

Neste tutorial, você vai aprender a usar o Synapse Studio Knowledge Center.

## <a name="finding-to-the-knowledge-center"></a>Encontrar para o Centro de Conhecimento

Há duas formas de encontrar o Knowledge Center no Estúdio Synapse:

  1. No centro home, perto do topo-direito da página clique em **Learn**.
  2. Na barra de menus no topo, **clique?** e, em seguida, **Centro de Conhecimento**.

Escolha qualquer um dos métodos e abra o **Centro de Conhecimento.**

## <a name="exploring-the-knowledge-center"></a>Exploração do Centro de Conhecimento

Uma vez visível, verá que o Centro de **Conhecimento** lhe permite fazer três coisas:
* **Utilize amostras imediatamente**. Se quiser um exemplo rápido de como funciona a Sinapse, escolha esta opção.
* **Procurar na galeria.** Esta opção permite-lhe ligar conjuntos de dados de amostra e adicionar código de amostra sob a forma de scripts, cadernos e oleodutos SQL.
* **Tour Synapse Studio**. Esta opção leva-o a uma breve visita às partes básicas do Synapse Studio. Isto é útil se nunca usou o Synapse Studio antes.

## <a name="use-samples-immediately-three-samples-to-help-you-get-started-fast"></a>Use amostras imediatamente: Três amostras para ajudá-lo a começar rapidamente

Há três itens nesta secção:
* Explore os dados da amostra com a Spark
* Dados de consulta com SQL
* Criar tabela externa com SQL

1. No **centro de conhecimento,** clique em **Utilizar imediatamente as amostras.**
1. Selecione **dados de consulta com SQL**.
1. Clique **na amostra de utilização.**
1. Um novo script SQL de amostra será aberto.
1. Percorra a primeira consulta (linhas 28 a 32) e selecione o texto de consulta.
1. Clique em Run (Executar). Só irá executar o código que selecionou.

## <a name="gallery-a-collectiopn-of-sample-data-sets-and-sample-code"></a>Galeria: Um conjunto de conjuntos de dados de amostra e código de amostra

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

> [!div class="nextstepaction"]
> [Adicionar um administrador](get-started-add-admin.md)

