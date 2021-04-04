---
title: incluir ficheiro
description: incluir ficheiro
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 01/29/2021
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 25404837d5bc66ff415be8d8670eb6650475c30f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "99094667"
---
## <a name="warm-up-the-cache"></a>Aqueça a Cache

O disco com o cache do anfitrião ReadOnly é capaz de dar IOPS mais alto do que o limite do disco. Para obter este desempenho de leitura máxima da cache do anfitrião, primeiro deve aquecer a cache deste disco. Isto garante que os IOs de Leitura que a ferramenta de benchmarking irá conduzir no volume CacheReads, realmente atinge a cache, e não o disco diretamente. Os hits de cache resultam em mais IOPS do disco ativado por cache única.

> [!IMPORTANT]
> Tem de aquecer a cache antes de executar os parâmetros de referência sempre que o VM for reiniciado.

## <a name="diskspd"></a>DISKSPD

[Descarregue a ferramenta DISKSP](https://github.com/Microsoft/diskspd) no VM. DISKSPD é uma ferramenta que pode personalizar para criar as suas próprias cargas de trabalho sintéticas. Usaremos a mesma configuração descrita acima para executar testes de benchmarking. Pode alterar as especificações para testar diferentes cargas de trabalho.

Neste exemplo, utilizamos o seguinte conjunto de parâmetros de base:

- -c200G: Cria (ou recria) o ficheiro de amostra utilizado no ensaio. Pode ser configurado em bytes, KiB, MiB, GiB, ou blocos. Neste caso, um grande ficheiro de 200-GiB é usado para minimizar o caching de memória.
- -w100: Especifica a percentagem de operações que são pedidos de escrita (-w0 é equivalente a 100% de leitura).
- -b4K: Indica o tamanho do bloco em bytes, KiB, MiB ou GiB. Neste caso, o tamanho do bloco 4K é usado para simular um teste de E/S aleatório.
- -F4: Define um total de quatro fios.
- -r: Indica o teste de E/S aleatório (sobrepõe-se ao parâmetro-s).
- -o128: Indica o número de pedidos de E/S pendentes por alvo por fio. Isto também é conhecido como a profundidade da fila. Neste caso, 128 é usado para stressar o CPU.
- -W7200: Especifica a duração do tempo de aquecimento antes de as medições começarem.
- -d30: Especifica a duração do ensaio, sem incluir o aquecimento.
- -Sh: Desativar o software e o hardware escrever caching (equivalente a -Suw).

Para obter uma lista completa de parâmetros, consulte o [repositório GitHub.](https://github.com/Microsoft/diskspd/wiki/Command-line-and-parameters)

### <a name="maximum-write-iops"></a>IOPS de escrita máxima
Usamos uma alta profundidade de fila de 128, um pequeno bloco de 8 KB, e quatro fios de trabalho para dirigir operações de escrita. Os trabalhadores da escrita estão a conduzir o tráfego no volume "NoCacheWrites", que tem três discos com cache definido para "Nenhum".

Executar o seguinte comando durante 30 segundos de aquecimento e 30 segundos de medição:

`diskspd -c200G -w100 -b8K -F4 -r -o128 -W30 -d30 -Sh testfile.dat`

Os resultados mostram que o Standard_D8ds_v4 VM está a entregar o seu limite máximo de 12.800 IOPS de escrita.

:::image type="content" source="../articles/virtual-machines/linux/media/premium-storage-performance/disks-benchmarks-diskspd-max-write-io-per-second.png" alt-text="Para 3208642560 bytes totais, máximo total I/Os de 391680, com um total de 101,97 MiB/s, e um total de 13052,65 I/O por segundo.":::

### <a name="maximum-read-iops"></a>IOPS de leitura máxima

Usamos uma alta profundidade de fila de 128, um pequeno bloco de quatro KB, e quatro fios de trabalho para dirigir operações de leitura. Os trabalhadores de leitura estão a conduzir o tráfego no volume "CacheReads", que tem um disco com cache definido para "ReadOnly".

Executar o seguinte comando durante duas horas de aquecimento e 30 segundos de medição:

`diskspd -c200G -b4K -F4 -r -o128 -W7200 -d30 -Sh testfile.dat`

Os resultados mostram que o Standard_D8ds_v4 VM está a entregar o seu limite máximo de 77.000 IOPS de leitura máxima.

:::image type="content" source="../articles/virtual-machines/linux/media/premium-storage-performance/disks-benchmarks-diskspd-max-read-io-per-second.png" alt-text="Para o total de 9652785152, foram 2356637 total de I/Os, em 306,72 mib/s totais, e um total de 78521,23 I/Os por segundo.":::

### <a name="maximum-throughput"></a>Débito máximo

Para obter a produção máxima de leitura e escrita, pode mudar para um bloco maior de 64 KB.
## <a name="fio"></a>FIO

A FIO é uma ferramenta popular para comparar o armazenamento nos VMs Linux. Tem a flexibilidade para selecionar diferentes tamanhos de IO, leituras sequenciais ou aleatórias e escreve. Cria fios ou processos de trabalhador para realizar as operações de E/S especificadas. Pode especificar o tipo de operações de E/S que cada fio de trabalho deve efetuar utilizando ficheiros de trabalho. Criámos um ficheiro de trabalho por cenário ilustrado nos exemplos abaixo. Pode alterar as especificações destes ficheiros de trabalho para comparar diferentes cargas de trabalho em execução no Armazenamento Premium. Nos exemplos, estamos a usar um Standard_D8ds_v4 **ubuntu.** Utilize a mesma configuração descrita no início da secção de referência e aqueça a cache antes de escorrê-la nos testes de referência.

Antes de começar, [faça o download da FIO](https://github.com/axboe/fio) e instale-o na sua máquina virtual.

Executar o seguinte comando para Ubuntu,

```
apt-get install fio
```

Usamos quatro fios de trabalhador para dirigir operações de escrita e quatro fios de trabalho para dirigir operações de leitura nos discos. Os trabalhadores da escrita estão a conduzir o tráfego no volume "nocache", que tem três discos com cache definido para "Nenhum". Os trabalhadores de leitura estão a conduzir o tráfego no volume de "readcache", que tem um disco com cache definido para "ReadOnly".

### <a name="maximum-write-iops"></a>IOPS de escrita máxima

Crie o ficheiro de trabalho com as seguintes especificações para obter o máximo de IOPS de escrita. Diga-lhe "fiowrite.ini".

```ini
[global]
size=30g
direct=1
iodepth=256
ioengine=libaio
bs=4k
numjobs=4

[writer1]
rw=randwrite
directory=/mnt/nocache
```

Note as coisas-chave que estão em conformidade com as diretrizes de design discutidas em secções anteriores. Estas especificações são essenciais para impulsionar o máximo de IOPS,  

* Uma alta profundidade de fila de 256.  
* Um pequeno bloco tamanho de 4 KB.  
* Múltiplos fios executando escritas aleatórias.

Executar o seguinte comando para iniciar o teste FIO por 30 segundos,  

```
sudo fio --runtime 30 fiowrite.ini
```

Enquanto o teste decorre, é possível ver o número de IOPS de escrita que os discos VM e Premium estão a entregar. Como mostrado na amostra abaixo, o Standard_D8ds_v4 VM está a entregar o seu limite máximo de 12.800 IOPS de escrita.  
    :::image type="content" source="../articles/virtual-machines/linux/media/premium-storage-performance/fio-uncached-writes-1.jpg" alt-text="Número de VM de escrita e SSDs premium estão a ser entregues, mostra que as escritas são de 13,1 k IOPS.":::

### <a name="maximum-read-iops"></a>IOPS de leitura máxima

Crie o ficheiro de trabalho com as seguintes especificações para obter o máximo de IOPS de leitura. Diga-lhe "fioread.ini".

```ini
[global]
size=30g
direct=1
iodepth=256
ioengine=libaio
bs=4k
numjobs=4

[reader1]
rw=randread
directory=/mnt/readcache
```

Note as coisas-chave que estão em conformidade com as diretrizes de design discutidas em secções anteriores. Estas especificações são essenciais para impulsionar o máximo de IOPS,

* Uma alta profundidade de fila de 256.  
* Um pequeno bloco tamanho de 4 KB.  
* Múltiplos fios executando escritas aleatórias.

Executar o seguinte comando para iniciar o teste FIO por 30 segundos,

```
sudo fio --runtime 30 fioread.ini
```

Enquanto o teste decorre, é possível ver o número de IOPS lidos que os discos VM e Premium estão a entregar. Como mostrado na amostra abaixo, o Standard_D8ds_v4 VM está a entregar mais de 77.000 IOPS de leitura. Esta é uma combinação do disco e do desempenho da cache.  
    :::image type="content" source="../articles/virtual-machines/linux/media/premium-storage-performance/fio-cached-reads-1.jpg" alt-text="Screenshot do número de VM de escrita e SSDs premium estão a entregar, mostra que as leituras são de 78,6k.":::

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
rate_iops=3200
```

Note as coisas-chave que estão em conformidade com as diretrizes de design discutidas em secções anteriores. Estas especificações são essenciais para impulsionar o máximo de IOPS,

* Uma alta profundidade de fila de 128.  
* Um pequeno bloco tamanho de 4 KB.  
* Múltiplos fios executando leituras e escritas aleatórias.

Executar o seguinte comando para iniciar o teste FIO por 30 segundos,

```
sudo fio --runtime 30 fioreadwrite.ini
```

Enquanto o teste decorre, é possível ver o número de leitura combinada e escrever IOPS que os discos VM e Premium estão a entregar. Como mostrado na amostra abaixo, o Standard_D8ds_v4 VM está a entregar mais de 90.000 IOPS combinados de leitura e escrita. Esta é uma combinação do disco e do desempenho da cache.  
    :::image type="content" source="../articles/virtual-machines/linux/media/premium-storage-performance/fio-both-1.jpg" alt-text="Leitura combinada e escrita IOPS, mostra que as leituras são de 78.3k e as escritas são 12.6k IOPS.":::

### <a name="maximum-combined-throughput"></a>Produção combinada máxima

Para obter o máximo combinado De ler e escrever, use um tamanho de bloco maior e uma grande profundidade de fila com múltiplos fios executando leituras e escritas. Pode utilizar um bloco de 64 KB e uma profundidade de fila de 128.