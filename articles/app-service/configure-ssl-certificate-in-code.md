---
title: Utilize um certificado TLS/SSL em código
description: Aprenda a usar os certificados de cliente no seu código. Autenticar com recursos remotos com um certificado de cliente, ou executar tarefas criptográficas com eles.
ms.topic: article
ms.date: 11/04/2019
ms.reviewer: yutlin
ms.custom: seodec18
ms.openlocfilehash: d76bac60bae11f0843d81de523030154af62a373
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80811691"
---
# <a name="use-a-tlsssl-certificate-in-your-code-in-azure-app-service"></a>Utilize um certificado TLS/SSL no seu código no Serviço de Aplicações Azure

No seu código de aplicação, pode aceder aos [certificados públicos ou privados que adiciona ao Serviço de Aplicações](configure-ssl-certificate.md). O seu código de aplicação pode funcionar como cliente e aceder a um serviço externo que requer a autenticação do certificado, ou pode ter de executar tarefas criptográficas. Este guia de como fazer mostra como utilizar certificados públicos ou privados no seu código de candidatura.

Esta abordagem à utilização de certificados no seu código utiliza a funcionalidade TLS no Serviço de Aplicações, que requer que a sua aplicação esteja no nível **Básico** ou superior. Se a sua aplicação estiver em nível **Livre** ou **Partilhado,** pode [incluir o ficheiro de certificado no seu repositório](#load-certificate-from-file)de aplicações .

Quando permite que o Serviço de Aplicações gere os seus certificados TLS/SSL, pode manter os certificados e o código de aplicação separadamente e salvaguardar os seus dados sensíveis.

## <a name="prerequisites"></a>Pré-requisitos

Para seguir este guia:

- [Criar uma aplicação do Serviço de Aplicações](/azure/app-service/)
- [Adicione um certificado à sua aplicação](configure-ssl-certificate.md)

## <a name="find-the-thumbprint"></a>Encontre a impressão digital

No <a href="https://portal.azure.com" target="_blank">portal Azure</a>, a partir do menu esquerdo, selecione o**\<nome da aplicação ** **App Services** > >.

A partir da navegação à esquerda da sua aplicação, selecione **as definições TLS/SSL,** em seguida, selecione **Certificados de Chave Privada (.pfx)** ou Certificados de **Chave Pública (.cer)**.

Encontre o certificado que pretende utilizar e copie a impressão digital.

![Copiar a impressão digital do certificado](./media/configure-ssl-certificate/create-free-cert-finished.png)

## <a name="make-the-certificate-accessible"></a>Tornar o certificado acessível

Para aceder a um certificado no seu código `WEBSITE_LOAD_CERTIFICATES` de aplicações, adicione a sua impressão digital à definição da aplicação, executando o seguinte comando na <a target="_blank" href="https://shell.azure.com" >Cloud Shell:</a>

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_LOAD_CERTIFICATES=<comma-separated-certificate-thumbprints>
```

Para tornar todos os seus certificados `*`acessíveis, detete o valor para .

## <a name="load-certificate-in-windows-apps"></a>Certificado de carga em aplicativos Windows

A `WEBSITE_LOAD_CERTIFICATES` definição de aplicações torna os certificados especificados acessíveis à sua aplicação hospedada no Windows na loja de certificados Windows, e a localização depende do nível de [preços:](overview-hosting-plans.md)

- Nível **isolado** - em [Máquina Local\My](/windows-hardware/drivers/install/local-machine-and-current-user-certificate-stores). 
- Todos os outros níveis - em [Utilizador Atual\My](/windows-hardware/drivers/install/local-machine-and-current-user-certificate-stores).

No código C#, acede ao certificado pela impressão digital do certificado. O código seguinte carrega um `E661583E8FABEF4C0BEF694CBC41C28FB81CD870`certificado com a impressão digital .

```csharp
using System;
using System.Linq;
using System.Security.Cryptography.X509Certificates;

string certThumbprint = "E661583E8FABEF4C0BEF694CBC41C28FB81CD870";
bool validOnly = false;

using (X509Store certStore = new X509Store(StoreName.My, StoreLocation.CurrentUser))
{
  certStore.Open(OpenFlags.ReadOnly);

  X509Certificate2Collection certCollection = certStore.Certificates.Find(
                              X509FindType.FindByThumbprint,
                              // Replace below with your certificate's thumbprint
                              certThumbprint,
                              validOnly);
  // Get the first cert with the thumbprint
  X509Certificate2 cert = certCollection.OfType<X509Certificate>().FirstOrDefault();

  if (cert is null)
      throw new Exception($"Certificate with thumbprint {certThumbprint} was not found");

  // Use certificate
  Console.WriteLine(cert.FriendlyName);
  
  // Consider to call Dispose() on the certificate after it's being used, avaliable in .NET 4.6 and later
}
```

No código Java, acede ao certificado da loja "Windows-MY" utilizando o campo Nome Comum sujeito (ver certificado de [chave pública).](https://en.wikipedia.org/wiki/Public_key_certificate) O seguinte código mostra como carregar um certificado de chave privada:

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

Para idiomas que não suportem ou ofereçam suporte insuficiente para a loja de certificados Windows, consulte [o certificado de carga a partir do ficheiro](#load-certificate-from-file).

## <a name="load-certificate-in-linux-apps"></a>Certificado de carga em aplicativos Linux

As `WEBSITE_LOAD_CERTIFICATES` definições da aplicação tornam os certificados especificados acessíveis às suas aplicações hospedadas pelo Linux (incluindo aplicações de contentores personalizados) como ficheiros. Os ficheiros são encontrados nos seguintes diretórios:

- Certificados privados - `/var/ssl/private` (ficheiros) `.p12`
- Certificados públicos - `/var/ssl/certs` (ficheiros) `.der`

Os nomes dos ficheiros do certificado são as impressões digitais do certificado. O código C# seguinte mostra como carregar um certificado público numa aplicação Linux.

```csharp
using System;
using System.IO;
using System.Security.Cryptography.X509Certificates;

...
var bytes = File.ReadAllBytes("/var/ssl/certs/<thumbprint>.der");
var cert = new X509Certificate2(bytes);

// Use the loaded certificate
```

Para ver como carregar um certificado TLS/SSL a partir de um ficheiro no Node.js, PHP, Python, Java ou Ruby, consulte a documentação para o respetivo idioma ou plataforma web.

## <a name="load-certificate-from-file"></a>Certificado de carga a partir de arquivo

Se precisar de carregar um ficheiro de certificado que faça o upload manual, é melhor carregar o certificado usando [FTPS](deploy-ftp.md) em vez de [Git,](deploy-local-git.md)por exemplo. Deve manter dados sensíveis como um certificado privado fora do controlo de fonte.

> [!NOTE]
> ASP.NET e ASP.NET Core no Windows devem aceder à loja de certificados mesmo que carregue um certificado de um ficheiro. Para carregar um ficheiro de certificado numa aplicação Windows .NET, carregue o perfil de utilizador atual com o seguinte comando na <a target="_blank" href="https://shell.azure.com" >Cloud Shell:</a>
>
> ```azurecli-interactive
> az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_LOAD_USER_PROFILE=1
> ```
>
> Esta abordagem à utilização de certificados no seu código utiliza a funcionalidade TLS no Serviço de Aplicações, que requer que a sua aplicação esteja no nível **Básico** ou superior.

O exemplo C# seguinte carrega um certificado público de um caminho relativo na sua aplicação:

```csharp
using System;
using System.IO;
using System.Security.Cryptography.X509Certificates;

...
var bytes = File.ReadAllBytes("~/<relative-path-to-cert-file>");
var cert = new X509Certificate2(bytes);

// Use the loaded certificate
```

Para ver como carregar um certificado TLS/SSL a partir de um ficheiro no Node.js, PHP, Python, Java ou Ruby, consulte a documentação para o respetivo idioma ou plataforma web.

## <a name="more-resources"></a>Mais recursos

* [Proteja um nome DNS personalizado com uma ligação TLS/SSL no Serviço de Aplicações Azure](configure-ssl-bindings.md)
* [Impor HTTPS](configure-ssl-bindings.md#enforce-https)
* [Impor TLS 1.1/1.2](configure-ssl-bindings.md#enforce-tls-versions)
* [FAQ : Certificados de serviço de aplicações](https://docs.microsoft.com/azure/app-service/faq-configuration-and-management/)
