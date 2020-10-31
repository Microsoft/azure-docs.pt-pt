---
title: 'Quickstart: Nova atribuição de políticas com a Terraform'
description: Neste arranque rápido, você usa a sintaxe Terraform e HCL para criar uma atribuição de política para identificar recursos não conformes.
ms.date: 10/27/2020
ms.topic: quickstart
ms.openlocfilehash: dc4dae2dc6e43e7532117bf64af3ce97ddc7c496
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93106405"
---
# <a name="quickstart-create-a-policy-assignment-to-identify-non-compliant-resources-using-terraform"></a>Quickstart: Criar uma atribuição de política para identificar recursos não conformes usando a Terraform

O primeiro passo para compreender a conformidade no Azure consiste em identificar o estado dos seus recursos.
Este início rápido acompanha-o ao longo do processo de criação de uma atribuição de política para identificar máquinas virtuais que não estão a utilizar discos geridos.

No final deste processo, vai identificar com êxito as máquinas virtuais que não estão a utilizar discos geridos. Estão em _não conformidade_ com a atribuição de política.

## <a name="prerequisites"></a>Pré-requisitos

- Se não tiver uma subscrição do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.
- [Versão terraforme](https://www.terraform.io/) 0.12.0 ou superior configurada no seu ambiente.
  Para obter instruções, consulte [Configure Terraform usando Azure Cloud Shell](/azure/developer/terraform/get-started-cloud-shell).
- Este arranque rápido requer que execute a versão 2.13.0 do Azure CLI ou posterior. Para localizar a versão, execute `az --version`. Se precisar de instalar ou atualizar, veja [Install Azure CLI (Instalar o Azure CLI)](/cli/azure/install-azure-cli).

## <a name="create-the-terraform-configuration-variable-and-output-file"></a>Criar a configuração, variável e ficheiro de saída Terraform

Neste arranque rápido, cria-se uma atribuição de política e atribui os **VMs de auditoria que não utilizam discos geridos** `06a78e20-9358-41c9-923c-fb736d382a4d` () definição. Esta definição de política identifica recursos que não estão em conformidade com as condições definidas na definição de política.

Primeiro, configurar a configuração terraforma, variável e ficheiros de saída. Os recursos Terraform para Azure Policy utilizam o [Fornecedor Azure](https://www.terraform.io/docs/providers/azurerm/index.html).

1. Crie uma nova pasta nomeada `policy-assignment` e mude os diretórios para a touca.

1. Criar `main.tf` com o seguinte código:

   ```hcl
   provider "azurerm" {
       version = "~>2.0"
       features {}
   }
   
   resource "azurerm_policy_assignment" "auditvms" {
       name = "audit-vm-manageddisks"
       scope = var.cust_scope
       policy_definition_id = "/providers/Microsoft.Authorization/policyDefinitions/06a78e20-9358-41c9-923c-fb736d382a4d"
       description = "Shows all virtual machines not using managed disks"
       display_name = "Audit VMs without managed disks Assignment"
   }
   ```
1. Criar `variables.tf` com o seguinte código:

   ```hcl
   variable "cust_scope" {
       default = "{scope}"
   }
   ```

   Um âmbito determina quais os recursos ou agrupamento de recursos em que a atribuição de política será imposta. Pode ir de um grupo de gestão a um recurso individual. Certifique-se de que `{scope}` substitui por um dos seguintes padrões:

   - Assinatura: `/subscriptions/{subscriptionId}`
   - Grupo de recursos: `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}`
   - Recurso: `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{resourceProviderNamespace}/[{parentResourcePath}/]`

1. Criar `output.tf` com o seguinte código:

   ```hcl
   output "assignment_id" {
       value = azurerm_policy_assignment.auditvms.id
   }
   ```

## <a name="initialize-terraform-and-create-plan"></a>Inicialize o Terraform e crie um plano

Em seguida, inicialize a Terraform para descarregar os fornecedores necessários e, em seguida, criar um plano.

1. Executar o comando [init terraform.](https://www.terraform.io/docs/commands/init.html) Este comando descarrega os módulos Azure necessários para criar os recursos Azure na configuração Terraform.

   ```bash
   terraform init
   ```

   :::image type="content" source="./media/assign-policy-terraform/terraform-initialize.png" alt-text="Screenshot de executar o comando init terraform que mostra o download do módulo azurerm e uma mensagem de sucesso.":::

1. Autenticar com [Azure CLI](/cli/azure/) para Terraform. Para obter mais informações, consulte [O Fornecedor Azure: Autenticação utilizando o Azure CLI](https://www.terraform.io/docs/providers/azurerm/guides/azure_cli.html).

   ```bash
   az login
   ```

1. Crie o plano de execução com o comando [do plano terraforme](https://www.terraform.io/docs/commands/plan.html) e **parâmetro de saída.**

   ```bash
   terraform plan -out assignment.tfplan
   ```

   :::image type="content" source="./media/assign-policy-terraform/terraform-plan-out.png" alt-text="Screenshot de executar o comando init terraform que mostra o download do módulo azurerm e uma mensagem de sucesso.":::

   > [!NOTE]
   > Para obter informações sobre planos de execução e segurança persistentes, consulte [Plano Terraform: Aviso de Segurança](https://www.terraform.io/docs/commands/plan.html#security-warning).

## <a name="apply-the-terraform-execution-plan"></a>Aplicar o plano de execução do Terraform

Por último, aplique o plano de execução.

Executar o [comando de aplicação do terraforme](https://www.terraform.io/docs/commands/apply.html) e especificar o `assignment.tfplan` já criado.

```bash
terraform apply assignment.tfplan
```

:::image type="content" source="./media/assign-policy-terraform/terraform-apply.png" alt-text="Screenshot de executar o comando init terraform que mostra o download do módulo azurerm e uma mensagem de sucesso." mensagem, a atribuição de política é agora criada. Uma vez que definimos o `outputs.tf` ficheiro, o _\_ id de atribuição_ também é devolvido.

## <a name="identify-non-compliant-resources"></a>Identificar recursos que não estão em conformidade

Para ver os recursos que não estão em conformidade com esta nova atribuição, use o _\_ id de atribuição_ devolvido por `terraform apply` . Com ele, executar o seguinte comando para obter os IDs de recursos dos recursos não conformes que são output em um arquivo JSON:

```console
armclient post "/subscriptions/<subscriptionID>/resourceGroups/<rgName>/providers/Microsoft.PolicyInsights/policyStates/latest/queryResults?api-version=2019-10-01&$filter=IsCompliant eq false and PolicyAssignmentId eq '<policyAssignmentID>'&$apply=groupby((ResourceId))" > <json file to direct the output with the resource IDs into>
```

Os resultados assemelham-se ao seguinte exemplo:

```json
{
    "@odata.context": "https://management.azure.com/subscriptions/<subscriptionId>/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest",
    "@odata.count": 3,
    "value": [{
            "@odata.id": null,
            "@odata.context": "https://management.azure.com/subscriptions/<subscriptionId>/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest/$entity",
            "ResourceId": "/subscriptions/<subscriptionId>/resourcegroups/<rgname>/providers/microsoft.compute/virtualmachines/<virtualmachineId>"
        },
        {
            "@odata.id": null,
            "@odata.context": "https://management.azure.com/subscriptions/<subscriptionId>/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest/$entity",
            "ResourceId": "/subscriptions/<subscriptionId>/resourcegroups/<rgname>/providers/microsoft.compute/virtualmachines/<virtualmachine2Id>"
        },
        {
            "@odata.id": null,
            "@odata.context": "https://management.azure.com/subscriptions/<subscriptionId>/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest/$entity",
            "ResourceId": "/subscriptions/<subscriptionName>/resourcegroups/<rgname>/providers/microsoft.compute/virtualmachines/<virtualmachine3ID>"
        }

    ]
}
```

Os resultados são comparáveis aos que normalmente vê listados em **recursos que não estão em conformidade** na vista do portal do Azure.

## <a name="clean-up-resources"></a>Limpar recursos

Para remover a atribuição criada, utilize o CLI Azure ou inverta o plano de execução Terraform com `terraform destroy` .

- CLI do Azure

  ```azurecli-interactive
  az policy assignment delete --name 'audit-vm-manageddisks' --scope '/subscriptions/<subscriptionID>/<resourceGroupName>'
  ```

- Terraform

  ```bash
  terraform destroy assignment.tfplan
  ```

## <a name="next-steps"></a>Passos seguintes

Neste guia de introdução, atribuiu uma definição de política para identificar recursos incompatíveis no seu ambiente do Azure.

Para saber mais sobre a atribuição de políticas para validar que os novos recursos estão em conformidade, continue ao tutorial para:

> [!div class="nextstepaction"]
> [Criar e gerir políticas](./tutorials/create-and-manage.md)
