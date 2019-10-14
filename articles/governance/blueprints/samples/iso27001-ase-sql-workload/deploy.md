---
title: Exemplo de Blueprint de carga de trabalho do ISO 27001 ASE/SQL-etapas de implantação
description: Implante as etapas do exemplo de plano gráfico de carga de trabalho do banco de dados ISO 27001 Ambiente do Serviço de Aplicativo/SQL, incluindo detalhes do parâmetro de artefato Blueprint.
author: DCtheGeek
ms.author: dacoulte
ms.date: 03/14/2019
ms.topic: sample
ms.service: blueprints
ms.openlocfilehash: bd85a65d3e4d8aa1a16bb57dc5981d661a83d412
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/13/2019
ms.locfileid: "72297628"
---
# <a name="deploy-the-iso-27001-app-service-environmentsql-database-workload-blueprint-sample"></a>Implantar a amostra do plano gráfico de carga de trabalho do banco de dados ISO 27001 Ambiente do Serviço de Aplicativo/SQL

Para implantar a amostra do plano gráfico de carga de trabalho do banco de dados SQL 27001 Ambiente do Serviço de Aplicativo/projetos do Azure BluePrints, as seguintes etapas devem ser executadas:

> [!div class="checklist"]
> - Implantar o exemplo de plano gráfico [dos serviços compartilhados ISO 27001](../iso27001-shared/index.md)
> - Criar um novo plano gráfico por meio do exemplo
> - Marque sua cópia do exemplo como **publicado**
> - Atribuir sua cópia do plano gráfico a uma assinatura existente

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free) antes de começar.

## <a name="deploy-the-iso-27001-shared-services-blueprint-sample"></a>Implantar o exemplo de plano gráfico dos serviços compartilhados ISO 27001

Antes que este exemplo de plano gráfico possa ser implantado, o exemplo de plano gráfico [dos serviços compartilhados ISO 27001](../iso27001-shared/index.md) deve ser implantado na assinatura de destino. Sem uma implantação bem-sucedida do exemplo de plano gráfico de serviços compartilhados ISO 27001, essa amostra de diagrama não terá dependências de infraestrutura e falhará durante a implantação.

> [!IMPORTANT]
> Este exemplo de projeto deve ser atribuído na mesma assinatura que o exemplo de plano gráfico de [serviços compartilhados ISO 27001](../iso27001-shared/index.md) .

## <a name="create-blueprint-from-sample"></a>Criar plano gráfico por meio de exemplo

Primeiro, implemente o exemplo Blueprint criando uma nova especificação técnica em seu ambiente usando o exemplo como um início.

1. Selecione **todos os serviços** no painel esquerdo. Pesquise e selecione **plantas**.

1. Na página **Guia de introdução** à esquerda, selecione o botão **criar** em _criar um plano gráfico_.

1. Localize a amostra **ISO 27001: ase/** Blueprint de carga de trabalho do SQL em _outros exemplos_ e selecione **usar este exemplo**.

1. Insira os _conceitos básicos_ do exemplo Blueprint:

   - **Nome do plano**: forneça um nome para a sua cópia do exemplo de esquema de carga de trabalho do ISO 27001 ase/SQL.
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
     - **ID da assinatura do serviço compartilhado**: ID da assinatura em que o exemplo de plano gráfico [dos serviços compartilhados ISO 27001](../iso27001-shared/index.md) está atribuído.
     - **Prefixo de endereço de sub-rede padrão**: a notação CIDR para a sub-rede padrão da rede virtual.
       O valor padrão é _10.1.0.0/16_.
     - **Local da carga de trabalho**: determina em qual local os artefatos são implantados. Nem todos os serviços estão disponíveis em todos os locais. Os artefatos que implantam esses serviços fornecem uma opção de parâmetro para o local no qual o artefato será implantado.

   - Parâmetros de artefato

     Os parâmetros definidos nesta seção se aplicam ao artefato sob o qual ele é definido. Esses parâmetros são [parâmetros dinâmicos](../../concepts/parameters.md#dynamic-parameters) , pois eles são definidos durante a atribuição do plano gráfico. Para obter uma lista completa ou parâmetros de artefato e suas descrições, consulte [tabela de parâmetros de artefato](#artifact-parameters-table).

1. Depois que todos os parâmetros forem inseridos, selecione **atribuir** na parte inferior da página. A atribuição Blueprint é criada e a implantação do artefato começa. A implantação leva aproximadamente uma hora. Para verificar o status da implantação, abra a atribuição Blueprint.

> [!WARNING]
> O serviço de plantas do Azure e os exemplos de plantas internas são **gratuitos**. Os recursos do Azure são [cobrados por produto](https://azure.microsoft.com/pricing/). Use a [calculadora de preços](https://azure.microsoft.com/pricing/calculator/) para estimar o custo da execução de recursos implantados por este exemplo de Blueprint.

## <a name="artifact-parameters-table"></a>Tabela de parâmetros de artefato

A tabela a seguir fornece uma lista dos parâmetros de artefatos do Blueprint:

|Nome do artefato|Tipo de artefato|Nome do parâmetro|Descrição|
|-|-|-|-|
|Log Analytics Grupo de recursos|Grupo de recursos|Nome|**Locked** – concatena o **nome da organização** com `-workload-log-rg` para tornar o grupo de recursos exclusivo.|
|Log Analytics Grupo de recursos|Grupo de recursos|Localização|**Locked** -usa o parâmetro Blueprint.|
|Modelo de Log Analytics|Modelo do Resource Manager|Escalão de serviço|Define a camada do espaço de trabalho Log Analytics. O valor padrão é _PerNode_.|
|Modelo de Log Analytics|Modelo do Resource Manager|Retenção de log em dias|Retenção de dados em dias. O valor padrão é _365_.|
|Modelo de Log Analytics|Modelo do Resource Manager|Localização|Região usada para criar o espaço de trabalho Log Analytics. O valor padrão é _oeste dos EUA 2_.|
|Grupo de recursos de rede|Grupo de recursos|Nome|**Locked** – concatena o **nome da organização** com `-workload-net-rg` para tornar o grupo de recursos exclusivo.|
|Grupo de recursos de rede|Grupo de recursos|Localização|**Locked** -usa o parâmetro Blueprint.|
|Modelo de grupo de segurança de rede|Modelo do Resource Manager|Retenção de log em dias|Retenção de dados em dias. O valor padrão é _365_.|
|Modelo de rede virtual e de tabela de rotas|Modelo do Resource Manager|IP privado do firewall do Azure|Configura o IP privado do [Firewall do Azure](../../../../firewall/overview.md). Deve ser parte da notação CIDR definida em _ISO 27001:_ parâmetro de artefato de serviços compartilhados **prefixo de endereço de sub-rede do firewall do Azure**. O valor padrão é _10.0.4.4_.|
|Modelo de rede virtual e de tabela de rotas|Modelo do Resource Manager|ID da assinatura de serviços compartilhados|Valor usado para habilitar o emparelhamento VNET entre uma carga de trabalho e os serviços compartilhados.|
|Modelo de rede virtual e de tabela de rotas|Modelo do Resource Manager|Prefixo de endereço de rede virtual|A notação CIDR para a rede virtual. O valor padrão é _10.1.0.0/16_.|
|Modelo de rede virtual e de tabela de rotas|Modelo do Resource Manager|Prefixo de endereço de sub-rede padrão|A notação CIDR para a sub-rede padrão da rede virtual. O valor padrão é _10.1.0.0/16_.|
|Modelo de rede virtual e de tabela de rotas|Modelo do Resource Manager|Adiciona o endereço IP|O endereço IP da primeira adiciona VM. Esse valor é usado como DNS de VNET personalizado.|
|Key Vault grupo de recursos|Grupo de recursos|Nome|**Locked** – concatena o **nome da organização** com `-workload-kv-rg` para tornar o grupo de recursos exclusivo.|
|Key Vault grupo de recursos|Grupo de recursos|Localização|**Locked** -usa o parâmetro Blueprint.|
|Modelo de Key Vault|Modelo do Resource Manager|ID do objeto do AAD|O identificador de objeto do AAD da conta que requer acesso à instância de Key Vault. Nenhum valor padrão e não pode ser deixado em branco. Para localizar esse valor no portal do Azure, procure e selecione "usuários" em _Serviços_. Use a caixa _nome_ para filtrar o nome da conta e selecione essa conta. Na página _perfil do usuário_ , selecione o ícone "clique para copiar" ao lado da _ID do objeto_.|
|Modelo de Key Vault|Modelo do Resource Manager|Retenção de log em dias|Retenção de dados em dias. O valor padrão é _365_.|
|Modelo de Key Vault|Modelo do Resource Manager|Key Vault SKU|Especifica a SKU do Key Vault que é criado. O valor padrão é _Premium_.|
|Modelo de Key Vault|Modelo do Resource Manager|Nome de usuário do administrador do Azure SQL Server|O nome de usuário usado para acessar o SQL Server do Azure. Deve corresponder ao mesmo valor de propriedade no **modelo de banco de dados SQL do Azure**. O valor padrão é _SQL-admin-user_.|
|Grupo de recursos do banco de dados SQL do Azure|Grupo de recursos|Nome|**Locked** – concatena o **nome da organização** com `-workload-azsql-rg` para tornar o grupo de recursos exclusivo.|
|Grupo de recursos do banco de dados SQL do Azure|Grupo de recursos|Localização|**Locked** -usa o parâmetro Blueprint.|
|Modelo de banco de dados SQL do Azure|Modelo do Resource Manager|Nome de usuário do administrador do Azure SQL Server|Nome de usuário para o SQL Server do Azure. Deve corresponder ao mesmo valor de propriedade no **modelo de Key Vault**. O valor padrão é _SQL-admin-user_.|
|Modelo de banco de dados SQL do Azure|Modelo do Resource Manager|Senha de administrador do Azure SQL Server (Key Vault ID do recurso)|A ID de recurso do Key Vault. Use "/subscription/{subscriptionId}/resourceGroups/{orgName}-workload-kv/providers/Microsoft.KeyVault/vaults/{orgName}-workload-kv" e substitua `{subscriptionId}` pela sua ID de assinatura e `{orgName}` pelo plano de **nome da organização** meter.|
|Modelo de banco de dados SQL do Azure|Modelo do Resource Manager|Senha de administrador do Azure SQL Server (nome do Key Vault segredo)|Nome de usuário do administrador do SQL Server. Deve corresponder ao valor na propriedade de **modelo Key Vault** o **nome de usuário do administrador do Azure SQL Server**.|
|Modelo de banco de dados SQL do Azure|Modelo do Resource Manager|Retenção de log em dias|Retenção de dados em dias. O valor padrão é _365_.|
|Modelo de banco de dados SQL do Azure|Modelo do Resource Manager|ID do objeto de administrador do AAD|ID de objeto do AAD do usuário que será atribuído como um administrador de Active Directory. Nenhum valor padrão e não pode ser deixado em branco. Para localizar esse valor no portal do Azure, procure e selecione "usuários" em _Serviços_. Use a caixa _nome_ para filtrar o nome da conta e selecione essa conta. Na página _perfil do usuário_ , selecione o ícone "clique para copiar" ao lado da _ID do objeto_.|
|Modelo de banco de dados SQL do Azure|Modelo do Resource Manager|Logon de administrador do AAD|Atualmente, as contas da Microsoft (como live.com ou outlook.com) não podem ser definidas como administrador. Somente usuários e grupos de segurança em sua organização podem ser definidos como administrador. Nenhum valor padrão e não pode ser deixado em branco. Para localizar esse valor no portal do Azure, procure e selecione "usuários" em _Serviços_. Use a caixa _nome_ para filtrar o nome da conta e selecione essa conta. Na página _perfil do usuário_ , copie o _nome de usuário_.|
|Ambiente do Serviço de Aplicativo grupo de recursos|Grupo de recursos|Nome|**Locked** – concatena o **nome da organização** com `-workload-ase-rg` para tornar o grupo de recursos exclusivo.|
|Ambiente do Serviço de Aplicativo grupo de recursos|Grupo de recursos|Localização|**Locked** -usa o parâmetro Blueprint.|
|Modelo de Ambiente do Serviço de Aplicativo|Modelo do Resource Manager|Nome de domínio|Nome do Active Directory criado pelo exemplo. O valor padrão é _contoso.com_.|
|Modelo de Ambiente do Serviço de Aplicativo|Modelo do Resource Manager|Local do ASE|Ambiente do Serviço de Aplicativo local. O valor padrão é _oeste dos EUA 2_.|
|Modelo de Ambiente do Serviço de Aplicativo|Modelo do Resource Manager|Retenção de log do gateway de aplicativo em dias|Retenção de dados em dias. O valor padrão é _365_.|

## <a name="next-steps"></a>Passos seguintes

Agora que você analisou as etapas para implantar o exemplo de esquema de carga de trabalho do banco de dados do Ambiente do Serviço de Aplicativo/SQL ISO 27001, visite os seguintes artigos para saber mais sobre o mapeamento de arquitetura e controle:

> [!div class="nextstepaction"]
> [Iso 27001 ambiente do serviço de aplicativo/esquema de carga de trabalho do banco de dados SQL-visão geral](./index.md)
> [ISO 27001 ambiente do serviço de aplicativo/esquema de carga de trabalho do banco de dados SQL](./control-mapping.md)

Artigos adicionais sobre esquemas e como utilizá-los:

- Saiba mais sobre o [ciclo de vida do esquema](../../concepts/lifecycle.md).
- Compreenda como utilizar [parâmetros estáticos e dinâmicos](../../concepts/parameters.md).
- Aprenda a personalizar a [ordem de sequenciação do esquema](../../concepts/sequencing-order.md).
- Saiba como utilizar o [bloqueio de recursos de esquema](../../concepts/resource-locking.md).
- Saiba como [atualizar as atribuições existentes](../../how-to/update-existing-assignments.md).