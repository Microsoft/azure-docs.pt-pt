---
author: wolfma61
ms.service: cognitive-services
ms.topic: include
ms.date: 05/06/2019
ms.author: wolfma
ms.openlocfilehash: d5a4b3a07854c2664de7ec60f3677b666798a9bd
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/07/2019
ms.locfileid: "65237062"
---
### <a name="standard-and-neural-voices"></a>Vozes padrão e neurais

Utilize esta tabela para determinar a disponibilidade de vozes padrão e neurais por região/ponto de extremidade:

| Região | Ponto Final | Vozes padrão | Vozes neurais |
|--------|----------|-----------------|---------------|
| Leste da Austrália | `https://australiaeast.tts.speech.microsoft.com/cognitiveservices/v1` | Sim | Sim |
| Canadá Central | `https://canadacentral.tts.speech.microsoft.com/cognitiveservices/v1` | Sim | Sim |
| EUA Central | `https://centralus.tts.speech.microsoft.com/cognitiveservices/v1` | Sim | Não |
| Ásia Oriental | `https://eastasia.tts.speech.microsoft.com/cognitiveservices/v1` | Sim | Não |
| EUA Leste | `https://eastus.tts.speech.microsoft.com/cognitiveservices/v1` | Sim | Sim |
| EUA Leste 2 | `https://eastus2.tts.speech.microsoft.com/cognitiveservices/v1` | Sim | Não |
| França Central | `https://francecentral.tts.speech.microsoft.com/cognitiveservices/v1` | Sim | Não |
| Índia Central | `https://centralindia.tts.speech.microsoft.com/cognitiveservices/v1` | Sim | Sim |
| Leste do Japão | `https://japaneast.tts.speech.microsoft.com/cognitiveservices/v1` | Sim | Não |
| Coreia do Sul Central | `https://koreacentral.tts.speech.microsoft.com/cognitiveservices/v1` | Sim | Não |
| EUA Centro-Norte | `https://northcentralus.tts.speech.microsoft.com/cognitiveservices/v1` | Sim | Não |
| Europa do Norte | `https://northeurope.tts.speech.microsoft.com/cognitiveservices/v1` | Sim | Não |
| EUA Centro-Sul | `https://southcentralus.tts.speech.microsoft.com/cognitiveservices/v1` | Sim | Sim |
| Sudeste Asiático | `https://southeastasia.tts.speech.microsoft.com/cognitiveservices/v1` | Sim | Sim |
| Reino Unido Sul | `https://uksouth.tts.speech.microsoft.com/cognitiveservices/v1` | Sim | Sim |
| Europa Ocidental | `https://westeurope.tts.speech.microsoft.com/cognitiveservices/v1` | Sim | Sim |
| EUA Oeste | `https://westus.tts.speech.microsoft.com/cognitiveservices/v1` | Sim | Não |
| E.U.A. Oeste 2 | `https://westus2.tts.speech.microsoft.com/cognitiveservices/v1` | Sim | Sim |

### <a name="custom-voices"></a>Vozes personalizadas

Se criou um tipo de voz personalizada, utilize o ponto final que criou. Também pode utilizar os pontos finais listados abaixo, substituindo o `{deploymentId}` com o ID de implementação para o modelo de voz.

| Região | Ponto Final |
|--------|----------|
| Leste da Austrália | `https://australiaeast.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Canadá Central | `https://canadacentral.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| EUA Central | `https://centralus.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Ásia Oriental | `https://eastasia.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| EUA Leste | `https://eastus.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| EUA Leste 2 | `https://eastus2.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| França Central | `https://francecentral.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Índia Central | `https://centralindia.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Leste do Japão | `https://japaneast.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Coreia do Sul Central | `https://koreacentral.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| EUA Centro-Norte | `https://northcentralus.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Europa do Norte | `https://northeurope.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| EUA Centro-Sul | `https://southcentralus.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Sudeste Asiático | `https://southeastasia.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Reino Unido Sul | `https://uksouth.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Europa Ocidental | `https://westeurope.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| EUA Oeste | `https://westus.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| E.U.A. Oeste 2 | `https://westus2.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
