---
title: Desenhe mesas escaláveis e performantes no armazenamento da mesa Azure. | Microsoft Docs
description: Aprenda a desenhar mesas escaláveis e performantes no armazenamento de mesaS Azure. Rever divisórias de tabela, Transações do Grupo de Entidades e considerações de capacidade e custos.
services: storage
ms.service: storage
author: tamram
ms.author: tamram
ms.topic: article
ms.date: 03/09/2020
ms.subservice: tables
ms.openlocfilehash: 9fd274fb72c80475ca53d0f1bdedc1e09c10ea60
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88236510"
---
# <a name="design-scalable-and-performant-tables"></a>Criar tabelas escaláveis e de desempenho

[!INCLUDE [storage-table-cosmos-db-tip-include](../../../includes/storage-table-cosmos-db-tip-include.md)]

Para desenhar tabelas escaláveis e performativas, deve considerar fatores como desempenho, escalabilidade e custo. Se já concebeu esquemas para bases de dados relacionais, estas considerações são familiares, mas embora existam algumas semelhanças entre o modelo de armazenamento de serviços Azure Table e os modelos relacionais, existem também diferenças importantes. Estas diferenças normalmente levam a diferentes designs que podem parecer contraintuitivos ou errados para alguém familiarizado com bases de dados relacionais, mas fazem sentido se estiver a desenhar para uma loja de chaves/valor NoSQL, como o serviço Azure Table. Muitas das suas diferenças de design refletem o facto de o serviço Table ser projetado para apoiar aplicações em escala em nuvem que podem conter milhares de milhões de entidades (ou linhas na terminologia de base de dados relacionais) de dados ou para conjuntos de dados que devem suportar elevados volumes de transações. Por isso, deve pensar de forma diferente sobre como armazenar os seus dados e entender como funciona o serviço Table. Uma loja de dados NoSQL bem concebida pode permitir que a sua solução se dimensione muito mais e a um custo mais baixo do que uma solução que utiliza uma base de dados relacional. Este guia ajuda-o com estes tópicos.  

## <a name="about-the-azure-table-service"></a>Sobre o serviço Azure Table
Esta secção destaca algumas das principais características do serviço Table que são especialmente relevantes para a conceção de desempenho e escalabilidade. Se é novo no Azure Storage e no serviço Table, leia pela primeira vez [Introdução ao Armazenamento do Microsoft Azure](../../storage/common/storage-introduction.md) e [Inicie-se com o Azure Table Storage usando .NET](../../cosmos-db/table-storage-how-to-use-dotnet.md) antes de ler o restante deste artigo. Embora o foco deste guia esteja no serviço Mesa, inclui a discussão dos serviços Azure Queue e Blob, e como poderá utilizá-los com o serviço Table.  

O que é o serviço de mesa? Como seria de esperar do nome, o serviço Table utiliza um formato tabular para armazenar dados. Na terminologia padrão, cada fileira da tabela representa uma entidade, e as colunas armazenam as várias propriedades dessa entidade. Cada entidade tem um par de chaves para identificá-lo de forma única, e uma coluna de hora que o serviço Table usa para rastrear quando a entidade foi atualizada pela última vez. A estamp de tempo é aplicada automaticamente e não é possível substituir manualmente a placa de tempo com um valor arbitrário. O serviço Table utiliza esta última etiqueta de tempo modificada (LMT) para gerir a concordância otimista.  

> [!NOTE]
> As operações de API do serviço de mesa REST também devolvem um valor **ETag** que deriva do LMT. Este documento utiliza os termos ETag e LMT intercambiavelmente porque se referem aos mesmos dados subjacentes.  
> 
> 

O exemplo a seguir mostra um design de mesa simples para armazenar funcionários e entidades de departamento. Muitos dos exemplos mostrados mais tarde neste guia são baseados neste design simples.  

<table>
<tr>
<th>PartitionKey</th>
<th>RowKey</th>
<th>Timestamp</th>
<th></th>
</tr>
<tr>
<td>Marketing</td>
<td>00001</td>
<td>2014-08-22T00:50:32Z</td>
<td>
<table>
<tr>
<th>FirstName</th>
<th>LastName</th>
<th>Idade</th>
<th>E-mail</th>
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
<td>2014-08-22T00:50:34Z</td>
<td>
<table>
<tr>
<th>FirstName</th>
<th>LastName</th>
<th>Idade</th>
<th>E-mail</th>
</tr>
<tr>
<td>Jun</td>
<td>Cao</td>
<td>47</td>
<td>junc@contoso.com</td>
</tr>
</table>
</tr>
<tr>
<td>Marketing</td>
<td>Departamento</td>
<td>2014-08-22T00:50:30Z</td>
<td>
<table>
<tr>
<th>DepartmentName</th>
<th>Contagem de Funcionários</th>
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
<td>2014-08-22T00:50:44Z</td>
<td>
<table>
<tr>
<th>FirstName</th>
<th>LastName</th>
<th>Idade</th>
<th>E-mail</th>
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


Até agora, estes dados parecem semelhantes a uma tabela numa base de dados relacional, sendo as principais diferenças as colunas obrigatórias, e a capacidade de armazenar vários tipos de entidades na mesma tabela. Além disso, cada uma das propriedades definidas pelo utilizador, como **FirstName** ou **Age,** tem um tipo de dados, como o número inteiro ou o string, tal como uma coluna numa base de dados relacional. Embora ao contrário de uma base de dados relacional, a natureza sem esquemas do serviço Tabela significa que uma propriedade não precisa de ter o mesmo tipo de dados em cada entidade. Para armazenar tipos de dados complexos numa única propriedade, deve utilizar um formato serializado como JSON ou XML. Para obter mais informações sobre o serviço de tabelas, tais como tipos de dados suportados, intervalos de datas suportados, regras de nomeação e restrições de tamanho, consulte [compreender o Modelo de Dados de Serviço de Tabela](https://msdn.microsoft.com/library/azure/dd179338.aspx).

A sua escolha de **PartitionKey** e **RowKey** é fundamental para um bom design de mesa. Todas as entidades armazenadas numa tabela devem ter uma combinação única de **PartitionKey** e **RowKey**. Tal como acontece com as chaves numa tabela de bases de dados relacional, os valores **PartitionKey** e **RowKey** estão indexados para criar um índice agrupado para permitir visualizações rápidas. No entanto, o serviço Table não cria quaisquer índices secundários, pelo **que partitionKey** e **RowKey** são as únicas propriedades indexadas. Alguns dos padrões descritos nos [padrões de design de mesa](table-storage-design-patterns.md) ilustram como você pode trabalhar em torno desta limitação aparente.  

Uma tabela compreende uma ou mais divisórias, e muitas das decisões de design que tomarão estarão por perto escolhendo um **PartitionKey** e **RowKey** adequados para otimizar a sua solução. Uma solução pode consistir numa única tabela que contenha todas as suas entidades organizadas em divisórias, mas tipicamente uma solução tem várias tabelas. As tabelas ajudam-no a organizar logicamente as suas entidades, a ajudá-lo a gerir o acesso aos dados utilizando listas de controlo de acesso, e pode deixar cair uma mesa inteira usando uma única operação de armazenamento.  

## <a name="table-partitions"></a>Divisórias de mesa
O nome da conta, o nome da tabela e a **PartitionKey** em conjunto identificam a partição dentro do serviço de armazenamento onde o serviço de mesa armazena a entidade. Além de fazerem parte do regime de endereçamento das entidades, as divisórias definem um âmbito de transação (ver [Entity Group Transactions](#entity-group-transactions) abaixo), e formam a base de como a tabela de serviços escala. Para obter mais informações sobre as divisórias, consulte [a lista de verificação de desempenho e escalabilidade para armazenamento de mesas.](storage-performance-checklist.md)  

No serviço Table, um nó individual presta uma ou mais divisórias completas, e as balanças de serviço por divisórias de equilíbrio dinâmico de carga em nós. Se um nó estiver carregado, o serviço de mesa pode *dividir* a gama de divisórias servidas por esse nó em diferentes nós; quando o tráfego diminui, o serviço pode *fundir* as gamas de partição de nós silenciosos de volta para um único nó.  

Para obter mais informações sobre os detalhes internos do serviço Table, e em particular como o serviço gere as divisórias, consulte o papel [Microsoft Azure Storage: Um serviço de armazenamento de nuvem altamente disponível com forte consistência](https://docs.microsoft.com/archive/blogs/windowsazurestorage/sosp-paper-windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency).  

## <a name="entity-group-transactions"></a>Transações do Grupo entidade
No serviço Table, as Transações do Grupo Entity (EGTs) são o único mecanismo incorporado para a realização de atualizações atómicas em várias entidades. Os EGTs são por vezes também referidos como *transações de lote*. Os EGTs só podem operar em entidades armazenadas na mesma partição (isto é, partilhar a mesma chave de partição numa dada tabela). Por isso, sempre que necessitar de comportamento transacional atómico em várias entidades, deve garantir que essas entidades estão na mesma divisão. Esta é frequentemente uma razão para manter vários tipos de entidades na mesma tabela (e partição) e não usar várias tabelas para diferentes tipos de entidades. Um único EGT pode operar no máximo 100 entidades.  Se submeter vários EGTs simultâneos para processamento, é importante garantir que esses EGTs não operem em entidades comuns em EGTs; caso contrário, o processamento pode ser retardado.

Os EGTs também introduzem uma potencial compensação para que possa avaliar no seu design. Ou seja, usar mais divisórias aumenta a escalabilidade da sua aplicação, porque o Azure tem mais oportunidades para pedidos de equilíbrio de carga em nós. Mas usar mais divisórias pode limitar a capacidade da sua aplicação de realizar transações atómicas e manter uma forte consistência para os seus dados. Além disso, existem alvos específicos de escalabilidade ao nível de uma partição que podem limitar o rendimento das transações que se pode esperar de um único nó. Para obter mais informações sobre os objetivos de escalabilidade das contas de armazenamento padrão Azure, consulte [os objetivos de Escalaability para contas de armazenamento padrão](../common/scalability-targets-standard-account.md). Para obter mais informações sobre os objetivos de escalabilidade para o serviço Table, consulte [metas de escalabilidade e desempenho para armazenamento de mesa.](scalability-targets.md)

## <a name="capacity-considerations"></a>Considerações de capacidade

[!INCLUDE [storage-table-scale-targets](../../../includes/storage-tables-scale-targets.md)]

## <a name="cost-considerations"></a>Considerações de custos
O armazenamento de mesa é relativamente barato, mas deve incluir estimativas de custos tanto para o uso da capacidade como para a quantidade de transações como parte da sua avaliação de qualquer solução de serviço de tabela. No entanto, em muitos cenários, armazenar dados denormalizados ou duplicados de forma a melhorar o desempenho ou escalabilidade da sua solução é uma abordagem válida. Para obter mais informações sobre preços, consulte [o Preço de Armazenamento Azure](https://azure.microsoft.com/pricing/details/storage/).  

## <a name="next-steps"></a>Passos seguintes

- [Padrões de design de mesa](table-storage-design-patterns.md)
- [Modelar relações](table-storage-design-modeling.md)
- [Design das consultas](table-storage-design-for-query.md)
- [Encriptação de dados de tabela](table-storage-design-encrypt-data.md)
- [Design da modificação de dados](table-storage-design-for-modification.md)
