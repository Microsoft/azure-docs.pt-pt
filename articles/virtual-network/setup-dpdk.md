---
title: DPDK em um Azure Linux VM | Microsoft Docs
description: Conheça os benefícios do Kit de Desenvolvimento de Planos de Dados (DPDK) e como configurar o DPDK numa máquina virtual Linux.
services: virtual-network
documentationcenter: na
author: laxmanrb
manager: gedegrac
editor: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/12/2020
ms.author: labattul
ms.openlocfilehash: 3b4d66525ec52ef2382dfbe97bc09278e35b31fb
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102124674"
---
# <a name="set-up-dpdk-in-a-linux-virtual-machine"></a>Configurar o DPDK numa máquina virtual Linux

O Data Plane Development Kit (DPDK) em Azure oferece uma estrutura de processamento de pacotes de espaço de utilizador mais rápida para aplicações intensivas de desempenho. Esta estrutura contorna a pilha de rede de núcleos da máquina virtual.

No processamento típico de pacotes que utiliza a pilha de rede de núcleo, o processo é conduzido por interrupções. Quando a interface de rede recebe pacotes de entrada, há uma interrupção de kernel para processar o pacote e um interruptor de contexto do espaço do núcleo para o espaço do utilizador. A DPDK elimina a comutação de contexto e o método orientado por interrupção em favor de uma implementação do espaço de utilizador que utiliza os controladores do modo de votação para o processamento rápido de pacotes.

A DPDK consiste em conjuntos de bibliotecas espaciais de utilizador que fornecem acesso a recursos de nível inferior. Estes recursos podem incluir hardware, núcleos lógicos, gestão de memória e controladores de modo de pesquisa para cartões de interface de rede.

A DPDK pode funcionar em máquinas virtuais Azure que suportam múltiplas distribuições de sistemas operativos. A DPDK fornece uma diferenciação de desempenho chave nas implementações de virtualização da função de condução da rede. Estas implementações podem assumir a forma de aparelhos virtuais de rede (NVAs), tais como routers virtuais, firewalls, VPNs, equilibradores de carga, núcleos de pacotes evoluídos e aplicações de negação de serviço (DDoS).

## <a name="benefit"></a>Vantagem

**Pacotes mais elevados por segundo (PPS)**: Contornar o núcleo e assumir o controlo dos pacotes no espaço do utilizador reduz a contagem de ciclos eliminando os interruptores de contexto. Também melhora a taxa de pacotes que são processados por segundo em máquinas virtuais Azure Linux.


## <a name="supported-operating-systems"></a>Sistemas operativos suportados

São suportadas as seguintes distribuições do Mercado Azure:

| Linux OS     | Versão de kernel               | 
|--------------|---------------------------   |
| Ubuntu 16.04 | 4.15.0-1014-azure+           | 
| Ubuntu 18.04 | 4.15.0-1014-azure+           |
| SLES 15 SP1  | 4.12.14-8.19-azure+          | 
| RHEL 7.5     | 3.10.0-862.11.6.el7.x86_64+  | 
| CentOS 7.5   | 3.10.0-862.11.6.el7.x86_64+  | 

**Suporte personalizado de kernel**

Para qualquer versão de kernel Linux que não esteja listada, consulte [Patches para construir um kernel Linux afinado a Azure.](https://github.com/microsoft/azure-linux-kernel) Para mais informações, também pode [aznetdpdk@microsoft.com](mailto:aznetdpdk@microsoft.com) contactar. 

## <a name="region-support"></a>Suporte de região

Todas as regiões de Azure apoiam a DPDK.

## <a name="prerequisites"></a>Pré-requisitos

A ligação de rede acelerada deve ser ativada numa máquina virtual Linux. A máquina virtual deve ter pelo menos duas interfaces de rede, com uma interface para gestão. Não é aconselhável permitir a ligação acelerada da interface de gestão. Saiba como [criar uma máquina virtual Linux com ligação acelerada em rede](create-vm-accelerated-networking-cli.md).

## <a name="install-dpdk-dependencies"></a>Instalar dependências DPDK

### <a name="ubuntu-1604"></a>Ubuntu 16.04

```bash
sudo add-apt-repository ppa:canonical-server/dpdk-azure -y
sudo apt-get update
sudo apt-get install -y librdmacm-dev librdmacm1 build-essential libnuma-dev libmnl-dev
```

### <a name="ubuntu-1804"></a>Ubuntu 18.04

```bash
sudo add-apt-repository ppa:canonical-server/dpdk-azure -y
sudo apt-get update
sudo apt-get install -y librdmacm-dev librdmacm1 build-essential libnuma-dev libmnl-dev
```

### <a name="rhel75centos-75"></a>RHEL7.5/CentOS 7.5

```bash
yum -y groupinstall "Infiniband Support"
sudo dracut --add-drivers "mlx4_en mlx4_ib mlx5_ib" -f
yum install -y gcc kernel-devel-`uname -r` numactl-devel.x86_64 librdmacm-devel libmnl-devel
```

### <a name="sles-15-sp1"></a>SLES 15 SP1

**Núcleo de Azure**

```bash
zypper  \
  --no-gpg-checks \
  --non-interactive \
  --gpg-auto-import-keys install kernel-azure kernel-devel-azure gcc make libnuma-devel numactl librdmacm1 rdma-core-devel
```

**Núcleo padrão**

```bash
zypper \
  --no-gpg-checks \
  --non-interactive \
  --gpg-auto-import-keys install kernel-default-devel gcc make libnuma-devel numactl librdmacm1 rdma-core-devel
```

## <a name="set-up-the-virtual-machine-environment-once"></a>Configurar o ambiente da máquina virtual (uma vez)

1. [Descarregue o mais recente DPDK](https://core.dpdk.org/download). A versão 18.11 LTS ou 19.11 LTS é necessária para o Azure.
2. Construa o config padrão com `make config T=x86_64-native-linuxapp-gcc` .
3. Ativar pmDs Mellanox no config gerado com `sed -ri 's,(MLX._PMD=)n,\1y,' build/.config` .
4. Compilar com `make` .
5. Instale com `make install DESTDIR=<output folder>` .

## <a name="configure-the-runtime-environment"></a>Configure o ambiente de tempo de execução

Depois de reiniciar, executar os seguintes comandos uma vez:

1. Páginas enormes

   * Configure uma enorme página executando o seguinte comando, uma vez para cada nó numa:

     ```bash
     echo 1024 | sudo tee /sys/devices/system/node/node*/hugepages/hugepages-2048kB/nr_hugepages
     ```

   * Crie um diretório para montagem com `mkdir /mnt/huge` .
   * Monte enormes páginas com `mount -t hugetlbfs nodev /mnt/huge` .
   * Verifique se as páginas grandes estão reservadas `grep Huge /proc/meminfo` com .

     > [NOTA] Existe uma forma de modificar o ficheiro de comida para que as páginas enormes sejam reservadas no arranque seguindo as [instruções](https://dpdk.org/doc/guides/linux_gsg/sys_reqs.html#use-of-hugepages-in-the-linux-environment) para o DPDK. As instruções estão na parte inferior da página. Quando estiver a utilizar uma máquina virtual Azure Linux, modifique ficheiros em **/etc/config/grub.d,** para reservar enormes páginas através de reboots.

2. MAC & endereços IP: Utilize `ifconfig –a` para visualizar o endereço MAC e IP das interfaces de rede. A interface de rede *VF* e a interface de rede *NETVSC* têm o mesmo endereço MAC, mas apenas a interface de rede *NETVSC* tem um endereço IP. As interfaces *VF* estão a funcionar como interfaces subordinadas das interfaces *NETVSC.*

3. Endereços PCI

   * Utilize `ethtool -i <vf interface name>` para saber qual o endereço PCI a utilizar para *VF*.
   * Se *a eth0* tiver ativado a ligação em rede acelerada, certifique-se de que o testpmd não assume acidentalmente o dispositivo *PCI VF* para *o eth0*. Se a aplicação DPDK assumir acidentalmente a interface da rede de gestão e fizer com que perca a sua ligação SSH, utilize a consola em série para parar a aplicação DPDK. Também pode utilizar a consola em série para parar ou iniciar a máquina virtual.

4. *Carregue ibuverbs* em cada reinicialização com `modprobe -a ib_uverbs` . Apenas para SLES 15, carregue também *mlx4_ib* com `modprobe -a mlx4_ib` .

## <a name="failsafe-pmd"></a>PMD de segurança de falhas

As aplicações DPDK devem passar por cima do PMD de segurança que está exposto em Azure. Se a aplicação correr diretamente sobre o *PMD VF,* não recebe **todos os** pacotes que estão destinados ao VM, uma vez que alguns pacotes aparecem sobre a interface sintética. 

Se executar uma aplicação DPDK sobre o PMD de segurança, garante que a aplicação recebe todos os pacotes que lhe estão destinados. Também garante que a aplicação continua a funcionar no modo DPDK, mesmo que o VF seja revogado quando o hospedeiro está a ser reparado. Para obter mais informações sobre o PMD de segurança, consulte [a biblioteca do controlador do modo de pesquisa de segurança de falhas](https://doc.dpdk.org/guides/nics/fail_safe.html).

## <a name="run-testpmd"></a>Teste de execução

Para executar o testpmd no modo raiz, utilize `sudo` antes do comando *testpmd.*

### <a name="basic-sanity-check-failsafe-adapter-initialization"></a>Básico: Verificação de sanidade, inicialização do adaptador de segurança

1. Executar os seguintes comandos para iniciar uma única aplicação de teste de porta:

   ```bash
   testpmd -w <pci address from previous step> \
     --vdev="net_vdev_netvsc0,iface=eth1" \
     -- -i \
     --port-topology=chained
    ```

2. Executar os seguintes comandos para iniciar uma aplicação de teste de porta dupla:

   ```bash
   testpmd -w <pci address nic1> \
   -w <pci address nic2> \
   --vdev="net_vdev_netvsc0,iface=eth1" \
   --vdev="net_vdev_netvsc1,iface=eth2" \
   -- -i
   ```

   Se estiver a fazer testes com mais de dois NICs, o `--vdev` argumento segue este padrão: `net_vdev_netvsc<id>,iface=<vf’s pairing eth>` .

3.  Depois de ter começado, corra `show port info all` para verificar a informação do porto. Deve ver uma ou duas portas DPDK que são net_failsafe (não *net_mlx4).*
4.  Use `start <port> /stop <port>` para começar o tráfego.

Os comandos anteriores iniciam *o teste* em modo interativo, que é recomendado para experimentar comandos testpmd.

### <a name="basic-single-sendersingle-receiver"></a>Básico: Único remetente/único recetor

Os seguintes comandos imprimem periodicamente os pacotes por segundo:

1. Do lado TX, executar o seguinte comando:

   ```bash
   testpmd \
     -l <core-list> \
     -n <num of mem channels> \
     -w <pci address of the device you plan to use> \
     --vdev="net_vdev_netvsc<id>,iface=<the iface to attach to>" \
     -- --port-topology=chained \
     --nb-cores <number of cores to use for test pmd> \
     --forward-mode=txonly \
     --eth-peer=<port id>,<receiver peer MAC address> \
     --stats-period <display interval in seconds>
   ```

2. Do lado RX, executar o seguinte comando:

   ```bash
   testpmd \
     -l <core-list> \
     -n <num of mem channels> \
     -w <pci address of the device you plan to use> \
     --vdev="net_vdev_netvsc<id>,iface=<the iface to attach to>" \
     -- --port-topology=chained \
     --nb-cores <number of cores to use for test pmd> \
     --forward-mode=rxonly \
     --eth-peer=<port id>,<sender peer MAC address> \
     --stats-period <display interval in seconds>
   ```

Quando estiver a executar os comandos anteriores numa máquina virtual, altere *IP_SRC_ADDR* e *IP_DST_ADDR* `app/test-pmd/txonly.c` para corresponder ao endereço IP real das máquinas virtuais antes de compilar. Caso contrário, os pacotes são largados antes de chegarem ao recetor.

### <a name="advanced-single-sendersingle-forwarder"></a>Avançado: Remetente único/único avançado
Os seguintes comandos imprimem periodicamente os pacotes por segundo:

1. Do lado TX, executar o seguinte comando:

   ```bash
   testpmd \
     -l <core-list> \
     -n <num of mem channels> \
     -w <pci address of the device you plan to use> \
     --vdev="net_vdev_netvsc<id>,iface=<the iface to attach to>" \
     -- --port-topology=chained \
     --nb-cores <number of cores to use for test pmd> \
     --forward-mode=txonly \
     --eth-peer=<port id>,<receiver peer MAC address> \
     --stats-period <display interval in seconds>
    ```

2. Do lado FWD, executar o seguinte comando:

   ```bash
   testpmd \
     -l <core-list> \
     -n <num of mem channels> \
     -w <pci address NIC1> \
     -w <pci address NIC2> \
     --vdev="net_vdev_netvsc<id>,iface=<the iface to attach to>" \
     --vdev="net_vdev_netvsc<2nd id>,iface=<2nd iface to attach to>" (you need as many --vdev arguments as the number of devices used by testpmd, in this case) \
     -- --nb-cores <number of cores to use for test pmd> \
     --forward-mode=io \
     --eth-peer=<recv port id>,<sender peer MAC address> \
     --stats-period <display interval in seconds>
    ```

Quando estiver a executar os comandos anteriores numa máquina virtual, altere *IP_SRC_ADDR* e *IP_DST_ADDR* `app/test-pmd/txonly.c` para corresponder ao endereço IP real das máquinas virtuais antes de compilar. Caso contrário, os pacotes são largados antes de chegar ao reencaminhador. Não será possível que uma terceira máquina receba tráfego reencaminhado, porque o reencaminhador *de testes* não modifica os endereços da camada 3, a menos que faça algumas alterações de código.

## <a name="references"></a>Referências

* [Opções EAL](https://dpdk.org/doc/guides/testpmd_app_ug/run_app.html#eal-command-line-options)
* [Comandos testpmd](https://dpdk.org/doc/guides/testpmd_app_ug/run_app.html#testpmd-command-line-options)
