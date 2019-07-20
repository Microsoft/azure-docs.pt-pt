---
title: Contentores do Docker
titleSuffix: Language Understanding - Azure Cognitive Services
description: O contêiner LUIS carrega seu aplicativo treinado ou publicado em um contêiner do Docker e fornece acesso às previsões de consulta dos pontos de extremidade de API do contêiner.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 07/02/2019
ms.author: dapine
ms.openlocfilehash: ae2f24c83cb0de054cc97bf0be8ada35a568ad82
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/19/2019
ms.locfileid: "68360562"
---
# <a name="install-and-run-luis-docker-containers"></a>Instalar e executar contêineres do Docker LUIS
 
O contêiner Reconhecimento vocal (LUIS) carrega seu modelo de Reconhecimento vocal treinado ou publicado, também conhecido como um [aplicativo Luis](https://www.luis.ai), em um contêiner do Docker e fornece acesso às previsões de consulta dos pontos de extremidade de API do contêiner. Você pode coletar logs de consulta do contêiner e carregá-los de volta para o aplicativo Reconhecimento vocal para melhorar a precisão da previsão do aplicativo.

O vídeo a seguir demonstra como usar esse contêiner.

[![Demonstração de contentor para os serviços cognitivos](./media/luis-container-how-to/luis-containers-demo-video-still.png)](https://aka.ms/luis-container-demo)

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para executar o contêiner LUIS, você deve ter o seguinte: 

|Necessário|Objetivo|
|--|--|
|Mecanismo do Docker| Você precisa do mecanismo do Docker instalado em um [computador host](#the-host-computer). O Docker fornece pacotes que configuram o ambiente do Docker no [MacOS](https://docs.docker.com/docker-for-mac/), no [Windows](https://docs.docker.com/docker-for-windows/)e no [Linux](https://docs.docker.com/engine/installation/#supported-platforms). Para obter um manual sobre noções básicas do Docker e um contentor, consulte a [descrição geral do Docker](https://docs.docker.com/engine/docker-overview/).<br><br> Docker tem de ser configurado para permitir que os contentores para se ligar com e enviar dados de faturação para o Azure. <br><br> **No Windows**, o Docker também deve ser configurado para dar suporte a contêineres do Linux.<br><br>|
|Familiaridade com o Docker | Você deve ter uma compreensão básica dos conceitos do Docker, como registros, repositórios, contêineres e imagens de contêiner, bem como o conhecimento de comandos `docker` básicos.| 
|Recurso `Cognitive Services` do Azure e arquivo de [aplicativo empacotado](luis-how-to-start-new-app.md#export-app-for-containers) Luis |Para usar o contêiner, você deve ter:<br><br>* Um recurso do Azure de _Serviços cognitivas_ e a chave de cobrança associada ao URI do ponto de extremidade de cobrança. Ambos os valores estão disponíveis nas páginas visão geral e chaves para o recurso e são necessários para iniciar o contêiner. Você precisa adicionar o `luis/v2.0` roteamento ao URI do ponto de extremidade, conforme mostrado no exemplo de BILLING_ENDPOINT_URI a seguir. <br>* Um aplicativo treinado ou publicado empacotado como uma entrada montada para o contêiner com sua ID de aplicativo associada. Você pode obter o arquivo empacotado no portal do LUIS ou as APIs de criação. Se você estiver obtendo um aplicativo LUIS empacotado das [APIs de criação](#authoring-apis-for-package-file), também precisará da sua _chave de criação_.<br><br>Esses requisitos são usados para passar argumentos de linha de comando para as seguintes variáveis:<br><br>**{AUTHORING_KEY}** : Essa chave é usada para obter o aplicativo empacotado do serviço LUIS na nuvem e carregar os logs de consulta de volta para a nuvem. O formato é `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`.<br><br>**{APPLICATION_ID}** : Essa ID é usada para selecionar o aplicativo. O formato é `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx`.<br><br>**{API_KEY}** : Essa chave é usada para iniciar o contêiner. Você pode encontrar a chave do ponto de extremidade em dois locais. A primeira é a portal do Azure na lista de chaves do recurso de _Serviços cognitivas_ . A chave do ponto de extremidade também está disponível no portal do LUIS na página Configurações de ponto de extremidade e chaves. Não use a chave inicial.<br><br>**{ENDPOINT_URI}** : O ponto de extremidade conforme fornecido na página Visão geral.<br><br>A chave de [criação e a chave do ponto de extremidade](luis-boundaries.md#key-limits) têm finalidades diferentes. Não os use de forma intercambiável. |

### <a name="authoring-apis-for-package-file"></a>Criando APIs para o arquivo de pacote

Criando APIs para aplicativos empacotados:

* [API do pacote publicado](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/apps-packagepublishedapplicationasgzip)
* [API de pacote apenas não publicada, com treinamento](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/apps-packagetrainedapplicationasgzip)

### <a name="the-host-computer"></a>O computador host

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="container-requirements-and-recommendations"></a>Requisitos do contentor e recomendações

Este contêiner oferece suporte a valores mínimos e recomendados para as configurações:

|Contentor| Mínimo | Recomendado | TPS<br>(Mínimo, máximo)|
|-----------|---------|-------------|--|
|LUIS|1 núcleo, 2 GB de memória|1 núcleo, 4 GB de memória|20,40|

* Cada núcleo deve ter pelo menos 2,6 gigahertz (GHz) ou mais rápido.
* TPS-transações por segundo

O núcleo e a `--cpus` memória correspondem às configurações e `--memory` , que são `docker run` usadas como parte do comando.

## <a name="get-the-container-image-with-docker-pull"></a>Obter a imagem de contêiner com`docker pull`

Use o [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) comando para baixar uma imagem `mcr.microsoft.com/azure-cognitive-services/luis` de contêiner do repositório:

```
docker pull mcr.microsoft.com/azure-cognitive-services/luis:latest
```

Use o [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) comando para baixar uma imagem de contêiner.

Para obter uma descrição completa das marcas disponíveis, como `latest` usado no comando anterior, consulte [Luis](https://go.microsoft.com/fwlink/?linkid=2043204) no Hub do Docker.

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]


## <a name="how-to-use-the-container"></a>Como usar o contêiner

Depois que o contêiner estiver no [computador host](#the-host-computer), use o processo a seguir para trabalhar com o contêiner.

![Processo para usar o contêiner Reconhecimento vocal (LUIS)](./media/luis-container-how-to/luis-flow-with-containers-diagram.jpg)

1. [Exportar pacote](#export-packaged-app-from-luis) para contêiner do portal do Luis ou APIs do Luis.
1. Mova o arquivo de pacote para o diretório de **entrada** necessário no [computador host](#the-host-computer). Não renomeie, altere, substitua ou Descompacte o arquivo de pacote LUIS.
1. [Execute o contêiner](##run-the-container-with-docker-run), com as configurações necessárias de montagem e cobrança de _entrada_ . Mais [exemplos](luis-container-configuration.md#example-docker-run-commands) do `docker run` comando estão disponíveis. 
1. [Consultando o ponto de extremidade de previsão do contêiner](#query-the-containers-prediction-endpoint). 
1. Quando você terminar o contêiner, [importe os logs de ponto de extremidade](#import-the-endpoint-logs-for-active-learning) da montagem de saída no portal do Luis e [interrompa](#stop-the-container) o contêiner.
1. Use o [aprendizado ativo](luis-how-to-review-endpoint-utterances.md) do portal do Luis na página **examinar ponto de extremidade declarações** para melhorar o aplicativo.

O aplicativo em execução no contêiner não pode ser alterado. Para alterar o aplicativo no contêiner, você precisa alterar o aplicativo no serviço LUIS usando o portal do [Luis](https://www.luis.ai) ou usar as [APIs de criação](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c2f)do Luis. Em seguida, treine e/ou publique e, em seguida, baixe um novo pacote e execute o contêiner novamente.

O aplicativo LUIS dentro do contêiner não pode ser exportado de volta para o serviço LUIS. Somente os logs de consulta podem ser carregados. 

## <a name="export-packaged-app-from-luis"></a>Exportar aplicativo empacotado do LUIS

O contêiner LUIS requer um aplicativo LUIS treinado ou publicado para responder às consultas de previsão do usuário declarações. Para obter o aplicativo LUIS, use a API de pacote treinada ou publicada. 

O local padrão é o `input` subdiretório em relação ao local em que você executa `docker run` o comando.  

Coloque o arquivo de pacote em um diretório e faça referência a esse diretório como a montagem de entrada ao executar o contêiner do Docker. 

### <a name="package-types"></a>Tipos de pacote

O diretório de montagem de entrada pode conter as versões de **produção**, **preparo**e **treinadas** do aplicativo simultaneamente. Todos os pacotes são montados. 

|Tipo de pacote|API de ponto de extremidade de consulta|Disponibilidade da consulta|Formato do nome de arquivo do pacote|
|--|--|--|--|
|Treina|Get, post|Somente contêiner|`{APPLICATION_ID}_v{APPLICATION_VERSION}.gz`|
|Teste|Get, post|Azure e contêiner|`{APPLICATION_ID}_STAGING.gz`|
|Produção|Get, post|Azure e contêiner|`{APPLICATION_ID}_PRODUCTION.gz`|

> [!IMPORTANT]
> Não renomeie, altere, substitua ou descompacte os arquivos de pacote LUIS.

### <a name="packaging-prerequisites"></a>Pré-requisitos de empacotamento

Antes de empacotar um aplicativo LUIS, você deve ter o seguinte:

|Requisitos de empacotamento|Detalhes|
|--|--|
|Instância de recurso de _Serviços cognitivas_ do Azure|As regiões com suporte incluem<br><br>Oeste dos EUA```westus```()<br>Europa Ocidental (```westeurope```)<br>Leste da Austrália```australiaeast```()|
|Aplicativo LUIS treinado ou publicado|Sem [dependências](#unsupported-dependencies)não suportadas. |
|Acesso ao sistema de arquivos do [computador host](#the-host-computer) |O computador host deve permitir uma [montagem de entrada](luis-container-configuration.md#mount-settings).|
  
### <a name="export-app-package-from-luis-portal"></a>Exportar pacote do aplicativo do portal do LUIS

O [portal](https://www.luis.ai) do Luis fornece a capacidade de exportar o pacote do aplicativo treinado ou publicado. 

### <a name="export-published-apps-package-from-luis-portal"></a>Exportar o pacote do aplicativo publicado do portal do LUIS

O pacote do aplicativo publicado está disponível na página de lista **meus aplicativos** . 

1. Entre no [portal](https://www.luis.ai)do Luis.
1. Marque a caixa de seleção à esquerda do nome do aplicativo na lista. 
1. Selecione o item de **exportação** da barra de ferramentas contextual acima da lista.
1. Selecione **exportar para contêiner (gzip)** .
1. Selecione o ambiente do slot de **produção** ou do **slot de preparo**.
1. O pacote é baixado do navegador.

![Exportar o pacote publicado para o contêiner no menu exportar da página de aplicativo](./media/luis-container-how-to/export-published-package-for-container.png)

### <a name="export-trained-apps-package-from-luis-portal"></a>Exportar pacote de aplicativo treinado do portal do LUIS

O pacote do aplicativo treinado está disponível na página de lista de **versões** . 

1. Entre no [portal](https://www.luis.ai)do Luis.
1. Selecione o aplicativo na lista. 
1. Selecione **gerenciar** na barra de navegação do aplicativo.
1. Selecione **versões** na barra de navegação à esquerda.
1. Marque a caixa de seleção à esquerda do nome da versão na lista.
1. Selecione o item de **exportação** da barra de ferramentas contextual acima da lista.
1. Selecione **exportar para contêiner (gzip)** .
1. O pacote é baixado do navegador.

![Exportar o pacote treinado para o contêiner no menu exportar da página de versões](./media/luis-container-how-to/export-trained-package-for-container.png)


### <a name="export-published-apps-package-from-api"></a>Exportar pacote do aplicativo publicado da API

Use o seguinte método de API REST para empacotar um aplicativo LUIS que você já [publicou](luis-how-to-publish-app.md). Substituindo seus próprios valores apropriados para os espaços reservados na chamada à API, usando a tabela abaixo da especificação HTTP.

```http
GET /luis/api/v2.0/package/{APPLICATION_ID}/slot/{APPLICATION_ENVIRONMENT}/gzip HTTP/1.1
Host: {AZURE_REGION}.api.cognitive.microsoft.com
Ocp-Apim-Subscription-Key: {AUTHORING_KEY}
```

| Marcador de posição | Valor |
|-------------|-------|
|{APPLICATION_ID} | A ID de aplicativo do aplicativo LUIS publicado. |
|{APPLICATION_ENVIRONMENT} | O ambiente do aplicativo LUIS publicado. Use um dos seguintes valores:<br/>```PRODUCTION```<br/>```STAGING``` |
|{AUTHORING_KEY} | A chave de criação da conta LUIS para o aplicativo LUIS publicado.<br/>Você pode obter sua chave de criação na página **configurações do usuário** no portal do Luis. |
|{AZURE_REGION} | A região apropriada do Azure:<br/><br/>```westus```-Oeste dos EUA<br/>```westeurope```-Europa Ocidental<br/>```australiaeast```-Leste da Austrália |

Para baixar o pacote publicado, consulte a documentação da [API aqui][download-published-package]. Se for baixado com êxito, a resposta será um arquivo de pacote LUIS. Salve o arquivo no local de armazenamento especificado para a montagem de entrada do contêiner. 

### <a name="export-trained-apps-package-from-api"></a>Exportar pacote de aplicativo treinado da API

Use o seguinte método de API REST para empacotar um aplicativo LUIS que você já tenha [treinado](luis-how-to-train.md). Substituindo seus próprios valores apropriados para os espaços reservados na chamada à API, usando a tabela abaixo da especificação HTTP.

```http
GET /luis/api/v2.0/package/{APPLICATION_ID}/versions/{APPLICATION_VERSION}/gzip HTTP/1.1
Host: {AZURE_REGION}.api.cognitive.microsoft.com
Ocp-Apim-Subscription-Key: {AUTHORING_KEY}
```

| Marcador de posição | Value |
|-------------|-------|
|{APPLICATION_ID} | A ID do aplicativo LUIS treinado. |
|{APPLICATION_VERSION} | A versão do aplicativo do aplicativo LUIS treinado. |
|{AUTHORING_KEY} | A chave de criação da conta LUIS para o aplicativo LUIS publicado.<br/>Você pode obter sua chave de criação na página **configurações do usuário** no portal do Luis.  |
|{AZURE_REGION} | A região apropriada do Azure:<br/><br/>```westus```-Oeste dos EUA<br/>```westeurope```-Europa Ocidental<br/>```australiaeast```-Leste da Austrália |

Para baixar o pacote treinado, consulte a documentação da [API aqui][download-trained-package]. Se for baixado com êxito, a resposta será um arquivo de pacote LUIS. Salve o arquivo no local de armazenamento especificado para a montagem de entrada do contêiner. 

## <a name="run-the-container-with-docker-run"></a>Execute o contêiner com`docker run`

Use o comando [Docker execute](https://docs.docker.com/engine/reference/commandline/run/) para executar o contêiner. O comando usa os seguintes parâmetros:

| Marcador de posição | Value |
|-------------|-------|
|{API_KEY} | Essa chave é usada para iniciar o contêiner. Não use a chave inicial. |
|{ENDPOINT_URI} | O valor do ponto de extremidade está disponível na `Cognitive Services` página de visão geral do portal do Azure. |

Substitua esses parâmetros pelos seus próprios valores no comando de exemplo `docker run` a seguir. Execute o comando no console do Windows.

```console
docker run --rm -it -p 5000:5000 ^
--memory 4g ^
--cpus 2 ^
--mount type=bind,src=c:\input,target=/input ^
--mount type=bind,src=c:\output\,target=/output ^
mcr.microsoft.com/azure-cognitive-services/luis ^
Eula=accept ^
Billing={ENDPOINT_URI} ^
ApiKey={API_KEY}
```

* Este exemplo usa o diretório fora da `C:` unidade para evitar conflitos de permissão no Windows. Se precisar de utilizar um diretório específico como a entrada de diretório, poderá ter de conceder o docker permissão de serviço. 
* Não altere a ordem dos argumentos, a menos que você esteja familiarizado com contêineres do Docker.
* Se você estiver usando um sistema operacional diferente, use o console/terminal correto, a sintaxe de pasta para montagens e o caractere de continuação de linha para o seu sistema. Esses exemplos pressupõem um console do Windows com um caractere `^`de continuação de linha. Como o contêiner é um sistema operacional Linux, a montagem de destino usa uma sintaxe de pasta em estilo Linux.

Este comando:

* Executa um contêiner da imagem de contêiner LUIS
* Carrega o aplicativo LUIS da montagem de entrada em c:\input, localizado no host do contêiner
* Aloca dois núcleos de CPU e 4 gigabytes (GB) de memória
* Expõe a porta TCP 5000 e aloca um TTY pseudo para o contentor
* Salva os logs de contêiner e LUIS na montagem de saída em c:\output, localizado no host do contêiner
* Remove automaticamente o contêiner depois que ele é encerrado. A imagem de contêiner ainda está disponível no computador host. 

Mais [exemplos](luis-container-configuration.md#example-docker-run-commands) do `docker run` comando estão disponíveis. 

> [!IMPORTANT]
> O `Eula`, `Billing`, e `ApiKey` opções tem de ser especificadas para executar o contentor; caso contrário, não inicia o contentor.  Para obter mais informações, consulte [faturação](#billing).
> O valor ApiKey é a **chave** da página chaves e pontos de extremidade no portal do Luis e também está disponível na página chaves de `Cognitive Services` recurso do Azure.  

[!INCLUDE [Running multiple containers on the same host](../../../includes/cognitive-services-containers-run-multiple-same-host.md)]

## <a name="endpoint-apis-supported-by-the-container"></a>APIs de ponto de extremidade com suporte no contêiner

As versões V2 e [v3 (versão prévia)](luis-migration-api-v3.md) da API estão disponíveis com o contêiner. 

## <a name="query-the-containers-prediction-endpoint"></a>Consultar o ponto de extremidade de previsão do contêiner

O contêiner fornece APIs de ponto de extremidade de previsão de consulta baseadas em REST. Os pontos de extremidade para aplicativos publicados (de preparo ou produção) têm uma rota _diferente_ de pontos de extremidade para aplicativos treinados. 

Use o host, `https://localhost:5000`, para APIs de contêiner. 

|Tipo de pacote|Método|Encaminhar|Parâmetros de consulta|
|--|--|--|--|
|Publicado|[Get](https://westus.dev.cognitive.microsoft.com/docs/services/5819c76f40a6350ce09de1ac/operations/5819c77140a63516d81aee78), [post](https://westus.dev.cognitive.microsoft.com/docs/services/5819c76f40a6350ce09de1ac/operations/5819c77140a63516d81aee79)|/luis/v2.0/apps/{appId}?|q={q}<br>preparação de &<br>[& timezoneOffset]<br>[& detalhado]<br>[log de &]<br>|
|Treina|Get, post|/luis/v2.0/apps/{appId}/versions/{versionId}?|q={q}<br>[& timezoneOffset]<br>[& detalhado]<br>[log de &]|

Os parâmetros de consulta configuram como e o que é retornado na resposta da consulta:

|Parâmetro de consulta|Type|Objetivo|
|--|--|--|
|`q`|Cadeia de caracteres|O expressão do usuário.|
|`timezoneOffset`|número|O timezoneOffset permite que você [altere o fuso horário](luis-concept-data-alteration.md#change-time-zone-of-prebuilt-datetimev2-entity) usado pela entidade predefinida datetimeV2.|
|`verbose`|boolean|Retorna todas as intenções e suas pontuações quando definidas como true. O padrão é false, que retorna apenas a intenção mais.|
|`staging`|boolean|Retorna a consulta de resultados do ambiente de preparo se definido como true. |
|`log`|boolean|Registra consultas, que podem ser usadas posteriormente para o [aprendizado ativo](luis-how-to-review-endpoint-utterances.md). O padrão é true.|

### <a name="query-published-app"></a>Consultar aplicativo publicado

Um exemplo de comando de ONDULAção para consultar o contêiner para um aplicativo publicado é:

```bash
curl -X GET \
"http://localhost:5000/luis/v2.0/apps/{APPLICATION_ID}?q=turn%20on%20the%20lights&staging=false&timezoneOffset=0&verbose=false&log=true" \
-H "accept: application/json"
```
Para fazer consultas ao ambiente de **preparo** , altere o valor do parâmetro da cadeia de caracteres de consulta de **preparo** para true: 

`staging=true`

### <a name="query-trained-app"></a>Aplicativo treinado para consulta

Um exemplo de comando de ONDULAção para consultar o contêiner para um aplicativo treinado é: 

```bash
curl -X GET \
"http://localhost:5000/luis/v2.0/apps/{APPLICATION_ID}/versions/{APPLICATION_VERSION}?q=turn%20on%20the%20lights&timezoneOffset=0&verbose=false&log=true" \
-H "accept: application/json"
```
O nome da versão tem um máximo de 10 caracteres e contém somente caracteres permitidos em uma URL. 

## <a name="import-the-endpoint-logs-for-active-learning"></a>Importar os logs de ponto de extremidade para o aprendizado ativo

Se uma montagem de saída for especificada para o contêiner LUIS, os arquivos de log de consulta de aplicativo serão salvos no diretório de saída, em que {INSTANCE_ID} é a ID do contêiner. O log de consulta do aplicativo contém a consulta, a resposta e os carimbos de data/hora para cada consulta de previsão enviada ao contêiner LUIS. 

O local a seguir mostra a estrutura de diretório aninhada para os arquivos de log do contêiner.
```
/output/luis/{INSTANCE_ID}/
```
 
No portal do LUIS, selecione seu aplicativo e, em seguida, selecione **importar logs de ponto de extremidade** para carregar esses logs. 

![Importar arquivos de log do contêiner para o aprendizado ativo](./media/luis-container-how-to/upload-endpoint-log-files.png)

Depois que o log for carregado, [examine o ponto de extremidade](https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-review-endpoint-utterances) declarações no portal do Luis.

<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="stop-the-container"></a>Parar o contêiner

Para desligar o contêiner, no ambiente de linha de comando em que o contêiner está em execução, pressione **Ctrl + C**.

## <a name="troubleshooting"></a>Resolução de problemas

Se você executar o contêiner com uma [montagem](luis-container-configuration.md#mount-settings) de saída e o registro em log habilitado, o contêiner gerará arquivos de log que são úteis para solucionar problemas que ocorrem ao iniciar ou executar o contêiner. 

## <a name="billing"></a>Faturação

O contêiner LUIS envia informações de cobrança para o Azure, usando um recurso de _Serviços cognitivas_ em sua conta do Azure. 

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Para obter mais informações sobre estas opções, consulte [configurar contentores](luis-container-configuration.md).

## <a name="supported-dependencies-for-latest-container"></a>Dependências com `latest` suporte para o contêiner

O contêiner mais recente, lançado em 2019//Build, dará suporte a:

* Verificação ortográfica do Bing: solicitações para o ponto de extremidade de previsão `&spellCheck=true&bing-spell-check-subscription-key={bingKey}` de consulta com os parâmetros de cadeia de caracteres de consulta. Use o [tutorial verificação ortográfica do Bing v7](luis-tutorial-bing-spellcheck.md) para saber mais. Se esse recurso for usado, o contêiner enviará o expressão para o recurso v7 Verificação Ortográfica do Bing.
* [Novos domínios pré-criados](luis-reference-prebuilt-domains.md): esses domínios voltados para a empresa incluem entidades, exemplos de declarações e padrões. Estenda esses domínios para seu próprio uso. 

<a name="unsupported-dependencies"></a>

## <a name="unsupported-dependencies-for-latest-container"></a>Dependências sem suporte para `latest` o contêiner

Se seu aplicativo LUIS tiver dependências sem suporte, você não poderá [exportar para o contêiner](#export-packaged-app-from-luis) até remover os recursos sem suporte. Quando você tenta exportar para o contêiner, o portal do LUIS relata os recursos sem suporte que você precisa remover.

Você pode usar um aplicativo LUIS se ele **não incluir** nenhuma das seguintes dependências:

Configurações de aplicativo sem suporte|Detalhes|
|--|--|
|Culturas de contêiner sem suporte| Holandês (NL-NL)<br>Japonês (ja-JP)<br>O alemão só tem suporte com o [criador 1.0.2](luis-language-support.md#custom-tokenizer-versions).|
|Entidades sem suporte para todas as culturas|[KeyPhrase](https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-prebuilt-keyphrase) entidade predefinida para todas as culturas|
|Entidades sem suporte para cultura Inglês (en-US)|[GeographyV2](https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-prebuilt-geographyv2) entidades predefinidas|
|Espriming de fala|Não há suporte para dependências externas no contêiner.|
|Análise de sentimentos|Não há suporte para dependências externas no contêiner.|

<!--blogs/samples/video courses -->
[!INCLUDE [Discoverability of more container information](../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>Resumo

Neste artigo, você aprendeu os conceitos e o fluxo de trabalho para baixar, instalar e executar os contêineres de Reconhecimento vocal (LUIS). Em resumo:

* O Reconhecimento vocal (LUIS) fornece um contêiner do Linux para o Docker fornecendo previsões de consulta de ponto de extremidade de declarações.
* As imagens de contêiner são baixadas do registro de contêiner da Microsoft (MCR).
* Executam imagens de contentor no Docker.
* Você pode usar a API REST para consultar os pontos de extremidade do contêiner especificando o URI do host do contêiner.
* Tem de especificar informações de faturação ao instanciar um contentor.

> [!IMPORTANT]
> Contentores de serviços cognitivos não estão licenciados para executar sem a ser ligado ao Azure para medição. Os clientes têm de ativar os contentores comunicar informações de faturação com o serviço de medição em todos os momentos. Os contêineres de serviços cognitivas não enviam dados do cliente (por exemplo, a imagem ou o texto que está sendo analisado) para a Microsoft.

## <a name="next-steps"></a>Passos seguintes

* Revisão [configurar contentores](luis-container-configuration.md) para definições de configuração
* Consulte [solução de problemas](troubleshooting.md) para resolver problemas relacionados à funcionalidade do Luis.
* Usar mais [contêineres de serviços cognitivas](../cognitive-services-container-support.md)

<!-- Links - external -->
[download-published-package]: https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/apps-packagepublishedapplicationasgzip
[download-trained-package]: https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/apps-packagetrainedapplicationasgzip