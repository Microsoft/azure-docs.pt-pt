---
title: Ferramenta de firmware do Azure Kinect
description: Compreenda como consultar e atualizar o firmware do dispositivo utilizando a ferramenta de firmware Azure Kinect.
author: tesych
ms.author: tesych
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: kinect, firmware, atualização
ms.openlocfilehash: f8b89751d27fb5a4b18d635f45f63f4f36bd05f6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85277522"
---
# <a name="azure-kinect-dk-firmware-tool"></a>Ferramenta de firmware Azure Kinect DK

A Ferramenta de Firmware Azure Kinect pode ser usada para consultar e atualizar o firmware do dispositivo do Azure Kinect DK.

## <a name="list-connected-devices"></a>Listar dispositivos conectados

Pode obter uma lista de dispositivos conectados utilizando a opção -l.  `AzureKinectFirmwareTool.exe -l`

```console
 == Azure Kinect DK Firmware Tool ==
Found 2 connected devices:
0: Device "000036590812"
1: Device "000274185112"
```

## <a name="check-device-firmware-version"></a>Verifique a versão do firmware do dispositivo

Pode verificar as versões atuais do firmware do primeiro dispositivo anexado utilizando a opção -q, por exemplo, `AzureKinectFirmwareTool.exe -q` .

```console
 == Azure Kinect DK Firmware Tool ==
Device Serial Number: 000036590812
Current Firmware Versions:
    RGB camera firmware:      1.5.92
    Depth camera firmware:    1.5.66
    Depth config file:        6109.7
    Audio firmware:           1.5.14
    Build Config:             Production
    Certificate Type:         Microsoft
```

Se houver mais de um dispositivo ligado, pode especificar qual o dispositivo que pretende consultar adicionando o número de série completo ao comando, tais como:

`AzureKinectFirmwareTool.exe -q 000036590812`

## <a name="update-device-firmware"></a>Atualizar firmware de dispositivo

O uso mais comum desta ferramenta é atualizar o firmware do dispositivo. Faça a atualização ligando para a ferramenta utilizando a `-u` opção. Uma atualização de firmware pode demorar alguns minutos, dependendo dos ficheiros de firmware que devem ser atualizados.

Para obter instruções de atualização passo a passo do firmware, consulte [a atualização do firmware Azure Kinect](update-device-firmware.md).  

`AzureKinectFirmwareTool.exe -u firmware\AzureKinectDK_Fw_1.5.926614.bin`

Se houver mais de um dispositivo ligado, pode especificar qual o dispositivo que pretende consultar adicionando o número de série completo ao comando.

`AzureKinectFirmwareTool.exe -u firmware\AzureKinectDK_Fw_1.5.926614.bin 000036590812`

## <a name="reset-device"></a>Repor dispositivo

Um DK Azure Kinect em anexo pode ser reiniciado utilizando a opção -r, se tiver de colocar o dispositivo num estado conhecido.

Se houver mais de um dispositivo ligado, pode especificar qual o dispositivo que pretende consultar adicionando o número de série completo ao comando.

`AzureKinectFirmwareTool.exe -r 000036590812`

## <a name="inspect-firmware"></a>Inspecionar o firmware

A inspeção do firmware permite-lhe obter as informações da versão a partir de um ficheiro de caixa de firmware antes de atualizar um dispositivo real.

`AzureKinectFirmwareTool.exe -i firmware\AzureKinectDK_Fw_1.5.926614.bin`

```console
 == Azure Kinect DK Firmware Tool ==
Loading firmware package ..\tools\updater\firmware\AzureKinectDK_Fw_1.5.926614.bin.
File size: 1228844 bytes
This package contains:
  RGB camera firmware:      1.5.92
  Depth camera firmware:    1.5.66
  Depth config files: 6109.7 5006.27
  Audio firmware:           1.5.14
  Build Config:             Production
  Certificate Type:         Microsoft
  Signature Type:           Microsoft
```

## <a name="firmware-update-tool-options"></a>Opções de ferramenta de atualização de firmware

```console
 == Azure Kinect DK Firmware Tool ==
* Usage Info *
    AzureKinectFirmwareTool.exe <Command> <Arguments>

Commands:
    List Devices: -List, -l
    Query Device: -Query, -q
        Arguments: [Serial Number]
    Update Device: -Update, -u
        Arguments: <Firmware Package Path and FileName> [Serial Number]
    Reset Device: -Reset, -r
        Arguments: [Serial Number]
    Inspect Firmware: -Inspect, -i
        Arguments: <Firmware Package Path and FileName>

    If no Serial Number is provided, the tool will just connect to the first device.

Examples:
    AzureKinectFirmwareTool.exe -List
    AzureKinectFirmwareTool.exe -Update c:\data\firmware.bin 0123456
```

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
>[Instruções passo a passo para atualizar o firmware do dispositivo](update-device-firmware.md)
