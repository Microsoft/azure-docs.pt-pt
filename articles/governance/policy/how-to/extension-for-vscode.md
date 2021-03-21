---
title: Extensão da política Azure para Código do Estúdio Visual
description: Saiba como usar a extensão da Política Azure para o Código do Estúdio Visual para procurar pseudónimos do Gestor de Recursos Azure.
ms.date: 01/11/2021
ms.topic: how-to
ms.openlocfilehash: 4c4ba0eeb0506179ff92ead0ee86f048600d157e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98107944"
---
# <a name="use-azure-policy-extension-for-visual-studio-code"></a>Use a extensão da política Azure para código de estúdio visual

> Aplica-se à versão de extensão da Política Azure **0.1.1** e mais recente

Saiba como usar a extensão da Política Azure para o Código do Estúdio Visual para procurar [pseudónimos,](../concepts/definition-structure.md#aliases)rever recursos e políticas, exportar objetos e avaliar definições de políticas. Primeiro, descreveremos como instalar a extensão da Política Azure no Código do Estúdio Visual. Depois vamos ver como procurar pseudónimos.

A extensão da Política Azure para Código do Estúdio Visual pode ser instalada no Windows.

## <a name="prerequisites"></a>Pré-requisitos

São necessários os seguintes itens para completar as etapas deste artigo:

- Uma subscrição do Azure. Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.
- [Visual Studio Code](https://code.visualstudio.com).

## <a name="install-and-configure-the-azure-policy-extension"></a>Instale e configuure a extensão da Política Azure

Depois de cumprir os pré-requisitos, pode instalar a extensão da Política Azure para o Código do Estúdio Visual seguindo estes passos:

1. Abra o Visual Studio Code.
1. A partir da barra de menu, vá a **Ver**  >  **Extensões.**
1. Na caixa de pesquisa, insira **a Política Azure**.
1. Selecione **Azure Policy** a partir dos resultados da pesquisa e, em seguida, selecione **Instalar**.
1. Selecione **Recarregar** quando necessário.

Para um utilizador nacional de nuvem, siga estes passos para definir primeiro o ambiente Azure:

1. Selecione **Ficheiro\Preferências\Definições**.
1. Pesquisar na seguinte cadeia: _Azure: Cloud_
1. Selecione a nuvem nacional da lista:

   :::image type="content" source="../media/extension-for-vscode/set-default-azure-cloud-sign-in.png" alt-text="Screenshot de selecionar a nação Azure cloud iniciar súmite para Código de Estúdio Visual." border="false":::

## <a name="using-the-policy-extension"></a>Usando a extensão da Política

> [!NOTE]
> As alterações feitas localmente às políticas visualizadas na extensão da Política Azure para o Código do Estúdio Visual não estão sincronizadas com o Azure.

### <a name="connect-to-an-azure-account"></a>Ligar a uma conta do Azure

Para avaliar recursos e procurar pseudónimos, tem de se ligar à sua conta Azure. Siga estes passos para ligar ao Azure a partir do Código do Estúdio Visual:

1. Inscreva-se no Azure a partir da extensão da Política Azure ou da Paleta de Comando.

   - Extensão da Política Azure

     A partir da extensão da Política Azure, **selecione Iniciar súm em Azure**.

     :::image type="content" source="../media/extension-for-vscode/azure-cloud-sign-in-policy-extension.png" alt-text="Screenshot do Código do Estúdio Visual e o ícone para a extensão da Política Azure." border="false":::

   - Paleta de comando

     A partir da barra de menu, vá à **Paleta de**  >  **Comando,** e introduza **Azure: Iniciar Sedura**.

     :::image type="content" source="../media/extension-for-vscode/azure-cloud-sign-in-command-palette.png" alt-text="Screenshot do sinal de nuvem Azure em opções para Código de Estúdio Visual a partir da Paleta de Comando." border="false":::

1. Siga o sinal em instruções para iniciar seduca. Depois de conectado, o nome da sua conta Azure é mostrado na barra de estado na parte inferior da janela Visual Studio Code.

### <a name="select-subscriptions"></a>Selecione subscrições

Quando iniciar a sua primeira sação, apenas os recursos e políticas de subscrição predefinidos são carregados pela extensão da Política Azure. Para adicionar ou remover subscrições de recursos e políticas de exibição, siga estes passos:

1. Inicie o comando de subscrição a partir da Paleta de Comando ou do rodapé da janela.

   - Paleta de comando:

     A partir da barra de menu, vá à **Paleta** > **de Comando,** e **introduza Azure: Selecione Subscrições**.

   - Rodapé da janela

     No rodapé da janela na parte inferior do ecrã, selecione o segmento que corresponde **a Azure: \<your account\>**.

1. Utilize a caixa de filtro para encontrar rapidamente as subscrições pelo nome. Em seguida, verifique ou remova o cheque de cada subscrição para definir as subscrições mostradas pela extensão da Política Azure. Quando terminar de adicionar ou remover subscrições para exibir, selecione **OK**.

### <a name="search-for-and-view-resources"></a>Pesquisar e visualizar recursos

A extensão da Política Azure lista os recursos nas subscrições selecionadas pelo Fornecedor de Recursos e pelo grupo de recursos no painel **de recursos.** A visão de árvore inclui os seguintes agrupamentos de recursos dentro da subscrição selecionada ou ao nível de subscrição:

- **Fornecedores de Recursos**
  - Cada Fornecedor de Recursos registado com recursos e recursos infantis relacionados que têm pseudónimos políticos
- **Grupos de Recursos**
  - Todos os recursos do grupo de recursos em que estão

Por predefinição, a extensão filtra a parte do "Fornecedor de Recursos" através de recursos e recursos existentes que possuem pseudónimos políticos. Altere este comportamento na **Política** de  >  **Azure de Definições para** ver todos os  >   Fornecedores de Recursos sem filtragem.

Os clientes com centenas ou milhares de recursos numa única subscrição podem preferir uma forma pes pesquisada de localizar os seus recursos. A extensão da Política Azure permite procurar um recurso específico com as seguintes etapas:

1. Inicie a interface de pesquisa a partir da extensão da Política Azure ou da Paleta de Comando.

   - Extensão da Política Azure

     A partir da extensão da Política Azure, paire sobre o painel **de Recursos** e selecione a elipse e, em seguida, selecione Recursos **de Pesquisa**.

   - Paleta de comando:

     A partir da barra de menu, vá à **Paleta** > **de Comando,** e insira **Recursos: Recursos de Pesquisa**.

1. Se for selecionada mais de uma subscrição para visualização, utilize o filtro para selecionar qual a subscrição a procurar.

1. Utilize o filtro para selecionar o grupo de recursos para pesquisar que faz parte da subscrição previamente escolhida.

1. Utilize o filtro para selecionar qual o recurso a exibir. O filtro funciona tanto para o nome do recurso como para o tipo de recurso.

### <a name="discover-aliases-for-resource-properties"></a>Descubra pseudónimos para propriedades de recursos

Quando um recurso é selecionado, seja através da interface de pesquisa ou selecionando-o na visão de árvore, a extensão Azure Policy abre o ficheiro JSON representando esse recurso e todos os seus valores de propriedade Azure Resource Manager.

Uma vez que um recurso esteja aberto, pairando sobre o nome ou valor da propriedade do Gestor de Recursos exibe o pseudónimo da Política Azure se existir. Neste exemplo, o recurso é um `Microsoft.Compute/virtualMachines` tipo de recurso e as **propriedades.storageProfile.imageReference.offer** propriedade está sobreda. Pairar mostra os pseudónimos correspondentes.

:::image type="content" source="../media/extension-for-vscode/extension-hover-shows-property-alias.png" alt-text="Screenshot da extensão da Política Azure para Visual Studio Code pairando sobre uma propriedade para exibir os nomes de pseudónimos." border="false":::

> [!NOTE]
> A extensão do Código VS suporta apenas a avaliação das propriedades do modo Gestor de Recursos. Para obter mais informações sobre os modos, consulte as [definições](../concepts/definition-structure.md#mode)do modo .

### <a name="search-for-and-view-policies-and-assignments"></a>Procurar e visualizar políticas e atribuições

A extensão da Política Azure lista tipos de política e atribuições de políticas como uma visão de árvore para as subscrições selecionadas a serem exibidas no painel **de políticas.** Os clientes com centenas ou milhares de políticas ou atribuições numa única subscrição podem preferir uma forma pes pes pes pesquisada de localizar as suas políticas ou atribuições. A extensão da Política Azure permite procurar uma política ou atribuição específica com as seguintes etapas:

1. Inicie a interface de pesquisa a partir da extensão da Política Azure ou da Paleta de Comando.

   - Extensão da Política Azure

     A partir da extensão da Política Azure, paire sobre o painel **de Políticas** e selecione a elipse e, em seguida, selecione Políticas **de Pesquisa**.

   - Paleta de comando:

     A partir da barra de menu, vá à **Paleta** > **de Comando,** e introduza **Políticas: Políticas de pesquisa**.

1. Se for selecionada mais de uma subscrição para visualização, utilize o filtro para selecionar qual a subscrição a procurar.

1. Utilize o filtro para selecionar qual o tipo de política ou atribuição para pesquisar que faça parte da subscrição previamente escolhida.

1. Utilize o filtro para selecionar qual a política ou para visualizar. O filtro funciona para _o displayName_ para a definição de política ou atribuição de políticas.

Ao selecionar uma política ou atribuição, seja através da interface de pesquisa ou selecionando-a na visão de árvore, a extensão da Política Azure abre o JSON que representa a política ou atribuição e todos os valores de propriedade do Gestor de Recursos. A extensão pode validar o esquema JSON da Política Azure aberta.

### <a name="export-objects"></a>Objetos de exportação

Os objetos das suas subscrições podem ser exportados para um ficheiro JSON local. No painel **de Recursos** ou **Políticas,** paire sobre ou selecione um objeto exportável. No final da linha realçada, selecione o ícone de poupança e selecione uma pasta para guardar esses recursos JSON.

Os seguintes objetos podem ser exportados localmente:

- Painel de recursos
  - Grupos de recursos
  - Recursos individuais (seja num grupo de recursos ou sob um Fornecedor de Recursos)
- Painel de políticas
  - Atribuições de políticas
  - Definições políticas incorporadas
  - Definições de política personalizada
  - Iniciativas

### <a name="on-demand-evaluation-scan"></a>On-demand evaluation scan (Análise de avaliação a pedido)

Pode iniciar-se uma verificação de avaliação com a extensão da Política Azure para o Código do Estúdio Visual. Para iniciar uma avaliação, selecione e pine cada um dos seguintes objetos: um recurso, uma definição de política e uma atribuição de políticas.

1. Para fixar cada objeto, encontre-o no painel **de Recursos** ou no painel **de Políticas** e selecione o pino para um ícone do separador de edição. Fixar um objeto adiciona-o ao painel de **avaliação** da extensão.
1. No painel de **avaliação,** selecione um de cada objeto e utilize o selecionador para o ícone de avaliação para assinalá-lo como incluído na avaliação.
1. No topo do painel de **avaliação,** selecione o ícone de avaliação de execução. Um novo painel no Código do Estúdio Visual abre com os detalhes de avaliação resultantes no formulário JSON.

> [!NOTE]
> Para [auditIfNotExists](../concepts/effects.md#auditifnotexists) ou implementar definições de [política,](../concepts/effects.md#deployifnotexists) utilize o ícone mais no painel **de avaliação** para selecionar um recurso _relacionado_ para a verificação da existência.

Os resultados da avaliação fornecem informações sobre a definição de política e atribuição de políticas, juntamente com a **políticaEvaluations.evaluationResult** property. A saída é semelhante ao seguinte exemplo:

```json
{
    "policyEvaluations": [
        {
            "policyInfo": {
                ...
            },
            "evaluationResult": "Compliant",
            "effectDetails": {
                "policyEffect": "Audit",
                "existenceScope": "None"
            }
        }
    ]
}
```

> [!NOTE]
> A extensão do Código VS suporta apenas a avaliação das propriedades do modo Gestor de Recursos. Para obter mais informações sobre os modos, consulte as [definições](../concepts/definition-structure.md#mode)do modo .
>
> A funcionalidade de avaliação não funciona nas instalações do macOS e linux da extensão.

### <a name="sign-out"></a>Terminar sessão

A partir da barra de menu, vá à **Paleta de** Comando  >  **Ver,** e depois insira **Azure: Assine**.

## <a name="next-steps"></a>Passos seguintes

- Rever exemplos nas [amostras da Azure Policy](../samples/index.md).
- Reveja a [estrutura de definição do Azure Policy](../concepts/definition-structure.md).
- Veja [Compreender os efeitos do Policy](../concepts/effects.md).
- Entenda como [criar políticas programáticas.](programmatically-create.md)
- Saiba como [remediar recursos não conformes.](remediate-resources.md)
- Reveja o que é um grupo de gestão com [Organizar os seus recursos com grupos de gestão Azure.](../../management-groups/overview.md)
