---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 06/26/2019
ms.author: alkohli
ms.openlocfilehash: 09d9b5bbf3f9ca7a4eef37891d03c9c865e7f74b
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67448644"
---
Um certificado SSL adequado garante que estiver enviando informações encriptadas para o servidor correto. Além de criptografia, o certificado também permite a autenticação. Pode carregar seu próprio certificado SSL confiável por meio da interface do PowerShell do dispositivo.

1. [Ligar a interface do PowerShell](#connect-to-the-powershell-interface).
2. Utilize o `Set-HcsCertificate` cmdlet para carregar o certificado. Quando lhe for pedido, forneça os seguintes parâmetros:

   - `CertificateFilePath` -Path para a partilha que contém o ficheiro de certificado no *. pfx* formato.
   - `CertificatePassword` -A palavra-passe utilizada para proteger o certificado.
   - `Credentials` -Nome de utilizador para aceder à partilha que contém o certificado. Forneça a palavra-passe para a partilha de rede quando lhe for pedido.

     O exemplo seguinte mostra a utilização deste cmdlet:

     ```
     Set-HcsCertificate -Scope LocalWebUI -CertificateFilePath "\\myfileshare\certificates\mycert.pfx" -CertificatePassword "mypassword" -Credential "Username"
     ```

