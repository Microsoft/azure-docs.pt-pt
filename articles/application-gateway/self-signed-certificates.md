---
title: Gere um certificado auto-assinado com uma ca de raiz personalizada
titleSuffix: Azure Application Gateway
description: Saiba como gerar um certificado auto-assinado do Azure Application Gateway com uma raiz personalizada CA
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: how-to
ms.date: 07/23/2019
ms.author: victorh
ms.openlocfilehash: e60aa9f072a447af97aa7cc66534e6e893fdbcf6
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "93396945"
---
# <a name="generate-an-azure-application-gateway-self-signed-certificate-with-a-custom-root-ca"></a>Gerar um certificado autoassinado do Gateway de Aplicação do Azure com uma AC raiz personalizada

O Gateway de Aplicação v2 SKU introduz a utilização de Certificados de Raiz Fidedigna para permitir servidores de backend. Isto remove os certificados de autenticação que eram necessários no V1 SKU. O *certificado de raiz* é um X.509 codificado base-64(. CER) certificado de raiz de formato do servidor de certificado de backend. Identifica a autoridade de certificados de raiz (CA) que emitiu o certificado do servidor e o certificado do servidor é então utilizado para a comunicação TLS/SSL.

A Application Gateway confia o certificado do seu website por padrão se for assinado por um ca conhecido (por exemplo, GoDaddy ou DigiCert). Não precisa de carregar explicitamente o certificado de raiz nesse caso. Para obter mais informações, consulte [a visão geral da rescisão do TLS e o fim do TLS com o Gateway de aplicação.](ssl-overview.md) No entanto, se tiver um ambiente dev/teste e não quiser adquirir um certificado assinado pela AC, pode criar o seu próprio CA personalizado e criar um certificado auto-assinado com ele. 

> [!NOTE]
> Os certificados auto-assinados não são de confiança por defeito e podem ser difíceis de manter. Além disso, podem usar suítes de haxixe e cifra desatualizadas que podem não ser fortes. Para melhor segurança, compre um certificado assinado por uma conhecida autoridade de certificados.

Neste artigo, aprenderá a:

- Crie a sua própria Autoridade de Certificados personalizada
- Crie um certificado auto-assinado assinado pela sua AC personalizada
- Faça o upload de um certificado de raiz auto-assinado para um Gateway de aplicação para autenticar o servidor backend

## <a name="prerequisites"></a>Pré-requisitos

- **[OpenSSL](https://www.openssl.org/) em um computador que executa Windows ou Linux** 

   Embora possam existir outras ferramentas disponíveis para a gestão de certificados, este tutorial utiliza o OpenSSL. Pode encontrar o OpenSSL agregado com muitas distribuições Linux, como o Ubuntu.
- **Um servidor web**

   Por exemplo, Apache, IIS ou NGINX para testar os certificados.

- **Um Gateway de Aplicação v2 SKU**
   
  Se não tiver uma porta de aplicação existente, consulte [Quickstart: Tráfego web direto com Azure Application Gateway - Portal Azure](quick-create-portal.md).

## <a name="create-a-root-ca-certificate"></a>Criar um certificado de CA raiz

Crie o seu certificado de CA raiz utilizando o OpenSSL.

### <a name="create-the-root-key"></a>Criar a chave raiz

1. Inscreva-se no seu computador onde o OpenSSL está instalado e execute o seguinte comando. Isto cria uma chave protegida por palavra-passe.

   ```
   openssl ecparam -out contoso.key -name prime256v1 -genkey
   ```
1. Ao solicitar, escreva uma senha forte. Por exemplo, pelo menos nove caracteres, utilizando maiússão, minúscula, números e símbolos.

### <a name="create-a-root-certificate-and-self-sign-it"></a>Crie um Certificado de Raiz e auto-assine-o

1. Utilize os seguintes comandos para gerar o csr e o certificado.

   ```
   openssl req -new -sha256 -key contoso.key -out contoso.csr

   openssl x509 -req -sha256 -days 365 -in contoso.csr -signkey contoso.key -out contoso.crt
   ```
   Os comandos anteriores criam o certificado raiz. Usará isto para assinar o certificado do servidor.

1. Quando solicitado, digite a palavra-passe para a chave raiz, e as informações organizacionais para a CA personalizada, tais como País/Região, Estado, Org, OU, e o nome de domínio totalmente qualificado (este é o domínio do emitente).

   ![criar certificado de raiz](media/self-signed-certificates/root-cert.png)

## <a name="create-a-server-certificate"></a>Criar um certificado de servidor

Em seguida, irá criar um certificado de servidor utilizando o OpenSSL.

### <a name="create-the-certificates-key"></a>Crie a chave do certificado

Utilize o seguinte comando para gerar a chave para o certificado do servidor.

   ```
   openssl ecparam -out fabrikam.key -name prime256v1 -genkey
   ```

### <a name="create-the-csr-certificate-signing-request"></a>Criar o CSR (Pedido de Assinatura de Certificado)

A RSE é uma chave pública que é dada a uma AC quando solicita um certificado. A AC emite o certificado para este pedido específico.

> [!NOTE]
> O CN (Nome Comum) para o certificado do servidor deve ser diferente do domínio do emitente. Por exemplo, neste caso, o CN para o emitente é `www.contoso.com` e o certificado de servidor CN é `www.fabrikam.com` .


1. Utilize o seguinte comando para gerar a RSE:

   ```
   openssl req -new -sha256 -key fabrikam.key -out fabrikam.csr
   ```

1. Quando solicitado, digite a palavra-passe para a chave raiz, e as informações organizacionais para o CA personalizado: País/Região, Estado, Org, OU, e o nome de domínio totalmente qualificado. Este é o domínio do site e deve ser diferente do emitente.

   ![Certificado de servidor](media/self-signed-certificates/server-cert.png)

### <a name="generate-the-certificate-with-the-csr-and-the-key-and-sign-it-with-the-cas-root-key"></a>Gere o certificado com a RSE e a chave e assine-o com a chave raiz da AC

1. Utilize o seguinte comando para criar o certificado:

   ```
   openssl x509 -req -in fabrikam.csr -CA  contoso.crt -CAkey contoso.key -CAcreateserial -out fabrikam.crt -days 365 -sha256
   ```
### <a name="verify-the-newly-created-certificate"></a>Verifique o certificado recém-criado

1. Utilize o seguinte comando para imprimir a saída do ficheiro CRT e verificar o seu conteúdo:

   ```
   openssl x509 -in fabrikam.crt -text -noout
   ```

   ![Verificação do certificado](media/self-signed-certificates/verify-cert.png)

1. Verifique os ficheiros do seu diretório e certifique-se de que tem os seguintes ficheiros:

   - contoso.crt
   - contoso.key
   - fabrikam.crt
   - fabrikam.key

## <a name="configure-the-certificate-in-your-web-servers-tls-settings"></a>Configurar o certificado nas definições de TLS do seu servidor web

No seu servidor web, configurar OTS utilizando os ficheiros fabrikam.crt e fabrikam.key. Se o seu servidor web não puder levar dois ficheiros, pode combiná-los com um único ficheiro .pem ou .pfx utilizando comandos OpenSSL.

### <a name="iis"></a>IIS

Para obter instruções sobre como importar certificado e carregá-los como certificado de servidor no IIS, consulte [COMO: Instalar certificados importados num Servidor Web no Windows Server 2003](https://support.microsoft.com/help/816794/how-to-install-imported-certificates-on-a-web-server-in-windows-server).

Para instruções de ligação TLS, consulte [como configurar o SSL no IIS 7](/iis/manage/configuring-security/how-to-set-up-ssl-on-iis#create-an-ssl-binding-1).

### <a name="apache"></a>Apache

A seguinte configuração é um anfitrião virtual de exemplo [configurado para SSL](https://cwiki.apache.org/confluence/display/HTTPD/NameBasedSSLVHosts) em Apache:

```
<VirtualHost www.fabrikam:443>
      DocumentRoot /var/www/fabrikam
      ServerName www.fabrikam.com
      SSLEngine on
      SSLCertificateFile /home/user/fabrikam.crt
      SSLCertificateKeyFile /home/user/fabrikam.key
</VirtualHost>
```

### <a name="nginx"></a>NGINX

A seguinte configuração é um [bloco de servidor NGINX](https://nginx.org/docs/http/configuring_https_servers.html) exemplo com configuração TLS:

![NGINX com TLS](media/self-signed-certificates/nginx-ssl.png)

## <a name="access-the-server-to-verify-the-configuration"></a>Aceda ao servidor para verificar a configuração

1. Adicione o certificado de raiz à loja de raiz fidedigna da sua máquina. Ao aceder ao site, certifique-se de que toda a cadeia de certificados é vista no navegador.

   ![Certificados de raiz fidedignos](media/self-signed-certificates/trusted-root-cert.png)

   > [!NOTE]
   > Presume-se que o DNS foi configurado para apontar o nome do servidor web (neste exemplo, www.fabrikam.com) para o endereço IP do seu servidor web. Caso contrário, pode editar o ficheiro dos [anfitriões](https://answers.microsoft.com/en-us/windows/forum/all/how-to-edit-host-file-in-windows-10/7696f204-2aaf-4111-913b-09d6917f7f3d) para resolver o nome.
1. Navegue no seu site e clique no ícone de bloqueio na caixa de endereços do seu navegador para verificar as informações do site e do certificado.

## <a name="verify-the-configuration-with-openssl"></a>Verifique a configuração com OpenSSL

Ou, pode usar o OpenSSL para verificar o certificado.

```
openssl s_client -connect localhost:443 -servername www.fabrikam.com -showcerts
```

![Verificação de certificados OpenSSL](media/self-signed-certificates/openssl-verify.png)

## <a name="upload-the-root-certificate-to-application-gateways-http-settings"></a>Faça o upload do certificado de raiz para as definições HTTP do Gateway de Aplicação

Para fazer o upload do certificado no Application Gateway, tem de exportar o certificado .crt para um formato .cer Base-64 codificado. Uma vez que .crt já contém a chave pública no formato codificado base-64, basta mudar o nome da extensão do ficheiro de .crt para .cer. 

### <a name="azure-portal"></a>Portal do Azure

Para fazer o upload do certificado de raiz fidedigno a partir do portal, selecione as **Definições HTTP** e escolha o protocolo **HTTPS.**

![Adicionar um certificado usando o portal](media/self-signed-certificates/portal-cert.png)

### <a name="azure-powershell"></a>Azure PowerShell

Ou, pode utilizar o Azure CLI ou o Azure PowerShell para carregar o certificado raiz. O seguinte código é uma amostra Azure PowerShell.

> [!NOTE]
> A amostra seguinte adiciona um certificado de raiz fidedigno ao gateway de aplicação, cria uma nova definição HTTP e adiciona uma nova regra, assumindo que o pool de backend e o ouvinte já existem.

```azurepowershell
## Add the trusted root certificate to the Application Gateway

$gw=Get-AzApplicationGateway -Name appgwv2 -ResourceGroupName rgOne

Add-AzApplicationGatewayTrustedRootCertificate `
   -ApplicationGateway $gw `
   -Name CustomCARoot `
   -CertificateFile "C:\Users\surmb\Downloads\contoso.cer"

$trustedroot = Get-AzApplicationGatewayTrustedRootCertificate `
   -Name CustomCARoot `
   -ApplicationGateway $gw

## Get the listener, backend pool and probe

$listener = Get-AzApplicationGatewayHttpListener `
   -Name basichttps `
   -ApplicationGateway $gw

$bepool = Get-AzApplicationGatewayBackendAddressPool `
  -Name testbackendpool `
  -ApplicationGateway $gw

Add-AzApplicationGatewayProbeConfig `
  -ApplicationGateway $gw `
  -Name testprobe `
  -Protocol Https `
  -HostName "www.fabrikam.com" `
  -Path "/" `
  -Interval 15 `
  -Timeout 20 `
  -UnhealthyThreshold 3

$probe = Get-AzApplicationGatewayProbeConfig `
  -Name testprobe `
  -ApplicationGateway $gw

## Add the configuration to the HTTP Setting and don't forget to set the "hostname" field
## to the domain name of the server certificate as this will be set as the SNI header and
## will be used to verify the backend server's certificate. Note that TLS handshake will
## fail otherwise and might lead to backend servers being deemed as Unhealthy by the probes

Add-AzApplicationGatewayBackendHttpSettings `
  -ApplicationGateway $gw `
  -Name testbackend `
  -Port 443 `
  -Protocol Https `
  -Probe $probe `
  -TrustedRootCertificate $trustedroot `
  -CookieBasedAffinity Disabled `
  -RequestTimeout 20 `
  -HostName www.fabrikam.com

## Get the configuration and update the Application Gateway

$backendhttp = Get-AzApplicationGatewayBackendHttpSettings `
  -Name testbackend `
  -ApplicationGateway $gw

Add-AzApplicationGatewayRequestRoutingRule `
  -ApplicationGateway $gw `
  -Name testrule `
  -RuleType Basic `
  -BackendHttpSettings $backendhttp `
  -HttpListener $listener `
  -BackendAddressPool $bepool

Set-AzApplicationGateway -ApplicationGateway $gw 
```

### <a name="verify-the-application-gateway-backend-health"></a>Verifique a saúde do backend do gateway de aplicação

1. Clique na vista **Backend Health** da sua porta de aplicação para verificar se a sonda está saudável.
1. Deve ver se o Estado é **saudável** para a sonda HTTPS.

![Sonda HTTPS](media/self-signed-certificates/https-probe.png)

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre sSL\TLS em Gateway de aplicação, consulte [a visão geral da rescisão de TLS e o fim do TLS com o Gateway de aplicações](ssl-overview.md).