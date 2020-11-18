---
title: Aplicativos conscientes de reclamações - Azure AD App Proxy / Microsoft Docs
description: Como publicar no local ASP.NET aplicações que aceitam pedidos ADFS para acesso remoto seguro por parte dos seus utilizadores.
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
ms.date: 11/08/2018
ms.author: kenwith
ms.reviewer: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: f5c840722ae6b03a0b8a7fa44e5999e14730d4f3
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/17/2020
ms.locfileid: "94656280"
---
# <a name="working-with-claims-aware-apps-in-application-proxy"></a>Trabalhar com aplicações conscientes de reclamações no Application Proxy
[As aplicações conscientes das reclamações](/previous-versions/windows/desktop/legacy/bb736227(v=vs.85)) realizam uma reorientação para o Serviço de Token de Segurança (STS). O STS solicita credenciais ao utilizador em troca de um token e, em seguida, redireciona o utilizador para a aplicação. Existem algumas formas de permitir que o Application Proxy trabalhe com estes redirecionamentos. Utilize este artigo para configurar a sua implementação para aplicações conscientes de sinistros. 

## <a name="prerequisites"></a>Pré-requisitos
Certifique-se de que o STS que a aplicação de sinistros redireciona para está disponível fora da sua rede no local. Pode disponibilizar o STS expondo-o através de um representante ou permitindo ligações externas. 

## <a name="publish-your-application"></a>Publicar a aplicação

1. Publique a sua aplicação de acordo com as instruções descritas em [Publicar aplicações com Aplicação Proxy](application-proxy-add-on-premises-application.md).
2. Navegue para a página de aplicação no portal e selecione **Single sign-on**.
3. Se escolher **o Azure Ative Directory** como **método de pré-autorização,** selecione **Azure AD single sign-on desativado** como o seu **Método de Autenticação Interna**. Se escolheste **o Passthrough** como o teu **Método de Pré-Autorização,** não precisas de mudar nada.

## <a name="configure-adfs"></a>Configure ADFS

Pode configurar a ADFS para aplicações conscientes de reclamações de uma de duas maneiras. A primeira é utilizando domínios personalizados. A segunda é com a WS-Federation. 

### <a name="option-1-custom-domains"></a>Opção 1: Domínios personalizados

Se todos os URLs internos para as suas aplicações forem nomes de domínio totalmente qualificados (FQDNs), então pode configurar [domínios personalizados](application-proxy-configure-custom-domain.md) para as suas aplicações. Utilize os domínios personalizados para criar URLs externos que são os mesmos que os URLs internos. Quando os URLs externos combinam com os URLs internos, então as reorientações STS funcionam quer os seus utilizadores estejam no local ou remotos. 

### <a name="option-2-ws-federation"></a>Opção 2: WS-Federation

1. Abrir a gestão da ADFS.
2. Vá a **Relying Party Trusts,** clique à direita na aplicação que está a publicar com o Application Proxy e escolha **Propriedades.**  

   ![Trusts do Partido Confia no botão direito no nome da aplicação - screenshot](./media/application-proxy-configure-for-claims-aware-applications/appproxyrelyingpartytrust.png)  

3. No **separador Endpoints,** sob o **tipo Endpoint**, selecione **WS-Federation**.
4. Em **URL Fidedigno**, insira o URL que introduziu no Proxy de aplicação em **URL Externo** e clique em **OK**.  

   ![Adicione um Ponto Final - definir valor de URL fidedigno - screenshot](./media/application-proxy-configure-for-claims-aware-applications/appproxyendpointtrustedurl.png)  

## <a name="next-steps"></a>Próximos passos
* [Permitir que as aplicações de clientes nativos interajam com aplicações proxy](application-proxy-configure-native-client-application.md)