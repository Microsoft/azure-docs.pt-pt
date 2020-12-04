---
title: Compare os serviços baseados no Diretório Ativo no Azure Microsoft Docs
description: Nesta visão geral, você compara as diferentes ofertas de identidade para Serviços de Domínio de Diretório Ativo, Diretório Ativo Azure e Serviços de Domínio do Diretório Ativo Azure.
services: active-directory-ds
author: MicrosoftGuyJFlo
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: overview
ms.date: 06/08/2020
ms.author: joflore
ms.openlocfilehash: 916615808bc1e28b9794b57e08960520e3abd835
ms.sourcegitcommit: c4246c2b986c6f53b20b94d4e75ccc49ec768a9a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/04/2020
ms.locfileid: "96602279"
---
# <a name="compare-self-managed-active-directory-domain-services-azure-active-directory-and-managed-azure-active-directory-domain-services"></a>Compare os serviços de domínio do diretório ativo auto-geridos, o Azure Ative Directory e os serviços de domínio do diretório ativo geridos a Azure Ative Directory

Para fornecer aplicações, serviços ou dispositivos de acesso a uma identidade central, existem três formas comuns de utilizar serviços baseados em Diretório Ativo em Azure. Esta escolha em soluções de identidade dá-lhe a flexibilidade para usar o diretório mais adequado para as necessidades da sua organização. Por exemplo, se gere maioritariamente utilizadores apenas na nuvem que executam dispositivos móveis, pode não fazer sentido construir e executar a sua própria solução de identidade Ative Directory Domain Services (AD DS). Em vez disso, podes usar o Azure Ative Directory.

Embora as três soluções de identidade baseadas em Ative Directory partilhem um nome e tecnologia comuns, são projetadas para fornecer serviços que atendam às diferentes exigências dos clientes. A alto nível, estas soluções de identidade e conjuntos de recursos são:

* **Ative Directory Domain Services (AD DS)** - Servidor de acesso a diretório leve pronto para a empresa (LDAP) que fornece funcionalidades-chave como identidade e autenticação, gestão de objetos informáticos, política de grupo e fidedignidades.
    * O AD DS é um componente central em muitas organizações com um ambiente de TI no local, e fornece recursos de autenticação de conta de utilizador e gestão de computadores.
    * Para obter mais informações, consulte a [visão geral dos Serviços de Domínio do Diretório Ativo na documentação do Windows Server][overview-adds].
* **Azure Ative Directory (Azure AD)** - Gestão de identidade e dispositivos móveis baseados na nuvem que fornece serviços de conta de utilizador e autenticação para recursos como o Microsoft 365, o portal Azure ou aplicações SaaS.
    * O Azure AD pode ser sincronizado com um ambiente AD DS no local para fornecer uma única identidade aos utilizadores que trabalham nativamente na nuvem.
    * Para mais informações sobre a Azure AD, veja [o que é o Diretório Ativo Azure?][whatis-azuread]
* **Azure Ative Directory Domain Services (Azure AD DS)** - Fornece serviços de domínio geridos com um subconjunto de funcionalidades tradicionais de AD DS tradicionais totalmente compatíveis, tais como a junção de domínio, política de grupo, LDAP e autenticação Kerberos /NTLM.
    * O Azure AD DS integra-se com o Azure AD, que por si só pode sincronizar com um ambiente AD DS no local. Esta capacidade alarga os casos de utilização de identidade central às aplicações web tradicionais que funcionam em Azure como parte de uma estratégia de elevação e mudança.
    * Para saber mais sobre a sincronização com a Azure AD e no local, veja [como os objetos e credenciais são sincronizados num domínio gerido.][synchronization]

Este artigo de visão geral compara e contrasta como estas soluções de identidade podem funcionar em conjunto, ou seriam usadas de forma independente, dependendo das necessidades da sua organização.

> [!div class="nextstepaction"]
> [Para começar, crie um domínio gerido Azure AD DS utilizando o portal Azure][tutorial-create]

## <a name="azure-ad-ds-and-self-managed-ad-ds"></a>Azure AD DS e DS AD auto-gerido

Se tiver aplicações e serviços que necessitem de acesso a mecanismos tradicionais de autenticação, como Kerberos ou NTLM, existem duas formas de fornecer Serviços de Domínio de Diretório Ativo na nuvem:

* Um *domínio gerido* que cria utilizando os Serviços de Domínio do Diretório Ativo Azure (Azure AD DS). A Microsoft cria e gere os recursos necessários.
* Um domínio *auto-gerido* que cria e configura usando recursos tradicionais como máquinas virtuais (VMs), os oss convidados do Windows Server e os Serviços de Domínio do Diretório Ativo (DS AD). Em seguida, continua a administrar estes recursos.

Com o Azure AD DS, os componentes de serviço principal são implantados e mantidos para si pela Microsoft como uma experiência de domínio *gerida.* Não implementa, gere, remendo e protege a infraestrutura AD DS para componentes como os VMs, Windows Server OS ou controladores de domínio (DCs).

O Azure AD DS fornece um subconjunto menor de funcionalidades ao ambiente tradicional auto-gerido AD DS, o que reduz parte da complexidade de design e gestão. Por exemplo, não existem florestas de AD, domínio, locais e ligações de replicação para projetar e manter. Você ainda pode [criar fundos florestais entre Azure AD DS e ambientes no local.][create-forest-trust]

Para aplicações e serviços que funcionam na nuvem e precisam de acesso a mecanismos tradicionais de autenticação, como Kerberos ou NTLM, a Azure AD DS proporciona uma experiência de domínio gerida com a quantidade mínima de despesas administrativas. Para mais informações, consulte [conceitos de Gestão para contas de utilizador, palavras-passe e administração em Azure AD DS][administration-concepts].

Quando implementa e gere um ambiente DS AD auto-gerido, tem de manter todas as infraestruturas associadas e componentes de diretório. Há uma despesa adicional de manutenção com um ambiente DS DS auto-gerido, mas você é capaz de fazer tarefas adicionais, como estender o esquema ou criar fundos florestais.

Os modelos de implementação comuns para um ambiente DS AD auto-gerido que fornece identidade a aplicações e serviços na nuvem incluem o seguinte:

* **DS AD - VMs** Azure apenas em nuvem autónoma são configurados como controladores de domínio e um ambiente AD DS separado e apenas em nuvem é criado. Este ambiente AD DS não se integra com um ambiente AD DS no local. Um conjunto diferente de credenciais é usado para iniciar e administrar VMs na nuvem.
* **Implantação de recursos florestais** - Os VMs Azure são configurados como controladores de domínio e um domínio DS AD que faz parte de uma floresta existente é criado. Uma relação de confiança é então configurada para um ambiente AD DS no local. Outros VMs Azure podem unir-se a esta floresta de recursos na nuvem. A autenticação do utilizador passa por uma ligação VPN/ExpressRoute ao ambiente AD DS no local.
* **Alargar o domínio no local ao Azure** - Uma rede virtual Azure conecta-se a uma rede no local utilizando uma ligação VPN/ExpressRoute. Os VMs Azure conectam-se a esta rede virtual Azure, que lhes permite unir o domínio ao ambiente AD DS no local.
    * Uma alternativa é criar VMs Azure e promovê-los como controladores de domínio réplica a partir do domínio AD DS no local. Estes controladores de domínio replicam-se sobre uma ligação VPN/ExpressRoute ao ambiente AD DS no local. O domínio AD DS no local é efetivamente estendido ao Azure.

A tabela a seguir descreve algumas das funcionalidades que pode necessitar para a sua organização, e as diferenças entre um domínio Azure AD DS gerido ou um domínio DS AD auto-gerido:

| **Funcionalidade** | **Azure AD DS** | **DS de AD auto-gerido** |
| ----------- |:---------------:|:----------------------:|
| **Serviço gerido**                               | **&#x2713;** | **&#x2715;** |
| **Implementações seguras**                            | **&#x2713;** | O administrador assegura a implantação |
| **Servidor DNS**                                    | **&#x2713;** (serviço gerido) |**&#x2713;** |
| **Privilégios de administrador de domínio ou empresa** | **&#x2715;** | **&#x2713;** |
| **Associação a um domínio**                                   | **&#x2713;** | **&#x2713;** |
| **Autenticação de domínio usando NTLM e Kerberos** | **&#x2713;** | **&#x2713;** |
| **Kerberos constrangido delegação**               | Baseada em recursos | Baseada em recursos & com base em contas|
| **Estrutura personalizada da OU**                           | **&#x2713;** | **&#x2713;** |
| **Política de Grupo**                                  | **&#x2713;** | **&#x2713;** |
| **Extensões de esquema**                             | **&#x2715;** | **&#x2713;** |
| **Domínio AD / fundos florestais**                     | **&#x2713;** (apenas fundos florestais de ida e saída) | **&#x2713;** |
| **LDAP seguro (LDAPS)**                           | **&#x2713;** | **&#x2713;** |
| **LDAP ler**                                     | **&#x2713;** | **&#x2713;** |
| **Escrita LDAP**                                    | **&#x2713;** (dentro do domínio gerido) | **&#x2713;** |
| **Implantações geo-distribuídas**                   | **&#x2715;** | **&#x2713;** |

## <a name="azure-ad-ds-and-azure-ad"></a>Azure AD DS e Azure AD

O Azure AD permite-lhe gerir a identidade dos dispositivos utilizados pela organização e controlar o acesso aos recursos corporativos a partir desses dispositivos. Os utilizadores também podem registar o seu dispositivo pessoal (um modelo bring-your-your-your-own (BYO) com a Azure AD, que fornece ao dispositivo uma identidade. O Azure AD autentica então o dispositivo quando um utilizador assina no Azure AD e utiliza o dispositivo para aceder a recursos seguros. O dispositivo pode ser gerido utilizando software Mobile Device Management (MDM) como o Microsoft Intune. Esta capacidade de gestão permite restringir o acesso a recursos sensíveis a dispositivos geridos e compatíveis com políticas.

Computadores e portáteis tradicionais também podem se juntar ao Azure AD. Este mecanismo oferece os mesmos benefícios de registar um dispositivo pessoal com a Azure AD, como permitir que os utilizadores entrem no dispositivo usando as suas credenciais corporativas.

Os dispositivos aderidos a Azure D.C. dão-lhe os seguintes benefícios:

* SSO (SSO) para aplicações protegidas pela Azure AD.
* Roaming compatível com a política empresarial das definições do utilizador através de dispositivos.
* Acesso à Windows Store para Negócios utilizando credenciais corporativas.
* Windows Hello for Business.
* Acesso restrito a apps e recursos de dispositivos em conformidade com a política corporativa.

Os dispositivos podem ser ligados ao Azure AD com ou sem uma implementação híbrida que inclua um ambiente AD DS no local. A tabela que se segue descreve os modelos comuns de propriedade do dispositivo e como normalmente seriam unidos a um domínio:

| **Tipo de dispositivo**                                        | **Plataformas de dispositivos**             | **Mecanismo**          |
|:----------------------------------------------------------| -------------------------------- | ---------------------- |
| Dispositivos pessoais                                          | Windows 10, iOS, Android, macOS | Azure AD registado    |
| Dispositivo de propriedade da organização não aderido a AD DS no local | Windows 10                       | Azure AD associado        |
| Dispositivo de propriedade da organização juntou-se a um DS AD no local  | Windows 10                       | associado ao Azure AD Híbrido |

Num dispositivo Azure AD ou registado, a autenticação do utilizador acontece utilizando protocolos modernos baseados em OAuth /OpenID Connect. Estes protocolos são projetados para funcionar através da internet, por isso são ótimos para cenários móveis onde os utilizadores acedem a recursos corporativos a partir de qualquer lugar.

Com dispositivos Azure AD DS, as aplicações podem usar os protocolos Kerberos e NTLM para a autenticação, assim pode suportar aplicações antigas migradas para executar em VMs Azure como parte de uma estratégia de elevação e mudança. O quadro que se segue descreve diferenças na forma como os dispositivos são representados e pode autenticar-se contra o diretório:

| **Aspeto**                      | **Azure AD-joined**                                 | **Azure AD DS-joined**                                                    |
|:--------------------------------| --------------------------------------------------- | ------------------------------------------------------------------------- |
| Dispositivo controlado por            | Azure AD                                            | Domínio gerido Azure AD DS                                                |
| Representação no diretório | Objetos do dispositivo no diretório AD Azure            | Objetos de computador no domínio gerido Azure AD DS                        |
| Autenticação                  | Protocolos baseados em OAuth / OpenID Connect              | Protocolos Kerberos e NTLM                                               |
| Gestão                      | Software mobile de Gestão de Dispositivos (MDM) como o Intune | Política de Grupo                                                              |
| Redes                      | Funciona através da internet                             | Deve ser ligado ou esprevado para a rede virtual onde o domínio gerido é implantado |
| Ótimo para...                    | Dispositivos móveis ou desktop do utilizador final                  | VMs do servidor implantados em Azure                                              |


Se o DS DS e a AD AD em prem e Azure estiverem configurados para a autenticação federada utilizando o ADFS, então não existe um haxixe de senha (corrente/válido) disponível em Azure DS. As contas de utilizadores da AD AZure criadas antes da implementação do fed auth podem ter um haxixe de senha antiga, mas isso provavelmente não corresponde a um haxixe da sua palavra-passe on-prem. Assim, a Azure AD DS não será capaz de validar as credenciais dos utilizadores

## <a name="next-steps"></a>Próximos passos

Para começar a utilizar o Azure AD DS, [crie um domínio gerido Azure AD DS utilizando o portal Azure][tutorial-create].

Também pode aprender mais sobre [conceitos de gestão para contas de utilizador, palavras-passe e administração em Azure AD DS][administration-concepts] e [como objetos e credenciais são sincronizados num domínio gerido.][synchronization]

<!-- INTERNAL LINKS -->
[manage-dns]: manage-dns.md
[deploy-kcd]: deploy-kcd.md
[custom-ou]: create-ou.md
[manage-gpos]: manage-group-policy.md
[tutorial-ldaps]: tutorial-configure-ldaps.md
[tutorial-create]: tutorial-create-instance.md
[whatis-azuread]: ../active-directory/fundamentals/active-directory-whatis.md
[overview-adds]: /windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview
[create-forest-trust]: tutorial-create-forest-trust.md
[administration-concepts]: administration-concepts.md
[synchronization]: synchronization.md
