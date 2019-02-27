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
ms.openlocfilehash: 9c59b98fb615266c193f997c01c83922c18d4408
ms.sourcegitcommit: 24906eb0a6621dfa470cb052a800c4d4fae02787
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/27/2019
ms.locfileid: "56890942"
---
*Aquecer a Cache*  
O disco com o anfitrião só de leitura de colocação em cache são capaz de lhe dar mais elevada IOPS que o limite de disco. Para obter este máximo desempenho de leitura da cache do anfitrião, primeiro deve entender o cache do disco. Isto garante que a leitura do IOs que a ferramenta de benchmark irá orientar no volume de CacheReads, na verdade, acessa o cache e não o disco diretamente. O resultado de acertos na cache em ESP adicionais da cache única ativada disco.

> [!IMPORTANT]
> Deve entender o cache antes de executar o benchmark, sempre que a VM é reiniciada.

## <a name="tools"></a>Ferramentas

### <a name="iometer"></a>Iometer

[Transferir a ferramenta de Iometer](http://sourceforge.net/projects/iometer/files/iometer-stable/2006-07-27/iometer-2006.07.27.win32.i386-setup.exe/download) na VM.

#### <a name="test-file"></a>Ficheiro de teste

Iometer utiliza um ficheiro de teste que é armazenado no volume no qual executa o teste de benchmark. Ele unidades de leituras e escritas neste ficheiro de teste para medir o disco IOPS e débito. Iometer cria este ficheiro de teste, se não tiver fornecido um. Crie um ficheiro de teste de 200 GB chamado iobw.tst nos volumes CacheReads e NoCacheWrites.

#### <a name="access-specifications"></a>Especificações de acesso

As especificações, pedir o tamanho de e/s, % de leitura/escrita, % aleatório/sequenciais são configurados utilizando o separador "Especificações de acesso" no Iometer. Crie uma especificação de acesso para cada um dos cenários descritos abaixo. Criar as especificações de acesso e "Salvar" apropriadas com nome, como – RandomWrites\_8K, RandomReads\_8 K. Selecione a especificação correspondente ao executar o cenário de teste.

Um exemplo de especificações de acesso para o cenário de escrever o IOPS máximo é mostrado abaixo,  
    ![Exemplo de especificações de acesso para o máximo de IOPS de escrita](../articles/virtual-machines/linux/media/premium-storage-performance/image8.png)

#### <a name="maximum-iops-test-specifications"></a>Especificações de teste de IOPS máximo

Para demonstrar as IOPs máximas, utilize o tamanho mais pequeno do pedido. Utilize o tamanho do pedido de 8K e criar especificações para escritas aleatórias e leituras.

| Especificação de acesso | Tamanho do pedido | % Aleatório | % De leitura |
| --- | --- | --- | --- |
| RandomWrites\_8K |8K |100 |0 |
| RandomReads\_8K |8K |100 |100 |

#### <a name="maximum-throughput-test-specifications"></a>Especificações de teste de débito máximo

Para demonstrar o débito máximo, utilize o maior tamanho do pedido. Utilize o tamanho do pedido de 64K e criar especificações para escritas aleatórias e leituras.

| Especificação de acesso | Tamanho do pedido | % Aleatório | % De leitura |
| --- | --- | --- | --- |
| RandomWrites\_64K |64 K |100 |0 |
| RandomReads\_64K |64 K |100 |100 |

#### <a name="run-the-iometer-test"></a>Execute o teste de Iometer

Execute os passos abaixo para entender cache

1. Criar duas especificações de acesso com valores apresentados abaixo,

   | Name | Tamanho do pedido | % Aleatório | % De leitura |
   | --- | --- | --- | --- |
   | RandomWrites\_1 MB |1 MB |100 |0 |
   | RandomReads\_1MB |1 MB |100 |100 |
1. Execute o teste de Iometer para inicializar o disco de cache com os parâmetros seguintes. Utilize três threads de trabalho para o volume de destino e uma profundidade de fila de 128. Definir a duração do teste de "Tempo de execução" para 2 horas, no separador "Configuração de teste".

   | Cenário | Volume de destino | Name | Duração |
   | --- | --- | --- | --- |
   | Inicialize o disco de Cache |CacheReads |RandomWrites\_1 MB |2 horas |
1. Execute o teste de Iometer para aquecer o disco de cache com os parâmetros seguintes. Utilize três threads de trabalho para o volume de destino e uma profundidade de fila de 128. Definir a duração do teste de "Tempo de execução" para 2 horas, no separador "Configuração de teste".

   | Cenário | Volume de destino | Name | Duração |
   | --- | --- | --- | --- |
   | Quente no disco de Cache |CacheReads |RandomReads\_1MB |2 horas |

Depois do disco de cache é preparado, prossiga com os cenários de teste listados abaixo. Para executar o teste de Iometer, utilize, pelo menos, três threads de trabalho para **cada** volume de destino. Para cada thread de trabalho, selecione o volume de destino, defina a profundidade de fila e selecione uma das especificações de teste guardado, conforme mostrado na tabela abaixo, para executar o cenário de teste correspondente. A tabela também mostra os resultados esperados para IOPS e débito ao executar estes testes. Para todos os cenários, são utilizados um tamanho de e/s pequeno de 8 KB e uma profundidade de fila de alta de 128.

| Cenário de teste | Volume de destino | Name | Resultado |
| --- | --- | --- | --- |
| Um máximo de IOPS de leitura |CacheReads |RandomWrites\_8K |50,000 IOPS |
| Um máximo de IOPS de escrita |NoCacheWrites |RandomReads\_8K |64,000 IOPS |
| Um máximo de IOPS combinada |CacheReads |RandomWrites\_8K |100 000 IOPS |
| NoCacheWrites |RandomReads\_8K | &nbsp; | &nbsp; |
| Um máximo de MB/seg de leitura |CacheReads |RandomWrites\_64K |524 MB/seg |
| Um máximo de MB/s de escrita |NoCacheWrites |RandomReads\_64K |524 MB/seg |
| Combinado MB/seg |CacheReads |RandomWrites\_64K |1000 MB/seg |
| NoCacheWrites |RandomReads\_64K | &nbsp; | &nbsp; |

Seguem-se capturas de ecrã do Iometer resultados do teste para cenários IOPS e débito combinados.

#### <a name="combined-reads-and-writes-maximum-iops"></a>Combinados lê e escreve o IOPS máximo

![IOPS máximo combinado de leituras e gravações](../articles/virtual-machines/linux/media/premium-storage-performance/image9.png)

#### <a name="combined-reads-and-writes-maximum-throughput"></a>Combinados lê e escreve o débito máximo

![Débito máximo combinado de leituras e gravações](../articles/virtual-machines/linux/media/premium-storage-performance/image10.png)

### <a name="fio"></a>FIO

FIO é uma ferramenta popular para o armazenamento de benchmark em VMs do Linux. Ele tem a flexibilidade para selecionar diferentes tamanhos de e/s, seqüenciais ou leituras aleatórias e escreve. Ele gera threads de trabalho ou processos para efetuar as operações de e/s especificadas. Pode especificar o tipo de operações de e/s, que cada thread de trabalho tem de efetuar o uso de arquivos de tarefa. Criamos um ficheiro de tarefa por cenário ilustrado nos exemplos abaixo. Pode alterar as especificações nesses arquivos de tarefa para diferentes cargas de trabalho em execução no armazenamento Premium. Os exemplos, estamos a utilizar uma execução de VM de 14 de DS Standard **Ubuntu**. Use a mesma configuração descrita no início da seção Benchmarking e disponibilize a cache antes de executar os testes de benchmark.

Antes de começar, [transferir FIO](https://github.com/axboe/fio) e instalá-lo na sua máquina virtual.

Execute o seguinte comando para Ubuntu,

```
apt-get install fio
```

Utilizamos o quatro threads de trabalho para orientar operações de escrita e quatro segmentos de trabalho para que as operações de leitura nos discos. Os operadores de escrita estão a originar tráfego no volume "nocache", que tem 10 discos com cache definido como "Nenhum". Os operadores de leitura estão a originar tráfego no volume "readcache", que possui um disco com o conjunto de cache para "Só de leitura".

#### <a name="maximum-write-iops"></a>Máximo IOPS de escrita

Crie o ficheiro de tarefa com especificações seguintes para obter o máximo IOPS de escrita. Nomeie-o "fiowrite.ini".

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

Tenha em atenção as siga principais informações que estão de acordo com as diretrizes de design abordadas nas secções anteriores. Nestas especificações são essenciais para orientar o IOPS máximo,  

* Uma profundidade de fila de alta de 256.  
* Um tamanho de pequeno bloco de 8 KB.  
* Vários threads para efetuar operações de escrita aleatórias.

Execute o seguinte comando para iniciar o teste FIO durante 30 segundos,  

```
sudo fio --runtime 30 fiowrite.ini
```

Enquanto o teste é executada, são capazes de ver o número de escrever IOPS a VM e fornecendo os discos Premium. Conforme mostrado no exemplo abaixo, a VM DS14 está a fornecer a máxima limite de IOPS de 50 000 IOPS de escrita.  
    ![Número de escrita fornecendo discos de VM de IOPS e Premium](../articles/virtual-machines/linux/media/premium-storage-performance/image11.png)

#### <a name="maximum-read-iops"></a>Máximo IOPS de leitura

Crie o ficheiro de tarefa com especificações seguintes para obter o máximo IOPS de leitura. Nomeie-o "fioread.ini".

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

Tenha em atenção as siga principais informações que estão de acordo com as diretrizes de design abordadas nas secções anteriores. Nestas especificações são essenciais para orientar o IOPS máximo,

* Uma profundidade de fila de alta de 256.  
* Um tamanho de pequeno bloco de 8 KB.  
* Vários threads para efetuar operações de escrita aleatórias.

Execute o seguinte comando para iniciar o teste FIO durante 30 segundos,

```
sudo fio --runtime 30 fioread.ini
```

Enquanto o teste é executada, são capazes de ver o número de ler IOPS a VM e fornecendo os discos Premium. Conforme mostrado no exemplo abaixo, a VM DS14 está a fornecer mais de 64.000 IOPS de leitura. Esta é uma combinação de disco e o desempenho da cache.  
    ![](../articles/virtual-machines/linux/media/premium-storage-performance/image12.png)

#### <a name="maximum-read-and-write-iops"></a>Máximo de leitura e escrita de IOPS

Criar o ficheiro de tarefa com o seguinte combinado de especificações para obter o máximo IOPS de escrita e leitura. Nomeie-o "fioreadwrite.ini".

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

Tenha em atenção as siga principais informações que estão de acordo com as diretrizes de design abordadas nas secções anteriores. Nestas especificações são essenciais para orientar o IOPS máximo,

* Uma profundidade de fila de alta de 128.  
* Um tamanho de pequeno bloco de 4 KB.  
* Leituras e escritas de vários threads que executam aleatório.

Execute o seguinte comando para iniciar o teste FIO durante 30 segundos,

```
sudo fio --runtime 30 fioreadwrite.ini
```

Enquanto executa o teste, pode ver o número de leitura combinado e IOPS de escrita da VM e fornecendo os discos Premium. Conforme mostrado no exemplo abaixo, a VM DS14 está a fornecer mais de 100 000 leitura combinado e IOPS de escrita. Esta é uma combinação de disco e o desempenho da cache.  
    ![Combinado de leitura e de IOPS de escrita](../articles/virtual-machines/linux/media/premium-storage-performance/image13.png)

#### <a name="maximum-combined-throughput"></a>Débito máximo de combinada

Para obter o máximo combinado de leitura e escrita a taxa de transferência, utilize um tamanho de bloco maior e a profundidade de fila grandes com vários threads, execução de leituras e gravações. Pode utilizar um tamanho de bloco de 64 KB e a profundidade de fila de 128.
