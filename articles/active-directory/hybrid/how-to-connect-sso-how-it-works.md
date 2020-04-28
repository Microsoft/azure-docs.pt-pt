---
title: 'Azure AD Connect: Single Sign-On Seamless - Como funciona Microsoft Docs'
description: Este artigo descreve como funciona a funcionalidade Single Sign-On do Diretório Ativo Azure.
services: active-directory
keywords: o que é Azure AD Connect, instalar Diretório Ativo, componentes necessários para Azure AD, SSO, Sign-on Único
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/16/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: bd4743bc38c3b2b4b9495b33535b4b73f48d1372
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "71176669"
---
# <a name="azure-active-directory-seamless-single-sign-on-technical-deep-dive"></a>Azure Ative Directory Seamless Single Sign-On: Mergulho profundo técnico

Este artigo dá-lhe detalhes técnicos sobre como funciona a funcionalidade Azure Ative Directory Seamless Single Sign-On (Seamless SSO).

## <a name="how-does-seamless-sso-work"></a>Como funciona o SSO SSO sem emenda?

Esta secção tem três partes:

1. A configuração da função SSO SSO sem emenda.
2. Como uma única transação de sessão de utilizador num navegador web funciona com SSO Sso Sem emenda.
3. Como uma única transação de login de utilizador num cliente nativo trabalha com O SSO Sso.

### <a name="how-does-set-up-work"></a>Como funciona a instalação?

O SSO sem emenda está ativado utilizando o Azure AD Connect, como mostrado [aqui](how-to-connect-sso-quick-start.md). Ao ativar a funcionalidade, ocorrem os seguintes passos:

- Uma conta`AZUREADSSOACC`de computador () é criada no seu Diretório Ativo (AD) em cada floresta ad que sincroniza com Azure AD (utilizando Azure AD Connect).
- Além disso, alguns nomes principais de serviço Kerberos (SPNs) são criados para serem usados durante o processo de entrada de AD Azure.
- A chave de desencriptação Kerberos da conta de computador é partilhada de forma segura com a AD Azure. Se existirem múltiplas florestas adc, cada conta de computador terá a sua própria chave de desencriptação Kerberos única.

>[!IMPORTANT]
> A `AZUREADSSOACC` conta do computador precisa de ser fortemente protegida por razões de segurança. Apenas os Administradores de Domínio devem ser capazes de gerir a conta do computador. Certifique-se de que a delegação kerberos na conta do computador está desativada e que nenhuma outra conta no Diretório Ativo tem permissões de delegação na conta do `AZUREADSSOACC` computador. Guarde a conta do computador numa Unidade de Organização (OU) onde estejam a salvo de supressões acidentais e onde apenas os Administradores de Domínio tenham acesso. A chave de desencriptação Kerberos na conta do computador também deve ser tratada como sensível. Recomendamos vivamente que revire a chave de `AZUREADSSOACC` [desencriptação Kerberos](how-to-connect-sso-faq.md) da conta do computador pelo menos a cada 30 dias.

Uma vez concluída a configuração, o SSO SSO sem emenda funciona da mesma forma que qualquer outro sign-in que utilize autenticação integrada do Windows (IWA).

### <a name="how-does-sign-in-on-a-web-browser-with-seamless-sso-work"></a>Como funciona o sessão num navegador web com SSO SSO sem emenda?

O fluxo de entrada num navegador web é o seguinte:

1. O utilizador tenta aceder a uma aplicação web https://outlook.office365.com/owa/) (por exemplo, a App Web do Outlook - a partir de um dispositivo corporativo de domínio dentro da sua rede corporativa.
2. Se o utilizador ainda não estiver inscrito, o utilizador é redirecionado para a página de entrada da AD Azure.
3. Os tipos de utilizador no seu nome de utilizador na página de entrada de anúncios da AD Azure.

   >[!NOTE]
   >Para [determinadas aplicações,](./how-to-connect-sso-faq.md)os passos 2 & 3 são ignorados.

4. Utilizando o JavaScript em segundo plano, o Azure AD desafia o navegador, através de uma resposta não autorizada 401, para fornecer um bilhete Kerberos.
5. O navegador, por sua vez, solicita um `AZUREADSSOACC` bilhete do Ative Directy para a conta de computador (que representa a Azure AD).
6. O Ative Directory localiza a conta do computador e devolve um bilhete Kerberos ao navegador encriptado com o segredo da conta do computador.
7. O navegador reencaminha o bilhete Kerberos que adquiriu do Ative Directory para o Azure AD.
8. A Azure AD desencripta o bilhete Kerberos, que inclui a identidade do utilizador assinado no dispositivo corporativo, utilizando a chave previamente partilhada.
9. Após avaliação, a Azure AD devolve um token à aplicação ou pede ao utilizador que execute provas adicionais, como a Autenticação multi-factor.
10. Se o utilizador iniciar o seu registo, o utilizador poderá aceder à aplicação.

O diagrama que se segue ilustra todos os componentes e passos envolvidos.

![Sem emenda single sign on - fluxo de aplicativo web](./media/how-to-connect-sso-how-it-works/sso2.png)

O SSO sem emenda é oportunista, o que significa que se falhar, a experiência de entrada recai sobre o seu comportamento regular - ou seja, o utilizador precisa de introduzir a sua palavra-passe para iniciar sessão.

### <a name="how-does-sign-in-on-a-native-client-with-seamless-sso-work"></a>Como funciona o login num cliente nativo com SSO SSO sem emenda?

O fluxo de entrada num cliente nativo é o seguinte:

1. O utilizador tenta aceder a uma aplicação nativa (por exemplo, ao cliente Outlook) a partir de um dispositivo corporativo de domínio dentro da sua rede corporativa.
2. Se o utilizador ainda não estiver inscrito, a aplicação nativa recupera o nome de utilizador do utilizador da sessão windows do dispositivo.
3. A aplicação envia o nome de utilizador para a AD Azure e recupera o ponto final do WS-Trust MEX do seu inquilino. Este ponto final wS-Trust é utilizado exclusivamente pela funcionalidade Seamless SSO, e não é uma implementação geral do protocolo WS-Trust sobre a AD Azure.
4. A aplicação questiona então o ponto final do WS-Trust MEX para ver se o ponto final de autenticação integrado está disponível. O ponto final de autenticação integrado é utilizado exclusivamente pela funcionalidade Seamless SSO.
5. Se o passo 4 for bem sucedido, um desafio Kerberos é emitido.
6. Se a aplicação conseguir recuperar o bilhete Kerberos, encaminha-o para o ponto final de autenticação integrado da Azure AD.
7. A Azure AD desencripta o bilhete Kerberos e valida-o.
8. A Azure AD assina o utilizador e emite um token SAML para a aplicação.
9. A aplicação submete então o símbolo SAML ao ponto final de token OAuth2 da Azure AD.
10. A Azure AD valida o símbolo SAML, e emite na app um token de acesso e um token de atualização para o recurso especificado, e um símbolo id.
11. O utilizador tem acesso ao recurso da aplicação.

O diagrama que se segue ilustra todos os componentes e passos envolvidos.

![Sem emenda single sign on - fluxo de aplicativo nativo](./media/how-to-connect-sso-how-it-works/sso14.png)

## <a name="next-steps"></a>Passos seguintes

- [**Quick Start**](how-to-connect-sso-quick-start.md) - Prepare-se e execute Azure AD Seamless SSO.
- [**Perguntas frequentes**](how-to-connect-sso-faq.md) - Respostas a perguntas frequentes.
- [**Troubleshoot**](tshoot-connect-sso.md) - Aprenda a resolver problemas comuns com a funcionalidade.
- [**UserVoice**](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) - Para arquivar novos pedidos de funcionalidades.
