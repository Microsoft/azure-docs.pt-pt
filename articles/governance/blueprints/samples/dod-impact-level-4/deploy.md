---
title: Amostra de planta do DoD Impact Level 4
description: Implementar etapas para a amostra de planta do DoD Impact Level 4, incluindo detalhes do parâmetro do artefacto da planta.
ms.date: 06/30/2020
ms.topic: sample
ms.openlocfilehash: 2dc6c4ae70ce18a75f277495665ed5afdce2713c
ms.sourcegitcommit: a989fb89cc5172ddd825556e45359bac15893ab7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/01/2020
ms.locfileid: "85800414"
---
# <a name="deploy-the-dod-impact-level-4-blueprint-sample"></a>Implementar a amostra de planta do DoD Impact Level 4

Para implantar a amostra de planta do Azure Blueprints Department of Defense Impact Level 4 (DoD IL4), devem ser tomadas as seguintes medidas:

> [!div class="checklist"]
> - Criar uma nova planta a partir da amostra
> - Marque a sua cópia da amostra como **Publicada**
> - Atribua a sua cópia da planta a uma subscrição existente

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free) antes de começar.

## <a name="create-blueprint-from-sample"></a>Criar planta a partir de amostra

Em primeiro lugar, implemente a amostra de planta criando uma nova planta no seu ambiente usando a amostra como entrada.

1. Selecione **Todos os serviços** no painel esquerdo. Procure e selecione **Plantas.**

1. A partir da página **'Iniciar'** à esquerda, selecione o botão **Criar** _uma plantação_.

1. Encontre a amostra de planta **do DoD Impact Level 4** em _outras amostras_ e selecione **Utilize esta amostra**.

1. Introduza os _fundamentos_ da amostra da planta:

   - **Nome da planta**: Forneça um nome para a sua cópia da amostra de planta DoD Impact Level 4.
   - **Localização de definição**: Utilize a elipse e selecione o grupo de gestão para guardar a sua cópia da amostra para.

1. Selecione o _separador Artefactos_ na parte superior da página ou **seguinte: Artefactos** na parte inferior da página.

1. Reveja a lista de artefactos que compõem a amostra de planta. Muitos dos artefactos têm parâmetros que definiremos mais tarde. **Selecione Save Draft** quando terminar de rever a amostra de planta.

## <a name="publish-the-sample-copy"></a>Publique a cópia da amostra

A sua cópia da amostra foi agora criada no seu ambiente. É criado no modo **Draft** e deve ser **publicado** antes de poder ser atribuído e implementado. A cópia da amostra de planta pode ser personalizada para o seu ambiente e necessidades, mas essa modificação pode movê-la para longe do alinhamento com os controlos DoD Impact Level 4.

1. Selecione **Todos os serviços** no painel esquerdo. Procure e selecione **Plantas.**

1. Selecione a página **de definições de Planta** à esquerda. Utilize os filtros para encontrar a sua cópia da amostra de planta e, em seguida, selecione-a.

1. **Selecione Publicar** a planta no topo da página. Na nova página à direita, forneça uma **versão** para a sua cópia da amostra de planta. Esta propriedade é útil para se você fizer uma modificação mais tarde. Fornecer **notas de alteração** como "Primeira versão publicada a partir da amostra de planta DoD IL4." Em seguida, **selecione Publicar** na parte inferior da página.

## <a name="assign-the-sample-copy"></a>Atribuir a cópia da amostra

Uma vez publicada com **sucesso**a cópia da amostra de plantas, pode ser atribuída a uma subscrição dentro do grupo de gestão a que foi guardada. Este passo é onde são fornecidos parâmetros para tornar cada implantação da cópia da amostra de planta única.

1. Selecione **Todos os serviços** no painel esquerdo. Procure e selecione **Plantas.**

1. Selecione a página **de definições de Planta** à esquerda. Utilize os filtros para encontrar a sua cópia da amostra de planta e, em seguida, selecione-a.

1. Selecione Atribuir a **planta** no topo da página de definição de planta.

1. Fornecer os valores dos parâmetros para a atribuição do projeto:

   - Noções básicas

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

|Nome do artefacto|Tipo de artefacto|Nome do parâmetro|Description|
|-|-|-|-|
|Localizações permitidas|Atribuição de Política|Localizações Permitidas|Esta política permite-lhe restringir as localizações que a sua organização pode especificar ao implementar recursos. Utilize para impor os requisitos de conformidade geográfica.|
|Locais permitidos para grupos de recursos|Atribuição de Política |Localizações Permitidas|Esta política permite-lhe restringir as localizações em que a sua organização pode criar grupos de recursos. Utilize para impor os requisitos de conformidade geográfica.|
|Implementar auditorias em servidores SQL|Atribuição de política|O valor em dias do período de retenção (0 indica retenção ilimitada)|Dias de retenção (opcional, 180 dias se não especificado)|
|Implementar auditorias em servidores SQL|Atribuição de política|Nome do grupo de recursos para conta de armazenamento para auditoria de servidor SQL|A auditoria escreve eventos de base de dados para um registo de auditoria na sua conta de Armazenamento Azure (será criada uma conta de armazenamento em cada região onde é criado um SQL Server que será partilhado por todos os servidores dessa região). Importante - para um bom funcionamento da Auditoria não apague nem mude o nome do grupo de recursos ou das contas de armazenamento.|
|Implementar definições de diagnóstico para grupos de segurança de rede|Atribuição de política|Prefixo de conta de armazenamento para diagnósticos de grupos de segurança de rede|Este prefixo será combinado com a localização do grupo de segurança da rede para formar o nome da conta de armazenamento criada.|
|Implementar definições de diagnóstico para grupos de segurança de rede|Atribuição de política|Nome do grupo de recursos para conta de armazenamento para diagnósticos de grupos de segurança de rede (deve existir)|O grupo de recursos em que a conta de armazenamento será criada. Este grupo de recursos já deve existir.|
|Implementar o agente de análise de registo para conjuntos de escalas de VM Linux (VMSS)|Atribuição de política|Log Analytics espaço de trabalho para conjuntos de escala de VM Linux (VMSS)|Se este espaço de trabalho estiver fora do âmbito da atribuição, deve conceder manualmente permissões de "Log Analytics Contributor" (ou similar) ao ID principal da atribuição de políticas.|
|Implementar o agente de análise de registo para conjuntos de escalas de VM Linux (VMSS)|Atribuição de política|Opcional: Lista de imagens VM que apoiaram o Linux OS para adicionar ao âmbito|Pode ser utilizado um conjunto vazio para indicar que não há parâmetros opcionais:\[\]|
|Implementar agente de análise de registo para VMs Linux|Atribuição de política|Log Analytics espaço de trabalho para VMs Linux|Se este espaço de trabalho estiver fora do âmbito da atribuição, deve conceder manualmente permissões de "Log Analytics Contributor" (ou similar) ao ID principal da atribuição de políticas.|
|Implementar agente de análise de registo para VMs Linux|Atribuição de política|Opcional: Lista de imagens VM que apoiaram o Linux OS para adicionar ao âmbito|Pode ser utilizado um conjunto vazio para indicar que não há parâmetros opcionais:\[\]|
|Implementar o agente de análise de registo para conjuntos de escala vm do Windows (VMSS)|Atribuição de política|Log Analytics espaço de trabalho para conjuntos de escala vm do Windows (VMSS)|Se este espaço de trabalho estiver fora do âmbito da atribuição, deve conceder manualmente permissões de "Log Analytics Contributor" (ou similar) ao ID principal da atribuição de políticas.|
|Implementar o agente de análise de registo para conjuntos de escala vm do Windows (VMSS)|Atribuição de política|Opcional: Lista de imagens VM que apoiaram o Windows OS para adicionar ao âmbito|Pode ser utilizado um conjunto vazio para indicar que não há parâmetros opcionais:\[\]|
|Implementar agente de análise de registo para VMs do Windows|Atribuição de política|Log Analytics espaço de trabalho para VMs windows|Se este espaço de trabalho estiver fora do âmbito da atribuição, deve conceder manualmente permissões de "Log Analytics Contributor" (ou similar) ao ID principal da atribuição de políticas.|
|Implementar agente de análise de registo para VMs do Windows|Atribuição de política|Opcional: Lista de imagens VM que apoiaram o Windows OS para adicionar ao âmbito|Pode ser utilizado um conjunto vazio para indicar que não há parâmetros opcionais:\[\]|
|\[Pré-visualização \] : DoD Impact Level 4|Atribuição de política|Membros a serem incluídos no grupo local administradores|Uma lista separada de membros que deve ser excluída no grupo local de administradores. Ex: Administrador; myUser1; myUser2|
|\[Pré-visualização \] : DoD Impact Level 4|Atribuição de política|Membros que devem ser excluídos no grupo local de administradores|Uma lista separada de membros que deve ser incluída no grupo local de administradores. Ex: Administrador; myUser1; myUser2|
|\[Pré-visualização \] : DoD Impact Level 4|Atribuição de política|Lista de tipos de recursos que devem ter registos de diagnóstico ativados|Lista de tipos de recursos para auditar se a definição de registo de diagnóstico não estiver ativada. Os valores aceitáveis podem ser encontrados nos [esquemas de registos de diagnóstico do Azure Monitor](../../../../azure-monitor/platform/diagnostic-logs-schema.md#supported-log-categories-per-resource-type).|
|\[Pré-visualização \] : DoD Impact Level 4|Atribuição de política|Log Analytics workspace ID que VMs deve ser configurado para|Este é o ID (GUID) do espaço de trabalho Log Analytics para o qual os VMs devem ser configurados.|
|\[Pré-visualização \] : DoD Impact Level 4|Atribuição de política|Backup geo-redundante a longo prazo deve ser ativado para bases de dados Azure SQL|Informações sobre os efeitos da política podem ser encontradas na [Understand Azure Policy Effects](../../../policy/concepts/effects.md).|
|\[Pré-visualização \] : DoD Impact Level 4|Atribuição de política|A avaliação da vulnerabilidade deve ser ativada nas suas instâncias geridas pelo SQL|Informações sobre os efeitos da política podem ser encontradas na [Understand Azure Policy Effects](../../../policy/concepts/effects.md).|
|\[Pré-visualização \] : DoD Impact Level 4|Atribuição de política|A avaliação da vulnerabilidade deve ser ativada nos seus servidores SQL|Informações sobre os efeitos da política podem ser encontradas na [Understand Azure Policy Effects](../../../policy/concepts/effects.md).|
|\[Pré-visualização \] : DoD Impact Level 4|Atribuição de política|O armazenamento geo-redundante deve ser ativado para contas de armazenamento|Informações sobre os efeitos da política podem ser encontradas na [Understand Azure Policy Effects](../../../policy/concepts/effects.md).|
|\[Pré-visualização \] : DoD Impact Level 4|Atribuição de política|Backup geo-redundante deve ser ativado para Azure Database para MySQL|Informações sobre os efeitos da política podem ser encontradas na [Understand Azure Policy Effects](../../../policy/concepts/effects.md).|
|\[Pré-visualização \] : DoD Impact Level 4|Atribuição de política|A cópia de segurança geo-redundante deve ser ativada para a Base de Dados Azure para PostgreSQL|Informações sobre os efeitos da política podem ser encontradas na [Understand Azure Policy Effects](../../../policy/concepts/effects.md).|
|\[Pré-visualização \] : DoD Impact Level 4|Atribuição de política|A Aplicação Web só deve ser acessível em HTTPS|Informações sobre os efeitos da política podem ser encontradas na [Understand Azure Policy Effects](../../../policy/concepts/effects.md).|
|\[Pré-visualização \] : DoD Impact Level 4|Atribuição de política|A App de função só deve estar acessível através do HTTPS|Informações sobre os efeitos da política podem ser encontradas na [Understand Azure Policy Effects](../../../policy/concepts/effects.md).|
|\[Pré-visualização \] : DoD Impact Level 4|Atribuição de política|As contas externas com permissões de escrita devem ser removidas da sua subscrição|Informações sobre os efeitos da política podem ser encontradas na [Understand Azure Policy Effects](../../../policy/concepts/effects.md).|
|\[Pré-visualização \] : DoD Impact Level 4|Atribuição de política|As contas externas com permissões de leitura devem ser removidas da sua subscrição|Informações sobre os efeitos da política podem ser encontradas na [Understand Azure Policy Effects](../../../policy/concepts/effects.md).|
|\[Pré-visualização \] : DoD Impact Level 4|Atribuição de política|As contas externas com permissões do proprietário devem ser removidas da sua subscrição|Informações sobre os efeitos da política podem ser encontradas na [Understand Azure Policy Effects](../../../policy/concepts/effects.md).|
|\[Pré-visualização \] : DoD Impact Level 4|Atribuição de política|Contas pregridadas com permissões do proprietário devem ser removidas da sua subscrição|Informações sobre os efeitos da política podem ser encontradas na [Understand Azure Policy Effects](../../../policy/concepts/effects.md).|
|\[Pré-visualização \] : DoD Impact Level 4|Atribuição de política|As contas preprecadas devem ser removidas da sua subscrição|Informações sobre os efeitos da política podem ser encontradas na [Understand Azure Policy Effects](../../../policy/concepts/effects.md).|
|\[Pré-visualização \] : DoD Impact Level 4|Atribuição de política|O CORS não deve permitir que todos os recursos acedam à sua Aplicação Web|Informações sobre os efeitos da política podem ser encontradas na [Understand Azure Policy Effects](../../../policy/concepts/effects.md).|
|\[Pré-visualização \] : DoD Impact Level 4|Atribuição de política|As atualizações do sistema em conjuntos de escala de máquinas virtuais devem ser instaladas|Informações sobre os efeitos da política podem ser encontradas na [Understand Azure Policy Effects](../../../policy/concepts/effects.md).|
|\[Pré-visualização \] : DoD Impact Level 4|Atribuição de política|MFA deve ser ativado em contas com permissões de leitura na sua subscrição|Informações sobre os efeitos da política podem ser encontradas na [Understand Azure Policy Effects](../../../policy/concepts/effects.md).|
|\[Pré-visualização \] : DoD Impact Level 4|Atribuição de política|O MFA deve ser ativado em contas com permissões do proprietário na sua subscrição|Informações sobre os efeitos da política podem ser encontradas na [Understand Azure Policy Effects](../../../policy/concepts/effects.md).|
|\[Pré-visualização \] : DoD Impact Level 4|Atribuição de política|MFA deve ser ativado em contas com permissões de escrita na sua subscrição|As informações sobre os efeitos da política podem ser encontradas em [Compreender efeitos da política de Azure](.. /.. /.. /política/conceitos/efeitos.

## <a name="next-steps"></a>Passos seguintes

Agora que reviu os passos para implementar a amostra de planta do DoD Impact Level 4, visite os seguintes artigos para saber sobre a planta e o mapeamento de controlo:

> [!div class="nextstepaction"]
> [Projeto do DoD Impact Level 4 - Visão geral](./index.md) 
>  [Projeto do DoD Impact Level 4 - Controlo de mapeamento](./control-mapping.md)

Artigos adicionais sobre esquemas e como os utilizar:

- Saiba mais sobre o [ciclo de vida do esquema](../../concepts/lifecycle.md).
- Compreenda como utilizar [parâmetros estáticos e dinâmicos](../../concepts/parameters.md).
- Aprenda a personalizar a [ordem de sequenciação do esquema](../../concepts/sequencing-order.md).
- Saiba como utilizar o [bloqueio de recursos de esquema](../../concepts/resource-locking.md).
- Saiba como [atualizar as atribuições existentes](../../how-to/update-existing-assignments.md).