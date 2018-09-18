---
title: Obter os dados tutoriais utilizar a API de relatórios do AD do Azure com certificados | Documentos da Microsoft
description: Este tutorial explica como utilizar a API de relatórios do AD do Azure com credenciais de certificados para obter dados de diretórios sem intervenção do utilizador.
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.component: report-monitor
ms.date: 05/07/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: 3529de1e4a06c9c7f386a9fbd06470ce03cd84a6
ms.sourcegitcommit: 1b561b77aa080416b094b6f41fce5b6a4721e7d5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/17/2018
ms.locfileid: "45729812"
---
# <a name="tutorial-get-data-using-the-azure-active-directory-reporting-api-with-certificates"></a>Tutorial: Utilizar obter dados do Azure Active Directory reporting API com certificados

As [APIs de relatórios Azure Active Directory (Azure AD)](concept-reporting-api.md) proporcionam acesso programático aos dados através de um conjunto de APIs baseadas em REST. Pode chamar estas APIs a partir de várias linguagens e ferramentas de programação. Se desejar acessar a API de relatórios de AD do Azure sem intervenção do utilizador, tem de configurar o acesso ao utilizar certificados.

Neste tutorial, saiba como criar um certificado de teste e utilizá-lo para aceder à Graph API do MS para relatórios. Não recomendamos utilizar o certificado de teste num ambiente de produção. 

## <a name="prerequisites"></a>Pré-requisitos

1. Em primeiro lugar, concluir o [pré-requisitos para aceder ao Azure Active Directory reporting API](howto-configure-prerequisites-for-reporting-api.md). 

2. Transfira e instale [do Azure AD PowerShell V2](https://github.com/Azure/azure-docs-powershell-azuread/blob/master/docs-conceptual/azureadps-2.0/install-adv2.md).

3. Instale [MSCloudIdUtils](https://www.powershellgallery.com/packages/MSCloudIdUtils/). Este módulo disponibiliza vários cmdlets de utilitário, incluindo:
    - As bibliotecas ADAL necessárias à autenticação
    - Tokens de acesso de utilizador, chaves de aplicação e certificados, através da ADAL
    - Graph API que processa resultados paginados

4. Se for a primeira vez utilizando o módulo execute **Install-MSCloudIdUtilsModule**, caso contrário, importe-o utilizando o **Import-Module** comando do Powershell.

A sua sessão deve ter um aspeto semelhante a este ecrã:

  ![Windows Powershell](./media/tutorial-access-api-with-certificates/module-install.png)
  
## <a name="create-a-test-certificate"></a>Criar um certificado de teste

1. Utilize o **New-SelfSignedCertificate** commandlet do Powershell para criar um certificado de teste.

   ```
   $cert = New-SelfSignedCertificate -Subject "CN=MSGraph_ReportingAPI" -CertStoreLocation "Cert:\CurrentUser\My" -KeyExportPolicy Exportable -KeySpec Signature -KeyLength 2048 -KeyAlgorithm RSA -HashAlgorithm SHA256
   ```

2. Utilize o **Export-Certificate** commandlet para os exportar para um ficheiro de certificado.

   ```
   Export-Certificate -Cert $cert -FilePath "C:\Reporting\MSGraph_ReportingAPI.cer"

   ```

## <a name="register-the-certificate-in-your-app"></a>Registre-se o certificado na sua aplicação

1. Navegue para o [portal do Azure](https://portal.azure.com), selecione **Azure Active Directory**, em seguida, selecione **registos das aplicações** e escolha a sua aplicação na lista. 

2. Selecione **configurações** > **chaves** e selecione **carregar chave pública**.

3. Selecione o ficheiro de certificado do passo anterior e selecione **guardar**. 

4. Tenha em atenção que o ID da aplicação e o thumbprint do certificado que acabou de registar com a sua aplicação. Para encontrar o thumbprint, a partir da sua página de aplicativo no portal, aceda a **configurações** e clique em **chaves**. O thumbprint será sob o **chaves públicas** lista.

5. Abra o manifesto do aplicativo no editor de manifesto de inline e substitua a *keyCredentials* propriedade com as suas informações de certificado novo com o esquema abaixo. 

   ```
   "keyCredentials": [
        {
            "customKeyIdentifier": "$base64Thumbprint", //base64 encoding of the certificate hash
            "keyId": "$keyid", //GUID to identify the key in the manifest
            "type": "AsymmetricX509Cert",
            "usage": "Verify",
            "value":  "$base64Value" //base64 encoding of the certificate raw data
        }
    ]
   ```

6. Guarde o manifesto. 
  
## <a name="get-an-access-token-for-ms-graph-api"></a>Obter um acesso de token para MS Graph API

1. Utilize o **Get-MSCloudIdMSGraphAccessTokenFromCert** cmdlet do módulo do PowerShell de MSCloudIdUtils, passando o ID da aplicação e o thumbprint que obteve no passo anterior. 

 ![Portal do Azure](./media/tutorial-access-api-with-certificates/getaccesstoken.png)

## <a name="use-the-access-token-to-call-the-graph-api"></a>Utilizar o token de acesso para chamar a Graph API

1. Agora, pode utilizar o token de acesso no seu script de Powershell para consultar a Graph API. Utilize o **Invoke-MSCloudIdMSGraphQuery** cmdlet a partir de MSCloudIDUtils enumerar o ponto de extremidade signins e directoryAudits. Este cmdlet processa resultados paginados múltiplos e envia-os para o pipeline do PowerShell.

2. Consulte o ponto de extremidade directoryAudits obtém os registos de auditoria. 
 ![Portal do Azure](./media/tutorial-access-api-with-certificates/query-directoryAudits.png)

3. Consulte o ponto de extremidade signins obtém os registos de início de sessão.
 ![Portal do Azure](./media/tutorial-access-api-with-certificates/query-signins.png)

4. Agora, pode optar por exportar estes dados para um CSV e guardar num sistema SIEM. Também pode encapsular o script numa tarefa agendada para obter dados do Azure AD a partir do seu inquilino periodicamente, sem ter de armazenar chaves de aplicação no código de origem. 

## <a name="next-steps"></a>Passos Seguintes

* [Obter uma primeira impressão das APIs de relatórios](concept-reporting-api.md)
* [Referência da API de auditoria](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/directoryaudit) 
* [Relatório de atividade de início de sessão de referência da API](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/signin)



