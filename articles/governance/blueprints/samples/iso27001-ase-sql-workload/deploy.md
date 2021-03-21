---
title: Implementar amostra de amostra de carga de carga DE CARGA ISO 27001 ASE/SQL
description: Implementar etapas da amostra de projeto de projeto de projeto de modelo de tipo de modelo de serviço de aplicações ISO 27001/SQL Database, incluindo detalhes do parâmetro do artefacto da planta.
ms.date: 02/05/2021
ms.topic: sample
ms.openlocfilehash: 5c329a9d7175772e80ea6d9d8da3baf85ce0d170
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "104669649"
---
# <a name="deploy-the-iso-27001-app-service-environmentsql-database-workload-blueprint-sample"></a>Implementar a amostra de amostra de carga de carga de trabalho do serviço de aplicações ISO 27001/SQL Database

Para implementar a amostra de amostra de carga de trabalho da base de dados da Azure Blueprints ISO 27001, devem ser tomadas as seguintes medidas:

> [!div class="checklist"]
> - Implementar a amostra de projeto [iso 27001 serviços partilhados](../iso27001-shared/index.md)
> - Criar um esquema novo a partir do exemplo
> - Marcar a cópia do exemplo como **Publicada**
> - Atribuir a cópia do esquema a uma subscrição já existente

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free) antes de começar.

## <a name="deploy-the-iso-27001-shared-services-blueprint-sample"></a>Implementar a amostra de projeto iso 27001 serviços partilhados

Antes de esta amostra de planta poder ser implantada, a amostra de projeto [iso 27001 dos Serviços Partilhados](../iso27001-shared/index.md) deve ser implantada na subscrição-alvo. Sem uma implantação bem sucedida da amostra de projeto iso 27001 dos Serviços Partilhados, esta amostra de planta perderá dependências de infraestruturas e falhará durante a implantação.

> [!IMPORTANT]
> Esta amostra de planta deve ser atribuída na mesma subscrição que a amostra de projeto [iso 27001 dos Serviços Partilhados.](../iso27001-shared/index.md)

## <a name="create-blueprint-from-sample"></a>Criar planta a partir de amostra

Primeiro, crie um esquema novo no ambiente utilizando o exemplo como ponto de partida, para implementar o esquema de exemplo.

1. Selecione **Todos os serviços** no painel esquerdo. Procure e selecione **Esquemas**.

1. Na página **Começar** à esquerda, selecione o botão **Criar** em _Criar um esquema_.

1. Encontre a amostra de amostra de carga **de trabalho ISO 27001: ASE/SQL Workload** em _outras amostras_ e selecione **Utilize esta amostra**.

1. Introduza as _Informações Básicas_ do esquema de exemplo:

   - **Nome da planta**: Forneça um nome para a sua cópia da amostra de projeto de carga de trabalho ISO 27001 ASE/SQL.
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
     - **ID de assinatura de serviço partilhado**: ID de assinatura onde a amostra de projeto [iso 27001 de serviços partilhados](../iso27001-shared/index.md) é atribuída.
     - **Prefixo prefixo do endereço de sub-rede preguiça:** A notação CIDR para a sub-rede por defeito da rede virtual.
       O valor predefinido é _de 10.1.0.0/16_.
     - **Localização da carga de trabalho**: Determina para que local os artefactos são implantados. Nem todos os serviços estão disponíveis em todos os locais. Os artefactos que implantam esses serviços fornecem uma opção de parâmetro para a localização para implantar esse artefacto.

   - Parâmetros dos artefactos

     Os parâmetros definidos nesta secção aplicam-se ao artefacto no qual são definidos. Estes parâmetros são [parâmetros dinâmicos,](../../concepts/parameters.md#dynamic-parameters) uma vez que são definidos durante a atribuição da planta. Para obter uma lista completa ou parâmetros de artefactos e suas descrições, consulte [a tabela de parâmetros do Artefacto](#artifact-parameters-table).

1. Depois de introduzidos todos os parâmetros, selecione **Atribuir**, na parte inferior da página. A atribuição do esquema é criada e a implementação do artefacto inicia-se. A implantação leva cerca de uma hora. Para verificar o estado, abra a atribuição do esquema.

> [!WARNING]
> O serviço Azure Blueprints e os esquemas de exemplo incorporados são **gratuitos**. Os preços dos recursos do Azure são os [preços por produto](https://azure.microsoft.com/pricing/). Utilize a [calculadora de preços](https://azure.microsoft.com/pricing/calculator/) para prever o custo da execução de recursos implementados por este esquema de exemplo.

## <a name="artifact-parameters-table"></a>Tabela de parâmetros dos artefactos

A tabela seguinte mostra uma lista dos parâmetros dos artefactos de esquema:

|Nome do artefacto|Tipo de artefacto|Nome do parâmetro|Descrição|
|-|-|-|-|
|Grupo de recursos Log Analytics|Grupo de recursos|Name|**Locked** - Concatenates o **nome da Organização** para tornar o grupo de recursos `-workload-log-rg` único.|
|Grupo de recursos Log Analytics|O grupo de recursos|A localização|**Bloqueado** - Usa o parâmetro da planta.|
|Modelo de Analítica de Log|Modelo do Resource Manager|Escalão de serviço|Define o nível do espaço de trabalho Log Analytics. O valor predefinido é _PerNode_.|
|Modelo de Analítica de Log|Modelo do Resource Manager|Retenção de registos em dias|Retenção de dados em dias. O valor predefinido é _de 365_.|
|Modelo de Analítica de Log|Modelo do Resource Manager|Localização|Região utilizada para criar o espaço de trabalho Log Analytics. O valor predefinido é _West US 2_.|
|Grupo de recursos de rede|Grupo de recursos|Name|**Locked** - Concatenates o **nome da Organização** para tornar o grupo de recursos `-workload-net-rg` único.|
|Grupo de recursos de rede|O grupo de recursos|A localização|**Bloqueado** - Usa o parâmetro da planta.|
|Modelo de grupo de segurança de rede|Modelo do Resource Manager|Retenção de registos em dias|Retenção de dados em dias. O valor predefinido é _de 365_.|
|Modelo de tabela de rede virtual e rota|Modelo do Resource Manager|IP privado de firewall Azure|Configura o IP privado da [firewall Azure](../../../../firewall/overview.md). Deve fazer parte da notação CIDR definida na _ISO 27001: Parâmetro de artefacto de serviços partilhados_ **Azure Firewall prefixo de endereço do sub-redes**. O valor predefinido é _de 10.0.4.4_.|
|Modelo de tabela de rede virtual e rota|Modelo do Resource Manager|ID de assinatura de serviços partilhados|Valor utilizado para permitir o espreitamento do VNET entre uma Carga de Trabalho e Serviços Partilhados.|
|Modelo de tabela de rede virtual e rota|Modelo do Resource Manager|Prefixo de endereço de rede virtual|A notação CIDR para a rede virtual. O valor predefinido é _de 10.1.0.0/16_.|
|Modelo de tabela de rede virtual e rota|Modelo do Resource Manager|Prefixo de endereço de sub-rede predefinido|A notação CIDR para a sub-rede por defeito da rede virtual. O valor predefinido é _de 10.1.0.0/16_.|
|Modelo de tabela de rede virtual e rota|Modelo do Resource Manager|ADICIONA ENDEREÇO IP|Endereço IP do primeiro ADDS VM. Este valor é usado como DNS VNET personalizado.|
|Grupo de recursos Key Vault|Grupo de recursos|Name|**Locked** - Concatenates o **nome da Organização** para tornar o grupo de recursos `-workload-kv-rg` único.|
|Grupo de recursos Key Vault|O grupo de recursos|A localização|**Bloqueado** - Usa o parâmetro da planta.|
|Modelo de abóbada de chave|Modelo do Resource Manager|ID de objeto AAD|O identificador de objetos AAD da conta que requer acesso à instância key vault. Sem valor padrão e não pode ser deixado em branco. Para localizar este valor a partir do portal Azure, procure e selecione "Utilizadores" em _Serviços_. Utilize a caixa _Nome_ para filtrar o nome da conta e selecione essa conta. Na página de perfil do _Utilizador,_ selecione o ícone "Clique para copiar" ao lado do ID do _objeto._|
|Modelo de abóbada de chave|Modelo do Resource Manager|Retenção de registos em dias|Retenção de dados em dias. O valor predefinido é _de 365_.|
|Modelo de abóbada de chave|Modelo do Resource Manager|SKU cofre-chave|Especifica o SKU do Cofre chave que é criado. O valor predefinido é _Premium._|
|Modelo de abóbada de chave|Modelo do Resource Manager|Nome de utilizador de administração Azure SQL Server|O nome de utilizador utilizado para aceder ao Azure SQL Server. Deve corresponder ao mesmo valor de propriedade no **modelo de base de dados Azure SQL**. O valor predefinido é _sql-administrador-utilizador_.|
|Grupo de recursos de base de dados Azure SQL|Grupo de recursos|Name|**Locked** - Concatenates o **nome da Organização** para tornar o grupo de recursos `-workload-azsql-rg` único.|
|Grupo de recursos de base de dados Azure SQL|O grupo de recursos|A localização|**Bloqueado** - Usa o parâmetro da planta.|
|Modelo de base de dados Azure SQL|Modelo do Resource Manager|Nome de utilizador de administração Azure SQL Server|Nome de utilizador para o Azure SQL Server. Deve corresponder ao mesmo valor de propriedade no **modelo key vault**. O valor predefinido é _sql-administrador-utilizador_.|
|Modelo de base de dados Azure SQL|Modelo do Resource Manager|Senha de administração do Azure SQL Server (ID de recurso do cofre de chaves)|A identificação de recursos do Cofre de Chaves. Utilize "/subscrições/{subscriçãoId}/resourceGroups/{orgName}-workload-kv-rg/providers/Microsoft.KeyVault/vaults/{orgName}-workload-kv" e `{subscriptionId}` substitua-o pelo seu ID de subscrição e `{orgName}` pelo parâmetro de plantação do nome da **Organização.**|
|Modelo de base de dados Azure SQL|Modelo do Resource Manager|Senha de administração do Azure SQL Server (Key Vault Secret Name)|Nome de utilizador do administrador do SQL Server. Deve corresponder o valor na propriedade **do modelo do Cofre de Chaves** **Azure SQL Server nome de utilizador** de administração .|
|Modelo de base de dados Azure SQL|Modelo do Resource Manager|Retenção de registos em dias|Retenção de dados em dias. O valor predefinido é _de 365_.|
|Modelo de base de dados Azure SQL|Modelo do Resource Manager|ID de objeto de administração AAD|ID de objeto AAD do utilizador que será designado como administrador do Ative Directory. Sem valor padrão e não pode ser deixado em branco. Para localizar este valor a partir do portal Azure, procure e selecione "Utilizadores" em _Serviços_. Utilize a caixa _Nome_ para filtrar o nome da conta e selecione essa conta. Na página de perfil do _Utilizador,_ selecione o ícone "Clique para copiar" ao lado do ID do _objeto._|
|Modelo de base de dados Azure SQL|Modelo do Resource Manager|Login de administração AAD|Atualmente, as contas da Microsoft (como live.com ou outlook.com) não podem ser definidas como administradora. Apenas os utilizadores e grupos de segurança dentro da sua organização podem ser definidos como administradores. Sem valor padrão e não pode ser deixado em branco. Para localizar este valor a partir do portal Azure, procure e selecione "Utilizadores" em _Serviços_. Utilize a caixa _Nome_ para filtrar o nome da conta e selecione essa conta. Na página de perfil do _Utilizador,_ copie o _nome de Utilizador._|
|Grupo de recursos de serviço de aplicação ambiente|Grupo de recursos|Name|**Locked** - Concatenates o **nome da Organização** para tornar o grupo de recursos `-workload-ase-rg` único.|
|Grupo de recursos de serviço de aplicação ambiente|O grupo de recursos|A localização|**Bloqueado** - Usa o parâmetro da planta.|
|Modelo de ambiente de serviço de aplicativo|Modelo do Resource Manager|Nome de domínio|Nome do Diretório Ativo criado pela amostra. O valor predefinido é _contoso.com_.|
|Modelo de ambiente de serviço de aplicativo|Modelo do Resource Manager|Localização ASE|Localização do Ambiente do Serviço de Aplicações. O valor predefinido é _West US 2_.|
|Modelo de ambiente de serviço de aplicativo|Modelo do Resource Manager|Retenção de registos de gateway de aplicação em dias|Retenção de dados em dias. O valor predefinido é _de 365_.|

## <a name="next-steps"></a>Passos seguintes

Agora que reviu os passos para implementar a amostra de projeto de carga de trabalho do Serviço de Aplicações ISO 27001/SQL Database, visite os seguintes artigos para saber sobre a arquitetura e o mapeamento de controlo:

> [!div class="nextstepaction"]
> [ISO 27001 App Service Environment/SQL Database projeto de carga de carga - Visão geral](./index.md) 
>  [ISO 27001 App Service Environment/SQL Database projeto de carga de carga de trabalho - Controlo de mapeamento](./control-mapping.md)

Artigos adicionais sobre esquemas e como os utilizar:

- Saiba mais sobre o [ciclo de vida do esquema](../../concepts/lifecycle.md).
- Compreenda como utilizar [parâmetros estáticos e dinâmicos](../../concepts/parameters.md).
- Aprenda a personalizar a [ordem de sequenciação do esquema](../../concepts/sequencing-order.md).
- Saiba como utilizar o [bloqueio de recursos de esquema](../../concepts/resource-locking.md).
- Saiba como [atualizar as atribuições existentes](../../how-to/update-existing-assignments.md).
