---
title: Implementar a amostra de plantas de serviços partilhados ISO 27001
description: Desloque os passos para a amostra de projeto de serviços partilhados ISO 27001, incluindo detalhes do parâmetro do artefacto da planta.
ms.date: 01/13/2020
ms.topic: sample
ms.openlocfilehash: a9dabc99469321445006e449757a10fbc51aba87
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "75920681"
---
# <a name="deploy-the-iso-27001-shared-services-blueprint-sample"></a>Implementar a amostra de plantas iso 27001 serviços partilhados

Para implantar a amostra de projeto de serviços partilhados Azure Blueprints ISO 27001, devem ser tomadas as seguintes medidas:

> [!div class="checklist"]
> - Criar uma nova planta a partir da amostra
> - Marque a sua cópia da amostra como **Publicado**
> - Atribuir a sua cópia da planta a uma subscrição existente

Se não tiver uma subscrição Azure, crie uma [conta gratuita](https://azure.microsoft.com/free) antes de começar.

## <a name="create-blueprint-from-sample"></a>Criar a planta a partir da amostra

Em primeiro lugar, implemente a amostra de planta criando uma nova planta no seu ambiente usando a amostra como entrada.

1. Selecione **Todos os serviços** no painel esquerdo. Procure e selecione **Plantas**.

1. A partir da página **iniciar** à esquerda, selecione o botão **Criar** _uma planta_.

1. Encontre a amostra **iso 27001: Serviços Partilhados** em _outras amostras_ e selecione **Utilize esta amostra**.

1. Introduza os _Fundamentos_ da amostra de plantas:

   - **Nome**da planta : Forneça um nome para a sua cópia da amostra de plantas de serviços partilhados ISO 27001.
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
     - Prefixo de endereço de **sub-rede de serviços partilhados**: Forneça o valor de notação CIDR para a rede dos recursos implantados em conjunto.
     - **Localização dos serviços partilhados**: Determina para que localização os artefactos são implantados. Nem todos os serviços estão disponíveis em todos os locais. Os artefactos que implantam tais serviços fornecem uma opção parametrómetro para a localização para implantar esse artefacto para.
     - **Localização permitida (Política: Iniciativa blueprint para a ISO 27001)**: Valor que indica as localizações permitidas para grupos de recursos e recursos.
     - Log Analytics espaço de **trabalho para agentes VM (Política: Iniciativa Blueprint para ISO 27001)**: Especifica o ID de recurso de um espaço de trabalho. Este parâmetro utiliza `concat` uma função para construir o ID de recurso.

   - Parâmetros de artefacto

     Os parâmetros definidos nesta secção aplicam-se ao artefacto sob o qual é definido. Estes parâmetros são [parâmetros dinâmicos](../../concepts/parameters.md#dynamic-parameters) uma vez que são definidos durante a atribuição da planta. Para obter uma lista completa ou parâmetros de artefactos e suas descrições, consulte a [tabela de parâmetros do Artefacto](#artifact-parameters-table).

1. Uma vez introduzidos todos os parâmetros, **selecione Atribuir** na parte inferior da página. A atribuição da planta é criada e a implantação de artefactos começa. O destacamento demora cerca de uma hora. Para verificar o estado da implantação, abra a atribuição da planta.

> [!WARNING]
> O serviço Azure Blueprints e as amostras de plantas incorporadas estão **isentos de custos.** Os recursos azure são [avaliados pelo produto.](https://azure.microsoft.com/pricing/) Utilize a [calculadora](https://azure.microsoft.com/pricing/calculator/) de preços para estimar o custo dos recursos de funcionamento implantados por esta amostra de plantas.

## <a name="artifact-parameters-table"></a>Tabela de parâmetros de artefactos

A tabela seguinte fornece uma lista dos parâmetros do artefacto da planta:

|Nome do artefacto|Tipo de artefacto|Nome do parâmetro|Descrição|
|-|-|-|-|
|\[Pré-visualização\]: Implementar o agente de análise de registo para conjuntos de escala SM (VMSS)|Atribuição de política|Opcional: Lista de imagens VM que apoiaram o Sistema Linux OS para adicionar ao âmbito|(Opcional) O valor predefinido é _["nenhum"]_.|
|\[Pré-visualização\]: Implementar o Agente de Análise de Registo para VMs Linux|Atribuição de política|Opcional: Lista de imagens VM que apoiaram o Sistema Linux OS para adicionar ao âmbito|(Opcional) O valor predefinido é _["nenhum"]_.|
|\[Pré-visualização\]: Implementar o agente de análise de registo para conjuntos de escala seletivas VM Do Windows (VMSS)|Atribuição de política|Opcional: Lista de imagens VM que têm suportado o Windows OS para adicionar ao âmbito|(Opcional) O valor predefinido é _["nenhum"]_.|
|\[Pré-visualização\]: Implementar o agente de análise de registo para VMs do Windows|Atribuição de política|Opcional: Lista de imagens VM que têm suportado o Windows OS para adicionar ao âmbito|(Opcional) O valor predefinido é _["nenhum"]_.|
|Tipos de recursos permitidos|Atribuição de política|Tipos de recursos permitidos|Lista de tipos de recursos autorizados a serem implantados. Esta lista é composta por todos os tipos de recursos implantados em Serviços Partilhados.|
|SKUs de contas de armazenamento permitidos|Atribuição de política|SKUs de armazenamento permitido|Lista de registos de diagnóstico conta de armazenamento SKUs permitida. O valor predefinido é _["Standard_LRS"]_.|
|Máquina virtual permitida SKUs|Atribuição de política|A lista de SKUs da máquina virtual permitiu ser implantada. O valor predefinido é _["Standard_DS1_v2", "Standard_DS2_v2"]_.|
|Iniciativa blueprint para ISO 27001|Atribuição de política|Tipos de recursos para auditar registos de diagnóstico|Lista de tipos de recursos para auditar se a definição de registo de diagnóstico não estiver ativada. Valores aceitáveis podem ser encontrados em [registos de diagnóstico do Monitor Azure.](../../../../azure-monitor/platform/diagnostic-logs-schema.md#supported-log-categories-per-resource-type)|
|Grupo de recursos Log Analytics|Grupo de recursos|Nome|**Locked** - Concatenates the `-sharedsvsc-log-rg` Organization **name** with to make the resource group unique.|
|Grupo de recursos Log Analytics|Grupo de recursos|Localização|**Bloqueado** - Usa o parâmetro da planta.|
|Modelo de Análise de Log|Modelo do Resource Manager|Camada de serviços|Define o nível do espaço de trabalho Log Analytics. O valor predefinido é _PerNode_.|
|Modelo de Análise de Log|Modelo do Resource Manager|Retenção de registo em dias|Retenção de dados em dias. O valor predefinido é _de 365_.|
|Modelo de Análise de Log|Modelo do Resource Manager|Localização|Região utilizada para criar o espaço de trabalho Log Analytics. O valor padrão é _West US 2_.|
|Grupo de recursos de rede|Grupo de recursos|Nome|**Locked** - Concatenates the `-sharedsvcs-net-rg` Organization **name** with to make the resource group unique.|
|Grupo de recursos de rede|Grupo de recursos|Localização|**Bloqueado** - Usa o parâmetro da planta.|
|Modelo de Firewall Azure|Modelo do Resource Manager|IP privado de firewall Azure|Configura o IP privado da [firewall Azure.](../../../../firewall/overview.md) Este valor também é usado como tabela de rota padrão na subnet de serviços partilhados. Deve fazer parte da notação CIDR definida no prefixo de endereço de **sub-rede da Firewall Azure**. O valor predefinido é _de 10.0.4.4_.|
|Modelo de Firewall Azure|Modelo do Resource Manager|Retenção de registo em dias|Retenção de dados em dias. O valor predefinido é _de 365_.|
|Modelo de grupo de segurança de rede|Modelo do Resource Manager|Retenção de registo em dias|Retenção de dados em dias. O valor predefinido é _de 365_.|
|Modelo de tabela de rede virtual e rota|Modelo do Resource Manager|Prefixo de endereço de rede virtual|A notação CIDR para a rede virtual. O valor predefinido é _de 10.0.0.0/16_.|
|Modelo de tabela de rede virtual e rota|Modelo do Resource Manager|Ativar a proteção DDoS da rede virtual|Configura a proteção DDoS para a rede virtual. O valor predefinido é _verdadeiro._|
|Modelo de tabela de rede virtual e rota|Modelo do Resource Manager|Prefixo de endereço de sub-rede de serviços partilhados|A notação CIDR para a subnet de Serviços Partilhados. O valor predefinido é _de 10.0.0.0/24_.|
|Modelo de tabela de rede virtual e rota|Modelo do Resource Manager|Prefixo de endereço de sub-rede DMZ|A notação CIDR para a sub-rede DMZ. O valor predefinido é _de 10.0.1.0/24_.|
|Modelo de tabela de rede virtual e rota|Modelo do Resource Manager|Prefixo de endereço de sub-rede gateway de aplicação|A notação CIDR para a sub-rede de gateway de aplicação. O valor predefinido é _de 10.0.2.0/24_.|
|Modelo de tabela de rede virtual e rota|Modelo do Resource Manager|Prefixo de endereço de sub-rede de gateway virtual|A notação CIDR para a sub-rede de gateway virtual. O valor predefinido é _de 10.0.3.0/24_.|
|Modelo de tabela de rede virtual e rota|Modelo do Resource Manager|Prefixo de endereço de sub-rede Azure Firewall|A notação CIDR para a sub-rede [de firewall Azure.](../../../../firewall/overview.md) Deve incluir o parâmetro **IP privado da firewall Azure.**|
|Grupo de recursos key vault|Grupo de recursos|Nome|**Locked** - Concatenates the `-sharedsvcs-kv-rg` Organization **name** with to make the resource group unique.|
|Grupo de recursos key vault|Grupo de recursos|Localização|**Bloqueado** - Usa o parâmetro da planta.|
|Modelo de cofre de chave|Modelo do Resource Manager|Nome de utilizador de administrador jumpbox|Nome de utilizador para a caixa de salto. Deve corresponder ao mesmo valor de propriedade no **modelo Jumpbox**. O valor predefinido é _jb-admin-user_.|
|Modelo de cofre de chave|Modelo do Resource Manager|Chave ou senha de sh ssh de administração jumpbox|Chave ou senha para a conta na caixa de salto. Deve corresponder ao mesmo valor de propriedade no **modelo Jumpbox**. Sem valor predefinido e não pode ser deixado em branco.|
|Modelo de cofre de chave|Modelo do Resource Manager|Nome de utilizador de administração de domínio|O nome de utilizador usado para aceder ao Ative Directory VM e para se juntar a outros VMs a um domínio. Deve corresponder ao valor de propriedade do **utilizador administrativo de domínio** no modelo de Serviços de Domínio de **Diretório Ativo**. O valor predefinido é o _utilizador de domínio-administrador._|
|Modelo de cofre de chave|Modelo do Resource Manager|Senha de administração de domínio|Palavra-passe do utilizador do utilizador de domínio. Sem valor predefinido e não pode ser deixado em branco.|
|Modelo de cofre de chave|Modelo do Resource Manager|Id de objeto AAD|O identificador de objetos AAD da conta que requer acesso à instância do Cofre chave. Sem valor predefinido e não pode ser deixado em branco. Para localizar este valor a partir do portal Azure, procure e selecione "Utilizadores" nos _Serviços_. Utilize a caixa _nome_ para filtrar o nome da conta e selecione essa conta. Na página do perfil do _Utilizador,_ selecione o ícone "Clique para copiar" ao lado do ID do _objeto_.  |
|Modelo de cofre de chave|Modelo do Resource Manager|Retenção de registo em dias|Retenção de dados em dias. O valor predefinido é _de 365_.|
|Modelo de cofre de chave|Modelo do Resource Manager|Cofre chave SKU|Especifica o SKU do Cofre chave que é criado. O valor predefinido é _Premium._|
|Grupo de recursos jumpbox|Grupo de recursos|Nome|**Locked** - Concatenates the `-sharedsvcs-jb-rg` Organization **name** with to make the resource group unique.|
|Grupo de recursos jumpbox|Grupo de recursos|Localização|**Bloqueado** - Usa o parâmetro da planta.|
|Modelo de caixa de salto|Modelo do Resource Manager|Nome de utilizador de administrador jumpbox|O nome de utilizador usado para aceder a VMs de caixa de salto. Deve corresponder ao mesmo valor de propriedade no **modelo key vault**. O valor predefinido é _jb-admin-user_.|
|Modelo de caixa de salto|Modelo do Resource Manager|Palavra-passe de administração jumpbox (ID de recurso do cofre chave)|A identificação de recursos do cofre chave. Utilize "/subscrições/{subscriçãoId}/resourceGroups/{orgName}-sharedsvcs-kv-rg/providers/Microsoft.KeyVault/vaults/{orgName}-sharedsvcs-kv" `{subscriptionId}` e substitua-o pelo seu ID de subscrição e `{orgName}` pelo parâmetro de nome da **Organização.**|
|Modelo de caixa de salto|Modelo do Resource Manager|Palavra-passe de administrador da Jumpbox (Nome Secreto do Cofre chave)|Nome de utilizador do administrador da caixa de salto. Deve corresponder ao valor na propriedade do **modelo do Cofre chave** **Jumpbox nome de utilizador**.|
|Modelo de caixa de salto|Modelo do Resource Manager|Sistema Operativo Jumpbox|Determina o sistema operativo da caixa de salto VM. O valor predefinido é _o Windows_.|
|Grupo de recursos de serviços de domínio de diretório ativo|Grupo de recursos|Nome|**Locked** - Concatenates the `-sharedsvcs-adds-rg` Organization **name** with to make the resource group unique.|
|Grupo de recursos de serviços de domínio de diretório ativo|Grupo de recursos|Localização|**Bloqueado** - Usa o parâmetro da planta.|
|Modelo de serviços de domínio de diretório ativo|Modelo do Resource Manager|Nome de utilizador de administração de domínio|Nome de utilizador para a caixa de salto ADDS. Deve corresponder ao mesmo valor de propriedade no **modelo key vault**. O valor predefinido é _o utilizador de adiciona-administrador._|
|Modelo de serviços de domínio de diretório ativo|Modelo do Resource Manager|Senha de administração de domínio (ID de recurso do cofre chave)|A identificação de recursos do cofre chave. Utilize "/subscrições/{subscriçãoId}/resourceGroups/{orgName}-sharedsvcs-kv-rg/providers/Microsoft.KeyVault/vaults/{orgName}-sharedsvcs-kv" `{subscriptionId}` e substitua-o pelo seu ID de subscrição e `{orgName}` pelo parâmetro de nome da **Organização.**|
|Modelo de serviços de domínio de diretório ativo|Modelo do Resource Manager|Palavra-passe de administração de domínio (Nome secreto do cofre chave)|Nome de utilizador do administrador de domínio. Deve corresponder ao valor na propriedade do **modelo do cofre chave** Nome de utilizador de **domínio**.|
|Modelo de serviços de domínio de diretório ativo|Modelo do Resource Manager|Nome de domínio|Nome do Diretório Ativo criado pela amostra. O valor predefinido é _contoso.com_.|
|Modelo de serviços de domínio de diretório ativo|Modelo do Resource Manager|Utilizador administrativo de domínio|Nome de utilizador para a conta AD de administrador e para a junção de dispositivos ao domínio AD. Deve corresponder ao valor de propriedade do nome de utilizador da **administração AD** no **modelo key vault**. O valor predefinido é o _utilizador de domínio-administrador._|
|Modelo de serviços de domínio de diretório ativo|Modelo do Resource Manager|Senha de administração de domínio|Detete os detalhes do Cofre de Chaves para armazenar a palavra-passe. Sem valor predefinido e não pode ser deixado em branco.|

## <a name="next-steps"></a>Passos seguintes

Agora que reviu os passos para implementar a amostra de projeto iso 27001 Serviços Partilhados, visite os seguintes artigos para saber sobre a arquitetura e o mapeamento de controlo:

> [!div class="nextstepaction"]
> [ISO 27001 Projeto de Serviços Partilhados - Visão geral](./index.md)
> [ISO 27001 Projeto de Serviços Partilhados - Mapeamento](./control-mapping.md) de controlo

Artigos adicionais sobre esquemas e como os utilizar:

- Saiba mais sobre o [ciclo de vida do esquema](../../concepts/lifecycle.md).
- Compreenda como utilizar [parâmetros estáticos e dinâmicos](../../concepts/parameters.md).
- Aprenda a personalizar a [ordem de sequenciação do esquema](../../concepts/sequencing-order.md).
- Saiba como utilizar o [bloqueio de recursos de esquema](../../concepts/resource-locking.md).
- Saiba como [atualizar as atribuições existentes](../../how-to/update-existing-assignments.md).
