---
title: Azure AD assegura acesso híbrido | Microsoft Docs
description: Este artigo descreve soluções parceiras para integrar o seu legado no local, nuvem pública ou aplicações em nuvem privada com Azure AD. Proteja as suas aplicações antigas ligando controladores ou redes de entrega de aplicações ao Azure AD.
services: active-directory
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: how-to
ms.workload: identity
ms.date: 2/16/2021
ms.author: gasinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: a793ebb6d2b58718a6ee42c69c38b9da1b124722
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "104589400"
---
# <a name="secure-hybrid-access-secure-legacy-apps-with-azure-active-directory"></a>Acesso híbrido seguro: Aplicações antigas seguras com diretório ativo Azure

Pode agora proteger as suas aplicações de autenticação de legados no local e em nuvem, ligando-as ao Azure Ative Directory (AD) com:

- [Azure AD Application Proxy](#secure-hybrid-access-sha-through-azure-ad-application-proxy)

- [Os controladores e redes de entrega de aplicações existentes](#sha-through-networking-and-delivery-controllers)

- [Aplicações de Rede Privada Virtual (VPN) e Software-Defined Perimeter (SDP)](#sha-through-vpn-and-sdp-applications)

Pode colmatar a lacuna e fortalecer a sua postura de segurança em todas as aplicações com capacidades AD AZure como [acesso condicionado](../conditional-access/overview.md) AD Azure e [Proteção](../identity-protection/overview-identity-protection.md)de Identidade AD AZure .

## <a name="secure-hybrid-access-sha-through-azure-ad-application-proxy"></a>Acesso híbrido seguro (SHA) através do Azure AD Application Proxy
  
Utilizando [o Application Proxy,](./what-is-application-proxy.md) pode fornecer [acesso remoto seguro](./application-proxy.md) às suas aplicações web no local. Os seus utilizadores não necessitam de usar uma VPN. Os utilizadores beneficiam de uma ligação fácil às suas aplicações a partir de qualquer dispositivo após uma [única sing-on](./add-application-portal-setup-sso.md). O Application Proxy fornece acesso remoto como um serviço e [permite-lhe publicar facilmente as suas aplicações no local](./application-proxy-add-on-premises-application.md) para utilizadores fora da rede corporativa. Ajuda-o a escalar a sua gestão de acesso à nuvem sem exigir que modifique as suas aplicações no local. [Planeie uma implementação de Proxy de aplicação AD Azure](./application-proxy-deployment-plan.md) como um próximo passo.

## <a name="azure-ad-partner-integrations"></a>Integrações de parceiros AZure AD

### <a name="sha-through-networking-and-delivery-controllers"></a>SHA através de controladores de rede e de entrega

Além do [Azure AD Application Proxy](./what-is-application-proxy.md), para lhe permitir utilizar o [quadro Zero Trust,](https://www.microsoft.com/security/blog/2020/04/02/announcing-microsoft-zero-trust-assessment-tool/)a Microsoft é parceira de fornecedores de terceiros. Pode utilizar os controladores de rede e de entrega existentes e proteger facilmente aplicações antigas que são fundamentais para os seus processos de negócio, mas que não conseguiu proteger antes com a Azure AD. É provável que já tenhas tudo o que precisas para começar a proteger estas aplicações.

![Imagem mostra acesso híbrido seguro com parceiros de rede e procuração de aplicativos](./media/secure-hybrid-access/secure-hybrid-access.png)

Os seguintes fornecedores de rede oferecem soluções pré-construídas e orientações detalhadas para a integração com a Azure AD.

- [Akamai Enterprise Application Access (EAA)](../saas-apps/akamai-tutorial.md)

- [Controlador de entrega de aplicações Citrix (ADC)](../saas-apps/citrix-netscaler-tutorial.md)

- [F5 Big-IP APM](./f5-aad-integration.md)

- [Rio Kemp](../saas-apps/kemp-tutorial.md)

- [Pulse Secure Virtual Traffic Manager (VTM)](../saas-apps/pulse-secure-virtual-traffic-manager-tutorial.md)

### <a name="sha-through-vpn-and-sdp-applications"></a>SHA através de aplicações VPN e SDP

Utilizando soluções VPN e SDP, pode fornecer acesso seguro à sua rede empresarial a partir de qualquer dispositivo, a qualquer momento, em qualquer local, protegendo os dados da sua organização. Ao ter a Azure AD como fornecedor de identidade (IDP), pode utilizar métodos modernos de autenticação e autorização como a autenticação [única de Azure](./what-is-single-sign-on.md) AD e [autenticação multi-factor](../authentication/concept-mfa-howitworks.md) para garantir as suas aplicações antigas no local.  

![Imagem mostra acesso híbrido seguro com parceiros VPN e procuração de aplicativos ](./media/secure-hybrid-access/app-proxy-vpn.png)

Os seguintes fornecedores VPN oferecem soluções pré-construídas e orientações detalhadas para a integração com a Azure AD.

- [Cisco AnyConnect](../saas-apps/cisco-anyconnect.md)

- [Fortinet](../saas-apps/fortigate-ssl-vpn-tutorial.md)

- [F5 Big-IP APM](./f5-aad-password-less-vpn.md)

- [Palo Alto Networks Global Protect](../saas-apps/paloaltoadmin-tutorial.md)

- [Pulse Secure Pulse Connect Secure (PCS)](../saas-apps/pulse-secure-pcs-tutorial.md)

Os seguintes fornecedores de SDP oferecem soluções pré-construídas e orientações detalhadas para a integração com a Azure AD.

- [Corretor de acesso datawiza](./add-application-portal-setup-oidc-sso.md)

- [Perimeter 81](../saas-apps/perimeter-81-tutorial.md)


- [Plataforma de Autenticação Silverfort](./add-application-portal-setup-oidc-sso.md)

- [Strata](../saas-apps/maverics-identity-orchestrator-saml-connector-tutorial.md)

- [Zscaler Private Access (ZPA)](../saas-apps/zscalerprivateaccess-tutorial.md)
