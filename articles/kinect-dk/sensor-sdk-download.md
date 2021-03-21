---
title: Download SDK do sensor Azure Kinect
description: Saiba como descarregar e instalar o Sensor Azure Kinect SDK no Windows e Linux.
author: Brent-A
ms.author: brenta
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: azure, kinect,sdk, download update, mais recente, disponível, instalar
ms.openlocfilehash: 591fcba4c887e298cf667c5d95c19184bc213ffe
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102179634"
---
# <a name="azure-kinect-sensor-sdk-download"></a>Download SDK do sensor Azure Kinect

Esta página tem os links de descarregamento para cada versão do Sensor Kinect SDK Azure. O instalador fornece todos os ficheiros necessários para desenvolver para o Azure Kinect.

## <a name="azure-kinect-sensor-sdk-contents"></a>Conteúdo SDK do sensor Azure Kinect

- Cabeçalhos e bibliotecas para construir uma aplicação utilizando o Azure Kinect DK.
- DLLs redistribuíveis necessários por aplicações utilizando o Azure Kinect DK.
- O [Espectador Azure Kinect.](azure-kinect-viewer.md)
- O [gravador Azure Kinect](azure-kinect-recorder.md).
- A [ferramenta de firmware Azure Kinect](azure-kinect-firmware-tool.md).

## <a name="windows-installation-instructions"></a>Instruções de instalação do Windows

Pode encontrar detalhes de instalação para as versões mais recentes e anteriores do Sensor Azure Kinect SDK e Firmware [aqui.](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/blob/develop/docs/usage.md)

Pode encontrar o código fonte [aqui.](https://github.com/microsoft/Azure-Kinect-Sensor-SDK)

> [!NOTE]
> Ao instalar o SDK, lembre-se do caminho a que se instala. Por exemplo, "C:\Program Files\Azure Kinect SDK 1.2". Encontrará as ferramentas referenciadas em artigos neste caminho.

## <a name="linux-installation-instructions"></a>Instruções de instalação do Linux

Atualmente, a única distribuição suportada é ubuntu 18.04. Para solicitar apoio a outras distribuições, consulte [esta página.](https://aka.ms/azurekinectfeedback)

Em primeiro lugar, terá de configurar o [Repositório de Pacotes da Microsoft,](https://packages.microsoft.com/)seguindo as instruções [aqui](/windows-server/administration/linux-package-repository-for-microsoft-software).

Agora, pode instalar os pacotes necessários. O `k4a-tools` pacote inclui o [Azure Kinect Viewer,](azure-kinect-viewer.md)o [Azure Kinect Recorder](record-sensor-streams-file.md)e a [Azure Kinect Firmware Tool](azure-kinect-firmware-tool.md). Para instalar a embalagem, corra:

`sudo apt install k4a-tools`
 
Este comando instala os pacotes de dependência necessários para que as ferramentas funcionem corretamente, incluindo a versão mais recente de `libk4a<major>.<minor>` . Terá de adicionar regras udev para aceder ao Azure Kinect DK sem ser o utilizador principal. Para obter instruções, consulte [a configuração do dispositivo Linux](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/blob/develop/docs/usage.md#linux-device-setup). Como alternativa, pode lançar aplicações que utilizem o dispositivo como raiz.

O `libk4a<major>.<minor>-dev` pacote contém os cabeçalhos e ficheiros CMake para construir as suas aplicações/execuções contra `libk4a` .

O `libk4a<major>.<minor>` pacote contém os objetos partilhados necessários para executar aplicações/execuções que dependem de `libk4a` .

Os tutoriais básicos requerem o `libk4a<major>.<minor>-dev` pacote. Para instalar a embalagem, corra:

`sudo apt install libk4a<major>.<minor>-dev` 

Se o comando for bem sucedido, o SDK está pronto a ser utilizado.

Certifique-se de que instala a versão correspondente com `libk4a<major>.<minor>` `libk4a<major>.<minor>-dev` . Por exemplo, se instalar o `libk4a4.1-dev` pacote, instale o pacote correspondente `libk4a4.1` que contenha a versão correspondente de ficheiros de objetos partilhados. Para obter a versão mais recente de `libk4a` , consulte os links na secção seguinte.

## <a name="change-log-and-older-versions"></a>Alterar log e versões mais antigas

Pode encontrar o registo de alteração do Sensor Azure Kinect SDK [aqui](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/blob/develop/CHANGELOG.md).

Se precisar de uma versão mais antiga do Sensor Kinect Azure SDK, encontre-o [aqui](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/blob/develop/docs/usage.md).

## <a name="next-steps"></a>Passos seguintes

[Configurar o Azure Kinect DK](set-up-azure-kinect-dk.md)
