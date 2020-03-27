---
title: Ver as métricas principais nos dashboards da Cloudyn no Azure
description: Este artigo descreve como pode ver as métricas principais nos dashboards da Cloudyn.
author: bandersmsft
ms.author: banders
ms.date: 03/12/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.reviewer: vitavor
ms.custom: seodec18
ROBOTS: NOINDEX
ms.openlocfilehash: 892df9a6e0eb4d791f818eed0a78c96a829e25a0
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "79464228"
---
# <a name="view-key-cost-metrics-with-dashboards"></a>Ver as métricas principais dos custos nos dashboards

Os dashboards da Cloudyn fornecem uma vista de alto nível dos relatórios. Os dashboards permitem ver as métricas principais dos custos numa única vista. Também apresentam os destaques das tendências empresariais para o ajudar a tomar decisões empresariais importantes.

[!INCLUDE [cloudyn-note](../../../includes/cloudyn-note.md)]

Os dashboards também são utilizados para criar vistas para pessoas com diferentes responsabilidades na sua organização, que podem incluir:

- Controlador financeiro
- Proprietário da aplicação ou do projeto
- Engenheiro de DevOps
- Executivos

Os dashboards são constituídos por widgets e cada widget é essencialmente uma miniatura de relatório. Clique num widget para abrir o relatório. Quando personalizar os relatórios, poderá guardá-los em Os Meus Relatórios e, assim, serem adicionados ao dashboard.

As versões do dashboard diferem para os utilizadores da Cloudyn de Gestão (MSP), Enterprise e Premium. As diferenças são determinadas pelos níveis de acesso de entidade. Para obter mais informações sobre os níveis de acesso, veja [Níveis de acesso de entidade](tutorial-user-access.md#entity-access-levels).

A disponibilidade dos dashboards depende do tipo de conta do fornecedor de serviços cloud utilizada na visualização dos dashboards. O tipo de informação disponível e recolhida pela Cloudyn afeta os relatórios nos dashboards. Por exemplo, se não tiver uma conta do AWS, não verá o dashboard Controlador S3. Da mesma forma, se não permitir o acesso do Azure Resource Manager à Cloudyn, não verá nenhuma informação específica do Azure nas miniaplicações de dashboard Otimizador.

Pode utilizar qualquer um dos dashboards pré-criados ou criar o seu próprio dashboard com relatórios personalizados. Se não estiver familiarizado com os relatórios da Cloudyn, veja [Utilizar relatórios da Cloudyn](use-reports.md).

## <a name="create-a-custom-dashboard"></a>Criar um dashboard personalizado

Para começar a utilizar rapidamente um dashboard personalizado, pode duplicar um existente para utilizar as suas propriedades. Em seguida, pode modificá-lo de acordo com as suas necessidades. No dashboard que quer copiar, clique em **Guardar Como**. Só pode duplicar os dashboards personalizados, não pode duplicar os dashboards incluídos na Cloudyn.

Para criar um dashboard personalizado:

1. Na home page, clique em **Adicionar Novo +** . É apresentada a página O Meu Dashboard.  
    ![página O meu dashboard onde adiciona novos relatórios](./media/dashboards/my-dashboard.png)
2. Clique em **Adicionar Novo Relatório**. É apresentada a caixa Adicionar Relatório.
3. Selecione o relatório que quer adicionar à miniaplicação de dashboard. O widget é adicionado ao dashboard.
4. Repita os passos anteriores até que o dashboard esteja concluído.
5. Para alterar o nome do dashboard, clique no nome do dashboard na home page do Dashboard e escreva o novo nome.

## <a name="modify-a-custom-dashboard"></a>Modificar um dashboard personalizado

Tal como criar um dashboard personalizado, não pode modificar os dashboards incluídos na Cloudyn. Para modificar um relatório personalizado do dashboard:

1. No dashboard, procure o relatório que quer modificar e clique em **Editar**. O relatório é apresentado.
2. Faça as alterações desejadas no relatório e clique em **Guardar**. O relatório é atualizado e apresenta as alterações feitas.

## <a name="share-a-custom-dashboard"></a>Partilhar um dashboard personalizado

Pode partilhar um dashboard personalizado com outras pessoas com _Público_ ou _A Minha Entidade_. Quando partilha com Público, todos os utilizadores podem ver o dashboard. Apenas os utilizadores com acesso à entidade atual podem ver o dashboard quando partilhar com A Minha Entidade. Os passos para partilhar um dashboard personalizado com Público e A Minha Entidade são semelhantes.

Para partilhar um dashboard personalizado com Público:

1. Num dashboard, clique em **Definições do Dashboard**. É apresentada a caixa Definições do Dashboard.  
    ![definições de um dashboard personalizado](./media/dashboards/dashboard-options.png)
2. Na caixa Definições do Dashboard, clique no símbolo de seta e, em seguida, clique em **Público**. É apresentada a caixa de diálogo de confirmação Dashboard Público.
3. Clique em **Sim**. O dashboard está agora disponível para outras pessoas.

## <a name="delete-a-custom-dashboard-report"></a>Eliminar um relatório personalizado do dashboard

Pode eliminar um componente de relatório personalizado do dashboard. Eliminar o relatório do dashboard não o remove da lista de relatórios. Em vez disso, a eliminação do relatório remove-o apenas do dashboard.

Para eliminar um componente do dashboard, clique em **Eliminar** no componente do dashboard. Clicar em **Eliminar** elimina imediatamente o componente do dashboard.

## <a name="share-a-dashboard-enterprise"></a>Partilhar um dashboard (Enterprise)

Pode partilhar dashboards personalizados com todos os utilizadores da sua organização ou com os utilizadores da entidade atual. Partilhar um dashboard pode dar às outras pessoas uma vista rápida de alto nível do seu KPI. Quando partilha um dashboard, replica automaticamente o dashboard em todas as entidades/clientes da Cloudyn. As alterações ao dashboard partilhado são atualizadas automaticamente.

Para partilhar um dashboard com todos os utilizadores, incluindo subentidades:

1. Na home page do dashboard, clique em **Editar**.
2. Clique em **Partilhar** e, em seguida, selecione **Público**.
3. É apresentada a caixa de confirmação Dashboard Público Global.
4. Clique em **Sim** para definir o dashboard como público global.

Para partilhar um dashboard com todos os utilizadores da entidade atual:

1. Na home page do Dashboard, clique em **Editar**.
2. Clique em **Partilhar** e, em seguida, selecione **A Minha Entidade**.
3. Clique em **Sim** para definir o dashboard como público.

## <a name="duplicate-a-custom-dashboard"></a>Duplicar um dashboard personalizado

Quando criar um novo dashboard, é melhor utilizar propriedades semelhantes de um dashboard existente. Pode duplicar o dashboard para criar um novo.

Só pode duplicar os dashboards personalizados. Não pode duplicar os dashboards padrão.

Para duplicar (clonar) um dashboard personalizado:

1. No Dashboard que quer duplicar, clique em **Guardar Como**. É aberto um novo dashboard com o mesmo nome e um número.
2. Mude o nome do dashboard duplicado e modifique-o como quiser.

-Ou-

1. Em Definições do Dashboard, clique em **Guardar Como** na linha do dashboard que quer duplicar.
2. É aberto o dashboard duplicado.
3. Mude o nome do dashboard e modifique-o como quiser.

## <a name="set-a-default-dashboard"></a>Definir um dashboard predefinido

Pode definir qualquer dashboard como a sua predefinição. Se for definido como a sua predefinição, será apresentado como o separador mais à esquerda na lista de separadores do dashboard. O dashboard predefinido é apresentado ao abrir o portal da Cloudyn.

- Clique no separador do dashboard que definir como o predefinido e, em seguida, clique em **Predefinição** à direita.

-Ou-

1. Clique em **Definições do Dashboard** para ver a lista de dashboards disponíveis e selecione o dashboard que definir como o predefinido.  
    ![opções para um dashboard predefinido](./media/dashboards/dashboard-options.png)
2. Clique em **Predefinição** na linha do dashboard. É apresentada a caixa de confirmação Dashboard Predefinido.
3. Clique em **Sim**. O dashboard está predefinido.

## <a name="management-dashboard"></a>Dashboard de gestão
O dashboard Gestão (ou dashboard MSP para os utilizadores do MSP) inclui destaques dos principais tipos de relatório.  
![Dashboard Gestão a mostrar vários relatórios](./media/dashboards/management-dash.png)

### <a name="cost-entity-summary-enterprise-only"></a>Resumo da Entidade de Custos (apenas Enterprise)
Este widget resume as entidades dos custos geridas, incluindo o número de entidades e o número de contas.
- Clique no widget para abrir o relatório Detalhes da Empresa.

### <a name="cost-over-time"></a>Custos ao Longo do Tempo
Este widget pode ajudá-lo a detetar tendências de custos. Destaca o custo do último dia, com base na tendência dos últimos 30 dias.
- Clique no widget para abrir o relatório Custos Reais ao Longo do Tempo para ver e filtrar detalhes adicionais.

### <a name="asset-controller"></a>Controlador de Recursos
Este widget destaca o número de instâncias em execução do dia anterior, acima da tendência de utilização nos últimos 30 dias.
- Clique no widget para abrir o dashboard Controlador de Recursos.

### <a name="unused-ri-detector"></a>Detetor de RIs não utilizadas
Este widget destaca o número de reservas não utilizadas do Amazon EC2.
- Clique no widget para abrir o relatório Reservas Atualmente Não Utilizadas, onde pode ver as reservas não utilizadas que pode modificar.

### <a name="cost-by-service"></a>Custo por Serviço
Este widget destaca os custos amortizados por serviço nos últimos 30 dias. Paire sobre o gráfico circular para ver os custos por serviço.
- Clique no widget para abrir o relatório Análise de Custos Reais.

### <a name="potential-savings"></a>Potenciais poupanças
Este widget mostra recomendações de preços de tipos de instância para o Amazon EC2 e o Amazon RDS.
- Clique no widget para abrir o relatório Análise de Poupanças. Lista os custos por tipos de instância com as potenciais poupanças.

### <a name="compute-instances---daily-trend"></a>Instâncias de Computação – Tendência Diária
Este widget apresenta as instâncias ativas por tipo, nos últimos 30 dias.
- Clique no widget para abrir o relatório Instâncias ao Longo do Tempo, onde pode ver uma discriminação de todas as instâncias em execução durante os últimos 30 dias.

### <a name="storage-by-department"></a>Armazenamento por departamento
Este widget apresenta os serviços de armazenamento utilizados pelos departamentos. Paire sobre o gráfico circular para ver o consumo de armazenamento por departamento.
- Clique no widget para abrir o dashboard Controlador S3.

## <a name="cost-controller-dashboard"></a>Dashboard Controlador de Custos
O dashboard Controlador de Custos mostra os destaques de alocação de custos predefinidos.  
![Dashboard Controlador de Custos a mostrar vários relatórios](./media/dashboards/cost-controller-dashboard.png)

### <a name="cost-over-time"></a>Custos ao Longo do Tempo
Este widget ajuda-o a detetar tendências de custos. Destaca o custo do último dia, com base na tendência dos últimos 30 dias.
- Clique no widget para abrir o relatório Custos Reais ao Longo do Tempo para ver e filtrar detalhes adicionais.

### <a name="monthly-cost-trends"></a>Tendências dos Custos Mensais
Este widget destaca as despesas amortizadas previstas e os gastos reais desde o início do mês.
- Clique no widget para abrir o relatório Custos Previstos do Mês Atual, que fornece um resumo de custos mensal.

Este relatório mostra o custo desde o início do mês, o custo do mês anterior e o custo previsto do mês atual. O custo previsto do mês atual é calculado ao adicionar o custo mensal atualizado e a projeção. A projeção baseia-se no custo monitorizado nos últimos 30 dias.

### <a name="12-month-planner"></a>Planeador de 12 Meses
Este widget destaca os custos previstos ao longo dos próximos 12 meses e as potenciais poupanças.
- Clique no widget para abrir o relatório Custos Previstos Anuais.

### <a name="cost-by-service"></a>Custo por Serviço
Este widget destaca os custos amortizados por serviço nos últimos 30 dias.
- Paire sobre o gráfico circular para ver os custos por serviço.
- Clique no widget para abrir o relatório Análise de Custos Reais.

### <a name="cost-by-account"></a>Custo por Conta
Este widget destaca os custos amortizados por conta nos últimos 30 dias.
- Paire sobre o gráfico circular para ver os custos por conta.
- Clique no widget para abrir o relatório Análise de Custos Reais.

### <a name="cost-trend-by-day"></a>Tendência de Custos por Dia
Este widget destaca os gastos nos últimos 30 dias.
- Paire sobre o gráfico de barras para ver os custos por dia.
- Clique no widget para abrir o relatório Custos Reais ao Longo do Tempo.

### <a name="cost-trend-by-month---last-6-months"></a>Tendência de Custos por Mês – Últimos 6 meses

Este widget destaca os gastos nos últimos seis meses.
- Paire sobre o gráfico de barras para ver os custos por mês.
- Clique no widget para abrir o relatório Custos Reais ao Longo do Tempo.

## <a name="asset-controller-dashboard"></a>Dashboard Controlador de Recursos

Este dashboard apresenta o número de instâncias em execução, discos disponíveis e em utilização, distribuição de tipos de instância e informações de armazenamento.  
![Dashboard Controlador de Recursos a mostrar vários relatórios](./media/dashboards/asset-controller-dashboard.png)

### <a name="compute-instances"></a>Instâncias de Computação
Este widget apresenta o número de instâncias em execução com base na tendência de utilização dos últimos 30 dias.
- Clique no widget para abrir o relatório Instâncias ao Longo do Tempo.

### <a name="disks"></a>Discos
Este widget destaca o número total e o volume de discos em utilização e disponíveis.
- Clique no widget para abrir o relatório Discos Ativos.

### <a name="instance-type-distribution"></a>Distribuição do Tipo de Instâncias
Este widget destaca os tipos de instâncias num gráfico circular.
- Clique no widget para abrir o relatório Distribuição de Instâncias, que fornece uma discriminação das instâncias ativas pela agregação selecionada.

### <a name="compute-instances---daily-trend"></a>Instâncias de Computação – Tendência Diária
Este widget destaca as instâncias de computação (pontuais, reservadas e a pedido) por dia durante os últimos 30 dias.
- Paire sobre o gráfico para ver o número de instâncias de computação, por tipo e por dia.
- Clique no widget para abrir o relatório Instâncias ao Longo do Tempo.

### <a name="all-buckets-s3"></a>Todos os Registos (S3)
Este widget destaca o armazenamento S3 total e o número de objetos armazenados.
- Clique no widget para abrir o dashboard Controlador S3. O dashboard ajuda-o a procurar, analisar e apresentar as tendências e a utilização de armazenamento atual.

### <a name="sql-db-instances-rds"></a>Instâncias da BD SQL (RDS)
Este widget destaca o número de instâncias do Amazon RDS em execução com base na tendência dos últimos 30 dias.
- Clique no widget para abrir o relatório Instâncias do RDS ao Longo do Tempo.

## <a name="optimizer-dashboard"></a>Dashboard Otimizador
Este dashboard apresenta recomendações de redução, recursos não utilizados e as potenciais poupanças.  
![Dashboard Otimizador a mostrar vários relatórios](./media/dashboards/optimizer-dashboard.png)

### <a name="ri-calculator"></a>Calculadora de RIs
Este widget apresenta o número de recomendações de compra de RIs e destaca as potenciais poupanças anuais.
- Clique no widget para abrir a Calculadora de Instâncias Reservadas, onde pode determinar quando utilizar planos de preços a pedido vs. reservados.

### <a name="sizing"></a>Dimensionamento
Este widget destaca o dimensionamento recomendado e as potenciais poupanças, se implementado.
- Clique no widget para abrir o relatório Recomendações de Dimensionamento Rentável do EC2.

### <a name="unused-ri-detector"></a>Detetor de RIs não utilizadas
Este widget destaca o número de reservas não utilizadas do Amazon EC2.
- Clique no widget para abrir o relatório Reservas Atualmente Não Utilizadas, onde pode ver as reservas não utilizadas que pode modificar.

###  <a name="available-disks"></a>Discos Disponíveis
Este widget destaca o número de discos desanexados na sua implementação.
- Clique no widget para abrir o relatório Discos Desanexados.

### <a name="rds-ri-calculator"></a>Calculadora de RIs do RDS
Este widget destaca o número de recomendações de reserva para as instâncias do Amazon RDS e as potenciais poupanças.
- Clique no widget para abrir o relatório Recomendações de Compra de RIs do RDS, onde pode ver as recomendações da Cloudyn para utilizar instâncias reservadas em vez de instâncias a pedido.

### <a name="rds-sizing"></a>Dimensionamento do RDS
Este widget mostra o número de recomendações de dimensionamento e as potenciais poupanças.
- Clique no widget para abrir o relatório Recomendações de Dimensionamento do RDS, que apresenta recomendações de dimensionamento detalhadas do Amazon RDS.

As recomendações de otimização baseiam-se na utilização e nos dados de desempenho monitorados do último mês.

## <a name="s3-tracker-dashboard"></a>Dashboard Controlador S3
O dashboard Controlador S3 ajuda-o a procurar, analisar e apresentar as tendências e a utilização de armazenamento atual.  
![Dashboard Controlador S3 a mostrar vários relatórios](./media/dashboards/s3-tracker-dashboard.png)

### <a name="all-buckets"></a>Todos os Registos
Este widget destaca o tamanho total de todos os registos, em GB, e o número total de objetos nos registos.
- Clique no widget para abrir o relatório Distribuição do Tamanho S3. O relatório ajuda-o a analisar o tamanho S3 por registo, pasta de nível superior, classe de armazenamento e estado de controlo de versões.

### <a name="bucket-properties"></a>Propriedades de Registo
Este widget destaca o número total de registos de armazenamento.
- Clique no widget para ver o relatório Propriedades de Registo do S3.

### <a name="scan-status"></a>Estado de Análise
Este widget destaca quando foi realizada a última análise S3 e quando terá início a próxima.
- Clique no widget para abrir o relatório Estado de Análise do S3.

### <a name="storage-by-bucket"></a>Armazenamento por Registo
Este widget destaca a percentagem que cada classe de armazenamento de registo está a utilizar.
- Clique no widget para abrir o relatório Distribuição do Tamanho S3. O relatório ajuda-o a analisar o tamanho S3 por registo, pasta de nível superior, classe de armazenamento e estado de controlo de versões.

### <a name="number-of-objects-by-bucket"></a>Número de Objetos por Registo
Este widget destaca o número de objetos por registo em número real e percentagem. Paire sobre o registo para ver o total de objetos.
- Clique no widget para abrir o relatório Distribuição do Tamanho S3 (baseado na Análise).

## <a name="cloud-comparison-dashboard"></a>Dashboard Comparação de Clouds
O dashboard Comparação de Clouds ajuda-o a comparar os custos de diferentes fornecedores de cloud com base no preço, tipo de CPU e tamanho da RAM.  
![Dashboard Comparação de Clouds a mostrar vários relatórios](./media/dashboards/cloud-comparison-dashboard.png)

### <a name="ec2-cost-in-azure-by-instance-type"></a>Custo do EC2 no Azure por Tipo de Instância
Este widget destaca os últimos 30 dias de utilização em taxas a pedido. Compara o custo com o custo atual vs. o custo potencial do Amazon EC2 no Azure.
- Paire sobre as barras para comparar os custos por tipo de instância.
- Clique no widget para abrir o relatório Transferir a Implementação – Análise de Custos.

### <a name="ec2-cost-in-azure"></a>Custo do EC2 no Azure
Este widget mostra os custos atuais do Amazon EC2 e compara-os com o Azure. A comparação baseia-se nos últimos 30 dias de utilização em taxas de procura.
- Clique no widget para abrir o relatório Transferir a Implementação – Análise de Custos.

### <a name="ec2azure-instance-type-mapping"></a>Mapeamento de Tipos de Instâncias do EC2/Azure
Este widget destaca o melhor mapeamento de unidades de computação elástica entre o Amazon EC2 e o Azure.
- Clique no widget para abrir o relatório Mapeamento de Tipos de Instâncias.

## <a name="next-steps"></a>Passos seguintes
- Leia o artigo [Utilizar relatórios da Cloudyn](use-reports.md) para saber mais sobre os relatórios.
