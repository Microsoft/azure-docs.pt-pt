---
title: Mover ficheiros de e para Azure Linux VMs com SCP
description: Mover de e para um Linux VM em Azure com um par de chaves SSH.
author: cynthn
ms.service: virtual-machines-linux
ms.workload: infrastructure
ms.topic: how-to
ms.date: 07/12/2017
ms.author: cynthn
ms.subservice: disks
ms.openlocfilehash: 83c054a9e2dd829dbfb34a3873f06332e504b832
ms.sourcegitcommit: 2bd0a039be8126c969a795cea3b60ce8e4ce64fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/14/2021
ms.locfileid: "98201200"
---
# <a name="move-files-to-and-from-a-linux-vm-using-scp"></a>Mover ficheiros de e para um VM Linux usando SCP

Este artigo mostra como mover ficheiros da sua estação de trabalho para um VM Azure Linux, ou de um VM Azure Linux até à sua estação de trabalho, utilizando o Secure Copy (SCP). Mover ficheiros entre a sua estação de trabalho e um Linux VM, de forma rápida e segura, é fundamental para gerir a sua infraestrutura Azure. 

Para este artigo, você precisa de um VM Linux implantado em Azure usando [ficheiros chave SSH públicos e privados](mac-create-ssh-keys.md). Também precisa de um cliente SCP para o seu computador local. É construído em cima de SSH e incluído na casca padrão bash da maioria dos computadores Linux e Mac e algumas conchas Windows.

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

Como exemplos, movemos um ficheiro de configuração Azure até um Linux VM e puxamos um diretório de ficheiros de registo, ambos usando as teclas SCP e SSH.   

## <a name="ssh-key-pair-authentication"></a>Autenticação do par de chaves SSH

O SCP utiliza sSH para a camada de transporte. O SSH trata da autenticação no anfitrião do destino e move o ficheiro num túnel encriptado fornecido por padrão com SSH. Para a autenticação do SSH, podem ser utilizados nomes de utilizador e palavras-passe. No entanto, a autenticação de chaves públicas e privadas SSH é recomendada como uma melhor prática de segurança. Uma vez que o SSH tenha autenticado a ligação, a SCP começa então a copiar o ficheiro. Utilizando uma chave pública `~/.ssh/config` e privada SSH devidamente configurada, a ligação SCP pode ser estabelecida apenas utilizando um nome de servidor (ou endereço IP). Se tiver apenas uma chave SSH, a SCP procura-a no `~/.ssh/` diretório e utiliza-a por defeito para iniciar sessão no VM.

Para obter mais informações sobre a configuração das `~/.ssh/config` suas chaves públicas e privadas SSH, consulte as [teclas Create SSH](mac-create-ssh-keys.md).

## <a name="scp-a-file-to-a-linux-vm"></a>SCP um ficheiro para um Linux VM

Para o primeiro exemplo, copiamos um ficheiro de configuração Azure até um Linux VM que é usado para implantar automatização. Como este ficheiro contém credenciais AZure API, que incluem segredos, a segurança é importante. O túnel encriptado fornecido pela SSH protege o conteúdo do ficheiro.

O seguinte comando copia o ficheiro *local .azure/config* para um Azure VM com FQDN *myserver.eastus.cloudapp.azure.com*. O nome de utilizador administrativo no Azure VM é *azureuser*. O ficheiro é direcionado para o */home/azureuser/diretório.* Substitua os seus próprios valores neste comando.

```bash
scp ~/.azure/config azureuser@myserver.eastus.cloudapp.com:/home/azureuser/config
```

## <a name="scp-a-directory-from-a-linux-vm"></a>SCP um diretório de um Linux VM

Para este exemplo, copiamos um diretório de ficheiros de registo do Linux VM até à sua estação de trabalho. Um ficheiro de registo pode ou não conter dados confidenciais ou secretos. No entanto, a utilização do SCP garante que o conteúdo dos ficheiros de registo são encriptados. Usar o SCP para transferir os ficheiros é a forma mais fácil de levar o diretório de registos e os ficheiros para a sua estação de trabalho, ao mesmo tempo que está seguro.

Os seguintes ficheiros de cópias de comando no */home/azureuser/logs/* diretório no Azure VM para o diretório local /tmp:

```bash
scp -r azureuser@myserver.eastus.cloudapp.com:/home/azureuser/logs/. /tmp/
```

A `-r` bandeira instrui a SCP a copiar novamente os ficheiros e diretórios do ponto do diretório listado no comando.  Note também que a sintaxe da linha de comando é semelhante a um `cp` comando de cópia.

## <a name="next-steps"></a>Passos seguintes

* [Gerir utilizadores, SSH e verificar ou reparar discos em VMs Azure Linux utilizando a Extensão VMAccess](../extensions/vmaccess.md?toc=/azure/virtual-machines/linux/toc.json)
