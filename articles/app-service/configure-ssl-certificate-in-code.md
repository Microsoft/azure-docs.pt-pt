---
title: Utilize um certificado TLS/SSL em código
description: Saiba como usar certificados de cliente no seu código. Autenticar com recursos remotos com certificado de cliente ou executar tarefas criptográficas com eles.
ms.topic: article
ms.date: 09/22/2020
ms.reviewer: yutlin
ms.custom: seodec18
ms.openlocfilehash: b4e184f827875ebebd40ab976ef63e77ee702d49
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93126044"
---
# <a name="use-a-tlsssl-certificate-in-your-code-in-azure-app-service"></a>Utilize um certificado TLS/SSL no seu código no Serviço de Aplicações Azure

No seu código de aplicação, pode aceder aos [certificados públicos ou privados que adiciona ao Serviço de Aplicações.](configure-ssl-certificate.md) O seu código de aplicação pode funcionar como cliente e aceder a um serviço externo que requer autenticação de certificados, ou pode precisar de executar tarefas criptográficas. Este guia de como usar certificados públicos ou privados no seu código de aplicação.

Esta abordagem para usar certificados no seu código faz uso da funcionalidade TLS no Serviço de Aplicações, que requer que a sua app esteja no nível **Básico** ou acima. Se a sua aplicação estiver no nível **Gratuito** ou **Partilhado,** pode [incluir o ficheiro de certificado no repositório da sua aplicação.](#load-certificate-from-file)

Quando deixar o Serviço de Aplicações gerir os seus certificados TLS/SSL, pode manter os certificados e o código de aplicação separadamente e salvaguardar os seus dados sensíveis.

## <a name="prerequisites"></a>Pré-requisitos

Para acompanhar este guia:

- [Criar uma aplicação do Serviço de Aplicações](./index.yml)
- [Adicione um certificado à sua aplicação](configure-ssl-certificate.md)

## <a name="find-the-thumbprint"></a>Encontre a impressão digital

No <a href="https://portal.azure.com" target="_blank">portal Azure,</a>a partir do menu esquerdo, selecione **Serviços de Aplicações**  >  **\<app-name>** .

A partir da navegação à esquerda da sua aplicação, selecione **as definições TLS/SSL,** selecione **certificados de chave privada (.pfx)** ou **Certificados de Chave Pública (.cer)** .

Encontre o certificado que pretende utilizar e copie a impressão digital.

![Copiar a impressão digital do certificado](./media/configure-ssl-certificate/create-free-cert-finished.png)

## <a name="make-the-certificate-accessible"></a>Tornar o certificado acessível

Para aceder a um certificado no seu código de aplicação, adicione a sua impressão digital à definição da `WEBSITE_LOAD_CERTIFICATES` aplicação, executando o seguinte comando na <a target="_blank" href="https://shell.azure.com" >Cloud Shell</a>:

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_LOAD_CERTIFICATES=<comma-separated-certificate-thumbprints>
```

Para tornar todos os seus certificados acessíveis, desa um valor para `*` .

## <a name="load-certificate-in-windows-apps"></a>Certificado de carga em aplicativos Windows

A `WEBSITE_LOAD_CERTIFICATES` definição da aplicação torna os certificados especificados acessíveis à sua aplicação hospedada no Windows certificate store, no [Utilizador Atual\My](/windows-hardware/drivers/install/local-machine-and-current-user-certificate-stores).

No código C# aceda ao certificado através da impressão digital do certificado. O seguinte código carrega um certificado com a impressão digital `E661583E8FABEF4C0BEF694CBC41C28FB81CD870` .

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

No código Java, aceda ao certificado a partir da loja "Windows-MY" utilizando o campo Nome Comum do Assunto (ver [certificado de chave pública).](https://en.wikipedia.org/wiki/Public_key_certificate) O seguinte código mostra como carregar um certificado chave privado:

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

Para idiomas que não suportem ou ofereçam suporte insuficiente para a loja de certificados Windows, consulte [o certificado de carga a partir de ficheiros](#load-certificate-from-file).

## <a name="load-certificate-from-file"></a>Certificado de carga a partir de arquivo

Se precisar de carregar um ficheiro de certificado que carrega manualmente, é melhor carregar o certificado usando [FTPS](deploy-ftp.md) em vez de [Git](deploy-local-git.md), por exemplo. Deve manter os dados sensíveis como um certificado privado fora do controlo de origem.

> [!NOTE]
> ASP.NET e ASP.NET Core no Windows devem aceder à loja de certificados mesmo que carregue um certificado a partir de um ficheiro. Para carregar um ficheiro de certificado numa aplicação Windows .NET, carregue o perfil atual do utilizador com o seguinte comando na <a target="_blank" href="https://shell.azure.com" >Cloud Shell</a>:
>
> ```azurecli-interactive
> az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_LOAD_USER_PROFILE=1
> ```
>
> Esta abordagem para usar certificados no seu código faz uso da funcionalidade TLS no Serviço de Aplicações, que requer que a sua app esteja no nível **Básico** ou acima.

O exemplo C# a seguir carrega um certificado público de um caminho relativo na sua aplicação:

```csharp
using System;
using System.IO;
using System.Security.Cryptography.X509Certificates;

...
var bytes = File.ReadAllBytes("~/<relative-path-to-cert-file>");
var cert = new X509Certificate2(bytes);

// Use the loaded certificate
```

Para ver como carregar um certificado TLS/SSL a partir de um ficheiro em Node.js, PHP, Python, Java ou Ruby, consulte a documentação para a respetiva idioma ou plataforma web.

## <a name="load-certificate-in-linuxwindows-containers"></a>Certificado de carga em recipientes Linux/Windows

As `WEBSITE_LOAD_CERTIFICATES` definições da aplicação tornam os certificados especificados acessíveis às suas aplicações de contentores Windows ou Linux (incluindo contentores Linux incorporados) como ficheiros. Os ficheiros são encontrados nos seguintes diretórios:

| Plataforma de contentores | Certificados públicos | Certificados privados |
| - | - | - |
| Contentor do Windows | `C:\appservice\certificates\public` | `C:\appservice\certificates\private` |
| Contentor do Linux | `/var/ssl/certs` | `/var/ssl/private` |

Os nomes dos ficheiros de certificado são as impressões digitais do certificado. 

> [!NOTE]
> O Serviço de Aplicações injeta os caminhos dos certificados nos contentores do Windows como as seguintes variáveis `WEBSITE_PRIVATE_CERTS_PATH` `WEBSITE_INTERMEDIATE_CERTS_PATH` ambientais, e `WEBSITE_PUBLIC_CERTS_PATH` `WEBSITE_ROOT_CERTS_PATH` . É melhor fazer referência ao percurso do certificado com as variáveis ambientais em vez de codificar o caminho do certificado, caso os caminhos do certificado mudem no futuro.
>

Além disso, [os contentores do Windows Server Core](configure-custom-container.md#supported-parent-images) carregam automaticamente os certificados na loja de certificados, em **LocalMachine\My** . Para carregar os certificados, siga o mesmo padrão que o [certificado de carga nas aplicações do Windows](#load-certificate-in-windows-apps). Para recipientes baseados no Windows Nano, utilize os caminhos de ficheiro supricionados para [carregar o certificado diretamente a partir do ficheiro](#load-certificate-from-file).

O código C# que se segue mostra como carregar um certificado público numa aplicação Linux.

```csharp
using System;
using System.IO;
using System.Security.Cryptography.X509Certificates;

...
var bytes = File.ReadAllBytes("/var/ssl/certs/<thumbprint>.der");
var cert = new X509Certificate2(bytes);

// Use the loaded certificate
```

Para ver como carregar um certificado TLS/SSL a partir de um ficheiro em Node.js, PHP, Python, Java ou Ruby, consulte a documentação para a respetiva idioma ou plataforma web.

## <a name="more-resources"></a>Mais recursos

* [Proteja um nome DNS personalizado com uma ligação TLS/SSL no Azure App Service](configure-ssl-bindings.md)
* [Impor HTTPS](configure-ssl-bindings.md#enforce-https)
* [Impor TLS 1.1/1.2](configure-ssl-bindings.md#enforce-tls-versions)
* [FAQ : Certificados de Serviço de Aplicações](./faq-configuration-and-management.md)