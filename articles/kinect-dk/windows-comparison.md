---
title: Comparação do Azure Kinect DK Windows
description: Diferenças de hardware e software entre Azure Kinect DK e Kinect para Windows v2
author: tesych
ms.author: tesych
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: article
keywords: Kinect, Windows, v2, Azure Kinect, comparação, SDK, diferenças, hardware, software
ms.openlocfilehash: 0a8d399370f354524858bdd658ffd65c0494dd4d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87031580"
---
# <a name="azure-kinect-and-kinect-windows-v2-comparison"></a>Comparação Azure Kinect e Kinect Windows v2

Os kits de hardware E Desenvolvimento de Software Azure Kinect DK têm diferenças em kinect para Windows v2. Qualquer Kinect existente para aplicações windows v2 não funcionará diretamente com a Azure Kinect DK e exigirá a despaagem para o novo SDK.  

## <a name="hardware"></a>Hardware

Diferenças de alto nível entre o kit de desenvolvimento Azure Kinect e o Kinect para o Windows v2 estão listados na tabela seguinte.

| Funcionalidade | Tipo | Azure Kinect DK | Kinect para Windows v2 |
| ------- | ---- | --------------- | --------------------- |
| **Áudio** | Detalhes  | Matriz circular de 7 mic | Matriz linear de 4 mic faseada |
| **Sensor de movimento** | Detalhes | Acelerómetro de 3 eixos giroscópio de 3 eixos | Acelerómetro de 3 eixos |
| **Câmera RGB**    | Detalhes | 3840 x 2160 px @30 fps | 1920 x 1080 px @30 fps |
| **Câmera de profundidade**  | Método   | Hora do voo | Hora do voo |
|                   | Resolução | 640 x 576 px @30 fps | 512 x 424 px @ 30 fps |
|                   |            | 512 x 512 px @30 fps |                       |
|                   |            | 1024x1024 px @15 fps |                       |
| **Conetividade** | Dados | USB3.1 Gen 1 com tipo USB-C  | USB 3.1 gen 1|
|  | Energia | PSU externo ou USB-C | PSU externo |
|  | Sincronização | RGB & Profundidade dispositivo interno e externo| RGB & Profundidade interna apenas |
| **Mecânica** | Dimensões | 103 x 39 x 126 mm | 249 x 66 x 67 mm |
|  | Missa | 440 g | 970 g |
| | Montagem | Um 1/4-20 UNC. Quatro pontos internos de parafuso | Um 1/4-20 UNC |

Encontre detalhes adicionais no documento de [hardware Azure Kinect DK.](hardware-specification.md)

## <a name="sensor-access"></a>Acesso a sensores

A tabela a seguir fornece uma comparação de capacidade de acesso a sensores de baixo nível.

| **Funcionalidade**| **Azure Kinect** | **Kinect para Windows** | **Notas** |
|---------|---------|------------|---------|
| **Profundidade** | ✔️ | ✔️ |    |   |
| **IR** | ✔️ | ✔️ |  |
| **Color** | ✔️ | ✔️ | Formato de cor suporta diferenças, Azure Kinect DK suporta estes controlos de câmara: Exposição, equilíbrio branco, brilho, contraste, saturação, nitidez e ganhar controlo |
| **Áudio** | ✔️ | ✔️ | Os microfones DK do Azure Kinect são acedidos através do Speech SDK ou da API nativa do Windows |
| **IMU** | ✔️ |  | Azure Kinect DK tem um IMU de 6 eixos completo e Kinect para windows apenas fornece 1 eixo |
| **Dados de calibração** | ✔️ | ✔️ | Calibração do modelo de câmara compatível com OpenCV |
| **Sincronização interna depth-RGB** | ✔️ | ✔️ |  |
| **Sincronização externa**| ✔️|  | Azure Kinect DK permite atraso programável para sincronização externa |
| **Partilhar acesso com vários clientes** | | ✔️ | O Azure Kinect Sensor SDK conta com winUSB/libUSB para aceder ao dispositivo e não tem um serviço implementado para permitir o acesso ao dispositivo de partilha com vários processos |
| **Ferramenta de gravação / reprodução de fluxo** | ✔️ | ✔️ | A Azure Kinect DK utiliza uma implementação baseada em contentores Matroska de código aberto |

## <a name="features"></a>Funcionalidades

O conjunto de funcionalidades Azure Kinect SDK é diferente do Kinect para Windows v2, conforme descrito abaixo:

| **Kinect v2 Recurso** | **Tipo de dados Kinect v2** | **Azure Kinect SDK/Serviço** |
|--------|--------|------|
| Acesso de dados de sensores |Quadro de Profundidade| [Sensor SDK - Recuperar imagens](retrieve-images.md) 
| |InfraredFrame | [Sensor SDK - Recuperar imagens](retrieve-images.md) 
| | ColorFrame | [Sensor SDK - Recuperar imagens](retrieve-images.md) | 
| | AudioBeamFrame |Atualmente, não é suportado 
| Rastreio do corpo | BodyFrame | SDK de rastreio de corpo |
| | BodyIndexFrame | SDK de rastreio de corpo  |
| Mapeamento de coordenadas|CoordenadaMapper| [Sensor SDK - Transformações de imagem](use-image-transformation.md) |
|Rastreio de rosto | FaceFrame | [Serviços Cognitivos: Rosto](https://azure.microsoft.com/services/cognitive-services/face/)       |
|    Reconhecimento da Fala    |    N/D                      |    [Serviços Cognitivos: Discurso](https://azure.microsoft.com/services/cognitive-services/directory/speech/)     |

## <a name="next-steps"></a>Passos seguintes

[Kinect para páginas de desenvolvedores do Windows](https://developer.microsoft.com/windows/kinect)
