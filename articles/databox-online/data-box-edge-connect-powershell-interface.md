---
title: Ligar a e gerir o dispositivo de limite de caixa de dados do Microsoft Azure através da interface do Windows PowerShell | Documentos da Microsoft
description: Descreve como ligar a e, em seguida, gerir dados caixa Edge através da interface do Windows PowerShell.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 04/25/2019
ms.author: alkohli
ms.openlocfilehash: 8cd89b21e80662ec50746e0c7721a5544cfbce30
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2019
ms.locfileid: "64717496"
---
# <a name="manage-an-azure-data-box-edge-device-via-windows-powershell"></a>Gerir um dispositivo de limite de caixa de dados do Azure através do Windows PowerShell

Solução de borda de caixa de dados do Azure permite-lhe processar dados e enviá-lo através da rede para o Azure. Este artigo descreve algumas das tarefas de configuração e gestão para o seu dispositivo do Edge de caixa de dados. Pode utilizar o portal do Azure, da IU web local ou a interface do Windows PowerShell para gerir o seu dispositivo.

Este artigo se concentra nas tarefas que fizer através da interface do PowerShell.

Este artigo inclui os seguintes procedimentos:

- Ligar a interface do PowerShell
- Criar um pacote de suporte
- Carregar certificado
- Repor o dispositivo
- Ver informações de dispositivo
- Obter registos de computação
- Monitorizar e resolver problemas de módulos de computação

## <a name="connect-to-the-powershell-interface"></a>Ligar a interface do PowerShell

[!INCLUDE [Connect to admin runspace](../../includes/data-box-edge-gateway-connect-minishell.md)]

## <a name="create-a-support-package"></a>Criar um pacote de suporte

[!INCLUDE [Create a support package](../../includes/data-box-edge-gateway-create-support-package.md)]

## <a name="upload-certificate"></a>Carregar certificado

[!INCLUDE [Upload certificate](../../includes/data-box-edge-gateway-upload-certificate.md)]

Também pode carregar certificados de IoT Edge para permitir uma ligação segura entre o seu dispositivo IoT Edge e os dispositivos de downstream que podem se conectar a ele. Existem três certificados de IoT Edge (*. pem* formato) que tem de instalar:

- Certificado de AC de raiz ou o proprietário da AC
- Certificado de acesso condicional de dispositivo
- Certificado de chave do dispositivo

O exemplo seguinte mostra a utilização deste cmdlet para instalar os certificados de IoT Edge:

```
Set-HcsCertificate -Scope IotEdge -RootCACertificateFilePath "\\hcfs\root-ca-cert.pem" -DeviceCertificateFilePath "\\hcfs\device-ca-cert.pem\" -DeviceKeyFilePath "\\hcfs\device-key-cert.pem" -Credential "username/password"
```

Para obter mais informações sobre certificados, aceda a [certificados do Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/iot-edge-certs) ou [instalar certificados num gateway](https://docs.microsoft.com/azure/iot-edge/how-to-create-transparent-gateway#install-certificates-on-the-gateway).

## <a name="view-device-information"></a>Ver informações de dispositivo
 
[!INCLUDE [View device information](../../includes/data-box-edge-gateway-view-device-info.md)]

## <a name="reset-your-device"></a>Repor o seu dispositivo

[!INCLUDE [Reset your device](../../includes/data-box-edge-gateway-deactivate-device.md)]

## <a name="get-compute-logs"></a>Obter registos de computação

Se a função de computação é configurada no seu dispositivo, também pode obter os registos de computação através da interface do PowerShell.

1. [Ligar a interface do PowerShell](#connect-to-the-powershell-interface).
2. Utilize o `Get-AzureDataBoxEdgeComputeRoleLogs` para obter os registos de computação para o seu dispositivo.

    O exemplo seguinte mostra a utilização deste cmdlet:

    ```powershell
    Get-AzureDataBoxEdgeComputeRoleLogs -Path "\\hcsfs\logs\myacct" -Credential "username/password" -RoleInstanceName "IotRole" -FullLogCollection
    ```

    Eis uma descrição dos parâmetros utilizados para o cmdlet:
    - `Path`: Forneça um caminho de rede para a partilha de onde pretende criar o pacote de registo de computação.
    - `Credential`: Forneça o nome de utilizador e palavra-passe para a partilha de rede.
    - `RoleInstanceName`: Fornecer essa cadeia de caracteres `IotRole` para este parâmetro.
    - `FullLogCollection`: Esse parâmetro garante que o pacote de registo irá conter todos os registos de computação. Por predefinição, o pacote de registo contém apenas um subconjunto de registos.

## <a name="monitor-and-troubleshoot-compute-modules"></a>Monitorizar e resolver problemas de módulos de computação

[!INCLUDE [Monitor and troubleshoot compute modules](../../includes/data-box-edge-monitor-troubleshoot-compute.md)]

## <a name="exit-the-remote-session"></a>Saia da sessão remota

Para sair na sessão remota do PowerShell, feche a janela do PowerShell.

## <a name="next-steps"></a>Passos Seguintes

- Implemente o [Azure Data Box Edge](data-box-edge-deploy-prep.md) no portal do Azure.
