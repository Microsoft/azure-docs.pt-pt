---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 06/26/2019
ms.author: alkohli
ms.openlocfilehash: 09d9b5bbf3f9ca7a4eef37891d03c9c865e7f74b
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "67448644"
---
Um certificado SSL adequado garante que está a enviar informações encriptadas para o servidor certo. Além da encriptação, o certificado também permite a autenticação. Pode carregar o seu próprio certificado SSL fidedigno através da interface PowerShell do dispositivo.

1. [Ligue-se à interface PowerShell](#connect-to-the-powershell-interface).
2. Utilize o `Set-HcsCertificate` cmdlet para carregar o certificado. Quando solicitado, forneça os seguintes parâmetros:

   - `CertificateFilePath`- Caminho para a partilha que contém o ficheiro de certificado em formato *.pfx.*
   - `CertificatePassword`- Uma senha usada para proteger o certificado.
   - `Credentials`- Nome de utilizador para aceder à parte que contém o certificado. Forneça a palavra-passe à partilha de rede quando solicitado.

     O exemplo a seguir mostra a utilização deste cmdlet:

     ```
     Set-HcsCertificate -Scope LocalWebUI -CertificateFilePath "\\myfileshare\certificates\mycert.pfx" -CertificatePassword "mypassword" -Credential "Username"
     ```

