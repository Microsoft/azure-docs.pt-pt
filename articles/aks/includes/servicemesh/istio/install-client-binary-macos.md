---
author: paulbouwer
ms.topic: include
ms.date: 11/15/2019
ms.author: pabouwer
ms.openlocfilehash: 74f5b22ccc822a188059b29d9c661a15cf8412bf
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/25/2020
ms.locfileid: "77594021"
---
## <a name="download-and-install-the-istio-istioctl-client-binary"></a>Descarregue e instale o binário do cliente istioctl istioctl

Numa concha baseada em bash no MacOS, use `curl` para descarregar o lançamento istio e, em seguida, extrair com `tar` seguinte:

```bash
# Specify the Istio version that will be leveraged throughout these instructions
ISTIO_VERSION=1.4.0

curl -sL "https://github.com/istio/istio/releases/download/$ISTIO_VERSION/istio-$ISTIO_VERSION-osx.tar.gz" | tar xz
```

O `istioctl` binário de cliente funciona na sua máquina cliente e permite-lhe interagir com a malha de serviço Istio. Utilize os seguintes comandos para instalar o istio `istioctl` binário de cliente numa concha baseada em golpes no MacOS. Estes comandos copiam o `istioctl` binário do cliente para a localização padrão do programa de utilizador na sua `PATH`.

```bash
cd istio-$ISTIO_VERSION
sudo cp ./bin/istioctl /usr/local/bin/istioctl
sudo chmod +x /usr/local/bin/istioctl
```

Se quiser a conclusão da linha de comando para o istio `istioctl` binário de cliente, então configura-o da seguinte forma:

```bash
# Generate the bash completion file and source it in your current shell
mkdir -p ~/completions && istioctl collateral --bash -o ~/completions
source ~/completions/istioctl.bash

# Source the bash completion file in your .bashrc so that the command-line completions
# are permanently available in your shell
echo "source ~/completions/istioctl.bash" >> ~/.bashrc
```