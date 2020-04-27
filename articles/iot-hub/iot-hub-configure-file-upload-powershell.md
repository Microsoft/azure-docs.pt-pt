---
title: Utilize o Azure PowerShell para configurar o upload de ficheiros [ Microsoft Docs
description: Como utilizar os cmdlets Azure PowerShell para configurar o seu hub IoT para ativar os uploads de ficheiros de dispositivos conectados. Inclui informações sobre a configuração da conta de armazenamento do destino Azure.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/08/2017
ms.author: robinsh
ms.openlocfilehash: c8fc0393e0961b46fbb8031d735f27e9ad785031
ms.sourcegitcommit: be32c9a3f6ff48d909aabdae9a53bd8e0582f955
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/26/2020
ms.locfileid: "60318465"
---
# <a name="configure-iot-hub-file-uploads-using-powershell"></a>Configure uploads de ficheiros IoT Hub usando powerShell

[!INCLUDE [iot-hub-file-upload-selector](../../includes/iot-hub-file-upload-selector.md)]

Para utilizar a funcionalidade de upload de [ficheiros no IoT Hub,](iot-hub-devguide-file-upload.md)tem primeiro de associar uma conta de armazenamento Azure ao seu hub IoT. Pode utilizar uma conta de armazenamento existente ou criar uma nova.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Para concluir este tutorial, precisa do seguinte:

* Uma conta ativa do Azure. Se não tiver uma conta, pode criar uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/) em apenas alguns minutos.

* [Cmdlets Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-Az-ps).

* Um centro Azure IoT. Se não tiver um hub IoT, pode usar o [cmdlet New-AzIoTHub](https://docs.microsoft.com/powershell/module/az.iothub/new-aziothub) para criar um ou usar o portal para [criar um hub IoT](iot-hub-create-through-portal.md).

* Uma conta de armazenamento do Azure. Se não tiver uma conta de armazenamento Azure, pode utilizar os [cmdlets De Armazenamento Azure PowerShell](https://docs.microsoft.com/powershell/module/az.storage/) para criar uma ou usar o portal para [criar uma conta](../storage/common/storage-create-storage-account.md) de armazenamento

## <a name="sign-in-and-set-your-azure-account"></a>Inscreva-se e detete a sua conta Azure

Inicie sessão na sua conta do Azure e selecione a sua subscrição.

1. No pedido powerShell, executar o cmdlet **Connect-AzAccount:**

    ```powershell
    Connect-AzAccount
    ```

2. Se tiver várias subscrições do Azure, a sessão no Azure dá-lhe acesso a todas as subscrições do Azure associadas às suas credenciais. Utilize o seguinte comando para listar as subscrições Azure disponíveis para que possa utilizar:

    ```powershell
    Get-AzSubscription
    ```

    Utilize o seguinte comando para selecionar a subscrição que pretende utilizar para executar os comandos para gerir o seu hub IoT. Pode utilizar o nome ou o ID da subscrição da saída do comando anterior:

    ```powershell
    Select-AzSubscription `
        -SubscriptionName "{your subscription name}"
    ```

## <a name="retrieve-your-storage-account-details"></a>Recupere os detalhes da sua conta de armazenamento

Os seguintes passos assumem que criou a sua conta de armazenamento utilizando o modelo de implementação do Gestor de **Recursos,** e não o modelo de implementação **Classic.**

Para configurar uploads de ficheiros dos seus dispositivos, precisa da cadeia de ligação para uma conta de armazenamento Azure. A conta de armazenamento deve estar na mesma subscrição que o seu hub IoT. Também precisa do nome de um recipiente de bolhas na conta de armazenamento. Utilize o seguinte comando para recuperar as chaves da sua conta de armazenamento:

```powershell
Get-AzStorageAccountKey `
  -Name {your storage account name} `
  -ResourceGroupName {your storage account resource group}
```

Tome nota do valor-chave da conta de armazenamento **key1.** Precisa nos seguintes passos.

Pode utilizar um recipiente de bolhas existente para os uploads dos seus ficheiros ou criar um novo:

* Para listar os recipientes de bolha existentes na sua conta de armazenamento, utilize os seguintes comandos:

    ```powershell
    $ctx = New-AzStorageContext `
        -StorageAccountName {your storage account name} `
        -StorageAccountKey {your storage account key}
    Get-AzStorageContainer -Context $ctx
    ```

* Para criar um recipiente de bolha na sua conta de armazenamento, utilize os seguintes comandos:

    ```powershell
    $ctx = New-AzStorageContext `
        -StorageAccountName {your storage account name} `
        -StorageAccountKey {your storage account key}
    New-AzStorageContainer `
        -Name {your new container name} `
        -Permission Off `
        -Context $ctx
    ```

## <a name="configure-your-iot-hub"></a>Configure o seu hub IoT

Agora pode configurar o seu hub IoT para [fazer o upload de ficheiros para o hub IoT](iot-hub-devguide-file-upload.md) utilizando os dados da sua conta de armazenamento.

A configuração requer os seguintes valores:

* **Recipiente de armazenamento**: Um recipiente de blob numa conta de armazenamento Azure na sua assinatura Atual Azure para associar ao seu hub IoT. Recuperou as informações necessárias da conta de armazenamento na secção anterior. O IoT Hub gera automaticamente URIs SAS com permissões de escrita a este recipiente blob para dispositivos a utilizar quando fazem o upload de ficheiros.

* **Receba notificações para ficheiros enviados**: Ativar ou desativar notificações de upload de ficheiros.

* **SAS TTL**: Esta definição é o tempo de viver dos URIs SAS devolvidos ao dispositivo pelo IoT Hub. Definido para uma hora por defeito.

* Definições de notificação de **ficheiros padrão TTL**: O tempo de vida de uma notificação de upload de ficheiro antes de expirar. Definido para um dia por defeito.

* Contagem máxima de entrega de notificação de **ficheiros**: O número de vezes que o IoT Hub tenta entregar uma notificação de upload de ficheiro. Definido para 10 por padrão.

Utilize o seguinte cmdlet PowerShell para configurar as definições de upload de ficheiros no seu hub IoT:

```powershell
Set-AzIotHub `
    -ResourceGroupName "{your iot hub resource group}" `
    -Name "{your iot hub name}" `
    -FileUploadNotificationTtl "01:00:00" `
    -FileUploadSasUriTtl "01:00:00" `
    -EnableFileUploadNotifications $true `
    -FileUploadStorageConnectionString "DefaultEndpointsProtocol=https;AccountName={your storage account name};AccountKey={your storage account key};EndpointSuffix=core.windows.net" `
    -FileUploadContainerName "{your blob container name}" `
    -FileUploadNotificationMaxDeliveryCount 10
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
