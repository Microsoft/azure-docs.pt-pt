---
title: Como decidir se dos serviços de domínio do Azure AD é a certo para seu caso de utilização
description: Comparar o Azure Active Directory Domain Services para controladores de domínio faça mesmo
services: active-directory-ds
documentationcenter: ''
author: MikeStephens-MS
manager: daveba
editor: curtand
ms.assetid: 165249d5-e0e7-4ed1-aa26-91a05a87bdc9
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: mstephen
ms.openlocfilehash: 17bb8eb910990e0e9da65491f8dd7361398479a5
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66246468"
---
# <a name="how-to-decide-if-azure-ad-domain-services-is-right-for-your-use-case"></a>Como decidir se dos serviços de domínio do Azure AD é a certo para seu caso de utilização

Com o Azure AD Domain Services pode implementar cargas de trabalho nos serviços de infraestrutura do Azure, sem ter de se preocupar sobre como manter a infraestrutura de identidade no Azure. Este serviço gerido é diferente de uma implementação típica do Windows Server Active Directory que implementar e administrar por conta própria. O serviço é fácil implementar e oferece monitorização de estado de funcionamento automatizado e remediação. Estamos constantemente a desenvolver o serviço para adicionar suporte para cenários comuns de implantação.

Para decidir se deve utilizar o Azure AD Domain Services, recomendamos o seguinte material de leitura:

* Ver a lista de [recursos oferecidos pelo Azure AD Domain Services](active-directory-ds-features.md).
* Revisão comum [cenários de implementação para o Azure AD Domain Services](scenarios.md).
* Por fim, [comparar o Azure AD Domain Services para uma opção de AD tipo faça mesmo](comparison.md#compare-azure-ad-domain-services-to-diy-ad-domain-in-azure).

## <a name="compare-azure-ad-domain-services-to-diy-ad-domain-in-azure"></a>Comparar o Azure AD Domain Services para o domínio do AD faça mesmo no Azure

A tabela seguinte ajuda-o a decidir entre a utilizar o Azure AD Domain Services e gerir a sua própria infraestrutura de AD no Azure.

| **Funcionalidade** | **Azure AD Domain Services** | **AD "Faça mesmo" em VMs do Azure** |
| --- |:---:|:---:|
| [**Serviço gerido**](comparison.md#managed-service) |**&#x2713;** |**&#x2715;** |
| [**Implementações seguras**](comparison.md#secure-deployments) |**&#x2713;** |Administrador precisa de proteger a implementação. |
| [**Servidor DNS**](comparison.md#dns-server) |**&#x2713;** (serviço gerido) |**&#x2713;** |
| [**Privilégios de administrador Corporativo ou de domínio**](comparison.md#domain-or-enterprise-administrator-privileges) |**&#x2715;** |**&#x2713;** |
| [**Associação a um domínio**](comparison.md#domain-join) |**&#x2713;** |**&#x2713;** |
| [**Autenticação de domínio utilizando o NTLM e Kerberos**](comparison.md#domain-authentication-using-ntlm-and-kerberos) |**&#x2713;** |**&#x2713;** |
| [**Delegação restringida de Kerberos**](comparison.md#kerberos-constrained-delegation)|baseada em recursos|baseada em recursos & com base na conta|
| [**Estrutura de UO personalizados**](comparison.md#custom-ou-structure) |**&#x2713;** |**&#x2713;** |
| [**Extensões de esquema**](comparison.md#schema-extensions) |**&#x2715;** |**&#x2713;** |
| [**Confianças de entidades de domínio/floresta do AD**](comparison.md#ad-domain-or-forest-trusts) |**&#x2715;** |**&#x2713;** |
| [**Leitura de LDAP**](comparison.md#ldap-read) |**&#x2713;** |**&#x2713;** |
| [**LDAP seguro (LDAPS)** ](comparison.md#secure-ldap) |**&#x2713;** |**&#x2713;** |
| [**Escrita LDAP**](comparison.md#ldap-write) |**&#x2715;** |**&#x2713;** |
| [**Política de grupo**](comparison.md#group-policy) |**&#x2713;** |**&#x2713;** |
| [**Implementações distribuídas geograficamente**](comparison.md#geo-dispersed-deployments) |**&#x2715;** |**&#x2713;** |

#### <a name="managed-service"></a>Serviço gerido

Domínios de serviços de domínio do AD do Azure são geridos pela Microsoft. Não precisa se preocupar sobre a aplicação de patches, atualizações, monitorização, cópias de segurança e garantir a disponibilidade do seu domínio. Estas tarefas de gestão são oferecidas como um serviço Microsoft Azure para os seus domínios geridos.

#### <a name="secure-deployments"></a>Implementações seguras

O domínio gerido com segurança é bloqueado de acordo com recomendações de segurança da Microsoft para implementações do AD. Estas recomendações são provenientes décadas da equipe de produto de AD de experiência em engenharia e dar suporte a implementações do AD. Para implementações do tipo faça mesmo, terá de realizar passos de implementação específicas para bloquear a secure/para baixo a implementação.

#### <a name="dns-server"></a>Servidor DNS

Um domínio gerido do Azure AD Domain Services inclui os serviços DNS geridos. Membros do grupo "Administradores do AAD DC" podem gerir o DNS no domínio gerido. Os membros deste grupo recebem todos os privilégios de administração do DNS para o domínio gerido. Gestão de DNS pode ser efetuada utilizando a "Consola de administração de DNS" incluída no pacote de ferramentas de administração remota de servidor (FARS).
[Mais informações](manage-dns.md)

#### <a name="domain-or-enterprise-administrator-privileges"></a>Privilégios de administrador Corporativo ou de domínio

Esses privilégios elevados não são oferecidos num domínio gerido do AAD-DS. Aplicativos que exigem esses privilégios elevados não podem ser implementados em relação a AAD-DS Domínios geridos. Um subconjunto mais pequeno de privilégios administrativos está disponível para membros do grupo de administração delegada chamada "Administradores do AAD DC". Esses privilégios incluem privilégios para configurar o DNS, configurar a política de grupo, obter privilégios de administrador nos computadores associados a um domínio etc.

#### <a name="domain-join"></a>Associação a um domínio

Pode associar máquinas virtuais para o domínio gerido, semelhante a como associar computadores a um domínio do AD.

#### <a name="domain-authentication-using-ntlm-and-kerberos"></a>Autenticação de domínio utilizando o NTLM e Kerberos

Com os serviços de domínio do Azure AD, pode utilizar as credenciais da empresa para autenticar com o domínio gerido. As credenciais são mantidas em sincronia com o inquilino do Azure AD. Para inquilinos sincronizados, o Azure AD Connect assegura que as alterações feitas de credenciais no local são sincronizadas com o Azure AD. Com uma configuração de domínio do tipo faça mesmo, poderá ter de configurar um domínio AD fidedignidade com o ambiente AD para os utilizadores para se autenticar com as credenciais da empresa. Em alternativa, terá de configurar a replicação de AD para garantir que as palavras-passe de utilizador sincronizar às suas máquinas de virtuais de controlador de domínio do Azure.

#### <a name="kerberos-constrained-delegation"></a>Delegação restringida de Kerberos

Não tem privilégios de administrador de domínio num domínio gerido dos serviços de domínio do Active Directory. Por conseguinte, não é possível configurar a delegação restringida de Kerberos (tradicional) com base na conta. No entanto, pode configurar a mais segura delegação restrita baseada em recursos.
[Mais informações](deploy-kcd.md)

#### <a name="custom-ou-structure"></a>Estrutura de UO personalizados

Membros do grupo "Administradores do AAD DC" podem criar UOs personalizadas dentro do domínio gerido. Os utilizadores que criar UOs personalizadas são concedidos privilégios administrativos totais sobre a UO.
[Mais informações](create-ou.md)

#### <a name="schema-extensions"></a>Extensões de esquema

Não é possível expandir o esquema de base de um domínio gerido do Azure AD Domain Services. Por conseguinte, aplicativos que dependem das extensões de esquema do AD (por exemplo, novos atributos no objeto de utilizador) não podem ser elevados e mudou para domínios de DS do AAD.

#### <a name="ad-domain-or-forest-trusts"></a>Domínio do AD ou fidedignidades de floresta

Não não possível configurar domínios geridos para configurar as relações de confiança (entrada/saída) com outros domínios. Portanto, cenários de implementação de floresta de recursos não é possível utilizar o Azure AD Domain Services. Da mesma forma, as implementações em que preferir não se sincronizar as palavras-passe para o Azure AD não é possível utilizar o Azure AD Domain Services.

#### <a name="ldap-read"></a>Leitura LDAP

O domínio gerido suporta cargas de trabalho de leitura de LDAP. Por isso pode implantar aplicativos que realizam operações de leitura de LDAP contra o domínio gerido.

#### <a name="secure-ldap"></a>LDAP seguro

Pode configurar o Azure AD Domain Services para fornecer acesso de secure LDAP ao seu domínio gerido, incluindo através da internet.
[Mais informações](configure-ldaps.md)

#### <a name="ldap-write"></a>Escrita LDAP

O domínio gerido é só de leitura para objetos de utilizador. Portanto, aplicativos que realizam operações de escrita LDAP em relação a atributos do objeto de utilizador não funcionam num domínio gerido. Além disso, palavras-passe de utilizador não podem ser alteradas no domínio gerido. Outro exemplo seria a modificação de associações de grupo ou atributos de grupo no domínio gerido, que não é permitido. No entanto, qualquer é alterado para atributos de utilizador ou palavras-passe feitas no Azure AD (por meio do portal do PowerShell/Azure) ou AD são sincronizadas com o domínio gerido do AAD-DS no local.

#### <a name="group-policy"></a>Política de grupo

Existe um GPO incorporado cada para os contentores "Computadores do aad DC" e "Utilizadores do aad DC". Pode personalizar estes GPOs incorporados para configurar a política de grupo. Membros do grupo "Administradores do AAD DC" também podem criar GPOs personalizados e ligá-las às UOs existentes (incluindo UOs personalizadas).
[Mais informações](manage-group-policy.md)

#### <a name="geo-dispersed-deployments"></a>Implementações geograficamente dispersos

Domínios de geridos de serviços de domínio do AD do Azure estão disponíveis numa única rede virtual no Azure. Para cenários que necessitam de controladores de domínio para estar disponível em várias regiões do Azure em todo o mundo, a configuração de controladores de domínio em VMs IaaS do Azure pode ser a melhor alternativa.

## <a name="do-it-yourself-diy-ad-deployment-options"></a>"Faça mesmo" Opções de implementação do (DIY) AD

Talvez tenha casos de utilização de implementação em que é necessário que alguns dos recursos oferecidos por uma instalação do Windows Server AD. Nestes casos, considere uma das seguintes opções (DIY) tipo faça mesmo:

* **Domínio de nuvem autónomo:** Pode configurar uma autónoma 'domínio na cloud' utilizando as máquinas virtuais do Azure que tenham sido configuradas como controladores de domínio. Esta infraestrutura não se integra com o ambiente ambiente AD. Esta opção requer um conjunto diferente de credenciais de cloud para início de sessão/administrar VMs na cloud.
* **Implementação de floresta de recursos:** Pode configurar um domínio na topologia de floresta de recursos, utilizando as máquinas virtuais do Azure configuradas como controladores de domínio. Em seguida, pode configurar uma relação de confiança do AD no local com o ambiente AD. Pode computadores de associação a um domínio (as VMs do Azure) para esta floresta de recursos na cloud. Autenticação do usuário ocorre ao longo do seja uma ligação de VPN/ExpressRoute para o seu diretório no local.
* **Expanda o seu domínio no local para o Azure:** Pode ligar uma rede virtual do Azure à sua rede no local através de uma ligação de VPN/ExpressRoute. Esta configuração permite que as VMs do Azure esteja associado ao seu local AD. Outra alternativa é promover os controladores de domínio de réplica do seu domínio no local no Azure como uma VM. Pode, em seguida, configurá-lo para replicar através de uma ligação de VPN/ExpressRoute para o seu diretório no local. Neste modo de implementação expande efetivamente o seu domínio no local para o Azure.

> [!NOTE]
> Pode determinar que uma opção de BRICOLAGEM melhor é adequada para seus casos de uso de implementação. Considere [partilhar comentários](contact-us.md) para ajudar-na perceber o que ajudaria funcionalidades escolheu o Azure AD Domain Services no futuro. Seus comentários ajudam-na evoluir o serviço para se adequar melhor às suas necessidades de implementação e casos de utilização.
>
>

Publicamos [diretrizes para implementar o Windows Server Active Directory em máquinas de virtuais do Azure](/windows-server/identity/ad-ds/introduction-to-active-directory-domain-services-ad-ds-virtualization-level-100) para ajudar a facilitar a instalações faça mesmo.

## <a name="related-content"></a>Conteúdo relacionado

* [Recursos - dos serviços de domínio do Azure AD](active-directory-ds-features.md)
* [Cenários de implementação - Azure AD Domain Services](scenarios.md)
* [Diretrizes para implementar o Windows Server Active Directory em máquinas virtuais do Azure](/windows-server/identity/ad-ds/introduction-to-active-directory-domain-services-ad-ds-virtualization-level-100)
