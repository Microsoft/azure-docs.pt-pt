---
title: Desenhe tabelas escaláveis e performantes no armazenamento de mesa Azure. | Microsoft Docs
description: Desenhe tabelas escaláveis e performantes no armazenamento de mesa Azure.
services: storage
author: SnehaGunda
ms.service: storage
ms.topic: article
ms.date: 03/09/2020
ms.author: sngun
ms.subservice: tables
ms.openlocfilehash: 8df639eea757c374554fa19e57c43cef79308e98
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79255148"
---
# <a name="design-scalable-and-performant-tables"></a>Criar tabelas escaláveis e de desempenho

[!INCLUDE [storage-table-cosmos-db-tip-include](../../../includes/storage-table-cosmos-db-tip-include.md)]

Para conceber tabelas escaláveis e performantes, deve considerar fatores como desempenho, escalabilidade e custo. Se já desenhou esquemas para bases de dados relacionais, estas considerações são familiares, mas embora existam algumas semelhanças entre o modelo de armazenamento de serviço seletiva e modelos relacionais, existem também diferenças importantes. Estas diferenças normalmente levam a diferentes designs que podem parecer contraintuitivos ou errados para alguém familiarizado com bases de dados relacionais, mas faz sentido se você está projetando para uma loja de chave/valor NoSQL, como o serviço De Mesa Azure. Muitas das suas diferenças de design refletem o facto de o serviço Tabela ter sido projetado para suportar aplicações em escala em nuvem que podem conter milhares de milhões de entidades (ou linhas na terminologia relacional da base de dados) de dados ou para conjuntos de dados que devem suportar transações elevadas volumes. Por isso, deve pensar de forma diferente sobre como armazena os seus dados e compreender como funciona o serviço De Mesa. Uma loja de dados NoSQL bem concebida pode permitir que a sua solução escale muito mais e a um custo mais baixo do que uma solução que utiliza uma base de dados relacional. Este guia ajuda-o com estes tópicos.  

## <a name="about-the-azure-table-service"></a>Sobre o serviço De Mesa Azure
Esta secção destaca algumas das principais características do serviço tabela que são especialmente relevantes para o design para o desempenho e escalabilidade. Se é novo no Serviço de Armazenamento Azure e mesa, leia primeiro [introdução ao Armazenamento Microsoft Azure](../../storage/common/storage-introduction.md) e inicie-se com o Armazenamento de Mesa [Azure utilizando .NET](../../cosmos-db/table-storage-how-to-use-dotnet.md) antes de ler o restante deste artigo. Embora o foco deste guia esteja no serviço Mesa, inclui a discussão dos serviços Azure Queue e Blob, e como poderá utilizá-los com o serviço Mesa.  

O que é o serviço de Mesa? Como seria de esperar do nome, o serviço Table utiliza um formato tabular para armazenar dados. Na terminologia padrão, cada linha da tabela representa uma entidade, e as colunas armazenam as várias propriedades dessa entidade. Cada entidade tem um par de chaves para identificá-lo de forma única, e uma coluna de carimbo suster o tempo que o serviço De Mesa usa para rastrear quando a entidade foi atualizada pela última vez. O carimbo de tempo é aplicado automaticamente e não pode substituir manualmente a marca de tempo com um valor arbitrário. O serviço de mesa utiliza este carimbo temporal (LMT) modificado para gerir a moeda otimista.  

> [!NOTE]
> O serviço de mesa REST API operações também devolvem um valor **ETag** que obtém do LMT. Este documento utiliza os termos ETag e LMT intercambiavelmente porque se referem aos mesmos dados subjacentes.  
> 
> 

O exemplo que se segue mostra um design de mesa simples para armazenar funcionários e entidades de departamento. Muitos dos exemplos mostrados mais tarde neste guia são baseados neste design simples.  

<table>
<tr>
<th>PartitionKey</th>
<th>RowKey</th>
<th>Carimbo de data/hora</th>
<th></th>
</tr>
<tr>
<td>Marketing</td>
<td>00001</td>
<td>2014-08-22t00:50:32Z</td>
<td>
<table>
<tr>
<th>FirstName</th>
<th>LastName</th>
<th>Idade</th>
<th>Email</th>
</tr>
<tr>
<td>Don</td>
<td>Salão</td>
<td>34</td>
<td>donh@contoso.com</td>
</tr>
</table>
</tr>
<tr>
<td>Marketing</td>
<td>00002</td>
<td>2014-08-22t00:50:34Z</td>
<td>
<table>
<tr>
<th>FirstName</th>
<th>LastName</th>
<th>Idade</th>
<th>Email</th>
</tr>
<tr>
<td>Jun</td>
<td>Rio Cao</td>
<td>47</td>
<td>junc@contoso.com</td>
</tr>
</table>
</tr>
<tr>
<td>Marketing</td>
<td>Departamento</td>
<td>2014-08-22t00:50:30Z</td>
<td>
<table>
<tr>
<th>DepartmentName</th>
<th>Contagem de funcionários</th>
</tr>
<tr>
<td>Marketing</td>
<td>153</td>
</tr>
</table>
</td>
</tr>
<tr>
<td>Sales</td>
<td>00010</td>
<td>2014-08-22t00:50:44Z</td>
<td>
<table>
<tr>
<th>FirstName</th>
<th>LastName</th>
<th>Idade</th>
<th>Email</th>
</tr>
<tr>
<td>Ken</td>
<td>Rio Kwok</td>
<td>23</td>
<td>kenk@contoso.com</td>
</tr>
</table>
</td>
</tr>
</table>


Até agora, estes dados parecem semelhantes a uma tabela numa base de dados relacional, sendo as principais diferenças as colunas obrigatórias e a capacidade de armazenar vários tipos de entidades na mesma tabela. Além disso, cada uma das propriedades definidas pelo utilizador, como **FirstName** ou **Age,** tem um tipo de dados, como o número inteiro ou o string, tal como uma coluna numa base de dados relacional. Embora ao contrário de uma base de dados relacional, a natureza sem esquemado serviço de Mesa significa que um imóvel não precisa ter o mesmo tipo de dados em cada entidade. Para armazenar tipos de dados complexos numa única propriedade, deve utilizar um formato serializado como JSON ou XML. Para obter mais informações sobre o serviço de mesa, tais como tipos de dados suportados, intervalos de data sondados, regras de nomeação e restrições de tamanho, consulte Compreender o Modelo de [Dados do Serviço](https://msdn.microsoft.com/library/azure/dd179338.aspx)de Mesa .

A sua escolha de **PartitionKey** e **RowKey** é fundamental para um bom design de mesa. Todas as entidades armazenadas numa mesa devem ter uma combinação única de **PartitionKey** e **RowKey**. Tal como acontece com as teclas numa tabela de bases de dados relacional, os valores **PartitionKey** e **RowKey** são indexados para criar um índice agrupado para permitir uma rápida pesquisa. No entanto, o serviço Tabela não cria quaisquer índices secundários, pelo que **partitionKey** e **RowKey** são as únicas propriedades indexadas. Alguns dos padrões descritos nos [padrões](table-storage-design-patterns.md) de design de tabela suprimam como pode trabalhar em torno desta limitação aparente.  

Uma tabela compreende uma ou mais divisórias, e muitas das decisões de design que tomar será em torno de escolher uma **Chave de Partição** adequada e **RowKey** para otimizar a sua solução. Uma solução pode consistir numa única tabela que contém todas as suas entidades organizadas em divisórias, mas tipicamente uma solução tem várias tabelas. As tabelas ajudam-no a organizar logicamente as suas entidades, ajudá-lo a gerir o acesso aos dados através de listas de controlo de acesso, e pode deixar cair uma tabela inteira usando uma única operação de armazenamento.  

## <a name="table-partitions"></a>Divisórias de mesa
O nome da conta, o nome da tabela e a **PartitionKey** identificam em conjunto a partição dentro do serviço de armazenamento onde o serviço de mesa armazena a entidade. Além de fazerem parte do regime de endereçamento das entidades, as divisórias definem uma margem de manobra para transações (ver [Operações](#entity-group-transactions) do Grupo entidade abaixo), e formam a base de como as escalas de serviço de mesa. Para obter mais informações sobre divisórias, consulte a lista de [verificação de desempenho e escalabilidade para armazenamento](storage-performance-checklist.md)de mesa .  

No serviço mesa, um nó individual presta serviços uma ou mais divisórias completas, e as escalas de serviço através de divisórias dinamicamente equilibradas em nós. Se um nó estiver sob carga, o serviço de mesa pode *dividir* a gama de divisórias servida saem por esse nó em diferentes nós; quando o tráfego diminui, o serviço pode *fundir* as divisórias desde nós silenciosos de volta a um único nó.  

Para obter mais informações sobre os detalhes internos do serviço Table e, em particular, como o serviço gere as divisórias, consulte o papel [Microsoft Azure Storage: A Highly Available Cloud Storage Service com Forte Consistência](https://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx).  

## <a name="entity-group-transactions"></a>Transações do Grupo entidade
No serviço de Mesa, as Transações do Grupo entidade (EGTs) são o único mecanismo incorporado para a realização de atualizações atómicas em várias entidades. Por vezes, os EGTs também são referidos como transações de *lote.* Os EGTs só podem operar em entidades armazenadas na mesma divisória (ou seja, partilhar a mesma chave de partição numa determinada tabela). Assim, sempre que necessitar de comportamento transacional atómico em várias entidades, deve garantir que essas entidades estão na mesma divisão. Esta é muitas vezes uma razão para manter vários tipos de entidades na mesma tabela (e partição) e não usar várias tabelas para diferentes tipos de entidades. Um único EGT pode operar no máximo 100 entidades.  Se submeter múltiplos EGTs simultâneos para processamento, é importante garantir que esses EGTs não operam em entidades que são comuns em todos os EGTs; caso contrário, o processamento pode ser adiado.

Os EGTs também introduzem uma potencial compensação para você avaliar no seu design. Ou seja, usar mais divisórias aumenta a escalabilidade da sua aplicação, porque o Azure tem mais oportunidades para equilibrar a carga nos nós. Mas usar mais divisórias pode limitar a capacidade da sua aplicação para realizar transações atómicas e manter uma forte consistência para os seus dados. Além disso, existem alvos específicos de escalabilidade ao nível de uma partição que podem limitar a entrada de transações que você pode esperar para um único nó. Para obter mais informações sobre os objetivos de escalabilidade das contas de armazenamento padrão do Azure, consulte os objetivos de [escalabilidade para as contas de armazenamento padrão](../common/scalability-targets-standard-account.md). Para obter mais informações sobre os objetivos de escalabilidade para o serviço de mesa, consulte os objetivos de [escalabilidade e desempenho para o armazenamento](scalability-targets.md)de mesas .

## <a name="capacity-considerations"></a>Considerações de capacidade

[!INCLUDE [storage-table-scale-targets](../../../includes/storage-tables-scale-targets.md)]

## <a name="cost-considerations"></a>Considerações de custos
O armazenamento de mesa é relativamente barato, mas deve incluir estimativas de custos tanto para o uso da capacidade como para a quantidade de transações como parte da sua avaliação de qualquer solução de serviço tabela. No entanto, em muitos cenários, armazenar dados desnormalizados ou duplicados de forma a melhorar o desempenho ou a escalabilidade da sua solução é uma abordagem válida. Para mais informações sobre preços, consulte o [Preço de Armazenamento do Azure](https://azure.microsoft.com/pricing/details/storage/).  

## <a name="next-steps"></a>Passos seguintes

- [Padrões de design de mesa](table-storage-design-patterns.md)
- [Modelar relações](table-storage-design-modeling.md)
- [Design das consultas](table-storage-design-for-query.md)
- [Encriptar dados de tabela](table-storage-design-encrypt-data.md)
- [Design da modificação de dados](table-storage-design-for-modification.md)
