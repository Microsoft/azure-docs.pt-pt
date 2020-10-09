---
author: paulbouwer
ms.topic: include
ms.date: 10/09/2019
ms.author: pabouwer
ms.openlocfilehash: 1a023475de1ce2891916807632d9ee15e382326c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "81734101"
---
## <a name="download-and-install-the-linkerd-linkerd-client-binary"></a>Descarregue e instale o binário de cliente linkerd Linkerd

Numa concha baseada no PowerShell no Windows, utilize `Invoke-WebRequest` para descarregar a versão Linkerd da seguinte forma:

```powershell
# Specify the Linkerd version that will be leveraged throughout these instructions
$LINKERD_VERSION="stable-2.6.0"

# Enforce TLS 1.2
[Net.ServicePointManager]::SecurityProtocol = "tls12"
$ProgressPreference = 'SilentlyContinue'; Invoke-WebRequest -URI "https://github.com/linkerd/linkerd2/releases/download/$LINKERD_VERSION/linkerd2-cli-$LINKERD_VERSION-windows.exe" -OutFile "linkerd2-cli-$LINKERD_VERSION-windows.exe"
```

O `linkerd` binário cliente funciona na sua máquina de cliente e permite-lhe interagir com a malha de serviço Linkerd. Utilize os seguintes comandos para instalar o binário do cliente Linkerd `linkerd` numa concha baseada em PowerShell no Windows. Estes comandos copiam o binário do `linkerd` cliente para uma pasta Linkerd e, em seguida, disponibilizam-no imediatamente (na casca atual) e permanentemente (através do recomeço da concha) através do seu `PATH` . Não precisa de privilégios elevados (Administrador) para executar estes comandos e não precisa de reiniciar a sua concha.

```powershell
# Copy linkerd.exe to C:\Linkerd
New-Item -ItemType Directory -Force -Path "C:\Linkerd"
Copy-Item -Path ".\linkerd2-cli-$LINKERD_VERSION-windows.exe" -Destination "C:\Linkerd\linkerd.exe"

# Add C:\Linkerd to PATH. 
# Make the new PATH permanently available for the current User
$USER_PATH = [environment]::GetEnvironmentVariable("PATH", "User") + ";C:\Linkerd\"
[environment]::SetEnvironmentVariable("PATH", $USER_PATH, "User")
# Make the new PATH immediately available in the current shell
$env:PATH += ";C:\Linkerd\"
```