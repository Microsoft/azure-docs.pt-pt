---
title: Gerir metadados de artefactos de conta de integração
description: Adicione ou obtenha metadados de artefactos de contas de integração em Aplicações lógicas azure com pacote de integração empresarial
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 01/17/2019
ms.openlocfilehash: bc119f1ce8efb821781dabfb9dd259cc5c8d9c23
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74792465"
---
# <a name="manage-artifact-metadata-in-integration-accounts-with-azure-logic-apps-and-enterprise-integration-pack"></a>Gerir metadados de artefactos em contas de integração com aplicações da Lógica Azure e Pacote de Integração Empresarial

Pode definir metadados personalizados para artefactos em contas de integração e obter esses metadados durante o tempo de execução para a sua aplicação lógica usar. Por exemplo, pode fornecer metadados para artefactos, tais como parceiros, acordos, schemas e mapas - todos os metadados da loja usando pares de valor-chave. 

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição, <a href="https://azure.microsoft.com/free/" target="_blank">inscreva-se numa conta do Azure gratuita</a>.

* Uma conta de [integração](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) básica que tem os artefactos onde pretende adicionar metadados, por exemplo: 

  * [Partner](logic-apps-enterprise-integration-partners.md)
  * [Contrato](logic-apps-enterprise-integration-agreements.md)
  * [Esquema](logic-apps-enterprise-integration-schemas.md)
  * [Mapa](logic-apps-enterprise-integration-maps.md)

* Uma aplicação lógica que está ligada à conta de integração e metadados de artefactos que pretende utilizar. Se a sua aplicação lógica ainda não está ligada, aprenda [a ligar aplicações lógicas a contas de integração.](logic-apps-enterprise-integration-create-integration-account.md#link-account) 

  Se ainda não tem uma aplicação lógica, aprenda [a criar aplicações lógicas.](../logic-apps/quickstart-create-first-logic-app-workflow.md) 
  Adicione o gatilho e as ações que pretende utilizar para gerir metadados de artefactos. Ou, para apenas experimentar as coisas, adicione um gatilho como **Request** ou **HTTP** à sua aplicação lógica.

## <a name="add-metadata-to-artifacts"></a>Adicionar metadados a artefactos

1. Inicie sessão no <a href="https://portal.azure.com" target="_blank">portal do Azure</a> com as credenciais da sua conta do Azure. Encontre e abra a sua conta de integração.

1. Selecione o artefacto onde pretende adicionar metadados e escolha **Editar**. Introduza os dados de dados para esse artefacto, por exemplo:

   ![Introduzir metadados](media/logic-apps-enterprise-integration-metadata/add-partner-metadata.png)

1. Quando tiver terminado, selecione **OK**.

1. Para visualizar estes metadados na definição de Notação de Objetos JavaScript (JSON) para a conta de integração, escolha **Editar como JSON** para que o editor da JSON abra: 

   ![JSON para metadados de parceiros](media/logic-apps-enterprise-integration-metadata/partner-metadata.png)

## <a name="get-artifact-metadata"></a>Obtenha metadados de artefactos

1. No portal Azure, abra a app lógica que está ligada à conta de integração que deseja. 

1. No Logic App Designer, se estiver a adicionar o passo para obter metadados sob o gatilho ou última ação no fluxo de trabalho, escolha **novo passo** > **Adicione uma ação**. 

1. Na caixa de pesquisa, insira a "conta de integração". Debaixo da caixa de pesquisa, escolha **All**. Da lista de ações, selecione esta ação: **Integration Account Artifact Lookup - Conta** de Integração

   ![Selecione "Integration Account Artifact Lookup"](media/logic-apps-enterprise-integration-metadata/integration-account-artifact-lookup.png)

1. Forneça esta informação para o artefacto que pretende encontrar:

   | Propriedade | Necessário | Valor | Descrição | 
   |----------|---------|-------|-------------| 
   | **Tipo de artefacto** | Sim | **Schema**, **Mapa,** **Parceiro,** **Acordo,** ou um tipo personalizado | O tipo para o artefacto que quer | 
   | **Nome do artefacto** | Sim | <*nome do artefacto*> | O nome do artefacto que quer | 
   ||| 

   Por exemplo, suponha que quer obter os metadados para um artefacto de parceiro comercial:

   ![Selecione o tipo de artefacto e introduza o nome do artefacto](media/logic-apps-enterprise-integration-metadata/artifact-lookup-information.png)

1. Adicione a ação que pretende para o manuseamento dos metadados, por exemplo:

   1. Sob a ação de procura de artefactos de **integração,** escolha **o próximo passo,** e selecione **Adicionar uma ação**. 

   1. Na caixa de pesquisa, insira "http". Sob a caixa de pesquisa, escolha **Incorporados,** e selecione esta ação: **HTTP - HTTP - HTTP**

      ![Adicionar ação HTTP](media/logic-apps-enterprise-integration-metadata/http-action.png)

   1. Forneça informações sobre os metadados de artefactos que pretende gerir. 

      Por exemplo, suponha `routingUrl` que quer obter os metadados que são adicionados mais cedo neste tópico. Aqui estão os valores de propriedade que pode especificar: 

      | Propriedade | Necessário | Valor | Descrição | 
      |----------|----------|-------|-------------| 
      | **Método** | Sim | <*operação-a-executar*> | A operação HTTP para funcionar no artefacto. Por exemplo, esta ação HTTP utiliza o método **GET.** | 
      | **URI** | Sim | <*metadados-localização*> | Para aceder `routingUrl` ao valor dos metadados a partir do artefacto que recuperou, pode utilizar uma expressão, por exemplo: <p>`@{outputs('Integration_Account_Artifact_Lookup')['properties']['metadata']['routingUrl']}` | 
      | **Cabeçalhos** | Não | <*valores cabeçalho*> | Quaisquer saídas de cabeçalho do gatilho que deseja passar para a ação HTTP. Por exemplo, para passar o `headers` valor de propriedade do gatilho: pode usar uma expressão, por exemplo: <p>`@triggeroutputs()['headers']` | 
      | **Corpo** | Não | <*conteúdo corporal*> | Qualquer outro conteúdo que queira passar pela `body` propriedade da ação HTTP. Este exemplo passa os `properties` valores do artefacto para a ação HTTP: <p>1. Clique no interior da propriedade **do Corpo** para que a lista de conteúdos dinâmicos apareça. Se não aparecerem propriedades, escolha **Ver mais**. <br>2. Da lista de conteúdos dinâmicos, no âmbito da **Integration Account Artifact Lookup,** selecione **Properties**. | 
      |||| 

      Por exemplo:

      ![Especificar valores e expressões para a ação HTTP](media/logic-apps-enterprise-integration-metadata/add-http-action-values.png)

   1. Para verificar as informações que forneceu para a ação HTTP, consulte a definição JSON da sua aplicação lógica. Na barra de ferramentas Logic App Designer, escolha a **visão de Código** para que a definição JSON da aplicação apareça, por exemplo:

      ![Definição de JSON da aplicação lógica](media/logic-apps-enterprise-integration-metadata/finished-logic-app-definition.png)

      Depois de voltar para o Logic App Designer, quaisquer expressões que usou parecem agora resolvidas, por exemplo:

      ![Expressões resolvidas em Logic App Designer](media/logic-apps-enterprise-integration-metadata/resolved-expressions.png)

## <a name="next-steps"></a>Passos seguintes

* [Saiba mais sobre os acordos](logic-apps-enterprise-integration-agreements.md)
