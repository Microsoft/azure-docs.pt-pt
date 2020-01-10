---
title: Pacote de detecção de segurança com informações do Aplicativo Azure
description: Monitore o aplicativo com informações de Aplicativo Azure e detecção inteligente para possíveis problemas de segurança.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 12/12/2017
ms.openlocfilehash: ba35b3a3e1985f49ade468c4d2e82ca579fdac2a
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75432460"
---
# <a name="application-security-detection-pack-preview"></a>Pacote de detecção de segurança do aplicativo (versão prévia)

Application Insights analisa automaticamente a telemetria gerada pelo seu aplicativo e detecta possíveis problemas de segurança. Essa funcionalidade permite identificar possíveis problemas de segurança e tratá-los corrigindo o aplicativo ou tomando as medidas de segurança necessárias.

Esse recurso não requer nenhuma configuração especial, além de [configurar seu aplicativo para enviar telemetria](https://docs.microsoft.com/azure/application-insights/app-insights-usage-overview).

## <a name="when-would-i-get-this-type-of-smart-detection-notification"></a>Quando eu receberia esse tipo de notificação de detecção inteligente?
Há três tipos de problemas de segurança detectados:
1. Acesso à URL inseguro: uma URL no aplicativo está sendo acessada via HTTP e HTTPS. Normalmente, uma URL que aceita solicitações HTTPS não deve aceitar solicitações HTTP. Isso pode indicar um problema de bug ou segurança em seu aplicativo.
2. Formulário inseguro: um formulário (ou outra solicitação "POST") no aplicativo usa HTTP em vez de HTTPS. O uso de HTTP pode comprometer os dados do usuário que são enviados pelo formulário.
3. Atividade de usuário suspeita: o aplicativo está sendo acessado de vários países/regiões pelo mesmo usuário praticamente ao mesmo tempo. Por exemplo, o mesmo usuário acessou o aplicativo da Espanha e o Estados Unidos dentro da mesma hora. Essa detecção indica uma tentativa de acesso potencialmente mal-intencionado ao seu aplicativo.

## <a name="does-my-app-definitely-have-a-security-issue"></a>Meu aplicativo definitivamente tem um problema de segurança?
Não, uma notificação não significa que seu aplicativo definitivamente tem um problema de segurança. Uma detecção de qualquer um dos cenários acima pode, em muitos casos, indicar um problema de segurança. No entanto, a detecção pode ter uma justificativa de negócios natural e pode ser ignorada.

## <a name="how-do-i-fix-the-insecure-url-access-detection"></a>Como fazer corrigir a detecção de "acesso à URL inseguro"?
1. **Triagem.** A notificação fornece o número de usuários que acessaram URLs inseguras e a URL que foi mais afetada por acesso inseguro. Isso pode ajudá-lo a atribuir uma prioridade ao problema.
2. **Com.** Qual porcentagem dos usuários acessaram URLs inseguras? Quantas URLs foram afetadas? Essas informações podem ser obtidas na notificação.
3. **Tect.** A detecção fornece a lista de solicitações inseguras e as listas de URLs e usuários que foram afetados, para ajudá-lo a diagnosticar o problema.

## <a name="how-do-i-fix-the-insecure-form-detection"></a>Como fazer corrigir a detecção de "forma insegura"?
1. **Triagem.** A notificação fornece o número de formulários inseguros e o número de usuários cujos dados foram potencialmente comprometidos. Isso pode ajudá-lo a atribuir uma prioridade ao problema.
2. **Com.** Qual formulário estava envolvido no maior número de transmissões inseguras e qual é a distribuição de transmissões inseguras ao longo do tempo? Essas informações podem ser obtidas na notificação.
3. **Tect.** A detecção fornece a lista de formulários inseguros e uma análise do número de transmissões inseguras para cada formulário, para ajudá-lo a diagnosticar o problema.

## <a name="how-do-i-fix-the-suspicious-user-activity-detection"></a>Como fazer corrigir a detecção de "atividade de usuário suspeita"?
1. **Triagem.** A notificação fornece o número de usuários diferentes que exibiram o comportamento suspeito. Isso pode ajudá-lo a atribuir uma prioridade ao problema.
2. **Com.** De quais países/regiões as solicitações suspeitas se originam? Qual usuário foi o mais suspeito? Essas informações podem ser obtidas na notificação.
3. **Tect.** A detecção fornece a lista de usuários suspeitos e a lista de países/regiões para cada usuário, para ajudá-lo a diagnosticar o problema.
