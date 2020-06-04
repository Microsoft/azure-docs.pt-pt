---
title: Conecte-se e gere o dispositivo Microsoft Azure Stack Edge através da interface Windows PowerShell Microsoft Docs
description: Descreve como ligar e, em seguida, gerir o Azure Stack Edge através da interface Windows PowerShell.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 06/25/2019
ms.author: alkohli
ms.openlocfilehash: 445e86f1979d52496052572c1682e0b894d512dd
ms.sourcegitcommit: 61d850bc7f01c6fafee85bda726d89ab2ee733ce
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/03/2020
ms.locfileid: "84339880"
---
# <a name="manage-an-azure-stack-edge-device-via-windows-powershell"></a>Gerir um dispositivo Azure Stack Edge via Windows PowerShell

A solução Azure Stack Edge permite-lhe processar dados e enviá-lo pela rede para a Azure. Este artigo descreve algumas das tarefas de configuração e gestão do seu dispositivo Azure Stack Edge. Pode utilizar o portal Azure, a web UI local ou a interface Windows PowerShell para gerir o seu dispositivo.

Este artigo centra-se nas tarefas que faz utilizando a interface PowerShell. 

Este artigo inclui os seguintes procedimentos:

- Ligue-se à interface PowerShell
- Criar um pacote de suporte
- Carregar certificado
- Reiniciar o dispositivo
- Ver informações do dispositivo
- Obtenha registos de cálculo
- Monitor e resolução de problemas módulos de computação

## <a name="connect-to-the-powershell-interface"></a>Ligue-se à interface PowerShell

[!INCLUDE [Connect to admin runspace](../../includes/data-box-edge-gateway-connect-minishell.md)]

## <a name="create-a-support-package"></a>Criar um pacote de suporte

[!INCLUDE [Create a support package](../../includes/data-box-edge-gateway-create-support-package.md)]

## <a name="upload-certificate"></a>Carregar certificado

[!INCLUDE [Upload certificate](../../includes/data-box-edge-gateway-upload-certificate.md)]

Também pode carregar certificados IoT Edge para permitir uma ligação segura entre o seu dispositivo IoT Edge e os dispositivos a jusante que podem ligar-se ao mesmo. Existem três certificados IoT Edge (formato *.pem)* que precisa de instalar:

- Certificado de CA raiz ou o proprietário CA
- Certificado ca dispositivo
- Certificado chave do dispositivo

O exemplo a seguir mostra a utilização deste cmdlet para instalar certificados IoT Edge:

```
Set-HcsCertificate -Scope IotEdge -RootCACertificateFilePath "\\hcfs\root-ca-cert.pem" -DeviceCertificateFilePath "\\hcfs\device-ca-cert.pem\" -DeviceKeyFilePath "\\hcfs\device-key-cert.pem" -Credential "username"
```
Quando executar este cmdlet, será solicitado que forneça a palavra-passe para a partilha da rede.

Para obter mais informações sobre certificados, aceda aos [certificados Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/iot-edge-certs) ou [instale certificados num gateway](https://docs.microsoft.com/azure/iot-edge/how-to-create-transparent-gateway).

## <a name="view-device-information"></a>Ver informações do dispositivo
 
[!INCLUDE [View device information](../../includes/data-box-edge-gateway-view-device-info.md)]

## <a name="reset-your-device"></a>Repor o dispositivo

[!INCLUDE [Reset your device](../../includes/data-box-edge-gateway-deactivate-device.md)]

## <a name="get-compute-logs"></a>Obtenha registos de cálculo

Se a função de cálculo estiver configurada no seu dispositivo, também pode obter os registos computacional através da interface PowerShell.

1. [Ligue-se à interface PowerShell](#connect-to-the-powershell-interface).
2. Utilize o `Get-AzureDataBoxEdgeComputeRoleLogs` para obter os registos computacional do seu dispositivo.

    O exemplo a seguir mostra a utilização deste cmdlet:

    ```powershell
    Get-AzureDataBoxEdgeComputeRoleLogs -Path "\\hcsfs\logs\myacct" -Credential "username" -FullLogCollection
    ```

    Aqui está uma descrição dos parâmetros utilizados para o cmdlet:
    - `Path`: Providenciar um caminho de rede para a partilha onde pretende criar o pacote de registo de cálculo.
    - `Credential`: Forneça o nome de utilizador para a partilha da rede. Quando executar este cmdlet, terá de fornecer a senha de partilha.
    - `FullLogCollection`: Este parâmetro garante que o pacote de registos conterá todos os registos computatórios. Por predefinição, o pacote de registo contém apenas um subconjunto de registos.

## <a name="monitor-and-troubleshoot-compute-modules"></a>Monitor e resolução de problemas módulos de computação

[!INCLUDE [Monitor and troubleshoot compute modules](../../includes/data-box-edge-monitor-troubleshoot-compute.md)]

## <a name="exit-the-remote-session"></a>Sair da sessão remota

Para sair da sessão remota powerShell, feche a janela PowerShell.

## <a name="next-steps"></a>Próximos passos

- Implementar [Azure Stack Edge](azure-stack-edge-deploy-prep.md) no portal Azure.
