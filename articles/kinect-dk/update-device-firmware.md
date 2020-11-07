---
title: Atualizar firmware Azure Kinect DK
description: Saiba como atualizar o firmware do dispositivo Azure Kinect DK utilizando a ferramenta de firmware Azure Kinect.
author: tesych
ms.author: tesych
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: kinect, firmware, atualização, recuperação
ms.openlocfilehash: 71557798a584635356cc47fd7c4a0309df4d018a
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/07/2020
ms.locfileid: "94356231"
---
# <a name="update-azure-kinect-dk-firmware"></a>Atualizar firmware Azure Kinect DK

Este documento fornece orientações sobre como atualizar o firmware do dispositivo no seu Azure Kinect DK.

A Azure Kinect DK não atualiza o firmware automaticamente. Pode utilizar [a Ferramenta de Firmware Azure Kinect](azure-kinect-firmware-tool.md) para atualizar o firmware manualmente para a versão mais recente disponível.

## <a name="prepare-for-firmware-update"></a>Prepare-se para atualização de firmware

1. [Baixar SDK](sensor-sdk-download.md).
2. Instale o SDK.
3. No local de instalação SDK sob (localização de instalação SDK)\ferramentas\ deve encontrar:

    - AzureKinectFirmwareTool.exe
    - Um ficheiro Firmware .bin na pasta firmware, tal como *AzureKinectDK_Fw_1.5.926614.bin*.

4. Ligue o seu dispositivo ao PC anfitrião e ligue-o também.

> [!IMPORTANT]
> Mantenha o USB e a alimentação ligados durante a atualização do firmware. A remoção de qualquer ligação durante a atualização pode colocar o firmware num estado corrompido.

## <a name="update-device-firmware"></a>Atualizar firmware de dispositivo

1. Abra um pedido de comando na pasta (SDK install location)\tools\ .
2. Atualizar Firmware utilizando a ferramenta de firmware Azure Kinect

    `AzureKinectFirmwareTool.exe -u <device_firmware_file.bin>`

    Exemplo:

    `AzureKinectFirmwareTool.exe -u firmware\AzureKinectDK_Fw_1.5.926614.bin`

3. Aguarde até que a atualização do firmware termine. Pode levar alguns minutos dependendo do tamanho da imagem.

### <a name="verify-device-firmware-version"></a>Verifique a versão do firmware do dispositivo

1. Verifique se o firmware está atualizado.

    `AzureKinectFirmwareTool.exe -q`

2. Veja o seguinte exemplo.

    ```console
       >AzureKinectFirmwareTool.exe -q
       == Azure Kinect DK Firmware Tool ==
        Device Serial Number: 000805192412
        Current Firmware Versions:
        RGB camera firmware:      1.6.102
        Depth camera firmware:    1.6.75
        Depth config file:        6109.7
        Audio firmware:           1.6.14
        Build Config:             Production
        Certificate Type:         Microsoft
    ```

3. Se vir a saída acima, o seu firmware é atualizado.

4. Após a atualização do firmware, pode executar [o espectador Azure Kinect](azure-kinect-viewer.md) para verificar se todos os sensores estão a funcionar como esperado.

## <a name="troubleshooting"></a>Resolução de problemas

As atualizações do Firmware podem falhar por várias razões. Quando uma atualização de firmware falhar, experimente os seguintes passos de mitigação:

1. Tente executar o comando de atualização de firmware uma segunda vez.

2. Confirme que o dispositivo ainda está ligado consultando a versão firmware.        AzureKinectFirmareTool.exe

3. Se tudo o resto falhar, siga os passos [de recuperação](https://support.microsoft.com/help/4494277/reset-azure-kinect-dk) para voltar ao firmware de fábrica e tentar novamente.

Para quaisquer problemas adicionais, consulte [as páginas de suporte da Microsoft](./index.yml)

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
>[Ferramenta de firmware Azure Kinect](azure-kinect-firmware-tool.md)