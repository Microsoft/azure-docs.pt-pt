---
author: paulbouwer
ms.topic: include
ms.date: 10/09/2019
ms.author: pabouwer
ms.openlocfilehash: 876e05d7b18ac193edbc9cf842ea2c1bf0555d54
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/25/2020
ms.locfileid: "77593742"
---
## <a name="download-and-install-the-linkerd-linkerd-client-binary"></a>Descarregue e instale o linkerd linkerd client binary

Numa concha baseada em bash no MacOS, use `curl` para descarregar o lançamento linkerd da seguinte forma:

```bash
# Specify the Linkerd version that will be leveraged throughout these instructions
LINKERD_VERSION=stable-2.6.0

curl -sLO "https://github.com/linkerd/linkerd2/releases/download/$LINKERD_VERSION/linkerd2-cli-$LINKERD_VERSION-darwin"
```

O binário `linkerd` cliente funciona na sua máquina cliente e permite-lhe interagir com a malha de serviço Linkerd. Utilize os seguintes comandos para instalar o Linkerd `linkerd` binário de cliente numa concha baseada em golpes no MacOS. Estes comandos copiam o `linkerd` binário do cliente para a localização padrão do programa de utilizador na sua `PATH`.

```bash
sudo cp ./linkerd2-cli-$LINKERD_VERSION-darwin /usr/local/bin/linkerd
sudo chmod +x /usr/local/bin/linkerd
```

Se quiser a conclusão da linha de comando para o linkerd `linkerd` binário de cliente, então configura-o da seguinte forma:

```bash
# Generate the bash completion file and source it in your current shell
mkdir -p ~/completions && linkerd completion bash > ~/completions/linkerd.bash
source ~/completions/linkerd.bash

# Source the bash completion file in your .bashrc so that the command-line completions
# are permanently available in your shell
echo "source ~/completions/linkerd.bash" >> ~/.bashrc
```
