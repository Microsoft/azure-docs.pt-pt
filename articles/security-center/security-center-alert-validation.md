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
ms.date: 07/02/2019
ms.author: memildin
ms.openlocfilehash: 32f67fb94b207735e77583a6db62f7c8703dd991
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/23/2019
ms.locfileid: "71202737"
---
# <a name="alert-validation-eicar-test-file-in-azure-security-center"></a>Validação de alerta (arquivo de teste EICAR) na central de segurança do Azure
Este documento ajuda-o a aprender como verificar se o sistema está corretamente configurado para os alertas do Centro de Segurança do Azure.

## <a name="what-are-security-alerts"></a>O que são alertas de segurança?
Alertas são as notificações que a central de segurança gera quando detecta ameaças em seus recursos. Ele prioriza e lista os alertas junto com as informações necessárias para que você investigue rapidamente o problema. A central de segurança também fornece recomendações sobre como você pode corrigir um ataque.
Para obter mais informações, consulte [alertas de segurança na central de segurança do Azure](security-center-alerts-overview.md) e [Gerenciando e respondendo a alertas de segurança na central de segurança do Azure](security-center-managing-and-responding-alerts.md)

## <a name="alert-validation"></a>Validação de alertas

* [Windows](#validate-windows)
* [Linux](#validate-linux)

## Validar o alerta na VM do Windows<a name="validate-windows"></a>

Depois que o agente da central de segurança estiver instalado no computador, siga estas etapas do computador no qual você deseja ser o recurso atacado do alerta:

1. Copie um executável (por exemplo, **Calc. exe**) na área de trabalho do computador ou em outro diretório de sua conveniência e renomeie-o como **ASC_AlertTest_662jfi039N. exe**.
1. Abra o prompt de comando e execute esse arquivo com um argumento (apenas um nome de argumento falso), como:```ASC_AlertTest_662jfi039N.exe -foo```
1. Aguarde 5 a 10 minutos e abra os Alertas do Centro de Segurança. Um alerta semelhante ao [exemplo](#alert-validate) abaixo deve ser exibido:

> [!NOTE]
> Ao revisar este alerta de teste para o Windows, verifique se a auditoria de argumentos de campo **habilitada** é **verdadeira**. Se for **false**, você precisará habilitar a auditoria de argumentos de linha de comando. Para habilitá-lo, use a seguinte linha de comando:
>
>```reg add "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\policies\system\Audit" /f /v "ProcessCreationIncludeCmdLine_Enabled"```

## Validar o alerta na VM do Linux<a name="validate-linux"></a>

Depois que o agente da central de segurança estiver instalado no computador, siga estas etapas do computador no qual você deseja ser o recurso atacado do alerta:
1. Copie um executável para um local conveniente e renomeie-o para **./asc_alerttest_662jfi039n**, por exemplo:

    ```cp /bin/echo ./asc_alerttest_662jfi039n```

1. Abra o prompt de comando e execute este arquivo:

    ```./asc_alerttest_662jfi039n testing eicar pipe```

1. Aguarde 5 a 10 minutos e abra os Alertas do Centro de Segurança. Um alerta semelhante ao [exemplo](#alert-validate) abaixo deve ser exibido:

### Exemplo de alerta<a name="alert-validate"></a>

![Exemplo de validação de alerta](./media/security-center-alert-validation/security-center-alert-validation-fig2.png) 

## <a name="see-also"></a>Consulte também
Este artigo apresentou-lhe o processo de validação de alertas. Agora que está familiarizado com a validação, experimente os seguintes artigos:

* [Gerir e responder a alertas de segurança no Centro de Segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts). Saiba como gerir alertas e responder a incidentes de segurança no Centro de Segurança.
* [Monitorização de estado de funcionamento de segurança no Centro de Segurança do Azure](security-center-monitoring.md). Saiba como monitorizar o estado de funcionamento dos recursos do Azure.
* [Compreender os alertas de segurança no Centro de Segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-alerts-type). Saiba mais sobre os diferentes tipos de alertas de segurança.
* [Guia de Resolução de Problemas do Centro de Segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-troubleshooting-guide). Saiba como resolver problemas comuns no Centro de Segurança.
* [Centro de Segurança do Azure FAQ (FAQ do Centro de Segurança do Azure)](security-center-faq.md). Encontre as perguntas mais frequentes acerca de como utilizar o serviço.
* [Blogue de Segurança do Azure](https://blogs.msdn.com/b/azuresecurity/). Encontre mensagens do blogue acerca da segurança e conformidade do Azure.
