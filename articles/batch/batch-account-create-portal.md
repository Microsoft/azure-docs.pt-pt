---
title: Criar uma conta no portal Azure
description: Saiba como criar uma conta do Azure Batch no portal do Azure a executar cargas de trabalho paralelas em grande escala na nuvem
ms.topic: how-to
ms.date: 01/26/2021
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: bd9dd9b6d6d3a8d6938427b83528746ae5d09318
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/14/2021
ms.locfileid: "100368511"
---
# <a name="create-a-batch-account-with-the-azure-portal"></a>Criar uma conta do Batch com portal do Azure

Este tópico mostra como criar uma [conta Azure Batch](accounts.md) no [portal Azure](https://portal.azure.com), escolhendo as propriedades da conta que se encaixam no seu cenário computacional. Você também vai aprender onde encontrar propriedades importantes da conta, como chaves de acesso e URLs de conta.

Para obter informações sobre contas e cenários do Batch, consulte [o fluxo de trabalho e os recursos do serviço Batch](batch-service-workflow-features.md).

## <a name="create-a-batch-account"></a>Criar uma conta do Batch

[!INCLUDE [batch-account-mode-include](../../includes/batch-account-mode-include.md)]

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

1. A partir da página inicial, **selecione Criar um recurso**.

1. Na caixa de pesquisa, insira **o Serviço de Lote**. Selecione **o Serviço** de Lote a partir dos resultados e, em seguida, selecione **Criar**.

1. Insira os seguintes detalhes.

    :::image type="content" source="media/batch-account-create-portal/batch-account-portal.png" alt-text="Screenshot do ecrã da conta New Batch.":::

    a. **Subscrição**: a subscrição onde cria a conta do Batch. Se tiver apenas uma subscrição, está selecionada por predefinição.

    b. **Grupo de recursos**: selecione um grupo de recursos existente para a sua nova conta do Batch ou, opcionalmente, para criar um novo.

    c. **Nome da conta**: o nome que escolher deve ser único na região do Azure na qual é criada a conta (veja a **Localização** abaixo). O nome da conta pode conter apenas carateres em minúsculas ou números e deve ter 3 a 24 carateres de comprimento.

    d. **Localização**: a região do Azure na qual se cria a conta do Batch. Apenas as regiões suportadas pela sua subscrição e grupo de recursos são apresentadas como opções.

    e. **Conta de armazenamento**: Uma conta de [Armazenamento Azure](accounts.md#azure-storage-accounts) opcional que associa à sua conta Batch. Pode selecionar uma conta de armazenamento existente ou criar uma nova. Recomenda-se uma conta de armazenamento v2 para o melhor desempenho.

    :::image type="content" source="media/batch-account-create-portal/storage_account.png" alt-text="Screenshot das opções ao criar uma conta de armazenamento.":::

1. Se desejar, selecione **Advanced** para especificar **o tipo de identidade,** acesso à rede **pública** ou modo de atribuição **de Piscina**. Para a maioria dos cenários, as opções padrão são boas.

1. Selecione **Rever + criar** e, em seguida, selecione **Criar** para criar a conta.

## <a name="view-batch-account-properties"></a>Ver propriedades da conta do Batch

Depois de a conta ter sido criada, selecione a mesma para aceder às respetivas definições e propriedades. Pode aceder a todas as definições e propriedades da conta através do menu à esquerda.

> [!NOTE]
> O nome da conta Batch é o seu ID e não pode ser alterado. Se precisar de alterar o nome de uma conta Batch, terá de apagar a conta e criar uma nova com o nome pretendido.

:::image type="content" source="media/batch-account-create-portal/batch_blade.png" alt-text="Screenshot da página da conta do Lote no portal Azure.":::

Quando desenvolve uma aplicação com as APIs do [Lote,](batch-apis-tools.md#azure-accounts-for-batch-development)precisa de um URL de conta e chave para aceder aos seus recursos do Batch. (O Lote também suporta a autenticação do Azure Ative Directory.) Para ver as informações de acesso à conta do Lote, selecione **Keys**.

:::image type="content" source="media/batch-account-create-portal/batch-account-keys.png" alt-text="Screenshot das chaves da conta do Batch no portal Azure.":::

Para ver o nome e as chaves da conta de armazenamento associado à sua conta do Batch, selecione **Conta de armazenamento**.

Para visualizar as [quotas de recursos](batch-quota-limit.md) aplicáveis à conta Batch, selecione **Quotas**.

## <a name="additional-configuration-for-user-subscription-mode"></a>Configuração adicional para o modo de subscrição de utilizador

Se optar por criar uma conta do Batch no modo de subscrição de utilizador, execute os seguintes passos adicionais antes de criar a conta.

> [!IMPORTANT]
> O utilizador que cria a conta Batch no modo de subscrição do utilizador precisa de ter a atribuição de funções do Contribuinte ou do Proprietário para a subscrição na qual a conta Batch será criada.

### <a name="allow-azure-batch-to-access-the-subscription-one-time-operation"></a>Permitir que o Azure Batch aceda à subscrição (operação única)

Quando criar a sua primeira conta do Batch no modo de subscrição de utilizador, tem de registar a sua subscrição no Batch. (Se já o fez, salte para a secção seguinte.)

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

1. Selecione **Todas as**  >  **Subscrições de serviços** e selecione a subscrição que pretende utilizar para a conta Batch.

1. Na página **Subscrição**, selecione **Fornecedores de recursos** e procure **Microsoft.Batch**. Verifique se o fornecedor de recursos **Microsoft.Batch** está registado na subscrição. Se não for, selecione a **ligação Registar** perto da parte superior do ecrã.

    :::image type="content" source="media/batch-account-create-portal/register_provider.png" alt-text="Screenshot mostrando o fornecedor de recursos ch Microsoft.Bat.":::

1. Volte à página **de Subscrição** e, em seguida, selecione atribuições de funções de **controlo de acesso (IAM)**  >    >  **Adicionar** a atribuição de  >  **função**.

    :::image type="content" source="media/batch-account-create-portal/subscription_iam.png" alt-text="Screenshot da página de atribuições role para uma subscrição.":::

1. Na página **de atribuição de funções Adicionar,** selecione a função **Contribuinte** ou **Proprietário** e, em seguida, procure a API do lote. Procure no **Microsoft Azure Batch** ou **no MicrosoftAzureBatch** para encontrar a API. **(ddbf3205-c6bd-46ae-8127-60eb93363864** é o ID de aplicação para o Lote API.)

1. Depois de encontrar a API do Batch, selecione-a e selecione **Guardar**.

### <a name="create-a-key-vault"></a>Criar um Key Vault

No modo de subscrição do utilizador, é necessário um [Cofre de Chaves Azure.](../key-vault/general/overview.md) O Cofre-Chave deve estar na mesma subscrição e região que a conta Batch a criar.

1. A partir da página inicial do [portal Azure,](https://portal.azure.com)selecione **Criar um recurso**.
1. Na caixa de busca, introduza **o Cofre de Chaves**. Selecione **o Cofre** de Chaves a partir dos resultados e, em seguida, selecione **Criar**.
1. Na página do **cofre de chaves Create,** insira um nome para o Cofre de Chaves e crie um novo grupo de recursos na mesma região que deseja para a sua conta Batch. Deixe as definições restantes nos valores predefinidos e selecione **Criar**.

Ao criar a conta Batch no modo de subscrição do utilizador, especifique a **subscrição do Utilizador** como o modo de atribuição da piscina, selecione o Cofre-Chave que criou e verifique a caixa para conceder acesso ao Cofre de Chaves.

Se preferir conceder acesso manualmente ao Cofre de Chaves, aceda à secção de **políticas** de acesso do Cofre de Chaves e selecione **Adicionar Política de Acesso**. Selecione o link ao lado **de Select principal** e procure o Microsoft **Azure Batch** (Aplicação **ID ddbf3205-c6bd-46ae-8127-60eb93363864).** Selecione o principal e, em seguida, configuure as **permissões Secret** usando o menu suspenso. O Lote Azure deve receber um mínimo de permissões **Get**, **List,** **set** e **Delete.**

:::image type="content" source="media/batch-account-create-portal/secret-permissions.png" alt-text="Screenshot das permissões secretas seleções para Azure Batch":::

Selecione **Add**, em seguida, certifique-se de que as **Máquinas Virtuais Azure para implementação** e **O Gestor de Recursos Azure para caixas de verificação de implementação** do modelo são selecionadas para o recurso key **vault** ligado. **Selecione Guardar** para cometer as suas alterações.

:::image type="content" source="media/batch-account-create-portal/key-vault-access-policy.png" alt-text="Screenshot do ecrã da política de acesso.":::

### <a name="configure-subscription-quotas"></a>Configure quotas de subscrição

Para a subscrição do utilizador, as [quotas principais](batch-quota-limit.md) devem ser definidas manualmente. As quotas core do Lote Padrão não se aplicam às contas em modo de subscrição do utilizador, e as [quotas na sua subscrição](../azure-resource-manager/management/azure-subscription-service-limits.md) para núcleos de computação regional, núcleos computacional por série, e outros recursos são utilizados e aplicados.

1. No [portal Azure,](https://portal.azure.com)selecione o modo de subscrição do utilizador A conta Batch para exibir as suas definições e propriedades.
1. A partir do menu esquerdo, selecione **Quotas** para visualizar e configurar as quotas principais associadas à sua conta Batch.

## <a name="other-batch-account-management-options"></a>Outras opções de gestão de contas do Batch

Além do portal do Azure, também pode criar e gerir contas do Batch com ferramentas, incluindo o seguinte:

- [Cmdlets do PowerShell do Batch](batch-powershell-cmdlets-get-started.md)
- [CLI do Azure](batch-cli-get-started.md)
- [Gestão de Batch .NET](batch-management-dotnet.md)

## <a name="next-steps"></a>Passos seguintes

- Conheça o fluxo de trabalho do [serviço Batch e os recursos primários,](batch-service-workflow-features.md) tais como piscinas, nós, empregos e tarefas.
- Aprenda os conceitos básicos de programação de uma aplicação compatível com o Batch ao utilizar a [biblioteca de cliente .NET do Batch](quick-run-dotnet.md) ou [Python](quick-run-python.md). Estes quickstarts guiam-no através de uma aplicação de amostra que utiliza o serviço Batch para executar uma carga de trabalho em múltiplos nós de computação, utilizando o Azure Storage para a paragem e recuperação de ficheiros de carga de trabalho.