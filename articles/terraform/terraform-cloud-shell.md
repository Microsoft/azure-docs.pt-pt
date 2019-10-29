---
title: Tutorial – configurar Azure Cloud Shell para Terraform
description: Utilize o Terraform com o Azure Cloud Shell para simplificar a autenticação e a configuração de modelos.
ms.service: terraform
author: tomarchermsft
ms.author: tarcher
ms.topic: tutorial
ms.date: 10/26/2019
ms.openlocfilehash: 693ed462fb1ba3dfed079e8ae97152732c771253
ms.sourcegitcommit: b1c94635078a53eb558d0eb276a5faca1020f835
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/27/2019
ms.locfileid: "72969583"
---
# <a name="tutorial-configure-azure-cloud-shell-for-terraform"></a>Tutorial: configurar Azure Cloud Shell para Terraform

O Terraform funciona bem de uma linha de comando do bash no macOS, Windows ou Linux. Executar as configurações do Terraform na experiência do bash do [Azure cloud Shell](/azure/cloud-shell/overview) tem algumas vantagens exclusivas. Este tutorial mostra como gravar scripts Terraform que implantam no Azure usando Cloud Shell.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="automatic-credential-configuration"></a>Configuração automática de credenciais

O Terraform está instalado e imediatamente disponível no Cloud Shell. Os scripts do Terraform são autenticados com o Azure quando existe sessão iniciada no Cloud Shell para gerir a infraestrutura sem qualquer configuração adicional. A autenticação automática ignora dois processos manuais:
- Criando uma entidade de serviço Active Directory.
- Configurando as variáveis do provedor Terraform do Azure.


## <a name="using-modules-and-providers"></a>Utilizar Módulos e Fornecedores

Os módulos Terraform do Azure exigem credenciais para acessar e modificar os recursos do Azure. Para usar os módulos Terraform no Cloud Shell, adicione o seguinte código:


```hcl
# Configure the Microsoft Azure Provider
provider "azurerm" {
}
```

O Cloud Shell passa os valores necessários para o fornecedor do `azurerm` através de variáveis de ambiente ao utilizar qualquer um dos comandos da CLI do `terraform`.

## <a name="other-cloud-shell-developer-tools"></a>Outras ferramentas de programação do Cloud Shell

Os ficheiros e estados de shell são mantidos no Armazenamento do Azure entre sessões do Cloud Shell. Utilize o [Explorador de Armazenamento do Azure](/azure/vs-azure-tools-storage-manage-with-storage-explorer) para copiar e carregar ficheiros para o Cloud Shell a partir do computador local.

A CLI do Azure está disponível no Cloud Shell e é uma ótima ferramenta para testar configurações e verificar o seu trabalho depois de concluído um `terraform apply` ou `terraform destroy`.


## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Criar um cluster de VM pequeno usando o registro de módulo](terraform-create-vm-cluster-module.md)