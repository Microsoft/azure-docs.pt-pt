---
title: Configurar o serviço de Azure App de autenticação mútua de TLS
description: Saiba como configurar seu aplicativo para usar a autenticação de certificado de cliente no TLS.
services: app-service
documentationcenter: ''
author: cephalin
manager: erikre
editor: jimbe
ms.assetid: cd1d15d3-2d9e-4502-9f11-a306dac4453a
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/01/2019
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: d2823158192ae9fc9182f3f60f82d5bd9c050b09
ms.sourcegitcommit: 80da36d4df7991628fd5a3df4b3aa92d55cc5ade
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/02/2019
ms.locfileid: "71811633"
---
# <a name="configure-tls-mutual-authentication-for-azure-app-service"></a>Configurar a autenticação mútua TLS para o serviço Azure App

Você pode restringir o acesso ao seu aplicativo de serviço de Azure App habilitando diferentes tipos de autenticação para ele. Uma maneira de fazer isso é solicitar um certificado de cliente quando a solicitação do cliente é por TLS/SSL e validar o certificado. Esse mecanismo é chamado de autenticação mútua TLS ou autenticação de certificado de cliente. Este artigo mostra como configurar seu aplicativo para usar a autenticação de certificado do cliente.

> [!NOTE]
> Se você acessar seu site por HTTP e não HTTPS, você não receberá nenhum certificado de cliente. Portanto, se seu aplicativo exigir certificados de cliente, você não deve permitir solicitações para seu aplicativo por HTTP.
>

## <a name="enable-client-certificates"></a>Habilitar certificados de cliente

Para configurar seu aplicativo para exigir certificados de cliente, você precisa definir a configuração `clientCertEnabled` para seu aplicativo como `true`. Para definir a configuração, execute o seguinte comando na [Cloud Shell](https://shell.azure.com).

```azurecli-interactive
az webapp update --set clientCertEnabled=true --name <app_name> --resource-group <group_name>
```

## <a name="exclude-paths-from-requiring-authentication"></a>Excluir caminhos da exigência de autenticação

Quando você habilita a autenticação mútua para seu aplicativo, todos os caminhos na raiz do seu aplicativo exigirão um certificado de cliente para acesso. Para permitir que determinados caminhos permaneçam abertos para acesso anônimo, você pode definir caminhos de exclusão como parte da configuração do aplicativo.

Os caminhos de exclusão podem ser configurados selecionando **configuração** > **configurações gerais** e definindo um caminho de exclusão. Neste exemplo, qualquer coisa no caminho `/public` para seu aplicativo não solicitaria um certificado de cliente.

![Caminhos de exclusão de certificado][exclusion-paths]


## <a name="access-client-certificate"></a>Acessar certificado do cliente

No serviço de aplicativo, o término do SSL da solicitação ocorre no balanceador de carga de front-end. Ao encaminhar a solicitação para o código do aplicativo com [certificados de cliente habilitados](#enable-client-certificates), o serviço de aplicativo injeta um cabeçalho de solicitação `X-ARR-ClientCert` com o certificado do cliente. O serviço de aplicativo não faz nada com esse certificado de cliente além de encaminhá-lo para seu aplicativo. O código do aplicativo é responsável por validar o certificado do cliente.

Para ASP.NET, o certificado do cliente está disponível por meio da propriedade **HttpRequest. ClientCertificate** .

Para outras pilhas de aplicativos (Node. js, PHP, etc.), o certificado do cliente está disponível em seu aplicativo por meio de um valor codificado em base64 no cabeçalho de solicitação `X-ARR-ClientCert`.

## <a name="aspnet-sample"></a>Exemplo de ASP.NET

```csharp
    using System;
    using System.Collections.Specialized;
    using System.Security.Cryptography.X509Certificates;
    using System.Web;

    namespace ClientCertificateUsageSample
    {
        public partial class cert : System.Web.UI.Page
        {
            public string certHeader = "";
            public string errorString = "";
            private X509Certificate2 certificate = null;
            public string certThumbprint = "";
            public string certSubject = "";
            public string certIssuer = "";
            public string certSignatureAlg = "";
            public string certIssueDate = "";
            public string certExpiryDate = "";
            public bool isValidCert = false;

            //
            // Read the certificate from the header into an X509Certificate2 object
            // Display properties of the certificate on the page
            //
            protected void Page_Load(object sender, EventArgs e)
            {
                NameValueCollection headers = base.Request.Headers;
                certHeader = headers["X-ARR-ClientCert"];
                if (!String.IsNullOrEmpty(certHeader))
                {
                    try
                    {
                        byte[] clientCertBytes = Convert.FromBase64String(certHeader);
                        certificate = new X509Certificate2(clientCertBytes);
                        certSubject = certificate.Subject;
                        certIssuer = certificate.Issuer;
                        certThumbprint = certificate.Thumbprint;
                        certSignatureAlg = certificate.SignatureAlgorithm.FriendlyName;
                        certIssueDate = certificate.NotBefore.ToShortDateString() + " " + certificate.NotBefore.ToShortTimeString();
                        certExpiryDate = certificate.NotAfter.ToShortDateString() + " " + certificate.NotAfter.ToShortTimeString();
                    }
                    catch (Exception ex)
                    {
                        errorString = ex.ToString();
                    }
                    finally 
                    {
                        isValidCert = IsValidClientCertificate();
                        if (!isValidCert) Response.StatusCode = 403;
                        else Response.StatusCode = 200;
                    }
                }
                else
                {
                    certHeader = "";
                }
            }

            //
            // This is a SAMPLE verification routine. Depending on your application logic and security requirements, 
            // you should modify this method
            //
            private bool IsValidClientCertificate()
            {
                // In this example we will only accept the certificate as a valid certificate if all the conditions below are met:
                // 1. The certificate is not expired and is active for the current time on server.
                // 2. The subject name of the certificate has the common name nildevecc
                // 3. The issuer name of the certificate has the common name nildevecc and organization name Microsoft Corp
                // 4. The thumbprint of the certificate is 30757A2E831977D8BD9C8496E4C99AB26CB9622B
                //
                // This example does NOT test that this certificate is chained to a Trusted Root Authority (or revoked) on the server 
                // and it allows for self signed certificates
                //

                if (certificate == null || !String.IsNullOrEmpty(errorString)) return false;

                // 1. Check time validity of certificate
                if (DateTime.Compare(DateTime.Now, certificate.NotBefore) < 0 || DateTime.Compare(DateTime.Now, certificate.NotAfter) > 0) return false;

                // 2. Check subject name of certificate
                bool foundSubject = false;
                string[] certSubjectData = certificate.Subject.Split(new char[] { ',' }, StringSplitOptions.RemoveEmptyEntries);
                foreach (string s in certSubjectData)
                {
                    if (String.Compare(s.Trim(), "CN=nildevecc") == 0)
                    {
                        foundSubject = true;
                        break;
                    }
                }
                if (!foundSubject) return false;

                // 3. Check issuer name of certificate
                bool foundIssuerCN = false, foundIssuerO = false;
                string[] certIssuerData = certificate.Issuer.Split(new char[] { ',' }, StringSplitOptions.RemoveEmptyEntries);
                foreach (string s in certIssuerData)
                {
                    if (String.Compare(s.Trim(), "CN=nildevecc") == 0)
                    {
                        foundIssuerCN = true;
                        if (foundIssuerO) break;
                    }

                    if (String.Compare(s.Trim(), "O=Microsoft Corp") == 0)
                    {
                        foundIssuerO = true;
                        if (foundIssuerCN) break;
                    }
                }

                if (!foundIssuerCN || !foundIssuerO) return false;

                // 4. Check thumprint of certificate
                if (String.Compare(certificate.Thumbprint.Trim().ToUpper(), "30757A2E831977D8BD9C8496E4C99AB26CB9622B") != 0) return false;

                return true;
            }
        }
    }
```

## <a name="nodejs-sample"></a>Exemplo de Node. js

O código de exemplo do node. js a seguir obtém o cabeçalho `X-ARR-ClientCert` e usa a [forjação de nó](https://github.com/digitalbazaar/forge) para converter a cadeia de caracteres PEM codificada em base64 em um objeto de certificado e validá-la:

```javascript
import { NextFunction, Request, Response } from 'express';
import { pki, md, asn1 } from 'node-forge';

export class AuthorizationHandler {
    public static authorizeClientCertificate(req: Request, res: Response, next: NextFunction): void {
        try {
            // Get header
            const header = req.get('X-ARR-ClientCert');
            if (!header) throw new Error('UNAUTHORIZED');

            // Convert from PEM to pki.CERT
            const pem = `-----BEGIN CERTIFICATE-----${header}-----END CERTIFICATE-----`;
            const incomingCert: pki.Certificate = pki.certificateFromPem(pem);

            // Validate certificate thumbprint
            const fingerPrint = md.sha1.create().update(asn1.toDer(pki.certificateToAsn1(incomingCert)).getBytes()).digest().toHex();
            if (fingerPrint.toLowerCase() !== 'abcdef1234567890abcdef1234567890abcdef12') throw new Error('UNAUTHORIZED');

            // Validate time validity
            const currentDate = new Date();
            if (currentDate < incomingCert.validity.notBefore || currentDate > incomingCert.validity.notAfter) throw new Error('UNAUTHORIZED');

            // Validate issuer
            if (incomingCert.issuer.hash.toLowerCase() !== 'abcdef1234567890abcdef1234567890abcdef12') throw new Error('UNAUTHORIZED');

            // Validate subject
            if (incomingCert.subject.hash.toLowerCase() !== 'abcdef1234567890abcdef1234567890abcdef12') throw new Error('UNAUTHORIZED');

            next();
        } catch (e) {
            if (e instanceof Error && e.message === 'UNAUTHORIZED') {
                res.status(401).send();
            } else {
                next(e);
            }
        }
    }
}
```

[exclusion-paths]: ./media/app-service-web-configure-tls-mutual-auth/exclusion-paths.png