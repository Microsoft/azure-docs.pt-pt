---
author: paulbouwer
ms.topic: include
ms.date: 11/15/2019
ms.author: pabouwer
ms.openlocfilehash: b310de560f9791e1fc49d54dfbf0789c38d37f57
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "77594018"
---
## <a name="download-and-install-the-istio-istioctl-client-binary"></a>Descarregue e instale o binário de cliente istioctl Istio

Numa concha baseada em bash no Linux ou [no Subsistema Windows para Linux,][install-wsl]utilize `curl` para descarregar a versão Istio e, em seguida, extrair com o `tar` seguinte:

```bash
# Specify the Istio version that will be leveraged throughout these instructions
ISTIO_VERSION=1.4.0

curl -sL "https://github.com/istio/istio/releases/download/$ISTIO_VERSION/istio-$ISTIO_VERSION-linux.tar.gz" | tar xz
```

O `istioctl` binário cliente funciona na sua máquina de cliente e permite-lhe interagir com a malha de serviço Istio. Utilize os seguintes comandos para instalar o binário cliente Istio `istioctl` numa concha baseada em bash no Linux ou no Windows [Subsistema para Linux][install-wsl]. Estes comandos copiam o binário do `istioctl` cliente para a localização padrão do programa de utilizador no seu `PATH` .

```bash
cd istio-$ISTIO_VERSION
sudo cp ./bin/istioctl /usr/local/bin/istioctl
sudo chmod +x /usr/local/bin/istioctl
```

Se quiser a conclusão da linha de comando para o binário cliente Istio, `istioctl` então conehe-o da seguinte forma:

```bash
# Generate the bash completion file and source it in your current shell
mkdir -p ~/completions && istioctl collateral --bash -o ~/completions
source ~/completions/istioctl.bash

# Source the bash completion file in your .bashrc so that the command-line completions
# are permanently available in your shell
echo "source ~/completions/istioctl.bash" >> ~/.bashrc
```

<!-- LINKS - external -->
[install-wsl]: https://docs.microsoft.com/windows/wsl/install-win10