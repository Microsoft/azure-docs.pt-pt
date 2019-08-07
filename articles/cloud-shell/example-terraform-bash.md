---
title: Implantar com Terraform de Azure Cloud Shell | Microsoft Docs
description: Implantar com Terraform de Azure Cloud Shell
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
ms.openlocfilehash: f5939251729905d349b79a94411cf87e3873b279
ms.sourcegitcommit: d060947aae93728169b035fd54beef044dbe9480
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/02/2019
ms.locfileid: "68742070"
---
# <a name="deploy-with-terraform-from-bash-in-azure-cloud-shell"></a>Implantar com Terraform do bash no Azure Cloud Shell
Este artigo orienta você pela criação de um grupo de recursos com o [provedor Terraform AzureRM](https://www.terraform.io/docs/providers/azurerm/index.html). 

[Hashicorp Terraform](https://www.terraform.io/) é uma ferramenta de software livre que codifica APIs em arquivos de configuração declarativos que podem ser compartilhados entre os membros da equipe para serem editados, revisados e com controle de versão. O provedor Microsoft AzureRM é usado para interagir com recursos com suporte pelo Azure Resource Manager por meio das APIs do AzureRM. 

## <a name="automatic-authentication"></a>Autenticação automática
O Terraform é instalado no bash em Cloud Shell por padrão. Além disso, Cloud Shell autentica automaticamente sua assinatura de CLI do Azure padrão para implantar recursos por meio dos módulos do Azure Terraform.

Terraform usa a assinatura de CLI do Azure padrão que está definida. Para atualizar as assinaturas padrão, execute:

```azurecli-interactive
az account set --subscription mySubscriptionName
```

## <a name="walkthrough"></a>Instruções
### <a name="launch-bash-in-cloud-shell"></a>Iniciar bash no Cloud Shell
1. Iniciar o Cloud Shell do seu local preferido
2. Verifique se sua assinatura preferida está definida

```azurecli-interactive
az account show
```

### <a name="create-a-terraform-template"></a>Criar um modelo Terraform
Crie um novo modelo Terraform chamado main.tf com seu editor de texto preferido.

```
vim main.tf
```

Copie/cole o código a seguir em Cloud Shell.

```
resource "azurerm_resource_group" "myterraformgroup" {
    name = "myRgName"
    location = "West US"
}
```

Salve o arquivo e saia do editor de texto.

### <a name="terraform-init"></a>Terraform init
Comece executando `terraform init`.

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

O [comando Terraform init](https://www.terraform.io/docs/commands/init.html) é usado para inicializar um diretório de trabalho que contém os arquivos de configuração do Terraform. O `terraform init` comando é o primeiro comando que deve ser executado após a gravação de uma nova configuração do Terraform ou a clonagem de uma existente do controle de versão. É seguro executar esse comando várias vezes.

### <a name="terraform-plan"></a>Comando plan do Terraform
Visualize os recursos a serem criados pelo modelo Terraform com `terraform plan`.

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

O [comando terraform plan](https://www.terraform.io/docs/commands/plan.html) é utilizado para criar um plano de execução. O Terraform executa uma atualização, a menos que explicitamente desabilitado e, em seguida, determina quais ações são necessárias para atingir o estado desejado especificado nos arquivos de configuração. O plano pode ser salvo usando-out e, em seguida, fornecido a Terraform Apply para garantir que apenas as ações predefinidas sejam executadas.

### <a name="terraform-apply"></a>Comando apply do Terraform
Provisione os recursos `terraform apply`do Azure com o.

```
justin@Azure:~$ terraform apply
azurerm_resource_group.demo: Creating...
  location: "" => "westus"
  name:     "" => "myRGName"
  tags.%:   "" => "<computed>"
azurerm_resource_group.demo: Creation complete after 0s (ID: /subscriptions/mySubIDmysub/resourceGroups/myRGName)

Apply complete! Resources: 1 added, 0 changed, 0 destroyed.
```

O [comando Terraform Apply](https://www.terraform.io/docs/commands/apply.html) é usado para aplicar as alterações necessárias para alcançar o estado desejado da configuração.

### <a name="verify-deployment-with-azure-cli"></a>Verificar a implantação com CLI do Azure
Execute `az group show -n myRgName` para verificar se o recurso teve êxito no provisionamento.

```azcliinteractive
az group show -n myRgName
```

### <a name="clean-up-with-terraform-destroy"></a>Limpar com Terraform Destroy
Limpe o grupo de recursos criado com o [comando Terraform Destroy](https://www.terraform.io/docs/commands/destroy.html) para limpar a infraestrutura criada pelo Terraform.

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

Você criou com êxito um recurso do Azure por meio do Terraform. Visite as próximas etapas para continuar aprendendo sobre Cloud Shell.

## <a name="next-steps"></a>Passos Seguintes
[Saiba mais sobre o provedor do Terraform Azure](https://www.terraform.io/docs/providers/azurerm/#)<br>
[Bash no guia de início rápido Cloud Shell](quickstart.md)
