---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/05/2019
ms.author: alkohli
ms.openlocfilehash: 216380cf7069468d13c4e533fc90b2596aa211c4
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58124987"
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

