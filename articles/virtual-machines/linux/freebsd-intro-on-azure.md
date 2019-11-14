---
title: Introdução ao FreeBSD no Azure
description: Saiba mais sobre como usar máquinas virtuais FreeBSD no Azure
services: virtual-machines-linux
documentationcenter: ''
author: thomas1206
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 32b87a5f-d024-4da0-8bf0-77e233d1422b
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 09/13/2017
ms.author: huishao
ms.openlocfilehash: d243806d619aa6d7328220eae53804d734737489
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74036112"
---
# <a name="introduction-to-freebsd-on-azure"></a>Introdução ao FreeBSD no Azure
Este artigo fornece uma visão geral da execução de uma máquina virtual FreeBSD no Azure.

## <a name="overview"></a>Descrição geral
O FreeBSD para Microsoft Azure é um sistema operacional de computador avançado usado para alimentar servidores, desktops e plataformas integradas modernos.

A Microsoft Corporation está disponibilizando imagens do FreeBSD no Azure com o [agente convidado da VM do Azure](https://github.com/Azure/WALinuxAgent/) pré-configurado. Atualmente, as seguintes versões do FreeBSD são oferecidas como imagens pela Microsoft:

- [FreeBSD 10,4 no Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.FreeBSD104)
- [FreeBSD 11,2 no Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.FreeBSD112)
- [FreeBSD 12,0 no Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.FreeBSD120)

O agente é responsável pela comunicação entre a VM FreeBSD e a malha do Azure para operações como o provisionamento da VM na primeira utilização (nome de usuário, senha ou chave SSH, nome do host, etc.) e habilitação da funcionalidade para extensões de VM seletivas.

Para versões futuras do FreeBSD, a estratégia é manter-se atualizado e disponibilizar as versões mais recentes logo depois que elas forem publicadas pela equipe de engenharia de versão do FreeBSD.

### <a name="create-a-freebsd-vm-through-azure-cli-on-freebsd"></a>Criar uma VM FreeBSD por meio de CLI do Azure no FreeBSD
Primeiro, você precisa instalar [CLI do Azure](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) por meio do comando a seguir em um computador FreeBSD.

```bash 
curl -L https://aka.ms/InstallAzureCli | bash
```

Se o bash não estiver instalado em seu computador FreeBSD, execute o comando a seguir antes da instalação. 

```bash
sudo pkg install bash
```

Se o Python não estiver instalado em seu computador FreeBSD, execute os comandos a seguir antes da instalação. 

```bash
sudo pkg install python35
cd /usr/local/bin 
sudo rm /usr/local/bin/python 
sudo ln -s /usr/local/bin/python3.5 /usr/local/bin/python
```

Durante a instalação, você será solicitado a `Modify profile to update your $PATH and enable shell/tab completion now? (Y/n)`. Se você responder `y` e inserir `/etc/rc.conf` como `a path to an rc file to update`, poderá encontrar o problema `ERROR: [Errno 13] Permission denied`. Para resolver esse problema, você deve conceder o direito de gravação ao usuário atual no arquivo `etc/rc.conf`.

Agora você pode entrar no Azure e criar sua VM do FreeBSD. Veja abaixo um exemplo para criar uma VM FreeBSD 11,0. Você também pode adicionar o parâmetro `--public-ip-address-dns-name` com um nome DNS globalmente exclusivo para um IP público recém-criado. 

```azurecli
az login 
az group create --name myResourceGroup --location eastus
az vm create --name myFreeBSD11 \
    --resource-group myResourceGroup \
    --image MicrosoftOSTC:FreeBSD:11.0:latest \
    --admin-username azureuser \
    --generate-ssh-keys
```

Em seguida, você pode entrar em sua VM do FreeBSD por meio do endereço IP que foi impresso na saída da implantação acima. 

```bash
ssh azureuser@xx.xx.xx.xx -i /etc/ssh/ssh_host_rsa_key
```   

## <a name="vm-extensions-for-freebsd"></a>Extensões de VM para FreeBSD
Veja a seguir as extensões de VM com suporte no FreeBSD.

### <a name="vmaccess"></a>VMAccess
A extensão [VMAccess](https://github.com/Azure/azure-linux-extensions/tree/master/VMAccess) pode:

* Redefina a senha do usuário sudo original.
* Crie um novo usuário sudo com a senha especificada.
* Defina a chave de host pública com a chave fornecida.
* Redefina a chave de host pública fornecida durante o provisionamento da VM se a chave do host não for fornecida.
* Abra a porta SSH (22) e restaure o sshd_config se reset_ssh estiver definido como true.
* Remova o usuário existente.
* Verifique os discos.
* Reparar um disco adicionado.

### <a name="customscript"></a>CustomScript
A extensão [CustomScript](https://github.com/Azure/azure-linux-extensions/tree/master/CustomScript) pode:

* Se fornecido, baixe os scripts personalizados do armazenamento do Azure ou do armazenamento público externo (por exemplo, GitHub).
* Execute o script de ponto de entrada.
* Suporte a comandos embutidos.
* Converta a nova linha do estilo do Windows em scripts do Shell e Python automaticamente.
* Remova a BOM em scripts de shell e Python automaticamente.
* Proteja dados confidenciais no CommandToExecute.

> [!NOTE]
> A VM FreeBSD só dá suporte a CustomScript versão 1. x por enquanto.  

## <a name="authentication-user-names-passwords-and-ssh-keys"></a>Autenticação: nomes de usuário, senhas e chaves SSH
Ao criar uma máquina virtual FreeBSD usando o portal do Azure, você deve fornecer um nome de usuário, uma senha ou uma chave pública SSH.
Os nomes de usuário para implantar uma máquina virtual FreeBSD no Azure não devem corresponder aos nomes de contas do sistema (UID < 100) já presentes na máquina virtual ("raiz", por exemplo).
Atualmente, há suporte apenas para a chave RSA SSH. Uma chave SSH de várias linhas deve começar com `---- BEGIN SSH2 PUBLIC KEY ----` e terminar com `---- END SSH2 PUBLIC KEY ----`.

## <a name="obtaining-superuser-privileges"></a>Obtendo privilégios de superusuário
A conta de usuário especificada durante a implantação da instância de máquina virtual no Azure é uma conta com privilégios. O pacote do sudo foi instalado na imagem do FreeBSD publicada.
Depois de fazer logon por meio dessa conta de usuário, você pode executar comandos como raiz usando a sintaxe do comando.

```
$ sudo <COMMAND>
```

Opcionalmente, você pode obter um shell raiz usando `sudo -s`.

## <a name="known-issues"></a>Problemas conhecidos
A versão 2.2.2 do [agente convidado da VM do Azure](https://github.com/Azure/WALinuxAgent/) tem um [problema conhecido](https://github.com/Azure/WALinuxAgent/pull/517) que causa a falha de provisionamento para a VM FreeBSD no Azure. A correção foi capturada pelo [agente convidado da VM do Azure](https://github.com/Azure/WALinuxAgent/) versão 2.2.3 e versões posteriores. 

## <a name="next-steps"></a>Passos seguintes
* Vá para o [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.FreeBSD112) para criar uma VM FreeBSD.
