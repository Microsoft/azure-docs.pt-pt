---
title: Apresentar um bloco de notas do Jupyter como uma apresentação de diapositivos no Azure
description: Como configurar células para o modo de apresentação de slides num bloco de notas do Jupyter e, em seguida, apresentar a apresentação de slides usando a extensão de aumento.
services: app-service
documentationcenter: ''
author: kraigb
manager: douge
ms.assetid: c372175b-beb5-4b45-b2f8-34cb06990117
ms.service: azure
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/04/2018
ms.author: kraigb
ms.openlocfilehash: d36cbe50ab040549353efd19dd794ec7fab51b7b
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57774409"
---
# <a name="run-a-notebook-slideshow"></a>Executar uma apresentação de slides do bloco de notas

Blocos de notas do Azure está pré-configurada com o Jupyter/IPython Slideshow extensão (aumento) que permite-lhe apresentar um bloco de notas diretamente como uma apresentação de diapositivos. Numa apresentação de diapositivos, as células são normalmente apresentados um a um usando um tamanho de fonte que é adequado para apresentar em telas grandes e pode executar o código, em vez de mudar para um computador separado de demonstração.

A imagem seguinte mostra a vista de bloco de anotações padrão, no qual pode ver as células de Markdown e o código de todas as ferramentas:

![Um bloco de notas no modo de exibição padrão](media/slideshow/slideshow-notebook-view.png)

Quando inicia uma apresentação de slides, a primeira célula for alargada para preencher o browser, onde o **X** no canto superior esquerdo termina a apresentação de slides, **?** o apresenta à esquerda inferior atalhos de teclado e as setas no canto inferior direito navegue entre slides:

![Um bloco de notas no modo de apresentação de slides](media/slideshow/slideshow-slide-view.png)

Preparar um bloco de notas para uma apresentação de diapositivos envolve duas atividades principais:

1. Uma vez que as células de Markdown são compostas com tipos de letra grandes, algum conteúdo poderá não estar visível na apresentação de slides. Portanto, normalmente, limitar a quantidade de texto em qualquer determinada célula; Normalmente, um cabeçalho com quatro a seis linhas funciona melhor. Se tiver mais texto, dividido essas informações em várias células.

2. Configure o comportamento de cada célula na apresentação de slides usando a barra de ferramentas de célula de apresentação de slides. Tipos de célula determinam o comportamento dos botões de navegação.

## <a name="the-anatomy-of-a-slideshow"></a>A anatomia de uma apresentação de diapositivos

Se tirar um bloco de notas aleatório e utilizá-lo para uma apresentação de diapositivos, normalmente descobrir que estão se todas as células e grande parte do conteúdo está oculto desativado na parte inferior da janela do navegador. Para fazer uma apresentação em vigor, em seguida, tem de atribuir um tipo de apresentação de slides para cada célula usando a barra de ferramentas do Slideshow célula:

1. Sobre o **vista** menu, selecione **barra de ferramentas de célula** > **Slideshow**:

    ![Ativar a barra de ferramentas de apresentação de slides da célula](media/slideshow/slideshow-view-cell-toolbar.png)

1. R **deslize tipo** pendente é apresentado no canto superior direito de cada célula no bloco de notas:

    ![Barra de ferramentas de apresentação de slides de célula](media/slideshow/slideshow-cell-toolbar.png)

1. Para cada célula, selecione uma das cinco tipos:

    ![Tipos de apresentação de slides de célula](media/slideshow/slideshow-cell-slide-types.png)

    | Tipo de slide | Comportamento |
    | --- | --- |
    | -(não definido) | Célula é apresentada com a célula anterior, o que é muitas vezes não um efeito desejado numa apresentação de diapositivos. |
    | Diapositivo | A célula é um slide primário, navegado com as setas do esquerda e direita do controle de navegação. |
    | Slide secundárias | Célula é "inferior" um slide primário, navegado para utilizar a seta para baixo do controle de navegação. Devolve a seta para cima ao diapositivo principal. Slides secundárias são utilizados para secundário material que poderiam Pular no caminho do principal de uma apresentação, mas está prontamente disponível caso seja necessário. |
    | Fragmento | Conteúdo da célula é apresentado no contexto do slide anterior ou slide secundárias ao utilizar a seta de navegação baixo (um fragmento é removido quando utilizar a seta para cima). Pode utilizar um fragmento com uma célula de código para tornar esse código aparecem dentro de um slide, ou pode usar vários fragmentos para tornar o texto marcadores são apresentados um a um (veja o exemplo na secção seguinte). Uma vez que criar fragmentos no slide atual, fragmentos em excesso não será visíveis desativado na parte inferior da janela do navegador. |
    | Ignorar | Célula não é mostrada na apresentação de slides. |
    | Notas | Célula contém como anotações do orador, que não são mostradas na apresentação de slides. |

1. Inicialmente, é útil escolher **deslize** para cada célula. Em seguida, pode executar a apresentação de slides e efetue os ajustes adequados.

### <a name="example-fragment-cells-for-bullet-items"></a>Exemplo: fragmento células de itens de marca de lista

Para tornar marcadores num slide aparecer uma ao local de um, o cabeçalho de slides numa célula de Markdown com o **Slide** escreva, em seguida, coloque cada item numa célula separada de Markdown com o **fragmento** tipo:

![Exemplo de criação de várias células de Markdown para itens de marca de lista](media/slideshow/slideshow-fragments.png)

Uma vez que a apresentação de slides renderiza fragmentos com mais vertical espaçamento do que quando todos os marcadores estão na mesma célula, poderá não conseguir utilizar a mesma quantidade de itens de marca de lista.

## <a name="run-the-slideshow"></a>Executar a apresentação de slides

1. Se tiver editado células de Markdown, certifique-se para executá-los para renderizar o HTML, caso contrário, eles aparecem *como* Markdown na apresentação de slides.

1. Assim que tiver configurado a **tipo de deslize** para cada célula, selecionar a célula com a qual iniciar a apresentação de slides, em seguida, selecione o **apresentação de slides de aumentar Enter/saída** botão na barra de ferramentas principal:

    ![Apresentação de slides de aumentar entrar/sair do botão na barra de ferramentas principal](media/slideshow/slideshow-start.png)

1. Para navegar entre slides, bem como fragmentos, use as setas do esquerda e direita no controle de navegação. O texto no controle mostra um número que representa *slide.sub slide*.

    ![Controle de navegação de apresentação de slides](media/slideshow/slideshow-navigation-control.png)

1. Para navegar entre slides e secundárias slides, bem como fragmentos, utilize a cópia de segurança e para baixo setas, se estiver ativada:

    ![Controles de navegação de apresentação de slides para slides secundárias](media/slideshow/slideshow-navigation-control-subslide.png)

1. Numa célula de código, utilize o botão Reproduzir para executar o código; saída será no slide:

    ![Botão Reproduzir para executar uma célula de código](media/slideshow/slideshow-run-code-cell.png)

    ![Saída de célula de código é apresentado na apresentação de diapositivos](media/slideshow/slideshow-run-code-cell-output.png)

    > [!Tip]
    > Saída de célula é considerada parte da célula numa apresentação de diapositivos. Se executar uma célula no bloco de notas ou exibição de apresentação de slides, o resultado é apresentado na outra vista também. Para limpar a saída, utilize o **célula** > **saídas atual** > **limpar** comando (para a célula atual) ou **célula**  >  **Todos os resultados** > **clara** (para todas as células).

1. Quando tiver terminado com a apresentação de slides, utilize o **X** para regressar à vista de bloco de notas.

## <a name="next-steps"></a>Passos Seguintes

- [How to: Configurar e gerir projetos](configure-manage-azure-notebooks-projects.md)
- [How to: Instalar pacotes a partir de um bloco de notas](install-packages-jupyter-notebook.md)
- [How to: Trabalhar com arquivos de dados](work-with-project-data-files.md)
- [How to: Aceder a recursos de dados](access-data-resources-jupyter-notebooks.md)
