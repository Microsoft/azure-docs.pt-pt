---
title: Monitorize o desempenho de uma base de dados de vários inquilinos
description: Monitorize e gere o desempenho da base de dados Azure SQL de vários inquilinos em uma aplicação SaaS multi-arrendatário
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
ms.openlocfilehash: d37bf2c84b74dba76e5d1921ed67072af7f6c328
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "92790904"
---
# <a name="monitor-and-manage-performance-of-sharded-multi-tenant-azure-sql-database-in-a-multi-tenant-saas-app"></a>Monitorize e gere o desempenho da base de dados Azure SQL de vários inquilinos em uma aplicação SaaS multi-arrendatário
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Neste tutorial, são explorados vários cenários-chave de gestão de desempenho utilizados nas aplicações do SaaS. Utilizando um gerador de carga para simular a atividade através de bases de dados de vários inquilinos, são demonstradas as funcionalidades de monitorização e alerta incorporadas da Base de Dados Azure SQL.

A app Wingtip Tickets SaaS Multi-tenant Database utiliza um modelo de dados de vários inquilinos, onde os dados do local (inquilino) são distribuídos pelo ID do inquilino através de bases de dados potencialmente múltiplas. Como em muitas aplicações SaaS, o padrão de carga de trabalho do inquilino antecipado é imprevisível e esporádico. Por outras palavras, as vendas de bilhetes podem ocorrer em qualquer altura. Para tirar partido deste padrão típico de utilização da base de dados, as bases de dados podem ser dimensionadas para cima e para baixo para otimizar o custo de uma solução. Com este tipo de padrão, é importante monitorizar o uso de recursos de base de dados para garantir que as cargas são razoavelmente equilibradas em bases de dados potencialmente múltiplas. Também é necessário garantir que as bases de dados individuais possuam recursos adequados e não estão a atingir os seus limites [de DTU.](purchasing-models.md#dtu-based-purchasing-model) Este tutorial explora formas de monitorizar e gerir bases de dados, e como tomar medidas corretivas em resposta a variações na carga de trabalho.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> 
> * Simular o uso numa base de dados de vários inquilinos com um gerador de carga fornecido
> * Monitorize a base de dados à medida que responde ao aumento da carga
> * Dimensione a base de dados em resposta ao aumento da carga da base de dados
> * Provisione um inquilino numa base de dados de inquilinos únicos

Para concluir este tutorial, devem ser cumpridos os seguintes pré-requisitos:

* A aplicação Wingtip Tickets SaaS Multi-tenant Database está implementada. Para implementar em menos de cinco minutos, consulte [implementar e explorar a aplicação de Base de Dados De Multi-inquilinos Wingtip SaaS](./saas-multitenantdb-get-started-deploy.md)
* O Azure PowerShell está instalado. Para obter mais detalhes, veja [Introdução ao Azure PowerShell](/powershell/azure/get-started-azureps)

## <a name="introduction-to-saas-performance-management-patterns"></a>Introdução aos padrões de gestão de desempenho do SaaS

Gerir o desempenho da base de dados consiste em compilar e analisar dados de desempenho e, em seguida, reagir a estes dados ajustando os parâmetros para manter um tempo de resposta aceitável para a aplicação. 

### <a name="performance-management-strategies"></a>Estratégias da gestão do desempenho

* Para evitar ter de monitorizar manualmente o desempenho, é mais eficaz **definir alertas que desencadeiam quando as bases de dados se desviam dos intervalos normais**.
* Para responder a flutuações de curto prazo no tamanho do cálculo de uma base de dados, o **nível de DTU pode ser dimensionado para cima ou para baixo**. Se esta flutuação ocorrer numa base regular ou previsível, **a escala da base de dados pode ser programada automaticamente**. Por exemplo, reduza verticalmente quando sabe que a carga de trabalho é leve, talvez durante a noite ou durante os fins de semana.
* Para responder a flutuações a longo prazo, ou alterações nos inquilinos, **os inquilinos individuais podem ser transferidos para outras bases de dados.**
* Para responder aos aumentos a curto prazo da carga *individual* **de inquilinos, os inquilinos individuais podem ser retirados de uma base de dados e atribuídos um tamanho de computação individual.** Uma vez que a carga é reduzida, o inquilino pode então ser devolvido à base de dados multi-inquilinos. Quando isto é conhecido antecipadamente, os inquilinos podem ser movidos preventivamente para garantir que a base de dados tem sempre os recursos de que necessita, e para evitar impactos em outros inquilinos na base de dados multi-inquilinos. Se este requisito for previsível, por exemplo, quando um local tem uma grande procura de bilhetes para um evento popular, este comportamento de gestão poderá ser integrado na aplicação.

O [portal do Azure](https://portal.azure.com) fornece monitorização e alertas incorporados na maior parte dos recursos. Para a Base de Dados SQL, a monitorização e alerta estão disponíveis nas bases de dados. Este monitor incorporado e alerta é específico de recursos, por isso é conveniente usar para um pequeno número de recursos, mas não é conveniente quando se trabalha com muitos recursos.

Para cenários de grande volume, onde você está trabalhando com muitos recursos, [os registos do Azure Monitor](https://azure.microsoft.com/services/log-analytics/) podem ser usados. Este é um serviço Azure separado que fornece análise sobre registos emitidos recolhidos num espaço de trabalho log analytics. Os registos do Azure Monitor podem recolher telemetria de muitos serviços e ser usados para consultar e definir alertas.

## <a name="get-the-wingtip-tickets-saas-multi-tenant-database-application-source-code-and-scripts"></a>Obtenha o código fonte de origem de aplicação de aplicação de pedidos de bases de dados de ponta da Wingtip SaaS Multi-inquilinos

Os scripts de base de dados de multi-inquilinos Wingtip SaaS e código fonte de aplicação estão disponíveis no [wingtipTicketsSaS-MultitenantDB](https://github.com/microsoft/WingtipTicketsSaaS-MultiTenantDB) GitHub repo. Confira as [orientações gerais](saas-tenancy-wingtip-app-guidance-tips.md) para as etapas para descarregar e desbloquear os scripts SaaS dos Bilhetes Wingtip.

## <a name="provision-additional-tenants"></a>Aprovisionar inquilinos adicionais

Para uma boa compreensão de como a monitorização e gestão de desempenho funcionam em escala, este tutorial requer que você tenha vários inquilinos em uma base de dados de vários inquilinos.

Se já forte um lote de inquilinos num tutorial anterior, salte para o [uso simulado em todas as bases de dados de inquilinos.](#simulate-usage-on-all-tenant-databases)

1. No **PowerShell ISE,** aberto... \\ Módulos de Aprendizagem \\ Monitorização e Gestão \\ *de DesempenhoDemo-PerformanceMonitoringAndManagement.ps1*. Mantenha este script aberto, uma vez que vai executar vários cenários durante este tutorial.
1. Definir **$DemoScenario**  =  **1**, _Provision um lote de inquilinos_
1. Prima **F5** para executar o script.

O guião coloca 17 inquilinos na base de dados de vários inquilinos em poucos minutos. 

O script *New-TenantBatch* cria novos inquilinos com chaves de inquilino únicas dentro da base de dados de vários inquilinos e inicializa-os com o nome do inquilino e o tipo de local. Isto é consistente com a forma como a aplicação prevê um novo inquilino. 

## <a name="simulate-usage-on-all-tenant-databases"></a>Simular a utilização em todas as bases de dados de inquilinos

O *Demo-PerformanceMonitoringAndManagement.ps1* script é fornecido que simula uma carga de trabalho que corre contra a base de dados de vários inquilinos. A carga é gerada utilizando um dos cenários de carga disponíveis:

| Demonstração | Scenario |
|:--|:--|
| 2 | Gerar carga de intensidade normal (aproximadamente 30 DTU) |
| 3 | Gerar carga com rajadas mais longas por inquilino|
| 4 | Gerar carga com rajadas de DTU mais altas por inquilino (aproximadamente 70 DTU)|
| 5 | Gere uma alta intensidade (aproximadamente 90 DTU) num único inquilino mais uma carga de intensidade normal em todos os outros inquilinos |

O gerador de carga aplica uma carga *sintética* só na CPU para cada base de dados do inquilino. O gerador inicia uma tarefa para cada base de dados de inquilino, que chama um procedimento armazenado que periodicamente gera a carga. Os níveis de carga (em DTUs), duração e intervalos são variados em todas as bases de dados, simulando atividade de inquilino imprevisível.

1. No **PowerShell ISE,** aberto... \\ Módulos de Aprendizagem \\ Monitorização e Gestão \\ *de DesempenhoDemo-PerformanceMonitoringAndManagement.ps1*. Mantenha este script aberto, uma vez que vai executar vários cenários durante este tutorial.
1. Definir **$DemoScenario**  =  **2**, _Gerar carga de intensidade normal_
1. Pressione **f5** para aplicar uma carga a todos os seus inquilinos.

Wingtip Tickets SaaS Multi-tenant Database é uma aplicação SaaS, e a carga do mundo real numa aplicação SaaS é tipicamente esporádica e imprevisível. Para simular isto, o gerador de carga produz uma carga aleatória distribuída por todos os inquilinos. São necessários vários minutos para que o padrão de carga emerja, por isso, carregue o gerador de carga durante 3-5 minutos antes de tentar monitorizar a carga nas seguintes secções.

> [!IMPORTANT]
> O gerador de carga está a funcionar como uma série de trabalhos numa nova janela PowerShell. Se fechar a sessão, o gerador de carga para. O gerador de carga permanece num estado *de invocação de emprego* onde gera carga sobre quaisquer novos inquilinos que sejam a provisionados após o início do gerador. Use *ctrl-C* para parar de invocar novos empregos e sair do guião. O gerador de carga continuará a funcionar, mas apenas com os inquilinos existentes.

## <a name="monitor-resource-usage-using-the-azure-portal"></a>Monitorize a utilização de recursos utilizando o portal Azure

Para monitorizar a utilização dos recursos resultante da aplicação da carga, abra o portal para a base de **dados multi-inquilinos, inquilinos1,** contendo os inquilinos:

1. Abra o [portal Azure](https://portal.azure.com) e navegue para os inquilinos do *servidor1-mt-USER &lt; &gt;*.
1. Percorra as bases de dados e clique nos **inquilinos1**. Esta base de dados de vários inquilinos contém todos os inquilinos criados até agora.

![gráfico de base de dados](./media/saas-multitenantdb-performance-monitoring/multitenantdb.png)

Observe a ficha do **DTU.**

## <a name="set-performance-alerts-on-the-database"></a>Definir alertas de desempenho na base de dados

Desatenção na base de dados que ativa \> uma utilização de 75% da seguinte forma:

1. Abra a base de dados dos *inquilinos1* (no servidor *&lt; utilizadores &gt; de inquilinos1-mt)* no [portal Azure](https://portal.azure.com).
1. Clique em **Regras de Alerta** e, em seguida, clique em **+ Adicionar alerta**:

   ![adicionar alerta](./media/saas-multitenantdb-performance-monitoring/add-alert.png)

1. Forneça um nome, tal como **DTU elevada**,
1. Defina os seguintes valores:
   * **Métrica = percentagem de DTU**
   * **Condição = maior do que**
   * **Limiar = 75**.
   * **Período = Nos últimos 30 minutos**
1. Adicione um endereço de e-mail à caixa *de e-mails do administrador adicional* e clique em **OK**.

   ![alerta de definição](./media/saas-multitenantdb-performance-monitoring/set-alert.png)

## <a name="scale-up-a-busy-database"></a>Dimensione uma base de dados ocupada

Se o nível de carga aumentar numa base de dados ao ponto de atingir a base de dados e atingir uma utilização de DTU a 100%, então o desempenho da base de dados é afetado, abrandando potencialmente os tempos de resposta da consulta.

**A curto prazo,** considere aumentar a base de dados para fornecer recursos adicionais, ou remover os inquilinos da base de dados multi-inquilinos (transferindo-os da base de dados multi-inquilinos para uma base de dados autónoma).

**A longo prazo,** considere otimizar consultas ou indexar o uso para melhorar o desempenho da base de dados. Dependendo da sensibilidade da aplicação para problemas de desempenho, a sua melhor prática para escalar uma base de dados antes de atingir o uso de 100% de DTU. Utilize um alerta para avisá-lo antecipadamente.

Pode simular uma base de dados ocupada aumentando a carga produzida pelo gerador. Fazendo com que os inquilinos rebentem com mais frequência, e por mais tempo, aumentando a carga na base de dados de vários inquilinos sem alterar os requisitos dos inquilinos individuais. A escala da base de dados é facilmente feita no portal ou na PowerShell. Este exercício utiliza o portal.

1. Definir *$DemoScenario*  =  **3**, Gere a carga com _rajadas mais longas e mais frequentes por base_ de dados para aumentar a intensidade da carga agregada na base de dados sem alterar a carga máxima exigida por cada inquilino.
1. Prima **F5** para aplicar uma carga a todas as bases de dados de inquilinos.
1. Vá à base de dados dos **inquilinos1** no portal Azure.

Monitorize o aumento da utilização do DTU na tabela superior. A carga mais alta leva alguns minutos para que a nova carga superior faça efeito, mas deve rapidamente ver a base de dados começar a atingir a utilização máxima, e à medida que a carga se mantém no novo padrão, sobrecarrega rapidamente a base de dados.

1. Para escalar a base de dados, clique em **Nível de Preços (DTUs** de escala) na lâmina de definição.
1. Ajuste a regulação **DTU** para **100**. 
1. Clique **em Aplicar** para submeter o pedido para escalar a base de dados.

Volte para os **inquilinos1**  >  **visão geral** para ver os gráficos de monitorização. Monitorize o efeito de fornecer a base de dados com mais recursos (embora com poucos inquilinos e uma carga aleatória nem sempre seja fácil ver conclusivamente até correr durante algum tempo). Enquanto você está olhando para os gráficos tenha em mente que 100% na tabela superior agora representa 100 DTUs, enquanto no gráfico inferior 100% ainda é 50 DTUs.

As bases de dados permanecem online e estão totalmente disponíveis durante o processo. O código de aplicação deve ser sempre escrito para tentar novamente as ligações deixadas, e assim reconectar-se-á novamente à base de dados.

## <a name="provision-a-new-tenant-in-its-own-database"></a>Provisiona um novo inquilino na sua própria base de dados 

O modelo de multi-inquilinos em pedaços permite-lhe escolher se fornece um novo inquilino numa base de dados multi-inquilinos ao lado de outros inquilinos, ou providenciar o inquilino numa base de dados própria. Ao providenciar um inquilino na sua própria base de dados, beneficia do isolamento inerente à base de dados separada, permitindo-lhe gerir o desempenho desse inquilino independentemente de outros, restaurar esse inquilino independentemente dos outros, etc. Por exemplo, pode optar por colocar clientes de teste gratuito ou regulares numa base de dados multi-inquilinos e clientes premium em bases de dados individuais.  Se forem criadas bases de dados isoladas de inquilinos individuais, ainda podem ser geridas coletivamente numa piscina elástica para otimizar os custos dos recursos.

Se já adquirou um novo inquilino na sua própria base de dados, ignore os próximos passos.

1. No **PowerShell ISE,** aberto... \\ Módulos de Aprendizagem \\ ProvisionTenants \\ *Demo-ProvisionTenants.ps1*. 
1. Modificar **$TenantName = "Salix Salsa"** e **$VenueType = "dance"**
1. Definir **$Scenario**  =  **2**, _Provision a um inquilino em uma nova base de dados de inquilino único_
1. Prima **F5** para executar o script.

O script irá providenciar este inquilino numa base de dados separada, registar a base de dados e o inquilino com o catálogo e, em seguida, abrir a página eventos do inquilino no navegador. Refresque a página do Centro de Eventos e verá "Salix Salsa" como local.

## <a name="manage-performance-of-an-individual-database"></a>Gerir o desempenho de uma base de dados individual

Se um único inquilino dentro de uma base de dados multi-inquilino sofre uma carga elevada sustentada, pode tender a dominar os recursos da base de dados e impactar outros inquilinos na mesma base de dados. Se a atividade continuar por algum tempo, o inquilino pode ser temporariamente transferido para fora da base de dados e para a sua própria base de dados de inquilinos únicos. Isto permite ao inquilino ter os recursos extras de que necessita, e isola-o totalmente dos outros inquilinos.

Este exercício simula o efeito de Salix Salsa experimentar uma carga elevada quando os bilhetes estão à venda para um evento popular.

1. Abra o ... \\ *Demo-PerformanceMonitoringAndManagement.ps1* guião.
1. Definir **$DemoScenario = 5**, _Gerar uma carga normal mais uma carga elevada sobre um único inquilino (aproximadamente 90 DTU)._
1. Set **$SingleTenantName = Salix Salsa**
1. Execute o script com **F5**.

Vá ao portal e navegue para **salixsalsa**  >  **Overview** para ver os gráficos de monitorização. 

## <a name="other-performance-management-patterns"></a>Outros padrões de gestão de desempenho

**Escala de autosserviço do inquilino**

Como o dimensionamento é uma tarefa facilmente chamada através da API de gestão, você pode facilmente construir a capacidade de escalar bases de dados de inquilinos em sua aplicação virada para o inquilino, e oferecê-la como uma característica do seu serviço SaaS. Por exemplo, permita que os inquilinos administrem automaticamente o aumento ou redução vertical, talvez ligando-os diretamente à faturação deles!

**Escalonamento de uma base de dados para cima e para baixo em um horário para combinar padrões de uso**

Quando o uso agregado de inquilinos segue padrões de utilização previsíveis, você pode usar a Azure Automation para escalar uma base de dados para cima e para baixo em um horário. Por exemplo, reduza a base de dados depois das 18h e volte a subir antes das 6h nos dias úteis quando sabe que há uma queda nos requisitos de recursos.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Simular o uso numa base de dados de vários inquilinos com um gerador de carga fornecido
> * Monitorize a base de dados à medida que responde ao aumento da carga
> * Dimensione a base de dados em resposta ao aumento da carga da base de dados
> * Provisione um inquilino numa base de dados de inquilinos únicos

## <a name="additional-resources"></a>Recursos adicionais

<!--* [Additional tutorials that build upon the Wingtip Tickets SaaS Multi-tenant Database application deployment](saas-multitenantdb-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)-->
* [Automação azul](../../automation/automation-intro.md)