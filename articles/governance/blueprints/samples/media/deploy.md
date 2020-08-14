---
title: Implementar amostra de planta de mídia
description: Implementar etapas para a amostra de planta media, incluindo detalhes do parâmetro do artefacto da planta.
ms.date: 08/13/2020
ms.topic: sample
ms.openlocfilehash: c8ac1726bc5007c1790f5efbc45c72f9712fcdf2
ms.sourcegitcommit: 4913da04fd0f3cf7710ec08d0c1867b62c2effe7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/14/2020
ms.locfileid: "88208440"
---
# <a name="deploy-the-media-blueprint-sample"></a>Implementar a amostra de planta mídia

Para implantar a amostra de planta mídia, devem ser tomadas as seguintes medidas:

> [!div class="checklist"]
> - Criar uma nova planta a partir da amostra
> - Marque a sua cópia da amostra como **Publicada**
> - Atribua a sua cópia da planta a uma subscrição existente

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free) antes de começar.

## <a name="create-blueprint-from-sample"></a>Criar planta a partir de amostra

Em primeiro lugar, implemente a amostra de planta criando uma nova planta no seu ambiente usando a amostra como entrada.

1. Selecione **Todos os serviços** e procure e selecione **Política** no painel esquerdo. Na página **'Política',** selecione **Plantas**.

1. A partir da página **'Iniciar'** à esquerda, selecione o botão **Criar** _uma plantação_.

1. Encontre a amostra de planta **media** em _outras amostras_ e selecione **Utilize esta amostra**.

1. Introduza os _fundamentos_ da amostra da planta:

   - **Nome da planta**: Forneça um nome para a sua cópia da amostra de planta.
   - **Localização de definição**: Utilize a elipse e selecione o grupo de gestão para guardar a sua cópia da amostra para.

1. Selecione o _separador Artefactos_ na parte superior da página ou **seguinte: Artefactos** na parte inferior da página.

1. Reveja a lista de artefactos que compõem a amostra de planta. Muitos dos artefactos têm parâmetros que definiremos mais tarde. **Selecione Save Draft** quando terminar de rever a amostra de planta.

## <a name="publish-the-sample-copy"></a>Publique a cópia da amostra

A sua cópia da amostra foi agora criada no seu ambiente. É criado no modo **Draft** e deve ser **publicado** antes de poder ser atribuído e implementado. A cópia da amostra de planta pode ser personalizada para o seu ambiente e necessidades, mas essa modificação pode afastá-la do padrão.

1. Selecione **Todos os serviços** e procure e selecione **Política** no painel esquerdo. Na página **'Política',** selecione **Plantas**.

1. Selecione a página **de definições de Planta** à esquerda. Utilize os filtros para encontrar a sua cópia da amostra de planta e, em seguida, selecione-a.

1. **Selecione Publicar** a planta no topo da página. Na nova página à direita, forneça uma **versão** para a sua cópia da amostra de planta. Esta propriedade é útil para se você fizer uma modificação mais tarde. Fornecer **notas de alteração** como "Primeira versão publicada a partir da amostra de planta media." Em seguida, **selecione Publicar** na parte inferior da página.

## <a name="assign-the-sample-copy"></a>Atribuir a cópia da amostra

Uma vez publicada com **sucesso**a cópia da amostra de plantas, pode ser atribuída a uma subscrição dentro do grupo de gestão a que foi guardada. Este passo é onde são fornecidos parâmetros para tornar cada implantação da cópia da amostra de planta única.

1. Selecione **Todos os serviços** e procure e selecione **Política** no painel esquerdo. Na página **'Política',** selecione **Plantas**.

1. Selecione a página **de definições de Planta** à esquerda. Utilize os filtros para encontrar a sua cópia da amostra de planta e, em seguida, selecione-a.

1. Selecione Atribuir a **planta** no topo da página de definição de planta.

1. Fornecer os valores dos parâmetros para a atribuição do projeto:

   - Informações básicas

     - **Subscrições**: Selecione uma ou mais das subscrições que estão no grupo de gestão a que guardou a sua cópia da amostra de planta. Se selecionar mais de uma subscrição, será criada uma atribuição para cada um utilizando os parâmetros introduzidos.
     - **Nome da atribuição**: O nome é pré-povoado para si com base no nome da planta.
       Mude o que for necessário ou saia como for.
     - **Localização**: Selecione uma região para a identidade gerida a criar. O Azure Blueprint utiliza esta identidade gerida para implementar todos os artefactos no esquema atribuído. Para saber mais, consulte [identidades geridas para recursos Azure.](../../../../active-directory/managed-identities-azure-resources/overview.md)
     - **Versão de definição de planta**: Escolha uma versão **publicada** da sua cópia da amostra de planta.

   - Atribuição de bloqueio

     Selecione a definição de bloqueio de planta para o seu ambiente. Para obter mais informações, veja [bloqueio de recurso em esquemas](../../concepts/resource-locking.md).

   - Identidade Gerida

     Deixe a opção de identidade gerida _por defeito atribuída._

   - Parâmetros de artefactos

     Os parâmetros definidos nesta secção aplicam-se ao artefacto sob o qual é definido. Estes parâmetros são [parâmetros dinâmicos,](../../concepts/parameters.md#dynamic-parameters) uma vez que são definidos durante a atribuição da planta. Para obter uma lista completa ou parâmetros de artefactos e suas descrições, consulte [a tabela de parâmetros do Artefacto](#artifact-parameters-table).

1. Uma vez introduzidos todos os parâmetros, **selecione Atribuir** na parte inferior da página. A atribuição da planta é criada e a implantação de artefactos começa. A implantação leva cerca de uma hora. Para verificar o estado de implantação, abra a atribuição do projeto.

> [!WARNING]
> O serviço Azure Blueprints e as amostras de plantas incorporadas estão **livres de custos.** Os recursos azure são [avaliados pelo produto.](https://azure.microsoft.com/pricing/) Utilize a [calculadora de preços](https://azure.microsoft.com/pricing/calculator/) para estimar o custo de funcionamento dos recursos utilizados por esta amostra de planta.

## <a name="artifact-parameters-table"></a>Tabela de parâmetros de artefacto

A tabela a seguir fornece uma lista dos parâmetros do artefacto da planta:

Nome do artefacto|Tipo de artefacto|Nome do parâmetro|Descrição|
|-|-|-|-|
|\[Pré-visualização \] : Implementar agente de análise de registo para VMs Linux |Atribuição de políticas |Log Analytics espaço de trabalho para VMs Linux |Para obter mais informações, consulte [Criar um espaço de trabalho log Analytics no portal Azure](../../../../azure-monitor/learn/quick-create-workspace.md). |
|\[Pré-visualização \] : Implementar agente de análise de registo para VMs Linux |Atribuição de políticas |Opcional: Lista de imagens VM que apoiaram o Linux OS para adicionar ao âmbito |Pode ser utilizado um conjunto vazio para indicar que não há parâmetros opcionais: `[]` |
|\[Pré-visualização \] : Implementar agente de análise de registo para VMs do Windows |Atribuição de políticas |Opcional: Lista de imagens VM que apoiaram o Windows OS para adicionar ao âmbito |Pode ser utilizado um conjunto vazio para indicar que não há parâmetros opcionais: `[]` |
|\[Pré-visualização \] : Implementar agente de análise de registo para VMs do Windows |Atribuição de políticas |Log Analytics espaço de trabalho para VMs windows |Para obter mais informações, consulte [Criar um espaço de trabalho log Analytics no portal Azure](../../../../azure-monitor/learn/quick-create-workspace.md). |
|\[Pré-visualização \] : Audit Media controls e implementar extensões VM específicas para suportar requisitos de auditoria |Atribuição de políticas |Log Analytics workspace ID que VMs deve ser configurado para |Este é o ID (GUID) do espaço de trabalho Log Analytics para o qual os VMs devem ser configurados. |
|\[Pré-visualização \] : Audit Media controls e implementar extensões VM específicas para suportar requisitos de auditoria |Atribuição de políticas |Lista de tipos de recursos que devem ter registos de diagnóstico ativados |Lista de tipos de recursos para auditar se a definição de registo de diagnóstico não estiver ativada. Os valores aceitáveis podem ser encontrados nos [esquemas de registos de diagnóstico do Azure Monitor](../../../../azure-monitor/platform/resource-logs-schema.md#service-specific-schemas). |
|\[Pré-visualização \] : Audit Media controls e implementar extensões VM específicas para suportar requisitos de auditoria |Atribuição de políticas |Grupo de administradores |Grupo. Exemplo: `Administrator; myUser1; myUser2` |
|\[Pré-visualização \] : Audit Media controls e implementar extensões VM específicas para suportar requisitos de auditoria |Atribuição de políticas |Lista de utilizadores que devem ser incluídos no grupo de administradores do Windows VM |Uma lista separada de membros que deve ser incluída no grupo local de administradores. Exemplo: `Administrator; myUser1; myUser2` |
|Implementar proteção avançada de ameaças em contas de armazenamento |Atribuição de políticas |Efeito |Informações sobre os efeitos da política podem ser encontradas na [Understand Azure Policy Effects](../../../policy/concepts/effects.md). |
|Implementar auditorias em servidores SQL |Atribuição de políticas |O valor em dias do período de retenção (0 indica retenção ilimitada) |Dias de retenção (opcional, _180_ dias se não especificado) |
|Implementar auditorias em servidores SQL |Atribuição de políticas |Nome do grupo de recursos para conta de armazenamento para auditoria de servidor SQL |A auditoria escreve eventos de base de dados para um registo de auditoria na sua conta de Armazenamento Azure (é criada uma conta de armazenamento em cada região onde é criado um SQL Server que é partilhado por todos os servidores dessa região). Importante - para um bom funcionamento da Auditoria não apague nem mude o nome do grupo de recursos ou das contas de armazenamento. |
|Implementar definições de diagnóstico para grupos de segurança de rede |Atribuição de políticas |Prefixo de conta de armazenamento para diagnósticos de grupos de segurança de rede |Este prefixo é combinado com a localização do grupo de segurança da rede para formar o nome da conta de armazenamento criada. |
|Implementar definições de diagnóstico para grupos de segurança de rede |Atribuição de políticas |Nome do grupo de recursos para conta de armazenamento para diagnósticos de grupos de segurança de rede (deve existir) |O grupo de recursos em que a conta de armazenamento é criada. Este grupo de recursos já deve existir. |

## <a name="next-steps"></a>Passos seguintes

Agora que reviu os passos para implementar a amostra de Media, visite os seguintes artigos para saber mais sobre a visão geral e o mapeamento de controlo:

> [!div class="nextstepaction"]
> [Plantas mediáticas - Visão geral](./index.md) 
>  [Plantas de mídia - Controlo de mapeamento](./control-mapping.md)

Artigos adicionais sobre esquemas e como os utilizar:

- Saiba mais sobre o [ciclo de vida do esquema](../../concepts/lifecycle.md).
- Compreenda como utilizar [parâmetros estáticos e dinâmicos](../../concepts/parameters.md).
- Aprenda a personalizar a [ordem de sequenciação do esquema](../../concepts/sequencing-order.md).
- Saiba como utilizar o [bloqueio de recursos de esquema](../../concepts/resource-locking.md).
- Saiba como [atualizar as atribuições existentes](../../how-to/update-existing-assignments.md).
