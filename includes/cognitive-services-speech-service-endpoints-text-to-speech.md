---
author: wolfma61
ms.service: cognitive-services
ms.topic: include
ms.date: 05/06/2019
ms.author: wolfma
ms.openlocfilehash: a1de313245e826511a7e9c864165ac44bcb48530
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80234205"
---
### <a name="standard-and-neural-voices"></a>Vozes padrão e neurais

Utilize esta tabela para determinar a disponibilidade de vozes standard e neurais por região/ponto final:

| Região | Ponto Final | Vozes Padrão | Vozes Neurais |
|--------|----------|-----------------|---------------|
| Leste da Austrália | `https://australiaeast.tts.speech.microsoft.com/cognitiveservices/v1` | Sim | Sim |
| Sul do Brasil | `https://brazilsouth.tts.speech.microsoft.com/cognitiveservices/v1` | Sim | Não |
| Canadá Central | `https://canadacentral.tts.speech.microsoft.com/cognitiveservices/v1` | Sim | Sim |
| E.U.A. Central | `https://centralus.tts.speech.microsoft.com/cognitiveservices/v1` | Sim | Não |
| Ásia Leste | `https://eastasia.tts.speech.microsoft.com/cognitiveservices/v1` | Sim | Não |
| E.U.A. Leste | `https://eastus.tts.speech.microsoft.com/cognitiveservices/v1` | Sim | Sim |
| E.U.A. Leste 2 | `https://eastus2.tts.speech.microsoft.com/cognitiveservices/v1` | Sim | Não |
| França Central | `https://francecentral.tts.speech.microsoft.com/cognitiveservices/v1` | Sim | Não |
| Índia Central | `https://centralindia.tts.speech.microsoft.com/cognitiveservices/v1` | Sim | Sim |
| Leste do Japão | `https://japaneast.tts.speech.microsoft.com/cognitiveservices/v1` | Sim | Não |
| Oeste do Japão | `https://japanwest.tts.speech.microsoft.com/cognitiveservices/v1` | Sim | Não |
| Coreia do Sul Central | `https://koreacentral.tts.speech.microsoft.com/cognitiveservices/v1` | Sim | Não |
| E.U.A. Centro-Norte | `https://northcentralus.tts.speech.microsoft.com/cognitiveservices/v1` | Sim | Não |
| Europa do Norte | `https://northeurope.tts.speech.microsoft.com/cognitiveservices/v1` | Sim | Não |
| E.U.A. Centro-Sul | `https://southcentralus.tts.speech.microsoft.com/cognitiveservices/v1` | Sim | Sim |
| Ásia Sudeste | `https://southeastasia.tts.speech.microsoft.com/cognitiveservices/v1` | Sim | Sim |
| Sul do Reino Unido | `https://uksouth.tts.speech.microsoft.com/cognitiveservices/v1` | Sim | Sim |
| Europa ocidental | `https://westeurope.tts.speech.microsoft.com/cognitiveservices/v1` | Sim | Sim |
| E.U.A. Oeste | `https://westus.tts.speech.microsoft.com/cognitiveservices/v1` | Sim | Não |
| E.U.A.Oeste 2 | `https://westus2.tts.speech.microsoft.com/cognitiveservices/v1` | Sim | Sim |

### <a name="custom-voices"></a>Vozes personalizadas

Se criou uma fonte de voz personalizada, use o ponto final que criou. Também pode utilizar os pontos finais `{deploymentId}` listados abaixo, substituindo o ID de implementação para o seu modelo de voz.

| Região | Ponto Final |
|--------|----------|
| Leste da Austrália | `https://australiaeast.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Sul do Brasil | `https://brazilsouth.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Canadá Central | `https://canadacentral.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| E.U.A. Central | `https://centralus.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Ásia Leste | `https://eastasia.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| E.U.A. Leste | `https://eastus.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| E.U.A. Leste 2 | `https://eastus2.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| França Central | `https://francecentral.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Índia Central | `https://centralindia.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Leste do Japão | `https://japaneast.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Oeste do Japão | `https://japanwest.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Coreia do Sul Central | `https://koreacentral.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| E.U.A. Centro-Norte | `https://northcentralus.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Europa do Norte | `https://northeurope.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| E.U.A. Centro-Sul | `https://southcentralus.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Ásia Sudeste | `https://southeastasia.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Sul do Reino Unido | `https://uksouth.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Europa ocidental | `https://westeurope.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| E.U.A. Oeste | `https://westus.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| E.U.A.Oeste 2 | `https://westus2.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
