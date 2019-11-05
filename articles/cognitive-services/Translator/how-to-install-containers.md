---
title: Instalar e executar contêineres-Tradução de Texto
titleSuffix: Azure Cognitive Services
description: Como baixar, instalar e executar contêineres para análise de Tradução de Texto neste tutorial passo a passos.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: dapine
ms.openlocfilehash: 7b2b07f71d00e6da66062d1826f8c5c88bb6be7a
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73507882"
---
# <a name="install-and-run-translator-text-containers"></a>Instalar e executar contêineres de Tradução de Texto

<!--
    ACOM forward link:
    https://docs.microsoft.com/azure/cognitive-services/translator/howto-install-containers
-->

Os contêineres permitem executar as APIs de Tradução de Texto em seu próprio ambiente e são ótimos para requisitos específicos de segurança e governança de dados.

## <a name="prerequisites"></a>Pré-requisitos

Você deve atender aos seguintes pré-requisitos antes de usar contêineres de Tradução de Texto:

|Necessário|Objetivo|
|--|--|
|Mecanismo do Docker| Você precisa do mecanismo do Docker instalado em um [computador host](#the-host-computer). O Docker fornece pacotes que configuram o ambiente do Docker no [MacOS](https://docs.docker.com/docker-for-mac/), no [Windows](https://docs.docker.com/docker-for-windows/)e no [Linux](https://docs.docker.com/engine/installation/#supported-platforms). Para obter uma introdução sobre o Docker e noções básicas de contêiner, consulte a [visão geral do Docker](https://docs.docker.com/engine/docker-overview/).<br><br> **No Windows**, o Docker também deve ser configurado para dar suporte a contêineres do Linux.<br><br>|
|Familiaridade com o Docker | Você deve ter uma compreensão básica dos conceitos do Docker, como registros, repositórios, contêineres e imagens de contêiner, bem como o conhecimento de comandos básicos de `docker`.|

## <a name="request-access-to-the-container-registry"></a>Solicitar acesso ao registro de contêiner

Você deve primeiro concluir e enviar o [formulário de solicitação de contêineres de tradução de texto cognitiva](https://aka.ms/translatorcontainerform) para solicitar acesso ao contêiner.

[!INCLUDE [Request access to the container registry](../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Authenticate to the container registry](../../../includes/cognitive-services-containers-access-registry.md)]

### <a name="the-host-computer"></a>O computador host

O host é um computador baseado em x64 com um sistema operacional Linux que executa o contêiner do Docker. Ele pode ser um computador em seu local ou em um serviço de hospedagem do Docker no Azure, como:

* Serviço kubernetes do Azure.
* Instâncias de contêiner do Azure.
* Um cluster [kubernetes](https://kubernetes.io/) implantado para Azure Stack.

### <a name="container-requirements-and-recommendations"></a>Recomendações e requisitos de contêiner

A tabela a seguir descreve os núcleos de CPU mínimos e recomendados, pelo menos 2,6 gigahertz (GHz) ou mais rápido, e memória, em gigabytes (GB), para alocar para cada contêiner de Tradução de Texto.

| Contentor | Mínimo | Par de idiomas |
|-----------|---------|---------------|
| Texto do Tradutor | 4 núcleos, 4 GB de memória | 4 |

Para cada par de idiomas, é recomendável ter 1 GB de memória. Por padrão, o contêiner de Tradução de Texto tem três ou quatro pares de idiomas, dependendo do `<image-tag>` que você está executando. Consulte [idiomas com suporte e tradução](#supported-languages-and-translation) para obter os detalhes. O núcleo e a memória correspondem às configurações de `--cpus` e `--memory`, que são usadas como parte do comando `docker run`.

## <a name="get-the-container-image-with-docker-pull"></a>Obter a imagem de contêiner com `docker pull`

As imagens de contêiner para Tradução de Texto estão disponíveis no repositório de contêiner a seguir. A tabela também mapeia as marcas de imagem de contêiner e seus idiomas com suporte correspondentes.

| Contentor | Marca de imagem | Idiomas com suporte |
|-----------|-----------|---------------------|
| `containerpreview.azurecr.io/microsoft/cognitive-services-translator-text` | `ar_de_en_ru_zh_1.0.0` | `ar-SA`, `zh-CN`, `de-DE`e `ru-RU` |
| `containerpreview.azurecr.io/microsoft/cognitive-services-translator-text` | `de_en_es_fr_1.0.0` | `de-DE`, `fr-FR`e `es-ES` |

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

### <a name="docker-pull-for-the-translator-text-container"></a>Pull do Docker para o contêiner de Tradução de Texto

Para executar o comando [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) , primeiro você precisa ter acesso ao registro de contêiner. No CLI do Azure você pode fazer logon no registro de contêiner do Azure usando o comando [`az acr login`](https://docs.microsoft.com/cli/azure/acr?view=azure-cli-latest#az-acr-login) .

```azureinteractive
az acr login --name containerpreview.azurecr.io
```

Este comando autenticará o usuário atual com o registro de contêiner do Azure correspondente. Agora, você está livre para executar o comando `docker pull`.

> [!NOTE]
> Dependendo de qual suporte de idioma você precisa, forneça o `<image-tag>`correspondente.

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-translator-text:<image-tag>
```

## <a name="how-to-use-the-container"></a>Como usar o contêiner

Depois que o contêiner estiver no [computador host](#the-host-computer), use o processo a seguir para trabalhar com o contêiner.

1. [Execute o contêiner](#run-the-container-with-docker-run). Mais [exemplos](translator-text-container-config.md#example-docker-run-commands) do comando `docker run` estão disponíveis.
1. [Consultar o ponto de extremidade de conversão do contêiner](#query-the-containers-translate-endpoint).

## <a name="run-the-container-with-docker-run"></a>Execute o contêiner com `docker run`

Use o comando [Docker execute](https://docs.docker.com/engine/reference/commandline/run/) para executar qualquer um dos três contêineres. [Exemplos](translator-text-container-config.md#example-docker-run-commands) do comando `docker run` estão disponíveis.

### <a name="run-container-example-of-docker-run-command"></a>Exemplo de execução de contêiner do comando de execução do Docker

```Docker
docker run --rm -it -p 5000:80 --memory 4g --cpus 4 \
containerpreview.azurecr.io/microsoft/cognitive-services-translator-text:ar_de_en_ru_zh_1.0.0 \
Eula=accept
```

Este comando:

* Executa um contêiner de Tradução de Texto a partir da imagem de contêiner
* Aloca 4 núcleos de CPU e 4 gigabytes (GB) de memória
* Expõe a porta TCP 5000 e aloca um pseudo-TTY para o contêiner
* Aceita o contrato de usuário final (EULA)
* Remove automaticamente o contêiner depois que ele é encerrado. A imagem de contêiner ainda está disponível no computador host

### <a name="how-to-collect-docker-logs"></a>Como coletar logs do Docker

Para fins de solução de problemas, pode ser útil exibir os logs do Docker a partir da execução do contêiner. Primeiro, execute o comando [Docker PS](https://docs.docker.com/engine/reference/commandline/ps/) com o sinalizador de formatação para limitar os detalhes exibidos para todos os contêineres.

```Docker
docker ps -a --format "table {{.ID}}\t{{.Image}}\t{{.Status}}"

CONTAINER ID    IMAGE                                                                       STATUS
ed6f115697f3    containerpreview.azurecr.io/microsoft/cognitive-services-translator-text    Up 4 minutes
```

Em seguida, execute o comando [Docker logs](https://docs.docker.com/engine/reference/commandline/logs/) com o `<Container ID>` para o contêiner correspondente em questão para exibir seus logs.

```Docker
docker logs <Container ID> --timestamps --since=4h | grep Error
```

O comando Docker logs acima coletará os logs de "erro" das últimas quatro horas.

## <a name="supported-languages-and-translation"></a>Idiomas com suporte e tradução

O método `POST /translate` dá suporte às seguintes conversões de idiomas, passando do *Inglês* para um idioma de destino e vice-versa. Observe que, embora você possa ir para e do inglês com um dos idiomas listados, *não é possível* ir de um idioma *diferente do inglês* para outro idioma diferente do *Inglês* .

> [!NOTE]
> Para obter a qualidade ideal, os consumidores devem enviar apenas uma frase por solicitação.

| Conversão de idioma | Conversão de idioma ISO | Marcas de imagem |
|--|--|:--|
| Inglês: left_right_arrow: Chinês | `en-US`: left_right_arrow: `zh-CN` | `ar_de_en_ru_zh_1.0.0` |
| Inglês: left_right_arrow: Russo | `en-US`: left_right_arrow: `ru-RU` | `ar_de_en_ru_zh_1.0.0` |
| Inglês: left_right_arrow: árabe | `en-US`: left_right_arrow: `ar-SA` | `ar_de_en_ru_zh_1.0.0` |
| Inglês: left_right_arrow: alemão | `en-US`: left_right_arrow: `de-DE` | `ar_de_en_ru_zh_1.0.0` e `de_en_es_fr_1.0.0` |
| Inglês: left_right_arrow: espanhol | `en-US`: left_right_arrow: `es-ES` | `de_en_es_fr_1.0.0` |
| Inglês: left_right_arrow: francês | `en-US`: left_right_arrow: `fr-FR` | `de_en_es_fr_1.0.0` |

> [!IMPORTANT]
> O `Eula` deve ser especificado para executar o contêiner; caso contrário, o contêiner não será iniciado.

## <a name="query-the-containers-translate-endpoint"></a>Consultar o ponto de extremidade de conversão do contêiner

O contêiner fornece uma API de ponto de extremidade de conversão baseada em REST. Vários exemplos de uso desse ponto de extremidade são os seguintes:

# <a name="curltabcurl"></a>[cURL](#tab/curl)


Execute o comando de rotação a seguir da CLI desejada.

```curl
curl -X POST "http://localhost:5000/translate?api-version=3.0&from=en-US&to=de-DE"
    -H "Content-Type: application/json" -d "[{'Text':'hello, how are you'}]"
```

> [!TIP]
> Se você tentar esta ondulação antes que o contêiner esteja pronto, você acabará recebendo uma resposta "o serviço está temporariamente indisponível". Basta aguardar até que o contêiner esteja pronto e, em seguida, tentar novamente.

A saída a seguir será impressa no console.

```console
"translations": [
    {
        "text": "hallo, wie geht es dir",
        "to": "de-DE"
    }
]
```

# <a name="swaggertabswagger"></a>[Swagger](#tab/Swagger)

Navegue até a página do Swagger, http://localhost:5000/swagger/index.html

1. Selecione **postar/translate**
1. Selecione **experimentar**
1. Insira o parâmetro **from** como `en-US`
1. Insira o parâmetro **to** como `de-DE`
1. Insira o parâmetro de **versão da API** como `3.0`
1. Em **textos**, substitua `string` pelo seguinte JSON
    ```json
    [
        {
            "text": "hello, how are you"
        }
    ]
    ```
1. Selecione **executar**, as traduções resultantes serão geradas no **corpo da resposta**. Você deve esperar algo semelhante ao seguinte:
    ```json
    "translations": [
      {
          "text": "hallo, wie geht es dir",
          "to": "de-DE"
      }
    ]
    ```

# <a name="net-coretabnetcore"></a>[.NET Core](#tab/netcore)

1. Inicie o Visual Studio e crie um novo aplicativo de console.
1. Edite o arquivo `*.csproj` para adicionar o nó `<LangVersion>7.1</LangVersion>`-isso C# especifica 7,1.
1. Adicione o pacote NuGet [Newtonsoft. JSON](https://www.nuget.org/packages/Newtonsoft.Json/) , versão 11.0.2.
1. Em `Program.cs`, substitua todo o código existente pelo seguinte:
    ```csharp
    using Newtonsoft.Json;
    using System;
    using System.Net.Http;
    using System.Text;
    using System.Threading.Tasks;

    namespace TranslateContainer
    {
        class Program
        {
            const string ApiHostEndpoint = "http://localhost:5000";
            const string TranslateApi = "/translate?api-version=3.0&from=en-US&to=de-DE";

            static async Task Main(string[] args)
            {
                var textToTranslate = "hello, how are you";
                var result = await TranslateTextAsync(textToTranslate);

                Console.WriteLine(result);
                Console.ReadLine();
            }

            static async Task<string> TranslateTextAsync(string textToTranslate)
            {
                var body = new object[] { new { Text = textToTranslate } };
                var requestBody = JsonConvert.SerializeObject(body);

                var client = new HttpClient();
                using (var request =
                    new HttpRequestMessage
                    {
                        Method = HttpMethod.Post,
                        RequestUri = new Uri($"{ApiHostEndpoint}{TranslateApi}"),
                        Content = new StringContent(requestBody, Encoding.UTF8, "application/json")
                    })
                {
                    // Send the request and await a response.
                    var response = await client.SendAsync(request);

                    return await response.Content.ReadAsStringAsync();
                }
            }
        }
    }
    ```
1. Pressione **F5** para executar o programa.
1. A saída a seguir será impressa no console.
    ```console
    "translations": [
      {
          "text": "hallo, wie geht es dir",
          "to": "de-DE"
      }
    ]
    ```

***

[!INCLUDE [Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="stop-the-container"></a>Parar o contêiner

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>Resolução de problemas

Se você executar o contêiner com uma [montagem](translator-text-container-config.md#mount-settings) de saída e o registro em log habilitado, o contêiner gerará arquivos de log que são úteis para solucionar problemas que ocorrem ao iniciar ou executar o contêiner.

<!--blogs/samples/video course -->

[!INCLUDE [Discoverability of more container information](../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>Resumo

Neste artigo, você aprendeu os conceitos e o fluxo de trabalho para baixar, instalar e executar Tradução de Texto contêineres. Em resumo:

* O Tradução de Texto fornece vários contêineres do Linux para o Docker, encapsulando vários pares de idiomas.
* As imagens de contêiner são baixadas do registro de "visualização do contêiner".
* As imagens de contêiner são executadas no Docker.
* Você pode usar a API REST ou o SDK para chamar operações em contêineres Tradução de Texto especificando o URI do host do contêiner.

## <a name="next-steps"></a>Passos seguintes

* Examinar [definir contêineres](translator-text-container-config.md) para definições de configuração
* Consulte as [perguntas frequentes sobre o contêiner](../containers/container-faq.md) para resolver problemas relacionados à funcionalidade.
