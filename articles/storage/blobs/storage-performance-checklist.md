---
title: Lista de verificação de desempenho e escalabilidade para armazenamento blob - Azure Storage
description: Uma lista de práticas comprovadas para utilização com armazenamento Blob no desenvolvimento de aplicações de alto desempenho.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 10/10/2019
ms.author: tamram
ms.subservice: blobs
ms.custom: devx-track-csharp
ms.openlocfilehash: 3a3395873d7655118e3fcc9c36cdfc3855f8f000
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91714816"
---
# <a name="performance-and-scalability-checklist-for-blob-storage"></a>Lista de verificação de desempenho e escalabilidade para armazenamento blob

A Microsoft desenvolveu uma série de práticas comprovadas para desenvolver aplicações de alto desempenho com armazenamento Blob. Esta lista de verificação identifica práticas-chave que os desenvolvedores podem seguir para otimizar o desempenho. Tenha em mente estas práticas enquanto está a desenhar a sua aplicação e durante todo o processo.

O Azure Storage tem metas de escalabilidade e desempenho para capacidade, taxa de transação e largura de banda. Para obter mais informações sobre os objetivos de escalabilidade do Armazenamento Azure, consulte [os objetivos de escalabilidade e desempenho para contas de armazenamento padrão](../common/scalability-targets-standard-account.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) e [metas de escalabilidade e desempenho para armazenamento blob](scalability-targets.md).

## <a name="checklist"></a>Lista de Verificação

Este artigo organiza práticas comprovadas para o desempenho numa lista de verificação que pode seguir enquanto desenvolve a sua aplicação de armazenamento Blob.

| Concluído | Categoria | Consideração de design |
| --- | --- | --- |
| &nbsp; |Metas de escalabilidade |[Pode conceber a sua aplicação para utilizar não mais do que o número máximo de contas de armazenamento?](#maximum-number-of-storage-accounts) |
| &nbsp; |Metas de escalabilidade |[Está a evitar aproximar-se dos limites de capacidade e transação?](#capacity-and-transaction-targets) |
| &nbsp; |Metas de escalabilidade |[Um grande número de clientes está a aceder a uma única bolha simultaneamente?](#multiple-clients-accessing-a-single-blob-concurrently) |
| &nbsp; |Metas de escalabilidade |[A sua aplicação está dentro dos alvos de escalabilidade para uma única bolha?](#bandwidth-and-operations-per-blob) |
| &nbsp; |Criação de partições |[A sua convenção de nomeação foi concebida para permitir um melhor equilíbrio de cargas?](#partitioning) |
| &nbsp; |Redes |[Os dispositivos do lado do cliente têm largura de banda suficientemente alta e baixa latência para alcançar o desempenho necessário?](#throughput) |
| &nbsp; |Redes |[Os dispositivos do lado do cliente têm uma ligação de rede de alta qualidade?](#link-quality) |
| &nbsp; |Redes |[O pedido do cliente é na mesma região que a conta de armazenamento?](#location) |
| &nbsp; |Acesso direto ao cliente |[Está a utilizar assinaturas de acesso partilhado (SAS) e partilha de recursos de origem cruzada (CORS) para permitir o acesso direto ao Azure Storage?](#sas-and-cors) |
| &nbsp; |Colocação em cache |[A sua aplicação é de caching dados que são frequentemente acedidos e raramente alterados?](#reading-data) |
| &nbsp; |Colocação em cache |[A sua aplicação está a fazer atualizações de loteamento, caching-las no cliente e, em seguida, a carregá-las em conjuntos maiores?](#uploading-data-in-batches) |
| &nbsp; |Configuração .NET |[Está a utilizar .NET Core 2.1 ou mais tarde para um desempenho ótimo?](#use-net-core) |
| &nbsp; |Configuração .NET |[Configurado o seu cliente para utilizar um número suficiente de ligações simultâneas?](#increase-default-connection-limit) |
| &nbsp; |Configuração .NET |[Para aplicações .NET, configuraste .NET para utilizar um número suficiente de fios?](#increase-minimum-number-of-threads) |
| &nbsp; |Paralelismo |[Assegurou-se de que o paralelismo está devidamente limitado para não sobrecarregar as capacidades do seu cliente ou aproximar-se dos alvos de escalabilidade?](#unbounded-parallelism) |
| &nbsp; |Ferramentas |[Está a utilizar as versões mais recentes das bibliotecas e ferramentas fornecidas pela Microsoft?](#client-libraries-and-tools) |
| &nbsp; |Tentativas |[Está a usar uma política de repetição com um recuo exponencial para erros de estrangulamento e intervalos?](#timeout-and-server-busy-errors) |
| &nbsp; |Tentativas |[A sua aplicação evita recaída por erros não retáveis?](#non-retryable-errors) |
| &nbsp; |Bolhas de cópia |[Estás a copiar bolhas da maneira mais eficiente?](#blob-copy-apis) |
| &nbsp; |Bolhas de cópia |[Está a utilizar a versão mais recente do AzCopy para operações de cópia a granel?](#use-azcopy) |
| &nbsp; |Bolhas de cópia |[Está a utilizar a família Azure Data Box para importar grandes volumes de dados?](#use-azure-data-box) |
| &nbsp; |Distribuição de conteúdos |[Está a usar um CDN para distribuição de conteúdos?](#content-distribution) |
| &nbsp; |Utilizar metadados |[Está a armazenar metadados frequentemente usados sobre bolhas nos seus metadados?](#use-metadata) |
| &nbsp; |Upload rápido |[Quando se tenta carregar uma bolha rapidamente, está a carregar blocos em paralelo?](#upload-one-large-blob-quickly) |
| &nbsp; |Upload rápido |[Quando tentas carregar muitas bolhas rapidamente, estás a carregar bolhas em paralelo?](#upload-many-blobs-quickly) |
| &nbsp; |Tipo de blob |[Está a usar bolhas de página ou bolhas de bloqueio quando apropriado?](#choose-the-correct-type-of-blob) |

## <a name="scalability-targets"></a>Metas de escalabilidade

Se a sua aplicação se aproximar ou exceder qualquer um dos alvos de escalabilidade, poderá encontrar atrasos de transação aumentadas ou estrangulamentos. Quando o Azure Storage acelera a sua aplicação, o serviço começa a devolver códigos de erro 503 (Server busy) ou 500 (Tempo limite de funcionamento). Evitar estes erros mantendo-se dentro dos limites dos objetivos de escalabilidade é uma parte importante para melhorar o desempenho da sua aplicação.

Para obter mais informações sobre os alvos de escalabilidade para o serviço de fila, consulte a [escalabilidade do armazenamento Azure e os objetivos de desempenho](/azure/storage/queues/scalability-targets#scale-targets-for-queue-storage).

### <a name="maximum-number-of-storage-accounts"></a>Número máximo de contas de armazenamento

Se estiver a aproximar-se do número máximo de contas de armazenamento permitidas para uma determinada combinação subscrição/região, avalie o seu cenário e determine se alguma das seguintes condições se aplica:

- Está a utilizar contas de armazenamento para armazenar discos não geridos e adicionar esses discos às suas máquinas virtuais (VMs)? Para este cenário, a Microsoft recomenda a utilização de discos geridos. Os discos geridos escalam para si automaticamente e sem a necessidade de criar e gerir contas de armazenamento individuais. Para mais informações, consulte [discos geridos introdução ao Azure](../../virtual-machines/managed-disks-overview.md)
- Está a utilizar uma conta de armazenamento por cliente, para efeitos de isolamento de dados? Para este cenário, a Microsoft recomenda a utilização de um recipiente blob para cada cliente, em vez de uma conta de armazenamento inteira. O Azure Storage permite-lhe agora atribuir funções Azure por cada contentor. Para obter mais informações, consulte [o portal Azure para atribuir uma função Azure para acesso a dados de bolhas e filas](../common/storage-auth-aad-rbac-portal.md).
- Está a utilizar várias contas de armazenamento para aumentar as operações de entrada, saída, E/S por segundo (IOPS) ou capacidade? Neste cenário, a Microsoft recomenda que aproveite os limites acrescidos para as contas de armazenamento para reduzir o número de contas de armazenamento necessárias para a sua carga de trabalho, se possível. Contacte [o Suporte Azure](https://azure.microsoft.com/support/options/) para solicitar limites acrescidos para a sua conta de armazenamento. Para obter mais informações, consulte [anunciar contas de armazenamento de maior escala.](https://azure.microsoft.com/blog/announcing-larger-higher-scale-storage-accounts/)

### <a name="capacity-and-transaction-targets"></a>Objetivos de capacidade e transações

Se a sua aplicação estiver a aproximar-se dos objetivos de escalabilidade de uma única conta de armazenamento, considere a adoção de uma das seguintes abordagens:  

- Se a sua aplicação atingir o objetivo de transação, considere a utilização de contas de armazenamento de blob de bloco, que são otimizadas para altas taxas de transação e baixa e consistente latência. Para mais informações, veja [Visão geral de conta de armazenamento do Azure](../common/storage-account-overview.md).
- Reconsidere a carga de trabalho que faz com que a sua aplicação se aproxime ou exceda o objetivo de escalabilidade. Pode desenhá-lo de forma diferente para utilizar menos largura de banda ou capacidade, ou menos transações?
- Se a sua aplicação tiver de exceder um dos objetivos de escalabilidade, em seguida, crie várias contas de armazenamento e partition os seus dados de aplicação através dessas contas de armazenamento múltiplas. Se utilizar este padrão, então certifique-se de conceber a sua aplicação para que possa adicionar mais contas de armazenamento no futuro para equilibrar a carga. As próprias contas de armazenamento não têm qualquer custo que não seja a sua utilização em termos de dados armazenados, transações efe feitas ou dados transferidos.
- Se a sua aplicação estiver a aproximar-se dos alvos de largura de banda, considere comprimir dados do lado do cliente para reduzir a largura de banda necessária para enviar os dados para o Azure Storage.
    Embora a compressão de dados possa salvar a largura de banda e melhorar o desempenho da rede, também pode ter efeitos negativos no desempenho. Avaliar o impacto de desempenho dos requisitos adicionais de processamento para a compressão de dados e descompressão do lado do cliente. Tenha em mente que armazenar dados comprimidos pode dificultar a resolução de problemas, pois pode ser mais desafiante visualizar os dados usando ferramentas padrão.
- Se a sua aplicação estiver a aproximar-se dos alvos de escalabilidade, certifique-se de que está a utilizar um backoff exponencial para retrações. É melhor tentar evitar atingir os objetivos de escalabilidade implementando as recomendações descritas neste artigo. No entanto, a utilização de um recuo exponencial para as retreições impedirá que a sua aplicação volte rapidamente, o que pode piorar a aceleração. Para obter mais informações, consulte a secção intitulada [Timeout e Server Busy errors](#timeout-and-server-busy-errors).

### <a name="multiple-clients-accessing-a-single-blob-concurrently"></a>Vários clientes acedem a uma única bolha simultaneamente

Se tiver um grande número de clientes a aceder a uma única bolha simultaneamente, terá de considerar tanto por blob como por objetivo de escalabilidade da conta de armazenamento. O número exato de clientes que podem aceder a uma única bolha variará consoante o número de clientes que solicitam a bolha simultaneamente, o tamanho do blob e as condições de rede.

Se a bolha pode ser distribuída através de um CDN, como imagens ou vídeos servidos a partir de um website, então pode utilizar um CDN. Para mais informações, consulte a secção intitulada [Distribuição de Conteúdo.](#content-distribution)

Noutros cenários, como simulações científicas em que os dados são confidenciais, tem duas opções. A primeira é escalonar o acesso da sua carga de trabalho de modo a que a bolha seja acedida durante um período de tempo vs a ser acedido simultaneamente. Em alternativa, pode copiar temporariamente a bolha para várias contas de armazenamento para aumentar o total de IOPS por blob e através das contas de armazenamento. Os resultados variam dependendo do comportamento da sua aplicação, por isso certifique-se de testar padrões de concordância durante o design.

### <a name="bandwidth-and-operations-per-blob"></a>Largura de banda e operações por bolha

Uma única bolha suporta até 500 pedidos por segundo. Se tiver vários clientes que precisam de ler a mesma bolha e pode exceder este limite, então considere usar uma conta de armazenamento de blob bloco. Uma conta de armazenamento de blob de bloco fornece uma taxa de pedido mais alta, ou operações de E/S por segundo (IOPS).

Também pode utilizar uma rede de entrega de conteúdos (CDN) como a Azure CDN para distribuir operações na bolha. Para obter mais informações sobre a Azure CDN, consulte [a visão geral do Azure CDN](../../cdn/cdn-overview.md).  

## <a name="partitioning"></a>Criação de partições

Compreender como as divisórias de armazenamento Azure os seus dados blob são úteis para melhorar o desempenho. O Azure Storage pode servir dados numa única partição mais rapidamente do que os dados que abrangem várias divisórias. Ao nomear as suas bolhas adequadamente, pode melhorar a eficiência dos pedidos de leitura.

O armazenamento de bolhas utiliza um esquema de divisórias baseado na gama para o equilíbrio de escalonamento e carga. Cada bolha tem uma chave de partição composta pelo nome blob completo (conta+container+blob). A chave de partição é usada para dividir dados blob em intervalos. As gamas são então equilibradas em carga através do armazenamento Blob.

A partição baseada em gama significa que as convenções de nomeação que utilizam encomendas lexical (por exemplo, *mypayroll,* *myperformance,* *myemployees,* etc.) ou timetamps *(log20160101*, *log20160102*, *log20160102,* etc.) são mais propensos a que as divisórias sejam co-localizadas no mesmo servidor de divisórias. , até que o aumento da carga exija que sejam divididos em faixas mais pequenas. A co-localização de bolhas no mesmo servidor de partição melhora o desempenho, por isso uma parte importante do melhoramento de desempenho envolve nomear bolhas de uma forma que as organize de forma mais eficaz.

Por exemplo, todas as bolhas dentro de um recipiente podem ser servidas por um único servidor até que a carga nestas bolhas exija um reequilíbrio adicional das gamas de partição. Da mesma forma, um grupo de contas levemente carregadas com os seus nomes dispostos por ordem lexical pode ser servido por um único servidor até que a carga numa ou em todas estas contas exija que sejam divididas em vários servidores de partição.

Cada operação de equilíbrio de carga pode ter impacto na latência das chamadas de armazenamento durante a operação. A capacidade do serviço de lidar com uma súbita explosão de tráfego para uma divisória é limitada pela escalabilidade de um único servidor de partição até que a operação de equilíbrio de carga entre e reequilibra a gama de chaves de partição.

Pode seguir algumas boas práticas para reduzir a frequência de tais operações.  

- Se possível, utilize tamanhos de bolha ou bloco superiores a 4 MiB para contas de armazenamento padrão e superiores a 256 KiB para contas de armazenamento premium. Os tamanhos de bolhas maiores ou blocos ativam automaticamente bolhas de blocos de alta potência. As bolhas de bloco de alta produção fornecem ingestão de alto desempenho que não é afetada pelo nome de partição.
- Examine a convenção de nomeação que utiliza para contas, contentores, bolhas, mesas e filas. Considere a prefixagem de nomes de conta, recipiente ou bolha com um haxixe de três dígitos usando uma função de hashing que melhor se adapte às suas necessidades.
- Se organizar os seus dados utilizando cartões temporais ou identificadores numéricos, certifique-se de que não está a utilizar um padrão de tráfego apenas (ou apenas pré-retenção). Estes padrões não são adequados para um sistema de divisórias baseado em gama. Estes padrões podem levar a que todo o tráfego vá para uma única divisória e limite o sistema de um equilíbrio eficaz da carga.

    Por exemplo, se tiver operações diárias que utilizem uma bolha com um timetamp como *o yyymmdd*, todo o tráfego para essa operação diária é direcionado para uma única bolha, que é servida por um único servidor de partição. Considere se os limites por blob e os limites por partição satisfazem as suas necessidades, e considere quebrar esta operação em várias bolhas, se necessário. Da mesma forma, se armazenar dados de séries de tempo nas suas tabelas, todo o tráfego pode ser direcionado para a última parte do espaço de nomes chave. Se estiver a utilizar IDs numéricos, prefixe o ID com um haxixe de três dígitos. Se estiver a utilizar os timetamps, prefixe o tempotando com o valor dos segundos, por exemplo, *ssyyymmd .* Se a sua aplicação realizar rotineiramente operações de listagem e consulta, escolha uma função de hashing que limitará o seu número de consultas. Em alguns casos, um prefixo aleatório pode ser suficiente.
  
- Para obter mais informações sobre o esquema de partição utilizado no Azure Storage, consulte [o Azure Storage: Um serviço de armazenamento em nuvem altamente disponível com forte consistência](https://sigops.org/sosp/sosp11/current/2011-Cascais/printable/11-calder.pdf).

## <a name="networking"></a>Redes

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

Para uma ampla distribuição de conteúdo blob, utilize uma rede de entrega de conteúdos como a Azure CDN. Para obter mais informações sobre a Azure CDN, consulte [a Azure CDN](../../cdn/cdn-overview.md).  

## <a name="sas-and-cors"></a>SAS e CORS

Suponha que precisa de autorizar códigos como o JavaScript que está a ser executada no navegador web de um utilizador ou numa aplicação de telemóvel para aceder a dados no Azure Storage. Uma abordagem é construir uma aplicação de serviço que atue como um representante. O dispositivo do utilizador autentica-se com o serviço, que por sua vez autoriza o acesso aos recursos de Armazenamento Azure. Desta forma, pode evitar expor as chaves da sua conta de armazenamento em dispositivos inseguros. No entanto, esta abordagem coloca uma sobrecarga significativa na aplicação de serviço, uma vez que todos os dados transferidos entre o dispositivo do utilizador e o Azure Storage devem passar pela aplicação de serviço.

Pode evitar a utilização de uma aplicação de serviço como proxy para o Azure Storage utilizando assinaturas de acesso partilhado (SAS). Utilizando o SAS, pode ativar o dispositivo do seu utilizador a estojos diretamente para o Azure Storage utilizando um token de acesso limitado. Por exemplo, se um utilizador quiser enviar uma fotografia para a sua aplicação, então a sua aplicação de serviço pode gerar um SAS e enviá-la para o dispositivo do utilizador. O token SAS pode conceder permissão para escrever a um recurso de armazenamento Azure por um intervalo de tempo especificado, após o qual o token SAS expira. Para obter mais informações sobre o SAS, consulte [Grant acesso limitado aos recursos de armazenamento Azure utilizando assinaturas de acesso partilhado (SAS)](../common/storage-sas-overview.md).  

Normalmente, um navegador web não permitirá que o JavaScript numa página que é hospedada por um site num domínio realize determinadas operações, como operações de escrita, para outro domínio. Conhecida como a mesma política de origem, esta política impede que um script malicioso numa página obtenha acesso a dados em outra página web. No entanto, a política de mesma origem pode ser uma limitação quando se constrói uma solução na nuvem. A partilha de recursos de origem cruzada (CORS) é uma funcionalidade de navegador que permite ao domínio alvo comunicar ao navegador que confia em pedidos originários do domínio de origem.

Por exemplo, suponha que uma aplicação web em execução em Azure faça um pedido de recurso para uma conta de Armazenamento Azure. A aplicação web é o domínio de origem, e a conta de armazenamento é o domínio alvo. Pode configurar o CORS para que qualquer um dos serviços de Armazenamento Azure comunique ao navegador web que os pedidos do domínio de origem são fidedignos pelo Azure Storage. Para obter mais informações sobre o CORS, consulte [o suporte de partilha de recursos cross-origin (CORS) para o Armazenamento Azure](/rest/api/storageservices/Cross-Origin-Resource-Sharing--CORS--Support-for-the-Azure-Storage-Services).  
  
Tanto o SAS como o CORS podem ajudá-lo a evitar cargas desnecessárias na sua aplicação web.  

## <a name="caching"></a>Colocação em cache

Caching desempenha um papel importante no desempenho. As seguintes secções discutem as melhores práticas.

### <a name="reading-data"></a>Ler os dados

Em geral, ler dados uma vez é preferível lê-lo duas vezes. Considere o exemplo de uma aplicação web que recuperou uma bolha de 50 MiB do Azure Storage para servir de conteúdo a um utilizador. Idealmente, a aplicação caches a bolha localmente para o disco e, em seguida, recupera a versão em cache para pedidos subsequentes do utilizador.

Uma forma de evitar recuperar uma bolha se não tiver sido modificada desde que foi em cache é qualificar a operação GET com um cabeçalho condicional para o tempo de modificação. Se o último tempo modificado for após o tempo em que a bolha foi em cache, então a bolha é recuperada e re-em cache. Caso contrário, a bolha em cache é recuperada para um desempenho ótimo.

Também pode decidir conceber a sua aplicação para assumir que a bolha permanece inalterada por um curto período de tempo após a sua recuperação. Neste caso, o pedido não necessita de verificar se a bolha foi modificada durante esse intervalo.

Os dados de configuração, os dados de procura e outros dados que são frequentemente utilizados pela aplicação são bons candidatos para o caching.  

Para obter mais informações sobre a utilização de cabeçalhos condicional, consulte [os cabeçalhos condicional para as operações de serviço blob](/rest/api/storageservices/specifying-conditional-headers-for-blob-service-operations).  

### <a name="uploading-data-in-batches"></a>Upload de dados em lotes

Em alguns cenários, pode agregar dados localmente e, em seguida, enviá-los periodicamente em um lote em vez de carregar cada pedaço de dados imediatamente. Por exemplo, suponha que uma aplicação web mantém um arquivo de atividades. A aplicação pode ou carregar detalhes de todas as atividades como acontece com uma tabela (que requer muitas operações de armazenamento), ou pode guardar detalhes de atividade para um arquivo de log local e, em seguida, periodicamente carregar todos os detalhes da atividade como um ficheiro delimitado para uma bolha. Se cada entrada de registo tiver um tamanho de 1 KB, pode carregar milhares de entradas numa única transação. Uma única transação suporta o upload de uma bolha de até 64 MiB em tamanho. O desenvolvedor de aplicações deve projetar para a possibilidade de dispositivo cliente ou falhas de upload. Se os dados de atividade precisarem de ser descarregados por um intervalo de tempo e não para uma única atividade, então é recomendado o armazenamento blob sobre o armazenamento de mesa.

## <a name="net-configuration"></a>Configuração .NET

Se utilizar o Quadro .NET, esta secção lista várias definições de configuração rápidas que pode utilizar para fazer melhorias significativas no desempenho.  Se utilizar outras línguas, verifique se conceitos semelhantes se aplicam na sua língua escolhida.  

### <a name="use-net-core"></a>Use .NET Core

Desenvolva as suas aplicações de Armazenamento Azure com .NET Core 2.1 ou posteriormente para tirar partido das melhorias de desempenho. A utilização de .NET Core 3.x é recomendada quando possível.

Para obter mais informações sobre melhorias de desempenho em .NET Core, consulte as seguintes publicações de blog:

- [Melhorias de desempenho em .NET Core 3.0](https://devblogs.microsoft.com/dotnet/performance-improvements-in-net-core-3-0/)
- [Melhorias de desempenho em .NET Core 2.1](https://devblogs.microsoft.com/dotnet/performance-improvements-in-net-core-2-1/)

### <a name="increase-default-connection-limit"></a>Aumentar o limite de ligação padrão

Em .NET, o seguinte código aumenta o limite de ligação predefinido (que normalmente é dois num ambiente de cliente ou dez num ambiente de servidor) para 100. Normalmente, deve definir o valor para aproximadamente o número de fios utilizados pela sua aplicação. Desa estada o limite de ligação antes de abrir quaisquer ligações.

```csharp
ServicePointManager.DefaultConnectionLimit = 100; //(Or More)  
```

Para outras linguagens de programação, consulte a documentação para determinar como definir o limite de ligação.  

Para mais informações, consulte o blog post [Web Services: Concurrent Connections](https://blogs.msdn.microsoft.com/darrenj/2005/03/07/web-services-concurrent-connections/).  

### <a name="increase-minimum-number-of-threads"></a>Aumentar o número mínimo de fios

Se estiver a utilizar chamadas sincronizadas juntamente com tarefas assíncronos, é melhor aumentar o número de fios na piscina de roscas:

```csharp
ThreadPool.SetMinThreads(100,100); //(Determine the right number for your application)  
```

Para obter mais informações, consulte o método [ThreadPool.SetMinThreads.](/dotnet/api/system.threading.threadpool.setminthreads)  

## <a name="unbounded-parallelism"></a>Paralelismo ilimitado

Embora o paralelismo possa ser ótimo para o desempenho, tenha cuidado com a utilização de paralelismo ilimitado, o que significa que não há limite imposto sobre o número de fios ou pedidos paralelos. Certifique-se de limitar os pedidos paralelos para carregar ou descarregar dados, aceder a várias divisórias na mesma conta de armazenamento ou aceder a vários itens na mesma partição. Se o paralelismo não for limitado, a sua aplicação pode exceder as capacidades do dispositivo cliente ou os alvos de escalabilidade da conta de armazenamento, resultando em atrasos e estrangulamentos mais longos.  

## <a name="client-libraries-and-tools"></a>Bibliotecas e ferramentas do cliente

Para um melhor desempenho, utilize sempre as mais recentes bibliotecas e ferramentas de clientes fornecidas pela Microsoft. As bibliotecas de clientes do Azure Storage estão disponíveis para uma variedade de idiomas. O Azure Storage também suporta o PowerShell e o Azure CLI. A Microsoft desenvolve ativamente estas bibliotecas e ferramentas de clientes com o desempenho em mente, mantém-nas atualizadas com as versões de serviço mais recentes, e garante que eles lidam com muitas das práticas de desempenho comprovadas internamente. Para mais informações, consulte a [documentação de referência do Azure Storage](/azure/storage/#reference).

## <a name="handle-service-errors"></a>Lidar com erros de serviço

O Azure Storage retorna um erro quando o serviço não pode processar um pedido. Compreender os erros que podem ser devolvidos pelo Azure Storage num dado cenário é útil para otimizar o desempenho.

### <a name="timeout-and-server-busy-errors"></a>Erros de timeout e servidor ocupado

O Azure Storage pode acelerar a sua aplicação se se aproximar dos limites de escalabilidade. Em alguns casos, o Azure Storage pode não conseguir lidar com um pedido devido a alguma condição transitória. Em ambos os casos, o serviço pode devolver um erro de 503 (Server Busy) ou 500 (Timeout). Estes erros também podem ocorrer se o serviço estiver a reequilibtar as divisórias de dados para permitir uma maior produção. A aplicação do cliente deve normalmente voltar a tentar a operação que causa um destes erros. No entanto, se o Azure Storage está a estrangular a sua aplicação por estar a exceder os alvos de escalabilidade, ou mesmo que o serviço não tenha conseguido servir o pedido por outra razão, as retrações agressivas podem piorar o problema. Recomenda-se a utilização de uma política de retrocesso exponencial e as bibliotecas do cliente estão em incumprimento deste comportamento. Por exemplo, a sua aplicação pode voltar a tentar após 2 segundos, depois 4 segundos, depois 10 segundos, depois 30 segundos, e depois desistir completamente. Desta forma, a sua aplicação reduz significativamente a sua carga no serviço, em vez de exacerbar comportamentos que podem levar a estrangulamentos.  

Os erros de conectividade podem ser novamente julgados imediatamente, porque não são o resultado de estrangulamento e espera-se que sejam transitórios.  

### <a name="non-retryable-errors"></a>Erros não retáveis

As bibliotecas do cliente lidam com retreiças com consciência de quais os erros que podem ser novamente julgados e que não podem. No entanto, se estiver a ligar diretamente para a API do Azure Storage REST, existem alguns erros que não deve voltar a tentar. Por exemplo, um erro de 400 (Mau Pedido) indica que o pedido do cliente enviou um pedido que não pôde ser processado porque não estava na forma esperada. Reencando este pedido resulta sempre na mesma resposta, pelo que não faz sentido voltar a tentar. Se estiver a ligar diretamente para a AZure Storage REST API, esteja ciente de potenciais erros e se devem ser novamente julgados.

Para obter mais informações sobre os códigos de erro do Azure Storage, consulte [códigos de estado e de erro](/rest/api/storageservices/status-and-error-codes2).

## <a name="copying-and-moving-blobs"></a>Bolhas de cópia e de movimento

O Azure Storage fornece uma série de soluções para copiar e mover bolhas dentro de uma conta de armazenamento, entre contas de armazenamento e entre sistemas no local e a nuvem. Esta secção descreve algumas destas opções em termos dos seus efeitos no desempenho. Para obter informações sobre a transferência eficiente de dados para ou a partir do armazenamento blob, consulte [Escolha uma solução Azure para transferência de dados.](../common/storage-choose-data-transfer-solution.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

### <a name="blob-copy-apis"></a>APIs de cópia blob

Para copiar bolhas nas contas de armazenamento, utilize a operação [Put Block From URL.](/rest/api/storageservices/put-block-from-url) Esta operação copia os dados sincronizadamente de qualquer fonte de URL numa bolha de bloco. A utilização da `Put Block from URL` operação pode reduzir significativamente a largura de banda necessária quando estiver a migrar dados através de contas de armazenamento. Como a operação de cópia ocorre no lado do serviço, não é necessário fazer o download e re-carregar os dados.

Para copiar dados dentro da mesma conta de armazenamento, utilize a operação [Copy Blob.](/rest/api/storageservices/Copy-Blob) A cópia dos dados dentro da mesma conta de armazenamento é normalmente concluída rapidamente.  

### <a name="use-azcopy"></a>Utilizar o AZCopy

O utilitário de linha de comando AzCopy é uma opção simples e eficiente para a transferência a granel de blobs para, a partir e através de contas de armazenamento. O AzCopy está otimizado para este cenário, e pode alcançar altas taxas de transferência. A versão 10 da AzCopy utiliza a `Put Block From URL` operação para copiar dados blob através de contas de armazenamento. Para obter mais informações, consulte [copiar ou mover dados para o Azure Storage utilizando a AzCopy v10](/azure/storage/common/storage-use-azcopy-v10).  

### <a name="use-azure-data-box"></a>Use a caixa de dados Azure

Para importar grandes volumes de dados para o armazenamento blob, considere usar a família Azure Data Box para transferências offline. Os dispositivos Data Box fornecidos pela Microsoft são uma boa escolha para mover grandes quantidades de dados para o Azure quando se está limitado pelo tempo, disponibilidade da rede ou custos. Para mais informações, consulte a [Documentação Azure DataBox](/azure/databox/).

## <a name="content-distribution"></a>Distribuição de conteúdos

Por vezes, uma aplicação precisa de servir o mesmo conteúdo a muitos utilizadores (por exemplo, um vídeo de demonstração de produto usado na página inicial de um website), localizado nas mesmas regiões ou em várias regiões. Neste cenário, utilize uma Rede de Entrega de Conteúdos (CDN) como a Azure CDN para distribuir geograficamente o conteúdo blob. Ao contrário de uma conta de Armazenamento Azure que existe numa única região e que não pode fornecer conteúdo com baixa latência a outras regiões, a Azure CDN utiliza servidores em vários centros de dados em todo o mundo. Além disso, um CDN pode normalmente suportar limites de saída muito mais elevados do que uma única conta de armazenamento.  

Para obter mais informações sobre a Azure CDN, consulte [a Azure CDN](../../cdn/cdn-overview.md).

## <a name="use-metadata"></a>Utilizar metadados

O serviço Blob suporta pedidos HEAD, que podem incluir propriedades blob ou metadados. Por exemplo, se a sua aplicação necessitar dos dados exif (formato de imagem trocavel) a partir de uma foto, pode recuperar a foto e extraí-la. Para poupar largura de banda e melhorar o desempenho, a sua aplicação pode armazenar os dados Exif nos metadados da bolha quando a aplicação faz o upload da fotografia. Em seguida, pode recuperar os dados Exif em metadados utilizando apenas um pedido HEAD. A recuperação de apenas metadados e não o conteúdo completo da bolha poupa largura de banda significativa e reduz o tempo de processamento necessário para extrair os dados do Exif. Tenha em mente que 8 KiB de metadados podem ser armazenados por bolha.  

## <a name="upload-blobs-quickly"></a>Carregar bolhas rapidamente

Para carregar as bolhas rapidamente, primeiro determine se vai carregar uma bolha ou muitas. Utilize a orientação abaixo para determinar o método correto para utilizar dependendo do seu cenário.  

### <a name="upload-one-large-blob-quickly"></a>Carre faça upload de uma bolha grande rapidamente

Para carregar rapidamente uma única bolha grande, uma aplicação do cliente pode carregar os seus blocos ou páginas em paralelo, estando atento aos alvos de escalabilidade para as bolhas individuais e a conta de armazenamento como um todo. As bibliotecas de clientes Azure Storage suportam o upload em paralelo. Por exemplo, pode utilizar as seguintes propriedades para especificar o número de pedidos simultâneos permitidos em .NET ou Java. As bibliotecas de clientes para outras línguas suportadas oferecem opções semelhantes.

- Para .NET, descreva a propriedade [BlobRequestOptions.ParallelOperationThreadCount.](/dotnet/api/microsoft.azure.storage.blob.blobrequestoptions.paralleloperationthreadcount)
- Para Java/Android, ligue para o método [BlobRequestOptions.setConcurrentRequestCount (último Integer concurrentRequestCount).](/java/api/com.microsoft.azure.storage.blob.blobrequestoptions.setconcurrentrequestcount)

### <a name="upload-many-blobs-quickly"></a>Carre faça upload de muitas bolhas rapidamente

Para carregar muitas bolhas rapidamente, carrele as bolhas em paralelo. O upload em paralelo é mais rápido do que carregar bolhas únicas de cada vez com uploads de blocos paralelos porque espalha o upload através de várias divisórias do serviço de armazenamento. A AzCopy executa uploads em paralelo por padrão, e é recomendado para este cenário. Para mais informações, consulte [Começar com a AzCopy.](../common/storage-use-azcopy-v10.md)  

## <a name="choose-the-correct-type-of-blob"></a>Escolha o tipo correto de bolha

O Azure Storage suporta bolhas de blocos, bolhas de apêndice e bolhas de página. Para um determinado cenário de utilização, a sua escolha do tipo blob irá afetar o desempenho e a escalabilidade da sua solução.

As bolhas de bloco são apropriadas quando pretende carregar grandes quantidades de dados de forma eficiente. Por exemplo, uma aplicação de cliente que faça upload de fotos ou vídeos para o armazenamento blob teria como alvo blobs de bloqueio.

As bolhas de apêndice são semelhantes às bolhas de bloqueio, na medida em que são compostas por blocos. Quando modificar uma bolha de apêndice, os blocos são adicionados apenas à extremidade da bolha. As bolhas de apêndice são úteis para cenários como o registo, quando uma aplicação precisa adicionar dados a uma bolha existente.

As bolhas de página são apropriadas se a aplicação precisar de efetuar gravações aleatórias nos dados. Por exemplo, os discos de máquina virtual Azure são armazenados como bolhas de página. Para obter mais informações, consulte [as bolhas de blocos de compreensão, as bolhas de apêndice e as bolhas de página](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs).  

## <a name="next-steps"></a>Passos seguintes

- [Metas de escalabilidade e desempenho para armazenamento blob](scalability-targets.md)
- [Metas de escalabilidade e desempenho para contas de armazenamento padrão](../common/scalability-targets-standard-account.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
- [Códigos de estado e de erro](/rest/api/storageservices/Status-and-Error-Codes2)
