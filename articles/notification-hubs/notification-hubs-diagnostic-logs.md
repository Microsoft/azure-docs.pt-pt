---
title: Azure Notification Hubs diagnostica registos | Microsoft Docs
description: Este artigo fornece uma visão geral de todos os registos operacionais e de diagnóstico que estão disponíveis para os Hubs de Notificação Azure.
author: brannon
ms.author: brjones
ms.service: notification-hubs
ms.topic: article
ms.date: 01/29/2021
ms.openlocfilehash: b532dca6ceba44a32132bf64b322e1b4764fd5fa
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/14/2021
ms.locfileid: "100418144"
---
# <a name="enable-diagnostics-logs-for-notification-hubs"></a>Ativar registos de diagnóstico para centros de notificação

Quando começar a utilizar o espaço de nomes Azure Notification Hubs, poderá querer monitorizar como e quando o seu espaço de nome é criado, eliminado ou acedido. Este artigo fornece uma visão geral de todos os registos operacionais e de diagnóstico que estão disponíveis.

A Azure Notification Hubs atualmente suporta atividade e registos operacionais, que capturam operações de *gestão* que são realizadas no espaço de nomes Azure Notification Hubs.

## <a name="diagnostic-logs-schema"></a>Esquema de registos de diagnóstico

Todos os registos são armazenados no formato JavaScript Object Notation (JSON) nos dois locais seguintes:

- **AzureActivity**: Exibe registos de operações e ações que são conduzidas contra o seu espaço de nome no portal Azure ou através de implementações de modelos do Azure Resource Manager.
- **AzureDiagnostics**: Exibe registos de operações e ações que são conduzidas contra o seu espaço de nome utilizando a API, ou através de clientes de gestão na língua SDK.

As cordas JSON do registo de diagnóstico incluem os elementos listados na tabela seguinte:

| Nome | Descrição |
| ------- | ------- |
| hora | Tempo de tempo UTC do log |
| resourceId | Caminho relativo para o recurso Azure |
| operationName | Nome da operação de gestão |
| categoria | Categoria de registo. Valores válidos: `OperationalLogs` |
| entidade callerId | Identidade do chamador que iniciou a operação de gestão |
| resultType | Estado da operação de gestão. Valores válidos: `Succeeded` ou `Failed` |
| resultDescription | Descrição da operação de gestão |
| correlationId | ID de correlação da operação de gestão (se especificado) |
| callerIpAddress | O endereço IP do chamador. Vazio para chamadas originárias do portal Azure |

Aqui está um exemplo de uma cadeia de log JSON operacional:

```json
{
    "operationName": "Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/action",
    "resourceId": "/SUBSCRIPTIONS/2CAC2A14-BA6B-46A6-BCE8-2D9781A41BA2/RESOURCEGROUPS/SAMPLES/PROVIDERS/MICROSOFT.NOTIFICATIONHUBS/NAMESPACES/SAMPLE-NS",
    "time": "1/1/2021 5:16:32 AM +00:00",
    "category": "OperationalLogs",
    "resultType": "Succeeded",
    "resultDescription": "Gets Hub Authorization Rules",
    "correlationId": "00000000-0000-0000-0000-000000000000",
    "callerIdentity": "{ \"identityType\": \"Portal\", \"identity\": \"\" }"
}
```

O `callerIdentity` campo pode estar vazio, ou uma corda JSON com um dos seguintes formatos.

Para chamadas originárias do portal Azure, o `identity` campo está vazio. O registo pode ser correlacionado com registos de atividade para determinar o sessão registado no utilizador.
```json
{
    "identityType": "Portal",
    "identity": ""
}
```

Para chamadas es feitas através do Azure Resource Manager, o `identity` campo conterá o nome de utilizador do utilizador registado no sessão.
```json
{
   "identityType": "Username",
   "identity": "test@foo.com"
}
```

Para chamadas para os Centros de Notificação REST API o `identity` campo conterá o nome da política de acesso utilizada para gerar o token SharedAccessSignature.
```json
{
   "identityType": "KeyName",
   "identity": "SharedAccessRootKey2"
}
```

## <a name="events-and-operations-captured-in-operational-logs"></a>Eventos e operações capturados em registos operacionais

Os registos operacionais captam todas as operações de gestão que são realizadas no espaço de nomes Azure Notification Hubs. As operações de dados não são capturadas, devido ao elevado volume de operações de dados que são realizadas nos Hubs de Notificação Azure.

As seguintes operações de gestão são capturadas em registos operacionais: 

| Âmbito | Nome da Operação | Descrição da operação |
| :-- | :-- | :-- |
| Espaço de Nomes | Microsoft.NotificationHubs/Namespaces/authorizationRules/action | Regras de Autorização de Lista |
| Espaço de Nomes | Microsoft.NotificationHubs/Namespaces/authorizationRules/delete | Eliminar regra de autorização |
| Espaço de Nomes | Microsoft.NotificationHubs/Namespaces/authorizationRules/listkeys/action | Chaves da lista |
| Espaço de Nomes | Microsoft.NotificationHubs/Namespaces/authorizationRules/read | Obtenha a Regra de Autorização |
| Espaço de Nomes | Microsoft.NotificationHubs/Namespaces/authorizationRules/regenerateKeys/action | Chaves Regeneração |
| Espaço de Nomes | Microsoft.NotificationHubs/Namespaces/authorizationRules/write | Criar ou atualizar regra de autorização |
| Espaço de Nomes | Microsoft.NotificationHubs/Namespaces/delete | Excluir espaço de nomes |
| Espaço de Nomes | Microsoft.NotificationHubs/Namespaces/read | Obtenha espaço para nomes |
| Espaço de Nomes | Microsoft.NotificationHubs/Namespaces/write | Criar ou atualizar o espaço de nomes |
| Hub de Notificação | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/action | Regras de Autorização de Lista |
| Hub de Notificação | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/delete | Eliminar regra de autorização |
| Hub de Notificação | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/listkeys/action | Chaves da lista |
| Hub de Notificação | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/read | Ler regra de autorização |
| Hub de Notificação | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/regenerateKeys/action | Chaves Regeneração |
| Hub de Notificação | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/write | Criar ou atualizar regra de autorização |
| Hub de Notificação | Microsoft.NotificationHubs/Namespaces/NotificationHubs/delete | Eliminar o Centro de Notificação |
| Hub de Notificação | Microsoft.NotificationHubs/Namespaces/NotificationHubs/pnsCredentials/action | Criar, atualizar ou obter credenciais de PNS |
| Hub de Notificação | Microsoft.NotificationHubs/Namespaces/NotificationHubs/read | Obter Centro de Notificação |
| Hub de Notificação | Microsoft.NotificationHubs/Namespaces/NotificationHubs/write | Criar ou atualizar o centro de notificação |

## <a name="enable-operational-logs"></a>Ativar registos operacionais

Os registos operacionais são desativado por predefinição. Para ativar os registos, faça o seguinte:

1. No [portal Azure,](https://portal.azure.com)aceda ao espaço de nomes Azure Notification Hubs e, em seguida, em **Monitorização,** selecione  **Definições de Diagnóstico**.

   ![A ligação "Definições de diagnóstico"](./media/notification-hubs-diagnostic-logs/image-1.png)

1. No painel de **definições de Diagnóstico,** selecione **Adicionar a definição de diagnóstico**.  

   ![A ligação "Adicionar definição de diagnóstico"](./media/notification-hubs-diagnostic-logs/image-2.png)

1. Configure as definições de diagnóstico fazendo o seguinte:

   a. Na caixa **Nome,** insira um nome para as definições de diagnóstico.  

   b. Selecione um dos seguintes três destinos para os seus registos de diagnóstico:  
   - Se selecionar **Enviar para log analytics espaço de trabalho,** tem de especificar para que instância de Log Analytics os diagnósticos serão enviados.  
   - Se selecionar **o Archive para uma conta de armazenamento,** tem de configurar a conta de armazenamento onde os registos de diagnóstico serão armazenados.  
   - Se selecionar **o Stream para um centro** de eventos, tem de configurar o centro de eventos para o quais pretende transmitir os registos de diagnóstico.

   c. Selecione a caixa de verificação **'Registos Operacionais'.**

    ![O painel "Definições de Diagnóstico"](./media/notification-hubs-diagnostic-logs/image-3.png)

1. Selecione **Guardar**.

As novas definições fazem efeito em cerca de 10 minutos. Os registos são apresentados no alvo de arquivo configurado, no painel de **registos de diagnóstico.**

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre a configuração das definições de diagnóstico, consulte:
* [Visão geral dos registos de diagnósticos do Azure](../azure-monitor/platform/platform-logs-overview.md).

Para saber mais sobre os Hubs de Notificação Azure, consulte:
* [O que são os Hubs de Notificação do Azure?](notification-hubs-push-notification-overview.md)

