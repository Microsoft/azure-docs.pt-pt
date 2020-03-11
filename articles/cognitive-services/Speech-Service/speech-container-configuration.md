---
title: Configure recipientes de fala
titleSuffix: Azure Cognitive Services
description: O serviço de fala fornece a cada recipiente um quadro de configuração comum, para que possa configurar e gerir facilmente o armazenamento, a extração e a telemetria e as definições de segurança dos seus contentores.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/09/2020
ms.author: dapine
ms.openlocfilehash: dd5a531e4a979cba9c2a766c7774762a0427ad02
ms.sourcegitcommit: b8d0d72dfe8e26eecc42e0f2dbff9a7dd69d3116
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/10/2020
ms.locfileid: "79037315"
---
# <a name="configure-speech-service-containers"></a>Configure recipientes de serviço de fala

Os recipientes de fala permitem aos clientes construir uma arquitetura de aplicação de fala que é otimizada para tirar partido de capacidades de nuvem robustas e localidade de borda. Os quatro contentores da fala que apoiamos agora são, **discurso a texto,** **discurso personalizado- discurso-a-texto,** **texto-a-fala,** e **texto-a-fala personalizado.**

O ambiente de execução do contentor **da fala** é configurado usando os argumentos de comando `docker run`. Este recipiente tem várias configurações necessárias, juntamente com algumas configurações opcionais. Vários [exemplos](#example-docker-run-commands) do comando estão disponíveis. As definições específicas do recipiente são as definições de faturação.

## <a name="configuration-settings"></a>Definições de configuração

[!INCLUDE [Container shared configuration settings table](../../../includes/cognitive-services-containers-configuration-shared-settings-table.md)]

> [!IMPORTANT]
> As definições [`ApiKey`, ](#apikey-configuration-setting) [`Billing`](#billing-configuration-setting)e [`Eula`](#eula-setting) são utilizadas em conjunto, e deve fornecer valores válidos para todos os três; caso contrário, o seu recipiente não arranca. Para obter mais informações sobre a utilização destas definições de configuração para instantaneamente um recipiente, consulte [a Faturação](speech-container-howto.md#billing).

## <a name="apikey-configuration-setting"></a>ApiKey definição de configuração

A definição `ApiKey` especifica a chave de recursos Azure utilizada para rastrear as informações de faturação do recipiente. Deve especificar um valor para o ApiKey e o valor deve ser uma chave válida para o recurso _da Fala_ especificado para a definição de configuração [`Billing`.](#billing-configuration-setting)

Esta definição pode ser encontrada no seguinte local:

- Portal Azure: Gestão de Recursos **da Fala,** sob **Chaves**

## <a name="applicationinsights-setting"></a>Definição do Application Insights

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>Definição de configuração de faturação

A definição `Billing` especifica o ponto final URI do recurso _Da Fala_ em Azure usado para medir informações de faturação para o recipiente. Deve especificar um valor para esta configuração, e o valor deve ser um uri de ponto final válido para um recurso _da Fala_ no Azure. O recipiente reporta o uso a cada 10 a 15 minutos.

Esta definição pode ser encontrada no seguinte local:

- Portal Azure: Visão geral **do discurso,** rotulada `Endpoint`

| Necessário | Nome | Tipo de dados | Descrição |
| -------- | ---- | --------- | ----------- |
| Sim | `Billing` | String | Ponto final de faturação URI. Para obter mais informações sobre a obtenção do URI de faturação, consulte a [recolha de parâmetros necessários](speech-container-howto.md#gathering-required-parameters). Para mais informações e uma lista completa de pontos finais regionais, consulte [nomes de subdomínio personalizado para Serviços Cognitivos](../cognitive-services-custom-subdomains.md). |

## <a name="eula-setting"></a>EULA definição

[!INCLUDE [Container shared configuration eula settings](../../../includes/cognitive-services-containers-configuration-shared-settings-eula.md)]

## <a name="fluentd-settings"></a>Definições de Fluentd

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-fluentd.md)]

## <a name="http-proxy-credentials-settings"></a>Definições de credenciais de procuração HTTP

[!INCLUDE [Container shared HTTP proxy settings](../../../includes/cognitive-services-containers-configuration-shared-settings-http-proxy.md)]

## <a name="logging-settings"></a>Definições de registo

[!INCLUDE [Container shared configuration logging settings](../../../includes/cognitive-services-containers-configuration-shared-settings-logging.md)]

## <a name="mount-settings"></a>Definições de montagem

Monta o enlace de utilização para ler e escrever dados de e para o contentor. Pode especificar um suporte de entrada ou montagem de saída especificando a opção `--mount` no comando de execução do [estivador.](https://docs.docker.com/engine/reference/commandline/run/)

Os recipientes Standard Speech não utilizam suportes de entrada ou saída para armazenar dados de formação ou de serviço. No entanto, os recipientes de fala personalizados dependem de suportes de volume.

A sintaxe exata da localização de montagem do anfitrião varia consoante o sistema operativo anfitrião. Além disso, a localização do suporte do [computador hospedeiro](speech-container-howto.md#the-host-computer)pode não estar acessível devido a um conflito entre permissões utilizadas pela conta de serviço do estivador e as permissões de localização do suporte do hospedeiro.

| Opcional | Nome | Tipo de dados | Descrição |
| -------- | ---- | --------- | ----------- |
| Não permitido | `Input` | String | Os recipientes standard de fala não usam isto. Os recipientes de fala personalizados utilizam [suportes](#volume-mount-settings)de volume .                                                                                    |
| Opcional | `Output` | String | O destino de montagem de saída. O valor predefinido é `/output`. Esta é a localização dos registos. Isto inclui troncos de contentores. <br><br>Exemplo:<br>`--mount type=bind,src=c:\output,target=/output` |

## <a name="volume-mount-settings"></a>Configurações de montagem de volume

Os recipientes de fala personalizados usam [suportes](https://docs.docker.com/storage/volumes/) de volume para persistir modelos personalizados. Pode especificar um suporte de volume adicionando a opção `-v` (ou `--volume`) ao comando de execução do [estivador.](https://docs.docker.com/engine/reference/commandline/run/)

Os modelos personalizados são descarregados na primeira vez que um novo modelo é ingerido como parte do comando de execução de portais personalizado. As corridas sequenciais do mesmo `ModelId` para um recipiente de discurso personalizado usarão o modelo previamente descarregado. Se o volume não for fornecido, os modelos personalizados não podem ser persistidos.

A definição de montagem de volume consiste em três campos separados `:` de cor:

1. O primeiro campo é o nome do volume na máquina hospedeira, por exemplo _C:\entrada_.
2. O segundo campo é o diretório no recipiente, por exemplo _/usr/local/modelos_.
3. O terceiro campo (opcional) é uma lista de opções separadas pela vírvia, para mais informações ver volumes de [utilização](https://docs.docker.com/storage/volumes/).

### <a name="volume-mount-example"></a>Exemplo de montagem de volume

```bash
-v C:\input:/usr/local/models
```

Este comando monta a máquina hospedeira _C:\input_ diretório para os recipientes _/usr/local/modelos_ diretório.

> [!IMPORTANT]
> As definições de montagem de volume são aplicáveis apenas aos recipientes **personalizados** de discurso a texto e **texto** personalizado. Os recipientes padrão **de fala-a-texto** e **texto-a-fala** não utilizam suportes de volume.

## <a name="example-docker-run-commands"></a>Executar comandos de docker de exemplo

Os exemplos seguintes utilizam as definições de configuração para ilustrar como escrever e utilizar comandos `docker run`. Uma vez em funcionamento, o recipiente continua a funcionar até o [parar.](speech-container-howto.md#stop-the-container)

- **Personagem de continuação da linha**: Os comandos do Docker nas seguintes secções utilizam o corte traseiro, `\`, como um personagem de continuação da linha. Substituir ou remova esta com base nos requisitos de seu sistema de operativo de anfitrião.
- **Ordem de argumentação**: Não altere a ordem dos argumentos a menos que esteja familiarizado com os contentores do Docker.

Substitua {_argument_name_} com os seus próprios valores:

| Marcador de posição | Valor | Formato ou de exemplo |
| ----------- | ----- | ----------------- |
| **{API_KEY}** | A chave final do recurso `Speech` na página Azure `Speech` Keys.   | `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`                                                                                  |
| **{ENDPOINT_URI}** | O valor final da faturação está disponível na página de visão geral do Azure `Speech`. | Consulte a [recolha de parâmetros necessários](speech-container-howto.md#gathering-required-parameters) para exemplos explícitos. |

[!INCLUDE [subdomains-note](../../../includes/cognitive-services-custom-subdomains-note.md)]

> [!IMPORTANT]
> As opções de `Eula`, `Billing`e `ApiKey` devem ser especificadas para funcionar o contentor; caso contrário, o contentor não vai começar. Para mais informações, consulte [billing.](#billing-configuration-setting)
> O valor ApiKey é a **chave** da página de teclas azure Speech Resource.

## <a name="speech-container-docker-examples"></a>Porta-contentor Exemplos De Docker

Os seguintes exemplos do Docker são para o contentor da Fala.

## <a name="speech-to-text"></a>[Conversão de voz em texto](#tab/stt)

### <a name="basic-example-for-speech-to-text"></a>Exemplo básico para o Discurso-a-texto

```Docker
docker run --rm -it -p 5000:5000 --memory 4g --cpus 4 \
containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

### <a name="logging-example-for-speech-to-text"></a>Exemplo de registo de discurso-a-texto

```Docker
docker run --rm -it -p 5000:5000 --memory 4g --cpus 4 \
containerpreview.azurecr.io/microsoft/cognitive-services-custom-speech-to-text \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY} \
Logging:Console:LogLevel:Default=Information
```

## <a name="custom-speech-to-text"></a>[Discurso personalizado a texto](#tab/cstt)

### <a name="basic-example-for-custom-speech-to-text"></a>Exemplo básico para discurso personalizado-a-texto

```Docker
docker run --rm -it -p 5000:5000 --memory 4g --cpus 4 \
-v {VOLUME_MOUNT}:/usr/local/models \
containerpreview.azurecr.io/microsoft/cognitive-services-custom-speech-to-text \
ModelId={MODEL_ID} \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

### <a name="logging-example-for-custom-speech-to-text"></a>Exemplo de registo de discurso personalizado a texto

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

## <a name="text-to-speech"></a>[Conversão de texto em voz](#tab/tss)

### <a name="basic-example-for-text-to-speech"></a>Exemplo básico para texto-a-fala

```Docker
docker run --rm -it -p 5000:5000 --memory 2g --cpus 1 \
containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

### <a name="logging-example-for-text-to-speech"></a>Exemplo de registo de texto-a-fala

```Docker
docker run --rm -it -p 5000:5000 --memory 2g --cpus 1 \
containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY} \
Logging:Console:LogLevel:Default=Information
```

## <a name="custom-text-to-speech"></a>[Texto-a-fala personalizado](#tab/ctts)

### <a name="basic-example-for-custom-text-to-speech"></a>Exemplo básico para texto-a-fala personalizado

```Docker
docker run --rm -it -p 5000:5000 --memory 2g --cpus 1 \
-v {VOLUME_MOUNT}:/usr/local/models \
containerpreview.azurecr.io/microsoft/cognitive-services-custom-text-to-speech \
ModelId={MODEL_ID} \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

### <a name="logging-example-for-custom-text-to-speech"></a>Exemplo de registo de texto-a-fala personalizado

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

- Rever [Como instalar e executar contentores](speech-container-howto.md)
