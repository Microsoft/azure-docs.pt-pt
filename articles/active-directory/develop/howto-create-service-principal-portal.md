---
title: Criar uma app e diretor de serviço azure AD no portal
titleSuffix: Microsoft identity platform
description: Crie uma nova app e serviço de diretório Ativo Azure para gerir o acesso a recursos com controlo de acesso baseado em papéis no Azure Resource Manager.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.date: 10/14/2019
ms.author: ryanwi
ms.reviewer: tomfitz
ms.custom: aaddev, seoapril2019, identityplatformtop40
ms.openlocfilehash: 2283f4f3cf1d31f0d67e01e1a63ee20557ef5633
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/25/2020
ms.locfileid: "77591579"
---
# <a name="how-to-use-the-portal-to-create-an-azure-ad-application-and-service-principal-that-can-access-resources"></a>Como: Utilizar o portal para criar uma aplicação e diretor de serviço seletiva azure que possa aceder a recursos

Este artigo mostra-lhe como criar uma nova aplicação e diretor de serviço azure Ative Directory (Azure AD) que pode ser usado com o controlo de acesso baseado em papéis. Quando tem um código que precisa de aceder ou modificar recursos, pode criar uma identidade para a aplicação. Esta identidade é conhecida como um principal de serviço. Em seguida, pode atribuir as permissões necessárias ao diretor de serviço. Este artigo mostra-lhe como usar o portal para criar o diretor de serviço. Centra-se numa aplicação de um único inquilino onde o pedido se destina a ser executado dentro de apenas uma organização. Você normalmente usa aplicações de inquilino único para aplicações de linha de negócio que funcionam dentro da sua organização.

> [!IMPORTANT]
> Em vez de criar um diretor de serviço, considere usar identidades geridas para recursos Azure para a sua identidade de aplicação. Se o seu código for executado num serviço que suporte identidades geridas e aceda a recursos que suportam a autenticação Azure AD, as identidades geridas são uma melhor opção para si. Para saber mais sobre identidades geridas para os recursos do Azure, incluindo quais os serviços que atualmente o apoiam, veja [o que é gerida identidades para os recursos Azure?](../managed-identities-azure-resources/overview.md)

## <a name="create-an-azure-active-directory-application"></a>Criar uma aplicação azure ative diretório

Vamos entrar na criação da identidade. Se tiver um problema, verifique as [permissões necessárias](#required-permissions) para se certificar de que a sua conta pode criar a identidade.

1. Inscreva-se na sua Conta Azure através do [portal Azure.](https://portal.azure.com)
1. Selecione **Azure Active Directory**.
1. Selecione **Registos das aplicações**.
1. Selecione **Novo registo**.
1. Diga o nome do pedido. Selecione um tipo de conta suportada, que determina quem pode utilizar a aplicação. Em **Redirect URI,** selecione **Web** para o tipo de aplicação que pretende criar. Insira o URI para onde o sinal de acesso é enviado. Não se pode criar credenciais para uma [aplicação nativa.](../manage-apps/application-proxy-configure-native-client-application.md) Não pode supor esse tipo para uma aplicação automatizada. Depois de definir os valores, selecione **Registar**.

   ![Digite um nome para a sua candidatura](./media/howto-create-service-principal-portal/create-app.png)

Criou a sua aplicação e diretor de serviço azure AD.

## <a name="assign-a-role-to-the-application"></a>Atribuir uma função à aplicação

Para aceder aos recursos na sua subscrição, deve atribuir uma função à aplicação. Decida qual o papel que oferece as permissões certas para a aplicação. Para conhecer as funções disponíveis, consulte [RBAC: Built in Roles](../../role-based-access-control/built-in-roles.md).

Pode definir o âmbito ao nível da subscrição, grupo de recursos ou recurso. As permissões são herdadas para níveis inferiores de âmbito. Por exemplo, adicionar uma aplicação ao papel do Leitor para um grupo de recursos significa que pode ler o grupo de recursos e quaisquer recursos que contenha.

1. No portal Azure, selecione o nível de âmbito a que pretende atribuir a aplicação. Por exemplo, atribuir uma função no âmbito de subscrição, pesquisar e selecionar **Subscrições,** ou selecionar **Subscrições** na página **Inicial.**

   ![Por exemplo, atribuir uma função no âmbito de subscrição](./media/howto-create-service-principal-portal/select-subscription.png)

1. Selecione a subscrição específica para atribuir a aplicação.

   ![Selecione subscrição para atribuição](./media/howto-create-service-principal-portal/select-one-subscription.png)

   Se não vir a subscrição que procura, selecione filtro de **subscrições globais.** Certifique-se de que a subscrição que deseja está selecionada para o portal.

1. Selecione **Controlo de acesso (IAM)** .
1. **Selecione Adicionar a atribuição de funções**.
1. Selecione a função que pretende atribuir à aplicação. Por exemplo, para permitir que a aplicação execute ações como **reiniciar,** **iniciar** e **parar** instâncias, selecione a função **Contributiva.**  Leia mais sobre as [funções disponíveis](../../role-based-access-control/built-in-roles.md) Por defeito, as aplicações Azure AD não são apresentadas nas opções disponíveis. Para encontrar a sua aplicação, procure o nome e selecione-o.

   ![Selecione a função a atribuir à aplicação](./media/howto-create-service-principal-portal/select-role.png)

1. Selecione **Guardar** para terminar a atribuição da função. Vê a sua aplicação na lista de utilizadores com um papel nesse âmbito.

O seu diretor de serviço está preparado. Pode começar a usá-lo para executar os seus scripts ou aplicações. A secção seguinte mostra como obter valores que são necessários ao assinar programáticamente.

## <a name="get-values-for-signing-in"></a>Obtenha valores para iniciar sessão

Ao iniciar sessão programática, tem de passar a identificação do inquilino com o seu pedido de autenticação. Também precisa do ID para a sua aplicação e de uma chave de autenticação. Para obter esses valores, utilize os seguintes passos:

1. Selecione **Azure Active Directory**.
1. A partir de registos de **aplicações** em Azure AD, selecione a sua aplicação.
1. Copie o ID do Diretório (inquilino) e guarde-o no seu código de candidatura.

    ![Copie o diretório (ID do inquilino) e guarde-o no seu código de aplicações](./media/howto-create-service-principal-portal/copy-tenant-id.png)

1. Copie o **ID da Aplicação** e armazene-o no código da aplicação.

   ![Copiar o ID do pedido (cliente)](./media/howto-create-service-principal-portal/copy-app-id.png)

## <a name="certificates-and-secrets"></a>Certificados e segredos
As aplicações Daemon podem usar duas formas de credenciais para autenticar com AD Azure: certificados e segredos de aplicação.  Recomendamos a utilização de um certificado, mas também pode criar um novo segredo de aplicação.

### <a name="upload-a-certificate"></a>Faça upload de um certificado

Pode usar um certificado existente se tiver um.  Opcionalmente, pode criar um certificado auto-assinado para efeitos de teste. Abra a PowerShell e execute [o New-SelfSignedCertificate](/powershell/module/pkiclient/new-selfsignedcertificate) com os seguintes parâmetros para criar um certificado auto-assinado na loja de certificados do utilizador no seu computador: 

```powershell
$cert=New-SelfSignedCertificate -Subject "CN=DaemonConsoleCert" -CertStoreLocation "Cert:\CurrentUser\My"  -KeyExportPolicy Exportable -KeySpec Signature
```

Exporte este certificado para um ficheiro utilizando o snap-in do Certificado de [Utilizador Manage,](/dotnet/framework/wcf/feature-details/how-to-view-certificates-with-the-mmc-snap-in) acessível a partir do Painel de Controlo do Windows.

Para fazer o upload do certificado:

1. Selecione **Certificados e segredos.**
1. Selecione **o certificado de upload** e selecione o certificado (um certificado existente ou o certificado auto-assinado que exportou).

    ![Selecione o certificado de upload e selecione o que pretende adicionar](./media/howto-create-service-principal-portal/upload-cert.png)

1. Selecione **Adicionar**.

Depois de registar o certificado com a sua aplicação no portal de registo de candidaturas, tem de ativar o código de aplicação do cliente para utilizar o certificado.

### <a name="create-a-new-application-secret"></a>Criar um novo segredo de aplicação

Se optar por não utilizar um certificado, pode criar um novo segredo de aplicação.

1. Selecione **Certificados e segredos.**
1. Selecione **segredos de Cliente -> Novo segredo do cliente**.
1. Forneça uma descrição do segredo, e uma duração. Quando terminar, selecione **Adicionar**.

   Depois de salvar o segredo do cliente, o valor do segredo do cliente é mostrado. Copie este valor porque não conseguirá recuperar a chave mais tarde. Fornecerá o valor-chave com o ID da aplicação para iniciar sessão como aplicação. Armazene o valor da chave num local onde a aplicação o possa obter.

   ![Copie o valor secreto porque não pode recuperar isto mais tarde.](./media/howto-create-service-principal-portal/copy-secret.png)

## <a name="configure-access-policies-on-resources"></a>Configurar políticas de acesso aos recursos
Tenha em mente que poderá ser necessário configurar permissões de adição nos recursos a que a sua aplicação precisa de aceder. Por exemplo, também deve atualizar as políticas de [acesso de um cofre chave](/azure/key-vault/key-vault-secure-your-key-vault#data-plane-and-access-policies) para dar à sua aplicação acesso a chaves, segredos ou certificados.  

1. No [portal Azure,](https://portal.azure.com)navegue até ao seu cofre chave e selecione políticas de **acesso.**  
1. Selecione **Adicionar a política de acesso**e, em seguida, selecione as permissões de chave, segredo e certificado que pretende conceder ao seu pedido.  Selecione o diretor de serviço que criou anteriormente.
1. Selecione **Adicionar** para adicionar a política de acesso **e,** em seguida, Guardar para comprometer as suas alterações.
    ![Adicionar](./media/howto-create-service-principal-portal/add-access-policy.png) de política de acesso

## <a name="required-permissions"></a>Permissões obrigatórias

Deve ter permissões suficientes para registar uma candidatura com o seu inquilino Azure AD e atribuir à aplicação um papel na sua subscrição Azure.

### <a name="check-azure-ad-permissions"></a>Verifique as permissões da AD Azure

1. Selecione **Azure Active Directory**.
1. Reparem no vosso papel. Se tiver a função **de Utilizador,** deve certificar-se de que os não administradores podem registar aplicações.

   ![Encontre o seu papel. Se for utilizador, certifique-se de que os não administradores podem registar aplicações](./media/howto-create-service-principal-portal/view-user-info.png)

1. No painel esquerdo, selecione **as definições do utilizador**.
1. Verifique a definição de registos da **App.** Este valor só pode ser definido por um administrador. Se definido para **Sim**, qualquer utilizador do inquilino Azure AD pode registar uma aplicação.

Se a definição de registos da aplicação estiver definida para **Não,** apenas os utilizadores com uma função de administrador podem registar este tipo de aplicações. Consulte [as funções disponíveis](../users-groups-roles/directory-assign-admin-roles.md#available-roles) e as [permissões](../users-groups-roles/directory-assign-admin-roles.md#role-permissions) de funções para conhecer as funções de administrador disponíveis e as permissões específicas em Azure AD que são dadas a cada função. Se a sua conta for atribuída a função de Utilizador, mas a definição de registo da aplicação está limitada aos utilizadores de administração, peça ao seu administrador que lhe atribua uma das funções do administrador que possam criar e gerir todos os aspetos dos registos de aplicações, ou para permitir que os utilizadores se registem apps.

### <a name="check-azure-subscription-permissions"></a>Verifique as permissões de subscrição do Azure

Na subscrição do Azure, a sua conta deve ter acesso `Microsoft.Authorization/*/Write` para atribuir uma função a uma aplicação AD. Esta ação é concedida através das funções [Proprietário](../../role-based-access-control/built-in-roles.md#owner) ou [Administrador de Acesso dos Utilizadores](../../role-based-access-control/built-in-roles.md#user-access-administrator). Se a sua conta for atribuída a função **de Contribuinte,** não tem permissão adequada. Receberá um erro ao tentar atribuir uma função ao diretor de serviço.

Para verificar as suas permissões de subscrição:

1. Selecione a sua conta no canto superior direito e selecione... **-> Minhas permissões**.

   ![Selecione a sua conta e as suas permissões de utilizador](./media/howto-create-service-principal-portal/select-my-permissions.png)

1. A partir da lista de entrega, selecione a subscrição em que pretende criar o principal de serviço. Em seguida, selecione **Clique aqui para ver detalhes completos de acesso para esta subscrição**.

   ![Selecione a subscrição que pretende criar o principal de serviço em](./media/howto-create-service-principal-portal/view-details.png)

1. Selecione **atribuições** de Role para visualizar as suas funções atribuídas e determine se tem permissões adequadas para atribuir uma função a uma aplicação ad. Caso contrário, peça ao seu administrador de subscrição que o adicione à função de Administrador de Acesso ao Utilizador. Na seguinte imagem, é atribuído ao utilizador a função Proprietário, o que significa que o utilizador tem permissões adequadas.

   ![Este exemplo mostra que o utilizador é atribuído a função Proprietário](./media/howto-create-service-principal-portal/view-user-role.png)

## <a name="next-steps"></a>Passos seguintes

* Para aprender sobre a especificação das políticas de segurança, consulte [o Controlo de Acesso baseado em Papel Azure](../../role-based-access-control/role-assignments-portal.md).  
* Para obter uma lista de ações disponíveis que possam ser concedidas ou negadas aos utilizadores, consulte as operações do Fornecedor de Recursos do Gestor de [Recursos do Azure.](../../role-based-access-control/resource-provider-operations.md)
