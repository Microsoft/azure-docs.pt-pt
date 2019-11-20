---
author: paulbouwer
ms.service: container-service
ms.topic: include
ms.date: 10/09/2019
ms.author: pabouwer
ms.openlocfilehash: a4090172e926f21db01a8e374d8c4bb1c80402c7
ms.sourcegitcommit: 8e31a82c6da2ee8dafa58ea58ca4a7dd3ceb6132
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/19/2019
ms.locfileid: "74197373"
---
## <a name="download-and-install-the-linkerd-linkerd-client-binary"></a>Baixar e instalar o binário do cliente Linkerd Linkerd

Em um shell baseado em bash no MacOS, use `curl` para baixar a versão Linkerd da seguinte maneira:

```bash
# Specify the Linkerd version that will be leveraged throughout these instructions
LINKERD_VERSION=stable-2.6.0

curl -sLO "https://github.com/linkerd/linkerd2/releases/download/$LINKERD_VERSION/linkerd2-cli-$LINKERD_VERSION-darwin"
```

O binário de cliente do `linkerd` é executado no computador cliente e permite que você interaja com a malha do serviço Linkerd. Use os comandos a seguir para instalar o binário do Linkerd `linkerd` Client em um shell baseado em bash no MacOS. Esses comandos copiam o binário do `linkerd` cliente para o local do programa de usuário padrão em seu `PATH`.

```bash
sudo cp ./linkerd2-cli-$LINKERD_VERSION-darwin /usr/local/bin/linkerd
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
