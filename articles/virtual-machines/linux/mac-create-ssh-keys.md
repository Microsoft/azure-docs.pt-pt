---
title: Crie e use um par de chaves SSH para VMs Linux em Azure
description: Como criar e utilizar um par de chaves público-privado SSH para VMs Linux em Azure para melhorar a segurança do processo de autenticação.
author: cynthn
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.topic: article
ms.date: 12/06/2019
ms.author: cynthn
ms.openlocfilehash: af18a32143ebc9db7be923b09de106b79022321f
ms.sourcegitcommit: 5f39f60c4ae33b20156529a765b8f8c04f181143
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/10/2020
ms.locfileid: "78969046"
---
# <a name="quick-steps-create-and-use-an-ssh-public-private-key-pair-for-linux-vms-in-azure"></a>Passos rápidos: Crie e use um par de chaves público-privado SSH para VMs Linux em Azure

Com um par de chaves de concha segura (SSH), pode criar máquinas virtuais (VMs) em Azure que utilizam chaves SSH para autenticação, eliminando a necessidade de as palavras-passe iniciarem o seu início. Este artigo mostra-lhe como gerar e usar rapidamente um par de ficheiros público-privado sSH para VMs Linux. Pode completar estes passos com o Azure Cloud Shell, um anfitrião macOS ou Linux, o Subsistema Windows para Linux e outras ferramentas que suportam o OpenSSH. 

> [!NOTE]
> Os VMs criados com teclas SSH são configurados por padrão com senhas desativadas, o que aumenta consideravelmente a dificuldade de ataques de adivinhação de força bruta. 

Para mais fundos e exemplos, consulte [passos detalhados para criar pares de chaves SSH](create-ssh-keys-detailed.md).

Para obter formas adicionais de gerar e utilizar as teclas SSH num computador Windows, consulte [como utilizar as teclas SSH com o Windows no Azure](ssh-from-windows.md).

[!INCLUDE [virtual-machines-common-ssh-support](../../../includes/virtual-machines-common-ssh-support.md)]

## <a name="create-an-ssh-key-pair"></a>Criar um par de chaves SSH

Utilize o comando `ssh-keygen` para gerar ficheiros de chaves públicas e privadas SSH. Por padrão, estes ficheiros são criados no diretório ~/.ssh. Pode especificar uma localização diferente e uma palavra-passe opcional *(palavra-passe)* para aceder ao ficheiro chave privado. Se um par de chaves SSH com o mesmo nome existir no local dado, esses ficheiros são substituídos.

O seguinte comando cria um par de chaves SSH usando encriptação RSA e um pouco de comprimento de 4096:

```bash
ssh-keygen -m PEM -t rsa -b 4096
```

Se utilizar o [Azure CLI](/cli/azure) para criar o seu VM com o comando [az vm criar,](/cli/azure/vm#az-vm-create) pode gerar opcionalmente ficheiros de chaves públicas e privadas sSH utilizando a opção `--generate-ssh-keys`. Os ficheiros-chave são armazenados no diretório ~/.ssh, salvo especificação em contrário com a opção `--ssh-dest-key-path`. A opção `--generate-ssh-keys` não sobreporá os ficheiros-chave existentes, em vez de devolver um erro. No seguinte comando, substitua *o Nome VM* e *o RGname* com os seus próprios valores:

```azurecli
az vm create --name VMname --resource-group RGname --generate-ssh-keys 
```

## <a name="provide-an-ssh-public-key-when-deploying-a-vm"></a>Forneça uma chave pública SSH ao implementar um VM

Para criar um VM Linux que utilize chaves SSH para autenticação, especifique a sua chave pública SSH ao criar o VM utilizando o portal Azure, modelos Azure CLI, Azure Resource Manager ou outros métodos:

* [Crie uma máquina virtual Linux com o portal Azure](quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Crie uma máquina virtual Linux com o Azure CLI](quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Crie um VM Linux usando um modelo Azure](create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

Se não estiver familiarizado com o formato de uma chave pública SSH, pode exibir a sua chave pública com o seguinte comando `cat`, substituindo `~/.ssh/id_rsa.pub` pelo caminho e nome de ficheiro do seu próprio ficheiro de chaves públicas, se necessário:

```bash
cat ~/.ssh/id_rsa.pub
```

Um valor-chave público típico parece este exemplo:

```
ssh-rsa AAAAB3NzaC1yc2EAABADAQABAAACAQC1/KanayNr+Q7ogR5mKnGpKWRBQU7F3Jjhn7utdf7Z2iUFykaYx+MInSnT3XdnBRS8KhC0IP8ptbngIaNOWd6zM8hB6UrcRTlTpwk/SuGMw1Vb40xlEFphBkVEUgBolOoANIEXriAMvlDMZsgvnMFiQ12tD/u14cxy1WNEMAftey/vX3Fgp2vEq4zHXEliY/sFZLJUJzcRUI0MOfHXAuCjg/qyqqbIuTDFyfg8k0JTtyGFEMQhbXKcuP2yGx1uw0ice62LRzr8w0mszftXyMik1PnshRXbmE2xgINYg5xo/ra3mq2imwtOKJpfdtFoMiKhJmSNHBSkK7vFTeYgg0v2cQ2+vL38lcIFX4Oh+QCzvNF/AXoDVlQtVtSqfQxRVG79Zqio5p12gHFktlfV7reCBvVIhyxc2LlYUkrq4DHzkxNY5c9OGSHXSle9YsO3F1J5ip18f6gPq4xFmo6dVoJodZm9N0YMKCkZ4k1qJDESsJBk2ujDPmQQeMjJX3FnDXYYB182ZCGQzXfzlPDC29cWVgDZEXNHuYrOLmJTmYtLZ4WkdUhLLlt5XsdoKWqlWpbegyYtGZgeZNRtOOdN6ybOPJqmYFd2qRtb4sYPniGJDOGhx4VodXAjT09omhQJpE6wlZbRWDvKC55R2d/CSPHJscEiuudb+1SG2uA/oik/WQ== username@domainname
```

Se copiar e colar o conteúdo do ficheiro de chaves públicas para utilizar no portal Azure ou num modelo de Gestor de Recursos, certifique-se de que não copia nenhum espaço branco. Para copiar uma chave pública no macOS, pode canalizar o ficheiro chave público para `pbcopy`. Da mesma forma em Linux, pode canalizar o ficheiro chave público para programas como `xclip`.

A chave pública que coloca no seu Linux VM em Azure é por padrão armazenada em ~/.ssh/id_rsa.pub, a menos que tenha especificado um local diferente quando criou o par chave. Para utilizar o [Azure CLI 2.0](/cli/azure) para criar o seu VM com uma chave pública existente, especifique o valor e opcionalmente a localização desta chave pública utilizando o [az vM criar](/cli/azure/vm#az-vm-create) comando com a opção `--ssh-key-values`. No seguinte comando, substitua *o Nome VM,* *o RGname*e *o tecladoFile* com os seus próprios valores:

```azurecli
az vm create --name VMname --resource-group RGname --ssh-key-values mysshkey.pub
```

Se quiser utilizar várias teclas SSH com o seu VM, pode introduzi-las numa lista separada pelo espaço, como esta `--ssh-key-values sshkey-desktop.pub sshkey-laptop.pub`.


## <a name="ssh-into-your-vm"></a>Aceder através de SSH à VM

Com a chave pública implantada no seu VM Azure e a chave privada no seu sistema local, SSH no seu VM utilizando o endereço IP ou o nome DNS do seu VM. No comando seguinte, substitua *o azureuser* e *o myvm.westus.cloudapp.azure.com* pelo nome de utilizador do administrador e pelo nome de domínio totalmente qualificado (ou endereço IP):

```bash
ssh azureuser@myvm.westus.cloudapp.azure.com
```

Se especificou uma palavra-passe quando criou o seu par de chaves, introduza essa frase-passe quando solicitada durante o processo de login. O VM é adicionado ao seu ficheiro ~/.ssh/known_hosts, e não lhe será pedido que se conecte novamente até que a chave pública das alterações do VM Do Seu Azure ou o nome do servidor seja removido de ~/.ssh/known_hosts.

Se o VM estiver a utilizar a política de acesso just-in-time, precisa de solicitar acesso antes de se ligar ao VM. Para obter mais informações sobre a política just-in-time, consulte Gerir o acesso virtual à [máquina utilizando a política de tempo justo.](../../security-center/security-center-just-in-time.md)

## <a name="next-steps"></a>Passos seguintes

* Para obter mais informações sobre o trabalho com os pares de chaves SSH, consulte [passos detalhados para criar e gerir os pares de chaves SSH](create-ssh-keys-detailed.md).

* Se tiver dificuldades com ligações SSH a VMs Azure, consulte [ligações SSH de Troubleshoot a um VM Azure Linux](troubleshoot-ssh-connection.md).
