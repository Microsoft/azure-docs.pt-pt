---
title: 'Aplicação de saas: Monitorizar o desempenho de muitas bases de dados SQL do Azure | Documentos da Microsoft'
description: Monitorizar e gerir o desempenho de bases de dados SQL do Azure e conjuntos numa aplicação SaaS multi-inquilino
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
manager: craigg
ms.date: 01/25/2019
ms.openlocfilehash: df54f9dd4047fffb578a1a95a2edc47cba711ba1
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57433523"
---
# <a name="monitor-and-manage-performance-of-azure-sql-databases-and-pools-in-a-multi-tenant-saas-app"></a>Monitorizar e gerir o desempenho de bases de dados SQL do Azure e conjuntos numa aplicação SaaS multi-inquilino

Neste tutorial, serão explorados vários cenários de gerenciamento de chave de desempenho utilizados nas aplicações SaaS. Utilizar um gerador de carga para simular atividade em todas as bases de dados do inquilino, a monitorização incorporada e funcionalidades de base de dados SQL e conjuntos elásticos de alerta são demonstrados.

A aplicação Wingtip Tickets SaaS da base de dados por inquilino utiliza um modelo de dados de inquilino único, onde cada local (inquilino) tem sua própria base de dados. Como em muitas aplicações SaaS, o padrão de carga de trabalho do inquilino antecipado é imprevisível e esporádico. Por outras palavras, as vendas de bilhetes podem ocorrer em qualquer altura. Para tirar partido deste padrão de utilização típica da base de dados, bases de dados de inquilinos são implementadas em conjuntos elásticos. Os conjuntos elásticos otimizam o custo de uma solução ao partilhar recursos em muitas bases de dados. Com este tipo de padrão, é importante monitorizar a utilização das bases de dados e dos recursos dos conjuntos para confirmar que as cargas estão razoavelmente equilibradas entre os conjuntos. Também tem de garantir que as bases de dados individuais têm os recursos adequados e que os conjuntos não estão a atingir os seus limites [eDTU](sql-database-purchase-models.md#dtu-based-purchasing-model). Este tutorial analisa formas de monitorizar e gerir bases de dados e conjuntos e como tomar uma medida corretiva em resposta a variações na carga de trabalho.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]

> * Simular a utilização nas bases de dados de inquilinos ao executar um gerador de carga fornecido
> * Monitorizar as bases de dados de inquilinos à medida que a carga aumenta
> * Aumentar verticalmente o Conjunto elástico em resposta ao aumento de carga da base de dados
> * Aprovisionar um segundo Conjunto elástico para balancear a carga da atividade da base de dados


Para concluir este tutorial, confirme que conclui os pré-requisitos seguintes:

* A aplicação Wingtip Tickets SaaS da base de dados por inquilino é implementada. Para implementar em menos de cinco minutos, veja [implementar e explorar a aplicação Wingtip Tickets SaaS da base de dados por inquilino](saas-dbpertenant-get-started-deploy.md)
* O Azure PowerShell está instalado. Para obter mais detalhes, veja [Introdução ao Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps)

## <a name="introduction-to-saas-performance-management-patterns"></a>Introdução aos padrões de gestão do desempenho de SaaS

Gerir o desempenho da base de dados consiste em compilar e analisar dados de desempenho e, em seguida, reagir a estes dados ajustando os parâmetros para manter um tempo de resposta aceitável para a aplicação. Ao alojar vários inquilinos, conjuntos elásticos são uma maneira econômica de fornecer e gerir os recursos de um grupo de bases de dados com cargas de trabalho imprevisíveis. Com determinados padrões de carga de trabalho, um mínimo de duas bases de dados S3 pode beneficiar ao serem geridas num conjunto.

![Diagrama de aplicação](./media/saas-dbpertenant-performance-monitoring/app-diagram.png)

Os conjuntos e as bases de dados em conjuntos, devem ser monitorizados para garantir que eles permaneçam nos intervalos de desempenho aceitáveis. Otimize a configuração do pool para satisfazer as necessidades da carga de trabalho agregada de todas as bases de dados, garantindo que as eDTUs de conjunto são adequadas para a carga de trabalho geral. Ajuste os valores eDTU mínimo e máximo por cada base de dados para adequar os valores aos requisitos da aplicação específica.

### <a name="performance-management-strategies"></a>Estratégias da gestão do desempenho

* Para evitar ter de monitorizar manualmente o desempenho, é mais eficaz **definir alertas que acionam quando ou conjuntos de bases de dados se afastarem dos intervalos normais**.
* Para responder a flutuações de curta duração no agregado, tamanho de computação de um conjunto, o **nível de eDTU do conjunto pode ser aumentado vertical ou horizontalmente**. Se esta flutuação ocorrer numa base regular ou previsível, **o dimensionamento do conjunto poderá ser agendado para ocorrer automaticamente**. Por exemplo, reduza verticalmente quando sabe que a carga de trabalho é leve, talvez durante a noite ou durante os fins de semana.
* Para responder a flutuações de duração mais longa ou alterações no número de bases de dados, **as bases de dados individuais podem ser movidas para outros grupos**.
* Para responder a aumentos de curto prazo na *individuais* carga da base de dados **bases de dados individuais podem ser retiradas do conjunto e atribuídas um tamanho de computação individual**. Assim que a carga for reduzida, a base de dados pode ser devolvida ao conjunto. Quando isso é conhecido antecipadamente, o bases de dados podem ser movidas previamente para garantir que a base de dados tem sempre os recursos de que necessita e para evitar o impacto nas outras bases de dados no conjunto. Se este requisito for previsível, por exemplo, quando um local tem uma grande procura de bilhetes para um evento popular, este comportamento de gestão poderá ser integrado na aplicação.

O [portal do Azure](https://portal.azure.com) fornece monitorização e alertas incorporados na maior parte dos recursos. Na Base de Dados SQL, a monitorização e os alertas estão disponíveis nas bases de dados e nos conjuntos. Estes alertas e monitorização incorporados são específicos dos recursos, portanto, é útil para utilização em pequenos números de recursos, mas não é muito útil quando trabalha com muitos recursos.

Para cenários de volume elevado, em que trabalha com muitos recursos, [registos do Azure Monitor](saas-dbpertenant-log-analytics.md) pode ser utilizado. Este é um serviço do Azure separado que fornece análises sobre os registos de diagnóstico emitidos e a telemetria recolhida na área de trabalho do Log Analytics. Registos de Monitor do Azure podem recolher telemetria de muitos serviços e ser utilizados para consultar e definir alertas.

## <a name="get-the-wingtip-tickets-saas-database-per-tenant-application-scripts"></a>Obter os scripts de aplicativo da Wingtip Tickets SaaS da base de dados por inquilino

Os scripts de base de dados do Wingtip Tickets SaaS multi-inquilino e o código de origem da aplicação estão disponíveis no [WingtipTicketsSaaS DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) repositório do GitHub. Veja a [orientações gerais](saas-tenancy-wingtip-app-guidance-tips.md) para obter os passos transferir e os scripts de Wingtip Tickets SaaS de desbloqueio.

## <a name="provision-additional-tenants"></a>Aprovisionar inquilinos adicionais

Apesar de os conjuntos poderem ser económicos com apenas duas bases de dados S3, quantas mais bases de dados estiverem no conjunto mais económico será o efeito em média. Para uma boa compreensão de como funciona a monitorização e a gestão do desempenho à escala, este tutorial requer que tenha, pelo menos, 20 bases de dados implementadas.

Se já tiver aprovisionado um lote de inquilinos num tutorial anterior, avance para o [simular utilização em todas as bases de dados de inquilinos](#simulate-usage-on-all-tenant-databases) secção.

1. Na **ISE do PowerShell**, abra... \\Módulos de aprendizagem\\monitoramento de desempenho e gerenciamento\\*Demo-PerformanceMonitoringAndManagement.ps1*. Mantenha este script aberto, uma vez que vai executar vários cenários durante este tutorial.
1. Defina **$DemoScenario** = **1**, **Aprovisionar um lote de inquilinos**
1. Prima **F5** para executar o script.

O script implementará 17 inquilinos em menos de cinco minutos.

O *New-TenantBatch* script usa um conjunto ligado ou aninhado de [Gestor de recursos](../azure-resource-manager/index.yml) modelos que cria um lote de inquilinos, que, por predefinição, copia a base de dados **basetenantdb**no servidor de catálogo para criar o novo inquilino bases de dados, em seguida, regista estes no catálogo e, finalmente, inicializa-os com o tipo de nome e o local do inquilino. Isso é consistente com a forma como a aplicação aprovisiona um novo inquilino. Todas as alterações efetuadas *basetenantdb* são aplicadas a quaisquer novos inquilinos aprovisionados a partir de então. Consulte a [tutorial de gestão de esquemas](saas-tenancy-schema-management.md) para ver como efetuar alterações de esquema ao *existente* bases de dados de inquilino (incluindo o *basetenantdb* base de dados).

## <a name="simulate-usage-on-all-tenant-databases"></a>Simular a utilização em todas as bases de dados de inquilinos

O *Demo-PerformanceMonitoringAndManagement.ps1* script é fornecida que simula uma carga de trabalho em execução em todas as bases de dados do inquilino. A carga é gerada usando um dos cenários de carga disponíveis:

| Demonstração | Cenário |
|:--|:--|
| 2 | Gerar carga de intensidade normal (aproximadamente 40 DTUS) |
| 3 | Gerar carga com picos mais demorados e mais frequentes por base de dados|
| 4 | Gerar carga com picos de DTU mais altos por base de dados (aproximadamente 80 DTUS)|
| 5 | Gerar uma carga normal + uma carga elevada num inquilino individual (aproximadamente 95 DTUS)|
| 6 | Gerar carga desequilibrada em vários conjuntos|

O gerador de carga aplica uma carga *sintética* só na CPU para cada base de dados do inquilino. O gerador inicia uma tarefa para cada base de dados de inquilino, que chama um procedimento armazenado que periodicamente gera a carga. Os níveis de carga (em eDTUs), a duração e os intervalos são diversificados em todas as bases de dados, o que simula a atividade imprevisível do inquilino.

1. Na **ISE do PowerShell**, abra... \\Módulos de aprendizagem\\monitoramento de desempenho e gerenciamento\\*Demo-PerformanceMonitoringAndManagement.ps1*. Mantenha este script aberto, uma vez que vai executar vários cenários durante este tutorial.
1. Definir **$DemoScenario** = **2**, *gerar carga de intensidade normal*.
1. Prima **F5** para aplicar uma carga a todas as bases de dados de inquilinos.

A Wingtip Tickets SaaS da base de dados por inquilino é uma aplicação SaaS e a carga do mundo real numa aplicação SaaS é, normalmente, esporádica e imprevisível. Para simular isto, o gerador de carga produz uma carga aleatória distribuída por todos os inquilinos. São necessários vários minutos para o padrão de carga a surgir, assim, executar o gerador de carga durante 3 a 5 minutos antes de tentar monitorizar a carga nas seções a seguir.

> [!IMPORTANT]
> O gerador de carga está em execução como uma série de tarefas na sessão local do PowerShell. Mantenha aberto o separador *Demo-PerformanceMonitoringAndManagement.ps1*! Se fechar o separador ou suspender o computador, o gerador de carga será interrompido. O gerador de carga continuará a ser um *invocar a tarefa* Estado em que gera carga de quaisquer novos inquilinos aprovisionados após o gerador de início. Uso *Ctrl-C* para parar a invocar novas tarefas e sair do script. O gerador de carga continua em execução, mas somente em inquilinos existentes.

## <a name="monitor-resource-usage-using-the-azure-portal"></a>Monitorizar a utilização de recursos com o portal do Azure

Para monitorizar a utilização de recursos resultantes da carga que está a ser aplicada, abra o portal para o conjunto que contém as bases de dados do inquilino:

1. Abra o [portal do Azure](https://portal.azure.com) e navegue para o *tenants1-dpt -&lt;utilizador&gt;*  server.
1. Desloque-se para baixo, localize os conjuntos elásticos e clique em **Pool1**. Este conjunto contém todas as bases de dados de inquilinos criadas até à data.

Observe a **monitorização do conjunto elástico** e **bases de dados elásticas monitorização** gráficos.

Utilização de recursos do conjunto é a utilização de agregação da base de dados para todas as bases de dados no conjunto. O gráfico de base de dados mostra as cinco bases de dados mais interessantes:

![gráfico de base de dados](./media/saas-dbpertenant-performance-monitoring/pool1.png)

Como existem bases de dados adicionais no conjunto além os cinco principais, a utilização do conjunto mostra a atividade que não é refletida no gráfico de cinco bancos de dados superior. Para obter mais detalhes, clique em **utilização de recursos de bases de dados**:

![utilização de recursos de bases de dados](./media/saas-dbpertenant-performance-monitoring/database-utilization.png)


## <a name="set-performance-alerts-on-the-pool"></a>Definir alertas de desempenho no conjunto

Definir um alerta no conjunto acionado \>75% de utilização da seguinte forma:

1. Open *Pool1* (no *tenants1-dpt -\<utilizador\>*  servidor) no [portal do Azure](https://portal.azure.com).
1. Clique em **Regras de Alerta**e, em seguida, clique em **+ Adicionar alerta**:

   ![adicionar alerta](media/saas-dbpertenant-performance-monitoring/add-alert.png)

1. Forneça um nome, tal como **DTU elevada**,
1. Defina os seguintes valores:
   * **Métrica = percentagem de eDTU**
   * **Condição = maior que**
   * **Limiar = 75**
   * **Período = nos últimos 30 minutos**
1. Adicionar um endereço de e-mail para o *adicionais do administrador email(s)* caixa e clique em **OK**.

   ![alerta de definição](media/saas-dbpertenant-performance-monitoring/alert-rule.png)


## <a name="scale-up-a-busy-pool"></a>Aumentar verticalmente um conjunto ocupado

Se o nível de carga agregado aumentar num conjunto até ao ponto máximo do conjunto e atingir a utilização de 100% de eDTU, o desempenho da base de dados individual será afetado, o que potencialmente abranda os tempos de resposta de consulta de todas as bases de dados no conjunto.

**Curta duração**, considere aumentar verticalmente o conjunto para fornecer recursos adicionais ou remover bases de dados do conjunto (movê-las para outros conjuntos ou para fora do conjunto para um escalão de serviço autónomo).

**Longo prazo**, considere a otimização das consultas ou utilização de índices para melhorar o desempenho da base de dados. Dependendo da sensibilidade da aplicação relativamente a problemas de desempenho, a melhor prática para aumentar verticalmente um conjunto antes de atingir os 100% de utilização de eDTU. Utilize um alerta para avisá-lo antecipadamente.

Pode simular um conjunto ocupado ao aumentar a carga produzida pelo gerador. Fazendo com que as bases de dados de expandir mais tempo, com mais frequência e para aumentar a carga agregada no conjunto sem alterar os requisitos das bases de dados individuais. O aumento vertical do conjunto é feito facilmente no portal ou a partir do PowerShell. Este exercício utiliza o portal.

1. Defina *$DemoScenario* = **3**, _Gerar carga com picos mais demorados e mais frequentes por base de dados_ para aumentar a intensidade da carga agregada no conjunto sem alterar a carga de pico necessária para cada base de dados.
1. Prima **F5** para aplicar uma carga a todas as bases de dados de inquilinos.

1. Aceda a **Pool1** no portal do Azure.

Monitorize a utilização de eDTU do conjunto de aumento no gráfico superior. Demora alguns minutos para que a nova carga superior surgir, mas pode ver rapidamente o conjunto de começar a alcançar uma utilização máxima e que a carga estabiliza num novo padrão, que rapidamente sobrecarrega o conjunto.

1. Para aumentar verticalmente o conjunto, clique em **configurar conjunto** na parte superior a **Pool1** página.
1. Ajustar a **eDTU do conjunto** definição **100**. Alterar a eDTU do conjunto não altera as definições por base de dados (que é ainda o máximo de 50 eDTU por base de dados). Pode ver as definições por base de dados no lado direito do **configurar conjunto** página.
1. Clique em **guardar** para submeter o pedido para dimensionar o conjunto.

Volte ao **Pool1** > **descrição geral** para ver os gráficos de monitorização. Monitorize o efeito de fornecer o conjunto com mais recursos (embora com algumas bases de dados e uma carga aleatória não seja sempre fácil ver as consequências até executar durante algum tempo). Enquanto estiver a visualizar os gráficos tenha em atenção que 100% no gráfico superior representa agora 100 eDTUs, enquanto no gráfico inferior 100% é ainda 50 eDTUs, uma vez que o máximo por base de dados ainda está em 50 eDTUs.

As bases de dados permanecem online e estão totalmente disponíveis durante o processo. No último momento, à medida que cada base de dados fica pronta para ser ativada com a nova eDTU de conjunto, todas as ligações ativas são perdidas. O código da aplicação deve ser sempre escrito para repetir as ligações que caem e, por conseguinte, voltar a ligar à base de dados no conjunto aumentado verticalmente.

## <a name="load-balance-between-pools"></a>Balanceamento de carga entre conjuntos

Como alternativa ao aumento vertical do conjunto, crie um segundo conjunto e mova as bases de dados para o conjunto para balancear a carga entre os dois conjuntos. Para fazê-lo, o novo conjunto tem de ser criado no mesmo servidor do primeiro conjunto.

1. Na [portal do Azure](https://portal.azure.com), abra o **tenants1-dpt -&lt;utilizador&gt;**  server.
1. Clique em **+ novo conjunto** para criar um conjunto no servidor atual.
1. Sobre o **conjunto elástico** modelo:

    1. Definir **Name** ao *Pool2*.
    1. Deixe o escalão de preço como **Conjunto Padrão**.
    1. Clique em **Configurar conjunto**.
    1. Definir **eDTU do conjunto** ao *50 Edtus*.
    1. Clique em **adicionar bases de dados** para ver uma lista de bases de dados no servidor que pode ser adicionado ao *Pool2*.
    1. Selecione qualquer 10 bancos de dados movê-las para o novo conjunto e, em seguida, clique em **selecione**. Se esteve executando o gerador de carga, o serviço já sabe que o seu perfil de desempenho requer um conjunto maior do que o tamanho do padrão de 50 Edtus e recomenda começando com uma definição de eDTU de 100.

    ![recomendação](media/saas-dbpertenant-performance-monitoring/configure-pool.png)

    1. Para este tutorial, deixe a predefinição em 50 eDTUs e clique em **selecione** novamente.
    1. Selecione **OK** para criar o conjunto de novas e para mover bases de dados selecionadas para o mesmo.

A criar o conjunto e mover as bases de dados demora alguns minutos. Como as bases de dados são movidos permanecem online e totalmente acessível até ao último muito momento, altura em que todas as ligações abertas são fechadas. Desde que tiver alguma lógica de repetição, clientes, em seguida, irão ligar à base de dados do novo conjunto.

Navegue até **Pool2** (no *tenants1-dpt -\<utilizador\>*  server) para abrir o conjunto e monitorizar o desempenho dele. Se não o vir, aguarde que o aprovisionamento do novo conjunto para concluir.

Ver agora que a utilização de recursos em *Pool1* caiu e que *Pool2* agora é carregado da mesma forma.

## <a name="manage-performance-of-an-individual-database"></a>Gerir o desempenho de uma base de dados individual

Se uma base de dados individual num conjunto sofrer uma carga elevada constante, dependendo da configuração de agrupamento, poderá ter tendência para dominar os recursos no conjunto e afetar as outras bases de dados. Se a atividade é provável que continue durante algum tempo, a base de dados pode ser movido temporariamente para fora do conjunto. Isso permite que o banco de dados para os recursos extras que necessita e isola-a partir de outras bases de dados.

Este exercício simula o efeito de uma carga elevada em Contoso Concert Hall, quando são colocados à venda os bilhetes para um concerto popular.

1. Na **ISE do PowerShell**, abra o... \\ *Demo-PerformanceMonitoringAndManagement.ps1* script.
1. Definir **$DemoScenario = 5, gerar uma carga normal + uma carga elevada num inquilino individual (aproximadamente 95 DTUS).**
1. Defina **$SingleTenantDatabaseName = contosoconcerthall**
1. Execute o script com **F5**.


1. Na [portal do Azure](https://portal.azure.com), navegue para a lista de bases de dados no *tenants1-dpt -\<utilizador\>*  server. 
1. Clique nas **contosoconcerthall** base de dados.
1. Clique no conjunto que **contosoconcerthall** é na. Localize o conjunto no **conjunto elástico** secção.

1. Inspecionar a **monitorização do conjunto elástico** do gráfico e procure a utilização de eDTU do conjunto de maior. Depois de um minuto ou dois, o aumento de carga deve começar a surgir e deve poder ver rapidamente que o conjunto atinge os 100% de utilização.
2. Inspecionar a **bases de dados elásticas monitorização** apresentar, que mostra as bases de dados mais interessantes na hora anterior. O *contosoconcerthall* base de dados deverá ser logo apresentada como uma das cinco bases de dados mais interessantes.
3. **Clique na monitorização de base de dados elástica** **gráfico** e será aberta a **utilização de recursos de bases de dados** página onde pode monitorizar qualquer uma das bases de dados. Isto permite-lhe isolar a apresentação para o *contosoconcerthall* base de dados.
4. Na lista de bases de dados, clique em **contosoconcerthall**.
5. Clique em **escalão de preço (Dimensionar DTUs)** para abrir o **configurar o desempenho** página onde pode definir um tamanho de computação autónomo para a base de dados.
6. Clique no separador **Padrão** para abrir as opções de dimensionamento na camada Padrão.
7. Deslize o **controlo de deslize da DTU** para a direita para selecionar **100** DTUs. Isso corresponde ao objetivo de serviço, de observação **S3**.
8. Clique em **aplicar** para mover a base de dados para fora do conjunto e torná-lo um *Standard S3* base de dados.
9. Depois de concluída a dimensionar, monitorize o efeito na base de dados contosoconcerthall e Pool1 nos painéis de agrupamento e a base de dados elásticos.

Depois de ter a carga elevada na base de dados contosoconcerthall deverá devolvê-la ao conjunto para reduzir o seu custo. Se não está claro quando que irá acontecer, poderá configurar um alerta na base de dados que irá acionar quando a sua utilização de DTUS desce abaixo o por base de dados máximo no conjunto. Veja o exercício 5, para saber como mover uma base de dados para um conjunto.

## <a name="other-performance-management-patterns"></a>Outros padrões de gestão de desempenho

**Dimensionamento preventivas** no exercício acima onde explorou como dimensionar uma base de dados isolada, sabia que banco de dados para procurar. Se a gestão de Contoso Concert Hall tivesse informado Wingtips de venda de bilhetes iminente, a base de dados poderia ter sido movida para fora do conjunto previamente. Caso contrário, seria provavelmente necessário um alerta no conjunto ou na base de dados para detetar o que estava a acontecer. Não gostaria de ter conhecimento desta situação através dos outros inquilinos no conjunto a reclamarem de uma degradação do desempenho. E se o inquilino pode prever por quanto tempo serão necessários recursos adicionais, pode configurar um runbook de Automatização do Azure para mover a base de dados para fora do conjunto e, em seguida, fazê-la voltar numa agenda definida.

**Dimensionamento de self-service do inquilino** – Porque o dimensionamento é uma tarefa chamada facilmente através de API de gestão, pode criar facilmente a capacidade de dimensionar bases de dados de inquilino na sua aplicação direcionada para o inquilino e oferecê-la como uma funcionalidade do serviço SaaS. Por exemplo, permita que os inquilinos administrem automaticamente o aumento ou redução vertical, talvez ligando-os diretamente à faturação deles!

**Aumentar um conjunto e reduzir verticalmente com base numa agenda de acordo com padrões de utilização**

Quando a utilização agregada de inquilinos segue padrões de utilização previsível, pode utilizar a Automatização do Azure para aumentar ou reduzir verticalmente um conjunto com base numa agenda. Por exemplo, reduza verticalmente um conjunto após as 18:00 e aumente-o verticalmente antes das 06:00 nos dias da semana quando sabe que existe uma quebra nos requisitos de recursos.



## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Simular a utilização nas bases de dados de inquilinos ao executar um gerador de carga fornecido
> * Monitorizar as bases de dados de inquilinos à medida que a carga aumenta
> * Aumentar verticalmente o Conjunto elástico em resposta ao aumento de carga da base de dados
> * Aprovisionar um segundo Conjunto elástico para balancear a carga da atividade da base de dados

[Tutorial Restaurar um inquilino individual](saas-dbpertenant-restore-single-tenant.md)


## <a name="additional-resources"></a>Recursos adicionais

* Adicionais [tutoriais que são criados após a implementação da aplicação Wingtip Tickets SaaS da base de dados por inquilino](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* [Conjuntos elásticos SQL](sql-database-elastic-pool.md)
* [Automatização do Azure](../automation/automation-intro.md)
* [Registos de Monitor do Azure](saas-dbpertenant-log-analytics.md) - definição e utilização tutorial de registos do Azure Monitor
