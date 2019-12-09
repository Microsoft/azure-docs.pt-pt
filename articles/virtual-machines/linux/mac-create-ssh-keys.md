---
title: Criar e usar um par de chaves SSH para VMs do Linux no Azure
description: Como criar e usar um par de chaves SSH pública-privada para VMs do Linux no Azure para melhorar a segurança do processo de autenticação.
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 34ae9482-da3e-4b2d-9d0d-9d672aa42498
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 12/06/2019
ms.author: cynthn
ms.openlocfilehash: 6cf636e7d7ee35680c1da872b186748c333a81dc
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/08/2019
ms.locfileid: "74929996"
---
# <a name="quick-steps-create-and-use-an-ssh-public-private-key-pair-for-linux-vms-in-azure"></a>Etapas rápidas: criar e usar um par de chaves SSH pública-privada para VMs do Linux no Azure

Com um par de chaves SSH (Secure Shell), você pode criar VMs (máquinas virtuais) no Azure que usam chaves SSH para autenticação, eliminando a necessidade de senhas para entrar. Este artigo mostra como gerar e usar rapidamente um par de arquivos de chave pública-privada SSH para VMs Linux. Você pode concluir essas etapas com o Azure Cloud Shell, um host macOS ou Linux, o subsistema do Windows para Linux e outras ferramentas que dão suporte ao OpenSSH. 

> [!NOTE]
> As VMs criadas usando chaves SSH são, por padrão, configuradas com senhas desabilitadas, o que aumenta muito a dificuldade de ataques de adivinhação de força bruta. 

Para obter mais informações e exemplos, consulte [etapas detalhadas para criar pares de chaves SSH](create-ssh-keys-detailed.md).

Para obter outras maneiras de gerar e usar chaves SSH em um computador Windows, consulte [como usar chaves SSH com o Windows no Azure](ssh-from-windows.md).

[!INCLUDE [virtual-machines-common-ssh-support](../../../includes/virtual-machines-common-ssh-support.md)]

## <a name="create-an-ssh-key-pair"></a>Criar um par de chaves SSH

Use o comando `ssh-keygen` para gerar arquivos de chave pública e privada SSH. Por padrão, esses arquivos são criados no diretório ~/.ssh Você pode especificar um local diferente e uma senha (*frase secreta*) opcional para acessar o arquivo de chave privada. Se um par de chaves SSH com o mesmo nome existir no local especificado, esses arquivos serão substituídos.

O comando a seguir cria um par de chaves SSH usando a criptografia RSA e um comprimento de bit de 4096:

```bash
ssh-keygen -m PEM -t rsa -b 4096
```

Se você usar o [CLI do Azure](/cli/azure) para criar sua VM com o comando [AZ VM Create](/cli/azure/vm#az-vm-create) , você poderá, opcionalmente, gerar arquivos de chave pública e privada SSH usando a opção `--generate-ssh-keys`. Os arquivos de chave são armazenados no diretório ~/.ssh, a menos que especificado de outra forma com a opção `--ssh-dest-key-path`. A opção `--generate-ssh-keys` não substituirá os arquivos de chave existentes, em vez disso, retornando um erro. No comando a seguir, substitua *VMname* e *RGname* pelos seus próprios valores:

```azurecli
az vm create --name VMname --resource-group RGname --generate-ssh-keys 
```

## <a name="provide-an-ssh-public-key-when-deploying-a-vm"></a>Fornecer uma chave pública SSH ao implantar uma VM

Para criar uma VM do Linux que usa chaves SSH para autenticação, especifique sua chave pública SSH ao criar a VM usando o portal do Azure, CLI do Azure, modelos de Azure Resource Manager ou outros métodos:

* [Criar uma máquina virtual Linux com o portal do Azure](quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Criar uma máquina virtual Linux com o CLI do Azure](quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Criar uma VM do Linux usando um modelo do Azure](create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

Se você não estiver familiarizado com o formato de uma chave pública SSH, poderá exibir sua chave pública com o comando `cat` a seguir, substituindo `~/.ssh/id_rsa.pub` pelo caminho e nome do arquivo de chave pública, se necessário:

```bash
cat ~/.ssh/id_rsa.pub
```

Um valor típico de chave pública é semelhante a este exemplo:

```
ssh-rsa AAAAB3NzaC1yc2EAABADAQABAAACAQC1/KanayNr+Q7ogR5mKnGpKWRBQU7F3Jjhn7utdf7Z2iUFykaYx+MInSnT3XdnBRS8KhC0IP8ptbngIaNOWd6zM8hB6UrcRTlTpwk/SuGMw1Vb40xlEFphBkVEUgBolOoANIEXriAMvlDMZsgvnMFiQ12tD/u14cxy1WNEMAftey/vX3Fgp2vEq4zHXEliY/sFZLJUJzcRUI0MOfHXAuCjg/qyqqbIuTDFyfg8k0JTtyGFEMQhbXKcuP2yGx1uw0ice62LRzr8w0mszftXyMik1PnshRXbmE2xgINYg5xo/ra3mq2imwtOKJpfdtFoMiKhJmSNHBSkK7vFTeYgg0v2cQ2+vL38lcIFX4Oh+QCzvNF/AXoDVlQtVtSqfQxRVG79Zqio5p12gHFktlfV7reCBvVIhyxc2LlYUkrq4DHzkxNY5c9OGSHXSle9YsO3F1J5ip18f6gPq4xFmo6dVoJodZm9N0YMKCkZ4k1qJDESsJBk2ujDPmQQeMjJX3FnDXYYB182ZCGQzXfzlPDC29cWVgDZEXNHuYrOLmJTmYtLZ4WkdUhLLlt5XsdoKWqlWpbegyYtGZgeZNRtOOdN6ybOPJqmYFd2qRtb4sYPniGJDOGhx4VodXAjT09omhQJpE6wlZbRWDvKC55R2d/CSPHJscEiuudb+1SG2uA/oik/WQ== username@domainname
```

Se você copiar e colar o conteúdo do arquivo de chave pública para usar no portal do Azure ou em um modelo do Resource Manager, certifique-se de não copiar nenhum espaço em branco à direita. Para copiar uma chave pública no macOS, você pode canalizar o arquivo de chave pública para **pbcopy**. Da mesma forma, no Linux, você pode canalizar o arquivo de chave pública para programas como **XCLIP**.

A chave pública que você coloca em sua VM Linux no Azure é, por padrão, armazenada em ~/.ssh/id_rsa. pub, a menos que você tenha especificado um local diferente quando criou o par de chaves. Para usar o [CLI do Azure 2,0](/cli/azure) para criar sua VM com uma chave pública existente, especifique o valor e, opcionalmente, o local dessa chave pública usando o comando [AZ VM Create](/cli/azure/vm#az-vm-create) com a opção `--ssh-key-value`. No comando a seguir, substitua *VMname*, *RGname*e *keyfile* pelos seus próprios valores:

```azurecli
az vm create --name VMname --resource-group RGname --ssh-key-value @keyFile
```

## <a name="ssh-into-your-vm"></a>Aceder através de SSH à VM

Com a chave pública implantada em sua VM do Azure e a chave privada em seu sistema local, use SSH em sua VM usando o endereço IP ou o nome DNS da sua VM. No comando a seguir, substitua *azureuser* e *MyVM.westus.cloudapp.Azure.com* pelo nome de usuário do administrador e pelo nome de domínio totalmente qualificado (ou endereço IP):

```bash
ssh azureuser@myvm.westus.cloudapp.azure.com
```

Se você especificou uma senha quando criou o par de chaves, insira essa senha quando solicitado durante o processo de logon. A VM é adicionada ao seu arquivo ~/.ssh/known_hosts, e você não será solicitado a se conectar novamente até que a chave pública na VM do Azure seja alterada ou o nome do servidor seja removido de ~/.ssh/known_hosts.

Se a VM estiver usando a política de acesso just-in-time, você precisará solicitar acesso antes de poder se conectar à VM. Para obter mais informações sobre a política just-in-time, consulte [gerenciar o acesso à máquina virtual usando a política just in time](../../security-center/security-center-just-in-time.md).

## <a name="next-steps"></a>Passos seguintes

* Para obter mais informações sobre como trabalhar com pares de chaves SSH, consulte [etapas detalhadas para criar e gerenciar pares de chaves SSH](create-ssh-keys-detailed.md).

* Se você tiver dificuldades com as conexões SSH com as VMs do Azure, consulte [solucionar problemas de conexões SSH para uma VM Linux do Azure](troubleshoot-ssh-connection.md).
