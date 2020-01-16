---
title: Comprar um nome de domínio personalizado
description: Saiba como comprar um domínio do serviço de aplicativo e usá-lo como um domínio personalizado para o serviço de Azure App de aplicativo.
ms.assetid: 70fb0e6e-8727-4cca-ba82-98a4d21586ff
ms.topic: article
ms.date: 11/24/2017
ms.custom: seodec18
ms.openlocfilehash: 6bc6f1ca29343e027730cbbd99d210f911fcb492
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/15/2020
ms.locfileid: "75967305"
---
# <a name="buy-a-custom-domain-name-for-azure-app-service"></a>Comprar um nome de domínio personalizado para o serviço de Azure App

Os domínios do serviço de aplicativo são domínios de nível superior que são gerenciados diretamente no Azure. Eles facilitam o gerenciamento de domínios personalizados para [Azure app serviço](overview.md). Este tutorial mostra como comprar um domínio do serviço de aplicativo e atribuir nomes DNS ao serviço Azure App.

Para a VM do Azure ou o armazenamento do Azure, consulte [atribuir domínio do serviço de aplicativo à VM do Azure ou armazenamento do Azure](https://blogs.msdn.microsoft.com/appserviceteam/2017/07/31/assign-app-service-domain-to-azure-vm-or-azure-storage/). Para serviços de nuvem, consulte [Configurando um nome de domínio personalizado para um serviço de nuvem do Azure](../cloud-services/cloud-services-custom-domain-name-portal.md).

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial:

* [Crie uma aplicação do Serviço de Aplicações](/azure/app-service/) ou utilize uma aplicação que tenha criado para outro tutorial.
* [Remova o limite de gastos em sua assinatura](../cost-management-billing/manage/spending-limit.md#remove). Não é possível comprar domínios do serviço de aplicativo com créditos de assinatura gratuitos.

## <a name="prepare-the-app"></a>Preparar a aplicação

[!INCLUDE [app-service-dev-test-note](../../includes/app-service-dev-test-note.md)]

Para usar domínios personalizados no serviço Azure App, o plano do [serviço de aplicativo](https://azure.microsoft.com/pricing/details/app-service/) do aplicativo deve ser uma camada paga (**compartilhada**, **básica**, **Standard**ou **Premium**). Nesta etapa, você verifica se o aplicativo está no tipo de preço com suporte.

### <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Abra ao [portal do Azure](https://portal.azure.com) e inicie sessão com a sua conta do Azure.

### <a name="navigate-to-the-app-in-the-azure-portal"></a>Navegar para a aplicação no portal do Azure

No menu à esquerda, selecione **Serviços de Aplicações** e selecione o nome da aplicação Web.

![Navegação do portal para a aplicação do Azure](./media/app-service-web-tutorial-custom-domain/select-app.png)

Será apresentada a página de gestão da aplicação do Serviço de Aplicações.  

### <a name="check-the-pricing-tier"></a>Verificar o escalão de preço

No painel de navegação esquerdo da página da aplicação, desloque-se para a secção **Definições** e selecione **Aumentar verticalmente (plano do Serviço de Aplicações)** .

![Menu de aumento vertical](./media/app-service-web-tutorial-custom-domain/scale-up-menu.png)

O escalão atual da aplicação é realçado com um limite azul. Confirme que a aplicação não está no escalão **F1**. O DNS personalizado não é suportado no escalão **F1**. 

![Verificar o escalão de preço](./media/app-service-web-tutorial-custom-domain/check-pricing-tier.png)

Se o plano do serviço de aplicativo não estiver na camada **F1** , feche a página **escalar verticalmente** e pule para [comprar o domínio](#buy-the-domain).

### <a name="scale-up-the-app-service-plan"></a>Aumentar verticalmente o plano do Serviço de Aplicações

Selecione qualquer uma das camadas não gratuitas (**D1**, **B1**, **B2**, **B3** ou qualquer camada na categoria **Produção**). Para obter opções adicionais, clique em **Ver opções adicionais**.

Clique em **Aplicar**.

![Verificar o escalão de preço](./media/app-service-web-tutorial-custom-domain/choose-pricing-tier.png)

Quando vir a notificação seguinte, significa que a operação de dimensionamento está completa.

![Confirmação de operação de dimensionamento](./media/app-service-web-tutorial-custom-domain/scale-notification.png)

## <a name="buy-the-domain"></a>Comprar o domínio

### <a name="pricing-information"></a>Informações sobre Preços
Para obter informações sobre preços em domínios de serviço Azure App, visite a [página de preços do serviço de aplicativo](https://azure.microsoft.com/pricing/details/app-service/windows/) e role para baixo até domínio do serviço de aplicativo.

### <a name="sign-in-to-azure"></a>Iniciar sessão no Azure
Abra ao [portal do Azure](https://portal.azure.com/) e inicie sessão com a sua conta do Azure.

### <a name="launch-buy-domains"></a>Iniciar a compra de domínios
Na guia **serviços de aplicativos** , clique no nome do seu aplicativo, selecione **configurações**e, em seguida, selecione **domínios personalizados**
   
![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-6.png)

Na página **domínios personalizados** , clique em **comprar domínio**.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-1.png)

> [!NOTE]
> Se não for possível ver a seção **domínios do serviço de aplicativo** , você precisará remover o limite de gastos em sua conta do Azure (consulte [pré-requisitos](#prerequisites)).
>
>

### <a name="configure-the-domain-purchase"></a>Configurar a compra de domínio

Na página **domínio do serviço de aplicativo** , na caixa **Pesquisar domínio** , digite o nome de domínio que você deseja comprar e digite `Enter`. Os domínios disponíveis sugeridos são mostrados logo abaixo da caixa de texto. Selecione um ou mais domínios que você deseja comprar.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-2.png)

> [!NOTE]
> Os domínios de [nível superior](https://wikipedia.org/wiki/Top-level_domain) a seguir têm suporte dos domínios do serviço de aplicativo: _com_, _net_, _co.uk_, _org_, _nl_, _in_, _biz_, _org.uk_e _co.in_.
>
>

Clique nas **informações de contato** e preencha o formulário de informações de contato do domínio. Quando terminar, clique em **OK** para retornar à página de domínio do serviço de aplicativo.

É importante que você preencha todos os campos obrigatórios com o máximo de precisão possível. Dados incorretos para informações de contato podem resultar em falha na compra de domínios.

Em seguida, selecione as opções desejadas para seu domínio. Consulte a tabela a seguir para obter explicações:

| Definição | Valor sugerido | Descrição |
|-|-|-|
|Proteção de privacidade | Ativar | Aceite a "proteção de privacidade", que está incluída no preço de compra _gratuitamente_. Alguns domínios de nível superior são gerenciados por registradores que não dão suporte à proteção de privacidade e estão listados na página **proteção de privacidade** . |
| Atribuir nomes de host padrão | **www** e **\@** | Selecione as associações de nome de host desejadas, se desejar. Quando a operação de compra de domínio for concluída, seu aplicativo poderá ser acessado nos nomes de host selecionados. Se o aplicativo estiver por trás do [Gerenciador de tráfego do Azure](https://azure.microsoft.com/services/traffic-manager/), você não verá a opção de atribuir o domínio raiz (@), pois o Gerenciador de tráfego não oferece suporte A registros a. Você pode fazer alterações nas atribuições de nome de host após a conclusão da compra de domínio. |

### <a name="accept-terms-and-purchase"></a>Aceitar os termos e comprar

Clique em **termos legais** para examinar os termos e os encargos e, em seguida, clique em **comprar**.

> [!NOTE]
> Os domínios do serviço de aplicativo usam GoDaddy para o registro de domínio e o DNS do Azure para hospedar os domínios. Além da taxa de registro de domínio, os encargos de uso para o DNS do Azure se aplicam. Para obter informações, consulte [preços de DNS do Azure](https://azure.microsoft.com/pricing/details/dns/).
>
>

De volta à página **domínio do serviço de aplicativo** , clique em **OK**. Enquanto a operação estiver em andamento, você verá as seguintes notificações:

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-validate.png)

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-purchase-success.png)

### <a name="test-the-hostnames"></a>Testar os nomes de host

Se você tiver atribuído nomes de host padrão ao seu aplicativo, você também verá uma notificação de êxito para cada nome de host selecionado.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-bind-success.png)

Você também verá os nomes de host selecionados na página **domínios personalizados** , na seção **nomes de host personalizados** .

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-hostnames-added.png)

> [!NOTE]
> Um rótulo **não seguro** para seu domínio personalizado significa que ele ainda não está associado a um certificado SSL, e qualquer solicitação HTTPS de um navegador para seu domínio personalizado receberá um erro ou aviso, dependendo do navegador. Para configurar a associação SSL, consulte [proteger um nome DNS personalizado com uma associação SSL no serviço Azure app](configure-ssl-bindings.md).
>

Para testar os nomes de host, navegue até os nomes de host listados no navegador. No exemplo na captura de tela anterior, tente navegar até _kontoso.net_ e _www\.kontoso.net_.

## <a name="assign-hostnames-to-app"></a>Atribuir nomes de host ao aplicativo

Se você optar por não atribuir um ou mais nomes de host padrão ao seu aplicativo durante o processo de compra, ou se precisar atribuir um nome de host não listado, você poderá atribuir um nome de host a qualquer momento.

Você também pode atribuir nomes de host no domínio do serviço de aplicativo a qualquer outro aplicativo. As etapas dependem se o domínio do serviço de aplicativo e o aplicativo pertencem à mesma assinatura.

- Assinatura diferente: mapeie registros DNS personalizados do domínio do serviço de aplicativo para o aplicativo, como um domínio comprado externamente. Para obter informações sobre como adicionar nomes DNS personalizados a um domínio do serviço de aplicativo, consulte [gerenciar registros DNS personalizados](#custom). Para mapear um domínio comprado externo para um aplicativo, consulte [mapear um nome DNS personalizado existente para o serviço de Azure app](app-service-web-tutorial-custom-domain.md). 
- Mesma assinatura: Use as etapas a seguir.

### <a name="launch-add-hostname"></a>Iniciar adicionar nome do host
Na página **serviços de aplicativos** , selecione o nome do aplicativo ao qual você deseja atribuir nomes de host, selecione **configurações**e, em seguida, selecione **domínios personalizados**.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-6.png)

Verifique se o domínio comprado está listado na seção **domínios do serviço de aplicativo** , mas não o Selecione. 

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-select-domain.png)

> [!NOTE]
> Todos os domínios do serviço de aplicativo na mesma assinatura são mostrados na página **domínios personalizados** do aplicativo. Se o seu domínio estiver na assinatura do aplicativo, mas você não puder vê-lo na página **domínios personalizados** do aplicativo, tente reabrir a página **domínios personalizados** ou atualizar a página da Web. Além disso, verifique o Bell de notificação na parte superior da portal do Azure em busca de progresso ou falhas de criação.
>
>

Selecione **Adicionar nome de anfitrião**.

### <a name="configure-hostname"></a>Configurar nome do host
Na caixa de diálogo Adicionar nome de **host** , digite o nome de domínio totalmente qualificado do seu domínio do serviço de aplicativo ou de qualquer subdomínio. Por exemplo:

- kontoso.net
- \.da Web kontoso.net
- abc.kontoso.net

Quando terminar, selecione **validar**. O tipo de registro hostname é selecionado automaticamente para você.

Selecione **Adicionar nome de anfitrião**.

Quando a operação for concluída, você verá uma notificação de êxito para o nome de host atribuído.  

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-bind-success.png)

### <a name="close-add-hostname"></a>Fechar adicionar nome do host
Na página **adicionar nome de host** , atribua qualquer outro nome de host ao seu aplicativo, conforme desejado. Quando terminar, feche a página **adicionar nome do host** .

Agora você deve ver os nomes de host recentemente atribuídos na página **domínios personalizados** do seu aplicativo.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-hostnames-added2.png)

### <a name="test-the-hostnames"></a>Testar os nomes de host

Navegue até os nomes de host listados no navegador. No exemplo na captura de tela anterior, tente navegar até _ABC.kontoso.net_.

## <a name="renew-the-domain"></a>Renovar o domínio

O domínio do serviço de aplicativo que você comprou é válido por um ano a partir do momento da compra. Por padrão, o domínio é configurado para ser renovado automaticamente, cobrando seu método de pagamento para o próximo ano. Você pode renovar manualmente seu nome de domínio.

Se você quiser desativar a renovação automática ou se quiser renovar manualmente seu domínio, siga as etapas aqui.

Na guia **serviços de aplicativos** , clique no nome do seu aplicativo, selecione **configurações**e, em seguida, selecione **domínios personalizados**.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-6.png)

Na seção **domínios do serviço de aplicativo** , selecione o domínio que você deseja configurar.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-select-domain.png)

No painel de navegação à esquerda do domínio, selecione **renovação de domínio**. Para parar de renovar seu domínio automaticamente, selecione **desativado**e, em seguida, **salvar**.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-autorenew.png)

Para renovar seu domínio manualmente, selecione **renovar domínio**. No entanto, esse botão não estará ativo até [90 dias antes da expiração do domínio](#when-domain-expires).

Se a renovação do seu domínio for bem-sucedida, você receberá uma notificação por email dentro de 24 horas.

## <a name="when-domain-expires"></a>Quando o domínio expirar

O Azure lida com domínios do serviço de aplicativo expirados ou expirados da seguinte maneira:

* Se a renovação automática estiver desabilitada: 90 dias antes da expiração do domínio, um email de notificação de renovação será enviado a você e o botão **renovar domínio** será ativado no Portal.
* Se a renovação automática estiver habilitada: no dia após a data de expiração do domínio, o Azure tentará cobrar pela renovação do nome de domínio.
* Se ocorrer um erro durante a renovação automática (por exemplo, se o cartão no arquivo estiver expirado) ou se a renovação automática estiver desabilitada e você permitir que o domínio expire, o Azure o notificará sobre a expiração do domínio e os parques do seu nome de domínio. Você pode [renovar manualmente](#renew-the-domain) seu domínio.
* No dia 4 e 12 dias após a expiração, o Azure envia emails de notificação adicionais. Você pode [renovar manualmente](#renew-the-domain) seu domínio.
* No dia 19 após a expiração, seu domínio permanece em espera, mas se torna sujeito a uma taxa de resgate. Você pode chamar o atendimento ao cliente para renovar seu nome de domínio, sujeito a qualquer taxa de renovação e resgate aplicável.
* No 25º dia após a expiração, o Azure coloca seu domínio para o leilão com um serviço de serviços de leilões do setor de nome de domínio. Você pode chamar o atendimento ao cliente para renovar seu nome de domínio, sujeito a qualquer taxa de renovação e resgate aplicável.
* No dia 30 após a expiração, você não poderá mais resgatar seu domínio.

<a name="custom"></a>

## <a name="manage-custom-dns-records"></a>Gerenciar registros DNS personalizados

No Azure, os registros DNS para um domínio do serviço de aplicativo são gerenciados usando o [DNS do Azure](https://azure.microsoft.com/services/dns/). Você pode adicionar, remover e atualizar registros DNS, assim como para um domínio comprado externamente.

### <a name="open-app-service-domain"></a>Abrir domínio do serviço de aplicativo

No portal do Azure, no menu à esquerda, selecione **todos os serviços** > **domínios do serviço de aplicativo**.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-access.png)

Selecione o domínio a ser gerenciado. 

### <a name="access-dns-zone"></a>Acessar zona DNS

No menu à esquerda do domínio, selecione **zona DNS**.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-dns-zone.png)

Essa ação abre a página [zona DNS](../dns/dns-zones-records.md) do seu domínio do serviço de aplicativo no DNS do Azure. Para obter informações sobre como editar registros DNS, consulte [como gerenciar zonas DNS no portal do Azure](../dns/dns-operations-dnszones-portal.md).

## <a name="cancel-purchase-delete-domain"></a>Cancelar compra (excluir domínio)

Depois de comprar o domínio do serviço de aplicativo, você tem cinco dias para cancelar sua compra por um reembolso total. Após cinco dias, você pode excluir o domínio do serviço de aplicativo, mas não pode receber um reembolso.

### <a name="open-app-service-domain"></a>Abrir domínio do serviço de aplicativo

No portal do Azure, no menu à esquerda, selecione **todos os serviços** > **domínios do serviço de aplicativo**.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-access.png)

Selecione o domínio a ser cancelado ou excluído. 

### <a name="delete-hostname-bindings"></a>Excluir associações de nome de host

No menu à esquerda do domínio, selecione **associações de nome de host**. As associações de nome de host de todos os serviços do Azure são listadas aqui.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-hostname-bindings.png)

Você não pode excluir o domínio do serviço de aplicativo até que todas as associações de nome de host sejam excluídas.

Exclua cada associação de nome de host selecionando **...**  > **excluir**. Depois que todas as associações forem excluídas, selecione **salvar**.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-delete-hostname-bindings.png)

### <a name="cancel-or-delete"></a>Cancelar ou excluir

No menu à esquerda do domínio, selecione **visão geral**. 

Se o período de cancelamento no domínio comprado não tiver decorrido, selecione **Cancelar compra**. Caso contrário, você verá um botão **excluir** em vez disso. Para excluir o domínio sem reembolso, selecione **excluir**.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-cancel.png)

Para confirmar a operação, selecione **Sim**.

Depois que a operação for concluída, o domínio será liberado da sua assinatura e estará disponível para qualquer pessoa comprar novamente. 

## <a name="direct-default-url-to-a-custom-directory"></a>Direcionar o URL predefinido para um diretório personalizado

Por predefinição, o Serviço de Aplicações direciona os pedidos Web para o diretório de raiz do código da sua aplicação. Para direcioná-los para um subdiretório, como `public`, consulte [URL padrão direto para um diretório personalizado](app-service-web-tutorial-custom-domain.md#virtualdir).
