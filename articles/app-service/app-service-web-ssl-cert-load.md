---
title: Usar certificado SSL do cliente no código do aplicativo – serviço de Azure App | Microsoft Docs
description: Saiba como usar certificados de cliente para se conectar a recursos remotos que os exigem.
services: app-service\web
documentationcenter: ''
author: cephalin
manager: cfowler
editor: ''
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 05/29/2019
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: 0c8c270681794621b2a12671d4bcf350cd6cc4d8
ms.sourcegitcommit: d7689ff43ef1395e61101b718501bab181aca1fa
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/06/2019
ms.locfileid: "71981121"
---
# <a name="use-an-ssl-certificate-in-your-application-code-in-azure-app-service"></a>Usar um certificado SSL no código do aplicativo no serviço Azure App

Este guia de instruções mostra como usar certificados públicos ou privados no código do aplicativo. Um exemplo do caso de uso é que seu aplicativo acessa um serviço externo que requer autenticação de certificado.

Essa abordagem para usar certificados em seu código usa a funcionalidade SSL no serviço de aplicativo, o que exige que seu aplicativo esteja na camada **básica** ou acima. Como alternativa, você pode [incluir o arquivo de certificado em seu repositório de aplicativos](#load-certificate-from-file), mas não é uma prática recomendada para certificados privados.

Quando você permite que o serviço de aplicativo gerencie seus certificados SSL, você pode manter os certificados e o código do aplicativo separadamente e proteger seus dados confidenciais.

## <a name="upload-a-private-certificate"></a>Carregar um certificado privado

Antes de carregar um certificado privado, verifique se [ele atende a todos os requisitos](app-service-web-tutorial-custom-ssl.md#prepare-a-private-certificate), exceto que ele não precisa ser configurado para autenticação do servidor.

Quando estiver pronto para carregar, execute o seguinte comando na <a target="_blank" href="https://shell.azure.com" >Cloud Shell</a>.

```azurecli-interactive
az webapp config ssl upload --name <app-name> --resource-group <resource-group-name> --certificate-file <path-to-PFX-file> --certificate-password <PFX-password> --query thumbprint
```

Copie a impressão digital do certificado e veja [tornar o certificado acessível](#make-the-certificate-accessible).

## <a name="upload-a-public-certificate"></a>Carregar um certificado público

Há suporte para certificados públicos no formato *. cer* . Para carregar um certificado público, o <a href="https://portal.azure.com" target="_blank">portal do Azure</a>e navegar até seu aplicativo.

Clique em **configurações SSL** > **certificados públicos (. cer)**  > **carregar certificado público** do painel de navegação esquerdo do seu aplicativo.

Em **nome**, digite um nome para o certificado. No **arquivo de certificado cer**, selecione o arquivo cer.

Clique em **Carregar**.

![Carregar certificado público](./media/app-service-web-ssl-cert-load/private-cert-upload.png)

Depois que o certificado for carregado, copie a impressão digital do certificado e veja [tornar o certificado acessível](#make-the-certificate-accessible).

## <a name="import-an-app-service-certificate"></a>Importar um certificado do serviço de aplicativo

Consulte [comprar e configurar um certificado SSL para Azure app serviço](web-sites-purchase-ssl-web-site.md).

Depois que o certificado for importado, copie a impressão digital do certificado e veja [tornar o certificado acessível](#make-the-certificate-accessible).

## <a name="make-the-certificate-accessible"></a>Tornar o certificado acessível

Para usar um certificado carregado ou importado no código do aplicativo, torne sua impressão digital acessível com a configuração de aplicativo `WEBSITE_LOAD_CERTIFICATES`, executando o seguinte comando no <a target="_blank" href="https://shell.azure.com" >Cloud Shell</a>:

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_LOAD_CERTIFICATES=<comma-separated-certificate-thumbprints>
```

Para tornar todos os certificados acessíveis, defina o valor como `*`.

> [!NOTE]
> Essa configuração coloca os certificados especificados no repositório [User\My atual](/windows-hardware/drivers/install/local-machine-and-current-user-certificate-stores) para a maioria dos tipos de preço, mas se seu aplicativo estiver em execução na camada **isolada** (ou seja, o aplicativo é executado em um [ambiente do serviço de aplicativo](environment/intro.md)), talvez seja necessário fazer check-in no [local Machine\My](/windows-hardware/drivers/install/local-machine-and-current-user-certificate-stores) Store em vez disso.
>

![Definir configuração de aplicativo](./media/app-service-web-ssl-cert-load/configure-app-setting.png)

Quando terminar, clique em **Guardar**.

Os certificados configurados agora estão prontos para serem usados pelo seu código.

## <a name="load-the-certificate-in-code"></a>Carregar o certificado no código

Depois que o certificado estiver acessível, acesse- C# o no código pela impressão digital do certificado. O código a seguir carrega um certificado com a impressão digital `E661583E8FABEF4C0BEF694CBC41C28FB81CD870`.

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
## <a name="load-certificate-from-file"></a>Carregar o certificado do arquivo

Se você precisar carregar um arquivo de certificado do diretório do aplicativo, é melhor carregá-lo usando [FTPS](deploy-ftp.md) em vez do [git](deploy-local-git.md), por exemplo. Você deve manter dados confidenciais como um certificado privado fora do controle do código-fonte.

Embora você esteja carregando o arquivo diretamente no seu código .NET, a biblioteca ainda verifica se o perfil do usuário atual está carregado. Para carregar o perfil de usuário atual, defina a configuração de aplicativo `WEBSITE_LOAD_USER_PROFILE` com o comando a seguir na <a target="_blank" href="https://shell.azure.com" >Cloud Shell</a>.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_LOAD_USER_PROFILE=1
```

Quando essa configuração é definida, o exemplo C# a seguir carrega um certificado chamado `mycert.pfx` do diretório `certs` do repositório do aplicativo.

```csharp
using System;
using System.Security.Cryptography.X509Certificates;

...
// Replace the parameter with "~/<relative-path-to-cert-file>".
string certPath = Server.MapPath("~/certs/mycert.pfx");

X509Certificate2 cert = GetCertificate(certPath, signatureBlob.Thumbprint);
...
```
