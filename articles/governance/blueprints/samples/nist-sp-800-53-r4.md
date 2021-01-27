---
title: Descrição geral do exemplo de esquema NIST SP 800-53 R4
description: Descrição geral do exemplo de esquema NIST SP 800-53 R4. Este exemplo de esquema ajuda os clientes a avaliar controlos NIST SP 800-53 R4 específicos.
ms.date: 01/27/2021
ms.topic: sample
ms.openlocfilehash: b773013c61a8bed3c5c5f4e8b24878f10889f954
ms.sourcegitcommit: 436518116963bd7e81e0217e246c80a9808dc88c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/27/2021
ms.locfileid: "98918132"
---
# <a name="nist-sp-800-53-r4-blueprint-sample"></a>Amostra de planta NIST SP 800-53 R4

O exemplo de esquema NIST SP 800-53 R4 fornece proteções de governação com o [Azure Policy](../../policy/overview.md) que o ajudam a avaliar controlos de NIST SP 800-53 R4 específicos. Este esquema ajuda os clientes a implementar um conjunto essencial de políticas para qualquer arquitetura implementada no Azure que tenham de implementar controlos de NIST SP 800-53 R4.

## <a name="control-mapping"></a>Mapeamento de controlo

O [mapeamento de controlo de política Azure](../../policy/samples/nist-sp-800-53-r4.md) fornece detalhes sobre as definições de política incluídas neste projeto e como estas definições de política mapeiam para os **domínios** de conformidade e **controlos** no NIST SP 800-53 R4. Quando atribuídos a uma arquitetura, os recursos são avaliados pela Azure Policy para o incumprimento das definições políticas atribuídas. Para obter mais informações, veja [Azure Policy](../../policy/overview.md).

## <a name="deploy"></a>Implementar

Para implantar a amostra de planta Azure Blueprints NIST SP 800-53 R4, devem ser tomadas as seguintes medidas:

> [!div class="checklist"]
> - Criar um esquema novo a partir do exemplo
> - Marcar a cópia do exemplo como **Publicada**
> - Atribuir a cópia do esquema a uma subscrição já existente

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free) antes de começar.

### <a name="create-blueprint-from-sample"></a>Criar planta a partir de amostra

Primeiro, crie um esquema novo no ambiente utilizando o exemplo como ponto de partida, para implementar o esquema de exemplo.

1. Selecione **Todos os serviços** no painel esquerdo. Procure e selecione **Esquemas**.

1. Na página **Começar** à esquerda, selecione o botão **Criar** em _Criar um esquema_.

1. Encontre a amostra de planta **NIST SP 800-53 R4** em _Outras Amostras_ e selecione **Utilize esta amostra**.

1. Introduza as _Informações Básicas_ do esquema de exemplo:

   - **Nome da planta**: Forneça um nome para a sua cópia da amostra de planta NIST SP 800-53 R4.
   - **Localização de definição**: Utilize a elipse e selecione o grupo de gestão para guardar a sua cópia da amostra para.

1. Selecione o separador _Artefactos_, na parte superior da página, ou **Seguinte: Artefactos**, na parte inferior.

1. Reveja a lista de artefactos que compõem o esquema de exemplo. Muitos dos artefactos têm parâmetros que vamos definir mais tarde. Quando terminar de rever o esquema de exemplo, selecione **Guardar Rascunho**.

### <a name="publish-the-sample-copy"></a>Publicar a cópia do exemplo

A cópia do esquema de exemplo está agora criada no seu ambiente. Está criada no modo **Rascunho** e tem de ser **Publicada** antes de poder ser atribuída e implementada. A cópia da amostra de planta pode ser personalizada para o seu ambiente e necessidades, mas essa modificação pode movê-la para longe do alinhamento com os controlos NIST SP 800-53.

1. Selecione **Todos os serviços** no painel esquerdo. Procure e selecione **Esquemas**.

1. Selecione a página **Definições de esquema** à esquerda. Utilize os filtros para localizar a cópia do esquema de exemplo e selecione-a.

1. Selecione **Publicar esquema**, na parte superior da página. Na página nova à direita, indique uma **Versão** para a cópia do esquema de exemplo. Esta propriedade é útil se fizer modificações mais tarde. Fornecer **notas de alteração** como "Primeira versão publicada a partir da amostra de planta NIST SP 800-53 R4." Em seguida, selecione **Publicar**, na parte inferior da página.

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
|\[Pré-visualização \] : Auditoria NIST SP 800-53 R4 controla e implementa extensões VM específicas para apoiar os requisitos de auditoria|Atribuição de políticas|Log Analytics workspace ID que VMs deve ser configurado para|Este é o ID (GUID) do espaço de trabalho Log Analytics para o qual os VMs devem ser configurados.|
|\[Pré-visualização \] : Auditoria NIST SP 800-53 R4 controla e implementa extensões VM específicas para apoiar os requisitos de auditoria|Atribuição de políticas|Lista de tipos de recursos que devem ter registos de diagnóstico ativados|Lista de tipos de recursos para auditar se a definição de registo de diagnóstico não estiver ativada. Os valores aceitáveis podem ser encontrados nos [esquemas de registos de diagnóstico do Azure Monitor](../../../azure-monitor/platform/resource-logs-schema.md#service-specific-schemas).|
|\[Pré-visualização \] : Auditoria NIST SP 800-53 R4 controla e implementa extensões VM específicas para apoiar os requisitos de auditoria|Atribuição de políticas|Lista de utilizadores que devem ser excluídos do grupo de administradores do Windows VM|Uma lista separada de membros que deve ser excluída no grupo local de administradores. Ex: Administrador; myUser1; myUser2|
|\[Pré-visualização \] : Auditoria NIST SP 800-53 R4 controla e implementa extensões VM específicas para apoiar os requisitos de auditoria|Atribuição de políticas|Lista de utilizadores que devem ser incluídos no grupo de administradores do Windows VM|Uma lista separada de membros que deve ser incluída no grupo local de administradores. Ex: Administrador; myUser1; myUser2|
|\[Pré-visualização \] : Implementar o agente de análise de registo para conjuntos de escalas De VM Linux (VMSS)|Atribuição de políticas|Log Analytics espaço de trabalho para conjuntos de escala de VM Linux (VMSS)|Se este espaço de trabalho estiver fora do âmbito da atribuição, deve conceder manualmente permissões de "Log Analytics Contributor" (ou similar) ao ID principal da atribuição de políticas.|
|\[Pré-visualização \] : Implementar o agente de análise de registo para conjuntos de escalas De VM Linux (VMSS)|Atribuição de políticas|Opcional: Lista de imagens VM que apoiaram o Linux OS para adicionar ao âmbito|Pode ser utilizado um conjunto vazio para indicar que não há parâmetros opcionais: \[\]|
|\[Pré-visualização \] : Implementar agente de análise de registo para VMs Linux|Atribuição de políticas|Log Analytics espaço de trabalho para VMs Linux|Se este espaço de trabalho estiver fora do âmbito da atribuição, deve conceder manualmente permissões de "Log Analytics Contributor" (ou similar) ao ID principal da atribuição de políticas.|
|\[Pré-visualização \] : Implementar agente de análise de registo para VMs Linux|Atribuição de políticas|Opcional: Lista de imagens VM que apoiaram o Linux OS para adicionar ao âmbito|Pode ser utilizado um conjunto vazio para indicar que não há parâmetros opcionais: \[\]|
|\[Pré-visualização \] : Implementar o agente de análise de registo para conjuntos de escala vm do Windows (VMSS)|Atribuição de políticas|Log Analytics espaço de trabalho para conjuntos de escala vm do Windows (VMSS)|Se este espaço de trabalho estiver fora do âmbito da atribuição, deve conceder manualmente permissões de "Log Analytics Contributor" (ou similar) ao ID principal da atribuição de políticas.|
|\[Pré-visualização \] : Implementar o agente de análise de registo para conjuntos de escala vm do Windows (VMSS)|Atribuição de políticas|Opcional: Lista de imagens VM que apoiaram o Windows OS para adicionar ao âmbito|Pode ser utilizado um conjunto vazio para indicar que não há parâmetros opcionais: \[\]|
|\[Pré-visualização \] : Implementar agente de análise de registo para VMs do Windows|Atribuição de políticas|Log Analytics espaço de trabalho para VMs windows|Se este espaço de trabalho estiver fora do âmbito da atribuição, deve conceder manualmente permissões de "Log Analytics Contributor" (ou similar) ao ID principal da atribuição de políticas.|
|\[Pré-visualização \] : Implementar agente de análise de registo para VMs do Windows|Atribuição de políticas|Opcional: Lista de imagens VM que apoiaram o Windows OS para adicionar ao âmbito|Pode ser utilizado um conjunto vazio para indicar que não há parâmetros opcionais: \[\]|
|Implementar proteção avançada de ameaças em contas de armazenamento|Atribuição de políticas|Efeito|Informações sobre efeitos políticos podem ser encontradas na [Understand Azure Policy Effects](../../policy/concepts/effects.md)|
|Implementar auditorias em servidores SQL|Atribuição de políticas|O valor em dias do período de retenção (0 indica retenção ilimitada)|Dias de retenção (opcional, 180 dias se não especificado)|
|Implementar auditorias em servidores SQL|Atribuição de políticas|Nome do grupo de recursos para conta de armazenamento para auditoria de servidor SQL|A auditoria escreve eventos de base de dados para um registo de auditoria na sua conta de Armazenamento Azure (será criada uma conta de armazenamento em cada região onde é criado um SQL Server que será partilhado por todos os servidores dessa região). Importante - para um bom funcionamento da Auditoria não apague nem mude o nome do grupo de recursos ou das contas de armazenamento.|
|Implementar definições de diagnóstico para grupos de segurança de rede|Atribuição de políticas|Prefixo de conta de armazenamento para diagnósticos de grupos de segurança de rede|Este prefixo será combinado com a localização do grupo de segurança da rede para formar o nome da conta de armazenamento criada.|
|Implementar definições de diagnóstico para grupos de segurança de rede|Atribuição de políticas|Nome do grupo de recursos para conta de armazenamento para diagnósticos de grupos de segurança de rede (deve existir)|O grupo de recursos em que a conta de armazenamento será criada. Este grupo de recursos já deve existir.|

## <a name="next-steps"></a>Próximos passos

Artigos adicionais sobre esquemas e como os utilizar:

- Saiba mais sobre o [ciclo de vida do esquema](../concepts/lifecycle.md).
- Compreenda como utilizar [parâmetros estáticos e dinâmicos](../concepts/parameters.md).
- Aprenda a personalizar a [ordem de sequenciação do esquema](../concepts/sequencing-order.md).
- Saiba como utilizar o [bloqueio de recursos de esquema](../concepts/resource-locking.md).
- Saiba como [atualizar as atribuições existentes](../how-to/update-existing-assignments.md).
