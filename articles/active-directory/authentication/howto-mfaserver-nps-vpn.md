---
title: Servidor Azure MFA e VPNs de terceiros - Diretório Ativo Azure
description: Guias de configuração passo a passo para o Azure MFA Server integrar-se com Cisco, Citrix e Juniper.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1ba0d359079a8999b3c4f2a41f4beadb835ccacc
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/18/2020
ms.locfileid: "94838234"
---
# <a name="advanced-scenarios-with-azure-mfa-server-and-third-party-vpn-solutions"></a>Cenários avançados com O Azure MFA Server e soluções VPN de terceiros

O Azure Multi-Factor Authentication Server (Azure MFA Server) pode ser utilizado para se conectar perfeitamente com várias soluções VPN de terceiros. Este artigo centra-se no aparelho Cisco &reg; ASA VPN, no aparelho Citrix NetScaler SSL VPN e no aparelho VPN Secure Access/Pulse Secure Connect Secure SSL. Criámos guias de configuração para abordar estes três aparelhos comuns. O Azure MFA Server também pode integrar-se com a maioria dos outros sistemas que utilizam RADIUS, LDAP, IIS ou autenticação baseada em sinistros para AD FS. Pode encontrar mais detalhes nas [configurações do Azure MFA Server](howto-mfaserver-deploy.md#next-steps).

> [!IMPORTANT]
> A partir de 1 de julho de 2019, a Microsoft já não oferece O MFA Server para novas implementações. Os novos clientes que pretendam exigir a autenticação de vários fatores (MFA) durante os eventos de entrada devem utilizar a autenticação multi-factor Azure AD baseada na nuvem.
>
> Para começar com mFA baseado na nuvem, consulte [Tutorial: Secure user in events with Azure AD Multi-Factor Authentication](tutorial-enable-azure-mfa.md).
>
> Se utilizar MFA baseado na nuvem, consulte [Integrar a sua infraestrutura VPN com Azure MFA](howto-mfa-nps-extension-vpn.md).
>
> Os clientes existentes que ativaram o MFA Server antes de 1 de julho de 2019 podem descarregar a versão mais recente, futuras atualizações e gerar credenciais de ativação como de costume.

## <a name="cisco-asa-vpn-appliance-and-azure-mfa-server"></a>Cisco ASA VPN eletrodoméstico e Azure MFA Server
O Azure MFA Server integra-se com o seu &reg; aparelho Cisco ASA VPN para fornecer segurança adicional para &reg; os logins VPN cisco AnyConnect e acesso ao portal.  Pode utilizar o protocolo LDAP ou RADIUS.  Selecione um dos seguintes para descarregar os guias de configuração passo a passo detalhados.

| Guia de Configuração | Descrição |
| --- | --- |
| [Cisco ASA com Configuração MFA de Anyconnect e Azure para LDAP](https://download.microsoft.com/download/A/2/0/A201567C-C3DE-4227-AF89-4567A470899E/Cisco_ASA_Azure_MFA_LDAP.docx) | Integre o seu aparelho Cisco ASA VPN com Azure MFA utilizando lDAP |
| [Cisco ASA com Configuração MFA de Anyconnect e Azure para RADIUS](https://download.microsoft.com/download/4/5/7/4579C1CF-35B0-4FBE-8A1A-B49CB2CC0382/Cisco_ASA_Azure_MFA_RADIUS.docx) | Integre o seu aparelho Cisco ASA VPN com Azure MFA utilizando o RADIUS |

## <a name="citrix-netscaler-ssl-vpn-and-azure-mfa-server"></a>Citrix NetScaler SSL VPN e Azure MFA Server
O Azure MFA Server integra-se com o seu aparelho Citrix NetScaler SSL VPN para fornecer segurança adicional para logins Citrix NetScaler SSL VPN e acesso ao portal.  Pode utilizar o protocolo LDAP ou RADIUS.  Selecione um dos seguintes para descarregar os guias de configuração passo a passo detalhados.

| Guia de Configuração | Descrição |
| --- | --- |
| [Citrix NetScaler SSL VPN e Azure MFA Configuração para LDAP](https://download.microsoft.com/download/2/4/E/24E1E722-72DF-471F-A88A-D1338DB1AF83/Citrix_NS_Azure_MFA_LDAP.docx) | Integre o seu Citrix NetScaler SSL VPN com o aparelho Azure MFA utilizando lDAP |
| [Citrix NetScaler SSL VPN e Azure MFA Configuração para RADIUS](https://download.microsoft.com/download/1/A/4/1A482764-4A63-45C2-A5EC-2B673ACCDD12/Citrix_NS_Azure_MFA_RADIUS.docx) | Integre o seu aparelho Citrix NetScaler SSL VPN com Azure MFA utilizando RADIUS |

## <a name="juniperpulse-secure-ssl-vpn-appliance-and-azure-mfa-server"></a>O aparelho VPN Juniper/Pulse Secure SSL e o servidor Azure MFA
O Azure MFA Server integra-se com o seu aparelho VPN Juniper/Pulse Secure SSL para fornecer segurança adicional para logins VPN Juniper/Pulse Secure SSL e acesso ao portal.  Pode utilizar o protocolo LDAP ou RADIUS.  Selecione um dos seguintes para descarregar os guias de configuração passo a passo detalhados.

| Guia de Configuração | Descrição |
| --- | --- |
| [Juniper/Pulse Secure SSL VPN e Azure MFA Configuração para LDAP](https://download.microsoft.com/download/6/5/8/6587B418-75B1-4FCB-84D4-984BC479309E/JuniperPulse_Azure_MFA_LDAP.docx) | Integre o seu Zimbro/Pulse Secure SSL VPN com o aparelho Azure MFA utilizando lDAP |
| [Juniper/Pulse Secure SSL VPN e Azure MFA Configuração para RADIUS](https://download.microsoft.com/download/7/9/A/79AB3DAD-4799-4379-B1DA-B95ABDF231DC/JuniperPulse_Azure_MFA_RADIUS.docx) | Integre o seu aparelho VPN Zimbro/Pulse Secure SSL com Azure MFA utilizando o RADIUS |

## <a name="next-steps"></a>Passos seguintes

- [Aumente a sua infraestrutura de autenticação existente com a extensão NPS para autenticação multi-factor Azure](howto-mfa-nps-extension.md)

- [Configurar Definições do Multi-Factor Authentication do Azure](howto-mfa-mfasettings.md)
