---
author: wolfma61
ms.service: cognitive-services
ms.topic: include
ms.date: 05/06/2019
ms.author: wolfma
ms.openlocfilehash: 6b16dea3c4f9241133b91b092c90c9056da57de0
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/14/2021
ms.locfileid: "100515072"
---
### <a name="standard-and-neural-voices"></a>Vozes padrão e neurais

Utilize esta tabela para determinar a disponibilidade de vozes padrão e neurais por região/ponto final:

| Region | Ponto final | Vozes Neurais | Vozes Padrão |
|--------|----------|-----------------|---------------|
| Leste da Austrália | `https://australiaeast.tts.speech.microsoft.com/cognitiveservices/v1` | Yes | Yes |
| Sul do Brasil | `https://brazilsouth.tts.speech.microsoft.com/cognitiveservices/v1` | No | Yes |
| Canadá Central | `https://canadacentral.tts.speech.microsoft.com/cognitiveservices/v1` | Yes | Yes |
| E.U.A. Central | `https://centralus.tts.speech.microsoft.com/cognitiveservices/v1` | No | Yes |
| Ásia Leste | `https://eastasia.tts.speech.microsoft.com/cognitiveservices/v1` | No | Yes |
| E.U.A. Leste | `https://eastus.tts.speech.microsoft.com/cognitiveservices/v1` | Yes | Yes |
| E.U.A. Leste 2 | `https://eastus2.tts.speech.microsoft.com/cognitiveservices/v1` | No | Yes |
| França Central | `https://francecentral.tts.speech.microsoft.com/cognitiveservices/v1` | No | Yes |
| Índia Central | `https://centralindia.tts.speech.microsoft.com/cognitiveservices/v1` | Yes | Yes |
| Leste do Japão | `https://japaneast.tts.speech.microsoft.com/cognitiveservices/v1` | No | Yes |
| Oeste do Japão | `https://japanwest.tts.speech.microsoft.com/cognitiveservices/v1` | No | Yes |
| Coreia do Sul Central | `https://koreacentral.tts.speech.microsoft.com/cognitiveservices/v1` | No | Yes |
| E.U.A. Centro-Norte | `https://northcentralus.tts.speech.microsoft.com/cognitiveservices/v1` | No | Yes |
| Europa do Norte | `https://northeurope.tts.speech.microsoft.com/cognitiveservices/v1` | No | Yes |
| E.U.A. Centro-Sul | `https://southcentralus.tts.speech.microsoft.com/cognitiveservices/v1` | Yes | Yes |
| Sudeste Asiático | `https://southeastasia.tts.speech.microsoft.com/cognitiveservices/v1` | Yes | Yes |
| Sul do Reino Unido | `https://uksouth.tts.speech.microsoft.com/cognitiveservices/v1` | Yes | Yes |
| Europa Ocidental | `https://westeurope.tts.speech.microsoft.com/cognitiveservices/v1` | Yes | Yes |
| E.U.A. Oeste | `https://westus.tts.speech.microsoft.com/cognitiveservices/v1` | No | Yes |
| E.U.A. Oeste 2 | `https://westus2.tts.speech.microsoft.com/cognitiveservices/v1` | Yes | Yes |

> [!TIP]
> [As vozes na pré-visualização](../articles/cognitive-services/Speech-Service/language-support.md#neural-voices-in-preview) só estão disponíveis nestas três regiões: Leste dos EUA, Europa Ocidental e Sudeste Asiático.

### <a name="custom-voices"></a>Vozes personalizadas

Se criou uma fonte de voz personalizada, use o ponto final que criou. Também pode utilizar os pontos finais listados abaixo, substituindo o `{deploymentId}` ID de implementação para o seu modelo de voz.

| Region | Ponto final |
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
| Sudeste Asiático | `https://southeastasia.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Sul do Reino Unido | `https://uksouth.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Europa Ocidental | `https://westeurope.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| E.U.A. Oeste | `https://westus.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| E.U.A. Oeste 2 | `https://westus2.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |

### <a name="custom-neural-voice"></a>Voz Neural Personalizada

A tabela seguinte detalha o suporte regional para as funcionalidades de Voz Neural Personalizada.

| Funcionalidade | Regiões suportadas |
|---|---|
| Hospedagem de modelo de voz | Leste dos EUA, Oeste dos EUA 2, South Central EUA, Sudeste Asiático, Reino Unido Sul, Europa Ocidental, Austrália Leste |
| Personagens em tempo real | Leste dos EUA, Oeste dos EUA 2, South Central EUA, Sudeste Asiático, Reino Unido Sul, Europa Ocidental, Austrália Leste |
| Personagens de áudio longos | Leste dos EUA, Europa Ocidental, Reino Unido Sul, Sudeste Asiático, Índia Central |
| Treino Neural Personalizado | East US, Reino Unido Sul |

### <a name="long-audio-api"></a>API de áudio longo

A API áudio longa está disponível em várias regiões com pontos finais únicos.

| Region | Ponto final |
|--------|----------|
| E.U.A. Leste | `https://eastus.customvoice.api.speech.microsoft.com` |
| Índia Central | `https://centralindia.customvoice.api.speech.microsoft.com` |
| Sudeste Asiático | `https://southeastasia.customvoice.api.speech.microsoft.com` |
| Sul do Reino Unido | `https://uksouth.customvoice.api.speech.microsoft.com` |
| Europa Ocidental | `https://westeurope.customvoice.api.speech.microsoft.com` |
