---
title: Como configurar a palavra-passe início de sessão único para um applicationn externas à Galeria | Documentos da Microsoft
description: Como configurar uma aplicação de externas à Galeria personalizada para segura baseada em palavra-passe de início de sessão único, quando não está listado na Galeria de aplicações do Azure AD
services: active-directory
author: CelesteDG
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 11/12/2018
ms.author: celested
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0c77ac32bd6079a8583cc6ea91cb8e214e5b31ac
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58095556"
---
# <a name="how-to-configure-password-single-sign-on-for-a-non-gallery-application"></a>Como configurar a palavra-passe início de sessão único para uma aplicação de externas à Galeria

Além das escolhas encontradas dentro da Galeria de aplicações do Azure AD, tem também a opção de adicionar uma **aplicações externas à galeria** quando a aplicação que quer não está listada aqui. Através desta funcionalidade, pode adicionar qualquer aplicação que já existe na sua organização ou qualquer aplicativo de terceiros que poderá utilizar de um fornecedor que não esteja já faz parte dos [Galeria de aplicações do Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

Depois de adicionar uma aplicação de externas à galeria, em seguida, pode configurar a método de início de início de sessão único esta aplicação utiliza ao selecionar o **início de sessão único** item de navegação num aplicativo empresarial a [deportaldoAzure](https://portal.azure.com/).

Um dos início de sessão único métodos disponíveis para é o [baseado em palavra-passe de início de sessão único](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) opção. Com o **adicionar uma aplicação de externas à galeria** experiência, pode integrar qualquer aplicativo que processa um nome de utilizador baseada em HTML e a palavra-passe de entrada campo, mesmo que não está no nosso conjunto de aplicações previamente integradas.

A maneira como isso funciona é uma tecnologia que faz parte da extensão do painel de acesso que permite-nos campos de entrada de nome de utilizador e palavra-passe de deteção automática, armazená-los de forma segura para a instância da aplicação específico de recorte de página. Em seguida, reproduza com segurança nomes de utilizador e palavras-passe para esses campos quando um utilizador navega para essa aplicação no painel de acesso de aplicações.

Esta é uma ótima maneira de começar a integrar rapidamente qualquer tipo de aplicativo para o Azure AD e permite-lhe:

-   Integre **qualquer aplicativo no mundo** com o seu inquilino do Azure AD, desde que compõe um campo de entrada HTML nome de utilizador e palavra-passe

-   Ativar **início de sessão único para os seus utilizadores** ao armazenar e reproduzir os nomes de utilizador e palavras-passe para a aplicação de forma segura que já integrado com o Azure AD

-   **Deteção automática de entrada** campos para qualquer aplicação e permitem-lhe detetar manualmente esses campos com a extensão de Browser do painel de acesso, no caso de deteção automática não encontrá-los

-   **Suporte a aplicativos que necessitam de vários campos de início de sessão** para aplicações que requerem mais do que apenas nome de utilizador e palavra-passe campos para iniciar sessão

-   **Personalizar as etiquetas** os nome de utilizador e palavra-passe dos campos de entrada nos seus utilizadores verão a [painel de acesso de aplicação](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) quando eles introduzam as respetivas credenciais

-   Permitir que sua **usuários** para fornecer seus próprios nomes de utilizador e palavras-passe para as contas de aplicação existentes que estão escrevendo manualmente no [painel de acesso de aplicação](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

-   Permitir que um **membro do grupo de negócio** para especificar os nomes de utilizador e palavras-passe atribuídas a um utilizador utilizando o [acesso da aplicação Self-Service](https://docs.microsoft.com/azure/active-directory/active-directory-self-service-application-access) funcionalidade

-   Permitir que um **administrador** para especificar os nomes de utilizador e palavras-passe atribuídas a um utilizador ao utilizar a funcionalidade das credenciais de atualização quando atribuir um utilizador a uma aplicação

-   Permitir que um **administrador** para especificar o nome de utilizador partilhada ou a palavra-passe utilizada por um grupo de pessoas com as credenciais de atualização de funcionalidades quando [atribuir um grupo a uma aplicação](#assign-an-application-to-a-group-directly)

A seguinte secção descreve como pode permitir [baseado em palavra-passe de início de sessão único](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) a qualquer aplicativo que adicionar usando o **adicionar uma aplicação de externas à galeria** experiência.

## <a name="overview-of-steps-required"></a>Descrição geral dos passos necessários

Para configurar uma aplicação da galeria do Azure AD, que tem de:

-   [Adicionar uma aplicação de externas à Galeria](#add-a-non-gallery-application)

-   [Configurar a aplicação para a palavra-passe início de sessão único](#configure-the-application-for-password-single-sign-on)

-   Atribuir a aplicação a um utilizador ou um grupo

    -   [Atribuir um utilizador a uma aplicação diretamente](#assign-a-user-to-an-application-directly)

    -   [Atribuir diretamente uma aplicação a um grupo](#assign-an-application-to-a-group-directly)

## <a name="add-a-non-gallery-application"></a>Adicionar uma aplicação de externas à Galeria

Para adicionar uma aplicação da galeria do Azure AD, siga estes passos:

1.  Abra o [portal do Azure](https://portal.azure.com) e inicie sessão como um **Administrador Global** ou **coadministrador**

2.  Abra o **extensão do Active Directory do Azure** ao clicar em **todos os serviços** na parte superior do menu de navegação esquerdo principal.

3.  Escreva **"Azure Active Directory**" na caixa de pesquisa de filtro e selecione o **Azure Active Directory** item.

4.  Clique em **aplicações empresariais** no menu de navegação do lado esquerdo do Azure Active Directory.

5.  Clique nas **Add** botão no canto superior direito a **aplicações empresariais** painel.

6.  Clique em **aplicação da Galeria não**.

7.  Introduza o nome da sua aplicação no **nome** caixa de texto. Selecione **adicionar.**

Após um curto período, poderá ver o painel de configuração do aplicativo.

## <a name="configure-the-application-for-password-single-sign-on"></a>Configurar a aplicação para a palavra-passe início de sessão único

Para configurar o início de sessão único para uma aplicação, siga estes passos:

1. Abra o [ **portal do Azure** ](https://portal.azure.com/) e inicie sessão como um **Administrador Global** ou **Coadministrador.**

2. Abra o **extensão do Active Directory do Azure** ao clicar em **todos os serviços** na parte superior do menu de navegação esquerdo principal.

3. Escreva **"Azure Active Directory**" na caixa de pesquisa de filtro e selecione o **Azure Active Directory** item.

4. Clique em **aplicações empresariais** no menu de navegação do lado esquerdo do Azure Active Directory.

5. Clique em **todos os aplicativos** para ver uma lista de todas as suas aplicações.

   * Se não vir a aplicação que quer mostrar aqui, utilize o **filtro** na parte superior do **todas as listas de aplicações** e defina o **mostrar** a opção de **todos os Aplicações.**

6. Selecione a aplicação que pretende configurar o início de sessão único.

7. Assim que o aplicativo é carregado, clique nas **início de sessão único** no menu de navegação do lado esquerdo da aplicação.

8. Selecione o modo **baseado em palavra-passe de início de sessão.**

9. Introduza o **URL de início de sessão**. Este é o URL em que os utilizadores introduzirem o respetivo nome de utilizador e palavra-passe para iniciar sessão no. Certifique-se de que os campos de início de sessão são visíveis no URL.

10. Atribua utilizadores à aplicação.

11. Além disso, também pode fornecer as credenciais em nome do utilizador ao selecionar as linhas dos utilizadores e clicar em **as credenciais de atualização** e introduzindo o nome de utilizador e palavra-passe em nome dos utilizadores. Caso contrário, ser pedido aos utilizadores para introduzir as credenciais durante a inicialização propriamente ditas.


## <a name="assign-a-user-to-an-application-directly"></a>Atribuir um utilizador a uma aplicação diretamente

Para atribuir diretamente um ou mais utilizadores a uma aplicação, siga estes passos:

1. Abra o [ **portal do Azure** ](https://portal.azure.com/) e inicie sessão como um **Administrador Global.**

2. Abra o **extensão do Active Directory do Azure** ao clicar em **todos os serviços** na parte superior do menu de navegação esquerdo principal.

3. Escreva **"Azure Active Directory**" na caixa de pesquisa de filtro e selecione o **Azure Active Directory** item.

4. Clique em **aplicações empresariais** no menu de navegação do lado esquerdo do Azure Active Directory.

5. Clique em **todos os aplicativos** para ver uma lista de todas as suas aplicações.

   * Se não vir a aplicação que quer mostrar aqui, utilize o **filtro** na parte superior do **todas as listas de aplicações** e defina o **mostrar** a opção de **todos os Aplicações.**

6. Selecione a aplicação que pretende atribuir um utilizador a partir da lista.

7. Assim que o aplicativo é carregado, clique em **utilizadores e grupos** no menu de navegação do lado esquerdo da aplicação.

8. Para abrir o **adicionar atribuição** painel, clique nas **Add** botão na parte superior do **utilizadores e grupos** lista.

9. Clique nas **utilizadores e grupos** Seletor da **adicionar atribuição** painel.

10. Escreva o **nome completo** ou **endereço de e-mail** do utilizador estiver interessado em atribuir para o **procurar por nome ou endereço de e-mail** caixa de pesquisa.

11. Paire o rato sobre o **usuário** na lista para revelar uma **caixa de verificação**. Clique na caixa de verificação junto a fotografia do perfil do usuário ou a logótipo para adicionar o utilizador para o **selecionados** lista.

12. **Opcional:** Se quiser **adicionar mais de um usuário**, tipo em outro **nome completo** ou **endereço de e-mail** para o **procurar por nome ou endereço de e-mail** caixa de pesquisa e clique na caixa de verificação para adicionar este utilizador para o **selecionados** lista.

13. Quando tiver terminado de selecionar utilizadores, clique nas **selecione** botão para adicioná-los à lista de utilizadores e grupos que devem ser atribuídos à aplicação.

14. **Opcional:** clique a **selecionar função** Seletor no **adicionar atribuição** painel para selecionar uma função para atribuir aos utilizadores que selecionou.

15. Clique nas **atribuir** botão para atribuir a aplicação aos utilizadores selecionados.

## <a name="assign-an-application-to-a-group-directly"></a>Atribuir diretamente uma aplicação a um grupo

Para atribuir um ou mais grupos diretamente a uma aplicação, siga estes passos:

1. Abra o [ **portal do Azure** ](https://portal.azure.com/) e inicie sessão como um **Administrador Global.**

2. Abra o **extensão do Active Directory do Azure** ao clicar em **todos os serviços** na parte superior do menu de navegação esquerdo principal.

3. Escreva **"Azure Active Directory**" na caixa de pesquisa de filtro e selecione o **Azure Active Directory** item.

4. Clique em **aplicações empresariais** no menu de navegação do lado esquerdo do Azure Active Directory.

5. Clique em **todos os aplicativos** para ver uma lista de todas as suas aplicações.

   * Se não vir a aplicação que quer mostrar aqui, utilize o **filtro** na parte superior do **todas as listas de aplicações** e defina o **mostrar** a opção de **todos os Aplicações.**

6. Selecione a aplicação que pretende atribuir um utilizador a partir da lista.

7. Assim que o aplicativo é carregado, clique em **utilizadores e grupos** no menu de navegação do lado esquerdo da aplicação.

8. Para abrir o **adicionar atribuição** painel, clique nas **Add** botão na parte superior do **utilizadores e grupos** lista.

9. Clique nas **utilizadores e grupos** Seletor da **adicionar atribuição** painel.

10. Escreva o **nome do grupo completo** do grupo estiver interessado em atribuir para o **pesquisar por nome ou endereço de e-mail** caixa de pesquisa.

11. Paire o rato sobre o **grupo** na lista para revelar uma **caixa de verificação**. Clique na caixa de verificação junto a fotografia do perfil ou à logótipo para adicionar o utilizador para o grupo de **selecionados** lista.

12. **Opcional:** Se quiser **adicionar mais de um grupo**, tipo em outro **nome do grupo completo** no **procurar por nome ou endereço de e-mail** caixa de pesquisa e clique na caixa de verificação para adicionar este grupo para o **selecionados** lista.

13. Quando tiver terminado de selecionar os grupos, clique nas **selecione** botão para adicioná-los à lista de utilizadores e grupos que devem ser atribuídos à aplicação.

14. **Opcional:** clique a **selecionar função** Seletor no **adicionar atribuição** painel para selecionar uma função para atribuir aos grupos que selecionou.

15. Clique nas **atribuir** botão para atribuir a aplicação aos grupos selecionados.

Após um curto período, os utilizadores que selecionou ser capaz de iniciar estas aplicações no painel de acesso.

## <a name="next-steps"></a>Passos Seguintes
[Fornecer início de sessão único às suas aplicações com o Proxy de aplicações](application-proxy-configure-single-sign-on-with-kcd.md)
