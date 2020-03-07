---
title: Contentores de estiva - LUIS
titleSuffix: Azure Cognitive Services
description: O recipiente LUIS carrega a sua app treinada ou publicada num recipiente de estivador e dá acesso às previsões de consulta dos pontos finais da API do contentor.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/08/2019
ms.author: dapine
ms.openlocfilehash: 308a474970db54022e5351fdf349d9572fbafb0d
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78390157"
---
# <a name="install-and-run-luis-docker-containers"></a>Instale e corra recipientes de estival LUIS
 
O recipiente de Compreensão da Língua (LUIS) carrega o seu modelo de compreensão linguística treinado ou publicado. Como [aplicação LUIS,](https://www.luis.ai)o recipiente de estivador proporciona acesso às previsões de consulta dos pontos finais da API do contentor. Pode recolher registos de consultas a partir do contentor e enviá-los de volta para a aplicação Language Understanding para melhorar a precisão de previsão da aplicação.

O vídeo seguinte demonstra a utilização deste recipiente.

[Demonstração de contentores de ![para serviços cognitivos](./media/luis-container-how-to/luis-containers-demo-video-still.png)](https://aka.ms/luis-container-demo)

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para executar o recipiente LUIS, note os seguintes pré-requisitos:

|Necessário|Objetivo|
|--|--|
|Motor Docker| Precisa do Motor Docker instalado num [computador de acolhimento.](#the-host-computer) O Docker fornece pacotes que configuram o ambiente Docker no [macOS,](https://docs.docker.com/docker-for-mac/) [Windows](https://docs.docker.com/docker-for-windows/)e [Linux.](https://docs.docker.com/engine/installation/#supported-platforms) Para um primer sobre o Docker e o básico do contentor, consulte a visão geral do [Docker.](https://docs.docker.com/engine/docker-overview/)<br><br> Docker tem de ser configurado para permitir que os contentores para se ligar com e enviar dados de faturação para o Azure. <br><br> **No Windows,** o Docker também deve ser configurado para suportar os recipientes Linux.<br><br>|
|Familiaridade com Docker | Você deve ter uma compreensão básica dos conceitos docker, como registos, repositórios, contentores e imagens de contentores, bem como conhecimento de comandos básicos de `docker`.| 
|Azure `Cognitive Services` recurso e arquivo [de aplicativo embalado](luis-how-to-start-new-app.md) LUIS |Para utilizar o recipiente, deve ter:<br><br>* Um recurso _Cognitive Services_ Azure e a chave de faturação associada do ponto final de faturação URI. Ambos os valores estão disponíveis nas páginas Overview e Keys para o recurso e são necessários para iniciar o recipiente. <br>* Uma aplicação treinada ou publicada embalada como entrada montada no recipiente com o seu ID de aplicação associado. Pode obter o ficheiro embalado no portal LUIS ou nas APIs de autoria. Se você está recebendo a app embalada LUIS a partir da [apis autora,](#authoring-apis-for-package-file)você também vai precisar da sua _Chave de Autor._<br><br>Estes requisitos são utilizados para passar argumentos de linha de comando para as seguintes variáveis:<br><br>**{AUTHORING_KEY}** : Esta chave é usada para obter a aplicação embalada do serviço LUIS na nuvem e carregar os registos de consulta de volta para a nuvem. O formato é `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`.<br><br>**{APP_ID}** : Este ID é utilizado para selecionar a App. O formato é `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx`.<br><br>**{API_KEY}** : Esta chave é utilizada para ligar o recipiente. Você pode encontrar a chave final em dois lugares. O primeiro é o portal Azure dentro da lista de chaves do recurso dos _Serviços Cognitivos._ A chave de ponto final também está disponível no portal LUIS na página de definições de Teclas e Ponto final. Não utilize a tecla de arranque.<br><br>**{ENDPOINT_URI}** : O ponto final fornecido na página 'Visão Geral'.<br><br>A chave de autor e a [chave de ponto final](luis-boundaries.md#key-limits) têm propósitos diferentes. Não os utilize de forma intercambiada. |

[!INCLUDE [Gathering required container parameters](../containers/includes/container-gathering-required-parameters.md)]

### <a name="authoring-apis-for-package-file"></a>Autoria de APIs para ficheiro de pacote

Autoria de APIs para aplicações embaladas:

* [Pacote publicado API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/apps-packagepublishedapplicationasgzip)
* [Pacote API não publicado e treinado](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/apps-packagetrainedapplicationasgzip)

### <a name="the-host-computer"></a>O computador anfitrião

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="container-requirements-and-recommendations"></a>Requisitos do contentor e recomendações

Este recipiente suporta valores mínimos e recomendados para as definições:

|Contentor| Mínimo | Recomendado | TPS<br>(Mínimo, Máximo)|
|-----------|---------|-------------|--|
|LUIS|1 núcleo, 2-GB de memória|1 núcleo, 4-GB de memória|20, 40|

* Cada núcleo deve ter pelo menos 2,6 gigahertz (GHz) ou mais rápido.
* TPS - transações por segundo

O núcleo e a memória correspondem às definições `--cpus` e `--memory`, que são utilizadas como parte do comando `docker run`.

## <a name="get-the-container-image-with-docker-pull"></a>Obtenha a imagem do recipiente com `docker pull`

Utilize o comando [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) para descarregar uma imagem de contentor do repositório `mcr.microsoft.com/azure-cognitive-services/luis`:

```
docker pull mcr.microsoft.com/azure-cognitive-services/luis:latest
```

Para uma descrição completa das etiquetas disponíveis, como `latest` utilizadas no comando anterior, consulte [LUIS](https://go.microsoft.com/fwlink/?linkid=2043204) no Docker Hub.

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

## <a name="how-to-use-the-container"></a>Como utilizar o recipiente

Uma vez que o recipiente esteja no [computador de acolhimento,](#the-host-computer)utilize o seguinte processo para trabalhar com o recipiente.

![Processo de utilização do recipiente de compreensão linguística (LUIS)](./media/luis-container-how-to/luis-flow-with-containers-diagram.jpg)

1. [Pacote de exportação](#export-packaged-app-from-luis) para contentor do portal LUIS ou DA APIs LUIS.
1. Mova o ficheiro do pacote para o diretório de **entrada** necessário no [computador anfitrião](#the-host-computer). Não mude o nome, altere, sobreescreva ou descomprima o ficheiro pacote LUIS.
1. [Executar o recipiente,](#run-the-container-with-docker-run)com as definições de montagem de _entrada_ e faturação necessárias. Mais [exemplos](luis-container-configuration.md#example-docker-run-commands) do comando `docker run` estão disponíveis. 
1. [Consultando o ponto final de previsão do recipiente](#query-the-containers-prediction-endpoint). 
1. Quando terminar com o recipiente, [importe os troncos](#import-the-endpoint-logs-for-active-learning) de ponto final do suporte de saída no portal LUIS e [pare](#stop-the-container) o recipiente.
1. Utilize a [aprendizagem ativa](luis-how-to-review-endpoint-utterances.md) do portal LUIS na página de **declarações** de ponto final da Revisão para melhorar a aplicação.

A aplicação que funciona no contentor não pode ser alterada. Para alterar a aplicação no recipiente, é necessário alterar a aplicação no serviço LUIS utilizando o portal [LUIS](https://www.luis.ai) ou utilizar a [APIs de autor](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c2f)da LUIS . Em seguida, treine e/ou publique, em seguida, faça o download de um novo pacote e executar o recipiente novamente.

A aplicação LUIS dentro do contentor não pode ser exportada de volta para o serviço LUIS. Só os registos de consulta podem ser carregados. 

## <a name="export-packaged-app-from-luis"></a>App embalada para exportação da LUIS

O recipiente LUIS requer uma aplicação LUIS treinada ou publicada para responder a consultas de previsão de declarações de utilizadores. Para obter a app LUIS, utilize o pacote api treinado ou publicado. 

A localização padrão é o subdiretório `input` em relação ao local onde executa o comando `docker run`.  

Coloque o ficheiro de embalagem num diretório e refira este diretório como suporte de entrada quando executar o recipiente de estivador. 

### <a name="package-types"></a>Tipos de pacotes

O diretório de montagem de entrada pode conter simultaneamente os modelos **De Produção,** **Encenação**e **Versão** da app. Todos os pacotes estão montados.

|Tipo de pacote|Consulta Endpoint API|Disponibilidade de consulta|Formato de nome de ficheiro de pacote|
|--|--|--|--|
|Versão|GET, POST|Apenas contentor|`{APP_ID}_v{APP_VERSION}.gz`|
|Encenação|GET, POST|Azul e recipiente|`{APP_ID}_STAGING.gz`|
|Produção|GET, POST|Azul e recipiente|`{APP_ID}_PRODUCTION.gz`|

> [!IMPORTANT]
> Não mude o nome, altere, sobreescreva ou descomprima os ficheiros do pacote LUIS.

### <a name="packaging-prerequisites"></a>Pré-requisitos de embalagem

Antes de embalar uma aplicação LUIS, deve ter o seguinte:

|Requisitos de embalagem|Detalhes|
|--|--|
|Instância de recursos _dos Serviços Cognitivos_ Azure|Regiões apoiadas incluem<br><br>Oeste dos EUA (`westus`)<br>Europa Ocidental (`westeurope`)<br>Austrália Leste (`australiaeast`)|
|App LUIS treinada ou publicada|Sem [dependências não apoiadas.][unsupported-dependencies] |
|Acesso ao sistema de ficheiros do [computador anfitrião](#the-host-computer) |O computador hospedeiro deve permitir uma montagem de [entrada](luis-container-configuration.md#mount-settings).|
  
### <a name="export-app-package-from-luis-portal"></a>Pacote de aplicativos de exportação do portal LUIS

O [portal](https://www.luis.ai) LUIS fornece a capacidade de exportar o pacote da app treinada ou publicada.

### <a name="export-published-apps-package-from-luis-portal"></a>Exportação publicado pacote de app do portal LUIS

O pacote da aplicação publicada está disponível na página da lista **my apps.** 

1. Inscreva-se no [portal](https://www.luis.ai)LUIS.
1. Selecione a caixa de verificação à esquerda do nome da aplicação na lista. 
1. Selecione o item **exportação** da barra de ferramentas contextual acima da lista.
1. Selecione **Exportar para recipiente (GZIP)** .
1. Selecione o ambiente de ranhura de **produção** ou ranhura de **encenação**.
1. O pacote é descarregado a partir do navegador.

![Exportar o pacote publicado para o recipiente do menu exporta da página da App](./media/luis-container-how-to/export-published-package-for-container.png)

### <a name="export-versioned-apps-package-from-luis-portal"></a>Pacote de aplicativo sonértido exportação do portal LUIS

O pacote da aplicação versão está disponível na página da lista de **Versões.**

1. Inscreva-se no [portal](https://www.luis.ai)LUIS.
1. Selecione a aplicação na lista. 
1. Selecione **Gerir** na barra de navegação da aplicação.
1. Selecione **Versões** na barra de navegação esquerda.
1. Selecione a caixa de verificação à esquerda do nome da versão na lista.
1. Selecione o item **exportação** da barra de ferramentas contextual acima da lista.
1. Selecione **Exportar para recipiente (GZIP)** .
1. O pacote é descarregado a partir do navegador.

![Exportar o pacote treinado para o recipiente a partir do menu de exportação da página versões](./media/luis-container-how-to/export-trained-package-for-container.png)

### <a name="export-published-apps-package-from-api"></a>Pacote de aplicativo publicado exportação da API

Utilize o seguinte método REST API, para embalar uma aplicação LUIS que já [publicou.](luis-how-to-publish-app.md) Substituindo os seus próprios valores adequados pelos espaços reservados na chamada API, utilizando a tabela abaixo da especificação HTTP.

```http
GET /luis/api/v2.0/package/{APP_ID}/slot/{SLOT_NAME}/gzip HTTP/1.1
Host: {AZURE_REGION}.api.cognitive.microsoft.com
Ocp-Apim-Subscription-Key: {AUTHORING_KEY}
```

| Marcador de posição | Valor |
|-------------|-------|
| **{APP_ID}** | A aplicação ID da aplicação PUBLICADA LUIS. |
| **{SLOT_NAME}** | O ambiente da aplicação PUBLICADA LUIS. Utilize um dos seguintes valores:<br/>`PRODUCTION`<br/>`STAGING` |
| **{AUTHORING_KEY}** | A chave de autor da conta LUIS para a aplicação DOT publicada.<br/>Pode obter a sua chave de autor a partir da página **Definições** do Utilizador no portal LUIS. |
| **{AZURE_REGION}** | A região de Azure apropriada:<br/><br/>`westus` - Oeste dos EUA<br/>`westeurope` - Europa Ocidental<br/>`australiaeast` - Austrália Leste |

Para fazer o download do pacote publicado, consulte a documentação da [API aqui][download-published-package]. Se tiver sido descarregado com sucesso, a resposta é um ficheiro pacote LUIS. Guarde o ficheiro no local de armazenamento especificado para o suporte de entrada do recipiente. 

### <a name="export-versioned-apps-package-from-api"></a>Pacote de aplicativo sonérquio de exportação da API

Utilize o seguinte método REST API, para embalar uma aplicação LUIS que já [treinou.](luis-how-to-train.md) Substituindo os seus próprios valores adequados pelos espaços reservados na chamada API, utilizando a tabela abaixo da especificação HTTP.

```http
GET /luis/api/v2.0/package/{APP_ID}/versions/{APP_VERSION}/gzip HTTP/1.1
Host: {AZURE_REGION}.api.cognitive.microsoft.com
Ocp-Apim-Subscription-Key: {AUTHORING_KEY}
```

| Marcador de posição | Valor |
|-------------|-------|
| **{APP_ID}** | O ID de aplicação da app LUIS treinada. |
| **{APP_VERSION}** | A versão de aplicação da app LUIS treinada. |
| **{AUTHORING_KEY}** | A chave de autor da conta LUIS para a aplicação DOT publicada.<br/>Pode obter a sua chave de autor a partir da página **Definições** do Utilizador no portal LUIS. |
| **{AZURE_REGION}** | A região de Azure apropriada:<br/><br/>`westus` - Oeste dos EUA<br/>`westeurope` - Europa Ocidental<br/>`australiaeast` - Austrália Leste |

Para descarregar o pacote versão, consulte a documentação da [API aqui][download-versioned-package]. Se tiver sido descarregado com sucesso, a resposta é um ficheiro pacote LUIS. Guarde o ficheiro no local de armazenamento especificado para o suporte de entrada do recipiente. 

## <a name="run-the-container-with-docker-run"></a>Executar o recipiente com `docker run`

Use o comando de execução de [estivador](https://docs.docker.com/engine/reference/commandline/run/) para executar o recipiente. Consulte a recolha de [parâmetros necessários](#gathering-required-parameters) para obter os valores `{ENDPOINT_URI}` e `{API_KEY}`.

[Exemplos](luis-container-configuration.md#example-docker-run-commands) do comando `docker run` estão disponíveis.

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

* Este exemplo utiliza o diretório fora da unidade de `C:` para evitar quaisquer conflitos de permissões no Windows. Se precisar de utilizar um diretório específico como a entrada de diretório, poderá ter de conceder o docker permissão de serviço. 
* Não altere a ordem dos argumentos a menos que esteja familiarizado com os recipientes de estivadores.
* Se estiver a utilizar um sistema operativo diferente, utilize a consola/terminal correto, a sintaxe de pasta para os suportes e o carácter de continuação da linha para o seu sistema. Estes exemplos assumem uma consola Windows com um carácter de continuação de linha `^`. Como o recipiente é um sistema operativo Linux, o suporte-alvo usa uma sintaxe de pasta estilo Linux.

Este comando:

* Executa um recipiente a partir da imagem do recipiente LUIS
* Loads APP LUIS do suporte de entrada em *C:\entrada,* localizado no hospedeiro de contentores
* Atribui dois núcleos de CPU e 4 gigabytes (GB) de memória
* Expõe a porta TCP 5000 e aloca um TTY pseudo para o contentor
* Guarda os registos do contentor e do LUIS para o suporte de saída em *C:\output,* localizado no hospedeiro do contentor
* Remove automaticamente o recipiente após a sua saída. A imagem do recipiente ainda está disponível no computador hospedeiro. 

Mais [exemplos](luis-container-configuration.md#example-docker-run-commands) do comando `docker run` estão disponíveis. 

> [!IMPORTANT]
> As opções de `Eula`, `Billing`e `ApiKey` devem ser especificadas para funcionar o contentor; caso contrário, o contentor não vai começar.  Para mais informações, consulte [billing.](#billing)
> O valor ApiKey é a **chave** da página De **recursos azure** no portal LUIS e também está disponível na página de chaves de recursos Do Azure `Cognitive Services`.  

[!INCLUDE [Running multiple containers on the same host](../../../includes/cognitive-services-containers-run-multiple-same-host.md)]

## <a name="endpoint-apis-supported-by-the-container"></a>APIs endpoint suportados pelo recipiente

As versões V2 e [V3](luis-migration-api-v3.md) da API estão disponíveis com o recipiente. 

## <a name="query-the-containers-prediction-endpoint"></a>Consulta do ponto final da previsão do recipiente

O recipiente fornece APIs finais de previsão de consulta baseadas em REST. Os pontos finais para aplicações publicadas (encenação ou produção) têm uma rota _diferente_ das finais para aplicações versonizadas.

Utilize o hospedeiro, `http://localhost:5000`, para apis de contentor.

# <a name="v3-prediction-endpoint"></a>[Ponto final de previsão V3](#tab/v3)

|Tipo de pacote|Verbo HTTP|Encaminhar|Parâmetros de consulta|
|--|--|--|--|
|Publicado|GET, POST|`/luis/v3.0/apps/{appId}/slots/{slotName}/predict?`|`query={query}`<br>[`&verbose`]<br>[`&log`]<br>[`&show-all-intents`]|
|Versão|GET, POST|`/luis/v3.0/apps/{appId}/versions/{versionId}/predict?`|`query={query}`<br>[`&verbose`]<br>[`&log`]<br>[`&show-all-intents`]|

Os parâmetros de consulta configuram como e o que é devolvido na resposta à consulta:

|Parâmetro de consulta|Tipo|Objetivo|
|--|--|--|
|`query`|string|A expressão do utilizador.|
|`verbose`|boolean|Um valor booleano indicando se deve devolver todos os metadados para os modelos previstos. A predefinição é falso.|
|`log`|boolean|Consultas de logs, que podem ser usadas mais tarde para [aprendizagem ativa](luis-how-to-review-endpoint-utterances.md). A predefinição é falso.|
|`show-all-intents`|boolean|Um valor booleano indicando se deve devolver todas as intenções ou apenas a intenção de pontuação superior. A predefinição é falso.|

# <a name="v2-prediction-endpoint"></a>[Ponto final da previsão V2](#tab/v2)

|Tipo de pacote|Verbo HTTP|Encaminhar|Parâmetros de consulta|
|--|--|--|--|
|Publicado|[GET,](https://westus.dev.cognitive.microsoft.com/docs/services/5819c76f40a6350ce09de1ac/operations/5819c77140a63516d81aee78) [POST](https://westus.dev.cognitive.microsoft.com/docs/services/5819c76f40a6350ce09de1ac/operations/5819c77140a63516d81aee79)|`/luis/v2.0/apps/{appId}?`|`q={q}`<br>`&staging`<br>[`&timezoneOffset`]<br>[`&verbose`]<br>[`&log`]<br>|
|Versão|GET, POST|`/luis/v2.0/apps/{appId}/versions/{versionId}?`|`q={q}`<br>[`&timezoneOffset`]<br>[`&verbose`]<br>[`&log`]|

Os parâmetros de consulta configuram como e o que é devolvido na resposta à consulta:

|Parâmetro de consulta|Tipo|Objetivo|
|--|--|--|
|`q`|string|A expressão do utilizador.|
|`timezoneOffset`|número|O timezoneOffset permite-lhe [alterar o fuso horário](luis-concept-data-alteration.md#change-time-zone-of-prebuilt-datetimev2-entity) utilizado pela data datav2 da entidade pré-construída.|
|`verbose`|boolean|Devolve todas as intenções e as suas pontuações quando é verdade. O padrão é falso, o que devolve apenas a intenção máxima.|
|`staging`|boolean|Devolve a consulta dos resultados do ambiente de encenação se for definido como verdadeiro. |
|`log`|boolean|Consultas de logs, que podem ser usadas mais tarde para [aprendizagem ativa](luis-how-to-review-endpoint-utterances.md). O padrão é verdade.|

***

### <a name="query-the-luis-app"></a>Consulta da app LUIS

Um exemplo de comando CURL para consulta do recipiente para uma aplicação publicada é:

# <a name="v3-prediction-endpoint"></a>[Ponto final de previsão V3](#tab/v3)

Para consultar um modelo numa ranhura, utilize a seguinte API:

```bash
curl -G \
-d verbose=false \
-d log=true \
--data-urlencode "query=turn the lights on" \
"http://localhost:5000/luis/v3.0/apps/{APP_ID}/slots/production/predict"
```

Para fazer consultas ao ambiente **de encenação,** substitua `production` no percurso por `staging`:

`http://localhost:5000/luis/v3.0/apps/{APP_ID}/slots/staging/predict`

Para consultar um modelo versonizado, utilize a seguinte API:

```bash
curl -G \
-d verbose=false \
-d log=false \
--data-urlencode "query=turn the lights on" \
"http://localhost:5000/luis/v3.0/apps/{APP_ID}/versions/{APP_VERSION}/predict"
```

# <a name="v2-prediction-endpoint"></a>[Ponto final da previsão V2](#tab/v2)

Para consultar um modelo numa ranhura, utilize a seguinte API:

```bash
curl -X GET \
"http://localhost:5000/luis/v2.0/apps/{APP_ID}?q=turn%20on%20the%20lights&staging=false&timezoneOffset=0&verbose=false&log=true" \
-H "accept: application/json"
```
Para fazer consultas ao ambiente **de encenação,** altere o valor do parâmetro de corda de consulta de **encenação** para verdadeiro: 

`staging=true`

Para consultar um modelo versonizado, utilize a seguinte API:

```bash
curl -X GET \
"http://localhost:5000/luis/v2.0/apps/{APP_ID}/versions/{APP_VERSION}?q=turn%20on%20the%20lights&timezoneOffset=0&verbose=false&log=true" \
-H "accept: application/json"
```
O nome da versão tem um máximo de 10 caracteres e contém apenas caracteres permitidos num URL.

***

## <a name="import-the-endpoint-logs-for-active-learning"></a>Importar os registos finais para a aprendizagem ativa

Se for especificado um suporte de saída para o recipiente LUIS, os ficheiros de registo de consulta de aplicações são guardados no diretório de saída, onde `{INSTANCE_ID}` é o ID do recipiente. O registo de consulta da aplicação contém a consulta, resposta e carimbos de tempo para cada consulta de previsão submetida ao recipiente LUIS. 

O seguinte local mostra a estrutura de diretório aninhada para os ficheiros de registo do contentor.
```
/output/luis/{INSTANCE_ID}/
```
 
A partir do portal LUIS, selecione a sua aplicação e, em seguida, selecione **registos de pontofinal de Importação** para fazer upload destes registos. 

![Importaos ficheiros de registo do contentor para aprendizagem ativa](./media/luis-container-how-to/upload-endpoint-log-files.png)

Depois de o registo ser carregado, [reveja as](https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-review-endpoint-utterances) declarações de ponto final no portal LUIS.

<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="stop-the-container"></a>Pare o recipiente

Para desligar o recipiente, no ambiente da linha de comando onde o contentor está em funcionamento, prima **Ctrl+C**.

## <a name="troubleshooting"></a>Resolução de problemas

Se executar o recipiente com um [suporte](luis-container-configuration.md#mount-settings) de saída e uma exploração de madeira ativada, o recipiente gera ficheiros de registo que são úteis para resolver problemas que ocorrem durante o arranque ou funcionamento do recipiente.

[!INCLUDE [Cognitive Services FAQ note](../containers/includes/cognitive-services-faq-note.md)]

## <a name="billing"></a>Faturação

O recipiente LUIS envia informações de faturação para o Azure, utilizando um recurso _dos Serviços Cognitivos_ na sua conta Azure. 

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Para mais informações sobre estas opções, consulte [os recipientes Configur.](luis-container-configuration.md)

<!--blogs/samples/video courses -->
[!INCLUDE [Discoverability of more container information](../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>Resumo

Neste artigo, aprendeu conceitos e fluxo sinuoso para descarregar, instalar e executar recipientes de Compreensão de Linguagem (LUIS). Em resumo:

* A Compreensão da Linguagem (LUIS) fornece um recipiente Linux para docker fornecendo previsões de consulta de ponto final de expressões.
* As imagens do contentor são descarregadas a partir do Registo de Contentores da Microsoft (MCR).
* Executam imagens de contentor no Docker.
* Pode utilizar a API REST para consultar os pontos finais do recipiente especificando o uri hospedeiro do recipiente.
* Tem de especificar informações de faturação ao instanciar um contentor.

> [!IMPORTANT]
> Contentores de serviços cognitivos não estão licenciados para executar sem a ser ligado ao Azure para medição. Os clientes têm de ativar os contentores comunicar informações de faturação com o serviço de medição em todos os momentos. Os recipientes dos Serviços Cognitivos não enviam dados dos clientes (por exemplo, a imagem ou texto que está a ser analisado) para a Microsoft.

## <a name="next-steps"></a>Passos seguintes

* Reveja os recipientes de [configuração](luis-container-configuration.md) para configurações de configuração.
* Consulte [as limitações](luis-container-limitations.md) do contentor LUIS para restrições de capacidade conhecidas.
* Consulte a [Resolução de Problemas](troubleshooting.md) para resolver problemas relacionados com a funcionalidade LUIS.
* Use mais [recipientes de serviços cognitivos](../cognitive-services-container-support.md)

<!-- Links - external -->
[download-published-package]: https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/apps-packagepublishedapplicationasgzip
[download-versioned-package]: https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/apps-packagetrainedapplicationasgzip

[unsupported-dependencies]: luis-container-limitations.md#unsupported-dependencies-for-latest-container
