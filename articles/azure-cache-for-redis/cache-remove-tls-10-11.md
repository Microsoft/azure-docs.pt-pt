---
title: Remova TLS 1.0 e 1.1 da utilização com Azure Cache for Redis
description: Saiba como remover TLS 1.0 e 1.1 da sua aplicação ao comunicar com Azure Cache for Redis
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 10/22/2019
ms.author: yegu
ms.openlocfilehash: 6130c934f9a718baab840dae714222e4153bfcf6
ms.sourcegitcommit: f97d3d1faf56fb80e5f901cd82c02189f95b3486
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/11/2020
ms.locfileid: "79126354"
---
# <a name="remove-tls-10-and-11-from-use-with-azure-cache-for-redis"></a>Remova TLS 1.0 e 1.1 da utilização com Azure Cache for Redis

Há um impulso em toda a indústria para o uso exclusivo da versão 1.2 (TLS) de Transporte layer security ou posterior. As versões TLS 1.0 e 1.1 são conhecidas por serem suscetíveis a ataques como BEAST e POODLE, e ter outras fraquezas comuns de vulnerabilidades e exposições (CVE). Também não suportam os métodos modernos de encriptação e as suítes cifra recomendadas pelas normas de conformidade da Indústria de Cartões de Pagamento (PCI). Este blog de [segurança TLS](https://www.acunetix.com/blog/articles/tls-vulnerabilities-attacks-final-part/) explica algumas destas vulnerabilidades com mais detalhes.

Como parte deste esforço, vamos fazer as seguintes alterações ao Azure Cache para redis:

* **Fase 1:** Vamos configurar a versão TLS mínima padrão para ser 1.2 para casos de cache recém-criados. (Isto costumava ser TLS 1.0.) As instâncias de cache existentes não serão atualizadas neste momento. Poderá alterar a [versão TLS mínima](cache-configure.md#access-ports) para 1.0 ou 1.1 para retrocompatibilidade, se necessário. Esta alteração pode ser feita através do portal Azure ou de outras APIs de gestão.
* **Fase 2:** Vamos parar de suportar as versões TLS 1.0 e 1.1. Após esta alteração, a sua aplicação será obrigada a utilizar TLS 1.2 ou mais tarde para comunicar com a sua cache.

Além disso, como parte desta mudança, vamos remover o suporte para suites cítropeçares mais antigas e inseguras.  As nossas suítes cypher suportadas serão restritas ao seguinte quando a cache estiver configurada com uma versão TLS mínima de 1.2.

* TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384_P384
* TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256_P256

Este artigo fornece orientações gerais sobre como detetar dependências destas versões TLS anteriores e removê-las da sua aplicação.

As datas em que estas alterações entrem em vigor são:

| Cloud               | Data de início da fase 1 | Data de início da fase 2 |
|---------------------|--------------------|--------------------|
| Azure (global)      |  13 de janeiro de 2020  | 31 de março de 2020     |
| Azure Government    |  13 de março de 2020    | 11 de maio de 2020       |
| Azure Alemanha       |  13 de março de 2020    | 11 de maio de 2020       |
| Azure China         |  13 de março de 2020    | 11 de maio de 2020       |

## <a name="check-whether-your-application-is-already-compliant"></a>Verifique se a sua aplicação já está em conformidade

A maneira mais fácil de saber se a sua aplicação funcionará com TLS 1.2 é definir o valor mínimo da **versão TLS** para TLS 1.2 num teste ou cache de paragem que utiliza. A definição mínima de **versão TLS** encontra-se nas [definições avançadas](cache-configure.md#advanced-settings) da sua instância de cache no portal Azure. Se a aplicação continuar a funcionar como esperado após esta mudança, é provavelmente compatível. Pode ser necessário configurar algumas bibliotecas de clientes Redis utilizadas pela sua aplicação especificamente para ativar o TLS 1.2, para que possam ligar-se ao Azure Cache for Redis sobre esse protocolo de segurança.

## <a name="configure-your-application-to-use-tls-12"></a>Configure a sua aplicação para utilizar TLS 1.2

A maioria das aplicações usa bibliotecas de clientes Redis para lidar com a comunicação com os seus caches. Aqui estão instruções para configurar algumas das bibliotecas populares de clientes, em várias linguagens e quadros de programação, para usar TLS 1.2.

### <a name="net-framework"></a>.NET Framework

Os clientes Redis .NET utilizam a versão TLS mais antiga por defeito no .NET Framework 4.5.2 ou anterior, e utilizam a versão TLS mais recente no .NET Framework 4.6 ou posterior. Se estiver a utilizar uma versão mais antiga do .NET Framework, pode ativar manualmente o TLS 1.2:

* **StackExchange.Redis:** Coloque `ssl=true` e `sslprotocols=tls12` na corda de ligação.
* **ServiceStack.Redis:** Siga as [instruções ServiceStack.Redis](https://github.com/ServiceStack/ServiceStack.Redis/pull/247).

### <a name="net-core"></a>.NET Core

Os clientes Redis .NET Core utilizam a versão TLS mais recente por padrão.

### <a name="java"></a>Java

Os clientes Redis Java usam TLS 1.0 na versão Java 6 ou anterior. Jedis, Alface e Redisson não podem ligar-se ao Azure Cache for Redis se o TLS 1.0 estiver desativado na cache. Atualize a sua estrutura Java para utilizar novas versões TLS.

Para java 7, os clientes Redis não usam TLS 1.2 por padrão, mas podem ser configurados para o mesmo. O Jedis permite especificar as definições de TLS subjacentes com o seguinte código snippet:

``` Java
SSLSocketFactory sslSocketFactory = (SSLSocketFactory) SSLSocketFactory.getDefault();
SSLParameters sslParameters = new SSLParameters();
sslParameters.setEndpointIdentificationAlgorithm("HTTPS");
sslParameters.setProtocols(new String[]{"TLSv1.2"});
 
URI uri = URI.create("rediss://host:port");
JedisShardInfo shardInfo = new JedisShardInfo(uri, sslSocketFactory, sslParameters, null);
 
shardInfo.setPassword("cachePassword");
 
Jedis jedis = new Jedis(shardInfo);
```

Os clientes de Alface e Redisson ainda não suportam especificar a versão TLS, pelo que quebrarão se a cache aceitar apenas ligações TLS 1.2. As correções para estes clientes estão a ser revistas, por isso verifique com esses pacotes uma versão atualizada com este suporte.

Em Java 8, o TLS 1.2 é utilizado por padrão e não deve exigir atualizações para a configuração do seu cliente na maioria dos casos. Para estar seguro, teste a sua candidatura.

### <a name="nodejs"></a>Node.js

O nó Redis e o IORedis usam o TLS 1.2 por defeito.

### <a name="php"></a>PHP

#### <a name="predis"></a>Predis
 
* Versões anteriores a PHP 7: Predis suporta apenas TLS 1.0. Estas versões não funcionam com TLS 1.2; tem de fazer o upgrade para utilizar o TLS 1.2.
 
* PHP 7.0 a PHP 7.2.1: Predis utiliza apenas TLS 1.0 ou 1.1 por padrão. Pode utilizar a seguinte sucinta para utilizar o TLS 1.2. Especifique tLS 1.2 quando criar a instância do cliente:

  ``` PHP
  $redis=newPredis\Client([
      'scheme'=>'tls',
      'host'=>'host',
      'port'=>6380,
      'password'=>'password',
      'ssl'=>[
          'crypto_type'=>STREAM_CRYPTO_METHOD_TLSv1_2_CLIENT,
      ],
  ]);
  ```

* PHP 7.3 e versões posteriores: Predis usa a versão TLS mais recente.

#### <a name="phpredis"></a>PhpRedis

PhpRedis não suporta TLS em qualquer versão PHP.

### <a name="python"></a>Python

Redis-py utiliza TLS 1.2 por padrão.

### <a name="go"></a>GO

Redigo utiliza TLS 1.2 por padrão.

## <a name="additional-information"></a>Informações adicionais

- [Como configurar o Cache Azure para redis](cache-configure.md)
