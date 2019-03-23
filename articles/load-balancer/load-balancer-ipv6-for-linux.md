---
title: Configurar DHCPv6 para VMs do Linux
titlesuffix: Azure Load Balancer
description: Como configurar DHCPv6 para VMs do Linux.
services: load-balancer
documentationcenter: na
author: KumudD
keywords: IPv6, o Balanceador de carga do azure, pilha dupla, ip público, ipv6 nativo, móvel, iot
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/22/2019
ms.author: kumud
ms.openlocfilehash: 66777ec314e95d81a4be57082f06ef16dc170186
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/22/2019
ms.locfileid: "58369637"
---
# <a name="configure-dhcpv6-for-linux-vms"></a>Configurar DHCPv6 para VMs do Linux


Algumas das imagens de máquinas virtuais Linux no Azure Marketplace tem Dynamic Host Configuration Protocol versão 6 (DHCPv6) configurados por predefinição. Para oferecer suporte a IPv6, o DHCPv6 tem de ser configurado da distribuição de SO Linux que está a utilizar. Várias distribuições de Linux configurar DHCPv6 de diversas formas, porque utilizam diferentes pacotes.

> [!NOTE]
> Imagens recentes de SUSE Linux e o CoreOS no Azure Marketplace têm sido pré-configuradas com o DHCPv6. Não existem alterações adicionais são necessárias quando utiliza essas imagens.

Este documento descreve como ativar o DHCPv6 para que sua máquina virtual Linux obtém um endereço IPv6.

> [!WARNING]
> Ao editar incorretamente os ficheiros de configuração de rede, pode perder o acesso à rede para a VM. Recomendamos que teste as alterações de configuração em sistemas de não produção. As instruções neste artigo foram testadas nas versões mais recentes das imagens do Linux no Azure Marketplace. Para obter instruções mais detalhadas, consulte a documentação para a sua própria versão do Linux.

## <a name="ubuntu"></a>Ubuntu

1. Editar a */etc/dhcp/dhclient6.conf* de ficheiros e adicione a seguinte linha:

        timeout 10;

2. Edite a configuração de rede para a interface de eth0 com a seguinte configuração:

   * No **Ubuntu 12.04 e 14.04**, edite a */etc/network/interfaces.d/eth0.cfg* ficheiro. 
   * No **Ubuntu 16.04**, edite a */etc/network/interfaces.d/50-cloud-init.cfg* ficheiro.

         iface eth0 inet6 auto
             up sleep 5
             up dhclient -1 -6 -cf /etc/dhcp/dhclient6.conf -lf /var/lib/dhcp/dhclient6.eth0.leases -v eth0 || true

3. Renove o endereço IPv6:

    ```bash
    sudo ifdown eth0 && sudo ifup eth0
    ```
A partir do Ubuntu 17.10, o mecanismo de configuração de rede predefinido é [NETPLAN]( https://netplan.io).  Durante a instalação/instanciação, o NETPLAN lê a configuração de rede de ficheiros de configuração de YAML nesta localização: / {lib,etc,run}/netplan/*.yaml.

Inclua uma *dhcp6:true* instrução para cada interface de ethernet na sua configuração.  Por exemplo:
  
        network:
          version: 2
          ethernets:
            eno1:
              dhcp6: true

Durante a inicialização antecipada, o netplan "compositor de rede" escritas configuração para/executar a transferência fora de controle de dispositivos para o daemon de sistema de rede especificado para as informações de referência sobre NETPLAN, consulte https://netplan.io/reference.
 
## <a name="debian"></a>Debian

1. Editar a */etc/dhcp/dhclient6.conf* de ficheiros e adicione a seguinte linha:

        timeout 10;

2. Editar a */etc/network/interfaces* de ficheiros e adicione a seguinte configuração:

        iface eth0 inet6 auto
            up sleep 5
            up dhclient -1 -6 -cf /etc/dhcp/dhclient6.conf -lf /var/lib/dhcp/dhclient6.eth0.leases -v eth0 || true

3. Renove o endereço IPv6:

    ```bash
    sudo ifdown eth0 && sudo ifup eth0
    ```

## <a name="rhel-centos-and-oracle-linux"></a>RHEL, CentOS e Oracle Linux

1. Editar a */etc/sysconfig/network* de ficheiros e adicione o seguinte parâmetro:

        NETWORKING_IPV6=yes

2. Editar a */etc/sysconfig/network-scripts/ifcfg-eth0* de ficheiros e adicionar os dois parâmetros seguintes:

        IPV6INIT=yes
        DHCPV6C=yes

3. Renove o endereço IPv6:

    ```bash
    sudo ifdown eth0 && sudo ifup eth0
    ```

## <a name="sles-11-and-opensuse-13"></a>SLES 11 e openSUSE 13

Recentes SUSE Linux Enterprise Server (SLES) e o openSUSE imagens no Azure têm sido pré-configuradas com o DHCPv6. Não existem alterações adicionais são necessárias quando utiliza essas imagens. Se tiver uma VM que se baseia numa imagem SUSE mais antiga ou personalizada, faça o seguinte:

1. Instalar o `dhcp-client` empacotar, se necessário:

    ```bash
    sudo zypper install dhcp-client
    ```

2. Editar a */etc/sysconfig/network/ifcfg-eth0* de ficheiros e adicione o seguinte parâmetro:

        DHCLIENT6_MODE='managed'

3. Renove o endereço IPv6:

    ```bash
    sudo ifdown eth0 && sudo ifup eth0
    ```

## <a name="sles-12-and-opensuse-leap"></a>SLES 12 e openSUSE salto

Imagens SLES e openSUSE recentes no Azure tenham sido pré-configuradas com o DHCPv6. Não existem alterações adicionais são necessárias quando utiliza essas imagens. Se tiver uma VM que se baseia numa imagem SUSE mais antiga ou personalizada, faça o seguinte:

1. Editar a */etc/sysconfig/network/ifcfg-eth0* do ficheiro e substitua o `#BOOTPROTO='dhcp4'` parâmetro com o seguinte valor:

        BOOTPROTO='dhcp'

2. Para o */etc/sysconfig/network/ifcfg-eth0* de ficheiros, adicione o seguinte parâmetro:

        DHCLIENT6_MODE='managed'

3. Renove o endereço IPv6:

    ```bash
    sudo ifdown eth0 && sudo ifup eth0
    ```

## <a name="coreos"></a>CoreOS

Imagens recentes de CoreOS no Azure tenham sido pré-configuradas com o DHCPv6. Não existem alterações adicionais são necessárias quando utiliza essas imagens. Se tiver uma VM com base numa imagem de CoreOS mais antiga ou personalizada, faça o seguinte:

1. Editar a */etc/systemd/network/10_dhcp.network* ficheiro:

        [Match]
        eth0

        [Network]
        DHCP=ipv6

2. Renove o endereço IPv6:

    ```bash
    sudo systemctl restart systemd-networkd
    ```
