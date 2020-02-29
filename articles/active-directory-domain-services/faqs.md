---
title: Perguntas frequentes sobre os Serviços de Domínio Azure AD  Microsoft Docs
description: Leia e compreenda algumas das perguntas frequentes em torno da configuração, administração e disponibilidade para serviços de domínio de diretório ativo Azure
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 48731820-9e8c-4ec2-95e8-83dba1e58775
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 01/21/2020
ms.author: iainfou
ms.openlocfilehash: 3abd9835c1cf750b926f49442f3e34e96dc9c865
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/28/2020
ms.locfileid: "77917361"
---
# <a name="frequently-asked-questions-faqs"></a>Perguntas frequentes (Perguntas Frequentes)

Esta página responde frequentemente a perguntas sobre os Serviços de Domínio de Diretório Ativo Azure.

## <a name="configuration"></a>Configuração

* [Posso criar vários domínios geridos para um único diretório da AD Azure?](#can-i-create-multiple-managed-domains-for-a-single-azure-ad-directory)
* [Posso ativar os Serviços de Domínio Azure AD numa rede virtual clássica?](#can-i-enable-azure-ad-domain-services-in-a-classic-virtual-network)
* [Posso ativar os Serviços de Domínio Azure AD numa rede virtual do Gestor de Recursos Azure?](#can-i-enable-azure-ad-domain-services-in-an-azure-resource-manager-virtual-network)
* [Posso migrar o meu domínio gerido existente de uma rede virtual clássica para uma rede virtual do Gestor de Recursos?](#can-i-migrate-my-existing-managed-domain-from-a-classic-virtual-network-to-a-resource-manager-virtual-network)
* [Posso ativar os Serviços de Domínio Azure AD numa subscrição azure CSP (Cloud Solution Provider) ?](#can-i-enable-azure-ad-domain-services-in-an-azure-csp-cloud-solution-provider-subscription)
* [Posso ativar os Serviços de Domínio Azure AD num diretório azure federado? Não sincronizo as hashes de senha para o Azure AD. Posso ativar os Serviços de Domínio Azure AD para este diretório?](#can-i-enable-azure-ad-domain-services-in-a-federated-azure-ad-directory-i-do-not-synchronize-password-hashes-to-azure-ad-can-i-enable-azure-ad-domain-services-for-this-directory)
* [Posso disponibilizar serviços de domínio Azure AD em várias redes virtuais dentro da minha subscrição?](#can-i-make-azure-ad-domain-services-available-in-multiple-virtual-networks-within-my-subscription)
* [Posso ativar os Serviços de Domínio Azure AD utilizando o PowerShell?](#can-i-enable-azure-ad-domain-services-using-powershell)
* [Posso ativar os Serviços de Domínio Azure AD usando um modelo de gestor de recursos?](#can-i-enable-azure-ad-domain-services-using-a-resource-manager-template)
* [Posso adicionar controladores de domínio a um domínio gerido pelo Azure AD Domain Services?](#can-i-add-domain-controllers-to-an-azure-ad-domain-services-managed-domain)
* [Os utilizadores convidados convidados para o meu diretório podem usar os Serviços de Domínio Azure AD?](#can-guest-users-invited-to-my-directory-use-azure-ad-domain-services)
* [Posso mover um domínio de domínio azure ad existente gerido para uma subscrição diferente, grupo de recursos, região ou rede virtual?](#can-i-move-an-existing-azure-ad-domain-services-managed-domain-to-a-different-subscription-resource-group-region-or-virtual-network)

### <a name="can-i-create-multiple-managed-domains-for-a-single-azure-ad-directory"></a>Posso criar vários domínios geridos para um único diretório da AD Azure?
Não. Só é possível criar um único domínio gerido servido pelos Serviços de Domínio Azure AD para um único diretório Azure AD.

### <a name="can-i-enable-azure-ad-domain-services-in-a-classic-virtual-network"></a>Posso ativar os Serviços de Domínio Azure AD numa rede virtual clássica?
As redes virtuais clássicas não são suportadas para novas implementações. Os domínios geridos existentes implantados nas redes virtuais clássicas continuam a ser suportados até que se reformem a 1 de março de 2023. Para as implementações existentes, pode [migrar os Serviços de Domínio Azure AD do modelo](migrate-from-classic-vnet.md)clássico de rede virtual para O Gestor de Recursos.

Para mais informações, consulte o [aviso oficial de depreciação](https://azure.microsoft.com/updates/we-are-retiring-azure-ad-domain-services-classic-vnet-support-on-march-1-2023/).

### <a name="can-i-enable-azure-ad-domain-services-in-an-azure-resource-manager-virtual-network"></a>Posso ativar os Serviços de Domínio Azure AD numa rede virtual do Gestor de Recursos Azure?
Sim. Os Serviços de Domínio Azure AD podem ser ativados numa rede virtual do Gestor de Recursos Azure. As redes virtuais Clássicas do Azure já não estão disponíveis quando se cria um domínio gerido.

### <a name="can-i-migrate-my-existing-managed-domain-from-a-classic-virtual-network-to-a-resource-manager-virtual-network"></a>Posso migrar o meu domínio gerido existente de uma rede virtual clássica para uma rede virtual do Gestor de Recursos?
Sim. Para mais informações, consulte [migrate Azure AD Domain Services do modelo clássico de rede virtual para Gestor](migrate-from-classic-vnet.md)de Recursos .

### <a name="can-i-enable-azure-ad-domain-services-in-an-azure-csp-cloud-solution-provider-subscription"></a>Posso ativar os Serviços de Domínio Azure AD numa subscrição azure CSP (Cloud Solution Provider) ?
Sim. Para mais informações, consulte como ativar os Serviços de [Domínio Azure AD nas subscrições do Azure CSP](csp.md).

### <a name="can-i-enable-azure-ad-domain-services-in-a-federated-azure-ad-directory-i-do-not-synchronize-password-hashes-to-azure-ad-can-i-enable-azure-ad-domain-services-for-this-directory"></a>Posso ativar os Serviços de Domínio Azure AD num diretório azure federado? Não sincronizo as hashes de senha para o Azure AD. Posso ativar os Serviços de Domínio Azure AD para este diretório?
Não. Para autenticar os utilizadores através da NTLM ou kerberos, o Azure AD Domain Services precisa de ter acesso às hashes de palavra-passe das contas dos utilizadores. Num diretório federado, as hashes de senha não são armazenadas no diretório da AD Azure. Portanto, a Azure AD Domain Services não funciona com tais diretórios da AD Azure.

### <a name="can-i-make-azure-ad-domain-services-available-in-multiple-virtual-networks-within-my-subscription"></a>Posso disponibilizar serviços de domínio Azure AD em várias redes virtuais dentro da minha subscrição?
O serviço em si não suporta diretamente este cenário. O seu domínio gerido está disponível numa única rede virtual de cada vez. No entanto, pode configurar a conectividade entre várias redes virtuais para expor os Serviços de Domínio Azure AD a outras redes virtuais. Para mais informações, consulte [como ligar redes virtuais em Azure utilizando gateways VPN](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md) ou [peering de rede virtual](../virtual-network/virtual-network-peering-overview.md).

### <a name="can-i-enable-azure-ad-domain-services-using-powershell"></a>Posso ativar os Serviços de Domínio Azure AD utilizando o PowerShell?
Sim. Para mais informações, consulte como ativar os Serviços de [Domínio Azure AD utilizando](powershell-create-instance.md)o PowerShell .

### <a name="can-i-enable-azure-ad-domain-services-using-a-resource-manager-template"></a>Posso ativar os Serviços de Domínio Azure AD usando um modelo de gestor de recursos?
Sim, você pode criar um domínio gerido por Serviços de Domínio Azure AD usando um modelo de Gestor de Recursos. Um diretor de serviço e um grupo Azure AD para administração devem ser criados usando o portal Azure ou Azure PowerShell antes de o modelo ser implantado. Para mais informações, consulte [Criar um domínio gerido por AD DS azure utilizando um modelo de Gestor](template-create-instance.md)de Recursos Azure . Quando cria um domínio gerido pelos Serviços de Domínio Azure AD no portal Azure, existe também uma opção para exportar o modelo para uso com implementações adicionais.

### <a name="can-i-add-domain-controllers-to-an-azure-ad-domain-services-managed-domain"></a>Posso adicionar controladores de domínio a um domínio gerido pelo Azure AD Domain Services?
Não. O domínio fornecido pela Azure AD Domain Services é um domínio gerido. Não é necessário fornecer, configurar ou gerir de outra forma controladores de domínio para este domínio. Estas atividades de gestão são fornecidas como um serviço pela Microsoft. Portanto, não é possível adicionar controladores de domínio adicionais (ler ou ler apenas) para o domínio gerido.

### <a name="can-guest-users-invited-to-my-directory-use-azure-ad-domain-services"></a>Os utilizadores convidados convidados para o meu diretório podem usar os Serviços de Domínio Azure AD?
Não. Os utilizadores convidados convidados para o seu diretório Azure AD utilizando o processo de convite [Azure AD B2B](../active-directory/active-directory-b2b-what-is-azure-ad-b2b.md) são sincronizados no seu domínio gerido pelo Azure AD Domain Services. No entanto, as palavras-passe para estes utilizadores não estão armazenadas no seu diretório Azure AD. Por isso, a Azure AD Domain Services não tem forma de sincronizar os hashes da NTLM e da Kerberos para estes utilizadores no seu domínio gerido. Estes utilizadores não podem inscrever ou juntar computadores ao domínio gerido.

### <a name="can-i-move-an-existing-azure-ad-domain-services-managed-domain-to-a-different-subscription-resource-group-region-or-virtual-network"></a>Posso mover um domínio de domínio azure ad existente gerido para uma subscrição diferente, grupo de recursos, região ou rede virtual?
Não. Depois de criar um domínio gerido pelo Azure AD Domain Services, não pode então mover a instância para um grupo de recursos diferente, rede virtual, subscrição, etc. Tenha o cuidado de selecionar a subscrição mais adequada, grupo de recursos, região e rede virtual quando implementar a instância Azure AD DS.

## <a name="administration-and-operations"></a>Administração e operações

* [Posso ligar-me ao controlador de domínio para o meu domínio gerido utilizando o Remote Desktop?](#can-i-connect-to-the-domain-controller-for-my-managed-domain-using-remote-desktop)
* [Ativei os Serviços de Domínio Da Azure. Que conta de utilizador uso para unir máquinas a este domínio?](#ive-enabled-azure-ad-domain-services-what-user-account-do-i-use-to-domain-join-machines-to-this-domain)
* [Tenho privilégios de administrador de domínio para o domínio gerido fornecido pelos Serviços de Domínio Azure AD?](#do-i-have-domain-administrator-privileges-for-the-managed-domain-provided-by-azure-ad-domain-services)
* [Posso modificar membros do grupo usando LDAP ou outras ferramentas administrativas aD em domínios geridos?](#can-i-modify-group-memberships-using-ldap-or-other-ad-administrative-tools-on-managed-domains)
* [Quanto tempo demora a fazer mudanças no meu diretório Azure AD para ser visível no meu domínio gerido?](#how-long-does-it-take-for-changes-i-make-to-my-azure-ad-directory-to-be-visible-in-my-managed-domain)
* [Posso estender o esquema do domínio gerido fornecido pelos Serviços de Domínio Da Azure AD?](#can-i-extend-the-schema-of-the-managed-domain-provided-by-azure-ad-domain-services)
* [Posso modificar ou adicionar registos dNS no meu domínio gerido?](#can-i-modify-or-add-dns-records-in-my-managed-domain)
* [Qual é a política de vida da palavra-passe num domínio gerido?](#what-is-the-password-lifetime-policy-on-a-managed-domain)
* [Os Serviços de Domínio Da Azure AD fornecem proteção contra bloqueio de conta AD?](#does-azure-ad-domain-services-provide-ad-account-lockout-protection)

### <a name="can-i-connect-to-the-domain-controller-for-my-managed-domain-using-remote-desktop"></a>Posso ligar-me ao controlador de domínio para o meu domínio gerido utilizando o Remote Desktop?
Não. Não tem permissões para se ligar aos controladores de domínio para o domínio gerido utilizando o Remote Desktop. Os membros do grupo de administradores da *AAD DC* podem administrar o domínio gerido usando ferramentas de administração aD, tais como o Ative Directory Administration Center (ADAC) ou a AD PowerShell. Estas ferramentas estão instaladas utilizando a funcionalidade Ferramentas de *Administração* do Servidor Remoto num servidor Windows ligado ao domínio gerido. Para mais informações, consulte Criar um VM de [gestão para configurar e administrar um domínio gerido pelos Serviços de Domínio Azure AD](tutorial-create-management-vm.md).

### <a name="ive-enabled-azure-ad-domain-services-what-user-account-do-i-use-to-domain-join-machines-to-this-domain"></a>Ativei os Serviços de Domínio Da Azure. Que conta de utilizador uso para unir máquinas a este domínio?
Os membros do grupo administrativo *AAD DC Administradores* podem juntar máquinas de adesão ao domínio. Além disso, os membros deste grupo recebem acesso remoto ao ambiente de trabalho a máquinas que foram unidas ao domínio.

### <a name="do-i-have-domain-administrator-privileges-for-the-managed-domain-provided-by-azure-ad-domain-services"></a>Tenho privilégios de administrador de domínio para o domínio gerido fornecido pelos Serviços de Domínio Azure AD?
Não. Não lhe são concedidos privilégios administrativos no domínio gerido. *Os* privilégios do Administrador de Domínio e *do Administrador da Empresa* não estão disponíveis para que possa utilizar dentro do domínio. Os membros do administrador de domínio ou dos grupos de administradores empresariais no seu Diretório Ativo no local também não são concedidos privilégios de administrador de domínio/empresa no domínio gerido.

### <a name="can-i-modify-group-memberships-using-ldap-or-other-ad-administrative-tools-on-managed-domains"></a>Posso modificar membros do grupo usando LDAP ou outras ferramentas administrativas aD em domínios geridos?
Não. Os membros do grupo não podem ser modificados em domínios servidos pelos Serviços de Domínio Da Azure AD. O mesmo se aplica aos atributos do utilizador. Pode alterar membros do grupo ou atributos de utilizador, quer em AD Azure, quer no seu domínio no local. As alterações são automaticamente sincronizadas para os Serviços de Domínio Azure AD.

### <a name="how-long-does-it-take-for-changes-i-make-to-my-azure-ad-directory-to-be-visible-in-my-managed-domain"></a>Quanto tempo demora a fazer mudanças no meu diretório Azure AD para ser visível no meu domínio gerido?
As alterações efetuadas no seu diretório Azure AD utilizando o Azure AD UI ou o PowerShell são automaticamente sincronizadas com o seu domínio gerido. Este processo de sincronização funciona em segundo plano. Não há um período de tempo definido para esta sincronização completar todas as alterações do objeto.

### <a name="can-i-extend-the-schema-of-the-managed-domain-provided-by-azure-ad-domain-services"></a>Posso estender o esquema do domínio gerido fornecido pelos Serviços de Domínio Da Azure AD?
Não. O esquema é administrado pela Microsoft para o domínio gerido. As extensões de Schema não são suportadas pelos Serviços de Domínio da AD Azure.

### <a name="can-i-modify-or-add-dns-records-in-my-managed-domain"></a>Posso modificar ou adicionar registos dNS no meu domínio gerido?
Sim. Os membros do grupo de administradores da *AAD DC* têm direito a privilégios de *administrador dNS* para modificar os registos de DNS no domínio gerido. Estes utilizadores podem utilizar a consola DNS Manager numa máquina que executa o Windows Server, que se juntou ao domínio gerido para gerir o DNS. Para utilizar a consola DNS Manager, instale *as Ferramentas de Servidor DNS,* que faz parte da funcionalidade opcional de ferramentas opcionais de *administração* do servidor remoto no servidor. Para mais informações, consulte [ADNS num domínio gerido](manage-dns.md)pelos Serviços de Domínio Azure AD .

### <a name="what-is-the-password-lifetime-policy-on-a-managed-domain"></a>Qual é a política de vida da palavra-passe num domínio gerido?
A vida útil da palavra-passe padrão num domínio gerido pelo Azure AD Domain Services é de 90 dias. Esta vida útil da palavra-passe não é sincronizada com a vida útil da palavra-passe configurada em Azure AD. Portanto, pode ter uma situação em que as palavras-passe dos utilizadores expiram no seu domínio gerido, mas ainda são válidas em Azure AD. Nestes cenários, os utilizadores precisam de alterar a sua palavra-passe em AD Azure e a nova palavra-passe sincronizará o seu domínio gerido. Além disso, os atributos de *password-não-expiração* e de mudança *de palavra-passe-no-próximo* para as contas de utilizador não são sincronizados para o seu domínio gerido.

### <a name="does-azure-ad-domain-services-provide-ad-account-lockout-protection"></a>Os Serviços de Domínio Da Azure AD fornecem proteção contra bloqueio de conta AD?
Sim. Cinco tentativas de senha inválidas dentro de 2 minutos no domínio gerido fazem com que uma conta de utilizador seja bloqueada durante 30 minutos. Após 30 minutos, a conta de utilizador é automaticamente desbloqueada. As tentativas de senha inválidas no domínio gerido não bloqueiam a conta de utilizador em Azure AD. A conta de utilizador está bloqueada apenas dentro do domínio gerido pelos Serviços de Domínio Azure AD. Para obter mais informações, consulte as políticas de bloqueio de [passwords e de conta em domínios geridos](password-policy.md).

## <a name="billing-and-availability"></a>Faturação e disponibilidade

* [A Azure AD Domain Services é um serviço pago?](#is-azure-ad-domain-services-a-paid-service)
* [Há um julgamento gratuito para o serviço?](#is-there-a-free-trial-for-the-service)
* [Posso parar um domínio gerido pela Azure AD Domain Services?](#can-i-pause-an-azure-ad-domain-services-managed-domain)
* [Posso falhar os Serviços de Domínio Azure AD para outra região para um evento DE R?](#can-i-pause-an-azure-ad-domain-services-managed-domain)
* [Posso obter serviços de domínio Azure AD como parte da Enterprise Mobility Suite (EMS)? Preciso do Azure AD Premium para utilizar os Serviços de Domínio Azure AD?](#can-i-failover-azure-ad-domain-services-to-another-region-for-a-dr-event)
* [Em que regiões azure está o serviço disponível?](#can-i-get-azure-ad-domain-services-as-part-of-enterprise-mobility-suite-ems-do-i-need-azure-ad-premium-to-use-azure-ad-domain-services)

### <a name="is-azure-ad-domain-services-a-paid-service"></a>A Azure AD Domain Services é um serviço pago?
Sim. Para obter mais informações, veja a [página de preços](https://azure.microsoft.com/pricing/details/active-directory-ds/).

### <a name="is-there-a-free-trial-for-the-service"></a>Há um julgamento gratuito para o serviço?
Os Serviços de Domínio Azure AD estão incluídos no teste gratuito para o Azure. Pode inscrever-se para um [julgamento gratuito de um mês de Azure.](https://azure.microsoft.com/pricing/free-trial/)

### <a name="can-i-pause-an-azure-ad-domain-services-managed-domain"></a>Posso parar um domínio gerido pela Azure AD Domain Services?
Não. Uma vez ativado um domínio gerido pelo Azure AD Domain Services, o serviço está disponível dentro da sua rede virtual selecionada até eliminar o domínio gerido. Não há como parar o serviço. A faturação continua de hora em hora até eliminar o domínio gerido.

### <a name="can-i-failover-azure-ad-domain-services-to-another-region-for-a-dr-event"></a>Posso falhar os Serviços de Domínio Azure AD para outra região para um evento DE R?
Não. A Azure AD Domain Services não fornece atualmente um modelo de implantação geo-redundante. Está limitado a uma única rede virtual numa região de Azure. Se quiser utilizar várias regiões azure, precisa de executar os seus Controladores de Domínio de Diretório Ativo em VMs Azure IaaS. Para orientação de arquitetura, consulte [Extend your on-premis Ative Directory domain to Azure](https://docs.microsoft.com/azure/architecture/reference-architectures/identity/adds-extend-domain).

### <a name="can-i-get-azure-ad-domain-services-as-part-of-enterprise-mobility-suite-ems-do-i-need-azure-ad-premium-to-use-azure-ad-domain-services"></a>Posso obter serviços de domínio Azure AD como parte da Enterprise Mobility Suite (EMS)? Preciso do Azure AD Premium para utilizar os Serviços de Domínio Azure AD?
Não. O Azure AD Domain Services é um serviço Azure pay-as-you-go e não faz parte da EMS. Os Serviços de Domínio Azure AD podem ser utilizados com todas as edições do Azure AD (Grátis e Premium). É cobrado de hora em hora, dependendo do uso.

### <a name="what-azure-regions-is-the-service-available-in"></a>Em que regiões azure está o serviço disponível?
Consulte os [Serviços Azure por página da região](https://azure.microsoft.com/regions/#services/) para ver uma lista das regiões azure onde estão disponíveis os Serviços de Domínio Azure AD.

## <a name="troubleshooting"></a>Resolução de problemas

Consulte o guia de resolução de [problemas](troubleshoot.md) para soluções para questões comuns com a configuração ou administração de Serviços de Domínio Azure AD.

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre os Serviços de Domínio Azure AD, veja [o que é azure Ative Directory Domain Services?](overview.md)

Para começar, consulte Criar e configurar uma instância de Serviços de [Domínio de Diretório Ativo Azure.](tutorial-create-instance.md)
