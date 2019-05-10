---
title: Recomendações de matriz de microfone de SDK de dispositivos de voz - serviços de voz
titleSuffix: Azure Cognitive Services
description: Recomendações de matriz de microfone de SDK de dispositivos de voz. Geometrias de matriz seguintes são recomendadas para utilização com a pilha de áudio de Microsoft. Localização de origens de som e de rejeição de acordo com o som são aprimoradas com maior número de microfones com dependências em aplicações específicas, cenários de usuário e o fator de formulário de dispositivo.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: erhopf
ms.openlocfilehash: 63dd64e900cf68e708032569ca75ac2e8b221491
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/07/2019
ms.locfileid: "65236998"
---
# <a name="speech-devices-sdk-microphone-array-recommendations"></a>Recomendações de matriz de microfone do SDK de dispositivos de voz

Neste artigo, saiba como criar uma matriz de microfone para o SDK de dispositivos de voz.

O SDK de dispositivos de voz funciona melhor com uma matriz de microfone foi projetada de acordo com as seguintes diretrizes, incluindo a seleção de geometria e componente do microfone. Documentação de orientação também é fornecida no considerações elétrica e de integração.

## <a name="microphone-geometry"></a>Geometria de microfone

Geometrias de matriz seguintes são recomendadas para utilização com a pilha de áudio de Microsoft. Localização de origens de som e de rejeição de acordo com o som são aprimoradas com maior número de microfones com dependências em aplicações específicas, cenários de usuário e o fator de formulário de dispositivo.

|          | Matriz circular    |       |  Matriz linear              |                |
|----------|-------------------|-------------------|----------------|----------------|
|          |<img src="media/speech-devices-sdk/7-mic-c.png" alt="7 mic circular array" width="150"/>|<img src="media/speech-devices-sdk/4-mic-c.png" alt="4 mic circular array" width="150"/>|<img src="media/speech-devices-sdk/4-mic-l.png" alt="4 mic linear array" width="150"/>|<img src="media/speech-devices-sdk/2-mic-l.png" alt="2 mic linear array" width="150"/>|
| \# Microfones  | 7                 | 4                 | 4              | 2              |
| Geometry | 6 1 externa, centro, Radius = 42.5 mm, espaçado| 3 1 externa, centro, Radius = 42.5 mm, espaçado | Comprimento = 120 mm, espaçamento = 40 mm | Espaçamento = 40 mm |

Canais de microfone devem ser ordenados de acordo com a numeração descrito para cada acima matriz, o aumento de 0.  A pilha de áudio do Microsoft exigirá um fluxo de referência adicionais de reprodução de áudio para efetuar o cancelamento de eco.

## <a name="component-selection"></a>Seleção do componente

Componentes de microfone devem ser selecionadas para reproduzir com exatidão um sinal livre de ruído e distorção.

As propriedades recomendadas ao selecionar microfones são:

| Parâmetro                         | Recomendado                       |
|-----------------------------------|-----------------------------------|
| SNR                               | \> 65 dB (1 kHz sinal 94 dBSPL, ruído ponderada de R)   |
| Amplitude correspondentes                | ± 1 dB @ 1 kHz                     |
| Fase de correspondência                    | ± 2° @ 1 kHz                       |
| O ponto de sobrecarga de acústicos (AOP)     | \> 120 dBSPL (THD = 10%)          |
| Taxa de bits                          | Mínimo 24 bits                    |
| Taxa de Amostragem                     | Mínimo kHz 16\*                   |
| Directivity                       | Onidirecionais                   |
| Resposta de frequência                | ± dB 3, a máscara de vírgula flutuante Hz de 200 8000\*|
| Fiabilidade                       | Intervalo de temperatura de armazenamento-40,Year),temperature ° C para 70 ° C<br />Intervalo de temperatura operacional-20 ° C 55 ° c  |

*\*Taxas de amostragem mais elevadas ou intervalos de frequência "maiores" podem ser necessários para aplicativos de comunicação de alta qualidade (VoIP)*

Seleção do componente boa deve ser emparelhada com boa integração electroacoustic para evitar relativamente o desempenho dos componentes utilizados. Também podem necessitar de casos de utilização exclusivo de requisitos adicionais (por exemplo: operacional intervalos de temperatura).

## <a name="microphone-array-integration"></a>Integração de matriz de microfone

O desempenho de matrizes quando integrado num dispositivo e depois qualquer ganho fixo ou EQ deve cumprir as seguintes recomendações:

|  Parâmetro        |    Recomendado |
|--------------------|----------------------------------------------------|
|  SNR                 | \> 65 dB (1 kHz sinal 94 dBSPL, ruído ponderada de R) |
|  Sensibilidade de saída  | -26 dBFS/Pa @ 1 kHz (recomendado) |
|  Amplitude correspondentes  | ± 2 dB, 200-8000 Hz |
|  Fase de correspondência      | ± 5°, 200-8000 Hz |
| THD%                 | % De 1 ≤, 200 8000 Hz, dBSPL 94, 5th ordem |
|  Resposta de frequência  | ± 6 dB, a máscara de vírgula flutuante Hz de 200 8000\* |

*\*Intervalos de frequência "Maiores" podem ser necessários para aplicativos de comunicação de alta qualidade (VoIP)*

## <a name="speaker-integration-recommendations"></a>Recomendações de integração de orador

Como eco cancelamento é necessário para dispositivos de reconhecimento de fala que contêm os oradores, recomendações adicionais estão disponíveis para seleção de orador e integração.

| Parâmetro                         | Recomendado                       |
|-----------------------------------|-----------------------------------|
| Considerações de linearity          | Nenhum processamento não-lineares após a referência de orador, caso contrário, um fluxo de referência de loopback baseada em hardware é necessário  |
| Loopback de orador                  | Fornecido por meio de WASAPI, privada APIs, personalizado ALSA Plug-in (Linux), ou fornecidos através do canal de firmware      |
| THD%                              | 3º oitava inteira bandas mínimo 5th ordem, 70 dBA reprodução @ m 0,8 ≤ 6.3%, 315 500 Hz ≤ 5%, Hz 630 5000                 |
| Acoplamento de eco a microfones      | \> dB-10 TCLw usando o método ITU-T G.122 Annex B.4, normalizados para o nível de mic<br />TCLw = TCLwmeasured \+ (medida de nível - alvo sensibilidade de saída)<br />TCLw = TCLwmeasured \+ (medida no nível - (-26)) |

## <a name="integration-design-architecture"></a>Arquitetura de design de integração

As seguintes diretrizes para arquitetura são necessárias quando integrar microfones num dispositivo:

| Parâmetro                         | Recomendação                    |
|-----------------------------------|-----------------------------------|
| Porta de MIC semelhança               | Todas as portas do microfone são o mesmo comprimento na matriz    |
| Dimensões de porta de MIC               | Porta tamanho Ø0.8 1.0 mm. Comprimento da porta porta diâmetro / \< 2              |
| Lacre de MIC                       | Selar gaskets uniformemente implementados na pilha de segurança. Recomendamos \> taxa de compressão de 70% para gaskets espuma     |
| Fiabilidade de MIC                   | Malha deve ser usada para impedir a poeira e de entrada (entre PCB para parte inferior portado microfones e selar capa gasket/superior)  |
| Isolamento de MIC                     | Gaskets de borracha e vibração desacoplamento pela estrutura, particularmente para isolar qualquer caminhos de vibração devido a oradores integradas      |
| Período de amostragem                    | Áudio de dispositivo tem de ser sem distorção e implementações de pacotes ignorados com descompassos baixo    |
| Capacidade de registo                 | O dispositivo tem de ser capaz de gravar fluxos não processados do canal individual em simultâneo |
| USB                               | Todos os dispositivos áudio USB entrados tem de definir descritores de acordo com o [USB áudio dispositivos Rev3 Spec](https://www.usb.org/document-library/usb-audio-devices-rev-30-and-adopters-agreement) |
| Geometria de microfone               | Tem de implementar controladores [descritores de geometria de matriz de microfone](https://docs.microsoft.com/windows-hardware/drivers/audio/ksproperty-audio-mic-array-geometry) corretamente  |
| Capacidade de deteção                   | Dispositivos não têm de ter qualquer undiscoverable ou incontroláveis para o hardware, firmware ou 3º algoritmos de baseada em software não-lineares processamento de áudio de terceiros de/para o dispositivo|
| Captura de formato                    | Formatos de captura tem de utilizar uma taxa de amostragem mínimo de 16 kHz e a profundidade de 24 bits recomendada      |

## <a name="electrical-architecture-considerations"></a>Considerações sobre a arquitetura elétrica

Quando aplicável, as matrizes podem estar conectadas a um anfitrião USB (por exemplo, um SoC que executa a pilha de áudio da Microsoft) e interfaces para outras aplicações ou serviços de voz.

Componentes de hardware, tal como a conversão de PDM-TDM devem certificar-se de que o intervalo dinâmico e SNR dos microfones serão mantidos no samplers reavaliação.

Alta velocidade 2.0 classe áudio USB deve ser suportada dentro de qualquer MCUs áudio a fim de fornecer a largura de banda necessária para até sete canais taxas de exemplo mais elevadas e fundo o bits.

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Saiba mais sobre o SDK de dispositivos de voz](speech-devices-sdk.md)
