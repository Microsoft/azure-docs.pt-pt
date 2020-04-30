---
title: Atualizar uma oferta vm existente no Mercado Azure
description: Explica como atualizar uma oferta vm existente no Azure Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 08/27/2018
ms.author: dsindona
ms.openlocfilehash: 934be24d07c01c76c8caf5e16af4b765df79c964
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82142922"
---
# <a name="update-an-existing-vm-offer-on-azure-marketplace"></a>Atualizar uma oferta vm existente no Azure Marketplace

> [!IMPORTANT]
> A partir de 13 de abril de 2020, começaremos a mover a gestão das suas ofertas de Máquina Virtual Azure para partner Center. Após a migração, você vai criar e gerir suas ofertas no Partner Center. Siga as instruções em [Create a Azure Virtual Machine para](https://docs.microsoft.com/azure/marketplace/partner-center-portal/azure-vm-create-offer) gerir as suas ofertas migratórias.

Este artigo acompanha-o através dos diferentes aspetos da atualização da sua oferta de máquina virtual (VM) no Portal do [Parceiro cloud](https://cloudpartner.azure.com/) e, em seguida, reeditando a oferta.

Existem uma série de razões comuns para atualizar a sua oferta, incluindo:

-  Adicione uma nova versão de imagem VM às SKUs existentes
-  Alterar regiões um SKU está disponível
-  Adicionar novas SKUs
-  Atualizar os metadados do mercado para a oferta ou SKUs individuais
-  Atualizar os preços das ofertas pay-as-you-go

Para o ajudar nestas modificações, o portal oferece as funcionalidades **Compare** e **History.**  

>[!Note]
>O canal parceiro Cloud Solution Providers (CSP) já está disponível.  Consulte os [Fornecedores de Soluções cloud](../../cloud-solution-providers.md) para obter mais informações sobre o marketing da sua oferta através dos canais parceiros do Microsoft CSP.

## <a name="unpermitted-changes-to-vm-offer-or-sku"></a>Alterações não autorizadas à oferta vm ou SKU

Existem alguns atributos de uma oferta VM ou SKU que não podem ser modificadas uma vez que a oferta é ao vivo no Mercado Azure, principalmente:

-  **Oferta ID** e **Id** editor da oferta
-  **Identificação SKU** das SKUs existentes
-  Contagem de discos de dados das SKUs existentes
-  Alterações no modelo de faturação/licença às SKUs existentes
-  Aumento de preços para um SKU publicado


## <a name="common-update-operations"></a>Operações comuns de atualização

Embora exista uma ampla gama de características que pode alterar numa oferta VM, as seguintes operações são comuns.

### <a name="update-the-vm-image-version-for-a-sku"></a>Atualize a versão de imagem VM para um SKU

É comum que uma imagem VM seja periodicamente atualizada com patches de segurança, funcionalidades adicionais, e assim por diante.  Nestes cenários, pretende atualizar a imagem VM que o seu SKU refere utilizando os seguintes passos:

1.  Assine no Portal do [Parceiro da Nuvem.](https://cloudpartner.azure.com/)

2.  Em **todas as ofertas,** encontre a oferta para atualizar.

3.  No separador **SKUs,** clique no SKU associado à imagem VM para atualizar.

4.  Na **versão Disk,** clique em **+Versão New Disk** para adicionar uma nova imagem VM.

5.  Forneça a nova **versão VM**Images Disk . A versão em disco tem de seguir o formato da [versão semântica.](https://semver.org/) As versões devem ser do formulário X.Y.Z, onde X, Y e Z são inteiros. Verifique se a nova versão que fornece é maior do que todas as versões anteriores; caso contrário, após a nova publicação, a nova versão não será exibida nem no portal nem no Mercado Azure.

6.  Para **O URL OS VHD,** introduza a assinatura de acesso partilhado [(SAS) URI](./cpp-get-sas-uri.md) criada para o sistema operativo VHD. 

    > [!WARNING] 
    > A contagem de discos de dados não pode alterar entre diferentes versões do SKU. Se as versões anteriores tivessem os discos de dados configurados, esta nova versão também deve ter o mesmo número de discos de dados.

7.  Clique em **Publicar** para iniciar o fluxo de trabalho para publicar a sua nova versão VM no Mercado Azure.


### <a name="change-region-availability-of-a-sku"></a>Alterar a disponibilidade da região de um SKU

Com o tempo, poderá querer disponibilizar a sua oferta/SKU em mais regiões.  Em alternativa, pode querer deixar de apoiar a oferta/SKU numa determinada região.
Para modificar a disponibilidade, utilize os seguintes passos:

1.  Assine no Portal do [Parceiro da Nuvem.](https://cloudpartner.azure.com/)

2.  Em **todas as ofertas** encontre a oferta que deseja atualizar.

3.  No separador **SKUs,** clique no SKU que pretende modificar a sua disponibilidade.

4.  Clique no botão **Select Countries** no campo de **disponibilidade País/Região.**

5.  Na região, a disponibilidade pop-up, adicione ou remova as regiões para este SKU.

6.  Clique em **Publicar** para iniciar o fluxo de trabalho de publicação para atualizar a sua disponibilidade da região das SKUs.

Se um SKU estiver a ser disponibilizado numa nova região, terá a capacidade de especificar preços para essa região em particular através da funcionalidade de Dados de Preços de **Exportação.** Se estiver a adicionar uma região que já esteve disponível antes, não poderá atualizar os seus preços porque não são permitidas alterações de preços.


### <a name="add-a-new-sku"></a>Adicione um novo SKU

Utilize os seguintes passos para disponibilizar um novo SKU para a sua oferta existente: 

1.  Assine no Portal do [Parceiro da Nuvem.](https://cloudpartner.azure.com/)

2.  Em **todas as ofertas** encontre a oferta que deseja atualizar.

3.  Sob o separador **SKUs,** clique em **Adicionar novo SKU** e forneça um **ID SKU** no pop-up.

4.  Republique o VM conforme detalhado no artigo [Publique uma máquina virtual no Azure Marketplace.](./cpp-publish-offer.md)

5.  Clique em **Publicar** para iniciar o fluxo de trabalho para publicar o seu novo SKU.


### <a name="update-offer-marketplace-metadata"></a>Atualizar oferecem metadados de mercado

Utilize os seguintes passos para atualizar os metadados do mercado — nome da empresa, logotipos, etc.- associados à sua oferta: 

1.  Assine no Portal do [Parceiro da Nuvem.](https://cloudpartner.azure.com/)

2.  Em **todas as ofertas** encontre a oferta que deseja atualizar.

3.  Goto the **Marketplace** tab segue as instruções no artigo [Publicar uma máquina virtual no Azure Marketplace](./cpp-publish-offer.md) para fazer alterações de metadados.

4.  Clique em **Publicar** para iniciar o fluxo de trabalho para publicar as suas alterações.


### <a name="update-pricing-on-published-offers"></a>Atualizar preços em ofertas publicadas

Uma vez publicada a sua oferta pay-as-you-go, não pode aumentar diretamente os preços sku.  (No entanto, pode criar um novo SKU sob a mesma oferta, eliminar o antigo SKU e, em seguida, reeditar a sua oferta para novos clientes.)  Em contrapartida, pode diminuir o preço de uma oferta publicada utilizando os seguintes passos:

1.  Assine no Portal do [Parceiro da Nuvem.](https://cloudpartner.azure.com/)

2.  Em **todas as ofertas,** encontre a oferta para atualizar.

3.  Clique no SKU para o qual pretende diminuir os preços.

4.  Se tiver definido o preço no 1x1 GUI, pode alterar o preço diretamente na UI. Se definir preços através da folha de cálculo de importação/exportação, só poderá baixar os preços através da característica de importação/exportação.

3.  Clique em **Guardar**.

4.  Clique em **Publicar** para iniciar o fluxo de trabalho para publicar as suas alterações.

A nova diminuição dos preços será visível para novos clientes assim que estiver ao vivo no site.  Este novo preço irá afetar os seus clientes das seguintes formas:

- Novos clientes serão cobrados esta nova taxa. 
- Para os clientes existentes, a diminuição dos preços refletir-se-á retroativamente no início do ciclo de faturação durante o qual a diminuição dos preços se tornou efetiva.
Se já foram cobrados para o ciclo durante o qual ocorreu uma diminuição dos preços, receberão um reembolso durante o seu próximo ciclo de faturação para cobrir a diminuição do preço.


<!-- TD: This has been implemented, need to change the SKU Tab topic to reflect and move this section there. -->
### <a name="simplified-currency-pricing"></a>Preços simplificados da moeda

A partir de 1 de setembro de 2018, será adicionada ao portal uma nova secção chamada **Preços Simplificados** da Moeda. A Microsoft está a dinamizar o negócio do Azure Marketplace, permitindo preços e coleções mais previsíveis dos seus clientes em todo o mundo. Esta racionalização incluirá a redução do número de moedas em que faturamos os seus clientes.

A nova secção terá preços nestas novas moedas.Uma vez que todos os clientes tenham sido migrados para estas novas moedas de liquidação, a secção de preços original será retirada e apenas a secção de preços simplificados da moeda permanecerá.

Terá até 1 de novembro de 2018 para fixar um novo preço para as regiões onde a moeda de liquidação está a mudar. Não poderá aumentar o preço das regiões em que a moeda de liquidação não está a mudar.

> [!NOTE] 
> Se utilizar APIs para publicar a sua oferta, poderá ver uma nova secção dentro da Oferta JSON. Isto seria anotado como `virtualMachinePricingV2` `monthlyPricingV2`ou, dependendo do tipo de oferta. 

Se tiver alguma dúvida sobre esta alteração, contacte o [Azure Marketplace Support](../../support-azure-marketplace.md).


## <a name="compare-feature"></a>Comparar funcionalidade

Quando fizer alterações numa oferta já publicada, pode aproveitar a funcionalidade **Compare** para auditar as alterações que foram efetuadas. Para utilizar esta funcionalidade:

1.  Em qualquer ponto do processo de edição, clique no botão **Compare** para a sua oferta.

    ![Comparar botão de funcionalidade](./media/publishvm_037.png)


2.  Ver versões lado a lado de ativos de marketing e metadados.


## <a name="history-of-publishing-actions"></a>História das Ações Editoriais

Para ver qualquer atividade de publicação histórica, clique no item **Histórico** na barra de menus de navegação esquerda do Cloud Partner Portal. Aqui poderá ver ações timestamped que foram tomadas durante a vida das suas ofertas do Azure Marketplace.  
<!-- TD: Add after section authored: For more information, see [History page](../portal-tour/cpp-history-page.md). -->

