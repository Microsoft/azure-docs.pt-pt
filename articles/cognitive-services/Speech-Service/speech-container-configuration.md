---
title: Configure recipientes de fala
titleSuffix: Azure Cognitive Services
description: O serviço de fala fornece a cada recipiente um quadro de configuração comum, para que possa configurar e gerir facilmente o armazenamento, a extração e a telemetria e as definições de segurança dos seus contentores.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 5c74aa48b18661236eb55278d1e5a05215b2432c
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "80877580"
---
# <a name="configure-speech-service-containers"></a>Configure recipientes de serviço de fala

Os recipientes de fala permitem aos clientes construir uma arquitetura de aplicação de fala que é otimizada para tirar partido de capacidades de nuvem robustas e localidade de borda. Os quatro contentores da fala que apoiamos agora são, **discurso a texto,** **discurso personalizado- discurso-a-texto,** **texto-a-fala,** e **texto-a-fala personalizado.**

O ambiente de execução do `docker run` contentor da **fala** é configurado usando os argumentos de comando. Este recipiente tem várias configurações necessárias, juntamente com algumas configurações opcionais. Vários [exemplos](#example-docker-run-commands) do comando estão disponíveis. As definições específicas do recipiente são as definições de faturação.

## <a name="configuration-settings"></a>Definições de configuração

[!INCLUDE [Container shared configuration settings table](../../../includes/cognitive-services-containers-configuration-shared-settings-table.md)]

> [!IMPORTANT]
> As [`ApiKey`](#apikey-configuration-setting) [`Billing`](#billing-configuration-setting)definições [`Eula`](#eula-setting) e as configurações são utilizadas em conjunto, e deve fornecer valores válidos para todos os três; caso contrário, o seu recipiente não arranca. Para obter mais informações sobre a utilização destas definições de configuração para instantaneamente um recipiente, consulte [a Faturação](speech-container-howto.md#billing).

## <a name="apikey-configuration-setting"></a>Definição de configuração ApiKey

A `ApiKey` definição especifica a chave de recursos Azure utilizada para rastrear as informações de faturação do recipiente. Deve especificar um valor para o ApiKey e o valor deve ser [`Billing`](#billing-configuration-setting) uma chave válida para o recurso da _Fala_ especificado para a definição de configuração.

Esta definição pode ser encontrada no seguinte local:

- Portal Azure: Gestão de Recursos **da Fala,** sob **Chaves**

## <a name="applicationinsights-setting"></a>Definição de ApplicationInsights

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>Definição de configuração de faturação

A `Billing` definição especifica o ponto final URI do recurso _Speech_ em Azure usado para medir informações de faturação para o recipiente. Deve especificar um valor para esta configuração, e o valor deve ser um uri de ponto final válido para um recurso _da Fala_ no Azure. O recipiente reporta o uso a cada 10 a 15 minutos.

Esta definição pode ser encontrada no seguinte local:

- Portal Azure: Visão geral **do discurso,** rotulada`Endpoint`

| Necessário | Nome | Tipo de dados | Descrição |
| -------- | ---- | --------- | ----------- |
| Sim | `Billing` | String | Ponto final de faturação URI. Para obter mais informações sobre a obtenção do URI de faturação, consulte a [recolha de parâmetros necessários](speech-container-howto.md#gathering-required-parameters). Para mais informações e uma lista completa de pontos finais regionais, consulte [nomes de subdomínio personalizado para Serviços Cognitivos](../cognitive-services-custom-subdomains.md). |

## <a name="eula-setting"></a>Cenário eula

[!INCLUDE [Container shared configuration eula settings](../../../includes/cognitive-services-containers-configuration-shared-settings-eula.md)]

## <a name="fluentd-settings"></a>Configurações fluentes

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-fluentd.md)]

## <a name="http-proxy-credentials-settings"></a>Definições de credenciais de procuração HTTP

[!INCLUDE [Container shared HTTP proxy settings](../../../includes/cognitive-services-containers-configuration-shared-settings-http-proxy.md)]

## <a name="logging-settings"></a>Definições de registo

[!INCLUDE [Container shared configuration logging settings](../../../includes/cognitive-services-containers-configuration-shared-settings-logging.md)]

## <a name="mount-settings"></a>Configurações do monte

Utilize suportes de ligação para ler e escrever dados de e para o recipiente. Pode especificar um suporte de entrada ou `--mount` montagem de saída especificando a opção no comando de execução do [estivador.](https://docs.docker.com/engine/reference/commandline/run/)

Os recipientes Standard Speech não utilizam suportes de entrada ou saída para armazenar dados de formação ou de serviço. No entanto, os recipientes de fala personalizados dependem de suportes de volume.

A sintaxe exata da localização do suporte do hospedeiro varia consoante o sistema operativo do hospedeiro. Além disso, a localização do suporte do [computador hospedeiro](speech-container-howto.md#the-host-computer)pode não estar acessível devido a um conflito entre permissões utilizadas pela conta de serviço do estivador e as permissões de localização do suporte do hospedeiro.

| Opcional | Nome | Tipo de dados | Descrição |
| -------- | ---- | --------- | ----------- |
| Não permitido | `Input` | String | Os recipientes standard de fala não usam isto. Os recipientes de fala personalizados utilizam [suportes](#volume-mount-settings)de volume .                                                                                    |
| Opcional | `Output` | String | O alvo do suporte de saída. O valor predefinido é `/output`. Esta é a localização dos registos. Isto inclui troncos de contentores. <br><br>Exemplo:<br>`--mount type=bind,src=c:\output,target=/output` |

## <a name="volume-mount-settings"></a>Configurações de montagem de volume

Os recipientes de fala personalizados usam [suportes](https://docs.docker.com/storage/volumes/) de volume para persistir modelos personalizados. Pode especificar um suporte de `-v` volume `--volume`adicionando a (ou ) opção ao comando de execução do [estivador.](https://docs.docker.com/engine/reference/commandline/run/)

Os modelos personalizados são descarregados na primeira vez que um novo modelo é ingerido como parte do comando de execução de portais personalizado. As corridas sequenciais `ModelId` do mesmo para um recipiente de discurso personalizado usarão o modelo previamente descarregado. Se o volume não for fornecido, os modelos personalizados não podem ser persistidos.

A definição de montagem `:` de volume consiste em três campos separados a cores:

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

## <a name="example-docker-run-commands"></a>Comandos de execução de estivadores exemplo

Os exemplos seguintes utilizam as definições `docker run` de configuração para ilustrar como escrever e utilizar comandos. Uma vez em funcionamento, o recipiente continua a funcionar até o [parar.](speech-container-howto.md#stop-the-container)

- **Personagem de continuação da linha**: Os comandos `\`do Docker nas seguintes secções usam o corte traseiro, como um personagem de continuação da linha. Substitua ou remova isto com base nos requisitos do sistema operativo do hospedeiro.
- **Ordem de argumentação**: Não altere a ordem dos argumentos a menos que esteja familiarizado com os contentores do Docker.

Substitua {_argument_name_} com os seus próprios valores:

| Marcador de posição | Valor | Formato ou exemplo |
| ----------- | ----- | ----------------- |
| **{API_KEY}** | A chave final `Speech` do recurso na `Speech` página Azure Keys.   | `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`                                                                                  |
| **{ENDPOINT_URI}** | O valor final da faturação está `Speech` disponível na página de visão geral do Azure. | Consulte a [recolha de parâmetros necessários](speech-container-howto.md#gathering-required-parameters) para exemplos explícitos. |

[!INCLUDE [subdomains-note](../../../includes/cognitive-services-custom-subdomains-note.md)]

> [!IMPORTANT]
> A `Eula` `Billing`, `ApiKey` e as opções devem ser especificadas para executar o recipiente; caso contrário, o contentor não vai começar. Para mais informações, consulte [billing.](#billing-configuration-setting)
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
