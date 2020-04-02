---
title: Criar uma aplicação Azure AD no Azure Data Explorer
description: Saiba como criar uma aplicação Azure AD no Azure Data Explorer.
author: orspod
ms.author: orspodek
ms.reviewer: herauch
ms.service: data-explorer
ms.topic: conceptual
ms.date: 04/01/2020
ms.openlocfilehash: 64e8637630675120fece1bbe1fa4a57d97f36eb5
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/02/2020
ms.locfileid: "80550516"
---
# <a name="create-an-azure-active-directory-application-registration-in-azure-data-explorer"></a>Criar um registo de candidatura de Diretório Ativo Azure no Azure Data Explorer

A autenticação da aplicação Azure Ative Directory (Azure AD) é utilizada para aplicações, como um serviço não atendido ou um fluxo regular, que precisam de aceder ao Azure Data Explorer sem um utilizador presente. Se estiver a ligar-se a uma base de dados do Azure Data Explorer utilizando uma aplicação, como uma aplicação web, deverá autenticar usando a autenticação principal do serviço. Este artigo detalha como criar e registar um diretor de serviço da Azure AD e, em seguida, autorizá-lo a aceder a uma base de dados do Azure Data Explorer.

## <a name="create-azure-ad-application-registration"></a>Criar registo de candidatura da Azure AD

A autenticação da aplicação Azure AD requer a criação e registo de uma aplicação com a AD Azure. Um diretor de serviço é automaticamente criado quando o registo de candidatura é criado em um inquilino Da D. Azure. 

1. Inicie sessão no portal `Azure Active Directory` [Azure](https://portal.azure.com) e abra a lâmina

    ![Selecione Diretório Ativo Azure a partir do menu do portal](media/provision-azure-ad-app/create-app-reg-select-azure-active-directory.png)

1. Selecione a lâmina de **registos** da App e selecione **Novo registo**

    ![Iniciar uma nova inscrição de aplicativos](media/provision-azure-ad-app/create-app-reg-new-registration.png)

1. Preencha as seguintes informações: 

    * **Nome** 
    * **Tipos de conta suportados**
    * **Redirecione a Web URI** > **Web**
        > [!IMPORTANT] 
        > O tipo de aplicação deve ser **Web**. O URI é opcional e é deixado em branco neste caso.
    * Selecione **Registar**

    ![Registar nova inscrição de aplicativos](media/provision-azure-ad-app/create-app-reg-register-app.png)

1. Selecione a lâmina **de visão geral** e copie o ID da **aplicação**.

    > [!NOTE]
    > Vai precisar do ID da aplicação para autorizar o diretor de serviço a aceder à base de dados.

    ![Copiar identificação de registo de aplicativos](media/provision-azure-ad-app/create-app-reg-copy-applicationid.png)

1. Nos **Certificados & lâmina de segredos,** selecione **Novo segredo do cliente**

    ![Iniciar a criação do segredo do cliente](media/provision-azure-ad-app/create-app-reg-new-client-secret.png)

    > [!TIP]
    > Este artigo descreve o uso de um segredo de cliente para as credenciais da aplicação.  Também pode utilizar um certificado X509 para autenticar a sua aplicação. Selecione **o certificado de upload** e siga as instruções para fazer o upload da parte pública do certificado.

1. Introduza uma descrição, expiração e **selecione Adicionar**

    ![Introduza parâmetros secretos do cliente](media/provision-azure-ad-app/create-app-reg-enter-client-secret-details.png)

1. Copie o valor da chave.

    > [!NOTE]
    > Quando sair desta página, o valor-chave não será acessível.  Vai precisar da chave para configurar as credenciais dos clientes na base de dados.

    ![Copiar valor-chave secreto do cliente](media/provision-azure-ad-app/create-app-reg-copy-client-secret.png)

A sua aplicação foi criada. Se necessitar apenas de acesso a um recurso autorizado do Explorador de Dados Azure, como no exemplo programático abaixo, ignore a secção seguinte. Para obter o suporte de permissões delegados, consulte a configuração das [permissões delegadas para o registo da candidatura](#configure-delegated-permissions-for-the-application-registration).

## <a name="configure-delegated-permissions-for-the-application-registration"></a>Configure permissões delegadas para o registo de candidaturas

Se a sua aplicação necessitar de aceder ao Azure Data Explorer utilizando as credenciais do utilizador de chamadas, configure as permissões delegadas para o registo da sua candidatura. Por exemplo, se estiver a construir uma API web para aceder ao Azure Data Explorer e quiser autenticar usando as credenciais do utilizador que está *a chamar* a sua API.  

1. Na lâmina de **permissões API,** selecione **Adicionar uma permissão**.
1. Selecione **APIs que a minha organização usa.** Procure e selecione **Azure Data Explorer**.

    ![Adicionar permissão aPi do Explorador de Dados Azure](media/provision-azure-ad-app/configure-delegated-add-api-permission.png)

1. Nas **permissões delegadas,** selecione a caixa **user_impersonation** e **adicione permissões**

    ![Selecione permissões delegadas com personificação do utilizador](media/provision-azure-ad-app/configure-delegated-click-add-permissions.png)     

## <a name="grant-the-service-principal-access-to-an-azure-data-explorer-database"></a>Conceda o principal acesso ao serviço principal a uma base de dados do Azure Data Explorer

Agora que o registo principal de aplicação do seu serviço é criado, você precisa conceder o serviço correspondente de acesso principal à sua base de dados Do Explorador de Dados Azure. 

1. Na [Web UI,](https://dataexplorer.azure.com/)ligue-se à sua base de dados e abra um separador de consulta.

1. Execute o seguinte comando:

    ```kusto
    .add database <DatabaseName> viewers ('<ApplicationId>') '<Notes>'
    ```

    Por exemplo:
    
    ```kusto
    .add database Logs viewers ('aadapp=f778b387-ba15-437f-a69e-ed9c9225278b') 'Azure Data Explorer App Registration'
    ```

    O último parâmetro é uma cadeia que aparece como notas quando se consultam os papéis associados a uma base de dados.
    
    > [!NOTE]
    > Após a criação do registo de candidatura, pode haver um atraso de vários minutos até que o Azure Data Explorer possa remeti-lo. Se, ao executar o comando, receber um erro de que a aplicação não for encontrada, espere e tente novamente.

Para mais informações, consulte a [gestão](/azure/kusto/management/security-roles) de funções de segurança e [permissões de ingestão.](/azure/kusto/api/netfx/kusto-ingest-client-permissions.md)  

## <a name="using-application-credentials-to-access-a-database"></a>Utilização de credenciais de aplicação para aceder a uma base de dados

Utilize as credenciais de aplicação para aceder programáticamente à sua base de dados utilizando a [biblioteca de clientes do Azure Data Explorer.](/azure/kusto/api/netfx/about-kusto-data.md)

```C#
. . .
string applicationClientId = "<myClientID>";
string applicationKey = "<myApplicationKey>";
. . .
var kcsb = new KustoConnectionStringBuilder($"https://{clusterName}.kusto.windows.net/{databaseName}")
    .WithAadApplicationKeyAuthentication(
        applicationClientId,
        applicationKey,
        authority);
var client = KustoClientFactory.CreateCslQueryProvider(kcsb);
var queryResult = client.ExecuteQuery($"{query}");
```

   > [!NOTE]
   > Especifique o id de aplicação e a chave do registo de candidatura (diretor de serviço) criado anteriormente.

Para mais informações, consulte [autenticar com Azure AD para acesso](/azure/kusto/management/access-control/how-to-authenticate-with-aad) ao Azure Data Explorer e utilize o [Azure Key Vault com a aplicação web .NET Core](/azure/key-vault/tutorial-net-create-vault-azure-web-app#create-a-net-core-web-app).

## <a name="troubleshooting"></a>Resolução de problemas

### <a name="invalid-resource-error"></a>Erro de recurso inválido

Se a sua aplicação for utilizada para autenticar utilizadores ou aplicações para acesso ao Azure Data Explorer, deve configurar permissões delegadas para a aplicação de serviço do Azure Data Explorer. Declarar que a sua aplicação pode autenticar utilizadores ou aplicações para acesso ao Azure Data Explorer. Não o fazer resultará num erro semelhante ao seguinte, quando for feita uma tentativa de autenticação:

`AADSTS650057: Invalid resource. The client has requested access to a resource which is not listed in the requested permissions in the client's application registration...`

Terá de seguir as instruções sobre a criação de [permissões delegadas para](#configure-delegated-permissions-for-the-application-registration)a aplicação do serviço Azure Data Explorer .

### <a name="enable-user-consent-error"></a>Ativar o erro de consentimento do utilizador

O administrador de inquilinos da Azure AD pode promulgar uma política que impeça os utilizadores de inquilinos de darem consentimento às candidaturas. Esta situação resultará num erro semelhante ao seguinte, quando um utilizador tenta iniciar sessão na sua aplicação:

`AADSTS65001: The user or administrator has not consented to use the application with ID '<App ID>' named 'App Name'`

Terá de contactar o seu administrador da AD Azure para conceder o consentimento a todos os utilizadores do arrendatário ou permitir o consentimento do utilizador para a sua aplicação específica.

## <a name="next-steps"></a>Passos seguintes

* Consulte as cordas de [ligação Kusto](/azure/kusto/api/connection-strings/kusto.md) para obter a lista de cordas de ligação suportadas.
