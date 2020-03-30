---
author: paulbouwer
ms.topic: include
ms.date: 10/09/2019
ms.author: pabouwer
ms.openlocfilehash: b7d832ba375925d30a976dfde63a776b5d0742bb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77593741"
---
## <a name="download-and-install-the-linkerd-linkerd-client-binary"></a>Descarregue e instale o linkerd linkerd client binary

Numa concha baseada no PowerShell `Invoke-WebRequest` no Windows, utilize para descarregar o lançamento do Linkerd da seguinte forma:

```powershell
# Specify the Linkerd version that will be leveraged throughout these instructions
$LINKERD_VERSION="stable-2.6.0"

# Enforce TLS 1.2
[Net.ServicePointManager]::SecurityProtocol = "tls12"
$ProgressPreference = 'SilentlyContinue'; Invoke-WebRequest -URI "https://github.com/linkerd/linkerd2/releases/download/$LINKERD_VERSION/linkerd2-cli-$LINKERD_VERSION-windows.exe" -OutFile "linkerd2-cli-$LINKERD_VERSION-windows.exe"
```

O `linkerd` binário do cliente funciona na sua máquina cliente e permite-lhe interagir com a malha de serviço Linkerd. Utilize os seguintes comandos para `linkerd` instalar o binário do cliente Linkerd numa concha baseada em PowerShell no Windows. Estes comandos `linkerd` copiam o binário do cliente para uma pasta Linkerd e, em seguida, disponibilizam-no `PATH`imediatamente (na concha atual) e permanentemente (através da casca reinicia) através da sua . Não precisa de privilégios elevados (administradores) para executar estes comandos e não precisa reiniciar a sua concha.

```powershell
# Copy linkerd.exe to C:\Linkerd
New-Item -ItemType Directory -Force -Path "C:\Linkerd"
Copy-Item -Path ".\linkerd2-cli-$LINKERD_VERSION-windows.exe" -Destination "C:\Linkerd\linkerd.exe"

# Add C:\Linkerd to PATH. 
# Make the new PATH permanently available for the current User, and also immediately available in the current shell.
$PATH = [environment]::GetEnvironmentVariable("PATH", "User") + "; C:\Linkerd\"
[environment]::SetEnvironmentVariable("PATH", $PATH, "User") 
[environment]::SetEnvironmentVariable("PATH", $PATH)
```