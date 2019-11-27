---
title: Visão geral de Azure Active Directory Domain Services | Microsoft Docs
description: Nesta visão geral, saiba o que Azure Active Directory Domain Services fornece e como usá-lo em sua organização para fornecer serviços de identidade para aplicativos e serviços na nuvem.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: overview
ms.date: 10/30/2019
ms.author: iainfou
ms.openlocfilehash: e5e6a2fe856915a3625f22bffa91403e3c036a22
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2019
ms.locfileid: "74481361"
---
# <a name="what-is-azure-active-directory-domain-services"></a>O que é Azure Active Directory Domain Services?

Azure Active Directory Domain Services (Azure AD DS) fornece serviços de domínio gerenciados, como ingresso no domínio, diretiva de grupo, LDAP (Lightweight Directory Access Protocol) e autenticação Kerberos/NTLM que é totalmente compatível com o Windows Server active Active. Você usa esses serviços de domínio sem a necessidade de implantar, gerenciar e corrigir controladores de domínio na nuvem. O Azure AD DS integra-se ao seu locatário existente do Azure AD, o que possibilita que os usuários entrem usando suas credenciais existentes. Você também pode usar contas de usuário e grupos existentes para proteger o acesso a recursos, que fornece um aumento mais suave de recursos locais para o Azure.

O Azure AD DS Replica informações de identidade do Azure AD e, portanto, funciona com locatários do Azure AD que são somente em nuvem ou sincronizados com um ambiente de Active Directory Domain Services (AD DS) local. O mesmo conjunto de recursos do Azure AD DS existe para ambos os ambientes.

* Se você tiver um ambiente de AD DS local existente, poderá sincronizar as informações da conta de usuário para fornecer uma identidade consistente para os usuários.
* Para ambientes somente em nuvem, você não precisa de um ambiente de AD DS local tradicional para usar os serviços de identidade centralizados do Azure AD DS.

O vídeo a seguir fornece uma visão geral de como o Azure AD DS se integra aos seus aplicativos e cargas de trabalho para fornecer serviços de identidade na nuvem:

<br />

>[!VIDEO https://www.youtube.com/embed/T1Nd9APNceQ]

## <a name="common-ways-to-provide-identity-solutions-in-the-cloud"></a>Maneiras comuns de fornecer soluções de identidade na nuvem

Quando você migra as cargas de trabalho existentes para a nuvem, os aplicativos com reconhecimento de diretório podem usar o LDAP para acesso de leitura ou gravação a um diretório AD DS local. Os aplicativos executados no Windows Server normalmente são implantados em VMs (máquinas virtuais) Unidas ao domínio para que possam ser gerenciados com segurança usando Política de Grupo. Para autenticar usuários finais, os aplicativos também podem depender da autenticação integrada do Windows, como a autenticação Kerberos ou NTLM.

Os administradores de ti geralmente usam uma das seguintes soluções para fornecer um serviço de identidade para aplicativos executados no Azure:

* Configure uma conexão VPN site a site entre cargas de trabalho que são executadas no Azure e no ambiente de AD DS local.
* Crie controladores de domínio de réplica usando VMs (máquinas virtuais) do Azure para estender o AD DS domínio/floresta.
* Implantar um ambiente de AD DS autônomo no Azure usando controladores de domínio que são executados em VMs do Azure.

Com essas abordagens, as conexões VPN com o diretório local tornam os aplicativos vulneráveis a falhas de rede transitórias ou interrupções. Se você implantar controladores de domínio usando VMs no Azure, as VMs da equipe de ti deverão gerenciar, proteger, aplicar patch, monitorar, fazer backup e solucionar problemas deles.

O Azure AD DS oferece alternativas à necessidade de criar conexões VPN de volta para um ambiente de AD DS local ou executar e gerenciar VMs no Azure para fornecer serviços de identidade. Como um serviço gerenciado, o Azure AD DS reduz a complexidade de criar uma solução de identidade integrada para ambientes híbridos e somente de nuvem.

## <a name="azure-ad-ds-features-and-benefits"></a>Recursos e benefícios do Azure AD DS

Para fornecer serviços de identidade para aplicativos e VMs na nuvem, o Azure AD DS é totalmente compatível com um ambiente de AD DS tradicional para operações como ingresso no domínio, LDAPS seguros (LDAPs), gerenciamento de Política de Grupo e DNS e suporte a associações LDAP e leitura. O suporte à gravação LDAP está disponível para objetos criados no domínio gerenciado AD DS do Azure, mas não recursos sincronizados do Azure AD. Os seguintes recursos do Azure AD DS simplificam as operações de implantação e gerenciamento:

* **Experiência de implantação simplificada:** O AD DS do Azure está habilitado para seu locatário do Azure AD usando um único assistente no portal do Azure.
* **Integrado ao Azure AD:** As contas de usuário, as associações de grupo e as credenciais ficam automaticamente disponíveis no seu locatário do Azure AD. Novos usuários, grupos ou alterações de atributos do seu locatário do Azure AD ou seu ambiente de AD DS local são sincronizados automaticamente com o AD DS do Azure.
    * Contas em diretórios externos vinculados ao Azure AD não estão disponíveis no Azure AD DS. As credenciais não estão disponíveis para esses diretórios externos, portanto, não podem ser sincronizadas em um domínio gerenciado do Azure AD DS.
* **Use suas credenciais/senhas corporativas:** As senhas para usuários em seu locatário do Azure AD são as mesmas no Azure AD DS. Os usuários podem usar suas credenciais corporativas para computadores de ingresso no domínio, entrar interativamente ou pela área de trabalho remota e autenticar no domínio gerenciado do Azure AD DS.
* **Autenticação NTLM e Kerberos:** Com suporte para autenticação NTLM e Kerberos, você pode implantar aplicativos que dependem da autenticação integrada do Windows.
* **Alta disponibilidade:** O Azure AD DS inclui vários controladores de domínio, que fornecem alta disponibilidade para seu domínio gerenciado. Essa alta disponibilidade garante o tempo de atividade do serviço e a resiliência a falhas.
    * Em regiões que dão suporte a [zonas de disponibilidade do Azure][availability-zones], esses controladores de domínio também são distribuídos entre zonas para resiliência adicional. 

Alguns aspectos principais de um domínio gerenciado AD DS do Azure incluem o seguinte:

* O domínio gerenciado AD DS do Azure é um domínio autônomo. Não é uma extensão de um domínio local.
* Sua equipe de ti não precisa gerenciar, aplicar patches ou monitorar controladores de domínio para este domínio gerenciado do Azure AD DS.

Para ambientes híbridos que executam o AD DS local, você não precisa gerenciar a replicação do AD para o domínio gerenciado do Azure AD DS. As contas de usuário, as associações de grupo e as credenciais do seu diretório local são sincronizadas com o Azure AD via [Azure ad Connect][azure-ad-connect]. Essas contas de usuário, associações de grupo e credenciais ficam automaticamente disponíveis no domínio gerenciado AD DS do Azure.

## <a name="how-does-azure-ad-ds-work"></a>Como funciona o Azure AD DS?

Para fornecer serviços de identidade, o Azure cria uma instância de AD DS em uma rede virtual de sua escolha. Nos bastidores e sem a necessidade de gerenciar, proteger ou atualizar, a redundância é fornecida por meio de um par de controladores de domínio do Windows Server.

O domínio gerenciado AD DS do Azure é configurado para executar uma sincronização unidirecional do Azure AD para fornecer acesso a um conjunto central de usuários, grupos e credenciais. Você pode criar recursos diretamente no domínio gerenciado AD DS do Azure, mas eles não são sincronizados novamente com o Azure AD. Aplicativos, serviços e VMs no Azure que se conectam a essa rede virtual podem usar recursos comuns de AD DS, como ingresso no domínio, diretiva de grupo, LDAP e autenticação Kerberos/NTLM.

Em um ambiente híbrido com um ambiente de AD DS local, [Azure ad Connect][azure-ad-connect] sincroniza informações de identidade com o Azure AD.

![Sincronização no Azure AD Domain Services com o Azure AD e o Active Directory Domain Services local usando o AD Connect](./media/active-directory-domain-services-design-guide/sync-topology.png)

Para ver AD DS do Azure em ação, vamos examinar alguns exemplos:

* [AD DS do Azure para organizações híbridas](#azure-ad-ds-for-hybrid-organizations)
* [AD DS do Azure para organizações somente em nuvem](#azure-ad-ds-for-cloud-only-organizations)

### <a name="azure-ad-ds-for-hybrid-organizations"></a>AD DS do Azure para organizações híbridas

Muitas organizações executam uma infraestrutura híbrida que inclui cargas de trabalho de aplicativos locais e na nuvem. Os aplicativos herdados migrados para o Azure como parte de uma estratégia de comparação de precisão e deslocamento podem usar conexões LDAP tradicionais para fornecer informações de identidade. Para dar suporte a essa infraestrutura híbrida, as informações de identidade de um ambiente de AD DS local podem ser sincronizadas com um locatário do Azure AD. Em seguida, o Azure AD DS fornece esses aplicativos herdados no Azure com uma fonte de identidade, sem a necessidade de configurar e gerenciar a conectividade de aplicativos de volta para os serviços de diretório locais.

Vejamos um exemplo para a Litware Corporation, uma organização híbrida que executa recursos locais e do Azure:

![Azure Active Directory Domain Services para uma organização híbrida que inclui sincronização local](./media/overview/synced-tenant.png)

* Aplicativos e cargas de trabalho de servidor que exigem serviços de domínio são implantados em uma rede virtual no Azure.
    * Isso pode incluir aplicativos herdados migrados para o Azure como parte de uma estratégia de comparação de precisão e deslocamento.
* Para sincronizar informações de identidade de seu diretório local para seu locatário do Azure AD, a Litware Corporation implanta [Azure ad Connect][azure-ad-connect].
    * As informações de identidade sincronizadas incluem contas de usuário e associações de grupo.
* A equipe de ti da Litware habilita o Azure AD DS para seu locatário do Azure AD nesta ou em uma rede virtual emparelhada.
* Os aplicativos e as VMs implantados na rede virtual do Azure podem usar recursos do Azure AD DS como ingresso no domínio, leitura LDAP, associação LDAP, autenticação NTLM e Kerberos e Política de Grupo.

> [!IMPORTANT]
> Azure AD Connect só devem ser instalados e configurados para sincronização com ambientes de AD DS locais. Não há suporte para instalar Azure AD Connect em um domínio gerenciado do Azure AD DS para sincronizar objetos de volta para o Azure AD.

### <a name="azure-ad-ds-for-cloud-only-organizations"></a>AD DS do Azure para organizações somente em nuvem

Um locatário do Azure AD somente na nuvem não tem uma fonte de identidade local. As contas de usuário e associações de grupo, por exemplo, são criadas e gerenciadas diretamente no Azure AD.

Agora, vejamos um exemplo de contoso, uma organização somente em nuvem que usa apenas o Azure AD para identidade. Todas as identidades de usuário, suas credenciais e associações de grupo são criadas e gerenciadas no Azure AD. Não há nenhuma configuração adicional de Azure AD Connect para sincronizar informações de identidade de um diretório local.

![Azure Active Directory Domain Services para uma organização somente em nuvem sem sincronização local](./media/overview/cloud-only-tenant.png)

* Aplicativos e cargas de trabalho de servidor que exigem serviços de domínio são implantados em uma rede virtual no Azure.
* A equipe de ti da Contoso permite que o Azure AD DS para seu locatário do Azure AD nesta ou em uma rede virtual emparelhada.
* Os aplicativos e as VMs implantados na rede virtual do Azure podem usar recursos do Azure AD DS como ingresso no domínio, leitura LDAP, associação LDAP, autenticação NTLM e Kerberos e Política de Grupo.

## <a name="next-steps"></a>Passos Seguintes

Para saber mais sobre o Azure AD DS compara com outras soluções de identidade e como a sincronização funciona, consulte os seguintes artigos:

* [Comparar AD DS do Azure com o Azure AD, Active Directory Domain Services em VMs do Azure e Active Directory Domain Services no local][compare]
* [Saiba como Azure AD Domain Services sincroniza com seu diretório do Azure AD][synchronization]

Para começar, [crie um domínio gerenciado do Azure AD DS usando o portal do Azure][tutorial-create].

<!-- INTERNAL LINKS -->
[compare]: compare-identity-solutions.md
[synchronization]: synchronization.md
[tutorial-create]: tutorial-create-instance.md
[azure-ad-connect]: ../active-directory/hybrid/whatis-azure-ad-connect.md
[password-hash-sync]: ../active-directory/hybrid/how-to-connect-password-hash-synchronization.md
[availability-zones]: ../availability-zones/az-overview.md
