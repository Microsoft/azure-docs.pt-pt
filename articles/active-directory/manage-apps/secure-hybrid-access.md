---
title: Azure AD garante acesso híbrido / Microsoft Docs
description: Este artigo descreve soluções parceiras para integrar o seu legado no local, nuvem pública ou aplicações em nuvem privada com Azure AD. Proteja as suas aplicações antigas ligando controladores ou redes de entrega de aplicações ao Azure AD.
services: active-directory
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: how-to
ms.workload: identity
ms.date: 9/10/2020
ms.author: gasinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4d64f7423d537958b6d3c388cb12f23bd2e30e36
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90087099"
---
# <a name="secure-hybrid-access-secure-legacy-apps-with-azure-active-directory"></a>Acesso híbrido seguro: Aplicações antigas seguras com diretório ativo Azure

Pode agora proteger as suas aplicações de autenticação de legados no local e em nuvem, ligando-as ao Azure Ative Directory (AD) com:

- [Azure AD Application Proxy](#secure-hybrid-access-sha-through-azure-ad-application-proxy)

- [Os controladores e redes de entrega de aplicações existentes](#sha-through-networking-and-delivery-controllers)

- [Aplicações de Rede Privada Virtual (VPN) e Software-Defined Perimeter (SDP)](#sha-through-vpn-and-sdp-applications)

Pode colmatar a lacuna e fortalecer a sua postura de segurança em todas as aplicações com capacidades AD AZure como [acesso condicionado](https://docs.microsoft.com/azure/active-directory/conditional-access/overview) AD Azure e [Proteção](https://docs.microsoft.com/azure/active-directory/identity-protection/overview-identity-protection)de Identidade AD AZure .

## <a name="secure-hybrid-access-sha-through-azure-ad-application-proxy"></a>Acesso híbrido seguro (SHA) através do Azure AD Application Proxy
  
Utilizando [o Application Proxy,](https://aka.ms/whyappproxy) pode fornecer [acesso remoto seguro](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy) às suas aplicações web no local. Os seus utilizadores não necessitam de usar uma VPN. Os utilizadores beneficiam de uma ligação fácil às suas aplicações a partir de qualquer dispositivo após uma [única sing-on](https://docs.microsoft.com/azure/active-directory/manage-apps/add-application-portal-setup-sso). O Application Proxy fornece acesso remoto como um serviço e [permite-lhe publicar facilmente as suas aplicações no local](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-add-on-premises-application) para utilizadores fora da rede corporativa. Ajuda-o a escalar a sua gestão de acesso à nuvem sem exigir que modifique as suas aplicações no local. [Planeie uma implementação de Proxy de aplicação AD Azure](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-deployment-plan) como um próximo passo.

## <a name="azure-ad-partner-integrations"></a>Integrações de parceiros AZure AD

### <a name="sha-through-networking-and-delivery-controllers"></a>SHA através de controladores de rede e de entrega

Além do [Azure AD Application Proxy](https://aka.ms/whyappproxy), para lhe permitir utilizar o [quadro Zero Trust,](https://www.microsoft.com/security/blog/2020/04/02/announcing-microsoft-zero-trust-assessment-tool/)a Microsoft é parceira de fornecedores de terceiros. Pode utilizar os controladores de rede e de entrega existentes e proteger facilmente aplicações antigas que são fundamentais para os seus processos de negócio, mas que não conseguiu proteger antes com a Azure AD. É provável que já tenhas tudo o que precisas para começar a proteger estas aplicações.

![Imagem mostra acesso híbrido seguro com parceiros de rede e procuração de aplicativos](./media/secure-hybrid-access/secure-hybrid-access.png)

Os seguintes fornecedores de rede oferecem soluções pré-construídas e orientações detalhadas para a integração com a Azure AD.

- [Akamai Enterprise Application Access (EAA)](https://docs.microsoft.com/azure/active-directory/saas-apps/akamai-tutorial)

- [Controlador de entrega de aplicações Citrix (ADC)](https://docs.microsoft.com/azure/active-directory/saas-apps/citrix-netscaler-tutorial)

- [F5 Big-IP APM](https://docs.microsoft.com/azure/active-directory/saas-apps/headerf5-tutorial)

- [Rio Kemp](https://docs.microsoft.com/azure/active-directory/saas-apps/kemp-tutorial)

### <a name="sha-through-vpn-and-sdp-applications"></a>SHA através de aplicações VPN e SDP

Utilizando soluções VPN e SDP, pode fornecer acesso seguro à sua rede empresarial a partir de qualquer dispositivo, a qualquer momento, em qualquer local, protegendo os dados da sua organização. Ao ter a Azure AD como fornecedor de identidade (IDP), pode utilizar métodos modernos de autenticação e autorização como a autenticação [única de Azure](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on) AD e [autenticação multi-factor](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-howitworks) para garantir as suas aplicações antigas no local.  

![Imagem mostra acesso híbrido seguro com parceiros VPN e procuração de aplicativos ](./media/secure-hybrid-access/app-proxy-vpn.png)

Os seguintes fornecedores VPN e SDP oferecem soluções pré-construídas e orientações detalhadas para a integração com a Azure AD.

• [Cisco AnyConnect](https://docs.microsoft.com/azure/active-directory/saas-apps/cisco-anyconnect)

• [Fortinet](https://docs.microsoft.com/azure/active-directory/saas-apps/fortigate-ssl-vpn-tutorial)

• [F5 Big-IP APM](https://aka.ms/f5-hybridaccessguide)

• [Palo Alto Networks Global Protect](https://docs.microsoft.com/azure/active-directory/saas-apps/paloaltoadmin-tutorial)

• [Zscaler Private Access (ZPA)](https://aka.ms/zscaler-hybridaccessguide)
