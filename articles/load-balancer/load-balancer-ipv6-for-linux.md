---
title: Configurar DHCPv6 para Os VMs Linux
titleSuffix: Azure Load Balancer
description: Neste artigo, aprenda a configurar DHCPv6 para Os VMs Linux.
services: load-balancer
documentationcenter: na
author: asudbring
keywords: ipv6, balançador de carga azul, pilha dupla, ip público, ipv6 nativo, móvel, iot
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/22/2019
ms.author: allensu
ms.openlocfilehash: d8bd62bab627beb70a8fcba276bf8c2eca309c45
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96006751"
---
# <a name="configure-dhcpv6-for-linux-vms"></a>Configurar DHCPv6 para Os VMs Linux


Algumas das imagens de máquina virtual Linux no Mercado Azure não têm o Protocolo de Configuração do Anfitrião Dinâmico versão 6 (DHCPv6) configurado por padrão. Para apoiar o IPv6, o DHCPv6 tem de ser configurado na distribuição linux OS que está a utilizar. As várias distribuições Linux configuram DHCPv6 de várias maneiras porque usam diferentes pacotes.

> [!NOTE]
> Imagens recentes do SUSE Linux e CoreOS no Azure Marketplace foram pré-configuradas com DHCPv6. Não são necessárias alterações adicionais quando utilizar estas imagens.

Este documento descreve como ativar o DHCPv6 de modo a que a sua máquina virtual Linux obtenha um endereço IPv6.

> [!WARNING]
> Ao editar indevidamente ficheiros de configuração de rede, pode perder o acesso à rede ao seu VM. Recomendamos que teste as alterações de configuração em sistemas de não produção. As instruções deste artigo foram testadas nas versões mais recentes das imagens Linux no Azure Marketplace. Para obter instruções mais detalhadas, consulte a documentação para a sua própria versão do Linux.

## <a name="ubuntu"></a>Ubuntu

1. Editar o ficheiro */etc/dhcp/dhclient6.conf* e adicionar a seguinte linha:

    ```config
    timeout 10;
    ```

2. Editar a configuração da rede para a interface eth0 com a seguinte configuração:

   * No **Ubuntu 12.04 e 14.04,** edite o ficheiro */etc/network/interfaces.d/eth0.cfg.* 
   * No **Ubuntu 16.04,** edite o ficheiro */etc/rede/interfaces.d/50-cloud-init.cfg.*

    ```config
    iface eth0 inet6 auto
        up sleep 5
        up dhclient -1 -6 -cf /etc/dhcp/dhclient6.conf -lf /var/lib/dhcp/dhclient6.eth0.leases -v eth0 || true
    ```

3. Renovar o endereço IPv6:

    ```bash
    sudo ifdown eth0 && sudo ifup eth0
    ```

Começando com Ubuntu 17.10, o mecanismo de configuração de rede padrão é [NETPLAN]( https://netplan.io).  No tempo de instalação/instantiação, NETPLAN lê a configuração da rede a partir de ficheiros de configuração YAML neste local: /{lib,etc,run}/netplan/*.yaml.

Por favor, inclua uma declaração *dhcp6:verdadeira* para cada interface ethernet na sua configuração.  Por exemplo:

```config
network:
  version: 2
  ethernets:
    eno1:
      dhcp6: true
```

Durante o arranque inicial, o netplan "renderizador de rede" escreve a configuração para /executar para entregar o controlo dos dispositivos ao daemon de rede especificado Para obter informações de referência sobre netplan, ver https://netplan.io/reference .
 
## <a name="debian"></a>Debian

1. Editar o ficheiro */etc/dhcp/dhclient6.conf* e adicionar a seguinte linha:

    ```config
    timeout 10;
    ```

2. Editar o ficheiro */etc/rede/interfaces* e adicionar a seguinte configuração:

    ```config
    iface eth0 inet6 auto
        up sleep 5
        up dhclient -1 -6 -cf /etc/dhcp/dhclient6.conf -lf /var/lib/dhcp/dhclient6.eth0.leases -v eth0 || true
    ```

3. Renovar o endereço IPv6:

    ```bash
    sudo ifdown eth0 && sudo ifup eth0
    ```

## <a name="rhel-centos-and-oracle-linux"></a>RHEL, CentOS e Oracle Linux

1. Editar o ficheiro */etc/sysconfig/rede* e adicionar o seguinte parâmetro:

    ```config
    NETWORKING_IPV6=yes
    ```

2. Editar o ficheiro */etc/sysconfig/network-scripts/ifcfg-eth0* e adicionar os seguintes dois parâmetros:

    ```config
    IPV6INIT=yes
    DHCPV6C=yes
    ```

3. Renovar o endereço IPv6:

    ```bash
    sudo ifdown eth0 && sudo ifup eth0
    ```

## <a name="sles-11-and-opensuse-13"></a>SLES 11 e abreSUSE 13

O SUSE Linux Enterprise Server (SLES) e as imagens de SUSE em Azure foram pré-configuradas com DHCPv6. Não são necessárias alterações adicionais quando utilizar estas imagens. Se tiver um VM baseado numa imagem SUSE mais antiga ou personalizada, faça o seguinte:

1. Instale a `dhcp-client` embalagem, se necessário:

    ```bash
    sudo zypper install dhcp-client
    ```

2. Editar o ficheiro */etc/sysconfig/rede/ifcfg-eth0* e adicionar o seguinte parâmetro:

    ```config
    DHCLIENT6_MODE='managed'
    

3. Renew the IPv6 address:

    ```bash
    sudo ifdown eth0 && sudo ifup eth0
    ```

## <a name="sles-12-and-opensuse-leap"></a>SLES 12 e abre O SaltoSSUSE

As recentes imagens SLES e openSUSE em Azure foram pré-configuradas com DHCPv6. Não são necessárias alterações adicionais quando utilizar estas imagens. Se tiver um VM baseado numa imagem SUSE mais antiga ou personalizada, faça o seguinte:

1. Editar o ficheiro */etc/sysconfig/rede/ifcfg-eth0* e substituir o `#BOOTPROTO='dhcp4'` parâmetro pelo seguinte valor:

    ```config
    BOOTPROTO='dhcp'
    ```

2. Ao ficheiro */etc/sysconfig/rede/ifcfg-eth0,* adicione o seguinte parâmetro:

    ```config
    DHCLIENT6_MODE='managed'
    ```

3. Renovar o endereço IPv6:

    ```bash
    sudo ifdown eth0 && sudo ifup eth0
    ```

## <a name="coreos"></a>CoreOS

Imagens recentes do CoreOS em Azure foram pré-configuradas com DHCPv6. Não são necessárias alterações adicionais quando utilizar estas imagens. Se tiver um VM baseado numa imagem CoreOS mais antiga ou personalizada, faça o seguinte:

1. Editar o ficheiro */etc/systemd/network/10_dhcp.network:*

    ```config
    [Match]
    eth0

    [Network]
    DHCP=ipv6
    ```

2. Renovar o endereço IPv6:

    ```bash
    sudo systemctl restart systemd-networkd
    ```
