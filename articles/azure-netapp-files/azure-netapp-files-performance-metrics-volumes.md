---
title: Parâmetro de comparação de teste de desempenho do volume e de métricas com ficheiros do Azure NetApp | Documentos da Microsoft
description: Fornece recomendações de desempenho do volume e de métricas com ficheiros do Azure NetApp de testes de benchmark.
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
ms.date: 06/26/2019
ms.author: b-juche
ms.openlocfilehash: 12ae9e313655924f11799152b5e58b77776c135c
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/29/2019
ms.locfileid: "67478813"
---
# <a name="benchmark-testing-for-volume-performance-and-metrics-using-azure-netapp-files"></a>Teste de referência do desempenho e das métricas do volume com o Azure NetApp Files

Este artigo fornece recomendações de desempenho do volume e de métricas com ficheiros do Azure NetApp de testes de benchmark.

## <a name="overview"></a>Descrição geral

Para compreender as características de desempenho de um volume de ficheiros do Azure NetApp, pode usar a ferramenta de código-fonte aberto [FIO](https://github.com/axboe/fio) para executar uma série de testes de desempenho para simular uma variedade de cargas de trabalho. FIO pode ser instalado em ambos os Linux e Windows com base em sistemas operativos.  É uma excelente ferramenta para obter um instantâneo rápido de IOPS e débito para um volume.

### <a name="vm-instance-sizing"></a>Dimensionamento de instância VM

Para obter melhores resultados, certifique-se de que está a utilizar uma instância de máquina virtual (VM) que está em um tamanho apropriado para realizar os testes. Os exemplos seguintes utilizam uma instância de Standard_D32s_v3. Para obter mais informações sobre tamanhos de instâncias VM, consulte [máquinas de virtuais de tamanhos para Windows no Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sizes?toc=%2fazure%2fvirtual-network%2ftoc.json) para as VMs com base no Windows, e [tamanhos de máquinas de virtuais do Linux no Azure](https://docs.microsoft.com/azure/virtual-machines/linux/sizes?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) para VMs baseadas em Linux.

### <a name="azure-netapp-files-volume-sizing"></a>Dimensionamento de volume de ficheiros NetApp do Azure

Certifique-se de que escolha o tamanho de quota de serviço correto nível e o volume, para o nível de desempenho esperadas. Ver [níveis de serviço para os ficheiros do Azure NetApp](azure-netapp-files-service-levels.md) para obter mais informações.

### <a name="virtual-network-vnet-recommendations"></a>Recomendações de rede virtual (VNet)

Deve executar o benchmark testes na mesma VNet como ficheiros do Azure NetApp. O exemplo a seguir mostra a recomendação:

![Recomendações de VNet](../media/azure-netapp-files/azure-netapp-files-benchmark-testing-vnet.png)

## <a name="installation-of-fio"></a>Instalação do FIO

FIO está disponível em formato binário para Linux e Windows. Siga a secção de pacotes de binários [FIO](https://github.com/axboe/fio) instalar para a plataforma à sua escolha.

## <a name="fio-examples-for-iops"></a>Exemplos FIO de IOPS 

Os exemplos FIO nesta secção utilizam a seguinte configuração:
* Tamanho da instância VM: D32s_v3
* Nível de serviço do conjunto de capacidade e o tamanho: Premium / 50 TiB
* Tamanho de quota do volume: 48 TiB

Os exemplos seguintes mostram o FIO aleatório leituras e escritas.

### <a name="fio-8k-block-size-100-random-reads"></a>FIO: 8k bloquear leituras aleatórias de 100% de tamanho

`fio --name=8krandomreads --rw=randread --direct=1 --ioengine=libaio --bs=8k --numjobs=4 --iodepth=128 --size=4G --runtime=600 --group_reporting`

### <a name="output-68k-read-iops-displayed"></a>Saída: 68k apresentada de IOPS de leitura

`Starting 4 processes`  
`Jobs: 4 (f=4): [r(4)][84.4%][r=537MiB/s,w=0KiB/s][r=68.8k,w=0 IOPS][eta 00m:05s]`

### <a name="fio-8k-block-size-100-random-writes"></a>FIO: 8k Bloquear gravações aleatórias de 100% de tamanho

`fio --name=8krandomwrites --rw=randwrite --direct=1 --ioengine=libaio --bs=8k --numjobs=4 --iodepth=128  --size=4G --runtime=600 --group_reporting`

### <a name="output-73k-write-iops-displayed"></a>Saída: apresentado de IOPS de escrita de 73k

`Starting 4 processes`  
`Jobs: 4 (f=4): [w(4)][26.7%][r=0KiB/s,w=571MiB/s][r=0,w=73.0k IOPS][eta 00m:22s]`

## <a name="fio-examples-for-bandwidth"></a>Exemplos FIO de largura de banda

Os exemplos nesta apresentação de secção FIO sequencial lê e escreve.

### <a name="fio-64k-block-size-100-sequential-reads"></a>FIO: bloquear a 64K leituras sequenciais de 100% de tamanho

`fio --name=64kseqreads --rw=read --direct=1 --ioengine=libaio --bs=64k --numjobs=4 --iodepth=128  --size=4G --runtime=600 --group_reporting`

### <a name="output-118-gbits-throughput-displayed"></a>Saída: 11.8 débito de Gbit/s apresentado

`Starting 4 processes`  
`Jobs: 4 (f=4): [R(4)][40.0%][r=1313MiB/s,w=0KiB/s][r=21.0k,w=0 IOPS][eta 00m:09s]`

### <a name="fio-64k-block-size-100-sequential-writes"></a>FIO: bloquear a 64K escritas sequenciais de 100% de tamanho

`fio --name=64kseqwrites --rw=write --direct=1 --ioengine=libaio --bs=64k --numjobs=4 --iodepth=128  --size=4G --runtime=600 --group_reporting`

### <a name="output-122-gbits-throughput-displayed"></a>Saída: 12.2 débito de Gbit/s apresentado

`Starting 4 processes`  
`Jobs: 4 (f=4): [W(4)][85.7%][r=0KiB/s,w=1356MiB/s][r=0,w=21.7k IOPS][eta 00m:02s]`

## <a name="volume-metrics"></a>Métricas de volume

Dados de desempenho de NetApp ficheiros do Azure estão disponíveis através de contadores do Monitor do Azure. Os contadores estão disponíveis através do portal do Azure e de pedidos de obtenção de API de REST. 

Pode ver os dados históricos para as seguintes informações:
* Média de latência de leitura 
* Latência média de escrita 
* IOPS de leitura (média)
* Escrever IOPS (média)
* Tamanho lógico do volume (média)
* Tamanho do instantâneo de volume (média)

### <a name="using-azure-monitor"></a>Utilizar o Azure Monitor 

Pode acessar contadores de ficheiros do Azure NetApp numa base por volume da página de métricas, como mostrado abaixo:

![Métricas de Monitor do Azure](../media/azure-netapp-files/azure-netapp-files-benchmark-monitor-metrics.png)

Também pode criar um dashboard no Azure Monitor para ficheiros de NetApp do Azure vai para a página de métricas, a filtragem para NetApp, e especificando os contadores de volume de interesse: 

![Dashboard Azure Monitor](../media/azure-netapp-files/azure-netapp-files-benchmark-monitor-dashboard.png)

### <a name="azure-monitor-api-access"></a>Acesso de API do Monitor do Azure

Pode acessar contadores de ficheiros do Azure NetApp utilizando chamadas de REST API. Consulte [suportado métricas com o Azure Monitor: Microsoft.NetApp/netAppAccounts/capacityPools/Volumes](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported#microsoftnetappnetappaccountscapacitypoolsvolumes) para contadores para conjuntos de capacidade e volumes.

O exemplo seguinte mostra um URL de introdução para a visualização de tamanho do volume lógico:

`#get ANF volume usage`  
`curl -X GET -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts/ANFACCOUNTGOESHERE/capacityPools/ANFPOOLGOESHERE/Volumes/ANFVOLUMEGOESHERE/providers/microsoft.insights/metrics?api-version=2018-01-01&metricnames=VolumeLogicalSize`


## <a name="next-steps"></a>Passos Seguintes

- [Níveis de serviço para os ficheiros do Azure NetApp](azure-netapp-files-service-levels.md)
- [Parâmetros de comparação de desempenho para os ficheiros do Azure NetApp](azure-netapp-files-performance-benchmarks.md)