---
title: DPDK em um Azure Linux VM [ Microsoft Docs
description: Aprenda a configurar dPDK numa máquina virtual Linux.
services: virtual-network
documentationcenter: na
author: laxmanrb
manager: gedegrac
editor: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: NA
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/12/2020
ms.author: labattul
ms.openlocfilehash: 79e06fe95b48468616dce913e19c430dc2818719
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83744871"
---
# <a name="set-up-dpdk-in-a-linux-virtual-machine"></a>Configurar dPDK numa máquina virtual Linux

O Kit de Desenvolvimento de Planos de Dados (DPDK) no Azure oferece um quadro de processamento de pacotes de espaço de utilizador mais rápido para aplicações intensivas de desempenho. Esta estrutura contorna a pilha de rede de núcleos da máquina virtual.

No processamento típico de pacotes que utiliza a pilha de rede kernel, o processo é conduzido por interrupção. Quando a interface de rede recebe pacotes de entrada, há uma interrupção do kernel para processar o pacote e um interruptor de contexto do espaço kernel para o espaço do utilizador. A DPDK elimina a comutação de contexto e o método de interrupção em favor de uma implementação do espaço de utilizador que utiliza os condutores do modo de sondagem para processamento rápido de pacotes.

A DPDK é constituída por conjuntos de bibliotecas espaço-utilizador que proporcionam acesso a recursos de nível inferior. Estes recursos podem incluir hardware, núcleos lógicos, gestão de memória e controladores de modo de pesquisa para cartões de interface de rede.

O DPDK pode funcionar em máquinas virtuais Azure que estão a suportar várias distribuições de sistemas operativos. O DPDK fornece diferenciação de desempenho chave nas implementações de virtualização da função de rede de condução. Estas implementações podem assumir a forma de aparelhos virtuais de rede (NVAs), tais como routers virtuais, firewalls, VPNs, equilibradores de carga, núcleos de pacotes evoluídos e aplicações de negação de serviço (DDoS).

## <a name="benefit"></a>Vantagem

**Pacotes mais elevados por segundo (PPS)**: Contornar o núcleo e assumir o controlo dos pacotes no espaço do utilizador reduz a contagem de ciclos eliminando os interruptores de contexto. Também melhora a taxa de pacotes que são processados por segundo em máquinas virtuais Azure Linux.


## <a name="supported-operating-systems"></a>Sistemas operativos suportados

As seguintes distribuições do Mercado Azure são suportadas:

| Linux OS     | Versão de kernel               | 
|--------------|---------------------------   |
| Ubuntu 16.04 | 4.15.0-1014-azure+           | 
| Ubuntu 18.04 | 4.15.0-1014-azure+           |
| SLES 15 SP1  | 4.12.14-8.27-azure+          | 
| RHEL 7.5     | 3.10.0-862.11.6.el7.x86_64+  | 
| CentOS 7.5   | 3.10.0-862.11.6.el7.x86_64+  | 

**Suporte personalizado de kernel**

Para qualquer versão linux kernel que não esteja listada, consulte [Patches para construir um kernel Linux afinado em Azure.](https://github.com/microsoft/azure-linux-kernel) Para mais informações, também pode [azuredpdk@microsoft.com](mailto:azuredpdk@microsoft.com) contactar. 

## <a name="region-support"></a>Suporte de região

Todas as regiões azure apoiam o DPDK.

## <a name="prerequisites"></a>Pré-requisitos

A ligação acelerada deve ser ativada numa máquina virtual Linux. A máquina virtual deve ter pelo menos duas interfaces de rede, com uma interface para gestão. Aprenda a [criar uma máquina virtual Linux com rede acelerada ativada](create-vm-accelerated-networking-cli.md).

## <a name="install-dpdk-dependencies"></a>Instalar dependências de DPDK

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

**Núcleo azul**

```bash
zypper  \
  --no-gpg-checks \
  --non-interactive \
  --gpg-auto-import-keys install kernel-azure kernel-devel-azure gcc make libnuma-devel numactl librdmacm1 rdma-core-devel
```

**Kernel padrão**

```bash
zypper \
  --no-gpg-checks \
  --non-interactive \
  --gpg-auto-import-keys install kernel-default-devel gcc make libnuma-devel numactl librdmacm1 rdma-core-devel
```

## <a name="set-up-the-virtual-machine-environment-once"></a>Configurar o ambiente da máquina virtual (uma vez)

1. [Descarregue o mais recente DPDK](https://core.dpdk.org/download). Versão 18.11 LTS ou 19.11 LTS é necessária para o Azure.
2. Construa o config predefinido com `make config T=x86_64-native-linuxapp-gcc` .
3. Ative pmDs Mellanox no config gerado com `sed -ri 's,(MLX._PMD=)n,\1y,' build/.config` .
4. Compilar `make` com.
5. Instale com `make install DESTDIR=<output folder>` .

## <a name="configure-the-runtime-environment"></a>Configure o ambiente de tempo de execução

Depois de reiniciar, executar os seguintes comandos uma vez:

1. Páginas enormes

   * Configure a página enorme executando o seguinte comando, uma vez por cada nó de numa:

     ```bash
     echo 1024 | sudo tee /sys/devices/system/node/node*/hugepages/hugepages-2048kB/nr_hugepages
     ```

   * Crie um diretório para montagem com `mkdir /mnt/huge` .
   * Monte páginas enormes `mount -t hugetlbfs nodev /mnt/huge` com.
   * Verifique se as páginas enormes estão reservadas `grep Huge /proc/meminfo` com.

     > [NOTA] Existe uma maneira de modificar o ficheiro de larvas para que as páginas enormes sejam reservadas no arranque seguindo as [instruções](https://dpdk.org/doc/guides/linux_gsg/sys_reqs.html#use-of-hugepages-in-the-linux-environment) para o DPDK. As instruções estão na parte inferior da página. Quando estiver a utilizar uma máquina virtual Azure Linux, modifique os ficheiros em vez de **/etc/config/grub.d,** para reservar páginas enormes através de reboots.

2. MAC & endereços IP: Utilize `ifconfig –a` para visualizar o endereço MAC e IP das interfaces de rede. A interface de rede *VF* e a interface de rede *NETVSC* têm o mesmo endereço MAC, mas apenas a interface de rede *NETVSC* tem um endereço IP. As interfaces *VF* estão a funcionar como interfaces subordinadas das interfaces *NETVSC.*

3. Endereços PCI

   * Utilize para descobrir qual o `ethtool -i <vf interface name>` endereço PCI a utilizar para *VF*.
   * Se o *eth0* tiver ativado a rede acelerada, certifique-se de que o testpmd não assume acidentalmente o dispositivo *vf* pci para o *eth0*. Se a aplicação DPDK assumir acidentalmente a interface da rede de gestão e fizer com que perca a ligação SSH, utilize a consola em série para parar a aplicação DPDK. Também pode utilizar a consola em série para parar ou ligar a máquina virtual.

4. Carregue *ibuverbos* em cada reinicialização com `modprobe -a ib_uverbs` . Apenas para sles 15, também carregue *mlx4_ib* com `modprobe -a mlx4_ib` .

## <a name="failsafe-pmd"></a>PMD de segurança

As aplicações DPDK devem passar por cima do PMD de segurança que está exposto em Azure. Se a aplicação passar diretamente sobre o *VF* PMD, não recebe **todos os** pacotes que estão destinados ao VM, uma vez que alguns pacotes aparecem sobre a interface sintética. 

Se executar uma aplicação DPDK sobre o PMD de segurança, garante que a aplicação recebe todos os pacotes que estão destinados a ele. Também garante que a aplicação continua a funcionar no modo DPDK, mesmo que o VF seja revogado quando o hospedeiro está a ser reparado. Para obter mais informações sobre o PMD safe fail, consulte a biblioteca do condutor do modo de [sondagem safe Fail](https://doc.dpdk.org/guides/nics/fail_safe.html).

## <a name="run-testpmd"></a>Executar testpmd

Para executar o testpmd no modo raiz, utilize `sudo` antes do comando *testpmd.*

### <a name="basic-sanity-check-failsafe-adapter-initialization"></a>Básico: Verificação de sanidade, inicialização do adaptador failsafe

1. Executar os seguintes comandos para iniciar uma única aplicação de teste portuário:

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

3.  Depois de começar, corra `show port info all` para verificar a informação do porto. Deve ver uma ou duas portas DPDK que são net_failsafe (não *net_mlx4).*
4.  Usa `start <port> /stop <port>` para iniciar o trânsito.

Os comandos anteriores iniciam o *teste* em modo interativo, que é recomendado para a experimentação de comandos testpmd.

### <a name="basic-single-sendersingle-receiver"></a>Básico: Remetente único/recetor único

Os seguintes comandos imprimem periodicamente os pacotes por segunda estatística:

1. Do lado tX, corra o seguinte comando:

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

2. Do lado RX, corra o seguinte comando:

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

Quando estiver a executar os comandos anteriores numa máquina virtual, altere *IP_SRC_ADDR* e *IP_DST_ADDR* `app/test-pmd/txonly.c` para corresponder ao endereço IP real das máquinas virtuais antes de compilar. Caso contrário, os pacotes são retirados antes de chegarem ao recetor.

### <a name="advanced-single-sendersingle-forwarder"></a>Avançado: Remetente único/único forwardr
Os seguintes comandos imprimem periodicamente os pacotes por segunda estatística:

1. Do lado tX, corra o seguinte comando:

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

2. Do lado da FWD, corra o seguinte comando:

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

Quando estiver a executar os comandos anteriores numa máquina virtual, altere *IP_SRC_ADDR* e *IP_DST_ADDR* `app/test-pmd/txonly.c` para corresponder ao endereço IP real das máquinas virtuais antes de compilar. Caso contrário, os pacotes são retirados antes de chegarem ao avançado. Não poderá ter uma terceira máquina a receber tráfego encaminhada, porque o avançado *testpmd* não modifica os endereços da camada 3, a menos que faça algumas alterações de código.

## <a name="references"></a>Referências

* [Opções EAL](https://dpdk.org/doc/guides/testpmd_app_ug/run_app.html#eal-command-line-options)
* [Comandos Testpmd](https://dpdk.org/doc/guides/testpmd_app_ug/run_app.html#testpmd-command-line-options)
