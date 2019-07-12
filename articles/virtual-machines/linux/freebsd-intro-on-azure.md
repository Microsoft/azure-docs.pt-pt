---
title: Introdução ao FreeBSD no Azure | Documentos da Microsoft
description: Saiba como utilizar máquinas de virtuais de FreeBSD no Azure
services: virtual-machines-linux
documentationcenter: ''
author: thomas1206
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 32b87a5f-d024-4da0-8bf0-77e233d1422b
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 09/13/2017
ms.author: huishao
ms.openlocfilehash: 248588ad43f3b3beefa35de468a21cecedc8913d
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/09/2019
ms.locfileid: "67667794"
---
# <a name="introduction-to-freebsd-on-azure"></a>Introdução ao FreeBSD no Azure
Este artigo fornece uma descrição geral da execução de uma máquina virtual de FreeBSD no Azure.

## <a name="overview"></a>Descrição geral
FreeBSD para o Microsoft Azure é um sistema operativo do computador avançadas usada para servidores de power modernos, áreas de trabalho e incorporados plataformas.

Microsoft Corporation é disponibilizar imagens do FreeBSD no Azure com o [agente convidado da VM do Azure](https://github.com/Azure/WALinuxAgent/) pré-configuradas. Atualmente, as seguintes versões do FreeBSD são oferecidas como imagens pela Microsoft:

- LANÇAMENTO de 10.3 FreeBSD
- LANÇAMENTO de 10.4 FreeBSD
- LANÇAMENTO de 11.1 FreeBSD

O agente é responsável pela comunicação entre a VM de FreeBSD e os recursos de infraestrutura do Azure para operações como o aprovisionamento da VM na primeira utilização (nome de utilizador, palavra-passe ou chave SSH, nome de anfitrião, etc.) e ativar a funcionalidade de extensões de VM seletivas.

E para versões futuras do FreeBSD, a estratégia é de se manter atualizado e tornar as versões mais recentes disponíveis pouco tempo depois de serem publicados pela equipe de engenharia de versão do FreeBSD.

## <a name="deploying-a-freebsd-virtual-machine"></a>Implementar uma máquina virtual de FreeBSD
Implementar uma máquina virtual de FreeBSD é um processo simples através de uma imagem do Azure Marketplace no portal do Azure:

- [FreeBSD 10.4 no Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.FreeBSD104)
- [FreeBSD 11.2 no Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.FreeBSD112)

### <a name="create-a-freebsd-vm-through-azure-cli-on-freebsd"></a>Criar uma VM de FreeBSD através da CLI do Azure no FreeBSD
Primeiro tem de instalar [CLI do Azure](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) apesar do comando a seguir numa máquina de FreeBSD.

```bash 
curl -L https://aka.ms/InstallAzureCli | bash
```

Se bash não estiver instalado no seu computador FreeBSD, execute o comando antes da instalação a seguir. 

```bash
sudo pkg install bash
```

Se python não estiver instalado no seu computador FreeBSD, execute os seguintes comandos antes da instalação. 

```bash
sudo pkg install python35
cd /usr/local/bin 
sudo rm /usr/local/bin/python 
sudo ln -s /usr/local/bin/python3.5 /usr/local/bin/python
```

Durante a instalação, é-lhe perguntado `Modify profile to update your $PATH and enable shell/tab completion now? (Y/n)`. Se responder `y` e introduza `/etc/rc.conf` como `a path to an rc file to update`, pode satisfazer o problema `ERROR: [Errno 13] Permission denied`. Para resolver este problema, só deverá conceder a gravação diretamente para o utilizador atual com o arquivo `etc/rc.conf`.

Agora pode iniciar sessão no Azure e criar a sua VM FreeBSD. Segue-se um exemplo para criar uma VM de 11.0 FreeBSD. Também pode adicionar o parâmetro `--public-ip-address-dns-name` com um nome DNS globalmente exclusivo para um IP público criado recentemente. 

```azurecli
az login 
az group create --name myResourceGroup --location eastus
az vm create --name myFreeBSD11 \
    --resource-group myResourceGroup \
    --image MicrosoftOSTC:FreeBSD:11.0:latest \
    --admin-username azureuser \
    --generate-ssh-keys
```

Em seguida, pode iniciar sessão sua VM FreeBSD através do endereço ip que impresso na saída da acima de implementação. 

```bash
ssh azureuser@xx.xx.xx.xx -i /etc/ssh/ssh_host_rsa_key
```   

## <a name="vm-extensions-for-freebsd"></a>Extensões VM do FreeBSD
Seguem-se extensões VM suportadas no FreeBSD.

### <a name="vmaccess"></a>VMAccess
O [VMAccess](https://github.com/Azure/azure-linux-extensions/tree/master/VMAccess) extensão pode:

* Repor a palavra-passe do utilizador de sudo original.
* Crie um novo utilizador de sudo com a palavra-passe especificada.
* Defina a chave de anfitrião público com a chave, tendo em conta.
* Repor a chave de anfitrião público fornecida durante o aprovisionamento da VM se a chave do anfitrião não for fornecida.
* Abra a porta SSH (22) e restaurar o sshd_config se reset_ssh estiver definido como true.
* Remova o utilizador existente.
* Verificar os discos.
* Repare um disco adicionado.

### <a name="customscript"></a>CustomScript
O [CustomScript](https://github.com/Azure/azure-linux-extensions/tree/master/CustomScript) extensão pode:

* Se for fornecido, transferir os scripts personalizados do armazenamento do Azure ou armazenamento público externo (por exemplo, o GitHub).
* Execute o script de ponto de entrada.
* Suporta comandos inline.
* Converta automaticamente a nova linha de estilo do Windows no shell e scripts do Python.
* Remova automaticamente o BOM no shell e scripts do Python.
* Protege dados confidenciais em CommandToExecute.

> [!NOTE]
> FreeBSD VM só suporta CustomScript versão 1.x agora.  

## <a name="authentication-user-names-passwords-and-ssh-keys"></a>Autenticação: os nomes de utilizador, senhas e chaves SSH
Quando estiver a criar uma máquina virtual de FreeBSD com o portal do Azure, tem de fornecer um nome de utilizador, palavra-passe ou chave pública SSH.
Nomes de utilizador para implementar uma máquina virtual de FreeBSD no Azure não têm de corresponder nomes de contas do sistema (UID < 100) já está presente na máquina virtual ("raiz", por exemplo).
Atualmente, apenas a RSA chave SSH é suportada. Tem de começar com uma chave SSH com várias linhas `---- BEGIN SSH2 PUBLIC KEY ----` e terminar com `---- END SSH2 PUBLIC KEY ----`.

## <a name="obtaining-superuser-privileges"></a>Obter privilégios de Superutilizador
A conta de utilizador especificado durante a implementação de instância de máquina virtual no Azure é uma conta com privilégios. O pacote de sudo foi instalado na imagem FreeBSD publicada.
Depois que está conectado através desta conta de utilizador, pode executar comandos como raiz, utilizando a sintaxe de comando.

```
$ sudo <COMMAND>
```

Opcionalmente, pode obter um shell de raiz utilizando `sudo -s`.

## <a name="known-issues"></a>Problemas conhecidos
O [agente convidado da VM do Azure](https://github.com/Azure/WALinuxAgent/) versão 2.2.2 tem um [problema conhecido](https://github.com/Azure/WALinuxAgent/pull/517) que faz com que a falha de aprovisionamento para a VM de FreeBSD no Azure. A correção capturada pelos [agente convidado da VM do Azure](https://github.com/Azure/WALinuxAgent/) versão 2.2.3 e versões posteriores. 

## <a name="next-steps"></a>Passos Seguintes
* Aceda a [do Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.FreeBSD112) para criar uma VM de FreeBSD.
