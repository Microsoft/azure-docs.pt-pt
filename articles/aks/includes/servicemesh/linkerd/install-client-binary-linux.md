---
author: paulbouwer
ms.topic: include
ms.date: 10/09/2019
ms.author: pabouwer
ms.openlocfilehash: d303def85d66d5c2039b426e48186f90c2b3fff6
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "86277867"
---
## <a name="download-and-install-the-linkerd-linkerd-client-binary"></a>Descarregue e instale o binário de cliente linkerd Linkerd

Numa concha baseada em bash no Linux ou [no Subsistema Windows para Linux,][install-wsl]utilize `curl` para descarregar a versão Linkerd da seguinte forma:

```bash
# Specify the Linkerd version that will be leveraged throughout these instructions
LINKERD_VERSION=stable-2.6.0

curl -sLO "https://github.com/linkerd/linkerd2/releases/download/$LINKERD_VERSION/linkerd2-cli-$LINKERD_VERSION-linux"
```

O `linkerd` binário cliente funciona na sua máquina de cliente e permite-lhe interagir com a malha de serviço Linkerd. Utilize os seguintes comandos para instalar o binário do cliente Linkerd `linkerd` numa concha baseada em bash no Linux ou no Windows [Subsistema para Linux][install-wsl]. Estes comandos copiam o binário do `linkerd` cliente para a localização padrão do programa de utilizador no seu `PATH` .

```bash
sudo cp ./linkerd2-cli-$LINKERD_VERSION-linux /usr/local/bin/linkerd
sudo chmod +x /usr/local/bin/linkerd
```

Se quiser a conclusão da linha de comando para o binário do cliente Linkerd, `linkerd` então conehe-o da seguinte forma:

```bash
# Generate the bash completion file and source it in your current shell
mkdir -p ~/completions && linkerd completion bash > ~/completions/linkerd.bash
source ~/completions/linkerd.bash

# Source the bash completion file in your .bashrc so that the command-line completions
# are permanently available in your shell
echo "source ~/completions/linkerd.bash" >> ~/.bashrc
```

<!-- LINKS - external -->
[install-wsl]: /windows/wsl/install-win10
