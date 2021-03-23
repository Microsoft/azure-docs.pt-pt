---
title: Amostra de planta restrita ism da Nova Zelândia
description: Visão geral da amostra de planta restrita do ISM da Nova Zelândia. Este exemplo de esquema ajuda os clientes a avaliar controlos específicos.
ms.date: 03/22/2021
ms.topic: sample
ms.openlocfilehash: a52470ea45e6358007dc49122599e87091fbf8f7
ms.sourcegitcommit: ba3a4d58a17021a922f763095ddc3cf768b11336
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/23/2021
ms.locfileid: "104803933"
---
# <a name="new-zealand-ism-restricted-blueprint-sample"></a>Amostra de planta restrita ism da Nova Zelândia

A amostra de planta restrita do ISM da Nova Zelândia fornece guarda-carris de governação usando [a Política Azure](../../policy/overview.md) que o ajuda a avaliar controlos específicos [do Manual de Segurança da Informação da Nova Zelândia.](https://www.nzism.gcsb.govt.nz/) Este projeto ajuda os clientes a implementar um conjunto central de políticas para qualquer arquitetura implantada pelo Azure que deve implementar controlos para a Nova Zelândia ISM Restricted.

## <a name="control-mapping"></a>Mapeamento de controlo

O [mapeamento do controlo da política Azure](../../policy/samples/new-zealand-ism.md) fornece detalhes sobre as definições de política incluídas neste projeto e como estas definições de política mapeiam para os **controlos** no Manual de Segurança da Informação da Nova Zelândia. Quando atribuídos a uma arquitetura, os recursos são avaliados pela Azure Policy para o incumprimento das definições políticas atribuídas. Para obter mais informações, veja [Azure Policy](../../policy/overview.md).

## <a name="deploy"></a>Implementar

Para implantar a amostra de planta restrita da Azure Blueprints New Zealand ISM, devem ser tomadas as seguintes medidas:

> [!div class="checklist"]
> - Criar um esquema novo a partir do exemplo
> - Marcar a cópia do exemplo como **Publicada**
> - Atribuir a cópia do esquema a uma subscrição já existente

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free) antes de começar.

### <a name="create-blueprint-from-sample"></a>Criar planta a partir de amostra

Primeiro, crie um esquema novo no ambiente utilizando o exemplo como ponto de partida, para implementar o esquema de exemplo.

1. Selecione **Todos os serviços** no painel esquerdo. Procure e selecione **Esquemas**.

1. Na página **Começar** à esquerda, selecione o botão **Criar** em _Criar um esquema_.

1. Encontre a amostra de planta **restrita ism da Nova Zelândia** sob _outras amostras_ e selecione **Utilize esta amostra.**

1. Introduza as _Informações Básicas_ do esquema de exemplo:

   - **Nome da planta**: Forneça um nome para a sua cópia da amostra de planta restrita ism da Nova Zelândia.
   - **Localização de definição**: Utilize a elipse e selecione o grupo de gestão para guardar a sua cópia da amostra para.

1. Selecione o separador _Artefactos_, na parte superior da página, ou **Seguinte: Artefactos**, na parte inferior.

1. Reveja a lista de artefactos incluídos na amostra de planta. Muitos dos artefactos têm parâmetros que vamos definir mais tarde. Quando terminar de rever o esquema de exemplo, selecione **Guardar Rascunho**.

### <a name="publish-the-sample-copy"></a>Publicar a cópia do exemplo

A cópia do esquema de exemplo está agora criada no seu ambiente. Está criada no modo **Rascunho** e tem de ser **Publicada** antes de poder ser atribuída e implementada. A cópia da amostra de planta pode ser personalizada para o seu ambiente e necessidades, mas essa modificação pode afastá-la do alinhamento com os controlos ISM Restritos da Nova Zelândia.

1. Selecione **Todos os serviços** no painel esquerdo. Procure e selecione **Esquemas**.

1. Selecione a página **Definições de esquema** à esquerda. Utilize os filtros para localizar a cópia do esquema de exemplo e selecione-a.

1. Selecione **Publicar esquema**, na parte superior da página. Na página nova à direita, indique uma **Versão** para a cópia do esquema de exemplo. Esta propriedade é útil se fizer modificações mais tarde. Fornecer **notas de alteração** como "Primeira versão publicada a partir da amostra de planta restrita do ISM da Nova Zelândia." Em seguida, selecione **Publicar**, na parte inferior da página.

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
|Ism da Nova Zelândia restrito|Atribuição de Política|Lista de utilizadores que devem ser incluídos no grupo de administradores do Windows VM|Uma lista separada de utilizadores que deve ser incluída no grupo local administradores; Ex: Administrador; myUser1; myUser2|
|Ism da Nova Zelândia restrito|Atribuição de Política|Lista de utilizadores que devem ser excluídos do grupo de administradores do Windows VM|Uma lista separada de utilizadores que deve ser excluída no grupo local administradores; Ex: Administrador; myUser1; myUser2|
|Ism da Nova Zelândia restrito|Atribuição de Política|Lista de utilizadores que o grupo de administradores do Windows VM deve incluir apenas|Uma lista separada de todos os membros esperados do grupo local administradores; Ex: Administrador; myUser1; myUser2|
|Ism da Nova Zelândia restrito|Atribuição de Política|ID do espaço de trabalho do Log Analytics para relatórios de agentes VM|ID (GUID) do espaço de trabalho Log Analytics onde os agentes VMs devem reportar|
|Ism da Nova Zelândia restrito|Atribuição de Política|Efeito para a política: Firewall de aplicação web (WAF) deve ser ativado para o serviço de porta frontal Azure|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Ism da Nova Zelândia restrito|Atribuição de Política|Efeito para a política: As definições de Avaliação de Vulnerabilidade para servidor SQL devem conter um endereço de e-mail para receber relatórios de digitalização|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Ism da Nova Zelândia restrito|Atribuição de Política|Efeito para a política: Recomendações de endurecimento da rede adaptativa devem ser aplicadas na internet face a máquinas virtuais|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Ism da Nova Zelândia restrito|Atribuição de Política|Efeito para a política: Deve haver mais de um proprietário atribuído à sua subscrição|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Ism da Nova Zelândia restrito|Atribuição de Política|Efeito para a política: A encriptação do disco deve ser aplicada em máquinas virtuais|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Ism da Nova Zelândia restrito|Atribuição de Política|Efeito para a política: Depuragem remota deve ser desligada para apps de funções|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Ism da Nova Zelândia restrito|Atribuição de Política|Efeito para a política: Firewall de aplicação web (WAF) deve utilizar o modo especificado para o Gateway de aplicação|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Ism da Nova Zelândia restrito|Atribuição de Política|Requisito do modo WAF para o Gateway de Aplicação|O modo de Prevenção ou Deteção deve ser ativado no serviço Application Gateway|
|Ism da Nova Zelândia restrito|Atribuição de Política|Efeito para a política: A encriptação transparente de dados nas bases de dados SQL deve ser ativada|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Ism da Nova Zelândia restrito|Atribuição de Política|Efeito para a política: A avaliação da vulnerabilidade deve ser ativada em casos geridos do SQL|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Ism da Nova Zelândia restrito|Atribuição de Política|Opcional: Lista de imagens VM personalizadas que apoiaram o Windows OS para adicionar ao âmbito adicional às imagens na galeria para a política: Implementar - Configure Dependency agent para ser ativado em máquinas virtuais do Windows|Para mais informações sobre a Configuração do Hóspede, visite [https://aka.ms/gcpol](https://aka.ms/gcpol)|
|Ism da Nova Zelândia restrito|Atribuição de Política|Efeito para a política: Um administrador do Azure Ative Directory deve ser previsto para servidores SQL|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Ism da Nova Zelândia restrito|Atribuição de Política|Efeito para a política: Apenas devem ser ativadas ligações seguras à sua Cache Azure para Redis|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Ism da Nova Zelândia restrito|Atribuição de Política|Efeito para a política: A solução de proteção endpoint deve ser instalada em conjuntos de escala de máquinas virtuais|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Ism da Nova Zelândia restrito|Atribuição de Política|Incluir servidores ligados ao Arco ao avaliar a política: Auditar máquinas windows que faltam qualquer um dos membros especificados no grupo de Administradores|Ao selecionar 'true', concorda em ser carregado mensalmente por máquina ligada ao Arco|
|Ism da Nova Zelândia restrito|Atribuição de Política|Opcional: Lista de imagens VM personalizadas que apoiaram o Windows OS para adicionar ao âmbito adicional às imagens na galeria para a política: [Pré-visualização]: O Agente de Análise de Registo deve ser ativado para imagens de máquinas virtuais listadas|Para mais informações sobre a Configuração do Hóspede, visite [https://aka.ms/gcpol](https://aka.ms/gcpol)|
|Ism da Nova Zelândia restrito|Atribuição de Política|Opcional: Lista de imagens VM personalizadas que apoiaram o Linux OS para adicionar ao âmbito adicional às imagens na galeria para a política: [Pré-visualização]: O Agente de Análise de Registo deve ser ativado para imagens de máquinas virtuais listadas|Para mais informações sobre a Configuração do Hóspede, visite [https://aka.ms/gcpol](https://aka.ms/gcpol)|
|Ism da Nova Zelândia restrito|Atribuição de Política|Efeito para a política: As contas de armazenamento devem restringir o acesso à rede|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Ism da Nova Zelândia restrito|Atribuição de Política|Opcional: Lista de imagens VM personalizadas que apoiaram o Windows OS para adicionar ao âmbito adicional às imagens na galeria para a política: Implementar - Configure Dependency agent para ser ativado em conjuntos de escala de máquina virtual Windows|Para mais informações sobre a Configuração do Hóspede, visite [https://aka.ms/gcpol](https://aka.ms/gcpol)|
|Ism da Nova Zelândia restrito|Atribuição de Política|Efeito para a política: As vulnerabilidades na configuração de segurança nos conjuntos de escala de máquinas virtuais devem ser remediadas|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Ism da Nova Zelândia restrito|Atribuição de Política|Incluir servidores ligados ao Arco ao avaliar a política: Auditar máquinas Windows que têm contas extra no grupo de Administradores|Ao selecionar 'true', concorda em ser carregado mensalmente por máquina ligada ao Arco|
|Ism da Nova Zelândia restrito|Atribuição de Política|Efeito para a política: A transferência segura para as contas de armazenamento deve ser ativada|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Ism da Nova Zelândia restrito|Atribuição de Política|Efeito para a política: Firewall de aplicação web (WAF) deve utilizar o modo especificado para O Serviço de Porta Frontal Azure|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Ism da Nova Zelândia restrito|Atribuição de Política|Requisito do modo WAF para O Serviço de Porta Frontal Azure|O modo de Prevenção ou Deteção deve ser ativado no serviço Azure Front Door|
|Ism da Nova Zelândia restrito|Atribuição de Política|Efeito para a política: Os controlos de aplicação adaptativos para a definição de aplicações seguras devem ser ativados nas suas máquinas|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Ism da Nova Zelândia restrito|Atribuição de Política|Efeito para a política: Um máximo de 3 proprietários deve ser designado para a sua subscrição|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Ism da Nova Zelândia restrito|Atribuição de Política|Efeito para a política: [Pré-visualização]: O acesso público da conta de armazenamento deve ser proibido|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Ism da Nova Zelândia restrito|Atribuição de Política|Efeito para a política: Uma solução de avaliação de vulnerabilidade deve ser ativada nas suas máquinas virtuais|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Ism da Nova Zelândia restrito|Atribuição de Política|Efeito para a política: Firewall de aplicação web (WAF) deve ser ativado para Gateway de aplicação|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Ism da Nova Zelândia restrito|Atribuição de Política|Efeito para a política: O CORS não deve permitir que todos os recursos acedam às suas Aplicações Web|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Ism da Nova Zelândia restrito|Atribuição de Política|Incluir servidores ligados ao Arco ao avaliar a política: Auditar servidores web do Windows que não estão a utilizar protocolos de comunicação seguros|Ao selecionar 'true', concorda em ser carregado mensalmente por máquina ligada ao Arco|
|Ism da Nova Zelândia restrito|Atribuição de Política|Versão TLS mínima para servidores web do Windows|Servidores web do Windows com versões TLS mais baixas serão avaliados como não conformes|
|Ism da Nova Zelândia restrito|Atribuição de Política|Opcional: Lista de imagens VM personalizadas que apoiaram o Linux OS para adicionar ao âmbito adicional às imagens na galeria para política: O agente Log Analytics deve ser ativado em conjuntos de escala de máquinas virtuais para imagens de máquinas virtuais listadas|Para mais informações sobre a Configuração do Hóspede, visite [https://aka.ms/gcpol](https://aka.ms/gcpol)|
|Ism da Nova Zelândia restrito|Atribuição de Política|Opcional: Lista de imagens VM personalizadas que apoiaram o Windows OS para adicionar ao âmbito adicional às imagens na galeria para política: O agente Log Analytics deve ser ativado em conjuntos de escala de máquinas virtuais para imagens de máquinas virtuais listadas|Para mais informações sobre a Configuração do Hóspede, visite [https://aka.ms/gcpol](https://aka.ms/gcpol)|
|Ism da Nova Zelândia restrito|Atribuição de Política|Efeito para a política: Contas externas com permissões de escrita devem ser removidas da sua subscrição|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Ism da Nova Zelândia restrito|Atribuição de Política|Incluir servidores ligados ao Arco ao avaliar a política: Auditar máquinas Windows que têm os membros especificados no grupo de Administradores|Ao selecionar 'true', concorda em ser carregado mensalmente por máquina ligada ao Arco|
|Ism da Nova Zelândia restrito|Atribuição de Política|Efeito para a política: As contas precodadas devem ser removidas da sua subscrição|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Ism da Nova Zelândia restrito|Atribuição de Política|Efeito para a política: A App de função só deve estar acessível através do HTTPS|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Ism da Nova Zelândia restrito|Atribuição de Política|Efeito para a política: As subscrições do Azure devem ter um perfil de registo para o Registo de Atividades|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Ism da Nova Zelândia restrito|Atribuição de Política|Lista de tipos de recursos que devem ter registos de diagnóstico ativados||
|Ism da Nova Zelândia restrito|Atribuição de Política|Efeito para a política: As atualizações do sistema devem ser instaladas nas suas máquinas|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Ism da Nova Zelândia restrito|Atribuição de Política|Efeito para a política: A versão TLS mais recente deve ser usada na sua App API|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Ism da Nova Zelândia restrito|Atribuição de Política|Efeito para a política: MFA deve ser ativado contas com permissões de escrita na sua subscrição|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Ism da Nova Zelândia restrito|Atribuição de Política|Efeito para a política: A extensão do Microsoft IaaSAntimalware deve ser implementada nos servidores do Windows|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Ism da Nova Zelândia restrito|Atribuição de Política|Efeito para a política: A Aplicação Web só deve ser acessível em HTTPS|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Ism da Nova Zelândia restrito|Atribuição de Política|Efeito para a política: Azure DDoS Protection Standard deve ser ativado|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Ism da Nova Zelândia restrito|Atribuição de Política|Efeito para a política: O MFA deve ser ativado em contas com permissões do proprietário na sua subscrição|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Ism da Nova Zelândia restrito|Atribuição de Política|Efeito para a política: A segurança avançada dos dados deve ser ativada nos seus servidores SQL|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Ism da Nova Zelândia restrito|Atribuição de Política|Efeito para a política: A segurança avançada dos dados deve ser ativada em SqL Managed Instance|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Ism da Nova Zelândia restrito|Atribuição de Política|Efeito para a política: Monitorar a falta de proteção de pontos finais no Centro de Segurança Azure|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Ism da Nova Zelândia restrito|Atribuição de Política|Efeito para a política: O registo de atividade deve ser mantido durante pelo menos um ano|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Ism da Nova Zelândia restrito|Atribuição de Política|Efeito para a política: As portas de gestão das máquinas virtuais devem ser protegidas com controlo de acesso à rede just-in-time|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Ism da Nova Zelândia restrito|Atribuição de Política|Efeito para a política: Os clusters de tecidos de serviço só devem utilizar o Azure Ative Directory para a autenticação do cliente|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Ism da Nova Zelândia restrito|Atribuição de Política|Efeito para a política: App API só deve estar acessível em HTTPS|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Ism da Nova Zelândia restrito|Atribuição de Política|Efeito para a política: Auditar máquinas Windows nas quais o Windows Defender Exploit Guard não está ativado|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Ism da Nova Zelândia restrito|Atribuição de Política|Incluir servidores ligados ao Arco ao avaliar a política: Auditar máquinas Windows nas quais o Windows Defender Exploit Guard não está ativado|Ao selecionar 'true', concorda em ser carregado mensalmente por máquina ligada ao Arco|
|Ism da Nova Zelândia restrito|Atribuição de Política|Estado de conformidade a reportar para máquinas Windows em que o Windows Defender Exploit Guard não está disponível|O Windows Defender Exploit Guard só está disponível a partir do Windows 10/Windows Server com a atualização 1709. A definição deste valor para 'Não Conforme' mostra máquinas com versões mais antigas nas quais o Windows Defender Exploit Guard não está disponível (como o Windows Server 2012 R2) como não conforme. A definição deste valor para 'Compliant' mostra estas máquinas como conformes.|
|Ism da Nova Zelândia restrito|Atribuição de Política|Efeito para a política: As atualizações do sistema em conjuntos de escala de máquinas virtuais devem ser instaladas|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Ism da Nova Zelândia restrito|Atribuição de Política|Efeito para a política: Depuragem remota deve ser desligada para aplicações web|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Ism da Nova Zelândia restrito|Atribuição de Política|Efeito para a política: As vulnerabilidades na configuração de segurança nas suas máquinas devem ser remediadas|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Ism da Nova Zelândia restrito|Atribuição de Política|Efeito para a política: O MFA deve ser ativado em contas com permissões de leitura na sua subscrição|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Ism da Nova Zelândia restrito|Atribuição de Política|Efeito para a política: As vulnerabilidades nas configurações de segurança dos contentores devem ser remediadas|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Ism da Nova Zelândia restrito|Atribuição de Política|Efeito para a política: Depuragem remota deve ser desligada para apps da API|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Ism da Nova Zelândia restrito|Atribuição de Política|Efeito para a política: Auditar máquinas Linux que permitem ligações remotas a partir de contas sem senhas|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Ism da Nova Zelândia restrito|Atribuição de Política|Incluir servidores ligados ao Arco ao avaliar a política: Auditar máquinas Linux que permitem ligações remotas a partir de contas sem palavras-passe|Ao selecionar 'true', concorda em ser carregado mensalmente por máquina ligada ao Arco|
|Ism da Nova Zelândia restrito|Atribuição de Política|Efeito para a política: As contas precodadas com permissões do proprietário devem ser removidas da sua subscrição|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Ism da Nova Zelândia restrito|Atribuição de Política|Efeito para a política: A avaliação da vulnerabilidade deve ser ativada nos seus servidores SQL|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Ism da Nova Zelândia restrito|Atribuição de Política|Efeito para a política: A versão TLS mais recente deve ser usada na sua Web App|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Ism da Nova Zelândia restrito|Atribuição de Política|Efeito para a política: As máquinas windows devem satisfazer os requisitos para "Definições de Segurança - Políticas de Conta"|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Ism da Nova Zelândia restrito|Atribuição de Política|Imponha o histórico de palavras-passe para contas locais do Windows VM|Especifica limites à reutilização de palavras-passe - quantas vezes uma nova palavra-passe deve ser criada para uma conta de utilizador antes de a palavra-passe poder ser repetida|
|Ism da Nova Zelândia restrito|Atribuição de Política|Incluir servidores ligados ao Arco na avaliação da política: As máquinas do Windows devem satisfazer os requisitos de "Definições de Segurança - Políticas de Conta"|Ao selecionar 'true', concorda em ser carregado mensalmente por máquina ligada ao Arco|
|Ism da Nova Zelândia restrito|Atribuição de Política|Idade máxima da palavra-passe para contas locais do Windows VM|Especifica o número máximo de dias que podem decorrer antes de uma palavra-passe da conta de utilizador ser alterada; o formato do valor são dois inteiros separados por uma vírgula, denotando uma gama inclusiva|
|Ism da Nova Zelândia restrito|Atribuição de Política|Idade mínima de senha para contas locais do Windows VM|Especifica o número mínimo de dias que deve decorrer antes de uma palavra-passe da conta de utilizador poder ser alterada|
|Ism da Nova Zelândia restrito|Atribuição de Política|Comprimento mínimo de senha para contas locais do Windows VM|Especifica o número mínimo de caracteres que uma palavra-passe da conta de utilizador pode conter|
|Ism da Nova Zelândia restrito|Atribuição de Política|A palavra-passe deve satisfazer os requisitos de complexidade das contas locais do Windows VM|Especifica se a palavra-passe da conta de utilizador deve ser complexa; Se necessário, uma palavra-passe complexa não deve conter parte do nome da conta do utilizador ou nome completo; ter pelo menos 6 caracteres de comprimento; contêm uma mistura de caracteres maiúsculos, minúsculos, número e não alfabéticos|
|Ism da Nova Zelândia restrito|Atribuição de Política|Efeito para a política: As máquinas virtuais viradas para a Internet devem ser protegidas com grupos de segurança de rede|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Ism da Nova Zelândia restrito|Atribuição de Política|Efeito para a política: Auditar máquinas Linux que têm contas sem senhas|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Ism da Nova Zelândia restrito|Atribuição de Política|Incluir servidores ligados ao Arco ao avaliar a política: Auditar máquinas Linux que têm contas sem senhas|Ao selecionar 'true', concorda em ser carregado mensalmente por máquina ligada ao Arco|
|Ism da Nova Zelândia restrito|Atribuição de Política|Efeito para a política: Contas externas com permissões do proprietário devem ser removidas da sua subscrição|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Ism da Nova Zelândia restrito|Atribuição de Política|Efeito para a política: A versão TLS mais recente deve ser usada na sua App de Função|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Ism da Nova Zelândia restrito|Atribuição de Política|Efeito para a política: [Pré-visualização]: Todo o tráfego de Internet deve ser encaminhado através do seu Azure Firewall implantado|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|
|Ism da Nova Zelândia restrito|Atribuição de Política|Efeito para a política: As vulnerabilidades nas bases de dados SQL devem ser remediadas|Para mais informações sobre efeitos, visite [https://aka.ms/policyeffects](https://aka.ms/policyeffects)|

## <a name="next-steps"></a>Passos seguintes

Artigos adicionais sobre esquemas e como os utilizar:

- Saiba mais sobre o [ciclo de vida do esquema](../concepts/lifecycle.md).
- Compreenda como utilizar [parâmetros estáticos e dinâmicos](../concepts/parameters.md).
- Aprenda a personalizar a [ordem de sequenciação do esquema](../concepts/sequencing-order.md).
- Saiba como utilizar o [bloqueio de recursos de esquema](../concepts/resource-locking.md).
- Saiba como [atualizar as atribuições existentes](../how-to/update-existing-assignments.md).