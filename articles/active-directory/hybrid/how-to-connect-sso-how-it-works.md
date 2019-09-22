---
title: 'Azure AD Connect: Logon único contínuo-como funciona | Microsoft Docs'
description: Este artigo descreve como o Azure Active Directory recurso de logon único contínuo funciona.
services: active-directory
keywords: o que é Azure AD Connect, instalar Active Directory, componentes necessários para o Azure AD, SSO, logon único
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
ms.sourcegitcommit: 83df2aed7cafb493b36d93b1699d24f36c1daa45
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/22/2019
ms.locfileid: "71176669"
---
# <a name="azure-active-directory-seamless-single-sign-on-technical-deep-dive"></a>Azure Active Directory logon único contínuo: Aprofundamento técnico

Este artigo fornece detalhes técnicos sobre como o recurso de Azure Active Directory logon único contínuo (SSO contínuo) funciona.

## <a name="how-does-seamless-sso-work"></a>Como funciona o SSO contínuo?

Esta seção tem três partes:

1. A configuração do recurso SSO contínuo.
2. Como uma transação de entrada de usuário único em um navegador da Web funciona com o SSO contínuo.
3. Como uma transação de entrada de usuário único em um cliente nativo funciona com o SSO contínuo.

### <a name="how-does-set-up-work"></a>Como configurar o trabalho?

O SSO contínuo é habilitado usando Azure AD Connect, como mostrado [aqui](how-to-connect-sso-quick-start.md). Ao habilitar o recurso, ocorrem as seguintes etapas:

- Uma conta de computador`AZUREADSSOACC`() é criada no seu Active Directory local (AD) em cada floresta do AD que você sincroniza com o Azure AD (usando Azure ad Connect).
- Além disso, um número de nomes de entidade de serviço (SPNs) Kerberos é criado para ser usado durante o processo de entrada do Azure AD.
- A chave de descriptografia Kerberos da conta do computador é compartilhada com segurança com o Azure AD. Se houver várias florestas do AD, cada conta de computador terá sua própria chave de descriptografia Kerberos exclusiva.

>[!IMPORTANT]
> A `AZUREADSSOACC` conta do computador precisa estar fortemente protegida por motivos de segurança. Somente administradores de domínio devem ser capazes de gerenciar a conta de computador. Verifique se a delegação de Kerberos na conta do computador está desabilitada e se nenhuma outra conta no Active Directory tem permissões de `AZUREADSSOACC` delegação na conta do computador. Armazene a conta de computador em uma UO (unidade organizacional) em que elas estão protegidas contra exclusões acidentais e em que somente administradores de domínio têm acesso. A chave de descriptografia Kerberos na conta do computador também deve ser tratada como confidencial. É altamente recomendável que você [reverta a chave de descriptografia Kerberos](how-to-connect-sso-faq.md) da `AZUREADSSOACC` conta do computador pelo menos a cada 30 dias.

Após a conclusão da instalação, o SSO contínuo funciona da mesma maneira que qualquer outra entrada que usa a autenticação integrada do Windows (IWA).

### <a name="how-does-sign-in-on-a-web-browser-with-seamless-sso-work"></a>Como a entrada em um navegador da Web com o SSO contínuo funciona?

O fluxo de entrada em um navegador da Web é o seguinte:

1. O usuário tenta acessar um aplicativo Web (por exemplo, o Outlook Web App- https://outlook.office365.com/owa/) de um dispositivo corporativo ingressado no domínio dentro de sua rede corporativa.
2. Se o usuário ainda não estiver conectado, o usuário será redirecionado para a página de entrada do Azure AD.
3. O usuário digita em seu nome de usuário na página de entrada do Azure AD.

   >[!NOTE]
   >Para [determinados aplicativos](./how-to-connect-sso-faq.md), as etapas 2 & 3 são ignoradas.

4. Usando o JavaScript em segundo plano, o Azure AD desafia o navegador, por meio de uma resposta não autorizada do 401, para fornecer um tíquete Kerberos.
5. O navegador, por sua vez, solicita um tíquete de Active Directory para `AZUREADSSOACC` a conta de computador (que representa o Azure AD).
6. Active Directory localiza a conta de computador e retorna um tíquete Kerberos para o navegador criptografado com o segredo da conta do computador.
7. O navegador encaminha o tíquete Kerberos que adquiriu do Active Directory para o Azure AD.
8. O Azure AD descriptografa o tíquete Kerberos, que inclui a identidade do usuário conectado ao dispositivo corporativo, usando a chave compartilhada anteriormente.
9. Após a avaliação, o Azure AD retorna um token de volta para o aplicativo ou solicita que o usuário execute provas adicionais, como a autenticação multifator.
10. Se a entrada do usuário for bem-sucedida, o usuário será capaz de acessar o aplicativo.

O diagrama a seguir ilustra todos os componentes e as etapas envolvidas.

![Logon único contínuo-fluxo do aplicativo Web](./media/how-to-connect-sso-how-it-works/sso2.png)

O SSO contínuo é oportuno, o que significa que, se ele falhar, a experiência de entrada voltará a seu comportamento regular, ou seja, o usuário precisará inserir sua senha para entrar.

### <a name="how-does-sign-in-on-a-native-client-with-seamless-sso-work"></a>Como funciona o logon em um cliente nativo com SSO contínuo?

O fluxo de entrada em um cliente nativo é o seguinte:

1. O usuário tenta acessar um aplicativo nativo (por exemplo, o cliente do Outlook) de um dispositivo corporativo ingressado no domínio dentro de sua rede corporativa.
2. Se o usuário ainda não estiver conectado, o aplicativo nativo recuperará o nome de usuário da sessão do Windows do dispositivo.
3. O aplicativo envia o nome de usuário para o Azure AD e recupera o ponto de extremidade MEX WS-Trust do seu locatário. Esse ponto de extremidade WS-Trust é usado exclusivamente pelo recurso SSO contínuo e não é uma implementação geral do protocolo WS-Trust no Azure AD.
4. Em seguida, o aplicativo consulta o ponto de extremidade MEX do WS-Trust para ver se o ponto de extremidade de autenticação integrada está disponível. O ponto de extremidade de autenticação integrado é usado exclusivamente pelo recurso SSO contínuo.
5. Se a etapa 4 tiver sucesso, um desafio Kerberos será emitido.
6. Se o aplicativo for capaz de recuperar o tíquete Kerberos, ele o encaminhará para o ponto de extremidade de autenticação integrada do Azure AD.
7. O Azure AD descriptografa o tíquete Kerberos e o valida.
8. O Azure AD assina o usuário e emite um token SAML para o aplicativo.
9. Em seguida, o aplicativo envia o token SAML para o ponto de extremidade do token OAuth2 do Azure AD.
10. O Azure AD valida o token SAML e emite para o aplicativo um token de acesso e um token de atualização para o recurso especificado e um token de ID.
11. O usuário obtém acesso ao recurso do aplicativo.

O diagrama a seguir ilustra todos os componentes e as etapas envolvidas.

![Logon único contínuo-fluxo de aplicativo nativo](./media/how-to-connect-sso-how-it-works/sso14.png)

## <a name="next-steps"></a>Passos seguintes

- [**Início rápido**](how-to-connect-sso-quick-start.md) -obtenha e execute o SSO contínuo do Azure AD.
- [**Perguntas**](how-to-connect-sso-faq.md) frequentes-respostas para perguntas frequentes.
- [**Solução de problemas**](tshoot-connect-sso.md) -saiba como resolver problemas comuns com o recurso.
- [**UserVoice**](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) -para o arquivamento de novas solicitações de recursos.
