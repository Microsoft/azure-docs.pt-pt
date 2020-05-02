---
title: Implementar amostra de plano de benchmark de segurança Azure
description: Desloque os passos para a amostra de projeto de benchmark de segurança azure, incluindo detalhes do parâmetro do artefacto de plantas.
ms.date: 05/01/2020
ms.topic: sample
ms.openlocfilehash: 0e967e2217b90e036d809ce7baf500c3bc791c6f
ms.sourcegitcommit: 366e95d58d5311ca4b62e6d0b2b47549e06a0d6d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/01/2020
ms.locfileid: "82725967"
---
# <a name="deploy-the-azure-security-benchmark-blueprint-sample"></a>Implementar a amostra de projeto de benchmark de segurança Azure

Para implantar a amostra de projeto de referência de segurança Azure Blueprints Azure, devem ser tomadas as seguintes medidas:

> [!div class="checklist"]
> - Criar uma nova planta a partir da amostra
> - Marque a sua cópia da amostra como **Publicado**
> - Atribuir a sua cópia da planta a uma subscrição existente

Se não tiver uma subscrição Azure, crie uma [conta gratuita](https://azure.microsoft.com/free) antes de começar.

## <a name="create-blueprint-from-sample"></a>Criar a planta a partir da amostra

Em primeiro lugar, implemente a amostra de planta criando uma nova planta no seu ambiente usando a amostra como entrada.

1. Selecione **Todos os serviços** no painel esquerdo. Procure e selecione **Plantas**.

1. A partir da página **iniciar** à esquerda, selecione o botão **Criar** _uma planta_.

1. Encontre a amostra de referência de **segurança azure** em _Outras Amostras_ e selecione clique no nome para selecionar esta amostra.

1. Introduza os _Fundamentos_ da amostra de plantas:

   - **Nome**da planta : Forneça um nome para a sua cópia da amostra de projeto de benchmark de segurança azure.
   - **Localização da definição**: Utilize a elipse e selecione o grupo de gestão para guardar a sua cópia da amostra para.

1. Selecione o separador _Artefactos_ na parte superior da página ou **Seguinte: Artefactos** na parte inferior da página.

1. Reveja a lista de artefactos que compõem a amostra da planta. Muitos dos artefactos têm parâmetros que definiremos mais tarde. Selecione **Guardar Rascunho** quando terminar de rever a amostra de plantas.

## <a name="publish-the-sample-copy"></a>Publicar a cópia da amostra

A sua cópia da amostra de plantas foi agora criada no seu ambiente. É criado em modo **Draft** e deve ser **publicado** antes de ser atribuído e implantado. A cópia da amostra de plantas pode ser personalizada para o seu ambiente e necessidades, mas essa modificação pode afastá-la do alinhamento com as recomendações do Azure Security Benchmark.

1. Selecione **Todos os serviços** no painel esquerdo. Procure e selecione **Plantas**.

1. Selecione a página de **definições** de Blueprint à esquerda. Utilize os filtros para encontrar a sua cópia da amostra de plantas e, em seguida, selecione-a.

1. Selecione **Publicar a planta** no topo da página. Na nova página à direita, forneça uma **Versão** para a sua cópia da amostra de plantas. Esta propriedade é útil para se fizer uma modificação mais tarde. Forneça **notas de alteração** como "Primeira versão publicada a partir da amostra de projeto de benchmark de segurança do Azure." Em seguida, **selecione Publicar** na parte inferior da página.

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

   - Parâmetros de artefacto

     Os parâmetros definidos nesta secção aplicam-se ao artefacto sob o qual é definido. Estes parâmetros são [parâmetros dinâmicos](../../concepts/parameters.md#dynamic-parameters) uma vez que são definidos durante a atribuição da planta. Para obter uma lista completa ou parâmetros de artefactos e suas descrições, consulte a [tabela de parâmetros do Artefacto](#artifact-parameters-table).

1. Uma vez introduzidos todos os parâmetros, **selecione Atribuir** na parte inferior da página. A atribuição da planta é criada e a implantação de artefactos começa. O destacamento demora cerca de uma hora. Para verificar o estado da implantação, abra a atribuição da planta.

> [!WARNING]
> O serviço Azure Blueprints e as amostras de plantas incorporadas estão **isentos de custos.** Os recursos azure são [avaliados pelo produto.](https://azure.microsoft.com/pricing/) Utilize a [calculadora](https://azure.microsoft.com/pricing/calculator/) de preços para estimar o custo dos recursos de funcionamento implantados por esta amostra de plantas.

## <a name="artifact-parameters-table"></a>Tabela de parâmetros de artefactos

A tabela seguinte fornece uma lista dos parâmetros do artefacto da planta:

|Nome do artefacto|Tipo de artefacto|Nome do parâmetro|Descrição|
|-|-|-|-|
|Audite recomendações de benchmark de segurança azure e implemente extensões vm específicas|Atribuição de política|
Lista de utilizadores excluídos do grupo de Administradores VM do Windows|Uma lista separada do ponto-e-vírgula dos membros que deve ser excluída no grupo local dos administradores. Ex: Administrador; myUser1; myUser2|
|Audite recomendações de benchmark de segurança azure e implemente extensões vm específicas|Atribuição de política|Lista de utilizadores que devem ser incluídos no grupo de Administradores VM do Windows|Uma lista separada do ponto-e-vírgula dos membros que deve ser incluída no grupo local administradores. Ex: Administrador; myUser1; myUser2|
|Audite recomendações de benchmark de segurança azure e implemente extensões vm específicas|Atribuição de política|Lista de utilizadores que o grupo de administradores do Windows VM *só* deve incluir|Uma lista semi-separada de todos os membros esperados do grupo local administradores. Ex: Administrador; myUser1; myUser2|
|Audite recomendações de benchmark de segurança azure e implemente extensões vm específicas|Atribuição de política|Lista de regiões onde o Observador da Rede deve ser ativado|Para ver uma lista completa de regiões use Get-AzLocation|
|Audite recomendações de benchmark de segurança azure e implemente extensões vm específicas|Atribuição de política|Rede virtual onde os VMs devem ser conectados|Exemplo: /subscrições/YourSubscriptionId/resourceGroups/YourResourceGroupName/providers/Microsoft.Network/virtualNetworks/Name|
|Audite recomendações de benchmark de segurança azure e implemente extensões vm específicas|Atribuição de política|Gateway da rede que as redes virtuais devem usar|Exemplo: /subscrições/YourSubscriptionId/resourceGroups/YourResourceGroup/providers/Microsoft.Network/virtualNetworkGateways/Name|
|Audite recomendações de benchmark de segurança azure e implemente extensões vm específicas|Atribuição de política|Lista de IDs do espaço de trabalho onde os agentes do Log Analytics devem ligar-se|Uma lista semi-separada das iDs do espaço de trabalho a que o agente Log Analytics deve ser ligado|
|Audite recomendações de benchmark de segurança azure e implemente extensões vm específicas|Atribuição de política|Lista de tipos de recursos que devem ter registos de diagnóstico ativados|Definir definição de diagnóstico de auditoria para tipos de recursos selecionados|
|Audite recomendações de benchmark de segurança azure e implemente extensões vm específicas|Atribuição de política|Versão PHP mais recente|Versão PHP suportada mais recente para Serviços de Aplicações|
|Audite recomendações de benchmark de segurança azure e implemente extensões vm específicas|Atribuição de política|Versão java mais recente|Versão Java suportada mais recente para serviços de aplicações|
|Audite recomendações de benchmark de segurança azure e implemente extensões vm específicas|Atribuição de política|Versão mais recente do Windows Python|Versão Python mais recente suportada para serviços de aplicações|
|Audite recomendações de benchmark de segurança azure e implemente extensões vm específicas|Atribuição de política|Versão mais recente do Linux Python|Versão Python mais recente suportada para serviços de aplicações|

## <a name="next-steps"></a>Passos seguintes

Agora que reviu os passos para implementar a amostra de projeto de benchmark de segurança Azure, visite os seguintes artigos para saber sobre a planta e o mapeamento de controlo:

> [!div class="nextstepaction"]
> [Projeto](./index.md)
> de benchmark de segurança azure - visão geral azure plano de[referência de segurança - Mapeamento de controlo](./control-mapping.md)

Artigos adicionais sobre esquemas e como utilizá-los:

- Saiba mais sobre o [ciclo de vida do esquema](../../concepts/lifecycle.md).
- Compreenda como utilizar [parâmetros estáticos e dinâmicos](../../concepts/parameters.md).
- Aprenda a personalizar a [ordem de sequenciação do esquema](../../concepts/sequencing-order.md).
- Saiba como utilizar o [bloqueio de recursos de esquema](../../concepts/resource-locking.md).
- Saiba como [atualizar as atribuições existentes](../../how-to/update-existing-assignments.md).
