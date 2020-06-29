---
title: Criar uma app AD AD & principal no portal
titleSuffix: Microsoft identity platform
description: Crie uma nova aplicação Azure Ative Directory & principal de serviço para gerir o acesso a recursos com controlo de acesso baseado em funções no Azure Resource Manager.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.date: 04/01/2020
ms.author: ryanwi
ms.reviewer: tomfitz
ms.custom: aaddev, seoapril2019, identityplatformtop40
ms.openlocfilehash: 91abfc395c84ad49079fcaa55c20d641ee4e7b49
ms.sourcegitcommit: 1d9f7368fa3dadedcc133e175e5a4ede003a8413
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/27/2020
ms.locfileid: "85478115"
---
# <a name="how-to-use-the-portal-to-create-an-azure-ad-application-and-service-principal-that-can-access-resources"></a>How to: Utilizar o portal para criar uma aplicação e um principal de serviço do Azure AD que possam aceder aos recursos

Este artigo mostra-lhe como criar uma nova aplicação e principal de serviço Azure Ative Directory (Azure AD) que pode ser usado com o controlo de acesso baseado em funções. Quando tem um código que precisa de aceder ou modificar recursos, pode criar uma identidade para a aplicação. Esta identidade é conhecida como um principal de serviço. Em seguida, pode atribuir as permissões necessárias ao principal serviço. Este artigo mostra-lhe como usar o portal para criar o principal serviço. Centra-se numa aplicação de um único inquilino em que o pedido se destina a funcionar dentro de apenas uma organização. Você normalmente usa aplicações de inquilino único para aplicações de linha de negócio que funcionam dentro da sua organização.

> [!IMPORTANT]
> Em vez de criar um principal de serviço, considere usar identidades geridas para recursos Azure para a sua identidade de aplicação. Se o seu código funciona num serviço que suporta identidades geridas e acede a recursos que suportam a autenticação AD do Azure, as identidades geridas são uma opção melhor para si. Para saber mais sobre identidades geridas para os recursos da Azure, incluindo quais os serviços que atualmente o suportam, veja [o que é gerida identidades para os recursos do Azure?](../managed-identities-azure-resources/overview.md)

## <a name="create-an-azure-active-directory-application"></a>Criar uma aplicação de Diretório Ativo Azure

Vamos saltar diretamente para criar a identidade. Se tiver algum problema, verifique as [permissões necessárias](#required-permissions) para se certificar de que a sua conta pode criar a identidade.

1. Inscreva-se na sua Conta Azure através do [portal Azure](https://portal.azure.com).
1. Selecione **Azure Active Directory**.
1. Selecione **Registos das aplicações**.
1. Selecione **Novo registo**.
1. Diga o nome do pedido. Selecione um tipo de conta suportado, que determina quem pode usar a aplicação. Em **Redirect URI**, selecione **Web** para o tipo de aplicação que pretende criar. Insira o URI para onde o token de acesso é enviado. Não é possível criar credenciais para uma [aplicação nativa.](../manage-apps/application-proxy-configure-native-client-application.md) Não podes usar esse tipo para uma aplicação automatizada. Depois de definir os valores, **selecione Registar- se**.

   ![Digite um nome para a sua aplicação](./media/howto-create-service-principal-portal/create-app.png)

Criou o seu azure AD e o seu diretor de serviço.

## <a name="assign-a-role-to-the-application"></a>Atribuir uma função à aplicação

Para aceder aos recursos na sua subscrição, tem de atribuir uma função à aplicação. Decida qual a função que oferece as permissões certas para o pedido. Para conhecer as funções disponíveis, consulte [o RBAC: Construído em Papéis.](../../role-based-access-control/built-in-roles.md)

Pode definir o âmbito ao nível da subscrição, grupo de recursos ou recurso. As permissões são herdadas para níveis mais baixos de âmbito. Por exemplo, adicionar uma aplicação à função Reader para um grupo de recursos significa que pode ler o grupo de recursos e quaisquer recursos que contenha.

1. No portal Azure, selecione o nível de âmbito a que pretende atribuir a aplicação. Por exemplo, para atribuir uma função no âmbito de subscrição, procurar e selecionar **Subscrições**, ou selecionar **Subscrições** na página **Inicial.**

   ![Por exemplo, atribuir uma função no âmbito de subscrição](./media/howto-create-service-principal-portal/select-subscription.png)

1. Selecione a subscrição específica para atribuir o pedido.

   ![Selecione subscrição para a atribuição](./media/howto-create-service-principal-portal/select-one-subscription.png)

   Se não vir a subscrição que procura, selecione **filtro global de subscrições**. Certifique-se de que a subscrição desejada está selecionada para o portal.

1. Selecione **Controlo de acesso (IAM)** .
1. Selecione **Adicionar atribuição de função**.
1. Selecione a função que deseja atribuir à aplicação. Por exemplo, para permitir que a aplicação execute ações como **reiniciar,** **iniciar** e **parar** instâncias, selecione a **função Contribuinte.**  Leia mais sobre as [funções disponíveis](../../role-based-access-control/built-in-roles.md) Por padrão, as aplicações AD do Azure não são apresentadas nas opções disponíveis. Para encontrar a sua aplicação, procure o nome e selecione-o.

   ![Selecione a função a atribuir à aplicação](./media/howto-create-service-principal-portal/select-role.png)

1. **Selecione Guardar** para terminar a atribuição do papel. Vê a sua aplicação na lista de utilizadores com uma função para esse âmbito.

O seu diretor de serviço está preparado. Pode começar a usá-lo para executar os seus scripts ou aplicativos. A próxima secção mostra como obter valores que são necessários ao assinar programáticamente.

## <a name="get-values-for-signing-in"></a>Obtenha valores para iniciar sessão

Ao iniciar sessão programática, tem de passar a identificação do inquilino com o seu pedido de autenticação. Também precisa do ID para a sua aplicação e uma chave de autenticação. Para obter esses valores, utilize os seguintes passos:

1. Selecione **Azure Active Directory**.
1. A partir de inscrições de **Aplicações** em Azure AD, selecione a sua aplicação.
1. Copie o ID do Diretório (inquilino) e guarde-o no seu código de inscrição.

    ![Copie o diretório (ID do inquilino) e guarde-o no seu código de aplicações](./media/howto-create-service-principal-portal/copy-tenant-id.png)

1. Copie o **ID da Aplicação** e armazene-o no código da aplicação.

   ![Copiar o ID da aplicação (cliente)](./media/howto-create-service-principal-portal/copy-app-id.png)

## <a name="certificates-and-secrets"></a>Certificados e segredos
As aplicações da Daemon podem usar duas formas de credenciais para autenticar com Azure AD: certificados e segredos de aplicação.  Recomendamos a utilização de um certificado, mas também pode criar um novo segredo de aplicação.

### <a name="upload-a-certificate"></a>Faça upload de um certificado

Pode usar um certificado existente se tiver um.  Opcionalmente, pode criar um certificado auto-assinado *apenas*para fins de teste . Abra o PowerShell e [execute o Certificado New-SelfSigned](/powershell/module/pkiclient/new-selfsignedcertificate) com os seguintes parâmetros para criar um certificado auto-assinado na loja de certificados do utilizador no seu computador: 

```powershell
$cert=New-SelfSignedCertificate -Subject "CN=DaemonConsoleCert" -CertStoreLocation "Cert:\CurrentUser\My"  -KeyExportPolicy Exportable -KeySpec Signature
```

Exporte este certificado para um ficheiro utilizando o snap-in MMC do [Certificado de Utilizador Gerido](/dotnet/framework/wcf/feature-details/how-to-view-certificates-with-the-mmc-snap-in) acessível a partir do Painel de Controlo do Windows.

1. Selecione **Executar** a partir do menu **Iniciar** e, em seguida, insira **certmgr.msc**.

   Aparece a ferramenta Certificate Manager para o utilizador atual.

1. Para ver os seus certificados, em **Certificados - Utilizador Atual** no painel esquerdo, expanda o **diretório pessoal.**
1. Clique com o botão direito no certificado que criou, selecione **Todas as tarefas >Exportação**.
1. Siga o assistente de exportação de certificados.  Não exporte a chave privada e exporte para um . Arquivo CER.

Para fazer o upload do certificado:

1. Selecione **Azure Active Directory**.
1. A partir de inscrições de **Aplicações** em Azure AD, selecione a sua aplicação.
1. Selecione **Certificados & segredos**.
1. Selecione **o certificado de upload** e selecione o certificado (um certificado existente ou o certificado auto-assinado que exportou).

    ![Selecione o certificado de upload e selecione o que pretende adicionar](./media/howto-create-service-principal-portal/upload-cert.png)

1. Selecione **Adicionar**.

Depois de registar o certificado com a sua candidatura no portal de registo de candidaturas, é necessário ativar o código de candidatura do cliente para utilizar o certificado.

### <a name="create-a-new-application-secret"></a>Criar um novo segredo de aplicação

Se optar por não utilizar um certificado, pode criar um novo segredo de aplicação.

1. Selecione **Certificados & segredos**.
1. Selecione **segredos do Cliente - > novo segredo do cliente**.
1. Forneça uma descrição do segredo, e uma duração. Quando terminar, **selecione Adicionar**.

   Depois de salvar o segredo do cliente, o valor do segredo do cliente é exibido. Copie este valor porque não poderá recuperar a chave mais tarde. Irá fornecer o valor-chave com o ID da aplicação para iniciar sinscreve-se como a aplicação. Armazene o valor da chave num local onde a aplicação o possa obter.

   ![Copie o valor secreto porque não pode recuperar isto mais tarde](./media/howto-create-service-principal-portal/copy-secret.png)

## <a name="configure-access-policies-on-resources"></a>Configure políticas de acesso aos recursos
Tenha em mente que poderá ter de configurar permissões adicionais sobre recursos a que a sua aplicação necessita de aceder. Por exemplo, também deve [atualizar as políticas de acesso de um cofre chave](/azure/key-vault/key-vault-secure-your-key-vault#data-plane-and-access-policies) para dar acesso à sua aplicação a chaves, segredos ou certificados.  

1. No [portal Azure,](https://portal.azure.com)navegue para o cofre e selecione **as políticas de acesso**.  
1. **Selecione Adicionar a política de acesso,** em seguida, selecione as permissões de chave, segredo e certificado que pretende conceder à sua candidatura.  Selecione o principal de serviço que criou anteriormente.
1. **Selecione Adicionar** para adicionar a política de acesso e, em seguida, **guardar** para cometer as suas alterações.
    ![Adicionar política de acesso](./media/howto-create-service-principal-portal/add-access-policy.png)

## <a name="required-permissions"></a>Permissões obrigatórias

Você deve ter permissões suficientes para registar um pedido com o seu inquilino Azure AD, e atribuir à aplicação um papel na sua subscrição Azure.

### <a name="check-azure-ad-permissions"></a>Verifique permissões AZure AD

1. Selecione **Azure Active Directory**.
1. Reparem no vosso papel. Se tiver a função **De Utilizador,** deve certificar-se de que os não administradores podem registar as aplicações.

   ![Encontre o seu papel. Se for um Utilizador, certifique-se de que os administradores não-administradores podem registar aplicações](./media/howto-create-service-principal-portal/view-user-info.png)

1. No painel esquerdo, selecione **as definições do Utilizador**.
1. Verifique a definição de registos da **App.** Este valor só pode ser definido por um administrador. Se definido para **Sim,** qualquer utilizador no inquilino Azure AD pode registar uma aplicação.

Se a definição de registos de aplicações estiver definida como **Nº,** apenas os utilizadores com uma função de administrador podem registar este tipo de aplicações. Consulte [as funções disponíveis](../users-groups-roles/directory-assign-admin-roles.md#available-roles) e [permissões de função](../users-groups-roles/directory-assign-admin-roles.md#role-permissions) para saber sobre as funções de administrador disponíveis e as permissões específicas em Azure AD que são dadas a cada papel. Se a sua conta tiver sido atribuída à função Utilizador, mas a definição de registo da aplicação está limitada aos utilizadores administrativos, peça ao seu administrador para lhe atribuir uma das funções de administrador que possa criar e gerir todos os aspetos dos registos de aplicações, ou para permitir que os utilizadores registem aplicações.

### <a name="check-azure-subscription-permissions"></a>Verifique permissões de subscrição do Azure

Na sua subscrição Azure, a sua conta deve ter `Microsoft.Authorization/*/Write` acesso para atribuir uma função a uma aplicação AD. Esta ação é concedida através das funções [Proprietário](../../role-based-access-control/built-in-roles.md#owner) ou [Administrador de Acesso dos Utilizadores](../../role-based-access-control/built-in-roles.md#user-access-administrator). Se a sua conta for atribuída à **função Contribuinte,** não tem a devida permissão. Receberá um erro ao tentar atribuir ao diretor de serviço um papel.

Para verificar as permissões de subscrição:

1. Procure e selecione **Subscrições**ou selecione **Subscrições** na página **inicial.**

   ![Pesquisar](./media/howto-create-service-principal-portal/select-subscription.png)

1. Selecione a subscrição em que pretende criar o principal serviço em.

   ![Selecione subscrição para a atribuição](./media/howto-create-service-principal-portal/select-one-subscription.png)

   Se não vir a subscrição que procura, selecione **filtro global de subscrições**. Certifique-se de que a subscrição desejada está selecionada para o portal.

1. Selecione **as minhas permissões.** Em seguida, **selecione Clique aqui para ver detalhes completos de acesso para esta subscrição**.

   ![Selecione a subscrição que pretende criar o principal de serviço em](./media/howto-create-service-principal-portal/view-details.png)

1. Selecione **Ver** nas **atribuições de Função** para ver as suas funções atribuídas e determinar se tem permissões adequadas para atribuir uma função a uma aplicação AD. Caso contrário, peça ao administrador de subscrição para o adicionar à função de Administrador de Acesso ao Utilizador. Na imagem seguinte, o utilizador é atribuído à função Proprietário, o que significa que o utilizador tem permissões adequadas.

   ![Este exemplo mostra que o utilizador é atribuído à função Proprietário](./media/howto-create-service-principal-portal/view-user-role.png)

## <a name="next-steps"></a>Passos seguintes

* Para saber especificar políticas de segurança, consulte [o Controlo de Acesso baseado em funções Azure.](../../role-based-access-control/role-assignments-portal.md)  
* Para obter uma lista de ações disponíveis que podem ser concedidas ou negadas aos utilizadores, consulte [as operações do Fornecedor de Recursos do Gestor de Recursos da Azure.](../../role-based-access-control/resource-provider-operations.md)
