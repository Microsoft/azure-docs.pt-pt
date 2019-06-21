---
title: incluir ficheiro
description: incluir ficheiro
services: storage
author: wmgries
ms.service: storage
ms.topic: include
ms.date: 07/08/2018
ms.author: wgries
ms.custom: include file
ms.openlocfilehash: 359347e41264711a6ac0fa4d2dd0c3633590e917
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/18/2019
ms.locfileid: "67184332"
---
Este erro pode ocorrer sempre que o serviço de sincronização de ficheiros do Azure não está acessível a partir do servidor. Pode resolver este erro ao trabalhar com os seguintes passos:

1. Verificar se o serviço Windows `FileSyncSvc.exe` não está bloqueado pela firewall.
2. Certifique-se de que a porta 443 está aberta para ligações de saída para o serviço de sincronização de ficheiros do Azure. Pode fazê-lo com o `Test-NetConnection` cmdlet. O URL para o `<azure-file-sync-endpoint>` marcador de posição abaixo pode encontrados no [definições de proxy e firewall do Azure File Sync](../articles/storage/files/storage-sync-files-firewall-and-proxy.md#firewall) documento. 

    ```powershell
    Test-NetConnection -ComputerName <azure-file-sync-endpoint> -Port 443
    ```

3. Certifique-se de que a configuração de proxy está definida como previsto. Isso pode ser feito com o `Get-StorageSyncProxyConfiguration` cmdlet. Obter mais informações sobre como configurar a configuração de proxy para o Azure File Sync pode ser encontrado na [definições de proxy e firewall do Azure File Sync](../articles/storage/files/storage-sync-files-firewall-and-proxy.md#firewall).

    ```powershell
    $agentPath = "C:\Program Files\Azure\StorageSyncAgent"
    Import-Module "$agentPath\StorageSync.Management.ServerCmdlets.dll"
    Get-StorageSyncProxyConfiguration
    ```
    
4. Contacte o administrador de rede para obter assistência adicional, resolução de problemas de conectividade de rede.