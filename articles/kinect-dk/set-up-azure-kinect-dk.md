---
title: Início Rápido – Configurar o Azure Kinect DK
description: Este início rápido disponibiliza instruções para configurar o hardware do Azure Kinect DK
author: tesych
ms.author: tesych
ms.prod: kinect-dk
ms.topic: quickstart
ms.date: 02/12/2020
keywords: azure, kinect, dev kit, azure dk, set up, hardware, quick, usb, power, viewer, sensor, streaming, setup, SDK, firmware
ms.custom:
- CI 114092
- CSSTroubleshooting
audience: ITPro
manager: dcscontentpm
ms.localizationpriority: high
ms.openlocfilehash: b33565f319de77385c966f742fa52391f4869eed
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/14/2020
ms.locfileid: "77211283"
---
# <a name="quickstart-set-up-your-azure-kinect-dk"></a>Início Rápido: Configurar o Azure Kinect DK

Este início rápido disponibiliza orientações para configurar o seu Azure Kinect DK. Vamos mostrar-lhe como testar a visualização do fluxo de sensores e utilizar o [Azure Kinect Viewer](azure-kinect-viewer.md).

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="system-requirements"></a>Requisitos de sistema

Verifique os [Requisitos de sistema](system-requirements.md) para confirmar que a configuração do PC anfitrião cumpre todos os requisitos mínimos do Azure Kinect DK.

## <a name="set-up-hardware"></a>Configurar o hardware

> [!NOTE]
> Antes de utilizar o dispositivo, confirme que retira a película protetora da câmara.

1. Ligue o cabo de alimentação à entrada de alimentação na parte de trás do dispositivo. Ligue o adaptador de alimentação USB à outra extremidade do cabo e ligue o adaptador a uma tomada.
2. Ligue o cabo de dados USB ao dispositivo e a uma porta USB 3.0 no PC.
   >[!NOTE]
   >Para obter os melhores resultados, ligue o cabo diretamente ao dispositivo e ao PC. Evite utilizar extensões ou adaptadores extra na ligação.

3. Verifique se o LED indicador de alimentação (junto ao cabo USB) aparece a branco sólido.
  
   A alimentação do dispositivo leva alguns segundos. O dispositivo está pronto a ser utilizado quando o indicador de transmissão LED frontal se desligar.  

   Para obter mais informações sobre o LED indicador de alimentação, veja [O que é que a luz significa?](hardware-specification.md#what-does-the-light-mean)

    ![Funcionalidades completas do dispositivo](./media/quickstarts/full-device-features.png)

## <a name="download-the-sdk"></a>Transferir o SDK

1. Selecione a ligação para [Transferir o SDK](sensor-sdk-download.md).
2. Instale o SDK no PC.

## <a name="update-firmware"></a>Atualizar o Firmware

Para funcionar corretamente, o SDK requer a versão mais recente do firmware do dispositivo. Para verificar e atualizar a versão do firmware, siga os passos em [Atualizar Firmware do Azure Kinect DK](update-device-firmware.md).

## <a name="verify-that-the-device-streams-data"></a>Confirmar que o dispositivo transmite dados

1. Inicie o [Azure Kinect Viewer](azure-kinect-viewer.md). Pode utilizar um dos métodos abaixo para o iniciar:
   - Pode iniciar o visualizador na linha de comandos ou ao fazer duplo clique no ficheiro executável. O ficheiro, `k4aviewer.exe`, reside no diretório de ferramentas do SDK (por exemplo, `C:\Program Files\Azure Kinect SDK vX.Y.Z\tools\k4aviewer.exe`, em que `X.Y.Z` é a versão instalada do SDK).
   - Pode iniciar o Azure Kinect Viewer a partir do menu **Start** (Iniciar) do dispositivo.
2. No Azure Kinect Viewer, selecione **Open Device** (Abrir Dispositivo) > **Start** (Iniciar)

    ![Azure Kinect Viewer](./media/quickstarts/viewer.png)

3. Confirme que a ferramenta visualiza a transmissão de cada sensor:
   - Câmara de profundidade
   - Câmara de cor
   - Câmara de infravermelhos
   - IMU
   - Microfones

    ![Ferramenta de Visualização](./media/quickstarts/visualization-tool.png)

Concluiu a configuração do Azure Kinect DK. Agora, pode começar a desenvolver a sua aplicação ou a integrar serviços.

Se tiver algum problema, veja a [Resolução de problemas](troubleshooting.md).

## <a name="see-also"></a>Consulte também

- [Informações de hardware do Azure Kinect DK](hardware-specification.md)
- [Atualizar o firmware do dispositivo](update-device-firmware.md)
- Saiba mais sobre o [Azure Kinect Viewer](azure-kinect-viewer.md)

## <a name="next-steps"></a>Passos seguintes

Quando o Azure Kinect DK estiver pronto e a funcionar, pode também aprender a
> [!div class="nextstepaction"]
> [Registar as transmissões dos sensores num ficheiro](record-sensor-streams-file.md)
