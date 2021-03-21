---
title: Tutorial para configurar Azure Ative Directy B2C com Jumio
titleSuffix: Azure AD B2C
description: Neste tutorial, configura o Azure Ative Directory B2C com o Jumio para verificação automatizada de ID, salvaguardando os dados dos clientes.
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 08/20/2020
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: 66ec0d4b09dc983eb898d63d45b3dd7cab291c4c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96928669"
---
# <a name="tutorial-for-configuring-jumio-with-azure-active-directory-b2c"></a>Tutorial para configurar Jumio com Azure Ative Directory B2C

Neste tutorial de amostra, fornecemos orientações sobre como integrar o Azure Ative Directory B2C (Azure AD B2C) com [o Jumio](https://www.jumio.com/). O Jumio é um serviço de verificação de identificação que permite a verificação automática de ID em tempo real para ajudar a salvaguardar os dados dos clientes.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, vai precisar de:

- Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)

- Um [inquilino Azure AD B2C](./tutorial-create-tenant.md) que está ligado à sua assinatura Azure.

## <a name="scenario-description"></a>Descrição do cenário

A integração jumio inclui os seguintes componentes:

- Azure AD B2C: O servidor de autorização responsável pela verificação das credenciais do utilizador. Também é conhecido como o fornecedor de identidade.

- Jumio: O serviço que pega nos dados de identificação fornecidos pelo utilizador e os verifica.

- API de Repouso Intermédio: A API que implementa a integração entre o Azure AD B2C e o serviço Jumio.

- Armazenamento Azure Blob: O serviço que fornece ficheiros de UI personalizados às políticas Azure AD B2C.

O seguinte diagrama de arquitetura mostra a implementação.

![Diagrama da arquitetura de uma integração Azure AD B2C com Jumio.](./media/partner-jumio/jumio-architecture-diagram.png)

|Passo | Description |
|:-----| :-----------|
| 1. | O utilizador chega a uma página para iniciar sessão ou iniciar sessão para criar uma conta. Azure AD B2C recolhe os atributos do utilizador.
| 2. | Azure AD B2C chama a API de camada média e transmite os atributos do utilizador.
| 3. | A API de camada média recolhe atributos do utilizador e transforma-os num formato que a API jumio pode consumir. Depois envia os atributos ao Jumio.
| 4. | Depois de Jumio consumir a informação e processá-la, devolve o resultado à API de camada média.
| 5. | A API de camada média processa a informação e envia informações relevantes para a Azure AD B2C.
| 6. | Azure AD B2C recebe informações da API de camada média. Se mostrar uma resposta de falha, é apresentada uma mensagem de erro ao utilizador. Se mostrar uma resposta de sucesso, o utilizador é autenticado e escrito no diretório.

## <a name="sign-up-with-jumio"></a>Inscreva-se no Jumio

Para criar uma conta Jumio, contacte [o Jumio.](https://www.jumio.com/contact/)

## <a name="configure-azure-ad-b2c-with-jumio"></a>Configure Azure AD B2C com Jumio

Depois de criar uma conta Jumio, utilize a conta para configurar a Azure AD B2C. As seguintes secções descrevem o processo em sequência.

### <a name="deploy-the-api"></a>Implementar a API

Implementar o [código API](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Jumio/API/Jumio.Api) fornecido para um serviço Azure. Pode publicar o código a partir do Visual Studio, seguindo [estas instruções.](/visualstudio/deployment/quickstart-deploy-to-azure)

>[!NOTE]
>Você precisará do URL do serviço implantado para configurar Azure AD com as configurações necessárias.

### <a name="deploy-the-client-certificate"></a>Implementar o certificado de cliente

1. Um certificado de cliente ajuda a proteger a chamada da API do Jumio. Crie um certificado auto-assinado utilizando o seguinte código de amostra PowerShell:

   ``` PowerShell
   $cert = New-SelfSignedCertificate -Type Custom -Subject "CN=Demo-SigningCertificate" -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.3") -KeyUsage DigitalSignature -KeyAlgorithm RSA -KeyLength 2048 -NotAfter (Get-Date).AddYears(2) -CertStoreLocation "Cert:\CurrentUser\My"
   $cert.Thumbprint
   $pwdText = "Your password"
   $pwd = ConvertTo-SecureString -String $pwdText -Force -AsPlainText
   Export-PfxCertificate -Cert $Cert -FilePath "{your-local-path}\Demo-SigningCertificate.pfx" -Password $pwd.

   ```

   O certificado é então exportado para o local especificado para ``{your-local-path}`` .

3. Importe o certificado para o Azure App Service seguindo as instruções [deste artigo.](../app-service/configure-ssl-certificate.md#upload-a-private-certificate)

### <a name="create-a-signingencryption-key"></a>Criar uma chave de assinatura/encriptação

Crie uma cadeia aleatória com um comprimento superior a 64 caracteres que contenha apenas letras e números.

Por exemplo: ``C9CB44D98642A7062A0D39B94B6CDC1E54276F2E7CFFBF44288CEE73C08A8A65``

Utilize o seguinte script PowerShell para criar a cadeia:

```PowerShell
-join ((0x30..0x39) + ( 0x41..0x5A) + ( 0x61..0x7A) + ( 65..90 ) | Get-Random -Count 64  | % {[char]$_})

```

### <a name="configure-the-api"></a>Configurar a API

Pode [configurar as definições de aplicações no Azure App Service](../app-service/configure-common.md#configure-app-settings). Com este método, pode configurar de forma segura as definições sem as verificar num repositório. Terá de fornecer as seguintes definições à API de Descanso:

| Definições da aplicação | Origem | Notas |
| :-------- | :------------| :-----------|
|JumioSettings:AuthUsername | Configuração da conta Jumio |     |
|JumioSettings:AuthPassword | Configuração da conta Jumio |     |
|AppSettings:SigningCertThumbprint|Impressão digital do certificado auto-assinado criado|  |
|AppSettings:IdTokenSigningKey| Chave de assinatura criada usando PowerShell | |
| AppSettings:IdTokenEncrypationKey |Chave de encriptação criada usando PowerShell
| AppSettings:IdTokenIssuer | Emitente a ser utilizado para o token JWT (é preferível um valor GUID) |
| AppSettings:IdTokenAudience  | Público a ser utilizado para o token JWT (é preferível um valor GUID) |
|AppSettings:BaseRedirectUrl | URL base da política Azure AD B2C | https://{your-tenant-name}.b2clogin.com/{your-application-id}|
| WEBSITE_LOAD_CERTIFICATES| Impressão digital do certificado auto-assinado criado |

### <a name="deploy-the-ui"></a>Implementar a UI

1. Crie um [recipiente de armazenamento de bolhas na sua conta de armazenamento.](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container)

2. Guarde os ficheiros de UI da [pasta UI](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Jumio/UI) no seu recipiente blob.

#### <a name="update-ui-files"></a>Atualizar ficheiros de UI

1. Nos ficheiros de UI, aceda à pasta [ocean_blue](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Jumio/UI/ocean_blue).

2. Abra cada ficheiro HTML.

3. Encontre e `{your-ui-blob-container-url}` substitua pelo URL do seu recipiente blob.

4. Encontre e `{your-intermediate-api-url}` substitua pelo URL do serviço de aplicações API intermédio.

>[!NOTE]
> Como uma boa prática, recomendamos que adicione a notificação de consentimento na página de recolha de atributos. Notifique os utilizadores de que a informação será enviada para serviços de terceiros para verificação de identidade.

### <a name="configure-the-azure-ad-b2c-policy"></a>Configure a política Azure AD B2C

1. Aceda à [política Azure AD B2C](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Jumio/Policies) na pasta Políticas.

2. Siga [este artigo](./custom-policy-get-started.md?tabs=applications#custom-policy-starter-pack) para descarregar o pacote de arranque [LocalAccounts](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/LocalAccounts).

3. Configure a política para o inquilino Azure AD B2C.

>[!NOTE]
>Atualize as políticas fornecidas para se relacionar com o seu inquilino específico.

## <a name="test-the-user-flow"></a>Teste o fluxo do utilizador

1. Abra o inquilino Azure AD B2C. No âmbito **de Políticas**, selecione Identity **Experience Framework**.

2. Selecione o **seu Signo De Assinatura de Inscrição** anteriormente criado.

3. Selecione **Executar o fluxo do utilizador** e, em seguida:

   a. Para **Aplicação**, selecione a aplicação registada (a amostra é JWT).

   b. Para **URL de resposta,** selecione o **URL de redirecionamento**.

   c. Selecione **Executar o fluxo do utilizador**.

4. Passe pelo fluxo de inscrição e crie uma conta.

5. O serviço Jumio será chamado durante o fluxo, após a criação do atributo do utilizador. Se o fluxo estiver incompleto, verifique se o utilizador não está guardado no diretório.

## <a name="next-steps"></a>Passos seguintes

Para obter informações adicionais, reveja os seguintes artigos:

- [Políticas personalizadas no Azure AD B2C](./custom-policy-overview.md)

- [Começar com políticas personalizadas em Azure AD B2C](./custom-policy-get-started.md?tabs=applications)
