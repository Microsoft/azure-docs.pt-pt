---
title: Design dimensionável e de elevado desempenho tabelas no armazenamento de tabelas do Azure. | Microsoft Docs
description: Design dimensionável e de elevado desempenho tabelas no armazenamento de tabelas do Azure.
services: storage
author: SnehaGunda
ms.service: storage
ms.topic: article
ms.date: 04/23/2018
ms.author: sngun
ms.subservice: tables
ms.openlocfilehash: 8387e41d57edfa0e54ac930c9462714aca571f2a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60848287"
---
# <a name="design-scalable-and-performant-tables"></a>Criar tabelas escaláveis e de desempenho

[!INCLUDE [storage-table-cosmos-db-tip-include](../../../includes/storage-table-cosmos-db-tip-include.md)]

Design dimensionável e de tabelas de alto desempenho, deve considerar fatores como custo, a escalabilidade e desempenho. Se tiver que anteriormente criado esquemas para bases de dados relacionais, estas considerações estão familiarizadas, mas embora haja algumas semelhanças entre os modelos relacionais e o modelo de armazenamento do serviço de tabelas do Azure, também existem diferenças importantes. Essas diferenças, normalmente, levam a designs diferentes que podem ter um aspeto achem ou errada para alguém familiarizado com as bases de dados relacionais, mas fazer sentido se está Projetando para um arquivo de chave/valor NoSQL como o serviço de tabelas do Azure. Muitas das diferenças de design refletem o fato de que o serviço de tabela foi concebido para suportar aplicações de escala da cloud que podem conter bilhões de entidades (ou linhas na terminologia de base de dados relacional) de dados de ou para conjuntos de dados tem de suportar alta transação volumes. Por conseguinte, tem de pensar de forma diferente sobre como armazenar os seus dados e compreender como funciona o serviço de tabela. Um arquivo de dados NoSQL bem projetado pode ativar a sua solução para dimensionar muito mais e a um custo menor do que uma solução que utiliza uma base de dados relacional. Este guia ajuda-o com estes tópicos.  

## <a name="about-the-azure-table-service"></a>Sobre o serviço de tabelas do Azure
Esta secção destaca alguns dos principais recursos do serviço de tabela que são especialmente relevantes para o projeto para o desempenho e escalabilidade. Se estiver familiarizado com o armazenamento do Azure e o serviço de tabela, leia primeiro [introdução ao armazenamento do Microsoft Azure](../../storage/common/storage-introduction.md) e [introdução ao armazenamento de tabelas do Azure com o .NET](../../cosmos-db/table-storage-how-to-use-dotnet.md) antes de ler o restante deste artigo . Embora o foco deste guia é o serviço de tabela, ele inclui a discussão sobre os serviços de BLOBs e filas do Azure e como pode usá-los com o serviço de tabela.  

O que é o serviço de tabela? Como pode esperar do nome, o serviço de tabela utiliza um formato tabular para armazenar dados. Na terminologia padrão, cada linha da tabela representa uma entidade e as colunas armazenam várias propriedades de entidade. Cada entidade tem um par de chaves para identificar exclusivamente e uma coluna de timestamp que o serviço de tabela usa para controlar quando a entidade foi atualizado pela última vez. O carimbo de hora é aplicado automaticamente e manualmente não é possível substituir o período de tempo com um valor arbitrário. O serviço de tabelas utiliza este timestamp (LMT) da última modificação para gerir a simultaneidade otimista.  

> [!NOTE]
> As operações de REST API do serviço de tabela também devolvem uma **ETag** valor que ela é derivada do LMT. Este documento usa termos ETag e LMT alternadamente porque eles fazem referência aos mesmos dados subjacentes.  
> 
> 

O exemplo seguinte mostra um design de tabela simples para armazenar as entidades employee e department. Muitos dos exemplos mostrados mais tarde neste guia são baseiam-se este design simples.  

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
<td>2014-08-22T00:50:32Z</td>
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
<td>Hall</td>
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
<th>Email</th>
</tr>
<tr>
<td>Jun</td>
<td>CaO</td>
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
<th>Nome do Departamento</th>
<th>EmployeeCount</th>
</tr>
<tr>
<td>Marketing</td>
<td>153</td>
</tr>
</table>
</td>
</tr>
<tr>
<td>Vendas</td>
<td>00010</td>
<td>2014-08-22T00:50:44Z</td>
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
<td>Kwok</td>
<td>23</td>
<td>kenk@contoso.com</td>
</tr>
</table>
</td>
</tr>
</table>


Até agora, estes dados parece ser semelhantes a uma tabela na base de dados relacional com as diferenças principais sendo as colunas obrigatórias e a capacidade de armazenar vários tipos de entidade na mesma tabela. Além disso, cada uma das propriedades definidas pelo utilizador, tal como **FirstName** ou **idade** tem um tipo de dados, como integer ou string, tal como uma coluna na base de dados relacional. Embora ao contrário de na base de dados relacional, a natureza de esquema do serviço tabela significa que uma propriedade não precisa ter os mesmo tipo em cada entidade de dados. Para armazenar tipos de dados complexos numa única propriedade, tem de utilizar um formato serializado como JSON ou XML. Para obter mais informações sobre os tipos de dados do serviço, como suporte de tabela, intervalos de datas suportados, as regras de nomenclatura e restrições de tamanho, consulte [Noções básicas sobre o modelo de dados do serviço de tabela](https://msdn.microsoft.com/library/azure/dd179338.aspx).

Sua escolha de **PartitionKey** e **RowKey** é fundamental para o design da tabela de bom. Todas as entidades armazenadas numa tabela tem de ter uma combinação única de **PartitionKey** e **RowKey**. Tal como acontece com as chaves numa tabela de base de dados relacional, o **PartitionKey** e **RowKey** valores são indexados para criar um índice em cluster para ativar look-ups rápidas. No entanto, o serviço de tabela não cria índices secundários, então **PartitionKey** e **RowKey** são as únicas propriedades indexadas. Alguns dos padrões descritos em [padrões de design de tabela](table-storage-design-patterns.md) ilustrar como pode contornar esta limitação aparente.  

Uma tabela é composto por uma ou mais partições e, muitas das decisões de design que tomar será em torno de escolher um adequado **PartitionKey** e **RowKey** para otimizar a sua solução. Uma solução pode consistir numa única tabela que contém todas as suas entidades organizadas em partições, mas, normalmente, uma solução tem várias tabelas. Tabelas ajudá-lo a logicamente organizar suas entidades, ajudar a gerir o acesso aos dados através de listas de controlo de acesso, e pode soltar uma tabela inteira através de uma operação de armazenamento única.  

## <a name="table-partitions"></a>Partições da tabela
O nome de conta, o nome da tabela, e **PartitionKey** em conjunto identificam a partição dentro do serviço de armazenamento onde o serviço de tabela armazena a entidade. Bem como a fazer parte do esquema de endereçamento para entidades, partições definem um âmbito para transações (consulte [transações de grupo de entidades](#entity-group-transactions) abaixo), e formar a base da forma como o serviço de tabela pode ser dimensionada. Para obter mais informações sobre as partições, consulte [metas de desempenho e escalabilidade do armazenamento do Azure](../../storage/common/storage-scalability-targets.md).  

No serviço tabela, um nó individual dos serviços de um ou mais concluir as partições e as escalas de serviço por dinamicamente balanceamento de carga partições entre nós. Se for um nó sob carga, o serviço de tabela pode *dividir* o intervalo de partições atendidos por esse nó em diferentes nós; quando o tráfego diminua, o serviço pode *intercalação* os intervalos de partição de nós silencioso num único nó.  

Para obter mais informações sobre os detalhes internos do serviço tabela e, em particular, como o serviço gere as partições, consulte o documento [armazenamento do Microsoft Azure: Um serviço de armazenamento de Cloud de elevada disponibilidade com consistência forte](https://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx).  

## <a name="entity-group-transactions"></a>Transações do grupo de entidade
No serviço tabela, transações de grupo de entidades (EGTs) são o mecanismo interno apenas para a realização de atualizações atómicas em múltiplas entidades. EGTs são, às vezes, também denominados *do batch transações*. EGTs só pode funcionar em entidades armazenadas na mesma partição (ou seja, partilham a mesma chave de partição numa determinada tabela). Portanto, sempre que necessitar de comportamento transacional atómico em múltiplas entidades, certifique-se de que essas entidades estão na mesma partição. Isto é, muitas vezes, um motivo para manter os vários tipos de entidade na mesma tabela (e partição) e não a utilizar várias tabelas para tipos de entidade diferentes. Um único EGT pode operar no máximo 100 entidades.  Se submeter vários EGTs simultâneas para processamento, é importante certificar-se de que esses EGTs não funciona em entidades que são comuns em EGTs; caso contrário, o processamento pode ser atrasado.

EGTs também introduzir uma potencial desvantagem para avaliar em seu design. Ou seja, usar mais partições aumenta a escalabilidade do seu aplicativo, porque o Azure tem mais oportunidades de balanceamento de carga pedidos em nós. Mas usar mais partições pode limitar a capacidade do seu aplicativo para executar transações atómicas e manter a consistência forte para os seus dados. Além disso, existem destinos de escalabilidade específico no nível de uma partição que pode limitar o débito de transações que pode esperar para um único nó. Para obter mais informações sobre os destinos de escalabilidade para contas de armazenamento do Azure e o serviço de tabela, consulte [metas de desempenho e escalabilidade do armazenamento do Azure](../../storage/common/storage-scalability-targets.md).   

## <a name="capacity-considerations"></a>Considerações sobre capacidade
A tabela seguinte descreve alguns dos valores chave estar ciente de quando está Projetando uma solução de serviço de tabela:  

| Capacidade total de uma conta de armazenamento do Azure | 500 TB |
| --- | --- |
| Número de tabelas numa conta de armazenamento do Azure |Limitado apenas pela capacidade da conta de armazenamento |
| Número de partições numa tabela |Limitado apenas pela capacidade da conta de armazenamento |
| Número de entidades numa partição |Limitado apenas pela capacidade da conta de armazenamento |
| Tamanho de uma entidade individual |Até 1 MB com um máximo de 255 propriedades (incluindo o **PartitionKey**, **RowKey**, e **Timestamp**) |
| Tamanho do **PartitionKey** |A cadeia de caracteres até 1 KB de tamanho |
| Tamanho do **RowKey** |A cadeia de caracteres até 1 KB de tamanho |
| Tamanho de uma transação de grupos de entidades |Uma transação pode incluir até 100 entidades e a carga tem de ser inferior a 4 MB de tamanho. Uma vez, um EGT só pode atualizar uma entidade. |

Para obter mais informações, consulte [Noções Básicas sobre o Modelo de Dados do Serviço Tabela](https://msdn.microsoft.com/library/azure/dd179338.aspx).  

## <a name="cost-considerations"></a>Considerações de custos
Armazenamento de tabelas está relativamente mais barato, mas deve incluir estimativas de custo de utilização da capacidade e a quantidade de transações como parte de sua avaliação de qualquer solução de serviço de tabela. No entanto, em muitos cenários, a armazenar dados desnormalizados ou duplicados para melhorar o desempenho ou escalabilidade da sua solução é uma abordagem válida. Para obter mais informações sobre preços, consulte [preços de armazenamento do Azure](https://azure.microsoft.com/pricing/details/storage/).  

## <a name="next-steps"></a>Passos Seguintes

- [Padrões de Design de tabela](table-storage-design-patterns.md)
- [Modelando as relações](table-storage-design-modeling.md)
- [Design para a consulta](table-storage-design-for-query.md)
- [Encriptação de dados de tabela](table-storage-design-encrypt-data.md)
- [Design para modificação de dados](table-storage-design-for-modification.md)
