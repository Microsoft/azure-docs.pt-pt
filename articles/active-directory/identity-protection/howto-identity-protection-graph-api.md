---
title: Microsoft Graph PowerShell SDK e Azure Ative Directory Identity Protection
description: Saiba como consultar as deteções de risco do Microsoft Graph e informações associadas a partir do Azure Ative Directory
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: how-to
ms.date: 01/25/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2db8cfe652c0fca4b68b00d846e345c1b60cd05d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98880241"
---
# <a name="azure-active-directory-identity-protection-and-the-microsoft-graph-powershell-sdk"></a>Azure Ative Directory Identity Protection e o Microsoft Graph PowerShell SDK

Microsoft Graph é o ponto final unificado da Microsoft EPI e a casa das APIs de [Proteção de Identidade do Diretório Ativo Azure.](./overview-identity-protection.md) Este artigo irá mostrar-lhe como usar o [Microsoft Graph PowerShell SDK](/graph/powershell/get-started) para obter detalhes de utilizador arriscados usando o PowerShell. As organizações que pretendam consultar diretamente as APIs do Gráfico microsoft podem usar o artigo, [Tutorial: Identificar e corrigir riscos usando APIs do Microsoft Graph](/graph/tutorial-riskdetection-api) para iniciar essa viagem.


## <a name="connect-to-microsoft-graph"></a>Ligue-se ao Gráfico da Microsoft

Existem quatro passos para aceder aos dados de Proteção de Identidade através do Microsoft Graph:

- [Criar um certificado](#create-a-certificate)
- [Criar um novo registo de aplicações](#create-a-new-app-registration)
- [Configure permissões da API](#configure-api-permissions)
- [Configure uma credencial válida](#configure-a-valid-credential)

### <a name="create-a-certificate"></a>Criar um certificado

Num ambiente de produção, utilizaria um certificado da sua Autoridade de Certificados de produção, mas nesta amostra utilizaremos um certificado auto-assinado. Criar e exportar o certificado utilizando os seguintes comandos PowerShell.

```powershell
$cert = New-SelfSignedCertificate -Subject "CN=MSGraph_ReportingAPI" -CertStoreLocation "Cert:\CurrentUser\My" -KeyExportPolicy Exportable -KeySpec Signature -KeyLength 2048 -KeyAlgorithm RSA -HashAlgorithm SHA256
Export-Certificate -Cert $cert -FilePath "C:\Reporting\MSGraph_ReportingAPI.cer"
```

### <a name="create-a-new-app-registration"></a>Criar um novo registo de aplicações

1. No portal Azure, consulte as inscrições da **Azure Ative Directory**  >  **App**.
1. Selecione **Novo registo**.
1. Na página **Criar,** execute os seguintes passos:
   1. Na caixa de texto **Name,** digite um nome para a sua aplicação (por exemplo: Azure AD Risk Detection API).
   1. Nos **tipos de conta suportados,** selecione o tipo de contas que utilizarão as APIs.
   1. Selecione **Registar**.
1. Tome nota do ID de **Aplicação (cliente)** e **ID do Diretório (inquilino),** pois vai precisar destes itens mais tarde.

### <a name="configure-api-permissions"></a>Configure permissões da API

Neste exemplo, configuramos permissões de aplicação permitindo que esta amostra seja utilizada sem vigilância. Se conceder permissões a um utilizador que será iniciado, escolha permissões delegadas. Mais informações sobre diferentes tipos de permissão podem ser encontradas no artigo, [permissões e consentimento na plataforma de identidade da Microsoft.](../develop/v2-permissions-and-consent.md#permission-types)

1. A partir da **Aplicação** que criou, selecione **permissões API.**
1. Na página de **permissões configuradas,** na barra de ferramentas na parte superior, clique em **Adicionar uma permissão**.
1. Na página **de acesso API,** clique em **Selecionar uma API.**
1. Na página **Select a API,** selecione **o Microsoft Graph** e, em seguida, clique em **Select**.
1. Na página de permissões da **API do Pedido:** 
   1. Selecione **permissões de aplicação**.
   1. Selecione as caixas de verificação ao lado `IdentityRiskEvent.Read.All` e `IdentityRiskyUser.Read.All` .
   1. **Selecione Permissões de adicionar**.
1. Selecione **Grant consentimento de administração para domínio** 

### <a name="configure-a-valid-credential"></a>Configure uma credencial válida

1. A partir da **Aplicação** que criou, selecione **Certificados & segredos.**
1. Nos **certificados,** selecione **o certificado de upload**.
   1. Selecione o certificado previamente exportado da janela que abre.
   1. Selecione **Adicionar**.
1. Tome nota da **impressão digital** do certificado, pois necessitará desta informação no passo seguinte.

## <a name="list-risky-users-using-powershell"></a>Lista utilizadores arriscados usando PowerShell

Para ativar a capacidade de consultar o Microsoft Graph, precisamos de instalar o `Microsoft.Graph` módulo na nossa janela PowerShell, utilizando o `Install-Module Microsoft.Graph` comando.

Modifique as seguintes variáveis para incluir as informações geradas nos passos anteriores e, em seguida, executá-las como um todo para obter detalhes de utilizador arriscados usando PowerShell.

```powershell
$ClientID       = "<your client ID here>"        # Application (client) ID gathered when creating the app registration
$tenantdomain   = "<your tenant domain here>"    # Directory (tenant) ID gathered when creating the app registration
$Thumbprint     = "<your client secret here>"    # Certificate thumbprint gathered when configuring your credential

Select-MgProfile -Name "beta"
  
Connect-MgGraph -ClientId $ClientID -TenantId $tenantdomain -CertificateThumbprint $Thumbprint

Get-MgRiskyUser -All
```

## <a name="next-steps"></a>Passos seguintes

- [Começa com o Microsoft Graph PowerShell SDK](/graph/powershell/get-started)
- [Tutorial: Identificar e corrigir riscos usando APIs de Gráficos da Microsoft](/graph/tutorial-riskdetection-api)
- [Descrição Geral do Microsoft Graph](https://developer.microsoft.com/graph/docs)
- [Ter acesso sem um utilizador](/graph/auth-v2-service)
- [Raiz do serviço de proteção de identidade Azure Ad](/graph/api/resources/identityprotectionroot)
- [Azure Active Directory Identity Protection](./overview-identity-protection.md)
