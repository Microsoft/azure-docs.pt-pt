---
title: Visão geral dos Serviços de Domínio de Diretório Ativo azure [ Microsoft Docs
description: Nesta visão geral, saiba o que o Azure Ative Directory Domain Services fornece e como usá-lo na sua organização para fornecer serviços de identidade a aplicações e serviços na nuvem.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: overview
ms.date: 01/22/2020
ms.author: iainfou
ms.openlocfilehash: ea0fa0e9d4e475a8496d1ee52b4cdfea11a13d8d
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2020
ms.locfileid: "76544117"
---
# <a name="what-is-azure-active-directory-domain-services"></a>O que é azure Ative Directory Domain Services?

O Azure Ative Directory Domain Services (Azure AD DS) fornece serviços de domínio geridos, tais como a adesão ao domínio, a política de grupo, o protocolo de acesso ao diretório leve (LDAP) e a autenticação Kerberos /NTLM que é totalmente compatível com o Diretório Ativo do Windows Server. Utiliza estes serviços de domínio sem a necessidade de implantar, gerir e corrigir controladores de domínio na nuvem. O Azure AD DS integra-se com o seu inquilino Azure AD existente, o que permite aos utilizadores assinarem usando as suas credenciais existentes. Também pode utilizar grupos e contas de utilizador existentes para garantir o acesso aos recursos, o que proporciona um aumento e transferência mais suave dos recursos no local para o Azure.

A Azure AD DS replica informações de identidade da Azure AD, por isso trabalha com inquilinos da Azure AD que são apenas em nuvem, ou sincronizados com um ambiente de Serviços de Domínio de Diretório Ativo (AD DS) no local. O mesmo conjunto de funcionalidades Azure AD DS existem para ambos os ambientes.

* Se tiver um ambiente AD DS existente no local, pode sincronizar as informações da conta de utilizador para fornecer uma identidade consistente para os utilizadores.
* Para ambientes apenas em nuvem, você não precisa de um ambiente AD DS tradicional no local para usar os serviços de identidade centralizados do Azure AD DS.

O seguinte vídeo fornece uma visão geral de como o Azure AD DS se integra com as suas aplicações e cargas de trabalho para fornecer serviços de identidade na nuvem:

<br />

>[!VIDEO https://www.youtube.com/embed/T1Nd9APNceQ]

## <a name="common-ways-to-provide-identity-solutions-in-the-cloud"></a>Formas comuns de fornecer soluções de identidade na nuvem

Quando migra as cargas de trabalho existentes para a nuvem, as aplicações conscientes do diretório podem utilizar o LDAP para ler ou escrever acesso a um diretório DS a.DS no local. As aplicações que funcionam no Windows Server são normalmente implementadas em máquinas virtuais (VMs) unidas pelo domínio para que possam ser geridas de forma segura usando a Política de Grupo. Para autenticar os utilizadores finais, as aplicações podem também contar com a autenticação integrada no Windows, como a autenticação Kerberos ou NTLM.

Os administradores de TI utilizam frequentemente uma das seguintes soluções para prestar um serviço de identidade às aplicações que funcionam no Azure:

* Configure uma ligação VPN site-to-site entre cargas de trabalho que funcionam em Azure e um ambiente AD DS no local.
    * Os controladores de domínio no local fornecem então a autenticação através da ligação VPN.
* Crie controladores de domínio de réplica utilizando máquinas virtuais Azure (VMs) para estender o domínio/floresta AD DS a partir das instalações.
    * Os controladores de domínio que funcionam em VMs Azure fornecem autenticação e replicam informações de diretório entre o ambiente AD DS no local.
* Implemente um ambiente AD DS autónomo em Azure utilizando controladores de domínio que funcionam em VMs Azure.
    * Os controladores de domínio que funcionam em VMs Azure fornecem autenticação, mas não há nenhuma informação de diretório replicada a partir de um ambiente AD DS no local.

Com estas abordagens, as ligações VPN ao diretório no local tornam as aplicações vulneráveis a falhas ou falhas de rede transitórias. Se implementar controladores de domínio utilizando VMs em Azure, a equipa de TI deve gerir os VMs, em seguida, proteger, patch, monitor, backup e problemas.

A Azure AD DS oferece alternativas à necessidade de criar ligações VPN de volta a um ambiente AD DS no local ou executar e gerir VMs em Azure para fornecer serviços de identidade. Como um serviço gerido, o Azure AD DS reduz a complexidade para criar uma solução de identidade integrada para ambientes híbridos e apenas em nuvem.

## <a name="azure-ad-ds-features-and-benefits"></a>Funcionalidades e benefícios da Azure AD DS

Para fornecer serviços de identidade a aplicações e VMs na nuvem, o Azure AD DS é totalmente compatível com um ambiente Tradicional DS AD para operações como a adesão ao domínio, o LDAP seguro (LDAPS), a política do grupo e a gestão do DNS, e o LDAP ligam e lêem suporte. O suporte de escrita LDAP está disponível para objetos criados no domínio gerido pelo Azure AD DS, mas não recursos sincronizados a partir de Azure AD. As seguintes características do Azure AD DS simplificam as operações de implantação e gestão:

* **Experiência de implantação simplificada:** O Azure AD DS está ativado para o seu inquilino Azure AD utilizando um único assistente no portal Azure.
* **Integrado com a Azure AD:** As contas de utilizador, membros do grupo e credenciais estão automaticamente disponíveis do seu inquilino Azure AD. Novos utilizadores, grupos ou alterações aos atributos do seu inquilino Azure AD ou do seu ambiente AD DS no local são automaticamente sincronizados com O DS Azure.
    * As contas em diretórios externos ligados ao seu Azure AD não estão disponíveis no Azure AD DS. As credenciais não estão disponíveis para esses diretórios externos, por isso não podem ser sincronizadas num domínio gerido pelo Azure AD DS.
* **Utilize as suas credenciais/palavras-passe corporativas:** As palavras-passe para utilizadores em Azure AD DS são as mesmas que no seu inquilino Azure AD. Os utilizadores podem usar as suas credenciais corporativas para unir máquinas de união de domínios, iniciar sessão interativa ou sobre o ambiente de trabalho remoto e autenticar contra o domínio gerido pelo Azure AD DS.
* **A autenticação NTLM e Kerberos:** Com suporte para autenticação NTLM e Kerberos, pode implementar aplicações que dependem da autenticação integrada no Windows.
* **Alta disponibilidade:** O Azure AD DS inclui vários controladores de domínio, que proporcionam uma elevada disponibilidade para o seu domínio gerido. Esta elevada disponibilidade garante o tempo de uptime do serviço e a resiliência às falhas.
    * Nas regiões que suportam as Zonas de [Disponibilidade Azure,][availability-zones]estes controladores de domínio também são distribuídos por zonas para maior resiliência.

Alguns aspectos-chave de um domínio gerido pela AD DS azure incluem o seguinte:

* O domínio gerido pelo Azure AD DS é um domínio autónomo. Não é uma extensão de um domínio no local.
    * Se necessário, você pode criar fundos florestais de saída única de Azure AD DS para um ambiente AD DS no local. Para mais informações, consulte [conceitos e funcionalidades da Floresta de Recursos para DS Azure AD.][ forest-trusts]
* A sua equipa de TI não precisa de gerir, patch ou monitorizar controladores de domínio para este domínio gerido pelo Azure AD DS.

Para ambientes híbridos que executam DS DS no local, você não precisa de gerir a replicação de AD para o domínio gerido pela AD DS Azure. As contas de utilizador, membros do grupo e credenciais do seu diretório no local são sincronizadas para Azure AD via [Azure AD Connect][azure-ad-connect]. Estas contas de utilizador, membros do grupo e credenciais estão automaticamente disponíveis dentro do domínio gerido pelo Azure AD DS.

## <a name="how-does-azure-ad-ds-work"></a>Como funciona o Azure AD DS?

Para fornecer serviços de identidade, o Azure cria uma instância AD DS numa rede virtual à sua escolha. Nos bastidores, é criado um par de controladores de domínio do Windows Server que funcionam em VMs Azure. Não é necessário gerir, configurar ou atualizar estes controladores de domínio. A plataforma Azure gere os controladores de domínio como parte do serviço Azure AD DS.

O domínio gerido pelo Azure AD DS está configurado para realizar uma sincronização de ida e meia a partir do Azure AD para fornecer acesso a um conjunto central de utilizadores, grupos e credenciais. Pode criar recursos diretamente no domínio gerido pelo Azure AD DS, mas não são sincronizados de volta ao Azure AD. Aplicações, serviços e VMs em Azure que se ligam a esta rede virtual podem então usar funcionalidades dS AD comuns, tais como afiliação de domínio, política de grupo, LDAP e autenticação Kerberos /NTLM.

Num ambiente híbrido com um ambiente AD DS no local, o [Azure AD Connect][azure-ad-connect] sincroniza informações de identidade com a Azure AD, que é então sincronizada com o Azure AD DS.

![Sincronização em Serviços de Domínio Azure AD com AD Azure e no local Serviços de Domínio ativo de diretório utilizando AD Connect](./media/active-directory-domain-services-design-guide/sync-topology.png)

Para ver o Azure AD DS em ação, vamos olhar para alguns exemplos:

* [Azure AD DS para organizações híbridas](#azure-ad-ds-for-hybrid-organizations)
* [DS Azure AD para organizações apenas em nuvem](#azure-ad-ds-for-cloud-only-organizations)

### <a name="azure-ad-ds-for-hybrid-organizations"></a>Azure AD DS para organizações híbridas

Muitas organizações gerem uma infraestrutura híbrida que inclui cargas de trabalho de aplicação de nuvens e no local. As aplicações antigas migradas para Oie como parte de uma estratégia de elevação e mudança podem usar ligações Tradicionais LDAP para fornecer informações de identidade. Para apoiar esta infraestrutura híbrida, a informação de identidade de um ambiente AD DS no local pode ser sincronizada para um inquilino da AD Azure. A Azure AD DS fornece então estas aplicações antigas em Azure com uma fonte de identidade, sem a necessidade de configurar e gerir a conectividade da aplicação de volta aos serviços de diretório no local.

Vejamos um exemplo para a Litware Corporation, uma organização híbrida que gere tanto os recursos no local como os recursos do Azure:

![Azure Ative Directory Domain Services para uma organização híbrida que inclui sincronização no local](./media/overview/synced-tenant.png)

* Aplicações e cargas de trabalho do servidor que requerem serviços de domínio são implementadas numa rede virtual no Azure.
    * Isto pode incluir aplicações antigas migradas para Azure como parte de uma estratégia de elevação e mudança.
* Para sincronizar informações de identidade do seu diretório no local para o seu inquilino Azure AD, a Litware Corporation implementa o [Azure AD Connect][azure-ad-connect].
    * A informação de identidade sincronizada inclui contas de utilizador e membros do grupo.
* A equipa de TI da Litware permite o Azure AD DS para o seu inquilino Azure AD nesta, ou numa rede virtual esparsada.
* Aplicações e VMs implantados na rede virtual Azure podem então utilizar funcionalidades DeD DS Azure Como a adesão ao domínio, leitura LDAP, ligação LDAP, autenticação NTLM e Kerberos e Política de Grupo.

> [!IMPORTANT]
> O Azure AD Connect só deve ser instalado e configurado para sincronização com ambientes AD DS no local. Não é suportado para instalar o Azure AD Connect num domínio gerido pelo Azure AD DS para sincronizar objetos de volta ao Azure AD.

### <a name="azure-ad-ds-for-cloud-only-organizations"></a>DS Azure AD para organizações apenas em nuvem

Um inquilino azure só em nuvem não tem uma fonte de identidade no local. As contas de utilizador e membros do grupo, por exemplo, são criadas e geridas diretamente em Azure AD.

Agora vamos olhar para um exemplo para Contoso, uma organização só em nuvem que usa AD Azure para identidade. Todas as identidades dos utilizadores, as suas credenciais e membros do grupo são criados e geridos em Azure AD. Não existe uma configuração adicional do Azure AD Connect para sincronizar qualquer informação de identidade a partir de um diretório no local.

![Azure Ative Directory Domain Services para uma organização só em nuvem sem sincronização no local](./media/overview/cloud-only-tenant.png)

* Aplicações e cargas de trabalho do servidor que requerem serviços de domínio são implementadas numa rede virtual no Azure.
* A equipa de TI da Contoso permite o Azure AD DS para o seu inquilino Azure AD nesta, ou numa rede virtual esparsada.
* Aplicações e VMs implantados na rede virtual Azure podem então utilizar funcionalidades DeD DS Azure Como a adesão ao domínio, leitura LDAP, ligação LDAP, autenticação NTLM e Kerberos e Política de Grupo.

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre o Azure AD DS compara-se com outras soluções identitárias e como funciona a sincronização, consulte os seguintes artigos:

* [Compare O DS Azure com a AD Azure, os Serviços de Domínio de Diretório Ativo em VMs Azure e os Serviços de Domínio de Diretório Ativo no local][compare]
* [Saiba como os Serviços de Domínio Azure AD sincronizam com o seu diretório Azure AD][synchronization]

Para começar, [crie um domínio gerido azure AD DS utilizando o portal Azure][tutorial-create].

<!-- INTERNAL LINKS -->
[compare]: compare-identity-solutions.md
[synchronization]: synchronization.md
[tutorial-create]: tutorial-create-instance.md
[azure-ad-connect]: ../active-directory/hybrid/whatis-azure-ad-connect.md
[password-hash-sync]: ../active-directory/hybrid/how-to-connect-password-hash-synchronization.md
[availability-zones]: ../availability-zones/az-overview.md
[forest-trusts]: concepts-resource-forest.md
