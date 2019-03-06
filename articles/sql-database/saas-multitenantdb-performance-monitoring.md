---
title: Monitorizar o desempenho de um em partição horizontal multi-inquilino do Azure base de dados SQL em aplicações SaaS multi-inquilino | Documentos da Microsoft
description: Monitorizar e gerir o desempenho da base de SQL do Azure multi-inquilino em partição horizontal em aplicações SaaS multi-inquilino de
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
ms.openlocfilehash: 731b87d7958bb461466b16f2ff724ea33f234ba8
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57439813"
---
# <a name="monitor-and-manage-performance-of-sharded-multi-tenant-azure-sql-database-in-a-multi-tenant-saas-app"></a>Monitorizar e gerir o desempenho da base de SQL do Azure multi-inquilino em partição horizontal em aplicações SaaS multi-inquilino de

Neste tutorial, serão explorados vários cenários de gerenciamento de chave de desempenho utilizados nas aplicações SaaS. Utilizar um gerador de carga para simular atividade entre bases de dados de multi-inquilinos em partição horizontal, as funcionalidades da base de dados SQL de alertas e monitorização incorporados são demonstrados.

A aplicação de base de dados do Wingtip Tickets SaaS multi-inquilino utiliza um modelo de dados em partição horizontal do multi-inquilino, onde os dados de local (inquilino) são distribuídos por ID de inquilino por potencialmente várias bases de dados. Como em muitas aplicações SaaS, o padrão de carga de trabalho do inquilino antecipado é imprevisível e esporádico. Por outras palavras, as vendas de bilhetes podem ocorrer em qualquer altura. Para tirar partido deste padrão de utilização típica da base de dados, bases de dados podem ser dimensionados e reduzir verticalmente para otimizar o custo de uma solução. Com esse tipo de padrão, é importante monitorizar a utilização de recursos de base de dados para se certificar de que as cargas estão razoavelmente equilibradas entre potencialmente várias bases de dados. Também precisa garantir que as bases de dados individuais têm os recursos adequados e não estão a atingir seus [DTU](sql-database-purchase-models.md#dtu-based-purchasing-model) limites. Este tutorial analisa formas para monitorizar e gerir bases de dados e como tomar uma ação corretiva em resposta a variações na carga de trabalho.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]

> * Simular a utilização num banco de dados em partição horizontal do multi-inquilino ao executar um gerador de carga fornecido
> * Monitorizar a base de dados, como ele responde à carga aumenta
> * Aumentar verticalmente a base de dados para a carga de base de dados aumentada
> * Aprovisionar um inquilino num banco de dados de inquilino único

Para concluir este tutorial, confirme que conclui os pré-requisitos seguintes:

* A aplicação de base de dados do Wingtip Tickets SaaS multi-inquilino é implementada. Para implementar em menos de cinco minutos, veja [implementar e explorar a aplicação de base de dados do Wingtip Tickets SaaS multi-inquilino](saas-multitenantdb-get-started-deploy.md)
* O Azure PowerShell está instalado. Para obter mais detalhes, veja [Introdução ao Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps)

## <a name="introduction-to-saas-performance-management-patterns"></a>Introdução aos padrões de gestão do desempenho de SaaS

Gerir o desempenho da base de dados consiste em compilar e analisar dados de desempenho e, em seguida, reagir a estes dados ajustando os parâmetros para manter um tempo de resposta aceitável para a aplicação. 

### <a name="performance-management-strategies"></a>Estratégias da gestão do desempenho

* Para evitar ter de monitorizar manualmente o desempenho, é mais eficaz **definir alertas que acionam quando as bases de dados se afastarem dos intervalos normais**.
* Para responder a flutuações de curto prazo do tamanho de computação de uma base de dados, o **nível DTU pode ser aumentado vertical ou horizontalmente**. Se esta flutuação ocorrer numa base regular ou previsível, **dimensionar a base de dados pode ser agendado para ocorrer automaticamente**. Por exemplo, reduza verticalmente quando sabe que a carga de trabalho é leve, talvez durante a noite ou durante os fins de semana.
* Para responder a flutuações de duração mais, ou alterações em inquilinos, **inquilinos individuais podem ser movidos para outro banco de dados**.
* Para responder a aumentos de curto prazo na *individuais* carga de inquilino **inquilinos individuais podem ser retirados uma base de dados e atribuídos um tamanho de computação individual**. Assim que a carga for reduzida, o inquilino, em seguida, pode ser retornado para a base de dados do multi-inquilino. Quando isso é conhecido antecipadamente, os inquilinos podem ser movidos previamente para garantir que a base de dados tem sempre os recursos de que necessita e para evitar o impacto nos outros inquilinos na base de dados do multi-inquilino. Se este requisito for previsível, por exemplo, quando um local tem uma grande procura de bilhetes para um evento popular, este comportamento de gestão poderá ser integrado na aplicação.

O [portal do Azure](https://portal.azure.com) fornece monitorização e alertas incorporados na maior parte dos recursos. Base de dados SQL, monitorização e alertas estão disponível em bases de dados. Estes alertas e monitorização incorporados são específicos dos recursos, portanto, é útil para utilização em pequenos números de recursos, mas não é conveniente quando trabalha com muitos recursos.

Para cenários de volume elevado, em que trabalha com muitos recursos, [registos do Azure Monitor](https://azure.microsoft.com/services/log-analytics/) pode ser utilizado. Este é um serviço do Azure separado que fornece análises sobre os registos de diagnóstico emitidos e a telemetria recolhida na área de trabalho do Log Analytics. Registos de Monitor do Azure podem recolher telemetria de muitos serviços e ser utilizados para consultar e definir alertas.

## <a name="get-the-wingtip-tickets-saas-multi-tenant-database-application-source-code-and-scripts"></a>Obter o código de origem da aplicação de base de dados do Wingtip Tickets SaaS multi-inquilino e os scripts

Os scripts de base de dados do Wingtip Tickets SaaS multi-inquilino e o código de origem da aplicação estão disponíveis no [WingtipTicketsSaaS MultitenantDB](https://github.com/microsoft/WingtipTicketsSaaS-MultiTenantDB) repositório do GitHub. Veja a [orientações gerais](saas-tenancy-wingtip-app-guidance-tips.md) para obter os passos transferir e os scripts de Wingtip Tickets SaaS de desbloqueio.

## <a name="provision-additional-tenants"></a>Aprovisionar inquilinos adicionais

Para uma boa compreensão de como funciona a monitorização do desempenho e gestão em escala, este tutorial requer que tenha vários inquilinos numa base de dados em partição horizontal do multi-inquilino.

Se já tiver aprovisionado um lote de inquilinos num tutorial anterior, avance para o [simular utilização em todas as bases de dados de inquilinos](#simulate-usage-on-all-tenant-databases) secção.

1. Na **ISE do PowerShell**, abra... \\Módulos de aprendizagem\\monitoramento de desempenho e gerenciamento\\*Demo-PerformanceMonitoringAndManagement.ps1*. Mantenha este script aberto, uma vez que vai executar vários cenários durante este tutorial.
1. Defina **$DemoScenario** = **1**, _Aprovisionar um lote de inquilinos_
1. Prima **F5** para executar o script.

O script implementa 17 inquilinos na base de dados do multi-inquilino dentro de alguns minutos. 

O *New-TenantBatch* script cria novos inquilinos ao inquilino exclusivo chaves na base de dados em partição horizontal do multi-inquilino e os inicializa com o tipo de nome e o local do inquilino. Isso é consistente com a forma como a aplicação aprovisiona um novo inquilino. 

## <a name="simulate-usage-on-all-tenant-databases"></a>Simular a utilização em todas as bases de dados de inquilinos

O *Demo-PerformanceMonitoringAndManagement.ps1* script é fornecida que simula uma carga de trabalho em execução na base de dados do multi-inquilino. A carga é gerada usando um dos cenários de carga disponíveis:

| Demonstração | Cenário |
|:--|:--|
| 2 | Gerar carga de intensidade normal (aproximadamente 30 DTU) |
| 3 | Gerar carga com picos mais por inquilino|
| 4 | Gerar carga com picos de DTU mais altos por inquilino (cerca de 70 DTU)|
| 5 | Gerar uma alta intensidade (aproximadamente 90 DTUS) num único inquilino mais de uma carga de intensidade normal em todos os outros inquilinos |

O gerador de carga aplica uma carga *sintética* só na CPU para cada base de dados do inquilino. O gerador inicia uma tarefa para cada base de dados de inquilino, que chama um procedimento armazenado que periodicamente gera a carga. Os níveis de carga (em DTUs), a duração e intervalos são diversificados em todas as bases de dados, simulando a atividade imprevisível do inquilino.

1. Na **ISE do PowerShell**, abra... \\Módulos de aprendizagem\\monitoramento de desempenho e gerenciamento\\*Demo-PerformanceMonitoringAndManagement.ps1*. Mantenha este script aberto, uma vez que vai executar vários cenários durante este tutorial.
1. Definir **$DemoScenario** = **2**, _gerar carga de intensidade normal_
1. Prima **F5** para aplicar uma carga para todos os seus inquilinos.

Base de dados do Wingtip Tickets SaaS multi-inquilino é uma aplicação SaaS e a carga do mundo real numa aplicação SaaS é, normalmente, esporádica e imprevisível. Para simular isto, o gerador de carga produz uma carga aleatória distribuída por todos os inquilinos. São necessários vários minutos para o padrão de carga a surgir, assim, executar o gerador de carga durante 3 a 5 minutos antes de tentar monitorizar a carga nas seções a seguir.

> [!IMPORTANT]
> O gerador de carga está em execução como uma série de tarefas numa nova janela do PowerShell. Se fechar a sessão, o gerador de carga será interrompido. O gerador de carga continuará a ser um *invocar a tarefa* Estado em que gera carga de quaisquer novos inquilinos aprovisionados após o gerador de início. Uso *Ctrl-C* para parar a invocar novas tarefas e sair do script. O gerador de carga continua em execução, mas somente em inquilinos existentes.

## <a name="monitor-resource-usage-using-the-azure-portal"></a>Monitorizar a utilização de recursos com o portal do Azure

Para monitorizar a utilização de recursos resultantes da carga que está a ser aplicada, abra o portal para a base de dados do multi-inquilino **tenants1**, que contém os inquilinos:

1. Abra o [portal do Azure](https://portal.azure.com) e navegue para o servidor *tenants1-mt -&lt;utilizador&gt;*.
1. Desloque para baixo e localizar as bases de dados e clique em **tenants1**. Esta base de dados do multi-inquilino em partição horizontal contém todos os inquilinos criados até.

![gráfico de base de dados](./media/saas-multitenantdb-performance-monitoring/multitenantdb.png)

Observe a **DTU** gráfico.

## <a name="set-performance-alerts-on-the-database"></a>Definir alertas de desempenho na base de dados

Definir um alerta na base de dados que aciona em \>75% de utilização da seguinte forma:

1. Abra o *tenants1* base de dados (no *tenants1-mt -&lt;utilizador&gt;*  servidor) no [portal do Azure](https://portal.azure.com).
1. Clique em **Regras de Alerta**e, em seguida, clique em **+ Adicionar alerta**:

   ![adicionar alerta](media/saas-multitenantdb-performance-monitoring/add-alert.png)

1. Forneça um nome, tal como **DTU elevada**,
1. Defina os seguintes valores:
   * **Métrica = percentagem de DTU**
   * **Condição = maior que**
   * **Limiar = 75**.
   * **Período = nos últimos 30 minutos**
1. Adicionar um endereço de e-mail para o *adicionais do administrador email(s)* caixa e clique em **OK**.

   ![alerta de definição](media/saas-multitenantdb-performance-monitoring/set-alert.png)

## <a name="scale-up-a-busy-database"></a>Aumentar verticalmente uma base de dados ocupada

Se aumentar o nível de carga numa base de dados para o ponto que maxes horizontalmente a base de dados e atingir 100% de utilização DTU, em seguida, o desempenho da base de dados é afetado, potencialmente reduzindo os tempos de resposta de consulta.

**A curto prazo**, considere aumentar verticalmente a base de dados para fornecer recursos adicionais ou remover inquilinos a partir da base de dados do multi-inquilino (movê-las fora da base de dados do multi-inquilino para uma base de dados autónomo).

**Longo prazo**, considere a otimização das consultas ou utilização de índices para melhorar o desempenho da base de dados. Dependendo da sensibilidade da aplicação para o desempenho emite a melhor prática para ampliar uma base de dados antes de atingir os 100% de utilização DTU. Utilize um alerta para avisá-lo antecipadamente.

Pode simular uma base de dados ocupado ao aumentar a carga produzida pelo gerador. Fazendo com que os inquilinos expandir mais tempo, com mais frequência e para aumentar a carga na base de dados do multi-inquilino sem alterar os requisitos dos inquilinos individuais. Aumentar verticalmente a base de dados é feito facilmente no portal ou do PowerShell. Este exercício utiliza o portal.

1. Definir *$DemoScenario* = **3**, _gerar carga com picos e mais frequentes por base de dados_ para aumentar a intensidade da carga agregada no base de dados sem alterar a carga de pico necessária por cada inquilino.
1. Prima **F5** para aplicar uma carga a todas as bases de dados de inquilinos.
1. Vá para o **tenants1** base de dados no portal do Azure.

Monitorize a base de dados maior utilização da DTU no gráfico superior. Demora alguns minutos para que a nova carga superior surgir, mas pode ver rapidamente a base de dados começar a alcançar uma utilização máxima e que a carga estabiliza num novo padrão, que rapidamente sobrecarrega o banco de dados.

1. Para aumentar verticalmente a base de dados, clique em **escalão de preço (DTUs de escala)** no painel Definições.
1. Ajustar a **DTU** definição **100**. 
1. Clique em **aplicar** para submeter o pedido para dimensionar a base de dados.

Volte ao **tenants1** > **descrição geral** para ver os gráficos de monitorização. Monitorize o efeito de fornecer a base de dados com mais recursos (embora com algumas inquilinos e uma carga aleatória não seja sempre fácil ver as consequências até executar durante algum tempo). Enquanto estiver a visualizar os gráficos tenha em atenção que 100% gráfico superior representa agora 100 DTUs, enquanto no gráfico inferior 100% é ainda 50 DTUs.

As bases de dados permanecem online e estão totalmente disponíveis durante o processo. Código da aplicação deve ser sempre escrito para repetir as quedas de conexões e por isso, voltar a ligar à base de dados.

## <a name="provision-a-new-tenant-in-its-own-database"></a>Aprovisionar um inquilino novo na sua própria base de dados 

O modelo de multi-inquilino em partição horizontal permite-lhe escolher se pretende aprovisionar um inquilino novo numa base de dados multi-inquilino juntamente com outros inquilinos ou para aprovisionar o inquilino numa base de dados por conta própria. Ao aprovisionar um inquilino na sua própria base de dados, ele se beneficia do isolamento inerente na base de dados separado, permitindo-lhe gerir o desempenho desse inquilino independentemente das outras pessoas, restaurar esse inquilino independentemente dos outros, etc. Por exemplo, poderá optar por colocar os clientes de avaliação gratuita ou regulares num banco de dados do multi-inquilino e clientes premium nas bases de dados individuais.  Se forem criadas bases de dados de inquilino único isoladas, podem ainda ser geridos coletivamente num conjunto elástico para otimizar os custos de recursos.

Se já tiver aprovisionado um novo inquilino na sua própria base de dados, ignore os passos seguintes.

1. Na **ISE do PowerShell**, abra... \\Módulos de aprendizagem\\ProvisionTenants\\*demonstração ProvisionTenants.ps1*. 
1. Modificar **$TenantName = "Salix Salsa"** e **$VenueType = "dançava"**
1. Definir **$Scenario** = **2**, _aprovisionar um inquilino numa nova base de dados de inquilino único_
1. Prima **F5** para executar o script.

O script irá aprovisionar este inquilino numa base de dados separado, registar a base de dados e o inquilino no catálogo e, em seguida, abra a página de eventos do inquilino no browser. Atualize a página de Hub de eventos e verá "Salix Salsa" foi adicionado como um local.

## <a name="manage-performance-of-an-individual-database"></a>Gerir o desempenho de uma base de dados individual

Se um único inquilino dentro de uma base de dados do multi-inquilino sofrer uma carga elevada constante, poderá ter tendência para dominar os recursos de base de dados e afetar outros inquilinos na mesma base de dados. Se a atividade é provável que continue durante algum tempo, o inquilino pode ser movido temporariamente fora da base de dados e em seu próprio banco de dados de inquilino único. Isso permite que o inquilino tenha os recursos extras que necessita e totalmente isola-a partir de outros inquilinos.

Este exercício simula o efeito de Salix Salsa com uma carga elevada quando os pedidos de suporte, aceda à venda para um evento popular.

1. Abra o... \\ *Demo-PerformanceMonitoringAndManagement.ps1* script.
1. Definir **$DemoScenario = 5**, _gerar uma carga normal + uma carga elevada num inquilino individual (aproximadamente 90 DTUS)._
1. Definir **$SingleTenantName = Salix Salsa**
1. Execute o script com **F5**.

Aceda ao portal e navegue para **salixsalsa** > **descrição geral** para ver os gráficos de monitorização. 

## <a name="other-performance-management-patterns"></a>Outros padrões de gestão de desempenho

**Dimensionamento de self-service de inquilino**

Porque o dimensionamento é uma tarefa facilmente chamado através da gestão de API, pode facilmente criar a capacidade de dimensionar bases de dados de inquilino na sua aplicação direcionada para o inquilino e oferecê-la como uma funcionalidade do serviço SaaS. Por exemplo, permita que os inquilinos administrem automaticamente o aumento ou redução vertical, talvez ligando-os diretamente à faturação deles!

**Dimensionar uma base de dados e reduzir verticalmente com base numa agenda de acordo com padrões de utilização**

Em que a utilização de agregação inquilino segue os padrões de utilização previsíveis, pode utilizar a automatização do Azure para dimensionar uma base de dados e reduzir verticalmente com base numa agenda. Por exemplo, reduza, uma base de dados após as 18:00 e cópia de segurança antes das 06:00 nos dias de semana quando sabe que existe uma quebra nos requisitos de recursos.

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Simular a utilização num banco de dados em partição horizontal do multi-inquilino ao executar um gerador de carga fornecido
> * Monitorizar a base de dados, como ele responde à carga aumenta
> * Aumentar verticalmente a base de dados para a carga de base de dados aumentada
> * Aprovisionar um inquilino num banco de dados de inquilino único

## <a name="additional-resources"></a>Recursos adicionais

<!--* [Additional tutorials that build upon the Wingtip Tickets SaaS Multi-tenant Database application deployment](saas-multitenantdb-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)-->
* [Automatização do Azure](../automation/automation-intro.md)