---
title: Criar e utilizar um par de chaves SSH para Os VMs Linux em Azure
description: Como criar e utilizar um par de chaves SSH público-privado para Os VMs Linux em Azure para melhorar a segurança do processo de autenticação.
author: cynthn
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 12/06/2019
ms.author: cynthn
ms.openlocfilehash: 33ba816227db4cf958fd30c9dac1a0745505c504
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87513694"
---
# <a name="quick-steps-create-and-use-an-ssh-public-private-key-pair-for-linux-vms-in-azure"></a>Passos rápidos: Criar e utilizar um par de chaves SSH público-privado para Os VMs Linux em Azure

Com um par de chaves secure shell (SSH), pode criar máquinas virtuais (VMs) em Azure que utilizam chaves SSH para autenticação. Este artigo mostra-lhe como gerar e usar rapidamente um par de ficheiros ssh público-privado para Os VMs Linux. Pode completar estes passos com o Azure Cloud Shell, um anfitrião macOS ou Linux. 

> [!NOTE]
> Os VMs criados com teclas SSH são por padrão configurados com palavras-passe desativadas, o que aumenta consideravelmente a dificuldade de ataques de adivinhação de força bruta. 

Para obter mais antecedentes e exemplos, consulte [passos detalhados para criar pares de chaves SSH](create-ssh-keys-detailed.md).

Para obter formas adicionais de gerar e utilizar as teclas SSH num computador Windows, consulte [como utilizar as teclas SSH com o Windows no Azure](ssh-from-windows.md).

[!INCLUDE [virtual-machines-common-ssh-support](../../../includes/virtual-machines-common-ssh-support.md)]

## <a name="create-an-ssh-key-pair"></a>Criar um par de chaves SSH

Utilize o `ssh-keygen` comando para gerar ficheiros chave públicos e privados SSH. Por predefinição, estes ficheiros são criados no diretório ~/ssh. Pode especificar uma localização diferente e uma senha opcional *(palavra-passe)* para aceder ao ficheiro de chave privada. Se existir um par de chaves SSH com o mesmo nome no local indicado, esses ficheiros são substituídos.

O seguinte comando cria um par de chaves SSH usando encriptação RSA e um pouco de comprimento de 4096:

```bash
ssh-keygen -m PEM -t rsa -b 4096
```

Se utilizar o [CLI Azure](/cli/azure) para criar o seu VM com o comando [az vm criar,](/cli/azure/vm#az-vm-create) pode opcionalmente gerar ficheiros de chaves públicas e privadas SSH utilizando a `--generate-ssh-keys` opção. Os ficheiros-chave são armazenados no diretório ~/ssh, salvo especificação em contrário com a `--ssh-dest-key-path` opção. Se já existir um par de chaves ssh e a  `--generate-ssh-keys` opção for utilizada, não será gerado um novo par de chaves, mas em vez disso será utilizado o par de chaves existente. No seguinte comando, substitua *o VMname* e *o RGname* pelos seus próprios valores:

```azurecli
az vm create --name VMname --resource-group RGname --image UbuntuLTS --generate-ssh-keys 
```

## <a name="provide-an-ssh-public-key-when-deploying-a-vm"></a>Forneça uma chave pública SSH ao implementar um VM

Para criar um VM Linux que utilize chaves SSH para autenticação, especifique a sua chave pública SSH ao criar o VM utilizando o portal Azure CLI, modelos Azure Resource Manager ou outros métodos:

* [Criar uma máquina virtual do Linux com o portal do Azure](quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Criar uma máquina virtual Linux com o Azure CLI](quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Criar uma VM com Linux utilizando um modelo do Azure](create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

Se não estiver familiarizado com o formato de uma chave pública SSH, pode apresentar a sua chave pública com o seguinte `cat` comando, substituindo `~/.ssh/id_rsa.pub` pelo caminho e nome de ficheiro do seu próprio ficheiro de chave pública, se necessário:

```bash
cat ~/.ssh/id_rsa.pub
```

Um valor típico da chave pública parece este exemplo:

```
ssh-rsa AAAAB3NzaC1yc2EAABADAQABAAACAQC1/KanayNr+Q7ogR5mKnGpKWRBQU7F3Jjhn7utdf7Z2iUFykaYx+MInSnT3XdnBRS8KhC0IP8ptbngIaNOWd6zM8hB6UrcRTlTpwk/SuGMw1Vb40xlEFphBkVEUgBolOoANIEXriAMvlDMZsgvnMFiQ12tD/u14cxy1WNEMAftey/vX3Fgp2vEq4zHXEliY/sFZLJUJzcRUI0MOfHXAuCjg/qyqqbIuTDFyfg8k0JTtyGFEMQhbXKcuP2yGx1uw0ice62LRzr8w0mszftXyMik1PnshRXbmE2xgINYg5xo/ra3mq2imwtOKJpfdtFoMiKhJmSNHBSkK7vFTeYgg0v2cQ2+vL38lcIFX4Oh+QCzvNF/AXoDVlQtVtSqfQxRVG79Zqio5p12gHFktlfV7reCBvVIhyxc2LlYUkrq4DHzkxNY5c9OGSHXSle9YsO3F1J5ip18f6gPq4xFmo6dVoJodZm9N0YMKCkZ4k1qJDESsJBk2ujDPmQQeMjJX3FnDXYYB182ZCGQzXfzlPDC29cWVgDZEXNHuYrOLmJTmYtLZ4WkdUhLLlt5XsdoKWqlWpbegyYtGZgeZNRtOOdN6ybOPJqmYFd2qRtb4sYPniGJDOGhx4VodXAjT09omhQJpE6wlZbRWDvKC55R2d/CSPHJscEiuudb+1SG2uA/oik/WQ== username@domainname
```

Se copiar e colar o conteúdo do ficheiro de chave pública para utilizar no portal Azure ou num modelo de Gestor de Recursos, certifique-se de que não copia nenhum espaço em branco. Para copiar uma chave pública no macOS, pode canalizar o ficheiro de chave pública para `pbcopy` . Da mesma forma no Linux, pode canalizar o ficheiro de chave pública para programas como `xclip` .

A chave pública que coloca no seu Linux VM em Azure é por padrão armazenada em ~/.ssh/id_rsa.pub, a menos que tenha especificado uma localização diferente quando criou o par de chaves. Para utilizar o [Azure CLI 2.0](/cli/azure) para criar o seu VM com uma chave pública existente, especifique o valor e opcionalmente a localização desta chave pública utilizando o [comando az vm criar](/cli/azure/vm#az-vm-create) com a `--ssh-key-values` opção. No seguinte comando, substitua *o myVM*, *myResourceGroup,* *UbuntuLTS,* *azureuser,* e *mysshkey.pub* pelos seus próprios valores:


```azurecli
az vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --image UbuntuLTS \
  --admin-username azureuser \
  --ssh-key-values mysshkey.pub
```

Se pretender utilizar várias teclas SSH com o seu VM, pode introduzi-las numa lista separada do espaço, como esta `--ssh-key-values sshkey-desktop.pub sshkey-laptop.pub` .


## <a name="ssh-into-your-vm"></a>Aceder através de SSH à VM

Com a chave pública implantada no seu Azure VM, e a chave privada no seu sistema local, SSH no seu VM utilizando o endereço IP ou o nome DNS do seu VM. No seguinte comando, *substitua o azureuser* e *myvm.westus.cloudapp.azure.com* pelo nome de utilizador do administrador e pelo nome de domínio totalmente qualificado (ou endereço IP):

```bash
ssh azureuser@myvm.westus.cloudapp.azure.com
```

Se especificou uma palavra-passe quando criou o seu par de chaves, introduza essa palavra-passe quando solicitada durante o processo de início de sessão. O VM é adicionado ao seu ficheiro ~/.ssh/known_hosts, e não lhe será solicitado que volte a ligar até que a chave pública do seu VM Azure mude ou o nome do servidor seja removido de ~/.ssh/known_hosts.

Se o VM estiver a utilizar a política de acesso just-in-time, tem de solicitar acesso antes de poder ligar-se ao VM. Para obter mais informações sobre a política just-in-time, consulte [Gerir o acesso à máquina virtual utilizando a política just in time](../../security-center/security-center-just-in-time.md).

## <a name="next-steps"></a>Passos seguintes

* Para obter mais informações sobre o trabalho com os pares de chaves SSH, consulte [passos detalhados para criar e gerir os pares de chaves SSH](create-ssh-keys-detailed.md).

* Se tiver dificuldades com as ligações SSH aos VMs Azure, consulte [as ligações SSH de resolução de problemas a um VM Azure Linux](../troubleshooting/troubleshoot-ssh-connection.md).
