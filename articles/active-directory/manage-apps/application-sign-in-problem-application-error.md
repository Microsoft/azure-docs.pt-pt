---
title: A mensagem de erro aparece na página da aplicação depois de iniciar sessão Microsoft Docs
description: Como resolver problemas com o Anúncio Azure iniciar o início quando a aplicação devolve uma mensagem de erro.
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
ms.openlocfilehash: 9b8d20b31e96973a492355f0515d0532deea0ac9
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/13/2020
ms.locfileid: "77185482"
---
# <a name="an-app-page-shows-an-error-message-after-the-user-signs-in"></a>Uma página de aplicativo mostra uma mensagem de erro após o utilizador entrar

Neste cenário, o Azure Ative Directory (Azure AD) inscreve o utilizador. Mas a aplicação exibe uma mensagem de erro e não permite que o utilizador termine o fluxo de entrada. O problema é que a app não aceitou a resposta que a Azure AD emitiu.

Existem várias razões possíveis para a aplicação não ter aceitado a resposta da Azure AD. Se a mensagem de erro não identificar claramente o que falta na resposta, tente o seguinte:

-   Se a aplicação for a galeria Azure AD, verifique se seguiu os passos de [Como depurar o único sign-on baseado em SAML para aplicações em Azure AD](https://azure.microsoft.com/documentation/articles/active-directory-saml-debugging).

-   Utilize uma ferramenta como [o Fiddler](https://www.telerik.com/fiddler) para capturar o pedido, resposta e ficha da SAML.

-   Envie a resposta SAML ao fornecedor de aplicações e pergunte-lhes o que falta.

## <a name="attributes-are-missing-from-the-saml-response"></a>Faltam atributos da resposta SAML

Para adicionar um atributo na configuração azure aD que será enviada na resposta da AD Azure, siga estes passos:

1. Abra o [**portal Azure**](https://portal.azure.com/) e inscreva-se como administrador global ou coadministrador.

2. Na parte superior do painel de navegação do lado esquerdo, selecione **Todos os serviços** para abrir a extensão Azure AD.

3. Digite o **Diretório Ativo Do** tipo Azure na caixa de pesquisa de filtros e, em seguida, selecione **Azure Ative Directory**.

4. Selecione **Aplicações Empresariais** no painel de navegação Azure AD.

5. Selecione **Todas as Aplicações** para ver uma lista das suas apps.

   > [!NOTE]
   > Se não vir a aplicação que deseja, utilize o controlo **filter** no topo da Lista de **Todas as Aplicações**. Detete a opção **Show** para "Todas as Aplicações".

6. Selecione a aplicação que pretende configurar para um único início de sessão.

7. Depois de a aplicação carregar, selecione **um único sinal no** painel de navegação.

8. Na secção **Atributos do Utilizador,** selecione **'Ver' e editar todos os outros atributos do utilizador**. Aqui pode alterar quais os atributos a enviar para a aplicação no token SAML quando os utilizadores iniciarem o seu insessão.

   Para adicionar um atributo:

   1. **Selecione Adicionar atributo**. Introduza o **Nome**e selecione o **Valor** da lista de lançamentos.

   1.  Selecione **Guardar**. Verá o novo atributo na mesa.

9. Guarde a configuração.

   Da próxima vez que o utilizador entrar na aplicação, o Azure AD enviará o novo atributo na resposta SAML.

## <a name="the-app-doesnt-identify-the-user"></a>A aplicação não identifica o utilizador

A inscrição na aplicação falha porque a resposta SAML está a faltar um atributo, como um papel. Ou falha porque a aplicação espera um formato ou valor diferente para o atributo **NameID** (Identificador de Utilizador).

Se estiver a utilizar o fornecimento automatizado de utilizadores da [Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning) para criar, manter e remover utilizadores na aplicação, verifique se o utilizador foi aprovisionado na aplicação SaaS. Para mais informações, não forneça utilizadores a uma aplicação da [Galeria AD Azure.](../app-provisioning/application-provisioning-config-problem-no-users-provisioned.md)

## <a name="add-an-attribute-to-the-azure-ad-app-configuration"></a>Adicione um atributo à configuração da aplicação Azure AD

Para alterar o valor do Identificador de Utilizador, siga estes passos:

1. Abra o [**portal Azure**](https://portal.azure.com/) e inscreva-se como administrador global ou coadministrador.

2. Selecione **Todos os serviços** na parte superior do painel de navegação do lado esquerdo para abrir a extensão Azure AD.

3. Digite o **Diretório Ativo Do** tipo Azure na caixa de pesquisa de filtros e, em seguida, selecione **Azure Ative Directory**.

4. Selecione **Aplicações Empresariais** no painel de navegação Azure AD.

5. Selecione **Todas as Aplicações** para ver uma lista das suas apps.

   > [!NOTE]
   > Se não vir a aplicação que deseja, utilize o controlo **filter** no topo da Lista de **Todas as Aplicações**. Detete a opção **Show** para "Todas as Aplicações".

6. Selecione a aplicação que pretende configurar para SSO.

7. Depois de a aplicação carregar, selecione **um único sinal no** painel de navegação.

8. Em **atributos do Utilizador,** selecione o identificador único para o utilizador da lista de drop-down do Identificador do **Utilizador.**

## <a name="change-the-nameid-format"></a>Alterar o formato NameID

Se a aplicação espera outro formato para o atributo **NameID** (Identificador de Utilizador), consulte o [nome de edição ID](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization#editing-nameid) para alterar o formato NameID.

O Azure AD seleciona o formato para o atributo **NameID** (Identificador de utilizador) com base no valor selecionado ou no formato solicitado pela aplicação no AuthRequest SAML. Para mais informações, consulte a secção "NameIDPolicy" do [protocolo SAML](https://docs.microsoft.com/azure/active-directory/develop/single-sign-on-saml-protocol#nameidpolicy)de inscrição única .

## <a name="the-app-expects-a-different-signature-method-for-the-saml-response"></a>A aplicação espera um método de assinatura diferente para a resposta SAML

Para alterar quais as partes do token SAML assinadas digitalmente pela Azure AD, siga estes passos:

1. Abra o [portal Azure](https://portal.azure.com/) e inscreva-se como administrador global ou coadministrador.

2. Selecione **Todos os serviços** na parte superior do painel de navegação do lado esquerdo para abrir a extensão Azure AD.

3. Digite o **Diretório Ativo Do** tipo Azure na caixa de pesquisa de filtros e, em seguida, selecione **Azure Ative Directory**.

4. Selecione **Aplicações Empresariais** no painel de navegação Azure AD.

5. Selecione **Todas as Aplicações** para ver uma lista das suas apps.

   > [!NOTE]
   > Se não vir a aplicação que deseja, utilize o controlo **filter** no topo da Lista de **Todas as Aplicações**. Detete a opção **Show** para "Todas as Aplicações".

6. Selecione a aplicação que pretende configurar para um único início de sessão.

7. Depois de a aplicação carregar, selecione **um único sinal no** painel de navegação.

8. Sob o Certificado de **Assinatura SAML,** selecione **Mostrar definições avançadas**de assinatura de certificado .

9. Selecione a **Opção de Contratação** que a aplicação espera entre estas opções:

   * **Assinar resposta SAML**
   * **Assinar resposta e afirmação SAML**
   * **Sign a afirmação do SAML**

   Da próxima vez que o utilizador iniciar sessão na aplicação, o Azure AD assinará a parte da resposta SAML que selecionou.

## <a name="the-app-expects-the-sha-1-signing-algorithm"></a>A aplicação espera o algoritmo de assinatura SHA-1

Por padrão, a AD Azure assina o símbolo SAML utilizando o algoritmo mais seguro. Recomendamos que não altere o algoritmo de assinatura para *SHA-1* a menos que a aplicação exija SHA-1.

Para alterar o algoritmo de assinatura, siga estes passos:

1. Abra o [portal Azure](https://portal.azure.com/) e inscreva-se como administrador global ou coadministrador.

2. Selecione **Todos os serviços** na parte superior do painel de navegação do lado esquerdo para abrir a extensão Azure AD.

3. Digite o **Diretório Ativo Do** tipo Azure na caixa de pesquisa de filtros e, em seguida, selecione **Azure Ative Directory**.

4. Selecione **Aplicações Empresariais** no painel de navegação Azure AD.

5. Selecione **Todas as Aplicações** para ver uma lista das suas aplicações.

   > [!NOTE]
   > Se não vir a aplicação que deseja, utilize o controlo **filter** no topo da Lista de **Todas as Aplicações**. Detete a opção **Show** para "Todas as Aplicações".

6. Selecione a aplicação que pretende configurar para um único início de sessão.

7. Depois de a aplicação carregar, selecione **um único sinal no** painel de navegação no lado esquerdo da aplicação.

8. Sob o Certificado de **Assinatura SAML,** selecione **Mostrar definições avançadas**de assinatura de certificado .

9. Selecione **SHA-1** como algoritmo **de assinatura**.

   Da próxima vez que o utilizador entrar na aplicação, o Azure AD assinará o símbolo SAML utilizando o algoritmo SHA-1.

## <a name="next-steps"></a>Passos seguintes
[Como depurar uma única assinatura baseada em SAML para aplicações em Azure AD](https://azure.microsoft.com/documentation/articles/active-directory-saml-debugging).
