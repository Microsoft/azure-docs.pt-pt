---
title: Lista de verificação de desempenho e escalabilidade para armazenamento de fila - Armazenamento Azure
description: Uma lista de práticas comprovadas para utilização com armazenamento de fila no desenvolvimento de aplicações de alto desempenho.
author: tamram
services: storage
ms.author: tamram
ms.date: 10/10/2019
ms.topic: overview
ms.service: storage
ms.subservice: queues
ms.custom: devx-track-csharp
ms.openlocfilehash: 4040a81d5b509ddbdd355953e28721a7c9fccfb8
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "97585671"
---
<!-- docutune:casing "Timeout and Server Busy errors" -->

# <a name="performance-and-scalability-checklist-for-queue-storage"></a>Lista de verificação de desempenho e escalabilidade para armazenamento de filas

A Microsoft desenvolveu uma série de práticas comprovadas para desenvolver aplicações de alto desempenho com o Armazenamento de Fila. Esta lista de verificação identifica práticas-chave que os desenvolvedores podem seguir para otimizar o desempenho. Tenha em mente estas práticas enquanto está a desenhar a sua aplicação e durante todo o processo.

O Azure Storage tem metas de escalabilidade e desempenho para capacidade, taxa de transação e largura de banda. Para obter mais informações sobre os objetivos de escalabilidade do Armazenamento Azure, consulte [metas de escalabilidade e desempenho para contas de armazenamento padrão](../common/scalability-targets-standard-account.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json) e [metas de escalabilidade e desempenho para armazenamento de fila.](scalability-targets.md)

## <a name="checklist"></a>Lista de Verificação

Este artigo organiza práticas comprovadas para o desempenho numa lista de verificação que pode seguir enquanto desenvolve a sua aplicação de Armazenamento de Fila.

| Concluído | Categoria | Consideração de design |
|--|--|--|
| &nbsp; | Metas de escalabilidade | [Pode conceber a sua aplicação para utilizar não mais do que o número máximo de contas de armazenamento?](#maximum-number-of-storage-accounts) |
| &nbsp; | Metas de escalabilidade | [Está a evitar aproximar-se dos limites de capacidade e transação?](#capacity-and-transaction-targets) |
| &nbsp; | Rede | [Os dispositivos do lado do cliente têm largura de banda suficientemente alta e baixa latência para alcançar o desempenho necessário?](#throughput) |
| &nbsp; | Rede | [Os dispositivos do lado do cliente têm uma ligação de rede de alta qualidade?](#link-quality) |
| &nbsp; | Rede | [O pedido do cliente é na mesma região que a conta de armazenamento?](#location) |
| &nbsp; | Acesso direto ao cliente | [Está a utilizar assinaturas de acesso partilhado (SAS) e partilha de recursos de origem cruzada (CORS) para permitir o acesso direto ao Azure Storage?](#sas-and-cors) |
| &nbsp; | Configuração .NET | [Está a utilizar .NET Core 2.1 ou mais tarde para um desempenho ótimo?](#use-net-core) |
| &nbsp; | Configuração .NET | [Configurado o seu cliente para utilizar um número suficiente de ligações simultâneas?](#increase-default-connection-limit) |
| &nbsp; | Configuração .NET | [Para aplicações .NET, configuraste .NET para utilizar um número suficiente de fios?](#increase-the-minimum-number-of-threads) |
| &nbsp; | Paralelismo | [Assegurou-se de que o paralelismo está devidamente limitado para não sobrecarregar as capacidades do seu cliente ou aproximar-se dos alvos de escalabilidade?](#unbounded-parallelism) |
| &nbsp; | Ferramentas | [Está a utilizar as versões mais recentes das bibliotecas e ferramentas fornecidas pela Microsoft?](#client-libraries-and-tools) |
| &nbsp; | Tentativas | [Está a usar uma política de repetição com um recuo exponencial para erros de estrangulamento e intervalos?](#timeout-and-server-busy-errors) |
| &nbsp; | Tentativas | [A sua aplicação evita recaída por erros não retáveis?](#non-retryable-errors) |
| &nbsp; | Configuração | [Desligou o algoritmo do Nagle para melhorar o desempenho de pequenos pedidos?](#disable-nagles-algorithm) |
| &nbsp; | Tamanho da mensagem | [As suas mensagens são compactas para melhorar o desempenho da fila?](#message-size) |
| &nbsp; | Recuperação a granel | [Está a recuperar várias mensagens numa única operação?](#batch-retrieval) |
| &nbsp; | Frequência de sondagens | [Está a sondar com frequência para reduzir a percebido latência da sua candidatura?](#queue-polling-interval) |
| &nbsp; | Atualizar mensagem | [Está a realizar uma operação de mensagem de atualização para armazenar o progresso no processamento de mensagens, para evitar ter de reprocessar toda a mensagem se ocorrer um erro?](#perform-an-update-message-operation) |
| &nbsp; | Arquitetura | [Está a usar filas para tornar toda a sua aplicação mais escalável, mantendo cargas de trabalho de longa duração fora do caminho crítico e escala, em seguida, independentemente?](#application-architecture) |

## <a name="scalability-targets"></a>Metas de escalabilidade

Se a sua aplicação se aproximar ou exceder qualquer um dos alvos de escalabilidade, poderá encontrar atrasos de transação aumentadas ou estrangulamentos. Quando o Azure Storage acelera a sua aplicação, o serviço começa a devolver `Server Busy` códigos de erro 503 () ou 500 ( `Operation Timeout` ). Evitar estes erros mantendo-se dentro dos limites dos objetivos de escalabilidade é uma parte importante para melhorar o desempenho da sua aplicação.

Para obter mais informações sobre os alvos de escalabilidade para armazenamento de fila, consulte [a escalabilidade do armazenamento Azure e os objetivos de desempenho](./scalability-targets.md#scale-targets-for-queue-storage).

### <a name="maximum-number-of-storage-accounts"></a>Número máximo de contas de armazenamento

Se estiver a aproximar-se do número máximo de contas de armazenamento permitidas para uma determinada combinação subscrição/região, está a utilizar várias contas de armazenamento para aumentar as operações de entrada, saída, E/S por segundo (IOPS) ou capacidade? Neste cenário, a Microsoft recomenda que aproveite os limites acrescidos para as contas de armazenamento para reduzir o número de contas de armazenamento necessárias para a sua carga de trabalho, se possível. Contacte [o Suporte Azure](https://azure.microsoft.com/support/options/) para solicitar limites acrescidos para a sua conta de armazenamento. Para obter mais informações, consulte [anunciar contas de armazenamento de maior escala.](https://azure.microsoft.com/blog/announcing-larger-higher-scale-storage-accounts/)

### <a name="capacity-and-transaction-targets"></a>Objetivos de capacidade e transações

Se a sua aplicação estiver a aproximar-se dos objetivos de escalabilidade de uma única conta de armazenamento, considere a adoção de uma das seguintes abordagens:

- Se os alvos de escalabilidade das filas forem insuficientes para a sua aplicação, utilize várias filas e distribua mensagens por elas.
- Reconsidere a carga de trabalho que faz com que a sua aplicação se aproxime ou exceda o objetivo de escalabilidade. Pode desenhá-lo de forma diferente para utilizar menos largura de banda ou capacidade, ou menos transações?
- Se a sua aplicação tiver de exceder um dos objetivos de escalabilidade, em seguida, crie várias contas de armazenamento e partition os seus dados de aplicação através dessas contas de armazenamento múltiplas. Se utilizar este padrão, então certifique-se de conceber a sua aplicação para que possa adicionar mais contas de armazenamento no futuro para equilibrar a carga. As próprias contas de armazenamento não têm qualquer custo que não seja a sua utilização em termos de dados armazenados, transações efe feitas ou dados transferidos.
- Se a sua aplicação estiver a aproximar-se dos alvos de largura de banda, considere comprimir dados do lado do cliente para reduzir a largura de banda necessária para enviar os dados para o Azure Storage. Embora a compressão de dados possa salvar a largura de banda e melhorar o desempenho da rede, também pode ter efeitos negativos no desempenho. Avaliar o impacto de desempenho dos requisitos adicionais de processamento para a compressão de dados e descompressão do lado do cliente. Tenha em mente que armazenar dados comprimidos pode dificultar a resolução de problemas, pois pode ser mais desafiante visualizar os dados usando ferramentas padrão.
- Se a sua aplicação estiver a aproximar-se dos alvos de escalabilidade, certifique-se de que está a utilizar um backoff exponencial para retrações. É melhor tentar evitar atingir os objetivos de escalabilidade implementando as recomendações descritas neste artigo. No entanto, a utilização de um recuo exponencial para as retreições impedirá que a sua aplicação volte rapidamente, o que pode piorar a aceleração. Para obter mais informações, consulte a secção [de erros timeout e Server Busy.](#timeout-and-server-busy-errors)

## <a name="networking"></a>Rede

Os constrangimentos físicos da rede da aplicação podem ter um impacto significativo no desempenho. As seguintes secções descrevem algumas das limitações que os utilizadores podem encontrar.

### <a name="client-network-capability"></a>Capacidade de rede de clientes

A largura de banda e a qualidade da ligação de rede desempenham papéis importantes no desempenho da aplicação, conforme descrito nas seguintes secções.

#### <a name="throughput"></a>Débito

Para a largura de banda, o problema é muitas vezes as capacidades do cliente. Casos maiores de Azure têm NICs com maior capacidade, por isso deve considerar usar um caso maior ou mais VMs se precisar de limites de rede mais elevados a partir de uma única máquina. Se estiver a aceder ao Azure Storage a partir de uma aplicação no local, então aplica-se a mesma regra: compreenda as capacidades de rede do dispositivo cliente e a conectividade da rede com o local de armazenamento Azure e melhore-as conforme necessário ou desenhe a sua aplicação para funcionar dentro das suas capacidades.

#### <a name="link-quality"></a>Qualidade de ligação

Como em qualquer utilização da rede, tenha em mente que as condições de rede que resultam em erros e perda de pacotes retardarão a produção eficaz. A utilização de Arameshark ou Monitor de Rede pode ajudar a diagnosticar este problema.

### <a name="location"></a>Localização

Em qualquer ambiente distribuído, colocar o cliente perto do servidor oferece na melhor performance. Para aceder ao Azure Storage com a latência mais baixa, a melhor localização para o seu cliente é dentro da mesma região de Azure. Por exemplo, se tiver uma aplicação web Azure que utiliza o Azure Storage, então localize-os numa única região, como o Oeste dos EUA ou o Sudeste Asiático. A co-localização de recursos reduz a latência e o custo, uma vez que o uso de largura de banda numa única região é gratuito.

Se as aplicações do cliente acederem ao Azure Storage mas não estiverem hospedadas no Azure, como aplicações de dispositivos móveis ou serviços empresariais no local, então localizar a conta de armazenamento numa região próxima a esses clientes pode reduzir a latência. Se os seus clientes são amplamente distribuídos (por exemplo, alguns na América do Norte, e alguns na Europa), então considere usar uma conta de armazenamento por região. Esta abordagem é mais fácil de implementar se os dados que as lojas de aplicações são específicos de cada um dos utilizadores, e não requer a replicação de dados entre contas de armazenamento.

## <a name="sas-and-cors"></a>SAS e CORS

Suponha que precisa de autorizar códigos como o JavaScript que está a ser executada no navegador web de um utilizador ou numa aplicação de telemóvel para aceder a dados no Azure Storage. Uma abordagem é construir uma aplicação de serviço que atue como um representante. O dispositivo do utilizador autentica-se com o serviço, que por sua vez autoriza o acesso aos recursos de Armazenamento Azure. Desta forma, pode evitar expor as chaves da sua conta de armazenamento em dispositivos inseguros. No entanto, esta abordagem coloca uma sobrecarga significativa na aplicação de serviço, uma vez que todos os dados transferidos entre o dispositivo do utilizador e o Azure Storage devem passar pela aplicação de serviço.

Pode evitar a utilização de uma aplicação de serviço como proxy para o Azure Storage utilizando assinaturas de acesso partilhado (SAS). Utilizando o SAS, pode ativar o dispositivo do seu utilizador a estojos diretamente para o Azure Storage utilizando um token de acesso limitado. Por exemplo, se um utilizador quiser enviar uma fotografia para a sua aplicação, então a sua aplicação de serviço pode gerar um SAS e enviá-la para o dispositivo do utilizador. O token SAS pode conceder permissão para escrever a um recurso de armazenamento Azure por um intervalo de tempo especificado, após o qual o token SAS expira. Para obter mais informações sobre o SAS, consulte [Grant acesso limitado aos recursos de armazenamento Azure utilizando assinaturas de acesso partilhado (SAS)](../common/storage-sas-overview.md).

Normalmente, um navegador web não permitirá que o JavaScript numa página que é hospedada por um site num domínio realize determinadas operações, como operações de escrita, para outro domínio. Conhecida como a mesma política de origem, esta política impede que um script malicioso numa página obtenha acesso a dados em outra página web. No entanto, a política de mesma origem pode ser uma limitação quando se constrói uma solução na nuvem. A partilha de recursos de origem cruzada (CORS) é uma funcionalidade de navegador que permite ao domínio alvo comunicar ao navegador que confia em pedidos originários do domínio de origem.

Por exemplo, suponha que uma aplicação web em execução em Azure faça um pedido de recurso para uma conta de Armazenamento Azure. A aplicação web é o domínio de origem, e a conta de armazenamento é o domínio alvo. Pode configurar o CORS para que qualquer um dos serviços de Armazenamento Azure comunique ao navegador web que os pedidos do domínio de origem são fidedignos pelo Azure Storage. Para obter mais informações sobre o CORS, consulte [o suporte de partilha de recursos cross-origin (CORS) para o Armazenamento Azure](/rest/api/storageservices/Cross-Origin-Resource-Sharing--CORS--Support-for-the-Azure-Storage-Services).

Tanto o SAS como o CORS podem ajudá-lo a evitar cargas desnecessárias na sua aplicação web.

## <a name="net-configuration"></a>Configuração .NET

Se utilizar o Quadro .NET, esta secção lista várias definições de configuração rápidas que pode utilizar para fazer melhorias significativas no desempenho. Se utilizar outras línguas, verifique se conceitos semelhantes se aplicam na sua língua escolhida.

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

Para mais informações, consulte os serviços web post [blog: Conexões simultâneas](/archive/blogs/darrenj/web-services-concurrent-connections).

### <a name="increase-the-minimum-number-of-threads"></a>Aumentar o número mínimo de fios

Se estiver a utilizar chamadas sincronizadas juntamente com tarefas assíncronos, é melhor aumentar o número de fios na piscina de roscas:

```csharp
ThreadPool.SetMinThreads(100,100); //(Determine the right number for your application)  
```

Para mais informações, consulte o [`ThreadPool.SetMinThreads`](/dotnet/api/system.threading.threadpool.setminthreads) método.

## <a name="unbounded-parallelism"></a>Paralelismo ilimitado

Embora o paralelismo possa ser ótimo para o desempenho, tenha cuidado com a utilização de paralelismo ilimitado, o que significa que não há limite imposto sobre o número de fios ou pedidos paralelos. Certifique-se de limitar os pedidos paralelos para carregar ou descarregar dados, aceder a várias divisórias na mesma conta de armazenamento ou aceder a vários itens na mesma partição. Se o paralelismo não for limitado, a sua aplicação pode exceder as capacidades do dispositivo cliente ou os alvos de escalabilidade da conta de armazenamento, resultando em atrasos e estrangulamentos mais longos.

## <a name="client-libraries-and-tools"></a>Bibliotecas e ferramentas do cliente

Para um melhor desempenho, utilize sempre as mais recentes bibliotecas e ferramentas de clientes fornecidas pela Microsoft. As bibliotecas de clientes do Azure Storage estão disponíveis para uma variedade de idiomas. O Azure Storage também suporta o PowerShell e o Azure CLI. A Microsoft desenvolve ativamente estas bibliotecas e ferramentas de clientes com o desempenho em mente, mantém-nas atualizadas com as versões de serviço mais recentes, e garante que eles lidam com muitas das práticas de desempenho comprovadas internamente. Para mais informações, consulte a [documentação de referência do Azure Storage](./reference.md).

## <a name="handle-service-errors"></a>Lidar com erros de serviço

O Azure Storage retorna um erro quando o serviço não pode processar um pedido. Compreender os erros que podem ser devolvidos pelo Azure Storage num dado cenário é útil para otimizar o desempenho.

### <a name="timeout-and-server-busy-errors"></a>Erros de timeout e servidor ocupado

O Azure Storage pode acelerar a sua aplicação se se aproximar dos limites de escalabilidade. Em alguns casos, o Azure Storage pode não conseguir lidar com um pedido devido a alguma condição transitória. Em ambos os casos, o serviço pode devolver um erro de 503 ( `Server Busy` ou 500 `Timeout` ). Estes erros também podem ocorrer se o serviço estiver a reequilibtar as divisórias de dados para permitir uma maior produção. A aplicação do cliente deve normalmente voltar a tentar a operação que causa um destes erros. No entanto, se o Azure Storage está a estrangular a sua aplicação por estar a exceder os alvos de escalabilidade, ou mesmo que o serviço não tenha conseguido servir o pedido por outra razão, as retrações agressivas podem piorar o problema. Recomenda-se a utilização de uma política de retrocesso exponencial e as bibliotecas do cliente estão em incumprimento deste comportamento. Por exemplo, a sua aplicação pode voltar a tentar após 2 segundos, depois 4 segundos, depois 10 segundos, depois 30 segundos, e depois desistir completamente. Desta forma, a sua aplicação reduz significativamente a sua carga no serviço, em vez de exacerbar comportamentos que podem levar a estrangulamentos.

Os erros de conectividade podem ser novamente julgados imediatamente, porque não são o resultado de estrangulamento e espera-se que sejam transitórios.

### <a name="non-retryable-errors"></a>Erros não retáveis

As bibliotecas do cliente lidam com retreiças com consciência de quais os erros que podem ser novamente julgados e que não podem. No entanto, se estiver a ligar diretamente para a API do Azure Storage REST, existem alguns erros que não deve voltar a tentar. Por exemplo, um erro de 400 indica `Bad Request` que o pedido do cliente enviou um pedido que não pôde ser processado porque não estava na forma esperada. Reencando este pedido resulta sempre na mesma resposta, pelo que não faz sentido voltar a tentar. Se estiver a ligar diretamente para a AZure Storage REST API, esteja ciente de potenciais erros e se devem ser novamente julgados.

Para obter mais informações sobre os códigos de erro do Azure Storage, consulte [códigos de estado e de erro](/rest/api/storageservices/status-and-error-codes2).

## <a name="disable-nagles-algorithm"></a>Desativar o algoritmo de Nagle

O algoritmo de Nagle é amplamente implementado através das redes TCP/IP como forma de melhorar o desempenho da rede. No entanto, não é o ideal em todas as circunstâncias (tais como ambientes altamente interativos). O algoritmo da Nagle tem um impacto negativo no desempenho dos pedidos para o Azure Table Storage, e deve desativá-lo se possível.

## <a name="message-size"></a>Tamanho da mensagem

O desempenho da fila e a escalabilidade diminuem à medida que o tamanho da mensagem aumenta. Coloque apenas a informação de que o recetor necessita numa mensagem.

## <a name="batch-retrieval"></a>Recuperação de lotes

Pode recuperar até 32 mensagens de uma fila numa única operação. A recuperação de lotes pode reduzir o número de viagens de ida e volta da aplicação do cliente, o que é especialmente útil para ambientes, como dispositivos móveis, com alta latência.

## <a name="queue-polling-interval"></a>Intervalo de votação na fila

A maioria das aplicações pesquisa para mensagens de uma fila, que pode ser uma das maiores fontes de transações para essa aplicação. Selecione o seu intervalo de sondagens com demasiada frequência: as sondagens com demasiada frequência podem fazer com que a sua aplicação se aproxime dos alvos de escalabilidade da fila. No entanto, a 200.000 transações por $0,01 (no momento da escrita), uma sondagem de um único processador a cada segundo por mês custaria menos de 15 cêntimos, pelo que o custo não é normalmente um fator que afete a sua escolha de intervalo de votação.

Para obter informações sobre os custos atualizados, consulte [os preços de armazenamento Azure.](https://azure.microsoft.com/pricing/details/storage/)

## <a name="perform-an-update-message-operation"></a>Realizar uma operação de mensagem de atualização

Pode executar uma operação de mensagem de atualização para aumentar o tempo limite de invisibilidade ou atualizar as informações do estado de uma mensagem. Esta abordagem pode ser mais eficiente do que ter um fluxo de trabalho que passa um trabalho de uma fila para outra, à medida que cada passo do trabalho está concluído. A sua aplicação pode guardar o estado de trabalho para a mensagem e depois continuar a trabalhar, em vez de requenciar a mensagem para o próximo passo do trabalho sempre que um passo completo. Tenha em mente que cada operação de mensagem de atualização conta para o alvo de escalabilidade.

## <a name="application-architecture"></a>Arquitetura da aplicação

Use filas para tornar a sua arquitetura de aplicação escalável. As seguintes listas são algumas formas de utilizar as filas para tornar a sua aplicação mais escalável:

- Pode utilizar filas para criar atrasos de trabalho para o processamento e suavizar as cargas de trabalho na sua aplicação. Por exemplo, pode fazer fila de pedidos dos utilizadores para realizar trabalhos intensivos de processador, como redimensionar imagens carregadas.
- Pode utilizar as filas para dissociar partes da sua aplicação para que possa escaloná-las de forma independente. Por exemplo, uma extremidade frontal web poderia colocar os resultados do inquérito dos utilizadores numa fila para posterior análise e armazenamento. Pode adicionar mais instâncias de função do trabalhador para processar os dados da fila conforme necessário.

## <a name="next-steps"></a>Passos seguintes

- [Metas de escalabilidade e desempenho para armazenamento de filas](scalability-targets.md)
- [Metas de escalabilidade e desempenho para contas de armazenamento padrão](../common/scalability-targets-standard-account.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)
- [Códigos de estado e de erro](/rest/api/storageservices/Status-and-Error-Codes2)
