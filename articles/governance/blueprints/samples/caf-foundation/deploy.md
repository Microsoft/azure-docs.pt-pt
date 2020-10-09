---
title: Implementar amostra de projeto da Fundação CAF
description: Implementar etapas para a amostra de planta da Fundação CAF, incluindo detalhes do parâmetro do artefacto da planta.
ms.date: 05/06/2020
ms.topic: sample
ms.openlocfilehash: a2d3090e5e9fe265683c077d7056ebf2b108352d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "82871634"
---
# <a name="deploy-the-microsoft-cloud-adoption-framework-for-azure-foundation-blueprint-sample"></a>Implementar o Quadro de Adoção da Microsoft Cloud para a amostra de projeto da Fundação Azure

Para implementar a amostra de projeto da Microsoft Cloud Adopt Framework for Azure (CAF), devem ser tomadas as seguintes medidas:

> [!div class="checklist"]
> - Criar um esquema novo a partir do exemplo
> - Marcar a cópia do exemplo como **Publicada**
> - Atribuir a cópia do esquema a uma subscrição já existente

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free) antes de começar.

## <a name="create-blueprint-from-sample"></a>Criar planta a partir de amostra

Primeiro, crie um esquema novo no ambiente utilizando o exemplo como ponto de partida, para implementar o esquema de exemplo.

1. Selecione **Todos os serviços** no painel esquerdo. Procure e selecione **Esquemas**.

1. Na página **Começar** à esquerda, selecione o botão **Criar** em _Criar um esquema_.

1. Encontre a amostra de planta da **Fundação CAF** em _Outras Amostras_ e selecione **Utilize esta amostra.**

1. Introduza as _Informações Básicas_ do esquema de exemplo:

   - **Nome da planta**: Forneça um nome para a sua cópia da amostra de planta da Fundação CAF.
   - **Localização de definição**: Utilize a elipse e selecione o grupo de gestão para guardar a sua cópia da amostra para.

1. Selecione o separador _Artefactos_, na parte superior da página, ou **Seguinte: Artefactos**, na parte inferior.

1. Reveja a lista de artefactos que compõem o esquema de exemplo. Muitos dos artefactos têm parâmetros que vamos definir mais tarde. Quando terminar de rever o esquema de exemplo, selecione **Guardar Rascunho**.

## <a name="publish-the-sample-copy"></a>Publicar a cópia do exemplo

A cópia do esquema de exemplo está agora criada no seu ambiente. Está criada no modo **Rascunho** e tem de ser **Publicada** antes de poder ser atribuída e implementada. A cópia da amostra de plantas pode ser personalizada para o seu ambiente e necessidades, mas essa modificação pode afastá-la do projeto da Fundação CAF.

1. Selecione **Todos os serviços** no painel esquerdo. Procure e selecione **Esquemas**.

1. Selecione a página **Definições de esquema** à esquerda. Utilize os filtros para localizar a cópia do esquema de exemplo e selecione-a.

1. Selecione **Publicar esquema**, na parte superior da página. Na página nova à direita, indique uma **Versão** para a cópia do esquema de exemplo. Esta propriedade é útil se fizer modificações mais tarde. Fornecer **notas de alteração** como "Primeira versão publicada a partir da amostra de planta da Fundação CAF." Em seguida, selecione **Publicar**, na parte inferior da página.

## <a name="assign-the-sample-copy"></a>Atribuir a cópia de exemplo

Uma vez publicada com **sucesso**a cópia da amostra de plantas, pode ser atribuída a uma subscrição dentro do grupo de gestão a que foi guardada. É neste passo que são fornecidos os parâmetros que fazem com que cada implementação da cópia do esquema de exemplo seja única.

1. Selecione **Todos os serviços** no painel esquerdo. Procure e selecione **Esquemas**.

1. Selecione a página **Definições de esquema** à esquerda. Utilize os filtros para localizar a cópia do esquema de exemplo e selecione-a.

1. Selecione **Atribuir esquema**, na parte superior da página de definição do esquema.

1. Indique os valores dos parâmetros para a atribuição do esquema:

   - Noções básicas
       - **Subscrições**: Selecione uma ou mais das subscrições que estão no grupo de gestão a que guardou a sua cópia da amostra de planta. Se selecionar mais de uma subscrição, é criada uma atribuição para cada uma mediante a utilização dos parâmetros introduzidos.
     - **Nome da atribuição**: O nome é pré-povoado para si com base no nome da planta.
       Mude-o se necessário ou deixe-o como está.
     - **Localização**: Selecione uma região para a identidade gerida a criar.
     - O Azure Blueprint utiliza esta identidade gerida para implementar todos os artefactos no esquema atribuído.
       Para saber mais, consulte [identidades geridas para recursos Azure.](../../../../active-directory/managed-identities-azure-resources/overview.md)
     - **Versão de definição de planta**: Escolha uma versão **publicada** da sua cópia da amostra de planta.

   - Atribuição de bloqueio

     Selecione a definição de bloqueio do esquema no seu ambiente. Para obter mais informações, veja [bloqueio de recurso em esquemas](../../concepts/resource-locking.md).

   - Identidade Gerida

     Escolha a opção de identidade gerida _atribuída pelo sistema_ predefinida ou a opção de identidade _atribuída pelo utilizador_.

   - Parâmetros de esquema

     Os parâmetros definidos nesta secção são utilizados por muitos dos artefactos na definição do esquema, para proporcionar consistência.

     - **Organização**: Insira o nome da sua organização, como Contoso, deve ser único.
     - **Região de Azure**: Selecione a Região Azure para implantação.
     - **Locais permitidos**: Em que regiões de Azure permitirá a construção de recursos?
     
   - Parâmetros dos artefactos

     Os parâmetros definidos nesta secção aplicam-se ao artefacto no qual são definidos. Estes parâmetros são [parâmetros dinâmicos,](../../concepts/parameters.md#dynamic-parameters) uma vez que são definidos durante a atribuição da planta. Para obter uma lista completa ou parâmetros de artefactos e suas descrições, consulte [a tabela de parâmetros do Artefacto](#artifact-parameters-table).

1. Depois de introduzidos todos os parâmetros, selecione **Atribuir**, na parte inferior da página. A atribuição do esquema é criada e a implementação do artefacto inicia-se. A implantação leva cerca de uma hora. Para verificar o estado, abra a atribuição do esquema.

> [!WARNING]
> O serviço Azure Blueprints e os esquemas de exemplo incorporados são **gratuitos**. Os preços dos recursos do Azure são os [preços por produto](https://azure.microsoft.com/pricing/). Utilize a [calculadora de preços](https://azure.microsoft.com/pricing/calculator/) para prever o custo da execução de recursos implementados por este esquema de exemplo.

## <a name="artifact-parameters-table"></a>Tabela de parâmetros dos artefactos

A tabela seguinte mostra uma lista dos parâmetros dos artefactos de esquema:

|Nome do artefacto|Tipo de artefacto|Nome do parâmetro|Descrição|
|-|-|-|-|
|SKUs de contas de armazenamento permitidos|Atribuição de políticas|Policy_Allowed-StorageAccount-SKUs|SKU utilizado nas contas de armazenamento de registo de diagnóstico|
|SKUs de máquina virtual permitida|Atribuição de políticas|Policy_Allowed-VM-SKUs|SKUs de máquina virtual permitida|
|Append CostCenter TAG para grupos de recursos|Atribuição de políticas|Policy_CostCenter_Tag|Append CostCenter TAG e o seu valor do Grupo de Recursos|
|Tipos de Recursos que não pretende permitir no seu ambiente|Atribuição de políticas|Política _Allowed-Tipos de Recursos|Quais recursos Azure que você quer permitir no seu ambiente|
|Implementar o Key Vault|Modelo do Resource Manager|KV-AccessPolicy|**Bloqueado** - Grupo AD AZure ou Utilizador <Object ID> para conceder permissões no Cofre de Chaves|
|Implementar o Log Analytics|Modelo do Resource Manager|LogAnalytics_DataRetention|**Bloqueado** - Número de dias os dados serão retidos no Log Analytics|
|Implementar o Log Analytics|Modelo do Resource Manager|LogAnalytics_Location|**Bloqueado** - região utilizada ao estabelecer a área de trabalho|

## <a name="next-steps"></a>Passos seguintes

Agora que reviu os passos para implementar a amostra de projeto da Fundação CAF, visite o seguinte artigo para saber sobre a arquitetura:

> [!div class="nextstepaction"]
> [Projeto da Fundação CAF - Visão geral](./index.md)

Artigos adicionais sobre esquemas e como os utilizar:

- Saiba mais sobre o [ciclo de vida do esquema](../../concepts/lifecycle.md).
- Compreenda como utilizar [parâmetros estáticos e dinâmicos](../../concepts/parameters.md).
- Aprenda a personalizar a [ordem de sequenciação do esquema](../../concepts/sequencing-order.md).
- Saiba como utilizar o [bloqueio de recursos de esquema](../../concepts/resource-locking.md).
- Saiba como [atualizar as atribuições existentes](../../how-to/update-existing-assignments.md).
