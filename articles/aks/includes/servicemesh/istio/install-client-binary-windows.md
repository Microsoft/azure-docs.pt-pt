---
author: paulbouwer
ms.topic: include
ms.date: 11/15/2019
ms.author: pabouwer
ms.openlocfilehash: e26a2c214a03243d6507296c1e981706be8c56db
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "81736078"
---
## <a name="download-and-install-the-istio-istioctl-client-binary"></a>Descarregue e instale o binário de cliente istioctl Istio

Numa concha baseada em PowerShell no Windows, utilize `Invoke-WebRequest` para descarregar a versão Istio e, em seguida, extrair com o `Expand-Archive` seguinte:

```powershell
# Specify the Istio version that will be leveraged throughout these instructions
$ISTIO_VERSION="1.4.0"

# Enforce TLS 1.2
[Net.ServicePointManager]::SecurityProtocol = "tls12"
$ProgressPreference = 'SilentlyContinue'; Invoke-WebRequest -URI "https://github.com/istio/istio/releases/download/$ISTIO_VERSION/istio-$ISTIO_VERSION-win.zip" -OutFile "istio-$ISTIO_VERSION.zip"
Expand-Archive -Path "istio-$ISTIO_VERSION.zip" -DestinationPath .
```

O `istioctl` binário cliente funciona na sua máquina de cliente e permite-lhe interagir com a malha de serviço Istio. Utilize os seguintes comandos para instalar o binário do cliente Istio `istioctl` numa concha baseada em PowerShell no Windows. Estes comandos copiam o binário do `istioctl` cliente para uma pasta Istio e, em seguida, disponibilizam-no imediatamente (na casca atual) e permanentemente (através do recomeço da concha) através do seu `PATH` . Não precisa de privilégios elevados (Administrador) para executar estes comandos e não precisa de reiniciar a sua concha.

```powershell
# Copy istioctl.exe to C:\Istio
cd istio-$ISTIO_VERSION
New-Item -ItemType Directory -Force -Path "C:\Istio"
Copy-Item -Path .\bin\istioctl.exe -Destination "C:\Istio\"

# Add C:\Istio to PATH. 
# Make the new PATH permanently available for the current User
$USER_PATH = [environment]::GetEnvironmentVariable("PATH", "User") + ";C:\Istio\"
[environment]::SetEnvironmentVariable("PATH", $USER_PATH, "User")
# Make the new PATH immediately available in the current shell
$env:PATH += ";C:\Istio\"
```