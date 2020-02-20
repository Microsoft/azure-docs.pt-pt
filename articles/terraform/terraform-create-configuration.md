---
title: Quickstart - Criar uma configuração Terraform para o Azure
description: Inicie com terraforma em Azure implantando um Azure Cosmos DB e Azure Container Instances.
ms.topic: quickstart
ms.date: 10/26/2019
ms.openlocfilehash: 92f66fcfeac69b66eb8cdb0dc58b7a3171d45e10
ms.sourcegitcommit: 64def2a06d4004343ec3396e7c600af6af5b12bb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/19/2020
ms.locfileid: "77470026"
---
# <a name="quickstart-create-a-terraform-configuration-for-azure"></a>Quickstart: Criar uma configuração terraforma para o Azure

Neste arranque rápido, cria-se recursos Azure utilizando a Terraform. Os passos deste artigo passam por você através da criação dos seguintes recursos:

> [!div class="checklist"]
> * Instância DB Azure Cosmos
> * Instância de Contentor do Azure
> * App que funciona através destes dois recursos

## <a name="create-first-configuration"></a>Criar a primeira configuração

Nesta secção, cria-se a configuração para uma instância De Db Do Cosmos Azure.

1. Inicie sessão no [Portal do Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Abra a Concha da Nuvem Azure.

1. Inicie o editor da Cloud Shell:

    ```bash
    code main.tf
    ```

1. A configuração neste passo modela um par de recursos Azure. Estes recursos incluem um grupo de recursos Azure e uma instância Azure Cosmos DB. Um inteiro aleatório é usado para criar um nome único cosmos DB exemplo. Várias configurações de Cosmos DB também estão configuradas. Para mais informações, consulte a [referência Cosmos DB Terraform](https://www.terraform.io/docs/providers/azurerm/r/cosmosdb_account.html). Copiar e colar a seguinte configuração Terraform no editor:

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

1. Guarde o ficheiro **(&lt;Ctrl>S)** e saia do editor **(&lt;Ctrl>Q).**

## <a name="run-the-configuration"></a>Executar a configuração

Nesta secção, utiliza vários comandos Terraform para executar a configuração.

1. O comando [terraforme init](https://www.terraform.io/docs/commands/init.html) inicializa o diretório de trabalho. Executar o seguinte comando em Cloud Shell:

    ```bash
    terraform init
    ```

1. O comando do [plano terraforme](https://www.terraform.io/docs/commands/plan.html) pode ser usado para validar a sintaxe de configuração. O parâmetro `-out` direciona os resultados para um ficheiro. O ficheiro de saída pode ser usado mais tarde para aplicar a configuração. Executar o seguinte comando em Cloud Shell:

    ```bash
    terraform plan --out plan.out
    ```

1. O comando [de aplicação de terraforma](https://www.terraform.io/docs/commands/apply.html) é utilizado para aplicar a configuração. O ficheiro de saída do passo anterior é especificado. Este comando faz com que os recursos do Azure sejam criados. Executar o seguinte comando em Cloud Shell:

    ```bash
    terraform apply plan.out
    ```

1. Para verificar os resultados dentro do portal Azure, navegue para o novo grupo de recursos. A nova instância do Azure Cosmos DB está no novo grupo de recursos.

## <a name="update-configuration"></a>Configuração de atualização

Esta secção mostra como atualizar a configuração para incluir uma instância de contentores Azure. O contentor executa uma aplicação que lê e escreve dados para o Cosmos DB.

1. Inicie o editor da Cloud Shell:

    ```bash
    code main.tf
    ```

1. A configuração neste passo define duas variáveis ambientais: `COSMOS_DB_ENDPOINT` e `COSMOS_DB_MASTERKEY`. Estas variáveis detêm a localização e a chave para aceder à base de dados. Os valores destas variáveis são obtidos a partir da instância de base de dados criada na etapa anterior. Este processo é conhecido como interpolação. Para saber mais sobre a interpolação da Terraforme, consulte [interpolação Syntax](https://www.terraform.io/docs/configuration/interpolation.html). A configuração também inclui um bloco de saída, que devolve o nome de domínio totalmente qualificado (FQDN) da instância do recipiente. Copiar e colar o seguinte código no editor:

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

1. Guarde o ficheiro **(&lt;Ctrl>S)** e saia do editor **(&lt;Ctrl>Q).**

1. Tal como fez na secção anterior, execute o seguinte comando para visualizar as alterações a elegiras:

    ```bash
    terraform plan --out plan.out
    ```

1. Executar o comando `terraform apply` para aplicar a configuração.

    ```bash
    terraform apply plan.out
    ```

1. Tome nota da instância do recipiente FQDN.

## <a name="test-application"></a>Testar aplicação

Para testar a aplicação, navegue para o FQDN da instância do contentor. Deve ver resultados semelhantes aos seguintes resultados:

![Pedido de voto do Azure](media/terraform-quickstart/azure-vote.jpg)

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não for necessário, apague os recursos criados neste artigo.

Executar o [comando de destruição terraforme](https://www.terraform.io/docs/commands/destroy.html) para remover os recursos Azure criados neste tutorial:

```bash
terraform destroy -auto-approve
```

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Instale e configure a Terraform para fornecer recursos Azure.](terraform-install-configure.md)