---
title: Etiquetas de imagem de contentor dos Serviços Cognitivos
titleSuffix: Azure Cognitive Services
description: Uma lista completa de todas as etiquetas de imagem do serviço cognitivo.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: reference
ms.date: 11/17/2020
ms.author: aahi
ms.openlocfilehash: 90ac4891dbca34ff2d9bdb9653e27ad2a495b317
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/21/2020
ms.locfileid: "95021257"
---
# <a name="azure-cognitive-services-container-image-tags-and-release-notes"></a>Etiquetas de imagem de contentores Azure Cognitive Services e notas de lançamento

A Azure Cognitive Services oferece muitas imagens de contentores. Os registos dos contentores e os repositórios correspondentes variam entre as imagens dos contentores. Cada nome de imagem do recipiente oferece várias tags. Uma etiqueta de imagem de recipiente é um mecanismo de versão da imagem do recipiente. Este artigo destina-se a ser usado como uma referência abrangente para a listagem de todas as imagens dos contentores dos Serviços Cognitivos e suas etiquetas disponíveis.

> [!TIP]
> Ao [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) utilizar, preste muita atenção ao invólucro do registo do contentor, repositório, nome de imagem do recipiente e etiqueta correspondente - uma vez que são **sensíveis ao caso**.

## <a name="anomaly-detector"></a>Detetor de Anomalias

A imagem do detetor [de anomalias][ad-containers] pode ser encontrada no sindicato do registo do `mcr.microsoft.com` contentor. Reside dentro do `azure-cognitive-services/decision` repositório e é `anomaly-detector` nomeado. O nome de imagem do recipiente totalmente qualificado é, `mcr.microsoft.com/azure-cognitive-services/decision/anomaly-detector` .

Esta imagem do recipiente tem as seguintes etiquetas disponíveis. Também pode encontrar uma lista completa de [tags no MCR](https://mcr.microsoft.com/v2/azure-cognitive-services/decision/anomaly-detector/tags/list).

# <a name="latest-version"></a>[Versão mais recente](#tab/current)

| Etiquetas de imagem                    | Notas |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.013560003-amd64-preview` |      |

# <a name="previous-versions"></a>[Versões anteriores](#tab/previous)

| Etiquetas de imagem                    | Notas |
|-------------------------------|:------|
| `1.1.012300001-amd64-preview` |       |

---

## <a name="read-ocr-optical-character-recognition"></a>Ler OCR (Reconhecimento de Caracteres Óticos)

A imagem do contentor OCR da [Visão de Computador][cv-containers] pode ser encontrada no sindicato do registo do `mcr.microsoft.com` contentor. Reside dentro do `azure-cognitive-services` repositório e é `read` nomeado. O nome de imagem do recipiente totalmente qualificado é, `mcr.microsoft.com/azure-cognitive-services/vision/read` .

Esta imagem do recipiente tem as seguintes etiquetas disponíveis. Também pode encontrar uma lista completa de [tags no MCR](https://mcr.microsoft.com/v2/azure-cognitive-services/vision/read/tags/list).

# <a name="latest-version"></a>[Versão mais recente](#tab/current)

Notas de lançamento `v2.0.013250001-amd64-preview` para:

* Diminua ainda mais o uso da memória para o recipiente.
* É necessária cache externa para a configuração de várias cápsulas. Por exemplo, configurar Redis para o caching.
* Resultados em falta fixos quando a cache redis é configurada e `ResultExpirationPeriod` está definida para 0.
* Remova a limitação do tamanho do corpo do pedido de 26MB. O recipiente pode agora aceitar >ficheiros de 26MB.
* Adicione o carimbo de tempo e construa a versão para o registo de consolas.

| Etiquetas de imagem                    | Notas |
|-------------------------------|:------|
| `latest`                      |       |
| `2.0.013250001-amd64-preview` |  |

# <a name="previous-versions"></a>[Versões anteriores](#tab/previous)

Notas de lançamento para `1.1.013050001-amd64-preview`

* Configuração de inicialização de `ReadEngineConfig:ResultExpirationPeriod` contentores adicionada para especificar quando o sistema deve limpar os resultados do reconhecimento. 
    * A definição é em horas, e o valor predefinido é de 48 horas.
    * A definição pode reduzir a utilização da memória para o armazenamento de resultados, especialmente quando é utilizado o armazenamento em memória do recipiente.
    * Exemplo 1. ReadEngineConfig:ResultExpirationPeriod=1, o sistema limpará o resultado do reconhecimento 1hr após o processo.
    * Se esta configuração estiver definida para 0, o sistema limpará o resultado do reconhecimento após a recuperação do resultado.

* Corrigiu um erro de servidor interno de 500 quando um formato de imagem inválido é passado para o sistema. Irá agora devolver um erro de 400:

    ```json
    {
        "error": {
        "code": "InvalidImageSize",
        "message": "Image must be between 1024 and 209715200 bytes."
        }
    }
    ```

| Etiquetas de imagem                    | Notas |
|-------------------------------|:------|
| `1.1.013050001-amd64-preview` |       |
| `1.1.011580001-amd64-preview` |       |
| `1.1.009920003-amd64-preview` |       |
| `1.1.009910003-amd64-preview` |       |

---


## <a name="form-recognizer"></a>Reconhecedor de Formato

A imagem do contentor [do "Reconhecimento de Formulários"][fr-containers] pode ser encontrada no sindicato do registo do `mcr.microsoft.com` contentor. Reside dentro do `azure-cognitive-services/custom-form` repositório e é `labeltool` nomeado. O nome de imagem do recipiente totalmente qualificado é, `mcr.microsoft.com/azure-cognitive-services/custom-form/labeltool` .

Esta imagem do recipiente tem as seguintes etiquetas disponíveis. Também pode encontrar uma lista completa de [tags no MCR](https://mcr.microsoft.com/v2/azure-cognitive-services/custom-form/labeltool/tags/list).

# <a name="latest-version"></a>[Versão mais recente](#tab/current)

| Etiquetas de imagem                    | Notas |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.009301-amd64-preview`    |       |


# <a name="previous-versions"></a>[Versões anteriores](#tab/previous)

| Etiquetas de imagem                    | Notas |
|-------------------------------|:------|
| `1.1.008640001-amd64-preview` |       |
| `1.1.008510001-amd64-preview` |       |

---

## <a name="language-understanding-luis"></a>Compreensão de Idiomas (LUIS)

A imagem do contentor [LUIS][lu-containers] pode ser encontrada no sindicato do `mcr.microsoft.com` registo de contentores. Reside dentro do `azure-cognitive-services/language` repositório e é `luis` nomeado. O nome de imagem do recipiente totalmente qualificado é, `mcr.microsoft.com/azure-cognitive-services/language/luis` .

Esta imagem do recipiente tem as seguintes etiquetas disponíveis. Também pode encontrar uma lista completa de [tags no MCR](https://mcr.microsoft.com/v2/azure-cognitive-services/language/luis/tags/list).

# <a name="latest-version"></a>[Versão mais recente](#tab/current)

| Etiquetas de imagem                    | Notas |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.012280003-amd64-preview` |       |


# <a name="previous-version"></a>[Versão anterior](#tab/previous)

| Etiquetas de imagem                    | Notas |
|-------------------------------|:------|
| `1.1.012130003-amd64-preview` |       |

---

## <a name="custom-speech-to-text"></a>Discurso-a-texto personalizado

A imagem do recipiente [personalizado discurso-a-texto][sp-cstt] pode ser encontrada no sindicato do registo do `mcr.microsoft.com` contentor. Reside dentro do `azure-cognitive-services/speechservices/` repositório e é `custom-speech-to-text` nomeado. O nome de imagem do recipiente totalmente qualificado é, `mcr.microsoft.com/azure-cognitive-services/speechservices/custom-speech-to-text` . Também pode encontrar uma lista completa de [tags no MCR](https://mcr.microsoft.com/v2/azure-cognitive-services/speechservices/custom-speech-to-text/tags/list).


# <a name="latest-version"></a>[Versão mais recente](#tab/current)

Nota de lançamento `2.7.0-amd64` para:

**Funcionalidades**
* A pontuação é definida como ativada por defeito.

Note que devido às listas de frases incluídas, o tamanho desta imagem do recipiente aumentou.

| Etiquetas de imagem                    | Notas | Digerir                                                                  |
|-------------------------------|:------|:------------------------------------------------------------------------|
| `latest`                      |       | `sha256:d1573c2543cb7afedb0122da0995f345767b02f9c5f181950acf1509ca65726` |
| `2.7.0-amd64`                 |       | `sha256:d1573c2543cb7afedb0122da0995f345767b02f9c5f181950acf1509ca65726` |


# <a name="previous-version"></a>[Versão anterior](#tab/previous)
Nota de lançamento `2.6.0-amd64` para:

**Funcionalidades**
* Suporte para lista de frases v2 
* As listas de frases são suportadas nos seguintes locais:
    * en-au
    * en-ca
    * en-gb
    * en-in
    * pt-pt
    * zh-cn
* Suporte para download personalizado do modelo base. 
    * Usar `BaseModelLocale=<locale>` com o `docker run` comando
* Totalmente migrado para .NET 3.1

**Correções**
* Corrigiu um problema em que a pontuação de confiança era sempre 1 no modo de diarização
* Migrado para o TextAnalytics 3.0 api

Note que devido às listas de frases incluídas, o tamanho desta imagem do recipiente aumentou.

Nota de lançamento `2.5.0-amd64` para:

**Funcionalidades**
* Suporte pronúncia personalizada em modelos personalizados
* Apoiar Azure e Azure US Government Cloud

**Correções**
* Correção executada como problema de utilizador não raiz no modo de diarização

| Etiquetas de imagem                    | Notas               |
|-------------------------------|:--------------------|
| `2.6.0-amd64`                 |                     |
| `2.5.0-amd64`                 |   1ª versão GA    |

---

## <a name="custom-text-to-speech"></a>Texto-a-discurso personalizado

A imagem do recipiente [de texto para a fala personalizado][sp-ctts] pode ser encontrada no sindicato do registo do `mcr.microsoft.com` contentor. Reside dentro do `azure-cognitive-services/speechservices/` repositório e é `custom-text-to-speech` nomeado. O nome de imagem do recipiente totalmente qualificado é, `mcr.microsoft.com/azure-cognitive-services/speechservices/custom-text-to-speech` . Também pode encontrar uma lista completa de [tags no MCR](https://mcr.microsoft.com/v2/azure-cognitive-services/speechservices/custom-text-to-speech/tags/list).


# <a name="latest-version"></a>[Versão mais recente](#tab/current)

Nota de lançamento `1.9.0-amd64` para:

Lançamento mensal regular

| Etiquetas de imagem                    | Notas | Digerir                                                                  |
|-------------------------------|:------|:------------------------------------------------------------------------|
| `latest`                      |       | `sha256:e0397cf12d1367b13dd258f782bb513c93afcd5ee4b897794fe533205336355` |
| `1.9.0-amd64`                 |       | `sha256:e0397cf12d1367b13dd258f782bb513c93afcd5ee4b897794fe533205336355` |


# <a name="previous-version"></a>[Versão anterior](#tab/previous)
Nota de lançamento `1.8.0-amd64` para:

**Funcionalidades**
* Totalmente migrado para .NET 3.1

Nota de lançamento `1.7.0-amd64` para:

**Funcionalidade**
* Parcialmente migrado para .NET 3.1

| Etiquetas de imagem                    | Notas               |
|-------------------------------|:--------------------|
| `1.8.0-amd64`                 |                     |
| `1.7.0-amd64`                 |   1ª versão GA    |

---

## <a name="speech-to-text"></a>Conversão de voz em texto

A imagem do contentor [discurso-a-texto][sp-stt] pode ser encontrada no sindicato do registo do `mcr.microsoft.com` contentor. Reside dentro do `azure-cognitive-services/speechservices/` repositório e é `speech-to-text` nomeado. O nome de imagem do recipiente totalmente qualificado é, `mcr.microsoft.com/azure-cognitive-services/speechservices/speech-to-text` . Pode encontrar uma lista completa de [etiquetas no MCR](https://mcr.microsoft.com/v2/azure-cognitive-services/speechservices/speech-to-text/tags/list).

Desde discurso-a-texto v2.5.0, as imagens são apoiadas na região *do governo dos EUA Virginia.* Por favor, use as chaves de ponta de faturação do *governo dos EUA* e apiítmo ao usar esta região.

# <a name="latest-version"></a>[Versão mais recente](#tab/current)

Nota de lançamento `2.7.0-amd64-<locale>` para:

**Funcionalidades**
* Apoio aos seguintes novos locais:
    * ar-bh, ar-iq, ar-jo, ar-lb, ar-om, ar-sy
    * bg-bg
    * el-gr
    * en-hk, en-ie, en-ph, en-sg, en-za
    * es-ar, es-bo, es-cl, es-co, es-cr, es-cu, es-do, es-ec, es-gt, es-pa, es-pe, es-pr, es-py, es-sv, es-us, es-uy, es-ve
    * et-ee
    * ga-ie
    * hr-hr
    * hu-hu
    * Lt-lt
    * lv-lv
    * mt-mt
    * ro-ro
    * sk-sk
    * sl-sl
* A pontuação é ativada por defeito.

Note que devido às listas de frases incluídas, o tamanho desta imagem do recipiente aumentou. 

| Etiquetas de imagem                    | Notas                                                                                                |
|-------------------------------|:-----------------------------------------------------------------------------------------------------|
| `latest`                      | Imagem de recipiente com o `en-US` local.                                                             |
| `2.7.0-amd64-<locale>`        | Substitua `<locale>` por um dos locais disponíveis, listado abaixo. Por exemplo, `2.7.0-amd64-en-us`. |

Este recipiente tem os seguintes locais disponíveis.

| Local para v2.7.0           | Notas                                    | Digerir                                                                  |
|-----------------------------|:-----------------------------------------|:------------------------------------------------------------------------|
| `ar-ae`                     | Imagem de recipiente com o `ar-AE` local. | `sha256:c8e99e71e6740cf671f3bf79de8b7dd890122cb674eedd2440e71e7cbc4c66b` |
| `ar-bh`                     | Imagem de recipiente com o `ar-BH` local. | `sha256:5a2c140661f50d0c95587121ec1ab8895289f4dda5b3ad14074413e869e6bd4` |
| `ar-eg`                     | Imagem de recipiente com o `ar-EG` local. | `sha256:783bb8321fcfb7890b0c99935099f7e84c85a698c2fe0031c661e265358d79c` |
| `ar-iq`                     | Imagem de recipiente com o `ar-IQ` local. | `sha256:abd0101f73c1cf71f30da7b11b93d2a7ac8877dbfcfc2d34553d20705aca7a2` |
| `ar-jo`                     | Imagem de recipiente com o `ar-JO` local. | `sha256:d4c7fd2a1637e163aa106c23b6a759e8c78366c60ece83b3aabfe93ebabae07` |
| `ar-kw`                     | Imagem de recipiente com o `ar-KW` local. | `sha256:c8e99e71e6740cf671f3bf79de8b7dd890122cb674eedd2440e71e7cbc4c66b` |
| `ar-lb`                     | Imagem de recipiente com o `ar-LB` local. | `sha256:20e5c9105e86625c72de54290a6eb07630d35c3760f729c4b855e3661583dfe` |
| `ar-om`                     | Imagem de recipiente com o `ar-OM` local. | `sha256:97f1b44f2cbb837a2ef86441a0a52a07f706240edb6ef6618ee4db8cbbe1c19` |
| `ar-qa`                     | Imagem de recipiente com o `ar-QA` local. | `sha256:c8e99e71e6740cf671f3bf79de8b7dd890122cb674eedd2440e71e7cbc4c66b` |
| `ar-sa`                     | Imagem de recipiente com o `ar-SA` local. | `sha256:c8e99e71e6740cf671f3bf79de8b7dd890122cb674eedd2440e71e7cbc4c66b` |
| `ar-sy`                     | Imagem de recipiente com o `ar-SY` local. | `sha256:51980a2e2c3dd3548deedcedaf5fc688db602a5eced1a4b7df7d10750393623` |
| `bg-bg`                     | Imagem de recipiente com o `bg-BG` local. | `sha256:1c1acf0fbb353ebb04692f37eb4d4cdf0b4e309720dd7e709001dada0d1ea81` |
| `ca-es`                     | Imagem de recipiente com o `ca-ES` local. | `sha256:c60baa0007f61c7652b97b49645215de63411125d627c974c09222e316df204` |
| `cs-cz`                     | Imagem de recipiente com o `cs-CZ` local. | `sha256:3fa09fc3a6bde6b77df2444aae8fc78b5f25fb9010171d1682db116ea5801f5` |
| `da-dk`                     | Imagem de recipiente com o `da-DK` local. | `sha256:4b26dbba50c2771943880b68e0e4ea0713d0e3bb8bad884454849bccc9e94a3` |
| `de-de`                     | Imagem de recipiente com o `de-DE` local. | `sha256:5109ed80b1fecf4db0328adcd50528d0aa9e726b5fc84587c40aaea4e91256d` |
| `el-gr`                     | Imagem de recipiente com o `el-GR` local. | `sha256:fc8b466c588bf097efac2b79454d5ac0df5c6990398f07ede9be7e1d536e4bd` |
| `en-au`                     | Imagem de recipiente com o `en-AU` local. | `sha256:3461892a27fc3eb3f9610b2def00bc15f380c6b9797c90ceca19e6abb55f6a6` |
| `en-ca`                     | Imagem de recipiente com o `en-CA` local. | `sha256:a0509be39785f1e869bd96ab10e7c07d3f4e61c9aa17ff5900076e7bd64ba11` |
| `en-gb`                     | Imagem de recipiente com o `en-GB` local. | `sha256:1b976fc7ac109e61dcf74af3652c12535e3db92931d2d0bb2ea59bd46f9efed` |
| `en-hk`                     | Imagem de recipiente com o `en-HK` local. | `sha256:0b1e1df101f978869c98f6e50632712016b8311fc89b334e7f44e968d64bf2f` |
| `en-ie`                     | Imagem de recipiente com o `en-IE` local. | `sha256:c5ba0d3c7219ce39f0b918a51a7cae8a65c277f564279cad920e068725aa39f` |
| `en-in`                     | Imagem de recipiente com o `en-IN` local. | `sha256:e907f07be498f024103f6fe6abffa23e242bf3585724741b29a2f3f41d0899c` |
| `en-nz`                     | Imagem de recipiente com o `en-NZ` local. | `sha256:66845f6ce20ae71d609867c6eb4772366ce042499e4bcdce4c1b579daf7fad7` |
| `en-ph`                     | Imagem de recipiente com o `en-PH` local. | `sha256:e7874653bf66b1a1ab344b3391eb8767be34260b7f11b62fd057cbe17b805b2` |
| `en-sg`                     | Imagem de recipiente com o `en-SG` local. | `sha256:827cdb158280e6f4037f4815410c7aa78abf9c6467876c1504aecfef787bdd7` |
| `en-us`                     | Imagem de recipiente com o `en-US` local. | `sha256:248d17340055e3e137219ddc234c605e6a53ceead136ea55c9697c352da6a8d` |
| `en-za`                     | Imagem de recipiente com o `en-ZA` local. | `sha256:a8abc99f498db7088bb25acec47da81e90b6a5eaa1c6f78e0f9a314d839d0ae` |
| `es-ar`                     | Imagem de recipiente com o `es-AR` local. | `sha256:edf78429630851b6eb01f54f8a8a1aeeda9971c6a834403a204662eda22b3b9` |
| `es-bo`                     | Imagem de recipiente com o `es-BO` local. | `sha256:5832b44f1da2f6b9a097c99babfbc370d8d0eabe1ff8daabec2c3f482dc9d63` |
| `es-cl`                     | Imagem de recipiente com o `es-CL` local. | `sha256:409a712b96235e154472134f96ff9272265f1e5b555e00ad03c2260b0781009` |
| `es-co`                     | Imagem de recipiente com o `es-CO` local. | `sha256:99792bc083dc16e0edf15491e6a840d786c9140b747551563a8d98f66f0b415` |
| `es-cr`                     | Imagem de recipiente com o `es-CR` local. | `sha256:21fe14a538e5b8b2d288b00b8f5a02d87469e285f32e725155042079f336ac9` |
| `es-cu`                     | Imagem de recipiente com o `es-CU` local. | `sha256:05d40eae01cec4c42c4febd379cd61373eb43d0aacfd47b988bb95e6a6ad216` |
| `es-do`                     | Imagem de recipiente com o `es-DO` local. | `sha256:73dd0e0d4f39a259563ee7cc18c2e72c9ab20c52905fe343e0413ca7c4b3f0d` |
| `es-ec`                     | Imagem de recipiente com o `es-EC` local. | `sha256:c3e69139ef365fe9332b5b68b43458242c7dad9d9f2b557431272306e81cb9e` |
| `es-es`                     | Imagem de recipiente com o `es-ES` local. | `sha256:bd83fcfc116ba645a0e12a7a93b6ada74a8f701172f826a91c5f223a1dbaa61` |
| `es-gt`                     | Imagem de recipiente com o `es-GT` local. | `sha256:5bb9b18b91b74e123e3720893d88bfcb0a87dac31a1f7171d23c7cb1fa09fee` |
| `es-hn`                     | Imagem de recipiente com o `es-HN` local. | `sha256:941d108a4b76eb554e8f13cf5090665a702de3ebf35b75e4350f0916dfccd72` |
| `es-mx`                     | Imagem de recipiente com o `es-MX` local. | `sha256:cebea03732781b4425500d162ae6580bbd7ce9b5f4ede988c4570fe311d8567` |
| `es-ni`                     | Imagem de recipiente com o `es-NI` local. | `sha256:8ba165f94ad840936ebd0af17a0a63aa08a6292e7ad9029f5b93eef41165eb9` |
| `es-pa`                     | Imagem de recipiente com o `es-PA` local. | `sha256:c61b7f1b6801a03c3eab0dd1aede87017a86bc7368ded2f8bad8d9e5f60d0d3` |
| `es-pe`                     | Imagem de recipiente com o `es-PE` local. | `sha256:447a3ab3f302aba24d201d9f5b2877ffcd64dfd5e9d6b88d9924847160b2de2` |
| `es-pr`                     | Imagem de recipiente com o `es-PR` local. | `sha256:a53b3295c986e91ee8cf93ebe1057b997c76ef7f99913508b859311a194fdd4` |
| `es-py`                     | Imagem de recipiente com o `es-PY` local. | `sha256:85b3f75e75e63e29521daf772ee68a59ac2428579512501aa81dc51a2315652` |
| `es-sv`                     | Imagem de recipiente com o `es-SV` local. | `sha256:db5ece7ba536e38d5de59cd37807630ab76589dcf1c97e253f98d7f44d9424e` |
| `es-us`                     | Imagem de recipiente com o `es-US` local. | `sha256:99f2743725bb71e25543484f49bcfde14584ccbbaaa912678938d69d965075a` |
| `es-uy`                     | Imagem de recipiente com o `es-UY` local. | `sha256:a3e11c16a97a1ae76408d812b2fee1e4b3ba07160bbcb62a22814523568ee5d` |
| `es-ve`                     | Imagem de recipiente com o `es-VE` local. | `sha256:8cb431aafd84263ead8de946377c1d3f2ddfa7e172b8a4c5aa7ba477c5b41f0` |
| `et-ee`                     | Imagem de recipiente com o `et-EE` local. | `sha256:943e7cf894e9d75341a58993104824c1c8cd8da1322cc5a732e9d53882c6523` |
| `fi-fi`                     | Imagem de recipiente com o `fi-FI` local. | `sha256:35658e9dce796cb96a1371f250398e86351ea1b5ada080da7ce8471b30c7cae` |
| `fr-ca`                     | Imagem de recipiente com o `fr-CA` local. | `sha256:62256cad671e8baa03fdd4c5f4eca7d5c5effedd64cafd9020ba72c9c4210e0` |
| `fr-fr`                     | Imagem de recipiente com o `fr-FR` local. | `sha256:b385993232d9daa327d1a7b067268927b17f36eed3e8d423748794544c62746` |
| `ga-ie`                     | Imagem de recipiente com o `ga-IE` local. | `sha256:ab9abdb993b0f7487edda8200f1393ac44ba4888c0f444a02afb6c85ca3e393` |
| `gu-in`                     | Imagem de recipiente com o `gu-IN` local. | `sha256:328e69488f2948722d7ccc97e266071f61a8c9f65cd671688490955806526de` |
| `hi-in`                     | Imagem de recipiente com o `hi-IN` local. | `sha256:b9b0bfec80aa53d06ea2cbd9097f753ec5caaf00ac2f00321ae7ad916fd7fa6` |
| `hr-hr`                     | Imagem de recipiente com o `hr-HR` local. | `sha256:ab849cd2eeea682f8958bba8986fe90f0f7bb3b447512a10cf464e8e1ce4ea5` |
| `hu-hu`                     | Imagem de recipiente com o `hu-HU` local. | `sha256:30f239b155d91523442cf74a1f2732304fa2b50ae7b786833bb6a020b982621` |
| `it-it`                     | Imagem de recipiente com o `it-IT` local. | `sha256:288f95413870eb9d33bf1dabfa6fbd6b55b0faa52e4d5face3171d1dd4ddbdd` |
| `ja-jp`                     | Imagem de recipiente com o `ja-JP` local. | `sha256:e3ab37a80c215dec565eca212f57eb81887fc2894452868dff92e3bd42c4bb9` |
| `ko-kr`                     | Imagem de recipiente com o `ko-KR` local. | `sha256:c1208b8459333b606af516cd7806e9d4d5e002247bb1225e1f246563b356890` |
| `lt-lt`                     | Imagem de recipiente com o `lt-LT` local. | `sha256:8dec331161d3c29fc65ba6651fcc6cfe69fa314519f408b5f9f8eb27da09830` |
| `lv-lv`                     | Imagem de recipiente com o `lv-LV` local. | `sha256:7cf31282910b339666bb2b0a555caa7fc6ae414eea4423a41f35c3527f83235` |
| `mr-in`                     | Imagem de recipiente com o `mr-IN` local. | `sha256:9cb012bd58ef7723d4905d6fa3c1fde96e33c354b3d96d4e3ff69cf6e1bfe3a` |
| `mt-mt`                     | Imagem de recipiente com o `mt-MT` local. | `sha256:a0094c032ea555b168ec5751ab3257337d902d526e9ae335671fb751a352378` |
| `nb-no`                     | Imagem de recipiente com o `nb-NO` local. | `sha256:6bbc326e20a6a785b1ca33143b42a060858efb67b863a267d6efb7aebb48f87` |
| `nl-nl`                     | Imagem de recipiente com o `nl-NL` local. | `sha256:94b4ddf4cc80fa666e422f8416aea3f98ebe4842dfe9b1f4bfea7c47eb61127` |
| `pl-pl`                     | Imagem de recipiente com o `pl-PL` local. | `sha256:58e5f78bf772c3c8cbd5f0c5d6e67f5348e04e3f893d84738a2a3e964bab256` |
| `pt-br`                     | Imagem de recipiente com o `pt-BR` local. | `sha256:f500ef956bd28807f40df1f9f0520e437c5084f61a3be6d1379e746887d5b7c` |
| `pt-pt`                     | Imagem de recipiente com o `pt-PT` local. | `sha256:c841d2dbe5f40adf6039242c106985febb1a44212feb55d9769fe31134ec116` |
| `ro-ro`                     | Imagem de recipiente com o `ro-RO` local. | `sha256:93271c39c0a134e987a069c2a65289acff9869ae0d90fdcb39928c9ef0fd86b` |
| `ru-ru`                     | Imagem de recipiente com o `ru-RU` local. | `sha256:8d6b3c600e56cc96813b8c14b7916c5539a20ba561dc1c6d5bbef6285d6eef6` |
| `sk-sk`                     | Imagem de recipiente com o `sk-SK` local. | `sha256:6d604092cc6c964663a1c97d91c8f1c8cf4b46d07427d03f7041c0cc55eb521` |
| `sl-si`                     | Imagem de recipiente com o `sl-SI` local. | `sha256:f237ed58fedefcc749e74be1258cc70e5a690ee6c5a6b6388bd24075faa61da` |
| `sv-se`                     | Imagem de recipiente com o `sv-SE` local. | `sha256:da4233e6658b00eefdadb9d4acd889c6550a5e2a4a7af7a9f915c878abd4c9c` |
| `ta-in`                     | Imagem de recipiente com o `ta-IN` local. | `sha256:22b77606d25e9c2f52bf3cad6218782b4719f6a9dcfadc770468d266758a56c` |
| `te-in`                     | Imagem de recipiente com o `te-IN` local. | `sha256:7f4d11372862ca1d65fc9b868e2d775701b8e6eabd786c90c4e9ab82ba86e88` |
| `th-th`                     | Imagem de recipiente com o `th-TH` local. | `sha256:69033bcd7c0f59d31bafec6c2b7a9ff343928cdd58c16105415c291d555d37b` |
| `tr-tr`                     | Imagem de recipiente com o `tr-TR` local. | `sha256:4b7d339846a0d371dfe25aa2e626f131003c01329c9a1da468eb3703ef176ea` |
| `zh-cn`                     | Imagem de recipiente com o `zh-CN` local. | `sha256:a428459830fb766083212f71c5638a65ce30d8dd84f6c624ae22768e8a76976` |
| `zh-hk`                     | Imagem de recipiente com o `zh-HK` local. | `sha256:7a2903462b67336a6ce4c8e2faac42052f0a4392d1d5eb3839758cc8d0429f1` |
| `zh-tw`                     | Imagem de recipiente com o `zh-TW` local. | `sha256:30fd2b3660e047d24a46fbba14ba282f15bc0339ec93f49afd0d02ff4069146` |


# <a name="previous-version"></a>[Versão anterior](#tab/previous)

Nota de lançamento `2.6.0-amd64-<locale>` para:

**Funcionalidades**
* Atualizado para os modelos mais recentes e totalmente migrado para .NET 3.1
* Suporte para lista de frases v2
* As listas de frases são suportadas nos seguintes locais:
    * en-au
    * en-ca
    * en-gb
    * en-in
    * pt-pt
    * zh-cn
* Apoio a novas localidades `cs-CZ` 
    * Atualmente, a capitalização e a pontuação não são apoiadas.

**Correções**
* Corrige um problema onde as pontuações de confiança foram sempre 1 no modo de Diarização
* Utilização migrada do TextAnalytics 3.0 API

Note que devido às listas de frases incluídas, o tamanho desta imagem do recipiente aumentou. 

Nota de lançamento `2.5.0-amd64-<locale>` para:

**Funcionalidades**
* Apoio à Nuvem do Governo dos EUA do Azure

**Correções**
* Corrige um problema com a execução como utilizador não-raiz no modo de diarização

| Etiquetas de imagem                  | Notas                                    |
|-----------------------------|:-----------------------------------------|
| `2.6.0-amd64-<locale>`      | Substitua `<locale>` por um dos locais disponíveis, listado abaixo. Por exemplo, `2.6.0-amd64-en-us`. |
| `2.5.0-amd64-<locale>`      | Substitua `<locale>` por um dos locais disponíveis, listado abaixo. Por exemplo, `2.5.0-amd64-en-us`. |


Este recipiente tem os seguintes locais disponíveis.

| Local para v2.6.0           | Notas                                    |
|-----------------------------|:-----------------------------------------|
| `ar-ae`                     | Imagem de recipiente com o `ar-AE` local. |
| `ar-eg`                     | Imagem de recipiente com o `ar-EG` local. |
| `ar-kw`                     | Imagem de recipiente com o `ar-KW` local. |
| `ar-qa`                     | Imagem de recipiente com o `ar-QA` local. |
| `ar-sa`                     | Imagem de recipiente com o `ar-SA` local. |
| `ca-es`                     | Imagem de recipiente com o `ca-ES` local. |
| `cs-cz`                     | Imagem de recipiente com o `cs-CZ` local. |
| `da-dk`                     | Imagem de recipiente com o `da-DK` local. |
| `de-de`                     | Imagem de recipiente com o `de-DE` local. |
| `en-au`                     | Imagem de recipiente com o `en-AU` local. |
| `en-ca`                     | Imagem de recipiente com o `en-CA` local. |
| `en-gb`                     | Imagem de recipiente com o `en-GB` local. |
| `en-in`                     | Imagem de recipiente com o `en-IN` local. |
| `en-nz`                     | Imagem de recipiente com o `en-NZ` local. |
| `en-us`                     | Imagem de recipiente com o `en-US` local. |
| `es-es`                     | Imagem de recipiente com o `es-ES` local. |
| `es-mx`                     | Imagem de recipiente com o `es-MX` local. |
| `fi-fi`                     | Imagem de recipiente com o `fi-FI` local. |
| `fr-ca`                     | Imagem de recipiente com o `fr-CA` local. |
| `fr-fr`                     | Imagem de recipiente com o `fr-FR` local. |
| `gu-in`                     | Imagem de recipiente com o `gu-IN` local. |
| `hi-in`                     | Imagem de recipiente com o `hi-IN` local. |
| `it-it`                     | Imagem de recipiente com o `it-IT` local. |
| `ja-jp`                     | Imagem de recipiente com o `ja-JP` local. |
| `ko-kr`                     | Imagem de recipiente com o `ko-KR` local. |
| `mr-in`                     | Imagem de recipiente com o `mr-IN` local. |
| `nb-no`                     | Imagem de recipiente com o `nb-NO` local. |
| `nl-nl`                     | Imagem de recipiente com o `nl-NL` local. |
| `pl-pl`                     | Imagem de recipiente com o `pl-PL` local. |
| `pt-br`                     | Imagem de recipiente com o `pt-BR` local. |
| `pt-pt`                     | Imagem de recipiente com o `pt-PT` local. |
| `ru-ru`                     | Imagem de recipiente com o `ru-RU` local. |
| `sv-se`                     | Imagem de recipiente com o `sv-SE` local. |
| `ta-in`                     | Imagem de recipiente com o `ta-IN` local. |
| `te-in`                     | Imagem de recipiente com o `te-IN` local. |
| `th-th`                     | Imagem de recipiente com o `th-TH` local. |
| `tr-tr`                     | Imagem de recipiente com o `tr-TR` local. |
| `zh-cn`                     | Imagem de recipiente com o `zh-CN` local. |
| `zh-hk`                     | Imagem de recipiente com o `zh-HK` local. |
| `zh-tw`                     | Imagem de recipiente com o `zh-TW` local. |

| Local para v2.5.0           | Notas                                    |
|-----------------------------|:-----------------------------------------|
| `ar-ae`                     | Imagem de recipiente com o `ar-AE` local. |
| `ar-eg`                     | Imagem de recipiente com o `ar-EG` local. |
| `ar-kw`                     | Imagem de recipiente com o `ar-KW` local. |
| `ar-qa`                     | Imagem de recipiente com o `ar-QA` local. |
| `ar-sa`                     | Imagem de recipiente com o `ar-SA` local. |
| `ca-es`                     | Imagem de recipiente com o `ca-ES` local. |
| `da-dk`                     | Imagem de recipiente com o `da-DK` local. |
| `de-de`                     | Imagem de recipiente com o `de-DE` local. |
| `en-au`                     | Imagem de recipiente com o `en-AU` local. |
| `en-ca`                     | Imagem de recipiente com o `en-CA` local. |
| `en-gb`                     | Imagem de recipiente com o `en-GB` local. |
| `en-in`                     | Imagem de recipiente com o `en-IN` local. |
| `en-nz`                     | Imagem de recipiente com o `en-NZ` local. |
| `en-us`                     | Imagem de recipiente com o `en-US` local. |
| `es-es`                     | Imagem de recipiente com o `es-ES` local. |
| `es-mx`                     | Imagem de recipiente com o `es-MX` local. |
| `fi-fi`                     | Imagem de recipiente com o `fi-FI` local. |
| `fr-ca`                     | Imagem de recipiente com o `fr-CA` local. |
| `fr-fr`                     | Imagem de recipiente com o `fr-FR` local. |
| `gu-in`                     | Imagem de recipiente com o `gu-IN` local. |
| `hi-in`                     | Imagem de recipiente com o `hi-IN` local. |
| `it-it`                     | Imagem de recipiente com o `it-IT` local. |
| `ja-jp`                     | Imagem de recipiente com o `ja-JP` local. |
| `ko-kr`                     | Imagem de recipiente com o `ko-KR` local. |
| `mr-in`                     | Imagem de recipiente com o `mr-IN` local. |
| `nb-no`                     | Imagem de recipiente com o `nb-NO` local. |
| `nl-nl`                     | Imagem de recipiente com o `nl-NL` local. |
| `pl-pl`                     | Imagem de recipiente com o `pl-PL` local. |
| `pt-br`                     | Imagem de recipiente com o `pt-BR` local. |
| `pt-pt`                     | Imagem de recipiente com o `pt-PT` local. |
| `ru-ru`                     | Imagem de recipiente com o `ru-RU` local. |
| `sv-se`                     | Imagem de recipiente com o `sv-SE` local. |
| `ta-in`                     | Imagem de recipiente com o `ta-IN` local. |
| `te-in`                     | Imagem de recipiente com o `te-IN` local. |
| `th-th`                     | Imagem de recipiente com o `th-TH` local. |
| `tr-tr`                     | Imagem de recipiente com o `tr-TR` local. |
| `zh-cn`                     | Imagem de recipiente com o `zh-CN` local. |
| `zh-hk`                     | Imagem de recipiente com o `zh-HK` local. |
| `zh-tw`                     | Imagem de recipiente com o `zh-TW` local. |

---

## <a name="text-to-speech"></a>Conversão de texto em voz

A imagem do contentor [text-to-speech][sp-tts] pode ser encontrada no sindicato do registo do `mcr.microsoft.com` contentor. Reside dentro do `azure-cognitive-services/speechservices/` repositório e é `text-to-speech` nomeado. O nome de imagem do recipiente totalmente qualificado é, `mcr.microsoft.com/azure-cognitive-services/speechservices/text-to-speech` .

Esta imagem do recipiente tem as seguintes etiquetas disponíveis. Também pode encontrar uma lista completa de [tags no MCR](https://mcr.microsoft.com/v2/azure-cognitive-services/speechservices/text-to-speech/tags/list).


# <a name="latest-version"></a>[Versão mais recente](#tab/current)

Nota de lançamento `1.9.0-amd64-<locale-and-voice>` para:

* Lançamento mensal regular

| Etiquetas de imagem                                  | Notas                                                                                                         |
|---------------------------------------------|:--------------------------------------------------------------------------------------------------------------|
| `latest`                                    | Imagem de recipiente com o `en-US` local e `en-US-AriaRUS` a voz.                                            | 
| `1.9.0-amd64-<locale-and-voice>`            | Substitua `<locale>` por um dos locais disponíveis, listado abaixo. Por exemplo, `1.9.0-amd64-en-us-ariarus`.  |


| Locais para v1.9.0                          | Notas                                                                      | Digerir                         |
|---------------------------------------------|:---------------------------------------------------------------------------|:-------------------------------|
| `ar-eg-hoda`                                | Imagem de recipiente com o `ar-EG` local e `ar-EG-Hoda` a voz.            | `sha256:2b19cfd2212d6517b286aa18617d2f9d1dd1520078b559cbbf9240599270d10` | 
| `ar-sa-naayf`                               | Imagem de recipiente com o `ar-SA` local e `ar-SA-Naayf` a voz.           | `sha256:6063aae5fb15c62b234cf945220916516a06ca81354c5311dee02af4d8cb0d3` |
| `bg-bg-ivan`                                | Imagem de recipiente com o `bg-BG` local e `bg-BG-Ivan` a voz.            | `sha256:c6786916464755e64ffa64e69e8f3e7ef16115bac00bb6ea1e45368c42c58d1` |
| `ca-es-herenarus`                           | Imagem de recipiente com o `ca-ES` local e `ca-ES-HerenaRUS` a voz.       | `sha256:2a8a1accbf99e2746c9345b77e2f261e0111227312c402cc2e1cd8760cdc82a` |
| `cs-cz-jakub`                               | Imagem de recipiente com o `cs-CZ` local e `cs-CZ-Jakub` a voz.           | `sha256:3e464356bb08c9c966af2b28a88ccafd591aecd2e37a0fedb356bd443720e8d` |
| `da-dk-hellerus`                            | Imagem de recipiente com o `da-DK` local e `da-DK-HelleRUS` a voz.        | `sha256:b85c43080804103673ff99dddea644a516c4103e8b1f11fa3dd34857492cd40` |
| `de-at-michael`                             | Imagem de recipiente com o `de-AT` local e `de-AT-Michael` a voz.         | `sha256:87b57ee61f964e4d72e75d860c499fa3b3d8dbda6a96c97d696beb20aa8b2a9` |
| `de-ch-karsten`                             | Imagem de recipiente com o `de-CH` local e `de-CH-Karsten` a voz.         | `sha256:ab1385b9746f4f054204302b9d564a433ae03748021b8ed71b4a3a224af1e9b` |
| `de-de-heddarus`                            | Imagem de recipiente com o `de-DE` local e `de-DE-Hedda` a voz.           | `sha256:82185a710c87f9dde678d88036867559ab3bf5f08f234d60d1548d3e106db57` |
| `de-de-hedda`                               | Imagem de recipiente com o `de-DE` local e `de-DE-Hedda` a voz.           | `sha256:82185a710c87f9dde678d88036867559ab3bf5f08f234d60d1548d3e106db57` |
| `de-de-stefan-apollo`                       | Imagem de recipiente com o `de-DE` local e `de-DE-Stefan-Apollo` a voz.   | `sha256:56a1c63e7e6a0f5623ddc1f6a44ac6e51471d073e02e14e8c8b1e577930d816` |
| `el-gr-stefanos`                            | Imagem de recipiente com o `el-GR` local e `el-GR-Stefanos` a voz.        | `sha256:ccbbb09f29ff8f276e246037183c7a3e9a3eb5bf33a942b22205cce3c6857f2` |
| `en-au-catherine`                           | Imagem de recipiente com o `en-AU` local e `en-AU-Catherine` a voz.       | `sha256:0c7374890f963e1ae9507e89dc9965a94723bd57802826c0677cd5262189783` |
| `en-au-hayleyrus`                           | Imagem de recipiente com o `en-AU` local e `en-AU-HayleyRUS` a voz.       | `sha256:7430bf8eace8294ca085f36ea56399261b2b4f69027e86649e8f3868fc3d811` |
| `en-ca-heatherrus`                          | Imagem de recipiente com o `en-CA` local e `en-CA-HeatherRUS` a voz.      | `sha256:0166ce1de3d669ea4ad80738c63369b7032125a54ecabade07241d740a94cfe` |
| `en-ca-linda`                               | Imagem de recipiente com o `en-CA` local e `en-CA-Linda` a voz.           | `sha256:50bed6a7bde9b793d307bcc3ace4c0f28d4a33c7a4dad9b3a394dc39a3e1c28` |
| `en-gb-george-apollo`                       | Imagem de recipiente com o `en-GB` local e `en-GB-George-Apollo` a voz.   | `sha256:50b800c0018a39609ddb1cee1b10062bf38a907644c393d20786db7c3ade748` |
| `en-gb-hazelrus`                            | Imagem de recipiente com o `en-GB` local e `en-GB-HazelRUS` a voz.        | `sha256:2aa79394dfeac8cec0cc1704a5199949cfccf347fe61161d02c7000c4ffcfa6` |
| `en-gb-susan-apollo`                        | Imagem de recipiente com o `en-GB` local e `en-GB-Susan-Apollo` a voz.    | `sha256:7a3174b3aae5f10241e731d392b56f124808cdd506f881ced919ced73d836c0` |
| `en-ie-sean`                                | Imagem de recipiente com o `en-IE` local e `en-IE-Sean` a voz.            | `sha256:2457202fadb2354fc8d3666432096bd87c07760a4e3f4dbcc49853fff658577` |
| `en-in-heera-apollo`                        | Imagem de recipiente com o `en-IN` local e `en-IN-Heera-Apollo` a voz.    | `sha256:e4068cd7ca4272ea94819e2ba8743d2a76c8710b162db5e9ecbde6c92c12877` |
| `en-in-priyarus`                            | Imagem de recipiente com o `en-IN` local e `en-IN-PriyaRUS` a voz.        | `sha256:9d63a0ed53ac06178ab84588551421c0e1d04b8bad3321410ebb99c3ca2a9e8` |
| `en-in-ravi-apollo`                         | Imagem de recipiente com o `en-IN` local e `en-IN-Ravi-Apollo` a voz.     | `sha256:67049c9ce591336655943f5030afcfdaa150a8aace7b372425a69cc33a6b7b9` |
| `en-us-aria24krus`                          | Imagem de recipiente com o `en-US` local e `en-US-Aria24kRUS` a voz.      | `sha256:a95acf6874bf3df7ae8e96be779f80cb5405d21250227b0c4b3ddbcb3014082` |
| `en-us-ariarus`                             | Imagem de recipiente com o `en-US` local e `en-US-AriaRUS` a voz.         | `sha256:a95acf6874bf3df7ae8e96be779f80cb5405d21250227b0c4b3ddbcb3014082` |
| `en-us-benjaminrus`                         | Imagem de recipiente com o `en-US` local e `en-US-BenjaminRUS` a voz.     | `sha256:93cd49adaaa2a1bdfb06ab655be164ae66f206cb7c03a2cbd59e5fba70610ab` |
| `en-us-guy24krus`                           | Imagem de recipiente com o `en-US` local e `en-US-Guy24kRUS` a voz.       | `sha256:7b788bfcaae4c63c274ca15924bfd861cfcafd5fec13f685d80babc25b2949d` |
| `en-us-zirarus`                             | Imagem de recipiente com o `en-US` local e `en-US-ZiraRUS` a voz.         | `sha256:bfc87a77df5695ad43481348500fba8f6a7b495708fba200706049469b5ba97` |
| `es-es-helenarus`                           | Imagem de recipiente com o `es-ES` local e `es-ES-HelenaRUS` a voz.       | `sha256:0b6c17aca75efb64aa9bfc0d83303038fe58d4b2fb1fc94c9380a4335b80796` |
| `es-es-laura-apollo`                        | Imagem de recipiente com o `es-ES` local e `es-ES-Laura-Apollo` a voz.    | `sha256:d6fcffc944c37a2dd0de29c39b82f3f8cce3a95ad925d2814ed7538335d5d4f` |
| `es-es-pablo-apollo`                        | Imagem de recipiente com o `es-ES` local e `es-ES-Pablo-Apollo` a voz.    | `sha256:a460bc53d9083d3c3770129995cf96cc1069ae4e8101f1739d304fe210f0af0` |
| `es-mx-hildarus`                            | Imagem de recipiente com o `es-MX` local e `es-MX-HildaRUS` a voz.        | `sha256:5b7578fc5b00158dfa674d95a3f1d57f22eb285e8333b4006d1fe1808bda7ba` |
| `es-mx-raul-apollo`                         | Imagem de recipiente com o `es-MX` local e `es-MX-Raul-Apollo` a voz.     | `sha256:03922fb017783c86d788c72e01c7ede440f8f3c913c86cab19bad4dfc2e4a2b` |
| `fi-fi-heidirus`                            | Imagem de recipiente com o `fi-FI` local e `fi-FI-HeidiRUS` a voz.        | `sha256:146c1f98d6fa061016eba41db6e7b654eef222d37f35406d4b43477bb2ff897` |
| `fr-ca-caroline`                            | Imagem de recipiente com o `fr-CA` local e `fr-CA-Caroline` a voz.        | `sha256:1ee2e53f12ad1c72665d2aef64e9d4a7f9ea05670cad84dcae5e75409494f32` |
| `fr-ca-harmonierus`                         | Imagem de recipiente com o `fr-CA` local e `fr-CA-HarmonieRUS` a voz.     | `sha256:a21d25d3ac699af4e9ba9194aadd9b45f35fd9205224f3429a4c7da41fc38fe` |
| `fr-ch-guillaume`                           | Imagem de recipiente com o `fr-CH` local e `fr-CH-Guillaume` a voz.       | `sha256:216125a9bd89a95d3c4dc2d7e031398659427b3aa7d4663d23a65737972e42b` |
| `fr-fr-hortenserus`                         | Imagem de recipiente com o `fr-FR` local e `fr-FR-HortenseRUS` a voz.     | `sha256:795a698120eecbd80c48e738f73300739c1698ca859130ddb4236317bcdf70f` |
| `fr-fr-julie-apollo`                        | Imagem de recipiente com o `fr-FR` local e `fr-FR-Julie-Apollo` a voz.    | `sha256:f6eb70d523c435c2e3a713b32a8af4a781df7ec043caad2fc7f458ee341eb2f` |
| `fr-fr-paul-apollo`                         | Imagem de recipiente com o `fr-FR` local e `fr-FR-Paul-Apollo` a voz.     | `sha256:28864c662a20f459b3051b1da2967a605e06267e6408285f7c2552748cf4eed` |
| `he-il-asaf`                                | Imagem de recipiente com o `he-IL` local e `he-IL-Asaf` a voz.            | `sha256:eaa834bac6b69abef096b36a8baead741db78fe438af3d30f60abde3631d639` |
| `hi-in-hemant`                              | Imagem de recipiente com o `hi-IN` local e `hi-IN-Hemant` a voz.          | `sha256:cfea0fa7cce9cc512f2fbb8b76f1c00fe5c32fad853c90b15934cf4ee6262fa` |
| `hi-in-kalpana-apollo`                      | Imagem de recipiente com o `hi-IN` local e `hi-IN-Kalpana-Apollo` a voz.  | `sha256:afbd6cc0413f3a3c9f6df044b6df6d9dac9e8e888c2cb619fefbdc3e105c644` |
| `hi-in-kalpana`                             | Imagem de recipiente com o `hi-IN` local e `hi-IN-Kalpana` a voz.         | `sha256:afbd6cc0413f3a3c9f6df044b6df6d9dac9e8e888c2cb619fefbdc3e105c644` |
| `hr-hr-matej`                               | Imagem de recipiente com o `hr-HR` local e `hr-HR-Matej` a voz.           | `sha256:86683597c62752b4d769b69e5294979fafd4c277aaef1536e1cb19f9f06c0bf` |
| `hu-hu-szabolcs`                            | Imagem de recipiente com o `hu-HU` local e `hu-HU-Szabolcs` a voz.        | `sha256:aa64eed28ca2ad060e2e02188e0401bf34e4caf7e2182b70a30ce33b3c11c9c` |
| `id-id-andika`                              | Imagem de recipiente com o `id-ID` local e `id-ID-Andika` a voz.          | `sha256:0e1394d231a57a1df8163ccb634dc2ef2f8103b10608a40ab3efc5c0fbe9ded` |
| `it-it-cosimo-apollo`                       | Imagem de recipiente com o `it-IT` local e `it-IT-Cosimo-Apollo` a voz.   | `sha256:eef97f2817fc24405823a5fe4e825244db32279b44c0e6631e8ad9a5c1acf40` |
| `it-it-luciarus`                            | Imagem de recipiente com o `it-IT` local e `it-IT-LuciaRUS` a voz.        | `sha256:ebc331b0685f482d2f55619fa81fd451fd7c8f107f9cd7ad159bc6213ae4e33` |
| `ja-jp-ayumi-apollo`                        | Imagem de recipiente com o `ja-JP` local e `ja-JP-Ayumi-Apollo` a voz.    | `sha256:e9cb7dfd2eec154c8f3d530c16b66e8558c5955a2edaede69740067f00e43cf` |
| `ja-jp-harukarus`                           | Imagem de recipiente com o `ja-JP` local e `ja-JP-HarukaRUS` a voz.       | `sha256:93ce2ef6177c0d8ac70b61df8b11fcbcdfd3c0be0cc51cd8644f26679a741c2` |
| `ja-jp-ichiro-apollo`                       | Imagem de recipiente com o `ja-JP` local e `ja-JP-Ichiro-Apollo` a voz.   | `sha256:6a18bae69ac63b42ba992b8b74d8d31d91ca984d61b5f62f38be988cf38645e` |
| `ko-kr-heamirus`                            | Imagem de recipiente com o `ko-KR` local e `ko-KR-HeamiRUS` a voz.        | `sha256:7a48252d4ada2af43f9266a70113426d330bac192348cbdc929022295a0e727` |
| `ms-my-rizwan`                              | Imagem de recipiente com o `ms-MY` local e `ms-MY-Rizwan` a voz.          | `sha256:90e2ecac14f8e960934fd013d208fc2a0afe1bfff037d5648d422bda8d8a76e` |
| `nb-no-huldarus`                            | Imagem de recipiente com o `nb-NO` local e `nb-NO-HuldaRUS` a voz.        | `sha256:217b61bd6244b5effda8f12a2c563ce1b4572e9c5b8a08df143665f9ff754e4` |
| `nl-nl-hannarus`                            | Imagem de recipiente com o `nl-NL` local e `nl-NL-HannaRUS` a voz.        | `sha256:fbff48dfc9dfadadf377867b28f6e3a3bd605e59da20f77a531efcc7d85d16e` |
| `pl-pl-paulinarus`                          | Imagem de recipiente com o `pl-PL` local e `pl-PL-PaulinaRUS` a voz.      | `sha256:856a033a09925773fa4b4531e199ab7c03c537f366acecbda60f8d21735725e` |
| `pt-br-daniel-apollo`                       | Imagem de recipiente com o `pt-BR` local e `pt-BR-Daniel-Apollo` a voz.   | `sha256:2d1ec975f1aee56a6fc6039d154fb3f2fbeb4636f7078c5dfe99aeddb6a3634` |
| `pt-br-heloisarus`                          | Imagem de recipiente com o `pt-BR` local e `pt-BR-HeloisaRUS` a voz.      | `sha256:b7d629f37ab3305274764264dc08fab5236e60ef18d40e987618115db67ce44` |
| `pt-pt-heliarus`                            | Imagem de recipiente com o `pt-PT` local e `pt-PT-HeliaRUS` a voz.        | `sha256:8b380ae7e4aac9d4ada4d15fa9e667387bc9ca038796d9b6999953bfbc97259` |
| `ro-ro-andrei`                              | Imagem de recipiente com o `ro-RO` local e `ro-RO-Andrei` a voz.          | `sha256:b00ca7f1411169a5baf7263a8d7e5eed1a72084d9489eaf458429dfc338564a` |
| `ru-ru-ekaterinarus`                        | Imagem de recipiente com o `ru-RU` local e `ru-RU-EkaterinaRUS` a voz.    | `sha256:31c588c31e3ac67305af66091e7756dfc4ca454317d0228116ea0b2fedf5d71` |
| `ru-ru-irina-apollo`                        | Imagem de recipiente com o `ru-RU` local e `ru-RU-Irina-Apollo` a voz.    | `sha256:e76437f8da7c279b38d2643defc997a13b4a364e9a212895cdb33a9a3f6457f` |
| `ru-ru-pavel-apollo`                        | Imagem de recipiente com o `ru-RU` local e `ru-RU-Pavel-Apollo` a voz.    | `sha256:461c1efa6cce0b10a87f338bc637aca76aef8458061a688870fb3343d682da0` |
| `sk-sk-filip`                               | Imagem de recipiente com o `sk-SK` local e `sk-SK-Filip` a voz.           | `sha256:7fb0cfab4c0fe2913eb20f28a25c6663015d62f82e7e7864d9f7fac2d27697b` |
| `sl-si-lado`                                | Imagem de recipiente com o `sl-SI` local e `sl-SI-Lado` a voz.            | `sha256:5336173d410e10ffeb5dc211a583887e33754319c757914955057d398dfbb0a` |
| `sv-se-hedvigrus`                           | Imagem de recipiente com o `sv-SE` local e `sv-SE-HedvigRUS` a voz.       | `sha256:5dc8cdcc3054386bf69596707d9d261d4db5bfd09f1882ceb4e29238a34b24e` |
| `ta-in-valluvar`                            | Imagem de recipiente com o `ta-IN` local e `ta-IN-Valluvar` a voz.        | `sha256:74ea485f23e4c1fe0029e06894860aa0188c36c0e14ea3584a06d4216ccef56` |
| `te-in-chitra`                              | Imagem de recipiente com o `te-IN` local e `te-IN-Chitra` a voz.          | `sha256:ff2977a98ef691da543db08be9cfe04d7fc3bf8f78b29310c163e47303b2ddd` |
| `th-th-pattara`                             | Imagem de recipiente com o `th-TH` local e `th-TH-Pattara` a voz.         | `sha256:ba7e2c0e5e75d9f2b52aa50c97728616c43e81f48c15e24665e4c2ea5770a8f` |
| `tr-tr-sedarus`                             | Imagem de recipiente com o `tr-TR` local e `tr-TR-SedaRUS` a voz.         | `sha256:375a8ceae89ea1f0dda551feff30ae3679231189b527992edbc49988d042d66` |
| `vi-vn-an`                                  | Imagem de recipiente com o `vi-VN` local e `vi-VN-An` a voz.              | `sha256:b6f82148295b38b4039c45c48695ec50b4e97cd02b18d49c39bf9fca3bec958` |
| `zh-cn-huihuirus`                           | Imagem de recipiente com o `zh-CN` local e `zh-CN-HuihuiRUS` a voz.       | `sha256:3e773931f3adaac92cba43773a241692a2b471ebe73ec51c475df8ff63b7ee1` |
| `zh-cn-kangkang-apollo`                     | Imagem de recipiente com o `zh-CN` local e `zh-CN-Kangkang-Apollo` a voz. | `sha256:05fc0d5075a1094caf70d98b4a9469952be52cb6eb4d9f7b9ff4ae961100c7b` |
| `zh-cn-yaoyao-apollo`                       | Imagem de recipiente com o `zh-CN` local e `zh-CN-Yaoyao-Apollo` a voz.   | `sha256:d7613bcefc48e85b9d6f07c8cd223c16d4958bcf7f24087575250e97c593ac1` |
| `zh-hk-danny-apollo`                        | Imagem de recipiente com o `zh-HK` local e `zh-HK-Danny-Apollo` a voz.    | `sha256:efe22bc123dac9312dcaeb859a377d81f61fbb25ef46e4678d36ec6bebc5d32` |
| `zh-hk-tracy-apollo`                        | Imagem de recipiente com o `zh-HK` local e `zh-HK-Tracy-Apollo` a voz.    | `sha256:802c60bc65012c03ffe96268dca79b8c6dcd0c5cc6180ec271c50ef5c9ba132` |
| `zh-hk-tracyrus`                            | Imagem de recipiente com o `zh-HK` local e `zh-HK-TracyRUS` a voz.        | `sha256:802c60bc65012c03ffe96268dca79b8c6dcd0c5cc6180ec271c50ef5c9ba132` |
| `zh-tw-hanhanrus`                           | Imagem de recipiente com o `zh-TW` local e `zh-TW-HanHanRUS` a voz.       | `sha256:95d58922463d577d4c4722ab722a5768af35fb62236d47f6709717dea758909` |
| `zh-tw-yating-apollo`                       | Imagem de recipiente com o `zh-TW` local e `zh-TW-Yating-Apollo` a voz.   | `sha256:33eec6e3aaaedafaf3969746eeaf97a1760e763505decfe2abaa03f5054bfd2` |
| `zh-tw-zhiwei-apollo`                       | Imagem de recipiente com o `zh-TW` local e `zh-TW-Zhiwei-Apollo` a voz.   | `sha256:456db2898b2e5a9c30b7071ce6ea3f141438cbf1aa4899c7ffccfc2f0dde5bd` |


# <a name="previous-version"></a>[Versão anterior](#tab/previous)

Nota de lançamento `1.8.0-amd64-<locale-and-voice>` para:

**Funcionalidade**

* Totalmente migrado para .NET 3.1

Nota de lançamento `1.7.0-amd64-<locale-and-voice>` para:

**Funcionalidade**

* Componentes atualizados para .NET 3.1

| Etiquetas de imagem                                  | Notas                                                                                                         |
|---------------------------------------------|:--------------------------------------------------------------------------------------------------------------|
| `1.8.0-amd64-<locale-and-voice>`            | Substitua `<locale>` por um dos locais disponíveis, listado abaixo. Por exemplo, `1.8.0-amd64-en-us-ariarus`.  |
| `1.7.0-amd64-<locale-and-voice>`            | 1ª versão GA. Substitua `<locale>` por um dos locais disponíveis, listado abaixo. Por exemplo, `1.7.0-amd64-en-us-ariarus`.  |


| Locais para v1.8.0                          | Notas                                                                      |
|---------------------------------------------|:---------------------------------------------------------------------------|
| `ar-eg-hoda`                                | Imagem de recipiente com o `ar-EG` local e `ar-EG-Hoda` a voz.            |
| `ar-sa-naayf`                               | Imagem de recipiente com o `ar-SA` local e `ar-SA-Naayf` a voz.           |
| `bg-bg-ivan`                                | Imagem de recipiente com o `bg-BG` local e `bg-BG-Ivan` a voz.            |
| `ca-es-herenarus`                           | Imagem de recipiente com o `ca-ES` local e `ca-ES-HerenaRUS` a voz.       |
| `cs-cz-jakub`                               | Imagem de recipiente com o `cs-CZ` local e `cs-CZ-Jakub` a voz.           |
| `da-dk-hellerus`                            | Imagem de recipiente com o `da-DK` local e `da-DK-HelleRUS` a voz.        |
| `de-at-michael`                             | Imagem de recipiente com o `de-AT` local e `de-AT-Michael` a voz.         |
| `de-ch-karsten`                             | Imagem de recipiente com o `de-CH` local e `de-CH-Karsten` a voz.         |
| `de-de-hedda`                               | Imagem de recipiente com o `de-DE` local e `de-DE-Hedda` a voz.           |
| `de-de-heddarus`                            | Imagem de recipiente com o `de-DE` local e `de-DE-Hedda` a voz.           |
| `de-de-stefan-apollo`                       | Imagem de recipiente com o `de-DE` local e `de-DE-Stefan-Apollo` a voz.   |
| `el-gr-stefanos`                            | Imagem de recipiente com o `el-GR` local e `el-GR-Stefanos` a voz.        |
| `en-au-catherine`                           | Imagem de recipiente com o `en-AU` local e `en-AU-Catherine` a voz.       |
| `en-au-hayleyrus`                           | Imagem de recipiente com o `en-AU` local e `en-AU-HayleyRUS` a voz.       |
| `en-ca-heatherrus`                          | Imagem de recipiente com o `en-CA` local e `en-CA-HeatherRUS` a voz.      |
| `en-ca-linda`                               | Imagem de recipiente com o `en-CA` local e `en-CA-Linda` a voz.           |
| `en-gb-george-apollo`                       | Imagem de recipiente com o `en-GB` local e `en-GB-George-Apollo` a voz.   |
| `en-gb-hazelrus`                            | Imagem de recipiente com o `en-GB` local e `en-GB-HazelRUS` a voz.        |
| `en-gb-susan-apollo`                        | Imagem de recipiente com o `en-GB` local e `en-GB-Susan-Apollo` a voz.    |
| `en-ie-sean`                                | Imagem de recipiente com o `en-IE` local e `en-IE-Sean` a voz.            |
| `en-in-heera-apollo`                        | Imagem de recipiente com o `en-IN` local e `en-IN-Heera-Apollo` a voz.    |
| `en-in-priyarus`                            | Imagem de recipiente com o `en-IN` local e `en-IN-PriyaRUS` a voz.        |
| `en-in-ravi-apollo`                         | Imagem de recipiente com o `en-IN` local e `en-IN-Ravi-Apollo` a voz.     |
| `en-us-benjaminrus`                         | Imagem de recipiente com o `en-US` local e `en-US-BenjaminRUS` a voz.     |
| `en-us-guy24krus`                           | Imagem de recipiente com o `en-US` local e `en-US-Guy24kRUS` a voz.       |
| `en-us-aria24krus`                          | Imagem de recipiente com o `en-US` local e `en-US-Aria24kRUS` a voz.      |
| `en-us-ariarus`                             | Imagem de recipiente com o `en-US` local e `en-US-AriaRUS` a voz.         |
| `en-us-zirarus`                             | Imagem de recipiente com o `en-US` local e `en-US-ZiraRUS` a voz.         |
| `es-es-helenarus`                           | Imagem de recipiente com o `es-ES` local e `es-ES-HelenaRUS` a voz.       |
| `es-es-laura-apollo`                        | Imagem de recipiente com o `es-ES` local e `es-ES-Laura-Apollo` a voz.    |
| `es-es-pablo-apollo`                        | Imagem de recipiente com o `es-ES` local e `es-ES-Pablo-Apollo` a voz.    |
| `es-mx-hildarus`                            | Imagem de recipiente com o `es-MX` local e `es-MX-HildaRUS` a voz.        |
| `es-mx-raul-apollo`                         | Imagem de recipiente com o `es-MX` local e `es-MX-Raul-Apollo` a voz.     |
| `fi-fi-heidirus`                            | Imagem de recipiente com o `fi-FI` local e `fi-FI-HeidiRUS` a voz.        |
| `fr-ca-caroline`                            | Imagem de recipiente com o `fr-CA` local e `fr-CA-Caroline` a voz.        |
| `fr-ca-harmonierus`                         | Imagem de recipiente com o `fr-CA` local e `fr-CA-HarmonieRUS` a voz.     |
| `fr-ch-guillaume`                           | Imagem de recipiente com o `fr-CH` local e `fr-CH-Guillaume` a voz.       |
| `fr-fr-hortenserus`                         | Imagem de recipiente com o `fr-FR` local e `fr-FR-HortenseRUS` a voz.     |
| `fr-fr-julie-apollo`                        | Imagem de recipiente com o `fr-FR` local e `fr-FR-Julie-Apollo` a voz.    |
| `fr-fr-paul-apollo`                         | Imagem de recipiente com o `fr-FR` local e `fr-FR-Paul-Apollo` a voz.     |
| `he-il-asaf`                                | Imagem de recipiente com o `he-IL` local e `he-IL-Asaf` a voz.            |
| `hi-in-hemant`                              | Imagem de recipiente com o `hi-IN` local e `hi-IN-Hemant` a voz.          |
| `hi-in-kalpana-apollo`                      | Imagem de recipiente com o `hi-IN` local e `hi-IN-Kalpana-Apollo` a voz.  |
| `hi-in-kalpana`                             | Imagem de recipiente com o `hi-IN` local e `hi-IN-Kalpana` a voz.         |
| `hr-hr-matej`                               | Imagem de recipiente com o `hr-HR` local e `hr-HR-Matej` a voz.           |
| `hu-hu-szabolcs`                            | Imagem de recipiente com o `hu-HU` local e `hu-HU-Szabolcs` a voz.        |
| `id-id-andika`                              | Imagem de recipiente com o `id-ID` local e `id-ID-Andika` a voz.          |
| `it-it-cosimo-apollo`                       | Imagem de recipiente com o `it-IT` local e `it-IT-Cosimo-Apollo` a voz.   |
| `it-it-luciarus`                            | Imagem de recipiente com o `it-IT` local e `it-IT-LuciaRUS` a voz.        |
| `ja-jp-ayumi-apollo`                        | Imagem de recipiente com o `ja-JP` local e `ja-JP-Ayumi-Apollo` a voz.    |
| `ja-jp-harukarus`                           | Imagem de recipiente com o `ja-JP` local e `ja-JP-HarukaRUS` a voz.       |
| `ja-jp-ichiro-apollo`                       | Imagem de recipiente com o `ja-JP` local e `ja-JP-Ichiro-Apollo` a voz.   |
| `ko-kr-heamirus`                            | Imagem de recipiente com o `ko-KR` local e `ko-KR-HeamiRUS` a voz.        |
| `ms-my-rizwan`                              | Imagem de recipiente com o `ms-MY` local e `ms-MY-Rizwan` a voz.          |
| `nb-no-huldarus`                            | Imagem de recipiente com o `nb-NO` local e `nb-NO-HuldaRUS` a voz.        |
| `nl-nl-hannarus`                            | Imagem de recipiente com o `nl-NL` local e `nl-NL-HannaRUS` a voz.        |
| `pl-pl-paulinarus`                          | Imagem de recipiente com o `pl-PL` local e `pl-PL-PaulinaRUS` a voz.      |
| `pt-br-daniel-apollo`                       | Imagem de recipiente com o `pt-BR` local e `pt-BR-Daniel-Apollo` a voz.   |
| `pt-br-heloisarus`                          | Imagem de recipiente com o `pt-BR` local e `pt-BR-HeloisaRUS` a voz.      |
| `pt-pt-heliarus`                            | Imagem de recipiente com o `pt-PT` local e `pt-PT-HeliaRUS` a voz.        |
| `ro-ro-andrei`                              | Imagem de recipiente com o `ro-RO` local e `ro-RO-Andrei` a voz.          |
| `ru-ru-ekaterinarus`                        | Imagem de recipiente com o `ru-RU` local e `ru-RU-EkaterinaRUS` a voz.    |
| `ru-ru-irina-apollo`                        | Imagem de recipiente com o `ru-RU` local e `ru-RU-Irina-Apollo` a voz.    |
| `ru-ru-pavel-apollo`                        | Imagem de recipiente com o `ru-RU` local e `ru-RU-Pavel-Apollo` a voz.    |
| `sk-sk-filip`                               | Imagem de recipiente com o `sk-SK` local e `sk-SK-Filip` a voz.           |
| `sl-si-lado`                                | Imagem de recipiente com o `sl-SI` local e `sl-SI-Lado` a voz.            |
| `sv-se-hedvigrus`                           | Imagem de recipiente com o `sv-SE` local e `sv-SE-HedvigRUS` a voz.       |
| `ta-in-valluvar`                            | Imagem de recipiente com o `ta-IN` local e `ta-IN-Valluvar` a voz.        |
| `te-in-chitra`                              | Imagem de recipiente com o `te-IN` local e `te-IN-Chitra` a voz.          |
| `th-th-pattara`                             | Imagem de recipiente com o `th-TH` local e `th-TH-Pattara` a voz.         |
| `tr-tr-sedarus`                             | Imagem de recipiente com o `tr-TR` local e `tr-TR-SedaRUS` a voz.         |
| `vi-vn-an`                                  | Imagem de recipiente com o `vi-VN` local e `vi-VN-An` a voz.              |
| `zh-cn-huihuirus`                           | Imagem de recipiente com o `zh-CN` local e `zh-CN-HuihuiRUS` a voz.       |
| `zh-cn-kangkang-apollo`                     | Imagem de recipiente com o `zh-CN` local e `zh-CN-Kangkang-Apollo` a voz. |
| `zh-cn-yaoyao-apollo`                       | Imagem de recipiente com o `zh-CN` local e `zh-CN-Yaoyao-Apollo` a voz.   |
| `zh-hk-danny-apollo`                        | Imagem de recipiente com o `zh-HK` local e `zh-HK-Danny-Apollo` a voz.    |
| `zh-hk-tracy-apollo`                        | Imagem de recipiente com o `zh-HK` local e `zh-HK-Tracy-Apollo` a voz.    |
| `zh-hk-tracyrus`                            | Imagem de recipiente com o `zh-HK` local e `zh-HK-TracyRUS` a voz.        |
| `zh-tw-hanhanrus`                           | Imagem de recipiente com o `zh-TW` local e `zh-TW-HanHanRUS` a voz.       |
| `zh-tw-yating-apollo`                       | Imagem de recipiente com o `zh-TW` local e `zh-TW-Yating-Apollo` a voz.   |
| `zh-tw-zhiwei-apollo`                       | Imagem de recipiente com o `zh-TW` local e `zh-TW-Zhiwei-Apollo` a voz.   |

| Locais para v1.7.0                          | Notas                                                                      |
|---------------------------------------------|:---------------------------------------------------------------------------|
| `ar-eg-hoda`                                | Imagem de recipiente com o `ar-EG` local e `ar-EG-Hoda` a voz.            |
| `ar-sa-naayf`                               | Imagem de recipiente com o `ar-SA` local e `ar-SA-Naayf` a voz.           |
| `bg-bg-ivan`                                | Imagem de recipiente com o `bg-BG` local e `bg-BG-Ivan` a voz.            |
| `ca-es-herenarus`                           | Imagem de recipiente com o `ca-ES` local e `ca-ES-HerenaRUS` a voz.       |
| `cs-cz-jakub`                               | Imagem de recipiente com o `cs-CZ` local e `cs-CZ-Jakub` a voz.           |
| `da-dk-hellerus`                            | Imagem de recipiente com o `da-DK` local e `da-DK-HelleRUS` a voz.        |
| `de-at-michael`                             | Imagem de recipiente com o `de-AT` local e `de-AT-Michael` a voz.         |
| `de-ch-karsten`                             | Imagem de recipiente com o `de-CH` local e `de-CH-Karsten` a voz.         |
| `de-de-hedda`                               | Imagem de recipiente com o `de-DE` local e `de-DE-Hedda` a voz.           |
| `de-de-heddarus`                            | Imagem de recipiente com o `de-DE` local e `de-DE-Hedda` a voz.           |
| `de-de-stefan-apollo`                       | Imagem de recipiente com o `de-DE` local e `de-DE-Stefan-Apollo` a voz.   |
| `el-gr-stefanos`                            | Imagem de recipiente com o `el-GR` local e `el-GR-Stefanos` a voz.        |
| `en-au-catherine`                           | Imagem de recipiente com o `en-AU` local e `en-AU-Catherine` a voz.       |
| `en-au-hayleyrus`                           | Imagem de recipiente com o `en-AU` local e `en-AU-HayleyRUS` a voz.       |
| `en-ca-heatherrus`                          | Imagem de recipiente com o `en-CA` local e `en-CA-HeatherRUS` a voz.      |
| `en-ca-linda`                               | Imagem de recipiente com o `en-CA` local e `en-CA-Linda` a voz.           |
| `en-gb-george-apollo`                       | Imagem de recipiente com o `en-GB` local e `en-GB-George-Apollo` a voz.   |
| `en-gb-hazelrus`                            | Imagem de recipiente com o `en-GB` local e `en-GB-HazelRUS` a voz.        |
| `en-gb-susan-apollo`                        | Imagem de recipiente com o `en-GB` local e `en-GB-Susan-Apollo` a voz.    |
| `en-ie-sean`                                | Imagem de recipiente com o `en-IE` local e `en-IE-Sean` a voz.            |
| `en-in-heera-apollo`                        | Imagem de recipiente com o `en-IN` local e `en-IN-Heera-Apollo` a voz.    |
| `en-in-priyarus`                            | Imagem de recipiente com o `en-IN` local e `en-IN-PriyaRUS` a voz.        |
| `en-in-ravi-apollo`                         | Imagem de recipiente com o `en-IN` local e `en-IN-Ravi-Apollo` a voz.     |
| `en-us-benjaminrus`                         | Imagem de recipiente com o `en-US` local e `en-US-BenjaminRUS` a voz.     |
| `en-us-guy24krus`                           | Imagem de recipiente com o `en-US` local e `en-US-Guy24kRUS` a voz.       |
| `en-us-aria24krus`                          | Imagem de recipiente com o `en-US` local e `en-US-Aria24kRUS` a voz.      |
| `en-us-ariarus`                             | Imagem de recipiente com o `en-US` local e `en-US-AriaRUS` a voz.         |
| `en-us-zirarus`                             | Imagem de recipiente com o `en-US` local e `en-US-ZiraRUS` a voz.         |
| `es-es-helenarus`                           | Imagem de recipiente com o `es-ES` local e `es-ES-HelenaRUS` a voz.       |
| `es-es-laura-apollo`                        | Imagem de recipiente com o `es-ES` local e `es-ES-Laura-Apollo` a voz.    |
| `es-es-pablo-apollo`                        | Imagem de recipiente com o `es-ES` local e `es-ES-Pablo-Apollo` a voz.    |
| `es-mx-hildarus`                            | Imagem de recipiente com o `es-MX` local e `es-MX-HildaRUS` a voz.        |
| `es-mx-raul-apollo`                         | Imagem de recipiente com o `es-MX` local e `es-MX-Raul-Apollo` a voz.     |
| `fi-fi-heidirus`                            | Imagem de recipiente com o `fi-FI` local e `fi-FI-HeidiRUS` a voz.        |
| `fr-ca-caroline`                            | Imagem de recipiente com o `fr-CA` local e `fr-CA-Caroline` a voz.        |
| `fr-ca-harmonierus`                         | Imagem de recipiente com o `fr-CA` local e `fr-CA-HarmonieRUS` a voz.     |
| `fr-ch-guillaume`                           | Imagem de recipiente com o `fr-CH` local e `fr-CH-Guillaume` a voz.       |
| `fr-fr-hortenserus`                         | Imagem de recipiente com o `fr-FR` local e `fr-FR-HortenseRUS` a voz.     |
| `fr-fr-julie-apollo`                        | Imagem de recipiente com o `fr-FR` local e `fr-FR-Julie-Apollo` a voz.    |
| `fr-fr-paul-apollo`                         | Imagem de recipiente com o `fr-FR` local e `fr-FR-Paul-Apollo` a voz.     |
| `he-il-asaf`                                | Imagem de recipiente com o `he-IL` local e `he-IL-Asaf` a voz.            |
| `hi-in-hemant`                              | Imagem de recipiente com o `hi-IN` local e `hi-IN-Hemant` a voz.          |
| `hi-in-kalpana-apollo`                      | Imagem de recipiente com o `hi-IN` local e `hi-IN-Kalpana-Apollo` a voz.  |
| `hi-in-kalpana`                             | Imagem de recipiente com o `hi-IN` local e `hi-IN-Kalpana` a voz.         |
| `hr-hr-matej`                               | Imagem de recipiente com o `hr-HR` local e `hr-HR-Matej` a voz.           |
| `hu-hu-szabolcs`                            | Imagem de recipiente com o `hu-HU` local e `hu-HU-Szabolcs` a voz.        |
| `id-id-andika`                              | Imagem de recipiente com o `id-ID` local e `id-ID-Andika` a voz.          |
| `it-it-cosimo-apollo`                       | Imagem de recipiente com o `it-IT` local e `it-IT-Cosimo-Apollo` a voz.   |
| `it-it-luciarus`                            | Imagem de recipiente com o `it-IT` local e `it-IT-LuciaRUS` a voz.        |
| `ja-jp-ayumi-apollo`                        | Imagem de recipiente com o `ja-JP` local e `ja-JP-Ayumi-Apollo` a voz.    |
| `ja-jp-harukarus`                           | Imagem de recipiente com o `ja-JP` local e `ja-JP-HarukaRUS` a voz.       |
| `ja-jp-ichiro-apollo`                       | Imagem de recipiente com o `ja-JP` local e `ja-JP-Ichiro-Apollo` a voz.   |
| `ko-kr-heamirus`                            | Imagem de recipiente com o `ko-KR` local e `ko-KR-HeamiRUS` a voz.        |
| `ms-my-rizwan`                              | Imagem de recipiente com o `ms-MY` local e `ms-MY-Rizwan` a voz.          |
| `nb-no-huldarus`                            | Imagem de recipiente com o `nb-NO` local e `nb-NO-HuldaRUS` a voz.        |
| `nl-nl-hannarus`                            | Imagem de recipiente com o `nl-NL` local e `nl-NL-HannaRUS` a voz.        |
| `pl-pl-paulinarus`                          | Imagem de recipiente com o `pl-PL` local e `pl-PL-PaulinaRUS` a voz.      |
| `pt-br-daniel-apollo`                       | Imagem de recipiente com o `pt-BR` local e `pt-BR-Daniel-Apollo` a voz.   |
| `pt-br-heloisarus`                          | Imagem de recipiente com o `pt-BR` local e `pt-BR-HeloisaRUS` a voz.      |
| `pt-pt-heliarus`                            | Imagem de recipiente com o `pt-PT` local e `pt-PT-HeliaRUS` a voz.        |
| `ro-ro-andrei`                              | Imagem de recipiente com o `ro-RO` local e `ro-RO-Andrei` a voz.          |
| `ru-ru-ekaterinarus`                        | Imagem de recipiente com o `ru-RU` local e `ru-RU-EkaterinaRUS` a voz.    |
| `ru-ru-irina-apollo`                        | Imagem de recipiente com o `ru-RU` local e `ru-RU-Irina-Apollo` a voz.    |
| `ru-ru-pavel-apollo`                        | Imagem de recipiente com o `ru-RU` local e `ru-RU-Pavel-Apollo` a voz.    |
| `sk-sk-filip`                               | Imagem de recipiente com o `sk-SK` local e `sk-SK-Filip` a voz.           |
| `sl-si-lado`                                | Imagem de recipiente com o `sl-SI` local e `sl-SI-Lado` a voz.            |
| `sv-se-hedvigrus`                           | Imagem de recipiente com o `sv-SE` local e `sv-SE-HedvigRUS` a voz.       |
| `ta-in-valluvar`                            | Imagem de recipiente com o `ta-IN` local e `ta-IN-Valluvar` a voz.        |
| `te-in-chitra`                              | Imagem de recipiente com o `te-IN` local e `te-IN-Chitra` a voz.          |
| `th-th-pattara`                             | Imagem de recipiente com o `th-TH` local e `th-TH-Pattara` a voz.         |
| `tr-tr-sedarus`                             | Imagem de recipiente com o `tr-TR` local e `tr-TR-SedaRUS` a voz.         |
| `vi-vn-an`                                  | Imagem de recipiente com o `vi-VN` local e `vi-VN-An` a voz.              |
| `zh-cn-huihuirus`                           | Imagem de recipiente com o `zh-CN` local e `zh-CN-HuihuiRUS` a voz.       |
| `zh-cn-kangkang-apollo`                     | Imagem de recipiente com o `zh-CN` local e `zh-CN-Kangkang-Apollo` a voz. |
| `zh-cn-yaoyao-apollo`                       | Imagem de recipiente com o `zh-CN` local e `zh-CN-Yaoyao-Apollo` a voz.   |
| `zh-hk-danny-apollo`                        | Imagem de recipiente com o `zh-HK` local e `zh-HK-Danny-Apollo` a voz.    |
| `zh-hk-tracy-apollo`                        | Imagem de recipiente com o `zh-HK` local e `zh-HK-Tracy-Apollo` a voz.    |
| `zh-hk-tracyrus`                            | Imagem de recipiente com o `zh-HK` local e `zh-HK-TracyRUS` a voz.        |
| `zh-tw-hanhanrus`                           | Imagem de recipiente com o `zh-TW` local e `zh-TW-HanHanRUS` a voz.       |
| `zh-tw-yating-apollo`                       | Imagem de recipiente com o `zh-TW` local e `zh-TW-Yating-Apollo` a voz.   |
| `zh-tw-zhiwei-apollo`                       | Imagem de recipiente com o `zh-TW` local e `zh-TW-Zhiwei-Apollo` a voz.   |

---

## <a name="neural-text-to-speech"></a>Texto-a-discurso neural

A imagem do recipiente [neural texto-a-fala][sp-ntts] pode ser encontrada no sindicato do registo do `mcr.microsoft.com` contentor. Reside dentro do `azure-cognitive-services/speechservices/` repositório e é `neural-text-to-speech` nomeado. O nome de imagem do recipiente totalmente qualificado é, `mcr.microsoft.com/azure-cognitive-services/speechservices/neural-text-to-speech` .

Esta imagem do recipiente tem as seguintes etiquetas disponíveis. Também pode encontrar uma lista completa de [tags no MCR](https://mcr.microsoft.com/v2/azure-cognitive-services/speechservices/neural-text-to-speech/tags/list).


# <a name="latest-version"></a>[Versão mais recente](#tab/current)

Notas de lançamento `v1.3.0` para:
* O recipiente Neural Texto-a-fala está geralmente disponível. 

| Etiquetas de imagem                                  | Notas                                                                      |
|---------------------------------------------|:---------------------------------------------------------------------------|
| `latest`                                    | Imagem de recipiente com o `en-US` local e `en-US-AriaNeural` a voz.      |
| `1.3.0-amd64-<locale-and-voice>`    | Substitua `<locale>` por um dos locais disponíveis, listado abaixo. Por exemplo, `1.3.0-amd64-en-us-arianeural`. |


| v1.3.0 Locais e vozes           | Notas                                                                      |
|---------------------------------------------|:---------------------------------------------------------------------------|
| `de-de-katjaneural`                 | Imagem de recipiente com o `de-DE` local e `de-DE-KatjaNeural` a voz.     |
| `en-au-natashaneural`               | Imagem de recipiente com o `en-AU` local e `en-AU-NatashaNeural` a voz.   |
| `en-ca-claraneural`                 | Imagem de recipiente com o `en-CA` local e `en-CA-ClaraNeural` a voz.     |
| `en-gb-libbyneural`                 | Imagem de recipiente com o `en-GB` local e `en-GB-LibbyNeural` a voz.     |
| `en-gb-mianeural`                   | Imagem de recipiente com o `en-GB` local e `en-GB-MiaNeural` a voz.       |
| `en-us-arianeural`                  | Imagem de recipiente com o `en-US` local e `en-US-AriaNeural` a voz.      |
| `en-us-guyneural`                   | Imagem de recipiente com o `en-US` local e `en-US-GuyNeural` a voz.       |
| `es-es-elviraneural`                | Imagem de recipiente com o `es-ES` local e `es-ES-ElviraNeural` a voz.    |
| `es-mx-dalianeural`                 | Imagem de recipiente com o `es-MX` local e `es-MX-DaliaNeural` a voz.     |
| `fr-ca-sylvieneural`                | Imagem de recipiente com o `fr-CA` local e `fr-CA-SylvieNeural` a voz.    |
| `fr-fr-deniseneural`                | Imagem de recipiente com o `fr-FR` local e `fr-FR-DeniseNeural` a voz.    |
| `it-it-elsaneural`                  | Imagem de recipiente com o `it-IT` local e `it-IT-ElsaNeural` a voz.      |
| `ja-jp-nanamineural`                | Imagem de recipiente com o `ja-JP` local e `ja-JP-NanamiNeural` a voz.    |
| `ko-kr-sunhineural`                 | Imagem de recipiente com o `ko-KR` local e `ko-KR-SunHiNeural` a voz.     |
| `pt-br-franciscaneural`             | Imagem de recipiente com o `pt-BR` local e `pt-BR-FranciscaNeural` a voz. |
| `zh-cn-xiaoxiaoneural`              | Imagem de recipiente com o `zh-CN` local e `zh-CN-XiaoxiaoNeural` a voz.  |

# <a name="previous-version"></a>[Versão anterior](#tab/previous)

| Etiquetas de imagem                                  | Notas                                                                      |
|---------------------------------------------|:---------------------------------------------------------------------------|
| `latest`                                    | Imagem de recipiente com o `en-US` local e `en-US-AriaNeural` a voz.      |
| `1.2.0-amd64-<locale-and-voice>-preview`    | Substitua `<locale>` por um dos locais disponíveis, listado abaixo. Por exemplo, `1.2.0-amd64-en-us-arianeural-preview`. |


| v1.2.0 Visualização Locais e vozes           | Notas                                                                      |
|---------------------------------------------|:---------------------------------------------------------------------------|
| `latest`                                    | Imagem de recipiente com o `en-US` local e `en-US-AriaNeural` a voz.      |
| `de-de-katjaneural-preview`                 | Imagem de recipiente com o `de-DE` local e `de-DE-KatjaNeural` a voz.     |
| `en-au-natashaneural-preview`               | Imagem de recipiente com o `en-AU` local e `en-AU-NatashaNeural` a voz.   |
| `en-ca-claraneural-preview`                 | Imagem de recipiente com o `en-CA` local e `en-CA-ClaraNeural` a voz.     |
| `en-gb-libbyneural-preview`                 | Imagem de recipiente com o `en-GB` local e `en-GB-LibbyNeural` a voz.     |
| `en-gb-mianeural-preview`                   | Imagem de recipiente com o `en-GB` local e `en-GB-MiaNeural` a voz.       |
| `en-us-arianeural-preview`                  | Imagem de recipiente com o `en-US` local e `en-US-AriaNeural` a voz.      |
| `en-us-guyneural-preview`                   | Imagem de recipiente com o `en-US` local e `en-US-GuyNeural` a voz.       |
| `es-es-elviraneural-preview`                | Imagem de recipiente com o `es-ES` local e `es-ES-ElviraNeural` a voz.    |
| `es-mx-dalianeural-preview`                 | Imagem de recipiente com o `es-MX` local e `es-MX-DaliaNeural` a voz.     |
| `fr-ca-sylvieneural-preview`                | Imagem de recipiente com o `fr-CA` local e `fr-CA-SylvieNeural` a voz.    |
| `fr-fr-deniseneural-preview`                | Imagem de recipiente com o `fr-FR` local e `fr-FR-DeniseNeural` a voz.    |
| `it-it-elsaneural-preview`                  | Imagem de recipiente com o `it-IT` local e `it-IT-ElsaNeural` a voz.      |
| `ja-jp-nanamineural-preview`                | Imagem de recipiente com o `ja-JP` local e `ja-JP-NanamiNeural` a voz.    |
| `ko-kr-sunhineural-preview`                 | Imagem de recipiente com o `ko-KR` local e `ko-KR-SunHiNeural` a voz.     |
| `pt-br-franciscaneural-preview`             | Imagem de recipiente com o `pt-BR` local e `pt-BR-FranciscaNeural` a voz. |
| `zh-cn-xiaoxiaoneural-preview`              | Imagem de recipiente com o `zh-CN` local e `zh-CN-XiaoxiaoNeural` a voz.  |

---

## <a name="speech-language-detection"></a>Deteção de idioma de voz

A imagem do recipiente [de deteção da linguagem][sp-lid] da fala pode ser encontrada no sindicato do registo de `mcr.microsoft.com` contentores. Reside dentro do `azure-cognitive-services/speechservices/` repositório e é `language-detection` nomeado. O nome de imagem do recipiente totalmente qualificado é, `mcr.microsoft.com/azure-cognitive-services/speechservices/language-detection` .

Esta imagem do recipiente tem as seguintes etiquetas disponíveis. Também pode encontrar uma lista completa de [tags no MCR](https://mcr.microsoft.com/v2/azure-cognitive-services/speechservices/language-detection/tags/list).

| Etiquetas de imagem                                  | Notas                                                                      |
|---------------------------------------------|:---------------------------------------------------------------------------|
| `latest`                       |      |
| `1.1.0-amd64-preview`                       |      |

## <a name="key-phrase-extraction"></a>Extração de Expressões-Chave

a imagem do contentor pode ser encontrada no sindicato do registo do `mcr.microsoft.com` contentor. Reside dentro do `azure-cognitive-services/textanalytics/` repositório e é `keyphrase` nomeado. O nome de imagem do recipiente totalmente qualificado é, `mcr.microsoft.com/azure-cognitive-services/textanalytics/keyphrase` .

Esta imagem do recipiente tem as seguintes etiquetas disponíveis. Também pode encontrar uma lista completa de [tags no MCR](https://mcr.microsoft.com/v2/azure-cognitive-services/textanalytics/keyphrase/tags/list).

# <a name="latest-version"></a>[Versão mais recente](#tab/current)


| Etiquetas de imagem                    | Notas |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.013570001-amd64` |       |

# <a name="previous-versions"></a>[Versões anteriores](#tab/previous)

| Etiquetas de imagem                    | Notas |
|-------------------------------|:------|
| `1.1.012840001-amd64` |       |
| `1.1.012830001-amd64`    |       |

---

## <a name="text-language-detection"></a>Deteção de idioma do texto

A imagem do contentor [de deteção][ta-la] de linguagem pode ser encontrada no sindicato do `mcr.microsoft.com` registo do contentor. Reside dentro do `azure-cognitive-services/textanalytics/` repositório e é `language` nomeado. O nome de imagem do recipiente totalmente qualificado é, `mcr.microsoft.com/azure-cognitive-services/textanalytics/language`


Esta imagem do recipiente tem as seguintes etiquetas disponíveis. Também pode encontrar uma lista completa de [tags no MCR](https://mcr.microsoft.com/v2/azure-cognitive-services/textanalytics/language/tags/list).

# <a name="latest-versions"></a>[Versões mais recentes](#tab/current)

| Etiquetas de imagem                    | Notas |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.013570001-amd64` | |
   

# <a name="previous-versions"></a>[Versões anteriores](#tab/previous)


| Etiquetas de imagem                    | Notas |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.012840001-amd64` |   |
| `1.1.012830001-amd64` |   |

---

## <a name="sentiment-analysis"></a>Análise de sentimentos

A imagem do contentor [de Análise de Sentimento][ta-se] pode ser encontrada no sindicato do registo do `mcr.microsoft.com` contentor. Reside dentro do `azure-cognitive-services/textanalytics/` repositório e é `sentiment` nomeado. O nome de imagem do recipiente totalmente qualificado é, `mcr.microsoft.com/azure-cognitive-services/textanalytics/sentiment`

Esta imagem do recipiente tem as seguintes etiquetas disponíveis. Também pode encontrar uma lista completa de [tags no MCR](https://mcr.microsoft.com/v2/azure-cognitive-services/textanalytics/sentiment/tags/list).

| Etiquetas de imagem | Notas                                         |
|------------|:----------------------------------------------|
| `latest`   |                                               |
| `3.0-en`   | Análise de Sentimento v3 (Inglês)               |
| `3.0-es`   | Análise de Sentimento v3 (Espanhol)               |
| `3.0-fr`   | Análise de Sentimento v3 (francês)                |
| `3.0-it`   | Análise de Sentimento v3 (Italiano)               |
| `3.0-de`   | Análise de Sentimento v3 (alemão)                |
| `3.0-zh`   | Análise de Sentimento v3 (chinês - simplificado)  |
| `3.0-zht`  | Análise de Sentimento v3 (chinês - tradicional) |
| `3.0-ja`   | Análise de Sentimento v3 (Japonês)              |
| `3.0-pt`   | Análise de Sentimento v3 (Português)            |
| `3.0-nl`   | Análise de Sentimento v3 (Holandês)                 |
| `2.1`    | Análise de Sentimento v2      |

[ad-containers]: ../anomaly-Detector/anomaly-detector-container-howto.md
[cv-containers]: ../computer-vision/computer-vision-how-to-install-containers.md
[fa-containers]: ../face/face-how-to-install-containers.md
[fr-containers]: ../form-recognizer/form-recognizer-container-howto.md
[lu-containers]: ../luis/luis-container-howto.md
[sp-stt]: ../speech-service/speech-container-howto.md?tabs=stt
[sp-cstt]: ../speech-service/speech-container-howto.md?tabs=cstt
[sp-tts]: ../speech-service/speech-container-howto.md?tabs=tts
[sp-ctts]: ../speech-service/speech-container-howto.md?tabs=ctts
[sp-ntts]: ../speech-service/speech-container-howto.md?tabs=ntts
[sp-lid]: ../speech-service/speech-container-howto.md?tabs=lid
[ta-kp]: ../text-analytics/how-tos/text-analytics-how-to-install-containers.md?tabs=keyphrase
[ta-la]: ../text-analytics/how-tos/text-analytics-how-to-install-containers.md?tabs=language
[ta-se]: ../text-analytics/how-tos/text-analytics-how-to-install-containers.md?tabs=sentiment
