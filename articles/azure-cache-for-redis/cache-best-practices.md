---
title: Melhores práticas da Cache do Azure para Redis
description: Aprenda a usar o seu Azure Cache para Redis de forma eficaz, seguindo estas boas práticas.
author: joncole
ms.service: cache
ms.topic: conceptual
ms.date: 01/06/2020
ms.author: joncole
ms.openlocfilehash: 84a6bba390b0f6b101bd8243cf47b79af9618999
ms.sourcegitcommit: 956dec4650e551bdede45d96507c95ecd7a01ec9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/09/2021
ms.locfileid: "102521650"
---
# <a name="best-practices-for-azure-cache-for-redis"></a>Melhores práticas da Cache do Azure para Redis 
Ao seguir estas boas práticas, pode ajudar a maximizar o desempenho e a utilização rentável da sua Cache Azure para a instância Redis.

## <a name="configuration-and-concepts"></a>Configuração e conceitos
 * **Utilize o nível Standard ou Premium para sistemas de produção.**  O nível Básico é um único sistema de nó sem replicação de dados e sem SLA. Além disso, utilize pelo menos uma cache C1.  Os caches C0 são destinados a cenários simples dev/teste, uma vez que têm um núcleo de CPU partilhado, pouca memória, e são propensos a problemas de "vizinho barulhento".

 * **Lembre-se que Redis é uma loja de dados na memória.**  [Este artigo](cache-troubleshoot-data-loss.md) descreve alguns cenários onde a perda de dados pode ocorrer.

 * **Desenvolva o seu sistema de modo a que possa lidar com os blips** de ligação [devido à remendação e ao failover](cache-failover.md).

 * **Configure a sua [definição reservada ao maxmemory](cache-configure.md#maxmemory-policy-and-maxmemory-reserved) para melhorar a capacidade de resposta do sistema** em condições de pressão de memória.  Uma definição de reserva suficiente é especialmente importante para cargas de trabalho pesadas ou se estiver a armazenar valores maiores (100 KB ou mais) em Redis. Deve começar com 10% do tamanho do seu cache e aumentar esta percentagem se tiver cargas pesadas.

 * **O Redis funciona melhor com valores mais pequenos,** por isso considere cortar dados maiores em várias teclas.  [Nesta discussão redis,](https://stackoverflow.com/questions/55517224/what-is-the-ideal-value-size-range-for-redis-is-100kb-too-large/)algumas considerações estão listadas que deve considerar cuidadosamente.  Leia [este artigo](cache-troubleshoot-client.md#large-request-or-response-size) para um problema de exemplo que pode ser causado por grandes valores.

 * **Localize o seu cache e a sua aplicação na mesma região.**  Estabelecer ligação a uma cache numa região diferente pode aumentar significativamente a latência e reduzir a fiabilidade.  Embora possa ligar-se do exterior do Azure, não é recomendado *especialmente quando se utiliza o Redis como cache*.  Se estiver a usar o Redis como apenas uma loja chave/valor, a latência pode não ser a principal preocupação. 

 * **Reutilizar as ligações.**  Criar novas ligações é dispendioso e aumenta a latência, por isso reutilizar as ligações o máximo possível. Se optar por criar novas ligações, certifique-se de fechar as ligações antigas antes de as libertar (mesmo em idiomas de memória geridos como .NET ou Java).

* **Use pipelining.**  Tente escolher um cliente Redis que suporte [a pipelining Redis](https://redis.io/topics/pipelining) de forma a fazer o uso mais eficiente da rede para obter o melhor rendimento possível.

 * **Configure a biblioteca do seu cliente para utilizar um tempo de *ligação* de pelo menos 15 segundos,** dando ao sistema tempo para se ligar mesmo em condições mais elevadas de CPU.  Um pequeno valor de tempo de ligação não garante que a ligação seja estabelecida nesse período de tempo.  Se algo correr mal (CPU de cliente alto, CPU de servidor alto, e assim por diante), então um curto valor de tempo de ligação causará a tentativa de falha da ligação. Este comportamento muitas vezes piora uma situação má.  Em vez de ajudar, os intervalos mais curtos agravam o problema forçando o sistema a reiniciar o processo de tentar reconectar-se, o que pode levar a um ciclo *de reluto -> de ligação - >.* Recomendamos geralmente que deixe a sua ligação Timeout a 15 segundos ou mais. É melhor deixar a sua tentativa de ligação ter sucesso após 15 ou 20 segundos do que fazê-la falhar rapidamente apenas para tentar novamente. Este ciclo de retíria pode fazer com que a sua paragem dure mais tempo do que se deixar o sistema demorar mais tempo inicialmente.  
     > [!NOTE]
     > Esta orientação é específica para a *tentativa de ligação* e não está relacionada com o tempo que você está disposto a esperar por uma *operação* como GET ou SET para completar.

 * **Evite operações dispendiosas** - Algumas operações redis, como o comando [KEYS,](https://redis.io/commands/keys) são *muito* caras e devem ser evitadas.  Para mais informações, consulte algumas considerações em torno [de comandos de longa duração](cache-troubleshoot-server.md#long-running-commands)

 * **Utilização da encriptação TLS** - Azure Cache for Redis requer comunicações encriptadas TLS por padrão.  As versões TLS 1.0, 1.1 e 1.2 estão atualmente suportadas.  No entanto, os TLS 1.0 e 1.1 estão num caminho para a depreciação em toda a indústria, pelo que utilize o TLS 1.2, se possível.  Se a biblioteca ou ferramenta do seu cliente não suportar TLS, então permitir ligações não encriptadas pode ser feita [através do portal Azure](cache-configure.md#access-ports) ou [APIs de gestão](/rest/api/redis/redis/update).  Nos casos em que as ligações encriptadas não são possíveis, seria recomendado colocar a sua cache e a sua aplicação de clientes numa rede virtual.  Para obter mais informações sobre quais portas são utilizadas no cenário de cache de rede virtual, consulte esta [tabela](cache-how-to-premium-vnet.md#outbound-port-requirements).

 * **Tempo de mente -** Azure Redis tem atualmente um tempo de 10 minutos para ligações, pelo que este deve ser programado para menos de 10 minutos.

## <a name="memory-management"></a>Gestão da memória
Existem várias coisas relacionadas com o uso da memória dentro do seu servidor Redis que talvez queira considerar.  Aqui estão alguns:

 * **Escolha uma [política de despejo](https://redis.io/topics/lru-cache) que funcione para a sua candidatura.**  A política de incumprimento para Azure Redis é *volátil-lru,* o que significa que apenas as chaves que têm um conjunto de valor TTL serão elegíveis para despejo.  Se nenhuma tecla tiver um valor TTL, então o sistema não despeja nenhuma tecla.  Se pretender que o sistema permita que qualquer chave seja despejada se estiver sob pressão de memória, então talvez deva considerar a política *allkeys-lru.*

 * **Desaça um valor de validade nas suas chaves.**  Uma expiração removerá as chaves proactivamente em vez de esperar até que haja pressão de memória.  Quando o despejo faz efeito devido à pressão da memória, pode causar carga adicional no seu servidor.  Para obter mais informações, consulte a documentação dos comandos [EXPIRE](https://redis.io/commands/expire) e [EXPIREAT.](https://redis.io/commands/expireat)

## <a name="client-library-specific-guidance"></a>Orientação específica da biblioteca do cliente
 * [StackExchange.Redis (.NET)](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#file-redis-bestpractices-stackexchange-redis-md)
 * [Java - Que cliente devo usar?](https://gist.github.com/warrenzhu25/1beb02a09b6afd41dff2c27c53918ce7#file-azure-redis-java-best-practices-md)
 * [Alface (Java)](https://github.com/Azure/AzureCacheForRedis/blob/main/Lettuce%20Best%20Practices.md)
 * [Jedis (Java)](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#file-redis-bestpractices-java-jedis-md)
 * [Node.js](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#file-redis-bestpractices-node-js-md)
 * [PHP](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#file-redis-bestpractices-php-md)
 * [Provedor do Estado da sessão de ASP.NET](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#file-redis-bestpractices-session-state-provider-md)


## <a name="when-is-it-safe-to-retry"></a>Quando é seguro voltar a tentar?
Infelizmente, não há uma resposta fácil.  Cada aplicação tem de decidir que operações podem ser novamente julgadas e quais não podem.  Cada operação tem diferentes requisitos e dependências inter-chave.  Eis algumas coisas que podem considerar:

 * Podes obter erros do lado do cliente, apesar do Redis ter executado com sucesso o comando que pediste para executar.  Por exemplo:
    - Os intervalos são um conceito do lado do cliente.  Se a operação chegar ao servidor, o servidor executará o comando mesmo que o cliente desasse de esperar.  
    - Quando ocorre um erro na ligação da tomada, não é possível saber se a operação realmente funcionou no servidor.  Por exemplo, o erro de ligação pode ocorrer após o servidor processar o pedido, mas antes que o cliente receba a resposta.
 * Como reage a minha candidatura se eu acidentalmente executar a mesma operação duas vezes?  Por exemplo, e se eu incrementar um inteiro duas vezes em vez de uma?  A minha candidatura está a escrever para a mesma chave de vários lugares?  E se a minha lógica de repreensão substituir um valor definido por outra parte da minha app?

Se quiser testar como o seu código funciona em condições de erro, considere utilizar a [função 'Reiniciar'.](cache-administration.md#reboot) O reboot permite-lhe ver como os blips de ligação afetam a sua aplicação.

## <a name="performance-testing"></a>Teste de desempenho
 * **Comece por `redis-benchmark.exe` usar** para obter uma sensação de possível produção/latência antes de escrever os seus próprios testes perf.  A documentação de referência do Redis pode ser [consultada aqui.](https://redis.io/topics/benchmarks)  Note que o redis-benchmark não suporta TLS, por isso terá de [ativar a porta Não-TLS através do Portal](cache-configure.md#access-ports) antes de executar o teste.  [Uma versão compatível com janelas de redis-benchmark.exe pode ser encontrada aqui](https://github.com/MSOpenTech/redis/releases)
 * O VM do cliente utilizado para testes deve estar **na mesma região que a** sua cache Redis.
 * **Recomendamos a utilização de Série VM Dv2** para o seu cliente, uma vez que têm melhor hardware e darão os melhores resultados.
 * Certifique-se de que o VM do cliente que utiliza tem pelo *menos tanto cálculo e largura de banda* como a cache que está a ser testada. 
 * **Teste em condições de failover** na sua cache. É importante garantir que não testa o seu cache apenas em condições de estado constantes. Teste também em condições de failover e meça a carga CPU/Servidor na sua cache durante esse tempo. Pode iniciar uma falha [reiniciando o nó primário](cache-administration.md#reboot). Isto permitir-lhe-á ver como a sua aplicação se comporta em termos de produção e latência durante as condições de failover (acontece durante as atualizações e pode acontecer durante um evento não planeado). Idealmente, não quer ver o pico de carga do CPU / Servidor a mais de 80% mesmo durante uma falha, pois isso pode afetar o desempenho.
 * **Alguns tamanhos de cache** são hospedados em VMs com 4 ou mais núcleos. Isto é útil para distribuir a encriptação/desencriptação TLS, bem como cargas de trabalho de ligação /desconexão TLS em vários núcleos para reduzir o uso geral do CPU nos VMs de cache.  [Consulte aqui para mais detalhes sobre tamanhos e núcleos VM](cache-planning-faq.md#azure-cache-for-redis-performance)
 * **Ativar o VRSS** na máquina do cliente se estiver no Windows.  [Consulte aqui para mais detalhes.](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn383582(v=ws.11))  Exemplo de script do PowerShell:
     >PowerShell -ExecutionPolicy Unrestricted Enable-NetAdapterRSS -Name (Get-NetAdapter). Nome 

 * **Considere utilizar instâncias De nível Premium Redis**.  Estes tamanhos de cache terão melhor latência e produção de rede porque estão a executar hardware melhor tanto para CPU como para a Rede.

     > [!NOTE]
     > Os nossos resultados de desempenho observados são [publicados aqui](cache-planning-faq.md#azure-cache-for-redis-performance) para a sua referência.   Além disso, esteja ciente de que o SSL/TLS adiciona algumas despesas gerais, para que possa obter latências e/ou produção diferentes se estiver a usar encriptação de transporte.

### <a name="redis-benchmark-examples"></a>Redis-Benchmark exemplos
**Configuração pré-teste**: Prepare a instância de cache com os dados necessários para os comandos de teste de latência e de produção listados abaixo.
> redis-benchmark -h yourcache.redis.cache.windows.net -a yourAccesskey -t SET -n 10 -d 1024 

**Para testar a latência**: Teste os pedidos GET utilizando uma carga útil de 1k.
> redis-benchmark -h yourcache.redis.cache.windows.net -a yourAccesskey -t GET -d 1024 -P 50 -c 4

**Para testar a produção:** Solicitações PIPELINED GET com carga útil de 1k.
> redis-benchmark -h yourcache.redis.cache.windows.net -a yourAccesskey -t GET -n 1000000 -d 1024 -P 50 -c 50
