---
title: Configurar relatórios do Azure Backup
description: Configure e veja relatórios para backup azure usando log analytics e livros de trabalho Azure
ms.topic: conceptual
ms.date: 02/10/2020
ms.openlocfilehash: e0c7418d7141a3b12f367f1b12ee740eaac64703
ms.sourcegitcommit: cf7caaf1e42f1420e1491e3616cc989d504f0902
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/22/2020
ms.locfileid: "83797536"
---
# <a name="configure-azure-backup-reports"></a>Configurar relatórios do Azure Backup

Um requisito comum para os administradores de backup é obter insights sobre backups com base em dados que se estendem por um longo período de tempo. Os casos de utilização para tal solução incluem:

- Alocar e prever o armazenamento em nuvem consumido.
- Auditoria de backups e restauros.
- Identificando as principais tendências em diferentes níveis de granularidade.

Hoje, a Azure Backup fornece uma solução de reporte que utiliza [registos do Monitor Azure](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal) e [livros de trabalho Azure.](https://docs.microsoft.com/azure/azure-monitor/platform/workbooks-overview) Estes recursos ajudam-no a obter informações ricas sobre os seus backups em toda a sua propriedade de reserva. Este artigo explica como configurar e visualizar relatórios de Backup Azure.

## <a name="supported-scenarios"></a>Cenários suportados

- Os relatórios de backup são suportados para VMs Azure, SQL em VMs Azure, SAP HANA em VMs Azure, agente microsoft Azure Recovery Services (MARS), Microsoft Azure Backup Server (MABS) e System Center Data Protection Manager (DPM). Os dados relativos à cópia de segurança do Azure File Share não são atualmente visíveis nos Relatórios de Backup.
- Para cargas de trabalho DPM, os relatórios de backup são suportados para dPM versão 5.1.363.0 e acima e Verden.0.0.9127.0 ou superior.
- Para cargas de trabalho MABS, os relatórios de backup são suportados para a versão 13.0.415.0 e superior e para a Versão Agente 2.0.9170.0 ou superior.
- Os relatórios de backup podem ser vistos em todos os itens de backup, cofres, subscrições e regiões, desde que os seus dados sejam enviados para um espaço de trabalho do Log Analytics a que o utilizador tenha acesso. Para ver relatórios para um conjunto de cofres, basta ter acesso dos leitores ao espaço de trabalho do Log Analytics para o qual os cofres estão a enviar os seus dados. Não precisas de ter acesso aos cofres individuais.
- Se é um utilizador do [Farol Azure](https://docs.microsoft.com/azure/lighthouse/) com acesso delegado às subscrições dos seus clientes, pode utilizar estes relatórios com o Farol Azure para ver relatórios em todos os seus inquilinos.
- Atualmente, os dados podem ser vistos em Relatórios de Backup através de um máximo de 100 espaços de trabalho de Log Analytics (entre inquilinos).
- Os dados relativos a trabalhos de backup de registo não estão atualmente apresentados nos relatórios.

## <a name="get-started"></a>Introdução

Siga estes passos para começar a utilizar os relatórios.

### <a name="1-create-a-log-analytics-workspace-or-use-an-existing-one"></a>1. Criar um espaço de trabalho log Analytics ou utilizar um existente

Crie um ou mais espaços de trabalho do Log Analytics para armazenar os seus dados de relatórios de backup. A localização e subscrição onde este espaço de trabalho log Analytics pode ser criado é independente da localização e subscrição onde os seus cofres existem.

Para configurar um espaço de trabalho log Analytics, consulte Criar um espaço de [trabalho Log Analytics no portal Azure](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace).

Por padrão, os dados num espaço de trabalho do Log Analytics são conservados durante 30 dias. Para ver dados para um horizonte temporal mais longo, altere o período de retenção do espaço de trabalho Log Analytics. Para alterar o período de retenção, consulte [Gerir a utilização e os custos com os registos do Monitor Azure](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage).

### <a name="2-configure-diagnostics-settings-for-your-vaults"></a>2. Configure as definições de diagnóstico para os seus cofres

Os recursos do Gestor de Recursos Azure, tais como cofres de Serviços de Recuperação, gravam informações sobre operações programadas e operações desencadeadas pelo utilizador como dados de diagnóstico.

Na secção de monitorização do cofre dos Serviços de Recuperação, selecione **definições** de Diagnóstico e especifique o alvo para os dados de diagnóstico do cofre do cofre dos Serviços de Recuperação. Para saber mais sobre a utilização de eventos de diagnóstico, consulte Utilize as definições de [diagnóstico para cofres](https://docs.microsoft.com/azure/backup/backup-azure-diagnostic-events)de Serviços de Recuperação .

![Painel de definições de diagnóstico](./media/backup-azure-configure-backup-reports/resource-specific-blade.png)

O Azure Backup também fornece uma definição de Política Azure incorporada, que automatiza a configuração das definições de diagnóstico para todos os cofres num dado âmbito. Para aprender a usar esta política, consulte as definições de [diagnóstico do cofre Configure em escala](https://docs.microsoft.com/azure/backup/azure-policy-configure-diagnostics).

> [!NOTE]
> Depois de configurar os diagnósticos, pode demorar até 24 horas para que o impulso inicial de dados seja concluído. Depois de os dados começarem a fluir para o espaço de trabalho do Log Analytics, pode não ver dados nos relatórios imediatamente porque os dados do dia parcial atual não são mostrados nos relatórios. Para obter mais informações, consulte [as Convenções utilizadas nos relatórios de backup](https://docs.microsoft.com/azure/backup/configure-reports#conventions-used-in-backup-reports). Recomendamos que comece a visualizar os relatórios dois dias após configurar os seus cofres para enviar dados para o Log Analytics.

#### <a name="3-view-reports-in-the-azure-portal"></a>3. Ver relatórios no portal Azure

Depois de configurar os seus cofres para enviar dados para o Log Analytics, veja os seus relatórios de backup indo ao painel de qualquer cofre e selecionando **Relatórios**de Backup .

![Painel de instrumentos do cofre](./media/backup-azure-configure-backup-reports/vault-dashboard.png)

Selecione este link para abrir o livro de trabalho do relatório de cópia de segurança.

> [!NOTE]
>
> - Atualmente, a carga inicial do relatório pode demorar até 1 minuto.
> - O cofre dos Serviços de Recuperação é apenas um ponto de entrada para os relatórios de backup. Depois do livro de trabalho de backup abrir a partir de um painel de um cofre, selecione o conjunto apropriado de espaços de trabalho Log Analytics para ver dados agregados em todos os seus cofres.

O relatório contém vários separadores:

- **Resumo:** Use este separador para obter uma visão geral de alto nível da sua propriedade de backup. Você pode obter um olhar rápido do número total de itens de backup, armazenamento total em nuvem consumido, o número de instâncias protegidas, e a taxa de sucesso de emprego por tipo de carga de trabalho. Para obter informações mais detalhadas sobre um tipo específico de artefacto de backup, vá aos respetivos separadores.

   ![Separador Resumo](./media/backup-azure-configure-backup-reports/summary.png)

- **Itens de backup:** Utilize este separador para ver informações e tendências no armazenamento em nuvem consumidas a um nível de backup. Por exemplo, se utilizar o SQL numa cópia de segurança Azure VM, pode ver o armazenamento em nuvem consumido por cada base de dados SQL que está a ser apoiada. Também pode optar por ver dados para itens de backup de um determinado estado de proteção. Por exemplo, selecionar o azulejo **de proteção parado** na parte superior do separador filtra todos os widgets por baixo para mostrar dados apenas para itens de backup no estado de Proteção Parado.

   ![Separador de itens de backup](./media/backup-azure-configure-backup-reports/backup-items.png)

- **Utilização**: Utilize este separador para visualizar os parâmetros de faturação das chaves para as suas cópias de segurança. As informações mostradas neste separador estão ao nível de uma entidade de faturação (recipiente protegido). Por exemplo, no caso de um servidor DPM ser apoiado até ao Azure, pode ver a tendência de instâncias protegidas e armazenamento em nuvem consumido para o servidor DPM. Da mesma forma, se utilizar o SQL em Backup Azure ou SAP HANA em Backup Azure, este separador fornece-lhe informações relacionadas com o uso ao nível da máquina virtual em que estas bases de dados estão contidas.

   ![Separador de utilização](./media/backup-azure-configure-backup-reports/usage.png)

> [!NOTE]
> No caso das cargas de trabalho do DPM, os utilizadores podem ver uma ligeira diferença (da ordem de 20 MB por servidor DPM) entre os valores de utilização apresentados nos relatórios em comparação com o valor de utilização agregado, como mostrado no separador de visão geral do cofre dos serviços de recuperação. Esta diferença é explicada pelo facto de todos os servidores DPM registados para cópia de segurança terem uma fonte de dados associada de 'metadados' que não é surgida como um artefacto para reportar.

- **Empregos**: Utilize este separador para ver as tendências de longo prazo no emprego, como o número de empregos falhados por dia e as principais causas de insuficiência de emprego. Pode ver esta informação tanto a um nível agregado como a um nível de backup. Selecione um determinado item de backup numa grelha para visualizar informações detalhadas sobre cada trabalho que foi desencadeado nesse item de backup no intervalo de tempo selecionado.

   ![Separador de empregos](./media/backup-azure-configure-backup-reports/jobs.png)

- **Políticas**: Utilize este separador para visualizar informações sobre todas as suas políticas ativas, tais como o número de itens associados e o armazenamento total em nuvem consumido por itens apoiados numa determinada política. Selecione uma determinada política para visualizar informações sobre cada um dos seus itens de backup associados.

   ![Separador de políticas](./media/backup-azure-configure-backup-reports/policies.png)

## <a name="export-to-excel"></a>Exportar para o Excel

Selecione o botão de seta para baixo na parte superior direita de qualquer widget, como uma tabela ou gráfico, para exportar o conteúdo desse widget como uma folha excel como é com filtros existentes aplicados. Para exportar mais linhas de uma tabela para o Excel, pode aumentar o número de linhas exibidas na página utilizando a seta de drop-down **Rows Per Page** no topo de cada grelha.

## <a name="pin-to-dashboard"></a>Afixar ao dashboard

Selecione o botão pin na parte superior de cada widget para fixar o widget no painel do portal Azure. Esta funcionalidade ajuda-o a criar dashboards personalizados à medida para mostrar as informações mais importantes de que necessita.

## <a name="cross-tenant-reports"></a>Relatórios de inquilinos cruzados

Se utilizar o [Farol Azure](https://docs.microsoft.com/azure/lighthouse/) com acesso delegado a subscrições em vários ambientes de inquilinos, pode utilizar o filtro de subscrição predefinido. Selecione o botão de filtro no canto superior direito do portal Azure para escolher todas as subscrições para as quais pretende ver dados. Ao fazê-lo, permite-lhe selecionar espaços de trabalho do Log Analytics em todos os seus inquilinos para ver relatórios multiarrendatizados.

## <a name="conventions-used-in-backup-reports"></a>Convenções utilizadas em relatórios de backup

- Os filtros funcionam da esquerda para a direita e de cima para baixo em cada separador. Ou seja, qualquer filtro só se aplica a todos os widgets que estão posicionados à direita desse filtro ou abaixo desse filtro.
- Selecionando um azulejo colorido filtra os widgets abaixo do azulejo para registos que digam respeito ao valor desse azulejo. Por exemplo, a seleção do azulejo **Descontinuado** de Proteção no separador **'Backup Itens'** filtra as grelhas e gráficos abaixo para mostrar dados relativos a itens de backup no estado de Proteção Parado.
- Os azulejos que não são coloridos não são clicáveis.
- Os dados do dia parcial de hoje não estão nos relatórios. Assim, quando o valor selecionado do Intervalo de **Tempo** é **de 7 dias,** o relatório mostra registos dos últimos sete dias concluídos. O dia de hoje não está incluído.
- O relatório mostra pormenores sobre os postos de trabalho (para além dos postos de trabalho de registo) que foram *desencadeados* no intervalo de tempo selecionado.
- Os valores apresentados para armazenamento em **nuvem** e **instâncias protegidas** estão no *final* da gama de tempo selecionada.
- Os itens de backup apresentados nos relatórios são os itens que existem no *final* do intervalo de tempo selecionado. Não são apresentados itens de backup que tenham sido eliminados no meio do intervalo de tempo selecionado. A mesma convenção aplica-se também às políticas de backup.

## <a name="query-load-times"></a>Tempos de carga de consulta

Os widgets no relatório Backup são alimentados por consultas Kusto, que funcionam nos espaços de trabalho do utilizador Log Analytics. Estas consultas normalmente envolvem o processamento de grandes quantidades de dados, com múltiplas juntas para permitir insights mais ricos. Como resultado, os widgets podem não carregar instantaneamente quando o utilizador visualiza relatórios através de uma grande propriedade de backup. Esta tabela fornece uma estimativa aproximada do tempo que diferentes widgets podem levar para carregar, com base no número de itens de backup e no intervalo de tempo para o qual o relatório está a ser visto.

| **# Fontes de dados**                         | **Horizonte temporal** | **Tempos de carga aproximados**                                              |
| --------------------------------- | ------------- | ------------------------------------------------------------ |
| ~5 K                       | 1 mês          | Azulejos: 5-10 segundos <br> Grelhas: 5-10 segundos <br> Gráficos: 5-10 segs <br> Filtros de nível de relatório: 5-10 segundos|
| ~5 K                       | 3 meses          | Azulejos: 5-10 segundos <br> Grelhas: 5-10 segundos <br> Gráficos: 5-10 segs <br> Filtros de nível de relatório: 5-10 segundos|
| ~10 K                       | 3 meses          | Azulejos: 15-20 segundos <br> Grelhas: 15-20 segundos <br> Gráficos: 1-2 minutos <br> Filtros de nível de relatório: 25-30 segundos|
| ~15 K                       | 1 mês          | Azulejos: 15-20 segundos <br> Grelhas: 15-20 segundos <br> Gráficos: 50-60 segs <br> Filtros de nível de relatório: 20-25 segs|
| ~15 K                       | 3 meses          | Azulejos: 20-30 segundos <br> Grelhas: 20-30 segundos <br> Gráficos: 2-3 minutos <br> Filtros de nível de relatório: 50-60 segundos |

## <a name="what-happened-to-the-power-bi-reports"></a> O que aconteceu aos relatórios do Power BI? 

- A anterior aplicação de modelo supreende Power BI para reportagem, que produziu dados de uma conta de armazenamento Azure, está numa trajetória de depreciação. Recomendamos que comece a enviar dados de diagnóstico de cofre para O Log Analytics para visualizar relatórios.

- Além disso, o [esquema V1](https://docs.microsoft.com/azure/backup/backup-azure-diagnostics-mode-data-model#v1-schema-vs-v2-schema) de enviar dados de diagnóstico para uma conta de armazenamento ou um espaço de trabalho de LA também está em uma rota de depreciação. Isto significa que se tiver escrito quaisquer consultas ou automatizações personalizadas com base no esquema V1, é aconselhável atualizar estas consultas para utilizar o esquema V2 atualmente suportado.

## <a name="next-steps"></a>Passos seguintes

[Saiba mais sobre monitorização e reportagem com O Backup Azure](https://docs.microsoft.com/azure/backup/backup-azure-monitor-alert-faq)
