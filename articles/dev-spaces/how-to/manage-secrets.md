---
title: Como gerir segredos ao trabalhar com um Azure Dev Space
services: azure-dev-spaces
ms.date: 12/03/2019
ms.topic: conceptual
description: Aprenda a usar segredos kubernetes em execução ou construa tempo ao desenvolver aplicações com Azure Dev Spaces
keywords: Docker, Kubernetes, Azure, AKS, Azure Container Service, contentores
ms.custom: devx-track-js
ms.openlocfilehash: 8791480f420dfd76d5291ce82e8ebf7412a41326
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "91972973"
---
# <a name="how-to-manage-secrets-when-working-with-an-azure-dev-space"></a>Como gerir segredos ao trabalhar com um Azure Dev Space

[!INCLUDE [Azure Dev Spaces deprecation](../../../includes/dev-spaces-deprecation.md)]

Os seus serviços podem necessitar de certas palavras-passe, cadeias de ligação e outros segredos, tais como bases de dados ou outros serviços seguros da Azure. Ao definir os valores destes segredos em ficheiros de configuração, pode disponibilizá-los no seu código como variáveis ambientais.  Estes ficheiros de configuração devem ser tratados com cuidado para evitar comprometer a segurança dos segredos.

## <a name="storing-and-using-runtime-secrets"></a>Armazenar e usar segredos de tempo de execução

A Azure Dev Spaces oferece duas opções recomendadas e simplificadas para armazenar segredos em gráficos Helm gerados pela ferramenta do cliente Azure Dev Spaces: no `values.dev.yaml` ficheiro, e inline diretamente em `azds.yaml` . Não é recomendado guardar segredos `values.yaml` em.

> [!NOTE]
> As seguintes abordagens mostram-lhe como armazenar e usar segredos para gráficos Helm gerados pela ferramenta do cliente. Se criar o seu próprio gráfico Helm, pode usar a tabela Helm diretamente para gerir e armazenar segredos.

### <a name="using-valuesdevyaml"></a>Usando valores.dev.yaml

Num projeto que já preparou com a Azure Dev Spaces, crie um `values.dev.yaml` ficheiro na mesma pasta que para definir as suas chaves e `azds.yaml` valores secretos. Por exemplo:

```yaml
secrets:
  redis:
    port: "6380"
    host: "contosodevredis.redis.cache.windows.net"
    key: "secretkeyhere"
```

Verifique as referências de `azds.yaml` `values.dev.yaml` ficheiros como opcionais utilizando um `?` . Por exemplo:

```yaml
install:
  values:
  - values.dev.yaml?
  - secrets.dev.yaml?
```

Se tiver ficheiros secretos adicionais, pode adicioná-los aqui também.

Atualize ou verifique as referências do seu serviço como variáveis ambientais. Por exemplo:

```javascript
var redisPort = process.env.REDIS_PORT
var host = process.env.REDIS_HOST
var theKey = process.env.REDIS_KEY
```
    
Execute os seus serviços atualizados utilizando `azds up` .

```console
azds up
```
 
Use `kubectl` para verificar se os seus segredos foram criados.

```console
kubectl get secret --namespace default -o yaml 
```

> [!IMPORTANT]
> Não é recomendado guardar segredos no controlo de fontes. Se utilizar o Git, adicione `values.dev.yaml` ao ficheiro para evitar cometer `.gitignore` segredos no controlo de fontes.

### <a name="using-azdsyaml"></a>Usando azds.yaml

Num projeto que já preparou com a Azure Dev Spaces, adicione chaves secretas e valor utilizando *$PLACEHOLDER* sintaxe em *configurações.develop.install.set* in `azds.yaml` . Por exemplo:

```yaml
configurations:
  develop:
    ...
    install:
      set:
        secrets:
          redis:
            port: "$REDIS_PORT"
            host: "$REDIS_HOST"
            key: "$REDIS_KEY"
```

> [!NOTE]
> Pode introduzir valores secretos diretamente sem utilizar *$PLACEHOLDER* sintaxe em `azds.yaml` . No entanto, esta abordagem não é recomendada uma vez `azds.yaml` que é armazenada no controlo de fontes.
     
Crie um `.env` ficheiro na mesma pasta que para definir os `azds.yaml` valores *$PLACEHOLDER.* Por exemplo:

```
REDIS_PORT=3333
REDIS_HOST=myredishost
REDIS_KEY=myrediskey
```

> [!IMPORTANT]
> Não é recomendado guardar segredos no controlo de fontes. Se utilizar o Git, adicione `.env` ao ficheiro para evitar cometer `.gitignore` segredos no controlo de fontes.

Atualize ou verifique as referências do seu serviço como variáveis ambientais. Por exemplo:

```javascript
var redisPort = process.env.REDIS_PORT
var host = process.env.REDIS_HOST
var theKey = process.env.REDIS_KEY
```
    
Execute os seus serviços atualizados utilizando `azds up` .

```console
azds up
```
 
Use `kubectl` para verificar se os seus segredos foram criados.

```console
kubectl get secret --namespace default -o yaml 
```

## <a name="using-secrets-as-build-arguments"></a>Usando segredos como argumentos de construção

A secção anterior mostrou como armazenar e usar segredos para usar no tempo de funcionação do contentor. Também pode utilizar qualquer segredo no tempo de construção de contentores, como uma palavra-passe para um NuGet privado, utilizando `azds.yaml` .

Em `azds.yaml` , definir os segredos do tempo de construção em *configurações.develop.build.args* usando a `<variable name>: ${secret.<secret name>.<secret key>}` sintaxe. Por exemplo:

```yaml
configurations:
  develop:
    build:
      dockerfile: Dockerfile.develop
      useGitIgnore: true
      args:
        BUILD_CONFIGURATION: ${BUILD_CONFIGURATION:-Debug}
        MYTOKEN: ${secret.mynugetsecret.pattoken}
```

No exemplo acima, *o menugetsecret* é um segredo existente e *pattoken* é uma chave existente.

>[!NOTE]
> Nomes e chaves secretos podem conter o `.` personagem. Use `\` para escapar ao passar `.` segredos como argumentos de construção. Por exemplo, para passar um segredo chamado *foo.bar* com a chave do *símbolo:* `MYTOKEN: ${secret.foo\.bar.token}` . Além disso, os segredos podem ser avaliados com prefixo e texto pós-fix. Por exemplo, `MYURL: eus-${secret.foo\.bar.token}-version1`. Além disso, os segredos disponíveis nos espaços dos pais e avós podem ser passados como argumentos de construção.

No seu Dockerfile, use a diretiva *ARG* para consumir o segredo e use a mesma variável mais tarde no Dockerfile. Por exemplo:

```dockerfile
...
ARG MYTOKEN
...
ARG NUGET_EXTERNAL_FEED_ENDPOINTS="{'endpointCredentials': [{'endpoint':'PRIVATE_NUGET_ENDPOINT', 'password':'${MYTOKEN}'}]}"
...
```

Atualize os serviços em execução no seu cluster com estas alterações. Na linha de comando, executar o comando:

```
azds up
```

## <a name="next-steps"></a>Passos seguintes

Com estes métodos, pode agora ligar-se de forma segura a uma base de dados, um Cache Azure para Redis ou aceder a serviços Azure seguros.
 
