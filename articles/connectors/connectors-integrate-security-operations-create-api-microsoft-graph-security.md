---
title: Integrar e gerenciar as operações de segurança & segurança de Microsoft Graph
description: Melhorar a proteção contra ameaças do seu aplicativo, a detecção e a resposta com o Microsoft Graph Security & aplicativos lógicos do Azure
services: logic-apps
ms.suite: integration
author: preetikr
ms.author: preetikr
ms.reviewer: klam, estfan, logicappspm
ms.topic: article
ms.date: 12/12/2019
tags: connectors
ms.openlocfilehash: f9aa88934d67d98fce43763c6c8fac7c384d765d
ms.sourcegitcommit: a9b1f7d5111cb07e3462973eb607ff1e512bc407
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/22/2020
ms.locfileid: "76313795"
---
# <a name="improve-threat-protection-by-integrating-security-operations-with-microsoft-graph-security--azure-logic-apps"></a>Melhore a proteção contra ameaças integrando operações de segurança com o Microsoft Graph Security & aplicativos lógicos do Azure

Com os [aplicativos lógicos do Azure](../logic-apps/logic-apps-overview.md) e o conector de [segurança Microsoft Graph](https://docs.microsoft.com/graph/security-concept-overview) , você pode melhorar a forma como seu aplicativo detecta, protege e responde a ameaças criando fluxos de trabalho automatizados para integrar produtos, serviços e parceiros de segurança da Microsoft. Por exemplo, você pode criar [Guias estratégicos da central de segurança do Azure](../security-center/security-center-playbooks.md) que monitoram e gerenciam Microsoft Graph entidades de segurança, como alertas. Aqui estão alguns cenários com suporte pelo conector de segurança Microsoft Graph:

* Obter alertas com base em consultas ou por ID de alerta. Por exemplo, você pode obter uma lista que inclui alertas de severidade alta.

* Atualizar alertas. Por exemplo, você pode atualizar atribuições de alerta, adicionar comentários a alertas ou marcar alertas.

* Monitore quando os alertas são criados ou alterados por meio da criação de [assinaturas de alerta (WebHooks)](https://docs.microsoft.com/graph/api/resources/webhooks).

* Gerencie suas assinaturas de alerta. Por exemplo, você pode obter assinaturas ativas, estender o tempo de expiração de uma assinatura ou excluir assinaturas.

O fluxo de trabalho do seu aplicativo lógico pode usar ações que obtêm respostas do conector de segurança Microsoft Graph e disponibilizar essa saída para outras ações no fluxo de trabalho. Você também pode ter outras ações em seu fluxo de trabalho usar a saída das ações do conector de segurança Microsoft Graph. Por exemplo, se você obtiver alertas de severidade alta por meio do conector de segurança Microsoft Graph, poderá enviar esses alertas em uma mensagem de email usando o conector do Outlook. 

Para saber mais sobre Microsoft Graph segurança, consulte a [visão geral da API de segurança do Microsoft Graph](https://aka.ms/graphsecuritydocs). Se você for novo em aplicativos lógicos, examine [o que são os aplicativos lógicos do Azure?](../logic-apps/logic-apps-overview.md). Se você estiver procurando Microsoft Flow ou PowerApps, consulte [o que é o Flow?](https://flow.microsoft.com/) ou [o que é o powerapps?](https://powerapps.microsoft.com/)

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, [inscreva-se para obter uma conta do Azure gratuita](https://azure.microsoft.com/free/). 

* Para utilizar o conector de Segurança do Microsoft Graph, tem de lhe ser *dado explicitamente* o consentimento de um administrador de inquilinos do Azure Active Directory (AD), o que faz parte dos [requisitos da Autenticação de Segurança do Microsoft Graph](https://aka.ms/graphsecurityauth). Esse consentimento exige a ID e o nome do aplicativo do conector de segurança Microsoft Graph, que você também pode encontrar na [portal do Azure](https://portal.azure.com):

  | Propriedade | Valor |
  |----------|-------|
  | **Nome da Aplicação** | `MicrosoftGraphSecurityConnector` |
  | **ID da Aplicação** | `c4829704-0edc-4c3d-a347-7c4a67586f3c` |
  |||

  Para conceder consentimento para o conector, o administrador de locatário do Azure AD pode seguir estas etapas:

  * [Dar consentimento ao administrador de inquilinos para as aplicações do Azure AD](../active-directory/develop/v2-permissions-and-consent.md).

  * Durante a primeira execução da sua aplicação lógica, a mesma poderá pedir o consentimento do seu administrador de inquilinos do Azure AD através da [experiência de consentimento da aplicação](../active-directory/develop/application-consent-experience.md).
   
* Conhecimento básico sobre [como criar aplicativos lógicos](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* O aplicativo lógico no qual você deseja acessar suas entidades de segurança do Microsoft Graph, como alertas. Para usar um gatilho de segurança Microsoft Graph, você precisa de um aplicativo lógico em branco. Para usar uma ação de segurança Microsoft Graph, você precisa de um aplicativo lógico que comece com o gatilho apropriado para seu cenário.

## <a name="connect-to-microsoft-graph-security"></a>Conectar-se à segurança do Microsoft Graph 

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Entre no [portal do Azure](https://portal.azure.com/)e abra seu aplicativo lógico no designer de aplicativo lógico, se ainda não estiver aberto.

1. Para aplicativos lógicos em branco, adicione o gatilho e todas as outras ações que desejar antes de adicionar uma ação de Microsoft Graph segurança.

   -ou-

   Para os aplicativos lógicos existentes, na última etapa em que você deseja adicionar uma Microsoft Graph ação de segurança, selecione **nova etapa**.

   -ou-

   Para adicionar uma ação entre etapas, mova o ponteiro sobre a seta entre as etapas. Selecione o sinal de adição (+) que aparece e selecione **Adicionar uma ação**.

1. Na caixa de pesquisa, digite "Microsoft Graph Security" como seu filtro. Na lista ações, selecione a ação desejada.

1. Entre com suas credenciais de segurança do Microsoft Graph.

1. Forneça os detalhes necessários para a ação selecionada e continue criando o fluxo de trabalho do aplicativo lógico.

## <a name="add-triggers"></a>Adicionar gatilhos

Em aplicativos lógicos do Azure, cada aplicativo lógico deve começar com um [gatilho](../logic-apps/logic-apps-overview.md#logic-app-concepts), que é acionado quando um evento específico ocorre ou quando uma condição específica é atendida. Cada vez que o gatilho é acionado, o mecanismo de aplicativos lógicos cria uma instância de aplicativo lógico e começa a executar o fluxo de trabalho do aplicativo.

> [!NOTE] 
> Quando um gatilho é acionado, o gatilho processa todos os novos alertas. Se nenhum alerta for recebido, a execução do gatilho será ignorada. A sondagem do gatilho seguinte ocorre com base no intervalo de recorrência especificado nas propriedades do gatilho.

Este exemplo mostra como você pode iniciar um fluxo de trabalho de aplicativo lógico quando novos alertas são enviados para seu aplicativo.

1.  No portal do Azure ou no Visual Studio, crie um aplicativo lógico em branco, que abre o designer do aplicativo lógico. Este exemplo usa o portal do Azure.

1.  No designer, na caixa de pesquisa, digite "Microsoft Graph Security" como filtro. Na lista de gatilhos, selecione este gatilho: **em todos os novos alertas**

1.  No gatilho, forneça informações sobre os alertas que você deseja monitorar. Para obter mais propriedades, abra a lista **Adicionar novo parâmetro** e selecione um parâmetro para adicionar essa propriedade ao gatilho.

   | Propriedade | Propriedade (JSON) | Obrigatório | Tipo | Descrição |
   |----------|-----------------|----------|------|-------------|
   | **Intervalo** | `interval` | Sim | Número inteiro | Um inteiro positivo que descreve com que frequência o fluxo de trabalho é executado com base na frequência. Aqui estão os intervalos mínimo e máximo: <p><p>-Mês: 1-16 meses <br>-Dia: 1-500 dias <br>-Hora: 1 a 12000 horas <br>-Minuto: 1 a 72000 minutos <br>-Segundo: 1 a 9999999 segundos <p>Por exemplo, se o intervalo for 6 e a frequência for "month", a recorrência será a cada seis meses. |
   | **Frequência** | `frequency` | Sim | Cadeia | A unidade de tempo para a recorrência: **segundo**, **minuto**, **hora**, **dia**, **semana**ou **mês** |
   | **Time zone** (Fuso horário) | `timeZone` | Não | Cadeia | Aplica-se somente quando você especifica uma hora de início porque esse gatilho não aceita o [deslocamento UTC](https://en.wikipedia.org/wiki/UTC_offset). Selecione o fuso horário que você deseja aplicar. |
   | **Start time** (Hora de início) | `startTime` | Não | Cadeia | Forneça uma data e hora de início neste formato: <p><p>AAAA-MM-DDThh: mm: SS se você selecionar um fuso horário <p>-ou- <p>AAAA-MM-DDThh: mm: ssZ se você não selecionar um fuso horário <p>Por exemplo, se você quiser 18 de setembro de 2017 às 2:00 PM, especifique "2017-09-18T14:00:00" e selecione um fuso horário como hora padrão do Pacífico. Ou especifique "2017-09-18T14:00:00Z" sem um fuso horário. <p>**Observação:** Essa hora de início tem um máximo de 49 anos no futuro e deve seguir a [especificação de data e hora ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations) no [formato de data e hora UTC](https://en.wikipedia.org/wiki/Coordinated_Universal_Time), mas sem um [deslocamento UTC](https://en.wikipedia.org/wiki/UTC_offset). Se você não selecionar um fuso horário, deverá adicionar a letra "Z" no final sem espaços. Esse "Z" refere-se ao [tempo náuticas](https://en.wikipedia.org/wiki/Nautical_time)equivalente. <p>Para agendamentos simples, a hora de início é a primeira ocorrência, enquanto para agendas complexas, o gatilho não é acionado antes da hora de início. [*Quais são as maneiras como posso usar a data e a hora de início?* ](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#start-time) |
   ||||||

1.  Quando terminar, na barra de ferramentas do designer, selecione **salvar**.

1.  Agora, continue adicionando uma ou mais ações ao seu aplicativo lógico para as tarefas que você deseja executar com os resultados do gatilho.

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

O Microsoft Graph dá suporte a [*assinaturas*](https://docs.microsoft.com/graph/api/resources/subscription)ou [*WebHooks*](https://docs.microsoft.com/graph/api/resources/webhooks). Para obter, atualizar ou excluir assinaturas, forneça os [parâmetros de consulta ODATA com suporte pelo Microsoft Graph](https://docs.microsoft.com/graph/query-parameters) para a construção de entidade Microsoft Graph e inclua `security/alerts` seguido pela consulta ODATA. *Não inclua* a URL base, por exemplo, `https://graph.microsoft.com/v1.0`. Em vez disso, use o formato neste exemplo:

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

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre outros [conectores de aplicativos lógicos](../connectors/apis-list.md)
