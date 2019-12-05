---
title: Configurar contêineres de fala
titleSuffix: Azure Cognitive Services
description: O serviço de fala fornece a cada contêiner uma estrutura de configuração comum, para que você possa configurar e gerenciar facilmente o armazenamento, o registro em log e a telemetria e as configurações de segurança para seus contêineres.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/07/2019
ms.author: dapine
ms.openlocfilehash: 34b4664ec13f7ba1871433e37d86170b2207a17a
ms.sourcegitcommit: 6c01e4f82e19f9e423c3aaeaf801a29a517e97a0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/04/2019
ms.locfileid: "74816571"
---
# <a name="configure-speech-service-containers"></a>Configurar contêineres de serviço de fala

Os contêineres de fala permitem que os clientes criem uma arquitetura de aplicativo de fala otimizada para aproveitar os recursos robustos de nuvem e a localidade de borda. Os quatro contêineres de fala para os quais damos suporte agora são, conversão de texto em **texto**, **personalizada de fala para texto**, conversão de textos em **fala**e **conversão personalizada**.

O ambiente de tempo de execução do contêiner de **fala** é configurado usando os argumentos do comando `docker run`. Esse contêiner tem várias configurações necessárias, juntamente com algumas configurações opcionais. Várias [exemplos](#example-docker-run-commands) do comando estão disponíveis. As configurações específicas do contêiner são as configurações de cobrança.

## <a name="configuration-settings"></a>Definições de configuração

[!INCLUDE [Container shared configuration settings table](../../../includes/cognitive-services-containers-configuration-shared-settings-table.md)]

> [!IMPORTANT]
> O [ `ApiKey` ](#apikey-configuration-setting), [ `Billing` ](#billing-configuration-setting), e [ `Eula` ](#eula-setting) definições são utilizadas em conjunto, e tem de indicar valores válidos para todas as três-los; caso contrário não inicia o contentor. Para obter mais informações sobre como utilizar estas definições de configuração para criar uma instância de um contentor, consulte [faturação](speech-container-howto.md#billing).

## <a name="apikey-configuration-setting"></a>ApiKey definição de configuração

O `ApiKey` definição especifica a chave de recurso do Azure utilizada para controlar informações de faturação para o contentor. Você deve especificar um valor para o ApiKey e o valor deve ser uma chave válida para o recurso de _fala_ especificado para a definição de configuração de [`Billing`](#billing-configuration-setting) .

Essa configuração pode ser encontrada no seguinte local:

- Portal do Azure: gerenciamento **de recursos de fala** , em **chaves**

## <a name="applicationinsights-setting"></a>Definição do Application Insights

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>Definição de configuração de faturação

A configuração `Billing` especifica o URI do ponto de extremidade do recurso de _fala_ no Azure usado para medir as informações de cobrança do contêiner. Você deve especificar um valor para essa definição de configuração e o valor deve ser um URI de ponto de extremidade válido para um recurso de _fala_ no Azure. O contêiner relata o uso de cada 10 a 15 minutos.

Essa configuração pode ser encontrada no seguinte local:

- Portal do Azure: visão geral **da fala** , rotulada `Endpoint`

| Obrigatório | Nome | Data type | Descrição |
| -------- | ---- | --------- | ----------- |
| Sim | `Billing` | String | URI do ponto de extremidade de cobrança. Para obter mais informações sobre como obter o URI de cobrança, consulte [coletando parâmetros necessários](speech-container-howto.md#gathering-required-parameters). Para obter mais informações e uma lista completa de pontos de extremidade regionais, consulte [nomes de subdomínio personalizados para serviços cognitivas](../cognitive-services-custom-subdomains.md). |

## <a name="eula-setting"></a>EULA definição

[!INCLUDE [Container shared configuration eula settings](../../../includes/cognitive-services-containers-configuration-shared-settings-eula.md)]

## <a name="fluentd-settings"></a>Definições de Fluentd

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-fluentd.md)]

## <a name="http-proxy-credentials-settings"></a>Configurações de credenciais de proxy HTTP

[!INCLUDE [Container shared HTTP proxy settings](../../../includes/cognitive-services-containers-configuration-shared-settings-http-proxy.md)]

## <a name="logging-settings"></a>Definições de registo

[!INCLUDE [Container shared configuration logging settings](../../../includes/cognitive-services-containers-configuration-shared-settings-logging.md)]

## <a name="mount-settings"></a>Definições de montagem

Monta o enlace de utilização para ler e escrever dados de e para o contentor. Pode especificar uma montagem de entrada ou saída de montagem, especificando o `--mount` opção da [docker run](https://docs.docker.com/engine/reference/commandline/run/) comando.

Os contêineres de fala padrão não usam montagens de entrada ou de saída para armazenar dados de treinamento ou de serviço. No entanto, os contêineres de fala personalizados dependem de montagens de volume.

A sintaxe exata da localização de montagem do anfitrião varia consoante o sistema operativo anfitrião. Além disso, o [computador anfitrião](speech-container-howto.md#the-host-computer)da localização de montagem não estar acessível devido a um conflito entre as permissões usadas pela conta de serviço do docker e o anfitrião montar permissões de localização.

| Opcional | Nome | Data type | Descrição |
| -------- | ---- | --------- | ----------- |
| Não permitido | `Input` | String | Os contêineres de fala padrão não usam isso. Os contêineres de fala personalizados usam [montagens de volume](#volume-mount-settings).                                                                                    |
| Opcional | `Output` | String | O destino de montagem de saída. O valor predefinido é `/output`. Esta é a localização dos registos. Isso inclui logs de contêiner. <br><br>Exemplo:<br>`--mount type=bind,src=c:\output,target=/output` |

## <a name="volume-mount-settings"></a>Configurações de montagem de volume

Os contêineres de fala personalizados usam [montagens de volume](https://docs.docker.com/storage/volumes/) para manter modelos personalizados. Você pode especificar uma montagem de volume adicionando a opção `-v` (ou `--volume`) ao comando [Docker execute](https://docs.docker.com/engine/reference/commandline/run/) .

Os modelos personalizados são baixados na primeira vez que um novo modelo é ingerido como parte do comando personalizado de execução do Docker de contêiner de fala. As execuções sequenciais da mesma `ModelId` para um contêiner de fala personalizado usarão o modelo baixado anteriormente. Se a montagem de volume não for fornecida, os modelos personalizados não poderão ser persistidos.

A configuração de montagem de volume consiste em três campos de `:` de cor separados:

1. O primeiro campo é o nome do volume no computador host, por exemplo, _C:\input_.
2. O segundo campo é o diretório no contêiner, por exemplo, _/usr/local/Models_.
3. O terceiro campo (opcional) é uma lista de opções separada por vírgulas, para obter mais informações, consulte [usar volumes](https://docs.docker.com/storage/volumes/).

### <a name="volume-mount-example"></a>Exemplo de montagem de volume

```bash
-v C:\input:/usr/local/models
```

Esse comando monta o diretório _C:\input_ do computador host no diretório _/usr/local/Models_ do contêineres.

> [!IMPORTANT]
> As configurações de montagem de volume são aplicáveis somente a contêineres de **texto em fala** e de **fala personalizada para texto** . Os contêineres padrão de **fala para texto** e **conversão de texto em fala** não usam montagens de volume.

## <a name="example-docker-run-commands"></a>Executar comandos de docker de exemplo

Os exemplos seguintes utilizam as definições de configuração para ilustrar como escrever e usar `docker run` comandos. Quando em execução, o contentor continua a ser executada até [parar](speech-container-howto.md#stop-the-container) -lo.

- **Caractere de continuação de linha**: os comandos do Docker nas seções a seguir usam a barra invertida, `\`, como um caractere de continuação de linha. Substituir ou remova esta com base nos requisitos de seu sistema de operativo de anfitrião.
- **Ordem do argumento**: não altere a ordem dos argumentos, a menos que você esteja familiarizado com contêineres do Docker.

Substitua {_argument_name_} pelos seus próprios valores:

| Marcador de posição | Valor | Formato ou de exemplo |
| ----------- | ----- | ----------------- |
| **{API_KEY}** | A chave do ponto de extremidade do recurso `Speech` na página chaves do `Speech` do Azure.   | `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`                                                                                  |
| **{ENDPOINT_URI}** | O valor do ponto de extremidade de cobrança está disponível na página Visão geral do `Speech` do Azure. | Consulte [coletando parâmetros necessários](speech-container-howto.md#gathering-required-parameters) para obter exemplos explícitos. |

[!INCLUDE [subdomains-note](../../../includes/cognitive-services-custom-subdomains-note.md)]

> [!IMPORTANT]
> O `Eula`, `Billing`, e `ApiKey` opções tem de ser especificadas para executar o contentor; caso contrário, não inicia o contentor. Para obter mais informações, consulte [faturação](#billing-configuration-setting).
> O valor de ApiKey é a **chave** da página de chaves de recurso de fala do Azure.

## <a name="speech-container-docker-examples"></a>Exemplos do Docker do contêiner de fala

Os exemplos do Docker a seguir são para o contêiner de fala.

## <a name="speech-to-texttabstt"></a>[Conversão de voz em texto](#tab/stt)

### <a name="basic-example-for-speech-to-text"></a>Exemplo básico de conversão de fala em texto

```Docker
docker run --rm -it -p 5000:5000 --memory 4g --cpus 4 \
containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

### <a name="logging-example-for-speech-to-text"></a>Exemplo de log para conversão de fala em texto

```Docker
docker run --rm -it -p 5000:5000 --memory 4g --cpus 4 \
containerpreview.azurecr.io/microsoft/cognitive-services-custom-speech-to-text \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY} \
Logging:Console:LogLevel:Default=Information
```

## <a name="custom-speech-to-texttabcstt"></a>[Fala Personalizada para texto](#tab/cstt)

### <a name="basic-example-for-custom-speech-to-text"></a>Exemplo básico para Fala Personalizada para texto

```Docker
docker run --rm -it -p 5000:5000 --memory 4g --cpus 4 \
-v {VOLUME_MOUNT}:/usr/local/models \
containerpreview.azurecr.io/microsoft/cognitive-services-custom-speech-to-text \
ModelId={MODEL_ID} \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

### <a name="logging-example-for-custom-speech-to-text"></a>Exemplo de log para Fala Personalizada para texto

```Docker
docker run --rm -it -p 5000:5000 --memory 4g --cpus 4 \
-v {VOLUME_MOUNT}:/usr/local/models \
containerpreview.azurecr.io/microsoft/cognitive-services-custom-speech-to-text \
ModelId={MODEL_ID} \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY} \
Logging:Console:LogLevel:Default=Information
```

## <a name="text-to-speechtabtss"></a>[Conversão de texto em voz](#tab/tss)

### <a name="basic-example-for-text-to-speech"></a>Exemplo básico para conversão de texto em fala

```Docker
docker run --rm -it -p 5000:5000 --memory 2g --cpus 1 \
containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

### <a name="logging-example-for-text-to-speech"></a>Exemplo de log para conversão de texto em fala

```Docker
docker run --rm -it -p 5000:5000 --memory 2g --cpus 1 \
containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY} \
Logging:Console:LogLevel:Default=Information
```

## <a name="custom-text-to-speechtabctts"></a>[Conversão de texto em fala personalizada](#tab/ctts)

### <a name="basic-example-for-custom-text-to-speech"></a>Exemplo básico para conversão de texto em fala personalizada

```Docker
docker run --rm -it -p 5000:5000 --memory 2g --cpus 1 \
-v {VOLUME_MOUNT}:/usr/local/models \
containerpreview.azurecr.io/microsoft/cognitive-services-custom-text-to-speech \
ModelId={MODEL_ID} \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

### <a name="logging-example-for-custom-text-to-speech"></a>Exemplo de log para conversão de texto em fala personalizada

```Docker
docker run --rm -it -p 5000:5000 --memory 2g --cpus 1 \
-v {VOLUME_MOUNT}:/usr/local/models \
containerpreview.azurecr.io/microsoft/cognitive-services-custom-text-to-speech \
ModelId={MODEL_ID} \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY} \
Logging:Console:LogLevel:Default=Information
```

---

## <a name="next-steps"></a>Passos seguintes

- Revisão [como instalar e executar contentores](speech-container-howto.md)
