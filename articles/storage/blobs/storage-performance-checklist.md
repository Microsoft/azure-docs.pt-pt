---
title: Lista de verificação de desempenho e escalabilidade para armazenamento de BLOBs – armazenamento do Azure
description: Uma lista de verificação de práticas comprovadas para uso com o armazenamento de BLOBs no desenvolvimento de aplicativos de alto desempenho.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 10/10/2019
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 84707c72e62bed7621d94dbd1ec65607cfcfd2d6
ms.sourcegitcommit: bd4198a3f2a028f0ce0a63e5f479242f6a98cc04
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/14/2019
ms.locfileid: "72303044"
---
# <a name="performance-and-scalability-checklist-for-blob-storage"></a>Lista de verificação de desempenho e escalabilidade para armazenamento de BLOBs

A Microsoft desenvolveu várias práticas comprovadas para o desenvolvimento de aplicativos de alto desempenho com o armazenamento de BLOBs. Esta lista de verificação identifica as principais práticas que os desenvolvedores podem seguir para otimizar o desempenho. Tenha essas práticas em mente enquanto estiver projetando seu aplicativo e durante todo o processo.

O armazenamento do Azure tem metas de desempenho e escalabilidade para capacidade, taxa de transação e largura de banda. Para obter mais informações sobre destinos de escalabilidade de armazenamento do Azure, consulte [metas de desempenho e escalabilidade do armazenamento do Azure para contas de armazenamento](../common/storage-scalability-targets.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

## <a name="checklist"></a>Lista de Verificação

Este artigo organiza as práticas comprovadas de desempenho em uma lista de verificação que você pode seguir ao desenvolver seu aplicativo de armazenamento de BLOBs.

| Concluído | Categoria | Consideração de design |
| --- | --- | --- |
| &nbsp; |Metas de escalabilidade |[Você pode projetar seu aplicativo para não usar mais do que o número máximo de contas de armazenamento?](#maximum-number-of-storage-accounts) |
| &nbsp; |Metas de escalabilidade |[Você está evitando a abordagem de limites de capacidade e transação?](#capacity-and-transaction-targets) |
| &nbsp; |Metas de escalabilidade |[Um grande número de clientes está acessando um único blob simultaneamente?](#multiple-clients-accessing-a-single-blob-concurrently) |
| &nbsp; |Metas de escalabilidade |[Seu aplicativo permanece dentro das metas de escalabilidade de um único blob?](#bandwidth-and-operations-per-blob) |
| &nbsp; |Criação de partições |[Sua Convenção de nomenclatura é projetada para permitir melhor balanceamento de carga?](#partitioning) |
| &nbsp; |Funcionamento em Rede |[Os dispositivos do lado do cliente têm largura de banda suficientemente alta e baixa latência para alcançar o desempenho necessário?](#throughput) |
| &nbsp; |Funcionamento em Rede |[Os dispositivos do lado do cliente têm um link de rede de alta qualidade?](#link-quality) |
| &nbsp; |Funcionamento em Rede |[O aplicativo cliente está na mesma região que a conta de armazenamento?](#location) |
| &nbsp; |Acesso direto do cliente |[Você está usando SAS (assinaturas de acesso compartilhado) e CORS (compartilhamento de recursos entre origens) para habilitar o acesso direto ao armazenamento do Azure?](#sas-and-cors) |
| &nbsp; |Colocação em Cache |[O aplicativo armazena em cache os dados que são frequentemente acessados e raramente alterados?](#reading-data) |
| &nbsp; |Colocação em Cache |[O aplicativo faz atualizações em lote armazenando-as no cliente e, em seguida, carregando-as em conjuntos maiores?](#uploading-data-in-batches) |
| &nbsp; |Configuração do .NET |[Você está usando o .NET Core 2,1 ou posterior para obter um desempenho ideal?](#use-net-core) |
| &nbsp; |Configuração do .NET |[Você configurou seu cliente para usar um número suficiente de conexões simultâneas?](#increase-default-connection-limit) |
| &nbsp; |Configuração do .NET |[Para aplicativos .NET, você configurou o .NET para usar um número suficiente de threads?](#increase-minimum-number-of-threads) |
| &nbsp; |Paralelismo |[Você assegurou que o paralelismo está limitado adequadamente para que você não sobrecarregar os recursos do cliente ou abordar as metas de escalabilidade?](#unbounded-parallelism) |
| &nbsp; |Ferramentas |[Você está usando as versões mais recentes das bibliotecas e ferramentas de cliente fornecidas pela Microsoft?](#client-libraries-and-tools) |
| &nbsp; |Tentativas |[Você está usando uma política de repetição com uma retirada exponencial para limitação de erros e tempos limite?](#timeout-and-server-busy-errors) |
| &nbsp; |Tentativas |[Seu aplicativo está evitando repetições para erros sem nova tentativa?](#non-retryable-errors) |
| &nbsp; |Distribuição de conteúdo |[Você está usando uma CDN para distribuição de conteúdo?](#content-distribution) |
| &nbsp; |Usar metadados |[Você está armazenando metadados usados com frequência sobre BLOBs em seus metadados?](#use-metadata) |
| &nbsp; |Carregando rapidamente |[Ao tentar carregar um blob rapidamente, você está carregando blocos em paralelo?](#upload-one-large-blob-quickly) |
| &nbsp; |Carregando rapidamente |[Ao tentar carregar vários BLOBs rapidamente, você está carregando BLOBs em paralelo?](#upload-many-blobs-quickly) |
| &nbsp; |Tipo de BLOB |[Você está usando blobs de página ou BLOBs de blocos quando apropriado?](#choose-the-correct-type-of-blob) |

## <a name="scalability-targets"></a>Metas de escalabilidade

Se seu aplicativo se aproximar ou exceder qualquer um dos destinos de escalabilidade, ele poderá encontrar maiores latências ou limitação de transação. Quando o armazenamento do Azure limita seu aplicativo, o serviço começa a retornar os códigos de erro 503 (servidor ocupado) ou 500 (tempo limite da operação). Evitar esses erros ao permanecer dentro dos limites das metas de escalabilidade é uma parte importante do aprimoramento do desempenho do seu aplicativo.

Para obter mais informações sobre metas de escalabilidade para o serviço Fila, consulte [metas de desempenho e escalabilidade do armazenamento do Azure](/azure/storage/common/storage-scalability-targets?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#azure-blob-storage-scale-targets).

### <a name="maximum-number-of-storage-accounts"></a>Número máximo de contas de armazenamento

Se você estiver se aproximando do número máximo de contas de armazenamento permitidas para uma combinação de assinatura/região específica, avalie seu cenário e determine se alguma das condições a seguir se aplica:

- Você está usando contas de armazenamento para armazenar discos não gerenciados e adicionar esses discos às suas VMs (máquinas virtuais)? Para este cenário, a Microsoft recomenda o uso de discos gerenciados. O Managed disks é dimensionado para você automaticamente e sem a necessidade de criar e gerenciar contas de armazenamento individuais. Para obter mais informações, consulte [introdução aos Managed disks do Azure](../../virtual-machines/windows/managed-disks-overview.md)
- Você está usando uma conta de armazenamento por cliente, para fins de isolamento de dados? Para este cenário, a Microsoft recomenda usar um contêiner de BLOB para cada cliente, em vez de uma conta de armazenamento inteira. O armazenamento do Azure agora permite que você atribua funções RBAC (controle de acesso baseado em função) em uma base por contêiner. Para obter mais informações, consulte [conceder acesso ao blob do Azure e dados de fila com RBAC no portal do Azure](../common/storage-auth-aad-rbac-portal.md).
- Você está usando várias contas de armazenamento para fragmentar para aumentar a entrada, a saída, as operações de e/s por segundo (IOPS) ou a capacidade? Nesse cenário, a Microsoft recomenda que você aproveite os limites maiores de contas de armazenamento padrão para reduzir o número de contas de armazenamento necessárias para sua carga de trabalho, se possível. Contate o [suporte do Azure](https://azure.microsoft.com/support/options/) para solicitar limites maiores para sua conta de armazenamento. Para obter mais informações, consulte [anunciando contas de armazenamento maiores e de maior escala](https://azure.microsoft.com/blog/announcing-larger-higher-scale-storage-accounts/).

### <a name="capacity-and-transaction-targets"></a>Metas de capacidade e de transação

Se seu aplicativo estiver se aproximando das metas de escalabilidade de uma única conta de armazenamento, considere a adoção de uma das seguintes abordagens:  

- Se o seu aplicativo atingir o destino da transação, considere o uso de contas de armazenamento de blobs de blocos, que são otimizadas para altas taxas de transação e latência baixa e consistente. Para mais informações, veja [Visão geral de conta de armazenamento do Azure](../common/storage-account-overview.md).
- Reconsidere a carga de trabalho que faz com que seu aplicativo aborde ou exceda a meta de escalabilidade. Você pode criá-lo de forma diferente para usar menos largura de banda ou capacidade, ou menos transações?
- Se seu aplicativo deve exceder um dos destinos de escalabilidade, crie várias contas de armazenamento e Particione os dados do aplicativo entre essas várias contas de armazenamento. Se você usar esse padrão, certifique-se de projetar seu aplicativo para que você possa adicionar mais contas de armazenamento no futuro para o balanceamento de carga. As próprias contas de armazenamento não têm nenhum custo além do uso em termos de dados armazenados, transações feitas ou dados transferidos.
- Se seu aplicativo estiver se aproximando das metas de largura de banda, considere compactar dados no lado do cliente para reduzir a largura de banda necessária para enviar os dados para o armazenamento do Azure.
    Embora a compactação de dados possa economizar largura de banda e melhorar o desempenho da rede, ela também pode ter efeitos negativos no desempenho. Avalie o impacto no desempenho dos requisitos de processamento adicionais para compactação e descompactação de dados no lado do cliente. Tenha em mente que armazenar dados compactados pode dificultar a solução de problemas, pois pode ser mais desafiador exibir os dados usando ferramentas padrão.
- Se seu aplicativo estiver se aproximando das metas de escalabilidade, verifique se você está usando uma retirada exponencial para novas tentativas. É melhor tentar evitar o alcance das metas de escalabilidade implementando as recomendações descritas neste artigo. No entanto, o uso de uma retirada exponencial para novas tentativas impedirá que seu aplicativo seja repetido rapidamente, o que poderia tornar a limitação pior. Para obter mais informações, consulte a seção intitulada [tempo limite e erros de servidor ocupado](#timeout-and-server-busy-errors).

### <a name="multiple-clients-accessing-a-single-blob-concurrently"></a>Vários clientes acessando um único blob simultaneamente

Se você tiver um grande número de clientes acessando um único blob simultaneamente, você precisará considerar as metas de escalabilidade por blob e por conta de armazenamento. O número exato de clientes que podem acessar um único blob variará dependendo de fatores como o número de clientes que solicitam o blob simultaneamente, o tamanho do blob e as condições de rede.

Se o blob puder ser distribuído por meio de uma CDN, como imagens ou vídeos servidos de um site, você poderá usar uma CDN. Para obter mais informações, consulte a seção intitulada [distribuição de conteúdo](#content-distribution).

Em outros cenários, como simulações científicas em que os dados são confidenciais, você tem duas opções. A primeira é escalonar o acesso de sua carga de trabalho, de modo que o blob seja acessado durante um período de tempo vs sendo acessado simultaneamente. Como alternativa, você pode copiar temporariamente o blob para várias contas de armazenamento para aumentar o total de IOPS por blob e entre contas de armazenamento. Os resultados variam dependendo do comportamento do seu aplicativo, portanto, certifique-se de testar padrões de simultaneidade durante o design.

### <a name="bandwidth-and-operations-per-blob"></a>Largura de banda e operações por blob

Um único blob dá suporte a até 500 solicitações por segundo. Se você tiver vários clientes que precisam ler o mesmo BLOB e você pode exceder esse limite, considere usar uma conta de armazenamento de blob de blocos. Uma conta de armazenamento de blobs de blocos fornece uma taxa de solicitação mais alta ou operações de e/s por segundo (IOPS).

Você também pode usar uma CDN (rede de distribuição de conteúdo), como a CDN do Azure, para distribuir operações no BLOB. Para obter mais informações sobre a CDN do Azure, consulte [visão geral da CDN do Azure](../../cdn/cdn-overview.md).  

## <a name="partitioning"></a>Criação de partições

Entender como o armazenamento do Azure particiona seus dados de blob é útil para melhorar o desempenho. O armazenamento do Azure pode servir dados em uma única partição mais rapidamente do que os dados que abrangem várias partições. Ao nomear seus BLOBs adequadamente, você pode melhorar a eficiência das solicitações de leitura.

O armazenamento de BLOBs usa um esquema de particionamento baseado em intervalo para dimensionamento e balanceamento de carga. Cada blob tem uma chave de partição composta pelo nome completo do blob (conta + contêiner + BLOB). A chave de partição é usada para particionar dados de BLOB em intervalos. Os intervalos são então balanceados por carga no armazenamento de BLOBs.

O particionamento baseado em intervalo significa que as convenções de nomenclatura que usam a ordenação lexical (por exemplo, *mypayroll*, *myperformance*, *MyEmployees*etc.) ou carimbos de data/hora (*log20160101*, *log20160102*, *log20160102* , etc.) são mais prováveis de resultar nas partições que estão sendo colocalizadas no mesmo servidor de partição. , até que o aumento da carga exija que eles sejam divididos em intervalos menores. A colocalização de BLOBs no mesmo servidor de partição melhora o desempenho, portanto, uma parte importante do aprimoramento de desempenho envolve nomear blobs de forma a organizá-los com mais eficiência.

Por exemplo, todos os BLOBs dentro de um contêiner podem ser servidos por um único servidor até que a carga nesses BLOBs exija um rebalanceamento adicional dos intervalos de partição. Da mesma forma, um grupo de contas levemente carregadas com seus nomes organizados em ordem lexical pode ser servido por um único servidor até que a carga em uma ou todas essas contas exija que eles sejam divididos em vários servidores de partição.

Cada operação de balanceamento de carga pode afetar a latência de chamadas de armazenamento durante a operação. A capacidade do serviço de lidar com uma intermitência repentina de tráfego para uma partição é limitada pela escalabilidade de um único servidor de partição até que a operação de balanceamento de carga seja iniciada e rebalancee o intervalo de chaves de partição.

Você pode seguir algumas práticas recomendadas para reduzir a frequência dessas operações.  

- Se possível, use o BLOB ou os tamanhos de bloco maiores que 4 MiB para contas de armazenamento Standard e superior a 256 KiB para contas de armazenamento Premium. Os tamanhos maiores de BLOB ou bloco ativam automaticamente blobs de blocos de alta taxa de transferência. Os blobs de blocos de alta taxa de transferência fornecem ingestão de alto desempenho que não é afetada pela nomenclatura de partição.
- Examine a Convenção de nomenclatura usada para contas, contêineres, BLOBs, tabelas e filas. Considere a possibilidade de prefixar nomes de conta, contêiner ou BLOB com um hash de três dígitos usando uma função de hash que melhor atenda às suas necessidades.
- Se você organizar seus dados usando carimbos de data/hora ou identificadores numéricos, certifique-se de que você não está usando um padrão de tráfego somente de acréscimo (ou somente preceder). Esses padrões não são adequados para um sistema de particionamento baseado em intervalo. Esses padrões podem levar a todo o tráfego indo para uma única partição e limitar o sistema de balanceamento de carga com eficiência.

    Por exemplo, se você tiver operações diárias que usam um blob com um carimbo de data/hora como *aaaammdd*, todo o tráfego dessa operação diária será direcionado para um único BLOB, que é servido por um único servidor de partição. Considere se os limites por blob e os limites por partição atendem às suas necessidades e considere dividir essa operação em vários BLOBs, se necessário. Da mesma forma, se você armazenar dados de série temporal em suas tabelas, todo o tráfego poderá ser direcionado para a última parte do namespace de chave. Se você estiver usando IDs numéricas, Prefixe a ID com um hash de três dígitos. Se você estiver usando carimbos de hora, Prefixe o carimbo de data/hora com o valor de segundos, por exemplo, *ssaaaammdd*. Se seu aplicativo executa rotineiramente as operações de listagem e consulta, escolha uma função de hash que limitará o número de consultas. Em alguns casos, um prefixo aleatório pode ser suficiente.
  
- Para obter mais informações sobre o esquema de particionamento usado no armazenamento do Azure, consulte [armazenamento do Azure: um serviço de armazenamento em nuvem altamente disponível com consistência forte](https://sigops.org/sosp/sosp11/current/2011-Cascais/printable/11-calder.pdf).

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

Para uma ampla distribuição de conteúdo de BLOB, use uma rede de entrega de conteúdo, como a CDN do Azure. Para obter mais informações sobre a CDN do Azure, consulte [CDN do Azure](../../cdn/cdn-overview.md).  

## <a name="sas-and-cors"></a>SAS e CORS

Suponha que você precise autorizar um código como o JavaScript que está sendo executado no navegador da Web de um usuário ou em um aplicativo de telefone celular para acessar dados no armazenamento do Azure. Uma abordagem é criar um aplicativo de serviço que atue como um proxy. O dispositivo do usuário é autenticado com o serviço que, por sua vez, autoriza o acesso aos recursos de armazenamento do Azure. Dessa forma, você pode evitar expor suas chaves de conta de armazenamento em dispositivos não seguros. No entanto, essa abordagem coloca uma sobrecarga significativa no aplicativo de serviço, pois todos os dados transferidos entre o dispositivo do usuário e o armazenamento do Azure devem passar pelo aplicativo de serviço.

Você pode evitar o uso de um aplicativo de serviço como proxy para o armazenamento do Azure usando SAS (assinaturas de acesso compartilhado). Usando a SAS, você pode habilitar o dispositivo do usuário para fazer solicitações diretamente no armazenamento do Azure usando um token de acesso limitado. Por exemplo, se um usuário quiser carregar uma foto em seu aplicativo, o aplicativo de serviço poderá gerar uma SAS e enviá-la para o dispositivo do usuário. O token SAS pode conceder permissão para gravar em um recurso de armazenamento do Azure por um intervalo de tempo especificado, após o qual o token SAS expira. Para obter mais informações sobre SAS, consulte [conceder acesso limitado aos recursos de armazenamento do Azure usando SAS (assinaturas de acesso compartilhado)](../common/storage-sas-overview.md).  

Normalmente, um navegador da Web não permitirá o JavaScript em uma página hospedada por um site em um domínio para executar determinadas operações, como operações de gravação, em outro domínio. Conhecida como a política de mesma origem, essa política impede que um script mal-intencionado em uma página obtenha acesso a dados em outra página da Web. No entanto, a política de mesma origem pode ser uma limitação ao criar uma solução na nuvem. O CORS (compartilhamento de recursos entre origens) é um recurso de navegador que permite que o domínio de destino se comunique com o navegador que confia nas solicitações originadas no domínio de origem.

Por exemplo, suponha que um aplicativo Web em execução no Azure faça uma solicitação de um recurso para uma conta de armazenamento do Azure. O aplicativo Web é o domínio de origem e a conta de armazenamento é o domínio de destino. Você pode configurar o CORS para qualquer um dos serviços de armazenamento do Azure para se comunicar com o navegador da Web que as solicitações do domínio de origem são confiáveis pelo armazenamento do Azure. Para obter mais informações sobre CORS, consulte [suporte ao compartilhamento de recursos entre origens (CORS) para o armazenamento do Azure](/rest/api/storageservices/Cross-Origin-Resource-Sharing--CORS--Support-for-the-Azure-Storage-Services).  
  
SAS e CORS podem ajudá-lo a evitar a carga desnecessária em seu aplicativo Web.  

## <a name="caching"></a>Colocação em Cache

O Caching desempenha um papel importante no desempenho. As seções a seguir abordam as práticas recomendadas de cache.

### <a name="reading-data"></a>Lendo dados

Em geral, a leitura de dados uma vez é preferível para lê-lo duas vezes. Considere o exemplo de um aplicativo Web que recuperou um blob de MiB 50 do armazenamento do Azure para servir como conteúdo para um usuário. O ideal é que o aplicativo armazene em cache o blob localmente em disco e, em seguida, recupere a versão armazenada em cache para solicitações subsequentes do usuário.

Uma maneira de evitar a recuperação de um blob se ele não foi modificado desde que ele foi armazenado em cache é qualificar a operação GET com um cabeçalho condicional para o tempo de modificação. Se a hora da última modificação for posterior à hora em que o blob foi armazenado em cache, o blob será recuperado e armazenado em cache novamente. Caso contrário, o blob armazenado em cache é recuperado para um desempenho ideal.

Você também pode optar por projetar seu aplicativo para assumir que o blob permanece inalterado por um curto período depois de recuperá-lo. Nesse caso, o aplicativo não precisa verificar se o blob foi modificado durante esse intervalo.

Dados de configuração, dados de pesquisa e outros dados frequentemente usados pelo aplicativo são bons candidatos para cache.  

Para obter mais informações sobre como usar cabeçalhos condicionais, consulte [especificando cabeçalhos condicionais para operações de serviço blob](/rest/api/storageservices/specifying-conditional-headers-for-blob-service-operations).  

### <a name="uploading-data-in-batches"></a>Carregando dados em lotes

Em alguns cenários, você pode agregar dados localmente e, em seguida, carregá-los periodicamente em um lote em vez de carregar cada parte dos dados imediatamente. Por exemplo, suponha que um aplicativo Web mantenha um arquivo de log de atividades. O aplicativo pode carregar detalhes de cada atividade à medida que ela acontece com uma tabela (que requer muitas operações de armazenamento) ou pode salvar detalhes da atividade em um arquivo de log local e, em seguida, carregar periodicamente todos os detalhes da atividade como um arquivo delimitado em um blob. Se cada entrada de log tiver 1 KB de tamanho, você poderá carregar milhares de entradas em uma única transação. Uma única transação dá suporte ao carregamento de um blob de até 64 MiB de tamanho. O desenvolvedor de aplicativos deve criar a possibilidade de falhas de carregamento ou de dispositivo cliente. Se os dados da atividade precisarem ser baixados por um intervalo de tempo em vez de uma única atividade, o uso do armazenamento de blob é recomendado em relação ao armazenamento de tabelas.

## <a name="net-configuration"></a>Configuração do .NET

Se estiver usando o .NET Framework, esta seção listará várias definições de configuração rápida que você pode usar para fazer melhorias significativas no desempenho.  Se estiver usando outras linguagens, verifique se conceitos semelhantes se aplicam à linguagem escolhida.  

### <a name="use-net-core"></a>Usar o .NET Core

Desenvolva seus aplicativos de armazenamento do Azure com o .NET Core 2,1 ou posterior para aproveitar os aprimoramentos de desempenho. O uso do .NET Core 3. x é recomendado quando possível.

Para obter mais informações sobre melhorias de desempenho no .NET Core, consulte as postagens de blog a seguir:

- [Melhorias de desempenho no .NET Core 3,0](https://devblogs.microsoft.com/dotnet/performance-improvements-in-net-core-3-0/)
- [Melhorias de desempenho no .NET Core 2,1](https://devblogs.microsoft.com/dotnet/performance-improvements-in-net-core-2-1/)

### <a name="increase-default-connection-limit"></a>Aumentar limite de conexão padrão

No .NET, o código a seguir aumenta o limite de conexão padrão (que geralmente é 2 em um ambiente de cliente ou 10 em um ambiente de servidor) para 100. Normalmente, você deve definir o valor para aproximadamente o número de threads usados pelo seu aplicativo. Defina o limite de conexão antes de abrir qualquer conexão.

```csharp
ServicePointManager.DefaultConnectionLimit = 100; //(Or More)  
```

Para outras linguagens de programação, consulte a documentação para determinar como definir o limite de conexão.  

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

## <a name="transfer-data"></a>Transferir dados

Para obter informações sobre como transferir dados de e para o armazenamento de BLOBs ou entre contas de armazenamento com eficiência, consulte [escolher uma solução do Azure para transferência de dados](../common/storage-choose-data-transfer-solution.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

## <a name="content-distribution"></a>Distribuição de conteúdo

Às vezes, um aplicativo precisa fornecer o mesmo conteúdo a muitos usuários (por exemplo, um vídeo de demonstração do produto usado na home page de um site), localizado em qualquer região ou em várias regiões. Nesse cenário, use uma CDN (rede de distribuição de conteúdo), como a CDN do Azure, para distribuir o conteúdo do blob geograficamente. Ao contrário de uma conta de armazenamento do Azure que existe em uma única região e que não pode fornecer conteúdo com baixa latência para outras regiões, a CDN do Azure usa servidores em vários data centers em todo o mundo. Além disso, uma CDN normalmente pode dar suporte a limites de saída muito maiores do que uma única conta de armazenamento.  

Para obter mais informações sobre a CDN do Azure, consulte [CDN do Azure](../../cdn/cdn-overview.md).

## <a name="use-metadata"></a>Usar metadados

O serviço blob dá suporte a solicitações HEAD, que podem incluir propriedades de BLOB ou metadados. Por exemplo, se seu aplicativo precisar de dados EXIF (formato de imagem exchangável) de uma foto, ele poderá recuperar a foto e extraí-la. Para economizar largura de banda e melhorar o desempenho, seu aplicativo pode armazenar os dados EXIF nos metadados do blob quando o aplicativo carregar a foto. Em seguida, você pode recuperar os dados EXIF nos metadados usando apenas uma solicitação HEAD. Recuperar somente metadados e não o conteúdo completo do blob salva uma largura de banda significativa e reduz o tempo de processamento necessário para extrair os dados EXIF. Tenha em mente que apenas 8 KB de metadados podem ser armazenados por blob.  

## <a name="upload-blobs-quickly"></a>Carregar BLOBs rapidamente

Para carregar os BLOBs rapidamente, primeiro determine se você estará carregando um ou vários BLOBs. Use as diretrizes abaixo para determinar o método correto a ser usado dependendo do seu cenário.  

### <a name="upload-one-large-blob-quickly"></a>Carregar um blob grande rapidamente

Para carregar um único blob grande rapidamente, um aplicativo cliente pode carregar seus blocos ou páginas em paralelo, sendo atento às metas de escalabilidade para BLOBs individuais e a conta de armazenamento como um todo. As bibliotecas de cliente de armazenamento do Azure dão suporte ao carregamento em paralelo. Por exemplo, você pode usar as propriedades a seguir para especificar o número de solicitações simultâneas permitidas no .NET ou Java. As bibliotecas de cliente para outros idiomas com suporte fornecem opções semelhantes.

- Para .NET, defina a propriedade [BlobRequestOptions. ParallelOperationThreadCount](/dotnet/api/microsoft.azure.storage.blob.blobrequestoptions.paralleloperationthreadcount) .
- Para Java/Android, chame o método [BlobRequestOptions. setConcurrentRequestCount (inteiro final concurrentRequestCount)](/java/api/com.microsoft.azure.storage.blob._blob_request_options.setconcurrentrequestcount) .

### <a name="upload-many-blobs-quickly"></a>Carregar vários BLOBs rapidamente

Para carregar vários BLOBs rapidamente, carregue os BLOBs em paralelo. O carregamento em paralelo é mais rápido do que carregar BLOBs únicos por vez com carregamentos de bloco paralelos porque ele espalha o carregamento entre várias partições do serviço de armazenamento. O AzCopy executa carregamentos em paralelo por padrão e é recomendado para esse cenário. Para obter mais informações, consulte Introdução [ao AzCopy](../common/storage-use-azcopy-v10.md).  

## <a name="choose-the-correct-type-of-blob"></a>Escolha o tipo correto de BLOB

O armazenamento do Azure dá suporte a blobs de blocos, blobs de acréscimo e blobs de páginas. Para um determinado cenário de uso, sua escolha de tipo de blob afetará o desempenho e a escalabilidade de sua solução.

Blobs de blocos são apropriados quando você deseja carregar grandes quantidades de dados com eficiência. Por exemplo, um aplicativo cliente que carrega fotos ou vídeo no armazenamento de BLOBs direcionaria blobs de blocos.

Os blobs de acréscimo são semelhantes aos blobs de blocos, pois são compostos por blocos. Quando você modifica um blob de acréscimo, os blocos são adicionados somente ao final do blob. Os blobs de acréscimo são úteis para cenários como registro em log, quando um aplicativo precisa adicionar dados a um blob existente.

Os blobs de páginas são apropriados se o aplicativo precisar executar gravações aleatórias nos dados. Por exemplo, os discos de máquina virtual do Azure são armazenados como BLOBs de página. Para obter mais informações, consulte [noções básicas sobre blobs de blocos, blobs de acréscimo e blobs de páginas](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs).  

## <a name="next-steps"></a>Passos seguintes

- [Escalabilidade e metas de desempenho do armazenamento do Azure para contas de armazenamento](../common/storage-scalability-targets.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
- [Lista de verificação de desempenho e escalabilidade para armazenamento de filas](../queues/storage-performance-checklist.md)
- [Lista de verificação de desempenho e escalabilidade para o armazenamento de tabelas](../tables/storage-performance-checklist.md)
- [Status e códigos de erro](/rest/api/storageservices/Status-and-Error-Codes2)
