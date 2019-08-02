---
title: Gerenciar recursos de conta com a biblioteca de cliente para .NET-Azure batch | Microsoft Docs
description: Crie, exclua e modifique os recursos da conta do lote do Azure com a biblioteca .NET de gerenciamento do lote.
services: batch
documentationcenter: .net
author: laurenhughes
manager: gwallace
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
ms.openlocfilehash: 41f3eecb1b3f488c355b1bad65b90dae8c76126e
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68323459"
---
# <a name="manage-batch-accounts-and-quotas-with-the-batch-management-client-library-for-net"></a>Gerenciar contas e cotas do lote com a biblioteca de cliente de gerenciamento de lote para .NET

> [!div class="op_single_selector"]
> * [Azure portal](batch-account-create-portal.md)
> * [Gestão de Batch .NET](batch-management-dotnet.md)
> 
> 

Você pode reduzir a sobrecarga de manutenção em seus aplicativos do lote do Azure usando a biblioteca [.net de gerenciamento do lote][api_mgmt_net] para automatizar a criação, a exclusão, o gerenciamento de chaves e a descoberta de cotas do lote.

* **Crie e exclua contas do lote** em qualquer região. Se, como um ISV (fornecedor independente de software), por exemplo, você fornecer um serviço para seus clientes em que cada um é atribuído a uma conta do lote separada para fins de cobrança, você pode adicionar recursos de criação e exclusão de conta ao portal do cliente.
* **Recuperar e regenerar chaves de conta** programaticamente para qualquer uma das suas contas do lote. Isso pode ajudá-lo a cumprir as políticas de segurança que impõem a substituição periódica ou a expiração de chaves de conta. Quando você tem várias contas do lote em várias regiões do Azure, a automação desse processo de substituição aumenta a eficiência da solução.
* **Verifique** as cotas de conta e tome as suposições de avaliação e erro para determinar quais contas do lote têm quais limites. Ao verificar as cotas de conta antes de iniciar trabalhos, criar pools ou adicionar nós de computação, você pode ajustar de forma proativa onde ou quando esses recursos de computação são criados. Você pode determinar quais contas exigem aumentos de cota antes de alocar recursos adicionais nessas contas.
* **Combine recursos de outros serviços do Azure** para uma experiência de gerenciamento com recursos completos, usando o .net de gerenciamento do lote, [Azure Active Directory][aad_about]e o [Azure Resource Manager][resman_overview] juntos no mesmo aplicativo. Ao usar esses recursos e suas APIs, você pode fornecer uma experiência de autenticação descomplicada, a capacidade de criar e excluir grupos de recursos e os recursos descritos acima para uma solução de gerenciamento de ponta a ponta.

> [!NOTE]
> Embora este artigo se concentre no gerenciamento programático de suas contas, chaves e cotas do lote, você pode executar muitas dessas atividades usando o [portal do Azure][azure_portal]. Para obter mais informações, consulte [criar uma conta do lote do Azure usando o portal do Azure](batch-account-create-portal.md) e [cotas e limites para o serviço de lote do Azure](batch-quota-limit.md).
> 
> 

## <a name="create-and-delete-batch-accounts"></a>Criar e excluir contas do lote
Como mencionado, um dos principais recursos da API de gerenciamento do lote é criar e excluir contas do lote em uma região do Azure. Para fazer isso, use [BatchManagementClient. Account. createasync][net_create] e [DeleteAsync][net_delete], ou suas contrapartes síncronas.

O trecho de código a seguir cria uma conta, obtém a conta recém-criada do serviço de lote e, em seguida, a exclui. Neste trecho de código e os outros neste artigo, `batchManagementClient` há uma instância totalmente inicializada do [BatchManagementClient][net_mgmt_client].

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
> Os aplicativos que usam a biblioteca .NET de gerenciamento do lote e sua classe BatchManagementClient exigem acesso de **administrador de serviço** ou coadministrador à assinatura que possui a conta do lote a ser gerenciada. Para obter mais informações, consulte a seção Azure Active Directory e o exemplo de código [AccountManagement][acct_mgmt_sample] .
> 
> 

## <a name="retrieve-and-regenerate-account-keys"></a>Recuperar e regenerar chaves de conta
Obtenha chaves de conta primárias e secundárias de qualquer conta do lote em sua assinatura usando o [ListKeysAsync][net_list_keys]. Você pode regenerar essas chaves usando [RegenerateKeyAsync][net_regenerate_keys].

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
> Você pode criar um fluxo de trabalho de conexão simplificado para seus aplicativos de gerenciamento. Primeiro, obtenha uma chave de conta para a conta do lote que você deseja gerenciar com o [ListKeysAsync][net_list_keys]. Em seguida, use essa chave ao inicializar a classe [BatchSharedKeyCredentials][net_sharedkeycred] da biblioteca .net do lote, que é usada ao inicializar o [BatchClient][net_batch_client].
> 
> 

## <a name="check-azure-subscription-and-batch-account-quotas"></a>Verificar as cotas da conta do lote e da assinatura do Azure
As assinaturas do Azure e os serviços individuais do Azure, como o lote, têm cotas padrão que limitam o número de determinadas entidades dentro delas. Para obter as cotas padrão das assinaturas do Azure, consulte [assinatura do Azure e limites de serviço, cotas e restrições](../azure-subscription-service-limits.md). Para as cotas padrão do serviço de lote, consulte [cotas e limites para o serviço de lote do Azure](batch-quota-limit.md). Usando a biblioteca .NET de gerenciamento do lote, você pode verificar essas cotas em seus aplicativos. Isso permite que você tome decisões de alocação antes de adicionar contas ou recursos de computação, como pools e nós de computação.

### <a name="check-an-azure-subscription-for-batch-account-quotas"></a>Verificar uma assinatura do Azure para cotas de conta do lote
Antes de criar uma conta do lote em uma região, você pode verificar sua assinatura do Azure para ver se é possível adicionar uma conta nessa região.

No trecho de código abaixo, usamos primeiro [BatchManagementClient. Account. ListAsync][net_mgmt_listaccounts] para obter uma coleção de todas as contas do lote que estão dentro de uma assinatura. Depois de obtermos essa coleção, determinamos quantas contas estão na região de destino. Em seguida, usamos [BatchManagementClient. subscriptions][net_mgmt_subscriptions] para obter a cota da conta do lote e determinar quantas contas (se houver) podem ser criadas nessa região.

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

No trecho acima, `creds` é uma instância de [TokenCloudCredentials][azure_tokencreds]. Para ver um exemplo de criação desse objeto, consulte o exemplo de código [AccountManagement][acct_mgmt_sample] no github.

### <a name="check-a-batch-account-for-compute-resource-quotas"></a>Verificar uma conta do lote para cotas de recursos de computação
Antes de aumentar os recursos de computação em sua solução do lote, você pode verificar se os recursos que deseja alocar não excederão as cotas da conta. No trecho de código abaixo, imprimemos as informações de cota para a conta `mybatchaccount`do lote chamada. Em seu próprio aplicativo, você pode usar essas informações para determinar se a conta pode manipular os recursos adicionais a serem criados.

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
> Embora haja cotas padrão para assinaturas e serviços do Azure, muitos desses limites podem ser gerados emitindo uma solicitação na [portal do Azure][azure_portal]. Por exemplo, consulte [cotas e limites para o serviço do lote do Azure](batch-quota-limit.md) para obter instruções sobre como aumentar as cotas de conta do lote.
> 
> 

## <a name="use-azure-ad-with-batch-management-net"></a>Usar o Azure AD com o .NET de gerenciamento do lote

A biblioteca .NET de gerenciamento do lote é um cliente do provedor de recursos do Azure e é usada junto com [Azure Resource Manager][resman_overview] para gerenciar recursos de conta programaticamente. O Azure AD é necessário para autenticar solicitações feitas por meio de qualquer cliente do provedor de recursos do Azure, incluindo a biblioteca .NET de gerenciamento do lote e por meio de [Azure Resource Manager][resman_overview]. Para obter informações sobre como usar o Azure AD com a biblioteca .NET de gerenciamento do lote, consulte [usar Azure Active Directory para autenticar soluções do lote](batch-aad-auth.md). 

## <a name="sample-project-on-github"></a>Projeto de exemplo no GitHub

Para ver o .NET de gerenciamento do lote em ação, confira o projeto de exemplo [AccountManagement][acct_mgmt_sample] no github. O aplicativo de exemplo AccountManagement demonstra as seguintes operações:

1. Adquirir um token de segurança do Azure AD usando a [Adal][aad_adal]. Se o usuário ainda não estiver conectado, ele receberá uma solicitação para suas credenciais do Azure.
2. Com o token de segurança obtido do Azure AD, crie um [SubscriptionClient][resman_subclient] para consultar o Azure para obter uma lista de assinaturas associadas à conta. O usuário pode selecionar uma assinatura na lista se ela contiver mais de uma assinatura.
3. Obtenha as credenciais associadas à assinatura selecionada.
4. Crie um objeto [ResourceManagementClient][resman_client] usando as credenciais.
5. Use um objeto [ResourceManagementClient][resman_client] para criar um grupo de recursos.
6. Use um objeto [BatchManagementClient][net_mgmt_client] para executar várias operações de conta do lote:
   * Crie uma conta do lote no novo grupo de recursos.
   * Obtenha a conta recém-criada do serviço de lote.
   * Imprima as chaves de conta para a nova conta.
   * Regenerar uma nova chave primária para a conta.
   * Imprima as informações de cota da conta.
   * Imprima as informações de cota da assinatura.
   * Imprimir todas as contas na assinatura.
   * Exclua a conta recém-criada.
7. Exclua o grupo de recursos.

Antes de excluir a conta e o grupo de recursos do lote recém-criado, você pode exibi-las no [portal do Azure][azure_portal]:

Para executar o aplicativo de exemplo com êxito, primeiro você deve registrá-lo com seu locatário do Azure AD no portal do Azure e conceder permissões para a API Azure Resource Manager. Siga as etapas fornecidas em [autenticar soluções de gerenciamento do lote com Active Directory](batch-aad-auth-management.md).


[aad_about]:../active-directory/fundamentals/active-directory-whatis.md "O que é Azure Active Directory?"
[aad_adal]: ../active-directory/active-directory-authentication-libraries.md
[aad_auth_scenarios]:../active-directory/develop/authentication-scenarios.md "Cenários de autenticação do Azure AD"
[aad_integrate]:../active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad.md "Integrando aplicativos com o Azure Active Directory"
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
