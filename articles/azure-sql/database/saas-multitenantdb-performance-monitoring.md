---
title: Monitorizar o desempenho de uma base de dados de multi-inquilinos espumosos
description: Monitorize e gerencie o desempenho da base de dados Azure SQL multi-inquilino numa aplicação SaaS multi-inquilino
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: seo-lt-2019, sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/25/2019
ms.openlocfilehash: e4f26b21ad7458b4f5bcad9a902f4e048d726f1f
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "84042436"
---
# <a name="monitor-and-manage-performance-of-sharded-multi-tenant-azure-sql-database-in-a-multi-tenant-saas-app"></a>Monitorize e gerencie o desempenho da base de dados Azure SQL multi-inquilino numa aplicação SaaS multi-inquilino
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Neste tutorial, são explorados vários cenários-chave de gestão de desempenho utilizados nas aplicações SaaS. Utilizando um gerador de carga para simular atividade através de bases de dados multi-inquilinos, são demonstradas as funcionalidades de monitorização e alerta incorporadas da Base de Dados Azure SQL.

A app Wingtip Tickets SaaS Multi-tenant Database usa um modelo de dados multi-inquilinos, onde os dados do local (inquilino) são distribuídos pelo ID do inquilino através de bases de dados potencialmente múltiplas. Como em muitas aplicações SaaS, o padrão de carga de trabalho do inquilino antecipado é imprevisível e esporádico. Por outras palavras, as vendas de bilhetes podem ocorrer em qualquer altura. Para tirar partido deste padrão típico de utilização da base de dados, as bases de dados podem ser dimensionadas para cima e para baixo para otimizar o custo de uma solução. Com este tipo de padrão, é importante monitorizar o uso de recursos de base de dados para garantir que as cargas são razoavelmente equilibradas em várias bases de dados. É também necessário garantir que as bases de dados individuais possuem recursos adequados e que não atinjam os seus limites [de DTU.](purchasing-models.md#dtu-based-purchasing-model) Este tutorial explora formas de monitorizar e gerir bases de dados, e como tomar medidas corretivas em resposta a variações na carga de trabalho.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> 
> * Simular o uso numa base de dados de multi-inquilinos espumosos executando um gerador de carga fornecido
> * Monitorize a base de dados à medida que responde ao aumento da carga
> * Aumentar a base de dados em resposta ao aumento da carga da base de dados
> * Provisão de um inquilino para uma base de dados de um único inquilino

Para concluir este tutorial, devem ser cumpridos os seguintes pré-requisitos:

* A aplicação de base de dados de bilhetes SaaS Multi-inquilinos SaaS está implementada. Para implantar em menos de cinco minutos, consulte Implementar e explorar a aplicação de [base de dados multi-inquilinos SaaS](../../sql-database/saas-multitenantdb-get-started-deploy.md)
* O Azure PowerShell está instalado. Para obter mais detalhes, veja [Introdução ao Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps)

## <a name="introduction-to-saas-performance-management-patterns"></a>Introdução aos padrões de gestão de desempenho da SaaS

Gerir o desempenho da base de dados consiste em compilar e analisar dados de desempenho e, em seguida, reagir a estes dados ajustando os parâmetros para manter um tempo de resposta aceitável para a aplicação. 

### <a name="performance-management-strategies"></a>Estratégias da gestão do desempenho

* Para evitar ter de monitorizar manualmente o desempenho, é mais eficaz **definir alertas que disparam quando as bases de dados se desviam dos intervalos normais**.
* Para responder a flutuações a curto prazo no tamanho da computação de uma base de dados, o **nível de DTU pode ser escalado para cima ou para baixo**. Se esta flutuação ocorrer regularmente ou previsível, **a escala da base de dados pode ser programada para ocorrer automaticamente**. Por exemplo, reduza verticalmente quando sabe que a carga de trabalho é leve, talvez durante a noite ou durante os fins de semana.
* Para responder a flutuações a longo prazo, ou alterações nos inquilinos, os **inquilinos individuais podem ser transferidos para outra base de dados.**
* Para responder aos aumentos a curto prazo da carga *individual* **dos inquilinos, os inquilinos individuais podem ser retirados de uma base de dados e atribuídos a uma dimensão individual**da computação . Uma vez reduzida a carga, o inquilino pode então ser devolvido à base de dados multi-inquilinos. Quando isso é conhecido antecipadamente, os inquilinos podem ser movidos preventivamente para garantir que a base de dados tem sempre os recursos de que necessita, e para evitar o impacto em outros inquilinos na base de dados multi-inquilinos. Se este requisito for previsível, por exemplo, quando um local tem uma grande procura de bilhetes para um evento popular, este comportamento de gestão poderá ser integrado na aplicação.

O [portal do Azure](https://portal.azure.com) fornece monitorização e alertas incorporados na maior parte dos recursos. Para a Base de Dados SQL, a monitorização e alerta estão disponíveis nas bases de dados. Esta monitorização e alerta incorporados é específica de recursos, por isso é conveniente usar para um pequeno número de recursos, mas não é conveniente quando se trabalha com muitos recursos.

Para cenários de grande volume, onde está a trabalhar com muitos recursos, os [registos do Monitor Azure](https://azure.microsoft.com/services/log-analytics/) podem ser usados. Este é um serviço Azure separado que fornece análises sobre registos emitidos recolhidos num espaço de trabalho log Analytics. Os registos do Azure Monitor podem recolher telemetria de muitos serviços e ser usados para consultar e definir alertas.

## <a name="get-the-wingtip-tickets-saas-multi-tenant-database-application-source-code-and-scripts"></a>Obtenha os bilhetes De acesso saaS Multi-inquilinoCódigo fonte de aplicação e scripts

Os bilhetes Wingtip SaaS Scripts de base de dados multi-inquilinos e código fonte de aplicação estão disponíveis no repo [WingtipTicketsSaaS-MultitenantDB](https://github.com/microsoft/WingtipTicketsSaaS-MultiTenantDB) GitHub. Confira as [orientações gerais](saas-tenancy-wingtip-app-guidance-tips.md) para os passos para descarregar e desbloquear os scripts Wingtip Tickets SaaS.

## <a name="provision-additional-tenants"></a>Aprovisionar inquilinos adicionais

Para uma boa compreensão de como funciona a monitorização e gestão de desempenho em escala, este tutorial requer que você tenha vários inquilinos numa base de dados de multi-inquilinos.

Se já disponibilizou um lote de inquilinos num tutorial anterior, salte para o uso simular em todas as bases de dados dos [inquilinos.](#simulate-usage-on-all-tenant-databases)

1. No **PowerShell ISE,** aberto ... \\ Módulos de Aprendizagem \\ Monitorização de Desempenho e Gestão \\ *Demo-PerformanceMonitoringAndManagement.ps1*. Mantenha este script aberto, uma vez que vai executar vários cenários durante este tutorial.
1. Conjunto **$DemoScenario**  =  **1**, _Provisiona um lote de inquilinos_
1. Prima **F5** para executar o script.

O guião coloca 17 inquilinos na base de dados de vários inquilinos em poucos minutos. 

O script *New-TenantBatch* cria novos inquilinos com chaves únicas de inquilino dentro da base de dados de multi-inquilinos e inicializa-os com o nome do inquilino e o tipo de local. Isto é consistente com a forma como a aplicação prevê um novo inquilino. 

## <a name="simulate-usage-on-all-tenant-databases"></a>Simular a utilização em todas as bases de dados de inquilinos

O script *Demo-PerformanceMonitoringAndManagement.ps1* é fornecido que simula uma carga de trabalho contra a base de dados multi-inquilinos. A carga é gerada utilizando um dos cenários de carga disponíveis:

| Demonstração | Cenário |
|:--|:--|
| 2 | Gerar carga de intensidade normal (aproximadamente 30 DTU) |
| 3 | Gerar carga com rajadas mais longas por inquilino|
| 4 | Gerar carga com explosões de DTU mais elevadas por inquilino (aproximadamente 70 DTU)|
| 5 | Gerar uma alta intensidade (aproximadamente 90 DTU) num único inquilino mais uma carga de intensidade normal em todos os outros inquilinos |

O gerador de carga aplica uma carga *sintética* só na CPU para cada base de dados do inquilino. O gerador inicia uma tarefa para cada base de dados de inquilino, que chama um procedimento armazenado que periodicamente gera a carga. Os níveis de carga (em DTUs), duração e intervalos são variados em todas as bases de dados, simulando atividade de inquilino imprevisível.

1. No **PowerShell ISE,** aberto ... \\ Módulos de Aprendizagem \\ Monitorização de Desempenho e Gestão \\ *Demo-PerformanceMonitoringAndManagement.ps1*. Mantenha este script aberto, uma vez que vai executar vários cenários durante este tutorial.
1. Set **$DemoScenario**  =  **2**, _Gerar carga de intensidade normal_
1. Pressione **F5** para aplicar uma carga a todos os seus inquilinos.

Wingtip Tickets SaaS Multi-inquilino Base de Dados é uma aplicação SaaS, e a carga real numa aplicação SaaS é tipicamente esporádica e imprevisível. Para simular isto, o gerador de carga produz uma carga aleatória distribuída por todos os inquilinos. São necessários vários minutos para que o padrão de carga emerja, por isso, coloque o gerador de carga durante 3-5 minutos antes de tentar monitorizar a carga nas seguintes secções.

> [!IMPORTANT]
> O gerador de carga está a funcionar como uma série de trabalhos numa nova janela da PowerShell. Se encerrar a sessão, o gerador de carga para. O gerador de carga permanece num estado *de invocação de emprego* onde gera carga em quaisquer novos inquilinos que são aprovisionados após o início do gerador. Use *ctrl-C* para parar de invocar novos empregos e sair do guião. O gerador de carga continuará a funcionar, mas apenas em inquilinos existentes.

## <a name="monitor-resource-usage-using-the-azure-portal"></a>Monitorizar o uso de recursos utilizando o portal Azure

Para monitorizar o uso de recursos que resulta da carga aplicada, abra o portal para a base de dados multi-arrendatária, **inquilinos1,** contendo os inquilinos:

1. Abra o [portal Azure](https://portal.azure.com) e navegue para os *inquilinos do servidor1-mt- &lt; USER &gt; *.
1. Desloque-se e localize bases de dados e clique **em inquilinos1**. Esta base de dados de multiinquilinos contém todos os inquilinos criados até agora.

![gráfico de base de dados](./media/saas-multitenantdb-performance-monitoring/multitenantdb.png)

Observe o gráfico **dTU.**

## <a name="set-performance-alerts-on-the-database"></a>Definir alertas de desempenho na base de dados

Deseme um alerta na base de dados que desencadeie uma utilização de \> 75% da seguinte forma:

1. Abra a base de dados dos *inquilinos1* (no servidor *de inquilinos1-mt- &lt; USER) &gt; * no [portal Azure](https://portal.azure.com).
1. Clique em **Regras de Alerta**e, em seguida, clique em **+ Adicionar alerta**:

   ![adicionar alerta](./media/saas-multitenantdb-performance-monitoring/add-alert.png)

1. Forneça um nome, tal como **DTU elevada**,
1. Defina os seguintes valores:
   * **Métrica = Percentagem de DTU**
   * **Condição = maior do que**
   * **Limiar = 75**.
   * **Período = Nos últimos 30 minutos**
1. Adicione um endereço de e-mail para a caixa *de e-mail do administrador adicional* e clique em **OK**.

   ![alerta de definição](./media/saas-multitenantdb-performance-monitoring/set-alert.png)

## <a name="scale-up-a-busy-database"></a>Aumentar uma base de dados movimentada

Se o nível de carga aumentar numa base de dados ao ponto de atingir a base de dados e atingir 100% de utilização de DTU, então o desempenho da base de dados é afetado, potencialmente abrandando os tempos de resposta à consulta.

**A curto prazo,** considere a escala da base de dados para fornecer recursos adicionais ou a remoção dos inquilinos da base de dados multi-arrendatária (transferindo-os da base de dados multi-inquilinos para uma base de dados autónoma).

**A longo prazo,** considere otimizar consultas ou utilizar índices para melhorar o desempenho da base de dados. Dependendo da sensibilidade da aplicação às questões de desempenho, as suas melhores práticas para escalar uma base de dados antes de atingir o uso 100% DTU. Utilize um alerta para avisá-lo antecipadamente.

Pode simular uma base de dados movimentada aumentando a carga produzida pelo gerador. Fazendo com que os inquilinos rebentem com mais frequência e, por mais tempo, aumentem a carga na base de dados multi-inquilinos sem alterar os requisitos dos inquilinos individuais. A escala da base de dados é facilmente feita no portal ou no PowerShell. Este exercício utiliza o portal.

1. Conjunto *$DemoScenario*  =  **3**, _Gere carga com explosões mais longas e mais frequentes por base_ de dados para aumentar a intensidade da carga agregada na base de dados sem alterar a carga máxima exigida por cada inquilino.
1. Prima **F5** para aplicar uma carga a todas as bases de dados de inquilinos.
1. Vá à base de dados dos **inquilinos1** no portal Azure.

Monitorize o uso de DTU da base de dados aumentado na tabela superior. Demora alguns minutos para que a nova carga mais elevada faça efeito, mas deve ver rapidamente a base de dados começar a atingir a utilização máxima, e à medida que a carga se mantém no novo padrão, sobrecarrega rapidamente a base de dados.

1. Para aumentar a base de dados, clique no nível de **preços (DTUs de escala)** na lâmina de definições.
1. Ajuste a regulação **dTU** para **100**. 
1. Clique **Em Aplicar** para submeter o pedido para escalar a base de dados.

Volte para os **inquilinos1**  >  **Visão geral** para ver as tabelas de monitorização. Monitorize o efeito de fornecer à base de dados mais recursos (embora com poucos inquilinos e uma carga aleatória nem sempre seja fácil de ver conclusivamente até correr durante algum tempo). Enquanto você está olhando para os gráficos lembre-se que 100% na tabela superior agora representa 100 DTUs, enquanto na tabela inferior 100% ainda é 50 DTUs.

As bases de dados permanecem online e estão totalmente disponíveis durante o processo. O código de aplicação deve ser sempre escrito para voltar a tentar ligações abandonadas, pelo que voltará a ligar-se à base de dados.

## <a name="provision-a-new-tenant-in-its-own-database"></a>Provisão de um novo inquilino na sua própria base de dados 

O modelo de multiinquilino sharded permite-lhe escolher se deve fornecer um novo inquilino numa base de dados multi-inquilinos ao lado de outros inquilinos, ou para fornecer o inquilino numa base de dados própria. Ao fornecer um inquilino na sua própria base de dados, beneficia do isolamento inerente à base de dados separada, permitindo-lhe gerir o desempenho desse inquilino independentemente de outros, restaurar esse inquilino independentemente de outros, etc. Por exemplo, pode optar por colocar clientes de teste livre ou regulares numa base de dados multi-inquilinos e clientes premium em bases de dados individuais.  Se forem criadas bases de dados isoladas de um único inquilino, elas ainda podem ser geridas coletivamente numa piscina elástica para otimizar os custos de recursos.

Se já disponibilizou um novo inquilino na sua própria base de dados, ignore os próximos passos.

1. No **PowerShell ISE,** aberto ... \\ Módulos de Aprendizagem \\ ProvisionTenants \\ *Demo-ProvisionTenants.ps1*. 
1. Modificar **$TenantName = "Salix Salsa"** e **$VenueType = "dance"**
1. Definir **$Scenario**  =  **2**, _Provisão de um inquilino numa nova base de dados de inquilinos únicos_
1. Prima **F5** para executar o script.

O script fornecerá este inquilino numa base de dados separada, registará a base de dados e o inquilino com o catálogo e, em seguida, abrirá a página de Eventos do inquilino no navegador. Refresque a página do Events Hub e verá que "Salix Salsa" foi adicionado como um local.

## <a name="manage-performance-of-an-individual-database"></a>Gerir o desempenho de uma base de dados individual

Se um único inquilino dentro de uma base de dados multi-inquilinos experimentar uma carga elevada sustentada, pode tender a dominar os recursos da base de dados e a afetar outros inquilinos na mesma base de dados. Se a atividade continuar durante algum tempo, o inquilino pode ser temporariamente transferido da base de dados para a sua própria base de dados de inquilinos únicos. Isto permite ao inquilino ter os recursos extra de que necessita, e isola-o totalmente dos outros inquilinos.

Este exercício simula o efeito da Salix Salsa experimentando uma carga elevada quando os bilhetes estão à venda para um evento popular.

1. Abra o ... \\ *Roteiro Demo-PerformanceMonitoringAndManagement.ps1.*
1. Coloque **$DemoScenario = 5**, Gere uma carga normal mais uma carga elevada num único inquilino _(aproximadamente 90 DTU)._
1. Set **$SingleTenantName = Salix Salsa**
1. Execute o script com **F5**.

Vá ao portal e navegue até à visão geral da **salixsalsa**  >  **Overview** para ver as tabelas de monitorização. 

## <a name="other-performance-management-patterns"></a>Outros padrões de gestão de desempenho

**Escalade autosserviço do inquilino**

Como o dimensionamento é uma tarefa facilmente chamada através da API de gestão, você pode facilmente construir a capacidade de escalar bases de dados de inquilinos em sua aplicação virada para o inquilino, e oferecê-la como uma característica do seu serviço SaaS. Por exemplo, permita que os inquilinos administrem automaticamente o aumento ou redução vertical, talvez ligando-os diretamente à faturação deles!

**Escalando uma base de dados para cima e para baixo em um horário para combinar padrões de uso**

Onde o uso agregado de inquilinos segue padrões de utilização previsíveis, você pode usar a Automatização Azure para escalar uma base de dados para cima e para baixo em um horário. Por exemplo, reduza uma base de dados depois das 18:00 e volte a subir antes das 6h nos dias úteis, quando souber que há uma diminuição dos requisitos de recursos.

## <a name="next-steps"></a>Próximos passos

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Simular o uso numa base de dados de multi-inquilinos espumosos executando um gerador de carga fornecido
> * Monitorize a base de dados à medida que responde ao aumento da carga
> * Aumentar a base de dados em resposta ao aumento da carga da base de dados
> * Provisão de um inquilino para uma base de dados de um único inquilino

## <a name="additional-resources"></a>Recursos adicionais

<!--* [Additional tutorials that build upon the Wingtip Tickets SaaS Multi-tenant Database application deployment](saas-multitenantdb-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)-->
* [Automação Azure](../../automation/automation-intro.md)