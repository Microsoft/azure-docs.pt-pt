---
title: Validação de alerta (ficheiro de teste EICAR) no Azure Security Center Microsoft Docs
description: Este documento ajuda-o a validar os alertas de segurança no Centro de Segurança do Azure.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: f8f17a55-e672-4d86-8ba9-6c3ce2e71a57
ms.service: security-center
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/22/2020
ms.author: memildin
ms.openlocfilehash: 0c777c2461047f21e8fb413faec6d5f1140fc75f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91438813"
---
# <a name="alert-validation-in-azure-security-center"></a>Validação de alerta no Centro de Segurança Azure
Este documento ajuda-o a aprender como verificar se o sistema está corretamente configurado para os alertas do Centro de Segurança do Azure.

## <a name="what-are-security-alerts"></a>O que são alertas de segurança?
Os alertas são as notificações que o Centro de Segurança gera quando deteta ameaças nos seus recursos. Prioriza e enumera os alertas juntamente com as informações necessárias para investigar rapidamente o problema. O Centro de Segurança também fornece recomendações sobre como pode remediar um ataque.
Para mais informações, consulte [alertas de segurança no Centro de Segurança](security-center-alerts-overview.md) e [Gestão e respondendo a alertas de segurança](security-center-managing-and-responding-alerts.md)

## <a name="alert-validation"></a>Validação de alertas

* [Windows](#validate-windows)
* [Linux](#validate-linux)
* [Kubernetes](#validate-kubernetes)

## <a name="validate-alerts-on-windows-vms"></a>Validar alertas em VMs do Windows <a name="validate-windows"></a>

Depois de instalado o agente do Centro de Segurança no seu computador, siga estes passos a partir do computador onde pretende ser o recurso atacado do alerta:

1. Copie um executável (por exemplo **calc.exe) **para o ambiente de trabalho do computador, ou outro diretório da sua conveniência, e rebatize-o como **ASC_AlertTest_662jfi039N.exe**.
1. Abra o pedido de comando e execute este ficheiro com um argumento (apenas um nome de argumento falso), tais como: ```ASC_AlertTest_662jfi039N.exe -foo```
1. Aguarde 5 a 10 minutos e abra os Alertas do Centro de Segurança. Deve aparecer um alerta.

> [!NOTE]
> Ao rever este alerta de teste para windows, certifique-se de que o campo **de auditoria de argumentos ativado** é **verdadeiro**. Se for **falso,** então tem de permitir a auditoria de argumentos de linha de comando. Para o ativar, utilize o seguinte comando:
>
>```reg add "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\policies\system\Audit" /f /v "ProcessCreationIncludeCmdLine_Enabled"```

## <a name="validate-alerts-on-linux-vms"></a>Validar alertas em VMs Linux <a name="validate-linux"></a>

Depois de instalado o agente do Centro de Segurança no seu computador, siga estes passos a partir do computador onde pretende ser o recurso atacado do alerta:
1. Copie um executável para um local conveniente e rebatize-o para **./asc_alerttest_662jfi039n,** por exemplo:

    ```cp /bin/echo ./asc_alerttest_662jfi039n```

1. Abra o pedido de comando e execute este ficheiro:

    ```./asc_alerttest_662jfi039n testing eicar pipe```

1. Aguarde 5 a 10 minutos e abra os Alertas do Centro de Segurança. Deve aparecer um alerta.


## <a name="validate-alerts-on-kubernetes"></a>Validar alertas sobre Kubernetes <a name="validate-kubernetes"></a>

Se integrou o Serviço Azure Kubernetes com o Security Center, pode testar que os seus alertas estão a funcionar com o seguinte comando kubectl:

```kubectl get pods --namespace=asc-alerttest-662jfi039n```

Para obter mais informações sobre a defesa dos seus nódoas e aglomerados Kubernetes, consulte [Introdução ao Azure Defender para Kubernetes](defender-for-kubernetes-introduction.md)

## <a name="next-steps"></a>Passos seguintes
Este artigo apresentou-lhe o processo de validação de alertas. Agora que está familiarizado com a validação, experimente os seguintes artigos:

* [Validação da deteção de ameaças de cofre de chave Azure no Centro de Segurança Azure](https://techcommunity.microsoft.com/t5/azure-security-center/validating-azure-key-vault-threat-detection-in-azure-security/ba-p/1220336)
* [Gerir e responder aos alertas de segurança no Azure Security Center](security-center-managing-and-responding-alerts.md) - Aprenda a gerir os alertas e responda a incidentes de segurança no Centro de Segurança.
* [Segurança monitorização de saúde no Azure Security Center](security-center-monitoring.md) - Saiba como monitorizar a saúde dos seus recursos Azure.
* [Compreender os alertas de segurança no Azure Security Center](security-center-alerts-type.md) - Conheça os diferentes tipos de alertas de segurança.