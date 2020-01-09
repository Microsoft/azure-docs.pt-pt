---
title: Crie tabelas escalonáveis e de alto desempenho no armazenamento de tabelas do Azure. | Microsoft Docs
description: Crie tabelas escalonáveis e de alto desempenho no armazenamento de tabelas do Azure.
services: storage
author: SnehaGunda
ms.service: storage
ms.topic: article
ms.date: 04/23/2018
ms.author: sngun
ms.subservice: tables
ms.openlocfilehash: 95272956da4567ec21e1c4603b88472e45373a39
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75351183"
---
# <a name="design-scalable-and-performant-tables"></a>Criar tabelas escaláveis e de desempenho

[!INCLUDE [storage-table-cosmos-db-tip-include](../../../includes/storage-table-cosmos-db-tip-include.md)]

Para criar tabelas escalonáveis e de alto desempenho, você deve considerar os fatores como performance, escalabilidade e custo. Se você tiver criado anteriormente esquemas para bancos de dados relacionais, essas considerações serão familiares, mas embora haja algumas semelhanças entre o modelo de armazenamento do serviço tabela do Azure e os modelos relacionais, também há diferenças importantes. Essas diferenças normalmente levam a diferentes designs que podem parecer um contador intuitivo ou errado para alguém familiarizado com bancos de dados relacionais, mas faça sentido se você estiver criando um repositório de chave/valor NoSQL, como o serviço tabela do Azure. Muitas das suas diferenças de design refletem o fato de que o serviço tabela foi projetado para oferecer suporte a aplicativos em escala de nuvem que podem conter bilhões de entidades (ou linhas na terminologia de banco de dados relacional) ou para conjuntos de dados que devem dar suporte a transações altas volumes. Portanto, você deve pensar de forma diferente sobre como armazenar seus dados e entender como o serviço tabela funciona. Um armazenamento de dados NoSQL bem projetado pode permitir que sua solução seja dimensionada muito além e com um custo menor do que uma solução que usa um banco de dados relacional. Este guia ajuda-o com estes tópicos.  

## <a name="about-the-azure-table-service"></a>Sobre o serviço de tabelas do Azure
Esta secção destaca alguns dos principais recursos do serviço de tabela que são especialmente relevantes para o projeto para o desempenho e escalabilidade. Se você for novo no armazenamento do Azure e no serviço tabela, leia primeiro [a introdução à armazenamento do Microsoft Azure](../../storage/common/storage-introduction.md) e comece a [usar o armazenamento de tabelas do Azure usando o .net](../../cosmos-db/table-storage-how-to-use-dotnet.md) antes de ler o restante deste artigo. Embora o foco deste guia seja no serviço tabela, ele inclui a discussão dos serviços de fila e BLOB do Azure e como você pode usá-los com o serviço tabela.  

O que é o serviço de tabela? Como pode esperar do nome, o serviço de tabela utiliza um formato tabular para armazenar dados. Na terminologia padrão, cada linha da tabela representa uma entidade e as colunas armazenam várias propriedades de entidade. Cada entidade tem um par de chaves para identificá-la exclusivamente e uma coluna de carimbo de data/hora que o serviço tabela usa para controlar quando a entidade foi atualizada pela última vez. O carimbo de data/hora é aplicado automaticamente e não é possível substituir manualmente o carimbo de data/hora por um valor arbitrário. O serviço de tabelas utiliza este timestamp (LMT) da última modificação para gerir a simultaneidade otimista.  

> [!NOTE]
> As operações da API REST do serviço tabela também retornam um valor de **ETag** derivado do LMT. Este documento usa os termos ETag e LMT interalteradomente porque eles se referem aos mesmos dados subjacentes.  
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
<th>Apelido</th>
<th>Idade</th>
<th>E-mail</th>
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
<th>Apelido</th>
<th>Idade</th>
<th>E-mail</th>
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
<th>DepartmentName</th>
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
<td>Sales</td>
<td>00010</td>
<td>2014-08-22T00:50:44Z</td>
<td>
<table>
<tr>
<th>FirstName</th>
<th>Apelido</th>
<th>Idade</th>
<th>E-mail</th>
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


Até agora, esses dados parecem ser semelhantes a uma tabela em um banco de dados relacional com as principais diferenças sendo as colunas obrigatórias e a capacidade de armazenar vários tipos de entidade na mesma tabela. Além disso, cada uma das propriedades definidas pelo usuário, como **FirstName** ou **age** , tem um tipo de dados, como inteiro ou cadeia de caracteres, assim como uma coluna em um banco de dado relacional. Embora ao contrário de na base de dados relacional, a natureza de esquema do serviço tabela significa que uma propriedade não precisa ter os mesmo tipo em cada entidade de dados. Para armazenar tipos de dados complexos numa única propriedade, tem de utilizar um formato serializado como JSON ou XML. Para obter mais informações sobre os tipos de dados do serviço, como suporte de tabela, intervalos de datas suportados, as regras de nomenclatura e restrições de tamanho, consulte [Noções básicas sobre o modelo de dados do serviço de tabela](https://msdn.microsoft.com/library/azure/dd179338.aspx).

Sua escolha de **PartitionKey** e **RowKey** é fundamental para um bom design de tabela. Todas as entidades armazenadas numa tabela tem de ter uma combinação única de **PartitionKey** e **RowKey**. Assim como acontece com as chaves em uma tabela de banco de dados relacional, os valores de **PartitionKey** e **RowKey** são indexados para criar um índice clusterizado para habilitar as pesquisas rápidas. No entanto, o serviço tabela não cria índices secundários, portanto, **PartitionKey** e **RowKey** são as únicas propriedades indexadas. Alguns dos padrões descritos em [padrões de design de tabela](table-storage-design-patterns.md) ilustram como você pode contornar essa limitação aparente.  

Uma tabela é composta de uma ou mais partições, e muitas das decisões de design que você tomará estarão relacionadas à escolha de um **PartitionKey** e **RowKey** adequados para otimizar sua solução. Uma solução pode consistir em uma única tabela que contém todas as suas entidades organizadas em partições, mas normalmente uma solução tem várias tabelas. Tabelas ajudá-lo a logicamente organizar suas entidades, ajudar a gerir o acesso aos dados através de listas de controlo de acesso, e pode soltar uma tabela inteira através de uma operação de armazenamento única.  

## <a name="table-partitions"></a>Partições da tabela
O nome de conta, o nome da tabela, e **PartitionKey** em conjunto identificam a partição dentro do serviço de armazenamento onde o serviço de tabela armazena a entidade. Bem como a fazer parte do esquema de endereçamento para entidades, partições definem um âmbito para transações (consulte [transações de grupo de entidades](#entity-group-transactions) abaixo), e formar a base da forma como o serviço de tabela pode ser dimensionada. Para obter mais informações sobre partições, consulte [lista de verificação de desempenho e escalabilidade para armazenamento de tabelas](storage-performance-checklist.md).  

No serviço tabela, um nó individual instala uma ou mais partições completas, e o serviço é dimensionado dinamicamente com balanceamento de carga de partições entre nós. Se for um nó sob carga, o serviço de tabela pode *dividir* o intervalo de partições atendidos por esse nó em diferentes nós; quando o tráfego diminua, o serviço pode *intercalação* os intervalos de partição de nós silencioso num único nó.  

Para obter mais informações sobre os detalhes internos do serviço tabela e, em particular, como o serviço gere as partições, consulte o documento [armazenamento do Microsoft Azure: A altamente disponível serviço armazenamento na Cloud com consistência forte](https://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx).  

## <a name="entity-group-transactions"></a>Transações do grupo de entidade
No serviço tabela, transações de grupo de entidades (EGTs) são o mecanismo interno apenas para a realização de atualizações atómicas em múltiplas entidades. Às vezes, as EGTs também são chamadas de *transações em lote*. EGTs só pode operar em entidades armazenadas na mesma partição (ou seja, compartilhar a mesma chave de partição em uma determinada tabela). Assim, sempre que você precisar do comportamento transacional atômico em várias entidades, deverá garantir que essas entidades estejam na mesma partição. Isto é, muitas vezes, um motivo para manter os vários tipos de entidade na mesma tabela (e partição) e não a utilizar várias tabelas para tipos de entidade diferentes. Um único EGT pode operar no máximo 100 entidades.  Se você enviar vários EGTs simultâneos para processamento, é importante garantir que esses EGTs não operem em entidades comuns em EGTs; caso contrário, o processamento poderá ser atrasado.

O EGTs também introduz uma possível compensação para você avaliar em seu design. Ou seja, o uso de mais partições aumenta a escalabilidade do seu aplicativo, pois o Azure tem mais oportunidades para balancear a carga de solicitações entre nós. Mas usar mais partições pode limitar a capacidade de seu aplicativo executar transações atômicas e manter uma consistência forte para seus dados. Além disso, há metas de escalabilidade específicas no nível de uma partição que pode limitar a taxa de transferência de transações que você pode esperar para um único nó. Para obter mais informações sobre metas de escalabilidade para contas de armazenamento standard do Azure, consulte [metas de escalabilidade para contas de armazenamento padrão](../common/scalability-targets-standard-account.md). Para obter mais informações sobre metas de escalabilidade para o serviço tabela, consulte [escalabilidade e metas de desempenho para o armazenamento de tabelas](scalability-targets.md).

## <a name="capacity-considerations"></a>Considerações sobre capacidade
A tabela a seguir descreve alguns dos principais valores a serem considerados quando você está criando uma solução de serviço tabela:  

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
O armazenamento de tabela é relativamente barato, mas você deve incluir estimativas de custo para o uso de capacidade e a quantidade de transações como parte da sua avaliação de qualquer solução de serviço de tabela. No entanto, em muitos cenários, armazenar dados desnormalizados ou duplicados para melhorar o desempenho ou a escalabilidade de sua solução é uma abordagem válida. Para obter mais informações sobre preços, consulte [preços de armazenamento do Azure](https://azure.microsoft.com/pricing/details/storage/).  

## <a name="next-steps"></a>Passos seguintes

- [Padrões de design de tabela](table-storage-design-patterns.md)
- [Relações de modelagem](table-storage-design-modeling.md)
- [Design para consulta](table-storage-design-for-query.md)
- [Criptografando dados da tabela](table-storage-design-encrypt-data.md)
- [Design para modificação de dados](table-storage-design-for-modification.md)
