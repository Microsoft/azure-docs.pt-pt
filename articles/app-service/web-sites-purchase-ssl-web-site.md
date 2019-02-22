---
title: Comprar e configurar um certificado SSL do Azure - serviço de aplicações | Documentos da Microsoft
description: Saiba como comprar um certificado de serviço de aplicações e vinculá-lo à sua aplicação de serviço de aplicações
services: app-service
documentationcenter: .net
author: cephalin
manager: cfowler
tags: buy-ssl-certificates
ms.assetid: cdb9719a-c8eb-47e5-817f-e15eaea1f5f8
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/16/2018
ms.author: apurvajo;cephalin
ms.custom: seodec18
ms.openlocfilehash: b569165153ce713846be5e836a26f48e500be1fc
ms.sourcegitcommit: a8948ddcbaaa22bccbb6f187b20720eba7a17edc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/21/2019
ms.locfileid: "56594139"
---
# <a name="buy-and-configure-an-ssl-certificate-for-azure-app-service"></a>Comprar e configurar um certificado SSL para o serviço de aplicações do Azure

Este tutorial mostra-lhe como proteger seus [aplicação de serviço de aplicações](https://docs.microsoft.com/azure/app-service/) ou [aplicação de funções](https://docs.microsoft.com/azure/azure-functions/) através da criação (compra) um certificado de serviço de aplicações no [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-whatis) e, em seguida, vinculá-lo para um Aplicação de serviço de aplicações.

> [!TIP]
> Certificados de serviço de aplicações pode ser usado para qualquer do Azure ou serviços do Azure e não está limitados a serviços de aplicações. Para tal, terá de criar uma cópia PFX local de um certificado de serviço de aplicações que pode usá-lo em qualquer lugar que desejar. Para obter mais informações, consulte [criar uma cópia PFX local de um certificado de serviço de aplicações](https://blogs.msdn.microsoft.com/appserviceteam/2017/02/24/creating-a-local-pfx-copy-of-app-service-certificate/).
>

## <a name="prerequisites"></a>Pré-requisitos

Para seguir este guia de procedimentos:

- [Criar uma aplicação do Serviço de Aplicações](/azure/app-service/)
- [Mapear um nome de domínio para a aplicação](app-service-web-tutorial-custom-domain.md) ou [comprar e configurá-la no Azure](manage-custom-dns-buy-domain.md)

[!INCLUDE [Prepare your web app](../../includes/app-service-ssl-prepare-app.md)]

## <a name="start-certificate-order"></a>Iniciar o pedido de certificado

Iniciar um pedido de certificado do serviço de aplicações no <a href="https://portal.azure.com/#create/Microsoft.SSL" target="_blank">certificado do serviço de aplicações criar página</a>.

![Criação do certificado](./media/app-service-web-purchase-ssl-web-site/createssl.png)

Utilize a tabela seguinte para ajudar a configurar o certificado. Quando terminar, clique em **Criar**.

| Definição | Descrição |
|-|-|
| Name | Um nome amigável para o seu certificado de serviço de aplicações. |
| Nome do Anfitrião do Domínio sem "www" | Se especificar o domínio raiz aqui, obtém um certificado que protege *ambos* o domínio de raiz e o `www` subdomínio. Para proteger qualquer subdomínio apenas, especifique o nome de domínio completamente qualificado do subdomínio aqui (por exemplo, `mysubdomain.contoso.com`). |
| Subscrição | O centro de dados onde o a aplicação Web está alojada. |
| Grupo de recursos | O grupo de recursos que contém o certificado. Pode utilizar um grupo de recursos ou selecione o mesmo grupo de recursos que a sua aplicação de serviço de aplicações, por exemplo. |
| SKU do Certificado | Determina o tipo de certificado para criar, se um certificado padrão ou uma [certificado de caráter universal](https://wikipedia.org/wiki/Wildcard_certificate). |
| Termos Legais | Clique para confirmar que aceita os termos legais. Os certificados são obtidos a partir da GoDaddy. |

## <a name="store-in-azure-key-vault"></a>Store no Cofre de chaves do Azure

Quando o processo de compra do certificado estiver concluído, há mais algumas etapas que precisa para concluir antes de começar a utilizar este certificado. 

Selecione o certificado no [certificados do serviço de aplicações](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.CertificateRegistration%2FcertificateOrders) página, em seguida, clique em **configuração de certificado** > **passo 1: Store**.

![Inserir a imagem de pronto para armazenar em KV](./media/app-service-web-purchase-ssl-web-site/ReadyKV.png)

[Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-whatis) é um serviço do Azure que ajuda a salvaguardar chaves criptográficas e segredos utilizados pelas aplicações e serviços cloud. É o armazenamento de eleição para certificados de serviço de aplicações.

Na **estado do Key Vault** página, clique em **repositório do Key Vault** para criar um novo cofre ou escolha um cofre existente. Se optar por criar um novo cofre, utilize a tabela seguinte para ajudar a configurar o Cofre e clique em criar. Ver Criar novo Key Vault dentro do mesmo grupo de recursos e subscrição.

| Definição | Descrição |
|-|-|
| Name | Um nome exclusivo que consiste em para carateres alfanuméricos e travessões. |
| Grupo de recursos | Como uma recomendação, selecione o mesmo grupo de recursos como o certificado de serviço de aplicações. |
| Localização | Selecione a mesma localização que a sua aplicação de serviço de aplicações. |
| Escalão de preço | Para obter informações, consulte [os detalhes dos preços do Azure Key Vault](https://azure.microsoft.com/pricing/details/key-vault/). |
| Políticas de acesso| Define as aplicações e de ter permissão para aceder aos recursos do cofre. Pode configurá-lo mais tarde, seguindo os passos indicados em [conceder acesso de aplicações de vários para um cofre de chaves](../key-vault/key-vault-group-permissions-for-apps.md). |
| Acesso de Rede Virtual | Restringir o acesso do cofre para determinadas redes virtuais do Azure. Pode configurá-lo mais tarde, seguindo os passos indicados em [configurar o Azure Key Vault Firewalls e redes virtuais](../key-vault/key-vault-network-security.md) |

Depois de selecionar o cofre, feche o **repositório do Key Vault** página. O **Store** opção deve mostrar uma marca de verificação verde para o sucesso. Mantenha a página aberta para a próxima etapa.

## <a name="verify-domain-ownership"></a>Verificar a propriedade do domínio

Da mesma **configuração do certificado** página que utilizou no último passo, clique em **passo 2: Certifique-se de**.

![](./media/app-service-web-purchase-ssl-web-site/verify-domain.png)

Selecione **verificação de serviço de aplicações**. Uma vez que já mapeou o domínio à sua aplicação web (consulte [pré-requisitos](#prerequisites)), ele já foi verificado. Basta clicar **Verifique se** para concluir este passo. Clique nas **Atualize** botão até que a mensagem **certificado tem o domínio verificado** aparece.

> [!NOTE]
> São suportados quatro tipos de métodos de verificação de domínio: 
> 
> - **Serviço de aplicações** -a opção mais conveniente quando o domínio já está mapeado para uma aplicação de serviço de aplicações na mesma subscrição. Tira partido do fato de que a aplicação de serviço de aplicações já verificou a propriedade do domínio.
> - **Domínio** -Certifique-se de um [domínio de serviço de aplicações que comprou na Azure](manage-custom-dns-buy-domain.md). O Azure automaticamente adiciona o registo TXT de verificação para e conclui o processo.
> - **Correio** -verificar o domínio ao enviar um e-mail para o administrador de domínio. São fornecidas instruções ao selecionar a opção.
> - **Manual** -Verifique se o domínio através de uma página HTML (**padrão** certificado apenas) ou um registo TXT de DNS. São fornecidas instruções ao selecionar a opção.

## <a name="bind-certificate-to-app"></a>Vincular o certificado à aplicação

Na  **[portal do Azure](https://portal.azure.com/)**, no menu à esquerda, selecione **serviços de aplicação** > **\<your_ aplicação >**.

No painel de navegação à esquerda da sua aplicação, selecione **definições de SSL** > **certificados privados (. pfx)** > **importar certificado do serviço de aplicações**.

![Inserir a imagem de importar certificado](./media/app-service-web-purchase-ssl-web-site/ImportCertificate.png)

Selecione o certificado que acaba de comprar.

Agora que o certificado foi importado, terá de associá-lo para um nome de domínio mapeado na sua aplicação. Selecione **enlaces** > **Adicionar enlace SSL**. 

![Inserir a imagem de importar certificado](./media/app-service-web-purchase-ssl-web-site/AddBinding.png)

Utilize a seguinte tabela para o ajudar a configurar o enlace na **enlaces SSL** caixa de diálogo, em seguida, clique em **Adicionar enlace**.

| Definição | Descrição |
|-|-|
| Nome de anfitrião | Adicionar enlace de SSL para o nome de domínio. |
| Thumbprint do Certificado Privado | O certificado para o enlace. |
| Tipo de SSL | <ul><li>**SNI SSL** -enlaces SSL baseado em SNI várias podem ser adicionados. Esta opção permite utilizar vários certificados SSL para proteger múltiplos domínios no mesmo endereço IP. Os browsers mais modernos (incluindo o Internet Explorer, o Chrome, o Firefox e o Opera) suportam SNI (encontre informações mais abrangentes sobre o suporte de browsers em [Server Name Indication](https://wikipedia.org/wiki/Server_Name_Indication) [Indicação do Nome de Servidor]).</li><li>**SSL baseado em IP** - só pode ser adicionado um enlace SSL baseado em IP. Esta opção permite utilizar apenas um certificado SSL para proteger um endereço IP público dedicado. Depois de configurar o enlace, siga os passos em [remapear um registo a para IP SSL](app-service-web-tutorial-custom-ssl.md#remap-a-record-for-ip-ssl). </li></ul> |

## <a name="verify-https-access"></a>Verificar o acesso HTTPS

Visite a sua aplicação através de `HTTPS://<domain_name>` em vez de `HTTP://<domain_name>` para verificar se o certificado foi configurado corretamente.

## <a name="rekey-and-sync-certificate"></a>A recodificação e o certificado de sincronização

Se alguma vez precisar de recodificar o certificado, selecione o certificado no [certificados do serviço de aplicações](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.CertificateRegistration%2FcertificateOrders) página, em seguida, selecione **recodificação e sincronização** no painel de navegação esquerda.

Clique em **recodificar** botão para iniciar o processo. Este processo pode demorar 1 a 10 minutos a concluir.

![Inserir a imagem de recodificação SSL](./media/app-service-web-purchase-ssl-web-site/Rekey.png)

Recodificação do certificado agrega o certificado com um novo certificado emitido a partir da autoridade de certificação.

## <a name="renew-certificate"></a>Renovar certificado

Para ativar a renovação automática do certificado em qualquer altura, selecione o certificado no [certificados do serviço de aplicações](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.CertificateRegistration%2FcertificateOrders) página, em seguida, clique em **definições de renovação automática** na navegação à esquerda. 

Selecione **nos** e clique em **guardar**. Certificados podem começar a renovar automaticamente 60 dias antes da expiração, se tiver ativada a renovação automática.

![](./media/app-service-web-purchase-ssl-web-site/auto-renew.png)

Para renovar manualmente o certificado em vez disso, clique em **renovação Manual**. Pode pedir para renovar manualmente o certificado 60 dias antes da expiração.

> [!NOTE]
> O certificado renovado não está vinculado automaticamente à sua aplicação, quer renovados manualmente ou ele renovado automaticamente. Associá-lo à sua aplicação, consulte [renovar certificados](./app-service-web-tutorial-custom-ssl.md#renew-certificates). 

## <a name="automate-with-scripts"></a>Automatizar com scripts

### <a name="azure-cli"></a>CLI do Azure

[!code-azurecli[main](../../cli_scripts/app-service/configure-ssl-certificate/configure-ssl-certificate.sh?highlight=3-5 "Bind a custom SSL certificate to a web app")] 

### <a name="powershell"></a>PowerShell

[!code-powershell[main](../../powershell_scripts/app-service/configure-ssl-certificate/configure-ssl-certificate.ps1?highlight=1-3 "Bind a custom SSL certificate to a web app")]

## <a name="more-resources"></a>Mais recursos

* [Impor HTTPS](app-service-web-tutorial-custom-ssl.md#enforce-https)
* [Enforce TLS 1.1/1.2](app-service-web-tutorial-custom-ssl.md#enforce-tls-versions)
* [Utilizar um certificado SSL no código da aplicação no serviço de aplicações do Azure](app-service-web-ssl-cert-load.md)
* [FAQ: Certificados de serviço de aplicações](https://blogs.msdn.microsoft.com/appserviceteam/2017/07/24/faq-app-service-certificates/)
