---
title: Tutorial - Configure Azure Cloud Shell para Terraform
description: Neste tutorial, você usa Terraform com Casca de Nuvem Azure para simplificar a autenticação e configuração do modelo.
keywords: casca de nuvem terraforme azure devops
ms.topic: tutorial
ms.date: 03/09/2020
ms.openlocfilehash: 3a9db1143ba07b549a271d53d610e0a4853467c6
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "78945334"
---
# <a name="tutorial-configure-azure-cloud-shell-for-terraform"></a>Tutorial: Configure Azure Cloud Shell para Terraform

A Terraform funciona bem a partir de uma linha de comando Bash em macOS, Windows ou Linux. Executar as suas configurações Terraform na experiência Bash da [Azure Cloud Shell](/azure/cloud-shell/overview) tem algumas vantagens únicas. Este tutorial mostra como escrever scripts Terraform que se desdobram para Azure usando Cloud Shell.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="automatic-credential-configuration"></a>Configuração automática de credenciais

O Terraform está instalado e imediatamente disponível no Cloud Shell. Os scripts terraform autenticam com o Azure quando acedeu à Cloud Shell para gerir a infraestrutura sem qualquer configuração adicional. A autenticação automática ignora dois processos manuais:
- Criação de um diretor ativo principal
- Configurar as variáveis do fornecedor Azure Terraform


## <a name="use-modules-and-providers"></a>Utilizar módulos e fornecedores

Os módulos Azure Terraform requerem credenciais para aceder e modificar os recursos azure. Para utilizar módulos Terraform em Cloud Shell, adicione o seguinte código:


```hcl
# Configure the Microsoft Azure Provider
provider "azurerm" {
    # The "feature" block is required for AzureRM provider 2.x. 
    # If you are using version 1.x, the "features" block is not allowed.
    version = "~>2.0"
    features {}
}
```

Cloud Shell passa valores `azurerm` necessários para o fornecedor através `terraform` de variáveis ambientais quando você usa qualquer um dos comandos CLI.

## <a name="other-cloud-shell-developer-tools"></a>Outras ferramentas de programação do Cloud Shell

Os ficheiros e estados de shell são mantidos no Armazenamento do Azure entre sessões do Cloud Shell. Utilize o [Azure Storage Explorer](/azure/vs-azure-tools-storage-manage-with-storage-explorer) para copiar e carregar ficheiros para cloud shell a partir do seu computador local.

O Azure CLI está disponível na Cloud Shell e é uma `terraform apply` excelente `terraform destroy` ferramenta para testar configurações e verificar o seu trabalho depois ou acabamentos.


## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Criar um pequeno cluster VM utilizando o Registo de Módulos](terraform-create-vm-cluster-module.md)