---
title: Aprovisionar uma instância do Azure Spring Cloud com terraform
description: Provisionar um exemplo de Nuvem de primavera Azure com Terraform.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: how-to
ms.date: 06/26/2020
ms.custom: devx-track-java
ms.openlocfilehash: 060ef2d08b849706b47b24748142c608292971b5
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/23/2021
ms.locfileid: "104878457"
---
# <a name="provision-an-azure-spring-cloud-instance-with-terraform"></a>Provisionar um exemplo de Nuvem de primavera Azure com Terraform

**Este artigo aplica-se a:** ✔️ Java ✔️ C #

Este exemplo cria uma instância Azure Spring Cloud usando Terraform. Os procedimentos acompanham-no através da criação dos seguintes recursos:

> [!div class="checklist"]
> * Grupo de Recursos
> * Exemplo de nuvem de primavera de Azure
> * Armazenamento Azure para Analítica de Log

> [!NOTE]
> Para suporte específico da Terraform, utilize um dos canais de apoio à comunidade da HashiCorp para a Terraform:
>
> * Perguntas, casos de uso e padrões úteis: [Secção terraforme do portal comunitário HashiCorp](https://discuss.hashicorp.com/c/terraform-core)
> * Questões relacionadas com o fornecedor: [Secção de Fornecedores Terraform do portal comunitário HashiCorp](https://discuss.hashicorp.com/c/terraform-providers)

## <a name="prerequisites"></a>Pré-requisitos

- **Subscrição do Azure**: se não tem uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) antes de começar.

## <a name="create-configuration-file"></a>Criar um ficheiro de configuração

1. Inicie sessão no [portal do Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Abra a [Concha da Nuvem Azure](../app-service/quickstart-java.md#use-azure-cloud-shell).

1. Inicie o editor da Cloud Shell:

    ```bash
    code main.tf
    ```

1. A configuração neste passo modela recursos Azure, incluindo um grupo de recursos Azure e uma instância Azure Spring Cloud.

    ```hcl
    provider "azurerm" {
        features {}
    }

    resource "azurerm_resource_group" "example" {
      name     = "username"
      location = "eastus"
    }

    resource "azurerm_spring_cloud_service" "example" {
      name                = "usernamesp"
      resource_group_name = azurerm_resource_group.example.name
      location            = azurerm_resource_group.example.location

      config_server_git_setting {
        uri          = "https://github.com/Azure-Samples/piggymetrics-config"
        label        = "master"
        search_paths = ["."]
      }
    }
    ```

1. Guarde o ficheiro **&lt; (Ctrl>S)** e saia do editor **&lt; (CTRL>Q).**

## <a name="apply-the-configuration"></a>Aplicar a configuração

Nesta secção, utiliza-se vários comandos Terraform para executar a configuração.

1. O comando [terraform init](https://www.terraform.io/docs/commands/init.html) inicializa o diretório de trabalho. Executar o seguinte comando em Cloud Shell:

    ```bash
    terraform init
    ```

1. O [plano terraforme de](https://www.terraform.io/docs/commands/plan.html) comando é utilizado para validar a sintaxe de configuração. O `-out` parâmetro direciona os resultados para um ficheiro. O ficheiro de saída pode ser usado mais tarde para aplicar a configuração. Executar o seguinte comando em Cloud Shell:

    ```bash
    terraform plan --out plan.out
    ```

1. O [comando terraforme aplicado](https://www.terraform.io/docs/commands/apply.html) é utilizado para aplicar a configuração. O comando especifica o ficheiro de saída do passo anterior. Este comando cria os recursos Azure. Executar o seguinte comando em Cloud Shell:

    ```bash
    terraform apply plan.out
    ```

1. Para verificar os resultados dentro do portal Azure, consulte o novo grupo de recursos. A nova instância **Azure Spring Cloud** mostra no novo grupo de recursos.

## <a name="update-configuration-to-config-logs-and-metrics"></a>Configuração de atualização para config registos e métricas

Esta secção mostra como atualizar a configuração para ativar o registo e métricas para Azure Spring Cloud com uma conta de Armazenamento Azure.

1. Inicie o editor da Cloud Shell:

    ```bash
    code main.tf
    ```

1. Adicione a seguinte configuração no final do ficheiro:

    ```hcl
    resource "azurerm_storage_account" "example" {
      name                     = "usernamest"
      resource_group_name      = azurerm_resource_group.example.name
      location                 = azurerm_resource_group.example.location
      account_tier             = "Standard"
      account_replication_type = "GRS"
    }

    resource "azurerm_monitor_diagnostic_setting" "example" {
      name               = "example"
      target_resource_id = "${azurerm_spring_cloud_service.example.id}"
      storage_account_id = "${azurerm_storage_account.example.id}"

      log {
        category = "SystemLogs"
        enabled  = true

        retention_policy {
          enabled = false
        }
      }

      metric {
        category = "AllMetrics"

        retention_policy {
          enabled = false
        }
      }
    }
    ```

1. Guarde o ficheiro **&lt; (CTRL>S),** e saia do editor (**&lt; CTRL>Q).**

1. Como na secção anterior, executar o seguinte comando para então fazer as alterações:

    ```bash
    terraform plan --out plan.out
    ```

1. Executar o `terraform apply` comando para aplicar a configuração.

    ```bash
    terraform apply plan.out
    ```

## <a name="clean-up-resources"></a>Limpar os recursos

Quando já não for necessário, elimine os recursos criados neste artigo.

Executar o comando [de destruição de terraformes](https://www.terraform.io/docs/commands/destroy.html) para remover os recursos Azure criados neste exercício:

```bash
terraform destroy -auto-approve
```

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Instale e configuure Terraform para a disponibilização de recursos Azure](/azure/developer/terraform/getting-started-cloud-shell).