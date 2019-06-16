---
title: Gerir os recursos de conta com a biblioteca de cliente para .NET - Azure Batch | Documentos da Microsoft
description: Criar, eliminar e modificar recursos da conta do Azure Batch com a biblioteca de gestão de lotes .NET.
services: batch
documentationcenter: .net
author: laurenhughes
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 16279b23-60ff-4b16-b308-5de000e4c028
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 04/24/2017
ms.author: lahugh
ms.custom: seodec18
ms.openlocfilehash: 301a3f9a500c41cf13dfa071d3526d2128b5e131
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60775139"
---
# <a name="manage-batch-accounts-and-quotas-with-the-batch-management-client-library-for-net"></a>Gerir contas do Batch e quotas com a biblioteca de cliente de gestão do Batch para .NET

> [!div class="op_single_selector"]
> * [Portal do Azure](batch-account-create-portal.md)
> * [Gestão de Batch .NET](batch-management-dotnet.md)
> 
> 

Pode reduzir manutenção sobrecarga nas suas aplicações do Azure Batch ao utilizar o [gestão de lotes .NET] [ api_mgmt_net] biblioteca para automatizar a criação de contas do Batch, eliminação, gestão de chaves e deteção de quota.

* **Criar e eliminar as contas do Batch** dentro de qualquer região. Se, como um fornecedor independente de software (ISV) por exemplo, fornecer um serviço para os seus clientes em que cada um é atribuído uma conta de Batch separada para efeitos de faturação, pode adicionar capacidades de criação e eliminação de conta para o seu portal de cliente.
* **Obter e voltar a gerar chaves de conta** por meio de programação para qualquer uma das suas contas do Batch. Isso pode ajudá-lo a estar em conformidade com as políticas de segurança que impõem periódica rollover ou expiração de chaves de conta. Quando tiver várias contas do Batch em várias regiões do Azure, automação desse processo de rollover aumenta a eficiência da sua solução.
* **Verifique as quotas de conta** e os imprevistos tentativa e erro da determinar quais contas do Batch têm os limites. Ao selecionar as suas quotas de conta antes de iniciar trabalhos, criar conjuntos, ou adicionar nós de computação, pode ajustar proativamente onde ou quando eles computação recursos são criados. Pode determinar quais as contas que necessitam de quota aumenta antes de alocar recursos adicionais nessas contas.
* **A combinação de recursos de outros serviços do Azure** para uma experiência de gestão completo – com o Batch Management .NET, [do Azure Active Directory][aad_about]e o [Azure Gestor de recursos] [ resman_overview] em conjunto no mesmo aplicativo. Ao usar esses recursos e suas APIs, pode fornecer uma experiência de autenticação sem conflitos, a capacidade de criar e eliminar grupos de recursos e as capacidades que são descritas acima para uma solução de gerenciamento de ponta a ponta.

> [!NOTE]
> Embora este artigo enfoca o gerenciamento programático de suas contas de Batch, chaves e quotas, pode efetuar muitas dessas atividades utilizando o [portal do Azure][azure_portal]. Para obter mais informações, consulte [criar uma conta do Azure Batch no portal do Azure](batch-account-create-portal.md) e [Quotas e limites para o serviço Azure Batch](batch-quota-limit.md).
> 
> 

## <a name="create-and-delete-batch-accounts"></a>Criar e eliminar as contas do Batch
Como mencionado, um dos principais recursos da API de gestão do Batch é criar e eliminar contas do Batch numa região do Azure. Para tal, utilize [BatchManagementClient.Account.CreateAsync] [ net_create] e [DeleteAsync][net_delete], ou os respectivos correspondentes síncronos.

O fragmento de código seguinte cria uma conta, obtém a conta recentemente criada do serviço Batch e, em seguida, elimina-lo. Este trecho de código e os outros neste artigo, `batchManagementClient` é uma instância totalmente inicializada do [BatchManagementClient][net_mgmt_client].

```csharp
// Create a new Batch account
await batchManagementClient.Account.CreateAsync("MyResourceGroup",
    "mynewaccount",
    new BatchAccountCreateParameters() { Location = "West US" });

// Get the new account from the Batch service
AccountResource account = await batchManagementClient.Account.GetAsync(
    "MyResourceGroup",
    "mynewaccount");

// Delete the account
await batchManagementClient.Account.DeleteAsync("MyResourceGroup", account.Name);
```

> [!NOTE]
> Aplicações que utilizam a biblioteca de gestão de lotes .NET e sua classe de BatchManagementClient exigir **administrador de serviços** ou **coadministrador** acesso à subscrição que possui o Batch conta para serem geridos. Para obter mais informações, consulte a secção Azure Active Directory e o [AccountManagement] [ acct_mgmt_sample] exemplo de código.
> 
> 

## <a name="retrieve-and-regenerate-account-keys"></a>Obter e voltar a gerar chaves de conta
Obter chaves da conta primária e secundária de qualquer conta do Batch na sua subscrição, utilizando [ListKeysAsync][net_list_keys]. Pode voltar a gerar essas chaves utilizando [RegenerateKeyAsync][net_regenerate_keys].

```csharp
// Get and print the primary and secondary keys
BatchAccountListKeyResult accountKeys =
    await batchManagementClient.Account.ListKeysAsync(
        "MyResourceGroup",
        "mybatchaccount");
Console.WriteLine("Primary key:   {0}", accountKeys.Primary);
Console.WriteLine("Secondary key: {0}", accountKeys.Secondary);

// Regenerate the primary key
BatchAccountRegenerateKeyResponse newKeys =
    await batchManagementClient.Account.RegenerateKeyAsync(
        "MyResourceGroup",
        "mybatchaccount",
        new BatchAccountRegenerateKeyParameters() {
            KeyName = AccountKeyType.Primary
            });
```

> [!TIP]
> Pode criar um fluxo de trabalho simplificado de ligação para as suas aplicações de gestão. Em primeiro lugar, obtenha uma chave de conta para a conta de Batch que pretende gerir com [ListKeysAsync][net_list_keys]. Em seguida, utilize esta chave ao inicializar a biblioteca de .NET do Batch [BatchSharedKeyCredentials] [ net_sharedkeycred] classe, que é utilizado ao inicializar [BatchClient] [ net_batch_client].
> 
> 

## <a name="check-azure-subscription-and-batch-account-quotas"></a>Verifique a subscrição do Azure e quotas de contas do Batch
As subscrições do Azure e os serviços do Azure individuais, como o Batch todos tem quotas predefinidas que limitam o número de determinadas entidades dentro dos mesmos. Para as quotas predefinidas para subscrições do Azure, consulte [subscrição do Azure e limites do serviço, quotas e restrições](../azure-subscription-service-limits.md). Para as quotas predefinidas do serviço Batch, consulte [Quotas e limites para o serviço Azure Batch](batch-quota-limit.md). Ao utilizar a biblioteca de gestão de lotes .NET, pode verificar destas quotas em seus aplicativos. Isto permite-lhe tomar decisões de alocação antes de adicionar contas ou recursos, como conjuntos de computação e nós de computação.

### <a name="check-an-azure-subscription-for-batch-account-quotas"></a>Verificar uma subscrição do Azure para quotas de contas do Batch
Antes de criar uma conta do Batch numa região, pode verificar a sua subscrição do Azure para ver se é possível adicionar uma conta nessa região.

No fragmento de código abaixo, usamos primeiro [BatchManagementClient.Account.ListAsync] [ net_mgmt_listaccounts] para obter uma coleção de todas as contas de Batch que estão dentro de uma subscrição. Uma vez que obtém essa coleção, podemos determinar quantas contas são na região de destino. Em seguida, usamos [BatchManagementClient.Subscriptions] [ net_mgmt_subscriptions] para obter a quota de conta do Batch e determinar quantas contas (se houver) podem ser criadas nessa região.

```csharp
// Get a collection of all Batch accounts within the subscription
BatchAccountListResponse listResponse =
        await batchManagementClient.Account.ListAsync(new AccountListParameters());
IList<AccountResource> accounts = listResponse.Accounts;
Console.WriteLine("Total number of Batch accounts under subscription id {0}:  {1}",
    creds.SubscriptionId,
    accounts.Count);

// Get a count of all accounts within the target region
string region = "westus";
int accountsInRegion = accounts.Count(o => o.Location == region);

// Get the account quota for the specified region
SubscriptionQuotasGetResponse quotaResponse = await batchManagementClient.Subscriptions.GetSubscriptionQuotasAsync(region);
Console.WriteLine("Account quota for {0} region: {1}", region, quotaResponse.AccountQuota);

// Determine how many accounts can be created in the target region
Console.WriteLine("Accounts in {0}: {1}", region, accountsInRegion);
Console.WriteLine("You can create {0} accounts in the {1} region.", quotaResponse.AccountQuota - accountsInRegion, region);
```

No fragmento acima, `creds` é uma instância de [TokenCloudCredentials][azure_tokencreds]. Para ver um exemplo de como criar este objeto, consulte a [AccountManagement] [ acct_mgmt_sample] exemplo de código no GitHub.

### <a name="check-a-batch-account-for-compute-resource-quotas"></a>Verificar uma conta do Batch para quotas de recursos de computação
Antes de aumentar os recursos de computação na sua solução do Batch, pode verificar garantir que os recursos que pretende alocar não exceda as quotas da conta. No fragmento de código abaixo, podemos imprimir as informações de quota para a conta do Batch com o nome `mybatchaccount`. Em seu próprio aplicativo, poderia usar essas informações para determinar se a conta pode lidar com os recursos adicionais a ser criada.

```csharp
// First obtain the Batch account
BatchAccountGetResponse getResponse =
    await batchManagementClient.Account.GetAsync("MyResourceGroup", "mybatchaccount");
AccountResource account = getResponse.Resource;

// Now print the compute resource quotas for the account
Console.WriteLine("Core quota: {0}", account.Properties.CoreQuota);
Console.WriteLine("Pool quota: {0}", account.Properties.PoolQuota);
Console.WriteLine("Active job and job schedule quota: {0}", account.Properties.ActiveJobAndJobScheduleQuota);
```

> [!IMPORTANT]
> Embora haja quotas predefinidas para os serviços e subscrições do Azure, muitos destes limites podem ser aumentados ao emitir um pedido no [portal do Azure][azure_portal]. Por exemplo, veja [Quotas e limites para o serviço Azure Batch](batch-quota-limit.md) para obter instruções sobre como aumentar as suas quotas de conta do Batch.
> 
> 

## <a name="use-azure-ad-with-batch-management-net"></a>Utilizar o Azure AD com a gestão de Batch .NET

A biblioteca de gestão de lotes .NET é um cliente do fornecedor de recursos do Azure e é utilizada em conjunto com [do Azure Resource Manager] [ resman_overview] para gerir programaticamente os recursos de conta. O Azure AD é necessário para autenticar pedidos feitos por meio de qualquer cliente de fornecedor de recursos do Azure, incluindo a biblioteca de gestão de lotes .NET e [do Azure Resource Manager][resman_overview]. Para obter informações sobre a utilização do Azure AD com a biblioteca de gestão de lotes .NET, consulte [Use Azure Active Directory para autenticar as soluções do Batch](batch-aad-auth.md). 

## <a name="sample-project-on-github"></a>Projeto de exemplo no GitHub

Para ver a gestão de lotes .NET em ação, veja a [AccountManagement] [ acct_mgmt_sample] projeto de exemplo no GitHub. O aplicativo de exemplo de AccountManagement demonstra as seguintes operações:

1. Adquirir um token de segurança do Azure AD utilizando [ADAL][aad_adal]. Se o utilizador não está já iniciou sessão, é-lhes pedido para as respetivas credenciais do Azure.
2. Com o token de segurança obtido a partir do Azure AD, crie uma [SubscriptionClient] [ resman_subclient] a consulta do Azure para obter uma lista das subscrições associadas à conta. O usuário pode selecionar uma subscrição na lista se contiver mais de uma assinatura.
3. Obtenha as credenciais associadas com a subscrição selecionada.
4. Criar uma [ResourceManagementClient] [ resman_client] objeto utilizando as credenciais.
5. Utilize um [ResourceManagementClient] [ resman_client] objeto para criar um grupo de recursos.
6. Utilize um [BatchManagementClient] [ net_mgmt_client] objeto para efetuar várias operações de conta do Batch:
   * Crie uma conta do Batch no novo grupo de recursos.
   * Obter a conta recentemente criada do serviço Batch.
   * As chaves de conta para a nova conta de impressão.
   * Voltar a gerar uma nova chave primária para a conta.
   * As informações de quota para a conta de impressão.
   * As informações de quota para a subscrição de impressão.
   * Imprima todas as contas na subscrição.
   * Elimine conta recentemente criada.
7. Elimine o grupo de recursos.

Antes de eliminar o grupo de recursos e a conta de Batch recém-criado, pode visualizá-las no [portal do Azure][azure_portal]:

Para executar o aplicativo de exemplo com êxito, tem primeiro de registá-lo no seu inquilino do Azure AD no portal do Azure e conceder permissões para a API do Azure Resource Manager. Siga as etapas fornecidas [soluções de gestão de Batch de autenticar com o Active Directory](batch-aad-auth-management.md).


[aad_about]:../active-directory/fundamentals/active-directory-whatis.md "O que é o Azure Active Directory?"
[aad_adal]: ../active-directory/active-directory-authentication-libraries.md
[aad_auth_scenarios]:../active-directory/develop/authentication-scenarios.md "Cenários de autenticação do Azure AD"
[aad_integrate]:../active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad.md "Integrar aplicações com o Azure Active Directory"
[acct_mgmt_sample]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/AccountManagement
[api_net]: https://msdn.microsoft.com/library/azure/mt348682.aspx
[api_mgmt_net]: https://msdn.microsoft.com/library/azure/mt463120.aspx
[azure_portal]: https://portal.azure.com
[azure_storage]: https://azure.microsoft.com/services/storage/
[azure_tokencreds]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.tokencloudcredentials.aspx
[batch_explorer_project]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchExplorer
[net_batch_client]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient.aspx
[net_list_keys]: https://msdn.microsoft.com/library/azure/microsoft.azure.management.batch.accountoperationsextensions.listkeysasync.aspx
[net_create]: https://msdn.microsoft.com/library/azure/microsoft.azure.management.batch.accountoperationsextensions.createasync.aspx
[net_delete]: https://msdn.microsoft.com/library/azure/microsoft.azure.management.batch.accountoperationsextensions.deleteasync.aspx
[net_regenerate_keys]: https://msdn.microsoft.com/library/azure/microsoft.azure.management.batch.accountoperationsextensions.regeneratekeyasync.aspx
[net_sharedkeycred]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.auth.batchsharedkeycredentials.aspx
[net_mgmt_client]: https://msdn.microsoft.com/library/azure/microsoft.azure.management.batch.batchmanagementclient.aspx
[net_mgmt_subscriptions]: https://msdn.microsoft.com/library/azure/microsoft.azure.management.batch.batchmanagementclient.subscriptions.aspx
[net_mgmt_listaccounts]: https://msdn.microsoft.com/library/azure/microsoft.azure.management.batch.iaccountoperations.listasync.aspx
[resman_api]: https://msdn.microsoft.com/library/azure/mt418626.aspx
[resman_client]: https://msdn.microsoft.com/library/azure/microsoft.azure.management.resources.resourcemanagementclient.aspx
[resman_subclient]: https://msdn.microsoft.com/library/azure/microsoft.azure.subscriptions.subscriptionclient.aspx
[resman_overview]: ../azure-resource-manager/resource-group-overview.md

[1]: ./media/batch-management-dotnet/portal-01.png
[2]: ./media/batch-management-dotnet/portal-02.png
[3]: ./media/batch-management-dotnet/portal-03.png
