---
title: Implementar amostra de projeto iso 27001 serviços partilhados
description: Implementar etapas para a amostra de planta de serviços partilhados ISO 27001, incluindo detalhes do parâmetro do artefacto da planta.
ms.date: 02/05/2021
ms.topic: sample
ms.openlocfilehash: 2eb9ed2199b28ad7cb349e6088c3cc583d8f011d
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/17/2021
ms.locfileid: "100577314"
---
# <a name="deploy-the-iso-27001-shared-services-blueprint-sample"></a>Implementar a amostra de projeto iso 27001 serviços partilhados

Para implementar a amostra de projeto da Azure Blueprints ISO 27001, devem ser tomadas as seguintes medidas:

> [!div class="checklist"]
> - Criar um esquema novo a partir do exemplo
> - Marcar a cópia do exemplo como **Publicada**
> - Atribuir a cópia do esquema a uma subscrição já existente

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free) antes de começar.

## <a name="create-blueprint-from-sample"></a>Criar planta a partir de amostra

Primeiro, crie um esquema novo no ambiente utilizando o exemplo como ponto de partida, para implementar o esquema de exemplo.

1. Selecione **Todos os serviços** no painel esquerdo. Procure e selecione **Esquemas**.

1. Na página **Começar** à esquerda, selecione o botão **Criar** em _Criar um esquema_.

1. Encontre a amostra de planta **ISO 27001: Shared Services** em _outras amostras_ e selecione **Utilize esta amostra**.

1. Introduza as _Informações Básicas_ do esquema de exemplo:

   - **Nome da planta**: Forneça um nome para a sua cópia da amostra de planta iso 27001 Dos Serviços Partilhados.
   - **Localização de definição**: Utilize a elipse e selecione o grupo de gestão para guardar a sua cópia da amostra para.

1. Selecione o separador _Artefactos_, na parte superior da página, ou **Seguinte: Artefactos**, na parte inferior.

1. Reveja a lista de artefactos que compõem o esquema de exemplo. Muitos dos artefactos têm parâmetros que vamos definir mais tarde. Quando terminar de rever o esquema de exemplo, selecione **Guardar Rascunho**.

## <a name="publish-the-sample-copy"></a>Publicar a cópia do exemplo

A cópia do esquema de exemplo está agora criada no seu ambiente. Está criada no modo **Rascunho** e tem de ser **Publicada** antes de poder ser atribuída e implementada. A cópia da amostra de plantas pode ser personalizada para o seu ambiente e necessidades, mas essa modificação pode afastá-la da norma ISO 27001.

1. Selecione **Todos os serviços** no painel esquerdo. Procure e selecione **Esquemas**.

1. Selecione a página **Definições de esquema** à esquerda. Utilize os filtros para localizar a cópia do esquema de exemplo e selecione-a.

1. Selecione **Publicar esquema**, na parte superior da página. Na página nova à direita, indique uma **Versão** para a cópia do esquema de exemplo. Esta propriedade é útil se fizer modificações mais tarde. Fornecer **notas de alteração** tais como "Primeira versão publicada a partir da amostra de planta ISO 27001." Em seguida, selecione **Publicar**, na parte inferior da página.

## <a name="assign-the-sample-copy"></a>Atribuir a cópia de exemplo

Uma vez publicada com **sucesso** a cópia da amostra de plantas, pode ser atribuída a uma subscrição dentro do grupo de gestão a que foi guardada. É neste passo que são fornecidos os parâmetros que fazem com que cada implementação da cópia do esquema de exemplo seja única.

1. Selecione **Todos os serviços** no painel esquerdo. Procure e selecione **Esquemas**.

1. Selecione a página **Definições de esquema** à esquerda. Utilize os filtros para localizar a cópia do esquema de exemplo e selecione-a.

1. Selecione **Atribuir esquema**, na parte superior da página de definição do esquema.

1. Indique os valores dos parâmetros para a atribuição do esquema:

   - Noções básicas

     - **Subscrições**: Selecione uma ou mais das subscrições que estão no grupo de gestão a que guardou a sua cópia da amostra de planta. Se selecionar mais de uma subscrição, é criada uma atribuição para cada uma mediante a utilização dos parâmetros introduzidos.
     - **Nome da atribuição**: O nome é pré-povoado para si com base no nome da planta.
       Mude-o se necessário ou deixe-o como está.
     - **Localização**: Selecione uma região para a identidade gerida a criar. O Azure Blueprint utiliza esta identidade gerida para implementar todos os artefactos no esquema atribuído. Para saber mais, consulte [identidades geridas para recursos Azure.](../../../../active-directory/managed-identities-azure-resources/overview.md)
     - **Versão de definição de planta**: Escolha uma versão **publicada** da sua cópia da amostra de planta.

   - Atribuição de bloqueio

     Selecione a definição de bloqueio do esquema no seu ambiente. Para obter mais informações, veja [bloqueio de recurso em esquemas](../../concepts/resource-locking.md).

   - Identidade Gerida

     Deixe a opção de identidade gerida _por defeito atribuída._

   - Parâmetros de esquema

     Os parâmetros definidos nesta secção são utilizados por muitos dos artefactos na definição do esquema, para proporcionar consistência.

     - **Nome da organização**: Introduza um nome curto para a sua organização. Esta propriedade é usada principalmente para nomear recursos.
     - **Prefixo do endereço do endereço de sub-rede de serviços partilhados**: Forneça o valor de notação CIDR para a rede dos recursos implantados em conjunto.
     - **Localização dos serviços partilhados**: Determina a localização para a qual os artefactos são implantados. Nem todos os serviços estão disponíveis em todos os locais. Os artefactos que implantam esses serviços fornecem uma opção de parâmetro para a localização para implantar esse artefacto.
     - **Localização permitida (Política: Iniciativa blueprint para a ISO 27001)**: Valor que indica as localizações permitidas para grupos de recursos e recursos.
     - **Log Analytics espaço de trabalho para agentes VM (Policy: Blueprint initiative for ISO 27001)**: Especifica o ID de recurso de um espaço de trabalho. Este parâmetro utiliza uma `concat` função para construir o ID de recurso.

   - Parâmetros dos artefactos

     Os parâmetros definidos nesta secção aplicam-se ao artefacto no qual são definidos. Estes parâmetros são [parâmetros dinâmicos,](../../concepts/parameters.md#dynamic-parameters) uma vez que são definidos durante a atribuição da planta. Para obter uma lista completa ou parâmetros de artefactos e suas descrições, consulte [a tabela de parâmetros do Artefacto](#artifact-parameters-table).

1. Depois de introduzidos todos os parâmetros, selecione **Atribuir**, na parte inferior da página. A atribuição do esquema é criada e a implementação do artefacto inicia-se. A implantação leva cerca de uma hora. Para verificar o estado, abra a atribuição do esquema.

> [!WARNING]
> O serviço Azure Blueprints e os esquemas de exemplo incorporados são **gratuitos**. Os preços dos recursos do Azure são os [preços por produto](https://azure.microsoft.com/pricing/). Utilize a [calculadora de preços](https://azure.microsoft.com/pricing/calculator/) para prever o custo da execução de recursos implementados por este esquema de exemplo.

## <a name="artifact-parameters-table"></a>Tabela de parâmetros dos artefactos

A tabela seguinte mostra uma lista dos parâmetros dos artefactos de esquema:

|Nome do artefacto|Tipo de artefacto|Nome do parâmetro|Descrição|
|-|-|-|-|
|\[Pré-visualização \] : Implementar o agente de análise de registo para conjuntos de escalas De VM Linux (VMSS)|Atribuição de políticas|Opcional: Lista de imagens VM que apoiaram o Linux OS para adicionar ao âmbito|(Opcional) O valor predefinido é _["nenhum"]_.|
|\[Pré-visualização \] : Implementar agente de análise de registo para VMs Linux|Atribuição de políticas|Opcional: Lista de imagens VM que apoiaram o Linux OS para adicionar ao âmbito|(Opcional) O valor predefinido é _["nenhum"]_.|
|\[Pré-visualização \] : Implementar o agente de análise de registo para conjuntos de escala vm do Windows (VMSS)|Atribuição de políticas|Opcional: Lista de imagens VM que apoiaram o Windows OS para adicionar ao âmbito|(Opcional) O valor predefinido é _["nenhum"]_.|
|\[Pré-visualização \] : Implementar agente de análise de registo para VMs do Windows|Atribuição de políticas|Opcional: Lista de imagens VM que apoiaram o Windows OS para adicionar ao âmbito|(Opcional) O valor predefinido é _["nenhum"]_.|
|Tipos de recursos permitidos|Atribuição de políticas|Tipos de recursos permitidos|Lista de tipos de recursos autorizados a ser implantados. Esta lista é composta por todos os tipos de recursos implantados em Serviços Partilhados.|
|SKUs de contas de armazenamento permitidos|Atribuição de políticas|SKUs de armazenamento permitido|Lista de registos de diagnóstico de conta de armazenamento SKUs permitida. O valor predefinido é _["Standard_LRS"]_.|
|SKUs de máquina virtual permitida|Atribuição de políticas|Lista de máquinas virtuais SKUs permitidas para ser implantada. O valor predefinido é _["Standard_DS1_v2", "Standard_DS2_v2"]_.|
|Iniciativa de planta para a ISO 27001|Atribuição de políticas|Tipos de recursos para auditar registos de diagnóstico|Lista de tipos de recursos para auditar se a definição de registo de diagnóstico não estiver ativada. Os valores aceitáveis podem ser encontrados nos [esquemas de registos de diagnóstico do Azure Monitor](../../../../azure-monitor/essentials/resource-logs-schema.md#service-specific-schemas).|
|Grupo de recursos Log Analytics|Grupo de recursos|Name|**Locked** - Concatenates o **nome da Organização** para tornar o grupo de recursos `-sharedsvsc-log-rg` único.|
|Grupo de recursos Log Analytics|O grupo de recursos|A localização|**Bloqueado** - Usa o parâmetro da planta.|
|Modelo de Analítica de Log|Modelo do Resource Manager|Escalão de serviço|Define o nível do espaço de trabalho Log Analytics. O valor predefinido é _PerNode_.|
|Modelo de Analítica de Log|Modelo do Resource Manager|Retenção de registos em dias|Retenção de dados em dias. O valor predefinido é _de 365_.|
|Modelo de Analítica de Log|Modelo do Resource Manager|Localização|Região utilizada para criar o espaço de trabalho Log Analytics. O valor predefinido é _West US 2_.|
|Grupo de recursos de rede|Grupo de recursos|Name|**Locked** - Concatenates o **nome da Organização** para tornar o grupo de recursos `-sharedsvcs-net-rg` único.|
|Grupo de recursos de rede|O grupo de recursos|A localização|**Bloqueado** - Usa o parâmetro da planta.|
|Modelo de firewall Azure|Modelo do Resource Manager|IP privado de firewall Azure|Configura o IP privado da [firewall Azure](../../../../firewall/overview.md). Este valor também é usado como tabela de rotas predefinidos na sub-rede de serviços partilhados. Deve fazer parte da notação CIDR definida no **prefixo do endereço do sub-redes Azure Firewall**. O valor predefinido é _de 10.0.4.4_.|
|Modelo de firewall Azure|Modelo do Resource Manager|Retenção de registos em dias|Retenção de dados em dias. O valor predefinido é _de 365_.|
|Modelo de grupo de segurança de rede|Modelo do Resource Manager|Retenção de registos em dias|Retenção de dados em dias. O valor predefinido é _de 365_.|
|Modelo de tabela de rede virtual e rota|Modelo do Resource Manager|Prefixo de endereço de rede virtual|A notação CIDR para a rede virtual. O valor predefinido é _de 10.0.0.0/16_.|
|Modelo de tabela de rede virtual e rota|Modelo do Resource Manager|Ativar a proteção DDoS de rede virtual|Configura a proteção DDoS para a rede virtual. O valor predefinido é _verdadeiro._|
|Modelo de tabela de rede virtual e rota|Modelo do Resource Manager|Prefixo do endereço do endereço de sub-rede de serviços partilhados|A notação CIDR para a sub-rede de Serviços Partilhados. O valor predefinido é _de 10.0.0.0/24_.|
|Modelo de tabela de rede virtual e rota|Modelo do Resource Manager|Prefixo do endereço de sub-rede DMZ|A notação CIDR para a sub-rede DMZ. O valor predefinido é _de 10.0.1.0/24_.|
|Modelo de tabela de rede virtual e rota|Modelo do Resource Manager|Prefixo do endereço do endereço do gateway de aplicação|A notação CIDR para a sub-rede de gateway de aplicação. O valor predefinido é _de 10.0.2.0/24_.|
|Modelo de tabela de rede virtual e rota|Modelo do Resource Manager|Prefixo do endereço de sub-rede virtual Gateway|A notação CIDR para a sub-rede virtual gateway. O valor predefinido é _de 10.0.3.0/24_.|
|Modelo de tabela de rede virtual e rota|Modelo do Resource Manager|Prefixo do endereço do endereço da sub-rede Azure Firewall|A notação CIDR para a sub-rede [de firewall Azure.](../../../../firewall/overview.md) Deve incluir o parâmetro **IP privado de firewall Azure.**|
|Grupo de recursos Key Vault|Grupo de recursos|Name|**Locked** - Concatenates o **nome da Organização** para tornar o grupo de recursos `-sharedsvcs-kv-rg` único.|
|Grupo de recursos Key Vault|O grupo de recursos|A localização|**Bloqueado** - Usa o parâmetro da planta.|
|Modelo de abóbada de chave|Modelo do Resource Manager|Nome de utilizador de administração jumpbox|Nome de utilizador para a caixa de salto. Deve corresponder ao mesmo valor de propriedade no **modelo Jumpbox**. O valor predefinido é _jb-administrador-utilizador_.|
|Modelo de abóbada de chave|Modelo do Resource Manager|Chave de administração de jumpbox ou senha|Chave ou senha para a conta no jumpbox. Deve corresponder ao mesmo valor de propriedade no **modelo Jumpbox**. Sem valor padrão e não pode ser deixado em branco.|
|Modelo de abóbada de chave|Modelo do Resource Manager|Nome de utilizador de administração de domínio|O nome de utilizador usado para aceder a VM do Ative Directory e para juntar outros VMs a um domínio. Deve coincidir com o valor da propriedade **do utilizador do domínio** no modelo de **Serviços de Domínio do Diretório Ativo.** O valor predefinido é _o utilizador de administração de domínios._|
|Modelo de abóbada de chave|Modelo do Resource Manager|Senha de administração de domínio|Senha do utilizador do administrador de domínio. Sem valor padrão e não pode ser deixado em branco.|
|Modelo de abóbada de chave|Modelo do Resource Manager|ID de objeto AAD|O identificador de objetos AAD da conta que requer acesso à instância key vault. Sem valor padrão e não pode ser deixado em branco. Para localizar este valor a partir do portal Azure, procure e selecione "Utilizadores" em _Serviços_. Utilize a caixa _Nome_ para filtrar o nome da conta e selecione essa conta. Na página de perfil do _Utilizador,_ selecione o ícone "Clique para copiar" ao lado do ID do _objeto._  |
|Modelo de abóbada de chave|Modelo do Resource Manager|Retenção de registos em dias|Retenção de dados em dias. O valor predefinido é _de 365_.|
|Modelo de abóbada de chave|Modelo do Resource Manager|SKU cofre-chave|Especifica o SKU do Cofre chave que é criado. O valor predefinido é _Premium._|
|Grupo de recursos Jumpbox|Grupo de recursos|Name|**Locked** - Concatenates o **nome da Organização** para tornar o grupo de recursos `-sharedsvcs-jb-rg` único.|
|Grupo de recursos Jumpbox|O grupo de recursos|A localização|**Bloqueado** - Usa o parâmetro da planta.|
|Modelo de caixa de salto|Modelo do Resource Manager|Nome de utilizador de administração jumpbox|O nome de utilizador usado para aceder a VMs de caixa de salto. Deve corresponder ao mesmo valor de propriedade no **modelo key vault**. O valor predefinido é _jb-administrador-utilizador_.|
|Modelo de caixa de salto|Modelo do Resource Manager|Senha de administração jumpbox (ID de recurso do cofre chave)|A identificação de recursos do Cofre de Chaves. Utilize "/subscrições/{subscriçãoId}/resourceGroups/{orgName}-sharedsvcs-kv-rg/providers/Microsoft.KeyVault/vaults/{orgName}-sharedsvcs-kv" e `{subscriptionId}` substitua-o pelo seu ID de subscrição e `{orgName}` pelo parâmetro de planta do nome da **Organização.**|
|Modelo de caixa de salto|Modelo do Resource Manager|Senha de administração jumpbox (Nome Secreto do Cofre de Chaves)|Nome de utilizador do administrador da caixa de salto. Deve corresponder o valor na propriedade **do modelo do Key Vault** Jumpbox nome de utilizador de **administração**.|
|Modelo de caixa de salto|Modelo do Resource Manager|Sistema operativo Jumpbox|Determina o sistema operativo do VM da caixa de salto. O valor predefinido é _o Windows_.|
|Grupo de recursos de serviços de domínio de diretório ativo|Grupo de recursos|Name|**Locked** - Concatenates o **nome da Organização** para tornar o grupo de recursos `-sharedsvcs-adds-rg` único.|
|Grupo de recursos de serviços de domínio de diretório ativo|O grupo de recursos|A localização|**Bloqueado** - Usa o parâmetro da planta.|
|Modelo de serviços de domínio de diretório ativo|Modelo do Resource Manager|Nome de utilizador de administração de domínio|Nome de utilizador para a caixa de salto ADDS. Deve corresponder ao mesmo valor de propriedade no **modelo key vault**. O valor predefinido é _o utilizador de administradores adicionais._|
|Modelo de serviços de domínio de diretório ativo|Modelo do Resource Manager|Senha de administração de domínio (ID de recurso do cofre chave)|A identificação de recursos do Cofre de Chaves. Utilize "/subscrições/{subscriçãoId}/resourceGroups/{orgName}-sharedsvcs-kv-rg/providers/Microsoft.KeyVault/vaults/{orgName}-sharedsvcs-kv" e `{subscriptionId}` substitua-o pelo seu ID de subscrição e `{orgName}` pelo parâmetro de planta do nome da **Organização.**|
|Modelo de serviços de domínio de diretório ativo|Modelo do Resource Manager|Senha de administração de domínio (Nome Secreto do Cofre de Chaves)|Nome de utilizador do administrador de domínio. Deve corresponder o valor na propriedade **do modelo do cofre de chaves** nome de **dados de administração** de domínio .|
|Modelo de serviços de domínio de diretório ativo|Modelo do Resource Manager|Nome de domínio|Nome do Diretório Ativo criado pela amostra. O valor predefinido é _contoso.com_.|
|Modelo de serviços de domínio de diretório ativo|Modelo do Resource Manager|Utilizador de administração de domínio|Nome de utilizador para a conta AD administração e para unir dispositivos ao domínio AD. Deve coincidir com o valor da propriedade do nome de utilizador do administrador **AD** no **modelo key vault**. O valor predefinido é _o utilizador de administração de domínios._|
|Modelo de serviços de domínio de diretório ativo|Modelo do Resource Manager|Senha de administração de domínio|Desa estada os detalhes do Cofre de Chaves para armazenar a palavra-passe. Sem valor padrão e não pode ser deixado em branco.|

## <a name="next-steps"></a>Passos seguintes

Agora que reviu os passos para implementar a amostra de projeto iso 27001 dos Serviços Partilhados, visite os seguintes artigos para saber sobre a arquitetura e o mapeamento de controlo:

> [!div class="nextstepaction"]
> [Projeto de serviços partilhados ISO 27001 - Visão geral](./index.md) 
>  [Projeto iso 27001 Serviços Partilhados - Controlo de mapeamento](./control-mapping.md)

Artigos adicionais sobre esquemas e como os utilizar:

- Saiba mais sobre o [ciclo de vida do esquema](../../concepts/lifecycle.md).
- Compreenda como utilizar [parâmetros estáticos e dinâmicos](../../concepts/parameters.md).
- Aprenda a personalizar a [ordem de sequenciação do esquema](../../concepts/sequencing-order.md).
- Saiba como utilizar o [bloqueio de recursos de esquema](../../concepts/resource-locking.md).
- Saiba como [atualizar as atribuições existentes](../../how-to/update-existing-assignments.md).
