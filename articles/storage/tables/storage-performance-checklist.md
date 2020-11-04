---
title: Lista de verificação de desempenho e escalabilidade para armazenamento de mesa - Azure Storage
description: Uma lista de práticas comprovadas para utilização com armazenamento de mesa no desenvolvimento de aplicações de alto desempenho.
services: storage
author: tamram
ms.author: tamram
ms.service: storage
ms.topic: overview
ms.date: 10/10/2019
ms.subservice: tables
ms.custom: devx-track-csharp
ms.openlocfilehash: 71b1f3cfa1df86b417c468d56f67cd7fe8d71d73
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93316196"
---
# <a name="performance-and-scalability-checklist-for-table-storage"></a>Lista de verificação de desempenho e escalabilidade para armazenamento de mesas

A Microsoft desenvolveu uma série de práticas comprovadas para desenvolver aplicações de alto desempenho com armazenamento de mesa. Esta lista de verificação identifica práticas-chave que os desenvolvedores podem seguir para otimizar o desempenho. Tenha em mente estas práticas enquanto está a desenhar a sua aplicação e durante todo o processo.

O Azure Storage tem metas de escalabilidade e desempenho para capacidade, taxa de transação e largura de banda. Para obter mais informações sobre os objetivos de escalabilidade do Armazenamento Azure, consulte [os objetivos de escalabilidade e desempenho para contas de armazenamento padrão](../common/scalability-targets-standard-account.md?toc=%2fazure%2fstorage%2ftables%2ftoc.json) e [metas de escalabilidade e desempenho para armazenamento de mesas.](scalability-targets.md)

## <a name="checklist"></a>Lista de Verificação

Este artigo organiza práticas comprovadas para o desempenho numa lista de verificação que pode seguir enquanto desenvolve a sua aplicação de armazenamento de mesa.

| Concluído | Categoria | Consideração de design |
| --- | --- | --- |
| &nbsp; |Metas de escalabilidade |[Pode conceber a sua aplicação para utilizar não mais do que o número máximo de contas de armazenamento?](#maximum-number-of-storage-accounts) |
| &nbsp; |Metas de escalabilidade |[Está a evitar aproximar-se dos limites de capacidade e transação?](#capacity-and-transaction-targets) |
| &nbsp; |Metas de escalabilidade |[Está a aproximar-se dos objetivos de escalabilidade para entidades por segundo?](#targets-for-data-operations) |
| &nbsp; |Rede |[Os dispositivos do lado do cliente têm largura de banda suficientemente alta e baixa latência para alcançar o desempenho necessário?](#throughput) |
| &nbsp; |Rede |[Os dispositivos do lado do cliente têm uma ligação de rede de alta qualidade?](#link-quality) |
| &nbsp; |Rede |[O pedido do cliente é na mesma região que a conta de armazenamento?](#location) |
| &nbsp; |Acesso direto ao cliente |[Está a utilizar assinaturas de acesso partilhado (SAS) e partilha de recursos de origem cruzada (CORS) para permitir o acesso direto ao Azure Storage?](#sas-and-cors) |
| &nbsp; |Lotes |[A sua aplicação está a fazer atualizações utilizando transações de grupos de entidades?](#batch-transactions) |
| &nbsp; |Configuração .NET |[Está a utilizar .NET Core 2.1 ou mais tarde para um desempenho ótimo?](#use-net-core) |
| &nbsp; |Configuração .NET |[Configurado o seu cliente para utilizar um número suficiente de ligações simultâneas?](#increase-default-connection-limit) |
| &nbsp; |Configuração .NET |[Para aplicações .NET, configuraste .NET para utilizar um número suficiente de fios?](#increase-minimum-number-of-threads) |
| &nbsp; |Paralelismo |[Assegurou-se de que o paralelismo está devidamente limitado para não sobrecarregar as capacidades do seu cliente ou aproximar-se dos alvos de escalabilidade?](#unbounded-parallelism) |
| &nbsp; |Ferramentas |[Está a utilizar as versões mais recentes das bibliotecas e ferramentas fornecidas pela Microsoft?](#client-libraries-and-tools) |
| &nbsp; |Tentativas |[Está a usar uma política de repetição com um recuo exponencial para erros de estrangulamento e intervalos?](#timeout-and-server-busy-errors) |
| &nbsp; |Tentativas |[A sua aplicação evita recaída por erros não retáveis?](#non-retryable-errors) |
| &nbsp; |Configuração |[Está a usar o JSON para os seus pedidos de mesa?](#use-json) |
| &nbsp; |Configuração |[Desligou o algoritmo Nagle para melhorar o desempenho de pequenos pedidos?](#disable-nagle) |
| &nbsp; |Tabelas e divisórias |[Dividiu devidamente os seus dados?](#schema) |
| &nbsp; |Divisórias quentes |[Estás a evitar padrões só de apêndices e prepend?](#append-only-and-prepend-only-patterns) |
| &nbsp; |Divisórias quentes |[As suas inserções/atualizações estão espalhadas por muitas divisórias?](#high-traffic-data) |
| &nbsp; |Âmbito de consulta |[Concebeu o seu esquema para permitir que as consultas pontuais sejam usadas na maioria dos casos, e consultas de mesa para serem usadas com moderação?](#query-scope) |
| &nbsp; |Densidade de consulta |[Normalmente, as suas consultas apenas digitalizam e devolvem as linhas que a sua aplicação irá utilizar?](#query-density) |
| &nbsp; |Limitação dos dados devolvidos |[Está a usar a filtragem para evitar o regresso de entidades que não são necessárias?](#limiting-the-amount-of-data-returned) |
| &nbsp; |Limitação dos dados devolvidos |[Está a utilizar a projeção para evitar a devolução de propriedades que não são necessárias?](#limiting-the-amount-of-data-returned) |
| &nbsp; |Desnormalização |[Desnormalizou os seus dados de modo a evitar consultas ineficientes ou múltiplos pedidos de leitura ao tentar obter dados?](#denormalization) |
| &nbsp; |Insira, atualize e elimine |[Está a reunir pedidos que precisam de ser transacionais ou que podem ser feitos ao mesmo tempo para reduzir as viagens de ida e volta?](#batching) |
| &nbsp; |Insira, atualize e elimine |[Está a evitar recuperar uma entidade apenas para determinar se deve ou não ligar para inserir ou atualizar?](#upsert) |
| &nbsp; |Insira, atualize e elimine |[Já considerou armazenar séries de dados que serão frequentemente recuperados numa única entidade como propriedades em vez de múltiplas entidades?](#storing-data-series-in-a-single-entity) |
| &nbsp; |Insira, atualize e elimine |[Para entidades que serão sempre recuperadas em conjunto e que podem ser escritas em lotes (por exemplo, dados de séries de tempo), já pensou em usar bolhas em vez de tabelas?](#storing-structured-data-in-blobs) |

## <a name="scalability-targets"></a>Metas de escalabilidade

Se a sua aplicação se aproximar ou exceder qualquer um dos alvos de escalabilidade, poderá encontrar atrasos de transação aumentadas ou estrangulamentos. Quando o Azure Storage acelera a sua aplicação, o serviço começa a devolver códigos de erro 503 (Server busy) ou 500 (Tempo limite de funcionamento). Evitar estes erros mantendo-se dentro dos limites dos objetivos de escalabilidade é uma parte importante para melhorar o desempenho da sua aplicação.

Para obter mais informações sobre os objetivos de escalabilidade para o serviço Table, consulte [metas de escalabilidade e desempenho para armazenamento de mesa.](scalability-targets.md)

### <a name="maximum-number-of-storage-accounts"></a>Número máximo de contas de armazenamento

Se estiver a aproximar-se do número máximo de contas de armazenamento permitidas para uma determinada combinação subscrição/região, está a utilizar várias contas de armazenamento para aumentar as operações de entrada, saída, E/S por segundo (IOPS) ou capacidade? Neste cenário, a Microsoft recomenda que aproveite os limites acrescidos para as contas de armazenamento para reduzir o número de contas de armazenamento necessárias para a sua carga de trabalho, se possível. Contacte [o Suporte Azure](https://azure.microsoft.com/support/options/) para solicitar limites acrescidos para a sua conta de armazenamento. Para obter mais informações, consulte [anunciar contas de armazenamento de maior escala.](https://azure.microsoft.com/blog/announcing-larger-higher-scale-storage-accounts/)

### <a name="capacity-and-transaction-targets"></a>Objetivos de capacidade e transações

Se a sua aplicação estiver a aproximar-se dos objetivos de escalabilidade de uma única conta de armazenamento, considere a adoção de uma das seguintes abordagens:  

- Reconsidere a carga de trabalho que faz com que a sua aplicação se aproxime ou exceda o objetivo de escalabilidade. Pode desenhá-lo de forma diferente para utilizar menos largura de banda ou capacidade, ou menos transações?
- Se a sua aplicação tiver de exceder um dos objetivos de escalabilidade, em seguida, crie várias contas de armazenamento e partition os seus dados de aplicação através dessas contas de armazenamento múltiplas. Se utilizar este padrão, então certifique-se de conceber a sua aplicação para que possa adicionar mais contas de armazenamento no futuro para equilibrar a carga. As próprias contas de armazenamento não têm qualquer custo que não seja a sua utilização em termos de dados armazenados, transações efe feitas ou dados transferidos.
- Se a sua aplicação estiver a aproximar-se dos alvos de largura de banda, considere comprimir dados do lado do cliente para reduzir a largura de banda necessária para enviar os dados para o Azure Storage.
    Embora a compressão de dados possa salvar a largura de banda e melhorar o desempenho da rede, também pode ter efeitos negativos no desempenho. Avaliar o impacto de desempenho dos requisitos adicionais de processamento para a compressão de dados e descompressão do lado do cliente. Tenha em mente que armazenar dados comprimidos pode dificultar a resolução de problemas, pois pode ser mais desafiante visualizar os dados usando ferramentas padrão.
- Se a sua aplicação estiver a aproximar-se dos alvos de escalabilidade, certifique-se de que está a utilizar um backoff exponencial para retrações. É melhor tentar evitar atingir os objetivos de escalabilidade implementando as recomendações descritas neste artigo. No entanto, a utilização de um recuo exponencial para as retreições impedirá que a sua aplicação volte rapidamente, o que pode piorar a aceleração. Para obter mais informações, consulte a secção intitulada [Timeout e Server Busy errors](#timeout-and-server-busy-errors).

### <a name="targets-for-data-operations"></a>Alvos para operações de dados

A azure Storage load balance à medida que o tráfego para a sua conta de armazenamento aumenta, mas se o tráfego apresentar rajadas repentinas, você pode não ser capaz de obter este volume de produção imediatamente. Espere ver estrangulamentos e/ou intervalos durante a explosão, uma vez que o Azure Storage equilibra automaticamente a sua tabela. O aumento lentamente proporciona geralmente melhores resultados, uma vez que o sistema tem tempo para carregar o equilíbrio adequadamente.

#### <a name="entities-per-second-storage-account"></a>Entidades por segundo (conta de armazenamento)

O limite de escalabilidade para aceder às tabelas é de até 20.000 entidades (1 KB cada) por segundo para uma conta. Em geral, cada entidade que é inserida, atualizada, eliminada ou digitalizada conta para este alvo. Assim, um lote que contém 100 entidades contaria como 100 entidades. Uma consulta que digitaliza 1000 entidades e devolve 5 contaria como 1000 entidades.

#### <a name="entities-per-second-partition"></a>Entidades por segundo (partição)

Dentro de uma única partição, o objetivo de escalabilidade para aceder às tabelas é de 2.000 entidades (1 KB cada) por segundo, utilizando a mesma contagem descrita na secção anterior.

## <a name="networking"></a>Rede

Os constrangimentos físicos da rede da aplicação podem ter um impacto significativo no desempenho. As seguintes secções descrevem algumas das limitações que os utilizadores podem encontrar.  

### <a name="client-network-capability"></a>Capacidade de rede de clientes

A largura de banda e a qualidade da ligação de rede desempenham papéis importantes no desempenho da aplicação, conforme descrito nas seguintes secções.

#### <a name="throughput"></a>Débito

Para a largura de banda, o problema é muitas vezes as capacidades do cliente. Casos maiores de Azure têm NICs com maior capacidade, por isso deve considerar usar um caso maior ou mais VMs se precisar de limites de rede mais elevados a partir de uma única máquina. Se estiver a aceder ao Azure Storage a partir de uma aplicação no local, então aplica-se a mesma regra: compreenda as capacidades de rede do dispositivo cliente e a conectividade da rede com o local de Armazenamento Azure e melhore-as conforme necessário ou desenhe a sua aplicação para trabalhar dentro das suas capacidades.

#### <a name="link-quality"></a>Qualidade de ligação

Como em qualquer utilização da rede, tenha em mente que as condições de rede que resultam em erros e perda de pacotes retardarão a produção eficaz.  A utilização do WireShark ou da NetMon pode ajudar a diagnosticar este problema.  

### <a name="location"></a>Localização

Em qualquer ambiente distribuído, colocar o cliente perto do servidor oferece na melhor performance. Para aceder ao Azure Storage com a latência mais baixa, a melhor localização para o seu cliente é dentro da mesma região de Azure. Por exemplo, se tiver uma aplicação web Azure que utiliza o Azure Storage, então localize-os numa única região, como o Eua West ou o Asia Southeast. A co-localização de recursos reduz a latência e o custo, uma vez que o uso de largura de banda numa única região é gratuito.  

Se as aplicações do cliente acederem ao Azure Storage mas não estiverem hospedadas no Azure, como aplicações de dispositivos móveis ou em serviços empresariais de instalações, então a localização da conta de armazenamento numa região próxima a esses clientes pode reduzir a latência. Se os seus clientes são amplamente distribuídos (por exemplo, alguns na América do Norte, e alguns na Europa), então considere usar uma conta de armazenamento por região. Esta abordagem é mais fácil de implementar se os dados que as lojas de aplicações são específicos de cada um dos utilizadores, e não requer a replicação de dados entre contas de armazenamento.

## <a name="sas-and-cors"></a>SAS e CORS

Suponha que precisa de autorizar códigos como o JavaScript que está a ser executada no navegador web de um utilizador ou numa aplicação de telemóvel para aceder a dados no Azure Storage. Uma abordagem é construir uma aplicação de serviço que atue como um representante. O dispositivo do utilizador autentica-se com o serviço, que por sua vez autoriza o acesso aos recursos de Armazenamento Azure. Desta forma, pode evitar expor as chaves da sua conta de armazenamento em dispositivos inseguros. No entanto, esta abordagem coloca uma sobrecarga significativa na aplicação de serviço, uma vez que todos os dados transferidos entre o dispositivo do utilizador e o Azure Storage devem passar pela aplicação de serviço.

Pode evitar a utilização de uma aplicação de serviço como proxy para o Azure Storage utilizando assinaturas de acesso partilhado (SAS). Utilizando o SAS, pode ativar o dispositivo do seu utilizador a estojos diretamente para o Azure Storage utilizando um token de acesso limitado. Por exemplo, se um utilizador quiser enviar uma fotografia para a sua aplicação, então a sua aplicação de serviço pode gerar um SAS e enviá-la para o dispositivo do utilizador. O token SAS pode conceder permissão para escrever a um recurso de armazenamento Azure por um intervalo de tempo especificado, após o qual o token SAS expira. Para obter mais informações sobre o SAS, consulte [Grant acesso limitado aos recursos de armazenamento Azure utilizando assinaturas de acesso partilhado (SAS)](../common/storage-sas-overview.md).  

Normalmente, um navegador web não permitirá que o JavaScript numa página que é hospedada por um site num domínio realize determinadas operações, como operações de escrita, para outro domínio. Conhecida como a mesma política de origem, esta política impede que um script malicioso numa página obtenha acesso a dados em outra página web. No entanto, a política de mesma origem pode ser uma limitação quando se constrói uma solução na nuvem. A partilha de recursos de origem cruzada (CORS) é uma funcionalidade de navegador que permite ao domínio alvo comunicar ao navegador que confia em pedidos originários do domínio de origem.

Por exemplo, suponha que uma aplicação web em execução em Azure faça um pedido de recurso para uma conta de Armazenamento Azure. A aplicação web é o domínio de origem, e a conta de armazenamento é o domínio alvo. Pode configurar o CORS para que qualquer um dos serviços de Armazenamento Azure comunique ao navegador web que os pedidos do domínio de origem são fidedignos pelo Azure Storage. Para obter mais informações sobre o CORS, consulte [o suporte de partilha de recursos cross-origin (CORS) para o Armazenamento Azure](/rest/api/storageservices/Cross-Origin-Resource-Sharing--CORS--Support-for-the-Azure-Storage-Services).  
  
Tanto o SAS como o CORS podem ajudá-lo a evitar cargas desnecessárias na sua aplicação web.  

## <a name="batch-transactions"></a>Transações de lotes

O serviço Table suporta transações de lotes em entidades que estão na mesma tabela e pertencem ao mesmo grupo de partição. Para obter mais informações, consulte [a Realização de transações de grupos de entidades.](/rest/api/storageservices/performing-entity-group-transactions)

## <a name="net-configuration"></a>Configuração .NET

Se utilizar o Quadro .NET, esta secção lista várias definições de configuração rápidas que pode utilizar para fazer melhorias significativas no desempenho.  Se utilizar outras línguas, verifique se conceitos semelhantes se aplicam na sua língua escolhida.  

### <a name="use-net-core"></a>Use .NET Core

Desenvolva as suas aplicações de Armazenamento Azure com .NET Core 2.1 ou posteriormente para tirar partido das melhorias de desempenho. A utilização de .NET Core 3.x é recomendada quando possível.

Para obter mais informações sobre melhorias de desempenho em .NET Core, consulte as seguintes publicações de blog:

- [Melhorias de desempenho em .NET Core 3.0](https://devblogs.microsoft.com/dotnet/performance-improvements-in-net-core-3-0/)
- [Melhorias de desempenho em .NET Core 2.1](https://devblogs.microsoft.com/dotnet/performance-improvements-in-net-core-2-1/)

### <a name="increase-default-connection-limit"></a>Aumentar o limite de ligação padrão

Em .NET, o seguinte código aumenta o limite de ligação predefinido (que normalmente é 2 num ambiente de cliente ou 10 em ambiente de servidor) para 100. Normalmente, deve definir o valor para aproximadamente o número de fios utilizados pela sua aplicação.  

```csharp
ServicePointManager.DefaultConnectionLimit = 100; //(Or More)  
```

Desa estada o limite de ligação antes de abrir quaisquer ligações.  

Para outras linguagens de programação, consulte a documentação dessa língua para determinar como definir o limite de ligação.  

Para mais informações, consulte o blog post [Web Services: Concurrent Connections](/archive/blogs/darrenj/web-services-concurrent-connections).  

### <a name="increase-minimum-number-of-threads"></a>Aumentar o número mínimo de fios

Se estiver a utilizar chamadas sincronizadas juntamente com tarefas assíncronos, é melhor aumentar o número de fios na piscina de roscas:

```csharp
ThreadPool.SetMinThreads(100,100); //(Determine the right number for your application)  
```

Para obter mais informações, consulte o método [ThreadPool.SetMinThreads.](/dotnet/api/system.threading.threadpool.setminthreads)  

## <a name="unbounded-parallelism"></a>Paralelismo ilimitado

Embora o paralelismo possa ser ótimo para o desempenho, tenha cuidado com a utilização de paralelismo ilimitado, o que significa que não há limite imposto sobre o número de fios ou pedidos paralelos. Certifique-se de limitar os pedidos paralelos para carregar ou descarregar dados, aceder a várias divisórias na mesma conta de armazenamento ou aceder a vários itens na mesma partição. Se o paralelismo não for limitado, a sua aplicação pode exceder as capacidades do dispositivo cliente ou os alvos de escalabilidade da conta de armazenamento, resultando em atrasos e estrangulamentos mais longos.  

## <a name="client-libraries-and-tools"></a>Bibliotecas e ferramentas do cliente

Para um melhor desempenho, utilize sempre as mais recentes bibliotecas e ferramentas de clientes fornecidas pela Microsoft. As bibliotecas de clientes do Azure Storage estão disponíveis para uma variedade de idiomas. O Azure Storage também suporta o PowerShell e o Azure CLI. A Microsoft desenvolve ativamente estas bibliotecas e ferramentas de clientes com o desempenho em mente, mantém-nas atualizadas com as versões de serviço mais recentes, e garante que eles lidam com muitas das práticas de desempenho comprovadas internamente.

## <a name="handle-service-errors"></a>Lidar com erros de serviço

O Azure Storage retorna um erro quando o serviço não pode processar um pedido. Compreender os erros que podem ser devolvidos pelo Azure Storage num dado cenário é útil para otimizar o desempenho.

### <a name="timeout-and-server-busy-errors"></a>Erros de timeout e servidor ocupado

O Azure Storage pode acelerar a sua aplicação se se aproximar dos limites de escalabilidade. Em alguns casos, o Azure Storage pode não conseguir lidar com um pedido devido a alguma condição transitória. Em ambos os casos, o serviço pode devolver um erro de 503 (Server Busy) ou 500 (Timeout). Estes erros também podem ocorrer se o serviço estiver a reequilibtar as divisórias de dados para permitir uma maior produção. A aplicação do cliente deve normalmente voltar a tentar a operação que causa um destes erros. No entanto, se o Azure Storage está a estrangular a sua aplicação por estar a exceder os alvos de escalabilidade, ou mesmo que o serviço não tenha conseguido servir o pedido por outra razão, as retrações agressivas podem piorar o problema. Recomenda-se a utilização de uma política de retrocesso exponencial e as bibliotecas do cliente estão em incumprimento deste comportamento. Por exemplo, a sua aplicação pode voltar a tentar após 2 segundos, depois 4 segundos, depois 10 segundos, depois 30 segundos, e depois desistir completamente. Desta forma, a sua aplicação reduz significativamente a sua carga no serviço, em vez de exacerbar comportamentos que podem levar a estrangulamentos.  

Os erros de conectividade podem ser novamente julgados imediatamente, porque não são o resultado de estrangulamento e espera-se que sejam transitórios.  

### <a name="non-retryable-errors"></a>Erros não retáveis

As bibliotecas do cliente lidam com retreiças com consciência de quais os erros que podem ser novamente julgados e que não podem. No entanto, se estiver a ligar diretamente para a API do Azure Storage REST, existem alguns erros que não deve voltar a tentar. Por exemplo, um erro de 400 (Mau Pedido) indica que o pedido do cliente enviou um pedido que não pôde ser processado porque não estava na forma esperada. Reencando este pedido resulta sempre na mesma resposta, pelo que não faz sentido voltar a tentar. Se estiver a ligar diretamente para a AZure Storage REST API, esteja ciente de potenciais erros e se devem ser novamente julgados.

Para obter mais informações sobre os códigos de erro do Azure Storage, consulte [códigos de estado e de erro](/rest/api/storageservices/status-and-error-codes2).

## <a name="configuration"></a>Configuração

Esta secção lista várias configurações rápidas que pode utilizar para fazer melhorias significativas no desempenho no serviço Tabela:

### <a name="use-json"></a>Use JSON

Começando pela versão de serviço de armazenamento 2013-08-15, o serviço Table suporta a utilização do JSON em vez do formato AtomPub baseado em XML para a transferência de dados da tabela. A utilização do JSON pode reduzir os tamanhos de carga útil em até 75% e pode melhorar significativamente o desempenho da sua aplicação.

Para obter mais informações, consulte as [tabelas post Microsoft Azure: Introdução do Formato JSON](/archive/blogs/windowsazurestorage/windows-azure-tables-introducing-json) e [Payload para Operações de Serviço de Tabela](/rest/api/storageservices/Payload-Format-for-Table-Service-Operations).

### <a name="disable-nagle"></a>Desativar o Nagle

O algoritmo de Nagle é amplamente implementado através das redes TCP/IP como forma de melhorar o desempenho da rede. No entanto, não é o ideal em todas as circunstâncias (tais como ambientes altamente interativos). O algoritmo da Nagle tem um impacto negativo no desempenho dos pedidos para o serviço Azure Table, e deve desativá-lo se possível.

## <a name="schema"></a>Esquema

A forma como representa e consulta os seus dados é o maior fator único que afeta o desempenho do serviço Table. Embora cada aplicação seja diferente, esta secção descreve algumas práticas comprovadas gerais que se relacionam com:

- Design de mesa
- Consultas eficientes
- Atualizações de dados eficientes

### <a name="tables-and-partitions"></a>Tabelas e divisórias

As mesas dividem-se em divisórias. Todas as entidades armazenadas numa partição partilham a mesma chave de partição e têm uma chave de linha única para identificá-la dentro dessa divisória. As partições proporcionam benefícios, mas também introduzem limites de escalabilidade.

- Benefícios: Pode atualizar as entidades na mesma partição numa única transação de lote atómico que contenha até 100 operações de armazenamento separadas (limite de 4 MB de tamanho total). Assumindo o mesmo número de entidades a recuperar, também pode consultar os dados dentro de uma única partição de forma mais eficiente do que os dados que abrangem as divisórias (embora leia para mais recomendações sobre a consulta dos dados dos quadros).
- Limite de escala: O acesso a entidades armazenadas numa única divisória não pode ser equilibrado em carga porque as divisórias suportam transações de lotes atómicos. Por esta razão, o objetivo de escalabilidade para uma divisória de mesa individual é inferior ao do serviço table como um todo.

Devido a estas características de tabelas e divisórias, deve adotar os seguintes princípios de design:

- Localize os dados que a sua aplicação do cliente frequentemente atualiza ou consulta na mesma unidade lógica de trabalho na mesma divisão. Por exemplo, localize os dados na mesma partição se a sua aplicação estiver a agregar escritos ou se estiver a realizar operações de lote atómico. Além disso, os dados numa única partição podem ser consultados de forma mais eficiente numa única consulta do que os dados através de divisórias.
- Localize os dados que a sua aplicação do cliente não insere, atualização ou consulta na mesma unidade lógica de trabalho (isto é, numa única consulta ou atualização de lote) em divisórias separadas. Tenha em mente que não há limite para o número de chaves de partição numa única tabela, por isso ter milhões de chaves de partição não é um problema e não terá impacto no desempenho. Por exemplo, se a sua aplicação for um website popular com login do utilizador, usar o ID do Utilizador como chave de partição pode ser uma boa escolha.

#### <a name="hot-partitions"></a>Divisórias quentes

Uma divisória quente é aquela que está a receber uma percentagem desproporcional do tráfego para uma conta, e não pode ser equilibrada por ser uma única divisória. Em geral, as divisórias quentes são criadas de duas maneiras:

#### <a name="append-only-and-prepend-only-patterns"></a>Apenas padrões de apêndice e prepend

O padrão "Apend Only" é aquele em que todo (ou quase todos) do tráfego para uma determinada chave de partição aumenta e diminui de acordo com o tempo atual. Por exemplo, suponha que a sua aplicação utiliza a data atual como chave de partição para dados de registo. Este design resulta em todas as inserções que vão para a última divisória da sua mesa, e o sistema não consegue carregar o equilíbrio corretamente. Se o volume de tráfego para essa partição exceder o objetivo de escalabilidade do nível de partição, então resultará em estrangulamento. É melhor garantir que o tráfego é enviado para várias divisórias, para permitir o equilíbrio de carga dos pedidos em toda a sua mesa.

#### <a name="high-traffic-data"></a>Dados de alto tráfego

Se o seu esquema de partição resultar numa única partição que apenas tenha dados muito mais utilizados do que outras divisórias, também poderá ver estrangulamento à medida que a partição se aproxima do objetivo de escalabilidade para uma única partição. É melhor certificar-se de que o seu esquema de partição resulta em nenhuma partição que se aproxime dos alvos de escalabilidade.

### <a name="querying"></a>A consultar

Esta secção descreve práticas comprovadas para consulta do serviço table.

#### <a name="query-scope"></a>Âmbito de consulta

Existem várias formas de especificar o leque de entidades a consultar. A lista a seguir descreve cada opção para o âmbito de consulta.

- **Consultas de ponto:** - Uma consulta de pontos recupera exatamente uma entidade especificando tanto a chave de partição como a chave de linha da entidade para recuperar. Estas consultas são eficientes, e você deve usá-las sempre que possível.
- **Consultas de partição:** Uma consulta de partição é uma consulta que recupera um conjunto de dados que partilha uma chave de partição comum. Tipicamente, a consulta especifica uma gama de valores-chave de linha ou uma gama de valores para alguma propriedade de entidade, além de uma chave de partição. Estas consultas são menos eficientes do que as consultas pontuais, e devem ser usadas com moderação.
- **Consultas de tabela:** Uma consulta de mesa é uma consulta que recupera um conjunto de entidades que não partilham uma chave de partição comum. Estas consultas não são eficientes e deve evitá-las se possível.

Em geral, evite digitalizações (consultas maiores do que uma única entidade), mas se tiver de digitalizar, tente organizar os seus dados para que os seus exames recuperem os dados de que necessita sem digitalizar ou devolver quantidades significativas de entidades de que não necessita.

#### <a name="query-density"></a>Densidade de consulta

Outro fator-chave na eficiência da consulta é o número de entidades devolvidas em comparação com o número de entidades digitalizadas para encontrar o conjunto devolvido. Se a sua aplicação efetuar uma consulta de tabela com um filtro para um valor patrimonial que apenas 1% das partilhas de dados, a consulta irá digitalizar 100 entidades por cada entidade que devolva. Os alvos de escala da tabela discutidos anteriormente dizem respeito ao número de entidades digitalizadas, e não ao número de entidades devolvidas: uma baixa densidade de consulta pode facilmente fazer com que o serviço de Tabela acione a sua aplicação porque deve digitalizar tantas entidades para recuperar a entidade que procura. Para obter mais informações sobre como evitar estrangulamentos, consulte a secção intitulada [Denormalização](#denormalization).

#### <a name="limiting-the-amount-of-data-returned"></a>Limitando a quantidade de dados devolvidos

Quando souber que uma consulta devolverá entidades que não necessita na aplicação do cliente, considere usar um filtro para reduzir o tamanho do conjunto devolvido. Embora as entidades não devolvidas ao cliente ainda contem para os limites de escalabilidade, o desempenho da sua aplicação melhorará devido ao reduzido tamanho da carga útil da rede e ao número reduzido de entidades que a sua aplicação de clientes deve processar. Tenha em mente que os alvos de escalabilidade dizem respeito ao número de entidades digitalizadas, pelo que uma consulta que filtra muitas entidades pode ainda resultar em estrangulamento, mesmo que poucas entidades sejam devolvidas. Para obter mais informações sobre a eficiência das consultas, consulte a secção intitulada [Densidade de Consulta](#query-density).

Se a sua aplicação ao cliente necessitar apenas de um conjunto limitado de propriedades das entidades da sua mesa, pode utilizar a projeção para limitar o tamanho do conjunto de dados devolvidos. Tal como na filtragem, a projeção ajuda a reduzir a carga de rede e o processamento do cliente.

#### <a name="denormalization"></a>Desnormalização

Ao contrário de trabalhar com bases de dados relacionais, as práticas comprovadas para consulta eficiente de dados de tabelas levam a desnormalizar os seus dados. Ou seja, duplicando os mesmos dados em várias entidades (uma para cada chave que pode utilizar para encontrar os dados) para minimizar o número de entidades que uma consulta deve digitalizar para encontrar os dados de que o cliente necessita, em vez de ter de digitalizar um grande número de entidades para encontrar os dados de que a sua aplicação necessita. Por exemplo, num site de e-commerce, poderá querer encontrar uma encomenda tanto pelo ID do cliente (dê-me as ordens deste cliente) como pela data (dê-me encomendas numa data). No Armazenamento de Mesa, o melhor é armazenar a entidade (ou uma referência a ela) duas vezes – uma vez com o Table Name, PK e RK para facilitar a descoberta pelo ID do cliente, uma vez que facilita a sua descoberta até à data.  

### <a name="insert-update-and-delete"></a>Insira, atualize e elimine

Esta secção descreve práticas comprovadas para modificar entidades armazenadas no serviço Tabela.  

#### <a name="batching"></a>Lotes

As transações de lote são conhecidas como transações de grupos de entidades no Azure Storage. Todas as operações dentro de uma transação de grupo de entidade devem estar numa única divisória numa única tabela. Sempre que possível, utilize transações de grupos de entidades para efetuar inserções, atualizações e eliminações em lotes. A utilização de transações de grupos de entidades reduz o número de viagens de ida e volta da sua aplicação de cliente para o servidor, reduz o número de transações faturadas (uma transação de grupo de entidades conta como uma única transação para efeitos de faturação e pode conter até 100 operações de armazenamento), e permite atualizações atómicas (todas as operações têm sucesso ou falham numa transação de grupo de entidades). Ambientes com elevadas latências, como dispositivos móveis, beneficiarão muito da utilização de transações de grupos de entidades.  

#### <a name="upsert"></a>Upsert

Utilize as operações **de Upsert** de tabela sempre que possível. Existem dois tipos de **Upsert,** ambos podem ser mais eficientes do que uma operação tradicional de **Inserção** e **Atualização:**  

- **InserirOrMerge** : Utilize esta operação quando pretender carregar um subconjunto das propriedades da entidade, mas não tem a certeza se a entidade já existe. Se a entidade existir, esta chamada atualiza as propriedades incluídas na operação **Upsert,** e deixa todas as propriedades existentes como são, se a entidade não existir, insere a nova entidade. Isto é semelhante ao uso da projeção numa consulta, na medida em que só precisa de carregar as propriedades que estão a mudar.
- **InserirOrReplace** : Utilize esta operação quando pretender carregar uma entidade inteiramente nova, mas não tem a certeza se já existe. Utilize esta operação quando souber que a entidade recém-carregada está inteiramente correta porque substitui completamente a antiga entidade. Por exemplo, pretende atualizar a entidade que armazena a localização atual de um utilizador, independentemente de a aplicação ter ou não armazenado dados de localização previamente armazenados para o utilizador; a nova entidade de localização está completa, e não precisa de nenhuma informação de nenhuma entidade anterior.

#### <a name="storing-data-series-in-a-single-entity"></a>Armazenar séries de dados numa única entidade

Por vezes, uma aplicação armazena uma série de dados que frequentemente precisa para recuperar tudo de uma vez: por exemplo, uma aplicação pode rastrear o uso do CPU ao longo do tempo, de modo a traçar um gráfico rolante dos dados das últimas 24 horas. Uma abordagem é ter uma entidade de mesa por hora, com cada entidade representando uma hora específica e armazenando o uso do CPU para essa hora. Para traçar estes dados, a aplicação precisa de recuperar as entidades que detêm os dados das 24 horas mais recentes.  

Em alternativa, a sua aplicação poderia armazenar o uso do CPU por cada hora como propriedade separada de uma única entidade: para atualizar a cada hora, a sua aplicação pode usar uma única chamada **InsertOrMerge Upsert** para atualizar o valor para a hora mais recente. Para traçar os dados, a aplicação só precisa de recuperar uma única entidade em vez de 24, fazendo uma consulta eficiente. Para obter mais informações sobre a eficiência da consulta, consulte a secção intitulada [Âmbito de Consulta](#query-scope)).

#### <a name="storing-structured-data-in-blobs"></a>Armazenar dados estruturados em bolhas

Se estiver a realizar inserções de lotes e, em seguida, a recuperar gamas de entidades em conjunto, considere usar bolhas em vez de tabelas. Um bom exemplo é um ficheiro de registo. Pode lotar vários minutos de troncos e inseri-los e, em seguida, recuperar vários minutos de registos de cada vez. Neste caso, o desempenho é melhor se utilizar bolhas em vez de tabelas, uma vez que pode reduzir significativamente o número de objetos escritos ou lidos, e também possivelmente o número de pedidos que precisam de ser feitos.  

## <a name="next-steps"></a>Passos seguintes

- [Metas de escalabilidade e desempenho para armazenamento de mesa](scalability-targets.md)
- [Metas de escalabilidade e desempenho para contas de armazenamento padrão](../common/scalability-targets-standard-account.md?toc=%2fazure%2fstorage%2ftables%2ftoc.json)
- [Códigos de estado e de erro](/rest/api/storageservices/Status-and-Error-Codes2)