---
title: Tutorial para a API de relatórios do AD com certificados | Microsoft Docs
description: Este tutorial explica como usar a API de relatórios do Azure AD com credenciais de certificado para obter dados de diretórios sem intervenção do usuário.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4d723af5d994006c4ae4f90905ede73fa87326bf
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74014273"
---
# <a name="tutorial-get-data-using-the-azure-active-directory-reporting-api-with-certificates"></a>Tutorial: obter dados usando a API de relatório de Azure Active Directory com certificados

As [APIs de relatórios Azure Active Directory (Azure AD)](concept-reporting-api.md) proporcionam acesso programático aos dados através de um conjunto de APIs baseadas em REST. Pode chamar estas APIs a partir de várias linguagens e ferramentas de programação. Se você quiser acessar a API de relatórios do Azure AD sem intervenção do usuário, deverá configurar o acesso para usar certificados.

Neste tutorial, você aprenderá a usar um certificado de teste para acessar o MS API do Graph para relatórios. Não recomendamos o uso de certificados de teste em um ambiente de produção. 

## <a name="prerequisites"></a>Pré-requisitos

1. Para acessar os dados de entrada, verifique se você tem um locatário Azure Active Directory com uma licença Premium (P1/P2). Consulte [introdução ao Azure Active Directory Premium](../fundamentals/active-directory-get-started-premium.md) para atualizar sua edição do Azure Active Directory. Observe que, se você não tiver dados de atividades antes da atualização, levará alguns dias para que os dados sejam exibidos nos relatórios após a atualização para uma licença Premium. 

2. Crie ou alterne para uma conta de usuário na função **administrador global**, **administrador de segurança**, leitor de **segurança** ou leitor de **relatório** para o locatário. 

3. Conclua os [pré-requisitos para acessar a API de relatório do Azure Active Directory](howto-configure-prerequisites-for-reporting-api.md). 

4. Baixe e instale o [PowerShell v2 do Azure ad](https://github.com/Azure/azure-docs-powershell-azuread/blob/master/docs-conceptual/azureadps-2.0/install-adv2.md).

5. Instale o [MSCloudIdUtils](https://www.powershellgallery.com/packages/MSCloudIdUtils/). Este módulo disponibiliza vários cmdlets de utilitário, incluindo:
    - As bibliotecas ADAL necessárias para a autenticação
    - Tokens de acesso de utilizador, chaves de aplicação e certificados, através da ADAL
    - Graph API que processa resultados paginados

6. Se for a primeira vez que você usa o módulo, execute **install-MSCloudIdUtilsModule**, caso contrário, importe-o usando o comando do PowerShell **Import-Module** . Sua sessão deve ser semelhante a esta tela: ![Windows PowerShell](./media/tutorial-access-api-with-certificates/module-install.png)
  
7. Use o commandlet do PowerShell **New-SelfSignedCertificate** para criar um certificado de teste.

   ```
   $cert = New-SelfSignedCertificate -Subject "CN=MSGraph_ReportingAPI" -CertStoreLocation "Cert:\CurrentUser\My" -KeyExportPolicy Exportable -KeySpec Signature -KeyLength 2048 -KeyAlgorithm RSA -HashAlgorithm SHA256
   ```

8. Use o commandlet **Export-Certificate** para exportá-lo para um arquivo de certificado.

   ```
   Export-Certificate -Cert $cert -FilePath "C:\Reporting\MSGraph_ReportingAPI.cer"

   ```

## <a name="get-data-using-the-azure-active-directory-reporting-api-with-certificates"></a>Obter dados com a API de relatórios do Azure Active Directory com certificados

1. Navegue até a [portal do Azure](https://portal.azure.com), selecione **Azure Active Directory**, em seguida, selecione **registros de aplicativo** e escolha o aplicativo na lista. 

2. Selecione **configurações** > **chaves** e selecione **carregar chave pública**.

3. Selecione o arquivo de certificado da etapa anterior e selecione **salvar**. 

4. Anote a ID do aplicativo e a impressão digital do certificado que você acabou de registrar com seu aplicativo. Para localizar a impressão digital, na página do aplicativo no portal, vá para **configurações** e clique em **chaves**. A impressão digital estará na lista de **chaves públicas** .

5. Abra o manifesto do aplicativo no editor de manifesto embutido e substitua a propriedade *Keycredentials* por suas novas informações de certificado usando o esquema a seguir. 

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

6. Salve o manifesto. 
  
7. Agora, você pode obter um token de acesso para o MS API do Graph usando esse certificado. Use o cmdlet **Get-MSCloudIdMSGraphAccessTokenFromCert** do módulo MSCloudIdUtils do PowerShell, passando a ID do aplicativo e a impressão digital obtida da etapa anterior. 

   ![Portal do Azure](./media/tutorial-access-api-with-certificates/getaccesstoken.png)

8. Use o token de acesso no script do PowerShell para consultar o API do Graph. Use o cmdlet **Invoke-MSCloudIdMSGraphQuery** do MSCloudIDUtils para enumerar o ponto de extremidade entrada e directoryAudits. Esse cmdlet manipula os resultados de várias páginas e envia esses resultados para o pipeline do PowerShell.

9. Consulte o ponto de extremidade directoryAudits para recuperar os logs de auditoria. 
   ![Azure portal](./media/tutorial-access-api-with-certificates/query-directoryAudits.png)

10. Consulte o ponto de extremidade entrada para recuperar os logs de entrada.
    ![Azure portal](./media/tutorial-access-api-with-certificates/query-signins.png)

11. Agora você pode optar por exportar esses dados para um CSV e salvá-los em um sistema SIEM. Também pode encapsular o script numa tarefa agendada para obter dados do Azure AD a partir do seu inquilino periodicamente, sem ter de armazenar chaves de aplicação no código de origem. 

## <a name="next-steps"></a>Passos seguintes

* [Obter uma primeira impressão das APIs de relatórios](concept-reporting-api.md)
* [Referência de API de auditoria](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/directoryaudit) 
* [Referência da API de relatório de atividade de entrada](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/signin)
