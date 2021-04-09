---
title: Benefícios da utilização de ficheiros Azure NetApp para implementação de servidor SQL | Microsoft Docs
description: Mostra um desempenho detalhado da análise de custos sobre a utilização de ficheiros Azure NetApp para implementação de servidor SQL.
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
ms.date: 03/19/2021
ms.author: b-juche
ms.openlocfilehash: 46fe7570b7b9ea9446918d407dbe87596b8d0496
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104863909"
---
#  <a name="benefits-of-using-azure-netapp-files-for-sql-server-deployment"></a>Benefícios da utilização de ficheiros Azure NetApp para implementação de servidor SQL

O Azure NetApp Files reduz o custo total de propriedade do SQL Server (TCO) em comparação com as soluções de armazenamento de blocos.  Com o armazenamento de blocos, as máquinas virtuais impuseram limites à I/O e largura de banda para operações de disco, os limites de largura de banda de rede são aplicados apenas contra ficheiros Azure NetApp.  Por outras palavras, não são aplicados limites de nível VM em Ficheiros Azure NetApp. Sem estes limites de E/O, o SQL Server funciona em máquinas virtuais mais pequenas ligadas aos Ficheiros Azure NetApp, bem como o SQL Server a funcionar em máquinas virtuais muito maiores. O dimensionamento de instâncias como tal reduz o custo do cálculo para 25% do preço anterior.  *Pode reduzir os custos de computação com ficheiros Azure NetApp.*  

Os custos de cálculo, no entanto, são pequenos em comparação com os custos de licença do SQL Server.  O [licenciamento](https://download.microsoft.com/download/B/C/0/BC0B2EA7-D99D-42FB-9439-2C56880CAFF4/SQL_Server_2017_Licensing_Datasheet.pdf) do Microsoft SQL Server está ligado à contagem de núcleos físicos. Como tal, a diminuição do tamanho do exemplo introduz uma poupança de custos ainda maior para o licenciamento de software. *Pode reduzir os custos de licença de software com ficheiros Azure NetApp.*

O custo do armazenamento em si é variável dependendo do tamanho real da base de dados. Independentemente do armazenamento selecionado, a capacidade tem custos, seja um disco gerido ou uma partilha de ficheiros.  À medida que as dimensões das bases de dados aumentam e o armazenamento aumenta de custo, o armazenamento contribui para o aumento do TCO, afetando o custo global.  Como tal, a afirmação é ajustada da seguinte forma: *Pode reduzir os custos de implementação do SQL Server com ficheiros Azure NetApp.* 

Este artigo mostra uma análise detalhada de custos e benefícios de desempenho sobre a utilização de Ficheiros Azure NetApp para implementação de SERVIDOR SQL. Não só os casos mais pequenos têm CPU suficiente para fazer o trabalho de base de dados apenas possível com o bloqueio em casos maiores, *em muitos casos, os casos mais pequenos são ainda mais performantes do que os seus maiores homólogos baseados em discos por causa dos Ficheiros Azure NetApp.* 

## <a name="detailed-cost-analysis"></a>Análise detalhada de custos 

Os dois conjuntos de gráficos desta secção mostram o exemplo TCO.  O número e tipo de discos geridos, o nível de serviço Azure NetApp Files e a capacidade de cada cenário foram selecionados para alcançar o melhor desempenho de preço-capacidade.  Cada gráfico é composto por máquinas agrupadas (D16 com Ficheiros Azure NetApp, em comparação com D64 com disco gerido por exemplo), e os preços são divididos para cada tipo de máquina.  

O primeiro conjunto de gráficos mostra o custo global da solução utilizando um tamanho de base de dados 1-TiB, comparando o D16s_v3 com o D64, o D8 ao D32 e o D4 com o D16. Os IOPs projetados para cada configuração são indicados por uma linha verde ou amarela e correspondem ao eixo Y do lado direito.

[![Gráfico que mostra o custo global da solução usando um tamanho de base de dados 1-TiB. ](../media/azure-netapp-files/solution-sql-server-cost-1-tib.png)](../media/azure-netapp-files/solution-sql-server-cost-1-tib.png#lightbox)


O segundo conjunto de gráficos mostra o custo global utilizando uma base de dados de 50 TiB. As comparações são as mesmas – D16 em comparação com Azure NetApp Files versus D64 com bloco por exemplo. 

[![Gráfico que mostra o custo global usando um tamanho de base de dados de 50 TiB. ](../media/azure-netapp-files/solution-sql-server-cost-50-tib.png)](../media/azure-netapp-files/solution-sql-server-cost-50-tib.png#lightbox)
 
## <a name="performance-and-lots-of-it"></a>Desempenho, e muito dele  

Para concretizar a significativa afirmação de redução de custos requer muito desempenho - as maiores instâncias no suporte geral de inventário Azure 80.000 discos IOPS por exemplo. Um único volume de Ficheiros Azure NetApp pode atingir 80.000 IOPS de base de dados, e casos como o D16 são capazes de consumir o mesmo. O D16, normalmente capaz de 25.600 IOPS em disco, é 25% do tamanho do D64.  O D64s_v3 é capaz de 80.000 IOPS de disco, e como tal, apresenta um excelente ponto de comparação de nível superior.

O D16s_v3 pode conduzir um volume de Ficheiros Azure NetApp para 80.000 IOPS de base de dados. Como comprovado pela ferramenta de benchmark de armazenamento SQL (SSB), a instância D16 obteve uma carga de trabalho 125% maior do que a alcançável ao disco a partir da instância D64.  Consulte a secção de [ferramentas de teste SSB](#ssb-testing-tool) para obter mais detalhes sobre a ferramenta.

Utilizando um tamanho de conjunto de trabalho de 1-TiB e uma leitura de 80%, 20% de atualização da carga de trabalho do SQL Server, foram medidas as capacidades de desempenho da maioria dos casos na classe de instânciaS D; a maioria, nem todos, uma vez que os próprios casos D2 e D64 foram excluídos dos testes. O primeiro foi deixado de fora por não apoiar a rede acelerada, e este último porque é o ponto de comparação. Consulte o gráfico seguinte para entender os limites de D4s_v3, D8s_v3, D16s_v3 e D32s_v3, respectivamente.  Os testes de armazenamento de discos geridos não são apresentados no gráfico. Os valores de comparação são extraídos diretamente da tabela de limites da [Máquina Virtual Azure](../virtual-machines/dv3-dsv3-series.md) para o tipo de instância de classe D.

Com os Ficheiros Azure NetApp, cada uma das instâncias da classe D pode cumprir ou exceder as capacidades de desempenho do disco de instâncias duas vezes maiores.  *Pode reduzir significativamente os custos de licença de software com ficheiros Azure NetApp.*  

* O D4 com 75% de utilização do CPU correspondeu às capacidades do disco do D16.  
    * O D16 é limitado a 25.600 IOPS de disco.  
* O D8 com 75% de utilização do CPU correspondeu às capacidades do disco do D32.  
    * O D32 é limitado a 51.200 IOPS de disco.  
* O D16 com 55% de utilização do CPU correspondeu às capacidades do disco do D64.  
    * O D64 é limitado a 80.000 IOPS de disco.  
* O D32 com 15% de utilização do CPU correspondeu também às capacidades do disco do D64.  
    * O D64, tal como acima referido, é limitado a 80.000 IOPS de disco.  

### <a name="s3b-cpu-limits-test--performance-versus-processing-power"></a>Teste de limites S3B CPU - Desempenho versus potência de processamento

O diagrama seguinte resume o teste de limites do CPU S3B:

![Diagrama que mostra uma percentagem média de CPU para o SqL Server de uma só instância sobre os ficheiros Azure NetApp.](../media/azure-netapp-files/solution-sql-server-single-instance-average-cpu.png)

A escalabilidade é apenas parte da história. A outra parte é a latência.  Uma coisa é as máquinas virtuais mais pequenas terem a capacidade de conduzir taxas de E/S muito mais altas, outra coisa é fazê-lo com baixas latências de um dígito, como mostrado abaixo.  

* O D4 conduziu 26.000 IOPS contra ficheiros Azure NetApp com uma latência de 2,3 ms.  
* O D8 conduziu 51.000 IOPS contra ficheiros Azure NetApp com uma latência de 2.0-ms.  
* O D16 conduziu 88.000 IOPS contra ficheiros Azure NetApp com uma latência de 2,8 ms.
* O D32 conduziu 80.000 IOPS contra ficheiros Azure NetApp com uma latência de 2,4 ms.  

### <a name="s3b-per-instance-type-latency-results"></a>Resultados de latência do tipo S3B por instância

O diagrama seguinte mostra a latência do servidor SQL de instância única sobre ficheiros Azure NetApp:

![Diagrama que mostra latência para o SqL Server de instância única sobre ficheiros Azure NetApp.](../media/azure-netapp-files/solution-sql-server-single-instance-latency.png)

## <a name="ssb-testing-tool"></a>Ferramenta de teste SSB 
 
A ferramenta de benchmarking [TPC-E,](http://www.tpc.org/tpce/) por design, sublinha *o cálculo* em vez do *armazenamento.* Os resultados dos testes apresentados nesta secção baseiam-se numa ferramenta de teste de stress denominada SQL Storage Benchmark (SSB).  O SQL Server Storage Benchmark pode conduzir a execução de SQL em escala maciça contra uma base de dados do SQL Server para simular uma carga de trabalho OLTP, semelhante à [ferramenta de benchmarking SLOB2 Oracle](https://kevinclosson.net/slob/). 

A ferramenta SSB gera uma carga de trabalho orientada select e UPDATE emitindo as referidas declarações diretamente para a base de dados do SQL Server que funciona dentro da máquina virtual Azure.  Para este projeto, as cargas de trabalho SSB aumentaram de 1 para 100 utilizadores do SQL Server, com 10 ou 12 pontos intermédios a 15 minutos por contagem de utilizadores.  Todas as métricas de desempenho destas corridas foram do ponto de vista do perfmon, pois a repetibilidade SSB correu três vezes por cenário. 

Os próprios testes foram configurados como 80% SELECT e 20% UPDATE statement, portanto, 90% leitura aleatória.  A base de dados em si, que a SSB criou, tinha 1000 GB de tamanho. É composto por 15 mesas de utilizadores e 9.000.000 linhas por tabela de utilizadores e 8192 bytes por linha. 

O benchmark SSB é uma ferramenta de código aberto.  Está disponível livremente na página gitHub de [benchmark de armazenamento SQL.](https://github.com/NetApp/SQL_Storage_Benchmark.git)  


## <a name="in-summary"></a>Em resumo  

Com os Ficheiros Azure NetApp, pode aumentar o desempenho do servidor SQL, reduzindo significativamente o seu custo total de propriedade. 

## <a name="next-steps"></a>Passos Seguintes

* [Criar um volume SMB para o Azure NetApp Files](azure-netapp-files-create-volumes-smb.md) 
* [Arquiteturas de soluções usando ficheiros Azure NetApp – SQL Server](azure-netapp-files-solution-architectures.md#sql-server) 

