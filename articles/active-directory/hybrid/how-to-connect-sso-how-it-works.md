---
title: 'Azure AD Connect: Single Sign-On sem emenda - Como funciona | Microsoft Docs'
description: Este artigo descreve como funciona o recurso Azure Ative Directory Seamless Single Sign-On.
services: active-directory
keywords: o que é Azure AD Connect, instalar Ative Directory, componentes necessários para Azure AD, SSO, Single Sign-on
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 04/16/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: bde937adba8d2469390a6cf404f6cce8c5008e87
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "100369650"
---
# <a name="azure-active-directory-seamless-single-sign-on-technical-deep-dive"></a>Azure Ative Directory Single Sign-On: Mergulho profundo técnico

Este artigo dá-lhe detalhes técnicos sobre como funciona a funcionalidade Azure Ative Directory Seamless Single Sign-On (Seamless SSO).

## <a name="how-does-seamless-sso-work"></a>Como funciona o Seamless SSO?

Esta secção tem três partes:

1. A configuração da funcionalidade SSO sem emenda.
2. Como uma única transação de insusição de utilizador num navegador web funciona com Seamless SSO.
3. Como uma única transação de login de um único utilizador num cliente nativo funciona com a Seamless SSO.

### <a name="how-does-set-up-work"></a>Como é que se instala o trabalho?

O SSO sem emenda está ativado utilizando o Azure AD Connect, como mostrado [aqui.](how-to-connect-sso-quick-start.md) Ao permitir a funcionalidade, ocorrem os seguintes passos:

- Uma conta de computador ( `AZUREADSSOACC` ) é criada no seu Ative Directy (AD) em cada floresta AD que sincroniza com Azure AD (usando Azure AD Connect).
- Além disso, são criados vários nomes principais de serviço Kerberos (SPNs) para serem utilizados durante o processo de entrada de Azure AD.
- A chave de desencriptação Kerberos da conta do computador é partilhada de forma segura com a Azure AD. Se existirem múltiplas florestas de AD, cada conta de computador terá a sua própria chave de desencriptação Kerberos única.

>[!IMPORTANT]
> A `AZUREADSSOACC` conta do computador precisa de ser fortemente protegida por razões de segurança. Apenas os Administradores de Domínio devem ser capazes de gerir a conta do computador. Certifique-se de que a delegação kerberos na conta do computador está desativada e que nenhuma outra conta no Ative Directory tem permissões de delegação na `AZUREADSSOACC` conta do computador.. Guarde a conta do computador numa Unidade de Organização (OU) onde estão a salvo de supressões acidentais e onde apenas os Administradores de Domínio têm acesso. A chave de desencriptação Kerberos na conta do computador também deve ser tratada como sensível. Recomendamos vivamente que [revir a chave de desencriptação Kerberos](how-to-connect-sso-faq.md) da conta do `AZUREADSSOACC` computador pelo menos a cada 30 dias.

>[!IMPORTANT]
> O Seamless SSO suporta os tipos de encriptação AES256_HMAC_SHA1, AES128_HMAC_SHA1 e RC4_HMAC_MD5 para Kerberos. Recomenda-se que o tipo de encriptação da conta AzureADSSSOAcc$ esteja definido para AES256_HMAC_SHA1, ou um dos tipos AES vs. RC4 para uma maior segurança. O tipo de encriptação é armazenado nos MsDS-SuportadosEncryptionTypes atribuem da conta no seu Diretório Ativo.  Se o tipo de encriptação da conta AzureADSSOAcc$ estiver definido para RC4_HMAC_MD5, e pretender alterá-lo para um dos tipos de encriptação AES, certifique-se de que primeiro rolar sobre a chave de desencriptação Kerberos da conta AzureADSSOAcc$, conforme explicado no [documento faq](how-to-connect-sso-faq.md) sob a questão relevante, caso contrário, o SSO sem emenda não acontecerá.

Uma vez concluída a configuração, o Seamless SSO funciona da mesma forma que qualquer outro sinal que utiliza a Autenticação Integrada do Windows (IWA).

### <a name="how-does-sign-in-on-a-web-browser-with-seamless-sso-work"></a>Como funciona o s-in num navegador web com SSO sem emenda?

O fluxo de entrada de entrada num navegador web é o seguinte:

1. O utilizador tenta aceder a uma aplicação web (por exemplo, a App Web Outlook - https://outlook.office365.com/owa/) a partir de um dispositivo corporativo de domínio dentro da sua rede corporativa.
2. Se o utilizador ainda não tiver assinado, o utilizador é redirecionado para a página de insusição Azure.
3. Os tipos de utilizador no seu nome de utilizador na página de entrada Azure AD.

   >[!NOTE]
   >Para [certas aplicações,](./how-to-connect-sso-faq.md)os passos 2 & 3 são ignorados.

4. Utilizando o JavaScript em segundo plano, o Azure AD desafia o navegador, através de uma resposta não autorizada 401, para fornecer um bilhete Kerberos.
5. O navegador, por sua vez, solicita um bilhete do Ative Directory para a `AZUREADSSOACC` conta do computador (que representa o Azure AD).
6. Ative Directory localiza a conta do computador e devolve um bilhete Kerberos para o navegador encriptado com o segredo da conta do computador.
7. O navegador remete o bilhete Kerberos que adquiriu do Ative Directory para a Azure AD.
8. A Azure AD desencripta o bilhete Kerberos, que inclui a identidade do utilizador assinado no dispositivo corporativo, utilizando a chave anteriormente partilhada.
9. Após avaliação, a Azure AD devolve um símbolo à aplicação ou pede ao utilizador para efetuar provas adicionais, como a Autenticação Multi-Factor.
10. Se o pedido de sômed in do utilizador for bem sucedido, o utilizador poderá aceder à aplicação.

O diagrama seguinte ilustra todos os componentes e os passos envolvidos.

![Sem emenda single sign on - Fluxo de aplicativo web](./media/how-to-connect-sso-how-it-works/sso2.png)

O SSO sem emenda é oportunista, o que significa que, se falhar, a experiência de entrada recai sobre o seu comportamento regular - ou seja, o utilizador precisa de introduzir a sua palavra-passe para iniciar sessão.

### <a name="how-does-sign-in-on-a-native-client-with-seamless-sso-work"></a>Como funciona o login de um cliente nativo com SSO sem emenda?

O fluxo de entrada num cliente nativo é o seguinte:

1. O utilizador tenta aceder a uma aplicação nativa (por exemplo, o cliente Outlook) a partir de um dispositivo corporativo de domínio dentro da sua rede corporativa.
2. Se o utilizador ainda não tiver assinado, a aplicação nativa recupera o nome de utilizador do utilizador a partir da sessão do Windows do dispositivo.
3. A aplicação envia o nome de utilizador para Azure AD e recupera o WS-Trust ponto final MEX do seu inquilino. Este WS-Trust ponto final é utilizado exclusivamente pela funcionalidade SSO sem emenda, e não é uma implementação geral do protocolo WS-Trust em Azure AD.
4. A aplicação consulta então o ponto final mex WS-Trust para ver se está disponível o ponto final de autenticação integrada. O ponto final de autenticação integrado é utilizado exclusivamente pela funcionalidade Seamless SSO.
5. Se o passo 4 for bem sucedido, é emitido um desafio Kerberos.
6. Se a aplicação conseguir recuperar o bilhete Kerberos, encaminha-o para o ponto final de autenticação integrado da Azure AD.
7. A AZure AD desencripta o bilhete Kerberos e valida-o.
8. A Azure AD assina o utilizador e emite um token SAML para a aplicação.
9. Em seguida, a aplicação submete o token SAML ao ponto final simbólico da Azure AD.
10. O Azure AD valida o token SAML, e emite à app um token de acesso e um token de atualização para o recurso especificado, e um token de id.
11. O utilizador tem acesso ao recurso da aplicação.

O diagrama seguinte ilustra todos os componentes e os passos envolvidos.

![Sem emenda single sign on - fluxo de aplicativo nativo](./media/how-to-connect-sso-how-it-works/sso14.png)

## <a name="next-steps"></a>Passos seguintes

- [**Quick Start**](how-to-connect-sso-quick-start.md) - Levante-se e execute Azure AD Seamless SSO.
- [**Perguntas frequentes**](how-to-connect-sso-faq.md) - Respostas a perguntas frequentes.
- [**Resolução de problemas**](tshoot-connect-sso.md) - Aprenda a resolver problemas comuns com a funcionalidade.
- [**UserVoice**](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) - Para arquivar novos pedidos de funcionalidades.
