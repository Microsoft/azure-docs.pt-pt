---
title: 'App Saas: Monitorizar o desempenho de muitas bases de dados'
description: Monitorize e gerencie o desempenho das bases de dados e piscinas Azure SQL numa aplicação SaaS multi-inquilino
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: seo-lt-2019
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/25/2019
ms.openlocfilehash: 34c50795567615637e31446ad3dc51a5e1b355f6
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79214457"
---
# <a name="monitor-and-manage-performance-of-azure-sql-databases-and-pools-in-a-multi-tenant-saas-app"></a>Monitorize e gerencie o desempenho das bases de dados e piscinas Azure SQL numa aplicação SaaS multi-inquilino

Neste tutorial, são explorados vários cenários-chave de gestão de desempenho utilizados nas aplicações SaaS. A utilização de um gerador de carga para simular atividade em todas as bases de dados dos inquilinos, demonstrou-se a monitorização e alerta incorporadas da Base de Dados SQL e piscinas elásticas.

A app Wingtip Tickets SaaS Database Per Tenant utiliza um modelo de dados de um único inquilino, onde cada local (inquilino) tem a sua própria base de dados. Como em muitas aplicações SaaS, o padrão de carga de trabalho do inquilino antecipado é imprevisível e esporádico. Por outras palavras, as vendas de bilhetes podem ocorrer em qualquer altura. Para tirar partido deste padrão típico de utilização da base de dados, as bases de dados dos inquilinos são implantadas em piscinas elásticas. Os conjuntos elásticos otimizam o custo de uma solução ao partilhar recursos em muitas bases de dados. Com este tipo de padrão, é importante monitorizar a utilização das bases de dados e dos recursos dos conjuntos para confirmar que as cargas estão razoavelmente equilibradas entre os conjuntos. Também tem de garantir que as bases de dados individuais têm os recursos adequados e que os conjuntos não estão a atingir os seus limites [eDTU](sql-database-purchase-models.md#dtu-based-purchasing-model). Este tutorial analisa formas de monitorizar e gerir bases de dados e conjuntos e como tomar uma medida corretiva em resposta a variações na carga de trabalho.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> 
> * Simular a utilização nas bases de dados de inquilinos ao executar um gerador de carga fornecido
> * Monitorizar as bases de dados de inquilinos à medida que a carga aumenta
> * Aumentar verticalmente o Conjunto elástico em resposta ao aumento de carga da base de dados
> * Aprovisionar um segundo Conjunto elástico para balancear a carga da atividade da base de dados


Para concluir este tutorial, devem ser cumpridos os seguintes pré-requisitos:

* A aplicação Wingtip Tickets SaaS Database Per Tenant está implementada. Para implantar em menos de cinco minutos, consulte Implementar e explorar a base de [dados saaS de bilhetes wingtip por pedido](saas-dbpertenant-get-started-deploy.md) de inquilino
* O Azure PowerShell está instalado. Para obter mais detalhes, veja [Introdução ao Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps)

## <a name="introduction-to-saas-performance-management-patterns"></a>Introdução aos padrões de gestão de desempenho da SaaS

Gerir o desempenho da base de dados consiste em compilar e analisar dados de desempenho e, em seguida, reagir a estes dados ajustando os parâmetros para manter um tempo de resposta aceitável para a aplicação. Ao hospedar vários inquilinos, as piscinas elásticas são uma forma rentável de fornecer e gerir recursos para um grupo de bases de dados com cargas de trabalho imprevisíveis. Com determinados padrões de carga de trabalho, um mínimo de duas bases de dados S3 pode beneficiar ao serem geridas num conjunto.

![diagrama de aplicação](./media/saas-dbpertenant-performance-monitoring/app-diagram.png)

As piscinas e as bases de dados em piscinas devem ser monitorizadas para garantir que se mantenham dentro de intervalos de desempenho aceitáveis. Sintonize a configuração do pool para atender às necessidades da carga de trabalho agregada de todas as bases de dados, garantindo que as eDTUs do pool são adequadas para a carga de trabalho global. Ajuste os valores eDTU mínimo e máximo por cada base de dados para adequar os valores aos requisitos da aplicação específica.

### <a name="performance-management-strategies"></a>Estratégias da gestão do desempenho

* Para evitar ter de monitorizar manualmente o desempenho, é mais eficaz **definir alertas que disparam quando bases de dados ou piscinas se desviam dos intervalos normais**.
* Para responder a flutuações a curto prazo no tamanho da computação agregada de uma piscina, o **nível eDTU da piscina pode ser escalado para cima ou para baixo**. Se esta flutuação ocorrer numa base regular ou previsível, **o dimensionamento do conjunto poderá ser agendado para ocorrer automaticamente**. Por exemplo, reduza verticalmente quando sabe que a carga de trabalho é leve, talvez durante a noite ou durante os fins de semana.
* Para responder a flutuações de duração mais longa ou alterações no número de bases de dados, **as bases de dados individuais podem ser movidas para outros grupos**.
* Para responder a aumentos a curto prazo da carga individual de base de dados as **bases de dados individuais podem ser retiradas de uma piscina e atribuídas a um tamanho individual**de cálculo . *individual* Assim que a carga for reduzida, a base de dados pode ser devolvida ao conjunto. Quando isso é conhecido antecipadamente, as bases de dados podem ser movidas preventivamente para garantir que a base de dados tem sempre os recursos de que necessita, e para evitar o impacto noutras bases de dados na piscina. Se este requisito for previsível, por exemplo, quando um local tem uma grande procura de bilhetes para um evento popular, este comportamento de gestão poderá ser integrado na aplicação.

O [portal do Azure](https://portal.azure.com) fornece monitorização e alertas incorporados na maior parte dos recursos. Na Base de Dados SQL, a monitorização e os alertas estão disponíveis nas bases de dados e nos conjuntos. Esta monitorização e alerta incorporados é específica de recursos, por isso é conveniente usar para um pequeno número de recursos, mas não é muito conveniente quando se trabalha com muitos recursos.

Para cenários de grande volume, onde está a trabalhar com muitos recursos, os [registos do Monitor Azure](saas-dbpertenant-log-analytics.md) podem ser usados. Este é um serviço Azure separado que fornece análises sobre registos emitidos recolhidos num espaço de trabalho log Analytics. Os registos do Azure Monitor podem recolher telemetria de muitos serviços e ser usados para consultar e definir alertas.

## <a name="get-the-wingtip-tickets-saas-database-per-tenant-application-scripts"></a>Obtenha os bilhetes de ala SaaS Database per Tenant scripts de aplicação

Os bilhetes Wingtip SaaS Scripts de base de dados multi-inquilinos e código fonte de aplicação estão disponíveis no repo [WingtipTicketsSaaS-DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) GitHub. Confira as [orientações gerais](saas-tenancy-wingtip-app-guidance-tips.md) para os passos para descarregar e desbloquear os scripts Wingtip Tickets SaaS.

## <a name="provision-additional-tenants"></a>Aprovisionar inquilinos adicionais

Apesar de os conjuntos poderem ser económicos com apenas duas bases de dados S3, quantas mais bases de dados estiverem no conjunto mais económico será o efeito em média. Para uma boa compreensão de como funciona a monitorização e a gestão do desempenho à escala, este tutorial requer que tenha, pelo menos, 20 bases de dados implementadas.

Se já for provisionado um lote de inquilinos num tutorial anterior, salte para o uso simular em todas as bases de dados dos [inquilinos.](#simulate-usage-on-all-tenant-databases)

1. No **PowerShell ISE,** aberto ... \\Módulos\\de Aprendizagem\\Monitorização de Desempenho e Gestão*Demo-PerformanceMonitoringAndManagement.ps1*. Mantenha este script aberto, uma vez que vai executar vários cenários durante este tutorial.
1. Conjunto **$DemoScenario** = **1**, **Provisiona um lote de inquilinos**
1. Prima **F5** para executar o script.

O script implementará 17 inquilinos em menos de cinco minutos.

O script *New-TenantBatch* usa um conjunto aninhado ou ligado de modelos de Gestor de [Recursos](../azure-resource-manager/index.yml) que criam um lote de inquilinos, que por padrão copia a base de **base** do servidor de base no servidor de catálogo para criar as novas bases de dados de inquilinos, em seguida, registra-os no catálogo, e finalmente os inicia com o nome do inquilino e tipo de local. Isto é consistente com a forma como a aplicação prevê um novo inquilino. Quaisquer alterações introduzidas na *base tenantdb* são aplicadas a quaisquer novos inquilinos a partir daí previstos. Consulte o tutorial da [Schema Management](saas-tenancy-schema-management.md) para ver como fazer alterações de esquemas nas bases de dados de inquilinos *existentes* (incluindo a base de dados *basetenantdb).*

## <a name="simulate-usage-on-all-tenant-databases"></a>Simular a utilização em todas as bases de dados de inquilinos

O script *Demo-PerformanceMonitoringAndManagement.ps1* é fornecido que simula uma carga de trabalho em execução contra todas as bases de dados de inquilinos. A carga é gerada utilizando um dos cenários de carga disponíveis:

| Demonstração | Cenário |
|:--|:--|
| 2 | Gerar carga de intensidade normal (aproximadamente 40 DTU) |
| 3 | Gerar carga com picos mais demorados e mais frequentes por base de dados|
| 4 | Gerar carga com explosões dTU mais altas por base de dados (aproximadamente 80 DTU)|
| 5 | Gerar uma carga normal mais uma carga elevada num único inquilino (aproximadamente 95 DTU)|
| 6 | Gerar carga desequilibrada em vários conjuntos|

O gerador de carga aplica uma carga *sintética* só na CPU para cada base de dados do inquilino. O gerador inicia uma tarefa para cada base de dados de inquilino, que chama um procedimento armazenado que periodicamente gera a carga. Os níveis de carga (em eDTUs), a duração e os intervalos são diversificados em todas as bases de dados, o que simula a atividade imprevisível do inquilino.

1. No **PowerShell ISE,** aberto ... \\Módulos\\de Aprendizagem\\Monitorização de Desempenho e Gestão*Demo-PerformanceMonitoringAndManagement.ps1*. Mantenha este script aberto, uma vez que vai executar vários cenários durante este tutorial.
1. Definir **$DemoScenario** = **2**, Gerar carga de *intensidade normal*.
1. Prima **F5** para aplicar uma carga a todas as bases de dados de inquilinos.

Wingtip Tickets SaaS Database Per Tenant é uma aplicação SaaS, e a carga real numa aplicação SaaS é tipicamente esporádica e imprevisível. Para simular isto, o gerador de carga produz uma carga aleatória distribuída por todos os inquilinos. São necessários vários minutos para que o padrão de carga emerja, por isso, coloque o gerador de carga durante 3-5 minutos antes de tentar monitorizar a carga nas seguintes secções.

> [!IMPORTANT]
> O gerador de carga está em execução como uma série de tarefas na sessão local do PowerShell. Mantenha aberto o separador *Demo-PerformanceMonitoringAndManagement.ps1*! Se fechar o separador ou suspender o computador, o gerador de carga será interrompido. O gerador de carga permanece num estado *de invocação de emprego* onde gera carga em quaisquer novos inquilinos que são aprovisionados após o início do gerador. Use *ctrl-C* para parar de invocar novos empregos e sair do guião. O gerador de carga continuará a funcionar, mas apenas em inquilinos existentes.

## <a name="monitor-resource-usage-using-the-azure-portal"></a>Monitorizar o uso de recursos utilizando o portal Azure

Para monitorizar o uso de recursos que resulta da carga aplicada, abra o portal para a piscina que contém as bases de dados dos inquilinos:

1. Abra o [portal Azure](https://portal.azure.com) e navegue até ao servidor de utilizador dos *&lt;inquilinos1-dpt.&gt; *
1. Desloque-se para baixo, localize os conjuntos elásticos e clique em **Pool1**. Este conjunto contém todas as bases de dados de inquilinos criadas até à data.

Observe os gráficos de **monitorização** da piscina elástica e **elásticos.**

A utilização de recursos do pool é a utilização agregada da base de dados para todas as bases de dados da piscina. O gráfico da base de dados mostra as cinco bases de dados mais quentes:

![gráfico de base de dados](./media/saas-dbpertenant-performance-monitoring/pool1.png)

Como existem bases de dados adicionais na piscina para além dos cinco primeiros, a utilização do pool mostra atividade que não se reflete no top 5 das bases de dados. Para mais detalhes, clique na **utilização de recursos**da base de dados:

![utilização de recursos de base de dados](./media/saas-dbpertenant-performance-monitoring/database-utilization.png)


## <a name="set-performance-alerts-on-the-pool"></a>Definir alertas de desempenho no conjunto

Despolete um alerta na \>piscina que desencadeie uma utilização de 75% da seguinte forma:

1. Open *Pool1* (no servidor de *\<utilizadores\> de 1 dpt)* no portal [Azure](https://portal.azure.com).
1. Clique em **Regras de Alerta**e, em seguida, clique em **+ Adicionar alerta**:

   ![adicionar alerta](media/saas-dbpertenant-performance-monitoring/add-alert.png)

1. Forneça um nome, tal como **DTU elevada**,
1. Defina os seguintes valores:
   * **Métrica = percentagem de eDTU**
   * **Condição = maior do que**
   * **Limiar = 75**
   * **Período = Nos últimos 30 minutos**
1. Adicione um endereço de e-mail para a caixa *de e-mail do administrador adicional* e clique em **OK**.

   ![alerta de definição](media/saas-dbpertenant-performance-monitoring/alert-rule.png)


## <a name="scale-up-a-busy-pool"></a>Aumentar verticalmente um conjunto ocupado

Se o nível de carga agregado aumentar num conjunto até ao ponto máximo do conjunto e atingir a utilização de 100% de eDTU, o desempenho da base de dados individual será afetado, o que potencialmente abranda os tempos de resposta de consulta de todas as bases de dados no conjunto.

**A curto prazo,** considere escalar a piscina para fornecer recursos adicionais, ou remover bases de dados da piscina (transferindo-as para outras piscinas, ou para fora da piscina para um nível de serviço autónomo).

**A longo prazo,** considere otimizar consultas ou utilizar índices para melhorar o desempenho da base de dados. Dependendo da sensibilidade da aplicação relativamente a problemas de desempenho, a melhor prática para aumentar verticalmente um conjunto antes de atingir os 100% de utilização de eDTU. Utilize um alerta para avisá-lo antecipadamente.

Pode simular um conjunto ocupado ao aumentar a carga produzida pelo gerador. Fazendo com que as bases de dados rebentem com mais frequência e, por mais tempo, aumentando a carga agregada na piscina sem alterar os requisitos das bases de dados individuais. O aumento vertical do conjunto é feito facilmente no portal ou a partir do PowerShell. Este exercício utiliza o portal.

1. Conjunto *$DemoScenario* = **3**, Gere carga _com explosões mais longas e frequentes por base_ de dados para aumentar a intensidade da carga agregada na piscina sem alterar a carga máxima exigida por cada base de dados.
1. Prima **F5** para aplicar uma carga a todas as bases de dados de inquilinos.

1. Vá à **Pool1** no portal Azure.

Monitorize o aumento da utilização do eDTU na tabela superior. Leva alguns minutos para a nova carga mais alta fazer efeito, mas deve ver rapidamente a piscina começar a atingir a utilização máxima, e à medida que a carga se mantém no novo padrão, rapidamente sobrecarrega a piscina.

1. Para escalar a piscina, clique em **Configurar piscina** no topo da página **Pool1.**
1. Ajuste a regulação **do Pool eDTU** para **100**. Alterar a eDTU do conjunto não altera as definições por base de dados (que é ainda o máximo de 50 eDTU por base de dados). Pode ver as definições por base de dados no lado direito da página de **piscina Configure.**
1. Clique em **Guardar** para submeter o pedido para escalar a piscina.

Volte ao **Pool1** > **Overview** para ver as tabelas de monitorização. Monitorize o efeito de fornecer mais recursos ao pool (embora com poucas bases de dados e uma carga aleatória nem sempre seja fácil de ver conclusivamente até correr durante algum tempo). Enquanto estiver a visualizar os gráficos tenha em atenção que 100% no gráfico superior representa agora 100 eDTUs, enquanto no gráfico inferior 100% é ainda 50 eDTUs, uma vez que o máximo por base de dados ainda está em 50 eDTUs.

As bases de dados permanecem online e estão totalmente disponíveis durante o processo. No último momento, à medida que cada base de dados fica pronta para ser ativada com a nova eDTU de conjunto, todas as ligações ativas são perdidas. O código da aplicação deve ser sempre escrito para repetir as ligações que caem e, por conseguinte, voltar a ligar à base de dados no conjunto aumentado verticalmente.

## <a name="load-balance-between-pools"></a>Equilíbrio de carga entre piscinas

Como alternativa ao aumento vertical do conjunto, crie um segundo conjunto e mova as bases de dados para o conjunto para balancear a carga entre os dois conjuntos. Para fazê-lo, o novo conjunto tem de ser criado no mesmo servidor do primeiro conjunto.

1. No [portal Azure,](https://portal.azure.com)abra o servidor **de&lt;&gt; utilizador dos inquilinos1-dpt.**
1. Clique **+ Piscina nova** para criar uma piscina no servidor atual.
1. No modelo de **piscina elástica:**

   1. Definir **nome** para *Pool2*.
   1. Deixe o escalão de preço como **Conjunto Padrão**.
   1. Clique em **piscina de configuração,**
   1. Definir **piscina eDTU** para *50 eDTU*.
   1. Clique em **Adicionar bases** de dados para ver uma lista de bases de dados no servidor que podem ser adicionadas ao *Pool2*.
   1. Selecione quaisquer 10 bases de dados para movê-las para a nova piscina e, em seguida, clique em **Selecionar**. Se tem estado a executar o gerador de carga, o serviço já sabe que o seu perfil de desempenho requer uma piscina maior do que o tamanho padrão de 50 eDTU e recomenda começar com uma definição de 100 eDTU.

      ![recomendação](media/saas-dbpertenant-performance-monitoring/configure-pool.png)

   1. Para este tutorial, deixe o padrão em 50 eDTUs e clique em **Selecionar** novamente.
   1. Selecione **OK** para criar a nova piscina e para mover as bases de dados selecionadas para o mesmo.

Criar a piscina e mover as bases de dados leva alguns minutos. À medida que as bases de dados são movidas, permanecem on-line e totalmente acessíveis até ao último momento, altura em que quaisquer ligações abertas estão fechadas. Desde que tenha alguma lógica de retry, os clientes irão então ligar-se à base de dados da nova piscina.

Navegue até ao **Pool2** (no servidor de *\<\> utilizadores de 1 dpt)* para abrir a piscina e monitorizar o seu desempenho. Se não o vir, espere pelo fornecimento da nova piscina para completar.

Agora vê que o uso de recursos no *Pool1* diminuiu e que o *Pool2* está agora igualmente carregado.

## <a name="manage-performance-of-an-individual-database"></a>Gerir o desempenho de uma base de dados individual

Se uma base de dados individual numa piscina experimentar uma carga elevada sustentada, dependendo da configuração do pool, pode tender a dominar os recursos na piscina e a impactar outras bases de dados. Se a atividade continuar durante algum tempo, a base de dados pode ser temporariamente retirada da piscina. Isto permite que a base de dados tenha os recursos extra de que necessita e isola-os das outras bases de dados.

Este exercício simula o efeito de uma carga elevada em Contoso Concert Hall, quando são colocados à venda os bilhetes para um concerto popular.

1. No **PowerShell ISE,** abra o ... \\ *Roteiro Demo-PerformanceMonitoringAndManagement.ps1.*
1. Definir **$DemoScenario = 5, Gerar uma carga normal mais uma carga elevada num único inquilino (aproximadamente 95 DTU).**
1. Defina **$SingleTenantDatabaseName = contosoconcerthall**
1. Execute o script com **F5**.


1. No [portal Azure,](https://portal.azure.com)navegue na lista de bases de dados do servidor de *\<\> utilizadores do 1º dpt dos inquilinos.* 
1. Clique na base de dados de **Contosoconcerthall.**
1. Clique na piscina onde está o **concerthall.** Localize a piscina na secção da **piscina elástica.**

1. Inspecione o gráfico de **monitorização** da piscina elástica e procure o aumento da utilização do eDTU da piscina. Depois de um minuto ou dois, o aumento de carga deve começar a surgir e deve poder ver rapidamente que o conjunto atinge os 100% de utilização.
2. Inspecione o visor de monitorização da base de **dados Elástica,** que mostra as bases de dados mais quentes da última hora. A base de dados *contosoconcerthall* deve em breve aparecer como uma das cinco bases de dados mais quentes.
3. Clique na **tabela** de **monitorização da base** de dados Elástica e abre a página de **Utilização** de Recursos da Base de Dados onde pode monitorizar qualquer uma das bases de dados. Isto permite-lhe isolar o ecrã para a base de dados *contosoconcerthall.*
4. Na lista de bases de dados, clique **em contosoconcerthall.**
5. Clique no Nível de **Preços (DTUs de escala)** para abrir a página de desempenho do **Configure,** onde pode definir um tamanho de computação autónomo para a base de dados.
6. Clique no separador **Padrão** para abrir as opções de dimensionamento na camada Padrão.
7. Deslize o **slider DTU** para a direita para selecionar **100** DTUs. Note que isto corresponde ao objetivo de serviço, **S3**.
8. Clique **Em Aplicar** para tirar a base de dados da piscina e torná-la uma base de dados Standard *S3.*
9. Uma vez concluída a escala, monitorize o efeito na base de dados contosoconcerthall e pool1 na piscina elástica e lâminas de base de dados.

Uma vez que a alta carga na base de dados contosoconcerthall diminua, deve devolvê-la prontamente à piscina para reduzir o seu custo. Se não for claro quando isso irá acontecer, poderá definir um alerta na base de dados que irá desencadear quando o seu uso de DTU descer abaixo do máximo por base de dados na piscina. Veja o exercício 5, para saber como mover uma base de dados para um conjunto.

## <a name="other-performance-management-patterns"></a>Outros padrões de gestão de desempenho

**Escala** preventiva No exercício acima onde explorou como escalar uma base de dados isolada, sabia qual base de dados procurar. Se a administração da Sala de Concertos Contoso tivesse informado as Pontas das Asas da venda iminente de bilhetes, a base de dados poderia ter sido retirada preventivamente da piscina. Caso contrário, seria provavelmente necessário um alerta no conjunto ou na base de dados para detetar o que estava a acontecer. Não gostaria de ter conhecimento desta situação através dos outros inquilinos no conjunto a reclamarem de uma degradação do desempenho. E se o inquilino pode prever por quanto tempo serão necessários recursos adicionais, pode configurar um runbook de Automatização do Azure para mover a base de dados para fora do conjunto e, em seguida, fazê-la voltar numa agenda definida.

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

* [Tutoriais adicionais que se baseiam na base de dados SaaS dos bilhetes de asa](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials) por inquilino
* [Piscinas elásticas SQL](sql-database-elastic-pool.md)
* [Automação Azure](../automation/automation-intro.md)
* [Registos do Monitor Azure](saas-dbpertenant-log-analytics.md) - Configuração e utilização de um tutorial de registos do Monitor Azure
