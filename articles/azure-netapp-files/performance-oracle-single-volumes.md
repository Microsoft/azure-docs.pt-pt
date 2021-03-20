---
title: Desempenho da base de dados da Oracle no Azure NetApp Files volume único | Microsoft Docs
description: Descreve os resultados dos testes de desempenho de um volume único dos Ficheiros Azure NetApp na base de dados da Oracle.
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
ms.date: 09/30/2020
ms.author: b-juche
ms.openlocfilehash: c6cdf2f6dada0aa4dea2f70f18237b7ee39e3ea1
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "91571414"
---
# <a name="oracle-database-performance-on-azure-netapp-files-single-volumes"></a>Desempenho da base de dados Oracle em volumes individuais do Azure NetApp Files

Este artigo aborda os seguintes tópicos sobre o Oráculo na nuvem. Estes tópicos podem ser de particular interesse para um administrador de base de dados, arquiteto de nuvem ou arquiteto de armazenamento:   

* Quando conduz uma carga de trabalho de processamento de transações on-line (OLTP) (principalmente e/I)aleatória) ou uma carga de trabalho de processamento analítico online (OLAP) (principalmente sequencial I/O), como é o desempenho?   
* Qual é a diferença de desempenho entre o cliente regular do Kernel NFS (kNFS) e o cliente Direct NFS da Oracle?
* No que diz respeito à largura de banda, o desempenho de um único volume de Ficheiros Azure NetApp é suficiente?

## <a name="testing-environment-and-components"></a>Ambiente e componentes de teste

O diagrama a seguir ilustra o ambiente utilizado para os testes. Para a consistência e simplicidade, os livros de reprodução ansible foram usados para implantar todos os elementos da cama de ensaio.

![Ambiente de testes oráculo](../media/azure-netapp-files/performance-oracle-test-environment.png)  

### <a name="virtual-machine-configuration"></a>Configuração de máquina virtual

Os testes utilizaram a seguinte configuração para a máquina virtual:
* Sistema Operativo:   
    RedHat Enterprise Linux 7.8 (wle-ora01)
* Tipos de instância:   
    Dois modelos foram utilizados em testes – D32s_v3 e D64s_v3
* Contagem de interface de rede:   
    Um (1) colocado na sub-rede 3  
* Discos:   
    Binários oráculos e SO foram colocados num único disco premium

### <a name="azure-netapp-files-configuration"></a><a name="anf_config"></a>Configuração de ficheiros Azure NetApp
Os testes utilizaram a seguinte configuração de Ficheiros Azure NetApp:   

* Tamanho da piscina de capacidade:  
    Vários tamanhos da piscina foram configurados: 4 TiB, 8 TiB, 16 TiB, 32 TiB 
* Nível de serviço:  
    Ultra (128 MiB/s de largura de banda por 1 TiB da capacidade de volume atribuída)
* Volumes:  
    Foram avaliados testes de volume um e dois

### <a name="workload-generator"></a>Gerador de carga de trabalho 

Os testes utilizados geraram SLOB 2.5.4.2. SLOB (Silly Little Oracle Benchmark) é um conhecido gerador de carga de trabalho no espaço Oráculo projetado para stressar e testar o subsistema de I/O com uma carga de trabalho física de E/O tamponada SGA.  

O SLOB 2.5.4.2 não suporta a base de dados pluggable (PDB). Como tal, foi adicionada uma alteração aos `setup.sh` scripts e `runit.sh` scripts para adicionar suporte PDB.  

As variáveis SLOB utilizadas nos ensaios são descritas nas seguintes secções.

#### <a name="workload-80-select-20-update--random-io--slobconf-variables"></a>Carga de trabalho 80% SELECT, 20% UPDATE | I/O aleatório – `slob.conf` variáveis   

`UPDATE_PCT=20`   
`SCAN_PCT=0`   
`RUN_TIME=600`   
`WORK_LOOP=0`   
`SCALE=75G`   
`SCAN_TABLE_SZ=50G`   
`WORK_UNIT=64`   
`REDO_STRESS=LITE`   
`LOAD_PARALLEL_DEGREE=12`   

#### <a name="workload-100-select--sequential-io--slobconf-variables"></a>Carga de trabalho 100% SELECT | I/O sequencial – `slob.conf` variáveis

`UPDATE_PCT=0`   
`SCAN_PCT=100`   
`RUN_TIME=600`   
`WORK_LOOP=0`   
`SCALE=75G`   
`SCAN_TABLE_SZ=50G`   
`WORK_UNIT=64`   
`REDO_STRESS=LITE`   
`LOAD_PARALLEL_DEGREE=12`   

### <a name="database"></a>Base de Dados

A versão Oráculo utilizada para os testes é Oracle Database Enterprise Edition 19.3.0.0.

Os parâmetros Oráculo são os seguintes:  
* `sga_max_size`: 4096M
* `sga_target`: 4096
* `db_writer_processes`: 12
* `awr_pdb_autoflush_enabled`: verdadeiro
* `filesystemio_options`: SETALL
* `log_buffer`: 134217728

Foi criado um PDB para a base de dados SLOB.

O diagrama seguinte mostra o espaço de mesa chamado PERFIO com 600 GB de tamanho (20 ficheiros de dados, 30 GB cada) criados para acolher quatro esquemas de utilizador SLOB. Cada esquema de utilizador tinha 125 GB de tamanho.

![Base de dados Oracle](../media/azure-netapp-files/performance-oracle-tablespace.png)  

## <a name="performance-metrics"></a>Métricas de desempenho

O objetivo era reportar o desempenho do OI como experimentado pela aplicação. Portanto, todos os diagramas deste artigo utilizam métricas relatadas pela base de dados da Oracle através dos seus relatórios de Repositório automático de Carga de Trabalho (AWR). As métricas utilizadas nos diagramas são as seguintes:   

* **Pedidos/seg médio de IO**   
    Corresponde à soma de pedidos de IO de leitura média/seg e pedidos médios de IO de escrita/seg da secção de perfil de carga
* **IO médio MB/seg**   
    Corresponde à soma da média de Leitura IO MB/seg e da média de escrita IO MB/seg da secção de perfil de carga
* **Média De Leitura da latência**   
    Corresponde à latência média do Oracle Wait Event "db file sequencial read" em microsegundos
* **Número de fios/esquema**   
    Corresponde ao número de fios SLOB por esquema de utilizador

## <a name="performance-measurement-results"></a>Resultados da medição do desempenho  

Esta secção descreve os resultados da medição do desempenho.

### <a name="linux-knfs-client-vs-oracle-direct-nfs"></a>Linux kNFS Cliente vs. Oracle Direct NFS

Este cenário estava a decorrer num Standard_D32s_v3 VM Azure (Intel E5-2673 v4 @ 2.30 GHz). A carga de trabalho é de 75% SELECT e 25% UPDATE, na sua maioria I/O aleatório, e com um amortecedor de base de dados atingido por ~7,5%. 

Como mostrado no diagrama seguinte, o cliente Da Oracle DNFS entregou até 2,8x mais produção do que o Cliente Linux kNFS normal:  

![Linux kNFS Cliente em comparação com Oracle Direct NFS](../media/azure-netapp-files/performance-oracle-kfns-compared-dnfs.png)  

O diagrama seguinte mostra a curva de latência para as operações de leitura. Neste contexto, o estrangulamento para o cliente kNFS é a única ligação de tomada NFS TCP estabelecida entre o cliente e o servidor NFS (o volume de Ficheiros Azure NetApp).  

![Linux kNFS Cliente em comparação com a curva de latência NFS da Oracle Direct](../media/azure-netapp-files/performance-oracle-latency-curve.png)  

O cliente DNFS foi capaz de empurrar mais pedidos de IO/seg devido à sua capacidade de criar centenas de ligações de tomadaS TCP, tirando assim partido do paralelismo. Conforme descrito na [configuração dos Ficheiros Azure NetApp](#anf_config), cada TiB adicional de capacidade atribuída permite mais 128MiB/s de largura de banda. DNFS superou em 1 GiB/s de produção, que é o limite imposto pela seleção de capacidade de 8-TiB. Dada mais capacidade, mais produção teria sido impulsionada.

A produção é apenas uma das considerações. Outra consideração é a latência, que tem o principal impacto na experiência do utilizador. Como mostra o diagrama seguinte, os aumentos de latência podem ser esperados muito mais rapidamente com kNFS do que com DNFS. 

![Linux kNFS Cliente em comparação com Oracle Direct NFS ler latência](../media/azure-netapp-files/performance-oracle-read-latency.png)  

Os histogramas fornecem uma excelente visão sobre as latências da base de dados. O diagrama seguinte fornece uma visão completa do ponto de vista da "leitura sequencial do ficheiro db", ao mesmo tempo que utiliza o DNFS no ponto de dados de maior concordância (32 fios/esquema). Como mostrado no diagrama seguinte, 47% de todas as operações de leitura foram homenageadas entre 512 microsegundos e 1000 microsegundos, enquanto 90% de todas as operações de leitura foram servidas com uma latência inferior a 2 ms.

![Linux kNFS Cliente em comparação com histogramas NFS da Oracle Direct](../media/azure-netapp-files/performance-oracle-histogram-read-latency.png)  

Em conclusão, é claro que o DNFS é um must-have quando se trata de melhorar o desempenho de uma placa de base de dados da Oracle na NFS.

### <a name="single-volume-performance-limits"></a>Limites de desempenho de volume único

Esta secção descreve os limites de desempenho de um único volume com I/O aleatório e I/O sequencial. 

#### <a name="random-io"></a>I/O aleatório

A DNFS é capaz de consumir muito mais largura de banda do que a fornecida por uma quota de desempenho de 8-TB Azure NetApp Files. Ao aumentar a capacidade de volume dos Ficheiros Azure NetApp para 16 TiB, o que constitui uma alteração instantânea, a quantidade de largura de banda de volume aumentou de 1024 MiB/s em 2X para 2048 MiB/s. 

O diagrama seguinte mostra uma configuração para uma carga de trabalho de atualização de 80% e 20% de atualização, e com um rácio de sucesso de tampão de base de dados de 8%. A SLOB conseguiu conduzir um único volume para 200.000 pedidos de NFS I/O por segundo. Tendo em conta que cada operação tem o tamanho de 8 KiB, o sistema em teste foi capaz de entregar ~200.000 pedidos de OI/seg ou 1600 MiB/s.
 
![Produção de DNFS da Oracle](../media/azure-netapp-files/performance-oracle-dnfs-throughput.png)  

O diagrama da curva de latência seguinte mostra que, à medida que a produção de leitura aumenta, a latência aumenta suavemente abaixo da linha de 1-ms, e atinge o joelho da curva em ~165.000 pedidos de leitura média de IO/sec na latência média de leitura de ~1,3 ms.  Este valor é um valor incrível de latência para uma taxa de I/O inalcançável com quase todas as outras tecnologias na Nuvem Azure. 

![Curva de latência do Oráculo DNFS](../media/azure-netapp-files/performance-oracle-dnfs-latency-curve.png)  

#### <a name="sequential-io"></a>I/O sequencial  

Como mostrado no diagrama seguinte, nem todos os I/S são de natureza aleatória, considerando uma cópia de segurança RMAN ou uma tomografia completa, por exemplo, como cargas de trabalho que requerem o máximo de largura de banda que puderem obter.  Utilizando a mesma configuração descrita anteriormente, mas com o volume redimensionado para 32 TiB, o diagrama seguinte mostra que uma única instância Oráculo DB pode conduzir acima de 3.900 MB/s de produção, muito perto da quota de desempenho do volume de 32 TB (128 MB/s * 32 = 4096 4096 MB/sMB).

![Oráculo DNFS I/O](../media/azure-netapp-files/performance-oracle-dnfs-io.png)  

Em resumo, o Azure NetApp Files ajuda-o a levar as bases de dados da Oracle para a nuvem. Apresenta desempenho quando a base de dados o exige. Pode redimensionar dinamicamente e não disruptivamente a sua quota de volume a qualquer momento.

## <a name="next-steps"></a>Passos seguintes

- [Recomendações do teste da referência de desempenho do Azure NetApp Files](azure-netapp-files-performance-metrics-volumes.md)
- [Testes de referência de desempenho para Linux](performance-benchmarks-linux.md)