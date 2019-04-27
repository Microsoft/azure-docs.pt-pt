---
title: Ferramenta - Azure de modelagem de ameaças da Microsoft | Documentos da Microsoft
description: Saiba mais sobre todas as funcionalidades disponíveis na ferramenta de modelagem de ameaças
services: security
documentationcenter: na
author: jegeib
manager: jegeib
editor: jegeib
ms.assetid: na
ms.service: security
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/17/2017
ms.author: rodsan
ms.openlocfilehash: 601f3bf05388406c8f96a7351f7fb3aa4de2650a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60588546"
---
# <a name="threat-modeling-tool-feature-overview"></a>Descrição geral da funcionalidade de ferramenta de modelagem de ameaças

A ferramenta de modelagem de ameaças pode ajudá-lo com suas necessidades de modelagem de ameaças. Para obter uma introdução básica para a ferramenta, consulte [começar com a ferramenta de modelagem de ameaças](./azure-security-threat-modeling-tool-getting-started.md).

> [!NOTE]
>A ferramenta de modelagem de ameaças é atualizada frequentemente, por isso, verifique este guia, muitas vezes, para ver o nosso mais recentes funcionalidades e melhorias.

Para abrir uma página em branco, selecione **criar modelo de um**.

![Página em branco](./media/azure-security-threat-modeling-tool-feature-overview/tmtstart.png)

Para ver as funcionalidades atualmente disponíveis na ferramenta, utilize o modelo de ameaças criado pelo nossa equipe na [começar](./azure-security-threat-modeling-tool-getting-started.md) exemplo.

![Modelo de ameaças básico](./media/azure-security-threat-modeling-tool-feature-overview/basictmt.png)

## <a name="navigation"></a>Navegação

Antes de abordarmos as funcionalidades incorporadas, vamos rever os principais componentes encontrados na ferramenta.

### <a name="menu-items"></a>Itens de menu

A experiência é semelhante a outros produtos da Microsoft. Vamos rever os itens de menu de nível superior.

![Itens de menu](./media/azure-security-threat-modeling-tool-feature-overview/menuitems.png)

| Label                               | Detalhes      |
| --------------------------------------- | ------------ |
| **Ficheiro** | <ul><li>Abrir, guardar e fechar arquivos</li><li>Inicie sessão e terminar sessão em contas do OneDrive.</li><li>Partilhar ligações (ver e editar).</li><li>Ver informações de ficheiro.</li><li>Aplica um novo modelo para modelos existentes.</li></ul> |
| **Editar** | Desfazer e Refazer ações, bem como copiar, cola e eliminar. |
| **modo de exibição** | <ul><li>Alternar entre **Analysis** e **Design** vistas.</li><li>Fechadas as janelas abertas (por exemplo, estênceis, propriedades de elemento e mensagens).</li><li>Repor esquema para as predefinições.</li></ul> |
| **Diagrama** | Adicionar e eliminar os diagramas e mover através de guias de diagramas. |
| **Relatórios** | Crie relatórios HTML para partilhar com outras pessoas. |
| **Obter ajuda** | Encontre guias para ajudar a utilizar a ferramenta. |

Os símbolos são atalhos para os menus de nível superior:

| Símbolo                               | Detalhes      |
| --------------------------------------- | ------------ |
| **abrir** | É aberto um novo ficheiro. |
| **Guardar** | Guarda o ficheiro atual. |
| **Design** | Abre o **Design** vista, onde pode criar modelos. |
| **Analisar** | Mostra gerado ameaças e as respetivas propriedades. |
| **Adicionar o diagrama** | Adiciona um novo diagrama (semelhante a novas guias no Excel). |
| **Eliminar diagrama** | Elimina o diagrama atual. |
| **Cortar/copiar/colar** | Copia, reduz e cola elementos. |
| **Desfazer/Refazer** | Anula e redoes ações. |
| **Ampliar / reduzir** | Amplia dentro e fora o diagrama para obter uma exibição melhor. |
| **Comentários** | É aberto o Fórum do MSDN. |

### <a name="canvas"></a>Canvas

A tela é o espaço onde arrastar e soltar elementos. Arrastar e soltar é a forma mais rápida e mais eficiente de criar modelos. Pode também com o botão direito e selecionar itens do menu para adicionar versões genéricas dos elementos, conforme mostrado:

#### <a name="drop-the-stencil-on-the-canvas"></a>Remover o estêncil na tela

![Lista de tela](./media/azure-security-threat-modeling-tool-feature-overview/canvasdrop1.png)

#### <a name="select-the-stencil"></a>Selecione o estêncil

![Propriedades de elemento](./media/azure-security-threat-modeling-tool-feature-overview/canvasdrop2.png)

### <a name="stencils"></a>Estênceis

Com base no modelo que selecionar, pode encontrar todos os estênceis disponíveis para utilização. Se não conseguir encontrar os elementos corretos, utilize outro modelo. Ou pode modificar um modelo de acordo com as suas necessidades. Em geral, pode encontrar uma combinação de categorias, como estes:

| Nome do estêncil                               | Detalhes      |
| --------------------------------------- | ------------ |
| **Processo** | Aplicações, plug-ins do browser, threads, máquinas virtuais |
| **Interactor externo** | Provedores de autenticação, navegadores, os utilizadores, aplicações web |
| **Arquivo de dados** | Cache, armazenamento, ficheiros de configuração, bases de dados, registo |
| **Fluxo de dados** | Binário, ALPC, HTTP, HTTPS/TLS/SSL, IOCTL, IPSec, com o nome pipe, RPC/DCOM, SMB, UDP |
| **Limite de linha/limite de confiança** | Redes corporativas, a internet, a máquina, a sandbox, a modo kernel/utilizador |

### <a name="notesmessages"></a>Notas/mensagens

| Componente                               | Detalhes      |
| --------------------------------------- | ------------ |
| **Mensagens** | Lógica de ferramenta interna que os utilizadores de alertas, sempre que houver um erro, como não existem fluxos de dados entre elementos. |
| **Notas** | Notas de manuais são adicionadas ao ficheiro pelas equipas de engenharia em todo o design e processo de revisão. |

### <a name="element-properties"></a>Propriedades de elemento

Propriedades do elemento variam de acordo com os elementos que selecionar. Para além dos limites de fidedignidade, todos os outros elementos contêm três seleções gerais:

| Propriedade do elemento                               | Detalhes      |
| --------------------------------------- | ------------ |
| **Nome** | Útil para atribuir nomes aos seus processos, arquivos, interadores e fluxos, para que eles estão a ser reconhecidos facilmente. |
| **Fora do âmbito** | Se selecionado, o elemento será retirado da matriz de geração de ameaças (não recomendada). |
| **Motivo para fora do âmbito** | Campo de justificação para permitir aos utilizadores saber porquê fora do escopo foi selecionado. |

As propriedades são alteradas em cada categoria de elemento. Selecione cada elemento para inspecionar as opções disponíveis. Ou pode abrir o modelo para obter mais informações. Vamos analisar as funcionalidades.

## <a name="welcome-screen"></a>Ecrã de boas-vindas

Quando abre a aplicação, consulte a **bem-vindo** ecrã.

### <a name="open-a-model"></a>Abra um modelo

Coloque o cursor sobre **abra um modelo** para revelar as duas opções: **Abrir a partir do computador** e **abrir a partir do OneDrive**. Abre-a primeira opção se o **File Open** ecrã. A segunda opção leva-o no processo de início de sessão para o OneDrive. Após a autenticação com êxito, pode selecionar pastas e arquivos.

![Modelo aberto](./media/azure-security-threat-modeling-tool-feature-overview/openmodel.png)

![Abrir a partir do computador ou o OneDrive](./media/azure-security-threat-modeling-tool-feature-overview/openmodel2.png)

### <a name="feedback-suggestions-and-issues"></a>Comentários, sugestões e problemas

Quando seleciona **comentários, sugestões e problemas**, vá para o Fórum do MSDN para ferramentas de SDL. É possível ler o que outras pessoas estão dizendo sobre a ferramenta, incluindo soluções alternativas e novas ideias.

![Comentários](./media/azure-security-threat-modeling-tool-feature-overview/feedback.png)

## <a name="design-view"></a>Vista de estrutura

Quando abrir ou criar um novo modelo, o **Design** ver abre-se.

### <a name="add-elements"></a>Adicionar elementos

Pode adicionar elementos na grade de duas formas:

- **Arraste e largue**: Arraste o elemento desejado para a grade. Em seguida, utilize as propriedades do elemento para fornecer informações adicionais.
- **Com o botão direito**: Com o botão direito em qualquer lugar na grade e selecione os itens no menu pendente. Será apresentada uma representação genérica do elemento que seleciona na tela.

### <a name="connect-elements"></a>Conectar elementos

Pode ligar-se de elementos de duas formas:

- **Arraste e largue**: Arraste o fluxo de dados pretendido para a grade e ligue ambas as extremidades para os elementos apropriados.
- **Clique em + Shift**: Clique no primeiro elemento (enviar dados), prima e mantenha premida a tecla Shift e, em seguida, selecione o segundo elemento (receber dados). Com o botão direito e selecione **Connect**. Se usar um fluxo de dados bidirecional, a ordem não é tão importante.

### <a name="properties"></a>Propriedades

 Para ver as propriedades que podem ser modificadas no estênceis, selecionadas o estêncil e as informações preenche em conformidade. O exemplo seguinte mostra antes e depois uma **base de dados** estêncil é arrastado para o diagrama:

#### <a name="before"></a>Antes

![Antes](./media/azure-security-threat-modeling-tool-feature-overview/properties1.png)

#### <a name="after"></a>Após

![Após](./media/azure-security-threat-modeling-tool-feature-overview/properties2.png)

### <a name="messages"></a>Mensagens

Se criar um modelo de ameaças e se esqueça de ligar fluxos de dados para elementos, receberá uma notificação. Pode ignorar a mensagem ou, pode seguir as instruções para corrigir o problema. 

![Mensagens](./media/azure-security-threat-modeling-tool-feature-overview/messages.png)

### <a name="notes"></a>Notas

Para adicionar notas para seu diagrama, alternar do **mensagens** separador para o **notas** separador.

## <a name="analysis-view"></a>Ver análise

Depois de criar o seu diagrama, selecione o **Analysis** símbolo (Lupa) na barra de ferramentas de atalhos para mudar para o **Analysis** vista.

![Ver análise](./media/azure-security-threat-modeling-tool-feature-overview/analysisview.png)

### <a name="generated-threat-selection"></a>Seleção de ameaças gerado

Quando seleciona uma ameaça, pode usar três funções distintas:

| Funcionalidade                               | Informações      |
| --------------------------------------- | ------------ |
| **Indicador de leitura** | <p>A ameaça é marcada como leitura, o que o ajuda a manter o controle de itens revisto.</p><p>![Indicador lido/não lido](./media/azure-security-threat-modeling-tool-feature-overview/readmode.png)</p> |
| **Foco de interação** | <p>Interação no diagrama que pertence a uma ameaça é realçada.</p><p>![Foco de interação](./media/azure-security-threat-modeling-tool-feature-overview/interactionfocus.png)</p> |
| **Propriedades de ameaças** | <p>Informações adicionais sobre a threat aparecem no **propriedades de ameaças** janela.</p><p>![Propriedades de ameaças](./media/azure-security-threat-modeling-tool-feature-overview/threatproperties.png)</p> |

### <a name="priority-change"></a>Alteração de prioridade

Pode alterar o nível de prioridade de cada ameaça gerado. Cores diferentes tornam mais fácil identificar alta - medium- e ameaças de baixa prioridade.

![Alteração de prioridade](./media/azure-security-threat-modeling-tool-feature-overview/prioritychange.png)

### <a name="threat-properties-editable-fields"></a>Campos de editáveis de propriedades de ameaças

Conforme mostrado na imagem anterior, pode alterar as informações geradas pela ferramenta. Também pode adicionar informações a determinados campos, tais como de justificação. Estes campos são gerados pelo modelo. Se precisar de mais informações para cada ameaça, pode fazer modificações.

![Propriedades de ameaças](./media/azure-security-threat-modeling-tool-feature-overview/threatproperties.png)

## <a name="reports"></a>Relatórios

Depois de concluir a alterar as prioridades de e para atualizar o status de cada ameaça gerado, pode salvar o arquivo e/ou imprimir um relatório. Aceda a **relatório** > **criar relatório completo**. Dê um nome do relatório e deverá ver algo semelhante à imagem seguinte:

![Relatório](./media/azure-security-threat-modeling-tool-feature-overview/report.png)

## <a name="next-steps"></a>Passos Seguintes

- Envie suas dúvidas, comentários e preocupações para tmtextsupport@microsoft.com. **[Baixe](https://aka.ms/threatmodelingtool)**  a ferramenta de modelagem de ameaças para começar a utilizar.
- Para contribuir com um modelo para a Comunidade, aceda ao nosso [GitHub](https://github.com/Microsoft/threat-modeling-templates) página.