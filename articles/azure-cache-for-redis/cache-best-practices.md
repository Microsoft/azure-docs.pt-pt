---
title: Melhores práticas da Cache do Azure para Redis
description: Aprenda a usar o seu Cache Azure para Redis de forma eficaz, seguindo estas boas práticas.
author: joncole
ms.service: cache
ms.topic: conceptual
ms.date: 01/06/2020
ms.author: joncole
ms.openlocfilehash: 105a3996753a1d1c2d71846cc8bad574e4498acf
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80478620"
---
# <a name="best-practices-for-azure-cache-for-redis"></a>Melhores práticas da Cache do Azure para Redis 
Seguindo estas boas práticas, pode ajudar a maximizar o desempenho e a utilização rentável do seu Cache Azure para a instância Redis.

## <a name="configuration-and-concepts"></a>Configuração e conceitos
 * **Utilizar o nível Standard ou Premium para sistemas de produção.**  O nível Básico é um único sistema de nó sem replicação de dados e sem SLA. Além disso, utilize pelo menos uma cache C1.  Os caches C0 destinam-se a cenários simples de dev/teste, uma vez que têm um núcleo de CPU partilhado, pouca memória, e são propensos a problemas de "vizinho barulhento".

 * **Lembre-se que Redis é uma loja de dados na memória.**  [Este artigo](cache-troubleshoot-data-loss.md) descreve alguns cenários onde a perda de dados pode ocorrer.

 * **Desenvolva o seu sistema** de modo a que possa lidar com os blips de ligação devido a [remendos e falhas](cache-failover.md).

 * **Configure a [sua configuração reservada à memória máxima](cache-configure.md#maxmemory-policy-and-maxmemory-reserved) para melhorar a capacidade de resposta** do sistema em condições de pressão de memória.  Uma definição de reserva suficiente é especialmente importante para cargas de trabalho pesadas ou se estiver a armazenar valores maiores (100 KB ou mais) em Redis. Deve começar com 10% do tamanho da sua cache e aumentar esta percentagem se tiver cargas pesadas.

 * **Redis funciona melhor com valores menores,** por isso considere cortar dados maiores em várias teclas.  [Nesta discussão redis,](https://stackoverflow.com/questions/55517224/what-is-the-ideal-value-size-range-for-redis-is-100kb-too-large/)estão enumeradas algumas considerações que devem considerar cuidadosamente.  Leia [este artigo](cache-troubleshoot-client.md#large-request-or-response-size) para um problema de exemplo que pode ser causado por grandes valores.

 * **Localize a sua instância de cache e a sua aplicação na mesma região.**  Ligar a uma cache numa região diferente pode aumentar significativamente a latência e reduzir a fiabilidade.  Embora possa ligar-se de fora de Azure, não é recomendado *especialmente quando se utiliza redis como cache*.  Se estiver a usar o Redis como apenas uma loja chave/valor, a latência pode não ser a principal preocupação. 

 * **Reutilizar ligações.**  A criação de novas ligações é dispendiosa e aumenta a latência, pelo que reutilizar as ligações tanto quanto possível. Se optar por criar novas ligações, certifique-se de fechar as antigas ligações antes de as lançar (mesmo em línguas de memória geridas como .NET ou Java).

 * **Configure a biblioteca do seu cliente para utilizar um tempo limite de *ligação* de pelo menos 15 segundos,** dando ao sistema tempo para se ligar mesmo em condições de CPU mais elevadas.  Um pequeno valor de tempo de ligação não garante que a ligação seja estabelecida nesse período de tempo.  Se algo correr mal (CPU de cliente elevado, CPU de servidor alto, e assim por diante), então um curto valor de tempo de ligação fará com que a tentativa de ligação falhe. Este comportamento muitas vezes piora a situação.  Em vez de ajudar, os tempos mais curtos agravam o problema forçando o sistema a reiniciar o processo de tentativa de reconectar-se, o que pode levar a um ciclo de *reprovação > de ligação > retry.* Geralmente recomendamos que deixe o seu tempo limite de ligação a 15 segundos ou mais. É melhor deixar a sua tentativa de ligação ter sucesso após 15 ou 20 segundos do que fazê-la falhar rapidamente apenas para voltar a tentar. Tal volta pode fazer com que a sua paragem dure mais tempo do que se deixar o sistema demorar mais tempo inicialmente.  
     > [!NOTE]
     > Esta orientação é específica para a tentativa de *ligação* e não está relacionada com o tempo que está disposto a esperar que uma *operação* como GET ou SET complete.
 
 * **Evite operações dispendiosas** - Algumas operações da Redis, como o comando [KEYS,](https://redis.io/commands/keys) são *muito* caras e devem ser evitadas.  Para mais informações, consulte algumas considerações em torno [de comandos de longa duração](cache-troubleshoot-server.md#long-running-commands)

 * **Utilizar encriptação TLS** - O Cache Azure para Redis requer comunicações encriptadas TLS por padrão.  As versões TLS 1.0, 1.1 e 1.2 são atualmente suportadas.  No entanto, os TLS 1.0 e 1.1 estão no caminho da depreciação em toda a indústria, por isso use TLS 1.2 se possível.  Se a sua biblioteca ou ferramenta do seu cliente não suportar tLS, então permitir ligações não encriptadas pode ser feita [através do portal Azure](cache-configure.md#access-ports) ou [apis de gestão](https://docs.microsoft.com/rest/api/redis/redis/update).  Nestes casos em que as ligações encriptadas não são possíveis, recomenda-se a colocação da sua cache e aplicação do cliente numa rede virtual.  Para obter mais informações sobre quais as portas utilizadas no cenário de cache de rede virtual, consulte esta [tabela](cache-how-to-premium-vnet.md#outbound-port-requirements).
 
## <a name="memory-management"></a>Gestão da memória
Existem várias coisas relacionadas com o uso da memória dentro da sua instância do servidor Redis que você pode querer considerar.  Aqui estão alguns:

 * **Escolha uma política de [despejo](https://redis.io/topics/lru-cache) que funcione para a sua candidatura.**  A política de incumprimento para o Azure Redis é *volátil-lru*, o que significa que apenas as chaves que têm um conjunto de valor TTL serão elegíveis para despejo.  Se nenhuma tecla tiver um valor TTL, então o sistema não despeja chaves.  Se quiser que o sistema permita que qualquer chave seja despejada se estiver sob pressão de memória, então talvez queira considerar a política *allkeys-lru.*

 * **Detete um valor de validade nas suas chaves.**  Uma expiração removerá as teclas proactivamente em vez de esperar até que haja pressão de memória.  Quando o despejo faz efeito devido à pressão da memória, pode causar uma carga adicional no seu servidor.  Para mais informações, consulte a documentação para os comandos [EXPIRE](https://redis.io/commands/expire) e [EXPIREAT.](https://redis.io/commands/expireat)
 
## <a name="client-library-specific-guidance"></a>Orientação específica da biblioteca do cliente
 * [StackExchange.Redis (.NET)](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#file-redis-bestpractices-stackexchange-redis-md)
 * [Java - Que cliente devo usar?](https://gist.github.com/warrenzhu25/1beb02a09b6afd41dff2c27c53918ce7#file-azure-redis-java-best-practices-md)
 * [Alface (Java)](https://gist.github.com/warrenzhu25/181ccac7fa70411f7eb72aff23aa8a6a#file-azure-redis-lettuce-best-practices-md)
 * [Jedis (Java)](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#file-redis-bestpractices-java-jedis-md)
 * [Node.js](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#file-redis-bestpractices-node-js-md)
 * [PHP](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#file-redis-bestpractices-php-md)
 * [Provedor estatal de sessão de Asp.Net](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#file-redis-bestpractices-session-state-provider-md)


## <a name="when-is-it-safe-to-retry"></a>Quando é seguro voltar a tentar?
Infelizmente, não há uma resposta fácil.  Cada pedido precisa decidir que operações podem ser novamente julgadas e quais não podem.  Cada operação tem diferentes requisitos e dependências inter-chave.  Aqui estão algumas coisas que pode considerar:

 * Podes ter erros do lado do cliente, mesmo que o Redis tenha executado com sucesso o comando que pediste para executar.  Por exemplo:
     - Os intervalos são um conceito do lado do cliente.  Se a operação chegar ao servidor, o servidor executará o comando mesmo que o cliente desaperte a espera.  
     - Quando ocorre um erro na ligação da tomada, não é possível saber se a operação realmente funcionou no servidor.  Por exemplo, o erro de ligação pode ocorrer após o servidor processar o pedido, mas antes que o cliente receba a resposta.
 *  Como reage a minha candidatura se eu acidentalmente faço a mesma operação duas vezes?  Por exemplo, e se eu aumentar uma inteiro duas vezes em vez de uma?  A minha candidatura está a escrever para a mesma chave de vários lugares?  E se a minha lógica de retry substituir um valor definido por outra parte da minha aplicação?

Se quiser testar como funciona o seu código em condições de erro, considere utilizar a [função Reboot](cache-administration.md#reboot). Reiniciar permite-lhe ver como os blips de ligação afetam a sua aplicação.

## <a name="performance-testing"></a>Teste de desempenho
 * **Comece por `redis-benchmark.exe` usar** para obter uma sensação de possível entrada/latência antes de escrever os seus próprios testes perf.  A documentação de referência redis pode ser [encontrada aqui](https://redis.io/topics/benchmarks).  Note que o redis-benchmark não suporta TLS, por isso terá de [ativar a porta Não-TLS através do Portal](cache-configure.md#access-ports) antes de executar o teste.  [Uma versão compatível com janelas de redis-benchmark.exe pode ser encontrada aqui](https://github.com/MSOpenTech/redis/releases)
 * O VM cliente utilizado para testes deve estar **na mesma região que a** sua instância redis cache.
 * **Recomendamos a utilização da Série VM Dv2** para o seu cliente, uma vez que têm melhor hardware e darão os melhores resultados.
 * Certifique-se de que o VM do cliente que utiliza tem **pelo menos tanto computação e largura* de banda como a cache que está a ser testada. 
 * **Ative o VRSS** na máquina do cliente se estiver no Windows.  [Consulte aqui para mais detalhes.](https://technet.microsoft.com/library/dn383582(v=ws.11).aspx)  Exemplo de script powershell:
     >PowerShell -ExecutionPolicy Sem restrições activa-netadapterRSS -Name (Get-NetAdapter). Nome 
     
 * **Considere utilizar instâncias Redis de nível Premium**.  Estes tamanhos de cache terão melhor latência e entrada de rede porque estão a executar um melhor hardware tanto para cpu como para Network.
 
     > [!NOTE]
     > Os nossos resultados de desempenho observados são [publicados aqui](cache-faq.md#azure-cache-for-redis-performance) para a sua referência.   Além disso, esteja ciente de que o SSL/TLS adiciona algumas despesas gerais, para que possa obter diferentes latenciências e/ou entradas se estiver a usar encriptação de transporte.
 
### <a name="redis-benchmark-examples"></a>Exemplos redis-benchmark
**Pré-teste configuração**: Prepare a instância de cache com os dados necessários para os comandos de teste de latência e de compostagem listados abaixo.
> redis-benchmark.exe -h yourcache.redis.cache.windows.net -a yourAccesskey -t SET -n 10-d 1024 

**Para testar a latência**: Teste os pedidos GET utilizando uma carga útil de 1k.
> redis-benchmark.exe -h yourcache.redis.cache.windows.net -a yourAccesskey -t GET-d 1024 -P 50 -c 4

**Para testar a entrada:** Pedidos de GET pipelined com carga útil de 1k.
> redis-benchmark.exe -h yourcache.redis.cache.windows.net -a yourAccesskey -t GET -n 1000000 -d 1024 -P 50 -c 50
