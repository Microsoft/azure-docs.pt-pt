---
title: Executar exemplo de recipiente de estivador executar comando
titleSuffix: Azure Cognitive Services
description: Estivar executar comando para recipiente de saúde
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 07/07/2020
ms.author: aahi
ms.openlocfilehash: 72a877ab8cfda126016376017c0a7eb42b1186d6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86108948"
---
Para executar o recipiente, primeiro encontre a sua identificação de imagem:
 
```bash
docker images --format "table {{.ID}}\t{{.Repository}}\t{{.Tag}}"
```

Execute o seguinte `docker run` comando. Substitua os espaços reservados abaixo pelos seus próprios valores:

| Marcador de posição | Valor | Formato ou exemplo |
|-------------|-------|---|
| **{API_KEY}** | A chave para o seu recurso Text Analytics. Pode encontrá-lo na página **chave e ponta final** do seu recurso, no portal Azure. |`xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`|
| **{ENDPOINT_URI}** | O ponto final para aceder à API de Análise de Texto. Pode encontrá-lo na página **chave e ponta final** do seu recurso, no portal Azure. | `https://<your-custom-subdomain>.cognitiveservices.azure.com` |
| **{IMAGE_ID}** | A identificação da imagem do seu recipiente. | `1.1.011300001-amd64-preview` |
| **{INPUT_DIR}** | O diretório de entrada para o contentor. | Janelas: `C:\healthcareMount` <br> Linux/MacOS: `/home/username/input` |

```bash
docker run --rm -it -p 5000:5000 --cpus 6 --memory 12g \
--mount type=bind,src={INPUT_DIR},target=/output {IMAGE_ID} \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY} \
Logging:Disk:Format=json
```

Este comando:

- Assume que o diretório de entrada existe na máquina de anfitrião
- Executa um texto analytics para saúde recipiente a partir da imagem do recipiente
- Atribui 6 núcleos de CPU e 12 gigabytes (GB) de memória
- Expõe a porta TCP 5000 e atribui uma pseudo-TTY para o contentor
- Remove automaticamente o recipiente depois de sair. A imagem do recipiente ainda está disponível no computador anfitrião.
