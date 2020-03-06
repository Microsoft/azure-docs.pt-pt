---
title: incluir ficheiro
description: incluir ficheiro
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/10/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 1e18223736964b0327a4c8f6ddb73ddb4f58889a
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78305018"
---
## <a name="rootcert"></a>Criar um certificado de raiz auto-assinado

Utilize o cmdlet New Self-SignedCertificate para criar um certificado de raiz auto-assinado. Para obter informações adicionais sobre parâmetros, consulte [New-SelfSignedCertificate](https://technet.microsoft.com/itpro/powershell/windows/pkiclient/new-selfsignedcertificate).

1. A partir de um computador que executa o Windows 10 ou windows Server 2016, abra uma consola Windows PowerShell com privilégios elevados. Estes exemplos não funcionam na Casca de Nuvem Azure "Experimente". Deve executar estes exemplos localmente.
2. Utilize o seguinte exemplo para criar o certificado raiz auto-assinado. O exemplo seguinte cria um certificado de raiz auto-assinado chamado 'P2SRootCert' que é automaticamente instalado em 'Certificados-Utilizador Corrente\Certificados pessoais\'. Pode ver o certificado abrindo *certmgr.msc,* ou *gerir certificados*de utilizador.

   ```powershell
   $cert = New-SelfSignedCertificate -Type Custom -KeySpec Signature `
   -Subject "CN=P2SRootCert" -KeyExportPolicy Exportable `
   -HashAlgorithm sha256 -KeyLength 2048 `
   -CertStoreLocation "Cert:\CurrentUser\My" -KeyUsageProperty Sign -KeyUsage CertSign
   ```
 3. Deixe a consola PowerShell aberta se quiser criar um certificado de cliente logo após a criação deste certificado de raiz.

## <a name="clientcert"></a>Gerar um certificado de cliente

Cada computador cliente que se ligue uma VNet por Ponto a Site tem de ter um certificado de cliente instalado. Gera um certificado de cliente a partir do certificado raiz auto-assinado e depois exporta e instala o certificado de cliente. Se o certificado de cliente não estiver instalado, a autenticação falha. 

Os seguintes passos passam por gerar um certificado de cliente a partir de um certificado de raiz auto-assinado. Pode gerar vários certificados de cliente a partir do mesmo certificado de raiz. Quando gera certificados de cliente utilizando os passos abaixo, o certificado de cliente é automaticamente instalado no computador que usou para gerar o certificado. Se quiser instalar um certificado de cliente noutro computador cliente, pode exportar o certificado.

Os exemplos usam o cmdlet New SelfSignedCertificate para gerar um certificado de cliente que expira dentro de um ano. Para obter informações adicionais sobre parâmetros, tais como a definição de um valor de validade diferente para o certificado de cliente, consulte [New-SelfSignedCertificate](https://technet.microsoft.com/itpro/powershell/windows/pkiclient/new-selfsignedcertificate).

### <a name="example-1---powershell-console-session-still-open"></a>Exemplo 1 - Sessão de consola PowerShell ainda em aberto

Use este exemplo se não tiver fechado a consola PowerShell depois de criar o certificado raiz auto-assinado. Este exemplo continua a partir da secção anterior e utiliza a variável declarada "$cert". Se fechou a consola PowerShell depois de criar o certificado raiz auto-assinado, ou estiver a criar certificados adicionais de cliente numa nova sessão de consola powerShell, utilize os passos no [Exemplo 2](#ex2).

Modifique e execute o exemplo para gerar um certificado de cliente. Se executar o seguinte exemplo sem modificá-lo, o resultado é um certificado de cliente chamado 'P2SChildCert'.  Se quiser nomear o certificado de criança outra coisa, modifique o valor NC. Não altere a Extensão de Texto ao executar este exemplo. O certificado de cliente que gera é automaticamente instalado em 'Certificados - Utilizador Atual\Certificados Pessoais\' no seu computador.

```powershell
New-SelfSignedCertificate -Type Custom -DnsName P2SChildCert -KeySpec Signature `
-Subject "CN=P2SChildCert" -KeyExportPolicy Exportable `
-HashAlgorithm sha256 -KeyLength 2048 `
-CertStoreLocation "Cert:\CurrentUser\My" `
-Signer $cert -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.2")
```

### <a name="ex2"></a>Exemplo 2 - Nova sessão de consola PowerShell

Se estiver a criar certificados adicionais de cliente, ou se não estiver a utilizar a mesma sessão PowerShell que usou para criar o seu certificado raiz auto-assinado, utilize os seguintes passos:

1. Identifique o certificado de raiz auto-assinado que está instalado no computador. Este cmdlet devolve uma lista de certificados que estão instalados no seu computador.

   ```powershell
   Get-ChildItem -Path "Cert:\CurrentUser\My"
   ```
2. Localize o nome do assunto na lista retornada e, em seguida, copie a impressão digital que está localizada ao lado dele para um ficheiro de texto. No exemplo seguinte, existem dois certificados. O nome NC é o nome do certificado raiz auto-assinado a partir do qual pretende gerar um certificado de criança. Neste caso, "P2SRootCert".

   ```
   Thumbprint                                Subject
  
   AED812AD883826FF76B4D1D5A77B3C08EFA79F3F  CN=P2SChildCert4
   7181AA8C1B4D34EEDB2F3D3BEC5839F3FE52D655  CN=P2SRootCert
   ```
3. Declare uma variável para o certificado de raiz utilizando a impressão digital do passo anterior. Substitua a impressão digital por impressão digital do certificado de raiz a partir do qual pretende gerar um certificado de criança.

   ```powershell
   $cert = Get-ChildItem -Path "Cert:\CurrentUser\My\THUMBPRINT"
   ```

   Por exemplo, usando a impressão digital para P2SRootCert no passo anterior, a variável é assim:

   ```powershell
   $cert = Get-ChildItem -Path "Cert:\CurrentUser\My\7181AA8C1B4D34EEDB2F3D3BEC5839F3FE52D655"
   ```
4. Modifique e execute o exemplo para gerar um certificado de cliente. Se executar o seguinte exemplo sem modificá-lo, o resultado é um certificado de cliente chamado 'P2SChildCert'. Se quiser nomear o certificado de criança outra coisa, modifique o valor NC. Não altere a Extensão de Texto ao executar este exemplo. O certificado de cliente que gera é automaticamente instalado em 'Certificados - Utilizador Atual\Certificados Pessoais\' no seu computador.

   ```powershell
   New-SelfSignedCertificate -Type Custom -DnsName P2SChildCert -KeySpec Signature `
   -Subject "CN=P2SChildCert" -KeyExportPolicy Exportable `
   -HashAlgorithm sha256 -KeyLength 2048 `
   -CertStoreLocation "Cert:\CurrentUser\My" `
   -Signer $cert -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.2")
   ```

## <a name="cer"></a>Exportar a chave pública do certificado de raiz (.cer)

[!INCLUDE [Export public key](vpn-gateway-certificates-export-public-key-include.md)]

### <a name="export-the-self-signed-root-certificate-and-private-key-to-store-it-optional"></a>Exportar o certificado de raiz auto-assinado e a chave privada para armazená-lo (opcional)

Pode querer exportar o certificado de raiz auto-assinado e armazená-lo com segurança como cópia de segurança. Se necessário, pode posteriormente instalá-lo noutro computador e gerar mais certificados de cliente. Para exportar o certificado de raiz auto-assinado como .pfx, selecione o certificado de raiz e utilize os mesmos passos descritos na [Exportação de um certificado](#clientexport)de cliente .

## <a name="clientexport"></a>Exportar o certificado de cliente

[!INCLUDE [Export client certificate](vpn-gateway-certificates-export-client-cert-include.md)]
