---
title: Migrar soluções de co-venda do OCP GTM para Partner Center para o Microsoft AppSource
description: Saiba como migrar soluções de co-venda do OCP GTM para partner Center para o Microsoft AppSource).
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: vamahtan
ms.author: vamahtan
ms.date: 12/07/2020
ms.openlocfilehash: 6ad58283ad9078088f241a67426657eb7a538e10
ms.sourcegitcommit: 8b4b4e060c109a97d58e8f8df6f5d759f1ef12cf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/07/2020
ms.locfileid: "96781135"
---
# <a name="migration-of-co-sell-solutions-from-ocp-gtm-to-the-commercial-marketplace"></a>Migração de soluções de co-venda da OCP GTM para o mercado comercial

A Microsoft está a mover a experiência de publicação. O [mercado comercial](overview.md) fornece uma oferta simplificada de publicação para co-vender através dos três canais da Microsoft, centralizando a criação e gestão de ofertas no Partner Center, onde já está a gerir a sua relação com a Microsoft.

Como parceiro da Microsoft inscrito no mercado comercial, pode:

- Publique as suas ofertas centralmente e co-venda através dos canais diretos de cliente, parceiro e vendedor da Microsoft.
- Certifique-se de que as suas ofertas estão na loja online correta,[Microsoft AppSource](https://appsource.microsoft.com) ou [Azure Marketplace,](https://azure.microsoft.com)para chegar aos milhões de clientes em nuvem que se alinham com as capacidades da sua oferta.
- Conduza a sua própria experiência de publicação para co-vender com as ofertas que se alinham com os seus objetivos de negócio.
- Alinhe a sua oferta no Partner Center, onde já está a gerir a sua relação com a Microsoft e a co-vender oportunidades.
- Desbloqueie [as Recompensas do Mercado.](partner-center-portal/marketplace-rewards.md)

## <a name="prerequisites-to-continue-co-selling-with-microsoft"></a>Pré-requisitos para continuar a co-vender com a Microsoft

Certifique-se de que tem uma subscrição ativa da Microsoft Partner Network e está inscrito no mercado comercial no Partner Center.

- Junte-se à Rede de Parceiros da Microsoft [sem custos.](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership) Como parceiro, terá acesso a recursos exclusivos, programas, ferramentas e conexões para fazer crescer o seu negócio.
- Se não tiver uma conta no mercado comercial, [inscreva-se agora](partner-center-portal/create-account.md) para continuar a co-venda com a Microsoft e aceder a toda a experiência de publicação.

## <a name="publishing-updates-for-attaining-co-sell-ready-status"></a>Publicar atualizações para obter o estatuto de co-venda-ready

Para que a sua solução seja detetável aos vendedores e parceiros da Microsoft, deve satisfazer os [requisitos de co-venda prontos.](marketplace-co-sell.md) Para que um vendedor da Microsoft seja incentivado, a sua solução deve satisfazer os [requisitos elegíveis para incentivos.](marketplace-co-sell.md) Preencha estes requisitos no separador co-venda no Partner Center (ver [esta imagem](#cosell-tab) mais tarde neste artigo).

> [!NOTE]
> No mercado comercial, as suas soluções são referidas como "ofertas" ao longo da experiência editorial.

Depois de se matricular no mercado comercial, prepare-se para migrar as suas soluções da OCP GTM.

Siga estes passos antes de importar as suas soluções da OCP GTM:

1. Visite a lista de [editores](https://partner.microsoft.com/dashboard/account/v3/publishers/list)da sua empresa. Inclui o proprietário da conta, gestores e desenvolvedores que têm acesso editorial. Saiba mais sobre [as funções de utilizador do Partner Center](/azure/marketplace/partner-center-portal/manage-account#define-user-roles-and-permissions).
2. Peça a um dos contactos listados para [adicionar utilizadores](https://partner.microsoft.com/dashboard/account/usermanagement) ao mercado comercial como *gestores* ou *desenvolvedores,* uma vez que apenas estas funções podem editar e publicar soluções.
3. Trabalhe com os seus desenvolvedores para mover as suas soluções da sua conta OCP GTM para o mercado comercial.
4. Decida qual dos seguintes pretende fazer:
    1. Fundir esta solução com uma oferta semelhante no mercado comercial.
    1. Migrar esta solução da OCP GTM para o mercado comercial.
    1. Elimine esta solução.

## <a name="migrate-your-solutions-from-ocp-gtm"></a>Migrar as suas soluções a partir do OCP GTM

1. Inicie a migração [aqui.](https://partner.microsoft.com/solutions/migration#)
2. Selecione a página **'Vista Geral'** e, em seguida, **clique aqui para começar**.

    :::image type="content" source="media/co-sell-migrate/migration-overview.png" alt-text="Página geral do Centro de Parceiros, separador visão geral.":::

3. Para começar a migrar, selecione o separador **Soluções,** que exibe todas as soluções associadas aos seus IDs MPN.

    :::image type="content" source="media/co-sell-migrate/solutions-tab.png" alt-text="Página geral do Centro de Parceiros, separador Soluções.":::

    > [!NOTE]
    > Este separador notará se não houver soluções pendentes para migrar para o mercado comercial. Para continuar a co-vender com a Microsoft, certifique-se de que as soluções migradas são publicadas no mercado comercial.

    Saiba mais sobre o estado da solução, revendo as dicas da ferramenta. Todas as soluções pendentes estão listadas no âmbito **da Ação**.<a name="beginmigration"></a>

4. Selecione **Iniciar a migração** (ver imagem acima) para a solução que pretende migrar e, em seguida, selecione uma das seguintes opções:

    :::image type="content" source="media/co-sell-migrate/migration-options.png" alt-text="As três opções para a migração.":::

### <a name="merge-solution-with-a-similar-offer"></a>Solução de fusão com uma oferta semelhante

Selecione esta opção se a solução já estiver publicada no mercado comercial e os dois deverão ser fundidos numa única oferta. Isto evitará a criação de ofertas duplicadas.

1. Identifique a oferta existente.
    1. **Selecione Eu quero fundir esta solução com uma oferta semelhante no mercado comercial** (ver **Ação necessária** imagem [acima).](#beginmigration)
    1. O separador **Action 1** mostra que o mercado comercial ao vivo oferece que a sua solução OCP GTM pode ser associada. Selecione a oferta ao vivo da lista se tiver uma. Se não houver uma lista de ofertas à escolha, insira o endereço virado para o cliente (URL) do Microsoft AppSource ou do Azure Marketplace.
        [![O separador Action 1 do processo de fusão.](media/co-sell-migrate/action-1-merge.png)](media/co-sell-migrate/action-1-merge.png#lightbox)
    1. Selecione **Continuar**.
1. Solicito a fusão.
    1. O separador **Action 2** mostra direções para solicitar a fusão da sua solução OCP GTM com a que identificou. Para solicitar a fusão, **selecione Suporte de Contato Save &**, que abre uma página de suporte do parceiro num browser.
    1. **Selecione Fornecer Detalhes de Problema** e introduza o seguinte: O [ ![ separador Ação 2 do processo de fusão.](media/co-sell-migrate/action-2-merge.png)](media/co-sell-migrate/action-2-merge.png#lightbox)
    1. Selecione **Submeter**. A equipa de apoio ao parceiro entrará em contacto consigo dentro de dois dias úteis.
    1. O apoio ao parceiro trabalhará consigo para garantir uma fusão bem-sucedida desta oferta para que seja publicada como uma oferta ao vivo.

### <a name="migrate-this-solution-from-ocp-gtm"></a>Migrar esta solução a partir do OCP GTM

Selecione esta opção quando tiver uma solução OCP GTM que ainda não tenha uma oferta correspondente publicada no mercado comercial. Terá de publicar esta solução no mercado comercial para continuar a co-vender com a Microsoft e a migração desta solução irá poupar-lhe tempo, retendo a informação e a fatura de materiais da OCP GTM. Esta opção requer que selecione um tipo de oferta.

1. **Selecione Eu quero migrar esta solução do OCP GTM para o mercado comercial** (ver **Ação necessária** imagem [acima](#beginmigration)), em **seguida, continuar**.
1. No **separador Action 1,** selecione o [tipo de oferta](publisher-guide-by-offer-type.md)e, em seguida, **Continue**.

    [![O separador Action 1 do processo de migração.](media/co-sell-migrate/action-1-migrate.png)](media/co-sell-migrate/action-1-migrate.png#lightbox)

1. No **separador Action 2,** selecione o perfil do [editor](partner-center-portal/create-account.md) da lista fornecida. Se não tiver uma conta de editor, crie uma no [Partner Center,](https://partner.microsoft.com/solutions/migration)selecione-a aqui.

    [![O separador Action 2 do processo de migração.](media/co-sell-migrate/action-2-migrate.png)](media/co-sell-migrate/action-2-migrate.png#lightbox)

1. **Selecione Criar uma oferta de projeto** para migrar a sua solução para o mercado comercial como um rascunho. Ainda não será ao vivo.
1. Continue o processo de publicação no Partner Center. Para obter assistência com a publicação no Partner Center, consulte [Faça a sua oferta ao vivo no mercado comercial abaixo.](#make-your-offer-live-in-the-marketplace)

### <a name="discard-this-solution"></a>Deite fora esta solução

Selecione esta opção quando uma solução em soluções OCP GTM já não é relevante. Ser-lhe-á pedido que confirme a devoluções, e também pode desfazê-la mais tarde.

1. **Selecione Eu quero descartar esta solução** (ver **Ação necessária** imagem [acima),](#beginmigration)em **seguida, Continuar**.
2. **Selecione Descartar**.

    :::image type="content" source="media/co-sell-migrate/migration-discard.png" alt-text="Confirme a devoluções.":::

3. Para desfazer a sua devoluções, selecione **Desfazer Descartar**.

    :::image type="content" source="media/co-sell-migrate/migration-discard-undo.png" alt-text="O link desfazer descartar.":::

4. Se precisar de ajuda adicional, selecione o **separador Obter Ajuda** para contactar a equipa de apoio do parceiro.

    :::image type="content" source="media/co-sell-migrate/get-support-link.png" alt-text="O link de suporte no separador Obter Ajuda.":::

## <a name="make-your-offer-live-in-the-marketplace"></a>Faça a sua oferta ao vivo no mercado

Se selecionou para migrar a sua oferta para o mercado comercial, chegará como um rascunho. Ainda precisa publicar a sua oferta para a tornar viva no mercado comercial; isto manterá o seu estatuto de co-venda, incentivos e gasoduto de encaminhamento.

Para obter instruções detalhadas sobre as informações que necessita de fornecer antes de a sua oferta poder ser publicada, leia o [guia de publicação](publisher-guide-by-offer-type.md)apropriado. Para um resumo, leia abaixo.

1. Na página **'Vista Geral'** no Partner Center, selecione o nome da oferta do seu projeto.

    :::image type="content" source="media/co-sell-migrate/offer-overview.png" alt-text="A página geral da oferta no Partner Center.":::

<a name="cosell-tab"></a>

2. Preencha as informações necessárias em cada separador. Opcionalmente, complete a página de Revenda através de **CSPs** (no menu de navegação à esquerda abaixo) para revender através do programa Cloud Solution Provider (CSP). O **Learn mais** links e ferramentas irão guiá-lo através dos requisitos e detalhes.

    :::image type="content" source="media/co-sell-migrate/offer-setup.png" alt-text="Os separadores de configuração da oferta no Partner Center.":::

3. Alguns dos detalhes voltados para o vendedor da Microsoft foram copiados da solução OCP GTM. Preencha as restantes informações necessárias no separador **Co-venda com** o separador Microsoft para tornar a sua oferta co-venda pronta. Quando estiver concluído, **selecione 'Rever' e publicar**.

    :::image type="content" source="media/co-sell-migrate/co-sell-page.png" alt-text="A oferta co-vende o separador no Partner Center.":::

4. Depois de rever todas as informações submetidas, selecione **Publicar** para submeter a sua oferta de projeto para revisão de certificação.

    :::image type="content" source="media/co-sell-migrate/co-sell-review-publish.png" alt-text="O separador de revisão de ofertas e o botão publicar no Partner Center.":::

5. Acompanhe o estado da sua submissão no **separador 'Vista Geral'.**

    :::image type="content" source="media/publish-status-publisher-signoff.png" alt-text="A oferta publica a barra de estado no separador Overview no Partner Center.":::

6. Iremos notificá-lo quando a nossa avaliação de certificação estiver completa. Se fornecermos feedback acionável, endereçá-lo e, em seguida, selecionar **Publicar** para iniciar uma recertificação.
7. Assim que a sua oferta passar pela certificação, faça a pré-visualização da oferta com o link fornecido e faça quaisquer ajustes finais que possa desejar. Quando estiver pronto, selecione **Go live** (ver botão acima) para publicar a sua oferta para lojas de mercado comerciais relevantes. Uma vez ao vivo, a sua oferta manterá o seu estatuto de co-venda a partir da solução original do OCP GTM.

## <a name="next-steps"></a>Passos seguintes

- [Revenda através de Parceiros CSP](cloud-solution-providers.md)
- Ver estas [PERGUNTAS Frequentes](https://partner.microsoft.com/resources/detail/co-sell-requirements-publish-commercial-marketplace-faq-pdf) (PDF)
