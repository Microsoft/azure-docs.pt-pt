---
title: Gerenciar metadados de artefato da conta de integração-aplicativos lógicos do Azure
description: Adicionar ou obter metadados de artefato de contas de integração em aplicativos lógicos do Azure com Enterprise Integration Pack
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.date: 01/17/2019
ms.openlocfilehash: e8e2daf1de9223766c8cec835f7718007a8cf309
ms.sourcegitcommit: d37991ce965b3ee3c4c7f685871f8bae5b56adfa
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/21/2019
ms.locfileid: "72679981"
---
# <a name="manage-artifact-metadata-in-integration-accounts-with-azure-logic-apps-and-enterprise-integration-pack"></a>Gerenciar metadados de artefato em contas de integração com aplicativos lógicos do Azure e Enterprise Integration Pack

Você pode definir metadados personalizados para artefatos em contas de integração e obter os metadados durante o tempo de execução para o aplicativo lógico usar. Por exemplo, você pode fornecer metadados para artefatos, como parceiros, contratos, esquemas e mapas – todos armazenam metadados usando pares chave-valor. 

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição, <a href="https://azure.microsoft.com/free/" target="_blank">inscreva-se numa conta do Azure gratuita</a>.

* Uma [conta de integração](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) básica que tem os artefatos em que você deseja adicionar metadados, por exemplo: 

  * [Parceiro](logic-apps-enterprise-integration-partners.md)
  * [Contrato](logic-apps-enterprise-integration-agreements.md)
  * [Esquema](logic-apps-enterprise-integration-schemas.md)
  * [Mapeada](logic-apps-enterprise-integration-maps.md)

* Um aplicativo lógico vinculado à conta de integração e aos metadados de artefato que você deseja usar. Se seu aplicativo lógico ainda não estiver vinculado, saiba [como vincular aplicativos lógicos a contas de integração](logic-apps-enterprise-integration-create-integration-account.md#link-account). 

  Se você ainda não tiver um aplicativo lógico, saiba [como criar aplicativos lógicos](../logic-apps/quickstart-create-first-logic-app-workflow.md). 
  Adicione o gatilho e as ações que você deseja usar para gerenciar metadados de artefato. Ou, para tentar apenas as coisas, adicione um gatilho como **solicitação** ou **http** ao seu aplicativo lógico.

## <a name="add-metadata-to-artifacts"></a>Adicionar metadados a artefatos

1. Inicie sessão no <a href="https://portal.azure.com" target="_blank">portal do Azure</a> com as credenciais da sua conta do Azure. Localize e abra sua conta de integração.

1. Selecione o artefato para o qual você deseja adicionar metadados e escolha **Editar**. Insira os detalhes de metadados para esse artefato, por exemplo:

   ![Inserir metadados](media/logic-apps-enterprise-integration-metadata/add-partner-metadata.png)

1. Quando terminar, escolha **OK**.

1. Para exibir esses metadados na definição de JavaScript Object Notation (JSON) para a conta de integração, escolha **Editar como JSON** para que o editor de JSON seja aberto: 

   ![JSON para metadados do parceiro](media/logic-apps-enterprise-integration-metadata/partner-metadata.png)

## <a name="get-artifact-metadata"></a>Obter metadados de artefato

1. No portal do Azure, abra o aplicativo lógico que está vinculado à conta de integração que você deseja. 

1. No designer de aplicativo lógico, se você estiver adicionando a etapa para obter metadados no gatilho ou na última ação no fluxo de trabalho, escolha **nova etapa**  > **Adicionar uma ação**. 

1. Na caixa de pesquisa, digite "conta de integração". Na caixa de pesquisa, escolha **tudo**. Na lista ações, selecione esta ação: **pesquisa de artefato da conta de integração-conta de integração**

   ![Selecione "pesquisa de artefato da conta de integração"](media/logic-apps-enterprise-integration-metadata/integration-account-artifact-lookup.png)

1. Forneça essas informações para o artefato que você deseja encontrar:

   | Propriedade | Obrigatório | Valor | Descrição | 
   |----------|---------|-------|-------------| 
   | **Tipo de artefato** | Sim | **Esquema**, **mapa**, **parceiro**, **contrato**ou tipo personalizado | O tipo do artefato que você deseja | 
   | **Nome do artefato** | Sim | <*artefato-nome* > | O nome do artefato que você deseja | 
   ||| 

   Por exemplo, suponha que você queira obter os metadados para um artefato de parceiro comercial:

   ![Selecione o tipo de artefato e insira o nome do artefato](media/logic-apps-enterprise-integration-metadata/artifact-lookup-information.png)

1. Adicione a ação que você deseja para lidar com os metadados, por exemplo:

   1. Na ação **pesquisa de artefato da conta de integração** , escolha **próxima etapa**e selecione **Adicionar uma ação**. 

   1. Na caixa de pesquisa, digite "http". Na caixa de pesquisa, escolha **interno**e selecione esta ação: **http-http**

      ![Adicionar ação HTTP](media/logic-apps-enterprise-integration-metadata/http-action.png)

   1. Forneça informações para os metadados de artefato que você deseja gerenciar. 

      Por exemplo, suponha que você queira obter os metadados `routingUrl` que são adicionados anteriormente neste tópico. Aqui estão os valores de propriedade que você pode especificar: 

      | Propriedade | Obrigatório | Valor | Descrição | 
      |----------|----------|-------|-------------| 
      | **Método** | Sim | <*operação para executar* > | A operação HTTP a ser executada no artefato. Por exemplo, essa ação HTTP usa o método **Get** . | 
      | **URI** | Sim | *metadados <-local* > | Para acessar o valor de metadados de `routingUrl` do artefato que você recuperou, você pode usar uma expressão, por exemplo: <p>`@{outputs('Integration_Account_Artifact_Lookup')['properties']['metadata']['routingUrl']}` | 
      | **Headers** (Cabeçalhos) | Não | <*valores de cabeçalho* > | Qualquer saída de cabeçalho do gatilho que você deseja passar para a ação HTTP. Por exemplo, para passar o valor da propriedade de `headers` do gatilho: você pode usar uma expressão, por exemplo: <p>`@triggeroutputs()['headers']` | 
      | **Corpo** | Não | >*de < corpo-conteúdo* | Qualquer outro conteúdo que você queira passar pela propriedade `body` da ação HTTP. Este exemplo passa os valores de `properties` do artefato para a ação HTTP: <p>1. Clique dentro da propriedade **corpo** para que a lista de conteúdo dinâmico seja exibida. Se nenhuma propriedade aparecer, escolha **Ver mais**. <br>2. na lista de conteúdo dinâmico, em **pesquisa de artefato da conta de integração**, selecione **Propriedades**. | 
      |||| 

      Por exemplo:

      ![Especificar valores e expressões para a ação HTTP](media/logic-apps-enterprise-integration-metadata/add-http-action-values.png)

   1. Para verificar as informações fornecidas para a ação HTTP, exiba a definição de JSON do aplicativo lógico. Na barra de ferramentas do designer do aplicativo lógico, escolha **exibição de código** para que a definição JSON do aplicativo seja exibida, por exemplo:

      ![Definição de JSON do aplicativo lógico](media/logic-apps-enterprise-integration-metadata/finished-logic-app-definition.png)

      Depois de voltar para o designer do aplicativo lógico, todas as expressões usadas agora serão resolvidas, por exemplo:

      ![Expressões resolvidas no designer de aplicativo lógico](media/logic-apps-enterprise-integration-metadata/resolved-expressions.png)

## <a name="next-steps"></a>Passos seguintes

* [Saiba mais sobre contratos](logic-apps-enterprise-integration-agreements.md)
