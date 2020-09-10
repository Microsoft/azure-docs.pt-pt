---
title: Descrição geral do exemplo de esquema da Referência de Segurança do Azure
description: Descrição geral do exemplo de esquema da Referência de Segurança do Azure. Este exemplo de esquema ajuda os clientes a avaliar controlos específicos.
ms.date: 06/02/2020
ms.topic: sample
ms.openlocfilehash: 4113f350c21087dbda5f69d9c3981e2d169ffc00
ms.sourcegitcommit: 3be3537ead3388a6810410dfbfe19fc210f89fec
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/10/2020
ms.locfileid: "89651901"
---
# <a name="azure-security-benchmark-blueprint-sample"></a>Amostra de projeto de benchmark de segurança Azure

O exemplo de esquema da Referência de Segurança do Azure disponibiliza proteções de governação com o [Azure Policy](../../policy/overview.md) que ajudam a avaliar controlos específicos da [Referência de Segurança do Azure](../../../security/benchmarks/overview.md). Este esquema ajuda os clientes a implementar um conjunto essencial de políticas para qualquer arquitetura implementada no Azure na qual queiram implementar controlos da Referência de Segurança do Azure.

## <a name="control-mapping"></a>Mapeamento de controlo

O [mapeamento de controlo de política Azure](../../policy/samples/azure-security-benchmark.md) fornece detalhes sobre as definições de política incluídas neste projeto e como estas definições de política mapeiam para os **domínios** de conformidade e **controlos** no Benchmark de Segurança Azure. Quando atribuídos a uma arquitetura, os recursos são avaliados pela Azure Policy para o incumprimento das definições políticas atribuídas. Para obter mais informações, veja [Azure Policy](../../policy/overview.md).

## <a name="deploy"></a>Implementar

Para implantar a amostra de projeto Azure Security Benchmark, devem ser tomadas as seguintes medidas:

> [!div class="checklist"]
> - Criar uma nova planta a partir da amostra
> - Marque a sua cópia da amostra como **Publicada**
> - Atribua a sua cópia da planta a uma subscrição existente

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free) antes de começar.

### <a name="create-blueprint-from-sample"></a>Criar planta a partir de amostra

Em primeiro lugar, implemente a amostra de planta criando uma nova planta no seu ambiente usando a amostra como entrada.

1. Selecione **Todos os serviços** no painel esquerdo. Procure e selecione **Plantas.**

1. A partir da página **'Iniciar'** à esquerda, selecione o botão **Criar** _uma plantação_.

1. Encontre a amostra de projeto **Azure Security Benchmark** em _Outras Amostras_ e selecione o nome para selecionar esta amostra.

1. Introduza os _fundamentos_ da amostra da planta:

   - **Nome da planta**: Forneça um nome para a sua cópia da amostra de projeto Azure Security Benchmark.
   - **Localização de definição**: Utilize a elipse e selecione o grupo de gestão para guardar a sua cópia da amostra para.

1. Selecione o _separador Artefactos_ na parte superior da página ou **seguinte: Artefactos** na parte inferior da página.

1. Reveja a lista de artefactos que compõem a amostra de planta. Muitos dos artefactos têm parâmetros que definiremos mais tarde. **Selecione Save Draft** quando terminar de rever a amostra de planta.

### <a name="publish-the-sample-copy"></a>Publique a cópia da amostra

A sua cópia da amostra foi agora criada no seu ambiente. É criado no modo **Draft** e deve ser **publicado** antes de poder ser atribuído e implementado. A cópia da amostra de planta pode ser personalizada para o seu ambiente e necessidades, mas essa modificação pode afastá-la do alinhamento com as recomendações do Azure Security Benchmark.

1. Selecione **Todos os serviços** no painel esquerdo. Procure e selecione **Plantas.**

1. Selecione a página **de definições de Planta** à esquerda. Utilize os filtros para encontrar a sua cópia da amostra de planta e, em seguida, selecione-a.

1. **Selecione Publicar** a planta no topo da página. Na nova página à direita, forneça uma **versão** para a sua cópia da amostra de planta. Esta propriedade é útil para se você fizer uma modificação mais tarde. Fornecer **notas de alteração** como "Primeira versão publicada a partir da amostra de projeto de benchmark de segurança Azure." Em seguida, **selecione Publicar** na parte inferior da página.

### <a name="assign-the-sample-copy"></a>Atribuir a cópia da amostra

Uma vez publicada com **sucesso**a cópia da amostra de plantas, pode ser atribuída a uma subscrição dentro do grupo de gestão a que foi guardada. Este passo é onde são fornecidos parâmetros para tornar cada implantação da cópia da amostra de planta única.

1. Selecione **Todos os serviços** no painel esquerdo. Procure e selecione **Plantas.**

1. Selecione a página **de definições de Planta** à esquerda. Utilize os filtros para encontrar a sua cópia da amostra de planta e, em seguida, selecione-a.

1. Selecione Atribuir a **planta** no topo da página de definição de planta.

1. Fornecer os valores dos parâmetros para a atribuição do projeto:

   - Noções básicas

     - **Subscrições**: Selecione uma ou mais das subscrições que estão no grupo de gestão a que guardou a sua cópia da amostra de planta. Se selecionar mais de uma subscrição, será criada uma atribuição para cada um utilizando os parâmetros introduzidos.
     - **Nome da atribuição**: O nome é pré-povoado para si com base no nome da planta.
       Mude o que for necessário ou saia como for.
     - **Localização**: Selecione uma região para a identidade gerida a criar. O Azure Blueprint utiliza esta identidade gerida para implementar todos os artefactos no esquema atribuído. Para saber mais, consulte [identidades geridas para recursos Azure.](../../../active-directory/managed-identities-azure-resources/overview.md)
     - **Versão de definição de planta**: Escolha uma versão **publicada** da sua cópia da amostra de planta.

   - Atribuição de bloqueio

     Selecione a definição de bloqueio de planta para o seu ambiente. Para obter mais informações, veja [bloqueio de recurso em esquemas](../concepts/resource-locking.md).

   - Identidade Gerida

     Deixe a opção de identidade gerida _por defeito atribuída._

   - Parâmetros de artefactos

     Os parâmetros definidos nesta secção aplicam-se ao artefacto sob o qual é definido. Estes parâmetros são [parâmetros dinâmicos,](../concepts/parameters.md#dynamic-parameters) uma vez que são definidos durante a atribuição da planta. Para obter uma lista completa ou parâmetros de artefactos e suas descrições, consulte [a tabela de parâmetros do Artefacto](#artifact-parameters-table).

1. Uma vez introduzidos todos os parâmetros, **selecione Atribuir** na parte inferior da página. A atribuição da planta é criada e a implantação de artefactos começa. A implantação leva cerca de uma hora. Para verificar o estado de implantação, abra a atribuição do projeto.

> [!WARNING]
> O serviço Azure Blueprints e as amostras de plantas incorporadas estão **livres de custos.** Os recursos azure são [avaliados pelo produto.](https://azure.microsoft.com/pricing/) Utilize a [calculadora de preços](https://azure.microsoft.com/pricing/calculator/) para estimar o custo de funcionamento dos recursos utilizados por esta amostra de planta.

### <a name="artifact-parameters-table"></a>Tabela de parâmetros de artefacto

A tabela a seguir fornece uma lista dos parâmetros do artefacto da planta:

|Nome do artefacto|Tipo de artefacto|Nome do parâmetro|Descrição|
|-|-|-|-|
|Auditar recomendações de Benchmark de Segurança Azure e implementar extensões específicas de suporte VM|Atribuição de políticas|Lista de utilizadores excluídos do grupo de administradores do Windows VM|Uma lista separada de membros que deve ser excluída no grupo local de administradores. Ex: Administrador; myUser1; myUser2|
|Auditar recomendações de Benchmark de Segurança Azure e implementar extensões específicas de suporte VM|Atribuição de políticas|Lista de utilizadores que devem ser incluídos no grupo de administradores do Windows VM|Uma lista separada de membros que deve ser incluída no grupo local de administradores. Ex: Administrador; myUser1; myUser2|
|Auditar recomendações de Benchmark de Segurança Azure e implementar extensões específicas de suporte VM|Atribuição de políticas|Lista de utilizadores que o grupo de administradores do Windows VM deve incluir *apenas*|Uma lista separada de todos os membros esperados do grupo local administradores. Ex: Administrador; myUser1; myUser2|
|Auditar recomendações de Benchmark de Segurança Azure e implementar extensões específicas de suporte VM|Atribuição de políticas|Lista de regiões onde o Observador de Rede deve ser ativado|Para ver uma lista completa de regiões use Get-AzLocation|
|Auditar recomendações de Benchmark de Segurança Azure e implementar extensões específicas de suporte VM|Atribuição de políticas|Rede virtual onde os VMs devem ser conectados|Exemplo: /subscrições/YourSubscriptionId/resourceGroups/YourResourceGroupName/providers/Microsoft.Network/virtualNetworks/Name|
|Auditar recomendações de Benchmark de Segurança Azure e implementar extensões específicas de suporte VM|Atribuição de políticas|Gateway de rede que as redes virtuais devem usar|Exemplo: /subscrições/YourSubscriptionId/resourceGroups/YourResourceGroup/providers/Microsoft.Network/virtualNetworkGateways/Name|
|Auditar recomendações de Benchmark de Segurança Azure e implementar extensões específicas de suporte VM|Atribuição de políticas|Lista de IDs do espaço de trabalho onde os agentes do Log Analytics devem ligar|Uma lista separada de pontos de trabalho com os IDs do espaço de trabalho a que o agente Log Analytics deve ser ligado|
|Auditar recomendações de Benchmark de Segurança Azure e implementar extensões específicas de suporte VM|Atribuição de políticas|Lista de tipos de recursos que devem ter registos de diagnóstico ativados|Definição de diagnóstico de auditoria para tipos de recursos selecionados|
|Auditar recomendações de Benchmark de Segurança Azure e implementar extensões específicas de suporte VM|Atribuição de políticas|Versão PHP mais recente|Versão PHP mais recente suportada para Serviços de Aplicações|
|Auditar recomendações de Benchmark de Segurança Azure e implementar extensões específicas de suporte VM|Atribuição de políticas|Última versão java|Versão java suportada mais recente para Serviços de Aplicações|
|Auditar recomendações de Benchmark de Segurança Azure e implementar extensões específicas de suporte VM|Atribuição de políticas|Versão mais recente do Windows Python|Versão python suportada mais recente para Serviços de Aplicações|
|Auditar recomendações de Benchmark de Segurança Azure e implementar extensões específicas de suporte VM|Atribuição de políticas|Última versão Linux Python|Versão python suportada mais recente para Serviços de Aplicações|

## <a name="next-steps"></a>Próximos passos

Artigos adicionais sobre esquemas e como os utilizar:

- Saiba mais sobre o [ciclo de vida do esquema](../concepts/lifecycle.md).
- Compreenda como utilizar [parâmetros estáticos e dinâmicos](../concepts/parameters.md).
- Aprenda a personalizar a [ordem de sequenciação do esquema](../concepts/sequencing-order.md).
- Saiba como utilizar o [bloqueio de recursos de esquema](../concepts/resource-locking.md).
- Saiba como [atualizar as atribuições existentes](../how-to/update-existing-assignments.md).