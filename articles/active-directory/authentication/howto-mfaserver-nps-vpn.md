---
title: Cenários avançados com o servidor MFA do Azure e VPNs de terceiros-Azure Active Directory
description: Guias de configuração passo a passo para o servidor Azure MFA integrar com Cisco, Citrix e Juniper.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 08/05/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: f99df5daf559ef0d08b18104331c688503719e9b
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/06/2019
ms.locfileid: "68811791"
---
# <a name="advanced-scenarios-with-azure-mfa-server-and-third-party-vpn-solutions"></a>Cenários avançados com o servidor MFA do Azure e soluções de VPN de terceiros

O servidor de autenticação multifator do Azure (servidor Azure MFA) pode ser usado para se conectar diretamente com várias soluções VPN de terceiros. Este artigo enfoca o dispositivo VPN Cisco® ASA, o dispositivo VPN Citrix Netscaler SSL e o dispositivo de VPN Juniper Networks Secure Access/Pulse Secure Connect Secure SSL. Criamos guias de configuração para abordar esses três dispositivos comuns. O servidor MFA do Azure também pode ser integrado à maioria dos outros sistemas que usam RADIUS, LDAP, IIS ou autenticação baseada em declarações para AD FS. Você pode encontrar mais detalhes nas [configurações do servidor do Azure MFA](howto-mfaserver-deploy.md#next-steps).

> [!IMPORTANT]
> A partir de 1º de julho de 2019, a Microsoft não oferecerá mais o servidor MFA para novas implantações. Novos clientes que queiram exigir a autenticação multifator de seus usuários devem usar a autenticação multifator do Azure baseada em nuvem. Os clientes existentes que ativaram o servidor MFA antes de 1º de julho poderão baixar a versão mais recente, futuras atualizações e gerar credenciais de ativação como de costume.

## <a name="cisco-asa-vpn-appliance-and-azure-mfa-server"></a>Dispositivo VPN Cisco ASA e servidor Azure MFA
O servidor MFA do Azure integra-se ao dispositivo VPN Cisco® ASA para fornecer segurança adicional para os logons VPN do Cisco AnyConnect® e acesso ao Portal.  Você pode usar o protocolo LDAP ou RADIUS.  Selecione uma das opções a seguir para baixar os guias de configuração passo a passo detalhados.

| Guia de configuração | Descrição |
| --- | --- |
| [Cisco ASA com AnyConnect VPN e a configuração do Azure MFA para LDAP](https://download.microsoft.com/download/A/2/0/A201567C-C3DE-4227-AF89-4567A470899E/Cisco_ASA_Azure_MFA_LDAP.docx) | Integrar seu dispositivo VPN Cisco ASA ao Azure MFA usando LDAP |
| [Cisco ASA com AnyConnect VPN e configuração do Azure MFA para RADIUS](https://download.microsoft.com/download/4/5/7/4579C1CF-35B0-4FBE-8A1A-B49CB2CC0382/Cisco_ASA_Azure_MFA_RADIUS.docx) | Integrar seu dispositivo VPN Cisco ASA ao Azure MFA usando RADIUS |

## <a name="citrix-netscaler-ssl-vpn-and-azure-mfa-server"></a>VPN e servidor MFA do Citrix Netscaler SSL
O servidor MFA do Azure integra-se ao seu dispositivo VPN Citrix Netscaler SSL para fornecer segurança adicional para logons de VPN e acesso ao portal Citrix Netscaler SSL.  Você pode usar o protocolo LDAP ou RADIUS.  Selecione uma das opções a seguir para baixar os guias de configuração passo a passo detalhados.

| Guia de configuração | Descrição |
| --- | --- |
| [Configuração de VPN e do Azure MFA SSL para LDAP](https://download.microsoft.com/download/2/4/E/24E1E722-72DF-471F-A88A-D1338DB1AF83/Citrix_NS_Azure_MFA_LDAP.docx) | Integrar sua VPN Citrix Netscaler SSL ao dispositivo Azure MFA usando LDAP |
| [Configuração de VPN e do Azure MFA da Citrix Netscaler SSL para RADIUS](https://download.microsoft.com/download/1/A/4/1A482764-4A63-45C2-A5EC-2B673ACCDD12/Citrix_NS_Azure_MFA_RADIUS.docx) | Integrar seu dispositivo VPN Citrix Netscaler SSL com o Azure MFA usando RADIUS |

## <a name="juniperpulse-secure-ssl-vpn-appliance-and-azure-mfa-server"></a>Dispositivo de VPN Juniper/Pulse Secure SSL e servidor Azure MFA
O servidor MFA do Azure integra-se ao seu dispositivo de VPN Juniper/Pulse Secure SSL para fornecer segurança adicional para logons de VPN Juniper/Pulse Secure SSL e acesso ao Portal.  Você pode usar o protocolo LDAP ou RADIUS.  Selecione uma das opções a seguir para baixar os guias de configuração passo a passo detalhados.

| Guia de configuração | Descrição |
| --- | --- |
| [Configuração de VPN do Juniper/Pulse Secure SSL e do Azure MFA para LDAP](https://download.microsoft.com/download/6/5/8/6587B418-75B1-4FCB-84D4-984BC479309E/JuniperPulse_Azure_MFA_LDAP.docx) | Integrar sua VPN Juniper/Pulse SSL segura com o dispositivo MFA do Azure usando LDAP |
| [Configuração de VPN do Juniper/Pulse Secure SSL e do Azure MFA para RADIUS](https://download.microsoft.com/download/7/9/A/79AB3DAD-4799-4379-B1DA-B95ABDF231DC/JuniperPulse_Azure_MFA_RADIUS.docx) | Integrar seu dispositivo VPN Juniper/Pulse Secure SSL com o Azure MFA usando RADIUS |

## <a name="next-steps"></a>Passos seguintes

- [Aumente sua infraestrutura de autenticação existente com a extensão NPS para a autenticação multifator do Azure](howto-mfa-nps-extension.md)

- [Configurar Definições do Multi-Factor Authentication do Azure](howto-mfa-mfasettings.md)
