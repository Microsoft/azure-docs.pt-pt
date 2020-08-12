---
title: Problemas na resolução de problemas da política de acesso ao cofre do Azure
description: Problemas na resolução de problemas da política de acesso ao cofre do Azure
author: sebansal
ms.author: sebansal
ms.date: 08/10/2020
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.openlocfilehash: e52fe8e738f2a35204078b27019ea3742c608560
ms.sourcegitcommit: b8702065338fc1ed81bfed082650b5b58234a702
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/11/2020
ms.locfileid: "88122175"
---
# <a name="troubleshooting-azure-key-vault-access-policy-issues"></a>Problemas na resolução de problemas da política de acesso ao cofre do Azure

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes

### <a name="how-can-i-identify-how-and-when-key-vaults-are-accessed"></a>Como posso identificar como e quando os cofres são acedidos?
Depois de criar um ou mais cofres chave, provavelmente vai querer monitorizar como e quando os seus cofres chave são acedidos, e por quem. Pode fazê-lo ativando o registo do Azure Key Vault, para um guia passo a passo para permitir a exploração madeireira, [leia mais](https://docs.microsoft.com/azure/key-vault/general/logging).

### <a name="how-can-i-monitor-vault-availability-service-latency-periods-or-other-performance-metrics-for-key-vault"></a>Como posso monitorizar a disponibilidade do cofre, períodos de latência de serviço ou outras métricas de desempenho para o cofre chave?
À medida que começa a escalar o seu serviço, o número de pedidos enviados para o seu cofre-chave aumentará. Isto tem um potencial para aumentar a latência dos seus pedidos e, em casos extremos, fazer com que os seus pedidos sejam estrangulados, o que irá afetar o desempenho do seu serviço. Pode monitorizar as métricas de desempenho do cofre e ser alertado para limiares específicos, para um guia passo a passo para configurar a monitorização, [ler mais](https://docs.microsoft.com/azure/key-vault/general/alert).

### <a name="how-can-i-assign-access-control-per-key-vault-object"></a>Como posso atribuir controlo de acesso por objeto de cofre chave? 
A disponibilidade da funcionalidade de controlo de acesso por segredo/chave/certificado será notificada aqui, [leia mais](https://feedback.azure.com/forums/906355-azure-key-vault/suggestions/.32213176-per-secret-key-certificate-access-control)

### <a name="how-can-i-provide-key-vault-authenticate-using-access-control-policy"></a>Como posso fornecer a autenticação do cofre de chaves usando a política de controlo de acessos?
A forma mais simples de autenticar uma aplicação baseada em nuvem para o Key Vault é com uma identidade gerida; ver [usar uma identidade gerida pelo Serviço de Aplicações para aceder ao Cofre da Chave Azure]( https://docs.microsoft.com/azure/key-vault/general/managed-identity) para obter detalhes.
Se estiver a criar uma aplicação on-prem, a fazer desenvolvimento local ou de outra forma incapaz de utilizar uma identidade gerida, pode, em vez disso, registar manualmente um serviço principal e fornecer acesso ao cofre-chave utilizando uma política de controlo de acesso, [leia mais](https://docs.microsoft.com/azure/key-vault/general/group-permissions-for-apps).


### <a name="how-can-i-give-the-ad-group-access-to-the-key-vault"></a>Como posso dar ao grupo AD acesso ao cofre das chaves?
Dê permissões ao grupo AD para o seu cofre de chaves utilizando o comando de definição de chave Azure CLI az, ou o cmdlet Azure PowerShell Set-AzKeyVaultAccessPolicy. Por exemplo, reveja [dar à aplicação, ao grupo AZure AD ou ao acesso do utilizador ao cofre da chave.](https://docs.microsoft.com/azure/key-vault/general/group-permissions-for-apps#give-the-principal-access-to-your-key-vault)

A aplicação também precisa de pelo menos uma função de Gestão de Identidade e Acesso (IAM) atribuída ao cofre-chave. Caso contrário, não poderá iniciar sessão e falhará com direitos insuficientes de acesso à subscrição. Os grupos AD Azure com Identidades Geridas podem exigir até 8 horas para refrescar o token e tornar-se eficazes.

### <a name="recommended-troubleshooting-steps-for-following-error-types"></a>Etapas recomendadas de resolução de problemas para seguir tipos de erro
* HTTP 401: Pedido não autenticado - [Etapas de resolução de problemas](https://docs.microsoft.com/azure/key-vault/general/rest-error-codes#http-401-unauthenticated-request)
* HTTP 403: Permissões insuficientes - [Etapas de resolução de problemas](https://docs.microsoft.com/azure/key-vault/general/rest-error-codes#http-403-insufficient-permissions)
* HTTP 429: Demasiados pedidos - [Etapas de resolução de problemas](https://docs.microsoft.com/azure/key-vault/general/rest-error-codes#http-429-too-many-requests)
* Verifique se eliminou a permissão de acesso ao cofre de chaves: [Políticas de Acesso do Key Vault](https://docs.microsoft.com/azure/key-vault/general/group-permissions-for-apps)
* Se tiver problemas com a autenticação no cofre de chave, utilize o [SDK de Autenticação](https://azure.github.io/azure-sdk/posts/2020-02-25/defaultazurecredentials.html)

### <a name="what-are-the-best-practices-i-should-implement-when-key-vault-is-getting-throttled"></a>Quais são as melhores práticas que devo implementar quando o cofre está a ser acelerado?
Siga as melhores práticas, documentadas [aqui](https://docs.microsoft.com/azure/key-vault/general/overview-throttling#how-to-throttle-your-app-in-response-to-service-limits)

## <a name="next-steps"></a>Passos Seguintes

Saiba como resolver erros de autenticação do cofre de chaves. [Guia de resolução de problemas do cofre chave](https://docs.microsoft.com/azure/key-vault/general/rest-error-codes)
