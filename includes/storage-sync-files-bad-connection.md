---
title: ficheiro de inclusão
description: ficheiro de inclusão
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 07/08/2018
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: b2ff542d2782293e89b66e5d25cb67a9bcde6da8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "75773021"
---
Este erro pode ocorrer sempre que o serviço do Azure File Sync está inacessível no servidor. Pode resolver este erro ao realizar os passos seguintes:

1. Verifique se o serviço Windows `FileSyncSvc.exe` não está bloqueado pela sua firewall.
2. Verifique se a porta 443 está aberta a ligações de saída ao serviço Azure File Sync. Pode fazer isso com o `Test-NetConnection` cmdlet. O URL para o marcador de posição `<azure-file-sync-endpoint>` abaixo pode ser encontrada no documento [Definições da firewall e do proxy do Azure File Sync](../articles/storage/files/storage-sync-files-firewall-and-proxy.md#firewall). 

    ```powershell
    Test-NetConnection -ComputerName <azure-file-sync-endpoint> -Port 443
    ```

3. Verifique se a configuração do proxy está definida como o esperado. Pode ser definida com o cmdlet `Get-StorageSyncProxyConfiguration`. Par obter mais informações sobre como definir a configuração do proxy do Azure File Sync, veja [Definições da firewall e do proxy do Azure File Sync](../articles/storage/files/storage-sync-files-firewall-and-proxy.md#firewall).

    ```powershell
    $agentPath = "C:\Program Files\Azure\StorageSyncAgent"
    Import-Module "$agentPath\StorageSync.Management.ServerCmdlets.dll"
    Get-StorageSyncProxyConfiguration
    ```
4. Utilize o Test-StorageSyncNetworkConnectivity cmdlet para verificar a conectividade da rede nos pontos finais de serviço. Para saber mais, consulte [a conectividade da rede de teste para os pontos finais de serviço](https://docs.microsoft.com/azure/storage/files/storage-sync-files-firewall-and-proxy#test-network-connectivity-to-service-endpoints).    

5. Contacte o administrador da rede para obter uma conectividade adicional de resolução de problemas da rede de resolução de problemas.
