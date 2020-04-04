---
title: Compare serviços baseados em Diretório Ativo no Azure Microsoft Docs
description: Nesta visão geral, você compara as diferentes ofertas de identidade para Ative Directory Domain Services, Azure Ative Directory e Azure Ative Directory Domain Services.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: overview
ms.date: 03/30/2020
ms.author: iainfou
ms.openlocfilehash: 5925e3374634dd4db4bdc6855949dc3880d8de7c
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/03/2020
ms.locfileid: "80655520"
---
# <a name="compare-self-managed-active-directory-domain-services-azure-active-directory-and-managed-azure-active-directory-domain-services"></a>Compare serviços de domínio ativo autogeridos, Diretório Ativo Azure e gerido serviços de domínio de diretório ativo azure

Para fornecer aplicações, serviços ou dispositivos de acesso a uma identidade central, existem três formas comuns de utilizar serviços baseados em Diretório ativo em Azure. Esta escolha em soluções identitárias dá-lhe a flexibilidade para usar o diretório mais adequado para as necessidades da sua organização. Por exemplo, se gere principalmente utilizadores apenas na nuvem que executam dispositivos móveis, pode não fazer sentido construir e executar a sua própria solução de identidade Ative Directory Domain Services (AD DS). Em vez disso, podias usar o Diretório Ativo Azure.

Embora as três soluções de identidade baseadas no Ative Directory partilhem um nome e tecnologia comuns, são projetadas para fornecer serviços que vão ao encontro das diferentes exigências dos clientes. A alto nível, estas soluções de identidade e conjuntos de funcionalidades são:

* **Ative Directory Domain Services (AD DS)** - Servidor de protocolo de acesso leve de diretório (LDAP) pronto para a empresa que fornece funcionalidades-chave como identidade e autenticação, gestão de objetos informáticos, política de grupo e fidedignidades.
    * O AD DS é um componente central em muitas organizações com um ambiente informático no local, e fornece funcionalidades de autenticação de conta de utilizador e gestão de computadores.
    * Para mais informações, consulte a visão geral dos Serviços de Domínio do [Diretório Ativo na documentação do Windows Server][overview-adds].
* **Azure Ative Directory (Azure AD)** - Gestão de identidade e dispositivos móveis baseadona na nuvem que fornece serviços de conta de utilizador e autenticação para recursos como o Office 365, o portal Azure ou aplicações SaaS.
    * A Azure AD pode ser sincronizada com um ambiente AD DS no local para fornecer uma única identidade aos utilizadores que funcionam de forma nativa na nuvem.
    * Para mais informações sobre o Azure AD, consulte [o what is Azure Ative Directory?][whatis-azuread]
* **Azure Ative Directory Domain Services (Azure AD DS)** - Fornece serviços de domínio geridos com um subconjunto de funcionalidades tradicionais de DS ad si compatíveis, tais como a adesão ao domínio, política de grupo, LDAP e autenticação Kerberos/NTLM.
    * A Azure AD DS integra-se com a Azure AD, que por si só pode sincronizar com um ambiente AD DS no local. Esta capacidade alarga os casos centrais de utilização da identidade às aplicações web tradicionais que funcionam em Azure como parte de uma estratégia de elevação e mudança.

Este artigo de visão geral compara e contrasta como estas soluções de identidade podem funcionar em conjunto, ou seriam usadas de forma independente, dependendo das necessidades da sua organização.

Para começar, [crie um domínio gerido azure AD DS utilizando o portal Azure][tutorial-create].

## <a name="azure-ad-ds-and-self-managed-ad-ds"></a>Azure AD DS e AD DS autogerido

Se tiver aplicações e serviços que necessitem de acesso a mecanismos tradicionais de autenticação, como kerberos ou NTLM, existem duas formas de fornecer serviços de domínio de diretório ativo na nuvem:

* Um domínio *gerido* que cria utilizando serviços de domínio de diretório ativo Azure (Azure AD DS). A Microsoft cria e gere os recursos necessários.
* Um domínio *autogerido* que cria e configura utilizando recursos tradicionais, como máquinas virtuais (VMs), os os so e serviços de domínio de diretório ativo (AD DS). Em seguida, continua a administrar estes recursos.

Com o Azure AD DS, os componentes de serviço principal são implantados e mantidos para si pela Microsoft como uma experiência de domínio *gerida.* Não implementa, gere, patch e protege a infraestrutura AD DS para componentes como os VMs, Windows Server OS ou controladores de domínio (DCs).

O Azure AD DS fornece um subconjunto menor de funcionalidades para o ambiente AD DS tradicional autogerido, o que reduz parte da complexidade de design e gestão. Por exemplo, não há florestas, domínios, locais e links de replicação para projetar e manter. Para aplicações e serviços que funcionam na nuvem e precisam de acesso a mecanismos tradicionais de autenticação como Kerberos ou NTLM, o Azure AD DS proporciona uma experiência de domínio gerida com a quantidade mínima de despesas administrativas.

Quando se implanta e executa um ambiente AD DS autogerido, tem de manter todas as infraestruturas e componentes de diretórios associados. Há uma manutenção adicional com um ambiente AD DS autogerido, mas depois és capaz de fazer tarefas adicionais, como estender o esquema ou criar fundos florestais.

Os modelos comuns de implantação para um ambiente AD DS autogerido que fornece identidade a aplicações e serviços na nuvem incluem o seguinte:

* **AD DS** - VMs azure autónomos são configurados como controladores de domínio e um ambiente AD DS separado e apenas em nuvem é criado. Este ambiente AD DS não se integra com um ambiente AD DS no local. Um conjunto diferente de credenciais é usado para iniciar sessão e administrar VMs na nuvem.
* **Implantação** da floresta de recursos - Os VMs azure são configurados como controladores de domínio e um domínio AD DS que faz parte de uma floresta existente é criado. Uma relação de confiança é então configurada para um ambiente AD DS no local. Outros VMs Azure podem unir-se a esta floresta de recursos na nuvem. A autenticação do utilizador passa por cima de uma ligação VPN/ExpressRoute ao ambiente AD DS no local.
* Alargar o **domínio no local ao Azure** - Uma rede virtual Azure liga-se a uma rede no local utilizando uma ligação VPN/ExpressRoute. Os VMs Azure ligam-se a esta rede virtual Azure, que lhes permite aderir ao ambiente AD DS no local.
    * Uma alternativa é criar VMs Azure e promovê-los como controladores de domínio de réplica a partir do domínio AD DS no local. Estes controladores de domínio replicam-se sobre uma ligação VPN/ExpressRoute ao ambiente AD DS no local. O domínio AD DS no local é efetivamente estendido para Azure.

A tabela que se segue descreve algumas das funcionalidades que pode necessitar para a sua organização, e as diferenças entre um domínio Azure AD DS gerido ou um domínio AD DS autogerido:

| **Funcionalidade** | **Azure AD DS** | **DS AD autogerido** |
| ----------- |:---------------:|:----------------------:|
| **Serviço gerido**                               | **&#x2713;** | **&#x2715;** |
| **Implementações seguras**                            | **&#x2713;** | Administrador assegura a implantação |
| **Servidor DNS**                                    | **&#x2713;** (serviço gerido) |**&#x2713;** |
| **Privilégios de administrador de domínio ou empresa** | **&#x2715;** | **&#x2713;** |
| **Associação a um domínio**                                   | **&#x2713;** | **&#x2713;** |
| **Autenticação de domínio usando NTLM e Kerberos** | **&#x2713;** | **&#x2713;** |
| **Delegação restrita de Kerberos**               | Baseado em recursos | Baseada em & baseada em recursos|
| **Estrutura personalizada da OU**                           | **&#x2713;** | **&#x2713;** |
| **Política de Grupo**                                  | **&#x2713;** | **&#x2713;** |
| **Extensões de esquema**                             | **&#x2715;** | **&#x2713;** |
| **Domínio ad / fundos florestais**                     | **&#x2713;** (apenas uma via de saída da floresta) | **&#x2713;** |
| **LDAP seguro (LDAPS)**                           | **&#x2713;** | **&#x2713;** |
| **LDAP ler**                                     | **&#x2713;** | **&#x2713;** |
| **LDAP escrever**                                    | **&#x2713;** (dentro do domínio gerido) | **&#x2713;** |
| **Implantações geodistribuídas**                   | **&#x2715;** | **&#x2713;** |

## <a name="azure-ad-ds-and-azure-ad"></a>Azure AD DS e Azure AD

A Azure AD permite-lhe gerir a identidade dos dispositivos utilizados pela organização e controlar o acesso aos recursos corporativos a partir desses dispositivos. Users can also register their personal device (a bring-your-own (BYO) model) with Azure AD, which provides the device with an identity. A Azure AD autentica o dispositivo quando um utilizador entra em Anúncio Azure e utiliza o dispositivo para aceder a recursos seguros. O dispositivo pode ser gerido utilizando software de Gestão de Dispositivos Móveis (MDM) como o Microsoft Intune. Esta capacidade de gestão permite-lhe restringir o acesso a recursos sensíveis a dispositivos geridos e compatíveis com políticas.

Computadores e portáteis tradicionais também podem aderir ao Azure AD. Este mecanismo oferece os mesmos benefícios de registar um dispositivo pessoal com a AD Azure, de modo a permitir que os utilizadores inscrevam no dispositivo usando as suas credenciais corporativas.

Os dispositivos unidos da Azure AD dão-lhe os seguintes benefícios:

* Inscrição única (SSO) para aplicações garantidas pela Azure AD.
* Roaming compatível com a política da empresa das definições de utilizador através dos dispositivos.
* Acesso à Windows Store for Business utilizando credenciais corporativas.
* Windows Olá para negócios.
* Acesso restrito a apps e recursos de dispositivos em conformidade com a política corporativa.

Os dispositivos podem ser unidos ao Azure AD com ou sem uma implantação híbrida que inclua um ambiente AD DS no local. A tabela seguinte descreve modelos comuns de propriedade do dispositivo e como eles normalmente seriam unidos a um domínio:

| **Tipo de dispositivo**                                        | **Plataformas de dispositivos**             | **Mecanismo**          |
|:----------------------------------------------------------| -------------------------------- | ---------------------- |
| Dispositivos pessoais                                          | Windows 10, iOS, Android, Mac OS | Azure AD registado    |
| Dispositivo de propriedade da organização não se juntou ao AD DS no local | Windows 10                       | Azure AD associado        |
| Dispositivo de propriedade da organização juntou-se a um AD DS no local  | Windows 10                       | associado ao Azure AD Híbrido |

Num dispositivo azure ad-join ou registado, a autenticação do utilizador acontece usando protocolos modernos baseados em OAuth / OpenID Connect. Estes protocolos são projetados para funcionar através da internet, por isso são ótimos para cenários móveis onde os utilizadores acedem a recursos corporativos de qualquer lugar.

Com dispositivos azure AD DS, as aplicações podem usar os protocolos Kerberos e NTLM para autenticação, assim podem suportar aplicações antigas migradas para executar em VMs Azure como parte de uma estratégia de elevação e mudança. A tabela que se segue descreve diferenças na forma como os dispositivos são representados e pode autenticar-se contra o diretório:

| **Aspeto**                      | **Azure AD-joined**                                 | **Azure AD DS-joined**                                                    |
|:--------------------------------| --------------------------------------------------- | ------------------------------------------------------------------------- |
| Dispositivo controlado por            | Azure AD                                            | Domínio gerido pela Azure AD DS                                                |
| Representação no diretório | Objetos do dispositivo no diretório DaD Azure            | Objetos de computador no domínio gerido pela AD DS azure                        |
| Autenticação                  | Protocolos baseados em OAuth / OpenID Connect              | Protocolos Kerberos e NTLM                                               |
| Gestão                      | Software de Gestão de Dispositivos Móveis (MDM) como Intune | Política de Grupo                                                              |
| Redes                      | Funciona através da internet                             | Deve ser ligado ou espreitar com a rede virtual onde o domínio gerido é implantado |
| Ótimo para...                    | Dispositivos móveis ou desktop de utilizador final                  | VMs de servidor implantados em Azure                                              |

## <a name="next-steps"></a>Passos seguintes

Para começar com a utilização de Azure AD DS, [crie um domínio gerido azure AD DS utilizando o portal Azure][tutorial-create].

<!-- INTERNAL LINKS -->
[manage-dns]: manage-dns.md
[deploy-kcd]: deploy-kcd.md
[custom-ou]: create-ou.md
[manage-gpos]: manage-group-policy.md
[tutorial-ldaps]: tutorial-configure-ldaps.md
[tutorial-create]: tutorial-create-instance.md
[whatis-azuread]: ../active-directory/fundamentals/active-directory-whatis.md
[overview-adds]: /windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview