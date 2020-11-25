---
title: Configurar recipientes de discurso
titleSuffix: Azure Cognitive Services
description: O serviço de fala fornece a cada recipiente uma estrutura de configuração comum, para que possa configurar e gerir facilmente o armazenamento, registo e telemetria e as definições de segurança para os seus contentores.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: aahi
ms.openlocfilehash: e65bb7c7d8fc04baec6b50a53519e689e748fbe1
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96012174"
---
# <a name="configure-speech-service-containers"></a>Configurar recipientes de serviço de fala

Os recipientes de fala permitem que os clientes construam uma arquitetura de aplicação de fala que esteja otimizada para tirar partido das capacidades de nuvem robustas e da localidade de borda. Os cinco recipientes de discurso que apoiamos agora são, **fala-a-texto,** **discurso-a-texto personalizado,** **texto-a-discurso,** **texto-a-discurso neural** e **texto-a-discurso personalizado.**

O ambiente de funcionamento do recipiente **da fala** é configurado utilizando os argumentos de `docker run` comando. Este recipiente tem várias configurações necessárias, juntamente com algumas configurações opcionais. Vários [exemplos](#example-docker-run-commands) do comando estão disponíveis. As definições específicas do contentor são as definições de faturação.

## <a name="configuration-settings"></a>Definições de configuração

[!INCLUDE [Container shared configuration settings table](../../../includes/cognitive-services-containers-configuration-shared-settings-table.md)]

> [!IMPORTANT]
> As [`ApiKey`](#apikey-configuration-setting) [`Billing`](#billing-configuration-setting) definições , e [`Eula`](#eula-setting) configurações são utilizadas em conjunto, e deve fornecer valores válidos para os três; caso contrário, o seu recipiente não arranca. Para obter mais informações sobre a utilização destas configurações para instantaneaizar um recipiente, consulte [Billing](speech-container-howto.md#billing).

## <a name="apikey-configuration-setting"></a>Definição de configuração apikey

A `ApiKey` definição especifica a chave de recursos Azure utilizada para rastrear as informações de faturação do recipiente. Tem de especificar um valor para o ApiKey e o valor deve ser uma chave válida para o recurso _Discurso_ especificado para a definição de [`Billing`](#billing-configuration-setting) configuração.

Esta definição pode ser encontrada no seguinte local:

- Portal Azure: Gestão de Recursos **da Fala,** em **Chaves**

## <a name="applicationinsights-setting"></a>Configuração de AplicaçõesInsights

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>Definição de configuração de faturação

A `Billing` definição especifica o ponto final URI do recurso _Discurso_ no Azure utilizado para medir as informações de faturação do recipiente. Tem de especificar um valor para esta definição de configuração, e o valor deve ser um URI de ponto final válido para um recurso _de Voz_ no Azure. O recipiente relata o uso a cada 10 a 15 minutos.

Esta definição pode ser encontrada no seguinte local:

- Portal Azure: Visão geral **da fala,** rotulada `Endpoint`

| Necessário | Nome | Tipo de dados | Descrição |
| -------- | ---- | --------- | ----------- |
| Sim | `Billing` | String | URI de faturação. Para obter mais informações sobre a obtenção do URI de faturação, consulte [a recolha dos parâmetros necessários](speech-container-howto.md#gathering-required-parameters). Para obter mais informações e uma lista completa de pontos finais regionais, consulte [os nomes de subdomínio personalizados para serviços cognitivos.](../cognitive-services-custom-subdomains.md) |

## <a name="eula-setting"></a>Definição de Eula

[!INCLUDE [Container shared configuration eula settings](../../../includes/cognitive-services-containers-configuration-shared-settings-eula.md)]

## <a name="fluentd-settings"></a>Configurações fluentes

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-fluentd.md)]

## <a name="http-proxy-credentials-settings"></a>Definições de credenciais de procuração HTTP

[!INCLUDE [Container shared HTTP proxy settings](../../../includes/cognitive-services-containers-configuration-shared-settings-http-proxy.md)]

## <a name="logging-settings"></a>Definições de registo

[!INCLUDE [Container shared configuration logging settings](../../../includes/cognitive-services-containers-configuration-shared-settings-logging.md)]

## <a name="mount-settings"></a>Configurações de montagem

Utilize suportes de ligação para ler e escrever dados de e para o recipiente. Pode especificar um suporte de entrada ou de saída especificando a `--mount` opção no comando de execução do [estivador.](https://docs.docker.com/engine/reference/commandline/run/)

Os recipientes Standard Speech não utilizam suportes de entrada ou saída para armazenar dados de treino ou serviço. No entanto, os recipientes de fala personalizados dependem de suportes de volume.

A sintaxe exata da localização do suporte do hospedeiro varia consoante o sistema operativo do hospedeiro. Além disso, a localização do suporte do [computador anfitrião](speech-container-howto.md#the-host-computer)pode não estar acessível devido a um conflito entre permissões utilizadas pela conta de serviço do estivador e as permissões de localização do anfitrião.

| Opcional | Nome | Tipo de dados | Descrição |
| -------- | ---- | --------- | ----------- |
| Não permitido | `Input` | String | Os recipientes de fala padrão não usam isto. Os recipientes de fala personalizados utilizam [suportes de volume](#volume-mount-settings).                                                                                    |
| Opcional | `Output` | String | O alvo do suporte de saída. O valor predefinido é `/output`. Esta é a localização dos registos. Isto inclui registos de contentores. <br><br>Exemplo:<br>`--mount type=bind,src=c:\output,target=/output` |

## <a name="volume-mount-settings"></a>Configurações de montagem de volume

Os recipientes de fala personalizados usam [suportes de volume](https://docs.docker.com/storage/volumes/) para persistir em modelos personalizados. Pode especificar um suporte de volume adicionando a `-v` `--volume` opção (ou) ao comando de execução do [estivador.](https://docs.docker.com/engine/reference/commandline/run/)

Os modelos personalizados são descarregados da primeira vez que um novo modelo é ingerido como parte do comando personalizado do estivador de estivador de porta-estivador de discurso. As corridas sequenciais do mesmo `ModelId` para um recipiente de fala personalizado utilizarão o modelo previamente descarregado. Se o suporte de volume não for fornecido, os modelos personalizados não podem ser persistidos.

A definição de montagem de volume é composta por três campos separados de três `:` cores:

1. O primeiro campo é o nome do volume na máquina hospedeira, por exemplo _C:\entrada_.
2. O segundo campo é o diretório no contentor, por exemplo _/usr/local/modelos_.
3. O terceiro campo (opcional) é uma lista de opções separadas por vírgula, para obter mais informações ver [volumes de utilização](https://docs.docker.com/storage/volumes/).

### <a name="volume-mount-example"></a>Exemplo de montagem de volume

```bash
-v C:\input:/usr/local/models
```

Este comando monta a máquina de hospedeiro _C:\input_ directy para os contentores _/usr/local/diretório de modelos._

> [!IMPORTANT]
> As definições de montagem de volume só são aplicáveis aos recipientes **personalizados de discurso a texto** e de texto para a fala **personalizados.** Os **recipientes Fala-a-texto,** **Texto-a-fala Neural** e os recipientes **texto-a-fala** não utilizam suportes de volume.

## <a name="example-docker-run-commands"></a>Exemplo de estivador executar comandos

Os exemplos a seguir utilizam as definições de configuração para ilustrar como escrever e utilizar `docker run` comandos. Uma vez em funcionamento, o recipiente continua a funcionar até o [parar.](speech-container-howto.md#stop-the-container)

- **Carácter de continuação da linha**: Os comandos do Docker nas seguintes secções usam o corte `\` traseiro, como um personagem de continuação de linha. Substitua ou remova isto com base nos requisitos do seu sistema operativo anfitrião.
- **Ordem de argumento**: Não altere a ordem dos argumentos a menos que esteja familiarizado com os recipientes docker.

Substitua {_argument_name_} com os seus próprios valores:

| Marcador de posição | Valor | Formato ou exemplo |
| ----------- | ----- | ----------------- |
| **{API_KEY}** | A chave de ponta final do `Speech` recurso na página Azure `Speech` Keys.   | `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`                                                                                  |
| **{ENDPOINT_URI}** | O valor do ponto final de faturação está disponível na página Azure `Speech` Overview. | Consulte [os parâmetros necessários](speech-container-howto.md#gathering-required-parameters) para obter exemplos explícitos. |

[!INCLUDE [subdomains-note](../../../includes/cognitive-services-custom-subdomains-note.md)]

> [!IMPORTANT]
> As `Eula` `Billing` opções , e `ApiKey` opções devem ser especificadas para executar o recipiente; caso contrário, o recipiente não arranca. Para mais informações, consulte [Billing.](#billing-configuration-setting)
> O valor ApiKey é a **chave** da página de teclas de Recursos de Fala Azure.

## <a name="speech-container-docker-examples"></a>Exemplos de Docker de contentor de fala

Os seguintes exemplos do Docker são para o recipiente da Fala.

## <a name="speech-to-text"></a>[Conversão de voz em texto](#tab/stt)

### <a name="basic-example-for-speech-to-text"></a>Exemplo básico para Discurso-a-Texto

```Docker
docker run --rm -it -p 5000:5000 --memory 4g --cpus 4 \
mcr.microsoft.com/azure-cognitive-services/speechservices/speech-to-text \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

### <a name="logging-example-for-speech-to-text"></a>Exemplo de registo para Discurso-a-Texto

```Docker
docker run --rm -it -p 5000:5000 --memory 4g --cpus 4 \
mcr.microsoft.com/azure-cognitive-services/speechservices/custom-speech-to-text \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY} \
Logging:Console:LogLevel:Default=Information
```

## <a name="custom-speech-to-text"></a>[Discurso-a-texto personalizado](#tab/cstt)

### <a name="basic-example-for-custom-speech-to-text"></a>Exemplo básico para discurso-a-texto personalizado

```Docker
docker run --rm -it -p 5000:5000 --memory 4g --cpus 4 \
-v {VOLUME_MOUNT}:/usr/local/models \
mcr.microsoft.com/azure-cognitive-services/speechservices/custom-speech-to-text \
ModelId={MODEL_ID} \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

### <a name="logging-example-for-custom-speech-to-text"></a>Registação de exemplo para discurso-a-texto personalizado

```Docker
docker run --rm -it -p 5000:5000 --memory 4g --cpus 4 \
-v {VOLUME_MOUNT}:/usr/local/models \
mcr.microsoft.com/azure-cognitive-services/speechservices/custom-speech-to-text \
ModelId={MODEL_ID} \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY} \
Logging:Console:LogLevel:Default=Information
```

## <a name="text-to-speech"></a>[Conversão de texto em voz](#tab/tss)

### <a name="basic-example-for-text-to-speech"></a>Exemplo básico para texto-a-discurso

```Docker
docker run --rm -it -p 5000:5000 --memory 2g --cpus 1 \
mcr.microsoft.com/azure-cognitive-services/speechservices/text-to-speech \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

### <a name="logging-example-for-text-to-speech"></a>Exemplo de registo para texto-a-voz

```Docker
docker run --rm -it -p 5000:5000 --memory 2g --cpus 1 \
mcr.microsoft.com/azure-cognitive-services/speechservices/text-to-speech \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY} \
Logging:Console:LogLevel:Default=Information
```

## <a name="custom-text-to-speech"></a>[Texto-a-discurso personalizado](#tab/ctts)

### <a name="basic-example-for-custom-text-to-speech"></a>Exemplo básico para texto-a-voz personalizado

```Docker
docker run --rm -it -p 5000:5000 --memory 2g --cpus 1 \
-v {VOLUME_MOUNT}:/usr/local/models \
mcr.microsoft.com/azure-cognitive-services/speechservices/custom-text-to-speech \
ModelId={MODEL_ID} \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

### <a name="logging-example-for-custom-text-to-speech"></a>Registação de exemplo para texto-a-voz personalizado

```Docker
docker run --rm -it -p 5000:5000 --memory 2g --cpus 1 \
-v {VOLUME_MOUNT}:/usr/local/models \
mcr.microsoft.com/azure-cognitive-services/speechservices/custom-text-to-speech \
ModelId={MODEL_ID} \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY} \
Logging:Console:LogLevel:Default=Information
```

## <a name="neural-text-to-speech"></a>[Texto-a-discurso neural](#tab/ntts)

### <a name="basic-example-for-neural-text-to-speech"></a>Exemplo básico para texto neural-a-voz

```Docker
docker run --rm -it -p 5000:5000 --memory 12g --cpus 6 \
mcr.microsoft.com/azure-cognitive-services/speechservices/neural-text-to-speech \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

### <a name="logging-example-for-neural-text-to-speech"></a>Exemplo de registo para texto neural-a-voz

```Docker
docker run --rm -it -p 5000:5000 --memory 12g --cpus 6 \
mcr.microsoft.com/azure-cognitive-services/speechservices/neural-text-to-speech \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY} \
Logging:Console:LogLevel:Default=Information
```

## <a name="speech-language-detection"></a>[Deteção de linguagem da fala](#tab/lid)

### <a name="basic-example-for-speech-language-detection"></a>Exemplo básico para deteção de linguagem de fala

```Docker
docker run --rm -it -p 5000:5000 --memory 12g --cpus 6 \
mcr.microsoft.com/azure-cognitive-services/speechservices/language-detection \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

### <a name="logging-example-for-speech-language-detection"></a>Exemplo de registo para deteção de linguagem de fala

```Docker
docker run --rm -it -p 5000:5000 --memory 12g --cpus 6 \
mcr.microsoft.com/azure-cognitive-services/speechservices/language-detection \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY} \
Logging:Console:LogLevel:Default=Information
```

---

## <a name="next-steps"></a>Passos seguintes

- Rever [Como instalar e executar contentores](speech-container-howto.md)
