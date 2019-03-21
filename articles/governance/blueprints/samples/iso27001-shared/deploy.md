---
title: Exemplo - ISO 27001 Shared Services esquema - implementar passos
description: Implemente os passos para o exemplo de plano gráfico de ISO 27001 Shared Services.
services: blueprints
author: DCtheGeek
ms.author: dacoulte
ms.date: 03/14/2019
ms.topic: conceptual
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: 7fea9b0d00d92c63bb4c8bbdadada9e8eb6a05fe
ms.sourcegitcommit: dec7947393fc25c7a8247a35e562362e3600552f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58202508"
---
# <a name="deploy-the-azure-blueprints-iso-27001-shared-services-blueprint-sample"></a>Implementar o exemplo de plano gráfico de planos gráficos ISO 27001 partilhado dos serviços do Azure

Para implementar o exemplo de plano gráfico de planos gráficos ISO 27001 partilhado dos serviços do Azure, é necessário os seguintes passos:

> [!div class="checklist"]
> - Criar um novo plano de gráfico do exemplo
> - Marcar a sua cópia do exemplo de como **publicado**
> - Atribuir a sua cópia do esquema a uma subscrição existente

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free) antes de começar.

## <a name="create-blueprint-from-sample"></a>Criar o plano gráfico de exemplo

Em primeiro lugar, implementa o exemplo de esquema ao criar uma novo esquema no seu ambiente com o exemplo de como um starter.

1. Selecione **todos os serviços** e procure e selecione **política** no painel esquerdo. Sobre o **diretiva** página, selecione **esquemas**.

1. Do **introdução** página à esquerda, selecione a **Create** botão sob _criar um plano gráfico_.

1. Encontrar o **ISO 27001: Serviços compartilhados** exemplo de esquema sob _outros exemplos_ e selecione **utilizar este exemplo**.

1. Introduza o _Noções básicas_ do exemplo de esquema:

   - **Nome do blueprint**: Forneça um nome para a sua cópia do exemplo de plano gráfico ISO 27001 Shared Services.
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
     - **Prefixo de endereço de sub-rede de serviços compartilhados**: Forneça o valor de notação CIDR de rede os recursos implementados em conjunto.
     - **Serviços de localização partilhada**: Determina o local em que os artefactos são implementados. Nem todos os serviços estão disponíveis em todas as localizações. Artefatos de implementação de tais serviços fornecem uma opção de parâmetro para a localização implementar esse artefacto a.
     - **Localização permitida (política: Esquema iniciativa da ISO 27001)**: Valor que indica as localizações permitidas para grupos de recursos e recursos.
     - **Área de trabalho de análise de registo para os agentes VM (política: Esquema iniciativa da ISO 27001)**: Especifica o ID de recurso de uma área de trabalho. Este parâmetro utiliza um `concat` função para construir o ID de recurso.

   - Parâmetros de artefacto

     Os parâmetros definidos nesta secção aplicam-se para o artefacto sob a qual está definido. Esses parâmetros são [parâmetros dinâmicos](../../concepts/parameters.md#dynamic-parameters) , uma vez que estão definidos durante a atribuição do esquema. Para ver uma lista completa ou parâmetros de artefacto e suas descrições, veja [tabela de parâmetros de artefacto](#artifact-parameters-table).

1. Depois de tem sido introduzidos todos os parâmetros, selecione **atribuir** na parte inferior da página. A atribuição do esquema é criada e começa a implementação de artefactos. Implementação demora aproximadamente uma hora. Para verificar o estado da implementação, abra a atribuição do esquema.

> [!WARNING]
> O serviço de esquemas do Azure e os exemplos de esquema incorporados estão **gratuita**. Recursos do Azure estão [o preço por produto](https://azure.microsoft.com/en-us/pricing/). Utilize o [Calculadora de preços](https://azure.microsoft.com/pricing/calculator/) para calcular o custo da execução de recursos implementados por este exemplo de esquema.

## <a name="artifact-parameters-table"></a>Tabela de parâmetros de artefacto

A tabela seguinte fornece uma lista do plano gráfico de parâmetros de artefacto:

|Nome do artefacto|Tipo de artefacto|Nome do parâmetro|Descrição|
|-|-|-|-|
|[Preview]: Implementar o agente de análise de registo para conjuntos de dimensionamento de VM do Linux (VMSS)|Atribuição de política|Opcional: Lista de imagens VM que tenha suporte SO Linux para adicionar ao âmbito|(Opcional) Valor predefinido é _["none"]_.|
|[Preview]: Implementar o agente de análise de registo para VMs do Linux|Atribuição de política|Opcional: Lista de imagens VM que tenha suporte SO Linux para adicionar ao âmbito|(Opcional) Valor predefinido é _["none"]_.|
|[Preview]: Implementar o agente de análise de registo para conjuntos de dimensionamento VM do Windows (VMSS)|Atribuição de política|Opcional: Lista de imagens VM que têm suporte SO do Windows para adicionar ao âmbito|(Opcional) Valor predefinido é _["none"]_.|
|[Preview]: Implementar o agente de análise de registo para VMs do Windows|Atribuição de política|Opcional: Lista de imagens VM que têm suporte SO do Windows para adicionar ao âmbito|(Opcional) Valor predefinido é _["none"]_.|
|Tipos de recursos permitidos|Atribuição de política|Tipos de recursos permitidos|Lista de tipos de recursos permitida para ser implementado. Esta lista é composta por todos os tipos de recurso implementados nos serviços partilhados.|
|SKUs de contas de armazenamento permitidos|Atribuição de política|SKUs de armazenamento permitido|Lista de diagnóstico de registos permitidos de SKUs de conta de armazenamento. Valor predefinido é _["Standard_LRS"]_.|
|SKUs de máquinas virtuais permitidas|Atribuição de política|Lista de SKUs de máquina virtual pode ser implementado. Default value is _["Standard_DS1_v2", "Standard_DS2_v2"]_.|
|Iniciativa do esquema para ISO 27001|Atribuição de política|Tipos de recursos para os registos de diagnóstico de auditoria|Lista de tipos de recursos para auditar se a definição de registo de diagnóstico não está ativada. Os valores aceitáveis podem ser encontrados em [esquemas de registos de diagnóstico do Azure Monitor](../../../../azure-monitor/platform/diagnostic-logs-schema.md#supported-log-categories-per-resource-type).|
|Grupo de recursos do log Analytics|Grupo de recursos|Name|**Bloqueado** -concatena a **nome da organização** com `-sharedsvsc-log-rg` para tornar o grupo de recursos exclusivo.|
|Grupo de recursos do log Analytics|Grupo de recursos|Localização|**Bloqueado** -utiliza o parâmetro de esquema.|
|Modelo do Log Analytics|Modelo do Resource Manager|Camada de serviços|Define a camada da área de trabalho do Log Analytics. Valor predefinido é _PerNode_.|
|Modelo do Log Analytics|Modelo do Resource Manager|Retenção do registo em dias|Retenção de dados em dias. Valor predefinido é _365_.|
|Modelo do Log Analytics|Modelo do Resource Manager|Localização|Região utilizada para criar a área de trabalho do Log Analytics. Valor predefinido é _E.U.A. oeste 2_.|
|Grupo de recursos de rede|Grupo de recursos|Name|**Bloqueado** -concatena a **nome da organização** com `-sharedsvcs-net-rg` para tornar o grupo de recursos exclusivo.|
|Grupo de recursos de rede|Grupo de recursos|Localização|**Bloqueado** -utiliza o parâmetro de esquema.|
|Modelo do Azure Firewall|Modelo do Resource Manager|IP privado do Azure Firewall|Configura o IP privado dos [firewall do Azure](../../../../firewall/overview.md). Este valor também é utilizado como tabela de rotas predefinidas na sub-rede de serviços compartilhados. Deve fazer parte da notação CIDR definida no **prefixo de endereço de sub-rede de Firewall do Azure**. Valor predefinido é _10.0.4.4_.|
|Modelo do Azure Firewall|Modelo do Resource Manager|Retenção do registo em dias|Retenção de dados em dias. Valor predefinido é _365_.|
|Modelo do Grupo de Segurança de Rede|Modelo do Resource Manager|Retenção do registo em dias|Retenção de dados em dias. Valor predefinido é _365_.|
|Modelo da Rede Virtual e Tabela de Rotas|Modelo do Resource Manager|Prefixo do endereço da Rede Virtual|A notação CIDR para a rede virtual. Valor predefinido é _10.0.0.0/16_.|
|Modelo da Rede Virtual e Tabela de Rotas|Modelo do Resource Manager|Ativar proteção contra DDoS da Rede Virtual|Configura a proteção contra DDoS para a rede virtual. Valor predefinido é _true_.|
|Modelo da Rede Virtual e Tabela de Rotas|Modelo do Resource Manager|Partilhado o prefixo de endereço de sub-rede de serviços|A notação CIDR para a sub-rede de serviços compartilhados. Valor predefinido é _10.0.0.0/24_.|
|Modelo da Rede Virtual e Tabela de Rotas|Modelo do Resource Manager|Prefixo de endereço da sub-rede de perímetro|A notação CIDR para a sub-rede de rede de Perímetro. Valor predefinido é _10.0.1.0/24_.|
|Modelo da Rede Virtual e Tabela de Rotas|Modelo do Resource Manager|Prefixo do endereço de sub-rede do Gateway de Aplicação|A notação CIDR para a sub-rede de gateway de aplicação. Valor predefinido é _10.0.2.0/24_.|
|Modelo da Rede Virtual e Tabela de Rotas|Modelo do Resource Manager|Prefixo do endereço da sub-rede do Gateway da Rede Virtual|A notação CIDR para a sub-rede de gateway de rede virtual. Valor predefinido é _10.0.3.0/24_.|
|Modelo da Rede Virtual e Tabela de Rotas|Modelo do Resource Manager|Prefixo do endereço da sub-rede do Azure Firewall|A notação CIDR para a [firewall do Azure](../../../../firewall/overview.md) sub-rede. Deve incluir o **IP privado da firewall do Azure** parâmetro.|
|Grupo de recursos do Cofre de chaves|Grupo de recursos|Name|**Bloqueado** -concatena a **nome da organização** com `-sharedsvcs-kv-rg` para tornar o grupo de recursos exclusivo.|
|Grupo de recursos do Cofre de chaves|Grupo de recursos|Localização|**Bloqueado** -utiliza o parâmetro de esquema.|
|Modelo do Key Vault|Modelo do Resource Manager|Nome de utilizador de administrador do Jumpbox|Nome de utilizador para a jumpbox. Tem de corresponder ao valor da propriedade mesmo nas **Jumpbox modelo**. Valor predefinido é _utilizador de admin jb_.|
|Modelo do Key Vault|Modelo do Resource Manager|Chave ou palavra-passe ssh do administrador do Jumpbox|Chave ou palavra-passe para a conta na jumpbox. Tem de corresponder ao valor da propriedade mesmo nas **Jumpbox modelo**. Sem predefinição de valor e não pode ser deixado em branco.|
|Modelo do Key Vault|Modelo do Resource Manager|Nome de utilizador do administrador de domínio|O nome de utilizador utilizado para aceder à VM do Active Directory e associar a outras VMs a um domínio. Tem de corresponder ao **utilizador de administrador de domínio** valor da propriedade nas **modelo de serviços de domínio do Active Directory**. Valor predefinido é _utilizador de administrador de domínio_.|
|Modelo do Key Vault|Modelo do Resource Manager|Palavra-passe do administrador de domínio|Palavra-passe de utilizador de administrador a domínio. Sem predefinição de valor e não pode ser deixado em branco.|
|Modelo do Key Vault|Modelo do Resource Manager|ID do objeto do AAD|O identificador de objeto do AAD da conta de que necessita de acesso para a instância do Key Vault. Sem predefinição de valor e não pode ser deixado em branco. Para localizar este valor a partir do portal do Azure, procure e selecione "Utilizadores" em _serviços_. Utilize o _nome_ caixa para filtrar para o nome da conta e selecionar essa conta. No _perfil de utilizador_ página, selecione o ícone de "Clique para copiar" junto a _ID de objeto_.  |
|Modelo do Key Vault|Modelo do Resource Manager|Retenção do registo em dias|Retenção de dados em dias. Valor predefinido é _365_.|
|Modelo do Key Vault|Modelo do Resource Manager|SKU do Key Vault|Especifica o SKU de Cofre de chaves do que é criado. Valor predefinido é _Premium_.|
|Grupo de recursos da Jumpbox|Grupo de recursos|Name|**Bloqueado** -concatena a **nome da organização** com `-sharedsvcs-jb-rg` para tornar o grupo de recursos exclusivo.|
|Grupo de recursos da Jumpbox|Grupo de recursos|Localização|**Bloqueado** -utiliza o parâmetro de esquema.|
|Modelo do Jumpbox|Modelo do Resource Manager|Nome de utilizador de administrador do Jumpbox|O nome de utilizador utilizado para aceder a jumpbox VMs. Tem de corresponder ao valor da propriedade mesmo nas **modelo de Key Vault**. Valor predefinido é _utilizador de admin jb_.|
|Modelo do Jumpbox|Modelo do Resource Manager|Jumpbox palavra-passe de administrador (ID de recurso do Key Vault)|O ID de recurso do Cofre de chaves. Utilize "/ subscriptions/{subscriptionId}/resourceGroups/{orgName}-sharedsvcs-kv-rg/providers/Microsoft.KeyVault/vaults/{orgName}-sharedsvcs-kv" e substitua `{subscriptionId}` pelo ID da subscrição e `{orgName}` com o  **Nome da organização** esquema de parâmetro.|
|Modelo do Jumpbox|Modelo do Resource Manager|Jumpbox palavra-passe de administrador (nome de segredo do Key Vault)|Nome de utilizador do administrador jumpbox. Tem de corresponder ao valor na **modelo de Key Vault** propriedade **nome de utilizador de administrador de Jumpbox**.|
|Modelo do Jumpbox|Modelo do Resource Manager|Sistema Operativo do Jumpbox|Determina o sistema operativo da VM da jumpbox. Valor predefinido é _Windows_.|
|Grupo de recursos do Active Directory Domain Services|Grupo de recursos|Name|**Bloqueado** -concatena a **nome da organização** com `-sharedsvcs-adds-rg` para tornar o grupo de recursos exclusivo.|
|Grupo de recursos do Active Directory Domain Services|Grupo de recursos|Localização|**Bloqueado** -utiliza o parâmetro de esquema.|
|Modelo dos Serviços de Domínio do Active Directory|Modelo do Resource Manager|Nome de utilizador do administrador de domínio|Nome de utilizador para a jumpbox do ADDS. Tem de corresponder ao valor da propriedade mesmo nas **modelo de Key Vault**. Valor predefinido é _adiciona-admin-user_.|
|Modelo dos Serviços de Domínio do Active Directory|Modelo do Resource Manager|Senha de administrador de domínio (ID de recurso do Key Vault)|O ID de recurso do Cofre de chaves. Utilize "/ subscriptions/{subscriptionId}/resourceGroups/{orgName}-sharedsvcs-kv-rg/providers/Microsoft.KeyVault/vaults/{orgName}-sharedsvcs-kv" e substitua `{subscriptionId}` pelo ID da subscrição e `{orgName}` com o  **Nome da organização** esquema de parâmetro.|
|Modelo dos Serviços de Domínio do Active Directory|Modelo do Resource Manager|Senha de administrador de domínio (nome de segredo do Key Vault)|Nome de utilizador do administrador de domínio. Tem de corresponder ao valor na **modelo de Key Vault** propriedade **nome de utilizador de administrador de domínio**.|
|Modelo dos Serviços de Domínio do Active Directory|Modelo do Resource Manager|Nome de domínio|Nome do Active Directory criadas pelo exemplo. Valor predefinido é _contoso.com_.|
|Modelo dos Serviços de Domínio do Active Directory|Modelo do Resource Manager|Utilizador de administrador de domínio|Nome de utilizador para a conta de administrador AD e para associar dispositivos ao domínio do AD. Tem de corresponder ao **nome de utilizador de administrador de AD** valor da propriedade nas **modelo do Key Vault**. Valor predefinido é _utilizador de administrador de domínio_.|
|Modelo dos Serviços de Domínio do Active Directory|Modelo do Resource Manager|Palavra-passe do administrador de domínio|Defina os detalhes do Cofre de chaves para armazenar a palavra-passe. Sem predefinição de valor e não pode ser deixado em branco.|

## <a name="next-steps"></a>Passos Seguintes

Agora que reviu os passos para implementar o exemplo de esquema de serviços de compartilhados do ISO 27001, visite os seguintes artigos para saber mais sobre a arquitetura e o mapeamento de controle:

> [!div class="nextstepaction"]
> [Esquema de serviços de compartilhados do ISO 27001 - descrição geral](./index.md)
> [esquema ISO 27001 Shared Services - mapeamento de controlo](./control-mapping.md)

Artigos adicionais sobre esquemas e como utilizá-los:

- Saiba mais sobre o [ciclo de vida de um esquema](../../concepts/lifecycle.md).
- Compreenda como utilizar [parâmetros estáticos e dinâmicos](../../concepts/parameters.md).
- Aprenda a personalizar a [ordem de sequenciação do esquema](../../concepts/sequencing-order.md).
- Saiba como utilizar o [bloqueio de recursos de esquema](../../concepts/resource-locking.md).
- Saiba como [atualizar as atribuições existentes](../../how-to/update-existing-assignments.md).