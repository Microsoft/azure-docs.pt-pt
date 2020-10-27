---
title: Remova TLS 1.0 e 1.1 da utilização com cache Azure para Redis
description: Saiba como remover o TLS 1.0 e o 1.1 da sua aplicação ao comunicar com a Azure Cache para Redis
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 10/22/2019
ms.author: yegu
ms.openlocfilehash: fd0e6f893d152259c46ff06e9ec20af54395c5e6
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/26/2020
ms.locfileid: "92545315"
---
# <a name="remove-tls-10-and-11-from-use-with-azure-cache-for-redis"></a>Remova TLS 1.0 e 1.1 da utilização com cache Azure para Redis

Há um impulso a nível da indústria para o uso exclusivo da versão 1.2 ou posterior da Transport Layer Security (TLS). As versões TLS 1.0 e 1.1 são conhecidas por serem suscetíveis a ataques como BEAST e POODLE, e ter outras fraquezas comuns de Vulnerabilidades e Exposições (CVE). Também não suportam os métodos modernos de encriptação e suítes de cifra recomendadas pelas normas de conformidade da Indústria de Cartões de Pagamento (PCI). Este blog de [segurança TLS](https://www.acunetix.com/blog/articles/tls-vulnerabilities-attacks-final-part/) explica algumas destas vulnerabilidades com mais detalhes.

Como parte deste esforço, vamos fazer as seguintes alterações ao Azure Cache para Redis:

* **Fase 1:** Vamos configurar a versão mínima TLS padrão para ser 1.2 para instâncias de cache recém-criadas (anteriormente, era TLS 1.0). As instâncias de cache existentes não serão atualizadas neste momento. Pode ainda utilizar o portal Azure ou outras APIs de gestão para [alterar a versão mínima TLS](cache-configure.md#access-ports) para 1.0 ou 1.1 para retrocompatibilidade, se necessário.
* **Fase 2:** Vamos parar de apoiar os TLS 1.1 e TLS 1.0. Após esta alteração, a sua aplicação deve utilizar o TLS 1.2 ou mais tarde para comunicar com o seu cache. Espera-se que o serviço Azure Cache for Redis esteja disponível enquanto o migramos para suportar apenas TLS 1.2 ou mais tarde.

  > [!NOTE]
  > A fase 2 está provisoriamente prevista para começar não antes de 31 de dezembro de 2020. No entanto, recomendamos vivamente que comece a planear esta mudança agora e atualizar proativamente os clientes para apoiar o TLS 1.2 ou mais tarde. 
  >

Como parte desta mudança, também removeremos suporte para suítes cifras mais antigas que não são seguras. As nossas suítes cifradas suportadas serão restritas às seguintes suites quando a cache estiver configurada com um mínimo de TLS 1.2:

* TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384_P384
* TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256_P256

Este artigo fornece orientações gerais sobre como detetar dependências nestas versões TLS anteriores e removê-las da sua aplicação.

As datas em que estas alterações fazem efeito são:

| Cloud                | Fase 1 Data de Início | Fase 2 Data de Início         |
|----------------------|--------------------|----------------------------|
| Azure (global)       |  13 de janeiro de 2020  | Adiado devido ao COVID-19  |
| Azure Government     |  13 de março de 2020    | Adiado devido ao COVID-19  |
| Azure Alemanha        |  13 de março de 2020    | Adiado devido ao COVID-19  |
| Azure China 21Vianet |  13 de março de 2020    | Adiado devido ao COVID-19  |

> [!NOTE]
> A fase 2 está provisoriamente prevista para começar não antes de 31 de dezembro de 2020. Este artigo será atualizado quando as datas específicas forem definidas.
>

## <a name="check-whether-your-application-is-already-compliant"></a>Verifique se a sua aplicação já está em conformidade

A maneira mais fácil de descobrir se a sua aplicação funcionará com o TLS 1.2 é definir o valor mínimo da **versão TLS** para TLS 1.2 num cache de teste ou de encenação e, em seguida, realizar testes. A definição **de versão Mínima TLS** encontra-se nas [definições Avançadas](cache-configure.md#advanced-settings) da sua instância cache no portal Azure.  Se a aplicação continuar a funcionar como esperado após esta mudança, provavelmente está em conformidade. Poderá ser necessário configurar a biblioteca cliente Redis utilizada pela sua aplicação para ativar o TLS 1.2 para se ligar ao Azure Cache para Redis.

## <a name="configure-your-application-to-use-tls-12"></a>Configure a sua aplicação para utilizar o TLS 1.2

A maioria das aplicações usam bibliotecas de clientes Redis para lidar com a comunicação com os seus caches. Aqui estão as instruções para configurar algumas das bibliotecas de clientes populares, em várias linguagens e enquadramentos de programação, para utilizar o TLS 1.2.

### <a name="net-framework"></a>.NET Framework

Os clientes Redis .NET utilizam a versão TLS mais antiga por padrão no .NET Framework 4.5.2 ou mais cedo, e utilizam a versão TLS mais recente no Quadro 4.6 ou mais tarde. Se estiver a utilizar uma versão mais antiga do Quadro .NET, pode ativar o TLS 1.2 manualmente:

* **StackExchange.Redis:** Desa parte `ssl=true` e na cadeia de `sslprotocols=tls12` ligação.
* **ServiceStack.Redis:** Siga as instruções [ServiceStack.Redis](https://github.com/ServiceStack/ServiceStack.Redis#servicestackredis-ssl-support) e requer serviceStack.Redis v5.6 no mínimo.

### <a name="net-core"></a>.NET Core

Os clientes Redis .NET Core estão por defeito na versão padrão do SISTEMA TLS, que obviamente depende do próprio SISTEMA. 

Dependendo da versão OS e de quaisquer patches que tenham sido aplicados, a versão TLS padrão eficaz pode variar. Embora haja uma fonte de informação sobre isso, [aqui](/dotnet/framework/network-programming/tls#support-for-tls-12) está um artigo para o Windows. 

No entanto, se estiver a utilizar um sistema operativo antigo ou apenas quiser ter a certeza, recomendamos configurar manualmente a versão TLS preferida através do cliente.


### <a name="java"></a>Java

Os clientes Redis Java usam TLS 1.0 na versão Java 6 ou mais cedo. Jedis, Alface e Redisson não podem ligar-se à Cache Azure para Redis se o TLS 1.0 estiver desativado na cache. Atualize a sua estrutura Java para utilizar novas versões TLS.

Para Java 7, os clientes Redis não usam TLS 1.2 por padrão, mas podem ser configurados para isso. O Jedis permite especificar as definições de TLS subjacentes com o seguinte corte de código:

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

Os clientes Alface e Redisson ainda não suportam especificar a versão TLS, por isso quebrarão se a cache aceitar apenas ligações TLS 1.2. As correções para estes clientes estão a ser revistas, por isso verifique com esses pacotes uma versão atualizada com este suporte.

Em Java 8, o TLS 1.2 é utilizado por padrão e não deve exigir atualizações para a configuração do seu cliente na maioria dos casos. Por segurança, teste a sua aplicação.

### <a name="nodejs"></a>Node.js

Node Redis e IORedis utilizam TLS 1.2 por predefinição.

### <a name="php"></a>PHP

#### <a name="predis"></a>Predis
 
* Versões anteriores do PHP 7: Predis suporta apenas TLS 1.0. Estas versões não funcionam com TLS 1.2; tem de atualizar para utilizar o TLS 1.2.
 
* PHP 7.0 a PHP 7.2.1: Predis utiliza apenas TLS 1.0 ou 1.1 por predefinição. Pode utilizar o SÍLvio TLS 1.2. Especificar TLS 1.2 quando criar a instância do cliente:

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

* Versões PHP 7.3 e posterior: Predis utiliza a versão TLS mais recente.

#### <a name="phpredis"></a>PhpRedis

PhpRedis não suporta TLS em nenhuma versão PHP.

### <a name="python"></a>Python

Redis-py utiliza TLS 1.2 por predefinição.

### <a name="go"></a>GO

O Redigo utiliza o TLS 1.2 por predefinição.

## <a name="additional-information"></a>Informações adicionais

- [Como configurar cache Azure para Redis](cache-configure.md)