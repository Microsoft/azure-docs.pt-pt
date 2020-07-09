---
title: Início Rápido - Criar um orçamento com um modelo do Azure Resource Manager
description: Início Rápido que mostra como Criar um orçamento com um modelo do Azure Resource Manager.
author: bandersmsft
ms.author: banders
tags: azure-resource-manager
ms.service: cost-management-billing
ms.topic: quickstart
ms.date: 06/10/2020
ms.custom: subject-armqs
ms.openlocfilehash: dc37039d6777a77f9de247808329930f1621ee82
ms.sourcegitcommit: 537c539344ee44b07862f317d453267f2b7b2ca6
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/11/2020
ms.locfileid: "84686414"
---
# <a name="quickstart-create-a-budget-with-an-azure-resource-manager-template"></a>Início Rápido: Criar um orçamento com um modelo do Azure Resource Manager

Os orçamentos no Cost Management ajudam-no a planear e a gerar a responsabilidade organizacional. Com os orçamentos, pode contribuir para os serviços do Azure que consome ou subscrever durante um período específico. Ajudam-no a informar os outros sobre os custos para que possam proativamente gerir os custos e para monitorizar o progresso dos custos ao longo do tempo. Quando os limiares do orçamento que criou são excedidos, são acionadas notificações. Nenhum dos seus recursos é afetado e o consumo não será suspenso. Pode utilizar os orçamentos para comparar e acompanhar as despesas enquanto analisa os custos. Este início rápido mostra como criar um orçamento com um modelo do Resource Manager.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

## <a name="prerequisites"></a>Pré-requisitos

O modelo do Azure Resource Manager apenas suporta subscrições ao Azure para Contratos Enterprise (EA). Outros tipos de subscrição não são suportados pelo modelo.

Para criar e gerir orçamentos, tem de ter permissão de contribuidor. Pode criar orçamentos individuais para subscrições EA e grupos de recursos. Contudo, não pode criar orçamentos para contas de faturação EA. No caso de subscrições EA, tem de ter acesso de leitura para ver os orçamentos.

Após a criação de um orçamento, precisa de ter, pelo menos, acesso de leitura na sua conta do Azure para ver os orçamentos.

Se tiver uma subscrição nova, não pode criar imediatamente um orçamento nem utilizar outras funcionalidades do Cost Management. Poderá demorar até 48 horas até poder utilizar todas as funcionalidades do Cost Management.

As seguintes permissões, ou âmbitos, do Azure são suportadas por subscrição para os orçamentos por utilizador e grupo. Para obter mais informações sobre os âmbitos, veja [Compreender e trabalhar com âmbitos](understand-work-scopes.md).

- Proprietário: pode criar, modificar ou eliminar orçamentos de uma subscrição.
- Contribuidor e contribuidor do Cost Management: podem criar, modificar ou eliminar os orçamentos deles. Pode modificar a quantidade de orçamento para orçamentos criados por outros utilizadores.
- Leitor e leitor do Cost Management: podem ver os orçamentos para os quais têm permissões.

Para obter mais informações sobre a atribuição da permissão para os dados do Cost Management, veja [Atribuir acesso aos dados do Cost Management](assign-access-acm-data.md).

## <a name="create-a-budget"></a>Criar um orçamento

### <a name="review-the-template"></a>Rever o modelo

O modelo utilizado neste início rápido pertence aos [modelos de Início Rápido do Azure](https://azure.microsoft.com/resources/templates/create-budget).

:::code language="json" source="~/quickstart-templates/create-budget/azuredeploy.json" range="1-146" highlight="110-139":::

No modelo, está definido um recurso do Azure:

* [Microsoft.Consumption/budgets](/azure/templates/microsoft.consumption/budgets): Crie um orçamento do Azure.

### <a name="deploy-the-template"></a>Implementar o modelo

1. Selecione a imagem seguinte para iniciar sessão no Azure e abrir um modelo. O modelo cria um orçamento.

   [![Implementar no Azure](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fcreate-budget%2Fazuredeploy.json)

2. Selecione ou introduza os seguintes valores.

   [![Modelo do Resource Manager, Criar orçamento, portal de implementação](./media/quick-create-budget-template/create-budget-using-template-portal.png)](./media/quick-create-budget-template/create-budget-using-template-portal.png#lightbox)

    * **Subscrição**: selecione uma subscrição do Azure.
    * **Grupo de recursos**: selecione **Criar novo**, introduza um nome exclusivo para o grupo de recursos e, em seguida, clique em **OK** ou selecione um grupo de recursos existente.
    * **Localização**: selecione uma localização. Por exemplo, **E.U.A. Central**.
    * **Nome do Orçamento**: introduza um nome para o orçamento. Tem de ser exclusivo dentro de um grupo de recursos. Só são permitidos carateres alfanuméricos, carateres de sublinhado e hífenes.
    * **Montante**: introduza o montante total de custo ou utilização para acompanhar o orçamento.
    * **Categoria de Orçamento**: selecione a categoria do orçamento, se o orçamento controla o **Custo** ou a **Utilização**.
    * **Intervalo de Agregação**: introduza o intervalo de tempo abrangido por um orçamento. Os valores permitidos são Mensalmente, Trimestralmente ou Anualmente. O orçamento é reposto no final do intervalo de agregação.
    * **Data de Início**: introduza a data de início com o primeiro dia do mês no formato AAAA-MM-DD. Uma data de início futura não deve ser a mais de três meses. Pode especificar uma data de início anterior com o período do Intervalo de Agregação.
    * **Data de Fim**: introduza a data de fim do orçamento no formato AAAA-MM-DD. Caso não seja fornecido, a predefinição é 10 anos a partir da data de início.
    * **Operador**: selecione um operador de comparação. Os valores possíveis são EqualTo, GreaterThan ou GreaterThanOrEqualTo.
    * **Limiar**: introduza um valor de limiar para a notificação. É enviada uma notificação quando o custo exceder o limiar. É sempre uma percentagem e tem de ser entre 0 e 1000.
    * **E-mails de Contacto**: introduza uma lista de endereços de e-mail para enviar a notificação de orçamento quando o limiar for excedido. O formato esperado é `["user1@domain.com","user2@domain.com"]`.
    * **Funções de Contacto**: introduza a lista de funções de contacto para enviar a notificação de orçamento para quando o limiar for excedido. Os valores predefinidos são Proprietário, Contribuidor e Leitor. O formato esperado é `["Owner","Contributor","Reader"]`.
    * **Grupos de Contacto**: introduza uma lista de IDs de recursos de grupos de ações, como URIs de recursos completos, para onde enviar a notificação de orçamento quando o limiar é excedido. É aceite uma matriz de cadeias. O formato esperado é `["action group resource ID1","action group resource ID2"]`. Se não quiser utilizar grupos de ações, introduza `[]`.
    * **Filtro de Recursos**: introduza uma lista de filtros para os recursos. O formato esperado é `["Resource Filter Name1","Resource Filter Name2"]`. Se não quiser aplicar um filtro, introduza `[]`. Se introduzir um filtro de recursos, também tem de introduzir os valores de **filtros de medidores**.
    * **Filtro de Medidores**: introduza uma lista de filtros em medidores, obrigatórios para orçamentos com a categoria **Utilização**. O formato esperado é `["Meter Filter Name1","Meter Filter Name2"]`. Se não introduziu um **filtro de recursos**, introduza `[]`.
    * **Aceito os termos e condições acima apresentados**: selecione.

3. Selecione **Comprar**. Depois de o orçamento ser implementado com êxito, recebe uma notificação:

   ![Modelo do Resource Manager, orçamento, notificação do portal de implementação](./media/quick-create-budget-template/resource-manager-template-portal-deployment-notification.png)

O portal do Azure é utilizado para implementar o modelo. Além do portal do Azure, também pode utilizar o Azure PowerShell, a CLI do Azure e a API REST. Para saber mais sobre outros modelos de implementação, veja [Implementar modelos](../../azure-resource-manager/templates/deploy-powershell.md).

## <a name="validate-the-deployment"></a>Validar a implementação

Pode utilizar o portal do Azure para verificar se o orçamento está criado ao navegar para **Gestão de Custos + Faturação** > selecione um âmbito > **Orçamentos**. Em alternativa, utilize os seguintes scripts da CLI do Azure ou do Azure PowerShell para ver o orçamento.

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli-interactive
az consumption budget list
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
Get-AzConsumptionBudget
```

---

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não precisar de um orçamento, elimine-o através de um dos seguintes métodos:

### <a name="azure-portal"></a>Portal do Azure

Navegue até **Cost Management + Faturação** > selecione um âmbito de faturação > **Orçamentos** > selecione um orçamento > e, em seguida, selecione **Eliminar orçamento**.

### <a name="command-line"></a>Linha de comandos

Pode remover o orçamento com a CLI do Azure ou com o Azure PowerShell.

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli-interactive
echo "Enter the budget name:" &&
read budgetName &&
az consumption budget delete --budget-name $budgetName &&
echo "Press [ENTER] to continue ..."
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$budgetName = Read-Host -Prompt "Enter the budget name"
Remove-AzConsumptionBudget -Name $budgetName
Write-Host "Press [ENTER] to continue..."
```

---

## <a name="next-steps"></a>Passos seguintes

Neste início rápido, criou um orçamento do Azure para a implementação. Para saber mais sobre o Azure Cost Management, a Faturação e o Azure Resource Manager, avance para os artigos abaixo.

- Leia a descrição geral de [Cost Management e Faturação](../cost-management-billing-overview.md)
- [Criar orçamentos](tutorial-acm-create-budgets.md) no portal do Azure
- Saiba mais sobre o [Azure Resource Manager](../../azure-resource-manager/management/overview.md)
