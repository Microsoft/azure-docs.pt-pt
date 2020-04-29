---
title: Utilize a biblioteca De Gestão de Lotes .NET para gerir os recursos da conta
description: Criar, eliminar e modificar os recursos da conta do Lote Azure com a biblioteca Batch Management .NET.
ms.topic: article
ms.date: 04/24/2017
ms.custom: seodec18
ms.openlocfilehash: 69e3eb04352feff11ee50acab11328adb7900539
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82115997"
---
# <a name="manage-batch-accounts-and-quotas-with-the-batch-management-client-library-for-net"></a>Gerir contas e quotas de Lote com a biblioteca de clientes da Batch Management para .NET

> [!div class="op_single_selector"]
> * [Portal do Azure](batch-account-create-portal.md)
> * [Gestão de Batch .NET](batch-management-dotnet.md)
> 
> 

Pode reduzir as despesas de manutenção nas suas aplicações do Lote Azure utilizando a biblioteca [Batch Management .NET][api_mgmt_net] para automatizar a criação de conta Batch, a eliminação, a gestão de chaves e a descoberta de quotas.

* **Crie e elimine as contas do Lote** em qualquer região. Se, como fornecedor independente de software (ISV), por exemplo, prestar um serviço aos seus clientes no qual cada um é atribuído uma conta de Lote separada para efeitos de faturação, pode adicionar capacidades de criação e eliminação de contas ao seu portal de clientes.
* **Recupere e regeneraas chaves** de conta programáticamente para qualquer uma das suas contas do Lote. Isto pode ajudá-lo a cumprir as políticas de segurança que impõem o capotamento periódico ou a caducidade das chaves da conta. Quando se tem várias contas do Lote em várias regiões do Azure, a automatização deste processo de capotamento aumenta a eficiência da sua solução.
* **Verifique as quotas de conta** e tire o trabalho de adivinhação de tentativa e erro para determinar quais as contas do Lote que têm quais os limites. Ao verificar as quotas da sua conta antes de iniciar empregos, criar piscinas ou adicionar nós de computação, pode ajustar proativamente onde ou quando estes recursos de computação são criados. Pode determinar quais as contas que requerem aumentos de quota antes de atribuir recursos adicionais nessas contas.
* **Combine funcionalidades de outros serviços Azure** para uma experiência de gestão completa, utilizando a Batch Management .NET, [Azure Ative Directory,][aad_about]e o Gestor de [Recursos Azure][resman_overview] juntos na mesma aplicação. Ao utilizar estas funcionalidades e as suas APIs, pode proporcionar uma experiência de autenticação sem atritos, a capacidade de criar e eliminar grupos de recursos, bem como as capacidades acima descritas para uma solução de gestão de ponta a ponta.

> [!NOTE]
> Enquanto este artigo se centra na gestão programática das suas contas, chaves e quotas do Lote, pode realizar muitas destas atividades utilizando o [portal Azure.][azure_portal] Para mais informações, consulte [Criar uma conta Azure Batch utilizando o portal Azure](batch-account-create-portal.md) e [Quotas e limites para o serviço De lote Azure](batch-quota-limit.md).
> 
> 

## <a name="create-and-delete-batch-accounts"></a>Criar e eliminar contas do Lote
Como mencionado, uma das principais características da API de Gestão de Lotes é criar e apagar contas de Lote numa região do Azure. Para tal, utilize [o BatchManagementClient.Account.CreateAsync][net_create] e [DeleteAsync,][net_delete]ou os seus homólogos sincronizados.

O seguinte código snippet cria uma conta, obtém a conta recém-criada a partir do serviço Batch e, em seguida, elimina-a. Neste corte e nos outros neste `batchManagementClient` artigo, é uma instância totalmente inicializada de [BatchManagementClient][net_mgmt_client].

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
> As aplicações que utilizam a biblioteca Batch Management .NET e a sua classe BatchManagementClient requerem acesso ao administrador de **serviço** ou **coadministrador** à subscrição que detém a conta Batch para ser gerida. Para mais informações, consulte a secção de Diretório Ativo Azure e a amostra de código [DeGestão][acct_mgmt_sample] de Contas.
> 
> 

## <a name="retrieve-and-regenerate-account-keys"></a>Recuperar e regenerar chaves de conta
Obtenha chaves de conta primária e secundária de qualquer conta de Lote dentro da sua subscrição utilizando [listKeysAsync][net_list_keys]. Pode regenerar essas teclas utilizando [reregenerarKeyAsync][net_regenerate_keys].

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
> Pode criar um fluxo de trabalho de ligação simplificado para as suas aplicações de gestão. Em primeiro lugar, obtenha uma chave de conta para a conta Batch que deseja gerir com [listKeysAsync][net_list_keys]. Em seguida, utilize esta tecla ao inicializar a classe Batch .NET [library's BatchSharedKeyCredentials,][net_sharedkeycred] que é usada na rubrica [do BatchClient][net_batch_client].
> 
> 

## <a name="check-azure-subscription-and-batch-account-quotas"></a>Verifique as quotas de subscrição do Azure e do Lote
As subscrições do Azure e os serviços azure individuais como o Batch têm quotas padrão que limitam o número de determinadas entidades dentro delas. Para as quotas padrão para assinaturas Azure, consulte [os limites de subscrição e serviço do Azure, quotas e restrições.](../azure-resource-manager/management/azure-subscription-service-limits.md) Para as quotas padrão do serviço Batch, consulte [Quotas e limites para o serviço De lote Azure](batch-quota-limit.md). Ao utilizar a biblioteca Batch Management .NET, pode verificar estas quotas nas suas aplicações. Isto permite-lhe tomar decisões de atribuição antes de adicionar contas ou calcular recursos como piscinas e nós de computação.

### <a name="check-an-azure-subscription-for-batch-account-quotas"></a>Verifique uma subscrição azure para quotas de conta lote
Antes de criar uma conta De Lote numa região, pode verificar a sua subscrição azure para ver se é capaz de adicionar uma conta nessa região.

No código abaixo, usamos primeiro [BatchManagementClient.Account.ListAsync][net_mgmt_listaccounts] para obter uma coleção de todas as contas do Lote que estão dentro de uma subscrição. Assim que obtivemos esta coleção, determinamos quantas contas existem na região alvo. Em seguida, usamos [BatchManagementClient.Subscrições][net_mgmt_subscriptions] para obter o contingente de conta Batch e determinar quantas contas (se houver) podem ser criadas nessa região.

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

No corte acima, `creds` é um exemplo de [TokenCloudCredentials][azure_tokencreds]. Para ver um exemplo de criação deste objeto, consulte a amostra de código [DeGestão][acct_mgmt_sample] de Contas no GitHub.

### <a name="check-a-batch-account-for-compute-resource-quotas"></a>Verifique uma conta de Lote para quotas de recursos computacionais
Antes de aumentar os recursos computacionais na sua solução Batch, pode verificar para garantir que os recursos que pretende alocar não excederão as quotas da conta. No código abaixo, imprimimos as informações de quota `mybatchaccount`para a conta Batch chamada . Na sua própria aplicação, poderá utilizar essas informações para determinar se a conta pode lidar com os recursos adicionais a criar.

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
> Embora existam quotas padrão para subscrições e serviços Do Azure, muitos destes limites podem ser aumentados através da emissão de um pedido no [portal Azure.][azure_portal] Por exemplo, consulte [Quotas e limites para o serviço Azure Batch](batch-quota-limit.md) para obter instruções sobre o aumento das quotas da sua conta Batch.
> 
> 

## <a name="use-azure-ad-with-batch-management-net"></a>Utilize o Azure AD com gestão de lotes .NET

A biblioteca Batch Management .NET é um cliente fornecedor de recursos Azure, e é usada em conjunto com o [Azure Resource Manager][resman_overview] para gerir os recursos da conta programáticamente. A Azure AD é obrigada a autenticar pedidos feitos através de qualquer cliente do fornecedor de recursos Azure, incluindo a biblioteca Batch Management .NET, e através do [Azure Resource Manager.][resman_overview] Para obter informações sobre a utilização do Azure AD com a biblioteca Batch Management .NET, consulte [Use Azure Ative Directory para autenticar soluções de lote](batch-aad-auth.md). 

## <a name="sample-project-on-github"></a>Projeto de amostra no GitHub

Para ver a Gestão de Lotes .NET em ação, consulte o projeto de amostra [de Gestão de Contas][acct_mgmt_sample] no GitHub. A aplicação da amostra De Gestão de Contas demonstra as seguintes operações:

1. Adquira um símbolo de segurança da Azure AD utilizando a [ADAL][aad_adal]. Se o utilizador ainda não estiver inscrito, são solicitados para as suas credenciais Azure.
2. Com o símbolo de segurança obtido a partir do Azure AD, crie um [SubscriptionClient][resman_subclient] para consultar o Azure para uma lista de subscrições associadas à conta. O utilizador pode selecionar uma subscrição da lista se contiver mais de uma subscrição.
3. Obtenha credenciais associadas à subscrição selecionada.
4. Crie um objeto [ResourceManagementClient][resman_client] utilizando as credenciais.
5. Utilize um objeto [ResourceManagementClient][resman_client] para criar um grupo de recursos.
6. Utilize um objeto [BatchManagementClient][net_mgmt_client] para realizar várias operações de conta batch:
   * Crie uma conta Batch no novo grupo de recursos.
   * Obtenha a conta recém-criada a partir do serviço Batch.
   * Imprima as chaves da conta para a nova conta.
   * Regenerar uma nova chave primária para a conta.
   * Imprima a informação da quota para a conta.
   * Imprima a informação da quota para a subscrição.
   * Imprima todas as contas dentro da subscrição.
   * Elimine a conta recém-criada.
7. Elimine o grupo de recursos.

Antes de apagar a recém-criada conta batch e grupo de recursos, pode vê-los no [portal Azure:][azure_portal]

Para executar a aplicação da amostra com sucesso, você deve registrá-lo primeiro com o seu inquilino Azure AD no portal Azure e conceder permissões à API gestor de recursos Azure. Siga os passos fornecidos nas [soluções authenticate de gestão de lotes com diretório ativo](batch-aad-auth-management.md).


[aad_about]:../active-directory/fundamentals/active-directory-whatis.md "O que é o Azure Ative Directory?"
[aad_adal]: ../active-directory/active-directory-authentication-libraries.md
[aad_auth_scenarios]:../active-directory/develop/authentication-scenarios.md "Cenários de autenticação para AD Azure"
[aad_integrate]:../active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad.md "Integração de Aplicações com Diretório Ativo Azure"
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
[resman_overview]: ../azure-resource-manager/management/overview.md

[1]: ./media/batch-management-dotnet/portal-01.png
[2]: ./media/batch-management-dotnet/portal-02.png
[3]: ./media/batch-management-dotnet/portal-03.png
