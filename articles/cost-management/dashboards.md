---
title: Veja métricas-chave com dashboards da Cloudyn no Azure | Documentos da Microsoft
description: Este artigo descreve como pode ver as principais métricas com os dashboards no Cloudyn.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 03/14/2019
ms.topic: conceptual
ms.service: cost-management
manager: vitavor
ms.custom: seodec18
ms.openlocfilehash: 7ea7cbf4526528eb481b6125b006d0c29d86deb2
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58001069"
---
# <a name="view-key-cost-metrics-with-dashboards"></a>Chave de ver as métricas com os dashboards de custos

Dashboards no Cloudyn fornecem uma visão geral de relatórios. Dashboards permitem-lhe ver métricas de custo de chave numa única vista. Eles também fornecem a tendência comercial destaca-se para o ajudar a tomar decisões empresariais importantes.

Dashboards também são utilizados para criar vistas para pessoas com responsabilidades diferentes na sua organização, que pode incluir:

- Controlador financeiro
- Proprietário da aplicação ou projeto
- Engenheiro de DevOps
- Executivos

Dashboards são compostos de widgets e cada widget é essencialmente uma miniatura de relatório. Clique num widget para abrir o relatório. Quando personaliza os relatórios, guardá-los para os meus relatórios e estes serão adicionados ao dashboard.

Versões de dashboard são diferentes para os utilizadores de gestão (MSP), Enterprise e Premium Cloudyn. As diferenças são determinadas pela níveis de acesso de entidade. Para obter mais informações sobre níveis de acesso, consulte [níveis de acesso de entidade](tutorial-user-access.md#entity-access-levels).

Disponibilidade de dashboard depende do tipo de conta de fornecedor de serviço na cloud que é utilizado ao visualizar os dashboards. O tipo de informação disponível e recolhido pelo Cloudyn afeta relatórios nos dashboards. Por exemplo, se não tiver uma conta AWS, em seguida, não verá o dashboard do controlador de S3. Da mesma forma, se não ativar o acesso ao Cloudyn do Azure Resource Manager, em seguida, não verá quaisquer informações específicas do Azure em widgets do dashboard de otimizador.

Pode utilizar qualquer um dos dashboards pré-criados ou pode criar seu próprio dashboard com relatórios personalizados. Se não estiver familiarizado com os relatórios do Cloudyn, consulte [relatórios de utilização Cloudyn](use-reports.md).

## <a name="create-a-custom-dashboard"></a>Criar um dashboard personalizado

Para começar rapidamente com um dashboard personalizado, pode duplicar um já existente para utilizar as respetivas propriedades. Em seguida, pode modificá-lo para satisfazer as suas necessidades. No dashboard que pretende copiar, clique em **guardar como**. Pode duplicar apenas dashboards personalizados — não é possível duplicar os dashboards que estão incluídos no Cloudyn.

Para criar um dashboard personalizado:

1. Na home page, clique em **adicionar novo +**. É apresentada a página de meu painel.  
    ![Minha página de dashboard onde adiciona novos relatórios](./media/dashboards/my-dashboard.png)
2. Clique em **adicione o novo relatório**. É apresentada a caixa Adicionar relatório.
3. Selecione o relatório que pretende adicionar para o widget de dashboard. O widget é adicionado ao dashboard.
4. Repita os passos anteriores até está concluído, o dashboard.
5. Para alterar o nome do dashboard, clique no nome do dashboard na home page do Dashboard e escreva o novo nome.

## <a name="modify-a-custom-dashboard"></a>Modificar um dashboard personalizado

Como criar um dashboard personalizado, não é possível modificar dashboards que estão incluídos no Cloudyn. Para modificar um relatório de dashboard personalizado:

1. No dashboard, localizar o relatório que pretende modificar e clique em **editar**. O relatório é apresentado.
2. Efetue as alterações que pretende para o relatório e clique em **guardar**. O relatório é atualizado e apresenta as suas alterações.

## <a name="share-a-custom-dashboard"></a>Partilhar um dashboard personalizado

Pode partilhar um dashboard personalizado com outras pessoas _pública_ ou _minha entidade_. Quando partilha para público, todos os utilizadores podem ver o dashboard. Apenas os utilizadores com acesso à entidade atual podem ver o dashboard ao partilhar a minha entidade. Os passos para partilhar um dashboard personalizado com peering público e minha entidade são semelhantes.

Para partilhar um dashboard personalizado para público:

1. Num dashboard, clique em **definições do Dashboard**. É apresentada a caixa de definições do Dashboard.  
    ![definições do dashboard para um dashboard personalizado](./media/dashboards/dashboard-options.png)
2. Na caixa de definições do Dashboard, clique no símbolo de seta e, em seguida, clique em **público**. É apresentada a caixa de diálogo de confirmação de Dashboard público.
3. Clique em **Sim**. O dashboard está agora disponível para outros utilizadores.

## <a name="delete-a-custom-dashboard-report"></a>Eliminar um relatório de dashboard personalizado

Pode eliminar um componente de relatório personalizado a partir do dashboard. A eliminar o relatório a partir do dashboard não elimina o relatório na lista de relatórios. Em vez disso, a eliminar o relatório o remove do apenas o dashboard.

Para eliminar um componente de dashboard, no componente de dashboard, clique em **eliminar**. Clicar **eliminar** elimina imediatamente o componente de dashboard.

## <a name="share-a-dashboard-enterprise"></a>Partilhar um dashboard (Enterprise)

Pode partilhar dashboards personalizados para todos os utilizadores na sua organização ou com os utilizadores da entidade atual. Partilhar um dashboard pode permitir que outras pessoas uma rápida visão geral dos seus KPIS. Quando partilha um dashboard, replica automaticamente o dashboard para todos os seus Cloudyn entidades/clientes. As alterações para o dashboard partilhado são atualizadas automaticamente.

Para partilhar um dashboard com todos os utilizadores, incluindo subentidades:

1. Na home page do dashboard, clique em **editar**.
2. Clique em **partilha** e, em seguida, selecione **público**.
3. É apresentada a caixa de confirmação de Dashboard público Global.
4. Clique em **Sim** para definir o dashboard como um dashboard público global.

Para partilhar um dashboard com todos os utilizadores de uma entidade atual:

1. A partir da home page do Dashboard, clique em **editar**.
2. Clique em **partilha** e, em seguida, selecione **minha entidade**.
3. Clique em **Sim** para definir o dashboard como um dashboard público.

## <a name="duplicate-a-custom-dashboard"></a>Duplicar um dashboard personalizado

Quando cria um novo dashboard, pode querer utilizar propriedades semelhantes a partir de um dashboard existente. Pode duplicar dashboard para criar um novo.

Pode duplicar apenas dashboards personalizados. Não é possível duplicar dashboards padrão.

Duplicar dashboard (clona) personalizada:

1. No Dashboard que pretende duplicar, clique em **guardar como**. É aberto um novo dashboard com o mesmo nome e um número.
2. Mudar o nome do dashboard duplicado e modificá-lo como desejar.

-Ou-

1. Nas definições do Dashboard, clique em **guardar como** na linha do dashboard que pretende duplicar.
2. É aberto o dashboard duplicado.
3. Mudar o nome do dashboard e modificá-lo como desejar.

## <a name="set-a-default-dashboard"></a>Definir um dashboard predefinido

Pode definir qualquer dashboard como padrão. Defini-la como o padrão faz parecer como separador mais à esquerda da lista de guias de dashboard. O dashboard predefinido é apresentado quando abrir o portal da Cloudyn.

- Clique no separador de dashboard que pretende definir como padrão, em seguida, clique em **predefinição** à direita.

-Ou-

1. Clique em **definições do Dashboard** para ver a lista de dashboards disponíveis e selecione o dashboard que pretende definir como predefinição.  
    ![Opções de dashboard para um dashboard predefinido](./media/dashboards/dashboard-options.png)
2. Clique em **predefinido** na linha do dashboard. É apresentada a caixa de confirmação de Dashboard predefinido.
3. Clique em **Sim**. O dashboard é definido como predefinido.

## <a name="management-dashboard"></a>Dashboard de gestão
A gestão (ou dashboard MSP para utilizadores MSP) dashboard inclui destaques dos tipos de relatório principal.  
![Dashboard de gestão que mostra os vários relatórios](./media/dashboards/management-dash.png)

### <a name="cost-entity-summary-enterprise-only"></a>Resumo de entidade de custo (apenas para empresas)
Este widget resume as entidades de custo gerenciado, incluindo o número de entidades e o número de contas.
- Clique em do widget para abrir o relatório de detalhes do Enterprise.

### <a name="cost-over-time"></a>Custo ao longo do tempo
Este widget pode ajudá-lo a detetar tendências de custo. Destaca o custo para o último dia, com base na tendência dos últimos 30 dias.
- Clique em do widget para abrir o relatório de custo real ao longo do tempo para ver e filtrar os detalhes adicionais.

### <a name="asset-controller"></a>Controlador de recursos
Este widget destaca o número de instâncias em execução do dia anterior, acima a tendência de utilização ao longo dos últimos 30 dias.
- Clique em do widget para abrir o dashboard do controlador de recursos.

### <a name="unused-ri-detector"></a>Detetor de RI não utilizado
Este widget destaca o número de Amazon EC2 reservas não utilizadas.
- Clique em do widget para abrir o relatório de reservas atualmente não utilizadas, onde pode ver os montantes não utilizados reservas pode modificar.

### <a name="cost-by-service"></a>Custo por serviço
Este widget destaca os custos amortizados pelo serviço para os últimos 30 dias. Paire o rato sobre o gráfico de pizza para ver os custos por serviço.
- Clique em do widget para abrir o relatório de análise de custo real.

### <a name="potential-savings"></a>Potenciais poupanças
Este widget mostra o tipo de instância preços recomendações para o Amazon EC2 e RDS do Amazon.
- Clique em abrir o widget do relatório de análise de economia. Esta tabela indica os custos por tipos de instância com a economia em potencial.

### <a name="compute-instances---daily-trend"></a>Instâncias de computação - tendência diária
Este widget mostra as instâncias ativas por tipo, nos últimos 30 dias.
- Clique em do widget para abrir o relatório de instâncias ao longo do tempo, onde pode ver uma análise detalhada de todas as instâncias em execução durante os últimos 30 dias.

### <a name="storage-by-department"></a>Armazenamento por departamento
Este widget apresenta os serviços de armazenamento utilizados pelos departamentos. Paire o rato sobre o gráfico de pizza para ver o consumo de armazenamento por departamento.
- Clique em do widget para abrir o dashboard do controlador de S3.

## <a name="cost-controller-dashboard"></a>Dashboard do controlador de custos
O dashboard do controlador de custos mostra destaques de alocação de custos predefinidas.  
![Dashboard do controlador que mostra os vários relatórios de custos](./media/dashboards/cost-controller-dashboard.png)

### <a name="cost-over-time"></a>Custo ao longo do tempo
Este widget ajuda-o a detetar tendências de custo. Destaca o custo para o último dia, com base na tendência dos últimos 30 dias.
- Clique em do widget para abrir o relatório de custo real ao longo do tempo para ver e filtrar os detalhes adicionais.

### <a name="monthly-cost-trends"></a>Tendências de custos mensais
Este widget destaca gastos amortizado prevista e gasta o seu real desde o início do mês.
- Clique em do widget para abrir o relatório de custo previsto do mês atual, que fornece um resumo de custo de mês até à data.

Este relatório mostra o custo do início do mês, o custo do mês anterior e o custo previsto do mês. O mês atual custo previsto é calculado ao adicionar o custo mensal atualizado e projeção. A projeção é baseada no custo monitorizado durante os últimos 30 dias.

### <a name="12-month-planner"></a>Planeador de implementações de 12 meses
Este widget destaca os custos estimados ao longo dos próximos doze meses e a economia potencial.
- Clique em do widget para abrir o relatório de custo previsto anual.

### <a name="cost-by-service"></a>Custo por serviço
Este widget destaca os custos amortizados pelo serviço para os últimos 30 dias.
- Paire o rato sobre o gráfico de pizza para ver os custos por serviço.
- Clique em do widget para abrir o relatório de análise de custo real.

### <a name="cost-by-account"></a>Custo por conta
Este widget destaca os custos amortizados por conta dos últimos 30 dias.
- Paire o rato sobre o gráfico de pizza para ver os custos por conta.
- Clique em do widget para abrir o relatório de análise de custo real.

### <a name="cost-trend-by-day"></a>Tendência de custo por dia
Este widget destaques gastar durante os últimos 30 dias.
- Paire o rato sobre o gráfico de barras para ver os custos por dia.
- Clique em do widget para abrir o relatório de custo real ao longo do tempo.

### <a name="cost-trend-by-month---last-6-months"></a>Tendência de custo por mês - últimos 6 meses

Este widget destaques gastar durante os últimos seis meses.
- Paire o rato sobre o gráfico de barras para ver os custos por mês.
- Clique em do widget para abrir o relatório de custo real ao longo do tempo.

## <a name="asset-controller-dashboard"></a>Dashboard do controlador ativo

Este dashboard mostra o número de instâncias, discos disponíveis e em utilização, distribuição de tipos de instância e informações de armazenamento a executar.  
![Dashboard do controlador ativo que mostra os vários relatórios](./media/dashboards/asset-controller-dashboard.png)

### <a name="compute-instances"></a>Instâncias de computação
Este widget mostra o número de instâncias de base a tendência de utilização ao longo dos últimos 30 dias em execução.
- Clique em do widget para abrir o relatório de instâncias ao longo do tempo.

### <a name="disks"></a>Discos
Este widget destaca o número total e o volume de discos, que estão em utilização e disponível.
- Clique em do widget para abrir o relatório de discos ativos.

### <a name="instance-type-distribution"></a>Distribuição de tipo de instância
Este widget realça os tipos de instância num gráfico de pizza.
- Clique no widget para abrir o relatório de distribuição de instância, que fornece uma análise detalhada das instâncias de Active Directory, a agregação selecionada.

### <a name="compute-instances---daily-trend"></a>Instâncias de computação - tendência diária
Este widget destaca as instâncias de computação (spot, reservadas e sob demanda) por dia nos últimos 30 dias.
- Paire o rato sobre o gráfico para ver o número de instâncias de computação, por tipo por dia.
- Clique em do widget para abrir o relatório de instâncias ao longo do tempo.

### <a name="all-buckets-s3"></a>Todos os registos (S3)
Este widget destaca o total de armazenamento S3 e o número de objetos armazenados.
- Clique em do widget para abrir o Dashboard do controlador de S3. O dashboard ajuda a localizar, analisar e apresentar a sua utilização de armazenamento atual e tendências.

### <a name="sql-db-instances-rds"></a>Instâncias de DB do SQL (RDS)
Este widget destaca o número de instâncias de Amazon RDS com base na tendência dos últimos 30 dias em execução.
- Clique em do widget para abrir o relatório de RDS instância ao longo do tempo.

## <a name="optimizer-dashboard"></a>Dashboard de otimizador
Este dashboard apresenta recomendações downsizing, recursos não utilizados e economia potencial.  
![Dashboard de otimizador que mostra os vários relatórios](./media/dashboards/optimizer-dashboard.png)

### <a name="ri-calculator"></a>Calculadora de RI
Este widget mostra o número de recomendações de compra de RI e realça as poupanças anuais potenciais.
- Clique em do widget para abrir a Calculadora de instância reservada onde pode determinar quando deve utilizar a pedido versus reservado planos de preços.

### <a name="sizing"></a>Dimensionamento
Este widget destaca o dimensionamento recomendado e a economia em potencial, se implementada.
- Clique para abrir o relatório de recomendações de dimensionamento económico EC2 widget.

### <a name="unused-ri-detector"></a>Detetor de RI não utilizado
Este widget destaca o número de Amazon EC2 reservas não utilizadas.
- Clique em do widget para abrir o relatório de reservas atualmente não utilizadas, onde pode ver as reservas não utilizadas que pode modificar.

###  <a name="available-disks"></a>Discos disponíveis
Este widget destaca o número de discos desanexados na sua implementação.
- Clique para abrir o relatório de discos Desanexados widget.

### <a name="rds-ri-calculator"></a>Calculadora de RI de RDS
Este widget destaca o número de recomendações de reserva para as instâncias de RDS do Amazon e a economia potencial.
- Clique em do widget para abrir o relatório de recomendações de compra de RI de RDS em que pode ver as recomendações do Cloudyn para utilizar instâncias reservadas em vez de instâncias a pedido.

### <a name="rds-sizing"></a>Dimensionamento de RDS
Este widget mostra o número de recomendações de dimensionamento e a economia potencial.
- Clique em do widget para abrir o relatório de recomendações de dimensionamento de RDS, que mostra detalhadas RDS Amazon recomendações de dimensionamento.

As recomendações de otimização baseiam-se nos dados de utilização e desempenho monitorizados no último mês.

## <a name="s3-tracker-dashboard"></a>Dashboard de controlador de S3
O dashboard do controlador de S3 ajuda a localizar, analisar e apresentar a sua utilização de armazenamento atual e tendências.  
![Dashboard de controlador de S3 que mostra os vários relatórios](./media/dashboards/s3-tracker-dashboard.png)

### <a name="all-buckets"></a>Todos os registos
Este widget destaca o tamanho total de todos os seus registos, em GB e o número total de objetos nos seus registos.
- Clique em do widget para abrir o relatório de tamanho de distribuição de S3. O relatório ajuda a analisar o tamanho de S3 por bucket, pasta de nível superior, a classe de armazenamento e estado de controle de versão.

### <a name="bucket-properties"></a>Propriedades de registo
Este widget destaca o número total de registos de armazenamento.
- Clique em do widget para ver o relatório de propriedades de registo de S3.

### <a name="scan-status"></a>Estado da análise
Este widget destaca quando a última análise de S3 foi concluída e quando a próxima sessão será iniciada.
- Clique em do widget para abrir o relatório de estado de análise de S3.

### <a name="storage-by-bucket"></a>Armazenamento por Bucket
Este widget realça a percentagem que cada classe de armazenamento de registo está a utilizar.
- Clique em do widget para abrir o relatório de tamanho de distribuição de S3. O relatório ajuda a analisar o tamanho de S3 por bucket, pasta de nível superior, a classe de armazenamento e estado de controle de versão.

### <a name="number-of-objects-by-bucket"></a>Número de objetos por Bucket
Este widget destaca o número de objetos por bucket no número real e a percentagem. Paire o rato sobre o registo para ver o total de objetos.
- Clique em do widget para abrir o relatório de tamanho de distribuição de S3 (análise com base).

## <a name="cloud-comparison-dashboard"></a>Dashboard de comparação de cloud
O dashboard de comparação na Cloud ajuda-o a comparar os custos de fornecedores de cloud diferentes com base nos preços, o tipo de CPU e o tamanho de RAM.  
![Dashboard de comparação de cloud que mostra vários relatórios](./media/dashboards/cloud-comparison-dashboard.png)

### <a name="ec2-cost-in-azure-by-instance-type"></a>EC2 Custo no Azure por tipo de instância
Este widget destaca os últimos 30 dias de utilização nas taxas de sob demanda. Ele compara o custo com os vs atuais do custo de Amazon EC2 o potencial de custos no Azure.
- Paire o rato sobre as barras para comparar os custos por tipo de instância.
- Clique em do widget para abrir a migrar a implementação – relatório de análise de custo.

### <a name="ec2-cost-in-azure"></a>EC2 Custo no Azure
Este widget mostra os custos de Amazon EC2 atuais e compara-os para o Azure. A comparação baseia-se nos últimos 30 dias de utilização nas taxas de sob demanda.
- Clique em do widget para abrir a migrar a implementação - relatório de análise de custo.

### <a name="ec2azure-instance-type-mapping"></a>Mapeamento de tipo de instância EC2/do Azure
Este widget destaca o melhor mapeamento de unidades de computação elástica entre Amazon EC2 e o Azure.
- Clique em do widget para abrir o relatório de mapeamento de tipo de instâncias.

## <a name="next-steps"></a>Passos Seguintes
- Leitura a [relatórios de utilização Cloudyn](use-reports.md) artigo para saber mais sobre os relatórios.
