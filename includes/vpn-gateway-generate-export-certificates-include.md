---
title: incluir ficheiro
description: incluir ficheiro
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/29/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 946ff043828034340ae3273fc0629e32de755540
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/24/2020
ms.locfileid: "95563728"
---
## <a name="create-a-self-signed-root-certificate"></a><a name="rootcert"></a>Criar um certificado de raiz auto-assinado

Utilize o cmdlet New-SelfSignedCertificate para criar um certificado de raiz auto-assinado. Para obter informações adicionais [sobre parâmetros, consulte o Certificado New-Self-SelfSigned](/powershell/module/pkiclient/new-selfsignedcertificate).

1. A partir de um computador que executa o Windows 10 ou o Windows Server 2016, abra uma consola Windows PowerShell com privilégios elevados. Estes exemplos não funcionam na Azure Cloud Shell "Try It". Deve executar estes exemplos localmente.
1. Utilize o seguinte exemplo para criar o certificado de raiz auto-assinado. O exemplo a seguir cria um certificado de raiz auto-assinado denominado 'P2SRootCert' que é automaticamente instalado em 'Certificados-Utilizador Corrente\Personal\Certificates'. Pode ver o certificado abrindo *certmgr.msc,* ou *Gerir os Certificados de Utilizador.*

   Inscreva-se com o `Connect-AzAccount` cmdlet. Em seguida, corra o seguinte exemplo com as modificações necessárias.

   ```powershell
   $cert = New-SelfSignedCertificate -Type Custom -KeySpec Signature `
   -Subject "CN=P2SRootCert" -KeyExportPolicy Exportable `
   -HashAlgorithm sha256 -KeyLength 2048 `
   -CertStoreLocation "Cert:\CurrentUser\My" -KeyUsageProperty Sign -KeyUsage CertSign
   ```

1. Deixe a consola PowerShell aberta e proceda aos próximos passos para gerar certificados de cliente.

## <a name="generate-a-client-certificate"></a><a name="clientcert"></a>Gerar um certificado de cliente

Cada computador cliente que se ligue uma VNet por Ponto a Site tem de ter um certificado de cliente instalado. Gera um certificado de cliente a partir do certificado de raiz auto-assinado e, em seguida, exporta e instala o certificado de cliente. Se o certificado do cliente não for instalado, a autenticação falha. 

Os passos seguintes acompanham-no através da geração de um certificado de cliente a partir de um certificado de raiz auto-assinado. Pode gerar vários certificados de cliente a partir do mesmo certificado de raiz. Quando gera certificados de cliente utilizando os passos abaixo, o certificado de cliente é automaticamente instalado no computador que usou para gerar o certificado. Se quiser instalar um certificado de cliente noutro computador cliente, pode exportar o certificado.

Os exemplos usam o New-SelfSignedCertificate cmdlet para gerar um certificado de cliente que expira num ano. Para obter informações adicionais sobre parâmetros, tais como a definição de um valor de validade diferente para o certificado do cliente, consulte [New-SelfSignedCertificate](/powershell/module/pkiclient/new-selfsignedcertificate).

### <a name="example-1---powershell-console-session-still-open"></a>Exemplo 1 - Sessão de consola PowerShell ainda aberta

Utilize este exemplo se não tiver fechado a consola PowerShell depois de ter criado o certificado de raiz auto-assinado. Este exemplo continua a partir da secção anterior e utiliza a variável declarada "$cert". Se fechou a consola PowerShell depois de criar o certificado de raiz auto-assinado ou estiver a criar certificados adicionais para clientes numa nova sessão de consola PowerShell, utilize os passos em [Exemplo 2](#ex2).

Modifique e corra o exemplo para gerar um certificado de cliente. Se executar o exemplo seguinte sem modificá-lo, o resultado é um certificado de cliente denominado 'P2SChildCert'.  Se quiser nomear o certificado da criança outra coisa, modifique o valor CN. Não altere a extensão de texto ao executar este exemplo. O certificado de cliente que gera está automaticamente instalado em 'Certificados - Utilizador Atual\Personal\Certificates' no seu computador.

```powershell
New-SelfSignedCertificate -Type Custom -DnsName P2SChildCert -KeySpec Signature `
-Subject "CN=P2SChildCert" -KeyExportPolicy Exportable `
-HashAlgorithm sha256 -KeyLength 2048 `
-CertStoreLocation "Cert:\CurrentUser\My" `
-Signer $cert -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.2")
```

### <a name="example-2---new-powershell-console-session"></a><a name="ex2"></a>Exemplo 2 - Nova sessão de consola PowerShell

Se estiver a criar certificados de cliente adicionais, ou não estiver a utilizar a mesma sessão PowerShell que usou para criar o seu certificado de raiz auto-assinado, utilize os seguintes passos:

1. Identifique o certificado de raiz auto-assinado que está instalado no computador. Este cmdlet devolve uma lista de certificados instalados no seu computador.

   ```powershell
   Get-ChildItem -Path "Cert:\CurrentUser\My"
   ```

1. Localize o nome do sujeito na lista de devoluções e, em seguida, copie a impressão digital que está localizada ao lado dela num ficheiro de texto. No exemplo seguinte, existem dois certificados. O nome CN é o nome do certificado de raiz auto-assinado a partir do qual pretende gerar um certificado para criança. Neste caso, 'P2SRootCert'.

   ```
   Thumbprint                                Subject
  
   AED812AD883826FF76B4D1D5A77B3C08EFA79F3F  CN=P2SChildCert4
   7181AA8C1B4D34EEDB2F3D3BEC5839F3FE52D655  CN=P2SRootCert
   ```

1. Declare uma variável para o certificado de raiz utilizando a impressão digital do passo anterior. Substitua a impressão digital por impressão digital do certificado de raiz a partir do qual pretende gerar um certificado para criança.

   ```powershell
   $cert = Get-ChildItem -Path "Cert:\CurrentUser\My\THUMBPRINT"
   ```

   Por exemplo, usando a impressão digital para P2SRootCert no passo anterior, a variável é assim:

   ```powershell
   $cert = Get-ChildItem -Path "Cert:\CurrentUser\My\7181AA8C1B4D34EEDB2F3D3BEC5839F3FE52D655"
   ```

1. Modifique e corra o exemplo para gerar um certificado de cliente. Se executar o exemplo seguinte sem modificá-lo, o resultado é um certificado de cliente denominado 'P2SChildCert'. Se quiser nomear o certificado da criança outra coisa, modifique o valor CN. Não altere a extensão de texto ao executar este exemplo. O certificado de cliente que gera está automaticamente instalado em 'Certificados - Utilizador Atual\Personal\Certificates' no seu computador.

   ```powershell
   New-SelfSignedCertificate -Type Custom -DnsName P2SChildCert -KeySpec Signature `
   -Subject "CN=P2SChildCert" -KeyExportPolicy Exportable `
   -HashAlgorithm sha256 -KeyLength 2048 `
   -CertStoreLocation "Cert:\CurrentUser\My" `
   -Signer $cert -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.2")
   ```

## <a name="export-the-root-certificate-public-key-cer"></a><a name="cer"></a>Exportar a chave pública do certificado de raiz (.cer)

[!INCLUDE [Export public key](vpn-gateway-certificates-export-public-key-include.md)]

### <a name="export-the-self-signed-root-certificate-and-private-key-to-store-it-optional"></a>Exportar o certificado de raiz auto-assinado e a chave privada para armazená-lo (opcional)

Pode querer exportar o certificado de raiz auto-assinado e armazená-lo com segurança como cópia de segurança. Se necessário, poderá instalá-lo mais tarde noutro computador e gerar mais certificados de cliente. Para exportar o certificado de raiz auto-assinado como .pfx, selecione o certificado raiz e utilize os mesmos passos descritos no [Certificado de Cliente Exportação](#clientexport).

## <a name="export-the-client-certificate"></a><a name="clientexport"></a>Exportar o certificado de cliente

[!INCLUDE [Export client certificate](vpn-gateway-certificates-export-client-cert-include.md)]