---
title: 'Quickstart: Criar uma zona de DNS Azure e gravar - Modelo de Gestor de Recursos Azure (modelo ARM)'
titleSuffix: Azure DNS
description: Saiba como criar uma zona DNS e o registar no DNS do Azure. Este é um quickstart passo a passo para criar e gerir a sua primeira zona de DNS e gravar usando o modelo Azure Resource Manager (modelo ARM).
services: dns
author: duongau
ms.service: dns
ms.topic: quickstart
ms.date: 09/8/2020
ms.author: duau
ms.custom: subject-armqs
ms.openlocfilehash: 24460167e2279e7d3001d0bc16d050beb5b55289
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/28/2020
ms.locfileid: "92791006"
---
# <a name="quickstart-create-an-azure-dns-zone-and-record-using-an-arm-template"></a>Quickstart: Crie uma zona de DNS Azure e grave usando um modelo ARM

Este quickstart descreve como usar um modelo de Gestor de Recursos Azure (Modelo ARM) para criar uma zona DE DNS com um `A` registo.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Se o seu ambiente cumpre os pré-requisitos e se está familiarizado com a utilização de modelos ARM, selecione o botão **Implementar no Azure** . O modelo será aberto no portal do Azure.

[![Implementar no Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-azure-dns-new-zone%2Fazuredeploy.json)

## <a name="prerequisites"></a>Pré-requisitos

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="review-the-template"></a>Rever o modelo

O modelo utilizado neste início rápido pertence aos [Modelos de Início Rápido do Azure](https://azure.microsoft.com/resources/templates/101-azure-dns-new-zone).

Neste arranque rápido, você vai criar uma zona de DNS única com um sufixo de `azurequickstart.org` . Um `A` registo que indique dois endereços IP também será colocado na zona.

:::code language="json" source="~/quickstart-templates/101-azure-dns-new-zone/azuredeploy.json":::

Dois recursos Azure foram definidos no modelo:

- [**Microsoft.Network/dnsZones**](/azure/templates/microsoft.network/dnsZones)
- [**Microsoft.Network/dnsZones/A**](/azure/templates/microsoft.network/dnsZones/A): Usado para criar um `A` registo na zona.

Para encontrar mais modelos relacionados com O Gestor de Tráfego Azure, consulte [Azure Quickstart Templates](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Network&pageNumber=1&sort=Popular).

## <a name="deploy-the-template"></a>Implementar o modelo

1. Selecione **Experimentá-lo** a partir do bloco de código que se segue para abrir a Azure Cloud Shell e, em seguida, siga as instruções para iniciar súm no Azure.

    ```azurepowershell-interactive
    $projectName = Read-Host -Prompt "Enter a project name that is used for generating resource names"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
    $templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-azure-dns-new-zone/azuredeploy.json"

    $resourceGroupName = "${projectName}rg"

    New-AzResourceGroup -Name $resourceGroupName -Location "$location"
    New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri

    Read-Host -Prompt "Press [ENTER] to continue ..."
    ```

    Espere até ver o pedido da consola.

1. Selecione **Copiar** do bloco de código anterior para copiar o script PowerShell.

1. Clique com o botão direito da placa de consola da concha e, em seguida, **selecione Pasta** .

1. Insira os valores.

    A implementação do modelo cria uma zona com um `A` registo apontando para dois endereços IP. O nome do grupo de recursos é o nome do projeto com **rg** anexado.

    Leva alguns segundos para implantar o modelo. Quando concluída, a saída é semelhante a:

    :::image type="content" source="./media/dns-getstarted-template/create-dns-zone-powershell-output.png" alt-text="Modelo de implementação do gestor de recursos da zona de Azure DNS":::

Azure PowerShell é usado para implementar o modelo. Além do Azure PowerShell, também pode utilizar o portal Azure CLI e a REST API. Para aprender outros métodos de implementação, consulte [os modelos de implementação](../azure-resource-manager/templates/deploy-portal.md).

## <a name="validate-the-deployment"></a>Validar a implementação

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).

1. Selecione **grupos** de recursos do painel esquerdo.

1. Selecione o grupo de recursos que criou na secção anterior. O nome do grupo de recursos predefinidos é o nome do projeto com **rg** anexado.

1. O grupo de recursos deve conter os seguintes recursos vistos aqui:

    :::image type="content" source="./media/dns-getstarted-template/resource-group-dns-zone.png" alt-text="Modelo de implementação do gestor de recursos da zona de Azure DNS":::

1. Selecione a zona DNS com o sufixo de `azurequickstart.org` verificar se a zona é criada corretamente com um registo que refere o valor de e `A` `1.2.3.4` `1.2.3.5` .

    :::image type="content" source="./media/dns-getstarted-template/dns-zone-overview.png" alt-text="Modelo de implementação do gestor de recursos da zona de Azure DNS":::

1. Copie um dos nomes do servidor de nomes do passo anterior.

1. Abra um pedido de comando e executar o seguinte comando:

   ```cmd
   nslookup www.<dns zone name> <name server name>
   ```

   Por exemplo:

   ```cmd
   nslookup www.2lwynbseszpam.azurequickstart.org ns1-09.azure-dns.com.
   ```

   Deve ver algo como a seguinte imagem:

    :::image type="content" source="./media/dns-getstarted-template/dns-zone-validation.png" alt-text="Modelo de implementação do gestor de recursos da zona de Azure DNS":::

O nome do hospedeiro `www.2lwynbseszpam.azurequickstart.org` resolve `1.2.3.4` `1.2.3.5` e, tal como o configuraste. Este resultado verifica que a resolução de nomes está a funcionar corretamente.

## <a name="clean-up-resources"></a>Limpar os recursos

Quando já não precisar dos recursos que criou com a zona DNS, elimine o grupo de recursos. Isto remove a zona de DNS e todos os recursos conexos.

Para eliminar o grupo de recursos, ligue para o `Remove-AzResourceGroup` cmdlet:

```azurepowershell-interactive
Remove-AzResourceGroup -Name <your resource group name>
```

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, criou um:

- Zona DNS
- `A` recorde

Agora que criou a sua primeira zona de DNS e gravou usando um modelo ARM, pode criar registos para uma aplicação web num domínio personalizado.

> [!div class="nextstepaction"]
> [Criar registos DNS para uma aplicação Web num domínio personalizado](./dns-web-sites-custom-domain.md)
