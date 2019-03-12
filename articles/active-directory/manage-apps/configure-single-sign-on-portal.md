---
title: Configurar o início de sessão único - Azure Active Directory | Microsoft Docs
description: Este tutorial utiliza o portal do Azure para configurar o início de sessão único baseado em SAML para uma aplicação com o Azure Active Directory (Azure AD).
services: active-directory
author: CelesteDG
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: tutorial
ms.workload: identity
ms.date: 12/06/2018
ms.author: celested
ms.reviewer: arvinh,luleon
ms.collection: M365-identity-device-management
ms.openlocfilehash: 037f5b554889d89fc0b50983d3d85d38f4345311
ms.sourcegitcommit: dd1a9f38c69954f15ff5c166e456fda37ae1cdf2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57571410"
---
# <a name="tutorial-configure-saml-based-single-sign-on-for-an-application-with-azure-active-directory"></a>Tutorial: Configurar baseado em SAML início de sessão único para uma aplicação com o Azure Active Directory

Este tutorial utiliza o portal do [Azure](https://portal.azure.com) para configurar o início de sessão único baseado em SAML para uma aplicação com o Azure Active Directory (Azure AD). Utilize este tutorial quando uma [específico do aplicativo tutorial](../saas-apps/tutorial-list.md) não está disponível. 

Este tutorial utiliza o portal do Azure para:

> [!div class="checklist"]
> * Selecionar o modo de início de sessão único baseado em SAML
> * Configurar domínios e URLs específicos de URLs
> * Configurar os atributos de utilizador
> * Criar um certificado de assinatura de SAML
> * Atribuir utilizadores à aplicação
> * Configurar a aplicação para início de sessão único baseado em SAML
> * Testar as definições de SAML

## <a name="before-you-begin"></a>Antes de começar

1. Se o aplicativo não tenha sido adicionado ao seu inquilino do Azure AD, consulte o artigo [início rápido: Adicionar uma aplicação ao seu inquilino do Azure AD](add-application-portal.md).

2. Peça ao fornecedor da sua aplicação as informações descritas em [Configurar domínio e URLs](#configure-domain-and-urls).

3. Para testar os passos neste tutorial, recomendamos utilizar um ambiente de não produção. Se não tiver um ambiente de não produção do Azure AD, pode [obter uma avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

4. Inicie sessão para o [portal do Azure](https://portal.azure.com) como um administrador de aplicações na cloud ou um administrador da aplicação para o seu inquilino do Azure AD.

## <a name="select-a-single-sign-on-mode"></a>Selecionar o modo de início de sessão único

Depois de um aplicativo é adicionado ao inquilino do Azure AD, está pronto para configurar o início de sessão único para a aplicação.

Para abrir as definições do início de sessão único:

1. No [portal do Azure](https://portal.azure.com), no painel de navegação do lado esquerdo, clique em **Azure Active Directory**. 

2. No painel **Azure Active Directory,** clique em **Aplicações empresariais**. É aberto o painel **Todas as aplicações**, que mostra uma amostra aleatória das aplicações no seu inquilino do Azure AD. 

3. No menu **Tipo de Aplicação**, selecione **Todas as Aplicações** e clique em **Aplicar**.

4. Introduza o nome da aplicação para a qual pretende configurar o início de sessão único. Escolha a sua aplicação ou introduza **GitHub-teste** para configurar o aplicativo adicionado na [Adicionar aplicação](add-application-portal.md) início rápido.

5. Clique em **Início de sessão único**. Em **Modo de Início de Sessão Único**, é apresentado **Início de sessão baseado em SAML** como a opção predefinida. 

    ![Opções de configuração](media/configure-single-sign-on-portal/config-options.png)

6. Clique em **Guardar**, na parte superior do painel. 

## <a name="configure-domain-and-urls"></a>Configurar domínio e URLs

Para configurar o domínio e os URLs:

1. Contacte o fornecedor da aplicação para obter as informações certas para as definições seguintes:

    | Definição de configuração | Iniciado pelo SP | Iniciado pelo idP | Descrição |
    |:--|:--|:--|:--|
    | URL de início de sessão | Necessário | Não especificar | Quando um utilizador abre este URL, o fornecedor de serviços redireciona para o Azure AD para autenticar e iniciar a sessão do utilizador. Azure AD utiliza o URL para iniciar a aplicação do Office 365 ou o painel de acesso do Azure AD. Quando em branco, o Azure AD depende do fornecedor de identidade para iniciar o início de sessão único, quando um usuário inicia o aplicativo.|
    | Identificador (ID de Entidade) | Necessário para algumas aplicações | Necessário para algumas aplicações | Identifica exclusivamente a aplicação para a qual o início de sessão único está a ser configurado. O Azure AD envia o identificador para o aplicativo como o parâmetro de audiência do SAML token. Espera-se que o aplicativo validá-la. Este valor também aparece como o ID da Entidade nos metadados SAML que a aplicação fornece.|
    | URL de Resposta | Opcional | Necessário | Especifica onde é que a aplicação espera receber o token SAML. O URL de resposta também é denominado URL do Serviço de Consumidor de Asserções (ACS). |
    | Estado de Reencaminhamento | Opcional | Opcional | Especifica à aplicação para onde deve redirecionar o utilizador após a conclusão da autenticação. Normalmente, o valor é um URL válido para a aplicação; no entanto, algumas aplicações utilizam este campo de outra forma. Para obter mais informações, contacte o fornecedor da aplicação.

2. Introduza as informações. Para ver todas as definições, clique em **Mostrar definições de URL avançadas**.

    ![Opções de configuração](media/configure-single-sign-on-portal/config-urls.png)

3. Na parte superior do painel, clique em **Guardar**.

4. Há uma **definições de SAML do teste** botão nesta secção. Execute este teste mais à frente no tutorial, na secção [Testar o início de sessão único](#test-single-sign-on).

## <a name="configure-user-attributes"></a>Configurar os atributos de utilizador

Atributos de utilizador permitem-lhe controlar as informações do Azure AD envia para a aplicação no token SAML sempre que um utilizador inicia sessão. Por exemplo, pode enviar o nome, o e-mail e o identificador de colaborador dos utilizadores. 

Estes atributos podem ser obrigatórios ou opcionais para fazer com que o início de sessão único funcione corretamente. Para obter mais informações, veja o [tutorial para aplicações específicas](../saas-apps/tutorial-list.md) ou peça-as ao fornecedor da aplicação.

1. Para ver todas as opções, clique em **Ver e editar todos os outros atributos de utilizador**.

    ![Configurar os atributos de utilizador](media/configure-single-sign-on-portal/config-user-attributes.png)

2. Introduza o **Identificador de Utilizador**.

    O identificador de utilizador identifica exclusivamente cada utilizador da aplicação. Por exemplo, se o endereço de e-mail for o nome de utilizador e o identificador exclusivo, defina o valor como *user.mail*.

3. Para mais atributos do token SAML, clique em **Ver e editar todos os outros atributos de utilizador**.

4. Para adicionar um atributo aos **Atributos do Token SAML**, clique em **Adicionar atributo**. Introduza o **Nome** e selecione o **Valor**, no menu.

5. Clique em **Guardar**. O atributo novo é apresentado na tabela.
 
## <a name="create-a-saml-signing-certificate"></a>Criar um certificado de assinatura de SAML

O Azure AD utiliza um certificado para assinar os tokens SAML que envia à aplicação. 

1. Para ver todas as opções, clique em **Mostrar opções avançadas de assinatura de certificados**.

    ![Configurar os certificados](media/configure-single-sign-on-portal/config-certificate.png)

2. Para configurar um certificado, clique em **Criar Novo Certificado**.

3. Na **criar novo certificado** painel, defina **data de expiração**e clique em **guardar**.

4. Clique em **Tornar o certificado novo ativo**.

5. Para saber mais, veja [Advanced certificate signing options](certificate-signing-options.md) (Opções avançadas de assinatura de certificados).

6. Para não perder as alterações que fez até ao momento, confirme que clica em **Guardar**, na parte superior do painel**Início de sessão único**. 

## <a name="assign-users-to-the-application"></a>Atribuir utilizadores à aplicação

Antes de implementar a aplicação na sua organização, a Microsoft recomenda testar o início de sessão único com vários utilizadores ou grupos.

Para atribuir um utilizador ou grupo à aplicação:

1. Abra a aplicação no portal, se ainda não estiver aberta.
2. No painel da aplicação do lado esquerdo, clique em **Utilizadores e grupos**.
3. Clique em **Adicionar utilizador**.
4. No painel **Adicionar Atribuição**, clique em **Utilizadores e grupos**.
5. Para encontrar um utilizador específico, escreva o nome do mesmo na caixa **Selecionar**, clique na caixa de verificação junto à fotografia de perfil ou ao logótipo do utilizador e clique em **Selecionar**. 
6. Localize o seu nome de utilizador atual e selecione-o. Pode selecionar mais utilizadores, opcionalmente.
7. No painel **Adicionar Atribuição**, clique em **Atribuir**. Quando terminar, os utilizadores selecionados aparecem na lista **Utilizadores e grupos**.

## <a name="configure-the-application-to-use-azure-ad"></a>Configurar a aplicação para utilizar o Azure AD

Está quase concluído.  Como último passo, tem de configurar a aplicação para utilizar o Azure AD como o fornecedor de identidade SAML. 

1. Desloque-se até à parte inferior do painel **Início de sessão único** da aplicação. 

    ![Configurar aplicação](media/configure-single-sign-on-portal/configure-app.png)

2. Clique em **Configurar aplicação**, no portal, e siga as instruções.
3. Crie manualmente contas de utilizador na aplicação para testar o início de sessão único. Crie as contas de utilizador que atribuiu à aplicação na [secção anterior](#assign-users-to-the-application). 

## <a name="test-single-sign-on"></a>Testar início de sessão único

Está pronto para testar as suas definições.  

1. Abra as definições do início de sessão único para a sua aplicação. 
2. Desloque-se para a secção **Configurar domínio e URLs**.
2. Clique em **Testar Definições de SAML**. As opções do teste aparecem.

    ![Opções do teste do início de sessão único](media/configure-single-sign-on-portal/test-single-sign-on.png) 

3. Clique em **Iniciar sessão como o utilizador atual**. Este teste permite que primeiro veja se o início de sessão único funcione para si, o administrador.
4. Se existir um erro, é apresentada uma mensagem de erro. Copie e cole os detalhes na caixa **Qual é o aspeto do erro?**.

    ![Obter orientações de resolução](media/configure-single-sign-on-portal/error-guidance.png)

5. Clique em **Obter orientações de resolução**. São apresentadas as orientações de resolução e causa raiz.  Neste exemplo, o utilizador não foi atribuída à aplicação.

    ![Corrigir o erro](media/configure-single-sign-on-portal/fix-error.png)

6. Leia as orientações de resolução e, se necessário, clique em **Corrigir**.

7. Execute o teste novamente, até que seja concluído sem erros.



## <a name="next-steps"></a>Passos Seguintes
Neste tutorial, configurou as definições únicas início de sessão para uma aplicação. Depois de concluída a configuração, atribuiu um utilizador à aplicação e configurou-a para utilizar o início de sessão único baseado em SAML. Depois de ter feito tudo isto, verificou se o início de sessão SAML estava a funcionar corretamente.

Fez tudo isto:
> [!div class="checklist"]
> * Selecionou SAML para o modo de início de sessão único
> * Contacto o fornecedor da aplicação para configurar o domínio e os URLs
> * Configurou os atributos de utilizador
> * Criou um certificado de assinatura de SAML
> * Atribuiu manualmente utilizadores ou grupos à aplicação
> * Configurado a aplicação para utilizar o Azure AD como um fornecedor de identidade
> * Testou o início de sessão único baseado em SAML

Para implementar a aplicação a mais utilizadores na sua organização, recomendamos que utilize o aprovisionamento automático de utilizadores.

> [!div class="nextstepaction"]
> [Learn how to assign users with automatic provisioning](configure-automatic-user-provisioning-portal.md) Saiba como atribuir utilizadores com o aprovisionamento automático)


