---
title: Recomendações de matriz de microfone sdk dispositivos de fala
titleSuffix: Azure Cognitive Services
description: Recomendações de matriz de microfone SDK dispositivos de fala. Estas geometrias de matriz são recomendadas para utilização com a Microsoft Audio Stack.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/16/2019
ms.author: erhopf
ms.openlocfilehash: a87bdd7a55036e8b70f0bc5816d2b587c1569202
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77168128"
---
# <a name="speech-devices-sdk-microphone-array-recommendations"></a>Recomendações de matriz de microfone sdk dispositivos de fala

Neste artigo, aprende-se a desenhar uma matriz de microfone para o SDK de Dispositivos de Fala.

O SDK dos Dispositivos de Fala funciona melhor com uma matriz de microfone que foi desenhada de acordo com as seguintes diretrizes, incluindo a geometria do microfone e a seleção de componentes. É também dada orientação sobre a integração e considerações elétricas.

## <a name="microphone-geometry"></a>Geometria do microfone

Recomenda-se a utilização das seguintes geometrias de matriz estoque com a Microsoft Audio Stack. A localização das fontes sonoras e a rejeição do ruído ambiente são melhoradas com um maior número de microfones com dependências de aplicações específicas, cenários de utilizador e o fator formato do dispositivo.

|     | Matriz Circular |     | Matriz Linear |     |
| --- | -------------- | --- | ------------ | --- |
|     | <img src="media/speech-devices-sdk/7-mic-c.png" alt="7 mic circular array" width="150"/> | <img src="media/speech-devices-sdk/4-mic-c.png" alt="4 mic circular array" width="150"/> | <img src="media/speech-devices-sdk/4-mic-l.png" alt="4 mic linear array" width="150"/> | <img src="media/speech-devices-sdk/2-mic-l.png" alt="2 mic linear array" width="150"/> |
| \#Mics | 7 | 4 | 4 | 2 |
| Geometria | 6 Exteriores, 1 Centro, Raio = 42,5 mm, Uniformemente Espaçado | 3 Exteriores, 1 Centro, Raio = 42,5 mm, Uniformemente Espaçado | Comprimento = 120 mm, espaçamento = 40 mm | Espaçamento = 40 mm |

Os canais de microfone devem ser encomendados de acordo com a numeração representada para cada matriz acima, aumentando a partir de 0. O Microsoft Audio Stack necessitará de um fluxo adicional de referência de reprodução áudio para realizar o cancelamento de eco.

## <a name="component-selection"></a>Seleção de componentes

Os componentes do microfone devem ser selecionados para reproduzir com precisão um sinal livre de ruído e distorção.

As propriedades recomendadas ao selecionar microfones são:

| Parâmetro | Recomendado |
| --------- | ----------- |
| SNR | \>= 65 dB (sinal de 1 kHz 94 dBSPL, ruído ponderado a A) |
| Combinação de amplitude | ± 1 dB @ 1 kHz |
| Correspondência de fase | ± 2° @ 1 kHz |
| Ponto de sobrecarga acústica (AOP) | \>= 120 dBSPL (THD = 10%) |
| Taxa bit | Mínimo de 24 bits |
| Taxa de Amostragem | Mínimo de 16 kHz\* |
| Resposta de frequência | ± 3 dB, 200-8000 Hz Máscara Flutuante\* |
| Fiabilidade | Gama de Temperatura de Armazenamento -40°C a 70°C<br />Intervalo de temperatura de funcionamento -20°C a 55°C |

\*_Taxas de amostragem mais elevadas ou intervalos de frequência "mais amplos" podem ser necessários para aplicações de comunicações de alta qualidade (VoIP)_

Uma boa seleção de componentes deve ser emparelhada com uma boa integração eletroacústica, a fim de evitar comprometer o desempenho dos componentes utilizados. Os casos de utilização única também podem exigir requisitos adicionais (por exemplo: intervalos de temperatura de funcionamento).

## <a name="microphone-array-integration"></a>Integração de matriz de microfone

O desempenho da matriz do microfone quando integrado num dispositivo diferirá da especificação do componente. É importante garantir que os microfones sejam bem compatíveis após a integração. Por conseguinte, o desempenho do dispositivo medido após qualquer ganho fixo ou QE deve satisfazer as seguintes recomendações:

| Parâmetro          | Recomendado                                        |
| ------------------ | -------------------------------------------------- |
| SNR                | \>63 dB (sinal de 1 kHz 94 dBSPL, ruído ponderado a a) |
| Sensibilidade à saída | -26 dBFS/Pa @ 1 kHz (recomendado)                  |
| Combinação de amplitude | ± 2 dB, 200-8000 Hz                                |
| THD%\*             | ≤ 1%, 200-8000 Hz, 94 dBSPL, 5ª Ordem             |
| Resposta de frequência | ± 6 dB, 200-8000 Hz Máscara Flutuante\*\*              |

\*\*_É necessário um altifalante de baixa distorção para medir o THD (por exemplo, Neumann KH120)_

\*\*_Podem ser necessárias gamas de frequências "mais amplas" para aplicações de comunicações de alta qualidade (VoIP)_

## <a name="speaker-integration-recommendations"></a>Recomendações de integração de oradores

Como o cancelamento do eco é necessário para dispositivos de reconhecimento de voz que contenham oradores, recomendações adicionais são fornecidas para a seleção e integração de oradores.

| Parâmetro | Recomendado |
| --------- | ----------- |
| Considerações de Linearidade | Não é necessário processamento não linear após referência do altifalante, caso contrário é necessário um fluxo de referência de loopback baseado em hardware |
| Loopback do altifalante | Fornecido via WASAPI, APIs privados, plug-in ALSA personalizado (Linux), ou fornecido através do canal firmware |
| THD% | 3ª Octave Bands mínimo 5ª Ordem, 70 dBA Playback @ 0,8 m ≤ 6,3%, 315-500 Hz ≤ 5%, 630-5000 Hz |
| Eco Acoplamento aos microfones | \>-10 dB TCLw utilizando o método ITU-T G.122 anexo B.4, normalizado ao nível do microfone<br />TCLw = TCLwmeasured \+ (Nível medido - Sensibilidade à saída do alvo)<br />TCLw = TCLwmeasured \+ (Nível Medido - (-26)) |

## <a name="integration-design-architecture"></a>Arquitetura de design de integração

São necessárias as seguintes orientações para a arquitetura ao integrar os microfones num dispositivo:

| Parâmetro | Recomendação |
| --------- | -------------- |
| Semelhança do Porto Mic | Todas as portas do microfone têm o mesmo comprimento na matriz |
| Dimensões do Porto Mic | Tamanho da porta Ø0.8-1,0 mm. Comprimento da porta \< / diâmetro da porta 2 |
| Vedação mic         | Vedação de vedações uniformemente implementadas em empilhamento. Recomendar \> rácio de compressão de 70% para juntas de espuma |
| Fiabilidade do Mic     | A malha deve ser utilizada para evitar poeiras e entradas (entre PCB para microfones de base e vedação de vedação/cobertura superior) |
| Isolamento mic       | Juntas de borracha e vibrações dissociadas através da estrutura, particularmente para isolar quaisquer caminhos de vibração devido a altifalantes integrados |
| Relógio de amostragem      | O áudio do dispositivo deve estar livre de nervosismo e desistentes com baixa deriva |
| Capacidade de gravação   | O dispositivo deve ser capaz de gravar fluxos brutos de canais individuais simultaneamente |
| USB                 | Todos os dispositivos de entrada de áudio USB devem definir descritores de acordo com os dispositivos de [áudio USB Rev3 Spec](https://www.usb.org/document-library/usb-audio-devices-rev-30-and-adopters-agreement) |
| Geometria do microfone | Os condutores devem implementar corretamente [os Descritores de Geometria](https://docs.microsoft.com/windows-hardware/drivers/audio/ksproperty-audio-mic-array-geometry) da Matriz de Microfone |
| Descoberta     | Os dispositivos não devem ter qualquer hardware, firmware ou algoritmos de processamento de áudio não lineares baseados em software de/a partir do dispositivo |
| Formato de captura      | Os formatos de captura devem utilizar uma taxa mínima de amostragem de 16 kHz e profundidade recomendada de 24 bits |

## <a name="electrical-architecture-considerations"></a>Considerações de arquitetura elétrica

Quando aplicável, as matrizes podem ser ligadas a um anfitrião USB (como um SoC que executa o Microsoft Audio Stack) e interfaces para serviços de Fala ou outras aplicações.

Os componentes de hardware, como a conversão PDM-tDM, devem assegurar que a gama dinâmica e o SNR dos microfones sejam preservados dentro dos re-samplers.

Usb Audio Class 2.0 de alta velocidade deve ser suportado dentro de qualquer MCUs áudio, a fim de fornecer a largura de banda necessária para até sete canais a taxas de amostra mais elevadas e profundidades de bits.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Saiba mais sobre os dispositivos de Fala SDK](speech-devices-sdk.md)
