---
title: Usar certificado SSL no código
description: Saiba como usar certificados de cliente em seu código. Autentique com recursos remotos com um certificado de cliente ou execute tarefas criptográficas com eles.
ms.topic: article
ms.date: 11/04/2019
ms.reviewer: yutlin
ms.custom: seodec18
ms.openlocfilehash: d783b61c372c7d0f8cca13106bf297ab9b55c424
ms.sourcegitcommit: 265f1d6f3f4703daa8d0fc8a85cbd8acf0a17d30
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/02/2019
ms.locfileid: "74671882"
---
# <a name="use-an-ssl-certificate-in-your-code-in-azure-app-service"></a>Usar um certificado SSL em seu código no serviço Azure App

No código do aplicativo, você pode acessar os [certificados públicos ou privados adicionados ao serviço de aplicativo](configure-ssl-certificate.md). O código do aplicativo pode atuar como um cliente e acessar um serviço externo que requer autenticação de certificado ou pode precisar executar tarefas criptográficas. Este guia de instruções mostra como usar certificados públicos ou privados no código do aplicativo.

Essa abordagem para usar certificados em seu código usa a funcionalidade SSL no serviço de aplicativo, o que exige que seu aplicativo esteja na camada **básica** ou acima. Se seu aplicativo estiver em uma camada **gratuita** ou **compartilhada** , você poderá [incluir o arquivo de certificado em seu repositório de aplicativos](#load-certificate-from-file).

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

## <a name="make-the-certificate-accessible"></a>Tornar o certificado acessível

Para acessar um certificado em seu código do aplicativo, adicione sua impressão digital à configuração do aplicativo `WEBSITE_LOAD_CERTIFICATES`, executando o seguinte comando no <a target="_blank" href="https://shell.azure.com" >Cloud Shell</a>:

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_LOAD_CERTIFICATES=<comma-separated-certificate-thumbprints>
```

Para tornar todos os certificados acessíveis, defina o valor como `*`.

## <a name="load-certificate-in-windows-apps"></a>Carregar o certificado em aplicativos do Windows

A configuração do aplicativo `WEBSITE_LOAD_CERTIFICATES` torna os certificados especificados acessíveis para seu aplicativo hospedado do Windows no repositório de certificados do Windows e o local depende do [tipo de preço](overview-hosting-plans.md):

- Camada **isolada** -no [Machine\My local](/windows-hardware/drivers/install/local-machine-and-current-user-certificate-stores). 
- Todas as outras camadas-no [User\My atual](/windows-hardware/drivers/install/local-machine-and-current-user-certificate-stores).

No C# código, você acessa o certificado pela impressão digital do certificado. O código a seguir carrega um certificado com o `E661583E8FABEF4C0BEF694CBC41C28FB81CD870`de impressão digital.

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

No código Java, você acessa o certificado do repositório "Windows-MY" usando o campo nome comum da entidade (consulte [certificado de chave pública](https://en.wikipedia.org/wiki/Public_key_certificate)). O código a seguir mostra como carregar um certificado de chave privada:

```java
import org.springframework.web.bind.annotation.RestController;
import org.springframework.web.bind.annotation.RequestMapping;
import java.security.KeyStore;
import java.security.cert.Certificate;
import java.security.PrivateKey;

...
KeyStore ks = KeyStore.getInstance("Windows-MY");
ks.load(null, null); 
Certificate cert = ks.getCertificate("<subject-cn>");
PrivateKey privKey = (PrivateKey) ks.getKey("<subject-cn>", ("<password>").toCharArray());

// Use the certificate and key
...
```

Para idiomas que não dão suporte ou oferecem suporte insuficiente para o repositório de certificados do Windows, consulte [carregar certificado do arquivo](#load-certificate-from-file).

## <a name="load-certificate-in-linux-apps"></a>Carregar o certificado em aplicativos do Linux

As configurações do aplicativo `WEBSITE_LOAD_CERTIFICATES` tornam os certificados especificados acessíveis para seus aplicativos hospedados do Linux (incluindo aplicativos de contêiner personalizados) como arquivos. Os arquivos são encontrados nos seguintes diretórios:

- Certificados privados-`/var/ssl/private` (arquivos `.p12`)
- Certificados públicos-`/var/ssl/certs` (arquivos `.der`)

Os nomes de arquivo de certificado são as impressões digitais do certificado. O código C# a seguir mostra como carregar um certificado público em um aplicativo do Linux.

```csharp
using System;
using System.Security.Cryptography.X509Certificates;

...
var bytes = System.IO.File.ReadAllBytes("/var/ssl/certs/<thumbprint>.der");
var cert = new X509Certificate2(bytes);

// Use the loaded certificate
```

Para ver como carregar um certificado SSL de um arquivo em node. js, PHP, Python, Java ou Ruby, consulte a documentação do respectivo idioma ou plataforma da Web.

## <a name="load-certificate-from-file"></a>Carregar o certificado do arquivo

Se você precisar carregar um arquivo de certificado que é carregado manualmente, é melhor carregar o certificado usando [FTPS](deploy-ftp.md) em vez de [git](deploy-local-git.md), por exemplo. Você deve manter dados confidenciais como um certificado privado fora do controle do código-fonte.

> [!NOTE]
> ASP.NET e ASP.NET Core no Windows devem acessar o repositório de certificados mesmo se você carregar um certificado de um arquivo. Para carregar um arquivo de certificado em um aplicativo do Windows .NET, carregue o perfil do usuário atual com o seguinte comando no <a target="_blank" href="https://shell.azure.com" >Cloud Shell</a>:
>
> ```azurecli-interactive
> az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_LOAD_USER_PROFILE=1
> ```
>
> Essa abordagem para usar certificados em seu código usa a funcionalidade SSL no serviço de aplicativo, o que exige que seu aplicativo esteja na camada **básica** ou acima.

O exemplo C# a seguir carrega um certificado público de um caminho relativo em seu aplicativo:

```csharp
using System;
using System.Security.Cryptography.X509Certificates;

...
var bytes = System.IO.File.ReadAllBytes("~/<relative-path-to-cert-file>");
var cert = new X509Certificate2(bytes);

// Use the loaded certificate
```

Para ver como carregar um certificado SSL de um arquivo em node. js, PHP, Python, Java ou Ruby, consulte a documentação do respectivo idioma ou plataforma da Web.

## <a name="more-resources"></a>Mais recursos

* [Proteger um nome DNS personalizado com uma associação SSL](configure-ssl-bindings.md)
* [Impor HTTPS](configure-ssl-bindings.md#enforce-https)
* [Impor o TLS 1.1/1.2](configure-ssl-bindings.md#enforce-tls-versions)
* [Perguntas frequentes: certificados do serviço de aplicativo](https://docs.microsoft.com/azure/app-service/faq-configuration-and-management/)
