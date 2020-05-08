---
author: dlepow
ms.service: container-registry
ms.topic: include
ms.date: 05/07/2020
ms.author: danlep
ms.openlocfilehash: be170144fddeb1a69592f1714ec745d559665832
ms.sourcegitcommit: 999ccaf74347605e32505cbcfd6121163560a4ae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/08/2020
ms.locfileid: "82982452"
---
## <a name="create-a-docker-enabled-virtual-machine"></a>Criar uma máquina virtual ativada pelo Docker

Para efeitos de teste, utilize um Ubuntu VM ativado pelo Docker para aceder a um registo de contentores Azure. Para utilizar a autenticação do Diretório Ativo Azure no registo, instale também o [Azure CLI][azure-cli] no VM. Se já tem uma máquina virtual Azure, ignore este passo de criação.

Pode utilizar o mesmo grupo de recursos para a sua máquina virtual e o seu registo de contentores. Esta configuração simplifica a limpeza no final, mas não é necessária. Se optar por criar um grupo de recursos separado para a máquina virtual e rede virtual, executar grupo [Az criar][az-group-create]. O exemplo que se segue pressupõe que definiu variáveis ambientais para o nome do grupo de recursos e localização do registo:

```azurecli
az group create --name $RESOURCE_GROUP --location $REGISTRY_LOCATION
```

Agora implemente uma máquina virtual Ubuntu Azure padrão com [az vm criar][az-vm-create]. O exemplo seguinte cria um VM chamado *myDockerVM*.

```azurecli
VM_NAME=myDockerVM

az vm create \
  --resource-group $RESOURCE_GROUP \
  --name $VM_NAME \
  --image UbuntuLTS \
  --admin-username azureuser \
  --generate-ssh-keys
```

Demora alguns minutos até que a VM seja criada. Quando o comando estiver concluído, `publicIpAddress` tome nota do mostrado pelo Azure CLI. Utilize este endereço para efazer ligações SSH ao VM.

### <a name="install-docker-on-the-vm"></a>Instale docker no VM

Depois de o VM estar em execução, faça uma ligação SSH ao VM. Substitua *publicamente IpAddress* pelo endereço IP público do seu VM.

```bash
ssh azureuser@publicIpAddress
```

Executar os seguintes comandos para instalar docker no Ubuntu VM:

```bash
sudo apt-get update
sudo apt install docker.io -y
```

Após a instalação, execute o seguinte comando para verificar se o Docker está a funcionar corretamente no VM:

```bash
sudo docker run -it hello-world
```

Saída:

```
Hello from Docker!
This message shows that your installation appears to be working correctly.
[...]
```

### <a name="install-the-azure-cli"></a>Instalar a CLI do Azure

Siga os passos em [Instalação Azure CLI com aptidão](/cli/azure/install-azure-cli-apt?view=azure-cli-latest) para instalar o Azure CLI na sua máquina virtual Ubuntu. Por exemplo:

```bash
curl -sL https://aka.ms/InstallAzureCLIDeb | sudo bash
```

Saia da ligação SSH.

[azure-cli]: /cli/azure/install-azure-cli
[az-vm-create]: /cli/azure/vm#az-vm-create
[az-group-create]: /cli/azure/group