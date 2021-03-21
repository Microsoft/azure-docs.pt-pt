---
title: Planeie a sua implantação de dispositivos Azure Ative Directory
description: Escolha as estratégias de integração de dispositivos AZure que vão de encontro às suas necessidades organizacionais.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: how-to
ms.date: 06/15/2020
ms.author: baselden
author: BarbaraSelden
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: fc631e4329c1df2cdcbfe57c2b43ccccf14afa85
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "97936452"
---
# <a name="plan-your-azure-active-directory-device-deployment"></a>Planeie a sua implantação de dispositivos Azure Ative Directory

Este artigo ajuda-o a avaliar os métodos para integrar o seu dispositivo com a Azure AD, escolher o plano de implementação e fornecer links-chave para ferramentas de gestão de dispositivos suportados.

A paisagem dos dispositivos a partir dos quais os seus utilizadores assinam está a expandir-se. As organizações podem fornecer computadores de secretária, portáteis, telefones, tablets e outros dispositivos. Os seus utilizadores podem trazer a sua própria variedade de dispositivos e aceder a informações de locais variados. Neste ambiente, o seu trabalho como administrador é manter os seus recursos organizacionais seguros em todos os dispositivos.

O Azure Ative Directory (Azure AD) permite à sua organização cumprir estes objetivos com a gestão da identidade do dispositivo. Pode agora obter os seus dispositivos em Azure AD e controlá-los a partir de uma localização central no [portal Azure](https://portal.azure.com/). Isto dá-lhe uma experiência unificada, segurança reforçada, e reduz o tempo necessário para configurar um novo dispositivo.

Existem vários métodos para integrar os seus dispositivos no Azure AD:

* Pode [registar dispositivos](concept-azure-ad-register.md) com Azure AD

* [Junte dispositivos](concept-azure-ad-join.md) ao Azure AD (apenas na nuvem) ou

* [Crie um AD Azure híbrido](concept-azure-ad-join-hybrid.md) entre dispositivos no seu Ative Directory e Azure AD. 

## <a name="learn"></a>Learn

Antes de começar, certifique-se de que está familiarizado com a [visão geral](overview.md)da gestão da identidade do dispositivo .

### <a name="benefits"></a>Benefícios

Os principais benefícios de dar aos seus dispositivos uma identidade AD Azure:

* Aumentar a produtividade – Com a Azure AD, os seus utilizadores podem fazer [um sso sem emenda (SSO)](./azuread-join-sso.md) para os seus recursos no local e na nuvem, o que lhes permite ser produtivos onde quer que estejam.

* Aumentar a segurança – Os dispositivos AD Azure permitem-lhe aplicar [políticas de Acesso Condicional](../conditional-access/require-managed-devices.md) a recursos baseados na identidade do dispositivo ou utilizador. As políticas de acesso condicional podem oferecer proteção extra utilizando [a Proteção de Identidade Azure AD](../identity-protection/overview-identity-protection.md). A junção de um dispositivo ao Azure AD é um pré-requisito para aumentar a sua segurança com uma estratégia [de autenticação sem palavras-passe.](../authentication/concept-authentication-passwordless.md)

* Melhorar a experiência do utilizador – Com identidades de dispositivos em Azure AD, pode fornecer aos seus utilizadores um fácil acesso aos recursos baseados na nuvem da sua organização a partir de dispositivos pessoais e corporativos. Os administradores podem ativar [o Roaming do Estado da Empresa](enterprise-state-roaming-overview.md) para uma experiência unificada em todos os dispositivos Windows.

* Simplificar a implementação e gestão – A gestão da identidade do dispositivo simplifica o processo de trazer dispositivos para a Azure AD com [o Windows Autopilot,](/windows/deployment/windows-autopilot/windows-10-autopilot) [provisão a granel](/mem/intune/enrollment/windows-bulk-enroll)e [self-service: out of Box Experience (OOBE)](../user-help/user-help-join-device-on-network.md). Pode gerir estes dispositivos com ferramentas de Gestão de Dispositivos Móveis (MDM) como [o Microsoft Intune,](/mem/intune/fundamentals/what-is-intune)e as suas identidades no [portal Azure](https://portal.azure.com/).

### <a name="training-resources"></a>Recursos de formação

Vídeo:  [Acesso condicional com controlos do dispositivo](https://youtu.be/NcONUf-jeS4)

PERGUNTAS Frequentes: Gestão de [dispositivos AZURE AD FAQ](faq.md)  e [Configurações e faQ roaming de dados](enterprise-state-roaming-faqs.md) 

## <a name="plan-the-deployment-project"></a>Planear o projeto de implantação

Considere as suas necessidades organizacionais enquanto determina a estratégia para esta implantação no seu ambiente.

### <a name="engage-the-right-stakeholders"></a>Envolver as partes interessadas certas

Quando os projetos tecnológicos falham, normalmente fazem-no devido a expectativas desajustadas no impacto, resultados e responsabilidades. Para evitar estas armadilhas, [certifique-se de que está a envolver as partes interessadas certas](../fundamentals/active-directory-deployment-plans.md) e que as partes interessadas no projeto são bem compreendidas. 

Para este plano, adicione as seguintes partes interessadas à sua lista:

| Função| Description |
| - | - |
| Administrador do dispositivo| Um representante da equipa de dispositivos que pode verificar se o plano irá satisfazer os requisitos do dispositivo da sua organização. |
| Administrador de rede| Um representante da equipa de rede que pode certificar-se de que cumpre os requisitos da rede. |
| Equipa de gestão de dispositivos| Equipa que gere o inventário dos dispositivos. |
| Equipas de administração específicas do OS| Equipas que suportam e gerem versões específicas do SISTEMA. Por exemplo, pode haver uma equipa focada no Mac ou iOS. |

### <a name="plan-communications"></a>Planear as comunicações

A comunicação é fundamental para o sucesso de qualquer novo serviço. Comunicar proativamente com os seus utilizadores como a sua experiência vai mudar, quando vai mudar, e como ganhar apoio se eles experimentarem problemas.

### <a name="plan-a-pilot"></a>Planeie um piloto

Recomendamos que a configuração inicial do seu método de integração esteja num ambiente de teste, ou com um pequeno grupo de dispositivos de teste. Consulte [as melhores práticas para um piloto.](../fundamentals/active-directory-deployment-plans.md)

A implementação híbrida Azure AD é simples, e é 100% uma tarefa de administrador sem ação do utilizador final necessária. Você pode querer fazer uma [validação controlada da ad híbrida Azure](hybrid-azuread-join-control.md) antes de permitir em toda a organização de uma só vez.

## <a name="choose-your-integration-methods"></a>Escolha os seus métodos de integração

A sua organização pode utilizar vários métodos de integração de dispositivos num único inquilino AZure AD. O objetivo é escolher os métodos(s) adequados para obter os seus dispositivos geridos de forma segura em Azure AD. Existem muitos parâmetros que impulsionam esta decisão, incluindo a propriedade, tipos de dispositivos, audiência primária e infraestrutura da sua organização.

As seguintes informações podem ajudá-lo a decidir quais os métodos de integração a utilizar.

### <a name="decision-tree-for-devices-integration"></a>Árvore de decisão para integração de dispositivos

Use esta árvore para determinar opções para dispositivos pertencentes à organização. 

> [!NOTE]
> Os cenários pessoais ou próprios do dispositivo (BYOD) não são retratados neste diagrama. Resultam sempre no registo AZure AD.

 ![Árvore de decisões](./media/plan-device-deployment/flowchart.png)

### <a name="comparison-matrix"></a>Matriz de comparação

Os dispositivos iOS e Android só podem estar registados no Azure AD. A tabela a seguir apresenta considerações de alto nível para dispositivos clientes Windows. Use-o como uma visão geral, em seguida, explore os diferentes métodos de integração em detalhe.

| Consideração | Azure AD registado| Associação ao Azure AD| Associação ao Azure AD Híbrido |
| - | - | - | - |
| **Sistemas operativos do cliente**| | |  |
| Dispositivos Windows 10| ![Marque estes valores.](./media/plan-device-deployment/check.png)| ![Marque estes valores.](./media/plan-device-deployment/check.png)| ![Marque estes valores.](./media/plan-device-deployment/check.png) |
| Dispositivos de nível inferior do Windows (Windows 8.1 ou Windows 7)| | | ![Marque estes valores.](./media/plan-device-deployment/check.png) |
|**Iniciar sinsções**| | |  |
| Credenciais locais de utilizador final| ![Marque estes valores.](./media/plan-device-deployment/check.png)| |  |
| Palavra-passe| ![Marque estes valores.](./media/plan-device-deployment/check.png)| ![Marque estes valores.](./media/plan-device-deployment/check.png)| ![Marque estes valores.](./media/plan-device-deployment/check.png) |
| PIN do dispositivo| ![Marque estes valores.](./media/plan-device-deployment/check.png)| |  |
| Windows Hello| ![Marque estes valores.](./media/plan-device-deployment/check.png)| |  |
| Windows Hello para empresas| | ![Marque estes valores.](./media/plan-device-deployment/check.png)| ![Marque estes valores.](./media/plan-device-deployment/check.png) |
| Chaves de segurança FIDO 2.0| | ![Marque estes valores.](./media/plan-device-deployment/check.png)| ![Marque estes valores.](./media/plan-device-deployment/check.png) |
| Microsoft Authenticator App (sem palavras-passe)| ![Marque estes valores.](./media/plan-device-deployment/check.png)| ![Marque estes valores.](./media/plan-device-deployment/check.png)| ![Marque estes valores.](./media/plan-device-deployment/check.png) |
|**Principais capacidades**| | |  |
| SSO para cloud resources| ![Marque estes valores.](./media/plan-device-deployment/check.png)| ![Marque estes valores.](./media/plan-device-deployment/check.png)| ![Marque estes valores.](./media/plan-device-deployment/check.png) |
| Recursos de SSO para o local| | ![Marque estes valores.](./media/plan-device-deployment/check.png)| ![Marque estes valores.](./media/plan-device-deployment/check.png) |
| Acesso Condicional <br> (Exigir que os dispositivos sejam marcados como conformes) <br> (Deve ser gerido pelo MDM)| ![Marque estes valores.](./media/plan-device-deployment/check.png) | ![Marque estes valores.](./media/plan-device-deployment/check.png)|![Marque estes valores.](./media/plan-device-deployment/check.png) |
Acesso Condicional <br>(Requera dispositivos híbridos AZure AD)| | | ![Marque estes valores.](./media/plan-device-deployment/check.png)
| Palavra-passe de self-service reiniciada a partir do ecrã de login do Windows| | ![Marque estes valores.](./media/plan-device-deployment/check.png)| ![Marque estes valores.](./media/plan-device-deployment/check.png) |
| Windows hello PIN reset| | ![Marque estes valores.](./media/plan-device-deployment/check.png)| ![Marque estes valores.](./media/plan-device-deployment/check.png) |
| Estado empresarial roaming através de dispositivos| | ![Marque estes valores.](./media/plan-device-deployment/check.png)| ![Marque estes valores.](./media/plan-device-deployment/check.png) |


## <a name="azure-ad-registration"></a>Registo Azure Ad 

Os dispositivos registados são frequentemente geridos com [o Microsoft Intune.](/mem/intune/enrollment/device-enrollment) Os dispositivos estão matriculados no Intune de várias formas, dependendo do sistema operativo. 

Os dispositivos registados Azure AD fornecem suporte para Bring Your Own Devices (BYOD) e dispositivos corporativos para SSO para cloud resources. O acesso aos recursos baseia-se nas políticas de [acesso condicional](../conditional-access/require-managed-devices.md) Azure AD aplicadas ao dispositivo e ao utilizador.

### <a name="registering-devices"></a>Registar dispositivos

Os dispositivos registados são frequentemente geridos com [o Microsoft Intune.](/mem/intune/enrollment/device-enrollment) Os dispositivos estão matriculados no Intune de várias formas, dependendo do sistema operativo. 

A BYOD e o dispositivo móvel de propriedade corporativa são registados pelos utilizadores que instalam a aplicação portal da Empresa.

* [iOS](/mem/intune/user-help/install-and-sign-in-to-the-intune-company-portal-app-ios)

* [Android](/mem/intune/user-help/enroll-device-android-company-portal)

* [Windows 10](/mem/intune/user-help/enroll-windows-10-device)

Se registar os seus dispositivos é a melhor opção para a sua organização, consulte os seguintes recursos:

* Esta visão geral dos [dispositivos registados Azure AD](concept-azure-ad-register.md).

* Esta documentação do utilizador final no [Registo do seu dispositivo pessoal na rede da sua organização.](../user-help/user-help-register-device-on-network.md)

## <a name="azure-ad-join"></a>Associação ao Azure AD

A aderimento AD AZure permite-lhe transitar para um modelo de primeira nuvem com Windows. Fornece uma grande base se estiver a planear modernizar a gestão do seu dispositivo e reduzir os custos de TI relacionados com o dispositivo. A azure AD junta-se a obras apenas com dispositivos Windows 10. Considere-o como a primeira escolha para novos dispositivos.

No entanto, [os dispositivos azure AD podem SSO para recursos no local](azuread-join-sso.md) quando estão na rede da organização, podendo autenticar para servidores no local como ficheiros, impressão e outras aplicações.

Se esta for a melhor opção para a sua organização, consulte os seguintes recursos:

* Esta visão geral do [Azure AD juntou dispositivos](concept-azure-ad-join.md).

* Familiarize-se com o [plano de implementação Azure AD Join](azureadjoin-plan.md).

### <a name="provisioning-azure-ad-join-to-your-devices"></a>Provisioning AZure AD Junte-se aos seus dispositivos

Para providenciar a Azure AD Join, tem as seguintes abordagens:

* Self-Service: [Experiência de primeira execução do Windows 10](azuread-joined-devices-frx.md)

Se tiver o Windows 10 Professional ou o Windows 10 Enterprise instalado num dispositivo, a experiência predefine o processo de instalação dos dispositivos da empresa.

* [Windows out of Box Experience (OOBE) ou a partir de Definições do Windows](../user-help/user-help-join-device-on-network.md)

* [Windows Autopilot](/windows/deployment/windows-autopilot/windows-autopilot)

* [Inscrição em Massa](/mem/intune/enrollment/windows-bulk-enroll)

Escolha o seu procedimento de implantação após uma comparação cuidadosa [destas abordagens](azureadjoin-plan.md).

Pode determinar que o Azure AD Join é a melhor solução para um dispositivo, e esse dispositivo pode já estar em estados diferentes. Aqui estão as considerações de upgrade.

| Estado atual do dispositivo| Estado do dispositivo desejado| Procedimentos |
| - | - | - |
| Domínio no local aderido| Associação ao Azure AD| Unte o dispositivo do domínio no local antes de se juntar ao Azure AD |
| Híbrido Azure AD Join| Associação ao Azure AD| Unte o dispositivo do domínio no local e do Azure AD antes de se juntar ao Azure AD |
| Azure AD registado| Associação ao Azure AD| Desregralar o dispositivo antes de se juntar ao Azure AD |


## <a name="hybrid-azure-ad-join"></a>Associação ao Azure AD Híbrido

Se tiver um ambiente de Ative Directory no local e quiser juntar-se aos seus computadores ligados ao domínio do diretório Ativo ao Azure AD, pode fazê-lo com a ad AD híbrida Azure. Suporta uma [ampla gama de dispositivos Windows](hybrid-azuread-join-plan.md), incluindo dispositivos de nível inicial do Windows e Windows.

A maioria das organizações já tem dispositivos de união de domínios e gerenciá-los através de Política de Grupo ou Gestor de Configuração do Centro de Sistema (SCCM). Nesse caso, recomendamos configurar a Azure AD Join híbrida para começar a obter benefícios enquanto alavanca o investimento existente.

Se a aderimento híbrido AZure AD é a melhor opção para a sua organização, consulte os seguintes recursos:

* Esta visão geral do [Hybrid Azure AD juntou dispositivos](concept-azure-ad-join-hybrid.md).

* Familiarize-se com o [Hybrid Azure AD junte-se ao plano de implementação.](hybrid-azuread-join-plan.md)

### <a name="provisioning-hybrid-azure-ad-join-to-your-devices"></a>Provisionando híbrido Azure AD juntar-se aos seus dispositivos

[Reveja a sua infraestrutura de identidade.](hybrid-azuread-join-plan.md) O Azure AD Connect fornece-lhe um assistente para configurar a Azure AD AD híbrida para:

* [Domínios Federados](hybrid-azuread-join-federated-domains.md)

* [Domínios geridos](hybrid-azuread-join-managed-domains.md)

Se instalar a versão necessária do Azure AD Connect não é uma opção para si, consulte [como configurar manualmente a ad AD híbrida](hybrid-azuread-join-manual.md). 

> [!NOTE] 
> O dispositivo do Windows 10, ligado ao domínio no local, tenta juntar-se automaticamente ao Azure AD para se tornar O AD Híbrido Azure, acompanhado por padrão. Isto só será bem sucedido se tiver criado o ambiente certo. 

Pode determinar que o Hybrid Azure AD Join é a melhor solução para um dispositivo, e esse dispositivo pode já estar num estado diferente. Aqui estão as considerações de upgrade.

| Estado atual do dispositivo| Estado do dispositivo desejado| Procedimentos |
| - | - | - |
| Domínio no local junta-se| Híbrido Azure AD Join| Use a ligação AD AD ou AD FS para se juntar ao Azure |
| Grupo de trabalho no local aderiu ou novo| Híbrido Azure AD Join| Suportado com [Windows Autopilot](/windows/deployment/windows-autopilot/windows-autopilot). Caso contrário, o dispositivo precisa de ser no domínio do local junto antes da AD Híbrida AZure |
| Azure AD associado| Híbrido Azure AD Join| Un-se da Azure AD, que o coloca no grupo de trabalho no local ou no estado novo. |
| Azure AD registrado| Híbrido Azure AD Join| Depende da versão do Windows. [Veja estas considerações.](hybrid-azuread-join-plan.md) |

## <a name="manage-your-devices"></a>Faça a gestão dos seus dispositivos

Depois de ter registado ou juntado os seus dispositivos ao Azure AD, utilize o [portal Azure](https://portal.azure.com/) como um local central para gerir as identidades do seu dispositivo. A página de dispositivos Azure Ative Directory permite-lhe:

* [Configure as definições do seu dispositivo](device-management-azure-portal.md#configure-device-settings)
* É preciso ser um administrador local para gerir dispositivos Windows. [O Azure AD atualiza esta subscrição dos dispositivos aderidos ao Azure AD](assign-local-admin.md), adicionando automaticamente aqueles com a função de gestor de dispositivos como administradores a todos os dispositivos associados.

Certifique-se de que mantém o ambiente limpo [gerindo dispositivos antigos](manage-stale-devices.md)e concentre os seus recursos na gestão dos dispositivos atuais.

* [Rever registos de auditoria relacionados com dispositivos](device-management-azure-portal.md#audit-logs)

### <a name="supported-device-management-tools"></a>Ferramentas de gestão de dispositivos suportadas

Os administradores podem proteger e controlar ainda mais estes dispositivos registados e unidos utilizando ferramentas adicionais de gestão de dispositivos. Estas ferramentas fornecem um meio para impor configurações exigidas pela organização, como exigir que o armazenamento seja encriptado, complexidade de palavras-passe, instalações de software e atualizações de software. 

Rever plataformas suportadas e não apoiadas para dispositivos integrados:

| Ferramentas de gestão de dispositivos| Azure AD registado| Associação ao Azure AD| Associação ao Azure AD Híbrido|
| - | - | - | - |
| [Gestão de Dispositivos Móveis (MDM) ](/windows/client-management/mdm/azure-active-directory-integration-with-mdm) <br>Exemplo: Microsoft Intune| ![Marque estes valores.](./media/plan-device-deployment/check.png)| ![Marque estes valores.](./media/plan-device-deployment/check.png)| ![Marque estes valores.](./media/plan-device-deployment/check.png)|  |
| [Cogestão com Microsoft Intune e Microsoft Endpoint Configuration Manager](/mem/configmgr/comanage/overview) <br>(Windows 10 e mais tarde)| | ![Marque estes valores.](./media/plan-device-deployment/check.png)| ![Marque estes valores.](./media/plan-device-deployment/check.png)|  |
| [Política de grupo](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh831791(v=ws.11))<br>(Apenas janelas)| | | ![Marque estes valores.](./media/plan-device-deployment/check.png)|  |



 Recomendamos que considere a [gestão de aplicações móveis do Microsoft Intune (MAM)](/mem/intune/apps/app-management) com ou sem gestão de dispositivos para dispositivos iOS ou Android registados.

 Os administradores também podem [implementar plataformas virtuais de infraestruturas de desktop (VDI)](howto-device-identity-virtual-desktop-infrastructure.md) que hospedam sistemas operativos Windows nas suas organizações para simplificar a gestão e reduzir custos através da consolidação e centralização de recursos. 

### <a name="troubleshoot-device-identities"></a>Resolver problemas de identidades de dispositivo

* [Dispositivos de resolução de problemas utilizando o comando dsregcmd](troubleshoot-device-dsregcmd.md)

* [Resolução de problemas das configurações do roaming do estado da empresa no Diretório Ativo do Azure](enterprise-state-roaming-troubleshooting.md)

Se sentir problemas com a conclusão da azure híbrida ad para dispositivos Windows ligados ao domínio, consulte:

* [Híbrido Azure AD de resolução de problemas junta-se aos dispositivos atuais do Windows](troubleshoot-hybrid-join-windows-current.md)

* [Híbrido de resolução de problemas Azure AD junta-se para dispositivos de nível baixo do Windows](troubleshoot-hybrid-join-windows-legacy.md)

## <a name="next-steps"></a>Passos seguintes

* [Planeie a sua implementação Azure AD](azureadjoin-plan.md)
* [Planeie a sua implementação híbrida Azure AD](hybrid-azuread-join-plan.md)
* [Gerir identidades do dispositivo](device-management-azure-portal.md)