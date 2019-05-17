---
title: Configurar o início de sessão único - Azure Active Directory | Microsoft Docs
description: Este tutorial utiliza o portal do Azure para configurar o início de sessão único baseado em SAML para uma aplicação com o Azure Active Directory (Azure AD).
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: tutorial
ms.workload: identity
ms.date: 04/08/2019
ms.author: mimart
ms.reviewer: arvinh,luleon
ms.collection: M365-identity-device-management
ms.openlocfilehash: fa18bc637ec31a1f83b5cab090e008715c5e0c2a
ms.sourcegitcommit: be9fcaace62709cea55beb49a5bebf4f9701f7c6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/17/2019
ms.locfileid: "65825017"
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

2. Faça o fornecedor da aplicação para as informações descritas [configurar opções básicas de SAML](#configure-basic-saml-options).

3. Utilize um ambiente de não produção para testar os passos neste tutorial. Se não tiver um ambiente de não produção do Azure AD, pode [obter uma avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

4. Inicie sessão para o [portal do Azure](https://portal.azure.com) como um administrador de aplicações na cloud ou um administrador da aplicação para o seu inquilino do Azure AD.

## <a name="select-a-single-sign-on-mode"></a>Selecionar o modo de início de sessão único

Depois de adicionar uma aplicação ao seu inquilino do Azure AD, está pronto para configurar o início de sessão único para a aplicação.

Para abrir as definições do início de sessão único:

1. Na [portal do Azure](https://portal.azure.com), no painel de navegação esquerdo, selecione **Azure Active Directory**. 

2. Sob **Manage** no **Azure Active Directory** painel de navegação que aparece, selecione **aplicações empresariais**. É apresentada uma amostra aleatória das aplicações no inquilino do Azure AD. 

3. Na **tipo de aplicação** menu, selecione **todos os aplicativos**e, em seguida, selecione **aplicar**.

4. Introduza o nome da aplicação para a qual pretende configurar o início de sessão único. Por exemplo, pode introduzir **GitHub-teste** para configurar o aplicativo adicionado na [Adicionar aplicação](add-application-portal.md) início rápido.  

     ![Captura de ecrã que mostra a barra de pesquisa do aplicativo.](media/configure-single-sign-on-portal/azure-portal-application-search.png)

5. Escolha a aplicação para o qual pretende configurar início de sessão único.

6. Sob o **Manage** secção, selecione **início de sessão único**. 

7. Selecione **SAML** para configurar o início de sessão único. O **definir a segurança de início de sessão único com o SAML - pré-visualização** é apresentada a página.

## <a name="configure-basic-saml-options"></a>Configurar opções básicas de SAML

Para configurar o domínio e os URLs:

1. Contacte o fornecedor da aplicação para obter as informações certas para as definições seguintes:

    | Definição de configuração | Iniciado pelo SP | Iniciado pelo idP | Descrição |
    |:--|:--|:--|:--|
    | Identificador (ID de Entidade) | Necessário para algumas aplicações | Necessário para algumas aplicações | Identifica exclusivamente a aplicação para a qual o início de sessão único está a ser configurado. O Azure AD envia o identificador para o aplicativo como o parâmetro de audiência do SAML token. Espera-se que o aplicativo validá-la. Este valor também aparece como o ID da Entidade nos metadados SAML que a aplicação fornece.|
    | URL de Resposta | Opcional | Necessário | Especifica onde é que a aplicação espera receber o token SAML. O URL de resposta também é denominado URL do Serviço de Consumidor de Asserções (ACS). |
    | URL de início de sessão | Necessário | Não especificar | Quando um utilizador abre este URL, o fornecedor de serviços redireciona para o Azure AD para autenticar e iniciar a sessão do utilizador. Azure AD utiliza o URL para iniciar a aplicação do Office 365 ou o painel de acesso do Azure AD. Quando em branco, o Azure AD depende do fornecedor de identidade para iniciar o início de sessão único, quando um usuário inicia o aplicativo.|
    | Estado de Reencaminhamento | Opcional | Opcional | Especifica à aplicação para onde deve redirecionar o utilizador após a conclusão da autenticação. Normalmente, o valor é um URL válido para a aplicação. No entanto, alguns aplicativos usam esse campo diferente. Para obter mais informações, contacte o fornecedor da aplicação.
    | URL de fim de sessão | Opcional | Opcional | Utilizado para enviar as respostas de fim de sessão de SAML para a aplicação.


2. Para editar as opções de configuração de SAML básicas, selecione o **edite** ícone (um lápis) no canto superior direito do **configuração básica de SAML** secção.

     ![Configurar os certificados](media/configure-single-sign-on-portal/basic-saml-configuration-edit-icon.png)

3. Os campos adequados na página, introduza as informações fornecidas pelo fornecedor do aplicativo no passo 1.

4. Na parte superior da página, selecione **guardar**.

## <a name="configure-user-attributes-and-claims"></a>Configurar atributos de utilizador e afirmações 

Pode controlar as informações do Azure AD envia para a aplicação no SAML token, quando um utilizador inicia sessão. Pode controlar estas informações através da configuração de atributos do utilizador. Por exemplo, pode configurar o Azure AD para enviar o nome do utilizador, e-mail e identificação do funcionário para a aplicação quando um utilizador inicia sessão. 

Estes atributos podem ser obrigatórios ou opcionais para fazer com que o início de sessão único funcione corretamente. Para obter mais informações, veja o [tutorial para aplicações específicas](../saas-apps/tutorial-list.md) ou peça-as ao fornecedor da aplicação.

1. Para editar afirmações e atributos de utilizador, selecione o **editar** ícone (um lápis) no canto superior direito do **atributos de utilizador e afirmações** secção.

   O **nome do valor do identificador** está definida com o valor predefinido de *user.principalname*. O identificador de utilizador identifica exclusivamente cada utilizador da aplicação. Por exemplo, se o endereço de e-mail for o nome de utilizador e o identificador exclusivo, defina o valor como *user.mail*.

2. Para modificar a **valor do identificador de nome**, selecione a **editar** ícone (um lápis) para o **valor do identificador de nome** campo. Faça as alterações apropriadas para o formato de identificador e a origem, conforme necessário. Quando tiver terminado, guarde as alterações. Para obter mais informações sobre como personalizar afirmações, consulte a [personalizar afirmações emitidas no token SAML para aplicações empresariais](../develop/active-directory-saml-claims-customization.md) artigo que mostra como.

3. Para adicionar uma afirmação, selecione **Adicionar nova afirmação** na parte superior da página. Introduza o **nome** e selecione a origem adequada. Se selecionar a **atributo** origem, terá de escolher a **atributo de origem** que pretende utilizar. Se selecionar a **tradução** origem, terá de escolher a **transformação** e **parâmetro 1** que pretende utilizar.

4. Selecione **Guardar**. Nova afirmação é apresentada na tabela.
 
## <a name="generate-a-saml-signing-certificate"></a>Gerar um certificado de assinatura de SAML

O Azure AD utiliza um certificado para assinar os tokens SAML que envia à aplicação. 

1. Para gerar um novo certificado, selecione o **edite** ícone (um lápis) no canto superior direito do **certificado de assinatura SAML** secção.

2. Na **certificado de assinatura SAML** secção, selecione **novo certificado**.

3. Na nova linha de certificado que aparece, defina o **data de expiração**. Para obter mais informações sobre as opções de configuração disponíveis, consulte a [opções de assinatura de certificado avançadas](certificate-signing-options.md) artigo.

4. Selecione **salvar** na parte superior a **certificado de assinatura SAML** secção. 

## <a name="assign-users-to-the-application"></a>Atribuir utilizadores à aplicação

É uma boa idéia para testar o início de sessão único com vários utilizadores ou grupos antes de implementar a aplicação à sua organização.

> [!NOTE]
>
> Estes passos levá-lo para o **utilizadores e grupos** seção de configuração no portal. Quando terminar, terá de navegar de volta para o **início de sessão único** secção para concluir o tutorial.

Para atribuir um utilizador ou grupo à aplicação:

1. Abra a aplicação no portal, se ainda não estiver aberta.
2. No painel de navegação à esquerda para a aplicação, selecione **utilizadores e grupos**.
3. Selecione **adicionar utilizador**.
4. Na **adicionar atribuição** secção, selecione **utilizadores e grupos**.
5. Para localizar um utilizador específico, escreva o nome de utilizador para o **selecione o membro ou Convide um utilizador externo** caixa. Em seguida, selecione fotografia do perfil do usuário ou o logótipo e, em seguida, escolha **selecione**. 
6. Na **adicionar atribuição** secção, selecione **atribuir**. Quando terminar, os utilizadores selecionados aparecem na **utilizadores e grupos** lista.

## <a name="set-up-the-application-to-use-azure-ad"></a>Configurar a aplicação para utilizar o Azure AD

Está quase concluído.  Como passo final, terá de configurar a aplicação para utilizar o Azure AD como um fornecedor de identidade. 

1. Desloque para baixo para o **configure <applicationName>**  secção. Para este tutorial, esta secção é chamada **configurar o GitHub e teste**. 
2. Copie o valor de cada linha nesta secção. Em seguida, cole cada valor na linha apropriada no **configuração básica de SAML** secção. Por exemplo, copie o **URL de início de sessão** partir dos **configurar teste de GitHub** secção e cole-o no **URL de início de sessão** campo no **configuração básica de SAML**  secção e assim por diante.
3. Quando tiver colado todos os valores nos campos apropriados, selecione **guardar**.

## <a name="test-single-sign-on"></a>Testar início de sessão único

Está pronto para testar suas configurações.  

1. Abra as definições do início de sessão único para a sua aplicação. 
2. Desloque-se para o **validar o início de sessão único com <applicationName>**  secção. Para este tutorial, esta secção é chamada **configurar o GitHub e teste**.
3. Selecione **teste**. As opções do teste aparecem.
4. Selecione **iniciar sessão como utilizador atual**. Este teste permite que primeiro veja se o início de sessão único funcione para si, o administrador.

Se existir um erro, é apresentada uma mensagem de erro. Conclua os seguintes passos:

1. Copie e cole os detalhes na caixa **Qual é o aspeto do erro?**.

    ![Obter instruções de resolução](media/configure-single-sign-on-portal/error-guidance.png)

2. Selecione **obtenha documentação de orientação de resolução**. São apresentadas as orientações de resolução e causa raiz.  Neste exemplo, o utilizador não foi atribuída à aplicação.

3. Leia as orientações de resolução e, em seguida, se possível, corrigir o problema.

4. Execute o teste novamente, até que seja concluído sem erros.

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

Para implementar a aplicação a mais utilizadores na sua organização, utilize o aprovisionamento automático de utilizadores.

> [!div class="nextstepaction"]
> [Learn how to assign users with automatic provisioning](configure-automatic-user-provisioning-portal.md) Saiba como atribuir utilizadores com o aprovisionamento automático)


