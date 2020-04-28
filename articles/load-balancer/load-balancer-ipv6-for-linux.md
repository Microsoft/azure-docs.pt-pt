---
title: Configure DHCPv6 para VMs Linux
titleSuffix: Azure Load Balancer
description: Neste artigo, aprenda a configurar o DHCPv6 para VMs Linux.
services: load-balancer
documentationcenter: na
author: asudbring
keywords: ipv6, equilibrador de carga azul, dupla pilha, ip público, ipv6 nativo, móvel, iot
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/22/2019
ms.author: allensu
ms.openlocfilehash: 6ea215b6aa826231e940f88c3687bb65591303f2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "74225327"
---
# <a name="configure-dhcpv6-for-linux-vms"></a>Configure DHCPv6 para VMs Linux


Algumas das imagens da máquina virtual Linux no Mercado Azure não têm a versão 6 (DHCPv6) do Protocolo de Configuração dinâmica do anfitrião 6 (DHCPv6) configurada por padrão. Para suportar o IPv6, o DHCPv6 deve ser configurado na distribuição Linux OS que está a utilizar. As várias distribuições linux configuram o DHCPv6 de várias maneiras porque utilizam pacotes diferentes.

> [!NOTE]
> Imagens recentes do SUSE Linux e CoreOS no Azure Marketplace foram pré-configuradas com dHCPv6. Não são necessárias alterações adicionais quando utiliza estas imagens.

Este documento descreve como ativar o DHCPv6 para que a sua máquina virtual Linux obtenha um endereço IPv6.

> [!WARNING]
> Ao editar indevidamente ficheiros de configuração da rede, pode perder o acesso à rede ao seu VM. Recomendamos que teste as alterações de configuração em sistemas de não produção. As instruções deste artigo foram testadas nas versões mais recentes das imagens Linux no Azure Marketplace. Para obter instruções mais detalhadas, consulte a documentação para a sua própria versão do Linux.

## <a name="ubuntu"></a>Ubuntu

1. Editar o ficheiro */etc/dhcp/dhclient6.conf* e adicionar a seguinte linha:

        timeout 10;

2. Editar a configuração da rede para a interface eth0 com a seguinte configuração:

   * Em **Ubuntu 12.04 e 14.04,** edite o ficheiro */etc/network/interfaces.d/eth0.cfg.* 
   * No **ubuntu 16.04,** edite o ficheiro */etc/network/interfaces.d/50-cloud-init.cfg.*

         iface eth0 inet6 auto
             up sleep 5
             up dhclient -1 -6 -cf /etc/dhcp/dhclient6.conf -lf /var/lib/dhcp/dhclient6.eth0.leases -v eth0 || true

3. Renovar o endereço IPv6:

    ```bash
    sudo ifdown eth0 && sudo ifup eth0
    ```
Começando com Ubuntu 17.10, o mecanismo de configuração da rede padrão é [NETPLAN]( https://netplan.io).  No tempo de instalação/instantânea, o NETPLAN lê a configuração da rede a partir de ficheiros de configuração YAML neste local: /{lib,etc,run}/netplan/*.yaml.

Por favor, inclua uma declaração *dhcp6:true* para cada interface ethernet na sua configuração.  Por exemplo:
  
        network:
          version: 2
          ethernets:
            eno1:
              dhcp6: true

Durante o arranque precoce, o "renderizador de rede" do netplan escreve a configuração para/executar para entregar o https://netplan.io/referencecontrolo dos dispositivos ao daemon de rede especificado Para obter informações de referência sobre o NETPLAN, ver .
 
## <a name="debian"></a>Debian

1. Editar o ficheiro */etc/dhcp/dhclient6.conf* e adicionar a seguinte linha:

        timeout 10;

2. Editar o ficheiro */etc/rede/interfaces* e adicionar a seguinte configuração:

        iface eth0 inet6 auto
            up sleep 5
            up dhclient -1 -6 -cf /etc/dhcp/dhclient6.conf -lf /var/lib/dhcp/dhclient6.eth0.leases -v eth0 || true

3. Renovar o endereço IPv6:

    ```bash
    sudo ifdown eth0 && sudo ifup eth0
    ```

## <a name="rhel-centos-and-oracle-linux"></a>RHEL, CentOS e Oracle Linux

1. Editar o ficheiro */etc/sysconfig/rede* e adicionar o seguinte parâmetro:

        NETWORKING_IPV6=yes

2. Editar o ficheiro */etc/sysconfig/network-scripts/ifcfg-eth0* e adicionar os seguintes dois parâmetros:

        IPV6INIT=yes
        DHCPV6C=yes

3. Renovar o endereço IPv6:

    ```bash
    sudo ifdown eth0 && sudo ifup eth0
    ```

## <a name="sles-11-and-opensuse-13"></a>SLES 11 e abre SUSE 13

O Recente SUSE Linux Enterprise Server (SLES) e as imagens openSUSE em Azure foram pré-configuradas com DHCPv6. Não são necessárias alterações adicionais quando utiliza estas imagens. Se tiver um VM baseado numa imagem SUSE mais antiga ou personalizada, faça o seguinte:

1. Instale `dhcp-client` a embalagem, se necessário:

    ```bash
    sudo zypper install dhcp-client
    ```

2. Editar o ficheiro */etc/sysconfig/rede/ifcfg-eth0* e adicionar o seguinte parâmetro:

        DHCLIENT6_MODE='managed'

3. Renovar o endereço IPv6:

    ```bash
    sudo ifdown eth0 && sudo ifup eth0
    ```

## <a name="sles-12-and-opensuse-leap"></a>SLES 12 e abre Salto SUSE

As recentes imagens SLES e OpenSUSE em Azure foram pré-configuradas com DHCPv6. Não são necessárias alterações adicionais quando utiliza estas imagens. Se tiver um VM baseado numa imagem SUSE mais antiga ou personalizada, faça o seguinte:

1. Editar o ficheiro */etc/sysconfig/rede/ifcfg-eth0* `#BOOTPROTO='dhcp4'` e substituir o parâmetro pelo seguinte valor:

        BOOTPROTO='dhcp'

2. Ao ficheiro */etc/sysconfig/rede/ifcfg-eth0,* adicione o seguinte parâmetro:

        DHCLIENT6_MODE='managed'

3. Renovar o endereço IPv6:

    ```bash
    sudo ifdown eth0 && sudo ifup eth0
    ```

## <a name="coreos"></a>CoreOS

Imagens recentes do CoreOS em Azure foram pré-configuradas com DHCPv6. Não são necessárias alterações adicionais quando utiliza estas imagens. Se tiver um VM baseado numa imagem coreos mais antiga ou personalizada, faça o seguinte:

1. Editar o ficheiro */etc/systemd/network/10_dhcp.network:*

        [Match]
        eth0

        [Network]
        DHCP=ipv6

2. Renovar o endereço IPv6:

    ```bash
    sudo systemctl restart systemd-networkd
    ```
