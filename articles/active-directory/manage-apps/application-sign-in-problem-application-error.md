---
title: A mensagem de erro aparece na página da aplicação depois de iniciar sôm no | Microsoft Docs
description: Como resolver problemas com o Azure AD entrar quando a aplicação retorna uma mensagem de erro.
services: active-directory
documentationcenter: ''
author: kenwith
manager: daveba
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 07/11/2017
ms.author: kenwith
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: c96209f33491645510d8592997c418472d4f227c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "99258817"
---
# <a name="an-app-page-shows-an-error-message-after-the-user-signs-in"></a>A página de uma aplicação apresenta uma mensagem de erro após o utilizador iniciar sessão

Neste cenário, o Azure Ative Directory (Azure AD) assina o utilizador. Mas a aplicação apresenta uma mensagem de erro e não permite que o utilizador termine o fluxo de entrada. O problema é que a aplicação não aceitou a resposta que a Azure AD emitiu.

Existem várias razões possíveis para a aplicação não aceitar a resposta do Azure AD. Se a mensagem de erro não identificar claramente o que falta na resposta, tente o seguinte:

-   Se a aplicação for a galeria AD AZure, verifique se seguiu os passos em [Como depurar o único sign-on baseado em SAML para aplicações em Azure AD](./debug-saml-sso-issues.md).

-   Use uma ferramenta como [o Fiddler](https://www.telerik.com/fiddler) para capturar o pedido, resposta e ficha SAML.

-   Envie a resposta SAML ao fornecedor de aplicações e pergunte-lhes o que falta.

## <a name="attributes-are-missing-from-the-saml-response"></a>Faltam atributos da resposta SAML

Para adicionar um atributo na configuração AD AZure que será enviado na resposta AD Azure, siga estes passos:

1. Abra o [**portal Azure**](https://portal.azure.com/) e inscreva-se como administrador global ou coadministrador.

2. Na parte superior do painel de navegação do lado esquerdo, selecione **Todos os serviços** para abrir a extensão AD AZure.

3. Digite **O Diretório Ativo Azure** na caixa de pesquisa de filtros e, em seguida, selecione **O Diretório Ativo Azure**.

4. Selecione **Aplicações Empresariais** no painel de navegação AD Azure.

5. Selecione **Todas as Aplicações** para ver uma lista das suas aplicações.

   > [!NOTE]
   > Se não vir a aplicação que pretende, utilize o controlo **do Filtro** no topo da Lista de **Todas as Aplicações.** Desa estada a opção **"Todas** as Aplicações".

6. Selecione a aplicação que pretende configurar para uma única sação.

7. Depois de carregar a aplicação, selecione **Single sign-on no** painel de navegação.

8. Na secção **Atributos** do Utilizador, selecione **Ver e edite todos os outros atributos do utilizador**. Aqui pode alterar quais os atributos a enviar para a app no token SAML quando os utilizadores iniciarem súm.

   Para adicionar um atributo:

   1. **Selecione Adicionar atributo**. Introduza o **Nome** e selecione o **Valor** da lista de drop-down.

   1.  Selecione **Guardar**. Verá o novo atributo na mesa.

9. Guarde a configuração.

   Da próxima vez que o utilizador entrar na app, o Azure AD enviará o novo atributo na resposta SAML.

## <a name="the-app-doesnt-identify-the-user"></a>A aplicação não identifica o utilizador

A inscrição na aplicação falha porque falta uma resposta SAML como um papel. Ou falha porque a aplicação espera um formato ou valor diferente para o atributo **NameID** (User Identifier).

Se estiver a utilizar o fornecimento automatizado de [utilizadores Azure AD](../app-provisioning/user-provisioning.md) para criar, manter e remover os utilizadores na aplicação, verifique se o utilizador foi aprovisionado na aplicação SaaS. Para mais informações, consulte [Nenhum utilizadores está a ser atetado a uma aplicação da Galeria AD Azure.](../app-provisioning/application-provisioning-config-problem-no-users-provisioned.md)

## <a name="add-an-attribute-to-the-azure-ad-app-configuration"></a>Adicione um atributo à configuração da aplicação AD Azure

Para alterar o valor do Identificador de Utilizador, siga estes passos:

1. Abra o [**portal Azure**](https://portal.azure.com/) e inscreva-se como administrador global ou coadministrador.

2. Selecione **Todos os serviços** na parte superior do painel de navegação do lado esquerdo para abrir a extensão AD AZure.

3. Digite **O Diretório Ativo Azure** na caixa de pesquisa de filtros e, em seguida, selecione **O Diretório Ativo Azure**.

4. Selecione **Aplicações Empresariais** no painel de navegação AD Azure.

5. Selecione **Todas as Aplicações** para ver uma lista das suas aplicações.

   > [!NOTE]
   > Se não vir a aplicação que pretende, utilize o controlo **do Filtro** no topo da Lista de **Todas as Aplicações.** Desa estada a opção **"Todas** as Aplicações".

6. Selecione a aplicação que pretende configurar para SSO.

7. Depois de carregar a aplicação, selecione **Single sign-on no** painel de navegação.

8. Nos **atributos do Utilizador,** selecione o identificador único para o utilizador a partir da lista de drop-down **do User Identifier.**

## <a name="change-the-nameid-format"></a>Alterar o formato NameID

Se a aplicação espera outro formato para o atributo **NameID** (User Identifier), consulte o [nome de EdiçãoID](../develop/active-directory-saml-claims-customization.md#editing-nameid) para alterar o formato NameID.

A Azure AD seleciona o formato para o atributo **NameID** (User Identifier) com base no valor selecionado ou no formato que é solicitado pela aplicação no AuthRequest SAML. Para mais informações, consulte a secção "NameIDPolicy" do [protocolo SAML de assinatura única.](../develop/single-sign-on-saml-protocol.md#nameidpolicy)

## <a name="the-app-expects-a-different-signature-method-for-the-saml-response"></a>A aplicação espera um método de assinatura diferente para a resposta SAML

Para alterar quais as partes do token SAML que são assinadas digitalmente pela Azure AD, siga estes passos:

1. Abra o [portal Azure](https://portal.azure.com/) e inscreva-se como administrador global ou coadministrador.

2. Selecione **Todos os serviços** na parte superior do painel de navegação do lado esquerdo para abrir a extensão AD AZure.

3. Digite **O Diretório Ativo Azure** na caixa de pesquisa de filtros e, em seguida, selecione **O Diretório Ativo Azure**.

4. Selecione **Aplicações Empresariais** no painel de navegação AD Azure.

5. Selecione **Todas as Aplicações** para ver uma lista das suas aplicações.

   > [!NOTE]
   > Se não vir a aplicação que pretende, utilize o controlo **do Filtro** no topo da Lista de **Todas as Aplicações.** Desa estada a opção **"Todas** as Aplicações".

6. Selecione a aplicação que pretende configurar para uma única sação.

7. Depois das cargas de aplicação, selecione **'Único' no** painel de navegação.

8. Ao abrigo **do Certificado de Assinatura SAML,** selecione Mostrar  **definições avançadas de assinatura de certificados**.

9. Selecione a **Opção de Assinatura** que a aplicação espera entre estas opções:

   * **Assine a resposta SAML**
   * **Assine resposta e afirmação do SAML**
   * **Assine a afirmação do SAML**

   Da próxima vez que o utilizador entrar na app, o Azure AD assinará a parte da resposta SAML que selecionou.

## <a name="the-app-expects-the-sha-1-signing-algorithm"></a>A aplicação espera o algoritmo de assinatura SHA-1

Por padrão, a Azure AD assina o token SAML usando o algoritmo mais seguro. Recomendamos que não altere o algoritmo de assinatura para *SHA-1* a menos que a aplicação exija SHA-1.

Para alterar o algoritmo de assinatura, siga estes passos:

1. Abra o [portal Azure](https://portal.azure.com/) e inscreva-se como administrador global ou coadministrador.

2. Selecione **Todos os serviços** na parte superior do painel de navegação do lado esquerdo para abrir a extensão AD AZure.

3. Digite **O Diretório Ativo Azure** na caixa de pesquisa de filtros e, em seguida, selecione **O Diretório Ativo Azure**.

4. Selecione **Aplicações Empresariais** no painel de navegação AD Azure.

5. Selecione **Todas as Aplicações** para ver uma lista das suas aplicações.

   > [!NOTE]
   > Se não vir a aplicação que pretende, utilize o controlo **do Filtro** no topo da Lista de **Todas as Aplicações.** Desa estada a opção **"Todas** as Aplicações".

6. Selecione a aplicação que pretende configurar para um único sign-on.

7. Depois de carregar a aplicação, selecione **Single sign-on** a partir do painel de navegação no lado esquerdo da aplicação.

8. Ao abrigo **do Certificado de Assinatura SAML,** selecione Mostrar **definições avançadas de assinatura de certificados**.

9. Selecione **SHA-1** como algoritmo **de assinatura.**

   Da próxima vez que o utilizador entrar na aplicação, o Azure AD assinará o token SAML utilizando o algoritmo SHA-1.

## <a name="next-steps"></a>Passos seguintes
[Como depurar o único sign-on baseado em SAML para aplicações em Azure AD](./debug-saml-sso-issues.md).