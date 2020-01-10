---
title: DPDK em uma VM Linux do Azure | Microsoft Docs
description: Saiba como configurar o DPDK em uma máquina virtual do Linux.
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
ms.date: 07/27/2018
ms.author: labattul
ms.openlocfilehash: 876e64cd29aabe1fd4274872800a29cf1a83a0d6
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75350496"
---
# <a name="set-up-dpdk-in-a-linux-virtual-machine"></a>Configurar o DPDK em uma máquina virtual Linux

O DPDK (data avião Development Kit) no Azure oferece uma estrutura de processamento de pacotes de espaço de usuário mais rápida para aplicativos com uso intensivo de desempenho. Essa estrutura ignora a pilha de rede do kernel da máquina virtual.

No processamento de pacotes típico que usa a pilha de rede do kernel, o processo é controlado por interrupção. Quando a interface de rede recebe pacotes de entrada, há uma interrupção de kernel para processar o pacote e uma alternância de contexto do espaço do kernel para o espaço do usuário. O DPDK elimina a alternância de contexto e o método controlado por interrupção em favor de uma implementação de espaço de usuário que usa drivers do modo de sondagem para processamento rápido de pacotes.

O DPDK consiste em conjuntos de bibliotecas de espaço do usuário que fornecem acesso a recursos de nível inferior. Esses recursos podem incluir hardware, núcleos lógicos, gerenciamento de memória e drivers do modo de sondagem para placas de interface de rede.

O DPDK pode ser executado em máquinas virtuais do Azure que dão suporte a várias distribuições de sistema operacional. O DPDK fornece diferenciação de desempenho importante na condução de implementações de virtualização de função de rede. Essas implementações podem assumir a forma de NVAs (soluções de virtualização de rede), como roteadores virtuais, firewalls, VPNs, balanceadores de carga, núcleos de pacotes desenvolvidos e aplicativos de DDoS (negação de serviço).

## <a name="benefit"></a>Beneficie

**Pacotes mais altos por segundo (PPS)** : ignorar o kernel e assumir o controle de pacotes no espaço do usuário reduz a contagem de ciclos eliminando alternâncias de contexto. Ele também melhora a taxa de pacotes processados por segundo em máquinas virtuais Linux do Azure.


## <a name="supported-operating-systems"></a>Sistemas operativos suportados

Há suporte para as seguintes distribuições da galeria do Azure:

| SO Linux     | Versão de kernel        |
|--------------|----------------       |
| Ubuntu 16.04 | 4.15.0-1015-Azure     |
| Ubuntu 18.04 | 4.15.0-1015-Azure     |
| SLES 15      | 4.12.14-5.5-Azure     |
| RHEL 7.5     | 3.10.0-862.9.1.el7    |
| CentOS 7.5   | 3.10.0-862.3.3.el7    |

**Suporte a kernel personalizado**

Para qualquer versão do kernel do Linux que não esteja listada, consulte [patches para criar um kernel do Linux ajustado para o Azure](https://github.com/microsoft/azure-linux-kernel). Para obter mais informações, você também pode entrar em contato com [azuredpdk@microsoft.com](mailto:azuredpdk@microsoft.com). 

## <a name="region-support"></a>Suporte de região

Todas as regiões do Azure dão suporte a DPDK.

## <a name="prerequisites"></a>Pré-requisitos

A rede acelerada deve estar habilitada em uma máquina virtual Linux. A máquina virtual deve ter pelo menos duas interfaces de rede, com uma interface para gerenciamento. Saiba como [criar uma máquina virtual Linux com rede acelerada habilitada](create-vm-accelerated-networking-cli.md).

## <a name="install-dpdk-dependencies"></a>Instalar dependências do DPDK

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

### <a name="sles-15"></a>SLES 15

**Kernel do Azure**

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

## <a name="set-up-the-virtual-machine-environment-once"></a>Configurar o ambiente de máquina virtual (uma vez)

1. [Baixe o DPDK mais recente](https://core.dpdk.org/download). A versão 18, 2 ou superior é necessária para o Azure.
2. Crie a configuração padrão com `make config T=x86_64-native-linuxapp-gcc`.
3. Habilite o Mellanox PMDs na configuração gerada com `sed -ri 's,(MLX._PMD=)n,\1y,' build/.config`.
4. Compile com `make`.
5. Instale com o `make install DESTDIR=<output folder>`.

## <a name="configure-the-runtime-environment"></a>Configurar o ambiente de tempo de execução

Após a reinicialização, execute os seguintes comandos uma vez:

1. Hugepages

   * Configure o hugepage executando o seguinte comando, uma vez para todos os NumaNodes:

     ```bash
     echo 1024 | sudo tee
     /sys/devices/system/node/node*/hugepages/hugepages-2048kB/nr_hugepages
     ```

   * Crie um diretório para montagem com `mkdir /mnt/huge`.
   * Monte hugepages com `mount -t hugetlbfs nodev /mnt/huge`.
   * Verifique se hugepages estão reservados com `grep Huge /proc/meminfo`.

     > [!NOTE]
     > Há uma maneira de modificar o arquivo grub para que hugepages sejam reservados na inicialização seguindo as [instruções](https://dpdk.org/doc/guides/linux_gsg/sys_reqs.html#use-of-hugepages-in-the-linux-environment) para o DPDK. As instruções estão na parte inferior da página. Quando você estiver usando uma máquina virtual Linux do Azure, modifique os arquivos em **/etc/config/grub.d** em vez disso, para reservar hugepages entre reinicializações.

2. Endereços IP do MAC &: Use `ifconfig –a` para exibir o MAC e o endereço IP das interfaces de rede. A interface de rede *VF* e a interface de rede *NETVSC* têm o mesmo endereço Mac, mas apenas a interface de rede *NETVSC* tem um endereço IP. As interfaces VF são executadas como interfaces subordinadas das interfaces NETVSC.

3. Endereços PCI

   * Use `ethtool -i <vf interface name>` para descobrir qual endereço de PCI usar para o *FV*.
   * Se o *eth0* tiver a rede acelerada habilitada, certifique-se de que o testpmd não assuma acidentalmente o dispositivo PCI VF para *eth0*. Se o aplicativo DPDK assumir acidentalmente a interface de rede de gerenciamento e fizer com que você perca a conexão SSH, use o console serial para interromper o aplicativo DPDK. Você também pode usar o console serial para parar ou iniciar a máquina virtual.

4. Carregue *ibuverbs* em cada reinicialização com `modprobe -a ib_uverbs`. Somente para SLES 15, carregue também *mlx4_ib* com `modprobe -a mlx4_ib`.

## <a name="failsafe-pmd"></a>PMD failsafe

Os aplicativos DPDK devem ser executados sobre o PMD de failsafe que é exposto no Azure. Se o aplicativo for executado diretamente por meio do PMD VF, ele não receberá **todos os** pacotes destinados à VM, já que alguns pacotes aparecem na interface sintética. 

Se você executar um aplicativo DPDK no PMD de failsafe, ele garante que o aplicativo receba todos os pacotes destinados a ele. Ele também garante que o aplicativo continue em execução no modo DPDK, mesmo que o FV seja revogado quando o host estiver sendo atendido. Para obter mais informações sobre PMD FailSafe, consulte [biblioteca de drivers do modo de sondagem com segurança](https://doc.dpdk.org/guides/nics/fail_safe.html).

## <a name="run-testpmd"></a>Executar testpmd

Para executar testpmd no modo raiz, use `sudo` antes do comando *testpmd* .

### <a name="basic-sanity-check-failsafe-adapter-initialization"></a>Básico: verificação de integridade, inicialização do adaptador de failsafe

1. Execute os seguintes comandos para iniciar um aplicativo testpmd de porta única:

   ```bash
   testpmd -w <pci address from previous step> \
     --vdev="net_vdev_netvsc0,iface=eth1" \
     -- -i \
     --port-topology=chained
    ```

2. Execute os seguintes comandos para iniciar um aplicativo testpmd de porta dupla:

   ```bash
   testpmd -w <pci address nic1> \
   -w <pci address nic2> \
   --vdev="net_vdev_netvsc0,iface=eth1" \
   --vdev="net_vdev_netvsc1,iface=eth2" \
   -- -i
   ```

   Se você estiver executando o testpmd com mais de duas NICs, o argumento `--vdev` seguirá esse padrão: `net_vdev_netvsc<id>,iface=<vf’s pairing eth>`.

3.  Depois de iniciado, execute `show port info all` para verificar as informações de porta. Você deve ver uma ou duas portas DPDK net_failsafe (não *net_mlx4*).
4.  Use `start <port> /stop <port>` para iniciar o tráfego.

Os comandos anteriores iniciam o *testpmd* no modo interativo, que é recomendado para experimentar os comandos do testpmd.

### <a name="basic-single-sendersingle-receiver"></a>Básico: único remetente/receptor único

Os comandos a seguir imprimem periodicamente as estatísticas de pacotes por segundo:

1. No lado do TX, execute o seguinte comando:

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

2. No lado do RX, execute o seguinte comando:

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

Quando você estiver executando os comandos anteriores em uma máquina virtual, altere *IP_SRC_ADDR* e *IP_DST_ADDR* em `app/test-pmd/txonly.c` para corresponder ao endereço IP real das máquinas virtuais antes de Compilar. Caso contrário, os pacotes serão descartados antes de alcançar o receptor.

### <a name="advanced-single-sendersingle-forwarder"></a>Avançado: único remetente/encaminhador único
Os comandos a seguir imprimem periodicamente as estatísticas de pacotes por segundo:

1. No lado do TX, execute o seguinte comando:

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

2. No lado do FWD, execute o seguinte comando:

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

Quando você estiver executando os comandos anteriores em uma máquina virtual, altere *IP_SRC_ADDR* e *IP_DST_ADDR* em `app/test-pmd/txonly.c` para corresponder ao endereço IP real das máquinas virtuais antes de Compilar. Caso contrário, os pacotes serão descartados antes de alcançar o encaminhador. Você não poderá fazer com que uma terceira máquina receba tráfego encaminhado, porque o encaminhador *testpmd* não modifica os endereços de camada 3, a menos que você faça algumas alterações de código.

## <a name="references"></a>Referências

* [Opções de EAL](https://dpdk.org/doc/guides/testpmd_app_ug/run_app.html#eal-command-line-options)
* [Comandos Testpmd](https://dpdk.org/doc/guides/testpmd_app_ug/run_app.html#testpmd-command-line-options)
