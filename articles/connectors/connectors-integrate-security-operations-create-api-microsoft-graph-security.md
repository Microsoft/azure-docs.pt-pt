---
title: Integrar e gerenciar as operações de segurança & segurança de Microsoft Graph
description: Melhorar a proteção contra ameaças do seu aplicativo, a detecção e a resposta com o Microsoft Graph Security & aplicativos lógicos do Azure
services: logic-apps
ms.suite: integration
author: preetikr
ms.author: preetikr
ms.reviewer: klam, estfan, logicappspm
ms.topic: article
ms.date: 01/30/2019
tags: connectors
ms.openlocfilehash: 7e9cc2d8d38af7e5e6cf26ccc3659ee58ef17e59
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74789057"
---
# <a name="improve-threat-protection-by-integrating-security-operations-with-microsoft-graph-security--azure-logic-apps"></a>Melhore a proteção contra ameaças integrando operações de segurança com o Microsoft Graph Security & aplicativos lógicos do Azure

Com os [aplicativos lógicos do Azure](../logic-apps/logic-apps-overview.md) e o conector de [segurança Microsoft Graph](https://docs.microsoft.com/graph/security-concept-overview) , você pode melhorar a forma como seu aplicativo detecta, protege e responde a ameaças criando fluxos de trabalho automatizados para integrar produtos, serviços e parceiros de segurança da Microsoft. Por exemplo, você pode criar [Guias estratégicos da central de segurança do Azure](../security-center/security-center-playbooks.md) que monitoram e gerenciam Microsoft Graph entidades de segurança, como alertas. Aqui estão alguns cenários com suporte do conector de segurança Microsoft Graph:

* Obter alertas com base em consultas ou por ID de alerta. Por exemplo, você pode obter uma lista que inclui alertas de severidade alta.
* Atualizar alertas. Por exemplo, você pode atualizar atribuições de alerta, adicionar comentários a alertas ou marcar alertas.
* Monitore quando os alertas são criados ou alterados por meio da criação de [assinaturas de alerta (WebHooks)](https://docs.microsoft.com/graph/api/resources/webhooks).
* Gerencie suas assinaturas de alerta. Por exemplo, você pode obter assinaturas ativas, estender o tempo de expiração de uma assinatura ou excluir assinaturas.

O fluxo de trabalho do seu aplicativo lógico pode usar ações que obtêm respostas do conector de segurança Microsoft Graph e disponibilizar essa saída para outras ações no fluxo de trabalho. Você também pode ter outras ações em seu fluxo de trabalho usar a saída das ações do conector de segurança Microsoft Graph. Por exemplo, se você obtiver alertas de severidade alta por meio do conector de segurança Microsoft Graph, poderá enviar esses alertas em uma mensagem de email usando o conector do Outlook. 

Para saber mais sobre Microsoft Graph segurança, consulte a [visão geral da API de segurança do Microsoft Graph](https://aka.ms/graphsecuritydocs). Se você for novo em aplicativos lógicos, examine [o que são os aplicativos lógicos do Azure?](../logic-apps/logic-apps-overview.md). Se você estiver procurando Microsoft Flow ou PowerApps, consulte [o que é o Flow?](https://flow.microsoft.com/) ou [o que é o PowerApps?](https://powerapps.microsoft.com/)

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, [inscreva-se para obter uma conta do Azure gratuita](https://azure.microsoft.com/free/). 

* Para usar o conector de segurança do Microsoft Graph, você deve ter *explicitamente* o consentimento do administrador de locatários do Azure Active Directory (AD), que faz parte dos [requisitos de autenticação de segurança do Microsoft Graph](https://aka.ms/graphsecurityauth). Esse consentimento exige a ID e o nome do aplicativo do conector de segurança Microsoft Graph, que você também pode encontrar na [portal do Azure](https://portal.azure.com):

   | Propriedade | Valor |
   |----------|-------|
   | **Nome do aplicativo** | `MicrosoftGraphSecurityConnector` |
   | **ID do aplicativo** | `c4829704-0edc-4c3d-a347-7c4a67586f3c` |
   |||

   Para conceder consentimento para o conector, o administrador de locatário do Azure AD pode seguir estas etapas:

   * [Conceda consentimento de administrador de locatários para aplicativos do Azure ad](../active-directory/develop/v2-permissions-and-consent.md).

   * Durante a primeira execução do aplicativo lógico, seu aplicativo pode solicitar o consentimento do administrador de locatário do Azure AD por meio da [experiência de consentimento do aplicativo](../active-directory/develop/application-consent-experience.md).
   
* Conhecimento básico sobre [como criar aplicativos lógicos](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* O aplicativo lógico no qual você deseja acessar suas entidades de segurança do Microsoft Graph, como alertas. Atualmente, esse conector não tem gatilhos. Portanto, para usar uma ação de segurança Microsoft Graph, inicie seu aplicativo lógico com um gatilho, por exemplo, o gatilho de **recorrência** .

## <a name="connect-to-microsoft-graph-security"></a>Conectar-se à segurança do Microsoft Graph 

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Entre no [portal do Azure](https://portal.azure.com/)e abra seu aplicativo lógico no designer de aplicativo lógico, se ainda não estiver aberto.

1. Para aplicativos lógicos em branco, adicione o gatilho e todas as outras ações desejadas antes de adicionar uma ação de Microsoft Graph segurança.

   -ou-

   Para os aplicativos lógicos existentes, na última etapa em que você deseja adicionar uma Microsoft Graph ação de segurança, escolha **nova etapa**.

   -ou-

   Para adicionar uma ação entre etapas, mova o ponteiro sobre a seta entre as etapas. 
   Escolha o sinal de adição (+) que aparece e selecione **Adicionar uma ação**.

1. Na caixa de pesquisa, digite "Microsoft Graph Security" como seu filtro. Na lista ações, selecione a ação desejada.

1. Entre com suas credenciais de segurança do Microsoft Graph.

1. Forneça os detalhes necessários para a ação selecionada e continue criando o fluxo de trabalho do aplicativo lógico.

## <a name="add-actions"></a>Adicionar ações

Aqui estão detalhes mais específicos sobre como usar as várias ações disponíveis com o conector de segurança Microsoft Graph.

### <a name="manage-alerts"></a>Gerir alertas

Para filtrar, classificar ou obter os resultados mais recentes, forneça *somente* os [parâmetros de consulta ODATA com suporte pelo Microsoft Graph](https://docs.microsoft.com/graph/query-parameters). *Não especifique* a URL base completa ou a ação http, por exemplo, `https://graph.microsoft.com/v1.0/security/alerts`, ou a `GET` ou `PATCH` operação. Aqui está um exemplo específico que mostra os parâmetros para uma ação **obter alertas** quando você deseja uma lista com alertas de severidade alta:

`Filter alerts value as Severity eq 'high'`

Para obter mais informações sobre as consultas que você pode usar com esse conector, consulte a [documentação de referência de alertas de segurança Microsoft Graph](https://docs.microsoft.com/graph/api/alert-list). Para criar experiências aprimoradas com esse conector, saiba mais sobre os [alertas de propriedades de esquema](https://docs.microsoft.com/graph/api/resources/alert) aos quais o conector dá suporte.

| Ação | Descrição |
|--------|-------------|
| **Obter alertas** | Obtenha alertas filtrados com base em uma ou mais [Propriedades de alerta](https://docs.microsoft.com/graph/api/resources/alert), por exemplo: <p>`Provider eq 'Azure Security Center' or 'Palo Alto Networks'` | 
| **Obter alerta por ID** | Obtenha um alerta específico com base na ID do alerta. | 
| **Atualizar alerta** | Atualize um alerta específico com base na ID do alerta. <p>Para garantir que você passe as propriedades requeridas e editáveis em sua solicitação, consulte as [Propriedades editáveis para alertas](https://docs.microsoft.com/graph/api/alert-update). Por exemplo, para atribuir um alerta a um analista de segurança para que ele possa investigar, você pode atualizar a propriedade **atribuída ao** alerta. |
|||

### <a name="manage-alert-subscriptions"></a>Gerenciar assinaturas de alerta

O Microsoft Graph dá suporte a [*assinaturas*](https://docs.microsoft.com/graph/api/resources/subscription)ou [*WebHooks*](https://docs.microsoft.com/graph/api/resources/webhooks). Para obter, atualizar ou excluir assinaturas, forneça os [parâmetros de consulta ODATA com suporte pelo Microsoft Graph](https://docs.microsoft.com/graph/query-parameters) para a construção de entidade Microsoft Graph e inclua `security/alerts` seguido pela consulta ODATA. 
*Não inclua* a URL base, por exemplo, `https://graph.microsoft.com/v1.0`. Em vez disso, use o formato neste exemplo:

`security/alerts?$filter=status eq 'New'`

| Ação | Descrição |
|--------|-------------|
| **Criar assinaturas** | [Crie uma assinatura](https://docs.microsoft.com/graph/api/subscription-post-subscriptions) que notifique você sobre quaisquer alterações. Você pode filtrar essa assinatura para os tipos de alertas específicos que desejar. Por exemplo, você pode criar uma assinatura que notifique sobre alertas de severidade alta. |
| **Obter assinaturas ativas** | [Obter assinaturas não expiradas](https://docs.microsoft.com/graph/api/subscription-list). | 
| **Atualizar assinatura** | [Atualize uma assinatura](https://docs.microsoft.com/graph/api/subscription-update) fornecendo a ID da assinatura. Por exemplo, para estender sua assinatura, você pode atualizar a propriedade de `expirationDateTime` da assinatura. | 
| **Excluir assinatura** | [Exclua uma assinatura](https://docs.microsoft.com/graph/api/subscription-delete) fornecendo a ID da assinatura. | 
||| 

## <a name="connector-reference"></a>Referência do conector

Para obter detalhes técnicos sobre gatilhos, ações e limites, que são descritos pela descrição de OpenAPI (anteriormente, Swagger) do conector, examine a [página de referência](https://aka.ms/graphsecurityconnectorreference)do conector.

## <a name="get-support"></a>Obter suporte

Relativamente a dúvidas, visite o [fórum do Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
Para submeter ou votar em ideias para funcionalidades, visite o [site de comentários dos utilizadores do Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre outros [conectores de aplicativos lógicos](../connectors/apis-list.md)
