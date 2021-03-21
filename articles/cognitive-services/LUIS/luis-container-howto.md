---
title: Instale e execute os recipientes Docker para o LUIS
titleSuffix: Azure Cognitive Services
description: Use o recipiente LUIS para carregar a sua aplicação treinada ou publicada e tenha acesso às suas previsões no local.
services: cognitive-services
author: aahill
manager: nitinme
ms.custom: seodec18, cog-serv-seo-aug-2020
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 03/02/2021
ms.author: aahi
keywords: no local, Docker, contentor
ms.openlocfilehash: e157e976186f03aa984877435c42b996ce476740
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102040197"
---
# <a name="install-and-run-docker-containers-for-luis"></a>Instale e execute os recipientes Docker para o LUIS

[!INCLUDE [container image location note](../containers/includes/image-location-note.md)]

Os recipientes permitem-lhe utilizar o LUIS no seu próprio ambiente. Os contentores são ótimos para requisitos específicos de governação de dados e segurança. Neste artigo você vai aprender a baixar, instalar e executar um recipiente LUIS.

O recipiente Language Understanding (LUIS) carrega o seu modelo de Compreensão linguística treinado ou publicado. Como [uma aplicação LUIS,](https://www.luis.ai)o estivador fornece acesso às previsões de consulta dos pontos finais da API do contentor. Pode recolher registos de consultas a partir do contentor e enviá-los de volta para a aplicação Dein entendimento de idiomas para melhorar a precisão de previsão da aplicação.

O vídeo que se segue demonstra a utilização deste recipiente.

[![Demonstração de contentores para serviços cognitivos](./media/luis-container-how-to/luis-containers-demo-video-still.png)](https://aka.ms/luis-container-demo)

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/cognitive-services/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para executar o recipiente LUIS, note os seguintes pré-requisitos:

|Necessário|Objetivo|
|--|--|
|Motor do Docker| Precisa do Motor Docker instalado num [computador anfitrião.](#the-host-computer) O Docker oferece pacotes que configuram o ambiente do Docker no [macOS](https://docs.docker.com/docker-for-mac/), no [Windows](https://docs.docker.com/docker-for-windows/) e no [Linux](https://docs.docker.com/engine/installation/#supported-platforms). Para um manual de noções básicas do Docker e do contentor, veja a [descrição geral do Docker](https://docs.docker.com/engine/docker-overview/).<br><br> O Docker deve ser configurado para permitir que os contentores se conectem e enviem dados de faturação para a Azure. <br><br> **No Windows,** o Docker também deve ser configurado para suportar recipientes Linux.<br><br>|
|Familiaridade com Docker | Você deve ter uma compreensão básica de conceitos docker, como registos, repositórios, contentores e imagens de contentores, bem como conhecimento de `docker` comandos básicos.|
|Recurso Azure `Cognitive Services` e ficheiro [de aplicações embalados](luis-how-to-start-new-app.md) pela LUIS |Para utilizar o recipiente, deve ter:<br><br>* Um recurso _de Azure dos Serviços Cognitivos_ e a chave de faturação associada o ponto final de faturação URI. Ambos os valores estão disponíveis nas páginas 'Visão Geral' e Chaves para o recurso e são obrigados a iniciar o contentor. <br>* Uma aplicação treinada ou publicada embalada como uma entrada montada no recipiente com o seu ID de aplicação associado. Pode obter o ficheiro embalado do portal LUIS ou das APIs de Autoria. Se estiver a receber a app embalada LUIS das APIs de [autoria,](#authoring-apis-for-package-file)também necessitará da sua _Chave de Autoria._<br><br>Estes requisitos são utilizados para passar os argumentos da linha de comando para as seguintes variáveis:<br><br>**{AUTHORING_KEY}**: Esta chave é utilizada para obter a aplicação embalada do serviço LUIS na nuvem e carregar os registos de consulta de volta para a nuvem. O formato é `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`.<br><br>**{APP_ID}**: Este ID é utilizado para selecionar a App. O formato é `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx`.<br><br>**{API_KEY}**: Esta tecla é utilizada para iniciar o recipiente. Pode encontrar a chave do ponto final em dois lugares. O primeiro é o portal Azure dentro da lista de chaves dos _serviços cognitivos._ A tecla de ponto final também está disponível no portal LUIS na página de definições de Chaves e Ponto Final. Não utilize a chave de arranque.<br><br>**{ENDPOINT_URI}**: O ponto final, conforme fornecido na página 'Vista Geral'.<br><br>A [chave de autoria e a chave de ponta](luis-limits.md#key-limits) têm propósitos diferentes. Não os utilize de forma intermutável. |

[!INCLUDE [Gathering required container parameters](../containers/includes/container-gathering-required-parameters.md)]

### <a name="authoring-apis-for-package-file"></a>ApIs de autoria para ficheiro de pacote

Autoria de APIs para aplicações embaladas:

* [Pacote publicado API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/apps-packagepublishedapplicationasgzip)
* [API de pacotes treinados e não publicados](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/apps-packagetrainedapplicationasgzip)

### <a name="the-host-computer"></a>O computador anfitrião

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="container-requirements-and-recommendations"></a>Requisitos e recomendações do contentor

A tabela abaixo lista valores mínimos e recomendados para o hospedeiro do recipiente. Os seus requisitos podem mudar dependendo do volume de tráfego.

|Contentor| Mínimo | Recomendado | TPS<br>(Mínimo, Máximo)|
|-----------|---------|-------------|--|
|LUIS|1 núcleo, memória de 2 GB|1 núcleo, memória de 4-GB|20, 40|

* Cada núcleo deve ser pelo menos 2,6 gigahertz (GHz) ou mais rápido.
* TPS - transações por segundo

O núcleo e a memória correspondem às `--cpus` `--memory` definições e configurações, que são utilizadas como parte do `docker run` comando.

## <a name="get-the-container-image-with-docker-pull"></a>Obtenha a imagem do recipiente com `docker pull`

Utilize o [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) comando para descarregar uma imagem de contentor do `mcr.microsoft.com/azure-cognitive-services/language/luis` repositório:

```
docker pull mcr.microsoft.com/azure-cognitive-services/language/luis:latest
```

Para obter uma descrição completa das etiquetas disponíveis, tais como `latest` utilizadas no comando anterior, consulte [LUIS](https://go.microsoft.com/fwlink/?linkid=2043204) no Docker Hub.

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

## <a name="how-to-use-the-container"></a>Como usar o recipiente

Uma vez que o recipiente esteja no [computador anfitrião,](#the-host-computer)utilize o seguinte processo para trabalhar com o recipiente.

![Processo de utilização do recipiente de compreensão linguística (LUIS)](./media/luis-container-how-to/luis-flow-with-containers-diagram.jpg)

1. [Pacote de exportação](#export-packaged-app-from-luis) para contentor a partir do portal LUIS ou APIs LUIS.
1. Desloque o ficheiro de pacote para o diretório de **entrada** necessário no [computador anfitrião](#the-host-computer). Não mude o nome, altere, substitua ou descompromete o ficheiro de embalagem LUIS.
1. [Executar o recipiente,](#run-the-container-with-docker-run)com as definições de _entrada_ e faturação necessárias. Mais [exemplos](luis-container-configuration.md#example-docker-run-commands) do `docker run` comando estão disponíveis.
1. [Consultando o ponto final de previsão do recipiente.](#query-the-containers-prediction-endpoint)
1. Quando terminar com o recipiente, [importe os troncos](#import-the-endpoint-logs-for-active-learning) de ponto final do suporte de saída no portal LUIS e [pare](#stop-the-container) o recipiente.
1. Utilize a [aprendizagem ativa](luis-how-to-review-endpoint-utterances.md) do portal LUIS na página **de comentários de ponto final do Ponto final** para melhorar a aplicação.

A aplicação que funciona no contentor não pode ser alterada. Para alterar a aplicação no recipiente, é necessário alterar a aplicação no serviço LUIS utilizando o portal [LUIS](https://www.luis.ai) ou utilizar as [APIs de autoria](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c2f)luis. Em seguida, treine e/ou publique, em seguida, faça o download de um novo pacote e volte a executar o recipiente.

A aplicação LUIS no interior do contentor não pode ser exportada de volta para o serviço LUIS. Apenas os registos de consulta podem ser carregados.

## <a name="export-packaged-app-from-luis"></a>App embalada de exportação da LUIS

O contentor LUIS requer uma aplicação LUIS treinada ou publicada para responder a consultas de previsão de declarações de utilizadores. Para obter a app LUIS, utilize a API de pacote treinado ou publicado.

A localização predefinitiva é a `input` subdiretória em relação ao local onde corre o `docker run` comando.

Coloque o ficheiro de embalagem num diretório e refira este diretório como o suporte de entrada quando estiver a executar o recipiente do estivador.

### <a name="package-types"></a>Tipos de pacotes

O diretório de suporte de entrada pode conter simultaneamente os modelos **Production,** **Staging** e **Versioned** da aplicação. Todos os pacotes estão montados.

|Tipo de pacote|Consulta Endpoint API|Disponibilidade de consulta|Formato de nome de ficheiro de pacote|
|--|--|--|--|
|Versão|GET, POST|Apenas contentor|`{APP_ID}_v{APP_VERSION}.gz`|
|Processo de teste|GET, POST|Azul e contentor|`{APP_ID}_STAGING.gz`|
|Produção|GET, POST|Azul e contentor|`{APP_ID}_PRODUCTION.gz`|

> [!IMPORTANT]
> Não mude o nome, altere, sobreescreva ou descomprimir os ficheiros do pacote LUIS.

### <a name="packaging-prerequisites"></a>Pré-requisitos de embalagem

Antes de embalar uma aplicação LUIS, deve ter o seguinte:

|Requisitos de embalagem|Detalhes|
|--|--|
|Exemplo de recurso _dos Serviços Cognitivos_ Azure|As regiões apoiadas incluem<br><br>Oeste dos EUA ( `westus` )<br>Europa Ocidental ( `westeurope` )<br>Austrália Leste ( `australiaeast` )|
|Aplicativo LUIS treinado ou publicado|Sem [dependências não apoiadas.][unsupported-dependencies] |
|Acesso ao sistema de ficheiros do [computador anfitrião](#the-host-computer) |O computador anfitrião deve permitir uma [montagem de entrada](luis-container-configuration.md#mount-settings).|

### <a name="export-app-package-from-luis-portal"></a>Pacote de aplicativos de exportação do portal LUIS

O [portal](https://www.luis.ai) LUIS oferece a capacidade de exportar o pacote da app treinada ou publicada.

### <a name="export-published-apps-package-from-luis-portal"></a>Pacote de aplicação publicado pela exporte do portal LUIS

O pacote da aplicação publicada está disponível na página da lista **My Apps.**

1. Inscreva-se no [portal](https://www.luis.ai)LUIS.
1. Selecione a caixa de verificação à esquerda do nome da aplicação na lista.
1. Selecione o item **Exportação** da barra de ferramentas contextual acima da lista.
1. Selecione **Exportação para recipiente (GZIP)**.
1. Selecione o ambiente da ranhura de **produção** ou **da ranhura de encenação**.
1. O pacote é descarregado do navegador.

![Exporte o pacote publicado para o recipiente a partir do menu de exportação da página da App](./media/luis-container-how-to/export-published-package-for-container.png)

### <a name="export-versioned-apps-package-from-luis-portal"></a>Pacote de app versão de exportação do portal LUIS

O pacote da aplicação em versão está disponível na página da lista **de versões.**

1. Inscreva-se no [portal](https://www.luis.ai)LUIS.
1. Selecione a aplicação na lista.
1. **Selecione Gerir** na barra de navegação da aplicação.
1. Selecione **Versões** na barra de navegação esquerda.
1. Selecione a caixa de verificação à esquerda do nome da versão na lista.
1. Selecione o item **Exportação** da barra de ferramentas contextual acima da lista.
1. Selecione **Exportação para recipiente (GZIP)**.
1. O pacote é descarregado do navegador.

![Exporte o pacote treinado para o recipiente a partir do menu exportação da página versões](./media/luis-container-how-to/export-trained-package-for-container.png)

### <a name="export-published-apps-package-from-api"></a>Pacote de aplicações publicados pela Export a partir da API

Utilize o seguinte método REST API, para embalar uma aplicação LUIS que já [publicou.](luis-how-to-publish-app.md) Substituindo os seus próprios valores adequados para os espaços reservados na chamada API, utilizando o quadro abaixo da especificação HTTP.

```http
GET /luis/api/v2.0/package/{APP_ID}/slot/{SLOT_NAME}/gzip HTTP/1.1
Host: {AZURE_REGION}.api.cognitive.microsoft.com
Ocp-Apim-Subscription-Key: {AUTHORING_KEY}
```

| Marcador de posição | Valor |
|-------------|-------|
| **{APP_ID}** | O ID da aplicação da aplicação LUIS publicada. |
| **{SLOT_NAME}** | O ambiente da aplicação LUIS publicada. Utilize um dos seguintes valores:<br/>`PRODUCTION`<br/>`STAGING` |
| **{AUTHORING_KEY}** | A chave de autoria da conta LUIS para a aplicação LUIS publicada.<br/>Pode obter a sua chave de autoria na página **Definições** do Utilizador no portal LUIS. |
| **{AZURE_REGION}** | A região de Azure adequada:<br/><br/>`westus` - Oeste dos EUA<br/>`westeurope` - Europa Ocidental<br/>`australiaeast` - Austrália Leste |

Para descarregar o pacote publicado, consulte a documentação da [API aqui.][download-published-package] Se for descarregado com sucesso, a resposta é um ficheiro de pacote LUIS. Guarde o ficheiro no local de armazenamento especificado para o suporte de entrada do recipiente.

### <a name="export-versioned-apps-package-from-api"></a>Pacote de app versão de exportação da API

Utilize o seguinte método REST API, para embalar uma aplicação LUIS que já [treinou.](luis-how-to-train.md) Substituindo os seus próprios valores adequados para os espaços reservados na chamada API, utilizando o quadro abaixo da especificação HTTP.

```http
GET /luis/api/v2.0/package/{APP_ID}/versions/{APP_VERSION}/gzip HTTP/1.1
Host: {AZURE_REGION}.api.cognitive.microsoft.com
Ocp-Apim-Subscription-Key: {AUTHORING_KEY}
```

| Marcador de posição | Valor |
|-------------|-------|
| **{APP_ID}** | Identificação de aplicação da app LUIS treinada. |
| **{APP_VERSION}** | A versão de aplicação da app LUIS treinada. |
| **{AUTHORING_KEY}** | A chave de autoria da conta LUIS para a aplicação LUIS publicada.<br/>Pode obter a sua chave de autoria na página **Definições** do Utilizador no portal LUIS. |
| **{AZURE_REGION}** | A região de Azure adequada:<br/><br/>`westus` - Oeste dos EUA<br/>`westeurope` - Europa Ocidental<br/>`australiaeast` - Austrália Leste |

Para descarregar o pacote de versão, consulte a documentação da [API aqui.][download-versioned-package] Se for descarregado com sucesso, a resposta é um ficheiro de pacote LUIS. Guarde o ficheiro no local de armazenamento especificado para o suporte de entrada do recipiente.

## <a name="run-the-container-with-docker-run"></a>Executar o recipiente com `docker run`

Use o comando de execução do [estivador](https://docs.docker.com/engine/reference/commandline/run/) para executar o contentor. Consulte a [recolha dos parâmetros necessários](#gathering-required-parameters) para obter detalhes sobre como obter os `{ENDPOINT_URI}` valores e `{API_KEY}` valores.

[Exemplos](luis-container-configuration.md#example-docker-run-commands) do `docker run` comando estão disponíveis.

```console
docker run --rm -it -p 5000:5000 ^
--memory 4g ^
--cpus 2 ^
--mount type=bind,src=c:\input,target=/input ^
--mount type=bind,src=c:\output\,target=/output ^
mcr.microsoft.com/azure-cognitive-services/language/luis ^
Eula=accept ^
Billing={ENDPOINT_URI} ^
ApiKey={API_KEY}
```

* Este exemplo utiliza o diretório fora da `C:` unidade para evitar quaisquer conflitos de permissão no Windows. Se precisar de utilizar um diretório específico como diretório de entrada, poderá ter de conceder a permissão de serviço do estivador.
* Não altere a ordem dos argumentos a menos que esteja familiarizado com os recipientes de estivadores.
* Se estiver a utilizar um sistema operativo diferente, utilize a sintaxe correta da consola/terminal, a sintaxe da pasta para os suportes e o carácter de continuação da linha para o seu sistema. Estes exemplos assumem uma consola Windows com um carácter de continuação de `^` linha. Como o recipiente é um sistema operativo Linux, o suporte alvo utiliza uma sintaxe de pasta ao estilo Linux.

Este comando:

* Executa um contentor a partir da imagem do recipiente LUIS
* Carrega a aplicação LUIS a partir do suporte de entrada em *C:\input*, localizada no hospedeiro do contentor
* Atribui dois núcleos de CPU e 4 gigabytes (GB) de memória
* Expõe a porta TCP 5000 e atribui uma pseudo-TTY para o contentor
* Guarda os troncos de contentores e LUIS para o suporte de saída em *C:\output*, localizado no hospedeiro do contentor
* Remove automaticamente o recipiente depois de sair. A imagem do recipiente ainda está disponível no computador anfitrião.

Mais [exemplos](luis-container-configuration.md#example-docker-run-commands) do `docker run` comando estão disponíveis.

> [!IMPORTANT]
> As `Eula` `Billing` opções , e `ApiKey` opções devem ser especificadas para executar o recipiente; caso contrário, o recipiente não arranca.  Para mais informações, consulte [Billing.](#billing)
> O valor ApiKey é a **chave** da página **Recursos Azure** no portal LUIS e também está disponível na página de chaves de recursos Azure. `Cognitive Services`

[!INCLUDE [Running multiple containers on the same host](../../../includes/cognitive-services-containers-run-multiple-same-host.md)]

## <a name="endpoint-apis-supported-by-the-container"></a>APIs de ponto final suportados pelo contentor

As versões V2 e [V3](luis-migration-api-v3.md) da API estão disponíveis com o recipiente.

## <a name="query-the-containers-prediction-endpoint"></a>Consultar o ponto final de predição do contentor

O contentor fornece APIs de ponto final de predição de consulta com base em REST. Os pontos finais para aplicações publicadas (encenação ou produção) têm uma rota _diferente_ dos pontos finais para aplicações versadas.

Utilize o anfitrião, `http://localhost:5000`, para APIs de contentor.

# <a name="v3-prediction-endpoint"></a>[Ponto final de previsão V3](#tab/v3)

|Tipo de pacote|Verbo HTTP|Rota|Parâmetros de consulta|
|--|--|--|--|
|Publicado|GET, POST|`/luis/v3.0/apps/{appId}/slots/{slotName}/predict?`|`query={query}`<br>[`&verbose`]<br>[`&log`]<br>[`&show-all-intents`]|
|Versão|GET, POST|`/luis/v3.0/apps/{appId}/versions/{versionId}/predict?`|`query={query}`<br>[`&verbose`]<br>[`&log`]<br>[`&show-all-intents`]|

Os parâmetros de consulta configuram como e o que é devolvido na resposta de consulta:

|Parâmetro de consulta|Tipo|Objetivo|
|--|--|--|
|`query`|string|A expressão do utilizador.|
|`verbose`|boolean|Um valor booleano que indica a devolução de todos os metadados para os modelos previstos. A predefinição é falso.|
|`log`|boolean|Regista consultas, que podem ser usadas mais tarde para [a aprendizagem ativa.](luis-how-to-review-endpoint-utterances.md) A predefinição é falso.|
|`show-all-intents`|boolean|Um valor booleano que indica se deve devolver todas as intenções ou a intenção de pontuação superior apenas. A predefinição é falso.|

# <a name="v2-prediction-endpoint"></a>[Ponto final de previsão V2](#tab/v2)

|Tipo de pacote|Verbo HTTP|Rota|Parâmetros de consulta|
|--|--|--|--|
|Publicado|[GET](https://westus.dev.cognitive.microsoft.com/docs/services/5819c76f40a6350ce09de1ac/operations/5819c77140a63516d81aee78), [POST](https://westus.dev.cognitive.microsoft.com/docs/services/5819c76f40a6350ce09de1ac/operations/5819c77140a63516d81aee79)|`/luis/v2.0/apps/{appId}?`|`q={q}`<br>`&staging`<br>[`&timezoneOffset`]<br>[`&verbose`]<br>[`&log`]<br>|
|Versão|GET, POST|`/luis/v2.0/apps/{appId}/versions/{versionId}?`|`q={q}`<br>[`&timezoneOffset`]<br>[`&verbose`]<br>[`&log`]|

Os parâmetros de consulta configuram como e o que é devolvido na resposta de consulta:

|Parâmetro de consulta|Tipo|Objetivo|
|--|--|--|
|`q`|string|A expressão do utilizador.|
|`timezoneOffset`|número|O timezoneOffset permite [alterar o timezone](luis-concept-data-alteration.md#change-time-zone-of-prebuilt-datetimev2-entity) utilizado pela data V2 da entidade pré-construída.|
|`verbose`|boolean|Devolve todas as intenções e as suas pontuações quando definidas como verdadeiras. O padrão é falso, que devolve apenas a intenção superior.|
|`staging`|boolean|Devolve a consulta dos resultados do ambiente de encenação se definido como verdadeiro. |
|`log`|boolean|Regista consultas, que podem ser usadas mais tarde para [a aprendizagem ativa.](luis-how-to-review-endpoint-utterances.md) A predefinição é verdadeiro.|

***

### <a name="query-the-luis-app"></a>Consulta da app LUIS

Um comando CURL de exemplo para consulta do recipiente para uma aplicação publicada é:

# <a name="v3-prediction-endpoint"></a>[Ponto final de previsão V3](#tab/v3)

Para consultar um modelo numa ranhura, utilize a seguinte API:

```bash
curl -G \
-d verbose=false \
-d log=true \
--data-urlencode "query=turn the lights on" \
"http://localhost:5000/luis/v3.0/apps/{APP_ID}/slots/production/predict"
```

Para fazer consultas ao ambiente **de encenação,** substitua-se `production` no percurso `staging` com:

`http://localhost:5000/luis/v3.0/apps/{APP_ID}/slots/staging/predict`

Para consultar um modelo em versão, utilize a seguinte API:

```bash
curl -G \
-d verbose=false \
-d log=false \
--data-urlencode "query=turn the lights on" \
"http://localhost:5000/luis/v3.0/apps/{APP_ID}/versions/{APP_VERSION}/predict"
```

# <a name="v2-prediction-endpoint"></a>[Ponto final de previsão V2](#tab/v2)

Para consultar um modelo numa ranhura, utilize a seguinte API:

```bash
curl -X GET \
"http://localhost:5000/luis/v2.0/apps/{APP_ID}?q=turn%20on%20the%20lights&staging=false&timezoneOffset=0&verbose=false&log=true" \
-H "accept: application/json"
```
Para fazer consultas ao ambiente **de encenação,** altere o valor do parâmetro de linha de **preparação** para verdadeiro:

`staging=true`

Para consultar um modelo em versão, utilize a seguinte API:

```bash
curl -X GET \
"http://localhost:5000/luis/v2.0/apps/{APP_ID}/versions/{APP_VERSION}?q=turn%20on%20the%20lights&timezoneOffset=0&verbose=false&log=true" \
-H "accept: application/json"
```
O nome da versão tem um máximo de 10 caracteres e contém apenas caracteres permitidos num URL.

***

## <a name="import-the-endpoint-logs-for-active-learning"></a>Importar os registos de ponto final para a aprendizagem ativa

Se for especificado um suporte de saída para o contentor LUIS, os ficheiros de registo de consulta de aplicações são guardados no diretório de saída, onde `{INSTANCE_ID}` está o ID do contentor. O registo de consulta de aplicações contém a consulta, resposta e os horários de cada consulta de previsão submetida ao recipiente LUIS.

A localização seguinte mostra a estrutura aninhada do diretório para os ficheiros de registo do contentor.
```
/output/luis/{INSTANCE_ID}/
```

A partir do portal LUIS, selecione a sua aplicação e, em seguida, selecione **registos de ponto final de Importação** para carregar estes registos.

![Arquivos de registo de contentores de importação para aprendizagem ativa](./media/luis-container-how-to/upload-endpoint-log-files.png)

Depois do registo ser carregado, [reveja as](./luis-concept-review-endpoint-utterances.md) expressões do ponto final no portal LUIS.

<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="stop-the-container"></a>Parar o contentor

Para desligar o recipiente, no ambiente de linha de comando onde o recipiente está em funcionamento, prima **Ctrl+C**.

## <a name="troubleshooting"></a>Resolução de problemas

Se executar o recipiente com um [suporte](luis-container-configuration.md#mount-settings) de saída e um registo ativado, o recipiente gera ficheiros de registo que são úteis para resolver problemas que ocorrem durante o arranque ou execução do recipiente.

[!INCLUDE [Cognitive Services FAQ note](../containers/includes/cognitive-services-faq-note.md)]

## <a name="billing"></a>Faturação

O contentor LUIS envia informações de faturação para a Azure, utilizando um recurso _de Serviços Cognitivos_ na sua conta Azure.

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Para obter mais informações sobre estas opções, consulte [os recipientes Configure](luis-container-configuration.md).

## <a name="summary"></a>Resumo

Neste artigo, aprendeu conceitos e fluxo de trabalho para descarregar, instalar e executar recipientes de Compreensão linguística (LUIS). Em resumo:

* A Compreensão linguística (LUIS) fornece um recipiente Linux para Docker que fornece previsões de consultas finais de expressões.
* As imagens do contentor são descarregadas a partir do Registo de Contentores da Microsoft (MCR).
* Imagens de contentores correm em Docker.
* Pode utilizar a API REST para consultar os pontos finais do contentor especificando o hospedeiro URI do recipiente.
* Deve especificar as informações de faturação ao instantaneamente um recipiente.

> [!IMPORTANT]
> Os recipientes dos Serviços Cognitivos não estão licenciados para funcionar sem estarem ligados ao Azure para a medição. Os clientes precisam de permitir que os contentores comuniquem informações de faturação com o serviço de medição em todos os momentos. Os recipientes de Serviços Cognitivos não enviam dados do cliente (por exemplo, a imagem ou texto que está a ser analisado) para a Microsoft.

## <a name="next-steps"></a>Passos seguintes

* [Reveja os recipientes de configuração](luis-container-configuration.md) para configurações.
* Consulte [as limitações do contentor LUIS](luis-container-limitations.md) para as restrições de capacidade conhecidas.
* Consulte a [Resolução de Problemas](troubleshooting.md) para resolver problemas relacionados com a funcionalidade LUIS.
* Use mais [recipientes de serviços cognitivos](../cognitive-services-container-support.md)

<!-- Links - external -->
[download-published-package]: https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/apps-packagepublishedapplicationasgzip
[download-versioned-package]: https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/apps-packagetrainedapplicationasgzip

[unsupported-dependencies]: luis-container-limitations.md#unsupported-dependencies-for-latest-container