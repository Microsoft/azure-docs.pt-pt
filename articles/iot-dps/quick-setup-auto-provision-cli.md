---
title: Quickstart - Criar o Serviço de Provisionamento de Dispositivos Azure IoT Hub utilizando o Azure CLI
description: Quickstart - Configurar o Serviço de Provisionamento de Dispositivos Azure IoT Hub (DPS) utilizando o Azure CLI
author: wesmc7777
ms.author: wesmc
ms.date: 11/08/2019
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 611068fa020321be88be6e1d6da663266029c658
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "94660190"
---
# <a name="quickstart-set-up-the-iot-hub-device-provisioning-service-with-azure-cli"></a>Quickstart: Crie o serviço de provisionamento de dispositivos IoT Hub com Azure CLI

A CLI do Azure é utilizada para criar e gerir recursos do Azure a partir da linha de comandos ou em scripts. Este quickstart detalha usando o CLI Azure para criar um hub IoT e um serviço de fornecimento de dispositivos IoT Hub, e para ligar os dois serviços juntos. 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

> [!IMPORTANT]
> Tanto o hub IoT como o serviço de fornecimento que criar neste quickstart serão publicamente detetáveis como pontos finais dns. Se optar por alterar os nomes utilizadores para os recursos, evite informações confidenciais.
>

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]


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

O exemplo seguinte cria um hub IoT com o nome *my-sample-hub* na localização *westus*. Um nome de hub IoT deve ser globalmente único em Azure, por isso você pode querer adicionar um prefixo ou sufixo único ao nome de exemplo, ou escolher um novo nome completamente. Certifique-se de que o seu nome segue as convenções de nomeação adequadas para um hub IoT: deve ter 3-50 caracteres de comprimento e pode conter apenas caracteres alfanuméricos ou hífenes de caixa superior ou inferior ('-'). 

```azurecli-interactive 
az iot hub create --name my-sample-hub --resource-group my-sample-resource-group --location westus
```

## <a name="create-a-device-provisioning-service"></a>Criar um serviço de provisionamento de dispositivos

Crie um serviço de provisionamento de dispositivos com os [dps az iot criar](/cli/azure/iot/dps#az-iot-dps-create) comando. 

O exemplo a seguir cria um serviço de fornecimento chamado *my-sample-dps* no local *westus.* Você também precisará escolher um nome globalmente único para o seu próprio serviço de fornecimento. Certifique-se de que segue as convenções de nomeação adequadas para um serviço de fornecimento de dispositivos IoT Hub: deve ter 3-64 caracteres de comprimento e pode conter apenas caracteres alfanuméricos ou hífenes maiúsitos superiores ou inferiores ('-').

```azurecli-interactive 
az iot dps create --name my-sample-dps --resource-group my-sample-resource-group --location westus
```

> [!TIP]
> O exemplo cria o Serviço de Aprovisionamento na localização E.U.A. Oeste. Pode ver uma lista das localizações disponíveis ao executar o comando `az provider show --namespace Microsoft.Devices --query "resourceTypes[?resourceType=='ProvisioningServices'].locations | [0]" --out table` ou ao aceder à página [Estado do Azure](https://azure.microsoft.com/status/) e procurar "Serviço Aprovisionamento de Dispositivos". Nos comandos, as localizações podem ser especificadas num formato de uma palavra ou multi-palavras; por exemplo: westus, West US, WEST US, etc. O valor não é sensível a casos. Se especificar a localização com o formato de várias palavras, ponha o valor entre aspas, como, por exemplo, `--location "West US"`.
>

## <a name="get-the-connection-string-for-the-iot-hub"></a>Obter a cadeia de ligação do hub IoT

Precisa da cadeia de ligação do hub IoT para associar ao Serviço Aprovisionamento de Dispositivos. Utilize o comando [az iot hub show-connection-string](/cli/azure/iot/hub#az-iot-hub-show-connection-string) para obter a cadeia de ligação e utilize a saída do mesmo para definir uma variável que utilizará quando associar ambos os recursos. 

O exemplo a seguir define a variável *hubConnectionString* ao valor da cadeia de ligação para a chave primária da política do *iothubowner* do hub (o `--policy-name` parâmetro pode ser usado para especificar uma política diferente). Troque *o meu centro de amostras* pelo nome único do hub IoT que escolheu anteriormente. O comando utiliza as opções [consulta](/cli/azure/query-azure-cli) e [saída](/cli/azure/format-output-azure-cli#tsv-output-format) da CLI do Azure para extrair a cadeia de ligação da saída do comando.

```azurecli-interactive 
hubConnectionString=$(az iot hub show-connection-string --name my-sample-hub --key primary --query connectionString -o tsv)
```

Pode utilizar o comando `echo` para ver a cadeia de ligação.

```azurecli-interactive 
echo $hubConnectionString
```

> [!NOTE]
> Estes dois comandos são válidos para anfitriões em execução no Bash.
> 
> Se estiver a utilizar uma concha local do Windows/CMD ou um hospedeiro PowerShell, modifique os comandos para utilizar a sintaxe correta para esse ambiente.
>
> Se estiver a usar a Azure Cloud Shell, verifique se o ambiente desce no lado esquerdo da janela da concha diz **Bash**.
>

## <a name="link-the-iot-hub-and-the-provisioning-service"></a>Associar o hub IoT e o Serviço de Aprovisionamento

Associe o hub IoT e o Serviço de Aprovisionamento com o comando [az iot dps linked-hub create](/cli/azure/iot/dps/linked-hub#az-iot-dps-linked-hub-create). 

O exemplo a seguir liga um hub IoT chamado *my-sample-hub* no local *westus* e um serviço de provisionamento de dispositivos chamado *my-sample-dps*. Troque estes nomes pelos nomes exclusivos do hub IoT e do Serviço de Provisionamento de Dispositivos que escolheu anteriormente. O comando utiliza a cadeia de ligação para o seu hub IoT que foi armazenado na variável *hubConnectionString* no passo anterior.

```azurecli-interactive 
az iot dps linked-hub create --dps-name my-sample-dps --resource-group my-sample-resource-group --connection-string $hubConnectionString --location westus
```

O comando pode demorar alguns minutos a ser concluído.

## <a name="verify-the-provisioning-service"></a>Verificar o Serviço Aprovisionamento

Obtenha os detalhes do Serviço Aprovisionamento com o comando [az iot dps show](/cli/azure/iot/dps#az-iot-dps-show).

O exemplo seguinte obtém os detalhes de um serviço de aprovisionamento com o nome *my-sample-dps*. Troque este nome pelo seu próprio nome de Serviço de Provisionamento de Dispositivos.

```azurecli-interactive
az iot dps show --name my-sample-dps
```
O hub IoT associado é apresentado na coleção *properties.iotHubs*.

![Verificar serviço de provisionamento](./media/quick-setup-auto-provision-cli/verify-provisioning-service.png)

## <a name="clean-up-resources"></a>Limpar os recursos

Outros guias de introdução desta coleção têm por base este guia de introdução. Se pretender continuar a trabalhar com guias de introdução subsequentes ou com os tutoriais, não limpe os recursos criados neste guia de introdução. Se não quiser continuar, pode utilizar os comandos seguintes para eliminar o serviço de aprovisionamento, o hub do IoT ou o grupo de recursos e todos os recursos do mesmo. Substitua os nomes dos recursos abaixo escritos pelos nomes dos seus próprios recursos.

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

## <a name="next-steps"></a>Passos seguintes

Neste quickstart, você implantou um hub IoT e uma instância de Serviço de Provisionamento de Dispositivos, e ligou os dois recursos. Para aprender a usar esta configuração para forrá um dispositivo simulado, continue até ao início rápido para criar um dispositivo simulado.

> [!div class="nextstepaction"]
> [Quickstart para criar um dispositivo simulado](./quick-create-simulated-device.md)
