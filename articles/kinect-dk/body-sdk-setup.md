---
title: Quickstart - Configurar o rastreio do corpo de Azure Kinect
description: Neste arranque rápido, você vai configurar o corpo rastreando SDK para Azure Kinect.
author: qm13
ms.author: quentinm
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: quickstart
keywords: kinect, azul, sensor, acesso, profundidade, sdk, corpo, rastreamento, articulação, configuração, cuda, nvidia
ms.openlocfilehash: 2cf4c1097730f88fc4bd66c28e1bdddd7fea8640
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/05/2020
ms.locfileid: "85277985"
---
# <a name="quickstart-set-up-azure-kinect-body-tracking"></a>Quickstart: Configurar o rastreio do corpo de Azure Kinect

Este quickstart irá guiá-lo através do processo de fazer o rastreio do corpo funcionar no seu Azure Kinect DK.

## <a name="system-requirements"></a>Requisitos de sistema

O Body Tracking SDK requer um GPU NVIDIA instalado no PC anfitrião. O requisito recomendado para o pc do anfitrião de rastreio do corpo é descrito na página [de requisitos do sistema.](system-requirements.md)

## <a name="install-software"></a>Instalar software

### <a name="install-the-latest-nvidia-driver"></a>[Instale o mais recente controlador NVIDIA](https://www.nvidia.com/Download/index.aspx?lang=en-us)

Descarregue e instale o mais recente controlador NVIDIA para a sua placa gráfica. Os condutores mais velhos podem não ser compatíveis com os binários CUDA redistribuídos com o SDK de localização corporal.

### <a name="visual-c-redistributable-for-visual-studio-2015"></a>[Visual C++ Redistributable para Visual Studio 2015](https://www.microsoft.com/en-us/download/details.aspx?id=48145)

Descarregue e instale Visual C++ Redistributable para Visual Studio 2015. 

## <a name="set-up-hardware"></a>Configurar hardware

### <a name="set-up-azure-kinect-dk"></a>[Configurar o Azure Kinect DK](set-up-azure-kinect-dk.md)

Lance o [Azure Kinect Viewer](azure-kinect-viewer.md) para verificar se o seu Azure Kinect DK está configurado corretamente.

## <a name="download-the-body-tracking-sdk"></a>Transferir o Body Tracking SDK
 
1. Selecione o link para [descarregar o Body Tracking SDK](body-sdk-download.md)
2. Instale o Body Tracking SDK no seu PC.

## <a name="verify-body-tracking"></a>Verificar o rastreio do corpo

Lance o **Azure Kinect Body Tracking Viewer** para verificar se o SDK de rastreio do corpo está configurado corretamente. O espectador está instalado com o instalador SDK MSI. Pode encontrá-lo no menu inicial ou em `<SDK Installation Path>\tools\k4abt_simple_3d_viewer.exe` .

Se não tiver uma GPU suficientemente poderosa e ainda quiser testar o resultado, pode lançar o **Azure Kinect Body Tracking Viewer** na linha de comando pelo seguinte comando: `<SDK Installation Path>\tools\k4abt_simple_3d_viewer.exe CPU`

Se tudo estiver configurado corretamente, deve aparecer uma janela com uma nuvem de ponto 3D e corpos rastreados.


![Espectador 3D de rastreio do corpo](./media/quickstarts/samples-simple3dviewer.png)

## <a name="examples"></a>Exemplos

Pode encontrar os exemplos sobre como usar o corpo que rastreia o SDK [aqui.](https://github.com/microsoft/Azure-Kinect-Samples/tree/master/body-tracking-samples)

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
>[Criar a sua primeira aplicação de deteção de movimentos](build-first-body-app.md)

