---
title: Comparar serviços baseados em Active Directory no Azure | Microsoft Docs
description: Nesta visão geral, você compara as diferentes ofertas de identidade para Active Directory Domain Services, Azure Active Directory e Azure Active Directory Domain Services.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: overview
ms.date: 10/30/2019
ms.author: iainfou
ms.openlocfilehash: 04a1f19ddf894467a9129e8a16c951298a6af529
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73474710"
---
# <a name="compare-self-managed-active-directory-domain-services-azure-active-directory-and-managed-azure-active-directory-domain-services"></a>Comparar Active Directory Domain Services autogerenciados, Azure Active Directory e gerenciados Azure Active Directory Domain Services

Para fornecer acesso a aplicativos, serviços ou dispositivos a uma identidade central, há três maneiras comuns de usar serviços baseados em Active Directory no Azure. Essa opção nas soluções de identidade proporciona a flexibilidade de usar o diretório mais apropriado para as necessidades da sua organização. Por exemplo, se você gerencia principalmente usuários somente de nuvem que executam dispositivos móveis, talvez não faça sentido compilar e executar sua própria solução de identidade de Active Directory Domain Services (AD DS). Em vez disso, você poderia usar apenas Azure Active Directory.

Embora as três soluções de identidade baseadas em Active Directory compartilhem um nome e uma tecnologia comuns, elas foram projetadas para fornecer serviços que atendam às diferentes demandas dos clientes. Em alto nível, essas soluções de identidade e conjuntos de recursos são:

* **Active Directory Domain Services (AD DS)** -servidor de LDAP (Lightweight Directory Access Protocol) pronto para empresas que fornece recursos importantes como identidade e autenticação, gerenciamento de objetos de computador, diretiva de grupo e relações de confiança.
    * O AD DS é um componente central em muitas organizações com um ambiente de ti local e fornece autenticação de conta de usuário principal e recursos de gerenciamento de computador.
* **Azure Active Directory (AD do Azure)** – gerenciamento de identidades e dispositivos móveis baseado em nuvem que fornece serviços de conta e autenticação de usuário para recursos como o Office 365, os aplicativos portal do Azure ou SaaS.
    * O Azure AD pode ser sincronizado com um ambiente de AD DS local para fornecer uma única identidade para os usuários que trabalham nativamente na nuvem.
* **Azure Active Directory Domain Services (Azure AD DS)** – fornece serviços de domínio gerenciado com um subconjunto de recursos de AD DS tradicionais totalmente compatíveis, como ingresso no domínio, diretiva de grupo, LDAP e autenticação Kerberos/NTLM.
    * O Azure AD DS integra-se ao Azure AD, que pode ser sincronizado com um ambiente de AD DS local, para estender casos de uso de identidade central para aplicativos Web tradicionais executados no Azure como parte de uma estratégia de comparação de precisão e deslocamento.

Este artigo de visão geral compara e contrasta como essas soluções de identidade podem trabalhar em conjunto ou devem ser usadas de forma independente, dependendo das necessidades da sua organização.

## <a name="azure-ad-ds-and-self-managed-ad-ds"></a>AD DS do Azure e autogerenciado AD DS

Se você tiver aplicativos e serviços que precisam de acesso a mecanismos de autenticação tradicionais, como Kerberos ou NTLM, há duas maneiras de fornecer Active Directory Domain Services na nuvem:

* Um domínio *gerenciado* que você cria usando Azure Active Directory Domain Services (AD DS do Azure). A Microsoft cria e gerencia os recursos necessários.
* Um domínio *autogerenciado* que você cria e configura usando recursos tradicionais, como VMS (máquinas virtuais), SO convidado do Windows Server e Active Directory Domain Services (AD DS). Em seguida, Continue administrando esses recursos.

Com o Azure AD DS, os principais componentes de serviço são implantados e mantidos para você pela Microsoft como uma experiência de domínio *gerenciada* . Você não implanta, gerencia, aplica patches e protege a infraestrutura de AD DS para componentes como as VMs, o sistema operacional Windows Server ou controladores de domínio (DCs).

O Azure AD DS fornece um subconjunto menor de recursos para o ambiente de AD DS tradicional autogerenciado, que reduz parte da complexidade do design e do gerenciamento. Por exemplo, não há florestas do AD, domínio, sites e links de replicação para design e manutenção. Para aplicativos e serviços que são executados na nuvem e precisam de acesso a mecanismos de autenticação tradicionais, como Kerberos ou NTLM, o Azure AD DS fornece uma experiência de domínio gerenciada com a quantidade mínima de sobrecarga administrativa.

Ao implantar e executar um ambiente de AD DS autogerenciado, você precisa manter toda a infraestrutura e os componentes de diretório associados. Há uma sobrecarga de manutenção adicional com um ambiente de AD DS autogerenciado, mas você pode executar tarefas adicionais, como estender o esquema ou criar relações de confiança de floresta.

Os modelos de implantação comuns para um ambiente de AD DS autogerenciado que fornece identidade para aplicativos e serviços na nuvem incluem o seguinte:

* **AD DS somente em nuvem autônoma** -as VMs do Azure são configuradas como controladores de domínio e um ambiente de AD DS de nuvem separado é criado. Esse ambiente de AD DS não se integra a um ambiente de AD DS local. Um conjunto diferente de credenciais é usado para entrar e administrar VMs na nuvem.
* **Implantação de floresta de recursos** -as VMs do Azure são configuradas como controladores de domínio e um domínio AD DS como parte de uma floresta existente é criado. Uma relação de confiança é então configurada para um ambiente de AD DS local. Outras VMs do Azure podem ingressar no domínio para essa floresta de recurso na nuvem. A autenticação do usuário é executada em uma conexão VPN/ExpressRoute para o ambiente de AD DS local.
* **Estender o domínio local para o Azure** -uma rede virtual do Azure conecta-se a uma rede local usando uma conexão VPN/ExpressRoute. As VMs do Azure se conectam a essa rede virtual do Azure, que permite ingressar no domínio para o ambiente de AD DS local.
    * Uma alternativa é criar VMs do Azure e promovê-las como controladores de domínio de réplica do domínio AD DS local. Esses controladores de domínio replicam em uma conexão VPN/ExpressRoute para o ambiente de AD DS local. O domínio AD DS local é efetivamente estendido para o Azure.

A tabela a seguir descreve alguns dos recursos que podem ser necessários para sua organização e as diferenças entre um domínio gerenciado do Azure AD DS ou um domínio de AD DS autogerenciado:

| **Funcionalidade** | **AD DS do Azure** | **AD DS autogerenciados** |
| ----------- |:---------------:|:----------------------:|
| **Serviço gerenciado**                               | **&#x2713;** | **&#x2715;** |
| **Implantações seguras**                            | **&#x2713;** | O administrador protege a implantação |
| **Servidor DNS**                                    | **&#x2713;** (serviço gerenciado) |**&#x2713;** |
| **Privilégios de administrador corporativo ou de domínio** | **&#x2715;** | **&#x2713;** |
| **Associação a um domínio**                                   | **&#x2713;** | **&#x2713;** |
| **Autenticação de domínio usando NTLM e Kerberos** | **&#x2713;** | **&#x2713;** |
| **Delegação restrita de Kerberos**               | Baseado em recursos | Baseado em recursos & com base em conta|
| **Estrutura de UO personalizada**                           | **&#x2713;** | **&#x2713;** |
| **Política de Grupo**                                  | **&#x2713;** | **&#x2713;** |
| **Extensões de esquema**                             | **&#x2715;** | **&#x2713;** |
| **Relações de confiança de domínio/floresta do AD**                     | **&#x2715;** | **&#x2713;** |
| **LDAP Seguro (LDAPs)**                           | **&#x2713;** | **&#x2713;** |
| **Leitura LDAP**                                     | **&#x2713;** | **&#x2713;** |
| **Gravação LDAP**                                    | **&#x2713;** (dentro do domínio gerenciado) | **&#x2713;** |
| **Implantações distribuídas geograficamente**                   | **&#x2715;** | **&#x2713;** |

## <a name="azure-ad-ds-and-azure-ad"></a>Azure AD DS e Azure AD

O Azure AD permite que você gerencie a identidade dos dispositivos usados pela organização e controle o acesso aos recursos corporativos desses dispositivos. Os usuários também podem registrar seu dispositivo pessoal (um modelo traga seu próprio ou BYO) com o Azure AD, que fornece uma identidade ao dispositivo. Em seguida, o Azure AD autentica o dispositivo quando um usuário entra no Azure AD e usa o dispositivo para acessar recursos protegidos. O dispositivo pode ser gerenciado usando o software MDM (gerenciamento de dispositivo móvel), como Microsoft Intune. Essa capacidade de gerenciamento permite restringir o acesso a recursos confidenciais para dispositivos gerenciados e em conformidade com políticas.

Computadores e laptops tradicionais também podem ingressar no Azure AD. Esse mecanismo oferece os mesmos benefícios de registrar um dispositivo pessoal com o Azure AD, como permitir que os usuários entrem no dispositivo usando suas credenciais corporativas.

Os dispositivos ingressados no Azure AD oferecem os seguintes benefícios:

* SSO (logon único) para aplicativos protegidos pelo Azure AD.
* Roaming de configurações de usuário em conformidade com políticas corporativas em dispositivos.
* Acesso à Windows Store para empresas usando credenciais corporativas.
* Windows Hello para empresas.
* Acesso restrito a aplicativos e recursos de dispositivos em conformidade com a política corporativa.

Os dispositivos podem ser ingressados no Azure AD com ou sem uma implantação híbrida que inclui um ambiente de AD DS local. A tabela a seguir descreve os modelos de propriedade de dispositivo comuns e como eles normalmente seriam associados a um domínio:

| **Tipo de dispositivo**                                        | **Plataformas de dispositivo**             | **Mecanismo**          |
|:----------------------------------------------------------| -------------------------------- | ---------------------- |
| Dispositivos pessoais                                          | Windows 10, iOS, Android, Mac OS | Azure AD registrado    |
| O dispositivo de propriedade da organização não ingressou no local AD DS | Windows 10                       | Ingressado no Azure AD        |
| O dispositivo de propriedade da organização ingressou em um AD DS local  | Windows 10                       | Ingressado no Azure AD híbrido |

Em um dispositivo ingressado no Azure AD, a autenticação do usuário acontece usando protocolos baseados em OAuth/OpenID Connect modernos. Esses protocolos são projetados para funcionar pela Internet e, portanto, são ótimos para cenários móveis em que os usuários acessam recursos corporativos de qualquer lugar.

Com os dispositivos ingressados no AD DS do Azure, os aplicativos podem usar os protocolos Kerberos e NTLM para autenticação, de modo que o pode dar suporte a aplicativos herdados migrados para execução em VMs do Azure como parte de uma estratégia de comparação de precisão e deslocamento. A tabela a seguir descreve as diferenças em como os dispositivos são representados e pode se autenticar no diretório:

| **Aspecto**                      | **Ingressado no Azure AD**                                 | **Ingressado no Azure AD DS**                                                    |
|:--------------------------------| --------------------------------------------------- | ------------------------------------------------------------------------- |
| Dispositivo controlado por            | Azure AD                                            | Domínio gerenciado do Azure AD DS                                                |
| Representação no diretório | Objetos de dispositivo no diretório do Azure AD            | Objetos de computador no domínio gerenciado AD DS do Azure                        |
| Autenticação                  | Protocolos baseados em OAuth/OpenID Connect              | Protocolos Kerberos e NTLM                                               |
| Gestão                      | Software de MDM (gerenciamento de dispositivo móvel), como o Intune | Política de Grupo                                                              |
| Redes                      | Funciona na Internet                             | Deve estar conectado ou emparelhado com a rede virtual em que o domínio gerenciado está implantado |
| Ótimo para...                    | Dispositivos móveis ou de área de trabalho do usuário final                  | VMs de servidor implantadas no Azure                                              |

## <a name="next-steps"></a>Passos seguintes

Para começar a usar o Azure AD DS, [crie um domínio gerenciado do azure AD DS usando o portal do Azure][tutorial-create].

<!-- INTERNAL LINKS -->
[manage-dns]: manage-dns.md
[deploy-kcd]: deploy-kcd.md
[custom-ou]: create-ou.md
[manage-gpos]: manage-group-policy.md
[tutorial-ldaps]: tutorial-configure-ldaps.md
[tutorial-create]: tutorial-create-instance.md
