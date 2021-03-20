---
author: paulbouwer
ms.topic: include
ms.date: 10/02/2020
ms.author: pabouwer
ms.openlocfilehash: 164844a9da09563f8fbefe7ec60aff7eb05ace2d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "91666744"
---
## <a name="download-and-install-the-istio-istioctl-client-binary"></a>Descarregue e instale o binário de cliente istioctl Istio

Numa concha baseada em bash no Linux ou [no Subsistema Windows para Linux,][install-wsl]utilize `curl` para descarregar a versão Istio e, em seguida, extrair com o `tar` seguinte:

```bash
# Specify the Istio version that will be leveraged throughout these instructions
ISTIO_VERSION=1.7.3

curl -sL "https://github.com/istio/istio/releases/download/$ISTIO_VERSION/istioctl-$ISTIO_VERSION-linux-amd64.tar.gz" | tar xz
```

O `istioctl` binário cliente funciona na sua máquina de cliente e permite-lhe instalar e gerir a Istio no seu cluster AKS. Utilize os seguintes comandos para instalar o binário cliente Istio `istioctl` numa concha baseada em bash no Linux ou no Windows [Subsistema para Linux][install-wsl]. Estes comandos copiam o binário do `istioctl` cliente para a localização padrão do programa de utilizador no seu `PATH` .

```bash
sudo mv ./istioctl /usr/local/bin/istioctl
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
[install-wsl]: /windows/wsl/install-win10
