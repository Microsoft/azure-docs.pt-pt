---
title: Usar Área de Trabalho Remota para uma VM do Linux no Azure
description: Saiba como instalar e configurar o Área de Trabalho Remota (xrdp) para se conectar a uma VM do Linux no Azure usando ferramentas gráficas
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
ms.openlocfilehash: 8631b05bc42df86ef6865bf2a07c0e3deaaad2fe
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74034285"
---
# <a name="install-and-configure-remote-desktop-to-connect-to-a-linux-vm-in-azure"></a>Instalar e configurar Área de Trabalho Remota para se conectar a uma VM do Linux no Azure
As VMs (máquinas virtuais) do Linux no Azure geralmente são gerenciadas na linha de comando usando uma conexão SSH (Secure Shell). Quando for novo no Linux ou para cenários de solução de problemas rápidos, o uso da área de trabalho remota pode ser mais fácil. Este artigo fornece detalhes sobre como instalar e configurar um ambiente de área de trabalho ([Xfce](https://www.xfce.org)) e uma área de trabalho remota ([xrdp](https://www.xrdp.org)) para sua VM Linux usando o modelo de implantação do Gerenciador de recursos.


## <a name="prerequisites"></a>Pré-requisitos
Este artigo requer uma VM do Ubuntu 18, 4 LTS existente no Azure. Se você precisar criar uma VM, use um dos seguintes métodos:

- O [CLI do Azure](quick-create-cli.md)
- O [portal do Azure](quick-create-portal.md)


## <a name="install-a-desktop-environment-on-your-linux-vm"></a>Instalar um ambiente de área de trabalho em sua VM Linux
A maioria das VMs do Linux no Azure não tem um ambiente de área de trabalho instalado por padrão. As VMs do Linux são normalmente gerenciadas usando conexões SSH em vez de um ambiente de área de trabalho. Há vários ambientes de área de trabalho no Linux que você pode escolher. Dependendo de sua escolha de ambiente de área de trabalho, pode consumir de um a 2 GB de espaço em disco e levar de 5 a 10 minutos para instalar e configurar todos os pacotes necessários.

O exemplo a seguir instala o ambiente de área de trabalho leve do [xfce4](https://www.xfce.org/) em uma VM do Ubuntu 18, 4 LTS. Os comandos para outras distribuições variam ligeiramente (use `yum` para instalar em Red Hat Enterprise Linux e configurar as regras de `selinux` apropriadas, ou use `zypper` para instalar no SUSE, por exemplo).

Primeiro, SSH para sua VM. O exemplo a seguir conecta-se à VM chamada *MyVM.westus.cloudapp.Azure.com* com o nome de usuário de *azureuser*. Use seus próprios valores:

```bash
ssh azureuser@myvm.westus.cloudapp.azure.com
```

Se você estiver usando o Windows e precisar de mais informações sobre o uso do SSH, consulte [como usar chaves SSH com o Windows](ssh-from-windows.md).

Em seguida, instale o Xfce usando `apt` da seguinte maneira:

```bash
sudo apt-get update
sudo apt-get install xfce4
```

## <a name="install-and-configure-a-remote-desktop-server"></a>Instalar e configurar um servidor de área de trabalho remota
Agora que você tem um ambiente de área de trabalho instalado, configure um serviço de área de trabalho remota para escutar conexões de entrada. [xrdp](http://xrdp.org) é um servidor de protocolo RDP de software livre (RDP) que está disponível na maioria das distribuições do Linux e funciona bem com o Xfce. Instale o xrdp em sua VM do Ubuntu da seguinte maneira:

```bash
sudo apt-get -y install xrdp
sudo systemctl enable xrdp
```

Diga ao xrdp qual ambiente de área de trabalho usar ao iniciar a sessão. Configure o xrdp para usar o Xfce como seu ambiente de área de trabalho da seguinte maneira:

```bash
echo xfce4-session >~/.xsession
```

Reinicie o serviço xrdp para que as alterações entrem em vigor da seguinte maneira:

```bash
sudo service xrdp restart
```


## <a name="set-a-local-user-account-password"></a>Definir uma senha de conta de usuário local
Se você criou uma senha para sua conta de usuário quando criou sua VM, ignore esta etapa. Se você usar somente a autenticação de chave SSH e não tiver uma senha de conta local definida, especifique uma senha antes de usar o xrdp para fazer logon em sua VM. xrdp não pode aceitar chaves SSH para autenticação. O exemplo a seguir especifica uma senha para a conta de usuário *azureuser*:

```bash
sudo passwd azureuser
```

> [!NOTE]
> A especificação de uma senha não atualiza a configuração do SSHD para permitir logons de senha, se não houver. De uma perspectiva de segurança, talvez você queira se conectar à sua VM com um túnel SSH usando a autenticação baseada em chave e, em seguida, conectar-se ao xrdp. Nesse caso, ignore a etapa a seguir sobre como criar uma regra de grupo de segurança de rede para permitir o tráfego de área de trabalho remota.


## <a name="create-a-network-security-group-rule-for-remote-desktop-traffic"></a>Criar uma regra de grupo de segurança de rede para tráfego de Área de Trabalho Remota
Para permitir que Área de Trabalho Remota tráfego alcance sua VM do Linux, é necessário criar uma regra de grupo de segurança de rede que permita que o TCP na porta 3389 alcance sua VM. Para obter mais informações sobre regras de grupo de segurança de rede, consulte [o que é um grupo de segurança de rede?](../../virtual-network/security-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) Você também pode [usar o portal do Azure para criar uma regra de grupo de segurança de rede](../windows/nsg-quickstart-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

O exemplo a seguir cria uma regra de grupo de segurança de rede com [AZ VM Open-Port](/cli/azure/vm#az-vm-open-port) na porta *3389*. No CLI do Azure, não na sessão SSH para sua VM, abra a seguinte regra de grupo de segurança de rede:

```azurecli
az vm open-port --resource-group myResourceGroup --name myVM --port 3389
```


## <a name="connect-your-linux-vm-with-a-remote-desktop-client"></a>Conectar sua VM Linux a um cliente Área de Trabalho Remota
Abra o cliente de área de trabalho remota local e conecte-se ao endereço IP ou nome DNS da VM Linux. Insira o nome de usuário e a senha para a conta de usuários na VM da seguinte maneira:

![Conectar-se ao xrdp usando seu cliente Área de Trabalho Remota](./media/use-remote-desktop/remote-desktop-client.png)

Após a autenticação, o ambiente de área de trabalho Xfce será carregado e terá uma aparência semelhante ao exemplo a seguir:

![ambiente de área de trabalho Xfce por meio de xrdp](./media/use-remote-desktop/xfce-desktop-environment.png)

Se o cliente RDP local usar NLA (autenticação de nível de rede), talvez seja necessário desabilitar essa configuração de conexão. Atualmente, o XRDP não dá suporte a NLA. Você também pode examinar soluções de RDP alternativas que dão suporte a NLA, como [FreeRDP](https://www.freerdp.com).


## <a name="troubleshoot"></a>Resolução de problemas
Se você não puder se conectar à sua VM do Linux usando um cliente Área de Trabalho Remota, use `netstat` em sua VM Linux para verificar se sua VM está escutando conexões RDP da seguinte maneira:

```bash
sudo netstat -plnt | grep rdp
```

O exemplo a seguir mostra a VM escutando na porta TCP 3389 conforme esperado:

```bash
tcp     0     0      127.0.0.1:3350     0.0.0.0:*     LISTEN     53192/xrdp-sesman
tcp     0     0      0.0.0.0:3389       0.0.0.0:*     LISTEN     53188/xrdp
```

Se o serviço *xrdp-sesman* não estiver escutando, em uma VM do Ubuntu, reinicie o serviço da seguinte maneira:

```bash
sudo service xrdp restart
```

Examine os logs em */var/log* em sua VM do Ubuntu para saber por que o serviço pode não estar respondendo. Você também pode monitorar o syslog durante uma tentativa de conexão de área de trabalho remota para exibir os erros:

```bash
tail -f /var/log/syslog
```

Outras distribuições do Linux, como Red Hat Enterprise Linux e SUSE, podem ter diferentes maneiras de reiniciar serviços e locais de arquivos de log alternativos para revisão.

Se você não receber nenhuma resposta em seu cliente de área de trabalho remota e não vir nenhum evento no log do sistema, esse comportamento indicará que o tráfego da área de trabalho remota não poderá acessar a VM. Examine as regras do grupo de segurança de rede para garantir que você tenha uma regra para permitir o TCP na porta 3389. Para obter mais informações, consulte [solucionar problemas de conectividade do aplicativo](../windows/troubleshoot-app-connection.md).


## <a name="next-steps"></a>Passos seguintes
Para obter mais informações sobre como criar e usar chaves SSH com VMs do Linux, consulte [criar chaves SSH para VMs do Linux no Azure](mac-create-ssh-keys.md).

Para obter informações sobre como usar o SSH do Windows, consulte [como usar chaves SSH com o Windows](ssh-from-windows.md).

