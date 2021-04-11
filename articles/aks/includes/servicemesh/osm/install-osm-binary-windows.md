---
author: phillipgibson
ms.topic: include
ms.date: 03/15/2021
ms.author: pgibson
ms.openlocfilehash: 8ac70027f7483fbf0131c31a5ba3631ed1d4ff9a
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/31/2021
ms.locfileid: "106080376"
---
## <a name="download-and-install-the-osm-client-binary"></a>Descarregue e instale o binário de clientes OSM

Numa concha baseada em PowerShell no Windows, utilize `Invoke-WebRequest` para descarregar a versão Istio e, em seguida, extrair com o `Expand-Archive` seguinte:

```powershell
# Specify the OSM version that will be leveraged throughout these instructions
$OSM_VERSION="v0.8.2"

[Net.ServicePointManager]::SecurityProtocol = "tls12"
$ProgressPreference = 'SilentlyContinue'; Invoke-WebRequest -URI "https://github.com/openservicemesh/osm/releases/download/$OSM_VERSION/osm-$OSM_VERSION-windows-amd64.zip&quot; -OutFile &quot;osm-$OSM_VERSION.zip"
Expand-Archive -Path "osm-$OSM_VERSION.zip" -DestinationPath .
```

O `osm` binário do cliente funciona na sua máquina de cliente e permite-lhe gerir o controlador OSM no seu cluster AKS. Utilize os seguintes comandos para instalar o binário do cliente OSM `osm` numa concha baseada em PowerShell no Windows. Estes comandos copiam o binário do `osm` cliente para uma pasta OSM e, em seguida, disponibilizam-no imediatamente (na casca atual) e permanentemente (através do recomeço da concha) através do seu `PATH` . Não precisa de privilégios elevados (Administrador) para executar estes comandos e não precisa de reiniciar a sua concha.

```powershell
# Copy osm.exe to C:\OSM
New-Item -ItemType Directory -Force -Path "C:\OSM"
Move-Item -Path .\windows-amd64\osm.exe -Destination "C:\OSM\"

# Add C:\OSM to PATH.
# Make the new PATH permanently available for the current User
$USER_PATH = [environment]::GetEnvironmentVariable("PATH&quot;, &quot;User&quot;) + &quot;;C:\OSM\"
[environment]::SetEnvironmentVariable("PATH", $USER_PATH, "User")
# Make the new PATH immediately available in the current shell
$env:PATH += ";C:\OSM\"
```
