---
title: Conecte-se e gere o dispositivo Microsoft Azure Data Box Edge através da interface Windows PowerShell [ Microsoft Docs
description: Descreve como se conectar e, em seguida, gerir o Data Box Edge através da interface Windows PowerShell.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 06/25/2019
ms.author: alkohli
ms.openlocfilehash: f49396331a31f7ca9eaf453dc8bf6880da2e0da8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79265483"
---
# <a name="manage-an-azure-data-box-edge-device-via-windows-powershell"></a>Gerencie um dispositivo Azure Data Box Edge via Windows PowerShell

A solução Azure Data Box Edge permite processar dados e enviá-lo pela rede para o Azure. Este artigo descreve algumas das tarefas de configuração e gestão do seu dispositivo Data Box Edge. Pode utilizar o portal Azure, a Web UI local ou a interface Windows PowerShell para gerir o seu dispositivo.

Este artigo centra-se nas tarefas que faz utilizando a interface PowerShell.

Este artigo inclui os seguintes procedimentos:

- Ligue-se à interface PowerShell
- Criar um pacote de apoio
- Carregar certificado
- Redefinir o dispositivo
- Ver informações do dispositivo
- Obter registos de cálculo
- Módulos de computação monitor e resolução de problemas

## <a name="connect-to-the-powershell-interface"></a>Ligue-se à interface PowerShell

[!INCLUDE [Connect to admin runspace](../../includes/data-box-edge-gateway-connect-minishell.md)]

## <a name="create-a-support-package"></a>Criar um pacote de apoio

[!INCLUDE [Create a support package](../../includes/data-box-edge-gateway-create-support-package.md)]

## <a name="upload-certificate"></a>Carregar certificado

[!INCLUDE [Upload certificate](../../includes/data-box-edge-gateway-upload-certificate.md)]

Também pode carregar certificados IoT Edge para permitir uma ligação segura entre o seu dispositivo IoT Edge e os dispositivos a jusante que possam ligar-se ao mesmo. Existem três certificados IoT Edge (formato *.pem)* que precisa de instalar:

- Certificado de CA raiz ou o proprietário CA
- Certificado CA do dispositivo
- Certificado de chave do dispositivo

O exemplo que se segue mostra a utilização deste cmdlet para instalar certificados IoT Edge:

```
Set-HcsCertificate -Scope IotEdge -RootCACertificateFilePath "\\hcfs\root-ca-cert.pem" -DeviceCertificateFilePath "\\hcfs\device-ca-cert.pem\" -DeviceKeyFilePath "\\hcfs\device-key-cert.pem" -Credential "username"
```
Quando executar este cmdlet, será solicitado a fornecer a palavra-passe para a partilha da rede.

Para obter mais informações sobre certificados, vá aos [certificados Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/iot-edge-certs) ou [instale certificados numa porta de entrada](https://docs.microsoft.com/azure/iot-edge/how-to-create-transparent-gateway).

## <a name="view-device-information"></a>Ver informações do dispositivo
 
[!INCLUDE [View device information](../../includes/data-box-edge-gateway-view-device-info.md)]

## <a name="reset-your-device"></a>Repor o dispositivo

[!INCLUDE [Reset your device](../../includes/data-box-edge-gateway-deactivate-device.md)]

## <a name="get-compute-logs"></a>Obter registos de cálculo

Se a função computacional estiver configurada no seu dispositivo, também pode obter os registos de cálculo através da interface PowerShell.

1. [Ligue-se à interface PowerShell](#connect-to-the-powershell-interface).
2. Utilize `Get-AzureDataBoxEdgeComputeRoleLogs` os registos de cálculo para obter os registos de cálculo para o seu dispositivo.

    O exemplo que se segue mostra a utilização deste cmdlet:

    ```powershell
    Get-AzureDataBoxEdgeComputeRoleLogs -Path "\\hcsfs\logs\myacct" -Credential "username" -FullLogCollection
    ```

    Aqui está uma descrição dos parâmetros utilizados para o cmdlet:
    - `Path`: Forneça um caminho de rede para a parte onde pretende criar o pacote de registo de cálculo.
    - `Credential`: Forneça o nome de utilizador para a parte da rede. Quando executar este cmdlet, terá de fornecer a senha de partilha.
    - `FullLogCollection`: Este parâmetro garante que a embalagem de registo conterá todos os registos de cálculo. Por predefinição, o pacote de registo contém apenas um subconjunto de registos.

## <a name="monitor-and-troubleshoot-compute-modules"></a>Módulos de computação monitor e resolução de problemas

[!INCLUDE [Monitor and troubleshoot compute modules](../../includes/data-box-edge-monitor-troubleshoot-compute.md)]

## <a name="exit-the-remote-session"></a>Saia da sessão remota

Para sair da sessão remota PowerShell, feche a janela PowerShell.

## <a name="next-steps"></a>Passos seguintes

- Implemente o [Azure Data Box Edge](data-box-edge-deploy-prep.md) no portal do Azure.
