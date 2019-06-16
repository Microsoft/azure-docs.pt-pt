---
title: incluir ficheiro
description: incluir ficheiro
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: include
ms.date: 05/16/2019
ms.author: azcspmt;jonbeck;cynthn
ms.custom: include file
ms.openlocfilehash: 499b5ac77728bc14aadd5a2ef235e28b6ca83516
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67133737"
---
Com otimização de memória oferta de tamanhos VM uma alta taxa de memória de CPU que são ótimos para servidores de base de dados relacionais, caches médias a grandes e análise dentro da memória. Este artigo fornece informações sobre o número de vCPUs, discos de dados e NICs, bem como armazenamento e débito de rede da largura de banda para cada tamanho neste agrupamento. 

* A série Mv2 oferece a contagem de vCPU mais alta (até 208 vCPUs) e a maior capacidade de memória (até 5.7 TiB) de qualquer VM na cloud. É ideal para bases de dados muito grandes ou outras aplicações que tiram partido de contagens altas de vCPU e grandes quantidades de memória.
 
* A série M oferece uma contagem de altas de vCPU (até 128 vCPUs) e uma grande quantidade de memória (até 3,8 TiB). Também é ideal para bases de dados muito grandes ou outras aplicações que tiram partido de contagens altas de vCPU e grandes quantidades de memória.

* Série Dv2, série G e as DSv2/GS são ideais para aplicações que exigem vCPUs mais rápidas, melhor desempenho de armazenamento temporário ou memórias. Proporcionam uma combinação poderosa para inúmeras aplicações empresariais.

* A série DV2, o seguimento da série D original, apresenta uma CPU mais poderosa. A CPU da série Dv2 é cerca de 35% mais rápida do que a CPU da série D. Baseia-se a última geração 2,4 GHz Intel Xeon® E5-2673 v3 (Haswell) de 2,4 GHz ou E5 2673 v4 (Broadwell) de 2.3 GHz processadores, com o Intel Turbo Boost Technology 2.0, podendo chegar aos 3,1 GHz. A série Dv2 tem as mesmas configurações de memória e disco da série D.

* O série Ev3 funcionalidades E5-2673 v4 (Broadwell) de 2.3 GHz processador numa configuração com hyper-threading, fornecendo uma proposta de valor melhor para cargas de trabalho de fins mais gerais e colocar a Ev3 em alinhamento com as VMs para fins gerais da maioria das outras clouds.  Memória foi expandida (de 7 GiB/vCPU de 8 GiB/Vcpus) enquanto os limites de disco e rede foram ajustados numa base por núcleo para alinhar com a mudança para o hyperthreading.  A Ev3 é a siga até os tamanhos VM de elevada da memória das famílias D/Dv2.

* A Computação do Azure oferece tamanhos de máquinas virtuais que são Isolados para um tipo de hardware específico e dedicados a um único cliente.  Estes tamanhos de máquinas virtuais são mais adequados para cargas de trabalho que exigem um elevado grau de isolamento de outros clientes para cargas de trabalho que envolvem elementos como os requisitos de conformidade e regulamentares.  Os clientes também podem optar por subdividir os recursos dessas máquinas de virtuais isoladas usando [suporte do Azure para máquinas virtuais aninhadas](https://azure.microsoft.com/blog/nested-virtualization-in-azure/).  Consulte as tabelas de famílias de máquinas virtuais, abaixo, para as opções de VM isoladas.

## <a name="esv3-series"></a>Série Esv3 

ACU: 160-190 <sup>1</sup>

Armazenamento Premium:  Suportadas

Cache de armazenamento Premium:  Suportadas

As instâncias da série ESv3 baseiam-se no processador 2.3 GHz Intel XEON ® E5-2673 v4 (Broadwell) e podem chegar aos 3.5GHz com o Intel Turbo Boost Technology 2.0 e utilizar o armazenamento premium. As instâncias da série Ev3 são ideais para aplicações empresariais com utilização intensiva da memória.


| Tamanho             | vCPU | Memória: GiB | Armazenamento (SSD) temporário GiB | Discos de dados máximos | Débito máximo de armazenamento temporário e em cache: IOPS / MBps (tamanho da cache em GiB) | Débito máximo de disco não colocado em cache: IOPS / MBps | NICs. Máx. / esperado de largura de banda de rede (Mbps) |
|------------------|--------|-------------|----------------|----------------|-----------------------------------------------------------------------|-------------------------------------------|------------------------------------------------|
| Standard_E2s_v3 | 2      | 16          | 32             | 4              | 4000 / 32 (50)                                                       | 3200 / 48                                | 2 / 1000                                   |
| Standard_E4s_v3&nbsp;<sup>2</sup> | 4      | 32          | 64             | 8              | 8000 / 64 (100)                                                      | 6400 / 96                                | 2 / 2000                                   |
| Standard_E8s_v3&nbsp;<sup>2</sup> | 8      | 64          | 128            | 16             | 16000 / 128 (200)                                                    | 12800 / 192                              | 4 / 4000                                       |
| Standard_E16s_v3&nbsp;<sup>2</sup> | 16     | 128         | 256            | 32             | 32000 / 256 (400)                                                    | 25600 / 384                              | 8 / 8000                                       |
| Standard_E20s_v3                   | 20     | 160         | 320            | 32             | 40000 / 320 (400)                                                    | 32000 / 480                              | 8 / 10000                                       |
| Standard_E32s_v3&nbsp;<sup>2</sup> | 32     | 256         | 512            | 32             | 64000 / 512 (800)                                                    | 51200 / 768                              | 8 / 16000                             |
| Standard_E64s_v3&nbsp;<sup>2</sup> | 64     | 432         | 864            | 32             | 128000 / 1024 (1600)                                                   | 80000 / 1200                             | 8 / 30000                             |
| Standard_E64is_v3&nbsp;<sup>3</sup> | 64     | 432         | 864            | 32             | 128000 / 1024 (1600)                                                   | 80000 / 1200                             | 8 / 30000                             |


<sup>1</sup> as VMS da série Esv3 funcionalidade tecnologia Intel® Hyper-Threading.

<sup>2</sup> restrita tamanhos de núcleos disponíveis.

<sup>3</sup> instância está isolada do hardware dedicado de um único cliente.


## <a name="ev3-series"></a>Série Ev3 

ACU: 160 - 190 <sup>1</sup>

Armazenamento Premium:  Não suportado

Cache de armazenamento Premium:  Não suportado

As instâncias da série Ev3 são baseadas no processador de 2.3 GHz Intel XEON ® E5-2673 v4 (Broadwell) e pode chegar aos 3.5 GHz com o Intel Turbo Boost Technology 2.0. As instâncias da série Ev3 são ideais para aplicações empresariais com utilização intensiva da memória.

O armazenamento de discos de dados são cobrados em separado das máquinas virtuais. Para utilizar os discos de armazenamento premium, utilize os tamanhos ESv3. Os medidores de preços e de faturação dos tamanhos ESv3 são os mesmos do que os da série Ev3. 


| Tamanho            | vCPU | Memória: GiB | Armazenamento (SSD) temporário GiB | Discos de dados máximos | Débito do armazenamento temporário máximo: IOPS / MBps de leitura / MBps de escrita | NICs/Largura de banda da rede máximos |
|-----------------|-----------|-------------|----------------|----------------|----------------------------------------------------------|------------------------------|
| Standard_E2_v3  | 2         | 16          | 50             | 4              | 3000/46/23                                               | 2 / 1000                 |
| Standard_E4_v3  | 4         | 32          | 100            | 8              | 6000/93/46                                               | 2 / 2000                 |
| Standard_E8_v3  | 8         | 64          | 200            | 16             | 12000/187/93                                             | 4 / 4000                     |
| Standard_E16_v3 | 16        | 128         | 400            | 32             | 24000/375/187                                            | 8 / 8000                     |
| Standard_E20_v3 | 20        | 160         | 500            | 32             | 30000/469/234                                            | 8 / 10000                     |
| Standard_E32_v3 | 32        | 256         | 800            | 32             | 48000/750/375                                            | 8 / 16000                 |
| Standard_E64_v3 | 64        | 432         | 1600           | 32             | 96000/1000/500                                           | 8 / 30000           |
| Standard_E64i_v3&nbsp;<sup>2,&nbsp;3</sup> | 64        | 432         | 1600           | 32             | 96000/1000/500                                           | 8 / 30000           |

<sup>1</sup> tecnologia Intel® Hyper-Threading de recursos das VMS da série Ev3.

<sup>2</sup> restrita tamanhos de núcleos disponíveis.

<sup>3</sup> instância está isolada do hardware dedicado de um único cliente.


## <a name="mv2-series"></a>Série Mv2

Armazenamento Premium: Suportadas

Cache de armazenamento Premium: Suportadas

Acelerador de escrita: [Suportado](https://docs.microsoft.com/azure/virtual-machines/windows/how-to-enable-write-accelerator)

O série Mv2 funcionalidades alto débito, baixa latência, diretamente mapeado armazenamento local de NVMe em execução num hyper-thread Intel® Xeon® Platinum 8180 M 2,5 GHz (Skylake) processador com uma frequência de base de todos os núcleos de 2,5 GHz e uma frequência de turbo máx. de 3,8 GHz. Todos os tamanhos de máquinas virtuais de série Mv2 podem utilizar discos persistentes standard e premium. Instâncias da série Mv2 são os tamanhos de VM fornece desempenho computacional incomparável para suportar grandes bancos de dados na memória e cargas de trabalho, com uma alta taxa de memória de CPU, que é ideal para servidores de base de dados relacionais, caches grandes e na memória com otimização de memória análise. 

|Tamanho | vCPU | Memória: GiB | Armazenamento (SSD) temporário GiB | Discos de dados máximos | Débito máximo de armazenamento temporário e em cache: IOPS / MBps (tamanho da cache em GiB) | Débito máximo de disco não colocado em cache: IOPS / MBps | NICs. Máx. / esperado de largura de banda de rede (Mbps) |
|-----------------|------|-------------|----------------|----------------|-----------------------------------------------------------------------|-------------------------------------------|------------------------------|
| Standard_M208ms_v2<sup>1, 2</sup> | 208 | 5700 | 4096 | 64 | 80000 / 800 (7040) | 40000 / 1000 | 8 / 16000 |
| Standard_M208s_v2<sup>1, 2</sup> | 208 | 2850 | 4096 | 64 | 80000 / 800 (7040) | 40000 / 1000 | 8 / 16000 |

As VMS da série Mv2 funcionalidade Intel® Hyper-Threading Technology  

<sup>1</sup> estas VMs grandes exigem um destes SO convidados suportados: Windows Server 2016, Windows Server 2019, SLES 12 SP4, SLES 15.

<sup>2</sup> VMs da série Mv2 são apenas a geração 2. Se estiver a utilizar o Linux, consulte a secção seguinte para saber como localizar e selecionar uma imagem de SUSE Linux.

#### <a name="find-a-suse-image"></a>Encontrar uma imagem SUSE

Para selecionar uma imagem apropriada do SUSE Linux no portal do Azure: 

1. No portal do Azure, selecione **criar um recurso** 
1. Procure "SAP do SUSE" 
1. SLES para imagens de geração 2 SAP estão disponível como qualquer um dos pay as you go ou traga a sua própria subscrição (BYOS). Nos resultados da pesquisa, expanda a categoria de imagem pretendida:

    * SUSE Linux Enterprise Server (SLES) para SAP
    * SUSE Linux Enterprise Server (SLES) para SAP (BYOS)
    
1. Imagens SUSE compatíveis com a série Mv2 são prefixadas com o nome `GEN2:`. As seguintes imagens do SUSE estão disponíveis para as VMs da série Mv2:

    * GEN2: SUSE Linux Enterprise Server (SLES) 12 SP4 para aplicações SAP
    * GEN2: SUSE Linux Enterprise Server (SLES) 15 para aplicações SAP
    * GEN2: SUSE Linux Enterprise Server (SLES) 12 SP4 para aplicações SAP (BYOS)
    * GEN2: SUSE Linux Enterprise Server (SLES) 15 para aplicações SAP (BYOS)

#### <a name="select-a-suse-image-via-azure-cli"></a>Selecionar uma imagem SUSE através da CLI do Azure

Para ver uma lista da imagem SAP do SLES atualmente disponível para VMs de série Mv2, utilize o seguinte procedimento [ `az vm image list` ](https://docs.microsoft.com/cli/azure/vm/image?view=azure-cli-latest#az-vm-image-list) comando:

```azurecli
az vm image list --output table --publisher SUSE --sku gen2 --all
```

O comando produz o atualmente disponíveis VMs de geração 2 disponíveis do SUSE para VMs de série Mv2. 

Exemplo de saída:

```
Offer          Publisher  Sku          Urn                                        Version
-------------  ---------  -----------  -----------------------------------------  ----------
SLES-SAP       SUSE       gen2-12-sp4  SUSE:SLES-SAP:gen2-12-sp4:2019.05.13       2019.05.13
SLES-SAP       SUSE       gen2-15      SUSE:SLES-SAP:gen2-15:2019.05.13           2019.05.13
SLES-SAP-BYOS  SUSE       gen2-12-sp4  SUSE:SLES-SAP-BYOS:gen2-12-sp4:2019.05.13  2019.05.13
SLES-SAP-BYOS  SUSE       gen2-15      SUSE:SLES-SAP-BYOS:gen2-15:2019.05.13      2019.05.13
```

## <a name="m-series"></a>Série M 

ACU: 160-180 <sup>1</sup>

Armazenamento Premium:  Suportadas

Cache de armazenamento Premium:  Suportadas

Acelerador de escrita:  [Suportado](https://docs.microsoft.com/azure/virtual-machines/windows/how-to-enable-write-accelerator)

| Tamanho            | vCPU | Memória: GiB | Armazenamento (SSD) temporário GiB | Discos de dados máximos | Débito máximo de armazenamento temporário e em cache: IOPS / MBps (tamanho da cache em GiB) | Débito máximo de disco não colocado em cache: IOPS / MBps | NICs. Máx. / esperado de largura de banda de rede (Mbps) |
|-----------------|------|-------------|----------------|----------------|-----------------------------------------------------------------------|-------------------------------------------|------------------------------|
| Standard_M8ms&nbsp;<sup>3</sup>    | 8  | 218.75 | 256  | 8  | 10000 / 100 (793)  | 5000  / 125 | 4 / 2000 |
| Standard_M16ms&nbsp;<sup>3</sup>   | 16 | 437.5  | 512  | 16 | 20000 / 200 (1587) | 10000 / 250 | 8 / 4000 |
| Standard_M32ts | 32 | 192    | 1024 | 32 | 40000 / 400 (3174) | 20000 / 500 | 8 / 8000 |
| Standard_M32ls | 32 | 256    | 1024 | 32 | 40000 / 400 (3174) | 20000 / 500 | 8 / 8000 |
| Standard_M32ms&nbsp;<sup>3</sup>   | 32 | 875    | 1024 | 32 | 40000 / 400 (3174) | 20000 / 500 | 8 / 8000 |
| Standard_M64s  | 64 | 1024   | 2048 | 64 | 80000 / 800 (6348)| 40000 / 1000 | 8 / 16000          |
| Standard_M64ls  | 64 | 512    | 2048 | 64 | 80000 / 800 (6348) | 40000 / 1000 | 8 / 16000 |
| Standard_M64ms&nbsp;<sup>3</sup>  | 64   | 1792 | 2048 | 64 | 80000 / 800 (6348)| 40000 / 1000 | 8 / 16000          |
| Standard_M128s&nbsp;<sup>2</sup> | 128  | 2048        | 4096  | 64 | 160000 / 1600 (12696) | 80000 / 2000                            | 8 / 30000          |
| Standard_M128ms&nbsp;<sup>2,&nbsp;3,&nbsp;4</sup> | 128  | 3892  | 4096 | 64 | 160000 / 1600 (12696) | 80000 / 2000                            | 8 / 30000          |
| Standard_M64   | 64  | 1024 | 7168  | 64 | 80000  / 800  (1228) | 40000 / 1000 | 8 / 16000 |
| Standard_M64m  | 64  | 1792 | 7168  | 64 | 80000  / 800  (1228) | 40000 / 1000 | 8 / 16000 |
| Standard_M128&nbsp;<sup>2  | 128 | 2048 | 14336 | 64 | 250000 / 1600 (2456) | 80000 / 2000 | 8 / 32000 |
| Standard_M128m&nbsp;<sup>2 | 128 | 3892 | 14336 | 64 | 250000 / 1600 (2456) | 80000 / 2000 | 8 / 32000 |



<sup>1</sup> funcionalidade das VMS da série M Intel® Hyper-Threading Technology

<sup>2</sup> mais do que 64 Vcpus exigem um destes SO convidados suportados: Windows Server 2016, Ubuntu 16.04 LTS, SLES 12 SP2 e Red Hat Enterprise Linux, CentOS 7.3 ou Oracle Linux 7.3 com LIS 4.2.1.

<sup>3</sup> restrita tamanhos de núcleos disponíveis.

<sup>4</sup> instância está isolada do hardware dedicado de um único cliente.
<br>


## <a name="dsv2-series-11-15"></a>11-15 de série DSv2

ACU: 210 - 250 <sup>1</sup>

Armazenamento Premium:  Suportadas

Cache de armazenamento Premium:  Suportadas

| Tamanho | vCPU | Memória: GiB | Armazenamento (SSD) temporário GiB | Discos de dados máximos | Débito máximo de armazenamento temporário e em cache: IOPS / MBps (tamanho da cache em GiB) | Débito máximo de disco não colocado em cache: IOPS / MBps | NICs. Máx. / esperado de largura de banda de rede (Mbps) |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_DS11_v2&nbsp;<sup>3</sup> |2 |14 |28 |8 |8000 / 64 (72) |6400 / 96 |2 / 1500 |
| Standard_DS12_v2&nbsp;<sup>3</sup> |4 |28 |56 |16 |16000 / 128 (144) |12800 / 192 |4 / 3000 |
| Standard_DS13_v2&nbsp;<sup>3</sup> |8 |56 |112 |32 |32000 / 256 (288) |25600 / 384 |8 / 6000 |
| Standard_DS14_v2&nbsp;<sup>3</sup>|16 |112 |224 |64 |64000 / 512 (576) |51200 / 768 |8 / 12000 |
| Standard_DS15_v2&nbsp;<sup>2</sup> |20 |140 |280 |64 |80000 / 640 (720) |64000 / 960 |8 / 25000&nbsp;<sup>4</sup>

<sup>1</sup> o débito máximo do disco (IOPS ou MBps) possível com uma VM da série DSv2 pode estar limitado pelo número, tamanho e repartição dos discos anexados.  Para obter detalhes, consulte [conceber o elevado desempenho](../articles/virtual-machines/windows/premium-storage-performance.md).  
<sup>2</sup> instância está isolada do hardware dedicado de um único cliente.  
<sup>3</sup> restrita tamanhos de núcleos disponíveis.  
<sup>4</sup> 25000 Mbps com redes aceleradas. 

<br>

## <a name="dv2-series-11-15"></a>11-15 de série Dv2

ACU: 210 - 250

Armazenamento Premium:  Não suportado

Cache de armazenamento Premium:  Não suportado

| Tamanho              | vCPU | Memória: GiB | Armazenamento (SSD) temporário GiB | Débito do armazenamento temporário máximo: IOPS / MBps de leitura / MBps de escrita | Discos de dados máximos / débito: IOPS | NICs. Máx. / esperado de largura de banda de rede (Mbps) |
|-------------------|-----------|-------------|----------------|----------------------------------------------------------|-----------------------------------|------------------------------|
| Standard_D11_v2   | 2         | 14          | 100            | 6000 / 93 / 46                                           | 8 / 8x500                         | 2 / 1500                     |
| Standard_D12_v2   | 4         | 28          | 200            | 12000 / 187 / 93                                         | 16 / 16x500                         | 4 / 3000                     |
| Standard_D13_v2   | 8         | 56          | 400            | 24000 / 375 / 187                                        | 32 / 32x500                       | 8 / 6000                     |
| Standard_D14_v2   | 16        | 112         | 800            | 48000 / 750 / 375                                        | 64 / 64x500                       | 8 / 12000          |
| Standard_D15_v2&nbsp;<sup>1</sup> | 20        | 140         | 1000          | 60000 / 937 / 468                                        | 64 / 64x500                       | 8 / 25000&nbsp;<sup>2</sup> |

<sup>1</sup> instância está isolada do hardware dedicado de um único cliente.  
<sup>2</sup> 25000 Mbps com redes aceleradas. 
