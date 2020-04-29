---
title: Servidor Azure MFA e VPNs de terceiros - Diretório Ativo Azure
description: Guias de configuração passo a passo para o Azure MFA Server para integrar com cisco, Citrix e Juniper.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 01decb99a9eb24ae60250f83f1f961b4c1690bc0
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80652853"
---
# <a name="advanced-scenarios-with-azure-mfa-server-and-third-party-vpn-solutions"></a>Cenários avançados com servidor Azure MFA e soluções VPN de terceiros

O Servidor de Autenticação De Múltiplos Fatores Azure (Azure MFA Server) pode ser utilizado para se conectar sem problemas com várias soluções VPN de terceiros. Este artigo centra-se no aparelho Cisco&reg; ASA VPN VPN, no aparelho Citrix NetScaler SSL VPN e no eletrodoméstico Secure Access/Pulse Secure Connect Secure SSL VPN. Criámos guias de configuração para abordar estes três aparelhos comuns. O Azure MFA Server também pode integrar-se com a maioria dos outros sistemas que utilizam RADIUS, LDAP, IIS ou autenticação baseada em sinistros para AD FS. Pode encontrar mais detalhes nas configurações do [Servidor Azure MFA](howto-mfaserver-deploy.md#next-steps).

> [!IMPORTANT]
> A partir de 1 de julho de 2019, a Microsoft deixará de oferecer o MFA Server para novas implementações. Os novos clientes que pretendam exigir a autenticação de vários fatores dos seus utilizadores devem utilizar a autenticação multi-factor Azure baseada na nuvem. Os clientes existentes que ativaram o MFA Server antes do dia 1 de julho poderão descarregar a versão mais recente, futuras atualizações e gerar credenciais de ativação como de costume.

## <a name="cisco-asa-vpn-appliance-and-azure-mfa-server"></a>Aparelho Cisco ASA VPN e Servidor Azure MFA
O Azure MFA Server&reg; integra-se com o seu aparelho Cisco&reg; ASA VPN para fornecer segurança adicional para logins VPN cisco AnyConnect e acesso ao portal.  Pode utilizar o protocolo LDAP ou RADIUS.  Selecione um dos seguintes para descarregar os guias de configuração passo a passo detalhados.

| Guia de configuração | Descrição |
| --- | --- |
| [Cisco ASA com Configuração VpN e MFA Azure para LDAP](https://download.microsoft.com/download/A/2/0/A201567C-C3DE-4227-AF89-4567A470899E/Cisco_ASA_Azure_MFA_LDAP.docx) | Integre o seu aparelho Cisco ASA VPN com O MFA Azure utilizando o LDAP |
| [Cisco ASA com Configuração VpN e MFA Azure para RADIUS](https://download.microsoft.com/download/4/5/7/4579C1CF-35B0-4FBE-8A1A-B49CB2CC0382/Cisco_ASA_Azure_MFA_RADIUS.docx) | Integre o seu aparelho Cisco ASA VPN com O MFA Azure utilizando o RADIUS |

## <a name="citrix-netscaler-ssl-vpn-and-azure-mfa-server"></a>Citrix NetScaler SSL VPN e Servidor Azure MFA
O Azure MFA Server integra-se com o seu aparelho Citrix NetScaler SSL VPN para fornecer segurança adicional para logins VpN Citrix NetScaler SSL e acesso ao portal.  Pode utilizar o protocolo LDAP ou RADIUS.  Selecione um dos seguintes para descarregar os guias de configuração passo a passo detalhados.

| Guia de configuração | Descrição |
| --- | --- |
| [Citrix NetScaler SSL VPN e Configuração Azure MFA para LDAP](https://download.microsoft.com/download/2/4/E/24E1E722-72DF-471F-A88A-D1338DB1AF83/Citrix_NS_Azure_MFA_LDAP.docx) | Integre o seu Citrix NetScaler SSL VPN com o aparelho Azure MFA utilizando o LDAP |
| [Citrix NetScaler SSL VPN e Configuração Azure MFA para RADIUS](https://download.microsoft.com/download/1/A/4/1A482764-4A63-45C2-A5EC-2B673ACCDD12/Citrix_NS_Azure_MFA_RADIUS.docx) | Integre o seu aparelho Citrix NetScaler SSL VPN com O MFA Azure utilizando o RADIUS |

## <a name="juniperpulse-secure-ssl-vpn-appliance-and-azure-mfa-server"></a>Electroper/Pulse Secure SSL VPN e Servidor Azure MFA
O Azure MFA Server integra-se com o seu aparelho VPN SSL Seguro Juniper/Pulse para fornecer segurança adicional para logins VPN SSL SSL E acesso ao portal Juniper/Pulse Secure.  Pode utilizar o protocolo LDAP ou RADIUS.  Selecione um dos seguintes para descarregar os guias de configuração passo a passo detalhados.

| Guia de configuração | Descrição |
| --- | --- |
| [Configuração Juniper/Pulse Secure SSL VPN e Azure MFA para LDAP](https://download.microsoft.com/download/6/5/8/6587B418-75B1-4FCB-84D4-984BC479309E/JuniperPulse_Azure_MFA_LDAP.docx) | Integre o seu Juniper/Pulse Secure SSL VPN com o aparelho Azure MFA utilizando o LDAP |
| [Configuração Juniper/Pulse Secure SSL VPN e Azure MFA para RADIUS](https://download.microsoft.com/download/7/9/A/79AB3DAD-4799-4379-B1DA-B95ABDF231DC/JuniperPulse_Azure_MFA_RADIUS.docx) | Integre o seu aparelho VpN SSL Seguro Juniper/Pulse com MFA Azure utilizando o RADIUS |

## <a name="next-steps"></a>Passos seguintes

- [Aumente a sua infraestrutura de autenticação existente com a extensão NPS para autenticação multi-factor Azure](howto-mfa-nps-extension.md)

- [Configurar Definições do Multi-Factor Authentication do Azure](howto-mfa-mfasettings.md)
