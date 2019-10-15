---
title: Lista de verificação de desempenho e escalabilidade para armazenamento de tabelas-armazenamento do Azure
description: Uma lista de verificação de práticas comprovadas para uso com o armazenamento de tabelas no desenvolvimento de aplicativos de alto desempenho.
services: storage
author: tamram
ms.service: storage
ms.topic: overview
ms.date: 10/10/2019
ms.author: tamram
ms.subservice: tables
ms.openlocfilehash: d9fe4ee761a7ff9570bf0df61a8990f82640b4f7
ms.sourcegitcommit: 9dec0358e5da3ceb0d0e9e234615456c850550f6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/14/2019
ms.locfileid: "72311605"
---
# <a name="performance-and-scalability-checklist-for-table-storage"></a>Lista de verificação de desempenho e escalabilidade para o armazenamento de tabelas

A Microsoft desenvolveu várias práticas comprovadas para o desenvolvimento de aplicativos de alto desempenho com o armazenamento de tabelas. Esta lista de verificação identifica as principais práticas que os desenvolvedores podem seguir para otimizar o desempenho. Tenha essas práticas em mente enquanto estiver projetando seu aplicativo e durante todo o processo.

O armazenamento do Azure tem metas de desempenho e escalabilidade para capacidade, taxa de transação e largura de banda. Para obter mais informações sobre destinos de escalabilidade de armazenamento do Azure, consulte [metas de desempenho e escalabilidade do armazenamento do Azure para contas de armazenamento](../common/storage-scalability-targets.md?toc=%2fazure%2fstorage%2ftables%2ftoc.json).

## <a name="checklist"></a>Lista de Verificação

Este artigo organiza as práticas comprovadas de desempenho em uma lista de verificação que você pode seguir ao desenvolver seu aplicativo de armazenamento de tabelas.

| Concluído | Categoria | Consideração de design |
| --- | --- | --- |
| &nbsp; |Metas de escalabilidade |[Você pode projetar seu aplicativo para não usar mais do que o número máximo de contas de armazenamento?](#maximum-number-of-storage-accounts) |
| &nbsp; |Metas de escalabilidade |[Você está evitando a abordagem de limites de capacidade e transação?](#capacity-and-transaction-targets) |
| &nbsp; |Funcionamento em Rede |[Os dispositivos do lado do cliente têm largura de banda suficientemente alta e baixa latência para alcançar o desempenho necessário?](#throughput) |
| &nbsp; |Funcionamento em Rede |[Os dispositivos do lado do cliente têm um link de rede de alta qualidade?](#link-quality) |
| &nbsp; |Funcionamento em Rede |[O aplicativo cliente está na mesma região que a conta de armazenamento?](#location) |
| &nbsp; |Acesso direto do cliente |[Você está usando SAS (assinaturas de acesso compartilhado) e CORS (compartilhamento de recursos entre origens) para habilitar o acesso direto ao armazenamento do Azure?](#sas-and-cors) |
| &nbsp; |Lotes |[O aplicativo está enviando atualizações por lote usando transações de grupo de entidades?](#batch-transactions) |
| &nbsp; |Configuração do .NET |[Você está usando o .NET Core 2,1 ou posterior para obter um desempenho ideal?](#use-net-core) |
| &nbsp; |Configuração do .NET |[Você configurou seu cliente para usar um número suficiente de conexões simultâneas?](#increase-default-connection-limit) |
| &nbsp; |Configuração do .NET |[Para aplicativos .NET, você configurou o .NET para usar um número suficiente de threads?](#increase-minimum-number-of-threads) |
| &nbsp; |Paralelismo |[Você assegurou que o paralelismo está limitado adequadamente para que você não sobrecarregar os recursos do cliente ou abordar as metas de escalabilidade?](#unbounded-parallelism) |
| &nbsp; |Ferramentas |[Você está usando as versões mais recentes das bibliotecas e ferramentas de cliente fornecidas pela Microsoft?](#client-libraries-and-tools) |
| &nbsp; |Tentativas |[Você está usando uma política de repetição com uma retirada exponencial para limitação de erros e tempos limite?](#timeout-and-server-busy-errors) |
| &nbsp; |Tentativas |[Seu aplicativo está evitando repetições para erros sem nova tentativa?](#non-retryable-errors) |
| &nbsp; |Metas de escalabilidade |[Você está se aproximando das metas de escalabilidade para entidades por segundo?](#table-specific-scalability-targets) |
| &nbsp; |Configuração |[Você está usando JSON para suas solicitações de tabela?](#use-json) |
| &nbsp; |Configuração |[Você desligou o algoritmo Nagle para melhorar o desempenho de pequenas solicitações?](#disable-nagle) |
| &nbsp; |Tabelas e partições |[Você particionou corretamente os dados?](#schema) |
| &nbsp; |Partições ativas |[Você está evitando padrões somente de acréscimo e somente para preceder?](#append-only-and-prepend-only-patterns) |
| &nbsp; |Partições ativas |[Suas inserções/atualizações se espalham por várias partições?](#high-traffic-data) |
| &nbsp; |Âmbito de consulta |[Você criou seu esquema para permitir que as consultas de ponto sejam usadas na maioria dos casos e as consultas de tabela sejam usadas com moderação?](#query-scope) |
| &nbsp; |Densidade da consulta |[Suas consultas normalmente só verificam e retornam linhas que seu aplicativo usará?](#query-density) |
| &nbsp; |Limitando dados retornados |[Você está usando a filtragem para evitar o retorno de entidades que não são necessárias?](#limiting-the-amount-of-data-returned) |
| &nbsp; |Limitando dados retornados |[Você está usando a projeção para evitar o retorno de propriedades que não são necessárias?](#limiting-the-amount-of-data-returned) |
| &nbsp; |Desnormalização |[Você desnormalizou seus dados de forma a evitar consultas ineficientes ou várias solicitações de leitura ao tentar obter dados?](#denormalization) |
| &nbsp; |Inserir, atualizar e excluir |[Você está em lotes de solicitações que precisam ser transacionais ou podem ser feitas ao mesmo tempo para reduzir viagens de ida e volta?](#batching) |
| &nbsp; |Inserir, atualizar e excluir |[Você está evitando a recuperação de uma entidade apenas para determinar se deve chamar INSERT ou Update?](#upsert) |
| &nbsp; |Inserir, atualizar e excluir |[Você considerou o armazenamento de uma série de dados que frequentemente serão recuperados juntos em uma única entidade como propriedades em vez de várias entidades?](#storing-data-series-in-a-single-entity) |
| &nbsp; |Inserir, atualizar e excluir |[Para entidades que sempre serão recuperadas juntas e podem ser gravadas em lotes (por exemplo, dados de série temporal), você considerou o uso de BLOBs em vez de tabelas?](#storing-structured-data-in-blobs) |

## <a name="scalability-targets"></a>Metas de escalabilidade

Se seu aplicativo se aproximar ou exceder qualquer um dos destinos de escalabilidade, ele poderá encontrar maiores latências ou limitação de transação. Quando o armazenamento do Azure limita seu aplicativo, o serviço começa a retornar os códigos de erro 503 (servidor ocupado) ou 500 (tempo limite da operação). Evitar esses erros ao permanecer dentro dos limites das metas de escalabilidade é uma parte importante do aprimoramento do desempenho do seu aplicativo.

Para obter mais informações sobre metas de escalabilidade para o serviço tabela, consulte [metas de desempenho e escalabilidade do armazenamento do Azure para contas de armazenamento](/azure/storage/common/storage-scalability-targets?toc=%2fazure%2fstorage%2ftables%2ftoc.json#azure-table-storage-scale-targets).

### <a name="maximum-number-of-storage-accounts"></a>Número máximo de contas de armazenamento

Se estiver se aproximando do número máximo de contas de armazenamento permitidas para uma combinação de assinatura/região específica, você usará várias contas de armazenamento para fragmentar para aumentar a entrada, a saída, as operações de e/s por segundo (IOPS) ou a capacidade? Nesse cenário, a Microsoft recomenda que você aproveite os limites maiores de contas de armazenamento padrão para reduzir o número de contas de armazenamento necessárias para sua carga de trabalho, se possível. Contate o [suporte do Azure](https://azure.microsoft.com/support/options/) para solicitar limites maiores para sua conta de armazenamento. Para obter mais informações, consulte [anunciando contas de armazenamento maiores e de maior escala](https://azure.microsoft.com/blog/announcing-larger-higher-scale-storage-accounts/).

### <a name="capacity-and-transaction-targets"></a>Metas de capacidade e de transação

Se seu aplicativo estiver se aproximando das metas de escalabilidade de uma única conta de armazenamento, considere a adoção de uma das seguintes abordagens:  

- Reconsidere a carga de trabalho que faz com que seu aplicativo aborde ou exceda a meta de escalabilidade. Você pode criá-lo de forma diferente para usar menos largura de banda ou capacidade, ou menos transações?
- Se seu aplicativo deve exceder um dos destinos de escalabilidade, crie várias contas de armazenamento e Particione os dados do aplicativo entre essas várias contas de armazenamento. Se você usar esse padrão, certifique-se de projetar seu aplicativo para que você possa adicionar mais contas de armazenamento no futuro para o balanceamento de carga. As próprias contas de armazenamento não têm nenhum custo além do uso em termos de dados armazenados, transações feitas ou dados transferidos.
- Se seu aplicativo estiver se aproximando das metas de largura de banda, considere compactar dados no lado do cliente para reduzir a largura de banda necessária para enviar os dados para o armazenamento do Azure.
    Embora a compactação de dados possa economizar largura de banda e melhorar o desempenho da rede, ela também pode ter efeitos negativos no desempenho. Avalie o impacto no desempenho dos requisitos de processamento adicionais para compactação e descompactação de dados no lado do cliente. Tenha em mente que armazenar dados compactados pode dificultar a solução de problemas, pois pode ser mais desafiador exibir os dados usando ferramentas padrão.
- Se seu aplicativo estiver se aproximando das metas de escalabilidade, verifique se você está usando uma retirada exponencial para novas tentativas. É melhor tentar evitar o alcance das metas de escalabilidade implementando as recomendações descritas neste artigo. No entanto, o uso de uma retirada exponencial para novas tentativas impedirá que seu aplicativo seja repetido rapidamente, o que poderia tornar a limitação pior. Para obter mais informações, consulte a seção intitulada [tempo limite e erros de servidor ocupado](#timeout-and-server-busy-errors).

## <a name="table-specific-scalability-targets"></a>Metas de escalabilidade específicas de tabela

Além das limitações de largura de banda de uma conta de armazenamento inteira, as tabelas têm o seguinte limite de escalabilidade específico. O sistema balanceará a carga conforme o tráfego aumentar, mas se o tráfego tiver picos repentinos, talvez você não consiga obter esse volume de taxa de transferência imediatamente. Se o seu padrão tiver intermitências, você deve esperar ver a limitação e/ou os tempos limite durante a intermitência, uma vez que o serviço de armazenamento automaticamente balanceia a tabela. O aumento lento geralmente tem resultados melhores, pois dá ao tempo do sistema para balancear a carga adequadamente.

## <a name="networking"></a>Funcionamento em Rede

As restrições de rede física do aplicativo podem ter um impacto significativo no desempenho. As seções a seguir descrevem algumas das limitações que os usuários podem encontrar.  

### <a name="client-network-capability"></a>Funcionalidade de rede do cliente

A largura de banda e a qualidade do link de rede desempenham funções importantes no desempenho do aplicativo, conforme descrito nas seções a seguir.

#### <a name="throughput"></a>Débito

Para largura de banda, o problema costuma ser os recursos do cliente. Instâncias maiores do Azure têm NICs com maior capacidade, portanto, você deve considerar usar uma instância maior ou mais VMs se precisar de limites de rede maiores de um único computador. Se você estiver acessando o armazenamento do Azure de um aplicativo local, a mesma regra se aplicará: compreenda os recursos de rede do dispositivo cliente e a conectividade de rede com o local de armazenamento do Azure e melhore-os conforme necessário ou crie seu aplicativo para trabalhar em seus recursos.

#### <a name="link-quality"></a>Qualidade do link

Assim como acontece com qualquer uso de rede, tenha em mente que as condições de rede que resultam em erros e perda de pacotes atrasarão a taxa de transferência efetiva.  Usar o WireShark ou o NetMon pode ajudar a diagnosticar esse problema.  

### <a name="location"></a>Localização

Em qualquer ambiente distribuído, colocar o cliente perto do servidor proporciona o melhor desempenho. Para acessar o armazenamento do Azure com a menor latência, o melhor local para o cliente está dentro da mesma região do Azure. Por exemplo, se você tiver um aplicativo Web do Azure que usa o armazenamento do Azure, localize-os em uma única região, como oeste dos EUA ou sudeste asiático. A colocalização de recursos reduz a latência e o custo, pois o uso de largura de banda em uma única região é gratuito.  

Se os aplicativos cliente acessarem o armazenamento do Azure, mas não estiverem hospedados no Azure, como aplicativos de dispositivos móveis ou serviços corporativos locais, a localização da conta de armazenamento em uma região perto desses clientes poderá reduzir a latência. Se os clientes forem amplamente distribuídos (por exemplo, alguns em América do Norte e outros na Europa), considere o uso de uma conta de armazenamento por região. Essa abordagem é mais fácil de implementar se os dados que o aplicativo armazena são específicos para usuários individuais e não requer a replicação de dados entre contas de armazenamento.

## <a name="sas-and-cors"></a>SAS e CORS

Suponha que você precise autorizar um código como o JavaScript que está sendo executado no navegador da Web de um usuário ou em um aplicativo de telefone celular para acessar dados no armazenamento do Azure. Uma abordagem é criar um aplicativo de serviço que atue como um proxy. O dispositivo do usuário é autenticado com o serviço que, por sua vez, autoriza o acesso aos recursos de armazenamento do Azure. Dessa forma, você pode evitar expor suas chaves de conta de armazenamento em dispositivos não seguros. No entanto, essa abordagem coloca uma sobrecarga significativa no aplicativo de serviço, pois todos os dados transferidos entre o dispositivo do usuário e o armazenamento do Azure devem passar pelo aplicativo de serviço.

Você pode evitar o uso de um aplicativo de serviço como proxy para o armazenamento do Azure usando SAS (assinaturas de acesso compartilhado). Usando a SAS, você pode habilitar o dispositivo do usuário para fazer solicitações diretamente no armazenamento do Azure usando um token de acesso limitado. Por exemplo, se um usuário quiser carregar uma foto em seu aplicativo, o aplicativo de serviço poderá gerar uma SAS e enviá-la para o dispositivo do usuário. O token SAS pode conceder permissão para gravar em um recurso de armazenamento do Azure por um intervalo de tempo especificado, após o qual o token SAS expira. Para obter mais informações sobre SAS, consulte [conceder acesso limitado aos recursos de armazenamento do Azure usando SAS (assinaturas de acesso compartilhado)](../common/storage-sas-overview.md).  

Normalmente, um navegador da Web não permitirá o JavaScript em uma página hospedada por um site em um domínio para executar determinadas operações, como operações de gravação, em outro domínio. Conhecida como a política de mesma origem, essa política impede que um script mal-intencionado em uma página obtenha acesso a dados em outra página da Web. No entanto, a política de mesma origem pode ser uma limitação ao criar uma solução na nuvem. O CORS (compartilhamento de recursos entre origens) é um recurso de navegador que permite que o domínio de destino se comunique com o navegador que confia nas solicitações originadas no domínio de origem.

Por exemplo, suponha que um aplicativo Web em execução no Azure faça uma solicitação de um recurso para uma conta de armazenamento do Azure. O aplicativo Web é o domínio de origem e a conta de armazenamento é o domínio de destino. Você pode configurar o CORS para qualquer um dos serviços de armazenamento do Azure para se comunicar com o navegador da Web que as solicitações do domínio de origem são confiáveis pelo armazenamento do Azure. Para obter mais informações sobre CORS, consulte [suporte ao compartilhamento de recursos entre origens (CORS) para o armazenamento do Azure](/rest/api/storageservices/Cross-Origin-Resource-Sharing--CORS--Support-for-the-Azure-Storage-Services).  
  
SAS e CORS podem ajudá-lo a evitar a carga desnecessária em seu aplicativo Web.  

## <a name="batch-transactions"></a>Transações em lotes

O serviço tabela dá suporte a transações em lotes em entidades que estão na mesma tabela e pertencem ao mesmo grupo de partições. Para obter mais informações, consulte [executando transações de grupo de entidades](/rest/api/storageservices/performing-entity-group-transactions).

## <a name="net-configuration"></a>Configuração do .NET

Se estiver usando o .NET Framework, esta seção listará várias definições de configuração rápida que você pode usar para fazer melhorias significativas no desempenho.  Se estiver usando outras linguagens, verifique se conceitos semelhantes se aplicam à linguagem escolhida.  

### <a name="use-net-core"></a>Usar o .NET Core

Desenvolva seus aplicativos de armazenamento do Azure com o .NET Core 2,1 ou posterior para aproveitar os aprimoramentos de desempenho. O uso do .NET Core 3. x é recomendado quando possível.

Para obter mais informações sobre melhorias de desempenho no .NET Core, consulte as postagens de blog a seguir:

- [Melhorias de desempenho no .NET Core 3,0](https://devblogs.microsoft.com/dotnet/performance-improvements-in-net-core-3-0/)
- [Melhorias de desempenho no .NET Core 2,1](https://devblogs.microsoft.com/dotnet/performance-improvements-in-net-core-2-1/)

### <a name="increase-default-connection-limit"></a>Aumentar limite de conexão padrão

No .NET, o código a seguir aumenta o limite de conexão padrão (que geralmente é 2 em um ambiente de cliente ou 10 em um ambiente de servidor) para 100. Normalmente, você deve definir o valor para aproximadamente o número de threads usados pelo seu aplicativo.  

```csharp
ServicePointManager.DefaultConnectionLimit = 100; //(Or More)  
```

Defina o limite de conexão antes de abrir qualquer conexão.  

Para outras linguagens de programação, consulte a documentação dessa linguagem para determinar como definir o limite de conexão.  

Para obter mais informações, consulte a postagem no blog [Serviços Web: conexões simultâneas](https://blogs.msdn.microsoft.com/darrenj/2005/03/07/web-services-concurrent-connections/).  

### <a name="increase-minimum-number-of-threads"></a>Aumentar o número mínimo de threads

Se você estiver usando chamadas síncronas junto com tarefas assíncronas, talvez queira aumentar o número de threads no pool de threads:

```csharp
ThreadPool.SetMinThreads(100,100); //(Determine the right number for your application)  
```

Para obter mais informações, consulte o método [ThreadPool. SetMinThreads](/dotnet/api/system.threading.threadpool.setminthreads) .  

## <a name="unbounded-parallelism"></a>Paralelismo não associado

Embora o paralelismo possa ser ótimo para o desempenho, tenha cuidado ao usar paralelismo não associado, o que significa que não há nenhum limite imposto sobre o número de threads ou solicitações paralelas. Certifique-se de limitar as solicitações paralelas para carregar ou baixar dados, para acessar várias partições na mesma conta de armazenamento ou para acessar vários itens na mesma partição. Se o paralelismo não estiver associado, seu aplicativo poderá exceder os recursos do dispositivo cliente ou as metas de escalabilidade da conta de armazenamento, resultando em latências mais longas e limitação.  

## <a name="client-libraries-and-tools"></a>Bibliotecas e ferramentas de cliente

Para obter o melhor desempenho, sempre use as bibliotecas e ferramentas de cliente mais recentes fornecidas pela Microsoft. As bibliotecas de cliente de armazenamento do Azure estão disponíveis para uma variedade de idiomas. O armazenamento do Azure também dá suporte ao PowerShell e CLI do Azure. A Microsoft desenvolve ativamente essas bibliotecas e ferramentas de cliente com o desempenho em mente, mantém-as atualizadas com as versões de serviço mais recentes e garante que elas manipulem muitas das práticas de desempenho comprovadas internamente. Para obter mais informações, consulte a [documentação de referência do armazenamento do Azure](/azure/storage/#reference).

## <a name="handle-service-errors"></a>Manipular erros de serviço

O armazenamento do Azure retorna um erro quando o serviço não pode processar uma solicitação. Entender os erros que podem ser retornados pelo armazenamento do Azure em um determinado cenário é útil para otimizar o desempenho.

### <a name="timeout-and-server-busy-errors"></a>Tempo limite e erros de servidor ocupado

O armazenamento do Azure pode limitar seu aplicativo se ele se aproximar dos limites de escalabilidade. Em alguns casos, o armazenamento do Azure pode não ser capaz de lidar com uma solicitação devido a alguma condição transitória. Em ambos os casos, o serviço pode retornar um erro 503 (servidor ocupado) ou 500 (tempo limite). Esses erros também podem ocorrer se o serviço estiver reequilibrando partições de dados para permitir uma maior taxa de transferência. O aplicativo cliente normalmente deve repetir a operação que causa um desses erros. No entanto, se o armazenamento do Azure estiver limitando seu aplicativo porque ele está excedendo destinos de escalabilidade ou, mesmo que o serviço não tenha sido capaz de atender à solicitação por algum outro motivo, as tentativas agressivas podem dificultar o problema. É recomendável usar uma política de repetição de retirada exponencial e as bibliotecas de cliente usam como padrão esse comportamento. Por exemplo, seu aplicativo pode tentar novamente após 2 segundos, depois de 4 segundos, 10 segundos, 30 segundos e, em seguida, desistir completamente. Dessa forma, seu aplicativo reduz significativamente sua carga no serviço, em vez de exacerbar o comportamento que pode levar à limitação.  

Os erros de conectividade podem ser repetidos imediatamente, porque eles não são o resultado da limitação e devem ser transitórios.  

### <a name="non-retryable-errors"></a>Erros sem nova tentativa

As bibliotecas de cliente tratam repetições com um reconhecimento de quais erros podem ser repetidos e quais não podem. No entanto, se você estiver chamando a API REST do armazenamento do Azure diretamente, há alguns erros que você não deve tentar novamente. Por exemplo, um erro 400 (solicitação inválida) indica que o aplicativo cliente enviou uma solicitação que não pôde ser processada porque não estava no formato esperado. Reenviar essa solicitação resulta na mesma resposta a cada vez, portanto, não há nenhum ponto em tentar novamente. Se você estiver chamando a API REST do armazenamento do Azure diretamente, lembre-se de possíveis erros e se eles devem ser repetidos.

Para obter mais informações sobre os códigos de erro do armazenamento do Azure, consulte [status e códigos de erro](/rest/api/storageservices/status-and-error-codes2).

## <a name="configuration"></a>Configuração

Esta seção lista várias definições de configuração rápida que você pode usar para fazer melhorias significativas de desempenho no serviço tabela:

### <a name="use-json"></a>Usar JSON

A partir do serviço de armazenamento versão 2013-08-15, o serviço tabela dá suporte ao uso de JSON em vez do formato AtomPub baseado em XML para transferir dados da tabela. O uso do JSON pode reduzir os tamanhos de carga em até 75% e pode melhorar significativamente o desempenho do seu aplicativo.

Para obter mais informações, consulte as [tabelas post Microsoft Azure: Introducing JSON](https://blogs.msdn.com/b/windowsazurestorage/archive/2013/12/05/windows-azure-tables-introducing-json.aspx) and [Payload Format for Table Service Operations](https://msdn.microsoft.com/library/azure/dn535600.aspx).

### <a name="disable-nagle"></a>Desabilitar Nagle

O algoritmo do Nagle é amplamente implementado em redes TCP/IP como um meio para melhorar o desempenho da rede. No entanto, não é ideal em todas as circunstâncias (como ambientes altamente interativos). O algoritmo do Nagle tem um impacto negativo sobre o desempenho das solicitações para o serviço tabela do Azure e você deve desabilitá-la, se possível.

## <a name="schema"></a>Esquema

A maneira como você representa e consulta seus dados é o maior fator único que afeta o desempenho do serviço tabela. Embora cada aplicativo seja diferente, esta seção descreve algumas práticas comprovadas gerais relacionadas a:

- Design de tabela
- Consultas eficientes
- Atualizações de dados eficientes

### <a name="tables-and-partitions"></a>Tabelas e partições

As tabelas são divididas em partições. Cada entidade armazenada em uma partição compartilha a mesma chave de partição e tem uma chave de linha exclusiva para identificá-la nessa partição. As partições fornecem benefícios, mas também apresentam limites de escalabilidade.

- Benefícios: você pode atualizar entidades na mesma partição em uma única transação atômica, em lote que contém até 100 operações de armazenamento separadas (limite de 4 MB de tamanho total). Supondo que o mesmo número de entidades a serem recuperadas, você também pode consultar dados em uma única partição de forma mais eficiente do que os dados que abrangem partições (embora continue lendo para obter mais recomendações sobre como consultar os dados da tabela).
- Limite de escalabilidade: o acesso a entidades armazenadas em uma única partição não pode ter balanceamento de carga porque as partições dão suporte a transações em lote atômicas. Por esse motivo, a meta de escalabilidade para uma partição de tabela individual é menor do que para o serviço tabela como um todo.

Devido a essas características de tabelas e partições, você deve adotar os seguintes princípios de design:

- Localize os dados que o aplicativo cliente atualiza frequentemente ou consultas na mesma unidade lógica de trabalho na mesma partição. Por exemplo, localize dados na mesma partição se seu aplicativo estiver agregando gravações ou se você estiver executando operações de lote atômicas. Além disso, os dados em uma única partição podem ser consultados de forma mais eficiente em uma única consulta do que os dados entre partições.
- Localize os dados que o aplicativo cliente não insere, atualiza ou consulta na mesma unidade lógica de trabalho (ou seja, em uma única consulta ou atualização em lote) em partições separadas. Tenha em mente que não há nenhum limite para o número de chaves de partição em uma única tabela, portanto, ter milhões de chaves de partição não é um problema e não afetará o desempenho. Por exemplo, se seu aplicativo for um site popular com logon de usuário, usar a ID de usuário como a chave de partição pode ser uma boa opção.

#### <a name="hot-partitions"></a>Partições ativas

Uma partição ativa é aquela que está recebendo uma porcentagem desproporcional do tráfego para uma conta e não pode ter balanceamento de carga porque é uma única partição. Em geral, as partições ativas são criadas de duas maneiras:

#### <a name="append-only-and-prepend-only-patterns"></a>Somente acrescentar e preceder padrões

O padrão "acrescentar somente" é aquele em que todos (ou quase todos) do tráfego para uma determinada chave de partição aumentam e diminuem de acordo com a hora atual. Por exemplo, suponha que seu aplicativo use a data atual como uma chave de partição para dados de log. Esse design resulta em todas as inserções indo para a última partição em sua tabela, e o sistema não pode balancear a carga corretamente. Se o volume de tráfego para essa partição exceder o destino de escalabilidade no nível da partição, isso resultará em limitação. É melhor garantir que o tráfego seja enviado para várias partições, a fim de habilitar o balanceamento de carga das solicitações em sua tabela.

#### <a name="high-traffic-data"></a>Dados de tráfego alto

Se o esquema de particionamento resultar em uma única partição que tem apenas dados que são muito mais usados do que outras partições, você também poderá ver a limitação, pois essa partição se aproximará da meta de escalabilidade para uma única partição. É melhor garantir que o esquema de partição não resulte em nenhuma partição única que se aproxima das metas de escalabilidade.

### <a name="querying"></a>A consultar

Esta seção descreve as práticas comprovadas para consultar o serviço tabela.

#### <a name="query-scope"></a>Âmbito de consulta

Há várias maneiras de especificar o intervalo de entidades a serem consultadas. A lista a seguir descreve cada opção para o escopo de consulta.

- **Consultas de ponto:** -uma consulta de ponto recupera exatamente uma entidade especificando a chave de partição e a chave de linha da entidade a ser recuperada. Essas consultas são eficientes e você deve usá-las sempre que possível.
- **Consultas de partição:** Uma consulta de partição é uma consulta que recupera um conjunto de dados que compartilha uma chave de partição comum. Normalmente, a consulta especifica um intervalo de valores de chave de linha ou um intervalo de valores para alguma propriedade de entidade, além de uma chave de partição. Essas consultas são menos eficientes do que as consultas de ponto e devem ser usadas com moderação.
- **Consultas de tabela:** Uma consulta de tabela é uma consulta que recupera um conjunto de entidades que não compartilha uma chave de partição comum. Essas consultas não são eficientes e você deve evitá-las, se possível.

Em geral, evite verificações (consultas maiores que uma única entidade), mas se você precisar verificar, tente organizar seus dados para que suas verificações recuperem os dados necessários sem verificar ou retornar quantidades significativas de entidades que você não precisa.

#### <a name="query-density"></a>Densidade da consulta

Outro fator importante na eficiência da consulta é o número de entidades retornadas em comparação com o número de entidades verificadas para localizar o conjunto retornado. Se seu aplicativo executar uma consulta de tabela com um filtro para um valor de propriedade que apenas 1% dos compartilhamentos de dados, a consulta examinará 100 entidades para cada entidade que retornar. As metas de escalabilidade de tabela discutidas anteriormente se relacionam com o número de entidades verificadas e não o número de entidades retornadas: uma densidade de consulta baixa pode facilmente fazer com que o serviço tabela limite seu aplicativo, pois ele deve verificar tantas entidades para Recupere a entidade que você está procurando. Para obter mais informações sobre como evitar a limitação, consulte a seção intitulada [desnormalização](#denormalization).

#### <a name="limiting-the-amount-of-data-returned"></a>Limitando a quantidade de dados retornados

Quando você sabe que uma consulta retornará entidades que você não precisa no aplicativo cliente, considere usar um filtro para reduzir o tamanho do conjunto retornado. Embora as entidades não retornadas ao cliente ainda sejam contadas para os limites de escalabilidade, o desempenho do aplicativo será melhorado devido ao tamanho reduzido da carga de rede e ao número reduzido de entidades que seu aplicativo cliente deve processar. Lembre-se de que as metas de escalabilidade se relacionam com o número de entidades verificadas, portanto, uma consulta que filtra muitas entidades ainda pode resultar em limitação, mesmo que poucas entidades sejam retornadas. Para obter mais informações sobre como tornar as consultas eficientes, consulte a seção intitulada [densidade de consulta](#query-density).

Se o aplicativo cliente precisar apenas de um conjunto limitado de propriedades das entidades em sua tabela, você poderá usar a projeção para limitar o tamanho do conjunto de dados retornado. Assim como na filtragem, a projeção ajuda a reduzir a carga de rede e o processamento do cliente.

#### <a name="denormalization"></a>Desnormalização

Ao contrário do trabalho com bancos de dados relacionais, as práticas comprovadas para consultar com eficiência o dado da tabela levam à desnormalização dos dados. Ou seja, duplicar os mesmos dados em várias entidades (um para cada chave que você pode usar para localizar os dados) para minimizar o número de entidades que uma consulta deve verificar para localizar os dados de que o cliente precisa, em vez de ter que verificar grandes números de entidades para localizar os dados de seu aplicativo licativo necessidades. Por exemplo, em um site de comércio eletrônico, talvez você queira encontrar um pedido tanto pela ID do cliente (fornecer pedidos deste cliente) e pela data (fornecer pedidos em uma data). No armazenamento de tabelas, é melhor armazenar a entidade (ou uma referência a ela) duas vezes – uma vez com o nome da tabela, CP e r para facilitar a localização pela ID do cliente, uma vez para facilitar sua localização até a data.  

### <a name="insert-update-and-delete"></a>Inserir, atualizar e excluir

Esta seção descreve as práticas comprovadas para modificar entidades armazenadas no serviço tabela.  

#### <a name="batching"></a>Lotes

As transações em lote são conhecidas como transações de grupo de entidades no armazenamento do Azure. Todas as operações em uma transação de grupo de entidades devem estar em uma única partição em uma única tabela. Sempre que possível, use as transações do grupo de entidades para executar inserções, atualizações e exclusões em lotes. O uso de transações de grupo de entidades reduz o número de viagens de ida e volta de seu aplicativo cliente para o servidor, reduz o número de transações faturáveis (uma transação de grupo de entidades conta como uma única transação para fins de cobrança e pode conter até 100 operações de armazenamento) e habilita atualizações atômicas (todas as operações são bem sucedidas ou todas falham em uma transação de grupo de entidades). Ambientes com latências altas como dispositivos móveis se beneficiarão muito do uso de transações de grupo de entidades.  

#### <a name="upsert"></a>Upsert

Use as operações **Upsert** de tabela sempre que possível. Há dois tipos de **Upsert**, os quais podem ser mais eficientes do que as operações tradicionais de **inserção** e **atualização** :  

- **InsertOrMerge**: Use essa operação quando desejar carregar um subconjunto das propriedades da entidade, mas não tiver certeza se a entidade já existe. Se a entidade existir, essa chamada atualizará as propriedades incluídas na operação **Upsert** e deixará todas as propriedades existentes como estão, se a entidade não existir, ela inserirá a nova entidade. Isso é semelhante ao uso de projeção em uma consulta, pois você só precisa carregar as propriedades que estão sendo alteradas.
- **InsertOrReplace**: Use essa operação quando desejar carregar uma entidade totalmente nova, mas não tiver certeza se ela já existe. Use essa operação quando souber que a entidade carregada recentemente está totalmente correta porque substitui completamente a antiga entidade. Por exemplo, você deseja atualizar a entidade que armazena o local atual de um usuário, independentemente de o aplicativo ter armazenado previamente os dados de localização para o usuário; a nova entidade local está concluída e você não precisa de nenhuma informação de nenhuma entidade anterior.

#### <a name="storing-data-series-in-a-single-entity"></a>Armazenando séries de dados em uma única entidade

Às vezes, um aplicativo armazena uma série de dados que freqüentemente precisa recuperar de uma só vez: por exemplo, um aplicativo pode controlar o uso da CPU ao longo do tempo para plotar um gráfico de rolagem dos dados das últimas 24 horas. Uma abordagem é ter uma entidade de tabela por hora, com cada entidade representando uma hora específica e armazenando o uso da CPU para essa hora. Para plotar esses dados, o aplicativo precisa recuperar as entidades que contêm os dados das 24 horas mais recentes.  

Como alternativa, seu aplicativo pode armazenar o uso da CPU para cada hora como uma propriedade separada de uma única entidade: para atualizar a cada hora, seu aplicativo pode usar uma única chamada **InsertOrMerge Upsert** para atualizar o valor da hora mais recente. Para plotar os dados, o aplicativo precisa apenas recuperar uma única entidade em vez de 24, fazendo uma consulta eficiente. Para obter mais informações sobre a eficiência da consulta, consulte a seção [escopo da consulta](#query-scope)intitulada.

#### <a name="storing-structured-data-in-blobs"></a>Armazenando dados estruturados em BLOBs

Se você estiver executando inserções em lotes e, em seguida, recuperando intervalos de entidades, considere o uso de BLOBs em vez de tabelas. Um bom exemplo é um arquivo de log. Você pode fazer o lote de vários minutos de logs e inseri-los e, em seguida, recuperar vários minutos de logs por vez. Nesse caso, o desempenho será melhor se você usar BLOBs em vez de tabelas, já que você pode reduzir significativamente o número de objetos gravados ou lidos e, possivelmente, o número de solicitações que precisam ser feitas.  

## <a name="next-steps"></a>Passos seguintes

- [Escalabilidade e metas de desempenho do armazenamento do Azure para contas de armazenamento](../common/storage-scalability-targets.md?toc=%2fazure%2fstorage%2ftables%2ftoc.json)
- [Lista de verificação de desempenho e escalabilidade para armazenamento de BLOBs](../blobs/storage-performance-checklist.md)
- [Lista de verificação de desempenho e escalabilidade para armazenamento de filas](../queues/storage-performance-checklist.md)
- [Status e códigos de erro](/rest/api/storageservices/Status-and-Error-Codes2)
