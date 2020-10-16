---
title: Cloud Solution Provider - Mercado comercial da Microsoft
description: Saiba como vender as suas ofertas através do canal parceiro de programas microsoft Cloud Solution Provider (CSP) no mercado comercial.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: keferna
ms.author: keferna
ms.date: 07/14/2020
ms.openlocfilehash: bdd2dacbba4ef8b7c439a345ea70771354b7fb5a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91708599"
---
# <a name="cloud-solution-provider-program"></a>Programa fornecedor de solução de nuvem

Este artigo explica como configurar a sua oferta para estar disponível para o programa Cloud Solution Provider (CSP). Além de publicar as suas ofertas através de [lojas online de marketplace comercial,](overview.md#commercial-marketplace-online-stores)também pode vender através do programa CSP para chegar a milhões de clientes qualificados da Microsoft que o programa serve.

Pode configurar ofertas novas ou existentes para disponibilidade no programa CSP numa base de opt-in, o que permite aos parceiros da CSP vender os seus produtos e criar soluções agregadas para os clientes.

Os editores são responsáveis por fornecer suporte de break-fix aos clientes finais e por fornecer um mecanismo para que os parceiros do programa CSP e/ou clientes o contactem para apoio. É uma boa prática fornecer aos parceiros do programa CSP documentação, formação e notificações de saúde/interrupção de serviços (conforme aplicável) para que os parceiros do programa CSP estejam equipados para lidar com pedidos de suporte de nível 1 dos clientes.  

As seguintes ofertas são elegíveis para serem vendidas por parceiros no programa CSP:

- Ofertas de transações de software-as-a-service (SaaS)
- Máquinas Virtuais (VMs)
- Modelos de solução
- Aplicações geridas

> [!NOTE]
> Os planos de VM de Contentores e Bring Your Own (BYOL) são optados por serem vendidos por parceiros no programa CSP por padrão.

## <a name="how-to-configure-an-offer"></a>Como configurar uma oferta

Configure a definição de opt-in do programa CSP quando criar a oferta no Partner Center. [Saiba mais sobre a experiência da editora em mudança.](https://www.microsoftpartnercommunity.com/t5/Azure-Marketplace-and-AppSource/Cloud-Marketplace-In-Partner-Center/m-p/9738#M293)

### <a name="partner-center-opt-in"></a>Opt-in do Centro parceiro

A experiência de opt-in está localizada sob o módulo de audiência do Revendedor CSP:

![Público revendedor CSP](media/marketplace-publishers-guide/csp-reseller-audience.png)

Escolha entre três opções:

1. Qualquer parceiro no programa CSP.
2. Parceiros específicos no programa CSP que seleciono.
3. Sem parceiros no programa CSP.

#### <a name="option-1-any-partner-in-the-csp-program"></a>Opção 1: Qualquer parceiro no programa CSP

![Qualquer parceiro no programa CSP](media/marketplace-publishers-guide/csp-reseller-option-one.png)

 Ao escolher esta opção, todos os parceiros do programa CSP são elegíveis para revender a sua oferta aos seus clientes.

#### <a name="option-2-specific-partners-in-the-csp-program-i-select"></a>Opção 2: Parceiros específicos no programa CSP que seleciono

![Parceiros específicos no programa CSP eu seleciono](media/marketplace-publishers-guide/csp-reseller-option-two.png)

Ao escolher esta opção, você autoriza quais os parceiros no programa CSP elegíveis para revender a sua oferta.

Para autorizar parceiros, **selecione Parceiros CSP** e aparece um menu que lhe permite pesquisar pelo nome de parceiro ou ID do inquilino CSP Azure Ative(Azure AD).

![Selecione menu CSP](media/marketplace-publishers-guide/csp-pop-up-module.png)

Pode aplicar filtros de pesquisa, tais como **Country,** **Compete,** ou **Skill**.

![Filtros de país/região, competências e competências para pesquisa de parceiros](media/marketplace-publishers-guide/csp-add-resellers.png)

Depois de ter escolhido a lista de parceiros, **selecione Add**.

![Exemplo lista de parceiros autorizados no programa CSP](media/marketplace-publishers-guide/csp-add-resellers-details.png)

Uma tabela que mostra a lista de parceiros selecionados aparece na página de audiência do Revendedor CSP.

![Tabela com lista de parceiros na página de audiência do Revendedor CSP](media/marketplace-publishers-guide/csp-option-two-add-reseller.png)

**Selecione Guardar o projeto** para registar as suas alterações.

Se esta oferta não for publicada, terá de publicar a sua oferta para a disponibilizar aos seus parceiros selecionados.

>[!NOTE]
>Se autorizar um parceiro no programa CSP numa determinada região, pode vender a oferta a qualquer cliente que pertença a essa região em particular. Para obter mais informações sobre como as ofertas da CSP são classificadas em regiões, consulte [o programa Cloud Solution Provider mercados regionais e moeda.](https://docs.microsoft.com/partner-center/regional-authorization-overview)

Se estiver a atualizar a lista de CSP de uma oferta já publicada, adicione os parceiros adicionais e selecione **o público do Sync CSP**.

Se tiver uma oferta que já tenha uma lista de parceiros autorizados e gostaria de usar a mesma lista para outra oferta, use **Import/Export**. Navegue para a oferta que tem a lista de CSP e selecione **Export CSPs**. A função desenvolve um ficheiro .csv que pode ser importado para outra oferta.

#### <a name="option-3-no-partners-in-the-csp-program"></a>Opção 3: Sem parceiros no programa CSP

![Sem parceiros no programa CSP](media/marketplace-publishers-guide/csp-reseller-option-three.png)

Ao escolher esta opção, está a optar pela sua oferta fora do programa CSP. Pode alterar esta seleção a qualquer momento.

## <a name="deauthorize-partners-in-the-csp-program"></a>Desautorizar parceiros no programa CSP

Se autorizou um parceiro no programa CSP e esse parceiro já revendeu o produto aos seus clientes, não poderá desautorizar esse parceiro.

Se um parceiro no programa CSP não tiver vendido o seu produto aos seus clientes e quiser remover o CSP após a publicação da sua oferta, utilize as seguintes instruções:

1. Aceda à [página de pedido de apoio.](https://aka.ms/marketplacepublishersupport) Os primeiros menus suspensos são automaticamente preenchidos para si.

   > [!NOTE]
   > Não altere as seleções de menus pré-povoados.

2. Para **Selecionar a versão do produto,** selecione **Live offer management**.
3. Para **Selecionar uma categoria que melhor descreva o problema,** escolha a categoria que se refere à sua oferta.
4. Para **Selecionar um problema que melhor descreve o problema**, selecione **Atualização da oferta existente**.
5. Selecione **Next** para ser direcionado para a **página de detalhes do Assunto** para introduzir mais detalhes sobre o seu problema.
6. Utilize **a Deautorize CSP** como título de emissão e preencha as restantes secções necessárias.

## <a name="navigate-between-options"></a>Navegar entre opções

Utilize esta secção para navegar entre as três opções de revendedor CSP.

### <a name="navigate-from-option-one-any-partner-in-the-csp-program"></a>Navegue a partir da opção um: Qualquer parceiro no programa CSP

Se a sua oferta for atualmente **opção 1: Qualquer parceiro no programa CSP** e pretender navegar para qualquer uma das outras duas opções, use as seguintes instruções para criar um pedido:

1. Aceda à [página de pedido de apoio.](https://aka.ms/marketplacepublishersupport) Os primeiros menus suspensos são automaticamente preenchidos para si.

   > [!NOTE]
   > Não altere as seleções de menus pré-povoados.

2. Para **Selecionar a versão do produto,** selecione **Live offer management**.
3. Para **Selecionar uma categoria que melhor descreva o problema,** escolha a categoria que se refere à sua oferta.
4. Para **Selecionar um problema que melhor descreve o problema**, selecione **Atualização da oferta existente**.
5. Selecione **Next** para ser direcionado para a **página de detalhes do Assunto** para introduzir mais detalhes sobre o seu problema.
6. Utilize **a Deautorize CSP** como título de emissão e preencha as restantes secções necessárias.

> [!NOTE]
> Se está a tentar navegar para a Opção 2 e um parceiro no programa CSP já revendeu a oferta aos seus clientes, esse parceiro já está, por defeito, na sua lista de parceiros autorizados.  

### <a name="navigate-from-option-two-specific-partners-in-the-csp-program-i-select"></a>Navegar a partir da opção dois: Parceiros específicos no programa CSP eu seleciono

Se a sua oferta for atualmente **Opção 2: Parceiros específicos no programa CSP que seleciono** e gostaria de navegar para a **Opção um: Qualquer parceiro no programa CSP,** use as seguintes instruções para criar um pedido:

1. Aceda à [página de pedido de apoio.](https://aka.ms/marketplacepublishersupport) Os primeiros menus suspensos são automaticamente preenchidos para si.

   > [!NOTE]
   > Não altere as seleções de menus pré-povoados.

2. Para **Selecionar a versão do produto,** selecione **Live offer management**.
3. Para **Selecionar uma categoria que melhor descreva o problema,** escolha a categoria que se refere à sua oferta.
4. Para **Selecionar um problema que melhor descreve o problema**, selecione **Atualização da oferta existente**.
5. Selecione **Next** para ser direcionado para a **página de detalhes do Assunto** para introduzir mais detalhes sobre o seu problema.
6. Utilize **a Deautorize CSP** como título de emissão e preencha as restantes secções necessárias.

 Se a sua oferta for atualmente **Opção 2: Parceiros específicos no programa CSP que seleciono** e gostaria de navegar para a **Opção 3: Sem parceiros no programa CSP,** só poderá navegar para essa opção se os parceiros do programa CSP que tinha previamente autorizado não terem revendido a sua oferta para clientes finais. Utilize as seguintes instruções para criar um pedido:

1. Aceda à [página de pedido de apoio.](https://aka.ms/marketplacepublishersupport) Os primeiros menus suspensos são automaticamente preenchidos para si.

   > [!NOTE]
   > Não altere as seleções de menus pré-povoados.

2. Para **Selecionar a versão do produto,** selecione **Live offer management**.
3. Para **Selecionar uma categoria que melhor descreva o problema,** escolha a categoria que se refere à sua oferta.
4. Para **Selecionar um problema que melhor descreve o problema**, selecione **Atualização da oferta existente**.
5. Selecione **Next** para ser direcionado para a **página de detalhes do Assunto** para introduzir mais detalhes sobre o seu problema.
6. Utilize **a Deautorize CSP** como título de emissão e preencha as restantes secções necessárias.

### <a name="navigate-from-option-3-no-partners-in-the-csp-program"></a>Navegar a partir da Opção 3: Sem parceiros no programa CSP

Se a sua oferta for atualmente **Opção 3: Sem parceiros no programa CSP,** pode navegar para qualquer uma das outras duas opções a qualquer momento.

## <a name="sharing-sales-and-support-materials-with-partners-in-the-csp-program"></a>Partilha de vendas e materiais de apoio com parceiros no programa CSP

Para ajudar os parceiros no programa Cloud Solution Provider a representar mais eficazmente a sua oferta e a envolver-se com a sua organização, tem de submeter materiais de vendas e suporte que estarão disponíveis para os revendedores. Estes recursos não serão expostos aos clientes nas lojas online.

### <a name="partner-center-csp-channel"></a>Canal CSP do Centro Parceiro

Se optou pelo canal CSP no Partner Center, os editores devem introduzir um URL que acolhe materiais de marketing relevantes e informações de contacto de canais no âmbito do módulo de listagem de ofertas.

![Informações colaterais do Centro Parceiro CSP](media/marketplace-publishers-guide/pc-csp-channel.png)

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre [os serviços Go-to-market.](https://partner.microsoft.com/reach-customers/gtm)
- Inscreva-se no [Partner Center](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership) para criar e configurar a sua oferta.
