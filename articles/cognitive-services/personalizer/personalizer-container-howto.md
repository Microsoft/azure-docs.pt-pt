---
title: Instalar e executar contentores - Personalizer
titleSuffix: Azure Cognitive Services
description: Como transferir, instalar e executar contentores para Personalizer.
author: edjez
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: overview
ms.date: 05/09/2019
ms.author: edjez
ms.openlocfilehash: a197531a7c78823271c0a5fa5413b76746f63a9a
ms.sourcegitcommit: 8fc5f676285020379304e3869f01de0653e39466
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/09/2019
ms.locfileid: "65507023"
---
# <a name="install-and-run-personalizer-containers"></a>Instalar e executar contentores Personalizer

O serviço de Personalizer tem o seguinte contentor (es): 

|Função|Funcionalidades|
|-|-|
|personalizer|Determine a melhor ação a partir do contexto atual de conteúdo e o utilizador.|

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Tem de cumprir os seguintes pré-requisitos antes de utilizar contentores do serviço de Personalizer:

|Necessário|Objetivo|
|--|--|
|Motor do docker| É necessário o motor do Docker instalado num [computador anfitrião](#the-host-computer). Docker disponibiliza pacotes que configurar o ambiente do Docker num [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/), e [Linux](https://docs.docker.com/engine/installation/#supported-platforms). Para obter um manual sobre noções básicas do Docker e um contentor, consulte a [descrição geral do Docker](https://docs.docker.com/engine/docker-overview/).<br><br> Docker tem de ser configurado para permitir que os contentores para se ligar com e enviar dados de faturação para o Azure. <br><br> **No Windows**, Docker também tem de ser configurado para dar suporte a contentores do Linux.<br><br>|
|Familiaridade com o Docker | Deve ter uma noção básica dos conceitos do Docker, como registos, repositórios, contentores e imagens de contentor, bem como dados de conhecimento do basic `docker` comandos.| 
|Recurso de serviço personalizer |Para utilizar estes contentores, tem de ter:<br><br>R _Personalizer serviço_ recursos do Azure para obter a chave de faturação associada e a faturação URI do ponto final. Ambos os valores estão disponíveis nas páginas de descrição geral do serviço Personalizer e chaves do portal do Azure e são necessários para iniciar o contentor.<br><br>**{BILLING_KEY}** : chave de recurso<br><br>**{BILLING_ENDPOINT_URI}** : exemplo URI do ponto final é: `https://westus.api.cognitive.microsoft.com/`|

### <a name="the-host-computer"></a>O computador anfitrião

O **anfitrião** é o computador que executa o contentor do docker. Pode ser um computador no local ou um docker a alojar o serviço no Azure incluindo:

* [Serviço Kubernetes do Azure](https://docs.microsoft.com/aks/index.yml)
* [Azure Container Instances](https://docs.microsoft.com/container-instances/index.yml)
* [Kubernetes](https://kubernetes.io/) cluster implementado [do Azure Stack](https://docs.microsoft.com/azure-stack/index.yml). Para obter mais informações, consulte [implementar o Kubernetes no Azure Stack](https://docs.microsoft.com/azure-stack/user/azure-stack-solution-template-kubernetes-deploy.md).

### <a name="container-requirements-and-recommendations"></a>Requisitos do contentor e recomendações

A tabela seguinte descreve os mínimos e recomendados núcleos de CPU e memória para atribuir cada contentor do serviço de Personalizer.

| Contentor | Mínimo | Recomendado |
|-----------|---------|-------------|
|personalizer | 1 núcleo, 4 GB de memória | 2 núcleos, 8 GB de memória |

Cada principal tem de ser, pelo menos, de 2,6 GHz (gigahertz) ou mais rápido.

Núcleos e memória correspondem para o `--cpus` e `--memory` as definições, que são utilizadas como parte do `docker run` comando.

O contentor tem de ser capaz de ligar ao Azure EventHub em ordem às informações de reencaminhamento nas chamadas de classificação e a recompensa para o servidor de Personalizer no Azure e tem de ser capaz de ligar à API do Personalizer para carregar a configuração necessária e máquina mais recente modelos de aprendizagem.

## <a name="get-the-container-image-with-docker-pull"></a>Obter a imagem de contentor com o `docker pull`

Imagens de contentor para o serviço de Personalizer estão disponíveis. 

| Contentor | Repositório |
|-----------|------------|
| personalizer | `mcr.microsoft.com/azure-cognitive-services/personalizer:latest` |

> [!TIP]
> Pode utilizar o [imagens do docker](https://docs.docker.com/engine/reference/commandline/images/) command para listar as imagens de contentor transferido. Por exemplo, o comando seguinte lista o ID, o repositório e a etiqueta de cada imagem de contentor transferido, formatada como uma tabela:
>
>  ```Docker
>  docker images --format "table {{.ID}}\t{{.Repository}}\t{{.Tag}}"
>
>  IMAGE ID            REPOSITORY                                                                TAG
>  ebbee78a6baa        mcr.microsoft.com/azure-cognitive-services/personalizer                 latest
>  ``` 

### <a name="docker-pull-for-the-personalizer-service-container"></a>Solicitação de docker para o contentor de serviço Personalizer

```Docker
docker pull mcr.microsoft.com/azure-cognitive-services/personalizer:latest
```

## <a name="how-the-container-works"></a>Como funciona o contentor

O contentor de Personalizer representa a parte do cliente do serviço Personalizer. Quando o contentor é executado, ele obtém os modelos e as definições de configuração do serviço Personalizer na cloud do Azure. O serviço de contentor utiliza estas informações para responder aos pedidos **classificação** e **recompensa**. O contentor também envia essas solicitações para o recurso de Personalizer na cloud do Azure. Estas informações são utilizadas, em seguida, para preparar o seu modelo de loop Personalizer, com base na configuração atual para a frequência de atualização do modelo. O modelo atualizado é enviado novamente ao contêiner. 

![No local, arquitetura de local para o cliente do contentor de Personalizer](./media/personalizer-container-howto/personalizer-container-architecture.png)

## <a name="how-to-use-the-container"></a>Como utilizar o contentor

Assim que o contentor estiver no [computador anfitrião](#the-host-computer), utilize o seguinte processo para trabalhar com o contentor.

1. [Execute o contentor](#run-the-container-with-docker-run), o necessário com as definições de faturação. Obter mais [exemplos](personalizer-container-configuration.md#example-docker-run-commands) do `docker run` comandos estão disponíveis. 
1. [Consultar o ponto final de predição do contentor](#query-the-containers-prediction-endpoint). 

## <a name="run-the-container-with-docker-run"></a>Execute o contentor com `docker run`

Utilize o [docker run](https://docs.docker.com/engine/reference/commandline/run/) comando para executar qualquer um dos três contentores. O comando utiliza os seguintes parâmetros:

| Marcador de posição | Value |
|-------------|-------|
|{BILLING_KEY} | Esta chave é utilizada para iniciar o contentor e está disponível na página de chaves de serviço Personalizer do portal do Azure.  |
|{BILLING_ENDPOINT_URI} | O valor do URI de ponto de extremidade faturação está disponível na página de descrição geral do serviço Personalizer do portal do Azure.|

Substitua estes parâmetros pelos seus próprios valores no seguinte exemplo `docker run` comando.

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
mcr.microsoft.com/azure-cognitive-services/personalizer\
Eula=accept \
Billing={BILLING_ENDPOINT_URI} \
ApiKey={BILLING_KEY}
```

Este comando:

* Execute um contentor de serviço Personalizer a partir da imagem de contentor
* Aloca um núcleo de CPU e 4 gigabytes (GB) de memória
* Expõe a porta TCP 5000 e aloca um TTY pseudo para o contentor
* Remove automaticamente o contentor depois sai. A imagem de contentor ainda está disponível no computador anfitrião. 

> [!IMPORTANT]
> O `Eula`, `Billing`, e `ApiKey` opções tem de ser especificadas para executar o contentor; caso contrário, não inicia o contentor.  Para obter mais informações, consulte [faturação](#billing).

### <a name="run-multiple-containers-on-the-same-host"></a>Executar vários contentores no mesmo anfitrião

Se pretende executar vários contentores com portas expostas, certifique-se executar cada contentor com uma porta diferente. Por exemplo, execute o primeiro contentor na porta 5000 e o segundo contentor na porta 5001.

Substitua a `<container-registry>` e `<container-name>` com os valores dos contentores que utilizar. Estes não têm de ser o mesmo contentor. Pode ter o contentor de Personalizer e o contentor de LUIS em execução no anfitrião em conjunto, ou pode ter vários contentores de Personalizer em execução. 

Execute o primeiro contentor na porta 5000. 

```bash 
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
<container-registry>/microsoft/<container-name> \
Eula=accept \
Billing={BILLING_ENDPOINT_URI} \
ApiKey={BILLING_KEY}
```

Execute o contentor de segundo na porta 5001.


```bash 
docker run --rm -it -p 5000:5001 --memory 4g --cpus 1 \
<container-registry>/microsoft/<container-name> \
Eula=accept \
Billing={BILLING_ENDPOINT_URI} \
ApiKey={BILLING_KEY}
```

Cada contentor subseqüente deve ser uma porta diferente. 

## <a name="query-the-containers-prediction-endpoint"></a>Consultar o ponto final de predição do contentor

O contêiner fornece o ponto final de predição de consulta baseado em REST APIs. 

Usar o host, https://localhost:5000, para o contentor APIs.

## <a name="stop-the-container"></a>Parar o contentor

Para encerrar o contentor, no ambiente de linha de comando onde o contentor está em execução, prima **Ctrl + C**.

## <a name="troubleshoot"></a>Resolução de problemas

Se executar o contentor com uma saída [montar](personalizer-container-configuration.md#mount-settings) e registo ativado, o contentor gera os ficheiros de registo que são úteis para resolver os problemas que ocorrem ao iniciar ou executar o contentor. 

## <a name="container-api-documentation"></a>Documentação da API do contentor

O contentor fornece um conjunto completo de documentação para os pontos finais, bem como um `Try it now` funcionalidade. Esta funcionalidade permite-lhe introduzir as definições num formulário HTML baseada na web e fazer a consulta sem ter de escrever qualquer código. Depois da consulta de volta, um exemplo de comando CURL é fornecida para demonstrar os cabeçalhos HTTP e corpo formato necessário. 

> [!TIP]
> Leitura a [especificação de OpenAPI](https://swagger.io/docs/specification/about/), que descreve as operações de API suportadas pelo contêiner, da `/swagger` URI relativo. Por exemplo:
>
>  ```http
>  http://localhost:5000/swagger
>  ```

## <a name="billing"></a>Faturação

O envio de contentores de serviço Personalizer cobrança informações para o Azure, utilizando um _Personalizer serviço_ recursos na sua conta do Azure. 

Contentores de serviços cognitivos não estão licenciados para executar sem a ser ligado ao Azure para medição. Os clientes têm de ativar os contentores comunicar informações de faturação com o serviço de medição em todos os momentos.  

O `docker run` comando utiliza os argumentos a seguir para fins de faturação:

| Opção | Descrição |
|--------|-------------|
| `ApiKey` | A chave de API dos _Personalizer serviço_ recurso utilizado para controlar informações de faturação. |
| `Billing` | O ponto final da _Personalizer serviço_ recurso utilizado para controlar informações de faturação.|
| `Eula` | Indica que aceite a licença para o contentor.<br/>O valor desta opção tem de ser definido `accept`. |

> [!IMPORTANT]
> Todas as três opções tem de ser especificadas com valores válidos ou não inicia o contentor.

Para obter mais informações sobre estas opções, consulte [configurar contentores](personalizer-container-configuration.md).

## <a name="summary"></a>Resumo

Neste artigo, aprendeu conceitos e fluxo de trabalho para transferir, instalar e serviço Personalizer contentores em execução. Em resumo:

* O serviço de personalizer fornece um contentores de Linux para o Docker, encapsulando a personalização.
* Imagens de contentor são transferidas a partir do registo de contentor do Microsoft (MCR) no Azure.
* Executam imagens de contentor no Docker.
* Pode utilizar a REST API ou o SDK para chamar operações nos contentores do serviço de Personalizer ao especificar o URI do contentor do anfitrião.
* Tem de especificar informações de faturação ao instanciar um contentor.

> [!IMPORTANT]
> Contentores de serviços cognitivos não estão licenciados para executar sem a ser ligado ao Azure para medição. Os clientes têm de ativar os contentores comunicar informações de faturação com o serviço de medição em todos os momentos. Atualizar o Personalizer contentores também enviará os dados de pedido para o serviço correspondente no Azure para fins de formação online e irão obter modelos periodicamente a partir do Azure.

## <a name="next-steps"></a>Passos Seguintes

[Como configurar o contentor do Docker para o `Docker Run` comando](personalizer-container-configuration.md)