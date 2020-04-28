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
ms.openlocfilehash: b2ff542d2782293e89b66e5d25cb67a9bcde6da8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75773021"
---
Este erro pode ocorrer sempre que o serviço do Azure File Sync está inacessível no servidor. Pode resolver este erro ao realizar os passos seguintes:

1. Verifique se `FileSyncSvc.exe` o serviço Windows não está bloqueado pela sua firewall.
2. Verifique se a porta 443 está aberta a ligações de saída ao serviço Dessincronização de ficheiros Azure. Pode fazer isto `Test-NetConnection` com o cmdlet. O URL para o marcador de posição `<azure-file-sync-endpoint>` abaixo pode ser encontrada no documento [Definições da firewall e do proxy do Azure File Sync](../articles/storage/files/storage-sync-files-firewall-and-proxy.md#firewall). 

    ```powershell
    Test-NetConnection -ComputerName <azure-file-sync-endpoint> -Port 443
    ```

3. Verifique se a configuração do proxy está definida como o esperado. Pode ser definida com o cmdlet `Get-StorageSyncProxyConfiguration`. Par obter mais informações sobre como definir a configuração do proxy do Azure File Sync, veja [Definições da firewall e do proxy do Azure File Sync](../articles/storage/files/storage-sync-files-firewall-and-proxy.md#firewall).

    ```powershell
    $agentPath = "C:\Program Files\Azure\StorageSyncAgent"
    Import-Module "$agentPath\StorageSync.Management.ServerCmdlets.dll"
    Get-StorageSyncProxyConfiguration
    ```
4. Utilize o cmdlet de conectividade Deteste-StorageSyncNetwork para verificar a conectividade da rede aos pontos finais do serviço. Para saber mais, consulte a [conectividade da rede de teste para os pontos finais de serviço](https://docs.microsoft.com/azure/storage/files/storage-sync-files-firewall-and-proxy#test-network-connectivity-to-service-endpoints).    

5. Contacte o seu administrador de rede para obter uma assistência adicional para a conectividade da rede de resolução de problemas.
