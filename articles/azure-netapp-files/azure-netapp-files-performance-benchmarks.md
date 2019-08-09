---
title: Resultados do teste de benchmark de desempenho para Azure NetApp Files | Microsoft Docs
description: Descreve os resultados de testes de benchmark de desempenho para Azure NetApp Files no nível de volume.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/07/2019
ms.author: b-juche
ms.openlocfilehash: 1d6b43110046f26d8c8070b19587366588eee7b6
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/09/2019
ms.locfileid: "68881741"
---
# <a name="performance-benchmark-test-results-for-azure-netapp-files"></a>Resultados do teste de benchmark de desempenho para Azure NetApp Files

Este artigo descreve os resultados de testes de benchmark de desempenho para Azure NetApp Files no nível de volume. 

## <a name="sample-application-used-for-the-tests"></a>Aplicativo de exemplo usado para os testes

Os testes de desempenho foram executados com um aplicativo de exemplo usando Azure NetApp Files. O aplicativo tem as seguintes características: 

* Um aplicativo baseado em Linux criado para a nuvem
* Pode escalar linearmente com VMs (máquinas virtuais) adicionadas para aumentar a capacidade de computação conforme necessário
* Requer acessibilidade rápida do data Lake
* Tem padrões de e/s que às vezes são aleatórios e, às vezes, sequenciais 
    * Um padrão aleatório requer baixa latência para grandes quantidades de e/s. 
    * Um padrão sequencial requer grandes quantidades de largura de banda. 

## <a name="about-the-workload-generator"></a>Sobre o gerador de carga de trabalho

Os resultados são provenientes de arquivos de Resumo de Vdbench. [Vdbench](https://www.oracle.com/technetwork/server-storage/vdbench-downloads-1901681.html) é um utilitário de linha de comando que gera cargas de trabalho de e/s de disco para validar o desempenho do armazenamento. A configuração de cliente-servidor usada é escalonável.  Ele inclui um único mestre misto/cliente e 14 VMs de cliente dedicadas.

## <a name="about-the-tests"></a>Sobre os testes

Os testes foram projetados para identificar os limites que o aplicativo de exemplo pode ter e o tempo de resposta que se curva até os limites.  

Os seguintes testes foram executados: 

* 100% 8-leitura aleatória KiB
* 100% 8-gravação aleatória KiB
* 100% 64-leitura sequencial KiB
* 100% 64-gravação sequencial KiB
* 50% 64-KiB leitura sequencial, 50% 64-KiB de gravação sequencial
* 50% 8-leitura aleatória KiB, 50% 8-gravação aleatória KiB

## <a name="bandwidth"></a>Largura de banda

O Azure NetApp Files oferece vários [níveis de serviço](azure-netapp-files-service-levels.md). Cada nível de serviço oferece uma quantidade diferente de largura de banda por TiB de capacidade provisionada (cota de volume). O limite de largura de banda para um volume é provisionado com base na combinação do nível de serviço e da cota de volume. O limite de largura de banda é apenas um fator para determinar a quantidade real de taxa de transferência que será realizada.  

Atualmente, 4.500 MiB é a taxa de transferência mais alta que foi obtida por uma carga de trabalho em relação a um único volume em teste.  Com o nível de serviço Premium, uma cota de volume de 70,31 TiB irá provisionar largura de banda suficiente para obter essa taxa de transferência de acordo com o cálculo abaixo: 

![Fórmula de largura de banda](../media/azure-netapp-files/azure-netapp-files-bandwidth-formula.png)

![Nível de cota e de serviço](../media/azure-netapp-files/azure-netapp-files-quota-service-level.png)

## <a name="throughput-intensive-workloads"></a>Cargas de trabalho com uso intensivo de produtividade

O teste de taxa de transferência usou Vdbench e uma combinação de VMs de armazenamento 12xD32s v3. O volume de exemplo no teste obteve os seguintes números de taxa de transferência:

![Teste de taxa de transferência](../media/azure-netapp-files/azure-netapp-files-throughput-test.png)

## <a name="io-intensive-workloads"></a>Cargas de trabalho com uso intensivo de e/s

O teste de e/s usou Vdbench e uma combinação de VMs de armazenamento 12xD32s v3. O volume de exemplo no teste obteve os seguintes números de e/s:

![Teste de e/s](../media/azure-netapp-files/azure-netapp-files-io-test.png)

## <a name="latency"></a>Latência

A distância entre as VMs de teste e o volume de Azure NetApp Files tem um impacto no desempenho de e/s.  O gráfico a seguir compara as curvas IOPS versus resposta de latência para dois conjuntos diferentes de VMs.  Um conjunto de VMs é próximo de Azure NetApp Files e o outro conjunto está mais distante.  A maior latência para o conjunto adicional de VMs tem um impacto na quantidade de IOPS obtida em um determinado nível de paralelismo.  Independentemente disso, as leituras em um volume podem exceder 300.000 IOPS, conforme ilustrado abaixo: 

![Estudo de latência](../media/azure-netapp-files/azure-netapp-files-latency-study.png)

## <a name="summary"></a>Resumo

Cargas de trabalho sensíveis à latência (bancos de dados) podem ter um tempo de resposta de um milissegundo. O desempenho transacional pode ter mais de 300 mil IOPS para um único volume.

Aplicativos sensíveis à taxa de transferência (para streaming e geração de imagens) podem ter 4,5 taxa de transferência de GiB/s.

## <a name="example-scripts"></a>Scripts de exemplo

Os scripts de exemplo a seguir são apenas para fins de demonstração.  Eles não devem ser usados para fins de produção.  

    #
    #This script makes the following assumptions about the environment
    #VM Naming Convention:
    #   VM naming convention: vdbench-vnet1-1 .. vdbench-vnet1-x
    #
    #VM Count:
    #   The script is written for 24 VM's, 
    #   If you wish to test with a different number of vm's replace {1..24} with {1..Some Number}
    #
    #Volume mount point:
    #    The volumes in this script are mounted at the following mount points on all virtual machines
    #    /mnt/vdb1 ... /mnt/vdb6
    #   
    #Virtual machines must have
    #   make sure that vdbench is present on all vms in the environment and that java has been installed on each vm as well as nfs-utils
    #
    #The following tunables were configured on all virtual machines in the environment
    #    No special tunables were used to extract the results identified in this paper.
    #    Even rsize and wsize were left at the default (64K)
    #
    #Special Notes (thread counts) 
    #   You can hone in on the amount of I/O you hope to achieve in your testing using littles law,
    #   Run each test with a thread count of 1 to determnine the best possible latency.
    #   Thread count equals latency in seconds * desired I/O rate.  
    #   If you find a minimum latency of 1.14ms for example, divide that by 1000 to convert 1.3ms to .0013 seconds.
    #   
    #   If your goal from a single machine is for example 3,500 IOPS, a thread count of 4.0 is required.
    
    #   thread count 4.0 == .0013s * 5,000ops
    #
    #    
    #Special Notes: Increased window size:
    #   As round-trip time increases; corresponding increases are required in TCP Window Size – think Littles law as shown above. 
    #   Check the values of the following and adjust as needed: net.core.rmem_max, net.core.rmem_default, net.ipv4.tcp_rmem
    #
    #Run the utility like this:
    #    vdbench -f vnet1-SeqMix-workload
    #
    #When the utility is finished its run, grep 'avg' from the summary file to see overall run performance.
     
    
    
    #LUN CONFIG FILES
    for vnet in 1; do
        echo "sd=default,size=693g" > vnet${vnet}-luns-nfs
        for vm in {1..24}; do
            for vol in {1..6}; do
                echo "sd=sd-${vm}-${vol},host=vm${vm},lun=/mnt/vdb${vol}/file-${vm}-${vol},openflags=o_direct" >> vnet${vnet}-luns-nfs
            done
        done
    done
    
    
    #HOST CONFIG FILES
    for vnet in 1; do
        echo "hd=default,jvms=1,shell=ssh" > vnet${vnet}-hosts-nfs
        for vm in {1..24}; do
            echo "hd=vm${vm},system=vdbench-vnet${vnet}-${vm},user=root" >> vnet${vnet}-hosts-nfs
        done
    done
    
    #VDBENCH WORK SCRIPTS
    for vnet in 1; do
        for pattern in FillWrite SeqRead SeqWrite RndRead RndWrite SeqMix RndMix; do
            echo "include=vnet${vnet}-hosts-nfs" > vnet${vnet}-${pattern}-workload
            echo "include=vnet${vnet}-luns-nfs" >> vnet${vnet}-${pattern}-workload
        done 
        #Fill Write File
        echo "wd=FillWrite,sd=sd*,rdpct=0,seekpct=eof,xfersize=64k" >> vnet${vnet}-FillWrite-workload
        echo "* Run Defaults" >> vnet${vnet}-FillWrite-workload
        echo 'rd=Initialize-LUNs-Full,wd=FillWrite,sd=("sd*"),iorate=max,interval=1,forthreads=1,elapsed=259200' >> vnet${vnet}-FillWrite-workload
    
        #The actual workload files - feel free to add more or use less.
        #   The thread counts shown below were used to generate the performance collateral, 
        #   feel free to change to suite your needs.
    
        echo "wd=SeqWrite,sd=sd*,rdpct=0,seekpct=0,xfersize=64k" >> vnet${vnet}-SeqWrite-workload
        echo "wd=SeqRead,sd=sd*,rdpct=100,seekpct=0,xfersize=64k" >> vnet${vnet}-SeqRead-workload
        echo "wd=RndRead,sd=sd*,rdpct=100,seekpct=100,xfersize=8k" >> vnet${vnet}-RndRead-workload
        echo "wd=RndWrite,sd=sd*,rdpct=0,seekpct=100,xfersize=8k" >> vnet${vnet}-RndWrite-workload
        echo "wd=SeqMix,sd=sd*,rdpct=50,seekpct=0,xfersize=64k" >> vnet${vnet}-SeqMix-workload
        echo "wd=RndMix,sd=sd*,rdpct=50,seekpct=100,xfersize=8k" >> vnet${vnet}-RndMix-workload
        for pattern in SeqRead SeqWrite RndRead RndWrite SeqMix RndMix; do
            echo "* Run Defaults" >> vnet${vnet}-${pattern}-workload
            #echo "rd=default,curve=(50,100),iorate=curve,warmup=60,elapsed=600,iorate=curve" >> vnet${vnet}-${pattern}-workload
            echo "rd=default,warmup=60,elapsed=600,iorate=max" >> vnet${vnet}-${pattern}-workload
            echo "* Run Definitions" >> vnet${vnet}-${pattern}-workload
        done
        echo 'rd=RndRead,wd=RndRead,sd=("sd*"),threads=25' >> vnet${vnet}-RndRead-workload
        echo 'rd=RndWrite,wd=RndWrite,sd=("sd*"),threads=20' >> vnet${vnet}-RndWrite-workload
        echo 'rd=SeqRead-4,wd=SeqRead,sd=("sd*"),threads=20' >> vnet${vnet}-SeqRead-workload
        echo 'rd=SeqWrite-26,wd=SeqWrite,sd=("sd*"),threads=26' >> vnet${vnet}-SeqWrite-workload
        echo 'rd=SeqWrite-32,wd=SeqWrite,sd=("sd*"),threads=32' >> vnet${vnet}-SeqWrite-workload
        echo 'rd=SeqWrite-40,wd=SeqWrite,sd=("sd*"),threads=40' >> vnet${vnet}-SeqWrite-workload
        echo 'rd=RndMix,wd=RndMix,sd=("sd*"),threads=25' >> vnet${vnet}-RndMix-workload
        echo 'rd=SeqMix-6,wd=SeqMix,sd=("sd*"),threads=6' >> vnet${vnet}-SeqMix-workload
        echo 'rd=SeqMix-7,wd=SeqMix,sd=("sd*"),threads=7' >> vnet${vnet}-SeqMix-workload
    done
