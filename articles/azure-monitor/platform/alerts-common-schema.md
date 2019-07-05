---
title: Esquema comum do alerta para alertas do Azure monitor
description: Compreender o esquema comum do alerta, por que deve utilizá-lo e como ativá-la
author: anantr
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 03/14/2019
ms.author: anantr
ms.component: alerts
ms.openlocfilehash: be38f5d3bbf4fe3d90fc78feab8774595b5338f2
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67447675"
---
# <a name="common-alert-schema"></a>Esquema de alertas comuns

Este artigo descreve o que é o esquema comum do alerta, as vantagens da utilização que e como ativá-lo.

## <a name="what-is-the-common-alert-schema"></a>O que é o esquema comum de alerta?

O esquema comum do alerta uniformiza a experiência de consumo de notificações de alertas no Azure hoje mesmo. Historicamente, os três tipos de alertas no Azure hoje (métrica, registo e o registo de atividades) tenham tido a seus próprios modelos de e-mail, webhook esquemas, etc. Com o esquema comum do alerta, agora pode receber notificações de alerta com um esquema consistente.

Qualquer instância de alerta descreve **o recurso que foi afetado** e **a causa do alerta**, e estas instâncias são descritas no esquema comum nas seções a seguir:
* **Essentials**: Um conjunto de **padronizadas campos**comum em todos os tipos alerta, que descrevem **que recurso** o alerta está ativada, juntamente com o common alerta metadados adicionais (por exemplo, a gravidade ou a descrição). 
* **Contexto do alerta**: Um conjunto de campos que descrevem os **causa do alerta**, com campos que variam **com base no tipo de alerta**. Por exemplo, um alerta de métrica teria campos, como o nome da métrica e o valor de métrica no contexto do alerta, ao passo que um alerta de registo de atividade teria informações sobre o evento que gerou o alerta. 

Os cenários de integração típico que recebemos dos clientes envolvem o encaminhamento da instância do alerta para a equipe de preocupar com base em algumas dinâmica (por exemplo, grupo de recursos), após o qual a equipe responsável começa a funcionar no mesmo. Com o esquema comum do alerta, pode padronizaram o lógica de roteamento entre tipos de alertas ao tirar partido de campos essenciais, deixando os campos de contexto, pois é para as equipes envolvidas investigar mais.

Isso significa que pode potencialmente ter menos de integrações, tornando o processo de gerenciamento e manutenção-los um _muito_ tarefas mais simples. Além disso, possível de payload de alerta futura (por exemplo, personalização, enriquecimento de diagnóstico, etc.) apenas irá surgir no esquema comum.

## <a name="what-enhancements-does-the-common-alert-schema-bring"></a>Os aprimoramentos apresentam pelo esquema comum do alerta?

O esquema comum do alerta principalmente se manifestará de notificações de alerta. Os aprimoramentos que verá são listados abaixo:

| Ação | Aprimoramentos|
|:---|:---|
| SMS | Um modelo SMS consistente para todos os tipos de alertas. |
| Email | Um modelo de e-mail detalhado e consistente, permitindo-lhe facilmente diagnosticar problemas rapidamente. Ligações de profunda Embedded para a instância de alerta no portal e o recurso afetado Certifique-se de que possa rapidamente passar para o processo de atualização. |
| Runbook de automatização/função de aplicação/Azure Webhook/lógica | Uma JSON estrutura consistente para todos os tipos de alertas, que permite-lhe facilmente criar integrações entre os diferentes tipos de alerta. |

O novo esquema também permitirá uma experiência mais rica de alerta de consumo para através do portal do Azure e a aplicação móvel do Azure no futuro imediato. 

[Saiba mais sobre as definições de esquema para Runbooks de funções/automatização de aplicações/Azure/lógica de Webhooks.](https://aka.ms/commonAlertSchemaDefinitions)

> [!NOTE]
> As seguintes ações não suportam o esquema comum do alerta: Conector ITSM.

## <a name="how-do-i-enable-the-common-alert-schema"></a>Como posso ativar o esquema comum do alerta?

Pode optar ativamente por participar no ou optar ativamente por participar ao esquema comum do alerta através de grupos de ação, sobre o portal e através da API REST. O botão de alternar para mudar para o novo esquema existe num nível de ação. Por exemplo, terá de separadamente opta por utilizar uma ação de e-mail e uma ação do webhook.

> [!NOTE]
> 1. Os seguintes tipos de alerta suportam o esquema comum, por predefinição (não optar ativamente por participar no necessário):
>     * Alertas de deteção inteligente
> 1. Os seguintes tipos de alerta não suportam atualmente o esquema comum:
>     * Alertas gerados por [Azure Monitor para VMs](https://docs.microsoft.com/azure/azure-monitor/insights/vminsights-overview)
>     * Alertas gerados por [Azure Cost Management](https://docs.microsoft.com/azure/billing/billing-cost-management-budget-scenario)

### <a name="through-the-azure-portal"></a>Através do portal do Azure

![Escolher o esquema comum do alerta](media/alerts-common-schema/portal-opt-in.png)

1. Abra qualquer existente ou uma nova ação num grupo de ação. 
1. Selecione "Sim" para o botão de alternar ativar o esquema comum do alerta, conforme mostrado.

### <a name="through-the-action-groups-rest-api"></a>Através dos REST API de grupos de ação

Também pode utilizar o [API de grupos de ação](https://docs.microsoft.com/rest/api/monitor/actiongroups) para participar no esquema comum do alerta. Utilizando o [criar ou atualizar](https://docs.microsoft.com/rest/api/monitor/actiongroups/createorupdate) chamada à REST API, pode definir o sinalizador "useCommonAlertSchema" como "verdadeiro" (para optar ativamente por participar no) ou 'false' (ao excluir) de para qualquer uma das seguintes ações - runbook de automatização/função e-mail/webhook/lógica de aplicação/Azure.

Por exemplo, o seguinte solicitar corpo feito para o [criar ou atualizar](https://docs.microsoft.com/rest/api/monitor/actiongroups/createorupdate) REST API realizará o seguinte procedimento:

* Ativar o esquema comum do alerta para a ação de e-mail "Email de João Duarte"
* Desativar o esquema comum do alerta para a ação de e-mail "Email Joana Smith"
* Ativar o esquema comum do alerta para a ação do webhook "Webhook de exemplo"

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





## <a name="next-steps"></a>Passos Seguintes

- [Definições de alerta de esquema comuns para Runbooks de funções/automatização de aplicações/Azure/lógica de Webhooks.](https://aka.ms/commonAlertSchemaDefinitions)
- [Saiba como criar uma aplicação lógica que utiliza o esquema comum do alerta para lidar com todos os seus alertas.](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-common-schema-integrations) 



