---
title: Implementar a amostra de planta de carga de trabalho Da ISO 27001 ASE/SQL
description: Implemente etapas da amostra de função de trabalho de base de trabalho iso 27001 do serviço de aplicações Ambiente/SQL Database, incluindo detalhes do parâmetro de artefacto de plantas.
ms.date: 01/13/2020
ms.topic: sample
ms.openlocfilehash: 6b8f3b753f1dd8cfbc247a77f2004e3c4d3423bb
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "75922582"
---
# <a name="deploy-the-iso-27001-app-service-environmentsql-database-workload-blueprint-sample"></a>Implemente a amostra de plano de trabalho de trabalho do serviço de aplicações ISO 27001 Ambiente/Base de Dados De base de dados SQL

Para implementar a amostra de projeto de trabalho de trabalho do Serviço de Aplicações ISO 27001 do Serviço de Aplicações ISO 27001, devem ser tomadas as seguintes medidas:

> [!div class="checklist"]
> - Implementar a amostra de plantas [iso 27001 serviços partilhados](../iso27001-shared/index.md)
> - Criar uma nova planta a partir da amostra
> - Marque a sua cópia da amostra como **Publicado**
> - Atribuir a sua cópia da planta a uma subscrição existente

Se não tiver uma subscrição Azure, crie uma [conta gratuita](https://azure.microsoft.com/free) antes de começar.

## <a name="deploy-the-iso-27001-shared-services-blueprint-sample"></a>Implementar a amostra de plantas iso 27001 serviços partilhados

Antes de esta amostra de planta poder ser implementada, a amostra de projeto [iso 27001 Serviços Partilhados](../iso27001-shared/index.md) deve ser implantada para a subscrição do alvo. Sem uma implementação bem sucedida da amostra de projeto iso 27001 serviços partilhados, esta amostra de plantas perderá dependências de infraestruturas e falhará durante a implantação.

> [!IMPORTANT]
> Esta amostra de projeto deve ser atribuída na mesma subscrição que a amostra de projeto [iso 27001 Shared Services.](../iso27001-shared/index.md)

## <a name="create-blueprint-from-sample"></a>Criar a planta a partir da amostra

Em primeiro lugar, implemente a amostra de planta criando uma nova planta no seu ambiente usando a amostra como entrada.

1. Selecione **Todos os serviços** no painel esquerdo. Procure e selecione **Plantas**.

1. A partir da página **iniciar** à esquerda, selecione o botão **Criar** _uma planta_.

1. Encontre a amostra **iso 27001: ASE/SQL Workload** em _outras amostras_ e selecione **Utilize esta amostra**.

1. Introduza os _Fundamentos_ da amostra de plantas:

   - **Nome**da planta : Forneça um nome para a sua cópia da amostra de projeto de carga de trabalho Iso 27001 ASE/SQL.
   - **Localização da definição**: Utilize a elipse e selecione o grupo de gestão para guardar a sua cópia da amostra para.

1. Selecione o separador _Artefactos_ na parte superior da página ou **Seguinte: Artefactos** na parte inferior da página.

1. Reveja a lista de artefactos que compõem a amostra da planta. Muitos dos artefactos têm parâmetros que definiremos mais tarde. Selecione **Guardar Rascunho** quando terminar de rever a amostra de plantas.

## <a name="publish-the-sample-copy"></a>Publicar a cópia da amostra

A sua cópia da amostra de plantas foi agora criada no seu ambiente. É criado em modo **Draft** e deve ser **publicado** antes de ser atribuído e implantado. A cópia da amostra de plantas pode ser personalizada para o seu ambiente e necessidades, mas essa modificação pode afastá-la da norma ISO 27001.

1. Selecione **Todos os serviços** no painel esquerdo. Procure e selecione **Plantas**.

1. Selecione a página de **definições** de Blueprint à esquerda. Utilize os filtros para encontrar a sua cópia da amostra de plantas e, em seguida, selecione-a.

1. Selecione **Publicar a planta** no topo da página. Na nova página à direita, forneça uma **Versão** para a sua cópia da amostra de plantas. Esta propriedade é útil para se fizer uma modificação mais tarde. Forneça notas de **alteração** como "Primeira versão publicada a partir da amostra de plantas ISO 27001." Em seguida, **selecione Publicar** na parte inferior da página.

## <a name="assign-the-sample-copy"></a>Atribuir a cópia da amostra

Uma vez que a cópia da amostra de plantas tenha sido **publicada**com sucesso, pode ser atribuída a uma subscrição dentro do grupo de gestão a que foi guardada. Este passo é onde são fornecidos parâmetros para tornar cada implantação da cópia da amostra de plantas única.

1. Selecione **Todos os serviços** no painel esquerdo. Procure e selecione **Plantas**.

1. Selecione a página de **definições** de Blueprint à esquerda. Utilize os filtros para encontrar a sua cópia da amostra de plantas e, em seguida, selecione-a.

1. Selecione **a planta de atribuição** na parte superior da página de definição de planta.

1. Fornecer os valores do parâmetro para a atribuição do projeto:

   - Noções básicas

     - **Assinaturas**: Selecione uma ou mais das subscrições que estão no grupo de gestão para a qual guardou a sua cópia da amostra de projeto. Se selecionar mais do que uma subscrição, será criada uma atribuição para cada utilização dos parâmetros introduzidos.
     - **Nome de atribuição**: O nome é pré-povoado para si com base no nome da planta.
       Mude conforme necessário ou saia como está.
     - **Localização**: Selecione uma região para a identidade gerida a criar. O Azure Blueprint utiliza esta identidade gerida para implementar todos os artefactos no esquema atribuído. Para saber mais, consulte [identidades geridas para os recursos do Azure.](../../../../active-directory/managed-identities-azure-resources/overview.md)
     - Versão de **definição**de planta : Escolha uma versão **publicada** da sua cópia da amostra de plantas.

   - Atribuição de bloqueio

     Selecione a definição de bloqueio da planta para o seu ambiente. Para obter mais informações, veja [bloqueio de recurso em esquemas](../../concepts/resource-locking.md).

   - Identidade Gerida

     Deixe o sistema predefinido _atribuído_ à opção de identidade gerida.

   - Parâmetros de esquema

     Os parâmetros definidos nesta secção são utilizados por muitos dos artefactos na definição de planta para fornecer consistência.

     - **Nome**da organização : Insira um nome curto para a sua organização. Esta propriedade é usada principalmente para nomear recursos.
     - **ID de subscrição**de serviço partilhado : ID de subscrição onde a amostra de plantas [de serviços partilhados ISO 27001](../iso27001-shared/index.md) é atribuída.
     - **Prefixo de endereço de sub-rede predefinido**: Notação CIDR para a sub-rede padrão da rede virtual.
       O valor predefinido é _de 10.1.0.0/16_.
     - **Localização da carga de trabalho**: Determina para que localização os artefactos são implantados. Nem todos os serviços estão disponíveis em todos os locais. Os artefactos que implantam tais serviços fornecem uma opção parametrómetro para a localização para implantar esse artefacto para.

   - Parâmetros de artefacto

     Os parâmetros definidos nesta secção aplicam-se ao artefacto sob o qual é definido. Estes parâmetros são [parâmetros dinâmicos](../../concepts/parameters.md#dynamic-parameters) uma vez que são definidos durante a atribuição da planta. Para obter uma lista completa ou parâmetros de artefactos e suas descrições, consulte a [tabela de parâmetros do Artefacto](#artifact-parameters-table).

1. Uma vez introduzidos todos os parâmetros, **selecione Atribuir** na parte inferior da página. A atribuição da planta é criada e a implantação de artefactos começa. O destacamento demora cerca de uma hora. Para verificar o estado da implantação, abra a atribuição da planta.

> [!WARNING]
> O serviço Azure Blueprints e as amostras de plantas incorporadas estão **isentos de custos.** Os recursos azure são [avaliados pelo produto.](https://azure.microsoft.com/pricing/) Utilize a [calculadora](https://azure.microsoft.com/pricing/calculator/) de preços para estimar o custo dos recursos de funcionamento implantados por esta amostra de plantas.

## <a name="artifact-parameters-table"></a>Tabela de parâmetros de artefactos

A tabela seguinte fornece uma lista dos parâmetros do artefacto da planta:

|Nome do artefacto|Tipo de artefacto|Nome do parâmetro|Descrição|
|-|-|-|-|
|Grupo de recursos Log Analytics|Grupo de recursos|Nome|**Locked** - Concatenates the `-workload-log-rg` Organization **name** with to make the resource group unique.|
|Grupo de recursos Log Analytics|Grupo de recursos|Localização|**Bloqueado** - Usa o parâmetro da planta.|
|Modelo de Análise de Log|Modelo do Resource Manager|Camada de serviços|Define o nível do espaço de trabalho Log Analytics. O valor predefinido é _PerNode_.|
|Modelo de Análise de Log|Modelo do Resource Manager|Retenção de registo em dias|Retenção de dados em dias. O valor predefinido é _de 365_.|
|Modelo de Análise de Log|Modelo do Resource Manager|Localização|Região utilizada para criar o espaço de trabalho Log Analytics. O valor padrão é _West US 2_.|
|Grupo de recursos de rede|Grupo de recursos|Nome|**Locked** - Concatenates the `-workload-net-rg` Organization **name** with to make the resource group unique.|
|Grupo de recursos de rede|Grupo de recursos|Localização|**Bloqueado** - Usa o parâmetro da planta.|
|Modelo de grupo de segurança de rede|Modelo do Resource Manager|Retenção de registo em dias|Retenção de dados em dias. O valor predefinido é _de 365_.|
|Modelo de tabela de rede virtual e rota|Modelo do Resource Manager|IP privado de firewall Azure|Configura o IP privado da [firewall Azure.](../../../../firewall/overview.md) Deve fazer parte da notação CIDR definida na _ISO 27001:_ Prefixo de endereço de endereço de **sub-rede de**artefactos de serviços partilhados Azure Firewall . O valor predefinido é _de 10.0.4.4_.|
|Modelo de tabela de rede virtual e rota|Modelo do Resource Manager|ID de subscrição de serviços partilhados|Valor utilizado para permitir o vNET espreitar entre uma Carga de Trabalho e Serviços Partilhados.|
|Modelo de tabela de rede virtual e rota|Modelo do Resource Manager|Prefixo de endereço de rede virtual|A notação CIDR para a rede virtual. O valor predefinido é _de 10.1.0.0/16_.|
|Modelo de tabela de rede virtual e rota|Modelo do Resource Manager|Prefixo de endereço de sub-rede predefinido|Notação CIDR para a sub-rede padrão de rede virtual. O valor predefinido é _de 10.1.0.0/16_.|
|Modelo de tabela de rede virtual e rota|Modelo do Resource Manager|Endereço IP adiciona|Endereço IP do primeiro ADDS VM. Este valor é usado como DNS Personalizado VNET.|
|Grupo de recursos key vault|Grupo de recursos|Nome|**Locked** - Concatenates the `-workload-kv-rg` Organization **name** with to make the resource group unique.|
|Grupo de recursos key vault|Grupo de recursos|Localização|**Bloqueado** - Usa o parâmetro da planta.|
|Modelo de cofre de chave|Modelo do Resource Manager|Id de objeto AAD|O identificador de objetos AAD da conta que requer acesso à instância do Cofre chave. Sem valor predefinido e não pode ser deixado em branco. Para localizar este valor a partir do portal Azure, procure e selecione "Utilizadores" nos _Serviços_. Utilize a caixa _nome_ para filtrar o nome da conta e selecione essa conta. Na página do perfil do _Utilizador,_ selecione o ícone "Clique para copiar" ao lado do ID do _objeto_.|
|Modelo de cofre de chave|Modelo do Resource Manager|Retenção de registo em dias|Retenção de dados em dias. O valor predefinido é _de 365_.|
|Modelo de cofre de chave|Modelo do Resource Manager|Cofre chave SKU|Especifica o SKU do Cofre chave que é criado. O valor predefinido é _Premium._|
|Modelo de cofre de chave|Modelo do Resource Manager|Nome de utilizador do Servidor Azure SQL|O nome de utilizador usado para aceder ao Azure SQL Server. Deve corresponder ao mesmo valor de propriedade no modelo de base de **dados Azure SQL**. O valor predefinido é _o utilizador sql-admin_.|
|Grupo de recursos azure SQL Database|Grupo de recursos|Nome|**Locked** - Concatenates the `-workload-azsql-rg` Organization **name** with to make the resource group unique.|
|Grupo de recursos azure SQL Database|Grupo de recursos|Localização|**Bloqueado** - Usa o parâmetro da planta.|
|Modelo de base de dados Azure SQL|Modelo do Resource Manager|Nome de utilizador do Servidor Azure SQL|Nome de utilizador para o Servidor Azure SQL. Deve corresponder ao mesmo valor de propriedade no **modelo key vault**. O valor predefinido é _o utilizador sql-admin_.|
|Modelo de base de dados Azure SQL|Modelo do Resource Manager|Senha de administração do Servidor Azure SQL (ID de recurso do cofre chave)|A identificação de recursos do cofre chave. Utilize "/subscrição/{subscriçãoId}/resourceGroups/{orgName}-workload-kv/providers/Microsoft.KeyVault/vaults/{orgName}-workload-kv" e substitua-o `{subscriptionId}` pelo seu ID de subscrição e `{orgName}` pelo parâmetro de projeto de nome da **Organização.**|
|Modelo de base de dados Azure SQL|Modelo do Resource Manager|Senha de administração do Servidor Azure SQL (Nome Secreto do Cofre chave)|Nome de utilizador do administrador do SQL Server. Deve corresponder ao valor na propriedade do **modelo do cofre chave** **Azure SQL Server nome de utilizador**.|
|Modelo de base de dados Azure SQL|Modelo do Resource Manager|Retenção de registo em dias|Retenção de dados em dias. O valor predefinido é _de 365_.|
|Modelo de base de dados Azure SQL|Modelo do Resource Manager|Id de objeto de administrador aAD|Id de objeto AAD do utilizador que será atribuído como administrador de Diretório Ativo. Sem valor predefinido e não pode ser deixado em branco. Para localizar este valor a partir do portal Azure, procure e selecione "Utilizadores" nos _Serviços_. Utilize a caixa _nome_ para filtrar o nome da conta e selecione essa conta. Na página do perfil do _Utilizador,_ selecione o ícone "Clique para copiar" ao lado do ID do _objeto_.|
|Modelo de base de dados Azure SQL|Modelo do Resource Manager|Login de administrador da AAD|Atualmente, as contas da Microsoft (como live.com ou outlook.com) não podem ser definidas como administradora. Apenas utilizadores e grupos de segurança dentro da sua organização podem ser definidos como administradores. Sem valor predefinido e não pode ser deixado em branco. Para localizar este valor a partir do portal Azure, procure e selecione "Utilizadores" nos _Serviços_. Utilize a caixa _nome_ para filtrar o nome da conta e selecione essa conta. Na página do perfil do _Utilizador,_ copie o _nome utilizador_.|
|Grupo de recursos do ambiente do serviço de aplicação|Grupo de recursos|Nome|**Locked** - Concatenates the `-workload-ase-rg` Organization **name** with to make the resource group unique.|
|Grupo de recursos do ambiente do serviço de aplicação|Grupo de recursos|Localização|**Bloqueado** - Usa o parâmetro da planta.|
|Modelo de ambiente de serviço de aplicativo|Modelo do Resource Manager|Nome de domínio|Nome do Diretório Ativo criado pela amostra. O valor predefinido é _contoso.com_.|
|Modelo de ambiente de serviço de aplicativo|Modelo do Resource Manager|Localização ASE|Localização ambiente de serviço de aplicativos. O valor padrão é _West US 2_.|
|Modelo de ambiente de serviço de aplicativo|Modelo do Resource Manager|Retenção de log gateway de aplicação em dias|Retenção de dados em dias. O valor predefinido é _de 365_.|

## <a name="next-steps"></a>Passos seguintes

Agora que reviu os passos para implementar a amostra de planode carga de trabalho iso 27001 App Service Environment/SQL Database, visite os seguintes artigos para saber sobre a arquitetura e o mapeamento de controlo:

> [!div class="nextstepaction"]
> [ISO 27001 App Service Environment/SQL Base de Dados base de dados - Visão geral](./index.md)
> [ISO 27001 App Service Environment/SQL Database workload blueprint - Mapeamento de controlo](./control-mapping.md)

Artigos adicionais sobre esquemas e como os utilizar:

- Saiba mais sobre o [ciclo de vida do esquema](../../concepts/lifecycle.md).
- Compreenda como utilizar [parâmetros estáticos e dinâmicos](../../concepts/parameters.md).
- Aprenda a personalizar a [ordem de sequenciação do esquema](../../concepts/sequencing-order.md).
- Saiba como utilizar o [bloqueio de recursos de esquema](../../concepts/resource-locking.md).
- Saiba como [atualizar as atribuições existentes](../../how-to/update-existing-assignments.md).