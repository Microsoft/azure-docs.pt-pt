---
title: Testes de benchmark de desempenho recomendados - Ficheiros Azure NetApp
description: Saiba mais sobre recomendações de teste de referência para desempenho de volume e métricas usando ficheiros Azure NetApp.
author: b-juche
ms.author: b-juche
ms.service: azure-netapp-files
ms.workload: storage
ms.topic: conceptual
ms.date: 08/07/2019
ms.openlocfilehash: cf25ef59bc1ea5db61dcfb3c76c0d978cb1f95d0
ms.sourcegitcommit: 50802bffd56155f3b01bfb4ed009b70045131750
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91931841"
---
# <a name="performance-benchmark-test-recommendations-for-azure-netapp-files"></a>Recomendações do teste da referência de desempenho do Azure NetApp Files

Este artigo fornece recomendações de teste de referência para desempenho de volume e métricas usando Ficheiros Azure NetApp.

## <a name="overview"></a>Descrição geral

Para compreender as características de desempenho de um volume de Ficheiros Azure NetApp, pode utilizar a ferramenta de código aberto [FIO](https://github.com/axboe/fio) para executar uma série de referências para simular uma variedade de cargas de trabalho. O FIO pode ser instalado tanto em sistemas operativos Linux como windows.  É uma excelente ferramenta para obter uma imagem rápida de IOPS e produção para um volume.

### <a name="vm-instance-sizing"></a>Tamanho de instância VM

Para obter melhores resultados, certifique-se de que está a utilizar uma caixa de máquina virtual (VM) adequadamente dimensionada para realizar os testes. Os exemplos que se seguem utilizam uma Standard_D32s_v3 instância. Para obter mais informações sobre tamanhos de instâncias VM, consulte [tamanhos para máquinas virtuais Windows em Azure](../virtual-machines/sizes.md?toc=%252fazure%252fvirtual-network%252ftoc.json) para VMs baseados no Windows e [tamanhos para máquinas virtuais Linux em Azure](../virtual-machines/sizes.md?toc=%252fazure%252fvirtual-machines%252flinux%252ftoc.json) para VMs baseados em Linux.

### <a name="azure-netapp-files-volume-sizing"></a>Dimensionamento de volume de ficheiros Azure NetApp

Certifique-se de que escolhe o nível de serviço correto e o tamanho da quota de volume para o nível de desempenho esperado. Consulte [os níveis de serviço dos ficheiros Azure NetApp](azure-netapp-files-service-levels.md) para obter mais informações.

### <a name="virtual-network-vnet-recommendations"></a>Recomendações de rede virtual (VNet)

Deverá realizar os testes de referência no mesmo VNet que os Ficheiros Azure NetApp. O exemplo abaixo demonstra a recomendação:

![Recomendações vNet](../media/azure-netapp-files/azure-netapp-files-benchmark-testing-vnet.png)

## <a name="installation-of-fio"></a>Instalação da FIO

A FIO está disponível em formato binário tanto para o Linux como para o Windows. Siga a secção Pacotes Binários em [FIO](https://github.com/axboe/fio) para instalar para a plataforma à sua escolha.

## <a name="fio-examples-for-iops"></a>Exemplos FIO para IOPS 

Os exemplos FIO nesta secção utilizam a seguinte configuração:
* Tamanho da instância VM: D32s_v3
* Nível e tamanho do serviço de piscina de capacidade: Premium / 50 TiB
* Tamanho da quota de volume: 48 TiB

Os exemplos a seguir mostram que o FIO lê e escreve aleatoriamente.

### <a name="fio-8k-block-size-100-random-reads"></a>FIO: 8k bloco tamanho 100% leituras aleatórias

`fio --name=8krandomreads --rw=randread --direct=1 --ioengine=libaio --bs=8k --numjobs=4 --iodepth=128 --size=4G --runtime=600 --group_reporting`

### <a name="output-68k-read-iops-displayed"></a>Saída: 68k ler IOPS exibido

`Starting 4 processes`  
`Jobs: 4 (f=4): [r(4)][84.4%][r=537MiB/s,w=0KiB/s][r=68.8k,w=0 IOPS][eta 00m:05s]`

### <a name="fio-8k-block-size-100-random-writes"></a>FIO: bloco de 8k tamanho 100% escritos aleatórios

`fio --name=8krandomwrites --rw=randwrite --direct=1 --ioengine=libaio --bs=8k --numjobs=4 --iodepth=128  --size=4G --runtime=600 --group_reporting`

### <a name="output-73k-write-iops-displayed"></a>Saída: 73k escrever IOPS exibido

`Starting 4 processes`  
`Jobs: 4 (f=4): [w(4)][26.7%][r=0KiB/s,w=571MiB/s][r=0,w=73.0k IOPS][eta 00m:22s]`

## <a name="fio-examples-for-bandwidth"></a>Exemplos FIO para largura de banda

Os exemplos desta secção mostram que o FIO sequencial lê e escreve.

### <a name="fio-64k-block-size-100-sequential-reads"></a>FIO: 64k bloco tamanho 100% leituras sequenciais

`fio --name=64kseqreads --rw=read --direct=1 --ioengine=libaio --bs=64k --numjobs=4 --iodepth=128  --size=4G --runtime=600 --group_reporting`

### <a name="output-118-gbits-throughput-displayed"></a>Saída: 11.8 Gbit/s de saída visualizada

`Starting 4 processes`  
`Jobs: 4 (f=4): [R(4)][40.0%][r=1313MiB/s,w=0KiB/s][r=21.0k,w=0 IOPS][eta 00m:09s]`

### <a name="fio-64k-block-size-100-sequential-writes"></a>FIO: bloco de 64k tamanho 100% sequencial escreve

`fio --name=64kseqwrites --rw=write --direct=1 --ioengine=libaio --bs=64k --numjobs=4 --iodepth=128  --size=4G --runtime=600 --group_reporting`

### <a name="output-122-gbits-throughput-displayed"></a>Saída: 12.2 Gbit/s produção exibida

`Starting 4 processes`  
`Jobs: 4 (f=4): [W(4)][85.7%][r=0KiB/s,w=1356MiB/s][r=0,w=21.7k IOPS][eta 00m:02s]`

## <a name="volume-metrics"></a>Métricas de volume

Os dados de desempenho do Azure NetApp Files estão disponíveis através dos balcões do Azure Monitor. Os contadores estão disponíveis através do portal Azure e os pedidos REST API GET. 

Pode ver dados históricos para as seguintes informações:
* Latência média de leitura 
* Latência média de escrita 
* Ler IOPS (média)
* Escrever IOPS (média)
* Tamanho lógico do volume (média)
* Tamanho do instantâneo de volume (média)

### <a name="using-azure-monitor"></a>Utilizar o Azure Monitor 

Pode aceder aos balcões do Azure NetApp Files numa base por volume a partir da página Métricas, como mostrado abaixo:

![Métricas do Monitor Azure](../media/azure-netapp-files/azure-netapp-files-benchmark-monitor-metrics.png)

Também pode criar um dashboard no Azure Monitor para Ficheiros Azure NetApp, indo à página Métricas, filtrando para NetApp e especificando os contadores de volume de interesse: 

![Dashboard do Azure Monitor](../media/azure-netapp-files/azure-netapp-files-benchmark-monitor-dashboard.png)

### <a name="azure-monitor-api-access"></a>Acesso Azure Monitor API

Pode aceder aos balcões do Azure NetApp Files utilizando chamadas REST API. Consulte [métricas suportadas com o Azure Monitor: Microsoft.NetApp/netAppAccounts/capacityPools/Volumes](../azure-monitor/platform/metrics-supported.md#microsoftnetappnetappaccountscapacitypoolsvolumes) para balcões para piscinas e volumes de capacidade.

O exemplo a seguir mostra um URL GET para visualização do tamanho do volume lógico:

`#get ANF volume usage`  
`curl -X GET -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts/ANFACCOUNTGOESHERE/capacityPools/ANFPOOLGOESHERE/Volumes/ANFVOLUMEGOESHERE/providers/microsoft.insights/metrics?api-version=2018-01-01&metricnames=VolumeLogicalSize`


## <a name="next-steps"></a>Passos seguintes

- [Níveis de serviços do Azure NetApp Files](azure-netapp-files-service-levels.md)
- [Testes de referência de desempenho para Linux](performance-benchmarks-linux.md)