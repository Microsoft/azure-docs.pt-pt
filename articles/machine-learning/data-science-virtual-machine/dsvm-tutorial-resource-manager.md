---
title: 'Quickstart: Criar um VM de Ciência de Dados - Modelo de Gestor de Recursos'
titleSuffix: Azure Data Science Virtual Machine
description: Neste arranque rápido, você usa um modelo de Gestor de Recursos Azure para implementar rapidamente uma Máquina Virtual de Ciência de Dados
services: machine-learning
author: lobrien
ms.author: laobri
ms.custom: subject-armqs
ms.date: 06/10/2020
ms.service: machine-learning
ms.subservice: data-science-vm
ms.topic: quickstart
ms.openlocfilehash: 7e3e45d96839be06deec238deccdc9873492d68f
ms.sourcegitcommit: 5a8c8ac84c36859611158892422fc66395f808dc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/10/2020
ms.locfileid: "84660243"
---
# <a name="quickstart-create-an-ubuntu-data-science-virtual-machine-using-a-resource-manager-template"></a>Quickstart: Criar uma máquina virtual de ciência de dados Ubuntu usando um modelo de Gestor de Recursos
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

Este quickstart irá mostrar-lhe como criar uma Máquina Virtual Ubuntu 18.04 data science usando um modelo de Gestor de Recursos Azure. Data Science Virtual Machines são máquinas virtuais baseadas em nuvem pré-carregadas com um conjunto de quadros e ferramentas de aprendizagem automática de dados e de aprendizagem automática. Quando implantados em recursos computacionais movidos a GPU, todas as ferramentas e bibliotecas são configuradas para utilizar a GPU. 

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://aka.ms/AMLFree) antes de começar

* Para utilizar os comandos CLI neste documento a partir do seu **ambiente local,** precisa do [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)

## <a name="create-a-workspace"></a>Criar uma área de trabalho

### <a name="review-the-template"></a>Rever o modelo

O modelo utilizado neste arranque rápido é de [Azure Quickstart Templates](https://azure.microsoft.com/resources/templates/101-vm-ubuntu-DSVM-GPU-or-CPU/). O modelo completo para este artigo é muito longo para mostrar aqui. Para ver o modelo completo, consulte [azuredeploy.js.](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-ubuntu-DSVM-GPU-or-CPU/azuredeploy.json) A parte que define as especificidades do DSVM é mostrada aqui:

:::code language="json" source="~/quickstart-templates/101-vm-ubuntu-DSVM-GPU-or-CPU/azuredeploy.json" range="235-276":::

Os seguintes recursos são definidos no modelo:

* [Microsoft.Compute/virtualMachines](/azure/templates/microsoft.compute/virtualmachines): Crie uma máquina virtual baseada em nuvem. Neste modelo, a máquina virtual é configurada como uma Máquina Virtual de Ciência de Dados que executa Ubuntu 18.04.

### <a name="deploy-the-template"></a>Implementar o modelo 

Para utilizar o modelo a partir do CLI Azure, faça login e escolha a sua subscrição (Ver [Iniciar sessão com Azure CLI).](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest) Em seguida, execute:

```azurecli-interactive
read -p "Enter the name of the resource group to create:" resourceGroupName &&
read -p "Enter the Azure location (e.g., centralus):" location &&
read -p "Enter the authentication type (must be 'password' or 'sshPublicKey') :" authenticationType &&
read -p "Enter the login name for the administrator account (may not be 'admin'):" adminUsername &&
read -p "Enter administrator account secure string (value of password or ssh public key):" adminPasswordOrKey &&
templateUri="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-ubuntu-DSVM-GPU-or-CPU/azuredeploy.json" &&
az group create --name $resourceGroupName --location "$location" &&
az deployment group create --resource-group $resourceGroupName --template-uri $templateUri --parameters adminUsername=$adminUsername authenticationType=$authenticationType adminPasswordOrKey=$adminPasswordOrKey && 
echo "Press [ENTER] to continue ..." &&
read
```

Quando executar o comando acima, insira:

1. O nome do grupo de recursos que gostaria de criar para conter o DSVM e os recursos associados. 
1. A localização Azure em que deseja fazer a implantação
1. O tipo de autenticação que gostaria de usar (introduza a cadeia `password` `sshPublicKey` ou)
1. O nome de login da conta do administrador (este valor pode não `admin` ser)
1. O valor da senha ou chave pública ssh para a conta

## <a name="review-deployed-resources"></a>Revisão dos recursos implantados

Para ver a sua Máquina Virtual de Ciência de Dados:

1. Ir para https://portal.azure.com 
1. Iniciar sessão 
1. Escolha o grupo de recursos que acabou de criar

Verá as informações do Grupo de Recursos: 

:::image type="content" source="media/dsvm-tutorial-resource-manager/resource-group-home.png" alt-text="Screenshot de um Grupo de Recursos Básicos contendo um DSVM":::

Clique no recurso Máquina Virtual para ir à sua página de informações. Aqui pode encontrar informações sobre o VM, incluindo detalhes de conexão. 

## <a name="clean-up-resources"></a>Limpar recursos

Se não quiser utilizar esta máquina virtual, elimine-a. Uma vez que o DSVM está associado a outros recursos, como uma conta de armazenamento, provavelmente irá querer eliminar todo o grupo de recursos que criou. Pode eliminar o grupo de recursos utilizando o portal clicando no botão "Eliminar" e confirmando. Ou, pode eliminar o grupo de recursos do CLI com: 

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

## <a name="next-steps"></a>Próximos passos

Neste arranque rápido, criou uma Máquina Virtual de Ciência de Dados a partir de um modelo de Gestor de Recursos Azure. 

> [!div class="nextstepaction"]
> [Programas de amostra & adiscrição de ML](dsvm-samples-and-walkthroughs.md)
