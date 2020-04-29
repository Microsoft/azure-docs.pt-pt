---
title: Configure o upload de ficheiros para o IoT Hub utilizando o Azure CLI [ Microsoft Docs
description: Como configurar os uploads de ficheiros para o Hub Azure IoT utilizando o Azure CLI de plataforma cruzada.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/08/2017
ms.author: robinsh
ms.openlocfilehash: df3c8d2abf59de6c9f685ad8d93e6689738df8e6
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "78302530"
---
# <a name="configure-iot-hub-file-uploads-using-azure-cli"></a>Configure uploads de ficheiros IoT Hub utilizando o Azure CLI

[!INCLUDE [iot-hub-file-upload-selector](../../includes/iot-hub-file-upload-selector.md)]

Para [fazer upload de ficheiros a partir de um dispositivo,](iot-hub-devguide-file-upload.md)tem primeiro de associar uma conta Azure Storage ao seu hub IoT. Pode utilizar uma conta de armazenamento existente ou criar uma nova.

Para concluir este tutorial, precisa do seguinte:

* Uma conta ativa do Azure. Se não tiver uma conta, pode criar uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/) em apenas alguns minutos.

* [Azure CLI.](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)

* Um centro Azure IoT. Se não tiver um hub IoT, pode [ `az iot hub create` ](https://docs.microsoft.com/cli/azure/iot/hub#az-iot-hub-create) usar o comando para criar um ou [criar um hub IoT usando o portal](iot-hub-create-through-portal.md).

* Uma conta do Armazenamento do Azure. Se não tiver uma conta de Armazenamento Azure, pode utilizar o Azure CLI para criar um. Para obter mais informações, veja [Criar uma conta de armazenamento](../storage/common/storage-create-storage-account.md).

## <a name="sign-in-and-set-your-azure-account"></a>Inscreva-se e detete a sua conta Azure

Inicie sessão na sua conta do Azure e selecione a sua subscrição.

1. Na linha de comandos, execute o [comando login](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest):

    ```azurecli
    az login
    ```

    Siga as instruções para se autenticar com o código e inicie sessão na sua conta do Azure através de um browser.

2. Se tiver várias subscrições do Azure, iniciar sessão no Azure dá-lhe acesso a todas as contas do Azure associadas às suas credenciais. Utilize o comando [ para listar as contas do Azure](https://docs.microsoft.com/cli/azure/account) disponíveis e que pode utilizar:

    ```azurecli
    az account list
    ```

    Utilize o seguinte comando para selecionar a subscrição que pretende utilizar para executar os comandos para criar o seu hub IoT. Pode utilizar o nome ou o ID da subscrição da saída do comando anterior:

    ```azurecli
    az account set --subscription {your subscription name or id}
    ```

## <a name="retrieve-your-storage-account-details"></a>Recupere os detalhes da sua conta de armazenamento

Os seguintes passos assumem que criou a sua conta de armazenamento utilizando o modelo de implementação do Gestor de **Recursos,** e não o modelo de implementação **Classic.**

Para configurar uploads de ficheiros dos seus dispositivos, precisa da cadeia de ligação para uma conta de armazenamento Azure. A conta de armazenamento deve estar na mesma subscrição que o seu hub IoT. Também precisa do nome de um recipiente de bolhas na conta de armazenamento. Utilize o seguinte comando para recuperar as chaves da sua conta de armazenamento:

```azurecli
az storage account show-connection-string --name {your storage account name} \
  --resource-group {your storage account resource group}
```

Tome nota do valor do string de **ligação.** Precisa nos seguintes passos.

Pode utilizar um recipiente de bolhas existente para os uploads dos seus ficheiros ou criar um novo:

* Para listar os recipientes de bolha existentes na sua conta de armazenamento, utilize o seguinte comando:

    ```azurecli
    az storage container list --connection-string "{your storage account connection string}"
    ```

* Para criar um recipiente de bolha na sua conta de armazenamento, utilize o seguinte comando:

    ```azurecli
    az storage container create --name {container name} \
      --connection-string "{your storage account connection string}"
    ```

## <a name="file-upload"></a>Upload de ficheiros

Agora pode configurar o seu hub IoT para permitir o upload de [ficheiros para o hub IoT](iot-hub-devguide-file-upload.md) utilizando os detalhes da sua conta de armazenamento.

A configuração requer os seguintes valores:

* **Recipiente de armazenamento**: Um recipiente de blob numa conta de armazenamento Azure na sua assinatura Atual Azure para associar ao seu hub IoT. Recuperou as informações necessárias da conta de armazenamento na secção anterior. O IoT Hub gera automaticamente URIs SAS com permissões de escrita a este recipiente blob para dispositivos a utilizar quando fazem o upload de ficheiros.

* **Receba notificações para ficheiros enviados**: Ativar ou desativar notificações de upload de ficheiros.

* **SAS TTL**: Esta definição é o tempo de viver dos URIs SAS devolvidos ao dispositivo pelo IoT Hub. Definido para uma hora por defeito.

* Definições de notificação de **ficheiros padrão TTL**: O tempo de vida de uma notificação de upload de ficheiro antes de expirar. Definido para um dia por defeito.

* Contagem máxima de entrega de notificação de **ficheiros**: O número de vezes que o IoT Hub tenta entregar uma notificação de upload de ficheiro. Definido para 10 por padrão.

Utilize os seguintes comandos Azure CLI para configurar as definições de upload de ficheiros no seu hub IoT:

<!--Robinsh this is out of date, add cloud powershell -->

Numa concha, use:

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

Pode rever a configuração de upload de ficheiros no seu hub IoT utilizando o seguinte comando:

```azurecli
az iot hub show --name {your iot hub name}
```

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre as capacidades de upload de ficheiros do IoT Hub, consulte [ficheiros Upload a partir de um dispositivo](iot-hub-devguide-file-upload.md).

Siga estes links para saber mais sobre a gestão do Azure IoT Hub:

* [Gerir dispositivos IoT em massa](iot-hub-bulk-identity-mgmt.md)
* [Métricas do Hub IoT](iot-hub-metrics.md)
* [Monitorização de operações](iot-hub-operations-monitoring.md)

Para explorar ainda mais as capacidades do IoT Hub, consulte:

* [Guia de desenvolvimento do IoT Hub](iot-hub-devguide.md)
* [Implementar o AI em dispositivos de ponta com o Azure IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)
* [Proteja a sua solução IoT do zero](../iot-fundamentals/iot-security-ground-up.md)
