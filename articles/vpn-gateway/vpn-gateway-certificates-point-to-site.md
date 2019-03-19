---
title: 'Gerar e exportar certificados para ligações ponto a Site: PowerShell: Azure | Microsoft Docs'
description: Criar um certificado de raiz autoassinado, exportar a chave pública e gerar certificados de cliente com o PowerShell no Windows 10 ou Windows Server 2016.
services: vpn-gateway
documentationcenter: na
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 12/03/2018
ms.author: cherylmc
ms.openlocfilehash: 74639dee6fb548e1c9067cae6fc22f6e3cc872c3
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58096236"
---
# <a name="generate-and-export-certificates-for-point-to-site-using-powershell"></a>Gerar e exportar certificados para ligações ponto a Site com o PowerShell

As ligações ponto a Site utilizam certificados para autenticar. Este artigo mostra-lhe como criar um certificado de raiz autoassinados e gerar certificados de cliente com o PowerShell no Windows 10 ou Windows Server 2016. Se estiver à procura para obter instruções de certificado diferente, veja [certificados - Linux](vpn-gateway-certificates-point-to-site-linux.md) ou [Certificados - MakeCert](vpn-gateway-certificates-point-to-site-makecert.md).

Tem de efetuar os passos neste artigo num computador com Windows 10 ou Windows Server 2016. Os cmdlets do PowerShell que utilizar para gerar certificados fazem parte do sistema operativo e não funcionam em outras versões do Windows. O computador Windows 10 ou Windows Server 2016 só é necessário para gerar os certificados. Assim que os certificados são gerados, pode carregá-los ou instalá-los em qualquer sistema operativo de cliente suportados. 

Se não tiver acesso a um computador Windows 10 ou Windows Server 2016, pode utilizar [MakeCert](vpn-gateway-certificates-point-to-site-makecert.md) para gerar certificados. Os certificados que gerar utilizando um dos métodos que podem ser instalados em qualquer [suportado](vpn-gateway-howto-point-to-site-resource-manager-portal.md#faq) sistema operativo do cliente.

## <a name="rootcert"></a>1. Criar um certificado de raiz autoassinado

Utilize o cmdlet New-SelfSignedCertificate para criar um certificado de raiz autoassinado. Para informações de parâmetro adicional, consulte [New-SelfSignedCertificate](https://technet.microsoft.com/itpro/powershell/windows/pkiclient/new-selfsignedcertificate).

1. A partir de um computador com Windows 10 ou Windows Server 2016, abra a consola do Windows PowerShell com privilégios elevados. Estes exemplos não funcionam no Azure Cloud Shell "Experimente". Tem de executar estes exemplos localmente.
2. Utilize o seguinte exemplo para criar o certificado de raiz autoassinado. O exemplo seguinte cria um certificado de raiz autoassinado "P2SRootCert", que é instalado automaticamente em 'Certificates-Current User\Personal\Certificates' com o nome. Pode ver o certificado, abrindo *certmgr. msc*, ou *gerir certificados de utilizador*.

   ```powershell
   $cert = New-SelfSignedCertificate -Type Custom -KeySpec Signature `
   -Subject "CN=P2SRootCert" -KeyExportPolicy Exportable `
   -HashAlgorithm sha256 -KeyLength 2048 `
   -CertStoreLocation "Cert:\CurrentUser\My" -KeyUsageProperty Sign -KeyUsage CertSign
   ```

## <a name="clientcert"></a>2. Gerar um certificado de cliente

Cada computador cliente que se ligue uma VNet por Ponto a Site tem de ter um certificado de cliente instalado. Gerar um certificado de cliente a partir do certificado de raiz autoassinado e, em seguida, exportar e instalar o certificado de cliente. Se o certificado de cliente não estiver instalado, a autenticação falha. 

Os seguintes passos guiá-lo por meio de gerar um certificado de cliente a partir de um certificado de raiz autoassinado. Pode gerar vários certificados de cliente a partir do mesmo certificado de raiz. Ao gerar certificados de cliente através dos passos abaixo, o certificado de cliente é instalado automaticamente no computador que utilizou para gerar o certificado. Se pretender instalar um certificado de cliente noutro computador cliente, pode exportar o certificado.

Os exemplos utilizam o cmdlet New-SelfSignedCertificate para gerar um certificado de cliente que expira dentro de um ano. Para informações de parâmetro adicional, tal como definir um valor de expiração diferentes para o certificado de cliente, consulte [New-SelfSignedCertificate](https://technet.microsoft.com/itpro/powershell/windows/pkiclient/new-selfsignedcertificate).

### <a name="example-1"></a>Exemplo 1

Utilize este exemplo, caso não tenha fechado a consola do PowerShell depois de criar o certificado de raiz autoassinado. Neste exemplo continua da secção anterior e utiliza a variável '$cert' declarado. Se fechar a consola do PowerShell depois de criar o certificado de raiz autoassinado ou estiver a criar cliente adicionais certificados numa nova sessão de consola do PowerShell, utilize os passos em [exemplo 2](#ex2).

Modificar e execute o exemplo para gerar um certificado de cliente. Se executar o exemplo seguinte sem modificá-la, o resultado é um certificado de cliente com o nome 'P2SChildCert'.  Se quiser nomear o certificado de subordinados outra coisa, modifique o valor CN. Não altere o TextExtension ao executar este exemplo. O certificado de cliente que irá gerar é instalado automaticamente em 'Certificates - user\personal\certificates atual' ' no seu computador.

```powershell
New-SelfSignedCertificate -Type Custom -DnsName P2SChildCert -KeySpec Signature `
-Subject "CN=P2SChildCert" -KeyExportPolicy Exportable `
-HashAlgorithm sha256 -KeyLength 2048 `
-CertStoreLocation "Cert:\CurrentUser\My" `
-Signer $cert -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.2")
```

### <a name="ex2"></a>Exemplo 2

Se estiver a criar certificados de cliente adicionais ou não estiver a utilizar a mesma sessão do PowerShell que utilizou para criar o seu certificado de raiz autoassinado, utilize os seguintes passos:

1. Identifica o certificado de raiz autoassinado que é instalado no computador. Este cmdlet devolve uma lista de certificados que estão instalados no seu computador.

   ```powershell
   Get-ChildItem -Path “Cert:\CurrentUser\My”
   ```
2. Localize o nome do requerente da lista devolvida e, em seguida, copie o thumbprint que esteja localizado junto ao mesmo para um arquivo de texto. No exemplo a seguir, há dois certificados. O nome CN é o nome do certificado de raiz autoassinado partir do qual pretende gerar um certificado de subordinados. Neste caso, "P2SRootCert".

   ```
   Thumbprint                                Subject
  
   AED812AD883826FF76B4D1D5A77B3C08EFA79F3F  CN=P2SChildCert4
   7181AA8C1B4D34EEDB2F3D3BEC5839F3FE52D655  CN=P2SRootCert
   ```
3. Declare uma variável para o certificado de raiz com o thumbprint do passo anterior. Substitua o THUMBPRINT com o thumbprint do certificado de raiz do qual pretende gerar um certificado de subordinados.

   ```powershell
   $cert = Get-ChildItem -Path "Cert:\CurrentUser\My\THUMBPRINT"
   ```

   Por exemplo, a variável com o thumbprint para P2SRootCert no passo anterior, fica assim:

   ```powershell
   $cert = Get-ChildItem -Path "Cert:\CurrentUser\My\7181AA8C1B4D34EEDB2F3D3BEC5839F3FE52D655"
   ```
4. Modificar e execute o exemplo para gerar um certificado de cliente. Se executar o exemplo seguinte sem modificá-la, o resultado é um certificado de cliente com o nome 'P2SChildCert'. Se quiser nomear o certificado de subordinados outra coisa, modifique o valor CN. Não altere o TextExtension ao executar este exemplo. O certificado de cliente que irá gerar é instalado automaticamente em 'Certificates - user\personal\certificates atual' ' no seu computador.

   ```powershell
   New-SelfSignedCertificate -Type Custom -DnsName P2SChildCert -KeySpec Signature `
   -Subject "CN=P2SChildCert" -KeyExportPolicy Exportable `
   -HashAlgorithm sha256 -KeyLength 2048 `
   -CertStoreLocation "Cert:\CurrentUser\My" `
   -Signer $cert -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.2")
   ```

## <a name="cer"></a>3. Exportar a chave pública do certificado de raiz (. cer)

[!INCLUDE [Export public key](../../includes/vpn-gateway-certificates-export-public-key-include.md)]


### <a name="export-the-self-signed-root-certificate-and-private-key-to-store-it-optional"></a>Exportar o certificado de raiz autoassinado e armazená-los (opcional) a chave privada

Talvez queira exportar o certificado de raiz autoassinado e armazená-lo com segurança como cópia de segurança. Se precisa de ser, pode instalá-lo noutro computador e gerar mais certificados de cliente mais tarde. Para exportar o certificado de raiz autoassinado como um ficheiro. pfx, selecione o certificado de raiz e utilizar os mesmos passos conforme descrito em [exportar um certificado de cliente](#clientexport).

## <a name="clientexport"></a>4. Exportar o certificado de cliente

[!INCLUDE [Export client certificate](../../includes/vpn-gateway-certificates-export-client-cert-include.md)]


## <a name="install"></a>5. Instalar um certificado de cliente exportado

Cada cliente que estabelece ligação à VNet através de uma ligação de P2S requer um certificado de cliente seja instalado localmente.

Para instalar um certificado de cliente, consulte [instale um certificado de cliente para ligações ponto a Site](point-to-site-how-to-vpn-client-install-azure-cert.md).

## <a name="install"></a>6. Continue com os passos de configuração P2S

Continue com a sua configuração de ponto a Site.

* Para **Resource Manager** passos de modelo de implementação, consulte [configurar P2S através da autenticação de certificados nativa do Azure](vpn-gateway-howto-point-to-site-resource-manager-portal.md). 
* Para **clássica** passos de modelo de implementação, consulte [configurar uma ligação de VPN ponto a Site a uma VNet (clássica)](vpn-gateway-howto-point-to-site-classic-azure-portal.md).
* Para P2S resolução de problemas de informações, consulte [ligações de ponto a site de resolução de problemas do Azure](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md).
