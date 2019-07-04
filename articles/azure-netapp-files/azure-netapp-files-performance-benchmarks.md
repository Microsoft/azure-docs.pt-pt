---
title: Parâmetros de comparação de desempenho para ficheiros de NetApp do Azure | Documentos da Microsoft
description: Descreve os resultados dos testes de benchmark de desempenho para ficheiros de NetApp do Azure ao nível do volume.
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
ms.date: 04/22/2019
ms.author: b-juche
ms.openlocfilehash: 14081daf1f45a84bc8ad19bf0239db1281d9e624
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67449508"
---
# <a name="performance-benchmarks-for-azure-netapp-files"></a>Referências de desempenho para o Azure NetApp Files

Este artigo descreve os resultados dos testes de benchmark de desempenho para ficheiros de NetApp do Azure ao nível do volume. 

## <a name="sample-application-used-for-the-tests"></a>Aplicação de exemplo utilizada para os testes

Testes de desempenho foram executados com uma aplicação de exemplo com ficheiros de NetApp do Azure. O aplicativo tem as seguintes características: 

* Um aplicativo baseado em Linux, criado para a cloud
* Pode aumentam linearmente com adicionadas máquinas virtuais (VMs) para aumentar a capacidade de computação, conforme necessário
* Requer a acessibilidade rápida do data lake
* Tem padrões de e/s que às vezes são aleatórios e, às vezes, sequenciais 
    * Um padrão aleatório necessita de baixa latência para grandes quantidades de e/s. 
    * Um padrão sequencial requer grandes quantidades de largura de banda. 

## <a name="about-the-workload-generator"></a>Sobre o gerador de carga de trabalho

Os resultados provenientes de arquivos de resumos de Vdbench. [Vdbench](https://www.oracle.com/technetwork/server-storage/vdbench-downloads-1901681.html) é um utilitário de linha de comandos que gera cargas de trabalho de e/s de disco para validar o desempenho de armazenamento. A configuração de cliente-servidor utilizada é escalável.  Ele inclui um único mestre/cliente misto e o cliente dedicado 14 VMs.

## <a name="about-the-tests"></a>Sobre os testes

Os testes foram concebidos para identificar os limites que possa ter a aplicação de exemplo e o tempo de resposta que curvas até aos limites.  

Foram executados os seguintes testes: 

* 100% 8-KiB aleatório de leitura
* Escrita aleatória de 8 KiB 100%
* Leia a 100% 64-KiB sequencial
* Escrita sequencial de 64-KiB 100%
* 50% 64-KiB sequencial de leitura, escrita sequencial de 64-KiB de 50%
* 8-KiB de 50% aleatório de leitura, escrita aleatória de 8 KiB de 50%

## <a name="bandwidth"></a>Largura de banda

Os ficheiros NetApp do Azure oferece várias [níveis de serviço](azure-netapp-files-service-levels.md). Cada nível de serviço oferece uma quantidade diferente de largura de banda por TiB de capacidade aprovisionada (quota de volume). O limite de largura de banda para um volume é aprovisionado com base na combinação de nível de serviço e a quota de volume. O limite de largura de banda é apenas um fator usado para determinar a quantidade real de débito que ocorrerá.  

Atualmente, o 4,500 MiB é o débito mais elevado que obteve por uma carga de trabalho em relação a um único volume no teste.  Com o nível de serviço Premium, uma quota de volume de 70.31 TiB irá aprovisionar a largura de banda suficiente para realizar essa taxa de transferência pelo cálculo abaixo: 

![Fórmula de largura de banda](../media/azure-netapp-files/azure-netapp-files-bandwidth-formula.png)

![Nível de serviço e quota](../media/azure-netapp-files/azure-netapp-files-quota-service-level.png)

## <a name="throughput-intensive-workloads"></a>Cargas de trabalho intensivas de débito

O teste de débito utilizou Vdbench e uma combinação de VMs do armazenamento 12xD32s V3. O volume de exemplo no teste obtido os números de débito seguintes:

![Teste de débito](../media/azure-netapp-files/azure-netapp-files-throughput-test.png)

## <a name="io-intensive-workloads"></a>Cargas de trabalho de e/S intensivas

O teste de e/s utilizado Vdbench e uma combinação de VMs do armazenamento 12xD32s V3. O volume de exemplo no teste obtido os números de e/s seguintes:

![Teste de e/s](../media/azure-netapp-files/azure-netapp-files-io-test.png)

## <a name="latency"></a>Latência

A distância entre o teste de VMs e o volume de ficheiros do Azure NetApp tem um impacto sobre o desempenho de e/s.  A tabela abaixo compara o IOPS versus curvas de resposta de latência para dois conjuntos diferentes de VMs.  Um conjunto de VMs é quase NetApp serviço ficheiros do Azure e o outro conjunto é ainda mais longe.  A latência maior para o conjunto adicional de VMs tem um impacto na quantidade de IOPS obtida num determinado nível de paralelismo.  Seja como for, leituras em relação a um volume podem exceder 300.000 IOPS, conforme ilustrado abaixo: 

![Estudo de latência](../media/azure-netapp-files/azure-netapp-files-latency-study.png)

## <a name="summary"></a>Resumo

Cargas de trabalho sensíveis à latência (bases de dados) podem ter um tempo de resposta de um milissegundo. O desempenho transacional pode ser as mais de 300k IOPS para um único volume.

Aplicações sensíveis a taxa de transferência (para transmissão em fluxo e geração de imagens) podem ter 4.5GiB / débito s.

## <a name="example-scripts"></a>Scripts de exemplo

Os seguintes scripts de exemplo destinam-se apenas a fins de demonstração.  Eles não são deve ser utilizada para fins de produção.  

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
