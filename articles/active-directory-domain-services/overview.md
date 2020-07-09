---
title: Visão geral dos Serviços de Domínio do Diretório Ativo Azure / Microsoft Docs
description: Nesta visão geral, saiba o que a Azure Ative Directory Domain Services fornece e como usá-lo na sua organização para fornecer serviços de identidade a aplicações e serviços na nuvem.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: overview
ms.date: 06/08/2020
ms.author: iainfou
ms.openlocfilehash: 472ff9de069e7d95cb1753a6b05830649806d2fc
ms.sourcegitcommit: c4ad4ba9c9aaed81dfab9ca2cc744930abd91298
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/12/2020
ms.locfileid: "84734559"
---
# <a name="what-is-azure-active-directory-domain-services"></a>O que é Azure Ative Directory Domain Services?

A Azure Ative Directory Domain Services (Azure AD DS) fornece serviços de domínio geridos, tais como a adesão de domínio, a política de grupo, o protocolo de acesso ao diretório leve (LDAP) e a autenticação Kerberos /NTLM. Utiliza estes serviços de domínio sem a necessidade de implantar, gerir e remendar controladores de domínio (DCs) na nuvem.

Um domínio gerido é um espaço de nome DNS e diretório correspondente. O domínio gerido integra-se com o inquilino Azure AD existente, o que permite aos utilizadores assinarem usando as suas credenciais existentes. Também pode utilizar grupos e contas de utilizador existentes para garantir o acesso aos recursos, o que proporciona um levantamento e mudança mais suaves dos recursos no local para a Azure.

A Azure AD DS integra-se com o seu inquilino AZure AD existente. Esta integração permite que os utilizadores inscrevam-se no serviço e aplicações ligadas ao domínio gerido utilizando as suas credenciais existentes. Também pode utilizar grupos e contas de utilizador existentes para garantir o acesso aos recursos. Estas características proporcionam um levantamento e mudança mais suaves dos recursos no local para Azure.

> [!div class="nextstepaction"]
> [Para começar, crie um domínio gerido Azure AD DS utilizando o portal Azure][tutorial-create]

O Azure AD DS replica informações de identidade da Azure AD, pelo que funciona com inquilinos da AD Azure que são apenas cloud, ou sincronizados com um ambiente de Serviços de Domínio de Diretório Ativo (AD DS) no local. O mesmo conjunto de funcionalidades AD DS Azure existe para ambos os ambientes.

* Se tiver um ambiente AD DS existente no local, pode sincronizar as informações da conta do utilizador para fornecer uma identidade consistente para os utilizadores. Para saber mais, veja [como os objetos e credenciais são sincronizados num domínio gerido.][synchronization]
* Para ambientes apenas em nuvem, você não precisa de um ambiente tradicional no local AD DS para usar os serviços de identidade centralizados de Azure AD DS.

Para aprender a administrar um domínio gerido, consulte [conceitos de gestão para contas de utilizador, palavras-passe e administração em Azure AD DS][administration-concepts].

O vídeo que se segue fornece uma visão geral de como o Azure AD DS se integra com as suas aplicações e cargas de trabalho para fornecer serviços de identidade na nuvem:

<br />

>[!VIDEO https://www.youtube.com/embed/T1Nd9APNceQ]

## <a name="common-ways-to-provide-identity-solutions-in-the-cloud"></a>Formas comuns de fornecer soluções identitárias na nuvem

Quando migrar cargas de trabalho existentes para a nuvem, as aplicações conscientes do diretório podem usar LDAP para ler ou escrever acesso a um diretório AD DS no local. As aplicações que funcionam no Windows Server são normalmente implementadas em máquinas virtuais (VMs) de domínio para que possam ser geridas de forma segura utilizando a Política de Grupo. Para autenticar os utilizadores finais, as aplicações podem também contar com a autenticação integrada do Windows, como a autenticação Kerberos ou a autenticação NTLM.

Os administradores de TI usam frequentemente uma das seguintes soluções para fornecer um serviço de identidade às aplicações que funcionam em Azure:

* Configure uma ligação VPN local-a-local entre cargas de trabalho que funcionam em Azure e um ambiente AD DS no local.
    * Os controladores de domínio no local fornecem então a autenticação através da ligação VPN.
* Crie controladores de domínio de réplica utilizando máquinas virtuais Azure (VMs) para estender o domínio/floresta AD DS a partir das instalações.
    * Os controladores de domínio que funcionam em VMs Azure fornecem autenticação e replicam informações de diretório entre o ambiente AD DS no local.
* Implementar um ambiente AD DS autónomo em Azure utilizando controladores de domínio que funcionam em VMs Azure.
    * Os controladores de domínio que funcionam em VMs Azure fornecem autenticação, mas não há nenhuma informação de diretório replicada a partir de um ambiente AD DS no local.

Com estas abordagens, as ligações VPN ao diretório no local tornam as aplicações vulneráveis a falhas ou falhas de rede transitórias. Se implementar controladores de domínio utilizando VMs em Azure, a equipa de TI deve gerir os VMs, então proteger, corrigir, monitorizar, fazer backup e resolver problemas.

O Azure AD DS oferece alternativas à necessidade de criar ligações VPN de volta a um ambiente AD DS no local ou executar e gerir VMs em Azure para fornecer serviços de identidade. Como um serviço gerido, a Azure AD DS reduz a complexidade para criar uma solução de identidade integrada para ambientes híbridos e apenas em nuvem.

> [!div class="nextstepaction"]
> [Compare O AD DS Azure com AZure AD e DS AD auto-gerido em VMs ou no local][compare]

## <a name="how-does-azure-ad-ds-work"></a>Como funciona o Azure AD DS?

Para fornecer serviços de identidade, a Azure cria um domínio gerido por DS AD numa rede virtual à sua escolha. Nos bastidores, é criado um par de controladores de domínio do Windows Server que funcionam em VMs Azure. Não precisa de gerir, configurar ou atualizar estes controladores de domínio. A plataforma Azure gere os controladores de domínio como parte do serviço Azure AD DS.

O domínio gerido está configurado para realizar uma sincronização unidirecional a partir da Azure AD para fornecer acesso a um conjunto central de utilizadores, grupos e credenciais. Pode criar recursos diretamente no domínio gerido, mas não são sincronizados de volta ao Azure AD. Aplicações, serviços e VMs em Azure que se conectam a esta rede virtual podem então usar funcionalidades comuns de DS AD tais como a adesão de domínio, política de grupo, LDAP e a autenticação Kerberos /NTLM.

Num ambiente híbrido com um ambiente AD DS no local, [o Azure AD Connect][azure-ad-connect] sincroniza a informação de identidade com o Azure AD, que é então sincronizado com Azure AD DS.

![Sincronização em Serviços de Domínio AD AD Azure com Azure AD e no local Serviços de Domínio de Diretório Ativo usando AD Connect](./media/active-directory-domain-services-design-guide/sync-topology.png)

Para ver a Azure AD DS em ação, vamos olhar para alguns exemplos:

* [Azure AD DS para organizações híbridas](#azure-ad-ds-for-hybrid-organizations)
* [Azure AD DS para organizações só em nuvem](#azure-ad-ds-for-cloud-only-organizations)

### <a name="azure-ad-ds-for-hybrid-organizations"></a>Azure AD DS para organizações híbridas

Muitas organizações gerem uma infraestrutura híbrida que inclui cargas de trabalho de aplicação em nuvem e no local. As aplicações antigas migradas para Azure como parte de uma estratégia de elevação e mudança podem usar ligações LDAP tradicionais para fornecer informações de identidade. Para apoiar esta infraestrutura híbrida, a informação de identidade de um ambiente AD DS no local pode ser sincronizada com um inquilino AD AZure. A Azure AD DS fornece então estas aplicações antigas em Azure com uma fonte de identidade, sem a necessidade de configurar e gerir a conectividade da aplicação de volta aos serviços de diretório no local.

Vejamos um exemplo para a Litware Corporation, uma organização híbrida que gere tanto os recursos no local como os recursos da Azure:

![Azure Ative Directory Domain Services para uma organização híbrida que inclui sincronização no local](./media/overview/synced-tenant.png)

* As aplicações e as cargas de trabalho do servidor que requerem serviços de domínio são implementadas numa rede virtual em Azure.
    * Isto pode incluir aplicações antigas migradas para Azure como parte de uma estratégia de elevação e mudança.
* Para sincronizar informações de identidade do seu diretório no local para o seu inquilino AZURE AD, a Litware Corporation implementa [o Azure AD Connect][azure-ad-connect].
    * A informação de identidade sincronizada inclui contas de utilizador e membros do grupo.
* A equipa de TI da Litware permite que o Azure AD DS para o seu inquilino AZure AD nesta, ou uma rede virtual esprevada.
* Aplicações e VMs implantados na rede virtual Azure podem então usar funcionalidades Azure AD DS como aderente de domínio, LDAP ler, ligação LDAP, autenticação NTLM e Kerberos, e Política de Grupo.

> [!IMPORTANT]
> O Azure AD Connect só deve ser instalado e configurado para sincronização com ambientes AD DS no local. Não é suportado para instalar o Azure AD Connect num domínio gerido para sincronizar objetos de volta ao AZure AD.

### <a name="azure-ad-ds-for-cloud-only-organizations"></a>Azure AD DS para organizações só em nuvem

Um inquilino da AD Azure só na nuvem não tem uma fonte de identidade no local. As contas de utilizador e os membros do grupo, por exemplo, são criados e geridos diretamente no Azure AD.

Agora vamos olhar para um exemplo para Contoso, uma organização só em nuvem que usa Azure AD para identidade. Todas as identidades dos utilizadores, as suas credenciais e membros do grupo são criados e geridos em Azure AD. Não existe nenhuma configuração adicional do Azure AD Connect para sincronizar qualquer informação de identidade a partir de um diretório no local.

![Azure Ative Directory Domain Services para uma organização só em nuvem sem sincronização no local](./media/overview/cloud-only-tenant.png)

* As aplicações e as cargas de trabalho do servidor que requerem serviços de domínio são implementadas numa rede virtual em Azure.
* A equipa de TI da Contoso permite que o Azure AD DS para o seu inquilino AZure AD nesta, ou uma rede virtual esprevada.
* Aplicações e VMs implantados na rede virtual Azure podem então usar funcionalidades Azure AD DS como aderente de domínio, LDAP ler, ligação LDAP, autenticação NTLM e Kerberos, e Política de Grupo.

## <a name="azure-ad-ds-features-and-benefits"></a>Funcionalidades e benefícios do Azure AD DS

Para fornecer serviços de identidade a aplicações e VMs na nuvem, o Azure AD DS é totalmente compatível com um ambiente tradicional de DS AD para operações como união de domínios, LDAP seguro (LDAPS), Política de Grupo, gestão de DNS e suporte de ligação e leitura LDAP. O suporte de escrita LDAP está disponível para objetos criados no domínio gerido Azure AD DS, mas não recursos sincronizados a partir de Azure AD.

Para saber mais sobre as suas opções de identidade, [compare o Azure AD DS com a Azure AD, os Serviços de Domínio do Diretório Ativo em VMs Azure e os Serviços de Domínio de Diretório Ativo no local.][compare]

As seguintes características da Azure AD DS simplificam as operações de implantação e gestão:

* **Experiência de implementação simplificada:** O Azure AD DS está ativado para o seu inquilino AZure AD usando um único assistente no portal Azure.
* **Integrado com Azure AD:** As contas de utilizador, as filiações do grupo e as credenciais estão automaticamente disponíveis no seu inquilino AZure AD. Novos utilizadores, grupos ou alterações a atributos do seu inquilino AZure AD ou do ambiente AD DS no local são automaticamente sincronizados para Azure AD DS.
    * As contas em diretórios externos ligados ao seu AD Azure não estão disponíveis em Azure AD DS. As credenciais não estão disponíveis para esses diretórios externos, por isso não podem ser sincronizadas num domínio gerido pela Azure AD DS.
* **Use as suas credenciais/senhas corporativas:** As palavras-passe para utilizadores em Azure AD DS são as mesmas que no seu inquilino AZure AD. Os utilizadores podem usar as suas credenciais corporativas para máquinas de união de domínios, assinar em ambientes de trabalho interativos ou sobre ambientes de trabalho remotos e autenticar contra o domínio gerido Azure AD DS.
* **Autenticação NTLM e Kerberos:** Com suporte para a autenticação NTLM e Kerberos, pode implementar aplicações que dependam da autenticação integrada do Windows.
* **Alta disponibilidade:** O Azure AD DS inclui vários controladores de domínio, que fornecem alta disponibilidade para o seu domínio gerido. Esta elevada disponibilidade garante o tempo de serviço e a resiliência às falhas.
    * Nas regiões que suportam [zonas de disponibilidade de Azure,][availability-zones]estes controladores de domínio também são distribuídos por zonas para maior resiliência.

Alguns aspectos-chave de um domínio gerido AZure AD DS incluem os seguintes:

* O domínio gerido Azure AD DS é um domínio autónomo. Não é uma extensão de um domínio no local.
    * Se necessário, você pode criar fundos florestais de ida e saída da Azure AD DS para um ambiente AD DS no local. Para obter mais informações, consulte [conceitos e funcionalidades da floresta de recursos para Azure AD DS.][ forest-trusts]
* A sua equipa de TI não precisa de gerir, corrigir ou monitorizar controladores de domínio para este domínio gerido pelo Azure AD DS.

Para ambientes híbridos que executam AD DS no local, você não precisa gerir a replicação de AD para o domínio gerido Azure AD DS. As contas de utilizador, os membros do grupo e as credenciais do seu diretório no local são sincronizados para Azure AD via [Azure AD Connect][azure-ad-connect]. Estas contas de utilizador, membros do grupo e credenciais estão automaticamente disponíveis dentro do domínio gerido Azure AD DS.


## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre a Azure AD DS compara-se com outras soluções de identidade e como funciona a sincronização, consulte os seguintes artigos:

* [Compare Azure AD DS com Azure AD, Ative Directory Domain Services em Azure VMs e Ative Directory Domain Services no local][compare]
* [Saiba como o Azure AD Domain Services sincroniza com o seu diretório AD Azure][synchronization]

Para começar, [crie um domínio gerido utilizando o portal Azure][tutorial-create].

<!-- INTERNAL LINKS -->
[compare]: compare-identity-solutions.md
[synchronization]: synchronization.md
[tutorial-create]: tutorial-create-instance.md
[azure-ad-connect]: ../active-directory/hybrid/whatis-azure-ad-connect.md
[password-hash-sync]: ../active-directory/hybrid/how-to-connect-password-hash-synchronization.md
[availability-zones]: ../availability-zones/az-overview.md
[forest-trusts]: concepts-resource-forest.md
[administration-concepts]: administration-concepts.md
[synchronization]: synchronization.md
