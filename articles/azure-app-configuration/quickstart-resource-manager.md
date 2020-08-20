---
title: Implementação de VM automatizada com quickstart de configuração de aplicações Azure
description: Este quickstart demonstra como usar o módulo Azure PowerShell e os modelos Azure Resource Manager para implementar uma loja de configuração de aplicações Azure. Em seguida, utilize os valores na loja para implantar um VM.
author: lisaguthrie
ms.author: lcozzens
ms.date: 08/11/2020
ms.topic: quickstart
ms.service: azure-app-configuration
ms.custom:
- mvc
- subject-armqs
ms.openlocfilehash: 7b7dd00d3495c24733ecdc213e0e25f8bc9640eb
ms.sourcegitcommit: d18a59b2efff67934650f6ad3a2e1fe9f8269f21
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/20/2020
ms.locfileid: "88661474"
---
# <a name="quickstart-automated-vm-deployment-with-app-configuration-and-resource-manager-template-arm-template"></a>Quickstart: Implantação automatizada de VM com configuração de aplicações e modelo de gestor de recursos (modelo ARM)

Aprenda a usar os modelos do Azure Resource Manager e o Azure PowerShell para implementar uma loja de configuração de aplicações Azure, como adicionar valores-chave na loja e como usar os valores-chave na loja para implantar um recurso Azure, como uma máquina virtual Azure neste exemplo.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Se o seu ambiente cumpre os pré-requisitos e se está familiarizado com a utilização de modelos ARM, selecione o botão **Implementar no Azure**. O modelo será aberto no portal do Azure.

[![Implementar no Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-app-configuration-store%2Fazuredeploy.json)

## <a name="prerequisites"></a>Pré-requisitos

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="review-the-templates"></a>Rever os modelos

Os modelos utilizados neste arranque rápido são de [Azure Quickstart Templates](https://azure.microsoft.com/resources/templates/). O [primeiro modelo](https://azure.microsoft.com/resources/templates/101-app-configuration-store/) cria uma loja de configuração de aplicativos:

:::code language="json" source="~/quickstart-templates/101-app-configuration-store/azuredeploy.json" range="1-37" highlight="27-35":::

No modelo, está definido um recurso do Azure:

- [Microsoft.AppConfiguration/configurationStores:](/azure/templates/microsoft.appconfiguration/2019-10-01/configurationstores)criar uma loja de configuração de aplicações.

O [segundo modelo](https://azure.microsoft.com/resources/templates/101-app-configuration/) cria uma máquina virtual utilizando os valores-chave na loja. Antes deste passo, tem de adicionar valores-chave utilizando o portal ou o Azure CLI.

:::code language="json" source="~/quickstart-templates/101-app-configuration/azuredeploy.json" range="1-217" highlight="77, 181,189":::

## <a name="deploy-the-templates"></a>Implementar os modelos

### <a name="create-an-app-configuration-store"></a>Criar uma loja de configuração de aplicativos

1. Selecione a imagem seguinte para iniciar sessão no Azure e abrir um modelo. O modelo cria uma loja de configuração de aplicativos.

    [![Implementar no Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-app-configuration-store%2Fazuredeploy.json)

1. Selecione ou introduza os seguintes valores.

    - **subscrição**: selecione a subscrição Azure utilizada para criar a loja de Configuração de Aplicações.
    - **Grupo de recursos**: selecione **Criar novo** para criar um novo grupo de recursos, a menos que pretenda utilizar um grupo de recursos existente.
    - **Região**: selecione uma localização para o grupo de recursos.  Por exemplo, **Leste dos EUA.**
    - **Nome da loja Config:** introduza um novo nome de loja de configuração de aplicações.
    - **Localização**: especifique a localização da loja de Configuração de Aplicações.  Utilize o valor predefinido.
    - **Nome Sku**: especifique o nome SKU da loja de configuração de aplicações. Utilize o valor predefinido.

1. Selecione **Rever + criar**.
1. Verifique se a página mostra **validação passada**e, em seguida, selecione **Criar**.

Tome nota do nome do grupo de recursos e do nome da loja de configuração de aplicações.  Precisa destes valores quando implanta a máquina virtual
### <a name="add-vm-configuration-key-values"></a>Adicionar valores-chave de configuração VM

Depois de ter criado uma loja de Configuração de Aplicações, pode utilizar o portal Azure ou o Azure CLI para adicionar valores-chave à loja.

1. Inscreva-se no [portal Azure](https://portal.azure.com)e, em seguida, navegue para a recém-criada loja de Configuração de Aplicações.
1. Selecione o explorador de **configuração** a partir do menu esquerdo.
1. Selecione **Criar** para adicionar os seguintes pares de valor-chave:

   |Chave|Valor|Etiqueta|
   |-|-|-|
   |windowsOsVersion|Centro de Dados 2019|modelo|
   |diskSizeGB|1023|modelo|

   Mantenha **o tipo de conteúdo** vazio.

Para utilizar o Azure CLI, consulte [Trabalhar com valores-chave numa loja de configuração de aplicações Azure](./scripts/cli-work-with-keys.md).

### <a name="deploy-vm-using-stored-key-values"></a>Implementar VM utilizando valores-chave armazenados

Agora que adicionou valores-chave à loja, está pronto para implementar um VM usando um modelo de Gestor de Recursos Azure. O modelo faz referência às **chaves windowsOsVersion** e **diskSizeGB** que criou.

> [!WARNING]
> Os modelos ARM não podem fazer referência às teclas numa loja de Configuração de Aplicações que tenha o Link Privado ativado.

1. Selecione a imagem seguinte para iniciar sessão no Azure e abrir um modelo. O modelo cria uma máquina virtual utilizando valores-chave armazenados na loja de configuração de aplicações.

    [![Implementar no Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-app-configuration%2Fazuredeploy.json)

1. Selecione ou introduza os seguintes valores.

    - **subscrição**: selecione a assinatura Azure utilizada para criar a máquina virtual.
    - **Grupo de recursos**: especifique o mesmo grupo de recursos que a loja de Configuração de Aplicações ou selecione **Criar novos** para criar um novo grupo de recursos.
    - **Região**: selecione uma localização para o grupo de recursos.  Por exemplo, **Leste dos EUA.**
    - **Localização**: especificar a localização da máquina virtual. utilizar o valor predefinido.
    - **Nome de utilizador Admin**: especifique um nome de utilizador do administrador para a máquina virtual.
    - **Admin Password**: especifique uma palavra-passe do administrador para a máquina virtual.
    - **Etiqueta de nome de**domínio : especifique um nome de domínio único.
    - **Nome da conta de**armazenamento : especifique um nome único para uma conta de armazenamento associada à máquina virtual.
    - **App Config Store Resource Group**: especifique o grupo de recursos que contém a sua loja de Configuração de Aplicações.
    - **App Config Store Name**: especifique o nome da sua loja de configuração de aplicações Azure.
    - **Chave VM Sku**: especifique **a janelaOsVersion**.  Este é o nome de valor chave que adicionou à loja.
    - **Tecla de tamanho do disco**: especifique o **diskSizeGB**. Este é o nome de valor chave que adicionou à loja.

1. Selecione **Rever + criar**.
1. Verifique se a página mostra **validação passada**e, em seguida, selecione **Criar**.

## <a name="review-deployed-resources"></a>Revisão dos recursos implantados

1. Inscreva-se no [portal Azure](https://portal.azure.com)e, em seguida, navegue para a máquina virtual recém-criada.
1. Selecione **visão geral** do menu esquerdo e verifique se o **SKU** é **2019-Datacenter**.
1. Selecione **Discos** do menu esquerdo e verifique se o tamanho do disco de dados é **2013**.

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não for necessário, elimine o grupo de recursos, a loja de Configuração de Aplicações, VM e todos os recursos relacionados. Se, no futuro, estiver a planear utilizar a loja de Configuração de Aplicações ou VM, pode não a excluir. Se não continuar a utilizar este trabalho, elimine todos os recursos criados por este arranque rápido executando o seguinte cmdlet:

```azurepowershell-interactive
Remove-AzResourceGroup `
  -Name $resourceGroup
```

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, implementou um VM utilizando um modelo de Gestor de Recursos Azure e valores-chave da Configuração da Aplicação Azure.

Para saber mais sobre a criação de outras aplicações com configuração de aplicações Azure, continue ao seguinte artigo:

> [!div class="nextstepaction"]
> [Quickstart: Criar uma aplicação Core ASP.NET com configuração de aplicações Azure](quickstart-aspnet-core-app.md)
