---
title: Implementar a amostra de plantas do FedRAMP High
description: Desloque os passos para a amostra de plantas do FedRAMP High, incluindo detalhes do parâmetro de artefacto de plantas.
ms.date: 01/31/2020
ms.topic: sample
ms.openlocfilehash: 91f208105a07770c557b1f273f3420c03b3d6dcb
ms.sourcegitcommit: cfbea479cc065c6343e10c8b5f09424e9809092e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/08/2020
ms.locfileid: "77087308"
---
# <a name="deploy-the-fedramp-high-blueprint-sample"></a>Implementar a amostra de plantas do FedRAMP High

Para implantar a amostra de plantas FedRAMP High, devem ser tomadas as seguintes medidas:

> [!div class="checklist"]
> - Criar uma nova planta a partir da amostra
> - Marque a sua cópia da amostra como **Publicado**
> - Atribuir a sua cópia da planta a uma subscrição existente

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free) antes de começar.

## <a name="create-blueprint-from-sample"></a>Criar a planta a partir da amostra

Em primeiro lugar, implemente a amostra de planta criando uma nova planta no seu ambiente usando a amostra como entrada.

1. Selecione **Todos os serviços** no painel esquerdo. Procure e selecione **Plantas**.

1. A partir da página **iniciar** à esquerda, selecione o botão **Criar** _uma planta_.

1. Encontre a amostra de planta **alta fedRAMP** em _outras amostras_ e selecione **Utilize esta amostra**.

1. Introduza os _Fundamentos_ da amostra de plantas:

   - **Nome**da planta : Forneça um nome para a sua cópia da amostra de plantas fedramp high.
   - **Localização da definição**: Utilize a elipse e selecione o grupo de gestão para guardar a sua cópia da amostra para.

1. Selecione o separador _Artefactos_ na parte superior da página ou **Seguinte: Artefactos** na parte inferior da página.

1. Reveja a lista de artefactos que compõem a amostra da planta. Muitos dos artefactos têm parâmetros que definiremos mais tarde. Selecione **Guardar Rascunho** quando terminar de rever a amostra de plantas.

## <a name="publish-the-sample-copy"></a>Publicar a cópia da amostra

A sua cópia da amostra de plantas foi agora criada no seu ambiente. É criado em modo **Draft** e deve ser **publicado** antes de ser atribuído e implantado. A cópia da amostra de plantas pode ser personalizada para o seu ambiente e necessidades, mas essa modificação pode afastá-la do alinhamento com os controlos FedRAMP High.

1. Selecione **Todos os serviços** no painel esquerdo. Procure e selecione **Plantas**.

1. Selecione a página de **definições** de Blueprint à esquerda. Utilize os filtros para encontrar a sua cópia da amostra de plantas e, em seguida, selecione-a.

1. Selecione **Publicar a planta** no topo da página. Na nova página à direita, forneça uma **Versão** para a sua cópia da amostra de plantas. Esta propriedade é útil para se fizer uma modificação mais tarde. Forneça notas de **Mudança** como "Primeira versão publicada a partir da amostra de plantas fedramp high." Em seguida, **selecione Publicar** na parte inferior da página.

## <a name="assign-the-sample-copy"></a>Atribuir a cópia da amostra

Uma vez que a cópia da amostra de plantas tenha sido **publicada**com sucesso, pode ser atribuída a uma subscrição dentro do grupo de gestão a que foi guardada. Este passo é onde são fornecidos parâmetros para tornar cada implantação da cópia da amostra de plantas única.

1. Selecione **Todos os serviços** no painel esquerdo. Procure e selecione **Plantas**.

1. Selecione a página de **definições** de Blueprint à esquerda. Utilize os filtros para encontrar a sua cópia da amostra de plantas e, em seguida, selecione-a.

1. Selecione **a planta de atribuição** na parte superior da página de definição de planta.

1. Fornecer os valores do parâmetro para a atribuição do projeto:

   - Noções básicas

     - **Assinaturas**: Selecione uma ou mais das subscrições que estão no grupo de gestão para a qual guardou a sua cópia da amostra de projeto. Se selecionar mais do que uma subscrição, será criada uma atribuição para cada utilização dos parâmetros introduzidos.
     - **Nome de atribuição**: O nome é pré-povoado para si com base no nome da planta.
       Mude conforme necessário ou saia como está.
     - **Localização**: Selecione uma região para a identidade gerida a criar. O Azure Blueprint utiliza esta identidade gerida para implementar todos os artefactos no esquema atribuído. Para saber mais, veja [identidades geridas dos recursos do Azure](../../../../active-directory/managed-identities-azure-resources/overview.md).
     - Versão de **definição**de planta : Escolha uma versão **publicada** da sua cópia da amostra de plantas.

   - Atribuição de bloqueio

     Selecione a definição de bloqueio da planta para o seu ambiente. Para obter mais informações, veja [bloqueio de recurso em esquemas](../../concepts/resource-locking.md).

   - Identidade Gerida

     Deixe o sistema predefinido _atribuído_ à opção de identidade gerida.

   - Parâmetros de artefacto

     Os parâmetros definidos nesta secção aplicam-se ao artefacto sob o qual é definido. Estes parâmetros são [parâmetros dinâmicos](../../concepts/parameters.md#dynamic-parameters) uma vez que são definidos durante a atribuição da planta. Para obter uma lista completa ou parâmetros de artefactos e suas descrições, consulte a [tabela de parâmetros do Artefacto](#artifact-parameters-table).

1. Uma vez introduzidos todos os parâmetros, **selecione Atribuir** na parte inferior da página. A atribuição da planta é criada e a implantação de artefactos começa. O destacamento demora cerca de uma hora. Para verificar o estado da implantação, abra a atribuição da planta.

> [!WARNING]
> O serviço Azure Blueprints e as amostras de plantas incorporadas estão **isentos de custos.** Os recursos azure são [avaliados pelo produto.](https://azure.microsoft.com/pricing/) Utilize a [calculadora](https://azure.microsoft.com/pricing/calculator/) de preços para estimar o custo dos recursos de funcionamento implantados por esta amostra de plantas.

## <a name="artifact-parameters-table"></a>Tabela de parâmetros de artefactos

A tabela seguinte fornece uma lista dos parâmetros do artefacto da planta:

|Nome do artefacto|Tipo de artefacto|Nome do parâmetro|Descrição|
|-|-|-|-|
|\[Preview\]: Audit FedRAMP High controls and implemente extensões vm específicas para apoiar requisitos de auditoria|Atribuição de política|Log Analytics workspace ID para que os VMs devem ser configurados para|Este é o ID (GUID) do espaço de trabalho Log Analytics para o que os VMs devem ser configurados.|
|\[Preview\]: Audit FedRAMP High controls and implemente extensões vm específicas para apoiar requisitos de auditoria|Atribuição de política|Lista de tipos de recursos que devem ter registos de diagnóstico ativados|Lista de tipos de recursos para auditar se a configuração do log de diagnóstico não estiver habilitada. Valores aceitáveis podem ser encontrados em [registos de diagnóstico do Monitor Azure.](../../../../azure-monitor/platform/diagnostic-logs-schema.md#supported-log-categories-per-resource-type)|
|\[Preview\]: Audit FedRAMP High controls and implemente extensões vm específicas para apoiar requisitos de auditoria|Atribuição de política|Lista de utilizadores que devem ser excluídos do grupo de Administradores VM do Windows|Uma lista separada do ponto-e-vírgula dos membros que deve ser excluída no grupo local dos administradores. Ex: Administrador; myUser1; myUser2|
|\[Preview\]: Audit FedRAMP High controls and implemente extensões vm específicas para apoiar requisitos de auditoria|Atribuição de política|Lista de utilizadores que devem ser incluídos no grupo de Administradores VM do Windows|Uma lista separada do ponto-e-vírgula dos membros que deve ser incluída no grupo local administradores. Ex: Administrador; myUser1; myUser2|
|\[pré-visualização\]: Implementar o agente de análise de registo para conjuntos de escala SM (VMSS)|Atribuição de política|Log Analytics espaço de trabalho para conjuntos de dimensionamento de VM do Linux (VMSS)|Se esse espaço de trabalho estiver fora do escopo da atribuição, você deverá conceder manualmente as permissões ' Log Analytics colaborador ' (ou semelhante) à ID principal da atribuição de política.|
|\[pré-visualização\]: Implementar o agente de análise de registo para conjuntos de escala SM (VMSS)|Atribuição de política|Opcional: Lista de imagens VM que apoiaram o Sistema Linux OS para adicionar ao âmbito|Uma matriz vazia pode ser utilizada para não indicar parâmetros opcionais: \[\]|
|\[pré-visualização\]: Implementar o agente de análise de registo para VMs Linux|Atribuição de política|Log Analytics espaço de trabalho para VMs Linux|Se esse espaço de trabalho estiver fora do escopo da atribuição, você deverá conceder manualmente as permissões ' Log Analytics colaborador ' (ou semelhante) à ID principal da atribuição de política.|
|\[pré-visualização\]: Implementar o agente de análise de registo para VMs Linux|Atribuição de política|Opcional: Lista de imagens VM que apoiaram o Sistema Linux OS para adicionar ao âmbito|Uma matriz vazia pode ser utilizada para não indicar parâmetros opcionais: \[\]|
|\[\]de pré-visualização: Implementar o agente de análise de registo para conjuntos de escala seletivas vM (VMSS)|Atribuição de política|Log Analytics espaço de trabalho para VMSS (conjuntos de dimensionamento de VMs do Windows)|Se esse espaço de trabalho estiver fora do escopo da atribuição, você deverá conceder manualmente as permissões ' Log Analytics colaborador ' (ou semelhante) à ID principal da atribuição de política.|
|\[\]de pré-visualização: Implementar o agente de análise de registo para conjuntos de escala seletivas vM (VMSS)|Atribuição de política|Opcional: Lista de imagens VM que têm suportado o Windows OS para adicionar ao âmbito|Uma matriz vazia pode ser utilizada para não indicar parâmetros opcionais: \[\]|
|\[\]de pré-visualização : Implementar o agente de análise de registo para VMs do Windows|Atribuição de política|Log Analytics espaço de trabalho para VMs do Windows|Se esse espaço de trabalho estiver fora do escopo da atribuição, você deverá conceder manualmente as permissões ' Log Analytics colaborador ' (ou semelhante) à ID principal da atribuição de política.|
|\[\]de pré-visualização : Implementar o agente de análise de registo para VMs do Windows|Atribuição de política|Opcional: Lista de imagens VM que têm suportado o Windows OS para adicionar ao âmbito|Uma matriz vazia pode ser utilizada para não indicar parâmetros opcionais: \[\]|
|Implementar proteção avançada de ameaças em contas de armazenamento|Atribuição de política|Efeito|Informações sobre os efeitos políticos podem ser encontradas na [Understand Azure Policy Effects](../../../policy/concepts/effects.md).|
|Implementar auditoria sql|Atribuição de política|O valor nos dias do período de retenção (0 indica retenção ilimitada)|Dias de retenção (opcional, 180 dias se não especificados)|
|Implementar auditoria sql|Atribuição de política|Nome do grupo de recursos para conta de armazenamento para auditoria de servidor SQL|A auditoria escreve eventos de base de dados para um registo de auditoria na sua conta de Armazenamento Azure (será criada uma conta de armazenamento em cada região onde é criado um Servidor SQL que será partilhado por todos os servidores dessa região). Importante - para o bom funcionamento da Auditoria não apague ou mude o nome do grupo de recursos ou das contas de armazenamento.|
|Implementar definições de diagnóstico para grupos de segurança da rede|Atribuição de política|Prefixo de conta de armazenamento para diagnósticos de grupo de segurança de rede|Este prefixo será combinado com a localização do grupo de segurança da rede para formar o nome da conta de armazenamento criada.|
|Implementar definições de diagnóstico para grupos de segurança da rede|Atribuição de política|Nome do grupo de recursos para conta de armazenamento para diagnósticos de grupo de segurança de rede (deve existir)|O grupo de recursos em que a conta de armazenamento será criada. Este grupo de recursos já deve existir.|
|\[Preview\]: Audit FedRAMP High controls and implemente extensões vm específicas para apoiar requisitos de auditoria|Atribuição de política|Locais permitidos para recursos e grupos de recursos|Lista de localizações do Azure que a sua organização pode especificar ao implementar recursos. Este valor fornecido também é utilizado pela política de "locais permitidos" no âmbito da iniciativa política.|
|\[Preview\]: Audit FedRAMP High controls and implemente extensões vm específicas para apoiar requisitos de auditoria|Atribuição de política|A avaliação da vulnerabilidade deve ser ativada nos seus casos geridos pela SQL|Informações sobre os efeitos políticos podem ser encontradas na [Understand Azure Policy Effects](../../../policy/concepts/effects.md).|
|\[Preview\]: Audit FedRAMP High controls and implemente extensões vm específicas para apoiar requisitos de auditoria|Atribuição de política|A avaliação da vulnerabilidade deve ser ativada nos seus servidores SQL|Informações sobre os efeitos políticos podem ser encontradas na [Understand Azure Policy Effects](../../../policy/concepts/effects.md).|
|\[Preview\]: Audit FedRAMP High controls and implemente extensões vm específicas para apoiar requisitos de auditoria|Atribuição de política|A avaliação da vulnerabilidade deve ser ativada em máquinas virtuais|Informações sobre os efeitos políticos podem ser encontradas na [Understand Azure Policy Effects](../../../policy/concepts/effects.md).|
|\[Preview\]: Audit FedRAMP High controls and implemente extensões vm específicas para apoiar requisitos de auditoria|Atribuição de política|Armazenamento geo-redundante deve ser ativado para contas de armazenamento|Informações sobre os efeitos políticos podem ser encontradas na [Understand Azure Policy Effects](../../../policy/concepts/effects.md).|
|\[Preview\]: Audit FedRAMP High controls and implemente extensões vm específicas para apoiar requisitos de auditoria|Atribuição de política|Backup geo-redundante deve ser ativado para base de dados Azure para MariaDB|Informações sobre os efeitos políticos podem ser encontradas na [Understand Azure Policy Effects](../../../policy/concepts/effects.md).|
|\[Preview\]: Audit FedRAMP High controls and implemente extensões vm específicas para apoiar requisitos de auditoria|Atribuição de política|Backup geo-redundante deve ser ativado para Base de Dados Azure para MySQL|Informações sobre os efeitos políticos podem ser encontradas na [Understand Azure Policy Effects](../../../policy/concepts/effects.md).|
|\[Preview\]: Audit FedRAMP High controls and implemente extensões vm específicas para apoiar requisitos de auditoria|Atribuição de política|Backup geo-redundante deve ser ativado para Base de Dados Azure para PostgreSQL|Informações sobre os efeitos políticos podem ser encontradas na [Understand Azure Policy Effects](../../../policy/concepts/effects.md).|
|\[Preview\]: Audit FedRAMP High controls and implemente extensões vm específicas para apoiar requisitos de auditoria|Atribuição de política|As regras do Grupo de Segurança da Rede para as máquinas virtuais viradas para a Internet devem ser endurecidas|Informações sobre os efeitos políticos podem ser encontradas na [Understand Azure Policy Effects](../../../policy/concepts/effects.md).|
|\[Preview\]: Audit FedRAMP High controls and implemente extensões vm específicas para apoiar requisitos de auditoria|Atribuição de política|Aplicação Web só deve estar acessível através de HTTPS|Informações sobre os efeitos políticos podem ser encontradas na [Understand Azure Policy Effects](../../../policy/concepts/effects.md).|
|\[Preview\]: Audit FedRAMP High controls and implemente extensões vm específicas para apoiar requisitos de auditoria|Atribuição de política|Função de aplicação só deve estar acessível através de HTTPS|Informações sobre os efeitos políticos podem ser encontradas na [Understand Azure Policy Effects](../../../policy/concepts/effects.md).|
|\[Preview\]: Audit FedRAMP High controls and implemente extensões vm específicas para apoiar requisitos de auditoria|Atribuição de política|As contas externas com permissões de escrita devem ser removidas da sua subscrição|Informações sobre os efeitos políticos podem ser encontradas na [Understand Azure Policy Effects](../../../policy/concepts/effects.md).|
|\[Preview\]: Audit FedRAMP High controls and implemente extensões vm específicas para apoiar requisitos de auditoria|Atribuição de política|As contas externas com permissões de leitura devem ser removidas da sua subscrição|Informações sobre os efeitos políticos podem ser encontradas na [Understand Azure Policy Effects](../../../policy/concepts/effects.md).|
|\[Preview\]: Audit FedRAMP High controls and implemente extensões vm específicas para apoiar requisitos de auditoria|Atribuição de política|As contas externas com permissões do proprietário devem ser removidas da sua subscrição|Informações sobre os efeitos políticos podem ser encontradas na [Understand Azure Policy Effects](../../../policy/concepts/effects.md).|
|\[Preview\]: Audit FedRAMP High controls and implemente extensões vm específicas para apoiar requisitos de auditoria|Atribuição de política|Contas depreciadas com permissões do proprietário devem ser removidas da sua subscrição|Informações sobre os efeitos políticos podem ser encontradas na [Understand Azure Policy Effects](../../../policy/concepts/effects.md).|
|\[Preview\]: Audit FedRAMP High controls and implemente extensões vm específicas para apoiar requisitos de auditoria|Atribuição de política|As contas depreciadas devem ser removidas da sua subscrição|Informações sobre os efeitos políticos podem ser encontradas na [Understand Azure Policy Effects](../../../policy/concepts/effects.md).|
|\[Preview\]: Audit FedRAMP High controls and implemente extensões vm específicas para apoiar requisitos de auditoria|Atribuição de política|CorS não deve permitir que todos os recursos acedam à sua Aplicação Web|Informações sobre os efeitos políticos podem ser encontradas na [Understand Azure Policy Effects](../../../policy/concepts/effects.md).|
|\[Preview\]: Audit FedRAMP High controls and implemente extensões vm específicas para apoiar requisitos de auditoria|Atribuição de política|As atualizações do sistema nos conjuntos de escala de máquinas virtuais devem ser instaladas|Informações sobre os efeitos políticos podem ser encontradas na [Understand Azure Policy Effects](../../../policy/concepts/effects.md).|
|\[Preview\]: Audit FedRAMP High controls and implemente extensões vm específicas para apoiar requisitos de auditoria|Atribuição de política|MFA deve ser ativado em contas com permissões de leitura na sua subscrição|Informações sobre os efeitos políticos podem ser encontradas na [Understand Azure Policy Effects](../../../policy/concepts/effects.md).|
|\[Preview\]: Audit FedRAMP High controls and implemente extensões vm específicas para apoiar requisitos de auditoria|Atribuição de política|MFA deve ser ativado em contas com permissões do proprietário na sua subscrição|Informações sobre os efeitos políticos podem ser encontradas na [Understand Azure Policy Effects](../../../policy/concepts/effects.md).|
|\[Preview\]: Audit FedRAMP High controls and implemente extensões vm específicas para apoiar requisitos de auditoria|Atribuição de política|MFA deve ser ativado em contas com permissões de escrita na sua subscrição|Informações sobre os efeitos políticos podem ser encontradas na [Understand Azure Policy Effects](../../../policy/concepts/effects.md).|
|\[Preview\]: Audit FedRAMP High controls and implemente extensões vm específicas para apoiar requisitos de auditoria|Atribuição de política|Backup geo-redundante a longo prazo deve ser ativado para bases de dados Azure SQL|Informações sobre os efeitos políticos podem ser encontradas na [Understand Azure Policy Effects](../../../policy/concepts/effects.md).|


## <a name="next-steps"></a>Passos seguintes

Agora que reviu os passos para implementar a amostra de plantas do FedRAMP High, visite os seguintes artigos para saber sobre o mapeamento de plantas e controlo:

> [!div class="nextstepaction"]
> [FedRAMP High blueprint - Visão geral](./index.md)
> [FedRAMP High blueprint - Mapeamento de controlo](./control-mapping.md)

Artigos adicionais sobre esquemas e como utilizá-los:

- Saiba mais sobre o [ciclo de vida do esquema](../../concepts/lifecycle.md).
- Compreenda como utilizar [parâmetros estáticos e dinâmicos](../../concepts/parameters.md).
- Aprenda a personalizar a [ordem de sequenciação do esquema](../../concepts/sequencing-order.md).
- Saiba como utilizar o [bloqueio de recursos de esquema](../../concepts/resource-locking.md).
- Saiba como [atualizar as atribuições existentes](../../how-to/update-existing-assignments.md).