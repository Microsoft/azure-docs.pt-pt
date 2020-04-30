---
title: Tutorial para AD Reporting API com certificados Microsoft Docs
description: Este tutorial explica como usar a AD Reporting API azure com credenciais de certificado para obter dados de diretórios sem intervenção do utilizador.
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
ms.openlocfilehash: 2808c8431a6b98b162920fb58a6e2ac0498d2055
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82081715"
---
# <a name="tutorial-get-data-using-the-azure-active-directory-reporting-api-with-certificates"></a>Tutorial: Obtenha dados através do Diretório Ativo Azure reportando API com certificados

As [APIs de relatórios Azure Active Directory (Azure AD)](concept-reporting-api.md) proporcionam acesso programático aos dados através de um conjunto de APIs baseadas em REST. Pode chamar estas APIs a partir de várias linguagens e ferramentas de programação. Se pretender aceder à APi de Reporte De Informação aD Azure sem intervenção do utilizador, tem de configurar o seu acesso à utilização de certificados.

Neste tutorial, aprende-se a usar um certificado de teste para aceder à API do Gráfico MS para reporte. Não recomendamos a utilização de certificados de teste num ambiente de produção. 

## <a name="prerequisites"></a>Pré-requisitos

1. Para aceder aos dados de entrada, certifique-se de que tem um inquilino do Diretório Ativo Azure com uma licença premium (P1/P2). Ver [Começar com o Azure Ative Directory Premium](../fundamentals/active-directory-get-started-premium.md) para atualizar a sua edição de Diretório Ativo Azure. Note que se não tiver quaisquer dados de atividades antes da atualização, levará alguns dias para que os dados apareçam nos relatórios após o upgrade para uma licença premium. 

2. Crie ou mude para uma conta de utilizador no **administrador global,** administrador de **segurança,** leitor de **segurança** ou papel de leitor de **relatórios** para o inquilino. 

3. Complete os [pré-requisitos para aceder à API de reporte de Relatórios ativos do Azure](howto-configure-prerequisites-for-reporting-api.md). 

4. Descarregue e [instale O Azure AD PowerShell V2](https://github.com/Azure/azure-docs-powershell-azuread/blob/master/docs-conceptual/azureadps-2.0/install-adv2.md).

5. Instale [MSCloudIdUtils](https://www.powershellgallery.com/packages/MSCloudIdUtils/). Este módulo disponibiliza vários cmdlets de utilitário, incluindo:
    - As bibliotecas ADAL necessárias para a autenticação
    - Tokens de acesso de utilizador, chaves de aplicação e certificados, através da ADAL
    - Graph API que processa resultados paginados

6. Se for a primeira vez que utiliza o módulo **install-MSCloudIdUtilsModule,** importa-o utilizando o comando PowerShell **do Módulo de Importação.** A sua sessão deve ![ser semelhante a este ecrã: Windows PowerShell](./media/tutorial-access-api-with-certificates/module-install.png)
  
7. Utilize o comando **PowerShell de Certificado Novo Auto-Assinado** para criar um certificado de teste.

   ```
   $cert = New-SelfSignedCertificate -Subject "CN=MSGraph_ReportingAPI" -CertStoreLocation "Cert:\CurrentUser\My" -KeyExportPolicy Exportable -KeySpec Signature -KeyLength 2048 -KeyAlgorithm RSA -HashAlgorithm SHA256
   ```

8. Utilize o comando do **certificado de exportação** para exportá-lo para um ficheiro de certificado.

   ```
   Export-Certificate -Cert $cert -FilePath "C:\Reporting\MSGraph_ReportingAPI.cer"

   ```

## <a name="get-data-using-the-azure-active-directory-reporting-api-with-certificates"></a>Obter dados com a API de relatórios do Azure Active Directory com certificados

1. Navegue para o [portal Azure](https://portal.azure.com), selecione **Azure Ative Directory,** em seguida, selecione **os registos** da App e escolha a sua aplicação na lista. 

2. Selecione **Certificados & segredos** sob a secção **Gerir** na lâmina de registo da aplicação e selecione **Certificado de Upload**.

3. Selecione o ficheiro de certificado do passo anterior e selecione **Adicionar**. 

4. Note o ID de aplicação e a impressão digital do certificado que acabou de registar com a sua aplicação. Para encontrar a impressão digital, a partir da sua página de candidatura no portal, vá a **Certificados & segredos** sob a secção **Gerir.** A impressão digital estará na lista de **Certificados.**

5. Abra o manifesto de aplicação no editor de manifesto inline e verifique se a propriedade *keyCredencia* é atualizada com a sua nova informação de certificado, como mostrado abaixo - 

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
6. Agora, você pode obter um sinal de acesso para a MS Graph API usando este certificado. Utilize o **Get-MSCloudIdMSGraphAccessTokenFromCert** cmdlet do módulo MSCloudIdUtils PowerShell, passando o ID da aplicação e a impressão digital que obteve do passo anterior. 

   ![Portal do Azure](./media/tutorial-access-api-with-certificates/getaccesstoken.png)

7. Utilize o símbolo de acesso no seu script PowerShell para consultar a API do Gráfico. Utilize o cmdlet **Invoke-MSCloudIdMSGraphQuery** dos MSCloudIDUtils para enumerar as signins e o ponto final do directórioAudits. Este cmdlet lida com resultados de várias páginas e envia esses resultados para o pipeline PowerShell.

8. Consulta do ponto final do directórioAudits para recuperar os registos de auditoria. 
   ![Portal do Azure](./media/tutorial-access-api-with-certificates/query-directoryAudits.png)

9. Consulta do ponto final das placas para recuperar os registos de login.
    ![Portal do Azure](./media/tutorial-access-api-with-certificates/query-signins.png)

10. Agora pode optar por exportar estes dados para um CSV e economizar para um sistema SIEM. Também pode encapsular o script numa tarefa agendada para obter dados do Azure AD a partir do seu inquilino periodicamente, sem ter de armazenar chaves de aplicação no código de origem. 

## <a name="next-steps"></a>Passos seguintes

* [Obter uma primeira impressão das APIs de relatórios](concept-reporting-api.md)
* [Referência da Auditoria API](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/directoryaudit) 
* [Relatório de atividade de inatividade referência API](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/signin)
