---
title: Configurar interface de passagem de mensagens para HPC - Azure Virtual Machines Microsoft Docs
description: Saiba como configurar o MPI para o HPC em Azure.
services: virtual-machines
documentationcenter: ''
author: vermagit
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 05/15/2019
ms.author: amverma
ms.openlocfilehash: 469e926932ffa11ef9f2a262b78a587ba435549e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "77023995"
---
# <a name="set-up-message-passing-interface-for-hpc"></a>Configurar interface de passagem de mensagens para HPC

As cargas de trabalho da Interface de Passagem de Mensagens (MPI) são uma parte significativa das cargas de trabalho tradicionais do HPC. O SR-IOV permitiu que os tamanhos VM em Azure permitissem que quase qualquer sabor de MPI fosse utilizado. 

A execução de trabalhos de MPI em VMs requer a criação de chaves de partição (p-keys) através de um inquilino. Siga os passos na secção [de chaves de partição Discover](#discover-partition-keys) para obter mais detalhes sobre a determinação dos valores da chave p.

## <a name="ucx"></a>UCX

[A UCX](https://github.com/openucx/ucx) oferece o melhor desempenho no IB e trabalha com MPICH e OpenMPI.

```bash
wget https://github.com/openucx/ucx/releases/download/v1.4.0/ucx-1.4.0.tar.gz
tar -xvf ucx-1.4.0.tar.gz
cd ucx-1.4.0
./configure --prefix=<ucx-install-path>
make -j 8 && make install
```

## <a name="openmpi"></a>OpenMPI

Instale o UCX como descrito anteriormente.

```bash
sudo yum install –y openmpi
```

Construa o OpenMPI.

```bash
wget https://download.open-mpi.org/release/open-mpi/v4.0/openmpi-4.0.0.tar.gz
tar -xvf openmpi-4.0.0.tar.gz
cd openmpi-4.0.0
./configure --with-ucx=<ucx-install-path> --prefix=<ompi-install-path>
make -j 8 && make install
```

Executar OpenMPI.

```bash
<ompi-install-path>/bin/mpirun -np 2 --map-by node --hostfile ~/hostfile -mca pml ucx --mca btl ^vader,tcp,openib -x UCX_NET_DEVICES=mlx5_0:1  -x UCX_IB_PKEY=0x0003  ./osu_latency
```

Verifique a sua chave de partição como mencionado acima.

## <a name="mpich"></a>MPICH

Instale o UCX como descrito anteriormente.

Construa MPICH.

```bash
wget https://www.mpich.org/static/downloads/3.3/mpich-3.3.tar.gz
tar -xvf mpich-3.3.tar.gz
cd mpich-3.3
./configure --with-ucx=<ucx-install-path> --prefix=<mpich-install-path> --with-device=ch4:ucx
make -j 8 && make install
```

A executar mpich.

```bash
<mpich-install-path>/bin/mpiexec -n 2 -hostfile ~/hostfile -env UCX_IB_PKEY=0x0003 -bind-to hwthread ./osu_latency
```

Verifique a sua chave de partição como mencionado acima.

## <a name="mvapich2"></a>MVAPICH2

Construa MVAPICH2.

```bash
wget http://mvapich.cse.ohio-state.edu/download/mvapich/mv2/mvapich2-2.3.tar.gz
tar -xv mvapich2-2.3.tar.gz
cd mvapich2-2.3
./configure --prefix=<mvapich2-install-path>
make -j 8 && make install
```

Funcionando MVAPICH2.

```bash
<mvapich2-install-path>/bin/mpirun_rsh -np 2 -hostfile ~/hostfile MV2_CPU_MAPPING=48 ./osu_latency
```

## <a name="platform-mpi-community-edition"></a>Edição Comunitária MPI da plataforma

Instale pacotes necessários para MPI da plataforma.

```bash
sudo yum install libstdc++.i686
sudo yum install glibc.i686
Download platform MPI at https://www.ibm.com/developerworks/downloads/im/mpi/index.html 
sudo ./platform_mpi-09.01.04.03r-ce.bin
```

Siga o processo de instalação.

## <a name="intel-mpi"></a>Intel MPI

[Baixar Intel MPI](https://software.intel.com/mpi-library/choose-download).

Altere a variável ambiente I_MPI_FABRICS dependendo da versão. Para Intel MPI 2018, utilize `I_MPI_FABRICS=shm:ofa` e para 2019, utilize `I_MPI_FABRICS=shm:ofi` .

A fixação do processo funciona corretamente para 15, 30 e 60 PPN por padrão.

## <a name="osu-mpi-benchmarks"></a>OSU MPI Benchmarks

[Baixe os ÍNDICES DE REFERÊNCIA DA OSU](http://mvapich.cse.ohio-state.edu/benchmarks/) e desatar.

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

Descubra as teclas de partição (teclas p) para comunicar com outros VMs dentro do mesmo inquilino (Conjunto de Disponibilidade ou Conjunto de Escala VM).

```bash
/sys/class/infiniband/mlx5_0/ports/1/pkeys/0
/sys/class/infiniband/mlx5_0/ports/1/pkeys/1
```

A maior das duas é a chave do inquilino que deve ser usada com MPI. Exemplo: Se forem as teclas p, 0x800b deve ser utilizado com MPI.

```bash
cat /sys/class/infiniband/mlx5_0/ports/1/pkeys/0
0x800b
cat /sys/class/infiniband/mlx5_0/ports/1/pkeys/1
0x7fff
```

Utilize a partição que não seja a chave de partição padrão (0x7fff). A UCX exige que o MSB da chave p seja limpo. Por exemplo, coloque UCX_IB_PKEY como 0x000b para 0x800b.

Note também que enquanto o inquilino (AVSet ou VMSS) existir, os PKEYs permanecem os mesmos. Isto é verdade mesmo quando os nós são adicionados/eliminados. Os novos inquilinos têm pkeys diferentes.


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

## <a name="next-steps"></a>Próximos passos

Saiba mais sobre [o HPC](https://docs.microsoft.com/azure/architecture/topics/high-performance-computing/) em Azure.
