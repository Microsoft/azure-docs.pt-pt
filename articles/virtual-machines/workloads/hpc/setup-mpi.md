---
title: Configurar interface de passagem de mensagens para HPC - Azure Virtual Machines | Microsoft Docs
description: Saiba como configurar o MPI para o HPC em Azure.
author: vermagit
ms.service: virtual-machines
ms.subservice: hpc
ms.topic: article
ms.date: 03/18/2021
ms.author: amverma
ms.reviewer: cynthn
ms.openlocfilehash: 8f071dfe817d15b745575fbfb70ff662a643db70
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "104721353"
---
# <a name="set-up-message-passing-interface-for-hpc"></a>Configurar interface de passagem de mensagens para HPC

A [Interface de Passagem de Mensagens (MPI)](https://en.wikipedia.org/wiki/Message_Passing_Interface) é uma biblioteca aberta e padrão de facto para a paralelização da memória distribuída. É comumente usado em muitas cargas de trabalho de HPC. As cargas de trabalho do HPC nas [VMs da](../../sizes-hpc.md#rdma-capable-instances) série [H](../../sizes-hpc.md) e da [série N](../../sizes-gpu.md) podem utilizar MPI para comunicar sobre a baixa latência e a rede InfiniBand de alta largura de banda.
- O SR-IOV permitiu que os tamanhos de VM em Azure permitissem que quase qualquer sabor de MPI fosse usado com Mellanox OFED.
- Em VMs não-SR-IOV habilitados, implementações de MPI suportadas utilizam a interface Microsoft Network Direct (ND) para comunicar entre VMs. Assim, apenas as versões Microsoft MPI (MS-MPI) 2012 R2 ou posterior e Intel MPI 5.x são suportadas. Versões posteriores (2017, 2018) da biblioteca de tempo de execução do INTEL MPI podem ou não ser compatíveis com os controladores Azure RDMA.

Para [os VMs com capacidade](../../sizes-hpc.md#rdma-capable-instances)SR-IOV, [as imagens CentOS-HPC VM](configure.md#centos-hpc-vm-images) são adequadas à versão 7.6 e posterior. Estas imagens VM vêm otimizadas e pré-carregadas com os condutores ofed para RDMA e várias bibliotecas de MPI comumente usadas e pacotes de computação científica e são a maneira mais fácil de começar.

Embora os exemplos aqui sejam para RHEL/CentOS, mas os passos são gerais e podem ser usados para qualquer sistema operativo Linux compatível, como Ubuntu (16.04, 18.04 19.04, 20.04) e SLES (12 SP4 e 15). Mais exemplos para a criação de outras implementações de MPI em outros distros está no [repo azhpc-images](https://github.com/Azure/azhpc-images/blob/master/ubuntu/ubuntu-18.x/ubuntu-18.04-hpc/install_mpis.sh).

> [!NOTE]
> A execução de postos de trabalho de MPI em SR-IOV habilitados VMs com certas bibliotecas MPI (como mpi plataforma) pode exigir a criação de chaves de partição (p-keys) através de um inquilino para isolamento e segurança. Siga os passos na secção [de chaves de partição Discover](#discover-partition-keys) para obter detalhes sobre a determinação dos valores da chave p e defini-los corretamente para um trabalho de MPI com essa biblioteca MPI.

> [!NOTE]
> Os códigos abaixo são exemplos. Recomendamos a utilização das versões mais recentes e estáveis dos pacotes, ou referindo-se ao [repo azhpc-images](https://github.com/Azure/azhpc-images/blob/master/ubuntu/ubuntu-18.x/ubuntu-18.04-hpc/install_mpis.sh).

## <a name="ucx"></a>UCX

[Unified Communication X (UCX)](https://github.com/openucx/ucx) é um quadro de APIs de comunicação para hpc. Está otimizado para comunicação MPI sobre a InfiniBand e funciona com muitas implementações de MPI, tais como OpenMPI e MPICH.

```bash
wget https://github.com/openucx/ucx/releases/download/v1.4.0/ucx-1.4.0.tar.gz
tar -xvf ucx-1.4.0.tar.gz
cd ucx-1.4.0
./configure --prefix=<ucx-install-path>
make -j 8 && make install
```

> [!NOTE]
> As recentes construções da UCX resolveram um [problema](https://github.com/openucx/ucx/pull/5965) pelo qual a interface InfiniBand certa é escolhida na presença de múltiplas interfaces NIC. Mais detalhes [aqui](hb-hc-known-issues.md#accelerated-networking-on-hb-hc-hbv2-and-ndv2) sobre a execução de MPI sobre InfiniBand quando a rede acelerada está ativada no VM.

## <a name="hpc-x"></a>HPC-X

O [kit de ferramentas de software HPC-X](https://www.mellanox.com/products/hpc-x-toolkit) contém UCX e HCOLL e pode ser construído contra a UCX.

```bash
HPCX_VERSION="v2.6.0"
HPCX_DOWNLOAD_URL=https://azhpcstor.blob.core.windows.net/azhpc-images-store/hpcx-v2.6.0-gcc-MLNX_OFED_LINUX-5.0-1.0.0.0-redhat7.7-x86_64.tbz
get --retry-connrefused --tries=3 --waitretry=5 $HPCX_DOWNLOAD_URL
tar -xvf hpcx-${HPCX_VERSION}-gcc-MLNX_OFED_LINUX-5.0-1.0.0.0-redhat7.7-x86_64.tbz
mv hpcx-${HPCX_VERSION}-gcc-MLNX_OFED_LINUX-5.0-1.0.0.0-redhat7.7-x86_64 ${INSTALL_PREFIX}
HPCX_PATH=${INSTALL_PREFIX}/hpcx-${HPCX_VERSION}-gcc-MLNX_OFED_LINUX-5.0-1.0.0.0-redhat7.7-x86_64
```

Executar HPC-X

```bash
${HPCX_PATH}mpirun -np 2 --map-by ppr:2:node -x UCX_TLS=rc ${HPCX_PATH}/ompi/tests/osu-micro-benchmarks-5.3.2/osu_latency
```
> [!NOTE] 
> Com HPC-X 2.7.4+, pode ser necessário passar explicitamente LD_LIBRARY_PATH se a versão UCX em MOFED vs. que em HPC-X é diferente.

## <a name="openmpi"></a>OpenMPI

Instale o UCX como descrito acima. O HCOLL faz parte do [kit de ferramentas de software HPC-X](https://www.mellanox.com/products/hpc-x-toolkit) e não requer uma instalação especial.

O OpenMPI pode ser instalado a partir dos pacotes disponíveis no repo.

```bash
sudo yum install –y openmpi
```

Recomendamos a construção de uma versão mais recente e estável do OpenMPI com a UCX.

```bash
OMPI_VERSION="4.0.3"
OMPI_DOWNLOAD_URL=https://download.open-mpi.org/release/open-mpi/v4.0/openmpi-${OMPI_VERSION}.tar.gz
wget --retry-connrefused --tries=3 --waitretry=5 $OMPI_DOWNLOAD_URL
tar -xvf openmpi-${OMPI_VERSION}.tar.gz
cd openmpi-${OMPI_VERSION}
./configure --prefix=${INSTALL_PREFIX}/openmpi-${OMPI_VERSION} --with-ucx=${UCX_PATH} --with-hcoll=${HCOLL_PATH} --enable-mpirun-prefix-by-default --with-platform=contrib/platform/mellanox/optimized && make -j$(nproc) && make install
```

Para um melhor desempenho, executar OpenMPI com `ucx` e `hcoll` .

```bash
${INSTALL_PREFIX}/bin/mpirun -np 2 --map-by node --hostfile ~/hostfile -mca pml ucx --mca btl ^vader,tcp,openib -x UCX_NET_DEVICES=mlx5_0:1  -x UCX_IB_PKEY=0x0003  ./osu_latency
```

Verifique a sua chave de partição como mencionado acima.

## <a name="intel-mpi"></a>Intel MPI

Descarregue a sua versão de versão da [Intel MPI](https://software.intel.com/mpi-library/choose-download). Altere a variável ambiente I_MPI_FABRICS dependendo da versão.
- Intel MPI 2019 e 2021: use `I_MPI_FABRICS=shm:ofi` , `I_MPI_OFI_PROVIDER=mlx` . O `mlx` fornecedor utiliza a UCX. Verificou-se que o uso de verbos é instável e menos performante. Veja o [artigo TechCommunity](https://techcommunity.microsoft.com/t5/azure-compute/intelmpi-2019-on-azure-hpc-clusters/ba-p/1403149) para mais detalhes.
- Intel MPI 2018: utilização `I_MPI_FABRICS=shm:ofa`
- Intel MPI 2016: utilização `I_MPI_DAPL_PROVIDER=ofa-v2-ib0`

### <a name="non-sr-iov-vms"></a>VMs não SR-IOV
Para vMs não SR-IOV, um exemplo de descarregamento da [versão de avaliação gratuita](https://registrationcenter.intel.com/en/forms/?productid=1740) de 5.x é o seguinte:
```bash
wget http://registrationcenter-download.intel.com/akdlm/irc_nas/tec/9278/l_mpi_p_5.1.3.223.tgz
```
Para etapas de instalação, consulte o [Guia de Instalação da Biblioteca Intel MPI](https://registrationcenter-download.intel.com/akdlm/irc_nas/1718/INSTALL.html?lang=en&fileExt=.html).
Opcionalmente, pode querer ativar ptrace para processos não-depurados sem raiz (necessário para as versões mais recentes do INTEL MPI).
```bash
echo 0 | sudo tee /proc/sys/kernel/yama/ptrace_scope
```

### <a name="suse-linux"></a>SUSE Linux
Para as versões de imagem SUSE Linux Enterprise Server VM - SLES 12 SP3 para HPC, SLES 12 SP3 para HPC (Premium), SLES 12 SP1 para HPC, SLES 12 SP1 para HPC (Premium), SLES 12 SP4 e SLES 15, os controladores RDMA estão instalados e os pacotes De MPI da Intel estão distribuídos no VM. Instale o Intel MPI executando o seguinte comando:
```bash
sudo rpm -v -i --nodeps /opt/intelMPI/intel_mpi_packages/*.rpm
```

## <a name="mvapich2"></a>MVAPICH2

Construa MVAPICH2.

```bash
wget http://mvapich.cse.ohio-state.edu/download/mvapich/mv2/mvapich2-2.3.tar.gz
tar -xv mvapich2-2.3.tar.gz
cd mvapich2-2.3
./configure --prefix=${INSTALL_PREFIX}
make -j 8 && make install
```

Funcionando MVAPICH2.

```bash
${INSTALL_PREFIX}/bin/mpirun_rsh -np 2 -hostfile ~/hostfile MV2_CPU_MAPPING=48 ./osu_latency
```

## <a name="platform-mpi"></a>Plataforma MPI

Instale pacotes necessários para a Plataforma MPI Community Edition.

```bash
sudo yum install libstdc++.i686
sudo yum install glibc.i686
Download platform MPI at https://www.ibm.com/developerworks/downloads/im/mpi/index.html 
sudo ./platform_mpi-09.01.04.03r-ce.bin
```

Siga o processo de instalação.

Os seguintes comandos são exemplos de execução de pinguepong MPI e todos sãoduce usando a plataforma MPI em VMs HBv3 usando imagens CentOS-HPC 7.6, 7.8 e 8.1 VM.

```bash
/opt/ibm/platform_mpi/bin/mpirun -hostlist 10.0.0.8:1,10.0.0.9:1 -np 2 -e MPI_IB_PKEY=0x800a  -ibv  /home/jijos/mpi-benchmarks/IMB-MPI1 pingpong
/opt/ibm/platform_mpi/bin/mpirun -hostlist 10.0.0.8:120,10.0.0.9:120 -np 240 -e MPI_IB_PKEY=0x800a  -ibv  /home/jijos/mpi-benchmarks/IMB-MPI1 allreduce -npmin 240
```


## <a name="mpich"></a>MPICH

Instale o UCX como descrito acima. Construa MPICH.

```bash
wget https://www.mpich.org/static/downloads/3.3/mpich-3.3.tar.gz
tar -xvf mpich-3.3.tar.gz
cd mpich-3.3
./configure --with-ucx=${UCX_PATH} --prefix=${INSTALL_PREFIX} --with-device=ch4:ucx
make -j 8 && make install
```

A executar mpich.

```bash
${INSTALL_PREFIX}/bin/mpiexec -n 2 -hostfile ~/hostfile -env UCX_IB_PKEY=0x0003 -bind-to hwthread ./osu_latency
```

Verifique a sua chave de partição como mencionado acima.

## <a name="osu-mpi-benchmarks"></a>OSU MPI Benchmarks

Baixe [os ÍNDICES DE REFERÊNCIA DA OSU](http://mvapich.cse.ohio-state.edu/benchmarks/) e desatar.

```bash
wget http://mvapich.cse.ohio-state.edu/download/mvapich/osu-micro-benchmarks-5.5.tar.gz
tar –xvf osu-micro-benchmarks-5.5.tar.gz
cd osu-micro-benchmarks-5.5
```

Construa benchmarks utilizando uma biblioteca MPI específica:

```bash
CC=<mpi-install-path/bin/mpicc>CXX=<mpi-install-path/bin/mpicxx> ./configure 
make
```

As referências MPI estão em `mpi/` pasta.


## <a name="discover-partition-keys"></a>Descubra chaves de partição

Descubra as teclas de partição (teclas p) para comunicar com outros VMs dentro do mesmo inquilino (Conjunto de Disponibilidade ou Conjunto de Balanças de Máquinas Virtuais).

```bash
/sys/class/infiniband/mlx5_0/ports/1/pkeys/0
/sys/class/infiniband/mlx5_0/ports/1/pkeys/1
```

A maior das duas é a chave do inquilino que deve ser usada com MPI. Exemplo: Se forem as teclas p, 0x800b deve ser utilizada com MPI.

```bash
cat /sys/class/infiniband/mlx5_0/ports/1/pkeys/0
0x800b
cat /sys/class/infiniband/mlx5_0/ports/1/pkeys/1
0x7fff
```

Utilize a partição que não seja a chave de partição padrão (0x7fff). A UCX exige que o MSB da chave p seja limpo. Por exemplo, coloque UCX_IB_PKEY como 0x000b para 0x800b.

Note também que enquanto o inquilino (Conjunto de Disponibilidade ou Conjunto de Escala de Máquina Virtual) existir, os PKEYs permanecem os mesmos. Isto é verdade mesmo quando os nós são adicionados/eliminados. Os novos inquilinos têm pkeys diferentes.


## <a name="set-up-user-limits-for-mpi"></a>Configurar limites de utilizador para MPI

Configurar limites de utilizador para MPI.

```bash
cat << EOF | sudo tee -a /etc/security/limits.conf
*               hard    memlock         unlimited
*               soft    memlock         unlimited
*               hard    nofile          65535
*               soft    nofile          65535
EOF
```

## <a name="set-up-ssh-keys-for-mpi"></a>Configurar chaves SSH para MPI

Configurar as teclas SSH para os tipos de MPI que o exijam.

```bash
ssh-keygen -f /home/$USER/.ssh/id_rsa -t rsa -N ''
cat << EOF > /home/$USER/.ssh/config
Host *
    StrictHostKeyChecking no
EOF
cat /home/$USER/.ssh/id_rsa.pub >> /home/$USER/.ssh/authorized_keys
chmod 600 /home/$USER/.ssh/authorized_keys
chmod 644 /home/$USER/.ssh/config
```

A sintaxe acima pressupõe um diretório de casa compartilhado, senão .ssh direy deve ser copiado para cada nó.

## <a name="next-steps"></a>Passos seguintes

- Conheça os VMs da [série H](../../sizes-hpc.md) e [da série N](../../sizes-gpu.md) [infiniband](../../sizes-hpc.md#rdma-capable-instances)
- Reveja a visão geral da [série HBv3](hbv3-series-overview.md) e [a visão geral da série HC](hc-series-overview.md).
- Leia sobre os últimos anúncios, exemplos de carga de trabalho do HPC e resultados de desempenho nos [Blogs comunitários Azure Compute Tech.](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute)
- Para uma visão arquitetónica de nível mais elevado da execução das cargas de trabalho do HPC, consulte [a High Performance Computing (HPC) em Azure](/azure/architecture/topics/high-performance-computing/).
