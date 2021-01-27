---
title: Utilize a biblioteca De Gestão de Lotes .NET para gerir os recursos de conta
description: Criar, eliminar e modificar os recursos da conta Azure Batch com a biblioteca Batch Management .NET.
ms.topic: how-to
ms.date: 01/26/2021
ms.custom: seodec18, has-adal-ref, devx-track-csharp
ms.openlocfilehash: f6acf23742b8d4c5c31a5ea952aba5c76b64cae0
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/27/2021
ms.locfileid: "98896736"
---
# <a name="manage-batch-accounts-and-quotas-with-the-batch-management-client-library-for-net"></a>Gerir contas e quotas do Batch com a biblioteca de clientes de Batch Management para .NET

Pode baixar a despesa de manutenção nas suas aplicações Azure Batch utilizando a biblioteca [Batch Management .NET](/dotnet/api/overview/azure/batch) para automatizar a criação de conta batch, a supressão, a gestão de chaves e a descoberta de quotas.

- **Criar e eliminar contas Batch** em qualquer região. Se, como fornecedor independente de software (ISV), por exemplo, fornecer um serviço para os seus clientes no qual cada um é atribuído uma conta Batch separada para efeitos de faturação, pode adicionar capacidades de criação e eliminação de conta ao portal do seu cliente.
- **Recupere e regenera as chaves de conta** programáticamente para qualquer uma das suas contas batch. Isto pode ajudá-lo a cumprir as políticas de segurança que impõem a caducidade periódica ou a caducidade das chaves da conta. Quando tem várias contas de Batch em várias regiões do Azure, a automatização deste processo de capotamento aumenta a eficiência da sua solução.
- **Verifique as quotas** de conta e tire as suposições de tentativa e erro para determinar quais as contas do Batch que têm os limites. Ao verificar as quotas da sua conta antes de iniciar postos de trabalho, criar piscinas ou adicionar nós de computação, pode ajustar proativamente onde ou quando estes recursos computacional são criados. Pode determinar quais as contas que requerem aumentos de quota antes de atribuir recursos adicionais nessas contas.
- **Combine funcionalidades de outros serviços Azure** para uma experiência de gestão completa, utilizando batch Management .NET, [Azure Ative Directory](../active-directory/fundamentals/active-directory-whatis.md)e o [Azure Resource Manager](../azure-resource-manager/management/overview.md) juntos na mesma aplicação. Ao utilizar estas funcionalidades e as suas APIs, pode fornecer uma experiência de autenticação sem atritos, a capacidade de criar e eliminar grupos de recursos e as capacidades acima descritas para uma solução de gestão de ponta a ponta.

> [!NOTE]
> Enquanto este artigo se foca na gestão programática das suas contas, chaves e quotas do Batch, também pode realizar muitas destas atividades [utilizando o portal Azure.](batch-account-create-portal.md)

## <a name="create-and-delete-batch-accounts"></a>Criar e eliminar contas batch

Uma das principais características da API de Gestão de Lotes é criar e eliminar [contas Batch](accounts.md) numa região do Azure. Para tal, utilize [BatchManagementClient.Account.CreateAsync](/dotnet/api/microsoft.azure.management.batch.batchaccountoperationsextensions.createasync) e [DeleteAsync,](/dotnet/api/microsoft.azure.management.batch.batchaccountoperationsextensions.deleteasync)ou as suas contrapartidas sincronizadas.

O seguinte código snippet cria uma conta, obtém a conta recém-criada a partir do serviço Batch e, em seguida, elimina-a. Neste snippet e nos outros deste artigo, `batchManagementClient` é um exemplo totalmente inicializado de [BatchManagementClient](/dotnet/api/microsoft.azure.management.batch.batchmanagementclient).

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
> As aplicações que utilizam a biblioteca Batch Management .NET e a sua classe BatchManagementClient requerem o acesso do administrador de serviço ou coadministrator à subscrição que detém a conta Batch para ser gerida. Para mais informações, consulte a secção Azure Ative Directory e a amostra de código [AccountManagement.](https://github.com/Azure-Samples/azure-batch-samples/tree/master/CSharp/AccountManagement)

## <a name="retrieve-and-regenerate-account-keys"></a>Recuperar e regenerar chaves de conta

Obtenha chaves de conta primária e secundária de qualquer conta Batch dentro da sua subscrição utilizando [GetKeysAsync](/dotnet/api/microsoft.azure.management.batch.batchaccountoperationsextensions.getkeysasync). Pode regenerar essas chaves utilizando [o RegenerateKeyAsync](/dotnet/api/microsoft.azure.management.batch.batchaccountoperationsextensions.regeneratekeyasync).

```csharp
// Get and print the primary and secondary keys
BatchAccountGetKeyResult accountKeys =
    await batchManagementClient.Account.GetKeysAsync(
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
> Pode criar um fluxo de trabalho de conexão simplificado para as suas aplicações de gestão. Em primeiro lugar, obtenha uma chave de conta para a conta Batch que deseja gerir com [o GetKeysAsync](/dotnet/api/microsoft.azure.management.batch.batchaccountoperationsextensions.getkeysasync). Em seguida, utilize esta tecla ao rubricar a classe [BatchSharedKeyCredentials da biblioteca BatchSharedKeyCredentials,](/dotnet/api/microsoft.azure.batch.auth.batchsharedkeycredentials) que é utilizada ao rubricar [o BatchClient](/dotnet/api/microsoft.azure.batch.batchclient).

## <a name="check-azure-subscription-and-batch-account-quotas"></a>Verifique as quotas de subscrição da Azure e do Lote

As subscrições Azure e os serviços individuais da Azure, como o Batch, têm quotas de incumprimento que limitam o número de determinadas entidades dentro delas. Para as quotas padrão para subscrições Azure, consulte [os limites de subscrição e serviços, quotas e restrições de subscrição da Azure.](../azure-resource-manager/management/azure-subscription-service-limits.md) Para as quotas predefinidas do serviço Batch, consulte [quotas e limites para o serviço Azure Batch](batch-quota-limit.md). Ao utilizar a biblioteca Batch Management .NET, pode verificar estas quotas nas suas aplicações. Isto permite-lhe tomar decisões de alocação antes de adicionar contas ou calcular recursos como pools e nóns de computação.

### <a name="check-an-azure-subscription-for-batch-account-quotas"></a>Verifique uma subscrição da Azure para as quotas de conta batch

Antes de criar uma conta Batch numa região, pode consultar a sua subscrição do Azure para ver se é capaz de adicionar uma conta nessa região.

No código snippet abaixo, usamos **listAsync** para obter uma coleção de todas as contas batch que estão dentro de uma subscrição. Assim que obtivermos esta recolha, determinamos quantas contas estão na região alvo. Em **seguida, usamos GetQuotasAsync** para obter a quota de conta Batch e determinar quantas contas (se houver) podem ser criadas nessa região.

```csharp
// Get a collection of all Batch accounts within the subscription
BatchAccountListResponse listResponse =
        await batchManagementClient.BatchAccount.ListAsync(new AccountListParameters());
IList<AccountResource> accounts = listResponse.Accounts;
Console.WriteLine("Total number of Batch accounts under subscription id {0}:  {1}",
    creds.SubscriptionId,
    accounts.Count);

// Get a count of all accounts within the target region
string region = "westus";
int accountsInRegion = accounts.Count(o => o.Location == region);

// Get the account quota for the specified region
SubscriptionQuotasGetResponse quotaResponse = await batchManagementClient.Location.GetQuotasAsync(region);
Console.WriteLine("Account quota for {0} region: {1}", region, quotaResponse.AccountQuota);

// Determine how many accounts can be created in the target region
Console.WriteLine("Accounts in {0}: {1}", region, accountsInRegion);
Console.WriteLine("You can create {0} accounts in the {1} region.", quotaResponse.AccountQuota - accountsInRegion, region);
```

No snippet acima, `creds` é um exemplo de **TokenCredentials**. Para ver um exemplo de criação deste objeto, consulte a amostra de código [de Gestão de Contas](https://github.com/Azure-Samples/azure-batch-samples/tree/master/CSharp/AccountManagement) no GitHub.

### <a name="check-a-batch-account-for-compute-resource-quotas"></a>Verifique uma conta batch para as quotas de recursos computacional

Antes de aumentar os recursos de computação na sua solução Batch, pode verificar se os recursos que pretende alocar não excedem as quotas da conta. No código abaixo, imprimimos a informação da quota para a conta Batch denominada `mybatchaccount` . Na sua própria aplicação, pode utilizar essas informações para determinar se a conta pode lidar com os recursos adicionais a criar.

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
> Embora existam quotas padrão para subscrições e serviços Azure, muitos destes limites podem ser aumentados [solicitando um aumento de quota no portal Azure](batch-quota-limit.md#increase-a-quota).

## <a name="use-azure-ad-with-batch-management-net"></a>Use Azure AD com gestão de lotes .NET

A biblioteca Batch Management .NET é um cliente fornecedor de recursos Azure, e é usada juntamente com [o Azure Resource Manager](../azure-resource-manager/management/overview.md) para gerir programasticamente de recursos de conta. O Azure AD é obrigado a autenticar pedidos escorriam por qualquer cliente fornecedor de recursos Azure, incluindo a biblioteca Batch Management .NET e através do Azure Resource Manager. Para obter informações sobre a utilização do AD Azure com a biblioteca Batch Management .NET, consulte [o Diretório Ativo Azure para autenticar soluções de Lote](batch-aad-auth.md).

## <a name="sample-project-on-github"></a>Projeto de amostra no GitHub

Para ver a Gestão de Lotes .NET em ação, consulte o projeto de amostra [de Gestão de Contas](https://github.com/Azure/azure-batch-samples/tree/master/CSharp/AccountManagement) no GitHub. O pedido de amostra de gestão de contas demonstra as seguintes operações:

1. Adquirir um símbolo de segurança da Azure AD utilizando [a ADAL](../active-directory/azuread-dev/active-directory-authentication-libraries.md). Se o utilizador ainda não estiver inscrito, é solicitado para as suas credenciais Azure.
2. Com o token de segurança obtido a partir do Azure AD, crie um [SubscriptionClient](/dotnet/api/microsoft.azure.management.resourcemanager.subscriptionclient) para consultar a Azure para uma lista de subscrições associadas à conta. O utilizador pode selecionar uma subscrição da lista se contiver mais de uma subscrição.
3. Obtenha credenciais associadas à subscrição selecionada.
4. Crie um objeto [ResourceManagementClient](/dotnet/api/microsoft.azure.management.resourcemanager.resourcemanagementclient) utilizando as credenciais.
5. Utilize um objeto [ResourceManagementClient](/dotnet/api/microsoft.azure.management.resourcemanager.resourcemanagementclient) para criar um grupo de recursos.
6. Utilize um objeto [BatchManagementClient](/dotnet/api/microsoft.azure.management.batch.batchmanagementclient) para executar várias operações de conta batch:
   - Criar uma conta Batch no novo grupo de recursos.
   - Obtenha a conta recém-criada do serviço Batch.
   - Imprima as chaves da conta para a nova conta.
   - Regenerar uma nova chave primária para a conta.
   - Imprima a informação da quota para a conta.
   - Imprima a informação da quota para a subscrição.
   - Imprima todas as contas dentro da subscrição.
   - Elimine a conta recém-criada.
7. Elimine o grupo de recursos.

Para executar a aplicação da amostra com sucesso, você deve primeiro registrá-lo com o seu inquilino AZure AD no portal Azure e conceder permissões à API Gestor de Recursos Azure. Siga os passos fornecidos nas [soluções de Gestão de Lotes Autenticados com Diretório Ativo.](batch-aad-auth-management.md)

## <a name="next-steps"></a>Próximos passos

- Conheça o fluxo de trabalho do [serviço Batch e os recursos primários,](batch-service-workflow-features.md) tais como piscinas, nós, empregos e tarefas.
- Aprenda os conceitos básicos de programação de uma aplicação compatível com o Batch ao utilizar a [biblioteca de cliente .NET do Batch](quick-run-dotnet.md) ou [Python](quick-run-python.md). Estes quickstarts guiam-no através de uma aplicação de amostra que utiliza o serviço Batch para executar uma carga de trabalho em múltiplos nós computacionais, utilizando o Azure Storage para a paragem de ficheiros de carga de trabalho e recuperação.git pus
