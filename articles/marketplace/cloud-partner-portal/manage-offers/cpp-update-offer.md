---
title: Atualizar as ofertas do Marketplace - Azure Marketplace | Documentos da Microsoft
description: Ofertas de atualização no Azure e mercados de AppSource com o Portal de parceiro de Cloud
services: Azure, AppSource, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 01/11/2019
ms.author: pbutlerm
ms.openlocfilehash: 76b607502324c3ca25b3536d5197a97dbb80399d
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58102150"
---
# <a name="update-azure-marketplace-and-appsource-offers"></a>Atualizar as ofertas do Azure Marketplace e AppSource

Existem vários tipos de atualizações que pode aplicar a sua oferta depois da sua publicação.  O [Cloud Partner Portal](https://cloudpartner.azure.com/) ajuda a corretamente modificar atributos de uma oferta, incluindo:

-  Adicionar a nova versão de imagem ou pacote de máquina virtual (VM) para um SKU existente
-  Alterar um SKU está disponível de regiões
-  Adicionar novos SKUs
-  A atualizar os metadados de mercado para os SKUs ou ofertas 
-  A atualizar preços pay as you go oferece

O portal também tem recursos, como a capacidade de comparar recursos e ver um histórico de recursos para uma oferta, que auxiliam no gerenciamento de alterações.  Depois de modificar uma oferta ou SKU, tem de ser replicado antes das alterações ir "dinâmicos".  Este artigo explica os diferentes aspectos da atualizar a sua oferta do marketplace.

## <a name="unpermitted-changes-to-an-offersku"></a>Alterações unpermitted para uma oferta/SKU

Existem alguns atributos de uma oferta ou SKU que não é possível modificar assim que tiver sido publicado no marketplace.  Os campos correspondentes estão desativados no **Editor** separador do portal, por exemplo:  

- ID e o ID de publicador da oferta
- ID DO SKU 
- Contagem dos SKUs existentes do disco de dados
- Alterações no modelo de SKUs existentes de faturação/licença
- Versão etiquetas, por exemplo: `1.0.1`


## <a name="common-update-operations"></a>Operações de atualização comuns

As secções seguintes explicam como realizar algumas da maioria das operações de atualização.  Estas operações não estão disponíveis para todos os tipos de oferta.  Tem de iniciar sessão no Portal de parceiro de Cloud para começar a qualquer uma destas operações.


### <a name="update-offer-contacts"></a>Contactos de oferta de atualização

Utilize os seguintes passos para atualizar os contactos de suporte para a sua oferta.
1. Na **oferece todos os** , selecione a oferta.
2. Selecione o **contactos** separador. Atualize seus contatos.
3. Selecione o botão **Guardar**.
4. Selecione **publicar** para iniciar o processo de publicação.


### <a name="change-regions-an-offer-or-sku-is-available-in"></a>Alterar um SKU ou uma oferta está disponível de regiões

Ao longo do tempo, talvez queira tornar a sua oferta/SKU disponível em mais regiões.
Em alternativa, pode querer parar o/SKU de oferta de suporte numa determinada região.
Para implementar essas alterações, siga os passos seguintes.

1. Na **todas as ofertas** página, encontre a oferta que pretende atualizar.

Para ofertas do Azure Marketplace:

1. Selecione o **SKUs** separador.  Selecione o SKU para modificar.
1. Clique no **selecionar países** botão sob os **disponibilidade de país/região** campo.
1. Na caixa de diálogo de disponibilidade de região, adicionar ou remover as regiões para este SKU.

Para ofertas de AppSource:

1. Selecione o **detalhes de loja** separador.
1. Junto a **países/regiões suportadas** da etiqueta, clique em **países/regiões suportadas**. 
1. Na caixa de diálogo países/regiões suportados, adicionar ou remover regiões para esta oferta.

Para mercados:

1. Clique em **publicar** para iniciar o processo de publicação. 

Se um SKU está a ser disponibilizado numa nova região, tem a capacidade de especificar os preços para essa região específica através da **exportar dados de preços** funcionalidade. Se estiver a adicionar novamente uma região que estava anteriormente disponível, não é possível atualizar o seu preço porque não são permitidas alterações aos preços.


### <a name="add-a-new-sku"></a>Adicionar um novo SKU 

Para disponibilizar um SKU de novo para uma oferta existente, utilize os seguintes passos:

1. Na **todas as ofertas** página, encontre a oferta.
3. Sob o **SKUs** de formulário, clique em **adicionar novo SKU** e fornecer uma **ID de SKU** no pop-up.
4. Siga o resto dos passos detalhados [publicar uma oferta de máquina virtual](../virtual-machine/cpp-publish-offer.md).
5. Clique em **publicar** para iniciar o processo de publicação.


### <a name="update-offer-marketplace-assets"></a>Atualizar oferta marketplace ativos

Pode ter a cenários em que tem de atualizar o marketplace com base em texto e ativos de imagem, tais sua logótipos da empresa, oferecem a descrição, etc. Utilize os seguintes passos para atualizar estes recursos.

1. Na **todas as ofertas** página, encontre a sua oferta. 
2. Selecione o **Marketplace** separador e siga as instruções na sua oferta *separador Marketplace* tópico.
3. Clique em **publicar** para iniciar o processo de publicação.


### <a name="update-pricing-on-published-offers"></a>Atualizar dos preços das ofertas publicadas

Assim que a oferta pay as you go é publicada, não é possível aumentar o preço de um SKU existente.  Em vez disso, criação de um SKU relacionados à oferta mesmo, eliminar o SKU antigo e, em seguida, voltar a publicar a sua oferta. Pode diminuir o preço em ofertas publicadas anteriormente. Para diminuir o preço da oferta:

1. Selecione o SKU para o qual deseja diminuir a preços.
2. Tem de definir o preço mais reduzido, o mesmo mecanismo utilizado originalmente: diretamente no portal da interface do Usuário ou com a folha de cálculo de importação/exportação.
3. Clique em **Guardar**.
4. Clique em **publicar** para iniciar o processo de publicação.

O preço é visível para os novos clientes depois de ser publicada no marketplace, e todos os novos clientes, em seguida, irão pagar o novo preço reduzido.  Para clientes existentes, a diminuição do preço será refletida retroativamente para o início do ciclo de faturação durante o qual a diminuição do preço entrou em vigor.  Se já ter sido faturadas pelo ciclo durante o qual uma diminuição do preço ocorreu, receberá um reembolso durante o próximo ciclo de faturação para cobrir o preço reduzido.


## <a name="compare-feature"></a>Compare recursos

Quando fizer alterações na oferta publicada, pode utilizar o *comparar* funcionalidade para auditar as alterações. Para utilizar esta funcionalidade:

1. Em qualquer momento no processo de edição, pode clicar a **Compare** botão no **Editor** separador para a sua oferta.
2. Uma janela de comparação apresenta versões lado a lado das alterações guardadas para esta oferta em comparação com a oferta do marketplace. 

![Comparar o botão de oferta num separador do editor](./media/offer-compare-button.png)


## <a name="history-of-publishing-actions"></a>Histórico de ações de publicação

Para ver a atividade do histórico publicação, selecione o **histórico** separador na barra de menus vertical à esquerda do Portal de parceiro de Cloud.  A página de histórico fornece filtragem flexível por diversas características e suporta ordenação das colunas.  Cada evento de publicação é timestamped.  Para obter mais informações, consulte [página de histórico de auditoria](../portal-tour/cpp-history-page.md).


## <a name="next-steps"></a>Passos Seguintes

Também pode utilizar o Portal de parceiros da Cloud para [eliminar um SKU de publicação ou oferta](./cpp-delete-offer.md).
