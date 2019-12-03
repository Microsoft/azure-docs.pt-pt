---
title: Proteger um DNS personalizado com uma associação SSL
description: Proteja o acesso HTTPS ao seu domínio personalizado criando uma associação TLS/SSL com um certificado. Melhore a segurança de seu site impondo HTTPS ou TLS 1,2.
tags: buy-ssl-certificates
ms.topic: tutorial
ms.date: 10/25/2019
ms.reviewer: yutlin
ms.custom: seodec18
ms.openlocfilehash: b967e4cfad2444e39c7df8cfddcc2154bd48367d
ms.sourcegitcommit: 265f1d6f3f4703daa8d0fc8a85cbd8acf0a17d30
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/02/2019
ms.locfileid: "74670684"
---
# <a name="secure-a-custom-dns-name-with-an-ssl-binding-in-azure-app-service"></a>Proteger um nome DNS personalizado com uma associação SSL no serviço Azure App

Este artigo mostra como proteger o [domínio personalizado](app-service-web-tutorial-custom-domain.md) em seu aplicativo do [serviço de aplicativo](https://docs.microsoft.com/azure/app-service/) ou [aplicativo de funções](https://docs.microsoft.com/azure/azure-functions/) criando uma associação de certificado. Quando tiver terminado, você poderá acessar seu aplicativo do serviço de aplicativo no ponto de extremidade `https://` para seu nome DNS personalizado (por exemplo, `https://www.contoso.com`). 

![Aplicação Web com certificado SSL personalizado](./media/configure-ssl-bindings/app-with-custom-ssl.png)

A proteção de um [domínio personalizado](app-service-web-tutorial-custom-domain.md) com um certificado envolve duas etapas:

- [Adicione um certificado privado ao serviço de aplicativo](configure-ssl-certificate.md) que atenda a todos os [requisitos para associações SSL](configure-ssl-certificate.md#private-certificate-requirements).
-  Crie uma associação SSL com o domínio personalizado correspondente. Esta segunda etapa é abordada neste artigo.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Atualizar o escalão de preço da sua aplicação
> * Proteger um domínio personalizado com um certificado
> * Impor HTTPS
> * Impor TLS 1.1/1.2
> * Automatizar a gestão de TLS com scripts

## <a name="prerequisites"></a>Pré-requisitos

Para seguir este guia de instruções:

- [Criar uma aplicação do Serviço de Aplicações](/azure/app-service/)
- [Mapear um nome de domínio para seu aplicativo](app-service-web-tutorial-custom-domain.md) ou [comprá-lo e configurá-lo no Azure](manage-custom-dns-buy-domain.md)
- [Adicionar um certificado particular ao seu aplicativo](configure-ssl-certificate.md)

> [!NOTE]
> A maneira mais fácil de adicionar um certificado privado é [criar um certificado gerenciado do serviço de aplicativo gratuito](configure-ssl-certificate.md#create-a-free-certificate-preview) (versão prévia).

[!INCLUDE [Prepare your web app](../../includes/app-service-ssl-prepare-app.md)]

<a name="upload"></a>

## <a name="secure-a-custom-domain"></a>Proteger um domínio personalizado

Execute as seguintes etapas:

No <a href="https://portal.azure.com" target="_blank">portal do Azure</a>, no menu à esquerda, selecione **serviços de aplicativos** >  **\<nome-do-aplicativo >** .

No painel de navegação à esquerda do seu aplicativo, inicie a caixa de diálogo de **Associação TLS/SSL** :

- Selecionando **domínios personalizados** > **Adicionar Associação**
- Selecionando **configurações de TLS/ssl** > **Adicionar Associação TLS/SSL**

![Adicionar associação ao domínio](./media/configure-ssl-bindings/secure-domain-launch.png)

Em **domínio personalizado**, selecione o domínio personalizado ao qual você deseja adicionar uma associação.

Se seu aplicativo já tiver um certificado para o domínio personalizado selecionado, vá para [criar Associação](#create-binding) diretamente. Caso contrário, continue.

### <a name="add-a-certificate-for-custom-domain"></a>Adicionar um certificado para o domínio personalizado

Se seu aplicativo não tiver nenhum certificado para o domínio personalizado selecionado, você terá duas opções:

- **Carregar certificado pfx** -siga o fluxo de trabalho em [carregar um certificado privado](configure-ssl-certificate.md#upload-a-private-certificate)e selecione esta opção aqui.
- **Importar certificado do serviço de aplicativo** -siga o fluxo de trabalho em [importar um certificado do serviço de aplicativo](configure-ssl-certificate.md#import-an-app-service-certificate)e selecione esta opção aqui.

> [!NOTE]
> Você também pode [criar um certificado gratuito](configure-ssl-certificate.md#create-a-free-certificate-preview) (versão prévia) ou [importar um certificado Key Vault](configure-ssl-certificate.md#import-a-certificate-from-key-vault), mas você deve fazê-lo separadamente e retornar à caixa de diálogo de **Associação TLS/SSL** .

### <a name="create-binding"></a>Criar Associação

Use a tabela a seguir para ajudá-lo a configurar a associação SSL na caixa de diálogo de **Associação TLS/SSL** e, em seguida, clique em **Adicionar Associação**.

| Definição | Descrição |
|-|-|
| Domínio personalizado | O nome de domínio para o qual adicionar a associação SSL. |
| Impressão digital do certificado privado | O certificado a ser associado. |
| Tipo de TLS/SSL | <ul><li>**[SSL SNI](https://en.wikipedia.org/wiki/Server_Name_Indication)** -várias associações SSL SNI podem ser adicionadas. Esta opção permite utilizar vários certificados SSL para proteger múltiplos domínios no mesmo endereço IP. Os navegadores mais modernos (incluindo Internet Explorer, Chrome, Firefox e Opera) dão suporte a SNI (para obter mais informações, consulte [indicação de nome de servidor](https://wikipedia.org/wiki/Server_Name_Indication)).</li><li>**IP SSL** -somente uma associação de IP SSL pode ser adicionada. Esta opção permite utilizar apenas um certificado SSL para proteger um endereço IP público dedicado. Depois de configurar a associação, siga as etapas em [remapear um registro para IP SSL](#remap-a-record-for-ip-ssl).<br/>Só há suporte para IP SSL em camadas de produção ou isoladas. </li></ul> |

Quando a operação for concluída, o estado SSL do domínio personalizado será alterado para **seguro**.

![Associação SSL bem-sucedida](./media/configure-ssl-bindings/secure-domain-finished.png)

> [!NOTE]
> Um estado **seguro** nos **domínios personalizados** significa que ele é protegido com um certificado, mas o serviço de aplicativo não verifica se o certificado é autoassinado ou expirou, por exemplo, que também pode fazer com que os navegadores mostrem um erro ou aviso.

## <a name="remap-a-record-for-ip-ssl"></a>Remapear um registo A para SSL IP

Se você não usar IP SSL em seu aplicativo, pule para [testar HTTPS para seu domínio personalizado](#test-https).

Por padrão, seu aplicativo usa um endereço IP público compartilhado. Quando você associa um certificado ao IP SSL, o serviço de aplicativo cria um novo endereço IP dedicado para seu aplicativo.

Se você mapeou um registro a para seu aplicativo, atualize o registro de domínio com esse novo endereço IP dedicado.

A página de **domínio personalizado** do seu aplicativo é atualizada com o novo endereço IP dedicado. [Copie este endereço IP](app-service-web-tutorial-custom-domain.md#info) e [remapeie o registo A ](app-service-web-tutorial-custom-domain.md#map-an-a-record) para este endereço IP novo.

## <a name="test-https"></a>Tester HTTPS

Em vários navegadores, navegue até `https://<your.custom.domain>` para verificar se ele atende ao seu aplicativo.

![Navegação do portal para a aplicação do Azure](./media/configure-ssl-bindings/app-with-custom-ssl.png)

> [!NOTE]
> Se seu aplicativo fornecer erros de validação de certificado, provavelmente você está usando um certificado autoassinado.
>
> Se não for esse o caso, poderá ter deixou de fora certificados intermédios quando exportou o certificado para o ficheiro PFX.

## <a name="prevent-ip-changes"></a>Impedir alterações de IP

Seu endereço IP de entrada pode ser alterado quando você exclui uma associação, mesmo que essa associação seja IP SSL. Isso é especialmente importante quando você renova um certificado que já está em uma associação de IP SSL. Para evitar uma alteração de endereço IP da sua aplicação, siga estes passos por ordem:

1. Carregar o novo certificado.
2. Vincular o novo certificado com o domínio personalizado que pretende sem eliminar os antigo. Esta ação substitui o enlace em vez de remover o antigo.
3. Eliminar o certificado antigo. 

## <a name="enforce-https"></a>Impor HTTPS

Por padrão, qualquer pessoa ainda pode acessar seu aplicativo usando HTTP. Pode redirecionar todos os pedidos HTTP para a porta HTTPS.

Na página do aplicativo, no painel de navegação esquerdo, selecione **configurações de SSL**. Em seguida, em **HTTPS Apenas**, selecione **Ativado**.

![Impor HTTPS](./media/configure-ssl-bindings/enforce-https.png)

Quando a operação for concluída, navegue para um dos URLs HTTP que apontam para a sua aplicação. Por exemplo:

- `http://<app_name>.azurewebsites.net`
- `http://contoso.com`
- `http://www.contoso.com`

## <a name="enforce-tls-versions"></a>Impor versões do TLS

A aplicação permite o [TLS](https://wikipedia.org/wiki/Transport_Layer_Security) 1.2 por predefinição, o que é o nível do TLS recomendado pelas normas do setor, como [PCI DSS](https://wikipedia.org/wiki/Payment_Card_Industry_Data_Security_Standard). Para impor versões do TLS diferentes, siga estes passos:

Na página do aplicativo, no painel de navegação esquerdo, selecione **configurações de SSL**. Em seguida, na **versão do TLS**, selecione a versão mínima do TLS que pretende. Esta definição controla apenas as chamadas de entrada. 

![Impor TLS 1.1 ou 1.2](./media/configure-ssl-bindings/enforce-tls1-2.png)

Quando a operação for concluída, a sua aplicação rejeita todas as ligações com versões do TLS inferiores.

## <a name="automate-with-scripts"></a>Automatizar com scripts

### <a name="azure-cli"></a>CLI do Azure

[!code-azurecli[main](../../cli_scripts/app-service/configure-ssl-certificate/configure-ssl-certificate.sh?highlight=3-5 "Bind a custom SSL certificate to a web app")] 

### <a name="powershell"></a>PowerShell

[!code-powershell[main](../../powershell_scripts/app-service/configure-ssl-certificate/configure-ssl-certificate.ps1?highlight=1-3 "Bind a custom SSL certificate to a web app")]

## <a name="more-resources"></a>Mais recursos

* [Usar um certificado SSL no código do aplicativo](configure-ssl-certificate-in-code.md)
* [Perguntas frequentes: certificados do serviço de aplicativo](https://docs.microsoft.com/azure/app-service/faq-configuration-and-management/)
