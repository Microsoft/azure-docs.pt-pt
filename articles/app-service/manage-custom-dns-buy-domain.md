---
title: Comprar o nome de domínio personalizado no Azure - serviço de aplicações
description: Saiba como comprar um nome de domínio personalizado com uma aplicação web no App Service do Azure.
services: app-service\web
documentationcenter: ''
author: cephalin
manager: cfowler
editor: ''
ms.assetid: 70fb0e6e-8727-4cca-ba82-98a4d21586ff
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/24/2017
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: c2bee4d35fe857d5ad3bf26dc1145b8721c85df4
ms.sourcegitcommit: a8948ddcbaaa22bccbb6f187b20720eba7a17edc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/21/2019
ms.locfileid: "56594241"
---
# <a name="buy-a-custom-domain-name-for-azure-app-service"></a>Comprar um nome de domínio personalizado para o serviço de aplicações do Azure

Domínios do serviço de aplicações (pré-visualização) são domínios de nível superior que são geridos diretamente no Azure. Eles facilitam a gestão domínios personalizados para [App Service do Azure](overview.md). Este tutorial mostra-lhe como comprar um domínio do serviço de aplicações e atribuir nomes DNS para o serviço de aplicações do Azure.

Para a VM do Azure ou no armazenamento do Azure, consulte [domínio de serviço de aplicações de atribuir a VM do Azure ou o armazenamento do Azure](https://blogs.msdn.microsoft.com/appserviceteam/2017/07/31/assign-app-service-domain-to-azure-vm-or-azure-storage/). Para serviços em nuvem, consulte [configurar um nome de domínio personalizado para um serviço cloud do Azure](../cloud-services/cloud-services-custom-domain-name-portal.md).

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial:

* [Crie uma aplicação do Serviço de Aplicações](/azure/app-service/) ou utilize uma aplicação que tenha criado para outro tutorial.
* [Remover o limite de gastos na sua subscrição](../billing/billing-spending-limit.md#remove). Não é possível comprar domínios do serviço de aplicações com os créditos de subscrição gratuita.

## <a name="prepare-the-app"></a>Preparar a aplicação

[!INCLUDE [app-service-dev-test-note](../../includes/app-service-dev-test-note.md)]

Para utilizar domínios personalizados do App Service do Azure, a aplicação [plano do App Service](https://azure.microsoft.com/pricing/details/app-service/) tem de ser um escalão pago (**partilhado**, **básica**, **padrão**, ou  **Premium**). Neste passo, certifique-se de que a aplicação é suportado no escalão de preço.

### <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Abra ao [portal do Azure](https://portal.azure.com) e inicie sessão com a sua conta do Azure.

### <a name="navigate-to-the-app-in-the-azure-portal"></a>Navegar para a aplicação no portal do Azure

No menu à esquerda, selecione **Serviços de Aplicações** e selecione o nome da aplicação Web.

![Navegação do portal para a aplicação do Azure](./media/app-service-web-tutorial-custom-domain/select-app.png)

Será apresentada a página de gestão da aplicação do Serviço de Aplicações.  

### <a name="check-the-pricing-tier"></a>Verificar o escalão de preço

No painel de navegação esquerdo da página da aplicação, desloque-se para a secção **Definições** e selecione **Aumentar verticalmente (plano do Serviço de Aplicações)**.

![Menu de aumento vertical](./media/app-service-web-tutorial-custom-domain/scale-up-menu.png)

O escalão atual da aplicação é realçado com um limite azul. Confirme que a aplicação não está no escalão **F1**. O DNS personalizado não é suportado no escalão **F1**. 

![Verificar o escalão de preço](./media/app-service-web-tutorial-custom-domain/check-pricing-tier.png)

Se o plano do serviço de aplicações não está no **F1** tier, feche a **aumentar verticalmente** página e avançar para o [comprar o domínio](#buy-the-domain).

### <a name="scale-up-the-app-service-plan"></a>Aumentar verticalmente o plano do Serviço de Aplicações

Selecione qualquer uma das camadas não gratuitas (**D1**, **B1**, **B2**, **B3** ou qualquer camada na categoria **Produção**). Para obter opções adicionais, clique em **Ver opções adicionais**.

Clique em **Aplicar**.

![Verificar o escalão de preço](./media/app-service-web-tutorial-custom-domain/choose-pricing-tier.png)

Quando vir a notificação seguinte, significa que a operação de dimensionamento está completa.

![Confirmação de operação de dimensionamento](./media/app-service-web-tutorial-custom-domain/scale-notification.png)

## <a name="buy-the-domain"></a>Comprar domínio

### <a name="pricing-information"></a>Informações sobre Preços
Para obter informações sobre domínios do serviço de aplicações do Azure de preços, visite o [página de preços do App Service](https://azure.microsoft.com/pricing/details/app-service/windows/) e desloque-se para baixo até o domínio do serviço de aplicações.

### <a name="sign-in-to-azure"></a>Iniciar sessão no Azure
Abra ao [portal do Azure](https://portal.azure.com/) e inicie sessão com a sua conta do Azure.

### <a name="launch-buy-domains"></a>Iniciar comprar domínios
Na **dos serviços de aplicações** separador, clique no nome da sua aplicação, selecione **definições**e, em seguida, selecione **domínios personalizados**
   
![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-6.png)

Na **domínios personalizados** página, clique em **comprar domínio**.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-1.png)

> [!NOTE]
> Se não pode ver o **domínios do serviço de aplicações** secção, é preciso remover o limite de gastos na sua conta do Azure (consulte [pré-requisitos](#prerequisites)).
>
>

### <a name="configure-the-domain-purchase"></a>Configurar a compra de domínio

No **domínio do serviço de aplicações** página, além do **Procurar domínio** , escreva o nome de domínio que pretende comprar e escreva `Enter`. Os domínios disponíveis sugeridos são apresentados imediatamente por baixo da caixa de texto. Selecione um ou mais domínios que pretende comprar.
   
![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-2.png)

> [!NOTE]
> O seguinte procedimento [domínios de nível superior](https://wikipedia.org/wiki/Top-level_domain) são suportados pelos domínios do serviço de aplicações: _com_, _net_, _co.uk_, _org_, _nl_, _na_, _biz_, _org.pt_, e _co.in_.
>
>

Clique nas **informações de contacto** e preencha o formulário de informações de contacto do domínio. Quando terminar, clique em **OK** para voltar à página de domínio do serviço de aplicações.
   
É importante que preenche necessárias de todos os campos com muito mais precisão possível. Dados incorretos informações de contacto podem resultar numa falha para comprar domínios. 

Em seguida, selecione as opções pretendidas para o seu domínio. Consulte a tabela seguinte para obter explicações:

| Definição | Valor sugerido | Descrição |
|-|-|-|
|Proteção de privacidade | Ativar | Escolher "Proteção de privacidade", que está incluída no preço de compra _gratuitamente_. Alguns domínios de nível superior são gerenciados por entidades de registo que não suportam a proteção de privacidade e estão listadas no **proteção da privacidade** página. |
| Atribuir nomes de anfitrião predefinidos | **www** e **@** | Selecione os enlaces de nome de anfitrião desejado, se assim o desejar. Quando a operação de compra de domínio estiver concluída, a sua aplicação pode ser acessada em nomes de anfitrião selecionado. Se a aplicação está por trás [Gestor de tráfego do Azure](https://azure.microsoft.com/services/traffic-manager/), não vir a opção de atribuir o domínio de raiz (@), porque o Gestor de tráfego faz não suporte registos. Pode efetuar alterações a atribuições de nome de anfitrião depois de concluir a compra de domínio. |

### <a name="accept-terms-and-purchase"></a>Aceitar termos e compra

Clique em **termos legais** para rever os termos e os custos, em seguida, clique em **comprar**.

> [!NOTE]
> Domínios do serviço de aplicações utilizam GoDaddy para o registo de domínio e o DNS do Azure para alojar os domínios. Além da taxa de registo do domínio, aplicam-se custos de utilização de DNS do Azure. Para obter informações, consulte [preços de DNS do Azure](https://azure.microsoft.com/pricing/details/dns/).
>
>

De volta a **domínio do serviço de aplicações** página, clique em **OK**. Enquanto a operação estiver em curso, verá notificações que se seguem:

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-validate.png)

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-purchase-success.png)

### <a name="test-the-hostnames"></a>Testar os nomes de anfitrião

Se tiver atribuído nomes de anfitrião predefinidos à sua aplicação, verá também uma notificação de sucesso para cada nome de anfitrião selecionado. 

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-bind-success.png)

Também pode ver os nomes de anfitriões selecionados no **domínios personalizados** página, além do **nomes de anfitrião personalizado** secção. 

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-hostnames-added.png)

Para testar os nomes de anfitrião, navegue para os nomes de anfitrião listadas no browser. No exemplo da captura de ecrã anterior, tentar navegar até _kontoso.net_ e _www.kontoso.net_.

## <a name="assign-hostnames-to-app"></a>Atribuir nomes de anfitrião de aplicação

Se optar por não atribuir uma ou mais nomes de anfitrião de predefinidos à sua aplicação durante o processo de compra, ou se é necessário atribuir um nome de anfitrião não listada, pode atribuir um nome de anfitrião em qualquer altura.

Também pode atribuir nomes de anfitrião do domínio do serviço de aplicações para qualquer outra aplicação. Os passos dependem se o domínio do serviço de aplicações e a aplicação de pertencer à mesma subscrição.

- Subscrição diferente: Mapa personalizados registos DNS do domínio do serviço de aplicações para a aplicação como um domínio externamente adquirido. Para obter informações sobre como adicionar nomes DNS personalizados a um domínio do serviço de aplicações, consulte [gerir registos DNS personalizados](#custom). Para mapear um domínio adquirido externo para uma aplicação, consulte [mapear um nome DNS existente personalizado para o serviço de aplicações do Azure](app-service-web-tutorial-custom-domain.md). 
- Mesma subscrição: Utilize os seguintes passos.

### <a name="launch-add-hostname"></a>Iniciação Adicionar nome de anfitrião
Na **dos serviços de aplicações** página, selecione o nome da sua aplicação que pretende atribuir os nomes de anfitrião para, selecione **definições**e, em seguida, selecione **domínios personalizados**.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-6.png)

Certifique-se de que o seu domínio adquirido consta no **domínios do serviço de aplicações** secção, mas não o selecione. 

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-select-domain.png)

> [!NOTE]
> Mostra todos os domínios do serviço de aplicações na mesma subscrição da aplicação **domínios personalizados** página. Se o seu domínio for na subscrição da aplicação, mas não é possível vê-lo na aplicação do **domínios personalizados** página, tente voltar a abrir o **domínios personalizados** página ou Atualize a página Web. Além disso, verifique no sino de notificações na parte superior do portal do Azure para falhas de criação ou de progresso.
>
>

Selecione **Adicionar nome de anfitrião**.

### <a name="configure-hostname"></a>Configurar o nome de anfitrião
Na **Adicionar nome de anfitrião** caixa de diálogo, escreva o nome de domínio completamente qualificado do seu domínio do serviço de aplicações ou qualquer subdomínio. Por exemplo:

- kontoso.net
- www.kontoso.net
- abc.kontoso.net

Quando terminar, selecione **Validate**. O tipo de registo de nome de anfitrião é selecionado automaticamente para.

Selecione **Adicionar nome de anfitrião**.

Quando a operação estiver concluída, verá uma notificação de êxito para o nome de anfitrião atribuído.  

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-bind-success.png)

### <a name="close-add-hostname"></a>Feche a adicionar nome de anfitrião
Na **Adicionar nome de anfitrião** página, atribuir outro nome de anfitrião para a sua aplicação, conforme desejado. Quando terminar, feche o **Adicionar nome de anfitrião** página.

Agora, deverá ver o hostname(s) atribuída recentemente na sua aplicação **domínios personalizados** página.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-hostnames-added2.png)

### <a name="test-the-hostnames"></a>Testar os nomes de anfitrião

Navegue para os nomes de anfitrião listadas no browser. No exemplo da captura de ecrã anterior, tentar navegar até _abc.kontoso.net_.

## <a name="renew-the-domain"></a>Renovar o domínio

O domínio de serviço de aplicações que comprou é válido durante um ano a partir do momento da compra. Por predefinição, o domínio está configurado para renovar automaticamente através de seu método de pagamento para o próximo ano. Se pretender desativar a renovação automática, ou se pretende renovar manualmente o seu domínio, siga os passos aqui descritos.

Na **dos serviços de aplicações** separador, clique no nome da sua aplicação, selecione **definições**e, em seguida, selecione **domínios personalizados**.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-6.png)

Na **domínios do serviço de aplicações** secção, selecione o domínio que pretende configurar.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-select-domain.png)

No painel de navegação à esquerda do domínio, selecione **renovação de domínio**. Para parar a renovar automaticamente o seu domínio, selecione **Off**e, em seguida **guardar**. 

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-autorenew.png)

Para renovar manualmente o seu domínio, selecione **renovar domínio**. No entanto, este botão não está ativo até 90 dias antes da expiração do domínio.

<a name="custom"></a>

## <a name="manage-custom-dns-records"></a>Gerir registos DNS personalizados

No Azure, os registos DNS para um domínio do serviço de aplicações são geridos através do [DNS do Azure](https://azure.microsoft.com/services/dns/). Pode adicionar, remover e atualizar os registos DNS, tal como para um domínio externamente adquirido.

### <a name="open-app-service-domain"></a>Domínio de abrir o serviço de aplicações

No portal do Azure, no menu à esquerda, selecione **todos os serviços** > **domínios do serviço de aplicações**.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-access.png)

Selecione o domínio para gerir. 

### <a name="access-dns-zone"></a>Zona DNS de acesso

No menu do lado esquerdo do domínio, selecione **zona DNS**.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-dns-zone.png)

Esta ação abre o [zona DNS](../dns/dns-zones-records.md) página do seu domínio do serviço de aplicações no DNS do Azure. Para obter informações sobre como editar registos DNS, consulte [como gerir zonas DNS no portal do Azure](../dns/dns-operations-dnszones-portal.md).

## <a name="cancel-purchase-delete-domain"></a>Cancelar Compra (eliminação de domínio)

Depois de comprar o domínio de serviço do aplicativo, tem cinco dias para cancelar a compra de um reembolso total. Depois de cinco dias, pode eliminar o domínio do serviço de aplicações, mas não consegue receber um reembolso.

### <a name="open-app-service-domain"></a>Domínio de abrir o serviço de aplicações

No portal do Azure, no menu à esquerda, selecione **todos os serviços** > **domínios do serviço de aplicações**.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-access.png)

Selecione o domínio a pretende cancelar ou eliminar. 

### <a name="delete-hostname-bindings"></a>Eliminar os enlaces de nome de anfitrião

No menu do lado esquerdo do domínio, selecione **enlaces de nome de anfitrião**. Os enlaces de nome de anfitrião de todos os serviços do Azure estão listados aqui.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-hostname-bindings.png)

Não é possível eliminar o domínio de serviço do aplicativo até que todos os enlaces de nome de anfitrião são eliminados.

Eliminar cada enlace de nome de anfitrião ao selecionar **...**   >  **Eliminar**. Depois de todas as ligações são eliminadas, selecione **guardar**.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-delete-hostname-bindings.png)

### <a name="cancel-or-delete"></a>Cancelar ou delete

No menu do lado esquerdo do domínio, selecione **descrição geral**. 

Se não tiver decorrido o período de cancelamento no domínio adquirido, selecione **Cancelar Compra**. Caso contrário, verá uma **eliminar** botão em vez disso. Para eliminar o domínio sem um reembolso, selecione **eliminar**.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-cancel.png)

Para confirmar a operação, selecione **Sim**.

Depois da operação estiver concluída, o domínio é lançada da sua subscrição e está disponível para qualquer pessoa que adquirir novamente. 

## <a name="direct-default-url-to-a-custom-directory"></a>Direcionar o URL predefinido para um diretório personalizado

Por predefinição, o Serviço de Aplicações direciona os pedidos Web para o diretório de raiz do código da sua aplicação. Para direcioná-los para um subdiretório, tal como `public`, consulte [direcionar o URL predefinido para um diretório personalizado](app-service-web-tutorial-custom-domain.md#virtualdir).

## <a name="more-resources"></a>Mais recursos

[FAQ: Domínio do serviço de aplicações (pré-visualização) e domínios personalizados](https://blogs.msdn.microsoft.com/appserviceteam/2017/08/08/faq-app-service-domain-preview-and-custom-domains/)
