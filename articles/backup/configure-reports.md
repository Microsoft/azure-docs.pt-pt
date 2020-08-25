---
title: Configurar relatórios do Azure Backup
description: Configure e veja relatórios para Azure Backup usando diários de log analytics e Azure
ms.topic: conceptual
ms.date: 02/10/2020
ms.openlocfilehash: bcb9e75cea06be6ce69c6baccb5ac252c1666d4d
ms.sourcegitcommit: afa1411c3fb2084cccc4262860aab4f0b5c994ef
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/23/2020
ms.locfileid: "88755267"
---
# <a name="configure-azure-backup-reports"></a>Configurar relatórios do Azure Backup

Um requisito comum para os administradores de backup é obter insights sobre cópias de segurança com base em dados que se estendem por um longo período de tempo. Os casos de utilização para tal solução incluem:

- Alocação e previsão de armazenamento em nuvem consumido.
- Auditoria de backups e restauros.
- Identificar as principais tendências em diferentes níveis de granularidade.

Hoje, a Azure Backup fornece uma solução de reporte que utiliza [registos do Azure Monitor](../azure-monitor/log-query/get-started-portal.md) e [livros de trabalho Azure](../azure-monitor/platform/workbooks-overview.md). Estes recursos ajudam-no a obter informações ricas sobre os seus backups em toda a sua propriedade de reserva. Este artigo explica como configurar e ver relatórios de Backup da Azure.

## <a name="supported-scenarios"></a>Cenários suportados

- Os relatórios de backup são suportados para VMs Azure, SQL em VMs Azure, SAP HANA em VMs Azure, Microsoft Azure Recovery Services (MARS) agent, Microsoft Azure Backup Server (MABS) e System Center Data Protection Manager (DPM). Para a cópia de segurança do Azure File Share, os dados são apresentados para todos os registos criados em ou depois de 1 de junho de 2020.
- Para as cargas de trabalho de DPM, os relatórios de backup são suportados para a versão DPM 5.1.363.0 ou acima e a versão 2.0.9127.0 ou superior.
- Para as cargas de trabalho do MABS, os relatórios de backup são suportados para a versão MABS 13.0.415.0 e acima do agente Versão 2.0.9170.0 ou superior.
- Os relatórios de backup podem ser vistos em todos os itens de backup, cofres, subscrições e regiões, desde que os seus dados sejam enviados para um espaço de trabalho do Log Analytics a que o utilizador tenha acesso. Para visualizar relatórios de um conjunto de cofres, basta ter acesso ao espaço de trabalho do Log Analytics para onde os cofres estão a enviar os seus dados. Não precisas de ter acesso aos cofres individuais.
- Se é um utilizador do [Azure Lighthouse](../lighthouse/index.yml) com acesso delegado às subscrições dos seus clientes, pode utilizar estes relatórios com o Farol Azure para visualizar relatórios em todos os seus inquilinos.
- Atualmente, os dados podem ser vistos em Relatórios de Backup em um máximo de 100 log analytics workspaces (entre inquilinos).
- Os dados relativos aos trabalhos de backup de registos atualmente não estão nos relatórios.

## <a name="get-started"></a>Introdução

Siga estes passos para começar a usar os relatórios.

### <a name="1-create-a-log-analytics-workspace-or-use-an-existing-one"></a>1. Criar um espaço de trabalho Log Analytics ou utilizar um existente

Configurar um ou mais espaços de trabalho do Log Analytics para armazenar os seus dados de relatórios de backup. A localização e subscrição onde este espaço de trabalho Log Analytics pode ser criado é independente da localização e subscrição onde os seus cofres existem.

Para configurar um espaço de trabalho Log Analytics, consulte [Criar um espaço de trabalho Log Analytics no portal Azure](../azure-monitor/learn/quick-create-workspace.md).

Por predefinição, os dados num espaço de trabalho do Log Analytics são retidos durante 30 dias. Para ver os dados para um horizonte temporal mais longo, altere o período de retenção do espaço de trabalho Log Analytics. Para alterar o período de retenção, consulte [Gerir a utilização e os custos com os registos do Monitor Azure](../azure-monitor/platform/manage-cost-storage.md).

### <a name="2-configure-diagnostics-settings-for-your-vaults"></a>2. Configurar as definições de diagnóstico para os seus cofres

Recursos Azure Resource Manager, tais como cofres dos Serviços de Recuperação, informações de gravação sobre operações programadas e operações desencadeadas pelo utilizador como dados de diagnóstico.

Na secção de monitorização do cofre dos Serviços de Recuperação, selecione **as definições de Diagnóstico** e especifique o alvo para os dados de diagnóstico do cofre dos Serviços de Recuperação. Para saber mais sobre a utilização de eventos de diagnóstico, consulte [as definições de diagnóstico para cofres dos Serviços de Recuperação.](./backup-azure-diagnostic-events.md)

![Painel de definições de diagnóstico](./media/backup-azure-configure-backup-reports/resource-specific-blade.png)

O Azure Backup também fornece uma definição de Política Azure incorporada, que automatiza a configuração de configurações de diagnóstico para todos os cofres num determinado âmbito. Para aprender a utilizar esta política, consulte as definições de [diagnóstico de abóbada de Configure à escala](./azure-policy-configure-diagnostics.md).

> [!NOTE]
> Depois de configurar os diagnósticos, pode levar até 24 horas para que os dados iniciais se concretizem. Depois de os dados começarem a fluir para o espaço de trabalho do Log Analytics, poderá não ver os dados nos relatórios imediatamente porque os dados do dia parcial atual não são mostrados nos relatórios. Para obter mais informações, consulte [as Convenções utilizadas nos relatórios de backup](#conventions-used-in-backup-reports). Recomendamos que comece a ver os relatórios dois dias depois de configurar os seus cofres para enviar dados para o Log Analytics.

#### <a name="3-view-reports-in-the-azure-portal"></a>3. Ver relatórios no portal Azure

Depois de configurar os seus cofres para enviar dados para o Log Analytics, veja os seus relatórios de backup indo ao painel de qualquer cofre e selecionando **Relatórios de Backup**.

![Painel de abóbada](./media/backup-azure-configure-backup-reports/vault-dashboard.png)

Selecione este link para abrir o livro de relatórios de cópia de segurança.

> [!NOTE]
>
> - Atualmente, a carga inicial do relatório pode demorar até 1 minuto.
> - O cofre dos Serviços de Recuperação é apenas um ponto de entrada para relatórios de backup. Depois de o livro de relatórios de backup abrir a partir do painel de um cofre, selecione o conjunto apropriado de espaços de trabalho log Analytics para ver dados agregados em todos os seus cofres.

O relatório contém vários separadores:

##### <a name="summary"></a>Resumo

Use este separador para obter uma visão geral de alto nível da sua propriedade de reserva. Você pode obter uma olhada rápida do número total de itens de backup, armazenamento total na nuvem consumido, o número de casos protegidos, e a taxa de sucesso do trabalho por tipo de carga de trabalho. Para obter informações mais detalhadas sobre um tipo específico de artefacto de backup, aceda aos respetivos separadores.

   ![Separador Resumo](./media/backup-azure-configure-backup-reports/summary.png)

##### <a name="backup-items"></a>Itens de Cópia de Segurança

Utilize este separador para ver informações e tendências sobre o armazenamento na nuvem consumidas a um nível de itens de backup. Por exemplo, se utilizar o SQL numa cópia de segurança Azure VM, pode ver o armazenamento na nuvem consumido por cada base de dados SQL que está a ser apoiada. Também pode optar por ver dados para itens de backup de um determinado estado de proteção. Por exemplo, selecionar o azulejo **'Protecção' Stop** na parte superior do separador filtra todos os widgets por baixo para mostrar dados apenas para itens de backup no estado de Proteção Parado.

   ![Separador de Itens de Reserva](./media/backup-azure-configure-backup-reports/backup-items.png)

##### <a name="usage"></a>Utilização

Utilize este separador para visualizar os parâmetros de faturação das chaves para as suas cópias de segurança. A informação apresentada neste separador encontra-se a um nível de entidade de faturação (contentor protegido). Por exemplo, se um servidor DPM estiver a ser apoiado até ao Azure, pode ver a tendência de casos protegidos e armazenamento na nuvem consumido para o servidor DPM. Da mesma forma, se utilizar o SQL em Azure Backup ou SAP HANA em Azure Backup, este separador dá-lhe informações relacionadas com o uso ao nível da máquina virtual em que estas bases de dados estão contidas.

   ![Separador de utilização](./media/backup-azure-configure-backup-reports/usage.png)

> [!NOTE]
> No caso das cargas de trabalho de DPM, os utilizadores podem ver uma ligeira diferença (da ordem de 20 MB por servidor DPM) entre os valores de utilização indicados nos relatórios em comparação com o valor de utilização agregado, tal como mostrado no separador **Overview** do cofre dos Serviços de Recuperação. Esta diferença é explicada pelo facto de que todos os servidores DPM registados para cópia de segurança têm uma fonte de dados de metadados associada que não é surgiu como um artefacto para reportar.

##### <a name="jobs"></a>Tarefas

Utilize este separador para ver as tendências de longo prazo sobre os postos de trabalho, como o número de empregos falhados por dia e as principais causas de insuficiência de emprego. Pode ver estas informações tanto a um nível agregado como a um nível de backup-item. Selecione um item de cópia de segurança específico numa grelha para visualizar informações detalhadas sobre cada trabalho que foi ativado nesse item de Cópia de Segurança no intervalo de tempo selecionado.

   ![Guia de emprego](./media/backup-azure-configure-backup-reports/jobs.png)

##### <a name="policies"></a>Políticas

Utilize este separador para visualizar informações sobre todas as suas políticas ativas, tais como o número de itens associados e o armazenamento total de nuvem consumido por itens apoiados por uma determinada política. Selecione uma política específica para visualizar informações sobre cada um dos itens de Backup associados.

   ![Separador de políticas](./media/backup-azure-configure-backup-reports/policies.png)

##### <a name="optimize"></a>Otimizar

Use este separador para ganhar visibilidade em potenciais oportunidades de otimização de custos para as suas cópias de segurança. Seguem-se os cenários para os quais o separador Otimize fornece atualmente insights:

###### <a name="inactive-resources"></a>Recursos Inativos

Utilizando esta vista, pode identificar os itens de backup que não tenham tido uma cópia de segurança bem sucedida durante um período significativo de tempo. Isto pode significar que a máquina subjacente que está a ser apoiada já não existe (e por isso está a resultar em cópias de segurança falhadas), ou há algum problema com a máquina que está a impedir que as cópias de segurança sejam tomadas de forma fiável.

Para visualizar recursos inativos, navegue no **separador Otimize** e selecione o azulejo **Recursos Inativos.** Selecione este azulejo apresenta uma grelha que contém detalhes de todos os recursos inativos que existem no âmbito selecionado. Por predefinição, a grelha mostra itens que não têm um ponto de recuperação nos últimos sete dias. Para encontrar recursos inativos para um intervalo de tempo diferente, pode ajustar o filtro **de intervalo** de tempo na parte superior do separador.

Uma vez identificado um recurso inativo, pode investigar o problema mais adiante navegando no painel de instrumentos de cópia de segurança ou no painel de recursos Azure para esse recurso (sempre que aplicável). Dependendo do seu cenário, pode optar por parar a cópia de segurança da máquina (se já não existir) e eliminar cópias de segurança desnecessárias, o que poupa custos, ou pode corrigir problemas na máquina para garantir que as cópias de segurança são tomadas de forma fiável.

![Separador Otimizar - Recursos Inativos](./media/backup-azure-configure-backup-reports/optimize-inactive-resources.png)

###### <a name="backup-items-with-a-large-retention-duration"></a>Itens de cópia de segurança com uma grande duração de retenção

Utilizando esta vista, pode identificar os itens que têm cópias de segurança retidas por uma duração mais longa do que o exigido pela sua organização.

Selecionar o azulejo **de Otimizações de Política** seguido do azulejo de **Otimizações de Retenção** exibe uma grelha contendo todos os itens de backup para os quais a retenção do ponto de retenção diário, semanal, mensal ou anual (RP) é maior do que um valor especificado. Por predefinição, a grelha apresenta todos os itens de cópia de segurança no âmbito selecionado. Pode utilizar os filtros para a retenção diária, semanal, mensal e anual de RP para filtrar ainda mais a grelha e identificar os itens para os quais a retenção poderia potencialmente ser reduzida para economizar nos custos de armazenamento de backup.

Para cargas de trabalho de base de dados como SQL e SAP HANA, os períodos de retenção indicados na grelha correspondem aos períodos de retenção dos pontos de backup completos e não aos pontos de backup diferenciais. O mesmo se aplica aos filtros de retenção também.  

![Otimizar o separador - Otimizações de Retenção](./media/backup-azure-configure-backup-reports/optimize-retention.png)

###### <a name="databases-configured-for-daily-full-backup"></a>Bases de dados configuradas para cópia de segurança completa diária 

Utilizando esta vista, pode identificar cargas de trabalho de base de dados que foram configuradas para cópias de segurança diárias. Muitas vezes, usar backup diferencial diário juntamente com o backup completo semanal é mais rentável.

Selecionando o azulejo **de Otimizações de Políticas** seguido pelo azulejo de **otimizações** de agenda de backup exibe uma grelha contendo todas as bases de dados com uma política diária de backup completa. Pode optar por navegar para um determinado item de backup e modificar a política para utilizar a cópia de segurança diferencial diária com cópia de segurança total semanal.

O filtro **Do Tipo de Gestão de Cópias** de Segurança na parte superior do separador deve ter os itens **SQL em Azure VM** e **SAP HANA em Azure VM** selecionados, para que a grelha possa apresentar cargas de trabalho de base de dados como esperado.

![Otimizar o separador - Otimizações de Agenda de Backup](./media/backup-azure-configure-backup-reports/optimize-backup-schedule.png)

## <a name="export-to-excel"></a>Exportar para o Excel

Selecione o botão de seta para baixo no canto superior direito de qualquer widget, como uma tabela ou gráfico, para exportar o conteúdo desse widget como uma folha excel como-é com filtros existentes aplicados. Para exportar mais linhas de uma tabela para o Excel, pode aumentar o número de linhas apresentadas na página utilizando a seta de queda **rows Per Page** no topo de cada grelha.

## <a name="pin-to-dashboard"></a>Afixar ao dashboard

Selecione o botão pin na parte superior de cada widget para fixar o widget no seu painel de instrumentos do portal Azure. Esta funcionalidade ajuda-o a criar dashboards personalizados adaptados para exibir as informações mais importantes de que necessita.

## <a name="cross-tenant-reports"></a>Relatórios de inquilinos cruzados

Se utilizar [o Farol Azure](../lighthouse/index.yml) com acesso delegado a subscrições em vários ambientes de inquilinos, pode utilizar o filtro de subscrição predefinido. Selecione o botão de filtro no canto superior direito do portal Azure para escolher todas as subscrições para as quais deseja ver dados. Ao fazê-lo, permite-lhe selecionar espaços de trabalho do Log Analytics em todos os seus inquilinos para visualizar relatórios multitendidos.

## <a name="conventions-used-in-backup-reports"></a>Convenções utilizadas em relatórios de backup

- Os filtros funcionam da esquerda para a direita e de cima para baixo em cada separador. Ou seja, qualquer filtro só se aplica a todos os widgets que estão posicionados à direita desse filtro ou abaixo desse filtro.
- Selecionar um azulejo colorido filtra os widgets abaixo do azulejo para registos que dizem respeito ao valor desse azulejo. Por exemplo, selecionar o azulejo **'Protecção' Stop** no separador **Itens de Cópia de Segurança** filtra as grelhas e gráficos abaixo para mostrar dados para itens de cópia de segurança no estado de Proteção Parado.
- Azulejos que não são coloridos não são selecionáveis.
- Os dados do dia parcial atual não estão nos relatórios. Assim, quando o valor selecionado do Intervalo de **Tempo** é **o último de 7 dias,** o relatório mostra registos dos últimos sete dias concluídos. O dia de hoje não está incluído.
- O relatório mostra pormenores sobre os postos de trabalho (para além dos postos de trabalho) que foram *desencadeados* no intervalo de tempo selecionado.
- Os valores apresentados para **o armazenamento em nuvem** e **instâncias protegidas** estão no *final* do intervalo de tempo selecionado.
- Os itens de cópia de segurança apresentados nos relatórios são os itens que existem no *final* do intervalo de tempo selecionado. Os itens de cópia de segurança que foram eliminados no meio do intervalo de tempo selecionado não são apresentados. A mesma convenção aplica-se também às políticas de backup.

## <a name="query-load-times"></a>Tempos de carga de consulta

Os widgets do relatório Backup são alimentados por consultas kusto, que funcionam nos espaços de trabalho do Log Analytics do utilizador. Estas consultas normalmente envolvem o processamento de grandes quantidades de dados, com múltiplas junções para permitir insights mais ricos. Como resultado, os widgets podem não carregar instantaneamente quando o utilizador vê relatórios através de uma grande propriedade de backup. Esta tabela fornece uma estimativa aproximada do tempo que diferentes widgets podem levar a carregar, com base no número de itens de backup e no intervalo de tempo para o qual o relatório está a ser visto.

| **# Fontes de dados**                         | **Horizonte temporal** | **Tempos de carga aproximados**                                              |
| --------------------------------- | ------------- | ------------------------------------------------------------ |
| ~5 K                       | 1 mês          | Azulejos: 5-10 segundos <br> Grades: 5-10 segundos <br> Gráficos: 5-10 segundos <br> Filtros de nível de relatório: 5-10 segundos|
| ~5 K                       | 3 meses          | Azulejos: 5-10 segundos <br> Grades: 5-10 segundos <br> Gráficos: 5-10 segundos <br> Filtros de nível de relatório: 5-10 segundos|
| ~10 K                       | 3 meses          | Azulejos: 15-20 segundos <br> Grades: 15-20 segundos <br> Gráficos: 1-2 minutos <br> Filtros de nível de relatório: 25-30 segundos|
| ~15 K                       | 1 mês          | Azulejos: 15-20 segundos <br> Grades: 15-20 segundos <br> Gráficos: 50-60 segundos <br> Filtros de nível de relatório: 20-25 segundos|
| ~15 K                       | 3 meses          | Azulejos: 20-30 segundos <br> Grades: 20-30 segs <br> Gráficos: 2-3 minutos <br> Filtros de nível de relatório: 50-60 segundos |

## <a name="what-happened-to-the-power-bi-reports"></a> O que aconteceu aos relatórios do Power BI? 

- A aplicação anterior do modelo Power BI para reporte, que originou dados de uma conta de armazenamento Azure, encontra-se num caminho de depreciação. Recomendamos que comece a enviar dados de diagnóstico de abóbada para registar análises para visualizar relatórios.

- Além disso, o [esquema V1](./backup-azure-diagnostics-mode-data-model.md#v1-schema-vs-v2-schema) de enviar dados de diagnóstico para uma conta de armazenamento ou um espaço de trabalho de LA também está em um caminho de depreciação. Isto significa que se tiver escrito quaisquer consultas personalizadas ou automatizações com base no esquema V1, é aconselhável atualizar estas consultas para utilizar o esquema V2 atualmente suportado.

## <a name="next-steps"></a>Passos seguintes

[Saiba mais sobre monitorização e reporte com a Azure Backup](./backup-azure-monitor-alert-faq.md)
