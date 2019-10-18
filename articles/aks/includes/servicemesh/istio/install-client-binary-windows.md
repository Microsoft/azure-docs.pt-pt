---
author: paulbouwer
ms.service: container-service
ms.topic: include
ms.date: 10/09/2019
ms.author: pabouwer
ms.openlocfilehash: 1c63d02a2207a396183be4fdcc44074977d37b1a
ms.sourcegitcommit: f29fec8ec945921cc3a89a6e7086127cc1bc1759
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/17/2019
ms.locfileid: "72530371"
---
## <a name="download-and-install-the-istio-istioctl-client-binary"></a>Baixar e instalar o binário do cliente İSTİO istioctl

Em um shell baseado no PowerShell no Windows, use `Invoke-WebRequest` para baixar a versão İSTİO e, em seguida, extrair com `Expand-Archive` da seguinte maneira:

```powershell
# Specify the Istio version that will be leveraged throughout these instructions
$ISTIO_VERSION="1.3.2"

# Enforce TLS 1.2
[Net.ServicePointManager]::SecurityProtocol = "tls12"
$ProgressPreference = 'SilentlyContinue'; Invoke-WebRequest -URI "https://github.com/istio/istio/releases/download/$ISTIO_VERSION/istio-$ISTIO_VERSION-win.zip" -OutFile "istio-$ISTIO_VERSION.zip"
Expand-Archive -Path "istio-$ISTIO_VERSION.zip" -DestinationPath .
```

O binário de cliente do `istioctl` é executado no computador cliente e permite que você interaja com a malha do serviço İSTİO. Use os comandos a seguir para instalar o binário do İSTİO `istioctl` Client em um shell baseado no PowerShell no Windows. Esses comandos copiam o `istioctl` binário do cliente para uma pasta İSTİO e, em seguida, disponibilizam-os imediatamente (no shell atual) e permanentemente (através de reinicializações do Shell) por meio de seu `PATH`. Você não precisa de privilégios elevados (administrador) para executar esses comandos e não precisa reiniciar o Shell.

```powershell
# Copy istioctl.exe to C:\Istio
cd istio-$ISTIO_VERSION
New-Item -ItemType Directory -Force -Path "C:\Istio"
Copy-Item -Path .\bin\istioctl.exe -Destination "C:\Istio\"

# Add C:\Istio to PATH. 
# Make the new PATH permanently available for the current User, and also immediately available in the current shell.
$PATH = [environment]::GetEnvironmentVariable("PATH", "User") + "; C:\Istio\"
[environment]::SetEnvironmentVariable("PATH", $PATH, "User") 
[environment]::SetEnvironmentVariable("PATH", $PATH)
```