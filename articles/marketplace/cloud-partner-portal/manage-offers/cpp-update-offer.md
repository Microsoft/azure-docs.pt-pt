---
title: Atualizar ofertas de marketplace [ Mercado Azure
description: Atualizações nos Mercados Azure e AppSource utilizando o Portal do Parceiro cloud
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 01/11/2019
ms.author: dsindona
ms.openlocfilehash: 101369a050770be3acd9534cef6229037fe1c366
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80288517"
---
# <a name="update-azure-marketplace-and-appsource-offers"></a>Atualizar ofertas do Azure Marketplace e appSource

Existem vários tipos de atualizações que pode aplicar à sua oferta depois de publicada.  O [Portal do Parceiro cloud](https://cloudpartner.azure.com/) ajuda-o a modificar adequadamente os atributos de uma oferta, incluindo:

-  Adicionar nova imagem ou versão pacote de máquina virtual (VM) a um SKU existente
-  Alterar regiões um SKU está disponível em
-  Adicionar novas SKUs
-  Atualizar metadados de mercado para ofertas ou SKUs 
-  Atualizar os preços das ofertas pay-as-you-go

O portal também tem funcionalidades, como a capacidade de comparar funcionalidades e ver um histórico de funcionalidades para uma oferta, que o ajudam a gerir alterações.  Depois de modificar uma oferta ou SKU, deve ser republicada antes que as alterações sejam "ao vivo".  Este artigo acompanha-o através dos diferentes aspetos da atualização da sua oferta de mercado.

## <a name="unpermitted-changes-to-an-offersku"></a>Alterações não autorizadas a uma oferta/SKU

Existem alguns atributos de uma oferta ou SKU que não podem ser modificados uma vez que foi publicado no mercado.  Os campos correspondentes são desativados no separador **Editor** do portal, por exemplo:  

- DI de oferta e ID da editora
- SKU ID 
- Contagem de discos de dados das SKUs existentes
- Alterações no modelo de faturação/licença das SKUs existentes
- Etiquetas de versão, por exemplo:`1.0.1`


## <a name="common-update-operations"></a>Operações comuns de atualização

As seguintes secções explicam como realizar algumas das operações mais atualizadas.  Estas operações não estão disponíveis para todos os tipos de oferta.  Tem de assinar no Portal do Parceiro cloud para iniciar qualquer uma destas operações.


### <a name="update-offer-contacts"></a>Atualizar contactos de oferta

Utilize os seguintes passos para atualizar os contactos de suporte para a sua oferta.
1. Na página **All Offers,** selecione a oferta.
2. Selecione o separador **Contactos.** Atualizar os seus contactos.
3. Selecione o botão **Guardar**.
4. Selecione **Publicar** para iniciar o processo de publicação.


### <a name="change-regions-an-offer-or-sku-is-available-in"></a>Alterar regiões uma oferta ou SKU está disponível em

Com o tempo, poderá querer disponibilizar a sua oferta/SKU em mais regiões.
Em alternativa, pode querer deixar de apoiar a oferta/SKU numa determinada região.
Para implementar estas alterações, siga os seguintes passos.

1. Na página **All offers,** encontre a oferta que gostaria de atualizar.

Para ofertas do Azure Marketplace:

1. Selecione o separador **SKUs.**  Selecione o SKU para modificar.
1. Clique no botão **Select Countries** no campo de **disponibilidade País/Região.**
1. Na região, adicione ou remova as regiões para este SKU.

Para ofertas AppSource:

1. Selecione o separador Detalhes da **Montra.**
1. Ao lado do rótulo **países/regiões apoiados,** clique em **países/regiões apoiados.** 
1. Nos países/regiões apoiados, adicione ou remova as regiões para esta oferta.

Para qualquer um dos mercados:

1. Clique em **Publicar** para iniciar o processo de publicação. 

Se um SKU estiver a ser disponibilizado numa nova região, tem a capacidade de especificar preços para essa região em particular através da funcionalidade de Dados de Preços de **Exportação.** Se estiver a adicionar uma região que estava anteriormente disponível, não pode atualizar os seus preços porque não são permitidas alterações de preços.


### <a name="add-a-new-sku"></a>Adicione um novo SKU 

Para disponibilizar um novo SKU para uma oferta existente, utilize os seguintes passos:

1. Na página **All offers,** encontre a oferta.
3. Sob o formulário **SKUs,** clique em **Adicionar novo SKU** e fornecer um **ID SKU** no pop-up.
4. Siga o resto dos passos detalhados em [Publicar uma oferta de máquina virtual](../virtual-machine/cpp-publish-offer.md).
5. Clique em **Publicar** para iniciar o processo de publicação.


### <a name="update-offer-marketplace-assets"></a>Atualizar ativos de mercado de oferta

Pode ter cenários em que precisa de atualizar os ativos de imagem e texto do mercado, tais como os logótipos da sua empresa, a descrição da oferta, etc. Utilize os seguintes passos para atualizar estes ativos.

1. Na página **All offers,** encontre a sua oferta. 
2. Selecione o separador **Marketplace** e siga as instruções no tópico do *separador Marketplace* da sua oferta.
3. Clique em **Publicar** para iniciar o processo de publicação.


### <a name="update-pricing-on-published-offers"></a>Atualizar preços em ofertas publicadas

Uma vez publicada a sua oferta pay-as-you-go, não pode aumentar o preço de um SKU existente.  Em vez disso, crie um SKU sob a mesma oferta, elimine o antigo SKU e, em seguida, republique a sua oferta. Pode baixar o preço das ofertas anteriormente publicadas. Para diminuir o seu preço de oferta:

1. Selecione o SKU para o qual pretende diminuir os preços.
2. Deve fixar o preço mais baixo pelo mesmo mecanismo que utilizou inicialmente: quer diretamente no portal UI, quer com a folha de cálculo de importação/exportação.
3. Clique em **Guardar**.
4. Clique em **Publicar** para iniciar o processo de publicação.

O preço é visível para novos clientes uma vez que é ao vivo no mercado, e todos os novos clientes pagarão então o novo preço diminuído.  Para os clientes existentes, a diminuição dos preços reflete-se retroativamente no início do ciclo de faturação durante o qual a diminuição dos preços se tornou eficaz.  Se já foram cobrados para o ciclo durante o qual ocorreu uma diminuição dos preços, receberão um reembolso durante o seu próximo ciclo de faturação para cobrir a diminuição do preço.


## <a name="compare-feature"></a>Comparar funcionalidade

Quando efaz alterações numa oferta publicada, pode utilizar a funcionalidade *Compare* para auditar as alterações. Para utilizar esta funcionalidade:

1. Em qualquer ponto do processo de edição, pode clicar no botão **Compare** no separador **Editor** para a sua oferta.
2. Uma janela de comparação exibe versões lado a lado das alterações guardadas nesta oferta em comparação com a oferta de mercado. 

![Compare o botão de oferta no separador do editor](./media/offer-compare-button.png)


## <a name="history-of-publishing-actions"></a>História das ações editoriais

Para ver a atividade de publicação histórica, selecione o separador **História** na barra de menus vertical esquerda do Portal do Parceiro cloud.  A página História fornece filtragem flexível por várias características e suporta a encomenda da coluna.  Cada evento editorial tem um tema marcado.  Para mais informações, consulte a página de histórico da [Auditoria.](../portal-tour/cpp-history-page.md)


## <a name="next-steps"></a>Passos seguintes

Também pode utilizar o Portal do Parceiro cloud para [apagar um SKU ou oferta publicado.](./cpp-delete-offer.md)
