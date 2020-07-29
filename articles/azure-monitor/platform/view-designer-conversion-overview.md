---
title: Azure Monitor vê designer para guia de transição de livros
description: ''
author: austonli
ms.author: aul
ms.subservice: ''
ms.topic: conceptual
ms.date: 02/07/2020
ms.openlocfilehash: 977dcc71fd25b19e09cfa9126bf01d380f581aca
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/28/2020
ms.locfileid: "87289122"
---
# <a name="azure-monitor-view-designer-to-workbooks-transition-guide"></a>Azure Monitor vê designer para guia de transição de livros
[O view designer](view-designer.md) é uma funcionalidade do Azure Monitor que lhe permite criar visualizações personalizadas para o ajudar a visualizar dados no seu espaço de trabalho Log Analytics, com gráficos, listas e linhas de tempo. Estão a ser eliminados e substituídos por livros que fornecem funcionalidades adicionais. Este artigo fornece uma visão geral do processo de conversão das suas opiniões existentes em livros de trabalho.

## <a name="retirement-schedule"></a>Horário de aposentadoria

| Alterar | O que isto significa | Data Prevista |
|:---|:---|:---|
| Desativar a criação de novos pontos de vista criados através do View Designer. | Deixará de ser capaz de criar e guardar novas vistas personalizadas no portal Azure.| novembro de 2020 |
| Desative a função de edição para visualizações existentes com o View Designer. | Deixará de ser capaz de modificar e guardar alterações nas suas vistas personalizadas existentes. | novembro de 2020 |
| Desativar a implementação de pontos de vista para os espaços de trabalho do Log Analytics | Deixará de ser capaz de implementar vistas personalizadas utilizando os espaços de trabalho DA ARM para registar o trabalho. | março de 2021 |
| Ver Designer já não disponível no portal Azure | A experiência do portal deixará de suportar o View Designer. | junho de 2021 |
| Vistas personalizadas removidas do Resumo do Espaço de Trabalho | Deixará de poder aceder aos seus dados de visualização personalizados. | dezembro de 2021 |

## <a name="workbooks-overview"></a>Visão geral dos livros de trabalho
[Os livros combinam](../insights/vminsights-workbooks.md) texto, [consultas de registo,](../log-query/query-language.md)métricas e parâmetros em ricos relatórios interativos. Os membros da equipa com o mesmo acesso aos recursos do Azure também podem editar livros de trabalho.

Os livros de trabalho são úteis para cenários como:

-   Explorar o uso da sua máquina virtual quando não conhece as métricas de interesse com antecedência: utilização de CPU, espaço em disco, memória, dependências de rede, etc. Ao contrário de outras ferramentas de análise de uso, os livros permitem combinar vários tipos de visualizações e análises, tornando-as ótimas para este tipo de exploração de forma livre.
-   Explicando à sua equipa como está a funcionar um VM recentemente aprovisionado, mostrando métricas para contadores-chave e outros eventos de registo.
-   Partilhar os resultados de uma experiência de redimensionamento do seu VM com outros membros da sua equipa. Você pode explicar os objetivos para a experiência com texto, em seguida, mostrar cada métrica de uso e consultas analíticas usadas para avaliar a experiência, juntamente com chamadas claras para se cada métrica estava acima ou abaixo do alvo.
-   Relatar o impacto de uma paragem no uso do seu VM, combinando dados, explicação de texto, e uma discussão dos próximos passos para evitar interrupções no futuro.


## <a name="why-convert-view-designer-dashboards-to-workbooks"></a>Porquê converter painéis de visualização para livros de trabalho?

O view designer oferece a capacidade de gerar diferentes visualizações e visualizações baseadas em consultas. No entanto, muitas personalizações de alto nível permanecem limitadas, como formatar as grelhas e os esquemas de mosaicos ou selecionar gráficos alternativos para representar os seus dados. O designer de visualização está restrito a um total de nove azulejos distintos para representar os seus dados.

Os Livros são uma plataforma que desbloqueia todo o potencial dos seus dados. os livros de trabalho não só retêm todas as capacidades, como também suportam funcionalidades adicionais através de texto, métricas, parâmetros e muito mais. Por exemplo, os livros permitem que os utilizadores consolidem redes densas e adicione barras de pesquisa para filtrar e analisar facilmente os dados. 

### <a name="advantages-of-using-workbooks-over-view-designer"></a>Vantagens de usar livros de trabalho sobre o View Designer

* Suporta registos e métricas.
* Permite tanto vistas pessoais para controlo de acesso individual como vistas de livros partilhados.
* Opções de layout personalizadas com separadores, tamanho e controlos de escala.
* Suporte para consulta em vários espaços de trabalho do Log Analytics, aplicações application insights e subscrições.
* Permite parâmetros personalizados que atualizam dinamicamente gráficos e visualizações associadas.
* Suporte de galeria de modelos do gitHub público.

Embora este guia ofereça passos simples para recriar diretamente várias das vistas de designers comumente utilizadas, os livros de trabalho permitem que os utilizadores tenham a liberdade de criar e projetar qualquer uma das suas próprias visualizações e métricas personalizadas. A imagem a seguir é do modelo de [utilização](https://go.microsoft.com/fwlink/?linkid=874159&resourceId=Azure%20Monitor&featureName=Workbooks&itemId=community-Workbooks%2FAzure%20Monitor%20-%20Workspaces%2FWorkspace%20Usage&workbookTemplateName=Workspace%20Usage&func=NavigateToPortalFeature&type=workbook) do Espaço de Trabalho e mostra um exemplo do que os livros são capazes de criar:


![Exemplo da aplicação de livros de trabalho](media/view-designer-conversion-overview/workbook-template-example.jpg)


## <a name="how-to-start-using-workbooks"></a>Como começar a usar livros
Os livros abertos dos livros de trabalho são ativados nos espaços de trabalho do Log Analytics como um item na barra de navegação lateral, diretamente sob a localização do designer de visualização.

![Navegação de livros de trabalho](media/view-designer-conversion-overview/workbooks-nav.png)

Uma vez selecionada, será exibida uma galeria com a lista de todos os livros e modelos guardados para o seu espaço de trabalho.

![Galeria de livros](media/view-designer-conversion-overview/workbooks-gallery.png)

Para iniciar um novo livro, pode selecionar o modelo **Empty** em **Arranque Rápido**ou o **Novo** ícone na barra de navegação superior. Para ver os modelos ou voltar aos livros de trabalho guardados, selecione o item da galeria ou procure o nome na barra de pesquisa.

Para guardar um livro, terá de guardar o relatório com um título específico, subscrição, grupo de recursos e localização.
O livro irá preencher automaticamente as mesmas definições que o espaço de trabalho de LA, com a mesma subscrição, grupo de recursos, no entanto, os utilizadores podem alterar estas definições de relatório. Os livros de trabalho são, por defeito, guardados nos *Meus Relatórios,* acessíveis apenas por cada utilizador. Também podem ser guardados diretamente para relatos partilhados ou partilhados mais tarde.

![Livros de trabalho economizam](media/view-designer-conversion-overview/workbooks-save.png)

## <a name="next-steps"></a>Passos seguintes

- [Opções de conversão](view-designer-conversion-options.md)
