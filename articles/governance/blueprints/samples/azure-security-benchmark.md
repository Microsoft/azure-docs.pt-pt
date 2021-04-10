---
title: Descrição geral do exemplo de esquema da Referência de Segurança do Azure
description: Descrição geral do exemplo de esquema da Referência de Segurança do Azure. Este exemplo de esquema ajuda os clientes a avaliar controlos específicos.
ms.date: 01/27/2021
ms.topic: sample
ms.openlocfilehash: 38a50970999965b6a86b8ce8882006c169f2dc5a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98919243"
---
# <a name="azure-security-benchmark-blueprint-sample"></a>Amostra de projeto de benchmark de segurança Azure

A amostra de projeto Azure Security Benchmark fornece guarda-carris de governação usando [a Política Azure](../../policy/overview.md) que o ajudam a avaliar controlos específicos [de benchmark de segurança azure v1.](../../../security/benchmarks/overview.md) Este esquema ajuda os clientes a implementar um conjunto essencial de políticas para qualquer arquitetura implementada no Azure na qual queiram implementar controlos da Referência de Segurança do Azure.

## <a name="control-mapping"></a>Mapeamento de controlo

O [mapeamento de controlo de política Azure](../../policy/samples/azure-security-benchmark.md) fornece detalhes sobre as definições de política incluídas neste projeto e como estas definições de política mapeiam para os **domínios** de conformidade e **controlos** no Benchmark de Segurança Azure. Quando atribuídos a uma arquitetura, os recursos são avaliados pela Azure Policy para o incumprimento das definições políticas atribuídas. Para obter mais informações, veja [Azure Policy](../../policy/overview.md).

## <a name="deploy"></a>Implementar

Para implantar a amostra de projeto Azure Security Benchmark, devem ser tomadas as seguintes medidas:

> [!div class="checklist"]
> - Criar um esquema novo a partir do exemplo
> - Marcar a cópia do exemplo como **Publicada**
> - Atribuir a cópia do esquema a uma subscrição já existente

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free) antes de começar.

### <a name="create-blueprint-from-sample"></a>Criar planta a partir de amostra

Primeiro, crie um esquema novo no ambiente utilizando o exemplo como ponto de partida, para implementar o esquema de exemplo.

1. Selecione **Todos os serviços** no painel esquerdo. Procure e selecione **Esquemas**.

1. Na página **Começar** à esquerda, selecione o botão **Criar** em _Criar um esquema_.

1. Encontre a amostra de **projeto Azure Security Benchmark v1** em _Outras Amostras_ e selecione o nome para selecionar esta amostra.

1. Introduza as _Informações Básicas_ do esquema de exemplo:

   - **Nome da planta**: Forneça um nome para a sua cópia da amostra de projeto Azure Security Benchmark.
   - **Localização de definição**: Utilize a elipse e selecione o grupo de gestão para guardar a sua cópia da amostra para.

1. Selecione o separador _Artefactos_, na parte superior da página, ou **Seguinte: Artefactos**, na parte inferior.

1. Reveja a lista de artefactos incluídos na amostra de planta. Muitos dos artefactos têm parâmetros que vamos definir mais tarde. Quando terminar de rever o esquema de exemplo, selecione **Guardar Rascunho**.

### <a name="publish-the-sample-copy"></a>Publicar a cópia do exemplo

A cópia do esquema de exemplo está agora criada no seu ambiente. Está criada no modo **Rascunho** e tem de ser **Publicada** antes de poder ser atribuída e implementada. A cópia da amostra de planta pode ser personalizada para o seu ambiente e necessidades, mas essa modificação pode afastá-la do alinhamento com as recomendações do Azure Security Benchmark.

1. Selecione **Todos os serviços** no painel esquerdo. Procure e selecione **Esquemas**.

1. Selecione a página **Definições de esquema** à esquerda. Utilize os filtros para localizar a cópia do esquema de exemplo e selecione-a.

1. Selecione **Publicar esquema**, na parte superior da página. Na página nova à direita, indique uma **Versão** para a cópia do esquema de exemplo. Esta propriedade é útil se fizer modificações mais tarde. Fornecer **notas de alteração** como "Primeira versão publicada a partir da amostra de projeto de benchmark de segurança Azure." Em seguida, selecione **Publicar**, na parte inferior da página.

### <a name="assign-the-sample-copy"></a>Atribuir a cópia de exemplo

Uma vez publicada com **sucesso** a cópia da amostra de plantas, pode ser atribuída a uma subscrição dentro do grupo de gestão a que foi guardada. É neste passo que são fornecidos os parâmetros que fazem com que cada implementação da cópia do esquema de exemplo seja única.

1. Selecione **Todos os serviços** no painel esquerdo. Procure e selecione **Esquemas**.

1. Selecione a página **Definições de esquema** à esquerda. Utilize os filtros para localizar a cópia do esquema de exemplo e selecione-a.

1. Selecione **Atribuir esquema**, na parte superior da página de definição do esquema.

1. Indique os valores dos parâmetros para a atribuição do esquema:

   - Noções básicas

     - **Subscrições**: Selecione uma ou mais das subscrições que estão no grupo de gestão a que guardou a sua cópia da amostra de planta. Se selecionar mais de uma subscrição, é criada uma atribuição para cada uma mediante a utilização dos parâmetros introduzidos.
     - **Nome da atribuição**: O nome é pré-povoado para si com base no nome da planta.
       Mude-o se necessário ou deixe-o como está.
     - **Localização**: Selecione uma região para a identidade gerida a criar. O Azure Blueprint utiliza esta identidade gerida para implementar todos os artefactos no esquema atribuído. Para saber mais, consulte [identidades geridas para recursos Azure.](../../../active-directory/managed-identities-azure-resources/overview.md)
     - **Versão de definição de planta**: Escolha uma versão **publicada** da sua cópia da amostra de planta.

   - Atribuição de bloqueio

     Selecione a definição de bloqueio do esquema no seu ambiente. Para obter mais informações, veja [bloqueio de recurso em esquemas](../concepts/resource-locking.md).

   - Identidade Gerida

     Deixe a opção de identidade gerida _por defeito atribuída._

   - Parâmetros dos artefactos

     Os parâmetros definidos nesta secção aplicam-se ao artefacto no qual são definidos. Estes parâmetros são [parâmetros dinâmicos,](../concepts/parameters.md#dynamic-parameters) uma vez que são definidos durante a atribuição da planta. Para obter uma lista completa ou parâmetros de artefactos e suas descrições, consulte [a tabela de parâmetros do Artefacto](#artifact-parameters-table).

1. Depois de introduzidos todos os parâmetros, selecione **Atribuir**, na parte inferior da página. A atribuição do esquema é criada e a implementação do artefacto inicia-se. A implantação leva cerca de uma hora. Para verificar o estado, abra a atribuição do esquema.

> [!WARNING]
> O serviço Azure Blueprints e os esquemas de exemplo incorporados são **gratuitos**. Os preços dos recursos do Azure são os [preços por produto](https://azure.microsoft.com/pricing/). Utilize a [calculadora de preços](https://azure.microsoft.com/pricing/calculator/) para prever o custo da execução de recursos implementados por este esquema de exemplo.

### <a name="artifact-parameters-table"></a>Tabela de parâmetros dos artefactos

A tabela seguinte mostra uma lista dos parâmetros dos artefactos de esquema:

|Nome do artefacto|Tipo de artefacto|Nome do parâmetro|Descrição|
|-|-|-|-|
|Auditar recomendações de Benchmark de Segurança Azure e implementar extensões específicas de suporte VM|Atribuição de políticas|Lista de utilizadores excluídos do grupo de administradores do Windows VM|Uma lista separada de membros que deve ser excluída no grupo local de administradores. Ex: Administrador; myUser1; myUser2|
|Auditar recomendações de Benchmark de Segurança Azure e implementar extensões específicas de suporte VM|Atribuição de políticas|Lista de utilizadores que devem ser incluídos no grupo de administradores do Windows VM|Uma lista separada de membros que deve ser incluída no grupo local de administradores. Ex: Administrador; myUser1; myUser2|
|Auditar recomendações de Benchmark de Segurança Azure e implementar extensões específicas de suporte VM|Atribuição de políticas|Lista de utilizadores que o grupo de administradores do Windows VM deve incluir *apenas*|Uma lista separada de todos os membros esperados do grupo local administradores. Ex: Administrador; myUser1; myUser2|
|Auditar recomendações de Benchmark de Segurança Azure e implementar extensões específicas de suporte VM|Atribuição de políticas|Lista de regiões onde o Observador de Rede deve ser ativado|Para ver uma lista completa de regiões usam Get-AzLocation|
|Auditar recomendações de Benchmark de Segurança Azure e implementar extensões específicas de suporte VM|Atribuição de políticas|Rede virtual onde os VMs devem ser conectados|Exemplo: /subscrições/YourSubscriptionId/resourceGroups/YourResourceGroupName/providers/Microsoft.Network/virtualNetworks/Name|
|Auditar recomendações de Benchmark de Segurança Azure e implementar extensões específicas de suporte VM|Atribuição de políticas|Gateway de rede que as redes virtuais devem usar|Exemplo: /subscrições/YourSubscriptionId/resourceGroups/YourResourceGroup/providers/Microsoft.Network/virtualNetworkGateways/Name|
|Auditar recomendações de Benchmark de Segurança Azure e implementar extensões específicas de suporte VM|Atribuição de políticas|Lista de IDs do espaço de trabalho onde os agentes do Log Analytics devem ligar|Uma lista separada de pontos de trabalho com os IDs do espaço de trabalho a que o agente Log Analytics deve ser ligado|
|Auditar recomendações de Benchmark de Segurança Azure e implementar extensões específicas de suporte VM|Atribuição de políticas|Lista de tipos de recursos que devem ter registos de diagnóstico ativados|Definição de diagnóstico de auditoria para tipos de recursos selecionados|
|Auditar recomendações de Benchmark de Segurança Azure e implementar extensões específicas de suporte VM|Atribuição de políticas|Versão PHP mais recente|Versão PHP mais recente suportada para Serviços de Aplicações|
|Auditar recomendações de Benchmark de Segurança Azure e implementar extensões específicas de suporte VM|Atribuição de políticas|Última versão java|Versão java suportada mais recente para Serviços de Aplicações|
|Auditar recomendações de Benchmark de Segurança Azure e implementar extensões específicas de suporte VM|Atribuição de políticas|Versão mais recente do Windows Python|Versão python suportada mais recente para Serviços de Aplicações|
|Auditar recomendações de Benchmark de Segurança Azure e implementar extensões específicas de suporte VM|Atribuição de políticas|Última versão Linux Python|Versão python suportada mais recente para Serviços de Aplicações|

## <a name="next-steps"></a>Passos seguintes

Artigos adicionais sobre esquemas e como os utilizar:

- Saiba mais sobre o [ciclo de vida do esquema](../concepts/lifecycle.md).
- Compreenda como utilizar [parâmetros estáticos e dinâmicos](../concepts/parameters.md).
- Aprenda a personalizar a [ordem de sequenciação do esquema](../concepts/sequencing-order.md).
- Saiba como utilizar o [bloqueio de recursos de esquema](../concepts/resource-locking.md).
- Saiba como [atualizar as atribuições existentes](../how-to/update-existing-assignments.md).