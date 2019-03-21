---
title: Erro na página de uma aplicação depois de iniciar sessão | Documentos da Microsoft
description: Como resolver problemas com início de sessão do Azure AD quando o aplicativo propriamente dito emite um erro
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: celested
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 438f4f60c80cb798c3c40f57e22c9194a9fd5f86
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58110856"
---
# <a name="error-on-an-applications-page-after-signing-in"></a>Erro na página de uma aplicação depois de iniciar sessão

Neste cenário, o Azure AD iniciou sessão do utilizador no, mas o aplicativo está sendo exibido um erro não permitir que o usuário concluir com êxito o fluxo de início de sessão. Neste cenário, a aplicação não está a aceitar o problema de resposta pelo Azure AD.

Existem algumas razões possíveis, por que a aplicação não aceitar a resposta do Azure AD. Se o erro no aplicativo não está claro o suficiente para saber o que está em falta na resposta, em seguida:

-   Se o aplicativo é a galeria do Azure AD, certifique-se de que seguiu todos os passos no artigo [como depurar baseado em SAML início de sessão único para aplicações no Azure Active Directory](https://azure.microsoft.com/documentation/articles/active-directory-saml-debugging).

-   Usar uma ferramenta como [Fiddler](https://www.telerik.com/fiddler) para capturar o pedido SAML, resposta SAML e SAML token.

-   Partilhe a resposta SAML com o fabricante da aplicação para saber o que está em falta.

## <a name="missing-attributes-in-the-saml-response"></a>Atributos em falta na resposta SAML

Para adicionar um atributo na configuração do Azure AD para serem enviados na resposta do Azure AD, siga estes passos:

1. Abra o [ **portal do Azure** ](https://portal.azure.com/) e inicie sessão como um **Administrador Global** ou **Coadministrador.**

2. Abra o **extensão do Active Directory do Azure** ao clicar em **todos os serviços** na parte superior do menu de navegação esquerdo principal.

3. Escreva **"Azure Active Directory**" na caixa de pesquisa de filtro e selecione o **Azure Active Directory** item.

4. Clique em **aplicações empresariais** no menu de navegação do lado esquerdo do Azure Active Directory.

5. Clique em **todos os aplicativos** para ver uma lista de todas as suas aplicações.

   * Se não vir a aplicação que quer mostrar aqui, utilize o **filtro** na parte superior do **todas as listas de aplicações** e defina o **mostrar** a opção de **todos os Aplicações.**

6. Selecione a aplicação que pretende configurar o início de sessão único.

7. Assim que o aplicativo é carregado, clique nas **início de sessão único** no menu de navegação do lado esquerdo da aplicação.

8. Clique em **ver e editar os atributos de todos os outros utilizador sob** a **atributos de utilizador** secção para editar os atributos a serem enviados para a aplicação no SAML token, quando os utilizadores iniciam sessão.

   Para adicionar um atributo:

   * Clique em **adicionar atributo**. Introduza o **Name** e selecione o **valor** na lista pendente.

   * Clique em **guardar.** O atributo novo é apresentado na tabela.

9. Guarde a configuração.

Próxima vez que o utilizador inicia sessão para a aplicação do Azure AD envie o novo atributo na resposta SAML.

## <a name="the-application-expects-a-different-user-identifier-value-or-format"></a>O aplicativo espera um formato ou valor de identificador de utilizador diferente

O início de sessão para a aplicação está a falhar porque a resposta SAML está em falta atributos, tais como o funções ou porque o aplicativo está esperando um formato diferente para o atributo EntityID.

## <a name="add-an-attribute-in-the-azure-ad-application-configuration"></a>Adicione um atributo na configuração da aplicação do Azure AD:

Para alterar o valor do identificador de utilizador, siga estes passos:

1. Abra o [ **portal do Azure** ](https://portal.azure.com/) e inicie sessão como um **Administrador Global** ou **Coadministrador.**

2. Abra o **extensão do Active Directory do Azure** ao clicar em **todos os serviços** na parte superior do menu de navegação esquerdo principal.

3. Escreva **"Azure Active Directory**" na caixa de pesquisa de filtro e selecione o **Azure Active Directory** item.

4. Clique em **aplicações empresariais** no menu de navegação do lado esquerdo do Azure Active Directory.

5. Clique em **todos os aplicativos** para ver uma lista de todas as suas aplicações.

   * Se não vir a aplicação que quer mostrar aqui, utilize o **filtro** na parte superior do **todas as listas de aplicações** e defina o **mostrar** a opção de **todos os Aplicações.**

6. Selecione a aplicação que pretende configurar o início de sessão único.

7. Assim que o aplicativo é carregado, clique nas **início de sessão único** no menu de navegação do lado esquerdo da aplicação.

8. Sob o **atributos de utilizador**, selecione o identificador exclusivo para os seus utilizadores no **identificador de utilizador** lista pendente.

## <a name="change-entityid-user-identifier-format"></a>Altere o formato de EntityID (identificador de utilizador)

Se o aplicativo esperar outro formato para o atributo EntityID. Em seguida, não será possível selecionar o formato de EntityID (identificador de usuário) do Azure AD envia para o aplicativo na resposta após a autenticação de utilizador.

O Azure AD selecionar o formato para o atributo NameID (identificador de utilizador) com base no valor selecionado ou o formato de pedido com o aplicativo em AuthRequest o SAML. Para obter mais informações, visite o artigo [protocolo SAML de início de sessão único](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference#authnrequest) na secção NameIDPolicy.

## <a name="the-application-expects-a-different-signature-method-for-the-saml-response"></a>O aplicativo espera que um método de assinatura diferente para a resposta SAML

Para alterar que partes do SAML token são assinados digitalmente pelo Azure Active Directory. Siga estes passos.

1. Abra o [ **portal do Azure** ](https://portal.azure.com/) e inicie sessão como um **Administrador Global** ou **Coadministrador.**

2. Abra o **extensão do Active Directory do Azure** ao clicar em **todos os serviços** na parte superior do menu de navegação esquerdo principal.

3. Escreva **"Azure Active Directory**" na caixa de pesquisa de filtro e selecione o **Azure Active Directory** item.

4. Clique em **aplicações empresariais** no menu de navegação do lado esquerdo do Azure Active Directory.

5. Clique em **todos os aplicativos** para ver uma lista de todas as suas aplicações.

   * Se não vir a aplicação que quer mostrar aqui, utilize o **filtro** na parte superior do **todas as listas de aplicações** e defina o **mostrar** a opção de **todos os Aplicações.**

6. Selecione a aplicação que pretende configurar o início de sessão único.

7. Assim que o aplicativo é carregado, clique nas **início de sessão único** no menu de navegação do lado esquerdo da aplicação.

8. Clique em **Mostrar definições de assinatura de certificado avançadas** sob a **certificado de assinatura SAML** secção.

9. Selecione o adequado **assinatura opção** esperado pela aplicação:

   * Assinar resposta SAML

   * Assinar asserção e resposta SAML

   * Assinar asserção SAML

Próxima vez que o utilizador inicia sessão para a aplicação do Azure AD iniciar sessão a parte da resposta SAML selecionada.

## <a name="the-application-expects-the-signing-algorithm-to-be-sha-1"></a>O aplicativo espera que o algoritmo de assinatura para ser SHA-1

Por predefinição, o Azure AD assina o token SAML utilizando o algoritmo de segurança mais. Não é recomendado alterar o algoritmo de início de sessão para SHA-1, exceto se exigido pelo aplicativo.

Para alterar o algoritmo de assinatura, siga estes passos:

1. Abra o [ **portal do Azure** ](https://portal.azure.com/) e inicie sessão como um **Administrador Global** ou **Coadministrador.**

2. Abra o **extensão do Active Directory do Azure** ao clicar em **todos os serviços** na parte superior do menu de navegação esquerdo principal.

3. Escreva **"Azure Active Directory**" na caixa de pesquisa de filtro e selecione o **Azure Active Directory** item.

4. Clique em **aplicações empresariais** no menu de navegação do lado esquerdo do Azure Active Directory.

5. Clique em **todos os aplicativos** para ver uma lista de todas as suas aplicações.

   * Se não vir a aplicação que quer mostrar aqui, utilize o **filtro** na parte superior do **todas as listas de aplicações** e defina o **mostrar** a opção de **todos os Aplicações.**

6. Selecione a aplicação que pretende configurar o início de sessão único.

7. Assim que o aplicativo é carregado, clique nas **início de sessão único** no menu de navegação do lado esquerdo da aplicação.

8. Clique em **Mostrar definições de assinatura de certificado avançadas** sob a **certificado de assinatura SAML** secção.

9. Selecione SHA-1, o **algoritmo de assinatura**.

Próxima vez que o utilizador inicia sessão aplicação, o Azure AD assinar o token SAML com o algoritmo SHA-1.

## <a name="next-steps"></a>Passos Seguintes
[Como depurar baseado em SAML início de sessão único para aplicações no Azure Active Directory](https://azure.microsoft.com/documentation/articles/active-directory-saml-debugging)
