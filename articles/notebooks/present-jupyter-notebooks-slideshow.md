---
title: Apresente um caderno Jupyter como uma apresentação de diapositivos na Pré-visualização de cadernos Azure
description: Aprenda a configurar as células para o modo slideshow num portátil Jupyter e, em seguida, apresentar a apresentação de diapositivos utilizando a extensão RISE.
ms.topic: how-to
ms.date: 12/04/2018
ms.openlocfilehash: 05dd3d9c5580e208ecf6f9e6d762476b0b493a6c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75647123"
---
# <a name="run-a-notebook-slideshow-in-azure-notebooks-preview"></a>Executar um slideshow de cadernos em Pré-visualização de cadernos Azure

Os Cadernos Azure são pré-configurados com a Extensão de Apresentação de Slides Jupyter/IPython (RISE) que lhe permite apresentar um caderno diretamente como uma apresentação de diapositivos. Numa apresentação de diapositivos, as células são normalmente exibidas uma de cada vez usando um tamanho de fonte adequado para apresentar em ecrãs grandes, e você ainda pode executar o código em vez de mudar para um computador de demonstração separado.

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

A imagem que se segue mostra a vista padrão do caderno, na qual pode ver as células Markdown e code all together:

![Um caderno na vista padrão](media/slideshow/slideshow-notebook-view.png)

Quando inicia uma apresentação de diapositivos, a primeira célula é ampliada para preencher o navegador, onde o **X** na parte superior esquerda sai da apresentação de diapositivos, **?** na parte inferior esquerda exibe atalhos de teclado, e as setas na parte inferior direita navegam entre slides:

![Um caderno no modo slideshow](media/slideshow/slideshow-slide-view.png)

A preparação de um caderno para uma apresentação de diapositivos envolve duas atividades primárias:

1. Como as células Markdown são renderizadas com fontes grandes, alguns conteúdos podem não ser visíveis na apresentação de diapositivos. Assim, normalmente limita a quantidade de texto em qualquer célula; um cabeçalho com quatro a seis linhas geralmente funciona melhor. Se tiver mais texto, divida essa informação em várias células.

2. Configure o comportamento de cada célula na apresentação de diapositivos utilizando a barra de ferramentas de apresentação de diapositivos. Os tipos de células determinam o comportamento dos botões de navegação.

## <a name="the-anatomy-of-a-slideshow"></a>A anatomia de uma apresentação de diapositivos

Se pegar num caderno aleatório e usá-lo para uma apresentação de diapositivos, normalmente descobre que todas as células estão juntas, e grande parte do conteúdo está escondido na parte inferior da janela do navegador. Para fazer uma apresentação eficaz, então, você precisa atribuir um tipo de slideshow a cada célula usando a barra de ferramentas de célulaslideshow:

1. No menu **'Ver',** selecione Apresentação > **de Diapositivos**da **Barra de Ferramentas celulares:**

    ![Ligar a barra de ferramentas de apresentação de diapositivos da célula](media/slideshow/slideshow-view-cell-toolbar.png)

1. Uma queda **do tipo de diapositivo** aparece na parte superior direita de cada célula no Caderno:

    ![Barra de ferramentas de apresentação de diapositivos celular](media/slideshow/slideshow-cell-toolbar.png)

1. Para cada célula, selecione um de cinco tipos:

    ![Tipos de slideshow celular](media/slideshow/slideshow-cell-slide-types.png)

    | Tipo de diapositivo | Comportamento |
    | --- | --- |
    | - (não definido) | A célula é exibida com a célula anterior, que muitas vezes não é um efeito desejado numa apresentação de diapositivos. |
    | Slide | A célula é um slide primário, navegado com as setas esquerda e direita do controlo de navegação. |
    | Sub-slide | A célula é "abaixo" de um slide primário, navegado para usar a seta para baixo do controlo de navegação. A seta para cima volta ao slide primário. Os sub-slides são utilizados para material secundário que pode saltar no caminho principal de uma apresentação, mas está prontamente disponível se necessário. |
    | Fragment | O conteúdo celular aparece no contexto do slide ou sub-slide anterior esquelético quando se utiliza a seta de navegação para baixo (um fragmento é removido quando se utiliza a seta para cima). Pode utilizar um fragmento com uma célula de código para fazer com que esse código apareça dentro de um slide, ou pode utilizar vários fragmentos para fazer com que as balas de texto apareçam uma a uma (ver exemplo na secção seguinte). Como os fragmentos se baseiam no slide atual, os fragmentos em excesso não serão visíveis na parte inferior da janela do navegador. |
    | Ignorar | A célula não é mostrada na apresentação de diapositivos. |
    | Notas | A célula contém notas de altifalante, que não são mostradas na apresentação de diapositivos. |

1. Inicialmente, é útil escolher **slide** para cada célula. Em seguida, pode executar a apresentação de diapositivos e fazer os ajustes adequados.

### <a name="example-fragment-cells-for-bullet-items"></a>Exemplo: células de fragmento para objetos de bala

Para fazer com que as balas num slide apareçam uma a uma, coloque o cabeçalho de diapositivonuma célula de Marcação com o tipo **Slide** e, em seguida, coloque cada bala numa célula de Markdown separada com o tipo **Fragment:**

![Exemplo de criar várias células Markdown para itens de bala](media/slideshow/slideshow-fragments.png)

Como a apresentação de diapositivos torna fragmentos com mais espaçamento vertical do que quando todas as balas estão na mesma célula, pode não ser capaz de usar tantos objetos de bala.

## <a name="run-the-slideshow"></a>Executar a apresentação de diapositivos

1. Se editou quaisquer células Markdown, certifique-se de executá-las para renderizar o seu HTML, caso contrário aparecem *como* Markdown na apresentação de diapositivos.

1. Depois de configurar o **Tipo de Diapositivo** para cada célula, selecione a célula com a qual iniciar a apresentação de diapositivos, e, em seguida, selecione o botão 'Apresentação de **diapositivos 'Entrar/Saída RISE',** na barra principal de ferramentas:

    ![Botão de apresentação de diapositivos Enter/Exit RISE Na barra principal](media/slideshow/slideshow-start.png)

1. Para navegar entre slides e fragmentos, utilize as setas esquerda e direita no controlo de navegação. O texto no comando mostra um número que representa *slide.sub-slide*.

    ![Controlo de navegação slideshow](media/slideshow/slideshow-navigation-control.png)

1. Para navegar entre slides e sub-slides, bem como fragmentos, utilize as setas para cima e para baixo, se ativadas:

    ![Slideshow controlos de navegação para sub-slides](media/slideshow/slideshow-navigation-control-subslide.png)

1. Numa célula de código, utilize o botão de reprodução para executar o código; a saída aparece no slide:

    ![Reproduzir botão para executar uma célula de código](media/slideshow/slideshow-run-code-cell.png)

    ![A saída da célula de código aparece na apresentação de diapositivos](media/slideshow/slideshow-run-code-cell-output.png)

    > [!Tip]
    > A saída celular é considerada parte da célula numa apresentação de diapositivos. Se executar uma célula em qualquer portátil ou visualização de apresentação de diapositivos, a saída também aparece na outra vista. Para limpar a saída, utilize **o** > **Current Outputs** > comando**Desalvio** de Corrente celular (para a célula atual) ou **"Cell** > **All Outputs** > **Clear"** (para todas as células).

1. Quando terminar a apresentação de diapositivos, use o **X** para voltar à vista do caderno.

## <a name="next-steps"></a>Passos seguintes

- [Como: Configurar e gerir projetos](configure-manage-azure-notebooks-projects.md)
- [Como: Instalar pacotes dentro de um caderno](install-packages-jupyter-notebook.md)
- [Como: Trabalhar com ficheiros de dados](work-with-project-data-files.md)
- [Como: Aceder aos recursos de dados](access-data-resources-jupyter-notebooks.md)
