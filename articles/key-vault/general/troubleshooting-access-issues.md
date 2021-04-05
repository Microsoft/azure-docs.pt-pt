---
title: Problemas na resolução de problemas da política de acesso ao cofre do Azure
description: Problemas na resolução de problemas da política de acesso ao cofre do Azure
author: sebansal
ms.author: sebansal
ms.date: 08/10/2020
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.openlocfilehash: c5fab8b856ff9c82a0de887dc9c322dbf541348b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98791412"
---
# <a name="troubleshooting-azure-key-vault-access-policy-issues"></a>Problemas na resolução de problemas da política de acesso ao cofre do Azure

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes

### <a name="i-am-not-able-to-list-or-get-secretskeyscertificate-i-am-seeing-something-went-wrong-error"></a>Não posso listar ou obter segredos/chaves/certificado. Estou a ver que "alguma coisa correu mal". Um erro.
Se tiver problemas em listar/obter/criar ou aceder a segredo, certifique-se de que tem a política de acesso definida para fazer essa operação: [Políticas de acesso ao cofre de chaves](./assign-access-policy-cli.md)

### <a name="how-can-i-identify-how-and-when-key-vaults-are-accessed"></a>Como posso identificar como e quando os cofres são acedidos?

Depois de criar um ou mais cofres chave, provavelmente vai querer monitorizar como e quando os seus cofres chave são acedidos, e por quem. Pode fazer a monitorização, permitindo a exploração de registos para Azure Key Vault, para um guia passo a passo para permitir a exploração madeireira, [leia mais](./logging.md).

### <a name="how-can-i-monitor-vault-availability-service-latency-periods-or-other-performance-metrics-for-key-vault"></a>Como posso monitorizar a disponibilidade do cofre, períodos de latência de serviço ou outras métricas de desempenho para o cofre chave?

À medida que começa a escalar o seu serviço, o número de pedidos enviados para o seu cofre-chave aumentará. Tal procura tem um potencial para aumentar a latência dos seus pedidos e, em casos extremos, fazer com que os seus pedidos sejam estrangulados, o que terá impacto no desempenho do seu serviço. Pode monitorizar as métricas de desempenho do cofre e ser alertado para limiares específicos, para um guia passo a passo para configurar a monitorização, [ler mais](./alert.md).

### <a name="i-am-not-able-to-modify-access-policy-how-can-it-be-enabled"></a>Não posso modificar a política de acesso, como pode ser ativada?
O utilizador precisa de ter permissões AAD suficientes para modificar a política de acesso. Neste caso, o utilizador teria de ter um papel de contribuinte mais elevado.

### <a name="i-am-seeing-unknown-policy-error-what-does-that-mean"></a>Estou a ver um erro de "Política Desconhecida". O que significa?
Existem duas possibilidades diferentes de ver a política de acesso na secção Desconhecida:
* Pode haver um utilizador anterior que tenha tido acesso e por alguma razão que o utilizador não exista.
* Se a política de acesso for adicionada via powershell e a política de acesso for adicionada para a aplicação objectid em vez do principal serviço.

### <a name="how-can-i-assign-access-control-per-key-vault-object"></a>Como posso atribuir controlo de acesso por objeto de cofre chave? 

A disponibilidade da funcionalidade de controlo de acesso por segredo/chave/certificado será notificada aqui, [leia mais](https://feedback.azure.com/forums/906355-azure-key-vault/suggestions/32213176-per-secret-key-certificate-access-control)

### <a name="how-can-i-provide-key-vault-authenticate-using-access-control-policy"></a>Como posso fornecer a autenticação do cofre de chaves usando a política de controlo de acessos?

A forma mais simples de autenticar uma aplicação baseada em nuvem para o Key Vault é com uma identidade gerida; consulte [Autenticar para Azure Key Vault](authentication.md) para mais detalhes.
Se estiver a criar uma aplicação on-prem, a fazer desenvolvimento local ou de outra forma incapaz de utilizar uma identidade gerida, pode, em vez disso, registar manualmente um serviço principal e fornecer acesso ao cofre-chave utilizando uma política de controlo de acesso. Ver [Atribuir uma política de controlo de acesso.](assign-access-policy-portal.md)

### <a name="how-can-i-give-the-ad-group-access-to-the-key-vault"></a>Como posso dar ao grupo AD acesso ao cofre das chaves?

Dê permissões ao grupo AD para o seu cofre de chaves usando o comando Azure `az keyvault set-policy` CLI, ou o cmdlet Azure PowerShell Set-AzKeyVaultAccessPolicy. Ver [Atribuir uma política de acesso - CLI](assign-access-policy-cli.md) e atribuir uma política de acesso - [PowerShell](assign-access-policy-powershell.md).

A aplicação também precisa de pelo menos uma função de Gestão de Identidade e Acesso (IAM) atribuída ao cofre-chave. Caso contrário, não poderá iniciar sessão e falhará com direitos insuficientes de acesso à subscrição. Os grupos AD Azure com Identidades Geridas podem necessitar de até oito horas para refrescar tokens e tornar-se eficazes.

### <a name="how-can-i-redeploy-key-vault-with-arm-template-without-deleting-existing-access-policies"></a>Como posso recolocar o Key Vault com o modelo ARM sem eliminar as políticas de acesso existentes?

Atualmente, a reafectação do Key Vault elimina qualquer política de acesso no Key Vault e substitui-a pela política de acesso no modelo ARM. Não existe uma opção incremental para as políticas de acesso ao Cofre chave. Para preservar as políticas de acesso no Key Vault, é necessário ler as políticas de acesso existentes no Key Vault e preencher o modelo ARM com essas políticas para evitar falhas de acesso.

Outra opção que pode ajudar neste cenário é usar o Azure RBAC e papéis como alternativa às políticas de acesso. Com o Azure RBAC, pode voltar a implantar o cofre sem especificar novamente a apólice. Pode ler mais esta solução [aqui.](./rbac-guide.md)

### <a name="recommended-troubleshooting-steps-for-following-error-types"></a>Etapas recomendadas de resolução de problemas para seguir tipos de erro

* HTTP 401: Pedido Não Autenticado – [Passos de resolução de problemas](rest-error-codes.md#http-401-unauthenticated-request)
* HTTP 403: Permissões Insuficientes – [Passos de resolução de problemas](rest-error-codes.md#http-403-insufficient-permissions)
* HTTP 429: Demasiados Pedidos – [Passos de resolução de problemas](rest-error-codes.md#http-429-too-many-requests)
* Verifique se tem a permissão de acesso para o cofre de chaves: Consulte [atribuir uma política de acesso - CLI](assign-access-policy-cli.md), Atribuir uma política de acesso - [PowerShell,](assign-access-policy-powershell.md)ou [atribuir uma política de acesso - Portal](assign-access-policy-portal.md).
* Se tiver problemas com a autenticação no cofre de chave, utilize o [SDK de Autenticação](https://azure.github.io/azure-sdk/posts/2020-02-25/defaultazurecredentials.html)

### <a name="what-are-the-best-practices-i-should-implement-when-key-vault-is-getting-throttled"></a>Quais são as melhores práticas que devo implementar quando o cofre está a ser acelerado?
Siga as melhores práticas, documentadas [aqui](overview-throttling.md#how-to-throttle-your-app-in-response-to-service-limits)

## <a name="next-steps"></a>Passos Seguintes

Saiba como resolver os erros de autenticação do cofre de chaves: [Guia de resolução de problemas do cofre do cofre](rest-error-codes.md)de chaves .