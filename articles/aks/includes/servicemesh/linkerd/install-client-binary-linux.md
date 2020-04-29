---
author: paulbouwer
ms.topic: include
ms.date: 10/09/2019
ms.author: pabouwer
ms.openlocfilehash: 7a22256c88f9cee3ce62c68a2de4a5974d76f026
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77593740"
---
## <a name="download-and-install-the-linkerd-linkerd-client-binary"></a>Descarregue e instale o linkerd linkerd client binary

Numa concha baseada em golpes no Linux ou `curl` [no Subsistema Windows para linux,][install-wsl]utilize para descarregar o lançamento do Linkerd da seguinte forma:

```bash
# Specify the Linkerd version that will be leveraged throughout these instructions
LINKERD_VERSION=stable-2.6.0

curl -sLO "https://github.com/linkerd/linkerd2/releases/download/$LINKERD_VERSION/linkerd2-cli-$LINKERD_VERSION-linux"
```

O `linkerd` binário do cliente funciona na sua máquina cliente e permite-lhe interagir com a malha de serviço Linkerd. Utilize os seguintes comandos para `linkerd` instalar o binário do cliente Linkerd numa concha baseada em golpes no Linux ou [no Subsistema Windows para Linux][install-wsl]. Estes comandos `linkerd` copiam o binário do cliente `PATH`para a localização padrão do programa de utilizador na sua .

```bash
sudo cp ./linkerd2-cli-$LINKERD_VERSION-linux /usr/local/bin/linkerd
sudo chmod +x /usr/local/bin/linkerd
```

Se quiser a conclusão da linha de `linkerd` comando para o binário do cliente Linkerd, então instale-a da seguinte forma:

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