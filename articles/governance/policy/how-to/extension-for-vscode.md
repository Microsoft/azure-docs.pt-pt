---
title: Extensão de Azure Policy para Visual Studio Code
description: Saiba como usar a extensão de Azure Policy para Visual Studio Code para pesquisar aliases do Gerenciador de recursos.
ms.date: 11/04/2019
ms.topic: how-to
ms.openlocfilehash: 48ef098cf970b5128185c40c92f8bb02ad8d9698
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/05/2019
ms.locfileid: "74873119"
---
# <a name="use-azure-policy-extension-for-visual-studio-code"></a>Usar extensão de Azure Policy para Visual Studio Code

> Aplica-se a Azure Policy versão de extensão **0.0.21** e mais recente

Saiba como usar a extensão de Azure Policy para Visual Studio Code para pesquisar [aliases](../concepts/definition-structure.md#aliases) e examinar recursos e políticas. Primeiro, descreveremos como instalar a extensão de Azure Policy no Visual Studio Code. Em seguida, vamos examinar como Pesquisar aliases.

A extensão de Azure Policy para Visual Studio Code pode ser instalada em todas as plataformas com suporte no Visual Studio Code. Esse suporte inclui Windows, Linux e macOS.

> [!NOTE]
> As alterações feitas localmente nas políticas exibidas na extensão de Azure Policy para Visual Studio Code não são sincronizadas com o Azure.

## <a name="prerequisites"></a>Pré-requisitos

Os itens a seguir são necessários para concluir as etapas neste artigo:

- Uma subscrição do Azure. Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.
- [Visual Studio Code](https://code.visualstudio.com).

## <a name="install-azure-policy-extension"></a>Instalar a extensão de Azure Policy

Depois de atender aos pré-requisitos, você pode instalar Azure Policy extensão para Visual Studio Code seguindo estas etapas:

1. Abra o Visual Studio Code.

1. Na barra de menus, acesse **Exibir** **extensões**de > .

1. Na caixa de pesquisa, digite **Azure Policy**.

1. Selecione **Azure Policy** nos resultados da pesquisa e, em seguida, selecione **instalar**.

1. Selecione **recarregar** quando necessário.

## <a name="set-the-azure-environment"></a>Definir o ambiente do Azure

Para um usuário de nuvem nacional, siga estas etapas para definir o ambiente do Azure primeiro:

1. Selecione **File\Preferences\Settings**.

1. Pesquise na seguinte cadeia de caracteres: _Azure: Cloud_

1. Selecione a nuvem de país na lista:

   ![Definir entrada de nuvem do Azure padrão para Visual Studio Code](../media/extension-for-vscode/set-default-azure-cloud-sign-in.png)

## <a name="connect-to-an-azure-account"></a>Conectar-se a uma conta do Azure

Para avaliar os recursos e os aliases de pesquisa, você deve se conectar à sua conta do Azure. Siga estas etapas para se conectar ao Azure por meio do Visual Studio Code:

1. Entre no Azure por meio da extensão de Azure Policy ou da paleta de comandos.

   - Extensão de Azure Policy

     Na extensão Azure Policy, selecione **entrar no Azure**.

     ![Entrada na nuvem do Azure para Visual Studio Code da extensão Azure Policy](../media/extension-for-vscode/azure-cloud-sign-in-policy-extension.png)

   - Paleta de comandos

     Na barra de menus, acesse **exibir** > **paleta de comandos**e insira **Azure: entrar**.

     ![Entrada na nuvem do Azure para Visual Studio Code da paleta de comandos](../media/extension-for-vscode/azure-cloud-sign-in-command-palette.png)

1. Siga as instruções de entrada para entrar no Azure. Depois que você estiver conectado, o nome da sua conta do Azure será mostrado na barra de status na parte inferior da janela de Visual Studio Code.

## <a name="select-subscriptions"></a>Selecionar subscrições

Quando você entra pela primeira vez, somente os recursos e as políticas de assinatura padrão são carregados pela extensão de Azure Policy. Para adicionar ou remover assinaturas da exibição de recursos e políticas, siga estas etapas:

1. Inicie o comando de assinatura na paleta de comandos ou no rodapé da janela.

   - Paleta de comandos: 

     Na barra de menus, acesse **exibir** > **paleta de comandos**e digite **Azure Policy: selecione assinaturas**.

   - Rodapé da janela

     No rodapé da janela na parte inferior da tela, selecione o segmento que corresponde ao **Azure: \<sua conta\>** .

1. Use a caixa de filtro para localizar rapidamente as assinaturas por nome. Em seguida, verifique ou remova a verificação de cada assinatura para definir as assinaturas mostradas pela extensão de Azure Policy. Quando terminar de adicionar ou remover assinaturas a serem exibidas, selecione **OK**.

## <a name="search-for-and-view-resources"></a>Pesquisar e exibir recursos

A extensão Azure Policy lista os recursos nas assinaturas selecionadas por provedor de recursos e por grupo de recursos no painel **recursos** . O TreeView inclui os seguintes agrupamentos de recursos dentro da assinatura selecionada ou no nível da assinatura:

- **Provedores de recursos**
  - Cada provedor de recursos registrado com recursos e recursos filho relacionados que têm aliases de política
- **Grupos de recursos**
  - Todos os recursos do grupo de recursos em que estão

Por padrão, a extensão filtra a parte ' provedor de recursos ' por recursos existentes e recursos que têm aliases de política. Altere esse comportamento em **configurações** > **extensões** > **Azure Policy** para ver todos os provedores de recursos sem filtragem.

Clientes com centenas ou milhares de recursos em uma única assinatura podem preferir uma maneira pesquisável de localizar seus recursos. A extensão Azure Policy torna possível pesquisar um recurso específico com as seguintes etapas:

1. Inicie a interface de pesquisa na Azure Policy extensão ou na paleta de comandos.

   - Extensão de Azure Policy

     Na extensão Azure Policy, passe o mouse sobre o painel **recursos** e selecione as reticências e, em seguida, selecione **Pesquisar recursos**.

   - Paleta de comandos:

     Na barra de menus, acesse **exibir** > **paleta de comandos**e insira **recursos: pesquisar recursos**.

1. Se mais de uma assinatura for selecionada para exibição, use o filtro para selecionar qual assinatura será pesquisada.

1. Use o filtro para selecionar qual grupo de recursos a ser pesquisado faz parte da assinatura escolhida anteriormente.

1. Use o filtro para selecionar qual recurso Exibir. O filtro funciona tanto para o nome do recurso quanto para o tipo de recurso.

## <a name="discover-aliases-for-resource-properties"></a>Descobrir aliases para propriedades de recurso

Quando um recurso é selecionado, seja por meio da interface de pesquisa ou selecionando-o no modo de exibição de árvore, a extensão Azure Policy abre o arquivo JSON que representa esse recurso e todos os valores de Propriedade do Resource Manager.

Quando um recurso estiver aberto, passar o mouse sobre o nome ou o valor da Propriedade do Resource Manager exibirá o Azure Policy alias, se houver. Neste exemplo, o recurso é um tipo de recurso `Microsoft.Compute/virtualMachines` e a propriedade **Propriedades. storageProfile. imageReference. offer** é focalizada. Focalizar mostra os aliases correspondentes.

![Azure Policy o foco da extensão mostra o alias da Propriedade do Resource Manager](../media/extension-for-vscode/extension-hover-shows-property-alias.png)

## <a name="search-for-and-view-policies-and-assignments"></a>Pesquisar e exibir políticas e atribuições

A extensão Azure Policy lista os tipos de política e as atribuições de política como um TreeView para as assinaturas selecionadas a serem exibidas no painel **políticas** . Clientes com centenas ou milhares de políticas ou atribuições em uma única assinatura podem preferir uma maneira pesquisável de localizar suas políticas ou atribuições. A extensão Azure Policy torna possível pesquisar uma política ou atribuição específica com as seguintes etapas:

1. Inicie a interface de pesquisa na Azure Policy extensão ou na paleta de comandos.

   - Extensão de Azure Policy

     Na extensão Azure Policy, passe o mouse sobre o painel **políticas** e selecione as reticências e, em seguida, selecione **políticas de pesquisa**.

   - Paleta de comandos:

     Na barra de menus, acesse **exibir** > **paleta de comandos**e insira **políticas: Pesquisar políticas**.

1. Se mais de uma assinatura for selecionada para exibição, use o filtro para selecionar qual assinatura será pesquisada.

1. Use o filtro para selecionar o tipo de política ou a atribuição a ser pesquisada que faça parte da assinatura escolhida anteriormente.

1. Use o filtro para selecionar qual política ou exibir. O filtro funciona para _DisplayName_ para a definição de política ou atribuição de política.

Ao selecionar uma política ou atribuição, seja por meio da interface de pesquisa ou selecionando-a no modo de exibição de árvore, a extensão de Azure Policy abre o JSON que representa a política ou atribuição e todos os seus valores de Propriedade do Resource Manager. A extensão pode validar o esquema do Azure Policy JSON aberto.

## <a name="sign-out"></a>Terminar sessão

Na barra de menus, acesse **exibir** > **paleta de comandos**e, em seguida, insira **Azure: sair**.

## <a name="next-steps"></a>Passos seguintes

- Examine exemplos em [exemplos de Azure Policy](../samples/index.md).
- Reveja a [estrutura de definição do Azure Policy](../concepts/definition-structure.md).
- Veja [Compreender os efeitos do Policy](../concepts/effects.md).
- Entenda como [criar políticas programaticamente](programmatically-create.md).
- Saiba como [corrigir recursos sem conformidade](remediate-resources.md).
- Examine o que é um grupo de gerenciamento e [Organize seus recursos com grupos de gerenciamento do Azure](../../management-groups/overview.md).