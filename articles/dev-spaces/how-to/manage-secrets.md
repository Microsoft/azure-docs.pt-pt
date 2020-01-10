---
title: Como gerenciar segredos ao trabalhar com um espaço de desenvolvimento do Azure
services: azure-dev-spaces
ms.date: 12/03/2019
ms.topic: conceptual
description: Saiba como usar segredos do kubernetes em tempo de execução ou de compilação ao desenvolver aplicativos com Azure Dev Spaces
keywords: Docker, kubernetes, Azure, AKS, serviço de contêiner do Azure, contêineres
ms.openlocfilehash: d9dd0de348612bbb3baf5fb351c1c9af1c228c1f
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75438467"
---
# <a name="how-to-manage-secrets-when-working-with-an-azure-dev-space"></a>Como gerenciar segredos ao trabalhar com um espaço de desenvolvimento do Azure

Seus serviços podem exigir determinadas senhas, cadeias de conexão e outros segredos, como bancos de dados ou outros serviços seguros do Azure. Ao definir os valores desses segredos em arquivos de configuração, você pode torná-los disponíveis em seu código como variáveis de ambiente.  Esses arquivos de configuração devem ser tratados com cuidado para evitar comprometer a segurança dos segredos.

## <a name="storing-and-using-runtime-secrets"></a>Armazenando e usando segredos de tempo de execução

O Azure Dev Spaces fornece duas opções recomendadas e simplificadas para armazenar segredos em gráficos Helm gerados pelo Azure Dev Spaces ferramentas de cliente: no arquivo `values.dev.yaml` e embutido diretamente no `azds.yaml`. Não é recomendável armazenar segredos em `values.yaml`.

> [!NOTE]
> As abordagens a seguir mostram como armazenar e usar segredos para gráficos Helm gerados pelas ferramentas do cliente. Se você criar seu próprio gráfico Helm, poderá usar o gráfico Helm diretamente para gerenciar e armazenar segredos.

### <a name="using-valuesdevyaml"></a>Usando Values. dev. YAML

Em um projeto que você já preparou com Azure Dev Spaces, crie um arquivo de `values.dev.yaml` na mesma pasta que `azds.yaml` para definir suas chaves secretas e valores. Por exemplo:

```yaml
secrets:
  redis:
    port: "6380"
    host: "contosodevredis.redis.cache.windows.net"
    key: "secretkeyhere"
```

Verifique se o arquivo de `azds.yaml` faz referência a `values.dev.yaml` como opcional usando uma `?`. Por exemplo:

```yaml
install:
  values:
  - values.dev.yaml?
  - secrets.dev.yaml?
```

Se você tiver arquivos secretos adicionais, também poderá adicioná-los aqui.

Atualize ou verifique se seu serviço faz referência aos seus segredos como variáveis de ambiente. Por exemplo:

```javascript
var redisPort = process.env.REDIS_PORT
var host = process.env.REDIS_HOST
var theKey = process.env.REDIS_KEY
```
    
Execute seus serviços atualizados usando o `azds up`.

```console
azds up
```
 
Use `kubectl` para verificar se seus segredos foram criados.

```console
kubectl get secret --namespace default -o yaml 
```

> [!IMPORTANT]
> Não é recomendável armazenar segredos no controle do código-fonte. Se estiver usando o Git, adicione `values.dev.yaml` ao arquivo de `.gitignore` para evitar a confirmação de segredos no controle do código-fonte.

### <a name="using-azdsyaml"></a>Usando azds. YAML

Em um projeto que você já preparou com Azure Dev Spaces, adicione chaves secretas e o valor usando a sintaxe *$PlaceHolder* em *configurações. desenvolver. Install. definido* em `azds.yaml`. Por exemplo:

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
> Você pode inserir valores secretos diretamente sem usar a sintaxe *$PlaceHolder* em `azds.yaml`. No entanto, essa abordagem não é recomendada, pois `azds.yaml` é armazenada no controle do código-fonte.
     
Crie um arquivo de `.env` na mesma pasta que `azds.yaml` para definir seus valores de *$PlaceHolder* . Por exemplo:

```
REDIS_PORT=3333
REDIS_HOST=myredishost
REDIS_KEY=myrediskey
```

> [!IMPORTANT]
> Não é recomendável armazenar segredos no controle do código-fonte. Se estiver usando o Git, adicione `.env` ao arquivo de `.gitignore` para evitar a confirmação de segredos no controle do código-fonte.

Atualize ou verifique se seu serviço faz referência aos seus segredos como variáveis de ambiente. Por exemplo:

```javascript
var redisPort = process.env.REDIS_PORT
var host = process.env.REDIS_HOST
var theKey = process.env.REDIS_KEY
```
    
Execute seus serviços atualizados usando o `azds up`.

```console
azds up
```
 
Use `kubectl` para verificar se seus segredos foram criados.

```console
kubectl get secret --namespace default -o yaml 
```

## <a name="using-secrets-as-build-arguments"></a>Usando segredos como argumentos de compilação

A seção anterior mostrou como armazenar e usar segredos para usar em tempo de execução do contêiner. Você também pode usar qualquer segredo no momento da compilação do contêiner, como uma senha para um NuGet privado, usando `azds.yaml`.

Em `azds.yaml`, defina os segredos de tempo de compilação em *configurações. desenvolva. Build. args* usando a sintaxe `<variable name>: ${secret.<secret name>.<secret key>}`. Por exemplo:

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
> As chaves e os nomes de segredo podem conter o caractere `.`. Use `\` para escapar `.` ao passar segredos como argumentos de compilação. Por exemplo, para passar um segredo chamado *foo. bar* com a chave de *token*: `MYTOKEN: ${secret.foo\.bar.token}`. Além disso, os segredos podem ser avaliados com o prefixo e o texto do sufixo. Por exemplo, `MYURL: eus-${secret.foo\.bar.token}-version1`. Além disso, os segredos disponíveis nos espaços pai e avô podem ser passados como argumentos de compilação.

Em seu Dockerfile, use a diretiva *ARG* para consumir o segredo e, em seguida, use essa mesma variável posteriormente no Dockerfile. Por exemplo:

```dockerfile
...
ARG MYTOKEN
...
ARG NUGET_EXTERNAL_FEED_ENDPOINTS="{'endpointCredentials': [{'endpoint':'PRIVATE_NUGET_ENDPOINT', 'password':'${MYTOKEN}'}]}"
...
```

Atualize os serviços em execução no cluster com essas alterações. Na linha de comando, execute o comando:

```
azds up
```

## <a name="next-steps"></a>Passos seguintes

Com esses métodos, agora você pode se conectar com segurança a um banco de dados, um cache do Azure para Redis ou acessar serviços seguros do Azure.
 
