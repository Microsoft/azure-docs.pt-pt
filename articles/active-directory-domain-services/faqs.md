---
title: Perguntas frequentes sobre serviços de domínio Azure AD Microsoft Docs
description: Leia e compreenda algumas das perguntas frequentes em torno da configuração, administração e disponibilidade para serviços de domínio do Diretório Ativo Azure
services: active-directory-ds
author: MicrosoftGuyJFlo
manager: daveba
ms.assetid: 48731820-9e8c-4ec2-95e8-83dba1e58775
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 09/30/2020
ms.author: joflore
ms.openlocfilehash: 07d2d71c13cd054dec46b6eaa9f07079c8cec63b
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/13/2020
ms.locfileid: "91962297"
---
# <a name="frequently-asked-questions-faqs-about-azure-active-directory-ad-domain-services"></a>Perguntas frequentes (FAQs) sobre Azure Ative Directory (AD) Domain Services

Esta página responde a perguntas frequentes sobre os Serviços de Domínio do Diretório Ativo Azure.

## <a name="configuration"></a>Configuração

* [Posso criar vários domínios geridos para um único diretório AD Azure?](#can-i-create-multiple-managed-domains-for-a-single-azure-ad-directory)
* [Posso ativar os Serviços de Domínio AD Azure numa rede virtual clássica?](#can-i-enable-azure-ad-domain-services-in-a-classic-virtual-network)
* [Posso ativar os Serviços de Domínio AZure AD numa rede virtual Azure Resource Manager?](#can-i-enable-azure-ad-domain-services-in-an-azure-resource-manager-virtual-network)
* [Posso migrar o meu domínio gerido de uma rede virtual clássica para uma rede virtual do Gestor de Recursos?](#can-i-migrate-my-existing-managed-domain-from-a-classic-virtual-network-to-a-resource-manager-virtual-network)
* [Posso ativar os Serviços de Domínio AZure AD numa subscrição Azure CSP (Cloud Solution Provider)?](#can-i-enable-azure-ad-domain-services-in-an-azure-csp-cloud-solution-provider-subscription)
* [Posso permitir que os Serviços de Domínio AD da Azure num diretório federado da AD Azure? Não sincronizo as hashes de palavra-passe para a Azure AD. Posso ativar os Serviços de Domínio AZure AD para este diretório?](#can-i-enable-azure-ad-domain-services-in-a-federated-azure-ad-directory-i-do-not-synchronize-password-hashes-to-azure-ad-can-i-enable-azure-ad-domain-services-for-this-directory)
* [Posso disponibilizar serviços de domínio Azure AD em várias redes virtuais dentro da minha subscrição?](#can-i-make-azure-ad-domain-services-available-in-multiple-virtual-networks-within-my-subscription)
* [Posso ativar os serviços de domínio Azure AD utilizando o PowerShell?](#can-i-enable-azure-ad-domain-services-using-powershell)
* [Posso ativar os serviços de domínio Azure AD usando um modelo de gestor de recursos?](#can-i-enable-azure-ad-domain-services-using-a-resource-manager-template)
* [Posso adicionar controladores de domínio a um domínio gerido por serviços de domínio Azure AD?](#can-i-add-domain-controllers-to-an-azure-ad-domain-services-managed-domain)
* [Os utilizadores convidados podem ser convidados para o meu diretório usar serviços de domínio Azure AD?](#can-guest-users-be-invited-to-my-directory-use-azure-ad-domain-services)
* [Posso mover um domínio gerido a azure AD domain para uma subscrição diferente, grupo de recursos, região ou rede virtual?](#can-i-move-an-existing-azure-ad-domain-services-managed-domain-to-a-different-subscription-resource-group-region-or-virtual-network)
* [Os Serviços de Domínio AZure AD incluem opções de alta disponibilidade?](#does-azure-ad-domain-services-include-high-availability-options)

### <a name="can-i-create-multiple-managed-domains-for-a-single-azure-ad-directory"></a>Posso criar vários domínios geridos para um único diretório AD Azure?
Não. Só é possível criar um único domínio gerido, servido pela Azure AD Domain Services para um único diretório AD Azure.

### <a name="can-i-enable-azure-ad-domain-services-in-a-classic-virtual-network"></a>Posso ativar os Serviços de Domínio AD Azure numa rede virtual clássica?
As redes virtuais clássicas não são suportadas para novas implementações. Os domínios geridos existentes implantados nas redes virtuais clássicas continuam a ser suportados até que sejam retirados a 1 de março de 2023. Para implementações existentes, pode [migrar os Serviços de Domínio Azure AD do modelo de rede virtual Clássico para o Gestor de Recursos.](migrate-from-classic-vnet.md)

Para mais informações, consulte o [aviso oficial de depreciação.](https://azure.microsoft.com/updates/we-are-retiring-azure-ad-domain-services-classic-vnet-support-on-march-1-2023/)

### <a name="can-i-enable-azure-ad-domain-services-in-an-azure-resource-manager-virtual-network"></a>Posso ativar os Serviços de Domínio AZure AD numa rede virtual Azure Resource Manager?
Yes. Os Serviços de Domínio Azure AD podem ser ativados numa rede virtual Azure Resource Manager. As redes virtuais Classic Azure já não estão disponíveis quando crias um domínio gerido.

### <a name="can-i-migrate-my-existing-managed-domain-from-a-classic-virtual-network-to-a-resource-manager-virtual-network"></a>Posso migrar o meu domínio gerido de uma rede virtual clássica para uma rede virtual do Gestor de Recursos?
Yes. Para obter mais informações, consulte [os Serviços de Domínio Azure Migrate Azure do modelo de rede virtual Clássico ao Gestor de Recursos.](migrate-from-classic-vnet.md)

### <a name="can-i-enable-azure-ad-domain-services-in-an-azure-csp-cloud-solution-provider-subscription"></a>Posso ativar os Serviços de Domínio AZure AD numa subscrição Azure CSP (Cloud Solution Provider)?
Yes. Para obter mais informações, consulte [como ativar os Serviços de Domínio Azure AD nas assinaturas Azure CSP](csp.md).

### <a name="can-i-enable-azure-ad-domain-services-in-a-federated-azure-ad-directory-i-do-not-synchronize-password-hashes-to-azure-ad-can-i-enable-azure-ad-domain-services-for-this-directory"></a>Posso permitir que os Serviços de Domínio AD da Azure num diretório federado da AD Azure? Não sincronizo as hashes de palavra-passe para a Azure AD. Posso ativar os Serviços de Domínio AZure AD para este diretório?
Não. Para autenticar os utilizadores através da NTLM ou da Kerberos, o Azure AD Domain Services necessita de acesso às hashes de palavra-passe das contas dos utilizadores. Num diretório federado, as hashes de senha não são armazenadas no diretório AD Azure. Portanto, os Serviços de Domínio AD Azure não funcionam com tais diretórios AD Azure.

No entanto, se estiver a utilizar o Azure AD Connect para sincronização de hash de palavra-passe, pode utilizar os Serviços de Domínio AD Azure porque os valores do hash da palavra-passe são armazenados em Azure AD.

### <a name="can-i-make-azure-ad-domain-services-available-in-multiple-virtual-networks-within-my-subscription"></a>Posso disponibilizar serviços de domínio Azure AD em várias redes virtuais dentro da minha subscrição?
O serviço em si não suporta diretamente este cenário. O seu domínio gerido está disponível apenas numa rede virtual de cada vez. No entanto, é possível configurar a conectividade entre várias redes virtuais para expor os Serviços de Domínio AZure AD a outras redes virtuais. Para obter mais informações, consulte [como ligar redes virtuais em Azure utilizando gateways VPN](../vpn-gateway/vpn-gateway-howto-vnet-vnet-portal-classic.md) ou [espreitamento de rede virtual.](../virtual-network/virtual-network-peering-overview.md)

### <a name="can-i-enable-azure-ad-domain-services-using-powershell"></a>Posso ativar os serviços de domínio Azure AD utilizando o PowerShell?
Yes. Para obter mais informações, consulte [como ativar os Serviços de Domínio Azure AD utilizando o PowerShell](powershell-create-instance.md).

### <a name="can-i-enable-azure-ad-domain-services-using-a-resource-manager-template"></a>Posso ativar os serviços de domínio Azure AD usando um modelo de gestor de recursos?
Sim, pode criar um domínio gerido por Serviços de Domínio AZure AD utilizando um modelo de Gestor de Recursos. Um chefe de serviço e um grupo AD AD para administração devem ser criados usando o portal Azure ou Azure PowerShell antes de o modelo ser implementado. Para obter mais informações, consulte [Criar um domínio gerido AZure AD DS utilizando um modelo de Gestor de Recursos Azure](template-create-instance.md). Quando cria um domínio gerido por Serviços de Domínio AZure AD no portal Azure, existe também uma opção para exportar o modelo para utilização com implementações adicionais.

### <a name="can-i-add-domain-controllers-to-an-azure-ad-domain-services-managed-domain"></a>Posso adicionar controladores de domínio a um domínio gerido por serviços de domínio Azure AD?
Não. O domínio fornecido pela Azure AD Domain Services é um domínio gerido. Não precisa de providenciar, configurar ou gerir controladores de domínio para este domínio. Estas atividades de gestão são fornecidas como um serviço pela Microsoft. Portanto, não é possível adicionar controladores de domínio adicionais (ler-escrever ou ler apenas) para o domínio gerido.

### <a name="can-guest-users-be-invited-to-my-directory-use-azure-ad-domain-services"></a>Os utilizadores convidados podem ser convidados para o meu diretório usar serviços de domínio Azure AD?
Não. Os utilizadores convidados convidados para o seu diretório AZure AD utilizando o processo de convite [Azure AD B2B](../active-directory/external-identities/what-is-b2b.md) são sincronizados no seu domínio gerido pelos Serviços de Domínio Azure AD. No entanto, as palavras-passe para estes utilizadores não são armazenadas no seu diretório AZure AD. Por isso, a Azure AD Domain Services não tem forma de sincronizar as hashes NTLM e Kerberos para estes utilizadores no seu domínio gerido. Estes utilizadores não podem iniciar sussutil ou juntar computadores ao domínio gerido.

### <a name="can-i-move-an-existing-azure-ad-domain-services-managed-domain-to-a-different-subscription-resource-group-region-or-virtual-network"></a>Posso mover um domínio gerido a azure AD domain para uma subscrição diferente, grupo de recursos, região ou rede virtual?
Não. Depois de criar um domínio gerido por Azure AD Domain Services, não é possível mover o domínio gerido para um grupo de recursos diferente, rede virtual, subscrição, etc. Tenha o cuidado de selecionar a subscrição mais adequada, grupo de recursos, região e rede virtual quando implementar o domínio gerido.

### <a name="does-azure-ad-domain-services-include-high-availability-options"></a>Os Serviços de Domínio AZure AD incluem opções de alta disponibilidade?

Yes. Cada domínio gerido por Azure AD Domain Services inclui dois controladores de domínio. Não se gere ou se liga a estes controladores de domínio, eles fazem parte do serviço gerido. Se colocar os Serviços de Domínio Azure AD numa região que suporta Zonas de Disponibilidade, os controladores de domínio são distribuídos por zonas. Nas regiões que não suportam Zonas de Disponibilidade, os controladores de domínio são distribuídos por Conjuntos de Disponibilidade. Não tem opções de configuração ou controlo de gestão sobre esta distribuição. Para obter mais informações, consulte [as opções de Disponibilidade para máquinas virtuais em Azure.](../virtual-machines/availability.md)

## <a name="administration-and-operations"></a>Administração e operações

* [Posso ligar-me ao controlador de domínio para o meu domínio gerido utilizando o Ambiente de Trabalho Remoto?](#can-i-connect-to-the-domain-controller-for-my-managed-domain-using-remote-desktop)
* [I've enabled Azure AD Domain Services. Que conta de utilizador uso para dominar máquinas neste domínio?](#ive-enabled-azure-ad-domain-services-what-user-account-do-i-use-to-domain-join-machines-to-this-domain)
* [Tenho privilégios de administrador de domínio para o domínio gerido fornecido pela Azure AD Domain Services?](#do-i-have-domain-administrator-privileges-for-the-managed-domain-provided-by-azure-ad-domain-services)
* [Posso modificar os membros do grupo utilizando LDAP ou outras ferramentas administrativas AD em domínios geridos?](#can-i-modify-group-memberships-using-ldap-or-other-ad-administrative-tools-on-managed-domains)
* [Quanto tempo demora as alterações que faço ao meu diretório AD Azure para ser visível no meu domínio gerido?](#how-long-does-it-take-for-changes-i-make-to-my-azure-ad-directory-to-be-visible-in-my-managed-domain)
* [Posso estender o esquema do domínio gerido fornecido pela Azure AD Domain Services?](#can-i-extend-the-schema-of-the-managed-domain-provided-by-azure-ad-domain-services)
* [Posso modificar ou adicionar registos DNS no meu domínio gerido?](#can-i-modify-or-add-dns-records-in-my-managed-domain)
* [Qual é a política de vida útil de senha num domínio gerido?](#what-is-the-password-lifetime-policy-on-a-managed-domain)
* [Os Serviços de Domínio Azure AD fornecem proteção contra bloqueio de conta AD?](#does-azure-ad-domain-services-provide-ad-account-lockout-protection)
* [Posso configurar o Sistema de Ficheiros Distribuídos (DFS) e a replicação nos Serviços de Domínio AD Azure?](#can-i-configure-distributed-file-system-and-replication-within-azure-ad-domain-services)
* [Como é que as Atualizações do Windows são aplicadas nos Serviços de Domínio Ad Azure?](#how-are-windows-updates-applied-in-azure-ad-domain-services)

### <a name="can-i-connect-to-the-domain-controller-for-my-managed-domain-using-remote-desktop"></a>Posso ligar-me ao controlador de domínio para o meu domínio gerido utilizando o Ambiente de Trabalho Remoto?
Não. Não tem permissões para ligar aos controladores de domínio para o domínio gerido utilizando o Ambiente de Trabalho Remoto. Os membros do grupo *de administradores AAD DC* podem administrar o domínio gerido usando ferramentas de administração de AD, tais como o Ative Directory Administration Center (ADAC) ou o AD PowerShell. Estas ferramentas são instaladas utilizando a funcionalidade *Ferramentas de Administração do Servidor Remoto* num servidor windows ligado ao domínio gerido. Para obter mais informações, consulte [Criar um VM de gestão para configurar e administrar um domínio gerido por Serviços de Domínio AD Azure](tutorial-create-management-vm.md).

### <a name="ive-enabled-azure-ad-domain-services-what-user-account-do-i-use-to-domain-join-machines-to-this-domain"></a>I've enabled Azure AD Domain Services. Que conta de utilizador uso para dominar máquinas neste domínio?
Qualquer conta de utilizador que faça parte do domínio gerido pode juntar-se a um VM. Os membros do grupo *de administradores AAD DC* têm acesso remoto ao ambiente de trabalho a máquinas que foram unidas ao domínio gerido.

### <a name="do-i-have-domain-administrator-privileges-for-the-managed-domain-provided-by-azure-ad-domain-services"></a>Tenho privilégios de administrador de domínio para o domínio gerido fornecido pela Azure AD Domain Services?
Não. Não lhe são concedidos privilégios administrativos no domínio gerido. *Os* privilégios de Administrador de Domínio e *Administrador Empresarial* não estão disponíveis para que possa utilizar dentro do domínio. Os membros do administrador de domínio ou grupos de administradores empresariais no seu Diretório Ativo também não recebem privilégios de domínio/administrador de empresa no domínio gerido.

### <a name="can-i-modify-group-memberships-using-ldap-or-other-ad-administrative-tools-on-managed-domains"></a>Posso modificar os membros do grupo utilizando LDAP ou outras ferramentas administrativas AD em domínios geridos?
Os utilizadores e grupos sincronizados do Azure Ative Directory para os Serviços de Domínio Azure AD não podem ser modificados porque a sua fonte de origem é o Azure Ative Directory. Qualquer utilizador ou grupo originário do domínio gerido pode ser modificado.

### <a name="how-long-does-it-take-for-changes-i-make-to-my-azure-ad-directory-to-be-visible-in-my-managed-domain"></a>Quanto tempo demora as alterações que faço ao meu diretório AD Azure para ser visível no meu domínio gerido?
As alterações efetuadas no seu diretório AD Azure utilizando o Azure AD UI ou o PowerShell são automaticamente sincronizadas ao seu domínio gerido. Este processo de sincronização é executado em segundo plano. Não há um período de tempo definido para esta sincronização para completar todas as alterações de objetos.

### <a name="can-i-extend-the-schema-of-the-managed-domain-provided-by-azure-ad-domain-services"></a>Posso estender o esquema do domínio gerido fornecido pela Azure AD Domain Services?
Não. O esquema é administrado pela Microsoft para o domínio gerido. As extensões de Schema não são suportadas pelos Serviços de Domínio AD da Azure.

### <a name="can-i-modify-or-add-dns-records-in-my-managed-domain"></a>Posso modificar ou adicionar registos DNS no meu domínio gerido?
Yes. Os membros do grupo *de administradores da AAD DC* recebem privilégios de administrador de *DNS* para modificar os registos DNS no domínio gerido. Estes utilizadores podem utilizar a consola DNS Manager numa máquina que executa o Windows Server, juntando-se ao domínio gerido para gerir o DNS. Para utilizar a consola DNS Manager, instale *as Ferramentas do Servidor DNS*, que fazem parte da funcionalidade opcional de Administração de *Servidores Remotos* no servidor. Para obter mais informações, consulte [Administrar DNS num domínio gerido pelos Serviços de Domínio AZure AD](manage-dns.md).

### <a name="what-is-the-password-lifetime-policy-on-a-managed-domain"></a>Qual é a política de vida útil de senha num domínio gerido?
A vida útil da palavra-passe padrão num domínio gerido pelos Serviços de Domínio AD Azure é de 90 dias. Esta vida útil da palavra-passe não é sincronizada com a vida útil da palavra-passe configurada em Azure AD. Portanto, pode ter uma situação em que as palavras-passe dos utilizadores expiram no seu domínio gerido, mas ainda são válidas em Azure AD. Nestes cenários, os utilizadores precisam de alterar a sua palavra-passe em AZure AD e a nova palavra-passe sincronizará para o seu domínio gerido. Se pretender alterar a vida útil da palavra-passe padrão num domínio gerido, pode [criar e configurar políticas de palavras-passe personalizadas.](password-policy.md)

Além disso, a política de palavra-passe AD AZure para *DisablePasswordExpiration* é sincronizada para um domínio gerido. Quando *o DisablePasswordExpiration* é aplicado a um utilizador em Azure AD, o valor *UserAccountControl* para o utilizador sincronizado no domínio gerido *DONT_EXPIRE_PASSWORD* aplicado.

Quando os utilizadores reiniciam a sua palavra-passe em Ad Azure, é aplicado o atributo *forceChangePasswordNextSignIn=True.* Um domínio gerido sincroniza este atributo a partir do Azure AD. Quando o domínio gerido deteta *forceChangePasswordNextSignIn* é definido para um utilizador sincronizado a partir de Azure AD, o atributo *pwdLastSet* no domínio gerido é definido para *0*, o que invalida a palavra-passe atualmente definida.

### <a name="does-azure-ad-domain-services-provide-ad-account-lockout-protection"></a>Os Serviços de Domínio Azure AD fornecem proteção contra bloqueio de conta AD?
Yes. Cinco tentativas de senha inválidas no prazo de 2 minutos no domínio gerido fazem com que uma conta de utilizador fique bloqueada durante 30 minutos. Após 30 minutos, a conta do utilizador é desbloqueada automaticamente. As tentativas de palavra-passe inválidas no domínio gerido não bloqueiam a conta de utilizador no Azure AD. A conta de utilizador está bloqueada apenas dentro do domínio gerido pelos Serviços de Domínio Azure AD. Para obter mais informações, consulte [as políticas de bloqueio de passwords e conta em domínios geridos](password-policy.md).

### <a name="can-i-configure-distributed-file-system-and-replication-within-azure-ad-domain-services"></a>Posso configurar o Sistema de Ficheiros Distribuídos e a replicação nos Serviços de Domínio AD Azure?
Não. Sistema de Ficheiros Distribuídos (DFS) e replicação não estão disponíveis quando se utilizam os Serviços de Domínio AD Azure.

### <a name="how-are-windows-updates-applied-in-azure-ad-domain-services"></a>Como é que as Atualizações do Windows são aplicadas nos Serviços de Domínio Ad Azure?
Os controladores de domínio num domínio gerido aplicam automaticamente as atualizações necessárias do Windows. Não há nada para configurar ou administrar aqui. Certifique-se de que não cria regras de grupo de segurança de rede que bloqueiam o tráfego de saída para o Windows Updates. Para os seus próprios VMs unidos ao domínio gerido, você é responsável por configurar e aplicar quaisquer atualizações de sistema operativo e aplicação necessárias.

## <a name="billing-and-availability"></a>Faturação e disponibilidade

* [A Azure AD Domain Services é um serviço pago?](#is-azure-ad-domain-services-a-paid-service)
* [Há um julgamento gratuito para o serviço?](#is-there-a-free-trial-for-the-service)
* [Posso fazer uma pausa num domínio gerido por Azure AD Domain Services?](#can-i-pause-an-azure-ad-domain-services-managed-domain)
* [Posso falhar os Serviços de Domínio AZure AD para outra região para um evento DR?](#can-i-pause-an-azure-ad-domain-services-managed-domain)
* [Posso obter serviços de domínio Azure AD como parte da Enterprise Mobility Suite (EMS)? Preciso que o Azure AD Premium utilize os Serviços de Domínio AD da Azure?](#can-i-fail-over-azure-ad-domain-services-to-another-region-for-a-dr-event)
* [Em que regiões de Azure está o serviço disponível?](#can-i-get-azure-ad-domain-services-as-part-of-enterprise-mobility-suite-ems-do-i-need-azure-ad-premium-to-use-azure-ad-domain-services)

### <a name="is-azure-ad-domain-services-a-paid-service"></a>A Azure AD Domain Services é um serviço pago?
Yes. Para obter mais informações, veja a [página de preços](https://azure.microsoft.com/pricing/details/active-directory-ds/).

### <a name="is-there-a-free-trial-for-the-service"></a>Há um julgamento gratuito para o serviço?
Os Serviços de Domínio Azure AD estão incluídos no teste gratuito para a Azure. Pode inscrever-se para um [julgamento gratuito de um mês de Azure.](https://azure.microsoft.com/pricing/free-trial/)

### <a name="can-i-pause-an-azure-ad-domain-services-managed-domain"></a>Posso fazer uma pausa num domínio gerido por Azure AD Domain Services?
Não. Depois de ter ativado um domínio gerido pelos Serviços de Domínio AD Azure, o serviço está disponível na rede virtual selecionada até eliminar o domínio gerido. Não há como parar o serviço. A faturação continua de hora a hora até eliminar o domínio gerido.

### <a name="can-i-fail-over-azure-ad-domain-services-to-another-region-for-a-dr-event"></a>Posso falhar os Serviços de Domínio AZure AD para outra região para um evento DR?
Não. A Azure AD Domain Services não fornece atualmente um modelo de implementação geo-redundante. Está limitado a uma única rede virtual numa região de Azure. Se pretender utilizar várias regiões do Azure, tem de executar os seus Controladores de Domínio de Diretório Ativo em VMs Azure IaaS. Para obter orientação de arquitetura, consulte [Estenda o seu domínio ative directy para Azure.](/azure/architecture/reference-architectures/identity/adds-extend-domain)

### <a name="can-i-get-azure-ad-domain-services-as-part-of-enterprise-mobility-suite-ems-do-i-need-azure-ad-premium-to-use-azure-ad-domain-services"></a>Posso obter serviços de domínio Azure AD como parte da Enterprise Mobility Suite (EMS)? Preciso que o Azure AD Premium utilize os Serviços de Domínio AD da Azure?
Não. Azure AD Domain Services é um serviço Azure pay-as-you-go e não faz parte da EMS. Os Serviços de Domínio AZure AD podem ser utilizados com todas as edições do Azure AD (Grátis e Premium). É cobrado de hora a hora, dependendo do uso.

### <a name="what-azure-regions-is-the-service-available-in"></a>Em que regiões de Azure está o serviço disponível?
Consulte os [Serviços Azure por página](https://azure.microsoft.com/regions/#services/) para ver uma lista das regiões de Azure onde os Serviços de Domínio Azure AD estão disponíveis.

## <a name="troubleshooting"></a>Resolução de problemas

Consulte o [guia de resolução de problemas](troubleshoot.md) para encontrar soluções para questões comuns com a configuração ou administração de Serviços de Domínio Azure AD.

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre os Serviços de Domínio AD Azure, veja [o que é o Azure Ative Directory Domain Services?](overview.md)

Para começar, consulte Criar e configurar um domínio gerido por [Serviços de Domínio do Diretório Ativo Azure](tutorial-create-instance.md).
