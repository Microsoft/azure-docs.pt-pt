---
title: Mover ficheiros de e para VMs de Linux do Azure com o SCP | Documentos da Microsoft
description: Mover com segurança os ficheiros de e para uma VM do Linux no Azure utilizando o SCP e um par de chaves SSH.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: dlepow
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: danlep
ms.subservice: disks
ms.openlocfilehash: 13a2c889ac648e2847d1cc58a60a7b1c0f1fc1e2
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/09/2019
ms.locfileid: "67671656"
---
# <a name="move-files-to-and-from-a-linux-vm-using-scp"></a>Mover ficheiros de e para uma VM com Linux através de SCP

Este artigo mostra como mover os ficheiros da sua estação de trabalho até uma VM do Linux do Azure ou a partir de uma VM Linux do Azure para baixo para a sua estação de trabalho, utilizando a cópia segura (SCP). Mover ficheiros entre a estação de trabalho e uma VM do Linux, de forma rápida e segura, é essencial para gerir a sua infraestrutura do Azure. 

Neste artigo, terá de um VM implementada no Azure através do Linux [ficheiros de chaves públicos e privados SSH](mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Também precisa de um cliente de SCP para o seu computador local. É criado com base no SSH e incluído no shell de Bash padrão da maioria dos computadores Linux e Mac e alguns shells do Windows.

## <a name="quick-commands"></a>Comandos rápidos

Copiar um ficheiro até a VM do Linux

```bash
scp file azureuser@azurehost:directory/targetfile
```

Copiar um ficheiro para baixo a partir da VM do Linux

```bash
scp azureuser@azurehost:directory/file targetfile
```

## <a name="detailed-walkthrough"></a>Instruções detalhadas

Como exemplos, vamos mover um ficheiro de configuração do Azure até uma VM do Linux e a solicitação para baixo de um diretório do ficheiro de registo, ambos com chaves de SCP e SSH.   

## <a name="ssh-key-pair-authentication"></a>Autenticação do par de chaves SSH

SCP utiliza SSH para a camada de transporte. SSH processa a autenticação no anfitrião de destino, e ele move-o num túnel criptografado fornecido por padrão com SSH. Para a autenticação de SSH, nomes de utilizador e palavras-passe podem ser utilizadas. No entanto, a autenticação de chave pública e privada SSH são recomendados como uma melhor prática de segurança. Assim que o SSH foi autenticado a ligação, o SCP começa, em seguida, copiar o ficheiro. Usando um devidamente configuradas `~/.ssh/config` e chaves SSH públicas e privadas, a ligação de SCP podem ser estabelecidas utilizando apenas um nome de servidor (ou o endereço IP). Se tiver apenas uma chave SSH, SCP procura-lo no `~/.ssh/` directory e utiliza-o por predefinição para iniciar sessão VM.

Para obter mais informações sobre como configurar o seu `~/.ssh/config` e chaves SSH públicas e privadas, consulte [criar chaves SSH](mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="scp-a-file-to-a-linux-vm"></a>SCP um ficheiro para uma VM do Linux

Para o primeiro exemplo, vamos copiar um ficheiro de configuração do Azure até uma VM do Linux que é utilizada para implementar a automatização. Uma vez que este ficheiro contém as credenciais de API do Azure, que incluem segredos, segurança, é importante. O túnel encriptado fornecido pelo SSH protege o conteúdo do ficheiro.

O seguinte comando copia local *Azure/config* ficheiro para uma VM do Azure com o FQDN *myserver.eastus.cloudapp.azure.com*. O nome de utilizador de administrador na VM do Azure é *azureuser*. O ficheiro está direcionado para o */home/azureuser/* diretório. Substitua os seus valores neste comando.

```bash
scp ~/.azure/config azureuser@myserver.eastus.cloudapp.com:/home/azureuser/config
```

## <a name="scp-a-directory-from-a-linux-vm"></a>SCP um diretório a partir de uma VM do Linux

Neste exemplo, vamos copiar um diretório de ficheiros de registo da VM do Linux para baixo para a sua estação de trabalho. Um ficheiro de registo pode ou não pode conter dados confidenciais ou segredos. No entanto, a utilização de SCP assegura que o conteúdo dos ficheiros de registo é encriptado. Utilizar o SCP para transferir os ficheiros é a maneira mais fácil para obter o diretório de registo e ficheiros para baixo para a sua estação de trabalho e também está seguro.

O seguinte comando copia os ficheiros nos */home/azureuser/registos/* diretório na VM do Azure para o diretório /tmp dos locais:

```bash
scp -r azureuser@myserver.eastus.cloudapp.com:/home/azureuser/logs/. /tmp/
```

O `-r` cli sinalizador instrui o SCP para os ficheiros e diretórios a partir do ponto do diretório listados no comando de cópia de recursivamente.  Observe também que a sintaxe de linha de comando é semelhante a um `cp` copiar o comando.

## <a name="next-steps"></a>Passos Seguintes

* [Gerir utilizadores, SSH e verificar ou reparar discos em VMs do Linux do Azure com a extensão VMAccess](using-vmaccess-extension.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
