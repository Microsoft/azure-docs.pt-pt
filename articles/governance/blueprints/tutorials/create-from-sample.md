---
title: Criar um ambiente a partir de um exemplo de Blueprint
description: Use um exemplo Blueprint para criar uma definição de plano gráfico que configura dois grupos de recursos e configura uma atribuição de função para cada.
author: DCtheGeek
ms.author: dacoulte
ms.date: 03/05/2019
ms.topic: tutorial
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: 137764cba91ab94adef4719a0d34b6fb9e6efe29
ms.sourcegitcommit: 2aefdf92db8950ff02c94d8b0535bf4096021b11
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/03/2019
ms.locfileid: "70232626"
---
# <a name="tutorial-create-an-environment-from-a-blueprint-sample"></a>Tutorial: Criar um ambiente a partir de um exemplo de Blueprint

Os planos gráficos de exemplo fornecem exemplos do que pode ser feito usando plantas do Azure. Cada um é um exemplo com uma intenção ou finalidade específica, mas não cria um ambiente completo por si só. Cada um é destinado a um local de partida para explorar o uso de plantas do Azure com várias combinações de artefatos, designs e parâmetros incluídos.

O tutorial a seguir usa os **grupos de recursos com** a amostra do esquema do RBAC para demonstrar diferentes aspectos do serviço de plantas. As etapas a seguir são abordadas:

> [!div class="checklist"]
> - Criar uma nova definição de Blueprint a partir do exemplo
> - Marque sua cópia do exemplo como **publicado**
> - Atribuir sua cópia do plano gráfico a uma assinatura existente
> - Inspecionar recursos implantados para a atribuição
> - Cancelar a atribuição do plano gráfico para remover os bloqueios

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, é necessária uma assinatura do Azure. Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="create-blueprint-definition-from-sample"></a>Criar definição de plano gráfico do exemplo

Primeiro, implemente o exemplo Blueprint. A importação cria uma nova especificação técnica em seu ambiente com base no exemplo.

1. Selecione **todos os serviços** no painel esquerdo. Pesquise e selecione **plantas**.

1. Na página **Guia de introdução** à esquerda, selecione o botão **criar** em _criar um plano gráfico_.

1. Encontre os **grupos de recursos com** a amostra do Blueprint do RBAC em _outros exemplos_ e selecione **usar este exemplo**.

1. Insira os _conceitos básicos_ do exemplo Blueprint:

   - **Nome do plano gráfico**: Forneça um nome para a sua cópia do exemplo Blueprint. Para este tutorial, usaremos o nome _dois-RGS-with-role-assignments_.
   - **Local da definição**: Use as reticências e selecione o grupo de gerenciamento ou a assinatura para salvar sua cópia do exemplo.

1. Selecione a guia artefatos na parte superior da página ou **em Avançar: Artefatos** na parte inferior da página.

1. Examine a lista de artefatos que compõem o exemplo Blueprint. Este exemplo define dois grupos de recursos, com nomes de exibição de _ProdRG_ e _PreProdRG_. O nome final e o local de cada grupo de recursos são definidos durante a atribuição do Blueprint. O grupo de recursos _ProdRG_ é atribuído à função _colaborador_ e o grupo de recursos _PreProdRG_ recebe as funções _proprietário_ e _leitores_ . As funções atribuídas na definição são estáticas, mas o usuário, aplicativo ou grupo ao qual a função é atribuída é definido durante a atribuição do Blueprint.

1. Selecione **salvar rascunho** ao concluir a revisão do exemplo Blueprint.

Esta etapa cria uma cópia do exemplo de definição de Blueprint no grupo de gerenciamento ou na assinatura selecionada. A definição de Blueprint salva é gerenciada como qualquer plano gráfico criado do zero. Você pode salvar o exemplo em seu grupo de gerenciamento ou assinatura quantas vezes forem necessárias. No entanto, cada cópia deve fornecer um nome exclusivo.

Depois que a notificação do portal de **salvamento de definição de planta bem-sucedida** for exibida, vá para a próxima etapa.

## <a name="publish-the-sample-copy"></a>Publicar a cópia de exemplo

Sua cópia do exemplo Blueprint agora foi criada em seu ambiente. Ele é criado no modo de **rascunho** e deve ser **publicado** antes que possa ser atribuído e implantado. A cópia do exemplo de Blueprint pode ser personalizada para seu ambiente e necessidades. Para este tutorial, não faremos nenhuma alteração.

1. Selecione **todos os serviços** no painel esquerdo. Pesquise e selecione **plantas**.

1. Selecione a página **definições de plantas** à esquerda. Use os filtros para localizar a definição de planta de _dois-RGS-com-role-assignments_ e, em seguida, selecione-a.

1. Selecione **publicar Blueprint** na parte superior da página. No novo painel à direita, forneça a **versão** como _1,0_ para sua cópia do exemplo de plano gráfico. Essa propriedade será útil se você fizer uma modificação posteriormente. Forneça **observações de alteração** , como "primeira versão publicada a partir dos grupos de recursos com a amostra de plantas do RBAC". Em seguida, selecione **publicar** na parte inferior da página.

Essa etapa torna possível atribuir o plano gráfico a uma assinatura. Depois de publicadas, as alterações ainda podem ser feitas. Alterações adicionais exigem a publicação com um novo valor de **versão** para acompanhar as diferenças entre diferentes versões da mesma definição de Blueprint.

Depois que a notificação de **definição de planta de publicação com êxito** do portal for exibida, vá para a próxima etapa.

## <a name="assign-the-sample-copy"></a>Atribuir a cópia de exemplo

Depois que a cópia do exemplo Blueprint tiver sido **publicada**com êxito, ela poderá ser atribuída a uma assinatura dentro do grupo de gerenciamento no qual foi salva. Esta etapa é onde os parâmetros são fornecidos para fazer com que cada implantação da cópia do exemplo de plano gráfico seja exclusiva.

1. Selecione **todos os serviços** no painel esquerdo. Pesquise e selecione **plantas**.

1. Selecione a página **definições de plantas** à esquerda. Use os filtros para localizar a definição de planta de _dois-RGS-com-role-assignments_ e, em seguida, selecione-a.

1. Selecione **atribuir plano gráfico** na parte superior da página de definição do Blueprint.

1. Forneça os valores de parâmetro para a atribuição Blueprint:

   - Noções Básicas

     - **Subscrições**: Selecione uma ou mais das assinaturas que estão no grupo de gerenciamento em que você salvou sua cópia do exemplo Blueprint. Se você selecionar mais de uma assinatura, uma atribuição será criada para cada uma usando os parâmetros inseridos.
     - **Nome da atribuição**: O nome é preenchido previamente para você com base no nome da definição do Blueprint.
     - **Local**: Selecione uma região na qual a identidade gerenciada deve ser criada. O Azure Blueprint utiliza esta identidade gerida para implementar todos os artefactos no esquema atribuído. Para saber mais, veja [identidades geridas dos recursos do Azure](../../../active-directory/managed-identities-azure-resources/overview.md).
       Para este tutorial, selecione _leste dos EUA 2_.
     - **Versão de definição do Blueprint**: Escolha a versão publicada _1,0_ da sua cópia da definição do Blueprint de exemplo.

   - Atribuição de Bloqueio

     Selecione o modo de bloqueio de Blueprint _somente leitura_ . Para obter mais informações, veja [bloqueio de recurso em esquemas](../concepts/resource-locking.md).

   - Identidade Gerida

     Deixe a opção padrão _atribuído pelo sistema_ . Para obter mais informações, [](../../../active-directory/managed-identities-azure-resources/overview.md)consulte identidades gerenciadas.

   - Parâmetros de artefacto

     Os parâmetros definidos nesta seção se aplicam ao artefato sob o qual ele é definido. Esses parâmetros são [parâmetros dinâmicos](../concepts/parameters.md#dynamic-parameters) , pois eles são definidos durante a atribuição do plano gráfico. Para cada artefato, defina o valor do parâmetro como o que é definido na coluna **valor** . Para `{Your ID}`, selecione sua conta de usuário do Azure.

     |Nome do artefacto|Tipo de artefacto|Nome do parâmetro|Value|Descrição|
     |-|-|-|-|-|
     |Grupo de recursos ProdRG|Resource group|Name|ProductionRG|Define o nome do primeiro grupo de recursos.|
     |Grupo de recursos ProdRG|Resource group|Location|EUA Oeste 2|Define o local do primeiro grupo de recursos.|
     |Contribuinte|Atribuição de função|Usuário ou grupo|{Your ID}|Define qual usuário ou grupo deve conceder a atribuição de função de _colaborador_ dentro do primeiro grupo de recursos.|
     |Grupo de recursos PreProdRG|Resource group|Name|PreProductionRG|Define o nome do segundo grupo de recursos.|
     |Grupo de recursos PreProdRG|Resource group|Location|EUA Oeste|Define o local do segundo grupo de recursos.|
     |Owner|Atribuição de função|Usuário ou grupo|{Your ID}|Define qual usuário ou grupo deve conceder a atribuição de função de _proprietário_ dentro do segundo grupo de recursos.|
     |Leitores|Atribuição de função|Usuário ou grupo|{Your ID}|Define qual usuário ou grupo deve conceder a atribuição de função de _leitores_ dentro do segundo grupo de recursos.|

1. Depois que todos os parâmetros forem inseridos, selecione **atribuir** na parte inferior da página.

Esta etapa implanta os recursos definidos e configura a **atribuição de bloqueio**selecionada. Os bloqueios do Blueprint podem levar até 30 minutos para serem aplicados.

Depois que a notificação **atribuição de plano gráfico com êxito** do portal for exibida, vá para a próxima etapa.

## <a name="inspect-resources-deployed-by-the-assignment"></a>Inspecionar os recursos implantados pela atribuição

A atribuição Blueprint cria e controla os artefatos definidos na definição do Blueprint. Podemos ver o status dos recursos na página de atribuição do Blueprint e examinando os recursos diretamente.

1. Selecione **todos os serviços** no painel esquerdo. Pesquise e selecione **plantas**.

1. Selecione a página **plantas atribuídas** à esquerda. Use os filtros para localizar a atribuição de plano de _atribuição-dois-RGS-com-role-assignments_ e, em seguida, selecione-a.

   Nessa página, podemos ver a atribuição bem-sucedida e a lista de recursos criados junto com seu estado de bloqueio Blueprint. Se a atribuição for atualizada, a lista suspensa **operação de atribuição** mostrará detalhes sobre a implantação de cada versão de definição. Cada recurso listado que foi criado pode ser clicado e abre essa página de propriedades de recursos.

1. Selecione o grupo de recursos **ProductionRG** .

   Vemos que o nome do grupo de recursos é **ProductionRG** e não o nome de exibição do artefato _ProdRG_. Esse nome corresponde ao valor definido durante a atribuição de Blueprint.

1. Selecione a página **controle de acesso (iam)** à esquerda e, em seguida, a guia atribuições de **função** .

   Aqui, vemos que sua conta recebeu a função _colaborador_ no escopo deste _recurso_. A atribuição de plano de _atribuição-dois-RGS-com-atribuições de função_ tem a função _proprietário_ como foi usada para criar o grupo de recursos. Essas permissões também são usadas para gerenciar recursos com bloqueios de Blueprint configurados.

1. Na barra de navegação portal do Azure, selecione **atribuição-dois-RGS-com-atribuições de função** para voltar uma página e, em seguida, selecione o grupo de recursos **PreProductionRG** .

1. Selecione a página **controle de acesso (iam)** à esquerda e, em seguida, a guia atribuições de **função** .

   Aqui, vemos que sua conta recebeu as funções de _proprietário_ e de _leitor_ , ambas no escopo deste _recurso_. A atribuição Blueprint também tem a função de _proprietário_ como o primeiro grupo de recursos.

1. Selecione a guia atribuições de negação.

   A atribuição de Blueprint criou uma [atribuição](../../../role-based-access-control/deny-assignments.md) de negação no grupo de recursos implantado para impor o modo de bloqueio de Blueprint _somente leitura_ . A atribuição Deny impede que alguém com direitos apropriados na guia atribuições de _função_ faça ações específicas. A atribuição de negação afeta _todas as entidades de segurança_.

1. Selecione a atribuição negar e selecione a página **permissões negadas** à esquerda.

   A atribuição de negação está impedindo todas **\*** as operações com a configuração de **ação** e, mas permite acesso de leitura, excluindo  **\*/Read** por meio de **ações**.

1. No portal do Azure navegação estrutural, selecione **iam (PreProductionRG-Access Control)** . Em seguida, selecione a página **visão geral** à esquerda e, em seguida, o botão **excluir grupo de recursos** . Insira o nome _PreProductionRG_ para confirmar a exclusão e selecione **excluir** na parte inferior do painel.

   A notificação do portal **excluir grupo de recursos PreProductionRG falhou** é exibida. O erro informa que, embora sua conta tenha permissão para excluir o grupo de recursos, o acesso é negado pela atribuição Blueprint. Lembre-se de que selecionamos o modo de bloqueio de Blueprint _somente leitura_ durante a atribuição de Blueprint. O bloqueio Blueprint impede que uma conta com permissão, mesmo _proprietário_, exclua o recurso. Para obter mais informações, veja [bloqueio de recurso em esquemas](../concepts/resource-locking.md).

Essas etapas mostram que nossos recursos foram criados conforme definido e os bloqueios de plantas impediram a exclusão indesejada, mesmo de uma conta com permissão.

## <a name="unassign-the-blueprint"></a>Cancelar a atribuição do plano gráfico

A última etapa é remover a atribuição do plano gráfico e os recursos implantados.
A remoção da atribuição não remove os artefatos implantados.

1. Selecione **todos os serviços** no painel esquerdo. Pesquise e selecione **plantas**.

1. Selecione a página **plantas atribuídas** à esquerda. Use os filtros para localizar a atribuição de plano de _atribuição-dois-RGS-com-role-assignments_ e, em seguida, selecione-a.

1. Selecione o botão **desatribuir Blueprint** na parte superior da página. Leia o aviso na caixa de diálogo de confirmação e selecione **OK**.

   Com a atribuição Blueprint removida, os bloqueios do Blueprint também são removidos. Os recursos criados podem ser novamente excluídos por uma conta com permissões.

1. Selecione **grupos de recursos** no menu do Azure e, em seguida, selecione **ProductionRG**.

1. Selecione a página **controle de acesso (iam)** à esquerda e, em seguida, a guia atribuições de **função** .

A segurança de cada grupo de recursos ainda tem as atribuições de função implantadas, mas a atribuição de Blueprint não tem mais acesso de _proprietário_ .

Depois que a notificação do portal de **remoção de atribuição de plano gráfico for bem-sucedida** aparecer, vá para a próxima etapa.

## <a name="clean-up-resources"></a>Limpar recursos

Ao concluir este tutorial, exclua os seguintes recursos:

- _ProductionRG_ do grupo de recursos
- _PreProductionRG_ do grupo de recursos
- Definição de Blueprint _dois-RGS_ com atribuições de função

## <a name="next-steps"></a>Passos Seguintes

- Saiba mais sobre o [ciclo de vida do Blueprint](../concepts/lifecycle.md)
- Compreender como utilizar [parâmetros estáticos e dinâmicos](../concepts/parameters.md)
- Saber como utilizar o [bloqueio de recursos de esquema](../concepts/resource-locking.md)
- Aprender a personalizar a [ordem de sequenciação do esquema](../concepts/sequencing-order.md)
- Saber como [atualizar as atribuições existentes](../how-to/update-existing-assignments.md)
- Resolver problemas durante a atribuição de um esquema com [resolução de problemas gerais](../troubleshoot/general.md)