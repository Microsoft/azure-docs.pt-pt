---
title: Configurar o carregamento de ficheiro para o IoT Hub com a CLI do Azure | Documentos da Microsoft
description: Como configurar o ficheiro carrega para o IoT Hub do Azure com a CLI do Azure de várias plataformas.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/08/2017
ms.author: robin.shahan
ms.openlocfilehash: a77635b6c022527203d1df752723aac3ce2c296c
ms.sourcegitcommit: 15e9613e9e32288e174241efdb365fa0b12ec2ac
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/28/2019
ms.locfileid: "57010791"
---
# <a name="configure-iot-hub-file-uploads-using-azure-cli"></a>Configurar o IoT Hub, carregamentos de ficheiros com a CLI do Azure

[!INCLUDE [iot-hub-file-upload-selector](../../includes/iot-hub-file-upload-selector.md)]

Para [carregar ficheiros a partir de um dispositivo](iot-hub-devguide-file-upload.md), primeiro tem de associar uma conta de armazenamento do Azure com o seu hub IoT. Pode utilizar uma conta de armazenamento existente ou crie um novo.

Para concluir este tutorial, precisa do seguinte:

* Uma conta ativa do Azure. Se não tiver uma conta, pode criar uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/) em apenas alguns minutos.

* [CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

* Um hub IoT do Azure. Se não tiver um hub IoT, pode utilizar o [ `az iot hub create` comando](https://docs.microsoft.com/cli/azure/iot/hub#az-iot-hub-create) para criar um ou [criar um hub IoT com o portal](iot-hub-create-through-portal.md).

* Uma conta do Armazenamento do Azure. Se não tiver uma conta de armazenamento do Azure, pode utilizar o [CLI do Azure - gerir contas de armazenamento](../storage/common/storage-azure-cli.md#manage-storage-accounts) para criar ou utilizar o portal para [criar uma conta de armazenamento](../storage/common/storage-create-storage-account.md).

## <a name="sign-in-and-set-your-azure-account"></a>Iniciar sessão e definir a sua conta do Azure

Inicie sessão na sua conta do Azure e selecione a sua subscrição.

1. Na linha de comandos, execute o [comandos de início de sessão](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest):

    ```azurecli
    az login
    ```

    Siga as instruções para se autenticar com o código e inicie sessão na sua conta do Azure através de um browser.

2. Se tiver várias subscrições do Azure, iniciar sessão no Azure dá-lhe acesso a todas as contas do Azure associadas às suas credenciais. Utilize o seguinte procedimento [comando para listar as contas do Azure](https://docs.microsoft.com/cli/azure/account) disponíveis que pode utilizar:

    ```azurecli
    az account list
    ```

    Utilize o seguinte comando para selecionar a subscrição que pretende utilizar para executar os comandos para criar o hub IoT. Pode utilizar o nome ou o ID da subscrição da saída do comando anterior:

    ```azurecli
    az account set --subscription {your subscription name or id}
    ```

## <a name="retrieve-your-storage-account-details"></a>Obter os detalhes da conta de armazenamento

Os passos seguintes partem do princípio de que criou a sua conta de armazenamento com o **Resource Manager** modelo de implementação e não a **clássico** modelo de implementação.

Para configurar os carregamentos de ficheiros a partir dos seus dispositivos, precisa da cadeia de ligação para uma conta de armazenamento do Azure. A conta de armazenamento tem de ser na mesma subscrição que o seu hub IoT. Também precisa do nome de um contentor de BLOBs na conta de armazenamento. Utilize o seguinte comando para obter as chaves de conta de armazenamento:

```azurecli
az storage account show-connection-string --name {your storage account name} \
  --resource-group {your storage account resource group}
```

Anote o **connectionString** valor. Precisa nos passos seguintes.

Pode utilizar um contentor de BLOBs existentes para carregamentos de ficheiros ou criar um novo:

* Para listar os contentores de BLOBs existentes na sua conta de armazenamento, utilize o seguinte comando:

    ```azurecli
    az storage container list --connection-string "{your storage account connection string}"
    ```

* Para criar um contentor de BLOBs na sua conta de armazenamento, utilize o seguinte comando:

    ```azurecli
    az storage container create --name {container name} \
      --connection-string "{your storage account connection string}"
    ```

## <a name="file-upload"></a>Carregamento de ficheiros

Agora pode configurar o seu IoT hub para ativar a capacidade de [carregar ficheiros para o hub IoT](iot-hub-devguide-file-upload.md) com os detalhes da conta de armazenamento.

A configuração requer os seguintes valores:

* **Contentor de armazenamento**: Um contentor de BLOBs numa conta de armazenamento do Azure na sua subscrição do Azure atual para associar o seu hub IoT. Obter as informações de conta de armazenamento necessário na secção anterior. IoT Hub gera automaticamente os URIs de SAS com permissões de escrita para este contentor de BLOBs para os dispositivos a utilizar quando eles carregam ficheiros.

* **Receber notificações sobre os ficheiros carregados**: Ativar ou desativar notificações de carregamento do ficheiro.

* **SAS TTL**: Esta definição é o tempo de vida dos URIs de SAS retornado para o dispositivo ao IoT Hub. Definido como uma hora por predefinição.

* **TTL de padrão de definições de notificação de ficheiros**: O tempo de vida de um ficheiro a carregar notificação antes de expirar. Definido como um dia por predefinição.

* **Contagem máxima de entrega de notificação de ficheiros**: O número de vezes que o IoT Hub tentará entregar um arquivo carregar notificação. Definido como 10, por predefinição.

Utilize os seguintes comandos do CLI do Azure para configurar as definições de carregamento de ficheiros no seu hub IoT:

<!--Robinsh this is out of date, add cloud powershell -->

Na shell de bash, utilize:

```azurecli
az iot hub update --name {your iot hub name} \
  --set properties.storageEndpoints.'$default'.connectionString="{your storage account connection string}"

az iot hub update --name {your iot hub name} \
  --set properties.storageEndpoints.'$default'.containerName="{your storage container name}"

az iot hub update --name {your iot hub name} \
  --set properties.storageEndpoints.'$default'.sasTtlAsIso8601=PT1H0M0S

az iot hub update --name {your iot hub name} \
  --set properties.enableFileUploadNotifications=true

az iot hub update --name {your iot hub name} \
  --set properties.messagingEndpoints.fileNotifications.maxDeliveryCount=10

az iot hub update --name {your iot hub name} \
  --set properties.messagingEndpoints.fileNotifications.ttlAsIso8601=PT1H0M0S
```

Pode rever a configuração de carregamento de ficheiros no seu hub IoT com o seguinte comando:

```azurecli
az iot hub show --name {your iot hub name}
```

## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações sobre as capacidades de carregamento de arquivo do IoT Hub, veja [carregar ficheiros a partir de um dispositivo](iot-hub-devguide-file-upload.md).

Siga estas ligações para saber mais sobre como gerir o IoT Hub do Azure:

* [Gerir dispositivos IoT em massa](iot-hub-bulk-identity-mgmt.md)
* [Métricas do IoT Hub](iot-hub-metrics.md)
* [Monitorização de operações](iot-hub-operations-monitoring.md)

Para explorar ainda mais os recursos do IoT Hub, veja:

* [guia para programadores do IoT Hub](iot-hub-devguide.md)
* [Implementar o AI em dispositivos de ponta com o Azure IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)
* [Proteger a sua solução de IoT desde o backup](../iot-fundamentals/iot-security-ground-up.md)
