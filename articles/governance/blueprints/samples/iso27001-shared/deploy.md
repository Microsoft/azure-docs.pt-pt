---
title: Implementar amostra de projeto iso 27001 serviços partilhados
description: Implementar etapas para a amostra de planta de serviços partilhados ISO 27001, incluindo detalhes do parâmetro do artefacto da planta.
ms.date: 01/13/2020
ms.topic: sample
ms.openlocfilehash: 19e394f115c87c499f7839c6ef63921e68f4d357
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.contentlocale: pt-PT
ms.lasthandoff: 07/07/2020
ms.locfileid: "86044707"
---
# <a name="deploy-the-iso-27001-shared-services-blueprint-sample"></a>Implementar a amostra de projeto iso 27001 serviços partilhados

Para implementar a amostra de projeto da Azure Blueprints ISO 27001, devem ser tomadas as seguintes medidas:

> [!div class="checklist"]
> - Criar uma nova planta a partir da amostra
> - Marque a sua cópia da amostra como **Publicada**
> - Atribua a sua cópia da planta a uma subscrição existente

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free) antes de começar.

## <a name="create-blueprint-from-sample"></a>Criar planta a partir de amostra

Em primeiro lugar, implemente a amostra de planta criando uma nova planta no seu ambiente usando a amostra como entrada.

1. Selecione **Todos os serviços** no painel esquerdo. Procure e selecione **Plantas.**

1. A partir da página **'Iniciar'** à esquerda, selecione o botão **Criar** _uma plantação_.

1. Encontre a amostra de planta **ISO 27001: Shared Services** em _outras amostras_ e selecione **Utilize esta amostra**.

1. Introduza os _fundamentos_ da amostra da planta:

   - **Nome da planta**: Forneça um nome para a sua cópia da amostra de planta iso 27001 Dos Serviços Partilhados.
   - **Localização de definição**: Utilize a elipse e selecione o grupo de gestão para guardar a sua cópia da amostra para.

1. Selecione o _separador Artefactos_ na parte superior da página ou **seguinte: Artefactos** na parte inferior da página.

1. Reveja a lista de artefactos que compõem a amostra de planta. Muitos dos artefactos têm parâmetros que definiremos mais tarde. **Selecione Save Draft** quando terminar de rever a amostra de planta.

## <a name="publish-the-sample-copy"></a>Publique a cópia da amostra

A sua cópia da amostra foi agora criada no seu ambiente. É criado no modo **Draft** e deve ser **publicado** antes de poder ser atribuído e implementado. A cópia da amostra de plantas pode ser personalizada para o seu ambiente e necessidades, mas essa modificação pode afastá-la da norma ISO 27001.

1. Selecione **Todos os serviços** no painel esquerdo. Procure e selecione **Plantas.**

1. Selecione a página **de definições de Planta** à esquerda. Utilize os filtros para encontrar a sua cópia da amostra de planta e, em seguida, selecione-a.

1. **Selecione Publicar** a planta no topo da página. Na nova página à direita, forneça uma **versão** para a sua cópia da amostra de planta. Esta propriedade é útil para se você fizer uma modificação mais tarde. Fornecer **notas de alteração** tais como "Primeira versão publicada a partir da amostra de planta ISO 27001." Em seguida, **selecione Publicar** na parte inferior da página.

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

   - Parâmetros de esquema

     Os parâmetros definidos nesta secção são usados por muitos dos artefactos na definição de planta para fornecer consistência.

     - **Nome da organização**: Introduza um nome curto para a sua organização. Esta propriedade é usada principalmente para nomear recursos.
     - **Prefixo do endereço do endereço de sub-rede de serviços partilhados**: Forneça o valor de notação CIDR para a rede dos recursos implantados em conjunto.
     - **Localização dos serviços partilhados**: Determina a localização para a qual os artefactos são implantados. Nem todos os serviços estão disponíveis em todos os locais. Os artefactos que implantam esses serviços fornecem uma opção de parâmetro para a localização para implantar esse artefacto.
     - **Localização permitida (Política: Iniciativa blueprint para a ISO 27001)**: Valor que indica as localizações permitidas para grupos de recursos e recursos.
     - **Log Analytics espaço de trabalho para agentes VM (Policy: Blueprint initiative for ISO 27001)**: Especifica o ID de recurso de um espaço de trabalho. Este parâmetro utiliza uma `concat` função para construir o ID de recurso.

   - Parâmetros de artefactos

     Os parâmetros definidos nesta secção aplicam-se ao artefacto sob o qual é definido. Estes parâmetros são [parâmetros dinâmicos,](../../concepts/parameters.md#dynamic-parameters) uma vez que são definidos durante a atribuição da planta. Para obter uma lista completa ou parâmetros de artefactos e suas descrições, consulte [a tabela de parâmetros do Artefacto](#artifact-parameters-table).

1. Uma vez introduzidos todos os parâmetros, **selecione Atribuir** na parte inferior da página. A atribuição da planta é criada e a implantação de artefactos começa. A implantação leva cerca de uma hora. Para verificar o estado de implantação, abra a atribuição do projeto.

> [!WARNING]
> O serviço Azure Blueprints e as amostras de plantas incorporadas estão **livres de custos.** Os recursos azure são [avaliados pelo produto.](https://azure.microsoft.com/pricing/) Utilize a [calculadora de preços](https://azure.microsoft.com/pricing/calculator/) para estimar o custo de funcionamento dos recursos utilizados por esta amostra de planta.

## <a name="artifact-parameters-table"></a>Tabela de parâmetros de artefacto

A tabela a seguir fornece uma lista dos parâmetros do artefacto da planta:

|Nome do artefacto|Tipo de artefacto|Nome do parâmetro|Descrição|
|-|-|-|-|
|\[Pré-visualização \] : Implementar o agente de análise de registo para conjuntos de escalas De VM Linux (VMSS)|Atribuição de política|Opcional: Lista de imagens VM que apoiaram o Linux OS para adicionar ao âmbito|(Opcional) O valor predefinido é _["nenhum"]_.|
|\[Pré-visualização \] : Implementar agente de análise de registo para VMs Linux|Atribuição de política|Opcional: Lista de imagens VM que apoiaram o Linux OS para adicionar ao âmbito|(Opcional) O valor predefinido é _["nenhum"]_.|
|\[Pré-visualização \] : Implementar o agente de análise de registo para conjuntos de escala vm do Windows (VMSS)|Atribuição de política|Opcional: Lista de imagens VM que apoiaram o Windows OS para adicionar ao âmbito|(Opcional) O valor predefinido é _["nenhum"]_.|
|\[Pré-visualização \] : Implementar agente de análise de registo para VMs do Windows|Atribuição de política|Opcional: Lista de imagens VM que apoiaram o Windows OS para adicionar ao âmbito|(Opcional) O valor predefinido é _["nenhum"]_.|
|Tipos de recursos permitidos|Atribuição de política|Tipos de recursos permitidos|Lista de tipos de recursos autorizados a ser implantados. Esta lista é composta por todos os tipos de recursos implantados em Serviços Partilhados.|
|SKUs de contas de armazenamento permitidos|Atribuição de política|SKUs de armazenamento permitido|Lista de registos de diagnóstico de conta de armazenamento SKUs permitida. O valor predefinido é _["Standard_LRS"]_.|
|SKUs de máquina virtual permitida|Atribuição de política|Lista de máquinas virtuais SKUs permitidas para ser implantada. O valor predefinido é _["Standard_DS1_v2", "Standard_DS2_v2"]_.|
|Iniciativa de planta para a ISO 27001|Atribuição de política|Tipos de recursos para auditar registos de diagnóstico|Lista de tipos de recursos para auditar se a definição de registo de diagnóstico não estiver ativada. Os valores aceitáveis podem ser encontrados nos [esquemas de registos de diagnóstico do Azure Monitor](../../../../azure-monitor/platform/resource-logs-schema.md#service-specific-schemas).|
|Grupo de recursos Log Analytics|Grupo de recursos|Name|**Locked** - Concatenates o **nome da Organização** para tornar o grupo de recursos `-sharedsvsc-log-rg` único.|
|Grupo de recursos Log Analytics|Grupo de recursos|Localização|**Bloqueado** - Usa o parâmetro da planta.|
|Modelo de Analítica de Log|Modelo do Resource Manager|Camada de serviços|Define o nível do espaço de trabalho Log Analytics. O valor predefinido é _PerNode_.|
|Modelo de Analítica de Log|Modelo do Resource Manager|Retenção de registos em dias|Retenção de dados em dias. O valor predefinido é _de 365_.|
|Modelo de Analítica de Log|Modelo do Resource Manager|Localização|Região utilizada para criar o espaço de trabalho Log Analytics. O valor predefinido é _West US 2_.|
|Grupo de recursos de rede|Grupo de recursos|Name|**Locked** - Concatenates o **nome da Organização** para tornar o grupo de recursos `-sharedsvcs-net-rg` único.|
|Grupo de recursos de rede|Grupo de recursos|Localização|**Bloqueado** - Usa o parâmetro da planta.|
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
|Grupo de recursos Key Vault|Grupo de recursos|Localização|**Bloqueado** - Usa o parâmetro da planta.|
|Modelo de abóbada de chave|Modelo do Resource Manager|Nome de utilizador de administração jumpbox|Nome de utilizador para a caixa de salto. Deve corresponder ao mesmo valor de propriedade no **modelo Jumpbox**. O valor predefinido é _jb-administrador-utilizador_.|
|Modelo de abóbada de chave|Modelo do Resource Manager|Chave de administração de jumpbox ou senha|Chave ou senha para a conta no jumpbox. Deve corresponder ao mesmo valor de propriedade no **modelo Jumpbox**. Sem valor padrão e não pode ser deixado em branco.|
|Modelo de abóbada de chave|Modelo do Resource Manager|Nome de utilizador de administração de domínio|O nome de utilizador usado para aceder a VM do Ative Directory e para juntar outros VMs a um domínio. Deve coincidir com o valor da propriedade **do utilizador do domínio** no modelo de **Serviços de Domínio do Diretório Ativo.** O valor predefinido é _o utilizador de administração de domínios._|
|Modelo de abóbada de chave|Modelo do Resource Manager|Senha de administração de domínio|Senha do utilizador do administrador de domínio. Sem valor padrão e não pode ser deixado em branco.|
|Modelo de abóbada de chave|Modelo do Resource Manager|ID de objeto AAD|O identificador de objetos AAD da conta que requer acesso à instância key vault. Sem valor padrão e não pode ser deixado em branco. Para localizar este valor a partir do portal Azure, procure e selecione "Utilizadores" em _Serviços_. Utilize a caixa _Nome_ para filtrar o nome da conta e selecione essa conta. Na página de perfil do _Utilizador,_ selecione o ícone "Clique para copiar" ao lado do ID do _objeto._  |
|Modelo de abóbada de chave|Modelo do Resource Manager|Retenção de registos em dias|Retenção de dados em dias. O valor predefinido é _de 365_.|
|Modelo de abóbada de chave|Modelo do Resource Manager|SKU cofre-chave|Especifica o SKU do Cofre chave que é criado. O valor predefinido é _Premium._|
|Grupo de recursos Jumpbox|Grupo de recursos|Name|**Locked** - Concatenates o **nome da Organização** para tornar o grupo de recursos `-sharedsvcs-jb-rg` único.|
|Grupo de recursos Jumpbox|Grupo de recursos|Localização|**Bloqueado** - Usa o parâmetro da planta.|
|Modelo de caixa de salto|Modelo do Resource Manager|Nome de utilizador de administração jumpbox|O nome de utilizador usado para aceder a VMs de caixa de salto. Deve corresponder ao mesmo valor de propriedade no **modelo key vault**. O valor predefinido é _jb-administrador-utilizador_.|
|Modelo de caixa de salto|Modelo do Resource Manager|Senha de administração jumpbox (ID de recurso do cofre chave)|A identificação de recursos do Cofre de Chaves. Utilize "/subscrições/{subscriçãoId}/resourceGroups/{orgName}-sharedsvcs-kv-rg/providers/Microsoft.KeyVault/vaults/{orgName}-sharedsvcs-kv" e `{subscriptionId}` substitua-o pelo seu ID de subscrição e `{orgName}` pelo parâmetro de planta do nome da **Organização.**|
|Modelo de caixa de salto|Modelo do Resource Manager|Senha de administração jumpbox (Nome Secreto do Cofre de Chaves)|Nome de utilizador do administrador da caixa de salto. Deve corresponder o valor na propriedade **do modelo do Key Vault** Jumpbox nome de utilizador de **administração**.|
|Modelo de caixa de salto|Modelo do Resource Manager|Sistema operativo Jumpbox|Determina o sistema operativo do VM da caixa de salto. O valor predefinido é _o Windows_.|
|Grupo de recursos de serviços de domínio de diretório ativo|Grupo de recursos|Name|**Locked** - Concatenates o **nome da Organização** para tornar o grupo de recursos `-sharedsvcs-adds-rg` único.|
|Grupo de recursos de serviços de domínio de diretório ativo|Grupo de recursos|Localização|**Bloqueado** - Usa o parâmetro da planta.|
|Modelo de serviços de domínio de diretório ativo|Modelo do Resource Manager|Nome de utilizador de administração de domínio|Nome de utilizador para a caixa de salto ADDS. Deve corresponder ao mesmo valor de propriedade no **modelo key vault**. O valor predefinido é _o utilizador de administradores adicionais._|
|Modelo de serviços de domínio de diretório ativo|Modelo do Resource Manager|Senha de administração de domínio (ID de recurso do cofre chave)|A identificação de recursos do Cofre de Chaves. Utilize "/subscrições/{subscriçãoId}/resourceGroups/{orgName}-sharedsvcs-kv-rg/providers/Microsoft.KeyVault/vaults/{orgName}-sharedsvcs-kv" e `{subscriptionId}` substitua-o pelo seu ID de subscrição e `{orgName}` pelo parâmetro de planta do nome da **Organização.**|
|Modelo de serviços de domínio de diretório ativo|Modelo do Resource Manager|Senha de administração de domínio (Nome Secreto do Cofre de Chaves)|Nome de utilizador do administrador de domínio. Deve corresponder o valor na propriedade **do modelo do cofre de chaves** nome de **dados de administração**de domínio .|
|Modelo de serviços de domínio de diretório ativo|Modelo do Resource Manager|Nome de domínio|Nome do Diretório Ativo criado pela amostra. O valor predefinido é _contoso.com_.|
|Modelo de serviços de domínio de diretório ativo|Modelo do Resource Manager|Utilizador de administração de domínio|Nome de utilizador para a conta AD administração e para unir dispositivos ao domínio AD. Deve coincidir com o valor da propriedade do nome de utilizador do administrador **AD** no **modelo key vault**. O valor predefinido é _o utilizador de administração de domínios._|
|Modelo de serviços de domínio de diretório ativo|Modelo do Resource Manager|Senha de administração de domínio|Desa estada os detalhes do Cofre de Chaves para armazenar a palavra-passe. Sem valor padrão e não pode ser deixado em branco.|

## <a name="next-steps"></a>Próximos passos

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
