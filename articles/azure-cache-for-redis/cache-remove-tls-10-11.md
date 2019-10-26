---
title: Remover o uso de TLS 1,0 e 1,1 com o cache do Azure para Redis | Microsoft Docs
description: Saiba como remover o TLS 1,0 e 1,1 do seu aplicativo ao se comunicar com o cache do Azure para Redis
services: cache
documentationcenter: ''
author: yegu-ms
manager: maiye
editor: ''
ms.assetid: ''
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache
ms.devlang: na
ms.topic: article
ms.date: 10/22/2019
ms.author: yegu
ms.openlocfilehash: d1d44467d310b87d306ea7401e66784d684a1bf3
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/24/2019
ms.locfileid: "72901901"
---
# <a name="remove-use-of-tls-10-and-11-with-azure-cache-for-redis"></a>Remover o uso de TLS 1,0 e 1,1 com o cache do Azure para Redis

Há um push em todo o setor para usar o TLS 1,2 ou superior exclusivamente. As versões 1,0 e 1,1 do TLS são conhecidas como suscetíveis a ataques como fera e POODLE e têm outros pontos fracos de vulnerabilidades e exposições (CVE). Eles também não dão suporte aos métodos de criptografia modernos e conjuntos de codificação recomendados por padrões de conformidade de PCI. Este [blog de segurança do TLS](https://www.acunetix.com/blog/articles/tls-vulnerabilities-attacks-final-part/) explica algumas dessas vulnerabilidades em mais detalhes.

Embora nenhuma delas apresente problemas imediatos, você deve considerar a possibilidade de se afastar do uso de TLS 1,0 e 1,1 o mais cedo possível. O cache do Azure para Redis deixará de oferecer suporte a essas versões de TLS a partir de 31 de março de 2020. Seu aplicativo será solicitado a usar pelo menos o TLS 1,2 para se comunicar com o cache após essa data.

Este artigo fornece diretrizes gerais sobre como detectar e remover essas dependências do seu aplicativo.

## <a name="check-if-your-application-is-already-compliant"></a>Verifique se seu aplicativo já está em conformidade

A maneira mais fácil de descobrir se seu aplicativo funcionará com o TLS 1,2 é definir a versão mínima do TLS em um cache de teste ou de preparo que ele usa para TLS 1,2. Você pode encontrar a configuração de versão mínima do TLS nas [Configurações avançadas](cache-configure.md#advanced-settings) de sua instância de cache no portal do Azure. Se o aplicativo continuar a funcionar conforme o esperado após essa alteração, provavelmente será compatível. Algumas bibliotecas de cliente Redis usadas pelo nosso aplicativo talvez precisem ser especificamente configuradas para habilitar o TLS 1,2 para se conectarem ao cache do Azure para Redis sobre esse protocolo de segurança.

## <a name="configure-your-application-to-use-tls-12"></a>Configurar seu aplicativo para usar o TLS 1,2

A maioria dos aplicativos utiliza bibliotecas de cliente do Redis para lidar com a comunicação com seus caches. Veja abaixo as instruções sobre como configurar algumas das bibliotecas de cliente populares em várias linguagens de programação e estruturas para usar o TLS 1,2.

### <a name="net-framework"></a>.NET Framework

Os clientes do Redis .NET usam a versão mais baixa do TLS por padrão em .NET Framework 4.5.2 ou abaixo e a versão mais alta do TLS em 4,6 ou superior. Se você estiver usando uma versão mais antiga do .NET Framework, poderá habilitar o TLS 1,2 manualmente:

* StackExchange. Redis: defina `ssl=true` e `sslprotocls=tls12` na cadeia de conexão.
* Perstack. Redis: Siga [estas instruções](https://github.com/ServiceStack/ServiceStack.Redis/pull/247).

### <a name="net-core"></a>.NET Core

Os clientes do Redis .NET Core usam a versão mais alta do TLS por padrão.

### <a name="java"></a>Java

Os clientes Redis Java usam o TLS 1,0 no Java versão 6 ou inferior. Jedis, lettuce e Radisson não poderão se conectar ao cache do Azure para Redis se o TLS 1,0 estiver desabilitado no cache. Não há nenhuma solução alternativa conhecida no momento.

No Java 7 ou superior, os clientes Redis não usam o TLS 1,2 por padrão, mas podem ser configurados para ele. Lettuce e Radisson não dão suporte a isso no momento. Eles serão interrompidos se o cache aceitar apenas conexões TLS 1,2. Jedis permite que você especifique as configurações de TLS subjacentes com o seguinte trecho de código:

``` Java
SSLSocketFactory sslSocketFactory = (SSLSocketFactory) SSLSocketFactory.getDefault();
SSLParameters sslParameters = new SSLParameters();
sslParameters.setEndpointIdentificationAlgorithm("HTTPS");
sslParameters.setProtocols(new String[]{"TLSv1", "TLSv1.1", "TLSv1.2"});
 
URI uri = URI.create("rediss://host:port");
JedisShardInfo shardInfo = new JedisShardInfo(uri, sslSocketFactory, sslParameters, null);
 
shardInfo.setPassword("cachePassword");
 
Jedis jedis = new Jedis(shardInfo);
```

### <a name="nodejs"></a>Node.js

O node Redis e o IORedis usam TLS 1,2 por padrão.

### <a name="php"></a>PHP

Predis no PHP 7 não funcionará, pois o último só dá suporte a TLS 1,0. No PHP 7.2.1 ou abaixo, o Predis usa TLS 1,0 ou 1,1 por padrão. Você pode especificar o TLS 1,2 ao instanciar o cliente:

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

No PHP 7,3 ou superior, o Predis usa a versão mais recente do TLS.

PhpRedis não dá suporte a TLS em nenhuma versão do PHP.

### <a name="python"></a>Python

Redis-py usa TLS 1,2 por padrão.

### <a name="go"></a>GO

O RediGO usa TLS 1,2 por padrão.

## <a name="additional-information"></a>Informação adicional

- [Como configurar o cache do Azure para Redis](cache-configure.md)
