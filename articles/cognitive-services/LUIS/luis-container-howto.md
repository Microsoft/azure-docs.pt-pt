---
title: Contentores do Docker
titleSuffix: Language Understanding - Azure Cognitive Services
description: O contentor de LUIS carrega a aplicação publicada ou preparada para um contentor do docker e fornece acesso para as previsões de consulta de pontos finais da API do contentor.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 03/19/2019
ms.author: diberry
ms.openlocfilehash: 1cf5fb00e9f1a202fe7ad46253f916e3e6bee7a7
ms.sourcegitcommit: ab6fa92977255c5ecbe8a53cac61c2cd2a11601f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58295577"
---
# <a name="install-and-run-luis-docker-containers"></a>Instalar e executar o LUIS contentores do docker
 
O contentor de compreensão de idiomas (LUIS) carrega o modelo de compreensão de idiomas treinado ou publicado, também conhecido como um [aplicação LUIS](https://www.luis.ai), para um contentor de docker e fornece acesso para as previsões de consulta da API do contentor pontos de extremidade. Pode recolher registos de consulta do contêiner e carregar esses back para o modelo de compreensão de idiomas do Azure para melhorar a exatidão da previsão da aplicação.

O vídeo seguinte demonstra como utilizar este contentor.

[![Demonstração de contentor para os serviços cognitivos](./media/luis-container-how-to/luis-containers-demo-video-still.png)](https://aka.ms/luis-container-demo)

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para executar o contentor de LUIS, tem de ter o seguinte: 

|Necessário|Objetivo|
|--|--|
|Motor do docker| É necessário o motor do Docker instalado num [computador anfitrião](#the-host-computer). Docker disponibiliza pacotes que configurar o ambiente do Docker num [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/), e [Linux](https://docs.docker.com/engine/installation/#supported-platforms). Para obter um manual sobre noções básicas do Docker e um contentor, consulte a [descrição geral do Docker](https://docs.docker.com/engine/docker-overview/).<br><br> Docker tem de ser configurado para permitir que os contentores para se ligar com e enviar dados de faturação para o Azure. <br><br> **No Windows**, Docker também tem de ser configurado para dar suporte a contentores do Linux.<br><br>|
|Familiaridade com o Docker | Deve ter uma noção básica dos conceitos do Docker, como registos, repositórios, contentores e imagens de contentor, bem como dados de conhecimento do basic `docker` comandos.| 
|Recurso da compreensão (LUIS) de idioma e a aplicação associada |Para utilizar o contentor, tem de ter:<br><br>* A [ _compreensão de idiomas_ recursos do Azure](luis-how-to-azure-subscription.md), juntamente com a chave de ponto de extremidade associado e o ponto final do URI (utilizada como o ponto final de faturação).<br>* Uma aplicação publicada ou preparada empacotada como uma entrada montada no contentor com o seu ID de aplicação associada.<br>* A chave de criação de conteúdos para transferir o pacote de aplicação, se estiver fazendo isso da API.<br><br>Estes requisitos são utilizados para transmitir os argumentos da linha de comandos para as seguintes variáveis:<br><br>**{AUTHORING_KEY}**: Esta chave é utilizada para obter a aplicação em pacote de serviço do LUIS na cloud e carregar os registos de consulta de volta para a cloud. O formato é `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`.<br><br>**{APPLICATION_ID}** : Este ID é utilizado para selecionar a aplicação. O formato é `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx`.<br><br>**{ENDPOINT_KEY}**: Esta chave é utilizada para iniciar o contentor. Pode encontrar a chave de ponto final em dois locais. A primeira é o portal do Azure dentro de _compreensão de idiomas_ lista de chaves do recurso. Chave do ponto final também está disponível no portal do LUIS nas chaves e ponto final de página de definições. Não utilize a chave de arranque.<br><br>**{BILLING_ENDPOINT}**: O valor de ponto final de faturação está disponível na página de descrição geral de compreensão de idioma do portal do Azure. Um exemplo é: `https://westus.api.cognitive.microsoft.com/luis/v2.0`.<br><br>O [chave e a chave de ponto final de criação](luis-boundaries.md#key-limits) com objetivos diferentes. Não utilize-os alternadamente. |

### <a name="the-host-computer"></a>O computador anfitrião

[!INCLUDE [Request access to private preview](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="container-requirements-and-recommendations"></a>Requisitos do contentor e recomendações

Este contentor suporta valores mínimos e recomendados para as definições:

|Contentor| Mínimo | Recomendado | TPS<br>(No mínimo, máximo)|
|-----------|---------|-------------|--|
|LUIS|1 núcleo, 2 GB de memória|1 núcleo, 4 GB de memória|20,40|

* Cada principal tem de ser, pelo menos, de 2,6 GHz (gigahertz) ou mais rápido.
* TPS - transações por segundo

Núcleos e memória correspondem para o `--cpus` e `--memory` as definições, que são utilizadas como parte do `docker run` comando.

## <a name="get-the-container-image-with-docker-pull"></a>Obter a imagem de contentor com o `docker pull`

Utilize o [ `docker pull` ](https://docs.docker.com/engine/reference/commandline/pull/) comando para transferir uma imagem de contentor a `mcr.microsoft.com/azure-cognitive-services/luis` repositório:

```
docker pull mcr.microsoft.com/azure-cognitive-services/luis:latest
```

Utilize o [ `docker pull` ](https://docs.docker.com/engine/reference/commandline/pull/) comando para transferir uma imagem de contentor.

Para obter uma descrição completa de etiquetas disponíveis, como `latest` utilizado no comando anterior, consulte [LUIS](https://go.microsoft.com/fwlink/?linkid=2043204) no Docker Hub.

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]


## <a name="how-to-use-the-container"></a>Como utilizar o contentor

Assim que o contentor estiver no [computador anfitrião](#the-host-computer), utilize o seguinte processo para trabalhar com o contentor.

![Processo de utilização do contentor de compreensão de idiomas (LUIS)](./media/luis-container-how-to/luis-flow-with-containers-diagram.jpg)

1. [Exportar pacote](#export-packaged-app-from-luis) para o contentor a partir do portal do LUIS ou APIs de LUIS.
1. Mover o ficheiro de pacote para necessários **entrada** diretório a [computador anfitrião](#the-host-computer). Não mude o nome, alterar ou descomprimir o ficheiro de pacote do LUIS.
1. [Execute o contentor](##run-the-container-with-docker-run), com o necessária _entrada de montagem_ e as definições de faturação. Obter mais [exemplos](luis-container-configuration.md#example-docker-run-commands) do `docker run` comandos estão disponíveis. 
1. [Consultar o ponto final de predição do contentor](#query-the-containers-prediction-endpoint). 
1. Quando tiver terminado com o contêiner [importar os registos de ponto final](#import-the-endpoint-logs-for-active-learning) a partir da saída de montagem no portal do LUIS e [parar](#stop-the-container) o contentor.
1. Portal de LUIS a utilização [aprendizagem ativa](luis-how-to-review-endoint-utt.md) no **rever expressões de ponto final** página para melhorar a aplicação.

Não é possível alterar a aplicação em execução no contentor. Na ordem a alteração, a aplicação no contentor, tem de alterar a aplicação no serviço do LUIS com o [LUIS](https://www.luis.ai) portal ou utilizar o LUIS [APIs de criação](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c2f). Em seguida, dar formação e/ou publicar, transferir um novo pacote e execute novamente o contentor.

Não é possível exportar a aplicação do LUIS dentro do contentor para o serviço de LUIS. Apenas os registos de consulta podem ser carregados. 

## <a name="export-packaged-app-from-luis"></a>Exportar a aplicação em pacote do LUIS

O contentor de LUIS requer uma aplicação de LUIS publicada ou preparada para responder a consultas de previsão de expressões do utilizador. Para obter a aplicação do LUIS, utilize o o pacote de preparação ou publicado API. 

A localização predefinida é o `input` subdiretório em relação a onde executa a `docker run` comando.  

Coloque o arquivo de pacote num diretório e este diretório de referência como a montagem de entrada ao executar o contentor do docker. 

### <a name="package-types"></a>Tipos de pacotes

O diretório de montagem de entrada pode conter os **produção**, **teste**, e **Trained** versões da aplicação em simultâneo. Todos os pacotes estão montados. 

|Tipo de pacote|API de ponto final da consulta|Disponibilidade de consulta|Formato de nome de ficheiro de pacote|
|--|--|--|--|
|Preparado|GET, Post|Contentor apenas|`{APPLICATION_ID}_v{APPLICATION_VERSION}.gz`|
|Teste|GET, Post|Azure e contentores|`{APPLICATION_ID}_STAGING.gz`|
|Produção|GET, Post|Azure e contentores|`{APPLICATION_ID}_PRODUCTION.gz`|

> [!IMPORTANT]
> Não mude o nome, alterar ou descomprimir os ficheiros de pacote do LUIS.

### <a name="packaging-prerequisites"></a>Pré-requisitos de empacotamento

Antes de empacotar um aplicativo de LUIS, tem de ter o seguinte:

|Requisitos de empacotamento|Detalhes|
|--|--|
|Azure _compreensão de idiomas_ instância de recurso|Regiões suportadas incluem<br><br>Oeste dos E.U.A. (```westus```)<br>Europa Ocidental (```westeurope```)<br>Leste da Austrália (```australiaeast```)|
|Aplicação do LUIS treinada ou publicada|Sem nenhum [não suportado dependências](#unsupported-dependencies). |
|Acesso para o [computador anfitrião](#the-host-computer)do sistema de ficheiros |O computador anfitrião tem de permitir uma [entrada de montagem](luis-container-configuration.md#mount-settings).|
  
### <a name="export-app-package-from-luis-portal"></a>Exportar pacote de aplicação do portal de LUIS

O LUIS [portal](https://www.luis.ai) fornece a capacidade de exportar o pacote da aplicação publicada ou preparado. 

### <a name="export-published-apps-package-from-luis-portal"></a>Exportar pacote de aplicação publicada a partir do portal de LUIS

Pacote da aplicação publicada está disponível a partir da **as minhas aplicações** página da lista. 

1. Início de sessão para o LUIS [portal](https://www.luis.ai).
1. Na lista, selecione a caixa de verificação à esquerda do nome da aplicação. 
1. Selecione o **exportar** item da barra de ferramentas contextual acima da lista.
1. Selecione **exportar para o contentor (GZIP)**.
1. Selecione o ambiente do **bloco de produção** ou **bloco de teste**.
1. O pacote é transferido a partir do browser.

![Exportar o pacote publicado para o contentor no menu de exportação da página de aplicações](./media/luis-container-how-to/export-published-package-for-container.png)

### <a name="export-trained-apps-package-from-luis-portal"></a>Exportar pacote de preparação da aplicação do portal de LUIS

Pacote de preparação da aplicação está disponível a partir da **versões** página da lista. 

1. Início de sessão para o LUIS [portal](https://www.luis.ai).
1. Selecione a aplicação na lista. 
1. Selecione **gerir** na barra de navegação da aplicação.
1. Selecione **versões** na barra de navegação esquerdo.
1. Na lista, selecione a caixa de verificação à esquerda do nome da versão.
1. Selecione o **exportar** item da barra de ferramentas contextual acima da lista.
1. Selecione **exportar para o contentor (GZIP)**.
1. O pacote é transferido a partir do browser.

![Exportar o pacote de preparação para o contentor no menu de exportação da página de versões](./media/luis-container-how-to/export-trained-package-for-container.png)


### <a name="export-published-apps-package-from-api"></a>Exportar pacote de aplicação publicada a partir da API

Utilize o seguinte método da REST API, para empacotar uma aplicação do LUIS que já [publicados](luis-how-to-publish-app.md). Substituindo seus próprios valores adequados para os marcadores de posição na chamada de API, utilizando a tabela abaixo a especificação de HTTP.

```http
GET /luis/api/v2.0/package/{APPLICATION_ID}/slot/{APPLICATION_ENVIRONMENT}/gzip HTTP/1.1
Host: {AZURE_REGION}.api.cognitive.microsoft.com
Ocp-Apim-Subscription-Key: {AUTHORING_KEY}
```

| Marcador de posição | Value |
|-------------|-------|
|{APPLICATION_ID} | O ID da aplicação LUIS publicada. |
|{APPLICATION_ENVIRONMENT} | O ambiente da aplicação LUIS publicado. Utilize um dos seguintes valores:<br/>```PRODUCTION```<br/>```STAGING``` |
|{AUTHORING_KEY} | A chave de criação da conta do LUIS para a aplicação LUIS publicada.<br/>Pode obter a chave de criação a partir da **definições de utilizador** página no portal do LUIS. |
|{AZURE_REGION} | A região do Azure adequada:<br/><br/>```westus``` -E.U.A. oeste<br/>```westeurope``` -Europa Oeste<br/>```australiaeast``` -Leste da Austrália |

Utilize o comando CURL seguinte para transferir o pacote publicado, substituindo seus próprios valores:

```bash
curl -X GET \
https://{AZURE_REGION}.api.cognitive.microsoft.com/luis/api/v2.0/package/{APPLICATION_ID}/slot/{APPLICATION_ENVIRONMENT}/gzip  \
 -H "Ocp-Apim-Subscription-Key: {AUTHORING_KEY}" \
 -o {APPLICATION_ID}_{APPLICATION_ENVIRONMENT}.gz
```

Se tiver êxito, a resposta é um ficheiro de pacote do LUIS. Guarde o ficheiro na localização de armazenamento especificada para a montagem de entrada do contentor. 

### <a name="export-trained-apps-package-from-api"></a>Exportar pacote de preparação da aplicação a partir da API

Utilize o seguinte método da REST API, para empacotar um aplicativo de LUIS que já [treinado](luis-how-to-train.md). Substituindo seus próprios valores adequados para os marcadores de posição na chamada de API, utilizando a tabela abaixo a especificação de HTTP.

```http
GET /luis/api/v2.0/package/{APPLICATION_ID}/versions/{APPLICATION_VERSION}/gzip HTTP/1.1
Host: {AZURE_REGION}.api.cognitive.microsoft.com
Ocp-Apim-Subscription-Key: {AUTHORING_KEY}
```

| Marcador de posição | Value |
|-------------|-------|
|{APPLICATION_ID} | O ID de aplicação da aplicação LUIS treinado. |
|{APPLICATION_VERSION} | A versão da aplicação da aplicação LUIS treinada. |
|{AUTHORING_KEY} | A chave de criação da conta do LUIS para a aplicação LUIS publicada.<br/>Pode obter a chave de criação a partir da **definições de utilizador** página no portal do LUIS.  |
|{AZURE_REGION} | A região do Azure adequada:<br/><br/>```westus``` -E.U.A. oeste<br/>```westeurope``` -Europa Oeste<br/>```australiaeast``` -Leste da Austrália |

Utilize o comando CURL seguinte para transferir o pacote de preparação:

```bash
curl -X GET \
https://{AZURE_REGION}.api.cognitive.microsoft.com/luis/api/v2.0/package/{APPLICATION_ID}/versions/{APPLICATION_VERSION}/gzip  \
 -H "Ocp-Apim-Subscription-Key: {AUTHORING_KEY}" \
 -o {APPLICATION_ID}_v{APPLICATION_VERSION}.gz
```

Se tiver êxito, a resposta é um ficheiro de pacote do LUIS. Guarde o ficheiro na localização de armazenamento especificada para a montagem de entrada do contentor. 

## <a name="run-the-container-with-docker-run"></a>Execute o contentor com `docker run`

Utilize o [docker run](https://docs.docker.com/engine/reference/commandline/run/) comando para executar o contentor. O comando utiliza os seguintes parâmetros:

| Marcador de posição | Value |
|-------------|-------|
|{ENDPOINT_KEY} | Esta chave é utilizada para iniciar o contentor. Não utilize a chave de arranque. |
|{BILLING_ENDPOINT} | O valor de ponto final de faturação está disponível na página de descrição geral de compreensão de idioma do portal do Azure.|

Substitua estes parâmetros pelos seus próprios valores no seguinte exemplo `docker run` comando.

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 2 \
--mount type=bind,src=c:\input,target=/input \
--mount type=bind,src=c:\output,target=/output \
mcr.microsoft.com/azure-cognitive-services/luis \
Eula=accept \
Billing={BILLING_ENDPOINT} \
ApiKey={ENDPOINT_KEY}
```

> [!Note] 
> O comando anterior utiliza o diretório a `c:` unidade para evitar conflitos de permissão no Windows. Se precisar de utilizar um diretório específico como a entrada de diretório, poderá ter de conceder o docker permissão de serviço. O comando docker anterior utiliza a barra invertida, `\`, como um caractere de continuação de linha. Substituir ou remover isso com base no seu [computador anfitrião](#the-host-computer) requisitos do sistema operacional. Não altere a ordem dos argumentos, a menos que está bastante familiarizada com contentores do docker.


Este comando:

* Execute um contentor do LUIS imagem de contentor
* Carrega a aplicação LUIS de montagem de entrada em c:\input, localizada no anfitrião do contentor
* Aloca dois núcleos de CPU e 4 gigabytes (GB) de memória
* Expõe a porta TCP 5000 e aloca um TTY pseudo para o contentor
* Guarda o contentor e o LUIS registos de montagem em c:\output, localizada no anfitrião do contentor de saída
* Remove automaticamente o contentor depois sai. A imagem de contentor ainda está disponível no computador anfitrião. 

Obter mais [exemplos](luis-container-configuration.md#example-docker-run-commands) do `docker run` comandos estão disponíveis. 

> [!IMPORTANT]
> O `Eula`, `Billing`, e `ApiKey` opções tem de ser especificadas para executar o contentor; caso contrário, não inicia o contentor.  Para obter mais informações, consulte [faturação](#billing).
> O valor de ApiKey é o **chave** também está disponível na página de chaves do recurso de compreensão de linguagem do Azure e as chaves e os pontos finais de página no portal do LUIS.  

[!INCLUDE [Running multiple containers on the same host](../../../includes/cognitive-services-containers-run-multiple-same-host.md)]

## <a name="query-the-containers-prediction-endpoint"></a>Consultar o ponto final de predição do contentor

O contêiner fornece o ponto final de predição de consulta baseado em REST APIs. Pontos finais para aplicações publicadas, (de teste ou produção) tem um _diferentes_ rota que pontos de extremidade para aplicações preparados. 

Usar o host, `https://localhost:5000`, para o contentor APIs. 

|Tipo de pacote|Método|Encaminhar|Parâmetros de consulta|
|--|--|--|--|
|Publicado|[Get](https://westus.dev.cognitive.microsoft.com/docs/services/5819c76f40a6350ce09de1ac/operations/5819c77140a63516d81aee78), [Post](https://westus.dev.cognitive.microsoft.com/docs/services/5819c76f40a6350ce09de1ac/operations/5819c77140a63516d81aee79)|/luis/v2.0/apps/{appId}?|q={q}<br>& de teste<br>[&timezoneOffset]<br>[& verboso]<br>[&log]<br>|
|Preparado|GET, Post|/luis/v2.0/apps/{appId}/versions/{versionId}?|q={q}<br>[&timezoneOffset]<br>[& verboso]<br>[&log]|

Configurar os parâmetros de consulta como e o que é devolvido na resposta da consulta:

|Parâmetro de consulta|Type|Objetivo|
|--|--|--|
|`q`|string|Expressão do utilizador.|
|`timezoneOffset`|número|O timezoneOffset permite-lhe [alterar o fuso horário](luis-concept-data-alteration.md#change-time-zone-of-prebuilt-datetimev2-entity) utilizado pelo datetimeV2 a entidade pré-criados.|
|`verbose`|boolean|Devolve todas as intenções e suas pontuações quando definidas como true. A predefinição é FALSO, que retorna apenas a intenção superior.|
|`staging`|boolean|Consulta interativa devolve resultados do ambiente de teste se definido como true. |
|`log`|boolean|Registos de consultas, que podem ser utilizadas mais tarde para [aprendizagem ativa](luis-how-to-review-endoint-utt.md). A predefinição é verdadeiro.|

### <a name="query-published-app"></a>Aplicação publicada de consulta

Um exemplo de comando CURL para consultar o contentor para uma aplicação publicada é:

```bash
curl -X GET \
"http://localhost:5000/luis/v2.0/apps/{APPLICATION_ID}?q=turn%20on%20the%20lights&staging=false&timezoneOffset=0&verbose=false&log=true" \
-H "accept: application/json"
```
Para fazer consultas para o **de teste** ambiente, a alteração a **teste** consultar o valor de parâmetro de cadeia de caracteres como true: 

`staging=true`

### <a name="query-trained-app"></a>Aplicação de preparação de consulta

Um exemplo de comando CURL para consultar o contentor para uma aplicação com formação é: 

```bash
curl -X GET \
"http://localhost:5000/luis/v2.0/apps/{APPLICATION_ID}/versions/{APPLICATION_VERSION}?q=turn%20on%20the%20lights&timezoneOffset=0&verbose=false&log=true" \
-H "accept: application/json"
```
O nome da versão tem um máximo de 10 carateres e contém apenas carateres permitidos num URL. 

## <a name="import-the-endpoint-logs-for-active-learning"></a>Importar os registos de ponto final para aprendizagem ativa

Se for uma montagem de saída é especificada para o contentor de LUIS, arquivos de log de consulta de aplicação são guardados no diretório de saída, em que {INSTANCE_ID} é o ID de contentor. O registo de consulta de aplicação contém a consulta, resposta e carimbos de data / para cada consulta de predição submetida para o contentor de LUIS. 

A localização seguinte mostra a estrutura de diretório aninhado para ficheiros de registo do contentor.
`
/output/luis/{INSTANCE_ID}/
`
 
No portal do LUIS, selecione a sua aplicação, em seguida, selecione **importar registos de ponto final** para carregar estes registos. 

![Importar ficheiros de registo do contentor para a aprendizagem ativa](./media/luis-container-how-to/upload-endpoint-log-files.png)

Após o carregamento, o registo [reveja o ponto final](https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-review-endpoint-utterances) expressões com no portal do LUIS.

## <a name="stop-the-container"></a>Parar o contentor

Para encerrar o contentor, no ambiente de linha de comando onde o contentor está em execução, prima **Ctrl + C**.

## <a name="troubleshooting"></a>Resolução de problemas

Se executar o contentor com uma saída [montar](luis-container-configuration.md#mount-settings) e registo ativado, o contentor gera os ficheiros de registo que são úteis para resolver os problemas que ocorrem ao iniciar ou executar o contentor. 

## <a name="containers-api-documentation"></a>Documentação da API do contentor

[!INCLUDE [Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="billing"></a>Faturação

O envia de contentor do LUIS cobrança informações para o Azure, utilizando um _compreensão de idiomas_ recursos na sua conta do Azure. 

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Para obter mais informações sobre estas opções, consulte [configurar contentores](luis-container-configuration.md).

## <a name="unsupported-dependencies"></a>Dependências não suportadas

Pode utilizar uma aplicação do LUIS se ele **não inclui** qualquer uma das seguintes dependências:

Configurações de aplicações não suportado|Detalhes|
|--|--|
|Culturas do contentor não suportado| Alemão (Alemanha-DE)<br>Holandês (nl-NL)<br>Japonês (ja-JP)<br>|
|Domínios não suportados|Domínios pré-concebidos, incluindo o domínio pré-criado intenções e entidades|
|Entidades não suportadas para todas as culturas|[KeyPhrase](https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-prebuilt-keyphrase) entidade criados previamente para todas as culturas|
|Entidades não suportadas para a cultura inglês (en-US)|[GeographyV2](https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-prebuilt-geographyv2) entidades pré-concebidas|
|Priming de voz|Dependências externas não são suportadas no contentor.|
|Análise de sentimentos|Dependências externas não são suportadas no contentor.|
|Verificação ortográfica do Bing|Dependências externas não são suportadas no contentor.|

## <a name="summary"></a>Resumo

Neste artigo, aprendeu conceitos e fluxo de trabalho para transferir, instalar e contentores de compreensão de idiomas (LUIS) em execução. Em resumo:

* Compreensão de idiomas (LUIS) fornece um contentor do Linux para Docker fornecendo endpoint consulta predições de expressões.
* Imagens de contentor são transferidas a partir do registo de contentor do Microsoft (MCR).
* Executam imagens de contentor no Docker.
* Pode utilizar a REST API para consultar os pontos finais de contentor ao especificar o URI do contentor do anfitrião.
* Tem de especificar informações de faturação ao instanciar um contentor.

> [!IMPORTANT]
> Contentores de serviços cognitivos não estão licenciados para executar sem a ser ligado ao Azure para medição. Os clientes têm de ativar os contentores comunicar informações de faturação com o serviço de medição em todos os momentos. Contentores de serviços cognitivos não enviar dados de cliente (por exemplo, a imagem ou texto que está a ser analisado) para a Microsoft.

## <a name="next-steps"></a>Passos Seguintes

* Revisão [configurar contentores](luis-container-configuration.md) para definições de configuração
* Consulte a [resolução de problemas](troubleshooting.md) para resolver problemas relacionados com a funcionalidade de LUIS.
* Utilizar mais [contentores de serviços cognitivos](../cognitive-services-container-support.md)
