---
title: Introdução ao FreeBSD no Azure
description: Saiba mais sobre a utilização de máquinas virtuais FreeBSD no Azure
author: thomas1206
ms.service: virtual-machines-linux
ms.topic: article
ms.workload: infrastructure-services
ms.date: 09/13/2017
ms.author: huishao
ms.openlocfilehash: fe64418e254289a29aafd155b92396082bff5b6a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78945091"
---
# <a name="introduction-to-freebsd-on-azure"></a>Introdução ao FreeBSD no Azure
Este artigo fornece uma visão geral de executar uma máquina virtual FreeBSD em Azure.

## <a name="overview"></a>Descrição geral
FreeBSD para o Microsoft Azure é um sistema operativo avançado para computadores usado para alimentar servidores modernos, desktops e plataformas incorporadas.

A Microsoft Corporation está a disponibilizar imagens do FreeBSD no Azure com o [Agente Convidado Azure VM](https://github.com/Azure/WALinuxAgent/) pré-configurado. Atualmente, as seguintes versões FreeBSD são oferecidas como imagens pela Microsoft:

- [FreeBSD 10.4 no Mercado Azure](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.FreeBSD104)
- [FreeBSD 11.2 no Mercado Azure](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.FreeBSD112)
- [FreeBSD 12.0 no Mercado Azure](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.FreeBSD120)

O agente é responsável pela comunicação entre o VM FreeBSD e o tecido Azure para operações como o fornecimento do VM na primeira utilização (nome de utilizador, palavra-passe ou chave SSH, nome do anfitrião, etc.) e a funcionalidade de habilitação para extensões vm seletivas.

Quanto às futuras versões do FreeBSD, a estratégia é manter-se atual e disponibilizar os mais recentes lançamentos pouco depois de serem publicados pela equipa de engenharia de lançamento saqueada do FreeBSD.

### <a name="create-a-freebsd-vm-through-azure-cli-on-freebsd"></a>Criar um VM FreeBSD através do Azure CLI no FreeBSD
Primeiro, é necessário instalar [o Azure CLI,](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) seguindo o comando numa máquina FreeBSD.

```bash 
curl -L https://aka.ms/InstallAzureCli | bash
```

Se a batida não for instalada na sua máquina FreeBSD, execute o comando de seguimento antes da instalação. 

```bash
sudo pkg install bash
```

Se não for instalada a pitão na sua máquina FreeBSD, execute os comandos antes da instalação. 

```bash
sudo pkg install python35
cd /usr/local/bin 
sudo rm /usr/local/bin/python 
sudo ln -s /usr/local/bin/python3.5 /usr/local/bin/python
```

Durante a instalação, `Modify profile to update your $PATH and enable shell/tab completion now? (Y/n)`é-lhe pedido . Se responder `y` e `/etc/rc.conf` `a path to an rc file to update`introduzir como, pode `ERROR: [Errno 13] Permission denied`encontrar o problema. Para resolver este problema, deve conceder o direito `etc/rc.conf`de escrita ao utilizador atual contra o ficheiro .

Agora pode iniciar sessão no Azure e criar o seu VM FreeBSD. Abaixo está um exemplo para criar um FreeBSD 11.0 VM. Você também pode adicionar `--public-ip-address-dns-name` o parâmetro com um nome DNS globalmente único para um IP público recém-criado. 

```azurecli
az login 
az group create --name myResourceGroup --location eastus
az vm create --name myFreeBSD11 \
    --resource-group myResourceGroup \
    --image MicrosoftOSTC:FreeBSD:11.0:latest \
    --admin-username azureuser \
    --generate-ssh-keys
```

Em seguida, pode iniciar sessão no seu VM FreeBSD através do endereço ip que imprimiu na saída de uma implementação acima. 

```bash
ssh azureuser@xx.xx.xx.xx -i /etc/ssh/ssh_host_rsa_key
```   

## <a name="vm-extensions-for-freebsd"></a>Extensões VM para FreeBSD
Seguem-se extensões VM suportadas em FreeBSD.

### <a name="vmaccess"></a>VMAccess
A extensão [VMAccess](https://github.com/Azure/azure-linux-extensions/tree/master/VMAccess) pode:

* Redefinir a palavra-passe do utilizador original do sudo.
* Crie um novo utilizador sudo com a senha especificada.
* Desloque a chave do anfitrião público com a chave dada.
* Redefinir a chave de acolhimento pública fornecida durante o fornecimento de VM se a chave hospedeira não for fornecida.
* Abra a porta SSH (22) e restaure a sshd_config se reset_ssh for em realidade.
* Remover o utilizador existente.
* Verifique os discos.
* Repare um disco adicionado.

### <a name="customscript"></a>CustomScript
A extensão [CustomScript](https://github.com/Azure/azure-linux-extensions/tree/master/CustomScript) pode:

* Se fornecido, descarregue os scripts personalizados do Armazenamento Azure ou do armazenamento público externo (por exemplo, GitHub).
* Execute o script do ponto de entrada.
* Suporte comandos inline.
* Converta automaticamente a nova linha de estilo Windows em shell e scripts Python.
* Remova o BOM na concha e os scripts Python automaticamente.
* Proteja os dados sensíveis no CommandToExecute.

> [!NOTE]
> O FreeBSD VM só suporta a versão CustomScript 1.x.  

## <a name="authentication-user-names-passwords-and-ssh-keys"></a>Autenticação: nomes de utilizador, palavras-passe e chaves SSH
Quando estiver a criar uma máquina virtual FreeBSD utilizando o portal Azure, deve fornecer um nome de utilizador, senha ou chave pública SSH.
Os nomes dos utilizadores para a implementação de uma máquina virtual FreeBSD no Azure não devem coincidir com os nomes das contas do sistema (UID <100) já presentes na máquina virtual ("raiz", por exemplo).
Atualmente, apenas a chave RSA SSH é suportada. Uma tecla SSH multi-line deve começar com `---- BEGIN SSH2 PUBLIC KEY ----` e terminar com `---- END SSH2 PUBLIC KEY ----`.

## <a name="obtaining-superuser-privileges"></a>Obtenção de privilégios de superutilizador
A conta de utilizador especificada durante a implantação de instâncias de máquinavirtual no Azure é uma conta privilegiada. O pacote de sudo foi instalado na imagem publicada FreeBSD.
Depois de iniciar sessão através desta conta de utilizador, pode executar comandos como raiz utilizando a sintaxe de comando.

```
$ sudo <COMMAND>
```

Pode obter opcionalmente uma casca `sudo -s`de raiz utilizando .

## <a name="known-issues"></a>Problemas conhecidos
A versão 2.2.2 do [Agente Convidado Azure VM](https://github.com/Azure/WALinuxAgent/) tem uma [emissão conhecida](https://github.com/Azure/WALinuxAgent/pull/517) que causa a falha de provisionamento do FreeBSD VM no Azure. A correção foi captada pela versão 2.2.3 do [Agente Convidado Azure VM](https://github.com/Azure/WALinuxAgent/) e posteriormente lançada. 

## <a name="next-steps"></a>Passos seguintes
* Vá ao [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.FreeBSD112) para criar um VM FreeBSD.
