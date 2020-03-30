---
title: Mover ficheiros de e para VMs Azure Linux com SCP
description: Mova os ficheiros de e para um Linux VM em Azure utilizando sCP e um par de chaves SSH.
author: cynthn
ms.service: virtual-machines-linux
ms.workload: infrastructure
ms.topic: article
ms.date: 07/12/2017
ms.author: cynthn
ms.subservice: disks
ms.openlocfilehash: a0837790b70de42073338bf085ee0f3976b866f6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78969609"
---
# <a name="move-files-to-and-from-a-linux-vm-using-scp"></a>Mova ficheiros de e para um VM Linux usando SCP

Este artigo mostra como mover ficheiros da sua estação de trabalho para um VM Azure Linux, ou de um VM Azure Linux até à sua estação de trabalho, utilizando secure Copy (SCP). Mover ficheiros entre a sua estação de trabalho e um VM Linux, de forma rápida e segura, é fundamental para gerir a sua infraestrutura Azure. 

Para este artigo, é necessário um VM Linux implantado em Azure utilizando [ficheiros de chaves públicas e privadas SSH](mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Também precisa de um cliente SCP para o seu computador local. É construído em cima de SSH e incluído na concha padrão bash da maioria dos computadores Linux e Mac e algumas conchas do Windows.

## <a name="quick-commands"></a>Comandos rápidos

Copie um ficheiro até ao Linux VM

```bash
scp file azureuser@azurehost:directory/targetfile
```

Copie um ficheiro do Linux VM

```bash
scp azureuser@azurehost:directory/file targetfile
```

## <a name="detailed-walkthrough"></a>Instruções detalhadas

Como exemplos, movemos um ficheiro de configuração Azure até um VM Linux e retiramos um diretório de ficheiros de registo, ambos utilizando teclas SCP e SSH.   

## <a name="ssh-key-pair-authentication"></a>Autenticação do par de chaves SSH

O SCP utiliza SSH para a camada de transporte. O SSH trata da autenticação no anfitrião do destino e move o ficheiro num túnel encriptado fornecido por padrão com SSH. Para a autenticação SSH, podem ser utilizados nomes de utilizador e palavras-passe. No entanto, a autenticação de chaves públicas e privadas ssh é recomendada como uma melhor prática de segurança. Uma vez que o SSH tenha autenticado a ligação, o SCP começa a copiar o ficheiro. Utilizando uma chave `~/.ssh/config` pública e privada ssh devidamente configurada e ssh, a ligação SCP pode ser estabelecida apenas utilizando um nome de servidor (ou endereço IP). Se tiver apenas uma chave SSH, a `~/.ssh/` SCP procura-a no diretório e utiliza-a por defeito para iniciar sessão no VM.

Para obter mais informações sobre a configuração `~/.ssh/config` das suas chaves públicas e privadas SSH, consulte Create [SSH keys](mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="scp-a-file-to-a-linux-vm"></a>SCP um ficheiro para um Linux VM

Para o primeiro exemplo, copiamos um ficheiro de configuração Azure até um VM Linux que é usado para implementar automação. Como este ficheiro contém credenciais Azure API, que incluem segredos, a segurança é importante. O túnel encriptado fornecido pelo SSH protege o conteúdo do ficheiro.

O comando seguinte copia o ficheiro *local .azure/config* para um Azure VM com *myserver.eastus.cloudapp.azure.com*FQDN . O nome de utilizador administrativo do Azure VM é *azureuser*. O ficheiro é direcionado para o */home/azureuser/diretório.* Substitua os seus próprios valores neste comando.

```bash
scp ~/.azure/config azureuser@myserver.eastus.cloudapp.com:/home/azureuser/config
```

## <a name="scp-a-directory-from-a-linux-vm"></a>SCP um diretório de um Linux VM

Para este exemplo, copiamos um diretório de ficheiros de registo do VM Linux até à sua estação de trabalho. Um ficheiro de registo pode ou não conter dados sensíveis ou secretos. No entanto, a utilização do SCP garante que o conteúdo dos ficheiros de registo está encriptado. Usar o SCP para transferir os ficheiros é a forma mais fácil de levar o diretório de registo e os ficheiros para a sua estação de trabalho, estando também seguro.

Os seguintes ficheiros de cópias de comando no */home/azureuser/logs/ diretório* no Azure VM para o diretório local /tmp:

```bash
scp -r azureuser@myserver.eastus.cloudapp.com:/home/azureuser/logs/. /tmp/
```

A `-r` bandeira instrui a SCP a copiar recursivamente os ficheiros e diretórios a partir do ponto do diretório listado no comando.  Note também que a sintaxe `cp` da linha de comando é semelhante a um comando de cópia.

## <a name="next-steps"></a>Passos seguintes

* [Gerir utilizadores, SSH, e verificar ou reparar discos em VMs Azure Linux utilizando a extensão VMAccess](using-vmaccess-extension.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
