---
title: Cenários avançados com o MFA do Azure e VPNs de terceiros - Azure Active Directory
description: Guias de configuração passo a passo para a MFA do Azure integrar com o Cisco, Citrix e Juniper.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: acbf27ca6f5b58d5c3cebb28698304c130381a7a
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/21/2019
ms.locfileid: "58314404"
---
# <a name="advanced-scenarios-with-azure-multi-factor-authentication-and-third-party-vpn-solutions"></a>Cenários avançados com multi-factor Authentication e soluções VPN de terceiros

Azure multi-factor Authentication pode ser utilizado para ligar de forma totalmente integrada com várias soluções VPN de terceiros. Este artigo se concentra na aplicação de VPN do Cisco® ASA, a aplicação Citrix NetScaler SSL VPN e Juniper redes proteger o acesso/Pulse Secure ligar seguro SSL aplicação VPN. Criamos guias de configuração para resolver estas três aplicações comuns. Também pode integrar o servidor multi-factor Authentication com mais outros sistemas que usam o RADIUS, LDAP, IIS ou autenticação baseada em afirmações do AD FS. Pode encontrar mais detalhes [as configurações de servidor de MFA](howto-mfaserver-deploy.md#next-steps).

## <a name="cisco-asa-vpn-appliance-and-azure-multi-factor-authentication"></a>Aplicação de VPN de ASA da Cisco e multi-factor Authentication do Azure
Autenticação Multifator do Azure integra-se a sua aplicação de VPN do Cisco® ASA para fornecer segurança adicional para inícios de sessão de VPN do Cisco AnyConnect® e o acesso ao portal.  Pode utilizar o protocolo de LDAP ou RADIUS.  Selecione um dos seguintes procedimentos para transferir os guias de configuração passo a passo detalhada.

| Guia de configuração | Descrição |
| --- | --- |
| [Cisco ASA com Anyconnect VPN e o Azure MFA de configuração para o LDAP](https://download.microsoft.com/download/A/2/0/A201567C-C3DE-4227-AF89-4567A470899E/Cisco_ASA_Azure_MFA_LDAP.docx) | Integrar a sua aplicação VPN do Cisco ASA com a MFA do Azure através de LDAP |
| [Cisco ASA com a configuração de MFA Anyconnect VPN e o Azure para RADIUS](https://download.microsoft.com/download/4/5/7/4579C1CF-35B0-4FBE-8A1A-B49CB2CC0382/Cisco_ASA_Azure_MFA_RADIUS.docx) | Integrar a sua aplicação VPN do Cisco ASA com a MFA do Azure com o RADIUS |

## <a name="citrix-netscaler-ssl-vpn-and-azure-multi-factor-authentication"></a>VPN do Citrix NetScaler SSL e o Azure multi-factor Authentication
Autenticação Multifator do Azure integra-se a sua aplicação de VPN do Citrix NetScaler SSL para fornecer segurança adicional para inícios de sessão do Citrix NetScaler SSL VPN e acesso ao portal.  Pode utilizar o protocolo de LDAP ou RADIUS.  Selecione um dos seguintes procedimentos para transferir os guias de configuração passo a passo detalhada.

| Guia de configuração | Descrição |
| --- | --- |
| [Configuração de MFA de VPN do Citrix NetScaler SSL e o Azure para o LDAP](https://download.microsoft.com/download/2/4/E/24E1E722-72DF-471F-A88A-D1338DB1AF83/Citrix_NS_Azure_MFA_LDAP.docx) | Integrar o Citrix NetScaler SSL VPN da aplicação de MFA do Azure através de LDAP |
| [Configuração de MFA de VPN do Citrix NetScaler SSL e o Azure para RADIUS](https://download.microsoft.com/download/1/A/4/1A482764-4A63-45C2-A5EC-2B673ACCDD12/Citrix_NS_Azure_MFA_RADIUS.docx) | Integrar a sua aplicação Citrix NetScaler SSL VPN com a MFA do Azure com o RADIUS |

## <a name="juniperpulse-secure-ssl-vpn-appliance-and-azure-multi-factor-authentication"></a>Aplicação Juniper/Pulse Secure SSL VPN e o Azure multi-factor Authentication
Autenticação Multifator do Azure integra-se a sua aplicação de Juniper/Pulse Secure SSL VPN para fornecer segurança adicional para inícios de sessão de Juniper/Pulse Secure SSL VPN e acesso ao portal.  Pode utilizar o protocolo de LDAP ou RADIUS.  Selecione um dos seguintes procedimentos para transferir os guias de configuração passo a passo detalhada.

| Guia de configuração | Descrição |
| --- | --- |
| [Configuração de MFA Juniper/Pulse Secure SSL VPN e o Azure para o LDAP](https://download.microsoft.com/download/6/5/8/6587B418-75B1-4FCB-84D4-984BC479309E/JuniperPulse_Azure_MFA_LDAP.docx) | Integrar seu Juniper/Pulse Secure SSL VPN da aplicação de MFA do Azure através de LDAP |
| [Configuração de MFA Juniper/Pulse Secure SSL VPN e o Azure para RADIUS](https://download.microsoft.com/download/7/9/A/79AB3DAD-4799-4379-B1DA-B95ABDF231DC/JuniperPulse_Azure_MFA_RADIUS.docx) | Integrar a sua aplicação Juniper/Pulse Secure SSL VPN com a MFA do Azure com o RADIUS |

## <a name="next-steps"></a>Passos Seguintes

- [Aumentar a sua infraestrutura de autenticação existente com a extensão NPS para multi-factor Authentication do Azure](howto-mfa-nps-extension.md)

- [Configurar Definições do Multi-Factor Authentication do Azure](howto-mfa-mfasettings.md)
