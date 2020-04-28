---
title: Resultados dos testes de referência de desempenho para ficheiros Azure NetApp / Microsoft Docs
description: Descreve os resultados dos testes de benchmark de desempenho para ficheiros Azure NetApp a nível de volume.
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
ms.sourcegitcommit: fad3aaac5af8c1b3f2ec26f75a8f06e8692c94ed
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/27/2020
ms.locfileid: "68881741"
---
# <a name="performance-benchmark-test-results-for-azure-netapp-files"></a>Resultados do teste da referência de desempenho do Azure NetApp Files

Este artigo descreve os resultados dos testes de benchmark de desempenho para ficheiros Azure NetApp a nível de volume. 

## <a name="sample-application-used-for-the-tests"></a>Aplicação da amostra utilizada para os testes

Os testes de desempenho foram realizados com uma aplicação de amostra utilizando ficheiros Azure NetApp. O pedido tem as seguintes características: 

* Uma aplicação baseada em Linux construída para a nuvem
* Pode escalar linearmente com máquinas virtuais adicionadas (VMs) para aumentar a potência do cálculo conforme necessário
* Requer uma rápida acessibilidade do lago de dados
* Tem padrões de I/O que às vezes são aleatórios e às vezes sequenciais 
    * Um padrão aleatório requer baixa latência para grandes quantidades de E/O. 
    * Um padrão sequencial requer grandes quantidades de largura de banda. 

## <a name="about-the-workload-generator"></a>Sobre o gerador de carga de trabalho

Os resultados vêm de ficheiros resumos da Vdbench. [A Vdbench](https://www.oracle.com/technetwork/server-storage/vdbench-downloads-1901681.html) é um utilitário de linha de comando que gera cargas de trabalho em I/S do disco para validar o desempenho do armazenamento. A configuração cliente-servidor utilizada é escalável.  Inclui um único mestrado/cliente misto e 14 VMs de clientes dedicados.

## <a name="about-the-tests"></a>Sobre os testes

Os testes foram concebidos para identificar os limites que a aplicação da amostra pode ter e o tempo de resposta que se curva até aos limites.  

Foram realizados os seguintes testes: 

* 100% 8-KiB leitura aleatória
* 100% 8-KiB escrita aleatória
* Leitura sequencial 100% 64-KiB
* 100% 64-KiB escrita sequencial
* 50% 64-KiB leitura sequencial, 50% 64-KiB escrita sequencial
* 50% 8-KiB leitura aleatória, 50% 8-KiB escrita aleatória

## <a name="bandwidth"></a>Largura de banda

O Azure NetApp Files oferece [vários níveis](azure-netapp-files-service-levels.md)de serviço. Cada nível de serviço oferece uma quantidade diferente de largura de banda por TiB de capacidade provisionada (quota de volume). O limite de largura de banda para um volume é provisionado com base na combinação do nível de serviço e da quota de volume. O limite de largura de banda é apenas um fator para determinar a quantidade real de produção que será realizada.  

Atualmente, 4.500 MiB é o maior produto que foi alcançado por uma carga de trabalho contra um único volume em testes.  Com o nível de serviço Premium, uma quota de volume de 70.31 TiB fornecerá largura de banda suficiente para realizar este produto de acordo com o cálculo abaixo: 

![Fórmula de largura de banda](../media/azure-netapp-files/azure-netapp-files-bandwidth-formula.png)

![Quota e nível de serviço](../media/azure-netapp-files/azure-netapp-files-quota-service-level.png)

## <a name="throughput-intensive-workloads"></a>Cargas de trabalho intensivas em termos de entrada

O teste de entrada utilizou vdbench e uma combinação de VMs de armazenamento V3 de 12xD32s. O volume da amostra no ensaio obteve os seguintes números de saída:

![Teste de entrada](../media/azure-netapp-files/azure-netapp-files-throughput-test.png)

## <a name="io-intensive-workloads"></a>Cargas de trabalho intensivas em I/O

O teste de I/S utilizou vdbench e uma combinação de VMs de armazenamento V3 de 12xD32s. O volume da amostra no ensaio obteve os seguintes números de E/S:

![Teste de I/O](../media/azure-netapp-files/azure-netapp-files-io-test.png)

## <a name="latency"></a>Latência

A distância entre os VMs de teste e o volume de Ficheiros Azure NetApp tem um impacto no desempenho de I/S.  O gráfico abaixo compara as curvas de resposta iops versus latência para dois conjuntos diferentes de VMs.  Um conjunto de VMs está perto do Azure NetApp Files e o outro conjunto está mais longe.  O aumento da latência para o conjunto de VMs mais aprofundado tem um impacto na quantidade de IOPS alcançada a um determinado nível de paralelismo.  Independentemente disso, as leituras contra um volume podem exceder 300.000 IOPS, conforme ilustrado abaixo: 

![Estudo de latência](../media/azure-netapp-files/azure-netapp-files-latency-study.png)

## <a name="summary"></a>Resumo

As cargas de trabalho sensíveis à latência (bases de dados) podem ter um tempo de resposta de milissegundos. O desempenho transacional pode ser superior a 300 k IOPS para um único volume.

Aplicações sensíveis à entrada (para streaming e imagem) podem ter 4.5GiB/s de entrada.

## <a name="example-scripts"></a>Exemplo de scripts

Os seguintes exemplos são apenas para fins de demonstração.  Não devem ser utilizados para fins produtivos.  

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
