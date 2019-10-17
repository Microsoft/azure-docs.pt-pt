---
title: incluir ficheiro
description: incluir ficheiro
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: include
ms.date: 08/08/2019
ms.author: azcspmt;jonbeck;cynthn;joelpell
ms.custom: include file
ms.openlocfilehash: 55f82344956af7d6096fce7dbc3d7023589daa69
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/16/2019
ms.locfileid: "72428032"
---
Os tamanhos de VM com otimização de memória oferecem uma alta taxa de memória para CPU que são excelentes para servidores de banco de dados relacionais, caches médios a grandes e análise na memória. Este artigo fornece informações sobre o número de vCPUs, discos de dados e NICs, bem como a taxa de transferência de armazenamento e a largura de banda de rede para cada tamanho neste agrupamento.

* A série Ev3 apresenta os processadores Intel® Xeon® 8171M 2,1 GHz (Skylake) ou Intel® Xeon® E5-2673 v4 2,3 GHz (Broadwell) em uma configuração de hiperthread, fornecendo uma proposta de valor melhor para as cargas de trabalho de uso geral e trazendo o Ev3 para alinhamento com as VMs de uso geral da maioria das outras nuvens.  A memória foi expandida (de 7 GiB/vCPU para 8 GiB/vCPU) enquanto os limites de disco e rede foram ajustados por núcleo para ficarem alinhados com a mudança para o hyperthreading.  O Ev3 é o acompanhamento dos tamanhos de VM de alta memória das famílias D/Dv2.

* Os tamanhos de visualização das séries Eav3 e Easv3 utilizam o processador 2.35 GHz EPYC<sup>TM</sup> 7452 da AMD em uma configuração multi-threaded com até 256 MB de cache L3, aumentando as opções para executar a maioria das cargas de trabalho com otimização de memória.  As séries Eav3 e Easv3 têm as mesmas configurações de memória e disco que o Ev3 & série Esv3.

* A série Mv2 oferece a contagem de vCPU mais alta (até 208 vCPUs) e a maior memória (até 5,7 TiB) de qualquer VM na nuvem. É ideal para bases de dados muito grandes ou outras aplicações que tiram partido de contagens altas de vCPU e grandes quantidades de memória.

* A série M oferece uma contagem alta de vCPU (até 128 vCPUs) e uma grande quantidade de memória (até 3,8 TiB). Ele também é ideal para bancos de dados muito grandes ou outros aplicativos que se beneficiam de contagens vCPUs altas e grandes quantidades de memória.

* As Dv2, série G e as contrapartes DSv2/GS são ideais para aplicativos que exigem mais rapidez vCPUs, melhor desempenho de armazenamento temporário ou têm mais demandas de memória. Proporcionam uma combinação poderosa para inúmeras aplicações empresariais.

* A série DV2, o seguimento da série D original, apresenta uma CPU mais poderosa. A série Dv2 é de cerca de 35% mais rápida do que a série D. Ele é executado nos processadores Intel® Xeon® 8171M 2,1 GHz (Skylake) ou Intel® Xeon® E5-2673 v4 2,3 GHz (Broadwell) ou Intel® Xeon® E5-2673 v3 2,4 GHz (Haswell) e com a tecnologia Intel Turbo Boost 2,0. A série Dv2 tem as mesmas configurações de memória e disco da série D.

* A Computação do Azure oferece tamanhos de máquinas virtuais que são Isolados para um tipo de hardware específico e dedicados a um único cliente.  Estes tamanhos de máquinas virtuais são mais adequados para cargas de trabalho que exigem um elevado grau de isolamento de outros clientes para cargas de trabalho que envolvem elementos como os requisitos de conformidade e regulamentares.  Os clientes também podem optar por subdividir ainda mais os recursos dessas máquinas virtuais isoladas usando o [suporte do Azure para máquinas virtuais aninhadas](https://azure.microsoft.com/blog/nested-virtualization-in-azure/).  Consulte as tabelas de famílias de máquinas virtuais abaixo para suas opções de VM isoladas.

## <a name="esv3-series"></a>Série Esv3

ACU: 160-190 <sup>1</sup>

Armazenamento Premium: com suporte

Cache de armazenamento Premium: com suporte

As instâncias da série ESv3 apresentam os processadores Intel® Xeon® 8171M 2,1 GHz (Skylake) ou Intel® Xeon® E5-2673 v4 2,3 GHz (Broadwell) e podem atingir 3,5 GHz com a tecnologia Intel Turbo Boost 2,0 e usar o armazenamento Premium. As instâncias da série Ev3 são ideais para aplicações empresariais com utilização intensiva da memória.


| Tamanho             | vCPU | Memória: GiB | Armazenamento (SSD) temporário em GiB | Máximo de discos de dados | Débito máximo do armazenamento temporário e em cache: IOPS/MBps (tamanho da cache em GiB) | Débito máximo do disco não colocado em cache: IOPS/MBps | Máximo de NICs/largura de banda de rede esperada (Mbps) |
|------------------|--------|-------------|----------------|----------------|-----------------------------------------------------------------------|-------------------------------------------|------------------------------------------------|
| Standard_E2s_v3 | 2      | 16          | 32             | 4              | 4000/32 (50)                                                       | 3200/48                                | 2 / 1000                                   |
| Standard_E4s_v3 @ no__t-0<sup>2</sup> | 4      | 32          | 64             | 8              | 8000/64 (100)                                                      | 6400/96                                | 2 / 2000                                   |
| Standard_E8s_v3 @ no__t-0<sup>2</sup> | 8      | 64          | 128            | 16             | 16000/128 (200)                                                    | 12800/192                              | 4 / 4000                                       |
| Standard_E16s_v3 @ no__t-0<sup>2</sup> | 16     | 128         | 256            | 32             | 32000/256 (400)                                                    | 25600/384                              | 8 / 8000                                       |
| Standard_E20s_v3                   | 20     | 160         | 320            | 32             | 40000/320 (400)                                                    | 32000/480                              | 8 / 10000                                       |
| Standard_E32s_v3 @ no__t-0<sup>2</sup> | 32     | 256         | 512            | 32             | 64000/512 (800)                                                    | 51200/768                              | 8 / 16000                             |
| Standard_E48s_v3 @ no__t-0<sup>2</sup> | 48     | 384         | 768            | 32             | 96000/768 (1200)                                                   | 76800/1152                             | 8 / 24000                             |
| Standard_E64s_v3 @ no__t-0<sup>2</sup> | 64     | 432         | 864            | 32             | 128000/1024 (1600)                                                   | 80000/1200                             | 8 / 30000                             |
| Standard_E64is_v3 @ no__t-0<sup>3</sup> | 64     | 432         | 864            | 32             | 128000/1024 (1600)                                                   | 80000/1200                             | 8 / 30000                             |


<sup>1</sup> o recurso da VM da série Esv3 Intel® tecnologia Hyper-Threading.

<sup>2</sup> tamanhos de núcleos restritos disponíveis.

<sup>3</sup> a instância é isolada em hardware dedicado a um único cliente.

## <a name="easv3-series-preview"></a>Série Easv3 (versão prévia)

Armazenamento Premium: com suporte

Cache de armazenamento Premium: com suporte

Os tamanhos da série Easv3 baseiam-se no processador AMD EPYC<sup>TM</sup> 7452 de 2.35 GHz que pode alcançar um Fmax aumentado de 3.35 GHz e usar o armazenamento Premium. Os tamanhos da série Easv3 são ideais para aplicativos empresariais com uso intensivo de memória.

[Clique aqui para inscrever-se para a versão prévia](http://aka.ms/azureamdpreview).

| Tamanho | vCPU | Memória: GiB | Armazenamento temporário (SSD): GiB |
|---|---|---|---|
| Standard_E2as_v3  | 2  | 16  | 32  |
| Standard_E4as_v3  | 4  | 32  | 64  |
| Standard_E8as_v3  | 8  | 64  | 128 |
| Standard_E16as_v3 | 16 | 128 | 256 |
| Standard_E32as_v3 | 32 | 256 | 512 |
| Standard_E48as_v3 | 48 | 384 | 768 |
| Standard_E64as_v3 | 64 | 432 | 864 |

## <a name="ev3-series"></a>Série Ev3 

ACU: 160-190 <sup>1</sup>

Armazenamento Premium: sem suporte

Armazenamento em cache Premium: sem suporte

As instâncias da série Ev3 apresentam os processadores Intel® Xeon® 8171M 2,1 GHz (Skylake) ou Intel® Xeon® E5-2673 v4 2,3 GHz (Broadwell) e podem atingir 3,5 GHz com a tecnologia Intel Turbo Boost 2,0. As instâncias da série Ev3 são ideais para aplicações empresariais com utilização intensiva da memória.

O armazenamento de discos de dados é cobrado em separado das máquinas virtuais. Para utilizar os discos de armazenamento premium, utilize os tamanhos ESv3. Os medidores de preços e de faturação dos tamanhos ESv3 são os mesmos do que os da série Ev3. 


| Tamanho            | vCPU | Memória: GiB | Armazenamento (SSD) temporário em GiB | Máximo de discos de dados | Débito do armazenamento temporário máximo: IOPS/MBps de Leitura/MBps de Escrita | NICs/Largura de banda da rede máximos |
|-----------------|-----------|-------------|----------------|----------------|----------------------------------------------------------|------------------------------|
| Standard_E2_v3  | 2         | 16          | 50             | 4              | 3000/46/23                                               | 2 / 1000                 |
| Standard_E4_v3  | 4         | 32          | 100            | 8              | 6000/93/46                                               | 2 / 2000                 |
| Standard_E8_v3  | 8         | 64          | 200            | 16             | 12000/187/93                                             | 4 / 4000                     |
| Standard_E16_v3 | 16        | 128         | 400            | 32             | 24000/375/187                                            | 8 / 8000                     |
| Standard_E20_v3 | 20        | 160         | 500            | 32             | 30000/469/234                                            | 8 / 10000                     |
| Standard_E32_v3 | 32        | 256         | 800            | 32             | 48000/750/375                                            | 8 / 16000                 |
| Standard_E48_v3 | 48        | 384         | 1200            | 32             | 96000/1000/500                                            | 8 / 24000                 |
| Standard_E64_v3 | 64        | 432         | 1600           | 32             | 96000/1000/500                                           | 8 / 30000           |
| Standard_E64i_v3 @ no__t-0<sup>2, &nbsp;3</sup> | 64        | 432         | 1600           | 32             | 96000/1000/500                                           | 8 / 30000           |

<sup>1</sup> o recurso da VM da série Ev3 Intel® tecnologia Hyper-Threading.

<sup>2</sup> tamanhos de núcleos restritos disponíveis.

<sup>3</sup> a instância é isolada em hardware dedicado a um único cliente.

## <a name="eav3-series-preview"></a>Série Eav3 (versão prévia)

Armazenamento Premium: sem suporte

Armazenamento em cache Premium: sem suporte

Os tamanhos da série Eav3 baseiam-se no processador AMD EPYC<sup>TM</sup> 7452 de 2.35 GHz que pode alcançar um Fmax aumentado de 3.35 GHz e usar o armazenamento Premium. Os tamanhos da série Eav3 são ideais para aplicativos empresariais com uso intensivo de memória. O armazenamento de discos de dados é cobrado em separado das máquinas virtuais. Para usar discos de armazenamento Premium, use os tamanhos da série Easv3. Os medidores de cobrança e preço para os tamanhos de Easv3 são os mesmos que os da série Eav3.

[Clique aqui para inscrever-se para a versão prévia](http://aka.ms/azureamdpreview).

| Tamanho             | vCPU | Memória: GiB | Armazenamento temporário (SSD): GiB |
|------------------|------|-------------|-------------------------|
| Standard_E2a_v3  | 2    | 16          | 50                      |
| Standard_E4a_v3  | 4    | 32          | 100                     |
| Standard_E8a_v3  | 8    | 64          | 200                     |
| Standard_E16a_v3 | 16   | 128         | 400                     |
| Standard_E32a_v3 | 32   | 256         | 800                     |
| Standard_E48a_v3 | 48   | 384         | 1200                    |
| Standard_E64a_v3 | 64   | 432         | 1600                    |

## <a name="mv2-series"></a>Série Mv2

Armazenamento Premium: com suporte

Cache de armazenamento Premium: com suporte

Acelerador de Gravação: [com suporte](https://docs.microsoft.com/azure/virtual-machines/windows/how-to-enable-write-accelerator)

A Mv2-Series apresenta alta taxa de transferência, baixa latência, um armazenamento de NVMe local mapeado diretamente em execução em um processador de Skylake (Hyper-Threading Intel®® Xeon) com uma frequência de base básica de 2,5 GHz e uma frequência máxima de Turbo de 3,8 GHz. Todos os tamanhos de máquina virtual da série Mv2 podem usar discos persistentes Standard e Premium. As instâncias da série Mv2 são tamanhos de VM com otimização de memória que fornecem desempenho computacional inigualável para dar suporte a grandes bancos de dados na memória e cargas de trabalho, com uma alta taxa de memória para CPU, ideal para servidores de banco de dados relacionais, caches grandes e na memória Analytics. 

|Tamanho | vCPU | Memória: GiB | Armazenamento (SSD) temporário em GiB | Máximo de discos de dados | Débito máximo do armazenamento temporário e em cache: IOPS/MBps (tamanho da cache em GiB) | Débito máximo do disco não colocado em cache: IOPS/MBps | Máximo de NICs/largura de banda de rede esperada (Mbps) |
|-----------------|------|-------------|----------------|----------------|-----------------------------------------------------------------------|-------------------------------------------|------------------------------|
| Standard_M208ms_v2<sup>1, 2</sup> | 208 | 5700 | 4096 | 64 | 80000/800 (7040) | 40000/1000 | 8 / 16000 |
| Standard_M208s_v2<sup>1, 2</sup> | 208 | 2850 | 4096 | 64 | 80000/800 (7040) | 40000/1000 | 8 / 16000 |

O recurso da VM da série Mv2 Intel® tecnologia Hyper-Threading  

<sup>1</sup> essas VMs grandes exigem um desses SOS convidados com suporte: windows Server 2016, windows Server 2019, SLES 12 SP4, SLES 15.

<sup>2</sup> as VMs da série Mv2 são apenas de geração 2. Se você estiver usando o Linux, consulte a seção a seguir para saber como localizar e selecionar uma imagem do SUSE Linux.

#### <a name="find-a-suse-image"></a>Localizar uma imagem SUSE

Para selecionar uma imagem do SUSE Linux apropriada no portal do Azure: 

1. Na portal do Azure, selecione **criar um recurso** 
1. Pesquise "SUSE SAP" 
1. As imagens do SLES for SAP geração 2 estão disponíveis como de pagamento conforme o uso ou BYOS (traga sua própria assinatura). Nos resultados da pesquisa, expanda a categoria de imagem desejada:

    * SUSE Linux Enterprise Server (SLES) para SAP
    * SUSE Linux Enterprise Server (SLES) para SAP (BYOS)
    
1. As imagens SUSE compatíveis com a série Mv2 são prefixadas com o nome `GEN2:`. As seguintes imagens SUSE estão disponíveis para VMs da série Mv2:

    * GEN2: SUSE Linux Enterprise Server (SLES) 12 SP4 para aplicativos SAP
    * GEN2: SUSE Linux Enterprise Server (SLES) 15 para aplicativos SAP
    * GEN2: SUSE Linux Enterprise Server (SLES) 12 SP4 para aplicativos SAP (BYOS)
    * GEN2: SUSE Linux Enterprise Server (SLES) 15 para aplicativos SAP (BYOS)

#### <a name="select-a-suse-image-via-azure-cli"></a>Selecione uma imagem SUSE via CLI do Azure

Para ver uma lista da imagem do SLES for SAP disponível atualmente para VMs da série Mv2, use o seguinte comando [`az vm image list`](https://docs.microsoft.com/cli/azure/vm/image?view=azure-cli-latest#az-vm-image-list) :

```azurecli
az vm image list --output table --publisher SUSE --sku gen2 --all
```

O comando gera as VMs de geração 2 disponíveis atualmente disponíveis no SUSE para VMs da série Mv2. 

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

Armazenamento Premium: com suporte

Cache de armazenamento Premium: com suporte

Os tamanhos da série M são baseados no Intel (R) Xeon (R) CPU E7-8890 v3 @ 2,50 GHz   

Acelerador de Gravação: [com suporte](https://docs.microsoft.com/azure/virtual-machines/windows/how-to-enable-write-accelerator)

| Tamanho            | vCPU | Memória: GiB | Armazenamento (SSD) temporário em GiB | Máximo de discos de dados | Débito máximo do armazenamento temporário e em cache: IOPS/MBps (tamanho da cache em GiB) | Débito máximo do disco não colocado em cache: IOPS/MBps | Máximo de NICs/largura de banda de rede esperada (Mbps) |
|-----------------|------|-------------|----------------|----------------|-----------------------------------------------------------------------|-------------------------------------------|------------------------------|
| Standard_M8ms @ no__t-0<sup>3</sup>    | 8  | 218,75 | 256  | 8  | 10000/100 (793)  | 5000/125 | 4 / 2000 |
| Standard_M16ms @ no__t-0<sup>3</sup>   | 16 | 437,5  | 512  | 16 | 20000/200 (1587) | 10000/250 | 8 / 4000 |
| Standard_M32ts | 32 | 192    | 1024 | 32 | 40000/400 (3174) | 20000/500 | 8 / 8000 |
| Standard_M32ls | 32 | 256    | 1024 | 32 | 40000/400 (3174) | 20000/500 | 8 / 8000 |
| Standard_M32ms @ no__t-0<sup>3</sup>   | 32 | 875    | 1024 | 32 | 40000/400 (3174) | 20000/500 | 8 / 8000 |
| Standard_M64s  | 64 | 1024   | 2048 | 64 | 80000/800 (6348)| 40000/1000 | 8 / 16000          |
| Standard_M64ls  | 64 | 512    | 2048 | 64 | 80000/800 (6348) | 40000/1000 | 8 / 16000 |
| Standard_M64ms @ no__t-0<sup>3</sup>  | 64   | 1792 | 2048 | 64 | 80000/800 (6348)| 40000/1000 | 8 / 16000          |
| Standard_M128s @ no__t-0<sup>2</sup> | 128  | 2048        | 4096  | 64 | 160000/1600 (12696) | 80000/2000                            | 8 / 30000          |
| Standard_M128ms @ no__t-0<sup>2, &nbsp;3, &nbsp;4</sup> | 128  | 3892  | 4096 | 64 | 160000/1600 (12696) | 80000/2000                            | 8 / 30000          |
| Standard_M64   | 64  | 1024 | 7168  | 64 | 80000/800 (1228) | 40000/1000 | 8 / 16000 |
| Standard_M64m  | 64  | 1792 | 7168  | 64 | 80000/800 (1228) | 40000/1000 | 8 / 16000 |
| Standard_M128 @ no__t-0 @ no__t-12  | 128 | 2048 | 14336 | 64 | 250000/1600 (2456) | 80000/2000 | 8 / 32000 |
| Standard_M128m @ no__t-0 @ no__t-12 | 128 | 3892 | 14336 | 64 | 250000/1600 (2456) | 80000/2000 | 8 / 32000 |



o recurso da VM da série <sup>1</sup> M Intel® tecnologia Hyper-Threading

<sup>2</sup> mais de 64 vCPU requer um destes SOS convidados com suporte: Windows Server 2016, Ubuntu 16, 4 LTS, SLES 12 SP2 e Red Hat Enterprise Linux, CentOS 7,3 ou Oracle Linux 7,3 com Lis 4.2.1.

<sup>3</sup> tamanhos de núcleos restritos disponíveis.

<sup>4</sup> a instância é isolada em hardware dedicado a um único cliente.
<br>


## <a name="dsv2-series-11-15"></a>Série DSv2 11-15

ACU: 210-250 <sup>1</sup>

Armazenamento Premium: com suporte

Cache de armazenamento Premium: com suporte

Os tamanhos da série DSv2 são executados no Intel® Xeon® 8171M 2,1 GHz (Skylake) ou no Intel® Xeon® E5-2673 v4 2,3 GHz (Broadwell) ou nos processadores Intel® Xeon® E5-2673 v3 2,4 GHz (Haswell).

| Tamanho | vCPU | Memória: GiB | Armazenamento (SSD) temporário em GiB | Máximo de discos de dados | Débito máximo do armazenamento temporário e em cache: IOPS/MBps (tamanho da cache em GiB) | Débito máximo do disco não colocado em cache: IOPS/MBps | Máximo de NICs/largura de banda de rede esperada (Mbps) |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_DS11_v2 @ no__t-0<sup>3</sup> |2 |14 |28 |8 |8000/64 (72) |6400/96 |2 / 1500 |
| Standard_DS12_v2 @ no__t-0<sup>3</sup> |4 |28 |56 |16 |16000/128 (144) |12800/192 |4 / 3000 |
| Standard_DS13_v2 @ no__t-0<sup>3</sup> |8 |56 |112 |32 |32000/256 (288) |25600/384 |8 / 6000 |
| Standard_DS14_v2 @ no__t-0<sup>3</sup>|16 |112 |224 |64 |64000/512 (576) |51200/768 |8 / 12000 |
| Standard_DS15_v2 @ no__t-0<sup>2</sup> |20 |140 |280 |64 |80000/640 (720) |64000/960 |8/25000 @ no__t-0<sup>4</sup>

<sup>1</sup> a taxa de transferência máxima possível do disco (IOPS ou Mbps) com uma VM da série DSv2 pode ser limitada pelo número, tamanho e distribuição dos discos anexados.  Para obter detalhes, consulte [projetando para alto desempenho](../articles/virtual-machines/windows/premium-storage-performance.md).  
<sup>2</sup> a instância é isolada em hardware dedicado a um único cliente.  
<sup>3</sup> tamanhos de núcleos restritos disponíveis.  
<sup>4</sup> 25000 Mbps com rede acelerada. 

<br>

## <a name="dv2-series-11-15"></a>Série Dv2 11-15

ACU: 210 - 250

Armazenamento Premium: sem suporte

Armazenamento em cache Premium: sem suporte

Os tamanhos da série DSv2 são executados no Intel® Xeon® 8171M 2,1 GHz (Skylake) ou no Intel® Xeon® E5-2673 v4 2,3 GHz (Broadwell) ou nos processadores Intel® Xeon® E5-2673 v3 2,4 GHz (Haswell).

| Tamanho              | vCPU | Memória: GiB | Armazenamento (SSD) temporário em GiB | Débito do armazenamento temporário máximo: IOPS/MBps de Leitura/MBps de Escrita | Máximo do disco de dados/débito: IOPS | Máximo de NICs/largura de banda de rede esperada (Mbps) |
|-------------------|-----------|-------------|----------------|----------------------------------------------------------|-----------------------------------|------------------------------|
| Standard_D11_v2   | 2         | 14          | 100            | 6000 / 93 / 46                                           | 8 / 8x500                         | 2 / 1500                     |
| Standard_D12_v2   | 4         | 28          | 200            | 12000 / 187 / 93                                         | 16 / 16x500                         | 4 / 3000                     |
| Standard_D13_v2   | 8         | 56          | 400            | 24000 / 375 / 187                                        | 32 / 32x500                       | 8 / 6000                     |
| Standard_D14_v2   | 16        | 112         | 800            | 48000 / 750 / 375                                        | 64/64x500                       | 8 / 12000          |
| Standard_D15_v2 @ no__t-0<sup>1</sup> | 20        | 140         | 1000          | 60000 / 937 / 468                                        | 64/64x500                       | 8/25000 @ no__t-0<sup>2</sup> |

<sup>1</sup> a instância é isolada em hardware dedicado a um único cliente.  
<sup>2</sup> 25000 Mbps com rede acelerada. 
