---
author: dlepow
ms.service: container-registry
ms.topic: include
ms.date: 05/07/2020
ms.author: danlep
ms.openlocfilehash: d699e8985a3a23b3aab87601d5298d9c8f7e34e1
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2021
ms.locfileid: "102244855"
---
## <a name="create-a-docker-enabled-virtual-machine"></a>Criar uma máquina virtual ativada pelo Docker

Para efeitos de teste, utilize um Ubuntu VM ativado por Docker para aceder a um registo de contentores Azure. Para utilizar a autenticação do Azure Ative Directory no registo, instale também o [CLI Azure][azure-cli] no VM. Se já tem uma máquina virtual Azure, ignore este passo de criação.

Pode utilizar o mesmo grupo de recursos para a sua máquina virtual e o registo do seu contentor. Esta configuração simplifica a limpeza no final, mas não é necessária. Se optar por criar um grupo de recursos separado para a máquina virtual e rede virtual, executar [o grupo AZ criar][az-group-create]. O exemplo a seguir pressupõe que definiu variáveis ambientais para o nome do grupo de recursos e localização do registo:

```azurecli
az group create --name $RESOURCE_GROUP --location $REGISTRY_LOCATION
```

Agora implemente uma máquina virtual Ubuntu Azure padrão com [criação az vm][az-vm-create]. O exemplo a seguir cria um VM chamado *myDockerVM*.

```azurecli
VM_NAME=myDockerVM

az vm create \
  --resource-group $RESOURCE_GROUP \
  --name $VM_NAME \
  --image UbuntuLTS \
  --admin-username azureuser \
  --generate-ssh-keys
```

Demora alguns minutos até que a VM seja criada. Quando o comando terminar, tome nota do `publicIpAddress` mostrado pelo Azure CLI. Utilize este endereço para fazer ligações SSH ao VM.

### <a name="install-docker-on-the-vm"></a>Instale o Docker no VM

Depois de o VM estar em funcionamento, faça uma ligação SSH ao VM. Substitua *o endereço públicoIpAddress* pelo endereço IP público do seu VM.

```bash
ssh azureuser@publicIpAddress
```

Executar os seguintes comandos para instalar Docker no Ubuntu VM:

```bash
sudo apt-get update
sudo apt install docker.io -y
```

Após a instalação, executar o seguinte comando para verificar se o Docker está a funcionar corretamente no VM:

```bash
sudo docker run -it hello-world
```

Resultado:

```
Hello from Docker!
This message shows that your installation appears to be working correctly.
[...]
```

### <a name="install-the-azure-cli"></a>Instalar a CLI do Azure

Siga os passos na [Instalação Azure CLI com apta](/cli/azure/install-azure-cli-apt) a instalar o CLI Azure na sua máquina virtual Ubuntu. Por exemplo:

```bash
curl -sL https://aka.ms/InstallAzureCLIDeb | sudo bash
```

Saia da ligação SSH.

[azure-cli]: /cli/azure/install-azure-cli
[az-vm-create]: /cli/azure/vm#az-vm-create
[az-group-create]: /cli/azure/group