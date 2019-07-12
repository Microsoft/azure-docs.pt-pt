---
title: Mensagem de erro é apresentado na página de aplicação depois de iniciar sessão | Documentos da Microsoft
description: Como resolver problemas com início de sessão do Azure AD quando a aplicação devolver uma mensagem de erro.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 23e6a3d0b533dccc3c3111382b014907d5c026ab
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/07/2019
ms.locfileid: "67612672"
---
# <a name="an-app-page-shows-an-error-message-after-the-user-signs-in"></a>Uma página da aplicação mostra uma mensagem de erro depois do utilizador inicia sessão

Neste cenário, o Azure Active Directory (Azure AD) inicia o usuário sessão. Mas o aplicativo exibe uma mensagem de erro e não permite que o usuário concluir o fluxo de início de sessão. O problema é que a aplicação não aceita a resposta que emitiu do Azure AD.

Existem vários motivos possíveis, por que a aplicação não aceitou a resposta do Azure AD. Se a mensagem de erro não identificar claramente o que está em falta na resposta, experimente o seguinte:

-   Se a aplicação da galeria do Azure AD, certifique-se de que seguiu os passos em [como depurar baseado em SAML início de sessão único para aplicações no Azure AD](https://azure.microsoft.com/documentation/articles/active-directory-saml-debugging).

-   Usar uma ferramenta como [Fiddler](https://www.telerik.com/fiddler) para capturar o pedido SAML, a resposta e o token.

-   Enviar a resposta SAML para o fornecedor da aplicação e peça-lhe o que está em falta.

## <a name="attributes-are-missing-from-the-saml-response"></a>Atributos estão em falta da resposta SAML

Para adicionar um atributo na configuração do Azure AD que será enviada na resposta do Azure AD, siga estes passos:

1. Abra o [ **portal do Azure** ](https://portal.azure.com/) e inicie sessão como um administrador global ou coadministrador.

2. Na parte superior do painel de navegação no lado esquerdo, selecione **todos os serviços** para abrir a extensão do Azure AD.

3. Tipo **do Azure Active Directory** na caixa de pesquisa de filtro e, em seguida, selecione **Azure Active Directory**.

4. Selecione **aplicações empresariais** no painel de navegação do Azure AD.

5. Selecione **todos os aplicativos** para ver uma lista das suas aplicações.

   > [!NOTE]
   > Se não vir a aplicação que pretende, utilize o **filtro** na parte superior do **todas as listas de aplicativos**. Definir o **mostrar** opção para "Todas as aplicações".

6. Selecione a aplicação que pretende configurar para início de sessão único.

7. Depois da aplicação for carregada, selecione **início de sessão único** no painel de navegação.

8. Na **atributos de utilizador** secção, selecione **ver e editar todos os outros atributos de utilizador**. Aqui, pode alterar os atributos a enviar para a aplicação no SAML token, quando os utilizadores iniciam sessão.

   Para adicionar um atributo:

   1. Selecione **adicionar atributo**. Introduza o **Name**e selecione o **valor** na lista pendente.

   1.  Selecione **Guardar**. Verá o novo atributo na tabela.

9. Guarde a configuração.

   Da próxima vez que o utilizador inicia sessão na aplicação, o Azure AD irá enviar o novo atributo na resposta SAML.

## <a name="the-app-doesnt-identify-the-user"></a>A aplicação não identifica o utilizador

Iniciar sessão aplicação falha porque a resposta SAML está em falta um atributo, como uma função. Falha porque a aplicação espera um formato diferente ou o valor para o **NameID** atributo (identificador de utilizador).

Se estiver a utilizar [do Azure AD aprovisionamento automatizado do utilizador](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning) para criar, manter e remover utilizadores na aplicação, certifique-se de que o utilizador tiver sido aprovisionado para a aplicação SaaS. Para obter mais informações, consulte [os utilizadores não estão a ser aprovisionados para um aplicativo de galeria do Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-config-problem-no-users-provisioned).

## <a name="add-an-attribute-to-the-azure-ad-app-configuration"></a>Adicionar um atributo para a configuração de aplicações do Azure AD

Para alterar o valor do identificador de utilizador, siga estes passos:

1. Abra o [ **portal do Azure** ](https://portal.azure.com/) e inicie sessão como um administrador global ou coadministrador.

2. Selecione **todos os serviços** na parte superior do painel de navegação no lado esquerdo para abrir a extensão do Azure AD.

3. Tipo **do Azure Active Directory** na caixa de pesquisa de filtro e, em seguida, selecione **Azure Active Directory**.

4. Selecione **aplicações empresariais** no painel de navegação do Azure AD.

5. Selecione **todos os aplicativos** para ver uma lista das suas aplicações.

   > [!NOTE]
   > Se não vir a aplicação que pretende, utilize o **filtro** na parte superior do **todas as listas de aplicativos**. Definir o **mostrar** opção para "Todas as aplicações".

6. Selecione a aplicação que pretende configurar para SSO.

7. Depois da aplicação for carregada, selecione **início de sessão único** no painel de navegação.

8. Sob **atributos de utilizador**, selecione o identificador exclusivo para o utilizador a **identificador de utilizador** na lista pendente.

## <a name="change-the-nameid-format"></a>Alterar o formato NameID

Se o aplicativo esperar outro formato para o **NameID** atributo (identificador de utilizador), veja [nameID de edição](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization#editing-nameid) para alterar o formato NameID.

O Azure AD seleciona o formato para o **NameID** atributo (identificador de utilizador) com base no formato que é solicitado pela aplicação no AuthRequest de SAML ou o valor que está selecionado. Para obter mais informações, consulte a secção "NameIDPolicy" [único início de sessão do protocolo SAML](https://docs.microsoft.com/azure/active-directory/develop/single-sign-on-saml-protocol#nameidpolicy).

## <a name="the-app-expects-a-different-signature-method-for-the-saml-response"></a>A aplicação espera que um método de assinatura diferente para a resposta SAML

Para alterar as partes do SAML token são assinadas digitalmente pelo Azure AD, siga estes passos:

1. Abra o [portal do Azure](https://portal.azure.com/) e inicie sessão como um administrador global ou coadministrador.

2. Selecione **todos os serviços** na parte superior do painel de navegação no lado esquerdo para abrir a extensão do Azure AD.

3. Tipo **do Azure Active Directory** na caixa de pesquisa de filtro e, em seguida, selecione **Azure Active Directory**.

4. Selecione **aplicações empresariais** no painel de navegação do Azure AD.

5. Selecione **todos os aplicativos** para ver uma lista das suas aplicações.

   > [!NOTE]
   > Se não vir a aplicação que pretende, utilize o **filtro** na parte superior do **todas as listas de aplicativos**. Definir o **mostrar** opção para "Todas as aplicações".

6. Selecione a aplicação que pretende configurar para início de sessão único.

7. Depois da aplicação for carregada, selecione **início de sessão único** no painel de navegação.

8. Sob **certificado de assinatura SAML**, selecione **Mostrar definições de assinatura de certificado avançadas**.

9. Selecione o **assinatura opção** que a aplicação espera entre estas opções:

   * **Assinar resposta SAML**
   * **Assinar asserção e resposta SAML**
   * **Assinar asserção SAML**

   Da próxima vez que o utilizador inicia sessão na aplicação do Azure AD assinará a parte da resposta SAML que selecionou.

## <a name="the-app-expects-the-sha-1-signing-algorithm"></a>A aplicação espera que o algoritmo de assinatura de SHA-1

Por predefinição, o Azure AD assina o token SAML, utilizando o algoritmo mais segura. Recomendamos que não altere o algoritmo de assinatura *SHA-1* , a menos que a aplicação requer o SHA-1.

Para alterar o algoritmo de assinatura, siga estes passos:

1. Abra o [portal do Azure](https://portal.azure.com/) e inicie sessão como um administrador global ou coadministrador.

2. Selecione **todos os serviços** na parte superior do painel de navegação no lado esquerdo para abrir a extensão do Azure AD.

3. Tipo **do Azure Active Directory** na caixa de pesquisa de filtro e, em seguida, selecione **Azure Active Directory**.

4. Selecione **aplicações empresariais** no painel de navegação do Azure AD.

5. Selecione **todos os aplicativos** para ver uma lista das suas aplicações.

   > [!NOTE]
   > Se não vir a aplicação que pretende, utilize o **filtro** na parte superior do **todas as listas de aplicativos**. Definir o **mostrar** opção para "Todas as aplicações".

6. Selecione a aplicação que pretende configurar para início de sessão único.

7. Depois da aplicação for carregada, selecione **início de sessão único** no painel de navegação no lado esquerdo da aplicação.

8. Sob **certificado de assinatura SAML**, selecione **Mostrar definições de assinatura de certificado avançadas**.

9. Selecione **SHA-1** como o **algoritmo de assinatura**.

   Da próxima vez que o utilizador inicia sessão na aplicação do Azure AD assinar o token SAML, utilizando o algoritmo SHA-1.

## <a name="next-steps"></a>Passos Seguintes
[Como depurar baseado em SAML início de sessão único para aplicações no Azure AD](https://azure.microsoft.com/documentation/articles/active-directory-saml-debugging).
