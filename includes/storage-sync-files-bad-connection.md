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
ms.openlocfilehash: 07cae1cee9810646de5bf9610a29991376736373
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/15/2019
ms.locfileid: "72391752"
---
Esse erro pode ocorrer sempre que o serviço de Sincronização de Arquivos do Azure está inacessível do servidor. Você pode solucionar esse erro ao trabalhar com as seguintes etapas:

1. Verifique se o serviço do Windows `FileSyncSvc.exe` não está bloqueado pelo firewall.
2. Verifique se a porta 443 está aberta para conexões de saída para o serviço de Sincronização de Arquivos do Azure. Você pode fazer isso com o cmdlet `Test-NetConnection`. A URL para o espaço reservado `<azure-file-sync-endpoint>` abaixo pode ser encontrada no documento [sincronização de arquivos do Azure proxy e configurações de firewall](../articles/storage/files/storage-sync-files-firewall-and-proxy.md#firewall) . 

    ```powershell
    Test-NetConnection -ComputerName <azure-file-sync-endpoint> -Port 443
    ```

3. Verifique se a configuração de proxy está definida como prevista. Isso pode ser feito com o cmdlet `Get-StorageSyncProxyConfiguration`. Mais informações sobre como configurar a configuração de proxy para Sincronização de Arquivos do Azure podem ser encontradas nas [configurações de proxy e firewall do sincronização de arquivos do Azure](../articles/storage/files/storage-sync-files-firewall-and-proxy.md#firewall).

    ```powershell
    $agentPath = "C:\Program Files\Azure\StorageSyncAgent"
    Import-Module "$agentPath\StorageSync.Management.ServerCmdlets.dll"
    Get-StorageSyncProxyConfiguration
    ```
    
4. Contate o administrador de rede para obter assistência adicional Solucionando problemas de conectividade de rede.