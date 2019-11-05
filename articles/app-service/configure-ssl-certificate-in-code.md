---
title: Usar o certificado SSL no código do aplicativo – serviço de Azure App | Microsoft Docs
description: Saiba como usar certificados de cliente para se conectar a recursos remotos que os exigem.
services: app-service
documentationcenter: ''
author: cephalin
manager: gwallace
editor: ''
ms.service: app-service
ms.workload: web
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/16/2019
ms.author: cephalin
ms.reviewer: yutlin
ms.custom: seodec18
ms.openlocfilehash: 1f042f72f82d2198472fe81670c697c0c4b28321
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73513695"
---
# <a name="use-an-ssl-certificate-in-your-application-code-in-azure-app-service"></a>Usar um certificado SSL no código do aplicativo no serviço Azure App

O código do aplicativo do serviço de aplicativo pode atuar como um cliente e acessar um serviço externo que requer autenticação de certificado. Este guia de instruções mostra como usar certificados públicos ou privados no código do aplicativo.

Essa abordagem para usar certificados em seu código usa a funcionalidade SSL no serviço de aplicativo, o que exige que seu aplicativo esteja na camada **básica** ou acima. Como alternativa, você pode [incluir o arquivo de certificado em seu repositório de aplicativos](#load-certificate-from-file), mas não é uma prática recomendada para certificados privados.

Quando você permite que o serviço de aplicativo gerencie seus certificados SSL, você pode manter os certificados e o código do aplicativo separadamente e proteger seus dados confidenciais.

## <a name="prerequisites"></a>Pré-requisitos

Para seguir este guia de instruções:

- [Criar uma aplicação do Serviço de Aplicações](/azure/app-service/)
- [Adicionar um certificado ao seu aplicativo](configure-ssl-certificate.md)

## <a name="find-the-thumbprint"></a>Localizar a impressão digital

No <a href="https://portal.azure.com" target="_blank">portal do Azure</a>, no menu à esquerda, selecione **serviços de aplicativos** >  **\<nome-do-aplicativo >** .

No painel de navegação à esquerda do seu aplicativo, selecione **configurações de TLS/SSL**e, em seguida, selecione **certificados de chave privada (. pfx)** ou **certificados de chave pública (. cer)** .

Localize o certificado que você deseja usar e copie a impressão digital.

![Copiar a impressão digital do certificado](./media/configure-ssl-certificate/create-free-cert-finished.png)

## <a name="load-the-certificate"></a>Carregar o certificado

Para usar um certificado em seu código do aplicativo, adicione sua impressão digital à configuração do aplicativo `WEBSITE_LOAD_CERTIFICATES`, executando o seguinte comando no <a target="_blank" href="https://shell.azure.com" >Cloud Shell</a>:

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_LOAD_CERTIFICATES=<comma-separated-certificate-thumbprints>
```

Para tornar todos os certificados acessíveis, defina o valor como `*`.

> [!NOTE]
> Essa configuração coloca os certificados especificados no repositório [User\My atual](/windows-hardware/drivers/install/local-machine-and-current-user-certificate-stores) para a maioria dos tipos de preço, mas na camada **isolada** (ou seja, o aplicativo é executado em um [ambiente do serviço de aplicativo](environment/intro.md)), ele coloca os certificados no [Machine\My local](/windows-hardware/drivers/install/local-machine-and-current-user-certificate-stores) armazenadas.
>

Os certificados configurados agora estão prontos para serem usados pelo seu código.

## <a name="load-the-certificate-in-code"></a>Carregar o certificado no código

Depois que o certificado estiver acessível, acesse- C# o no código pela impressão digital do certificado. O código a seguir carrega um certificado com o `E661583E8FABEF4C0BEF694CBC41C28FB81CD870`de impressão digital.

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

Embora você esteja carregando o arquivo diretamente no seu código .NET, a biblioteca ainda verifica se o perfil do usuário atual está carregado. Para carregar o perfil do usuário atual, defina a configuração do aplicativo `WEBSITE_LOAD_USER_PROFILE` com o seguinte comando na <a target="_blank" href="https://shell.azure.com" >Cloud Shell</a>.

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

## <a name="more-resources"></a>Mais recursos

* [Proteger um nome DNS personalizado com uma associação SSL](configure-ssl-bindings.md)
* [Impor HTTPS](configure-ssl-bindings.md#enforce-https)
* [Impor o TLS 1.1/1.2](configure-ssl-bindings.md#enforce-tls-versions)
* [Perguntas frequentes: certificados do serviço de aplicativo](https://docs.microsoft.com/azure/app-service/faq-configuration-and-management/)
