---
title: Resolução de problemas conhecidos com VMs HPC e GPU - Azure Virtual Machines | Microsoft Docs
description: Saiba mais sobre problemas conhecidos com tamanhos de HPC e GPU VM em Azure.
author: vermagit
ms.service: virtual-machines
ms.subservice: hpc
ms.topic: article
ms.date: 04/16/2021
ms.author: amverma
ms.reviewer: cynthn
ms.openlocfilehash: f5bdae17126048da153f70bf27609bcc4b92fe21
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/18/2021
ms.locfileid: "107599592"
---
# <a name="known-issues-with-h-series-and-n-series-vms"></a>Problemas conhecidos relacionados com as VMs da série H e série N

Este artigo tenta listar as questões comuns recentes e as suas soluções ao utilizar os VMs HPC e GPU da [série Hpc](../../sizes-hpc.md) e da [série N.](../../sizes-gpu.md)

## <a name="qp0-access-restriction"></a>restrição de acesso qp0

Para evitar o acesso de hardware de baixo nível que pode resultar em vulnerabilidades de segurança, o Queue Pair 0 não está acessível aos VMs dos hóspedes. Isto só deve afetar ações tipicamente associadas à administração do ConnectX InfiniBand NIC, e executar alguns diagnósticos InfiniBand como ibdiagnet, mas não aplicações de utilizador final.

## <a name="mofed-installation-on-ubuntu"></a>Instalação MOFED em Ubuntu
Nas imagens VM do mercado baseado em Ubuntu-18.04 com versão kernels `5.4.0-1039-azure #42` e mais recentes, alguns Mellanox OFED mais antigos são incompatíveis causando um aumento no tempo de arranque VM até 30 minutos em alguns casos. Isto foi reportado para ambas as versões Mellanox OFED 5.2-1.0.4.0 e 5.2-2.2.0.0. A questão é resolvida com Mellanox OFED 5.3-1.0.0.1.
Se for necessário utilizar o INCOMPATÍVEL OFED, uma solução é utilizar a imagem **canonical:UbuntuServer:18_04-lts-gen2:18.04.202101290** imagem VM do mercado ou mais antiga e não atualizar o núcleo.

## <a name="mpi-qp-creation-errors"></a>Erros de criação do MPI QP
Se no meio de execução de quaisquer cargas de trabalho de MPI, erros de criação de QP infiniband, como mostrados abaixo, são lançados, sugerimos reiniciar o VM e re-tentar a carga de trabalho. Esta questão será corrigida no futuro.

```bash
ib_mlx5_dv.c:150  UCX  ERROR mlx5dv_devx_obj_create(QP) failed, syndrome 0: Invalid argument
```

Pode verificar os valores do número máximo de pares de filas quando o problema for observado da seguinte forma.
```bash
[user@azurehpc-vm ~]$ ibv_devinfo -vv | grep qp
max_qp: 4096
```

## <a name="accelerated-networking-on-hb-hc-hbv2-and-ndv2"></a>Rede Acelerada em HB, HC, HBv2 e NDv2

[O Azure Accelerated Networking](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/) está agora disponível nos tamanhos VM habilidosos de RDMA e InfiniBand e SR-IOV habilitados [HB,](../../hb-series.md) [HC,](../../hc-series.md) [HBv2](../../hbv2-series.md)e [NDv2](../../ndv2-series.md). Esta capacidade permite agora um aumento em toda (até 30 Gbps) e latências sobre a rede Azure Ethernet. Embora isto seja separado das capacidades de RDMA sobre a rede InfiniBand, algumas mudanças na plataforma para esta capacidade podem ter impacto no comportamento de certas implementações de MPI ao executar empregos sobre a InfiniBand. Especificamente, a interface InfiniBand em alguns VMs pode ter um nome ligeiramente diferente (mlx5_1 em oposição a mlx5_0 anteriores) e isso pode exigir o ajuste das linhas de comando MPI especialmente quando se utiliza a interface UCX (geralmente com OpenMPI e HPC-X). A solução mais simples atualmente pode ser utilizar o mais recente HPC-X nas imagens CentOS-HPC VM ou desativar a rede acelerada se não for necessário.
Mais detalhes sobre este assunto estão disponíveis neste [artigo da TechCommunity](https://techcommunity.microsoft.com/t5/azure-compute/accelerated-networking-on-hb-hc-and-hbv2/ba-p/2067965) com instruções sobre como lidar com quaisquer questões observadas.

## <a name="infiniband-driver-installation-on-non-sr-iov-vms"></a>Instalação do condutor InfiniBand em VMs não-SR-IOV

Atualmente H16r, H16mr e NC24r não estão ativados com SR-IOV. Alguns detalhes sobre a bifurcação da pilha InfiniBand estão [aqui.](../../sizes-hpc.md#rdma-capable-instances)
O InfiniBand pode ser configurado nos tamanhos VM ativados sr-IOV com os condutores OFED, enquanto os tamanhos VM não-SR-IOV requerem condutores de ND. Este suporte IB está disponível adequadamente para [CentOS, RHEL e Ubuntu.](configure.md)

## <a name="duplicate-mac-with-cloud-init-with-ubuntu-on-h-series-and-n-series-vms"></a>Duplicar MAC com cloud-init com Ubuntu na série H e VMs da série N

Há um problema conhecido com a inição de nuvem nas imagens Ubuntu VM enquanto tenta trazer a interface IB. Isto pode acontecer no reboot de VM ou ao tentar criar uma imagem VM após a generalização. Os registos de arranque VM podem mostrar um erro semelhante:
```console
“Starting Network Service...RuntimeError: duplicate mac found! both 'eth1' and 'ib0' have mac”.
```

Este MAC duplicado com cloud-init em Ubuntu é uma questão conhecida. Isto será resolvido em núcleos mais recentes. Se a questão for encontrada, a solução é:
1) Implementar a imagem VM do mercado (Ubuntu 18.04)
2) Instale os pacotes de software necessários para permitir o IB[(instrução aqui)](https://techcommunity.microsoft.com/t5/azure-compute/configuring-infiniband-for-ubuntu-hpc-and-gpu-vms/ba-p/1221351)
3) Editar waagent.conf para alterar EnableRDMA=y
4) Desativar a rede em nuvem
    ```console
    echo network: {config: disabled} | sudo tee /etc/cloud/cloud.cfg.d/99-disable-network-config.cfg
    ```
5) Editar o ficheiro de configuração de rede do Netplan gerado por cloud-init para remover o MAC
    ```console
    sudo bash -c "cat > /etc/netplan/50-cloud-init.yaml" <<'EOF'
    network:
      ethernets:
        eth0:
          dhcp4: true
      version: 2
    EOF
    ```

## <a name="dram-on-hb-series-vms"></a>DRAM em VMs da série HB

Os VMs da série HB só podem expor 228 GB de RAM a VMs convidados neste momento. Da mesma forma, 458 GB em HBv2 e 448 GB em HBv3 VMs. Isto deve-se a uma conhecida limitação do hipervisor Azure para evitar que as páginas sejam atribuídas ao DRAM local dos domínios DAM CCX (NUMA) reservados para o VM convidado.

## <a name="gss-proxy"></a>GSS Proxy

A GSS Proxy tem um bug conhecido no CentOS/RHEL 7.5 que pode manifestar-se como uma penalidade significativa de desempenho e capacidade de resposta quando usado com NFS. Isto pode ser atenuado com:

```console
sed -i 's/GSS_USE_PROXY="yes"/GSS_USE_PROXY="no"/g' /etc/sysconfig/nfs
```

## <a name="cache-cleaning"></a>Limpeza cache

Nos sistemas HPC, é frequentemente útil limpar a memória depois de um trabalho ter terminado antes de o próximo utilizador ser atribuído o mesmo nó. Depois de executar aplicações no Linux, poderá descobrir que a sua memória disponível reduz enquanto a memória do tampão aumenta, apesar de não ter execução de nenhuma aplicação.

![Screenshot do pedido de comando antes da limpeza](./media/known-issues/cache-cleaning-1.png)

A utilização `numactl -H` mostrará com que NUMAnode a memória é tamponada (possivelmente todas). No Linux, os utilizadores podem limpar as caches de três formas de devolver a memória tamponada ou em cache para 'grátis'. Tens de ser raiz ou ter permissões de sudo.

```console
echo 1 > /proc/sys/vm/drop_caches [frees page-cache]
echo 2 > /proc/sys/vm/drop_caches [frees slab objects e.g. dentries, inodes]
echo 3 > /proc/sys/vm/drop_caches [cleans page-cache and slab objects]
```

![Screenshot do pedido de comando após a limpeza](./media/known-issues/cache-cleaning-2.png)

## <a name="kernel-warnings"></a>Avisos de kernel

Pode ignorar as seguintes mensagens de aviso de núcleo ao iniciar um VM da série HB em Linux. Isto deve-se a uma conhecida limitação do hipervisor Azure que será abordado ao longo do tempo.

```console
[  0.004000] WARNING: CPU: 4 PID: 0 at arch/x86/kernel/smpboot.c:376 topology_sane.isra.3+0x80/0x90
[  0.004000] sched: CPU #4's llc-sibling CPU #0 is not on the same node! [node: 1 != 0]. Ignoring dependency.
[  0.004000] Modules linked in:
[  0.004000] CPU: 4 PID: 0 Comm: swapper/4 Not tainted 3.10.0-957.el7.x86_64 #1
[  0.004000] Hardware name: Microsoft Corporation Virtual Machine/Virtual Machine, BIOS 090007 05/18/2018
[  0.004000] Call Trace:
[  0.004000] [<ffffffffb8361dc1>] dump_stack+0x19/0x1b
[  0.004000] [<ffffffffb7c97648>] __warn+0xd8/0x100
[  0.004000] [<ffffffffb7c976cf>] warn_slowpath_fmt+0x5f/0x80
[  0.004000] [<ffffffffb7c02b34>] ? calibrate_delay+0x3e4/0x8b0
[  0.004000] [<ffffffffb7c574c0>] topology_sane.isra.3+0x80/0x90
[  0.004000] [<ffffffffb7c57782>] set_cpu_sibling_map+0x172/0x5b0
[  0.004000] [<ffffffffb7c57ce1>] start_secondary+0x121/0x270
[  0.004000] [<ffffffffb7c000d5>] start_cpu+0x5/0x14
[  0.004000] ---[ end trace 73fc0e0825d4ca1f ]---
```


## <a name="next-steps"></a>Passos seguintes

- Reveja a [visão geral](hb-series-overview.md) da série HB e [a visão geral da série HC](hc-series-overview.md) para aprender sobre a configuração ideal das cargas de trabalho para desempenho e escalabilidade.
- Leia sobre os últimos anúncios, exemplos de carga de trabalho do HPC e resultados de desempenho nos [Blogs comunitários Azure Compute Tech.](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute)
- Para uma visão arquitetónica de nível superior da execução das cargas de trabalho do HPC, consulte [a High Performance Computing (HPC) em Azure](/azure/architecture/topics/high-performance-computing/).
