---
title: Lista de verificação de desempenho e escalabilidade para armazenamento de mesa - Armazenamento Azure
description: Uma lista de práticas comprovadas para utilização com armazenamento de mesa no desenvolvimento de aplicações de alto desempenho.
services: storage
author: tamram
ms.service: storage
ms.topic: overview
ms.date: 10/10/2019
ms.author: tamram
ms.subservice: tables
ms.openlocfilehash: 89581c8ae2fbdbb55a2abfbd527c8fdcf4b65761
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "75749547"
---
# <a name="performance-and-scalability-checklist-for-table-storage"></a>Lista de verificação de desempenho e escalabilidade para armazenamento de mesa

A Microsoft desenvolveu uma série de práticas comprovadas para desenvolver aplicações de alto desempenho com armazenamento de mesa. Esta lista de verificação identifica práticas-chave que os desenvolvedores podem seguir para otimizar o desempenho. Tenha em mente estas práticas enquanto está a desenhar a sua aplicação e durante todo o processo.

O Azure Storage tem metas de escalabilidade e desempenho para capacidade, taxa de transação e largura de banda. Para obter mais informações sobre os objetivos de escalabilidade do Armazenamento Azure, consulte os objetivos de [escalabilidade e desempenho para contas de armazenamento padrão](../common/scalability-targets-standard-account.md?toc=%2fazure%2fstorage%2ftables%2ftoc.json) e metas de escalabilidade e desempenho para armazenamento de [mesas.](scalability-targets.md)

## <a name="checklist"></a>Lista de Verificação

Este artigo organiza práticas comprovadas para o desempenho numa lista de verificação que pode seguir enquanto desenvolve a sua aplicação de armazenamento de mesa.

| Concluído | Categoria | Consideração de design |
| --- | --- | --- |
| &nbsp; |Metas de escalabilidade |[Pode projetar a sua aplicação para usar não mais do que o número máximo de contas de armazenamento?](#maximum-number-of-storage-accounts) |
| &nbsp; |Metas de escalabilidade |[Está a evitar aproximar-se dos limites de capacidade e transação?](#capacity-and-transaction-targets) |
| &nbsp; |Metas de escalabilidade |[Está a aproximar-se dos objetivos de escalabilidade das entidades por segundo?](#targets-for-data-operations) |
| &nbsp; |Redes |[Os dispositivos do lado do cliente têm largura de banda suficientemente elevada e baixa latência para alcançar o desempenho necessário?](#throughput) |
| &nbsp; |Redes |[Os dispositivos do lado do cliente têm uma ligação de rede de alta qualidade?](#link-quality) |
| &nbsp; |Redes |[A aplicação do cliente está na mesma região que a conta de armazenamento?](#location) |
| &nbsp; |Acesso direto ao Cliente |[Está a utilizar assinaturas de acesso partilhado (SAS) e partilha de recursos de origem cruzada (CORS) para permitir o acesso direto ao Armazenamento Azure?](#sas-and-cors) |
| &nbsp; |Lotes |[A sua aplicação está a atualizar-se utilizando as transações do grupo de entidades?](#batch-transactions) |
| &nbsp; |configuração .NET |[Está a utilizar .NET Core 2.1 ou mais tarde para um desempenho ótimo?](#use-net-core) |
| &nbsp; |configuração .NET |[Já configurou o seu cliente para utilizar um número suficiente de ligações simultâneas?](#increase-default-connection-limit) |
| &nbsp; |configuração .NET |[Para aplicações .NET, configurou .NET para utilizar um número suficiente de fios?](#increase-minimum-number-of-threads) |
| &nbsp; |Paralelismo |[Assegurou que o paralelismo está devidamente limitado para que não sobrecarregue as capacidades do seu cliente ou se aproxime dos alvos de escalabilidade?](#unbounded-parallelism) |
| &nbsp; |Ferramentas |[Está a utilizar as versões mais recentes das bibliotecas e ferramentas fornecidas pela Microsoft?](#client-libraries-and-tools) |
| &nbsp; |Tentativas |[Estás a usar uma política de retry com um recuo exponencial para erros de estrangulamento e intervalos?](#timeout-and-server-busy-errors) |
| &nbsp; |Tentativas |[A sua aplicação está a evitar tentativas de erros não retáveis?](#non-retryable-errors) |
| &nbsp; |Configuração |[Está a usar a JSON para os seus pedidos de mesa?](#use-json) |
| &nbsp; |Configuração |[Desligou o algoritmo de Nagle para melhorar o desempenho de pequenos pedidos?](#disable-nagle) |
| &nbsp; |Mesas e divisórias |[Dividiu devidamente os seus dados?](#schema) |
| &nbsp; |Divisórias quentes |[Estáa evitar padrões só para apêndices e preparações?](#append-only-and-prepend-only-patterns) |
| &nbsp; |Divisórias quentes |[As suas inserções/atualizações estão espalhadas por muitas divisórias?](#high-traffic-data) |
| &nbsp; |Âmbito de consulta |[Já desenhou o seu esquema para permitir que consultas de pontos sejam usadas na maioria dos casos, e consultas de mesa para serem usadas com moderação?](#query-scope) |
| &nbsp; |Densidade de consulta |[As suas consultas normalmente só digitalizam e devolvem linhas que a sua aplicação irá utilizar?](#query-density) |
| &nbsp; |Limitação dos dados devolvidos |[Está a usar a filtragem para evitar o regresso de entidades que não são necessárias?](#limiting-the-amount-of-data-returned) |
| &nbsp; |Limitação dos dados devolvidos |[Está a usar a projeção para evitar a devolução de propriedades que não são necessárias?](#limiting-the-amount-of-data-returned) |
| &nbsp; |Desnormalização |[Desnormalizou os seus dados de forma a evitar consultas ineficientes ou pedidos de leitura múltiplas ao tentar obter dados?](#denormalization) |
| &nbsp; |Inserir, atualizar e eliminar |[Está a fazer pedidos que precisam de ser transacionais ou que podem ser feitos ao mesmo tempo para reduzir as viagens de ida e volta?](#batching) |
| &nbsp; |Inserir, atualizar e eliminar |[Está a evitar recuperar uma entidade apenas para determinar se deve ou não ligar para inserir ou atualizar?](#upsert) |
| &nbsp; |Inserir, atualizar e eliminar |[Já considerou armazenar uma série de dados que serão frequentemente recuperados numa única entidade como propriedades em vez de várias entidades?](#storing-data-series-in-a-single-entity) |
| &nbsp; |Inserir, atualizar e eliminar |[Para entidades que serão sempre recuperadas em conjunto e que podem ser escritas em lotes (por exemplo, dados da série time), já considerou usar bolhas em vez de tabelas?](#storing-structured-data-in-blobs) |

## <a name="scalability-targets"></a>Metas de escalabilidade

Se a sua aplicação se aproximar ou exceder qualquer um dos alvos de escalabilidade, poderá encontrar tardios de transação acrescidos ou estrangulamentos. Quando o Azure Storage acelera a sua aplicação, o serviço começa a devolver 503 códigos de erro (Servidor ocupado) ou 500 (tempo de funcionamento). Evitar estes erros mantendo-se dentro dos limites dos objetivos de escalabilidade é uma parte importante para melhorar o desempenho da sua aplicação.

Para obter mais informações sobre os objetivos de escalabilidade para o serviço de mesa, consulte os objetivos de [escalabilidade e desempenho para o armazenamento](scalability-targets.md)de mesas .

### <a name="maximum-number-of-storage-accounts"></a>Número máximo de contas de armazenamento

Se se aproximar do número máximo de contas de armazenamento permitidas para uma determinada combinação de subscrição/região, está a usar várias contas de armazenamento para aumentar a entrada, a saída, as operações de I/S por segundo (IOPS) ou a capacidade? Neste cenário, a Microsoft recomenda que aproveite os limites acrescidos para as contas de armazenamento para reduzir o número de contas de armazenamento necessárias para a sua carga de trabalho, se possível. Contacte o [Suporte Azure](https://azure.microsoft.com/support/options/) para solicitar limites acrescidos para a sua conta de armazenamento. Para mais informações, consulte Anunciar contas de [armazenamento maiores e de maior escala.](https://azure.microsoft.com/blog/announcing-larger-higher-scale-storage-accounts/)

### <a name="capacity-and-transaction-targets"></a>Metas de capacidade e transação

Se a sua aplicação estiver a aproximar-se dos objetivos de escalabilidade para uma única conta de armazenamento, considere adotar uma das seguintes abordagens:  

- Reconsidere a carga de trabalho que faz com que a sua aplicação se aproxime ou exceda o alvo de escalabilidade. Pode desenhá-lo de forma diferente para usar menos largura de banda ou capacidade, ou menos transações?
- Se a sua aplicação tiver de exceder um dos alvos de escalabilidade, crie várias contas de armazenamento e partifique os seus dados de aplicação através dessas várias contas de armazenamento. Se utilizar este padrão, certifique-se de que projeta a sua aplicação para que possa adicionar mais contas de armazenamento no futuro para equilibrar a carga. As próprias contas de armazenamento não têm qualquer custo que não seja a sua utilização em termos de dados armazenados, transações efetuadas ou dados transferidos.
- Se a sua aplicação estiver a aproximar-se dos alvos da largura de banda, considere comprimir os dados do lado do cliente para reduzir a largura de banda necessária para enviar os dados para o Armazenamento Azure.
    Embora os dados comprimidos possam poupar a largura de banda e melhorar o desempenho da rede, também pode ter efeitos negativos no desempenho. Avaliar o impacto do desempenho dos requisitos adicionais de processamento para a compressão de dados e descompressão do lado do cliente. Tenha em mente que armazenar dados comprimidos pode dificultar a resolução de problemas porque pode ser mais desafiante ver os dados usando ferramentas padrão.
- Se a sua aplicação estiver a aproximar-se dos alvos de escalabilidade, certifique-se de que está a utilizar um backoff exponencial para repetições. É melhor tentar evitar atingir os objetivos de escalabilidade implementando as recomendações descritas neste artigo. No entanto, usar um backoff exponencial para repetições impedirá a sua aplicação de retentar rapidamente, o que pode piorar o estrangulamento. Para mais informações, consulte a secção intitulada [Timeout e Server Busy errors](#timeout-and-server-busy-errors).

### <a name="targets-for-data-operations"></a>Metas para operações de dados

A carga de armazenamento azure equilibra à medida que o tráfego para a sua conta de armazenamento aumenta, mas se o tráfego apresentar rajadas repentinas, poderá não conseguir obter imediatamente este volume de entrada. Espere ver estrangulamentos e/ou intervalos durante a explosão, uma vez que o Armazenamento Azure carrega automaticamente os saldos da sua tabela. O aumento lenta geralmente proporciona melhores resultados, uma vez que o sistema tem tempo para carregar o equilíbrio adequadamente.

#### <a name="entities-per-second-storage-account"></a>Entidades por segundo (conta de armazenamento)

O limite de escalabilidade para aceder às tabelas é de até 20.000 entidades (1 KB cada) por segundo para uma conta. Em geral, cada entidade inserida, atualizada, eliminada ou digitalizada conta para este alvo. Assim, um encaixe de lote que contém 100 entidades contaria como 100 entidades. Uma consulta que digitaliza 1000 entidades e devolve 5 contaria como 1000 entidades.

#### <a name="entities-per-second-partition"></a>Entidades por segundo (partição)

Dentro de uma única divisória, o objetivo de escalabilidade para aceder às tabelas é de 2.000 entidades (1 KB cada) por segundo, utilizando a mesma contagem descrita na secção anterior.

## <a name="networking"></a>Redes

Os constrangimentos físicos da rede da aplicação podem ter um impacto significativo no desempenho. As seguintes secções descrevem algumas das limitações que os utilizadores podem encontrar.  

### <a name="client-network-capability"></a>Capacidade de rede de clientes

A largura de banda e a qualidade da ligação de rede desempenham papéis importantes no desempenho da aplicação, conforme descrito nas seguintes secções.

#### <a name="throughput"></a>Débito

Para a largura de banda, o problema é muitas vezes as capacidades do cliente. As instâncias azure maiores têm NICs com maior capacidade, por isso deve considerar a utilização de uma instância maior ou mais VMs se precisar de limites de rede mais elevados a partir de uma única máquina. Se estiver a aceder ao Azure Storage a partir de uma aplicação no local, então aplica-se a mesma regra: compreender as capacidades de rede do dispositivo cliente e a conectividade da rede com a localização do Armazenamento Azure e melhorá-las conforme necessário ou projetar a sua aplicação para trabalhar dentro das suas capacidades.

#### <a name="link-quality"></a>Qualidade de ligação

Como em qualquer utilização da rede, lembre-se que as condições de rede que resultam em erros e perda de pacotes irão retardar a entrada eficaz.  A utilização da WireShark ou da NetMon pode ajudar a diagnosticar este problema.  

### <a name="location"></a>Localização

Em qualquer ambiente distribuído, colocar o cliente perto do servidor proporciona o melhor desempenho. Para aceder ao Azure Storage com a latência mais baixa, a melhor localização para o seu cliente encontra-se dentro da mesma região azure. Por exemplo, se tiver uma aplicação web Azure que utiliza o Armazenamento Azure, localize-as ambas numa única região, como o Us West ou a Ásia Southeast. A co-localização de recursos reduz a latência e o custo, uma vez que a utilização da largura de banda numa única região é gratuita.  

Se as aplicações dos clientes acederem ao Armazenamento Azure, mas não estiverem alojadas no Azure, como aplicações de dispositivos móveis ou em serviços empresariais no local, a localização da conta de armazenamento numa região próxima desses clientes pode reduzir a latência. Se os seus clientes forem amplamente distribuídos (por exemplo, alguns na América do Norte, e alguns na Europa), então considere usar uma conta de armazenamento por região. Esta abordagem é mais fácil de implementar se os dados que as lojas de aplicação são específicos dos utilizadores individuais, e não requer a replicação de dados entre contas de armazenamento.

## <a name="sas-and-cors"></a>SAS e CORS

Suponha que precisa autorizar códigos como o JavaScript que está a ser gerido no navegador web de um utilizador ou numa aplicação para telemóvel para aceder a dados no Armazenamento Do Azure. Uma abordagem é construir uma aplicação de serviço que atue como um representante. O dispositivo do utilizador autentica-se com o serviço, que por sua vez autoriza o acesso aos recursos de Armazenamento Azure. Desta forma, pode evitar expor as chaves da sua conta de armazenamento em dispositivos inseguros. No entanto, esta abordagem coloca uma sobrecarga significativa na aplicação de serviço, uma vez que todos os dados transferidos entre o dispositivo do utilizador e o Armazenamento Azure devem passar pela aplicação de serviço.

Pode evitar utilizar uma aplicação de serviço como procuração para o Armazenamento Azure utilizando assinaturas de acesso partilhado (SAS). Utilizando o SAS, pode ativar o dispositivo do utilizador para fazer pedidos diretamente ao Armazenamento Azure, utilizando um sinal de acesso limitado. Por exemplo, se um utilizador quiser enviar uma fotografia para a sua aplicação, então a sua aplicação de serviço pode gerar um SAS e enviá-la para o dispositivo do utilizador. O token SAS pode conceder permissão para escrever a um recurso de armazenamento Azure por um intervalo de tempo especificado, após o qual expira o token SAS. Para obter mais informações sobre o SAS, consulte Grant acesso limitado aos recursos de [Armazenamento Azure utilizando assinaturas de acesso partilhado (SAS)](../common/storage-sas-overview.md).  

Normalmente, um navegador web não permitirá o JavaScript numa página que é hospedada por um site num domínio para realizar determinadas operações, como operações de escrita, para outro domínio. Conhecida como a política de origem mesma, esta política impede que um script malicioso numa página obtenha acesso a dados noutra página web. No entanto, a política de origem pode ser uma limitação na construção de uma solução na nuvem. A partilha de recursos de origem cruzada (CORS) é uma funcionalidade de navegador que permite ao domínio alvo comunicar ao navegador que confia em pedidos originários do domínio de origem.

Por exemplo, suponha que uma aplicação web em funcionamento em Azure faz um pedido de um recurso para uma conta de Armazenamento Azure. A aplicação web é o domínio de origem, e a conta de armazenamento é o domínio alvo. Pode configurar o CORS para qualquer um dos serviços de Armazenamento Azure para comunicar ao navegador web que os pedidos do domínio de origem são confiáveis pelo Armazenamento Azure. Para obter mais informações sobre o CORS, consulte o suporte de [partilha de recursos de origem cruzada (CORS) para o Armazenamento Azure](/rest/api/storageservices/Cross-Origin-Resource-Sharing--CORS--Support-for-the-Azure-Storage-Services).  
  
Tanto o SAS como o CORS podem ajudá-lo a evitar cargas desnecessárias na sua aplicação web.  

## <a name="batch-transactions"></a>Transações de lotes

O serviço de Mesa suporta transações de lotes em entidades que estão na mesma mesa e pertencem ao mesmo grupo de partição. Para mais informações, consulte as transações do [grupo de entidades Perform.](/rest/api/storageservices/performing-entity-group-transactions)

## <a name="net-configuration"></a>configuração .NET

Se utilizar a .NET Framework, esta secção lista várias configurações de configuração rápida que pode utilizar para fazer melhorias significativas de desempenho.  Se utilizar outras línguas, verifique se conceitos semelhantes se aplicam na sua língua escolhida.  

### <a name="use-net-core"></a>Use .NET Core

Desenvolva as suas aplicações de Armazenamento Azure com .NET Core 2.1 ou mais tarde para tirar partido das melhorias de desempenho. Recomenda-se a utilização do Núcleo 3.x .NET 3.x quando possível.

Para obter mais informações sobre melhorias de desempenho em .NET Core, consulte as seguintes publicações de blog:

- [Melhorias de desempenho em .NET Core 3.0](https://devblogs.microsoft.com/dotnet/performance-improvements-in-net-core-3-0/)
- [Melhorias de desempenho em .NET Core 2.1](https://devblogs.microsoft.com/dotnet/performance-improvements-in-net-core-2-1/)

### <a name="increase-default-connection-limit"></a>Aumentar o limite de ligação por defeito

Em .NET, o seguinte código aumenta o limite de ligação padrão (que normalmente é 2 em ambiente de cliente ou 10 em ambiente de servidor) para 100. Normalmente, deve definir o valor para aproximadamente o número de fios utilizados pela sua aplicação.  

```csharp
ServicePointManager.DefaultConnectionLimit = 100; //(Or More)  
```

Detete o limite de ligação antes de abrir quaisquer ligações.  

Para outras linguagens de programação, consulte a documentação dessa linguagem para determinar como definir o limite de ligação.  

Para mais informações, consulte o blog post [Web Services: Conexões Simultâneas](https://blogs.msdn.microsoft.com/darrenj/2005/03/07/web-services-concurrent-connections/).  

### <a name="increase-minimum-number-of-threads"></a>Aumentar o número mínimo de fios

Se estiver a utilizar chamadas sincronizadas juntamente com tarefas assíncronas, pode querer aumentar o número de fios na piscina de fios:

```csharp
ThreadPool.SetMinThreads(100,100); //(Determine the right number for your application)  
```

Para mais informações, consulte o método [ThreadPool.SetMinThreads.](/dotnet/api/system.threading.threadpool.setminthreads)  

## <a name="unbounded-parallelism"></a>Paralelismo ilimitado

Embora o paralelismo possa ser ótimo para o desempenho, tenha cuidado com o uso do paralelismo ilimitado, o que significa que não há limite imposto sobre o número de fios ou pedidos paralelos. Certifique-se de limitar pedidos paralelos para fazer upload ou descarregamento de dados, para aceder a várias divisórias na mesma conta de armazenamento ou para aceder a vários itens na mesma divisória. Se o paralelismo não estiver limitado, a sua aplicação pode exceder as capacidades do dispositivo cliente ou os alvos de escalabilidade da conta de armazenamento, resultando em tardios mais longos e estrangulamentos.  

## <a name="client-libraries-and-tools"></a>Bibliotecas e ferramentas de clientes

Para um melhor desempenho, utilize sempre as mais recentes bibliotecas e ferramentas de clientes fornecidas pela Microsoft. As bibliotecas de clientes do Azure Storage estão disponíveis para uma variedade de idiomas. O Azure Storage também suporta a PowerShell e o Azure CLI. A Microsoft desenvolve ativamente estas bibliotecas e ferramentas de clientes com desempenho em mente, mantém-nas atualizadas com as versões de serviço mais recentes, e garante que lidam com muitas das práticas de desempenho comprovadas internamente. Para mais informações, consulte a documentação de referência do [Armazenamento Azure.](/azure/storage/#reference)

## <a name="handle-service-errors"></a>Lidar com erros de serviço

O Armazenamento Azure devolve um erro quando o serviço não pode processar um pedido. Compreender os erros que podem ser devolvidos pelo Azure Storage num determinado cenário é útil para otimizar o desempenho.

### <a name="timeout-and-server-busy-errors"></a>Timeout e Servidor Erros ocupados

O Armazenamento Azure pode acelerar a sua aplicação se se aproximar dos limites de escalabilidade. Em alguns casos, o Armazenamento Azure pode não conseguir lidar com um pedido devido a alguma condição transitória. Em ambos os casos, o serviço pode devolver um erro de 503 (Server Busy) ou 500 (Timeout). Estes erros também podem ocorrer se o serviço estiver a reequilibrar as divisórias de dados para permitir uma maior entrada. A aplicação do cliente deve normalmente voltar a tentar a operação que causa um destes erros. No entanto, se o Armazenamento Azure estiver a estrangular a sua aplicação porque está a exceder os objetivos de escalabilidade, ou mesmo se o serviço não foi capaz de servir o pedido por outra razão, as tentativas agressivas podem piorar o problema. Recomenda-se a utilização de uma política exponencial de back off de retry, e as bibliotecas dos clientes não se adenam a este comportamento. Por exemplo, a sua aplicação pode voltar a tentar após 2 segundos, depois 4 segundos, depois 10 segundos, depois 30 segundos, e depois desistir completamente. Desta forma, a sua aplicação reduz significativamente a sua carga no serviço, em vez de exacerbar comportamentos que podem levar a estrangulamentos.  

Os erros de conectividade podem ser reexperimentados imediatamente, porque não são o resultado da aceleração e espera-se que sejam transitórios.  

### <a name="non-retryable-errors"></a>Erros não retáveis

As bibliotecas dos clientes lidam com repetições com uma consciência de quais os erros que podem ser novamente julgados e que não podem. No entanto, se estiver a chamar diretamente a API de Rest de Armazenamento Azure, existem alguns erros que não deve voltar a tentar. Por exemplo, um erro de 400 (Pedido De Mau Pedido) indica que a aplicação do cliente enviou um pedido que não podia ser processado por não estar na forma esperada. A reenvio deste pedido resulta sempre na mesma resposta, pelo que não faz sentido reejá-lo. Se estiver a ligar diretamente para a API de Depósito Azure, esteja ciente de potenciais erros e se devem ser novamente julgados.

Para obter mais informações sobre os códigos de erro do Armazenamento Azure, consulte [códigos de estado e erro](/rest/api/storageservices/status-and-error-codes2).

## <a name="configuration"></a>Configuração

Esta secção lista várias configurações de configuração rápida que pode utilizar para fazer melhorias significativas de desempenho no serviço Tabela:

### <a name="use-json"></a>Use JSON

Começando com a versão do serviço de armazenamento 2013-08-15, o serviço Table suporta a utilização de JSON em vez do formato AtomPub baseado em XML para a transferência de dados de tabela. A utilização do JSON pode reduzir os tamanhos de carga útil em até 75% e pode melhorar significativamente o desempenho da sua aplicação.

Para mais informações, consulte as [tabelas post Microsoft Azure: Introdução do Formato JSON](https://blogs.msdn.com/b/windowsazurestorage/archive/2013/12/05/windows-azure-tables-introducing-json.aspx) e [da Carga Útil para Operações](https://msdn.microsoft.com/library/azure/dn535600.aspx)de Serviço de Mesa .

### <a name="disable-nagle"></a>Desativar Nagle

O algoritmo de Nagle é amplamente implementado através das redes TCP/IP como um meio para melhorar o desempenho da rede. No entanto, não é o ideal em todas as circunstâncias (como ambientes altamente interativos). O algoritmo de Nagle tem um impacto negativo no desempenho dos pedidos para o serviço De Mesa Azure, e deve desativá-lo se possível.

## <a name="schema"></a>Esquema

A forma como representa e consulta os seus dados é o maior fator único que afeta o desempenho do serviço Tabela. Embora cada aplicação seja diferente, esta secção descreve algumas práticas comprovadas gerais que se relacionam com:

- Design de mesa
- Consultas eficientes
- Atualizações eficientes de dados

### <a name="tables-and-partitions"></a>Mesas e divisórias

As mesas dividem-se em divisórias. Todas as entidades armazenadas numa divisória partilham a mesma chave de partição e têm uma chave de linha única para identificá-la dentro dessa divisória. As divisórias proporcionam benefícios, mas também introduzem limites de escalabilidade.

- Benefícios: Pode atualizar as entidades na mesma partilha numa única transação de lote, atómica, que contém até 100 operações de armazenamento separadas (limite de 4 MB de tamanho total). Assumindo o mesmo número de entidades a serem recuperadas, também pode consultar dados dentro de uma única divisória de forma mais eficiente do que os dados que abrangem divisórias (embora lidos para mais recomendações sobre dados de tabelas de consulta).
- Limite de escalabilidade: O acesso a entidades armazenadas numa única divisória não pode ser equilibrado em carga porque as divisórias suportam transações atómicas de lotes. Por esta razão, o objetivo de escalabilidade para uma divisória individual é inferior ao do serviço de Mesa no seu conjunto.

Devido a estas características das tabelas e divisórias, deve adotar os seguintes princípios de conceção:

- Localize dados que a sua aplicação cliente atualiza ou questiona frequentemente na mesma unidade lógica de trabalho na mesma divisória. Por exemplo, localize dados na mesma divisória se a sua aplicação estiver a agregar as escritas ou se estiver a realizar operações de lote atómico. Além disso, os dados de uma única divisória podem ser mais eficientemente consultados numa única consulta do que dados em divisórias.
- Localize os dados que a sua aplicação cliente não insere, atualize ou consulta na mesma unidade lógica de trabalho (isto é, numa única consulta ou atualização de lote) em divisórias separadas. Tenha em mente que não há limite para o número de chaves de partição numa única mesa, pelo que ter milhões de chaves de partição não é um problema e não terá impacto no desempenho. Por exemplo, se a sua aplicação for um site popular com login do utilizador, usar o ID do utilizador como chave de partição pode ser uma boa escolha.

#### <a name="hot-partitions"></a>Divisórias quentes

Uma partição quente é uma que está a receber uma percentagem desproporcional do tráfego para uma conta, e não pode ser equilibrada por ser uma única divisória. Em geral, as divisórias quentes são criadas de duas formas:

#### <a name="append-only-and-prepend-only-patterns"></a>Apêndice Apenas e Prepend Apenas padrões

O padrão "Apêndice Apenas" é aquele em que todo (ou quase todo) do tráfego para uma determinada chave de partição aumenta e diminui de acordo com o tempo atual. Por exemplo, suponha que a sua aplicação utiliza a data atual como chave de partição para dados de registo. Este design resulta em todas as inserções que vão para a última partição da sua tabela, e o sistema não consegue carregar o equilíbrio corretamente. Se o volume de tráfego para essa divisória exceder o objetivo de escalabilidade ao nível da divisória, então resultará em estrangulamento. É melhor garantir que o tráfego é enviado para várias divisórias, para permitir o equilíbrio de carga dos pedidos em toda a sua mesa.

#### <a name="high-traffic-data"></a>Dados de tráfego elevado

Se o seu esquema de partição resultar numa única partição que apenas tenha dados muito mais utilizados do que outras divisórias, também pode ver estrangulamento à medida que essa divisória se aproxima do alvo de escalabilidade para uma única divisória. É melhor certificar-se de que o seu esquema de partição resulta em nenhuma divisão que se aproxime dos alvos de escalabilidade.

### <a name="querying"></a>A consultar

Esta secção descreve práticas comprovadas para consulta do serviço de Mesa.

#### <a name="query-scope"></a>Âmbito de consulta

Existem várias formas de especificar o leque de entidades para consultar. A lista seguinte descreve cada opção para consulta.

- Consultas de **ponto:**- Uma consulta de ponto recupera exatamente uma entidade especificando tanto a chave de partição como a chave de linha da entidade para recuperar. Estas consultas são eficientes, e deve usá-las sempre que possível.
- **Consultas de partição:** Uma consulta de partição é uma consulta que recupera um conjunto de dados que partilha uma chave de partição comum. Tipicamente, a consulta especifica uma gama de valores-chave de linha ou uma gama de valores para alguma propriedade de entidade, além de uma chave de partição. Estas consultas são menos eficientes do que consultas de pontos, e devem ser usadas com moderação.
- **Consultas de mesa:** Uma consulta de mesa é uma consulta que recupera um conjunto de entidades que não partilham uma chave de partição comum. Estas consultas não são eficientes e deve evitá-las se possível.

Em geral, evite exames (consultas maiores do que uma única entidade), mas se tiver de digitalizar, tente organizar os seus dados para que os seus exames recuperem os dados de que necessita sem digitalizar ou devolver quantidades significativas de entidades de que não precisa.

#### <a name="query-density"></a>Densidade de consulta

Outro fator chave na eficiência da consulta é o número de entidades devolvidas em comparação com o número de entidades digitalizadas para encontrar o conjunto devolvido. Se a sua aplicação realizar uma consulta de tabela com um filtro para um valor de propriedade que apenas 1% das ações de dados, a consulta irá digitalizar 100 entidades por cada entidade que devolve. Os alvos de escalabilidade de tabela seletivas anteriormente discutidos dizem respeito ao número de entidades digitalizadas, e não ao número de entidades devolvidas: uma baixa densidade de consulta pode facilmente fazer com que o serviço de Mesa acelere a sua aplicação porque deve digitalizar tantas entidades para recuperar a entidade que procura. Para obter mais informações sobre como evitar o estrangulamento, consulte a secção intitulada [Desnormalização](#denormalization).

#### <a name="limiting-the-amount-of-data-returned"></a>Limitando a quantidade de dados devolvidos

Quando souber que uma consulta devolverá as entidades de que não precisa na aplicação do cliente, considere usar um filtro para reduzir o tamanho do conjunto devolvido. Embora as entidades não devolvidas ao cliente ainda contem para os limites de escalabilidade, o desempenho da sua aplicação melhorará devido à redução do tamanho da carga útil da rede e ao reduzido número de entidades que a sua aplicação cliente deve processar. Tenha em mente que os alvos de escalabilidade dizem respeito ao número de entidades digitalizadas, pelo que uma consulta que filtra muitas entidades pode ainda resultar em estrangulamento, mesmo que poucas entidades sejam devolvidas. Para obter mais informações sobre como tornar as consultas eficientes, consulte a secção intitulada [Densidade de Consulta](#query-density).

Se a sua aplicação de cliente necessitar apenas de um conjunto limitado de propriedades das entidades da sua tabela, pode utilizar a projeção para limitar o tamanho do conjunto de dados devolvido. Tal como na filtragem, a projeção ajuda a reduzir a carga de rede e o processamento do cliente.

#### <a name="denormalization"></a>Desnormalização

Ao contrário de trabalhar com bases de dados relacionais, as práticas comprovadas para consulta eficiente dos dados da tabela levam à desnormalização dos seus dados. Ou seja, duplicar os mesmos dados em várias entidades (uma para cada chave que pode utilizar para encontrar os dados) para minimizar o número de entidades que uma consulta deve digitalizar para encontrar os dados de que o cliente precisa, em vez de ter de digitalizar um grande número de entidades para encontrar os dados que a sua aplicação necessita. Por exemplo, num site de e-commerce, pode querer encontrar uma encomenda tanto pelo ID do cliente (dê-me as encomendas deste cliente) e até à data (dê-me encomendas numa data). No Armazenamento de Mesa, o melhor é armazenar a entidade (ou uma referência a ela) duas vezes – uma vez com Nome de Mesa, PK e RK para facilitar a procura pelo ID do cliente, uma vez para facilitar a sua encontrá-la até à data.  

### <a name="insert-update-and-delete"></a>Inserir, atualizar e eliminar

Esta secção descreve práticas comprovadas para modificar entidades armazenadas no serviço Mesa.  

#### <a name="batching"></a>Lotes

As transações de lotes são conhecidas como transações de grupo de entidades no Armazenamento Azure. Todas as operações no âmbito de uma transação de grupo de entidades devem estar numa única divisória numa única tabela. Sempre que possível, utilize as transações do grupo de entidades para efetuar inserções, atualizações e eliminações em lotes. A utilização de transações de grupos de entidades reduz o número de viagens de ida e volta da sua aplicação cliente para o servidor, reduz o número de transações faturadas (uma transação de grupo de entidades conta como uma única transação para efeitos de faturação e pode conter até 100 operações de armazenamento), e permite atualizações atómicas (todas as operações têm sucesso ou todas falham dentro de uma transação de grupo de entidades). Ambientes com altas lupas, como dispositivos móveis, beneficiarão muito da utilização de transações de grupos de entidades.  

#### <a name="upsert"></a>Upsert

Utilize as operações **upsert** tabela sempre que possível. Existem dois tipos de **Upsert,** ambos mais eficientes do que as operações tradicionais de **inserção** e **atualização:**  

- **InsertOrMerge**: Utilize esta operação quando pretender fazer o upload de um subconjunto das propriedades da entidade, mas não tem a certeza se a entidade já existe. Se a entidade existir, esta chamada atualiza os imóveis incluídos na operação **Upsert,** e deixa todos os imóveis existentes tal como estão, se a entidade não existir, insere a nova entidade. Isto é semelhante ao uso da projeção numa consulta, na medida em que só precisa carregar as propriedades que estão a mudar.
- **InsertOrSubstitua**: Utilize esta operação quando pretender carregar uma entidade inteiramente nova, mas não tem a certeza se já existe. Utilize esta operação quando souber que a entidade recém-carregada está inteiramente correta porque substitui completamente a antiga entidade. Por exemplo, pretende atualizar a entidade que armazena a localização atual de um utilizador, independentemente de a aplicação ter ou não armazenado previamente dados de localização para o utilizador; a nova entidade de localização está completa, e não precisa de nenhuma informação de nenhuma entidade anterior.

#### <a name="storing-data-series-in-a-single-entity"></a>Armazenar séries de dados numa única entidade

Por vezes, uma aplicação armazena uma série de dados que frequentemente precisa para recuperar tudo de uma vez: por exemplo, uma aplicação pode rastrear o uso do CPU ao longo do tempo, de modo a traçar um gráfico de rolo dos dados das últimas 24 horas. Uma abordagem é ter uma entidade de mesa por hora, com cada entidade representando uma hora específica e armazenando o uso do CPU para essa hora. Para traçar estes dados, a aplicação precisa de recuperar as entidades que detêm os dados das 24 horas mais recentes.  

Em alternativa, a sua aplicação poderia armazenar o uso do CPU por cada hora como uma propriedade separada de uma única entidade: para atualizar a cada hora, a sua aplicação pode utilizar uma única chamada **InsertOrMerge Upsert** para atualizar o valor para a hora mais recente. Para traçar os dados, a aplicação só precisa de recuperar uma única entidade em vez de 24, tornando-se uma consulta eficiente. Para obter mais informações sobre a eficiência da consulta, consulte a secção intitulada [Âmbito de consulta).](#query-scope)

#### <a name="storing-structured-data-in-blobs"></a>Armazenar dados estruturados em bolhas

Se estiver a realizar inserções de lote e, em seguida, a recuperar gamas de entidades em conjunto, considere usar bolhas em vez de tabelas. Um bom exemplo é um ficheiro de registo. Pode emulhe vários minutos de troncos e insira-os e, em seguida, recupere vários minutos de troncos de cada vez. Neste caso, o desempenho é melhor se utilizar bolhas em vez de mesas, uma vez que pode reduzir significativamente o número de objetos escritos ou lidos, e também possivelmente o número de pedidos que precisam de ser feitos.  

## <a name="next-steps"></a>Passos seguintes

- [Metas de escalabilidade e desempenho para armazenamento de mesa](scalability-targets.md)
- [Metas de escalabilidade e desempenho para contas de armazenamento padrão](../common/scalability-targets-standard-account.md?toc=%2fazure%2fstorage%2ftables%2ftoc.json)
- [Códigos de estado e erro](/rest/api/storageservices/Status-and-Error-Codes2)
