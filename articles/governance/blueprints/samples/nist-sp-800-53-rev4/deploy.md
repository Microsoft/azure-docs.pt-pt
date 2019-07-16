---
title: Exemplo - esquema de R4 SP do NIST 800-53 - implementar passos
description: Implemente passos de NIST SP 800-53 de exemplo de plano gráfico de R4.
author: DCtheGeek
ms.author: dacoulte
ms.date: 06/24/2019
ms.topic: sample
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: 206763e2d17f4ad711ff5fd897f1429814e61837
ms.sourcegitcommit: 920ad23613a9504212aac2bfbd24a7c3de15d549
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/15/2019
ms.locfileid: "68228906"
---
# <a name="deploy-the-nist-sp-800-53-r4-blueprint-sample"></a>Implementar o SP NIST 800-53 de exemplo de plano gráfico de R4

Para implementar o SP do Azure esquemas NIST 800-53 de exemplo de esquema de R4, é necessário os seguintes passos:

> [!div class="checklist"]
> - Criar um novo plano de gráfico do exemplo
> - Marcar a sua cópia do exemplo de como **publicado**
> - Atribuir a sua cópia do esquema a uma subscrição existente

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free) antes de começar.

## <a name="create-blueprint-from-sample"></a>Criar o plano gráfico de exemplo

Em primeiro lugar, implementa o exemplo de esquema ao criar uma novo esquema no seu ambiente com o exemplo de como um starter.

1. Selecione **todos os serviços** e procure e selecione **política** no painel esquerdo. Sobre o **diretiva** página, selecione **esquemas**.

1. Do **introdução** página à esquerda, selecione a **Create** botão sob _criar um plano gráfico_.

1. Encontrar o **SP do NIST 800-53 R4** exemplo de esquema sob _outros exemplos_ e selecione **utilizar este exemplo**.

1. Introduza o _Noções básicas_ do exemplo de esquema:

   - **Nome do blueprint**: Forneça um nome para a sua cópia do exemplo de esquema do NIST SP 800-53 R4.
   - **Localização da definição**: Utilize o botão de reticências e selecione o grupo de gestão para guardar a sua cópia do exemplo para.

1. Selecione o _artefactos_ separador na parte superior da página ou **seguinte: Artefactos** na parte inferior da página.

1. Reveja a lista de artefactos que constituem o exemplo de esquema. Muitos dos artefatos os parâmetros que vamos definir mais tarde. Selecione **Guardar rascunho** quando terminar a rever o exemplo de esquema.

## <a name="publish-the-sample-copy"></a>Publicar a cópia de exemplo

Sua cópia do exemplo de plano gráfico agora foi criada no seu ambiente. Ele é criado na **rascunho** modo e têm de ser **publicado** antes de pode ser atribuída e implantada. A cópia do exemplo de esquema pode ser personalizada para seu ambiente e as necessidades, mas que a modificação pode movê-lo para fora do alinhamento com NIST SP 800-53 controles.

1. Selecione **todos os serviços** e procure e selecione **política** no painel esquerdo. Sobre o **diretiva** página, selecione **esquemas**.

1. Selecione o **definições de esquema** página à esquerda. Utilize os filtros para encontrar a sua cópia do exemplo de esquema e, em seguida, selecioná-lo.

1. Selecione **publicar esquema** na parte superior da página. A nova página à direita, fornecem uma **versão** para obter uma cópia do exemplo de esquema. Esta propriedade é útil para se tornar uma modificação mais tarde. Fornecer **alterar notas** como "primeira versão publicados a partir do NIST SP exemplo de plano gráfico de R4 800-53." Em seguida, selecione **publicar** na parte inferior da página.

## <a name="assign-the-sample-copy"></a>Atribuir a cópia de exemplo

Depois da cópia do exemplo de plano gráfico de ter sido com êxito **publicado**, podem ser atribuído a uma subscrição dentro do grupo de gestão que foram salvos. Este passo é onde os parâmetros são fornecidos para que cada implementação da cópia do exemplo de plano gráfico exclusivo.

1. Selecione **todos os serviços** e procure e selecione **política** no painel esquerdo. Sobre o **diretiva** página, selecione **esquemas**.

1. Selecione o **definições de esquema** página à esquerda. Utilize os filtros para encontrar a sua cópia do exemplo de esquema e, em seguida, selecioná-lo.

1. Selecione **esquema de atribuir** na parte superior da página de definição de esquema.

1. Forneça os valores de parâmetro para a atribuição do esquema:

   - Noções básicas

     - **Subscrições**: Selecione um ou mais das subscrições que estão no grupo de gestão é guardado sua cópia do exemplo de plano gráfico para. Se selecionar mais do que uma subscrição, será criada uma atribuição para cada um usando os parâmetros introduzidos.
     - **Nome da atribuição**: O nome é preenchido previamente com base no nome do plano gráfico.
       Altere conforme necessário, ou deixe como está.
     - **Localização**: Selecione uma região para a identidade gerida a ser criado em. O Azure Blueprint utiliza esta identidade gerida para implementar todos os artefactos no esquema atribuído. Para saber mais, veja [identidades geridas dos recursos do Azure](../../../../active-directory/managed-identities-azure-resources/overview.md).
     - **Versão da definição de esquema**: Escolher uma **publicado** versão da sua cópia do exemplo de esquema.

   - Atribuição de bloqueio

     Selecione o bloqueio de esquema definição para o seu ambiente. Para obter mais informações, veja [bloqueio de recurso em esquemas](../../concepts/resource-locking.md).

   - Identidade Gerida

     Deixe a predefinição _sistema atribuído_ geridos a opção de identidade.

   - Parâmetros de artefacto

     Os parâmetros definidos nesta secção aplicam-se para o artefacto sob a qual está definido. Esses parâmetros são [parâmetros dinâmicos](../../concepts/parameters.md#dynamic-parameters) , uma vez que estão definidos durante a atribuição do esquema. Para ver uma lista completa ou parâmetros de artefacto e suas descrições, veja [tabela de parâmetros de artefacto](#artifact-parameters-table).

1. Depois de tem sido introduzidos todos os parâmetros, selecione **atribuir** na parte inferior da página. A atribuição do esquema é criada e começa a implementação de artefactos. Implementação demora aproximadamente uma hora. Para verificar o estado da implementação, abra a atribuição do esquema.

> [!WARNING]
> O serviço de esquemas do Azure e os exemplos de esquema incorporados estão **gratuita**. Recursos do Azure estão [o preço por produto](https://azure.microsoft.com/pricing/). Utilize o [Calculadora de preços](https://azure.microsoft.com/pricing/calculator/) para calcular o custo da execução de recursos implementados por este exemplo de esquema.

## <a name="artifact-parameters-table"></a>Tabela de parâmetros de artefacto

A tabela seguinte fornece uma lista do plano gráfico de parâmetros de artefacto:

|Nome do artefacto|Tipo de artefacto|Nome do parâmetro|Descrição|
|-|-|-|-|
|\[Pré-visualização\]: Auditar NIST SP 800-53 R4 controles e implementar extensões de VM específicas para suportar os requisitos de auditoria|Atribuição de política|ID de área de trabalho do log Analytics que as VMs devem ser configuradas para|Este é o ID (GUID) da área de trabalho do Log Analytics que as VMs devem ser configuradas para.|
|\[Pré-visualização\]: Auditar NIST SP 800-53 R4 controles e implementar extensões de VM específicas para suportar os requisitos de auditoria|Atribuição de política|Lista de tipos de recursos que devem ter registos de diagnóstico ativados|Lista de tipos de recursos para auditar se a definição de registo de diagnóstico não está ativada. Os valores aceitáveis podem ser encontrados em [esquemas de registos de diagnóstico do Azure Monitor](../../../../azure-monitor/platform/diagnostic-logs-schema.md#supported-log-categories-per-resource-type).|
|\[Pré-visualização\]: Auditar NIST SP 800-53 R4 controles e implementar extensões de VM específicas para suportar os requisitos de auditoria|Atribuição de política|Lista de utilizadores que deve ser excluído do grupo de administradores de VM do Windows|Uma lista delimitada por vírgulas de membros do grupo local de administradores que devem ser excluídos. Ex.: Administrador; myUser1; myUser2|
|\[Pré-visualização\]: Auditar NIST SP 800-53 R4 controles e implementar extensões de VM específicas para suportar os requisitos de auditoria|Atribuição de política|Lista de utilizadores que devem ser incluídas no grupo de administradores de VM do Windows|Uma lista delimitada por vírgulas de membros que devem ser incluídas no grupo de administradores locais. Ex.: Administrador; myUser1; myUser2|
|\[Pré-visualização\]: Implementar o agente de análise de registo para conjuntos de dimensionamento de VM do Linux (VMSS)|Atribuição de política|Área de trabalho do log Analytics para conjuntos de dimensionamento de VM do Linux (VMSS)|Se esta área de trabalho está fora do âmbito da atribuição tem de conceder permissões de "Contribuidor do Log Analytics" manualmente (ou semelhante) para o ID de principal. a atribuição de política|
|\[Pré-visualização\]: Implementar o agente de análise de registo para conjuntos de dimensionamento de VM do Linux (VMSS)|Atribuição de política|Opcional: Lista de imagens VM que tenha suporte SO Linux para adicionar ao âmbito|Uma matriz vazia pode ser usada para indicar sem parâmetros opcionais: \[\]|
|\[Pré-visualização\]: Implementar o agente de análise de registo para VMs do Linux|Atribuição de política|Área de trabalho de análise de registo para VMs do Linux|Se esta área de trabalho está fora do âmbito da atribuição tem de conceder permissões de "Contribuidor do Log Analytics" manualmente (ou semelhante) para o ID de principal. a atribuição de política|
|\[Pré-visualização\]: Implementar o agente de análise de registo para VMs do Linux|Atribuição de política|Opcional: Lista de imagens VM que tenha suporte SO Linux para adicionar ao âmbito|Uma matriz vazia pode ser usada para indicar sem parâmetros opcionais: \[\]|
|\[Pré-visualização\]: Implementar o agente de análise de registo para conjuntos de dimensionamento VM do Windows (VMSS)|Atribuição de política|Área de trabalho do log Analytics para conjuntos de dimensionamento de VM do Windows (VMSS)|Se esta área de trabalho está fora do âmbito da atribuição tem de conceder permissões de "Contribuidor do Log Analytics" manualmente (ou semelhante) para o ID de principal. a atribuição de política|
|\[Pré-visualização\]: Implementar o agente de análise de registo para conjuntos de dimensionamento VM do Windows (VMSS)|Atribuição de política|Opcional: Lista de imagens VM que têm suporte SO do Windows para adicionar ao âmbito|Uma matriz vazia pode ser usada para indicar sem parâmetros opcionais: \[\]|
|\[Pré-visualização\]: Implementar o agente de análise de registo para VMs do Windows|Atribuição de política|Área de trabalho de análise de registo para VMs do Windows|Se esta área de trabalho está fora do âmbito da atribuição tem de conceder permissões de "Contribuidor do Log Analytics" manualmente (ou semelhante) para o ID de principal. a atribuição de política|
|\[Pré-visualização\]: Implementar o agente de análise de registo para VMs do Windows|Atribuição de política|Opcional: Lista de imagens VM que têm suporte SO do Windows para adicionar ao âmbito|Uma matriz vazia pode ser usada para indicar sem parâmetros opcionais: \[\]|
|Implementar a proteção avançada contra ameaças em contas de armazenamento|Atribuição de política|Efeito|Informações sobre os efeitos de política podem ser encontradas em [compreender efeitos de política do Azure](../../../policy/concepts/effects.md)|
|Implementar a auditoria em servidores SQL|Atribuição de política|O valor em dias do período de retenção (0 indica retenção ilimitada)|Dias de retenção (opcionais, 180 dias se não for especificado)|
|Implementar a auditoria em servidores SQL|Atribuição de política|Nome do grupo de recursos para a conta de armazenamento para auditoria do SQL server|Auditoria escreve eventos de base de dados para uma auditoria inicie sessão na sua conta de armazenamento do Azure (será criada uma conta de armazenamento em cada região onde é criado um SQL Server que irão ser partilhado por todos os servidores nessa região). Importante - para a operação correta de auditoria não eliminar ou mudar o nome do grupo de recursos ou as contas de armazenamento.|
|Implementar definições de diagnóstico para grupos de segurança de rede|Atribuição de política|Prefixo da conta de armazenamento para diagnóstico de grupo de segurança de rede|Este prefixo será combinado com a localização de grupo de segurança de rede para formar o nome da conta de armazenamento criada.|
|Implementar definições de diagnóstico para grupos de segurança de rede|Atribuição de política|Nome do grupo de recursos para a conta de armazenamento para diagnóstico de grupo de segurança de rede (tem de existir)|O grupo de recursos que a conta de armazenamento será criada na. Este grupo de recursos tem de existir.|

## <a name="next-steps"></a>Passos Seguintes

Agora que reviu os passos para implementar o SP NIST 800-53 de exemplo de plano gráfico de R4, visite os seguintes artigos para saber mais sobre o esquema e o mapeamento de controle:

> [!div class="nextstepaction"]
> [Esquema de R4 SP do NIST 800-53 - descrição geral](./index.md)
> [esquema de R4 SP do NIST 800-53 - mapeamento de controlo](./control-mapping.md)

Artigos adicionais sobre esquemas e como utilizá-los:

- Saiba mais sobre o [ciclo de vida de um esquema](../../concepts/lifecycle.md).
- Compreenda como utilizar [parâmetros estáticos e dinâmicos](../../concepts/parameters.md).
- Aprenda a personalizar a [ordem de sequenciação do esquema](../../concepts/sequencing-order.md).
- Saiba como utilizar o [bloqueio de recursos de esquema](../../concepts/resource-locking.md).
- Saiba como [atualizar as atribuições existentes](../../how-to/update-existing-assignments.md).
