---
title: Atualizar uma oferta de mercado comercial existente
description: Como fazer atualizações para uma oferta comercial existente, incluindo edição, eliminação de um rascunho, cancelamento de um pedido de publicação, parar de vender uma oferta ou plano, e sincronizar audiências privadas.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: keferna
ms.author: keferna
ms.date: 01/16/2020
ms.openlocfilehash: 810c38397981a100c2d5a68492f8a15b75cd79bd
ms.sourcegitcommit: b6f3ccaadf2f7eba4254a402e954adf430a90003
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/20/2020
ms.locfileid: "92276053"
---
# <a name="update-an-existing-offer-in-the-commercial-marketplace"></a>Atualizar uma oferta existente no mercado comercial

Pode ver as suas ofertas existentes no **separador Visão Geral** do [portal Mercado Comercial](https://partner.microsoft.com/dashboard/commercial-marketplace/offers) no Partner Center.

Para atualizar uma oferta existente que está atualmente ao vivo no mercado comercial:

1. Selecione o nome da oferta que gostaria de atualizar. O estado da oferta pode ser listado como **Preview**, **Live**, **Publish em curso**, **Draft**, **Atenção necessária**, ou **Não disponível** (se já escolheu deixar de vender a oferta). Uma vez selecionado, a página geral da **Oferta** para essa oferta será aberta.
2. Selecione **Update** a partir do cartão na página geral da oferta, ou o item do menu na navegação esquerda para a área que gostaria de atualizar. Pode querer atualizar a **configuração**da Oferta , **Propriedades**, **Listagem de Ofertas,** **Pré-visualização,** **Configuração Técnica,** **Visão geral do Plano**ou Test **drive**.
3. Faça as alterações e **selecione Guardar o rascunho**. Repita este processo até que todas as alterações estejam completas.

## <a name="review-and-publish-an-updated-offer"></a>Rever e publicar uma oferta atualizada

Quando estiver pronto para publicar a sua oferta atualizada, selecione **'Rever' e publicar** a partir de qualquer página. A **página de Revisão e publicação** abrirá. Nesta página pode:

- Consulte o estado de conclusão das secções da oferta que atualizou: 
    - **Alterações não publicadas**: A secção foi atualizada e está completa. Todos os dados necessários foram fornecidos e não foram introduzidos erros nas atualizações.
    - **Incompleto**: As atualizações efetuadas à secção introduziram erros que precisam de ser corrigidos ou exigem mais informações.
- Forneça informações adicionais à equipa de testes de certificação para garantir que os testes decorram sem problemas.
- Submeta a oferta atualizada para publicação selecionando **a Publish**.  Enviaremos um e-mail quando estiver disponível uma versão de pré-visualização da oferta atualizada para que possa rever e assinar.

> [!IMPORTANT]
> Tem de rever a pré-visualização da sua oferta assim que estiver disponível e selecione **Go-live** para publicar a sua oferta atualizada para o seu público pretendido (público ou privado).

## <a name="add-a-plan-to-an-existing-offer"></a>Adicione um plano a uma oferta existente

Para adicionar um novo plano dentro de uma oferta já publicada:

1. Com a página geral da **Oferta** para a sua oferta existente aberta, vá à página **de visão geral** do Plano e, em seguida, selecione Criar novo **plano**.
1. Crie um novo plano de acordo com as [diretrizes](./create-new-saas-offer.md#plan-overview) utilizando o modelo de preços dos **planos existentes.**
1. **Selecione Guardar o projeto** depois de alterar o nome do plano.
1. **Selecione Publicar** quando estiver pronto para publicar as suas atualizações. A página **[de Revisão e publicação](#review-and-publish-an-updated-offer)** abre e fornece um estado de conclusão para as suas atualizações.

## <a name="update-a-plan-within-an-existing-offer"></a>Atualizar um plano dentro de uma oferta existente

Para fazer alterações a um plano dentro de uma oferta já publicada:

1. Com a página geral da **Oferta** para a sua oferta existente aberta, escolha o plano que pretende alterar. Se o plano não estiver acessível a partir da lista **de visão geral** do Plano, selecione Ver todos os **planos**.
1. Selecione o modelo **de plano,** **preço**ou **disponibilidade**. *Atualmente, os planos estão disponíveis apenas em inglês (Estados Unidos)*.
1. **Selecione Guardar o rascunho** depois de escoar quaisquer alterações ao nome do plano, descrição ou disponibilidade do público.
1. Selecione **'Rever' e publique** quando estiver pronto para publicar as suas atualizações. A página **[de Revisão e publicação](#review-and-publish-an-updated-offer)** abre e fornece um estado de conclusão para as suas atualizações.
1. Submeta o plano atualizado de publicação selecionando **a Publish**. Enviaremos um e-mail quando estiver disponível uma versão de pré-visualização da oferta atualizada para que possa rever e assinar.

## <a name="offer-a-virtual-machine-plan-at-a-new-price"></a>Ofereça um plano de máquina virtual a um novo preço

Depois de publicado um plano de máquina virtual, o seu preço não pode ser alterado. Para oferecer o mesmo plano a um preço diferente, você deve esconder o plano e criar um novo com o preço atualizado. Primeiro, esconda o plano com o preço que quer mudar:

1. Com a página geral da **Oferta** para a sua oferta existente aberta, escolha o plano que pretende alterar. Se o plano não estiver acessível a partir da lista **de visão geral** do Plano, selecione Ver todos os **planos**.
1. Selecione a caixa de verificação **do plano Oculta.** Guarde o rascunho antes de continuar.

Agora que escondeu o plano com o preço antigo, crie uma cópia desse plano com o preço atualizado:

1. No Partner Center, volte à **visão geral do Plano.**
2. **Selecione Criar novo plano.** Introduza um **ID do plano** e um **nome de Plano**e, em seguida, selecione **Criar**.
1. Para reutilizar a configuração técnica do plano que ocultou, selecione a caixa **de verificação de configuração técnica de Reutilização.** Leia [Criar planos para uma oferta de VM](../azure-vm-create-plans.md) para saber mais.
    > [!IMPORTANT]
    > Se selecionar **Este plano reutiliza a configuração técnica de outro plano,** não poderá deixar de vender o plano dos pais mais tarde. Não use esta opção se quiser parar de vender o plano dos pais.
3. Complete todas as secções necessárias para o novo plano, incluindo o novo preço.
1. Selecione **Guardar rascunho**.
1. Depois de ter concluído todas as secções necessárias para o novo plano, selecione **'Rever' e publicar**. Isto submeterá a sua oferta para revisão e publicação. Leia [a Revisão e publique uma oferta no mercado comercial](../review-publish-offer.md) para mais detalhes.

## <a name="compare-changes-to-commercial-marketplace-offers"></a>Compare as alterações às ofertas de mercado comercial

Pode auditar as alterações que faz a uma oferta [publicada](#compare-changes-to-published-offer) ou [de pré-visualização](#compare-changes-to-a-preview-offer) antes de as tornar ao vivo utilizando **o Compare**.

> [!NOTE]
> Uma oferta publicada é uma oferta que foi publicada com sucesso para Preview ou Live state.

Veja abaixo as informações gerais de auditoria:

- Pode utilizar **Compare** em qualquer ponto durante o processo de edição.
- Selecione um campo na página **Compare** para navegar para o valor que pretende modificar.
- Se estiver pronto para publicar as suas atualizações, selecione **'Rever e publicar.'**
- Para ver os valores de todos os campos, mesmo campos não atualizados, selecione o filtro **Todos os campos.** Pode modificar filtros dentro destes campos selecionando **campos modificados**e selecionando um dos filtros abaixo:
    - O filtro **de valores removidos** apresenta campos que publicou e está agora completamente removido.
    - O filtro de **valores adicionados** exibe campos que não publicou originalmente e que agora está a adicionar.
    - O filtro de **valores editados** exibe campos que tinham sido publicados mas agora atualizou os conteúdos.

      >[!NOTE]
      > Se um destes filtros não estiver disponível, indica que não fez uma atualização desse tipo.

- Para ver apenas valores que não tenham sido atualizados, selecione o filtro **de campos inalterados.** Os valores de campo apresentados para a versão publicada e projeto serão os mesmos.

  ![Filtros para comparar atualizações com a sua oferta publicada ou de pré-visualização](./media/compare-changes-marketplace.png)

>[!NOTE]
> As páginas seguintes não **suportam**atualmente Compare:
>- Público revendedor de CSP
>- Configuração técnica da unidade de teste
>- Lista de mercado de unidade de teste
>- Co-vender
>- Ficheiros suplementares

### <a name="compare-changes-to-published-offer"></a>Compare as alterações à oferta publicada

Siga as instruções abaixo para comparar as suas alterações da oferta publicada:

1. **Selecione Compare** na barra de comando da página. A página **Compare** fornece versões lado a lado das alterações guardadas desta oferta e da oferta de mercado publicada.
2. Quando acede **a Comparar** a partir de uma página específica da oferta, o padrão apresenta apenas as alterações es feitas nessa página.
    - Se quiser comparar alterações em todas as páginas, altere a página de **Selecione uma página para comparar**.  
    - Se pretender comparar alterações à oferta em todas as páginas, selecione **Todas as páginas**.

Como padrão, **Compare** contrasta as suas novas alterações à oferta de mercado ao vivo.

Lembre-se de republicar a sua oferta depois de fazer atualizações para que as alterações produzam efeitos.

### <a name="compare-changes-to-a-preview-offer"></a>Compare alterações a uma oferta de pré-visualização

Se tiver alterações na pré-visualização que não estão ao vivo, pode comparar novas alterações com a oferta de pré-visualização do mercado.

Siga as instruções abaixo para comparar novas alterações com a sua oferta de mercado de pré-visualização:

1. **Selecione Compare** na barra de comando da página.
2. Selecione o **Com** dropdown e altere-o da **oferta ao vivo** para a oferta de **pré-visualização**.
3. A página **Compare** fornece versões lado a lado que mostram as alterações.

>[!NOTE]
>Se a sua oferta ainda não se tornou ao vivo, mas publicou para pré-visualização, não tem a opção de comparar com uma oferta ao vivo.

Lembre-se de republicar a sua oferta depois de fazer atualizações para que as alterações produzam efeitos.

## <a name="delete-a-draft-offer"></a>Excluir uma oferta de rascunho

Pode eliminar uma oferta de rascunho (que não foi publicada) selecionando o **projeto delete** na página de visão geral da **Oferta.** Esta opção não estará disponível para si se já publicou a oferta.

Depois de confirmar que pretende eliminar a oferta de projeto, a oferta deixará de ser visível ou acessível no Centro de Parceiros e a página **All Offers** será aberta.

## <a name="delete-a-draft-plan"></a>Excluir um projeto de plano

Para eliminar um plano que não tenha sido publicado, **selecione Eliminar o projeto** da página de visão geral do **Plano.** Esta opção não estará disponível para si se já publicou a oferta.

Depois de confirmar que pretende eliminar o projeto de plano, o plano deixará de ser visível ou acessível no Centro de Parceiros.

## <a name="cancel-publishing"></a>Cancelar publicação

Para cancelar uma oferta com a **Publicação em curso:**

1. Selecione o nome da oferta para abrir a página **geral da Oferta.**
1. Selecione **Cancelar a publicação** do canto superior direito da página.
1. Confirme que deseja impedir que a oferta seja publicada.

Se quiser publicar a oferta mais tarde, terá de começar o processo de publicação.

> [!NOTE]
> Só pode impedir que uma oferta seja publicada se a oferta ainda não tiver progredido para que a editora assine o passo. Depois de selecionar **Go ao vivo,** não terá a opção de cancelar a publicação por mais tempo.

## <a name="stop-selling-an-offer-or-plan"></a>Pare de vender uma oferta ou plano

Por várias razões, pode decidir remover a sua lista de ofertas do mercado comercial da Microsoft. A remoção da oferta garante que os novos clientes já não podem comprar ou implementar a sua oferta, mas não tem impacto nos clientes existentes.

Para parar de vender uma oferta depois de a publicar, selecione Pare de **vender** na página geral da **Oferta.**

Depois de confirmar que pretende deixar de vender a oferta, dentro de algumas horas deixará de ser visível no mercado comercial e nenhum novo cliente poderá descarregá-la.

Para parar de vender um plano, selecione Pare de **vender** na página **de visão geral** do Plano. A opção de parar de vender um plano só está disponível se tiver mais do que um plano na oferta. Pode optar por parar de vender um plano sem afetar outros planos dentro da sua oferta. Uma vez confirmado que quer parar de vender o plano, deve republicar a oferta para que a mudança entre em vigor. Após a republicação da oferta, o plano deixará de ser visível no mercado comercial e nenhum novo cliente poderá descarregá-la.

Todos os clientes que anteriormente adquiriram a oferta ou plano ainda podem usá-la. Podem descarregá-lo novamente, mas não receberão atualizações se atualizarem e reeditarem a oferta ou planearem posteriormente.

Depois de o seu pedido para parar de vender a oferta/plano ter sido concluído, ainda o verá no portal do mercado comercial no Partner Center com um estatuto **de Não disponível.**

Se decidir listar ou vender esta oferta ou planear novamente, siga as instruções para [atualizar uma oferta existente](#update-an-existing-offer-in-the-commercial-marketplace). Não se esqueça que terá de **publicar** novamente a oferta ou planear depois de escamar quaisquer alterações.

## <a name="remove-offers-from-existing-customers"></a>Remova as ofertas dos clientes existentes

Para remover as ofertas dos clientes existentes, [faça login um pedido de suporte](https://aka.ms/marketplacepublishersupport). Na lista de tópicos de suporte, selecione **Oferta de Mercado Comercial**ou  >  **Deslistação de Aplicações, Remoção ou Rescisão** e envie o pedido. A equipa de apoio irá guiá-lo através do processo de remoção da oferta.

## <a name="sync-private-plan-audiences"></a>Sync público de plano privado

Se a sua oferta inclui um ou mais planos configurados para estarem apenas disponíveis para um público restrito privado, é possível atualizar apenas o público que pode aceder a esse plano privado sem publicar outras alterações à oferta. 

Para atualizar e sincronizar o público privado para o seu plano):

- Modifique o público em um ou mais planos privados utilizando o botão + **Add ID** ou **Import (csv)** e, em seguida, guarde as alterações.
- Selecione **Sync audiência privada** a partir da página de visão geral do **Plano.**

**O público privado sincronizado** publicará apenas as alterações ao seu público privado, sem publicar quaisquer outras atualizações que possa ter feito à oferta de projeto.

## <a name="next-steps"></a>Passos seguintes

- [Verifique o estado editorial da sua oferta de mercado comercial](./publishing-status.md)
