---
title: Esquema de alerta comum para alertas de monitor de Azure
description: Compreender o esquema de alerta comum, por que deve usá-lo e como capacitá-lo
ms.topic: conceptual
ms.subservice: alerts
ms.date: 03/14/2019
ms.openlocfilehash: 1445e8cf38b2694146fc8749ba5e77f2297de969
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79249051"
---
# <a name="common-alert-schema"></a>Esquema de alertas comuns

Este artigo descreve o que é o esquema de alerta comum, os benefícios de usá-lo e como permiti-lo.

## <a name="what-is-the-common-alert-schema"></a>Qual é o esquema de alerta comum?

O esquema de alerta comum normaliza a experiência de consumo para notificações de alerta em Azure hoje. Historicamente, os três tipos de alerta em Azure hoje (registo métrico, diário e de atividade) tiveram os seus próprios modelos de e-mail, schemas webhook, etc. Com o esquema de alerta comum, pode agora receber notificações de alerta com um esquema consistente.

Qualquer instância de alerta descreve **o recurso que foi afetado** e a causa do **alerta,** e estes casos são descritos no esquema comum nas seguintes secções:
* **Essencial**: Um conjunto de **campos padronizados,** comuns em todos os tipos de alerta, que descrevem **em que recurso** o alerta está, juntamente com metadados de alerta comum adicionais (por exemplo, gravidade ou descrição). 
* **Contexto de alerta**: Um conjunto de campos que descrevem a **causa do alerta,** com campos que variam **em função do tipo**de alerta . Por exemplo, um alerta métrico teria campos como o nome métrico e o valor métrico no contexto de alerta, enquanto um alerta de registo de atividade teria informações sobre o evento que gerou o alerta. 

Os cenários típicos de integração que ouvimos dos clientes envolvem o encaminhamento da instância de alerta para a equipa em causa com base em algum pivô (por exemplo, grupo de recursos), após o qual a equipa responsável começa a trabalhar nele. Com o esquema de alerta comum, você pode ter a lógica de encaminhamento padronizada através de tipos de alerta, alavancando os campos essenciais, deixando os campos de contexto como é para as equipas envolvidas investigarem mais.

Isto significa que pode potencialmente ter menos integrações, tornando o processo de gestão e manutenção das mesmas uma tarefa _muito_ mais simples. Além disso, futuros enriquecimentos de carga de alerta (por exemplo, personalização, enriquecimento de diagnóstico, etc.) só surgirão no esquema comum.

## <a name="what-enhancements-does-the-common-alert-schema-bring"></a>Que melhorias traz o esquema de alerta comum?

O esquema de alerta comum manifestar-se-á principalmente nas suas notificações de alerta. As melhorias que verá estão listadas abaixo:

| Ação | Melhoramentos |
|:---|:---|
| SMS | Um modelo sMS consistente para todos os tipos de alerta. |
| Email | Um modelo de e-mail consistente e detalhado, permitindo-lhe diagnosticar facilmente problemas num ápice. Incrustadas ligações profundas à instância de alerta no portal e ao recurso afetado garantem que pode entrar rapidamente no processo de reparação. |
| Webhook/Logic App/Azure Function/Automation Runbook | Uma estrutura JSON consistente para todos os tipos de alerta, que permite construir facilmente integrações em todos os diferentes tipos de alerta. |

O novo esquema também permitirá uma experiência de consumo de alerta mais rica tanto no portal Azure como na aplicação móvel Azure no futuro imediato. 

[Saiba mais sobre as definições de esquemas para Webhooks/Logic Apps/Azure Functions/Automation Runbooks.](https://aka.ms/commonAlertSchemaDefinitions)

> [!NOTE]
> As seguintes ações não suportam o esquema de alerta comum: Conector ITSM.

## <a name="how-do-i-enable-the-common-alert-schema"></a>Como posso permitir o esquema de alerta comum?

Pode optar pelo esquema de alerta comum através de Grupos de Ação, tanto no portal como através da API REST. O alternância para mudar para o novo esquema existe a um nível de ação. Por exemplo, você tem que optar separadamente por uma ação de e-mail e uma ação webhook.

> [!NOTE]
> 1. Os seguintes tipos de alerta suportam o esquema comum por defeito (não é necessário optar por opção):
>     * Alertas de deteção inteligente
> 1. Os seguintes tipos de alerta atualmente não suportam o esquema comum:
>     * Alertas gerados pelo [Monitor Azure para VMs](https://docs.microsoft.com/azure/azure-monitor/insights/vminsights-overview)
>     * Alertas gerados pela [Azure Cost Management](https://docs.microsoft.com/azure/billing/billing-cost-management-budget-scenario)

### <a name="through-the-azure-portal"></a>Através do portal Azure

![Esquema de alerta comum opte por](media/alerts-common-schema/portal-opt-in.png)

1. Abra qualquer ação existente ou nova num grupo de ação. 
1. Selecione "Sim" para o alternância para ativar o esquema de alerta comum, como mostrado.

### <a name="through-the-action-groups-rest-api"></a>Através dos Grupos de Ação REST API

Também pode utilizar a API dos [Grupos](https://docs.microsoft.com/rest/api/monitor/actiongroups) de Ação para optar pelo esquema de alerta comum. Ao efetuar a chamada REST API de [criação ou atualização,](https://docs.microsoft.com/rest/api/monitor/actiongroups/createorupdate) pode definir a bandeira "useCommonAlertSchema" para "true" (para optar por) ou "falsa" (para não optar) por qualquer uma das seguintes ações - e-mail/webhook/logic app/Azure Function/automação.

Por exemplo, o seguinte organismo de pedido feito para a [criação ou atualização](https://docs.microsoft.com/rest/api/monitor/actiongroups/createorupdate) da API REST fará o seguinte:

* Ativar o esquema de alerta comum para a ação por e-mail "E-mail de João Ninguém"
* Desativar o esquema de alerta comum para a ação de e-mail "E-mail de Jane Smith"
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

- [Definições comuns de esquemas de alerta para Webhooks/Logic Apps/Azure Functions/Automation Runbooks.](https://aka.ms/commonAlertSchemaDefinitions)
- [Aprenda a criar uma aplicação lógica que aproveite o esquema de alerta comum para lidar com todos os seus alertas.](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-common-schema-integrations) 



