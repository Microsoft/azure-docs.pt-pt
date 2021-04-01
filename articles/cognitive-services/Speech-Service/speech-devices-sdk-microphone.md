---
title: Recomendações de conjunto de microfone SDK de dispositivos de fala
titleSuffix: Azure Cognitive Services
description: Recomendações de conjunto de microfone SDK de dispositivos de fala. Estas geometrias de matriz são recomendadas para utilização com a Microsoft Audio Stack.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/16/2019
ms.author: erhopf
ms.openlocfilehash: 698a1d52af6c2472d6c025851ead1a0b6a6dff82
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "95015346"
---
# <a name="speech-devices-sdk-microphone-array-recommendations"></a>Recomendações de conjunto de microfone SDK de dispositivos de fala

Neste artigo, aprende-se a desenhar uma matriz de microfone para o SDK de Dispositivos de Fala.

O Speech Devices SDK funciona melhor com uma matriz de microfone que foi desenhada de acordo com as seguintes orientações, incluindo a geometria do microfone e a seleção de componentes. Orientações também são dadas sobre a integração e considerações elétricas.

## <a name="microphone-geometry"></a>Geometria do microfone

Recomenda-se a utilização das seguintes geometrias de matrizes com a Microsoft Audio Stack. A localização das fontes sonoras e a rejeição do ruído ambiente é melhorada com um maior número de microfones com dependências de aplicações específicas, cenários de utilizador e o fator de forma do dispositivo.

| Geometria & de Microfones | Matriz Circular | Matriz Circular | Matriz Linear | Matriz Linear |
| --- | -------------- | --- | ------------ | --- |
|     | <img src="media/speech-devices-sdk/7-mic-c.png" alt="7 mic circular array" width="150"/> | <img src="media/speech-devices-sdk/4-mic-c.png" alt="4 mic circular array" width="150"/> | <img src="media/speech-devices-sdk/4-mic-l.png" alt="4 mic linear array" width="150"/> | <img src="media/speech-devices-sdk/2-mic-l.png" alt="2 mic linear array" width="150"/> |
| \# Mics | 7 | 4 | 4 | 2 |
| Geometria | 6 Exterior, 1 Centro, Raio = 42,5 mm, Uniformemente Espaçado | 3 Exterior, 1 Centro, Raio = 42,5 mm, Uniformemente Espaçado | Comprimento = 120 mm, Espaçamento = 40 mm | Espaçamento = 40 mm |

Os canais de microfone devem ser encomendados de acordo com a numeragem representada para cada matriz acima, aumentando a partir de 0. O Microsoft Audio Stack necessitará de um fluxo adicional de reprodução de áudio para realizar o cancelamento do eco.

## <a name="component-selection"></a>Seleção de componentes

Os componentes do microfone devem ser selecionados para reproduzir com precisão um sinal livre de ruído e distorção.

As propriedades recomendadas ao selecionar microfones são:

| Parâmetro | Recomendado |
| --------- | ----------- |
| SNR | \>= 65 dB (sinal de 1 kHz 94 dBSPL, ruído ponderado A) |
| Correspondência de amplitude | ± 1 dB @ 1 kHz |
| Correspondência de fase | ± 2° @ 1 kHz |
| Ponto de sobrecarga acústica (AOP) | \>= 120 dBSPL (THD = 10%) |
| Taxa de bit | Mínimo de 24 bits |
| Taxa de Amostragem | Mínimo de 16 kHz\* |
| Resposta de Frequência | ± 3 dB, 200-8000 Hz Máscara Flutuante\* |
| Fiabilidade | Intervalo de temperatura de armazenamento -40°C a 70°C<br />Intervalo de temperatura operacional -20°C a 55°C |

\*_Podem ser necessárias taxas de amostragem mais elevadas ou gamas de frequências "mais amplas" para aplicações de comunicações de alta qualidade (VoIP)_

Uma boa seleção dos componentes deve ser emparelhada com uma boa integração eletroacústica, a fim de evitar comprometer o desempenho dos componentes utilizados. Casos de utilização únicos também podem exigir requisitos adicionais (por exemplo: intervalos de temperatura de funcionamento).

## <a name="microphone-array-integration"></a>Integração da matriz do microfone

O desempenho da matriz do microfone quando integrado num dispositivo diferirá da especificação do componente. É importante garantir que os microfones estão bem combinados após a integração. Por conseguinte, o desempenho do dispositivo medido após qualquer ganho fixo ou QE deve satisfazer as seguintes recomendações:

| Parâmetro          | Recomendado                                        |
| ------------------ | -------------------------------------------------- |
| SNR                | \> 63 dB (sinal de 1 kHz 94 dBSPL, ruído ponderado A) |
| Sensibilidade à saída | -26 dBFS/Pa @ 1 kHz (recomendado)                  |
| Correspondência de amplitude | ± 2 dB, 200-8000 Hz                                |
| THD%\*             | ≤ 1%, 200-8000 Hz, 94 dBSPL, 5ª Ordem             |
| Resposta de Frequência | ± 6 dB, 200-8000 Hz Máscara Flutuante\*\*              |

\*\*_É necessário um altifalante de baixa distorção para medir o THD (por exemplo, Neumann KH120)_

\*\*_Podem ser necessárias gamas de frequências "mais amplas" para aplicações de comunicações de alta qualidade (VoIP)_

## <a name="speaker-integration-recommendations"></a>Recomendações de integração de oradores

Como o cancelamento de eco é necessário para dispositivos de reconhecimento de voz que contenham altifalantes, são fornecidas recomendações adicionais para a seleção e integração de alto-falantes.

| Parâmetro | Recomendado |
| --------- | ----------- |
| Considerações de Linearidade | Não é necessário um processamento não linear após a referência ao altifalante, caso contrário é necessário um fluxo de referência de backback baseado em hardware |
| Speaker Loopback | Fornecido via WASAPI, APIs privados, plug-in alsa personalizado (Linux), ou fornecido através de canal firmware |
| THD% | 3ª Oitava Bandas mínimas 5ª Encomenda, 70 dBA Playback @ 0,8 m ≤ 6,3%, 315-500 Hz ≤ 5%, 630-5000 Hz |
| Acoplamento de eco aos microfones | \> -10 dB TCLw utilizando o método ITU-T G.122 anexo B.4, normalizado ao nível do microfone<br />TCLw = TCLwmeasured \+ (Nível medido - Sensibilidade à saída do alvo)<br />TCLw = TCLwmeasured \+ (Nível medido - (-26)) |

## <a name="integration-design-architecture"></a>Arquitetura de design de integração

São necessárias as seguintes orientações para a arquitetura na integração dos microfones num dispositivo:

| Parâmetro | Recomendação |
| --------- | -------------- |
| Semelhança do porto mic | Todas as portas do microfone têm o mesmo comprimento na matriz |
| Dimensões da Porta Mic | Tamanho da porta Ø0.8-1.0 mm. Comprimento da porta / diâmetro da porta \< 2 |
| Vedação de microfones         | Vedação de vedação implementadas uniformemente em pilha-up. Recomendar \> rácio de compressão de 70% para as juntas de espuma |
| Fiabilidade do microfone     | A malha deve ser utilizada para evitar poeiras e entradas (entre PCB para microfones com edutos inferiores e vedação de vedação/tampa superior) |
| Isolamento mic       | Juntas de borracha e vibrações dissociando-se através da estrutura, particularmente para isolar quaisquer caminhos de vibração devido a altifalantes integrados |
| Relógio de amostragem      | O áudio do dispositivo deve estar livre de nervosismo e abandonos com baixa deriva |
| Capacidade de Gravação   | O dispositivo deve ser capaz de registar fluxos brutos de canais individuais simultaneamente |
| USB                 | Todos os dispositivos de entrada de áudio USB devem definir descritores de acordo com os [dispositivos de áudio USB Rev3 Spec](https://www.usb.org/document-library/usb-audio-devices-rev-30-and-adopters-agreement) |
| Geometria do microfone | Os controladores devem implementar os [descritores de geometria da matriz do microfone](/windows-hardware/drivers/audio/ksproperty-audio-mic-array-geometry) corretamente |
| Descoberta     | Os dispositivos não devem ter qualquer hardware, firmware ou algoritmos de processamento de áudio não lineares não lineares não-lineares não-lineares indebráveis ou incontroláveis |
| Formato de captura      | Os formatos de captura devem utilizar uma taxa mínima de amostragem de 16 kHz e a profundidade recomendada de 24 bits |

## <a name="electrical-architecture-considerations"></a>Considerações de arquitetura elétrica

Quando aplicável, as matrizes podem ser ligadas a um anfitrião USB (como um SoC que executa o Microsoft Audio Stack) e interfaces para serviços de fala ou outras aplicações.

Os componentes de hardware, tais como a conversão PDM-to-TDM, devem assegurar que a gama dinâmica e o SNR dos microfones sejam preservados dentro de re-samplers.

A classe 2.0 usb audio de alta velocidade 2.0 deve ser suportada em quaisquer MCUs áudio, a fim de fornecer a largura de banda necessária para até sete canais a taxas de amostra mais elevadas e profundidades de bits.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Saiba mais sobre os dispositivos de fala SDK](speech-devices-sdk.md)