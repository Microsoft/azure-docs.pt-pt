---
title: Padrões de design de tabela de armazenamento do Azure | Documentos da Microsoft
description: Utilize padrões para soluções de serviço de tabela do Azure.
services: storage
author: MarkMcGeeAtAquent
ms.service: storage
ms.topic: article
ms.date: 04/23/2018
ms.author: sngun
ms.subservice: tables
ms.openlocfilehash: f2f4fb04ac483f7716c0b7a0fb1f87843d8b817f
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57995306"
---
# <a name="table-design-patterns"></a>Padrões de design da tabela
Este artigo descreve alguns padrões adequados para utilização com soluções de serviço de tabela. Além disso, irá ver como pode praticamente solucionar alguns problemas e compensações abordadas em outros artigos de design do armazenamento de tabela. O diagrama seguinte resume as relações entre os diferentes padrões:  

![para procurar dados relacionados](media/storage-table-design-guide/storage-table-design-IMAGE05.png)


O mapa padrão acima destaca algumas relações entre (azuis) de padrões e antipadrões (laranja) que estão documentados neste guia. Existem muitos outros padrões que vale a pena examinar. Por exemplo, um dos principais cenários para o serviço de tabela é usar o [padrão de vista materializada](https://msdn.microsoft.com/library/azure/dn589782.aspx) partir a [comando segregação de responsabilidade da consulta (CQRS)](https://msdn.microsoft.com/library/azure/jj554200.aspx) padrão.  

## <a name="intra-partition-secondary-index-pattern"></a>Padrão de partição dentro de índice secundário
Store várias cópias de cada entidade usando diferentes **RowKey** valores (na mesma partição) para ativar rápida e eficientes pesquisas e ordens de classificação alternativas ao utilizar diferentes **RowKey** valores. As atualizações entre cópias podem ser mantidas consistentes EGT a utilizar.  

### <a name="context-and-problem"></a>Contexto e problema
O serviço de tabela indexa automaticamente as entidades com o **PartitionKey** e **RowKey** valores. Isto permite que um aplicativo de cliente para recuperar a entidade com eficiência com estes valores. Por exemplo, usando a estrutura da tabela abaixo, uma aplicação cliente pode utilizar uma consulta de ponto para obter uma entidade employee individuais com o nome do departamento e a identificação do funcionário (a **PartitionKey** e **RowKey**  valores). Um cliente também pode obter entidades ordenadas por ID de funcionário dentro de cada departamento.

![Image06](media/storage-table-design-guide/storage-table-design-IMAGE06.png)

Se pretender conseguir localizar uma entidade de funcionários com base no valor de outra propriedade, como o endereço de e-mail, também tem de utilizar uma análise de partição menos eficiente para encontrar uma correspondência. Isto acontece porque o serviço de tabela não fornece índices secundários. Além disso, não existe nenhuma opção para solicitar uma lista de funcionários ordenados numa ordem diferente daquela **RowKey** ordem.  

### <a name="solution"></a>Solução
Para solucionar a falta de índices secundários, é possível armazenar várias cópias de cada entidade com cada cópia utilizando outro **RowKey** valor. Se armazenar uma entidade com as estruturas de mostrado abaixo, pode recuperar com eficiência as entidades de funcionários com base no ID. o endereço ou funcionário do e-mail O prefixo de valores para o **RowKey**, "empid_" e "email_" permitem consultar para um único funcionário ou um intervalo de funcionários com um intervalo de endereços de e-mail ou ids de funcionários.  

![Entidades de funcionários](media/storage-table-design-guide/storage-table-design-IMAGE07.png)

Os seguintes critérios de filtro de dois (um procurar por ID de funcionário e uma pesquisa por endereço de e-mail), ambos especificar consultas de ponto:  

* $filter = (PartitionKey eq "Vendas") e (RowKey eq "empid_000223")  
* $filter = (PartitionKey eq "Vendas") e (RowKey eq 'email_jonesj@contoso.com")  

Se consultar um intervalo de entidades de funcionários, pode especificar um intervalo ordenados por ordem de ID do funcionário ou um intervalo ordenados por ordem de endereço de e-mail através da consulta para entidades com o prefixo adequado no **RowKey**.  

* Para localizar todos os funcionários do departamento de vendas com um funcionário ID no intervalo 000100 para 000199 utilização: $filter = (PartitionKey eq "Vendas") e (RowKey ge "empid_000100") e (RowKey le "empid_000199")  
* Para localizar todos os funcionários do departamento de vendas com um endereço de e-mail que começam com a letra "a" utilização: $filter = (PartitionKey eq "Vendas") e (RowKey ge "email_a") e (RowKey lt "email_b")  
  
  Tenha em atenção que a sintaxe de filtro utilizada nos exemplos acima é a partir da API de REST do serviço tabela para obter mais informações, consulte [consultar entidades](https://msdn.microsoft.com/library/azure/dd179421.aspx).  

### <a name="issues-and-considerations"></a>Problemas e considerações
Na altura de decidir como implementar este padrão, considere os seguintes pontos:  

* Armazenamento de tabelas é relativamente barato usar de modo que a sobrecarga de custos do armazenamento de dados duplicados não deve ser uma preocupação importante. No entanto, deve sempre avaliar o custo de seu design com base nos seus requisitos de armazenamento previsto e apenas adicionar duplicadas entidades para suportar as consultas que a aplicação de cliente será executado.  
* Como as entidades de índice secundário são armazenadas na mesma partição que as entidades originais, deve certificar-se de que não excedem os destinos de escalabilidade para uma partição individual.  
* Pode manter as suas entidades duplicadas consistentes entre si utilizando EGTs para atualizar as duas cópias da entidade de atomicamente. Isso implica que deve armazenar todas as cópias de uma entidade na mesma partição. Para obter mais informações, consulte a secção [transações de grupo de entidades usando](table-storage-design.md#entity-group-transactions).  
* O valor utilizado para o **RowKey** tem de ser exclusivo para cada entidade. Considere a utilização de valores da chave compostas.  
* Preenchimento de valores numéricos no **RowKey** (por exemplo, o funcionário ID 000223), permite corrigir de classificação e filtragem com base no superior e os limites inferiores.  
* Não precisa necessariamente duplicar todas as propriedades de entidade. Por exemplo, se as consultas que pesquisa as entidades com a mensagem de e-mail de endereços no **RowKey** nunca precisam de idade do funcionário, estas entidades poderiam ter a seguinte estrutura:

   ![Estrutura de entidade de funcionário](media/storage-table-design-guide/storage-table-design-IMAGE08.png)


* É, normalmente, é melhor armazenar dados duplicados e certifique-se de que possa recuperar todos os dados que precisa com uma única consulta, que usar uma consulta para localizar uma entidade e outro para pesquisar os dados necessários.  

### <a name="when-to-use-this-pattern"></a>Quando utilizar este padrão
Utilize este padrão quando a aplicação cliente precisa obter entidades utilizando uma variedade de chaves diferentes, quando o cliente tem de obter entidades em diferentes ordens de classificação, e onde é possível identificar cada entidade usando uma variedade de valores exclusivos. No entanto, deve se certificar de que excede os limites de escalabilidade de partição quando estiver a efetuar pesquisas de entidade usando o diferentes **RowKey** valores.  

### <a name="related-patterns-and-guidance"></a>Padrões e orientações relacionados
Os padrões e orientações que se seguem podem também ser relevantes ao implementar este padrão:  

* [Padrão de índice de partição entre secundário](#inter-partition-secondary-index-pattern)
* [Padrão de chave composta](#compound-key-pattern)
* Transações do grupo de entidade
* [Trabalhar com tipos de entidade heterogênea](#working-with-heterogeneous-entity-types)

## <a name="inter-partition-secondary-index-pattern"></a>Padrão de índice de partição entre secundário
Store várias cópias de cada entidade usando diferentes **RowKey** valores em separar as partições ou separar em tabelas a ativação rápida e eficientes pesquisas e a ordens de classificação alternativas ao utilizar diferentes **RowKey**valores.  

### <a name="context-and-problem"></a>Contexto e problema
O serviço de tabela indexa automaticamente as entidades com o **PartitionKey** e **RowKey** valores. Isto permite que um aplicativo de cliente para recuperar a entidade com eficiência com estes valores. Por exemplo, usando a estrutura da tabela abaixo, uma aplicação cliente pode utilizar uma consulta de ponto para obter uma entidade employee individuais com o nome do departamento e a identificação do funcionário (a **PartitionKey** e **RowKey**  valores). Um cliente também pode obter entidades ordenadas por ID de funcionário dentro de cada departamento.  

![ID do Empregado](media/storage-table-design-guide/storage-table-design-IMAGE09.png)

Se pretender conseguir localizar uma entidade de funcionários com base no valor de outra propriedade, como o endereço de e-mail, também tem de utilizar uma análise de partição menos eficiente para encontrar uma correspondência. Isto acontece porque o serviço de tabela não fornece índices secundários. Além disso, não existe nenhuma opção para solicitar uma lista de funcionários ordenados numa ordem diferente daquela **RowKey** ordem.  

Prevendo o um volume muito elevado de transações em relação a essas entidades e deseja minimizar o risco do serviço de tabela limitação seu cliente.  

### <a name="solution"></a>Solução
Para solucionar a falta de índices secundários, é possível armazenar várias cópias de cada entidade com cada cópia usando diferentes **PartitionKey** e **RowKey** valores. Se armazenar uma entidade com as estruturas de mostrado abaixo, pode recuperar com eficiência as entidades de funcionários com base no ID. o endereço ou funcionário do e-mail O prefixo de valores para o **PartitionKey**, "empid_" e "email_" ative-o a identificar que o índice que pretende utilizar para uma consulta.  

![Índice principal e o índice secundário](media/storage-table-design-guide/storage-table-design-IMAGE10.png)


Os seguintes critérios de filtro de dois (um procurar por ID de funcionário e uma pesquisa por endereço de e-mail), ambos especificar consultas de ponto:  

* $filter = (PartitionKey eq ' empid_Sales") e (RowKey eq"000223")
* $filter = (PartitionKey eq ' email_Sales") e (RowKey eq 'jonesj@contoso.com")  

Se consultar um intervalo de entidades de funcionários, pode especificar um intervalo ordenados por ordem de ID do funcionário ou um intervalo ordenados por ordem de endereço de e-mail através da consulta para entidades com o prefixo adequado no **RowKey**.  

* Para localizar todos os funcionários do departamento de vendas com um ID de funcionário no intervalo **000100** ao **000199** classificadas na utilização de ordem de ID de funcionário: $filter = (PartitionKey eq ' empid_Sales") e (RowKey ge"000100") e (RowKey le "000199")  
* Para localizar todos os funcionários do departamento de vendas com um endereço de e-mail que começa com "a" classificado em utilização de ordem de endereço de e-mail: $filter = (PartitionKey eq ' email_Sales") e (RowKey ge"a") e (RowKey lt"b")  

Tenha em atenção que a sintaxe de filtro utilizada nos exemplos acima é a partir da API de REST do serviço tabela para obter mais informações, consulte [consultar entidades](https://msdn.microsoft.com/library/azure/dd179421.aspx).  

### <a name="issues-and-considerations"></a>Problemas e considerações
Na altura de decidir como implementar este padrão, considere os seguintes pontos:  

* Pode manter as suas entidades duplicadas eventualmente consistente entre si utilizando o [padrão de transações eventualmente consistente](#eventually-consistent-transactions-pattern) para manter as entidades de índice primário e secundário.  
* Armazenamento de tabelas é relativamente barato usar de modo que a sobrecarga de custos do armazenamento de dados duplicados não deve ser uma preocupação importante. No entanto, deve sempre avaliar o custo de seu design com base nos seus requisitos de armazenamento previsto e apenas adicionar duplicadas entidades para suportar as consultas que a aplicação de cliente será executado.  
* O valor utilizado para o **RowKey** tem de ser exclusivo para cada entidade. Considere a utilização de valores da chave compostas.  
* Preenchimento de valores numéricos no **RowKey** (por exemplo, o funcionário ID 000223), permite corrigir de classificação e filtragem com base no superior e os limites inferiores.  
* Não precisa necessariamente duplicar todas as propriedades de entidade. Por exemplo, se as consultas que pesquisa as entidades com a mensagem de e-mail de endereços no **RowKey** nunca precisam de idade do funcionário, estas entidades poderiam ter a seguinte estrutura:
  
   ![Entidade Employee (índice secundário)](media/storage-table-design-guide/storage-table-design-IMAGE11.png)

* É, normalmente, é melhor armazenar dados duplicados e certifique-se de que possa recuperar todos os dados que precisa com uma única consulta que usar uma consulta para localizar a entidade com o índice secundário e outro para pesquisar os dados necessários no índice primário.  

### <a name="when-to-use-this-pattern"></a>Quando utilizar este padrão
Utilize este padrão quando a aplicação cliente precisa obter entidades utilizando uma variedade de chaves diferentes, quando o cliente tem de obter entidades em diferentes ordens de classificação, e onde é possível identificar cada entidade usando uma variedade de valores exclusivos. Utilize este padrão quando deseja evitar exceder os limites de escalabilidade de partição quando estiver a efetuar pesquisas de entidade usando o diferentes **RowKey** valores.  

### <a name="related-patterns-and-guidance"></a>Padrões e orientações relacionados
Os padrões e orientações que se seguem podem também ser relevantes ao implementar este padrão:  

* [Padrão de transações eventualmente consistente](#eventually-consistent-transactions-pattern)  
* [Padrão de partição dentro de índice secundário](#intra-partition-secondary-index-pattern)  
* [Padrão de chave composta](#compound-key-pattern)  
* Transações do grupo de entidade  
* [Trabalhar com tipos de entidade heterogênea](#working-with-heterogeneous-entity-types)  

## <a name="eventually-consistent-transactions-pattern"></a>Padrão de transações eventualmente consistente
Ative o comportamento eventualmente consistente entre limites de partição ou limites de sistema de armazenamento ao utilizar as filas do Azure.  

### <a name="context-and-problem"></a>Contexto e problema
EGTs ativar transações atómicas em múltiplas entidades que partilham a mesma chave de partição. Por motivos de escalabilidade e de desempenho, pode optar por armazenar entidades que tenham requisitos de consistência em partições separadas ou num sistema de armazenamento separada: neste cenário, não é possível utilizar EGTs para manter a consistência. Por exemplo, pode ter um requisito para manter a consistência eventual entre:  

* Entidades armazenadas em duas partições diferentes na mesma tabela, nas tabelas diferentes ou em contas de armazenamento diferentes.  
* Uma entidade armazenada no serviço tabela e um blob armazenados no serviço de Blobs.  
* Uma entidade armazenada no serviço de tabela e um arquivo de um sistema de ficheiros.  
* Um arquivo de entidade no serviço tabela ainda indexado com o serviço Azure Search.  

### <a name="solution"></a>Solução
Ao utilizar as filas do Azure, pode implementar uma solução que fornece a consistência eventual entre dois ou mais partições ou sistemas de armazenamento.
Para ilustrar essa abordagem, partem do princípio de que tem um requisito para conseguir arquivar antigo entidades de funcionários. Entidades do antigo funcionário raramente são consultadas e devem ser excluídas da quaisquer atividades que lidam com funcionários atuais. Para implementar esse requisito é armazenar funcionários ativos no **atual** tabela e funcionários antigos no **arquivo** tabela. Arquivamento de um funcionário, tem de eliminar a entidade dos **atual** da tabela e adicionar a entidade para o **arquivo** tabela, mas não é possível utilizar um EGT para efetuar estas duas operações. Para evitar o risco de uma falha faz com que uma entidade a aparecer nas tabelas de ambos ou nenhum, a operação de arquivo tem de ser eventualmente consistente. O diagrama de sequência seguinte descreve os passos nesta operação. Mais detalhes é fornecido para caminhos de exceção na seguinte texto.  

![Solução de filas do Azure](media/storage-table-design-guide/storage-table-design-IMAGE12.png)

Um cliente inicia a operação de arquivamento, colocando uma mensagem numa fila do Azure, neste exemplo, para arquivar o funcionário #456. Uma função de trabalho consulta a fila para novas mensagens; Quando encontrar uma, ele lê a mensagem e mantém uma cópia oculta na fila. A função de trabalho a seguir obtém uma cópia da entidade do **atual** tabela, insere uma cópia no **arquivo** da tabela e, em seguida, elimina o original do **atual** tabela. Por fim, se não houver nenhum erro dos passos anteriores, a função de trabalho elimina a mensagem oculta da fila.  

Neste exemplo, o passo 4 insere o funcionário para o **arquivo** tabela. Ele foi possível adicionar o funcionário para um blob no serviço Blob ou um ficheiro num sistema de ficheiros.  

### <a name="recovering-from-failures"></a>Recuperar de falhas
É importante que as operações nas etapas **4** e **5** tem de ser *idempotentes* no caso da função de trabalho tem de reiniciar a operação de arquivamento. Se estiver a utilizar o serviço de tabela para o passo **4** deve utilizar uma operação de "Inserir ou substituir"; para o passo **5** deve usar um "eliminar se existe" operação na biblioteca de cliente que está a utilizar. Se estiver a utilizar outro sistema de armazenamento, tem de utilizar uma operação idempotente apropriado.  

Se a função de trabalho nunca completa a etapa **6**, em seguida, após um tempo limite a mensagem será exibida novamente na fila pronta para a função de trabalho tentar reprocessá-lo. A função de trabalho pode verificar o número de vezes que uma mensagem na fila foi ler e, se necessário, o sinalizador é uma mensagem "não processáveis" para investigação enviando-os para uma fila separada. Para obter mais informações sobre a leitura de mensagens de fila e a verificar a contagem de dequeue, consulte [obter mensagens](https://msdn.microsoft.com/library/azure/dd179474.aspx).  

Alguns erros de serviços de tabela e fila são erros transitórios, e a aplicação cliente deve incluir a lógica de repetição adequado para manipulá-las.  

### <a name="issues-and-considerations"></a>Problemas e considerações
Na altura de decidir como implementar este padrão, considere os seguintes pontos:  

* Esta solução não fornece isolamento de transação. Por exemplo, um cliente poderia ler os **atual** e **arquivo** tabelas quando a função de trabalho foi entre passos **4** e **5**e ver um exibição inconsistente dos dados. Tenha em atenção que os dados serão consistentes no final.  
* Deve ter certeza de que os passos 4 e 5 são idempotentes para garantir a consistência eventual.  
* Pode dimensionar a solução ao utilizar várias filas e instâncias de função de trabalho.  

### <a name="when-to-use-this-pattern"></a>Quando utilizar este padrão
Utilize este padrão quando deseja garantir a consistência eventual entre entidades que existem em partições diferentes ou tabelas. Pode estender esse padrão para assegurar a consistência eventual para operações entre o serviço de tabela e o serviço de BLOBs e outros armazenamento do Azure origens de dados, como a base de dados ou o sistema de ficheiros.  

### <a name="related-patterns-and-guidance"></a>Padrões e orientações relacionados
Os padrões e orientações que se seguem podem também ser relevantes ao implementar este padrão:  

* Transações do grupo de entidade  
* [Intercalação ou substituição](#merge-or-replace)  

> [!NOTE]
> Se o isolamento de transação é importante para a sua solução, deve considerar a ter de redesenhar a sua tabelas para permitir que use EGTs.  
> 
> 

## <a name="index-entities-pattern"></a>Padrão de entidades de índice
Manter entidades de índice para permitir pesquisas eficientes que retornam as listas de entidades.  

### <a name="context-and-problem"></a>Contexto e problema
O serviço de tabela indexa automaticamente as entidades com o **PartitionKey** e **RowKey** valores. Isto permite que um aplicativo de cliente para recuperar uma entidade com eficiência usando uma consulta de ponto. Por exemplo, usando a estrutura da tabela abaixo, uma aplicação cliente pode com eficiência obter uma entidade employee individuais utilizando o nome do departamento e a identificação do funcionário (a **PartitionKey** e **RowKey**).  

![Entidade Employee](media/storage-table-design-guide/storage-table-design-IMAGE13.png)

Se pretender ser capaz de obter uma lista de entidades de funcionários com base no valor de outra propriedade exclusivos, como o sobrenome, também tem de utilizar uma análise de partição menos eficiente para localizar correspondências, em vez de usar um índice examiná-los diretamente. Isto acontece porque o serviço de tabela não fornece índices secundários.  

### <a name="solution"></a>Solução
Para ativar a pesquisa por apelido com a estrutura de entidade mostrada acima, tem de manter listas de ids de funcionários. Se quiser obter as entidades de funcionários com um nome de última específico, como Jones, tem de primeiro localizar a lista de ids de funcionário para funcionários com Jones como sobrenome e, em seguida, obter essas entidades de funcionários. Existem três opções principais para armazenar as listas de ids de funcionário:  

* Utilize o armazenamento de Blobs.  
* Crie entidades de índice na mesma partição que as entidades de funcionários.  
* Crie entidades de índice numa partição separada ou a tabela.  

<u>Opção #1: Utilizar o armazenamento de BLOBs</u>  

Para a primeira opção, crie um blob para cada nome de última exclusivo e, em cada armazenamento de BLOBs uma lista do **PartitionKey** (department) e **RowKey** (identificação do funcionário) valores para os funcionários que tenham esse nome passado. Quando adicionar ou eliminar um funcionário deve garantir que o conteúdo do blob relevante é eventualmente consistente com as entidades de funcionários.  

<u>Opção #2:</u> Criar entidades de índice na mesma partição  

Para a segunda opção, utilize entidades de índice que armazenam os dados seguintes:  

![Entidade de índice de funcionário](media/storage-table-design-guide/storage-table-design-IMAGE14.png)

O **EmployeeIDs** propriedade contém uma lista de ids de funcionário para os funcionários com o nome da última armazenado no **RowKey**.  

Os passos seguintes descrevem o processo que deve seguir quando estiver a adicionar um novo funcionário se estiver a utilizar a segunda opção. Neste exemplo, estamos a adicionar um funcionário com 000152 de Id e um apelido Jones no departamento de vendas:  

1. Obter a entidade de índice com um **PartitionKey** valor "Sales" e o **RowKey** valor "Jones." Guarde a ETag dessa entidade para utilizar no passo 2.  
2. Crie uma transação de entidade de grupo (ou seja, uma operação em lote) que insere a nova entidade de funcionário (**PartitionKey** valor "Sales" e **RowKey** valor "000152") e atualiza a entidade de índice (**PartitionKey** valor de "Sales" e **RowKey** valor "Jones") ao adicionar o novo ID de funcionário à lista no campo EmployeeIDs. Para obter mais informações sobre transações de grupo de entidades, consulte transações do grupo de entidades.  
3. Se a transação do grupo de entidade falhar devido a um erro de simultaneidade otimista (alguém apenas modificou a entidade de índice), em seguida, terá de recomeçar novamente no passo 1.  

Pode usar uma abordagem semelhante para eliminar um funcionário, se estiver a utilizar a segunda opção. Alterar o sobrenome de um funcionário é um pouco mais complexo porque vai precisar de executar uma transação de grupo de entidade que atualiza as três entidades: entidade employee, a entidade de índice para o nome antigo do último e a entidade de índice para o novo apelido. Tem de obter cada entidade antes de fazer alterações para poder recuperar os valores de ETag que, em seguida, pode utilizar para efetuar as atualizações usando a simultaneidade otimista.  

Os passos seguintes descrevem o processo que deve seguir quando precisar de pesquisar todos os funcionários com um determinado nome de último num departamento, se estiver a utilizar a segunda opção. Neste exemplo, estamos indo dormir todos os funcionários com o apelido Jones no departamento de vendas:  

1. Obter a entidade de índice com um **PartitionKey** valor "Sales" e o **RowKey** valor "Jones."  
2. Analisar a lista de Ids no campo EmployeeIDs do funcionário.  
3. Se precisar de informações adicionais sobre cada uma destes funcionários (por exemplo, os endereços de e-mail), obter cada uma dessas entidades employee usando **PartitionKey** valor "Sales" e **RowKey** valores do lista de funcionários que obteve no passo 2.  

<u>Opção #3:</u> Criar entidades de índice numa partição separada ou de uma tabela  

Para a terceira opção, utilize entidades de índice que armazenam os dados seguintes:  

![Entidade de índice de Employee numa partição separada](media/storage-table-design-guide/storage-table-design-IMAGE15.png)


O **EmployeeIDs** propriedade contém uma lista de ids de funcionário para os funcionários com o nome da última armazenado no **RowKey**.  

Com a terceira opção, não é possível utilizar EGTs para manter a consistência, porque as entidades de índice são numa partição separada das entidades de funcionários. Deve garantir que as entidades de índice são eventualmente consistentes com as entidades de funcionários.  

### <a name="issues-and-considerations"></a>Problemas e considerações
Na altura de decidir como implementar este padrão, considere os seguintes pontos:  

* Esta solução requer, pelo menos, duas consultas para recuperar entidades correspondentes: um para consultar entidades de índice para obter a lista de **RowKey** valores e, em seguida, obter cada entidade na lista de consultas.  
* Uma vez que uma entidade individual tem um tamanho máximo de 1 MB, a opção #2 e a opção #3 na solução partem do princípio de que a lista de ids de funcionário para qualquer determinado apelido nunca é superior a 1 MB. Se a lista de ids de funcionário é suscetível de ser maior que 1 MB de tamanho, utilize a opção #1 e armazenar os dados de índice no armazenamento de Blobs.  
* Se utilizar a opção #2 (usando EGTs para lidar com adicionar e eliminar os funcionários e alterar o sobrenome de um funcionário) tem de avaliar se o volume de transações abordará os limites de escalabilidade numa determinada partição. Se for este o caso, deve considerar uma solução eventualmente consistente (opção #1 ou a opção #3) que utiliza as filas para processar os pedidos de atualização e permite-lhe armazenar suas entidades de índice numa partição separada das entidades de funcionários.  
* Opção #2 nesta solução parte do princípio de que deseja procurar por apelido dentro de um departamento: por exemplo, deseja recuperar uma lista de funcionários com um apelido Jones no departamento de vendas. Se quiser ser capaz de procurar todos os funcionários com um apelido Jones em toda a organização, utilize a opção #1 ou opção #3.
* Pode implementar uma solução de baseada na fila de mensagens em fila que fornece a consistência eventual (veja a [padrão de transações eventualmente consistente](#eventually-consistent-transactions-pattern) para obter mais detalhes).  

### <a name="when-to-use-this-pattern"></a>Quando utilizar este padrão
Utilize este padrão quando deseja pesquisar um conjunto de entidades que todas partilham um valor de propriedade comuns, como todos os funcionários com o nome da última Jones.  

### <a name="related-patterns-and-guidance"></a>Padrões e orientações relacionados
Os padrões e orientações que se seguem podem também ser relevantes ao implementar este padrão:  

* [Padrão de chave composta](#compound-key-pattern)  
* [Padrão de transações eventualmente consistente](#eventually-consistent-transactions-pattern)  
* Transações do grupo de entidade  
* [Trabalhar com tipos de entidade heterogênea](#working-with-heterogeneous-entity-types)  

## <a name="denormalization-pattern"></a>Padrão de desnormalização
Combine dados relacionados em conjunto numa única entidade que lhe permite obter todos os dados que precisa com uma ponto único de consulta.  

### <a name="context-and-problem"></a>Contexto e problema
Numa base de dados relacional, normalmente, normalizar dados para remover a duplicação, resultando em consultas que obtêm dados de várias tabelas. Se normalizar os dados nas tabelas do Azure, tem de certificar vários ida e volta do cliente para o servidor para obter os dados relacionados. Por exemplo, com a estrutura da tabela mostrada abaixo, tem duas ida e volta ao obter os detalhes de um departamento: um para obter a entidade de departamento que incluem o Gestor do ID e, em seguida, outra solicitação para obter os detalhes do gestor numa entidade employee.  

![Entidade de departamento e entidade Employee](media/storage-table-design-guide/storage-table-design-IMAGE16.png)

### <a name="solution"></a>Solução
Em vez de armazenar os dados em duas entidades separadas, desnormalizar os dados e manter uma cópia dos detalhes do gerente na entidade do departamento. Por exemplo:  

![Entidade de departamento](media/storage-table-design-guide/storage-table-design-IMAGE17.png)

Com as entidades de departamento armazenadas com estas propriedades, agora pode recuperar todos os detalhes que precisa sobre um departamento usando uma consulta de ponto.  

### <a name="issues-and-considerations"></a>Problemas e considerações
Na altura de decidir como implementar este padrão, considere os seguintes pontos:  

* Existe algum custo de sobrecarga associado ao armazenamento alguns dados duas vezes. O benefício de desempenho (resultantes de menos de pedidos para o serviço de armazenamento) normalmente vale mais que o marginal aumento nos custos de armazenamento (e este custo parcialmente é contrabalançado por uma redução no número de transações que necessita para obter os detalhes de um departamento ).  
* Tem de manter a consistência das duas entidades que armazenam informações sobre os gestores. Pode manipular o problema de consistência utilizando EGTs para atualizar várias entidades numa transação atômica única: neste caso, a entidade de departamento e a entidade employee para o Gestor de departamento são armazenadas na mesma partição.  

### <a name="when-to-use-this-pattern"></a>Quando utilizar este padrão
Utilize este padrão quando precisa com frequência procurar informações relacionadas. Este padrão reduz o número de consultas de que seu cliente tem de efetuar para recuperar os dados que ela requer.  

### <a name="related-patterns-and-guidance"></a>Padrões e orientações relacionados
Os padrões e orientações que se seguem podem também ser relevantes ao implementar este padrão:  

* [Padrão de chave composta](#compound-key-pattern)  
* Transações do grupo de entidade  
* [Trabalhar com tipos de entidade heterogênea](#working-with-heterogeneous-entity-types)

## <a name="compound-key-pattern"></a>Padrão de chave composta
Utilize compostas **RowKey** valores para permitir que um cliente pesquisar dados relacionados com um ponto único de consulta.  

### <a name="context-and-problem"></a>Contexto e problema
Numa base de dados relacional, é muito natural usar junções em consultas para retornar partes relacionadas de dados para o cliente numa única consulta. Por exemplo, poderá utilizar a identificação do funcionário para procurar uma lista de entidades relacionadas que contêm o desempenho e rever os dados para aquele funcionário.  

Suponha que estão a armazenar entidades de funcionários no serviço tabela usando a seguinte estrutura:  

![Estrutura de entidade de funcionário](media/storage-table-design-guide/storage-table-design-IMAGE18.png)

Também precisa de armazenar dados históricos relacionadas com as revisões e desempenho para cada ano, que o funcionário trabalhou para a sua organização e tem de ser capaz de aceder a estas informações por ano. Uma opção é criar outra tabela que armazena as entidades com a seguinte estrutura:  

![Estrutura de entidade employee alternativo](media/storage-table-design-guide/storage-table-design-IMAGE19.png)

Tenha em atenção que esta abordagem poderá optar por duplicar algumas informações (como o nome próprio e apelido) na nova entidade que lhe permite obter os seus dados com um único pedido. No entanto, não é possível manter a consistência forte porque não é possível utilizar um EGT para atualizar as duas entidades atomicamente.  

### <a name="solution"></a>Solução
Store um novo tipo de entidade na sua tabela original usando entidades com a seguinte estrutura:  

![Solução para a estrutura de entidade de funcionário](media/storage-table-design-guide/storage-table-design-IMAGE20.png)

Observe como o **RowKey** agora é uma chave composta constituída pela identificação do funcionário e o ano dos dados de revisão que permite-lhe obter o desempenho do funcionário e consultar dados com uma única solicitação para uma única entidade.  

O exemplo a seguir descreve como recuperar todos os dados de revisão de um funcionário específico (por exemplo, o funcionário 000123 no departamento de vendas):  

$filter = (PartitionKey eq "Vendas") e (RowKey ge "empid_000123") e (RowKey lt 'empid_000124') & $select = RowKey, Gestor de classificação, a classificação de ponto a ponto, comentários  

### <a name="issues-and-considerations"></a>Problemas e considerações
Na altura de decidir como implementar este padrão, considere os seguintes pontos:  

* Deve utilizar um caráter de separação adequado que torna mais fácil de analisar os **RowKey** valor: por exemplo, **000123_2012**.  
* Também estão a armazenar esta entidade na mesma partição como outras entidades que contêm dados relacionados para o mesmo funcionário, o que significa que pode usar EGTs para manter a consistência forte.
* Deve considerar a frequência com que irá consultar os dados para determinar se este padrão é adequado.  Por exemplo, se acessará os dados de revisão com pouca frequência e os dados de funcionários principal, muitas vezes, deve evitar que elas entidades como separadas.  

### <a name="when-to-use-this-pattern"></a>Quando utilizar este padrão
Utilize este padrão quando tiver de armazenar um ou mais entidades relacionadas que consulta frequentemente.  

### <a name="related-patterns-and-guidance"></a>Padrões e orientações relacionados
Os padrões e orientações que se seguem podem também ser relevantes ao implementar este padrão:  

* Transações do grupo de entidade  
* [Trabalhar com tipos de entidade heterogênea](#working-with-heterogeneous-entity-types)  
* [Padrão de transações eventualmente consistente](#eventually-consistent-transactions-pattern)  

## <a name="log-tail-pattern"></a>Padrão de cauda do registo
Obter o *n* entidades mais recentemente adicionadas a uma partição ao utilizar um **RowKey** valor ordena inversa de data e a ordem de tempo.  

### <a name="context-and-problem"></a>Contexto e problema
Um requisito comum é a conseguir obter as entidades recentemente criadas, por exemplo o mais recente dez afirmações enviadas por um funcionário de despesas. Suporte de consulta de tabela uma **$top** consultar operação para retornar o primeiro *n* entidades a partir de um conjunto: não existe nenhuma operação equivalente de consulta para devolver as último entidades n num conjunto.  

### <a name="solution"></a>Solução
Store as entidades com um **RowKey** que naturalmente Ordena por ordem inversa de data/hora, utilizando a entrada por isso, a mais recente é sempre o primeiro na tabela.  

Por exemplo, para poder recuperar as dez declarações de despesas mais recentes enviadas por um funcionário, pode utilizar um valor de escala inversa derivado de data/hora atual. O seguinte exemplo de código do c# mostra uma forma de criar um valor de "invertido ticks" adequado para um **RowKey** que classifica da mais recente para o mais antigo:  

`string invertedTicks = string.Format("{0:D19}", DateTime.MaxValue.Ticks - DateTime.UtcNow.Ticks);`  

Pode voltar para o valor de hora de data usando o seguinte código:  

`DateTime dt = new DateTime(DateTime.MaxValue.Ticks - Int64.Parse(invertedTicks));`  

A consulta de tabela tem o seguinte aspeto:  

`https://myaccount.table.core.windows.net/EmployeeExpense(PartitionKey='empid')?$top=10`  

### <a name="issues-and-considerations"></a>Problemas e considerações
Na altura de decidir como implementar este padrão, considere os seguintes pontos:  

* Tem de preencher o valor de escala inversa aos principais zeros para garantir que o valor de cadeia de caracteres ordena conforme esperado.  
* Deve estar ciente dos destinos de escalabilidade no nível de uma partição. Tenha cuidado não criar partições de ponto de acesso.  

### <a name="when-to-use-this-pattern"></a>Quando utilizar este padrão
Utilize este padrão quando precisa acessar as entidades na ordem inversa de data/hora ou quando precisa acessar as entidades adicionadas recentemente.  

### <a name="related-patterns-and-guidance"></a>Padrões e orientações relacionados
Os padrões e orientações que se seguem podem também ser relevantes ao implementar este padrão:  

* [Antipadrão de preceder /append](#prepend-append-anti-pattern)  
* [Obter entidades](#retrieving-entities)  

## <a name="high-volume-delete-pattern"></a>Padrão de exclusão de volume elevado
Ativar a eliminação de um grande volume de entidades ao armazenar todas as entidades para eliminação simultânea em sua própria tabela separada; eliminar entidades ao eliminar a tabela.  

### <a name="context-and-problem"></a>Contexto e problema
Muitos aplicativos eliminar dados antigos que já não tem de estar disponíveis para uma aplicação cliente ou que o aplicativo tem arquivados para outro meio de armazenamento. Normalmente identificar esses dados por uma data: por exemplo, tem um requisito para eliminar registos de todos os pedidos de início de sessão que têm mais de 60 dias.  

Um design possíveis é usar a data e hora do pedido no início de sessão do **RowKey**:  

![Data e hora da tentativa de início de sessão](media/storage-table-design-guide/storage-table-design-IMAGE21.png)

Esta abordagem evita a pontos ativos de partição porque o aplicativo pode inserir e eliminar entidades de início de sessão para cada utilizador numa partição separada. No entanto, essa abordagem pode ser dispendioso e moroso se tiver um grande número de entidades, uma vez que primeiro precisa executar uma análise de tabela para identificar todas as entidades para eliminar e, em seguida, tem de eliminar cada entidade antiga. Tenha em atenção que pode reduzir o número de ida e volta para o servidor necessário para eliminar as entidades antigas através da criação de batches vários pedidos delete para EGTs.  

### <a name="solution"></a>Solução
Utilize uma tabela separada para cada dia de tentativas de início de sessão. Pode usar a estrutura de entidade acima para evitar pontos ativos durante a inserção de entidades, e excluindo entidades antigas agora é simplesmente uma questão de eliminação de todos os dias de uma tabela (uma operação de armazenamento única), em vez de localizar e eliminar centenas e milhares de pessoa entidades de início de sessão por dia.  

### <a name="issues-and-considerations"></a>Problemas e considerações
Na altura de decidir como implementar este padrão, considere os seguintes pontos:  

* O seu design suporta outras formas do aplicativo usará os dados, como a pesquisa de entidades específicas, com outros dados, ou gerar agregam informações de ligação?  
* O design evitar pontos ativos ao são inserir novas entidades?  
* Espere um atraso se quiser reutilizar o mesmo nome de tabela depois excluí-lo. É melhor sempre usar nomes de tabela exclusivo.  
* Espere alguns limitação quando usar primeiro uma nova tabela, enquanto o serviço de tabela aprende os padrões de acesso e distribui as partições entre nós. Deve considerar a frequência tem de criar novas tabelas.  

### <a name="when-to-use-this-pattern"></a>Quando utilizar este padrão
Utilize este padrão quando tem um grande volume de entidades que tem de eliminar ao mesmo tempo.  

### <a name="related-patterns-and-guidance"></a>Padrões e orientações relacionados
Os padrões e orientações que se seguem podem também ser relevantes ao implementar este padrão:  

* Transações do grupo de entidade
* [Modificar entidades](#modifying-entities)  

## <a name="data-series-pattern"></a>Padrão de série de dados
Série de dados completos de Store numa única entidade para minimizar o número de pedidos que fizer.  

### <a name="context-and-problem"></a>Contexto e problema
Um cenário comum é para uma aplicação armazenar uma série de dados que, normalmente, precisar de obter ao mesmo tempo. Por exemplo, seu aplicativo pode gravar o número de mensagens Instantâneas de mensagens, cada funcionário envia a cada hora e, em seguida, utilize estas informações para desenhar quantas mensagens cada usuário enviado através das 24 horas anteriores. Um design poderá estar a armazenar 24 entidades de cada funcionário:  

![Entidades de Store 24 de cada funcionário](media/storage-table-design-guide/storage-table-design-IMAGE22.png)

Com esta estrutura, pode facilmente localizar e atualizar a entidade a atualizar para cada funcionário, sempre que a aplicação tem de atualizar o valor de contagem de mensagens. No entanto, para obter as informações para desenhar um gráfico da atividade para as anteriores 24 horas, tem de obter 24 entidades.  

### <a name="solution"></a>Solução
Utilize a seguinte estrutura com uma propriedade separada para armazenar a contagem de mensagens para cada hora:  

![Entidade de estatísticas de mensagem](media/storage-table-design-guide/storage-table-design-IMAGE23.png)

Com esta estrutura, pode usar uma operação de intercalação para atualizar a contagem de mensagens para um funcionário durante uma hora específica. Agora, pode recuperar todas as informações necessárias desenhar o gráfico através de um pedido para uma única entidade.  

### <a name="issues-and-considerations"></a>Problemas e considerações
Na altura de decidir como implementar este padrão, considere os seguintes pontos:  

* Se sua série completa de dados não se encaixam numa única entidade (uma entidade pode ter até 252 propriedades), utilize um arquivo de dados alternativo, como um blob.  
* Se tiver vários clientes, atualizar uma entidade em simultâneo, terá de utilizar o **ETag** para implementar a simultaneidade otimista. Se tiver muitos clientes, pode provocar a contenção elevada.  

### <a name="when-to-use-this-pattern"></a>Quando utilizar este padrão
Utilize este padrão quando precisa atualizar e obter uma série de dados associada a uma entidade individual.  

### <a name="related-patterns-and-guidance"></a>Padrões e orientações relacionados
Os padrões e orientações que se seguem podem também ser relevantes ao implementar este padrão:  

* [Padrão de entidades grandes](#large-entities-pattern)  
* [Intercalação ou substituição](#merge-or-replace)  
* [Padrão de transações eventualmente consistente](#eventually-consistent-transactions-pattern) (se estiver armazenando a série de dados num blob)  

## <a name="wide-entities-pattern"></a>Padrão de entidades grande
Utilize várias entidades de físicas para armazenar entidades lógicas com mais de 252 propriedades.  

### <a name="context-and-problem"></a>Contexto e problema
Uma entidade individual pode ter mais do que 252 propriedades (excluindo as propriedades do sistema obrigatórios) e não é possível armazenar mais de 1 MB de dados no total. Numa base de dados relacional, normalmente obteria arredondar limites sobre o tamanho de uma linha ao adicionar uma nova tabela e a imposição de uma relação de 1 para 1 entre elas.  

### <a name="solution"></a>Solução
Com o serviço tabela, pode armazenar várias entidades para representar um objeto de empresas de grande única com mais de 252 propriedades. Por exemplo, se quiser armazenar uma contagem do número de mensagens Instantâneas enviados por cada funcionário para os últimos 365 dias, poderia usar a estrutura seguinte, que utiliza duas entidades com esquemas diferentes:  

![Várias entidades](media/storage-table-design-guide/storage-table-design-IMAGE24.png)

Se precisar de fazer uma alteração que requer a atualização de ambas as entidades para mantê-las sincronizadas entre si pode utilizar um EGT. Caso contrário, pode utilizar uma operação de intercalação único para atualizar a contagem de mensagens para um dia específico. Para obter todos os dados para um funcionário tem de obter as duas entidades, que pode ser feito com duas solicitações eficientes que utilizam ambos um **PartitionKey** e uma **RowKey** valor.  

### <a name="issues-and-considerations"></a>Problemas e considerações
Na altura de decidir como implementar este padrão, considere os seguintes pontos:  

* Obter uma entidade lógica concluída envolve a, pelo menos, duas transações de armazenamento: um para recuperar cada entidade física.  

### <a name="when-to-use-this-pattern"></a>Quando utilizar este padrão
Utilize este padrão quando precisa armazenar entidades cujo tamanho ou número de propriedades exceder os limites para uma entidade individual no serviço tabela.  

### <a name="related-patterns-and-guidance"></a>Padrões e orientações relacionados
Os padrões e orientações que se seguem podem também ser relevantes ao implementar este padrão:  

* Transações do grupo de entidade
* [Intercalação ou substituição](#merge-or-replace)

## <a name="large-entities-pattern"></a>Padrão de entidades grandes
Utilize o armazenamento de BLOBs para armazenar valores de propriedade grandes.  

### <a name="context-and-problem"></a>Contexto e problema
Uma entidade individual não é possível armazenar mais de 1 MB de dados no total. Se uma ou várias das suas propriedades armazenam valores que fazer com que o tamanho total da sua entidade exceder este valor, não é possível armazenar a entidade inteira no serviço tabela.  

### <a name="solution"></a>Solução
Se a sua entidade exceder 1 MB de tamanho, porque uma ou mais propriedades contêm uma grande quantidade de dados, pode armazenar dados no serviço de BLOBs e, em seguida, armazenar o endereço do blob numa propriedade na entidade. Por exemplo, pode armazenar a fotografia de um empregado no armazenamento de BLOBs e armazenar uma ligação para a foto na **fotos** propriedade da sua entidade employee:  

![Propriedade de fotos](media/storage-table-design-guide/storage-table-design-IMAGE25.png)

### <a name="issues-and-considerations"></a>Problemas e considerações
Na altura de decidir como implementar este padrão, considere os seguintes pontos:  

* Para manter a consistência eventual entre a entidade no serviço tabela e os dados no serviço Blob, utilize o [padrão de transações eventualmente consistente](#eventually-consistent-transactions-pattern) para manter suas entidades.
* Obter uma entidade completa envolve a, pelo menos, duas transações de armazenamento: um para recuperar a entidade e outra para recuperar os dados de Blobs.  

### <a name="when-to-use-this-pattern"></a>Quando utilizar este padrão
Utilize este padrão quando precisar armazenar entidades cujo tamanho exceda os limites para uma entidade individual no serviço tabela.  

### <a name="related-patterns-and-guidance"></a>Padrões e orientações relacionados
Os padrões e orientações que se seguem podem também ser relevantes ao implementar este padrão:  

* [Padrão de transações eventualmente consistente](#eventually-consistent-transactions-pattern)  
* [Padrão de entidades grande](#wide-entities-pattern)

<a name="prepend-append-anti-pattern"></a>

## <a name="prependappend-anti-pattern"></a>Antipadrão preceder/acrescentar
Aumente a escalabilidade quando tiver um grande volume de inserções ao propagar as inserções em várias partições.  

### <a name="context-and-problem"></a>Contexto e problema
Prefixação ou acrescentando entidades para as suas entidades armazenadas normalmente resulta na aplicação a adicionar novas entidades na partição do primeiro ou último de uma sequência de partições. Neste caso, todas as inserções em qualquer momento estão a ocorrer na mesma partição, a criação de um ponto de acesso que impede que o serviço de tabela do balanceamento de carga inserções em vários nós e, possivelmente, fazendo com que seu aplicativo atingir as metas de escalabilidade para partição. Por exemplo, se tiver uma aplicação que aceder a recursos e de rede de registos por funcionários, em seguida, uma estrutura de entidades, como mostrado a seguir pode resultar na partição a hora atual tornar-se de um ponto de acesso se o volume de transações de atingir o destino de escalabilidade para um partição individual:  

![Estrutura de entidade](media/storage-table-design-guide/storage-table-design-IMAGE26.png)

### <a name="solution"></a>Solução
A seguinte estrutura de entidade alternativa evita um ponto de acesso em qualquer partição específica, como os eventos de registos de aplicações:  

![Estrutura de entidade alternativo](media/storage-table-design-guide/storage-table-design-IMAGE27.png)

Observe que com este exemplo de como os dois o **PartitionKey** e **RowKey** são as chaves compostas. O **PartitionKey** utiliza o ID do departamento e o funcionário a distribuir o registo por várias partições.  

### <a name="issues-and-considerations"></a>Problemas e considerações
Na altura de decidir como implementar este padrão, considere os seguintes pontos:  

* A estrutura de chave alternativa evita a criação de partições muito ativas em inserções com eficiência suporta as consultas que a aplicação de cliente faz?  
* O volume antecipado de transações significa que é provável que a atingir as metas de escalabilidade para uma partição individual e limitados pelo serviço de armazenamento?  

### <a name="when-to-use-this-pattern"></a>Quando utilizar este padrão
Evite o antipadrão prepend/acrescentar quando o volume de transações é provável que resultará na limitação, o serviço de armazenamento ao aceder a uma partição de acesso frequente.  

### <a name="related-patterns-and-guidance"></a>Padrões e orientações relacionados
Os padrões e orientações que se seguem podem também ser relevantes ao implementar este padrão:  

* [Padrão de chave composta](#compound-key-pattern)  
* [Padrão de cauda do registo](#log-tail-pattern)  
* [Modificar entidades](#modifying-entities)  

## <a name="log-data-anti-pattern"></a>Padrão de anti de dados de registo
Normalmente, deve utilizar o serviço de BLOBs, em vez do serviço de tabela para armazenar dados de registo.  

### <a name="context-and-problem"></a>Contexto e problema
Caso de utilização de uma comum para dados de registo são obter uma seleção de entradas de registo de um intervalo específico de data/hora: por exemplo, pretender localizar todos os erros e mensagens críticas que seu aplicativo registado entre 15:04 e 15:06 numa data específica. Não pretende utilizar a data e hora da mensagem de registo para determinar a partição de entidades de registo para o guardou: que resulta numa partição de acesso frequente, porque a qualquer momento, todas as entidades de registo irão partilhar o mesmo **PartitionKey** valor (consulte a seção [antipadrão Prepend/acrescentar](#prepend-append-anti-pattern)). Por exemplo, o seguinte esquema de entidade para uma mensagem de registo resulta numa partição de acesso frequente como o aplicativo grava todas as mensagens de registo para a partição para a data atual e a hora:  

![Entidade de mensagem do registo](media/storage-table-design-guide/storage-table-design-IMAGE28.png)

Neste exemplo, o **RowKey** inclui a data e hora da mensagem de registo para garantir que as mensagens de registo são armazenadas, ordenados por ordem de data/hora e inclui um ID de mensagem no caso de várias mensagens de registo partilham a mesma data e hora.  

Outra abordagem é usar um **PartitionKey** que garante que a aplicação escreve mensagens numa variedade de partições. Por exemplo, se a origem da mensagem de registo fornece uma forma de distribuir as mensagens por várias partições, pode utilizar o esquema de entidade seguintes:  

![Entidade de mensagem do registo alternativo](media/storage-table-design-guide/storage-table-design-IMAGE29.png)

No entanto, o problema com este esquema é que, para recuperar todas as mensagens de registo para um intervalo de tempo específico precisar procurar cada partição na tabela.

### <a name="solution"></a>Solução
A secção anterior destacam-se o problema de tentar utilizar o serviço de tabela para armazenar as entradas de registo e dois sugeridas, sempre insatisfatória, designs. Uma solução que levou a uma partição frequente com o risco de fraco desempenho escrever as mensagens do registo; a outra solução resultou no desempenho da consulta fraco devido ao requisito analise cada partição da tabela para obter mensagens de registo para um intervalo de tempo específico. Armazenamento de BLOBs oferece uma solução melhor para este tipo de cenário e é os arquivos de análise de armazenamento como o Azure os dados de registo que coleta.  

Esta seção descreve como a análise de armazenamento armazena os dados de registo no armazenamento de BLOBs para ilustrar essa abordagem para armazenar dados que normalmente consulta por intervalo.  

Análise de armazenamento armazena as mensagens do registo num formato delimitado em vários blobs. O formato delimitado torna mais fácil para um aplicativo de cliente analisar os dados na mensagem de registo.  

Análise de armazenamento utiliza uma convenção de nomenclatura de blobs que permite localizar o blob (ou os blobs) que contêm as mensagens de registo para o qual está a procurar. Por exemplo, um blob com o nome "queue/2014/07/31/1800/000001.log" contém mensagens de registo relacionados com o serviço de fila para a hora, começando às 18:00 em 31 de Julho de 2014. "000001" indica que este é o primeiro ficheiro de registo para este período. Análise de armazenamento também regista os carimbos de data / das mensagens de log de primeiro e último armazenadas no ficheiro como parte dos metadados do blob. A API para ativa de armazenamento de BLOBs localizar os blobs num contentor com base num prefixo de nome: para localizar todos os blobs que contêm dados de registo de fila para a hora, começando às 18:00, pode usar o prefixo "fila/2014/07/31/1800".  

Memórias intermédias de análise de armazenamento registar mensagens internamente e, em seguida, periodicamente atualiza o blob apropriado ou cria um novo com o lote mais recente das entradas de log. Isso reduz o número de gravações que ele deve ser executado para o serviço de Blobs.  

Se estiver a implementar uma solução semelhante em seu próprio aplicativo, deve considerar como gerir o equilíbrio entre a fiabilidade (escrever cada entrada de registo para o armazenamento de BLOBs, por acaso) e custo e a escalabilidade (na memória intermédia atualizações na sua aplicação e a gravação -los para o blob storage em lotes).  

### <a name="issues-and-considerations"></a>Problemas e considerações
Considere os seguintes pontos ao decidir como armazenar dados de registo:  

* Se criar um design de tabela que evita potenciais partições muito ativas, pode achar que não é possível aceder aos dados de registo com eficiência.  
* Para processar dados de registo, um cliente, muitas vezes, precisa de carregar o número de registos.  
* Embora muitas vezes são estruturados de dados de registo, o armazenamento de BLOBs pode ser uma solução melhor.  

## <a name="implementation-considerations"></a>Considerações de implementação
Esta secção descreve algumas das considerações a ter em mente ao implementar os padrões descritos nas secções anteriores. Grande parte dessa seção utiliza exemplos escritos em c# que utilizam a biblioteca de cliente de armazenamento (versão versão 4.3.0 no momento da escrita).  

## <a name="retrieving-entities"></a>Obter entidades
Como discutido na seção de Design para a consulta, a consulta mais eficiente é uma consulta de ponto. No entanto, em alguns cenários poderá ter de obter várias entidades. Esta secção descreve algumas abordagens comuns para a recuperação de entidades usando a biblioteca de cliente de armazenamento.  

### <a name="executing-a-point-query-using-the-storage-client-library"></a>Execução de uma consulta de ponto usando a biblioteca de cliente de armazenamento
A maneira mais fácil de executar uma consulta de ponto é utilizar o **obter** operação de tabela, conforme mostrado na seguinte c# trecho de código que obtém uma entidade com um **PartitionKey** de valor de "Sales" e um  **RowKey** do valor "212":  

```csharp
TableOperation retrieveOperation = TableOperation.Retrieve<EmployeeEntity>("Sales", "212");
var retrieveResult = employeeTable.Execute(retrieveOperation);
if (retrieveResult.Result != null)
{
    EmployeeEntity employee = (EmployeeEntity)retrieveResult.Result;
    ...
}  
```

Observe como neste exemplo espera que a entidade obtém seja do tipo **EmployeeEntity**.  

### <a name="retrieving-multiple-entities-using-linq"></a>A obter várias entidades usando o LINQ
Pode recuperar várias entidades usando o LINQ com biblioteca de clientes de armazenamento e especificando uma consulta com um **onde** cláusula. Para evitar uma análise de tabela, deve incluir sempre o **PartitionKey** valor na where cláusula e se for possível os **RowKey** valor para evitar verificações de tabela e partição. O serviço de tabela suporta um conjunto limitado de operadores de comparação (maior que, maior que ou iguais, menos a, menos do que ou iguais, iguais e não é iguais a) para utilizar no where cláusula. O seguinte trecho de código do c# localiza todos os funcionários cuja último começa de nome com "B" (supondo que o **RowKey** armazena o sobrenome) do departamento de vendas (supondo que o **PartitionKey** armazena o nome do departamento):  

```csharp
TableQuery<EmployeeEntity> employeeQuery = employeeTable.CreateQuery<EmployeeEntity>();
var query = (from employee in employeeQuery
            where employee.PartitionKey == "Sales" &&
            employee.RowKey.CompareTo("B") >= 0 &&
            employee.RowKey.CompareTo("C") < 0
            select employee).AsTableQuery();
var employees = query.Execute();  
```

Observe como a consulta Especifica tanto uma **RowKey** e uma **PartitionKey** para garantir um melhor desempenho.  

O exemplo de código seguinte mostra as funcionalidade equivalente usando a API Fluente (para obter mais informações sobre fluentes APIs em geral, consulte [melhores práticas para criar uma API Fluent](https://visualstudiomagazine.com/articles/2013/12/01/best-practices-for-designing-a-fluent-api.aspx)):  

```csharp
TableQuery<EmployeeEntity> employeeQuery = new TableQuery<EmployeeEntity>().Where(
    TableQuery.CombineFilters(
    TableQuery.CombineFilters(
        TableQuery.GenerateFilterCondition(
    "PartitionKey", QueryComparisons.Equal, "Sales"),
    TableOperators.And,
    TableQuery.GenerateFilterCondition(
    "RowKey", QueryComparisons.GreaterThanOrEqual, "B")
),
TableOperators.And,
TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.LessThan, "C")
    )
);
var employees = employeeTable.ExecuteQuery(employeeQuery);  
```

> [!NOTE]
> O exemplo aninha várias **CombineFilters** métodos para incluir as três condições de filtro.  
> 
> 

### <a name="retrieving-large-numbers-of-entities-from-a-query"></a>A obter um grande número de entidades de uma consulta
Uma consulta ideal retorna uma entidade individual com base numa **PartitionKey** valor e um **RowKey** valor. No entanto, em alguns cenários pode ter um requisito para retornar muitas entidades, da mesma partição ou mesmo a partir de várias partições.  

Sempre totalmente, deve testar o desempenho da sua aplicação em tais cenários.  

Uma consulta contra o serviço de tabela pode devolver um máximo de 1000 entidades em simultâneo e pode executar para um máximo de cinco segundos. Se o conjunto de resultados contém mais de 1.000 entidades, se a consulta não foi concluída dentro de cinco segundos, ou se a consulta cruza o limite da partição, o serviço de tabela devolve um token de continuação para permitir que o aplicativo de cliente solicitar o próximo conjunto de entidades. Para obter mais informações sobre como a continuação tokens trabalho, consulte [tempo limite de consulta e paginação](https://msdn.microsoft.com/library/azure/dd135718.aspx).  

Se estiver a utilizar a biblioteca de cliente de armazenamento, pode processar de automaticamente tokens de continuação para como ela retorna entidades a partir do serviço tabela. O seguinte código exemplo c# usando a biblioteca de cliente de armazenamento automaticamente manipula os tokens de continuação, se o serviço de tabela retorna numa resposta:  

```csharp
string filter = TableQuery.GenerateFilterCondition(
        "PartitionKey", QueryComparisons.Equal, "Sales");
TableQuery<EmployeeEntity> employeeQuery =
        new TableQuery<EmployeeEntity>().Where(filter);

var employees = employeeTable.ExecuteQuery(employeeQuery);
foreach (var emp in employees)
{
        ...
}  
```

O seguinte código c# lida com tokens de continuação explicitamente:  

```csharp
string filter = TableQuery.GenerateFilterCondition(
        "PartitionKey", QueryComparisons.Equal, "Sales");
TableQuery<EmployeeEntity> employeeQuery =
        new TableQuery<EmployeeEntity>().Where(filter);

TableContinuationToken continuationToken = null;

do
{
        var employees = employeeTable.ExecuteQuerySegmented(
        employeeQuery, continuationToken);
    foreach (var emp in employees)
    {
    ...
    }
    continuationToken = employees.ContinuationToken;
} while (continuationToken != null);  
```

Ao utilizar tokens de continuação explicitamente, pode controlar quando a aplicação obtém o segmento seguinte de dados. Por exemplo, se a aplicação de cliente permite aos usuários navegarem por entidades armazenadas numa tabela, um usuário pode decidir não para a página por meio de todas as entidades obtidas pela consulta, para que seu aplicativo só utilizaria um token de continuação para recuperar o próximo segmento quando o utilizador se concluído paginação por meio de todas as entidades no segmento atual. Essa abordagem tem vários benefícios:  

* Permite-lhe limitar a quantidade de dados para recuperar a partir do serviço tabela e de que mova através da rede.  
* Permite-lhe realizar e/s assíncrona no .NET.  
* Permite-lhe serializar o token de continuação para armazenamento persistente, para poder continuar em caso de uma falha de aplicativo.  

> [!NOTE]
> Um token de continuação normalmente devolve um segmento que contém 1.000 entidades, embora possa ser menos. Isso também é o caso, se limitar o número de entradas de uma consulta devolve usando **tirar** para retornar as primeiro n as entidades que correspondem aos critérios de pesquisa: o serviço de tabela pode devolver um segmento que contenham menos de entidades n juntamente com um token de continuação para permitem-lhe obter as entidades restantes.  
> 
> 

O código do c# seguinte mostra como modificar o número de entidades retornadas dentro de um segmento:  

```csharp
employeeQuery.TakeCount = 50;  
```

### <a name="server-side-projection"></a>Projecção do lado do servidor
Uma única entidade pode ter até 255 propriedades e ser até 1 MB de tamanho. Quando consulta a tabela e recuperar entidades, pode não necessitar de todas as propriedades e pode evitar a transferência de dados desnecessariamente (para o ajudar a reduzir a latência e o custo). Pode usar a projeção do lado do servidor para transferir apenas as propriedades que precisa. O exemplo seguinte é obtém apenas o **E-Mail** propriedade (juntamente com **PartitionKey**, **RowKey**, **Timestamp**e o **ETag**) de entidades selecionadas pela consulta.  

```csharp
string filter = TableQuery.GenerateFilterCondition(
        "PartitionKey", QueryComparisons.Equal, "Sales");
List<string> columns = new List<string>() { "Email" };
TableQuery<EmployeeEntity> employeeQuery =
        new TableQuery<EmployeeEntity>().Where(filter).Select(columns);

var entities = employeeTable.ExecuteQuery(employeeQuery);
foreach (var e in entities)
{
        Console.WriteLine("RowKey: {0}, EmployeeEmail: {1}", e.RowKey, e.Email);
}  
```

Observe como o **RowKey** valor está disponível, apesar de não estava incluído na lista de propriedades para recuperar.  

## <a name="modifying-entities"></a>Modificar entidades
A biblioteca de cliente de armazenamento permite-lhe modificar suas entidades armazenadas no serviço tabela, inserir, eliminar e a atualização de entidades. Pode usar o EGTs para vários insert, update e operações de exclusão para reduzir o número de ida e volta necessárias do batch e melhorar o desempenho da sua solução.  

Tenha em atenção que as exceções geradas quando a biblioteca de cliente de armazenamento é executado um EGT normalmente incluem o índice da entidade que causou o batch efetuar a ativação. Isso é útil quando está a depurar o código que usa EGTs.  

Também deve considerar como seu design afeta como a aplicação cliente lida com operações de atualização e de simultaneidade.  

### <a name="managing-concurrency"></a>Gerir a simultaneidade
Por predefinição, o serviço de tabela implementa verifica no nível de entidades individuais para a simultaneidade otimista **inserir**, **intercalar**, e **eliminar** operações, embora ele é possível que um cliente forçar o serviço de tabela para ignorar estas verificações. Para obter mais informações sobre como o serviço de tabela gere a simultaneidade, consulte [gerenciamento de simultaneidade no armazenamento do Microsoft Azure](../../storage/common/storage-concurrency.md).  

### <a name="merge-or-replace"></a>Intercalação ou substituição
O **substitua** método o **TableOperation** classe substitui sempre a entidade completa no serviço tabela. Se não incluir uma propriedade no pedido quando essa propriedade existe na entidade armazenada, o pedido remove essa propriedade da entidade armazenada. A menos que queira remover uma propriedade explicitamente de uma entidade armazenada, tem de incluir todas as propriedades no pedido.  

Pode utilizar o **intercalar** método o **TableOperation** classe para reduzir a quantidade de dados que enviar para o serviço de tabela para atualizar uma entidade. O **intercalar** método substitui quaisquer propriedades na entidade armazenada com valores de propriedade da entidade incluídos no pedido, mas deixa intactos quaisquer propriedades na entidade armazenada que não estão incluídas no pedido. Isto é útil se tiver entidades grandes e só precisa de atualizar um pequeno número de propriedades numa solicitação.  

> [!NOTE]
> O **substitua** e **intercalar** métodos falharem se a entidade não existe. Como alternativa, pode utilizar o **InsertOrReplace** e **InsertOrMerge** métodos que se não existir, crie uma nova entidade.  
> 
> 

## <a name="working-with-heterogeneous-entity-types"></a>Trabalhar com tipos de entidade heterogênea
O serviço de tabela é um *esquema* armazenamento de tabela, que significa que uma única tabela pode armazenar entidades de vários tipos, fornecendo enorme flexibilidade em seu design. O exemplo seguinte ilustra uma tabela de armazenamento dos funcionários e entidades de departamento:  

<table>
<tr>
<th>PartitionKey</th>
<th>RowKey</th>
<th>Carimbo de data/hora</th>
<th></th>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td>
<table>
<tr>
<th>FirstName</th>
<th>LastName</th>
<th>Idade</th>
<th>Email</th>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td></td>
</tr>
</table>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td>
<table>
<tr>
<th>FirstName</th>
<th>LastName</th>
<th>Idade</th>
<th>Email</th>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td></td>
</tr>
</table>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td>
<table>
<tr>
<th>Nome do Departamento</th>
<th>EmployeeCount</th>
</tr>
<tr>
<td></td>
<td></td>
</tr>
</table>
</td>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td>
<table>
<tr>
<th>FirstName</th>
<th>LastName</th>
<th>Idade</th>
<th>Email</th>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td></td>
</tr>
</table>
</td>
</tr>
</table>

Tenha em atenção que cada entidade tem de ter ainda **PartitionKey**, **RowKey**, e **Timestamp** valores, mas pode ter qualquer conjunto de propriedades. Além disso, não há nada para indicar o tipo de uma entidade, a menos que optar por armazenar essa informação em algum lugar. Existem duas opções para identificar o tipo de entidade:  

* Preceder o tipo de entidade para o **RowKey** (ou, possivelmente, o **PartitionKey**). Por exemplo, **EMPLOYEE_000123** ou **DEPARTMENT_SALES** como **RowKey** valores.  
* Utilize uma propriedade separada para registar o tipo de entidade conforme mostrado na tabela abaixo.  

<table>
<tr>
<th>PartitionKey</th>
<th>RowKey</th>
<th>Carimbo de data/hora</th>
<th></th>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td>
<table>
<tr>
<th>entityType</th>
<th>FirstName</th>
<th>LastName</th>
<th>Idade</th>
<th>Email</th>
</tr>
<tr>
<td>Funcionário</td>
<td></td>
<td></td>
<td></td>
<td></td>
</tr>
</table>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td>
<table>
<tr>
<th>entityType</th>
<th>FirstName</th>
<th>LastName</th>
<th>Idade</th>
<th>Email</th>
</tr>
<tr>
<td>Funcionário</td>
<td></td>
<td></td>
<td></td>
<td></td>
</tr>
</table>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td>
<table>
<tr>
<th>entityType</th>
<th>Nome do Departamento</th>
<th>EmployeeCount</th>
</tr>
<tr>
<td>Departamento</td>
<td></td>
<td></td>
</tr>
</table>
</td>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td>
<table>
<tr>
<th>entityType</th>
<th>FirstName</th>
<th>LastName</th>
<th>Idade</th>
<th>Email</th>
</tr>
<tr>
<td>Funcionário</td>
<td></td>
<td></td>
<td></td>
<td></td>
</tr>
</table>
</td>
</tr>
</table>

A primeira opção, a prefixação a entidade de tipo para o **RowKey**, é útil se existe a possibilidade de que a duas entidades de diferentes tipos podem ter o mesmo valor de chave. Ele também agrupa as entidades do mesmo tipo em conjunto na partição.  

As técnicas apresentadas nesta seção são especialmente relevantes para a discussão [relações de herança](table-storage-design-modeling.md#inheritance-relationships) anteriormente neste guia no artigo [modelando as relações](table-storage-design-modeling.md).  

> [!NOTE]
> Deve considerar a inclusão de um número de versão no valor de tipo de entidade para ativar aplicações de cliente evoluir POCO objetos e trabalhar com versões diferentes.  
> 
> 

O resto desta secção descreve alguns dos recursos na biblioteca de cliente de armazenamento que facilitam o trabalho com vários tipos de entidade na mesma tabela.  

### <a name="retrieving-heterogeneous-entity-types"></a>Obter tipos de entidade heterogênea
Se estiver a utilizar a biblioteca de cliente de armazenamento, tem três opções para trabalhar com vários tipos de entidade.  

Se souber o tipo da entidade armazenado com um específico **RowKey** e **PartitionKey** valores, em seguida, pode especificar o tipo de entidade ao obter a entidade, conforme mostrado nos dois exemplos anteriores que obter entidades do tipo **EmployeeEntity**: [Execução de uma consulta de ponto usando a biblioteca de cliente de armazenamento](#executing-a-point-query-using-the-storage-client-library) e [recuperar várias entidades usando o LINQ](#retrieving-multiple-entities-using-linq).  

A segunda opção consiste em utilizar o **DynamicTableEntity** tipo (uma matriz de propriedades), em vez de um tipo de entidade POCO concreto (esta opção também pode melhorar o desempenho porque não é necessário para serializar e desserializar a entidade para tipos .NET). O seguinte código c# potencialmente obtém várias entidades de diferentes tipos de tabela, mas retorna todas as entidades como **DynamicTableEntity** instâncias. Em seguida, utiliza a **EntityType** propriedade para determinar o tipo de cada entidade:  

```csharp
string filter = TableQuery.CombineFilters(
    TableQuery.GenerateFilterCondition("PartitionKey",
    QueryComparisons.Equal, "Sales"),
    TableOperators.And,
    TableQuery.CombineFilters(
    TableQuery.GenerateFilterCondition("RowKey",
                    QueryComparisons.GreaterThanOrEqual, "B"),
        TableOperators.And,
        TableQuery.GenerateFilterCondition("RowKey",
        QueryComparisons.LessThan, "F")
    )
);
TableQuery<DynamicTableEntity> entityQuery =
    new TableQuery<DynamicTableEntity>().Where(filter);
var employees = employeeTable.ExecuteQuery(entityQuery);

IEnumerable<DynamicTableEntity> entities = employeeTable.ExecuteQuery(entityQuery);
foreach (var e in entities)
{
EntityProperty entityTypeProperty;
if (e.Properties.TryGetValue("EntityType", out entityTypeProperty))
{
    if (entityTypeProperty.StringValue == "Employee")
    {
        // Use entityTypeProperty, RowKey, PartitionKey, Etag, and Timestamp
        }
    }
}  
```

Observe que, para obter outras propriedades tem de utilizar o **TryGetValue** método no **propriedades** propriedade do **DynamicTableEntity** classe.  

Uma terceira opção é combinar com o **DynamicTableEntity** tipo e uma **EntityResolver** instância. Isto permite-lhe resolver para vários tipos POCO da mesma consulta. Neste exemplo, o **EntityResolver** delegado está a utilizar o **EntityType** propriedade para distinguir entre os dois tipos de entidade que a consulta devolve. O **resolver** método utiliza a **resolvedor** delegado para resolver **DynamicTableEntity** instâncias para **TableEntity** instâncias.  

```csharp
EntityResolver<TableEntity> resolver = (pk, rk, ts, props, etag) =>
{

        TableEntity resolvedEntity = null;
        if (props["EntityType"].StringValue == "Department")
        {
        resolvedEntity = new DepartmentEntity();
        }
        else if (props["EntityType"].StringValue == "Employee")
        {
        resolvedEntity = new EmployeeEntity();
        }
        else throw new ArgumentException("Unrecognized entity", "props");

        resolvedEntity.PartitionKey = pk;
        resolvedEntity.RowKey = rk;
        resolvedEntity.Timestamp = ts;
        resolvedEntity.ETag = etag;
        resolvedEntity.ReadEntity(props, null);
        return resolvedEntity;
};

string filter = TableQuery.GenerateFilterCondition(
        "PartitionKey", QueryComparisons.Equal, "Sales");
TableQuery<DynamicTableEntity> entityQuery =
        new TableQuery<DynamicTableEntity>().Where(filter);

var entities = employeeTable.ExecuteQuery(entityQuery, resolver);
foreach (var e in entities)
{
        if (e is DepartmentEntity)
        {
    ...
        }
        if (e is EmployeeEntity)
        {
    ...
        }
}  
```

### <a name="modifying-heterogeneous-entity-types"></a>Tipos de entidade heterogênea de modificação
Não é necessário saber o tipo de uma entidade para eliminá-lo e, sempre sabe o tipo de uma entidade ao inseri-lo. No entanto, pode usar **DynamicTableEntity** tipo para atualizar uma entidade sem saber o seu tipo e sem utilizar uma classe de entidade POCO. O código de exemplo seguinte obtém uma única entidade e verifica a **EmployeeCount** propriedade existe antes de atualizá-lo.  

```csharp
TableResult result =
        employeeTable.Execute(TableOperation.Retrieve(partitionKey, rowKey));
DynamicTableEntity department = (DynamicTableEntity)result.Result;

EntityProperty countProperty;

if (!department.Properties.TryGetValue("EmployeeCount", out countProperty))
{
        throw new
        InvalidOperationException("Invalid entity, EmployeeCount property not found.");
}
countProperty.Int32Value += 1;
employeeTable.Execute(TableOperation.Merge(department));  
```

## <a name="controlling-access-with-shared-access-signatures"></a>Controlar o acesso com assinaturas de acesso partilhado
Pode utilizar tokens de assinatura de acesso partilhado (SAS) para permitir que aplicativos de cliente modificar (e consultar) entidades da tabela sem a necessidade de incluir a sua chave de conta de armazenamento no seu código. Normalmente, há três benefícios principais para através da SAS em seu aplicativo:  

* Não é necessário distribuir a chave de conta de armazenamento para uma plataforma insegura (por exemplo, um dispositivo móvel) para permitir que o dispositivo aceder e modificar entidades no serviço tabela.  
* Pode descarregar o parte do trabalho que executam de funções web e de trabalho no gerenciamento de suas entidades para dispositivos de cliente, como computadores de usuários finais e dispositivos móveis.  
* Pode atribuir um restrita e tempo conjunto limitado de permissões para um cliente (por exemplo, permitindo o acesso só de leitura a recursos específicos).  

Para obter mais informações sobre como utilizar SAS tokens com o serviço de tabela, consulte [usando partilhado assinaturas de acesso (SAS)](../../storage/common/storage-dotnet-shared-access-signature-part-1.md).  

No entanto, tem ainda de gerar os tokens SAS que concedem um aplicativo de cliente para as entidades no serviço de tabela: deve fazê-lo num ambiente que tenha acesso seguro para as chaves de conta de armazenamento. Normalmente, utiliza uma função web ou de trabalho para gerar os tokens de SAS e entregue-o para as aplicações de cliente que precisam de acesso para as suas entidades. Uma vez que ainda há uma sobrecarga envolvida na geração e fornecer SAS tokens para os clientes, deve considerar a melhor maneira, para reduzir esta sobrecarga, especialmente em cenários de volume elevado.  

É possível gerar um token SAS que concede acesso a um subconjunto de entidades numa tabela. Por padrão, criar um token SAS para uma tabela inteira, mas também é possível especificar que o token SAS conceder acesso a qualquer um diversas **PartitionKey** valores ou um intervalo de **PartitionKey** e **RowKey** valores. Pode optar por gerar tokens SAS para utilizadores individuais do seu sistema, de modo a que o token SAS de cada utilizador só lhes permite aceder às suas próprias entidades no serviço tabela.  

## <a name="asynchronous-and-parallel-operations"></a>Operações assíncronas e paralelas
Desde que são propagar os pedidos entre várias partições, pode melhorar a capacidade de resposta de débito e o cliente utilizando consultas assíncronas ou paralelas.
Por exemplo, pode ter dois ou mais trabalho instâncias de função acessar suas tabelas em paralelo. Poderia ter funções de trabalho individuais responsáveis por determinada conjuntos de partições ou simplesmente ter várias funções instâncias de trabalho, cada capazes de aceder a todas as partições numa tabela.  

Dentro de uma instância de cliente, pode melhorar o débito ao executar operações de armazenamento de forma assíncrona. A biblioteca de cliente de armazenamento torna mais fácil escrever consultas assíncronas e modificações. Por exemplo, pode começar com o método síncrono que recupera todas as entidades numa partição conforme mostrado no código a seguir em C#:  

```csharp
private static void ManyEntitiesQuery(CloudTable employeeTable, string department)
{
        string filter = TableQuery.GenerateFilterCondition(
        "PartitionKey", QueryComparisons.Equal, department);
        TableQuery<EmployeeEntity> employeeQuery =
        new TableQuery<EmployeeEntity>().Where(filter);

        TableContinuationToken continuationToken = null;

        do
        {
        var employees = employeeTable.ExecuteQuerySegmented(
                employeeQuery, continuationToken);
        foreach (var emp in employees)
    {
        ...
    }
        continuationToken = employees.ContinuationToken;
        } while (continuationToken != null);
}  
```

Pode facilmente modificar esse código para que a consulta é executada de forma assíncrona, da seguinte forma:  

```csharp
private static async Task ManyEntitiesQueryAsync(CloudTable employeeTable, string department)
{
        string filter = TableQuery.GenerateFilterCondition(
        "PartitionKey", QueryComparisons.Equal, department);
        TableQuery<EmployeeEntity> employeeQuery =
        new TableQuery<EmployeeEntity>().Where(filter);
        TableContinuationToken continuationToken = null;

        do
        {
        var employees = await employeeTable.ExecuteQuerySegmentedAsync(
                employeeQuery, continuationToken);
        foreach (var emp in employees)
        {
            ...
        }
        continuationToken = employees.ContinuationToken;
            } while (continuationToken != null);
}  
```

Neste exemplo assíncrona, pode ver as seguintes alterações da versão síncrona:  

* A assinatura do método agora inclui a **async** modificador e devolve um **tarefa** instância.  
* Em vez de chamar o **ExecuteSegmented** chama o método para recuperar resultados, o método agora a **ExecuteSegmentedAsync** método e utiliza o **await** modificador para obter resultados de forma assíncrona.  

A aplicação cliente pode chamar esse método várias vezes (com valores diferentes para o **departamento** parâmetro), e cada consulta será executada num thread separado.  

Tenha em atenção que não existe nenhuma versão assíncrona do **Execute** método na **TableQuery** classe porque o **IEnumerable** interface não suporta assíncrona enumeração.  

Também pode inserir, atualizar e eliminar entidades de forma assíncrona. O exemplo do c# seguinte mostra um método simples, síncrono, para inserir ou substituir uma entidade employee:  

```csharp
private static void SimpleEmployeeUpsert(CloudTable employeeTable,
        EmployeeEntity employee)
{
        TableResult result = employeeTable
        .Execute(TableOperation.InsertOrReplace(employee));
        Console.WriteLine("HTTP Status: {0}", result.HttpStatusCode);
}  
```

Pode facilmente modificar esse código para que a atualização seja executada de forma assíncrona, da seguinte forma:  

```csharp
private static async Task SimpleEmployeeUpsertAsync(CloudTable employeeTable,
        EmployeeEntity employee)
{
        TableResult result = await employeeTable
        .ExecuteAsync(TableOperation.InsertOrReplace(employee));
        Console.WriteLine("HTTP Status: {0}", result.HttpStatusCode);
}  
```

Neste exemplo assíncrona, pode ver as seguintes alterações da versão síncrona:  

* A assinatura do método agora inclui a **async** modificador e devolve um **tarefa** instância.  
* Em vez de chamar o **Execute** chama o método para atualizar a entidade, o método agora a **ExecuteAsync** método e utiliza o **await** modificador para obter resultados de forma assíncrona.  

A aplicação cliente pode chamar vários métodos assíncronos como este, e cada invocação do método será executada num thread separado.  

## <a name="next-steps"></a>Passos Seguintes

- [Modelando as relações](table-storage-design-modeling.md)
- [Design para a consulta](table-storage-design-for-query.md)
- [Encriptação de dados de tabela](table-storage-design-encrypt-data.md)
- [Design para modificação de dados](table-storage-design-for-modification.md)
