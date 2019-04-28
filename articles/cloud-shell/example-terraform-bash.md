---
title: Implementar com o Terraform no Bash no Azure Cloud Shell | Documentos da Microsoft
description: Implementar com o Terraform no Bash no Azure Cloud Shell
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
ms.openlocfilehash: a08a4e7df6cf0493ab1aa6aced1abf888a61072a
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "62119117"
---
# <a name="deploy-with-terraform-from-bash-in-azure-cloud-shell"></a>Implementar com o Terraform no Bash no Azure Cloud Shell
Este artigo explica como criar um grupo de recursos com o [fornecedor do Terraform AzureRM](https://www.terraform.io/docs/providers/azurerm/index.html). 

[Terraform da Hashicorp](https://www.terraform.io/) é uma ferramenta de código-fonte aberto que codifies APIs em arquivos de configuração declarativa que podem ser partilhados entre os membros da Equipe para ser editado, revisto e com a versão. O fornecedor de Microsoft AzureRM é utilizado para interagir com os recursos suportados pelo Azure Resource Manager por meio das APIs do AzureRM. 

## <a name="automatic-authentication"></a>Autenticação automática
Por predefinição, Terraform está instalado no Bash no Cloud Shell. Além disso, o Cloud Shell autentica automaticamente sua assinatura da CLI do Azure de padrão para implementar recursos por meio de módulos do Terraform Azure.

Terraform utiliza a subscrição de CLI do Azure de predefinição está definida. Para atualizar a predefinição, as subscrições, execute:

```azurecli-interactive
az account set --subscription mySubscriptionName
```

## <a name="walkthrough"></a>Instruções
### <a name="launch-bash-in-cloud-shell"></a>Inicie o Bash no Cloud Shell
1. Iniciar Cloud Shell no seu local preferido
2. Certifique-se de que a subscrição preferencial está definida

```azurecli-interactive
az account show
```

### <a name="create-a-terraform-template"></a>Criar um modelo do Terraform
Crie um novo modelo de Terraform com o nome main.tf com seu editor de texto preferido.

```
vim main.tf
```

Copiar/colar o código a seguir no Cloud Shell.

```
resource "azurerm_resource_group" "myterraformgroup" {
    name = "myRgName"
    location = "West US"
}
```

Guardar o ficheiro e saia do editor de texto.

### <a name="terraform-init"></a>Terraform init
Começar executando o `terraform init`.

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

O [terraform init comando](https://www.terraform.io/docs/commands/init.html) é usado para inicializar um diretório de trabalho que contém ficheiros de configuração do Terraform. O `terraform init` comando é o primeiro comando que deve ser executado depois de escrever uma nova configuração de Terraform ou clonagem de um já existente no controle de versão. É seguro executar este comando várias vezes.

### <a name="terraform-plan"></a>Comando plan do Terraform
Pré-visualizar os recursos a ser criado pelo modelo de Terraform com `terraform plan`.

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

O [comando terraform plan](https://www.terraform.io/docs/commands/plan.html) é utilizado para criar um plano de execução. Terraform efetua uma atualização, a menos que explicitamente desabilitado e, em seguida, determina quais ações são necessárias para obter o estado pretendido especificado nos arquivos de configuração. O plano pode ser salvo usando-out e, em seguida, disponibilizado a terraform aplicam-se para garantir que apenas as ações em planeadas previamente são executadas.

### <a name="terraform-apply"></a>Comando apply do Terraform
Aprovisionar os recursos do Azure com `terraform apply`.

```
justin@Azure:~$ terraform apply
azurerm_resource_group.demo: Creating...
  location: "" => "westus"
  name:     "" => "myRGName"
  tags.%:   "" => "<computed>"
azurerm_resource_group.demo: Creation complete after 0s (ID: /subscriptions/mySubIDmysub/resourceGroups/myRGName)

Apply complete! Resources: 1 added, 0 changed, 0 destroyed.
```

O [terraform aplicar comandos](https://www.terraform.io/docs/commands/apply.html) é utilizado para aplicar as alterações necessárias para alcançar o estado pretendido da configuração.

### <a name="verify-deployment-with-azure-cli"></a>Certifique-se a implementação com a CLI do Azure
Executar `az group show -n myRgName` para verificar o recurso foi bem sucedida de aprovisionamento.

```azcliinteractive
az group show -n myRgName
```

### <a name="clean-up-with-terraform-destroy"></a>Limpeza do wsu com o terraform destruir
Limpar o grupo de recursos criado com o [Terraform destruir comando](https://www.terraform.io/docs/commands/destroy.html) para limpar a infraestrutura de criado o Terraform.

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

Criou um recurso do Azure através do Terraform com êxito. Visite os passos seguintes para saber mais sobre o Cloud Shell.

## <a name="next-steps"></a>Passos Seguintes
[Saiba mais sobre o fornecedor do Azure do Terraform](https://www.terraform.io/docs/providers/azurerm/#)<br>
[Guia de introdução do Cloud Shell de bash](quickstart.md)
