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
ms.date: 12/01/2017
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: 763aadc50a8760b4265dbfc21e9278f909b68433
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60851124"
---
# <a name="use-an-ssl-certificate-in-your-application-code-in-azure-app-service"></a>Utilizar um certificado SSL no código da aplicação no serviço de aplicações do Azure

Este guia de procedimentos mostra como utilizar um dos certificados SSL que carregou ou importou para a aplicação de serviço de aplicações no código da aplicação. Um exemplo de caso de utilização é a sua aplicação acede a um serviço externo que exija a autenticação de certificado. 

Essa abordagem para utilizar certificados SSL no seu código usa o SSL funcionalidade no serviço de aplicações, que requer a sua aplicação em **básica** escalão ou superior. Uma alternativa é incluir o ficheiro de certificado no seu diretório de aplicação e carregá-lo diretamente (consulte [alternativo: certificado de carga como um ficheiro](#file)). No entanto, essa alternativa não lhe permite ocultar a chave privada no certificado do código da aplicação ou o desenvolvedor. Além disso, se o código da aplicação estiver num repositório de código-fonte aberto, manter um certificado com uma chave privada no repositório não é uma opção.

Quando permite que o serviço de aplicações, gerir os certificados SSL, pode manter os certificados e o código da aplicação em separado e salvaguardar os seus dados confidenciais.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este guia de procedimentos:

- [Criar uma aplicação do Serviço de Aplicações](/azure/app-service/)
- [Mapear um nome DNS personalizado à sua aplicação Web](app-service-web-tutorial-custom-domain.md)
- [Carregar um certificado SSL](app-service-web-tutorial-custom-ssl.md) ou [importar um certificado de serviço de aplicações](web-sites-purchase-ssl-web-site.md) à sua aplicação web


## <a name="load-your-certificates"></a>Carregar os certificados

Para utilizar um certificado que é carregado para ou importado para o serviço de aplicações, primeiro torná-lo acessível para o código da aplicação. Fazê-lo com o `WEBSITE_LOAD_CERTIFICATES` definição de aplicação.

Na <a href="https://portal.azure.com" target="_blank">portal do Azure</a>, abra a página da aplicação web.

No painel de navegação esquerdo, clique em **certificados SSL**.

![Certificado carregado](./media/app-service-web-tutorial-custom-ssl/certificate-uploaded.png)

Todos os carregado e importados certificados SSL para esta aplicação web são mostrados aqui com os thumbprints. Copie o thumbprint do certificado que pretende utilizar.

No painel de navegação esquerdo, clique em **as definições da aplicação**.

Adicionar uma aplicação chamada `WEBSITE_LOAD_CERTIFICATES` e defina seu valor para o thumbprint do certificado. Para disponibilizar vários certificados, utilize os valores de thumbprint separados por vírgulas. Para disponibilizar todos os certificados, defina o valor como `*`. Observe que isso colocará o certificado para o `CurrentUser\My` armazenar.

![Configurar a definição de aplicação](./media/app-service-web-ssl-cert-load/configure-app-setting.png)

Quando terminar, clique em **Guardar**.

O certificado configurado está agora pronto para ser utilizada pelo seu código.

## <a name="use-certificate-in-c-code"></a>Utilizar um certificado em código c#

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
## <a name="alternative-load-certificate-as-a-file"></a>Alternativa: carregar o certificado como um ficheiro

Esta secção mostra como e carregar um ficheiro de certificado que está no diretório da aplicação. 

O exemplo do c# seguinte carrega um certificado chamado `mycert.pfx` partir o `certs` diretório do repositório da sua aplicação.

```csharp
using System;
using System.Security.Cryptography.X509Certificates;

...
// Replace the parameter with "~/<relative-path-to-cert-file>".
string certPath = Server.MapPath("~/certs/mycert.pfx");

X509Certificate2 cert = GetCertificate(certPath, signatureBlob.Thumbprint);
...
```

