---
title: Testes de referência de desempenho recomendados - Ficheiros Azure NetApp
description: Conheça as recomendações de teste de referência para o desempenho do volume e métricas utilizando ficheiros Azure NetApp.
author: b-juche
ms.author: b-juche
ms.service: azure-netapp-files
ms.workload: storage
ms.topic: conceptual
ms.date: 08/07/2019
ms.openlocfilehash: 8f354152c23dd7ad0413f27585d724f8070ca003
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75551527"
---
# <a name="performance-benchmark-test-recommendations-for-azure-netapp-files"></a>Recomendações do teste da referência de desempenho do Azure NetApp Files

Este artigo fornece recomendações de teste de referência para desempenho de volume e métricas usando Ficheiros Azure NetApp.

## <a name="overview"></a>Descrição geral

Para compreender as características de desempenho de um volume de Ficheiros Azure NetApp, pode utilizar a ferramenta de código aberto [FIO](https://github.com/axboe/fio) para executar uma série de referências para simular uma variedade de cargas de trabalho. O FIO pode ser instalado em sistemas operativos baseados no Linux e no Windows.  É uma excelente ferramenta para obter uma imagem rápida tanto do IOPS como da entrada para um volume.

### <a name="vm-instance-sizing"></a>Tamanho da instância VM

Para obter os melhores resultados, certifique-se de que está a utilizar uma instância de máquina virtual (VM) que é adequadamente dimensionada para efetuar os testes. Os exemplos que se seguem utilizam uma Standard_D32s_v3 exemplo. Para obter mais informações sobre tamanhos de instância VM, consulte [Sizes para máquinas virtuais Windows em Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sizes?toc=%2fazure%2fvirtual-network%2ftoc.json) para VMs baseados no Windows e [Tamanhos para máquinas virtuais Linux em Azure](https://docs.microsoft.com/azure/virtual-machines/linux/sizes?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) para VMs baseados em Linux.

### <a name="azure-netapp-files-volume-sizing"></a>Tamanho do volume de ficheiros Azure NetApp

Certifique-se de que escolhe o nível de serviço correto e o tamanho da quota de volume para o nível de desempenho esperado. Consulte [os níveis de serviço dos Ficheiros Azure NetApp](azure-netapp-files-service-levels.md) para obter mais informações.

### <a name="virtual-network-vnet-recommendations"></a>Recomendações de rede virtual (VNet)

Deve realizar os testes de referência no mesmo VNet que o Azure NetApp Files. O exemplo abaixo demonstra a recomendação:

![Recomendações VNet](../media/azure-netapp-files/azure-netapp-files-benchmark-testing-vnet.png)

## <a name="installation-of-fio"></a>Instalação da FIO

O FIO está disponível em formato binário tanto para linux como windows. Siga a secção pacotes binários na [FIO](https://github.com/axboe/fio) para instalar para a plataforma à sua escolha.

## <a name="fio-examples-for-iops"></a>Exemplos fio para IOPS 

Os exemplos fio nesta secção utilizam a seguinte configuração:
* Tamanho da instância VM: D32s_v3
* Nível e tamanho do serviço de piscina de capacidade: Premium / 50 TiB
* Tamanho da quota de volume: 48 TiB

Os seguintes exemplos mostram que o FIO lê e escreve aleatoriamente.

### <a name="fio-8k-block-size-100-random-reads"></a>FIO: 8k bloco tamanho 100% leituras aleatórias

`fio --name=8krandomreads --rw=randread --direct=1 --ioengine=libaio --bs=8k --numjobs=4 --iodepth=128 --size=4G --runtime=600 --group_reporting`

### <a name="output-68k-read-iops-displayed"></a>Saída: 68k ler IOPS exibido

`Starting 4 processes`  
`Jobs: 4 (f=4): [r(4)][84.4%][r=537MiB/s,w=0KiB/s][r=68.8k,w=0 IOPS][eta 00m:05s]`

### <a name="fio-8k-block-size-100-random-writes"></a>FIO: 8k bloco tamanho 100% escritos aleatórios

`fio --name=8krandomwrites --rw=randwrite --direct=1 --ioengine=libaio --bs=8k --numjobs=4 --iodepth=128  --size=4G --runtime=600 --group_reporting`

### <a name="output-73k-write-iops-displayed"></a>Saída: 73k escrever IOPS exibido

`Starting 4 processes`  
`Jobs: 4 (f=4): [w(4)][26.7%][r=0KiB/s,w=571MiB/s][r=0,w=73.0k IOPS][eta 00m:22s]`

## <a name="fio-examples-for-bandwidth"></a>Exemplos FIO para largura de banda

Os exemplos desta secção mostram o fio sequencial que lê e escreve.

### <a name="fio-64k-block-size-100-sequential-reads"></a>FIO: 64k bloco tamanho 100% leituras sequenciais

`fio --name=64kseqreads --rw=read --direct=1 --ioengine=libaio --bs=64k --numjobs=4 --iodepth=128  --size=4G --runtime=600 --group_reporting`

### <a name="output-118-gbits-throughput-displayed"></a>Saída: 11.8 Gbit/s de entrada visualizada

`Starting 4 processes`  
`Jobs: 4 (f=4): [R(4)][40.0%][r=1313MiB/s,w=0KiB/s][r=21.0k,w=0 IOPS][eta 00m:09s]`

### <a name="fio-64k-block-size-100-sequential-writes"></a>FIO: 64k bloco tamanho 100% sequencial escreve

`fio --name=64kseqwrites --rw=write --direct=1 --ioengine=libaio --bs=64k --numjobs=4 --iodepth=128  --size=4G --runtime=600 --group_reporting`

### <a name="output-122-gbits-throughput-displayed"></a>Saída: 12.2 Gbit/s de entrada visualizada

`Starting 4 processes`  
`Jobs: 4 (f=4): [W(4)][85.7%][r=0KiB/s,w=1356MiB/s][r=0,w=21.7k IOPS][eta 00m:02s]`

## <a name="volume-metrics"></a>Métricas de volume

Os dados de desempenho do Azure NetApp Files estão disponíveis através de balcões do Monitor Do Azure. Os balcões estão disponíveis através do portal Azure e dos pedidos REST API GET. 

Pode ver dados históricos para obter as seguintes informações:
* Latência média de leitura 
* Latência média de escrita 
* Ler IOPS (média)
* Escreva IOPS (média)
* Tamanho lógico de volume (média)
* Tamanho do instantâneo de volume (média)

### <a name="using-azure-monitor"></a>Utilizar o Azure Monitor 

Pode aceder aos contadores do Azure NetApp Files numa base por volume a partir da página Métricas, como mostrado abaixo:

![Métricas do Monitor Azure](../media/azure-netapp-files/azure-netapp-files-benchmark-monitor-metrics.png)

Também pode criar um dashboard no Azure Monitor para Ficheiros Azure NetApp, indo para a página Métricas, filtrando para o NetApp e especificando os contadores de volume de interesse: 

![Dashboard Azure Monitor](../media/azure-netapp-files/azure-netapp-files-benchmark-monitor-dashboard.png)

### <a name="azure-monitor-api-access"></a>Acesso à API do Monitor Azure

Pode aceder aos balcões do Azure NetApp Files utilizando chamadas REST API. Consulte [as métricas suportadas com o Monitor Azure: Microsoft.NetApp/netAppAccounts/capacityPools/Volumes](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported#microsoftnetappnetappaccountscapacitypoolsvolumes) para contadores para piscinas e volumes de capacidade.

O exemplo seguinte mostra um URL GET para visualizar o tamanho do volume lógico:

`#get ANF volume usage`  
`curl -X GET -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts/ANFACCOUNTGOESHERE/capacityPools/ANFPOOLGOESHERE/Volumes/ANFVOLUMEGOESHERE/providers/microsoft.insights/metrics?api-version=2018-01-01&metricnames=VolumeLogicalSize`


## <a name="next-steps"></a>Passos seguintes

- [Níveis de serviços do Azure NetApp Files](azure-netapp-files-service-levels.md)
- [Referências de desempenho para o Azure NetApp Files](azure-netapp-files-performance-benchmarks.md)