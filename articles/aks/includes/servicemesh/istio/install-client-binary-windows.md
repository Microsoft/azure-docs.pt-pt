---
author: paulbouwer
ms.topic: include
ms.date: 10/02/2020
ms.author: pabouwer
ms.openlocfilehash: a02c17013a205ccc0da85536b491d467ef72fa48
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "91666730"
---
## <a name="download-and-install-the-istio-istioctl-client-binary"></a>Descarregue e instale o binário de cliente istioctl Istio

Numa concha baseada em PowerShell no Windows, utilize `Invoke-WebRequest` para descarregar a versão Istio e, em seguida, extrair com o `Expand-Archive` seguinte:

```powershell
# Specify the Istio version that will be leveraged throughout these instructions
$ISTIO_VERSION="1.7.3"

[Net.ServicePointManager]::SecurityProtocol = "tls12"
$ProgressPreference = 'SilentlyContinue'; Invoke-WebRequest -URI "https://github.com/istio/istio/releases/download/$ISTIO_VERSION/istioctl-$ISTIO_VERSION-win.zip" -OutFile "istioctl-$ISTIO_VERSION.zip"
Expand-Archive -Path "istioctl-$ISTIO_VERSION.zip" -DestinationPath .
```

O `istioctl` binário cliente funciona na sua máquina de cliente e permite-lhe instalar e gerir a Istio no seu cluster AKS. Utilize os seguintes comandos para instalar o binário do cliente Istio `istioctl` numa concha baseada em PowerShell no Windows. Estes comandos copiam o binário do `istioctl` cliente para uma pasta Istio e, em seguida, disponibilizam-no imediatamente (na casca atual) e permanentemente (através do recomeço da concha) através do seu `PATH` . Não precisa de privilégios elevados (Administrador) para executar estes comandos e não precisa de reiniciar a sua concha.

```powershell
# Copy istioctl.exe to C:\Istio
New-Item -ItemType Directory -Force -Path "C:\Istio"
Move-Item -Path .\istioctl.exe -Destination "C:\Istio\"

# Add C:\Istio to PATH. 
# Make the new PATH permanently available for the current User
$USER_PATH = [environment]::GetEnvironmentVariable("PATH", "User") + ";C:\Istio\"
[environment]::SetEnvironmentVariable("PATH", $USER_PATH, "User")
# Make the new PATH immediately available in the current shell
$env:PATH += ";C:\Istio\"
```