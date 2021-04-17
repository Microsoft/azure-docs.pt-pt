---
title: Tutorial – Criar e gerir dados exportados do Azure Cost Management
description: Este artigo mostra como pode criar e gerir dados exportados do Azure Cost Management para os utilizar em sistemas externos.
author: bandersmsft
ms.author: banders
ms.date: 03/24/2020
ms.topic: tutorial
ms.service: cost-management-billing
ms.subservice: cost-management
ms.reviewer: adwise
ms.custom: seodec18, devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: af050ae95b4ab161028229299a8de5ed3426430b
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107482838"
---
# <a name="tutorial-create-and-manage-exported-data"></a>Tutorial: Criar e gerir dados exportados

Se leu o tutorial de Análise de Custos, está familiarizado com a transferência manual dos dados do Cost Management. No entanto, pode criar uma tarefa periódica, que exporta automaticamente os dados do Cost Management para o armazenamento do Azure com base numa frequência diária, semanal ou mensal. Os dados exportados estão no formato CSV e contêm todas as informações recolhidas pelo Cost Management. Pode utilizar os dados exportados no armazenamento do Azure com sistemas externos e combiná-los com os seus dados personalizados. E pode utilizar os dados exportados num sistema externo, como um dashboard ou outro sistema financeiro.

Veja o vídeo [How to schedule exports to storage with Azure Cost Management](https://www.youtube.com/watch?v=rWa_xI1aRzo) (Como agendar exportações para o armazenamento com o Azure Cost Management) sobre como criar uma exportação agendada dos seus dados de custos do Azure para o Armazenamento do Azure. Para ver outros vídeos, visite o canal do YouTube [Cost Management](https://www.youtube.com/c/AzureCostManagement).

>[!VIDEO https://www.youtube.com/embed/rWa_xI1aRzo]

Os exemplos neste tutorial orientam-no na exportação dos dados de gestão de custos e, em seguida, verificam se os dados foram exportados com êxito.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar uma exportação diária
> * Verificar se os dados são recolhidos

## <a name="prerequisites"></a>Pré-requisitos
A exportação de dados está disponível para uma variedade de tipos de contas do Azure, incluindo os clientes [Contrato Enterprise (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/) e [Contrato de Cliente Microsoft](get-started-partners.md). Para ver a lista completa dos tipos de contas suportados, veja [Compreender os dados do Cost Management](understand-cost-mgt-data.md). As seguintes permissões, ou âmbitos, do Azure são suportadas por subscrição para a exportação de dados por utilizador e grupo. Para obter mais informações sobre os âmbitos, veja [Compreender e trabalhar com âmbitos](understand-work-scopes.md).

- Proprietário â€" Pode criar, modificar ou apagar exportações programadas para uma subscrição.
- Contribuinte " Pode criar, modificar ou apagar as suas próprias exportações programadas. Pode modificar o nome de exportações agendadas criadas por outros utilizadores.
- Leitor " Pode agendar exportações para as que têm permissão.

Para contas de Armazenamento do Azure:
- São necessárias permissões de escrita para alterar a conta de armazenamento configurada, independentemente das permissões na exportação.
- A sua conta de armazenamento do Azure tem de estar configurada para o armazenamento de blobs ou ficheiros.

Se tiver uma subscrição nova, não pode utilizar as funcionalidades do Cost Management imediatamente. Poderá demorar até 48 horas até poder utilizar todas as funcionalidades do Cost Management.

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure
Inicie sessão no Portal do Azure em [https://portal.azure.com](https://portal.azure.com/).

## <a name="create-a-daily-export"></a>Criar uma exportação diária

### <a name="portal"></a>[Portal](#tab/azure-portal)

Para criar ou ver uma exportação de dados ou para agendar uma exportação, abra o âmbito desejado no portal do Azure e selecione **Análise de custos** no menu. Por exemplo, navegue para **Subscrições**, selecione uma subscrição na lista e, em seguida, selecione **Análise de custos** no menu. Na parte superior da página Análise de custos, selecione **Definições** e, em seguida, **Exportações**.

> [!NOTE]
> - Além das subscrições, pode criar exportações em grupos de recursos, grupos de gestão, departamentos e inscrições. Para obter mais informações sobre os âmbitos, veja [Compreender e trabalhar com âmbitos](understand-work-scopes.md).
>- Quando inicia sessão como parceiro no âmbito da conta de faturação ou no inquilino de um cliente, pode exportar os dados para uma conta do Armazenamento do Microsoft Azure que esteja associada à sua conta de armazenamento de parceiro. Contudo, tem de ter uma subscrição ativa no inquilino CSP.

1. Selecione **Adicionar** e escreva um nome para a exportação.
1. Para **Métrica**, escolha uma opção:
    - **Custo real (Utilização e Compras)** – selecione para exportar a utilização e as compras padrão
    - **Custo amortizado (Utilização e Compras)** – selecione para exportar os custos amortizados das compras, como reservas do Azure
1. Para **Tipo de exportação**, escolha uma opção:
    - **Exportação diária de custos mensais** â€" Fornece um novo arquivo de exportação diariamente para os seus custos mensais. Os dados mais recentes são agregados a partir das exportações diárias anteriores.
    - **Exportação semanal de custos para os últimos sete dias** â€" Cria uma exportação semanal dos seus custos para os últimos sete dias a partir da data de início selecionada da sua exportação.
    - **Exportação mensal dos custos do mês passado** " Proporciona-lhe uma exportação dos seus custos do mês passado em comparação com o mês em curso que cria a exportação. Ao avançar, o agendamento executa uma exportação no quinto dia de cada mês com os custos mensais anteriores.
    - Exportação única â€" **Permite-lhe** escolher uma gama de datas para os dados históricos exportarem para o armazenamento de blob Azure. Pode exportar um máximo de 90 dias de histórico de custos a partir da data escolhida. Esta exportação é executada imediatamente e está disponível na conta de armazenamento no prazo de duas horas.
        Dependendo do tipo de exportação, escolha uma data de início ou escolha uma data **De** e **Até**.
1. Especifique a subscrição da conta de armazenamento do Azure e, em seguida, selecione um grupo de recursos ou crie um novo.
1. Selecione o nome da conta de armazenamento ou crie uma nova.
1. Selecione a localização (região do Azure).
1. Especifique o contentor de armazenamento e o caminho do diretório para o qual pretende que o ficheiro de exportação vá.
    :::image type="content" source="./media/tutorial-export-acm-data/basics_exports.png" alt-text="Exemplo de nova exportação" lightbox="./media/tutorial-export-acm-data/basics_exports.png":::
1. Reveja os detalhes da sua exportação e selecione **Criar**.

A nova exportação é apresentada na lista de exportações. Por predefinição, as novas exportações estão ativadas. Se quiser desativar ou eliminar uma exportação agendada, selecione qualquer item na lista e, em seguida, selecione **Desativar** ou **Eliminar**.

Inicialmente, a execução da exportação pode demorar entre 12 e 24 horas. No entanto, a apresentação dos dados nos ficheiros exportados pode demorar mais tempo.

### <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Quando criar uma exportação programática, deve registar manualmente o `Microsoft.CostManagementExports` fornecedor de recursos com a subscrição onde reside a conta de armazenamento. O registo ocorre automaticamente quando cria a exportação utilizando o portal Azure. Para obter mais informações sobre como registar fornecedores de recursos, consulte [o fornecedor de recursos Do Registo.](../../azure-resource-manager/management/resource-providers-and-types.md#register-resource-provider)

Comece por preparar o seu ambiente para a CLI do Azure:

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../../includes/azure-cli-prepare-your-environment-no-header.md)]

1. Depois de iniciar sessão, para ver as exportações atuais, utilize o comando [az costmanagement export list](/cli/azure/ext/costmanagement/costmanagement/export#ext_costmanagement_az_costmanagement_export_list):

   ```azurecli
   az costmanagement export list --scope "subscriptions/00000000-0000-0000-0000-000000000000"
   ```

   >[!NOTE]
   >
   >* Além das subscrições, pode criar exportações para grupos de recursos e grupos de gestão. Para obter mais informações sobre os âmbitos, veja [Compreender e trabalhar com âmbitos](understand-work-scopes.md).
   >* Quando inicia sessão como parceiro no âmbito da conta de faturação ou no inquilino de um cliente, pode exportar os dados para uma conta do Armazenamento do Microsoft Azure que esteja associada à sua conta de armazenamento de parceiro. Contudo, tem de ter uma subscrição ativa no inquilino CSP.

1. Crie um grupo de recursos ou utilize um já existente. Para criar um grupo de recursos, utilize o comando [az group create](/cli/azure/group#az_group_create):

   ```azurecli
   az group create --name TreyNetwork --location "East US"
   ```

1. Crie uma conta de armazenamento para receber as exportações ou utilize uma já existente. Para criar uma conta de armazenamento, utilize o comando [az storage account create](/cli/azure/storage/account#az_storage_account_create):

   ```azurecli
   az storage account create --resource-group TreyNetwork --name cmdemo
   ```

1. Execute o comando [az costmanagement export create](/cli/azure/ext/costmanagement/costmanagement/export#ext_costmanagement_az_costmanagement_export_create) para criar a exportação:

   ```azurecli
   az costmanagement export create --name DemoExport --type ActualCost \
   --scope "subscriptions/00000000-0000-0000-0000-000000000000" --storage-account-id cmdemo \
   --storage-container democontainer --timeframe MonthToDate --recurrence Daily \
   --recurrence-period from="2020-06-01T00:00:00Z" to="2020-10-31T00:00:00Z" \
   --schedule-status Active --storage-directory demodirectory
   ```

   No parâmetro **--type**, pode escolher `ActualCost`, `AmortizedCost` ou `Usage`.

   Este exemplo utiliza `MonthToDate`. A exportação cria um ficheiro de exportação diariamente para os seus custos acumulados no mês. Os dados mais recentes são agregados a partir das exportações diárias anteriores deste mês.

1. Para ver os detalhes da operação de exportação, utilize o comando [az costmanagement export show](/cli/azure/ext/costmanagement/costmanagement/export#ext_costmanagement_az_costmanagement_export_show):

   ```azurecli
   az costmanagement export show --name DemoExport \
      --scope "subscriptions/00000000-0000-0000-0000-000000000000"
   ```

1. Utilize o comando [az costmanagement export update](/cli/azure/ext/costmanagement/costmanagement/export#ext_costmanagement_az_costmanagement_export_update) para atualizar uma exportação:

   ```azurecli
   az costmanagement export update --name DemoExport
      --scope "subscriptions/00000000-0000-0000-0000-000000000000" --storage-directory demodirectory02
   ```

   Este exemplo muda o diretório de saída.

>[!NOTE]
>Inicialmente, a execução da exportação pode demorar entre 12 e 24 horas. No entanto, a apresentação dos dados nos ficheiros exportados pode demorar mais tempo.

Pode utilizar o comando [az costmanagement export delete](/cli/azure/ext/costmanagement/costmanagement/export#ext_costmanagement_az_costmanagement_export_delete) para eliminar uma exportação:

```azurecli
az costmanagement export delete --name DemoExport --scope "subscriptions/00000000-0000-0000-0000-000000000000"
```

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

Quando criar uma exportação programática, deve registar manualmente o `Microsoft.CostManagementExports` fornecedor de recursos com a subscrição onde reside a conta de armazenamento. O registo ocorre automaticamente quando cria a exportação utilizando o portal Azure. Para obter mais informações sobre como registar fornecedores de recursos, consulte [o fornecedor de recursos Do Registo.](../../azure-resource-manager/management/resource-providers-and-types.md#register-resource-provider)

Comece por preparar o seu ambiente para o Azure PowerShell:

[!INCLUDE [azure-powershell-requirements-no-header.md](../../../includes/azure-powershell-requirements-no-header.md)]

  > [!IMPORTANT]
  > Embora o módulo **Az.CostManagement** do PowerShell esteja em pré-visualização, tem de o instalar separadamente com o cmdlet `Install-Module`. Quando este módulo do PowerShell entrar em disponibilidade geral, fará parte das versões futuras do módulo Az PowerShell e estará disponível por predefinição a partir do Azure Cloud Shell.

  ```azurepowershell-interactive
  Install-Module -Name Az.CostManagement
  ```

1. Depois de iniciar sessão, para ver as exportações atuais, utilize o cmdlet [Get-AzCostManagementExport](/powershell/module/Az.CostManagement/get-azcostmanagementexport):

   ```azurepowershell-interactive
   Get-AzCostManagementExport -Scope 'subscriptions/00000000-0000-0000-0000-000000000000'
   ```

   >[!NOTE]
   >
   >* Além das subscrições, pode criar exportações para grupos de recursos e grupos de gestão. Para obter mais informações sobre os âmbitos, veja [Compreender e trabalhar com âmbitos](understand-work-scopes.md).
   >* Quando inicia sessão como parceiro no âmbito da conta de faturação ou no inquilino de um cliente, pode exportar os dados para uma conta do Armazenamento do Microsoft Azure que esteja associada à sua conta de armazenamento de parceiro. Contudo, tem de ter uma subscrição ativa no inquilino CSP.

1. Crie um grupo de recursos ou utilize um já existente. Para criar um grupo de recursos, utilize o cmdlet [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup):

   ```azurepowershell-interactive
   New-AzResourceGroup -Name TreyNetwork -Location eastus
   ```

1. Crie uma conta de armazenamento para receber as exportações ou utilize uma já existente. Para criar uma conta de armazenamento, utilize o cmdlet [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount):

   ```azurepowershell-interactive
   New-AzStorageAccount -ResourceGroupName TreyNetwork -AccountName cmdemo -SkuName Standard_RAGRS -Location eastus
   ```

1. Execute o cmdlet [New-AzCostManagementExport](/powershell/module/Az.CostManagement/new-azcostmanagementexport) para criar a exportação:

   ```azurepowershell-interactive
   $Params = @{
     Name = 'DemoExport'
     DefinitionType = 'ActualCost'
     Scope = 'subscriptions/00000000-0000-0000-0000-000000000000'
     DestinationResourceId = '/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/treynetwork/providers/Microsoft.Storage/storageAccounts/cmdemo'
     DestinationContainer = 'democontainer'
     DefinitionTimeframe = 'MonthToDate'
     ScheduleRecurrence = 'Daily'
     RecurrencePeriodFrom = '2020-06-01T00:00:00Z'
     RecurrencePeriodTo = '2020-10-31T00:00:00Z'
     ScheduleStatus = 'Active'
     DestinationRootFolderPath = 'demodirectory'
     Format = 'Csv'
   }
   New-AzCostManagementExport @Params
   ```

   No parâmetro **DefinitionType**, pode escolher `ActualCost`, `AmortizedCost` ou `Usage`.

   Este exemplo utiliza `MonthToDate`. A exportação cria um ficheiro de exportação diariamente para os seus custos acumulados no mês. Os dados mais recentes são agregados a partir das exportações diárias anteriores deste mês.

1. Para ver os detalhes da sua operação de exportação, utilize o cmdlet `Get-AzCostManagementExport`:

   ```azurepowershell-interactive
   Get-AzCostManagementExport -Scope 'subscriptions/00000000-0000-0000-0000-000000000000'
   ```

1. Utilize o cmdlet [Update-AzCostManagementExport](/powershell/module/Az.CostManagement/update-azcostmanagementexport) para atualizar uma exportação:

   ```azurepowershell-interactive
   Update-AzCostManagementExport -Name DemoExport -Scope 'subscriptions/00000000-0000-0000-0000-000000000000' -DestinationRootFolderPath demodirectory02
   ```

   Este exemplo muda o diretório de saída.

>[!NOTE]
>Inicialmente, a execução da exportação pode demorar entre 12 e 24 horas. No entanto, a apresentação dos dados nos ficheiros exportados pode demorar mais tempo.

Pode utilizar o cmdlet [Remove-AzCostManagementExport](/powershell/module/Az.CostManagement/remove-azcostmanagementexport) para eliminar uma exportação:

```azurepowershell-interactive
Remove-AzCostManagementExport -Name DemoExport -Scope 'subscriptions/00000000-0000-0000-0000-000000000000'
```

---

### <a name="export-schedule"></a>Agenda de exportações

As exportações agendadas são afetadas pela hora e pelo dia da semana em que criou inicialmente a exportação. Quando cria uma exportação agendada, a exportação é executada com a mesma frequência para cada ocorrência de exportação subsequente. Por exemplo, numa exportação diária dos custos do mês até à data definida para uma frequência diária, a exportação é executada diariamente. Da mesma forma, numa exportação semanal, a exportação é executada todas as semanas no mesmo dia em que foi agendada. A hora de realização exata da exportação não é garantida e os dados da exportação estão disponíveis dentro de quatro horas após o tempo de execução.

Cada exportação cria um ficheiro novo, pelo que as exportações anteriores não são substituídas.

#### <a name="create-an-export-for-multiple-subscriptions"></a>Criar uma exportação para várias subscrições

Se tiver um Contrato Enterprise, pode utilizar um grupo de gestão para agregar as informações de custos das subscrições num único contentor. Em seguida, pode exportar os dados de gestão de custos para o grupo de gestão.

As exportações para grupos de gestão de outros tipos de subscrição não são suportadas.

1. Se ainda não tiver criado um grupo de gestão, crie um e atribua subscrições ao mesmo.
1. Na análise de custos, defina o âmbito para o grupo de gestão e selecione **Selecionar este grupo de gestão**.
    :::image type="content" source="./media/tutorial-export-acm-data/management-group-scope.png" alt-text="Exemplo a mostrar a opção Selecionar este grupo de gestão" lightbox="./media/tutorial-export-acm-data/management-group-scope.png":::
1. Crie uma exportação no âmbito para obter os dados de gestão de custos para as subscrições no grupo de gestão.
    :::image type="content" source="./media/tutorial-export-acm-data/new-export-management-group-scope.png" alt-text="Exemplo a mostrar a opção Criar nova exportação com um âmbito Grupo de gestão":::

## <a name="verify-that-data-is-collected"></a>Verificar se os dados são recolhidos

Pode verificar facilmente se os seus dados do Cost Management estão a ser recolhidos e ver o ficheiro CSV exportado com o Explorador de Armazenamento do Azure.

Na lista de exportação, selecione o nome da conta de armazenamento. Na página da conta de armazenamento, selecione Abrir no Explorador. Se vir uma caixa de confirmação, selecione **Sim** para abrir o ficheiro no Explorador de Armazenamento do Microsoft Azure.

![Página da conta de armazenamento a mostrar informações de exemplo e a ligação para Abrir no Explorador](./media/tutorial-export-acm-data/storage-account-page.png)

No Explorador de Armazenamento, navegue para o contentor que pretende abrir e selecione a pasta correspondente ao mês atual. É apresentada uma lista de ficheiros CSV. Selecione um e, em seguida, selecione **Abrir**.

![Informações de exemplo mostradas no Explorador de Armazenamento](./media/tutorial-export-acm-data/storage-explorer.png)

O ficheiro abre com o programa ou a aplicação que definiu para abrir as extensões de ficheiro CSV. Eis um exemplo do Excel.

![Dados CSV exportados de exemplo mostrados no Excel](./media/tutorial-export-acm-data/example-export-data.png)

### <a name="download-an-exported-csv-data-file"></a>Transferir um ficheiro de dados CSV exportado

Também pode transferir o ficheiro CSV exportado no portal do Azure. Os passos seguintes explicam como encontrá-lo a partir da análise de custos.

1. Na análise de custos, selecione **Definições**, e, em seguida, selecione **Exportações**.
1. Na lista de exportações, selecione a conta de armazenamento para uma exportação.
1. Na sua conta de armazenamento, selecione **Contentores**.
1. Na lista de contentores, selecione o contentor.
1. Navegue através dos diretórios e blobs de armazenamento até à data desejada.
1. Selecione o ficheiro CSV e, em seguida, **Transferir**.

[![Transferência de exportação de exemplo](./media/tutorial-export-acm-data/download-export.png)](./media/tutorial-export-acm-data/download-export.png#lightbox)

## <a name="view-export-run-history"></a>Ver histórico de execuções de exportação

Pode ver o histórico de execuções da exportação agendada ao selecionar uma exportação individual na página da lista de exportações. A página da lista de exportações também proporciona acesso rápido à visualização do tempo de execução das exportações anteriores e a próxima hora na qual a execução será executada. Veja a seguir um exemplo a mostrar o histórico de execuções.

:::image type="content" source="./media/tutorial-export-acm-data/run-history.png" alt-text="Captura de ecrã que mostra o painel Exportações.":::

Selecione uma exportação para ver o histórico de execuções.

:::image type="content" source="./media/tutorial-export-acm-data/single-export-run-history.png" alt-text="Captura de ecrã que mostra o histórico de execuções de uma exportação.":::

## <a name="access-exported-data-from-other-systems"></a>Aceder aos dados exportados a partir de outros sistemas

Um dos objetivos de exportar os dados do Cost Management é aceder aos dados a partir de sistemas externos. Pode utilizar um sistema de dashboard ou outro sistema financeiro. Estes sistemas variam bastante, de modo que mostrar um exemplo seria impraticável.  No entanto, pode começar a perceber como aceder aos dados das suas aplicações em [Introdução ao Armazenamento do Azure](../../storage/common/storage-introduction.md).

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Criar uma exportação diária
> * Verificar se os dados são recolhidos

Avance para o tutorial seguinte para otimizar e melhorar a eficiência, ao identificar os recursos inativos e subutilizados.

> [!div class="nextstepaction"]
> [Analisar e tomar medidas relacionadas com recomendações de otimização](tutorial-acm-opt-recommendations.md)
