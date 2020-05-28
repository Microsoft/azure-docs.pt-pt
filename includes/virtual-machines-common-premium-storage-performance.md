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
ms.openlocfilehash: 2c8c0430e8a1f54daa99d3fd986bae0c3eaf7f61
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "84017614"
---
## <a name="application-performance-indicators"></a>Indicadores de desempenho da aplicação

Avaliamos se uma aplicação está a ter um bom desempenho ou não utilizando indicadores de desempenho como, a rapidez com que uma aplicação está a processar um pedido do utilizador, quantos dados uma aplicação está a processar por pedido, quantos pedidos é um tratamento de aplicação num determinado período de tempo, quanto tempo um utilizador tem de esperar para obter uma resposta após a apresentação do seu pedido. Os termos técnicos para estes indicadores de desempenho são, IOPS, Despôs ou Largura de Banda, e Latência.

Nesta secção, discutiremos os indicadores comuns de desempenho no contexto do Armazenamento Premium. Na secção seguinte, Recolha de Requisitos de Aplicação, aprenderá a medir estes indicadores de desempenho para a sua aplicação. Mais tarde, na Otimização do Desempenho da Aplicação, irá conhecer os fatores que afetam estes indicadores de desempenho e recomendações para os otimizar.

## <a name="iops"></a>IOPS

IOPS, ou Operações de Entrada/Saída Por Segundo, é o número de pedidos que a sua aplicação está a enviar para os discos de armazenamento num segundo. Uma operação de entrada/saída pode ser lida ou escrita, sequencial ou aleatória. Aplicações de Processamento de Transações Online (OLTP) como um site de retalho online precisam processar imediatamente muitos pedidos de utilizador simultâneos. Os pedidos do utilizador são inserção e atualização de operações intensivas de base de dados, que a aplicação deve processar rapidamente. Por conseguinte, as aplicações OLTP requerem IOPS muito elevados. Tais aplicações lidam com milhões de pequenos e aleatórios pedidos de IO. Se tiver tal aplicação, deve projetar a infraestrutura de aplicação para otimizar o IOPS. Na secção posterior, Otimizando o Desempenho da *Aplicação,* discutimos em detalhe todos os fatores que deve considerar para obter IOPS elevados.

Quando anexa um disco de armazenamento premium à sua VM de grande escala, o Azure aprovisiona um número garantido de IOPS de acordo com a especificação do disco. Por exemplo, um disco P50 aprovisiona 7500 IOPS. Cada tamanho de VM de grande escala tem também um limite de IOPS específico que pode sustentar. Por exemplo, um VM GS5 Standard tem um limite de 80.000 IOPS.

## <a name="throughput"></a>Débito

A entrada ou largura de banda é a quantidade de dados que a sua aplicação está a enviar para os discos de armazenamento num intervalo especificado. Se a sua aplicação estiver a realizar operações de entrada/saída com grandes tamanhos de unidade IO, requer uma alta produção. As aplicações de armazéns de dados tendem a emitir operações intensivas de digitalização que acedem a grandes porções de dados de cada vez e realizam geralmente operações a granel. Por outras palavras, tais aplicações requerem uma maior entrada. Se tiver tal aplicação, deve projetar a sua infraestrutura para otimizar para a entrada. Na próxima secção, discutimos em pormenor os fatores que deve sintonizar para o conseguir.

Quando se fixa um disco de armazenamento premium a um VM de alta escala, as provisões Azure são de acordo com essa especificação do disco. Por exemplo, um disco P50 aprovisiona um débito de disco de 250 MB por segundo. Cada tamanho de VM de grande escala tem também um limite de débito específico que pode sustentar. Por exemplo, uma VM Standard GS5 tem um débito máximo de 2000 MB por segundo.

Existe uma relação entre a entrada e o IOPS, como mostra a fórmula abaixo.

![Relação dos IOPS e dos seus contributos](../articles/virtual-machines/linux/media/premium-storage-performance/image1.png)

Por isso, é importante determinar os valores ideais de entrada e IOPS que a sua aplicação necessita. À medida que tenta otimizar um, o outro também é afetado. Numa secção posterior, *Otimizando o Desempenho da Aplicação,* discutiremos mais detalhes sobre a otimização do IOPS e do Throughput.

## <a name="latency"></a>Latência

A latência é o tempo que leva um pedido para receber um único pedido, enviá-lo para os discos de armazenamento e enviar a resposta ao cliente. Esta é uma medida crítica do desempenho de uma aplicação para além do IOPS e do Resultado. A Latência de um disco de armazenamento premium é o tempo que leva para recuperar a informação para um pedido e comunicá-la de volta à sua aplicação. O Armazenamento Premium proporciona baixas lestabeleceções consistentes. Os Discos Premium são projetados para fornecer llácências de milissegundodede de um dígito para a maioria das operações io. Se ativar o recolhimento ReadOnly em discos de armazenamento premium, pode obter uma latência muito mais baixa. Discutiremos o Disk Caching mais detalhadamente na secção posterior sobre otimização do desempenho da *aplicação.*

Quando estiver a otimizar a sua aplicação para obter iOPS e Entrada mais elevados, isso afetará a latência da sua aplicação. Depois de afinar o desempenho da aplicação, avalie sempre a latência da aplicação para evitar comportamentos inesperados de alta latência.

As seguintes operações de plano de controlo em Discos Geridos podem envolver a deslocação do Disco de um local de armazenamento para outro. Isto é orquestrado através de cópia de fundo de dados que podem levar várias horas para completar, tipicamente menos de 24 horas dependendo da quantidade de dados nos discos. Durante esse tempo, a sua aplicação pode experimentar uma latência de leitura superior ao habitual, uma vez que algumas leituras podem ser redirecionadas para a localização original e podem demorar mais tempo a ser concluídas. Não há impacto na latência escrita durante este período.

- Atualize o tipo de armazenamento.
- Desaperte e prenda um disco de um VM a outro.
- Crie um disco gerido a partir de um VHD.
- Crie um disco gerido a partir de um instantâneo.
- Converter discos não geridos para discos geridos.

## <a name="performance-application-checklist-for-disks"></a>Lista de verificação de aplicações de desempenho para discos

O primeiro passo para a conceção de aplicações de alto desempenho em execução no Armazenamento Premium Azure é compreender os requisitos de desempenho da sua aplicação. Depois de ter recolhido requisitos de desempenho, pode otimizar a sua aplicação para obter o melhor desempenho.

Na secção anterior, explicámos os indicadores comuns de desempenho, IOPS, Throughput e Latency. Deve identificar quais destes indicadores de desempenho são fundamentais para a sua aplicação para fornecer a experiência de utilizador desejada. Por exemplo, os elevados IOPS são mais importantes para aplicações OLTP que processam milhões de transações num segundo. Considerando que a elevada dose de entrada é fundamental para as aplicações do Data Warehouse que processam grandes quantidades de dados num segundo. A Latência extremamente baixa é crucial para aplicações em tempo real, como sites de streaming de vídeo ao vivo.

Em seguida, meça os requisitos máximos de desempenho da sua aplicação ao longo da sua vida útil. Utilize a lista de verificação de amostras abaixo como início. Registem os requisitos máximos de desempenho durante os períodos normais, de pico e de trabalho fora de horas. Ao identificar requisitos para todos os níveis de carga de trabalho, poderá determinar o requisito geral de desempenho da sua aplicação. Por exemplo, a carga de trabalho normal de um site de e-commerce serão as transações que serve durante a maioria dos dias num ano. A carga de trabalho máxima do site serão as transações que serve durante a época de férias ou eventos especiais de venda. A carga de trabalho máxima é tipicamente experimentada por um período limitado, mas pode exigir que a sua aplicação escala duas ou mais vezes o seu funcionamento normal. Descubra os requisitos de 50 percentil, 90 percentil e 99 percentil. Isto ajuda a filtrar quaisquer outliers nos requisitos de desempenho e você pode concentrar os seus esforços na otimização para os valores certos.

## <a name="application-performance-requirements-checklist"></a>Lista de requisitos de desempenho da aplicação

| **Requisitos de desempenho** | **Percentil 50** | **Percentil 90** | **Percentil 99** |
| --- | --- | --- | --- |
| Um máximo de Transações por segundo | | | |
| % Continuar a ler operações | | | |
| % De write operations | | | |
| % Operações aleatórias | | | |
| % Operações sequenciais | | | |
| Tamanho do pedido iO | | | |
| Rendimento médio | | | |
| Um máximo de Débito | | | |
| Min, min. Latência | | | |
| Latência média | | | |
| Um máximo de CPU | | | |
| CpU médio | | | |
| Um máximo de Memória | | | |
| Memória Média | | | |
| Profundidade da fila | | | |

> [!NOTE]
> Deve considerar a redução destes números com base no crescimento futuro esperado da sua aplicação. É uma boa ideia planear o crescimento com antecedência, porque poderia ser mais difícil mudar as infraestruturas para melhorar o desempenho mais tarde.

Se tiver uma aplicação existente e pretender mudar-se para o Armazenamento Premium, primeiro construa a lista de verificação acima para a aplicação existente. Em seguida, construa um protótipo da sua aplicação no Armazenamento Premium e desenhe a aplicação com base em diretrizes descritas no *Optimizing Application Performance* numa secção posterior deste documento. O próximo artigo descreve as ferramentas que pode usar para recolher as medições de desempenho.

### <a name="counters-to-measure-application-performance-requirements"></a>Contadores para medir os requisitos de desempenho da aplicação

A melhor forma de medir os requisitos de desempenho da sua aplicação é utilizar ferramentas de monitorização de desempenho fornecidas pelo sistema operativo do servidor. Pode utilizar o PerfMon para Windows e iostat para o Linux. Estas ferramentas capturam contadores correspondentes a cada medida explicada na secção acima. Deve capturar os valores destes contadores quando a sua aplicação estiver a funcionar com as suas cargas de trabalho normais, máximas e fora de horas.

Os balcões PerfMon estão disponíveis para processador, memória e, cada disco lógico e disco físico do seu servidor. Quando utiliza discos de armazenamento premium com um VM, os contadores de discos físicos são para cada disco de armazenamento premium, e os contadores de discos lógicos são para cada volume criado nos discos de armazenamento premium. Deve capturar os valores dos discos que acolhem a sua carga de trabalho de aplicação. Se houver um mapeamento de um a um entre discos lógicos e físicos, pode consultar-se para contadores de discos físicos; caso contrário, refira-se aos contadores de discos lógicos. Em Linux, o comando iostat gera um CPU e um relatório de utilização do disco. O relatório de utilização do disco fornece estatísticas por dispositivo físico ou partição. Se tiver um servidor de base de dados com os seus dados e registos em discos separados, colete estes dados para ambos os discos. A tabela abaixo descreve contadores para discos, processadores e memória:

| Contador | Descrição | PerfMon | Iostat |
| --- | --- | --- | --- |
| **IOPS ou Transações por segundo** |Número de pedidos de I/S emitidos para o disco de armazenamento por segundo. |Leituras/seg de disco <br> Escritas de Disco/seg |tps <br> r/s <br> w/s |
| **Leituras e Escritos do Disco** |% das operações de Leitura e Escrita realizadas no disco. |% tempo de leitura do disco <br> % Tempo de escrita do disco |r/s <br> w/s |
| **Débito** |Quantidade de dados lidos ou escritos ao disco por segundo. |Discos Ler Bytes/seg <br> Discos Write Bytes/seg |kB_read/s <br> kB_wrtn/s |
| **Latência** |Tempo total para completar um pedido de IO do disco. |Sec/Leitura de Disco Médio <br> Sec/Write |esperar <br> svctm |
| **Tamanho IO** |O tamanho do I/S solicita problemas aos discos de armazenamento. |Bytes/Leitura médias do disco <br> Bytes/Escrita de Disco Médio |avgrq-sz |
| **Profundidade da fila** |Número de pedidos de I/S pendentes à espera de serem lidos ou escritos para o disco de armazenamento. |Comprimento da fila do disco atual |avgqu-sz |
| **Max, max. Memória** |Quantidade de memória necessária para executar a aplicação sem problemas |% dos Bytes Consolidados em Utilização |Utilizar vmstat |
| **Max, max. CPU** |Montante CPU necessário para executar a aplicação sem problemas |% tempo do processador |%util |

Saiba mais sobre [iostat](https://linux.die.net/man/1/iostat) e [PerfMon.](https://msdn.microsoft.com/library/aa645516.aspx)



## <a name="optimize-application-performance"></a>Otimizar o desempenho da aplicação

Os principais fatores que influenciam o desempenho de uma aplicação em execução no Armazenamento Premium são os pedidos de Natureza de IO, tamanho VM, tamanho do disco, Número de discos, cachede risque, multithreading e profundidade de fila. Pode controlar alguns destes fatores com botões fornecidos pelo sistema. A maioria das aplicações pode não lhe dar a opção de alterar diretamente o tamanho da IO e a Profundidade da Fila. Por exemplo, se estiver a utilizar o SQL Server, não pode escolher o tamanho IO e a profundidade da fila. O SQL Server escolhe os valores ideais de tamanho IO e profundidade de fila para obter o maior desempenho. É importante compreender os efeitos de ambos os tipos de fatores no desempenho da sua aplicação, para que possa fornecer recursos adequados para satisfazer as necessidades de desempenho.

Ao longo desta secção, consulte a lista de verificação de requisitos de aplicação que criou, para identificar o quanto precisa para otimizar o desempenho da sua aplicação. Com base nisso, poderá determinar quais os fatores desta secção que terá de sintonizar. Para testemunhar os efeitos de cada fator no desempenho da sua aplicação, faça ferramentas de benchmarking na configuração da sua aplicação. Consulte o artigo de Benchmarking, ligado no final, para que as medidas executem ferramentas comuns de benchmarking nos VMs Windows e Linux.

### <a name="optimize-iops-throughput-and-latency-at-a-glance"></a>Otimizar iOPS, entrada e latência num ápice

A tabela abaixo resume os fatores de desempenho e os passos necessários para otimizar o IOPS, a entrada e a latência. As secções que seguem este resumo descreverão cada fator é muito mais profunda.

Para obter mais informações sobre os tamanhos vm e sobre o IOPS, a entrada e a latência disponíveis para cada tipo de VM, consulte os [tamanhos de VM Linux](../articles/virtual-machines/linux/sizes.md) ou [os tamanhos vM do Windows](../articles/virtual-machines/windows/sizes.md).

| &nbsp; | **IOPS** | **Débito** | **Latência** |
| --- | --- | --- | --- |
| **Cenário de exemplo** |Aplicação Enterprise OLTP requer transações muito elevadas por segunda taxa. |Aplicação de armazenamento de dados empresariais processando grandes quantidades de dados. |Aplicações quase em tempo real que requerem respostas instantâneas aos pedidos dos utilizadores, como jogos online. |
| Fatores de desempenho | &nbsp; | &nbsp; | &nbsp; |
| **Tamanho IO** |O tamanho menor da IO produz iOPS mais elevado. |Maior tamanho iO para obter um rendimento mais alto. | &nbsp;|
| **Tamanho VM** |Utilize um tamanho VM que ofereça IOPS maior do que o seu requisito de aplicação. |Utilize um tamanho VM com limite de entrada superior ao seu requisito de aplicação. |Utilize um tamanho VM que ofereça limites de escala superiores aos requisitos de aplicação. |
| **Tamanho do disco** |Utilize um tamanho de disco que ofereça IOPS maior do que o seu requisito de aplicação. |Utilize um tamanho de disco com limite de entrada superior ao seu requisito de aplicação. |Utilize um tamanho de disco que ofereça limites de escala superiores aos requisitos de aplicação. |
| **Limites de escala de VM e disco** |O limite iOPS do tamanho vm escolhido deve ser superior ao total de IOPS impulsionado por discos de armazenamento ligados a ele. |O limite de produção do tamanho VM escolhido deve ser superior ao total de produção impulsionado por discos de armazenamento premium ligados a ele. |Os limites de escala do tamanho vm escolhidodevem ser superiores aos limites totais de escala dos discos de armazenamento premium anexos. |
| **Caching de disco** |Ativar A Cache ReadOnly em discos de armazenamento premium com leia as operações pesadas para obter iOPS de leitura mais elevada. | &nbsp; |Ativar A Caixa ReadOnly em discos de armazenamento premium com operações pesadas prontas para obter latenciências de leitura muito baixas. |
| **Striping de disco** |Use vários discos e junte-os para obter um limite combinado de IOPS e De sueste superior. O limite combinado por VM deve ser superior aos limites combinados dos discos premium anexados. | &nbsp; | &nbsp; |
| **Tamanho da risca** |Tamanho de listra menor para pequeno padrão iO aleatório visto em aplicações OLTP. Por exemplo, utilize o tamanho da listra de 64 KB para a aplicação SQL Server OLTP. |Tamanho maior da listra para padrão iO grande sequencial visto nas aplicações data Warehouse. Por exemplo, utilize o tamanho da risca 256 KB para a aplicação do armazém SQL Server Data. | &nbsp; |
| **Multithreading** |Utilize o multithreading para empurrar um maior número de pedidos para o Armazenamento Premium que levará a iOPS e Aputamais mais elevados. Por exemplo, no SQL Server delineou um elevado valor MAXDOP para alocar mais CPUs ao Servidor SQL. | &nbsp; | &nbsp; |
| **Profundidade da fila** |Maior profundidade de fila produz iOPS mais elevado. |Maior profundidade de fila produz uma maior produção. |Menor profundidade de fila produz tardios mais baixos. |

## <a name="nature-of-io-requests"></a>Natureza dos pedidos de IO

Um pedido de IO é uma unidade de operação de entrada/saída que a sua aplicação irá executar. Identificar a natureza dos pedidos de IO, aleatórios ou sequenciais, ler ou escrever, pequeno ou grande, irá ajudá-lo a determinar os requisitos de desempenho da sua aplicação. É importante compreender a natureza dos pedidos da OI, tomar as decisões certas ao conceber a sua infraestrutura de aplicação. Os IOs devem ser distribuídos uniformemente para alcançar o melhor desempenho possível.

O tamanho de IO é um dos fatores mais importantes. O tamanho da IO é o tamanho do pedido de funcionamento de entrada/saída gerado pela sua aplicação. O tamanho da IO tem um impacto significativo no desempenho especialmente no IOPS e na largura de banda que a aplicação é capaz de alcançar. A fórmula seguinte mostra a relação entre IOPS, tamanho IO e largura de banda/saída.  
    ![](media/premium-storage-performance/image1.png)

Algumas aplicações permitem alterar o seu tamanho de IO, enquanto algumas aplicações não. Por exemplo, o SQL Server determina o tamanho ideal da IO em si, e não fornece aos utilizadores nenhum botão para alterá-lo. Por outro lado, a Oracle fornece um parâmetro chamado [DB \_ BLOCK \_ SIZE](https://docs.oracle.com/cd/B19306_01/server.102/b14211/iodesign.htm#i28815) utilizando o qual pode configurar o tamanho do pedido de I/S da base de dados.

Se estiver a utilizar uma aplicação, que não lhe permite alterar o tamanho da IO, utilize as diretrizes deste artigo para otimizar o desempenho do KPI que é mais relevante para a sua aplicação. Por exemplo,

* Uma aplicação OLTP gera milhões de pequenos e aleatórios pedidos de IO. Para lidar com este tipo de pedidos de IO, você deve projetar a sua infraestrutura de aplicação para obter IOPS mais elevado.  
* Uma aplicação de armazenamento de dados gera pedidos de IO grandes e sequenciais. Para lidar com este tipo de pedidos de IO, deve projetar a sua infraestrutura de aplicação para obter uma largura de banda mais elevada ou uma entrada.

Se estiver a utilizar uma aplicação, que lhe permite alterar o tamanho da IO, utilize esta regra do polegar para o tamanho da IO para além de outras diretrizes de desempenho,

* Tamanho iO menor para obter iOPS mais alto. Por exemplo, 8 KB para uma aplicação OLTP.  
* Maior tamanho iO para obter maior largura de banda/potência. Por exemplo, 1024 KB para uma aplicação de armazém de dados.

Aqui está um exemplo de como pode calcular o IOPS e o Despto/Largura de Banda para a sua aplicação. Considere uma aplicação usando um disco P30. O iOPS máximo e a largura de banda/largura de banda que um disco P30 pode alcançar é de 5000 IOPS e 200 MB por segundo, respectivamente. Agora, se a sua aplicação requer o iOPS máximo do disco P30 e utilizar um tamanho IO menor como 8 KB, a largura de banda resultante que poderá obter é de 40 MB por segundo. No entanto, se a sua aplicação necessitar da largura de banda/saída máxima do disco P30 e utilizar um tamanho IO maior como 1024 KB, o IOPS resultante será menor, 200 IOPS. Por isso, sintonize o tamanho da IO de modo a que cumpra tanto o requisito iOPS da sua aplicação como de "Throughput/Width". A tabela seguinte resume os diferentes tamanhos de IO e os respetivos IOPS e Despôs para um disco P30.

| Requisito de candidatura | Tamanho I/O | IOPS | Largura de banda/banda |
| --- | --- | --- | --- |
| IOPS Máximo |8 KB |5000 |40 MB por segundo |
| Max Throughput |1024 KB |200 |200 MB por segundo |
| Max Throughput + IOPS elevado |64 KB |3,200 |200 MB por segundo |
| Max IOPS + alta potência |32 KB |5000 |160 MB por segundo |

Para obter IOPS e Largura de Banda superior ao valor máximo de um único disco de armazenamento premium, utilize vários discos premium listrados em conjunto. Por exemplo, stripe dois discos P30 para obter um IOPS combinado de 10.000 IOPS ou uma potência combinada de 400 MB por segundo. Como explicado na secção seguinte, deve utilizar um tamanho VM que suporte o IOPS e o Reput do disco combinado.

> [!NOTE]
> À medida que aumenta o IOPS ou o "Throughput" o outro também aumenta, certifique-se de que não atinge os limites de entrada ou iOPS do disco ou vM ao aumentar um.

Para testemunhar os efeitos do tamanho da IO no desempenho da aplicação, pode executar ferramentas de benchmarking no seu VM e discos. Crie vários ensaios de teste e use diferentes tamanhos de IO para cada corrida para ver o impacto. Consulte o artigo de Benchmarking, ligado no final, para mais detalhes.

## <a name="high-scale-vm-sizes"></a>Tamanhos VM de alta escala

Quando começar a desenhar uma aplicação, uma das primeiras coisas a fazer é escolher um VM para hospedar a sua aplicação. O Armazenamento Premium vem com tamanhos vm de alta escala que podem executar aplicações que requerem maior potência computacional e um alto desempenho de I/O de disco local. Estes VMs fornecem processadores mais rápidos, uma relação memória-núcleo mais elevada, e uma Unidade de Estado Sólido (SSD) para o disco local. Exemplos de VMs de alta escala que suportam o Armazenamento Premium são os VMs da série DS e GS.

VMs de alta escala estão disponíveis em diferentes tamanhos com um número diferente de núcleos de CPU, memória, OS e tamanho temporário do disco. Cada tamanho VM também tem o número máximo de discos de dados que pode anexar ao VM. Portanto, o tamanho de VM escolhido irá afetar a quantidade de processamento, memória e capacidade de armazenamento disponível para a sua aplicação. Também afeta o custo de Computação e Armazenamento. Por exemplo, abaixo estão as especificações do maior tamanho VM de uma série DS e uma série GS:

| Tamanho da VM | Núcleos de CPU | Memória | Tamanhos do disco VM | Um máximo de discos de dados | Tamanho da cache | IOPS | Limites de Cache IO de largura de banda |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_DS14 |16 |112 GB |OS = 1023 GB <br> Local SSD = 224 GB |32 |576 GB |50.000 IOPS <br> 512 MB por segundo |4.000 IOPS e 33 MB por segundo |
| Standard_GS5 |32 |448 GB |OS = 1023 GB <br> Local SSD = 896 GB |64 |4224 GB |80.000 IOPS <br> 2.000 MB por segundo |5.000 IOPS e 50 MB por segundo |

Para ver uma lista completa de todos os tamanhos de VM Azure disponíveis, consulte os [tamanhos VM do Windows](../articles/virtual-machines/windows/sizes.md) ou [tamanhos De VM Linux](../articles/virtual-machines/linux/sizes.md). Escolha um tamanho VM que possa satisfazer e escalar os requisitos de desempenho de aplicação pretendidos. Além disso, tome em consideração as considerações importantes na escolha dos tamanhos vm.

*Limites de escala*  
Os limites máximos de IOPS por VM e por disco são diferentes e independentes uns dos outros. Certifique-se de que a aplicação está a conduzir o IOPS dentro dos limites do VM, bem como os discos premium que lhe estão ligados. Caso contrário, o desempenho da aplicação sofrerá estrangulamentos.

Como exemplo, suponha que um requisito de candidatura seja um máximo de 4.000 IOPS. Para isso, você disponibiliza um disco P30 num VM DS1. O disco P30 pode entregar até 5.000 IOPS. No entanto, o DS1 VM está limitado a 3.200 IOPS. Consequentemente, o desempenho da aplicação será limitado pelo limite de VM em 3.200 IOPS e haverá um desempenho degradado. Para prevenir esta situação, escolha um VM e o tamanho do disco que ambos satisfaçam os requisitos de aplicação.

*Custo de Funcionamento*  
Em muitos casos, é possível que o seu custo total de operação utilizando o Armazenamento Premium seja inferior ao da utilização do Armazenamento Padrão.

Por exemplo, considere uma aplicação que exija 16.000 IOPS. Para alcançar este desempenho, você precisará de um \_ Standard D14 Azure IaaS VM, que pode dar um máximo de IOPS de 16.000 usando 32 discos de armazenamento padrão 1 TB. Cada disco de armazenamento padrão de 1-TB pode atingir um máximo de 500 IOPS. O custo estimado deste VM por mês será de $1.570. O custo mensal de 32 discos de armazenamento padrão será de $1.638. O custo total mensal estimado será de $3.208.

No entanto, se acolheu a mesma aplicação no Armazenamento Premium, precisará de um tamanho VM menor e menos discos de armazenamento premium, reduzindo assim o custo global. Um \_ VM Standard DS13 pode cumprir o requisito de 16.000 IOPS usando quatro discos P30. O DS13 VM tem um IOPS máximo de 25.600 e cada disco P30 tem um IOPS máximo de 5.000. No geral, esta configuração pode atingir 5.000 x 4 = 20.000 IOPS. O custo estimado deste VM por mês será de $1.003. O custo mensal de quatro discos de armazenamento premium P30 será de $544,34. O custo total mensal estimado será de $1.544.

A tabela abaixo resume a repartição de custos deste cenário para o Armazenamento Standard e Premium.

| &nbsp; | **Standard** | **Premium** |
| --- | --- | --- |
| **Custo da VM por mês** |$1.570.58 (Standard \_ D14) |$1.003.66 (Standard \_ DS13) |
| **Custo dos Discos por mês** |$1.638.40 (discos de 32 x 1 TB) |$544.34 (4 x Discos P30) |
| **Custo Global por mês** |$3.208.98 |$1.544.34 |

*Linux Distros*  

Com o Armazenamento Azure Premium, obtém o mesmo nível de Desempenho para VMs que executam Windows e Linux. Apoiamos muitos sabores de distros linux, e você pode ver a lista completa [aqui](../articles/virtual-machines/linux/endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). É importante notar que diferentes distros são mais adequados para diferentes tipos de cargas de trabalho. Verá diferentes níveis de desempenho dependendo da distro que a sua carga de trabalho está a decorrer. Teste os distros linux com a sua aplicação e escolha o que funciona melhor.

Ao executar o Linux com Armazenamento Premium, verifique as últimas atualizações sobre os condutores necessários para garantir um alto desempenho.

## <a name="premium-storage-disk-sizes"></a>Tamanhos de disco de armazenamento premium

O Azure Premium Storage oferece uma variedade de tamanhos para que possa escolher um que melhor se adapte às suas necessidades. Cada tamanho do disco tem um limite de escala diferente para IOPS, largura de banda e armazenamento. Escolha o tamanho certo do Disco de Armazenamento Premium dependendo dos requisitos de aplicação e do tamanho vm de alta escala. A tabela abaixo mostra os tamanhos dos discos e as suas capacidades. Os tamanhos P4, P6, P15, P60, P70 e P80 são atualmente suportados apenas para discos geridos.

[!INCLUDE [disk-storage-premium-ssd-sizes](disk-storage-premium-ssd-sizes.md)]

Quantos discos escolhe depende do tamanho do disco escolhido. Pode utilizar um único disco P50 ou vários discos P10 para satisfazer o seu requisito de aplicação. Tome em consideração as considerações listadas abaixo ao fazer a escolha.

*Limites de escala (IOPS e Entrada)*  
Os limites iOPS e De supor de cada tamanho de disco Premium são diferentes e independentes dos limites de escala VM. Certifique-se de que o iOPS total e a entrada dos discos estão dentro dos limites de escala do tamanho de VM escolhido.

Por exemplo, se um requisito de aplicação for um máximo de 250 MB/sec De entrada e estiver a utilizar um DS4 VM com um único disco P30. O DS4 VM pode ceder até 256 MB/sede De entrada. No entanto, um único disco P30 tem um limite de entrada de 200 MB/seg. Consequentemente, a aplicação será limitada a 200 MB/seg devido ao limite do disco. Para ultrapassar este limite, forneca mais de um disco de dados para o VM ou redimensione os seus discos para P40 ou P50.

> [!NOTE]
> As leituras servidas pela cache não estão incluídas no disco IOPS e Em Sput, pelo que não estão sujeitas a limites de disco. A Cache tem o seu limite de IOPS e de Entrada separado por VM.
>
> Por exemplo, inicialmente as suas leituras e escritos são de 60MB/seg e 40MB/seg, respectivamente. Com o tempo, a cache aquece e serve cada vez mais as leituras da cache. Depois, pode obter uma escrita mais alta Através do disco.

*Número de Discos*  
Determine o número de discos que necessitará avaliando os requisitos de aplicação. Cada tamanho VM também tem um limite no número de discos que pode anexar ao VM. Normalmente, este é o dobro do número de núcleos. Certifique-se de que o tamanho VM que escolher pode suportar o número de discos necessários.

Lembre-se, os discos de Armazenamento Premium têm maiorcapacidade de desempenho em comparação com os discos standard de armazenamento. Portanto, se estiver a migrar a sua aplicação do Azure IaaS VM utilizando o Armazenamento Padrão para o Armazenamento Premium, provavelmente precisará de menos discos premium para obter o mesmo ou maior desempenho para a sua aplicação.

## <a name="disk-caching"></a>Colocação em cache do disco

VMs de alta escala que alavancam o Armazenamento Premium Azure têm uma tecnologia de cache multi-nível chamada BlobCache. BlobCache usa uma combinação da RAM hospedeira e SSD local para o cache. Esta cache está disponível para os discos persistentes de Armazenamento Premium e para os discos locais VM. Por predefinição, esta definição de cache está definida para Ler/Escrever para discos OS e LerApenas para discos de dados hospedados no Armazenamento Premium. Com o cache de disco ativado nos discos de Armazenamento Premium, os VMs de alta escala podem atingir níveis de desempenho extremamente elevados que excedem o desempenho do disco subjacente.

> [!WARNING]
> O Caching do disco não é suportado para discos 4 TiB e maiores. Se vários discos estiverem ligados ao seu VM, cada disco inferior a 4 TiB suportará o cache.
>
> Alterar a definição de cache de um disco do Azure desanexa e anexa o disco de destino. Se for o disco do sistema operativo, o VM é reiniciado. Pare todas as aplicações/serviços que possam ser afetados por esta interrupção antes de alterar a definição da cache do disco. Não seguir estas recomendações pode levar à corrupção de dados.

Para saber mais sobre como funciona o BlobCache, consulte o post de blog Inside [Azure Premium Storage.](https://azure.microsoft.com/blog/azure-premium-storage-now-generally-available-2/)

É importante ativar a cache no conjunto certo de discos. Se deve ativar o cache de disco num disco premium ou não, dependerá do padrão de carga de trabalho que o disco irá manusear. A tabela abaixo mostra as definições de cache padrão para os discos OS e Data.

| **Tipo de disco** | **Definição de cache padrão** |
| --- | --- |
| Disco do SO |ReadWrite |
| Disco de dados |ReadOnly |

Seguem-se as definições recomendadas de cache de disco para discos de dados,

| **Definição de cache de disco** | **recomendação sobre quando usar esta definição** |
| --- | --- |
| Nenhum |Configure o cache do hospedeiro como Nenhum para discos apenas escritos e de escrita pesados. |
| ReadOnly |Configure o cache do anfitrião como ReadOnly para discos de leitura e leitura. |
| ReadWrite |Configure a cache do anfitrião como ReadWrite apenas se a sua aplicação lidar corretamente com a escrita de dados em cache para discos persistentes quando necessário. |

*ReadOnly*  
Ao configurar o caching readOnly em discos de dados de armazenamento premium, pode obter baixa latência de leitura e obter iOPS e Sons de leitura muito elevados para a sua aplicação. Isto é devido por duas razões,

1. As leituras realizadas a partir de cache, que está na memória VM e SSD local, são muito mais rápidas do que as leituras do disco de dados, que está no armazenamento de blob Azure.  
1. O Armazenamento Premium não conta as Leituras servidas a partir de cache, para o disco IOPS e 'Throughput'. Portanto, a sua aplicação é capaz de alcançar iOPS e Input totais mais elevados.

*ReadWrite*  
Por predefinição, os discos OS têm o cache de ReadWrite ativado. Adicionámos recentemente suporte para o ReadWrite caching em discos de dados também. Se estiver a utilizar o cacheWrite, deve ter uma forma adequada de escrever os dados desde cache até discos persistentes. Por exemplo, o SQL Server lida com a escrita de dados em cache para os discos de armazenamento persistentes por si só. Utilizar a cache ReadWrite com uma aplicação que não lida com a persistência dos dados necessários pode levar à perda de dados, se o VM falhar.

*Nenhum*  
Atualmente, **nenhum** é suportado apenas em discos de dados. Não é suportado em discos DE SO. Se colocar **nenhum** num disco OS, irá sobrepor-se a este sistema interno e defini-lo-á para **ReadOnly**.

Como exemplo, pode aplicar estas diretrizes ao SQL Server em execução no Armazenamento Premium, fazendo o seguinte,

1. Configure a cache "ReadOnly" em discos de armazenamento premium que hospedam ficheiros de dados.  
   a.  As leituras rápidas da cache baixam o tempo de consulta do SQL Server, uma vez que as páginas de dados são recuperadas muito mais rapidamente a partir da cache em comparação com diretamente dos discos de dados.  
   b.  Servir leituras a partir de cache, significa que há um Adicional de Entrada disponível a partir de discos de dados premium. O SQL Server pode utilizar este Adicional De produção para recuperar mais páginas de dados e outras operações como backup/restauro, cargas de lote e reconstruções de índices.  
1. Configure a cache "Nenhuma" nos discos de armazenamento premium que hospedam os ficheiros de registo.  
   a.  Os ficheiros de registo têm operações principalmente pesadas. Por conseguinte, não beneficiam da cache ReadOnly.

## <a name="optimize-performance-on-linux-vms"></a>Otimizar o desempenho nos VMs linux

Para todos os SSDs premium ou discos ultra com cache definido para **ReadOnly** ou **None,** deve desativar "barreiras" quando montar o sistema de ficheiros. Não é necessário barreiras neste cenário porque os escritos para discos de armazenamento premium são duráveis para estas definições de cache. Quando o pedido de escrita termina com sucesso, os dados foram escritos para a loja persistente. Para desativar as "barreiras", utilize um dos seguintes métodos. Escolha o do seu sistema de ficheiros:
  
* Para **reutilizar fossas,** para desativar as barreiras, utilize a `barrier=none` opção de montagem. (Para permitir barreiras, utilize `barrier=flush` .)
* Para **ext3/ext4**, para desativar as barreiras, utilize a `barrier=0` opção de montagem. (Para permitir barreiras, utilize `barrier=1` .)
* Para **o XFS,** para desativar as barreiras, utilize a `nobarrier` opção de montagem. (Para permitir barreiras, utilize `barrier` .)
* Para discos de armazenamento premium com cache definido para **ReadWrite,** ative barreiras para a durabilidade da escrita.
* Para que as etiquetas de volume persistam após o reinício do VM, deve atualizar /etc/fstab com as referências de identificador universalmente únicas (UUID) aos discos. Para mais informações, consulte [Adicionar um disco gerido a um VM Linux](../articles/virtual-machines/linux/add-disk.md).

As seguintes distribuições linux foram validadas para SSDs premium. Para um melhor desempenho e estabilidade com SSDs premium, recomendamos que atualize os seus VMs para uma destas versões ou mais recentes. 

Algumas das versões requerem os mais recentes Serviços de Integração linux (LIS), v4.0, para o Azure. Para descarregar e instalar uma distribuição, siga o link listado na tabela seguinte. Adicionamos imagens à lista à medida que completamos a validação. As nossas validações mostram que o desempenho varia para cada imagem. O desempenho depende das características da carga de trabalho e das definições de imagem. Imagens diferentes são sintonizadas para diferentes tipos de cargas de trabalho.

| Distribuição | Versão | Núcleo apoiado | Detalhes |
| --- | --- | --- | --- |
| Ubuntu | 12.04 ou mais recente| 3.2.0-75.110+ | &nbsp; |
| Ubuntu | 14.04 ou mais recente| 3.13.0-44.73+  | &nbsp; |
| Debian | 7.x, 8.x ou mais recente| 3.16.7-ckt4-1+ | &nbsp; |
| SUSE | SLES 12 ou mais recente| 3.12.36-38.1+ | &nbsp; |
| SUSE | SLES 11 SP4 ou mais recente| 3.0.101-0.63.1+ | &nbsp; |
| CoreOS | 584.0.0+ ou mais recente| 3.18.4+ | &nbsp; |
| CentOS | 6.5, 6.6, 6.7, 7.0, ou mais recente| &nbsp; | [LIS4 necessário](https://www.microsoft.com/download/details.aspx?id=55106) <br> *Ver nota na secção seguinte* |
| CentOS | 7.1+ ou mais recente| 3.10.0-229.1.2.el7+ | [LIS4 recomendado](https://www.microsoft.com/download/details.aspx?id=55106) <br> *Ver nota na secção seguinte* |
| Red Hat Enterprise Linux (RHEL) | 6.8+, 7.2+, ou mais recente | &nbsp; | &nbsp; |
| Oracle | 6.0+, 7.2+, ou mais recente | &nbsp; | UEK4 ou RHCK |
| Oracle | 7.0-7.1 ou mais recente | &nbsp; | UEK4 ou RHCK c/[LIS4](https://www.microsoft.com/download/details.aspx?id=55106) |
| Oracle | 6.4-6.7 ou mais recente | &nbsp; | UEK4 ou RHCK c/[LIS4](https://www.microsoft.com/download/details.aspx?id=55106) |

### <a name="lis-drivers-for-openlogic-centos"></a>Pilotos da LIS para o OpenLogic CentOS

Se estiver a executar VMs OpenLogic CentOS, execute o seguinte comando para instalar os mais recentes controladores:

```
sudo yum remove hypervkvpd  ## (Might return an error if not installed. That's OK.)
sudo yum install microsoft-hyper-v
sudo reboot
```

Em alguns casos, o comando acima também irá atualizar o núcleo. Se for necessária uma atualização de kernel, poderá ter de executar novamente os comandos acima, depois de reiniciar para instalar totalmente o pacote microsoft-hyper-v.


## <a name="disk-striping"></a>Tiras de disco

Quando um VM de alta escala é ligado com vários discos persistentes de armazenamento premium, os discos podem ser listrados em conjunto para agregar os seus IOPs, largura de banda e capacidade de armazenamento.

No Windows, pode utilizar espaços de armazenamento para riscar discos em conjunto. Deve configurar uma coluna para cada disco numa piscina. Caso contrário, o desempenho global do volume listrado pode ser inferior ao esperado, devido à distribuição desigual do tráfego através dos discos.

Importante: Utilizando o Server Manager UI, pode definir o número total de colunas até 8 para um volume listrado. Ao ligar mais de oito discos, utilize o PowerShell para criar o volume. Utilizando o PowerShell, pode definir o número de colunas igual ao número de discos. Por exemplo, se houver 16 discos num único conjunto de riscas; especificar 16 colunas no parâmetro *NumberOfColumns* do cmdlet *New-VirtualDisk* PowerShell.

No Linux, use o utilitário MDADM para riscar discos juntos. Para obter passos detalhados sobre discos de strip em Linux consulte o [Configure Software RAID no Linux](../articles/virtual-machines/linux/configure-raid.md).

*Tamanho da risca*  
Uma configuração importante na tiragem do disco é o tamanho da risca. O tamanho da risca ou do tamanho do bloco é o menor pedaço de dados que a aplicação pode abordar num volume listrado. O tamanho da risca que configura ruma depende do tipo de aplicação e do seu padrão de pedido. Se escolher o tamanho errado da risca, pode levar a um desalinhamento de IO, o que leva a um desempenho degradado da sua aplicação.

Por exemplo, se um pedido de IO gerado pela sua aplicação for maior do que o tamanho da risca do disco, o sistema de armazenamento escreve-o através dos limites da unidade de listras em mais de um disco. Quando chegar a altura de aceder a esses dados, terá de procurar em mais de uma unidade de listras para completar o pedido. O efeito cumulativo de tal comportamento pode levar a uma degradação substancial do desempenho. Por outro lado, se o tamanho do pedido da OI for menor do que o tamanho das riscas, e se for de natureza aleatória, os pedidos da OI podem acumular-se no mesmo disco causando um estrangulamento e, em última análise, degradando o desempenho da OI.

Dependendo do tipo de carga de trabalho que a sua aplicação está a executar, escolha um tamanho de listra apropriado. Para pedidos aleatórios de IO, use um tamanho de listra menor. Enquanto que para pedidos de IO sequenciais grandes utilizam um tamanho de listra maior. Descubra as recomendações do tamanho da risca para a aplicação que irá executar no Armazenamento Premium. Para o SQL Server, configure o tamanho das riscas de 64 KB para cargas de trabalho OLTP e 256 KB para cargas de trabalho de armazenamento de dados. Consulte [as melhores práticas de Desempenho para o SQL Server em VMs Azure](../articles/azure-sql/virtual-machines/windows/performance-guidelines-best-practices.md#disks-guidance) para saber mais.

> [!NOTE]
> Pode juntar um máximo de 32 discos de armazenamento premium numa série DS VM e 64 discos de armazenamento premium numa Série GS VM.

## <a name="multi-threading"></a>Multi-threading

A Azure desenhou a plataforma premium de armazenamento para ser massivamente paralela. Portanto, uma aplicação com fios múltiplos obtém um desempenho muito mais elevado do que uma aplicação de um fio único. Uma aplicação com fios múltiplos divide as suas tarefas através de vários fios e aumenta a eficiência da sua execução utilizando ao máximo os recursos VM e disco.

Por exemplo, se a sua aplicação estiver a funcionar num único Núcleo VM utilizando dois fios, o CPU pode alternar entre os dois fios para obter eficiência. Enquanto um fio está à espera de um IO do disco para ser concluído, o CPU pode mudar para o outro fio. Desta forma, dois fios podem realizar mais do que um único fio faria. Se o VM tiver mais de um núcleo, diminui ainda mais o tempo de funcionamento, uma vez que cada núcleo pode executar tarefas paralelamente.

Pode não ser capaz de alterar a forma como uma aplicação fora da prateleira implementa uma única rosca ou uma linha gemida. Por exemplo, o SQL Server é capaz de lidar com multi-CPU e multi-núcleo. No entanto, o SQL Server decide em que condições irá alavancar um ou mais fios para processar uma consulta. Pode executar consultas e construir índices usando várias linhas. Para uma consulta que envolve juntar grandes tabelas e separar dados antes de voltar ao utilizador, o SQL Server provavelmente utilizará vários fios. No entanto, um utilizador não pode controlar se o SQL Server executa uma consulta utilizando um único fio ou fios múltiplos.

Existem configurações de configuração que pode alterar para influenciar este processamento multi-threading ou paralelo de uma aplicação. Por exemplo, no caso do Servidor SQL é o grau máximo de configuração do paralelismo. Esta definição chamada MAXDOP permite configurar o número máximo de processadores Que o Servidor SQL pode utilizar no processamento paralelo. Pode configurar MAXDOP para consultas individuais ou operações de índice. Isto é benéfico quando se pretende equilibrar os recursos do seu sistema para uma aplicação crítica de desempenho.

Por exemplo, digamos que a sua aplicação usando o SQL Server está a executar uma grande consulta e uma operação de índice ao mesmo tempo. Assumamos que queria que a operação do índice fosse mais performante em comparação com a grande consulta. Neste caso, pode definir o valor MAXDOP da operação do índice para ser superior ao valor MAXDOP para a consulta. Desta forma, o SQL Server tem mais processadores que pode alavancar para a operação do índice em comparação com o número de processadores que pode dedicar à grande consulta. Lembre-se, não controla o número de fios que o Servidor SQL utilizará para cada operação. Pode controlar o número máximo de processadores dedicados a várias linhas.

Saiba mais sobre [graus de paralelismo](https://technet.microsoft.com/library/ms188611.aspx) no Servidor SQL. Descubra tais configurações que influenciam a multi-threading na sua aplicação e suas configurações para otimizar o desempenho.

## <a name="queue-depth"></a>Profundidade da fila

A profundidade da fila ou o comprimento da fila ou o tamanho da fila ou o tamanho da fila é o número de pedidos pendentes de IO no sistema. O valor da profundidade da fila determina quantas operações de IO a sua aplicação pode alinhar, que os discos de armazenamento vão processar. Afeta todos os três indicadores de desempenho da aplicação que discutimos neste artigo viz., IOPS, entrada e latência.

A profundidade da fila e a multi-rosca estão intimamente relacionadas. O valor de profundidade da fila indica quanto é que a aplicação pode obter várias linhas. Se a Profundidade da Fila for grande, a aplicação pode executar mais operações simultaneamente, ou seja, mais multi-threading. Se a Profundidade da Fila for pequena, mesmo que a aplicação seja multi-threaded, não terá pedidos suficientes alinhados para a execução simultânea.

Normalmente, as aplicações fora da prateleira não permitem alterar a profundidade da fila, porque se for definido incorretamente, fará mais mal do que bem. As aplicações definirão o valor certo da profundidade da fila para obter o desempenho ideal. No entanto, é importante compreender este conceito para que possa resolver problemas de desempenho com a sua aplicação. Também pode observar os efeitos da profundidade da fila executando ferramentas de benchmarking no seu sistema.

Algumas aplicações fornecem configurações para influenciar a Profundidade da Fila. Por exemplo, a definição MAXDOP (grau máximo de paralelismo) no Servidor SQL explicada na secção anterior. MAXDOP é uma forma de influenciar a profundidade da fila e a multi-threading, embora não altere diretamente o valor de profundidade da fila do Servidor SQL.

*Alta profundidade de fila*  
Uma alta profundidade de fila alinha mais operações no disco. O disco sabe o próximo pedido na sua fila antes do tempo. Consequentemente, o disco pode agendar operações com antecedência e processá-las numa sequência ideal. Uma vez que a aplicação está a enviar mais pedidos para o disco, o disco pode processar IOs mais paralelos. Em última análise, a aplicação será capaz de alcançar iOPS mais elevados. Uma vez que a aplicação está a processar mais pedidos, o Total de Produção da aplicação também aumenta.

Tipicamente, uma aplicação pode obter o máximo de Entrada com 8-16+ IOs pendentes por disco anexo. Se uma profundidade de fila for uma, a aplicação não está a empurrar iOs suficientes para o sistema, e processará menos quantidade num determinado período. Por outras palavras, menos Aput.

Por exemplo, no SQL Server, a definição do valor MAXDOP para uma consulta a "4" informa o SQL Server de que pode usar até quatro núcleos para executar a consulta. O SQL Server determinará qual é o melhor valor de profundidade da fila e o número de núcleos para a execução da consulta.

*Profundidade de fila ideal*  
O valor de profundidade da fila muito elevado também tem as suas desvantagens. Se o valor de profundidade da fila for demasiado elevado, a aplicação tentará conduzir iOPS muito elevado. A menos que a aplicação tenha discos persistentes com IOPS suficientes, isso pode afetar negativamente as latenciências da aplicação. A seguinte fórmula mostra a relação entre IOPS, latência e profundidade de fila.  
    ![](media/premium-storage-performance/image6.png)

Não deve configurar a Profundidade da Fila para qualquer valor elevado, mas para um valor ótimo, que pode fornecer IOPS suficientes para a aplicação sem afetar as latenciências. Por exemplo, se a latência da aplicação tiver de ser de 1 milissegundo, a Profundidade da Fila necessária para atingir 5.000 IOPS é, QD = 5000 x 0,001 = 5.

*Profundidade de fila para volume listrado*  
Para um volume listrado, mantenha uma profundidade de fila suficientemente alta de tal forma que, cada disco tem uma profundidade de fila de pico individualmente. Por exemplo, considere uma aplicação que empurre uma profundidade de fila de 2 e há quatro discos na risca. Os dois pedidos da IO vão para dois discos e os restantes dois discos ficarão inativos. Portanto, configure a profundidade da fila de modo a que todos os discos possam estar ocupados. A fórmula abaixo mostra como determinar a profundidade da fila dos volumes listrados.  
    ![](media/premium-storage-performance/image7.png)

## <a name="throttling"></a>Limitação

As provisões de armazenamento Azure Premium especificam o número de IOPS e De entrada, dependendo dos tamanhos vm e do disco que escolher. Sempre que a sua aplicação tentar conduzir IOPS ou Passput acima destes limites do que o VM ou disco pode suportar, o Armazenamento Premium irá estrangulá-lo. Isto manifesta-se sob a forma de desempenho degradado na sua aplicação. Isto pode significar maior latência, menor entrada ou iOPS mais baixo. Se o Armazenamento Premium não acelerar, a sua aplicação poderá falhar completamente ao exceder o que os seus recursos são capazes de alcançar. Assim, para evitar problemas de desempenho devido a estrangulamento, disponibilize sempre recursos suficientes para a sua aplicação. Tome em consideração o que discutimos nas secções de tamanhos VM e Disqueacima. O benchmarking é a melhor maneira de descobrir que recursos você precisará para hospedar a sua aplicação.

## <a name="next-steps"></a>Próximos passos

