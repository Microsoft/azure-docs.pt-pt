---
title: Validação de Alertas no Centro de Segurança do Azure | Microsoft Docs
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
ms.date: 11/28/2018
ms.author: rkarlin
ms.openlocfilehash: 009f5fe7243b8ce597c2be9f9c6874cdb56d103c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60706062"
---
# <a name="alerts-validation-in-azure-security-center"></a>Validação de Alertas no Centro de Segurança do Azure
Este documento ajuda-o a aprender como verificar se o sistema está corretamente configurado para os alertas do Centro de Segurança do Azure.

## <a name="what-are-security-alerts"></a>O que são alertas de segurança?
O Centro de Segurança recolhe, analisa e integra automaticamente dados de registo a partir dos seus recursos do Azure, da rede e das soluções de parceiros ligadas (como soluções de proteção de ponto final e firewall) para detetar e alertar relativamente a ameaças. Leia o artigo [Gerir e responder a alertas de segurança no Centro de Segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts) para obter mais informações sobre alertas de segurança e o artigo [Compreender os alertas de segurança no Centro de Segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-alerts-type) para saber mais sobre os diferentes tipos de alertas.

## <a name="alert-validation"></a>Validação de alertas
Depois de instalar o agente do Centro de Segurança no seu computador, siga os passos abaixo no computador onde quer que se encontre o recurso atacado do alerta:

1. Copie um executável (por exemplo, calc.exe) para o ambiente de trabalho do computador ou outro diretório à sua escolha.
2. Mude o nome deste ficheiro para **ASC_AlertTest_662jfi039N.exe**.
3. Abra a linha de comandos e execute este ficheiro com um argumento (apenas um nome de argumento FALSO), tais como: *ASC_AlertTest_662jfi039N.exe -foo*
4. Aguarde 5 a 10 minutos e abra os Alertas do Centro de Segurança. Aí, deverá encontrar um alerta semelhante ao seguinte:

    ![Validação de Alertas](./media/security-center-alert-validation/security-center-alert-validation-fig2.png)

Quando consultar este alerta, certifique-se de que o campo Auditoria de Argumentos Ativada é apresentado como true. Se for false, terá de ativar a auditoria de argumentos da linha de comandos. Para ativar esta opção, utilize a seguinte linha de comandos:

*reg add "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\policies\system\Audit" /f /v "ProcessCreationIncludeCmdLine_Enabled"*


> [!NOTE]
> Veja o vídeo [Validação de Alertas no Centro de Segurança do Azure](https://channel9.msdn.com/Blogs/Azure-Security-Videos/Alert-Validation-in-Azure-Security-Center) para ver uma demonstração desta funcionalidade.

## <a name="see-also"></a>Consulte também
Este artigo apresentou-lhe o processo de validação de alertas. Agora que está familiarizado com a validação, experimente os seguintes artigos:

* [Gerir e responder a alertas de segurança no Centro de Segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts). Saiba como gerir alertas e responder a incidentes de segurança no Centro de Segurança.
* [Monitorização de estado de funcionamento de segurança no Centro de Segurança do Azure](security-center-monitoring.md). Saiba como monitorizar o estado de funcionamento dos recursos do Azure.
* [Compreender os alertas de segurança no Centro de Segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-alerts-type). Saiba mais sobre os diferentes tipos de alertas de segurança.
* [Guia de Resolução de Problemas do Centro de Segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-troubleshooting-guide). Saiba como resolver problemas comuns no Centro de Segurança.
* [Centro de Segurança do Azure FAQ (FAQ do Centro de Segurança do Azure)](security-center-faq.md). Encontre as perguntas mais frequentes acerca de como utilizar o serviço.
* [Blogue de Segurança do Azure](https://blogs.msdn.com/b/azuresecurity/). Encontre mensagens do blogue acerca da segurança e conformidade do Azure.
