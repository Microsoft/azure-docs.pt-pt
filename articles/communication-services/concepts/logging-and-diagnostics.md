---
title: Registos de Serviços de Comunicação
titleSuffix: An Azure Communication Services concept document
description: Saiba mais sobre o login nos Serviços de Comunicação Azure
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 03/10/2021
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 5ed75a7f8c7915645778696282e179af3a4d4091
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/16/2021
ms.locfileid: "103493599"
---
# <a name="communication-services-logs"></a>Registos de serviços de comunicação

A Azure Communication Services oferece capacidades de registo que pode utilizar para monitorizar e depurar a sua solução de Serviços de Comunicação. Estas capacidades podem ser configuradas através do portal Azure.

## <a name="enable-diagnostic-logs-in-your-resource"></a>Ativar registos de diagnóstico no seu recurso

A sessão é desligada por padrão quando um recurso é criado. Para ativar o registo, navegue na lâmina de **definições de Diagnóstico** no menu de recursos sob a secção **de Monitorização.** Em seguida, clique na **definição de diagnóstico de adicionar**.

Em seguida, selecione o alvo de arquivo que deseja. Atualmente, apoiamos as contas de armazenamento e o Log Analytics como alvos de arquivo. Depois de selecionar os tipos de registos que pretende capturar, guarde as definições de diagnóstico.
 
As novas definições fazem efeito em cerca de dez minutos. Os registos começarão a aparecer no alvo de arquivo configurado dentro do painel de registos do seu recurso de Serviços de Comunicação.

:::image type="content" source="./media/diagnostic-settings.png" alt-text="Opções de Definições de Diagnóstico ACS.":::

Para obter mais informações sobre a configuração dos diagnósticos, consulte a visão geral dos registos de recursos do [Azure](../../azure-monitor/essentials/platform-logs-overview.md).

## <a name="resource-log-categories"></a>Categorias de registo de recursos

Os Serviços de Comunicação oferecem três tipos de registos que pode ativar:

* **Registos de utilização** - fornece dados de utilização associados a cada oferta de serviço faturado
* **Registos operacionais de chat** - fornece informações básicas relacionadas com o serviço de chat
* **Registos operacionais SMS** - fornece informações básicas relacionadas com o serviço SMS
* **Registos operacionais de autenticação** - fornece informações básicas relacionadas com o serviço de Autenticação

### <a name="usage-logs-schema"></a>Esquema de registos de utilização

| Propriedade | Descrição |
| -------- | ---------------|
| CarimboDeDataEHora | A hora de tempo (UTC) de quando o log foi gerado. |
| Nome da Operação | A operação associada ao registo de registo. |
| Versão de operação | O `api-version` produto associado à operação, se a operaçãoName foi realizada com uma API. Se não houver API que corresponda a esta operação, a versão representa a versão dessa operação no caso de as propriedades associadas à alteração da operação no futuro. |
| Categoria | A categoria de registo do evento. Categoria é a granularidade em que pode ativar ou desativar registos num determinado recurso. As propriedades que aparecem dentro da bolha de propriedades de um evento são as mesmas dentro de uma determinada categoria de registo e tipo de recurso. |
| ID de Correlação | A identificação para eventos correlacionados. Pode ser usado para identificar eventos correlacionados entre várias tabelas. |
| Propriedades | Outros dados aplicáveis a vários modos de Serviços de Comunicação. |
| ID de registo | A identificação única para um dado registo de utilização. |
| Tipo de Utilização | O modo de utilização. (por exemplo, Chat, PSTN, NAT, etc.) |
| Tipo de Unidade | O tipo de unidade que utiliza é baseado num determinado modo de utilização. (por exemplo, minutos, megabytes, mensagens, etc.). |
| Quantidade | O número de unidades utilizadas ou consumidas para este registo. |

### <a name="chat-operational-logs"></a>Registos operacionais de chat

| Propriedade | Descrição |
| -------- | ---------------|
| TimeGenerated | A hora de tempo (UTC) de quando o log foi gerado. |
| OperationName | A operação associada ao registo de registo. |
| CorrelationID | A identificação para eventos correlacionados. Pode ser usado para identificar eventos correlacionados entre várias tabelas. |
| OperationVersion | A versão api associada à operação, se a operaçãoName foi realizada com uma API. Se não houver API que corresponda a esta operação, a versão representa a versão dessa operação no caso de as propriedades associadas à alteração da operação no futuro. |
| Categoria | A categoria de registo do evento. Categoria é a granularidade em que pode ativar ou desativar registos num determinado recurso. As propriedades que aparecem dentro da bolha de propriedades de um evento são as mesmas dentro de uma determinada categoria de registo e tipo de recurso. |
| ResultType | O estado da operação. |
| ResultSignature | O sub-estado da operação. Se esta operação corresponder a uma chamada de API REST, este campo é o código de estado HTTP da chamada REST correspondente. |
| ResultDescription | A descrição estática do texto desta operação. |
| DurationMs | A duração da operação em milissegundos. |
| CallerIpAddress | O endereço IP do chamador, se a operação corresponder a uma chamada de API que viria de uma entidade com um endereço IP disponível ao público. |
| Level | O nível de gravidade do evento. |
| URI | O URI do pedido. |
| IDUtilizador | Identificação do utilizador do remetente de pedido. |
| ChatThreadId | O ID do fio de chat associado ao pedido. |
| ChatMessageId | O ID da mensagem de chat associado ao pedido. |
| SdkType | O tipo Sdk usado no pedido. |
| PlatformType | O tipo de plataforma usado no pedido. |

### <a name="sms-operational-logs"></a>Registos operacionais de SMS

| Propriedade | Descrição |
| -------- | ---------------|
| TimeGenerated | A hora de tempo (UTC) de quando o log foi gerado. |
| OperationName | A operação associada ao registo de registo. |
| CorrelationID | A identificação para eventos correlacionados. Pode ser usado para identificar eventos correlacionados entre várias tabelas. |
| OperationVersion | A versão api associada à operação, se a operaçãoName foi realizada com uma API. Se não houver API que corresponda a esta operação, a versão representa a versão dessa operação no caso de as propriedades associadas à alteração da operação no futuro. |
| Categoria | A categoria de registo do evento. Categoria é a granularidade em que pode ativar ou desativar registos num determinado recurso. As propriedades que aparecem dentro da bolha de propriedades de um evento são as mesmas dentro de uma determinada categoria de registo e tipo de recurso. |
| ResultType | O estado da operação. |
| ResultSignature | O sub-estado da operação. Se esta operação corresponder a uma chamada de API REST, este campo é o código de estado HTTP da chamada REST correspondente. |
| ResultDescription | A descrição estática do texto desta operação. |
| DurationMs | A duração da operação em milissegundos. |
| CallerIpAddress | O endereço IP do chamador, se a operação corresponder a uma chamada de API que viria de uma entidade com um endereço IP disponível ao público. |
| Level | O nível de gravidade do evento. |
| URI | O URI do pedido. |
| OutgoingMessageLength | O número de caracteres na mensagem de saída. |
| IncomingMessageLength | O número de caracteres na mensagem de entrada. |
| EntregaAptas | O número de tentativas feitas para entregar esta mensagem. |
| PhoneNumber | O número de telefone para o qual a mensagem SMS está a ser enviada. |
| SdkType | O tipo SDK usado no pedido. |
| PlatformType | O tipo de plataforma usado no pedido. |
| Método | O método utilizado no pedido. |

### <a name="authentication-operational-logs"></a>Registos operacionais de autenticação

| Propriedade | Descrição |
| -------- | ---------------|
| TimeGenerated | A hora de tempo (UTC) de quando o log foi gerado. |
| OperationName | A operação associada ao registo de registo. |
| CorrelationID | A identificação para eventos correlacionados. Pode ser usado para identificar eventos correlacionados entre várias tabelas. |
| OperationVersion | O `api-version` associado à operação, se o `operationName` for realizado com uma API. Se não houver API que corresponda a esta operação, a versão representa a versão dessa operação no caso de as propriedades associadas à alteração da operação no futuro. |
| Categoria | A categoria de registo do evento. Categoria é a granularidade em que pode ativar ou desativar registos num determinado recurso. As propriedades que aparecem dentro da bolha de propriedades de um evento são as mesmas dentro de uma determinada categoria de registo e tipo de recurso. |
| ResultType | O estado da operação. |
| ResultSignature | O sub-estado da operação. Se esta operação corresponder a uma chamada de API REST, este campo é o código de estado HTTP da chamada REST correspondente. |
| DurationMs | A duração da operação em milissegundos. |
| CallerIpAddress | O endereço IP do chamador, se a operação corresponder a uma chamada de API que viria de uma entidade com um endereço IP disponível ao público. |
| Level | O nível de gravidade do evento. |
| URI | O URI do pedido. |
| SdkType | O tipo SDK usado no pedido. |
| PlatformType | O tipo de plataforma usado no pedido. |
| Identidade | A identidade dos Serviços de Comunicação relacionado com a operação. |
| Âmbitos | Os serviços de comunicação apresentam-se no token de acesso. |
