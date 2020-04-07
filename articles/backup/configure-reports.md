---
title: Configurar relatórios do Azure Backup
description: Configure e veja relatórios para backup azure usando Log Analytics e Livros de Trabalho Azure
ms.topic: conceptual
ms.date: 02/10/2020
ms.openlocfilehash: 497d70c1bcc577faa467720b959eb828e785a26a
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/06/2020
ms.locfileid: "80672669"
---
# <a name="configure-azure-backup-reports"></a>Configurar relatórios do Azure Backup

Um requisito comum para os administradores de backup é obter insights sobre backups, com base em dados que abrangem um longo período de tempo. Poderia haver múltiplos casos de utilização para tal solução - alocação e previsão de armazenamento em nuvem consumida, auditoria de backups e restauros, e identificação de tendências-chave em diferentes níveis de granularidade.

Hoje, a Azure Backup fornece uma solução de reporte que aproveita os [Registos do Monitor Azure](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal) e os Livros de [Trabalho Azure,](https://docs.microsoft.com/azure/azure-monitor/app/usage-workbooks)ajudando-o a obter informações ricas sobre os seus backups em toda a sua propriedade de backup. Este artigo explica como configurar e ver Relatórios de Backup.

## <a name="supported-scenarios"></a>Cenários suportados

* Os Relatórios de Backup são suportados para VMs Azure, SQL em VMs Azure, SAP HANA/ASE em VMs Azure, Agente de Backup Azure (MARS), Servidor de Backup Azure (MABS) e System Center DPM.
* Para cargas de trabalho DPM, os Relatórios de Backup são suportados para dPM versão 5.1.363.0 ou superior, e Verdete agente 2.0.9127.0 ou superior.
* Para cargas de trabalho MABS, os Relatórios de Backup são suportados para a versão 13.0.415.0 e superior do Agente, e para a Versão Agente 2.0.9170.0 ou superior.
* Os Relatórios de Backup podem ser vistos em todos os itens de backup, cofres, subscrições e regiões desde que os seus dados sejam enviados para um Espaço de Trabalho de Log Analytics (LA) a que o utilizador tenha acesso. Note que para ver relatórios para um conjunto de cofres, basta ter acesso dos **leitores ao espaço** de trabalho de LA para o qual os cofres estão a enviar os seus dados. **Não precisas de** ter acesso aos cofres individuais.
* Se é um utilizador do [Farol Azure](https://docs.microsoft.com/azure/lighthouse/) com acesso delegado às subscrições dos seus clientes, pode utilizar estes relatórios com o Farol Azure para ver relatórios em todos os seus inquilinos.
* Os dados relativos a trabalhos de backup de registo não são atualmente apresentados nos relatórios.

## <a name="getting-started"></a>Introdução

Para começar com a utilização dos relatórios, siga os três passos descritos abaixo:

1. **Criar um espaço de trabalho log analytics (LA) (ou utilizar um existente):**

Precisa de configurar um ou mais espaços de trabalho de LA para armazenar os seus dados de relatórios de backup. A localização e subscrição onde este espaço de trabalho de LA pode ser criado é independente da localização e subscrição onde existem os seus cofres. 

Consulte o seguinte artigo: Crie um espaço de trabalho de [Log Analytics no portal Azure](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace) para criar um espaço de trabalho de LA.

Por padrão, os dados num espaço de trabalho de LA são conservados por 30 dias. Para ver dados para um horizonte temporal mais longo, mude o período de retenção do Espaço de Trabalho de LA. Para alterar o período de retenção, consulte o seguinte artigo: Gerir a [utilização e os custos com os Registos do Monitor Azure](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage).

2. **Configure as definições de diagnóstico para os seus cofres:**

Os recursos do Gestor de Recursos Azure, tais como cofres de Serviços de Recuperação, gravam informações sobre operações programadas e operações desencadeadas pelo utilizador como dados de diagnóstico. 

Na secção de monitorização do cofre dos Serviços de Recuperação, selecione **definições** de diagnóstico e especifique o alvo para os dados de diagnóstico do cofre do cofre dos Serviços de Recuperação. [Saiba mais sobre a utilização de eventos de diagnóstico.](https://docs.microsoft.com/azure/backup/backup-azure-diagnostic-events)

![Lâmina de definições de diagnóstico](./media/backup-azure-configure-backup-reports/resource-specific-blade.png)

O Azure Backup também fornece uma Política Azure incorporada, que automatiza a configuração de configurações de diagnóstico para todos os cofres num dado âmbito. Consulte o seguinte artigo para aprender a usar esta política: [Configure Configurações](https://docs.microsoft.com/azure/backup/azure-policy-configure-diagnostics) de Diagnóstico de Cofre em escala

> [!NOTE]
> Uma vez configurado os diagnósticos, pode demorar até 24 horas para que o impulso inicial de dados seja concluído. Uma vez que os dados começam a fluir para o Espaço de Trabalho de LA, você pode não ser capaz de ver os dados nos relatórios imediatamente, uma vez que os dados para o dia parcial atual não são mostrados nos relatórios (mais detalhes [aqui).](https://docs.microsoft.com/azure/backup/configure-reports#conventions-used-in-backup-reports) Assim, recomenda-se começar a visualizar os relatórios 2 dias após configurar os seus cofres para enviar dados para o Log Analytics.

3. **Ver relatórios no portal Azure:**

Depois de configurar os seus cofres para enviar dados para LA, veja os seus relatórios de backup navegando para a lâmina de qualquer cofre e clicando no item do menu **Backup Reports.** 

![Painel do Cofre](./media/backup-azure-configure-backup-reports/vault-dashboard.png)

Clicar neste link abre o Livro de Relatórios de Cópia de Segurança.

> [!NOTE]
> * Atualmente, a carga inicial do relatório pode demorar até 1 minuto.
> * O cofre dos Serviços de Recuperação é apenas um ponto de entrada para os Relatórios de Backup. Assim que o Livro de Trabalho de Relatórios de Backup se abrir a partir da lâmina de um cofre, poderá ver os dados agregados em todos os seus cofres (selecionando o conjunto apropriado de espaços de trabalho de LA).

Abaixo está uma descrição dos vários separadores que o relatório contém:

* **Resumo** - O separador Resumo fornece uma visão geral de alto nível da sua propriedade de reserva. Sob o separador Resumo, você pode obter uma olhada rápida do número total de itens de backup, armazenamento total em nuvem consumido, o número de instâncias protegidas e a taxa de sucesso de trabalho por tipo de carga de trabalho. Para obter informações mais detalhadas em torno de um tipo específico de artefacto de reserva, navegue para os respetivos separadores.

![Separador Resumo](./media/backup-azure-configure-backup-reports/summary.png)

* **Itens de backup** - O separador 'Backup Itens' permite-lhe ver informações e tendências no armazenamento em nuvem consumidas a um nível de Backup Item. Por exemplo, se estiver a utilizar o SQL na cópia de segurança Azure VM, pode ver que o armazenamento em nuvem consumido por cada base de dados SQL está a ser apoiado. Também pode optar por ver dados para itens de backup de um determinado estado de proteção. Por exemplo, clicando no azulejo **de proteção parado** na parte superior do separador, filtra todos os widgets abaixo para mostrar dados apenas para itens de backup em estado de proteção parado.

![Separador de itens de backup](./media/backup-azure-configure-backup-reports/backup-items.png)

* **Utilização** - O separador Usage ajuda-o a visualizar os parâmetros de faturação chave para as suas cópias de segurança. As informações mostradas neste separador estão ao nível de uma entidade de faturação (recipiente protegido). Por exemplo, no caso de um servidor DPM ser apoiado até ao Azure, pode ver a tendência de instâncias protegidas e armazenamento em nuvem consumido para o servidor DPM. Da mesma forma, se estiver a utilizar o SQL em Backup Azure ou sAP HANA em Backup Azure, este separador fornece-lhe informações relacionadas com o uso ao nível da máquina virtual em que estas bases de dados estão contidas.

![Separador de utilização](./media/backup-azure-configure-backup-reports/usage.png)

* **Jobs** - O separador Jobs permite-lhe ver as tendências de longo prazo em matéria de emprego, como o número de empregos fracassados por dia e as principais causas de insuficiência de emprego. Pode ver esta informação tanto a um nível agregado como a um nível de item de reserva. Clicar num determinado item de backup numa grelha permite-lhe visualizar informações detalhadas sobre cada trabalho que foi desencadeado nesse item de backup no intervalo de tempo selecionado.

![Separador de empregos](./media/backup-azure-configure-backup-reports/jobs.png)

* **Políticas** - O separador Políticas permite-lhe visualizar informações sobre todas as suas políticas ativas, como o número de itens associados, e o armazenamento total em nuvem consumido por itens apoiados numa determinada política. Clicar numa determinada política permite-lhe visualizar informações sobre cada um dos seus itens de backup associados.

![Separador de políticas](./media/backup-azure-configure-backup-reports/policies.png)

## <a name="exporting-to-excel"></a>Exportação para Excel

Clicar no botão de seta para baixo na parte superior direita de qualquer widget (tabela/gráfico) exporta o conteúdo desse widget como folha Excel, como é com os filtros existentes aplicados. Para exportar mais linhas de uma tabela para o Excel, pode aumentar o número de linhas exibidas na página utilizando o dropdown **Rows Per Page** no topo de cada grelha.

## <a name="pinning-to-dashboard"></a>Fixação ao Painel de Instrumentos

Clique no Ícone Pin na parte superior de cada widget para fixar o widget no seu painel de instrumentos do portal Azure. Isto ajuda-o a criar dashboards personalizados à medida para mostrar as informações mais importantes de que necessita.

## <a name="cross-tenant-reports"></a>Relatórios de inquilinos cruzados

Se for um utilizador do [Farol Azure](https://docs.microsoft.com/azure/lighthouse/) com acesso delegado a subscrições em vários ambientes de inquilinos, pode utilizar o filtro de subscrição predefinido (clicando no ícone do filtro no canto superior direito do portal Azure) para escolher todas as subscrições que deseja ver dados. Ao fazê-lo, permitir-lhe-á selecionar espaços de trabalho de LA em todos os seus inquilinos para ver relatórios multi-inquilinos.

## <a name="conventions-used-in-backup-reports"></a>Convenções utilizadas em Relatórios de Backup

* Os filtros funcionam da esquerda para a direita e de cima para baixo em cada separador, ou seja, qualquer filtro aplica-se apenas a todos os widgets que estão posicionados à direita desse filtro ou abaixo desse filtro. 
* Clicar num azulejo colorido filtra os widgets abaixo do azulejo para registos relativos ao valor desse azulejo. Por exemplo, clicar no azulejo *Descarregador* de Proteção no separador 'Backup Itens' filtra as grelhas e gráficos abaixo para mostrar dados relativos a itens de backup em estado 'Protection Stop'.
* Os azulejos que não são coloridos não são clicáveis.
* Os dados do dia parcial atual não são mostrados nos relatórios. Assim, quando o valor selecionado do Intervalo de **Tempo** é, ***Últimos 7 dias,*** o relatório mostra registos dos últimos 7 dias concluídos (que não incluem o dia atual).
* O relatório mostra detalhes de Jobs (para além dos trabalhos de registo) que foram **desencadeados** no intervalo de tempo selecionado. 
* Os valores apresentados para armazenamento em nuvem e instâncias protegidas, estão no **final** da gama de tempo selecionada.
* Os itens de backup apresentados nos relatórios são os itens que existem no **final** do intervalo de tempo selecionado. Não são apresentados itens de backup que tenham sido eliminados no meio do intervalo de tempo selecionado. A mesma convenção também se aplica às Políticas de Backup.

## <a name="query-load-times"></a>Tempos de carga de consulta

Os widgets no Relatório de Backup são alimentados por consultas Kusto, que funcionam nos espaços de trabalho de LA do utilizador. Como estas consultas normalmente envolvem o processamento de grandes quantidades de dados, com múltiplas juntas para permitir insights mais ricos, os widgets podem não carregar instantaneamente quando o utilizador está a ver relatórios através de uma grande propriedade de backup. O quadro abaixo fornece uma estimativa aproximada do tempo que diferentes widgets podem levar para carregar, com base no número de itens de backup e no intervalo de tempo para o qual o relatório está a ser visto:

| **# Fontes de dados**                         | **Horizonte temporal** | **Tempos de carga (aprox.)**                                              |
| --------------------------------- | ------------- | ------------------------------------------------------------ |
| ~5 K                       | 1 mês          | Azulejos: 5-10 segundos <br> Grelhas: 5-10 segundos <br> Gráficos: 5-10 segs <br> Filtros de nível de relatório: 5-10 segundos|
| ~5 K                       | 3 meses          | Azulejos: 5-10 segundos <br> Grelhas: 5-10 segundos <br> Gráficos: 5-10 segs <br> Filtros de nível de relatório: 5-10 segundos|
| ~10 K                       | 3 meses          | Azulejos: 15-20 segundos <br> Grelhas: 15-20 segundos <br> Gráficos: 1-2 minutos <br> Filtros de nível de relatório: 25-30 segundos|
| ~15 K                       | 1 mês          | Azulejos: 15-20 segundos <br> Grelhas: 15-20 segundos <br> Gráficos: 50-60 segs <br> Filtros de nível de relatório: 20-25 segs|
| ~15 K                       | 3 meses          | Azulejos: 20-30 segundos <br> Grelhas: 20-30 segundos <br> Gráficos: 2-3 minutos <br> Filtros de nível de relatório: 50-60 segundos |

## <a name="what-happened-to-the-power-bi-reports"></a>O que aconteceu com os Relatórios do Power BI?
* A nossa anterior aplicação de modelo supreende Power BI para reportagem (que produziu dados de uma Conta de Armazenamento Azure) está numa trajetória de depreciação. Recomenda-se começar a enviar dados de diagnóstico de cofre para o Log Analytics, conforme descrito acima, para visualizar relatórios.

* Além disso, o esquema V1 de enviar dados de diagnóstico para uma conta de armazenamento ou um espaço de trabalho de LA também está em uma rota de depreciação. Isto significa que se tiver escrito quaisquer consultas ou automatizações personalizadas com base no esquema V1, é aconselhável atualizar estas consultas para utilizar o esquema V2 atualmente suportado.

## <a name="next-steps"></a>Passos seguintes
[Saiba mais sobre monitorização e reportagem com O Backup Azure](https://docs.microsoft.com/azure/backup/backup-azure-monitor-alert-faq)