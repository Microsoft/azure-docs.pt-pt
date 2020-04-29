---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 06/26/2019
ms.author: alkohli
ms.openlocfilehash: 09d9b5bbf3f9ca7a4eef37891d03c9c865e7f74b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "67448644"
---
Um certificado SSL adequado garante que está a enviar informações encriptadas para o servidor certo. Além da encriptação, o certificado também permite a autenticação. Pode fazer o upload do seu próprio certificado SSL fidedigno através da interface PowerShell do dispositivo.

1. [Ligue-se à interface PowerShell](#connect-to-the-powershell-interface).
2. Utilize `Set-HcsCertificate` o cmdlet para fazer o upload do certificado. Quando solicitado, forneça os seguintes parâmetros:

   - `CertificateFilePath`- Caminho para a parte que contém o ficheiro de certificado em formato *.pfx.*
   - `CertificatePassword`- Uma senha usada para proteger o certificado.
   - `Credentials`- Nome de utilizador para aceder à parte que contém o certificado. Forneça a palavra-passe para a partilha da rede quando solicitado.

     O exemplo que se segue mostra a utilização deste cmdlet:

     ```
     Set-HcsCertificate -Scope LocalWebUI -CertificateFilePath "\\myfileshare\certificates\mycert.pfx" -CertificatePassword "mypassword" -Credential "Username"
     ```

