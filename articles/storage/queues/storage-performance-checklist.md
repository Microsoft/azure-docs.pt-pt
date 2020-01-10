---
title: Lista de verificação de desempenho e escalabilidade para armazenamento de filas-armazenamento do Azure
description: Uma lista de verificação de práticas comprovadas para uso com o armazenamento de filas no desenvolvimento de aplicativos de alto desempenho.
services: storage
author: tamram
ms.service: storage
ms.topic: overview
ms.date: 10/10/2019
ms.author: tamram
ms.subservice: queues
ms.openlocfilehash: eb1821537e6e25b05dfdca3107729eecf4c6e1bf
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/08/2020
ms.locfileid: "75750511"
---
# <a name="performance-and-scalability-checklist-for-queue-storage"></a>Lista de verificação de desempenho e escalabilidade para armazenamento de filas

A Microsoft desenvolveu várias práticas comprovadas para o desenvolvimento de aplicativos de alto desempenho com o armazenamento de filas. Esta lista de verificação identifica as principais práticas que os desenvolvedores podem seguir para otimizar o desempenho. Tenha essas práticas em mente enquanto estiver projetando seu aplicativo e durante todo o processo.

O armazenamento do Azure tem metas de desempenho e escalabilidade para capacidade, taxa de transação e largura de banda. Para obter mais informações sobre destinos de escalabilidade de armazenamento do Azure, consulte [metas de escalabilidade e desempenho para contas de armazenamento Standard](../common/scalability-targets-standard-account.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json) e [escalabilidade e metas de desempenho para armazenamento de filas](scalability-targets.md).

## <a name="checklist"></a>Lista de Verificação

Este artigo organiza as práticas comprovadas de desempenho em uma lista de verificação que você pode seguir ao desenvolver seu aplicativo de armazenamento de filas.

| Concluído | Categoria | Considerações de design |
| --- | --- | --- |
| &nbsp; |Metas de escalabilidade |[Você pode projetar seu aplicativo para não usar mais do que o número máximo de contas de armazenamento?](#maximum-number-of-storage-accounts) |
| &nbsp; |Metas de escalabilidade |[Você está evitando a abordagem de limites de capacidade e transação?](#capacity-and-transaction-targets) |
| &nbsp; |Funcionamento em Rede |[Os dispositivos do lado do cliente têm largura de banda suficientemente alta e baixa latência para alcançar o desempenho necessário?](#throughput) |
| &nbsp; |Funcionamento em Rede |[Os dispositivos do lado do cliente têm um link de rede de alta qualidade?](#link-quality) |
| &nbsp; |Funcionamento em Rede |[O aplicativo cliente está na mesma região que a conta de armazenamento?](#location) |
| &nbsp; |Acesso direto do cliente |[Você está usando SAS (assinaturas de acesso compartilhado) e CORS (compartilhamento de recursos entre origens) para habilitar o acesso direto ao armazenamento do Azure?](#sas-and-cors) |
| &nbsp; |Configuração do .NET |[Você está usando o .NET Core 2,1 ou posterior para obter um desempenho ideal?](#use-net-core) |
| &nbsp; |Configuração do .NET |[Você configurou seu cliente para usar um número suficiente de conexões simultâneas?](#increase-default-connection-limit) |
| &nbsp; |Configuração do .NET |[Para aplicativos .NET, você configurou o .NET para usar um número suficiente de threads?](#increase-minimum-number-of-threads) |
| &nbsp; |Paralelismo |[Você assegurou que o paralelismo está limitado adequadamente para que você não sobrecarregar os recursos do cliente ou abordar as metas de escalabilidade?](#unbounded-parallelism) |
| &nbsp; |Ferramentas |[Você está usando as versões mais recentes das bibliotecas e ferramentas de cliente fornecidas pela Microsoft?](#client-libraries-and-tools) |
| &nbsp; |Tentativas |[Você está usando uma política de repetição com uma retirada exponencial para limitação de erros e tempos limite?](#timeout-and-server-busy-errors) |
| &nbsp; |Tentativas |[Seu aplicativo está evitando repetições para erros sem nova tentativa?](#non-retryable-errors) |
| &nbsp; |Configuração |[Você desligou o algoritmo Nagle para melhorar o desempenho de pequenas solicitações?](#disable-nagle) |
| &nbsp; |Tamanho da mensagem |[Suas mensagens são compactadas para melhorar o desempenho da fila?](#message-size) |
| &nbsp; |Recuperação em massa |[Você está recuperando várias mensagens em uma única operação GET?](#batch-retrieval) |
| &nbsp; |Frequência de sondagem |[Você está sondando com frequência suficiente para reduzir a latência percebida do seu aplicativo?](#queue-polling-interval) |
| &nbsp; |Atualizar mensagem |[Você está usando a operação atualizar mensagem para armazenar o progresso no processamento de mensagens, para que você possa evitar ter que reprocessar a mensagem inteira se ocorrer um erro?](#use-update-message) |
| &nbsp; |Arquitetura |[Você está usando filas para tornar todo o aplicativo mais escalonável, mantendo cargas de trabalho demoradas fora do caminho crítico e dimensionamento de forma independente?](#application-architecture) |

## <a name="scalability-targets"></a>Metas de escalabilidade

Se seu aplicativo se aproximar ou exceder qualquer um dos destinos de escalabilidade, ele poderá encontrar maiores latências ou limitação de transação. Quando o armazenamento do Azure limita seu aplicativo, o serviço começa a retornar os códigos de erro 503 (servidor ocupado) ou 500 (tempo limite da operação). Evitar esses erros ao permanecer dentro dos limites das metas de escalabilidade é uma parte importante do aprimoramento do desempenho do seu aplicativo.

Para obter mais informações sobre metas de escalabilidade para o serviço Fila, consulte [metas de desempenho e escalabilidade do armazenamento do Azure](/azure/storage/queues/scalability-targets#scale-targets-for-queue-storage).

### <a name="maximum-number-of-storage-accounts"></a>Número máximo de contas de armazenamento

Se estiver se aproximando do número máximo de contas de armazenamento permitidas para uma combinação de assinatura/região específica, você usará várias contas de armazenamento para fragmentar para aumentar a entrada, a saída, as operações de e/s por segundo (IOPS) ou a capacidade? Nesse cenário, a Microsoft recomenda que você aproveite os limites maiores de contas de armazenamento para reduzir o número de contas de armazenamento necessárias para sua carga de trabalho, se possível. Contate o [suporte do Azure](https://azure.microsoft.com/support/options/) para solicitar limites maiores para sua conta de armazenamento. Para obter mais informações, consulte [anunciando contas de armazenamento maiores e de maior escala](https://azure.microsoft.com/blog/announcing-larger-higher-scale-storage-accounts/).

### <a name="capacity-and-transaction-targets"></a>Metas de capacidade e de transação

Se seu aplicativo estiver se aproximando das metas de escalabilidade de uma única conta de armazenamento, considere a adoção de uma das seguintes abordagens:  

- Se as metas de escalabilidade para filas forem insuficientes para seu aplicativo, use várias filas e distribua as mensagens entre elas.
- Reconsidere a carga de trabalho que faz com que seu aplicativo aborde ou exceda a meta de escalabilidade. Você pode criá-lo de forma diferente para usar menos largura de banda ou capacidade, ou menos transações?
- Se seu aplicativo deve exceder um dos destinos de escalabilidade, crie várias contas de armazenamento e Particione os dados do aplicativo entre essas várias contas de armazenamento. Se você usar esse padrão, certifique-se de projetar seu aplicativo para que você possa adicionar mais contas de armazenamento no futuro para o balanceamento de carga. As próprias contas de armazenamento não têm nenhum custo além do uso em termos de dados armazenados, transações feitas ou dados transferidos.
- Se seu aplicativo estiver se aproximando das metas de largura de banda, considere compactar dados no lado do cliente para reduzir a largura de banda necessária para enviar os dados para o armazenamento do Azure.
    Embora a compactação de dados possa economizar largura de banda e melhorar o desempenho da rede, ela também pode ter efeitos negativos no desempenho. Avalie o impacto no desempenho dos requisitos de processamento adicionais para compactação e descompactação de dados no lado do cliente. Tenha em mente que armazenar dados compactados pode dificultar a solução de problemas, pois pode ser mais desafiador exibir os dados usando ferramentas padrão.
- Se seu aplicativo estiver se aproximando das metas de escalabilidade, verifique se você está usando uma retirada exponencial para novas tentativas. É melhor tentar evitar o alcance das metas de escalabilidade implementando as recomendações descritas neste artigo. No entanto, o uso de uma retirada exponencial para novas tentativas impedirá que seu aplicativo seja repetido rapidamente, o que poderia tornar a limitação pior. Para obter mais informações, consulte a seção intitulada [tempo limite e erros de servidor ocupado](#timeout-and-server-busy-errors).

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

## <a name="disable-nagle"></a>Desabilitar Nagle

O algoritmo do Nagle é amplamente implementado em redes TCP/IP como um meio para melhorar o desempenho da rede. No entanto, não é ideal em todas as circunstâncias (como ambientes altamente interativos). O algoritmo do Nagle tem um impacto negativo sobre o desempenho das solicitações para o serviço tabela do Azure e você deve desabilitá-la, se possível.

## <a name="message-size"></a>Tamanho da mensagem

O desempenho e a escalabilidade da fila diminuem conforme o tamanho da mensagem aumenta. Coloque apenas as informações de que o destinatário precisa em uma mensagem.  

## <a name="batch-retrieval"></a>Recuperação em lote

Você pode recuperar até 32 mensagens de uma fila em uma única operação. A recuperação em lote pode reduzir o número de viagens de ida e volta do aplicativo cliente, o que é especialmente útil para ambientes, como dispositivos móveis, com alta latência.  

## <a name="queue-polling-interval"></a>Intervalo de sondagem de fila

A maioria dos aplicativos pesquisa mensagens de uma fila, que podem ser uma das maiores fontes de transações para esse aplicativo. Selecione seu intervalo de sondagem de maneira inteligente: a sondagem com muita frequência pode fazer com que seu aplicativo aborde as metas de escalabilidade para a fila. No entanto, às 200.000 transações para $0.01 (no momento da gravação), um único processador sondando uma vez por segundo por um mês custaria menos de 15 centavos, portanto, o custo não é normalmente um fator que afeta sua escolha de intervalo de sondagem.  

Para obter informações atualizadas sobre custos, consulte [preços do armazenamento do Azure](https://azure.microsoft.com/pricing/details/storage/).  

## <a name="use-update-message"></a>Usar mensagem de atualização

Você pode usar a operação **Atualizar mensagem** para aumentar o tempo limite de invisibilidade ou para atualizar as informações de estado de uma mensagem. O uso de uma **mensagem de atualização** pode ser uma abordagem mais eficiente do que ter um fluxo de trabalho que transmite uma tarefa de uma fila para a outra, uma vez que cada etapa do trabalho é concluída. Seu aplicativo pode salvar o estado do trabalho na mensagem e, em seguida, continuar trabalhando, em vez de enfileirar novamente a mensagem para a próxima etapa do trabalho sempre que uma etapa for concluída. Tenha em mente que cada operação **Atualizar mensagem** conta para o destino de escalabilidade.

## <a name="application-architecture"></a>Arquitetura da aplicação

Use filas para tornar a arquitetura do aplicativo escalonável. Veja a seguir algumas maneiras de usar filas para tornar seu aplicativo mais escalonável:  

- Você pode usar as filas do para criar registros posteriores de trabalho para processamento e suavizar cargas de trabalhos em seu aplicativo. Por exemplo, você pode enfileirar solicitações de usuários para executar o trabalho intensivo do processador, como redimensionar imagens carregadas.
- Você pode usar filas para desacoplar partes do seu aplicativo para que possa dimensioná-las de forma independente. Por exemplo, um front-end da Web poderia colocar os resultados da pesquisa de usuários em uma fila para análise e armazenamento posteriores. Você pode adicionar mais instâncias de função de trabalho para processar os dados da fila, conforme necessário.  

## <a name="next-steps"></a>Passos seguintes

- [Escalabilidade e metas de desempenho para o armazenamento de filas](scalability-targets.md)
- [Escalabilidade e metas de desempenho para contas de armazenamento Standard](../common/scalability-targets-standard-account.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)
- [Status e códigos de erro](/rest/api/storageservices/Status-and-Error-Codes2)
