---
title: Perguntas frequentes sobre Azure AD Domain Services | Microsoft Docs
description: Leia e entenda algumas das perguntas frequentes sobre configuração, administração e disponibilidade para Azure Active Directory Domain Services
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 48731820-9e8c-4ec2-95e8-83dba1e58775
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 09/03/2019
ms.author: iainfou
ms.openlocfilehash: 4add98ae092359c6f070bfc93b69257894f44e48
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/18/2019
ms.locfileid: "72596424"
---
# <a name="frequently-asked-questions-faqs"></a>Perguntas frequentes (FAQs)

Esta página responde a perguntas frequentes sobre Azure Active Directory Domain Services.

## <a name="configuration"></a>Configuração

* [Posso criar vários domínios gerenciados para um único diretório do AD do Azure?](#can-i-create-multiple-managed-domains-for-a-single-azure-ad-directory)
* [Posso habilitar Azure AD Domain Services em uma rede virtual clássica?](#can-i-enable-azure-ad-domain-services-in-a-classic-virtual-network)
* [Posso habilitar Azure AD Domain Services em uma rede virtual Azure Resource Manager?](#can-i-enable-azure-ad-domain-services-in-an-azure-resource-manager-virtual-network)
* [Posso migrar meu domínio gerenciado existente de uma rede virtual clássica para uma rede virtual do Resource Manager?](#can-i-migrate-my-existing-managed-domain-from-a-classic-virtual-network-to-a-resource-manager-virtual-network)
* [Posso habilitar Azure AD Domain Services em uma assinatura do Azure CSP (provedor de soluções na nuvem)?](#can-i-enable-azure-ad-domain-services-in-an-azure-csp-cloud-solution-provider-subscription)
* [Posso habilitar Azure AD Domain Services em um diretório federado do AD do Azure? Não sincronizar hashes de senha para o Azure AD. Posso habilitar Azure AD Domain Services para este diretório?](#can-i-enable-azure-ad-domain-services-in-a-federated-azure-ad-directory-i-do-not-synchronize-password-hashes-to-azure-ad-can-i-enable-azure-ad-domain-services-for-this-directory)
* [Posso disponibilizar Azure AD Domain Services em várias redes virtuais na minha assinatura?](#can-i-make-azure-ad-domain-services-available-in-multiple-virtual-networks-within-my-subscription)
* [Posso habilitar Azure AD Domain Services usando o PowerShell?](#can-i-enable-azure-ad-domain-services-using-powershell)
* [Posso habilitar Azure AD Domain Services usando um modelo do Resource Manager?](#can-i-enable-azure-ad-domain-services-using-a-resource-manager-template)
* [Posso adicionar controladores de domínio a um domínio gerenciado Azure AD Domain Services?](#can-i-add-domain-controllers-to-an-azure-ad-domain-services-managed-domain)
* [Os usuários convidados podem ser convidados para o meu diretório usar Azure AD Domain Services?](#can-guest-users-invited-to-my-directory-use-azure-ad-domain-services)
* [Posso mover um domínio gerenciado Azure AD Domain Services existente para uma assinatura, um grupo de recursos, uma região ou uma rede virtual diferente?](#can-i-move-an-existing-azure-ad-domain-services-managed-domain-to-a-different-subscription-resource-group-region-or-virtual-network)

### <a name="can-i-create-multiple-managed-domains-for-a-single-azure-ad-directory"></a>Posso criar vários domínios gerenciados para um único diretório do AD do Azure?
Não. Você só pode criar um único domínio gerenciado pelo Azure AD Domain Services para um único diretório do Azure AD.

### <a name="can-i-enable-azure-ad-domain-services-in-a-classic-virtual-network"></a>Posso habilitar Azure AD Domain Services em uma rede virtual clássica?
As redes virtuais clássicas não têm suporte para novas implantações. Os domínios gerenciados existentes implantados em redes virtuais clássicas continuam com suporte.

### <a name="can-i-enable-azure-ad-domain-services-in-an-azure-resource-manager-virtual-network"></a>Posso habilitar Azure AD Domain Services em uma rede virtual Azure Resource Manager?
Sim. Azure AD Domain Services pode ser habilitado em uma rede virtual Azure Resource Manager. As redes virtuais clássicas do Azure não são mais compatíveis com o quando você cria um novo domínio gerenciado.

### <a name="can-i-migrate-my-existing-managed-domain-from-a-classic-virtual-network-to-a-resource-manager-virtual-network"></a>Posso migrar meu domínio gerenciado existente de uma rede virtual clássica para uma rede virtual do Resource Manager?
Sim, esse recurso está em versão prévia. Para obter mais informações, consulte [migrar Azure AD Domain Services do modelo de rede virtual clássica para o Gerenciador de recursos (versão prévia)] [migrar de-Classic-vnet.md]

### <a name="can-i-enable-azure-ad-domain-services-in-an-azure-csp-cloud-solution-provider-subscription"></a>Posso habilitar Azure AD Domain Services em uma assinatura do Azure CSP (provedor de soluções na nuvem)?
Sim. Para obter mais informações, consulte [como habilitar Azure AD Domain Services em assinaturas do Azure CSP](csp.md).

### <a name="can-i-enable-azure-ad-domain-services-in-a-federated-azure-ad-directory-i-do-not-synchronize-password-hashes-to-azure-ad-can-i-enable-azure-ad-domain-services-for-this-directory"></a>Posso habilitar Azure AD Domain Services em um diretório federado do AD do Azure? Não sincronizar hashes de senha para o Azure AD. Posso habilitar Azure AD Domain Services para este diretório?
Não. Para autenticar usuários via NTLM ou Kerberos, Azure AD Domain Services precisa acessar os hashes de senha de contas de usuário. Em um diretório federado, os hashes de senha não são armazenados no diretório do AD do Azure. Portanto, Azure AD Domain Services não funciona com esses diretórios do Azure AD.

### <a name="can-i-make-azure-ad-domain-services-available-in-multiple-virtual-networks-within-my-subscription"></a>Posso disponibilizar Azure AD Domain Services em várias redes virtuais na minha assinatura?
O serviço em si não dá suporte direto a esse cenário. Seu domínio gerenciado está disponível apenas em uma rede virtual por vez. No entanto, você pode configurar a conectividade entre várias redes virtuais para expor Azure AD Domain Services a outras redes virtuais. Para obter mais informações, consulte [como conectar redes virtuais no Azure usando gateways de VPN](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md) ou [emparelhamento de rede virtual](../virtual-network/virtual-network-peering-overview.md).

### <a name="can-i-enable-azure-ad-domain-services-using-powershell"></a>Posso habilitar Azure AD Domain Services usando o PowerShell?
Sim. Para obter mais informações, consulte [como habilitar o Azure AD Domain Services usando o PowerShell](powershell-create-instance.md).

### <a name="can-i-enable-azure-ad-domain-services-using-a-resource-manager-template"></a>Posso habilitar Azure AD Domain Services usando um modelo do Resource Manager?
Não, atualmente, não é possível habilitar Azure AD Domain Services usando um modelo. Para obter uma abordagem com script, consulte [como habilitar Azure AD Domain Services usando o PowerShell](powershell-create-instance.md).

### <a name="can-i-add-domain-controllers-to-an-azure-ad-domain-services-managed-domain"></a>Posso adicionar controladores de domínio a um domínio gerenciado Azure AD Domain Services?
Não. O domínio fornecido pelo Azure AD Domain Services é um domínio gerenciado. Você não precisa provisionar, configurar ou gerenciar controladores de domínio para esse domínio. Essas atividades de gerenciamento são fornecidas como um serviço pela Microsoft. Portanto, você não pode adicionar mais controladores de domínio (leitura/gravação ou somente leitura) para o domínio gerenciado.

### <a name="can-guest-users-invited-to-my-directory-use-azure-ad-domain-services"></a>Os usuários convidados podem ser convidados para o meu diretório usar Azure AD Domain Services?
Não. Os usuários convidados convidados para seu diretório do Azure AD usando o processo de convite [B2B do Azure ad](../active-directory/active-directory-b2b-what-is-azure-ad-b2b.md) são sincronizados em seu Azure AD Domain Services domínio gerenciado. No entanto, as senhas para esses usuários não são armazenadas em seu diretório do Azure AD. Portanto, Azure AD Domain Services não tem como sincronizar os hashes NTLM e Kerberos para esses usuários em seu domínio gerenciado. Tais usuários não podem entrar ou ingressar computadores no domínio gerenciado.

### <a name="can-i-move-an-existing-azure-ad-domain-services-managed-domain-to-a-different-subscription-resource-group-region-or-virtual-network"></a>Posso mover um domínio gerenciado Azure AD Domain Services existente para uma assinatura, um grupo de recursos, uma região ou uma rede virtual diferente?
Não. Depois de criar um Azure AD Domain Services domínio gerenciado, você não poderá mover a instância para um grupo de recursos diferente, rede virtual, assinatura, etc. Tome cuidado para selecionar a assinatura, o grupo de recursos, a região e a rede virtual mais apropriados ao implantar a instância de AD DS do Azure.

## <a name="administration-and-operations"></a>Administração e operações

* [Posso me conectar ao controlador de domínio para meu domínio gerenciado usando Área de Trabalho Remota?](#can-i-connect-to-the-domain-controller-for-my-managed-domain-using-remote-desktop)
* [Habilitei Azure AD Domain Services. Qual conta de usuário eu uso para ingressar em computadores a este domínio?](#ive-enabled-azure-ad-domain-services-what-user-account-do-i-use-to-domain-join-machines-to-this-domain)
* [Tenho privilégios de administrador de domínio para o domínio gerenciado fornecido pelo Azure AD Domain Services?](#do-i-have-domain-administrator-privileges-for-the-managed-domain-provided-by-azure-ad-domain-services)
* [Posso modificar associações de grupo usando LDAP ou outras ferramentas administrativas do AD em domínios gerenciados?](#can-i-modify-group-memberships-using-ldap-or-other-ad-administrative-tools-on-managed-domains)
* [Quanto tempo leva para que as alterações feitas no meu diretório do Azure AD fiquem visíveis no meu domínio gerenciado?](#how-long-does-it-take-for-changes-i-make-to-my-azure-ad-directory-to-be-visible-in-my-managed-domain)
* [Posso estender o esquema do domínio gerenciado fornecido pelo Azure AD Domain Services?](#can-i-extend-the-schema-of-the-managed-domain-provided-by-azure-ad-domain-services)
* [Posso modificar ou adicionar registros DNS em meu domínio gerenciado?](#can-i-modify-or-add-dns-records-in-my-managed-domain)
* [Qual é a política de tempo de vida de senha em um domínio gerenciado?](#what-is-the-password-lifetime-policy-on-a-managed-domain)
* [Azure AD Domain Services fornece proteção de bloqueio de conta do AD?](#does-azure-ad-domain-services-provide-ad-account-lockout-protection)

### <a name="can-i-connect-to-the-domain-controller-for-my-managed-domain-using-remote-desktop"></a>Posso me conectar ao controlador de domínio para meu domínio gerenciado usando Área de Trabalho Remota?
Não. Você não tem permissões para se conectar a controladores de domínio para o domínio gerenciado usando o Área de Trabalho Remota. Os membros do grupo de *Administradores do AAD DC* podem administrar o domínio gerenciado usando ferramentas de administração do AD, como o ADAC (centro de administração do Active Directory) ou o AD PowerShell. Essas ferramentas são instaladas usando o recurso *ferramentas de administração de servidor remoto* em um Windows Server ingressado no domínio gerenciado. Para obter mais informações, consulte [criar uma VM de gerenciamento para configurar e administrar um Azure AD Domain Services domínio gerenciado](tutorial-create-management-vm.md).

### <a name="ive-enabled-azure-ad-domain-services-what-user-account-do-i-use-to-domain-join-machines-to-this-domain"></a>Habilitei Azure AD Domain Services. Qual conta de usuário eu uso para ingressar em computadores a este domínio?
Os membros do grupo administrativo *Administradores de controlador* de domínio do AAD podem ingressar em computadores. Além disso, os membros desse grupo recebem acesso à área de trabalho remota para computadores que ingressaram no domínio.

### <a name="do-i-have-domain-administrator-privileges-for-the-managed-domain-provided-by-azure-ad-domain-services"></a>Tenho privilégios de administrador de domínio para o domínio gerenciado fornecido pelo Azure AD Domain Services?
Não. Você não recebe privilégios administrativos no domínio gerenciado. Os privilégios de administrador de *domínio* e de *administrador corporativo* não estão disponíveis para uso no domínio. Os membros dos grupos de administrador de domínio ou de administrador corporativo na sua Active Directory local também não recebem privilégios de administrador de domínio/empresa no domínio gerenciado.

### <a name="can-i-modify-group-memberships-using-ldap-or-other-ad-administrative-tools-on-managed-domains"></a>Posso modificar associações de grupo usando LDAP ou outras ferramentas administrativas do AD em domínios gerenciados?
Não. As associações de grupo não podem ser modificadas em domínios atendidos por Azure AD Domain Services. O mesmo se aplica a atributos de usuário. Você pode alterar as associações de grupo ou os atributos de usuário no Azure AD ou no seu domínio local. As alterações são sincronizadas automaticamente para Azure AD Domain Services.

### <a name="how-long-does-it-take-for-changes-i-make-to-my-azure-ad-directory-to-be-visible-in-my-managed-domain"></a>Quanto tempo leva para que as alterações feitas no meu diretório do Azure AD fiquem visíveis no meu domínio gerenciado?
As alterações feitas no diretório do Azure AD usando a interface do usuário do Azure AD ou o PowerShell são sincronizadas automaticamente com o domínio gerenciado. Esse processo de sincronização é executado em segundo plano. Não há nenhum período de tempo definido para que essa sincronização conclua todas as alterações de objeto.

### <a name="can-i-extend-the-schema-of-the-managed-domain-provided-by-azure-ad-domain-services"></a>Posso estender o esquema do domínio gerenciado fornecido pelo Azure AD Domain Services?
Não. O esquema é administrado pela Microsoft para o domínio gerenciado. As extensões de esquema não têm suporte pelo Azure AD Domain Services.

### <a name="can-i-modify-or-add-dns-records-in-my-managed-domain"></a>Posso modificar ou adicionar registros DNS em meu domínio gerenciado?
Sim. Os membros do grupo de *Administradores do AAD DC* recebem privilégios de *administrador de DNS* para modificar os registros DNS no domínio gerenciado. Esses usuários podem usar o console do Gerenciador DNS em um computador que executa o Windows Server ingressado no domínio gerenciado, para gerenciar o DNS. Para usar o console do Gerenciador DNS, instale as *Ferramentas do servidor DNS*, que faz parte do *ferramentas de administração de servidor remoto* recurso opcional no servidor. Para obter mais informações, consulte [administrar o DNS em um domínio gerenciado Azure AD Domain Services](manage-dns.md).

### <a name="what-is-the-password-lifetime-policy-on-a-managed-domain"></a>Qual é a política de tempo de vida de senha em um domínio gerenciado?
O tempo de vida padrão de senha em um domínio gerenciado Azure AD Domain Services é de 90 dias. Esse tempo de vida da senha não está sincronizado com o tempo de vida da senha configurado no Azure AD. Portanto, você pode ter uma situação em que as senhas dos usuários expiram em seu domínio gerenciado, mas ainda são válidas no Azure AD. Nesses cenários, os usuários precisam alterar sua senha no Azure AD e a nova senha será sincronizada com o domínio gerenciado. Além disso, os atributos de *senha-não expirar* e *usuário-deve-alterar-senha-at-login* para contas de usuário não são sincronizados com o domínio gerenciado.

### <a name="does-azure-ad-domain-services-provide-ad-account-lockout-protection"></a>Azure AD Domain Services fornece proteção de bloqueio de conta do AD?
Sim. Cinco tentativas de senha inválidas em 2 minutos no domínio gerenciado fazem com que uma conta de usuário seja bloqueada por 30 minutos. Após 30 minutos, a conta de usuário será desbloqueada automaticamente. Tentativas de senha inválidas no domínio gerenciado não bloqueiam a conta de usuário no Azure AD. A conta de usuário é bloqueada somente dentro de seu Azure AD Domain Services domínio gerenciado. Para obter mais informações, consulte [políticas de bloqueio de senha e conta em domínios gerenciados](password-policy.md).

## <a name="billing-and-availability"></a>Cobrança e disponibilidade

* [Azure AD Domain Services é um serviço pago?](#is-azure-ad-domain-services-a-paid-service)
* [Há uma avaliação gratuita para o serviço?](#is-there-a-free-trial-for-the-service)
* [Posso pausar um Azure AD Domain Services domínio gerenciado?](#can-i-pause-an-azure-ad-domain-services-managed-domain)
* [Posso fazer failover de Azure AD Domain Services para outra região para um evento de recuperação de desastre?](#can-i-pause-an-azure-ad-domain-services-managed-domain)
* [Posso obter Azure AD Domain Services como parte do EMS (Enterprise Mobility Suite)? Preciso de Azure AD Premium para usar Azure AD Domain Services?](#can-i-failover-azure-ad-domain-services-to-another-region-for-a-dr-event)
* [Em quais regiões do Azure o serviço está disponível?](#can-i-get-azure-ad-domain-services-as-part-of-enterprise-mobility-suite-ems-do-i-need-azure-ad-premium-to-use-azure-ad-domain-services)

### <a name="is-azure-ad-domain-services-a-paid-service"></a>Azure AD Domain Services é um serviço pago?
Sim. Para obter mais informações, veja a [página de preços](https://azure.microsoft.com/pricing/details/active-directory-ds/).

### <a name="is-there-a-free-trial-for-the-service"></a>Há uma avaliação gratuita para o serviço?
Azure AD Domain Services está incluído na avaliação gratuita do Azure. Você pode se inscrever para uma [avaliação gratuita de um mês do Azure](https://azure.microsoft.com/pricing/free-trial/).

### <a name="can-i-pause-an-azure-ad-domain-services-managed-domain"></a>Posso pausar um Azure AD Domain Services domínio gerenciado?
Não. Depois de habilitar um Azure AD Domain Services domínio gerenciado, o serviço estará disponível em sua rede virtual selecionada até que você exclua o domínio gerenciado. Não há como pausar o serviço. A cobrança continua por hora até que você exclua o domínio gerenciado.

### <a name="can-i-failover-azure-ad-domain-services-to-another-region-for-a-dr-event"></a>Posso fazer failover de Azure AD Domain Services para outra região para um evento de recuperação de desastre?
Não. No momento, o Azure AD Domain Services não fornece um modelo de implantação com redundância geográfica. It'is limitado a uma única rede virtual em uma região do Azure. Se você quiser utilizar várias regiões do Azure, será necessário executar seus controladores de Domínio do Active Directory em VMs IaaS do Azure. As diretrizes de arquitetura podem ser encontradas [aqui](https://docs.microsoft.com/azure/architecture/reference-architectures/identity/adds-extend-domain).

### <a name="can-i-get-azure-ad-domain-services-as-part-of-enterprise-mobility-suite-ems-do-i-need-azure-ad-premium-to-use-azure-ad-domain-services"></a>Posso obter Azure AD Domain Services como parte do EMS (Enterprise Mobility Suite)? Preciso de Azure AD Premium para usar Azure AD Domain Services?
Não. Azure AD Domain Services é um serviço pago conforme o uso do Azure e não faz parte do EMS. Azure AD Domain Services pode ser usado com todas as edições do Azure AD (gratuito e Premium). Você é cobrado por hora, dependendo do uso.

### <a name="what-azure-regions-is-the-service-available-in"></a>Em quais regiões do Azure o serviço está disponível?
Consulte a página [Serviços do Azure por região](https://azure.microsoft.com/regions/#services/) para ver uma lista das regiões do azure onde Azure AD Domain Services está disponível.

## <a name="troubleshooting"></a>Resolução de problemas

Consulte o [Guia de solução de problemas](troubleshoot.md) para obter soluções para problemas comuns com a configuração ou administração de Azure AD Domain Services.

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre Azure AD Domain Services, confira [o que é Azure Active Directory Domain Services?](overview.md).

Para começar, consulte [criar e configurar uma instância de Azure Active Directory Domain Services](tutorial-create-instance.md).
