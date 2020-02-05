---
title: Criar uma conta no portal do Azure-lote do Azure | Microsoft Docs
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
ms.openlocfilehash: a0c6fab0c9e26630bd54830044da56dba20564b3
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/05/2020
ms.locfileid: "77025901"
---
# <a name="create-a-batch-account-with-the-azure-portal"></a>Criar uma conta do Batch com portal do Azure

Saiba como criar uma conta do lote do Azure no [portal do Azure][azure_portal]e escolha as propriedades da conta que se ajustam ao seu cenário de computação. Saiba onde encontrar as propriedades da conta importantes, como as teclas de acesso e os URLs de conta.

Para informações sobre contas do Batch e cenários, consulte a [descrição geral da funcionalidade](batch-api-basics.md).

## <a name="create-a-batch-account"></a>Criar uma conta do Batch

[!INCLUDE [batch-account-mode-include](../../includes/batch-account-mode-include.md)]

1. Inicie sessão no [Portal do Azure][azure_portal].

1. Selecione **Criar um recurso** > **Computação** > **Serviço do Batch**.

    ![Batch no Marketplace][marketplace_portal]

1. Introduza as definições da **Nova conta do Batch**. Veja os detalhes seguintes.

    ![Criar uma conta do Batch][account_portal]

    a. **Subscrição**: a subscrição onde cria a conta do Batch. Se tiver apenas uma subscrição, está selecionada por predefinição.

    b. **Grupo de recursos**: selecione um grupo de recursos existente para a sua nova conta do Batch ou, opcionalmente, para criar um novo.

    c. **Nome da conta**: o nome que escolher deve ser único na região do Azure na qual é criada a conta (veja a **Localização** abaixo). O nome da conta pode conter apenas carateres em minúsculas ou números e deve ter 3 a 24 carateres de comprimento.

    d. **Localização**: a região do Azure na qual se cria a conta do Batch. Apenas as regiões suportadas pela sua subscrição e grupo de recursos são apresentadas como opções.

    e. **Conta de armazenamento**: uma conta de armazenamento do Azure opcional que você associa à sua conta do lote. Uma conta de armazenamento de uso geral V2 é recomendada para o melhor desempenho. Para todas as opções de conta de armazenamento no lote, consulte a [visão geral do recurso de lote](batch-api-basics.md#azure-storage-account). No portal, selecione uma conta de armazenamento existente ou crie uma nova.

      ![Create a storage account][storage_account]

    f. **Modo de alocação de pool**: na guia configurações **avançadas** , você pode especificar o modo de alocação de pool como **serviço de lote** ou assinatura de **usuário**. Para a maioria dos cenários, aceite o **serviço de lote**padrão.

      ![Modo de alocação do pool do lote][pool_allocation]

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

1. Selecione **Todos os Serviços** > **Subscrições** e selecione a subscrição que pretende utilizar para a conta do Batch.

1. Na página **Subscrição**, selecione **Fornecedores de recursos** e procure **Microsoft.Batch**. Verifique se o fornecedor de recursos **Microsoft.Batch** está registado na subscrição. Se não estiver registado, selecione a ligação **Registar**.

    ![Registar fornecedor do Microsoft.Batch][register_provider]

1. Na página **assinatura** , selecione **controle de acesso (iam)**  > **atribuições de função** > **Adicionar atribuição de função**.

    ![Controlo de acesso da subscrição][subscription_access]

1. Na página **Adicionar atribuição de função** , selecione a função **colaborador** , procure a API do lote. Procure para cada uma destas cadeias até encontrar a API:
    1. **MicrosoftAzureBatch**.
    1. **Batch do Microsoft Azure**. Os inquilinos mais recentes podem utilizar este nome.
    1. **ddbf3205-c6bd-46ae-8127-60eb93363864** é o ID para a API do Batch.

1. Depois de encontrar a API do Batch, selecione-a e selecione **Guardar**.

    ![Adicionar permissões do Batch][add_permission]

### <a name="create-a-key-vault"></a>Criar um cofre de chaves

No modo de subscrição de utilizador, é necessário um cofre de chaves do Azure que pertence ao mesmo grupo de recursos que a conta do Batch a ser criada. Certifique-se de que o grupo de recursos está numa região onde o Batch está [disponível](https://azure.microsoft.com/regions/services/) e que a sua subscrição suporta.

1. Na [portal do Azure][azure_portal], selecione **novo** > **segurança** > **Key Vault**.

1. Na página **Criar Key Vault**, introduza um nome para o cofre de chaves e crie um grupo de recursos na região em que quer a sua conta do Batch. Deixe as definições restantes nos valores predefinidos e selecione **Criar**.

Ao criar a conta do lote no modo de assinatura do usuário, use o grupo de recursos para o cofre de chaves. Especifique a **assinatura do usuário** como o modo de alocação do pool, selecione o cofre de chaves e marque a caixa para conceder acesso ao lote do Azure ao cofre de chaves. 

Se você preferir conceder acesso ao key Vault manualmente, vá para a seção **políticas de acesso** do cofre de chaves e selecione **Adicionar política de acesso** e procure **lote do Microsoft Azure**. Depois de selecionado, você precisará configurar as **permissões secretas** usando o menu suspenso. O lote do Azure deve receber, no mínimo, as permissões **obter**, **listar**, **definir**e **excluir** .

![Permissões secretas para o lote do Azure](./media/batch-account-create-portal/secret-permissions.png)

### <a name="configure-subscription-quotas"></a>Configurar cotas de assinatura

As cotas de núcleo não são definidas por padrão nas contas do lote de assinatura do usuário. As cotas de núcleo devem ser definidas manualmente porque as cotas de núcleo do lote padrão não se aplicam a contas no modo de assinatura do usuário.

1. Na [portal do Azure][azure_portal], selecione a conta do lote do modo de assinatura do usuário para exibir suas configurações e propriedades.

1. No menu à esquerda, selecione **cotas** para exibir e configurar as cotas principais associadas à sua conta do lote.

Consulte as [cotas e limites do serviço de lote](batch-quota-limit.md) para obter mais informações sobre as cotas de núcleo do modo de assinatura do usuário.

## <a name="other-batch-account-management-options"></a>Outras opções de gestão de contas do Batch

Além do portal do Azure, também pode criar e gerir contas do Batch com ferramentas, incluindo o seguinte:

* [Cmdlets do PowerShell do Batch](batch-powershell-cmdlets-get-started.md)
* [CLI do Azure](batch-cli-get-started.md)
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
