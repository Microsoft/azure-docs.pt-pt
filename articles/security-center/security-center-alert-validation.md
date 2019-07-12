---
title: Alerta de validação (ficheiro de teste EICAR) no Centro de segurança do Azure | Documentos da Microsoft
description: Este documento ajuda-o a validar os alertas de segurança no Centro de Segurança do Azure.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: f8f17a55-e672-4d86-8ba9-6c3ce2e71a57
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 7/02/2019
ms.author: rkarlin
ms.openlocfilehash: f65b4b74a1a91fa081bd9c0d8146d055cebb0de6
ms.sourcegitcommit: c0419208061b2b5579f6e16f78d9d45513bb7bbc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/08/2019
ms.locfileid: "67626304"
---
# <a name="alert-validation-eicar-test-file-in-azure-security-center"></a>Validação de alertas (ficheiro de teste EICAR) no Centro de segurança do Azure
Este documento ajuda-o a aprender como verificar se o sistema está corretamente configurado para os alertas do Centro de Segurança do Azure.

## <a name="what-are-security-alerts"></a>O que são alertas de segurança?
Os alertas são notificações de centro de segurança gera quando detetar ameaças nos seus recursos. Ele dá prioridade e apresenta uma lista de alertas, juntamente com as informações necessárias para investigar rapidamente o problema. Centro de segurança também fornece recomendações sobre como pode remediar um ataque.
Para obter mais informações, consulte [alertas de segurança no Centro de segurança do Azure](security-center-alerts-overview.md) e [gerir e responder a alertas de segurança no Centro de segurança do Azure](security-center-managing-and-responding-alerts.md)

## <a name="alert-validation"></a>Validação de alertas

* [Windows](#validate-windows)
* [Linux](#validate-linux)

## Validar o alerta numa VM do Windows <a name="validate-windows"></a>

Depois do Centro de segurança agente está instalado no seu computador, siga estes passos do computador onde pretende que seja o recurso atacado do alerta:

1. Copie um executável (por exemplo **calc.exe**) para a área de trabalho do computador, ou outro diretório da sua comodidade e renomeá-lo como **ASC_AlertTest_662jfi039N.exe**.
1. Abra a linha de comandos e execute este ficheiro com um argumento (apenas um nome de argumento FALSO), tais como: ```ASC_AlertTest_662jfi039N.exe -foo```
1. Aguarde 5 a 10 minutos e abra os Alertas do Centro de Segurança. Um alerta semelhante para o [exemplo](#alert-validate) abaixo que deverá ser apresentada:

> [!NOTE]
> Ao rever este alerta de teste para Windows, certifique-se de que o campo **auditoria de argumentos ativada** é **verdadeiro**. Se for **false**, em seguida, tem de ativar a auditoria de argumentos da linha de comandos. Para o ativar, utilize a seguinte linha de comando:
>
>```reg add "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\policies\system\Audit" /f /v "ProcessCreationIncludeCmdLine_Enabled"```

## Validar o alerta numa VM do Linux <a name="validate-linux"></a>

Depois do Centro de segurança agente está instalado no seu computador, siga estes passos do computador onde pretende que seja o recurso atacado do alerta:
1. Copie um executável numa localização conveniente e mude o nome para **. / asc_alerttest_662jfi039n**, por exemplo:

    ```cp /bin/echo ./asc_alerttest_662jfi039n```

1. Abra a linha de comandos e execute este ficheiro:

    ```./asc_alerttest_662jfi039n testing eicar pipe```

1. Aguarde 5 a 10 minutos e abra os Alertas do Centro de Segurança. Um alerta semelhante para o [exemplo](#alert-validate) abaixo que deverá ser apresentada:

### Exemplo de alerta <a name="alert-validate"></a>

![Exemplo de validação de alertas](./media/security-center-alert-validation/security-center-alert-validation-fig2.png) 

## <a name="see-also"></a>Consulte também
Este artigo apresentou-lhe o processo de validação de alertas. Agora que está familiarizado com a validação, experimente os seguintes artigos:

* [Gerir e responder a alertas de segurança no Centro de Segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts). Saiba como gerir alertas e responder a incidentes de segurança no Centro de Segurança.
* [Monitorização de estado de funcionamento de segurança no Centro de Segurança do Azure](security-center-monitoring.md). Saiba como monitorizar o estado de funcionamento dos recursos do Azure.
* [Compreender os alertas de segurança no Centro de Segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-alerts-type). Saiba mais sobre os diferentes tipos de alertas de segurança.
* [Guia de Resolução de Problemas do Centro de Segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-troubleshooting-guide). Saiba como resolver problemas comuns no Centro de Segurança.
* [Centro de Segurança do Azure FAQ (FAQ do Centro de Segurança do Azure)](security-center-faq.md). Encontre as perguntas mais frequentes acerca de como utilizar o serviço.
* [Blogue de Segurança do Azure](https://blogs.msdn.com/b/azuresecurity/). Encontre mensagens do blogue acerca da segurança e conformidade do Azure.
