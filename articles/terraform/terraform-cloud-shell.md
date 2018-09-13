---
title: Utilizar o Terraform com o Azure Cloud Shell
description: Utilize o Terraform com o Azure Cloud Shell para simplificar a autenticação e a configuração de modelos.
services: terraform
ms.service: terraform
keywords: terraform, programadores, conjunto de dimensionamento, máquina virtual, rede, armazenamento, módulos
author: tomarcher
manager: jeconnoc
ms.author: tarcher
ms.topic: tutorial
ms.date: 10/19/2017
ms.openlocfilehash: 107a6dd82465ce1455a3c2922c8f9cba6b73dd64
ms.sourcegitcommit: 31241b7ef35c37749b4261644adf1f5a029b2b8e
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/04/2018
ms.locfileid: "43667967"
---
# <a name="terraform-cloud-shell-development"></a>Desenvolvimento do Terraform com o Cloud Shell 

O Terraform funciona muito bem a partir de uma linha de comandos do Bash, como o Terminal no macOS ou Bash no Windows ou Linux. Executar as configurações do Terraform na experiência Bash do [Azure Cloud Shell](/azure/cloud-shell/overview) tem algumas vantagens exclusivas para acelerar o ciclo de desenvolvimento.

Este artigo de conceitos abrange as funcionalidades do Cloud Shell que o ajudam a escrever scripts do Terraform que são implementados no Azure.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="automatic-credential-configuration"></a>Configuração automática de credenciais

O Terraform está instalado e imediatamente disponível no Cloud Shell. Os scripts do Terraform são autenticados com o Azure quando existe sessão iniciada no Cloud Shell para gerir a infraestrutura sem qualquer configuração adicional. A autenticação automática ignora a necessidade de criar manualmente um principal de serviço do Active Directory e configurar as variáveis de fornecedor do Terraform do Azure.


## <a name="using-modules-and-providers"></a>Utilizar Módulos e Fornecedores

Os módulos do Terraform do Azure necessitam de credenciais para aceder e efetuar alterações aos recursos na sua subscrição do Azure. Ao trabalhar no Cloud Shell, adicione o seguinte código aos seus scripts para utilizar os módulos do Terraform do Azure no Cloud Shell:

```tf
# Configure the Microsoft Azure Provider
provider "azurerm" {
}
```

O Cloud Shell passa os valores necessários para o fornecedor do `azurerm` através de variáveis de ambiente ao utilizar qualquer um dos comandos da CLI do `terraform`.

## <a name="other-cloud-shell-developer-tools"></a>Outras ferramentas de programação do Cloud Shell

Os ficheiros e estados de shell são mantidos no Armazenamento do Azure entre sessões do Cloud Shell. Utilize o [Explorador de Armazenamento do Azure](/azure/vs-azure-tools-storage-manage-with-storage-explorer) para copiar e carregar ficheiros para o Cloud Shell a partir do computador local.

A CLI 2.0 do Azure está disponível no Cloud Shell e é uma ótima ferramenta para testar configurações e verificar o seu trabalho depois de concluído um `terraform apply` ou `terraform destroy`.


## <a name="next-steps"></a>Passos seguintes

[Create a small VM cluster using the Module Registry](terraform-create-vm-cluster-module.md) (Criar um pequeno cluster de VM com o Registo do Módulo)
[Create a small VM cluster using custom HCL](terraform-create-vm-cluster-with-infrastructure.md) (Criar um pequeno cluster de VM com HCL personalizado)
