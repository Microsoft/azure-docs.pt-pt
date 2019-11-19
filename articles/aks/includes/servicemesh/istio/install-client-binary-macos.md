---
author: paulbouwer
ms.service: container-service
ms.topic: include
ms.date: 11/15/2019
ms.author: pabouwer
ms.openlocfilehash: d812baa15380b7824deaed081d2857fbc6e0b1b6
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/19/2019
ms.locfileid: "74170849"
---
## <a name="download-and-install-the-istio-istioctl-client-binary"></a>Baixar e instalar o binário do cliente İSTİO istioctl

Em um shell baseado em bash no MacOS, use `curl` para baixar a versão İSTİO e, em seguida, extrair com `tar` da seguinte maneira:

```bash
# Specify the Istio version that will be leveraged throughout these instructions
ISTIO_VERSION=1.4.0

curl -sL "https://github.com/istio/istio/releases/download/$ISTIO_VERSION/istio-$ISTIO_VERSION-osx.tar.gz" | tar xz
```

O binário de cliente do `istioctl` é executado no computador cliente e permite que você interaja com a malha do serviço İSTİO. Use os comandos a seguir para instalar o binário do İSTİO `istioctl` Client em um shell baseado em bash no MacOS. Esses comandos copiam o binário do `istioctl` cliente para o local do programa de usuário padrão em seu `PATH`.

```bash
cd istio-$ISTIO_VERSION
sudo cp ./bin/istioctl /usr/local/bin/istioctl
sudo chmod +x /usr/local/bin/istioctl
```

Se você quiser a conclusão da linha de comando para o binário do cliente do İSTİO `istioctl`, configure-o da seguinte maneira:

```bash
# Generate the bash completion file and source it in your current shell
mkdir -p ~/completions && istioctl collateral --bash -o ~/completions
source ~/completions/istioctl.bash

# Source the bash completion file in your .bashrc so that the command-line completions
# are permanently available in your shell
echo "source ~/completions/istioctl.bash" >> ~/.bashrc
```