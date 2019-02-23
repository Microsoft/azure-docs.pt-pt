---
title: Integrar as operações de segurança com o Microsoft Graph Security - Azure Logic Apps
description: Melhorar as capacidades de proteção, deteção e resposta de ameaças da sua aplicação ao gerir as operações de segurança com o Microsoft Graph Security e Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: preetikr
ms.author: preetikr
ms.reviewer: klam, estfan, LADocs
ms.topic: article
ms.date: 01/30/2019
tags: connectors
ms.openlocfilehash: 24963a35bc3e54b2d140bf4ed1d169b213bd9b2a
ms.sourcegitcommit: 8ca6cbe08fa1ea3e5cdcd46c217cfdf17f7ca5a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/22/2019
ms.locfileid: "56673689"
---
# <a name="improve-threat-protection-by-integrating-security-operations-with-microsoft-graph-security--azure-logic-apps"></a>Melhorar a proteção contra ameaças, integrando operações de segurança com o Microsoft Graph Security e Azure Logic Apps

Com o [Azure Logic Apps](../logic-apps/logic-apps-overview.md) e o [gráfico de segurança da Microsoft](https://docs.microsoft.com/graph/security-concept-overview) conector, pode melhorar como Deteta a sua aplicação, protege e responde às ameaças com a criação de fluxos de trabalho automatizados para a integração Microsoft produtos de segurança, serviços e parceiros. Por exemplo, pode criar [playbooks de centro de segurança do Azure](../security-center/security-center-playbooks.md) que monitorizar e gerir as entidades de segurança do Microsoft Graph, tais como alertas. Seguem-se alguns cenários suportados pelo conector do Microsoft Graph Security:

* Obter alertas com base nas consultas ou por ID de alerta. Por exemplo, pode obter uma lista que inclui alertas de gravidade elevada.
* Atualize alertas. Por exemplo, pode atualizar alerta atribuições, adicionar comentários a alertas ou marcar alertas.
* Monitor quando os alertas são criados ou alterados criando [alertar subscrições (webhooks)](https://docs.microsoft.com/graph/api/resources/webhooks).
* Gerir as subscrições de alertas. Por exemplo, pode obter subscrições ativas, expandir o tempo de expiração para uma subscrição ou eliminar subscrições.

Fluxo de trabalho da sua aplicação lógica pode utilizar as ações que obtém respostas a partir do conector do Microsoft Graph Security e disponibilizar essa saída para outras ações no seu fluxo de trabalho. Também pode ter outras ações no seu uso de fluxo de trabalho a saída das ações de conector de segurança do Microsoft Graph. Por exemplo, se receber alertas de gravidade elevada através do conector de segurança do Microsoft Graph, pode enviar alertas numa mensagem de e-mail ao utilizar o conector do Outlook. 

Para saber mais sobre a segurança do Microsoft Graph, veja a [descrição geral da API de segurança do Microsoft Graph](https://aka.ms/graphsecuritydocs). Se estiver familiarizado com aplicações lógicas, reveja [o que é o Azure Logic Apps?](../logic-apps/logic-apps-overview.md). Se estiver à procura para o Microsoft Flow ou PowerApps, veja [o que é o fluxo?](https://flow.microsoft.com/) ou [o que é o PowerApps?](https://powerapps.microsoft.com/)

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, [inscreva-se para obter uma conta do Azure gratuita](https://azure.microsoft.com/free/). 

* Para utilizar o conector de segurança do Microsoft Graph, tem de ter *explicitamente determinado* consentimento de administrador inquilino de Azure Active Directory (AD), que faz parte do [os requisitos de autenticação de segurança do Microsoft Graph ](https://aka.ms/graphsecurityauth). Este consentimento requer o ID da aplicação e o nome, o que também pode ser encontrado no conector de segurança do Microsoft Graph a [portal do Azure](https://portal.azure.com):

   | Propriedade | Value |
   |----------|-------|
   | **Nome da aplicação** | `MicrosoftGraphSecurityConnector` |
   | **ID da aplicação** | `c4829704-0edc-4c3d-a347-7c4a67586f3c` |
   |||

   Para conceder autorização para o conector, o administrador de inquilino do Azure AD pode seguir qualquer um destes passos:

   * [Conceder autorização de administrador de inquilino para aplicações do Azure AD](../active-directory/develop/v2-permissions-and-consent.md).

   * Durante a primeira execução da sua aplicação lógica, a aplicação pode pedir consentimento de administrador de inquilino do Azure AD através da [experiência de consentimento da aplicação](../active-directory/develop/application-consent-experience.md).
   
* Conhecimento básico sobre [como criar aplicações lógicas](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* A aplicação lógica em que deseja acessar suas entidades de segurança do Microsoft Graph, tais como alertas. Atualmente, este conector não tem acionadores. Então, para utilizar uma ação de segurança do Microsoft Graph, inicie a aplicação lógica com um acionador, por exemplo, o **periodicidade** acionador.

## <a name="connect-to-microsoft-graph-security"></a>Ligar a segurança do Microsoft Graph 

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Inicie sessão para o [portal do Azure](https://portal.azure.com/)e abra a aplicação lógica no Estruturador da aplicação lógica, se não estiver já abrir.

1. Para aplicações de lógica em branco, adicione o acionador e outras ações que pretende antes de adicionar uma ação de segurança do Microsoft Graph.

   -ou-

   Para logic apps existentes, sob a última etapa em que pretende adicionar uma ação de segurança do Microsoft Graph, escolha **novo passo**.

   -ou-

   Para adicionar uma ação entre passos, mova o ponteiro do mouse sobre a seta entre passos. 
   Selecione o sinal de adição (+) que aparece e selecione **adicionar uma ação**.

1. Na caixa de pesquisa, introduza "segurança da microsoft graph" como o filtro. Na lista de ações, selecione a ação que pretende.

1. Inicie sessão com as suas credenciais de segurança do Microsoft Graph.

1. Forneça os detalhes necessários para a ação selecionada e continuar a criar o fluxo de trabalho da sua aplicação lógica.

## <a name="add-actions"></a>Adicionar ações

Seguem-se detalhes mais específicos sobre como utilizar as diversas ações disponíveis com o conector de segurança do Microsoft Graph.

### <a name="manage-alerts"></a>Gerir alertas

Para filtrar, ordenar, ou obtenha os resultados mais recentes, fornecer *apenas* a [parâmetros de consulta ODATA suportados pelo Microsoft Graph](https://docs.microsoft.com/graph/query-parameters). *Não especificar* o URL de base completo ou a ação de HTTP, por exemplo, `https://graph.microsoft.com/v1.0/security/alerts`, ou o `GET` ou `PATCH` operação. Eis um exemplo específico, que mostra os parâmetros para um **obter alertas** ação quando pretender que uma lista com alertas de gravidade elevada:

`Filter alerts value as Severity eq 'high'`

Para obter mais informações sobre as consultas que pode utilizar com este conector, consulte a [documentação de referência de alertas de segurança do Microsoft Graph](https://docs.microsoft.com/graph/api/alert-list). Para criar experiências aprimoradas com este conector, saiba mais sobre o [alertas de propriedades de esquema](https://docs.microsoft.com/graph/api/resources/alert) que suporta o conector.

| Ação | Descrição |
|--------|-------------|
| **Obter alertas** | Obter alertas filtrado um com base num ou mais [propriedades do alerta](https://docs.microsoft.com/graph/api/resources/alert), por exemplo: <p>`Provider eq 'Azure Security Center' or 'Palo Alto Networks'` | 
| **Obter alerta por ID** | Obter um alerta específico com base no ID do alerta. | 
| **Alerta de atualização** | Atualizar um alerta específico com base no ID do alerta. <p>Para certificar-se de que passar as propriedades necessárias e editáveis no seu pedido, consulte a [editáveis propriedades de alertas](https://docs.microsoft.com/graph/api/alert-update). Por exemplo, para atribuir um alerta para um analista de segurança, para que podem investigar, pode atualizar o alerta **atribuídas a** propriedade. |
|||

### <a name="manage-alert-subscriptions"></a>Gerir subscrições de alertas

Suporta o Microsoft Graph [ *subscrições*](https://docs.microsoft.com/graph/api/resources/subscription), ou [ *webhooks*](https://docs.microsoft.com/graph/api/resources/webhooks). Para obter, atualizar, ou eliminar subscrições, forneça o [parâmetros de consulta ODATA suportados pelo Microsoft Graph](https://docs.microsoft.com/graph/query-parameters) para a entidade Microsoft Graph construir e incluir `security/alerts` seguido a consulta ODATA. 
*Não inclua* o URL de base, por exemplo, `https://graph.microsoft.com/v1.0`. Em vez disso, utilize o formato neste exemplo:

`security/alerts?$filter=status eq 'New'`

| Ação | Descrição |
|--------|-------------|
| **Criar subscrições** | [Criar uma subscrição](https://docs.microsoft.com/graph/api/subscription-post-subscriptions) que notifica-o sobre quaisquer alterações. Pode filtrar esta subscrição para os tipos de alerta específicos que pretende. Por exemplo, pode criar uma subscrição que notifica-o sobre alertas de gravidade elevada. |
| **Obter subscrições ativas** | [Obter subscrições existe](https://docs.microsoft.com/graph/api/subscription-list). | 
| **Atualizar a subscrição** | [Atualizar uma assinatura](https://docs.microsoft.com/graph/api/subscription-update) , fornecendo o ID de subscrição. Por exemplo, para expandir a sua subscrição, pode atualizar a subscrição `expirationDateTime` propriedade. | 
| **Eliminar subscrição** | [Eliminar uma subscrição](https://docs.microsoft.com/graph/api/subscription-delete) , fornecendo o ID de subscrição. | 
||| 

## <a name="connector-reference"></a>Referência do conector

Para obter detalhes técnicos sobre os limites, ações e acionadores, que é descrito através OpenAPI do conector (anteriormente Swagger) descrição, reveja o conector [página de referência](https://aka.ms/graphsecurityconnectorreference).

## <a name="get-support"></a>Obter suporte

Relativamente a dúvidas, visite o [fórum do Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
Para submeter ou votar em ideias para funcionalidades, visite o [site de comentários dos utilizadores do Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre outras [conectores do Logic Apps](../connectors/apis-list.md)
