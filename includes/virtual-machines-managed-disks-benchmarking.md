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
ms.openlocfilehash: e5148ff9e92a2e550a3117356a4e77cbac8fc6f4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "67673471"
---
*Aquecimento da Cache*  
O disco com o recolhimento ReadOnly é capaz de dar IOPS mais elevado do que o limite do disco. Para obter este desempenho máximo de leitura a partir da cache do hospedeiro, primeiro deve aquecer a cache deste disco. Isto garante que o Read IOs que a ferramenta de benchmarking irá conduzir no volume CacheReads, realmente atinge a cache, e não o disco diretamente. Os acertos de cache resultam em IOPS adicionais a partir do disco ativado por cache única.

> [!IMPORTANT]
> Deve aquecer a cache antes de executar o benchmarking, sempre que a VM for reiniciada.

## <a name="tools"></a>Ferramentas

### <a name="iometer"></a>Iómetro

[Descarregue a ferramenta Iometer](https://sourceforge.net/projects/iometer/files/iometer-stable/2006-07-27/iometer-2006.07.27.win32.i386-setup.exe/download) no VM.

#### <a name="test-file"></a>Arquivo de teste

O Iometer usa um ficheiro de teste que é armazenado no volume em que executa o teste de benchmarking. Conduz Leituras e Escritos neste ficheiro de teste para medir o disco IOPS e O Despto. Iometer cria este ficheiro de teste se não tiver fornecido um. Crie um ficheiro de teste de 200 GB chamado iobw.tst nos volumes CacheReads e NoCacheWrites.

#### <a name="access-specifications"></a>Especificações de acesso

As especificações, o tamanho do pedido IO, % de leitura/escrita, % aleatório/sequencial são configurados utilizando o separador "Especificações de Acesso" no iómetro. Crie uma especificação de acesso para cada um dos cenários descritos abaixo. Crie as especificações de acesso e "Guarde"\_com um\_nome apropriado como – RandomWrites 8K, RandomReads 8K. Selecione a especificação correspondente ao executar o cenário de teste.

Um exemplo de especificações de acesso para o cenário máximo de IOPS de Escrita é mostrado abaixo,  
    ![Exemplo de especificações de acesso para iOPS de escrita máxima](../articles/virtual-machines/linux/media/premium-storage-performance/image8.png)

#### <a name="maximum-iops-test-specifications"></a>Especificações máximas de teste do IOPS

Para demonstrar o máximo de IOPs, utilize o tamanho do pedido mais pequeno. Utilize o tamanho do pedido 8K e crie especificações para Escritas e Leituras Aleatórias.

| Especificação de Acesso | Tamanho do pedido | %aleatório | Ler % |
| --- | --- | --- | --- |
| RandomWrites\_8K |8 K |100 |0 |
| RandomReads\_8K |8 K |100 |100 |

#### <a name="maximum-throughput-test-specifications"></a>Especificações máximas de ensaio de entrada

Para demonstrar o máximo de entrada, utilize um maior tamanho de pedido. Utilize o tamanho do pedido de 64 K e crie especificações para Escritas e Leituras Aleatórias.

| Especificação de Acesso | Tamanho do pedido | %aleatório | Ler % |
| --- | --- | --- | --- |
| RandomWrites\_64K |64 K |100 |0 |
| RandomReads\_64K |64 K |100 |100 |

#### <a name="run-the-iometer-test"></a>Fazer o teste de iómetro

Execute os passos abaixo para aquecer cache

1. Criar duas especificações de acesso com valores abaixo apresentados,

   | Nome | Tamanho do pedido | %aleatório | Ler % |
   | --- | --- | --- | --- |
   | RandomWrites\_1MB |1 MB |100 |0 |
   | RandomReads\_1MB |1 MB |100 |100 |
1. Executar o teste de iómetro para inicializar o disco cache com os seguintes parâmetros. Utilize três fios de trabalhador para o volume de destino e uma profundidade de fila de 128. Detete a duração do teste de "Tempo de funcionamento" para 2 horas no separador "Configuração de Teste".

   | Cenário | Volume-alvo | Nome | Duração |
   | --- | --- | --- | --- |
   | Inicializar o Disco cache |Cachereads |RandomWrites\_1MB |2 horas |
1. Executar o teste de iómetro para aquecer o disco cache com os seguintes parâmetros. Utilize três fios de trabalhador para o volume de destino e uma profundidade de fila de 128. Detete a duração do teste de "Tempo de funcionamento" para 2 horas no separador "Configuração de Teste".

   | Cenário | Volume-alvo | Nome | Duração |
   | --- | --- | --- | --- |
   | Aquecer cache disk |Cachereads |RandomReads\_1MB |2 horas |

Depois de aquecido o disco cache, proceda aos cenários de teste listados abaixo. Para executar o teste de iómetro, utilize pelo menos três fios de trabalhador para **cada** volume-alvo. Para cada fio de trabalho, selecione o volume de destino, delineie a profundidade da fila e selecione uma das especificações de teste guardadas, como indicado na tabela abaixo, para executar o cenário de teste correspondente. A tabela também mostra os resultados esperados para iOPS e Throughput ao executar estes testes. Para todos os cenários, é utilizado um pequeno tamanho iO de 8 KB e uma alta profundidade de fila de 128.

| Cenário de teste | Volume-alvo | Nome | Resultado |
| --- | --- | --- | --- |
| Um máximo de Ler IOPS |Cachereads |RandomWrites\_8K |50.000 IOPS |
| Um máximo de Escrever IOPS |NoCacheWrites |RandomReads\_8K |64.000 IOPS |
| Um máximo de IOPS combinados |Cachereads |RandomWrites\_8K |100.000 IOPS |
| NoCacheWrites |RandomReads\_8K | &nbsp; | &nbsp; |
| Um máximo de Ler MB/seg |Cachereads |RandomWrites\_64K |524 MB/seg |
| Um máximo de Escreva MB/seg |NoCacheWrites |RandomReads\_64K |524 MB/seg |
| MB/seg combinado |Cachereads |RandomWrites\_64K |1000 MB/seg |
| NoCacheWrites |RandomReads\_64K | &nbsp; | &nbsp; |

Abaixo estão as imagens dos resultados dos testes de iómetro para cenários combinados de IOPS e Deputados.

#### <a name="combined-reads-and-writes-maximum-iops"></a>Leituras combinadas e escreve iOPS máximos

![Leituras combinadas e escreve IOPS máximos](../articles/virtual-machines/linux/media/premium-storage-performance/image9.png)

#### <a name="combined-reads-and-writes-maximum-throughput"></a>Leituras combinadas e escreve o máximo de entrada

![Leituras combinadas e escreve o máximo de entrada](../articles/virtual-machines/linux/media/premium-storage-performance/image10.png)

### <a name="fio"></a>FIO

Fio é uma ferramenta popular para comparar o armazenamento nos VMs Linux. Tem a flexibilidade para selecionar diferentes tamanhos io, leituras sequenciais ou aleatórias e escritos. Cria fios ou processos de trabalhador para realizar as operações de I/S especificadas. Pode especificar o tipo de operações de I/S que cada fio de trabalho deve realizar utilizando ficheiros de trabalho. Criámos um ficheiro de emprego por cenário ilustrado nos exemplos abaixo. Pode alterar as especificações destes ficheiros de trabalho para comparar diferentes cargas de trabalho em execução no Armazenamento Premium. Nos exemplos, estamos a usar um VM Standard DS 14 a executar **Ubuntu**. Utilize a mesma configuração descrita no início da secção de Benchmarking e aqueça a cache antes de executar os testes de benchmarking.

Antes de começar, [baixe o FIO](https://github.com/axboe/fio) e instale-o na sua máquina virtual.

Executar o seguinte comando para Ubuntu,

```
apt-get install fio
```

Usamos quatro fios de trabalhador para conduzir as operações de escrita e quatro fios de trabalhador para conduzir As operações de leitura nos discos. Os trabalhadores da Write estão a conduzir o tráfego no volume "nocache", que tem 10 discos com cache definido para "Nenhum". Os trabalhadores do Read estão a conduzir o tráfego no volume "readcache", que tem um disco com cache definido para "ReadOnly".

#### <a name="maximum-write-iops"></a>IOPS de escrita máxima

Crie o ficheiro de trabalho com as seguintes especificações para obter o máximo De Escrever IOPS. Nomeie-o "fiowrite.ini".

```ini
[global]
size=30g
direct=1
iodepth=256
ioengine=libaio
bs=8k

[writer1]
rw=randwrite
directory=/mnt/nocache
[writer2]
rw=randwrite
directory=/mnt/nocache
[writer3]
rw=randwrite
directory=/mnt/nocache
[writer4]
rw=randwrite
directory=/mnt/nocache
```

Note as seguintes coisas-chave que estão em consonância com as diretrizes de design discutidas em secções anteriores. Estas especificações são essenciais para impulsionar o máximo de IOPS,  

* Uma alta profundidade de fila de 256.  
* Um pequeno bloco de 8 KB.  
* Vários fios executando escritos aleatórios.

Executar o seguinte comando para iniciar o teste FIO por 30 segundos,  

```
sudo fio --runtime 30 fiowrite.ini
```

Enquanto o teste corre, você é capaz de ver o número de iOPS de escrita que os discos VM e Premium estão a entregar. Como mostra a amostra abaixo, o DS14 VM está a entregar o seu limite máximo de IOPS de 50.000 IOPS.  
    ![Número de discos IOPS VM e Premium de escrita estão a entregar](../articles/virtual-machines/linux/media/premium-storage-performance/image11.png)

#### <a name="maximum-read-iops"></a>IOPS de leitura máxima

Crie o ficheiro de trabalho com as seguintes especificações para obter o máximo DeIOpS de Leitura. Nomeie-o "fioread.ini".

```ini
[global]
size=30g
direct=1
iodepth=256
ioengine=libaio
bs=8k

[reader1]
rw=randread
directory=/mnt/readcache
[reader2]
rw=randread
directory=/mnt/readcache
[reader3]
rw=randread
directory=/mnt/readcache
[reader4]
rw=randread
directory=/mnt/readcache
```

Note as seguintes coisas-chave que estão em consonância com as diretrizes de design discutidas em secções anteriores. Estas especificações são essenciais para impulsionar o máximo de IOPS,

* Uma alta profundidade de fila de 256.  
* Um pequeno bloco de 8 KB.  
* Vários fios executando escritos aleatórios.

Executar o seguinte comando para iniciar o teste FIO por 30 segundos,

```
sudo fio --runtime 30 fioread.ini
```

Enquanto o teste corre, você é capaz de ver o número de iOPS lidos que os discos VM e Premium estão a entregar. Como mostra a amostra abaixo, o DS14 VM está a entregar mais de 64.000 IOPS de leitura. Esta é uma combinação do disco e do desempenho da cache.  
    ![](../articles/virtual-machines/linux/media/premium-storage-performance/image12.png)

#### <a name="maximum-read-and-write-iops"></a>Leitura máxima e escrita IOPS

Crie o ficheiro de trabalho com as seguintes especificações para obter o máximo combinado Ler e Escrever IOPS. Nomeie-o "fioreadwrite.ini".

```ini
[global]
size=30g
direct=1
iodepth=128
ioengine=libaio
bs=4k

[reader1]
rw=randread
directory=/mnt/readcache
[reader2]
rw=randread
directory=/mnt/readcache
[reader3]
rw=randread
directory=/mnt/readcache
[reader4]
rw=randread
directory=/mnt/readcache

[writer1]
rw=randwrite
directory=/mnt/nocache
rate_iops=12500
[writer2]
rw=randwrite
directory=/mnt/nocache
rate_iops=12500
[writer3]
rw=randwrite
directory=/mnt/nocache
rate_iops=12500
[writer4]
rw=randwrite
directory=/mnt/nocache
rate_iops=12500
```

Note as seguintes coisas-chave que estão em consonância com as diretrizes de design discutidas em secções anteriores. Estas especificações são essenciais para impulsionar o máximo de IOPS,

* Uma alta profundidade de fila de 128.  
* Um pequeno bloco de 4 KB.  
* Vários fios executando leituras aleatórias e escritos.

Executar o seguinte comando para iniciar o teste FIO por 30 segundos,

```
sudo fio --runtime 30 fioreadwrite.ini
```

Enquanto o teste corre, você é capaz de ver o número de combinados ler e escrever IOPS que os discos VM e Premium estão entregando. Como mostra a amostra abaixo, o DS14 VM está a entregar mais de 100.000 IOPS combinados de leitura e escrita. Esta é uma combinação do disco e do desempenho da cache.  
    ![Leia e escreva IOPS combinado](../articles/virtual-machines/linux/media/premium-storage-performance/image13.png)

#### <a name="maximum-combined-throughput"></a>O máximo de entrada combinada

Para obter o máximo combinado Ler e Escrever A Utilização, utilize um tamanho de bloco maior e uma grande profundidade de fila com vários fios que executam leituras e escritos. Pode utilizar um bloco de 64 KB e uma profundidade de fila de 128.
