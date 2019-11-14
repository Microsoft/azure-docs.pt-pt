---
title: Provedores de soluções de nuvem | Azure Marketplace
description: Os editores agora podem vender suas ofertas por meio do canal de parceiros do CSP (provedor de solução Microsoft Cloud).
services: Azure, Marketplace, Compute, Storage, Networking, Partner Center
author: ChJenk
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 11/12/2019
ms.author: v-chjen
ms.openlocfilehash: 546702af671cfe9506a4fc0448f40b7b8353960c
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74038709"
---
# <a name="cloud-solution-providers"></a>Fornecedores de Soluções Cloud

As ofertas de software podem alcançar milhões de clientes qualificados da Microsoft atendidos por parceiros no programa CSP (provedor de soluções na nuvem), além da disponibilidade pública de ofertas por meio de [vitrines da Web da Microsoft](https://docs.microsoft.com/azure/marketplace/comparing-appsource-azure-marketplace).

Os editores configuram ofertas de disponibilidade no programa CSP de acordo com a aceitação, para uma nova oferta ou uma existente, permitindo que os parceiros vendam seus produtos e criem soluções agrupadas para os clientes.

Os editores são responsáveis por fornecer suporte à correção de consertos para clientes finais e para fornecer um mecanismo para parceiros no programa CSP e/ou clientes para entrar em contato com você para obter suporte. É uma prática recomendada fornecer aos parceiros do programa CSP a documentação do usuário, o treinamento e as notificações de integridade/interrupção do serviço (conforme aplicável) para que os parceiros no programa CSP estejam equipados para lidar com as solicitações de suporte da camada 1 dos clientes.  

As ofertas a seguir estão qualificadas para serem aceitas para serem vendidas por parceiros no programa CSP:

- Ofertas de software como serviço (SaaS) Transact
- Máquinas virtuais (VMs)
- Modelos de solução
- Aplicativos gerenciados

> [!NOTE]
> Os SKUs de VM de contêineres e BYOL (traga sua própria licença) são aceitos para serem vendidos por parceiros no programa CSP por padrão.

## <a name="how-to-configure-an-offering"></a>Como configurar uma oferta

A configuração de aceitação do programa CSP está configurada no Partner Center ou na experiência de criação da oferta de Portal do Cloud Partner. [Saiba mais sobre a experiência de alteração do editor](https://www.microsoftpartnercommunity.com/t5/Azure-Marketplace-and-AppSource/Cloud-Marketplace-In-Partner-Center/m-p/9738#M293).

### <a name="partner-center-opt-in"></a>Aceitação do Partner Center

No Partner Center, você encontrará a experiência de aceitação no módulo público do revendedor CSP.

![Público do revendedor CSP](media/marketplace-publishers-guide/csp-reseller-audience.png)

No módulo público de revendedor CSP, você tem três opções para escolher:

- Opção um: qualquer parceiro no programa CSP
- Opção dois: parceiros específicos no programa CSP que seleciono
- Opção três: nenhum parceiro no programa CSP

#### <a name="option-one-any-partner-in-the-csp-program"></a>Opção um: qualquer parceiro no programa CSP

![Qualquer parceiro no programa CSP](media/marketplace-publishers-guide/csp-reseller-option-one.png)

 Ao escolher essa opção, todos os parceiros no programa CSP estão qualificados para revender sua oferta para seus clientes.

#### <a name="option-two-specific-partners-in-the-csp-program-i-select"></a>Opção dois: parceiros específicos no programa CSP que seleciono

![Parceiros específicos no programa CSP que seleciono](media/marketplace-publishers-guide/csp-reseller-option-two.png)

Ao escolher essa opção, você autoriza quais parceiros no programa CSP estão qualificados para revender sua oferta.

Para autorizar parceiros, clique em **selecionar parceiros CSP** e um menu é exibido para que você pesquise por nome do parceiro ou ID do locatário do AAD (Azure Active Directory do CSP).

![Selecione o menu CSP](media/marketplace-publishers-guide/csp-pop-up-module.png)

Você pode aplicar filtros de pesquisa, como **país**, **competência**ou **habilidade**.

![Os filtros de país, competência e habilidades para a pesquisa de parceiros](media/marketplace-publishers-guide/csp-add-resellers.png)

Depois de escolher a lista de parceiros, selecione **Adicionar**.

![Exemplo de lista de parceiros autorizados no programa CSP](media/marketplace-publishers-guide/csp-add-resellers-details.png)

Uma tabela que mostra a lista de parceiros que você selecionou aparece na página público do revendedor CSP.

![Tabela com lista de parceiros na página de público do revendedor CSP](media/marketplace-publishers-guide/csp-option-two-add-reseller.png)

Selecione **salvar rascunho** para registrar suas alterações.

Se esta oferta não for publicada, você precisará publicar sua oferta para disponibilizá-la para os parceiros selecionados.

>[!NOTE]
>Se você autorizar um parceiro no programa CSP em uma determinada região, ele poderá vender a oferta para qualquer cliente que pertença a essa região específica. Consulte o [programa provedor de soluções de nuvem mercados regionais e moeda](https://docs.microsoft.com/partner-center/regional-authorization-overview) para obter mais informações sobre como as ofertas do CSP são classificadas em regiões.

Se você estiver atualizando a lista de CSP de uma oferta já publicada, adicione os parceiros adicionais e selecione o **público-alvo do CSP de sincronização**.

Se você tiver uma oferta que já tenha uma lista de parceiros autorizados e quiser usar a mesma lista para outra oferta, use a **importação/exportação**. Navegue até a oferta que tem a lista CSP e selecione **Exportar CSPs**. A função desenvolve um arquivo. csv que pode ser importado para outra oferta.

#### <a name="option-three-no-partners-in-the-csp-program"></a>Opção três: nenhum parceiro no programa CSP

![Nenhum parceiro no programa CSP](media/marketplace-publishers-guide/csp-reseller-option-three.png)

Ao escolher essa opção, você está optando por sua oferta do programa CSP. Você pode alterar essa seleção a qualquer momento.

### <a name="cloud-partner-portal-opt-in"></a>Portal do Cloud Partner aceitar

No Portal do Cloud Partner, a opção aceitar é definida na guia Marketplace ou vitrine. A capacidade de escolher parceiros específicos no programa CSP só está disponível no Partner Center.

![Experiência de aceitação do CSP em CPP](media/marketplace-publishers-guide/csp-opt-in.png)

## <a name="deauthorize-partners-in-the-csp-program"></a>Desautorizar parceiros no programa CSP

Se você tiver autorizado um parceiro no programa CSP e esse parceiro já tiver revendido o produto a seus clientes, você não terá permissão para desautorizar esse parceiro.

Se um parceiro no programa CSP não tiver vendido seu produto aos clientes e você quiser remover o CSP após a sua oferta ter sido publicada, use as seguintes instruções:

1. Vá para a [página de solicitação de suporte](https://aka.ms/marketplacepublishersupport). Os primeiros menus suspensos são preenchidos automaticamente para você.

   > [!NOTE]
   > Não altere as seleções de menu suspenso preenchidas previamente.

2. Para **selecionar a versão do produto**, selecione **Gerenciamento de ofertas dinâmicas**.
3. Para **selecionar uma categoria que melhor descreva o problema**, escolha a categoria que se refere à sua oferta.
4. Para **selecionar um problema que melhor descreva o problema**, selecione **Atualizar oferta existente**.
5. Selecione **Avançar** para ser direcionado à **página detalhes do problema** para inserir mais detalhes sobre o problema.
6. Use o **CSP de desautorização** como o título do problema e preencha o restante das seções necessárias.




## <a name="navigate-between-options"></a>Navegar entre opções

Use esta seção para navegar entre as três opções de revendedor do CSP.

### <a name="navigate-from-option-one-any-partner-in-the-csp-program"></a>Navegar da opção um: qualquer parceiro no programa CSP

Se sua oferta for a **opção 1: qualquer parceiro no programa CSP** e você quiser navegar para uma das outras duas opções, use as instruções a seguir para criar uma solicitação:

1. Vá para a [página de solicitação de suporte](https://aka.ms/marketplacepublishersupport). Os primeiros menus suspensos são preenchidos automaticamente para você.

   > [!NOTE]
   > Não altere as seleções de menu suspenso preenchidas previamente.

2. Para **selecionar a versão do produto**, selecione **Gerenciamento de ofertas dinâmicas**.
3. Para **selecionar uma categoria que melhor descreva o problema**, escolha a categoria que se refere à sua oferta.
4. Para **selecionar um problema que melhor descreva o problema**, selecione **Atualizar oferta existente**.
5. Selecione **Avançar** para ser direcionado à **página detalhes do problema** para inserir mais detalhes sobre o problema.
6. Use o **CSP de desautorização** como o título do problema e preencha o restante das seções necessárias.

> [!NOTE]
> Se você estiver tentando navegar para a opção dois e um parceiro no programa CSP já revendeu a oferta a seus clientes, esse parceiro já estará, por padrão, em sua lista de parceiros autorizados.  

### <a name="navigate-from-option-two-specific-partners-in-the-csp-program-i-select"></a>Navegar da opção dois: parceiros específicos no programa CSP que seleciono

Se sua oferta for atualmente a **opção 2: parceiros específicos no programa CSP que eu seleciono** e você gostaria de navegar para a **opção um: qualquer parceiro no programa CSP**, use as instruções a seguir para criar uma solicitação:

1. Vá para a [página de solicitação de suporte](https://aka.ms/marketplacepublishersupport). Os primeiros menus suspensos são preenchidos automaticamente para você.

   > [!NOTE]
   > Não altere as seleções de menu suspenso preenchidas previamente.

2. Para **selecionar a versão do produto**, selecione **Gerenciamento de ofertas dinâmicas**.
3. Para **selecionar uma categoria que melhor descreva o problema**, escolha a categoria que se refere à sua oferta.
4. Para **selecionar um problema que melhor descreva o problema**, selecione **Atualizar oferta existente**.
5. Selecione **Avançar** para ser direcionado à **página detalhes do problema** para inserir mais detalhes sobre o problema.
6. Use o **CSP de desautorização** como o título do problema e preencha o restante das seções necessárias.

 Se sua oferta for a **opção 2: parceiros específicos no programa CSP que eu seleciono** e você gostaria de navegar para a **opção 3: nenhum parceiro no programa CSP**, você só poderá navegar para essa opção se os parceiros no programa CSP que você autorizou anteriormente não revenderem sua oferta aos clientes finais. Use as instruções a seguir para criar uma solicitação:

1. Vá para a [página de solicitação de suporte](https://aka.ms/marketplacepublishersupport). Os primeiros menus suspensos são preenchidos automaticamente para você.

   > [!NOTE]
   > Não altere as seleções de menu suspenso preenchidas previamente.

2. Para **selecionar a versão do produto**, selecione **Gerenciamento de ofertas dinâmicas**.
3. Para **selecionar uma categoria que melhor descreva o problema**, escolha a categoria que se refere à sua oferta.
4. Para **selecionar um problema que melhor descreva o problema**, selecione **Atualizar oferta existente**.
5. Selecione **Avançar** para ser direcionado à **página detalhes do problema** para inserir mais detalhes sobre o problema.
6. Use o **CSP de desautorização** como o título do problema e preencha o restante das seções necessárias.

### <a name="navigate-from-option-3-no-partners-in-the-csp-program"></a>Navegue da opção 3: nenhum parceiro no programa CSP

Se sua oferta for atualmente a **opção 3: nenhum parceiro no programa CSP**, você poderá navegar para qualquer uma das duas outras opções a qualquer momento.

## <a name="sharing-sales-and-support-materials-with-partners-in-the-csp-program"></a>Compartilhando materiais de vendas e suporte com parceiros no programa CSP

Para ajudar os parceiros no programa do provedor de soluções na nuvem a representar efetivamente sua oferta e se envolver com sua organização, você deve enviar materiais de vendas e de suporte que estarão disponíveis para os revendedores. Esses recursos não serão expostos aos clientes nas vitrines do Marketplace.

### <a name="partner-center-csp-channel"></a>Canal CSP do Partner Center

Se você tiver optado pelo canal CSP no Partner Center, os editores deverão inserir uma URL que hospede materiais de marketing relevantes e informações de contato do canal para o canal CSP no módulo de listagem de oferta:

![Informações adicionais sobre o CSP do Partner Center](media/marketplace-publishers-guide/pc-csp-channel.png)

### <a name="cloud-partner-portal-csp-channel"></a>Canal CSP Portal do Cloud Partner

Se você tiver optado no canal do CSP em Portal do Cloud Partner, os editores deverão inserir uma URL que hospede materiais de marketing relevantes e informações de contato do canal para o canal CSP:

![Informações adicionais sobre o CSP de Portal do Cloud Partner](media/marketplace-publishers-guide/cpp-csp-information.png)

## <a name="next-steps"></a>Passos seguintes

Visite o [Guia do Azure Marketplace e do AppSource Publisher](https://docs.microsoft.com/azure/marketplace/marketplace-publishers-guide).

Para saber mais sobre os serviços do Marketplace GTM, consulte [serviços de entrada no mercado](https://partner.microsoft.com/reach-customers/gtm).

Entre no [Partner Center](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/azureisv) ou [portal do Cloud Partner](https://cloudpartner.azure.com/) para criar e configurar sua oferta.
