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
ms.openlocfilehash: 68a4241a70577ce359320d388882312cf06090ba
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2021
ms.locfileid: "101645430"
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

- [Akamai Enterprise Application Access (EAA)](https://docs.microsoft.com/azure/active-directory/saas-apps/akamai-tutorial)

- [Controlador de entrega de aplicações Citrix (ADC)](https://docs.microsoft.com/azure/active-directory/saas-apps/citrix-netscaler-tutorial)

- [F5 Big-IP APM](https://docs.microsoft.com/azure/active-directory/saas-apps/headerf5-tutorial)

- [Rio Kemp](https://docs.microsoft.com/azure/active-directory/saas-apps/kemp-tutorial)

- [Pulse Secure Virtual Traffic Manager (VTM)](https://docs.microsoft.com/azure/active-directory/saas-apps/pulse-secure-virtual-traffic-manager-tutorial)

### <a name="sha-through-vpn-and-sdp-applications"></a>SHA através de aplicações VPN e SDP

Utilizando soluções VPN e SDP, pode fornecer acesso seguro à sua rede empresarial a partir de qualquer dispositivo, a qualquer momento, em qualquer local, protegendo os dados da sua organização. Ao ter a Azure AD como fornecedor de identidade (IDP), pode utilizar métodos modernos de autenticação e autorização como a autenticação [única de Azure](./what-is-single-sign-on.md) AD e [autenticação multi-factor](../authentication/concept-mfa-howitworks.md) para garantir as suas aplicações antigas no local.  

![Imagem mostra acesso híbrido seguro com parceiros VPN e procuração de aplicativos ](./media/secure-hybrid-access/app-proxy-vpn.png)

Os seguintes fornecedores VPN oferecem soluções pré-construídas e orientações detalhadas para a integração com a Azure AD.

- [Cisco AnyConnect](https://docs.microsoft.com/azure/active-directory/saas-apps/cisco-anyconnect)

- [Fortinet](https://docs.microsoft.com/azure/active-directory/saas-apps/fortigate-ssl-vpn-tutorial)

- [F5 Big-IP APM](https://docs.microsoft.com/azure/active-directory/manage-apps/f5-aad-password-less-vpn)

- [Palo Alto Networks Global Protect](https://docs.microsoft.com/azure/active-directory/saas-apps/paloaltoadmin-tutorial)

- [Pulse Secure Pulse Connect Secure (PCS)](https://docs.microsoft.com/azure/active-directory/saas-apps/pulse-secure-pcs-tutorial)

Os seguintes fornecedores de SDP oferecem soluções pré-construídas e orientações detalhadas para a integração com a Azure AD.

- [Corretor de acesso datawiza](https://docs.microsoft.com/azure/active-directory/manage-apps/add-application-portal-setup-oidc-sso)

- [Perimeter 81](https://docs.microsoft.com/azure/active-directory/saas-apps/perimeter-81-tutorial#:~:text=For%20SSO%20to%20work,%20you%20need%20to%20establish,to%20test%20Azure%20AD%20single%20sign-on%20with%20B.Simon.)

- [Plataforma de Autenticação Silverfort](https://docs.microsoft.com/azure/active-directory/manage-apps/add-application-portal-setup-oidc-sso)

- [Strata](https://docs.microsoft.com/azure/active-directory/saas-apps/maverics-identity-orchestrator-saml-connector-tutorial)

- [Zscaler Private Access (ZPA)](https://docs.microsoft.com/azure/active-directory/saas-apps/zscalerprivateaccess-tutorial)
