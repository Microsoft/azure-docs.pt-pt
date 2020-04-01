---
title: Lista de verificação de desempenho e escalabilidade para armazenamento Blob - Armazenamento Azure
description: Uma lista de práticas comprovadas para uso com armazenamento Blob no desenvolvimento de aplicações de alto desempenho.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 10/10/2019
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: b94725d4d3eb9fd6f13a39d00486b4ab085b9ef9
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/01/2020
ms.locfileid: "80473944"
---
# <a name="performance-and-scalability-checklist-for-blob-storage"></a>Lista de verificação de desempenho e escalabilidade para armazenamento blob

A Microsoft desenvolveu uma série de práticas comprovadas para desenvolver aplicações de alto desempenho com armazenamento Blob. Esta lista de verificação identifica práticas-chave que os desenvolvedores podem seguir para otimizar o desempenho. Tenha em mente estas práticas enquanto está a desenhar a sua aplicação e durante todo o processo.

O Azure Storage tem metas de escalabilidade e desempenho para capacidade, taxa de transação e largura de banda. Para obter mais informações sobre os objetivos de escalabilidade do Armazenamento Azure, consulte os objetivos de [escalabilidade e desempenho para contas de armazenamento padrão](../common/scalability-targets-standard-account.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) e metas de escalabilidade e desempenho para o armazenamento de [Blob.](scalability-targets.md)

## <a name="checklist"></a>Lista de Verificação

Este artigo organiza práticas comprovadas para o desempenho numa lista de verificação que pode seguir enquanto desenvolve a sua aplicação de armazenamento Blob.

| Concluído | Categoria | Consideração de design |
| --- | --- | --- |
| &nbsp; |Metas de escalabilidade |[Pode projetar a sua aplicação para usar não mais do que o número máximo de contas de armazenamento?](#maximum-number-of-storage-accounts) |
| &nbsp; |Metas de escalabilidade |[Está a evitar aproximar-se dos limites de capacidade e transação?](#capacity-and-transaction-targets) |
| &nbsp; |Metas de escalabilidade |[Um grande número de clientes acedem a uma única bolha simultaneamente?](#multiple-clients-accessing-a-single-blob-concurrently) |
| &nbsp; |Metas de escalabilidade |[A sua candidatura está dentro dos alvos de escalabilidade para uma única bolha?](#bandwidth-and-operations-per-blob) |
| &nbsp; |Criação de partições |[A sua convenção de nomeação foi concebida para permitir um melhor equilíbrio de carga?](#partitioning) |
| &nbsp; |Redes |[Os dispositivos do lado do cliente têm largura de banda suficientemente elevada e baixa latência para alcançar o desempenho necessário?](#throughput) |
| &nbsp; |Redes |[Os dispositivos do lado do cliente têm uma ligação de rede de alta qualidade?](#link-quality) |
| &nbsp; |Redes |[A aplicação do cliente está na mesma região que a conta de armazenamento?](#location) |
| &nbsp; |Acesso direto ao cliente |[Está a utilizar assinaturas de acesso partilhado (SAS) e partilha de recursos de origem cruzada (CORS) para permitir o acesso direto ao Armazenamento Azure?](#sas-and-cors) |
| &nbsp; |Colocação em cache |[Os dados de cache da sua aplicação são frequentemente acedidos e raramente alterados?](#reading-data) |
| &nbsp; |Colocação em cache |[A sua aplicação está a atualizar-se, colocando-as no cliente e, em seguida, enviando-as em conjuntos maiores?](#uploading-data-in-batches) |
| &nbsp; |configuração .NET |[Está a utilizar .NET Core 2.1 ou mais tarde para um desempenho ótimo?](#use-net-core) |
| &nbsp; |configuração .NET |[Já configurou o seu cliente para utilizar um número suficiente de ligações simultâneas?](#increase-default-connection-limit) |
| &nbsp; |configuração .NET |[Para aplicações .NET, configurou .NET para utilizar um número suficiente de fios?](#increase-minimum-number-of-threads) |
| &nbsp; |Paralelismo |[Assegurou que o paralelismo está devidamente limitado para que não sobrecarregue as capacidades do seu cliente ou se aproxime dos alvos de escalabilidade?](#unbounded-parallelism) |
| &nbsp; |Ferramentas |[Está a utilizar as versões mais recentes das bibliotecas e ferramentas fornecidas pela Microsoft?](#client-libraries-and-tools) |
| &nbsp; |Tentativas |[Estás a usar uma política de retry com um recuo exponencial para erros de estrangulamento e intervalos?](#timeout-and-server-busy-errors) |
| &nbsp; |Tentativas |[A sua aplicação está a evitar tentativas de erros não retáveis?](#non-retryable-errors) |
| &nbsp; |Bolhas de cópia |[Estás a copiar bolhas da maneira mais eficiente?](#blob-copy-apis) |
| &nbsp; |Bolhas de cópia |[Está a utilizar a versão mais recente do AzCopy para operações de cópia a granel?](#use-azcopy) |
| &nbsp; |Bolhas de cópia |[Está a usar a família Azure Data Box para importar grandes volumes de dados?](#use-azure-data-box) |
| &nbsp; |Distribuição de conteúdos |[Está a usar um CDN para distribuição de conteúdos?](#content-distribution) |
| &nbsp; |Usar metadados |[Está a armazenar metadados frequentemente usados sobre bolhas nos seus metadados?](#use-metadata) |
| &nbsp; |Upload rapidamente |[Ao tentar carregar uma bolha rapidamente, está a carregar blocos em paralelo?](#upload-one-large-blob-quickly) |
| &nbsp; |Upload rapidamente |[Ao tentar carregar muitas bolhas rapidamente, está a carregar bolhas paralelas?](#upload-many-blobs-quickly) |
| &nbsp; |Tipo de blob |[Está a usar bolhas de página ou blocos quando apropriado?](#choose-the-correct-type-of-blob) |

## <a name="scalability-targets"></a>Metas de escalabilidade

Se a sua aplicação se aproximar ou exceder qualquer um dos alvos de escalabilidade, poderá encontrar tardios de transação acrescidos ou estrangulamentos. Quando o Azure Storage acelera a sua aplicação, o serviço começa a devolver 503 códigos de erro (Servidor ocupado) ou 500 (tempo de funcionamento). Evitar estes erros mantendo-se dentro dos limites dos objetivos de escalabilidade é uma parte importante para melhorar o desempenho da sua aplicação.

Para obter mais informações sobre os alvos de escalabilidade para o serviço de fila, consulte [a escalabilidade do Armazenamento Azure e os alvos de desempenho](/azure/storage/queues/scalability-targets#scale-targets-for-queue-storage).

### <a name="maximum-number-of-storage-accounts"></a>Número máximo de contas de armazenamento

Se estiver a aproximar-se do número máximo de contas de armazenamento permitidas para uma determinada combinação de subscrição/região, avalie o seu cenário e determine se alguma das seguintes condições se aplicam:

- Está a usar contas de armazenamento para armazenar discos não geridos e adicionar esses discos às suas máquinas virtuais (VMs)? Para este cenário, a Microsoft recomenda a utilização de discos geridos. Escala de discos geridos para si automaticamente e sem a necessidade de criar e gerir contas individuais de armazenamento. Para mais informações, consulte [Introdução aos discos geridos pelo Azure](../../virtual-machines/windows/managed-disks-overview.md)
- Está a usar uma conta de armazenamento por cliente, para efeitos de isolamento de dados? Para este cenário, a Microsoft recomenda a utilização de um recipiente blob para cada cliente, em vez de uma conta de armazenamento inteira. O Azure Storage permite-lhe agora atribuir funções de controlo de acesso (RBAC) baseadas em funções por contentor. Para mais informações, consulte o Acesso ao Grant à blob Azure e aos dados da [fila com o RBAC no portal Azure.](../common/storage-auth-aad-rbac-portal.md)
- Está a usar várias contas de armazenamento para aumentar a entrada, a saída, as operações de I/S por segundo (IOPS) ou a capacidade? Neste cenário, a Microsoft recomenda que aproveite os limites acrescidos para as contas de armazenamento para reduzir o número de contas de armazenamento necessárias para a sua carga de trabalho, se possível. Contacte o [Suporte Azure](https://azure.microsoft.com/support/options/) para solicitar limites acrescidos para a sua conta de armazenamento. Para mais informações, consulte Anunciar contas de [armazenamento maiores e de maior escala.](https://azure.microsoft.com/blog/announcing-larger-higher-scale-storage-accounts/)

### <a name="capacity-and-transaction-targets"></a>Metas de capacidade e transação

Se a sua aplicação estiver a aproximar-se dos objetivos de escalabilidade para uma única conta de armazenamento, considere adotar uma das seguintes abordagens:  

- Se a sua aplicação atingir o alvo de transação, considere usar contas de armazenamento de blocos blob, que são otimizadas para altas taxas de transação e latência baixa e consistente. Para mais informações, veja [Visão geral de conta de armazenamento do Azure](../common/storage-account-overview.md).
- Reconsidere a carga de trabalho que faz com que a sua aplicação se aproxime ou exceda o alvo de escalabilidade. Pode desenhá-lo de forma diferente para usar menos largura de banda ou capacidade, ou menos transações?
- Se a sua aplicação tiver de exceder um dos alvos de escalabilidade, crie várias contas de armazenamento e partifique os seus dados de aplicação através dessas várias contas de armazenamento. Se utilizar este padrão, certifique-se de que projeta a sua aplicação para que possa adicionar mais contas de armazenamento no futuro para equilibrar a carga. As próprias contas de armazenamento não têm qualquer custo que não seja a sua utilização em termos de dados armazenados, transações efetuadas ou dados transferidos.
- Se a sua aplicação estiver a aproximar-se dos alvos da largura de banda, considere comprimir os dados do lado do cliente para reduzir a largura de banda necessária para enviar os dados para o Armazenamento Azure.
    Embora os dados comprimidos possam poupar a largura de banda e melhorar o desempenho da rede, também pode ter efeitos negativos no desempenho. Avaliar o impacto do desempenho dos requisitos adicionais de processamento para a compressão de dados e descompressão do lado do cliente. Tenha em mente que armazenar dados comprimidos pode dificultar a resolução de problemas porque pode ser mais desafiante ver os dados usando ferramentas padrão.
- Se a sua aplicação estiver a aproximar-se dos alvos de escalabilidade, certifique-se de que está a utilizar um backoff exponencial para repetições. É melhor tentar evitar atingir os objetivos de escalabilidade implementando as recomendações descritas neste artigo. No entanto, usar um backoff exponencial para repetições impedirá a sua aplicação de retentar rapidamente, o que pode piorar o estrangulamento. Para mais informações, consulte a secção intitulada [Timeout e Server Busy errors](#timeout-and-server-busy-errors).

### <a name="multiple-clients-accessing-a-single-blob-concurrently"></a>Vários clientes que acedem a uma única bolha simultaneamente

Se tiver um grande número de clientes a aceder a uma única bolha em simultâneo, terá de considerar tanto por blob como por cada armazenamento alvos de escalabilidade da conta de armazenamento. O número exato de clientes que podem aceder a uma única bolha variará consoante fatores como o número de clientes que solicitam a bolha simultaneamente, o tamanho da bolha e as condições de rede.

Se a bolha puder ser distribuída através de um CDN, como imagens ou vídeos servidos a partir de um website, então pode utilizar um CDN. Para mais informações, consulte a secção intitulada Distribuição de [Conteúdos](#content-distribution).

Noutros cenários, como simulações científicas onde os dados são confidenciais, tem duas opções. A primeira é escalonar o acesso da sua carga de trabalho de modo a que a bolha seja acedida durante um período de tempo vs sendo acedida simultaneamente. Em alternativa, pode copiar temporariamente a bolha para várias contas de armazenamento para aumentar o total de IOPS por bolha e através de contas de armazenamento. Os resultados variarão dependendo do comportamento da sua aplicação, por isso certifique-se de testar padrões de concurrency durante o design.

### <a name="bandwidth-and-operations-per-blob"></a>Largura de banda e operações por bolha

Uma única bolha suporta até 500 pedidos por segundo. Se tiver vários clientes que precisam de ler a mesma bolha e pode exceder este limite, então considere usar uma conta de armazenamento de blocos. Uma conta de armazenamento de blocos blob fornece uma taxa de pedido mais elevada, ou operações de I/O por segundo (IOPS).

Também pode utilizar uma rede de entrega de conteúdos (CDN) como o Azure CDN para distribuir operações na bolha. Para mais informações sobre o Azure CDN, consulte a [visão geral do Azure CDN](../../cdn/cdn-overview.md).  

## <a name="partitioning"></a>Criação de partições

Compreender como as divisórias de Armazenamento Azure os seus dados blob são úteis para melhorar o desempenho. O Armazenamento Azure pode servir dados numa única divisória mais rapidamente do que dados que abrangem várias divisórias. Ao nomear as suas bolhas adequadamente, pode melhorar a eficiência dos pedidos de leitura.

O armazenamento de blob utiliza um esquema de partição baseado em gama para escalonamento e equilíbrio de carga. Cada bolha tem uma chave de partição composta pelo nome completo blob (account+container+blob). A chave de partição é usada para dividir os dados blob em intervalos. As gamas são então equilibradas em carga através do armazenamento Blob.

A partilha baseada no alcance significa que as convenções de nomeação que utilizam encomendas lexical (por exemplo, *mypayroll*, myperformance , *myemployees*, *myemployees*, etc.) ou carimbos temporais *(log20160101*, *log20160102*, *log20160102*, etc.) são mais propensos a resultar na co-localização das divisórias no mesmo servidor de partição. , até que a carga aumentada exija que sejam divididas em gamas mais pequenas. Co-localizar bolhas no mesmo servidor de partição melhora o desempenho, por isso uma parte importante da melhoria do desempenho envolve nomear bolhas de uma forma que as organize de forma mais eficaz.

Por exemplo, todas as bolhas dentro de um recipiente podem ser servidas por um único servidor até que a carga nestas bolhas exija um reequilíbrio adicional das gamas de divisórias. Da mesma forma, um grupo de contas levemente carregadas com os seus nomes dispostos por ordem lexical pode ser servido por um único servidor até que a carga em uma ou todas estas contas exija que sejam divididas em vários servidores de partição.

Cada operação de equilíbrio de carga pode afetar a latência das chamadas de armazenamento durante a operação. A capacidade do serviço para lidar com uma súbita explosão de tráfego para uma divisória é limitada pela escalabilidade de um único servidor de partição até que a operação de equilíbrio de carga saqueie e reequilibre o intervalo da chave da divisória.

Pode seguir algumas boas práticas para reduzir a frequência de tais operações.  

- Se possível, utilize tamanhos de blocos ou blocos superiores a 4 MiB para contas de armazenamento padrão e superior a 256 KiB para contas de armazenamento premium. Os tamanhos de blocos ou blocos maiores ativam automaticamente bolhas de bloco de alta potência. As bolhas de bloco de alta produção fornecem uma ingestão de alto desempenho que não é afetada pelo nomeação da partição.
- Examine a convenção de nomeação que utiliza para contas, contentores, bolhas, mesas e filas. Considere pré-fixação de nomes de conta, contentor ou blob com um haxixe de três dígitos usando uma função de haxixe que melhor se adapte às suas necessidades.
- Se organizar os seus dados utilizando carimbos de tempo ou identificadores numéricos, certifique-se de que não está a utilizar um padrão de tráfego apenas anexado (ou apenas para preparação). Estes padrões não são adequados para um sistema de partição baseado em alcance. Estes padrões podem levar a que todo o tráfego vá para uma única divisória e limite o sistema de um equilíbrio eficaz de carga.

    Por exemplo, se tiver operações diárias que utilizem uma bolha com um carimbo temporal como *o yyymmdd,* então todo o tráfego para essa operação diária é direcionado para uma única bolha, que é servida por um único servidor de partição. Considere se os limites per-blob e os limites por partição satisfazem as suas necessidades, e considere quebrar esta operação em várias bolhas, se necessário. Da mesma forma, se armazenar dados da série de tempo nas suas tabelas, todo o tráfego pode ser direcionado para a última parte do espaço de nome-nome chave. Se estiver a usar identificações numéricas, prefixe a identificação com um hash de três dígitos. Se estiver a utilizar carimbos de tempo, prefixe a marca de tempo com o valor dos segundos, por exemplo, *ssyyymmdd .* Se a sua aplicação executar rotineiramente operações de listagem e consulta, escolha uma função de hashing que limitará o seu número de consultas. Em alguns casos, um prefixo aleatório pode ser suficiente.
  
- Para obter mais informações sobre o esquema de partição utilizado no Armazenamento Azure, consulte [O Armazenamento Azure: Um serviço de armazenamento em nuvem altamente disponível com forte consistência](https://sigops.org/sosp/sosp11/current/2011-Cascais/printable/11-calder.pdf).

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

Para uma ampla distribuição de conteúdo blob, utilize uma rede de entrega de conteúdos como o Azure CDN. Para mais informações sobre o Azure CDN, consulte [Azure CDN](../../cdn/cdn-overview.md).  

## <a name="sas-and-cors"></a>SAS e CORS

Suponha que precisa autorizar códigos como o JavaScript que está a ser gerido no navegador web de um utilizador ou numa aplicação para telemóvel para aceder a dados no Armazenamento Do Azure. Uma abordagem é construir uma aplicação de serviço que atue como um representante. O dispositivo do utilizador autentica-se com o serviço, que por sua vez autoriza o acesso aos recursos de Armazenamento Azure. Desta forma, pode evitar expor as chaves da sua conta de armazenamento em dispositivos inseguros. No entanto, esta abordagem coloca uma sobrecarga significativa na aplicação de serviço, uma vez que todos os dados transferidos entre o dispositivo do utilizador e o Armazenamento Azure devem passar pela aplicação de serviço.

Pode evitar utilizar uma aplicação de serviço como procuração para o Armazenamento Azure utilizando assinaturas de acesso partilhado (SAS). Utilizando o SAS, pode ativar o dispositivo do utilizador para fazer pedidos diretamente ao Armazenamento Azure, utilizando um sinal de acesso limitado. Por exemplo, se um utilizador quiser enviar uma fotografia para a sua aplicação, então a sua aplicação de serviço pode gerar um SAS e enviá-la para o dispositivo do utilizador. O token SAS pode conceder permissão para escrever a um recurso de armazenamento Azure por um intervalo de tempo especificado, após o qual expira o token SAS. Para obter mais informações sobre o SAS, consulte Grant acesso limitado aos recursos de [Armazenamento Azure utilizando assinaturas de acesso partilhado (SAS)](../common/storage-sas-overview.md).  

Normalmente, um navegador web não permitirá o JavaScript numa página que é hospedada por um site num domínio para realizar determinadas operações, como operações de escrita, para outro domínio. Conhecida como a política de origem mesma, esta política impede que um script malicioso numa página obtenha acesso a dados noutra página web. No entanto, a política de origem pode ser uma limitação na construção de uma solução na nuvem. A partilha de recursos de origem cruzada (CORS) é uma funcionalidade de navegador que permite ao domínio alvo comunicar ao navegador que confia em pedidos originários do domínio de origem.

Por exemplo, suponha que uma aplicação web em funcionamento em Azure faz um pedido de um recurso para uma conta de Armazenamento Azure. A aplicação web é o domínio de origem, e a conta de armazenamento é o domínio alvo. Pode configurar o CORS para qualquer um dos serviços de Armazenamento Azure para comunicar ao navegador web que os pedidos do domínio de origem são confiáveis pelo Armazenamento Azure. Para obter mais informações sobre o CORS, consulte o suporte de [partilha de recursos de origem cruzada (CORS) para o Armazenamento Azure](/rest/api/storageservices/Cross-Origin-Resource-Sharing--CORS--Support-for-the-Azure-Storage-Services).  
  
Tanto o SAS como o CORS podem ajudá-lo a evitar cargas desnecessárias na sua aplicação web.  

## <a name="caching"></a>Colocação em cache

Caching desempenha um papel importante no desempenho. As seguintes secções discutem as melhores práticas de cache.

### <a name="reading-data"></a>Dados de leitura

Em geral, ler dados uma vez é preferível a lê-lo duas vezes. Considere o exemplo de uma aplicação web que recuperou uma bolha de 50 MiB do Armazenamento Azure para servir de conteúdo a um utilizador. Idealmente, a aplicação coloca a bolha localmente para o disco e, em seguida, recupera a versão em cache para pedidos subsequentes do utilizador.

Uma forma de evitar recuperar uma bolha se não tiver sido modificada desde que foi emcache é qualificar a operação GET com um cabeçalho condicional para o tempo de modificação. Se o último tempo modificado for depois do tempo em que a bolha foi emcache, então a bolha é recuperada e re-cache. Caso contrário, a bolha em cache é recuperada para um desempenho ótimo.

Também pode decidir desenhar a sua aplicação para assumir que a bolha permanece inalterada por um curto período de tempo após a sua recuperação. Neste caso, o pedido não necessita de verificar se a bolha foi modificada durante esse intervalo.

Os dados de configuração, os dados de procura e outros dados que são frequentemente utilizados pela aplicação são bons candidatos para o cache.  

Para obter mais informações sobre a utilização de cabeçalhos condicionais, consulte a especificação de [cabeçalhos condicionais para operações](/rest/api/storageservices/specifying-conditional-headers-for-blob-service-operations)de serviço blob .  

### <a name="uploading-data-in-batches"></a>Envio de dados em lotes

Em alguns cenários, pode agregar dados localmente e, em seguida, carregá-los periodicamente num lote em vez de carregar cada pedaço de dados imediatamente. Por exemplo, suponha que uma aplicação web mantenha um arquivo de atividades. A aplicação pode fazer o upload de detalhes de cada atividade como acontece a uma tabela (que requer muitas operações de armazenamento), ou pode guardar detalhes da atividade para um ficheiro de registo local e, em seguida, periodicamente carregar todos os detalhes da atividade como um ficheiro delimitado para uma bolha. Se cada entrada de registo tiver 1 KB de tamanho, pode fazer o upload de milhares de entradas numa única transação. Uma única transação suporta o upload de uma bolha de até 64 MiB de tamanho. O desenvolvedor de aplicações deve projetar para a possibilidade de dispositivo cliente ou falhas de upload. Se os dados da atividade precisarem de ser descarregados por um intervalo de tempo e não para uma única atividade, então a utilização do armazenamento Blob é recomendada através do armazenamento da mesa.

## <a name="net-configuration"></a>configuração .NET

Se utilizar a .NET Framework, esta secção lista várias configurações de configuração rápida que pode utilizar para fazer melhorias significativas de desempenho.  Se utilizar outras línguas, verifique se conceitos semelhantes se aplicam na sua língua escolhida.  

### <a name="use-net-core"></a>Use .NET Core

Desenvolva as suas aplicações de Armazenamento Azure com .NET Core 2.1 ou mais tarde para tirar partido das melhorias de desempenho. Recomenda-se a utilização do Núcleo 3.x .NET 3.x quando possível.

Para obter mais informações sobre melhorias de desempenho em .NET Core, consulte as seguintes publicações de blog:

- [Melhorias de desempenho em .NET Core 3.0](https://devblogs.microsoft.com/dotnet/performance-improvements-in-net-core-3-0/)
- [Melhorias de desempenho em .NET Core 2.1](https://devblogs.microsoft.com/dotnet/performance-improvements-in-net-core-2-1/)

### <a name="increase-default-connection-limit"></a>Aumentar o limite de ligação por defeito

Em .NET, o seguinte código aumenta o limite de ligação padrão (que normalmente é dois em ambiente de cliente ou dez num ambiente de servidor) para 100. Normalmente, deve definir o valor para aproximadamente o número de fios utilizados pela sua aplicação. Detete o limite de ligação antes de abrir quaisquer ligações.

```csharp
ServicePointManager.DefaultConnectionLimit = 100; //(Or More)  
```

Para outras linguagens de programação, consulte a documentação para determinar como definir o limite de ligação.  

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

## <a name="copying-and-moving-blobs"></a>Bolhas de cópia e movimento

O Azure Storage fornece uma série de soluções para copiar e mover bolhas dentro de uma conta de armazenamento, entre contas de armazenamento, e entre sistemas no local e a nuvem. Esta secção descreve algumas destas opções em termos dos seus efeitos no desempenho. Para obter informações sobre a transferência eficiente de dados para ou para o armazenamento blob, consulte [Escolha uma solução Azure para transferência](../common/storage-choose-data-transfer-solution.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)de dados .

### <a name="blob-copy-apis"></a>Cópia blob APIs

Para copiar bolhas através das contas de armazenamento, utilize o bloqueio de entrada da operação [URL.](/rest/api/storageservices/put-block-from-url) Esta operação copia os dados sincronizadamente de qualquer fonte de URL para uma bolha de bloco. A `Put Block from URL` utilização da operação pode reduzir significativamente a largura de banda necessária quando estiver a migrar dados através de contas de armazenamento. Uma vez que a operação de cópia ocorre no lado do serviço, não precisa de descarregar e recarregar os dados.

Para copiar dados dentro da mesma conta de armazenamento, utilize a operação [Copy Blob.](/rest/api/storageservices/Copy-Blob) A cópia dos dados dentro da mesma conta de armazenamento é normalmente concluída rapidamente.  

### <a name="use-azcopy"></a>Utilizar o AZCopy

O utilitário de linha de comando AzCopy é uma opção simples e eficiente para a transferência a granel de bolhas para, a partir e através de contas de armazenamento. A ZCopy está otimizada para este cenário e pode atingir altas taxas de transferência. A versão AzCopy `Put Block From URL` 10 utiliza a operação para copiar dados blob através de contas de armazenamento. Para mais informações, consulte [Copiar ou mover dados para o Armazenamento Azure utilizando o AzCopy v10](/azure/storage/common/storage-use-azcopy-v10).  

### <a name="use-azure-data-box"></a>Utilize caixa de dados Azure

Para importar grandes volumes de dados para o armazenamento blob, considere utilizar a família Azure Data Box para transferências offline. Os dispositivos Data Box fornecidos pela Microsoft são uma boa escolha para mover grandes quantidades de dados para o Azure quando se está limitado pelo tempo, disponibilidade da rede ou custos. Para mais informações, consulte a [Documentação Azure DataBox](/azure/databox/).

## <a name="content-distribution"></a>Distribuição de conteúdos

Por vezes, uma aplicação precisa de servir o mesmo conteúdo a muitos utilizadores (por exemplo, um vídeo de demonstração de produto usado na página inicial de um website), localizado nas mesmas ou múltiplas regiões. Neste cenário, utilize uma Rede de Entrega de Conteúdos (CDN) como o Azure CDN para distribuir conteúdo blob geograficamente. Ao contrário de uma conta de Armazenamento Azure que existe numa única região e que não consegue entregar conteúdo com baixa latência a outras regiões, o Azure CDN utiliza servidores em vários centros de dados em todo o mundo. Além disso, um CDN pode normalmente suportar limites de saída muito mais elevados do que uma única conta de armazenamento.  

Para mais informações sobre o Azure CDN, consulte [Azure CDN](../../cdn/cdn-overview.md).

## <a name="use-metadata"></a>Usar metadados

O serviço Blob suporta pedidos HEAD, que podem incluir propriedades blob ou metadados. Por exemplo, se a sua aplicação precisar dos dados Exif (formato de imagem exchangável) a partir de uma foto, pode recuperar a foto e extraí-la. Para poupar largura de banda e melhorar o desempenho, a sua aplicação pode armazenar os dados exif nos metadados da bolha quando a aplicação fizer o upload da foto. Em seguida, pode recuperar os dados exif em metadados usando apenas um pedido HEAD. Recuperar apenas metadados e não o conteúdo total da bolha poupa largura de banda significativa e reduz o tempo de processamento necessário para extrair os dados exif. Tenha em mente que 8 KiB de metadados podem ser armazenados por bolha.  

## <a name="upload-blobs-quickly"></a>Carregar bolhas rapidamente

Para fazer o upload de bolhas rapidamente, determine primeiro se vai carregar uma bolha ou muitas. Utilize a orientação abaixo para determinar o método correto para utilizar dependendo do seu cenário.  

### <a name="upload-one-large-blob-quickly"></a>Faça upload de uma grande bolha rapidamente

Para fazer o upload de uma única bolha grande rapidamente, uma aplicação de cliente pode carregar os seus blocos ou páginas em paralelo, estando atento aos alvos de escalabilidade para as bolhas individuais e a conta de armazenamento como um todo. As bibliotecas de clientes do Azure Storage suportam o upload em paralelo. Por exemplo, pode utilizar as seguintes propriedades para especificar o número de pedidos simultâneos permitidos em .NET ou Java. As bibliotecas de clientes para outras línguas apoiadas fornecem opções semelhantes.

- Para .NET, delineie a propriedade [BlobRequestOptions.ParallelOperationThreadCount.](/dotnet/api/microsoft.azure.storage.blob.blobrequestoptions.paralleloperationthreadcount)
- Para Java/Android, ligue para o método [BlobRequestOptions.setConcurrentRequestCount (convidesia final ConcurrentRequestCount).](/java/api/com.microsoft.azure.storage.blob.blobrequestoptions.setconcurrentrequestcount)

### <a name="upload-many-blobs-quickly"></a>Faça upload de muitas bolhas rapidamente

Para fazer upload de muitas bolhas rapidamente, faça upload de bolhas em paralelo. O upload em paralelo é mais rápido do que carregar bolhas individuais de cada vez com uploads de blocos paralelos porque espalha o upload através de várias divisórias do serviço de armazenamento. A ZCopy executa uploads em paralelo por padrão, e é recomendado para este cenário. Para mais informações, consulte [Começar com a AzCopy](../common/storage-use-azcopy-v10.md).  

## <a name="choose-the-correct-type-of-blob"></a>Escolha o tipo correto de bolha

O Azure Storage suporta bolhas de blocos, bolhas de apêndice e bolhas de página. Para um determinado cenário de utilização, a sua escolha do tipo blob afetará o desempenho e a escalabilidade da sua solução.

As bolhas de bloco são apropriadas quando pretende carregar grandes quantidades de dados de forma eficiente. Por exemplo, uma aplicação de cliente que faça upload de fotos ou vídeo para o armazenamento blob teria como alvo bolhas de blocos.

As bolhas de apêndice são semelhantes às bolhas de bloco, na medida em que são compostas por blocos. Quando modifica uma bolha de apêndice, os blocos são adicionados apenas à extremidade da bolha. As bolhas de apêndice são úteis para cenários como a exploração madeireira, quando uma aplicação precisa adicionar dados a uma bolha existente.

As bolhas de página são apropriadas se a aplicação precisar de realizar escritos aleatórios nos dados. Por exemplo, os discos de máquinas virtuais Azure são armazenados como bolhas de página. Para mais informações, consulte [Understanding block blobs, apêndice blobs e page blobs](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs).  

## <a name="next-steps"></a>Passos seguintes

- [Metas de escalabilidade e desempenho para armazenamento blob](scalability-targets.md)
- [Metas de escalabilidade e desempenho para contas de armazenamento padrão](../common/scalability-targets-standard-account.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
- [Códigos de estado e erro](/rest/api/storageservices/Status-and-Error-Codes2)
