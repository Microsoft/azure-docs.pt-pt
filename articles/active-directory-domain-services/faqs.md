---
title: FAQ - serviços de domínio do Active Directory do Azure | Documentos da Microsoft
description: Perguntas mais frequentes sobre o Azure Active Directory Domain Services
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: daveba
editor: curtand
ms.assetid: 48731820-9e8c-4ec2-95e8-83dba1e58775
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/22/2019
ms.author: iainfou
ms.openlocfilehash: 10131ad306a8a24cb5835e55a02f1b502b20bea4
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/29/2019
ms.locfileid: "67473375"
---
# <a name="azure-active-directory-domain-services-frequently-asked-questions-faqs"></a>Azure Active Directory Domain Services: Perguntas Mais Frequentes (FAQs)
Esta página responde às perguntas mais frequentes sobre o Azure Active Directory Domain Services. Manter a verificação de volta para as atualizações.

## <a name="troubleshooting-guide"></a>Guia de resolução de problemas
Consulte a [guia de resolução de problemas](troubleshoot.md) para soluções para problemas comuns com a configuração ou administrar o Azure AD Domain Services.

## <a name="configuration"></a>Configuração
### <a name="can-i-create-multiple-managed-domains-for-a-single-azure-ad-directory"></a>Posso criar vários domínios geridos para um único diretório do Azure AD?
Não. Só pode criar um único domínio gerido atendido pelo Azure AD Domain Services para um único diretório do Azure AD.  

### <a name="can-i-enable-azure-ad-domain-services-in-an-azure-resource-manager-virtual-network"></a>Pode ativar os serviços de domínio do Azure AD numa rede virtual do Azure Resource Manager?
Sim. O Azure AD Domain Services pode ser ativados numa rede virtual do Azure Resource Manager. Redes virtuais do Azure clássicas já não são suportadas para a criação de novos domínios geridos.

### <a name="can-i-migrate-my-existing-managed-domain-from-a-classic-virtual-network-to-a-resource-manager-virtual-network"></a>Posso migrar meu domínio gerido existente de uma rede virtual clássica a uma rede virtual do Resource Manager?
Não é atualmente. A Microsoft fornecerá um mecanismo para migrar o domínio gerido existente de uma rede virtual clássica a uma rede virtual do Gestor de recursos no futuro.

### <a name="can-i-enable-azure-ad-domain-services-in-an-azure-csp-cloud-solution-provider-subscription"></a>Posso ativar o Azure AD Domain Services numa subscrição do Azure CSP (fornecedor de soluções Cloud)?
Sim. Veja como pode permitir [Azure AD Domain Services nas subscrições do Azure CSP](csp.md).

### <a name="can-i-enable-azure-ad-domain-services-in-a-federated-azure-ad-directory-i-do-not-synchronize-password-hashes-to-azure-ad-can-i-enable-azure-ad-domain-services-for-this-directory"></a>Posso habilitar serviços de domínio do Azure AD num Azure federado diretório AD? Eu não sincronizar os hashes de palavra-passe para o Azure AD. Pode ativar os serviços de domínio do Azure AD para este diretório?
Não. O Azure AD Domain Services precisa de acesso para os hashes de palavra-passe de contas de utilizador, para autenticar usuários via NTLM ou Kerberos. Num diretório federado, os hashes de palavra-passe não são armazenados no diretório do Azure AD. Por conseguinte, o Azure AD Domain Services não funciona com o desses diretórios do Azure AD.

### <a name="can-i-make-azure-ad-domain-services-available-in-multiple-virtual-networks-within-my-subscription"></a>Posso fazer do Azure AD Domain Services disponível em várias redes virtuais dentro de minha assinatura?
O próprio serviço não suporta diretamente neste cenário. O domínio gerido está disponível em apenas uma rede virtual ao mesmo tempo. No entanto, pode configurar a conectividade entre várias redes virtuais para expor serviços de domínio do Azure AD para outras redes virtuais. Veja como é possível [ligar redes virtuais no Azure](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md).

### <a name="can-i-enable-azure-ad-domain-services-using-powershell"></a>Pode ativar os serviços de domínio do Azure AD com o PowerShell?
Sim. Ver [como para ativar o Azure AD Domain Services com o PowerShell](powershell-create-instance.md).

### <a name="can-i-enable-azure-ad-domain-services-using-a-resource-manager-template"></a>Pode ativar os serviços de domínio do Azure AD com um modelo do Resource Manager?
Não, não é atualmente possível ativar os serviços de domínio do Azure AD através de um modelo. Em vez disso, utilize o PowerShell, consulte [como para ativar o Azure AD Domain Services com o PowerShell](powershell-create-instance.md).

### <a name="can-i-add-domain-controllers-to-an-azure-ad-domain-services-managed-domain"></a>Pode adicionar controladores de domínio a um domínio gerido do Azure AD Domain Services?
Não. O domínio fornecido pelo Azure AD Domain Services é um domínio gerido. Não é necessário aprovisionar, configurar ou gerenciar controladores de domínio para este domínio - estas atividades de gestão são fornecidas como um serviço pela Microsoft. Por conseguinte, não é possível adicionar controladores de domínio adicionais (leitura / escrita ou só de leitura) para o domínio gerido.

### <a name="can-guest-users-invited-to-my-directory-use-azure-ad-domain-services"></a>Utilizadores convidados para o meu diretório podem utilizar o Azure AD Domain Services?
Não. Utilizadores convidados para o seu diretório do Azure AD com o [do Azure AD B2B](../active-directory/active-directory-b2b-what-is-azure-ad-b2b.md) o processo de convite está sincronizada com o seu domínio gerido do Azure AD Domain Services. No entanto, as palavras-passe para estes utilizadores não são armazenadas no diretório do Azure AD. Por conseguinte, os serviços de domínio do Azure AD não tem nenhuma forma para sincronizar NTLM e Kerberos codifica para estes utilizadores no seu domínio gerido. Como resultado, esses usuários não é possível iniciar sessão no domínio gerido ou associar computadores ao domínio gerido.

## <a name="administration-and-operations"></a>Administração e de operações
### <a name="can-i-connect-to-the-domain-controller-for-my-managed-domain-using-remote-desktop"></a>Ligar ao controlador de domínio para meu domínio gerido com o ambiente de trabalho remoto?
Não. Não tem permissões para ligar aos controladores de domínio para o domínio gerido através do ambiente de trabalho remoto. Membros do grupo "Administradores do AAD DC" podem administrar domínio gerido com ferramentas de administração do AD como o Centro de administração do Active Directory (ADAC) ou o AD PowerShell. Essas ferramentas são instaladas usando a funcionalidade "Ferramentas de administração de servidor remoto" num servidor Windows associado ao domínio gerido.

### <a name="ive-enabled-azure-ad-domain-services-what-user-account-do-i-use-to-domain-join-machines-to-this-domain"></a>Habilitei o Azure AD Domain Services. Conta de utilizador que uso para máquinas de associação de domínio para este domínio?
Os membros do grupo administrativo "Administradores do AAD DC" podem máquinas de associação a um domínio. Além disso, os membros deste grupo recebem acesso de ambiente de trabalho remoto a máquinas que foram associados ao domínio.

### <a name="do-i-have-domain-administrator-privileges-for-the-managed-domain-provided-by-azure-ad-domain-services"></a>Tenho de privilégios de administrador de domínio para o domínio gerido fornecida pelos serviços de domínio do Azure AD?
Não. Não são concedidos privilégios administrativos no domínio gerido. Privilégios de administrador de domínio e o administrador de empresa não estão disponíveis para utilização no domínio. Não membros dos grupos de administrador de empresa no Active Directory no local ou administrador de domínio também são concedidos privilégios de administrador empresarial/domínio no domínio gerido.

### <a name="can-i-modify-group-memberships-using-ldap-or-other-ad-administrative-tools-on-managed-domains"></a>Pode modificar as associações de grupo utilizando LDAP ou outras ferramentas administrativas do AD em domínios geridos?
Não. Não não possível modificar as associações de grupo em domínios atendidos pelo Azure AD Domain Services. O mesmo se aplica a atributos do utilizador. No entanto pode alterar as associações de grupo ou atributos de utilizador no Azure AD ou no seu domínio no local. Essas alterações são sincronizadas automaticamente com os serviços de domínio do Azure AD.

### <a name="how-long-does-it-take-for-changes-i-make-to-my-azure-ad-directory-to-be-visible-in-my-managed-domain"></a>Quanto tempo demora para que as alterações, certifique-se ao meu diretório do Azure AD para estar visível no meu domínio gerido?
As alterações feitas no diretório do Azure AD através da IU do Azure AD ou do PowerShell são sincronizadas com o seu domínio gerido. Este processo de sincronização é executado em segundo plano. Depois de concluída a sincronização inicial, normalmente, demora cerca de 20 minutos para que as alterações feitas no Azure AD sejam refletidas no seu domínio gerido.

### <a name="can-i-extend-the-schema-of-the-managed-domain-provided-by-azure-ad-domain-services"></a>Pode expandir o esquema do domínio gerido fornecido pelos serviços de domínio do Azure AD?
Não. O esquema é administrado pela Microsoft para o domínio gerido. Extensões de esquema não são suportadas pelo Azure AD Domain Services.

### <a name="can-i-modify-or-add-dns-records-in-my-managed-domain"></a>Pode modificar ou adicionar registos DNS no meu domínio gerido?
Sim. Membros do grupo "Administradores do AAD DC" são concedidos privilégios de administrador de DNS, para modificar registos DNS no domínio gerido. Eles podem utilizar a consola de Gestor de DNS numa máquina com o Windows Server associado ao domínio gerido, para gerir o DNS. Para utilizar a consola do Gestor de DNS, instale "Ferramentas do servidor DNS", que faz parte da funcionalidade "Ferramentas de administração de servidor remoto" opcional no servidor. Obter mais informações sobre [utilitários para administrar, monitorização e resolução de problemas de DNS](https://technet.microsoft.com/library/cc753579.aspx) estão disponíveis no TechNet.

### <a name="what-is-the-password-lifetime-policy-on-a-managed-domain"></a>O que é a política de ciclo de vida de palavra-passe num domínio gerido?
O tempo de vida de palavra-passe padrão num domínio do Azure AD domínio gerido dos serviços é de 90 dias. Esta duração de palavra-passe não está sincronizada com o tempo de vida de palavra-passe configurado no Azure AD. Portanto, pode ter uma situação em que as de palavras-passe dos utilizadores expiram no seu domínio gerido, mas ainda são válidas no Azure AD. Em tais cenários, os usuários precisam alterar a palavra-passe no Azure AD e a nova palavra-passe serão sincronizados com o seu domínio gerido. Além disso, a 'palavra-passe-faz-não-expirar' e "user-must-change-password-at-next-logon" atributos para contas de utilizador não estão sincronizados com o seu domínio gerido.

### <a name="does-azure-ad-domain-services-provide-ad-account-lockout-protection"></a>O Azure AD Domain Services fornece a proteção de bloqueio de conta do AD?
Sim. Cinco tentativas de palavra-passe inválida em dois minutos no domínio gerido com que uma conta de utilizador ser bloqueadas durante 30 minutos. Após 30 minutos, a conta de utilizador é desbloqueada automaticamente. Tentativas de palavra-passe inválida no domínio gerido não bloquear a conta de utilizador no Azure AD. A conta de utilizador está bloqueada apenas dentro de seu domínio gerido do Azure AD Domain Services.

## <a name="billing-and-availability"></a>Faturação e de disponibilidade
### <a name="is-azure-ad-domain-services-a-paid-service"></a>É que um serviço pago de serviços de domínio do Azure AD?
Sim. Para obter mais informações, veja a [página de preços](https://azure.microsoft.com/pricing/details/active-directory-ds/).

### <a name="is-there-a-free-trial-for-the-service"></a>Existe uma avaliação gratuita para o serviço?
Este serviço está incluído na versão de avaliação gratuita do Azure. Pode inscrever-se para obter um [durante um mês avaliação do Azure gratuita](https://azure.microsoft.com/pricing/free-trial/).

### <a name="can-i-pause-an-azure-ad-domain-services-managed-domain"></a>Pode interromper um domínio gerido do Azure AD Domain Services? 
Não. Assim que tiver ativado um domínio gerido do Azure AD Domain Services, o serviço está disponível na sua rede virtual selecionada até que desativar/eliminar o domínio gerido. Não é possível colocar em pausa o serviço. Continua a faturação à hora, até que elimine o domínio gerido.

### <a name="can-i-failover-azure-ad-domain-services-to-another-region-for-a-dr-event"></a>Posso ativação pós-falha do Azure AD Domain Services para outra região para um evento de DR?
Não.  Atualmente, o Azure AD Domain Services não fornece um modelo de implementação georredundante. Está limitado a uma única rede virtual numa região do Azure. Se pretender utilizar várias regiões do Azure, terá de executar os seus controladores de domínio do Active Directory em VMs de IaaS do Azure.  Podem encontrar orientações de arquitetura [aqui](https://docs.microsoft.com/azure/architecture/reference-architectures/identity/adds-extend-domain).

### <a name="can-i-get-azure-ad-domain-services-as-part-of-enterprise-mobility-suite-ems-do-i-need-azure-ad-premium-to-use-azure-ad-domain-services"></a>Posso obter o Azure AD Domain Services como parte do Enterprise Mobility Suite (EMS)? É necessário utilizar o Azure AD Domain Services do Azure AD Premium?
Não. O Azure AD Domain Services é uma serviço do Azure pay as you go e não faz parte do EMS. O Azure AD Domain Services podem ser utilizados com todas as edições do Azure AD (gratuito, básico e, Premium). É cobrado numa base horária, consoante a utilização.

### <a name="what-azure-regions-is-the-service-available-in"></a>Em que regiões do Azure está disponível no serviço?
Consulte a [serviços do Azure por região](https://azure.microsoft.com/regions/#services/) página para ver uma lista das regiões do Azure onde o Azure AD Domain Services está disponível.
