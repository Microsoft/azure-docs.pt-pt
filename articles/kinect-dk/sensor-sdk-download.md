---
title: Download SDK do sensor Azure Kinect
description: Saiba como descarregar e instalar o Sensor Azure Kinect SDK no Windows e Linux.
author: Brent-A
ms.author: brenta
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: azure, kinect,sdk, download update, mais recente, disponível, instalar
ms.openlocfilehash: 48cdd35a80d68a7ec0d900639c0fca3a2c66787e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86171793"
---
# <a name="azure-kinect-sensor-sdk-download"></a>Download SDK do sensor Azure Kinect

Esta página tem os links de descarregamento para cada versão do Sensor Kinect SDK Azure. O instalador fornece todos os ficheiros necessários para desenvolver para o Azure Kinect.

## <a name="azure-kinect-sensor-sdk-contents"></a>Conteúdo SDK do sensor Azure Kinect

- Cabeçalhos e bibliotecas para construir uma aplicação utilizando o Azure Kinect DK.
- DLLs redistribuíveis necessários por aplicações utilizando o Azure Kinect DK.
- O [Espectador Azure Kinect.](azure-kinect-viewer.md)
- O [gravador Azure Kinect](azure-kinect-recorder.md).
- A [ferramenta de firmware Azure Kinect](azure-kinect-firmware-tool.md).

## <a name="windows-download-link"></a>Link de descarregamento do Windows

[Instalador da](https://download.microsoft.com/download/3/d/6/3d6d9e99-a251-4cf3-8c6a-8e108e960b4b/Azure%20Kinect%20SDK%201.4.1.exe)  |  Microsoft [Código fonte GitHub](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/issues/1093)

> [!NOTE]
> Ao instalar o SDK, lembre-se do caminho a que se instala. Por exemplo, "C:\Program Files\Azure Kinect SDK 1.2". Encontrará as ferramentas referenciadas em artigos neste caminho.

Pode encontrar versões anteriores do Sensor Azure Kinect SDK e Firmware no [GitHub.](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/blob/develop/docs/usage.md)

## <a name="linux-installation-instructions"></a>Instruções de instalação do Linux

Atualmente, a única distribuição suportada é ubuntu 18.04. Para solicitar apoio a outras distribuições, consulte [esta página.](https://aka.ms/azurekinectfeedback)

Em primeiro lugar, terá de configurar o [Repositório de Pacotes da Microsoft,](https://packages.microsoft.com/)seguindo as instruções [aqui](https://docs.microsoft.com/windows-server/administration/linux-package-repository-for-microsoft-software).

Agora, pode instalar os pacotes necessários. O `k4a-tools` pacote inclui o [Azure Kinect Viewer,](azure-kinect-viewer.md)o [Azure Kinect Recorder](record-sensor-streams-file.md)e a [Azure Kinect Firmware Tool](azure-kinect-firmware-tool.md). Para instalá-lo, executar

 `sudo apt install k4a-tools`

 O `libk4a<major>.<minor>-dev` pacote contém os cabeçalhos e os ficheiros CMake para construir contra `libk4a` .
O `libk4a<major>.<minor>` pacote contém os objetos partilhados necessários para executar executáveis que dependem de `libk4a` .

 Os tutoriais básicos requerem o `libk4a<major>.<minor>-dev` pacote. Para instalá-lo, executar

 `sudo apt install libk4a1.1-dev`

Se o comando for bem sucedido, o SDK está pronto a ser utilizado.

## <a name="change-log-and-older-versions"></a>Alterar log e versões mais antigas

Pode encontrar o registo de alteração do Sensor Azure Kinect SDK [aqui](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/blob/develop/CHANGELOG.md).

Se precisar de uma versão mais antiga do Sensor Kinect Azure SDK, encontre-o [aqui](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/blob/develop/docs/usage.md).

## <a name="next-steps"></a>Passos seguintes

[Configurar o Azure Kinect DK](set-up-azure-kinect-dk.md)
