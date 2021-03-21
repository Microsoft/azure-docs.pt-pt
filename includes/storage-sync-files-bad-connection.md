---
title: incluir ficheiro
description: incluir ficheiro
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 07/08/2018
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: b26d4af29a92fb0f14c52e76a7eae1d0073a3aa0
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96005340"
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
4. Utilize o Test-StorageSyncNetworkConnectivity cmdlet para verificar a conectividade da rede nos pontos finais de serviço. Para saber mais, consulte [a conectividade da rede de teste para os pontos finais de serviço](../articles/storage/files/storage-sync-files-firewall-and-proxy.md#test-network-connectivity-to-service-endpoints).    

5. Contacte o administrador da rede para obter uma conectividade adicional de resolução de problemas da rede de resolução de problemas.