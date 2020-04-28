---
title: Visão geral da ferramenta de modelação de ameaças da Microsoft - Azure
description: Conheça todas as funcionalidades disponíveis na Ferramenta de Modelação de Ameaças
author: jegeib
ms.author: jegeib
ms.service: security
ms.subservice: security-develop
ms.topic: article
ms.date: 08/17/2017
ms.openlocfilehash: 88eea0eb2bceb0f6322381a94d4339cbdfa662c0
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75552122"
---
# <a name="threat-modeling-tool-feature-overview"></a>Visão geral da ferramenta de modelação de ameaças

A Ferramenta de Modelação de Ameaças pode ajudá-lo com as suas necessidades de modelação de ameaças. Para uma introdução básica à ferramenta, consulte Começar com a Ferramenta de [Modelação de Ameaças](threat-modeling-tool-getting-started.md).

> [!NOTE]
>A Ferramenta de Modelação de Ameaças é atualizada com frequência, por isso consulte este guia muitas vezes para ver as nossas últimas funcionalidades e melhorias.

Para abrir uma página em branco, selecione **Criar um Modelo**.

![Página em branco](./media/threat-modeling-tool-feature-overview/tmtstart.png)

Para ver as funcionalidades atualmente disponíveis na ferramenta, utilize o modelo de ameaça criado pela nossa equipa no exemplo [get started.](threat-modeling-tool-getting-started.md)

![Modelo de ameaça básica](./media/threat-modeling-tool-feature-overview/basictmt.png)

## <a name="navigation"></a>Navegação

Antes de discutirmos as funcionalidades incorporadas, vamos rever os componentes principais encontrados na ferramenta.

### <a name="menu-items"></a>Itens de menu

A experiência é semelhante a outros produtos da Microsoft. Vamos rever os itens de menu de alto nível.

![Itens de menu](./media/threat-modeling-tool-feature-overview/menuitems.png)

| Etiqueta                               | Detalhes      |
| --------------------------------------- | ------------ |
| **Ficheiro** | <ul><li>Abrir, guardar e fechar ficheiros</li><li>Inscreva-se e assine as contas do OneDrive.</li><li>Partilhar links (ver e editar).</li><li>Ver informações sobre ficheiros.</li><li>Aplique um novo modelo nos modelos existentes.</li></ul> |
| **Editar** | Desfazer e refazer ações, bem como copiar, colar e apagar. |
| **Ver** | <ul><li>Alternaentre as vistas **de Análise** e **Design.**</li><li>Abrir janelas fechadas (por exemplo, stencils, propriedades de elementos e mensagens).</li><li>Redefinir o layout para as definições predefinidas.</li></ul> |
| **Diagrama** | Adicione e elimine os diagramas e mova-se através de separadores de diagramas. |
| **Relatórios** | Crie relatórios HTML para partilhar com os outros. |
| **Help** | Encontre guias para o ajudar a utilizar a ferramenta. |

Os símbolos são atalhos para os menus de alto nível:

| Símbolo                               | Detalhes      |
| --------------------------------------- | ------------ |
| **Abrir** | Abre um novo ficheiro. |
| **Guardar** | Guarda o ficheiro atual. |
| **Design** | Abre a vista **Design,** onde pode criar modelos. |
| **Analisar** | Mostra ameaças geradas e suas propriedades. |
| **Adicionar diagrama** | Adiciona um novo diagrama (semelhante a novos separadores no Excel). |
| **Eliminar diagrama** | Elimina o diagrama atual. |
| **Cópia/Corte/Pasta** | Cópias, cortes e elementos de colares. |
| **Desfazer/Redo** | Desfaz e refaz ações. |
| **Zoom dentro/Zoom para fora** | Zooms dentro e fora do diagrama para uma melhor vista. |
| **Feedback** | Abre o Fórum MSDN. |

### <a name="canvas"></a>Telas

A tela é o espaço onde se arrasta mato e larga elementos. Arrastar e largar é a forma mais rápida e eficiente de construir modelos. Também pode clicar à direita e selecionar itens do menu para adicionar versões genéricas de elementos, como mostrado:

#### <a name="drop-the-stencil-on-the-canvas"></a>Largue o stencil na tela

![Gota de lona](./media/threat-modeling-tool-feature-overview/canvasdrop1.png)

#### <a name="select-the-stencil"></a>Selecione o stencil

![Propriedades do elemento](./media/threat-modeling-tool-feature-overview/canvasdrop2.png)

### <a name="stencils"></a>Stencils

Com base no modelo que selecionar, pode encontrar todos os stencils disponíveis para usar. Se não encontrar os elementos certos, use outro modelo. Ou pode modificar um modelo para se adaptar às suas necessidades. Geralmente, você pode encontrar uma combinação de categorias como estas:

| Nome stencil                               | Detalhes      |
| --------------------------------------- | ------------ |
| **Processo** | Aplicações, plug-ins de navegador, fios, máquinas virtuais |
| **Interagindo externo** | Fornecedores de autenticação, navegadores, utilizadores, aplicações web |
| **Arquivo de dados** | Cache, armazenamento, ficheiros de configuração, bases de dados, registo |
| **Fluxo de dados** | Binário, ALPC, HTTP, HTTPS/TLS/SSL, IOCTL, IPSec, tubo nomeado, RPC/DCOM, SMB, UDP |
| **Linha de confiança/fronteira** | Redes corporativas, internet, máquina, caixa de areia, modo utilizador/kernel |

### <a name="notesmessages"></a>Notas/mensagens

| Componente                               | Detalhes      |
| --------------------------------------- | ------------ |
| **Mensagens** | Lógica interna da ferramenta que alerta os utilizadores sempre que há um erro, como nenhum fluxo de dados entre elementos. |
| **Notas** | As notas manuais são adicionadas ao ficheiro por equipas de engenharia durante todo o processo de conceção e revisão. |

### <a name="element-properties"></a>Propriedades do elemento

As propriedades do elemento variam pelos elementos que seleciona. Além dos limites de confiança, todos os outros elementos contêm três seleções gerais:

| Propriedade de elementos                               | Detalhes      |
| --------------------------------------- | ------------ |
| **Nome** | Útil para nomear os seus processos, lojas, interactors e fluxos para que sejam facilmente reconhecidos. |
| **Fora de âmbito** | Se selecionado, o elemento é retirado da matriz de geração de ameaças (não recomendado). |
| **Razão para fora de âmbito** | Campo de justificação para que os utilizadores saibam por que razão foi selecionado fora do âmbito. |

As propriedades são alteradas em cada categoria de elementos. Selecione cada elemento para inspecionar as opções disponíveis. Ou pode abrir o modelo para saber mais. Vamos rever as características.

## <a name="welcome-screen"></a>Ecrã de Boas-Vindas

Ao abrir a aplicação, vê o ecrã **Welcome.**

### <a name="open-a-model"></a>Abra um modelo

Paire sobre **um modelo aberto** para revelar duas opções: Open From This **Computer** e Open **From OneDrive**. A primeira opção abre o ecrã Aberto do **Ficheiro.** A segunda opção leva-o através do processo de entrada para o OneDrive. Após a autenticação bem sucedida, pode selecionar pastas e ficheiros.

![Modelo aberto](./media/threat-modeling-tool-feature-overview/openmodel.png)

![Aberto a partir de computador ou OneDrive](./media/threat-modeling-tool-feature-overview/openmodel2.png)

### <a name="feedback-suggestions-and-issues"></a>Feedback, sugestões e questões

Quando selecionar **Feedback, Sugestões e Problemas,** vá ao Fórum MSDN para ferramentas SDL. Pode ler o que as outras pessoas estão a dizer sobre a ferramenta, incluindo sobras e novas ideias.

![Comentários](./media/threat-modeling-tool-feature-overview/feedback.png)

## <a name="design-view"></a>Vista de design

Quando abre ou cria um novo modelo, abre-se a vista **Design.**

### <a name="add-elements"></a>Adicionar elementos

Pode adicionar elementos na grelha de duas formas:

- **Arrastar e largar**: Arraste o elemento desejado para a grelha. Em seguida, use as propriedades do elemento para fornecer informações adicionais.
- **Clique à direita**: Clique à direita em qualquer lugar da grelha e selecione itens do menu suspenso. Uma representação genérica do elemento que selecionar aparece no ecrã.

### <a name="connect-elements"></a>Ligar elementos

Pode ligar elementos de duas formas:

- **Arrastar e largar**: Arraste o fluxo de dados desejado para a rede e ligue ambas as extremidades aos elementos apropriados.
- **Clique + Shift**: Clique no primeiro elemento (dados de envio), prima e mantenha a tecla Shift e, em seguida, selecione o segundo elemento (dados recetores). Clique à direita e selecione **Connect**. Se utilizar um fluxo de dados bidirecionais, a ordem não é tão importante.

### <a name="properties"></a>Propriedades

 Para ver as propriedades que podem ser modificadas nos stencils, selecione o stencil e a informação povoa em conformidade. O exemplo que se segue mostra antes e depois de um stencil de base de **dados** ser arrastado para o diagrama:

#### <a name="before"></a>Antes

![Antes](./media/threat-modeling-tool-feature-overview/properties1.png)

#### <a name="after"></a>Depois

![Depois](./media/threat-modeling-tool-feature-overview/properties2.png)

### <a name="messages"></a>Mensagens

Se criar um modelo de ameaça e se esquecer de ligar fluxos de dados a elementos, recebe uma notificação. Pode ignorar a mensagem ou seguir as instruções para corrigir o problema. 

![Mensagens](./media/threat-modeling-tool-feature-overview/messages.png)

### <a name="notes"></a>Notas

Para adicionar notas ao seu diagrama, mude do separador **Mensagens** para o separador **Notas.**

## <a name="analysis-view"></a>Vista de análise

Depois de construir o seu diagrama, selecione o símbolo **de análise** (a lupa) na barra de ferramentas de atalhos para mudar para a vista **Análise.**

![Vista de análise](./media/threat-modeling-tool-feature-overview/analysisview.png)

### <a name="generated-threat-selection"></a>Seleção de ameaças gerada

Quando selecionar uma ameaça, pode utilizar três funções distintas:

| Funcionalidade                               | Informações      |
| --------------------------------------- | ------------ |
| **Ler indicador** | <p>A ameaça está marcada como lida, o que o ajuda a acompanhar os itens que reviu.</p><p>![Indicador de leitura/não leitura](./media/threat-modeling-tool-feature-overview/readmode.png)</p> |
| **Foco de interação** | <p>A interação no diagrama que pertence a uma ameaça é destacada.</p><p>![Foco de interação](./media/threat-modeling-tool-feature-overview/interactionfocus.png)</p> |
| **Propriedades de ameaça** | <p>Informações adicionais sobre a ameaça aparecem na janela **Ameaças Propriedades.**</p><p>![Propriedades de ameaça](./media/threat-modeling-tool-feature-overview/threatproperties.png)</p> |

### <a name="priority-change"></a>Mudança prioritária

Pode alterar o nível prioritário de cada ameaça gerada. Cores diferentes facilitam a identificação de ameaças de alta, média e baixa prioridade.

![Mudança prioritária](./media/threat-modeling-tool-feature-overview/prioritychange.png)

### <a name="threat-properties-editable-fields"></a>Propriedades de ameaça campos editáveis

Como se pode ver na imagem anterior, pode alterar a informação gerada pela ferramenta. Também pode adicionar informações a determinados campos, como justificação. Estes campos são gerados pelo modelo. Se precisar de mais informações para cada ameaça, pode fazer modificações.

![Propriedades de ameaça](./media/threat-modeling-tool-feature-overview/threatproperties.png)

## <a name="reports"></a>Relatórios

Depois de terminar a mudança de prioridades e atualizar o estado de cada ameaça gerada, pode guardar o ficheiro e/ou imprimir um relatório. Vá ao **relatório** > **Criar Relatório Completo**. Diga o nome do relatório e deve ver algo semelhante à seguinte imagem:

![Relatório](./media/threat-modeling-tool-feature-overview/report.png)

## <a name="next-steps"></a>Passos seguintes

- Envie as suas perguntas, comentários tmtextsupport@microsoft.come preocupações para . **[Descarregue](https://aka.ms/threatmodelingtool)** a Ferramenta de Modelação de Ameaças para começar.
- Para contribuir com um modelo para a comunidade, vá à nossa página [gitHub.](https://github.com/Microsoft/threat-modeling-templates)