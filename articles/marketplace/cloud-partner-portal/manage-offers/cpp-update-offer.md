---
title: Atualizar ofertas do Marketplace | Azure Marketplace
description: Atualizar ofertas no Azure e AppSource Marketplaces usando o Portal do Cloud Partner
services: Azure, AppSource, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 01/11/2019
ms.author: pabutler
ms.openlocfilehash: b3f579dbdc943b2380c9de3dde6b2ebf4754d4d1
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73826718"
---
# <a name="update-azure-marketplace-and-appsource-offers"></a>Atualizar ofertas do Azure Marketplace e do AppSource

Há vários tipos de atualizações que você pode aplicar à sua oferta depois que ela é publicada.  O [portal do Cloud Partner](https://cloudpartner.azure.com/) ajuda você a modificar corretamente os atributos de uma oferta, incluindo:

-  Adicionando nova imagem da VM (máquina virtual) ou versão do pacote a uma SKU existente
-  Alterar regiões em que um SKU está disponível
-  Adicionando novas SKUs
-  Atualizando metadados do Marketplace para ofertas ou SKUs 
-  Atualizando os preços das ofertas pagas conforme o uso

O portal também tem recursos, como a capacidade de comparar recursos e exibir um histórico de recursos para uma oferta, que o ajudará a gerenciar alterações.  Depois de modificar uma oferta ou SKU, ela deve ser republicada antes que as alterações entrem em "Live".  Este artigo orienta você pelos diferentes aspectos da atualização da oferta do Marketplace.

## <a name="unpermitted-changes-to-an-offersku"></a>Alterações não permitidas em uma oferta/SKU

Há alguns atributos de uma oferta ou SKU que não podem ser modificados depois de publicados no Marketplace.  Os campos correspondentes são desabilitados na guia **Editor** do portal, por exemplo:  

- ID da oferta e ID do Publicador
- ID DO SKU 
- Contagem de discos de dados de SKUs existentes
- Cobrança/alterações no modelo de licença de SKUs existentes
- Marcas de versão, por exemplo: `1.0.1`


## <a name="common-update-operations"></a>Operações comuns de atualização

As seções a seguir explicam como executar algumas das operações mais atualizadas.  Essas operações não estão disponíveis para todos os tipos de oferta.  Você deve entrar no Portal do Cloud Partner para iniciar qualquer uma dessas operações.


### <a name="update-offer-contacts"></a>Atualizar contatos da oferta

Use as etapas a seguir para atualizar os contatos de suporte para sua oferta.
1. Na página **todas as ofertas** , selecione a oferta.
2. Selecione a guia **contatos** . atualize seus contatos.
3. Selecione o botão **Guardar**.
4. Selecione **publicar** para iniciar o processo de publicação.


### <a name="change-regions-an-offer-or-sku-is-available-in"></a>Alterar as regiões em que uma oferta ou SKU está disponível

Ao longo do tempo, talvez você queira tornar sua oferta/SKU disponível em mais regiões.
Como alternativa, talvez você queira parar de dar suporte à oferta/SKU em uma determinada região.
Para implementar essas alterações, siga as etapas a seguir.

1. Na página **todas as ofertas** , localize a oferta que você deseja atualizar.

Para ofertas do Azure Marketplace:

1. Selecione a guia **SKUs** .  Selecione a SKU a ser modificada.
1. Clique no botão **selecionar países** no campo **disponibilidade de país/região** .
1. Na caixa de diálogo disponibilidade da região, adicione ou remova as regiões desta SKU.

Para ofertas do AppSource:

1. Selecione a guia **detalhes da vitrine** .
1. Ao lado do rótulo **países/regiões com suporte** , clique em **países/regiões com suporte**. 
1. Na caixa de diálogo países/regiões com suporte, adicione ou remova as regiões desta oferta.

Para ambos os Marketplaces:

1. Clique em **publicar** para iniciar o processo de publicação. 

Se um SKU estiver sendo disponibilizado em uma nova região, você poderá especificar o preço para essa região específica por meio da funcionalidade **exportar dados de preços** . Se você estiver adicionando uma região que estava disponível anteriormente, não poderá atualizar seu preço porque as alterações de preço não são permitidas.


### <a name="add-a-new-sku"></a>Adicionar uma nova SKU 

Para disponibilizar uma nova SKU para uma oferta existente, use as seguintes etapas:

1. Na página **todas as ofertas** , localize a oferta.
3. No formulário **SKUs** , clique em **Adicionar nova SKU** e forneça uma **ID de SKU** no pop-up.
4. Siga o restante das etapas detalhadas na [oferta publicar uma máquina virtual](../virtual-machine/cpp-publish-offer.md).
5. Clique em **publicar** para iniciar o processo de publicação.


### <a name="update-offer-marketplace-assets"></a>Atualizar ativos do Marketplace da oferta

Você pode ter cenários em que precisa atualizar os ativos de imagem e com base em texto do Marketplace, como logotipos da empresa, descrição da oferta, etc. Use as etapas a seguir para atualizar esses ativos.

1. Na página **todas as ofertas** , encontre sua oferta. 
2. Selecione a guia **Marketplace** e siga as instruções no tópico da *guia Marketplace* da sua oferta.
3. Clique em **publicar** para iniciar o processo de publicação.


### <a name="update-pricing-on-published-offers"></a>Atualizar os preços das ofertas publicadas

Depois que sua oferta paga conforme o uso for publicada, você não poderá aumentar o preço de uma SKU existente.  Em vez disso, crie um SKU na mesma oferta, exclua o SKU antigo e, em seguida, Republique sua oferta. Você pode diminuir o preço em ofertas publicadas anteriormente. Para diminuir o preço da oferta:

1. Selecione a SKU para a qual você deseja diminuir os preços.
2. Você deve definir o preço inferior pelo mesmo mecanismo usado originalmente: diretamente na interface do usuário do portal ou com a planilha de importação/exportação.
3. Clique em **Guardar**.
4. Clique em **publicar** para iniciar o processo de publicação.

O preço será visível para novos clientes quando ele estiver ativo no Marketplace, e todos os novos clientes pagarão o novo preço reduzido.  Para clientes existentes, a redução de preço é refletida retroativamente no início do ciclo de cobrança durante o qual o preço diminuiu em vigor.  Se eles já foram cobrados pelo ciclo durante o qual uma redução de preço ocorreu, eles receberão um reembolso durante o próximo ciclo de cobrança para cobrir o preço reduzido.


## <a name="compare-feature"></a>Comparar recurso

Ao fazer alterações em uma oferta publicada, você pode usar o recurso *comparar* para auditar as alterações. Para utilizar esse recurso:

1. Em qualquer ponto do processo de edição, você pode clicar no botão **comparar** na guia **Editor** para sua oferta.
2. Uma janela de comparação exibe as versões lado a lado das alterações salvas nessa oferta, em comparação com a oferta do Marketplace. 

![Botão comparar oferta na guia do editor](./media/offer-compare-button.png)


## <a name="history-of-publishing-actions"></a>Histórico de ações de publicação

Para exibir a atividade de publicação histórica, selecione a guia **histórico** na MenuBar vertical esquerda da portal do Cloud Partner.  A página histórico fornece filtragem flexível por várias características e dá suporte à ordenação de colunas.  Cada evento de publicação é carimbo de data/hora.  Para obter mais informações, consulte a [página Histórico de auditoria](../portal-tour/cpp-history-page.md).


## <a name="next-steps"></a>Passos seguintes

Você também pode usar a Portal do Cloud Partner para [excluir uma SKU ou oferta publicada](./cpp-delete-offer.md).
