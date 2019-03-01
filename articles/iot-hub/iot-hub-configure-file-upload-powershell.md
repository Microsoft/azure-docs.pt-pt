---
title: Utilizar o Azure PowerShell para configurar o carregamento de ficheiros | Documentos da Microsoft
description: Como utilizar cmdlets do Azure PowerShell para configurar o hub IoT para ativar o arquivo carrega a partir dos dispositivos ligados. Inclui informações sobre como configurar o conta de armazenamento do Azure de destino.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/08/2017
ms.author: robin.shahan
ms.openlocfilehash: 9754fe2bedae9c1eaf6b18614014485dbe8051f2
ms.sourcegitcommit: 15e9613e9e32288e174241efdb365fa0b12ec2ac
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/28/2019
ms.locfileid: "57010482"
---
# <a name="configure-iot-hub-file-uploads-using-powershell"></a>Configurar o IoT Hub, carregamentos de ficheiros com o PowerShell

[!INCLUDE [iot-hub-file-upload-selector](../../includes/iot-hub-file-upload-selector.md)]

Para utilizar o [funcionalidade de carregamento de ficheiros no IoT Hub](iot-hub-devguide-file-upload.md), primeiro tem de associar uma conta de armazenamento do Azure com o seu hub IoT. Pode utilizar uma conta de armazenamento existente ou crie um novo.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Para concluir este tutorial, precisa do seguinte:

* Uma conta ativa do Azure. Se não tiver uma conta, pode criar uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/) em apenas alguns minutos.

* [Cmdlets do Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-Az-ps).

* Um hub IoT do Azure. Se não tiver um hub IoT, pode utilizar o [cmdlet New-AzIoTHub](https://docs.microsoft.com/powershell/module/az.iothub/new-aziothub) para criar ou utilizar o portal para [criar um hub IoT](iot-hub-create-through-portal.md).

* Uma conta de armazenamento do Azure. Se não tiver uma conta de armazenamento do Azure, pode utilizar o [cmdlets do PowerShell do armazenamento do Azure](https://docs.microsoft.com/powershell/module/az.storage/) para criar ou utilizar o portal para [criar uma conta de armazenamento](../storage/common/storage-create-storage-account.md)

## <a name="sign-in-and-set-your-azure-account"></a>Iniciar sessão e definir a sua conta do Azure

Inicie sessão na sua conta do Azure e selecione a sua subscrição.

1. Na linha de comandos do PowerShell, execute o **Connect-AzAccount** cmdlet:

    ```powershell
    Connect-AzAccount
    ```

2. Se tiver várias subscrições do Azure, iniciar sessão no Azure dá-lhe acesso a todas as subscrições Azure associadas com as suas credenciais. Utilize o seguinte comando para listar as subscrições do Azure disponíveis que pode utilizar:

    ```powershell
    Get-AzSubscription
    ```

    Utilize o seguinte comando para selecionar a subscrição que pretende utilizar para executar os comandos para gerir o seu hub IoT. Pode utilizar o nome ou o ID da subscrição da saída do comando anterior:

    ```powershell
    Select-AzSubscription `
        -SubscriptionName "{your subscription name}"
    ```

## <a name="retrieve-your-storage-account-details"></a>Obter os detalhes da conta de armazenamento

Os passos seguintes partem do princípio de que criou a sua conta de armazenamento com o **Resource Manager** modelo de implementação e não a **clássico** modelo de implementação.

Para configurar os carregamentos de ficheiros a partir dos seus dispositivos, precisa da cadeia de ligação para uma conta de armazenamento do Azure. A conta de armazenamento tem de ser na mesma subscrição que o seu hub IoT. Também precisa do nome de um contentor de BLOBs na conta de armazenamento. Utilize o seguinte comando para obter as chaves de conta de armazenamento:

```powershell
Get-AzStorageAccountKey `
  -Name {your storage account name} `
  -ResourceGroupName {your storage account resource group}
```

Anote o **chave1** valor de chave de conta de armazenamento. Precisa nos passos seguintes.

Pode utilizar um contentor de BLOBs existentes para carregamentos de ficheiros ou crie uma nova:

* Para listar os contentores de BLOBs existentes na sua conta de armazenamento, utilize os seguintes comandos:

    ```powershell
    $ctx = New-AzStorageContext `
        -StorageAccountName {your storage account name} `
        -StorageAccountKey {your storage account key}
    Get-AzStorageContainer -Context $ctx
    ```

* Para criar um contentor de BLOBs na sua conta de armazenamento, utilize os seguintes comandos:

    ```powershell
    $ctx = New-AzStorageContext `
        -StorageAccountName {your storage account name} `
        -StorageAccountKey {your storage account key}
    New-AzStorageContainer `
        -Name {your new container name} `
        -Permission Off `
        -Context $ctx
    ```

## <a name="configure-your-iot-hub"></a>Configurar o hub IoT

Agora pode configurar o seu hub IoT para [carregar ficheiros para o hub IoT](iot-hub-devguide-file-upload.md) com os detalhes da conta de armazenamento.

A configuração requer os seguintes valores:

* **Contentor de armazenamento**: Um contentor de BLOBs numa conta de armazenamento do Azure na sua subscrição do Azure atual para associar o seu hub IoT. Obter as informações de conta de armazenamento necessário na secção anterior. IoT Hub gera automaticamente os URIs de SAS com permissões de escrita para este contentor de BLOBs para os dispositivos a utilizar quando eles carregam ficheiros.

* **Receber notificações sobre os ficheiros carregados**: Ativar ou desativar notificações de carregamento do ficheiro.

* **SAS TTL**: Esta definição é o tempo de vida dos URIs de SAS retornado para o dispositivo ao IoT Hub. Definido como uma hora por predefinição.

* **TTL de padrão de definições de notificação de ficheiros**: O tempo de vida de um ficheiro a carregar notificação antes de expirar. Definido como um dia por predefinição.

* **Contagem máxima de entrega de notificação de ficheiros**: O número de vezes que o IoT Hub tentará entregar um arquivo carregar notificação. Definido como 10, por predefinição.

Utilize o seguinte cmdlet do PowerShell para configurar o ficheiro de carregar as definições no seu hub IoT:

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
