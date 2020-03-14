---
title: Use o Código do Estúdio Visual para criar modelos
description: Saiba como instalar e utilizar o Código de Estúdio Visual e a extensão de Ferramentas de Gestor de Recursos Azure.
author: mumian
ms.topic: conceptual
ms.date: 08/30/2019
ms.author: jgao
ms.openlocfilehash: 585e2773ea5d6af184f85e65b63d39b60d632146
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79273634"
---
# <a name="use-visual-studio-code-to-create-azure-resource-manager-templates"></a>Use o Código do Estúdio Visual para criar modelos de Gestor de Recursos Azure

Visual Studio Code é um editor leve, multiplataforma e open-source. A extensão de ferramentas de modelo do Gestor de Recursos Azure é um plugin para o desenvolvimento do modelo de gestor de recursos. A extensão adiciona suporte linguístico para modelos que lhe dão intellisense, sintaxe realçando, ajuda em linha e muitas outras funções linguísticas. Juntos, proporcionam uma experiência recomendada de desenvolvimento do modelo.

## <a name="install-visual-studio-code"></a>Instalar Visual Studio Code

O visual de estúdio Code suporta MacOS, Windows e Linux.  Pode ser instalado a partir do [Visual Studio Code](https://code.visualstudio.com/).

## <a name="install-resource-manager-tools-extension"></a>Instalar extensão de ferramentas de gestor de recursos

1. Abra o Visual Studio Code.
1. **Selecione Extensões** do menu esquerdo. Ou a partir do menu **'Ver',** selecione **Extensões** para abrir o painel de extensões.

    ![Instale a extensão de ferramentas de gestor de recursos de código de estúdio visual](./media/use-vs-code-to-create-template/resource-manager-visual-studio-code-tools-extension.png)
1. Pesquisa por **Gestor de Recursos**.
1. **Selecione Instalar** sob **ferramentas de gestor de recursos Azure**.

## <a name="the-extension-features"></a>As características de extensão

### <a name="colorization-for-template-language-expressions"></a>Coloração para expressões de linguagem de modelo

Os parâmetros, variáveis, funções, nomes e expressões são codificados por cores, como mostra a seguinte imagem:

![Visual Studio Code Resource Manager ferramentas extensões coloração](./media/use-vs-code-to-create-template/resource-manager-tools-extension-colorization.png)

A vista de contorno do modelo facilita a navegação através de grandes modelos.

### <a name="intellisense"></a>Intellisense

A extensão do modelo do Gestor de Recursos conhece possíveis conclusões para nomes de funções, parâmetros, variáveis e referências. As sugestões do IntelliSense aparecem à medida que escreves. Se continuar a digitar caracteres, a lista de membros (variáveis, métodos, etc.) é filtrada apenas para incluir membros que contenham os caracteres dactilografados. Premir **o separador** ou **inserir** insere o membro selecionado.

- Nomes de função incorporados

    ![Visual Studio Code Resource Manager ferramentas de extensão funções intellisense](./media/use-vs-code-to-create-template/resource-manager-tools-extension-intellisense-functions.png)

- Referências paraparâmetros

    ![Visual Studio Code Resource Manager ferramentas de extensões de parâmetros intellisense](./media/use-vs-code-to-create-template/resource-manager-tools-extension-intellisense-parameters.png)

- Referências variáveis

    ![Visual Studio Code Resource Manager ferramentas extensões intellisense variáveis](./media/use-vs-code-to-create-template/resource-manager-tools-extension-intellisense-variables.png)

- propriedades do grupo de recursos()

    ![Visual Studio Code Resource Manager ferramentas de extensão funções intellisense](./media/use-vs-code-to-create-template/resource-manager-tools-extension-intellisense-resourcegroup.png)

Além disso, o intellisense também trabalha com propriedades de subscrição e as propriedades de referências a variáveis que são objetos.

### <a name="signature-help-for-function-parameters"></a>Ajuda de assinatura para parâmetros de função

Ao pairar sobre os nomes de funções, a extensão mostra a ajuda de assinatura para os parâmetros de função.

![Função de assinatura de extensões de funcionalidade seleções do Visual Studio Code Resource Manager](./media/use-vs-code-to-create-template/resource-manager-tools-extension-signature-function.png)

### <a name="go-to-definition-for-variable-and-parameter-references"></a>Ir definição para referências variáveis e parâmetros

Pode saltar para a definição com **Ctrl+Click,** ou utilizando o menu de contexto como mostrado na imagem: ![as extensões de ferramentas do Visual Studio Code Resource Manager vão para a definição](./media/use-vs-code-to-create-template/resource-manager-tools-extension-context-menu.png)

Pode abrir a definição para o lado com **Ctrl+Alt+Click**.

### <a name="peek-for-variable-and-parameter-definitions"></a>Espreite as definições variáveis e parâmetros

Para abrir o editor peeked, use o menu de contexto como mostrado na imagem anterior.

A seguinte imagem mostra o editor de peek:

![Visual Studio Code Resource Manager ferramentas extensões peek editor](./media/use-vs-code-to-create-template/resource-manager-tools-extension-peek-editor.png)

### <a name="find-all-references-for-variables-and-parameters"></a>Encontre todas as referências para variáveis e parâmetros

Para encontrar todas as referências, utilize o menu de contexto como mostrado na imagem anterior.

A imagem que se segue mostra como as referências são destacadas:

![As extensões de ferramentas visual Studio Code Resource Manager encontram todas as referências](./media/use-vs-code-to-create-template/resource-manager-tools-extension-find-all-references.png)

### <a name="rename-all-references-for-variables-and-parameters"></a>Mude o nome de todas as referências para variáveis e parâmetros

Para mudar o nome de todas as referências para variáveis e parâmetros, utilize o menu de contexto como mostrado na imagem anterior.

### <a name="hover-for-parameter-description"></a>Pairar para descrição do parâmetro

![Visual Studio Code Resource Manager ferramentas extensões de definição hover](./media/use-vs-code-to-create-template/resource-manager-tools-extension-hover-parameters.png)

### <a name="brace-matching"></a>Correspondência de cinta

Os suportes correspondentes são realçados assim que o cursor estiver perto de um deles. Quando clica numa cinta, o aparelho correspondente também é realçado como mostrado na seguinte imagem:

![Visual Studio Code Resource Manager ferramentas de extensão de suporte combinando](./media/use-vs-code-to-create-template/resource-manager-tools-extension-brace-matching.png)

### <a name="show-errors-and-warnings"></a>Mostrar erros e avisos

Os erros identificados pela extensão incluem:

- Referências de parâmetros indefinidas
- Referências variáveis indefinidas
- Nomes de funções não reconhecidos
- utilização da função de referência na definição variável
- Número incorreto de argumentos em funções

Os avisos incluem:

- Parâmetros não utilizados
- Variáveis não utilizadas

## <a name="next-steps"></a>Passos seguintes

- Para aprender sobre a criação de modelos de Gestor de Recursos Azure, consulte [Tutorial: Crie e implemente o seu primeiro modelo](template-tutorial-create-first-template.md)de Gestor de Recursos Azure .
- Para passar por um quickstart usando o Código do Estúdio Visual, consulte [quickstart: Create Azure Resource Manager, utilizando o Código de Estúdio Visual](quickstart-create-templates-use-visual-studio-code.md)
