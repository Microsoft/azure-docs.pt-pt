---
title: Integrar e gerir operações de segurança e segurança do gráfico da Microsoft
description: Melhore a proteção, deteção e resposta de ameaças da sua aplicação com aplicações lógicas de segurança e azure do Microsoft Graph
services: logic-apps
ms.suite: integration
author: preetikr
ms.author: preetikr
ms.reviewer: v-ching, estfan, logicappspm
ms.topic: article
ms.date: 02/21/2020
tags: connectors
ms.openlocfilehash: b4f51b192d1a7c0ee14a769321793753e8217dea
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/25/2020
ms.locfileid: "77598838"
---
# <a name="improve-threat-protection-by-integrating-security-operations-with-microsoft-graph-security--azure-logic-apps"></a>Melhorar a proteção contra ameaças integrando operações de segurança com aplicações lógicas de segurança e azure do Microsoft Graph

Com [as Aplicações Lógicas Azure](../logic-apps/logic-apps-overview.md) e o conector [Microsoft Graph Security,](https://docs.microsoft.com/graph/security-concept-overview) pode melhorar a forma como a sua aplicação deteta, protege e responde a ameaças através da criação de fluxos de trabalho automatizados para integrar produtos, serviços e parceiros de segurança da Microsoft. Por exemplo, pode criar [playbooks do Azure Security Center](../security-center/security-center-playbooks.md) que monitorizam e gerem entidades de Segurança do Microsoft Graph, como alertas. Aqui estão alguns cenários que são suportados pelo conector microsoft Graph Security:

* Receba alertas com base em consultas ou por id de alerta. Por exemplo, você pode obter uma lista que inclui alertas de alta gravidade.

* Alertas de atualização. Por exemplo, pode atualizar as atribuições de alerta, adicionar comentários a alertas ou alertas de etiquetas.

* Monitorize quando os alertas são criados ou alterados através da criação de subscrições de [alerta (webhooks)](https://docs.microsoft.com/graph/api/resources/webhooks).

* Gerencie as suas assinaturas de alerta. Por exemplo, pode obter subscrições ativas, prolongar o tempo de validade para uma subscrição ou eliminar subscrições.

O fluxo de trabalho da sua aplicação lógica pode usar ações que obtêm respostas do conector Microsoft Graph Security e disponibilizar essa saída a outras ações no seu fluxo de trabalho. Também pode ter outras ações no seu fluxo de trabalho, utilizar a saída a partir das ações do conector Microsoft Graph Security. Por exemplo, se receber alertas de alta gravidade através do conector microsoft Graph Security, pode enviar esses alertas numa mensagem de e-mail utilizando o conector Outlook. 

Para saber mais sobre a Segurança do Microsoft Graph, consulte a visão geral da [Microsoft Graph Security API](https://aka.ms/graphsecuritydocs). Se é novo em aplicações lógicas, reveja [o que são as Aplicações Lógicas Azure?](../logic-apps/logic-apps-overview.md) Se procura o Microsoft Flow ou o PowerApps, veja [o que é Flow ou](https://flow.microsoft.com/) O que é [PowerApps?](https://powerapps.microsoft.com/)

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, [inscreva-se para obter uma conta do Azure gratuita](https://azure.microsoft.com/free/). 

* Para utilizar o conector microsoft graph security, deve ter *dado explicitamente* o consentimento do administrador do administrador do Azure Ative Directory (AD), que faz parte dos requisitos de [autenticação](https://aka.ms/graphsecurityauth)de segurança do Microsoft Graph Security . Este consentimento requer o ID e o nome do conector microsoft Graph Security, que também pode encontrar no [portal Azure:](https://portal.azure.com)

  | Propriedade | Valor |
  |----------|-------|
  | **Nome da candidatura** | `MicrosoftGraphSecurityConnector` |
  | **ID de aplicação** | `c4829704-0edc-4c3d-a347-7c4a67586f3c` |
  |||

  Para conceder o consentimento para o conector, o seu administrador de inquilino da AD Azure pode seguir qualquer uma destas etapas:

  * [Conceda o consentimento do administrador inquilino para os pedidos da Azure AD.](../active-directory/develop/v2-permissions-and-consent.md)

  * Durante a primeira execução da sua aplicação lógica, a sua aplicação pode solicitar o consentimento do seu administrador de inquilino da AD Azure através da experiência de consentimento da [aplicação](../active-directory/develop/application-consent-experience.md).
   
* Conhecimento básico sobre [como criar aplicações lógicas](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* A aplicação lógica onde pretende aceder às suas entidades de Segurança do Microsoft Graph, como alertas. Para utilizar um gatilho de Segurança do Microsoft Graph, precisa de uma aplicação lógica em branco. Para utilizar uma ação de Segurança do Microsoft Graph, precisa de uma aplicação lógica que comece com o gatilho apropriado para o seu cenário.

## <a name="connect-to-microsoft-graph-security"></a>Ligar à Segurança do Gráfico da Microsoft 

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Inscreva-se no [portal Azure](https://portal.azure.com/)e abra a sua aplicação lógica no Logic App Designer, se ainda não estiver aberta.

1. Para aplicações lógicas em branco, adicione o gatilho e quaisquer outras ações que desejar antes de adicionar uma ação de Segurança do Microsoft Graph.

   -ou-

   Para aplicações lógicas existentes, sob o último passo em que pretende adicionar uma ação de Segurança do Microsoft Graph, selecione **Novo passo**.

   -ou-

   Para adicionar uma ação entre passos, mova o ponteiro sobre a seta entre os degraus. Selecione o sinal de mais (+) que aparece e selecione **Adicionar uma ação**.

1. Na caixa de pesquisa, introduza "microsoft graph security" como filtro. Na lista de ações, selecione a ação que deseja.

1. Inscreva-se com as suas credenciais de Segurança do Microsoft Graph.

1. Forneça os detalhes necessários para a sua ação selecionada e continue a construir o fluxo de trabalho da sua aplicação lógica.

## <a name="add-triggers"></a>Adicionar gatilhos

Nas Aplicações Lógicas Do Azure, todas as aplicações lógicas devem começar com um [gatilho](../logic-apps/logic-apps-overview.md#logic-app-concepts), que dispara quando um evento específico acontece ou quando uma condição específica é satisfeita. Cada vez que o gatilho dispara, o motor Logic Apps cria uma instância de aplicação lógica e começa a executar o fluxo de trabalho da sua aplicação.

> [!NOTE] 
> Quando um gatilho dispara, o gatilho processa todos os novos alertas. Se não forem recebidos alertas, o gatilho é ignorado. A próxima sondagem de gatilho acontece com base no intervalo de recorrência que especifica nas propriedades do gatilho.

Este exemplo mostra como pode iniciar um fluxo de trabalho de aplicações lógicas quando novos alertas são enviados para a sua aplicação.

1.  No portal Azure ou Estúdio Visual, crie uma aplicação lógica em branco, que abre o Logic App Designer. Este exemplo utiliza o portal Azure.

1.  No designer, na caixa de pesquisa, introduza "microsoft graph security" como filtro. A partir da lista de gatilhos, selecione este gatilho: **Em todos os novos alertas**

1.  No gatilho, forneça informações sobre os alertas que pretende monitorizar. Para mais propriedades, abra a **lista de novos parâmetros** e selecione um parâmetro para adicionar essa propriedade ao gatilho.

   | Propriedade | Propriedade (JSON) | Necessário | Tipo | Descrição |
   |----------|-----------------|----------|------|-------------|
   | **Intervalo** | `interval` | Sim | Número inteiro | Um inteiro positivo que descreve a frequência com que o fluxo de trabalho funciona com base na frequência. Aqui estão os intervalos mínimos e máximos: <p><p>- Mês: 1-16 meses <br>- Dia: 1-500 dias <br>- Hora: 1-12.000 horas <br>- Minuto: 1-72.000 minutos <br>- Segundo: 1-9.999,999 segundos <p>Por exemplo, se o intervalo for 6, e a frequência for "Mês", então a recorrência é a cada 6 meses. |
   | **Frequência** | `frequency` | Sim | String | A unidade de tempo para a recorrência: **Segundo,** **Minuto,** **Hora,** **Dia,** **Semana,** ou **Mês** |
   | **Time zone** (Fuso horário) | `timeZone` | Não | String | Aplica-se apenas quando especifica um tempo de início porque este gatilho não aceita [compensação UTC](https://en.wikipedia.org/wiki/UTC_offset). Selecione o fuso horário que pretende aplicar. |
   | **Start time** (Hora de início) | `startTime` | Não | String | Forneça uma data e hora de início neste formato: <p><p>YYYY-MM-DDThh:mm:ss se selecionar um fuso horário <p>-ou- <p>YYYY-MM-DDThh:mm:ssZ se não selecionar um fuso horário <p>Por exemplo, se quiser 18 de setembro de 2017 às 14:00, especifique "2017-09-18T14:00:00" e selecione um fuso horário como o Tempo Padrão do Pacífico. Ou, especificar "2017-09-18T14:00:00Z" sem fuso horário. <p>**Nota:** Esta hora de início tem um máximo de 49 anos no futuro e deve seguir a especificação de [data ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations) no [formato de data utc,](https://en.wikipedia.org/wiki/Coordinated_Universal_Time)mas sem [uma compensação UTC](https://en.wikipedia.org/wiki/UTC_offset). Se não selecionar um fuso horário, deve adicionar a letra "Z" no final sem espaços. Este "Z" refere-se ao [tempo náutico](https://en.wikipedia.org/wiki/Nautical_time)equivalente . <p>Para horários simples, a hora de início é a primeira ocorrência, enquanto para horários complexos, o gatilho não dispara tão cedo quanto a hora de início. [*Quais são as formas de usar a data e a hora de início?* ](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#start-time) |
   ||||||

1.  Quando terminar, na barra de ferramentas de design, selecione **Save**.

1.  Agora continue a adicionar uma ou mais ações à sua aplicação lógica para as tarefas que pretende executar com os resultados do gatilho.

## <a name="add-actions"></a>Adicionar ações

Aqui estão mais detalhes específicos sobre a utilização das várias ações disponíveis com o conector Microsoft Graph Security.

### <a name="manage-alerts"></a>Gerir alertas

Para filtrar, classificar ou obter os resultados mais recentes, fornecer *apenas* os parâmetros de [consulta ODATA suportados pelo Microsoft Graph](https://docs.microsoft.com/graph/query-parameters). *Não especifique* o URL base completo ou a ação HTTP, por exemplo, `https://graph.microsoft.com/v1.0/security/alerts`, ou a `GET` ou `PATCH` operação. Aqui está um exemplo específico que mostra os parâmetros para uma ação **de alertas Get** quando você quer uma lista com alertas de alta gravidade:

`Filter alerts value as Severity eq 'high'`

Para obter mais informações sobre as consultas que pode utilizar com este conector, consulte o [Microsoft Graph Security alerta a documentação de referência](https://docs.microsoft.com/graph/api/alert-list). Para construir experiências melhoradas com este conector, saiba mais sobre as [propriedades do esquema alerta](https://docs.microsoft.com/graph/api/resources/alert) que o conector suporta.

| Ação | Descrição |
|--------|-------------|
| **Receba alertas** | Receba alertas filtrados com base numa ou mais propriedades de [alerta,](https://docs.microsoft.com/graph/api/resources/alert)por exemplo, `Provider eq 'Azure Security Center' or 'Palo Alto Networks'`. | 
| **Receba alerta por ID** | Receba um alerta específico com base na identificação do alerta. | 
| **Alerta de atualização** | Atualize um alerta específico com base no ID de alerta. Para se certificar de que passa as propriedades necessárias e editáveis a seu pedido, consulte as [propriedades editáveis para alertas](https://docs.microsoft.com/graph/api/alert-update). Por exemplo, para atribuir um alerta a um analista de segurança para que possam investigar, pode atualizar o alerta **atribuído à** propriedade. |
|||

### <a name="manage-alert-subscriptions"></a>Gerir subscrições de alerta

O Microsoft Graph suporta [*subscrições*](https://docs.microsoft.com/graph/api/resources/subscription), ou [*webhooks.* ](https://docs.microsoft.com/graph/api/resources/webhooks) Para obter, atualizar ou eliminar subscrições, fornecer os parâmetros de [consulta ODATA suportados pelo Microsoft Graph](https://docs.microsoft.com/graph/query-parameters) para a construção da entidade do Microsoft Graph e incluem `security/alerts` seguido seguem-se a consulta ODATA. *Não inclua* o URL base, por exemplo, `https://graph.microsoft.com/v1.0`. Em vez disso, utilize o formato neste exemplo:

`security/alerts?$filter=status eq 'New'`

| Ação | Descrição |
|--------|-------------|
| **Criar subscrições** | [Crie uma subscrição](https://docs.microsoft.com/graph/api/subscription-post-subscriptions) que o notifique sobre quaisquer alterações. Pode filtrar esta subscrição para os tipos de alerta específicos que pretende. Por exemplo, pode criar uma subscrição que o notifique sobre alertas de alta gravidade. |
| **Obtenha subscrições ativas** | [Obtenha subscrições não expiradas.](https://docs.microsoft.com/graph/api/subscription-list) | 
| **Atualizar subscrição** | [Atualize uma subscrição](https://docs.microsoft.com/graph/api/subscription-update) fornecendo o ID de subscrição. Por exemplo, para estender a sua subscrição, pode atualizar a propriedade `expirationDateTime` da subscrição. | 
| **Excluir subscrição** | [Eliminar uma subscrição](https://docs.microsoft.com/graph/api/subscription-delete) fornecendo o ID de subscrição. | 
||| 

### <a name="manage-threat-intelligence-indicators"></a>Gerir indicadores de inteligência de ameaças

Para filtrar, classificar ou obter os resultados mais recentes, fornecer *apenas* os parâmetros de [consulta ODATA suportados pelo Microsoft Graph](https://docs.microsoft.com/graph/query-parameters). *Não especifique* o URL base completo ou a ação HTTP, por exemplo, `https://graph.microsoft.com/beta/security/tiIndicators`, ou a `GET` ou `PATCH` operação. Aqui está um exemplo específico que mostra os parâmetros para uma ação **Get tiIndicators** quando você quer uma lista que tem o tipo de ameaça `DDoS`:

`Filter threat intelligence indicator value as threatType eq 'DDoS'`

Para obter mais informações sobre as consultas que pode utilizar com este conector, consulte ["Opcional Query Parameters" na documentação](https://docs.microsoft.com/graph/api/tiindicators-list?view=graph-rest-beta&tabs=http)de referência do indicador de referência de ameaças da Microsoft Graph Security . Para construir experiências melhoradas com este conector, saiba mais sobre o indicador de inteligência de ameaça de [propriedades de esquemas](https://docs.microsoft.com/graph/api/resources/tiindicator?view=graph-rest-beta) que o conector suporta.

| Ação | Descrição |
|--------|-------------|
| **Obtenha indicadores de inteligência de ameaça** | Obtenha os tiIndicators filtrados com base em uma ou mais [propriedades tiIndicator](https://docs.microsoft.com/graph/api/resources/tiindicator?view=graph-rest-beta), por exemplo, `threatType eq 'MaliciousUrl' or 'DDoS'` |
| **Obtenha indicador de inteligência de ameaça por ID** | Obtenha um tiIndicator específico com base no ID do tiIndicator. | 
| **Criar indicador de inteligência de ameaça** | Crie um novo tiIndicator colocando na coleção tiIndicators. Para se certificar de que passa as propriedades necessárias a seu pedido, consulte as [propriedades necessárias para criar o tiIndicator](https://docs.microsoft.com/graph/api/tiindicators-post?view=graph-rest-beta&tabs=http). |
| **Submeta vários indicadores de inteligência de ameaça** | Crie vários novos tiIndicators publicando uma coleção de tiIndicators. Para se certificar de que passa as propriedades necessárias a seu pedido, consulte as propriedades necessárias para a apresentação de [vários tiIndicators](https://docs.microsoft.com/graph/api/tiindicator-submittiindicators?view=graph-rest-beta&tabs=http). |
| **Atualizar indicador de inteligência de ameaça** | Atualize um tiIndicator específico com base no ID do tiIndicator. Para se certificar de que passa as propriedades necessárias e editáveis a seu pedido, consulte as [propriedades editáveis para tiIndicator](https://docs.microsoft.com/graph/api/tiindicator-update?view=graph-rest-beta&tabs=http). Por exemplo, para atualizar a ação a aplicar se o indicador for compatível com a ferramenta de segurança targetProduct, pode atualizar a propriedade de **ação** do tiIndicator. |
| **Atualizar vários indicadores de inteligência de ameaça** | Atualizar vários tiIndicators. Para se certificar de que passa as propriedades necessárias a seu pedido, consulte as [propriedades necessárias para atualizar vários tiIndicators](https://docs.microsoft.com/graph/api/tiindicator-updatetiindicators?view=graph-rest-beta&tabs=http). |
| **Eliminar indicador de inteligência de ameaça por ID** | Elimine um tiIndicator específico com base no ID do indicador de ti. |
| **Eliminar vários indicadores de inteligência de ameaças por IDs** | Eliminar vários tiIndicators pelos seus IDs. Para se certificar de que passa as propriedades necessárias a seu pedido, consulte as [propriedades necessárias para apagar vários tiIndicators por IDs](https://docs.microsoft.com/graph/api/tiindicator-deletetiindicators?view=graph-rest-beta&tabs=http). |
| **Eliminar múltiplos indicadores de inteligência de ameaças por IDs externos** | Eliminar vários tiIndicators pelos IDs externos. Para se certificar de que passa as propriedades necessárias a seu pedido, consulte as [propriedades necessárias para apagar vários tiIndicators por IDs externos](https://docs.microsoft.com/graph/api/tiindicator-deletetiindicatorsbyexternalid?view=graph-rest-beta&tabs=http). |
|||

## <a name="connector-reference"></a>Referência do conector

Para detalhes técnicos sobre gatilhos, ações e limites, descritos pela descrição OpenAPI (ex-Swagger) do conector, reveja a página de [referência](https://aka.ms/graphsecurityconnectorreference)do conector .

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre outros [conectores de Aplicações Lógicas](../connectors/apis-list.md)
