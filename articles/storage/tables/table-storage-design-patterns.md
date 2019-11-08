---
title: Padrões de design de tabela de armazenamento do Azure | Microsoft Docs
description: Use padrões para soluções de serviço de tabela do Azure.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 04/08/2019
ms.author: tamram
ms.subservice: tables
ms.openlocfilehash: d7d4d7b331198982f7c5513d23420bdde9455c66
ms.sourcegitcommit: 018e3b40e212915ed7a77258ac2a8e3a660aaef8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/07/2019
ms.locfileid: "73796672"
---
# <a name="table-design-patterns"></a>Padrões de design da tabela
Este artigo descreve alguns padrões apropriados para uso com soluções de serviço de tabela. Além disso, você verá como é possível lidar praticamente com alguns dos problemas e compensações discutidos em outros artigos de design de armazenamento de tabelas. O diagrama a seguir resume as relações entre os diferentes padrões:  

![para pesquisar dados relacionados](media/storage-table-design-guide/storage-table-design-IMAGE05.png)


O mapa de padrões acima realça algumas relações entre padrões (azuis) e antipadrões (laranja) documentados neste guia. Há muitos outros padrões que vale a pena considerar. Por exemplo, um dos principais cenários do serviço de tabela é usar o [padrão de exibição materializado](https://msdn.microsoft.com/library/azure/dn589782.aspx) do padrão [CQRS (segregação de responsabilidade de consulta de comando)](https://msdn.microsoft.com/library/azure/jj554200.aspx) .  

## <a name="intra-partition-secondary-index-pattern"></a>Padrão de índice secundário intra-Partition
Armazene várias cópias de cada entidade usando valores diferentes de **RowKey** (na mesma partição) para habilitar pesquisas rápidas e eficientes e ordens de classificação alternativas usando valores diferentes de **RowKey** . As atualizações entre as cópias podem ser mantidas consistentes usando o EGTs.  

### <a name="context-and-problem"></a>Contexto e problema
O serviço tabela indexa automaticamente as entidades usando os valores **PartitionKey** e **RowKey** . Isso permite que um aplicativo cliente recupere uma entidade com eficiência usando esses valores. Por exemplo, usando a estrutura de tabela mostrada abaixo, um aplicativo cliente pode usar uma consulta de ponto para recuperar uma entidade de funcionário individual usando o nome do departamento e a ID do funcionário (os valores **PartitionKey** e **RowKey** ). Um cliente também pode recuperar entidades classificadas por ID de funcionário dentro de cada departamento.

![Image06](media/storage-table-design-guide/storage-table-design-IMAGE06.png)

Se você também quiser encontrar uma entidade de funcionário com base no valor de outra propriedade, como endereço de email, deverá usar uma verificação de partição menos eficiente para encontrar uma correspondência. Isso ocorre porque o serviço tabela não fornece índices secundários. Além disso, não há nenhuma opção para solicitar uma lista de funcionários classificados em uma ordem diferente da ordem **RowKey** .  

### <a name="solution"></a>Solução
Para contornar a falta de índices secundários, você pode armazenar várias cópias de cada entidade com cada cópia usando um valor diferente de **RowKey** . Se você armazenar uma entidade com as estruturas mostradas abaixo, poderá recuperar com eficiência entidades de funcionário com base no endereço de email ou na ID de funcionário. Os valores de prefixo para **RowKey**, "empid_" e "email_" permitem consultar um único funcionário ou um intervalo de funcionários usando um intervalo de endereços de email ou de IDs de funcionários.  

![Entidades de funcionário](media/storage-table-design-guide/storage-table-design-IMAGE07.png)

Os dois critérios de filtro a seguir (um pesquisando por ID de funcionário e um pesquisando por endereço de email) especificam consultas de ponto:  

* $filter = (PartitionKey EQ ' Sales ') e (RowKey EQ ' empid_000223 ')  
* $filter = (PartitionKey EQ ' Sales ') e (RowKey EQ 'email_jonesj@contoso.com')  

Se você consultar um intervalo de entidades de funcionário, poderá especificar um intervalo classificado na ordem de ID do funcionário ou um intervalo classificado em ordem de endereço de email consultando as entidades com o prefixo apropriado no **RowKey**.  

* Para localizar todos os funcionários do departamento de vendas com uma ID de funcionário no intervalo de 000100 a 000199, use: $filter = (PartitionKey EQ ' Sales ') e (RowKey ge ' empid_000100 ') e (RowKey Le ' empid_000199 ')  
* Para localizar todos os funcionários do departamento de vendas com um endereço de email começando com a letra ' a ' use: $filter = (PartitionKey EQ ' Sales ') e (RowKey ge ' email_a ') e (RowKey lt ' email_b ')  
  
  A sintaxe de filtro usada nos exemplos acima é da API REST do serviço tabela, para obter mais informações, consulte [entidades de consulta](https://msdn.microsoft.com/library/azure/dd179421.aspx).  

### <a name="issues-and-considerations"></a>Problemas e considerações
Na altura de decidir como implementar este padrão, considere os seguintes pontos:  

* O armazenamento de tabela é relativamente barato de usar, portanto, a sobrecarga de custo do armazenamento de dados duplicados não deve ser uma preocupação importante. No entanto, você deve sempre avaliar o custo do seu design com base em seus requisitos de armazenamento previstos e adicionar somente entidades duplicadas para dar suporte às consultas que seu aplicativo cliente executará.  
* Como as entidades de índice secundário são armazenadas na mesma partição que as entidades originais, você deve garantir que não exceda as metas de escalabilidade para uma partição individual.  
* Você pode manter suas entidades duplicadas consistentes entre si usando EGTs para atualizar as duas cópias da entidade atomicamente. Isso implica que você deve armazenar todas as cópias de uma entidade na mesma partição. Para obter mais informações, consulte a seção [usando transações de grupo de entidades](table-storage-design.md#entity-group-transactions).  
* O valor usado para **RowKey** deve ser exclusivo para cada entidade. Considere o uso de valores de chave composta.  
* Os valores numéricos de preenchimento no **RowKey** (por exemplo, a ID de funcionário 000223) permitem a classificação e a filtragem corretas com base em limites superiores e inferiores.  
* Você não precisa necessariamente duplicar todas as propriedades de sua entidade. Por exemplo, se as consultas que pesquisam as entidades usando o endereço de email no **RowKey** nunca precisarem da idade do funcionário, essas entidades poderão ter a seguinte estrutura:

   ![Estrutura de entidade do funcionário](media/storage-table-design-guide/storage-table-design-IMAGE08.png)


* Normalmente, é melhor armazenar dados duplicados e garantir que você possa recuperar todos os dados necessários com uma única consulta, do que usar uma consulta para localizar uma entidade e outra para pesquisar os dados necessários.  

### <a name="when-to-use-this-pattern"></a>Quando utilizar este padrão
Use esse padrão quando o aplicativo cliente precisar recuperar entidades usando uma variedade de chaves diferentes, quando o cliente precisar recuperar entidades em diferentes ordens de classificação, e onde você pode identificar cada entidade usando uma variedade de valores exclusivos. No entanto, você deve ter certeza de que não excederá os limites de escalabilidade de partição quando estiver executando pesquisas de entidade usando valores diferentes de **RowKey** .  

### <a name="related-patterns-and-guidance"></a>Padrões e orientações relacionados
Os padrões e orientações que se seguem podem também ser relevantes ao implementar este padrão:  

* [Padrão de índice secundário entre partições](#inter-partition-secondary-index-pattern)
* [Padrão de chave composta](#compound-key-pattern)
* Transações de grupo de entidades
* [Trabalhando com tipos de entidade heterogêneas](#working-with-heterogeneous-entity-types)

## <a name="inter-partition-secondary-index-pattern"></a>Padrão de índice secundário entre partições
Armazene várias cópias de cada entidade usando valores diferentes de **RowKey** em partições separadas ou em tabelas separadas para permitir pesquisas rápidas e eficientes e ordens de classificação alternativas usando valores diferentes de **RowKey** .  

### <a name="context-and-problem"></a>Contexto e problema
O serviço tabela indexa automaticamente as entidades usando os valores **PartitionKey** e **RowKey** . Isso permite que um aplicativo cliente recupere uma entidade com eficiência usando esses valores. Por exemplo, usando a estrutura de tabela mostrada abaixo, um aplicativo cliente pode usar uma consulta de ponto para recuperar uma entidade de funcionário individual usando o nome do departamento e a ID do funcionário (os valores **PartitionKey** e **RowKey** ). Um cliente também pode recuperar entidades classificadas por ID de funcionário dentro de cada departamento.  

![ID do funcionário](media/storage-table-design-guide/storage-table-design-IMAGE09.png)

Se você também quiser encontrar uma entidade de funcionário com base no valor de outra propriedade, como endereço de email, deverá usar uma verificação de partição menos eficiente para encontrar uma correspondência. Isso ocorre porque o serviço tabela não fornece índices secundários. Além disso, não há nenhuma opção para solicitar uma lista de funcionários classificados em uma ordem diferente da ordem **RowKey** .  

Você está prevendo um alto volume de transações em relação a essas entidades e deseja minimizar o risco de a limitação do serviço de tabela de seu cliente.  

### <a name="solution"></a>Solução
Para contornar a falta de índices secundários, você pode armazenar várias cópias de cada entidade com cada cópia usando valores diferentes de **PartitionKey** e **RowKey** . Se você armazenar uma entidade com as estruturas mostradas abaixo, poderá recuperar com eficiência entidades de funcionário com base no endereço de email ou na ID de funcionário. Os valores de prefixo para **PartitionKey**, "empid_" e "email_" permitem identificar qual índice você deseja usar para uma consulta.  

![Índice primário e índice secundário](media/storage-table-design-guide/storage-table-design-IMAGE10.png)


Os dois critérios de filtro a seguir (um pesquisando por ID de funcionário e um pesquisando por endereço de email) especificam consultas de ponto:  

* $filter = (PartitionKey EQ ' empid_Sales ') e (RowKey EQ ' 000223 ')
* $filter = (PartitionKey EQ ' email_Sales ') e (RowKey EQ 'jonesj@contoso.com')  

Se você consultar um intervalo de entidades de funcionário, poderá especificar um intervalo classificado na ordem de ID do funcionário ou um intervalo classificado em ordem de endereço de email consultando as entidades com o prefixo apropriado no **RowKey**.  

* Para localizar todos os funcionários do departamento de vendas com uma ID de funcionário no intervalo de **000100** a **000199** classificados em uso de ordem de ID de funcionário: $Filter = (PartitionKey EQ ' empid_Sales ') e (RowKey ge ' 000100 ') e (RowKey Le ' 000199 ')  
* Para localizar todos os funcionários do departamento de vendas com um endereço de email que começa com ' a ' classificado em uso de ordem de endereço de email: $filter = (PartitionKey EQ ' email_Sales ') e (RowKey ge ' a ') e (RowKey lt ' b ')  

A sintaxe de filtro usada nos exemplos acima é da API REST do serviço tabela, para obter mais informações, consulte [entidades de consulta](https://msdn.microsoft.com/library/azure/dd179421.aspx).  

### <a name="issues-and-considerations"></a>Problemas e considerações
Na altura de decidir como implementar este padrão, considere os seguintes pontos:  

* Você pode manter suas entidades duplicadas eventualmente consistentes entre si usando o [padrão de transações eventualmente consistentes](#eventually-consistent-transactions-pattern) para manter as entidades de índice primário e secundário.  
* O armazenamento de tabela é relativamente barato de usar, portanto, a sobrecarga de custo do armazenamento de dados duplicados não deve ser uma preocupação importante. No entanto, você deve sempre avaliar o custo do seu design com base em seus requisitos de armazenamento previstos e adicionar somente entidades duplicadas para dar suporte às consultas que seu aplicativo cliente executará.  
* O valor usado para **RowKey** deve ser exclusivo para cada entidade. Considere o uso de valores de chave composta.  
* Os valores numéricos de preenchimento no **RowKey** (por exemplo, a ID de funcionário 000223) permitem a classificação e a filtragem corretas com base em limites superiores e inferiores.  
* Você não precisa necessariamente duplicar todas as propriedades de sua entidade. Por exemplo, se as consultas que pesquisam as entidades usando o endereço de email no **RowKey** nunca precisarem da idade do funcionário, essas entidades poderão ter a seguinte estrutura:
  
   ![Entidade Employee (índice secundário)](media/storage-table-design-guide/storage-table-design-IMAGE11.png)

* Normalmente, é melhor armazenar dados duplicados e garantir que você possa recuperar todos os dados necessários com uma única consulta do que usar uma consulta para localizar uma entidade usando o índice secundário e outra para pesquisar os dados necessários no índice primário.  

### <a name="when-to-use-this-pattern"></a>Quando utilizar este padrão
Use esse padrão quando o aplicativo cliente precisar recuperar entidades usando uma variedade de chaves diferentes, quando o cliente precisar recuperar entidades em diferentes ordens de classificação, e onde você pode identificar cada entidade usando uma variedade de valores exclusivos. Use esse padrão quando desejar evitar exceder os limites de escalabilidade de partição quando estiver executando pesquisas de entidade usando os diferentes valores de **RowKey** .  

### <a name="related-patterns-and-guidance"></a>Padrões e orientações relacionados
Os padrões e orientações que se seguem podem também ser relevantes ao implementar este padrão:  

* [Padrão de transações eventualmente consistentes](#eventually-consistent-transactions-pattern)  
* [Padrão de índice secundário intra-Partition](#intra-partition-secondary-index-pattern)  
* [Padrão de chave composta](#compound-key-pattern)  
* Transações de grupo de entidades  
* [Trabalhando com tipos de entidade heterogêneas](#working-with-heterogeneous-entity-types)  

## <a name="eventually-consistent-transactions-pattern"></a>Padrão de transações eventualmente consistentes
Habilite comportamento eventualmente consistente entre limites de partição ou limites do sistema de armazenamento usando filas do Azure.  

### <a name="context-and-problem"></a>Contexto e problema
EGTs habilitar transações atômicas em várias entidades que compartilham a mesma chave de partição. Por motivos de desempenho e escalabilidade, você pode decidir armazenar entidades que têm requisitos de consistência em partições separadas ou em um sistema de armazenamento separado: nesse cenário, não é possível usar o EGTs para manter a consistência. Por exemplo, você pode ter um requisito para manter a consistência eventual entre:  

* As entidades armazenadas em duas partições diferentes na mesma tabela, em tabelas diferentes ou em contas de armazenamento diferentes.  
* Uma entidade armazenada no serviço tabela e um blob armazenado no serviço BLOB.  
* Uma entidade armazenada no serviço tabela e um arquivo em um sistema de arquivos.  
* Uma entidade armazenada no serviço tabela ainda indexada usando o serviço de Pesquisa Cognitiva do Azure.  

### <a name="solution"></a>Solução
Usando as filas do Azure, você pode implementar uma solução que fornece consistência eventual em duas ou mais partições ou sistemas de armazenamento.
Para ilustrar essa abordagem, suponha que você tenha um requisito para poder arquivar entidades de funcionário antigas. Entidades de funcionário antigas são raramente consultadas e devem ser excluídas de quaisquer atividades que lidam com os funcionários atuais. Para implementar esse requisito, você armazena os funcionários ativos na tabela **atual** e os funcionários antigos na tabela de **arquivamento** . O arquivamento de um funcionário exige que você exclua a entidade da tabela **atual** e adicione a entidade à tabela de **arquivo morto** , mas não pode usar um EGT para executar essas duas operações. Para evitar o risco de que uma falha faça com que uma entidade apareça em ambas ou nenhuma das tabelas, a operação de arquivamento deve ser eventualmente consistente. O diagrama de sequência a seguir descreve as etapas nesta operação. Mais detalhes são fornecidos para caminhos de exceção no texto a seguir.  

![Solução de filas do Azure](media/storage-table-design-guide/storage-table-design-IMAGE12.png)

Um cliente inicia a operação de arquivamento colocando uma mensagem em uma fila do Azure, neste exemplo, para arquivar #456 de funcionários. Uma função de trabalho pesquisa a fila em busca de novas mensagens; Quando encontra um, ele lê a mensagem e deixa uma cópia oculta na fila. A função de trabalho em seguida busca uma cópia da entidade da tabela **atual** , insere uma cópia na tabela de **arquivo morto** e, em seguida, exclui a original da tabela **atual** . Por fim, se não houvesse nenhum erro nas etapas anteriores, a função de trabalho excluirá a mensagem oculta da fila.  

Neste exemplo, a etapa 4 insere o funcionário na tabela de **arquivo morto** . Ele pode adicionar o funcionário a um blob no serviço BLOB ou a um arquivo em um sistema de arquivos.  

### <a name="recovering-from-failures"></a>Recuperando de falhas
É importante que as operações nas etapas **4** e **5** devam ser *idempotentes* caso a função de trabalho precise reiniciar a operação de arquivamento. Se você estiver usando o serviço tabela, na etapa **4** , você deve usar uma operação "inserir ou substituir"; para a etapa **5** , você deve usar uma operação "excluir se houver" na biblioteca de cliente que você está usando. Se você estiver usando outro sistema de armazenamento, deverá usar uma operação idempotente apropriada.  

Se a função de trabalho nunca concluir a etapa **6**, depois de um tempo limite, a mensagem será exibida novamente na fila pronta para que a função de trabalho tente processá-la novamente. A função de trabalho pode verificar quantas vezes uma mensagem na fila foi lida e, se necessário, sinalizá-la como uma mensagem "suspeita" para investigação, enviando-a para uma fila separada. Para obter mais informações sobre como ler mensagens da fila e verificar a contagem da remoção da fila, consulte [obter mensagens](https://msdn.microsoft.com/library/azure/dd179474.aspx).  

Alguns erros dos serviços tabela e fila são erros transitórios e o aplicativo cliente deve incluir uma lógica de repetição adequada para tratá-los.  

### <a name="issues-and-considerations"></a>Problemas e considerações
Na altura de decidir como implementar este padrão, considere os seguintes pontos:  

* Essa solução não fornece isolamento de transação. Por exemplo, um cliente pode ler as tabelas **atual** e **arquivo morto** quando a função de trabalho estava entre as etapas **4** e **5**e ver uma exibição inconsistente dos dados. Os dados serão consistentes eventualmente.  
* Você deve ter certeza de que as etapas 4 e 5 são idempotentes para garantir a consistência eventual.  
* Você pode dimensionar a solução usando várias filas e instâncias de função de trabalho.  

### <a name="when-to-use-this-pattern"></a>Quando utilizar este padrão
Use esse padrão quando desejar garantir a consistência eventual entre entidades que existem em diferentes partições ou tabelas. Você pode estender esse padrão para garantir a consistência eventual para operações em todo o serviço de tabela e o serviço BLOB e outras fontes de dados de armazenamento não Azure, como o Database ou o sistema de arquivos.  

### <a name="related-patterns-and-guidance"></a>Padrões e orientações relacionados
Os padrões e orientações que se seguem podem também ser relevantes ao implementar este padrão:  

* Transações de grupo de entidades  
* [Mesclar ou substituir](#merge-or-replace)  

> [!NOTE]
> Se o isolamento da transação for importante para sua solução, considere reformular suas tabelas para permitir que você use o EGTs.  
> 
> 

## <a name="index-entities-pattern"></a>Padrão de entidades de índice
Manter entidades de índice para habilitar pesquisas eficientes que retornam listas de entidades.  

### <a name="context-and-problem"></a>Contexto e problema
O serviço tabela indexa automaticamente as entidades usando os valores **PartitionKey** e **RowKey** . Isso permite que um aplicativo cliente recupere uma entidade com eficiência usando uma consulta de ponto. Por exemplo, usando a estrutura de tabela mostrada abaixo, um aplicativo cliente pode recuperar com eficiência uma entidade de funcionário individual usando o nome do departamento e a ID do funcionário ( **PartitionKey** e **RowKey**).  

![Entidade de funcionário](media/storage-table-design-guide/storage-table-design-IMAGE13.png)

Se você também deseja ser capaz de recuperar uma lista de entidades de funcionário com base no valor de outra propriedade não exclusiva, como seu sobrenome, você deve usar uma verificação de partição menos eficiente para localizar correspondências em vez de usar um índice para examiná-las diretamente. Isso ocorre porque o serviço tabela não fornece índices secundários.  

### <a name="solution"></a>Solução
Para habilitar a pesquisa por sobrenome com a estrutura de entidade mostrada acima, você deve manter listas de IDs de funcionários. Se você quiser recuperar as entidades de funcionário com um sobrenome específico, como Jones, deverá primeiro localizar a lista de IDs de funcionários para funcionários com Jones como seu sobrenome e, em seguida, recuperar essas entidades de funcionário. Há três opções principais para armazenar as listas de IDs de funcionários:  

* Use o armazenamento de BLOBs.  
* Crie entidades de índice na mesma partição que as entidades de funcionário.  
* Crie entidades de índice em uma partição ou tabela separada.  

<u>Opção #1: usar o armazenamento de BLOBs</u>  

Para a primeira opção, você cria um blob para cada sobrenome exclusivo e, em cada repositório de BLOB, uma lista dos valores de **PartitionKey** (departamento) e **RowKey** (ID de funcionário) para os funcionários que têm esse sobrenome. Ao adicionar ou excluir um funcionário, você deve garantir que o conteúdo do blob relevante seja eventualmente consistente com as entidades do funcionário.  

<u>Opção #2:</u> Criar entidades de índice na mesma partição  

Para a segunda opção, use as entidades de índice que armazenam os seguintes dados:  

![Entidade de índice do funcionário](media/storage-table-design-guide/storage-table-design-IMAGE14.png)

A propriedade **employeeids** contém uma lista de IDs de funcionários para funcionários com o último nome armazenado no **RowKey**.  

As etapas a seguir descrevem o processo que você deve seguir ao adicionar um novo funcionário se estiver usando a segunda opção. Neste exemplo, estamos adicionando um funcionário com a ID 000152 e um sobrenome Jones no departamento de vendas:  

1. Recupere a entidade de índice com um valor de **PartitionKey** "Sales" e o valor de **RowKey** "Jones". Salve a ETag desta entidade para usar na etapa 2.  
2. Crie uma transação de grupo de entidades (ou seja, uma operação em lote) que insira a nova entidade Employee (valor de**PartitionKey** "Sales" e o valor de **RowKey** "000152") e atualize a entidade de índice (o valor de**PartitionKey** "Sales" e **RowKey** valor "Jones") adicionando a nova ID de funcionário à lista no campo employeeids. Para obter mais informações sobre transações de grupo de entidades, consulte transações de grupo de entidades.  
3. Se a transação do grupo de entidades falhar devido a um erro de simultaneidade otimista (outra pessoa acabou de modificar a entidade de índice), você precisará começar novamente na etapa 1.  

Você pode usar uma abordagem semelhante para excluir um funcionário se estiver usando a segunda opção. Alterar o sobrenome de um funcionário é um pouco mais complexo, pois você precisará executar uma transação de grupo de entidades que atualiza três entidades: a entidade Employee, a entidade de índice para o sobrenome antigo e a entidade index para o novo sobrenome. Você deve recuperar cada entidade antes de fazer qualquer alteração a fim de recuperar os valores de ETag que você pode usar para executar as atualizações usando a simultaneidade otimista.  

As etapas a seguir descrevem o processo que você deve seguir quando precisar pesquisar todos os funcionários com um determinado sobrenome em um departamento se você estiver usando a segunda opção. Neste exemplo, Estamos pesquisando todos os funcionários com o sobrenome Jones no departamento de vendas:  

1. Recupere a entidade de índice com um valor de **PartitionKey** "Sales" e o valor de **RowKey** "Jones".  
2. Analise a lista de IDs de funcionário no campo EmployeeIDs.  
3. Se você precisar de informações adicionais sobre cada um desses funcionários (como seus endereços de email), recupere cada uma das entidades de funcionário usando o valor de **PartitionKey** "vendas" e os valores de **RowKey** da lista de funcionários obtidos na etapa 2.  

<u>Opção #3:</u> Criar entidades de índice em uma partição ou tabela separada  

Para a terceira opção, use entidades de índice que armazenem os seguintes dados:  

![Entidade de índice de funcionário em uma partição separada](media/storage-table-design-guide/storage-table-design-IMAGE15.png)


A propriedade **employeeids** contém uma lista de IDs de funcionários para funcionários com o último nome armazenado no **RowKey**.  

Com a terceira opção, você não pode usar EGTs para manter a consistência porque as entidades de índice estão em uma partição separada das entidades de funcionário. Verifique se as entidades de índice são eventualmente consistentes com as entidades de funcionário.  

### <a name="issues-and-considerations"></a>Problemas e considerações
Na altura de decidir como implementar este padrão, considere os seguintes pontos:  

* Esta solução requer pelo menos duas consultas para recuperar entidades correspondentes: uma para consultar as entidades de índice para obter a lista de valores de **RowKey** e, em seguida, consultas para recuperar cada entidade na lista.  
* Considerando que uma entidade individual tem um tamanho máximo de 1 MB, a opção #2 e a opção #3 na solução pressupõem que a lista de IDs de funcionário para qualquer sobrenome especificado nunca seja maior que 1 MB. Se a lista de IDs de funcionários for provavelmente maior que 1 MB de tamanho, use a opção #1 e armazene os dados de índice no armazenamento de BLOBs.  
* Se você usar a opção #2 (usando EGTs para lidar com a adição e exclusão de funcionários e alterar o sobrenome de um funcionário), será necessário avaliar se o volume de transações abordará os limites de escalabilidade em uma determinada partição. Se esse for o caso, você deve considerar uma solução eventualmente consistente (opção #1 ou #3 de opção) que usa filas para lidar com as solicitações de atualização e permite que você armazene suas entidades de índice em uma partição separada das entidades de funcionário.  
* A opção #2 nesta solução pressupõe que você deseja procurar por sobrenome dentro de um departamento: por exemplo, você deseja recuperar uma lista de funcionários com um sobrenome Jones no departamento de vendas. Se você quiser ser capaz de Pesquisar todos os funcionários com um sobrenome Jones em toda a organização, use a opção #1 ou Option #3.
* Você pode implementar uma solução baseada em fila que fornece consistência eventual (consulte o [padrão de transações eventualmente consistentes](#eventually-consistent-transactions-pattern) para obter mais detalhes).  

### <a name="when-to-use-this-pattern"></a>Quando utilizar este padrão
Use esse padrão quando desejar pesquisar um conjunto de entidades que compartilham um valor de propriedade comum, como todos os funcionários com o sobrenome Jones.  

### <a name="related-patterns-and-guidance"></a>Padrões e orientações relacionados
Os padrões e orientações que se seguem podem também ser relevantes ao implementar este padrão:  

* [Padrão de chave composta](#compound-key-pattern)  
* [Padrão de transações eventualmente consistentes](#eventually-consistent-transactions-pattern)  
* Transações de grupo de entidades  
* [Trabalhando com tipos de entidade heterogêneas](#working-with-heterogeneous-entity-types)  

## <a name="denormalization-pattern"></a>Padrão de desnormalização
Combine dados relacionados juntos em uma única entidade para permitir que você recupere todos os dados necessários com uma única consulta de ponto.  

### <a name="context-and-problem"></a>Contexto e problema
Em um banco de dados relacional, normalmente você normaliza dados para remover a duplicação, resultando em consultas que recuperam dados de várias tabelas. Se você normalizar seus dados nas tabelas do Azure, deverá fazer várias viagens de ida e volta do cliente para o servidor para recuperar os dados relacionados. Por exemplo, com a estrutura de tabela mostrada abaixo, você precisa de duas viagens de ida e volta para recuperar os detalhes de um departamento: um para buscar a entidade de departamento que inclui a ID do gerente e outra solicitação para buscar os detalhes do gerente em uma entidade de funcionário.  

![Entidade de departamento e entidade de funcionário](media/storage-table-design-guide/storage-table-design-IMAGE16.png)

### <a name="solution"></a>Solução
Em vez de armazenar os dados em duas entidades separadas, desnormalizar os dados e manter uma cópia dos detalhes do gerente na entidade Department. Por exemplo:  

![Entidade de departamento](media/storage-table-design-guide/storage-table-design-IMAGE17.png)

Com as entidades de departamento armazenadas com essas propriedades, agora você pode recuperar todos os detalhes necessários sobre um departamento usando uma consulta de ponto.  

### <a name="issues-and-considerations"></a>Problemas e considerações
Na altura de decidir como implementar este padrão, considere os seguintes pontos:  

* Há alguma sobrecarga de custo associada ao armazenamento de alguns dados duas vezes. O benefício de desempenho (resultante de menos solicitações para o serviço de armazenamento) normalmente supera o aumento marginal nos custos de armazenamento (e esse custo é parcialmente compensado por uma redução no número de transações que você precisa para buscar os detalhes de um departamento ).  
* Você deve manter a consistência das duas entidades que armazenam informações sobre os gerentes. Você pode lidar com o problema de consistência usando EGTs para atualizar várias entidades em uma única transação atômica: nesse caso, a entidade Department e a entidade Employee para o gerente do departamento são armazenadas na mesma partição.  

### <a name="when-to-use-this-pattern"></a>Quando utilizar este padrão
Use esse padrão quando precisar pesquisar informações relacionadas com frequência. Esse padrão reduz o número de consultas que o cliente deve fazer para recuperar os dados necessários.  

### <a name="related-patterns-and-guidance"></a>Padrões e orientações relacionados
Os padrões e orientações que se seguem podem também ser relevantes ao implementar este padrão:  

* [Padrão de chave composta](#compound-key-pattern)  
* Transações de grupo de entidades  
* [Trabalhando com tipos de entidade heterogêneas](#working-with-heterogeneous-entity-types)

## <a name="compound-key-pattern"></a>Padrão de chave composta
Use valores **RowKey** compostos para permitir que um cliente pesquise dados relacionados com uma única consulta de ponto.  

### <a name="context-and-problem"></a>Contexto e problema
Em um banco de dados relacional, é natural usar junções em consultas para retornar partes relacionadas de dados para o cliente em uma única consulta. Por exemplo, você pode usar a ID do funcionário para pesquisar uma lista de entidades relacionadas que contenham dados de desempenho e de revisão para esse funcionário.  

Suponha que você esteja armazenando entidades de funcionário no serviço tabela usando a seguinte estrutura:  

![Estrutura de entidade do funcionário](media/storage-table-design-guide/storage-table-design-IMAGE18.png)

Você também precisa armazenar dados históricos relacionados a revisões e desempenho para cada ano que o funcionário trabalhou para sua organização e você precisa ser capaz de acessar essas informações por ano. Uma opção é criar outra tabela que armazena entidades com a seguinte estrutura:  

![Estrutura de entidade de funcionário alternativa](media/storage-table-design-guide/storage-table-design-IMAGE19.png)

Observe que, com essa abordagem, você pode optar por duplicar algumas informações (como nome e sobrenome) na nova entidade para permitir que você recupere seus dados com uma única solicitação. No entanto, você não pode manter a consistência forte porque não pode usar um EGT para atualizar as duas entidades atomicamente.  

### <a name="solution"></a>Solução
Armazene um novo tipo de entidade em sua tabela original usando entidades com a seguinte estrutura:  

![Solução para estrutura de entidade de funcionário](media/storage-table-design-guide/storage-table-design-IMAGE20.png)

Observe como o **RowKey** agora é uma chave composta composta pela ID do funcionário e pelo ano dos dados de revisão que permitem recuperar o desempenho do funcionário e examinar os dados com uma única solicitação para uma única entidade.  

O exemplo a seguir descreve como você pode recuperar todos os dados de revisão de um funcionário específico (como o funcionário 000123 no departamento de vendas):  

$filter = (PartitionKey EQ ' Sales ') e (RowKey ge ' empid_000123 ') e (RowKey lt ' empid_000124 ') & $select = RowKey, gerente de classificação, classificação de pares, comentários  

### <a name="issues-and-considerations"></a>Problemas e considerações
Na altura de decidir como implementar este padrão, considere os seguintes pontos:  

* Você deve usar um caractere separador adequado que torna mais fácil analisar o valor de **RowKey** : por exemplo, **000123_2012**.  
* Você também está armazenando essa entidade na mesma partição que outras entidades que contêm dados relacionados para o mesmo funcionário, o que significa que você pode usar o EGTs para manter a consistência forte.
* Você deve considerar a frequência com que irá consultar os dados para determinar se esse padrão é apropriado.  Por exemplo, se você for acessar os dados de revisão com pouca frequência e os dados principais do funcionário, com frequência, você deverá mantê-los como entidades separadas.  

### <a name="when-to-use-this-pattern"></a>Quando utilizar este padrão
Use esse padrão quando precisar armazenar uma ou mais entidades relacionadas que você consulta com frequência.  

### <a name="related-patterns-and-guidance"></a>Padrões e orientações relacionados
Os padrões e orientações que se seguem podem também ser relevantes ao implementar este padrão:  

* Transações de grupo de entidades  
* [Trabalhando com tipos de entidade heterogêneas](#working-with-heterogeneous-entity-types)  
* [Padrão de transações eventualmente consistentes](#eventually-consistent-transactions-pattern)  

## <a name="log-tail-pattern"></a>Padrão de cauda de log
Recupere as entidades *n* adicionadas mais recentemente a uma partição usando um valor **RowKey** que classifica em ordem de data e hora inversa.  

### <a name="context-and-problem"></a>Contexto e problema
Um requisito comum é poder recuperar as entidades criadas mais recentemente, por exemplo, as 10 declarações de despesas mais recentes enviadas por um funcionário. As consultas de tabela dão suporte a uma **$Top** operação de consulta para retornar as primeiras *n* entidades de um conjunto: não há nenhuma operação de consulta equivalente para retornar as últimas n entidades em um conjunto.  

### <a name="solution"></a>Solução
Armazene as entidades usando um **RowKey** que, naturalmente, classifica em ordem inversa de data/hora usando, portanto, a entrada mais recente é sempre a primeira na tabela.  

Por exemplo, para poder recuperar as 10 solicitações de despesas mais recentes enviadas por um funcionário, você pode usar um valor de tique inverso derivado da data/hora atual. O exemplo C# de código a seguir mostra uma maneira de criar um valor adequado de "tiques invertidos" para um **RowKey** que é classificado do mais recente para o mais antigo:  

`string invertedTicks = string.Format("{0:D19}", DateTime.MaxValue.Ticks - DateTime.UtcNow.Ticks);`  

Você pode voltar ao valor de data e hora usando o seguinte código:  

`DateTime dt = new DateTime(DateTime.MaxValue.Ticks - Int64.Parse(invertedTicks));`  

A consulta de tabela tem esta aparência:  

`https://myaccount.table.core.windows.net/EmployeeExpense(PartitionKey='empid')?$top=10`  

### <a name="issues-and-considerations"></a>Problemas e considerações
Na altura de decidir como implementar este padrão, considere os seguintes pontos:  

* Você deve preencher o valor de tique inverso com zeros à esquerda para garantir que o valor da cadeia de caracteres seja classificado conforme o esperado.  
* Você deve estar ciente das metas de escalabilidade no nível de uma partição. Tenha cuidado para não criar partições de ponto de acesso.  

### <a name="when-to-use-this-pattern"></a>Quando utilizar este padrão
Use esse padrão quando precisar acessar entidades na ordem inversa de data/hora ou quando precisar acessar as entidades adicionadas mais recentemente.  

### <a name="related-patterns-and-guidance"></a>Padrões e orientações relacionados
Os padrões e orientações que se seguem podem também ser relevantes ao implementar este padrão:  

* [Antipadrão de preceder/acrescentar](#prepend-append-anti-pattern)  
* [Recuperando entidades](#retrieving-entities)  

## <a name="high-volume-delete-pattern"></a>Padrão de exclusão de alto volume
Habilite a exclusão de um alto volume de entidades armazenando todas as entidades para exclusão simultânea em sua própria tabela separada; Exclua as entidades excluindo a tabela.  

### <a name="context-and-problem"></a>Contexto e problema
Muitos aplicativos excluem dados antigos que não precisam mais estar disponíveis para um aplicativo cliente ou que o aplicativo tenha Arquivado em outro meio de armazenamento. Normalmente, você identifica esses dados por uma data: por exemplo, você tem um requisito para excluir registros de todas as solicitações de logon com mais de 60 dias.  

Um design possível é usar a data e a hora da solicitação de logon no **RowKey**:  

![Data e hora da tentativa de logon](media/storage-table-design-guide/storage-table-design-IMAGE21.png)

Essa abordagem evita pontos de acesso de partição porque o aplicativo pode inserir e excluir entidades de logon para cada usuário em uma partição separada. No entanto, essa abordagem pode ser dispendiosa e demorada se você tiver um grande número de entidades, pois primeiro você precisa executar uma verificação de tabela para identificar todas as entidades a serem excluídas e, em seguida, deve excluir cada entidade antiga. Você pode reduzir o número de viagens de ida e volta ao servidor necessário para excluir as entidades antigas enviando várias solicitações de exclusão para o EGTs.  

### <a name="solution"></a>Solução
Use uma tabela separada para cada dia das tentativas de logon. Você pode usar o design de entidade acima para evitar pontos de início quando estiver inserindo entidades, e a exclusão de entidades antigas agora é simplesmente uma questão de excluir uma tabela todos os dias (uma única operação de armazenamento) em vez de localizar e excluir centenas e milhares de pessoas entidades de logon todos os dias.  

### <a name="issues-and-considerations"></a>Problemas e considerações
Na altura de decidir como implementar este padrão, considere os seguintes pontos:  

* O design dá suporte a outras maneiras em que seu aplicativo usará os dados, como Pesquisar entidades específicas, vincular a outros dados ou gerar informações agregadas?  
* O design evita pontos de acesso quando você está inserindo novas entidades?  
* Espere um atraso se você quiser reutilizar o mesmo nome de tabela depois de excluí-lo. É melhor usar sempre nomes de tabela exclusivos.  
* Espere alguma limitação ao usar pela primeira vez uma nova tabela, enquanto o serviço tabela aprende os padrões de acesso e distribui as partições entre os nós. Você deve considerar a frequência com que precisa criar novas tabelas.  

### <a name="when-to-use-this-pattern"></a>Quando utilizar este padrão
Use esse padrão quando tiver um alto volume de entidades que você deve excluir ao mesmo tempo.  

### <a name="related-patterns-and-guidance"></a>Padrões e orientações relacionados
Os padrões e orientações que se seguem podem também ser relevantes ao implementar este padrão:  

* Transações de grupo de entidades
* [Modificando entidades](#modifying-entities)  

## <a name="data-series-pattern"></a>Padrão de série de dados
Armazene séries completas de dados em uma única entidade para minimizar o número de solicitações feitas.  

### <a name="context-and-problem"></a>Contexto e problema
Um cenário comum é que um aplicativo armazene uma série de dados que ele normalmente precisa recuperar de uma só vez. Por exemplo, seu aplicativo pode registrar quantas mensagens INSTANTÂNEAs cada funcionário envia a cada hora e, em seguida, usar essas informações para plotar quantas mensagens cada usuário enviou nas 24 horas anteriores. Um design pode ser armazenar 24 entidades para cada funcionário:  

![Armazenar 24 entidades para cada funcionário](media/storage-table-design-guide/storage-table-design-IMAGE22.png)

Com esse design, você pode facilmente localizar e atualizar a entidade a ser atualizada para cada funcionário sempre que o aplicativo precisar atualizar o valor da contagem de mensagens. No entanto, para recuperar as informações para plotar um gráfico da atividade das 24 horas anteriores, você deve recuperar 24 entidades.  

### <a name="solution"></a>Solução
Use o design a seguir com uma propriedade separada para armazenar a contagem de mensagens para cada hora:  

![Entidade de estatísticas de mensagem](media/storage-table-design-guide/storage-table-design-IMAGE23.png)

Com esse design, você pode usar uma operação de mesclagem para atualizar a contagem de mensagens para um funcionário por uma hora específica. Agora, você pode recuperar todas as informações necessárias para plotar o gráfico usando uma solicitação para uma única entidade.  

### <a name="issues-and-considerations"></a>Problemas e considerações
Na altura de decidir como implementar este padrão, considere os seguintes pontos:  

* Se sua série de dados completa não couber em uma única entidade (uma entidade pode ter até 252 Propriedades), use um armazenamento de dados alternativo, como um blob.  
* Se você tiver vários clientes atualizando uma entidade simultaneamente, será necessário usar a **ETag** para implementar a simultaneidade otimista. Se você tiver muitos clientes, poderá enfrentar uma alta contenção.  

### <a name="when-to-use-this-pattern"></a>Quando utilizar este padrão
Use esse padrão quando precisar atualizar e recuperar uma série de dados associada a uma entidade individual.  

### <a name="related-patterns-and-guidance"></a>Padrões e orientações relacionados
Os padrões e orientações que se seguem podem também ser relevantes ao implementar este padrão:  

* [Padrão de entidades grandes](#large-entities-pattern)  
* [Mesclar ou substituir](#merge-or-replace)  
* [Padrão de transações eventualmente consistentes](#eventually-consistent-transactions-pattern) (se você estiver armazenando a série de dados em um blob)  

## <a name="wide-entities-pattern"></a>Padrão de entidades largas
Use várias entidades físicas para armazenar entidades lógicas com mais de 252 Propriedades.  

### <a name="context-and-problem"></a>Contexto e problema
Uma entidade individual pode ter até 252 Propriedades (excluindo as propriedades obrigatórias do sistema) e não pode armazenar mais de 1 MB de dados no total. Em um banco de dados relacional, normalmente você pode arredondar os limites do tamanho de uma linha adicionando uma nova tabela e impondo uma relação de 1 para 1 entre elas.  

### <a name="solution"></a>Solução
Usando o serviço tabela, você pode armazenar várias entidades para representar um único objeto de negócios grande com mais de 252 Propriedades. Por exemplo, se você quiser armazenar uma contagem do número de mensagens INSTANTÂNEAs enviadas por cada funcionário pelos últimos 365 dias, poderá usar o design a seguir que usa duas entidades com esquemas diferentes:  

![Várias entidades](media/storage-table-design-guide/storage-table-design-IMAGE24.png)

Se você precisar fazer uma alteração que exija a atualização de ambas as entidades para mantê-las sincronizadas entre si, você poderá usar um EGT. Caso contrário, você pode usar uma única operação de mesclagem para atualizar a contagem de mensagens de um dia específico. Para recuperar todos os dados de um funcionário individual, você deve recuperar ambas as entidades, que podem ser feitas com duas solicitações eficientes que usam um valor de **PartitionKey** e **RowKey** .  

### <a name="issues-and-considerations"></a>Problemas e considerações
Na altura de decidir como implementar este padrão, considere os seguintes pontos:  

* A recuperação de uma entidade lógica completa envolve pelo menos duas transações de armazenamento: uma para recuperar cada entidade física.  

### <a name="when-to-use-this-pattern"></a>Quando utilizar este padrão
Use esse padrão quando precisar armazenar entidades cujo tamanho ou número de propriedades exceda os limites de uma entidade individual no serviço tabela.  

### <a name="related-patterns-and-guidance"></a>Padrões e orientações relacionados
Os padrões e orientações que se seguem podem também ser relevantes ao implementar este padrão:  

* Transações de grupo de entidades
* [Mesclar ou substituir](#merge-or-replace)

## <a name="large-entities-pattern"></a>Padrão de entidades grandes
Use o armazenamento de BLOBs para armazenar grandes valores de propriedade.  

### <a name="context-and-problem"></a>Contexto e problema
Uma entidade individual não pode armazenar mais de 1 MB de dados no total. Se uma ou várias das suas propriedades armazenarem valores que fazem com que o tamanho total da entidade exceda esse valor, você não poderá armazenar a entidade inteira no serviço tabela.  

### <a name="solution"></a>Solução
Se sua entidade exceder 1 MB de tamanho porque uma ou mais propriedades contêm uma grande quantidade de dados, você poderá armazenar dados no serviço BLOB e, em seguida, armazenar o endereço do blob em uma propriedade na entidade. Por exemplo, você pode armazenar a foto de um funcionário no armazenamento de BLOBs e armazenar um link para a foto na propriedade **Photo** da sua entidade Employee:  

![Propriedade Photo](media/storage-table-design-guide/storage-table-design-IMAGE25.png)

### <a name="issues-and-considerations"></a>Problemas e considerações
Na altura de decidir como implementar este padrão, considere os seguintes pontos:  

* Para manter a consistência eventual entre a entidade no serviço tabela e os dados no serviço BLOB, use o [padrão de transações eventualmente consistentes](#eventually-consistent-transactions-pattern) para manter suas entidades.
* A recuperação de uma entidade completa envolve pelo menos duas transações de armazenamento: uma para recuperar a entidade e outra para recuperar os dados do blob.  

### <a name="when-to-use-this-pattern"></a>Quando utilizar este padrão
Use esse padrão quando precisar armazenar entidades cujo tamanho exceda os limites de uma entidade individual no serviço tabela.  

### <a name="related-patterns-and-guidance"></a>Padrões e orientações relacionados
Os padrões e orientações que se seguem podem também ser relevantes ao implementar este padrão:  

* [Padrão de transações eventualmente consistentes](#eventually-consistent-transactions-pattern)  
* [Padrão de entidades largas](#wide-entities-pattern)

<a name="prepend-append-anti-pattern"></a>

## <a name="prependappend-anti-pattern"></a>Antipadrão de preceder/acrescentar
Aumente a escalabilidade quando tiver um alto volume de inserções distribuindo as inserções entre várias partições.  

### <a name="context-and-problem"></a>Contexto e problema
O prependência ou o acréscimo de entidades às entidades armazenadas normalmente resulta no aplicativo adicionando novas entidades à primeira ou à última partição de uma sequência de partições. Nesse caso, todas as inserções em um determinado momento estão ocorrendo na mesma partição, criando um ponto de acesso que impede que o serviço tabela faça o balanceamento de carga de inserções em vários nós e, possivelmente, fazendo com que seu aplicativo atinja as metas de escalabilidade para particion. Por exemplo, se você tiver um aplicativo que registra o acesso de rede e recursos por funcionários, uma estrutura de entidade, como mostrado abaixo, pode resultar na partição da hora atual se tornando um ponto de acesso se o volume de transações atingir o destino de escalabilidade para um partição individual:  

![Estrutura de entidade](media/storage-table-design-guide/storage-table-design-IMAGE26.png)

### <a name="solution"></a>Solução
A seguinte estrutura de entidade alternativa evita um ponto de HotSpot em qualquer partição específica, pois o aplicativo registra eventos:  

![Estrutura de entidade alternativa](media/storage-table-design-guide/storage-table-design-IMAGE27.png)

Observe que, com este exemplo, o **PartitionKey** e o **RowKey** são chaves compostas. O **PartitionKey** usa a ID do departamento e do funcionário para distribuir o registro em log entre várias partições.  

### <a name="issues-and-considerations"></a>Problemas e considerações
Na altura de decidir como implementar este padrão, considere os seguintes pontos:  

* A estrutura de chave alternativa que evita a criação de partições ativas em inserções dá suporte eficiente às consultas que seu aplicativo cliente faz?  
* O volume previsto de transações significa que é provável que você atinja as metas de escalabilidade de uma partição individual e seja limitado pelo serviço de armazenamento?  

### <a name="when-to-use-this-pattern"></a>Quando utilizar este padrão
Evite o antipadrão preceder/acrescentar quando o volume de transações provavelmente resultará em limitação pelo serviço de armazenamento quando você acessar uma partição ativa.  

### <a name="related-patterns-and-guidance"></a>Padrões e orientações relacionados
Os padrões e orientações que se seguem podem também ser relevantes ao implementar este padrão:  

* [Padrão de chave composta](#compound-key-pattern)  
* [Padrão de cauda de log](#log-tail-pattern)  
* [Modificando entidades](#modifying-entities)  

## <a name="log-data-anti-pattern"></a>Antipadrão de dados de log
Normalmente, você deve usar o serviço blob em vez do serviço tabela para armazenar dados de log.  

### <a name="context-and-problem"></a>Contexto e problema
Um caso de uso comum para dados de log é recuperar uma seleção de entradas de log para um intervalo de data/hora específico: por exemplo, você deseja localizar todas as mensagens de erro e críticas que o aplicativo registrou entre 15:04 e 15:06 em uma data específica. Você não deseja usar a data e a hora da mensagem de log para determinar a partição para a qual você salva as entidades de log: isso resulta em uma partição ativa porque, a qualquer momento, todas as entidades de log compartilharão o mesmo valor **PartitionKey** (consulte a seção [preceder/ acrescentar antipadrão](#prepend-append-anti-pattern)). Por exemplo, o seguinte esquema de entidade para uma mensagem de log resulta em uma partição ativa porque o aplicativo grava todas as mensagens de log na partição para a data e hora atuais:  

![Entidade de mensagem de log](media/storage-table-design-guide/storage-table-design-IMAGE28.png)

Neste exemplo, o **RowKey** inclui a data e a hora da mensagem de log para garantir que as mensagens de log sejam armazenadas classificadas em ordem de data/hora e inclua uma ID de mensagem caso várias mensagens de log compartilhem a mesma data e hora.  

Outra abordagem é usar um **PartitionKey** que garanta que o aplicativo grave mensagens em um intervalo de partições. Por exemplo, se a origem da mensagem de log fornecer uma maneira de distribuir mensagens em várias partições, você poderá usar o seguinte esquema de entidade:  

![Entidade de mensagem de log alternativa](media/storage-table-design-guide/storage-table-design-IMAGE29.png)

No entanto, o problema com esse esquema é que, para recuperar todas as mensagens de log de um período de tempo específico, você deve pesquisar todas as partições na tabela.

### <a name="solution"></a>Solução
A seção anterior realçou o problema de tentar usar o serviço tabela para armazenar entradas de log e sugerir dois designs insatisfatórios. Uma solução levou a uma partição ativa com o risco de mau desempenho na gravação de mensagens de log; a outra solução resultou em um desempenho de consulta insatisfatório devido ao requisito de verificar cada partição na tabela para recuperar mensagens de log para um período de tempo específico. O armazenamento de blob oferece uma solução melhor para esse tipo de cenário e é assim que Análise de Armazenamento do Azure armazena os dados de log coletados.  

Esta seção descreve como Análise de Armazenamento armazena os dados de log no armazenamento de BLOBs como uma ilustração dessa abordagem para armazenar dados que você normalmente consulta por intervalo.  

O Análise de Armazenamento armazena mensagens de log em um formato delimitado em vários BLOBs. O formato delimitado torna mais fácil para um aplicativo cliente analisar os dados na mensagem de log.  

Análise de Armazenamento usa uma Convenção de nomenclatura para BLOBs que permite localizar o blob (ou BLOBs) que contém as mensagens de log para as quais você está pesquisando. Por exemplo, um blob chamado "Queue/2014/07/31/1800/000001. log" contém mensagens de log relacionadas ao serviço fila para a hora a partir de 18:00 em 31 de julho de 2014. O "000001" indica que este é o primeiro arquivo de log para esse período. O Análise de Armazenamento também registra os carimbos de data/hora das mensagens do primeiro e do último log armazenadas no arquivo como parte dos metadados do blob. A API para armazenamento de BLOBs permite localizar BLOBs em um contêiner com base em um prefixo de nome: para localizar todos os blobs que contêm dados de log de fila para a hora que começa em 18:00, você pode usar o prefixo "Queue/2014/07/31/1800."  

O Análise de Armazenamento armazena em buffer mensagens de log internamente e, em seguida, atualiza periodicamente o blob apropriado ou cria um novo com o lote mais recente de entradas de log. Isso reduz o número de gravações que ele deve executar para o serviço BLOB.  

Se você estiver implementando uma solução semelhante em seu próprio aplicativo, deverá considerar como gerenciar a compensação entre a confiabilidade (gravando cada entrada de log no armazenamento de BLOBs conforme ele acontece) e o custo e a escalabilidade (armazenando em buffer as atualizações em seu aplicativo e gravando eles para o armazenamento de BLOBs em lotes).  

### <a name="issues-and-considerations"></a>Problemas e considerações
Considere os seguintes pontos ao decidir como armazenar dados de log:  

* Se você criar um design de tabela que evite possíveis partições Hot, talvez você ache que não pode acessar seus dados de log com eficiência.  
* Para processar dados de log, um cliente geralmente precisa carregar muitos registros.  
* Embora os dados de log sejam geralmente estruturados, o armazenamento de BLOBs pode ser uma solução melhor.  

## <a name="implementation-considerations"></a>Considerações de implementação
Esta seção aborda algumas das considerações que você deve ter em mente ao implementar os padrões descritos nas seções anteriores. A maior parte desta seção usa exemplos escritos C# em que usam a biblioteca de cliente de armazenamento (versão do 4.3.0 no momento da gravação).  

## <a name="retrieving-entities"></a>Recuperando entidades
Conforme discutido na seção design para consulta, a consulta mais eficiente é uma consulta de ponto. No entanto, em alguns cenários, talvez seja necessário recuperar várias entidades. Esta seção descreve algumas abordagens comuns para recuperar entidades usando a biblioteca de cliente de armazenamento.  

### <a name="executing-a-point-query-using-the-storage-client-library"></a>Executando uma consulta de ponto usando a biblioteca de cliente de armazenamento
A maneira mais fácil de executar uma consulta de ponto é usar a operação de **recuperação** de tabela, conforme mostrado C# no trecho de código a seguir, que recupera uma entidade com um **PartitionKey** de valor "Sales" e um **RowKey** de valor "212":  

```csharp
TableOperation retrieveOperation = TableOperation.Retrieve<EmployeeEntity>("Sales", "212");
var retrieveResult = employeeTable.Execute(retrieveOperation);
if (retrieveResult.Result != null)
{
    EmployeeEntity employee = (EmployeeEntity)retrieveResult.Result;
    ...
}  
```

Observe como este exemplo espera que a entidade que ele recupera seja do tipo **EmployeeEntity**.  

### <a name="retrieving-multiple-entities-using-linq"></a>Recuperando várias entidades usando o LINQ
Você pode usar o LINQ para recuperar várias entidades do serviço tabela ao trabalhar com Microsoft Azure biblioteca padrão da tabela Cosmos. 

```cli
dotnet add package Microsoft.Azure.Cosmos.Table
```

Para que os exemplos a seguir funcionem, você precisará incluir namespaces:

```csharp
using System.Linq;
using Microsoft.Azure.Cosmos.Table;
using Microsoft.Azure.Cosmos.Table.Queryable;
```

EmployeeTable é um objeto Cloudtable que implementa um método CreateQuery\<ITableEntity > (), que retorna um TableQuery\<ITableEntity >. Objetos desse tipo implementam um IQueryable e permitem o uso de expressões de consulta LINQ e a sintaxe de notação de ponto.

Recuperar várias entidades e ser obtido especificando uma consulta com uma cláusula **Where** . Para evitar uma verificação de tabela, você deve sempre incluir o valor **PartitionKey** na cláusula WHERE e, se possível, o valor de **RowKey** para evitar verificações de tabela e de partição. O serviço tabela dá suporte a um conjunto limitado de operadores de comparação (maior que, maior ou igual, menor que, menor que ou igual a, igual e diferente) para usar na cláusula WHERE. 

O trecho C# de código a seguir localiza todos os funcionários cujo sobrenome começa com "B" (supondo que o **RowKey** armazena o sobrenome) no departamento de vendas (supondo que o **PartitionKey** armazene o nome do departamento):  

```csharp
TableQuery<EmployeeEntity> employeeQuery = employeeTable.CreateQuery<EmployeeEntity>();
var query = (from employee in employeeQuery
            where employee.PartitionKey == "Sales" &&
            employee.RowKey.CompareTo("B") >= 0 &&
            employee.RowKey.CompareTo("C") < 0
            select employee).AsTableQuery();
            
var employees = query.Execute();  
```

Observe como a consulta especifica um **RowKey** e um **PartitionKey** para garantir um melhor desempenho.  

O exemplo de código a seguir mostra a funcionalidade equivalente sem usar a sintaxe LINQ:  

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
> O exemplo Aninha vários métodos **CombineFilters** para incluir as três condições de filtro.  
> 
> 

### <a name="retrieving-large-numbers-of-entities-from-a-query"></a>Recuperando grandes números de entidades de uma consulta
Uma consulta ideal retorna uma entidade individual com base em um valor de **PartitionKey** e um valor de **RowKey** . No entanto, em alguns cenários, você pode ter um requisito para retornar muitas entidades da mesma partição ou até mesmo de muitas partições.  

Você deve sempre testar completamente o desempenho do seu aplicativo nesses cenários.  

Uma consulta no serviço tabela pode retornar um máximo de 1.000 entidades ao mesmo tempo e pode ser executada por um máximo de cinco segundos. Se o conjunto de resultados contiver mais de 1.000 entidades, se a consulta não tiver sido concluída em cinco segundos ou se a consulta cruzar o limite da partição, o serviço tabela retornará um token de continuação para permitir que o aplicativo cliente solicite o próximo conjunto de entidades. Para obter mais informações sobre como os tokens de continuação funcionam, consulte [tempo limite de consulta e paginação](https://msdn.microsoft.com/library/azure/dd135718.aspx).  

Se você estiver usando a biblioteca de cliente de armazenamento, ela poderá manipular automaticamente os tokens de continuação para você, pois ele retorna entidades do serviço tabela. O exemplo C# de código a seguir usando a biblioteca de cliente de armazenamento manipula automaticamente os tokens de continuação se o serviço de tabela os retornar em uma resposta:  

```csharp
string filter = TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Sales");
TableQuery<EmployeeEntity> employeeQuery = new TableQuery<EmployeeEntity>().Where(filter);

var employees = employeeTable.ExecuteQuery(employeeQuery);
foreach (var emp in employees)
{
    // ...
}  
```

O código C# a seguir trata os tokens de continuação explicitamente:  

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

Usando tokens de continuação explicitamente, você pode controlar quando seu aplicativo recupera o próximo segmento de dados. Por exemplo, se o aplicativo cliente permitir que os usuários percorram as entidades armazenadas em uma tabela, um usuário poderá decidir não paginar todas as entidades recuperadas pela consulta para que seu aplicativo use apenas um token de continuação para recuperar o próximo segmento quando o usuário concluiu a paginação por todas as entidades no segmento atual. Essa abordagem tem vários benefícios:  

* Ele permite limitar a quantidade de dados a serem recuperados do serviço tabela e que você move pela rede.  
* Ele permite que você execute a e/s assíncrona no .NET.  
* Ele permite serializar o token de continuação para o armazenamento persistente para que você possa continuar no caso de uma falha de aplicativo.  

> [!NOTE]
> Um token de continuação normalmente retorna um segmento que contém 1.000 entidades, embora possa ser menos. Esse também é o caso se você limitar o número de entradas que uma consulta retorna usando **Take** para retornar as primeiras n entidades que correspondem aos seus critérios de pesquisa: o serviço tabela pode retornar um segmento contendo menos de n entidades junto com um token de continuação para permite que você recupere as entidades restantes.  
> 
> 

O código C# a seguir mostra como modificar o número de entidades retornadas dentro de um segmento:  

```csharp
employeeQuery.TakeCount = 50;  
```

### <a name="server-side-projection"></a>Projeção do lado do servidor
Uma única entidade pode ter até 255 Propriedades e ter até 1 MB de tamanho. Quando você consulta a tabela e recupera entidades, talvez não precise de todas as propriedades e pode evitar a transferência de dados desnecessariamente (para ajudar a reduzir a latência e o custo). Você pode usar a projeção do lado do servidor para transferir apenas as propriedades necessárias. O exemplo a seguir recupera apenas a propriedade **email** (juntamente com **PartitionKey**, **RowKey**, **timestamp**e **ETag**) das entidades selecionadas pela consulta.  

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

Observe como o valor **RowKey** está disponível, embora não tenha sido incluído na lista de propriedades a serem recuperados.  

## <a name="modifying-entities"></a>Modificando entidades
A biblioteca de cliente de armazenamento permite que você modifique suas entidades armazenadas no serviço tabela inserindo, excluindo e atualizando entidades. Você pode usar o EGTs para agrupar várias operações de inserção, atualização e exclusão para reduzir o número de viagens de ida e volta necessárias e melhorar o desempenho da solução.  

As exceções geradas quando a biblioteca de cliente de armazenamento executa um EGT normalmente incluem o índice da entidade que causou a falha do lote. Isso é útil quando você está depurando código que usa EGTs.  

Você também deve considerar como seu design afeta como o aplicativo cliente lida com operações de atualização e simultaneidade.  

### <a name="managing-concurrency"></a>Gerir a simultaneidade
Por padrão, o serviço tabela implementa verificações de simultaneidade otimistas no nível de entidades individuais para operações de **inserção**, **mesclagem**e **exclusão** , embora seja possível que um cliente Force o serviço de tabela a ignorá-las procedimento. Para obter mais informações sobre como o serviço tabela gerencia a simultaneidade, consulte [Gerenciando a simultaneidade no armazenamento do Microsoft Azure](../../storage/common/storage-concurrency.md).  

### <a name="merge-or-replace"></a>Mesclar ou substituir
O método **replace** da classe **TableOperation** sempre substitui a entidade completa no serviço tabela. Se você não incluir uma propriedade na solicitação quando essa propriedade existir na entidade armazenada, a solicitação removerá essa propriedade da entidade armazenada. A menos que você queira remover uma propriedade explicitamente de uma entidade armazenada, você deve incluir todas as propriedades na solicitação.  

Você pode usar o método **Merge** da classe **TableOperation** para reduzir a quantidade de dados enviados para o serviço tabela quando desejar atualizar uma entidade. O método **Merge** substitui todas as propriedades na entidade armazenada por valores de propriedade da entidade incluída na solicitação, mas deixa intactas todas as propriedades na entidade armazenada que não estão incluídas na solicitação. Isso será útil se você tiver grandes entidades e precisar apenas atualizar um pequeno número de propriedades em uma solicitação.  

> [!NOTE]
> Os métodos **replace** e **Merge** falharão se a entidade não existir. Como alternativa, você pode usar os métodos **InsertOrReplace** e **InsertOrMerge** que criam uma nova entidade, caso ela não exista.  
> 
> 

## <a name="working-with-heterogeneous-entity-types"></a>Trabalhando com tipos de entidade heterogêneas
O serviço tabela é um repositório *de tabela sem esquema* que significa que uma única tabela pode armazenar entidades de vários tipos, proporcionando grande flexibilidade ao seu design. O exemplo a seguir ilustra uma tabela que armazena as entidades Employee e Department:  

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
<th>firstName</th>
<th>lastName</th>
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
<th>firstName</th>
<th>lastName</th>
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
<th>firstName</th>
<th>lastName</th>
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

Cada entidade ainda deve ter valores de **PartitionKey**, **RowKey**e **timestamp** , mas pode ter qualquer conjunto de propriedades. Além disso, não há nada para indicar o tipo de uma entidade, a menos que você opte por armazenar essas informações em algum lugar. Há duas opções para identificar o tipo de entidade:  

* Preceda o tipo de entidade para **RowKey** (ou possivelmente o **PartitionKey**). Por exemplo, **EMPLOYEE_000123** ou **DEPARTMENT_SALES** como valores de **RowKey** .  
* Use uma propriedade separada para registrar o tipo de entidade, conforme mostrado na tabela a seguir.  

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
<th>firstName</th>
<th>lastName</th>
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
<th>EntityType</th>
<th>firstName</th>
<th>lastName</th>
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
<th>EntityType</th>
<th>DepartmentName</th>
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
<th>EntityType</th>
<th>firstName</th>
<th>lastName</th>
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

A primeira opção, pretendo o tipo de entidade para o **RowKey**, será útil se houver uma possibilidade de que duas entidades de tipos diferentes possam ter o mesmo valor de chave. Ele também agrupa entidades do mesmo tipo juntas na partição.  

As técnicas discutidas nesta seção são especialmente relevantes para as [relações de herança](table-storage-design-modeling.md#inheritance-relationships) de discussão anteriormente neste guia do artigo [relações de modelagem](table-storage-design-modeling.md).  

> [!NOTE]
> Você deve considerar a inclusão de um número de versão no valor do tipo de entidade para permitir que aplicativos cliente evoluam objetos POCO e trabalhem com versões diferentes.  
> 
> 

O restante desta seção descreve alguns dos recursos na biblioteca de cliente de armazenamento que facilitam o trabalho com vários tipos de entidade na mesma tabela.  

### <a name="retrieving-heterogeneous-entity-types"></a>Recuperando tipos de entidade heterogênea
Se você estiver usando a biblioteca de cliente de armazenamento, terá três opções para trabalhar com vários tipos de entidade.  

Se você souber o tipo da entidade armazenada com um **RowKey** e valores de **PartitionKey** específicos, poderá especificar o tipo de entidade ao recuperar a entidade, conforme mostrado nos dois exemplos anteriores que recuperam entidades do tipo **EmployeeEntity** : [Executando uma consulta de ponto usando a biblioteca de cliente de armazenamento](#executing-a-point-query-using-the-storage-client-library) e [recuperando várias entidades usando LINQ](#retrieving-multiple-entities-using-linq).  

A segunda opção é usar o tipo **DynamicTableEntity** (um recipiente de propriedades) em vez de um tipo de entidade poco concreto (essa opção também pode melhorar o desempenho porque não há necessidade de serializar e desserializar a entidade para tipos .net). O código C# a seguir recupera potencialmente várias entidades de tipos diferentes da tabela, mas retorna todas as entidades como instâncias de **DynamicTableEntity** . Em seguida, ele usa a propriedade **EntityType** para determinar o tipo de cada entidade:  

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

Para recuperar outras propriedades, você deve usar o método **TryGetValue** na propriedade **Properties** da classe **DynamicTableEntity** .  

Uma terceira opção é combinar usando o tipo **DynamicTableEntity** e uma instância **EntityResolver** . Isso permite que você resolva para vários tipos de POCO na mesma consulta. Neste exemplo, o delegado **EntityResolver** está usando a propriedade **EntityType** para distinguir entre os dois tipos de entidade que a consulta retorna. O método **resolve** usa o delegado do **resolvedor** para resolver instâncias de **DynamicTableEntity** para instâncias **TableEntity** .  

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

### <a name="modifying-heterogeneous-entity-types"></a>Modificando tipos de entidade heterogênea
Você não precisa saber o tipo de uma entidade para excluí-la e sempre sabe o tipo de uma entidade ao inseri-la. No entanto, você pode usar o tipo **DynamicTableEntity** para atualizar uma entidade sem saber seu tipo e sem usar uma classe de entidade poco. O exemplo de código a seguir recupera uma única entidade e verifica se a propriedade **EmployeeCount** existe antes de atualizá-la.  

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

## <a name="controlling-access-with-shared-access-signatures"></a>Controlando o acesso com assinaturas de acesso compartilhado
Você pode usar tokens de SAS (assinatura de acesso compartilhado) para permitir que os aplicativos cliente modifiquem (e consultem) as entidades de tabela sem a necessidade de incluir sua chave de conta de armazenamento em seu código. Normalmente, há três benefícios principais no uso de SAS em seu aplicativo:  

* Você não precisa distribuir sua chave de conta de armazenamento para uma plataforma não segura (como um dispositivo móvel) para permitir que esse dispositivo acesse e modifique entidades no serviço tabela.  
* Você pode descarregar parte do trabalho que as funções da Web e de trabalho executam no gerenciamento de suas entidades para dispositivos cliente, como computadores de usuário final e dispositivos móveis.  
* Você pode atribuir um conjunto de permissões restritas e limitadas por tempo a um cliente (como permitir acesso somente leitura a recursos específicos).  

Para obter mais informações sobre como usar tokens SAS com o serviço tabela, consulte [usando assinaturas de acesso compartilhado (SAS)](../../storage/common/storage-sas-overview.md).  

No entanto, você ainda deve gerar os tokens SAS que concedem a um aplicativo cliente as entidades no serviço tabela: você deve fazer isso em um ambiente que tenha acesso seguro às suas chaves de conta de armazenamento. Normalmente, você usa uma função Web ou de trabalho para gerar os tokens SAS e fornecê-los aos aplicativos cliente que precisam de acesso às suas entidades. Como ainda há uma sobrecarga envolvida na geração e entrega de tokens SAS aos clientes, você deve considerar a melhor maneira de reduzir essa sobrecarga, especialmente em cenários de alto volume.  

É possível gerar um token SAS que concede acesso a um subconjunto de entidades em uma tabela. Por padrão, você cria um token SAS para uma tabela inteira, mas também é possível especificar que o token SAS conceda acesso a um intervalo de valores **PartitionKey** ou a um intervalo de valores **PartitionKey** e **RowKey** . Você pode optar por gerar tokens SAS para usuários individuais do seu sistema, de modo que o token SAS de cada usuário só permita acesso às suas próprias entidades no serviço tabela.  

## <a name="asynchronous-and-parallel-operations"></a>Operações assíncronas e paralelas
Desde que você esteja distribuindo suas solicitações entre várias partições, você pode melhorar a taxa de transferência e a capacidade de resposta do cliente usando consultas assíncronas ou paralelas.
Por exemplo, você pode ter duas ou mais instâncias de função de trabalho acessando suas tabelas em paralelo. Você pode ter funções de trabalho individuais responsáveis por determinados conjuntos de partições ou simplesmente ter várias instâncias de função de trabalho, cada uma capaz de acessar todas as partições em uma tabela.  

Em uma instância de cliente, você pode melhorar a taxa de transferência executando operações de armazenamento de forma assíncrona. A biblioteca de cliente de armazenamento facilita a gravação de consultas e modificações assíncronas. Por exemplo, você pode começar com o método síncrono que recupera todas as entidades em uma partição, conforme mostrado no código C# a seguir:  

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

Você pode facilmente modificar esse código para que a consulta seja executada de forma assíncrona da seguinte maneira:  

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

Neste exemplo assíncrono, você pode ver as seguintes alterações da versão síncrona:  

* A assinatura do método agora inclui o modificador **Async** e retorna uma instância de **tarefa** .  
* Em vez de chamar o método **ExecuteSegmented** para recuperar os resultados, o método agora chama o método **ExecuteSegmentedAsync** e usa o modificador **Await** para recuperar os resultados de forma assíncrona.  

O aplicativo cliente pode chamar esse método várias vezes (com valores diferentes para o parâmetro **Department** ) e cada consulta será executada em um thread separado.  

Não há nenhuma versão assíncrona do método **Execute** na classe **TableQuery** porque a interface **IEnumerable** não oferece suporte à enumeração assíncrona.  

Você também pode inserir, atualizar e excluir entidades de forma assíncrona. O exemplo C# a seguir mostra um método síncrono simples para inserir ou substituir uma entidade Employee:  

```csharp
private static void SimpleEmployeeUpsert(
    CloudTable employeeTable,
    EmployeeEntity employee)
{
    TableResult result = employeeTable.Execute(TableOperation.InsertOrReplace(employee));
    Console.WriteLine("HTTP Status: {0}", result.HttpStatusCode);
}  
```

Você pode facilmente modificar esse código para que a atualização seja executada de forma assíncrona da seguinte maneira:  

```csharp
private static async Task SimpleEmployeeUpsertAsync(
    CloudTable employeeTable,
    EmployeeEntity employee)
{
    TableResult result = await employeeTable.ExecuteAsync(TableOperation.InsertOrReplace(employee));
    Console.WriteLine("HTTP Status: {0}", result.HttpStatusCode);
}  
```

Neste exemplo assíncrono, você pode ver as seguintes alterações da versão síncrona:  

* A assinatura do método agora inclui o modificador **Async** e retorna uma instância de **tarefa** .  
* Em vez de chamar o método **Execute** para atualizar a entidade, o método agora chama o método **ExecuteAsync** e usa o modificador **Await** para recuperar os resultados de forma assíncrona.  

O aplicativo cliente pode chamar vários métodos assíncronos como esse, e cada invocação de método será executada em um thread separado.  

## <a name="next-steps"></a>Passos seguintes

- [Relações de modelagem](table-storage-design-modeling.md)
- [Design para consulta](table-storage-design-for-query.md)
- [Criptografando dados da tabela](table-storage-design-encrypt-data.md)
- [Design para modificação de dados](table-storage-design-for-modification.md)
