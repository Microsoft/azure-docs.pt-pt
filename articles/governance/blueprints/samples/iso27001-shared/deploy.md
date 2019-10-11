---
title: Exemplo-projeto de serviços compartilhados ISO 27001 – etapas de implantação
description: Implante as etapas para o exemplo de plano gráfico dos serviços compartilhados ISO 27001, incluindo detalhes do parâmetro de artefato do Blueprint.
author: DCtheGeek
ms.author: dacoulte
ms.date: 03/14/2019
ms.topic: sample
ms.service: blueprints
ms.openlocfilehash: 184bc87dc74cd67115b82b5be1a939c8286ed54f
ms.sourcegitcommit: 961468fa0cfe650dc1bec87e032e648486f67651
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/10/2019
ms.locfileid: "72248955"
---
# <a name="deploy-the-iso-27001-shared-services-blueprint-sample"></a>Implantar o exemplo de plano gráfico dos serviços compartilhados ISO 27001

Para implantar a amostra do plano gráfico dos serviços compartilhados do ISO 27001, as seguintes etapas devem ser executadas:

> [!div class="checklist"]
> - Criar um novo plano gráfico por meio do exemplo
> - Marque sua cópia do exemplo como **publicado**
> - Atribuir sua cópia do plano gráfico a uma assinatura existente

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free) antes de começar.

## <a name="create-blueprint-from-sample"></a>Criar plano gráfico por meio de exemplo

Primeiro, implemente o exemplo Blueprint criando uma nova especificação técnica em seu ambiente usando o exemplo como um início.

1. Selecione **todos os serviços** no painel esquerdo. Pesquise e selecione **plantas**.

1. Na página **Guia de introdução** à esquerda, selecione o botão **criar** em _criar um plano gráfico_.

1. Localize o exemplo de projeto **ISO 27001: serviços compartilhados** em _outros exemplos_ e selecione **usar este exemplo**.

1. Insira os _conceitos básicos_ do exemplo Blueprint:

   - **Nome do plano gráfico**: forneça um nome para a sua cópia do exemplo de plano gráfico de serviços compartilhados ISO 27001.
   - **Local de definição**: Use as reticências e selecione o grupo de gerenciamento para salvar a cópia do exemplo.

1. Selecione a guia _artefatos_ na parte superior da página ou **próximo: artefatos** na parte inferior da página.

1. Examine a lista de artefatos que compõem o exemplo Blueprint. Muitos dos artefatos têm parâmetros que vamos definir mais tarde. Selecione **salvar rascunho** ao concluir a revisão do exemplo Blueprint.

## <a name="publish-the-sample-copy"></a>Publicar a cópia de exemplo

Sua cópia do exemplo Blueprint agora foi criada em seu ambiente. Ele é criado no modo de **rascunho** e deve ser **publicado** antes que possa ser atribuído e implantado. A cópia do exemplo de plano gráfico pode ser personalizada para seu ambiente e necessidades, mas essa modificação pode movê-lo para fora do padrão ISO 27001.

1. Selecione **todos os serviços** no painel esquerdo. Pesquise e selecione **plantas**.

1. Selecione a página **definições de plantas** à esquerda. Use os filtros para localizar sua cópia do exemplo de plano gráfico e, em seguida, selecione-o.

1. Selecione **publicar Blueprint** na parte superior da página. Na nova página à direita, forneça uma **versão** para sua cópia do exemplo Blueprint. Essa propriedade será útil se você fizer uma modificação posteriormente. Forneça **observações de alteração** , como "primeira versão publicada do exemplo de plano gráfico ISO 27001". Em seguida, selecione **publicar** na parte inferior da página.

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

   - Parâmetros de esquema

     Os parâmetros definidos nesta seção são usados por muitos dos artefatos na definição do Blueprint para fornecer consistência.

     - **Nome da organização**: Insira um nome curto para sua organização. Essa propriedade é usada principalmente para nomear recursos.
     - **Prefixo de endereço de sub-rede dos serviços compartilhados**: forneça o valor de notação CIDR para a rede dos recursos implantados juntos.
     - **Local dos serviços compartilhados**: determina em qual local os artefatos são implantados. Nem todos os serviços estão disponíveis em todos os locais. Os artefatos que implantam esses serviços fornecem uma opção de parâmetro para o local no qual o artefato será implantado.
     - **Local permitido (política: iniciativa Blueprint para ISO 27001)** : valor que indica os locais permitidos para grupos de recursos e recursos.
     - **Espaço de trabalho log Analytics para agentes de VM (política: iniciativa Blueprint para ISO 27001)** : especifica a ID de recurso de um espaço de trabalho. Esse parâmetro usa uma função `concat` para construir a ID do recurso.

   - Parâmetros de artefato

     Os parâmetros definidos nesta seção se aplicam ao artefato sob o qual ele é definido. Esses parâmetros são [parâmetros dinâmicos](../../concepts/parameters.md#dynamic-parameters) , pois eles são definidos durante a atribuição do plano gráfico. Para obter uma lista completa ou parâmetros de artefato e suas descrições, consulte [tabela de parâmetros de artefato](#artifact-parameters-table).

1. Depois que todos os parâmetros forem inseridos, selecione **atribuir** na parte inferior da página. A atribuição Blueprint é criada e a implantação do artefato começa. A implantação leva aproximadamente uma hora. Para verificar o status da implantação, abra a atribuição Blueprint.

> [!WARNING]
> O serviço de plantas do Azure e os exemplos de plantas internas são **gratuitos**. Os recursos do Azure são [cobrados por produto](https://azure.microsoft.com/pricing/). Use a [calculadora de preços](https://azure.microsoft.com/pricing/calculator/) para estimar o custo da execução de recursos implantados por este exemplo de Blueprint.

## <a name="artifact-parameters-table"></a>Tabela de parâmetros de artefato

A tabela a seguir fornece uma lista dos parâmetros de artefatos do Blueprint:

|Nome do artefato|Tipo de artefato|Nome do parâmetro|Descrição|
|-|-|-|-|
|\[Preview @ no__t-1: implantar o agente Log Analytics para VMSS (conjuntos de dimensionamento de VMs) do Linux|Atribuição de política|Opcional: lista de imagens de VM que têm suporte para o sistema operacional Linux para adicionar ao escopo|Adicional O valor padrão é _["None"]_ .|
|\[Preview @ no__t-1: implantar o agente Log Analytics para VMs Linux|Atribuição de política|Opcional: lista de imagens de VM que têm suporte para o sistema operacional Linux para adicionar ao escopo|Adicional O valor padrão é _["None"]_ .|
|\[Preview @ no__t-1: implantar o agente de Log Analytics para conjuntos de dimensionamento de VMs do Windows (VMSS)|Atribuição de política|Opcional: lista de imagens de VM que têm suporte para o sistema operacional Windows para adicionar ao escopo|Adicional O valor padrão é _["None"]_ .|
|\[Preview @ no__t-1: implantar Log Analytics agente para VMs do Windows|Atribuição de política|Opcional: lista de imagens de VM que têm suporte para o sistema operacional Windows para adicionar ao escopo|Adicional O valor padrão é _["None"]_ .|
|Tipos de recursos permitidos|Atribuição de política|Tipos de recursos permitidos|Lista de tipos de recursos que podem ser implantados. Essa lista é composta por todos os tipos de recursos implantados em serviços compartilhados.|
|SKUs de contas de armazenamento permitidos|Atribuição de política|SKUs de armazenamento permitidas|Lista de logs de diagnóstico SKUs de conta de armazenamento permitidos. O valor padrão é _["Standard_LRS"]_ .|
|SKUs de máquina virtual permitidas|Atribuição de política|Lista de SKUs de máquina virtual que podem ser implantadas. O valor padrão é _["Standard_DS1_v2", "Standard_DS2_v2"]_ .|
|Iniciativa Blueprint para ISO 27001|Atribuição de política|Tipos de recurso para auditar logs de diagnóstico|Lista de tipos de recursos para auditar se a configuração do log de diagnóstico não estiver habilitada. Os valores aceitáveis podem ser encontrados em [Azure monitor esquemas de logs de diagnóstico](../../../../azure-monitor/platform/diagnostic-logs-schema.md#supported-log-categories-per-resource-type).|
|Log Analytics Grupo de recursos|Grupo de recursos|Nome|**Locked** – concatena o **nome da organização** com `-sharedsvsc-log-rg` para tornar o grupo de recursos exclusivo.|
|Log Analytics Grupo de recursos|Grupo de recursos|Localização|**Locked** -usa o parâmetro Blueprint.|
|Modelo de Log Analytics|Modelo do Resource Manager|Escalão de serviço|Define a camada do espaço de trabalho Log Analytics. O valor padrão é _PerNode_.|
|Modelo de Log Analytics|Modelo do Resource Manager|Retenção de log em dias|Retenção de dados em dias. O valor padrão é _365_.|
|Modelo de Log Analytics|Modelo do Resource Manager|Localização|Região usada para criar o espaço de trabalho Log Analytics. O valor padrão é _oeste dos EUA 2_.|
|Grupo de recursos de rede|Grupo de recursos|Nome|**Locked** – concatena o **nome da organização** com `-sharedsvcs-net-rg` para tornar o grupo de recursos exclusivo.|
|Grupo de recursos de rede|Grupo de recursos|Localização|**Locked** -usa o parâmetro Blueprint.|
|Modelo de firewall do Azure|Modelo do Resource Manager|IP privado do firewall do Azure|Configura o IP privado do [Firewall do Azure](../../../../firewall/overview.md). Esse valor também é usado como tabela de rotas padrão na sub-rede de serviços compartilhados. Deve fazer parte da notação CIDR definida no **prefixo de endereço de sub-rede do firewall do Azure**. O valor padrão é _10.0.4.4_.|
|Modelo de firewall do Azure|Modelo do Resource Manager|Retenção de log em dias|Retenção de dados em dias. O valor padrão é _365_.|
|Modelo de grupo de segurança de rede|Modelo do Resource Manager|Retenção de log em dias|Retenção de dados em dias. O valor padrão é _365_.|
|Modelo de rede virtual e de tabela de rotas|Modelo do Resource Manager|Prefixo de endereço de rede virtual|A notação CIDR para a rede virtual. O valor padrão é _10.0.0.0/16_.|
|Modelo de rede virtual e de tabela de rotas|Modelo do Resource Manager|Habilitar a proteção contra DDoS da rede virtual|Configura a proteção contra DDoS para a rede virtual. O valor padrão é _true_.|
|Modelo de rede virtual e de tabela de rotas|Modelo do Resource Manager|Prefixo de endereço de sub-rede dos serviços compartilhados|A notação CIDR para a sub-rede de serviços compartilhados. O valor padrão é _10.0.0.0/24_.|
|Modelo de rede virtual e de tabela de rotas|Modelo do Resource Manager|Prefixo de endereço da sub-rede DMZ|A notação CIDR para a sub-rede DMZ. O valor padrão é _10.0.1.0/24_.|
|Modelo de rede virtual e de tabela de rotas|Modelo do Resource Manager|Prefixo de endereço de sub-rede do gateway de aplicativo|A notação CIDR para a sub-rede do gateway de aplicativo. O valor padrão é _10.0.2.0/24_.|
|Modelo de rede virtual e de tabela de rotas|Modelo do Resource Manager|Prefixo de endereço de sub-rede do gateway de rede virtual|A notação CIDR para a sub-rede de gateway de rede virtual. O valor padrão é _10.0.3.0/24_.|
|Modelo de rede virtual e de tabela de rotas|Modelo do Resource Manager|Prefixo de endereço de sub-rede do firewall do Azure|A notação CIDR para a sub-rede do [Firewall do Azure](../../../../firewall/overview.md) . Deve incluir o parâmetro de **IP privado do firewall do Azure** .|
|Key Vault grupo de recursos|Grupo de recursos|Nome|**Locked** – concatena o **nome da organização** com `-sharedsvcs-kv-rg` para tornar o grupo de recursos exclusivo.|
|Key Vault grupo de recursos|Grupo de recursos|Localização|**Locked** -usa o parâmetro Blueprint.|
|Modelo de Key Vault|Modelo do Resource Manager|Nome de usuário do administrador do Jumpbox|Nome de usuário para o Jumpbox. Deve corresponder ao mesmo valor de propriedade no **modelo Jumpbox**. O valor padrão é _JB-admin-user_.|
|Modelo de Key Vault|Modelo do Resource Manager|Chave ou senha ssh de administrador do Jumpbox|Chave ou senha da conta no Jumpbox. Deve corresponder ao mesmo valor de propriedade no **modelo Jumpbox**. Nenhum valor padrão e não pode ser deixado em branco.|
|Modelo de Key Vault|Modelo do Resource Manager|Nome de usuário do administrador de domínio|Nome de usuário usado para acessar Active Directory VM e unir outras VMs a um domínio. Deve corresponder ao valor da propriedade de **usuário administrador de domínio** no **modelo Active Directory Domain Services**. O valor padrão é _Domain-admin-user_.|
|Modelo de Key Vault|Modelo do Resource Manager|Senha de administrador de domínio|Senha do usuário administrador de domínio. Nenhum valor padrão e não pode ser deixado em branco.|
|Modelo de Key Vault|Modelo do Resource Manager|ID do objeto do AAD|O identificador de objeto do AAD da conta que requer acesso à instância de Key Vault. Nenhum valor padrão e não pode ser deixado em branco. Para localizar esse valor no portal do Azure, procure e selecione "usuários" em _Serviços_. Use a caixa _nome_ para filtrar o nome da conta e selecione essa conta. Na página _perfil do usuário_ , selecione o ícone "clique para copiar" ao lado da _ID do objeto_.  |
|Modelo de Key Vault|Modelo do Resource Manager|Retenção de log em dias|Retenção de dados em dias. O valor padrão é _365_.|
|Modelo de Key Vault|Modelo do Resource Manager|Key Vault SKU|Especifica a SKU do Key Vault que é criado. O valor padrão é _Premium_.|
|Grupo de recursos Jumpbox|Grupo de recursos|Nome|**Locked** – concatena o **nome da organização** com `-sharedsvcs-jb-rg` para tornar o grupo de recursos exclusivo.|
|Grupo de recursos Jumpbox|Grupo de recursos|Localização|**Locked** -usa o parâmetro Blueprint.|
|Modelo Jumpbox|Modelo do Resource Manager|Nome de usuário do administrador do Jumpbox|O nome de usuário usado para acessar VMs Jumpbox. Deve corresponder ao mesmo valor de propriedade no **modelo de Key Vault**. O valor padrão é _JB-admin-user_.|
|Modelo Jumpbox|Modelo do Resource Manager|Senha de administrador do Jumpbox (ID de recurso Key Vault)|A ID de recurso do Key Vault. Use "/subscriptions/{subscriptionId}/resourceGroups/{orgName}-sharedsvcs-kv-rg/providers/Microsoft.KeyVault/vaults/{orgName}-sharedsvcs-kv" e substitua `{subscriptionId}` pela sua ID de assinatura e `{orgName}` pelo **nome da organização** parâmetro Blueprint.|
|Modelo Jumpbox|Modelo do Resource Manager|Senha do administrador do Jumpbox (nome do Key Vault segredo)|Nome de usuário do administrador do Jumpbox. Deve corresponder ao valor na propriedade de **modelo Key Vault** **nome de usuário do administrador Jumpbox**.|
|Modelo Jumpbox|Modelo do Resource Manager|Sistema operacional Jumpbox|Determina o sistema operacional da VM Jumpbox. O valor padrão é _Windows_.|
|Active Directory Domain Services grupo de recursos|Grupo de recursos|Nome|**Locked** – concatena o **nome da organização** com `-sharedsvcs-adds-rg` para tornar o grupo de recursos exclusivo.|
|Active Directory Domain Services grupo de recursos|Grupo de recursos|Localização|**Locked** -usa o parâmetro Blueprint.|
|Modelo de Active Directory Domain Services|Modelo do Resource Manager|Nome de usuário do administrador de domínio|Nome de usuário para o adiciona Jumpbox. Deve corresponder ao mesmo valor de propriedade no **modelo de Key Vault**. O valor padrão é _ADDS-admin-user_.|
|Modelo de Active Directory Domain Services|Modelo do Resource Manager|Senha de administrador de domínio (Key Vault ID de recurso)|A ID de recurso do Key Vault. Use "/subscriptions/{subscriptionId}/resourceGroups/{orgName}-sharedsvcs-kv-rg/providers/Microsoft.KeyVault/vaults/{orgName}-sharedsvcs-kv" e substitua `{subscriptionId}` pela sua ID de assinatura e `{orgName}` pelo **nome da organização** parâmetro Blueprint.|
|Modelo de Active Directory Domain Services|Modelo do Resource Manager|Senha do administrador de domínio (nome do Key Vault segredo)|Nome de usuário do administrador de domínio. Deve corresponder ao valor na propriedade de **modelo Key Vault** **nome de usuário administrador de domínio**.|
|Modelo de Active Directory Domain Services|Modelo do Resource Manager|Nome de domínio|Nome do Active Directory criado pelo exemplo. O valor padrão é _contoso.com_.|
|Modelo de Active Directory Domain Services|Modelo do Resource Manager|Usuário administrador de domínio|Nome de usuário para a conta do AD do administrador e para unir dispositivos ao domínio do AD. Deve corresponder ao valor da propriedade **username do administrador do AD** no **modelo Key Vault**. O valor padrão é _Domain-admin-user_.|
|Modelo de Active Directory Domain Services|Modelo do Resource Manager|Senha de administrador de domínio|Defina os detalhes de Key Vault para armazenar a senha. Nenhum valor padrão e não pode ser deixado em branco.|

## <a name="next-steps"></a>Passos seguintes

Agora que você analisou as etapas para implantar o exemplo de plano gráfico de serviços compartilhados ISO 27001, visite os seguintes artigos para saber mais sobre o mapeamento de arquitetura e controle:

> [!div class="nextstepaction"]
> [Plano de serviços compartilhados iso 27001-visão geral](./index.md)
> [ISO 27001 serviços compartilhados Blueprint – mapeamento de controle](./control-mapping.md)

Artigos adicionais sobre esquemas e como utilizá-los:

- Saiba mais sobre o [ciclo de vida do esquema](../../concepts/lifecycle.md).
- Compreenda como utilizar [parâmetros estáticos e dinâmicos](../../concepts/parameters.md).
- Aprenda a personalizar a [ordem de sequenciação do esquema](../../concepts/sequencing-order.md).
- Saiba como utilizar o [bloqueio de recursos de esquema](../../concepts/resource-locking.md).
- Saiba como [atualizar as atribuições existentes](../../how-to/update-existing-assignments.md).
