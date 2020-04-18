---
title: Use o Ambiente de Trabalho Remoto para um Linux VM em Azure
description: Aprenda a instalar e configurar o Remote Desktop (xrdp) para ligar a um Linux VM em Azure utilizando ferramentas gráficas
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 09/12/2019
ms.author: cynthn
ms.openlocfilehash: 2e97442d4104f52c1a76ba8cd1d81c99508bb242
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/17/2020
ms.locfileid: "81605192"
---
# <a name="install-and-configure-remote-desktop-to-connect-to-a-linux-vm-in-azure"></a>Instale e configure o Ambiente de Trabalho Remoto para ligar a um Linux VM em Azure
As máquinas virtuais Linux (VMs) em Azure são geralmente geridas a partir da linha de comando utilizando uma ligação segura (SSH). Quando novo no Linux, ou para cenários de resolução rápida de problemas, a utilização de ambientes de trabalho remotos pode ser mais fácil. Este artigo detalha como instalar e configurar um ambiente de trabalho[(xfce)](https://www.xfce.org)e um ambiente de trabalho remoto[(xrdp)](http://xrdp.org)para o seu VM Linux utilizando o modelo de implementação do Gestor de Recursos.


## <a name="prerequisites"></a>Pré-requisitos
Este artigo requer um Ubuntu 18.04 LTS VM existente em Azure. Se precisar de criar um VM, utilize um dos seguintes métodos:

- O [Azure CLI](quick-create-cli.md)
- O [portal Azure](quick-create-portal.md)


## <a name="install-a-desktop-environment-on-your-linux-vm"></a>Instale um ambiente de trabalho no seu Linux VM
A maioria dos VMs Linux em Azure não tem um ambiente de trabalho instalado por padrão. Os VMs linux são geralmente geridos usando ligações SSH em vez de um ambiente de trabalho. Existem vários ambientes de desktop em Linux que você pode escolher. Dependendo da sua escolha de ambiente de trabalho, pode consumir um a 2 GB de espaço em disco, e demorar 5 a 10 minutos para instalar e configurar todos os pacotes necessários.

O exemplo seguinte instala o ambiente de trabalho leve [xfce4](https://www.xfce.org/) num Ubuntu 18.04 LTS VM. Os comandos para outras distribuições variam ligeiramente (a `yum` utilização `selinux` para instalar `zypper` no Red Hat Enterprise Linux e configurar as regras apropriadas, ou usar para instalar no SUSE, por exemplo).

Primeiro, SSH para o seu VM. O exemplo seguinte liga-se ao VM denominado *myvm.westus.cloudapp.azure.com* com o nome de utilizador do *azureuser*. Use os seus próprios valores:

```bash
ssh azureuser@myvm.westus.cloudapp.azure.com
```

Se estiver a utilizar o Windows e precisar de mais informações sobre a utilização do SSH, consulte [como utilizar as teclas SSH com o Windows](ssh-from-windows.md).

Em seguida, instale `apt` xfce utilizando o seguinte:

```bash
sudo apt-get update
sudo apt-get -y install xfce4
```

## <a name="install-and-configure-a-remote-desktop-server"></a>Instale e configure um servidor de ambiente de trabalho remoto
Agora que tem um ambiente de trabalho instalado, configure um serviço de ambiente de trabalho remoto para ouvir as ligações de entrada. [Xrdp](http://xrdp.org) é um servidor de protocolo de ambiente de trabalho remoto de código remoto (RDP) de código aberto que está disponível na maioria das distribuições do Linux, e funciona bem com xfce. Instale xrdp no seu Ubuntu VM da seguinte forma:

```bash
sudo apt-get -y install xrdp
sudo systemctl enable xrdp
```

Diga ao XRDP que ambiente de ambiente de trabalho usar quando iniciar a sua sessão. Configure o xrdp para usar o xfce como o seu ambiente de trabalho:

```bash
echo xfce4-session >~/.xsession
```

Reiniciar o serviço xrdp para que as alterações entrem em vigor da seguinte forma:

```bash
sudo service xrdp restart
```


## <a name="set-a-local-user-account-password"></a>Definir uma senha de conta de utilizador local
Se criou uma palavra-passe para a sua conta de utilizador quando criou o seu VM, ignore este passo. Se utilizar apenas a autenticação da chave SSH e não tiver uma palavra-passe de conta local, especifique uma palavra-passe antes de utilizar o XRDP para iniciar sessão no seu VM. Xrdp não pode aceitar chaves SSH para autenticação. O exemplo que se segue especifica uma palavra-passe para o *azureuser*da conta de utilizador:

```bash
sudo passwd azureuser
```

> [!NOTE]
> Especificar uma palavra-passe não atualiza a configuração SSHD para permitir logins de senha se não o fizer atualmente. Do ponto de vista da segurança, poderá desejar ligar-se ao seu VM com um túnel SSH utilizando a autenticação baseada na chave e, em seguida, ligar-se ao xrdp. Em caso afirmativo, ignore o passo seguinte na criação de uma regra do grupo de segurança da rede para permitir o tráfego remoto de ambientes de trabalho.


## <a name="create-a-network-security-group-rule-for-remote-desktop-traffic"></a>Criar uma regra do Grupo de Segurança da Rede para tráfego remoto de ambiente de trabalho
Para permitir que o tráfego do Remote Desktop chegue ao seu VM Linux, é necessário criar uma regra do grupo de segurança de rede que permita ao TCP na porta 3389 chegar ao seu VM. Para obter mais informações sobre as regras do grupo de segurança da rede, veja o que é um grupo de segurança da [rede?](../../virtual-network/security-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) Também pode [utilizar o portal Azure para criar uma regra](../windows/nsg-quickstart-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)de grupo de segurança de rede.

O exemplo seguinte cria uma regra do grupo de segurança da rede com [az vm open-port](/cli/azure/vm#az-vm-open-port) na porta *3389*. Do Azure CLI, não da sessão SSH ao seu VM, abra a seguinte regra do grupo de segurança da rede:

```azurecli
az vm open-port --resource-group myResourceGroup --name myVM --port 3389
```


## <a name="connect-your-linux-vm-with-a-remote-desktop-client"></a>Conecte o seu VM Linux com um cliente de ambiente de trabalho remoto
Abra o seu cliente local de ambiente de trabalho remoto e ligue-se ao endereço IP ou nome DNS do seu VM Linux. Introduza o nome de utilizador e a palavra-passe para a conta de utilizador no seu VM da seguinte forma:

![Ligue-se ao xrdp usando o seu cliente Remote Desktop](./media/use-remote-desktop/remote-desktop-client.png)

Após a autenticação, o ambiente de trabalho xfce carregará e será semelhante ao seguinte exemplo:

![xfce ambiente de trabalho através de xrdp](./media/use-remote-desktop/xfce-desktop-environment.png)

Se o seu cliente RDP local utilizar a autenticação de nível de rede (NLA), poderá ter de desativar essa definição de ligação. A XRDP não suporta atualmente a NLA. Pode também olhar para soluções alternativas de PDR que suportam o NLA, como o [FreeRDP](https://www.freerdp.com).


## <a name="troubleshoot"></a>Resolução de problemas
Se não conseguir ligar-se ao seu VM `netstat` Linux utilizando um cliente de Ambiente de Trabalho Remoto, utilize no seu VM Linux para verificar se o seu VM está a ouvir ligações RDP da seguinte forma:

```bash
sudo netstat -plnt | grep rdp
```

O exemplo que se segue mostra o VM a ouvir na porta TCP 3389, como esperado:

```bash
tcp     0     0      127.0.0.1:3350     0.0.0.0:*     LISTEN     53192/xrdp-sesman
tcp     0     0      0.0.0.0:3389       0.0.0.0:*     LISTEN     53188/xrdp
```

Se o serviço *xrdp-sesman* não estiver a ouvir, num Ubuntu VM reinicie o serviço da seguinte forma:

```bash
sudo service xrdp restart
```

Reveja os registos em */var/log* no seu Ubuntu VM para obter indicações sobre o motivo pelo qual o serviço pode não estar a responder. Também pode monitorizar o slog durante uma tentativa remota de ligação ao ambiente de trabalho para ver quaisquer erros:

```bash
tail -f /var/log/syslog
```

Outras distribuições linux, como Red Hat Enterprise Linux e SUSE, podem ter diferentes formas de reiniciar serviços e localizações alternativas de ficheiros de registo para rever.

Se não receber qualquer resposta no seu cliente de ambiente de trabalho remoto e não vir quaisquer eventos no registo do sistema, este comportamento indica que o tráfego remoto de ambiente de trabalho não pode chegar ao VM. Reveja as regras do seu grupo de segurança de rede para garantir que tem uma regra para permitir a TCP no porto 3389. Para mais informações, consulte problemas de [conectividade de aplicação Troubleshoot.](../windows/troubleshoot-app-connection.md)


## <a name="next-steps"></a>Passos seguintes
Para obter mais informações sobre a criação e utilização de chaves SSH com VMs Linux, consulte [Create SSH para VMs Linux em Azure](mac-create-ssh-keys.md).

Para obter informações sobre a utilização de SSH a partir do Windows, consulte [como utilizar as teclas SSH com o Windows](ssh-from-windows.md).

