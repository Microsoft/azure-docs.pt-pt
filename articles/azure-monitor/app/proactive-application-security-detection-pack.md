---
title: Deteção - Pack de deteção de segurança com o Azure Application Insights inteligente | Documentos da Microsoft
description: Monitorizar a aplicação com o Azure Application Insights para potenciais problemas de segurança.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: ea2a28ed-4cd9-4006-bd5a-d4c76f4ec20b
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 12/12/2017
ms.author: mbullwin
ms.openlocfilehash: 90d58d1b22e893e922aa0f3770198fc95f539419
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "64572884"
---
# <a name="application-security-detection-pack-preview"></a>Pacote de deteção de segurança de aplicações (pré-visualização)

Application Insights automaticamente analisa a telemetria gerada pela sua aplicação e Deteta potenciais problemas de segurança. Esta capacidade permite-lhe identificar potenciais problemas de segurança e manipulá-las por corrigir o aplicativo ou por tomar as medidas de segurança necessário.

Esta funcionalidade não requer nenhuma configuração especial, que [configurar a sua aplicação para enviar telemetria](https://docs.microsoft.com/azure/application-insights/app-insights-usage-overview).

## <a name="when-would-i-get-this-type-of-smart-detection-notification"></a>Quando é que eu teria este tipo de notificação de deteção inteligente?
Existem três tipos de problemas de segurança que são detetados:
1. Acesso de URL inseguro: um URL do aplicativo está sendo acessado por meio de HTTP e HTTPS. Normalmente, um URL que aceite pedidos HTTPS não deve aceitar pedidos HTTP. Isto pode indicar um problema de bug ou de segurança na sua aplicação.
2. Formulário inseguro: um formulário (ou outro pedido de "POST") no aplicativo utiliza HTTP em vez de HTTPS. Através de HTTP podem comprometer os dados de utilizador que são enviados pelo formulário.
3. Atividade de utilizador suspeita: o aplicativo está sendo acessado a partir de vários países/regiões pelo mesmo utilizador, aproximadamente ao mesmo tempo. Por exemplo, o mesmo utilizador aceder a aplicação da Espanha e Estados Unidos na mesma hora. Esta deteção indica uma tentativa de acesso potencialmente malicioso de seu aplicativo.

## <a name="does-my-app-definitely-have-a-security-issue"></a>O meu aplicativo tem definitivamente um problema de segurança?
Não, uma notificação não significa que a aplicação tem definitivamente um problema de segurança. Em muitos casos, uma deteção de qualquer um dos cenários acima pode indicar um problema de segurança. No entanto, a deteção pode ter uma justificativa comercial natural e pode ser ignorada.

## <a name="how-do-i-fix-the-insecure-url-access-detection"></a>Como corrigir a deteção de "Acesso de URL não seguro"?
1. **Triagem.** A notificação fornece o número de usuários que acessavam URLs inseguros e o URL que estava a maioria dos afetados pelo acesso inseguro. Isto pode ajudar a atribuir uma prioridade para o problema.
2. **Âmbito.** Qual é a porcentagem de usuários acedidos URLs inseguros? URLs de quantas foram afetados? Estas informações podem ser obtidas a partir da notificação.
3. **Diagnosticar.** A deteção fornece a lista de pedidos inseguros e as listas de URLs e os utilizadores que foram afetados, para o ajudar a diagnosticar melhor o problema.

## <a name="how-do-i-fix-the-insecure-form-detection"></a>Como corrigir a deteção de "Inseguro form"?
1. **Triagem.** A notificação fornece o número de formulários inseguros e número de utilizadores cujos dados potencialmente foi comprometidos. Isto pode ajudar a atribuir uma prioridade para o problema.
2. **Âmbito.** O formulário foi envolvido no maior número de transmissões inseguros e qual é a distribuição de transmissões inseguras ao longo do tempo? Estas informações podem ser obtidas a partir da notificação.
3. **Diagnosticar.** A deteção fornece a lista de formulários inseguros e uma divisão do número de transmissões inseguros para cada formulário, para o ajudar a diagnosticar melhor o problema.

## <a name="how-do-i-fix-the-suspicious-user-activity-detection"></a>Como corrigir a deteção de "atividade de utilizador suspeita"?
1. **Triagem.** A notificação fornece o número de utilizadores diferentes que são apresentados o comportamento suspeito. Isto pode ajudar a atribuir uma prioridade para o problema.
2. **Âmbito.** De que países/regiões teve as solicitações suspeitas origem? O utilizador que foi mais suspeitas? Estas informações podem ser obtidas a partir da notificação.
3. **Diagnosticar.** A deteção fornece a lista de utilizadores suspeitos e a lista de países/regiões, para cada utilizador, para o ajudar a diagnosticar melhor o problema.
