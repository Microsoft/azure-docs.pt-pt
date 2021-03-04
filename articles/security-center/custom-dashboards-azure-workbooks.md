---
title: Galeria de livros de obras no Centro de Segurança Azure
description: Saiba como criar relatórios ricos e interativos dos seus dados do Azure Security Center com a galeria integrada de livros de trabalho Azure Monitor
author: memildin
ms.author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 03/04/2021
ms.openlocfilehash: 198702f619e490e8000e4430aab23a7f6bfb6d85
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2021
ms.locfileid: "102107977"
---
# <a name="create-rich-interactive-reports-of-security-center-data"></a>Criar relatórios ricos e interativos de dados do Centro de Segurança

[Os livros de trabalho do Azure Monitor](../azure-monitor/visualize/workbooks-overview.md) fornecem uma tela flexível para análise de dados e a criação de relatórios visuais ricos dentro do portal Azure. Permitem-lhe aceder a várias fontes de dados de todo o Azure e combiná-las em experiências interativas unificadas.

Os livros de trabalho fornecem um rico conjunto de capacidades para visualizar os seus dados Azure. Para exemplos detalhados de cada tipo de visualização, consulte os [exemplos de visualização e documentação.](../azure-monitor/visualize/workbooks-text-visualizations.md) 

Dentro do Centro de Segurança Azure, você pode aceder aos relatórios incorporados para rastrear a postura de segurança da sua organização. Também pode construir relatórios personalizados para visualizar uma vasta gama de dados do Security Center ou de outras fontes de dados suportadas.

:::image type="content" source="media/custom-dashboards-azure-workbooks/secure-score-over-time-snip.png" alt-text="Pontuação segura ao longo do tempo":::

## <a name="availability"></a>Disponibilidade

| Aspeto                          | Detalhes                                                                                                                                      |
|---------------------------------|:---------------------------------------------------------------------------------------------------------------------------------------------|
| Estado de libertação:                  | Pré-visualizar<br>[!INCLUDE [Legalese](../../includes/security-center-preview-legal-text.md)]                                                       |
| Preços:                        | Gratuito                                                                                                                                         |
| Funções e permissões necessárias: | Para guardar livros de trabalho, deve ter pelo menos permissões de contribuinte de livros de trabalho no grupo de recursos-alvo                                      |
| Nuvens:                         | ![Sim](./media/icons/yes-icon.png) Nuvens comerciais<br>![Sim](./media/icons/yes-icon.png) Nacional/Soberano (Gov dos EUA, China Gov, Outro Gov) |
|                                 |                                                                                                                                              |

## <a name="workbooks-gallery-in-azure-security-center"></a>Galeria de livros de obras no Centro de Segurança Azure

Com a funcionalidade integrada Azure Workbooks, o Azure Security Center torna simples a construção de relatórios interativos personalizados. O Security Center também inclui uma galeria de livros com os seguintes relatórios prontos para a sua personalização:

- **Pontuação Segura Ao longo do Tempo** - Acompanhe as pontuações das suas subscrições e altera as recomendações para os seus recursos
- **Atualizações do sistema** - Ver atualizações do sistema em falta por recursos, SO, gravidade e muito mais
- **Resultados da Avaliação da Vulnerabilidade** - Ver as conclusões das análises de vulnerabilidade dos seus recursos Azure

:::image type="content" source="media/custom-dashboards-azure-workbooks/workbooks-gallery-security-center.png" alt-text="Galeria de livros embutidos no Centro de Segurança Azure":::

Escolha um dos relatórios fornecidos ou crie o seu próprio.

> [!TIP]
> Utilize o botão **Editar** para personalizar qualquer um dos relatórios fornecidos para sua satisfação. Quando terminar a edição, **selecione Save** e as suas alterações serão guardadas num novo livro.
> 
> :::image type="content" source="media/custom-dashboards-azure-workbooks/editing-supplied-workbooks.png" alt-text="Editar os livros fornecidos para personalizá-los para as suas necessidades particulares":::

### <a name="use-the-secure-score-over-time-report"></a>Utilize o relatório 'Pontuação Segura ao longo do tempo'

Este relatório utiliza dados de pontuação seguros do seu espaço de trabalho Log Analytics. Esses dados devem ser exportados a partir da ferramenta de exportação contínua, tal como descrito na [Configure, exportação contínua das páginas do Centro de Segurança no portal Azure](continuous-export.md?tabs=azure-portal).

Ao configurar a exportação contínua, desaveie a frequência de exportação tanto para **atualizações de streaming** como **para instantâneos**.

:::image type="content" source="media/custom-dashboards-azure-workbooks/export-frequency-both.png" alt-text="Para obter a pontuação segura ao longo do tempo, terá de selecionar ambas as opções a partir das definições de frequência de exportação na sua configuração de exportação contínua":::

> [!NOTE]
> As fotos são exportadas semanalmente, por isso terá de esperar pelo menos uma semana para que o primeiro instantâneo seja exportado antes de poder ver os dados neste relatório.

> [!TIP]
> Para configurar a exportação contínua em toda a sua organização, utilize as políticas fornecidas da Azure Policy 'DeployIfNotExist' descritas em [Configure exportação contínua à escala](continuous-export.md?tabs=azure-policy).

O relatório de pontuação segura ao longo do tempo tem cinco gráficos para as subscrições reportando aos espaços de trabalho selecionados:


|Graph  |Exemplo  |
|---------|---------|
|**Tendências de pontuação para a última semana e mês**<br>Utilize esta secção para monitorizar a pontuação atual e as tendências gerais das pontuações para as suas subscrições.|:::image type="content" source="media/custom-dashboards-azure-workbooks/secure-score-over-time-table-1.png" alt-text="Tendências para pontuação segura no relatório incorporado":::|
|**Pontuação agregada para todas as subscrições selecionadas**<br>Passe o rato por qualquer ponto da linha de tendência para ver a pontuação agregada em qualquer data no intervalo de tempo selecionado.|:::image type="content" source="media/custom-dashboards-azure-workbooks/secure-score-over-time-table-2.png" alt-text="Pontuação agregada para todas as subscrições selecionadas":::|
|**Recomendações com os recursos mais insalubres**<br>Esta tabela ajuda-o a triagem das recomendações que tiveram mais recursos alterados para insalubres durante o período selecionado.|:::image type="content" source="media/custom-dashboards-azure-workbooks/secure-score-over-time-table-3.png" alt-text="Recomendações com os recursos mais insalubres":::|
|**Pontuações para controlos de segurança específicos**<br>Os controlos de segurança do Centro de Segurança são agrupamentos lógicos de recomendações. Este gráfico mostra-lhe, num ápice, as pontuações semanais para todos os seus controlos.|:::image type="content" source="media/custom-dashboards-azure-workbooks/secure-score-over-time-table-4.png" alt-text="Pontuações para os seus controlos de segurança durante o período de tempo selecionado":::|
|**Mudanças de recursos**<br>Recomendações com mais recursos que mudaram de estado (saudável, pouco saudável ou não aplicável) durante o período selecionado são listadas aqui. Selecione qualquer recomendação da lista para abrir uma nova tabela listando os recursos específicos.|:::image type="content" source="media/custom-dashboards-azure-workbooks/secure-score-over-time-table-5.png" alt-text="Recomendações com mais recursos que mudaram o estado de saúde":::|

### <a name="use-the-system-updates-report"></a>Utilize o relatório 'Atualizações do Sistema'

Este relatório baseia-se na recomendação de segurança "As atualizações do sistema devem ser instaladas nas suas máquinas".

O relatório ajuda-o a identificar máquinas com atualizações pendentes.

Pode ver a situação das subscrições selecionadas de acordo com:

- A lista de recursos com atualizações pendentes
- A lista de atualizações em falta dos seus recursos

:::image type="content" source="media/custom-dashboards-azure-workbooks/system-updates-report.png" alt-text="Relatório de atualizações do sistema do Centro de Segurança com base na recomendação de segurança de atualizações em falta":::

### <a name="use-the-vulnerability-assessment-findings-report"></a>Utilize o relatório "Resultados da Avaliação de Vulnerabilidades"

O Security Center inclui scanners de vulnerabilidade para as suas máquinas, contentores em registos de contentores e servidores SQL.

Saiba mais sobre a utilização destes scanners:

- [Digitalize as suas máquinas com o scanner VA integrado](deploy-vulnerability-assessment-vm.md)
- [Arquivar as imagens de registo para encontrar vulnerabilidades](defender-for-container-registries-usage.md)
- [Digitalize os seus recursos SQL para obter vulnerabilidades](defender-for-sql-on-machines-vulnerability-assessment.md)

As conclusões de cada um destes scanners são comunicadas em recomendações separadas:

- As vulnerabilidades nas suas máquinas virtuais devem ser remediadas
- As vulnerabilidades nas imagens do Registo de Contentores de Azure devem ser remediadas (alimentadas por Qualys)
- Os resultados da avaliação da vulnerabilidade nas bases de dados do SQL devem ser remediados
- Os resultados da avaliação da vulnerabilidade nos seus servidores SQL em máquinas devem ser remediados

Este relatório reúne estas conclusões e organiza-as por gravidade, tipo de recursos e categoria.

:::image type="content" source="media/custom-dashboards-azure-workbooks/vulnerability-assessment-findings-report.png" alt-text="Relatório de resultados da avaliação de vulnerabilidades do Centro de Segurança":::


## <a name="import-workbooks-from-other-workbook-galleries"></a>Importar livros de outras galerias de livros

Se construiu livros noutros serviços da Azure e quer mudá-los para a galeria de livros do Azure Security Center:

1. Abra o livro de trabalho do alvo.

1. A partir da barra de ferramentas, **selecione Editar.**

    :::image type="content" source="media/custom-dashboards-azure-workbooks/editing-workbooks.png" alt-text="Edição de um livro do Azure Monitor":::

1. A partir da barra de ferramentas, selecione **</>** para introduzir o Editor Avançado.

    :::image type="content" source="media/custom-dashboards-azure-workbooks/editing-workbooks-advanced-editor.png" alt-text="Lançamento do editor avançado para obter o código JSON modelo de galeria":::

1. Copie o modelo de galeria JSON do livro.

1. Abra a galeria de livros no Security Center e a partir da barra de menu selecione **New**.
1. Selecione o **</>** para introduzir o Editor Avançado.
1. Cole em todo o modelo de galeria JSON.
1. Selecione **Aplicar**.
1. A partir da barra de ferramentas, **selecione Guardar Como**.

    :::image type="content" source="media/custom-dashboards-azure-workbooks/editing-workbooks-save-as.png" alt-text="Guardar o livro para a galeria no Centro de Segurança":::

1. Introduza os detalhes necessários para a poupança do livro:
   1. Um nome para o livro
   2. A região desejada
   3. Subscrição, grupo de recursos e partilha conforme apropriado.

Encontrará o seu livro de trabalho guardado na categoria **de livros modificados recentemente.**


## <a name="next-steps"></a>Passos seguintes

Este artigo descreveu a página integrada de livros de trabalho do Monitor Azure monitor do Security Center com relatórios incorporados e a opção de construir os seus próprios relatórios interativos personalizados.

- Saiba mais sobre [os livros de trabalho do Azure Monitor](../azure-monitor/visualize/workbooks-overview.md)
- Os relatórios incorporados retiram os seus dados das recomendações do Centro de Segurança. Conheça as muitas recomendações de segurança nas [recomendações de segurança - um guia de referência](recommendations-reference.md)