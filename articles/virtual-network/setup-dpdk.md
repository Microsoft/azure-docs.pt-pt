---
title: DPDK numa VM Linux do Azure | Documentos da Microsoft
description: Saiba como configurar DPDK numa máquina virtual Linux.
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
ms.openlocfilehash: b50f7c9b76e9309a1ee08257dd8b13ec289397a5
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57775922"
---
# <a name="set-up-dpdk-in-a-linux-virtual-machine"></a>Configurar DPDK numa máquina virtual do Linux

Dados plano Development Kit (DPDK) no Azure oferece uma arquitetura de processamento de pacotes de espaço do usuário mais rápida para aplicações intensivas de desempenho. Essa estrutura ignora a pilha de rede de kernel da máquina virtual.

Processamento de pacotes típicos que utiliza a pilha de rede do kernel, o processo é controlado por interrupção. Quando a interface de rede recebe pacotes de entrada, há uma interrupção de kernel para processar que o pacote e um contexto de mudar a partir do espaço de kernel para o espaço de utilizador. DPDK elimina a alternância de contexto e o método controlado por interrupção em prol de uma implementação de espaço do utilizador que utiliza consultar drivers no modo de processamento de pacotes rápida.

DPDK consiste em conjuntos de bibliotecas de espaço do usuário que fornecem acesso a recursos de nível inferior. Estes recursos podem incluir o hardware, núcleos lógicos, gerenciamento de memória e drivers de modo de consulta para placas de interface de rede.

DPDK podem ser executados em máquinas virtuais do Azure que estiver dando suporte a várias distribuições de sistema operativo. DPDK fornece diferenciação de chave de desempenho nos dirigir implementações de virtualização da função de rede. Essas implementações podem assumir a forma de aplicações virtuais de rede (NVAs), como virtual routers, firewalls, VPNs, balanceadores de carga, pacote evoluído núcleos e aplicativos de denial-of-service (DDoS).

## <a name="benefit"></a>Beneficie

**Superior pacotes por segundo (PPS)**: Ignorar o kernel e ter o controle de pacotes no espaço do usuário reduz a contagem de ciclo, eliminando Alternâncias de contexto. Ele também melhora a taxa de pacotes que são processados por segundo em máquinas virtuais do Linux do Azure.


## <a name="supported-operating-systems"></a>Sistemas operativos suportados

São suportadas as seguintes distribuições da galeria do Azure:

| SO Linux     | Versão de kernel        |
|--------------|----------------       |
| Ubuntu 16.04 | 4.15.0-1015-azure     |
| Ubuntu 18.04 | 4.15.0-1015-azure     |
| SLES 15      | 4.12.14-5.5-azure     |
| RHEL 7.5     | 3.10.0-862.9.1.el7    |
| CentOS 7.5   | 3.10.0-862.3.3.el7    |

**Suporte de kernel personalizado**

Para qualquer versão de kernel de Linux que não esteja listado, consulte [Patches para a criação de um kernel do Linux do Azure otimizados](https://github.com/microsoft/azure-linux-kernel). Para obter mais informações, também pode contactar [ azuredpdk@microsoft.com ](mailto:azuredpdk@microsoft.com). 

## <a name="region-support"></a>Suporte de região

Todas as regiões do Azure suportam DPDK.

## <a name="prerequisites"></a>Pré-requisitos

Funcionamento em rede acelerado tem de estar ativado na máquina virtual do Linux. A máquina virtual deve ter, pelo menos, duas interfaces de rede, com uma interface para a gestão. Saiba como [criar uma máquina virtual Linux com a aceleração de rede ativada](create-vm-accelerated-networking-cli.md).

## <a name="install-dpdk-dependencies"></a>Instalar dependências DPDK

### <a name="ubuntu-1604"></a>Ubuntu 16.04

```bash
sudo add-apt-repository ppa:canonical-server/dpdk-azure -y
sudo apt-get update
sudo apt-get install -y librdmacm-dev librdmacm1 build-essential libnuma-dev libmnl-dev
```

### <a name="ubuntu-1804"></a>Ubuntu 18.04

```bash
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

**Padrão kernel**

```bash
zypper \
  --no-gpg-checks \
  --non-interactive \
  --gpg-auto-import-keys install kernel-default-devel gcc make libnuma-devel numactl librdmacm1 rdma-core-devel
```

## <a name="set-up-the-virtual-machine-environment-once"></a>Configurar o ambiente de máquina virtual (uma vez)

1. [Transferir o mais recente DPDK](https://core.dpdk.org/download). Versão 18.02 ou superior é necessário para o Azure.
2. Criar a configuração padrão com `make config T=x86_64-native-linuxapp-gcc`.
3. Ativar Mellanox PMDs a configuração gerada com `sed -ri 's,(MLX._PMD=)n,\1y,' build/.config`.
4. A compilação com `make`.
5. Instalar com `make install DESTDIR=<output folder>`.

## <a name="configure-the-runtime-environment"></a>Configurar o ambiente de tempo de execução

Após o reinício, execute os seguintes comandos uma vez:

1. Hugepages

   * Configure hugepage ao executar o comando seguinte, uma vez para todas as numanodes:

     ```bash
     echo 1024 | sudo tee
     /sys/devices/system/node/node*/hugepages/hugepages-2048kB/nr_hugepages
     ```

   *  Criar um diretório para a montagem com `mkdir /mnt/huge`.
   *  Hugepages de montagem com `mount -t hugetlbfs nodev /mnt/huge`.
   *  Verifique que hugepages estão reservados com `grep Huge /proc/meminfo`.

     > [!NOTE]
     > Existe uma forma de modificar o ficheiro de grub para que hugepages estão reservados no arranque ao seguir a [instruções](http://dpdk.org/doc/guides/linux_gsg/sys_reqs.html#use-of-hugepages-in-the-linux-environment) para o DPDK. As instruções são na parte inferior da página. Quando estiver a utilizar uma máquina virtual Linux do Azure, modificar arquivos sob **/etc/config/grub.d** em vez disso, para reservar hugepages entre reinícios.

2. Endereços MAC e IP: Utilize `ifconfig –a` para ver o endereço MAC e IP das interfaces de rede. O *VF* interface de rede e *NETVSC* interface de rede têm o mesmo endereço MAC, mas apenas o *NETVSC* interface de rede tem um endereço IP. Interfaces de VF estão em execução como interfaces subordinadas NETVSC interfaces.

3. Endereços PCI

   * Utilizar `ethtool -i <vf interface name>` para saber qual a utilizar para o endereço PCI *VF*.
   * Se *eth0* tenha o accelerated networking ativada, certifique-se de que testpmd acidentalmente não assuma o dispositivo de pci VF para *eth0*. Se o aplicativo DPDK acidentalmente tem sobre a interface de rede de gestão e faz com que a perder a ligação de SSH, utilize a consola de série para parar a aplicação de DPDK. Também pode utilizar a consola de série para interromper ou iniciar a máquina virtual.

4. Carga *ibuverbs* em cada reinício com `modprobe -a ib_uverbs`. Para apenas a 15 de SLES, também carregar *mlx4_ib* com `modprobe -a mlx4_ib`.

## <a name="failsafe-pmd"></a>PMD contra falhas

Tem de executar aplicativos de DPDK sobre a segurança contra falhas PMD exposto no Azure. Se a aplicação é executada diretamente através do PMD VF, não receber **todos os** pacotes que estão destinados à VM, uma vez que alguns pacotes apresentados pela interface do sintética. 

Se executar um aplicativo de DPDK sobre a segurança contra falhas PMD, esta ação garante que o aplicativo recebe todos os pacotes que estão destinados a ele. Ele também torna-se de que o aplicativo permanece em execução no modo DPDK, mesmo que o VF é revogado quando o anfitrião está em manutenção. Para obter mais informações sobre segurança contra falhas PMD, consulte [biblioteca de drivers do modo de consulta isento de falhas](http://doc.dpdk.org/guides/nics/fail_safe.html).

## <a name="run-testpmd"></a>Executar testpmd

Para executar testpmd no modo de raiz, utilize `sudo` antes do *testpmd* comando.

### <a name="basic-sanity-check-failsafe-adapter-initialization"></a>Básico: Verificação da funcionalidade, inicialização de adaptador de segurança contra falhas

1. Execute os seguintes comandos para iniciar um aplicativo de testpmd única porta:

   ```bash
   testpmd -w <pci address from previous step> \
     --vdev="net_vdev_netvsc0,iface=eth1" \
     -- -i \
     --port-topology=chained
    ```

2. Execute os seguintes comandos para iniciar um aplicativo de testpmd de porta dupla:

   ```bash
   testpmd -w <pci address nic1> \
   -w <pci address nic2> \
   --vdev="net_vdev_netvsc0,iface=eth1" \
   --vdev="net_vdev_netvsc1,iface=eth2" \
   -- -i
   ```

   Se estiver a executar o testpmd com mais de duas NICs, o `--vdev` argumento segue este padrão: `net_vdev_netvsc<id>,iface=<vf’s pairing eth>`.

3.  Depois de ser iniciada, executar `show port info all` para verificar as informações da porta. Deverá ver uma ou duas portas DPDK que são net_failsafe (não *net_mlx4*).
4.  Utilize `start <port> /stop <port>` para iniciar o tráfego.

Iniciar os comandos anteriores *testpmd* no modo interativo, que é recomendado para experimentar testpmd comandos.

### <a name="basic-single-sendersingle-receiver"></a>Básico: Recetor de remetente único ou um só

Os seguintes comandos periodicamente os pacotes por segundo estatísticas de impressão:

1. No lado do TX, E.u.a., execute o seguinte comando:

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

Quando estiver a executar os comandos anteriores numa máquina virtual, altere *IP_SRC_ADDR* e *IP_DST_ADDR* no `app/test-pmd/txonly.c` para corresponder o endereço IP real das máquinas virtuais antes de compilar. Caso contrário, os pacotes são largados antes de atingir o recetor.

### <a name="advanced-single-sendersingle-forwarder"></a>Avançado: Reencaminhador de remetente único ou um só
Os seguintes comandos periodicamente os pacotes por segundo estatísticas de impressão:

1. No lado do TX, E.u.a., execute o seguinte comando:

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

Quando estiver a executar os comandos anteriores numa máquina virtual, altere *IP_SRC_ADDR* e *IP_DST_ADDR* no `app/test-pmd/txonly.c` para corresponder o endereço IP real das máquinas virtuais antes de compilar. Caso contrário, os pacotes são largados antes de atingir o reencaminhador. Não será possível ter uma terceira máquina receber tráfego reencaminhado, porque o *testpmd* reencaminhador não modifica os endereços de camada 3, a menos que faça algumas alterações de código.

## <a name="references"></a>Referências

* [Opções de EAL](https://dpdk.org/doc/guides/testpmd_app_ug/run_app.html#eal-command-line-options)
* [Comandos de Testpmd](https://dpdk.org/doc/guides/testpmd_app_ug/run_app.html#testpmd-command-line-options)
