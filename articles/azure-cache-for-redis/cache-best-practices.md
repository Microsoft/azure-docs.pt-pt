---
title: Melhores práticas para a Cache de Redis do Azure
description: Saiba como utilizar a sua Cache do Azure para Redis com eficiência ao seguir estas melhores práticas.
services: cache
documentationcenter: na
author: joncole
manager: jhubbard
editor: tysonn
ms.assetid: 3e4905e3-89e3-47f7-8cfb-12caf1c6e50e
ms.service: cache
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: cache
ms.workload: tbd
ms.date: 06/21/2019
ms.author: joncole
ms.openlocfilehash: bdc75033e0aa2e401a511789728feef3248d46ad
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67452465"
---
# <a name="best-practices-for-azure-cache-for-redis"></a>Melhores práticas para a Cache de Redis do Azure 
Ao seguir estas melhores práticas, pode ajudar a maximizar o desempenho e a utilização rentável do seu Cache do Azure para a instância de Redis.

## <a name="configuration-and-concepts"></a>Configuração e conceitos
 * **Utilize o escalão Standard ou Premium para sistemas de produção.**  O escalão básico é um sistema de nó único com nenhuma replicação de dados e nenhum SLA. Além disso, utilize, pelo menos, uma cache de C1.  C0 caches destinam-se a cenários de desenvolvimento/teste simples, uma vez que eles têm um núcleo de CPU compartilhado, pouca memória e estão sujeitos a problemas de "vizinho ruidoso".

 * **Lembre-se de que o Redis é um arquivo de dados na memória.**  [Este artigo](https://gist.github.com/JonCole/b6354d92a2d51c141490f10142884ea4#file-whathappenedtomydatainredis-md) descreve alguns cenários em que pode ocorrer perda de dados.

 * **Desenvolver o seu sistema, de modo a que possa tratar blips de ligação** [devido a aplicação de patches e de ativação pós-falha](https://gist.github.com/JonCole/317fe03805d5802e31cfa37e646e419d#file-azureredis-patchingexplained-md).

 * **Configurar o seu [definição reservados maxmemory](cache-configure.md#maxmemory-policy-and-maxmemory-reserved) para melhorar a capacidade de resposta do sistema** sob condições de pressão de memória.  Esta definição é especialmente importante para cargas de trabalho pesadas de escrita ou se está a armazenar valores maiores (100 KB ou mais) no Redis.  Eu seria Recomendamos comece com 10% do tamanho da cache, em seguida, aumentar se tiver cargas pesadas de escrita. Ver [algumas considerações](cache-how-to-troubleshoot.md#considerations-for-memory-reservations) ao selecionar um valor.

 * **Redis funciona melhor com valores menores**, por isso considere talhar dos dados de maiores em várias chaves.  Na [essa discussão de Redis](https://stackoverflow.com/questions/55517224/what-is-the-ideal-value-size-range-for-redis-is-100kb-too-large/), listadas algumas considerações que deve considerar cuidadosamente.  Leia [este artigo](cache-how-to-troubleshoot.md#large-requestresponse-size) para um problema de exemplo que pode ser causado por valores grandes.

 * **Localize a instância da cache e a sua aplicação na mesma região.**  Ligar a uma cache numa região diferente pode significativamente aumentar a latência e reduzir a confiabilidade.  Enquanto pode ligar a partir de fora do Azure, não recomendado *especialmente ao utilizar o Redis como uma cache*.  Se estiver a utilizar o Redis como apenas um arquivo de chave/valor, latência pode não ser a principal preocupação. 

 * **Reutilizar conexões** -criação de novas ligações é cara e aumenta a latência, por isso, reutilizar conexões tanto quanto possíveis. Se optar por criar novas ligações, certifique-se fechar as conexões antigas antes do lançamento (mesmo em linguagens de memória gerenciada como .NET ou Java).

 * **Configurar a sua biblioteca de cliente para utilizar um *tempo limite da ligação* de, pelo menos, 15 segundos**, dando a hora do sistema para se conectar mesmo sob condições de CPU superior.  Um valor de tempo limite de ligação pequeno não garante que a ligação é estabelecida nesse período de tempo.  Se algo entram errado (CPU de cliente elevado, CPU do servidor de elevada etc.), em seguida, um valor de tempo limite de ligação abreviada fará com que a tentativa de ligação falha. Muitas vezes, esse comportamento faz um pior situação ruim.  Em vez de ajuda, tempos limite mais curto aggravate o problema ao aplicar o sistema para reiniciar o processo de tentar restabelecer a ligação, que pode levar a uma *connect -> falha -> repetição* loop. Normalmente, recomendamos que deixe a ligação de tempo limite em 15 segundos ou superior. É melhor permitir que a tentativa de ligação for bem sucedido após 15 ou 20 segundos que fazer com que ele falhar rapidamente apenas para tentar novamente. Esse ciclo de tentativas pode causar a falha durar mais do que se deixar que o sistema apenas está a demorar mais inicialmente.  
     > [!NOTE]
     > Esta orientação é específica para o *tentativa de ligação* e não estão relacionados até ao momento em que está disposto a esperar que um *operação* como GET ou SET para concluir.
 

 * **Evitar comandos caros** -alguns redis operações, como o [comando KEYS](https://redis.io/commands/keys), são *muito* Caro e deve ser evitado.  Para obter mais informações, consulte [algumas considerações em torno de comandos de caras](cache-how-to-troubleshoot.md#expensive-commands)


 
## <a name="memory-management"></a>Gerenciamento de memória
Há várias coisas relacionadas com a utilização de memória na sua instância de servidor Redis que deve considerar.  Aqui estão alguns:

 * **Escolher uma [política de expulsão](https://redis.io/topics/lru-cache) que funciona para a sua aplicação.**  A política predefinida para de Redis do Azure é *volátil lru*, que significa que apenas chaves com um valor de TTL Valor conjunto serão elegível para expulsão.  Se não existem chaves tenham um valor TTL, o sistema não expulsar quaisquer chaves.  Se pretender que o sistema para permitir que qualquer tecla para ser expulsas se sob pressão de memória, então deve considerar a *allkeys lru* política.

 * **Defina um valor de expiração as suas chaves.**  Esta ação irá remover chaves de forma proativa, em vez de aguardar até que haja pressão de memória.  Quando surgir expulsão devido a pressão de memória, isso pode causar a carga adicional no seu servidor.  Para obter mais informações, consulte a documentação para o [expirar](https://redis.io/commands/expire) e [ExpireAt](https://redis.io/commands/expireat) comandos.
 
## <a name="client-library-specific-guidance"></a>Orientações específicas de biblioteca de cliente
 * [StackExchange.Redis (.NET)](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#file-redis-bestpractices-stackexchange-redis-md)
 * [Java - qual cliente devo utilizar?](https://gist.github.com/warrenzhu25/1beb02a09b6afd41dff2c27c53918ce7#file-azure-redis-java-best-practices-md)
 * [Lettuce (Java)](https://gist.github.com/warrenzhu25/181ccac7fa70411f7eb72aff23aa8a6a#file-azure-redis-lettuce-best-practices-md)
 * [Jedis (Java)](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#file-redis-bestpractices-java-jedis-md)
 * [Node.js](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#file-redis-bestpractices-node-js-md)
 * [PHP](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#file-redis-bestpractices-php-md)
 * [Fornecedor de estado de sessão do Asp.Net](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#file-redis-bestpractices-session-state-provider-md)


## <a name="when-is-it-safe-to-retry"></a>Quando é seguro tentar novamente?
Infelizmente, não existe uma resposta fácil.  Cada aplicação tem de decidir quais operações podem ser repetidas e que não é possível.  Cada operação tem requisitos diferentes e dependências entre chaves.  Aqui estão algumas coisas que pode considerar:

 * Pode obter os erros do lado do cliente, mesmo que o Redis executou com êxito o comando solicitado que seja executado.  Por exemplo:
     - Tempos limite é um conceito do lado do cliente.  Se a operação alcançou o servidor, o servidor será executado o comando mesmo se o cliente desiste de espera.  
     - Quando ocorre um erro na ligação de socket, não é possível saber se a operação, na verdade, executado no servidor.  Por exemplo, o erro de ligação pode ocorrer depois do pedido é processado pelo servidor, mas antes da resposta é recebida pelo cliente.
 *  Como meu aplicativo reagir se eu executar acidentalmente a mesma operação duas vezes?  Por exemplo, e se eu incrementar um número inteiro duas vezes em vez de apenas uma vez?  Meu aplicativo escreve para a mesma chave de vários locais?  E se a minha lógica de repetição substitui um valor definido por alguma outra parte do meu aplicativo?

Se desejar testar o funcionamento do seu código sob condições de erro, considere utilizar o [reiniciar funcionalidade](cache-administration.md#reboot). Isto permite-lhe ver como blips de ligação afetam a sua aplicação.

## <a name="performance-testing"></a>Teste de desempenho
 * **Comece usando `redis-benchmark.exe`**  ter uma noção do débito/latência possível antes de escrever seu próprio desempenho testa.  Documentação de referência de redis pode ser [encontrado aqui](http://redis.io/topics/benchmarks).  Observe nesse benchmark de redis não suporta SSL, para que precisará [ativar a porta não SSL através do Portal](cache-configure.md#access-ports) antes de executar o teste.  [Uma versão compatível do windows de redis benchmark.exe pode ser encontrada aqui](https://github.com/MSOpenTech/redis/releases)
 * O cliente VM utilizada para fins de teste deve ser **na mesma região** como a instância da cache de Redis.
 * **Recomendamos que utilize a série de VM Dv2** para o seu cliente tiver hardware melhor e dará os melhores resultados.
 * Certificar-se de que o cliente tem de VM que utiliza * *, pelo menos, tanta computação e a largura de banda* como a cache que está sendo testada. 
 * **Ativar o VRSS** no computador cliente se estiver no Windows.  [Veja aqui para obter detalhes](https://technet.microsoft.com/library/dn383582(v=ws.11).aspx).  Script do powershell de exemplo:
     >PowerShell -ExecutionPolicy Unrestricted Enable-NetAdapterRSS -Name (    Get-NetAdapter).Name 
     
 * **Considere a utilização de instâncias de Redis de escalão Premium**.  Estes tamanhos de cache terão o débito e latência de rede melhor porque está em execução em hardware melhor da CPU e da rede.
 
     > [!NOTE]
     > Nossos resultados observados de desempenho são [publicada aqui](cache-faq.md#azure-cache-for-redis-performance) para sua referência.   Além disso, lembre-se de que o SSL/TLS adiciona alguma sobrecarga, para que talvez tenha latências diferentes e/ou de débito se estiver a utilizar a encriptação de transporte.
 
### <a name="redis-benchmark-examples"></a>Exemplos de Benchmark de redis
**Configuração de pré-teste**: Isto irá preparar a instância da cache com os dados necessários para a latência e débito teste comandos listados abaixo.
> redis benchmark.exe - h yourcache.redis.cache.windows.net - a yourAccesskey -t SET -n - 10d 1024 

**Para testar a latência**: Isto irá testar a solicitações GET com um payload de k 1.
> redis benchmark.exe - h yourcache.redis.cache.windows.net - a yourAccesskey -t obter 50 1024 - P -d -c 4

**Para testar a taxa de transferência:** Esta opção utiliza Pipelined obter pedidos com o payload de 1 k.
> -n 1000000 - d 1024 - P 50 - c 50 de introdução do redis benchmark.exe - h yourcache.redis.cache.windows.net - a yourAccesskey -t
