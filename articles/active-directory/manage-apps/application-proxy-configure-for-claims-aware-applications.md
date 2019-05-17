---
title: Aplicações com suporte para afirmações - Proxy de aplicações do Azure AD | Documentos da Microsoft
description: Como publicar aplicações de ASP.NET no local que aceitar afirmações do ADFS para acesso remoto seguro aos utilizadores.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/08/2018
ms.author: mimart
ms.reviewer: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: c97729cf7d88ebdeefb44c83eb571bb6d7ebd0ed
ms.sourcegitcommit: be9fcaace62709cea55beb49a5bebf4f9701f7c6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/17/2019
ms.locfileid: "65825584"
---
# <a name="working-with-claims-aware-apps-in-application-proxy"></a>Trabalhar com aplicações com suporte para afirmações no Proxy de aplicações
[Aplicações com suporte para afirmações](https://msdn.microsoft.com/library/windows/desktop/bb736227.aspx) efetuar um redirecionamento para o serviço STS (Security Token). O STS solicita credenciais do usuário em troca de um token e, em seguida, redireciona o utilizador para a aplicação. Existem algumas formas de ativar o Proxy de aplicações para trabalhar com esses redirecionamentos. Utilize este artigo para configurar a sua implementação para aplicações com suporte para afirmações. 

## <a name="prerequisites"></a>Pré-requisitos
Certifique-se de que o STS que a aplicação com suporte para afirmações redireciona para está disponível fora da sua rede no local. Pode disponibilizar o STS, expondo-lo através de um proxy ou ao permitir conexões externas. 

## <a name="publish-your-application"></a>Publicar a aplicação

1. Publicar a sua aplicação, de acordo com as instruções descritas [publicar aplicações com o Proxy de aplicações](application-proxy-add-on-premises-application.md).
2. Navegue para a página de aplicativo no portal e selecione **início de sessão único**.
3. Se escolheu **do Azure Active Directory** como sua **método de pré-autenticação**, selecione **do Azure AD início de sessão único desativado** como sua **interno Método de autenticação**. Se escolheu **pass-through** como sua **método de pré-autenticação**, não precisa alterar nada.

## <a name="configure-adfs"></a>Configurar o AD FS

Pode configurar o ADFS para aplicações com suporte para afirmações de uma de duas formas. A primeira é através da utilização de domínios personalizados. A segunda é com o WS-Federation. 

### <a name="option-1-custom-domains"></a>Opção 1: Domínios personalizados

Se todos os URLs internos para as suas aplicações são totalmente qualificado (FQDN) de nomes de domínio, em seguida, pode configurar [domínios personalizados](application-proxy-configure-custom-domain.md) para as suas aplicações. Utilize os domínios personalizados para criar URLs externos que são os mesmos que os URLs internos. Quando suas URLs externos correspondem a suas URLs internos, em seguida, os redirecionamentos de STS funcionam se os seus utilizadores estiverem no local ou remoto. 

### <a name="option-2-ws-federation"></a>Opção 2: WS-Federation

1. Abra a gestão do AD FS.
2. Aceda a **entidade Confiadora confianças**, clique com o botão direito na aplicação está a publicar com o Proxy da aplicação e escolha **propriedades**.  

   ![Confianças da entidade confiadora faça duplo clique no nome da aplicação - captura de ecrã](./media/application-proxy-configure-for-claims-aware-applications/appproxyrelyingpartytrust.png)  

3. Sobre o **pontos de extremidade** separador, em **tipo de ponto final**, selecione **WS-Federation**.
4. Sob **fidedigno URL**, introduza o URL que introduziu no Proxy de aplicações em **URL externo** e clique em **OK**.  

   ![Adicionar um ponto final - definir o valor de URL fidedigno - captura de ecrã](./media/application-proxy-configure-for-claims-aware-applications/appproxyendpointtrustedurl.png)  

## <a name="next-steps"></a>Passos Seguintes
* [Ativar o início de sessão único em](configure-single-sign-on-portal.md) para aplicativos que não são compatíveis com declarações
* [Permitir que as aplicações de cliente nativo interagir com aplicativos de proxy](application-proxy-configure-native-client-application.md)


