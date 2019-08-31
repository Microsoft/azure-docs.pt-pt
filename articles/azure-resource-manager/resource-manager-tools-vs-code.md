---
title: Usar Visual Studio Code para criar modelos de Azure Resource Manager
description: Saiba como instalar e usar Visual Studio Code e a extensão de ferramentas de Azure Resource Manager.
author: mumian
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 08/30/2019
ms.author: jgao
ms.openlocfilehash: 8d2b818846eeae44ffe05b4546a333510c638621
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/30/2019
ms.locfileid: "70196156"
---
# <a name="use-visual-studio-code-to-create-azure-resource-manager-templates"></a>Usar Visual Studio Code para criar modelos de Azure Resource Manager

O Visual Studio Code é um editor leve, multiplataforma e de código aberto. A extensão de ferramentas de modelo de Azure Resource Manager é um plug-in para o desenvolvimento de modelo do Resource Manager. A extensão adiciona suporte de idioma para modelos para fornecer IntelliSense, realce de sintaxe, ajuda online e muitas outras funções de linguagem. Juntos, eles fornecem uma experiência de desenvolvimento de modelo recomendada.

## <a name="install-visual-studio-code"></a>Instalar Visual Studio Code

O Visual Studio Code dá suporte ao MacOS, ao Windows e ao Linux.  Ele pode ser instalado de [Visual Studio Code](https://code.visualstudio.com/).

## <a name="install-resource-manager-tools-extension"></a>Instalar extensão de ferramentas do Resource Manager

1. Abra o Visual Studio Code.
1. Selecione **extensões** no menu à esquerda. Ou, no menu **Exibir** , selecione **extensões** para abrir o painel extensões.

    ![Instalar Visual Studio Code extensão de ferramentas do Resource Manager](./media/resource-manager-tools-vs-code/resource-manager-visual-studio-code-tools-extension.png)
1. Procure **Gerenciador de recursos**.
1. Selecione **instalar** em **ferramentas de Azure Resource Manager**.

## <a name="the-extension-features"></a>Os recursos de extensão

### <a name="colorization-for-template-language-expressions"></a>Colorização para expressões de linguagem do modelo

Parâmetros, variáveis, funções, nomes e expressões são codificados por cores, conforme mostrado na seguinte captura de tela:

![Visual Studio Code colorização de extensões de ferramentas do Resource Manager](./media/resource-manager-tools-vs-code/resource-manager-tools-extension-colorization.png)

A exibição de estrutura de tópicos do modelo facilita a navegação por meio de modelos grandes.

### <a name="intellisense"></a>IntelliSense

A extensão do modelo do Resource Manager sabe possíveis conclusões de nomes de função, parâmetros, variáveis e referências. As sugestões do IntelliSense aparecem conforme você digita. Se você continuar digitando caracteres, a lista de Membros (variáveis, métodos, etc.) será filtrada para incluir apenas os membros que contêm os caracteres digitados. Pressionar **Tab** ou **Enter** insere o membro selecionado.

- Nomes de funções internas

    ![Visual Studio Code funções IntelliSense das extensões de ferramentas do Resource Manager](./media/resource-manager-tools-vs-code/resource-manager-tools-extension-intellisense-functions.png)

- Referências de parâmetro

    ![Visual Studio Code parâmetros do IntelliSense das extensões de ferramentas do Resource Manager](./media/resource-manager-tools-vs-code/resource-manager-tools-extension-intellisense-parameters.png)

- Referências de variáveis

    ![Visual Studio Code variáveis do IntelliSense das extensões de ferramentas do Resource Manager](./media/resource-manager-tools-vs-code/resource-manager-tools-extension-intellisense-variables.png)

- Propriedades de resourcegroup ()

    ![Visual Studio Code funções IntelliSense das extensões de ferramentas do Resource Manager](./media/resource-manager-tools-vs-code/resource-manager-tools-extension-intellisense-resourcegroup.png)

Além disso, o IntelliSense também funciona com as propriedades Subscription () e as propriedades de referências a variáveis que são objetos.

### <a name="signature-help-for-function-parameters"></a>Ajuda de assinatura para parâmetros de função

Ao passar o mouse sobre nomes de função, a extensão mostra a ajuda de assinatura para parâmetros de função.

![Função de assinatura de extensões de ferramentas do Resource Manager Visual Studio Code](./media/resource-manager-tools-vs-code/resource-manager-tools-extension-signature-function.png)

### <a name="go-to-definition-for-variable-and-parameter-references"></a>Ir para a definição de referências de variáveis e parâmetros

Você pode ir para a definição com **Ctrl + clique**ou usando o menu de contexto, conforme mostrado na captura de tela: ![Visual Studio Code extensões de ferramentas do Resource Manager vão para a definição](./media/resource-manager-tools-vs-code/resource-manager-tools-extension-context-menu.png)

Você pode abrir a definição para o lado com **Ctrl + Alt + clique**.

### <a name="peek-for-variable-and-parameter-definitions"></a>Inspecionar definições de variáveis e parâmetros

Para abrir o editor inspecionado, use o menu de contexto, conforme mostrado na captura de tela anterior.

A captura de tela a seguir mostra o editor de Peek:

![Editor de inspeção de extensões de ferramentas do Gerenciador de recursos Visual Studio Code](./media/resource-manager-tools-vs-code/resource-manager-tools-extension-peek-editor.png)

### <a name="find-all-references-for-variables-and-parameters"></a>Localizar todas as referências para variáveis e parâmetros

Para localizar todas as referências, use o menu de contexto, conforme mostrado na captura de tela anterior.

A captura de tela a seguir mostra como as referências são realçadas:

![Visual Studio Code extensões de ferramentas do Resource Manager localizam todas as referências](./media/resource-manager-tools-vs-code/resource-manager-tools-extension-find-all-references.png)

### <a name="rename-all-references-for-variables-and-parameters"></a>Renomear todas as referências para variáveis e parâmetros

Para renomear todas as referências para variáveis e parâmetros, use o menu de contexto, conforme mostrado na captura de tela anterior.

### <a name="hover-for-parameter-description"></a>Focalizar a descrição do parâmetro

![Definição de foco das extensões de ferramentas do Resource Manager Visual Studio Code](./media/resource-manager-tools-vs-code/resource-manager-tools-extension-hover-parameters.png)

### <a name="brace-matching"></a>Correspondência de chaves

Os colchetes correspondentes são realçados assim que o cursor está próximo de um deles. Quando você clica em uma chave, a chave correspondente também é realçada conforme mostrado na captura de tela a seguir:

![Correspondência de chaves de extensões de ferramentas do Resource Manager Visual Studio Code](./media/resource-manager-tools-vs-code/resource-manager-tools-extension-brace-matching.png)

### <a name="show-errors-and-warnings"></a>Mostrar erros e avisos

Os erros identificados pela extensão incluem:

- Referências de parâmetro indefinido
- Referências de variáveis indefinidas
- Nomes de função não reconhecidos
- uso da função Reference () na definição da variável
- Número incorreto de argumentos em funções

Os avisos incluem:

- Parâmetros não utilizados
- Variáveis não utilizadas

## <a name="next-steps"></a>Passos seguintes

- Para saber mais sobre os modelos do Resource Manager e do Resource Manager, confira [Azure Resource Manager visão geral](./resource-group-overview.md).
- Para percorrer um início rápido usando Visual Studio Code, consulte [início rápido: Criar modelos de Azure Resource Manager usando Visual Studio Code](./resource-manager-quickstart-create-templates-use-visual-studio-code.md)
