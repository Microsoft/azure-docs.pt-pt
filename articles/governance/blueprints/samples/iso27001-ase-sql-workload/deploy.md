---
title: Exemplo - esquema de carga de trabalho de base de dados do ISO 27001 aplicação serviço ambiente/SQL - implementar passos
description: Implemente os passos para o exemplo de plano gráfico de carga de trabalho de base de dados do ISO 27001 aplicação serviço ambiente/SQL.
services: blueprints
author: DCtheGeek
ms.author: dacoulte
ms.date: 03/14/2019
ms.topic: conceptual
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: 3130616a4373f29765e47308c379320230641322
ms.sourcegitcommit: 4133f375862fdbdec07b70de047d70c66ac29d50
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/15/2019
ms.locfileid: "57995668"
---
# <a name="deploy-the-azure-blueprints-iso-27001-app-service-environmentsql-database-workload-blueprint-sample"></a>Implementar o exemplo de plano gráfico de carga de trabalho do Azure esquemas ISO 27001 aplicação serviço ambiente/base de dados SQL

Para implementar o exemplo de plano gráfico de carga de trabalho do Azure esquemas ISO 27001 aplicação serviço ambiente/base de dados SQL, é necessário os seguintes passos:

> [!div class="checklist"]
> - Implementar o [ISO 27001 Shared Services](../iso27001-shared/index.md) exemplo de esquema
> - Criar um novo plano de gráfico do exemplo
> - Marcar a sua cópia do exemplo de como **publicado**
> - Atribuir a sua cópia do esquema a uma subscrição existente

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free) antes de começar.

## <a name="deploy-the-iso-27001-shared-services-blueprint-sample"></a>Implementar o exemplo de esquema de serviços de compartilhados do ISO 27001

Antes deste exemplo de esquema pode ser implementado, o [ISO 27001 Shared Services](../iso27001-shared/index.md) exemplo de plano gráfico tem de ser implementado para a subscrição de destino. Sem uma implementação bem-sucedida do exemplo de plano gráfico ISO 27001 Shared Services, este exemplo de plano gráfico irá ser dependências em falta infraestrutura e falhar durante a implementação.

> [!IMPORTANT]
> Este exemplo de plano gráfico tem de ser atribuído na mesma subscrição que o [ISO 27001 Shared Services](../iso27001-shared/index.md) exemplo de esquema.

## <a name="create-blueprint-from-sample"></a>Criar o plano gráfico de exemplo

Em primeiro lugar, implementa o exemplo de esquema ao criar uma novo esquema no seu ambiente com o exemplo de como um starter.

1. Selecione **todos os serviços** e procure e selecione **política** no painel esquerdo. Sobre o **diretiva** página, selecione **esquemas**.

1. Do **introdução** página à esquerda, selecione a **Create** botão sob _criar um plano gráfico_.

1. Encontrar o **ISO 27001: Carga de trabalho do ASE/SQL** exemplo de esquema sob _outros exemplos_ e selecione **utilizar este exemplo**.

1. Introduza o _Noções básicas_ do exemplo de esquema:

   - **Nome do blueprint**: Forneça um nome para a sua cópia do exemplo de plano gráfico de carga de trabalho de ISO 27001 ASE/SQL.
   - **Localização da definição**: Utilize o botão de reticências e selecione o grupo de gestão para guardar a sua cópia do exemplo para.

1. Selecione o _artefactos_ separador na parte superior da página ou **seguinte: Artefactos** na parte inferior da página.

1. Reveja a lista de artefactos que constituem o exemplo de esquema. Muitos dos artefatos os parâmetros que vamos definir mais tarde. Selecione **Guardar rascunho** quando terminar a rever o exemplo de esquema.

## <a name="publish-the-sample-copy"></a>Publicar a cópia de exemplo

Sua cópia do exemplo de plano gráfico agora foi criada no seu ambiente. Ele é criado na **rascunho** modo e têm de ser **publicado** antes de pode ser atribuída e implantada. A cópia do exemplo de esquema pode ser personalizada para seu ambiente e as necessidades, mas que a modificação pode movê-lo para fora da norma ISO 27001.

1. Selecione **todos os serviços** e procure e selecione **política** no painel esquerdo. Sobre o **diretiva** página, selecione **esquemas**.

1. Selecione o **definições de esquema** página à esquerda. Utilize os filtros para encontrar a sua cópia do exemplo de esquema e, em seguida, selecioná-lo.

1. Selecione **publicar esquema** na parte superior da página. A nova página à direita, fornecem uma **versão** para obter uma cópia do exemplo de esquema. Esta propriedade é útil para se tornar uma modificação mais tarde. Fornecer **alterar notas** como "primeira versão publicada do exemplo ISO 27001 esquema." Em seguida, selecione **publicar** na parte inferior da página.

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

   - Atribuição de Bloqueio

     Selecione o bloqueio de esquema definição para o seu ambiente. Para obter mais informações, veja [bloqueio de recurso em esquemas](../../concepts/resource-locking.md).

   - Identidade Gerida

     Deixe a predefinição _sistema atribuído_ geridos a opção de identidade.

   - Parâmetros de esquema

     Os parâmetros definidos nesta secção são utilizados por muitos dos artefatos na definição do esquema para fornecer consistência.

     - **Nome da organização**: Introduza um nome curto para a sua organização. Esta propriedade é usada principalmente para atribuir nomes a recursos.
     - **Partilhado ID de subscrição do serviço**: ID de subscrição em que o [ISO 27001 Shared Services](../iso27001-shared/index.md) exemplo de esquema está atribuído.
     - **Prefixo de endereço de sub-rede predefinido**: A notação CIDR para a sub-rede de predefinição da rede virtual.
       Valor predefinido é _10.1.0.0/16_.
     - **Localização da carga de trabalho**: Determina o local em que os artefactos são implementados. Nem todos os serviços estão disponíveis em todas as localizações. Artefatos de implementação de tais serviços fornecem uma opção de parâmetro para a localização implementar esse artefacto a.

   - Parâmetros de artefacto

     Os parâmetros definidos nesta secção aplicam-se para o artefacto sob a qual está definido. Esses parâmetros são [parâmetros dinâmicos](../../concepts/parameters.md#dynamic-parameters) , uma vez que estão definidos durante a atribuição do esquema. Para ver uma lista completa ou parâmetros de artefacto e suas descrições, veja [tabela de parâmetros de artefacto](#artifact-parameters-table).

1. Depois de tem sido introduzidos todos os parâmetros, selecione **atribuir** na parte inferior da página.

> [!WARNING]
> O serviço de esquemas do Azure e os exemplos de esquema incorporados estão **gratuita**. Recursos do Azure estão [o preço por produto](https://azure.microsoft.com/pricing/). Utilize o [Calculadora de preços](https://azure.microsoft.com/pricing/calculator/) para calcular o custo da execução de recursos implementados por este exemplo de esquema.

## <a name="artifact-parameters-table"></a>Tabela de parâmetros de artefacto

A tabela seguinte fornece uma lista do plano gráfico de parâmetros de artefacto:

|Nome do artefacto|Tipo de artefacto|Nome do parâmetro|Descrição|
|-|-|-|-|
|Grupo de recursos do log Analytics|Grupo de recursos|Name|**Bloqueado** -concatena a **nome da organização** com `-workload-log-rg` para tornar o grupo de recursos exclusivo.|
|Grupo de recursos do log Analytics|Grupo de recursos|Localização|**Bloqueado** -utiliza o parâmetro de esquema.|
|Modelo do Log Analytics|Modelo do Resource Manager|Camada de serviços|Define a camada da área de trabalho do Log Analytics. Valor predefinido é _PerNode_.|
|Modelo do Log Analytics|Modelo do Resource Manager|Retenção do registo em dias|Retenção de dados em dias. Valor predefinido é _365_.|
|Modelo do Log Analytics|Modelo do Resource Manager|Localização|Região utilizada para criar a área de trabalho do Log Analytics. Valor predefinido é _E.U.A. oeste 2_.|
|Grupo de recursos de rede|Grupo de recursos|Name|**Bloqueado** -concatena a **nome da organização** com `-workload-net-rg` para tornar o grupo de recursos exclusivo.|
|Grupo de recursos de rede|Grupo de recursos|Localização|**Bloqueado** -utiliza o parâmetro de esquema.|
|Modelo do Grupo de Segurança de Rede|Modelo do Resource Manager|Retenção do registo em dias|Retenção de dados em dias. Valor predefinido é _365_.|
|Modelo da Rede Virtual e Tabela de Rotas|Modelo do Resource Manager|IP privado do Azure Firewall|Configura o IP privado dos [firewall do Azure](../../../../firewall/overview.md). Deve fazer parte da notação CIDR definida no _ISO 27001: Serviços compartilhados_ parâmetro de artefacto **prefixo de endereço de sub-rede de Firewall do Azure**. Valor predefinido é _10.0.4.4_.|
|Modelo da Rede Virtual e Tabela de Rotas|Modelo do Resource Manager|ID da Subscrição dos Serviços partilhados|Valor utilizado para ativar o VNET peering entre uma carga de trabalho e serviços compartilhados.|
|Modelo da Rede Virtual e Tabela de Rotas|Modelo do Resource Manager|Prefixo do endereço da Rede Virtual|A notação CIDR para a rede virtual. Valor predefinido é _10.1.0.0/16_.|
|Modelo da Rede Virtual e Tabela de Rotas|Modelo do Resource Manager|Prefixo do endereço de sub-rede predefinido|A notação CIDR para a sub-rede de predefinição da rede virtual. Valor predefinido é _10.1.0.0/16_.|
|Modelo da Rede Virtual e Tabela de Rotas|Modelo do Resource Manager|Endereço IP do ADDS|Endereço IP da VM adiciona primeiro. Este valor é utilizado como o DNS personalizado da VNET.|
|Grupo de recursos do Cofre de chaves|Grupo de recursos|Name|**Bloqueado** -concatena a **nome da organização** com `-workload-kv-rg` para tornar o grupo de recursos exclusivo.|
|Grupo de recursos do Cofre de chaves|Grupo de recursos|Localização|**Bloqueado** -utiliza o parâmetro de esquema.|
|Modelo do Key Vault|Modelo do Resource Manager|ID do objeto do AAD|O identificador de objeto do AAD da conta de que necessita de acesso para a instância do Key Vault. Sem predefinição de valor e não pode ser deixado em branco. Para localizar este valor a partir do portal do Azure, procure e selecione "Utilizadores" em _serviços_. Utilize o _nome_ caixa para filtrar para o nome da conta e selecionar essa conta. No _perfil de utilizador_ página, selecione o ícone de "Clique para copiar" junto a _ID de objeto_.|
|Modelo do Key Vault|Modelo do Resource Manager|Retenção do registo em dias|Retenção de dados em dias. Valor predefinido é _365_.|
|Modelo do Key Vault|Modelo do Resource Manager|SKU do Key Vault|Especifica o SKU de Cofre de chaves do que é criado. Valor predefinido é _Premium_.|
|Modelo do Key Vault|Modelo do Resource Manager|Nome do administrador do SQL Server do Azure|O nome de utilizador utilizada para aceder ao servidor SQL do Azure. Tem de corresponder ao valor da propriedade mesmo nas **modelo de base de dados do Azure SQL**. Valor predefinido é _utilizador de administrador de sql_.|
|Grupo de recursos de base de dados SQL do Azure|Grupo de recursos|Name|**Bloqueado** -concatena a **nome da organização** com `-workload-azsql-rg` para tornar o grupo de recursos exclusivo.|
|Grupo de recursos de base de dados SQL do Azure|Grupo de recursos|Localização|**Bloqueado** -utiliza o parâmetro de esquema.|
|Modelo da Base de Dados SQL do Azure|Modelo do Resource Manager|Nome do administrador do SQL Server do Azure|Nome de utilizador para o servidor SQL do Azure. Tem de corresponder ao valor da propriedade mesmo nas **modelo de Key Vault**. Valor predefinido é _utilizador de administrador de sql_.|
|Modelo da Base de Dados SQL do Azure|Modelo do Resource Manager|Senha de administrador do Azure do SQL Server (ID de recurso do Key Vault)|O ID de recurso do Cofre de chaves. Utilize "/ subscription/{subscriptionId}/resourceGroups/{orgName}-workload-kv/providers/Microsoft.KeyVault/vaults/{orgName}-workload-kv" e substitua `{subscriptionId}` pelo ID da subscrição e `{orgName}` com o  **Nome da organização** esquema de parâmetro.|
|Modelo da Base de Dados SQL do Azure|Modelo do Resource Manager|Senha de administrador do Azure do SQL Server (nome de segredo do Key Vault)|Nome de utilizador do SQL administrador do servidor. Tem de corresponder ao valor na **modelo de Key Vault** propriedade **nome de utilizador de administrador de servidor SQL do Azure**.|
|Modelo da Base de Dados SQL do Azure|Modelo do Resource Manager|Retenção do registo em dias|Retenção de dados em dias. Valor predefinido é _365_.|
|Modelo da Base de Dados SQL do Azure|Modelo do Resource Manager|ID de objeto de administrador do AAD|ID de objeto do AAD do utilizador que irá obter atribuído como um administrador do Active Directory. Sem predefinição de valor e não pode ser deixado em branco. Para localizar este valor a partir do portal do Azure, procure e selecione "Utilizadores" em _serviços_. Utilize o _nome_ caixa para filtrar para o nome da conta e selecionar essa conta. No _perfil de utilizador_ página, selecione o ícone de "Clique para copiar" junto a _ID de objeto_.|
|Modelo da Base de Dados SQL do Azure|Modelo do Resource Manager|Início de sessão de administrador AAD|Atualmente, as contas Microsoft (como live.com ou outlook.com) não podem ser definidas como administrador. Apenas os utilizadores e grupos de segurança da sua organização podem ser definidos como administrador. Sem predefinição de valor e não pode ser deixado em branco. Para localizar este valor a partir do portal do Azure, procure e selecione "Utilizadores" em _serviços_. Utilize o _nome_ caixa para filtrar para o nome da conta e selecionar essa conta. Sobre o _perfil de utilizador_ página, copie a _nome de utilizador_.|
|Grupo de recursos de ambiente de serviço de aplicações|Grupo de recursos|Name|**Bloqueado** -concatena a **nome da organização** com `-workload-ase-rg` para tornar o grupo de recursos exclusivo.|
|Grupo de recursos de ambiente de serviço de aplicações|Grupo de recursos|Localização|**Bloqueado** -utiliza o parâmetro de esquema.|
|Modelo do Ambiente do Serviço de Aplicações|Modelo do Resource Manager|Nome de domínio|Nome do Active Directory criadas pelo exemplo. Valor predefinido é _contoso.com_.|
|Modelo do Ambiente do Serviço de Aplicações|Modelo do Resource Manager|Localização do ASE|Localização do ambiente de serviço de aplicações. Valor predefinido é _E.U.A. oeste 2_.|
|Modelo do Ambiente do Serviço de Aplicações|Modelo do Resource Manager|Retenção do registo do Gateway de Aplicação em dias|Retenção de dados em dias. Valor predefinido é _365_.|

## <a name="next-steps"></a>Passos Seguintes

Agora que reviu os passos para implementar o exemplo de plano gráfico de carga de trabalho de base de dados do ISO 27001 aplicação serviço ambiente/SQL, visite os seguintes artigos para saber mais sobre a arquitetura e o mapeamento de controle:

> [!div class="nextstepaction"]
> [Esquema de carga de trabalho de base de dados do ISO 27001 aplicação serviço ambiente/SQL - descrição geral](./index.md)
> [esquema da carga de trabalho de base de dados do ISO 27001 aplicação serviço ambiente/SQL - mapeamento de controlo](./control-mapping.md)

Artigos de adição sobre esquemas e como utilizá-los:

- Saiba mais sobre o [ciclo de vida de esquema](../../concepts/lifecycle.md).
- Compreender como usar [parâmetros estáticos e dinâmicos](../../concepts/parameters.md).
- Aprenda a personalizar a [esquema de ordem de sequenciamento](../../concepts/sequencing-order.md).
- Descubra como tornar a usar [esquema de bloqueio do recurso](../../concepts/resource-locking.md).
- Saiba como [atualizar atribuições existentes](../../how-to/update-existing-assignments.md).