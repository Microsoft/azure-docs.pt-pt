---
title: Implementar amostra de amostra de amostra de amostra de carga de carga DE CARGA ISO 27001 ASE/SQL
description: Implementar etapas da amostra de projeto de projeto de projeto de modelo de tipo de modelo de serviço de aplicações ISO 27001/SQL Database, incluindo detalhes do parâmetro do artefacto da planta.
ms.date: 07/13/2020
ms.topic: sample
ms.openlocfilehash: 74f2670b79d1968755e376d1f5a75bbb76e6e6c3
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87072896"
---
# <a name="deploy-the-iso-27001-app-service-environmentsql-database-workload-blueprint-sample"></a>Implementar a amostra de amostra de amostra de amostra de amostra de amostra de amostra de amostra de amostra de carga de carga de trabalho do serviço de aplicações ISO 27001/SQL Database

Para implementar a amostra de amostra de amostra de amostra de amostra de amostra de amostra de amostra de amostra de amostra de amostra de carga de trabalho da base de dados da Azure Blueprints ISO 27001, devem ser tomadas as seguintes medidas:

> [!div class="checklist"]
> - Implementar a amostra de projeto [iso 27001 serviços partilhados](../iso27001-shared/index.md)
> - Criar uma nova planta a partir da amostra
> - Marque a sua cópia da amostra como **Publicada**
> - Atribua a sua cópia da planta a uma subscrição existente

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free) antes de começar.

## <a name="deploy-the-iso-27001-shared-services-blueprint-sample"></a>Implementar a amostra de projeto iso 27001 serviços partilhados

Antes de esta amostra de planta poder ser implantada, a amostra de projeto [iso 27001 dos Serviços Partilhados](../iso27001-shared/index.md) deve ser implantada na subscrição-alvo. Sem uma implantação bem sucedida da amostra de projeto iso 27001 dos Serviços Partilhados, esta amostra de planta perderá dependências de infraestruturas e falhará durante a implantação.

> [!IMPORTANT]
> Esta amostra de planta deve ser atribuída na mesma subscrição que a amostra de projeto [iso 27001 dos Serviços Partilhados.](../iso27001-shared/index.md)

## <a name="create-blueprint-from-sample"></a>Criar planta a partir de amostra

Em primeiro lugar, implemente a amostra de planta criando uma nova planta no seu ambiente usando a amostra como entrada.

1. Selecione **Todos os serviços** no painel esquerdo. Procure e selecione **Plantas.**

1. A partir da página **'Iniciar'** à esquerda, selecione o botão **Criar** _uma plantação_.

1. Encontre a amostra de amostra de carga **de trabalho ISO 27001: ASE/SQL Workload** em _outras amostras_ e selecione **Utilize esta amostra**.

1. Introduza os _fundamentos_ da amostra da planta:

   - **Nome da planta**: Forneça um nome para a sua cópia da amostra de projeto de carga de trabalho ISO 27001 ASE/SQL.
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

   - Informações básicas

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
     - **ID de assinatura de serviço partilhado**: ID de assinatura onde a amostra de projeto [iso 27001 de serviços partilhados](../iso27001-shared/index.md) é atribuída.
     - **Prefixo prefixo do endereço de sub-rede preguiça:** A notação CIDR para a sub-rede por defeito da rede virtual.
       O valor predefinido é _de 10.1.0.0/16_.
     - **Localização da carga de trabalho**: Determina para que local os artefactos são implantados. Nem todos os serviços estão disponíveis em todos os locais. Os artefactos que implantam esses serviços fornecem uma opção de parâmetro para a localização para implantar esse artefacto.

   - Parâmetros de artefactos

     Os parâmetros definidos nesta secção aplicam-se ao artefacto sob o qual é definido. Estes parâmetros são [parâmetros dinâmicos,](../../concepts/parameters.md#dynamic-parameters) uma vez que são definidos durante a atribuição da planta. Para obter uma lista completa ou parâmetros de artefactos e suas descrições, consulte [a tabela de parâmetros do Artefacto](#artifact-parameters-table).

1. Uma vez introduzidos todos os parâmetros, **selecione Atribuir** na parte inferior da página. A atribuição da planta é criada e a implantação de artefactos começa. A implantação leva cerca de uma hora. Para verificar o estado de implantação, abra a atribuição do projeto.

> [!WARNING]
> O serviço Azure Blueprints e as amostras de plantas incorporadas estão **livres de custos.** Os recursos azure são [avaliados pelo produto.](https://azure.microsoft.com/pricing/) Utilize a [calculadora de preços](https://azure.microsoft.com/pricing/calculator/) para estimar o custo de funcionamento dos recursos utilizados por esta amostra de planta.

## <a name="artifact-parameters-table"></a>Tabela de parâmetros de artefacto

A tabela a seguir fornece uma lista dos parâmetros do artefacto da planta:

|Nome do artefacto|Tipo de artefacto|Nome do parâmetro|Descrição|
|-|-|-|-|
|Grupo de recursos Log Analytics|Grupo de recursos|Name|**Locked** - Concatenates o **nome da Organização** para tornar o grupo de recursos `-workload-log-rg` único.|
|Grupo de recursos Log Analytics|Grupo de recursos|Localização|**Bloqueado** - Usa o parâmetro da planta.|
|Modelo de Analítica de Log|Modelo do Resource Manager|Escalão de serviço|Define o nível do espaço de trabalho Log Analytics. O valor predefinido é _PerNode_.|
|Modelo de Analítica de Log|Modelo do Resource Manager|Retenção de registos em dias|Retenção de dados em dias. O valor predefinido é _de 365_.|
|Modelo de Analítica de Log|Modelo do Resource Manager|Localização|Região utilizada para criar o espaço de trabalho Log Analytics. O valor predefinido é _West US 2_.|
|Grupo de recursos de rede|Grupo de recursos|Name|**Locked** - Concatenates o **nome da Organização** para tornar o grupo de recursos `-workload-net-rg` único.|
|Grupo de recursos de rede|Grupo de recursos|Localização|**Bloqueado** - Usa o parâmetro da planta.|
|Modelo de grupo de segurança de rede|Modelo do Resource Manager|Retenção de registos em dias|Retenção de dados em dias. O valor predefinido é _de 365_.|
|Modelo de tabela de rede virtual e rota|Modelo do Resource Manager|IP privado de firewall Azure|Configura o IP privado da [firewall Azure](../../../../firewall/overview.md). Deve fazer parte da notação CIDR definida na _ISO 27001: Parâmetro de artefacto de serviços partilhados_ **Azure Firewall prefixo de endereço do sub-redes**. O valor predefinido é _de 10.0.4.4_.|
|Modelo de tabela de rede virtual e rota|Modelo do Resource Manager|ID de assinatura de serviços partilhados|Valor utilizado para permitir o espreitamento do VNET entre uma Carga de Trabalho e Serviços Partilhados.|
|Modelo de tabela de rede virtual e rota|Modelo do Resource Manager|Prefixo de endereço de rede virtual|A notação CIDR para a rede virtual. O valor predefinido é _de 10.1.0.0/16_.|
|Modelo de tabela de rede virtual e rota|Modelo do Resource Manager|Prefixo de endereço de sub-rede predefinido|A notação CIDR para a sub-rede por defeito da rede virtual. O valor predefinido é _de 10.1.0.0/16_.|
|Modelo de tabela de rede virtual e rota|Modelo do Resource Manager|ADICIONA ENDEREÇO IP|Endereço IP do primeiro ADDS VM. Este valor é usado como DNS VNET personalizado.|
|Grupo de recursos Key Vault|Grupo de recursos|Name|**Locked** - Concatenates o **nome da Organização** para tornar o grupo de recursos `-workload-kv-rg` único.|
|Grupo de recursos Key Vault|Grupo de recursos|Localização|**Bloqueado** - Usa o parâmetro da planta.|
|Modelo de abóbada de chave|Modelo do Resource Manager|ID de objeto AAD|O identificador de objetos AAD da conta que requer acesso à instância key vault. Sem valor padrão e não pode ser deixado em branco. Para localizar este valor a partir do portal Azure, procure e selecione "Utilizadores" em _Serviços_. Utilize a caixa _Nome_ para filtrar o nome da conta e selecione essa conta. Na página de perfil do _Utilizador,_ selecione o ícone "Clique para copiar" ao lado do ID do _objeto._|
|Modelo de abóbada de chave|Modelo do Resource Manager|Retenção de registos em dias|Retenção de dados em dias. O valor predefinido é _de 365_.|
|Modelo de abóbada de chave|Modelo do Resource Manager|SKU cofre-chave|Especifica o SKU do Cofre chave que é criado. O valor predefinido é _Premium._|
|Modelo de abóbada de chave|Modelo do Resource Manager|Nome de utilizador de administração Azure SQL Server|O nome de utilizador utilizado para aceder ao Azure SQL Server. Deve corresponder ao mesmo valor de propriedade no **modelo de base de dados Azure SQL**. O valor predefinido é _sql-administrador-utilizador_.|
|Grupo de recursos de base de dados Azure SQL|Grupo de recursos|Name|**Locked** - Concatenates o **nome da Organização** para tornar o grupo de recursos `-workload-azsql-rg` único.|
|Grupo de recursos de base de dados Azure SQL|Grupo de recursos|Localização|**Bloqueado** - Usa o parâmetro da planta.|
|Modelo de base de dados Azure SQL|Modelo do Resource Manager|Nome de utilizador de administração Azure SQL Server|Nome de utilizador para o Azure SQL Server. Deve corresponder ao mesmo valor de propriedade no **modelo key vault**. O valor predefinido é _sql-administrador-utilizador_.|
|Modelo de base de dados Azure SQL|Modelo do Resource Manager|Senha de administração do Azure SQL Server (ID de recurso do cofre de chaves)|A identificação de recursos do Cofre de Chaves. Utilize "/subscrição/{subscriçãoId}/resourceGroups/{orgName}-workload-kv/providers/Microsoft.KeyVault/vaults/{orgName}-workload-kv" e `{subscriptionId}` substitua-o pelo seu ID de subscrição e `{orgName}` pelo parâmetro de planta do nome da **Organização.**|
|Modelo de base de dados Azure SQL|Modelo do Resource Manager|Senha de administração do Azure SQL Server (Key Vault Secret Name)|Nome de utilizador do administrador do SQL Server. Deve corresponder o valor na propriedade **do modelo do Cofre de Chaves** **Azure SQL Server nome de utilizador**de administração .|
|Modelo de base de dados Azure SQL|Modelo do Resource Manager|Retenção de registos em dias|Retenção de dados em dias. O valor predefinido é _de 365_.|
|Modelo de base de dados Azure SQL|Modelo do Resource Manager|ID de objeto de administração AAD|ID de objeto AAD do utilizador que será designado como administrador do Ative Directory. Sem valor padrão e não pode ser deixado em branco. Para localizar este valor a partir do portal Azure, procure e selecione "Utilizadores" em _Serviços_. Utilize a caixa _Nome_ para filtrar o nome da conta e selecione essa conta. Na página de perfil do _Utilizador,_ selecione o ícone "Clique para copiar" ao lado do ID do _objeto._|
|Modelo de base de dados Azure SQL|Modelo do Resource Manager|Login de administração AAD|Atualmente, as contas da Microsoft (como live.com ou outlook.com) não podem ser definidas como administradora. Apenas os utilizadores e grupos de segurança dentro da sua organização podem ser definidos como administradores. Sem valor padrão e não pode ser deixado em branco. Para localizar este valor a partir do portal Azure, procure e selecione "Utilizadores" em _Serviços_. Utilize a caixa _Nome_ para filtrar o nome da conta e selecione essa conta. Na página de perfil do _Utilizador,_ copie o _nome de Utilizador._|
|Grupo de recursos de serviço de aplicação ambiente|Grupo de recursos|Name|**Locked** - Concatenates o **nome da Organização** para tornar o grupo de recursos `-workload-ase-rg` único.|
|Grupo de recursos de serviço de aplicação ambiente|Grupo de recursos|Localização|**Bloqueado** - Usa o parâmetro da planta.|
|Modelo de ambiente de serviço de aplicativo|Modelo do Resource Manager|Nome de domínio|Nome do Diretório Ativo criado pela amostra. O valor predefinido é _contoso.com_.|
|Modelo de ambiente de serviço de aplicativo|Modelo do Resource Manager|Localização ASE|Localização do Ambiente do Serviço de Aplicações. O valor predefinido é _West US 2_.|
|Modelo de ambiente de serviço de aplicativo|Modelo do Resource Manager|Retenção de registos de gateway de aplicação em dias|Retenção de dados em dias. O valor predefinido é _de 365_.|

## <a name="next-steps"></a>Passos seguintes

Agora que reviu os passos para implementar a amostra de projeto de projeto de projeto de carga de trabalho do Serviço de Aplicações ISO 27001/SQL Database, visite os seguintes artigos para saber sobre a arquitetura e o mapeamento de controlo:

> [!div class="nextstepaction"]
> [ISO 27001 App Service Environment/SQL Database projeto de carga de carga - Visão geral](./index.md) 
>  [ISO 27001 App Service Environment/SQL Database projeto de carga de carga de trabalho - Controlo de mapeamento](./control-mapping.md)

Artigos adicionais sobre esquemas e como os utilizar:

- Saiba mais sobre o [ciclo de vida do esquema](../../concepts/lifecycle.md).
- Compreenda como utilizar [parâmetros estáticos e dinâmicos](../../concepts/parameters.md).
- Aprenda a personalizar a [ordem de sequenciação do esquema](../../concepts/sequencing-order.md).
- Saiba como utilizar o [bloqueio de recursos de esquema](../../concepts/resource-locking.md).
- Saiba como [atualizar as atribuições existentes](../../how-to/update-existing-assignments.md).
