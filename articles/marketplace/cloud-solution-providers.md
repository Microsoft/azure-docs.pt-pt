---
title: Cloud Solution Provider - Mercado comercial da Microsoft
description: Saiba como vender as suas ofertas através do canal parceiro do programa Microsoft Cloud Solution Provider (CSP) no mercado comercial.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/16/2020
ms.author: dsindona
ms.openlocfilehash: 58bfe5cdc58c41c2ead82ac2d280629e4fec9bcd
ms.sourcegitcommit: cf7caaf1e42f1420e1491e3616cc989d504f0902
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/22/2020
ms.locfileid: "83798137"
---
# <a name="cloud-solution-provider-program"></a>Programa cloud solution provider

Este artigo explica como configurar a sua oferta para estar disponível para o programa Cloud Solution Provider (CSP). Além de publicar as suas ofertas através de [web storefronts de marketplace comercial,](https://docs.microsoft.com/azure/marketplace/comparing-appsource-azure-marketplace)também pode vender através do programa CSP para chegar a milhões de clientes qualificados da Microsoft que o programa serve.

Pode configurar ofertas novas ou existentes para disponibilidade no programa CSP numa base de opt-in, o que permite aos parceiros cSP vender os seus produtos e criar soluções agregadas para os clientes.

Os editores são responsáveis por fornecer suporte de break-fix aos clientes finais e por fornecer um mecanismo para os parceiros no programa CSP e/ou clientes para contactá-lo para obter suporte. É uma boa prática fornecer aos parceiros no programa CSP a documentação do utilizador, a formação e notificações de saúde/interrupção de serviço (conforme aplicável) para que os parceiros do programa CSP estejam equipados para lidar com pedidos de suporte de nível 1 dos clientes.  

As seguintes ofertas são elegíveis para serem optadas para serem vendidas por parceiros no programa CSP:

- Ofertas transatas de software-as-a-Service (SaaS)
- Máquinas Virtuais (VMs)
- Modelos de solução
- Aplicações geridas

> [!NOTE]
> Os contentores e trazer a sua própria licença (BYOL) VM SKUs são optados por serem vendidos por parceiros no programa CSP por padrão.

## <a name="how-to-configure-an-offering"></a>Como configurar uma oferta

A definição de opt-in do programa CSP está configurada no Partner Center ou cloud Partner Portal oferecem experiência de criação. [Saiba mais sobre a experiência da editora em mudança.](https://www.microsoftpartnercommunity.com/t5/Azure-Marketplace-and-AppSource/Cloud-Marketplace-In-Partner-Center/m-p/9738#M293)

### <a name="partner-center-opt-in"></a>Opt-in do Partner Center

No Partner Center, você encontrará a experiência de opt-in sob o módulo de público Reseller CSP.

![Público revendedor da CSP](media/marketplace-publishers-guide/csp-reseller-audience.png)

No módulo de audiência do Revendedor CSP, tem três opções para escolher:

- Opção um: Qualquer parceiro no programa CSP
- Opção dois: Parceiros específicos no programa CSP que seleciono
- Opção três: Sem parceiros no programa CSP

#### <a name="option-one-any-partner-in-the-csp-program"></a>Opção um: Qualquer parceiro no programa CSP

![Qualquer parceiro no programa CSP](media/marketplace-publishers-guide/csp-reseller-option-one.png)

 Ao escolher esta opção, todos os parceiros do programa CSP são elegíveis para revender a sua oferta aos seus clientes.

#### <a name="option-two-specific-partners-in-the-csp-program-i-select"></a>Opção dois: Parceiros específicos no programa CSP que seleciono

![Parceiros específicos no programa CSP que seleciono](media/marketplace-publishers-guide/csp-reseller-option-two.png)

Ao escolher esta opção, autoriza quais os parceiros do programa CSP elegíveis para revender a sua oferta.

Para autorizar parceiros, clique em **Select CSP Partners** e aparece um menu que lhe permite pesquisar pelo nome do parceiro ou CSP Azure Ative Directory (AAD) ID de inquilino.

![Selecione menu CSP](media/marketplace-publishers-guide/csp-pop-up-module.png)

Pode aplicar filtros de pesquisa, tais como **País,** **Competência,** ou **Habilidade**.

![País/região, competência e filtros de habilidades para pesquisa de parceiros](media/marketplace-publishers-guide/csp-add-resellers.png)

Depois de ter escolhido a lista de parceiros, selecione **Adicionar**.

![Lista de exemplo de parceiros autorizados no programa CSP](media/marketplace-publishers-guide/csp-add-resellers-details.png)

Uma tabela que mostra a lista de parceiros selecionados aparece na página de audiência do Revendedor CSP.

![Tabela com lista de parceiros na página de audiência do Revendedor CSP](media/marketplace-publishers-guide/csp-option-two-add-reseller.png)

Selecione **Guardar rascunho** para registar as suas alterações.

Se esta oferta não for publicada, terá de publicar a sua oferta para a disponibilizar aos seus parceiros selecionados.

>[!NOTE]
>Se autorizar um parceiro no programa CSP numa determinada região, eles podem vender a oferta a qualquer cliente que pertença a essa região em particular. Consulte os [mercados regionais do programa Cloud Solution Provider e](https://docs.microsoft.com/partner-center/regional-authorization-overview) a moeda para obter mais informações sobre como as ofertas de CSP são classificadas em regiões.

Se estiver a atualizar a lista de CSP de uma oferta já publicada, adicione os parceiros adicionais e selecione **o público Sync CSP**.

Se tiver uma oferta que já tenha uma lista de parceiros autorizados e deseja utilizar a mesma lista para outra oferta, utilize **import/exportação.** Navegue para a oferta que tem a lista de CSP e selecione **CSPs de exportação**. A função desenvolve um ficheiro .csv que pode ser importado para outra oferta.

#### <a name="option-three-no-partners-in-the-csp-program"></a>Opção três: Sem parceiros no programa CSP

![Sem parceiros no programa CSP](media/marketplace-publishers-guide/csp-reseller-option-three.png)

Ao escolher esta opção, está a optar pela sua oferta fora do programa CSP. Pode alterar esta seleção a qualquer momento.

### <a name="cloud-partner-portal-opt-in"></a>Cloud Partner Portal opt-in

No Portal do Parceiro cloud, o opt-in está definido no separador Marketplace ou Storefront. A capacidade de escolher parceiros específicos no programa CSP só está disponível no Partner Center.

![Experiência de opt-in do CSP em CPP](media/marketplace-publishers-guide/csp-opt-in.png)

## <a name="deauthorize-partners-in-the-csp-program"></a>Desautorizar Parceiros no programa CSP

Se autorizou um parceiro no programa CSP e esse parceiro já revendeu o produto aos seus clientes, não poderá desautorizar esse parceiro.

Se um parceiro no programa CSP não tiver vendido o seu produto aos seus clientes e quiser remover o CSP após a publicação da sua oferta, utilize as seguintes instruções:

1. Vá à página de pedido de [apoio.](https://aka.ms/marketplacepublishersupport) Os primeiros menus suspensos são automaticamente preenchidos para si.

   > [!NOTE]
   > Não mude as seleções de menus pré-povoados.

2. Para **selecionar a versão do produto,** selecione gestão de oferta ao **vivo.**
3. Para **selecionar uma categoria que melhor descreva o problema,** escolha a categoria que se refere à sua oferta.
4. Para **selecionar um problema que melhor descreva o problema,** selecione A oferta existente de **Atualização**.
5. Selecione **Next** para ser direcionado para a **página de detalhes** do Problema para inserir mais detalhes sobre o seu problema.
6. Utilize o **Desautorize o CSP** como título de emissão e preencha as restantes secções necessárias.




## <a name="navigate-between-options"></a>Navegar entre opções

Utilize esta secção para navegar entre as três opções de revendedor CSP.

### <a name="navigate-from-option-one-any-partner-in-the-csp-program"></a>Navegar a partir da Opção um: Qualquer parceiro no programa CSP

Se a sua oferta for atualmente **a Opção 1: Qualquer parceiro no programa CSP** e deseja navegar para qualquer uma das outras duas opções, use as seguintes instruções para criar um pedido:

1. Vá à página de pedido de [apoio.](https://aka.ms/marketplacepublishersupport) Os primeiros menus suspensos são automaticamente preenchidos para si.

   > [!NOTE]
   > Não mude as seleções de menus pré-povoados.

2. Para **selecionar a versão do produto,** selecione gestão de oferta ao **vivo.**
3. Para **selecionar uma categoria que melhor descreva o problema,** escolha a categoria que se refere à sua oferta.
4. Para **selecionar um problema que melhor descreva o problema,** selecione A oferta existente de **Atualização**.
5. Selecione **Next** para ser direcionado para a **página de detalhes** do Problema para inserir mais detalhes sobre o seu problema.
6. Utilize o **Desautorize o CSP** como título de emissão e preencha as restantes secções necessárias.

> [!NOTE]
> Se está a tentar navegar para a Opção dois e um parceiro no programa CSP já revendeu a oferta aos seus clientes, esse parceiro já está por defeito na sua lista de parceiros autorizados.  

### <a name="navigate-from-option-two-specific-partners-in-the-csp-program-i-select"></a>Navegue a partir da Opção dois: Parceiros específicos no programa CSP que seleciono

Se a sua oferta for atualmente **a Opção 2: Parceiros específicos no programa CSP seleciono** e gostaria de navegar para a **Opção um: Qualquer parceiro no programa CSP,** use as seguintes instruções para criar um pedido:

1. Vá à página de pedido de [apoio.](https://aka.ms/marketplacepublishersupport) Os primeiros menus suspensos são automaticamente preenchidos para si.

   > [!NOTE]
   > Não mude as seleções de menus pré-povoados.

2. Para **selecionar a versão do produto,** selecione gestão de oferta ao **vivo.**
3. Para **selecionar uma categoria que melhor descreva o problema,** escolha a categoria que se refere à sua oferta.
4. Para **selecionar um problema que melhor descreva o problema,** selecione A oferta existente de **Atualização**.
5. Selecione **Next** para ser direcionado para a **página de detalhes** do Problema para inserir mais detalhes sobre o seu problema.
6. Utilize o **Desautorize o CSP** como título de emissão e preencha as restantes secções necessárias.

 Se a sua oferta for atualmente **a Opção 2: Parceiros específicos no programa CSP que selecionei** e gostaria de navegar para a **Opção 3: Sem parceiros no programa CSP,** só poderá navegar para essa opção se os parceiros do programa CSP que previamente autorizou não tiverem revendido a sua oferta aos clientes finais. Por favor, utilize as seguintes instruções para criar um pedido:

1. Vá à página de pedido de [apoio.](https://aka.ms/marketplacepublishersupport) Os primeiros menus suspensos são automaticamente preenchidos para si.

   > [!NOTE]
   > Não mude as seleções de menus pré-povoados.

2. Para **selecionar a versão do produto,** selecione gestão de oferta ao **vivo.**
3. Para **selecionar uma categoria que melhor descreva o problema,** escolha a categoria que se refere à sua oferta.
4. Para **selecionar um problema que melhor descreva o problema,** selecione A oferta existente de **Atualização**.
5. Selecione **Next** para ser direcionado para a **página de detalhes** do Problema para inserir mais detalhes sobre o seu problema.
6. Utilize o **Desautorize o CSP** como título de emissão e preencha as restantes secções necessárias.

### <a name="navigate-from-option-3-no-partners-in-the-csp-program"></a>Navegar a partir da Opção 3: Sem parceiros no programa CSP

Se a sua oferta for atualmente **a Opção 3: Sem parceiros no programa CSP,** pode navegar para qualquer uma das outras duas opções a qualquer momento.

## <a name="sharing-sales-and-support-materials-with-partners-in-the-csp-program"></a>Partilhar vendas e materiais de apoio com parceiros no programa CSP

Para ajudar os parceiros no programa Cloud Solution Provider a representar mais eficazmente a sua oferta e envolver-se com a sua organização, deve submeter materiais de vendas e suporte que estarão disponíveis para os revendedores. Estes recursos não serão expostos aos clientes nas montras do mercado.

### <a name="partner-center-csp-channel"></a>Canal CSP partner Center

Se optou pelo canal CSP no Partner Center, os editores devem introduzir um URL que aconselhe materiais de marketing relevantes e informações de contacto de canais para o canal CSP sob o módulo de listagem de ofertas:

![Informação colateral do Centro parceiro CSP](media/marketplace-publishers-guide/pc-csp-channel.png)

### <a name="cloud-partner-portal-csp-channel"></a>Canal CSP do Portal do Parceiro de Nuvem

Se optou pelo canal CSP no Cloud Partner Portal, os editores devem introduzir um URL que aconselhe materiais de marketing relevantes e informações de contacto de canais para o canal CSP:

![Informação colateral do Portal csp do Portal cloud](media/marketplace-publishers-guide/cpp-csp-information.png)

## <a name="next-steps"></a>Passos seguintes

Visite o Azure Marketplace e o [AppSource Publisher Guide](https://docs.microsoft.com/azure/marketplace/marketplace-publishers-guide).

Para saber mais sobre os serviços GTM do marketplace, consulte [os serviços de Go-to-market.](https://partner.microsoft.com/reach-customers/gtm)

Inscreva-se no [Partner Center](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership) para criar e configurar a sua oferta.
