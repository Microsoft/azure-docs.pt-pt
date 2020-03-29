---
title: Como gerir segredos ao trabalhar com um Espaço Azure Dev
services: azure-dev-spaces
ms.date: 12/03/2019
ms.topic: conceptual
description: Saiba como usar os segredos da Kubernetes em executar ou construir tempo ao desenvolver aplicações com a Azure Dev Spaces
keywords: Docker, Kubernetes, Azure, AKS, Serviço de Contentores Azure, contentores
ms.openlocfilehash: d9dd0de348612bbb3baf5fb351c1c9af1c228c1f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75438467"
---
# <a name="how-to-manage-secrets-when-working-with-an-azure-dev-space"></a>Como gerir segredos ao trabalhar com um Espaço Azure Dev

Os seus serviços podem exigir certas palavras-passe, cordas de ligação e outros segredos, tais como bases de dados ou outros serviços azure seguros. Ao definir os valores destes segredos em ficheiros de configuração, pode disponibilizá-los no seu código como variáveis ambientais.  Estes ficheiros de configuração devem ser tratados com cuidado para evitar comprometer a segurança dos segredos.

## <a name="storing-and-using-runtime-secrets"></a>Armazenar e usar segredos de tempo de execução

A Azure Dev Spaces oferece duas opções recomendadas e simplificadas para armazenar segredos em `values.dev.yaml` gráficos Helm gerados `azds.yaml`pela ferramenta de cliente do Azure Dev Spaces: no ficheiro e inline diretamente em . Não é recomendado guardar segredos em. `values.yaml`

> [!NOTE]
> As seguintes abordagens mostram-lhe como armazenar e usar segredos para gráficos Helm gerados pela ferramenta do cliente. Se criar o seu próprio gráfico Helm, pode usar o gráfico Helm diretamente para gerir e armazenar segredos.

### <a name="using-valuesdevyaml"></a>Usando valores.dev.yaml

Num projeto que já preparou com a Azure `values.dev.yaml` Dev Spaces, `azds.yaml` crie um ficheiro na mesma pasta que definir as suas chaves e valores secretos. Por exemplo:

```yaml
secrets:
  redis:
    port: "6380"
    host: "contosodevredis.redis.cache.windows.net"
    key: "secretkeyhere"
```

Verifique `azds.yaml` as referências `values.dev.yaml` do `?`ficheiro como opcionais utilizando um . Por exemplo:

```yaml
install:
  values:
  - values.dev.yaml?
  - secrets.dev.yaml?
```

Se tiver ficheiros secretos adicionais, também pode adicioná-los aqui.

Atualize ou verifique se o seu serviço refere os seus segredos como variáveis ambientais. Por exemplo:

```javascript
var redisPort = process.env.REDIS_PORT
var host = process.env.REDIS_HOST
var theKey = process.env.REDIS_KEY
```
    
Execute os `azds up`seus serviços atualizados usando .

```console
azds up
```
 
Use `kubectl` para verificar se os seus segredos foram criados.

```console
kubectl get secret --namespace default -o yaml 
```

> [!IMPORTANT]
> Não é recomendado guardar segredos no controlo de fontes. Se utilizar o `values.dev.yaml` Git, adicione ao `.gitignore` ficheiro para evitar cometer segredos no controlo de origem.

### <a name="using-azdsyaml"></a>Usando azds.yaml

Num projeto que já preparou com a Azure Dev Spaces, adicione chaves e valor secretos `azds.yaml`usando *$PLACEHOLDER* sintaxe em *configurações.develop.set* em . Por exemplo:

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
> Pode introduzir valores secretos diretamente sem utilizar *$PLACEHOLDER* sintaxe em `azds.yaml`. No entanto, esta `azds.yaml` abordagem não é recomendada uma vez que está armazenada no controlo de fontes.
     
Crie `.env` um ficheiro na `azds.yaml` mesma pasta que para definir os seus *valores $PLACEHOLDER.* Por exemplo:

```
REDIS_PORT=3333
REDIS_HOST=myredishost
REDIS_KEY=myrediskey
```

> [!IMPORTANT]
> Não é recomendado guardar segredos no controlo de fontes. Se utilizar o `.env` Git, adicione ao `.gitignore` ficheiro para evitar cometer segredos no controlo de origem.

Atualize ou verifique se o seu serviço refere os seus segredos como variáveis ambientais. Por exemplo:

```javascript
var redisPort = process.env.REDIS_PORT
var host = process.env.REDIS_HOST
var theKey = process.env.REDIS_KEY
```
    
Execute os `azds up`seus serviços atualizados usando .

```console
azds up
```
 
Use `kubectl` para verificar se os seus segredos foram criados.

```console
kubectl get secret --namespace default -o yaml 
```

## <a name="using-secrets-as-build-arguments"></a>Usando segredos como construir argumentos

A secção anterior mostrou como armazenar e usar segredos para usar no tempo de funcionação do contentor. Também pode usar qualquer segredo no tempo de construção de contentores, como uma palavra-passe para um NuGet privado, usando `azds.yaml`.

Em `azds.yaml`, defina os segredos do tempo de construção em *configurações.develop.build.args* usando a `<variable name>: ${secret.<secret name>.<secret key>}` sintaxe. Por exemplo:

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

No exemplo acima, *mynugetsecret* é um segredo existente e *pattoken* é uma chave existente.

>[!NOTE]
> Nomes secretos e `.` chaves podem conter o personagem. Use `\` para `.` escapar ao passar segredos como construir argumentos. Por exemplo, para passar um segredo chamado *foo.bar* com a chave do *símbolo*: `MYTOKEN: ${secret.foo\.bar.token}`. Além disso, os segredos podem ser avaliados com texto prefixo e postfixo. Por exemplo, `MYURL: eus-${secret.foo\.bar.token}-version1`. Além disso, segredos disponíveis nos espaços dos pais e dos avós podem ser passados como argumentos de construção.

No seu Dockerfile, use a diretiva *ARG* para consumir o segredo, em seguida, use essa mesma variável mais tarde no Dockerfile. Por exemplo:

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

Com estes métodos, pode agora ligar-se de forma segura a uma base de dados, a um Azure Cache para Redis ou a aceder a serviços Azure seguros.
 
