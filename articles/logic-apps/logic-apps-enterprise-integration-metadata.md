---
title: Gerir metadados de artefactos de conta de integração - Azure Logic Apps | Documentos da Microsoft
description: Adicionar ou obter metadados de artefactos de contas de integração no Azure Logic Apps com o Enterprise Integration Pack
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.assetid: bb7d9432-b697-44db-aa88-bd16ddfad23f
ms.date: 01/17/2019
ms.openlocfilehash: 5ebdf45bec4e7cfceb75354af40c7a21c22c6eef
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60846206"
---
# <a name="manage-artifact-metadata-in-integration-accounts-with-azure-logic-apps-and-enterprise-integration-pack"></a>Gerir metadados de artefactos nas contas de integração com o Azure Logic Apps e o Enterprise Integration Pack

Pode definir metadados personalizados para artefactos em contas de integração e obtenha esses metadados durante o tempo de execução para a sua aplicação lógica utilizar. Por exemplo, pode fornecer metadados para artefactos, tais como parceiros, contratos, esquemas e mapas - todos os metadados de armazenamento através de pares chave-valor. 

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição, <a href="https://azure.microsoft.com/free/" target="_blank">inscreva-se numa conta do Azure gratuita</a>.

* Básica [conta de integração](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) que tem os artefactos em que pretende adicionar metadados, por exemplo: 

  * [Parceiro](logic-apps-enterprise-integration-partners.md)
  * [Contrato](logic-apps-enterprise-integration-agreements.md)
  * [Schema](logic-apps-enterprise-integration-schemas.md)
  * [Mapa](logic-apps-enterprise-integration-maps.md)

* Uma aplicação lógica que está ligada aos metadados de Federação e de conta de integração que pretende utilizar. Se já não está associada a sua aplicação lógica, saiba [como ligar aplicações lógicas para contas de integração](logic-apps-enterprise-integration-create-integration-account.md#link-account). 

  Se ainda não tiver uma aplicação lógica, saiba [como criar aplicações lógicas](../logic-apps/quickstart-create-first-logic-app-workflow.md). 
  Adicione o acionador e ações que pretende utilizar para o gerenciamento de metadados de artefactos. Ou, simplesmente, experimentar coisas sem, adicionar um acionador, tal como **pedir** ou **HTTP** à sua aplicação lógica.

## <a name="add-metadata-to-artifacts"></a>Adicionar metadados para artefactos

1. Inicie sessão no <a href="https://portal.azure.com" target="_blank">portal do Azure</a> com as credenciais da sua conta do Azure. Localize e abra a sua conta de integração.

1. Selecione o artefacto para adicionar metadados e escolha onde pretende **editar**. Introduza os detalhes de metadados para esse artefato, por exemplo:

   ![Introduzir metadados](media/logic-apps-enterprise-integration-metadata/add-partner-metadata.png)

1. Quando tiver terminado, escolha **OK**.

1. Para exibir esses metadados na definição do JavaScript Object Notation (JSON) para a conta de integração, escolha **editar como JSON** , de modo a que abre o editor de JSON: 

   ![JSON para os metadados de parceiro](media/logic-apps-enterprise-integration-metadata/partner-metadata.png)

## <a name="get-artifact-metadata"></a>Obter metadados de artefactos

1. No portal do Azure, abra a aplicação de lógica que está ligada à conta de integração que pretende. 

1. No Estruturador da aplicação lógica, se estiver a adicionar o passo para obter metadados sob o acionador ou a última ação no fluxo de trabalho, escolha **novo passo** > **adicionar uma ação**. 

1. Na caixa de pesquisa, introduza "conta de integração". Na caixa de pesquisa, escolha **todos os**. Na lista de ações, selecione a ação: **Pesquisa de artefacto de conta de integração - conta de integração**

   ![Selecione "Pesquisa de artefacto de conta de integração"](media/logic-apps-enterprise-integration-metadata/integration-account-artifact-lookup.png)

1. Forneça estas informações para o artefacto que pretende localizar:

   | Propriedade | Necessário | Value | Descrição | 
   |----------|---------|-------|-------------| 
   | **Tipo de artefacto** | Sim | **Esquema**, **mapa**, **parceiro**, **contrato**, ou um tipo personalizado | O tipo de artefacto que desejar | 
   | **Nome do artefacto** | Sim | <*artifact-name*> | O nome para o artefacto que pretende | 
   ||| 

   Por exemplo, suponha que queira obter os metadados para um artefacto de parceiro comercial:

   ![Selecione o tipo de artefacto e introduza o nome do artefacto](media/logic-apps-enterprise-integration-metadata/artifact-lookup-information.png)

1. Adicione a ação que pretende para lidar com esses metadados, por exemplo:

   1. Sob o **pesquisa de artefacto da conta de integração** ação, escolha **passo seguinte**e selecione **adicionar uma ação**. 

   1. Na caixa de pesquisa, introduza "http". Na caixa de pesquisa, escolha **Built-ins**e selecione a ação: **HTTP - HTTP**

      ![Adicionar ação de HTTP](media/logic-apps-enterprise-integration-metadata/http-action.png)

   1. Forneça informações para os metadados de artefactos que pretende gerir. 

      Por exemplo, suponha que queira obter o `routingUrl` metadados que é adicionado no início deste tópico. Seguem-se os valores de propriedade, que poderia especificar: 

      | Propriedade | Necessário | Value | Descrição | 
      |----------|----------|-------|-------------| 
      | **Método** | Sim | <*operation-to-run*> | A operação de HTTP para executar no artefacto. Por exemplo, esta ação de HTTP utiliza a **obter** método. | 
      | **URI** | Sim | <*metadata-location*> | Para o acesso a `routingUrl` valor de metadados de artefacto que recuperados, pode usar uma expressão, por exemplo: <p>`@{outputs('Integration_Account_Artifact_Lookup')['properties']['metadata']['routingUrl']}` | 
      | **Headers** (Cabeçalhos) | Não | <*header-values*> | Qualquer cabeçalho de saídas de Acionador que pretende passar para a ação de HTTP. Por exemplo, para passar o acionador `headers` valor da propriedade: pode usar uma expressão, por exemplo: <p>`@triggeroutputs()['headers']` | 
      | **Corpo** | Não | <*body-content*> | Qualquer outro conteúdo que pretende passar a ação de HTTP `body` propriedade. Neste exemplo, passa o artefacto `properties` valores para a ação de HTTP: <p>1. Clique no interior da **corpo** propriedade para que a lista de conteúdo dinâmico apareça. Se não existem propriedades são apresentados, escolha **ver mais**. <br>2. Na lista de conteúdo dinâmica, sob **pesquisa de artefacto da conta de integração**, selecione **propriedades**. | 
      |||| 

      Por exemplo:

      ![Especifique valores e expressões para a ação de HTTP](media/logic-apps-enterprise-integration-metadata/add-http-action-values.png)

   1. Para verificar as informações fornecidas para a ação de HTTP, ver a definição de JSON da sua aplicação lógica. Na barra de ferramentas da Estruturador da aplicação lógica, escolha **exibição de código** para que a definição de JSON da aplicação é apresentada, por exemplo:

      ![Definição JSON da aplicação lógica](media/logic-apps-enterprise-integration-metadata/finished-logic-app-definition.png)

      Depois de voltar a mudar para o Estruturador da aplicação lógica, qualquer utilizou as expressões aparecem agora resolvidas, por exemplo:

      ![Expressões resolvidas no Estruturador da aplicação lógica](media/logic-apps-enterprise-integration-metadata/resolved-expressions.png)

## <a name="next-steps"></a>Passos Seguintes

* [Saiba mais sobre os contratos](logic-apps-enterprise-integration-agreements.md)
