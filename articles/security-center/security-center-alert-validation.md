---
title: Validação de alerta (arquivo de teste EICAR) na central de segurança do Azure | Microsoft Docs
description: Este documento ajuda-o a validar os alertas de segurança no Centro de Segurança do Azure.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: f8f17a55-e672-4d86-8ba9-6c3ce2e71a57
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: 709ac3a9f5e7cf0bd13a6e387f0b80caf2608fe0
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/07/2019
ms.locfileid: "73748458"
---
# <a name="alert-validation-eicar-test-file-in-azure-security-center"></a>Validação de alerta (arquivo de teste EICAR) na central de segurança do Azure
Este documento ajuda-o a aprender como verificar se o sistema está corretamente configurado para os alertas do Centro de Segurança do Azure.

## <a name="what-are-security-alerts"></a>O que são alertas de segurança?
Alertas são as notificações que a central de segurança gera quando detecta ameaças em seus recursos. Ele prioriza e lista os alertas junto com as informações necessárias para investigar rapidamente o problema. A central de segurança também fornece recomendações sobre como você pode corrigir um ataque.
Para obter mais informações, consulte [alertas de segurança na central de segurança](security-center-alerts-overview.md) e [Gerenciando e respondendo a alertas de segurança](security-center-managing-and-responding-alerts.md)

## <a name="alert-validation"></a>Validação de alertas

* [Windows](#validate-windows)
* [Linux](#validate-linux)
* [Kubernetes](#validate-kubernetes)

## Validar alertas em VMs do Windows<a name="validate-windows"></a>

Depois que o agente da central de segurança estiver instalado no computador, siga estas etapas do computador no qual você deseja ser o recurso atacado do alerta:

1. Copie um executável (por exemplo, **Calc. exe**) na área de trabalho do computador ou em outro diretório de sua conveniência e renomeie-o como **ASC_AlertTest_662jfi039N. exe**.
1. Abra o prompt de comando e execute esse arquivo com um argumento (apenas um nome de argumento falso), como: ```ASC_AlertTest_662jfi039N.exe -foo```
1. Aguarde 5 a 10 minutos e abra os Alertas do Centro de Segurança. Um alerta semelhante ao [exemplo](#alert-validate) abaixo deve ser exibido:

> [!NOTE]
> Ao revisar este alerta de teste para o Windows, verifique se a auditoria de argumentos de campo **habilitada** é **verdadeira**. Se for **false**, você precisará habilitar a auditoria de argumentos de linha de comando. Para habilitá-lo, use o seguinte comando:
>
>```reg add "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\policies\system\Audit" /f /v "ProcessCreationIncludeCmdLine_Enabled"```

## Validar alertas em VMs do Linux<a name="validate-linux"></a>

Depois que o agente da central de segurança estiver instalado no computador, siga estas etapas do computador no qual você deseja ser o recurso atacado do alerta:
1. Copie um executável para um local conveniente e renomeie-o para **./asc_alerttest_662jfi039n**, por exemplo:

    ```cp /bin/echo ./asc_alerttest_662jfi039n```

1. Abra o prompt de comando e execute este arquivo:

    ```./asc_alerttest_662jfi039n testing eicar pipe```

1. Aguarde 5 a 10 minutos e abra os Alertas do Centro de Segurança. Um alerta semelhante ao [exemplo](#alert-validate) abaixo deve ser exibido:

### Exemplo de alerta<a name="alert-validate"></a>

![Exemplo de validação de alerta](./media/security-center-alert-validation/security-center-alert-validation-fig2.png) 


## Validar alertas no kubernetes<a name="validate-kubernetes"></a>

Se você estiver usando o recurso de visualização da central de segurança de integração do serviço kubernetes do Azure, execute o seguinte comando kubectl para testar se os alertas estão funcionando:

```kubectl get pods --namespace=asc-alerttest-662jfi039n```

Para obter mais informações sobre a integração do serviço kubernetes do Azure e da central de segurança do Azure, consulte [Este artigo](azure-kubernetes-service-integration.md).

## <a name="next-steps"></a>Passos seguintes
Este artigo apresentou-lhe o processo de validação de alertas. Agora que está familiarizado com a validação, experimente os seguintes artigos:

* [Gerenciando e respondendo a alertas de segurança na central de segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts) -saiba como gerenciar alertas e responder a incidentes de segurança na central de segurança.
* [Monitoramento de integridade de segurança na central de segurança do Azure](security-center-monitoring.md) -saiba como monitorar a integridade dos recursos do Azure.
* [Noções básicas sobre alertas de segurança na central de segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-alerts-type) -saiba mais sobre os diferentes tipos de alertas de segurança.
* [Guia de solução de problemas da central de segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-troubleshooting-guide) -saiba como solucionar problemas comuns na central de segurança.
* [Perguntas frequentes da central de segurança do Azure](security-center-faq.md) -encontre perguntas frequentes sobre como usar o serviço.
* [Blog de segurança do Azure](https://blogs.msdn.com/b/azuresecurity/) -encontre postagens no blog sobre a segurança e a conformidade do Azure.
