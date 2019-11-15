---
title: Tutorial – configurar Azure Cloud Shell para Terraform
description: Utilize o Terraform com o Azure Cloud Shell para simplificar a autenticação e a configuração de modelos.
ms.service: terraform
author: tomarchermsft
ms.author: tarcher
ms.topic: tutorial
ms.date: 10/26/2019
ms.openlocfilehash: 1259d5004bd547e33f65571333b6d0721d1253c0
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/14/2019
ms.locfileid: "74078739"
---
# <a name="tutorial-configure-azure-cloud-shell-for-terraform"></a>Tutorial: configurar Azure Cloud Shell para Terraform

O Terraform funciona bem de uma linha de comando do bash no macOS, Windows ou Linux. Executar as configurações do Terraform na experiência de bash do [Azure cloud Shell](/azure/cloud-shell/overview) tem algumas vantagens exclusivas. Este tutorial mostra como gravar scripts Terraform que implantam no Azure usando Cloud Shell.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="automatic-credential-configuration"></a>Configuração automática de credenciais

O Terraform está instalado e imediatamente disponível no Cloud Shell. Os scripts do Terraform são autenticados com o Azure quando conectados ao Cloud Shell para gerenciar a infraestrutura sem nenhuma configuração adicional. A autenticação automática ignora dois processos manuais:
- Criando uma entidade de serviço Active Directory
- Configurando as variáveis do provedor Terraform do Azure


## <a name="use-modules-and-providers"></a>Usar módulos e provedores

Os módulos Terraform do Azure exigem credenciais para acessar e modificar os recursos do Azure. Para usar os módulos Terraform no Cloud Shell, adicione o seguinte código:


```hcl
# Configure the Microsoft Azure Provider
provider "azurerm" {
}
```

Cloud Shell passa os valores necessários para o provedor de `azurerm` por meio de variáveis de ambiente quando você usa qualquer um dos comandos da CLI do `terraform`.

## <a name="other-cloud-shell-developer-tools"></a>Outras ferramentas de programação do Cloud Shell

Os ficheiros e estados de shell são mantidos no Armazenamento do Azure entre sessões do Cloud Shell. Use [Gerenciador de armazenamento do Azure](/azure/vs-azure-tools-storage-manage-with-storage-explorer) para copiar e carregar arquivos para Cloud shell do seu computador local.

O CLI do Azure está disponível no Cloud Shell e é uma excelente ferramenta para testar as configurações e verificar seu trabalho após a conclusão `terraform apply` ou `terraform destroy`.


## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Criar um cluster de VM pequeno usando o registro de módulo](terraform-create-vm-cluster-module.md)
