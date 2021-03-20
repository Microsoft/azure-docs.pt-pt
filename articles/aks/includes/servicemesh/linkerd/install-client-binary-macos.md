---
author: paulbouwer
ms.topic: include
ms.date: 10/09/2019
ms.author: pabouwer
ms.openlocfilehash: 876e05d7b18ac193edbc9cf842ea2c1bf0555d54
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "77593742"
---
## <a name="download-and-install-the-linkerd-linkerd-client-binary"></a>Descarregue e instale o binário de cliente linkerd Linkerd

Numa concha baseada em bash no MacOS, use `curl` para descarregar o lançamento linkerd da seguinte forma:

```bash
# Specify the Linkerd version that will be leveraged throughout these instructions
LINKERD_VERSION=stable-2.6.0

curl -sLO "https://github.com/linkerd/linkerd2/releases/download/$LINKERD_VERSION/linkerd2-cli-$LINKERD_VERSION-darwin"
```

O `linkerd` binário cliente funciona na sua máquina de cliente e permite-lhe interagir com a malha de serviço Linkerd. Utilize os seguintes comandos para instalar o binário do cliente Linkerd `linkerd` numa concha baseada em bash no MacOS. Estes comandos copiam o binário do `linkerd` cliente para a localização padrão do programa de utilizador no seu `PATH` .

```bash
sudo cp ./linkerd2-cli-$LINKERD_VERSION-darwin /usr/local/bin/linkerd
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
