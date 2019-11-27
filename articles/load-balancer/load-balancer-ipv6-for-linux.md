---
title: Configurar DHCPv6 para VMs do Linux
titleSuffix: Azure Load Balancer
description: Neste artigo, saiba como configurar o DHCPv6 para VMs Linux.
services: load-balancer
documentationcenter: na
author: asudbring
keywords: IPv6, o Balanceador de carga do azure, pilha dupla, ip público, ipv6 nativo, móvel, iot
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/22/2019
ms.author: allensu
ms.openlocfilehash: 6ea215b6aa826231e940f88c3687bb65591303f2
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74225327"
---
# <a name="configure-dhcpv6-for-linux-vms"></a>Configurar DHCPv6 para VMs do Linux


Algumas das imagens de máquinas virtuais Linux no Azure Marketplace tem Dynamic Host Configuration Protocol versão 6 (DHCPv6) configurados por predefinição. Para oferecer suporte a IPv6, o DHCPv6 tem de ser configurado da distribuição de SO Linux que está a utilizar. Várias distribuições de Linux configurar DHCPv6 de diversas formas, porque utilizam diferentes pacotes.

> [!NOTE]
> Imagens recentes de SUSE Linux e o CoreOS no Azure Marketplace têm sido pré-configuradas com o DHCPv6. Não existem alterações adicionais são necessárias quando utiliza essas imagens.

Este documento descreve como ativar o DHCPv6 para que sua máquina virtual Linux obtém um endereço IPv6.

> [!WARNING]
> Ao editar incorretamente os ficheiros de configuração de rede, pode perder o acesso à rede para a VM. Recomendamos que teste as alterações de configuração em sistemas de não produção. As instruções neste artigo foram testadas nas versões mais recentes das imagens do Linux no Azure Marketplace. Para obter instruções mais detalhadas, consulte a documentação para a sua própria versão do Linux.

## <a name="ubuntu"></a>Ubuntu

1. Edite o arquivo */etc/DHCP/dhclient6.conf* e adicione a seguinte linha:

        timeout 10;

2. Edite a configuração de rede para a interface de eth0 com a seguinte configuração:

   * No **Ubuntu 12, 4 e 14, 4**, edite o arquivo */etc/network/interfaces.d/eth0.cfg* . 
   * No **Ubuntu 16, 4**, edite o arquivo */etc/network/interfaces.d/50-Cloud-init.cfg* .

         iface eth0 inet6 auto
             up sleep 5
             up dhclient -1 -6 -cf /etc/dhcp/dhclient6.conf -lf /var/lib/dhcp/dhclient6.eth0.leases -v eth0 || true

3. Renove o endereço IPv6:

    ```bash
    sudo ifdown eth0 && sudo ifup eth0
    ```
A partir do Ubuntu 17,10, o mecanismo de configuração de rede padrão é [netplan]( https://netplan.io).  No momento da instalação/instanciação, o netplan lê a configuração de rede nos arquivos de configuração do YAML neste local:/{lib, etc, execute}/netplan/*. YAML.

Inclua uma instrução *dhcp6: true* para cada interface Ethernet em sua configuração.  Por exemplo:
  
        network:
          version: 2
          ethernets:
            eno1:
              dhcp6: true

Durante a inicialização inicial, o "processador de rede" do netplan grava a configuração em/Run para entregar o controle de dispositivos ao daemon de rede especificado para obter informações de referência sobre o netplan, consulte https://netplan.io/reference.
 
## <a name="debian"></a>Debian

1. Edite o arquivo */etc/DHCP/dhclient6.conf* e adicione a seguinte linha:

        timeout 10;

2. Edite o arquivo */etc/network/interfaces* e adicione a seguinte configuração:

        iface eth0 inet6 auto
            up sleep 5
            up dhclient -1 -6 -cf /etc/dhcp/dhclient6.conf -lf /var/lib/dhcp/dhclient6.eth0.leases -v eth0 || true

3. Renove o endereço IPv6:

    ```bash
    sudo ifdown eth0 && sudo ifup eth0
    ```

## <a name="rhel-centos-and-oracle-linux"></a>RHEL, CentOS e Oracle Linux

1. Edite o arquivo */etc/sysconfig/network* e adicione o seguinte parâmetro:

        NETWORKING_IPV6=yes

2. Edite o arquivo */etc/sysconfig/network-scripts/ifcfg-eth0* e adicione os dois parâmetros a seguir:

        IPV6INIT=yes
        DHCPV6C=yes

3. Renove o endereço IPv6:

    ```bash
    sudo ifdown eth0 && sudo ifup eth0
    ```

## <a name="sles-11-and-opensuse-13"></a>SLES 11 e openSUSE 13

Recentes SUSE Linux Enterprise Server (SLES) e o openSUSE imagens no Azure têm sido pré-configuradas com o DHCPv6. Não existem alterações adicionais são necessárias quando utiliza essas imagens. Se tiver uma VM que se baseia numa imagem SUSE mais antiga ou personalizada, faça o seguinte:

1. Instale o pacote `dhcp-client`, se necessário:

    ```bash
    sudo zypper install dhcp-client
    ```

2. Edite o arquivo */etc/sysconfig/network/ifcfg-eth0* e adicione o seguinte parâmetro:

        DHCLIENT6_MODE='managed'

3. Renove o endereço IPv6:

    ```bash
    sudo ifdown eth0 && sudo ifup eth0
    ```

## <a name="sles-12-and-opensuse-leap"></a>SLES 12 e openSUSE salto

Imagens SLES e openSUSE recentes no Azure tenham sido pré-configuradas com o DHCPv6. Não existem alterações adicionais são necessárias quando utiliza essas imagens. Se tiver uma VM que se baseia numa imagem SUSE mais antiga ou personalizada, faça o seguinte:

1. Edite o arquivo */etc/sysconfig/network/ifcfg-eth0* e substitua o parâmetro `#BOOTPROTO='dhcp4'` pelo seguinte valor:

        BOOTPROTO='dhcp'

2. Para o arquivo */etc/sysconfig/network/ifcfg-eth0* , adicione o seguinte parâmetro:

        DHCLIENT6_MODE='managed'

3. Renove o endereço IPv6:

    ```bash
    sudo ifdown eth0 && sudo ifup eth0
    ```

## <a name="coreos"></a>CoreOS

Imagens recentes de CoreOS no Azure tenham sido pré-configuradas com o DHCPv6. Não existem alterações adicionais são necessárias quando utiliza essas imagens. Se tiver uma VM com base numa imagem de CoreOS mais antiga ou personalizada, faça o seguinte:

1. Edite o arquivo */etc/systemd/network/10_dhcp. Network* :

        [Match]
        eth0

        [Network]
        DHCP=ipv6

2. Renove o endereço IPv6:

    ```bash
    sudo systemctl restart systemd-networkd
    ```
