---
title: Use xrdp com Linux
description: Aprenda a instalar e configurar o Ambiente de Trabalho Remoto (xrdp) para ligar a um Linux VM em Azure utilizando ferramentas gráficas
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.collection: linux
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 03/03/2021
ms.author: cynthn
ms.openlocfilehash: 84960e6247edc708bedb899c96ebf7522397269a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "104580373"
---
# <a name="install-and-configure-xrdp-to-use-remote-desktop-with-ubuntu"></a>Instale e configuure xrdp para usar desktop remoto com Ubuntu

As máquinas virtuais Linux (VMs) em Azure são geralmente geridas a partir da linha de comando utilizando uma ligação secure shell (SSH). Quando novo no Linux, ou para cenários rápidos de resolução de problemas, a utilização de ambientes de trabalho remotos pode ser mais fácil. Este artigo detalha como instalar e configurar um ambiente de ambiente de trabalho[(xfce)](https://www.xfce.org)e um ambiente de trabalho remoto[(xrdp)](http://xrdp.org)para o seu Linux VM que executa ubuntu.

O artigo foi escrito e testado usando um Ubuntu 18.04 VM. 

## <a name="prerequisites"></a>Pré-requisitos

Este artigo requer um Ubuntu 18.04 LTS VM existente em Azure. Se precisar de criar um VM, utilize um dos seguintes métodos:

- O [Azure CLI](quick-create-cli.md)
- O [portal Azure](quick-create-portal.md)


## <a name="install-a-desktop-environment-on-your-linux-vm"></a>Instale um ambiente de ambiente de trabalho no seu Linux VM

A maioria dos VMs Linux em Azure não têm um ambiente de ambiente de trabalho instalado por padrão. Os VMs Linux são geralmente geridos usando ligações SSH em vez de um ambiente de ambiente de trabalho. Existem vários ambientes de ambiente de trabalho em Linux que pode escolher. Dependendo da sua escolha do ambiente de ambiente de trabalho, pode consumir um a 2 GB de espaço em disco, e demorar 5 a 10 minutos para instalar e configurar todas as embalagens necessárias.

O exemplo a seguir instala o ambiente de trabalho [xfce4](https://www.xfce.org/) leve num Ubuntu 18.04 LTS VM. Os comandos para outras distribuições variam ligeiramente (use `yum` para instalar no Red Hat Enterprise Linux e configurar as `selinux` regras apropriadas, ou usar `zypper` para instalar no SUSE, por exemplo).

Primeiro, SSH para o seu VM. O exemplo a seguir liga-se ao VM nomeado *myvm.westus.cloudapp.azure.com* com o nome de utilizador do *azureuser*. Use os seus próprios valores:

```bash
ssh azureuser@myvm.westus.cloudapp.azure.com
```

Se estiver a utilizar o Windows e precisar de mais informações sobre a utilização do SSH, consulte [como utilizar as chaves SSH com o Windows](ssh-from-windows.md).

Em seguida, instale o xfce utilizando `apt` o seguinte:

```bash
sudo apt-get update
sudo apt-get -y install xfce4
```

## <a name="install-and-configure-a-remote-desktop-server"></a>Instale e configuure um servidor de ambiente de trabalho remoto
Agora que tem um ambiente de ambiente de trabalho instalado, configuure um serviço de ambiente de trabalho remoto para ouvir as ligações recebidas. [Xrdp](http://xrdp.org) é um servidor de Protocolo remoto de Desktop (RDP) de código aberto que está disponível na maioria das distribuições do Linux, e funciona bem com xfce. Instale xrdp no seu Ubuntu VM da seguinte forma:

```bash
sudo apt-get -y install xrdp
sudo systemctl enable xrdp
```

Diga ao Xrdp que ambiente de trabalho deve utilizar quando iniciar a sessão. Configure xrdp para usar o xfce como ambiente de ambiente de trabalho da seguinte forma:

```bash
echo xfce4-session >~/.xsession
```

Reinicie o serviço xrdp para que as alterações produzam o seguinte modo:

```bash
sudo service xrdp restart
```


## <a name="set-a-local-user-account-password"></a>Definir uma senha de conta de utilizador local
Se criou uma palavra-passe para a sua conta de utilizador quando criou o seu VM, ignore este passo. Se utilizar apenas a autenticação da chave SSH e não tiver uma definição de senha de conta local, especifique uma palavra-passe antes de utilizar o xrdp para iniciar sessão no seu VM. Xrdp não pode aceitar chaves SSH para autenticação. O exemplo a seguir especifica uma palavra-passe para o *azureuser* da conta de utilizador:

```bash
sudo passwd azureuser
```

> [!NOTE]
> Especificar uma palavra-passe não atualiza a sua configuração SSHD para permitir logins de palavra-passe se não o fizer atualmente. Do ponto de vista da segurança, pode desejar ligar-se ao seu VM com um túnel SSH utilizando a autenticação baseada em chaves e, em seguida, ligar-se ao xrdp. Em caso afirmativo, ignore o passo seguinte na criação de uma regra de grupo de segurança de rede para permitir o tráfego remoto no ambiente de trabalho.


## <a name="create-a-network-security-group-rule-for-remote-desktop-traffic"></a>Criar uma regra do Grupo de Segurança de Rede para tráfego remoto de ambiente de trabalho
Para permitir que o tráfego de ambiente de trabalho remoto chegue ao seu Linux VM, é necessário criar uma regra do grupo de segurança de rede que permita que o TCP na porta 3389 chegue ao seu VM. Para obter mais informações sobre as regras do grupo de segurança de rede, veja [o que é um grupo de segurança de rede?](../../virtual-network/network-security-groups-overview.md) Também pode [utilizar o portal Azure para criar uma regra do grupo de segurança de rede](../windows/nsg-quickstart-portal.md).

O exemplo a seguir cria uma regra de grupo de segurança de rede com [porta aberta az vm](/cli/azure/vm#az-vm-open-port) na porta *3389*. Do CLI Azure, não da sessão SSH para o seu VM, abra a seguinte regra do grupo de segurança da rede:

```azurecli
az vm open-port --resource-group myResourceGroup --name myVM --port 3389
```


## <a name="connect-your-linux-vm-with-a-remote-desktop-client"></a>Ligue o seu Linux VM a um cliente de ambiente de trabalho remoto

Abra o seu cliente local de desktop remoto e ligue-se ao endereço IP ou ao nome DNS do seu VM Linux. 

:::image type="content" source="media/use-remote-desktop/remote-desktop.png" alt-text="Screenshot do cliente de ambiente de trabalho remoto.":::

Introduza o nome de utilizador e a palavra-passe para a conta de utilizador no seu VM da seguinte forma:

:::image type="content" source="media/use-remote-desktop/xrdp-login.png" alt-text="Screenshot do registo xrdp no ecrã.":::

Após a autenticação, o ambiente de ambiente de trabalho xfce carregará e parecerá semelhante ao seguinte exemplo:

![xfce ambiente de ambiente de trabalho através de xrdp](./media/use-remote-desktop/xfce-desktop-environment.png)

Se o seu cliente RDP local utilizar a autenticação ao nível da rede (NLA), poderá ter de desativar essa definição de ligação. A XRDP não suporta atualmente a NLA. Pode também olhar para soluções alternativas de PDR que suportam o NLA, como o [FreeRDP.](https://www.freerdp.com)


## <a name="troubleshoot"></a>Resolução de problemas
Se não conseguir ligar-se ao seu Linux VM utilizando um cliente de Ambiente de Trabalho Remoto, utilize `netstat` no seu VM Linux para verificar se o seu VM está a ouvir as ligações RDP da seguinte forma:

```bash
sudo netstat -plnt | grep rdp
```

O exemplo a seguir mostra a escuta em VM na porta TCP 3389, como esperado:

```bash
tcp     0     0      127.0.0.1:3350     0.0.0.0:*     LISTEN     53192/xrdp-sesman
tcp     0     0      0.0.0.0:3389       0.0.0.0:*     LISTEN     53188/xrdp
```

Se o serviço *xrdp-sesman* não estiver a ouvir, num Ubuntu VM reinicie o serviço da seguinte forma:

```bash
sudo service xrdp restart
```

Reveja os registos em */var/log* no seu Ubuntu VM para obter indicações sobre o motivo pelo qual o serviço pode não estar a responder. Também pode monitorizar o sislog durante uma tentativa de ligação remota ao ambiente de trabalho para visualizar quaisquer erros:

```bash
tail -f /var/log/syslog
```

Outras distribuições de Linux, como Red Hat Enterprise Linux e SUSE, podem ter diferentes formas de reiniciar serviços e registar localizações alternativas para rever.

Se não receber qualquer resposta no seu cliente de ambiente de trabalho remoto e não vir quaisquer eventos no registo do sistema, este comportamento indica que o tráfego remoto de ambiente de trabalho não pode chegar ao VM. Reveja as regras do seu grupo de segurança de rede para garantir que tem uma regra para permitir a TCP na porta 3389. Para obter mais informações, consulte [problemas de conectividade da aplicação Troubleshoot](/troubleshoot/azure/virtual-machines/troubleshoot-app-connection).


## <a name="next-steps"></a>Passos seguintes
Para obter mais informações sobre a criação e utilização de chaves SSH com VMs Linux, consulte [as teclas Create SSH para Os VMs Linux em Azure](mac-create-ssh-keys.md).

Para obter informações sobre a utilização de SSH a partir do Windows, consulte [como utilizar as teclas SSH com o Windows](ssh-from-windows.md).