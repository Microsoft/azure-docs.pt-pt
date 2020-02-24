---
title: Especificações de hardware do Azure Kinect DK
description: Saiba quais são os componentes, as especificações e as capacidades do Azure Kinect DK.
author: tesych
ms.author: tesych
ms.reviewer: jarrettr
ms.prod: kinect-dk
ms.date: 02/14/2020
ms.topic: article
keywords: azure, kinect, specs, hardware, DK, capabilities, depth, color, RGB, IMU, microphone, array, depth
ms.custom:
- CI 114092
- CSSTroubleshooting
audience: ITPro
manager: dcscontentpm
ms.localizationpriority: high
ms.openlocfilehash: dbe4cac64e324c1188ec5a01b4da3935a5d5b479
ms.sourcegitcommit: f255f869c1dc451fd71e0cab340af629a1b5fb6b
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/16/2020
ms.locfileid: "77371288"
---
# <a name="azure-kinect-dk-hardware-specifications"></a>Especificações de hardware do Azure Kinect DK

Este artigo mostra os detalhes de como o hardware do Azure Kinect integra a mais recente tecnologia de sensores da Microsoft num acessório USB.

![Azure Kinect DK](./media/resources/hardware-specs-media/device-wire.png)

## <a name="terms"></a>Termos

São utilizadas as seguintes abreviaturas ao longo do artigo.

- NFOV (Narrow field-of-view depth mode; Modo de profundidade de campo de visão reduzida)
- WFOV (Wide field-of-view depth mode; Modo de profundidade de campo de visão ampla)
- FOV (Field-of-view; Campo de visão)
- FPS (Frames-per-second, Fotogramas por segundo)
- IMU (Inertial Measurement Unit; Unidade de medida inercial)
- FoI (Field of Interest; Área de interesse)

## <a name="product-dimensions-and-weight"></a>Dimensões e peso do produto

O dispositivo Azure Kinect tem as seguintes dimensões de tamanho e peso.

- **Dimensões**: 103 x 39 x 126 mm
- **Peso**: 440 g

![Dimensões do Azure Kinect DK](./media/resources/hardware-specs-media/dimensions.png)

## <a name="operating-environment"></a>Ambiente de funcionamento

O Azure Kinect DK destina-se a programadores e a empresas comerciais para utilização nas seguintes condições de ambiente:

- **Temperature**: 10-25⁰C
- **Humidade**: Humidade Relativa 8-90% (sem condensação)

> [!NOTE]
> A utilização fora das condições de ambiente pode fazer com que o dispositivo falhe e/ou não funcione corretamente. Estas condições ambientais são aplicáveis ao ambiente imediatamente à volta do dispositivo em todas as condições de funcionamento. Quando utilizado com uma caixa externa, recomenda-se utilizar soluções de controlo ativo de temperatura e/ou arrefecimento, para garantir que o dispositivo permanece dentro destes intervalos. O design do dispositivo tem um canal de arrefecimento entre a secção frontal e a manga traseira. Quando implementar o dispositivo, certifique-se de que este canal de arrefecimento não está obstruído.

Veja as [informações de segurança](https://support.microsoft.com/help/4023454/safety-information) adicionais do produto.

## <a name="depth-camera-supported-operating-modes"></a>Modos de funcionamento suportados pela câmara de profundidade

O Azure Kinect DK integra uma câmara de profundidade de 1 Megapixel Time-of-Flight (ToF) concebida pela Microsoft com o [sensor de imagem apresentado na ISSCC 2018](https://docs.microsoft.com/windows/mixed-reality/ISSCC-2018). A câmara de profundidade suporta os modos indicados abaixo:

 | Modo            | Resolução | FoI       | FPS                | Intervalo de funcionamento* | Tempo de exposição |
|-----------------|------------|-----------|--------------------|------------------|---------------|
| NFOV unbinned   | 640 x 576    | 75° x 65°   | 0, 5, 15, 30       | 0,5 - 3,86 m       | 12,8 ms        |
| NFOV 2x2 binned (SW) | 320 x 288    | 75° x 65°   | 0, 5, 15, 30       | 0,5 - 5,46 m       | 12,8 ms        |
| WFOV 2x2 binned | 512 x 512    | 120° x 120° | 0, 5, 15, 30       | 0,25 - 2,88 m      | 12,8 ms        |
| WFOV unbinned   | 1024 x 1024  | 120° x 120° | 0, 5, 15           | 0,25 - 2,21 m      | 20,3 ms        |
| IV Passivo      | 1024 x 1024  | N/D       | 0, 5, 15, 30       | N/D              | 1,6 ms         |

\*15% a 95% de refletividade a 850nm, 2,2 μW/cm<sup>2</sup>/nm, desvio padrão de erro aleatório. ≤ 17 mm, erro sistemático típico < 11 mm + 0,1% de distância sem interferência de propagação por vários caminhos. Profundidade fornecida para além do intervalo indicado dependendo da refletividade do objeto.

## <a name="color-camera-supported-operating-modes"></a>Modos de funcionamento suportados pela câmara de cor

O Azure Kinect DK inclui um sensor OV12A10 12MP CMOS de obturador rotativo. Os modos de funcionamento nativos estão indicados abaixo:

|             Resolução da câmara RGB (HxV)  |          Proporção  |          Opções de Formato   |          Taxas de Fotogramas (FPS)  |          FOV nominal (HxV) (pós-processado)  |
|------------------------------------------|------------------------|---------------------------|-----------------------------|---------------------------------------------|
|       3840 x 2160                          |          16:9          |          MJPEG            |          0, 5, 15, 30       |          90° x 59°                              |
|       2560 x 1440                          |          16:9          |          MJPEG            |          0, 5, 15, 30       |          90° x 59°                              |
|       1920 x 1080                          |          16:9          |          MJPEG            |          0, 5, 15, 30       |          90° x 59°                              |
|       1280 x 720                           |          16:9          |          MJPEG/YUY2/NV12  |          0, 5, 15, 30       |          90° x 59°                              |
|       4096 x 3072                          |          4:3           |          MJPEG             |          0, 5, 15           |          90° x 74,3°                            |
|       2048 x 1536                          |          4:3           |          MJPEG             |          0, 5, 15, 30       |          90° x 74,3°                            |

A câmara RGB é compatível com a classe USB Video e pode ser utilizada sem o Sensor SDK. O espaço de cores da câmara RGB: BT.601 gama completa [0..255]. 

> [!NOTE]
> O Sensor SDK pode fornecer imagens a cores no formato de píxeis BGRA. Este não é um modo nativo suportado pelo dispositivo e provoca uma carga adicional na CPU quando é utilizado. A CPU anfitriã é utilizada para converter de imagens MJPEG recebidas do dispositivo.

## <a name="rgb-camera-exposure-time-values"></a>Valores de tempo de exposição da câmara RGB

O mapa abaixo mostra os valores de exposição manual aceites pela câmara RGB:

| exp| 2^exp | 50Hz   |60Hz    |
|----|-------|--------|--------|
| -11|     488|    500|    500 |
| -10|     977|   1250|   1250 |
|  -9|    1953|   2500|   2500 |
|  -8|    3906|  10000|   8330 |
|  -7|    7813|  20000|  16670 |
|  -6|   15625|  30000|  33330 |
|  -5|   31250|  40000|  41670 |
|  -4|   62500|  50000|  50000 |
|  -3|  125000|  60000|  66670 |
|  -2|  250000|  80000|  83330 |
|  -1|  500000| 100000| 100000 |
|   0| 1000000| 120000| 116670 |
|   1| 2000000| 130000| 133330 |

## <a name="depth-sensor-raw-timing"></a>Tempo bruto do sensor de profundidade

Modo de Profundidade | IR <br>Pulsações | Largura da <br>Pulsação  | Períodos <br>Inativos| Tempo de Inatividade | Exposição <br> Hora
-|-|-|-|-|-
NFOV Unbinned <br>  NFOV 2xx Binned <br> WFOV 2x2 Binned | 9 | 125 us | 8 | 1450 us | 12,8 ms 
WFOV Unbinned                                            | 9 | 125 us | 8 | 2390 us | 20,3 ms


## <a name="camera-field-of-view"></a>Campo de visão da câmara

A imagem seguinte mostra a profundidade e o campo de visão da câmara RGB, ou os ângulos que os sensores "veem". Este diagrama mostra a câmara RGB no modo 4:3.

![Câmara FOV](./media/resources/hardware-specs-media/camera-fov.png)

Esta imagem demonstra o campo de visão da câmara visto da frente a uma distância de 2000 mm.

![Frente da Câmara FOV](./media/resources/hardware-specs-media/fov-front.png)

> [!NOTE]
> Quando a profundidade está no modo NFOV, a câmara RGB tem uma sobreposição de píxeis melhor em 4:3 do que em resoluções 16:9.

## <a name="motion-sensor-imu"></a>Sensor de movimento (IMU)

A Unidade de Medida Inercial (Inertial Measurement Unit, IMU) incorporada é uma LSM6DSMUS e inclui um acelerómetro e um giroscópio. A taxa do acelerómetro e do giroscópio é de 1,6 kHz simultaneamente. As taxas são comunicadas ao anfitrião a 208 Hz.

## <a name="microphone-array"></a>Matriz do microfone

O Azure Kinect DK tem incorporada uma matriz circular de microfone de sete canais e alta qualidade que se identifica como um dispositivo padrão de áudio USB de classe 2.0. É possível aceder a todos os sete canais. As especificações de desempenho são:

- Sensibilidade: -22 dBFS (94 dB SPL, 1 kHz)
- Relação sinal/ruído > 65 dB
- Ponto de sobrecarga acústica: 116 dB

![Proteção do microfone](./media/resources/hardware-specs-media/mic-bubble.png)

## <a name="usb"></a>USB

O Azure Kinect DK é um dispositivo composto USB3 que expõe os seguintes pontos finais de hardware ao sistema operativo:

O ID de fornecedor é 0x045E (Microsoft); tabela de IDs de Produto abaixo:

|    Interface USB        |    IP PNP    |     Notas            |
|-------------------------|--------------|----------------------|
|    Hub USB3.1 Gen1    |    0x097A    |    O hub principal    |
|    Hub USB2.0         |    0x097B    |    HS USB          |
|    Câmara de profundidade       |    0x097C    |    USB3.0            |
|    Câmara de cor       |    0x097D    |    USB3.0            |
|    Microfones        |    0x097E    |    HS USB          |

## <a name="indicators"></a>Indicadores

A frente do dispositivo inclui um indicador de transmissão em fluxo da câmara que pode ser desativado programaticamente com o Sensor SDK.

O LED de estado na parte de trás do dispositivo indica o estado do mesmo:

| Quando a luz está:     | Significa que                                                   |
|-----------------------|------------------------------------------------------------|
| Branco sólido           | O dispositivo está ligado e a funcionar corretamente.                         |
| Branco intermitente        | O dispositivo está ligado, mas não tem uma ligação de dados USB 3.0.   |
| Âmbar intermitente        | O dispositivo não tem energia suficiente para funcionar.               |
| Âmbar e branco intermitente  | Está em curso uma atualização de firmware ou uma recuperação.                    |

## <a name="power-device"></a>Alimentação do dispositivo

O dispositivo pode ser alimentado de duas formas:

1. Com a fonte de alimentação incluída na caixa. Os dados são ligados por um cabo USB Tipo C para Tipo A em separado.
2. Com um cabo Tipo C para Tipo C, para alimentação e para dados.

O cabo Tipo C para Tipo C não está incluído no Azure Kinect DK.

> [!NOTE]
> - O cabo de alimentação da caixa é um conector USB Tipo A para tambor único. Utilize a fonte de alimentação de parede fornecida com este cabo. O dispositivo é capaz de obter mais energia desta forma do que se forem utilizadas duas portas USB Tipo A padrão.
> - Os cabos USB são importantes e recomendamos utilizar cabos de alta qualidade e confirmar que estão a funcionar antes de implementar a unidade remotamente.

> [!TIP]
> Para selecionar um bom cabo Tipo C para Tipo C:
> - O [cabo USB certificado](https://www.usb.org/products) tem de suportar alimentação e dados.
> - Os cabos passivos devem ter menos de 1,5 m de comprimento. Se forem maiores, utilize um cabo ativo. 
> - O cabo tem de suportar pelo menos >1,5A. Caso contrário, tem de ligar uma fonte de alimentação externa.

Verificar o cabo:

- Ligue o dispositivo através do cabo ao PC anfitrião.
- Confirme que todos os dispositivos são enumerados corretamente no gestor de dispositivos do Windows. As câmaras de profundidade e RGB devem aparecer conforme ilustrado no exemplo abaixo.

  ![O Azure Kinect DK no Gestor de Dispositivos](./media/resources/hardware-specs-media/device-manager.png)

- Confirme que o cabo consegue transmitir fiavelmente em todos os sensores no Azure Kinect Viewer, com as definições seguintes:

  - Câmara de profundidade: NFOV unbinned
  - Câmara RGB: 2160p
  - Microfones e IMU ativados

## <a name="what-does-the-light-mean"></a>O que significa a luz?

O indicador de alimentação é um LED na parte de trás do Azure Kinect DK. A cor do LED muda consoante o estado do dispositivo.

![A imagem mostra a parte de trás do Azure Kinect DK. Há três notas numeradas; uma para um indicador LED e, abaixo, duas para os cabos.](./media/quickstarts/azure-kinect-dk-power-indicator.png)

Esta figura ilustra os componentes seguintes:

1. Indicador de alimentação
1. Cabo de alimentação (ligado à fonte de alimentação)
1. Cabo de dados USB-C (ligado ao PC)

Certifique-se de que os cabos estão ligados conforme mostrado. Em seguida, veja a tabela abaixo para saber o significado dos vários estados da luz de alimentação.

|Quando a luz está: |Significa que: |E que deve: |
| ---| --- | --- |
|Branco sólido |O dispositivo está ligado e a funcionar corretamente. |Utilizar o dispositivo. |
|Desligada |O dispositivo não está ligado ao PC. |Confirmar que o cabo conector redondo de alimentação está ligado ao dispositivo e adaptador de alimentação USB.<br /><br />Verificar se o cabo USB-C está ligado ao dispositivo e ao PC. |
|Branco intermitente |O dispositivo está ligado, mas não tem uma ligação de dados USB 3.0. |Confirmar que o cabo conector redondo de alimentação está ligado ao dispositivo e adaptador de alimentação USB.<br /><br />Verificar se o cabo USB-C está ligado ao dispositivo e a uma porta USB 3.0 no PC.<br /><br />Ligar o dispositivo a outra porta USB 3.0 no PC.<br /><br />No PC, abra o Gestor de Dispositivos (**Iniciar** > **Painel de Controlo** > **Gestor de Dispositivos**) e verifique se o PC tem um controlador de anfitrião USB 3.0 suportado. |
|Âmbar intermitente |O dispositivo não tem energia suficiente para funcionar. |Confirmar que o cabo conector redondo de alimentação está ligado ao dispositivo e adaptador de alimentação USB.<br /><br />Verificar se o cabo USB-C está ligado ao dispositivo e ao PC. |
|Âmbar e, depois, branco intermitente |O dispositivo está ligado e a receber uma atualização de firmware ou está a restaurar as definições de fábrica. |Aguarde até que a luz indicadora fique em branco sólido. Para obter mais informações, veja [Repor o Azure Kinect DK](reset-azure-kinect-dk.md). |

## <a name="power-consumption"></a>Consumo energético

O Azure Kinect DK consome até 5,9 W. O consumo energético específico depende de cada tipo de utilização.

## <a name="calibration"></a>Calibração

O Azure Kinect DK é calibrado na fábrica. Os parâmetros de calibração dos sensores visuais e inerciais podem ser consultados programaticamente através do Sensor SDK.

## <a name="device-recovery"></a>Recuperação do dispositivo

O firmware do dispositivo pode ser reposto para o original através do botão abaixo do parafuso de bloqueio.

![Botão de recuperação do Azure Kinect DK](./media/resources/hardware-specs-media/recovery.png)

Para recuperar o dispositivo, veja [estas instruções](reset-azure-kinect-dk.md).

## <a name="next-steps"></a>Passos seguintes

- [Utilizar o Azure Kinect Sensor SDK](about-sensor-sdk.md)
- [Configurar o hardware](set-up-azure-kinect-dk.md)
