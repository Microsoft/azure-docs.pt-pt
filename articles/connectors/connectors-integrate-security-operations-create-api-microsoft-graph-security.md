---
title: Integrar e gerir operações de segurança & Microsoft Graph Security
description: Melhorar a proteção, deteção e resposta de ameaças da sua aplicação com a Microsoft Graph Security & Azure Logic Apps
services: logic-apps
ms.suite: integration
author: preetikr
ms.author: preetikr
ms.reviewer: v-ching, estfan, logicappspm
ms.topic: article
ms.date: 02/21/2020
tags: connectors
ms.openlocfilehash: a5842c5f3130b39f75b5339274b84feb2e0f283f
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/26/2020
ms.locfileid: "92547100"
---
# <a name="improve-threat-protection-by-integrating-security-operations-with-microsoft-graph-security--azure-logic-apps"></a>Melhorar a proteção contra ameaças integrando operações de segurança com a Microsoft Graph Security & Azure Logic Apps

Com [as Apps Azure Logic](../logic-apps/logic-apps-overview.md) e o conector Microsoft [Graph Security,](/graph/security-concept-overview) pode melhorar a forma como a sua aplicação deteta, protege e responde a ameaças criando fluxos de trabalho automatizados para integrar produtos, serviços e parceiros de segurança da Microsoft. Por exemplo, pode criar [playbooks do Azure Security Center](../security-center/workflow-automation.md) que monitorizam e gerem as entidades de Segurança de Gráficos da Microsoft, como alertas. Aqui estão alguns cenários que são suportados pelo conector microsoft Graph Security:

* Receba alertas com base em consultas ou por identificação de alerta. Por exemplo, pode obter uma lista que inclui alertas de alta gravidade.

* Alertas de atualização. Por exemplo, pode atualizar as atribuições de alerta, adicionar comentários a alertas ou marcar alertas.

* Monitorize quando os alertas forem criados ou alterados criando [subscrições de alerta (webhooks)](/graph/api/resources/webhooks).

* Gerencie as suas subscrições de alerta. Por exemplo, pode obter subscrições ativas, prolongar o tempo de validade de uma subscrição ou eliminar subscrições.

O fluxo de trabalho da sua aplicação lógica pode usar ações que obtenham respostas do conector microsoft Graph Security e disponibilizar essa saída para outras ações no seu fluxo de trabalho. Também pode ter outras ações no seu fluxo de trabalho, utilizar a saída das ações do conector de Segurança do Gráfico microsoft. Por exemplo, se receber alertas de alta gravidade através do conector Microsoft Graph Security, pode enviar esses alertas numa mensagem de correio eletrónico utilizando o conector Outlook. 

Para saber mais sobre a Microsoft Graph Security, consulte a visão geral da API de [Segurança de Gráficos da Microsoft](https://aka.ms/graphsecuritydocs). Se é novo em aplicações lógicas, [reveja o que é Azure Logic Apps?](../logic-apps/logic-apps-overview.md) Se procura o Microsoft Flow ou PowerApps, veja [o que é Flow ou](https://flow.microsoft.com/) O que é [PowerApps?](https://powerapps.microsoft.com/)

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, [inscreva-se para obter uma conta do Azure gratuita](https://azure.microsoft.com/free/). 

* Para utilizar o conector de Segurança do Microsoft Graph, tem de lhe ser *dado explicitamente* o consentimento de um administrador de inquilinos do Azure Active Directory (AD), o que faz parte dos [requisitos da Autenticação de Segurança do Microsoft Graph](https://aka.ms/graphsecurityauth). Este consentimento requer o ID e nome da aplicação do conector de segurança do Gráfico microsoft, que também pode encontrar no [portal Azure:](https://portal.azure.com)

  | Propriedade | Valor |
  |----------|-------|
  | **Nome da aplicação** | `MicrosoftGraphSecurityConnector` |
  | **ID da Aplicação** | `c4829704-0edc-4c3d-a347-7c4a67586f3c` |
  |||

  Para conceder o consentimento para o conector, o seu administrador inquilino Azure AD pode seguir estes passos:

  * [Dar consentimento ao administrador de inquilinos para as aplicações do Azure AD](../active-directory/develop/v2-permissions-and-consent.md).

  * Durante a primeira execução da sua aplicação lógica, a mesma poderá pedir o consentimento do seu administrador de inquilinos do Azure AD através da [experiência de consentimento da aplicação](../active-directory/develop/application-consent-experience.md).
   
* Conhecimento básico sobre [como criar aplicativos lógicos](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* A aplicação lógica onde pretende aceder às suas entidades de Segurança de Gráficos da Microsoft, como alertas. Para utilizar um gatilho de Segurança do Gráfico microsoft, precisa de uma aplicação lógica em branco. Para utilizar uma ação de Segurança do Microsoft Graph, precisa de uma aplicação lógica que comece com o gatilho apropriado para o seu cenário.

## <a name="connect-to-microsoft-graph-security"></a>Ligue-se à Segurança do Gráfico da Microsoft 

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Inscreva-se no [portal Azure](https://portal.azure.com/)e abra a sua aplicação lógica no Logic App Designer, se não abrir já.

1. Para aplicações lógicas em branco, adicione o gatilho e quaisquer outras ações que pretenda antes de adicionar uma ação de Segurança do Gráfico da Microsoft.

   -ou-

   Para aplicações lógicas existentes, sob o último passo em que pretende adicionar uma ação de Segurança do Gráfico da Microsoft, selecione **Novo passo** .

   -ou-

   Para adicionar uma ação entre os degraus, mova o ponteiro sobre a seta entre os degraus. Selecione o sinal de mais (+) que aparece e **selecione Adicione uma ação** .

1. Na caixa de pesquisa, introduza "segurança do gráfico microsoft" como filtro. Na lista de ações, selecione a ação desejada.

1. Inscreva-se com as suas credenciais de Segurança do Gráfico microsoft.

1. Forneça os detalhes necessários para a sua ação selecionada e continue a construir o fluxo de trabalho da sua aplicação lógica.

## <a name="add-triggers"></a>Adicionar gatilhos

Nas Azure Logic Apps, todas as aplicações lógicas devem começar com um [gatilho](../logic-apps/logic-apps-overview.md#logic-app-concepts), que dispara quando um evento específico acontece ou quando uma condição específica é cumprida. Cada vez que o gatilho dispara, o motor Logic Apps cria uma instância lógica de aplicações e começa a executar o fluxo de trabalho da sua aplicação.

> [!NOTE] 
> Quando um gatilho dispara, o gatilho processa todos os novos alertas. Se não forem recebidos alertas, o gatilho é ignorado. A próxima sondagem do gatilho acontece com base no intervalo de recorrência que se especifica nas propriedades do gatilho.

Este exemplo mostra como pode iniciar um fluxo de trabalho de aplicações lógicas quando novos alertas são enviados para a sua aplicação.

1.  No portal Azure ou Visual Studio, crie uma aplicação lógica em branco, que abre o Logic App Designer. Este exemplo utiliza o portal Azure.

1.  No designer, na caixa de pesquisa, introduza "segurança do gráfico microsoft" como filtro. A partir da lista de gatilhos, selecione este gatilho: **Em todos os novos alertas**

1.  No gatilho, forneça informações sobre os alertas que pretende monitorizar. Para mais propriedades, abra a nova lista **de parâmetros adicionar** e selecione um parâmetro para adicionar essa propriedade ao gatilho.

   | Propriedade | Propriedade (JSON) | Necessário | Tipo | Descrição |
   |----------|-----------------|----------|------|-------------|
   | **Intervalo** | `interval` | Yes | Número inteiro | Um inteiro positivo que descreve com que frequência o fluxo de trabalho funciona com base na frequência. Aqui estão os intervalos mínimos e máximos: <p><p>- Mês: 1-16 meses <br>- Dia: 1-500 dias <br>- Hora: 1-12.000 horas <br>- Minuto: 1-72.000 minutos <br>- Segundo: 1-9.999,999 segundos <p>Por exemplo, se o intervalo for 6, e a frequência for "Mês", então a recorrência é a cada 6 meses. |
   | **Frequência** | `frequency` | Sim | String | A unidade de tempo para a recorrência: **Segundo,** **Minuto,** **Hora,** **Dia,** **Semana** ou **Mês** |
   | **Fuso horário** | `timeZone` | Não | String | Aplica-se apenas quando especifica uma hora de início porque este gatilho não aceita [a compensação UTC](https://en.wikipedia.org/wiki/UTC_offset). Selecione o fuso horário que pretende aplicar. |
   | **Hora de início** | `startTime` | Não | String | Fornecer uma data e hora de início neste formato: <p><p>YYYY-MM-DDThh:mm:mm se selecionar um fuso horário <p>-ou- <p>YYYY-MM-DDThh:mm:ssZ se não selecionar um fuso horário <p>Por exemplo, se você quiser 18 de setembro de 2017 às 14:00, então especifique "2017-09-18T14:00:00" e selecione um fuso horário como o Horário Padrão do Pacífico. Ou, especificar "2017-09-18T14:00:00Z" sem um fuso horário. <p>**Nota:** Este horário de início tem um máximo de 49 anos no futuro e deve seguir a [especificação de data ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations) no [formato de data UTC,](https://en.wikipedia.org/wiki/Coordinated_Universal_Time)mas sem [compensação UTC](https://en.wikipedia.org/wiki/UTC_offset). Se não selecionar um fuso horário, deve adicionar a letra "Z" no final sem espaços. Este "Z" refere-se ao [tempo náutico](https://en.wikipedia.org/wiki/Nautical_time)equivalente. <p>Para horários simples, a hora de início é a primeira ocorrência, enquanto para horários complexos, o gatilho não dispara tão cedo quanto a hora de início. [*Quais são as maneiras que posso usar a data de início e a hora?*](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#start-time) |
   ||||||

1.  Quando terminar, na barra de ferramentas do designer, **selecione Save** .

1.  Agora continue a adicionar uma ou mais ações à sua aplicação lógica para as tarefas que pretende executar com os resultados do gatilho.

## <a name="add-actions"></a>Adicionar ações

Aqui estão mais detalhes específicos sobre a utilização das várias ações disponíveis com o conector microsoft Graph Security.

### <a name="manage-alerts"></a>Gerir alertas

Para filtrar, classificar ou obter os resultados mais recentes, fornecer *apenas* os [parâmetros de consulta ODATA suportados pelo Microsoft Graph](/graph/query-parameters). *Não especifique* o URL de base completo ou a ação HTTP, por exemplo, `https://graph.microsoft.com/v1.0/security/alerts` ou o ou o `GET` `PATCH` funcionamento. Aqui está um exemplo específico que mostra os parâmetros para uma ação **de alertas** de obter quando você quer uma lista com alertas de alta gravidade:

`Filter alerts value as Severity eq 'high'`

Para obter mais informações sobre as consultas que pode utilizar com este conector, consulte a documentação de referência do [Microsoft Graph Security](/graph/api/alert-list). Para construir experiências melhoradas com este conector, saiba mais sobre os [alertas](/graph/api/resources/alert) de propriedades de esquema que o conector suporta.

| Ação | Descrição |
|--------|-------------|
| **Receba alertas** | Obtenha alertas filtrados com base numa ou mais [propriedades de alerta,](/graph/api/resources/alert)por exemplo, `Provider eq 'Azure Security Center' or 'Palo Alto Networks'` . | 
| **Receba alerta por ID** | Obtenha um alerta específico baseado na identificação de alerta. | 
| **Alerta de atualização** | Atualize um alerta específico baseado no ID de alerta. Para se certificar de que passa as propriedades necessárias e editáveis no seu pedido, consulte as [propriedades editáveis para alertas.](/graph/api/alert-update) Por exemplo, para atribuir um alerta a um analista de segurança para que possam investigar, pode atualizar o alerta **atribuído à** propriedade. |
|||

### <a name="manage-alert-subscriptions"></a>Gerir subscrições de alerta

O Microsoft Graph suporta [*subscrições*](/graph/api/resources/subscription)ou [*webhooks*](/graph/api/resources/webhooks). Para obter, atualizar ou eliminar subscrições, forneça os [parâmetros de consulta ODATA suportados pelo Microsoft Graph](/graph/query-parameters) para a construção da entidade do Gráfico Microsoft e inclua `security/alerts` a consulta ODATA. *Não inclua* o URL base, por `https://graph.microsoft.com/v1.0` exemplo, . Em vez disso, utilize o formato neste exemplo:

`security/alerts?$filter=status eq 'NewAlert'`

| Ação | Descrição |
|--------|-------------|
| **Criar subscrições** | [Crie uma subscrição](/graph/api/subscription-post-subscriptions) que o notifique sobre quaisquer alterações. Pode filtrar esta subscrição para os tipos de alerta específicos que pretende. Por exemplo, pode criar uma subscrição que o notifique sobre alertas de alta gravidade. |
| **Obter subscrições ativas** | [Obtenha subscrições não piradas](/graph/api/subscription-list). | 
| **Atualização subscrição** | [Atualizar uma subscrição](/graph/api/subscription-update) fornecendo o ID de subscrição. Por exemplo, para estender a sua subscrição, pode atualizar a propriedade da `expirationDateTime` subscrição. | 
| **Eliminar subscrição** | [Elimine uma subscrição](/graph/api/subscription-delete) fornecendo o ID de subscrição. | 
||| 

### <a name="manage-threat-intelligence-indicators"></a>Gerir indicadores de inteligência de ameaças

Para filtrar, classificar ou obter os resultados mais recentes, fornecer *apenas* os [parâmetros de consulta ODATA suportados pelo Microsoft Graph](/graph/query-parameters). *Não especifique* o URL de base completo ou a ação HTTP, por exemplo, `https://graph.microsoft.com/beta/security/tiIndicators` ou o ou o `GET` `PATCH` funcionamento. Aqui está um exemplo específico que mostra os parâmetros para uma ação **Get tiIndicators** quando você quer uma lista que tem o `DDoS` tipo de ameaça:

`Filter threat intelligence indicator value as threatType eq 'DDoS'`

Para obter mais informações sobre as consultas que pode utilizar com este conector, consulte ["Parâmetros de consulta opcional" na documentação](/graph/api/tiindicators-list)de referência do indicador de inteligência da ameaça de segurança do Microsoft Graph. Para construir experiências melhoradas com este conector, saiba mais sobre o indicador de [inteligência de ameaça de ameaças](/graph/api/resources/tiindicator) de propriedades de esquema que o conector suporta.

| Ação | Descrição |
|--------|-------------|
| **Obtenha indicadores de inteligência de ameaça** | Obter tiIndicators filtrados com base em uma ou mais [propriedades tiIndicator,](/graph/api/resources/tiindicator)por exemplo, `threatType eq 'MaliciousUrl' or 'DDoS'` |
| **Obtenha indicador de inteligência de ameaça por ID** | Obtenha um tiIndicator específico com base no iD do tiIndicator. | 
| **Criar indicador de inteligência de ameaça** | Crie um novo tiIndicator publicando na coleção tiIndicators. Para se certificar de que passa as propriedades necessárias no seu pedido, consulte as [propriedades necessárias para a criação do tiIndicator](/graph/api/tiindicators-post). |
| **Apresentar indicadores de inteligência de ameaça múltipla** | Crie vários novos tiIndicators publicando uma coleção de tiIndicators. Para se certificar de que passa as propriedades necessárias no seu pedido, consulte as [propriedades necessárias para a submissão de múltiplos tiDdicadores](/graph/api/tiindicator-submittiindicators). |
| **Indicador de inteligência de ameaça de atualização** | Atualize um tiIndicator específico com base no iD do tiIndicator. Para se certificar de que passa as propriedades necessárias e editáveis no seu pedido, consulte as [propriedades editáveis para tiIndicator](/graph/api/tiindicator-update). Por exemplo, para atualizar a ação a aplicar se o indicador for compatível com a ferramenta de segurança TargetProduct, pode atualizar a propriedade de **ação** do tiIndicator. |
| **Atualizar vários indicadores de inteligência de ameaça** | Atualize vários tiIndicators. Para se certificar de que passa as propriedades necessárias no seu pedido, consulte as [propriedades necessárias para a atualização de vários tiIndicadores](/graph/api/tiindicator-updatetiindicators). |
| **Eliminar indicador de inteligência de ameaça por ID** | Elimine um tiIndicator específico com base no iD do tiIndicator. |
| **Eliminar múltiplos indicadores de inteligência de ameaça por IDs** | Elimine vários tiIndicators pelos seus IDs. Para se certificar de que passa as propriedades necessárias no seu pedido, consulte as [propriedades necessárias para eliminar vários tiIndicadores por IDs](/graph/api/tiindicator-deletetiindicators). |
| **Eliminar múltiplos indicadores de inteligência de ameaça por IDs externos** | Elimine vários tiIndicators pelos IDs externos. Para se certificar de que passa as propriedades necessárias no seu pedido, consulte as [propriedades necessárias para eliminar vários tiIndicadores por IDs externos.](/graph/api/tiindicator-deletetiindicatorsbyexternalid) |
|||

## <a name="connector-reference"></a>Referência do conector

Para obter detalhes técnicos sobre gatilhos, ações e limites, descritos pela descrição openAPI (anteriormente Swagger) do conector, consulte a página de [referência](https://aka.ms/graphsecurityconnectorreference)do conector .

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre [outros conectores de Apps Lógicas](../connectors/apis-list.md)

