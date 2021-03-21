---
author: wolfma61
ms.service: cognitive-services
ms.topic: include
ms.date: 05/06/2019
ms.author: wolfma
ms.openlocfilehash: 1eefb7097f672552956e90a19e0a7b411cae8a24
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102455704"
---
### <a name="standard-and-neural-voices"></a>Vozes padrão e neurais

Utilize este quadro para determinar a **disponibilidade de vozes padrão** por região/ponto final:

| Region | Ponto final |
|--------|----------|
| Leste da Austrália | `https://australiaeast.tts.speech.microsoft.com/cognitiveservices/v1` |
| Sul do Brasil | `https://brazilsouth.tts.speech.microsoft.com/cognitiveservices/v1` |
| Canadá Central | `https://canadacentral.tts.speech.microsoft.com/cognitiveservices/v1` |
| E.U.A. Central | `https://centralus.tts.speech.microsoft.com/cognitiveservices/v1` |
| Ásia Leste | `https://eastasia.tts.speech.microsoft.com/cognitiveservices/v1` |
| E.U.A. Leste | `https://eastus.tts.speech.microsoft.com/cognitiveservices/v1` |
| E.U.A. Leste 2 | `https://eastus2.tts.speech.microsoft.com/cognitiveservices/v1` |
| França Central | `https://francecentral.tts.speech.microsoft.com/cognitiveservices/v1` |
| Índia Central | `https://centralindia.tts.speech.microsoft.com/cognitiveservices/v1` |
| Leste do Japão | `https://japaneast.tts.speech.microsoft.com/cognitiveservices/v1` |
| Oeste do Japão | `https://japanwest.tts.speech.microsoft.com/cognitiveservices/v1` |
| Coreia do Sul Central | `https://koreacentral.tts.speech.microsoft.com/cognitiveservices/v1` |
| E.U.A. Centro-Norte | `https://northcentralus.tts.speech.microsoft.com/cognitiveservices/v1` |
| Europa do Norte | `https://northeurope.tts.speech.microsoft.com/cognitiveservices/v1` |
| E.U.A. Centro-Sul | `https://southcentralus.tts.speech.microsoft.com/cognitiveservices/v1` |
| Sudeste Asiático | `https://southeastasia.tts.speech.microsoft.com/cognitiveservices/v1` |
| Sul do Reino Unido | `https://uksouth.tts.speech.microsoft.com/cognitiveservices/v1` |
| Europa Ocidental | `https://westeurope.tts.speech.microsoft.com/cognitiveservices/v1` |
| E.U.A. Oeste | `https://westus.tts.speech.microsoft.com/cognitiveservices/v1` |
| E.U.A. Oeste 2 | `https://westus2.tts.speech.microsoft.com/cognitiveservices/v1` |

> [!TIP]
> [As vozes na pré-visualização](../articles/cognitive-services/Speech-Service/language-support.md#neural-voices-in-preview) só estão disponíveis nestas três regiões: Leste dos EUA, Europa Ocidental e Sudeste Asiático.

Utilize esta tabela para determinar a **disponibilidade de vozes neurais** por região/ponto final:

| Region | Ponto final |
|--------|----------|
| Leste da Austrália | `https://australiaeast.tts.speech.microsoft.com/cognitiveservices/v1` | 
| Canadá Central | `https://canadacentral.tts.speech.microsoft.com/cognitiveservices/v1` |
| E.U.A. Leste | `https://eastus.tts.speech.microsoft.com/cognitiveservices/v1` |
| Índia Central | `https://centralindia.tts.speech.microsoft.com/cognitiveservices/v1` |
| E.U.A. Centro-Sul | `https://southcentralus.tts.speech.microsoft.com/cognitiveservices/v1` |
| Sudeste Asiático | `https://southeastasia.tts.speech.microsoft.com/cognitiveservices/v1` |
| Sul do Reino Unido | `https://uksouth.tts.speech.microsoft.com/cognitiveservices/v1` |
| Europa Ocidental | `https://westeurope.tts.speech.microsoft.com/cognitiveservices/v1` |
| E.U.A. Oeste 2 | `https://westus2.tts.speech.microsoft.com/cognitiveservices/v1` |

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
