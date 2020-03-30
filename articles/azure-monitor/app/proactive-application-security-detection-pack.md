---
title: Pacote de deteção de segurança com insights de aplicação Azure
description: Monitorize a aplicação com Insights de Aplicação Azure e Deteção Inteligente para potenciais problemas de segurança.
ms.topic: conceptual
ms.date: 12/12/2017
ms.openlocfilehash: b23ada6fe4596a2eee242cc9145789521caf697f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77669731"
---
# <a name="application-security-detection-pack-preview"></a>Pacote de deteção de segurança de aplicações (pré-visualização)

Application Insights analisa automaticamente a telemetria gerada pela sua aplicação e deteta potenciais problemas de segurança. Esta capacidade permite identificar potenciais problemas de segurança e manuseá-los através da fixação da aplicação ou através das medidas de segurança necessárias.

Esta funcionalidade não requer configuração especial, a não ser [configurar a sua aplicação para enviar telemetria](https://docs.microsoft.com/azure/application-insights/app-insights-usage-overview).

## <a name="when-would-i-get-this-type-of-smart-detection-notification"></a>Quando é que eu receberia este tipo de notificação de deteção inteligente?
Existem três tipos de questões de segurança que são detetadas:
1. Acesso inseguro ao URL: um URL na aplicação está a ser acedido através de HTTP e HTTPS. Tipicamente, um URL que aceite pedidos HTTPS não deve aceitar pedidos HTTP. Isto pode indicar um problema de segurança ou de inseto na sua aplicação.
2. Formulário inseguro: um formulário (ou outro pedido "POST" na aplicação utiliza HTTP em vez de HTTPS. A utilização do HTTP pode comprometer os dados do utilizador que são enviados pelo formulário.
3. Atividade suspeita do utilizador: a aplicação está a ser acedida a partir de vários países/regiões pelo mesmo utilizador aproximadamente ao mesmo tempo. Por exemplo, o mesmo utilizador acedeu à aplicação da Espanha e dos Estados Unidos na mesma hora. Esta deteção indica uma tentativa de acesso potencialmente maliciosa à sua aplicação.

## <a name="does-my-app-definitely-have-a-security-issue"></a>A minha aplicação tem um problema de segurança?
Não, uma notificação não significa que a sua aplicação tenha definitivamente um problema de segurança. Uma deteção de qualquer um dos cenários acima pode, em muitos casos, indicar um problema de segurança. No entanto, a deteção pode ter uma justificação natural do negócio, e pode ser ignorada.

## <a name="how-do-i-fix-the-insecure-url-access-detection"></a>Como posso corrigir a deteção de acesso a URL inseguro?
1. **A triagem.** A notificação fornece o número de utilizadores que acederam a URLs inseguros, e o URL que foi mais afetado pelo acesso inseguro. Isto pode ajudá-lo a atribuir uma prioridade ao problema.
2. **O alcance.** Que percentagem dos utilizadores acedeu a URLs inseguros? Quantos URLs foram afetados? Estas informações podem ser obtidas a partir da notificação.
3. **Diagnosticar.** A deteção fornece a lista de pedidos inseguros, e as listas de URLs e utilizadores afetados, para ajudá-lo a diagnosticar ainda mais o problema.

## <a name="how-do-i-fix-the-insecure-form-detection"></a>Como posso corrigir a deteção do formulário inseguro?
1. **A triagem.** A notificação fornece o número de formulários inseguros e o número de utilizadores cujos dados foram potencialmente comprometidos. Isto pode ajudá-lo a atribuir uma prioridade ao problema.
2. **O alcance.** Que forma estava envolvida no maior número de transmissões inseguras, e qual é a distribuição de transmissões inseguras ao longo do tempo? Estas informações podem ser obtidas a partir da notificação.
3. **Diagnosticar.** A deteção fornece a lista de formulários inseguros e uma repartição do número de transmissões inseguras para cada formulário, para ajudá-lo a diagnosticar ainda mais o problema.

## <a name="how-do-i-fix-the-suspicious-user-activity-detection"></a>Como posso corrigir a deteção de "atividade suspeita do utilizador"?
1. **A triagem.** A notificação fornece o número de diferentes utilizadores que exibiram o comportamento suspeito. Isto pode ajudá-lo a atribuir uma prioridade ao problema.
2. **O alcance.** De que países/regiões originaram os pedidos suspeitos? Qual era o utilizador mais suspeito? Estas informações podem ser obtidas a partir da notificação.
3. **Diagnosticar.** A deteção fornece a lista de utilizadores suspeitos e a lista de países/regiões para cada utilizador, para ajudá-lo a diagnosticar ainda mais o problema.
