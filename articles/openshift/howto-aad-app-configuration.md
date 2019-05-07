---
title: Criar um registo de aplicação do Azure AD e o utilizador para a Azure Red Hat OpenShift | Documentos da Microsoft
description: Saiba como criar um principal de serviço, gerar um URL de retorno de chamada de autenticação e o segredo do cliente e criar um novo utilizador do Active Directory para fins de teste de aplicações no seu cluster do Microsoft Azure Red Hat OpenShift.
author: tylermsft
ms.author: twhitney
ms.service: openshift
manager: jeconnoc
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 05/06/2019
ms.openlocfilehash: de3f3c30848d26ea399bcccc29a6149a149f6a55
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65078524"
---
# <a name="create-an-azure-ad-app-registration-and-user-for-azure-red-hat-openshift"></a>Criar um registo de aplicação do Azure AD e o utilizador para a Azure Red Hat OpenShift

Microsoft Azure Red Hat OpenShift necessita de permissões para executar tarefas em nome do seu cluster. Se sua organização ainda não tiver um registo de aplicações do Azure Active Directory (Azure AD) para utilizar como o principal de serviço, siga estas instruções para criar um.

Este tópico é concluído com instruções para criar um novo utilizador do Azure AD que precisará para aceder às aplicações em execução no seu cluster do Azure Red Hat OpenShift.

Se ainda não tiver criado um inquilino do Azure AD, siga as indicações [criar um inquilino do Azure AD para a Azure Red Hat OpenShift](howto-create-tenant.md) antes de continuar com estas instruções.

## <a name="create-a-new-app-registration"></a>Criar um novo registo de aplicação

Um aplicativo que deseja usar os recursos do Azure AD em primeiro lugar tem de estar registado no inquilino do Azure AD. Este processo de registo envolve fornecendo os detalhes do Azure AD sobre a sua aplicação, como o URL em que a aplicação está localizada, o URL para enviar respostas depois de um utilizador é autenticado, o URI que identifica a aplicação e assim por diante.

1. Na [portal do Azure](https://portal.azure.com), certifique-se de que o seu inquilino é apresentado em seu nome de utilizador no canto superior direito do portal:

    ![Captura de ecrã do portal com o inquilino listado no canto superior direito][tenantcallout] se o inquilino errado for apresentado, clique em seu nome de utilizador no canto superior direito, em seguida, clique em **trocar diretório**e selecione o inquilino correto do **todos os Diretórios** lista.

2. Abra o [painel de registos de aplicação](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps) e clique em **novo registo de aplicação**.
3. Na **criar** painel, introduza um nome amigável para seu objeto de aplicativo.
4. Certifique-se de que **tipo de aplicação** está definida como *aplicação/API Web*.
5. Criar uma **URL de início de sessão** usando o seguinte padrão:

    ```
    https://<cluster-name>.<azure-region>.cloudapp.azure.com
    ```

    . . . em que `<cluster-name>` é o nome pretendido do seu cluster do Azure Red Hat OpenShift e `<azure-region>` é o [região do Azure que aloja o seu cluster do Azure Red Hat OpenShift](supported-resources.md#azure-regions). Por exemplo, se o nome do cluster está a ser `contoso`, e irá criá-la no `eastus` região, o nome de domínio completamente qualificado (FQDN) que vai introduzir para o **URL de início de sessão** seria `https://contoso.eastus.cloudapp.azure.com`

    > [!IMPORTANT]
    > O nome do cluster tem de estar todo em minúsculo e o URL do FQDN tem de ser exclusivo.
    > Certifique-se de que escolha um nome suficientemente distinto para o seu cluster.

    Anote o nome do cluster e início de sessão no URL, precisará-los mais tarde para aceder a aplicações em execução no seu cluster. Iremos dar o nome do cluster `CLUSTER_NAME`e este URL de início de sessão como `FQDN` no [criar um cluster do Azure Red Hat OpenShift](tutorial-create-cluster.md) tutorial.

6. Certifique-se sua **URL de início de sessão** valor valida com uma verificação a verde. (Prima a tecla de tabulação concentrar-se do *URL de início de sessão* campo e execute a verificação de validação.)
7. Clique nas **criar** botão para criar o objeto de aplicação do Azure AD.
8. Sobre o **aplicação registada** página que aparece, copie a **ID da aplicação**. Nós nos referiremos a este valor como `APPID` no [criar um cluster do Azure Red Hat OpenShift](tutorial-create-cluster.md) tutorial.

    ![Captura de ecrã da caixa de texto de ID da aplicação][appidimage]
    
Para obter mais informações sobre objetos de aplicação do Azure, consulte [aplicativos e objetos de principal de serviço no Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals).

Para obter detalhes sobre como criar uma nova aplicação do Azure AD, consulte [registar uma aplicação com o ponto de final do Azure Active Directory v1.0](https://docs.microsoft.com/azure/active-directory/develop/quickstart-v1-add-azure-ad-app).

### <a name="create-a-client-secret"></a>Criar um segredo do cliente

Agora, está pronto para gerar um segredo do cliente para autenticar a sua aplicação ao Azure Active Directory.

1. Do **aplicação registada** página, clique em **definições** para abrir as definições para a sua aplicação registada.
2. Sobre o **definições** painel apresentado, clique em **chaves**.  O **chaves** é apresentado o painel.
![Captura de ecrã da página de chave a criar no portal do][createkeyimage]
3. Forneça uma chave **Descrição**.
4. Definir um valor para **Expires**, por exemplo *dentro de 2 anos*.
5. Clique em **guardar** e será apresentado o valor da chave.
6. Copie o valor da chave. Nós nos referiremos a este valor como `SECRET` no [criar um cluster do Azure Red Hat OpenShift](tutorial-create-cluster.md) tutorial.

### <a name="create-a-reply-url"></a>Criar um URL de resposta

Azure AD utiliza o objeto de aplicação *URL de resposta* para especificar o retorno de chamada para utilizar ao autorizar a aplicação. No caso de uma web API ou aplicação web, o URL de resposta é a localização onde o Azure AD irá enviar a resposta de autenticação, incluindo um token, se a autenticação foi efetuada com êxito.

Até mesmo o menor erro de correspondência de (à direita em falta a barra, maiúsculas/minúsculas diferentes) fará com que a falha da operação de emissão de token e não será possível iniciar sessão.

1. Volte à **configurações** painel (pode clicar em **definições** no trilho na parte superior do portal) e clique em **URLs de resposta** à direita.  O **URLs de resposta** é apresentado o painel.
2. O URL de resposta primeiro na lista irá ser o `FQDN` valor do passo 6 [criar um novo registo de aplicação](#create-a-new-app-registration). Editá-lo e de acréscimo `/oauth2callback/Azure%20AD`.  Por exemplo, o URL de resposta deve agora ter um aspeto semelhante `https://mycluster.eastus.cloudapp.azure.com/oauth2callback/Azure%20AD`
3. Clique em **guardar** para guardar o URL de resposta.

## <a name="create-a-new-active-directory-user"></a>Criar um novo utilizador do Active Directory

Crie um novo utilizador no Active Directory a utilizar para iniciar sessão na aplicação em execução no seu cluster do Azure Red Hat OpenShift.

1. Vá para o [utilizadores - todos os utilizadores](https://portal.azure.com/#blade/Microsoft_AAD_IAM/UsersManagementMenuBlade/AllUsers) painel.
2. Clique em **+ novo utilizador**. O **utilizador** é apresentado o painel.
3. Introduza um **nome** que pretende para este utilizador.
4. Criar uma **nome de utilizador** com base no nome do inquilino que criou com `.onmicrosoft.com` anexada ao final. Por exemplo, `yourUserName@yourTenantName.onmicrosoft.com`. Anote este nome de utilizador. Precisará das mesmas para iniciar sessão utilizar a aplicação no seu cluster.
5. Clique em **função de diretório** e selecione **Administrador Global** e, em seguida, clique em **Ok** na parte inferior do painel.
6. No meio dos **usuário** painel, clique em **mostrar palavra-passe** e registe a palavra-passe temporária. Depois de iniciar sessão pela primeira vez, será solicitado a repô-lo.
7. Na parte inferior do painel, clique em **criar** para criar o utilizador.

## <a name="resources"></a>Recursos

* [Aplicativos e objetos de principal de serviço no Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals)  
* [Quickstart: Registar uma aplicação com o ponto de final de versão 1.0 do Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/quickstart-v1-add-azure-ad-app)  

[appidimage]: ./media/howto-create-tenant/get-app-id.png
[createkeyimage]: ./media/howto-create-tenant/create-key.png
[tenantcallout]: ./media/howto-create-tenant/tenant-callout.png

## <a name="next-steps"></a>Passos Seguintes

Se tiver cumprido todos os [pré-requisitos do Azure Red Hat OpenShift](howto-setup-environment.md), está pronto para criar seu primeiro cluster!

Experimente o tutorial:
> [!div class="nextstepaction"]
> [Criar um cluster do Azure Red Hat OpenShift](tutorial-create-cluster.md)
