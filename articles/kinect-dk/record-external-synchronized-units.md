---
title: Utilize gravador Azure Kinect com dispositivos sincronizados externos
description: Saiba como gravar dados de dispositivos configurados para sincronização externa utilizando o Gravador Azure Kinect.
author: tesych
ms.author: tesych
ms.reviewer: jawirth
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: Kinect, sensor, espectador, sincronização externa, atraso de fase, profundidade, RGB, câmera, cabo de áudio, gravador
ms.openlocfilehash: 052f6f1ac9f90e764de25d1d4d1b25b3d50a848d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85277471"
---
# <a name="use-azure-kinect-recorder-with-external-synchronized-devices"></a>Utilize gravador Azure Kinect com dispositivos sincronizados externos

Este artigo fornece orientações sobre como o [Gravador Azure Kinect](azure-kinect-recorder.md) pode gravar dispositivos de sincronização externa de dados configurados.

## <a name="prerequisites"></a>Pré-requisitos

- [Confiúdlo de várias unidades de DK Azure Kinect para sincronização externa](https://support.microsoft.com/help/4494429).

## <a name="external-synchronization-constraints"></a>Restrições externas de sincronização

- O dispositivo principal não pode ter o cabo SYNC IN ligado.
- O dispositivo principal deve transmitir a câmara RGB para permitir a sincronização.
- Todas as unidades devem utilizar a mesma configuração da câmara (framerate e resolução).
- Todas as unidades devem executar o mesmo firmware do dispositivo (atualizar instruções[de firmware).](update-device-firmware.md)
- Todos os dispositivos subordinados devem ser iniciados antes do dispositivo principal.
- O mesmo valor de exposição deve ser fixado em todos os dispositivos.
- Cada configuração *principal* de atraso de cada subordinado é relativa ao dispositivo principal.

## <a name="record-when-each-unit-has-a-host-pc"></a>Registre quando cada unidade tem um PC anfitrião

No exemplo abaixo, cada dispositivo tem o seu próprio PC de anfitrião dedicado.
Recomenda-se que conecte os dispositivos a computadores dedicados para evitar problemas com largura de banda USB e utilização de CPU/GPU.

### <a name="subordinate-1"></a>Subordinado-1

1. Configurar o gravador para a primeira unidade

      `k4arecorder.exe --external-sync sub -e -8 -r 5 -l 10 sub1.mkv`

2. Dispositivo começa a esperar

    ```console
    Device serial number: 000011590212
    Device version: Rel; C: 1.5.78; D: 1.5.60[6109.6109]; A: 1.5.13
    Device started
    [subordinate mode] Waiting for signal from master
    ```

### <a name="subordinate-2"></a>Subordinado-2

1. Configurar o gravador para a segunda unidade

    `k4arecorder.exe --external-sync sub -e -8 -r 5 -l 10 sub2.mkv`

2. Dispositivo começa a esperar

    ```console
    Device serial number: 000011590212
    Device version: Rel; C: 1.5.78; D: 1.5.60[6109.6109]; A: 1.5.13
    Device started
    [subordinate mode] Waiting for signal from master
    ```

### <a name="master"></a>Principal

1. Comece a gravar no master

    `>k4arecorder.exe --external-sync master -e -8 -r 5 -l 10 master.mkv`

2. Aguarde até que a gravação termine.

## <a name="recording-when-multiple-units-connected-to-single-host-pc"></a>Gravação quando várias unidades ligadas a um único PC anfitrião

Pode ter vários DKs Azure Kinect ligados a um único PC de anfitrião. No entanto, isso pode ser muito exigente para a largura de banda USB e para o cálculo do anfitrião. Para reduzir a procura:

- Ligue cada dispositivo ao próprio controlador de hospedeiro USB.
- Tenha uma GPU potente que possa manusear o motor de profundidade para cada dispositivo.
- Grave apenas sensores necessários e use framerate inferior.

Comece sempre os dispositivos subordinados primeiro e o mestre em último.

## <a name="subordinate-1"></a>Subordinado-1

1. Inicie o gravador em subordinado

    `>k4arecorder.exe --device 1 --external-sync subordinate --imu OFF -e -8 -r 5 -l 5 output-2.mkv`

2. O dispositivo entra em estado de espera.

## <a name="master"></a>Principal

1. Iniciar dispositivo principal

    `>k4arecorder.exe --device 0 --external-sync master --imu OFF -e -8 -r 5 -l 5 output-1.mkv`

2. Aguarde a gravação para terminar

## <a name="playing-recording"></a>Reprodução de gravações

Pode utilizar o [espectador Azure Kinect](azure-kinect-viewer.md) para reproduzir a gravação.



## <a name="tips"></a>Sugestões

- Utilize exposição manual para gravar câmaras sincronizadas. A autoexposição da câmara RGB pode ter impacto na sincronização do tempo.
- Reiniciar o dispositivo subordinado fará com que a sincronização se perca.
- Alguns [modos de câmara](hardware-specification.md#depth-camera-supported-operating-modes) suportam 15 fps no máximo. Recomendamos que não misture modos/taxas de fotogramas entre dispositivos
- Ligar várias unidades a um único PC pode facilmente saturar a largura de banda USB, considerar a utilização de PC de hospedeiro separado por dispositivo. Preste também atenção ao cálculo cpu/GPU.
- Desative o microfone e a IMU se não forem necessários para melhorar a fiabilidade.

Para qualquer problema ver [resolução de problemas](troubleshooting.md)

## <a name="see-also"></a>Consulte também

- [Configurar sincronização externa](https://support.microsoft.com/help/4494429/sync-multiple-devices)
- [Gravador Azure Kinect](azure-kinect-recorder.md) para configurações de gravadores e informações adicionais.
- [Azure Kinect Viewer](azure-kinect-viewer.md) para reproduzir gravações ou definir propriedades de câmara RGB não disponíveis através de gravador.
- [Ferramenta de firmware Azure Kinect](azure-kinect-firmware-tool.md) para atualizar firmware do dispositivo.
