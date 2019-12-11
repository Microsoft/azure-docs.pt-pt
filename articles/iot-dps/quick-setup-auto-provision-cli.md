---
title: Configurar o serviço de provisionamento de dispositivos no Hub IoT do Azure usando o CLI do Azure
description: Início rápido-configurar o DPS (serviço de provisionamento de dispositivos) do Hub IoT do Azure usando o CLI do Azure
author: wesmc7777
ms.author: wesmc
ms.date: 11/08/2019
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
ms.custom: mvc
ms.openlocfilehash: f5737dccca6baa2dc2c1d98233b80d871cf86007
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/10/2019
ms.locfileid: "74974721"
---
# <a name="quickstart-set-up-the-iot-hub-device-provisioning-service-with-azure-cli"></a>Início rápido: configurar o serviço de provisionamento de dispositivos no Hub IoT com o CLI do Azure

A CLI do Azure é utilizada para criar e gerir recursos do Azure a partir da linha de comandos ou em scripts. Este guia de início rápido fornece detalhes usando o CLI do Azure para criar um hub IoT e um serviço de provisionamento de dispositivos no Hub IoT e vincular os dois serviços juntos. 

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

> [!IMPORTANT]
> O Hub IoT e o serviço de provisionamento que você cria neste guia de início rápido serão detectáveis publicamente como pontos de extremidade DNS. Se optar por alterar os nomes utilizadores para os recursos, evite informações confidenciais.
>

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]


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

O exemplo seguinte cria um hub IoT com o nome *my-sample-hub* na localização *westus*. Um nome de Hub IoT deve ser globalmente exclusivo no Azure, portanto, talvez você queira adicionar um prefixo ou sufixo exclusivo ao nome de exemplo ou escolher um novo nome totalmente. Verifique se seu nome segue as convenções de nomenclatura adequadas para um hub IoT: deve ter de 3-50 caracteres de comprimento e pode conter apenas caracteres alfanuméricos ou hifens ('-'). 

```azurecli-interactive 
az iot hub create --name my-sample-hub --resource-group my-sample-resource-group --location westus
```

## <a name="create-a-device-provisioning-service"></a>Criar um serviço de provisionamento de dispositivos

Crie um serviço de provisionamento de dispositivos com o comando [AZ IOT DPS Create](/cli/azure/iot/dps#az-iot-dps-create) . 

O exemplo a seguir cria um serviço de provisionamento chamado *My-Sample-DPS* no local *westus* . Você também precisará escolher um nome globalmente exclusivo para seu próprio serviço de provisionamento. Verifique se ele segue as convenções de nomenclatura adequadas para um serviço de provisionamento de dispositivos no Hub IoT: deve ter de 3-64 caracteres de comprimento e pode conter apenas caracteres alfanuméricos ou hifens ('-').

```azurecli-interactive 
az iot dps create --name my-sample-dps --resource-group my-sample-resource-group --location westus
```

> [!TIP]
> O exemplo cria o Serviço de Aprovisionamento na localização E.U.A. Oeste. Pode ver uma lista das localizações disponíveis ao executar o comando `az provider show --namespace Microsoft.Devices --query "resourceTypes[?resourceType=='ProvisioningServices'].locations | [0]" --out table` ou ao aceder à página [Estado do Azure](https://azure.microsoft.com/status/) e procurar "Serviço Aprovisionamento de Dispositivos". Em comandos, os locais podem ser especificados em um formato de palavra ou de várias palavras; por exemplo: westus, oeste dos EUA, oeste dos EUA, etc. O valor não diferencia maiúsculas de minúsculas. Se especificar a localização com o formato de várias palavras, ponha o valor entre aspas, como, por exemplo, `-- location "West US"`.
>

## <a name="get-the-connection-string-for-the-iot-hub"></a>Obter a cadeia de ligação do hub IoT

Precisa da cadeia de ligação do hub IoT para associar ao Serviço Aprovisionamento de Dispositivos. Utilize o comando [az iot hub show-connection-string](/cli/azure/iot/hub#az-iot-hub-show-connection-string) para obter a cadeia de ligação e utilize a saída do mesmo para definir uma variável que utilizará quando associar ambos os recursos. 

O exemplo a seguir define a variável *hubConnectionString* como o valor da cadeia de conexão para a chave primária da política *iothubowner* do hub (o parâmetro `--policy-name` pode ser usado para especificar uma política diferente). Negocie o *meu Hub de amostra* para o nome exclusivo do Hub IOT que você escolheu anteriormente. O comando utiliza as opções [consulta](/cli/azure/query-azure-cli) e [saída](/cli/azure/format-output-azure-cli#tsv-output-format) da CLI do Azure para extrair a cadeia de ligação da saída do comando.

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

O exemplo a seguir vincula um hub IoT chamado *My-Sample-Hub* no local *westus* e um serviço de provisionamento de dispositivos chamado *My-Sample-DPS*. Negocie esses nomes para o Hub IoT exclusivo e os nomes de serviço de provisionamento de dispositivos escolhidos anteriormente. O comando usa a cadeia de conexão para o Hub IoT que foi armazenado na variável *hubConnectionString* na etapa anterior.

```azurecli-interactive 
az iot dps linked-hub create --dps-name my-sample-dps --resource-group my-sample-resource-group --connection-string $hubConnectionString --location westus
```

O comando pode levar alguns minutos para ser concluído.

## <a name="verify-the-provisioning-service"></a>Verificar o Serviço Aprovisionamento

Obtenha os detalhes do Serviço Aprovisionamento com o comando [az iot dps show](/cli/azure/iot/dps#az-iot-dps-show).

O exemplo seguinte obtém os detalhes de um serviço de aprovisionamento com o nome *my-sample-dps*. Negocie esse nome para seu próprio nome de serviço de provisionamento de dispositivos.

```azurecli-interactive
az iot dps show --name my-sample-dps
```
O hub IoT associado é apresentado na coleção *properties.iotHubs*.

![Verificar o serviço de provisionamento](./media/quick-setup-auto-provision-cli/verify-provisioning-service.png)

## <a name="clean-up-resources"></a>Limpar recursos

Outros guias de introdução desta coleção têm por base este guia de introdução. Se pretender continuar a trabalhar com guias de introdução subsequentes ou com os tutoriais, não limpe os recursos criados neste guia de introdução. Se não quiser continuar, pode utilizar os comandos seguintes para eliminar o serviço de aprovisionamento, o hub do IoT ou o grupo de recursos e todos os recursos do mesmo. Substitua os nomes dos recursos gravados abaixo pelos nomes dos seus próprios recursos.

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

Neste guia de início rápido, você implantou um hub IoT e uma instância do serviço de provisionamento de dispositivos e vinculou os dois recursos. Para saber como usar essa configuração para provisionar um dispositivo simulado, continue no início rápido para criar um dispositivo simulado.

> [!div class="nextstepaction"]
> [Início rápido para criar um dispositivo simulado](./quick-create-simulated-device.md)
