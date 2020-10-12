---
title: Criar uma conta no portal Azure
description: Saiba como criar uma conta do Azure Batch no portal do Azure a executar cargas de trabalho paralelas em grande escala na nuvem
ms.topic: how-to
ms.date: 06/10/2020
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 1205de2b800588b735aeb20d388ba4b64bc6b078
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "84711345"
---
# <a name="create-a-batch-account-with-the-azure-portal"></a>Criar uma conta do Batch com portal do Azure

Este tópico mostra como criar uma conta Azure Batch no [portal Azure](https://portal.azure.com), escolhendo as propriedades da conta que se encaixam no seu cenário computacional. Você também vai aprender onde encontrar propriedades importantes da conta, como chaves de acesso e URLs de conta.

Para obter informações sobre contas e cenários do Batch, consulte [o fluxo de trabalho e os recursos do serviço Batch](batch-service-workflow-features.md).

## <a name="create-a-batch-account"></a>Criar uma conta do Batch

[!INCLUDE [batch-account-mode-include](../../includes/batch-account-mode-include.md)]

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

1. Selecione **Criar um recurso**e, em seguida, selecione **Computação** e **Serviço de Lote**.

1. Introduza as definições da **Nova conta do Batch**. Veja os detalhes seguintes.

    ![Criar uma conta do Batch][account_portal]

    a. **Subscrição**: a subscrição onde cria a conta do Batch. Se tiver apenas uma subscrição, está selecionada por predefinição.

    b. **Grupo de recursos**: selecione um grupo de recursos existente para a sua nova conta do Batch ou, opcionalmente, para criar um novo.

    c. **Nome da conta**: o nome que escolher deve ser único na região do Azure na qual é criada a conta (veja a **Localização** abaixo). O nome da conta pode conter apenas carateres em minúsculas ou números e deve ter 3 a 24 carateres de comprimento.

    d. **Localização**: a região do Azure na qual se cria a conta do Batch. Apenas as regiões suportadas pela sua subscrição e grupo de recursos são apresentadas como opções.

    e. **Conta de armazenamento**: Uma conta de Armazenamento Azure opcional que associa à sua conta Batch. Recomenda-se uma conta de armazenamento v2 para o melhor desempenho. Para todas as opções de conta de armazenamento em Batch, consulte a [visão geral](accounts.md#azure-storage-accounts)da funcionalidade Lote . No portal, selecione uma conta de armazenamento existente ou crie uma nova.

      ![Criar uma conta de armazenamento][storage_account]

    f. **Modo de atribuição de**piscinas : No separador **Definições Avançadas** pode especificar o modo de atribuição de piscinas como **serviço Batch** ou **subscrição do Utilizador.** Para a maioria dos cenários, aceite o **serviço De lote**predefinido .

      ![Modo de atribuição de piscinas de lote][pool_allocation]

1. Selecione **Criar** para criar a conta.

## <a name="view-batch-account-properties"></a>Ver propriedades da conta do Batch

Depois de a conta ter sido criada, selecione a mesma para aceder às respetivas definições e propriedades. Pode aceder a todas as definições e propriedades da conta através do menu à esquerda.

> [!NOTE]
> O nome da conta Batch é o seu ID e não pode ser alterado. Se precisar de alterar o nome de uma conta Batch, terá de apagar a conta e criar uma nova com o nome pretendido.

![Página Conta do Batch no portal do Azure][account_blade]

* **Nome da conta, URL e chaves do Batch**: quando desenvolver uma aplicação com as [APIs do Batch](batch-apis-tools.md#azure-accounts-for-batch-development), necessita de uma chave e de um URL de conta para aceder aos recursos do Batch. (O Batch também suporta a autenticação do Azure Active Directory.)

    Para ver as informações de acesso da conta de Batch, selecione **Chaves**.

    ![Chaves da conta do Batch no portal do Azure][account_keys]

* Para ver o nome e as chaves da conta de armazenamento associado à sua conta do Batch, selecione **Conta de armazenamento**.

* Para ver as quotas de recursos que se aplicam à conta do Batch, selecione **Quotas**. Para obter mais informações, consulte [Quotas e limites do serviço Batch](batch-quota-limit.md).

## <a name="additional-configuration-for-user-subscription-mode"></a>Configuração adicional para o modo de subscrição de utilizador

Se optar por criar uma conta do Batch no modo de subscrição de utilizador, execute os seguintes passos adicionais antes de criar a conta.

### <a name="allow-azure-batch-to-access-the-subscription-one-time-operation"></a>Permitir que o Azure Batch aceda à subscrição (operação única)

Quando criar a sua primeira conta do Batch no modo de subscrição de utilizador, tem de registar a sua subscrição no Batch. (Se já o fez, salte para a secção seguinte.)

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

1. Selecione **Todas as**  >  **Subscrições de serviços**e selecione a subscrição que pretende utilizar para a conta Batch.

1. Na página **Subscrição**, selecione **Fornecedores de recursos** e procure **Microsoft.Batch**. Verifique se o fornecedor de recursos **Microsoft.Batch** está registado na subscrição. Se não estiver registado, selecione a ligação **Registar**.

    ![Registar fornecedor do Microsoft.Batch][register_provider]

1. Na página **subscrição,** selecione **Access control (IAM)**  >  **Atribuições de**  >  **funções Adicionar atribuição de função**.

    ![Controlo de acesso da subscrição][subscription_access]

1. Na página **de atribuição de funções Adicionar,** selecione a função **Contribuinte** ou **Proprietário** e, em seguida, procure a API do lote. Procure para cada uma destas cadeias até encontrar a API:
    1. **MicrosoftAzureBatch**.
    1. **Batch do Microsoft Azure**. Os inquilinos mais recentes podem utilizar este nome.
    1. **ddbf3205-c6bd-46ae-8127-60eb93363864** é o ID para a API do Batch.

1. Depois de encontrar a API do Batch, selecione-a e selecione **Guardar**.

    ![Adicionar permissões do Batch][add_permission]

### <a name="create-a-key-vault"></a>Criar um Key Vault

No modo de subscrição do utilizador, é necessário um [Cofre de Chaves Azure.](../key-vault/general/overview.md) O Cofre-Chave deve estar na mesma subscrição e região que a conta Batch a criar. 

1. No [Portal do Azure](https://portal.azure.com), selecione **Novo** > **Segurança** > **Key Vault**.

1. Na página **'Criar Cofre de Chaves',** insira um nome para o Cofre de Chaves e crie um grupo de recursos na região que deseja para a sua conta Batch. Deixe as definições restantes nos valores predefinidos e selecione **Criar**.

Ao criar a conta Batch no modo de subscrição do utilizador, especifique a **subscrição do Utilizador** como o modo de atribuição da piscina, selecione o Cofre de Chaves e verifique a caixa para conceder acesso ao Azure Batch ao Cofre de Chaves.

Se preferir conceder acesso ao Cofre de Chaves manualmente, aceda à secção de políticas de **Acesso** do Cofre de Chaves, selecione **Add Access Policy** e procure o Microsoft **Azure Batch**. Uma vez selecionado, terá de configurar as **permissões Secret** utilizando o menu suspenso. O Lote Azure deve receber um mínimo de permissões **Get**, **List,** **set**e **Delete.**

![Permissões secretas para Azure Batch](./media/batch-account-create-portal/secret-permissions.png)

> [!NOTE]
> Certifique-se de que as **Máquinas Virtuais Azure para implantação** e **o Gestor de Recursos Azure para caixas de verificação de implementação de modelos** são selecionadas ao abrigo **das políticas de Acesso** para o recurso **Key Vault** ligado.
>
> ![Política obrigatória de acesso ao cofre de chaves](./media/batch-account-create-portal/key-vault-access-policy.png)

### <a name="configure-subscription-quotas"></a>Configure quotas de subscrição

Para a subscrição do utilizador, as quotas principais devem ser definidas manualmente. As quotas core do Lote Padrão não se aplicam às contas no modo de subscrição do utilizador.

1. No [portal Azure,](https://portal.azure.com)selecione o modo de subscrição do utilizador A conta Batch para exibir as suas definições e propriedades.
1. A partir do menu esquerdo, selecione **Quotas** para visualizar e configurar as quotas principais associadas à sua conta Batch.

Para obter mais informações sobre as quotas principais do modo de subscrição do utilizador, consulte [as quotas e limites de serviço do Batch](batch-quota-limit.md).

## <a name="other-batch-account-management-options"></a>Outras opções de gestão de contas do Batch

Além do portal do Azure, também pode criar e gerir contas do Batch com ferramentas, incluindo o seguinte:

* [Cmdlets do PowerShell do Batch](batch-powershell-cmdlets-get-started.md)
* [CLI do Azure](batch-cli-get-started.md)
* [Gestão de Batch .NET](batch-management-dotnet.md)

## <a name="next-steps"></a>Passos seguintes

* Conheça o fluxo de trabalho do [serviço Batch e os recursos primários,](batch-service-workflow-features.md) tais como piscinas, nós, empregos e tarefas.
* Aprenda os conceitos básicos de programação de uma aplicação compatível com o Batch ao utilizar a [biblioteca de cliente .NET do Batch](quick-run-dotnet.md) ou [Python](quick-run-python.md). Estes inícios rápidos orientam-no numa aplicação de exemplo que utiliza o serviço do Batch para executar uma carga de trabalho em vário nós de computação e inclui a utilização do Armazenamento do Azure para o teste e obtenção do ficheiro de carga de trabalho.

[account_blade]: ./media/batch-account-create-portal/batch_blade.png
[account_portal]: ./media/batch-account-create-portal/batch-account-portal.png
[pool_allocation]: ./media/batch-account-create-portal/batch-pool-allocation.png
[account_keys]: ./media/batch-account-create-portal/batch-account-keys.png
[storage_account]: ./media/batch-account-create-portal/storage_account.png
[subscription_access]: ./media/batch-account-create-portal/subscription_iam.png
[add_permission]: ./media/batch-account-create-portal/add_permission.png
[register_provider]: ./media/batch-account-create-portal/register_provider.png
