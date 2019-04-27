---
title: Criar e utilizar um par de chaves SSH para VMs do Linux no Azure | Microsoft Docs
description: Como criar e utilizar um par de chaves públicas-privadas SSH para VMs do Linux no Azure para melhorar a segurança do processo de autenticação.
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 34ae9482-da3e-4b2d-9d0d-9d672aa42498
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 09/11/2018
ms.author: cynthn
ms.openlocfilehash: d442d09c8c8ded3aa50faf74e28c8d95ded24a5e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60614411"
---
# <a name="quick-steps-create-and-use-an-ssh-public-private-key-pair-for-linux-vms-in-azure"></a>Passos rápidos: Criar e utilizar um par de chaves públicas-privadas SSH para VMs do Linux no Azure

Com um par de chaves secure shell (SSH), pode criar máquinas virtuais (VMs) no Azure que utilizar chaves SSH para autenticação, eliminando a necessidade de palavras-passe para iniciar sessão. Este artigo mostra-lhe como gerar e utilizar um par de ficheiros de chaves públicas-privadas de SSH para VMs do Linux rapidamente. Pode concluir estes passos com o Azure Cloud Shell, um macOS ou anfitrião Linux, o subsistema Windows para Linux e outras ferramentas que suportam o OpenSSH. 

> [!NOTE]
> As VMs criadas com chaves SSH são, por predefinição, configuradas com as palavras-passe desativadas, que aumenta muito a dificuldade de ataques de adivinhação de força bruta. 

Para obter mais informações e exemplos, consulte [pares de chaves de passos detalhados para criar SSH](create-ssh-keys-detailed.md).

Para outras formas de gerar e utilizar chaves SSH num computador Windows, consulte [chaves de como utilizar SSH com Windows no Azure](ssh-from-windows.md).

[!INCLUDE [virtual-machines-common-ssh-support](../../../includes/virtual-machines-common-ssh-support.md)]

## <a name="create-an-ssh-key-pair"></a>Criar um par de chaves SSH

Utilize o `ssh-keygen` comando para gerar ficheiros de chaves públicos e privados SSH. Por predefinição, estes ficheiros são criados no diretório ~/.ssh. Pode especificar uma localização diferente e uma palavra-passe opcional (*frase de acesso*) para aceder ao ficheiro de chave privado. Se existir um par de chaves SSH com o mesmo nome na localização especificada, esses arquivos são substituídos.

O comando seguinte cria um par de chaves SSH com a criptografia RSA e um comprimento de bits de 2048:

```bash
ssh-keygen -t rsa -b 2048
```

Se utilizar o [CLI do Azure](/cli/azure) para criar a sua VM com o [criar az vm](/cli/azure/vm#az-vm-create) comando, pode gerar opcionalmente públicos e privados ficheiros de chaves SSH com o `--generate-ssh-keys` opção. Os ficheiros de chave são armazenados no diretório ~/.ssh, a menos que especificado, caso contrário, com o `--ssh-dest-key-path` opção. O `--generate-ssh-keys` opção não irá substituir ficheiros de chaves existentes, em vez disso, devolver um erro. No comando seguinte, substitua *VMname* e *RGname* pelos seus próprios valores:

```azurecli
az vm create --name VMname --resource-group RGname --generate-ssh-keys 
```

## <a name="provide-an-ssh-public-key-when-deploying-a-vm"></a>Fornecer uma chave pública SSH ao implementar uma VM

Para criar uma VM do Linux utilizar chaves SSH para autenticação, especifique a chave pública SSH ao criar a VM com o portal do Azure, CLI do Azure, modelos Azure Resource Manager ou outros métodos:

* [Criar uma máquina virtual Linux com o portal do Azure](quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Criar uma máquina virtual Linux com a CLI do Azure](quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Criar uma VM do Linux com um modelo do Azure](create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

Se não estiver familiarizado com o formato de uma chave pública SSH, pode apresentar a chave pública com o seguinte procedimento `cat` comando, substituindo `~/.ssh/id_rsa.pub` com o caminho e nome de ficheiro do seu próprio ficheiro de chave pública, se necessário:

```bash
cat ~/.ssh/id_rsa.pub
```

Um valor de chave público típico é semelhante a este exemplo:

```
ssh-rsa AAAAB3NzaC1yc2EAABADAQABAAACAQC1/KanayNr+Q7ogR5mKnGpKWRBQU7F3Jjhn7utdf7Z2iUFykaYx+MInSnT3XdnBRS8KhC0IP8ptbngIaNOWd6zM8hB6UrcRTlTpwk/SuGMw1Vb40xlEFphBkVEUgBolOoANIEXriAMvlDMZsgvnMFiQ12tD/u14cxy1WNEMAftey/vX3Fgp2vEq4zHXEliY/sFZLJUJzcRUI0MOfHXAuCjg/qyqqbIuTDFyfg8k0JTtyGFEMQhbXKcuP2yGx1uw0ice62LRzr8w0mszftXyMik1PnshRXbmE2xgINYg5xo/ra3mq2imwtOKJpfdtFoMiKhJmSNHBSkK7vFTeYgg0v2cQ2+vL38lcIFX4Oh+QCzvNF/AXoDVlQtVtSqfQxRVG79Zqio5p12gHFktlfV7reCBvVIhyxc2LlYUkrq4DHzkxNY5c9OGSHXSle9YsO3F1J5ip18f6gPq4xFmo6dVoJodZm9N0YMKCkZ4k1qJDESsJBk2ujDPmQQeMjJX3FnDXYYB182ZCGQzXfzlPDC29cWVgDZEXNHuYrOLmJTmYtLZ4WkdUhLLlt5XsdoKWqlWpbegyYtGZgeZNRtOOdN6ybOPJqmYFd2qRtb4sYPniGJDOGhx4VodXAjT09omhQJpE6wlZbRWDvKC55R2d/CSPHJscEiuudb+1SG2uA/oik/WQ== username@domainname
```

Se copiar e colar o conteúdo do ficheiro de chave pública para utilizar no portal do Azure ou um modelo do Resource Manager, certifique-se de que não copia nenhum espaço em branco à direita. Para copiar uma chave pública no macOS, pode encaminhar o ficheiro de chave pública para **pbcopy**. Da mesma forma no Linux, pode encaminhar o ficheiro de chave pública para programas, como **xclip**.

A chave pública que colocar na sua VM do Linux no Azure é armazenada por predefinição em ~ /.ssh/id_rsa.pub, a menos que especificado numa localização diferente quando criou o par de chaves. Para utilizar o [CLI 2.0 do Azure](/cli/azure) para criar a sua VM com uma chave pública existente, especificar o valor e, opcionalmente, a localização com chave pública a [criar az vm](/cli/azure/vm#az-vm-create) comando com o `--ssh-key-value` opção. No comando seguinte, substitua *VMname*, *RGname*, e *keyFile* pelos seus próprios valores:

```azurecli
az vm create --name VMname --resource-group RGname --ssh-key-value @keyFile
```

## <a name="ssh-into-your-vm"></a>Aceder através de SSH à VM

Com a chave pública implementada na sua VM do Azure e a chave privada no sistema local, SSH à VM com o endereço IP ou nome DNS da sua VM. No comando seguinte, substitua *azureuser* e *myvm.westus.cloudapp.azure.com* com o nome de utilizador de administrador e o nome de domínio completamente qualificado (ou endereço IP):

```bash
ssh azureuser@myvm.westus.cloudapp.azure.com
```

Se tiver especificado uma frase de acesso quando criou o par de chaves, introduza essa frase de acesso quando lhe for pedido durante o processo de início de sessão. A VM é adicionada ao ficheiro ~/.ssh/known_hosts e não precisará para ligar novamente até a chave pública nas alterações de VM do Azure ou o nome do servidor é removido do ~/.ssh/known_hosts.

Se a VM estiver a utilizar a política de acesso just-in-time, terá de pedir acesso antes de poder ligar à VM. Para obter mais informações sobre a política de acesso just-in-time, consulte [gerir o acesso de máquina virtual utilizando o apenas na política de tempo](../../security-center/security-center-just-in-time.md).

## <a name="next-steps"></a>Passos Seguintes

* Para obter mais informações sobre como trabalhar com pares de chaves SSH, veja [pares de chaves de obter os passos detalhados para criar e gerir SSH](create-ssh-keys-detailed.md).

* Se tiver dificuldades com ligações SSH para VMs do Azure, veja [resolver problemas de SSH ligações a uma VM do Linux do Azure](troubleshoot-ssh-connection.md).
