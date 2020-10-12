---
title: Padrões de design de mesa de armazenamento Azure / Microsoft Docs
description: Reveja padrões de design apropriados para uso com soluções de serviço de mesa em Azure. Aborde questões e trocas que são discutidas noutros artigos.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 04/08/2019
ms.author: tamram
ms.subservice: tables
ms.custom: devx-track-csharp
ms.openlocfilehash: b200782d10ae3637fcade63feab1e638d40acddb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89006351"
---
# <a name="table-design-patterns"></a>Padrões de design da tabela
Este artigo descreve alguns padrões adequados para utilização com soluções de serviço de mesa. Além disso, você verá como você pode praticamente abordar algumas das questões e trade-offs discutidos em outros artigos de design de armazenamento de mesa. O diagrama seguinte resume as relações entre os diferentes padrões:  

![para procurar dados relacionados](media/storage-table-design-guide/storage-table-design-IMAGE05.png)


O mapa de padrões acima destaca algumas relações entre padrões (azul) e anti-padrões (laranja) que são documentados neste guia. Há muitos outros padrões que vale a pena considerar. Por exemplo, um dos cenários-chave para o Serviço de Mesa é utilizar o Padrão de [Visualização Materializado](https://msdn.microsoft.com/library/azure/dn589782.aspx) a partir do padrão de segregação de [responsabilidade de comando (CQRS).](https://msdn.microsoft.com/library/azure/jj554200.aspx)  

## <a name="intra-partition-secondary-index-pattern"></a>Padrão do índice secundário intra-partição
Armazenar várias cópias de cada entidade utilizando diferentes valores **RowKey** (na mesma partição) para permitir procuras rápidas e eficientes e ordens de classificação alternativas utilizando diferentes valores **RowKey.** As atualizações entre cópias podem ser mantidas consistentes utilizando EGTs.  

### <a name="context-and-problem"></a>Contexto e problema
O serviço tabela indexa automaticamente as entidades utilizando os valores **PartitionKey** e **RowKey.** Isto permite que uma aplicação do cliente recupere uma entidade de forma eficiente utilizando estes valores. Por exemplo, utilizando a estrutura de tabela apresentada abaixo, uma aplicação do cliente pode usar uma consulta de ponto para recuperar uma entidade de colaborador individual, utilizando o nome do departamento e o ID do empregado (os valores **PartitionKey** e **RowKey).** Um cliente também pode recuperar entidades classificadas por identificação de funcionários dentro de cada departamento.

![Imagem06](media/storage-table-design-guide/storage-table-design-IMAGE06.png)

Se também quiser encontrar uma entidade de colaboradores com base no valor de outra propriedade, como endereço de e-mail, deve utilizar uma verificação de partição menos eficiente para encontrar uma correspondência. Isto porque o serviço de mesa não fornece índices secundários. Além disso, não há opção de solicitar uma lista de funcionários classificados numa ordem diferente da ordem **RowKey.**  

### <a name="solution"></a>Solução
Para contornar a falta de índices secundários, pode armazenar várias cópias de cada entidade com cada cópia utilizando um valor **RowKey** diferente. Se armazenar uma entidade com as estruturas abaixo indicadas, pode recuperar eficientemente entidades de colaboradores com base no endereço de e-mail ou iD do funcionário. Os valores de prefixo para o **RowKey**, "empid_" e "email_" permitem-lhe consultar um único empregado ou uma série de colaboradores utilizando uma série de endereços de e-mail ou IDs de funcionários.  

![Entidades dos trabalhadores](media/storage-table-design-guide/storage-table-design-IMAGE07.png)

Os dois critérios de filtro a seguir (um olhando para cima pelo ID do empregado e outro procurando por endereço de e-mail) ambos especificam consultas de ponto:  

* $filter=(PartitionKey eq 'Sales') e (RowKey eq 'empid_000223')  
* $filter=(PartitionKey eq 'Sales') e (RowKey eq' email_jonesj@contoso.com ')  

Se consultar uma série de entidades de colaboradores, pode especificar um intervalo classificado na ordem de identificação dos colaboradores, ou uma gama ordenada por ordem de endereço de e-mail, consultando entidades com o prefixo apropriado no **RowKey.**  

* Para encontrar todos os colaboradores do departamento de Vendas com um ID de empregado na gama 000100 a 000199 use: $filter=(PartitionKey eq 'Sales') e (RowKey ge 'empid_000100') e (RowKey le 'empid_000199')  
* Encontrar todos os colaboradores do departamento de Vendas com um endereço de e-mail a começar pela letra 'a' use: $filter=(PartitionKey eq 'Sales') e (RowKey ge 'email_a') e (RowKey lt 'email_b')  
  
  A sintaxe do filtro utilizada nos exemplos acima é do serviço de tabela REST API, para mais informações, consulte [Entidades De Consulta](https://msdn.microsoft.com/library/azure/dd179421.aspx).  

### <a name="issues-and-considerations"></a>Problemas e considerações
Na altura de decidir como implementar este padrão, considere os seguintes pontos:  

* O armazenamento de mesas é relativamente barato de utilizar, pelo que a despesa com o armazenamento de dados duplicados não deve constituir uma grande preocupação. No entanto, deve sempre avaliar o custo do seu design com base nos seus requisitos de armazenamento previstos e apenas adicionar entidades duplicadas para suportar as consultas que a sua aplicação do cliente irá executar.  
* Uma vez que as entidades de índice secundário estão armazenadas na mesma divisão que as entidades originais, deve garantir que não exceda os objetivos de escalabilidade para uma partição individual.  
* Pode manter as suas entidades duplicadas consistentes entre si, utilizando EGTs para atualizar as duas cópias da entidade atomicamente. Isto implica que deve armazenar todas as cópias de uma entidade na mesma divisão. Para mais informações, consulte a secção [Utilização de Transações do Grupo Entity](table-storage-design.md#entity-group-transactions).  
* O valor utilizado para o **RowKey** deve ser único para cada entidade. Considere usar valores-chave compostos.  
* Os valores numéricos de enchimento no **RowKey** (por exemplo, o ID 000223 do empregado), permitem a correta triagem e filtragem com base nos limites superior e inferior.  
* Não precisa necessariamente de duplicar todas as propriedades da sua entidade. Por exemplo, se as consultas que procuram as entidades que usam o endereço de e-mail no **RowKey** nunca precisarem da idade do trabalhador, estas entidades podem ter a seguinte estrutura:

   ![Estrutura da entidade dos colaboradores](media/storage-table-design-guide/storage-table-design-IMAGE08.png)


* Normalmente, é melhor armazenar dados duplicados e garantir que pode recuperar todos os dados de que necessita com uma única consulta, do que usar uma consulta para localizar uma entidade e outra para procurar os dados necessários.  

### <a name="when-to-use-this-pattern"></a>Quando utilizar este padrão
Use este padrão quando a sua aplicação ao cliente precisa de recuperar entidades usando uma variedade de chaves diferentes, quando o seu cliente precisa de recuperar entidades em diferentes ordens de tipo, e onde pode identificar cada entidade usando uma variedade de valores únicos. No entanto, deve certificar-se de que não excede os limites de escalabilidade da partição quando estiver a realizar sondagens de entidade utilizando os diferentes valores **rowKey.**  

### <a name="related-patterns-and-guidance"></a>Padrões e orientações relacionados
Os padrões e orientações que se seguem podem também ser relevantes ao implementar este padrão:  

* [Padrão do índice secundário inter-partição](#inter-partition-secondary-index-pattern)
* [Padrão de chave composto](#compound-key-pattern)
* Transações do Grupo entidade
* [Trabalhar com tipos de entidades heterogéneas](#working-with-heterogeneous-entity-types)

## <a name="inter-partition-secondary-index-pattern"></a>Padrão do índice secundário inter-partição
Armazenar várias cópias de cada entidade utilizando diferentes valores **RowKey** em divisórias separadas ou em tabelas separadas para permitir pesquisas rápidas e eficientes e ordens de classificação alternativas utilizando diferentes valores **RowKey.**  

### <a name="context-and-problem"></a>Contexto e problema
O serviço tabela indexa automaticamente as entidades utilizando os valores **PartitionKey** e **RowKey.** Isto permite que uma aplicação do cliente recupere uma entidade de forma eficiente utilizando estes valores. Por exemplo, utilizando a estrutura de tabela apresentada abaixo, uma aplicação do cliente pode usar uma consulta de ponto para recuperar uma entidade de colaborador individual, utilizando o nome do departamento e o ID do empregado (os valores **PartitionKey** e **RowKey).** Um cliente também pode recuperar entidades classificadas por identificação de funcionários dentro de cada departamento.  

![ID de colaborador](media/storage-table-design-guide/storage-table-design-IMAGE09.png)

Se também quiser encontrar uma entidade de colaboradores com base no valor de outra propriedade, como endereço de e-mail, deve utilizar uma verificação de partição menos eficiente para encontrar uma correspondência. Isto porque o serviço de mesa não fornece índices secundários. Além disso, não há opção de solicitar uma lista de funcionários classificados numa ordem diferente da ordem **RowKey.**  

Está a antecipar um elevado volume de transações contra estas entidades e pretende minimizar o risco de o serviço de Tabela estrangular o seu cliente.  

### <a name="solution"></a>Solução
Para contornar a falta de índices secundários, pode armazenar várias cópias de cada entidade com cada cópia utilizando diferentes valores **PartitionKey** e **RowKey.** Se armazenar uma entidade com as estruturas abaixo indicadas, pode recuperar eficientemente entidades de colaboradores com base no endereço de e-mail ou iD do funcionário. Os valores de prefixo para a **PartitionKey**, "empid_" e "email_" permitem identificar o índice que pretende utilizar para uma consulta.  

![Índice primário e índice secundário](media/storage-table-design-guide/storage-table-design-IMAGE10.png)


Os dois critérios de filtro a seguir (um olhando para cima pelo ID do empregado e outro procurando por endereço de e-mail) ambos especificam consultas de ponto:  

* $filter=(PartitionKey eq 'empid_Sales') e (RowKey eq '000223')
* $filter=(PartitionKey eq 'email_Sales') e (RowKey eq ' jonesj@contoso.com ')  

Se consultar uma série de entidades de colaboradores, pode especificar um intervalo classificado na ordem de identificação dos colaboradores, ou uma gama ordenada por ordem de endereço de e-mail, consultando entidades com o prefixo apropriado no **RowKey.**  

* Para encontrar todos os colaboradores do departamento de Vendas com um ID de empregado na gama **000100** a **000199** classificados em uso de iD de empregado: $filter=(PartitionKey eq 'empid_Sales') e (RowKey ge '000100') e (RowKey le '000199')  
* Para encontrar todos os colaboradores do departamento de Vendas com um endereço de e-mail que começa com 'a' classificado na utilização de endereço de endereço de e-mail: $filter=(PartitionKey eq 'email_Sales') e (RowKey ge 'a') e (RowKey lt 'b')  

A sintaxe do filtro utilizada nos exemplos acima é do serviço de tabela REST API, para mais informações, consulte [Entidades De Consulta](https://msdn.microsoft.com/library/azure/dd179421.aspx).  

### <a name="issues-and-considerations"></a>Problemas e considerações
Na altura de decidir como implementar este padrão, considere os seguintes pontos:  

* Pode manter as suas entidades duplicadas eventualmente consistentes entre si, utilizando o [padrão de transações eventualmente consistente](#eventually-consistent-transactions-pattern) para manter as entidades de índice primário e secundário.  
* O armazenamento de mesas é relativamente barato de utilizar, pelo que a despesa com o armazenamento de dados duplicados não deve constituir uma grande preocupação. No entanto, deve sempre avaliar o custo do seu design com base nos seus requisitos de armazenamento previstos e apenas adicionar entidades duplicadas para suportar as consultas que a sua aplicação do cliente irá executar.  
* O valor utilizado para o **RowKey** deve ser único para cada entidade. Considere usar valores-chave compostos.  
* Os valores numéricos de enchimento no **RowKey** (por exemplo, o ID 000223 do empregado), permitem a correta triagem e filtragem com base nos limites superior e inferior.  
* Não precisa necessariamente de duplicar todas as propriedades da sua entidade. Por exemplo, se as consultas que procuram as entidades que usam o endereço de e-mail no **RowKey** nunca precisarem da idade do trabalhador, estas entidades podem ter a seguinte estrutura:
  
   ![Entidade dos trabalhadores (índice secundário)](media/storage-table-design-guide/storage-table-design-IMAGE11.png)

* Normalmente, é melhor armazenar dados duplicados e garantir que pode recuperar todos os dados de que necessita com uma única consulta do que usar uma consulta para localizar uma entidade usando o índice secundário e outra para procurar os dados necessários no índice primário.  

### <a name="when-to-use-this-pattern"></a>Quando utilizar este padrão
Use este padrão quando a sua aplicação ao cliente precisa de recuperar entidades usando uma variedade de chaves diferentes, quando o seu cliente precisa de recuperar entidades em diferentes ordens de tipo, e onde pode identificar cada entidade usando uma variedade de valores únicos. Utilize este padrão quando pretender evitar exceder os limites de escalabilidade da partição quando estiver a realizar sondagens de entidade utilizando os diferentes valores **RowKey.**  

### <a name="related-patterns-and-guidance"></a>Padrões e orientações relacionados
Os padrões e orientações que se seguem podem também ser relevantes ao implementar este padrão:  

* [Padrão de transações eventualmente consistente](#eventually-consistent-transactions-pattern)  
* [Padrão do índice secundário intra-partição](#intra-partition-secondary-index-pattern)  
* [Padrão de chave composto](#compound-key-pattern)  
* Transações do Grupo entidade  
* [Trabalhar com tipos de entidades heterogéneas](#working-with-heterogeneous-entity-types)  

## <a name="eventually-consistent-transactions-pattern"></a>Padrão de transações eventualmente consistente
Ativar um comportamento eventualmente consistente através dos limites da partição ou dos limites do sistema de armazenamento utilizando as filas do Azure.  

### <a name="context-and-problem"></a>Contexto e problema
As EGTs permitem transações atómicas em várias entidades que partilham a mesma chave de partição. Por razões de desempenho e escalabilidade, pode decidir armazenar entidades que tenham requisitos de consistência em divisórias separadas ou num sistema de armazenamento separado: nesse cenário, não pode utilizar EGTs para manter a consistência. Por exemplo, pode ter a obrigação de manter uma eventual consistência entre:  

* Entidades armazenadas em duas divisórias diferentes na mesma tabela, em tabelas diferentes, ou em diferentes contas de armazenamento.  
* Uma entidade armazenada no serviço Table e uma bolha armazenada no serviço Blob.  
* Uma entidade armazenada no serviço Table e um ficheiro num sistema de ficheiros.  
* Uma entidade armazenada no serviço Table ainda indexada usando o serviço de Pesquisa Cognitiva Azure.  

### <a name="solution"></a>Solução
Ao utilizar as filas Azure, pode implementar uma solução que proporciona uma eventual consistência em duas ou mais divisórias ou sistemas de armazenamento.
Para ilustrar esta abordagem, assuma que tem a obrigação de arquivar antigas entidades de colaboradores. As antigas entidades de trabalhadores raramente são questionadas e devem ser excluídas de quaisquer atividades que lidem com os atuais colaboradores. Para implementar este requisito, armazena funcionários ativos na tabela **Current** e antigos colaboradores na tabela **Archive.** Arquivar um colaborador requer que elimine a entidade da tabela **Current** e adicione a entidade à tabela **Archive,** mas não é possível utilizar um EGT para realizar estas duas operações. Para evitar o risco de uma falha fazer com que uma entidade apareça em ambas ou em nenhuma das tabelas, a operação de arquivo deve ser eventualmente consistente. O diagrama de sequência a seguir descreve os passos desta operação. Estão previstos mais pormenores para os caminhos de exceção no texto que se segue.  

![Solução de filas Azure](media/storage-table-design-guide/storage-table-design-IMAGE12.png)

Um cliente inicia a operação de arquivo colocando uma mensagem numa fila do Azure, neste exemplo para arquivar #456 de funcionários. Um papel de trabalhador sonda a fila para novas mensagens; quando encontra um, lê a mensagem e deixa uma cópia escondida na fila. A função do trabalhador em seguida recolhe uma cópia da entidade da tabela **Corrente,** insere uma cópia na tabela **Archive** e, em seguida, elimina o original da tabela **Current.** Finalmente, se não houve erros dos passos anteriores, o papel do trabalhador elimina a mensagem escondida da fila.  

Neste exemplo, o passo 4 insere o funcionário na tabela **Archive.** Pode adicionar o empregado a uma bolha no serviço Blob ou a um ficheiro num sistema de ficheiros.  

### <a name="recovering-from-failures"></a>Recuperação de falhas
É importante que as operações nos passos **4** e **5** sejam *consideradas como possíveis* no caso de a função do trabalhador ter de reiniciar a operação de arquivo. Se estiver a utilizar o serviço Tabela, para o passo **4** deverá utilizar uma operação de "inserir ou substituir"; para o passo **5,** deve utilizar uma operação de "excluir se existir" na biblioteca do cliente que está a utilizar. Se estiver a utilizar outro sistema de armazenamento, deve utilizar uma operação idempotente apropriada.  

Se o papel do trabalhador nunca completar o passo **6,** então, após um intervalo, a mensagem reaparece na fila pronta para o papel do trabalhador tentar reprocessá-la. O papel do trabalhador pode verificar quantas vezes uma mensagem na fila foi lida e, se necessário, sinalizar é uma mensagem "venenosa" para investigação enviando-a para uma fila separada. Para obter mais informações sobre a leitura de mensagens de fila e a verificação da contagem de pedidos, consulte ['Obter Mensagens'.](https://msdn.microsoft.com/library/azure/dd179474.aspx)  

Alguns erros dos serviços de Tabela e Fila são erros transitórios, e a sua aplicação ao cliente deve incluir lógica de relemis para lidar com eles.  

### <a name="issues-and-considerations"></a>Problemas e considerações
Na altura de decidir como implementar este padrão, considere os seguintes pontos:  

* Esta solução não prevê o isolamento das transações. Por exemplo, um cliente podia ler as tabelas **Corrente** e **Arquivo** quando o papel do trabalhador estava entre os passos **4** e **5**, e ver uma visão inconsistente dos dados. Os dados serão consistentes eventualmente.  
* Deve certificar-se de que os passos 4 e 5 são idempotentes para garantir uma eventual consistência.  
* Pode escalar a solução utilizando várias filas e instâncias de função do trabalhador.  

### <a name="when-to-use-this-pattern"></a>Quando utilizar este padrão
Utilize este padrão quando pretender garantir uma eventual consistência entre entidades que existem em diferentes divisórias ou tabelas. Pode estender este padrão para garantir uma eventual consistência para as operações através do serviço Table e do serviço Blob e outras fontes de dados de armazenamento não-Azure, como base de dados ou o sistema de ficheiros.  

### <a name="related-patterns-and-guidance"></a>Padrões e orientações relacionados
Os padrões e orientações que se seguem podem também ser relevantes ao implementar este padrão:  

* Transações do Grupo entidade  
* [Fundir ou substituir](#merge-or-replace)  

> [!NOTE]
> Se o isolamento de transações for importante para a sua solução, deve considerar redesenhar as suas tabelas para permitir a utilização de EGTs.  
> 
> 

## <a name="index-entities-pattern"></a>Padrão de entidades de índice
Manter entidades indexadas para permitir pesquisas eficientes que devolvam listas de entidades.  

### <a name="context-and-problem"></a>Contexto e problema
O serviço tabela indexa automaticamente as entidades utilizando os valores **PartitionKey** e **RowKey.** Isto permite que uma aplicação do cliente recupere uma entidade de forma eficiente usando uma consulta de ponto. Por exemplo, utilizando a estrutura de tabela apresentada abaixo, uma aplicação do cliente pode recuperar eficientemente uma entidade de colaborador individual utilizando o nome do departamento e o ID do empregado (o **PartitionKey** e **o RowKey).**  

![Entidade dos trabalhadores](media/storage-table-design-guide/storage-table-design-IMAGE13.png)

Se também quiser ser capaz de recuperar uma lista de entidades de colaboradores com base no valor de outra propriedade não única, como o seu apelido, deve utilizar uma verificação de partição menos eficiente para encontrar fósforos em vez de usar um índice para os procurar diretamente. Isto porque o serviço de mesa não fornece índices secundários.  

### <a name="solution"></a>Solução
Para ativar o número de procura pelo apelido com a estrutura da entidade acima indicada, deve manter as listas de IDs dos colaboradores. Se quiser recuperar as entidades de empregados com um apelido particular, como o Jones, tem primeiro de localizar a lista de identificações dos empregados para funcionários com o Jones como apelido e, em seguida, recuperar essas entidades de empregados. Existem três opções principais para armazenar as listas de IDs dos funcionários:  

* Use o armazenamento de bolhas.  
* Criar entidades indexárias na mesma divisão que as entidades de trabalho.  
* Criar entidades indexárias numa divisória ou tabela separada.  

<u>Opção #1: Use o armazenamento de bolhas</u>  

Para a primeira opção, cria-se uma bolha para cada apelido único, e em cada blob armazena uma lista dos valores **PartitionKey** (departamento) e **RowKey** (ID dos funcionários) para os funcionários que têm esse apelido. Quando adicionar ou eliminar um empregado, deve certificar-se de que o conteúdo da bolha relevante é eventualmente consistente com as entidades do colaborador.  

<u>Opção #2:</u> Criar entidades indexárias na mesma partição  

Para a segunda opção, utilize entidades indexárias que armazenem os seguintes dados:  

![Entidade de índice de empregados](media/storage-table-design-guide/storage-table-design-IMAGE14.png)

A propriedade **EmployeeIDs** contém uma lista de IDs de empregados para funcionários com o último nome armazenado no **RowKey**.  

Os seguintes passos descrevem o processo que deve seguir quando estiver a adicionar um novo funcionário se estiver a utilizar a segunda opção. Neste exemplo, estamos adicionando um empregado com ID 000152 e um sobrenome Jones no departamento de Vendas:  

1. Recupere a entidade de índice com um valor **PartitionKey** "Sales" e o valor **RowKey** "Jones". Guarde o ETag desta entidade para utilizar no passo 2.  
2. Criar uma transação de grupo de entidades (isto é, uma operação de lote) que insira a nova entidade de empregados (**Valor PartitionKey** "Vendas" e valor **RowKey** "000152"), e atualiza a entidade de índices (**valor PartitionKey** "Sales" e **valor RowKey** "Jones") adicionando o novo ID do empregado à lista no campo EmployeeIDs. Para obter mais informações sobre transações de grupos de entidades, consulte As Transações do Grupo Entity.  
3. Se a transação do grupo de entidades falhar devido a um erro de concordância otimista (alguém acabou de modificar a entidade de índice), então precisa recomeçar no passo 1.  

Pode utilizar uma abordagem semelhante para eliminar um empregado se estiver a utilizar a segunda opção. Alterar o apelido de um colaborador é um pouco mais complexo porque terá de executar uma transação de grupo de entidades que atualiza três entidades: a entidade de colaboradores, a entidade indexa para o apelido antigo e a entidade indexa para o novo apelido. Tem de recuperar cada entidade antes de efetuar quaisquer alterações para recuperar os valores ETag que pode utilizar para executar as atualizações utilizando a concordância otimista.  

Os seguintes passos descrevem o processo que deve seguir quando precisa de procurar todos os colaboradores com um nome dado num departamento se estiver a utilizar a segunda opção. Neste exemplo, estamos a procurar todos os empregados com apelido Jones no departamento de Vendas:  

1. Recupere a entidade de índice com um valor **PartitionKey** "Sales" e o valor **RowKey** "Jones".  
2. Analise a lista de identificações de empregados no campo dos Empregados.  
3. Se precisar de informações adicionais sobre cada um destes colaboradores (como os seus endereços de e-mail), recupere cada uma das entidades colaboradoras utilizando valores **partitionKey** "Vendas" e **RowKey** da lista de colaboradores que obteve no passo 2.  

<u>Opção #3:</u> Criar entidades indexárias numa divisória ou tabela separada  

Para a terceira opção, utilize entidades indexárias que armazenem os seguintes dados:  

![Entidade indexa dos empregados numa divisória separada](media/storage-table-design-guide/storage-table-design-IMAGE15.png)


A propriedade **EmployeeIDs** contém uma lista de IDs de empregados para funcionários com o último nome armazenado no **RowKey**.  

Com a terceira opção, não é possível utilizar EGTs para manter a consistência porque as entidades indexam uma divisão separada das entidades dos colaboradores. Certifique-se de que as entidades indexam eventualmente são consistentes com as entidades dos colaboradores.  

### <a name="issues-and-considerations"></a>Problemas e considerações
Na altura de decidir como implementar este padrão, considere os seguintes pontos:  

* Esta solução requer pelo menos duas consultas para recuperar entidades correspondentes: uma para consultar as entidades indexam para obter a lista de valores **rowKey** e, em seguida, consultas para recuperar cada entidade na lista.  
* Dado que uma entidade individual tem um tamanho máximo de 1 MB, opção #2 e opção #3 na solução assumem que a lista de IDs dos empregados para qualquer apelido dado nunca é superior a 1 MB. Se a lista de IDs dos empregados for provavelmente superior a 1 MB de tamanho, utilize a opção #1 e armazene os dados do índice no armazenamento de bolhas.  
* Se utilizar a opção #2 (utilizando EGTs para lidar com a adição e eliminação de funcionários e alterando o apelido de um empregado) deve avaliar se o volume de transações se aproximará dos limites de escalabilidade numa determinada partição. Se for esse o caso, deverá considerar uma solução eventualmente consistente (opção #1 ou opção #3) que utilize filas para lidar com os pedidos de atualização e lhe permita armazenar as suas entidades indexam numa divisão separada das entidades dos colaboradores.  
* Opção #2 nesta solução pressupõe que você quer olhar pelo apelido dentro de um departamento: por exemplo, você quer recuperar uma lista de funcionários com um sobrenome Jones no departamento de Vendas. Se quiser ser capaz de procurar todos os empregados com um sobrenome Jones em toda a organização, use qualquer opção #1 ou opção #3.
* Pode implementar uma solução baseada em filas que proporciona uma eventual consistência (ver o [padrão de transações eventualmente consistente](#eventually-consistent-transactions-pattern) para obter mais detalhes).  

### <a name="when-to-use-this-pattern"></a>Quando utilizar este padrão
Use este padrão quando quiser procurar um conjunto de entidades que todos partilham um valor de propriedade comum, como todos os funcionários com o sobrenome Jones.  

### <a name="related-patterns-and-guidance"></a>Padrões e orientações relacionados
Os padrões e orientações que se seguem podem também ser relevantes ao implementar este padrão:  

* [Padrão de chave composto](#compound-key-pattern)  
* [Padrão de transações eventualmente consistente](#eventually-consistent-transactions-pattern)  
* Transações do Grupo entidade  
* [Trabalhar com tipos de entidades heterogéneas](#working-with-heterogeneous-entity-types)  

## <a name="denormalization-pattern"></a>Padrão de desnormalização
Combine dados relacionados numa única entidade para que possa recuperar todos os dados de que necessita com uma única consulta de ponto.  

### <a name="context-and-problem"></a>Contexto e problema
Numa base de dados relacional, normalmente normaliza os dados para remover duplicações, resultando em consultas que recolhem dados de várias tabelas. Se normalizar os seus dados nas tabelas Azure, deve efetuar várias viagens de ida e volta do cliente ao servidor para recuperar os seus dados relacionados. Por exemplo, com a estrutura da tabela mostrada abaixo, você precisa de duas viagens de ida e volta para recuperar os detalhes para um departamento: um para ir buscar a entidade do departamento que inclui o ID do gerente, e, em seguida, outro pedido para recolher os detalhes do gestor em uma entidade de empregado.  

![Entidade do Departamento e Entidade dos Colaboradores](media/storage-table-design-guide/storage-table-design-IMAGE16.png)

### <a name="solution"></a>Solução
Em vez de armazenar os dados em duas entidades distintas, desnormalizar os dados e guardar uma cópia dos detalhes do gestor na entidade do departamento. Por exemplo:  

![Entidade do departamento](media/storage-table-design-guide/storage-table-design-IMAGE17.png)

Com entidades de departamento armazenadas com estas propriedades, você pode agora recuperar todos os detalhes que você precisa sobre um departamento usando uma consulta de ponto.  

### <a name="issues-and-considerations"></a>Problemas e considerações
Na altura de decidir como implementar este padrão, considere os seguintes pontos:  

* Há algumas despesas associadas ao armazenamento de alguns dados duas vezes. O benefício de desempenho (resultante de menos pedidos ao serviço de armazenamento) normalmente supera o aumento marginal dos custos de armazenamento (e este custo é parcialmente compensado por uma redução do número de transações que você precisa para obter os detalhes de um departamento).  
* Deve manter a consistência das duas entidades que armazenam informação sobre os gestores. Pode lidar com a questão da consistência utilizando EGTs para atualizar várias entidades numa única transação atómica: neste caso, a entidade de departamento e a entidade de empregados do gestor de departamentos são armazenadas na mesma divisão.  

### <a name="when-to-use-this-pattern"></a>Quando utilizar este padrão
Use este padrão quando precisar de procurar informações relacionadas. Este padrão reduz o número de consultas que o seu cliente deve fazer para recuperar os dados que necessita.  

### <a name="related-patterns-and-guidance"></a>Padrões e orientações relacionados
Os padrões e orientações que se seguem podem também ser relevantes ao implementar este padrão:  

* [Padrão de chave composto](#compound-key-pattern)  
* Transações do Grupo entidade  
* [Trabalhar com tipos de entidades heterogéneas](#working-with-heterogeneous-entity-types)

## <a name="compound-key-pattern"></a>Padrão de chave composto
Utilize valores compostos **do RowKey** para permitir que um cliente procure dados relacionados com uma única consulta de ponto.  

### <a name="context-and-problem"></a>Contexto e problema
Numa base de dados relacional, é natural utilizar juntas em consultas para devolver ao cliente peças de dados relacionadas numa única consulta. Por exemplo, pode usar o ID do empregado para procurar uma lista de entidades relacionadas que contêm dados de desempenho e revisão para esse funcionário.  

Assuma que está a armazenar entidades de empregados no serviço tabela utilizando a seguinte estrutura:  

![Estrutura da entidade dos colaboradores](media/storage-table-design-guide/storage-table-design-IMAGE18.png)

Também precisa de armazenar dados históricos relativos a avaliações e desempenho para cada ano que o colaborador tenha trabalhado para a sua organização e precisa de ter acesso a esta informação por ano. Uma opção é criar outra tabela que armazena entidades com a seguinte estrutura:  

![Estrutura alternativa da entidade dos colaboradores](media/storage-table-design-guide/storage-table-design-IMAGE19.png)

Note que com esta abordagem poderá decidir duplicar algumas informações (como o primeiro nome e o apelido) na nova entidade para que possa recuperar os seus dados com um único pedido. No entanto, não é possível manter uma forte consistência porque não pode utilizar um EGT para atualizar as duas entidades atomicamente.  

### <a name="solution"></a>Solução
Guarde um novo tipo de entidade na sua tabela original utilizando entidades com a seguinte estrutura:  

![Solução para a estrutura da entidade dos colaboradores](media/storage-table-design-guide/storage-table-design-IMAGE20.png)

Note como o **RowKey** é agora uma chave composta composta composta pelo ID do empregado e o ano dos dados de revisão que lhe permite recuperar o desempenho do colaborador e rever os dados com um único pedido para uma única entidade.  

O exemplo a seguir descreve como pode recuperar todos os dados de revisão para um determinado empregado (como o empregado 000123 no departamento de Vendas):  

$filter=(PartitionKey eq 'Sales') e (RowKey ge 'empid_000123') e (RowKey lt '000123_2012')&$select=RowKey,Manager Rating,Peer Rating,Comentários  

### <a name="issues-and-considerations"></a>Problemas e considerações
Na altura de decidir como implementar este padrão, considere os seguintes pontos:  

* Deve utilizar um carácter separador adequado que facilite a análise do valor **RowKey:** por exemplo, **000123_2012**.  
* Também está a armazenar esta entidade na mesma divisão que outras entidades que contêm dados relacionados para o mesmo colaborador, o que significa que pode usar EGTs para manter uma forte consistência.
* Deve considerar a frequência com que irá consultar os dados para determinar se este padrão é apropriado.  Por exemplo, se aceder aos dados de revisão com pouca frequência e os principais dados dos colaboradores muitas vezes deverá mantê-los como entidades separadas.  

### <a name="when-to-use-this-pattern"></a>Quando utilizar este padrão
Use este padrão quando precisar de armazenar uma ou mais entidades relacionadas que consulta com frequência.  

### <a name="related-patterns-and-guidance"></a>Padrões e orientações relacionados
Os padrões e orientações que se seguem podem também ser relevantes ao implementar este padrão:  

* Transações do Grupo entidade  
* [Trabalhar com tipos de entidades heterogéneas](#working-with-heterogeneous-entity-types)  
* [Padrão de transações eventualmente consistente](#eventually-consistent-transactions-pattern)  

## <a name="log-tail-pattern"></a>Padrão de cauda de log
Recupere as entidades *n* mais recentemente adicionadas a uma partição utilizando um valor **RowKey** que classifica em ordem de data e hora inversa.  

### <a name="context-and-problem"></a>Contexto e problema
Um requisito comum é capaz de recuperar as entidades mais recentemente criadas, por exemplo, os 10 pedidos de despesas mais recentes apresentados por um trabalhador. As consultas de tabela suportam uma operação **de consulta $top** para devolver as primeiras entidades *n* de um conjunto: não existe uma operação de consulta equivalente para devolver as últimas entidades n num conjunto.  

### <a name="solution"></a>Solução
Armazenar as entidades que utilizam um **RowKey** que naturalmente classifica em ordem de data/hora inversa utilizando para que a entrada mais recente seja sempre a primeira na tabela.  

Por exemplo, para poder recuperar os 10 pedidos de despesas mais recentes apresentados por um empregado, pode utilizar um valor de tique reverso derivado da data/hora corrente. A amostra de código C# que se segue mostra uma forma de criar um valor adequado de "tiques invertidos" para um **RowKey** que se separa do mais recente ao mais antigo:  

`string invertedTicks = string.Format("{0:D19}", DateTime.MaxValue.Ticks - DateTime.UtcNow.Ticks);`  

Pode voltar ao valor da hora da data utilizando o seguinte código:  

`DateTime dt = new DateTime(DateTime.MaxValue.Ticks - Int64.Parse(invertedTicks));`  

A consulta da mesa é assim:  

`https://myaccount.table.core.windows.net/EmployeeExpense(PartitionKey='empid')?$top=10`  

### <a name="issues-and-considerations"></a>Problemas e considerações
Na altura de decidir como implementar este padrão, considere os seguintes pontos:  

* Deve remar o valor do tique-taque invertido com os principais zeros para garantir que o valor da corda se classifica como esperado.  
* Deve estar ciente dos alvos de escalabilidade ao nível de uma partição. Tenha cuidado para não criar divisórias de ponto quente.  

### <a name="when-to-use-this-pattern"></a>Quando utilizar este padrão
Utilize este padrão quando precisar de aceder a entidades em ordem de data/hora inversa ou quando precisar de aceder às entidades mais recentemente adicionadas.  

### <a name="related-patterns-and-guidance"></a>Padrões e orientações relacionados
Os padrões e orientações que se seguem podem também ser relevantes ao implementar este padrão:  

* [Prepend / apêndio anti-padrão](#prepend-append-anti-pattern)  
* [Entidades de recuperação](#retrieving-entities)  

## <a name="high-volume-delete-pattern"></a>Padrão de eliminação de volume elevado
Permitir a supressão de um elevado volume de entidades armazenando todas as entidades para a supressão simultânea na sua própria tabela separada; elimina as entidades eliminando a tabela.  

### <a name="context-and-problem"></a>Contexto e problema
Muitas aplicações eliminam dados antigos que já não precisam de estar disponíveis para uma aplicação de cliente, ou que a aplicação tenha arquivado para outro suporte de armazenamento. Normalmente identifica esses dados por uma data: por exemplo, tem a obrigação de apagar registos de todos os pedidos de login com mais de 60 dias.  

Um projeto possível é utilizar a data e a hora do pedido de login no **RowKey:**  

![Data e hora da tentativa de login](media/storage-table-design-guide/storage-table-design-IMAGE21.png)

Esta abordagem evita hotspots de partição porque a aplicação pode inserir e eliminar entidades de login para cada utilizador numa divisória separada. No entanto, esta abordagem pode ser dispendiosa e morosa se tiver um grande número de entidades, porque primeiro é necessário fazer uma verificação de tabela para identificar todas as entidades para apagar, e depois deve eliminar cada entidade antiga. Pode reduzir o número de viagens de ida e volta ao servidor necessárias para eliminar as entidades antigas, desemargando vários pedidos de eliminação em EGTs.  

### <a name="solution"></a>Solução
Utilize uma tabela separada para cada dia de tentativas de login. Você pode usar o design da entidade acima para evitar hotspots quando você está inserindo entidades, e excluir entidades antigas é agora simplesmente uma questão de apagar uma mesa todos os dias (uma única operação de armazenamento) em vez de encontrar e apagar centenas e milhares de entidades individuais de login todos os dias.  

### <a name="issues-and-considerations"></a>Problemas e considerações
Na altura de decidir como implementar este padrão, considere os seguintes pontos:  

* O seu design suporta outras formas de a sua aplicação utilizar os dados, tais como procurar entidades específicas, ligar-se a outros dados ou gerar informação agregada?  
* O seu design evita pontos quentes quando está a inserir novas entidades?  
* Espere um atraso se quiser reutilizar o mesmo nome de mesa depois de o apagar. É melhor usar sempre nomes de mesa únicos.  
* Espere algumas acelerações quando utilizar pela primeira vez uma nova tabela enquanto o serviço Table aprende os padrões de acesso e distribui as divisórias através dos nós. Deve considerar a frequência com que necessita para criar novas tabelas.  

### <a name="when-to-use-this-pattern"></a>Quando utilizar este padrão
Use este padrão quando tiver um grande volume de entidades que deve apagar ao mesmo tempo.  

### <a name="related-patterns-and-guidance"></a>Padrões e orientações relacionados
Os padrões e orientações que se seguem podem também ser relevantes ao implementar este padrão:  

* Transações do Grupo entidade
* [Modificar entidades](#modifying-entities)  

## <a name="data-series-pattern"></a>Padrão da série de dados
Armazenar séries de dados completas numa única entidade para minimizar o número de pedidos que es faz.  

### <a name="context-and-problem"></a>Contexto e problema
Um cenário comum é uma aplicação para armazenar uma série de dados que normalmente precisa para recuperar tudo de uma vez. Por exemplo, a sua aplicação pode registar quantas mensagens IM cada funcionário envia a cada hora e, em seguida, usar esta informação para traçar quantas mensagens cada utilizador enviou nas 24 horas anteriores. Um dos projetos poderá ser armazenar 24 entidades para cada colaborador:  

![Armazenar 24 entidades para cada colaborador](media/storage-table-design-guide/storage-table-design-IMAGE22.png)

Com este design, pode localizar e atualizar facilmente a entidade para atualizar para cada colaborador sempre que a aplicação precisar de atualizar o valor da contagem de mensagens. No entanto, para recuperar a informação para traçar um gráfico da atividade nas 24 horas anteriores, deve recuperar 24 entidades.  

### <a name="solution"></a>Solução
Utilize o seguinte design com uma propriedade separada para armazenar a contagem de mensagens para cada hora:  

![Entidade de estatísticas de mensagens](media/storage-table-design-guide/storage-table-design-IMAGE23.png)

Com este design, pode utilizar uma operação de fusão para atualizar a contagem de mensagens para um empregado durante uma hora específica. Agora, pode recuperar toda a informação que precisa para traçar o gráfico usando um pedido para uma única entidade.  

### <a name="issues-and-considerations"></a>Problemas e considerações
Na altura de decidir como implementar este padrão, considere os seguintes pontos:  

* Se a sua série completa de dados não se encaixar numa única entidade (uma entidade pode ter até 252 propriedades), utilize uma loja de dados alternativa, como uma bolha.  
* Se tiver vários clientes a atualizar uma entidade simultaneamente, terá de utilizar o **ETag** para implementar uma concordância otimista. Se tiver muitos clientes, poderá sentir uma grande contenção.  

### <a name="when-to-use-this-pattern"></a>Quando utilizar este padrão
Utilize este padrão quando necessitar de atualizar e recuperar uma série de dados associada a uma entidade individual.  

### <a name="related-patterns-and-guidance"></a>Padrões e orientações relacionados
Os padrões e orientações que se seguem podem também ser relevantes ao implementar este padrão:  

* [Padrão de grandes entidades](#large-entities-pattern)  
* [Fundir ou substituir](#merge-or-replace)  
* [Padrão de transações eventualmente consistente](#eventually-consistent-transactions-pattern) (se estiver a armazenar a série de dados numa bolha)  

## <a name="wide-entities-pattern"></a>Padrão de entidades amplas
Utilize múltiplas entidades físicas para armazenar entidades lógicas com mais de 252 propriedades.  

### <a name="context-and-problem"></a>Contexto e problema
Uma entidade individual não pode ter mais de 252 propriedades (excluindo as propriedades obrigatórias do sistema) e não pode armazenar mais de 1 MB de dados no total. Numa base de dados relacional, normalmente, contornaria qualquer limite do tamanho de uma linha adicionando uma nova tabela e aplicando uma relação de 1 a 1 entre eles.  

### <a name="solution"></a>Solução
Utilizando o serviço Table, pode armazenar várias entidades para representar um único objeto de negócio grande com mais de 252 propriedades. Por exemplo, se pretender armazenar uma contagem do número de mensagens IM enviadas por cada colaborador nos últimos 365 dias, poderá utilizar o seguinte design que utiliza duas entidades com esquemas diferentes:  

![Múltiplas entidades](media/storage-table-design-guide/storage-table-design-IMAGE24.png)

Se precisar de fazer uma alteração que exija atualizar ambas as entidades para mantê-las sincronizadas entre si, pode utilizar um EGT. Caso contrário, pode utilizar uma única operação de fusão para atualizar a contagem de mensagens para um dia específico. Para recuperar todos os dados para um funcionário individual deve recuperar ambas as entidades, o que pode fazer com dois pedidos eficientes que utilizam tanto uma **PartitionKey** como um valor **RowKey.**  

### <a name="issues-and-considerations"></a>Problemas e considerações
Na altura de decidir como implementar este padrão, considere os seguintes pontos:  

* Recuperar uma entidade lógica completa envolve pelo menos duas transações de armazenamento: uma para recuperar cada entidade física.  

### <a name="when-to-use-this-pattern"></a>Quando utilizar este padrão
Utilize este padrão quando necessário para armazenar entidades cujo tamanho ou número de propriedades exceda os limites para uma entidade individual no serviço Tabela.  

### <a name="related-patterns-and-guidance"></a>Padrões e orientações relacionados
Os padrões e orientações que se seguem podem também ser relevantes ao implementar este padrão:  

* Transações do Grupo entidade
* [Fundir ou substituir](#merge-or-replace)

## <a name="large-entities-pattern"></a>Padrão de grandes entidades
Use o armazenamento de bolhas para armazenar grandes valores de propriedade.  

### <a name="context-and-problem"></a>Contexto e problema
Uma entidade individual não pode armazenar mais de 1 MB de dados no total. Se um ou vários dos valores da loja das suas propriedades que fazem com que o tamanho total da sua entidade exceda este valor, não poderá armazenar toda a entidade no serviço Tabela.  

### <a name="solution"></a>Solução
Se a sua entidade exceder 1 MB de tamanho porque uma ou mais propriedades contêm uma grande quantidade de dados, pode armazenar dados no serviço Blob e, em seguida, armazenar o endereço da bolha numa propriedade da entidade. Por exemplo, pode armazenar a foto de um empregado no armazenamento de bolhas e armazenar um link para a foto na propriedade **photo** da sua entidade colaboradora:  

![Propriedade fotográfica](media/storage-table-design-guide/storage-table-design-IMAGE25.png)

### <a name="issues-and-considerations"></a>Problemas e considerações
Na altura de decidir como implementar este padrão, considere os seguintes pontos:  

* Para manter uma eventual consistência entre a entidade no serviço Tabela e os dados no serviço Blob, utilize o [padrão de transações eventualmente consistente](#eventually-consistent-transactions-pattern) para manter as suas entidades.
* A recuperação de uma entidade completa envolve pelo menos duas transações de armazenamento: uma para recuperar a entidade e outra para recuperar os dados do blob.  

### <a name="when-to-use-this-pattern"></a>Quando utilizar este padrão
Utilize este padrão quando necessitar de armazenar entidades cujo tamanho exceda os limites para uma entidade individual no serviço Tabela.  

### <a name="related-patterns-and-guidance"></a>Padrões e orientações relacionados
Os padrões e orientações que se seguem podem também ser relevantes ao implementar este padrão:  

* [Padrão de transações eventualmente consistente](#eventually-consistent-transactions-pattern)  
* [Padrão de entidades amplas](#wide-entities-pattern)

<a name="prepend-append-anti-pattern"></a>

## <a name="prependappend-anti-pattern"></a>Prepend/apêndice anti-padrão
Aumente a escalabilidade quando tiver um grande volume de inserções espalhando as inserções através de múltiplas divisórias.  

### <a name="context-and-problem"></a>Contexto e problema
A premissão ou a anexação de entidades às suas entidades armazenadas normalmente resulta na adição de novas entidades à primeira ou última partição de uma sequência de divisórias. Neste caso, todos os inserções a qualquer momento estão a ocorrer na mesma partição, criando um hotspot que impede o serviço de mesa de inserirs de equilíbrio de carga em vários nós, e possivelmente fazendo com que a sua aplicação atinja os alvos de escalabilidade para a partição. Por exemplo, se tiver uma aplicação que regista o acesso à rede e ao acesso aos recursos pelos colaboradores, então uma estrutura de entidade, tal como mostrada abaixo, poderá resultar na divisão da hora atual se o volume de transações atingir o objetivo de escalabilidade de uma partição individual:  

![Estrutura de entidade](media/storage-table-design-guide/storage-table-design-IMAGE26.png)

### <a name="solution"></a>Solução
A seguinte estrutura de entidade alternativa evita um hotspot em qualquer partição específica à medida que a aplicação regista eventos:  

![Estrutura de entidades alternativas](media/storage-table-design-guide/storage-table-design-IMAGE27.png)

Note com este exemplo como tanto o **PartitionKey** como o **RowKey** são teclas compostas. O **PartitionKey** usa o departamento e o ID do funcionário para distribuir o registo através de várias divisórias.  

### <a name="issues-and-considerations"></a>Problemas e considerações
Na altura de decidir como implementar este padrão, considere os seguintes pontos:  

* A estrutura de chave alternativa que evita a criação de divisórias quentes em inserções suporta de forma eficiente as consultas que a sua aplicação do cliente faz?  
* O seu volume previsto de transações significa que é provável que atinja os objetivos de escalabilidade para uma divisória individual e seja estrangulado pelo serviço de armazenamento?  

### <a name="when-to-use-this-pattern"></a>Quando utilizar este padrão
Evite o anti-padrão pré-estotome quando o seu volume de transações é suscetível de resultar em estrangulamento pelo serviço de armazenamento quando aceder a uma divisória quente.  

### <a name="related-patterns-and-guidance"></a>Padrões e orientações relacionados
Os padrões e orientações que se seguem podem também ser relevantes ao implementar este padrão:  

* [Padrão de chave composto](#compound-key-pattern)  
* [Padrão de cauda de log](#log-tail-pattern)  
* [Modificar entidades](#modifying-entities)  

## <a name="log-data-anti-pattern"></a>Registar dados anti-padrão
Normalmente, deve utilizar o serviço Blob em vez do serviço Table para armazenar dados de registo.  

### <a name="context-and-problem"></a>Contexto e problema
Um caso de utilização comum para os dados de registo é recuperar uma seleção de entradas de registo para um intervalo específico de data/hora: por exemplo, pretende encontrar todos os erros e mensagens críticas que a sua aplicação registou entre as 15:04 e as 15:06 numa data específica. Não pretende utilizar a data e a hora da mensagem de registo para determinar a partição a que guarda entidades de registo: que resulta numa partição quente porque, a qualquer momento, todas as entidades de registo partilharão o mesmo valor **PartitionKey** (ver secção [Prepend/append anti-padrão).](#prepend-append-anti-pattern) Por exemplo, o esquema de entidade a seguir para uma mensagem de registo resulta numa partição quente porque a aplicação escreve todas as mensagens de registo para a partição para a data e hora em atuais:  

![Entidade de mensagem de registo](media/storage-table-design-guide/storage-table-design-IMAGE28.png)

Neste exemplo, o **RowKey** inclui a data e a hora da mensagem de registo para garantir que as mensagens de registo são armazenadas ordenadas na ordem data/hora, e inclui um ID de mensagem no caso de várias mensagens de registo partilharem a mesma data e hora.  

Outra abordagem é usar uma **PartitionKey** que garanta que a aplicação escreve mensagens em várias divisórias. Por exemplo, se a origem da mensagem de registo fornecer uma forma de distribuir mensagens em muitas divisórias, poderá utilizar o seguinte esquema de entidade:  

![Entidade de mensagem de registo alternativo](media/storage-table-design-guide/storage-table-design-IMAGE29.png)

No entanto, o problema com este esquema é que para recuperar todas as mensagens de registo por um período de tempo específico deve pesquisar todas as divisórias da tabela.

### <a name="solution"></a>Solução
A secção anterior destacou o problema de tentar utilizar o serviço Table para armazenar entradas de registo e sugeriu dois desenhos, insatisfatórios. Uma solução conduziu a uma divisória quente com o risco de mau desempenho escrever mensagens de registo; a outra solução resultou num fraco desempenho de consulta devido à exigência de digitalizar todas as divisórias da tabela para recuperar as mensagens de registo por um período de tempo específico. O armazenamento blob oferece uma melhor solução para este tipo de cenário e é assim que o Azure Storage Analytics armazena os dados de registo que recolhe.  

Esta secção descreve como o Storage Analytics armazena dados de registo no armazenamento de bolhas como uma ilustração desta abordagem para armazenar dados que normalmente consulta por gama.  

Storage Analytics armazena mensagens de registo num formato delimitado em várias bolhas. O formato delimitado facilita a análise dos dados na mensagem de registo.  

O Storage Analytics utiliza uma convenção de nomeação para bolhas que lhe permite localizar a bolha (ou bolhas) que contêm as mensagens de registo pelas quais está a pesquisar. Por exemplo, uma bolha chamada "queue/2014/07/31/1800/000001.log" contém mensagens de registo que dizem respeito ao serviço de fila para a hora a partir das 18:00 de 31 de julho de 2014. O "000001" indica que este é o primeiro ficheiro de registo deste período. O Storage Analytics também regista os termos tempotamos das primeira e últimas mensagens de registo armazenadas no ficheiro como parte dos metadados da bolha. A API para armazenamento de bolhas permite localizar bolhas num recipiente com base num prefixo de nome: para localizar todas as bolhas que contêm dados de registo de fila para a hora a partir das 18:00, pode utilizar o prefixo "fila/2014/07/31/1800".  

Os buffers de Armazenamento Analytics registam as mensagens internamente e, em seguida, atualiza periodicamente a bolha apropriada ou cria uma nova com o mais recente lote de entradas de registo. Isto reduz o número de escritos que deve executar para o serviço blob.  

Se estiver a implementar uma solução semelhante na sua própria aplicação, deve considerar como gerir a compensação entre a fiabilidade (escrever todas as entradas de registo para o armazenamento de bolhas como acontece) e o custo e escalabilidade (atualizações de tamponamento na sua aplicação e escrevê-las para armazenamento de bolhas em lotes).  

### <a name="issues-and-considerations"></a>Problemas e considerações
Considere os seguintes pontos ao decidir como armazenar dados de registo:  

* Se criar um design de mesa que evite potenciais divisórias quentes, poderá descobrir que não pode aceder aos seus dados de registo de forma eficiente.  
* Para processar dados de registo, um cliente precisa muitas vezes de carregar muitos registos.  
* Embora os dados de registo sejam muitas vezes estruturados, o armazenamento de bolhas pode ser uma solução melhor.  

## <a name="implementation-considerations"></a>Considerações de implementação
Esta secção discute algumas das considerações a ter em conta quando implementa os padrões descritos nas secções anteriores. A maior parte desta secção utiliza exemplos escritos em C# que utilizam a Biblioteca do Cliente de Armazenamento (versão 4.3.0 no momento da escrita).  

## <a name="retrieving-entities"></a>Entidades de recuperação
Como discutido na secção Design para consulta, a consulta mais eficiente é uma consulta pontual. No entanto, em alguns cenários poderá ser necessário recuperar várias entidades. Esta secção descreve algumas abordagens comuns para a recuperação de entidades utilizando a Biblioteca do Cliente de Armazenamento.  

### <a name="executing-a-point-query-using-the-storage-client-library"></a>Execução de uma consulta de pontos utilizando a Biblioteca do Cliente de Armazenamento
A forma mais fácil de executar uma consulta de pontos é utilizar a operação da tabela **Recuperar,** como mostrado no seguinte corte de código C# que recupera uma entidade com uma **PartitionKey** de valor "Vendas" e uma **RowKey** de valor "212":  

```csharp
TableOperation retrieveOperation = TableOperation.Retrieve<EmployeeEntity>("Sales", "212");
var retrieveResult = employeeTable.Execute(retrieveOperation);
if (retrieveResult.Result != null)
{
    EmployeeEntity employee = (EmployeeEntity)retrieveResult.Result;
    ...
}  
```

Note como este exemplo espera que a entidade que recupera seja do tipo **Entidade de Empregados.**  

### <a name="retrieving-multiple-entities-using-linq"></a>Recuperar várias entidades usando o LINQ
Pode utilizar o LINQ para recuperar várias entidades do serviço table ao trabalhar com a Microsoft Azure Cosmos Table Standard Library. 

```azurecli
dotnet add package Microsoft.Azure.Cosmos.Table
```

Para que os exemplos abaixo funcionem, terá de incluir espaços de nome:

```csharp
using System.Linq;
using Microsoft.Azure.Cosmos.Table;
using Microsoft.Azure.Cosmos.Table.Queryable;
```

O employeeTable é um objeto CloudTable que implementa um método CreateQuery \<ITableEntity> () que devolve um TableQuery \<ITableEntity> . Objetos deste tipo implementam um IQueryable e permitem a utilização de expressões de consulta LINQ e sintaxe de notação de pontos.

Recuperar várias entidades e ser alcançado especificando uma consulta com uma cláusula **onde.** Para evitar uma verificação de tabela, deve incluir sempre o valor **PartitionKey** na cláusula "onde" e, se possível, o valor **RowKey** para evitar sondagens de mesa e partição. O serviço de mesa suporta um conjunto limitado de operadores de comparação (maiores do que, maiores ou iguais, menos do que, menos ou iguais, iguais e não iguais) para utilizar na cláusula em que. 

O seguinte código C# snippet encontra todos os empregados cujo apelido começa com "B" (assumindo que o **RowKey** armazena o último nome) no departamento de vendas (assumindo que a **PartitionKey** armazena o nome do departamento):  

```csharp
TableQuery<EmployeeEntity> employeeQuery = employeeTable.CreateQuery<EmployeeEntity>();
var query = (from employee in employeeQuery
            where employee.PartitionKey == "Sales" &&
            employee.RowKey.CompareTo("B") >= 0 &&
            employee.RowKey.CompareTo("C") < 0
            select employee).AsTableQuery();
            
var employees = query.Execute();  
```

Note como a consulta especifica tanto um **RowKey** como um **PartitionKey** para garantir um melhor desempenho.  

A amostra de código que se segue mostra uma funcionalidade equivalente sem utilizar a sintaxe LINQ:  

```csharp
TableQuery<EmployeeEntity> employeeQuery = 
    new TableQuery<EmployeeEntity>().Where(
        TableQuery.CombineFilters(
            TableQuery.CombineFilters(
                TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Sales"),
                TableOperators.And,
                TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.GreaterThanOrEqual, "B")),
            TableOperators.And,
            TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.LessThan, "C")));
            
var employees = employeeTable.ExecuteQuery(employeeQuery);  
```

> [!NOTE]
> A amostra aninha vários **métodos CombineFilters** para incluir as três condições de filtro.  
> 
> 

### <a name="retrieving-large-numbers-of-entities-from-a-query"></a>Recuperar um grande número de entidades a partir de uma consulta
Uma consulta ideal devolve uma entidade individual com base num valor **PartitionKey** e num valor **RowKey.** No entanto, em alguns cenários poderá ter a obrigação de devolver muitas entidades da mesma partição ou mesmo de muitas divisórias.  

Deve sempre testar totalmente o desempenho da sua aplicação nesses cenários.  

Uma consulta contra o serviço de mesa pode devolver um máximo de 1.000 entidades de uma só vez e pode executar por um máximo de cinco segundos. Se o conjunto de resultados contiver mais de 1.000 entidades, se a consulta não tiver sido concluída no prazo de cinco segundos, ou se a consulta cruzar o limite de partição, o serviço Tabela devolve um token de continuação para permitir que o pedido do cliente solicite o próximo conjunto de entidades. Para obter mais informações sobre como funcionam os tokens de continuação, consulte [Consulta Timeout e Pagination](https://msdn.microsoft.com/library/azure/dd135718.aspx).  

Se estiver a utilizar a Biblioteca do Cliente de Armazenamento, pode lidar automaticamente com fichas de continuação para si, uma vez que devolve entidades do serviço Table. A amostra de código C# que utiliza a Biblioteca do Cliente de Armazenamento lida automaticamente com fichas de continuação se o serviço de tabela os devolver numa resposta:  

```csharp
string filter = TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Sales");
TableQuery<EmployeeEntity> employeeQuery = new TableQuery<EmployeeEntity>().Where(filter);

var employees = employeeTable.ExecuteQuery(employeeQuery);
foreach (var emp in employees)
{
    // ...
}  
```

O seguinte código C# lida explicitamente com fichas de continuação:  

```csharp
string filter = TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Sales");
TableQuery<EmployeeEntity> employeeQuery = new TableQuery<EmployeeEntity>().Where(filter);

TableContinuationToken continuationToken = null;
do
{
    var employees = employeeTable.ExecuteQuerySegmented(employeeQuery, continuationToken);
    foreach (var emp in employees)
    {
        // ...
    }
    
    continuationToken = employees.ContinuationToken;
} while (continuationToken != null);  
```

Ao utilizar tokens de continuação explicitamente, pode controlar quando a sua aplicação recupera o próximo segmento de dados. Por exemplo, se a sua aplicação cliente permitir que os utilizadores a página através das entidades armazenadas numa tabela, um utilizador pode decidir não páginar através de todas as entidades recuperadas pela consulta para que a sua aplicação apenas utilize um token de continuação para recuperar o segmento seguinte quando o utilizador tiver terminado de chamar por todas as entidades do segmento atual. Esta abordagem tem vários benefícios:  

* Permite-lhe limitar a quantidade de dados a obter do serviço Table e que se move sobre a rede.  
* Permite-lhe executar IO assíncronos em .NET.  
* Permite-lhe serializar o token de continuação para armazenamento persistente para que possa continuar em caso de falha de aplicação.  

> [!NOTE]
> Um símbolo de continuação normalmente devolve um segmento contendo 1.000 entidades, embora possa ser menor. É também o caso se limitar o número de entradas que uma consulta devolve utilizando **a Take** para devolver as primeiras entidades n que correspondam aos seus critérios de procura: o serviço de mesa pode devolver um segmento contendo menos de n entidades, juntamente com um token de continuação para que possa recuperar as restantes entidades.  
> 
> 

O seguinte código C# mostra como modificar o número de entidades devolvidas dentro de um segmento:  

```csharp
employeeQuery.TakeCount = 50;  
```

### <a name="server-side-projection"></a>Projeção do lado do servidor
Uma única entidade pode ter até 255 propriedades e ter até 1 MB de tamanho. Ao consultar a tabela e recuperar entidades, pode não precisar de todas as propriedades e pode evitar a transferência de dados desnecessariamente (para ajudar a reduzir a latência e o custo). Pode utilizar a projeção do lado do servidor para transferir apenas as propriedades necessárias. O exemplo a seguir é a recuperação apenas da propriedade **Email** (juntamente com **PartitionKey,** **RowKey,** **Timestamp**e **ETag**) das entidades selecionadas pela consulta.  

```csharp
string filter = TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Sales");
List<string> columns = new List<string>() { "Email" };
TableQuery<EmployeeEntity> employeeQuery =
    new TableQuery<EmployeeEntity>().Where(filter).Select(columns);

var entities = employeeTable.ExecuteQuery(employeeQuery);
foreach (var e in entities)
{
    Console.WriteLine("RowKey: {0}, EmployeeEmail: {1}", e.RowKey, e.Email);
}  
```

Note como o valor **RowKey** está disponível, mesmo não estando incluído na lista de propriedades para recuperar.  

## <a name="modifying-entities"></a>Modificar entidades
A Biblioteca do Cliente de Armazenamento permite modificar as suas entidades armazenadas no serviço de mesa, inserindo, eliminando e atualizando entidades. Pode utilizar EGTs para lotar várias inserções, atualizar e eliminar operações em conjunto para reduzir o número de viagens de ida e volta necessárias e melhorar o desempenho da sua solução.  

As exceções lançadas quando a Biblioteca do Cliente de Armazenamento executa um EGT normalmente incluem o índice da entidade que fez com que o lote falhasse. Isto é útil quando está a depurar código que utiliza EGTs.  

Também deve considerar como o seu design afeta a forma como a sua aplicação de cliente lida com as operações de conuncy e atualização.  

### <a name="managing-concurrency"></a>Gerir a simultaneidade
Por predefinição, o serviço de mesa implementa controlos de concordância otimistas ao nível de entidades individuais para operações **de Inserção,** **Fusão**e **Eliminação,** embora seja possível um cliente forçar o serviço de mesa a contornar estas verificações. Para obter mais informações sobre como o serviço de mesa gere a concordância, consulte  [a Managing Concurrency no Microsoft Azure Storage](../../storage/common/storage-concurrency.md).  

### <a name="merge-or-replace"></a>Fundir ou substituir
O método **de substituição** da classe **TableOperation** substitui sempre a entidade completa no serviço Tabela. Se não incluir um imóvel no pedido quando esse imóvel existe na entidade armazenada, o pedido retira esse imóvel da entidade armazenada. A menos que queira remover um imóvel explicitamente de uma entidade armazenada, deve incluir todos os bens no pedido.  

Pode utilizar o método **de Fusão** da classe **TableOperation** para reduzir a quantidade de dados que envia para o serviço De Mesa quando pretende atualizar uma entidade. O método **Merge** substitui quaisquer propriedades na entidade armazenada por valores patrimoniais da entidade incluída no pedido, mas deixa intactas quaisquer propriedades na entidade armazenada que não estejam incluídas no pedido. Isto é útil se você tem grandes entidades e apenas precisa atualizar um pequeno número de propriedades em um pedido.  

> [!NOTE]
> Os métodos **de substituição** e **fusão** falham se a entidade não existir. Como alternativa, pode utilizar os métodos **InsertOrReplace** e **InsertOrMerge** que criam uma nova entidade se não existir.  
> 
> 

## <a name="working-with-heterogeneous-entity-types"></a>Trabalhar com tipos de entidades heterogéneas
O serviço Table é uma loja de mesa *sem esquemas,* o que significa que uma única mesa pode armazenar entidades de vários tipos, proporcionando grande flexibilidade no seu design. O exemplo a seguir ilustra uma tabela que armazena tanto entidades de empregados como de departamento:  

<table>
<tr>
<th>PartitionKey</th>
<th>RowKey</th>
<th>Timestamp</th>
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
<th>E-mail</th>
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
<th>E-mail</th>
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
<th>DepartmentName</th>
<th>Contagem de Funcionários</th>
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
<th>E-mail</th>
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

Cada entidade ainda deve ter **valores PartitionKey,** **RowKey**e **Timestamp,** mas pode ter qualquer conjunto de propriedades. Além disso, não há nada que indique o tipo de entidade a menos que escolha armazenar essa informação em algum lugar. Existem duas opções para identificar o tipo de entidade:  

* Prepare o tipo de entidade para o **RowKey** (ou possivelmente o **PartitionKey).** Por exemplo, **EMPLOYEE_000123** ou **DEPARTMENT_SALES** como valores **do RowKey.**  
* Utilize uma propriedade separada para registar o tipo de entidade como mostrado na tabela abaixo.  

<table>
<tr>
<th>PartitionKey</th>
<th>RowKey</th>
<th>Timestamp</th>
<th></th>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td>
<table>
<tr>
<th>EntityType</th>
<th>FirstName</th>
<th>LastName</th>
<th>Idade</th>
<th>E-mail</th>
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
<th>EntityType</th>
<th>FirstName</th>
<th>LastName</th>
<th>Idade</th>
<th>E-mail</th>
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
<th>EntityType</th>
<th>DepartmentName</th>
<th>Contagem de Funcionários</th>
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
<th>EntityType</th>
<th>FirstName</th>
<th>LastName</th>
<th>Idade</th>
<th>E-mail</th>
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

A primeira opção, pré-pendente do tipo de entidade para o **RowKey,** é útil se houver a possibilidade de duas entidades de diferentes tipos poderem ter o mesmo valor-chave. Também agru se junta entidades do mesmo tipo na partição.  

As técnicas discutidas nesta secção são especialmente relevantes para a discussão [As relações de herança](table-storage-design-modeling.md#inheritance-relationships) mais cedo neste guia no artigo [Relacionamentos de Modelação.](table-storage-design-modeling.md)  

> [!NOTE]
> Deve considerar a inclusão de um número de versão no valor do tipo de entidade para permitir que as aplicações do cliente evoluam objetos POCO e trabalhem com diferentes versões.  
> 
> 

O restante desta secção descreve algumas das funcionalidades na Biblioteca do Cliente de Armazenamento que facilitam o funcionamento com vários tipos de entidades na mesma tabela.  

### <a name="retrieving-heterogeneous-entity-types"></a>Recuperação de tipos de entidades heterogéneas
Se estiver a utilizar a Biblioteca do Cliente de Armazenamento, tem três opções para trabalhar com vários tipos de entidades.  

Se conhecer o tipo de entidade armazenada com um determinado valor **RowKey** e **PartitionKey,** então pode especificar o tipo de entidade quando recuperar a entidade como mostrado nos dois exemplos anteriores que recuperam entidades de tipo **Entidade de Empregados**: Executar uma consulta de [ponto utilizando a Biblioteca do Cliente de Armazenamento](#executing-a-point-query-using-the-storage-client-library) e Recuperar [várias entidades utilizando o LINQ](#retrieving-multiple-entities-using-linq).  

A segunda opção é utilizar o tipo **DynamicTableEntity** (um saco de propriedade) em vez de um tipo de entidade POCO de betão (esta opção também pode melhorar o desempenho porque não há necessidade de serializar e desseializar a entidade para os tipos .NET). O código C# seguinte potencialmente recupera várias entidades de diferentes tipos da tabela, mas devolve todas as entidades como instâncias **DynamicTableEntity.** Em seguida, utiliza a propriedade **EntityType** para determinar o tipo de cada entidade:  

```csharp
string filter =
    TableQuery.CombineFilters(
        TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Sales"),
        TableOperators.And,
        TableQuery.CombineFilters(
            TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.GreaterThanOrEqual, "B"),
            TableOperators.And,
            TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.LessThan, "F")));
        
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
            // use entityTypeProperty, RowKey, PartitionKey, Etag, and Timestamp
        }
    }
}  
```

Para recuperar outras propriedades, deve utilizar o método **TryGetValue** na propriedade **Propriedades** da classe **DynamicTableEntity.**  

Uma terceira opção é combinar usando o tipo **DynamicTableEntity** e uma instância **EntityResolver.** Isto permite-lhe resolver vários tipos de POCO na mesma consulta. Neste exemplo, o delegado da **EntityResolver** está a usar a propriedade **EntityType** para distinguir entre os dois tipos de entidade que a consulta devolve. O método **Resolve** utiliza o delegado **resolver** para resolver instâncias **da DynamicTableEntity** em instâncias **da Entidade de Tabela.**  

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
    else 
    {
        throw new ArgumentException("Unrecognized entity", "props");
    }

    resolvedEntity.PartitionKey = pk;
    resolvedEntity.RowKey = rk;
    resolvedEntity.Timestamp = ts;
    resolvedEntity.ETag = etag;
    resolvedEntity.ReadEntity(props, null);
    return resolvedEntity;
};

string filter = TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Sales");
        
TableQuery<DynamicTableEntity> entityQuery = new TableQuery<DynamicTableEntity>().Where(filter);

var entities = employeeTable.ExecuteQuery(entityQuery, resolver);
foreach (var e in entities)
{
    if (e is DepartmentEntity)
    {
        // ...
    }
    else if (e is EmployeeEntity)
    {
        // ...
    }
}  
```

### <a name="modifying-heterogeneous-entity-types"></a>Modificação de tipos de entidades heterogéneas
Não precisa de saber o tipo de entidade para o apagar, e sabe sempre o tipo de entidade quando a insere. No entanto, pode utilizar o tipo **DynamicTableEntity** para atualizar uma entidade sem conhecer o seu tipo e sem utilizar uma classe de entidade POCO. A amostra de código que se segue recupera uma única entidade e verifica a existência da propriedade **EmployeeCount** antes de a atualizar.  

```csharp
TableResult result = employeeTable.Execute(TableOperation.Retrieve(partitionKey, rowKey));
DynamicTableEntity department = (DynamicTableEntity)result.Result;

EntityProperty countProperty;
if (!department.Properties.TryGetValue("EmployeeCount", out countProperty))
{
    throw new InvalidOperationException("Invalid entity, EmployeeCount property not found.");
}

countProperty.Int32Value += 1;
employeeTable.Execute(TableOperation.Merge(department));
```

## <a name="controlling-access-with-shared-access-signatures"></a>Controlo do acesso com assinaturas de acesso partilhado
Pode utilizar fichas de Assinatura de Acesso Partilhado (SAS) para permitir que as aplicações do cliente modifiquem (e questionem) entidades de mesa sem a necessidade de incluir a chave da sua conta de armazenamento no seu código. Normalmente, existem três principais benefícios para a utilização de SAS na sua aplicação:  

* Não é necessário distribuir a chave da sua conta de armazenamento para uma plataforma insegura (como um dispositivo móvel) para permitir que esse dispositivo aceda e modifique entidades no serviço Tabela.  
* Pode descarregar parte do trabalho que as funções web e trabalhadora desempenham na gestão das suas entidades para dispositivos clientes, como computadores de utilizador final e dispositivos móveis.  
* Pode atribuir um conjunto limitado de permissões a um cliente (como permitir o acesso apenas à leitura a recursos específicos).  

Para obter mais informações sobre a utilização de fichas SAS com o serviço table, consulte [utilizar assinaturas de acesso partilhado (SAS)](../../storage/common/storage-sas-overview.md).  

No entanto, ainda deve gerar os tokens SAS que concedem uma aplicação de cliente às entidades do serviço de mesa: deve fazê-lo num ambiente que tenha acesso seguro às chaves da sua conta de armazenamento. Normalmente, usa uma função web ou trabalhadora para gerar os tokens SAS e entregá-los às aplicações do cliente que precisam de acesso às suas entidades. Como ainda existe uma sobrecarga envolvida na geração e entrega de fichas SAS aos clientes, deve considerar a melhor forma de reduzir esta sobrecarga, especialmente em cenários de grande volume.  

É possível gerar um token SAS que dá acesso a um subconjunto das entidades numa tabela. Por padrão, cria um token SAS para uma tabela inteira, mas também é possível especificar que o token SAS concede acesso a uma gama de valores **PartitionKey,** ou a uma gama de valores **PartitionKey** e **RowKey.** Pode optar por gerar fichas SAS para utilizadores individuais do seu sistema de forma a permitir que o token SAS de cada utilizador apenas lhes permita aceder às suas próprias entidades no serviço de mesa.  

## <a name="asynchronous-and-parallel-operations"></a>Operações assíncronos e paralelas
Desde que esteja a difundir os seus pedidos através de múltiplas divisórias, pode melhorar a produção e a capacidade de resposta do cliente utilizando consultas assíncronos ou paralelas.
Por exemplo, pode ter duas ou mais instâncias de trabalho a aceder em paralelo às suas tabelas. Pode ter funções individuais de trabalhadores responsáveis por determinados conjuntos de divisórias, ou simplesmente ter múltiplas instâncias de papel dos trabalhadores, cada uma capaz de aceder a todas as divisórias numa tabela.  

Dentro de uma instância do cliente, você pode melhorar a produção executando operações de armazenamento assíncroneamente. A Biblioteca do Cliente de Armazenamento facilita a escrita de consultas e modificações assíncronos. Por exemplo, pode começar pelo método sincronizado que recupera todas as entidades numa partição, como mostra o seguinte código C#:  

```csharp
private static void ManyEntitiesQuery(CloudTable employeeTable, string department)
{
    string filter = TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, department);
    TableQuery<EmployeeEntity> employeeQuery = new TableQuery<EmployeeEntity>().Where(filter);

    TableContinuationToken continuationToken = null;
    do
    {
        var employees = employeeTable.ExecuteQuerySegmented(employeeQuery, continuationToken);
        foreach (var emp in employees)
        {
            // ...
        }
        
        continuationToken = employees.ContinuationToken;
    } while (continuationToken != null);
}  
```

Pode modificar facilmente este código de modo a que a consulta seja assíncronea da seguinte forma:  

```csharp
private static async Task ManyEntitiesQueryAsync(CloudTable employeeTable, string department)
{
    string filter = TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, department);
    TableQuery<EmployeeEntity> employeeQuery = new TableQuery<EmployeeEntity>().Where(filter);
    
    TableContinuationToken continuationToken = null;
    do
    {
        var employees = await employeeTable.ExecuteQuerySegmentedAsync(employeeQuery, continuationToken);
        foreach (var emp in employees)
        {
            // ...
        }
    
        continuationToken = employees.ContinuationToken;
    } while (continuationToken != null);
}  
```

Neste exemplo assíncronos, pode ver as seguintes alterações a partir da versão sincronizada:  

* A assinatura do método agora inclui o **modificador assínc** e devolve uma instância **de Tarefa.**  
* Em vez de chamar o método **ExecuteSegmented** para obter resultados, o método agora chama o método **ExecuteSegmentedAsync** e usa o modificador **de espera** para obter resultados assíncronosamente.  

A aplicação do cliente pode ligar para este método várias vezes (com valores diferentes para o parâmetro do **departamento),** e cada consulta será executada em um fio separado.  

Não existe uma versão assíncrona do método **Executar** na classe **TableQuery** porque a interface **IEnumerable** não suporta a enumeração assíncronus.  

Também pode inserir, atualizar e eliminar asincronizando asinerróneas. O exemplo C# a seguir mostra um método simples e sincronizado para inserir ou substituir uma entidade do empregado:  

```csharp
private static void SimpleEmployeeUpsert(
    CloudTable employeeTable,
    EmployeeEntity employee)
{
    TableResult result = employeeTable.Execute(TableOperation.InsertOrReplace(employee));
    Console.WriteLine("HTTP Status: {0}", result.HttpStatusCode);
}  
```

Pode modificar facilmente este código de modo a que a atualização seja assíncronea da seguinte forma:  

```csharp
private static async Task SimpleEmployeeUpsertAsync(
    CloudTable employeeTable,
    EmployeeEntity employee)
{
    TableResult result = await employeeTable.ExecuteAsync(TableOperation.InsertOrReplace(employee));
    Console.WriteLine("HTTP Status: {0}", result.HttpStatusCode);
}  
```

Neste exemplo assíncronos, pode ver as seguintes alterações a partir da versão sincronizada:  

* A assinatura do método agora inclui o **modificador assínc** e devolve uma instância **de Tarefa.**  
* Em vez de chamar o método **Executar** para atualizar a entidade, o método agora chama o método **ExecutarAsync** e usa o modificador **de espera** para obter resultados assíncronosamente.  

A aplicação do cliente pode chamar vários métodos assíncronos como este, e cada invocação de método será executada em um fio separado.  

## <a name="next-steps"></a>Passos seguintes

- [Modelar relações](table-storage-design-modeling.md)
- [Design das consultas](table-storage-design-for-query.md)
- [Encriptar dados da tabela](table-storage-design-encrypt-data.md)
- [Design da modificação de dados](table-storage-design-for-modification.md)
