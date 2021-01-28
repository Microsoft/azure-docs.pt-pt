---
title: Quickstart - Criar um serviço de fornecimento de dispositivos de hub IoT (DPS) azure usando o modelo de Gestor de Recursos Azure (modelo ARM)
description: Azure quickstart - Aprenda a criar um Serviço de Provisionamento de Dispositivos Azure IoT Hub (DPS) utilizando o modelo Azure Resource Manager (modelo ARM).
author: wesmc7777
ms.author: wesmc
ms.date: 01/27/2021
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
ms.custom: mvc, subject-armqs, devx-track-azurecli
ms.openlocfilehash: cc55d1569c85dd0873d0132833acc99bd5d06136
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/28/2021
ms.locfileid: "98928945"
---
# <a name="quickstart-set-up-the-iot-hub-device-provisioning-service-dps-with-an-arm-template"></a>Quickstart: Configurar o Serviço de Provisionamento de Dispositivos IoT Hub (DPS) com um modelo ARM

Pode utilizar um modelo [de Gestor de Recursos Azure](../azure-resource-manager/management/overview.md) (modelo ARM) para configurar programaticamente os recursos de nuvem Azure necessários para o fornecimento dos seus dispositivos. Estes passos mostram como criar um hub IoT e um novo serviço de provisionamento de dispositivos IoT Hub com um modelo ARM. O Iot Hub também está ligado ao recurso DPS usando o modelo. Esta ligação permite ao recurso DPS atribuir dispositivos ao centro com base nas políticas de atribuição que configura.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Este quickstart utiliza o [portal Azure](../azure-resource-manager/templates/deploy-portal.md), e o [CLI Azure](../azure-resource-manager/templates/deploy-cli.md) para executar os passos programáticos necessários para criar um grupo de recursos e implementar o modelo, mas você pode facilmente usar o [PowerShell](../azure-resource-manager/templates/deploy-powershell.md), .NET, Ruby, ou outras linguagens de programação para executar estes passos e implementar o seu modelo. 

Se o seu ambiente satisfaça os pré-requisitos e já estiver familiarizado com a utilização de modelos ARM, selecionar o botão **Implementar para Azul** abaixo abrirá o modelo para implantação no portal Azure.

[![Implementar para Azure em visão geral](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-iothub-device-provisioning%2fazuredeploy.json)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]


## <a name="review-the-template"></a>Rever o modelo

O modelo utilizado neste início rápido pertence aos [Modelos de Início Rápido do Azure](https://azure.microsoft.com/resources/templates/101-iothub-device-provisioning/).

> [!NOTE]
> Atualmente não existe suporte ao modelo ARM para criar inscrições com novos recursos DPS. Trata-se de um pedido comum e compreendido que está a ser considerado para implementação.

:::code language="json" source="~/quickstart-templates/101-iothub-device-provisioning/azuredeploy.json":::

Dois recursos Azure são definidos no modelo acima:

* [**Microsoft.Devices/iothubs**](/azure/templates/microsoft.devices/iothubs): Cria um novo Azure IoT Hub.
* [**Microsoft.Devices/provisioningservices**](/azure/templates/microsoft.devices/provisioningservices): Cria um novo Serviço de Provisionamento de Dispositivos Azure IoT Hub com o novo Hub IoT já ligado ao mesmo.


## <a name="deploy-the-template"></a>Implementar o modelo

#### <a name="deploy-with-the-portal"></a>Implementar com o Portal

1. Selecione a seguinte imagem para iniciar seduca e abra o modelo para implementação. O modelo cria um novo recurso Iot Hub e DPS. O hub será ligado no recurso DPS.

    [![Implementar para Azure em Passos do Portal](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-iothub-device-provisioning%2fazuredeploy.json)

2. Selecione ou introduza os seguintes valores e clique em **Rever + Criar.**

    ![Parâmetros de implantação do modelo ARM no portal](./media/quick-setup-auto-provision-rm/arm-template-deployment-parameters-portal.png)    

    A menos que seja especificado abaixo, use o valor padrão para criar o iot Hub e o recurso DPS.

    | Campo | Descrição |
    | :---- | :---------- |
    | **Subscrição** | Selecione a sua subscrição do Azure. |
    | **Grupo de recursos** | Clique em **Criar novo**, e insira um nome único para o grupo de recursos e, em seguida, clique em **OK**. |
    | **Região** | Selecione uma região para os seus recursos. Por exemplo, **Leste dos EUA.** |
    | **Nome do hub iot** | Insira um nome para o IoT Hub que deve ser globalmente único dentro do espaço *de nomes .azure-devices.net.* Precisa do nome do hub na secção seguinte quando validar a implementação. |
    | **Nome do serviço de prestação** | Introduza um nome para o novo recurso do Serviço de Provisionamento de Dispositivos (DPS). O nome deve ser globalmente único dentro do espaço de nome *.azure-devices-provisioning.net.* Precisa do nome DPS na secção seguinte quando validar a implementação. |
    
3. No próximo ecrã, leia os termos. Se concordar com todos os termos, clique em **Criar**. 

    A implantação levará alguns momentos para ser concluída. 

    Além do portal Azure, também pode utilizar o Azure PowerShell, Azure CLI e REST API. Para aprender outros métodos de implementação, consulte [os modelos de implementação](../azure-resource-manager/templates/deploy-powershell.md).


#### <a name="deploy-with-the-azure-cli"></a>Implementar com o Azure CLI

A utilização do CLI Azure requer a versão 2.6 ou posterior. Se estiver a executar o Azure CLI localmente, verifique a sua versão executando: `az --version`

Inicie sessão na sua conta do Azure e selecione a sua subscrição.

1. Se estiver a executar o Azure CLI localmente em vez de o executar no portal, terá de fazer login. Para iniciar sessão na solicitação de comando, execute o [comando de login:](/cli/azure/get-started-with-az-cli2)
    
    ```azurecli
    az login
    ```

    Siga as instruções para se autenticar com o código e inicie sessão na sua conta do Azure através de um browser.

2. Se tiver várias subscrições do Azure, iniciar sessão no Azure dá-lhe acesso a todas as contas do Azure associadas às suas credenciais. Utilize o comando [ para listar as contas do Azure](/cli/azure/account) disponíveis e que pode utilizar:
    
    ```azurecli
    az account list -o table
    ```

    Utilize o seguinte comando para selecionar a subscrição que pretende utilizar para executar os comandos para criar o seu hub IoT e recursos DPS. Pode utilizar o nome ou o ID da subscrição da saída do comando anterior:

    ```azurecli
    az account set --subscription {your subscription name or id}
    ```

3. Copie e cole os seguintes comandos na sua solicitação CLI. Em seguida, execute os comandos pressionando **ENTER**.
   
    > [!TIP]
    > Os comandos solicitarão a localização de um grupo de recursos. Pode ver uma lista de locais disponíveis executando primeiro o comando:
    >
    > `az account list-locations -o table`
    >
    >
    
    ```azurecli-interactive
    read -p "Enter a project name that is used for generating resource names:" projectName &&
    read -p "Enter the location (i.e. centralus):" location &&
    templateUri="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-iothub-device-provisioning/azuredeploy.json" &&
    resourceGroupName="${projectName}rg" &&
    az group create --name $resourceGroupName --location "$location" &&
    az deployment group create --resource-group $resourceGroupName --template-uri  $templateUri &&
    echo "Press [ENTER] to continue ..." &&
    read
    ```

4. Os comandos irão pedir-lhe as seguintes informações. Forneça cada valor e prima **ENTER**.

    | Parâmetro | Descrição |
    | :-------- | :---------- |
    | **Nome do projeto** | O valor deste parâmetro será usado para criar um grupo de recursos para deter todos os recursos. A cadeia `rg` será adicionada ao fim do valor para o nome do seu grupo de recursos. |
    | **localização** | Este valor é a região onde todos os recursos irão residir. |
    | **iotHubName** | Insira um nome para o IoT Hub que deve ser globalmente único dentro do espaço *de nomes .azure-devices.net.* Precisa do nome do hub na secção seguinte quando validar a implementação. |
    | **provisionamentoServiamentoName** | Introduza um nome para o novo recurso do Serviço de Provisionamento de Dispositivos (DPS). O nome deve ser globalmente único dentro do espaço de nome *.azure-devices-provisioning.net.* Precisa do nome DPS na secção seguinte quando validar a implementação. |

    O AzureCLI é utilizado para implantar o modelo. Além do Azure CLI, também pode utilizar o Azure PowerShell, o portal Azure e a API REST. Para aprender outros métodos de implementação, consulte [os modelos de implementação](../azure-resource-manager/templates/deploy-powershell.md).


## <a name="review-deployed-resources"></a>Revisão dos recursos implantados

1. Para verificar a implantação, executar o seguinte [comando para listar recursos](/cli/azure/resource?view=azure-cli-latest#az-resource-list&preserve-view=true) e procurar o novo serviço de fornecimento e hub IoT na saída:

    ```azurecli
     az resource list -g "${projectName}rg"
    ```

2. Para verificar se o hub já está ligado ao recurso DPS, execute o seguinte [comando de programa de extensão DPS](/cli/azure/iot/dps?view=azure-cli-latest#az_iot_dps_show&preserve-view=true).

    ```azurecli
     az iot dps show --name <Your provisioningServiceName>
    ```

    Reparem nos centros que estão ligados ao `iotHubs` membro.


## <a name="clean-up-resources"></a>Limpar os recursos

Outros guias de introdução desta coleção têm por base este guia de introdução. Se pretender continuar a trabalhar com guias de introdução subsequentes ou com os tutoriais, não limpe os recursos criados neste guia de introdução. Se não pretender continuar, pode utilizar o portal Azure ou o Azure CLI para eliminar o grupo de recursos e todos os seus recursos.

Para eliminar um grupo de recursos e todos os seus recursos a partir do portal Azure, basta abrir o grupo de recursos e clicar em **Eliminar grupo de recursos** e no topo.

Para eliminar o grupo de recursos implantado utilizando o Azure CLI:

```azurecli
az group delete --name "${projectName}rg"
```

Também pode eliminar grupos de recursos e recursos individuais utilizando o portal Azure, PowerShell ou REST APIs, bem como com SDKs de plataforma suportada publicados para O Azure Resource Manager ou IoT Hub Device Provisioning Service.

## <a name="next-steps"></a>Próximos passos

Neste quickstart, você implantou um hub IoT e uma instância de Serviço de Provisionamento de Dispositivos, e ligou os dois recursos. Para aprender a usar esta configuração para forrá um dispositivo, continue a iniciar o quickstart para criar um dispositivo.

> [!div class="nextstepaction"]
> [Quickstart para providenciar um dispositivo](./quick-create-simulated-device-symm-key.md)

