---
title: Utilizar certificado SSL de cliente no código da aplicação - serviço de aplicações do Azure | Documentos da Microsoft
description: Saiba como utilizar certificados de cliente para ligar a recursos remotos que dele necessitem.
services: app-service\web
documentationcenter: ''
author: cephalin
manager: cfowler
editor: ''
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/29/2019
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: ead1892062912840c9931ae60d11c90975ad26ac
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/04/2019
ms.locfileid: "66475123"
---
# <a name="use-an-ssl-certificate-in-your-application-code-in-azure-app-service"></a>Utilizar um certificado SSL no código da aplicação no serviço de aplicações do Azure

Este guia de procedimentos mostra como utilizar certificados públicos ou privados no código da aplicação. Um exemplo de caso de utilização é a sua aplicação acede a um serviço externo que exija a autenticação de certificado.

Essa abordagem para a utilização de certificados no seu código usa o SSL funcionalidade no serviço de aplicações, que requer a sua aplicação em **básica** escalão ou superior. Em alternativa, pode [incluir o ficheiro de certificado no seu repositório de aplicação](#load-certificate-from-file), mas não é uma prática recomendada para certificados privados.

Quando permite que o serviço de aplicações, gerir os certificados SSL, pode manter os certificados e o código da aplicação em separado e salvaguardar os seus dados confidenciais.

## <a name="upload-a-private-certificate"></a>Carregar um certificado privado

Antes de carregar um certificado privado, certifique-se [atende a todos os requisitos](app-service-web-tutorial-custom-ssl.md#prepare-a-private-certificate), exceto pelo fato de ele não precisa ser configurado para autenticação de servidor.

Quando estiver pronto para carregar, execute o seguinte comando <a target="_blank" href="https://shell.azure.com" >Cloud Shell</a>.

```azurecli-interactive
az webapp config ssl upload --name <app-name> --resource-group <resource-group-name> --certificate-file <path-to-PFX-file> --certificate-password <PFX-password> --query thumbprint
```

Copie o thumbprint do certificado e ver [disponibilizar o certificado](#make-the-certificate-accessible).

## <a name="upload-a-public-certificate"></a>Carregar um certificado público

Certificados públicos são suportados os *. cer* formato. Para carregar um certificado público, o <a href="https://portal.azure.com" target="_blank">portal do Azure</a>e navegue para a aplicação.

Clique em **definições de SSL** > **certificados públicos (. cer)**  > **carregar certificado público** no painel de navegação à esquerda da sua aplicação.

Na **nome**, escreva um nome para o certificado. Na **ficheiro de certificado CER**, selecione o seu ficheiro CER.

Clique em **Carregar**.

![Carregar o certificado público](./media/app-service-web-ssl-cert-load/private-cert-upload.png)

Depois do certificado é carregado, copie o thumbprint do certificado e ver [disponibilizar o certificado](#make-the-certificate-accessible).

## <a name="import-an-app-service-certificate"></a>Importar um certificado de serviço de aplicações

Ver [comprar e configurar um certificado SSL para o serviço de aplicações do Azure](web-sites-purchase-ssl-web-site.md).

Depois do certificado é importado, copie o thumbprint do certificado e ver [disponibilizar o certificado](#make-the-certificate-accessible).

## <a name="make-the-certificate-accessible"></a>Disponibilizar o certificado

Para utilizar um certificado carregado ou importado no seu código de aplicação, tornar sua impressão digital acessível com o `WEBSITE_LOAD_CERTIFICATES` definição de aplicação, ao executar o seguinte comando no <a target="_blank" href="https://shell.azure.com" >Cloud Shell</a>:

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_LOAD_CERTIFICATES=<comma-separated-certificate-thumbprints>
```

Para disponibilizar todos os seus certificados, defina o valor como `*`.

> [!NOTE]
> Esta definição coloca os certificados especificados no [User\My atual](/windows-hardware/drivers/install/local-machine-and-current-user-certificate-stores) loja para a maioria dos escalões de preço, contudo, o **Isolated** camada (ou seja, a aplicação é executada num [App Service Environment](environment/intro.md)), ele coloca os certificados no [Local Machine\My](/windows-hardware/drivers/install/local-machine-and-current-user-certificate-stores) armazenar.
>

![Configurar a definição de aplicação](./media/app-service-web-ssl-cert-load/configure-app-setting.png)

Quando terminar, clique em **Guardar**.

Os certificados configurados estão agora prontos para ser utilizado pelo seu código.

## <a name="load-the-certificate-in-code"></a>Carregar o certificado no código

Assim que o certificado estiver acessível, acessá-lo em código c#, o thumbprint do certificado. O seguinte código carrega um certificado com o thumbprint `E661583E8FABEF4C0BEF694CBC41C28FB81CD870`.

```csharp
using System;
using System.Security.Cryptography.X509Certificates;

...
X509Store certStore = new X509Store(StoreName.My, StoreLocation.CurrentUser);
certStore.Open(OpenFlags.ReadOnly);
X509Certificate2Collection certCollection = certStore.Certificates.Find(
                            X509FindType.FindByThumbprint,
                            // Replace below with your certificate's thumbprint
                            "E661583E8FABEF4C0BEF694CBC41C28FB81CD870",
                            false);
// Get the first cert with the thumbprint
if (certCollection.Count > 0)
{
    X509Certificate2 cert = certCollection[0];
    // Use certificate
    Console.WriteLine(cert.FriendlyName);
}
certStore.Close();
...
```

<a name="file"></a>
## <a name="load-certificate-from-file"></a>Certificado de carregamento do ficheiro

Se precisar de carregar um ficheiro de certificado do seu diretório do aplicativo, é melhor para carregá-lo usando [FTPS](deploy-ftp.md) em vez de [Git](deploy-local-git.md), por exemplo. Deve manter dados confidenciais, como um certificado privado fora do controlo de origem.

Mesmo que está carregando o arquivo diretamente no seu código .NET, a biblioteca ainda verifica se o perfil de utilizador atual é carregado. Para carregar perfil do usuário atual, defina o `WEBSITE_LOAD_USER_PROFILE` definição de aplicação com o seguinte comando no <a target="_blank" href="https://shell.azure.com" >Cloud Shell</a>.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_LOAD_USER_PROFILE=1
```

Assim que esta definição estiver definida, o seguinte C# exemplo carrega um certificado chamado `mycert.pfx` partir do `certs` diretório do repositório da sua aplicação.

```csharp
using System;
using System.Security.Cryptography.X509Certificates;

...
// Replace the parameter with "~/<relative-path-to-cert-file>".
string certPath = Server.MapPath("~/certs/mycert.pfx");

X509Certificate2 cert = GetCertificate(certPath, signatureBlob.Thumbprint);
...
```
