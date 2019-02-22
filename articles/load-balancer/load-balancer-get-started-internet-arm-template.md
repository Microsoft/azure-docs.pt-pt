---
title: Criar um balanceador de carga público – modelo do Azure
titlesuffix: Azure Load Balancer
description: Saiba como criar um balanceador de carga público no Resource Manager utilizando um modelo
services: load-balancer
documentationcenter: na
author: KumudD
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: kumud
ms.openlocfilehash: af72eb0214f46c1e001f9e93934d38cb934503e4
ms.sourcegitcommit: a8948ddcbaaa22bccbb6f187b20720eba7a17edc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/21/2019
ms.locfileid: "56592490"
---
# <a name="creating-a-public-load-balancer-using-a-template"></a>Criar um balanceador de carga público com um modelo

> [!div class="op_single_selector"]
> * [Portal](../load-balancer/load-balancer-get-started-internet-portal.md)
> * [PowerShell](../load-balancer/load-balancer-get-started-internet-arm-ps.md)
> * [CLI do Azure](../load-balancer/load-balancer-get-started-internet-arm-cli.md)
> * [Modelo](../load-balancer/load-balancer-get-started-internet-arm-template.md)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [load-balancer-get-started-internet-intro-include.md](../../includes/load-balancer-get-started-internet-intro-include.md)]

[!INCLUDE [load-balancer-get-started-internet-scenario-include.md](../../includes/load-balancer-get-started-internet-scenario-include.md)]

## <a name="deploy-the-template-by-using-click-to-deploy"></a>Implementar o modelo com a função clique para implementar

O modelo de exemplo disponível no repositório público utiliza um ficheiro de parâmetros com os valores predefinidos utilizados para gerar o cenário descrito acima. Para implementar este modelo com a função clique para implementar, siga [esta ligação](https://go.microsoft.com/fwlink/?LinkId=544801), clique em **Implementar no Azure**, substitua os valores de parâmetro predefinidos, se necessário, e siga as instruções apresentadas no portal.

## <a name="deploy-the-template-by-using-powershell"></a>Implementar o modelo com o PowerShell

Para implementar o modelo que transferiu com o PowerShell, siga os passos abaixo.

1. Se nunca tiver utilizado o Azure PowerShell, veja [How to Install and Configure Azure PowerShell (Como instalar e configurar o Azure PowerShell)](/powershell/azure/overview) e siga as instruções até ao fim para iniciar sessão no Azure e selecionar a sua subscrição.
2. Executar o **New-AzResourceGroupDeployment** cmdlet para criar um grupo de recursos com o modelo.

    ```powershell
    New-AzResourceGroupDeployment -Name TestRG -Location uswest `
        -TemplateFile 'https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-2-vms-loadbalancer-lbrules/azuredeploy.json' `
        -TemplateParameterFile 'https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-2-vms-loadbalancer-lbrules/azuredeploy.parameters.json'
    ```

## <a name="deploy-the-template-by-using-the-azure-cli"></a>Implementar o modelo com a CLI do Azure

Para implementar o modelo com a CLI do Azure, siga os passos abaixo.

1. Se nunca tiver utilizado a CLI do Azure, veja [Install and Configure the Azure CLI (Instalar e Configurar a CLI do Azure)](../cli-install-nodejs.md) e siga as instruções até ao ponto onde poderá selecionar a sua conta e subscrição do Azure.
2. Execute o comando **azure config mode** para mudar para o modo Resource Manager, como mostrado abaixo.

    ```azurecli
    azure config mode arm
    ```

    O resultado esperado para o comando acima é o seguinte:

        info:    New mode is arm

3. A partir do browser, navegue para o [Modelo de Início Rápido](https://github.com/Azure/azure-quickstart-templates/tree/master/201-2-vms-loadbalancer-lbrules), copie o conteúdo do ficheiro json e cole num novo ficheiro no seu computador. Para este cenário, teria de copiar os valores abaixo para um ficheiro denominado **c:\lb\azuredeploy.parameters.json**.
4. Execute o cmdlet **azure group deployment create** para implementar o novo balanceador de carga com o modelo e os ficheiros de parâmetros que transferiu e alterou acima. A lista apresentada depois do resultado explica os parâmetros utilizados.

    ```azurecli
    azure group create --name TestRG --location westus --template-file 'https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-2-vms-loadbalancer-lbrules/azuredeploy.json' --parameters-file 'c:\lb\azuredeploy.parameters.json'
    ```

## <a name="next-steps"></a>Passos Seguintes

[Começar a configurar um balanceador de carga interno](load-balancer-get-started-ilb-arm-ps.md)

[Configurar um modo de distribuição de balanceador de carga](load-balancer-distribution-mode.md)

[Configurar definições de tempo limite TCP inativo para o balanceador de carga](load-balancer-tcp-idle-timeout.md)

Para a sintaxe JSON e propriedades de um balanceador de carga num modelo, consulte [Network/loadbalancers](/azure/templates/microsoft.network/loadbalancers).
