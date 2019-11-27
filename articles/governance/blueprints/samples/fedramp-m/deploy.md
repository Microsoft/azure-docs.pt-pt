---
title: Implantar a amostra do FedRAMP moderado Blueprint
description: Implante as etapas do exemplo FedRAMP moderado Blueprint, incluindo detalhes do parâmetro de artefato do Blueprint.
ms.date: 10/31/2019
ms.topic: sample
ms.openlocfilehash: f38aad3adb625a0874d6287d25b1fe60f2579291
ms.sourcegitcommit: a678f00c020f50efa9178392cd0f1ac34a86b767
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/26/2019
ms.locfileid: "74544495"
---
# <a name="deploy-the-fedramp-moderate-blueprint-sample"></a>Implantar a amostra do FedRAMP moderado Blueprint

Para implantar o exemplo do plano gráfico do Azure Blueprints FedRAMP moderado, as etapas a seguir devem ser executadas:

> [!div class="checklist"]
> - Criar um novo plano gráfico por meio do exemplo
> - Marque sua cópia do exemplo como **publicado**
> - Atribuir sua cópia do plano gráfico a uma assinatura existente

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free) antes de começar.

## <a name="create-blueprint-from-sample"></a>Criar plano gráfico por meio de exemplo

Primeiro, implemente o exemplo Blueprint criando uma nova especificação técnica em seu ambiente usando o exemplo como um início.

1. Selecione **todos os serviços** no painel esquerdo. Pesquise e selecione **plantas**.

1. Na página **Guia de introdução** à esquerda, selecione o botão **criar** em _criar um plano gráfico_.

1. Encontre o exemplo **FedRAMP moderado** Blueprint em _outros exemplos_ e selecione **usar este exemplo**.

1. Insira os _conceitos básicos_ do exemplo Blueprint:

   - **Nome do plano gráfico**: forneça um nome para sua cópia do exemplo FedRAMP moderado Blueprint.
   - **Local de definição**: Use as reticências e selecione o grupo de gerenciamento para salvar a cópia do exemplo.

1. Selecione a guia _artefatos_ na parte superior da página ou **próximo: artefatos** na parte inferior da página.

1. Examine a lista de artefatos que compõem o exemplo Blueprint. Muitos dos artefatos têm parâmetros que vamos definir mais tarde. Selecione **salvar rascunho** ao concluir a revisão do exemplo Blueprint.

## <a name="publish-the-sample-copy"></a>Publicar a cópia de exemplo

Sua cópia do exemplo Blueprint agora foi criada em seu ambiente. Ele é criado no modo de **rascunho** e deve ser **publicado** antes que possa ser atribuído e implantado. A cópia do exemplo Blueprint pode ser personalizada para seu ambiente e necessidades, mas essa modificação pode movê-la para fora do alinhamento com controles moderados FedRAMP.

1. Selecione **todos os serviços** no painel esquerdo. Pesquise e selecione **plantas**.

1. Selecione a página **definições de plantas** à esquerda. Use os filtros para localizar sua cópia do exemplo de plano gráfico e, em seguida, selecione-o.

1. Selecione **publicar Blueprint** na parte superior da página. Na nova página à direita, forneça uma **versão** para sua cópia do exemplo Blueprint. Essa propriedade será útil se você fizer uma modificação posteriormente. Forneça **observações de alteração** , como "primeira versão publicada no exemplo do FedRAMP moderado Blueprint". Em seguida, selecione **publicar** na parte inferior da página.

## <a name="assign-the-sample-copy"></a>Atribuir a cópia de exemplo

Depois que a cópia do exemplo Blueprint tiver sido **publicada**com êxito, ela poderá ser atribuída a uma assinatura dentro do grupo de gerenciamento no qual foi salva. Esta etapa é onde os parâmetros são fornecidos para fazer com que cada implantação da cópia do exemplo de plano gráfico seja exclusiva.

1. Selecione **todos os serviços** no painel esquerdo. Pesquise e selecione **plantas**.

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
|\[visualização\]: auditar controles moderados do FedRAMP e implantar extensões de VM específicas para dar suporte aos requisitos de auditoria|Atribuição de política|ID do espaço de trabalho Log Analytics a qual as VMs devem ser configuradas|Essa é a ID (GUID) do espaço de trabalho Log Analytics para a qual as VMs devem ser configuradas.|
|\[visualização\]: auditar controles moderados do FedRAMP e implantar extensões de VM específicas para dar suporte aos requisitos de auditoria|Atribuição de política|Lista de tipos de recursos que devem ter logs de diagnóstico habilitados|Lista de tipos de recursos para auditar se a configuração do log de diagnóstico não estiver habilitada. Os valores aceitáveis podem ser encontrados em [Azure monitor esquemas de logs de diagnóstico](../../../../azure-monitor/platform/diagnostic-logs-schema.md#supported-log-categories-per-resource-type).|
|\[visualização\]: auditar controles moderados do FedRAMP e implantar extensões de VM específicas para dar suporte aos requisitos de auditoria|Atribuição de política|Lista de usuários que devem ser excluídos do grupo de administradores de VM do Windows|Uma lista de membros separados por ponto e vírgula que deve ser excluída no grupo local Administradores. Por exemplo: administrador; myusuário1; myUser2|
|\[visualização\]: auditar controles moderados do FedRAMP e implantar extensões de VM específicas para dar suporte aos requisitos de auditoria|Atribuição de política|Lista de usuários que devem ser incluídos no grupo de administradores de VM do Windows|Uma lista de membros separados por ponto e vírgula que deve ser incluída no grupo local Administradores. Por exemplo: administrador; myusuário1; myUser2|
|\]de visualização do \[: implantar o agente de Log Analytics para conjuntos de dimensionamento de VM do Linux (VMSS)|Atribuição de política|Log Analytics espaço de trabalho para conjuntos de dimensionamento de VM do Linux (VMSS)|Se esse espaço de trabalho estiver fora do escopo da atribuição, você deverá conceder manualmente as permissões ' Log Analytics colaborador ' (ou semelhante) à ID principal da atribuição de política.|
|\]de visualização do \[: implantar o agente de Log Analytics para conjuntos de dimensionamento de VM do Linux (VMSS)|Atribuição de política|Opcional: lista de imagens de VM que têm suporte para o sistema operacional Linux para adicionar ao escopo|Uma matriz vazia pode ser usada para indicar nenhum parâmetro opcional: \[\]|
|\]de visualização de \[: implantar o agente de Log Analytics para VMs Linux|Atribuição de política|Espaço de trabalho Log Analytics para VMs Linux|Se esse espaço de trabalho estiver fora do escopo da atribuição, você deverá conceder manualmente as permissões ' Log Analytics colaborador ' (ou semelhante) à ID principal da atribuição de política.|
|\]de visualização de \[: implantar o agente de Log Analytics para VMs Linux|Atribuição de política|Opcional: lista de imagens de VM que têm suporte para o sistema operacional Linux para adicionar ao escopo|Uma matriz vazia pode ser usada para indicar nenhum parâmetro opcional: \[\]|
|\]de visualização de \[: implantar o agente de Log Analytics para VMSS (conjuntos de dimensionamento de VMs) do Windows|Atribuição de política|Log Analytics espaço de trabalho para VMSS (conjuntos de dimensionamento de VMs do Windows)|Se esse espaço de trabalho estiver fora do escopo da atribuição, você deverá conceder manualmente as permissões ' Log Analytics colaborador ' (ou semelhante) à ID principal da atribuição de política.|
|\]de visualização de \[: implantar o agente de Log Analytics para VMSS (conjuntos de dimensionamento de VMs) do Windows|Atribuição de política|Opcional: lista de imagens de VM que têm suporte para o sistema operacional Windows para adicionar ao escopo|Uma matriz vazia pode ser usada para indicar nenhum parâmetro opcional: \[\]|
|\]de visualização do \[: implantar o agente de Log Analytics para VMs do Windows|Atribuição de política|Espaço de trabalho Log Analytics para VMs do Windows|Se esse espaço de trabalho estiver fora do escopo da atribuição, você deverá conceder manualmente as permissões ' Log Analytics colaborador ' (ou semelhante) à ID principal da atribuição de política.|
|\]de visualização do \[: implantar o agente de Log Analytics para VMs do Windows|Atribuição de política|Opcional: lista de imagens de VM que têm suporte para o sistema operacional Windows para adicionar ao escopo|Uma matriz vazia pode ser usada para indicar nenhum parâmetro opcional: \[\]|
|Implantar a proteção avançada contra ameaças em contas de armazenamento|Atribuição de política|Efeito|As informações sobre os efeitos da política podem ser encontradas em [entender Azure Policy efeitos](../../../policy/concepts/effects.md)|
|Implantar a auditoria em servidores SQL|Atribuição de política|O valor em dias do período de retenção (0 indica retenção ilimitada)|Dias de retenção (opcional, 180 dias se não especificado)|
|Implantar a auditoria em servidores SQL|Atribuição de política|Nome do grupo de recursos para a conta de armazenamento para auditoria do SQL Server|A auditoria grava eventos de banco de dados em um log de auditoria na sua conta de armazenamento do Azure (uma conta de armazenamento será criada em cada região em que um SQL Server é criado e será compartilhado por todos os servidores nessa região). Importante-para uma operação adequada de auditoria, não exclua ou renomeie o grupo de recursos ou as contas de armazenamento.|
|Implantar configurações de diagnóstico para grupos de segurança de rede|Atribuição de política|Prefixo da conta de armazenamento para diagnóstico do grupo de segurança de rede|Esse prefixo será combinado com o local do grupo de segurança de rede para formar o nome da conta de armazenamento criada.|
|Implantar configurações de diagnóstico para grupos de segurança de rede|Atribuição de política|Nome do grupo de recursos para a conta de armazenamento para diagnóstico do grupo de segurança de rede (deve existir)|O grupo de recursos no qual a conta de armazenamento será criada. Este grupo de recursos já deve existir.|

## <a name="next-steps"></a>Passos Seguintes

Agora que você analisou as etapas para implantar a amostra do FedRAMP moderado Blueprint, visite os artigos a seguir para saber mais sobre o esquema de plano gráfico e controle:

> [!div class="nextstepaction"]
> [FedRAMP moderado Blueprint-visão geral](./index.md)
> [FedRAMP moderado Blueprint – mapeamento de controle](./control-mapping.md)

Artigos adicionais sobre esquemas e como os utilizar:

- Saiba mais sobre o [ciclo de vida do esquema](../../concepts/lifecycle.md).
- Compreenda como utilizar [parâmetros estáticos e dinâmicos](../../concepts/parameters.md).
- Aprenda a personalizar a [ordem de sequenciação do esquema](../../concepts/sequencing-order.md).
- Saiba como utilizar o [bloqueio de recursos de esquema](../../concepts/resource-locking.md).
- Saiba como [atualizar as atribuições existentes](../../how-to/update-existing-assignments.md).