---
title: Comprar e configurar um certificado SSL do Azure-serviço de aplicativo | Microsoft Docs
description: Saiba como comprar um certificado do serviço de aplicativo e associá-lo ao seu aplicativo do serviço de aplicativo
services: app-service
documentationcenter: .net
author: cephalin
manager: jpconnoc
tags: buy-ssl-certificates
ms.assetid: cdb9719a-c8eb-47e5-817f-e15eaea1f5f8
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/16/2018
ms.author: cephalin
ms.reviewer: apurvajo
ms.custom: seodec18
ms.openlocfilehash: 7675a22b4b2d8b13524f06f45d6bb805c1e2fad1
ms.sourcegitcommit: b12a25fc93559820cd9c925f9d0766d6a8963703
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/14/2019
ms.locfileid: "69019138"
---
# <a name="buy-and-configure-an-ssl-certificate-for-azure-app-service"></a>Comprar e configurar um certificado SSL para o serviço de Azure App

Este tutorial mostra como proteger o aplicativo do [serviço de aplicativo](https://docs.microsoft.com/azure/app-service/) ou o [aplicativo de funções](https://docs.microsoft.com/azure/azure-functions/) criando (comprando) um certificado do serviço de aplicativo no [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-whatis) e, em seguida, associá-lo a um aplicativo do serviço de aplicativo.

> [!TIP]
> Os certificados do serviço de aplicativo podem ser usados para serviços do Azure ou não Azure e não se limitam aos serviços de aplicativos. Para fazer isso, você precisa criar uma cópia local do PFX de um certificado do serviço de aplicativo que você possa usá-lo em qualquer lugar desejado. Para obter mais informações, consulte [criando uma cópia local do pfx de um certificado do serviço de aplicativo](https://blogs.msdn.microsoft.com/benjaminperkins/2017/04/12/export-an-azure-app-service-certificate-pfx-powershell/).
>

## <a name="prerequisites"></a>Pré-requisitos

Para seguir este guia de instruções:

- [Criar uma aplicação do Serviço de Aplicações](/azure/app-service/)
- [Mapear um nome de domínio para seu aplicativo](app-service-web-tutorial-custom-domain.md) ou [comprá-lo e configurá-lo no Azure](manage-custom-dns-buy-domain.md)

[!INCLUDE [Prepare your web app](../../includes/app-service-ssl-prepare-app.md)]

## <a name="start-certificate-order"></a>Iniciar a ordem do certificado

Inicie uma ordem de certificado do serviço de aplicativo na <a href="https://portal.azure.com/#create/Microsoft.SSL" target="_blank">página certificado do serviço de aplicativo criar</a>.

![Criação de certificado](./media/app-service-web-purchase-ssl-web-site/createssl.png)

Use a tabela a seguir para ajudá-lo a configurar o certificado. Quando terminar, clique em **Criar**.

| Definição | Descrição |
|-|-|
| Nome | Um nome amigável para seu certificado de serviço de aplicativo. |
| Nome do Anfitrião do Domínio sem "www" | Especifique o domínio raiz aqui. O certificado emitido protege o domínio raiz e o `www` subdomínio. No certificado emitido, o campo nome comum contém o domínio raiz e o campo nome alternativo da entidade contém o `www` domínio. Para proteger somente qualquer subdomínio, especifique o nome de domínio totalmente qualificado do subdomínio aqui (por exemplo, `mysubdomain.contoso.com`).|
| Subscription | O centro de dados onde o a aplicação Web está alojada. |
| Resource group | O grupo de recursos que contém o certificado. Você pode usar um novo grupo de recursos ou selecionar o mesmo grupo de recursos que o aplicativo do serviço de aplicativo, por exemplo. |
| SKU do Certificado | Determina o tipo de certificado a ser criado, se um certificado padrão ou um [certificado curinga](https://wikipedia.org/wiki/Wildcard_certificate). |
| Termos Legais | Clique para confirmar que você concorda com os termos legais. Os certificados são obtidos de GoDaddy. |

## <a name="store-in-azure-key-vault"></a>Armazenar no Azure Key Vault

Após a conclusão do processo de compra do certificado, há mais algumas etapas que você precisa concluir para poder começar a usar esse certificado. 

Selecione o certificado na página [certificados do serviço de aplicativo](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.CertificateRegistration%2FcertificateOrders) e clique em **configuração** > **do certificado etapa 1: Armazenar**.

![Inserir imagem de pronto para armazenar no KV](./media/app-service-web-purchase-ssl-web-site/ReadyKV.png)

[Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-whatis) é um serviço do Azure que ajuda a proteger chaves criptográficas e segredos usados por aplicativos e serviços em nuvem. É o armazenamento de sua escolha para certificados do serviço de aplicativo.

Na página **status da Key Vault** , clique em **repositório Key Vault** para criar um novo cofre ou escolha um cofre existente. Se você optar por criar um novo cofre, use a tabela a seguir para ajudá-lo a configurar o cofre e clique em criar. consulte para criar novos Key Vault dentro da mesma assinatura e grupo de recursos.

| Definição | Descrição |
|-|-|
| Nome | Um nome exclusivo que consiste em caracteres alfanuméricos e traços. |
| Resource group | Como recomendação, selecione o mesmo grupo de recursos que o seu certificado de serviço de aplicativo. |
| Location | Selecione o mesmo local que o aplicativo do serviço de aplicativo. |
| Escalão de preço | Para obter informações, consulte [Azure Key Vault detalhes de preços](https://azure.microsoft.com/pricing/details/key-vault/). |
| Políticas de acesso| Define os aplicativos e o acesso permitido aos recursos do cofre. Você pode configurá-lo mais tarde, seguindo as etapas em [conceder a vários aplicativos acesso a um cofre de chaves](../key-vault/key-vault-group-permissions-for-apps.md). |
| Acesso de Rede Virtual | Restringir o acesso ao cofre para determinadas redes virtuais do Azure. Você pode configurá-lo mais tarde, seguindo as etapas em [configurar Azure Key Vault firewalls e redes virtuais](../key-vault/key-vault-network-security.md) |

Depois de selecionar o cofre, feche a página do **repositório Key Vault** . A opção de **repositório** deve mostrar uma marca de seleção verde para êxito. Mantenha a página aberta para a próxima etapa.

## <a name="verify-domain-ownership"></a>Verificar a propriedade do domínio

Na mesma página de **configuração de certificado** usada na última etapa, clique em **etapa 2: Verifique**.

![](./media/app-service-web-purchase-ssl-web-site/verify-domain.png)

Selecione **verificação do serviço de aplicativo**. Como você já mapeou o domínio para seu aplicativo Web (consulte [pré-requisitos](#prerequisites)), ele já está verificado. Basta clicar em **verificar** para concluir esta etapa. Clique no botão **Atualizar** até que o **certificado de mensagem seja verificado pelo domínio** .

> [!NOTE]
> Há suporte para quatro tipos de métodos de verificação de domínio: 
> 
> - **Serviço de aplicativo** -a opção mais conveniente quando o domínio já está mapeado para um aplicativo do serviço de aplicativo na mesma assinatura. Ele aproveita o fato de que o aplicativo do serviço de aplicativo já verificou a propriedade do domínio.
> - **Domínio** -verifique um [domínio do serviço de aplicativo que você comprou do Azure](manage-custom-dns-buy-domain.md). O Azure adiciona automaticamente o registro TXT de verificação para você e conclui o processo.
> - **Email** -Verifique o domínio enviando um email para o administrador de domínio. As instruções são fornecidas quando você seleciona a opção.
> - **Manual** -Verifique o domínio usando uma página HTML (somente certificado**padrão** ) ou um registro txt do DNS. As instruções são fornecidas quando você seleciona a opção.

## <a name="bind-certificate-to-app"></a>Associar certificado ao aplicativo

No **[portal do Azure](https://portal.azure.com/)** , no menu à esquerda, selecione **Serviços** >  **\<de aplicativos your_ aplicativo >** .

No painel de navegação à esquerda do seu aplicativo, selecione **configurações** > SSL**certificados privados (. pfx)**  > **importar certificado do serviço de aplicativo**.

![Inserir imagem do certificado de importação](./media/app-service-web-purchase-ssl-web-site/ImportCertificate.png)

Selecione o certificado que você acabou de comprar.

Agora que o certificado foi importado, você precisa associá-lo a um nome de domínio mapeado em seu aplicativo. Selecione **associações** > **Adicionar Associação SSL**. 

![Inserir imagem do certificado de importação](./media/app-service-web-purchase-ssl-web-site/AddBinding.png)

Use a tabela a seguir para ajudá-lo a configurar a associação na caixa de diálogo **associações SSL** e, em seguida, clique em **Adicionar Associação**.

| Definição | Descrição |
|-|-|
| Nome do Anfitrião | O nome de domínio para o qual adicionar a associação SSL. |
| Thumbprint do Certificado Privado | O certificado a ser associado. |
| Tipo de SSL | <ul><li>**SSL SNI** -várias associações SSL baseadas em SNI podem ser adicionadas. Esta opção permite utilizar vários certificados SSL para proteger múltiplos domínios no mesmo endereço IP. Os browsers mais modernos (incluindo o Internet Explorer, o Chrome, o Firefox e o Opera) suportam SNI (encontre informações mais abrangentes sobre o suporte de browsers em [Server Name Indication](https://wikipedia.org/wiki/Server_Name_Indication) [Indicação do Nome de Servidor]).</li><li>**SSL baseado em IP** - só pode ser adicionado um enlace SSL baseado em IP. Esta opção permite utilizar apenas um certificado SSL para proteger um endereço IP público dedicado. Depois de configurar a associação, siga as etapas em [remapear um registro para IP SSL](app-service-web-tutorial-custom-ssl.md#remap-a-record-for-ip-ssl). </li></ul> |

## <a name="verify-https-access"></a>Verificar o acesso HTTPS

Visite seu aplicativo usando `HTTPS://<domain_name>` em vez `HTTP://<domain_name>` de para verificar se o certificado foi configurado corretamente.

## <a name="rekey-certificate"></a>Recodificar certificado

Se você considerar que a chave privada do certificado está comprometida, poderá refazer o seu certificado. Selecione o certificado na página [certificados do serviço de aplicativo](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.CertificateRegistration%2FcertificateOrders) e, em seguida, selecione rechaveamento **e sincronização** no painel de navegação esquerdo.

Clique em rechaveamento para iniciar o processo. Esse processo pode levar de 1-10 a minutos para ser concluído.

![Inserir imagem do SSL de rechaveamento](./media/app-service-web-purchase-ssl-web-site/Rekey.png)

A recriação de chaves do certificado reverte o certificado com um novo certificado emitido pela autoridade de certificação.

Quando a operação de rechaveamento for concluída, clique em **sincronizar**. A operação de sincronização atualiza automaticamente as associações de nome de host para o certificado no serviço de aplicativo sem causar nenhum tempo de inatividade para seus aplicativos.

> [!NOTE]
> Se você não clicar em **sincronizar**, o serviço de aplicativo sincronizará automaticamente seu certificado dentro de 48 horas.

## <a name="renew-certificate"></a>Renovar certificado

Para ativar a renovação automática de seu certificado a qualquer momento, selecione o certificado na página [certificados do serviço de aplicativo](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.CertificateRegistration%2FcertificateOrders) e clique em **configurações de renovação automática** no painel de navegação esquerdo.

Selecione **ativado** e clique em **salvar**. Os certificados podem começar a renovar automaticamente 60 dias antes da expiração se você tiver a renovação automática ativada.

![renovar certificado automaticamente](./media/app-service-web-purchase-ssl-web-site/auto-renew.png)

Para renovar manualmente o certificado, clique em **renovação manual**. Você pode solicitar para renovar manualmente o certificado 60 dias antes da expiração.

Quando a operação de renovação for concluída, clique em **sincronizar**. A operação de sincronização atualiza automaticamente as associações de nome de host para o certificado no serviço de aplicativo sem causar nenhum tempo de inatividade para seus aplicativos.

> [!NOTE]
> Se você não clicar em **sincronizar**, o serviço de aplicativo sincronizará automaticamente seu certificado dentro de 48 horas.

## <a name="automate-with-scripts"></a>Automatizar com scripts

### <a name="azure-cli"></a>CLI do Azure

[!code-azurecli[main](../../cli_scripts/app-service/configure-ssl-certificate/configure-ssl-certificate.sh?highlight=3-5 "Bind a custom SSL certificate to a web app")] 

### <a name="powershell"></a>PowerShell

[!code-powershell[main](../../powershell_scripts/app-service/configure-ssl-certificate/configure-ssl-certificate.ps1?highlight=1-3 "Bind a custom SSL certificate to a web app")]

## <a name="more-resources"></a>Mais recursos

* [Impor HTTPS](app-service-web-tutorial-custom-ssl.md#enforce-https)
* [Impor o TLS 1.1/1.2](app-service-web-tutorial-custom-ssl.md#enforce-tls-versions)
* [Usar um certificado SSL no código do aplicativo no serviço Azure App](app-service-web-ssl-cert-load.md)
* [PERGUNTAS FREQÜENTES: Certificados do serviço de aplicativo](https://docs.microsoft.com/azure/app-service/faq-configuration-and-management/)
