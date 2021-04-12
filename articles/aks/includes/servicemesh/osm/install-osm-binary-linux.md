---
author: phillipgibson
ms.topic: include
ms.date: 03/15/2021
ms.author: phillipgibson
ms.openlocfilehash: f99db628b08084ca750816c00a6892e877e90efc
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/31/2021
ms.locfileid: "106080373"
---
## <a name="download-and-install-the-osm-client-binary"></a>Descarregue e instale o binário de clientes OSM

Numa concha baseada em bash no Linux ou [no Subsistema Windows para Linux,][install-wsl]utilize `curl` para descarregar a versão OSM e, em seguida, extrair com o `tar` seguinte:

```bash
# Specify the OSM version that will be leveraged throughout these instructions
OSM_VERSION=v0.8.2

curl -sL "https://github.com/openservicemesh/osm/releases/download/$OSM_VERSION/osm-$OSM_VERSION-linux-amd64.tar.gz" | tar -vxzf -
```

O `osm` binário cliente funciona na sua máquina de cliente e permite-lhe gerir a OSM no seu cluster AKS. Utilize os seguintes comandos para instalar o binário do cliente OSM `osm` numa concha baseada em bash no Linux ou no Windows [Subsistema para Linux][install-wsl]. Estes comandos copiam o binário do `osm` cliente para a localização padrão do programa de utilizador no seu `PATH` .

```bash
sudo mv ./linux-amd64/osm /usr/local/bin/osm
sudo chmod +x /usr/local/bin/osm
```

Pode verificar se a biblioteca do `osm` cliente foi corretamente adicionada ao seu caminho e ao seu número de versão com o seguinte comando.

```
osm version
```

<!-- LINKS - external -->

[install-wsl]: /windows/wsl/install-win10
