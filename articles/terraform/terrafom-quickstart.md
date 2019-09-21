---
title: Introdução ao Terraform no Azure.
description: Introdução ao Terraform no Azure implantando uma Azure Cosmos DB e instâncias de contêiner do Azure.
services: terraform
author: neilpeterson
ms.service: azure
ms.topic: quickstart
ms.date: 09/20/2019
ms.author: nepeters
ms.openlocfilehash: c53f3a31b46f00d3207cd8f47dcfbfa131c03666
ms.sourcegitcommit: f2771ec28b7d2d937eef81223980da8ea1a6a531
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/20/2019
ms.locfileid: "71173513"
---
# <a name="create-a-terraform-configuration-for-azure"></a>Criar uma configuração do Terraform para o Azure

Neste exemplo, você tem experiência na criação de uma configuração do Terraform e na implantação dessa configuração no Azure. Quando concluído, você terá implantado uma instância de Azure Cosmos DB, uma instância de contêiner do Azure e um aplicativo que funcionará entre esses dois recursos. Este documento pressupõe que todo o trabalho seja concluído no Azure Cloud Shell, que tem as ferramentas Terraform pré-instaladas. Se você quiser trabalhar com o exemplo em seu próprio sistema, o Terraform poderá ser instalado usando as instruções encontradas [aqui](../virtual-machines/linux/terraform-install-configure.md).

## <a name="create-first-configuration"></a>Criar primeira configuração

Nesta seção, você criará a configuração para uma instância de Azure Cosmos DB.

Selecione **experimentar agora** para abrir o Azure cloud Shell. Uma vez aberto, insira `code .` em para abrir o editor de código do Cloud Shell.

```bash
code .
```

Copie e cole a seguinte configuração de Terraform.

Essa configuração modela um grupo de recursos do Azure, um inteiro aleatório e uma instância de Azure Cosmos DB. O inteiro aleatório é usado no Cosmos DB nome da instância. Várias configurações de Cosmos DB também são configuradas. Para obter uma lista completa de Cosmos DB configurações do Terraform, consulte a [referência do Terraform do cosmos DB](https://www.terraform.io/docs/providers/azurerm/r/cosmosdb_account.html).

Salve o arquivo como `main.tf` quando terminar. Essa operação pode ser feita usando as reticências na parte superior direita do editor de código.

```hcl
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

O comando [Terraform init](https://www.terraform.io/docs/commands/init.html) Inicializa o diretório de trabalho. Execute `terraform init` no terminal do Cloud Shell para se preparar para a implantação da nova configuração.

```bash
terraform init
```

O comando [Terraform Plan](https://www.terraform.io/docs/commands/plan.html) pode ser usado para validar que a configuração está formatada corretamente e para visualizar quais recursos serão criados, atualizados ou destruídos. Os resultados podem ser armazenados em um arquivo e usados posteriormente para aplicar a configuração.

Execute `terraform plan` para testar a nova configuração de Terraform.

```bash
terraform plan --out plan.out
```

Aplique a configuração usando [Terraform Apply](https://www.terraform.io/docs/commands/apply.html) e especificando o nome do arquivo de plano. Este comando implanta os recursos em sua assinatura do Azure.

```bash
terraform apply plan.out
```

Depois de concluído, você pode ver que o grupo de recursos foi criado e uma instância de Azure Cosmos DB colocada no grupo de recursos.

## <a name="update-configuration"></a>Atualizar configuração

Atualize a configuração para incluir uma instância de contêiner do Azure. O contêiner executa um aplicativo que lê e grava dados no Cosmos DB.

Copie a configuração a seguir na parte inferior do `main.tf` arquivo. Salve o arquivo quando terminar.

Duas variáveis de ambiente são definidas `COSMOS_DB_ENDPOINT` , `COSMOS_DB_MASTERKEY`e. Essas variáveis mantêm o local e a chave para acessar o banco de dados. Os valores dessas variáveis são obtidos da instância de banco de dados criada na última etapa. Esse processo é conhecido como interpolação. Para saber mais sobre a interpolação de Terraform, consulte [sintaxe de interpolação](https://www.terraform.io/docs/configuration/interpolation.html).


A configuração também inclui um bloco de saída, que retorna o FQDN (nome de domínio totalmente qualificado) da instância de contêiner.

```hcl
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
    ports {
      port     = 80
      protocol = "TCP"
    }

    secure_environment_variables = {
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

Execute `terraform plan` para criar o plano atualizado e visualizar as alterações a serem feitas. Você deve ver que um recurso de instância de contêiner do Azure foi adicionado à configuração.

```bash
terraform plan --out plan.out
```

Por fim, `terraform apply` execute para aplicar a configuração.

```bash
terraform apply plan.out
```

Depois de concluído, anote o FQDN da instância de contêiner.

## <a name="test-application"></a>Testar aplicação

Navegue até o FQDN da instância de contêiner. Se tudo tiver sido configurado corretamente, você deverá ver o aplicativo a seguir.

![Aplicativo de voto do Azure](media/terraform-quickstart/azure-vote.jpg)

## <a name="clean-up-resources"></a>Limpar recursos

Quando terminar, os recursos e o grupo de recursos do Azure poderão ser removidos usando o comando [Terraform Destroy](https://www.terraform.io/docs/commands/destroy.html) .

```bash
terraform destroy -auto-approve
```

## <a name="next-steps"></a>Passos seguintes

Neste exemplo, você criou, implantou e destruiu uma configuração de Terraform. Para obter mais informações sobre como usar o Terraform no Azure, consulte a documentação do provedor de Terraform do Azure.

> [!div class="nextstepaction"]
> [Provedor de Terraform do Azure](https://www.terraform.io/docs/providers/azurerm/)
