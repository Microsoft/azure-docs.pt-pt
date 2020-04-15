---
title: Gerar certificado auto-assinado com uma raiz personalizada CA
titleSuffix: Azure Application Gateway
description: Saiba como gerar um certificado auto-assinado do Azure Application Gateway com uma raiz personalizada CA
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 07/23/2019
ms.author: victorh
ms.openlocfilehash: 5ceefb076b63df942cfff202946f6b82050bbab9
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/14/2020
ms.locfileid: "81311949"
---
# <a name="generate-an-azure-application-gateway-self-signed-certificate-with-a-custom-root-ca"></a>Gere um certificado auto-assinado do Azure Application Gateway com uma raiz personalizada CA

O Gateway de aplicação v2 SKU introduz a utilização de Certificados de Raiz Fidedignos para permitir servidores de backend. Isto remove os certificados de autenticação que foram exigidos no V1 SKU. O *certificado de raiz* é um Base-64 codificado X.509(. Cer) certificado raiz de formato do servidor de certificado backend. Identifica a autoridade do certificado de raiz (CA) que emitiu o certificado de servidor e o certificado de servidor é então utilizado para a comunicação TLS/SSL.

Application Gateway confia no certificado do seu website por padrão se for assinado por um conhecido CA (por exemplo, GoDaddy ou DigiCert). Não precisas de carregar explicitamente o certificado de raiz nesse caso. Para mais informações, consulte a [visão geral da rescisão do TLS e termine com o TLS com o Gateway](ssl-overview.md)de Aplicação . No entanto, se tiver um ambiente de dev/teste e não quiser adquirir um certificado assinado pela AC, pode criar o seu próprio CA personalizado e criar um certificado auto-assinado com ele. 

> [!NOTE]
> Os certificados auto-assinados não são confiáveis por defeito e podem ser difíceis de manter. Além disso, podem usar suítes de haxixe e cifra desatualizadas que podem não ser fortes. Para uma melhor segurança, compre um certificado assinado por uma conhecida autoridade de certificados.

Neste artigo, aprenderá a:

- Crie a sua própria Autoridade de Certificados Personalizados
- Crie um certificado auto-assinado assinado pelo seu CA personalizado
- Faça upload de um certificado de raiz auto-assinado para um Gateway de aplicação para autenticar o servidor backend

## <a name="prerequisites"></a>Pré-requisitos

- **[Abre o SSL](https://www.openssl.org/) num computador que executa windows ou Linux** 

   Embora possa haver outras ferramentas disponíveis para a gestão de certificados, este tutorial utiliza o OpenSSL. Você pode encontrar OpenSSL agregado com muitas distribuições Linux, como Ubuntu.
- **Um servidor web**

   Por exemplo, Apache, IIS ou NGINX para testar os certificados.

- **Um Gateway de Aplicação v2 SKU**
   
  Se não tiver um portal de aplicações existente, consulte [Quickstart: Tráfego web direto com portal Azure Application Gateway - Portal Azure](quick-create-portal.md).

## <a name="create-a-root-ca-certificate"></a>Criar um certificado ca raiz

Crie o seu certificado CA raiz utilizando o OpenSSL.

### <a name="create-the-root-key"></a>Criar a chave raiz

1. Inscreva-se no seu computador onde o OpenSSL está instalado e execute o seguinte comando. Isto cria uma chave protegida por palavra-passe.

   ```
   openssl ecparam -out contoso.key -name prime256v1 -genkey
   ```
1. No momento, escreva uma senha forte. Por exemplo, pelo menos nove caracteres, utilizando maiúsculas, minúsculas, números e símbolos.

### <a name="create-a-root-certificate-and-self-sign-it"></a>Crie um Certificado de Raiz e auto-assine-o

1. Utilize os seguintes comandos para gerar o csr e o certificado.

   ```
   openssl req -new -sha256 -key contoso.key -out contoso.csr

   openssl x509 -req -sha256 -days 365 -in contoso.csr -signkey contoso.key -out contoso.crt
   ```
   Os comandos anteriores criam o certificado de raiz. Vai usar isto para assinar o seu certificado de servidor.

1. Quando solicitado, digite a palavra-passe para a chave raiz, e as informações organizacionais para o CA personalizado, tais como Country, State, Org, OU, e o nome de domínio totalmente qualificado (este é o domínio do emitente).

   ![criar certificado de raiz](media/self-signed-certificates/root-cert.png)

## <a name="create-a-server-certificate"></a>Criar um certificado de servidor

Em seguida, criará um certificado de servidor utilizando o OpenSSL.

### <a name="create-the-certificates-key"></a>Criar a chave do certificado

Utilize o seguinte comando para gerar a chave para o certificado do servidor.

   ```
   openssl ecparam -out fabrikam.key -name prime256v1 -genkey
   ```

### <a name="create-the-csr-certificate-signing-request"></a>Criar o CSR (Pedido de Assinatura de Certificado)

A RSE é uma chave pública que é dada a uma AC ao solicitar um certificado. A AC emite o certificado para este pedido específico.

> [!NOTE]
> O NC (Nome Comum) para o certificado do servidor deve ser diferente do domínio do emitente. Por exemplo, neste caso, o NC `www.contoso.com` para o emitente `www.fabrikam.com`é e o NC do certificado de servidor é .


1. Utilize o seguinte comando para gerar a RSE:

   ```
   openssl req -new -sha256 -key fabrikam.key -out fabrikam.csr
   ```

1. Quando solicitado, digite a palavra-passe para a chave raiz, e as informações organizacionais para o CA personalizado: País, Estado, Org, OU, e o nome de domínio totalmente qualificado. Este é o domínio do site e deve ser diferente do emitente.

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

   ![Verificação de certificados](media/self-signed-certificates/verify-cert.png)

1. Verifique os ficheiros no seu diretório e certifique-se de que tem os seguintes ficheiros:

   - contoso.crt
   - contoso.chave
   - fabrikam.crt
   - fabrikam.key

## <a name="configure-the-certificate-in-your-web-servers-tls-settings"></a>Configure o certificado nas definições de TLS do seu servidor web

No seu servidor web, configure os ficheiros tLS utilizando os ficheiros fabrikam.crt e fabrikam.key. Se o seu servidor web não conseguir utilizar dois ficheiros, pode combiná-los com um único ficheiro .pem ou .pfx utilizando comandos OpenSSL.

### <a name="iis"></a>IIS

Para obter instruções sobre como importar certificado e carregá-los como certificado de servidor no IIS, consulte [COMO Instalar Certificados Importados num Servidor Web no Windows Server 2003](https://support.microsoft.com/help/816794/how-to-install-imported-certificates-on-a-web-server-in-windows-server).

Para instruções de ligação TLS, consulte [Como configurar o SSL no IIS 7](https://docs.microsoft.com/iis/manage/configuring-security/how-to-set-up-ssl-on-iis#create-an-ssl-binding-1).

### <a name="apache"></a>Apache

A seguinte configuração é um [exemplo de hospedeiro virtual configurado para SSL](https://cwiki.apache.org/confluence/display/HTTPD/NameBasedSSLVHosts) em Apache:

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

A seguinte configuração é um bloco de [servidor NGINX](https://nginx.org/docs/http/configuring_https_servers.html) de exemplo com configuração TLS:

![NGINX com TLS](media/self-signed-certificates/nginx-ssl.png)

## <a name="access-the-server-to-verify-the-configuration"></a>Aceda ao servidor para verificar a configuração

1. Adicione o certificado de raiz ao armazém de raiz fidedigno da sua máquina. Ao aceder ao site, certifique-se de que toda a cadeia de certificados é vista no navegador.

   ![Certificados de raiz fidedignos](media/self-signed-certificates/trusted-root-cert.png)

   > [!NOTE]
   > Presume-se que o DNS foi configurado para apontar o nome do servidor web (neste exemplo, www.fabrikam.com) para o endereço IP do seu servidor web. Caso contrário, pode editar o ficheiro dos [anfitriões](https://answers.microsoft.com/en-us/windows/forum/all/how-to-edit-host-file-in-windows-10/7696f204-2aaf-4111-913b-09d6917f7f3d) para resolver o nome.
1. Navegue no seu site e clique no ícone de bloqueio na caixa de endereços do seu navegador para verificar as informações do site e do certificado.

## <a name="verify-the-configuration-with-openssl"></a>Verifique a configuração com OpenSSL

Ou pode usar o OpenSSL para verificar o certificado.

```
openssl s_client -connect localhost:443 -servername www.fabrikam.com -showcerts
```

![Verificação de certificado openssl](media/self-signed-certificates/openssl-verify.png)

## <a name="upload-the-root-certificate-to-application-gateways-http-settings"></a>Faça upload do certificado de raiz para as definições http do Gateway de aplicação

Para fazer o upload do certificado no Gateway de Aplicação, deve exportar o certificado .crt para um formato .cer Base-64 codificado. Uma vez que .crt já contém a chave pública no formato codificado base-64, basta renomear a extensão do ficheiro de .crt para .cer. 

### <a name="azure-portal"></a>Portal do Azure

Para fazer o upload do certificado raiz fidedigno do portal, selecione as **Definições HTTP** e escolha o protocolo **HTTPS.**

![Adicione um certificado usando o portal](media/self-signed-certificates/portal-cert.png)

### <a name="azure-powershell"></a>Azure PowerShell

Ou pode utilizar o Azure CLI ou o Azure PowerShell para fazer o upload do certificado de raiz. O código seguinte é uma amostra Azure PowerShell.

> [!NOTE]
> A amostra seguinte adiciona um certificado de raiz fidedigno ao gateway da aplicação, cria uma nova definição HTTP e adiciona uma nova regra, assumindo que o pool backend e o ouvinte já existem.

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

### <a name="verify-the-application-gateway-backend-health"></a>Verifique a porta de aplicação backend health

1. Clique na visão **backend Health** da sua porta de entrada de aplicação para verificar se a sonda está saudável.
1. Deve ver que o Estado é **saudável** para a sonda HTTPS.

![Sonda HTTPS](media/self-signed-certificates/https-probe.png)

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre o SSL\TLS no Gateway de Aplicação, consulte a [visão geral da rescisão de TLS e termine com tLS com Application Gateway](ssl-overview.md).

