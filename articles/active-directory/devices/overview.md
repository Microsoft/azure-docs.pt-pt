---
title: O que é a gestão de dispositivos no Azure Active Directory? | Microsoft Docs
description: Saiba de que forma é que a gestão de dispositivos o pode ajudar a ter controlo sobre os dispositivos que acedem aos recursos no seu ambiente.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 54e1b01b-03ee-4c46-bcf0-e01affc0419d
ms.service: active-directory
ms.subservice: devices
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 03/01/2019
ms.author: markvi
ms.reviewer: jairoc
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0f3c1dae656e91fab91878ca2411f6cad0fc0431
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2019
ms.locfileid: "57315151"
---
# <a name="what-is-device-management-in-azure-active-directory"></a>O que é a gestão de dispositivos no Azure Active Directory?

Num mundo “mobile-first, cloud-first”, o Azure Active Directory (Azure AD) proporciona o início de sessão único em dispositivos, aplicações e serviços, em qualquer local. Com a proliferação de dispositivos, incluindo dispositivos Bring Your Own Device (BYOD), os profissionais de TI estão perante dois objetivos opostos:

- Capacitar os utilizadores finais a serem produtivos em qualquer local e em qualquer momento
- Proteger os recursos da empresa em todos os momentos

Através dos dispositivos, os seus utilizadores estão a obter acesso aos seus recursos corporativos. Para proteger os seus recursos corporativos, enquanto administrador de TI, quer controlar esses dispositivos. Isto permite garantir que os seus utilizadores estão a aceder aos seus recursos a partir de dispositivos que cumprem os seus padrões de segurança e conformidade. 

A gestão de dispositivos é também a base para cenários de [acesso condicional baseado no dispositivo](../conditional-access/require-managed-devices.md). Com o acesso condicional baseado no dispositivo, pode certificar-se de que o acesso a recursos no seu ambiente é apenas possível com os dispositivos geridos.   

Este artigo explica como é que a gestão de dispositivos funcionam no Azure Active Directory.



> [!VIDEO https://www.youtube.com/embed/NeezfKhomQU]


## <a name="getting-devices-under-the-control-of-azure-ad"></a>Ter dispositivos sob controlo do Azure AD

Para que um dispositivo esteja sob controlo do Azure AD, tem duas opções:

- Registar 
- Associar

**Registar** um dispositivo no Azure AD permite-lhe gerir a identidade do mesmo. Quando é registado um dispositivo, o registo do Azure (AD) fornece ao dispositivo uma identidade que é utilizada para o autenticar quando um utilizador inicia sessão no Azure AD. Pode utilizar a identidade para ativar ou desativar o dispositivo.

Ao serem combinados com uma solução de gestão de dispositivos móveis (MDM), como o Microsoft Intune, os atributos do dispositivo no Azure AD são atualizados com informações adicionais sobre o mesmo. Isto permite criar regras de acesso condicional que impõem o acesso a partir de dispositivos para cumprir as normas de segurança e conformidade. Para obter mais informações sobre como inscrever dispositivos no Microsoft Intune, consulte [o que é a inscrição de dispositivos?](https://docs.microsoft.com/intune/device-enrollment)

**Associar** um dispositivo é uma extensão ao registo. Isto significa que a associação lhe dá todas as vantagens de registar o dispositivo e, para além disso, também altera o estado local do mesmo. A alteração do estado local permite aos seus utilizadores iniciarem sessão num dispositivo com uma conta escolar ou profissional da organização em vez de uma conta pessoal.

## <a name="azure-ad-registered-devices"></a>Dispositivos registados no Azure AD   

O objetivo dos dispositivos registados no Azure AD é proporcionar-lhe suporte para o cenário **Bring Your Own Device (BYOD)**. Neste cenário, o utilizador pode utilizar um dispositivo pessoal para aceder aos recursos controlados pelo Azure Active Directory da sua organização.  

![Dispositivos registados no Azure AD](./media/overview/03.png)

O acesso baseia-ne numa conta escolar ou profissional que foi introduzida no dispositivo.  
Por exemplo, no Windows 10, os utilizadores podem adicionar uma conta escolar ou profissional a um computador, tablet ou telemóvel pessoal.  
Quando o utilizador adiciona uma conta escolar ou profissional, o dispositivo é registado no Azure AD e, opcionalmente, inscrito no sistema de gestão de dispositivos móveis (MDM) que a sua organização tem configurado. Os utilizadores da organização podem adicionar uma conta escolar ou profissional a um dispositivo pessoal facilmente:

- Quando acedem a uma aplicação de trabalho pela primeira vez
- De forma manual, através do menu **Definições**, no caso do Windows 10 

Pode configurar dispositivos registados no Azure AD para Windows 10, iOS, Android e macOS.

## <a name="azure-ad-joined-devices"></a>Dispositivos associados ao Azure AD

O objetivo dos dispositivos associados ao Azure AD é simplificar:

- As implementações de dispositivos detidos pela organização no Windows 
- O acesso a aplicações e recursos da organização a partir de qualquer dispositivo Windows
- A gestão baseada na cloud de dispositivos detidos pela organização

![Dispositivos registados no Azure AD](./media/overview/02.png)

A Associação do Azure AD pode ser implementada com um dos seguintes métodos: 
 - [Windows Autopilot](https://docs.microsoft.com/windows/deployment/windows-autopilot/windows-10-autopilot)
 - [Implementação em massa](https://docs.microsoft.com/intune/windows-bulk-enroll)
 - [Experiência personalizada](azuread-joined-devices-frx.md) 

A **Associação do Azure AD** destina-se a organizações que querem ser “cloud-first” (ou seja, que utilizam principalmente serviços cloud com o intuito de reduzir a utilização de infraestruturas no local) ou “cloud-only” (sem infraestruturas no local). Não há restrições quanto ao tamanho ou ao tipo de organizações que podem implementar a Associação do Azure AD. A Associação do Azure AD funciona bem mesmo em ambientes híbridos, permitindo acesso a aplicações e recursos baseados, quer na cloud, quer no local.

A implementação de dispositivos associados ao Azure AD dá-lhe as seguintes vantagens:

- **Início de Sessão Único (SSO)** nos seus serviços e aplicações SaaS geridos pelo Azure. Os utilizadores não veem avisos de autenticação adicionais quando acedem aos recursos do trabalho. A funcionalidade SSO está disponível, mesmo quando os utilizadores não estiverem ligados à rede de domínio.

- **Roaming em conformidade com a empresa** das definições de utilizador em todos os dispositivos associados. Os utilizadores não têm de ligar uma conta Microsoft (por exemplo, do Hotmail) para verem definições em vários dispositivos.

- **Acesso à Windows Store para Empresas** com uma conta do Azure AD. Os seus utilizadores podem escolher de entre um inventário de aplicações pré-selecionadas pela organização.

- Suporte para **Windows Hello** para acesso seguro e prático aos recursos do trabalho.

- **Restrição de acesso** a aplicações apenas em dispositivos que cumprem a política de conformidade.

- **Acesso ininterrupto a recursos no local** quando o dispositivo tem linha de visão para o controlador de domínio no local. 


Embora a associação do Azure AD se destine principalmente a organizações que não têm uma infraestrutura do Windows Server Active Directory no local, pode sem dúvida utilizá-la em cenários nos quais:

- Quer transitar para uma infraestrutura baseada na cloud mediante a utilização do Azure AD e da MDM, como o Intune.

- Se, por exemplo, tiver de controlar dispositivos móveis, como tablets e telemóveis, não pode utilizar uma associação a um domínio no local.

- Os utilizadores precisam de aceder essencialmente a aplicações do Office 365 ou a outras aplicações SaaS integradas no Azure AD.

- Quer gerir um grupo de utilizadores no Azure AD em vez de no Active Directory. Este cenário pode aplicar-se, por exemplo, a colaboradores sazonais, fornecedores ou estudantes.

- Quer disponibilizar capacidades de associação aos colaboradores em sucursais remotas com uma infraestrutura no local limitada.

Pode configurar dispositivos associados ao Azure AD para dispositivos Windows 10.


## <a name="hybrid-azure-ad-joined-devices"></a>Dispositivos híbridos associados ao Azure AD

Muitas organizações utilizam há mais de uma década a associação a um domínio no Active Directory no local para permitir:

- Aos departamentos de TI gerirem dispositivos detidos pela organização num local central.

- Aos utilizadores iniciarem sessão nos dispositivos deles com as contas escolares ou profissionais do Active Directory. 

Geralmente, as organizações que têm uma infraestrutura no local utilizam métodos de imagens para aprovisionar os dispositivos e, muitas vezes utilizam o **System Center Configuration Manager (SCCM)** ou a **política de grupo (GP)** para os gerir.

Se o seu ambiente tiver uma infraestrutura do AD no local e também quiser tirar partido das capacidades que o Azure Active Directory oferece, pode implementar dispositivos híbridos associados ao Azure AD. Estes são dispositivos associados ao Active Directory no local e são registados no Azure Active Directory.

![Dispositivos registados no Azure AD](./media/overview/01.png)


Deve utilizar dispositivos híbridos associados ao Azure AD se:

- Tiver aplicações Win32 implementadas nesses dispositivos que se baseiam na autenticação de máquinas do Active Directory.

- Precisa de GP para gerir os dispositivos.

- Quer continuar a utilizar soluções de imagens para configurar os dispositivos para os seus colaboradores.

Pode configurar os dispositivos híbridos associados ao Azure AD para dispositivos Windows 10 e outros dispositivos inferiores, como o Windows 8 e o Windows 7.

## <a name="summary"></a>Resumo

Com a gestão de dispositivos no Azure AD, pode: 

- Simplificar o processo de ter os dispositivos sob controlo do Azure AD

- Proporcionar aos utilizadores um acesso fácil de utilizar aos recursos baseados na cloud da sua organização

Como regra geral, deve utilizar:

- Dispositivos registados no Azure AD:

    - Para dispositivos pessoais 

    - Para registar dispositivos no Azure AD manualmente

- Dispositivos associados ao Azure AD: 

    - Para dispositivos que sejam detidos pela sua organização

    - Para dispositivos que **não** estão associados a um AD no local

    - Para registar dispositivos no Azure AD manualmente

    - Para alterar o estado local de um dispositivo

- Dispositivos híbridos associados ao Azure AD para dispositivos que estejam associados a um AD no local     

    - Para dispositivos que sejam detidos pela sua organização

    - Para dispositivos que estão associados a um AD no local

    - Para registar dispositivos no Azure AD automaticamente

    - Para alterar o estado local de um dispositivo



## <a name="next-steps"></a>Passos Seguintes

- Para obter uma descrição geral sobre como gerir dispositivos no portal do Azure, veja [Managing devices using the Azure portal](device-management-azure-portal.md) (Gerir dispositivos no portal do Azure)

- Para saber mais sobre o acesso condicional baseado em dispositivos, veja [Configure Azure Active Directory device-based conditional access policies](../conditional-access/require-managed-devices.md) (Configurar políticas de acesso condicional baseadas no dispositivo do Azure Active Directory).

- Para configurar:
    - Dispositivos Windows 10 registados no Azure Active Directory, veja [How to configure Azure Active Directory registered Windows 10 devices](../user-help/device-management-azuread-registered-devices-windows10-setup.md) (Como configurar dispositivos Windows 10 registados no Azure Active Directory)
    - Dispositivos associados Azure Active Directory, veja [How to configure Azure Active Directory joined devices](../user-help/device-management-azuread-joined-devices-setup.md) (Como configurar dispositivos associados ao Azure Active Directory)
    - Dispositivos híbridos associados ao Azure AD, veja [How to plan your hybrid Azure Active Directory join implementation](hybrid-azuread-join-plan.md) (Como planear a implementação da associação híbrida o Azure Active Directory).


