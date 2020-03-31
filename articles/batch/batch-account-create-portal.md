---
title: Criar uma conta no portal Azure - Azure Batch [ Lote Azul ] Microsoft Docs
description: Saiba como criar uma conta do Azure Batch no portal do Azure a executar cargas de trabalho paralelas em grande escala na nuvem
services: batch
documentationcenter: ''
author: LauraBrenner
manager: evansma
editor: ''
ms.assetid: 3fbae545-245f-4c66-aee2-e25d7d5d36db
ms.service: batch
ms.workload: big-compute
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 02/26/2019
ms.author: labrenne
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 3723631609a04f6d12abcaac1f9d7733bf3caa01
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79247647"
---
# <a name="create-a-batch-account-with-the-azure-portal"></a>Criar uma conta do Batch com portal do Azure

Saiba como criar uma conta do Azure Batch no [portal do Azure][azure_portal] e escolha as propriedades da conta que se adequam ao seu cenário de computação. Saiba onde encontrar as propriedades da conta importantes, como as teclas de acesso e os URLs de conta.

Para informações sobre contas do Batch e cenários, consulte a [descrição geral da funcionalidade](batch-api-basics.md).

## <a name="create-a-batch-account"></a>Criar uma conta do Batch

[!INCLUDE [batch-account-mode-include](../../includes/batch-account-mode-include.md)]

1. Inicie sessão no [Portal do Azure][azure_portal].

1. Selecione **Criar um** > serviço de**lote****computacional** > de recursos .

    ![Batch no Marketplace][marketplace_portal]

1. Introduza as definições da **Nova conta do Batch**. Veja os detalhes seguintes.

    ![Criar uma conta do Batch][account_portal]

    a. **Subscrição**: a subscrição onde cria a conta do Batch. Se tiver apenas uma subscrição, está selecionada por predefinição.

    b. **Grupo de recursos**: selecione um grupo de recursos existente para a sua nova conta do Batch ou, opcionalmente, para criar um novo.

    c. **Nome da conta**: o nome que escolher deve ser único na região do Azure na qual é criada a conta (veja a **Localização** abaixo). O nome da conta pode conter apenas carateres em minúsculas ou números e deve ter 3 a 24 carateres de comprimento.

    d. **Localização**: a região do Azure na qual se cria a conta do Batch. Apenas as regiões suportadas pela sua subscrição e grupo de recursos são apresentadas como opções.

    e. **Conta de armazenamento**: Uma conta opcional de Armazenamento Azure que associa à sua conta Batch. Recomenda-se uma conta de armazenamento v2 de uso geral para o melhor desempenho. Para todas as opções da conta de armazenamento em Batch, consulte a visão geral da [funcionalidade lote](batch-api-basics.md#azure-storage-account). No portal, selecione uma conta de armazenamento existente, ou crie uma nova.

      ![Criar uma conta do Storage][storage_account]

    f. **Modo de atribuição da piscina**: No separador **definições avançadas** pode especificar o modo de atribuição do pool como **serviço de lote** ou **subscrição do Utilizador**. Para a maioria dos cenários, aceite o serviço de **lote**padrão .

      ![Modo de alocação de piscina de lote][pool_allocation]

1. Selecione **Criar** para criar a conta.

## <a name="view-batch-account-properties"></a>Ver propriedades da conta do Batch

Depois de a conta ter sido criada, selecione a mesma para aceder às respetivas definições e propriedades. Pode aceder a todas as definições e propriedades da conta através do menu à esquerda.

![Página Conta do Batch no portal do Azure][account_blade]

* **Nome da conta, URL e chaves do Batch**: quando desenvolver uma aplicação com as [APIs do Batch](batch-apis-tools.md#azure-accounts-for-batch-development), necessita de uma chave e de um URL de conta para aceder aos recursos do Batch. (O Batch também suporta a autenticação do Azure Active Directory.)

    Para ver as informações de acesso da conta de Batch, selecione **Chaves**.

    ![Chaves da conta do Batch no portal do Azure][account_keys]

* Para ver o nome e as chaves da conta de armazenamento associado à sua conta do Batch, selecione **Conta de armazenamento**.

* Para ver as quotas de recursos que se aplicam à conta do Batch, selecione **Quotas**. Para obter mais informações, consulte [Quotas e limites do serviço Batch](batch-quota-limit.md).

## <a name="additional-configuration-for-user-subscription-mode"></a>Configuração adicional para o modo de subscrição de utilizador

Se optar por criar uma conta do Batch no modo de subscrição de utilizador, execute os seguintes passos adicionais antes de criar a conta.

### <a name="allow-azure-batch-to-access-the-subscription-one-time-operation"></a>Permitir que o Azure Batch aceda à subscrição (operação única)

Quando criar a sua primeira conta do Batch no modo de subscrição de utilizador, tem de registar a sua subscrição no Batch. (Se anteriormente efetuou este procedimento, avance para a secção seguinte.)

1. Inicie sessão no [Portal do Azure][azure_portal].

1. Selecione Todas as**Subscrições**de **Serviços** > e selecione a subscrição que pretende utilizar para a conta 'Lote'.

1. Na página **Subscrição**, selecione **Fornecedores de recursos** e procure **Microsoft.Batch**. Verifique se o fornecedor de recursos **Microsoft.Batch** está registado na subscrição. Se não estiver registado, selecione a ligação **Registar**.

    ![Registar fornecedor do Microsoft.Batch][register_provider]

1. Na página **de Subscrição,** selecione**atribuições** > de função de controlo de **acesso (IAM)** > Adicionar atribuição de**funções**.

    ![Controlo de acesso da subscrição][subscription_access]

1. Na página de atribuição de **funções Adicionar,** selecione a função **Contributiva,** procure a API do Lote. Procure para cada uma destas cadeias até encontrar a API:
    1. **MicrosoftAzureBatch**.
    1. **Batch do Microsoft Azure**. Os inquilinos mais recentes podem utilizar este nome.
    1. **ddbf3205-c6bd-46ae-8127-60eb93363864** é o ID para a API do Batch.

1. Depois de encontrar a API do Batch, selecione-a e selecione **Guardar**.

    ![Adicionar permissões do Batch][add_permission]

### <a name="create-a-key-vault"></a>Criar um cofre de chaves

No modo de subscrição de utilizador, é necessário um cofre de chaves do Azure que pertence ao mesmo grupo de recursos que a conta do Batch a ser criada. Certifique-se de que o grupo de recursos está numa região onde o Batch está [disponível](https://azure.microsoft.com/regions/services/) e que a sua subscrição suporta.

1. No [Portal do Azure][azure_portal], selecione **Novo** > **Segurança** > **Key Vault**.

1. Na página **Criar Key Vault**, introduza um nome para o cofre de chaves e crie um grupo de recursos na região em que quer a sua conta do Batch. Deixe as definições restantes nos valores predefinidos e selecione **Criar**.

Ao criar a conta Batch no modo de subscrição do utilizador, utilize o grupo de recursos para o cofre chave. Especifique a **subscrição** do Utilizador como o modo de atribuição da piscina, selecione o cofre chave e verifique a caixa para conceder acesso ao Lote Azure ao cofre da chave. 

Se preferir conceder acesso manual mente ao cofre chave, aceda à secção **de políticas** de acesso do cofre chave e selecione Adicionar Política de **Acesso** e procurar o **Microsoft Azure Batch**. Uma vez selecionado, terá de configurar as **permissões Secretas** utilizando o menu drop down. O Lote Azure deve ter um mínimo de **obter,** **lista,** **conjunto**e **eliminar** permissões.

![Permissões secretas para O Lote Azure](./media/batch-account-create-portal/secret-permissions.png)


> [!NOTE]
> Certifique-se de que as **Máquinas Virtuais Azure para implantação** e o Gestor de **Recursos Azure para** caixas de verificação de implementação de modelos são selecionadas no âmbito das políticas de **Acesso** para o recurso **Key Vault** ligado.
> 
> ![Política](./media/batch-account-create-portal/key-vault-access-policy.png) obrigatória de acesso ao cofre chave Isto não é obrigatório ao criar uma conta De lote no portal Azure. A opção é selecionada por defeito.



### <a name="configure-subscription-quotas"></a>Configure quotas de subscrição

As quotas de base não são definidas por padrão nas contas do Lote de subscrição do utilizador. As quotas de base devem ser definidas manualmente porque as quotas de base padrão do Lote não se aplicam às contas no modo de subscrição do utilizador.

1. No [portal Azure,][azure_portal]selecione a sua conta de modo de subscrição do utilizador Para exibir as suas definições e propriedades.

1. A partir do menu esquerdo, selecione **Quotas** para visualizar e configurar as quotas centrais associadas à sua conta Batch.

Consulte as [quotas de serviço do Lote e limites](batch-quota-limit.md) para obter mais informações sobre as quotas centrais do modo de subscrição do utilizador.

## <a name="other-batch-account-management-options"></a>Outras opções de gestão de contas do Batch

Além do portal do Azure, também pode criar e gerir contas do Batch com ferramentas, incluindo o seguinte:

* [Cmdlets do PowerShell do Batch](batch-powershell-cmdlets-get-started.md)
* [Azure CLI](batch-cli-get-started.md)
* [Gestão de Batch .NET](batch-management-dotnet.md)

## <a name="next-steps"></a>Passos seguintes

* Consulte a [Batch feature overview (Descrição geral da funcionalidade do Batch)](batch-api-basics.md) para saber mais sobre conceitos e funcionalidades de serviço do Batch. O artigo aborda os recursos do Batch principais como conjuntos, nós de computação e tarefas e fornece uma descrição geral das funcionalidades do serviço para cargas de trabalho de computação em grande escala.
* Aprenda os conceitos básicos de programação de uma aplicação compatível com o Batch ao utilizar a [biblioteca de cliente .NET do Batch](quick-run-dotnet.md) ou [Python](quick-run-python.md). Estes inícios rápidos orientam-no numa aplicação de exemplo que utiliza o serviço do Batch para executar uma carga de trabalho em vário nós de computação e inclui a utilização do Armazenamento do Azure para o teste e obtenção do ficheiro de carga de trabalho.

[azure_portal]: https://portal.azure.com
[batch_pricing]: https://azure.microsoft.com/pricing/details/batch/

[marketplace_portal]: ./media/batch-account-create-portal/marketplace-batch.png
[account_blade]: ./media/batch-account-create-portal/batch_blade.png
[account_portal]: ./media/batch-account-create-portal/batch-account-portal.png
[pool_allocation]: ./media/batch-account-create-portal/batch-pool-allocation.png
[account_keys]: ./media/batch-account-create-portal/batch-account-keys.png
[storage_account]: ./media/batch-account-create-portal/storage_account.png
[subscription_access]: ./media/batch-account-create-portal/subscription_iam.png
[add_permission]: ./media/batch-account-create-portal/add_permission.png
[register_provider]: ./media/batch-account-create-portal/register_provider.png
