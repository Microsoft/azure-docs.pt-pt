---
title: 'App Saas: Monitorizar o desempenho de muitas bases de dados'
description: Monitorize e gere o desempenho da Base de Dados Azure SQL numa aplicação SaaS multi-arrendatário
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: seo-lt-2019, sqldbrb=1
ms.devlang: ''
ms.topic: tutorial
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/25/2019
ms.openlocfilehash: d1349ccc5879cf461cd1c6a3c0122173a43e8123
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91619717"
---
# <a name="monitor-and-manage-performance-of-azure-sql-database-in-a-multi-tenant-saas-app"></a>Monitorize e gere o desempenho da Base de Dados Azure SQL numa aplicação SaaS multi-arrendatário
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Neste tutorial, são explorados vários cenários-chave de gestão de desempenho utilizados nas aplicações do SaaS. Utilizando um gerador de carga para simular a atividade em todas as bases de dados dos inquilinos, são demonstradas as funcionalidades de monitorização e alerta incorporadas da Base de Dados SQL e piscinas elásticas.

A app Wingtip Tickets SaaS Database Per Tenant utiliza um modelo de dados de um único inquilino, onde cada local (inquilino) tem a sua própria base de dados. Como em muitas aplicações SaaS, o padrão de carga de trabalho do inquilino antecipado é imprevisível e esporádico. Por outras palavras, as vendas de bilhetes podem ocorrer em qualquer altura. Para tirar partido deste padrão típico de utilização da base de dados, as bases de dados dos inquilinos são implantadas em piscinas elásticas. Os conjuntos elásticos otimizam o custo de uma solução ao partilhar recursos em muitas bases de dados. Com este tipo de padrão, é importante monitorizar a utilização das bases de dados e dos recursos dos conjuntos para confirmar que as cargas estão razoavelmente equilibradas entre os conjuntos. Também tem de garantir que as bases de dados individuais têm os recursos adequados e que os conjuntos não estão a atingir os seus limites [eDTU](purchasing-models.md#dtu-based-purchasing-model). Este tutorial analisa formas de monitorizar e gerir bases de dados e conjuntos e como tomar uma medida corretiva em resposta a variações na carga de trabalho.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> 
> * Simular a utilização nas bases de dados de inquilinos ao executar um gerador de carga fornecido
> * Monitorizar as bases de dados de inquilinos à medida que a carga aumenta
> * Aumentar verticalmente o Conjunto elástico em resposta ao aumento de carga da base de dados
> * Aprovisionar um segundo Conjunto elástico para balancear a carga da atividade da base de dados


Para concluir este tutorial, devem ser cumpridos os seguintes pré-requisitos:

* A aplicação Wingtip Tickets SaaS Database Per Tenant é implementada. Para implementar em menos de cinco minutos, consulte [Implementar e explorar a aplicação Wingtip Tickets SaaS Database per Tenant](../../sql-database/saas-dbpertenant-get-started-deploy.md)
* O Azure PowerShell está instalado. Para obter mais detalhes, veja [Introdução ao Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps)

## <a name="introduction-to-saas-performance-management-patterns"></a>Introdução aos padrões de gestão de desempenho do SaaS

Gerir o desempenho da base de dados consiste em compilar e analisar dados de desempenho e, em seguida, reagir a estes dados ajustando os parâmetros para manter um tempo de resposta aceitável para a aplicação. Ao hospedar vários inquilinos, as piscinas elásticas são uma forma rentável de fornecer e gerir recursos para um grupo de bases de dados com cargas de trabalho imprevisíveis. Com determinados padrões de carga de trabalho, um mínimo de duas bases de dados S3 pode beneficiar ao serem geridas num conjunto.

![diagrama de aplicação](./media/saas-dbpertenant-performance-monitoring/app-diagram.png)

As piscinas e as bases de dados em piscinas devem ser monitorizadas para garantir que se mantenham dentro dos intervalos aceitáveis de desempenho. Sintonize a configuração da piscina para atender às necessidades da carga de trabalho agregada de todas as bases de dados, garantindo que os eDTUs do pool são adequados para a carga de trabalho global. Ajuste os valores eDTU mínimo e máximo por cada base de dados para adequar os valores aos requisitos da aplicação específica.

### <a name="performance-management-strategies"></a>Estratégias da gestão do desempenho

* Para evitar ter de monitorizar manualmente o desempenho, é mais eficaz **definir alertas que desencadeiam quando bases de dados ou piscinas se desviam dos intervalos normais**.
* Para responder às flutuações de curto prazo no tamanho agregado do cálculo de uma piscina, o **nível de eDTU**da piscina pode ser dimensionado para cima ou para baixo . Se esta flutuação ocorrer numa base regular ou previsível, **o dimensionamento do conjunto poderá ser agendado para ocorrer automaticamente**. Por exemplo, reduza verticalmente quando sabe que a carga de trabalho é leve, talvez durante a noite ou durante os fins de semana.
* Para responder a flutuações de duração mais longa ou alterações no número de bases de dados, **as bases de dados individuais podem ser movidas para outros grupos**.
* Para responder a aumentos de curto prazo em bases de dados *individuais,* **as bases de dados individuais podem ser retiradas de uma piscina e atribuídas um tamanho de computação individual.** Assim que a carga for reduzida, a base de dados pode ser devolvida ao conjunto. Quando isto é conhecido antecipadamente, as bases de dados podem ser movidas preventivamente para garantir que a base de dados tem sempre os recursos de que necessita e para evitar impactos noutras bases de dados na piscina. Se este requisito for previsível, por exemplo, quando um local tem uma grande procura de bilhetes para um evento popular, este comportamento de gestão poderá ser integrado na aplicação.

O [portal do Azure](https://portal.azure.com) fornece monitorização e alertas incorporados na maior parte dos recursos. A monitorização e alerta estão disponíveis em bases de dados e piscinas. Este monitor incorporado e alerta é específico de recursos, por isso é conveniente usar para um pequeno número de recursos, mas não é muito conveniente quando se trabalha com muitos recursos.

Para cenários de grande volume, onde você está trabalhando com muitos recursos, [os registos do Azure Monitor](../../sql-database/saas-dbpertenant-log-analytics.md) podem ser usados. Este é um serviço Azure separado que fornece análise sobre registos emitidos recolhidos num espaço de trabalho log analytics. Os registos do Azure Monitor podem recolher telemetria de muitos serviços e ser usados para consultar e definir alertas.

## <a name="get-the-wingtip-tickets-saas-database-per-tenant-application-scripts"></a>Obtenha os scripts de aplicação de ingressos saas de ponta de asa por inquilino

Os scripts de base de dados de multi-inquilinos Wingtip SaaS e código fonte de aplicação estão disponíveis no [wingtipTicketsSaAS-DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) GitHub repo. Confira as [orientações gerais](saas-tenancy-wingtip-app-guidance-tips.md) para as etapas para descarregar e desbloquear os scripts SaaS dos Bilhetes Wingtip.

## <a name="provision-additional-tenants"></a>Aprovisionar inquilinos adicionais

Apesar de os conjuntos poderem ser económicos com apenas duas bases de dados S3, quantas mais bases de dados estiverem no conjunto mais económico será o efeito em média. Para uma boa compreensão de como funciona a monitorização e a gestão do desempenho à escala, este tutorial requer que tenha, pelo menos, 20 bases de dados implementadas.

Se já forte um lote de inquilinos num tutorial anterior, salte para o [uso simulado em todas as bases de dados de inquilinos.](#simulate-usage-on-all-tenant-databases)

1. No **PowerShell ISE,** aberto... \\ Módulos de Aprendizagem \\ Monitorização e Gestão \\ * de DesempenhoDemo-PerformanceMonitoringAndManagement.ps1*. Mantenha este script aberto, uma vez que vai executar vários cenários durante este tutorial.
1. Definir **$DemoScenario**  =  **1**, **Provision um lote de inquilinos**
1. Prima **F5** para executar o script.

O script implementará 17 inquilinos em menos de cinco minutos.

O script *New-TenantBatch* utiliza um conjunto aninhado ou ligado de modelos de Gestor de [Recursos](../../azure-resource-manager/index.yml) que criam um lote de inquilinos, que por predefinição copia a **base de** base de dados no servidor do catálogo para criar as novas bases de dados de inquilinos, depois regista-as no catálogo e, finalmente, iniciaisiza-as com o nome do inquilino e o tipo de local. Isto é consistente com a forma como a aplicação prevê um novo inquilino. Quaisquer alterações introduzidas na *basetenantdb* são aplicadas a quaisquer novos inquilinos anteentes posteriormente. Consulte o tutorial de [Gestão de Schema](saas-tenancy-schema-management.md) para ver como fazer alterações de esquema nas bases de dados de inquilinos *existentes* (incluindo a base de dados *basetenantdb).*

## <a name="simulate-usage-on-all-tenant-databases"></a>Simular a utilização em todas as bases de dados de inquilinos

O *Demo-PerformanceMonitoringAndManagement.ps1* script é fornecido que simula uma carga de trabalho correndo contra todas as bases de dados de inquilinos. A carga é gerada utilizando um dos cenários de carga disponíveis:

| Demonstração | Cenário |
|:--|:--|
| 2 | Gerar carga de intensidade normal (aproximadamente 40 DTU) |
| 3 | Gerar carga com picos mais demorados e mais frequentes por base de dados|
| 4 | Gerar carga com rajadas de DTU mais altas por base de dados (aproximadamente 80 DTU)|
| 5 | Gere uma carga normal mais uma carga elevada num único inquilino (aproximadamente 95 DTU)|
| 6 | Gerar carga desequilibrada em vários conjuntos|

O gerador de carga aplica uma carga *sintética* só na CPU para cada base de dados do inquilino. O gerador inicia uma tarefa para cada base de dados de inquilino, que chama um procedimento armazenado que periodicamente gera a carga. Os níveis de carga (em eDTUs), a duração e os intervalos são diversificados em todas as bases de dados, o que simula a atividade imprevisível do inquilino.

1. No **PowerShell ISE,** aberto... \\ Módulos de Aprendizagem \\ Monitorização e Gestão \\ * de DesempenhoDemo-PerformanceMonitoringAndManagement.ps1*. Mantenha este script aberto, uma vez que vai executar vários cenários durante este tutorial.
1. Definir **$DemoScenario**  =  **2**, *Gerar carga de intensidade normal*.
1. Prima **F5** para aplicar uma carga a todas as bases de dados de inquilinos.

Wingtip Tickets SaaS Database Per Tenant é uma aplicação SaaS, e a carga do mundo real numa aplicação SaaS é tipicamente esporádica e imprevisível. Para simular isto, o gerador de carga produz uma carga aleatória distribuída por todos os inquilinos. São necessários vários minutos para que o padrão de carga emerja, por isso, carregue o gerador de carga durante 3-5 minutos antes de tentar monitorizar a carga nas seguintes secções.

> [!IMPORTANT]
> O gerador de carga está em execução como uma série de tarefas na sessão local do PowerShell. Mantenha aberto o separador *Demo-PerformanceMonitoringAndManagement.ps1*! Se fechar o separador ou suspender o computador, o gerador de carga será interrompido. O gerador de carga permanece num estado *de invocação de emprego* onde gera carga sobre quaisquer novos inquilinos que sejam a provisionados após o início do gerador. Use *ctrl-C* para parar de invocar novos empregos e sair do guião. O gerador de carga continuará a funcionar, mas apenas com os inquilinos existentes.

## <a name="monitor-resource-usage-using-the-azure-portal"></a>Monitorize a utilização de recursos utilizando o portal Azure

Para monitorizar a utilização do recurso que resulta da carga aplicada, abra o portal para a piscina que contém as bases de dados do arrendatário:

1. Abra o [portal Azure](https://portal.azure.com) e navegue para o servidor *user-1-dpt-dos &lt; inquilinos. &gt; *
1. Desloque-se para baixo, localize os conjuntos elásticos e clique em **Pool1**. Este conjunto contém todas as bases de dados de inquilinos criadas até à data.

Observe os **gráficos de monitorização da piscina elástica** e os gráficos **de monitorização da base de dados elásticas.**

A utilização de recursos do pool é a utilização agregada da base de dados para todas as bases de dados da piscina. O gráfico da base de dados mostra as cinco bases de dados mais quentes:

![gráfico de base de dados](./media/saas-dbpertenant-performance-monitoring/pool1.png)

Como existem bases de dados adicionais na piscina para além das cinco primeiras, a utilização do pool mostra uma atividade que não se reflete no gráfico das cinco primeiras bases de dados. Para mais detalhes, clique em Data de Utilização de Recursos de Base de **Dados:**

![utilização de recursos de base de dados](./media/saas-dbpertenant-performance-monitoring/database-utilization.png)


## <a name="set-performance-alerts-on-the-pool"></a>Definir alertas de desempenho no conjunto

Desatenção na piscina que aciona \> 75% de utilização da seguinte forma:

1. Open *Pool1* (no *servidor de inquilinos1-dpt) \<user\> * no portal [Azure](https://portal.azure.com).
1. Clique em **Regras de Alerta**e, em seguida, clique em **+ Adicionar alerta**:

   ![adicionar alerta](./media/saas-dbpertenant-performance-monitoring/add-alert.png)

1. Forneça um nome, tal como **DTU elevada**,
1. Defina os seguintes valores:
   * **Métrica = percentagem de eDTU**
   * **Condição = maior do que**
   * **Limiar = 75**
   * **Período = Nos últimos 30 minutos**
1. Adicione um endereço de e-mail à caixa *de e-mails do administrador adicional* e clique em **OK**.

   ![alerta de definição](./media/saas-dbpertenant-performance-monitoring/alert-rule.png)


## <a name="scale-up-a-busy-pool"></a>Aumentar verticalmente um conjunto ocupado

Se o nível de carga agregado aumentar num conjunto até ao ponto máximo do conjunto e atingir a utilização de 100% de eDTU, o desempenho da base de dados individual será afetado, o que potencialmente abranda os tempos de resposta de consulta de todas as bases de dados no conjunto.

**A curto prazo,** considere aumentar a piscina para fornecer recursos adicionais, ou remover bases de dados da piscina (transferindo-as para outras piscinas, ou para fora da piscina para um nível de serviço autónomo).

**A longo prazo,** considere otimizar consultas ou indexar o uso para melhorar o desempenho da base de dados. Dependendo da sensibilidade da aplicação relativamente a problemas de desempenho, a melhor prática para aumentar verticalmente um conjunto antes de atingir os 100% de utilização de eDTU. Utilize um alerta para avisá-lo antecipadamente.

Pode simular um conjunto ocupado ao aumentar a carga produzida pelo gerador. Fazendo com que as bases de dados rebentem com mais frequência, e por mais tempo, aumentando a carga agregada na piscina sem alterar os requisitos das bases de dados individuais. O aumento vertical do conjunto é feito facilmente no portal ou a partir do PowerShell. Este exercício utiliza o portal.

1. Defina *$DemoScenario*  =  **3**, _Gere a carga com rajadas mais longas e mais frequentes por base_ de dados para aumentar a intensidade da carga agregada na piscina sem alterar a carga máxima exigida por cada base de dados.
1. Prima **F5** para aplicar uma carga a todas as bases de dados de inquilinos.

1. Vá à **Piscina1** no portal Azure.

Monitorize o aumento da utilização do eDTU da piscina no gráfico superior. Leva alguns minutos para que a nova carga superior faça efeito, mas você deve rapidamente ver a piscina começar a atingir a utilização máxima, e à medida que a carga se mantém no novo padrão, ele rapidamente sobrecarrega a piscina.

1. Para escalar a piscina, clique na **piscina Configure** no topo da página **Pool1.**
1. Ajuste a regulação **do Pool eDTU** para **100**. Alterar a eDTU do conjunto não altera as definições por base de dados (que é ainda o máximo de 50 eDTU por base de dados). Pode ver as definições por base de dados no lado direito da página do **pool Configure.**
1. Clique em **Guardar** para submeter o pedido de escala da piscina.

Volte à **Pool1**  >  **Visão Geral da Pool1** para ver os gráficos de monitorização. Monitorize o efeito de fornecer ao pool mais recursos (embora com poucas bases de dados e uma carga aleatória nem sempre seja fácil de ver conclusivamente até correr durante algum tempo). Enquanto estiver a visualizar os gráficos tenha em atenção que 100% no gráfico superior representa agora 100 eDTUs, enquanto no gráfico inferior 100% é ainda 50 eDTUs, uma vez que o máximo por base de dados ainda está em 50 eDTUs.

As bases de dados permanecem online e estão totalmente disponíveis durante o processo. No último momento, à medida que cada base de dados fica pronta para ser ativada com a nova eDTU de conjunto, todas as ligações ativas são perdidas. O código da aplicação deve ser sempre escrito para repetir as ligações que caem e, por conseguinte, voltar a ligar à base de dados no conjunto aumentado verticalmente.

## <a name="load-balance-between-pools"></a>Equilíbrio de carga entre piscinas

Como alternativa ao aumento vertical do conjunto, crie um segundo conjunto e mova as bases de dados para o conjunto para balancear a carga entre os dois conjuntos. Para fazê-lo, o novo conjunto tem de ser criado no mesmo servidor do primeiro conjunto.

1. No [portal Azure,](https://portal.azure.com)abra o servidor ** &lt; user-dpt-user &gt; dos inquilinos.**
1. Clique **+ Nova piscina** para criar uma piscina no servidor atual.
1. No modelo de **piscina elástica:**

   1. Definir **nome** para *Pool2*.
   1. Deixe o escalão de preço como **Conjunto Padrão**.
   1. Clique **na piscina Configure,**
   1. Definir **Pool eDTU** a *50 eDTU*.
   1. Clique **em Adicionar bases de dados** para ver uma lista de bases de dados no servidor que podem ser adicionadas ao *Pool2*.
   1. Selecione 10 bases de dados para movê-las para a nova piscina e, em seguida, clique em **Select**. Se esteve a executar o gerador de carga, o serviço já sabe que o seu perfil de desempenho requer uma piscina maior do que o tamanho padrão de 50 eDTU e recomenda começar com uma definição de 100 eDTU.

      ![recomendação](./media/saas-dbpertenant-performance-monitoring/configure-pool.png)

   1. Para este tutorial, deixe o padrão em 50 eDTUs e clique em **Selecionar** novamente.
   1. Selecione **OK** para criar o novo pool e para mover as bases de dados selecionadas para dentro dele.

Criar a piscina e mover as bases de dados leva alguns minutos. À medida que as bases de dados são movidas, permanecem on-line e totalmente acessíveis até ao último momento, altura em que quaisquer ligações abertas são fechadas. Desde que tenha alguma lógica de relemissão, os clientes ligar-se-ão à base de dados na nova piscina.

Navegue pela **Pool2** (no servidor de *1 \<user\> dpt-dpt-inquilinos)* para abrir a piscina e monitorizar o seu desempenho. Se não o vir, espere que o provisionamento da nova piscina esteja concluído.

Agora vê que o uso de recursos na *Pool1* diminuiu e que *o Pool2* está agora igualmente carregado.

## <a name="manage-performance-of-an-individual-database"></a>Gerir o desempenho de uma base de dados individual

Se uma base de dados individual numa piscina experimentar uma carga elevada sustentada, dependendo da configuração da piscina, pode tender a dominar os recursos na piscina e impactar outras bases de dados. Se a atividade continuar por algum tempo, a base de dados pode ser temporariamente transferida para fora da piscina. Isto permite que a base de dados tenha os recursos extras de que necessita e isola-a das outras bases de dados.

Este exercício simula o efeito de uma carga elevada em Contoso Concert Hall, quando são colocados à venda os bilhetes para um concerto popular.

1. No **PowerShell ISE,** abra o ... \\ *Demo-PerformanceMonitoringAndManagement.ps1* guião.
1. Definir **$DemoScenario = 5, Gerar uma carga normal mais uma carga elevada sobre um único inquilino (aproximadamente 95 DTU).**
1. Defina **$SingleTenantDatabaseName = contosoconcerthall**
1. Execute o script com **F5**.


1. No [portal Azure,](https://portal.azure.com)consulte a lista de bases de dados no servidor *de \<user\> inquilinos1-dpt.* 
1. Clique na base **de dados contosoconcerthall.**
1. Clique na piscina onde está o **contosoconcerthall.** Localize a piscina na secção da **piscina elástica.**

1. Inspecione o gráfico **de monitorização da piscina elástica** e procure o aumento da utilização do eDTU da piscina. Depois de um minuto ou dois, o aumento de carga deve começar a surgir e deve poder ver rapidamente que o conjunto atinge os 100% de utilização.
2. Inspecione o ecrã **de monitorização da base de dados elástica,** que mostra as bases de dados mais quentes da última hora. A base de *dados contosoconcerthall* deve aparecer em breve como uma das cinco bases de dados mais quentes.
3. Clique no **gráfico** **de monitorização da base de dados elástica** e abra a página **de Utilização de Recursos de Base de Dados,** onde pode monitorizar qualquer uma das bases de dados. Isto permite isolar o visor para a base *de dados contosoconcerthall.*
4. Na lista de bases de dados, clique em **contosoconcerthall**.
5. Clique em **Nível de Preços (DTUs de escala)** para abrir a página **de desempenho do Configure,** onde pode definir um tamanho de computação autónomo para a base de dados.
6. Clique no separador **Padrão** para abrir as opções de dimensionamento na camada Padrão.
7. Deslize o **deslizador DTU** para a direita para selecionar **100** DTUs. Note que isto corresponde ao objetivo de serviço, **S3**.
8. Clique **em Aplicar** para tirar a base de dados da piscina e torná-la uma base de dados Standard *S3.*
9. Uma vez concluído o dimensionamento, monitorize o efeito na base de dados contosoconcerthall e pool1 na piscina elástica e lâminas de base de dados.

Uma vez que a alta carga na base de dados contosoconcerthall diminua, deve devolvê-la prontamente à piscina para reduzir o seu custo. Se não for claro quando isso irá acontecer, poderá definir um alerta na base de dados que irá desencadear quando o seu uso de DTU cair abaixo do máximo por base de dados na piscina. Veja o exercício 5, para saber como mover uma base de dados para um conjunto.

## <a name="other-performance-management-patterns"></a>Outros padrões de gestão de desempenho

**Escala preventiva** No exercício acima onde explorava como escalar uma base de dados isolada, sabia qual base de dados procurar. Se a administração da Salas de Concertos contoso tivesse informado as pontas do Wingtips da venda iminente de bilhetes, a base de dados poderia ter sido retirada preventivamente da piscina. Caso contrário, seria provavelmente necessário um alerta no conjunto ou na base de dados para detetar o que estava a acontecer. Não gostaria de ter conhecimento desta situação através dos outros inquilinos no conjunto a reclamarem de uma degradação do desempenho. E se o inquilino pode prever por quanto tempo serão necessários recursos adicionais, pode configurar um runbook de Automatização do Azure para mover a base de dados para fora do conjunto e, em seguida, fazê-la voltar numa agenda definida.

**Dimensionamento de self-service do inquilino** – Porque o dimensionamento é uma tarefa chamada facilmente através de API de gestão, pode criar facilmente a capacidade de dimensionar bases de dados de inquilino na sua aplicação direcionada para o inquilino e oferecê-la como uma funcionalidade do serviço SaaS. Por exemplo, permita que os inquilinos administrem automaticamente o aumento ou redução vertical, talvez ligando-os diretamente à faturação deles!

**Aumentar e reduzir verticalmente um conjunto com base numa agenda para corresponder aos padrões de utilização**

Quando a utilização agregada de inquilinos segue padrões de utilização previsível, pode utilizar a Automatização do Azure para aumentar ou reduzir verticalmente um conjunto com base numa agenda. Por exemplo, reduza verticalmente um conjunto após as 18:00 e aumente-o verticalmente antes das 06:00 nos dias da semana quando sabe que existe uma quebra nos requisitos de recursos.



## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Simular a utilização nas bases de dados de inquilinos ao executar um gerador de carga fornecido
> * Monitorizar as bases de dados de inquilinos à medida que a carga aumenta
> * Aumentar verticalmente o Conjunto elástico em resposta ao aumento de carga da base de dados
> * Aprovisionar um segundo Conjunto elástico para balancear a carga da atividade da base de dados

[Tutorial Restaurar um inquilino individual](saas-dbpertenant-restore-single-tenant.md)


## <a name="additional-resources"></a>Recursos adicionais

* [Tutoriais adicionais que se baseiam na base de dados saass database por inquilino](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* [Piscinas elásticas SQL](elastic-pool-overview.md)
* [Automação azul](../../automation/automation-intro.md)
* [Registos do Monitor Azure](../../sql-database/saas-dbpertenant-log-analytics.md) - Configuração e utilização de registos Azure Monitor
