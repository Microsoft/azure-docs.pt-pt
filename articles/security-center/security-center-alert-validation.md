---
title: Validação de alerta no Centro de Segurança Azure | Microsoft Docs
description: Saiba como validar que os seus alertas de segurança estão corretamente configurados no Azure Security Center
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
ms.date: 02/17/2021
ms.author: memildin
ms.openlocfilehash: 3bdc0af2ea581954cb5edd2c7d00d286f814506c
ms.sourcegitcommit: 58ff80474cd8b3b30b0e29be78b8bf559ab0caa1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/17/2021
ms.locfileid: "100633602"
---
# <a name="alert-validation-in-azure-security-center"></a>Validação de alerta no Centro de Segurança Azure
Este documento ajuda-o a aprender como verificar se o sistema está corretamente configurado para os alertas do Centro de Segurança do Azure.

## <a name="what-are-security-alerts"></a>O que são alertas de segurança?
Os alertas são as notificações que o Centro de Segurança gera quando deteta ameaças nos seus recursos. Prioriza e enumera os alertas juntamente com as informações necessárias para investigar rapidamente o problema. O Centro de Segurança também fornece recomendações sobre como pode remediar um ataque.
Para mais informações, consulte [alertas de segurança no Centro de Segurança](security-center-alerts-overview.md) e [Gestão e respondendo a alertas de segurança](security-center-managing-and-responding-alerts.md)


## <a name="generate-sample-azure-defender-alerts"></a>Gerar alertas do Azure Defender de exemplo

Se estiver a utilizar a nova experiência de alertas de pré-visualização, tal como descrito no [Manage e responder aos alertas de segurança no Azure Security Center,](security-center-managing-and-responding-alerts.md)pode criar alertas de amostra em alguns cliques da página de alertas de segurança no portal Azure.

Utilize alertas de amostra para:

- avaliar o valor e as capacidades do Azure Defender
- validar quaisquer configurações que tenha feito para os seus alertas de segurança (tais como integrações SIEM, automatização de fluxos de trabalho e notificações de e-mail)

Para criar alertas de amostragem:

1. A partir da barra de ferramentas na página de alertas, **selecione Criar alertas de amostras**. 
1. Selecione uma subscrição.
1. Selecione o plano/s do Azure Defender relevante para o qual deseja ver alertas. 
1. Selecione **Criar alertas de amostras**.

    :::image type="content" source="media/security-center-alert-validation/create-sample-alerts-procedures.png" alt-text="Medidas para criar alertas de amostra no Centro de Segurança Azure":::
    
    Uma notificação aparece informando-o de que os alertas da amostra estão a ser criados:

    :::image type="content" source="media/security-center-alert-validation/notification-sample-alerts-creation.png" alt-text="Notificação de que os alertas da amostra estão a ser gerados.":::

    Após alguns minutos, os alertas aparecem na página de alertas de segurança. Também aparecerão em qualquer outro lugar que tenha configurado para receber os seus alertas de segurança do Azure Security Center (SIEMs conectados, notificações de e-mail, e assim por diante).

    :::image type="content" source="media/security-center-alert-validation/sample-alerts.png" alt-text="Alertas de amostra na lista de alertas de segurança":::

    > [!TIP]
    > Os alertas são para recursos simulados.

## <a name="simulate-alerts-on-your-azure-vms-windows"></a>Simular alertas nos seus VMs Azure (Windows) <a name="validate-windows"></a>

Depois de instalado o agente do Centro de Segurança no seu computador, siga estes passos a partir do computador onde pretende ser o recurso atacado do alerta:

1. Copie um executável (por exemplo **calc.exe)** para o ambiente de trabalho do computador, ou outro diretório da sua conveniência, e rebatize-o como **ASC_AlertTest_662jfi039N.exe**.
1. Abra o pedido de comando e execute este ficheiro com um argumento (apenas um nome de argumento falso), tais como: ```ASC_AlertTest_662jfi039N.exe -foo```
1. Aguarde 5 a 10 minutos e abra os Alertas do Centro de Segurança. Deve aparecer um alerta.

> [!NOTE]
> Ao rever este alerta de teste para windows, certifique-se de que o campo **de auditoria de argumentos ativado** é **verdadeiro**. Se for **falso,** então tem de permitir a auditoria de argumentos de linha de comando. Para a ativar, utilize o seguinte comando: 
>
>```reg add "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\policies\system\Audit" /f /v "ProcessCreationIncludeCmdLine_Enabled"```

## <a name="simulate-alerts-on-your-azure-vms-linux"></a>Simular alertas nos seus VMs Azure (Linux) <a name="validate-linux"></a>

Depois de instalado o agente do Centro de Segurança no seu computador, siga estes passos a partir do computador onde pretende ser o recurso atacado do alerta:
1. Copie um executável para um local conveniente e rebatize-o para **./asc_alerttest_662jfi039n,** por exemplo:

    ```cp /bin/echo ./asc_alerttest_662jfi039n```

1. Abra o pedido de comando e execute este ficheiro:

    ```./asc_alerttest_662jfi039n testing eicar pipe```

1. Aguarde 5 a 10 minutos e abra os Alertas do Centro de Segurança. Deve aparecer um alerta.


## <a name="simulate-alerts-on-kubernetes"></a>Simular alertas em Kubernetes <a name="validate-kubernetes"></a>

Se integrou o Serviço Azure Kubernetes com o Security Center, pode testar que os seus alertas estão a funcionar com o seguinte comando kubectl:

```kubectl get pods --namespace=asc-alerttest-662jfi039n```

Para obter mais informações sobre a defesa dos seus nódoas e aglomerados Kubernetes, consulte [Introdução ao Azure Defender para Kubernetes](defender-for-kubernetes-introduction.md)

## <a name="next-steps"></a>Passos seguintes
Este artigo apresentou-lhe o processo de validação de alertas. Agora que está familiarizado com a validação, experimente os seguintes artigos:

* [Validar a Deteção de Ameaças do Azure Key Vault no Centro de Segurança do Azure](https://techcommunity.microsoft.com/t5/azure-security-center/validating-azure-key-vault-threat-detection-in-azure-security/ba-p/1220336)
* [Gerir e responder aos alertas de segurança no Azure Security Center](security-center-managing-and-responding-alerts.md) - Aprenda a gerir os alertas e responda a incidentes de segurança no Centro de Segurança.
* [Segurança monitorização de saúde no Azure Security Center](security-center-monitoring.md) - Saiba como monitorizar a saúde dos seus recursos Azure.
* [Compreender os alertas de segurança no Azure Security Center](./security-center-alerts-overview.md) - Conheça os diferentes tipos de alertas de segurança.