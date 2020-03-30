---
title: Extensão da Política Azure para Código de Estúdio Visual
description: Aprenda a usar a extensão da Política Azure para o Código do Estúdio Visual para procurar pseudónimos do Gestor de Recursos.
ms.date: 03/07/2020
ms.topic: how-to
ms.openlocfilehash: 0e9123f2975bda0d61e9d6e9bf894ecd359e6c5a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79264677"
---
# <a name="use-azure-policy-extension-for-visual-studio-code"></a>Utilize a extensão da política azure para o Código do Estúdio Visual

> Aplica-se à versão de extensão da Política Azure **0.0.21** e mais recente

Aprenda a usar a extensão da Política Azure para o Código do Estúdio Visual para procurar [pseudónimos](../concepts/definition-structure.md#aliases) e rever recursos e políticas. Primeiro, descreveremos como instalar a extensão da Política Azure no Código do Estúdio Visual. Então vamos ver como procurar pseudónimos.

A extensão da Política Azure para Código de Estúdio Visual pode ser instalada em todas as plataformas que são suportadas pelo Visual Studio Code. Este suporte inclui Windows, Linux e macOS.

> [!NOTE]
> As alterações feitas localmente às políticas vistas na extensão da Política Azure para o Código do Estúdio Visual não estão sincronizadas com o Azure.

## <a name="prerequisites"></a>Pré-requisitos

São necessários os seguintes itens para completar os passos deste artigo:

- Uma subscrição do Azure. Se não tiver uma subscrição Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.
- [Código de estúdio visual.](https://code.visualstudio.com)

## <a name="install-azure-policy-extension"></a>Instalar extensão da política do Azure

Depois de cumprir os pré-requisitos, pode instalar a extensão da Política Azure para o Código do Estúdio Visual seguindo estes passos:

1. Abra o Visual Studio Code.

1. A partir da barra de menus, vá para **Ver** > **Extensões**.

1. Na caixa de pesquisa, introduza a **Política Azure.**

1. Selecione **A Política Azure** a partir dos resultados da pesquisa e, em seguida, selecione **Instalar**.

1. Selecione **Recarregar** quando necessário.

## <a name="set-the-azure-environment"></a>Definir o ambiente Azure

Para um utilizador nacional de nuvem, siga estes passos para definir primeiro o ambiente Azure:

1. Selecione **Ficheiro\Preferências\Definições**.

1. Pesquisa sobre a seguinte corda: _Azure: Cloud_

1. Selecione a nuvem da nação da lista:

   ![Detete o sinal de cloud do Azure para o Código do Estúdio Visual](../media/extension-for-vscode/set-default-azure-cloud-sign-in.png)

## <a name="connect-to-an-azure-account"></a>Ligue-se a uma conta Azure

Para avaliar os recursos e os pseudónimos de procura, deve ligar-se à sua conta Azure. Siga estes passos para ligar ao Azure a partir do Visual Studio Code:

1. Inscreva-se no Azure a partir da extensão da Política Azure ou da Paleta de Comando.

   - Extensão da Política Azure

     A partir da extensão política azure, selecione **Iniciar sessão em Azure**.

     ![Sinal de cloud Azure para Visual Studio Code da extensão da Política Azure](../media/extension-for-vscode/azure-cloud-sign-in-policy-extension.png)

   - Paleta de Comando

     A partir da barra de menus, vá **ver** > paleta de**comando,** e **insira Azure: Inscreva-se**.

     ![Sinal de nuvem azure para código de estúdio visual da paleta de comando](../media/extension-for-vscode/azure-cloud-sign-in-command-palette.png)

1. Siga o sinal em instruções para iniciar sessão no Azure. Depois de conectado, o nome da sua conta Azure é mostrado na barra de estado na parte inferior da janela Visual Studio Code.

## <a name="select-subscriptions"></a>Selecione subscrições

Quando se inscreveu pela primeira vez, apenas os recursos e políticas de subscrição padrão são carregados pela extensão da Política Azure. Para adicionar ou remover subscrições da exibição de recursos e políticas, siga estes passos:

1. Inicie o comando de subscrição a partir da Paleta de Comando ou do rodapé da janela.

   - Paleta de comando: 

     A partir da barra de menus, vá **ver** > paleta de**comando,** e **insira Azure: Selecione Subscrições**.

   - Rodapé da janela

     No rodapé da janela na parte inferior do ecrã, selecione o segmento que corresponde ao **Azure: \<a sua conta\>**.

1. Utilize a caixa de filtro para encontrar rapidamente subscrições pelo nome. Em seguida, verifique ou remova o cheque de cada subscrição para definir as subscrições mostradas pela extensão Política Azure. Quando terminar de adicionar ou remover subscrições para visualizar, selecione **OK**.

## <a name="search-for-and-view-resources"></a>Pesquisar e ver recursos

A extensão da Política Azure lista os recursos nas subscrições selecionadas pelo Fornecedor de Recursos e pelo grupo de recursos no painel **recursos.** A visão da árvore inclui os seguintes agrupamentos de recursos dentro da subscrição selecionada ou ao nível de subscrição:

- **Fornecedores de Recursos**
  - Cada Fornecedor de Recursos registado com recursos e recursos infantis relacionados que têm pseudónimos políticos
- **Grupos de Recursos**
  - Todos os recursos do grupo de recursos em que estão

Por predefinição, a extensão filtra a parte do "Fornecedor de Recursos" através dos recursos e recursos existentes que têm pseudónimos políticos. Altere este comportamento nas **Definições** > **Extensions** > **Política Azure** para ver todos os Fornecedores de Recursos sem filtrar.

Os clientes com centenas ou milhares de recursos numa única subscrição podem preferir uma forma pesquisável de localizar os seus recursos. A extensão da Política Azure permite procurar um recurso específico com os seguintes passos:

1. Inicie a interface de pesquisa a partir da extensão da Política Azure ou da Paleta de Comando.

   - Extensão da Política Azure

     A partir da extensão da Política Azure, paire sobre o painel **Recursos** e selecione a elipse e, em seguida, selecione Recursos de **Pesquisa**.

   - Paleta de comando:

     A partir da barra de menus, vá **ver** > paleta de **comando,** e insira **Recursos: Recursos**de Pesquisa .

1. Se for selecionada mais de uma subscrição para visualização, utilize o filtro para selecionar qual a subscrição a pesquisar.

1. Utilize o filtro para selecionar qual o grupo de recursos para pesquisar que faz parte da subscrição previamente escolhida.

1. Utilize o filtro para selecionar qual o recurso a exibir. O filtro funciona tanto para o nome do recurso como para o tipo de recurso.

## <a name="discover-aliases-for-resource-properties"></a>Descubra pseudónimos para propriedades de recursos

Quando um recurso é selecionado, seja através da interface de pesquisa ou selecionando-o na vista da árvore, a extensão da Política Azure abre o ficheiro JSON representando esse recurso e todos os valores de propriedade do Gestor de Recursos.

Uma vez aberto um recurso, pairando sobre o nome ou valor do Gestor de Recursos exibe o pseudónimo da Política Azure se existir. Neste exemplo, o recurso `Microsoft.Compute/virtualMachines` é um tipo de recurso e as **propriedades.storageProfile.imageReference.offer** property está sobrepairada. Pairar mostra os pseudónimos correspondentes.

![Hover de extensão da Política Azure mostra pseudónimo de Gestor de Recursos](../media/extension-for-vscode/extension-hover-shows-property-alias.png)

## <a name="search-for-and-view-policies-and-assignments"></a>Pesquisar e ver políticas e atribuições

A extensão da Política Azure lista os tipos de políticas e as atribuições políticas como uma visão de árvore para as assinaturas selecionadas para serem exibidas no painel **Políticas.** Os clientes com centenas ou milhares de apólices ou atribuições numa única subscrição podem preferir uma forma pesquisável de localizar as suas políticas ou atribuições. A extensão da Política Azure permite procurar uma política ou atribuição específica com os seguintes passos:

1. Inicie a interface de pesquisa a partir da extensão da Política Azure ou da Paleta de Comando.

   - Extensão da Política Azure

     A partir da extensão da Política Azure, paire sobre o painel **Políticas** e selecione a elipse e, em seguida, selecione Políticas de **Pesquisa**.

   - Paleta de comando:

     A partir da barra de menus, vá **ver** > paleta de **comando,** e insira **Políticas: Políticas**de Pesquisa .

1. Se for selecionada mais de uma subscrição para visualização, utilize o filtro para selecionar qual a subscrição a pesquisar.

1. Utilize o filtro para selecionar que tipo de política ou atribuição para pesquisar que faz parte da subscrição previamente escolhida.

1. Utilize o filtro para selecionar qual a política ou para visualizar. O filtro funciona para _o displayName_ para a definição de política ou atribuição de política.

Ao selecionar uma política ou atribuição, seja através da interface de pesquisa ou selecionando-a na vista da árvore, a extensão da Política Azure abre o JSON que representa a política ou atribuição e todos os valores de propriedade do Gestor de Recursos. A extensão pode validar o esquema Azure Policy JSON aberto.

## <a name="sign-out"></a>Terminar sessão

A partir da barra de menus, vá **ver** > a Paleta de**Comando,** e depois **introduza Azure: Sign out**.

## <a name="next-steps"></a>Passos seguintes

- Reveja exemplos nas [amostras da Política Azure.](../samples/index.md)
- Reveja a [estrutura de definição do Azure Policy](../concepts/definition-structure.md).
- Veja [Compreender os efeitos do Policy](../concepts/effects.md).
- Compreender como [criar políticas programáticas.](programmatically-create.md)
- Aprenda a [remediar recursos não conformes](remediate-resources.md).
- Reveja o que é um grupo de gestão com organizar os seus recursos com grupos de [gestão Azure.](../../management-groups/overview.md)