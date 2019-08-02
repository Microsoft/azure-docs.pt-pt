---
title: Microsoft Threat Modeling Tool – Azure | Microsoft Docs
description: Saiba mais sobre todos os recursos disponíveis no Threat Modeling Tool
services: security
documentationcenter: na
author: jegeib
manager: jegeib
editor: jegeib
ms.assetid: na
ms.service: security
ms.subservice: security-develop
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/17/2017
ms.author: rodsan
ms.openlocfilehash: e3c6769cc832a897cfbab5548de5328d743884b2
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/01/2019
ms.locfileid: "68728603"
---
# <a name="threat-modeling-tool-feature-overview"></a>Visão geral do recurso de Threat Modeling Tool

O Threat Modeling Tool pode ajudá-lo com suas necessidades de modelagem de risco. Para obter uma introdução básica à ferramenta, consulte Introdução ao [Threat Modeling Tool](threat-modeling-tool-getting-started.md).

> [!NOTE]
>A Threat Modeling Tool é atualizada com frequência, portanto, verifique este guia com frequência para ver nossos recursos e aprimoramentos mais recentes.

Para abrir uma página em branco, selecione **criar um modelo**.

![Página em branco](./media/threat-modeling-tool-feature-overview/tmtstart.png)

Para ver os recursos disponíveis atualmente na ferramenta, use o modelo de ameaça criado por nossa equipe [no exemplo de](threat-modeling-tool-getting-started.md) introdução.

![Modelo básico de ameaças](./media/threat-modeling-tool-feature-overview/basictmt.png)

## <a name="navigation"></a>Navegação

Antes de discutirmos os recursos internos, vamos examinar os principais componentes encontrados na ferramenta.

### <a name="menu-items"></a>Itens de menu

A experiência é semelhante a outros produtos da Microsoft. Vamos examinar os itens de menu de nível superior.

![Itens de menu](./media/threat-modeling-tool-feature-overview/menuitems.png)

| Etiqueta                               | Detalhes      |
| --------------------------------------- | ------------ |
| **Ficheiro** | <ul><li>Abrir, salvar e fechar arquivos</li><li>Entre e saia das contas do OneDrive.</li><li>Compartilhar links (exibir e editar).</li><li>Exibir informações do arquivo.</li><li>Aplique um novo modelo a modelos existentes.</li></ul> |
| **Editar** | Desfazer e refazer ações, bem como copiar, colar e excluir. |
| **Exibição** | <ul><li>Alternar entre as exibições de **análise** e **design** .</li><li>Abra janelas fechadas (por exemplo, estênceis, propriedades do elemento e mensagens).</li><li>Redefina o layout para as configurações padrão.</li></ul> |
| **Organograma** | Adicionar e excluir diagramas e percorrer guias de diagramas. |
| **Relatórios** | Crie relatórios HTML para compartilhar com outras pessoas. |
| **Ajuda** | Encontre guias para ajudá-lo a usar a ferramenta. |

Os símbolos são atalhos para os menus de nível superior:

| Símbolo                               | Detalhes      |
| --------------------------------------- | ------------ |
| **Abrir** | Abre um novo arquivo. |
| **Guarde** | Salva o arquivo atual. |
| **Desenvolver** | Abre o modo de exibição de **design** , no qual você pode criar modelos. |
| **Analisar** | Mostra as ameaças geradas e suas propriedades. |
| **Adicionar diagrama** | Adiciona um novo diagrama (semelhante a novas guias no Excel). |
| **Excluir diagrama** | Exclui o diagrama atual. |
| **Copiar/recortar/colar** | Copia, corta e cola elementos. |
| **Desfazer/refazer** | Desfaz e reexecuta ações. |
| **Ampliar/reduzir** | Amplia e reduz o diagrama para uma exibição melhor. |
| **Comentários** | Abre o fórum do MSDN. |

### <a name="canvas"></a>Canvas

A tela é o espaço onde você arrasta e solta elementos. Arrastar e soltar é a maneira mais rápida e eficiente de criar modelos. Você também pode clicar com o botão direito do mouse e selecionar itens no menu para adicionar versões genéricas de elementos, conforme mostrado:

#### <a name="drop-the-stencil-on-the-canvas"></a>Soltar o estêncil na tela

![Descartar tela](./media/threat-modeling-tool-feature-overview/canvasdrop1.png)

#### <a name="select-the-stencil"></a>Selecionar o estêncil

![Propriedades do elemento](./media/threat-modeling-tool-feature-overview/canvasdrop2.png)

### <a name="stencils"></a>Estênceis

Com base no modelo selecionado, você pode encontrar todos os estênceis disponíveis para uso. Se você não encontrar os elementos certos, use outro modelo. Ou você pode modificar um modelo para atender às suas necessidades. Em geral, você pode encontrar uma combinação de categorias como estas:

| Nome do estêncil                               | Detalhes      |
| --------------------------------------- | ------------ |
| **Process** | Aplicativos, plug-ins de navegador, threads, máquinas virtuais |
| **Interator externo** | Provedores de autenticação, navegadores, usuários, aplicativos Web |
| **Armazenamento de dados** | Cache, armazenamento, arquivos de configuração, bancos de dados, registro |
| **Fluxo de dados** | Binary, ALPC, HTTP, HTTPS/TLS/SSL, IOCTL, IPSec, pipe nomeado, RPC/DCOM, SMB, UDP |
| **Limite de linha/borda de confiança** | Redes corporativas, Internet, máquina, área restrita, modo de usuário/kernel |

### <a name="notesmessages"></a>Notas/mensagens

| Componente                               | Detalhes      |
| --------------------------------------- | ------------ |
| **Mensagens** | Lógica de ferramenta interna que alerta os usuários sempre que houver um erro, como nenhum fluxo de dados entre os elementos. |
| **Notas** | As notas manuais são adicionadas ao arquivo pelas equipes de engenharia em todo o processo de design e revisão. |

### <a name="element-properties"></a>Propriedades do elemento

As propriedades do elemento variam de acordo com os elementos selecionados. Além dos limites de confiança, todos os outros elementos contêm três seleções gerais:

| Propriedade do elemento                               | Detalhes      |
| --------------------------------------- | ------------ |
| **Name** | Útil para nomear seus processos, lojas, interadores e fluxos para que eles sejam facilmente reconhecidos. |
| **Fora do escopo** | Se selecionado, o elemento é retirado da matriz de geração de ameaças (não recomendado). |
| **Motivo para fora do escopo** | Campo de justificativa para permitir que os usuários saibam por que o escopo foi selecionado. |

As propriedades são alteradas em cada categoria de elemento. Selecione cada elemento para inspecionar as opções disponíveis. Ou você pode abrir o modelo para saber mais. Vamos examinar os recursos.

## <a name="welcome-screen"></a>Tela de boas-vindas

Ao abrir o aplicativo, você verá a tela de **boas-vindas** .

### <a name="open-a-model"></a>Abrir um modelo

Passe o mouse sobre **abrir um modelo** para revelar duas opções: **Abra a partir deste computador** e **abra do onedrive**. A primeira opção abre a tela **Abrir arquivo** . A segunda opção leva você pelo processo de entrada para o OneDrive. Após a autenticação bem-sucedida, você pode selecionar pastas e arquivos.

![Abrir modelo](./media/threat-modeling-tool-feature-overview/openmodel.png)

![Abrir do computador ou do OneDrive](./media/threat-modeling-tool-feature-overview/openmodel2.png)

### <a name="feedback-suggestions-and-issues"></a>Comentários, sugestões e problemas

Ao selecionar **comentários, sugestões e problemas**, você vai para o fórum do MSDN para ferramentas do SDL. Você pode ler o que outras pessoas estão dizendo sobre a ferramenta, incluindo soluções alternativas e novas ideias.

![Comentários](./media/threat-modeling-tool-feature-overview/feedback.png)

## <a name="design-view"></a>modo de exibição de Design

Quando você abre ou cria um novo modelo, a exibição **design** é aberta.

### <a name="add-elements"></a>Adicionar elementos

Você pode adicionar elementos na grade de duas maneiras:

- **Arrastar e soltar**: Arraste o elemento desejado para a grade. Em seguida, use as propriedades do elemento para fornecer informações adicionais.
- **Clique com o botão direito do mouse em**: Clique com o botão direito do mouse em qualquer lugar na grade e selecione itens no menu suspenso. Uma representação genérica do elemento selecionado aparece na tela.

### <a name="connect-elements"></a>Conectar elementos

Você pode conectar elementos de duas maneiras:

- **Arrastar e soltar**: Arraste a Dataflow desejada para a grade e conecte ambas as extremidades aos elementos apropriados.
- **Clique em + Shift**: Clique no primeiro elemento (enviando dados), pressione e segure a tecla Shift e, em seguida, selecione o segundo elemento (recebendo dados). Clique com o botão direito do mouse e selecione **conectar**. Se você usar um fluxo de dados bidirecional, a ordem não será tão importante.

### <a name="properties"></a>properties

 Para ver as propriedades que podem ser modificadas nos estênceis, selecione o estêncil e as informações são preenchidas de acordo. O exemplo a seguir mostra antes e depois que um estêncil de **banco de dados** é arrastado para o diagrama:

#### <a name="before"></a>Antes

![Antes](./media/threat-modeling-tool-feature-overview/properties1.png)

#### <a name="after"></a>Após

![Após](./media/threat-modeling-tool-feature-overview/properties2.png)

### <a name="messages"></a>Mensagens

Se você criar um modelo de ameaça e esquecer de conectar os fluxos de dados aos elementos, receberá uma notificação. Você pode ignorar a mensagem ou pode seguir as instruções para corrigir o problema. 

![Mensagens](./media/threat-modeling-tool-feature-overview/messages.png)

### <a name="notes"></a>Notas

Para adicionar anotações ao seu diagrama, alterne da guia **mensagens** para a guia **observações** .

## <a name="analysis-view"></a>Exibição de análise

Depois de criar seu diagrama, selecione o símbolo de **análise** (a lupa) na barra de ferramentas atalhos para alternar para a exibição de **análise** .

![Exibição de análise](./media/threat-modeling-tool-feature-overview/analysisview.png)

### <a name="generated-threat-selection"></a>Seleção de ameaças gerada

Ao selecionar uma ameaça, você pode usar três funções distintas:

| Funcionalidade                               | Information      |
| --------------------------------------- | ------------ |
| **Indicador de leitura** | <p>A ameaça é marcada como lida, o que ajuda você a acompanhar os itens revisados.</p><p>![Indicador de leitura/não lido](./media/threat-modeling-tool-feature-overview/readmode.png)</p> |
| **Foco de interação** | <p>A interação no diagrama que pertence a uma ameaça é realçada.</p><p>![Foco de interação](./media/threat-modeling-tool-feature-overview/interactionfocus.png)</p> |
| **Propriedades da ameaça** | <p>Informações adicionais sobre a ameaça aparecem na janela **Propriedades da ameaça** .</p><p>![Propriedades da ameaça](./media/threat-modeling-tool-feature-overview/threatproperties.png)</p> |

### <a name="priority-change"></a>Alteração de prioridade

Você pode alterar o nível de prioridade de cada ameaça gerada. Cores diferentes facilitam a identificação de ameaças de prioridade alta, média e baixa.

![Alteração de prioridade](./media/threat-modeling-tool-feature-overview/prioritychange.png)

### <a name="threat-properties-editable-fields"></a>Campos editáveis de propriedades de ameaça

Como visto na imagem anterior, você pode alterar as informações geradas pela ferramenta. Você também pode adicionar informações a determinados campos, como justificativa. Esses campos são gerados pelo modelo. Se precisar de mais informações para cada ameaça, você poderá fazer modificações.

![Propriedades da ameaça](./media/threat-modeling-tool-feature-overview/threatproperties.png)

## <a name="reports"></a>Relatórios

Depois de concluir a alteração das prioridades e atualizar o status de cada ameaça gerada, você poderá salvar o arquivo e/ou imprimir um relatório. Vá para **relatório** > **criar relatório completo**. Nomeie o relatório e você verá algo semelhante à imagem a seguir:

![Relatório](./media/threat-modeling-tool-feature-overview/report.png)

## <a name="next-steps"></a>Passos Seguintes

- Envie suas dúvidas, comentários e preocupações com tmtextsupport@microsoft.como. **[Baixe](https://aka.ms/threatmodelingtool)** o Threat Modeling Tool para começar.
- Para contribuir com um modelo para a Comunidade, acesse nossa página do [GitHub](https://github.com/Microsoft/threat-modeling-templates) .