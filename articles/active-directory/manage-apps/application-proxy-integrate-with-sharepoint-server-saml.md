---
title: Publicar on=premissas SharePoint com Application Proxy - Azure AD
description: Cobre o básico sobre como integrar um servidor SharePoint no local com O Azure AD Application Proxy for SAML.
services: active-directory
documentationcenter: ''
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 10/02/2019
ms.author: kenwith
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 34aaafcd03e737b1e59529f8001e0c008bd39b70
ms.sourcegitcommit: a67b972d655a5a2d5e909faa2ea0911912f6a828
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/23/2021
ms.locfileid: "104888877"
---
# <a name="integrate-with-sharepoint-saml"></a>Integre com SharePoint (SAML)

Este guia passo a passo explica como garantir o acesso ao [Azure Ative Directory integrado no local Sharepoint (SAML)](../saas-apps/sharepoint-on-premises-tutorial.md) utilizando o Azure AD Application Proxy, onde os utilizadores da sua organização (Azure AD, B2B) se conectam ao Sharepoint através da Internet.

> [!NOTE] 
> Se é novo no Azure AD Application Proxy e quer saber mais, consulte [acesso remoto a aplicações no local através do Azure AD Application Proxy](./application-proxy.md).

Há três vantagens primárias desta configuração:

- O Azure AD Application Proxy garante que o tráfego autenticado pode chegar à sua rede interna e ao servidor Sharepoint.
- Os seus utilizadores podem aceder aos sites Sharepoint como de costume sem utilizar a VPN.
- Pode controlar o acesso através da atribuição do utilizador ao nível de Procuração de Aplicações AD Azure e pode aumentar a segurança com funcionalidades AZure AD como Acesso Condicional e Autenticação Multi-Factor (MFA).

Este processo requer duas Aplicações empresariais. Um deles é um caso sharePoint no local que publica da galeria para a sua lista de aplicações geridas saaS. A segunda é uma aplicação no local (aplicação não-galeria) que utilizará para publicar a primeira Aplicação da Galeria Enterprise.

## <a name="prerequisites"></a>Pré-requisitos

Para completar esta configuração, precisa dos seguintes recursos:
 - Uma quinta SharePoint 2013 ou mais recente. A exploração sharepoint deve ser [integrada com a Azure AD](../saas-apps/sharepoint-on-premises-tutorial.md).
 - Um inquilino da AD Azure com um plano que inclui procuração de aplicação. Saiba mais sobre [os planos e preços da AZure AD.](https://azure.microsoft.com/pricing/details/active-directory/)
 - Um [domínio personalizado e verificado](../fundamentals/add-custom-domain.md) no inquilino AZure AD. O domínio verificado deve coincidir com o sufixo URL do SharePoint.
 - É necessário um certificado SSL. Consulte os detalhes na [publicação de domínio personalizado.](./application-proxy-configure-custom-domain.md)
 - No local, os utilizadores do Ative Directory devem ser sincronizados com o Azure AD Connect e devem ser configurados para [iniciar seduca no Azure](../hybrid/plan-connect-user-signin.md). 
 - Para utilizadores convidados apenas em nuvem e B2B, você precisa [conceder acesso a uma conta de hóspedes para SharePoint no local no portal Azure](../saas-apps/sharepoint-on-premises-tutorial.md#grant-access-to-a-guest-account-to-sharepoint-on-premises-in-the-azure-portal).
 - Um conector Proxy de aplicação instalado e em funcionamento numa máquina dentro do domínio corporativo.


## <a name="step-1-integrate-sharepoint-on-premises-with-azure-ad"></a>Passo 1: Integrar o SharePoint no local com a Azure AD 

1. Configure a aplicação SharePoint no local. Para obter mais informações, consulte [Tutorial: Azure Ative Directory integração única de sign-on com SharePoint no local](../saas-apps/sharepoint-on-premises-tutorial.md).
2. Valide a configuração antes de passar para o passo seguinte. Para validar, tente aceder ao SharePoint no local a partir da rede interna e confirmar que está acessível internamente. 


## <a name="step-2-publish-the-sharepoint-on-premises-application-with-application-proxy"></a>Passo 2: Publicar a aplicação sharepoint no local com aplicação Proxy

Neste passo, você cria uma aplicação no seu inquilino AZure AD que usa Application Proxy. Você define o URL externo e especifica o URL interno, ambos usados mais tarde no SharePoint.

> [!NOTE] 
> Os URLs Internos e Externos devem coincidir com o **Sinal no URL** na configuração da aplicação baseada em SAML no passo 1.

   ![Screenshot que mostra o sinal no valor de URL.](./media/application-proxy-integrate-with-sharepoint-server/sso-url-saml.png)


 1. Crie uma nova aplicação Azure AD Application Proxy com domínio personalizado. Para obter instruções passo a passo, consulte [os domínios personalizados no Azure AD Application Proxy](./application-proxy-configure-custom-domain.md).

    - URL interno: https://portal.contoso.com/ '
    - URL externo: https://portal.contoso.com/ '
    - Pré-Autenticação: Diretório Ativo Azure
    - Traduzir URLs em Cabeçalhos: Não
    - Traduzir URLs no Corpo de Aplicação: Não

        ![Screenshot que mostra as opções que usa para criar a aplicação.](./media/application-proxy-integrate-with-sharepoint-server/create-application-azure-active-directory.png)

2. Atribua os [mesmos grupos](../saas-apps/sharepoint-on-premises-tutorial.md#create-an-azure-ad-security-group-in-the-azure-portal) que atribuiu à aplicação da Galeria SharePoint no local.

3. Por fim, vá à secção **Propriedades** e coloque **visível para os utilizadores?** Esta opção garante que apenas o ícone da primeira aplicação aparece no Portal das Minhas Aplicações ( https://myapplications.microsoft.com) S.

   ![Screenshot que mostra onde definir o Visível para os utilizadores? opção.](./media/application-proxy-integrate-with-sharepoint-server/configure-properties.png)
 
## <a name="step-3-test-your-application"></a>Passo 3: Teste a sua aplicação

Utilizando um navegador a partir de um computador numa rede externa, navegue até ao link que configurado durante o passo de publicação. Certifique-se de que pode iniciar sôs com a conta de teste que criou.