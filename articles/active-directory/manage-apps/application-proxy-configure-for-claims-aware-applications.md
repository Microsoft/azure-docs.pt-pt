---
title: Aplicações conscientes de sinistros - Procuração de aplicações da AD Azure [ Microsoft Docs
description: Como publicar no local ASP.NET aplicações que aceitam pedidos aDFS para acesso remoto seguro pelos seus utilizadores.
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
ms.openlocfilehash: cbc5c356ea5a542fdc12b11aff236c56b146b3d5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "68477248"
---
# <a name="working-with-claims-aware-apps-in-application-proxy"></a>Trabalhar com aplicações conscientes de sinistros no Proxy de Aplicações
[As aplicações conscientes de sinistros](https://msdn.microsoft.com/library/windows/desktop/bb736227.aspx) realizam uma reorientação para o Serviço de Token de Segurança (STS). O STS solicita credenciais ao utilizador em troca de um token e, em seguida, redireciona o utilizador para a aplicação. Existem algumas formas de permitir que o Application Proxy trabalhe com estes redirecionamentos. Utilize este artigo para configurar a sua implementação para aplicações conscientes de sinistros. 

## <a name="prerequisites"></a>Pré-requisitos
Certifique-se de que o STS a que a aplicação consciente de sinistros redireciona para está disponível fora da sua rede no local. Pode disponibilizar o STS expondo-o através de um proxy ou permitindo ligações externas. 

## <a name="publish-your-application"></a>Publicar a aplicação

1. Publique a sua aplicação de acordo com as instruções descritas nas [aplicações da Publicação com procuração de aplicações](application-proxy-add-on-premises-application.md).
2. Navegue para a página de aplicação no portal e selecione **Single sign-on**.
3. Se escolheu o **Diretório Ativo Azure** como método de **pré-autenticação,** selecione um único sinal de **AD Azure desativado** como método de **autenticação interna**. Se escolheu a **Passthrough** como o seu Método de **Pré-Autenticação,** não precisa de mudar nada.

## <a name="configure-adfs"></a>Configurar ADFS

Pode configurar a ADFS para aplicações conscientes de sinistros de uma de duas formas. A primeira é usando domínios personalizados. A segunda é com a WS-Federação. 

### <a name="option-1-custom-domains"></a>Opção 1: Domínios personalizados

Se todos os URLs internos para as suas aplicações forem nomes de domínio totalmente qualificados (FQDNs), então pode configurar [domínios personalizados](application-proxy-configure-custom-domain.md) para as suas aplicações. Utilize os domínios personalizados para criar URLs externos que são os mesmos que os URLs internos. Quando os seus URLs externos correspondem aos seus URLs internos, as redirecionações STS funcionam se os seus utilizadores estão no local ou remotos. 

### <a name="option-2-ws-federation"></a>Opção 2: WS-Federação

1. Gestão aberta da ADFS.
2. Vá ao **Trusts trusts do Partido Fiduciário,** clique à direita na aplicação que está a publicar com o Application Proxy, e escolha **Propriedades**.  

   ![Confiar no partido trusts clique direito no nome da aplicação - screenshot](./media/application-proxy-configure-for-claims-aware-applications/appproxyrelyingpartytrust.png)  

3. No separador **Pontos Finais,** sob **o tipo Endpoint,** selecione **WS-Federation**.
4. Em **URL fidedigno,** introduza o URL introduzido no Proxy de Aplicação em **URL Externo** e clique EM **OK**.  

   ![Adicione um Endpoint - definir valor DE URL fidedigno - screenshot](./media/application-proxy-configure-for-claims-aware-applications/appproxyendpointtrustedurl.png)  

## <a name="next-steps"></a>Passos seguintes
* [Ativar um único sinal](configure-single-sign-on-non-gallery-applications.md) para aplicações que não estejam cientes de reclamações
* [Permitir que as aplicações de clientes nativos interajam com aplicações por procuração](application-proxy-configure-native-client-application.md)


