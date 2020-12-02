---
title: 'Armazenamento Azure Premium: Design para alto desempenho'
description: Desenhe aplicações de alto desempenho utilizando discos geridos Azure Premium SSD. O Premium Storage oferece suporte a discos de alto desempenho e baixa latência para cargas de trabalho intensivas em I/O em funcionamento em Máquinas Virtuais Azure.
author: roygara
ms.service: virtual-machines
ms.topic: conceptual
ms.date: 10/05/2020
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: acdddcd95883d13393838a47281fb888ac2f9274
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/02/2020
ms.locfileid: "96500398"
---
# <a name="azure-premium-storage-design-for-high-performance"></a>Armazenamento premium Azure: design para alto desempenho

Este artigo fornece diretrizes para a construção de aplicações de alto desempenho utilizando o Azure Premium Storage. Pode utilizar as instruções fornecidas neste documento combinadas com as melhores práticas de desempenho aplicáveis às tecnologias utilizadas pela sua aplicação. Para ilustrar as diretrizes, utilizamos o SQL Server a funcionar no Premium Storage como exemplo ao longo deste documento.

Enquanto abordamos os cenários de desempenho para a camada de Armazenamento neste artigo, você precisará otimizar a camada de aplicação. Por exemplo, se estiver a hospedar uma SharePoint Farm no Azure Premium Storage, pode utilizar os exemplos do SQL Server deste artigo para otimizar o servidor de base de dados. Além disso, otimize o servidor web e o servidor de aplicação da SharePoint Farm para obter o maior desempenho.

Este artigo ajudará a responder após perguntas comuns sobre a otimização do desempenho da aplicação no Azure Premium Storage,

* Como medir o desempenho da sua candidatura?  
* Por que não estás a ver o desempenho esperado?  
* Quais os fatores que influenciam o desempenho da sua aplicação no Armazenamento Premium?  
* Como é que estes fatores influenciam o desempenho da sua aplicação no Premium Storage?  
* Como pode otimizar para IOPS, Largura de Banda e Latência?  

Fornecemos estas diretrizes especificamente para o Armazenamento Premium, porque as cargas de trabalho em execução no Armazenamento Premium são altamente sensíveis ao desempenho. Demos exemplos sempre que adequado. Também pode aplicar algumas destas diretrizes a aplicações em execução em IaaS VMs com discos de armazenamento padrão.

> [!NOTE]
> Às vezes, o que parece ser um problema de desempenho em disco é, na verdade, um estrangulamento de rede. Nestas situações, deverá otimizar o desempenho da sua [rede.](../virtual-network/virtual-network-optimize-network-bandwidth.md)
>
> Se procura comparar o seu disco, consulte os nossos artigos sobre o benchmarking de um disco:
>
> * Para Linux: [Benchmark a sua aplicação no Armazenamento de Disco Azure](linux/disks-benchmarks.md)
> * Para windows: [Benchmarking um disco](windows/disks-benchmarks.md).
>
> Se o seu VM suportar uma rede acelerada, deve certificar-se de que está ativado. Se não estiver ativado, pode ative-lo em VMs já implantados tanto no [Windows](../virtual-network/create-vm-accelerated-networking-powershell.md#enable-accelerated-networking-on-existing-vms) como no [Linux](../virtual-network/create-vm-accelerated-networking-cli.md#enable-accelerated-networking-on-existing-vms).

Antes de começar, se é novo no Premium Storage, leia primeiro o [tipo de disco Azure para alvos IaaS VMs](disks-types.md) e [Escalaability para contas de armazenamento de blob de página premium.](../storage/blobs/scalability-targets-premium-page-blobs.md)

## <a name="application-performance-indicators"></a>Indicadores de desempenho da aplicação

Avaliamos se uma aplicação está a ter um bom desempenho ou não utilizando indicadores de desempenho como, a rapidez com que uma aplicação está a processar um pedido de utilizador, quantos dados uma aplicação está a processar por pedido, quantos pedidos é um processamento de aplicação num determinado período de tempo, quanto tempo um utilizador tem de esperar para obter uma resposta depois de apresentar o seu pedido. Os termos técnicos para estes indicadores de desempenho são, IOPS, Produção ou Largura de Banda, e Latência.

Nesta secção, discutiremos os indicadores de desempenho comuns no contexto do Armazenamento Premium. Na secção seguinte, Requisitos de Aplicação de Recolha, aprenderá a medir estes indicadores de desempenho para a sua aplicação. Mais tarde, na Otimização do Desempenho da Aplicação, irás aprender sobre os fatores que afetam estes indicadores de desempenho e recomendações para os otimizar.

## <a name="iops"></a>IOPS

IOPS, ou Operações de Entrada/Saída Por Segundo, é o número de pedidos que a sua aplicação está a enviar para os discos de armazenamento num segundo. Uma operação de entrada/saída pode ser lida ou escrita, sequencial ou aleatória. As aplicações de Processamento de Transações Online (OLTP) como um website de retalho online precisam processar imediatamente muitos pedidos simultâneos de utilizadores. Os pedidos do utilizador são inserir e atualizar transações intensivas de base de dados, que a aplicação deve processar rapidamente. Portanto, as aplicações OLTP requerem IOPS muito elevado. Tais aplicações tratam de milhões de pequenos e aleatórios pedidos de OI. Se tiver tal aplicação, deve projetar a infraestrutura de aplicação para otimizar para o IOPS. Na secção posterior, Otimizando o *Desempenho da Aplicação,* discutimos detalhadamente todos os fatores que deve considerar para obter iops elevados.

Quando anexa um disco de armazenamento premium à sua VM de grande escala, o Azure aprovisiona um número garantido de IOPS de acordo com a especificação do disco. Por exemplo, um disco P50 aprovisiona 7500 IOPS. Cada tamanho de VM de grande escala tem também um limite de IOPS específico que pode sustentar. Por exemplo, um GS5 VM standard tem um limite de 80.000 IOPS.

## <a name="throughput"></a>Débito

A produção, ou largura de banda, é a quantidade de dados que a sua aplicação está a enviar para os discos de armazenamento num intervalo especificado. Se a sua aplicação estiver a realizar operações de entrada/saída com grandes tamanhos de unidade de IO, requer uma produção elevada. As aplicações de armazém de dados tendem a emitir operações intensivas de digitalização que acedem a grandes porções de dados de cada vez e geralmente realizam operações a granel. Por outras palavras, tais aplicações requerem uma produção mais elevada. Se tiver tal aplicação, tem de desenhar a sua infraestrutura para otimizar a sua produção. Na secção seguinte, discutimos em pormenor os fatores que deve sintonizar para o conseguir.

Quando se anexa um disco de armazenamento premium a um VM de alta escala, as provisões Azure são executadas de acordo com a especificação do disco. Por exemplo, um disco P50 aprovisiona um débito de disco de 250 MB por segundo. Cada tamanho de VM de grande escala tem também um limite de débito específico que pode sustentar. Por exemplo, uma VM Standard GS5 tem um débito máximo de 2000 MB por segundo.

Existe uma relação entre a produção e o IOPS, como mostrado na fórmula abaixo.

![Relação entre iops e produção](linux/media/premium-storage-performance/image1.png)

Por isso, é importante determinar os valores de produção e IOPS ideais que a sua aplicação requer. À medida que tenta otimizar um, o outro também é afetado. Numa secção posterior, *Otimizando o Desempenho da Aplicação,* discutiremos mais detalhes sobre a otimização do IOPS e do Throughput.

## <a name="latency"></a>Latência

Latência é o tempo que leva um pedido para receber um único pedido, enviá-lo para os discos de armazenamento e enviar a resposta ao cliente. Esta é uma medida crítica do desempenho de uma aplicação, além de IOPS e Produção. A Latência de um disco de armazenamento premium é o tempo que leva para recuperar a informação para um pedido e comunicá-la de volta à sua aplicação. O Armazenamento Premium proporciona latências baixas consistentes. Os Discos Premium são projetados para fornecer latências milissegundos de um dígito para a maioria das operações de IO. Se ativar o cache do anfitrião ReadOnly em discos de armazenamento premium, pode obter uma latência de leitura muito mais baixa. Discutiremos o Disk Caching mais detalhadamente na secção posterior sobre *otimização do desempenho da aplicação.*

Quando estiver a otimizar a sua aplicação para obter IOPS e Produção mais elevada, irá afetar a latência da sua aplicação. Depois de afinar o desempenho da aplicação, avalie sempre a latência da aplicação para evitar comportamentos inesperados de alta latência.

As seguintes operações de avião de controlo em Discos Geridos podem envolver o movimento do Disco de um local de armazenamento para outro. Isto é orquestrado através de cópia de fundo de dados que podem demorar várias horas a ser concluídos, tipicamente menos de 24 horas dependendo da quantidade de dados nos discos. Durante esse tempo, a sua aplicação pode experimentar uma latência de leitura superior ao habitual, uma vez que algumas leituras podem ser redirecionadas para o local original e podem demorar mais tempo a ser concluídas. Durante este período, não há qualquer impacto na latência da escrita.

- Atualizar o tipo de armazenamento.
- Desprender e prenda um disco de um VM ao outro.
- Crie um disco gerido a partir de um VHD.
- Crie um disco gerido a partir de uma imagem instantânea.
- Converter discos não geridos em discos geridos.

## <a name="performance-application-checklist-for-disks"></a>Lista de verificação de aplicações de desempenho para discos

O primeiro passo para conceber aplicações de alto desempenho em execução no Azure Premium Storage é compreender os requisitos de desempenho da sua aplicação. Depois de reunir os requisitos de desempenho, pode otimizar a sua aplicação para obter o melhor desempenho.

Na secção anterior, explicámos os indicadores de desempenho comuns, IOPS, Produção e Latência. Tem de identificar quais destes indicadores de desempenho são cruciais para a sua aplicação para fornecer a experiência desejada pelo utilizador. Por exemplo, o IOPS elevado é mais importante para aplicações OLTP que processam milhões de transações num segundo. Considerando que a produção elevada é fundamental para aplicações data Warehouse que processam grandes quantidades de dados num segundo. A latência extremamente baixa é crucial para aplicações em tempo real, como sites de streaming de vídeo ao vivo.

Em seguida, meça os requisitos máximos de desempenho da sua aplicação ao longo da sua vida útil. Utilize a lista de verificação abaixo como um começo. Registem os requisitos máximos de desempenho durante os períodos normais, de pico e fora de horas de trabalho. Ao identificar requisitos para todos os níveis de carga de trabalho, poderá determinar o requisito de desempenho geral da sua aplicação. Por exemplo, a carga de trabalho normal de um site de e-commerce serão as transações que serve durante a maioria dos dias de um ano. O pico de carga de trabalho do site serão as transações que serve durante a época de férias ou eventos especiais de venda. A carga de trabalho máxima é tipicamente experimentada por um período limitado, mas pode exigir que a sua aplicação escalone duas ou mais vezes o seu funcionamento normal. Descubra os requisitos percentuais de 50%, 90% e 99% de percentil. Isto ajuda a filtrar quaisquer outliers nos requisitos de desempenho e você pode concentrar os seus esforços na otimização para os valores certos.

## <a name="application-performance-requirements-checklist"></a>Lista de verificação dos requisitos de desempenho da aplicação

| **Requisitos de desempenho** | **50 Percentil** | **90 Percentil** | **99 Percentil** |
| --- | --- | --- | --- |
| Um máximo de Transações por segundo | | | |
| % Ler operações | | | |
| % Escrever operações | | | |
| % Operações aleatórias | | | |
| % Operações sequenciais | | | |
| Tamanho do pedido io | | | |
| Produção média | | | |
| Um máximo de Débito | | | |
| Min, min. Latência | | | |
| Latência média | | | |
| Um máximo de CPU | | | |
| CPU médio | | | |
| Um máximo de Memória | | | |
| Memória Média | | | |
| Profundidade da fila | | | |

> [!NOTE]
> Deve considerar a escala destes números com base no crescimento futuro esperado da sua aplicação. É uma boa ideia planear o crescimento com antecedência, porque poderia ser mais difícil mudar a infraestrutura para melhorar o desempenho mais tarde.

Se tiver uma aplicação existente e quiser mudar-se para o Premium Storage, primeiro construa a lista de verificação acima para a aplicação existente. Em seguida, construa um protótipo da sua aplicação no Armazenamento Premium e desenhe a aplicação com base nas diretrizes descritas na Otimização do Desempenho da *Aplicação* numa secção posterior deste documento. O artigo seguinte descreve as ferramentas que pode utilizar para recolher as medições de desempenho.

### <a name="counters-to-measure-application-performance-requirements"></a>Contadores para medir os requisitos de desempenho da aplicação

A melhor forma de medir os requisitos de desempenho da sua aplicação é utilizar ferramentas de monitorização de desempenho fornecidas pelo sistema operativo do servidor. Pode utilizar o PerfMon para Windows e iostat para Linux. Estas ferramentas capturam contadores correspondentes a cada medida explicada na secção acima. Deve capturar os valores destes contadores quando a sua aplicação está a executar as suas cargas de trabalho normais, de pico e fora de horas.

Os contadores PerfMon estão disponíveis para processador, memória e cada disco lógico e disco físico do seu servidor. Quando utiliza discos de armazenamento premium com um VM, os contadores de disco físico são para cada disco de armazenamento premium, e os contadores de disco lógicos são para cada volume criado nos discos de armazenamento premium. Tem de capturar os valores dos discos que acolhem a carga de trabalho da sua aplicação. Se houver um mapeamento entre discos lógicos e físicos, pode consultar os contadores de discos físicos; caso contrário, consulte os contadores de disco lógicos. No Linux, o comando do iostat gera um relatório de utilização de CPU e disco. O relatório de utilização do disco fornece estatísticas por dispositivo físico ou partição. Se tiver um servidor de base de dados com os seus dados e registos em discos separados, recolha estes dados para ambos os discos. Abaixo a tabela descreve contadores para discos, processadores e memória:

| Contador | Descrição | PerfMon | Iostat |
| --- | --- | --- | --- |
| **IOPS ou Transações por segundo** |Número de pedidos de E/S emitidos para o disco de armazenamento por segundo. |Leituras/seg de disco <br> Escritas/seg de disco |tps <br> r/s <br> c/s |
| **Leituras e escritos de disco** |% das operações de Leitura e Escrita realizadas no disco. |% Tempo de leitura do disco <br> % Tempo de escrita do disco |r/s <br> c/s |
| **Débito** |Quantidade de dados lidos ou escritos para o disco por segundo. |Discos ler Bytes/seg <br> Discos De Escrita Bytes/seg |kB_read/s <br> kB_wrtn/s |
| **Latência** |Tempo total para completar um pedido de IO do disco. |Média de disco/leitura <br> Média de disco/Escrita |aguardar <br> svctm |
| **Tamanho IO** |O tamanho dos pedidos de E/O para os discos de armazenamento. |Bytes médios de disco/leitura <br> Bytes médios de disco/escrita |avgrq-sz |
| **Profundidade da fila** |Número de pedidos de E/S pendentes à espera de serem lidos ou escritos para o disco de armazenamento. |Comprimento atual da fila do disco |avgqu-sz |
| **Már. Memória** |Quantidade de memória necessária para executar a aplicação sem problemas |% dos Bytes Consolidados em Utilização |Use vmstat |
| **Max. CPU** |Valor CPU necessário para executar a aplicação sem problemas |% tempo de processador |%util |

Saiba mais sobre [iostat](https://linux.die.net/man/1/iostat) e [PerfMon.](/windows/win32/perfctrs/performance-counters-portal)



## <a name="optimize-application-performance"></a>Otimizar o desempenho da aplicação

Os principais fatores que influenciam o desempenho de uma aplicação em execução no Armazenamento Premium são a Natureza dos pedidos de IO, tamanho VM, tamanho do disco, Número de discos, cache de disco, multi-leitura e profundidade de fila. Pode controlar alguns destes fatores com botões fornecidos pelo sistema. A maioria das aplicações pode não lhe dar a opção de alterar diretamente o tamanho de IO e a Profundidade da Fila. Por exemplo, se estiver a utilizar o SQL Server, não pode escolher o tamanho do IO e a profundidade da fila. O SQL Server escolhe os valores ideais de profundidade de IO e de fila para obter o maior desempenho. É importante compreender os efeitos de ambos os tipos de fatores no desempenho da sua aplicação, para que possa obter recursos adequados para atender às necessidades de desempenho.

Ao longo desta secção, consulte a lista de verificação de requisitos de aplicação que criou, para identificar o quanto precisa para otimizar o desempenho da sua aplicação. Com base nisso, poderá determinar quais os fatores desta secção que terá de sintonizar. Para testemunhar os efeitos de cada fator no desempenho da sua aplicação, execute ferramentas de benchmarking na configuração da sua aplicação. Consulte o artigo de Benchmarking, ligado no final, para obter medidas para executar ferramentas comuns de benchmarking em Windows e Linux VMs.

### <a name="optimize-iops-throughput-and-latency-at-a-glance"></a>Otimizar iOPS, produção e latência num ápice

O quadro abaixo resume os fatores de desempenho e os passos necessários para otimizar o IOPS, a produção e a latência. As secções que seguem este resumo descreverão cada fator é muito mais profunda.

Para obter mais informações sobre os tamanhos VM e sobre o IOPS, produção e latência disponíveis para cada tipo de VM, consulte [tamanhos para máquinas virtuais em Azure](sizes.md).

| | **IOPS** | **Débito** | **Latência** |
| --- | --- | --- | --- |
| **Cenário de exemplo** |Aplicação OLTP da Empresa que requer transações muito elevadas por segunda taxa. |Aplicação de armazenamento de dados da empresa que processa grandes quantidades de dados. |Aplicações quase em tempo real que requerem respostas instantâneas aos pedidos dos utilizadores, como jogos online. |
| **Fatores de desempenho** | &nbsp; | &nbsp; | &nbsp; |
| **Tamanho IO** |O tamanho IO menor produz IOPS mais alto. |Maior tamanho IO para rendimentos mais altos. | &nbsp;|
| **Tamanho VM** |Utilize um tamanho VM que ofereça IOPS superior ao seu requisito de aplicação. |Utilize um tamanho VM com limite de produção superior ao seu requisito de aplicação. |Utilize um tamanho VM que ofereça limites de escala superiores aos requisitos da sua aplicação. |
| **Tamanho do disco** |Utilize um tamanho de disco que ofereça IOPS superior ao seu requisito de aplicação. |Utilize um tamanho de disco com limite de potência superior ao seu requisito de aplicação. |Utilize um tamanho de disco que ofereça limites de escala superiores aos requisitos da sua aplicação. |
| **Limites de escala de VM e disco** |O limite de IOPS do tamanho VM escolhido deve ser maior do que o total de IOPS impulsionado por discos de armazenamento ligados a ele. |O limite de produção do tamanho VM escolhido deve ser maior do que o total de Produção impulsionado por discos de armazenamento premium ligados a ele. |Os limites de escala do tamanho VM escolhidos devem ser superiores aos limites de escala totais dos discos de armazenamento premium anexados. |
| **Caching de disco** |Ativar a Cache ReadOnly em discos de armazenamento premium com leia operações pesadas para obter IOPS de leitura mais alta. | &nbsp; |Ativar a Cache ReadOnly em discos de armazenamento premium com operações pesadas prontas para obter latências de leitura muito baixas. |
| **Striping de disco** |Use vários discos e encoste-os juntos para obter um limite combinado de IOPS e De saída. O limite combinado por VM deve ser superior aos limites combinados dos discos premium anexados. | &nbsp; | &nbsp; |
| **Tamanho das riscas** |Tamanho de listra menor para padrão IO pequeno aleatório visto em aplicações OLTP. Por exemplo, utilize o tamanho das riscas de 64 KB para a aplicação OLTP do servidor SQL. |Tamanho de listra maior para padrão de IO grande sequencial visto nas aplicações data Warehouse. Por exemplo, utilize o tamanho de listras KB de 256 KB para a aplicação de armazém de dados do sql server. | &nbsp; |
| **Multithreading** |Utilize multi-leituras para empurrar um maior número de pedidos para o Armazenamento Premium que levará a iops e produção mais elevados. Por exemplo, no SQL Server definir um valor MAXDOP elevado para alocar mais CPUs ao SQL Server. | &nbsp; | &nbsp; |
| **Profundidade da fila** |Maior profundidade de fila produz IOPS mais elevados. |Maior profundidade de fila produz maior produção. |Menor profundidade de fila produz latências mais baixas. |

## <a name="nature-of-io-requests"></a>Natureza dos pedidos de OI

Um pedido de IO é uma unidade de operação de entrada/saída que a sua aplicação irá realizar. Identificar a natureza dos pedidos de IO, aleatórios ou sequenciais, ler ou escrever, pequenos ou grandes, irá ajudá-lo a determinar os requisitos de desempenho da sua aplicação. É importante compreender a natureza dos pedidos de IO, tomar as decisões certas ao conceber a sua infraestrutura de aplicação. Os IOs devem ser distribuídos uniformemente para alcançar o melhor desempenho possível.

O tamanho do IO é um dos fatores mais importantes. O tamanho IO é o tamanho do pedido de operação de entrada/saída gerado pela sua aplicação. O tamanho do IO tem um impacto significativo no desempenho, especialmente no IOPS e largura de banda que a aplicação é capaz de alcançar. A fórmula a seguir mostra a relação entre o IOPS, o tamanho IO e a largura de banda/produção.  
    ![Um diagrama que mostra a equação I O P S vezes O tamanho igual a Produção.](media/premium-storage-performance/image1.png)

Algumas aplicações permitem alterar o seu tamanho de IO, enquanto algumas aplicações não. Por exemplo, o SQL Server determina o tamanho ideal do IO em si e não fornece aos utilizadores quaisquer botões para alterá-lo. Por outro lado, a Oracle fornece um parâmetro chamado [DB \_ BLOCK \_ SIZE](https://docs.oracle.com/cd/B19306_01/server.102/b14211/iodesign.htm#i28815) utilizando o qual pode configurar o tamanho do pedido de E/S da base de dados.

Se estiver a utilizar uma aplicação, que não lhe permite alterar o tamanho do IO, utilize as diretrizes deste artigo para otimizar o desempenho KPI que é mais relevante para a sua aplicação. Por exemplo,

* Uma aplicação OLTP gera milhões de pequenos e aleatórios pedidos de IO. Para lidar com este tipo de pedidos de IO, você deve projetar a sua infraestrutura de aplicação para obter IOPS mais alto.  
* Uma aplicação de armazenamento de dados gera pedidos de IO grandes e sequenciais. Para lidar com este tipo de pedidos de IO, você deve projetar a sua infraestrutura de aplicação para obter largura de banda ou produção mais alta.

Se estiver a utilizar uma aplicação que lhe permite alterar o tamanho do IO, utilize esta regra do polegar para o tamanho IO, além de outras diretrizes de desempenho,

* Tamanho IO menor para obter IOPS mais alto. Por exemplo, 8 KB para uma aplicação OLTP.  
* Maior tamanho IO para obter largura de banda/produção de largura de banda mais alta. Por exemplo, 1024 KB para uma aplicação de armazém de dados.

Aqui está um exemplo de como pode calcular o IOPS e a Largura de Banda/Produção/Banda para a sua aplicação. Considere uma aplicação utilizando um disco P30. O máximo de IOPS e Produção/Largura de Banda que um disco P30 pode alcançar é de 5000 IOPS e 200 MB por segundo, respectivamente. Agora, se a sua aplicação necessitar do máximo de IOPS do disco P30 e utilizar um tamanho IO menor como 8 KB, a largura de banda resultante que poderá obter é de 40 MB por segundo. No entanto, se a sua aplicação necessitar da potência máxima de produção/largura de banda do disco P30, e utilizar um tamanho IO maior como 1024 KB, o IOPS resultante será inferior, 200 IOPS. Por isso, afina o tamanho IO de modo a que satisfaça tanto o requisito de IOPS da sua aplicação como o requisito de produção/largura de banda. A tabela seguinte resume os diferentes tamanhos de IO e os respetivos IOPS e produção para um disco P30.

| Requisito de aplicação | Tamanho de I/O | IOPS | Produção/Largura de Banda |
| --- | --- | --- | --- |
| IOPS Máximo |8 KB |5000 |40 MB por segundo |
| Produção máxima |1024 KB |200 |200 MB por segundo |
| Max Throughput + IOPS alto |64 KB |3,200 |200 MB por segundo |
| Max IOPS + produção alta |32 KB |5000 |160 MB por segundo |

Para obter IOPS e Largura de Banda superior ao valor máximo de um disco de armazenamento premium único, utilize vários discos premium listrados em conjunto. Por exemplo, listre dois discos P30 para obter um IOPS combinado de 10.000 IOPS ou um Rendimento combinado de 400 MB por segundo. Como explicado na secção seguinte, deve utilizar um tamanho VM que suporte o disco combinado IOPS e o Throughput.

> [!NOTE]
> À medida que aumenta o IOPS ou o Throughput, o outro também aumenta, certifique-se de que não atinge os limites de produção ou IOPS do disco ou VM ao aumentar qualquer um.

Para testemunhar os efeitos do tamanho do IO no desempenho da aplicação, pode executar ferramentas de benchmarking nos seus VM e discos. Crie várias correções de teste e use diferentes tamanhos de IO para cada corrida para ver o impacto. Consulte o artigo de Benchmarking, ligado no final, para obter mais detalhes.

## <a name="high-scale-vm-sizes"></a>Tamanhos VM de alta escala

Quando começar a desenhar uma aplicação, uma das primeiras coisas a fazer é escolher um VM para hospedar a sua aplicação. O Premium Storage vem com tamanhos VM de alta escala que podem executar aplicações que requerem maior potência de computação e um alto desempenho em disco local de E/S. Estes VMs fornecem processadores mais rápidos, uma relação memória-core mais alta e uma unidade de Solid-State (SSD) para o disco local. Exemplos de VMs de alta escala que suportam o Armazenamento Premium são os VMs da série DS e GS.

VMs de alta escala estão disponíveis em diferentes tamanhos com um número diferente de núcleos de CPU, memória, SO e tamanho do disco temporário. Cada tamanho VM também tem o número máximo de discos de dados que pode anexar ao VM. Portanto, o tamanho VM escolhido irá afetar a quantidade de processamento, memória e capacidade de armazenamento disponível para a sua aplicação. Também afeta o custo de Computação e Armazenamento. Por exemplo, abaixo estão as especificações do maior tamanho VM de uma série DS e uma série GS:

| Tamanho da VM | Núcleos de CPU | Memória | Tamanhos do disco VM | Um máximo de discos de dados | Tamanho da cache | IOPS | Limites cache IO de largura de banda |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_DS14 |16 |112 GB |OS = 1023 GB <br> Local SSD = 224 GB |32 |576 GB |50.000 OPS <br> 512 MB por segundo |4.000 IOPS e 33 MB por segundo |
| Standard_GS5 |32 |448 GB |OS = 1023 GB <br> Local SSD = 896 GB |64 |4224 GB |80.000 OPS <br> 2.000 MB por segundo |5.000 IOPS e 50 MB por segundo |

Para ver uma lista completa de todos os tamanhos Azure VM disponíveis, consulte [tamanhos para máquinas virtuais em Azure](sizes.md) ou . Escolha um tamanho VM que possa atender e escalar para os requisitos de desempenho da aplicação pretendidos. Além disso, tome em consideração a seguintes considerações importantes na escolha dos tamanhos de VM.

*Limites de escala*  
Os limites máximos de IOPS por VM e por disco são diferentes e independentes uns dos outros. Certifique-se de que a aplicação está a conduzir o IOPS dentro dos limites do VM, bem como os discos premium que lhe estão associados. Caso contrário, o desempenho da aplicação experimentará estrangulamento.

Como exemplo, suponha que um requisito de aplicação seja um máximo de 4.000 IOPS. Para isso, você fornece um disco P30 num DS1 VM. O disco P30 pode entregar até 5.000 IOPS. No entanto, o DS1 VM está limitado a 3.200 IOPS. Consequentemente, o desempenho da aplicação será limitado pelo limite de VM em 3.200 IOPS e haverá desempenho degradado. Para evitar esta situação, escolha um tamanho de VM e disco que satisfaça os requisitos da aplicação.

*Custo de Operação*  
Em muitos casos, é possível que o seu custo global de operação usando o Armazenamento Premium seja menor do que o uso de Armazenamento Padrão.

Por exemplo, considere uma aplicação que requer 16.000 IOPS. Para alcançar este desempenho, necessitará de um \_ Standard D14 Azure IaaS VM, que pode dar um máximo de 16.000 IOPS utilizando 32 discos de armazenamento padrão 1 TB. Cada disco de armazenamento padrão de 1-TB pode atingir um máximo de 500 IOPS. O custo estimado deste VM por mês será de $1.570. O custo mensal de 32 discos de armazenamento padrão será $1.638. O custo total mensal estimado será de $3.208.

No entanto, se hospedar a mesma aplicação no Premium Storage, necessitará de um tamanho VM menor e menos discos de armazenamento premium, reduzindo assim o custo global. Um \_ Standard DS13 VM pode cumprir o requisito de 16.000 IOPS usando quatro discos P30. O DS13 VM tem um IOPS máximo de 25.600 e cada disco P30 tem um IOPS máximo de 5.000. No geral, esta configuração pode alcançar 5.000 x 4 = 20.000 IOPS. O custo estimado deste VM por mês será de $1.003. O custo mensal de quatro discos de armazenamento premium P30 será de $544,34. O custo total mensal estimado será de $1.544.

Tabela abaixo resume a repartição de custos deste cenário para o Armazenamento Standard e Premium.

| &nbsp; | **Standard** | **Premium** |
| --- | --- | --- |
| **Custo de VM por mês** |$1.570,58 (Standard \_ D14) |$1.003.66 (Standard \_ DS13) |
| **Custo dos Discos por mês** |$1.638,40 (discos de 32 x 1-TB) |$544.34 (discos 4 x P30) |
| **Custo Global por mês** |$3.208.98 |$1.544.34 |

*Linux Distros*  

Com o Azure Premium Storage, obtém-se o mesmo nível de Desempenho para VMs que executam Windows e Linux. Apoiamos muitos sabores de distros Linux, e você pode ver a lista completa [aqui.](linux/endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) É importante notar que diferentes distros são mais adequados para diferentes tipos de cargas de trabalho. Verá diferentes níveis de desempenho dependendo do distro em que a sua carga de trabalho está a funcionar. Teste os distros Linux com a sua aplicação e escolha o que funciona melhor.

Ao executar o Linux com Armazenamento Premium, verifique as últimas atualizações sobre os controladores necessários para garantir um desempenho elevado.

## <a name="premium-storage-disk-sizes"></a>Tamanhos de disco de armazenamento premium

O Azure Premium Storage oferece uma variedade de tamanhos para que possa escolher um que melhor se adapte às suas necessidades. Cada tamanho do disco tem um limite de escala diferente para IOPS, largura de banda e armazenamento. Escolha o tamanho certo do disco de armazenamento Premium, dependendo dos requisitos de aplicação e do tamanho de VM de alta escala. A tabela abaixo mostra os tamanhos dos discos e as suas capacidades. Os tamanhos P4, P6, P15, P60, P70 e P80 são atualmente suportados apenas para Discos Geridos.

[!INCLUDE [disk-storage-premium-ssd-sizes](../../includes/disk-storage-premium-ssd-sizes.md)]

Quantos discos escolhes depende do tamanho do disco escolhido. Pode utilizar um disco P50 ou vários discos P10 para satisfazer os requisitos da sua aplicação. Tome em consideração as considerações listadas abaixo ao fazer a escolha.

*Limites de escala (IOPS e Produção)*  
Os limites de IOPS e Deprodução de cada tamanho de disco Premium são diferentes e independentes dos limites de escala VM. Certifique-se de que o total de IOPS e de produção dos discos estão dentro dos limites de escala do tamanho de VM escolhido.

Por exemplo, se um requisito de aplicação for um prazo máximo de 250 MB/sec e estiver a utilizar um DS4 VM com um único disco P30. O DS4 VM pode dar até 256 MB/seg. No entanto, um único disco P30 tem limite de produção de 200 MB/seg. Consequentemente, a aplicação será limitada a 200 MB/seg devido ao limite do disco. Para ultrapassar este limite, fori-lhe mais de um disco de dados para o VM ou redimensionar os discos para P40 ou P50.

> [!NOTE]
> As leituras servidas pela cache não estão incluídas no disco IOPS e Naprodução, pelo que não estão sujeitas a limites de disco. A Cache tem o seu limite de IOPS e de produção separado por VM.
>
> Por exemplo, inicialmente as suas leituras e escritas são de 60MB/seg e 40MB/seg, respectivamente. Com o tempo, a cache aquece e serve cada vez mais as leituras da cache. Em seguida, pode obter uma escrita mais alta do disco.

*Número de Discos*  
Determine o número de discos que necessitará avaliando os requisitos de aplicação. Cada tamanho VM também tem um limite no número de discos que pode anexar ao VM. Normalmente, este é o dobro do número de núcleos. Certifique-se de que o tamanho VM que escolhe pode suportar o número de discos necessários.

Lembre-se, os discos de armazenamento Premium têm capacidades de desempenho mais elevadas em comparação com os discos de armazenamento padrão. Portanto, se estiver a migrar a sua aplicação de Azure IaaS VM usando o Armazenamento Padrão para Armazenamento Premium, provavelmente necessitará de menos discos premium para obter o mesmo ou maior desempenho para a sua aplicação.

## <a name="disk-caching"></a>Colocação em cache do disco

Os VMs de alta escala que alavancam o Azure Premium Storage têm uma tecnologia de caching multi-nível chamada BlobCache. BlobCache usa uma combinação da RAM hospedeira e SSD local para o caching. Esta cache está disponível para os discos persistentes de Armazenamento Premium e os discos locais VM. Por predefinição, esta definição de cache é definida para Ler/Escrever para discos DE ES e ReadOnly para discos de dados alojados no Armazenamento Premium. Com o cache do disco ativado nos discos de armazenamento Premium, os VMs de alta escala podem atingir níveis de desempenho extremamente elevados que excedem o desempenho subjacente do disco.

> [!WARNING]
> O Caching do disco não é suportado para discos 4 TiB e maiores. Se vários discos estiverem ligados ao seu VM, cada disco que seja menor que 4 TiB irá suportar o cache.
>
> Alterar a definição de cache de um disco do Azure desanexa e anexa o disco de destino. Se for o disco do sistema operativo, o VM é reiniciado. Pare todas as aplicações/serviços que possam ser afetadas por esta interrupção antes de alterar a definição da cache do disco. Não seguir estas recomendações pode levar à corrupção de dados.

Para saber mais sobre como funciona o BlobCache, consulte o post de blog Inside [Azure Premium Storage.](https://azure.microsoft.com/blog/azure-premium-storage-now-generally-available-2/)

É importante ativar a cache no conjunto certo de discos. Se deve ativar o cache do disco num disco premium ou não dependerá do padrão de carga de trabalho que o disco irá manipular. A tabela abaixo mostra as definições de cache predefinidos para discos de SO e Dados.

| **Tipo de disco** | **Definição de cache predefinido** |
| --- | --- |
| Disco do SO |ReadWrite |
| Disco de dados |ReadOnly |

Seguem-se as definições recomendadas de cache de disco para discos de dados,

| **Definição de cache de disco** | **recomendação sobre quando usar esta definição** |
| --- | --- |
| Nenhum |Configure a cache do hospedeiro como Nenhum para discos só de escrita e de escrita pesada. |
| ReadOnly |Configure a cache do anfitrião como ReadOnly para discos de leitura e leitura. |
| ReadWrite |Configure a cache do hospedeiro como ReadWrite apenas se a sua aplicação manusear corretamente a escrita de dados em cache para discos persistentes quando necessário. |

*ReadOnly*  
Ao configurar o ReadOnly caching em discos de dados de armazenamento premium, pode obter baixa latência de Leitura e obter IOPS e Produção de Leitura muito elevados para a sua aplicação. Isto deve-se a duas razões,

1. As leituras realizadas a partir de cache, que está na memória VM e SSD local, são muito mais rápidas do que as leituras do disco de dados, que está no armazenamento de bolhas Azure.  
1. Premium Armazenamento não conta as Leituras servidas a partir de cache, para o disco IOPS e Produção. Portanto, a sua aplicação é capaz de alcançar um total mais elevado de IOPS e Produção.

*LerWrite*  
Por predefinição, os discos OS têm o cache ReadWrite ativado. Recentemente, acrescentámos suporte para o cache readWrite em discos de dados também. Se estiver a utilizar o cache ReadWrite, deve ter uma forma adequada de escrever os dados da cache para discos persistentes. Por exemplo, o SQL Server trata da escrita de dados em cache para os discos de armazenamento persistentes por si só. A utilização da cache ReadWrite com uma aplicação que não lida com a persistência dos dados necessários pode levar à perda de dados, se o VM falhar.

*Nenhuma*  
Atualmente, **nenhum** é suportado apenas em discos de dados. Não é suportado em discos de SO. Se colocar **Nenhum** num disco DE, irá sobrepor-se internamente e defini-lo-á para **ReadOnly**.

Como exemplo, pode aplicar estas diretrizes ao SQL Server em execução no Armazenamento Premium, fazendo o seguinte,

1. Configure a cache "ReadOnly" em discos de armazenamento premium que hospedam ficheiros de dados.  
   a.  As leituras rápidas a partir da cache baixam o tempo de consulta do SQL Server, uma vez que as páginas de dados são recuperadas muito mais rapidamente da cache em comparação com diretamente dos discos de dados.  
   b.  Servir leituras de cache, significa que há Um Rendimento adicional disponível a partir de discos de dados premium. O SQL Server pode utilizar este Serviço adicional para recuperar mais páginas de dados e outras operações como backup/restauro, cargas de lote e reconstruções de índices.  
1. Configure a cache "Nenhum" nos discos de armazenamento premium que hospedam os ficheiros de registo.  
   a.  Os ficheiros de registo têm principalmente operações pesadas de escrita. Portanto, não beneficiam da cache ReadOnly.

## <a name="optimize-performance-on-linux-vms"></a>Otimizar o desempenho nos VMs do Linux

Para todos os SSDs premium ou discos ultra, pode ser capaz de desativar "barreiras" para sistemas de ficheiros no disco, de modo a melhorar o desempenho quando se sabe que não existem caches que possam perder dados.  Se o cache do disco Azure estiver definido para ReadOnly ou None, pode desativar barreiras.  Mas se o caching estiver definido para ReadWrite, as barreiras devem permanecer habilitados para garantir a durabilidade da escrita.  As barreiras são normalmente ativadas por padrão, mas pode desativar barreiras utilizando um dos seguintes métodos dependendo do tipo do sistema de ficheiros:

* Para **o reiserFS,** utilize a opção de montagem barreira=nenhuma para desativar barreiras.  Para ativar explicitamente as barreiras, utilize barreira=flush.
* Para **ext3/ext4,** utilize a opção de montagem barreira=0 para desativar barreiras.  Para ativar explicitamente as barreiras, utilize a barreira=1.
* Para **xFS,** utilize a opção de montagem nobarrier para desativar barreiras.  Para ativar explicitamente as barreiras, utilize a barreira.  Note que em versões posteriores do kernel do Linux, o design do sistema de ficheiros XFS garante sempre a durabilidade, e as barreiras incapacitantes não têm qualquer efeito.  

## <a name="disk-striping"></a>Striping de disco

Quando um VM de alta escala é anexado com vários discos persistentes de armazenamento premium, os discos podem ser listrados em conjunto para agregar os seus IOPs, largura de banda e capacidade de armazenamento.

No Windows, pode utilizar espaços de armazenamento para riscar discos em conjunto. Deve configurar uma coluna para cada disco numa piscina. Caso contrário, o desempenho global do volume listrado pode ser inferior ao esperado, devido à distribuição desigual do tráfego através dos discos.

Importante: Utilizando o UI do Gestor do Servidor, pode definir o número total de colunas até 8 para um volume listrado. Ao fixar mais de oito discos, utilize o PowerShell para criar o volume. Utilizando o PowerShell, pode definir o número de colunas igual ao número de discos. Por exemplo, se houver 16 discos num único conjunto de listras; especificar 16 colunas no parâmetro *NumberOfColumns* do *cmdlet PowerShell New-VirtualDisk.*

No Linux, utilize o utilitário MDADM para riscar discos juntos. Para passos detalhados sobre discos de strip no Linux, consulte o [Configure Software RAID no Linux](/previous-versions/azure/virtual-machines/linux/configure-raid).

*Tamanho das riscas*  
Uma configuração importante no striping do disco é o tamanho das listras. O tamanho das listras ou o tamanho do bloco é o menor pedaço de dados que a aplicação pode endereçar num volume listrado. O tamanho das listras que configura depende do tipo de aplicação e do seu padrão de pedido. Se escolher o tamanho errado da listra, pode levar ao desalinhamento do IO, o que leva a um desempenho degradado da sua aplicação.

Por exemplo, se um pedido de IO gerado pela sua aplicação for maior do que o tamanho da risca de disco, o sistema de armazenamento escreve-o através dos limites da unidade de listras em mais de um disco. Quando chegar a hora de aceder a esses dados, terá de procurar mais de uma unidade de listras para completar o pedido. O efeito cumulativo de tal comportamento pode levar a uma degradação substancial do desempenho. Por outro lado, se o tamanho do pedido de IO for menor do que o tamanho das listras, e se for de natureza aleatória, os pedidos de IO podem somar no mesmo disco causando um estrangulamento e, em última análise, degradando o desempenho do OI.

Dependendo do tipo de carga de trabalho que a sua aplicação está a executar, escolha um tamanho adequado de listras. Para pedidos aleatórios de IO, utilize um tamanho de listra menor. Enquanto que para pedidos de IO sequenciais grandes utiliza um tamanho de listra maior. Descubra as recomendações de tamanho de listras para a aplicação que estará a executar no Premium Storage. Para o SQL Server, configurar o tamanho das listagens de 64 KB para cargas de trabalho OLTP e 256 KB para cargas de trabalho de armazenamento de dados. Consulte [as melhores práticas de desempenho para SQL Server em VMs Azure](../azure-sql/virtual-machines/windows/performance-guidelines-best-practices.md#disks-guidance) para saber mais.

> [!NOTE]
> Pode juntar um máximo de 32 discos de armazenamento premium numa série DS VM e 64 discos de armazenamento premium numa série GS VM.

## <a name="multi-threading"></a>Multi-rosca

A Azure desenhou a plataforma premium Storage para ser massivamente paralela. Portanto, uma aplicação multi-roscada obtém um desempenho muito maior do que uma aplicação de rosca única. Uma aplicação multi-roscada divide as suas tarefas através de múltiplos fios e aumenta a eficiência da sua execução utilizando o VM e os recursos de disco ao máximo.

Por exemplo, se a sua aplicação estiver a funcionar num único núcleo VM utilizando duas linhas, o CPU pode alternar entre as duas linhas para obter eficiência. Enquanto um fio está à espera de um IO de disco para completar, o CPU pode mudar para o outro fio. Desta forma, dois fios podem realizar mais do que um único fio. Se o VM tiver mais do que um núcleo, diminui ainda mais o tempo de funcionamento, uma vez que cada núcleo pode executar tarefas em paralelo.

Pode não ser capaz de alterar a forma como uma aplicação fora da prateleira implementa um único rosco ou multi-rosca. Por exemplo, o SQL Server é capaz de lidar com multi-CPU e multi-core. No entanto, o SQL Server decide em que condições irá aproveitar um ou mais fios para processar uma consulta. Pode executar consultas e construir índices usando multi-rosca. Para uma consulta que envolva juntar grandes tabelas e classificar dados antes de voltar ao utilizador, o SQL Server provavelmente utilizará vários fios. No entanto, um utilizador não pode controlar se o SQL Server executa uma consulta utilizando um único fio ou múltiplos fios.

Existem configurações que pode alterar para influenciar este processamento multi-roscado ou paralelo de uma aplicação. Por exemplo, no caso do SQL Server é o grau máximo de configuração do paralelo. Esta definição chamada MAXDOP, permite-lhe configurar o número máximo de processadores que o SQL Server pode utilizar quando o processamento paralelo. Pode configurar MAXDOP para consultas individuais ou operações de índice. Isto é benéfico quando se pretende equilibrar recursos do seu sistema para uma aplicação crítica de desempenho.

Por exemplo, digamos que a sua aplicação utilizando o SQL Server está a executar uma grande consulta e uma operação de índice ao mesmo tempo. Assumamos que queria que a operação de índice fosse mais performante em comparação com a grande consulta. Neste caso, pode definir o valor MAXDOP da operação do índice para ser superior ao valor MAXDOP para a consulta. Desta forma, o SQL Server tem mais processadores que pode alavancar para a operação do índice em comparação com o número de processadores que pode dedicar à grande consulta. Lembre-se que não controla o número de fios que o SQL Server utilizará para cada operação. Pode controlar o número máximo de processadores que estão a ser dedicados a multi-threading.

Saiba mais sobre [Os Graus de Paralelos](/previous-versions/sql/sql-server-2008-r2/ms188611(v=sql.105)) no SQL Server. Descubra tais configurações que influenciam o multi-threading na sua aplicação e as suas configurações para otimizar o desempenho.

## <a name="queue-depth"></a>Profundidade da fila

A profundidade da fila ou o comprimento da fila ou o tamanho da fila é o número de pedidos de IO pendentes no sistema. O valor da profundidade da fila determina quantas operações de IO a sua aplicação pode alinhar, quais os discos de armazenamento que serão processados. Afeta todos os três indicadores de desempenho da aplicação que discutimos neste artigo viz., IOPS, produção e latência.

A profundidade da fila e a multi-rosca estão intimamente relacionadas. O valor de profundidade de fila indica quanto multi-rosca pode ser alcançado pela aplicação. Se a Profundidade de Fila for grande, a aplicação pode executar mais operações simultaneamente, ou seja, mais multi-threading. Se a Profundidade de Fila for pequena, mesmo que a aplicação seja multi-roscada, não terá pedidos suficientes alinhados para execução simultânea.

Normalmente, fora das aplicações da prateleira não lhe permite alterar a profundidade da fila, porque se estiver definido incorretamente, fará mais mal do que bem. As aplicações definirão o valor certo da profundidade da fila para obter o melhor desempenho. No entanto, é importante compreender este conceito para que possa resolver problemas de desempenho com a sua aplicação. Também pode observar os efeitos da profundidade da fila executando ferramentas de benchmarking no seu sistema.

Algumas aplicações fornecem configurações para influenciar a Profundidade da Fila. Por exemplo, a definição MAXDOP (grau máximo de paralelismo) no SQL Server explicada na secção anterior. MAXDOP é uma forma de influenciar a profundidade da fila e a multi-rosca, embora não altere diretamente o valor de Profundidade de Fila do SQL Server.

*Alta profundidade da fila*  
Uma alta profundidade de fila alinha mais operações no disco. O disco sabe o próximo pedido na sua fila antes do tempo. Consequentemente, o disco pode agendar as operações com antecedência e processá-las numa sequência ideal. Uma vez que a aplicação está a enviar mais pedidos para o disco, o disco pode processar IOs mais paralelos. Em última análise, a aplicação será capaz de alcançar um IOPS mais elevado. Uma vez que a aplicação está a processar mais pedidos, o total de produção da aplicação também aumenta.

Normalmente, uma aplicação pode alcançar o máximo de Produção com 8-16+ IOs pendentes por disco anexo. Se uma profundidade de fila for uma, a aplicação não está a empurrar iOs suficientes para o sistema, e irá processar menos quantidade de um determinado período. Por outras palavras, menos Produção.

Por exemplo, no SQL Server, definir o valor MAXDOP para uma consulta a "4" informa o SQL Server que pode usar até quatro núcleos para executar a consulta. O SQL Server determinará qual o melhor valor de profundidade da fila e o número de núcleos para a execução de consultas.

*Profundidade de fila ideal*  
O valor de profundidade de fila muito elevado também tem as suas desvantagens. Se o valor de profundidade da fila for demasiado elevado, a aplicação tentará conduzir IOPS muito elevado. A menos que a aplicação tenha discos persistentes com IOPS suficientes, isso pode afetar negativamente as latências da aplicação. A fórmula seguinte mostra a relação entre iops, latência e profundidade de fila.  
    ![Um diagrama que mostra a equação I O P S vezes latência é igual a profundidade de fila.](media/premium-storage-performance/image6.png)

Não deve configurar a Profundidade da Fila a qualquer valor elevado, mas a um valor ideal, que pode fornecer IOPS suficiente para a aplicação sem afetar as latências. Por exemplo, se a latência da aplicação precisar de ser de 1 milissegundo, a Profundidade de Fila necessária para atingir 5.000 IOPS é, QD = 5000 x 0,001 = 5.

*Profundidade da fila para volume listrado*  
Para um volume listrado, mantenha uma profundidade de fila suficientemente alta para que, cada disco tenha uma profundidade de fila de pico individualmente. Por exemplo, considere uma aplicação que empurre uma profundidade de fila de 2 e há quatro discos na listra. Os dois pedidos de IO irão para dois discos e os restantes dois discos ficarão inativos. Portanto, configurar a profundidade da fila de modo que todos os discos possam estar ocupados. A fórmula abaixo mostra como determinar a profundidade da fila dos volumes listrados.  
    ![Um diagrama que mostra a equação Q D por Disco vezes o número de colunas por volume é igual a Q D de Volume Listrado.](media/premium-storage-performance/image7.png)

## <a name="throttling"></a>Limitação

As disposições de Armazenamento Azure Premium especificaram o número de IOPS e Depute dependendo dos tamanhos de VM e dos tamanhos do disco que escolher. Sempre que a sua aplicação tentar conduzir IOPS ou Produção acima destes limites do que o VM ou o disco podem manusear, o Premium Storage irá estrangulá-lo. Isto manifesta-se sob a forma de desempenho degradado na sua aplicação. Isto pode significar maior latência, menor produção ou iops mais baixo. Se o Armazenamento Premium não acelerar, a sua aplicação poderá falhar completamente excedendo o que os seus recursos são capazes de alcançar. Assim, para evitar problemas de desempenho devido a estrangulamento, sempre fornecendo recursos suficientes para a sua aplicação. Tome em consideração o que discutimos nas secções de tamanhos de VM e tamanhos de disco acima. O benchmarking é a melhor maneira de descobrir quais os recursos necessários para hospedar a sua aplicação.

## <a name="next-steps"></a>Passos seguintes

Se procura comparar o seu disco, consulte os nossos artigos sobre o benchmarking de um disco:

* Para Linux: [Benchmark a sua aplicação no Armazenamento de Disco Azure](linux/disks-benchmarks.md)
* Para windows: [Benchmarking um disco](windows/disks-benchmarks.md).

Saiba mais sobre os tipos de disco disponíveis:

* Para Linux: [Selecione um tipo de disco](disks-types.md)
* Para Windows: [Selecione um tipo de disco](disks-types.md)

Para os utilizadores do SQL Server, leia artigos sobre as melhores práticas de desempenho para o SQL Server:

* [Melhores práticas de desempenho para o SQL Server em Máquinas Virtuais Azure](../azure-sql/virtual-machines/windows/performance-guidelines-best-practices.md)
* [O Azure Premium Storage proporciona o melhor desempenho para o SQL Server em Azure VM](https://cloudblogs.microsoft.com/sqlserver/2015/04/23/azure-premium-storage-provides-highest-performance-for-sql-server-in-azure-vm/)