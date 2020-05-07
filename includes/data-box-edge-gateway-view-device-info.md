---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/04/2019
ms.author: alkohli
ms.openlocfilehash: 94c132421f1e113b667341b094acad8047e5f465
ms.sourcegitcommit: 856db17a4209927812bcbf30a66b14ee7c1ac777
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "82561771"
---
1. [Ligue-se à interface PowerShell](#connect-to-the-powershell-interface).
2. Use `Get-HcsApplianceInfo` o para obter a informação para o seu dispositivo.

    O exemplo que se segue mostra a utilização deste cmdlet:

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

    Aqui está uma tabela resumindo algumas das informações importantes do dispositivo:
    
    | Parâmetro                             | Descrição                                                                                                                                                  |   |
    |--------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------|---|
    | FriendlyName                   | O nome amigável do dispositivo, tal como configurado através da UI web local durante a implementação do dispositivo. O nome amigável padrão é o número de série do dispositivo.  |   |
    | SerialNumber                   | O número de série do dispositivo é um número único atribuído na fábrica.                                                                             |   |
    | Modelo                          | O modelo para o seu dispositivo Azure Stack Edge ou Data Box Gateway. O modelo é físico para Azure Stack Edge e virtual para Data Box Gateway.                   |   |
    | Versão amigável do software        | A corda amigável que corresponde à versão do software do dispositivo. Para uma pré-visualização de sistema, a versão amigável do software seria Data Box Edge 1902. |   |
    | HcsVersion                     | A versão do software HCS em execução no seu dispositivo. Por exemplo, a versão de software HCS correspondente ao Data Box Edge 1902 é de 1.4.771.324.            |   |
    | Capacidade localInMb              | A capacidade local total do dispositivo em Megabits.                                                                                                        |   |
    | Está Registado                   | Este valor indica se o seu dispositivo está ativado com o serviço.                                                                                         |   |


