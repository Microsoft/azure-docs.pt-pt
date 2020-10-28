---
title: Início Rápido - Criar um orçamento com um modelo do Azure Resource Manager
description: Início Rápido que mostra como Criar um orçamento com um modelo do Azure Resource Manager.
author: bandersmsft
ms.author: banders
tags: azure-resource-manager
ms.service: cost-management-billing
ms.subservice: cost-management
ms.topic: quickstart
ms.date: 07/28/2020
ms.custom: subject-armqs, devx-track-azurecli
ms.openlocfilehash: 7d93bd757a39247302a6bc09009a1a814425c32f
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/27/2020
ms.locfileid: "92745362"
---
# <a name="quickstart-create-a-budget-with-an-arm-template"></a>Início Rápido: Criar um orçamento com um modelo ARM

Os orçamentos no Cost Management ajudam-no a planear e a gerar a responsabilidade organizacional. Com os orçamentos, pode contribuir para os serviços do Azure que consome ou subscrever durante um período específico. Ajudam-no a informar os outros sobre os custos para que possam proativamente gerir os custos e para monitorizar o progresso dos custos ao longo do tempo. Quando os limiares do orçamento que criou são excedidos, são acionadas notificações. Nenhum dos seus recursos é afetado e o consumo não será suspenso. Pode utilizar os orçamentos para comparar e acompanhar as despesas enquanto analisa os custos. Este início rápido mostra como criar um orçamento com um modelo do Resource Manager (modelo ARM).

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

Se o seu ambiente cumpre os pré-requisitos e se está familiarizado com a utilização de modelos ARM, selecione o botão **Implementar no Azure** . O modelo será aberto no portal do Azure.

[![Implementar no Azure](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fcreate-budget%2Fazuredeploy.json)

## <a name="prerequisites"></a>Pré-requisitos

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

Se tiver uma subscrição nova, não pode criar imediatamente um orçamento nem utilizar outras funcionalidades do Cost Management. Poderá demorar até 48 horas até poder utilizar todas as funcionalidades do Cost Management.

São suportados orçamentos para os seguintes tipos e âmbitos de conta do Azure:

- Âmbitos do controlo de acesso baseado em funções do Azure (RBAC do Azure)
    - Grupos de gestão
    - Subscrição
- Âmbitos do Contrato Enterprise
    - Conta de faturação
    - Departamento
    - Conta de inscrição
- Contratos individuais
    - Conta de faturação
- Âmbitos do Contrato de Cliente Microsoft
    - Conta de faturação
    - Perfil de faturação
    - Secção de fatura
    - Cliente
- Âmbitos do AWS
    - Conta externa
    - Subscrição externa

Para ver os orçamentos, precisa de ter, pelo menos, acesso de leitura na sua conta do Azure.

No caso de subscrições EA, tem de ter acesso de leitura para ver os orçamentos. Para criar e gerir orçamentos, tem de ter permissão de contribuidor.

As seguintes permissões, ou âmbitos, do Azure são suportadas por subscrição para os orçamentos por utilizador e grupo. Para obter mais informações sobre os âmbitos, veja [Compreender e trabalhar com âmbitos](understand-work-scopes.md).

- Proprietário: pode criar, modificar ou eliminar orçamentos de uma subscrição.
- Contribuidor e contribuidor do Cost Management: podem criar, modificar ou eliminar os orçamentos deles. Pode modificar a quantidade de orçamento para orçamentos criados por outros utilizadores.
- Leitor e leitor do Cost Management: podem ver os orçamentos para os quais têm permissões.

Para obter mais informações sobre a atribuição da permissão para os dados do Cost Management, veja [Atribuir acesso aos dados do Cost Management](assign-access-acm-data.md).

## <a name="review-the-template"></a>Rever o modelo

O modelo utilizado neste início rápido pertence aos [Modelos de Início Rápido do Azure](https://azure.microsoft.com/resources/templates/create-budget).

:::code language="json" source="~/quickstart-templates/create-budget/azuredeploy.json" :::

No modelo, está definido um recurso do Azure:

* [Microsoft.Consumption/budgets](/azure/templates/microsoft.consumption/budgets): Crie um orçamento do Azure.

## <a name="deploy-the-template"></a>Implementar o modelo

1. Selecione a imagem seguinte para iniciar sessão no Azure e abrir um modelo. O modelo cria um orçamento.

   [![Implementar no Azure](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fcreate-budget%2Fazuredeploy.json)

2. Selecione ou introduza os seguintes valores.

   :::image type="content" source="./media/quick-create-budget-template/create-budget-using-template-portal.png" alt-text="Modelo do Resource Manager, Criar orçamento, portal de implementação]" lightbox="./media/quick-create-budget-template/create-budget-using-template-portal.png" :::
   
    * **Subscrição** : selecione uma subscrição do Azure.
    * **Grupo de recursos** : se for necessário, selecione um grupo de recursos existente ou **Crie um novo** .
    * **Região** : selecione uma região do Azure. Por exemplo, **E.U.A. Central** .
    * **Nome do Orçamento** : introduza um nome para o orçamento. Tem de ser exclusivo dentro de um grupo de recursos. Só são permitidos carateres alfanuméricos, carateres de sublinhado e hífenes.
    * **Montante** : introduza o montante total de custo para acompanhar o orçamento.
    * **Intervalo de Agregação** : introduza o intervalo de tempo abrangido por um orçamento. Os valores permitidos são Mensalmente, Trimestralmente ou Anualmente. O orçamento é reposto no final do intervalo de agregação.
    * **Data de Início** : introduza a data de início com o primeiro dia do mês no formato AAAA-MM-DD. Uma data de início futura não deve ser a mais de três meses. Pode especificar uma data de início anterior com o período do Intervalo de Agregação.
    * **Data de Fim** : introduza a data de fim do orçamento no formato AAAA-MM-DD. 
    * **Primeiro Limiar** : introduza um valor de limiar para a primeira notificação. É enviada uma notificação quando o custo exceder o limiar. É sempre uma percentagem e tem de ser entre 0 e 1000.
    * **Segundo Limiar** : introduza um valor de limiar para a segunda notificação. É enviada uma notificação quando o custo exceder o limiar. É sempre uma percentagem e tem de ser entre 0 e 1000.
    * **Funções de Contacto** : introduza a lista de funções de contacto para enviar a notificação de orçamento para quando o limiar for excedido. Os valores predefinidos são Proprietário, Contribuidor e Leitor. O formato esperado é `["Owner","Contributor","Reader"]`.
    * **E-mails de Contacto** : introduza uma lista de endereços de e-mail para enviar a notificação de orçamento quando for excedido um limiar. O formato esperado é `["user1@domain.com","user2@domain.com"]`.
    * **Grupos de Contacto** : introduza uma lista de IDs de recursos de grupos de ações, como URIs de recursos completos, para onde enviar a notificação de orçamento quando o limiar é excedido. É aceite uma matriz de cadeias. O formato esperado é `["action group resource ID1","action group resource ID2"]`. Se não quiser utilizar grupos de ações, introduza `[]`.
    * **Valores de Filtro de Grupos de Recursos** introduza uma lista de nomes de grupos de recursos a filtrar. O formato esperado é `["Resource Group Name1","Resource Group Name2"]`. Se não quiser aplicar um filtro, introduza `[]`. 
    * **Valores de Filtro de Categorias de Medidor** introduza uma lista de categorias de medidores de serviços do Azure. O formato esperado é `["Meter Category1","Meter Category2"]`. Se não quiser aplicar um filtro, introduza `[]`.
   
3. Consoante o seu tipo de subscrição do Azure, realize uma das seguintes ações:
   - Selecione **Rever + criar** .
   - Reveja os termos e condições, selecione **Aceito os temos e as condições apresentados acima** e, em seguida, selecione **Comprar** .

4. Se selecionou **Rever + criar** , o seu modelo é validado. Selecione **Criar** .  

   ![Modelo do Resource Manager, orçamento, notificação do portal de implementação](./media/quick-create-budget-template/resource-manager-template-portal-deployment-notification.png)

O portal do Azure é utilizado para implementar o modelo. Além do portal do Azure, também pode utilizar o Azure PowerShell, a CLI do Azure e a API REST. Para saber mais sobre outros modelos de implementação, veja [Implementar modelos](../../azure-resource-manager/templates/deploy-powershell.md).

## <a name="validate-the-deployment"></a>Validar a implementação

Pode utilizar o portal do Azure para verificar se o orçamento está criado ao navegar para **Gestão de Custos + Faturação** > selecione um âmbito > **Orçamentos** . Em alternativa, utilize os seguintes scripts da CLI do Azure ou do Azure PowerShell para ver o orçamento.

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

Navegue até **Cost Management + Faturação** > selecione um âmbito de faturação > **Orçamentos** > selecione um orçamento > e, em seguida, selecione **Eliminar orçamento** .

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
