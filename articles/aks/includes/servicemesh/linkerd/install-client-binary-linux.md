---
author: paulbouwer
ms.service: container-service
ms.topic: include
ms.date: 10/09/2019
ms.author: pabouwer
ms.openlocfilehash: 1729eabca75ed7c7a3a43ea2c0b1617efd337f2c
ms.sourcegitcommit: f29fec8ec945921cc3a89a6e7086127cc1bc1759
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/17/2019
ms.locfileid: "72530111"
---
## <a name="download-and-install-the-linkerd-linkerd-client-binary"></a>Baixar e instalar o binário do cliente Linkerd Linkerd

Em um shell baseado em bash no [subsistema Linux ou Windows para Linux][install-wsl], use `curl` para baixar a versão Linkerd da seguinte maneira:

```bash
# Specify the Linkerd version that will be leveraged throughout these instructions
LINKERD_VERSION=stable-2.6.0

curl -sLO "https://github.com/linkerd/linkerd2/releases/download/$LINKERD_VERSION/linkerd2-cli-$LINKERD_VERSION-linux"
```

O binário de cliente do `linkerd` é executado no computador cliente e permite que você interaja com a malha do serviço Linkerd. Use os comandos a seguir para instalar o binário do Linkerd `linkerd` Client em um shell baseado em bash no [subsistema Linux ou Windows para Linux][install-wsl]. Esses comandos copiam o binário do `linkerd` cliente para o local do programa de usuário padrão em seu `PATH`.

```bash
sudo cp ./linkerd2-cli-$LINKERD_VERSION-linux /usr/local/bin/linkerd
sudo chmod +x /usr/local/bin/linkerd
```

Se você quiser a conclusão da linha de comando para o binário do cliente do Linkerd `linkerd`, configure-o da seguinte maneira:

```bash
# Generate the bash completion file and source it in your current shell
mkdir -p ~/completions && linkerd completion bash > ~/completions/linkerd.bash
source ~/completions/linkerd.bash

# Source the bash completion file in your .bashrc so that the command-line completions
# are permanently available in your shell
echo "source ~/completions/linkerd.bash" >> ~/.bashrc
```

<!-- LINKS - external -->
[install-wsl]: https://docs.microsoft.com/windows/wsl/install-win10