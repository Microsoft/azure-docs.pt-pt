---
title: Mover arquivos de e para VMs Linux do Azure com o SCP | Microsoft Docs
description: Mova arquivos de e para uma VM do Linux com segurança no Azure usando o SCP e um par de chaves SSH.
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
ms.topic: article
ms.date: 07/12/2017
ms.author: danlep
ms.subservice: disks
ms.openlocfilehash: 594ce696245cdd688583f8565487844197cd0b2a
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70083760"
---
# <a name="move-files-to-and-from-a-linux-vm-using-scp"></a>Mover arquivos de e para uma VM do Linux usando o SCP

Este artigo mostra como mover arquivos de sua estação de trabalho para uma VM Linux do Azure ou de uma VM Linux do Azure para sua estação de trabalho, usando a cópia segura (SCP). Mover arquivos entre a estação de trabalho e uma VM Linux, de modo rápido e seguro, é essencial para gerenciar sua infraestrutura do Azure. 

Para este artigo, você precisa de uma VM Linux implantada no Azure usando [arquivos de chave SSH pública e privada](mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Você também precisa de um cliente SCP para seu computador local. Ele é criado com base no SSH e incluído no shell bash padrão da maioria dos computadores Linux e Mac e em alguns shells do Windows.

## <a name="quick-commands"></a>Comandos rápidos

Copiar um arquivo para a VM Linux

```bash
scp file azureuser@azurehost:directory/targetfile
```

Copiar um arquivo da VM Linux

```bash
scp azureuser@azurehost:directory/file targetfile
```

## <a name="detailed-walkthrough"></a>Instruções detalhadas

Como exemplos, movemos um arquivo de configuração do Azure para uma VM do Linux e obtemos um diretório de arquivos de log, usando as chaves SCP e SSH.   

## <a name="ssh-key-pair-authentication"></a>Autenticação do par de chaves SSH

O SCP usa SSH para a camada de transporte. O SSH manipula a autenticação no host de destino e move o arquivo em um túnel criptografado fornecido por padrão com o SSH. Para autenticação SSH, nomes de acessadores e senhas podem ser usados. No entanto, a autenticação SSH pública e a chave privada são recomendadas como uma prática recomendada de segurança. Depois que o SSH tiver autenticado a conexão, o SCP começará a copiar o arquivo. Usando uma chave pública `~/.ssh/config` e privada de SSH configurada corretamente, a conexão de SCP pode ser estabelecida usando apenas um nome de servidor (ou endereço IP). Se você tiver apenas uma chave SSH, o SCP a procurará no `~/.ssh/` diretório e a usará por padrão para fazer logon na VM.

Para obter mais informações sobre como `~/.ssh/config` configurar suas chaves pública e privada SSH, consulte [criar chaves SSH](mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="scp-a-file-to-a-linux-vm"></a>SCP um arquivo para uma VM do Linux

Para o primeiro exemplo, copiamos um arquivo de configuração do Azure para uma VM do Linux que é usada para implantar a automação. Como esse arquivo contém as credenciais da API do Azure, que incluem segredos, a segurança é importante. O túnel criptografado fornecido pelo SSH protege o conteúdo do arquivo.

O comando a seguir copia o arquivo local *. Azure/config* para uma VM do Azure com o FQDN *MyServer.eastus.cloudapp.Azure.com*. O nome de usuário administrador na VM do Azure é *azureuser*. O arquivo é direcionado para o diretório */Home/azureuser/* . Substitua seus próprios valores neste comando.

```bash
scp ~/.azure/config azureuser@myserver.eastus.cloudapp.com:/home/azureuser/config
```

## <a name="scp-a-directory-from-a-linux-vm"></a>SCP um diretório de uma VM do Linux

Para este exemplo, copiamos um diretório de arquivos de log da VM do Linux para sua estação de trabalho. Um arquivo de log pode ou não conter dados confidenciais ou secretos. No entanto, o uso do SCP garante que o conteúdo dos arquivos de log seja criptografado. Usar o SCP para transferir os arquivos é a maneira mais fácil de obter o diretório de log e os arquivos para sua estação de trabalho enquanto também está em segurança.

O comando a seguir copia os arquivos no diretório */Home/azureuser/logs/* na VM do Azure para o diretório/tmp local:

```bash
scp -r azureuser@myserver.eastus.cloudapp.com:/home/azureuser/logs/. /tmp/
```

O `-r` sinalizador da CLI instrui o SCP a copiar recursivamente os arquivos e diretórios do ponto do diretório listado no comando.  Observe também que a sintaxe de linha de comando é semelhante a `cp` um comando de cópia.

## <a name="next-steps"></a>Passos Seguintes

* [Gerenciar usuários, SSH e verificar ou reparar discos em VMs Linux do Azure usando a extensão VMAccess](using-vmaccess-extension.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
