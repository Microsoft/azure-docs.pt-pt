---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/23/2019
ms.author: alkohli
ms.openlocfilehash: d7a9923d5bd9e357bcd75fae6e0a7d1bcd437a53
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2019
ms.locfileid: "64732645"
---
Um certificado SSL adequado garante que estiver enviando informações encriptadas para o servidor correto. Além de criptografia, o certificado também permite a autenticação. Pode carregar seu próprio certificado SSL confiável por meio da interface do PowerShell do dispositivo.

1. [Ligar a interface do PowerShell](#connect-to-the-powershell-interface).
2. Utilize o `Set-HcsCertificate` cmdlet para carregar o certificado. Quando lhe for pedido, forneça os seguintes parâmetros:

   - `CertificateFilePath` -Path para a partilha que contém o ficheiro de certificado no *. pfx* formato.
   - `CertificatePassword` -A palavra-passe utilizada para proteger o certificado.
   - `Credentials` -O nome de utilizador e palavra-passe para aceder à partilha que contém o certificado.

     O exemplo seguinte mostra a utilização deste cmdlet:

     ```
     Set-HcsCertificate -Scope LocalWebUI -CertificateFilePath "\\myfileshare\certificates\mycert.pfx" -CertificatePassword "mypassword" -Credential "Username/Password"
     ```

