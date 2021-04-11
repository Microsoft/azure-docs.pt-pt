---
title: Introdução ao FreeBSD no Azure
description: Saiba como utilizar máquinas virtuais FreeBSD no Azure
author: thomas1206
ms.service: virtual-machines
ms.collection: linux
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 09/13/2017
ms.author: mimckitt
ms.openlocfilehash: da51fabaaa3c02137770f0b2d9a851b1f6702980
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105044054"
---
# <a name="introduction-to-freebsd-on-azure"></a>Introdução ao FreeBSD no Azure
Este artigo fornece uma visão geral de executar uma máquina virtual FreeBSD em Azure.

## <a name="overview"></a>Descrição Geral
FreeBSD para o Microsoft Azure é um sistema operativo avançado de computador usado para alimentar servidores modernos, desktops e plataformas incorporadas.

A Microsoft Corporation está a disponibilizar imagens do FreeBSD no Azure com o [Azure VM Guest Agent](https://github.com/Azure/WALinuxAgent/) pré-configurado. Atualmente, as seguintes versões FreeBSD são oferecidas como imagens pela Microsoft:

- FreeBSD 10.4 no Mercado Azure
- FreeBSD 11.2 no Mercado Azure
- FreeBSD 12.0 no Mercado Azure

O agente é responsável pela comunicação entre o FreeBSD VM e o tecido Azure para operações como o fornecimento do VM na primeira utilização (nome de utilizador, palavra-passe ou chave SSH, nome do anfitrião, etc.) e ativar a funcionalidade para extensões VM seletivas.

Quanto às futuras versões do FreeBSD, a estratégia é manter-se atual e disponibilizar as mais recentes versões pouco depois de serem publicadas pela equipa de engenharia de lançamentos FreeBSD.

### <a name="create-a-freebsd-vm-through-azure-cli-on-freebsd"></a>Criar um FreeBSD VM através do Azure CLI em FreeBSD
Primeiro, tem de instalar [o Azure CLI](/cli/azure/get-started-with-azure-cli) seguindo o comando numa máquina FreeBSD.

```bash 
curl -L https://aka.ms/InstallAzureCli | bash
```

Se não for instalada uma bash na sua máquina FreeBSD, corra seguindo o comando antes da instalação. 

```bash
sudo pkg install bash
```

Se a pitão não for instalada na sua máquina FreeBSD, corram seguindo os comandos antes da instalação. 

```bash
sudo pkg install python38
cd /usr/local/bin 
sudo rm /usr/local/bin/python 
sudo ln -s /usr/local/bin/python3.8 /usr/local/bin/python
```

Durante a instalação, é-lhe pedido `Modify profile to update your $PATH and enable shell/tab completion now? (Y/n)` . Se responder `y` e entrar como , pode encontrar o problema `/etc/rc.conf` `a path to an rc file to update` `ERROR: [Errno 13] Permission denied` . Para resolver este problema, deverá conceder o direito de escrita ao utilizador atual contra o ficheiro `etc/rc.conf` .

Agora pode iniciar sessão no Azure e criar o seu FreeBSD VM. Abaixo está um exemplo para criar um FreeBSD 11.0 VM. Também pode adicionar o parâmetro `--public-ip-address-dns-name` com um nome DNS globalmente único para um IP público recém-criado. 

```azurecli
az login 
az group create --name myResourceGroup --location eastus
az vm create --name myFreeBSD11 \
    --resource-group myResourceGroup \
    --image MicrosoftOSTC:FreeBSD:11.0:latest \
    --admin-username azureuser \
    --generate-ssh-keys
```

Em seguida, pode iniciar sação no seu FreeBSD VM através do endereço ip que imprimiu na saída de uma implementação acima. 

```bash
ssh azureuser@xx.xx.xx.xx -i /etc/ssh/ssh_host_rsa_key
```   

## <a name="vm-extensions-for-freebsd"></a>Extensões VM para FreeBSD
Seguem-se extensões VM suportadas em FreeBSD.

### <a name="vmaccess"></a>VMAccess
A extensão [VMAccess](https://github.com/Azure/azure-linux-extensions/tree/master/VMAccess) pode:

* Repor a palavra-passe do utilizador original do sudo.
* Crie um novo utilizador de sudo com a palavra-passe especificada.
* Coloque a chave de anfitrião pública com a chave dada.
* Repor a chave de anfitrião pública fornecida durante o provisionamento em VM se a chave de anfitrião não for fornecida.
* Abra a porta SSH (22) e restaure o sshd_config se reset_ssh estiver definido como verdadeiro.
* Remover o utilizador existente.
* Verifique os discos.
* Repare um disco adicionado.

### <a name="customscript"></a>PersonalScript
A extensão [CustomScript](https://github.com/Azure/azure-linux-extensions/tree/master/CustomScript) pode:

* Se fornecido, faça o download dos scripts personalizados do Azure Storage ou do armazenamento público externo (por exemplo, GitHub).
* Executar o roteiro do ponto de entrada.
* Suporte comandos inline.
* Converta automaticamente a nova linha de estilo Windows em shell e scripts Python.
* Remova os scripts BOM na concha e python automaticamente.
* Proteja dados sensíveis no CommandToExecute.

> [!NOTE]
> O FreeBSD VM só suporta a versão 1.x do CustomScript.  

## <a name="authentication-user-names-passwords-and-ssh-keys"></a>Autenticação: nomes de utilizador, palavras-passe e chaves SSH
Quando estiver a criar uma máquina virtual FreeBSD utilizando o portal Azure, tem de fornecer um nome de utilizador, palavra-passe ou chave pública SSH.
Os nomes dos utilizadores para a implementação de uma máquina virtual FreeBSD no Azure não devem corresponder aos nomes das contas do sistema (UID <100) já presentes na máquina virtual ("raiz", por exemplo).
Atualmente, apenas a chave RSA SSH é suportada. Uma chave SSH multiline deve começar `---- BEGIN SSH2 PUBLIC KEY ----` com e terminar com `---- END SSH2 PUBLIC KEY ----` .

## <a name="obtaining-superuser-privileges"></a>Obtenção de privilégios super-mais
A conta de utilizador que é especificada durante a implementação de casos de máquinas virtuais no Azure é uma conta privilegiada. O pacote de sudo foi instalado na imagem freeBSD publicada.
Depois de iniciar sessão através desta conta de utilizador, pode executar os comandos como raiz utilizando a sintaxe de comando.

```
$ sudo <COMMAND>
```

Pode obter opcionalmente uma casca de raiz utilizando `sudo -s` .

## <a name="known-issues"></a>Problemas conhecidos
A versão 2.2.2 [do Agente Convidado Azure VM](https://github.com/Azure/WALinuxAgent/) tem um [problema conhecido](https://github.com/Azure/WALinuxAgent/pull/517) que causa a falha de provisão para FreeBSD VM em Azure. A correção foi capturada pela versão 2.2.3 do [Azure VM Guest Agent](https://github.com/Azure/WALinuxAgent/) e posteriormente lançada. 

## <a name="next-steps"></a>Passos seguintes
* Vá ao [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/thefreebsdfoundation.freebsd-12_2?tab=Overview) para criar um VM FreeBSD.
