---
title: Utilizar o ambiente de trabalho remoto para uma VM do Linux no Azure | Documentos da Microsoft
description: Saiba como instalar e configurar o ambiente de trabalho remoto (xrdp) para ligar a uma VM do Linux no Azure com as ferramentas gráficas
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 05/30/2018
ms.author: cynthn
ms.openlocfilehash: dec0b8bcd5a0e0d20ead5b149405e3716589ba53
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57995722"
---
# <a name="install-and-configure-remote-desktop-to-connect-to-a-linux-vm-in-azure"></a>Instalar e configurar o ambiente de trabalho remoto para ligar a uma VM do Linux no Azure
Máquinas de virtuais do Linux (VMs) no Azure, normalmente, são geridas a partir da linha de comandos a utilizar uma ligação de secure shell (SSH). Quando novos para Linux, ou para cenários de resolução de problemas rápidos, o uso de ambiente de trabalho remoto pode ser mais fácil. Este artigo fornece detalhes sobre como instalar e configurar um ambiente de desktop ([xfce](https://www.xfce.org)) e o ambiente de trabalho remoto ([xrdp](http://www.xrdp.org)) para a sua VM do Linux com o modelo de implementação do Resource Manager.


## <a name="prerequisites"></a>Pré-requisitos
Este artigo requer uma VM de LTS do Ubuntu 16.04 existente no Azure. Se precisar de criar uma VM, utilize um dos seguintes métodos:

- O [da CLI do Azure](quick-create-cli.md)
- O [portal do Azure](quick-create-portal.md)


## <a name="install-a-desktop-environment-on-your-linux-vm"></a>Instalar um ambiente de desktop na sua VM do Linux
A maioria das VMs do Linux no Azure não tem um ambiente de desktop instalado por predefinição. VMs do Linux normalmente são geridas através de ligações SSH em vez de um ambiente de desktop. Existem vários ambientes de desktop em Linux que pode escolher. Dependendo de sua escolha de ambiente de desktop, ele pode consumir um a 2 GB de espaço em disco e demorar 5 a 10 minutos para instalar e configurar todos os pacotes necessários.

O exemplo seguinte instala o lightweight [xfce4](https://www.xfce.org/) ambiente de área de trabalho numa VM do Ubuntu 16.04 LTS. Comandos para outras distribuições são ligeiramente diferentes (utilize `yum` para instalar no Red Hat Enterprise Linux e configurar apropriado `selinux` regras ou utilização `zypper` para instalar no SUSE, por exemplo).

Em primeiro lugar, SSH à VM. O exemplo seguinte liga à VM com o nome *myvm.westus.cloudapp.azure.com* com o nome de utilizador *azureuser*. Utilize os seus próprios valores:

```bash
ssh azureuser@myvm.westus.cloudapp.azure.com
```

Se estiver a utilizar o Windows e precisar de mais informações sobre como utilizar o SSH, veja [chaves de como utilizar SSH com Windows](ssh-from-windows.md).

Em seguida, instale xfce usando `apt` da seguinte forma:

```bash
sudo apt-get update
sudo apt-get install xfce4
```

## <a name="install-and-configure-a-remote-desktop-server"></a>Instalar e configurar um servidor de ambiente de trabalho remoto
Agora que tem um ambiente de desktop instalado, configure um serviço de ambiente de trabalho remoto para escutar conexões de entrada. [xrdp](http://xrdp.org) é um servidor de protocolo RDP (Remote Desktop) de código-fonte aberto que está disponível na maioria das distribuições de Linux e funciona bem com xfce. Instale xrdp na sua VM do Ubuntu da seguinte forma:

```bash
sudo apt-get install xrdp
sudo systemctl enable xrdp
```

Diga xrdp o ambiente de área de trabalho para utilizar quando iniciar a sessão. Configure xrdp utilizar xfce como seu ambiente de área de trabalho da seguinte forma:

```bash
echo xfce4-session >~/.xsession
```

Reinicie o serviço de xrdp para que as alterações entrem em vigor da seguinte forma:

```bash
sudo service xrdp restart
```


## <a name="set-a-local-user-account-password"></a>Definir uma palavra-passe da conta de utilizador local
Se tiver criado uma palavra-passe para a sua conta de utilizador quando criou a VM, ignore este passo. Se utilizar a autenticação de chave SSH apenas e não tem uma palavra-passe de conta local definida, especifique uma palavra-passe antes de utilizar xrdp para iniciar sessão na sua VM. xrdp não pode aceitar as chaves SSH para autenticação. O exemplo seguinte especifica uma palavra-passe da conta de utilizador *azureuser*:

```bash
sudo passwd azureuser
```

> [!NOTE]
> Especificar uma palavra-passe não atualiza a configuração de SSHD para permitir inícios de sessão de palavra-passe se atualmente não existir. De uma perspectiva de segurança, que poderá pretender ligar à sua VM com um túnel SSH utilizando a autenticação baseada em chave e, em seguida, ligar a xrdp. Se assim for, ignore o passo seguinte sobre a criação de uma regra de grupo de segurança de rede para permitir o tráfego de ambiente de trabalho remoto.


## <a name="create-a-network-security-group-rule-for-remote-desktop-traffic"></a>Criar uma regra de grupo de segurança de rede para tráfego de ambiente de trabalho remoto
Para permitir o tráfego de ambiente de trabalho remoto aceda à VM do Linux, uma segurança de rede tem de regra de grupo ser criado que permite que o TCP na porta 3389, aceda à VM. Para obter mais informações sobre regras de grupo de segurança de rede, consulte [o que é um grupo de segurança de rede?](../../virtual-network/security-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) Também pode [utilizar o portal do Azure para criar uma regra de grupo de segurança de rede](../windows/nsg-quickstart-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

O exemplo seguinte cria uma regra de grupo de segurança de rede com [az vm open-port](/cli/azure/vm#az-vm-open-port) na porta *3389*. A partir do CLI do Azure, não a sessão SSH à VM, abra a seguinte regra de grupo de segurança de rede:

```azurecli
az vm open-port --resource-group myResourceGroup --name myVM --port 3389
```


## <a name="connect-your-linux-vm-with-a-remote-desktop-client"></a>Ligue a sua VM do Linux com um cliente de ambiente de trabalho remoto
Abra o seu cliente de ambiente de trabalho remota local e ligue-se para o endereço IP ou nome DNS da sua VM do Linux. Introduza o nome de utilizador e palavra-passe da conta de utilizador na sua VM da seguinte forma:

![Ligar ao xrdp através de seu cliente de ambiente de trabalho remoto](./media/use-remote-desktop/remote-desktop-client.png)

Depois de autenticar, o ambiente de área de trabalho xfce carregará e ter um aspeto semelhante ao seguinte exemplo:

![ambiente de área de trabalho de xfce por meio de xrdp](./media/use-remote-desktop/xfce-desktop-environment.png)

Se o seu cliente local do RDP utiliza a autenticação de nível de rede (NLA), terá de desativar essa definição de ligação. XRDP não suporta atualmente NLA. Pode também ver as soluções alternativas de RDP que suportam a NLA, tal como [FreeRDP](https://www.freerdp.com).


## <a name="troubleshoot"></a>Resolução de problemas
Se não conseguir ligar-se a sua VM do Linux utilizar um cliente de ambiente de trabalho remoto, utilize `netstat` na sua VM do Linux para verificar que a VM está escutando para ligações de RDP, da seguinte forma:

```bash
sudo netstat -plnt | grep rdp
```

O exemplo seguinte mostra a VM à escuta na porta TCP 3389 conforme esperado:

```bash
tcp     0     0      127.0.0.1:3350     0.0.0.0:*     LISTEN     53192/xrdp-sesman
tcp     0     0      0.0.0.0:3389       0.0.0.0:*     LISTEN     53188/xrdp
```

Se o *xrdp sesman* serviço não está a escutar, numa VM do Ubuntu para reiniciar o serviço da seguinte forma:

```bash
sudo service xrdp restart
```

Revisão de registos no */var/do registo* na sua VM do Ubuntu indicações de por que o serviço pode não estar a responder. Também pode monitorizar o syslog durante uma tentativa de conexão de área de trabalho remoto para ver todos os erros:

```bash
tail -f /var/log/syslog
```

Outras distribuições do Linux, como Red Hat Enterprise Linux e SUSE podem ter maneiras diferentes para reiniciar os serviços e localizações de ficheiros de registo alternativo para rever.

Se não recebeu qualquer resposta no seu cliente de ambiente de trabalho remoto e não vir quaisquer eventos no registo do sistema, este comportamento indica que o tráfego de ambiente de trabalho remoto não é possível contactar a VM. Reveja as suas regras de grupo de segurança de rede para se certificar de que tem uma regra para permitir o TCP na porta 3389. Para obter mais informações, consulte [resolver problemas de conectividade de aplicativo](../windows/troubleshoot-app-connection.md).


## <a name="next-steps"></a>Passos Seguintes
Para obter mais informações sobre como criar e utilizar chaves SSH com VMs do Linux, consulte [criar SSH chaves para VMs do Linux no Azure](mac-create-ssh-keys.md).

Para obter informações sobre como utilizar o SSH do Windows, consulte [chaves de como utilizar SSH com Windows](ssh-from-windows.md).

