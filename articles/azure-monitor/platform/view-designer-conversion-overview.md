---
title: Azure Monitor vê designer para guia de transição de livros
description: ''
author: austonli
ms.author: aul
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
ms.date: 02/07/2020
ms.openlocfilehash: 5c07d14f4a6d29b65a1ffe31fc6eaad18e8fbe26
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/12/2020
ms.locfileid: "77170906"
---
# <a name="azure-monitor-view-designer-to-workbooks-transition-guide"></a>Azure Monitor vê designer para guia de transição de livros
O designer de [visualização](view-designer.md) é uma funcionalidade do Azure Monitor que lhe permite criar vistas personalizadas para o ajudar a visualizar dados no seu espaço de trabalho Log Analytics, com gráficos, listas e cronologias. Estão a ser gradualmente eliminados e substituídos por livros de trabalho que fornecem funcionalidades adicionais. Este artigo fornece uma visão geral do processo de conversão das suas opiniões existentes em livros de trabalho.

## <a name="workbooks-overview"></a>Visão geral dos livros
[Os livros](../insights/vminsights-workbooks.md) de trabalho combinam texto, consultas de [registo,](../log-query/query-language.md)métricas e parâmetros em relatórios interativos ricos. Os membros da equipa com o mesmo acesso aos recursos do Azure também são capazes de editar livros de trabalho.

Os livros são úteis para cenários como:

-   Explorar o uso da sua máquina virtual quando não conhece antecipadamente as métricas de interesse: utilização de CPU, espaço de disco, memória, dependências de rede, etc. Ao contrário de outras ferramentas de análise de uso, os livros de livros permitem combinar vários tipos de visualizações e análises, tornando-as ótimas para este tipo de exploração em forma livre.
-   Explicando à sua equipa como está a funcionar um VM recentemente provisionado, mostrando métricas para contadores-chave e outros eventos de log.
-   Partilhar os resultados de uma experiência de redimensionamento do seu VM com outros membros da sua equipa. Você pode explicar os objetivos para a experiência com texto, em seguida, mostrar cada métrica de uso e consultas de análise usadas para avaliar a experiência, juntamente com chamadas claras para se cada métrica estava acima ou abaixo do alvo.
-   Reportar o impacto de uma interrupção no uso do seu VM, combinando dados, explicação de texto e uma discussão sobre os próximos passos para evitar interrupções no futuro.


## <a name="why-convert-view-designer-dashboards-to-workbooks"></a>Porquê converter dashboards de design para livros de trabalho?

O designer de visualização oferece a capacidade de gerar diferentes vistas e visualizações baseadas em consultas. No entanto, muitas personalizações de alto nível permanecem limitadas, como formatar as grelhas e os esquemas de mosaicos ou selecionar gráficos alternativos para representar os seus dados. O designer de visualização está restrito a um total de nove azulejos distintos para representar os seus dados.

Os Livros são uma plataforma que desbloqueia todo o potencial dos seus dados. os livros de trabalho não só retêm todas as capacidades, como também suportam funcionalidades adicionais através de texto, métricas, parâmetros e muito mais. Por exemplo, os livros de reprodução permitem que os utilizadores consolidem redes densas e adicione barras de pesquisa para filtrar e analisar facilmente os dados. 

### <a name="advantages-of-using-workbooks-over-view-designer"></a>Vantagens de usar livros de trabalho sobre ver designer

* Suporta troncos e métricas.
* Permite tanto visualizações pessoais para controlo de acesso individual e vistas partilhadas de livros de trabalho.
* Opções de layout personalizadas com separadores, dimensionamento e controlos de escala.
* Suporte para consulta em vários espaços de trabalho do Log Analytics, aplicações de Application Insights e subscrições.
* Permite parâmetros personalizados que atualizam dinamicamente gráficos e visualizações associados.
* Suporte de galeria de modelos do GitHub público.

Embora este guia ofereça passos simples para recriar diretamente várias das vistas de designers de visualização comumente usadas, os livros de trabalho permitem que os utilizadores tenham a liberdade de criar e projetar qualquer uma das suas próprias visualizações e métricas personalizadas. A seguinte imagem é do modelo de [utilização](https://go.microsoft.com/fwlink/?linkid=874159&resourceId=Azure%20Monitor&featureName=Workbooks&itemId=community-Workbooks%2FAzure%20Monitor%20-%20Workspaces%2FWorkspace%20Usage&workbookTemplateName=Workspace%20Usage&func=NavigateToPortalFeature&type=workbook) do Espaço de Trabalho e mostra um exemplo do que os livros de trabalho são capazes de criar:


![Exemplo da aplicação de livros de trabalho](media/view-designer-conversion-overview/workbook-template-example.jpg)


## <a name="how-to-start-using-workbooks"></a>Como começar a usar livros
Os livros abertos dos Livros de Trabalho estão habilitados em espaços de trabalho do Log Analytics como um item na barra de navegação lateral, diretamente sob a localização do designer de visualização.

![Navegação de livros de livros](media/view-designer-conversion-overview/workbooks-nav.png)

Uma vez selecionada, será exibida uma galeria com todos os livros e modelos guardados para o seu espaço de trabalho.

![Galeria de livros de livros](media/view-designer-conversion-overview/workbooks-gallery.png)

Para iniciar um novo livro, pode selecionar o modelo **Empty** sob **arranque rápido**, ou o **novo** ícone na barra de navegação superior. Para ver modelos ou voltar aos livros de trabalho guardados, selecione o item da galeria ou procure o nome na barra de pesquisa.

Para guardar um livro, terá de guardar o relatório com um título específico, subscrição, grupo de recursos e localização.
O livro irá encher automaticamente as mesmas configurações do espaço de trabalho de LA, com a mesma subscrição, grupo de recursos, no entanto, os utilizadores podem alterar estas definições de relatório. Os livros de reposição são, por padrão, guardados para *Os Meus Relatórios,* acessíveis apenas por cada utilizador. Também podem ser salvos diretamente para relatórios partilhados ou partilhados posteriormente.

![Poupança de livros](media/view-designer-conversion-overview/workbooks-save.png)

## <a name="next-steps"></a>Passos seguintes

- [Opções de conversão](view-designer-conversion-options.md)
