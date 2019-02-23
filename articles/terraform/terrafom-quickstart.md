---
title: Introdução ao Terraform no Azure.
description: Comece a utilizar com o Terraform no Azure ao implementar um Azure Cosmos DB e o Azure Container Instances.
services: terraform
author: neilpeterson
ms.service: terraform
ms.topic: quickstart
ms.date: 02/04/2019
ms.author: nepeters
ms.openlocfilehash: b3d872008c95d94a8401e6f38665c3c88616a168
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/23/2019
ms.locfileid: "56736928"
---
# <a name="create-a-terraform-configuration-for-azure"></a>Criar uma configuração do Terraform do Azure

Neste exemplo, ganha experiência na criação de uma configuração de Terraform e implementar esta configuração para o Azure. Quando concluída, será tiver implementado uma instância do Azure Cosmos DB, uma instância de contentor do Azure e um aplicativo que funcione entre esses dois recursos. Este documento parte do princípio de que todo o trabalho é concluído no Azure Cloud Shell, que tem as ferramentas do Terraform pré-instalado. Se gostaria de trabalhar com o exemplo no seu próprio sistema, Terraform pode ser instalado utilizando as instruções presentes [aqui](../virtual-machines/linux/terraform-install-configure.md).

## <a name="create-first-configuration"></a>Criar a configuração do primeiro

Nesta secção, irá criar a configuração para uma instância do Azure Cosmos DB.

Selecione **experimentar agora** abrir a shell de cloud do Azure. Depois de abrir, introduza no `code .` para abrir o editor de código do cloud shell.

```azurecli-interactive
code .
```

Copie e cole a seguinte configuração de Terraform.

Esta configuração modela um grupo de recursos do Azure, um número inteiro aleatório e uma instância do Azure Cosmos DB. O número inteiro aleatório é utilizado no nome da instância do Cosmos DB. Também estão configuradas várias definições de Cosmos DB. Para obter uma lista completa das configurações do Terraform do Cosmos DB, consulte a [referência do Terraform do Cosmos DB](https://www.terraform.io/docs/providers/azurerm/r/cosmosdb_account.html).

Guarde o ficheiro como `main.tf` quando tiver terminado. Esta operação pode ser feita usando as reticências na parte superior direita do editor de código.

```azurecli-interactive
resource "azurerm_resource_group" "vote-resource-group" {
  name     = "vote-resource-group"
  location = "westus"
}

resource "random_integer" "ri" {
  min = 10000
  max = 99999
}

resource "azurerm_cosmosdb_account" "vote-cosmos-db" {
  name                = "tfex-cosmos-db-${random_integer.ri.result}"
  location            = "${azurerm_resource_group.vote-resource-group.location}"
  resource_group_name = "${azurerm_resource_group.vote-resource-group.name}"
  offer_type          = "Standard"
  kind                = "GlobalDocumentDB"

  consistency_policy {
    consistency_level       = "BoundedStaleness"
    max_interval_in_seconds = 10
    max_staleness_prefix    = 200
  }

  geo_location {
    location          = "westus"
    failover_priority = 0
  }
}
```

O [terraform init](https://www.terraform.io/docs/commands/init.html) comando inicializa o diretório de trabalho. Executar `terraform init` no terminal para preparar a implementação da nova configuração do cloud shell.

```azurecli-interactive
terraform init
```

O [plano do terraform](https://www.terraform.io/docs/commands/plan.html) comando pode ser utilizado para validar que a configuração está formatada corretamente e para visualizar a que recursos irão ser criados, atualizados ou destruídos. Os resultados podem ser armazenados num arquivo e utilizados posteriormente para aplicar a configuração.

Executar `terraform plan` para testar a nova configuração de Terraform.

```azurecli-interactive
terraform plan --out plan.out
```

Aplicam-se de que a configuração através de [aplicam-se do terraform](https://www.terraform.io/docs/commands/apply.html) e especificando o nome do ficheiro de plano. Este comando implementa os recursos na sua subscrição do Azure.

```azurecli-interactive
terraform apply plan.out
```

Quando tiver terminado, pode ver que foi criado o grupo de recursos e uma instância do Azure Cosmos DB colocados no grupo de recursos.

## <a name="update-configuration"></a>Atualizar a configuração

Atualize a configuração para incluir uma instância de contentor do Azure. O contentor executa uma aplicação que lê e escreve dados para o Cosmos DB.

Copie a seguinte configuração para o fim do `main.tf` ficheiro. Guarde o ficheiro quando terminar.

Duas variáveis de ambiente são definidas, `COSMOS_DB_ENDPOINT` e `COSMOS_DB_MASTERKEY`. Estas variáveis de manter a localização e a chave para aceder à base de dados. Os valores para estas variáveis são obtidos a partir da instância de base de dados criada no último passo. Este processo é conhecido como interpolação. Para saber mais sobre o Terraform interpolação, veja [sintaxe de interpolação](https://www.terraform.io/docs/configuration/interpolation.html).


A configuração inclui também um bloco de saída, que retorna o nome de domínio completamente qualificado (FQDN) da instância do contentor.

```azurecli-interactive
resource "azurerm_container_group" "vote-aci" {
  name                = "vote-aci"
  location            = "${azurerm_resource_group.vote-resource-group.location}"
  resource_group_name = "${azurerm_resource_group.vote-resource-group.name}"
  ip_address_type     = "public"
  dns_name_label      = "vote-aci"
  os_type             = "linux"

  container {
    name   = "vote-aci"
    image  = "microsoft/azure-vote-front:cosmosdb"
    cpu    = "0.5"
    memory = "1.5"
    ports  = {
      port     = 80
      protocol = "TCP"
    }

    secure_environment_variables {
      "COSMOS_DB_ENDPOINT"  = "${azurerm_cosmosdb_account.vote-cosmos-db.endpoint}"
      "COSMOS_DB_MASTERKEY" = "${azurerm_cosmosdb_account.vote-cosmos-db.primary_master_key}"
      "TITLE"               = "Azure Voting App"
      "VOTE1VALUE"          = "Cats"
      "VOTE2VALUE"          = "Dogs"
    }
  }
}

output "dns" {
  value = "${azurerm_container_group.vote-aci.fqdn}"
}
```

Executar `terraform plan` para criar o plano foi atualizado e visualizar as alterações sejam feitas. Deverá ver que um recurso de instância de contentor do Azure foi adicionado à configuração.

```azurecli-interactive
terraform plan --out plan.out
```

Por fim, execute `terraform apply` para aplicar a configuração.

```azurecli-interactive
terraform apply plan.out
```

Depois de concluído, tome nota da instância de contentor FQDN.

## <a name="test-application"></a>Testar aplicação

Navegue para o FQDN da instância do contentor. Se tudo o que foi configurado corretamente, deverá ver a aplicação seguinte.

![Aplicação de voto do Azure](media/terraform-quickstart/azure-vote.jpg)

## <a name="clean-up-resources"></a>Limpar recursos

Quando terminar, os recursos do Azure e do grupo de recursos podem ser removidas com o [terraform destruir](https://www.terraform.io/docs/commands/destroy.html) comando.

```azurecli-interactive
terraform destroy -auto-approve
```

## <a name="next-steps"></a>Passos Seguintes

Neste exemplo, criado, implantado e destruído uma configuração de Terraform. Para obter mais informações sobre como utilizar o Terraform no Azure, consulte a documentação de fornecedor do Terraform do Azure.

> [!div class="nextstepaction"]
> [Fornecedor do Terraform do Azure](https://www.terraform.io/docs/providers/azurerm/)
