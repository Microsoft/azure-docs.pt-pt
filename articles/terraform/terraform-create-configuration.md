---
title: Início rápido-criar uma configuração do Terraform para o Azure
description: Introdução ao Terraform no Azure implantando uma Azure Cosmos DB e instâncias de contêiner do Azure.
ms.topic: quickstart
ms.date: 10/26/2019
ms.openlocfilehash: 47eec1f238d3fdff99b75953bc6d7e92137cca2d
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/18/2019
ms.locfileid: "74159325"
---
# <a name="quickstart-create-a-terraform-configuration-for-azure"></a>Início rápido: criar uma configuração do Terraform para o Azure

Neste guia de início rápido, você cria recursos do Azure usando o Terraform. As etapas neste artigo o orientarão na criação dos seguintes recursos:

> [!div class="checklist"]
> * Instância de Azure Cosmos DB
> * Instância de Contentor do Azure
> * Aplicativo que funciona entre esses dois recursos

## <a name="create-first-configuration"></a>Criar primeira configuração

Nesta seção, você cria a configuração para uma instância de Azure Cosmos DB.

1. Inicie sessão no [portal do Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Abra o Azure Cloud Shell.

1. Inicie o editor de Cloud Shell:

    ```bash
    code main.tf
    ```

1. A configuração nesta etapa modela alguns recursos do Azure. Esses recursos incluem um grupo de recursos do Azure e uma instância de Azure Cosmos DB. Um inteiro aleatório é usado para criar um nome de instância de Cosmos DB exclusivo. Várias configurações de Cosmos DB também são configuradas. Para obter mais informações, consulte a [referência do cosmos DB Terraform](https://www.terraform.io/docs/providers/azurerm/r/cosmosdb_account.html). Copie e cole a seguinte configuração de Terraform no editor:

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
      location            = azurerm_resource_group.vote-resource-group.location
      resource_group_name = azurerm_resource_group.vote-resource-group.name
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

1. Salve o arquivo ( **&lt;ctrl > S**) e saia do editor ( **&lt;CTRL > Q**).

## <a name="run-the-configuration"></a>Executar a configuração

Nesta seção, você usa vários comandos Terraform para executar a configuração.

1. O comando [Terraform init](https://www.terraform.io/docs/commands/init.html) Inicializa o diretório de trabalho. Execute o seguinte comando no Cloud Shell:

    ```bash
    terraform init
    ```

1. O comando de [plano Terraform](https://www.terraform.io/docs/commands/plan.html) pode ser usado para validar a sintaxe de configuração. O parâmetro `-out` direciona os resultados para um arquivo. O arquivo de saída pode ser usado posteriormente para aplicar a configuração. Execute o seguinte comando no Cloud Shell:

    ```bash
    terraform plan --out plan.out
    ```

1. O comando [Terraform Apply](https://www.terraform.io/docs/commands/apply.html) é usado para aplicar a configuração. O arquivo de saída da etapa anterior foi especificado. Esse comando faz com que os recursos do Azure sejam criados. Execute o seguinte comando no Cloud Shell:

    ```bash
    terraform apply plan.out
    ```

1. Para verificar os resultados dentro do portal do Azure, navegue até o novo grupo de recursos. A nova instância de Azure Cosmos DB está no novo grupo de recursos.

## <a name="update-configuration"></a>Atualizar configuração

Esta seção mostra como atualizar a configuração para incluir uma instância de contêiner do Azure. O contêiner executa um aplicativo que lê e grava dados no Cosmos DB.

1. Inicie o editor de Cloud Shell:

    ```bash
    code main.tf
    ```

1. A configuração nesta etapa define duas variáveis de ambiente: `COSMOS_DB_ENDPOINT` e `COSMOS_DB_MASTERKEY`. Essas variáveis mantêm o local e a chave para acessar o banco de dados. Os valores dessas variáveis são obtidos da instância de banco de dados criada na etapa anterior. Esse processo é conhecido como interpolação. Para saber mais sobre a interpolação de Terraform, consulte [sintaxe de interpolação](https://www.terraform.io/docs/configuration/interpolation.html). A configuração também inclui um bloco de saída, que retorna o FQDN (nome de domínio totalmente qualificado) da instância de contêiner. Copie e cole o seguinte código no editor:

    ```hcl
    resource "azurerm_container_group" "vote-aci" {
      name                = "vote-aci"
      location            = azurerm_resource_group.vote-resource-group.location
      resource_group_name = azurerm_resource_group.vote-resource-group.name
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
          "COSMOS_DB_ENDPOINT"  = azurerm_cosmosdb_account.vote-cosmos-db.endpoint
          "COSMOS_DB_MASTERKEY" = azurerm_cosmosdb_account.vote-cosmos-db.primary_master_key
          "TITLE"               = "Azure Voting App"
          "VOTE1VALUE"          = "Cats"
          "VOTE2VALUE"          = "Dogs"
        }
      }
    }

    output "dns" {
      value = azurerm_container_group.vote-aci.fqdn
    }
    ```

1. Salve o arquivo ( **&lt;ctrl > S**) e saia do editor ( **&lt;CTRL > Q**).

1. Como você fez na seção anterior, execute o seguinte comando para Visual as alterações a serem feitas:

    ```bash
    terraform plan --out plan.out
    ```

1. Execute o comando `terraform apply` para aplicar a configuração.

    ```bash
    terraform apply plan.out
    ```

1. Anote a instância do contêiner FQDN.

## <a name="test-application"></a>Testar aplicação

Para testar o aplicativo, navegue até o FQDN da instância de contêiner. Você deverá ver resultados semelhantes à seguinte saída:

![Aplicativo de voto do Azure](media/terraform-quickstart/azure-vote.jpg)

## <a name="clean-up-resources"></a>Limpar recursos

Quando não for mais necessário, exclua os recursos criados neste artigo.

Execute o comando [Terraform Destroy](https://www.terraform.io/docs/commands/destroy.html) para remover os recursos do Azure criados neste tutorial:

```bash
terraform destroy -auto-approve
```

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Instale e configure o Terraform para provisionar recursos do Azure](../virtual-machines/linux/terraform-install-configure.md).