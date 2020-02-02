---
title: Amostra - FedRAMP High blueprint - Implementar etapas
description: Desloque os passos para a amostra de plantas do FedRAMP High, incluindo detalhes do parâmetro de artefacto de plantas.
ms.date: 01/31/2020
ms.topic: sample
ms.openlocfilehash: baa9f419a9f15e84eced322c8f7095606c7a24c4
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/01/2020
ms.locfileid: "76941368"
---
# <a name="deploy-the-fedramp-high-blueprint-sample"></a>Implementar a amostra de plantas do FedRAMP High

Para implantar a amostra de plantas FedRAMP High, devem ser tomadas as seguintes medidas:

> [!div class="checklist"]
> - Criar um novo plano gráfico por meio do exemplo
> - Marque sua cópia do exemplo como **publicado**
> - Atribuir sua cópia do plano gráfico a uma assinatura existente

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free) antes de começar.

## <a name="create-blueprint-from-sample"></a>Criar plano gráfico por meio de exemplo

Primeiro, implemente o exemplo Blueprint criando uma nova especificação técnica em seu ambiente usando o exemplo como um início.

1. Selecione **Todos os serviços** no painel esquerdo. Pesquise e selecione **plantas**.

1. Na página **Guia de introdução** à esquerda, selecione o botão **criar** em _criar um plano gráfico_.

1. Encontre a amostra de planta **alta fedRAMP** em _outras amostras_ e selecione **Utilize esta amostra**.

1. Insira os _conceitos básicos_ do exemplo Blueprint:

   - **Nome**da planta : Forneça um nome para a sua cópia da amostra de plantas fedramp high.
   - **Local de definição**: Use as reticências e selecione o grupo de gerenciamento para salvar a cópia do exemplo.

1. Selecione a guia _artefatos_ na parte superior da página ou **próximo: artefatos** na parte inferior da página.

1. Examine a lista de artefatos que compõem o exemplo Blueprint. Muitos dos artefatos têm parâmetros que vamos definir mais tarde. Selecione **salvar rascunho** ao concluir a revisão do exemplo Blueprint.

## <a name="publish-the-sample-copy"></a>Publicar a cópia de exemplo

Sua cópia do exemplo Blueprint agora foi criada em seu ambiente. Ele é criado no modo de **rascunho** e deve ser **publicado** antes que possa ser atribuído e implantado. A cópia da amostra de plantas pode ser personalizada para o seu ambiente e necessidades, mas essa modificação pode afastá-la do alinhamento com os controlos FedRAMP High.

1. Selecione **Todos os serviços** no painel esquerdo. Pesquise e selecione **plantas**.

1. Selecione a página **definições de plantas** à esquerda. Use os filtros para localizar sua cópia do exemplo de plano gráfico e, em seguida, selecione-o.

1. Selecione **publicar Blueprint** na parte superior da página. Na nova página à direita, forneça uma **versão** para sua cópia do exemplo Blueprint. Essa propriedade será útil se você fizer uma modificação posteriormente. Forneça notas de **Mudança** como "Primeira versão publicada a partir da amostra de plantas fedramp high." Em seguida, selecione **publicar** na parte inferior da página.

## <a name="assign-the-sample-copy"></a>Atribuir a cópia de exemplo

Depois que a cópia do exemplo Blueprint tiver sido **publicada**com êxito, ela poderá ser atribuída a uma assinatura dentro do grupo de gerenciamento no qual foi salva. Esta etapa é onde os parâmetros são fornecidos para fazer com que cada implantação da cópia do exemplo de plano gráfico seja exclusiva.

1. Selecione **Todos os serviços** no painel esquerdo. Pesquise e selecione **plantas**.

1. Selecione a página **definições de plantas** à esquerda. Use os filtros para localizar sua cópia do exemplo de plano gráfico e, em seguida, selecione-o.

1. Selecione **atribuir plano gráfico** na parte superior da página de definição do Blueprint.

1. Forneça os valores de parâmetro para a atribuição Blueprint:

   - Noções básicas

     - **Assinaturas**: selecione uma ou mais das assinaturas que estão no grupo de gerenciamento em que você salvou sua cópia do exemplo Blueprint. Se você selecionar mais de uma assinatura, uma atribuição será criada para cada uma usando os parâmetros inseridos.
     - **Nome da atribuição**: o nome é preenchido previamente para você com base no nome do plano gráfico.
       Altere conforme necessário ou deixe como está.
     - **Local**: selecione uma região na qual a identidade gerenciada deve ser criada. O Azure Blueprint utiliza esta identidade gerida para implementar todos os artefactos no esquema atribuído. Para saber mais, veja [identidades geridas dos recursos do Azure](../../../../active-directory/managed-identities-azure-resources/overview.md).
     - **Versão de definição do Blueprint**: escolha uma versão **publicada** da sua cópia do exemplo Blueprint.

   - Atribuição de bloqueio

     Selecione a configuração de bloqueio Blueprint para o seu ambiente. Para obter mais informações, veja [bloqueio de recurso em esquemas](../../concepts/resource-locking.md).

   - Identidade Gerida

     Deixe a opção de identidade gerenciada _atribuída pelo sistema_ padrão.

   - Parâmetros de artefato

     Os parâmetros definidos nesta seção se aplicam ao artefato sob o qual ele é definido. Esses parâmetros são [parâmetros dinâmicos](../../concepts/parameters.md#dynamic-parameters) , pois eles são definidos durante a atribuição do plano gráfico. Para obter uma lista completa ou parâmetros de artefato e suas descrições, consulte [tabela de parâmetros de artefato](#artifact-parameters-table).

1. Depois que todos os parâmetros forem inseridos, selecione **atribuir** na parte inferior da página. A atribuição Blueprint é criada e a implantação do artefato começa. A implantação leva aproximadamente uma hora. Para verificar o status da implantação, abra a atribuição Blueprint.

> [!WARNING]
> O serviço de plantas do Azure e os exemplos de plantas internas são **gratuitos**. Os recursos do Azure são [cobrados por produto](https://azure.microsoft.com/pricing/). Use a [calculadora de preços](https://azure.microsoft.com/pricing/calculator/) para estimar o custo da execução de recursos implantados por este exemplo de Blueprint.

## <a name="artifact-parameters-table"></a>Tabela de parâmetros de artefato

A tabela a seguir fornece uma lista dos parâmetros de artefatos do Blueprint:

|Nome do artefato|Tipo de artefato|Nome do parâmetro|Descrição|
|-|-|-|-|
|\[Preview\]: Audit FedRAMP High controls and implemente extensões vm específicas para apoiar requisitos de auditoria|Atribuição de política|ID do espaço de trabalho Log Analytics a qual as VMs devem ser configuradas|Essa é a ID (GUID) do espaço de trabalho Log Analytics para a qual as VMs devem ser configuradas.|
|\[Preview\]: Audit FedRAMP High controls and implemente extensões vm específicas para apoiar requisitos de auditoria|Atribuição de política|Lista de tipos de recursos que devem ter logs de diagnóstico habilitados|Lista de tipos de recursos para auditar se a configuração do log de diagnóstico não estiver habilitada. Os valores aceitáveis podem ser encontrados em [Azure monitor esquemas de logs de diagnóstico](../../../../azure-monitor/platform/diagnostic-logs-schema.md#supported-log-categories-per-resource-type).|
|\[Preview\]: Audit FedRAMP High controls and implemente extensões vm específicas para apoiar requisitos de auditoria|Atribuição de política|Lista de utilizadores que devem ser excluídos do grupo de Administradores VM do Windows|Uma lista separada do ponto-e-vírgula dos membros que deve ser excluída no grupo local dos administradores. Ex: Administrador; myUser1; myUser2|
|\[Preview\]: Audit FedRAMP High controls and implemente extensões vm específicas para apoiar requisitos de auditoria|Atribuição de política|Lista de usuários que devem ser incluídos no grupo de administradores de VM do Windows|Uma lista de membros separados por ponto e vírgula que deve ser incluída no grupo local Administradores. Ex: Administrador; myUser1; myUser2|
|\[pré-visualização\]: Implementar o agente de análise de registo para conjuntos de escala SM (VMSS)|Atribuição de política|Log Analytics espaço de trabalho para conjuntos de dimensionamento de VM do Linux (VMSS)|Se esse espaço de trabalho estiver fora do escopo da atribuição, você deverá conceder manualmente as permissões ' Log Analytics colaborador ' (ou semelhante) à ID principal da atribuição de política.|
|\[pré-visualização\]: Implementar o agente de análise de registo para conjuntos de escala SM (VMSS)|Atribuição de política|Opcional: lista de imagens de VM que têm suporte para o sistema operacional Linux para adicionar ao escopo|Uma matriz vazia pode ser utilizada para não indicar parâmetros opcionais: \[\]|
|\[Pré-visualização\]: implementar o agente de análise de registo para VMs do Linux|Atribuição de política|Espaço de trabalho Log Analytics para VMs Linux|Se esse espaço de trabalho estiver fora do escopo da atribuição, você deverá conceder manualmente as permissões ' Log Analytics colaborador ' (ou semelhante) à ID principal da atribuição de política.|
|\[Pré-visualização\]: implementar o agente de análise de registo para VMs do Linux|Atribuição de política|Opcional: lista de imagens de VM que têm suporte para o sistema operacional Linux para adicionar ao escopo|Uma matriz vazia pode ser utilizada para não indicar parâmetros opcionais: \[\]|
|\[\]de pré-visualização: Implementar o agente de análise de registo para conjuntos de escala seletivas vM (VMSS)|Atribuição de política|Log Analytics espaço de trabalho para VMSS (conjuntos de dimensionamento de VMs do Windows)|Se esse espaço de trabalho estiver fora do escopo da atribuição, você deverá conceder manualmente as permissões ' Log Analytics colaborador ' (ou semelhante) à ID principal da atribuição de política.|
|\[\]de pré-visualização: Implementar o agente de análise de registo para conjuntos de escala seletivas vM (VMSS)|Atribuição de política|Opcional: lista de imagens de VM que têm suporte para o sistema operacional Windows para adicionar ao escopo|Uma matriz vazia pode ser utilizada para não indicar parâmetros opcionais: \[\]|
|\[Pré-visualização\]: implementar o agente de análise de registo para VMs do Windows|Atribuição de política|Espaço de trabalho Log Analytics para VMs do Windows|Se esse espaço de trabalho estiver fora do escopo da atribuição, você deverá conceder manualmente as permissões ' Log Analytics colaborador ' (ou semelhante) à ID principal da atribuição de política.|
|\[Pré-visualização\]: implementar o agente de análise de registo para VMs do Windows|Atribuição de política|Opcional: lista de imagens de VM que têm suporte para o sistema operacional Windows para adicionar ao escopo|Uma matriz vazia pode ser utilizada para não indicar parâmetros opcionais: \[\]|
|Implantar a proteção avançada contra ameaças em contas de armazenamento|Atribuição de política|Efeito|As informações sobre os efeitos da política podem ser encontradas em [entender Azure Policy efeitos](../../../policy/concepts/effects.md).|
|Implantar a auditoria em servidores SQL|Atribuição de política|O valor em dias do período de retenção (0 indica retenção ilimitada)|Dias de retenção (opcional, 180 dias se não especificados)|
|Implantar a auditoria em servidores SQL|Atribuição de política|Nome do grupo de recursos para a conta de armazenamento para auditoria do SQL Server|A auditoria escreve eventos de base de dados para um registo de auditoria na sua conta de Armazenamento Azure (será criada uma conta de armazenamento em cada região onde é criado um Servidor SQL que será partilhado por todos os servidores dessa região). Importante - para o bom funcionamento da Auditoria não apague ou mude o nome do grupo de recursos ou das contas de armazenamento.|
|Implantar configurações de diagnóstico para grupos de segurança de rede|Atribuição de política|Prefixo da conta de armazenamento para diagnóstico do grupo de segurança de rede|Este prefixo será combinado com a localização do grupo de segurança da rede para formar o nome da conta de armazenamento criada.|
|Implantar configurações de diagnóstico para grupos de segurança de rede|Atribuição de política|Nome do grupo de recursos para a conta de armazenamento para diagnóstico do grupo de segurança de rede (deve existir)|O grupo de recursos em que a conta de armazenamento será criada. Este grupo de recursos já deve existir.|
|\[Preview\]: Audit FedRAMP High controls and implemente extensões vm específicas para apoiar requisitos de auditoria|Atribuição de política|Locais permitidos para recursos e grupos de recursos|Lista de localizações do Azure que a sua organização pode especificar ao implementar recursos. Este valor fornecido também é utilizado pela política de "locais permitidos" no âmbito da iniciativa política.|
|\[Preview\]: Audit FedRAMP High controls and implemente extensões vm específicas para apoiar requisitos de auditoria|Atribuição de política|A avaliação de vulnerabilidade deve ser habilitada em suas instâncias gerenciadas do SQL|As informações sobre os efeitos da política podem ser encontradas em [entender Azure Policy efeitos](../../../policy/concepts/effects.md).|
|\[Preview\]: Audit FedRAMP High controls and implemente extensões vm específicas para apoiar requisitos de auditoria|Atribuição de política|A avaliação de vulnerabilidades deve estar ativada nos seus servidores SQL|As informações sobre os efeitos da política podem ser encontradas em [entender Azure Policy efeitos](../../../policy/concepts/effects.md).|
|\[Preview\]: Audit FedRAMP High controls and implemente extensões vm específicas para apoiar requisitos de auditoria|Atribuição de política|A avaliação da vulnerabilidade deve ser ativada em máquinas virtuais|As informações sobre os efeitos da política podem ser encontradas em [entender Azure Policy efeitos](../../../policy/concepts/effects.md).|
|\[Preview\]: Audit FedRAMP High controls and implemente extensões vm específicas para apoiar requisitos de auditoria|Atribuição de política|Armazenamento geo-redundante deve ser ativado para contas de armazenamento|As informações sobre os efeitos da política podem ser encontradas em [entender Azure Policy efeitos](../../../policy/concepts/effects.md).|
|\[Preview\]: Audit FedRAMP High controls and implemente extensões vm específicas para apoiar requisitos de auditoria|Atribuição de política|Backup geo-redundante deve ser ativado para base de dados Azure para MariaDB|As informações sobre os efeitos da política podem ser encontradas em [entender Azure Policy efeitos](../../../policy/concepts/effects.md).|
|\[Preview\]: Audit FedRAMP High controls and implemente extensões vm específicas para apoiar requisitos de auditoria|Atribuição de política|Backup geo-redundante deve ser ativado para Base de Dados Azure para MySQL|As informações sobre os efeitos da política podem ser encontradas em [entender Azure Policy efeitos](../../../policy/concepts/effects.md).|
|\[Preview\]: Audit FedRAMP High controls and implemente extensões vm específicas para apoiar requisitos de auditoria|Atribuição de política|Backup geo-redundante deve ser ativado para Base de Dados Azure para PostgreSQL|As informações sobre os efeitos da política podem ser encontradas em [entender Azure Policy efeitos](../../../policy/concepts/effects.md).|
|\[Preview\]: Audit FedRAMP High controls and implemente extensões vm específicas para apoiar requisitos de auditoria|Atribuição de política|As regras do Grupo de Segurança da Rede para as máquinas virtuais viradas para a Internet devem ser endurecidas|As informações sobre os efeitos da política podem ser encontradas em [entender Azure Policy efeitos](../../../policy/concepts/effects.md).|
|\[Preview\]: Audit FedRAMP High controls and implemente extensões vm específicas para apoiar requisitos de auditoria|Atribuição de política|Aplicação Web só deve estar acessível através de HTTPS|As informações sobre os efeitos da política podem ser encontradas em [entender Azure Policy efeitos](../../../policy/concepts/effects.md).|
|\[Preview\]: Audit FedRAMP High controls and implemente extensões vm específicas para apoiar requisitos de auditoria|Atribuição de política|Função de aplicação só deve estar acessível através de HTTPS|As informações sobre os efeitos da política podem ser encontradas em [entender Azure Policy efeitos](../../../policy/concepts/effects.md).|
|\[Preview\]: Audit FedRAMP High controls and implemente extensões vm específicas para apoiar requisitos de auditoria|Atribuição de política|As contas externas com permissões de escrita devem ser removidas da sua subscrição|As informações sobre os efeitos da política podem ser encontradas em [entender Azure Policy efeitos](../../../policy/concepts/effects.md).|
|\[Preview\]: Audit FedRAMP High controls and implemente extensões vm específicas para apoiar requisitos de auditoria|Atribuição de política|As contas externas com permissões de leitura devem ser removidas da sua subscrição|As informações sobre os efeitos da política podem ser encontradas em [entender Azure Policy efeitos](../../../policy/concepts/effects.md).|
|\[Preview\]: Audit FedRAMP High controls and implemente extensões vm específicas para apoiar requisitos de auditoria|Atribuição de política|As contas externas com permissões do proprietário devem ser removidas da sua subscrição|As informações sobre os efeitos da política podem ser encontradas em [entender Azure Policy efeitos](../../../policy/concepts/effects.md).|
|\[Preview\]: Audit FedRAMP High controls and implemente extensões vm específicas para apoiar requisitos de auditoria|Atribuição de política|Contas depreciadas com permissões do proprietário devem ser removidas da sua subscrição|As informações sobre os efeitos da política podem ser encontradas em [entender Azure Policy efeitos](../../../policy/concepts/effects.md).|
|\[Preview\]: Audit FedRAMP High controls and implemente extensões vm específicas para apoiar requisitos de auditoria|Atribuição de política|As contas depreciadas devem ser removidas da sua subscrição|As informações sobre os efeitos da política podem ser encontradas em [entender Azure Policy efeitos](../../../policy/concepts/effects.md).|
|\[Preview\]: Audit FedRAMP High controls and implemente extensões vm específicas para apoiar requisitos de auditoria|Atribuição de política|CorS não deve permitir que todos os recursos acedam à sua Aplicação Web|As informações sobre os efeitos da política podem ser encontradas em [entender Azure Policy efeitos](../../../policy/concepts/effects.md).|
|\[Preview\]: Audit FedRAMP High controls and implemente extensões vm específicas para apoiar requisitos de auditoria|Atribuição de política|As atualizações do sistema nos conjuntos de escala de máquinas virtuais devem ser instaladas|As informações sobre os efeitos da política podem ser encontradas em [entender Azure Policy efeitos](../../../policy/concepts/effects.md).|
|\[Preview\]: Audit FedRAMP High controls and implemente extensões vm específicas para apoiar requisitos de auditoria|Atribuição de política|MFA deve ser ativado em contas com permissões de leitura na sua subscrição|As informações sobre os efeitos da política podem ser encontradas em [entender Azure Policy efeitos](../../../policy/concepts/effects.md).|
|\[Preview\]: Audit FedRAMP High controls and implemente extensões vm específicas para apoiar requisitos de auditoria|Atribuição de política|MFA deve ser ativado em contas com permissões do proprietário na sua subscrição|As informações sobre os efeitos da política podem ser encontradas em [entender Azure Policy efeitos](../../../policy/concepts/effects.md).|
|\[Preview\]: Audit FedRAMP High controls and implemente extensões vm específicas para apoiar requisitos de auditoria|Atribuição de política|MFA deve ser ativado em contas com permissões de escrita na sua subscrição|As informações sobre os efeitos da política podem ser encontradas em [entender Azure Policy efeitos](../../../policy/concepts/effects.md).|
|\[Preview\]: Audit FedRAMP High controls and implemente extensões vm específicas para apoiar requisitos de auditoria|Atribuição de política|Backup geo-redundante a longo prazo deve ser ativado para bases de dados Azure SQL|As informações sobre os efeitos da política podem ser encontradas em [entender Azure Policy efeitos](../../../policy/concepts/effects.md).|


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