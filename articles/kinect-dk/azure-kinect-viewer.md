---
title: Espectador Azure Kinect
description: Compreenda como visualizar todos os fluxos de dados do dispositivo utilizando o visualizador Azure Kinect.
author: tesych
ms.author: tesych
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: azul, cinect, sensor, espectador, visualização, profundidade, rgb, cor, imu, áudio, microfone, nuvem de ponto
ms.openlocfilehash: f411de5854d516586dc64701cca729c692ce40e0
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "92166110"
---
# <a name="azure-kinect-viewer"></a>Espectador Azure Kinect

O Azure Kinect Viewer, encontrado sob o diretório de ferramentas instalados como `k4aviewer.exe` (por exemplo, `C:\Program Files\Azure Kinect SDK vX.Y.Z\tools\k4aviewer.exe` onde está a versão instalada do `X.Y.Z` SDK), pode ser usado para visualizar todos os fluxos de dados do dispositivo para:

* Verifique se os sensores estão a funcionar corretamente.
* Ajude a posicionar o dispositivo.
* Experimente as definições das câmaras.
* Leia a configuração do dispositivo.
* Gravações de reprodução feitas com [gravador Azure Kinect](azure-kinect-recorder.md).

Para obter mais informações sobre o espectador Azure Kinect, veja [Como usar o vídeo de Azure Kinect](https://www.microsoft.com/videoplayer/embed/RE3hNwG).

O Azure Kinect Viewer é [de código aberto](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/tree/develop/tools/k4aviewer) e pode ser usado como um exemplo para como usar as APIs.

## <a name="use-viewer"></a>Use o espectador

O espectador pode funcionar em dois modos: com dados ao vivo do sensor ou a partir de dados gravados[(Azure Kinect Recorder).](azure-kinect-recorder.md)

### <a name="start-application"></a>Iniciar aplicação

Lançar a aplicação em execução `k4aviewer.exe` .

![Screenshot que mostra a aplicação do espectador lançado.](./media/how-to-guides/open-viewer.png)

### <a name="use-the-viewer-with-live-data"></a>Use o espectador com dados ao vivo

1. Na secção **Dispositivo Aberto,** selecione o **Número de Série** do dispositivo para abrir. Em seguida, **selecione Refresh**, se o dispositivo estiver em falta.
2. Selecione o botão **Dispositivo Aberto.**
3. Selecione **Começar** a transmitir dados com as definições predefinidos.

### <a name="use-the-viewer-with-recorded-data"></a>Utilize o espectador com dados gravados

Na secção **Gravação Aberta,** navegue para o ficheiro gravado e selecione-o.

## <a name="check-device-firmware-version"></a>Verifique a versão do firmware do dispositivo

Aceda à versão do firmware do dispositivo na janela de configuração, como mostra a seguinte imagem.

![Verificação da versão do firmware do dispositivo utilizando o espectador](./media/how-to-guides/check-firmware-update.png)

Por exemplo, neste caso, a câmara de profundidade ISP está a executar FW 1.5.63.

## <a name="depth-camera"></a>Câmara de profundidade

O espectador de câmara de profundidade mostrará duas janelas:

* Um chama-se *Ative Brightness* que é uma imagem em tons de cinza que mostra brilho ir.
* A segunda chama-se *Depth,* que tem uma representação colorida dos dados de profundidade.

Passe o cursor, na janela de profundidade, para ver o valor do sensor de profundidade, como mostrado abaixo.

![Vista de profundidade](./media/how-to-guides/depth-camera.png)

## <a name="rgb-camera"></a>Câmera RGB

A imagem abaixo mostra a vista da câmara a cores.

![Vista RGB](./media/how-to-guides/viewer-rgb-camera.png)

Pode controlar as definições da câmara RGB a partir da janela de configuração durante o streaming.

![Controlos de câmara rGB](./media/how-to-guides/rgb-camera-settings.png)

## <a name="inertial-measurement-unit-imu"></a>Unidade de Medição por Inércia (IMU)

A janela IMU tem dois componentes, um acelerómetro e um giroscópio.

A metade superior é o acelerómetro e mostra aceleração linear nos contadores/segundo<sup>2</sup>.  Inclui aceleração da gravidade, por isso, se estiver deitado sobre uma mesa, o eixo Z provavelmente mostrará cerca de -9,8 m/s<sup>2</sup>.

A metade inferior é a porção do giroscópio e mostra movimento rotativo em radians/segundo

![Vista do sensor de movimento](./media/how-to-guides/viewer-mu-settings.png)

## <a name="microphone-input"></a>Entrada do microfone

A visualização do microfone mostra uma representação do som ouvido em cada microfone. Se não houver som, o gráfico é mostrado como vazio, caso contrário, verá uma forma de onda azul escura com uma forma de onda azul clara sobreposta em cima.

A onda escura representa os valores mínimos e máximos observados pelo microfone ao longo dessa rodela temporal. A onda luminosa representa o quadrado médio raiz dos valores observados pelo microfone ao longo dessa rodela temporal.

![Vista de entrada do microfone](./media/how-to-guides/microphone-data.png)

## <a name="point-cloud-visualization"></a>Visualização da nuvem de ponto

A profundidade visualizada em 3D permite-lhe mover-se na imagem utilizando teclas instruídas.

![Nuvem de ponto de profundidade](./media/how-to-guides/depth-point-cloud.png)

## <a name="synchronization-control"></a>Controlo de sincronização

Pode utilizar o espectador para configurar o dispositivo como modo autónomo (predefinido), mestre ou subordinado ao configurar a sincronização de vários dispositivos.
Ao alterar a configuração ou inserir/remover o cabo de sincronização, selecione **Refresh** para atualizar.

![Controlo de sincronização externa](./media/how-to-guides/sync-control.png)

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
>[Guia de configuração de sincronização externa](https://support.microsoft.com/help/4494429/sync-multiple-azure-kinect-dk-devices)
