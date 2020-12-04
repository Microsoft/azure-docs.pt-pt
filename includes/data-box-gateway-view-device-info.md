---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 10/15/2020
ms.author: alkohli
ms.openlocfilehash: ca060e75e50a3e2327fc0516c3cfc9550afbf90f
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2020
ms.locfileid: "96582762"
---
1. [Ligue-se à interface PowerShell](#connect-to-the-powershell-interface).
2. Use o `Get-HcsApplianceInfo` para obter a informação para o seu dispositivo.

    O exemplo a seguir mostra a utilização deste cmdlet:

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

    Aqui está uma tabela que resume algumas das informações importantes do dispositivo:

    | Parâmetro | Descrição |
    |-----------|-------------|
    | FriendlyName                   | O nome amigável do dispositivo configurado através da UI web local durante a implementação do dispositivo. O nome padrão amigável é o número de série do dispositivo.  |
    | SerialNumber                   | O número de série do dispositivo é um número único atribuído na fábrica.                                                                             |
    | Modelo                          | O modelo para o seu dispositivo. O modelo é virtual para Data Box Gateway.                   |
    | FriendlySoftwareVersion        | A cadeia amigável que corresponde à versão do software do dispositivo. Para uma pré-visualização do sistema em execução, a versão amigável do software seria Data Box Edge 1902. |
    | HcsVersion                     | A versão de software HCS em execução no seu dispositivo. Por exemplo, a versão de software HCS correspondente ao Data Box Edge 1902 é de 1.4.771.324.            |
    | Capacidade LocalInMb              | A capacidade local total do dispositivo em Megabits.                                                                                                        |
    | IsRegistered                   | Este valor indica se o seu dispositivo está ativado com o serviço.                                                                                         |


