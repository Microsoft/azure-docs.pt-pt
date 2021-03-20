---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 06/26/2019
ms.author: alkohli
ms.openlocfilehash: 09d9b5bbf3f9ca7a4eef37891d03c9c865e7f74b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "67448644"
---
Um certificado SSL adequado garante que está a enviar informações encriptadas para o servidor correto. Além da encriptação, o certificado também permite a autenticação. Pode carregar o seu próprio certificado SSL fidedigno através da interface PowerShell do dispositivo.

1. [Ligue-se à interface PowerShell](#connect-to-the-powershell-interface).
2. Utilize o `Set-HcsCertificate` cmdlet para carregar o certificado. Quando solicitado, forneça os seguintes parâmetros:

   - `CertificateFilePath`- Caminho para a partilha que contém o ficheiro de certificado em formato *.pfx.*
   - `CertificatePassword` - Uma senha usada para proteger o certificado.
   - `Credentials` - Nome de utilizador para aceder à parte que contém o certificado. Indique a palavra-passe para a partilha de rede quando lhe for pedido.

     O exemplo a seguir mostra a utilização deste cmdlet:

     ```
     Set-HcsCertificate -Scope LocalWebUI -CertificateFilePath "\\myfileshare\certificates\mycert.pfx" -CertificatePassword "mypassword" -Credential "Username"
     ```

