---
title: Conectar e gerenciar Microsoft Azure dispositivo de Data Box Edge por meio da interface do Windows PowerShell | Microsoft Docs
description: Descreve como se conectar ao e gerenciar o Data Box Edge por meio da interface do Windows PowerShell.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 06/25/2019
ms.author: alkohli
ms.openlocfilehash: f49396331a31f7ca9eaf453dc8bf6880da2e0da8
ms.sourcegitcommit: 003e73f8eea1e3e9df248d55c65348779c79b1d6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/02/2020
ms.locfileid: "75613861"
---
# <a name="manage-an-azure-data-box-edge-device-via-windows-powershell"></a>Gerenciar um dispositivo Azure Data Box Edge por meio do Windows PowerShell

Azure Data Box Edge solução permite processar dados e enviá-los pela rede para o Azure. Este artigo descreve algumas das tarefas de configuração e gerenciamento para seu dispositivo Data Box Edge. Você pode usar o portal do Azure, a interface do usuário da Web local ou a interface do Windows PowerShell para gerenciar seu dispositivo.

Este artigo se concentra nas tarefas que você faz usando a interface do PowerShell.

Este artigo inclui os seguintes procedimentos:

- Conectar-se à interface do PowerShell
- Criar um pacote de suporte
- Carregar certificado
- Redefinir o dispositivo
- Exibir informações do dispositivo
- Obter logs de computação
- Monitorar e solucionar problemas de módulos de computação

## <a name="connect-to-the-powershell-interface"></a>Conectar-se à interface do PowerShell

[!INCLUDE [Connect to admin runspace](../../includes/data-box-edge-gateway-connect-minishell.md)]

## <a name="create-a-support-package"></a>Criar um pacote de suporte

[!INCLUDE [Create a support package](../../includes/data-box-edge-gateway-create-support-package.md)]

## <a name="upload-certificate"></a>Carregar certificado

[!INCLUDE [Upload certificate](../../includes/data-box-edge-gateway-upload-certificate.md)]

Você também pode carregar IoT Edge certificados para habilitar uma conexão segura entre o dispositivo IoT Edge e os dispositivos downstream que podem se conectar a ele. Há três certificados de IoT Edge (formato *. pem* ) que você precisa instalar:

- Certificado de autoridade de certificação raiz ou autoridade de certificação do proprietário
- Certificado de acesso condicional de dispositivo
- Certificado de chave do dispositivo

O exemplo a seguir mostra o uso desse cmdlet para instalar IoT Edge certificados:

```
Set-HcsCertificate -Scope IotEdge -RootCACertificateFilePath "\\hcfs\root-ca-cert.pem" -DeviceCertificateFilePath "\\hcfs\device-ca-cert.pem\" -DeviceKeyFilePath "\\hcfs\device-key-cert.pem" -Credential "username"
```
Ao executar esse cmdlet, você será solicitado a fornecer a senha para o compartilhamento de rede.

Para obter mais informações sobre certificados, acesse [Azure IOT Edge certificados](https://docs.microsoft.com/azure/iot-edge/iot-edge-certs) ou [instalar certificados em um gateway](https://docs.microsoft.com/azure/iot-edge/how-to-create-transparent-gateway).

## <a name="view-device-information"></a>Exibir informações do dispositivo
 
[!INCLUDE [View device information](../../includes/data-box-edge-gateway-view-device-info.md)]

## <a name="reset-your-device"></a>Repor o dispositivo

[!INCLUDE [Reset your device](../../includes/data-box-edge-gateway-deactivate-device.md)]

## <a name="get-compute-logs"></a>Obter logs de computação

Se a função de computação estiver configurada em seu dispositivo, você também poderá obter os logs de computação por meio da interface do PowerShell.

1. [Conecte-se à interface do PowerShell](#connect-to-the-powershell-interface).
2. Use o `Get-AzureDataBoxEdgeComputeRoleLogs` para obter os logs de computação do seu dispositivo.

    O exemplo a seguir mostra o uso desse cmdlet:

    ```powershell
    Get-AzureDataBoxEdgeComputeRoleLogs -Path "\\hcsfs\logs\myacct" -Credential "username" -FullLogCollection
    ```

    Aqui está uma descrição dos parâmetros usados para o cmdlet:
    - `Path`: forneça um caminho de rede para o compartilhamento no qual você deseja criar o pacote de log de computação.
    - `Credential`: forneça o nome de usuário para o compartilhamento de rede. Quando você executar esse cmdlet, será necessário fornecer a senha de compartilhamento.
    - `FullLogCollection`: esse parâmetro garante que o pacote de log conterá todos os logs de computação. Por padrão, o pacote de log contém apenas um subconjunto de logs.

## <a name="monitor-and-troubleshoot-compute-modules"></a>Monitorar e solucionar problemas de módulos de computação

[!INCLUDE [Monitor and troubleshoot compute modules](../../includes/data-box-edge-monitor-troubleshoot-compute.md)]

## <a name="exit-the-remote-session"></a>Sair da sessão remota

Para sair da sessão remota do PowerShell, feche a janela do PowerShell.

## <a name="next-steps"></a>Passos seguintes

- Implemente o [Azure Data Box Edge](data-box-edge-deploy-prep.md) no portal do Azure.
