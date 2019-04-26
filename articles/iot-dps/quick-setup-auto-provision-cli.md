---
title: Configurar um Serviço Aprovisionamento de Dispositivos com a CLI do Azure | Microsoft Docs
description: Início Rápido do Azure - Configurar o Serviço Aprovisionamento de Dispositivos no Hub IoT do Azure com a CLI do Azure
author: wesmc7777
ms.author: wesmc
ms.date: 02/26/2018
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 3062fb640985498ba35e23f6310828a2bd59bfed
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60363721"
---
# <a name="set-up-the-iot-hub-device-provisioning-service-with-azure-cli"></a>Configurar o Serviço Aprovisionamento de Dispositivos no Hub IoT com a CLI do Azure

A CLI do Azure é utilizada para criar e gerir recursos do Azure a partir da linha de comandos ou em scripts. Este Início Rápido mostra como utilizar a CLI do Azure para criar um hub IoT e um Serviço Aprovisionamento de Dispositivos no Hub IoT e como associar ambos os serviços. 

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

> [!IMPORTANT]
> Tanto o hub IoT, como o Serviço Aprovisionamento, que criar neste Início Rápido serão detetáveis publicamente como pontos finais DNS. Se optar por alterar os nomes utilizadores para os recursos, evite informações confidenciais.
>



## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um grupo de recursos com o comando [az group create](/cli/azure/group#az-group-create). Um grupo de recursos do Azure é um contentor lógico no qual os recursos do Azure são implementados e geridos. 

O exemplo seguinte cria um grupo de recursos com o nome *my-sample-resource-group* na localização *westus*.

```azurecli-interactive 
az group create --name my-sample-resource-group --location westus
```

> [!TIP]
> O exemplo cria o grupo de recursos na localização E.U.A. Oeste. Pode executar o comando `az account list-locations -o table` para ver uma lista das localizações disponíveis.
>
>

## <a name="create-an-iot-hub"></a>Criar um hub IoT

Crie um hub IoT com o comando [az iot hub create](/cli/azure/iot/hub#az-iot-hub-create).

O exemplo seguinte cria um hub IoT com o nome *my-sample-hub* na localização *westus*.  

```azurecli-interactive 
az iot hub create --name my-sample-hub --resource-group my-sample-resource-group --location westus
```

## <a name="create-a-provisioning-service"></a>Criar um Serviço de Aprovisionamento

Crie um Serviço de Aprovisionamento com o comando [az iot dps create](/cli/azure/iot/dps#az-iot-dps-create). 

O exemplo seguinte cria um Serviço de Aprovisionamento com o nome *my-sample-dps* na localização *westus*.  

```azurecli-interactive 
az iot dps create --name my-sample-dps --resource-group my-sample-resource-group --location westus
```

> [!TIP]
> O exemplo cria o Serviço de Aprovisionamento na localização E.U.A. Oeste. Pode ver uma lista das localizações disponíveis ao executar o comando `az provider show --namespace Microsoft.Devices --query "resourceTypes[?resourceType=='ProvisioningServices'].locations | [0]" --out table` ou ao aceder à página [Estado do Azure](https://azure.microsoft.com/status/) e procurar "Serviço Aprovisionamento de Dispositivos". Nos comandos, as localizações podem ser especificadas no formato de uma ou de várias palavras; por exemplo, westus, West US, WEST US, etc. O valor não é sensível às maiúsculas e minúsculas. Se especificar a localização com o formato de várias palavras, ponha o valor entre aspas, como, por exemplo, `-- location "West US"`.
>


## <a name="get-the-connection-string-for-the-iot-hub"></a>Obter a cadeia de ligação do hub IoT

Precisa da cadeia de ligação do hub IoT para associar ao Serviço Aprovisionamento de Dispositivos. Utilize o comando [az iot hub show-connection-string](/cli/azure/iot/hub#az-iot-hub-show-connection-string) para obter a cadeia de ligação e utilize a saída do mesmo para definir uma variável que utilizará quando associar ambos os recursos. 

O exemplo seguinte define a variável *hubConnectionString* como o valor da cadeia de ligação da chave primária da política *iothubowner* do hub. Pode especificar uma política diferente com o parâmetro `--policy-name`. O comando utiliza as opções [consulta](/cli/azure/query-azure-cli) e [saída](/cli/azure/format-output-azure-cli#tsv-output-format) da CLI do Azure para extrair a cadeia de ligação da saída do comando.

```azurecli-interactive 
hubConnectionString=$(az iot hub show-connection-string --name my-sample-hub --key primary --query connectionString -o tsv)
```

Pode utilizar o comando `echo` para ver a cadeia de ligação.

```azurecli-interactive 
echo $hubConnectionString
```

> [!NOTE]
> Estes dois comandos são válidos para anfitriões em execução no Bash. Se utilizar uma shell Windows/CMD local ou um anfitrião do PowerShell, tem de modificar os comandos de modo a que utilizem a sintaxe correta para esse ambiente.
>

## <a name="link-the-iot-hub-and-the-provisioning-service"></a>Associar o hub IoT e o Serviço de Aprovisionamento

Associe o hub IoT e o Serviço de Aprovisionamento com o comando [az iot dps linked-hub create](/cli/azure/iot/dps/linked-hub#az-iot-dps-linked-hub-create). 

O exemplo seguinte associa um hub IoT com o nome *my-sample-hub* na localização *westus* a um Serviço Aprovisionamento de Dispositivos com o nome *my-sample-dps*. Utiliza a cadeia de ligação para *my-sample-hub* armazenada na variável *hubConnectionString* no passo anterior.

```azurecli-interactive 
az iot dps linked-hub create --dps-name my-sample-dps --resource-group my-sample-resource-group --connection-string $hubConnectionString --location westus
```

## <a name="verify-the-provisioning-service"></a>Verificar o Serviço Aprovisionamento

Obtenha os detalhes do Serviço Aprovisionamento com o comando [az iot dps show](/cli/azure/iot/dps#az-iot-dps-show).

O exemplo seguinte obtém os detalhes de um serviço de aprovisionamento com o nome *my-sample-dps*. O hub IoT associado é apresentado na coleção *properties.iotHubs*.

```azurecli-interactive
az iot dps show --name my-sample-dps
```

## <a name="clean-up-resources"></a>Limpar recursos

Outros Guias de Introdução desta coleção têm por base este Guia de Introdução. Se pretender continuar a trabalhar com Inícios Rápidos subsequentes ou com os tutoriais, não limpe os recursos criados neste Início Rápido. Se não quiser continuar, pode utilizar os comandos seguintes para eliminar o serviço de aprovisionamento, o hub do IoT ou o grupo de recursos e todos os recursos do mesmo.

Para eliminar o serviço de aprovisionamento, execute o comando [az iot dps delete](/cli/azure/iot/dps#az-iot-dps-delete):

```azurecli-interactive
az iot dps delete --name my-sample-dps --resource-group my-sample-resource-group
```
Para eliminar o hub IoT, execute o comando [az iot hub delete](/cli/azure/iot/hub#az-iot-hub-delete):

```azurecli-interactive
az iot hub delete --name my-sample-hub --resource-group my-sample-resource-group
```

Para eliminar um grupo de recursos e todos os respetivos recursos, execute o comando [az group delete](/cli/azure/group#az-group-delete):

```azurecli-interactive
az group delete --name my-sample-resource-group
```

## <a name="next-steps"></a>Passos Seguintes

Neste Início Rápido, implementou um hub do IoT e uma instância do Serviço Aprovisionamento de Dispositivos e ligou ambos os recursos. Para saber como utilizar esta configuração para aprovisionar um dispositivo simulado, avance para o Início Rápido para a criação de dispositivos simulados.

> [!div class="nextstepaction"]
> [Quickstart to create simulated device](./quick-create-simulated-device.md) (Início rápido para criar dispositivos simulados)

