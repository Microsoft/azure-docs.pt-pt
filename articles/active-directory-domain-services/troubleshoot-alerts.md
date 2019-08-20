---
title: 'Azure Active Directory Domain Services: Solucionar problemas de alertas | Microsoft Docs'
description: Solucionar problemas de alertas para Azure AD Domain Services
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: ''
editor: ''
ms.assetid: 54319292-6aa0-4a08-846b-e3c53ecca483
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/22/2019
ms.author: iainfou
ms.openlocfilehash: 5a8f3401de0dc452193efbcf79aef87a19aed081
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/19/2019
ms.locfileid: "69617078"
---
# <a name="azure-ad-domain-services---troubleshoot-alerts"></a>Azure AD Domain Services-solucionar problemas de alertas
Este artigo fornece guias de solução de problemas para quaisquer alertas que você possa ter em seu domínio gerenciado.


Escolha as etapas de solução de problemas que correspondem à ID ou à mensagem no alerta.

| **ID do alerta** | **Mensagem de alerta** | **Resolução** |
| --- | --- | :--- |
| AADDS001 | *LDAP Seguro pela Internet está habilitada para o domínio gerenciado. No entanto, o acesso à porta 636 não é bloqueado usando um grupo de segurança de rede. Isso pode expor as contas de usuário no domínio gerenciado para ataques de força bruta de senha.* | [Configuração de LDAP segura incorreta](alert-ldaps.md) |
| AADDS100 | *O diretório do Azure AD associado ao domínio gerenciado pode ter sido excluído. O domínio gerenciado não está mais em uma configuração com suporte. A Microsoft não pode monitorar, gerenciar, aplicar patch e sincronizar seu domínio gerenciado.* | [Diretório ausente](#aadds100-missing-directory) |
| AADDS101 | *Azure AD Domain Services não pode ser habilitada em um diretório Azure AD B2C.* | [Azure AD B2C está em execução neste diretório](#aadds101-azure-ad-b2c-is-running-in-this-directory) |
| AADDS102 | *Uma entidade de serviço necessária para que o Azure AD Domain Services funcione corretamente foi excluída do seu diretório do Azure AD. Essa configuração afeta a capacidade da Microsoft de monitorar, gerenciar, aplicar patches e sincronizar seu domínio gerenciado.* | [Entidade de serviço ausente](alert-service-principal.md) |
| AADDS103 | *O intervalo de endereços IP para a rede virtual na qual você habilitou Azure AD Domain Services está em um intervalo de IP público. Azure AD Domain Services deve ser habilitado em uma rede virtual com um intervalo de endereços IP privado. Essa configuração afeta a capacidade da Microsoft de monitorar, gerenciar, corrigir e sincronizar seu domínio gerenciado.* | [O endereço está em um intervalo de IP público](#aadds103-address-is-in-a-public-ip-range) |
| AADDS104 | *A Microsoft não consegue acessar os controladores de domínio para este domínio gerenciado. Isso pode acontecer se um NSG (grupo de segurança de rede) configurado em sua rede virtual bloquear o acesso ao domínio gerenciado. Outro motivo possível é se há uma rota definida pelo usuário que bloqueia o tráfego de entrada da Internet.* | [Erro de rede](alert-nsg.md) |
| AADDS105 | *A entidade de serviço com a ID de aplicativo "d87dcbc6-a371-462e-88e3-28ad15ec4e64" foi excluída e recriada. A recreação deixa por trás das permissões inconsistentes em Azure AD Domain Services recursos necessários para atender ao domínio gerenciado. A sincronização de senhas em seu domínio gerenciado pode ser afetada.* | [O aplicativo de sincronização de senha está desatualizado](alert-service-principal.md#alert-aadds105-password-synchronization-application-is-out-of-date) |
| AADDS106 | *Sua assinatura do Azure associada ao domínio gerenciado foi excluída.  Azure AD Domain Services requer uma assinatura ativa para continuar funcionando corretamente.* | [A assinatura do Azure não foi encontrada](#aadds106-your-azure-subscription-is-not-found) |
| AADDS107 | *Sua assinatura do Azure associada ao seu domínio gerenciado não está ativa.  Azure AD Domain Services requer uma assinatura ativa para continuar funcionando corretamente.* | [A assinatura do Azure está desabilitada](#aadds107-your-azure-subscription-is-disabled) |
| AADDS108 | *A assinatura usada pelo Azure AD Domain Services foi movida para outro diretório. Azure AD Domain Services precisa ter uma assinatura ativa no mesmo diretório para funcionar corretamente.* | [Diretórios movidos da assinatura](#aadds108-subscription-moved-directories) |
| AADDS109 | *Um recurso que é usado para o domínio gerenciado foi excluído. Esse recurso é necessário para que Azure AD Domain Services funcionem corretamente.* | [Um recurso foi excluído](#aadds109-resources-for-your-managed-domain-cannot-be-found) |
| AADDS110 | *A sub-rede selecionada para implantação de Azure AD Domain Services está cheia e não tem espaço para o controlador de domínio adicional que precisa ser criado.* | [A sub-rede está cheia](#aadds110-the-subnet-associated-with-your-managed-domain-is-full) |
| AADDS111 | *Uma entidade de serviço que o Azure AD Domain Services usa para atender seu domínio não está autorizada a gerenciar recursos na assinatura do Azure. A entidade de serviço precisa obter permissões para atender ao domínio gerenciado.* | [Entidade de serviço não autorizada](#aadds111-service-principal-unauthorized) |
| AADDS112 | *Identificamos que a sub-rede da rede virtual nesse domínio pode não ter endereços IP suficientes. Azure AD Domain Services precisa de pelo menos dois endereços IP disponíveis na sub-rede em que está habilitado. É recomendável ter pelo menos 3-5 endereços IP de reposição na sub-rede. Isso pode ter ocorrido se outras máquinas virtuais forem implantadas na sub-rede, esgotando assim o número de endereços IP disponíveis ou se houver uma restrição no número de endereços IP disponíveis na sub-rede.* | [Não há endereços IP suficientes](#aadds112-not-enough-ip-address-in-the-managed-domain) |
| AADDS113 | *Os recursos usados pelo Azure AD Domain Services foram detectados em um estado inesperado e não podem ser recuperados.* | [Os recursos são irrecuperáveis](#aadds113-resources-are-unrecoverable) |
| AADDS114 | *A sub-rede selecionada para implantação de Azure AD Domain Services é inválida e não pode ser usada.* | [Sub-rede inválida](#aadds114-subnet-invalid) |
| AADDS115 | *Um ou mais dos recursos de rede usados pelo domínio gerenciado não podem ser operados como o escopo de destino foi bloqueado.* | [Os recursos estão bloqueados](#aadds115-resources-are-locked) |
| AADDS116 | *Um ou mais dos recursos de rede usados pelo domínio gerenciado não podem ser operados devido à (s) restrição (ões) de política.* | [Os recursos são inutilizáveis](#aadds116-resources-are-unusable) |
| AADDS500 | *O domínio gerenciado foi sincronizado pela última vez com o Azure AD em [data]. Os usuários podem não conseguir entrar no domínio gerenciado ou as associações de grupo podem não estar sincronizadas com o Azure AD.* | [A sincronização não ocorreu há algum tempo](#aadds500-synchronization-has-not-completed-in-a-while) |
| AADDS501 | *O backup do domínio gerenciado foi feito pela última vez em [data].* | [Um backup não foi feito há algum tempo](#aadds501-a-backup-has-not-been-taken-in-a-while) |
| AADDS502 | *O certificado LDAP seguro para o domínio gerenciado expirará em [data].* | [Certificado LDAP seguro expirado](alert-ldaps.md#aadds502-secure-ldap-certificate-expiring) |
| AADDS503 | *O domínio gerenciado está suspenso porque a assinatura do Azure associada ao domínio não está ativa.* | [Suspensão devido a uma assinatura desabilitada](#aadds503-suspension-due-to-disabled-subscription) |
| AADDS504 | *O domínio gerenciado está suspenso devido a uma configuração inválida. O serviço não pôde gerenciar, aplicar patches ou atualizar os controladores de domínio para seu domínio gerenciado por um longo tempo.* | [Suspensão devido a uma configuração inválida](#aadds504-suspension-due-to-an-invalid-configuration) |



## <a name="aadds100-missing-directory"></a>AADDS100: Diretório ausente
**Mensagem de alerta:**

*O diretório do Azure AD associado ao domínio gerenciado pode ter sido excluído. O domínio gerenciado não está mais em uma configuração com suporte. A Microsoft não pode monitorar, gerenciar, aplicar patch e sincronizar seu domínio gerenciado.*

**Resolução:**

Esse erro geralmente é causado pela movimentação incorreta de sua assinatura do Azure para um novo diretório do Azure AD e pela exclusão do diretório do Azure AD antigo que ainda está associado ao Azure AD Domain Services.

Esse erro é irrecuperável. Para resolver o, você deve [excluir o domínio gerenciado existente](delete-aadds.md) e recriá-lo no novo diretório. Se você estiver tendo problemas para excluir, entre em contato com a equipe de produto do Azure Active Directory Domain Services [para obter suporte](contact-us.md).

## <a name="aadds101-azure-ad-b2c-is-running-in-this-directory"></a>AADDS101: Azure AD B2C está em execução neste diretório
**Mensagem de alerta:**

*Azure AD Domain Services não pode ser habilitada em um diretório Azure AD B2C.*

**Resolução:**

>[!NOTE]
>Para continuar a usar Azure AD Domain Services, você deve recriar sua instância de Azure AD Domain Services em um diretório não Azure AD B2C.

Para restaurar o serviço, siga estas etapas:

1. [Exclua seu domínio gerenciado](delete-aadds.md) do diretório do Azure ad existente.
2. Crie um novo diretório que não seja um diretório Azure AD B2C.
3. Siga o guia de [introdução](tutorial-create-instance.md) para recriar um domínio gerenciado.

## <a name="aadds103-address-is-in-a-public-ip-range"></a>AADDS103: O endereço está em um intervalo de IP público

**Mensagem de alerta:**

*O intervalo de endereços IP para a rede virtual na qual você habilitou Azure AD Domain Services está em um intervalo de IP público. Azure AD Domain Services deve ser habilitado em uma rede virtual com um intervalo de endereços IP privado. Essa configuração afeta a capacidade da Microsoft de monitorar, gerenciar, corrigir e sincronizar seu domínio gerenciado.*

**Resolução:**

> [!NOTE]
> Para resolver esse problema, você deve excluir seu domínio gerenciado existente e recriá-lo em uma rede virtual com um intervalo de endereços IP privado. Esse processo é interrompido.

Antes de começar, leia a seção **espaço de endereço IP privado IPv4** neste [artigo](https://en.wikipedia.org/wiki/Private_network#Private_IPv4_address_spaces).

Dentro da rede virtual, as máquinas podem fazer solicitações aos recursos do Azure que estão no mesmo intervalo de endereços IP que aquelas configuradas para a sub-rede. No entanto, como a rede virtual está configurada para esse intervalo, essas solicitações serão roteadas dentro da rede virtual e não alcançarão os recursos da Web pretendidos. Essa configuração pode levar a erros imprevisíveis com Azure AD Domain Services.

**Se você possui o intervalo de endereços IP na Internet configurado em sua rede virtual, esse alerta pode ser ignorado. No entanto, Azure AD Domain Services não pode confirmar para o [SLA](https://azure.microsoft.com/support/legal/sla/active-directory-ds/v1_0/)] com essa configuração, pois ela pode levar a erros imprevisíveis.**


1. [Exclua seu domínio gerenciado](delete-aadds.md) do seu diretório.
2. Corrigir o intervalo de endereços IP para a sub-rede
   1. Navegue até a [página redes virtuais na portal do Azure](https://portal.azure.com/?feature.canmodifystamps=true&Microsoft_AAD_DomainServices=preview#blade/HubsExtension/Resources/resourceType/Microsoft.Network%2FvirtualNetworks).
   2. Selecione a rede virtual que você planeja usar para Azure AD Domain Services.
   3. Clique em **espaço de endereço** em configurações
   4. Atualize o intervalo de endereços clicando no intervalo de endereços existente e editando-o ou adicionando um intervalo de endereços adicional. Verifique se o novo intervalo de endereços está em um intervalo de IP privado. Guarde as alterações.
   5. Clique em **sub-redes** na navegação à esquerda.
   6. Clique na sub-rede que você deseja editar na tabela.
   7. Atualize o intervalo de endereços e salve as alterações.
3. Siga [as introdução usando o guia de Azure AD Domain Services](tutorial-create-instance.md) para recriar o domínio gerenciado. Certifique-se de escolher uma rede virtual com um intervalo de endereços IP privado.
4. Para ingressar em domínio em suas máquinas virtuais para o novo domínio, siga [este guia](join-windows-vm.md).
8. Para garantir que o alerta seja resolvido, verifique a integridade do seu domínio em duas horas.

## <a name="aadds106-your-azure-subscription-is-not-found"></a>AADDS106: Sua assinatura do Azure não foi encontrada

**Mensagem de alerta:**

*Sua assinatura do Azure associada ao domínio gerenciado foi excluída.  Azure AD Domain Services requer uma assinatura ativa para continuar funcionando corretamente.*

**Resolução:**

Azure AD Domain Services requer uma assinatura para funcionar e não pode ser movida para uma assinatura diferente. Como a assinatura do Azure à qual o domínio gerenciado foi associado foi excluída, você precisará recriar uma assinatura do Azure e Azure AD Domain Services.

1. Crie uma subscrição do Azure
2. [Exclua seu domínio gerenciado](delete-aadds.md) do diretório do Azure ad existente.
3. Siga o guia de [introdução](tutorial-create-instance.md) para recriar um domínio gerenciado.

## <a name="aadds107-your-azure-subscription-is-disabled"></a>AADDS107: A subscrição do Azure está desativada

**Mensagem de alerta:**

*Sua assinatura do Azure associada ao seu domínio gerenciado não está ativa.  Azure AD Domain Services requer uma assinatura ativa para continuar funcionando corretamente.*

**Resolução:**


1. [Renove sua assinatura do Azure](https://docs.microsoft.com/azure/billing/billing-subscription-become-disable).
2. Depois que a assinatura for renovada, Azure AD Domain Services receberá uma notificação do Azure para reabilitar seu domínio gerenciado.

## <a name="aadds108-subscription-moved-directories"></a>AADDS108: Diretórios movidos da assinatura

**Mensagem de alerta:**

*A assinatura usada pelo Azure AD Domain Services foi movida para outro diretório. Azure AD Domain Services precisa ter uma assinatura ativa no mesmo diretório para funcionar corretamente.*

**Resolução:**

Você pode mover a assinatura associada ao Azure AD Domain Services de volta para o diretório anterior, ou você precisa [excluir o domínio gerenciado](delete-aadds.md) do diretório existente e recriá-lo no diretório escolhido (com uma nova assinatura ou Altere o diretório no qual sua instância do Azure AD Domain Services está).

## <a name="aadds109-resources-for-your-managed-domain-cannot-be-found"></a>AADDS109: Os recursos para seu domínio gerenciado não podem ser encontrados

**Mensagem de alerta:**

*Um recurso que é usado para o domínio gerenciado foi excluído. Esse recurso é necessário para que Azure AD Domain Services funcionem corretamente.*

**Resolução:**

Azure AD Domain Services cria recursos específicos durante a implantação a fim de funcionar corretamente, incluindo endereços IP públicos, NICs e um balanceador de carga. Se qualquer um dos nomes for excluído, isso fará com que o domínio gerenciado esteja em um estado sem suporte e impeça que o domínio seja gerenciado. Esse alerta é encontrado quando alguém que é capaz de editar os Azure AD Domain Services recursos exclui um recurso necessário. As etapas a seguir descrevem como restaurar seu domínio gerenciado.

1. Navegue até a página de Azure AD Domain Services integridade
   1.    Vá para a [página de Azure AD Domain Services](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.AAD%2FdomainServices) no portal do Azure.
   2.    No painel de navegação à esquerda, clique em **integridade**
2. Verifique se o alerta tem menos de 4 horas de idade
   1.    Na página integridade, clique no alerta com a ID **AADDS109**
   2.    O alerta terá um carimbo de data/hora para quando ele foi encontrado pela primeira vez. Se esse carimbo de data/hora for inferior a 4 horas, haverá uma chance de que Azure AD Domain Services possa recriar o recurso excluído.
3. Se o alerta tiver mais de 4 horas de idade, o domínio gerenciado estará em um estado irrecuperável. Você deve excluir e recriar Azure AD Domain Services.


## <a name="aadds110-the-subnet-associated-with-your-managed-domain-is-full"></a>AADDS110: A sub-rede associada ao seu domínio gerenciado está cheia

**Mensagem de alerta:**

*A sub-rede selecionada para implantação de Azure AD Domain Services está cheia e não tem espaço para o controlador de domínio adicional que precisa ser criado.*

**Resolução:**

Esse erro é irrecuperável. Para resolver o, você deve [excluir seu domínio gerenciado existente](delete-aadds.md) e recriar [seu domínio gerenciado](tutorial-create-instance.md)

## <a name="aadds111-service-principal-unauthorized"></a>AADDS111: Entidade de serviço não autorizada

**Mensagem de alerta:**

*Uma entidade de serviço que o Azure AD Domain Services usa para atender seu domínio não está autorizada a gerenciar recursos na assinatura do Azure. A entidade de serviço precisa obter permissões para atender ao domínio gerenciado.*

**Resolução:**

Nossas entidades de serviço precisam de acesso para poder gerenciar e criar recursos em seu domínio gerenciado. Alguém negou o acesso à entidade de serviço e agora não consegue gerenciar recursos. Siga as etapas para conceder acesso à sua entidade de serviço.

1. Leia sobre [o controle RBAC e como conceder acesso a aplicativos no portal do Azure](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)
2. Examine o acesso que a entidade de serviço com a ```abba844e-bc0e-44b0-947a-dc74e5d09022``` ID e conceda o acesso que foi negado em uma data anterior.


## <a name="aadds112-not-enough-ip-address-in-the-managed-domain"></a>AADDS112: Não há endereço IP suficiente no domínio gerenciado

**Mensagem de alerta:**

*Identificamos que a sub-rede da rede virtual nesse domínio pode não ter endereços IP suficientes. Azure AD Domain Services precisa de pelo menos dois endereços IP disponíveis na sub-rede em que está habilitado. É recomendável ter pelo menos 3-5 endereços IP de reposição na sub-rede. Isso pode ter ocorrido se outras máquinas virtuais forem implantadas na sub-rede, esgotando assim o número de endereços IP disponíveis ou se houver uma restrição no número de endereços IP disponíveis na sub-rede.*

**Resolução:**

1. Exclua seu domínio gerenciado do seu locatário.
2. Corrigir o intervalo de endereços IP para a sub-rede
   1. Navegue até a [página redes virtuais na portal do Azure](https://portal.azure.com/?feature.canmodifystamps=true&Microsoft_AAD_DomainServices=preview#blade/HubsExtension/Resources/resourceType/Microsoft.Network%2FvirtualNetworks).
   2. Selecione a rede virtual que você planeja usar para Azure AD Domain Services.
   3. Clique em **espaço de endereço** em configurações
   4. Atualize o intervalo de endereços clicando no intervalo de endereços existente e editando-o ou adicionando um intervalo de endereços adicional. Guarde as alterações.
   5. Clique em **sub-redes** na navegação à esquerda.
   6. Clique na sub-rede que você deseja editar na tabela.
   7. Atualize o intervalo de endereços e salve as alterações.
3. Siga [as introdução usando o guia de Azure AD Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-getting-started) para recriar o domínio gerenciado. Certifique-se de escolher uma rede virtual com um intervalo de endereços IP privado.
4. Para ingressar em domínio em suas máquinas virtuais para o novo domínio, siga [este guia](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-admin-guide-join-windows-vm-portal).
5. Verifique a integridade do seu domínio em duas horas para garantir que você concluiu as etapas corretamente.

## <a name="aadds113-resources-are-unrecoverable"></a>AADDS113: Os recursos são irrecuperáveis

**Mensagem de alerta:**

*Os recursos usados pelo Azure AD Domain Services foram detectados em um estado inesperado e não podem ser recuperados.*

**Resolução:**

Esse erro é irrecuperável. Para resolver o, você deve [excluir seu domínio gerenciado existente](delete-aadds.md) e recriar [seu domínio gerenciado](tutorial-create-instance.md).

## <a name="aadds114-subnet-invalid"></a>AADDS114: Sub-rede inválida

**Mensagem de alerta:**

*A sub-rede selecionada para implantação de Azure AD Domain Services é inválida e não pode ser usada.*

**Resolução:**

Esse erro é irrecuperável. Para resolver o, você deve [excluir seu domínio gerenciado existente](delete-aadds.md) e recriar [seu domínio gerenciado](tutorial-create-instance.md).

## <a name="aadds115-resources-are-locked"></a>AADDS115: Os recursos estão bloqueados

**Mensagem de alerta:**

*Um ou mais dos recursos de rede usados pelo domínio gerenciado não podem ser operados como o escopo de destino foi bloqueado.*

**Resolução:**

1.  Examine os logs de operação do Resource Manager nos recursos de rede (isso deve fornecer informações sobre qual bloqueio está impedindo a modificação).
2.  Remova os bloqueios nos recursos para que a entidade de serviço Azure AD Domain Services possa operar neles.

## <a name="aadds116-resources-are-unusable"></a>AADDS116: Os recursos são inutilizáveis

**Mensagem de alerta:**

*Um ou mais dos recursos de rede usados pelo domínio gerenciado não podem ser operados devido à (s) restrição (ões) de política.*

**Resolução:**

1.  Examine os logs de operação do Resource Manager nos recursos de rede para seu domínio gerenciado.
2.  Enfraquece as restrições de política nos recursos para que a entidade de serviço AAD-DS possa operar neles.



## <a name="aadds500-synchronization-has-not-completed-in-a-while"></a>AADDS500: A sincronização não foi concluída há algum tempo

**Mensagem de alerta:**

*O domínio gerenciado foi sincronizado pela última vez com o Azure AD em [data]. Os usuários podem não conseguir entrar no domínio gerenciado ou as associações de grupo podem não estar sincronizadas com o Azure AD.*

**Resolução:**

[Verifique a integridade do seu domínio](check-health.md) em busca de alertas que possam indicar problemas na configuração do seu domínio gerenciado. Às vezes, problemas com sua configuração podem bloquear a capacidade da Microsoft de sincronizar seu domínio gerenciado. Se você puder resolver alertas, aguarde duas horas e verifique se a sincronização foi concluída.

Aqui estão alguns motivos comuns pelos quais a sincronização é interrompida em domínios gerenciados:
- A conexão de rede está bloqueada no domínio gerenciado. Para saber mais sobre como verificar se há problemas na rede, leia [](alert-nsg.md) como solucionar [os requisitos de rede e os grupos de segurança de rede para Azure AD Domain Services](network-considerations.md).
-  A sincronização de senha nunca foi configurada ou concluída. Para configurar a sincronização de senha, leia [Este artigo](tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds).

## <a name="aadds501-a-backup-has-not-been-taken-in-a-while"></a>AADDS501: Um backup não foi feito há algum tempo

**Mensagem de alerta:**

*O backup do domínio gerenciado foi feito pela última vez em [data].*

**Resolução:**

[Verifique a integridade do seu domínio](check-health.md) em busca de alertas que possam indicar problemas na configuração do seu domínio gerenciado. Às vezes, problemas com sua configuração podem bloquear a capacidade da Microsoft de fazer backup de seu domínio gerenciado. Se você puder resolver alertas, aguarde duas horas e verifique se o backup foi concluído.


## <a name="aadds503-suspension-due-to-disabled-subscription"></a>AADDS503: Suspensão devido a uma assinatura desabilitada

**Mensagem de alerta:**

*O domínio gerenciado está suspenso porque a assinatura do Azure associada ao domínio não está ativa.*

**Resolução:**

> [!WARNING]
> Se seu domínio gerenciado for suspenso por um longo período de tempo, ele estará em perigo de ser excluído. É melhor abordar a suspensão o mais rápido possível. Para ler mais, visite [Este artigo](suspension.md).

Para restaurar seu serviço, [renove sua assinatura do Azure](https://docs.microsoft.com/azure/billing/billing-subscription-become-disable) associada ao seu domínio gerenciado.

## <a name="aadds504-suspension-due-to-an-invalid-configuration"></a>AADDS504: Suspensão devido a uma configuração inválida

**Mensagem de alerta:**

*O domínio gerenciado está suspenso devido a uma configuração inválida. O serviço não pôde gerenciar, aplicar patches ou atualizar os controladores de domínio para seu domínio gerenciado por um longo tempo.*

**Resolução:**

> [!WARNING]
> Se seu domínio gerenciado for suspenso por um longo período de tempo, ele estará em perigo de ser excluído. É melhor abordar a suspensão o mais rápido possível. Para ler mais, visite [Este artigo](suspension.md).

[Verifique a integridade do seu domínio](check-health.md) em busca de alertas que possam indicar problemas na configuração do seu domínio gerenciado. Se você puder resolver qualquer um desses alertas, faça isso. Depois, entre em contato com o suporte para reabilitar sua assinatura.


## <a name="contact-us"></a>Contacte-nos
Entre em contato com a equipe de produto Azure Active Directory Domain Services para [compartilhar comentários ou para obter suporte](contact-us.md).
