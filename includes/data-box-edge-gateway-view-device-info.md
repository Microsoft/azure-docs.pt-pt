---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/04/2019
ms.author: alkohli
ms.openlocfilehash: d5af557a62f4bd35c242d334c28a38c3d632f7cf
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/25/2019
ms.locfileid: "58404083"
---
1. [Ligar a interface do PowerShell](#connect-to-the-powershell-interface).
2. Utilize o `Get-HcsApplianceInfo` para obter as informações para o seu dispositivo.

    O exemplo seguinte mostra a utilização deste cmdlet:

    ```
    [10.100.10.10]: PS>Get-HcsApplianceInfo
    
    Id                            : b2044bdb-56fd-4561-a90b-407b2a67bdfc
    FriendlyName                  : DBE-NBSVFQR94S6
    Name                          : DBE-NBSVFQR94S6
    SerialNumber                  : HCS-NBSVFQR94S6
    DeviceId                      : 40d7288d-cd28-481d-a1ea-87ba9e71ca6b
    Model                         : Virtual
    FriendlySoftwareVersion       : Data Box Gateway 1902
    HcsVersion                    : 1.4.771.324
    IsClustered                   : False
    IsVirtual                     : True
    LocalCapacityInMb             : 1964992
    SystemState                   : Initialized
    SystemStatus                  : Normal
    Type                          : DataBoxGateway
    CloudReadRateBytesPerSec      : 0
    CloudWriteRateBytesPerSec     : 0
    IsInitialPasswordSet          : True
    FriendlySoftwareVersionNumber : 1902
    UploadPolicy                  : All
    DataDiskResiliencySettingName : Simple
    ApplianceTypeFriendlyName     : Data Box Gateway
    IsRegistered                  : False
    ```

    Esta é uma tabela que resume algumas das informações do dispositivo importantes:
    
    | Parâmetro                             | Descrição                                                                                                                                                  |   |
    |--------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------|---|
    | FriendlyName                   | O nome amigável do dispositivo, conforme configurado através da web local da interface do Usuário durante a implementação do dispositivo. O nome amigável do padrão é o número de série do dispositivo.  |   |
    | serialNumber                   | O número de série do dispositivo é um número exclusivo atribuído na fábrica.                                                                             |   |
    | Modelo                          | O modelo para o seu dispositivo Edge de caixa de dados ou dados de caixa de Gateway. O modelo é virtual para o Gateway de caixa de dados e físicas para o Edge de caixa de dados.                   |   |
    | FriendlySoftwareVersion        | A cadeia de caracteres amigável que corresponde à versão de software do dispositivo. Para um sistema em execução pré-visualização, a versão do software amigável seria 1902 de borda de caixa de dados. |   |
    | HcsVersion                     | A versão de software HCS em execução no seu dispositivo. Por exemplo, a versão do software HCS correspondentes aos dados de caixa de borda 1902 é 1.4.771.324.            |   |
    | LocalCapacityInMb              | Capacidade total local do dispositivo em Megabits.                                                                                                        |   |
    | IsRegistered                   | Este valor indica se o dispositivo é ativado com o serviço.                                                                                         |   |


