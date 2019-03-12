---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/05/2019
ms.author: alkohli
ms.openlocfilehash: c689dc4f7e0957218d6504532c4b481d7673def8
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57555279"
---
Um certificado SSL adequado garante que estiver enviando informações encriptadas para o servidor correto. Além de criptografia, o certificado também permite a autenticação. Pode carregar seu próprio certificado SSL confiável por meio da interface do PowerShell do dispositivo.

1. [Ligar a interface do PowerShell](#connect-to-the-powershell-interface).
2. Utilize o `Set-HcsCertificate` cmdlet para carregar o certificado. Quando lhe for pedido, forneça os seguintes parâmetros:

    - `CertificateFilePath` -Path para a partilha que contém o ficheiro de certificado no *. pfx* formato.
    - `CertificatePassword` -A palavra-passe utilizada para proteger o certificado.
    - `Credentials` -O nome de utilizador e palavra-passe para aceder à partilha que contém o certificado.

    O exemplo seguinte mostra a utilização deste cmdlet:

    ```
    Set-HcsCertificate -Scope LocalWebUI -CertificateFilePath "\\myfileshare\certificates\mycert.pfx" -CertificatePassword "mypassword" -Credentials "Username/Password"
    ```

