---
title: Tutorial para AD Reportando API com certificados | Microsoft Docs
description: Este tutorial explica como utilizar a Azure AD Reporting API com credenciais de certificado para obter dados de diretórios sem intervenção do utilizador.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.custom: has-adal-ref
ms.openlocfilehash: c3443cb73e85fc69349e7293597a5f4a723959d3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "93130056"
---
# <a name="tutorial-get-data-using-the-azure-active-directory-reporting-api-with-certificates"></a>Tutorial: Obtenha dados utilizando o Diretório Ativo Azure reportando API com certificados

As [APIs de relatórios Azure Active Directory (Azure AD)](concept-reporting-api.md) proporcionam acesso programático aos dados através de um conjunto de APIs baseadas em REST. Pode chamar estas APIs a partir de várias linguagens e ferramentas de programação. Se pretender aceder à AZure AD Reporting API sem intervenção do utilizador, tem de configurar o seu acesso à utilização de certificados.

Neste tutorial, você aprende a usar um certificado de teste para aceder à API do Ms Graph para reportagem. Não recomendamos a utilização de certificados de teste num ambiente de produção. 

## <a name="prerequisites"></a>Pré-requisitos

1. Para aceder aos dados de inscrição, certifique-se de que tem um inquilino do Azure Ative Directory com uma licença premium (P1/P2). Veja [como começar com o Azure Ative Directory Premium](../fundamentals/active-directory-get-started-premium.md) para atualizar a sua edição do Azure Ative Directory. Note que se não tiver quaisquer dados de atividades antes da atualização, levará alguns dias para que os dados apareçam nos relatórios depois de atualizar para uma licença premium. 

2. Criar ou mudar para uma conta de utilizador no **administrador global,** **administrador de segurança,** **leitor de segurança** ou **função de leitor de relatórios** para o arrendatário. 

3. Preencha os [pré-requisitos para aceder ao Azure Ative Directory reportando API](howto-configure-prerequisites-for-reporting-api.md). 

4. Faça o download e instale [o Azure AD PowerShell V2](https://github.com/Azure/azure-docs-powershell-azuread/blob/master/docs-conceptual/azureadps-2.0/install-adv2.md).

5. Instale [MSCloudIdUtils](https://www.powershellgallery.com/packages/MSCloudIdUtils/). Este módulo disponibiliza vários cmdlets de utilitário, incluindo:
    - As bibliotecas ADAL necessárias para a autenticação
    - Tokens de acesso de utilizador, chaves de aplicação e certificados, através da ADAL
    - Graph API que processa resultados paginados

6. Se for a primeira vez que utiliza o módulo **executar Install-MSCloudIdUtilsModule,** caso contrário, importe-o utilizando o comando **PowerShell do módulo de importação.** A sua sessão deve ser semelhante a este ecrã: ![ Windows PowerShell](./media/tutorial-access-api-with-certificates/module-install.png)
  
7. Utilize o comando **New-SelfSignedCertificate** PowerShell para criar um certificado de teste.

   ```
   $cert = New-SelfSignedCertificate -Subject "CN=MSGraph_ReportingAPI" -CertStoreLocation "Cert:\CurrentUser\My" -KeyExportPolicy Exportable -KeySpec Signature -KeyLength 2048 -KeyAlgorithm RSA -HashAlgorithm SHA256
   ```

8. Utilize o **comando de Certificado de Exportação** para exportá-lo para um ficheiro de certificado.

   ```
   Export-Certificate -Cert $cert -FilePath "C:\Reporting\MSGraph_ReportingAPI.cer"

   ```

## <a name="get-data-using-the-azure-active-directory-reporting-api-with-certificates"></a>Obter dados com a API de relatórios do Azure Active Directory com certificados

1. Navegue até ao [portal Azure](https://portal.azure.com), selecione **Azure Ative Directory**, em seguida, selecione **as inscrições da App** e escolha a sua aplicação na lista. 

2. Selecione **Certificados & segredos** na secção **Gerir** a lâmina de registo da aplicação e selecione **o Certificado de Upload**.

3. Selecione o ficheiro de certificado do passo anterior e selecione **Adicionar**. 

4. Note o ID do formulário de aplicação e a impressão digital do certificado que acabou de registar na sua aplicação. Para encontrar a impressão digital, a partir da sua página de aplicação no portal, vá a **Certificados & segredos** na secção **Manage.** A impressão digital estará na lista **de certificados.**

5. Abra o manifesto de aplicação no editor manifesto inline e verifique se a propriedade *keyCredentials* é atualizada com as informações do seu novo certificado, conforme mostrado abaixo - 

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
6. Agora, você pode obter um token de acesso para a MS Graph API usando este certificado. Utilize o cmdlet **Get-MSCloudIdMSGraphAccessTokenFromCert** do módulo MSCloudIdUtils PowerShell, passando no ID da aplicação e na impressão digital que obteve a partir do passo anterior. 

   ![A screenshot mostra uma janela PowerShell com um comando que cria um token de acesso.](./media/tutorial-access-api-with-certificates/getaccesstoken.png)

7. Utilize o token de acesso no seu script PowerShell para consultar a API do gráfico. Utilize o cmdlet **Invoke-MSCloudIdMSGraphQuery** do MSCloudIDUtils para enumerar os signins e o ponto final do directórioAudits. Este cmdlet lida com resultados com várias páginas e envia esses resultados para o gasoduto PowerShell.

8. Consultar o directórioAudits ponto final para recuperar os registos de auditoria. 

   ![A screenshot mostra uma janela PowerShell com um comando para consultar o ponto final do directórioAudits usando o token de acesso de mais cedo neste procedimento.](./media/tutorial-access-api-with-certificates/query-directoryAudits.png)

9. Consultar o ponto final dos signins para recuperar os registos de inscrição.

    ![A screenshot mostra uma janela PowerShell com um comando para consultar o ponto final do signins usando o token de acesso de início neste procedimento.](./media/tutorial-access-api-with-certificates/query-signins.png)

10. Pode agora optar por exportar estes dados para um CSV e economizar para um sistema SIEM. Também pode encapsular o script numa tarefa agendada para obter dados do Azure AD a partir do seu inquilino periodicamente, sem ter de armazenar chaves de aplicação no código de origem. 

## <a name="next-steps"></a>Passos seguintes

* [Obter uma primeira impressão das APIs de relatórios](concept-reporting-api.md)
* [Referência de API de auditoria](/graph/api/resources/directoryaudit?view=graph-rest-beta) 
* [Referência de API do relatório de atividade de inscrição](/graph/api/resources/signin?view=graph-rest-beta)