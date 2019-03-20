---
title: 'Azure AD Connect: Totalmente integrado Single Sign-On - como funciona | Documentos da Microsoft'
description: Este artigo descreve como funciona o recurso do Azure Active Directory totalmente integrada início de sessão único.
services: active-directory
keywords: o que é o Azure AD Connect, a instalação do Active Directory, necessário componentes para o Azure AD, SSO, Single Sign-on
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/14/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5217f21449efeb2086770f040fb781765ea819eb
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58083942"
---
# <a name="azure-active-directory-seamless-single-sign-on-technical-deep-dive"></a>O Azure Active Directory totalmente integrada início de sessão único: Análise técnica aprofundada

Este artigo fornece detalhes técnicos sobre como funciona o recurso do Azure Active Directory totalmente integrada início de sessão único (SSO totalmente integrado).

## <a name="how-does-seamless-sso-work"></a>Como funciona o SSO totalmente integrado?

Esta secção tem três partes a ele:

1. A configuração do recurso SSO totalmente integrado.
2. Como uma transação de início de sessão do utilizador único num navegador da web funciona com o SSO totalmente integrado.
3. Como uma transação de início de sessão do utilizador único num cliente nativo funciona com o SSO totalmente integrado.

### <a name="how-does-set-up-work"></a>Como configurar o trabalho?

SSO totalmente integrado é ativada através do Azure AD Connect, conforme mostrado [aqui](how-to-connect-sso-quick-start.md). Ao ativar a funcionalidade, ocorrem os seguintes passos:

- Uma conta de computador com o nome `AZUREADSSOACC` (que representa o Azure AD) é criada no seu local do Active Directory (AD) em cada floresta do AD.
- Chave de desencriptação do Kerberos da conta de computador é compartilhado de forma segura com o Azure AD. Se existirem várias florestas do AD, cada um terá sua própria chave de desencriptação do Kerberos.
- Além disso, os dois Kerberos principal nomes de serviço (SPNs) são criados para representar dois URLs que são utilizadas durante o início de sessão no Azure AD.

>[!NOTE]
> A conta de computador e os SPNs Kerberos são criados em cada floresta do AD sincroniza com o Azure AD (com o Azure AD Connect) e cujos usuários pretende obter SSO totalmente integrado. Mover o `AZUREADSSOACC` conta de computador para uma unidade organizacional (UO) onde outras contas de computador são armazenadas para se certificar de que é gerido da mesma forma e não é eliminado.

>[!IMPORTANT]
>É altamente recomendável que [o rollover da chave de desencriptação do Kerberos](how-to-connect-sso-faq.md#how-can-i-roll-over-the-kerberos-decryption-key-of-the-azureadssoacc-computer-account) do `AZUREADSSOACC` conta de computador, pelo menos, a cada 30 dias.

Assim que a configuração estiver concluída, o SSO totalmente integrado funciona da mesma forma que qualquer outro início de sessão que utiliza autenticação integrada do Windows (IWA).

### <a name="how-does-sign-in-on-a-web-browser-with-seamless-sso-work"></a>Como iniciar sessão num navegador da web com o trabalho de SSO totalmente integrado?

O fluxo de início de sessão num navegador da web é o seguinte:

1. O utilizador tenta aceder a aplicações web (por exemplo, o Outlook Web App - https://outlook.office365.com/owa/) de um dispositivo da empresa associado a um domínio no interior da rede empresarial.
2. Se o utilizador não está já iniciou sessão, o utilizador é redirecionado para a página de início de sessão do Azure AD.
3. O usuário digita no respetivo nome de utilizador para a página de início de sessão do Azure AD.

   >[!NOTE]
   >Para [determinados aplicativos](./how-to-connect-sso-faq.md#what-applications-take-advantage-of-domain_hint-or-login_hint-parameter-capability-of-seamless-sso), passos 2 e 3 são ignorados.

4. Usando o JavaScript em segundo plano, o Azure AD desafios ao navegador, via uma resposta 401 não autorizado, para fornecer um tíquete Kerberos.
5. O navegador, por sua vez, solicita um pedido de suporte do Active Directory para o `AZUREADSSOACC` conta de computador (que representa o Azure AD).
6. Active Directory localiza a conta de computador e retorna um tíquete Kerberos para o navegador encriptado com o segredo da conta de computador.
7. O browser encaminha o pedido de Kerberos adquiriu do Active Directory para o Azure AD.
8. O Azure AD desencripta o tíquete Kerberos, que inclui a identidade do utilizador com sessão iniciada no dispositivo da empresa, utilizando a chave partilhada anteriormente.
9. Após a avaliação, do Azure AD devolve um token de volta para a aplicação ou pede ao utilizador para efetuar provas adicionais, como o multi-factor Authentication.
10. Se o utilizador inicie sessão for bem-sucedida, o utilizador é capaz de aceder à aplicação.

O diagrama seguinte ilustra a todos os componentes e as etapas envolvidas.

![Início de sessão único totalmente integrado no - Web fluxo da aplicação](./media/how-to-connect-sso-how-it-works/sso2.png)

SSO totalmente integrado é oportunista, o que significa que se falhar, a experiência de início de sessão no retrocede para seu comportamento regular - ou seja, o utilizador tem de introduzir a palavra-passe para iniciar sessão.

### <a name="how-does-sign-in-on-a-native-client-with-seamless-sso-work"></a>Como iniciar sessão num cliente nativo com o trabalho de SSO totalmente integrado?

O fluxo de início de sessão num cliente nativo é o seguinte:

1. O utilizador tenta acessar um aplicativo nativo (por exemplo, o cliente do Outlook) a partir de um dispositivo da empresa associado a um domínio no interior da rede empresarial.
2. Se o utilizador não está já iniciou sessão, o aplicativo nativo obtém o nome de utilizador do utilizador da sessão do Windows do dispositivo.
3. A aplicação envia o nome de utilizador para o Azure AD e recupera o ponto de extremidade do seu inquilino MEX de WS-Trust. Esse ponto de extremidade do WS-Trust é usado exclusivamente pela funcionalidade de SSO totalmente integrado e não é uma implementação geral do protocolo WS-Trust no Azure AD.
4. A aplicação, em seguida, consulta o ponto de extremidade de MEX de WS-Trust para ver se integrado o ponto final de autenticação está disponível. O ponto de final de autenticação integrada é utilizado exclusivamente pela funcionalidade de SSO totalmente integrado.
5. Se tiver êxito passo 4, é emitido um desafio de Kerberos.
6. Se a aplicação é possível obter o tíquete Kerberos, ela encaminha até o ponto final de autenticação integrada do Azure AD.
7. Azure AD desencripta o tíquete Kerberos e validá-lo.
8. Azure AD assina o utilizador no e emite um token SAML para a aplicação.
9. A aplicação, em seguida, envia o token SAML para o ponto final de token OAuth2 do Azure AD.
10. Azure AD valida o token SAML e emite para a aplicação, um token de acesso e um token de atualização para o recurso especificado e um token de id.
11. O utilizador obtém acesso a recursos da aplicação.

O diagrama seguinte ilustra a todos os componentes e as etapas envolvidas.

![Inicie sessão único totalmente integrado no - fluxo de aplicação nativa](./media/how-to-connect-sso-how-it-works/sso14.png)

## <a name="next-steps"></a>Passos Seguintes

- [**Início Rápido** ](how-to-connect-sso-quick-start.md) - colocar em funcionamento o SSO totalmente integrado do Azure AD.
- [**Perguntas mais frequentes** ](how-to-connect-sso-faq.md) -respostas para perguntas mais frequentes.
- [**Resolução de problemas** ](tshoot-connect-sso.md) -Saiba como resolver problemas comuns com a funcionalidade.
- [**UserVoice** ](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) – para preenchimento de pedidos de novas funcionalidades.
