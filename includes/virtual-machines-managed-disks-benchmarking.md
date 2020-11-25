---
title: incluir ficheiro
description: incluir ficheiro
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 01/11/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 358e92d8e43473c168e24be9f4af504e6ffcc37a
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96028431"
---
*Aquecimento da Cache*  
O disco com o cache do anfitrião ReadOnly é capaz de dar IOPS mais alto do que o limite do disco. Para obter este desempenho de leitura máxima da cache do anfitrião, primeiro deve aquecer a cache deste disco. Isto garante que os IOs de Leitura que a ferramenta de benchmarking irá conduzir no volume CacheReads, realmente atinge a cache, e não o disco diretamente. Os hits de cache resultam em IOPS adicionais a partir do disco ativado por cache única.

> [!IMPORTANT]
> Tem de aquecer a cache antes de fazer o benchmarking, sempre que o VM for reiniciado.

## <a name="iometer"></a>Ímetro

[Descarregue a ferramenta Iometer](http://sourceforge.net/projects/iometer/files/iometer-stable/1.1.0/iometer-1.1.0-win64.x86_64-bin.zip/download) no VM.

### <a name="test-file"></a>Arquivo de teste

O iometer utiliza um ficheiro de teste que é armazenado no volume em que se faz o teste de benchmarking. Ele conduz leituras e escritos neste ficheiro de teste para medir o disco IOPS e produção. O iometer cria este ficheiro de teste se não tiver fornecido um. Crie um ficheiro de teste de 200 GB chamado iobw.tst nos volumes CacheReads e NoCacheWrites.

### <a name="access-specifications"></a>Especificações de acesso

As especificações, o tamanho do IO de pedido, % de leitura/escrita, % aleatório/sequencial são configurados utilizando o separador "Especificações de acesso" no iometer. Crie uma especificação de acesso para cada um dos cenários descritos abaixo. Crie as especificações de acesso e "Save" com um nome apropriado como – RandomWrites \_ 8K, RandomReads \_ 8K. Selecione a especificação correspondente ao executar o cenário de teste.

Um exemplo de especificações de acesso para o cenário máximo de IOPS de escrita é mostrado abaixo,  
    ![Exemplo de especificações de acesso para o máximo de IOPS de escrita](../articles/virtual-machines/linux/media/premium-storage-performance/image8.png)

### <a name="maximum-iops-test-specifications"></a>Especificações máximas de teste do IOPS

Para demonstrar o máximo de IOPs, utilize um tamanho de pedido menor. Use o tamanho do pedido de 8K e crie especificações para escritas e leituras aleatórias.

| Especificação de acesso | Tamanho do pedido | % aleatório | Ler % |
| --- | --- | --- | --- |
| RandomWrites \_ 8K |8 K |100 |0 |
| RandomReads \_ 8K |8 K |100 |100 |

### <a name="maximum-throughput-test-specifications"></a>Especificações máximas de teste de produção

Para demonstrar o máximo de produção, utilize um tamanho de pedido maior. Utilize o tamanho do pedido de 64 K e crie especificações para escritas e leituras aleatórias.

| Especificação de acesso | Tamanho do pedido | % aleatório | Ler % |
| --- | --- | --- | --- |
| RandomWrites \_ 64K |64 K |100 |0 |
| RandomReads \_ 64K |64 K |100 |100 |

### <a name="run-the-iometer-test"></a>Executar o teste do iometer

Execute os passos abaixo para aquecer cache

1. Criar duas especificações de acesso com valores apresentados abaixo,

   | Nome | Tamanho do pedido | % aleatório | Ler % |
   | --- | --- | --- | --- |
   | RandomWrites \_ 1MB |1 MB |100 |0 |
   | RandomReads \_ 1MB |1 MB |100 |100 |
1. Executar o teste de Iometer para inicializar o disco de cache com os seguintes parâmetros. Utilize três fios de trabalho para o volume de destino e uma profundidade de fila de 128. Desa cosar a duração do teste para 2 horas no separador "Configuração de Teste".

   | Cenário | Volume-alvo | Nome | Duração |
   | --- | --- | --- | --- |
   | Inicializar o disco cache |CacheReads |RandomWrites \_ 1MB |2 horas |
1. Executar o teste de Iometer para aquecer o disco de cache com os seguintes parâmetros. Utilize três fios de trabalho para o volume de destino e uma profundidade de fila de 128. Desa cosar a duração do teste para 2 horas no separador "Configuração de Teste".

   | Cenário | Volume-alvo | Nome | Duração |
   | --- | --- | --- | --- |
   | Aquecer disco cache |CacheReads |RandomReads \_ 1MB |2 horas |

Depois de aquecer o disco de cache, proceda com os cenários de teste listados abaixo. Para executar o teste do iometer, utilize pelo menos três fios de trabalho para **cada** volume de destino. Para cada fio do trabalhador, selecione o volume-alvo, desempate a profundidade da fila e selecione uma das especificações de ensaio guardadas, como indicado na tabela abaixo, para executar o cenário de teste correspondente. A tabela também mostra os resultados esperados para IOPS e Produção ao executar estes testes. Para todos os cenários, é utilizado um pequeno tamanho IO de 8 KB e uma alta profundidade de fila de 128.

| Cenário de teste | Volume-alvo | Nome | Resultado |
| --- | --- | --- | --- |
| Um máximo de Ler IOPS |CacheReads |RandomWrites \_ 8K |50.000 OPS |
| Um máximo de Escrever iops |NoCacheWrites |RandomReads \_ 8K |64.000 OPS |
| Um máximo de IOPS combinados |CacheReads |RandomWrites \_ 8K |100.000 OPS |
| NoCacheWrites |RandomReads \_ 8K | &nbsp; | &nbsp; |
| Um máximo de Ler MB/seg |CacheReads |RandomWrites \_ 64K |524 MB/seg |
| Um máximo de Escrever MB/seg |NoCacheWrites |RandomReads \_ 64K |524 MB/seg |
| MB/seg combinado |CacheReads |RandomWrites \_ 64K |1000 MB/seg |
| NoCacheWrites |RandomReads \_ 64K | &nbsp; | &nbsp; |

Abaixo estão as imagens dos resultados dos testes do IOPS combinados e dos cenários de produção.

### <a name="combined-reads-and-writes-maximum-iops"></a>Leituras combinadas e escreve o máximo IOPS

![Leituras e escritos máximos de IOPS combinados](../articles/virtual-machines/linux/media/premium-storage-performance/image9.png)

### <a name="combined-reads-and-writes-maximum-throughput"></a>Leituras combinadas e escreve a produção máxima

![Leituras combinadas e escreve o rendimento máximo](../articles/virtual-machines/linux/media/premium-storage-performance/image10.png)

## <a name="fio"></a>FIO

A FIO é uma ferramenta popular para comparar o armazenamento nos VMs Linux. Tem a flexibilidade para selecionar diferentes tamanhos de IO, leituras sequenciais ou aleatórias e escreve. Cria fios ou processos de trabalhador para realizar as operações de E/S especificadas. Pode especificar o tipo de operações de E/S que cada fio de trabalho deve efetuar utilizando ficheiros de trabalho. Criámos um ficheiro de trabalho por cenário ilustrado nos exemplos abaixo. Pode alterar as especificações destes ficheiros de trabalho para comparar diferentes cargas de trabalho em execução no Armazenamento Premium. Nos exemplos, estamos a utilizar um **Ubuntu** Standard DS 14 VM. Utilize a mesma configuração descrita no início da secção de benchmarking e aqueça a cache antes de escorrê-la nos testes de benchmarking.

Antes de começar, [faça o download da FIO](https://github.com/axboe/fio) e instale-o na sua máquina virtual.

Executar o seguinte comando para Ubuntu,

```
apt-get install fio
```

Usamos quatro fios de trabalhador para dirigir operações de escrita e quatro fios de trabalho para dirigir operações de leitura nos discos. Os trabalhadores da Write estão a conduzir o tráfego no volume "nocache", que tem 10 discos com cache definido para "Nenhum". Os trabalhadores da Leitura estão a conduzir o tráfego no volume "readcache", que tem um disco com cache definido para "ReadOnly".

### <a name="maximum-write-iops"></a>IOPS de escrita máxima

Crie o ficheiro de trabalho com as seguintes especificações para obter o máximo de IOPS de escrita. Diga-lhe "fiowrite.ini".

```ini
[global]
size=30g
direct=1
iodepth=256
ioengine=libaio
bs=8k
numjobs=4

[writer1]
rw=randwrite
directory=/mnt/nocache
```

Note as coisas-chave que estão em conformidade com as diretrizes de design discutidas em secções anteriores. Estas especificações são essenciais para impulsionar o máximo de IOPS,  

* Uma alta profundidade de fila de 256.  
* Um pequeno bloco de 8 KB.  
* Múltiplos fios executando escritas aleatórias.

Executar o seguinte comando para iniciar o teste FIO por 30 segundos,  

```
sudo fio --runtime 30 fiowrite.ini
```

Enquanto o teste decorre, é possível ver o número de IOPS de escrita que os discos VM e Premium estão a entregar. Como mostrado na amostra abaixo, o DS14 VM está a entregar o seu limite máximo de IOPS de escrita máxima de 50.000 IOPS.  
    ![Número de discos IOPS VM e Premium de escrita estão a ser entregues.](../articles/virtual-machines/linux/media/premium-storage-performance/image11.png)

### <a name="maximum-read-iops"></a>IOPS de leitura máxima

Crie o ficheiro de trabalho com as seguintes especificações para obter o máximo de IOPS de leitura. Diga-lhe "fioread.ini".

```ini
[global]
size=30g
direct=1
iodepth=256
ioengine=libaio
bs=8k
numjobs=4

[reader1]
rw=randread
directory=/mnt/readcache
```

Note as coisas-chave que estão em conformidade com as diretrizes de design discutidas em secções anteriores. Estas especificações são essenciais para impulsionar o máximo de IOPS,

* Uma alta profundidade de fila de 256.  
* Um pequeno bloco de 8 KB.  
* Múltiplos fios executando escritas aleatórias.

Executar o seguinte comando para iniciar o teste FIO por 30 segundos,

```
sudo fio --runtime 30 fioread.ini
```

Enquanto o teste decorre, é possível ver o número de IOPS lidos que os discos VM e Premium estão a entregar. Como mostrado na amostra abaixo, o DS14 VM está a entregar mais de 64.000 IOPS de leitura. Esta é uma combinação do disco e do desempenho da cache.  
    ![Screenshot do número de discos IOPS VM e Premium de escrita estão a ser entregues.](../articles/virtual-machines/linux/media/premium-storage-performance/image12.png)

### <a name="maximum-read-and-write-iops"></a>Leitura máxima e escrita IOPS

Crie o ficheiro de trabalho com as seguintes especificações para obter o máximo combinado de Read e Write IOPS. Diga-lhe "fioreadwrite.ini".

```ini
[global]
size=30g
direct=1
iodepth=128
ioengine=libaio
bs=4k
numjobs=4

[reader1]
rw=randread
directory=/mnt/readcache

[writer1]
rw=randwrite
directory=/mnt/nocache
rate_iops=12500
```

Note as coisas-chave que estão em conformidade com as diretrizes de design discutidas em secções anteriores. Estas especificações são essenciais para impulsionar o máximo de IOPS,

* Uma alta profundidade de fila de 128.  
* Um pequeno bloco tamanho de 4 KB.  
* Múltiplos fios executando leituras e escritas aleatórias.

Executar o seguinte comando para iniciar o teste FIO por 30 segundos,

```
sudo fio --runtime 30 fioreadwrite.ini
```

Enquanto o teste decorre, é possível ver o número de leitura combinada e escrever IOPS que os discos VM e Premium estão a entregar. Como mostrado na amostra abaixo, o DS14 VM está a entregar mais de 100.000 IOPS combinados de leitura e escrita. Esta é uma combinação do disco e do desempenho da cache.  
    ![Leitura combinada e escrita IOPS](../articles/virtual-machines/linux/media/premium-storage-performance/image13.png)

### <a name="maximum-combined-throughput"></a>Produção combinada máxima

Para obter o máximo combinado De ler e escrever, use um tamanho de bloco maior e uma grande profundidade de fila com múltiplos fios executando leituras e escritas. Pode utilizar um bloco de 64 KB e uma profundidade de fila de 128.
