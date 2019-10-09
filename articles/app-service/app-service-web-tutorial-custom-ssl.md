---
title: Carregar e associar certificado SSL-serviço de Azure App | Microsoft Docs
description: Saiba como vincular um certificado SSL personalizado a aplicações Web, ao back-end de aplicações móveis ou a aplicações API no Serviço de Aplicações do Azure.
services: app-service\web
documentationcenter: nodejs
author: cephalin
manager: erikre
editor: ''
ms.assetid: 5d5bf588-b0bb-4c6d-8840-1b609cfb5750
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 06/06/2019
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: c5095bc8c274ef0985b00459b0d088371ab24d88
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2019
ms.locfileid: "72177033"
---
# <a name="tutorial-upload-and-bind-ssl-certificates-to-azure-app-service"></a>Tutorial: Carregar e associar certificados SSL ao serviço Azure App

O [Serviço de Aplicações do Azure](overview.md) oferece um serviço de alojamento na Web altamente dimensionável e com correção automática. Este tutorial mostra como proteger um domínio personalizado no serviço de aplicativo com um certificado que você comprou de uma autoridade de certificação confiável. Ele também mostra como carregar quaisquer certificados públicos e privados de que seu aplicativo precisa. Quando tiver terminado, você poderá acessar seu aplicativo no ponto de extremidade HTTPS do seu domínio DNS personalizado.

![Aplicação Web com certificado SSL personalizado](./media/app-service-web-tutorial-custom-ssl/app-with-custom-ssl.png)

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Atualizar o escalão de preço da sua aplicação
> * Proteger um domínio personalizado com um certificado
> * Carregar um certificado privado
> * Carregar um certificado público
> * Renovar certificados
> * Impor HTTPS
> * Impor TLS 1.1/1.2
> * Automatizar a gestão de TLS com scripts

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial:

- [Criar uma aplicação do Serviço de Aplicações](/azure/app-service/)
- [Mapear um nome DNS personalizado para seu aplicativo do serviço de aplicativo](app-service-web-tutorial-custom-domain.md) (se estiver protegendo um domínio personalizado)
- Adquirir um certificado de uma autoridade de certificação confiável
- Ter a chave privada que você usou para assinar a solicitação de certificado (para certificados privados)

<a name="requirements"></a>

## <a name="prepare-a-private-certificate"></a>Preparar um certificado privado

Para proteger um domínio, o certificado deve atender a todos os seguintes requisitos:

* Configurado para autenticação do servidor
* Ser assinado por uma autoridade de certificação fidedigna
* Ser exportado como um ficheiro PFX protegido por palavra-passe
* Conter uma chave privada com, pelo menos, 2048 bits de comprimento
* Conter todos os certificados intermédios na cadeia de certificados

> [!TIP]
> Se você precisar obter um certificado SSL personalizado, poderá obter um no portal do Azure diretamente e importá-lo para seu aplicativo. Siga o [tutorial App Service Certificates tutorial](web-sites-purchase-ssl-web-site.md) (Certificados do Serviço de Aplicações).

> [!NOTE]
> Os **certificados de criptografia de curva elíptica (ECC)** podem funcionar com o Serviço de Aplicações, mas não são abordados neste artigo. Trabalhe com a sua autoridade de certificados para saber quais são os passos exatos para criar certificados ECC.

Depois de obter um certificado do seu provedor de certificado, siga as etapas nesta seção para deixá-lo pronto para o serviço de aplicativo.

### <a name="merge-intermediate-certificates"></a>Intercalar certificados intermédios

Se a sua autoridade de certificação lhe der vários certificados na cadeia de certificados, terá de intercalá-los por ordem.

Para tal, abra cada certificado recebido num editor de texto.

Crie um ficheiro para o certificado intercalado, denominado _mergedcertificate.crt_. Num editor de texto, copie o conteúdo de cada certificado para este ficheiro. A ordem dos seus certificados deve seguir a ordem na cadeia de certificados, a começar no seu certificado e a terminar no certificado de raiz. O aspeto é igual ao do exemplo abaixo:

```
-----BEGIN CERTIFICATE-----
<your entire Base64 encoded SSL certificate>
-----END CERTIFICATE-----

-----BEGIN CERTIFICATE-----
<The entire Base64 encoded intermediate certificate 1>
-----END CERTIFICATE-----

-----BEGIN CERTIFICATE-----
<The entire Base64 encoded intermediate certificate 2>
-----END CERTIFICATE-----

-----BEGIN CERTIFICATE-----
<The entire Base64 encoded root certificate>
-----END CERTIFICATE-----
```

### <a name="export-certificate-to-pfx"></a>Exportar o certificado para PFX

Exporte o certificado SSL intercalado com a chave privada com que o pedido de certificado foi gerado.

Se tiver gerado o pedido de certificado com OpenSSL, significa que criou um ficheiro de chave privada. Para exportar o certificado para PFX, execute o seguinte comando. Substitua os marcadores de posição  _&lt;private-key-file>_ e _&lt;merged-certificate-file>_ pelos caminhos para a chave privada e o ficheiro de certificado intercalado.

```bash
openssl pkcs12 -export -out myserver.pfx -inkey <private-key-file> -in <merged-certificate-file>  
```

Quando lhe for pedido, defina uma palavra-passe de exportação. Vai utilizar esta palavra-passe ao carregar o certificado SSL para o Serviço de Aplicações mais tarde.

Se tiver utilizado o IIS ou _Certreq.exe_ para gerar o pedido de certificado, instale o certificado no seu computador local e [exporte-o para PFX](https://technet.microsoft.com/library/cc754329(v=ws.11).aspx).

Agora você está pronto para carregar o certificado no serviço de aplicativo.

[!INCLUDE [Prepare your web app](../../includes/app-service-ssl-prepare-app.md)]

<a name="upload"></a>

## <a name="secure-a-custom-domain"></a>Proteger um domínio personalizado

> [!TIP]
> Se você precisar obter um certificado SSL personalizado, poderá obter um no portal do Azure diretamente e associá-lo ao seu aplicativo. Siga o [tutorial App Service Certificates tutorial](web-sites-purchase-ssl-web-site.md) (Certificados do Serviço de Aplicações).

Para proteger um [domínio personalizado](app-service-web-tutorial-custom-domain.md) com um certificado de terceiros, carregue o [certificado particular preparado](#prepare-a-private-certificate) e, em seguida, associe-o ao domínio personalizado, mas o serviço de aplicativo simplifica o processo para você. Execute as seguintes etapas:

Clique em **domínios personalizados** no painel de navegação esquerdo do seu aplicativo e clique em **Adicionar Associação** para o domínio que você deseja proteger. Se você não vir **Adicionar Associação** para um domínio, ele já estará seguro e deverá ter um estado SSL **seguro** .

![Adicionar associação ao domínio](./media/app-service-web-tutorial-custom-ssl/secure-domain-launch.png)

Clique em **Carregar Certificado**.

Em **Ficheiro de Certificado PFX**, selecione o ficheiro PFX. Em **Palavra-passe do certificado**, escreva a palavra-passe que criou quando exportou o ficheiro PFX.

Clique em **Carregar**.

![Carregar certificado para o domínio](./media/app-service-web-tutorial-custom-ssl/secure-domain-upload.png)

Aguarde até que o Azure carregue seu certificado e inicie a caixa de diálogo associações SSL.

Na caixa de diálogo associações SSL, selecione o certificado que você carregou e o tipo de SSL e clique em **Adicionar Associação**.

> [!NOTE]
> Há suporte para os seguintes tipos de SSL:
>
> - **[SSL baseado em SNI](https://en.wikipedia.org/wiki/Server_Name_Indication)** – várias associações SSL baseadas em SNI podem ser adicionadas. Esta opção permite utilizar vários certificados SSL para proteger múltiplos domínios no mesmo endereço IP. Os browsers mais modernos (incluindo o Internet Explorer, o Chrome, o Firefox e o Opera) suportam SNI (encontre informações mais abrangentes sobre o suporte de browsers em [Server Name Indication](https://wikipedia.org/wiki/Server_Name_Indication) [Indicação do Nome de Servidor]).
> - **SSL baseado em IP** - só pode ser adicionado um enlace SSL baseado em IP. Esta opção permite utilizar apenas um certificado SSL para proteger um endereço IP público dedicado. Para proteger vários domínios, tem de protegê-los a todos com o mesmo certificado SSL. Esta é a opção tradicional para o enlace de SSL.

![Associar SSL ao domínio](./media/app-service-web-tutorial-custom-ssl/secure-domain-bind.png)

O estado SSL do domínio agora deve ser alterado para **seguro**.

![Domínio protegido](./media/app-service-web-tutorial-custom-ssl/secure-domain-finished.png)

> [!NOTE]
> Um estado **seguro** nos **domínios personalizados** significa que ele é protegido com um certificado, mas o serviço de aplicativo não verifica se o certificado é autoassinado ou expirou, por exemplo, que também pode fazer com que os navegadores mostrem um erro ou aviso.

## <a name="remap-a-record-for-ip-ssl"></a>Remapear um registo A para SSL IP

Se você não usar SSL baseado em IP em seu aplicativo, pule para [testar HTTPS para seu domínio personalizado](#test).

Por padrão, seu aplicativo usa um endereço IP público compartilhado. Quando você associa um certificado com SSL baseado em IP, o serviço de aplicativo cria um novo endereço IP dedicado para seu aplicativo.

Se você mapeou um registro a para seu aplicativo, atualize o registro de domínio com esse novo endereço IP dedicado.

A página de **domínio personalizado** do seu aplicativo é atualizada com o novo endereço IP dedicado. [Copie este endereço IP](app-service-web-tutorial-custom-domain.md#info) e [remapeie o registo A ](app-service-web-tutorial-custom-domain.md#map-an-a-record) para este endereço IP novo.

<a name="test"></a>

## <a name="test-https"></a>Tester HTTPS

Agora, só falta confirmar que HTTPS funciona no seu domínio personalizado. Em vários navegadores, navegue até `https://<your.custom.domain>` para ver que ele serve para o seu aplicativo.

![Navegação do portal para a aplicação do Azure](./media/app-service-web-tutorial-custom-ssl/app-with-custom-ssl.png)

> [!NOTE]
> Se seu aplicativo fornecer erros de validação de certificado, provavelmente você está usando um certificado autoassinado.
>
> Se não for esse o caso, poderá ter deixou de fora certificados intermédios quando exportou o certificado para o ficheiro PFX.

## <a name="renew-certificates"></a>Renovar certificados

O endereço IP de entrada pode alterar-se quando elimina um enlace, mesmo que esse enlace seja baseado no IP. Isto é especialmente importante quando renovar um certificado que já está num enlace baseado no IP. Para evitar uma alteração de endereço IP da sua aplicação, siga estes passos por ordem:

1. Carregar o novo certificado.
2. Vincular o novo certificado com o domínio personalizado que pretende sem eliminar os antigo. Esta ação substitui o enlace em vez de remover o antigo.
3. Eliminar o certificado antigo. 

<a name="bkmk_enforce"></a>

## <a name="enforce-https"></a>Impor HTTPS

Por padrão, qualquer pessoa ainda pode acessar seu aplicativo usando HTTP. Pode redirecionar todos os pedidos HTTP para a porta HTTPS.

Na página do aplicativo, no painel de navegação esquerdo, selecione **configurações de SSL**. Em seguida, em **HTTPS Apenas**, selecione **Ativado**.

![Impor HTTPS](./media/app-service-web-tutorial-custom-ssl/enforce-https.png)

Quando a operação for concluída, navegue para um dos URLs HTTP que apontam para a sua aplicação. Por exemplo:

- `http://<app_name>.azurewebsites.net`
- `http://contoso.com`
- `http://www.contoso.com`

## <a name="enforce-tls-versions"></a>Impor versões do TLS

A aplicação permite o [TLS](https://wikipedia.org/wiki/Transport_Layer_Security) 1.2 por predefinição, o que é o nível do TLS recomendado pelas normas do setor, como [PCI DSS](https://wikipedia.org/wiki/Payment_Card_Industry_Data_Security_Standard). Para impor versões do TLS diferentes, siga estes passos:

Na página do aplicativo, no painel de navegação esquerdo, selecione **configurações de SSL**. Em seguida, na **versão do TLS**, selecione a versão mínima do TLS que pretende. Esta definição controla apenas as chamadas de entrada. 

![Impor TLS 1.1 ou 1.2](./media/app-service-web-tutorial-custom-ssl/enforce-tls1.2.png)

Quando a operação for concluída, a sua aplicação rejeita todas as ligações com versões do TLS inferiores.

## <a name="automate-with-scripts"></a>Automatizar com scripts

Você pode automatizar associações SSL para seu aplicativo com scripts, usando o [CLI do Azure](/cli/azure/install-azure-cli) ou [Azure PowerShell](/powershell/azure/overview).

### <a name="azure-cli"></a>CLI do Azure

O comando seguinte carrega um ficheiro PFX exportado e obtém o thumbprint.

```azurecli-interactive
thumbprint=$(az webapp config ssl upload \
    --name <app-name> \
    --resource-group <resource-group-name> \
    --certificate-file <path-to-PFX-file> \
    --certificate-password <PFX-password> \
    --query thumbprint \
    --output tsv)
```

O comando seguinte utiliza o thumbprint do comando anterior para adicionar um enlace SSL baseado em SNI.

```azurecli-interactive
az webapp config ssl bind \
    --name <app-name> \
    --resource-group <resource-group-name> \
    --certificate-thumbprint $thumbprint \
    --ssl-type SNI \
```

O comando a seguir força o aplicativo a usar HTTPS.

```azurecli-interactive
az webapp update \
    --name <app-name> \
    --resource-group <resource-group-name> \
    --https-only true
```

O seguinte comando impõe a versão mínima do TLS de 1.2.

```azurecli-interactive
az webapp config set \
    --name <app-name> \
    --resource-group <resource-group-name> \
    --min-tls-version 1.2
```

### <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

O comando seguinte carrega um ficheiro PFX exportado e adiciona um enlace SSL baseado em SNI.

```powershell
New-AzWebAppSSLBinding `
    -WebAppName <app_name> `
    -ResourceGroupName <resource_group_name> `
    -Name <dns_name> `
    -CertificateFilePath <path_to_PFX_file> `
    -CertificatePassword <PFX_password> `
    -SslState SniEnabled
```

## <a name="use-certificates-in-your-code"></a>Usar certificados em seu código

Se seu aplicativo precisar se conectar a recursos remotos e o recurso remoto exigir autenticação de certificado, você poderá carregar certificados públicos ou privados para seu aplicativo. Você não precisa associar esses certificados a qualquer domínio personalizado em seu aplicativo. Para obter mais informações, veja [Use an SSL certificate in your application code in Azure App Service](app-service-web-ssl-cert-load.md) (Utilizar um certificado SSL no código da sua aplicação no Serviço de Aplicações do Azure).

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Atualizar o escalão de preço da sua aplicação
> * Vincular o certificado personalizado ao Serviço de Aplicações
> * Renovar certificados
> * Impor HTTPS
> * Impor TLS 1.1/1.2
> * Automatizar a gestão de TLS com scripts

Avance para o próximo tutorial para saber como utilizar a Rede de Entrega de Conteúdos do Azure.

> [!div class="nextstepaction"]
> [Add a Content Delivery Network (CDN) to an Azure App Service](../cdn/cdn-add-to-web-app.md) (Adicionar uma Rede de Entrega de Conteúdos (CDN) ao Serviço de Aplicações do Azure)

Para obter mais informações, veja [Use an SSL certificate in your application code in Azure App Service](app-service-web-ssl-cert-load.md) (Utilizar um certificado SSL no código da sua aplicação no Serviço de Aplicações do Azure).
