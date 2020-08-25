---
title: Tutorial para configurar Azure Ative Directy B2C com Jumio
titleSuffix: Azure AD B2C
description: Tutorial para configurar O Diretório Ativo B2C do Azure com a Jumio para verificação automatizada de ID, salvaguardando os dados dos clientes
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 08/20/2020
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: 47e06c84eb2e0463b31b7bdea5897ceca4339419
ms.sourcegitcommit: d39f2cd3e0b917b351046112ef1b8dc240a47a4f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/25/2020
ms.locfileid: "88817803"
---
# <a name="tutorial-for-configuring-jumio-with-azure-active-directory-b2c"></a>Tutorial para configurar Jumio com Azure Ative Directory B2C

Neste tutorial de amostra, fornecemos orientações sobre como integrar o Azure AD B2C com [o Jumio.](https://www.jumio.com/) O Jumio é um serviço de verificação de identificação, que permite a verificação automática de ID em tempo real, salvaguardando os dados dos clientes.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, vai precisar de:

- Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)

- Um [inquilino Azure AD B2C.](https://review.docs.microsoft.com/azure/active-directory-b2c/tutorial-create-tenant) O inquilino está ligado à sua assinatura Azure.

## <a name="scenario-description"></a>Descrição do cenário

A integração jumio inclui os seguintes componentes:

- Azure AD B2C – O servidor de autorização, responsável pela verificação das credenciais do utilizador, também conhecido como fornecedor de identidade

- Jumio – O serviço que pega nos dados de identificação fornecidos pelo utilizador e o verifica.

- API de Repouso Intermédio – Esta API implementa a integração entre o Azure AD B2C e o serviço Jumio.

- Armazenamento blob – Usado para fornecer ficheiros de UI personalizados às políticas Azure AD B2C.

O seguinte diagrama de arquitetura mostra a implementação.

![Screenshot para jumio-arquitetura-diagrama](./media/partner-jumio/jumio-architecture-diagram.png)

|Passo | Descrição |
|:-----| :-----------|
| 1. | O utilizador chega a uma página de login. Os utilizadores selecionam o sesurá-se para criar uma nova conta e introduzir informações na página. Azure AD B2C recolhe os atributos do utilizador.
| 2. | Azure AD B2C chama a API de camada média e transmite os atributos do utilizador.
| 3. | A API de camada média recolhe atributos do utilizador e transforma-os num formato que a API jumio poderia consumir. Depois manda-o para Jumio.
| 4. | Depois de Jumio consumir a informação e processá-la, ela devolve o resultado à API de camada média.
| 5. | A API de camada média processa a informação e envia de volta informações relevantes para Azure AD B2C.
| 6. | Azure AD B2C recebe informações da API de camada média. Se mostrar uma resposta de Falha, é apresentada uma mensagem de erro ao utilizador. Se mostrar uma resposta de Sucesso, o utilizador é autenticado e escrito no diretório.

## <a name="onboard-with-jumio"></a>A bordo com Jumio

1. Para criar uma conta Jumio, contacte [o Jumio](https://www.jumio.com/contact/)

2. Uma vez criada uma conta, a informação é utilizada na configuração da API. As seguintes secções descrevem o processo.

## <a name="configure-azure-ad-b2c-with-jumio"></a>Configure Azure AD B2C com Jumio

### <a name="part-1---deploy-the-api"></a>Parte 1 - Implantar a API

Implementar o [código API](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Jumio/API/Jumio.Api) fornecido para um serviço Azure. O código pode ser publicado a partir do Visual Studio, seguindo estas [instruções](https://docs.microsoft.com/visualstudio/deployment/quickstart-deploy-to-azure?view=vs-2019).

>[!NOTE]
>Você precisará do URL do serviço implantado para configurar Azure AD com as configurações necessárias.

### <a name="part-2---deploy-the-client-certificate"></a>Parte 2 - Implementar o certificado de cliente

1. A chamada da API do Jumio está protegida por um certificado de cliente. Crie um certificado auto-assinado utilizando o código de amostra PowerShell mencionado abaixo:

``` PowerShell
$cert = New-SelfSignedCertificate -Type Custom -Subject "CN=Demo-SigningCertificate" -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.3") -KeyUsage DigitalSignature -KeyAlgorithm RSA -KeyLength 2048 -NotAfter (Get-Date).AddYears(2) -CertStoreLocation "Cert:\CurrentUser\My"
$cert.Thumbprint
$pwdText = "Your password"
$pwd = ConvertTo-SecureString -String $pwdText -Force -AsPlainText
Export-PfxCertificate -Cert $Cert -FilePath "{your-local-path}\Demo-SigningCertificate.pfx" -Password $pwd.

```

2. O certificado será então exportado para o local especificado para ``your-local-path`` {}.

3. Seguindo as instruções mencionadas neste [documento,](https://docs.microsoft.com/azure/app-service/configure-ssl-certificate#upload-a-private-certificate)importe o certificado para o serviço Azure App.

### <a name="part-3---create-a-signingencryption-key"></a>Parte 3 - Criar uma chave de assinatura/encriptação

Crie uma cadeia aleatória com um comprimento superior a 64 caracteres que contenha apenas alfabetos ou números.

Por exemplo: ``C9CB44D98642A7062A0D39B94B6CDC1E54276F2E7CFFBF44288CEE73C08A8A65``

Utilize o script PowerShell abaixo para criar um valor alfanumérico de 64 caracteres.

```PowerShell
-join ((0x30..0x39) + ( 0x41..0x5A) + ( 0x61..0x7A) + ( 65..90 ) | Get-Random -Count 64  | % {[char]$_})

```

### <a name="part-4---configure-the-api"></a>Parte 4 - Configurar a API

As definições de aplicação podem ser [configuradas no serviço app em Azure.](https://docs.microsoft.com/azure/app-service/configure-common#configure-app-settings) Com este método, as definições podem ser configuradas de forma segura sem as verificar num repositório. Terá de fornecer as seguintes definições à API de Descanso:

| Definições da aplicação | Origem | Notas |
| :-------- | :------------| :-----------|
|JumioSettings:AuthUsername | Configuração da conta Jumio |     |
|JumioSettings:AuthPassword | Configuração da conta Jumio |     |
|AppSettings:SigningCertThumbprint|Impressão digital do certificado auto-assinado criado|  |
|AppSettings:IdTokenSigningKey| Chave de assinatura criada usando PowerShell | |
| AppSettings:IdTokenEncrypationKey |Chave de encriptação criada usando PowerShell
| AppSettings:IdTokenIssuer | Emitente a ser utilizado para o token JWT (é preferível um valor orientador) |
| AppSettings:IdTokenAudience  | Público a ser usado para o símbolo JWT (um valor guia é preferido) |
|AppSettings:BaseRedirectUrl | A url base da política B2C | https://{your-tenant-name}.b2clogin.com/{your-application-id}|
| WEBSITE_LOAD_CERTIFICATES| Impressão digital do certificado auto-assinado criado |

### <a name="part-5---deploy-the-ui"></a>Parte 5 - Implementar a UI

1. Crie um [recipiente de armazenamento de bolhas na sua conta de armazenamento.](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal#create-a-container)

2. Guarde os ficheiros de UI da [pasta UI](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Jumio/UI) para o seu recipiente blob.

#### <a name="update-ui-files"></a>Atualizar ficheiros UI

1. Nos ficheiros de UI, vá à pasta [ocean_blue](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Jumio/UI/ocean_blue)

2. Abra cada ficheiro html.

3. Encontre e substitua {your-ui-blob-container-url} por url} do seu URL do recipiente blob.

4. Encontre e substitua {your-intermediate-api-url} por URL do serviço de aplicações API intermédio.

>[!NOTE]
> Como uma boa prática, recomendamos que os clientes adicionem a notificação de consentimento na página de recolha de atributos. Notifique os utilizadores de que as informações serão enviadas para serviços de terceiros para verificação de identidade.

### <a name="part-6---configure-the-azure-ad-b2c-policy"></a>Parte 6 - Configurar a política Azure AD B2C

1. Aceda à [política Azure AD B2C](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Jumio/Policies) na pasta Políticas.

2. Siga este [documento](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-get-started?tabs=applications#custom-policy-starter-pack) para baixar o [pacote de arranque LocalAccounts](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/LocalAccounts)

3. Configure a política para o inquilino Azure AD B2C.

>[!NOTE]
>Atualize as políticas fornecidas para se relacionar com o seu inquilino específico.

## <a name="test-the-user-flow"></a>Teste o fluxo do utilizador

1. Abra o inquilino Azure AD B2C e em Políticas selecione **Quadro de Experiência de Identidade.**

2. Selecione o **seu Signo De Assinatura de Inscrição**anteriormente criado.

3. Selecione **Executar o fluxo do utilizador** e selecione as definições:

   a. **Aplicação**: selecione a aplicação registada (a amostra é JWT)

   b. **URL de resposta**: selecione o **URL de redirecionamento**

   c. Selecione **Executar o fluxo do utilizador**.

4. Passe pelo fluxo de inscrição e crie uma conta

5. O serviço Jumio será chamado durante o fluxo, após a criação do atributo do utilizador. Se o fluxo estiver incompleto, verifique se o utilizador não está guardado no diretório.

## <a name="next-steps"></a>Passos seguintes

Para obter informações adicionais, reveja os seguintes artigos:

- [Políticas personalizadas no Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-overview)

- [Começar com políticas personalizadas em Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-get-started?tabs=applications)
