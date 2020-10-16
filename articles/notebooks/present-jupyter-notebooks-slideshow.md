---
title: Apresente um caderno Jupyter como uma apresentação de diapositivos na pré-visualização de cadernos Azure
description: Aprenda a configurar as células para o modo slideshow num caderno Jupyter e, em seguida, apresente a apresentação de diapositivos utilizando a extensão RISE.
ms.topic: how-to
ms.date: 12/04/2018
ms.openlocfilehash: 2fe337361436ecfc8eabf2855ad633b891db69d8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85834051"
---
# <a name="run-a-notebook-slideshow-in-azure-notebooks-preview"></a>Executar uma apresentação de slides de caderno em Azure Notebooks Preview

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

Os Cadernos Azure estão pré-configurados com a Extensão de Apresentação de Diapositivos Jupyter/IPython (RISE) que permite apresentar um caderno diretamente como uma apresentação de diapositivos. Numa apresentação de diapositivos, as células são normalmente exibidas uma de cada vez usando um tamanho de fonte apropriado para apresentar em ecrãs grandes, e ainda pode executar o código em vez de mudar para um computador de demonstração separado.

A imagem a seguir mostra a vista padrão do caderno, na qual pode ver as células markdown e código todas juntas:

![Um caderno na vista padrão](media/slideshow/slideshow-notebook-view.png)

Quando se inicia uma apresentação de diapositivos, a primeira célula é ampliada para preencher o navegador, onde o **X** na parte superior esquerda sai da apresentação de diapositivos, **?** no inferior esquerdo exibe atalhos de teclado e as setas na parte inferior direita navegam entre slides:

![Um caderno no modo slideshow](media/slideshow/slideshow-slide-view.png)

Preparar um caderno para uma apresentação de diapositivos envolve duas atividades primárias:

1. Como as células Markdown são renderizadas com grandes fontes, alguns conteúdos podem não ser visíveis na apresentação de diapositivos. Assim, normalmente, limita-se a quantidade de texto em qualquer célula; um cabeçalho com quatro a seis linhas normalmente funciona melhor. Se tiver mais texto, divida essa informação em várias células.

2. Configure o comportamento de cada célula na apresentação de diapositivos utilizando a barra de ferramentas da célula slideshow. Os tipos de células determinam o comportamento dos botões de navegação.

## <a name="the-anatomy-of-a-slideshow"></a>A anatomia de uma apresentação de diapositivos

Se pegar num caderno aleatório e usá-lo para uma apresentação de diapositivos, normalmente descobre que todas as células estão misturadas, e grande parte do conteúdo está escondido na parte inferior da janela do navegador. Para fazer uma apresentação eficaz, então, você precisa atribuir um tipo de apresentação de diapositivos a cada célula usando a barra de ferramentas da célula Slideshow:

1. No menu **Ver,** selecione **Cell Toolbar**  >  **Slideshow**:

    ![Ligar a barra de ferramentas slideshow celular](media/slideshow/slideshow-view-cell-toolbar.png)

1. Aparece no canto superior direito de cada célula um **deslize** no canto superior direito de cada célula:

    ![Barra de ferramentas de slideshow de células](media/slideshow/slideshow-cell-toolbar.png)

1. Para cada célula, selecione um de cinco tipos:

    ![Tipos de apresentação de diapositivos de células](media/slideshow/slideshow-cell-slide-types.png)

    | Tipo de diapositivos | Comportamento |
    | --- | --- |
    | - (não definido) | A célula é exibida com a célula anterior, que muitas vezes não é um efeito desejado numa apresentação de diapositivos. |
    | Slide | A célula é um slide primário, navegado usando as setas esquerda e direita do controlo de navegação. |
    | Sub-slide | A célula é "abaixo" de um slide primário, navegado para usar a seta para baixo do controlo de navegação. A seta para cima volta ao slide primário. Sub-slides são usados para material secundário que você pode saltar no caminho principal de uma apresentação, mas está prontamente disponível se necessário. |
    | Fragment | O conteúdo celular aparece no contexto do diapositivo ou sub-diapositivo anterior ao utilizar a seta de navegação para baixo (um fragmento é removido quando se utiliza a seta para cima). Pode utilizar um fragmento com uma célula de código para que esse código apareça dentro de uma lâmina, ou pode utilizar vários fragmentos para fazer com que as balas de texto apareçam uma a uma (ver exemplo na secção seguinte). Como os fragmentos se constroem na lâmina atual, os fragmentos em excesso não serão visíveis na parte inferior da janela do navegador. |
    | Ignorar | A célula não é mostrada na apresentação de diapositivos. |
    | Notas | A célula contém como notas de altifalante, que não são mostradas na apresentação de diapositivos. |

1. Inicialmente, é útil escolher **slide** para cada célula. Em seguida, pode executar a apresentação de diapositivos e fazer os ajustes adequados.

### <a name="example-fragment-cells-for-bullet-items"></a>Exemplo: células de fragmento para objetos de bala

Para fazer aparecer balas num slide, coloque o cabeçalho de diapositivos numa célula Markdown com o tipo **Slide** e, em seguida, coloque cada bala numa célula de Markdown separada com o tipo **Fragmento:**

![Exemplo de criação de várias células Markdown para itens de bala](media/slideshow/slideshow-fragments.png)

Como a apresentação de diapositivos torna fragmentos com mais espaçamento vertical do que quando todas as balas estão na mesma célula, pode não ser capaz de usar tantos itens de bala.

## <a name="run-the-slideshow"></a>Executar a apresentação de diapositivos

1. Se editou alguma célula Markdown, certifique-se de executá-las para tornar o seu HTML, caso contrário, elas aparecem *como* Markdown na apresentação de diapositivos.

1. Depois de configurar o **Tipo de Diapositivos** para cada célula, selecione a célula para iniciar a apresentação de diapositivos e, em seguida, selecione o botão **Desaboso Enter/Exit RISE** na barra de ferramentas principal:

    ![Introduzir/Sair SUBIR Slideshow botão na barra de ferramentas principal](media/slideshow/slideshow-start.png)

1. Para navegar entre slides e fragmentos, utilize as setas esquerda e direita no controlo de navegação. O texto no controlo mostra um número que representa *o diapositivo.sub-slide*.

    ![Controlo de navegação slideshow](media/slideshow/slideshow-navigation-control.png)

1. Para navegar entre slides e sub-slides, bem como fragmentos, utilize as setas para cima e para baixo, se ativadas:

    ![Controlos de navegação slideshow para sub-slides](media/slideshow/slideshow-navigation-control-subslide.png)

1. Numa célula de código, utilize o botão de reprodução para executar o código; saída aparece no slide:

    ![Botão de jogo para executar uma célula de código](media/slideshow/slideshow-run-code-cell.png)

    ![A saída de células de código aparece na apresentação de diapositivos](media/slideshow/slideshow-run-code-cell-output.png)

    > [!Tip]
    > A saída celular é considerada parte da célula numa apresentação de diapositivos. Se executar uma célula na visualização do caderno ou do slide, a saída também aparece na outra vista. Para limpar a saída, utilize o comando **Cell**  >  **Desimpusão as saídas de corrente**celular  >  **Clear** (para a célula atual) ou todas as saídas de **células**  >  (para todas as**células).**  >  **Clear**

1. Quando terminar a apresentação de diapositivos, use o **X** para voltar à vista do caderno.

## <a name="next-steps"></a>Passos seguintes

- [Como: Configurar e gerir projetos](configure-manage-azure-notebooks-projects.md)
- [Como: Instalar pacotes a partir de um caderno](install-packages-jupyter-notebook.md)
- [Como: Trabalhar com ficheiros de dados](work-with-project-data-files.md)
- [Como: Aceder aos recursos de dados](access-data-resources-jupyter-notebooks.md)
