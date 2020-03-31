---
author: paulbouwer
ms.topic: include
ms.date: 11/15/2019
ms.author: pabouwer
ms.openlocfilehash: 74f5b22ccc822a188059b29d9c661a15cf8412bf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77594021"
---
## <a name="download-and-install-the-istio-istioctl-client-binary"></a>Descarregue e instale o binário do cliente istioctl istioctl

Numa concha baseada em bash no `curl` MacOS, use para descarregar `tar` o lançamento istio e, em seguida, extrair com o seguinte:

```bash
# Specify the Istio version that will be leveraged throughout these instructions
ISTIO_VERSION=1.4.0

curl -sL "https://github.com/istio/istio/releases/download/$ISTIO_VERSION/istio-$ISTIO_VERSION-osx.tar.gz" | tar xz
```

O `istioctl` binário do cliente funciona na sua máquina cliente e permite-lhe interagir com a malha de serviço Istio. Utilize os seguintes comandos `istioctl` para instalar o binário do cliente Istio numa concha baseada em golpes no MacOS. Estes comandos `istioctl` copiam o binário do cliente `PATH`para a localização padrão do programa de utilizador na sua .

```bash
cd istio-$ISTIO_VERSION
sudo cp ./bin/istioctl /usr/local/bin/istioctl
sudo chmod +x /usr/local/bin/istioctl
```

Se quiser a conclusão da linha `istioctl` de comando para o binário do cliente Istio, então instale-a da seguinte forma:

```bash
# Generate the bash completion file and source it in your current shell
mkdir -p ~/completions && istioctl collateral --bash -o ~/completions
source ~/completions/istioctl.bash

# Source the bash completion file in your .bashrc so that the command-line completions
# are permanently available in your shell
echo "source ~/completions/istioctl.bash" >> ~/.bashrc
```