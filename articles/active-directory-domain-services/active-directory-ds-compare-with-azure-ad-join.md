---
title: Comparar a associação do Azure AD e o Azure Active Directory Domain Services | Documentos da Microsoft
description: Decidir entre a associação do Azure AD e serviços de domínio do Azure AD
services: active-directory-ds
documentationcenter: ''
author: MikeStephens-MS
manager: daveba
editor: curtand
ms.assetid: 31a71d36-58c1-4839-b958-80da0c6a77eb
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: mstephen
ms.openlocfilehash: eaa8cb54a46b1ff3c2c0f7c40c824f6ddcca16b9
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2019
ms.locfileid: "66234968"
---
# <a name="choose-between-azure-active-directory-join-and-azure-active-directory-domain-services"></a>Escolha entre a associação do Azure Active Directory e Azure Active Directory Domain Services
Este artigo descreve as diferenças entre a associação do Azure Active Directory (AD) e o Azure AD Domain Services e o ajuda a que escolher, com base nos seus casos de uso.

## <a name="azure-ad-registered-and-azure-ad-joined-devices"></a>O Azure AD registado e dispositivos associados ao Azure AD
O Azure AD permite-lhe gerir a identidade dos dispositivos utilizados pela sua organização e controlar a que o acesso aos recursos empresariais a partir destes dispositivos. Os utilizadores podem registar os respetivos dispositivos pessoais (bring your own) com o Azure AD, que fornece o dispositivo com uma identidade. Posteriormente, o Azure AD pode autenticar o dispositivo quando um utilizador inicia sessão com o Azure AD e utiliza o dispositivo para aceder a recursos protegidos. Além disso, pode gerir o dispositivo através do software de gestão de dispositivos móveis (MDM), como o Microsoft Intune. Esta funcionalidade permite-lhe restringir o acesso a recursos confidenciais a partir de dispositivos geridos e em conformidade com a política.

Também pode associar dispositivos ao Azure AD pertencentes à empresa de organização. Esse mecanismo oferece os mesmos benefícios de registro de um dispositivo pessoal com o Azure AD. Além disso, os utilizadores podem iniciar sessão dispositivo com as credenciais da empresa. Dispositivos do Azure AD associado ao dar-lhe as seguintes vantagens:
* Início de sessão único (SSO) para aplicativos a serem protegidos pelo Azure AD
* Em conformidade com a política de roaming empresarial as definições de utilizador em todos os dispositivos.
* Acesso para a Windows Store para empresas com as suas credenciais empresariais.
* Windows Hello para Empresas
* Acesso restrito a aplicações e recursos de dispositivos em conformidade com a política empresarial.

| **Tipo de dispositivo** | **Plataformas de dispositivos** | **Mecanismo** |
|:---| --- | --- |
| Dispositivos pessoais | Windows 10, iOS, Android, Mac OS | Azure AD registado |
| Organização pertencentes à empresa de dispositivos não associados ao AD no local | Windows 10 | Associados ao Azure AD |
| Organização associado a um local de dispositivos pertencentes à empresa AD | Windows 10 | Azure AD híbrido |

Num Azure AD associado ou dispositivo registado, autenticação de utilizador acontece através de protocolos OAuth/OpenID Connect com base modernos. Esses protocolos foram concebidos para funcionar através da internet e são ideais para cenários móveis em que os utilizadores acederem a recursos empresariais em qualquer lugar.


## <a name="domain-join-to-azure-ad-domain-services-managed-domains"></a>Associação a um domínio para domínios geridos do Azure AD Domain Services
O Azure AD Domain Services fornece um domínio gerido do AD numa rede virtual do Azure. Pode associar máquinas a este domínio gerido com mecanismos de associação a domínio tradicional. Cliente Windows (Windows 7, Windows 10) e máquinas do Windows Server podem ser associadas ao domínio gerido. Além disso, também pode associar máquinas do Linux e Mac OS com o domínio gerido. Ao aderir a uma máquina a um domínio do AD, os utilizadores podem iniciar sessão na máquina através das respetivas credenciais empresariais. Estas máquinas podem ser geridas utilizando a política de grupo, portanto, impondo a conformidade com as políticas de segurança da sua organização.

Num computador associado a um domínio, autenticação de utilizador ocorre através de protocolos de autenticação NTLM ou Kerberos. O computador associado a um domínio precisa linha Visual para os controladores de domínio do domínio gerido por ordem para a autenticação de utilizador trabalhar. Por conseguinte, o domínio associado a um computador tem de estar na mesma rede virtual como o domínio gerido. Em alternativa, o domínio associado a um máquina tem de estar ligados ao domínio gerido através de uma rede virtual em modo de peering ou através de uma ligação de VPN/ExpressRoute site a site. Portanto, esse mecanismo não é uma excelente escolha para dispositivos móveis ou ligar a recursos a partir de fora da rede empresarial.

> [!NOTE]
> Tecnicamente, é possível associar uma estação de trabalho do cliente no local para o domínio gerido através de uma ligação de site-site VPN ou ExpressRoute. No entanto, para dispositivos de utilizador final, recomendamos vivamente que utilize ou registar o dispositivo com o Azure AD (dispositivos pessoais) ou associar o dispositivo ao Azure AD (dispositivos da empresa). Esse mecanismo funciona melhor através da internet e permite aos utilizadores finais trabalhem em qualquer lugar. O Azure AD Domain Services é ótimo para Windows ou Linux Server máquinas de virtuais implementadas nas redes virtuais do Azure, no qual as aplicações forem implementadas.


## <a name="summary---key-differences"></a>Resumo - diferenças principais
| **Aspect** | **Associação ao Azure AD** | **Azure AD Domain Services** |
|:---| --- | --- |
| Dispositivo controlado por | Azure AD | Domínio gerido de serviços de domínio do AD do Azure |
| Representação no diretório | Objetos de dispositivo no diretório do Azure AD. | Objetos de computador no domínio gerido DS do AAD. |
| Autenticação | Protocolos OAuth/OpenID Connect com base em | Kerberos, protocolos NTLM |
| Gestão | Software de gestão de dispositivos móveis (MDM), como o Intune | Política de grupo |
| Redes | Funciona através da internet | Requer máquinas na mesma rede virtual que o domínio gerido.|
| Ótimo para... | Dispositivos móveis ou ambiente de trabalho do utilizador final | Máquinas de virtuais de servidor implementadas no Azure |


## <a name="next-steps"></a>Passos Seguintes
### <a name="learn-more-about-azure-ad-domain-services"></a>Saiba mais sobre o Azure AD Domain Services
* [Descrição geral dos serviços de domínio do Azure AD](overview.md)
* [Funcionalidades](active-directory-ds-features.md)
* [Cenários de implementação](scenarios.md)
* [Descubra se o Azure AD Domain Services se adequa aos seus casos de uso](comparison.md)
* [Compreender como o Azure AD Domain Services sincroniza com o seu diretório do Azure AD](synchronization.md)

### <a name="learn-more-about-azure-ad-join"></a>Saiba mais sobre a associação do Azure AD
* [Introduction to device management in Azure Active Directory](../active-directory/device-management-introduction.md) (Introdução à gestão de dispositivos no Azure Active Directory)

### <a name="get-started-with-azure-ad-domain-services"></a>Introdução ao Azure AD Domain Services
* [Ativar os serviços de domínio do Azure AD no portal do Azure](create-instance.md)
