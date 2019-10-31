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
ms.openlocfilehash: 89fa06dda418f328b3bc07aada49aa347e35220a
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73182266"
---
## <a name="rootcert"></a>Criar um certificado raiz autoassinado

Use o cmdlet New-SelfSignedCertificate para criar um certificado raiz autoassinado. Para obter informações adicionais sobre parâmetros, consulte [New-SelfSignedCertificate](https://technet.microsoft.com/itpro/powershell/windows/pkiclient/new-selfsignedcertificate).

1. Em um computador que executa o Windows 10 ou o Windows Server 2016, abra um console do Windows PowerShell com privilégios elevados. Esses exemplos não funcionam no Azure Cloud Shell "Experimente". Você deve executar esses exemplos localmente.
2. Use o exemplo a seguir para criar o certificado raiz autoassinado. O exemplo a seguir cria um certificado raiz autoassinado chamado ' P2SRootCert ' que é instalado automaticamente em ' Certificates-Current User\Personal\Certificates '. Você pode exibir o certificado abrindo *certmgr. msc*ou *gerenciar certificados de usuário*.

   ```powershell
   $cert = New-SelfSignedCertificate -Type Custom -KeySpec Signature `
   -Subject "CN=P2SRootCert" -KeyExportPolicy Exportable `
   -HashAlgorithm sha256 -KeyLength 2048 `
   -CertStoreLocation "Cert:\CurrentUser\My" -KeyUsageProperty Sign -KeyUsage CertSign
   ```

## <a name="clientcert"></a>Gerar um certificado de cliente

Cada computador cliente que se ligue uma VNet por Ponto a Site tem de ter um certificado de cliente instalado. Você gera um certificado de cliente do certificado raiz autoassinado e, em seguida, exporta e instala o certificado do cliente. Se o certificado do cliente não estiver instalado, a autenticação falhará. 

As etapas a seguir orientarão você na geração de um certificado de cliente de um certificado raiz autoassinado. Você pode gerar vários certificados de cliente do mesmo certificado raiz. Quando você gera certificados de cliente usando as etapas abaixo, o certificado do cliente é instalado automaticamente no computador que você usou para gerar o certificado. Se você quiser instalar um certificado de cliente em outro computador cliente, poderá exportar o certificado.

Os exemplos usam o cmdlet New-SelfSignedCertificate para gerar um certificado de cliente que expira em um ano. Para obter informações adicionais sobre parâmetros, como definir um valor de expiração diferente para o certificado do cliente, consulte [New-SelfSignedCertificate](https://technet.microsoft.com/itpro/powershell/windows/pkiclient/new-selfsignedcertificate).

### <a name="example-1"></a>Exemplo 1

Use este exemplo se você não tiver fechado o console do PowerShell depois de criar o certificado raiz autoassinado. Este exemplo continua da seção anterior e usa a variável ' $cert ' declarada. Se você fechou o console do PowerShell depois de criar o certificado raiz autoassinado ou estiver criando certificados de cliente adicionais em uma nova sessão de console do PowerShell, use as etapas no [exemplo 2](#ex2).

Modifique e execute o exemplo para gerar um certificado de cliente. Se você executar o exemplo a seguir sem modificá-lo, o resultado será um certificado de cliente chamado ' P2SChildCert '.  Se você quiser nomear o certificado filho outra coisa, modifique o valor CN. Não altere a textextension ao executar este exemplo. O certificado de cliente que você gera é instalado automaticamente em ' Certificates-Current User\Personal\Certificates ' no seu computador.

```powershell
New-SelfSignedCertificate -Type Custom -DnsName P2SChildCert -KeySpec Signature `
-Subject "CN=P2SChildCert" -KeyExportPolicy Exportable `
-HashAlgorithm sha256 -KeyLength 2048 `
-CertStoreLocation "Cert:\CurrentUser\My" `
-Signer $cert -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.2")
```

### <a name="ex2"></a>Exemplo 2

Se você estiver criando certificados de cliente adicionais ou não estiver usando a mesma sessão do PowerShell usada para criar seu certificado raiz autoassinado, use as seguintes etapas:

1. Identifique o certificado raiz autoassinado que está instalado no computador. Esse cmdlet retorna uma lista de certificados que estão instalados no seu computador.

   ```powershell
   Get-ChildItem -Path "Cert:\CurrentUser\My"
   ```
2. Localize o nome da entidade na lista retornada e copie a impressão digital que está localizada ao lado dele para um arquivo de texto. No exemplo a seguir, há dois certificados. O nome CN é o nome do certificado raiz autoassinado do qual você deseja gerar um certificado filho. Nesse caso, ' P2SRootCert '.

   ```
   Thumbprint                                Subject
  
   AED812AD883826FF76B4D1D5A77B3C08EFA79F3F  CN=P2SChildCert4
   7181AA8C1B4D34EEDB2F3D3BEC5839F3FE52D655  CN=P2SRootCert
   ```
3. Declare uma variável para o certificado raiz usando a impressão digital da etapa anterior. Substitua a impressão digital pela impressão digital do certificado raiz do qual você deseja gerar um certificado filho.

   ```powershell
   $cert = Get-ChildItem -Path "Cert:\CurrentUser\My\THUMBPRINT"
   ```

   Por exemplo, usando a impressão digital para P2SRootCert na etapa anterior, a variável é parecida com esta:

   ```powershell
   $cert = Get-ChildItem -Path "Cert:\CurrentUser\My\7181AA8C1B4D34EEDB2F3D3BEC5839F3FE52D655"
   ```
4. Modifique e execute o exemplo para gerar um certificado de cliente. Se você executar o exemplo a seguir sem modificá-lo, o resultado será um certificado de cliente chamado ' P2SChildCert '. Se você quiser nomear o certificado filho outra coisa, modifique o valor CN. Não altere a textextension ao executar este exemplo. O certificado de cliente que você gera é instalado automaticamente em ' Certificates-Current User\Personal\Certificates ' no seu computador.

   ```powershell
   New-SelfSignedCertificate -Type Custom -DnsName P2SChildCert -KeySpec Signature `
   -Subject "CN=P2SChildCert" -KeyExportPolicy Exportable `
   -HashAlgorithm sha256 -KeyLength 2048 `
   -CertStoreLocation "Cert:\CurrentUser\My" `
   -Signer $cert -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.2")
   ```

## <a name="cer"></a>Exportar a chave pública do certificado raiz (. cer)

[!INCLUDE [Export public key](vpn-gateway-certificates-export-public-key-include.md)]

### <a name="export-the-self-signed-root-certificate-and-private-key-to-store-it-optional"></a>Exportar o certificado raiz autoassinado e a chave privada para armazená-lo (opcional)

Talvez você queira exportar o certificado raiz autoassinado e armazená-lo com segurança como backup. Se necessário, você pode instalá-lo posteriormente em outro computador e gerar mais certificados de cliente. Para exportar o certificado raiz autoassinado como um. pfx, selecione o certificado raiz e use as mesmas etapas, conforme descrito em [exportar um certificado de cliente](#clientexport).

## <a name="clientexport"></a>Exportar o certificado do cliente

[!INCLUDE [Export client certificate](vpn-gateway-certificates-export-client-cert-include.md)]
