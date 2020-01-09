---
title: incluir ficheiro
description: incluir ficheiro
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 07/08/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 32c1ca95c01edec74f22fc051e453f2ac0dbd03f
ms.sourcegitcommit: 5925df3bcc362c8463b76af3f57c254148ac63e3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/31/2019
ms.locfileid: "75564809"
---
## <a name="application-performance-indicators"></a>Indicadores de desempenho do aplicativo

Avaliamos se um aplicativo está funcionando bem ou não usando indicadores de desempenho como, com que velocidade um aplicativo está processando uma solicitação de usuário, quantos dados um aplicativo está processando por solicitação, quantas solicitações é um processamento de aplicativo em um específico período, por quanto tempo um usuário precisa esperar para obter uma resposta depois de enviar a solicitação. Os termos técnicos para esses indicadores de desempenho são, IOPS, taxa de transferência ou largura de banda e latência.

Nesta seção, discutiremos os indicadores comuns de desempenho no contexto do armazenamento Premium. Na seção a seguir, coletando requisitos de aplicativo, você aprenderá a medir esses indicadores de desempenho para seu aplicativo. Posteriormente, na otimização do desempenho do aplicativo, você aprenderá sobre os fatores que afetam esses indicadores de desempenho e recomendações para otimizá-los.

## <a name="iops"></a>IOPS

IOPS, ou operações de entrada/saída por segundo, é o número de solicitações que seu aplicativo está enviando para os discos de armazenamento em um segundo. Uma operação de entrada/saída pode ser de leitura ou gravação, sequencial ou aleatória. Aplicativos OLTP (processamento de transações online) como um site de varejo online precisam processar muitas solicitações de usuário simultâneas imediatamente. As solicitações de usuário são as transações de banco de dados de inserção e atualização intensivas, que o aplicativo deve processar rapidamente. Portanto, os aplicativos OLTP exigem IOPS muito alta. Esses aplicativos lidam com milhões de solicitações de e/s pequenas e aleatórias. Se você tiver um aplicativo desse tipo, deverá projetar a infraestrutura do aplicativo para otimizar o IOPS. Na seção mais adiante, *otimizando o desempenho do aplicativo*, discutiremos em detalhes todos os fatores que você deve considerar para obter IOPS alto.

Quando anexa um disco de armazenamento premium à sua VM de grande escala, o Azure aprovisiona um número garantido de IOPS de acordo com a especificação do disco. Por exemplo, um disco P50 provisiona 7500 IOPS. Cada tamanho de VM de grande escala tem também um limite de IOPS específico que pode sustentar. Por exemplo, uma VM GS5 padrão tem um limite de 80.000 IOPS.

## <a name="throughput"></a>Débito

Taxa de transferência ou largura de banda é a quantidade de dados que seu aplicativo está enviando para os discos de armazenamento em um intervalo especificado. Se seu aplicativo estiver executando operações de entrada/saída com tamanhos de unidade de e/s grandes, ele exigirá alta taxa de transferência. Os aplicativos de data warehouse tendem a emitir operações de verificação intensiva que acessam grandes partes de dados de cada vez e normalmente executam operações em massa. Em outras palavras, esses aplicativos exigem maior taxa de transferência. Se você tiver um aplicativo desse tipo, deverá criar sua infraestrutura para otimizar a taxa de transferência. Na próxima seção, discutiremos em detalhes os fatores que você deve ajustar para conseguir isso.

Quando você anexa um disco de armazenamento Premium a uma VM de alta escala, o Azure provisiona a taxa de transferência de acordo com essa especificação de disco. Por exemplo, um disco P50 provisiona 250 MB por segundo de taxa de transferência de disco. Cada tamanho de VM de alta escala também tem um limite de taxa de transferência específico que pode sustentar. Por exemplo, a VM GS5 padrão tem uma taxa de transferência máxima de 2.000 MB por segundo.

Há uma relação entre taxa de transferência e IOPS, conforme mostrado na fórmula abaixo.

![Relação de IOPS e taxa de transferência](../articles/virtual-machines/linux/media/premium-storage-performance/image1.png)

Portanto, é importante determinar os valores de taxa de transferência e de IOPS ideais que seu aplicativo requer. Ao tentar otimizar um, o outro também é afetado. Em uma seção posterior, *otimizando o desempenho do aplicativo*, abordaremos mais detalhes sobre como otimizar a IOPS e a taxa de transferência.

## <a name="latency"></a>Latência

Latência é o tempo que leva um aplicativo para receber uma única solicitação, enviá-lo aos discos de armazenamento e enviar a resposta para o cliente. Essa é uma medida crítica do desempenho de um aplicativo, além de IOPS e taxa de transferência. A latência de um disco de armazenamento Premium é o tempo necessário para recuperar as informações de uma solicitação e transmiti-las de volta ao seu aplicativo. O armazenamento Premium fornece latências baixas consistentes. Os discos Premium são projetados para fornecer latências de milissegundos de dígito único para a maioria das operações de e/s. Se você habilitar o cache de host ReadOnly em discos de armazenamento Premium, poderá obter latência de leitura muito menor. Abordaremos o cache de disco com mais detalhes na seção mais adiante sobre como *otimizar o desempenho do aplicativo*.

Quando você estiver otimizando seu aplicativo para obter IOPS e taxa de transferência mais altas, isso afetará a latência do seu aplicativo. Depois de ajustar o desempenho do aplicativo, sempre avalie a latência do aplicativo para evitar um comportamento de alta latência inesperado.

As operações de plano de controle a seguir em Managed Disks podem envolver a movimentação do disco de um local de armazenamento para outro. Isso é orquestrado por meio da cópia em segundo plano dos dados que podem levar várias horas para serem concluídos, normalmente menos de 24 horas, dependendo da quantidade de dados nos discos. Durante esse tempo, seu aplicativo pode ficar mais alto do que a latência de leitura usual, já que algumas leituras podem ser redirecionadas para o local original e podem levar mais tempo para serem concluídas. Não há nenhum impacto na latência de gravação durante esse período.

- Atualize o tipo de armazenamento.
- Desanexar e anexar um disco de uma VM para outra.
- Crie um disco gerenciado com base em um VHD.
- Crie um disco gerenciado com base em um instantâneo.
- Converter discos não gerenciados em discos gerenciados.

## <a name="performance-application-checklist-for-disks"></a>Lista de verificação de aplicativo de desempenho para discos

A primeira etapa na criação de aplicativos de alto desempenho em execução no armazenamento Premium do Azure está compreendendo os requisitos de desempenho do seu aplicativo. Depois de ter coletado os requisitos de desempenho, você pode otimizar seu aplicativo para obter o melhor desempenho.

Na seção anterior, explicamos os indicadores comuns de desempenho, IOPS, taxa de transferência e latência. Você deve identificar quais desses indicadores de desempenho são essenciais para o seu aplicativo a fim de fornecer a experiência do usuário desejada. Por exemplo, o alto IOPS é mais importante para aplicativos OLTP processando milhões de transações em um segundo. Enquanto, a alta taxa de transferência é essencial para data warehouse aplicativos que processam grandes quantidades de dados em um segundo. A latência extremamente baixa é crucial para aplicativos em tempo real, como sites de streaming de vídeo ao vivo.

Em seguida, meça os requisitos de desempenho máximo do seu aplicativo durante seu tempo de vida. Use a lista de verificação de exemplo abaixo como um início. Registre os requisitos máximos de desempenho durante períodos normais, de pico e fora do horário de carga de trabalho. Ao identificar os requisitos para todos os níveis de cargas de trabalho, você poderá determinar o requisito de desempenho geral do seu aplicativo. Por exemplo, a carga de trabalho normal de um site de comércio eletrônico será as transações que ele atende durante a maioria dos dias em um ano. A carga de trabalho de pico do site será as transações que ele atende durante a época de Natal ou eventos de venda especiais. A carga de trabalho de pico normalmente é experimentada por um período limitado, mas pode exigir que seu aplicativo dimensione duas ou mais vezes sua operação normal. Descubra os requisitos 50 percentil, 90 percentil e 99 percentil. Isso ajuda a filtrar as exceções nos requisitos de desempenho e você pode concentrar seus esforços na otimização dos valores corretos.

## <a name="application-performance-requirements-checklist"></a>Lista de verificação de requisitos de desempenho do aplicativo

| **Requisitos de desempenho** | **50 percentil** | **90 percentil** | **99 percentil** |
| --- | --- | --- | --- |
| Um máximo de Transações por segundo | | | |
| % De operações de leitura | | | |
| % De operações de gravação | | | |
| % De operações aleatórias | | | |
| % De operações sequenciais | | | |
| Tamanho da solicitação de e/s | | | |
| Taxa de transferência média | | | |
| Um máximo de Débito | | | |
| Min. Latência | | | |
| Latência média | | | |
| Um máximo de CPU | | | |
| Média de CPU | | | |
| Um máximo de Memória | | | |
| Memória média | | | |
| Profundidade da fila | | | |

> [!NOTE]
> Você deve considerar o dimensionamento desses números com base no futuro crescimento esperado do seu aplicativo. É uma boa ideia planejar o crescimento antecipadamente, pois pode ser mais difícil alterar a infraestrutura para melhorar o desempenho mais tarde.

Se você tiver um aplicativo existente e quiser mudar para o armazenamento Premium, primeiro crie a lista de verificação acima para o aplicativo existente. Em seguida, crie um protótipo do seu aplicativo no armazenamento Premium e projete o aplicativo com base nas diretrizes descritas em *otimizando o desempenho do aplicativo* em uma seção posterior deste documento. O próximo artigo descreve as ferramentas que você pode usar para reunir as medidas de desempenho.

### <a name="counters-to-measure-application-performance-requirements"></a>Contadores para medir os requisitos de desempenho do aplicativo

A melhor maneira de medir os requisitos de desempenho do seu aplicativo é usar as ferramentas de monitoramento de desempenho fornecidas pelo sistema operacional do servidor. Você pode usar o PerfMon para Windows e iostat para Linux. Essas ferramentas capturam contadores correspondentes a cada medida explicada na seção acima. Você deve capturar os valores desses contadores quando seu aplicativo estiver executando suas cargas de trabalho normais, de pico e fora do horário comercial.

Os contadores PerfMon estão disponíveis para processador, memória e, cada disco lógico e disco físico do servidor. Quando você usa discos de armazenamento Premium com uma VM, os contadores de disco físico são para cada disco de armazenamento Premium, e os contadores de disco lógico são para cada volume criado nos discos de armazenamento Premium. Você deve capturar os valores para os discos que hospedam a carga de trabalho do aplicativo. Se houver um mapeamento de um para um entre discos lógicos e físicos, você poderá consultar contadores de disco físico; caso contrário, consulte os contadores de disco lógico. No Linux, o comando iostat gera um relatório de utilização de CPU e disco. O relatório de utilização de disco fornece estatísticas por dispositivo físico ou partição. Se você tiver um servidor de banco de dados com seu log e seus logs em discos separados, colete esses dados para ambos os discos. A tabela abaixo descreve os contadores para discos, processadores e memória:

| Contador | Descrição | PerfMon | Iostat |
| --- | --- | --- | --- |
| **IOPS ou transações por segundo** |Número de solicitações de e/s emitidas para o disco de armazenamento por segundo. |Leituras de disco/seg <br> Escritas de disco/seg |TPS <br> r/s <br> w/s |
| **Leituras e gravações de disco** |% de leituras e operações de gravação executadas no disco. |% De tempo de leitura de disco <br> % De tempo de gravação de disco |r/s <br> w/s |
| **Débito** |Quantidade de dados lidos ou gravados no disco por segundo. |Bytes Lidos de Disco/seg <br> Bytes Escritos em Disco/seg |kB_read/s <br> kB_wrtn/s |
| **Latência** |Tempo total para concluir uma solicitação de e/s de disco. |Média de disco s/leitura <br> Média de disco s/gravação |await <br> svctm |
| **Tamanho de e/s** |O tamanho dos problemas de solicitações de e/s para os discos de armazenamento. |Média de bytes de disco/leitura <br> Média de bytes de disco/gravação |avgrq-sz |
| **Profundidade da fila** |Número de solicitações de e/s pendentes aguardando para serem lidas ou gravadas no disco de armazenamento. |Comprimento da fila de disco atual |avgqu-sz |
| **Maximizar. Memória** |Quantidade de memória necessária para executar o aplicativo sem problemas |% dos Bytes Consolidados em Utilização |Usar vmstat |
| **Maximizar. CPUs** |Quantidade de CPU necessária para executar o aplicativo sem problemas |% De tempo do processador |% util |

Saiba mais sobre [iostat](https://linux.die.net/man/1/iostat) e [Perfmon](https://msdn.microsoft.com/library/aa645516.aspx).



## <a name="optimize-application-performance"></a>Otimizar o desempenho do aplicativo

Os principais fatores que influenciam o desempenho de um aplicativo em execução no armazenamento Premium são a natureza das solicitações de e/s, tamanho da VM, tamanho do disco, número de discos, cache de disco, multithread e profundidade da fila. Você pode controlar alguns desses fatores com botões fornecidos pelo sistema. A maioria dos aplicativos pode não lhe dar a opção de alterar o tamanho da e/s e a profundidade da fila diretamente. Por exemplo, se você estiver usando SQL Server, não será possível escolher o tamanho da e/s e a profundidade da fila. SQL Server escolhe o tamanho ideal de e/s e os valores de profundidade da fila para obter o máximo de desempenho. É importante entender os efeitos de ambos os tipos de fatores no desempenho do aplicativo, para que você possa provisionar os recursos apropriados para atender às necessidades de desempenho.

Ao longo desta seção, consulte a lista de verificação de requisitos do aplicativo que você criou, para identificar quanto você precisa para otimizar o desempenho do aplicativo. Com base nele, você poderá determinar quais fatores dessa seção precisará ajustar. Para testemunhar os efeitos de cada fator no desempenho do aplicativo, execute as ferramentas de benchmark na configuração do aplicativo. Consulte o artigo de benchmarking, vinculado ao final, para obter as etapas para executar ferramentas comuns de benchmark em VMs Windows e Linux.

### <a name="optimize-iops-throughput-and-latency-at-a-glance"></a>Otimizar o IOPS, a taxa de transferência e a latência em um relance

A tabela a seguir resume os fatores de desempenho e as etapas necessárias para otimizar o IOPS, a taxa de transferência e a latência. As seções que seguem este resumo descreverão cada fator é muito mais aprofundada.

Para obter mais informações sobre tamanhos de VM e sobre IOPS, taxa de transferência e latência disponíveis para cada tipo de VM, consulte [tamanhos de VM Linux](../articles/virtual-machines/linux/sizes.md) ou [tamanhos de VM do Windows](../articles/virtual-machines/windows/sizes.md).

| &nbsp; | **IOPS** | **Débito** | **Latência** |
| --- | --- | --- | --- |
| **Cenário de exemplo** |Aplicativo OLTP corporativo que requer taxa de transações muito altas por segundo. |Aplicativo Enterprise Data Warehousing processando grandes quantidades de dados. |Aplicativos quase em tempo real que exigem respostas instantâneas para solicitações de usuário, como jogos online. |
| Fatores de desempenho | &nbsp; | &nbsp; | &nbsp; |
| **Tamanho de e/s** |O tamanho de e/s menor gera IOPS maiores. |Tamanho de e/s maior para gerar uma taxa de transferência mais alta. | &nbsp;|
| **Tamanho da VM** |Use um tamanho de VM que ofereça IOPS maior do que o requisito do seu aplicativo. |Use um tamanho de VM com limite de taxa de transferência maior do que o requisito do aplicativo. |Use um tamanho de VM que ofereça limites de escala maiores do que o requisito do aplicativo. |
| **Tamanho do disco** |Use um tamanho de disco que ofereça IOPS maior do que o requisito do seu aplicativo. |Use um tamanho de disco com limite de taxa de transferência maior do que o requisito do aplicativo. |Use um tamanho de disco que ofereça limites de escala maiores do que o requisito do seu aplicativo. |
| **Limites de escala de VM e disco** |O limite de IOPS do tamanho da VM escolhido deve ser maior que o total de IOPS orientado por discos de armazenamento Premium anexados a ele. |O limite de taxa de transferência do tamanho da VM escolhido deve ser maior que a taxa de transferência total controlada por discos de armazenamento Premium anexados a ele. |Os limites de escala do tamanho da VM escolhido devem ser maiores que os limites de escala totais de discos de armazenamento Premium anexados. |
| **Cache de disco** |Habilite o cache ReadOnly em discos de armazenamento Premium com operações de leitura intensa para obter IOPS de leitura mais alta. | &nbsp; |Habilite o cache ReadOnly em discos de armazenamento Premium com operações pesadas prontas para obter latências de leitura muito baixas. |
| **Distribuição de disco** |Use vários discos e distribua-os juntos para obter um limite de IOPS e taxa de transferência mais alto combinado. O limite combinado por VM deve ser maior que os limites combinados de discos Premium anexados. | &nbsp; | &nbsp; |
| **Tamanho da distribuição** |Tamanho de distribuição menor para o padrão de e/s pequeno aleatório visto em aplicativos OLTP. Por exemplo, use o tamanho de distribuição de 64 KB para SQL Server aplicativo OLTP. |Tamanho de distribuição maior para o padrão de e/s grande sequencial visto em aplicativos data warehouse. Por exemplo, use o tamanho de distribuição de 256 KB para SQL Server aplicativo de data warehouse. | &nbsp; |
| **Multithreading** |Use multithreading para enviar um número maior de solicitações para o armazenamento Premium que levará a IOPS e taxa de transferência mais altas. Por exemplo, em SQL Server defina um valor de MAXDOP alto para alocar mais CPUs para SQL Server. | &nbsp; | &nbsp; |
| **Profundidade da fila** |A profundidade de fila maior produz IOPS maiores. |A profundidade de fila maior produz uma taxa de transferência maior. |A profundidade de fila menor resulta em latências menores. |

## <a name="nature-of-io-requests"></a>Natureza das solicitações de e/s

Uma solicitação de e/s é uma unidade de operação de entrada/saída que seu aplicativo executará. Identificar a natureza das solicitações de e/s, aleatória ou sequencial, leitura ou gravação, pequena ou grande, ajudará você a determinar os requisitos de desempenho do seu aplicativo. É importante entender a natureza das solicitações de e/s para tomar as decisões certas ao projetar sua infraestrutura de aplicativo. O IOs deve ser distribuído uniformemente para obter o melhor desempenho possível.

O tamanho de e/s é um dos fatores mais importantes. O tamanho de e/s é o tamanho da solicitação de operação de entrada/saída gerada pelo seu aplicativo. O tamanho de e/s tem um impacto significativo no desempenho, especialmente no IOPS e na largura de banda que o aplicativo é capaz de alcançar. A fórmula a seguir mostra a relação entre IOPS, tamanho de e/s e largura de banda/taxa de transferência.  
    ![](media/premium-storage-performance/image1.png)

Alguns aplicativos permitem que você altere seu tamanho de e/s, enquanto alguns aplicativos não têm. Por exemplo, SQL Server determina o tamanho de e/s ideal e não fornece aos usuários nenhum botão para alterá-lo. Por outro lado, o Oracle fornece um parâmetro chamado [DB\_bloco\_tamanho](https://docs.oracle.com/cd/B19306_01/server.102/b14211/iodesign.htm#i28815) usando o qual você pode configurar o tamanho da solicitação de e/s do banco de dados.

Se você estiver usando um aplicativo, que não permite alterar o tamanho de e/s, use as diretrizes neste artigo para otimizar o KPI de desempenho que é mais relevante para seu aplicativo. Por exemplo,

* Um aplicativo OLTP gera milhões de solicitações de e/s pequenas e aleatórias. Para lidar com esses tipos de solicitações de e/s, você deve projetar a infraestrutura do aplicativo para obter um IOPS maior.  
* Um aplicativo de data warehouse gera solicitações de e/s grandes e sequenciais. Para lidar com esses tipos de solicitações de e/s, você deve projetar sua infraestrutura de aplicativo para obter maior largura de banda ou taxa de transferência.

Se você estiver usando um aplicativo, que permite alterar o tamanho de e/s, use essa regra básica para o tamanho de e/s, além de outras diretrizes de desempenho,

* Tamanho de e/s menor para obter IOPS mais alto. Por exemplo, 8 KB para um aplicativo OLTP.  
* Tamanho de e/s maior para obter maior largura de banda/taxa de transferência. Por exemplo, 1024 KB para um aplicativo data warehouse.

Aqui está um exemplo de como você pode calcular a IOPS e a taxa de transferência/largura de banda para seu aplicativo. Considere um aplicativo usando um disco p30. O máximo de IOPS e taxa de transferência/largura de banda que um disco p30 pode atingir é de 5000 IOPS e 200 MB por segundo, respectivamente. Agora, se seu aplicativo exigir o máximo de IOPS do disco p30 e você usar um tamanho de e/s menor, como 8 KB, a largura de banda resultante que será capaz de obter será de 40 MB por segundo. No entanto, se seu aplicativo exigir a taxa de transferência/largura de banda máxima do disco p30 e você usar um tamanho de e/s maior, como 1024 KB, o IOPS resultante será menor, 200 IOPS. Portanto, ajuste o tamanho de e/s, de modo que atenda aos requisitos de IOPS e taxa de transferência/largura de banda do aplicativo. A tabela a seguir resume os diferentes tamanhos de e/s e seus IOPS e taxa de transferência correspondentes para um disco p30.

| Requisito de aplicativo | Tamanho de e/s | IOPS | Taxa de transferência/largura de banda |
| --- | --- | --- | --- |
| IOPS Máximo |8 KB |5000 |40 MB por segundo |
| Taxa de transferência máxima |1024 KB |200 |200 MB por segundo |
| Taxa de transferência máxima + IOPS alto |64 KB |3,200 |200 MB por segundo |
| IOPS máxima + alta taxa de transferência |32 KB |5000 |160 MB por segundo |

Para obter IOPS e largura de banda maiores que o valor máximo de um único disco de armazenamento Premium, use vários discos Premium distribuídos juntos. Por exemplo, distribua dois discos p30 para obter um IOPS combinado de 10.000 IOPS ou uma taxa de transferência combinada de 400 MB por segundo. Conforme explicado na próxima seção, você deve usar um tamanho de VM que dá suporte à taxa de transferência e IOPS de disco combinados.

> [!NOTE]
> À medida que você aumenta o IOPS ou a taxa de transferência, o outro também aumenta, certifique-se de não atingir os limites de taxa de transferência ou de IOPS do disco ou da VM ao aumentar um deles.

Para testemunhar os efeitos do tamanho de e/s no desempenho do aplicativo, você pode executar ferramentas de benchmark em sua VM e discos. Crie várias execuções de teste e use o tamanho de e/s diferente para cada execução para ver o impacto. Consulte o artigo de benchmarking, vinculado ao final, para obter mais detalhes.

## <a name="high-scale-vm-sizes"></a>Tamanhos de VM de alta escala

Quando você começa a criar um aplicativo, uma das primeiras coisas a fazer é escolher uma VM para hospedar seu aplicativo. O armazenamento Premium vem com tamanhos de VM de alta escala que podem executar aplicativos que exigem maior capacidade de computação e um alto desempenho de e/s de disco local. Essas VMs fornecem processadores mais rápidos, uma taxa maior de memória para núcleo e uma unidade de estado sólido (SSD) para o disco local. Exemplos de VMs de alta escala que dão suporte ao armazenamento Premium são as VMs da série DS e GS.

As VMs de alta escala estão disponíveis em tamanhos diferentes com um número diferente de núcleos de CPU, memória, sistema operacional e tamanho de disco temporário. Cada tamanho de VM também tem o número máximo de discos de dados que você pode anexar à VM. Portanto, o tamanho escolhido da VM afetará a quantidade de capacidade de processamento, memória e armazenamento disponível para seu aplicativo. Ele também afeta o custo de computação e armazenamento. Por exemplo, abaixo estão as especificações do maior tamanho de VM em uma série DS e uma da GS:

| Tamanhos de VM | Núcleos de CPU | Memória | Tamanhos de disco de VM | Um máximo de discos de dados | Tamanho da cache | IOPS | Limites de es de cache de largura de banda |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_DS14 |16 |112 GB |OS = 1023 GB <br> SSD local = 224 GB |32 |576 GB |IOPS DE 50.000 <br> 512 MB por segundo |4\.000 IOPS e 33 MB por segundo |
| Standard_GS5 |32 |448 GB |OS = 1023 GB <br> SSD local = 896 GB |64 |4224 GB |IOPS DE 80.000 <br> 2\.000 MB por segundo |5\.000 IOPS e 50 MB por segundo |

Para exibir uma lista completa de todos os tamanhos de VM do Azure disponíveis, consulte [tamanhos de VM do Windows](../articles/virtual-machines/windows/sizes.md) ou tamanhos de VM do [Linux](../articles/virtual-machines/linux/sizes.md). Escolha um tamanho de VM que possa atender e dimensionar seus requisitos de desempenho de aplicativo desejados. Além disso, leve em consideração as seguintes considerações importantes ao escolher tamanhos de VM.

*Limites de escala*  
Os limites máximos de IOPS por VM e por disco são diferentes e independentes um do outro. Certifique-se de que o aplicativo está impulsionando o IOPS dentro dos limites da VM, bem como os discos Premium anexados a ele. Caso contrário, o desempenho do aplicativo apresentará limitação.

Por exemplo, suponha que um requisito de aplicativo seja um máximo de 4.000 IOPS. Para conseguir isso, você provisiona um disco p30 em uma VM DS1. O disco p30 pode fornecer até 5.000 IOPS. No entanto, a VM DS1 é limitada a 3.200 IOPS. Consequentemente, o desempenho do aplicativo será restrito pelo limite da VM em 3.200 IOPS e haverá degradação do desempenho. Para evitar essa situação, escolha uma VM e o tamanho do disco que atenderão aos requisitos do aplicativo.

*Custo da operação*  
Em muitos casos, é possível que o custo geral da operação usando o armazenamento Premium seja menor do que o uso do armazenamento padrão.

Por exemplo, considere um aplicativo que requer 16.000 IOPS. Para atingir esse desempenho, você precisará de uma VM de IaaS do Azure padrão\_D14, que pode fornecer um máximo de IOPS de 16.000 usando discos de 1 TB de armazenamento Standard 32. Cada disco de armazenamento Standard de 1 TB pode alcançar um máximo de 500 IOPS. O custo estimado dessa VM por mês será de $1570. O custo mensal de 32 discos de armazenamento padrão será $1638. O custo mensal total estimado será de $3208.

No entanto, se você tiver hospedado o mesmo aplicativo no armazenamento Premium, será necessário um tamanho de VM menor e menos discos de armazenamento Premium, reduzindo assim o custo geral. Uma VM\_DS13 padrão pode atender ao requisito 16.000 IOPS usando quatro discos p30. A VM DS13 tem um máximo de IOPS de 25.600 e cada disco p30 tem um IOPS máximo de 5.000. Em geral, essa configuração pode atingir 5.000 x 4 = IOPS de 20.000. O custo estimado dessa VM por mês será de $1003. O custo mensal de quatro discos de armazenamento p30 Premium será $544.34. O custo mensal total estimado será de $1544.

A tabela a seguir resume a divisão de custo desse cenário para o armazenamento Standard e Premium.

| &nbsp; | **Standard** | **Premium** |
| --- | --- | --- |
| **Custo da VM por mês** |$1570.58 (padrão\_D14) |$1003.66 (Standard\_DS13) |
| **Custo de discos por mês** |$1638.40 (32 x 1 TB de discos) |$544.34 (4 x p30 discos) |
| **Custo geral por mês** |$3,208.98 |$1,544.34 |

*Distribuições Linux*  

Com o armazenamento Premium do Azure, você obtém o mesmo nível de desempenho para VMs que executam Windows e Linux. Damos suporte a vários tipos de distribuições do Linux e você pode ver a lista completa [aqui](../articles/virtual-machines/linux/endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). É importante observar que diferentes distribuições são mais adequadas para diferentes tipos de cargas de trabalho. Você verá níveis diferentes de desempenho dependendo do distribuição em que sua carga de trabalho está em execução. Teste o distribuições do Linux com seu aplicativo e escolha aquele que funciona melhor.

Ao executar o Linux com o armazenamento Premium, verifique as atualizações mais recentes sobre os drivers necessários para garantir o alto desempenho.

## <a name="premium-storage-disk-sizes"></a>Tamanhos de disco de armazenamento Premium

O armazenamento Premium do Azure oferece uma variedade de tamanhos para que você possa escolher um que melhor atenda às suas necessidades. Cada tamanho de disco tem um limite de escala diferente para IOPS, largura de banda e armazenamento. Escolha o tamanho correto do disco de armazenamento Premium, dependendo dos requisitos do aplicativo e do tamanho da VM de alta escala. A tabela a seguir mostra os tamanhos dos discos e seus recursos. Os tamanhos P4, P6, P15, P60, P70 e P80 atualmente só têm suporte para Managed Disks.

[!INCLUDE [disk-storage-premium-ssd-sizes](disk-storage-premium-ssd-sizes.md)]

Quantos discos você escolher depende do tamanho do disco escolhido. Você pode usar um único disco P50 ou vários discos P10 para atender aos requisitos do aplicativo. Leve em consideração as considerações listadas abaixo ao fazer a escolha.

*Limites de escala (IOPS e taxa de transferência)*  
Os limites de IOPS e taxa de transferência de cada tamanho de disco Premium são diferentes e independentes dos limites de escala da VM. Verifique se a IOPS e a taxa de transferência totais dos discos estão dentro dos limites de escala do tamanho da VM escolhida.

Por exemplo, se um requisito de aplicativo for de, no máximo, 250 MB/s de taxa de transferência e você estiver usando uma VM DS4 com um único disco p30. A VM DS4 pode dar até 256 MB/s de taxa de transferência. No entanto, um único disco p30 tem o limite de taxa de transferência de 200 MB/s. Consequentemente, o aplicativo será restrito a 200 MB/s devido ao limite de disco. Para superar esse limite, provisione mais de um disco de dados para a VM ou redimensione seus discos para P40 ou P50.

> [!NOTE]
> As leituras servidas pelo cache não são incluídas na IOPS e na taxa de transferência do disco, portanto, não estão sujeitas a limites de disco. O cache tem seu limite de IOPS e taxa de transferência separado por VM.
>
> Por exemplo, inicialmente, suas leituras e gravações são 60 MB/s e 40/s, respectivamente. Com o tempo, o cache fica ativo e atende a cada vez mais das leituras do cache. Em seguida, você pode obter uma taxa de transferência de gravação maior do disco.

*Número de discos*  
Determine o número de discos que você precisará avaliando os requisitos do aplicativo. Cada tamanho de VM também tem um limite no número de discos que você pode anexar à VM. Normalmente, esse é o dobro do número de núcleos. Verifique se o tamanho da VM escolhido pode dar suporte ao número de discos necessários.

Lembre-se de que os discos de armazenamento Premium têm recursos de desempenho mais altos em comparação com os discos de armazenamento padrão. Portanto, se você estiver migrando seu aplicativo da VM IaaS do Azure usando o armazenamento Standard para o armazenamento Premium, provavelmente precisará de menos discos Premium para obter o mesmo desempenho ou mais alto para seu aplicativo.

## <a name="disk-caching"></a>Colocação em cache do disco

As VMs de alta escala que aproveitam o armazenamento Premium do Azure têm uma tecnologia de cache de várias camadas chamada BlobCache. O BlobCache usa uma combinação da RAM da máquina virtual e do SSD local para cache. Esse cache está disponível para os discos persistentes de armazenamento Premium e os discos locais da VM. Por padrão, essa configuração de cache é definida como leitura/gravação para discos do sistema operacional e ReadOnly para discos de dados hospedados no armazenamento Premium. Com o cache de disco habilitado nos discos do armazenamento Premium, as VMs de alta escala podem atingir níveis extremamente altos de desempenho que excedem o desempenho de disco subjacente.

> [!WARNING]
> O cache de disco não tem suporte para discos 4 TiB e maiores. Se vários discos estiverem anexados à sua VM, cada disco com menos de 4 TiB dará suporte a cache.
>
> Alterar a definição de cache de um disco do Azure desanexa e anexa o disco de destino. Se for o disco do sistema operacional, a VM será reiniciada. Pare todas as aplicações/serviços que possam ser afetados por esta interrupção antes de alterar a definição da cache do disco.

Para saber mais sobre como o BlobCache funciona, consulte a postagem no blog do [armazenamento Premium do Azure](https://azure.microsoft.com/blog/azure-premium-storage-now-generally-available-2/) .

É importante habilitar o cache no conjunto correto de discos. Se você deve habilitar o cache de disco em um disco Premium ou não dependerá do padrão de carga de trabalho que será manuseado pelo disco. A tabela abaixo mostra as configurações de cache padrão para o sistema operacional e OS discos de dados.

| **Tipo de disco** | **Configuração de cache padrão** |
| --- | --- |
| Disco do SO |ReadWrite |
| Disco de dados |ReadOnly |

A seguir estão as configurações de cache de disco recomendadas para discos de dados,

| **Configuração de cache de disco** | **recomendação sobre quando usar essa configuração** |
| --- | --- |
| Nenhuma |Configure o cache de host como nenhum para discos somente gravação e de gravação intensa. |
| ReadOnly |Configure o cache do host como ReadOnly para discos somente leitura e de leitura/gravação. |
| ReadWrite |Configure o cache do host como ReadWrite somente se seu aplicativo manipula corretamente a gravação de dados armazenados em cache em discos persistentes quando necessário. |

*ReadOnly*  
Ao configurar o cache somente leitura em discos de dados de armazenamento Premium, você pode obter baixa latência de leitura e obter IOPS de leitura e taxa de transferência muito altas para seu aplicativo. Isso é devido a dois motivos,

1. As leituras realizadas do cache, que estão na memória da VM e no SSD local, são muito mais rápidas do que as leituras do disco de dados, que está no armazenamento de BLOBs do Azure.  
1. O armazenamento Premium não conta as leituras servidas do cache, em direção à taxa de transferência e IOPS de disco. Portanto, seu aplicativo é capaz de alcançar um total maior de IOPS e taxa de transferência.

*ReadWrite*  
Por padrão, os discos do sistema operacional têm o cache ReadWrite habilitado. Recentemente, adicionamos suporte para cache ReadWrite em discos de dados. Se você estiver usando o cache ReadWrite, deverá ter uma maneira adequada de gravar os dados do cache em discos persistentes. Por exemplo, SQL Server lida com a gravação de dados armazenados em cache nos discos de armazenamento persistentes por conta própria. O uso do cache ReadWrite com um aplicativo que não lida com a persistência dos dados necessários pode levar à perda de dados, se a VM falhar.

*Nenhum*  
No momento, **nenhum** só tem suporte em discos de dados. Não há suporte para ele em discos do sistema operacional. Se você definir **nenhum** em um disco do sistema operacional, ele substituirá isso internamente e o definirá como **ReadOnly**.

Por exemplo, você pode aplicar essas diretrizes para SQL Server em execução no armazenamento Premium fazendo o seguinte:

1. Configure o cache "ReadOnly" em discos de armazenamento Premium que hospedam arquivos de dados.  
   a.  As leituras rápidas do cache diminuem o tempo de consulta de SQL Server, pois as páginas de dados são recuperadas muito mais rapidamente do cache em comparação com os discos de dados.  
   b.  Atendendo leituras do cache, significa que há uma taxa de transferência adicional disponível nos discos de dados Premium. SQL Server pode usar essa taxa de transferência adicional para recuperar mais páginas de dados e outras operações, como backup/restauração, cargas de lote e recompilações de índice.  
1. Configure o cache "nenhum" em discos de armazenamento Premium que hospedam os arquivos de log.  
   a.  Os arquivos de log têm principalmente operações de gravação pesada. Portanto, eles não se beneficiam do cache ReadOnly.

## <a name="optimize-performance-on-linux-vms"></a>Otimizar o desempenho em VMs Linux

Para todos os SSDs ou ultra discos Premium com cache definido como **ReadOnly** ou **None**, você deve desabilitar "barreiras" ao montar o sistema de arquivos. Você não precisa de barreiras nesse cenário porque as gravações nos discos de armazenamento Premium são duráveis para essas configurações de cache. Quando a solicitação de gravação for concluída com êxito, os dados foram gravados no repositório persistente. Para desabilitar "barreiras", use um dos métodos a seguir. Escolha uma para o sistema de arquivos:
  
* Para **reiserFS**, para desabilitar as barreiras, use a opção de montagem `barrier=none`. (Para habilitar as barreiras, use `barrier=flush`.)
* Para **ext3/ext4**, para desabilitar as barreiras, use a opção de montagem `barrier=0`. (Para habilitar as barreiras, use `barrier=1`.)
* Para **xfs**, para desabilitar as barreiras, use a opção de montagem `nobarrier`. (Para habilitar as barreiras, use `barrier`.)
* Para discos de armazenamento Premium com cache definido como **ReadWrite**, habilite as barreiras para durabilidade de gravação.
* Para que os rótulos de volume persistam depois de reiniciar a VM, você deve atualizar o/etc/fstab com as referências de UUID (identificador universal exclusivo) para os discos. Para obter mais informações, consulte [Adicionar um disco gerenciado a uma VM do Linux](../articles/virtual-machines/linux/add-disk.md).

As seguintes distribuições Linux foram validadas para SSDs Premium. Para obter melhor desempenho e estabilidade com o SSDs Premium, recomendamos que você atualize suas VMs para uma dessas versões ou mais recente. 

Algumas das versões exigem o mais recente Integration Services do Linux (LIS), v 4.0, para o Azure. Para baixar e instalar uma distribuição, siga o link listado na tabela a seguir. Adicionamos imagens à lista à medida que concluímos a validação. Nossas validações mostram que o desempenho varia de acordo com cada imagem. O desempenho depende das características da carga de trabalho e das configurações da imagem. Imagens diferentes são ajustadas para diferentes tipos de cargas de trabalho.

| Distribuição | Versão | Kernel com suporte | Detalhes |
| --- | --- | --- | --- |
| Ubuntu | 12, 4 ou mais recente| 3.2.0-75.110+ | &nbsp; |
| Ubuntu | 14, 4 ou mais recente| 3.13.0-44.73+  | &nbsp; |
| Debian | 7. x, 8. x ou mais recente| 3.16.7-ckt4-1+ | &nbsp; |
| SUSE | SLES 12 ou mais recente| 3.12.36-38.1+ | &nbsp; |
| SUSE | SLES 11 SP4 ou mais recente| 3.0.101-0.63.1+ | &nbsp; |
| CoreOS | 584.0.0 + ou mais recente| 3.18.4+ | &nbsp; |
| CentOS | 6,5, 6,6, 6,7, 7,0 ou mais recente| &nbsp; | [LIS4 necessário](https://www.microsoft.com/download/details.aspx?id=55106) <br> *Consulte a observação na próxima seção* |
| CentOS | 7.1 + ou mais recente| 3.10.0-229.1.2.el7+ | [LIS4 recomendado](https://www.microsoft.com/download/details.aspx?id=55106) <br> *Consulte a observação na próxima seção* |
| Red Hat Enterprise Linux (RHEL) | 6.8 +, 7.2 + ou mais recente | &nbsp; | &nbsp; |
| Oracle | 6.0 +, 7.2 + ou mais recente | &nbsp; | UEK4 ou RHCK |
| Oracle | 7.0-7.1 ou mais recente | &nbsp; | UEK4 ou RHCK w/[LIS4](https://www.microsoft.com/download/details.aspx?id=55106) |
| Oracle | 6.4-6.7 ou mais recente | &nbsp; | UEK4 ou RHCK w/[LIS4](https://www.microsoft.com/download/details.aspx?id=55106) |

### <a name="lis-drivers-for-openlogic-centos"></a>Drivers LIS para OpenLogic CentOS

Se você estiver executando VMs OpenLogic CentOS, execute o seguinte comando para instalar os drivers mais recentes:

```
sudo yum remove hypervkvpd  ## (Might return an error if not installed. That's OK.)
sudo yum install microsoft-hyper-v
sudo reboot
```

Em alguns casos, o comando acima também atualizará o kernel. Se uma atualização de kernel for necessária, talvez seja necessário executar os comandos acima novamente após a reinicialização para instalar completamente o pacote Microsoft-Hyper-v.


## <a name="disk-striping"></a>Distribuição de disco

Quando uma VM de alta escala é anexada a vários discos persistentes de armazenamento Premium, os discos podem ser distribuídos em conjunto para agregar a capacidade de armazenamento, largura de banda e IOPs.

No Windows, você pode usar espaços de armazenamento para distribuir discos juntos. Você deve configurar uma coluna para cada disco em um pool. Caso contrário, o desempenho geral do volume distribuído pode ser menor do que o esperado, devido à distribuição irregular do tráfego entre os discos.

Importante: usando Gerenciador do Servidor interface do usuário, você pode definir o número total de colunas até 8 para um volume distribuído. Ao anexar mais de oito discos, use o PowerShell para criar o volume. Usando o PowerShell, você pode definir o número de colunas igual ao número de discos. Por exemplo, se houver 16 discos em um único conjunto de distribuição; Especifique 16 colunas no parâmetro *NumberOfColumns* do cmdlet *New-VirtualDisk* do PowerShell.

No Linux, use o utilitário MDADM para distribuir discos juntos. Para obter etapas detalhadas sobre a distribuição de discos no Linux, consulte [Configurar o RAID de software no Linux](../articles/virtual-machines/linux/configure-raid.md).

*Tamanho da distribuição*  
Uma configuração importante na distribuição de disco é o tamanho da distribuição. O tamanho da faixa ou o tamanho do bloco é a menor parte dos dados que o aplicativo pode tratar em um volume distribuído. O tamanho de distribuição que você configura depende do tipo de aplicativo e seu padrão de solicitação. Se você escolher o tamanho de distribuição errado, isso pode levar ao desalinhamento de e/s, o que leva à degradação do desempenho do seu aplicativo.

Por exemplo, se uma solicitação de e/s gerada pelo seu aplicativo for maior do que o tamanho de distribuição do disco, o sistema de armazenamento a gravará entre limites de unidade de distribuição em mais de um disco. Quando for o momento de acessar esses dados, será necessário buscar mais de uma unidade de distribuição para concluir a solicitação. O efeito cumulativo desse comportamento pode levar à degradação significativa do desempenho. Por outro lado, se o tamanho da solicitação de e/s for menor do que o tamanho da distribuição e, se for aleatório por natureza, as solicitações de e/s poderão ser adicionadas no mesmo disco causando um afunilamento e, conseqüentemente, degradando o desempenho de e/s.

Dependendo do tipo de carga de trabalho que seu aplicativo está executando, escolha um tamanho de distribuição apropriado. Para solicitações aleatórias de pequena e/s, use um tamanho de distribuição menor. Ao passo que para solicitações de e/s sequenciais grandes, use um tamanho de distribuição maior. Descubra as recomendações de tamanho de distribuição para o aplicativo que será executado no armazenamento Premium. Para SQL Server, configure o tamanho de distribuição de 64 KB para cargas de trabalho OLTP e 256 KB para cargas de trabalho de data warehouse. Consulte [práticas recomendadas de desempenho para SQL Server em VMs do Azure](../articles/virtual-machines/windows/sql/virtual-machines-windows-sql-performance.md#disks-guidance) para saber mais.

> [!NOTE]
> Você pode distribuir um máximo de 32 discos de armazenamento Premium em uma VM da série DS e 64 discos de armazenamento Premium em uma VM da série GS.

## <a name="multi-threading"></a>Multithreading

O Azure criou a plataforma de armazenamento Premium para ser massivamente paralela. Portanto, um aplicativo multi-threaded Obtém um desempenho muito maior do que um aplicativo de thread único. Um aplicativo multi-threaded divide suas tarefas em vários threads e aumenta a eficiência de sua execução utilizando a VM e os recursos de disco para o máximo.

Por exemplo, se seu aplicativo estiver sendo executado em uma única VM de núcleo usando dois threads, a CPU poderá alternar entre os dois threads para obter eficiência. Enquanto um thread está aguardando a conclusão de uma e/s de disco, a CPU pode mudar para o outro thread. Dessa forma, dois threads podem realizar mais do que um único thread. Se a VM tiver mais de um núcleo, diminuirá ainda mais o tempo de execução, pois cada núcleo pode executar tarefas em paralelo.

Talvez você não consiga alterar o modo como um aplicativo pronto para prateleira implementa thread único ou multithreading. Por exemplo, SQL Server é capaz de lidar com várias CPUs e vários núcleos. No entanto, SQL Server decide em quais condições ele usará um ou mais threads para processar uma consulta. Ele pode executar consultas e criar índices usando vários threads. Para uma consulta que envolve a junção de tabelas grandes e a classificação de dados antes de retornar ao usuário, SQL Server provavelmente usará vários threads. No entanto, um usuário não pode controlar se SQL Server executa uma consulta usando um único thread ou vários threads.

Há definições de configuração que você pode alterar para influenciar esse processamento paralelo ou multithread de um aplicativo. Por exemplo, no caso de SQL Server é a configuração de grau máximo de paralelismo. Essa configuração chamada MAXDOP, permite que você configure o número máximo de processadores SQL Server pode usar quando o processamento paralelo. Você pode configurar MAXDOP para consultas individuais ou operações de índice. Isso é benéfico quando você deseja balancear os recursos do seu sistema para um aplicativo de desempenho crítico.

Por exemplo, digamos que seu aplicativo usando SQL Server esteja executando uma consulta grande e uma operação de índice ao mesmo tempo. Vamos supor que você queria que a operação de índice fosse mais eficaz em comparação com a consulta grande. Nesse caso, você pode definir o valor MAXDOP da operação de índice para ser maior do que o valor MAXDOP da consulta. Dessa forma, SQL Server tem um número maior de processadores que ele pode aproveitar para a operação de índice em comparação com o número de processadores que ele pode dedicar à consulta grande. Lembre-se de que você não controla o número de threads que SQL Server será usado para cada operação. Você pode controlar o número máximo de processadores que estão sendo dedicados para vários threads.

Saiba mais sobre os [graus de paralelismo](https://technet.microsoft.com/library/ms188611.aspx) no SQL Server. Descubra essas configurações que influenciam multithreading em seu aplicativo e suas configurações para otimizar o desempenho.

## <a name="queue-depth"></a>Profundidade da fila

A profundidade da fila ou o tamanho da fila ou do intervalo é o número de solicitações de e/s pendentes no sistema. O valor da profundidade da fila determina quantas operações de e/s o seu aplicativo pode alinhar, quais serão processadas pelos discos de armazenamento. Ele afeta todos os três indicadores de desempenho do aplicativo que discutimos neste artigo aula sobre visualização., IOPS, taxa de transferência e latência.

A profundidade da fila e multithreading estão profundamente relacionadas. O valor de profundidade da fila indica a quantidade de multithreading que pode ser obtida pelo aplicativo. Se a profundidade da fila for grande, o aplicativo poderá executar mais operações simultaneamente, em outras palavras, mais multithreading. Se a profundidade da fila for pequena, mesmo que o aplicativo tenha vários threads, ele não terá solicitações suficientes alinhadas para execução simultânea.

Normalmente, os aplicativos de prateleira não permitem que você altere a profundidade da fila, porque se estiver definido incorretamente, ele fará mais danos do que o bom. Os aplicativos definirão o valor certo da profundidade da fila para obter o desempenho ideal. No entanto, é importante entender esse conceito para que você possa solucionar problemas de desempenho com seu aplicativo. Você também pode observar os efeitos da profundidade da fila executando ferramentas de benchmark em seu sistema.

Alguns aplicativos fornecem configurações para influenciar a profundidade da fila. Por exemplo, a configuração MAXDOP (grau máximo de paralelismo) no SQL Server explicado na seção anterior. MAXDOP é uma maneira de influenciar a profundidade da fila e o multithreading, embora não altere diretamente o valor de profundidade da fila de SQL Server.

*Profundidade de fila alta*  
Uma profundidade de fila alta alinha mais operações no disco. O disco sabe a próxima solicitação em sua fila antes do tempo. Consequentemente, o disco pode agendar operações antecipadamente e processá-las em uma sequência ideal. Como o aplicativo está enviando mais solicitações ao disco, o disco pode processar mais IOs paralelos. Por fim, o aplicativo poderá alcançar um IOPS maior. Como o aplicativo está processando mais solicitações, a taxa de transferência total do aplicativo também aumenta.

Normalmente, um aplicativo pode alcançar a taxa de transferência máxima com 8-16 + IOs pendente por disco anexado. Se uma profundidade de fila for uma, o aplicativo não está enviando o IOs suficiente para o sistema e processará menos valor em um determinado período. Em outras palavras, menos taxa de transferência.

Por exemplo, em SQL Server, definir o valor MAXDOP de uma consulta como "4" informa SQL Server que ele pode usar até quatro núcleos para executar a consulta. SQL Server determinará qual é o melhor valor de profundidade da fila e o número de núcleos para a execução da consulta.

*Profundidade de fila ideal*  
Um valor de profundidade de fila muito alto também tem suas desvantagens. Se o valor de profundidade da fila for muito alto, o aplicativo tentará gerar IOPS muito alto. A menos que o aplicativo tenha discos persistentes com IOPS provisionados suficiente, isso pode afetar negativamente as latências do aplicativo. A fórmula a seguir mostra a relação entre IOPS, latência e profundidade de fila.  
    ![](media/premium-storage-performance/image6.png)

Você não deve configurar a profundidade da fila para nenhum valor alto, mas para um valor ideal, que pode fornecer IOPS suficiente para o aplicativo sem afetar as latências. Por exemplo, se a latência do aplicativo precisa ser de 1 milissegundo, a profundidade da fila necessária para alcançar 5.000 IOPS é, QD = 5000 x 0, 1 = 5.

*Profundidade da fila para o volume distribuído*  
Para um volume distribuído, mantenha uma profundidade de fila alta o suficiente, de modo que cada disco tenha uma profundidade de fila de pico individualmente. Por exemplo, considere um aplicativo que envia uma profundidade de fila de 2 e que há quatro discos na faixa. As duas solicitações de e/s vão para dois discos e os dois discos restantes ficarão ociosos. Portanto, configure a profundidade da fila de modo que todos os discos possam estar ocupados. A fórmula abaixo mostra como determinar a profundidade da fila de volumes distribuídos.  
    ![](media/premium-storage-performance/image7.png)

## <a name="throttling"></a>Limitação

O armazenamento Premium do Azure provisiona o número especificado de IOPS e taxa de transferência, dependendo dos tamanhos de VM e tamanhos de disco que você escolher. Sempre que seu aplicativo tentar gerar IOPS ou taxa de transferência acima desses limites do que a VM ou o disco pode lidar, o armazenamento Premium o reduzirá. Isso se manifesta na forma de degradação do desempenho em seu aplicativo. Isso pode significar maior latência, taxa de transferência mais baixa ou IOPS menor. Se o armazenamento Premium não for limitado, o aplicativo poderá falhar completamente, excedendo o que seus recursos são capazes de alcançar. Portanto, para evitar problemas de desempenho devido à limitação, sempre provisione recursos suficientes para seu aplicativo. Leve em consideração o que discutimos nas seções tamanhos de VM e tamanhos de disco acima. O parâmetro de comparação é a melhor maneira de descobrir quais recursos serão necessários para hospedar seu aplicativo.

## <a name="next-steps"></a>Passos seguintes
