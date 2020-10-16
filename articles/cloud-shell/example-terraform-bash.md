---
title: Implementar com Terraform a partir de Azure Cloud Shell Microsoft Docs
description: Implementar com Terraform a partir de Azure Cloud Shell
services: Azure
documentationcenter: ''
author: tomarchermsft
manager: routlaw
tags: azure-cloud-shell
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 11/15/2017
ms.author: tarcher
ms.custom: devx-track-terraform
ms.openlocfilehash: e13e6d9ac2f4600e41f221efd23997f712dffc54
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87032090"
---
# <a name="deploy-with-terraform-from-bash-in-azure-cloud-shell"></a>Implementar com Terraform de Bash em Azure Cloud Shell
Este artigo acompanha-o através da criação de um grupo de recursos com o [provedor Terraform AzureRM.](https://www.terraform.io/docs/providers/azurerm/index.html)

[Hashicorp Terraform](https://www.terraform.io/) é uma ferramenta de código aberto que codifica APIs em ficheiros de configuração declarativa que podem ser partilhados entre membros da equipa para serem editados, revistos e versados. O fornecedor Microsoft AzureRM é utilizado para interagir com recursos suportados pelo Azure Resource Manager através das APIs AzureRM.

## <a name="automatic-authentication"></a>Autenticação automática
Terraform é instalado em Bash in Cloud Shell por padrão. Além disso, a Cloud Shell autentica automaticamente a subscrição padrão do Azure CLI para implementar recursos através dos módulos Terraform Azure.

Terraform utiliza a subscrição padrão do Azure CLI que está definida. Para atualizar subscrições predefinidas, execute:

```azurecli-interactive
az account set --subscription mySubscriptionName
```

## <a name="walkthrough"></a>Instruções
### <a name="launch-bash-in-cloud-shell"></a>Lançamento Bash em Cloud Shell
1. Lançar Cloud Shell a partir da sua localização preferida
2. Verifique se a sua subscrição preferida está definida

```azurecli-interactive
az account show
```

### <a name="create-a-terraform-template"></a>Crie um modelo terraform
Crie um novo modelo Terraform nomeado main.tf com o seu editor de texto preferido.

```
vim main.tf
```

Copiar/colar o seguinte código na Cloud Shell.

```
resource "azurerm_resource_group" "myterraformgroup" {
    name = "myRgName"
    location = "West US"
}
```

Guarde o seu ficheiro e saia do seu editor de texto.

### <a name="terraform-init"></a>Init terraform
Comece por `terraform init` correr.

```
justin@Azure:~$ terraform init

Initializing provider plugins...

The following providers do not have any version constraints in configuration,
so the latest version was installed.

To prevent automatic upgrades to new major versions that may contain breaking
changes, it is recommended to add version = "..." constraints to the
corresponding provider blocks in configuration, with the constraint strings
suggested below.

* provider.azurerm: version = "~> 0.2"

Terraform has been successfully initialized!

You may now begin working with Terraform. Try running "terraform plan" to see
any changes that are required for your infrastructure. All Terraform commands
should now work.

If you ever set or change modules or backend configuration for Terraform,
rerun this command to reinitialize your working directory. If you forget, other
commands will detect it and remind you to do so if necessary.
```

O [comando init terraform](https://www.terraform.io/docs/commands/init.html) é utilizado para inicializar um diretório de trabalho contendo ficheiros de configuração Terraform. O `terraform init` comando é o primeiro comando que deve ser executado depois de escrever uma nova configuração Terraform ou clonar um existente a partir do controlo de versão. É seguro executar este comando várias vezes.

### <a name="terraform-plan"></a>Comando plan do Terraform
Pré-visualizar os recursos a criar pelo modelo Terraform com `terraform plan` .

```
justin@Azure:~$ terraform plan
Refreshing Terraform state in-memory prior to plan...
The refreshed state will be used to calculate this plan, but will not be
persisted to local or remote state storage.


------------------------------------------------------------------------

An execution plan has been generated and is shown below.
Resource actions are indicated with the following symbols:
  + create

Terraform will perform the following actions:

  + azurerm_resource_group.demo
      id:       <computed>
      location: "westus"
      name:     "myRGName"
      tags.%:   <computed>


Plan: 1 to add, 0 to change, 0 to destroy.

------------------------------------------------------------------------

Note: You didn't specify an "-out" parameter to save this plan, so Terraform
can't guarantee that exactly these actions will be performed if
"terraform apply" is subsequently run.
```

O [comando terraform plan](https://www.terraform.io/docs/commands/plan.html) é utilizado para criar um plano de execução. Terraform executa uma atualização, a menos que explicitamente desativado, e, em seguida, determina que ações são necessárias para alcançar o estado desejado especificado nos ficheiros de configuração. O plano pode ser salvo usando -out, e depois fornecido para aplicar terraform para garantir que apenas as ações pré-planeadas são executadas.

### <a name="terraform-apply"></a>Comando apply do Terraform
A provisionar os recursos Azure com `terraform apply` .

```
justin@Azure:~$ terraform apply
azurerm_resource_group.demo: Creating...
  location: "" => "westus"
  name:     "" => "myRGName"
  tags.%:   "" => "<computed>"
azurerm_resource_group.demo: Creation complete after 0s (ID: /subscriptions/mySubIDmysub/resourceGroups/myRGName)

Apply complete! Resources: 1 added, 0 changed, 0 destroyed.
```

O [comando de aplicação de terraformes](https://www.terraform.io/docs/commands/apply.html) é utilizado para aplicar as alterações necessárias para atingir o estado desejado da configuração.

### <a name="verify-deployment-with-azure-cli"></a>Verificar a implantação com o Azure CLI
Corra `az group show -n myRgName` para verificar se o recurso conseguiu o fornecimento.

```azurecli-interactive
az group show -n myRgName
```

### <a name="clean-up-with-terraform-destroy"></a>Limpe com destruição de terraforme
Limpe o grupo de recursos criado com o [comando terraform para limpar](https://www.terraform.io/docs/commands/destroy.html) a infraestrutura criada pela Terraform.

```
justin@Azure:~$ terraform destroy
azurerm_resource_group.demo: Refreshing state... (ID: /subscriptions/mySubID/resourceGroups/myRGName)

An execution plan has been generated and is shown below.
Resource actions are indicated with the following symbols:
  - destroy

Terraform will perform the following actions:

  - azurerm_resource_group.demo


Plan: 0 to add, 0 to change, 1 to destroy.

Do you really want to destroy?
  Terraform will destroy all your managed infrastructure, as shown above.
  There is no undo. Only 'yes' will be accepted to confirm.

  Enter a value: yes

azurerm_resource_group.demo: Destroying... (ID: /subscriptions/mySubID/resourceGroups/myRGName)
azurerm_resource_group.demo: Still destroying... (ID: /subscriptions/mySubID/resourceGroups/myRGName, 10s elapsed)
azurerm_resource_group.demo: Still destroying... (ID: /subscriptions/mySubID/resourceGroups/myRGName, 20s elapsed)
azurerm_resource_group.demo: Still destroying... (ID: /subscriptions/mySubID/resourceGroups/myRGName, 30s elapsed)
azurerm_resource_group.demo: Still destroying... (ID: /subscriptions/mySubID/resourceGroups/myRGName, 40s elapsed)
azurerm_resource_group.demo: Destruction complete after 45s

Destroy complete! Resources: 1 destroyed.
```

Criou com sucesso um recurso Azure através da Terraform. Visite os próximos passos para continuar a aprender sobre a Cloud Shell.

## <a name="next-steps"></a>Passos seguintes
[Conheça o fornecedor Terraform Azure](https://www.terraform.io/docs/providers/azurerm/#)<br>
[Bash em Cloud Shell quickstart](quickstart.md)
