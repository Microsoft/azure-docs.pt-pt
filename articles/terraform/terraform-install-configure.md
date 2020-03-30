---
title: Quickstart - Instale e configure terraforma para fornecer recursos Azure
description: Neste quicstart, você instala e configura terraforma para criar recursos Azure
keywords: azure devops terraforma instalar configuração
ms.topic: quickstart
ms.date: 03/09/2020
ms.openlocfilehash: 82635f59ec8165add2046a230a040b06f89d9898
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2020
ms.locfileid: "78943508"
---
# <a name="quickstart-install-and-configure-terraform-to-provision-azure-resources"></a>Quickstart: Instale e configure a Terraform para fornecer recursos Azure
 
A Terraform fornece uma maneira fácil de definir, pré-visualizar e implementar infraestruturas em nuvem usando uma linguagem simples de [templating.](https://www.terraform.io/docs/configuration/syntax.html) Este artigo descreve as medidas necessárias para utilizar a Terraform para fornecer recursos em Azure.

Para saber mais sobre como usar terraforma com Azure, visite o [Centro Terraform.](/azure/terraform)
> [!NOTE]
> Para um suporte específico terraforme, contacte a Terraform diretamente utilizando um dos seus canais comunitários:
>
>    * A [secção Terraforme](https://discuss.hashicorp.com/c/terraform-core) do portal comunitário contém perguntas, casos de uso e padrões úteis.
>
>    * Para questões relacionadas com o provedor, visite a secção [fornecedores terraforme do](https://discuss.hashicorp.com/c/terraform-providers) portal comunitário.



[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

A terraforme é instalada por padrão na [Cloud Shell](/azure/terraform/terraform-cloud-shell). Se optar por instalar a Terraform localmente, complete o próximo passo, caso contrário continue a configurar o [acesso terraforme ao Azure](#set-up-terraform-access-to-azure).

## <a name="install-terraform"></a>Instalar terraforma

Para instalar a Terraform, [descarregue](https://www.terraform.io/downloads.html) o pacote apropriado para o seu sistema operativo num diretório de instalação separado. O download contém um único ficheiro executável, para o qual também deve definir um caminho global. Para obter instruções sobre como definir o caminho em Linux e Mac, vá a [esta página web](https://stackoverflow.com/questions/14637979/how-to-permanently-set-path-on-linux). Para obter instruções sobre como definir o caminho no Windows, vá a [esta página web](https://stackoverflow.com/questions/1618280/where-can-i-set-path-to-make-exe-on-windows).

Verifique a configuração `terraform` do seu caminho com o comando. É apresentada uma lista das opções disponíveis da Terraform, como na seguinte saída de exemplo:

```console
azureuser@Azure:~$ terraform
Usage: terraform [--version] [--help] <command> [args]
```

## <a name="set-up-terraform-access-to-azure"></a>Configurar o acesso terraforme ao Azure

Para permitir à Terraform fornecer recursos para o Azure, crie um diretor de [serviço saqueado azure](/cli/azure/create-an-azure-service-principal-azure-cli). O principal de serviço concede os seus scripts Terraform para fornecer recursos na sua subscrição Azure.

Se tiver várias subscrições do Azure, primeiro questione a sua conta com a lista de [conta Az](/cli/azure/account#az-account-list) para obter uma lista de valores de ID de subscrição e de identificação do inquilino:

```azurecli-interactive
az account list --query "[].{name:name, subscriptionId:id, tenantId:tenantId}"
```

Para utilizar uma subscrição selecionada, detete a subscrição para esta sessão com um conjunto de [conta az](/cli/azure/account#az-account-set). Desdefinir a `SUBSCRIPTION_ID` variável ambiental `id` para manter o valor do campo devolvido a partir da subscrição que pretende utilizar:

```azurecli-interactive
az account set --subscription="${SUBSCRIPTION_ID}"
```

Agora pode criar um diretor de serviço para uso com terraforma. Utilize [az ad sp create-for-rbac](/cli/azure/ad/sp#az-ad-sp-create-for-rbac)e defino o *âmbito* para a sua subscrição da seguinte forma:

```azurecli-interactive
az ad sp create-for-rbac --role="Contributor" --scopes="/subscriptions/${SUBSCRIPTION_ID}"
```

O `appId` `password`seu, `sp_name` `tenant` e são devolvidos. Tome nota `appId` do `password`e .

## <a name="configure-terraform-environment-variables"></a>Configure variáveis ambientais terraformes

Para configurar a Terraform para utilizar o seu diretor de serviço Azure AD, detete as seguintes variáveis ambientais, que são então utilizadas pelos [módulos Terraform Azure](https://registry.terraform.io/modules/Azure). Você também pode definir o ambiente se trabalhar com uma nuvem Azure que não seja o público Azure.

- `ARM_SUBSCRIPTION_ID`
- `ARM_CLIENT_ID`
- `ARM_CLIENT_SECRET`
- `ARM_TENANT_ID`
- `ARM_ENVIRONMENT`

Pode utilizar o seguinte script de concha de amostra para definir essas variáveis:

```bash
#!/bin/sh
echo "Setting environment variables for Terraform"
export ARM_SUBSCRIPTION_ID=your_subscription_id
export ARM_CLIENT_ID=your_appId
export ARM_CLIENT_SECRET=your_password
export ARM_TENANT_ID=your_tenant_id

# Not needed for public, required for usgovernment, german, china
export ARM_ENVIRONMENT=public
```

## <a name="run-a-sample-script"></a>Executar um script de amostra

Crie `test.tf` um ficheiro num diretório vazio e faça cola no seguinte script.

```hcl
provider "azurerm" {
  # The "feature" block is required for AzureRM provider 2.x. 
  # If you are using version 1.x, the "features" block is not allowed.
  version = "~>2.0"
  features {}
}
resource "azurerm_resource_group" "rg" {
        name = "testResourceGroup"
        location = "westus"
}
```

Guarde o ficheiro e, em seguida, inicialize a implantação terraforme. Este passo descarrega os módulos Azure necessários para criar um grupo de recursos Azure.

```bash
terraform init
```

O resultado é semelhante ao seguinte exemplo:

```console
* provider.azurerm: version = "~> 0.3"

Terraform has been successfully initialized!
```

Pode visualizar as ações a completar pelo `terraform plan`script Terraform com . Quando estiver pronto para criar o grupo de recursos, aplique o seu plano Terraform da seguinte forma:

```bash
terraform apply
```

O resultado é semelhante ao seguinte exemplo:

```console
An execution plan has been generated and is shown below.
Resource actions are indicated with the following symbols:
  + create

Terraform will perform the following actions:

  + azurerm_resource_group.rg
      id:       <computed>
      location: "westus"
      name:     "testResourceGroup"
      tags.%:   <computed>

azurerm_resource_group.rg: Creating...
  location: "" => "westus"
  name:     "" => "testResourceGroup"
  tags.%:   "" => "<computed>"
azurerm_resource_group.rg: Creation complete after 1s
```

## <a name="next-steps"></a>Passos seguintes

Neste artigo, instalou terrafora ou usou a Cloud Shell para configurar credenciais Azure e começar a criar recursos na sua subscrição Azure. Para criar uma implantação terraforme mais completa em Azure, consulte o seguinte artigo:

> [!div class="nextstepaction"]
> [Criar um VM Azure com Terraform](terraform-create-complete-vm.md)