---
title: Esquema de alerta comum para alertas do Monitor Azure
description: Compreender o esquema de alerta comum, por que deve usá-lo e como habilitá-lo
ms.topic: conceptual
ms.subservice: alerts
ms.date: 03/14/2019
ms.openlocfilehash: 70e37f8f1c9391eee5901f611bae579efe1cd9b9
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101737295"
---
# <a name="common-alert-schema"></a>Esquema de alertas comuns

Este artigo descreve o que é o esquema comum de alerta, os benefícios de usá-lo e como capacitá-lo.

## <a name="what-is-the-common-alert-schema"></a>Qual é o esquema de alerta comum?

O esquema de alerta comum normaliza a experiência de consumo para notificações de alerta hoje em Azure. Historicamente, os três tipos de alerta em Azure hoje em dia (métrica, log e log de atividade) tiveram os seus próprios modelos de e-mail, esquemas webhook, etc. Com o esquema de alerta comum, pode agora receber notificações de alerta com um esquema consistente.

Qualquer instância de alerta descreve **o recurso que foi afetado** e a causa do **alerta,** e estas instâncias são descritas no esquema comum nas seguintes secções:
* **Essenciais**: Um conjunto de **campos padronizados,** comuns em todos os tipos de alerta, que descrevem **em que recurso** o alerta está ligado juntamente com metadados de alerta comuns adicionais (por exemplo, gravidade ou descrição). 
* **Contexto de alerta**: Um conjunto de campos que descrevem a **causa do alerta,** com campos que variam **em função do tipo de alerta.** Por exemplo, um alerta métrico teria campos como o nome métrico e o valor métrico no contexto de alerta, enquanto um alerta de registo de atividade teria informações sobre o evento que gerou o alerta. 

Os cenários típicos de integração que ouvimos dos clientes envolvem o encaminhamento do caso de alerta para a equipa em causa com base em algum pivô (por exemplo, grupo de recursos), após o qual a equipa responsável começa a trabalhar nele. Com o esquema de alerta comum, você pode ter uma lógica de encaminhamento padronizada através de tipos de alerta, aproveitando os campos essenciais, deixando os campos de contexto como é para as equipas em causa investigarem mais.

Isto significa que pode potencialmente ter menos integrações, tornando o processo de gestão e manutenção uma tarefa _muito_ mais simples. Além disso, futuros enriquecimentos de carga útil de alerta (por exemplo, personalização, enriquecimento de diagnóstico, etc.) só surgirão no esquema comum.

## <a name="what-enhancements-does-the-common-alert-schema-bring"></a>Que melhorias traz o esquema de alerta comum?

O esquema de alerta comum manifestar-se-á principalmente nas suas notificações de alerta. As melhorias que verá estão listadas abaixo:

| Ação | Melhoramentos |
|:---|:---|
| SMS | Um modelo DE SMS consistente para todos os tipos de alerta. |
| E-mail | Um modelo de e-mail consistente e detalhado, permitindo-lhe diagnosticar facilmente problemas num ápice. As ligações profundas incorporadas à instância de alerta no portal e ao recurso afetado garantem que pode entrar rapidamente no processo de remediação. |
| Webhook/Logic App/Azure Function/Automation Runbook | Uma estrutura JSON consistente para todos os tipos de alerta, que permite construir facilmente integrações entre os diferentes tipos de alerta. |

O novo esquema também permitirá uma experiência de consumo de alerta mais rica, tanto no portal Azure como na aplicação móvel Azure, num futuro imediato. 

[Saiba mais sobre as definições de esquema para Webhooks/Logic Apps/Azure Functions/Automation Runbooks.](./alerts-common-schema-definitions.md)

> [!NOTE]
> As seguintes ações não suportam o esquema comum de alerta: CONECTOR ITSM.

## <a name="how-do-i-enable-the-common-alert-schema"></a>Como posso permitir o esquema de alerta comum?

Pode optar ou optar pelo esquema de alerta comum através de Grupos de Ação, tanto no portal como através da API REST. O alternador para mudar para o novo esquema existe a um nível de ação. Por exemplo, você tem que optar separadamente por uma ação de e-mail e uma ação webhook.

> [!NOTE]
> 1. Os seguintes tipos de alerta suportam o esquema comum por predefinição (não optar pelo necessário):
>     * Alertas de deteção inteligente
> 1. Atualmente, os seguintes tipos de alerta não suportam o esquema comum:
>     * Alertas gerados por [insights VM](../vm/vminsights-overview.md)
>     * Alertas gerados pela [Azure Cost Management](../../cost-management-billing/manage/cost-management-budget-scenario.md)

### <a name="through-the-azure-portal"></a>Através do portal Azure

![Esquema de alerta comum opte em](media/alerts-common-schema/portal-opt-in.png)

1. Abra qualquer ação existente ou nova num grupo de ação. 
1. Selecione 'Sim' para o alternar para ativar o esquema de alerta comum, como mostrado.

### <a name="through-the-action-groups-rest-api"></a>Através dos Grupos de Ação REST API

Também pode utilizar a [API dos Grupos de Ação](/rest/api/monitor/actiongroups) para optar pelo esquema de alerta comum. Ao fazer a chamada de API de [criação ou atualização,](/rest/api/monitor/actiongroups/createorupdate) pode definir a bandeira "useCommonAlertSchema" para 'true' (para optar) ou 'falso' (para excluir) para qualquer uma das seguintes ações - e-mail/webhook/logic app/Azure Function/automation runbook.

Por exemplo, o seguinte órgão de pedidos feito à [API de criação ou atualização](/rest/api/monitor/actiongroups/createorupdate) de REST fará o seguinte:

* Ativar o esquema de alerta comum para a ação de e-mail "E-mail de João Ninguém"
* Desative o esquema de alerta comum para a ação de e-mail "E-mail de Jane Smith"
* Ativar o esquema de alerta comum para a ação webhook "Sample webhook"

```json
{
  "properties": {
    "groupShortName": "sample",
    "enabled": true,
    "emailReceivers": [
      {
        "name": "John Doe's email",
        "emailAddress": "johndoe@email.com",
        "useCommonAlertSchema": true
      },
      {
        "name": "Jane Smith's email",
        "emailAddress": "janesmith@email.com",
        "useCommonAlertSchema": false
      }
    ],
    "smsReceivers": [
      {
        "name": "John Doe's mobile",
        "countryCode": "1",
        "phoneNumber": "1234567890"
      },
      {
        "name": "Jane Smith's mobile",
        "countryCode": "1",
        "phoneNumber": "0987654321"
      }
    ],
    "webhookReceivers": [
      {
        "name": "Sample webhook",
        "serviceUri": "http://www.example.com/webhook",
        "useCommonAlertSchema": true
      }
    ]
  },
  "location": "Global",
  "tags": {}
}
```





## <a name="next-steps"></a>Passos seguintes

- [Definições comuns de esquema de alerta para Webhooks/Logic Apps/Azure Functions/Automation Runbooks.](./alerts-common-schema-definitions.md)
- [Saiba como criar uma aplicação lógica que aproveite o esquema de alerta comum para lidar com todos os seus alertas.](./alerts-common-schema-integrations.md)