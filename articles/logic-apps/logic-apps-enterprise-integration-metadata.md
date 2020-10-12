---
title: Gerir metadados de artefactos de conta de integração
description: Adicione ou obtenha metadados de artefactos de contas de integração em Azure Logic Apps com Pacote de Integração Empresarial
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 01/17/2019
ms.openlocfilehash: bc119f1ce8efb821781dabfb9dd259cc5c8d9c23
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "74792465"
---
# <a name="manage-artifact-metadata-in-integration-accounts-with-azure-logic-apps-and-enterprise-integration-pack"></a>Gerir metadados de artefactos em contas de integração com o Azure Logic Apps e o Enterprise Integration Pack

Pode definir metadados personalizados para artefactos em contas de integração e obter esses metadados durante o runtime para a aplicação lógica utilizar. Por exemplo, pode fornecer metadados para artefactos, tais como parceiros, acordos, esquemas e mapas - todos armazenam metadados usando pares de valores-chave. 

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição, <a href="https://azure.microsoft.com/free/" target="_blank">inscreva-se numa conta do Azure gratuita</a>.

* Uma conta de [integração](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) básica que tem os artefactos onde pretende adicionar metadados, por exemplo: 

  * [Parceiro](logic-apps-enterprise-integration-partners.md)
  * [Contrato](logic-apps-enterprise-integration-agreements.md)
  * [Esquema](logic-apps-enterprise-integration-schemas.md)
  * [Mapa](logic-apps-enterprise-integration-maps.md)

* Uma aplicação lógica que está ligada à conta de integração e metadados de artefactos que pretende utilizar. Se a sua aplicação lógica ainda não está ligada, aprenda [a ligar aplicações lógicas a contas de integração.](logic-apps-enterprise-integration-create-integration-account.md#link-account) 

  Se ainda não tem uma aplicação lógica, aprenda [a criar aplicações lógicas.](../logic-apps/quickstart-create-first-logic-app-workflow.md) 
  Adicione o gatilho e as ações que pretende utilizar para gerir metadados de artefactos. Ou, para apenas experimentar as coisas, adicione um gatilho como **Request** ou **HTTP** à sua aplicação lógica.

## <a name="add-metadata-to-artifacts"></a>Adicionar metadados a artefactos

1. Inicie sessão no <a href="https://portal.azure.com" target="_blank">portal do Azure</a> com as credenciais da sua conta do Azure. Encontre e abra a sua conta de integração.

1. Selecione o artefacto onde pretende adicionar metadados e escolha **Editar.** Introduza os detalhes dos metadados para esse artefacto, por exemplo:

   ![Inserir metadados](media/logic-apps-enterprise-integration-metadata/add-partner-metadata.png)

1. Quando tiver terminado, selecione **OK**.

1. Para ver estes metadados na definição de Notação de Objetos JavaScript (JSON) para a conta de integração, escolha **Editar como JSON** para que o editor JSON abra: 

   ![JSON para metadados parceiros](media/logic-apps-enterprise-integration-metadata/partner-metadata.png)

## <a name="get-artifact-metadata"></a>Obtenha metadados de artefactos

1. No portal Azure, abra a app lógica que está ligada à conta de integração que pretende. 

1. No Logic App Designer, se estiver a adicionar o passo para obter metadados sob o gatilho ou última ação no fluxo de trabalho, escolha **Novo passo**Adicione  >  **uma ação**. 

1. Na caixa de pesquisa, insira "conta de integração". Debaixo da caixa de pesquisa, escolha **Todos.** Da lista de ações, selecione esta ação: **Integration Account Artifact Lookup - Integration Account**

   ![Selecione "Integration Account Artifact Lookup"](media/logic-apps-enterprise-integration-metadata/integration-account-artifact-lookup.png)

1. Forneça estas informações para o artefacto que pretende encontrar:

   | Propriedade | Necessário | Valor | Descrição | 
   |----------|---------|-------|-------------| 
   | **Tipo de artefacto** | Sim | **Schema,** **Mapa,** **Parceiro,** **Acordo,** ou um tipo personalizado | O tipo para o artefacto que quer | 
   | **Nome do artefacto** | Sim | <*nome de artefacto*> | O nome do artefacto que quer | 
   ||| 

   Por exemplo, suponha que queira obter os metadados de um artefacto de parceiro comercial:

   ![Selecione o tipo de artefacto e introduza o nome do artefacto](media/logic-apps-enterprise-integration-metadata/artifact-lookup-information.png)

1. Adicione a ação que deseja para manusear esses metadados, por exemplo:

   1. No âmbito da ação **de procura de artefactos de integração,** escolha **o próximo passo**e selecione Adicione **uma ação**. 

   1. Na caixa de pesquisa, insira "http". Na caixa de pesquisa, escolha **Incorporados,** e selecione esta ação: **HTTP - HTTP**

      ![Adicionar ação HTTP](media/logic-apps-enterprise-integration-metadata/http-action.png)

   1. Forneça informações sobre os metadados de artefactos que pretende gerir. 

      Por exemplo, suponha que queira obter os `routingUrl` metadados adicionados mais cedo neste tópico. Aqui estão os valores de propriedade que pode especificar: 

      | Propriedade | Necessário | Valor | Descrição | 
      |----------|----------|-------|-------------| 
      | **Método** | Sim | <*operação a executar*> | A operação HTTP para executar o artefacto. Por exemplo, esta ação HTTP utiliza o método **GET.** | 
      | **URI** | Sim | <*metadados-localização*> | Para aceder ao `routingUrl` valor dos metadados a partir do artefacto que recuperou, pode utilizar uma expressão, por exemplo: <p>`@{outputs('Integration_Account_Artifact_Lookup')['properties']['metadata']['routingUrl']}` | 
      | **Cabeçalhos** | Não | <*cabeçalho-valores*> | Quaisquer saídas de cabeçalho do gatilho que pretende passar para a ação HTTP. Por exemplo, para passar no valor da propriedade do `headers` gatilho: pode utilizar uma expressão, por exemplo: <p>`@triggeroutputs()['headers']` | 
      | **Corpo** | Não | <*conteúdo corporal*> | Qualquer outro conteúdo que pretenda passar pela propriedade da ação `body` HTTP. Este exemplo transmite os valores do artefacto `properties` para a ação HTTP: <p>1. Clique dentro da propriedade **Body** para que apareça a lista de conteúdos dinâmicos. Se não aparecerem propriedades, escolha **Ver mais.** <br>2. Da lista de conteúdos dinâmicos, no âmbito **do Integration Account Artifact Lookup**, selecione **Properties**. | 
      |||| 

      Por exemplo:

      ![Especificar valores e expressões para ação HTTP](media/logic-apps-enterprise-integration-metadata/add-http-action-values.png)

   1. Para verificar as informações fornecidas para a ação HTTP, consulte a definição JSON da sua aplicação lógica. Na barra de ferramentas Logic App Designer, escolha **a vista de código** para que a definição JSON da aplicação apareça, por exemplo:

      ![Definição JSON de aplicativo lógico](media/logic-apps-enterprise-integration-metadata/finished-logic-app-definition.png)

      Depois de voltar a mudar para o Logic App Designer, quaisquer expressões que usou parecem agora resolvidas, por exemplo:

      ![Expressões resolvidas no Designer de Aplicações Lógicas](media/logic-apps-enterprise-integration-metadata/resolved-expressions.png)

## <a name="next-steps"></a>Passos seguintes

* [Saiba mais sobre acordos](logic-apps-enterprise-integration-agreements.md)
