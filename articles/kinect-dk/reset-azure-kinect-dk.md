---
title: Repor o Azure Kinect DK
description: Descreve como repor um dispositivo Azure Kinect DK para a imagem de fábrica
author: Teresa-Motiv
ms.author: v-tea
ms.reviewer: jarrettr
ms.prod: kinect-dk
ms.date: 02/11/2020
ms.topic: how-to
keywords: kinect, reset
ms.custom:
- CI 113492
- CSSTroubleshooting
audience: ITPro
manager: dcscontentpm
ms.localizationpriority: high
ms.openlocfilehash: f851579712a9549c88f1ca652aea4f31457da132
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/13/2020
ms.locfileid: "77201903"
---
# <a name="reset-azure-kinect-dk"></a>Repor o Azure Kinect DK

Poderá haver casos em que tem de repor o Azure Kinect DK para a imagem de fábrica (por exemplo, se uma atualização de firmware não for instalada corretamente).

1. Desligue o Azure Kinect DK. Para tal, retire o cabo USB e o cabo de alimentação.
  ![Diagrama que mostra a localização do parafuso que cobre o botão de reposição.](media/reset-azure-kinect-dk-diagram.png)
1. Para encontrar o botão de reposição, retire o parafuso que está localizado na fechadura do suporte do tripé.
1. Volte a ligar o cabo de alimentação.
1. Introduza a ponta de um clipe de papel endireitado no orifício vazio do parafuso na fechada do suporte do tripé.
1. Utilize o clipe para premir suavemente e sem largar o botão de reposição.
1. Enquanto segura o botão de reposição, volte a ligar o cabo USB.
1. Após cerca de três segundos, a luz indicadora de alimentação muda para âmbar. Depois de a luz mudar, solte o botão de reposição.  
   
   Depois de soltar o botão de reposição, a luz indicadora de alimentação pisca a branco e âmbar enquanto o dispositivo é reposto. 
1. Aguarde até que a luz indicadora fique em branco sólido.
1. Substitua o parafuso na fechadura do suporte do tripé por cima do botão de reposição.
1. Utilize o Azure Kinect Viewer para confirmar que o firmware foi reposto. Para tal, inicie o [Azure Kinect Viewer](azure-kinect-viewer.md) e selecione **Device firmware version info** (Informações da versão de firmware do dispositivo) para ver a versão do firmware instalado no seu Azure Kinect DK.

Certifique-se sempre de que tem o mais recente firmware instalado no dispositivo. Para obter a versão mais recente do firmware, utilize a Ferramenta Azure Kinect Firmware. Para obter mais informações sobre como ver o estado do firmware, veja [Verificar a versão de firmware do dispositivo](azure-kinect-firmware-tool.md#check-device-firmware-version).

## <a name="related-topics"></a>Tópicos relacionados

- [Acerca do Azure Kinect DK](about-azure-kinect-dk.md)
- [Configurar o Azure Kinect DK](set-up-azure-kinect-dk.md)
- [Especificações de hardware do Azure Kinect DK: Ambiente de funcionamento](hardware-specification.md#operating-environment)
- [Ferramenta Azure Kinect Firmware](azure-kinect-firmware-tool.md)
- [Azure Kinect Viewer](azure-kinect-viewer.md)
- [Sincronização entre vários dispositivos Azure Kinect DK](multi-camera-sync.md)
