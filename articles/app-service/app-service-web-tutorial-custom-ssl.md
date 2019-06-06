---
title: Carregar e vincular o certificado SSL - serviço de aplicações do Azure | Documentos da Microsoft
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
ms.openlocfilehash: 32e6311a8796e708119f3e1df813b6ebb2ed0673
ms.sourcegitcommit: 7042ec27b18f69db9331b3bf3b9296a9cd0c0402
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/06/2019
ms.locfileid: "66743016"
---
# <a name="tutorial-upload-and-bind-ssl-certificates-to-azure-app-service"></a>Tutorial: Carregar e vincular certificados SSL para o serviço de aplicações do Azure

O [Serviço de Aplicações do Azure](overview.md) oferece um serviço de alojamento na Web altamente dimensionável e com correção automática. Este tutorial mostra-lhe como proteger um domínio personalizado no serviço de aplicações com um certificado que tiver adquirido de uma autoridade de certificação fidedigna. Também mostra-lhe como carregar qualquer privado e público certificados necessidades da sua aplicação. Quando tiver terminado, será capaz de aceder à sua aplicação no ponto final de HTTPS do seu domínio DNS personalizado.

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
- [Mapear um nome DNS personalizado para a aplicação de serviço de aplicações](app-service-web-tutorial-custom-domain.md) (se proteger um domínio personalizado)
- Adquirir um certificado de uma autoridade de certificado fidedigno
- Tem a chave privada utilizada para assinar o pedido de certificado (para certificados privados)

<a name="requirements"></a>

## <a name="prepare-a-private-certificate"></a>Preparar um certificado privado

Para proteger a um domínio, o certificado tem de cumprir os seguintes requisitos:

* Configurado para autenticação de servidor
* Ser assinado por uma autoridade de certificação fidedigna
* Ser exportado como um ficheiro PFX protegido por palavra-passe
* Conter uma chave privada com, pelo menos, 2048 bits de comprimento
* Conter todos os certificados intermédios na cadeia de certificados

> [!TIP]
> Se precisar de obter um certificado SSL personalizado, pode obtê-lo no portal do Azure diretamente e importá-lo à sua aplicação. Siga o [tutorial App Service Certificates tutorial](web-sites-purchase-ssl-web-site.md) (Certificados do Serviço de Aplicações).

> [!NOTE]
> Os **certificados de criptografia de curva elíptica (ECC)** podem funcionar com o Serviço de Aplicações, mas não são abordados neste artigo. Trabalhe com a sua autoridade de certificados para saber quais são os passos exatos para criar certificados ECC.

Depois de obter um certificado a partir do seu fornecedor do certificado, siga os passos nesta secção para torná-lo pronto para o serviço de aplicações.

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

Está agora pronto para carregar o certificado do serviço de aplicações.

[!INCLUDE [Prepare your web app](../../includes/app-service-ssl-prepare-app.md)]

<a name="upload"></a>

## <a name="secure-a-custom-domain"></a>Proteger um domínio personalizado

> [!TIP]
> Se precisar de obter um certificado SSL personalizado, pode obtê-lo no portal do Azure diretamente e vinculá-lo à sua aplicação. Siga o [tutorial App Service Certificates tutorial](web-sites-purchase-ssl-web-site.md) (Certificados do Serviço de Aplicações).

Para proteger uma [domínio personalizado](app-service-web-tutorial-custom-domain.md) com um certificado de terceiros, que carrega o [preparado certificado privado](#prepare-a-private-certificate) e, em seguida, vinculá-lo para o domínio personalizado, mas o serviço de aplicações simplifica o processo para. Siga os passos abaixo:

Clique em **domínios personalizados** no painel de navegação à esquerda da sua aplicação, em seguida, clique em **Adicionar enlace** para o domínio que pretende proteger. Se não vir **Adicionar enlace** para um domínio, em seguida, ele já é seguro e deve ter um **seguro** estado de SSL.

![Adicionar ligação ao domínio](./media/app-service-web-tutorial-custom-ssl/secure-domain-launch.png)

Clique em **Carregar Certificado**.

Em **Ficheiro de Certificado PFX**, selecione o ficheiro PFX. Em **Palavra-passe do certificado**, escreva a palavra-passe que criou quando exportou o ficheiro PFX.

Clique em **Carregar**.

![Carregar o certificado para o domínio](./media/app-service-web-tutorial-custom-ssl/secure-domain-upload.png)

A aguardar que o Azure para carregar o seu certificado e iniciar a caixa de diálogo de enlaces de SSL.

Na caixa de diálogo de enlaces de SSL, selecione o certificado que carregou e o tipo SSL e, em seguida, clique em **Adicionar enlace**.

> [!NOTE]
> São suportados os seguintes tipos SSL:
>
> - **[SSL baseado em SNI](https://en.wikipedia.org/wiki/Server_Name_Indication)**  -enlaces SSL baseado em SNI várias podem ser adicionados. Esta opção permite utilizar vários certificados SSL para proteger múltiplos domínios no mesmo endereço IP. Os browsers mais modernos (incluindo o Internet Explorer, o Chrome, o Firefox e o Opera) suportam SNI (encontre informações mais abrangentes sobre o suporte de browsers em [Server Name Indication](https://wikipedia.org/wiki/Server_Name_Indication) [Indicação do Nome de Servidor]).
> - **SSL baseado em IP** - só pode ser adicionado um enlace SSL baseado em IP. Esta opção permite utilizar apenas um certificado SSL para proteger um endereço IP público dedicado. Para proteger vários domínios, tem de protegê-los a todos com o mesmo certificado SSL. Esta é a opção tradicional para o enlace de SSL.

![Enlace SSL ao domínio](./media/app-service-web-tutorial-custom-ssl/secure-domain-bind.png)

Estado de SSL do domínio deve agora ser alterado para **seguro**.

![Domínio protegido](./media/app-service-web-tutorial-custom-ssl/secure-domain-finished.png)

> [!NOTE]
> R **seguro** Estado no **domínios personalizados** significa que ele é protegido por um certificado, mas o serviço de aplicações não verifica se o certificado é autoassinado ou expirado, por exemplo, que também pode fazer com que os navegadores Mostre um erro ou aviso.

## <a name="remap-a-record-for-ip-ssl"></a>Remapear um registo A para SSL IP

Se não utilizar o SSL baseado em IP na sua aplicação, avance para o [testar HTTPS para o seu domínio personalizado](#test).

Por predefinição, a sua aplicação utiliza um endereço IP público partilhado. Quando vincula um certificado com SSL baseado em IP, o serviço de aplicações cria um novo endereço IP dedicado para a sua aplicação.

Se tiver mapeado um registo para a sua aplicação, atualize o registo do seu domínio com este endereço IP dedicado novo.

Seu aplicativo **Custom domain** página é atualizada com o endereço IP dedicado novo. [Copie este endereço IP](app-service-web-tutorial-custom-domain.md#info) e [remapeie o registo A ](app-service-web-tutorial-custom-domain.md#map-an-a-record) para este endereço IP novo.

<a name="test"></a>

## <a name="test-https"></a>Tester HTTPS

Agora, só falta confirmar que HTTPS funciona no seu domínio personalizado. Em vários browsers, navegue até `https://<your.custom.domain>` para ver que o mesmo serve a sua aplicação.

![Navegação do portal para a aplicação do Azure](./media/app-service-web-tutorial-custom-ssl/app-with-custom-ssl.png)

> [!NOTE]
> Se a sua aplicação dá-erros de validação do certificado, provavelmente está usando um certificado autoassinado.
>
> Se não for esse o caso, poderá ter deixou de fora certificados intermédios quando exportou o certificado para o ficheiro PFX.

## <a name="renew-certificates"></a>Renovar certificados

O endereço IP de entrada pode alterar-se quando elimina um enlace, mesmo que esse enlace seja baseado no IP. Isto é especialmente importante quando renovar um certificado que já está num enlace baseado no IP. Para evitar uma alteração de endereço IP da sua aplicação, siga estes passos por ordem:

1. Carregar o novo certificado.
2. Vincular o novo certificado com o domínio personalizado que pretende sem eliminar os antigo. Esta ação substitui o enlace em vez de remover o antigo.
3. Eliminar o certificado antigo. 

<a name="bkmk_enforce"></a>

## <a name="enforce-https"></a>Impor HTTPS

Por padrão, qualquer pessoa pode continuar a aceder à aplicação utilizando o HTTP. Pode redirecionar todos os pedidos HTTP para a porta HTTPS.

Na página da aplicação, na navegação à esquerda, selecione **definições de SSL**. Em seguida, em **HTTPS Apenas**, selecione **Ativado**.

![Impor HTTPS](./media/app-service-web-tutorial-custom-ssl/enforce-https.png)

Quando a operação for concluída, navegue para um dos URLs HTTP que apontam para a sua aplicação. Por exemplo:

- `http://<app_name>.azurewebsites.net`
- `http://contoso.com`
- `http://www.contoso.com`

## <a name="enforce-tls-versions"></a>Impor versões do TLS

A aplicação permite o [TLS](https://wikipedia.org/wiki/Transport_Layer_Security) 1.2 por predefinição, o que é o nível do TLS recomendado pelas normas do setor, como [PCI DSS](https://wikipedia.org/wiki/Payment_Card_Industry_Data_Security_Standard). Para impor versões do TLS diferentes, siga estes passos:

Na página da aplicação, na navegação à esquerda, selecione **definições de SSL**. Em seguida, na **versão do TLS**, selecione a versão mínima do TLS que pretende. Esta definição controla apenas as chamadas de entrada. 

![Impor TLS 1.1 ou 1.2](./media/app-service-web-tutorial-custom-ssl/enforce-tls1.2.png)

Quando a operação for concluída, a sua aplicação rejeita todas as ligações com versões do TLS inferiores.

## <a name="automate-with-scripts"></a>Automatizar com scripts

Pode automatizar os enlaces SSL para a sua aplicação com scripts através da [CLI do Azure](/cli/azure/install-azure-cli) ou [Azure PowerShell](/powershell/azure/overview).

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
    --resource-group <resource-group-name>
    --certificate-thumbprint $thumbprint \
    --ssl-type SNI \
```

O seguinte comando impõe a versão mínima do TLS de 1.2.

```azurecli-interactive
az webapp config set \
    --name <app-name> \
    --resource-group <resource-group-name>
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

## <a name="use-certificates-in-your-code"></a>Utilizar certificados no seu código

Se a sua aplicação precisa ligar a recursos remotos e o recurso remoto requer autenticação de certificado, pode carregar certificados públicos ou privados para a sua aplicação. Não precisa de vincular esses certificados para qualquer domínio personalizado na sua aplicação. Para obter mais informações, veja [Use an SSL certificate in your application code in Azure App Service](app-service-web-ssl-cert-load.md) (Utilizar um certificado SSL no código da sua aplicação no Serviço de Aplicações do Azure).

## <a name="next-steps"></a>Passos Seguintes

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
