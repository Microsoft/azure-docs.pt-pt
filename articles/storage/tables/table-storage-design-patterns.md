---
title: Padrões de design de mesa de armazenamento azure [ Microsoft Docs
description: Utilize padrões para soluções de serviço de mesa Azure.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 04/08/2019
ms.author: tamram
ms.subservice: tables
ms.openlocfilehash: 5478163a6103bcc84b4f3608d7513c6e7cb11c01
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79529344"
---
# <a name="table-design-patterns"></a>Padrões de design da tabela
Este artigo descreve alguns padrões adequados para uso com soluções de serviço de mesa. Além disso, você verá como você pode praticamente abordar algumas das questões e trocas discutidas em outros artigos de design de armazenamento de mesa. O diagrama seguinte resume as relações entre os diferentes padrões:  

![para procurar dados relacionados](media/storage-table-design-guide/storage-table-design-IMAGE05.png)


O mapa de padrões acima destaca algumas relações entre padrões (azul) e anti-padrões (laranja) que são documentados neste guia. Há muitos outros padrões que valem a pena considerar. Por exemplo, um dos cenários-chave para o Serviço de Mesa é utilizar o Padrão de [Visualização Materializada](https://msdn.microsoft.com/library/azure/dn589782.aspx) a partir do padrão de segregação de responsabilidade de consulta de [comando (CQRS).](https://msdn.microsoft.com/library/azure/jj554200.aspx)  

## <a name="intra-partition-secondary-index-pattern"></a>Padrão de índice secundário intra-partição
Guarde várias cópias de cada entidade utilizando diferentes valores **RowKey** (na mesma divisória) para permitir procurações rápidas e eficientes e ordens de classificação alternativas utilizando diferentes valores **RowKey.** As atualizações entre cópias podem ser mantidas consistentes utilizando EGTs.  

### <a name="context-and-problem"></a>Contexto e problema
O serviço de tabela indexa automaticamente as entidades utilizando os valores **PartitionKey** e **RowKey.** Isto permite que uma aplicação do cliente recupere uma entidade utilizando eficientemente estes valores. Por exemplo, utilizando a estrutura de tabela apresentada abaixo, uma aplicação de cliente pode usar uma consulta pontual para recuperar uma entidade de empregado individual utilizando o nome do departamento e o ID do empregado (os valores **PartitionKey** e **RowKey).** Um cliente também pode recuperar entidades ordenadas por identificação de empregado dentro de cada departamento.

![Imagem06](media/storage-table-design-guide/storage-table-design-IMAGE06.png)

Se também pretende encontrar uma entidade colaboradora com base no valor de outra propriedade, como o endereço de e-mail, deve utilizar uma verificação de divisórias menos eficiente para encontrar uma correspondência. Isto porque o serviço de mesa não fornece índices secundários. Além disso, não existe opção de solicitar uma lista de colaboradores classificados numa ordem diferente da encomenda **RowKey.**  

### <a name="solution"></a>Solução
Para contornar a falta de índices secundários, pode armazenar várias cópias de cada entidade com cada cópia utilizando um valor **RowKey** diferente. Se armazenar uma entidade com as estruturas apresentadas abaixo, pode recuperar eficientemente as entidades dos colaboradores com base no endereço de e-mail ou no ID do empregado. Os valores prefixos para o **RowKey**, "empid_" e "email_" permitem-lhe consultar um único empregado ou uma série de colaboradores utilizando uma série de endereços de e-mail ou iDs de empregados.  

![Entidades colaboradoras](media/storage-table-design-guide/storage-table-design-IMAGE07.png)

Os dois critérios de filtro seguintes (um olhando para cima por id do empregado e outro olhando para cima por endereço de e-mail) ambos especificam consultas de ponto:  

* $filter=(PartitionKey eq 'Sales') e (RowKey eq 'empid_000223')  
* $filter=(PartitionKey eq 'Sales') e (RowKey eq')email_jonesj@contoso.com  

Se consultar uma série de entidades de empregados, pode especificar uma gama ordenada na ordem de identificação dos empregados, ou uma gama ordenada na ordem de endereço de e-mail consultando para entidades com o prefixo apropriado no **RowKey**.  

* Para encontrar todos os colaboradores do departamento de Vendas com identificação de empregado na gama 000100 a 000199 utilização: $filter=(PartitionKey eq 'Sales') e (RowKey ge 'empid_000100') e (RowKey le 'empid_000199')  
* Para encontrar todos os colaboradores do departamento de Vendas com um endereço de e-mail a começar pela letra 'a' use: $filter=(PartitionKey eq 'Sales') e (RowKey ge 'email_a') e (RowKey lt 'email_b')  
  
  A sintaxe de filtro utilizada nos exemplos acima é do serviço mesa REST API, para mais informações, ver [Consultas Entidades](https://msdn.microsoft.com/library/azure/dd179421.aspx).  

### <a name="issues-and-considerations"></a>Problemas e considerações
Na altura de decidir como implementar este padrão, considere os seguintes pontos:  

* O armazenamento de mesas é relativamente barato de utilizar, pelo que o custo de armazenamento de dados duplicados não deve ser uma grande preocupação. No entanto, deverá sempre avaliar o custo do seu design com base nos seus requisitos de armazenamento antecipados e apenas adicionar entidades duplicadas para suportar as consultas que a sua aplicação de cliente irá executar.  
* Uma vez que as entidades do índice secundário são armazenadas na mesma partilha que as entidades originais, deve garantir que não excede os objetivos de escalabilidade para uma partição individual.  
* Pode manter as suas entidades duplicadas consistentes entre si, utilizando EGTs para atualizar atomicamente as duas cópias da entidade. Isto implica que deve armazenar todas as cópias de uma entidade na mesma divisória. Para mais informações, consulte a secção [Utilização de Transações do Grupo entidade](table-storage-design.md#entity-group-transactions).  
* O valor utilizado para o **RowKey** deve ser único para cada entidade. Considere usar valores-chave compostos.  
* Acolchoamento de valores numéricos no **RowKey** (por exemplo, o ID do empregado 000223), permite a triagem e filtragem corretas com base nos limites superiores e inferiores.  
* Não precisa necessariamente de duplicar todas as propriedades da sua entidade. Por exemplo, se as consultas que procuram as entidades que usam o endereço de e-mail no **RowKey** nunca precisarem da idade do trabalhador, estas entidades poderão ter a seguinte estrutura:

   ![Estrutura da entidade dos colaboradores](media/storage-table-design-guide/storage-table-design-IMAGE08.png)


* Normalmente, é melhor armazenar dados duplicados e garantir que pode recuperar todos os dados necessários com uma única consulta, do que usar uma consulta para localizar uma entidade e outra para procurar os dados necessários.  

### <a name="when-to-use-this-pattern"></a>Quando utilizar este padrão
Utilize este padrão quando a sua aplicação cliente precisa de recuperar entidades usando uma variedade de chaves diferentes, quando o seu cliente precisa de recuperar entidades em diferentes ordens de classificação, e onde pode identificar cada entidade usando uma variedade de valores únicos. No entanto, deve certificar-se de que não excede os limites de escalabilidade da divisória quando está a realizar as procurações de entidades utilizando os diferentes valores **RowKey.**  

### <a name="related-patterns-and-guidance"></a>Padrões e orientações relacionados
Os padrões e orientações que se seguem podem também ser relevantes ao implementar este padrão:  

* [Padrão de índice secundário inter-divisória](#inter-partition-secondary-index-pattern)
* [Padrão de chave composto](#compound-key-pattern)
* Transações do Grupo entidade
* [Trabalhar com tipos de entidades heterogéneas](#working-with-heterogeneous-entity-types)

## <a name="inter-partition-secondary-index-pattern"></a>Padrão de índice secundário inter-divisória
Guarde várias cópias de cada entidade utilizando diferentes valores **RowKey** em divisórias separadas ou em tabelas separadas para permitir pesquisas rápidas e eficientes e ordens de classificação alternativas utilizando diferentes valores **RowKey.**  

### <a name="context-and-problem"></a>Contexto e problema
O serviço de tabela indexa automaticamente as entidades utilizando os valores **PartitionKey** e **RowKey.** Isto permite que uma aplicação do cliente recupere uma entidade utilizando eficientemente estes valores. Por exemplo, utilizando a estrutura de tabela apresentada abaixo, uma aplicação de cliente pode usar uma consulta pontual para recuperar uma entidade de empregado individual utilizando o nome do departamento e o ID do empregado (os valores **PartitionKey** e **RowKey).** Um cliente também pode recuperar entidades ordenadas por identificação de empregado dentro de cada departamento.  

![ID de Empregado](media/storage-table-design-guide/storage-table-design-IMAGE09.png)

Se também pretende encontrar uma entidade colaboradora com base no valor de outra propriedade, como o endereço de e-mail, deve utilizar uma verificação de divisórias menos eficiente para encontrar uma correspondência. Isto porque o serviço de mesa não fornece índices secundários. Além disso, não existe opção de solicitar uma lista de colaboradores classificados numa ordem diferente da encomenda **RowKey.**  

Está a antecipar um elevado volume de transações contra estas entidades e pretende minimizar o risco do serviço de Mesa estrangular o seu cliente.  

### <a name="solution"></a>Solução
Para contornar a falta de índices secundários, pode armazenar várias cópias de cada entidade com cada cópia utilizando diferentes valores **PartitionKey** e **RowKey.** Se armazenar uma entidade com as estruturas apresentadas abaixo, pode recuperar eficientemente as entidades dos colaboradores com base no endereço de e-mail ou no ID do empregado. Os valores prefixos para a **Chave partição,**"empid_" e "email_" permitem identificar qual o índice que pretende utilizar para uma consulta.  

![Índice primário e índice secundário](media/storage-table-design-guide/storage-table-design-IMAGE10.png)


Os dois critérios de filtro seguintes (um olhando para cima por id do empregado e outro olhando para cima por endereço de e-mail) ambos especificam consultas de ponto:  

* $filter=(PartitionKey eq 'empid_Sales') e (RowKey eq '000223')
* $filter=(PartitionKey eq 'email_Sales') e (RowKey eq')jonesj@contoso.com  

Se consultar uma série de entidades de empregados, pode especificar uma gama ordenada na ordem de identificação dos empregados, ou uma gama ordenada na ordem de endereço de e-mail consultando para entidades com o prefixo apropriado no **RowKey**.  

* Encontrar todos os colaboradores do departamento de Vendas com identificação de empregados na gama **000100** a **000199** classificados na utilização da ordem de identificação dos funcionários: $filter=(PartitionKey eq 'empid_Sales') e (RowKey ge '000100') e (RowKey le '000199')  
* Para encontrar todos os colaboradores do departamento de Vendas com um endereço de e-mail que comece com 'a' ordenado na utilização de endereçode e-mail: $filter=(PartitionKey eq 'email_Sales') e (RowKey ge 'a') e (RowKey lt 'b')  

A sintaxe de filtro utilizada nos exemplos acima é do serviço mesa REST API, para mais informações, ver [Consultas Entidades](https://msdn.microsoft.com/library/azure/dd179421.aspx).  

### <a name="issues-and-considerations"></a>Problemas e considerações
Na altura de decidir como implementar este padrão, considere os seguintes pontos:  

* Pode manter as suas entidades duplicadas eventualmente consistentes entre si, utilizando o padrão de [transações eventualmente consistente](#eventually-consistent-transactions-pattern) para manter as entidades de índice primário e secundário.  
* O armazenamento de mesas é relativamente barato de utilizar, pelo que o custo de armazenamento de dados duplicados não deve ser uma grande preocupação. No entanto, deverá sempre avaliar o custo do seu design com base nos seus requisitos de armazenamento antecipados e apenas adicionar entidades duplicadas para suportar as consultas que a sua aplicação de cliente irá executar.  
* O valor utilizado para o **RowKey** deve ser único para cada entidade. Considere usar valores-chave compostos.  
* Acolchoamento de valores numéricos no **RowKey** (por exemplo, o ID do empregado 000223), permite a triagem e filtragem corretas com base nos limites superiores e inferiores.  
* Não precisa necessariamente de duplicar todas as propriedades da sua entidade. Por exemplo, se as consultas que procuram as entidades que usam o endereço de e-mail no **RowKey** nunca precisarem da idade do trabalhador, estas entidades poderão ter a seguinte estrutura:
  
   ![Entidade de empregados (índice secundário)](media/storage-table-design-guide/storage-table-design-IMAGE11.png)

* Normalmente, é melhor armazenar dados duplicados e garantir que pode recuperar todos os dados necessários com uma única consulta do que usar uma consulta para localizar uma entidade usando o índice secundário e outra para procurar os dados necessários no índice primário.  

### <a name="when-to-use-this-pattern"></a>Quando utilizar este padrão
Utilize este padrão quando a sua aplicação cliente precisa de recuperar entidades usando uma variedade de chaves diferentes, quando o seu cliente precisa de recuperar entidades em diferentes ordens de classificação, e onde pode identificar cada entidade usando uma variedade de valores únicos. Utilize este padrão quando pretender evitar exceder os limites de escalabilidade da divisória quando estiver a realizar procuras de entidades utilizando os diferentes valores **RowKey.**  

### <a name="related-patterns-and-guidance"></a>Padrões e orientações relacionados
Os padrões e orientações que se seguem podem também ser relevantes ao implementar este padrão:  

* [Eventualmente consistente padrão de transações](#eventually-consistent-transactions-pattern)  
* [Padrão de índice secundário intra-partição](#intra-partition-secondary-index-pattern)  
* [Padrão de chave composto](#compound-key-pattern)  
* Transações do Grupo entidade  
* [Trabalhar com tipos de entidades heterogéneas](#working-with-heterogeneous-entity-types)  

## <a name="eventually-consistent-transactions-pattern"></a>Eventualmente consistente padrão de transações
Ativar um comportamento eventualmente consistente através dos limites da divisória ou dos limites do sistema de armazenamento utilizando as filas Azure.  

### <a name="context-and-problem"></a>Contexto e problema
Os EGTs permitem transações atómicas em várias entidades que partilham a mesma chave de partição. Por razões de desempenho e escalabilidade, poderá decidir armazenar entidades que tenham requisitos de consistência em divisórias separadas ou num sistema de armazenamento separado: em tal cenário, não pode utilizar EGTs para manter a consistência. Por exemplo, pode ter a obrigação de manter uma eventual consistência entre:  

* Entidades armazenadas em duas divisórias diferentes na mesma tabela, em tabelas diferentes, ou em diferentes contas de armazenamento.  
* Uma entidade armazenada no serviço Mesa e uma bolha armazenada no serviço Blob.  
* Uma entidade armazenada no serviço Mesa e um ficheiro num sistema de ficheiros.  
* Uma entidade armazenada no serviço Mesa ainda indexada utilizando o serviço de Pesquisa Cognitiva Azure.  

### <a name="solution"></a>Solução
Ao utilizar as filas Azure, pode implementar uma solução que proporciona uma eventual consistência em duas ou mais divisórias ou sistemas de armazenamento.
Para ilustrar esta abordagem, assuma que tem a obrigação de poder arquivar antigas entidades de funcionários. As entidades antigas de empregados raramente são questionadas e devem ser excluídas de quaisquer atividades que tratem com os atuais colaboradores. Para implementar este requisito, armazena colaboradores ativos na tabela **Current** e antigos colaboradores na tabela **Arquivo.** Arquivar um empregado requer que você elimine a entidade da tabela **Current** e adicione a entidade à tabela **Arquivo,** mas não pode utilizar um EGT para realizar estas duas operações. Para evitar o risco de uma falha fazer com que uma entidade apareça em ambas as tabelas ou em nenhuma das tabelas, a operação de arquivo deve ser eventualmente consistente. O diagrama de sequência a seguir descreve os passos desta operação. Mais pormenores são fornecidos para os caminhos de exceção no texto seguinte.  

![Solução de filas Azure](media/storage-table-design-guide/storage-table-design-IMAGE12.png)

Um cliente inicia a operação de arquivo colocando uma mensagem numa fila Azure, neste exemplo para arquivar #456 de funcionários. Um papel de trabalhador sondage a fila para novas mensagens; quando encontra uma, lê a mensagem e deixa uma cópia escondida na fila. O papel do trabalhador em seguida requer uma cópia da entidade da tabela **Atual,** insere uma cópia na tabela **Arquivo** e, em seguida, elimina o original da tabela **Atual.** Finalmente, se não houve erros dos passos anteriores, o papel do trabalhador elimina a mensagem escondida da fila.  

Neste exemplo, o passo 4 insere o empregado na tabela **Archive.** Poderia adicionar o empregado a uma bolha no serviço Blob ou a um ficheiro num sistema de ficheiros.  

### <a name="recovering-from-failures"></a>Recuperação de falhas
É importante que as operações nos passos **4** e **5** sejam *idempotentes* no caso de a função do trabalhador necessitar de reiniciar a operação de arquivo. Se estiver a utilizar o serviço Tabela, para o passo **4** deve utilizar uma operação de "inserir ou substituir"; para o passo **5** deve utilizar uma operação de "apagar se existir" na biblioteca de clientes que está a utilizar. Se estiver a utilizar outro sistema de armazenamento, deve utilizar uma operação idempotente adequada.  

Se a função do trabalhador nunca completar o passo **6**, então, após um intervalo, a mensagem reaparece na fila pronta para o papel do trabalhador tentar reprocessá-la. O papel do trabalhador pode verificar quantas vezes uma mensagem na fila foi lida e, se necessário, sinalizar é uma mensagem "venenosa" para investigação enviando-a para uma fila separada. Para obter mais informações sobre a leitura de mensagens de fila e a verificação da contagem de filas, consulte [Get Messages](https://msdn.microsoft.com/library/azure/dd179474.aspx).  

Alguns erros dos serviços de Mesa e Fila são erros transitórios, e a sua aplicação do cliente deve incluir uma lógica de retry adequada para lidar com eles.  

### <a name="issues-and-considerations"></a>Problemas e considerações
Na altura de decidir como implementar este padrão, considere os seguintes pontos:  

* Esta solução não prevê o isolamento das transações. Por exemplo, um cliente podia ler as tabelas **Current** and **Archive** quando o papel do trabalhador estava entre os passos **4** e **5**, e ver uma visão inconsistente dos dados. Os dados serão consistentes eventualmente.  
* Deve certificar-se de que os passos 4 e 5 são idempotentes para garantir uma eventual consistência.  
* Pode escalar a solução utilizando várias filas e exemplos de papéis do trabalhador.  

### <a name="when-to-use-this-pattern"></a>Quando utilizar este padrão
Utilize este padrão quando quiser garantir uma eventual consistência entre entidades que existem em diferentes divisórias ou tabelas. Pode estender este padrão para garantir uma eventual consistência para as operações através do serviço De mesa e do serviço Blob e outras fontes de dados não-Azure Storage, como base de dados ou sistema de ficheiros.  

### <a name="related-patterns-and-guidance"></a>Padrões e orientações relacionados
Os padrões e orientações que se seguem podem também ser relevantes ao implementar este padrão:  

* Transações do Grupo entidade  
* [Fundir ou substituir](#merge-or-replace)  

> [!NOTE]
> Se o isolamento de transações for importante para a sua solução, deverá considerar redesenhar as suas tabelas para permitir que utilize EGTs.  
> 
> 

## <a name="index-entities-pattern"></a>Padrão de entidades indexadas
Manter entidades indexadas para permitir pesquisas eficientes que devolução de listas de entidades.  

### <a name="context-and-problem"></a>Contexto e problema
O serviço de tabela indexa automaticamente as entidades utilizando os valores **PartitionKey** e **RowKey.** Isto permite que uma aplicação do cliente recupere uma entidade de forma eficiente usando uma consulta pontual. Por exemplo, utilizando a estrutura de tabela apresentada abaixo, uma aplicação cliente pode recuperar eficientemente uma entidade de empregados individuais utilizando o nome do departamento e o ID do empregado (a **PartitionKey** e **RowKey**).  

![Entidade colaboradora](media/storage-table-design-guide/storage-table-design-IMAGE13.png)

Se também pretende recuperar uma lista de entidades colaboradoras com base no valor de outra propriedade não única, como o apelido, deve utilizar uma verificação de divisórias menos eficiente para encontrar fósforos em vez de usar um índice para os procurar diretamente. Isto porque o serviço de mesa não fornece índices secundários.  

### <a name="solution"></a>Solução
Para permitir a procura por último nome com a estrutura da entidade acima apresentada, deve manter listas de IDs dos empregados. Se quiser recuperar as entidades colaboradoras com um nome em particular, como o Jones, tem primeiro de localizar a lista de identificações dos empregados para funcionários com o Jones como apelido e, em seguida, recuperar essas entidades de empregados. Existem três opções principais para armazenar as listas de IDs dos empregados:  

* Use o armazenamento de bolhas.  
* Criar entidades indexadas na mesma partilha que as entidades colaboradoras.  
* Crie entidades indexadas numa divisória ou mesa separada.  

<u>Opção #1: Utilize o armazenamento de bolhas</u>  

Para a primeira opção, cria-se uma bolha para cada apelido único e, em cada blob, armazena uma lista dos valores **PartitionKey** (departamento) e **RowKey** (ID do empregado) para os colaboradores que têm esse apelido. Quando adiciona ou apaga um empregado, deve certificar-se de que o conteúdo da bolha relevante é eventualmente consistente com as entidades colaboradoras.  

<u>Opção #2:</u> Criar entidades indexadas na mesma divisão  

Para a segunda opção, utilize entidades indexadas que armazenem os seguintes dados:  

![Entidade do índice de empregados](media/storage-table-design-guide/storage-table-design-IMAGE14.png)

A propriedade **EmployeeIDs** contém uma lista de IDs de empregados com o apelido armazenado no **RowKey**.  

Os seguintes passos delineiam o processo que deve seguir quando estiver a adicionar um novo empregado se estiver a utilizar a segunda opção. Neste exemplo, estamos adicionando um empregado com ID 000152 e um sobrenome Jones no departamento de vendas:  

1. Recupere a entidade indexada com um valor **"Vendas" partitionKey** e o valor **RowKey** "Jones". Guarde o ETag desta entidade para utilizar no passo 2.  
2. Criar uma transação de grupo de entidades (isto é, uma operação de lote) que insere a nova entidade de empregados (**PartitionKey** value "Sales" e **RowKey** value "000152"), e atualiza a entidade indexada (**PartitionKey** value "Sales" e **RowKey** value "Jones") adicionando o novo ID de empregado à lista no campo Dos Funcionários. Para obter mais informações sobre transações de grupos de entidades, consulte As Transações do Grupo entidade.  
3. Se a transação do grupo de entidades falhar devido a um erro de condivisa otimista (outra pessoa acabou de modificar a entidade indexada), então precisa recomeçar no passo 1 novamente.  

Pode utilizar uma abordagem semelhante para apagar um empregado se estiver a utilizar a segunda opção. Mudar o apelido de um colaborador é um pouco mais complexo porque terá de executar uma transação de grupo de entidades que atualiza três entidades: a entidade colaboradora, a entidade indexada para o antigo apelido, e a entidade indexada para o novo apelido. Deve recuperar cada entidade antes de efetuar quaisquer alterações para recuperar os valores do ETag que pode utilizar para realizar as atualizações utilizando uma moeda otimista.  

Os seguintes passos delineiam o processo que deve seguir quando precisa de procurar todos os colaboradores com um nome próprio num departamento se estiver a usar a segunda opção. Neste exemplo, estamos procurando todos os empregados com o sobrenome Jones no departamento de vendas:  

1. Recupere a entidade indexada com um valor **"Vendas" partitionKey** e o valor **RowKey** "Jones".  
2. Analise a lista de ids dos empregados no campo dos Funcionários.  
3. Se precisar de informações adicionais sobre cada um destes colaboradores (como os seus endereços de e-mail), recupere cada uma das entidades colaboradoras **utilizando** os valores de "Vendas" e **RowKey** da lista de colaboradores que obteve no passo 2.  

<u>Opção #3:</u> Criar entidades indexadas numa divisória ou tabela separada  

Para a terceira opção, utilize entidades indexadas que armazenem os seguintes dados:  

![Entidade do índice de empregados numa partição separada](media/storage-table-design-guide/storage-table-design-IMAGE15.png)


A propriedade **EmployeeIDs** contém uma lista de IDs de empregados com o apelido armazenado no **RowKey**.  

Com a terceira opção, não é possível utilizar EGTs para manter a consistência, uma vez que as entidades indexadas estão numa divisão separada das entidades colaboradoras. Certifique-se de que as entidades indexadas são eventualmente consistentes com as entidades colaboradoras.  

### <a name="issues-and-considerations"></a>Problemas e considerações
Na altura de decidir como implementar este padrão, considere os seguintes pontos:  

* Esta solução requer pelo menos duas consultas para recuperar entidades correspondentes: uma para consultar as entidades indexadas para obter a lista de valores **RowKey,** e, em seguida, consultas para recuperar cada entidade na lista.  
* Dado que uma entidade individual tem um tamanho máximo de 1 MB, a opção #2 e opção #3 na solução assumem que a lista de IDs de empregados para qualquer apelido nunca é superior a 1 MB. Se a lista de IDs dos empregados for suscetível de ser superior a 1 MB de tamanho, utilize a opção #1 e guarde os dados do índice no armazenamento de blob.  
* Se utilizar a opção #2 (utilizando EGTs para lidar com a adição e apagando os colaboradores, e alterando o apelido de um empregado) deve avaliar se o volume de transações se aproximará dos limites de escalabilidade numa determinada partição. Se for esse o caso, deverá considerar uma solução eventualmente consistente (opção #1 ou opção #3) que utilize filas para lidar com os pedidos de atualização e lhe permita armazenar as suas entidades indexadas numa partilha separada das entidades colaboradoras.  
* A opção #2 nesta solução pressupõe que quer procurar pelo último nome dentro de um departamento: por exemplo, quer recuperar uma lista de colaboradores com um apelido Jones no departamento de Vendas. Se quiser procurar todos os colaboradores com um apelido Jones em toda a organização, use qualquer opção #1 ou opção #3.
* Pode implementar uma solução baseada na fila que proporciona uma eventual consistência (ver o padrão de [transações eventualmente consistente](#eventually-consistent-transactions-pattern) para mais detalhes).  

### <a name="when-to-use-this-pattern"></a>Quando utilizar este padrão
Use este padrão quando quiser procurar um conjunto de entidades que todos partilham um valor de propriedade comum, como todos os colaboradores com o apelido Jones.  

### <a name="related-patterns-and-guidance"></a>Padrões e orientações relacionados
Os padrões e orientações que se seguem podem também ser relevantes ao implementar este padrão:  

* [Padrão de chave composto](#compound-key-pattern)  
* [Eventualmente consistente padrão de transações](#eventually-consistent-transactions-pattern)  
* Transações do Grupo entidade  
* [Trabalhar com tipos de entidades heterogéneas](#working-with-heterogeneous-entity-types)  

## <a name="denormalization-pattern"></a>Padrão de desnormalização
Combine dados relacionados numa única entidade para que possa recuperar todos os dados necessários com uma consulta de ponto único.  

### <a name="context-and-problem"></a>Contexto e problema
Numa base de dados relacional, normalmente normalmente normaliza dados para remover duplicações, resultando em consultas que recuperam dados de várias tabelas. Se normalizar os seus dados em tabelas Azure, tem de fazer várias viagens de ida e volta do cliente ao servidor para recuperar os dados relacionados. Por exemplo, com a estrutura de tabela mostrada abaixo, você precisa de duas viagens de ida e volta para recuperar os detalhes para um departamento: um para buscar a entidade do departamento que inclui o ID do gestor, e depois outro pedido para obter os detalhes do gestor em uma entidade colaboradora.  

![Entidade de departamento e entidade de empregados](media/storage-table-design-guide/storage-table-design-IMAGE16.png)

### <a name="solution"></a>Solução
Em vez de armazenar os dados em duas entidades distintas, desnormalize os dados e guarde uma cópia dos dados do gestor na entidade do departamento. Por exemplo:  

![Entidade do departamento](media/storage-table-design-guide/storage-table-design-IMAGE17.png)

Com entidades de departamento armazenadas com estas propriedades, agora pode recuperar todos os detalhes que precisa sobre um departamento usando uma consulta pontual.  

### <a name="issues-and-considerations"></a>Problemas e considerações
Na altura de decidir como implementar este padrão, considere os seguintes pontos:  

* Há alguns custos associados ao armazenamento de alguns dados duas vezes. O benefício de desempenho (resultante de menos pedidos ao serviço de armazenamento) normalmente supera o aumento marginal dos custos de armazenamento (e este custo é parcialmente compensado por uma redução do número de transações que você precisa para obter os detalhes de um departamento).  
* Deve manter a consistência das duas entidades que armazenam informação sobre gestores. Pode lidar com a questão da consistência utilizando EGTs para atualizar várias entidades numa única transação atómica: neste caso, a entidade do departamento e a entidade colaboradora do gestor do departamento são armazenadas na mesma partilha.  

### <a name="when-to-use-this-pattern"></a>Quando utilizar este padrão
Use este padrão quando precisa frequentemente de procurar informações relacionadas. Este padrão reduz o número de consultas que o seu cliente deve fazer para recuperar os dados necessários.  

### <a name="related-patterns-and-guidance"></a>Padrões e orientações relacionados
Os padrões e orientações que se seguem podem também ser relevantes ao implementar este padrão:  

* [Padrão de chave composto](#compound-key-pattern)  
* Transações do Grupo entidade  
* [Trabalhar com tipos de entidades heterogéneas](#working-with-heterogeneous-entity-types)

## <a name="compound-key-pattern"></a>Padrão de chave composto
Utilize valores compostos **RowKey** para permitir que um cliente procure dados relacionados com uma consulta de ponto único.  

### <a name="context-and-problem"></a>Contexto e problema
Numa base de dados relacional, é natural utilizar juntas em consultas para devolver peças de dados relacionadas ao cliente numa única consulta. Por exemplo, pode utilizar o ID do empregado para procurar uma lista de entidades relacionadas que contenham dados de desempenho e revisão para esse funcionário.  

Assuma que está a armazenar entidades de empregados no serviço Mesa utilizando a seguinte estrutura:  

![Estrutura da entidade dos colaboradores](media/storage-table-design-guide/storage-table-design-IMAGE18.png)

Também precisa de armazenar dados históricos relativos a avaliações e desempenho para cada ano que o colaborador tem trabalhado para a sua organização e precisa de ter acesso a essa informação por ano. Uma opção é criar outra tabela que armazene entidades com a seguinte estrutura:  

![Estrutura alternativa da entidade de empregados](media/storage-table-design-guide/storage-table-design-IMAGE19.png)

Note que com esta abordagem poderá decidir duplicar algumas informações (como o primeiro nome e apelido) na nova entidade para lhe permitir recuperar os seus dados com um único pedido. No entanto, não pode manter uma forte consistência porque não pode utilizar um EGT para atualizar atomicamente as duas entidades.  

### <a name="solution"></a>Solução
Guarde um novo tipo de entidade na sua tabela original utilizando entidades com a seguinte estrutura:  

![Solução para estrutura da entidade de colaboradores](media/storage-table-design-guide/storage-table-design-IMAGE20.png)

Note como o **RowKey** é agora uma chave composta pelo ID do empregado e o ano dos dados de revisão que lhe permite recuperar o desempenho do empregado e rever os dados com um único pedido de uma única entidade.  

O exemplo que se segue descreve como pode recuperar todos os dados de revisão para um determinado empregado (como o funcionário 000123 no departamento de Vendas):  

$filter=(PartitionKey eq 'Sales') e (RowKey ge 'empid_000123') e (RowKey lt 'empid_000124')&$select=RowKey,Manager Rating,Peer Rating,Comments  

### <a name="issues-and-considerations"></a>Problemas e considerações
Na altura de decidir como implementar este padrão, considere os seguintes pontos:  

* Deve utilizar um separador adequado que facilite a análise do valor **RowKey:** por exemplo, **000123_2012**.  
* Também está a armazenar esta entidade na mesma partilha que outras entidades que contêm dados relacionados para o mesmo colaborador, o que significa que pode utilizar EGTs para manter uma forte consistência.
* Deve considerar a frequência com que irá consultar os dados para determinar se este padrão é apropriado.  Por exemplo, se aceder aos dados de revisão com pouca frequência e os principais dados dos colaboradores, muitas vezes deve mantê-los como entidades separadas.  

### <a name="when-to-use-this-pattern"></a>Quando utilizar este padrão
Use este padrão quando precisa armazenar uma ou mais entidades relacionadas que consulta frequentemente.  

### <a name="related-patterns-and-guidance"></a>Padrões e orientações relacionados
Os padrões e orientações que se seguem podem também ser relevantes ao implementar este padrão:  

* Transações do Grupo entidade  
* [Trabalhar com tipos de entidades heterogéneas](#working-with-heterogeneous-entity-types)  
* [Eventualmente consistente padrão de transações](#eventually-consistent-transactions-pattern)  

## <a name="log-tail-pattern"></a>Padrão de cauda de log
Recupere as entidades *n* mais recentemente adicionadas a uma partição usando um valor **RowKey** que classifica em data e ordem de tempo inversa.  

### <a name="context-and-problem"></a>Contexto e problema
Um requisito comum é capaz de recuperar as entidades mais recentemente criadas, por exemplo, os 10 pedidos de despesas mais recentes apresentados por um empregado. As consultas de mesa suportam uma operação de consulta **$top** para devolver as primeiras entidades *n* de um conjunto: não há nenhuma operação de consulta equivalente para devolver as últimas entidades n em um conjunto.  

### <a name="solution"></a>Solução
Guarde as entidades utilizando um **RowKey** que naturalmente separa em data/hora inversa, utilizando para que a entrada mais recente seja sempre a primeira da tabela.  

Por exemplo, para poder recuperar os 10 pedidos de despesas mais recentes apresentados por um empregado, pode utilizar um valor de carraça inversa derivado da data/hora atual. A seguinte amostra de código C# mostra uma maneira de criar um valor adequado de "tiques invertidos" para um **RowKey** que seclassifica do mais recente ao mais antigo:  

`string invertedTicks = string.Format("{0:D19}", DateTime.MaxValue.Ticks - DateTime.UtcNow.Ticks);`  

Pode voltar ao valor da data utilizando o seguinte código:  

`DateTime dt = new DateTime(DateTime.MaxValue.Ticks - Int64.Parse(invertedTicks));`  

A consulta de mesa é assim:  

`https://myaccount.table.core.windows.net/EmployeeExpense(PartitionKey='empid')?$top=10`  

### <a name="issues-and-considerations"></a>Problemas e considerações
Na altura de decidir como implementar este padrão, considere os seguintes pontos:  

* Deve remar o valor do tique-taque inverso com os zeros principais para garantir que o valor da cadeia classifica como esperado.  
* Deve estar ciente dos alvos de escalabilidade ao nível de uma partição. Tenha cuidado para não criar divisórias de ponto sinuoso.  

### <a name="when-to-use-this-pattern"></a>Quando utilizar este padrão
Utilize este padrão quando necessitar de aceder a entidades em data/hora inversa ou quando necessitar de aceder às entidades mais recentes.  

### <a name="related-patterns-and-guidance"></a>Padrões e orientações relacionados
Os padrões e orientações que se seguem podem também ser relevantes ao implementar este padrão:  

* [Prepend / apêndice anti-padrão](#prepend-append-anti-pattern)  
* [Entidades de recuperação](#retrieving-entities)  

## <a name="high-volume-delete-pattern"></a>Padrão de eliminação de alto volume
Permitir a supressão de um elevado volume de entidades armazenando todas as entidades para eliminação simultânea na sua própria tabela separada; elimina as entidades eliminando a tabela.  

### <a name="context-and-problem"></a>Contexto e problema
Muitas aplicações apagam dados antigos que já não precisam de estar disponíveis para uma aplicação do cliente, ou que a aplicação foi arquivada para outro meio de armazenamento. Normalmente identifica esses dados por uma data: por exemplo, tem a obrigação de eliminar registos de todos os pedidos de login com mais de 60 dias de idade.  

Um dos desenhos possíveis é utilizar a data e a hora do pedido de login no **RowKey:**  

![Data e hora da tentativa de login](media/storage-table-design-guide/storage-table-design-IMAGE21.png)

Esta abordagem evita os hotspots de partição porque a aplicação pode inserir e eliminar entidades de login para cada utilizador numa divisória separada. No entanto, esta abordagem pode ser dispendiosa e morosa se tiver um grande número de entidades porque primeiro precisa de realizar uma digitalização de tabela para identificar todas as entidades a eliminar, e depois deve apagar cada entidade antiga. Pode reduzir o número de viagens de ida e volta ao servidor necessárias para eliminar as entidades antigas, emitindo vários pedidos de eliminação em EGTs.  

### <a name="solution"></a>Solução
Utilize uma tabela separada para cada dia de tentativas de login. Pode utilizar o design da entidade acima para evitar hotspots quando está a inserir entidades, e apagar entidades antigas é agora simplesmente uma questão de apagar uma tabela todos os dias (uma única operação de armazenamento) em vez de encontrar e apagar centenas e milhares de entidades individuais de login todos os dias.  

### <a name="issues-and-considerations"></a>Problemas e considerações
Na altura de decidir como implementar este padrão, considere os seguintes pontos:  

* O seu design suporta outras formas de utilizar os dados, tais como procurar entidades específicas, ligar-se a outros dados ou gerar informação agregada?  
* O seu design evita pontos quentes quando está a inserir novas entidades?  
* Espere um atraso se quiser reutilizar o mesmo nome de mesa depois de o apagar. É melhor usar sempre nomes de mesa únicos.  
* Espere um pouco de estrangulamento quando utilizar pela primeira vez uma nova tabela enquanto o serviço De Mesa aprende os padrões de acesso e distribui as divisórias através dos nódosos. Deve considerar a frequência com que precisa de criar novas tabelas.  

### <a name="when-to-use-this-pattern"></a>Quando utilizar este padrão
Utilize este padrão quando tiver um elevado volume de entidades que deve eliminar ao mesmo tempo.  

### <a name="related-patterns-and-guidance"></a>Padrões e orientações relacionados
Os padrões e orientações que se seguem podem também ser relevantes ao implementar este padrão:  

* Transações do Grupo entidade
* [Modificação de entidades](#modifying-entities)  

## <a name="data-series-pattern"></a>Padrão de série de dados
Guarde séries de dados completas numa única entidade para minimizar o número de pedidos que efaz.  

### <a name="context-and-problem"></a>Contexto e problema
Um cenário comum é uma aplicação para armazenar uma série de dados que normalmente precisa para recuperar tudo de uma vez. Por exemplo, a sua aplicação pode registar quantas mensagens im cada funcionário envia a cada hora, e depois usar esta informação para traçar quantas mensagens cada utilizador enviou nas 24 horas anteriores. Um dos desenhos pode ser armazenar 24 entidades para cada colaborador:  

![Armazenar 24 entidades para cada colaborador](media/storage-table-design-guide/storage-table-design-IMAGE22.png)

Com este design, pode facilmente localizar e atualizar a entidade para atualizar para cada colaborador sempre que a aplicação precisar atualizar o valor da contagem de mensagens. No entanto, para recuperar a informação para traçar um gráfico da atividade para as 24 horas anteriores, deve recuperar 24 entidades.  

### <a name="solution"></a>Solução
Utilize o seguinte design com uma propriedade separada para armazenar a contagem de mensagens por cada hora:  

![Entidade de estatísticas de mensagens](media/storage-table-design-guide/storage-table-design-IMAGE23.png)

Com este design, pode utilizar uma operação de fusão para atualizar a contagem de mensagens para um empregado durante uma hora específica. Agora, você pode recuperar toda a informação que precisa para traçar o gráfico usando um pedido para uma única entidade.  

### <a name="issues-and-considerations"></a>Problemas e considerações
Na altura de decidir como implementar este padrão, considere os seguintes pontos:  

* Se a sua série completa de dados não encaixar numa única entidade (uma entidade pode ter até 252 propriedades), utilize uma loja de dados alternativa, como uma bolha.  
* Se tiver vários clientes a atualizar uma entidade em simultâneo, terá de utilizar o **ETag** para implementar uma conmoeda otimista. Se tiver muitos clientes, poderá ter uma grande contenção.  

### <a name="when-to-use-this-pattern"></a>Quando utilizar este padrão
Utilize este padrão quando necessitar de atualizar e recuperar uma série de dados associada a uma entidade individual.  

### <a name="related-patterns-and-guidance"></a>Padrões e orientações relacionados
Os padrões e orientações que se seguem podem também ser relevantes ao implementar este padrão:  

* [Padrão de grandes entidades](#large-entities-pattern)  
* [Fundir ou substituir](#merge-or-replace)  
* [Eventualmente consistente padrão de transações](#eventually-consistent-transactions-pattern) (se estiver armazenando a série de dados em uma bolha)  

## <a name="wide-entities-pattern"></a>Padrão de entidades amplas
Utilize múltiplas entidades físicas para armazenar entidades lógicas com mais de 252 propriedades.  

### <a name="context-and-problem"></a>Contexto e problema
Uma entidade individual não pode ter mais de 252 propriedades (excluindo as propriedades do sistema obrigatório) e não pode armazenar mais de 1 MB de dados no total. Numa base de dados relacional, normalmente, você obteria contornar quaisquer limites do tamanho de uma linha, adicionando uma nova tabela e aplicando uma relação 1-a-1 entre eles.  

### <a name="solution"></a>Solução
Utilizando o serviço Mesa, pode armazenar várias entidades para representar um único objeto de negócio com mais de 252 propriedades. Por exemplo, se quiser armazenar uma contagem do número de mensagens IM enviadas por cada colaborador nos últimos 365 dias, poderá utilizar o seguinte design que utiliza duas entidades com diferentes esquemas:  

![Múltiplas entidades](media/storage-table-design-guide/storage-table-design-IMAGE24.png)

Se precisar de fazer uma alteração que exija atualizar ambas as entidades para as manter sincronizadas entre si, pode utilizar um EGT. Caso contrário, pode utilizar uma única operação de fusão para atualizar a contagem de mensagens para um dia específico. Para recuperar todos os dados de um funcionário individual, deve recuperar ambas as entidades, o que pode fazer com dois pedidos eficientes que utilizam tanto uma **PartitionKey** como um valor **RowKey.**  

### <a name="issues-and-considerations"></a>Problemas e considerações
Na altura de decidir como implementar este padrão, considere os seguintes pontos:  

* Recuperar uma entidade lógica completa envolve pelo menos duas transações de armazenamento: uma para recuperar cada entidade física.  

### <a name="when-to-use-this-pattern"></a>Quando utilizar este padrão
Utilize este padrão quando necessário armazenar entidades cujo tamanho ou número de propriedades exceda os limites para uma entidade individual no serviço Mesa.  

### <a name="related-patterns-and-guidance"></a>Padrões e orientações relacionados
Os padrões e orientações que se seguem podem também ser relevantes ao implementar este padrão:  

* Transações do Grupo entidade
* [Fundir ou substituir](#merge-or-replace)

## <a name="large-entities-pattern"></a>Padrão de grandes entidades
Utilize o armazenamento blob para armazenar grandes valores de propriedade.  

### <a name="context-and-problem"></a>Contexto e problema
Uma entidade individual não pode armazenar no total mais de 1 MB de dados. Se uma ou várias das suas propriedades armazenarem valores que fazem com que o tamanho total da sua entidade exceda este valor, não pode armazenar toda a entidade no serviço Mesa.  

### <a name="solution"></a>Solução
Se a sua entidade exceder 1 MB de tamanho porque uma ou mais propriedades contêm uma grande quantidade de dados, pode armazenar dados no serviço Blob e, em seguida, armazenar o endereço da bolha numa propriedade da entidade. Por exemplo, pode armazenar a foto de um empregado no armazenamento de blob e armazenar um link para a foto na propriedade **foto** da sua entidade colaboradora:  

![Propriedade de foto](media/storage-table-design-guide/storage-table-design-IMAGE25.png)

### <a name="issues-and-considerations"></a>Problemas e considerações
Na altura de decidir como implementar este padrão, considere os seguintes pontos:  

* Para manter uma eventual consistência entre a entidade no serviço De Mesa e os dados do serviço Blob, utilize o padrão de [transações eventualmente consistente](#eventually-consistent-transactions-pattern) para manter as suas entidades.
* A recuperação de uma entidade completa envolve pelo menos duas transações de armazenamento: uma para recuperar a entidade e outra para recuperar os dados blob.  

### <a name="when-to-use-this-pattern"></a>Quando utilizar este padrão
Utilize este padrão quando necessitar de armazenar entidades cujo tamanho exceda os limites para uma entidade individual no serviço Mesa.  

### <a name="related-patterns-and-guidance"></a>Padrões e orientações relacionados
Os padrões e orientações que se seguem podem também ser relevantes ao implementar este padrão:  

* [Eventualmente consistente padrão de transações](#eventually-consistent-transactions-pattern)  
* [Padrão de entidades amplas](#wide-entities-pattern)

<a name="prepend-append-anti-pattern"></a>

## <a name="prependappend-anti-pattern"></a>Prepend/apêndice anti-padrão
Aumente a escalabilidade quando tiver um grande volume de inserções espalhando as inserções em várias divisórias.  

### <a name="context-and-problem"></a>Contexto e problema
As entidades de pré-gastos ou despesas com as suas entidades armazenadas normalmente resultam na aplicação adicionando novas entidades à primeira ou última partição de uma sequência de divisórias. Neste caso, todas as inserções em qualquer momento estão a ocorrer na mesma divisória, criando um hotspot que impede o serviço de mesa de equilibrar a carga em vários nós, e possivelmente fazendo com que a sua aplicação atinja os alvos de escalabilidade para a partição. Por exemplo, se tiver uma aplicação que regista o acesso à rede e ao acesso de recursos pelos colaboradores, então uma estrutura de entidade, como mostrado abaixo, pode resultar na partilha da hora atual tornando-se um hotspot se o volume de transações atingir o alvo de escalabilidade para uma partição individual:  

![Estrutura de entidades](media/storage-table-design-guide/storage-table-design-IMAGE26.png)

### <a name="solution"></a>Solução
A seguinte estrutura de entidade alternativa evita um hotspot em qualquer partição em particular, uma vez que a aplicação regista eventos:  

![Estrutura de entidade alternativa](media/storage-table-design-guide/storage-table-design-IMAGE27.png)

Note com este exemplo como tanto a **PartitionKey** como **a RowKey** são chaves compostas. O **PartitionKey** utiliza tanto o departamento como o ID do empregado para distribuir o registo através de várias divisórias.  

### <a name="issues-and-considerations"></a>Problemas e considerações
Na altura de decidir como implementar este padrão, considere os seguintes pontos:  

* A estrutura-chave alternativa que evita criar divisórias quentes em inserções suporta eficazmente as consultas que a sua aplicação cliente faz?  
* O seu volume de transações antecipado significa que é provável que atinja os objetivos de escalabilidade para uma partição individual e seja estrangulado pelo serviço de armazenamento?  

### <a name="when-to-use-this-pattern"></a>Quando utilizar este padrão
Evite o anti-padrão de preparação/apêndice quando o seu volume de transações é suscetível de resultar em estrangulamento pelo serviço de armazenamento quando aceder a uma divisória quente.  

### <a name="related-patterns-and-guidance"></a>Padrões e orientações relacionados
Os padrões e orientações que se seguem podem também ser relevantes ao implementar este padrão:  

* [Padrão de chave composto](#compound-key-pattern)  
* [Padrão de cauda de log](#log-tail-pattern)  
* [Modificação de entidades](#modifying-entities)  

## <a name="log-data-anti-pattern"></a>Log data anti-padrão
Normalmente, deve utilizar o serviço Blob em vez do serviço Table para armazenar dados de registo.  

### <a name="context-and-problem"></a>Contexto e problema
Um caso comum para os dados de registo é recuperar uma seleção de entradas de registo para uma data/intervalo específico: por exemplo, pretende encontrar todos os erros e mensagens críticas que a sua aplicação registou entre as 15:04 e as 15:06 numa data específica. Não pretende utilizar a data e a hora da mensagem de registo para determinar a partição a que guarda as entidades de registo: o que resulta numa partição quente porque, a qualquer momento, todas as entidades de registo partilharão o mesmo valor **partitionKey** (ver a secção [Prepend/apêndice anti-padrão).](#prepend-append-anti-pattern) Por exemplo, o esquema da entidade seguinte para uma mensagem de log resulta numa partição quente porque a aplicação escreve todas as mensagens de registo na partição para a data e hora atuais:  

![Entidade de log message](media/storage-table-design-guide/storage-table-design-IMAGE28.png)

Neste exemplo, o **RowKey** inclui a data e a hora da mensagem de registo para garantir que as mensagens de registo são armazenadas ordenadas por ordem de data/hora, e inclui um ID de mensagem no caso de várias mensagens de registo partilharem a mesma data e hora.  

Outra abordagem é usar uma **Chave de Partição** que garanta que a aplicação escreve mensagens através de uma série de divisórias. Por exemplo, se a fonte da mensagem de registo fornecer uma forma de distribuir mensagens em muitas divisórias, poderá utilizar o seguinte esquema de entidade:  

![Entidade alternativa de mensagens de log](media/storage-table-design-guide/storage-table-design-IMAGE29.png)

No entanto, o problema com este esquema é que para recuperar todas as mensagens de registo por um período de tempo específico deve pesquisar todas as divisórias da tabela.

### <a name="solution"></a>Solução
A secção anterior destacou o problema de tentar utilizar o serviço Mesa para armazenar entradas de registo e sugeriu dois desenhos, insatisfatórios. Uma solução levou a uma partição quente com o risco de escrever mensagens de registo de má performance; a outra solução resultou num fraco desempenho de consulta devido à exigência de digitalizar todas as divisórias da tabela para recuperar mensagens de registo durante um período de tempo específico. O armazenamento blob oferece uma solução melhor para este tipo de cenário e é assim que o Azure Storage Analytics armazena os dados de registo que recolhe.  

Esta secção descreve como o Storage Analytics armazena dados de registo no armazenamento de blob como uma ilustração desta abordagem para armazenar dados que normalmente consulta por alcance.  

Storage Analytics armazena mensagens de log em um formato delimitado em várias bolhas. O formato delimitado facilita a análise dos dados por parte de uma aplicação do cliente na mensagem de registo.  

O Storage Analytics utiliza uma convenção de nomeação para bolhas que lhe permite localizar a bolha (ou bolhas) que contêm as mensagens de registo para as quais está a pesquisar. Por exemplo, uma bolha chamada "queue/2014/07/31/1800/000001.log" contém mensagens de registo que se relacionam com o serviço de fila para a hora a partir das 18:00 do dia 31 de julho de 2014. O "000001" indica que este é o primeiro ficheiro de registo deste período. O Storage Analytics também regista os carimbos de tempo das primeiras e últimas mensagens de registo armazenadas no ficheiro como parte dos metadados da bolha. A API para armazenamento de bolhas permite localizar bolhas num recipiente com base num prefixo de nome: localizar todas as bolhas que contenham dados de registo de fila para a hora a partir das 18:00, pode utilizar o prefixo "fila/2014/07/31/1800".  

Armazenamento Analytics tampões registam mensagens internamente e, em seguida, atualiza periodicamente a bolha apropriada ou cria uma nova com o mais recente lote de entradas de registo. Isto reduz o número de escritos que deve executar ao serviço de blob.  

Se estiver a implementar uma solução semelhante na sua própria aplicação, deve considerar como gerir a compensação entre a fiabilidade (escrever cada entrada de registo no armazenamento de blob à medida que acontece) e custo e escalabilidade (atualizações de tampão na sua aplicação e escrevê-las para armazenamento de bolhas em lotes).  

### <a name="issues-and-considerations"></a>Problemas e considerações
Considere os seguintes pontos ao decidir como armazenar dados de registo:  

* Se criar um design de mesa que evite potenciais divisórias quentes, poderá descobrir que não consegue aceder aos seus dados de registo de forma eficiente.  
* Para processar dados de registo, um cliente muitas vezes precisa de carregar muitos registos.  
* Embora os dados de registo sejam muitas vezes estruturados, o armazenamento de blob pode ser uma solução melhor.  

## <a name="implementation-considerations"></a>Considerações de implementação
Esta secção discute algumas das considerações a ter em conta quando implementa os padrões descritos nas secções anteriores. A maior parte desta secção utiliza exemplos escritos em C# que utilizam a Biblioteca do Cliente de Armazenamento (versão 4.3.0 no momento da escrita).  

## <a name="retrieving-entities"></a>Entidades de recuperação
Como discutido na secção Design para consulta, a consulta mais eficiente é uma consulta pontual. No entanto, em alguns cenários poderá ser necessário recuperar várias entidades. Esta secção descreve algumas abordagens comuns para recuperar entidades usando a Biblioteca do Cliente de Armazenamento.  

### <a name="executing-a-point-query-using-the-storage-client-library"></a>Executar uma consulta pontual usando a Biblioteca do Cliente de Armazenamento
A forma mais fácil de executar uma consulta pontual é utilizar a operação da tabela **Recuperar,** como mostra o seguinte código C# que recupera uma entidade com uma Chave de **Partilha** de valor "Vendas" e uma Chave de Valor **RowKey** de valor "212":  

```csharp
TableOperation retrieveOperation = TableOperation.Retrieve<EmployeeEntity>("Sales", "212");
var retrieveResult = employeeTable.Execute(retrieveOperation);
if (retrieveResult.Result != null)
{
    EmployeeEntity employee = (EmployeeEntity)retrieveResult.Result;
    ...
}  
```

Note como este exemplo espera que a entidade que recupera seja de tipo **EmployeeEntity**.  

### <a name="retrieving-multiple-entities-using-linq"></a>Recuperação de várias entidades usando LINQ
Pode utilizar o LINQ para recuperar várias entidades do serviço Table ao trabalhar com a Microsoft Azure Cosmos Table Standard Library. 

```azurecli
dotnet add package Microsoft.Azure.Cosmos.Table
```

Para que os exemplos abaixo funcionem, terá de incluir espaços de nome:

```csharp
using System.Linq;
using Microsoft.Azure.Cosmos.Table;
using Microsoft.Azure.Cosmos.Table.Queryable;
```

O employeeTable é um objeto CloudTable que\<implementa um método CreateQuery ITableEntity>() que devolve um tablequery\<ITableEntity>. Objetos deste tipo implementam um IQueryable e permitem usar tanto expressões de consulta LINQ como sintaxe de notação de pontos.

Recuperando várias entidades e ser alcançado especificando uma consulta com uma cláusula **de local.** Para evitar uma digitalização de tabela, deve sempre incluir o valor **partitionKey** na cláusula onde e, se possível, o valor **RowKey** para evitar digitalizações de tabela e partição. O serviço de mesa suporta um conjunto limitado de operadores de comparação (superior estoiro ou igual, inferior a, menos ou iguais, iguais e não iguais) para utilizar na cláusula em que. 

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

Note como a consulta especifica tanto uma **RowKey** como uma **PartitionKey** para garantir um melhor desempenho.  

A amostra de código seguinte mostra uma funcionalidade equivalente sem utilizar a sintaxe LINQ:  

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
> A amostra nidifica vários **métodos CombineFilters** para incluir as três condições do filtro.  
> 
> 

### <a name="retrieving-large-numbers-of-entities-from-a-query"></a>Recuperando um grande número de entidades de uma consulta
Uma consulta ideal devolve uma entidade individual com base num valor **PartitionKey** e num valor **RowKey.** No entanto, em alguns cenários poderá ter a obrigação de devolver muitas entidades da mesma divisão ou até de muitas divisórias.  

Deve testar sempre totalmente o desempenho da sua aplicação nesses cenários.  

Uma consulta contra o serviço de mesa pode devolver um máximo de 1.000 entidades de uma só vez e pode ser executada por um máximo de cinco segundos. Se o conjunto de resultados contiver mais de 1.000 entidades, se a consulta não tiver terminado dentro de cinco segundos, ou se a consulta ultrapassar o limite da divisória, o serviço tabela devolve um sinal de continuação para permitir que a aplicação do cliente solicite o próximo conjunto de entidades. Para obter mais informações sobre como funcionam os tokens de continuação, consulte [A Hora de Tempo e Paginação.](https://msdn.microsoft.com/library/azure/dd135718.aspx)  

Se estiver a utilizar a Biblioteca do Cliente de Armazenamento, pode tratar automaticamente os tokens de continuação para si, uma vez que devolve entidades do serviço Mesa. A seguinte amostra de código C# utilizando a Biblioteca do Cliente de Armazenamento trata automaticamente os tokens de continuação se o serviço de mesa os devolver numa resposta:  

```csharp
string filter = TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Sales");
TableQuery<EmployeeEntity> employeeQuery = new TableQuery<EmployeeEntity>().Where(filter);

var employees = employeeTable.ExecuteQuery(employeeQuery);
foreach (var emp in employees)
{
    // ...
}  
```

O código C# seguinte trata explicitamente as fichas de continuação:  

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

Ao utilizar fichas de continuação explicitamente, pode controlar quando a sua aplicação recuperar o próximo segmento de dados. Por exemplo, se a sua aplicação cliente permitir que os utilizadores páginam através das entidades armazenadas numa tabela, um utilizador pode decidir não páginar através de todas as entidades recuperadas pela consulta, pelo que a sua aplicação apenas utilizaria um sinal de continuação para recuperar o próximo segmento quando o utilizador tivesse terminado de prestar a conhecer todas as entidades do segmento atual. Esta abordagem tem vários benefícios:  

* Permite-lhe limitar a quantidade de dados a recuperar do serviço Tabela e que se move sobre a rede.  
* Permite-lhe realizar IO assíncrono em .NET.  
* Permite-lhe serializar o símbolo de continuação para armazenamento persistente para que possa continuar em caso de falha de aplicação.  

> [!NOTE]
> Um símbolo de continuação normalmente devolve um segmento contendo 1.000 entidades, embora possa ser menor. É também o caso se limitar o número de entradas que uma consulta devolve utilizando o **Take** para devolver as primeiras entidades n que correspondem aos seus critérios de procura: o serviço de mesa pode devolver um segmento que contenha menos de n entidades, juntamente com um sinal de continuação que lhe permita recuperar as restantes entidades.  
> 
> 

O seguinte código C# mostra como modificar o número de entidades devolvidas dentro de um segmento:  

```csharp
employeeQuery.TakeCount = 50;  
```

### <a name="server-side-projection"></a>Projeção do lado do servidor
Uma única entidade pode ter até 255 propriedades e ter até 1 MB de tamanho. Quando consulta a tabela e recupera entidades, pode não precisar de todas as propriedades e pode evitar transferir dados desnecessariamente (para ajudar a reduzir a latência e o custo). Pode utilizar a projeção do lado do servidor para transferir apenas as propriedades de que necessita. O exemplo a seguir é recuperar apenas a propriedade **email** (juntamente com **PartitionKey**, **RowKey,** **Timestamp**, e **ETag**) das entidades selecionadas pela consulta.  

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

Note como o valor **RowKey** está disponível, mesmo que não tenha sido incluído na lista de propriedades para recuperar.  

## <a name="modifying-entities"></a>Modificação de entidades
A Biblioteca do Cliente de Armazenamento permite modificar as suas entidades armazenadas no serviço de mesa, inserindo, apagando e atualizando entidades. Pode utilizar EGTs para emlotar múltiplas inserções, atualizar e eliminar as operações em conjunto para reduzir o número de viagens redondas necessárias e melhorar o desempenho da sua solução.  

As exceções lançadas quando a Biblioteca do Cliente de Armazenamento executa um EGT normalmente incluem o índice da entidade que fez com que o lote falhasse. Isto é útil quando se está a depurar o código que utiliza EGTs.  

Deve também considerar como o seu design afeta a forma como a sua aplicação de cliente lida com operações de concurrency e atualização.  

### <a name="managing-concurrency"></a>Gerir a simultaneidade
Por predefinição, o serviço de mesa implementa controlos de moeda optimistaao nível de entidades individuais para operações **de Inserção,** Fusão e **Exclusão,** embora seja possível que um cliente force o serviço de mesa a contornar estes controlos. **Delete** Para obter mais informações sobre como o serviço de mesa gere a concurrency, consulte [Managing Concurrency no Armazenamento Microsoft Azure](../../storage/common/storage-concurrency.md).  

### <a name="merge-or-replace"></a>Fundir ou substituir
O método **de substituição** da classe **TableOperation** substitui sempre a entidade completa do serviço Tabela. Se não incluir um imóvel no pedido quando esse imóvel existe na entidade armazenada, o pedido retira esse imóvel da entidade armazenada. A menos que pretenda remover uma propriedade explicitamente de uma entidade armazenada, deve incluir todos os imóveis no pedido.  

Pode utilizar o método **de fusão** da classe **TableOperation** para reduzir a quantidade de dados que envia ao serviço Tabela quando pretende atualizar uma entidade. O método **Merge** substitui quaisquer imóveis na entidade armazenada por valores patrimoniais da entidade incluída no pedido, mas deixa intactos quaisquer imóveis na entidade armazenada que não estejam incluídos no pedido. Isto é útil se tiver grandes entidades e apenas necessitar de atualizar um pequeno número de propriedades num pedido.  

> [!NOTE]
> Os métodos **de substituição** e **fusão** falham se a entidade não existir. Como alternativa, pode utilizar os métodos **InsertOrReplace** e **InsertOrMerge** que criam uma nova entidade se não existir.  
> 
> 

## <a name="working-with-heterogeneous-entity-types"></a>Trabalhar com tipos de entidades heterogéneas
O serviço de mesa é uma loja de mesa *sem esquemas,* o que significa que uma única mesa pode armazenar entidades de vários tipos que proporcionam uma grande flexibilidade no seu design. O exemplo que se segue ilustra uma tabela que armazena entidades de funcionários e departamentos:  

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
<th>DepartmentName</th>
<th>Contagem de funcionários</th>
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

Cada entidade deve ainda ter valores **de PartitionKey,** **RowKey**e **Timestamp,** mas pode ter qualquer conjunto de propriedades. Além disso, não há nada que indique o tipo de entidade a menos que opte por armazenar essa informação em algum lugar. Existem duas opções para identificar o tipo de entidade:  

* Prepare o tipo de entidade para o **RowKey** (ou possivelmente a **Chave de Partição**). Por exemplo, **EMPLOYEE_000123** ou **DEPARTMENT_SALES** como valores **RowKey.**  
* Utilize uma propriedade separada para registar o tipo de entidade, como indicado na tabela abaixo.  

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
<th>EntityType</th>
<th>FirstName</th>
<th>LastName</th>
<th>Idade</th>
<th>Email</th>
</tr>
<tr>
<td>Empregado</td>
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
<th>Email</th>
</tr>
<tr>
<td>Empregado</td>
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
<th>Contagem de funcionários</th>
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
<th>Email</th>
</tr>
<tr>
<td>Empregado</td>
<td></td>
<td></td>
<td></td>
<td></td>
</tr>
</table>
</td>
</tr>
</table>

A primeira opção, pré-gastando o tipo de entidade para o **RowKey,** é útil se houver a possibilidade de duas entidades de diferentes tipos poderem ter o mesmo valor-chave. Também agrupa entidades do mesmo tipo juntas na partição.  

As técnicas discutidas nesta secção são especialmente relevantes para a discussão [Relações de Herança](table-storage-design-modeling.md#inheritance-relationships) no início deste guia no artigo [Relações de Modelação.](table-storage-design-modeling.md)  

> [!NOTE]
> Deve considerar a inclusão de um número de versão no valor do tipo de entidade para permitir que as aplicações do cliente evoluam objetos POCO e trabalhem com diferentes versões.  
> 
> 

O restante desta secção descreve algumas das funcionalidades na Biblioteca do Cliente de Armazenamento que facilitam o trabalho com vários tipos de entidades na mesma tabela.  

### <a name="retrieving-heterogeneous-entity-types"></a>Recuperação de tipos de entidades heterogéneas
Se estiver a utilizar a Biblioteca do Cliente de Armazenamento, tem três opções para trabalhar com vários tipos de entidades.  

Se conhecer o tipo da entidade armazenada com um determinado **RowKey** e valores Chave de **Partilha,** então pode especificar o tipo de entidade quando recuperar a entidade, como mostram os dois exemplos anteriores que recuperam entidades do tipo **EmployeeEntity**: Executar uma consulta pontual [utilizando a Biblioteca](#executing-a-point-query-using-the-storage-client-library) do Cliente de Armazenamento e [recuperando várias entidades usando O LINQ](#retrieving-multiple-entities-using-linq).  

A segunda opção é utilizar o tipo **DynamicTableEntity** (um saco de propriedade) em vez de um tipo de entidade POCO de betão (esta opção também pode melhorar o desempenho porque não há necessidade de serializar e desserializar a entidade para tipos .NET). O código C# que se segue potencialmente recupera várias entidades de diferentes tipos da tabela, mas devolve todas as entidades como instâncias **DynamicTableEntity.** Em seguida, utiliza a propriedade **EntityType** para determinar o tipo de cada entidade:  

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

Para recuperar outras propriedades deve utilizar o método **TryGetValue** na propriedade **Propriedades** da classe **DynamicTableEntity.**  

Uma terceira opção é combinar usando o tipo **DynamicTableEntity** e uma instância **EntityResolver.** Isto permite-lhe resolver vários tipos de POCO na mesma consulta. Neste exemplo, o delegado **da EntidadeResolver** está a utilizar a propriedade **EntityType** para distinguir entre os dois tipos de entidade que a consulta devolve. O método **Resolve** utiliza o delegado **resolver** para resolver instâncias **DynamicTableEntity** para instâncias **TableEntity Table.**  

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
Não precisa de saber o tipo de entidade para a apagar, e conhece sempre o tipo de entidade quando a insere. No entanto, pode utilizar o tipo **DynamicTableEntity** para atualizar uma entidade sem conhecer o seu tipo e sem utilizar uma classe de entidade POCO. A amostra de código seguinte recupera uma única entidade, e verifica que a propriedade **Do Número de Empregados** existe antes de atualizá-la.  

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

## <a name="controlling-access-with-shared-access-signatures"></a>Controlar o acesso com assinaturas de acesso partilhado
Pode utilizar fichas de Assinatura de Acesso Partilhado (SAS) para permitir que as aplicações do cliente modifiquem (e consulta) entidades de tabela sem a necessidade de incluir a chave da sua conta de armazenamento no seu código. Normalmente, existem três principais benefícios para a utilização de SAS na sua aplicação:  

* Não é necessário distribuir a chave da sua conta de armazenamento para uma plataforma insegura (como um dispositivo móvel) de forma a permitir que esse dispositivo aceda e modifique entidades no serviço Mesa.  
* Pode descarregar parte do trabalho que as funções web e dos trabalhadores desempenham na gestão das suas entidades para dispositivos clientes, como computadores de utilizador final e dispositivos móveis.  
* Pode atribuir um conjunto limitado de permissões a um cliente (como permitir o acesso apenas a recursos específicos).  

Para obter mais informações sobre a utilização de tokens SAS com o serviço Mesa, consulte A Utilização de Assinaturas de [Acesso Partilhado (SAS)](../../storage/common/storage-sas-overview.md).  

No entanto, ainda deve gerar as fichas SAS que concedem uma aplicação de cliente às entidades do serviço de mesa: deve fazê-lo num ambiente que tenha acesso seguro às chaves da sua conta de armazenamento. Normalmente, utiliza uma função web ou trabalhadora para gerar as fichas SAS e entregá-las às aplicações do cliente que precisam de acesso às suas entidades. Como ainda há uma sobrecarga envolvida na geração e entrega de fichas SAS aos clientes, deve considerar a melhor forma de reduzir esta sobrecarga, especialmente em cenários de grande volume.  

É possível gerar um símbolo SAS que concede acesso a um subconjunto das entidades numa tabela. Por padrão, cria-se um símbolo SAS para uma tabela inteira, mas também é possível especificar que o token SAS concede acesso a uma gama de valores **PartitionKey,** ou a uma gama de valores **PartitionKey** e **RowKey.** Pode optar por gerar tokens SAS para utilizadores individuais do seu sistema de modo a que o token SAS de cada utilizador só lhes permita aceder às suas próprias entidades no serviço de mesa.  

## <a name="asynchronous-and-parallel-operations"></a>Operações assíncronas e paralelas
Desde que esteja a espalhar os seus pedidos por várias divisórias, pode melhorar a capacidade de entrada e capacidade de resposta do cliente utilizando consultas assíncronas ou paralelas.
Por exemplo, pode ter duas ou mais instâncias de função operária acedendo às suas mesas em paralelo. Você poderia ter funções individuais de trabalhador responsável por conjuntos particulares de divisórias, ou simplesmente ter múltiplos exemplos de papéis de trabalhador, cada um capaz de aceder a todas as divisórias em uma mesa.  

Dentro de uma instância de cliente, você pode melhorar a entrada executando operações de armazenamento sincronicamente. A Biblioteca do Cliente de Armazenamento facilita a escrita de consultas e modificações assíncronas. Por exemplo, pode começar com o método sincronizado que recupera todas as entidades numa partição, como mostra o seguinte código C#:  

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

Pode modificar facilmente este código de modo a que a consulta seja executado assincronicamente da seguinte forma:  

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

Neste exemplo assíncrono, pode ver as seguintes alterações a partir da versão sincronizada:  

* A assinatura do método inclui agora o modificador **assinizador** e devolve uma instância **de tarefa.**  
* Em vez de chamar o método **ExecuteSegmented** para recuperar resultados, o método chama agora o método **ExecuteSegmentedAsync** e utiliza o modificador **de espera** para recuperar resultados assincronicamente.  

A aplicação do cliente pode chamar este método várias vezes (com valores diferentes para o parâmetro do **departamento),** e cada consulta será executada em um fio separado.  

Não existe uma versão assíncrona do método **Executar** na classe **TableQuery** porque a interface **IEnumerable** não suporta a enumeração assíncrona.  

Também pode inserir, atualizar e eliminar as entidades de forma assíncrona. O exemplo C# seguinte mostra um método simples e sincronizado para inserir ou substituir uma entidade colaboradora:  

```csharp
private static void SimpleEmployeeUpsert(
    CloudTable employeeTable,
    EmployeeEntity employee)
{
    TableResult result = employeeTable.Execute(TableOperation.InsertOrReplace(employee));
    Console.WriteLine("HTTP Status: {0}", result.HttpStatusCode);
}  
```

Pode modificar facilmente este código de modo a que a atualização seja executado assincronicamente da seguinte forma:  

```csharp
private static async Task SimpleEmployeeUpsertAsync(
    CloudTable employeeTable,
    EmployeeEntity employee)
{
    TableResult result = await employeeTable.ExecuteAsync(TableOperation.InsertOrReplace(employee));
    Console.WriteLine("HTTP Status: {0}", result.HttpStatusCode);
}  
```

Neste exemplo assíncrono, pode ver as seguintes alterações a partir da versão sincronizada:  

* A assinatura do método inclui agora o modificador **assinizador** e devolve uma instância **de tarefa.**  
* Em vez de chamar o método **Executar** para atualizar a entidade, o método chama agora o método **ExecuteAsync** e utiliza o modificador **de espera** para recuperar resultados assincronicamente.  

A aplicação do cliente pode chamar vários métodos assíncronos como este, e cada invocação de método será executada em um fio separado.  

## <a name="next-steps"></a>Passos seguintes

- [Modelar relações](table-storage-design-modeling.md)
- [Design das consultas](table-storage-design-for-query.md)
- [Encriptar dados da tabela](table-storage-design-encrypt-data.md)
- [Design da modificação de dados](table-storage-design-for-modification.md)
