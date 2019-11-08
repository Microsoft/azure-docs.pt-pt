---
title: Monitorar o desempenho de um banco de dados multilocatário fragmentado
description: Monitorar e gerenciar o desempenho do banco de dados SQL do Azure multilocatário fragmentado em um aplicativo SaaS multilocatário
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
ms.openlocfilehash: cc8ccbbde56b57af684ad47840002a846bdcd8c0
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73827957"
---
# <a name="monitor-and-manage-performance-of-sharded-multi-tenant-azure-sql-database-in-a-multi-tenant-saas-app"></a>Monitorar e gerenciar o desempenho do banco de dados SQL do Azure multilocatário fragmentado em um aplicativo SaaS multilocatário

Neste tutorial, vários cenários importantes de gerenciamento de desempenho usados em aplicativos SaaS são explorados. Usando um gerador de carga para simular atividade entre bancos de dados multilocatários fragmentados, os recursos internos de monitoramento e alerta do banco de dados SQL são demonstrados.

O aplicativo de banco de dados multilocatário Wingtip tickets usa um modelo de data de multilocatário fragmentado, onde os dados de local (locatário) são distribuídos por ID de locatário em potencialmente vários bancos de dado. Como em muitas aplicações SaaS, o padrão de carga de trabalho do inquilino antecipado é imprevisível e esporádico. Por outras palavras, as vendas de bilhetes podem ocorrer em qualquer altura. Para aproveitar esse padrão de uso de banco de dados típico, os bancos de dados podem ser escalados verticalmente e reduzidos para otimizar o custo de uma solução. Com esse tipo de padrão, é importante monitorar o uso de recursos do banco de dados para garantir que as cargas sejam razoavelmente balanceadas em bancos de dados potencialmente múltiplos. Você também precisa garantir que bancos de dados individuais tenham recursos adequados e não estejam atingindo seus limites de [DTU](sql-database-purchase-models.md#dtu-based-purchasing-model) . Este tutorial explora maneiras de monitorar e gerenciar bancos de dados e como tomar uma ação corretiva em resposta a variações na carga de trabalho.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> 
> * Simular o uso em um banco de dados multilocatário fragmentado executando um gerador de carga fornecido
> * Monitore o banco de dados conforme ele responde ao aumento na carga
> * Escalar verticalmente o banco de dados em resposta à carga de banco de dados aumentada
> * Provisionar um locatário em um banco de dados de locatário único

Para concluir este tutorial, confirme que conclui os pré-requisitos seguintes:

* O aplicativo de banco de dados multilocatário do Wingtip tickets SaaS foi implantado. Para implantar em menos de cinco minutos, consulte [implantar e explorar o aplicativo de banco de dados multilocatário do Wingtip tickets SaaS](saas-multitenantdb-get-started-deploy.md)
* O Azure PowerShell está instalado. Para obter mais detalhes, veja [Introdução ao Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps)

## <a name="introduction-to-saas-performance-management-patterns"></a>Introdução aos padrões de gerenciamento de desempenho de SaaS

Gerir o desempenho da base de dados consiste em compilar e analisar dados de desempenho e, em seguida, reagir a estes dados ajustando os parâmetros para manter um tempo de resposta aceitável para a aplicação. 

### <a name="performance-management-strategies"></a>Estratégias da gestão do desempenho

* Para evitar a monitoração manual do desempenho, é mais eficaz **definir alertas que disparam quando os bancos de dados são perdidos em intervalos normais**.
* Para responder a flutuações de curto prazo no tamanho de computação de um banco de dados, o **nível de DTU pode ser aumentado ou reduzido verticalmente**. Se essa flutuação ocorrer em uma base regular ou previsível, **o dimensionamento do banco de dados poderá ser agendado para ocorrer automaticamente**. Por exemplo, reduza verticalmente quando sabe que a carga de trabalho é leve, talvez durante a noite ou durante os fins de semana.
* Para responder a flutuações de longo prazo ou alterações nos locatários, os **locatários individuais podem ser movidos para outro banco de dados**.
* Para responder a aumentos de curto prazo na carga *individual* de locatários, os **locatários individuais podem ser retirados de um banco de dados e atribuídos a um tamanho de computação individual**. Depois que o carregamento é reduzido, o locatário pode ser retornado para o banco de dados multilocatário. Quando isso é conhecido com antecedência, os locatários podem ser movidos preventivamentemente para garantir que o banco de dados sempre tenha os recursos de que precisa e para evitar o impacto em outros locatários no banco de dados de vários locatários. Se este requisito for previsível, por exemplo, quando um local tem uma grande procura de bilhetes para um evento popular, este comportamento de gestão poderá ser integrado na aplicação.

O [portal do Azure](https://portal.azure.com) fornece monitorização e alertas incorporados na maior parte dos recursos. Para o banco de dados SQL, o monitoramento e o alerta estão disponíveis em bancos de dados. Esse monitoramento e alerta internos são específicos ao recurso, portanto, é conveniente usar para pequenos números de recursos, mas não é conveniente ao trabalhar com muitos recursos.

Para cenários de alto volume, nos quais você está trabalhando com muitos recursos, [Azure monitor logs](https://azure.microsoft.com/services/log-analytics/) podem ser usados. Esse é um serviço do Azure separado que fornece análise sobre telemetria e logs de diagnóstico emitidos coletados em um espaço de trabalho Log Analytics. Os logs de Azure Monitor podem coletar telemetria de vários serviços e serem usados para consultar e definir alertas.

## <a name="get-the-wingtip-tickets-saas-multi-tenant-database-application-source-code-and-scripts"></a>Obtenha o código-fonte e os scripts do aplicativo de banco de dados multilocatário SaaS Wingtip tickets

Os scripts de banco de dados multilocatário do Wingtip tickets SaaS e o código-fonte do aplicativo estão disponíveis no repositório GitHub [repositório wingtipticketssaas-MultitenantDB](https://github.com/microsoft/WingtipTicketsSaaS-MultiTenantDB) . Confira as [diretrizes gerais](saas-tenancy-wingtip-app-guidance-tips.md) para obter as etapas para baixar e desbloquear os scripts SaaS do Wingtip tickets.

## <a name="provision-additional-tenants"></a>Aprovisionar inquilinos adicionais

Para uma boa compreensão de como o monitoramento e gerenciamento de desempenho funciona em escala, este tutorial exige que você tenha vários locatários em um banco de dados multilocatário fragmentado.

Se você já tiver provisionado um lote de locatários em um tutorial anterior, pule para a seção [simular o uso em todos os bancos de dados de locatário](#simulate-usage-on-all-tenant-databases) .

1. No **ISE do PowerShell**, abra...\\módulos de aprendizado\\monitoramento e gerenciamento de desempenho\\*demo-performancemonitoringandmanagement. ps1*. Mantenha este script aberto, uma vez que vai executar vários cenários durante este tutorial.
1. Defina **$DemoScenario** = **1**, _Aprovisionar um lote de inquilinos_
1. Prima **F5** para executar o script.

O script implanta 17 locatários no banco de dados de vários locatários em alguns minutos. 

O script *New-TenantBatch* cria novos locatários com chaves de locatário exclusivas dentro do banco de dados multilocatário fragmentado e os inicializa com o nome do locatário e o tipo de local. Isso é consistente com a maneira como o aplicativo provisiona um novo locatário. 

## <a name="simulate-usage-on-all-tenant-databases"></a>Simular a utilização em todas as bases de dados de inquilinos

O script *demo-performancemonitoringandmanagement. ps1* é fornecido para simular uma carga de trabalho em execução no banco de dados multilocatário. A carga é gerada usando um dos cenários de carga disponíveis:

| Demonstração | Cenário |
|:--|:--|
| 2 | Gerar carga de intensidade normal (aproximadamente 30 DTU) |
| 3 | Gerar carga com picos maiores por locatário|
| 4 | Gerar carga com intermitências de DTU mais altas por locatário (aproximadamente 70 DTU)|
| 5 | Gerar uma alta intensidade (aproximadamente 90 DTU) em um único locatário mais uma carga de intensidade normal em todos os outros locatários |

O gerador de carga aplica uma carga *sintética* só na CPU para cada base de dados do inquilino. O gerador inicia uma tarefa para cada base de dados de inquilino, que chama um procedimento armazenado que periodicamente gera a carga. Os níveis de carga (em DTUs), duração e intervalos são variados em todos os bancos de dados, simulando atividade de locatário imprevisível.

1. No **ISE do PowerShell**, abra...\\módulos de aprendizado\\monitoramento e gerenciamento de desempenho\\*demo-performancemonitoringandmanagement. ps1*. Mantenha este script aberto, uma vez que vai executar vários cenários durante este tutorial.
1. Definir **$DemoScenario** = **2**, _gerar carga de intensidade normal_
1. Pressione **F5** para aplicar uma carga a todos os seus locatários.

O banco de dados multilocatário SaaS Wingtip tickets é um aplicativo SaaS, e a carga do mundo real em um aplicativo SaaS é normalmente esporádica e imprevisível. Para simular isto, o gerador de carga produz uma carga aleatória distribuída por todos os inquilinos. São necessários vários minutos para que o padrão de carga surja, então execute o gerador de carga por 3-5 minutos antes de tentar monitorar a carga nas seções a seguir.

> [!IMPORTANT]
> O gerador de carga está sendo executado como uma série de trabalhos em uma nova janela do PowerShell. Se você fechar a sessão, o gerador de carga será interrompido. O gerador de carga permanece em um estado de *invocação de trabalho* onde ele gera carga em qualquer locatário novo provisionado depois que o gerador é iniciado. Use *Ctrl-C* para parar de invocar novos trabalhos e sair do script. O gerador de carga continuará a ser executado, mas somente em locatários existentes.

## <a name="monitor-resource-usage-using-the-azure-portal"></a>Monitorar o uso de recursos usando o portal do Azure

Para monitorar o uso de recursos que resulta da carga sendo aplicada, abra o portal para o banco de dados multilocatário, **tenants1**, que contém os locatários:

1. Abra o [portal do Azure](https://portal.azure.com) e navegue até o usuário do servidor *tenants1-mt-&lt;&gt;* .
1. Role para baixo e localize bancos de dados e clique em **tenants1**. Esse banco de dados multilocatário fragmentado contém todos os locatários criados até o momento.

![gráfico de banco de dados](./media/saas-multitenantdb-performance-monitoring/multitenantdb.png)

Observe o gráfico de **DTU** .

## <a name="set-performance-alerts-on-the-database"></a>Definir alertas de desempenho no banco de dados

Defina um alerta no banco de dados que dispare na utilização \>de 75% da seguinte maneira:

1. Abra o banco de dados *tenants1* (no servidor de *&gt;de usuário do tenants1-MT-&lt;* ) no [portal do Azure](https://portal.azure.com).
1. Clique em **Regras de Alerta**e, em seguida, clique em **+ Adicionar alerta**:

   ![adicionar alerta](media/saas-multitenantdb-performance-monitoring/add-alert.png)

1. Forneça um nome, tal como **DTU elevada**,
1. Defina os seguintes valores:
   * **Métrica = percentual de DTU**
   * **Condição = maior que**
   * **Limiar = 75**.
   * **Período = nos últimos 30 minutos**
1. Adicione um endereço de email à caixa *email (s) de administrador adicional* e clique em **OK**.

   ![alerta de definição](media/saas-multitenantdb-performance-monitoring/set-alert.png)

## <a name="scale-up-a-busy-database"></a>Escalar verticalmente um banco de dados ocupado

Se o nível de carga aumentar em um banco de dados para o ponto em que maximizar o banco de dados e atingir o uso de 100% de DTU, o desempenho do banco de dados será afetado, o que pode retardar os tempos de resposta de consulta.

A **curto prazo**, considere escalar verticalmente o banco de dados para fornecer recursos adicionais ou remover locatários do banco de dados multilocatário (movendo-os para fora do banco de dados de vários locatários para um banco de dados autônomo).

**Termo mais longo**, considere otimizar as consultas ou o uso do índice para melhorar o desempenho do banco de dados. Dependendo da sensibilidade do aplicativo a problemas de desempenho, sua prática recomendada é dimensionar um banco de dados antes de atingir 100% de uso de DTU. Utilize um alerta para avisá-lo antecipadamente.

Você pode simular um banco de dados ocupado aumentando a carga produzida pelo gerador. Fazendo com que os locatários sejam rompidos com mais frequência e por mais tempo, aumentando a carga no banco de dados multilocatário sem alterar os requisitos dos locatários individuais. Escalar verticalmente o banco de dados é facilmente feito no portal ou no PowerShell. Este exercício utiliza o portal.

1. Defina *$DemoScenario* = **3**, _gerar carga com intermitências mais longas e mais frequentes por banco de dados_ para aumentar a intensidade da carga agregada no banco de dados sem alterar a carga de pico exigida por cada locatário.
1. Prima **F5** para aplicar uma carga a todas as bases de dados de inquilinos.
1. Vá para o banco de dados **tenants1** no portal do Azure.

Monitore o maior uso de DTU do banco de dados no gráfico superior. Leva alguns minutos para que a nova carga mais alta seja iniciada, mas você deve ver rapidamente o banco de dados começar a atingir a utilização máxima e, à medida que a carga se estabilizar no novo padrão, ela sobrecarrega rapidamente o banco de dados.

1. Para escalar verticalmente o banco de dados, clique em **tipo de preço (dimensionar DTUs)** na folha configurações.
1. Ajuste a configuração de **DTU** para **100**. 
1. Clique em **aplicar** para enviar a solicitação para dimensionar o banco de dados.

Volte para **tenants1** > **visão geral** para exibir os gráficos de monitoramento. Monitore o efeito de fornecer ao banco de dados mais recursos (embora com poucos locatários e uma carga aleatória não seja sempre fácil de ver de forma conclusiva até que você execute por algum tempo). Enquanto você está olhando para os gráficos, lembre-se de que 100% no gráfico superior agora representa 100 DTUs, enquanto no gráfico inferior 100% ainda é de 50 DTUs.

As bases de dados permanecem online e estão totalmente disponíveis durante o processo. O código do aplicativo sempre deve ser escrito para tentar descartar conexões novamente e, portanto, se reconectará ao banco de dados.

## <a name="provision-a-new-tenant-in-its-own-database"></a>Provisionar um novo locatário em seu próprio banco de dados 

O modelo multilocatário fragmentado permite que você escolha se deseja provisionar um novo locatário em um banco de dados multilocatário junto com outros locatários ou provisionar o locatário em um banco de dados próprio. Ao provisionar um locatário em seu próprio banco de dados, ele se beneficia do isolamento inerente ao banco de dados separado, permitindo que você gerencie o desempenho desse locatário independentemente de outros, restaure esse locatário independentemente de outros, etc. Por exemplo, você pode optar por colocar clientes regulares ou de avaliação gratuita em um banco de dados multilocatário e clientes Premium em bancos de dados individuais.  Se bancos de dados de locatário único isolados forem criados, eles ainda poderão ser gerenciados coletivamente em um pool elástico para otimizar os custos de recursos.

Se você já provisionou um novo locatário em seu próprio banco de dados, pule as próximas etapas.

1. No **ISE do PowerShell**, abra...\\módulos de aprendizado\\ProvisionTenants\\*provisiontenants. ps1*. 
1. Modify **$TenantName = "Salix salsa"** e **$VenueType = "dança"**
1. Definir **$Scenario** = **2**, _provisionar um locatário em um novo banco de dados de locatário único_
1. Prima **F5** para executar o script.

O script provisionará esse locatário em um banco de dados separado, registrará o banco de dados e o locatário com o catálogo e, em seguida, abrirá a página de eventos do locatário no navegador. Atualize a página Hub de eventos e você verá que "Salix salsa" foi adicionado como um local.

## <a name="manage-performance-of-an-individual-database"></a>Gerenciar o desempenho de um banco de dados individual

Se um único locatário dentro de um banco de dados multilocatário apresentar uma alta carga prolongada, pode ser que ele tenha a tendência de dominar os recursos do banco de dados e afetar outros locatários no mesmo banco de dados. Se for provável que a atividade continue por algum tempo, o locatário poderá ser temporariamente removido do banco de dados e em seu próprio banco de dados de locatário único. Isso permite que o locatário tenha os recursos adicionais de que precisa e o isola totalmente dos outros locatários.

Este exercício simula o efeito de Salix salsa enfrentando uma alta carga quando os tíquetes passam pela venda para um evento popular.

1. Abra o script...\\*demo-performancemonitoringandmanagement. ps1* .
1. Defina **$DemoScenario = 5**, _gerar uma carga normal mais uma carga alta em um único locatário (aproximadamente 90 DTU)._
1. Definir **$SingleTenantName = Salix salsa**
1. Execute o script com **F5**.

Vá para o portal e navegue até **salixsalsa** > **visão geral** para exibir os gráficos de monitoramento. 

## <a name="other-performance-management-patterns"></a>Outros padrões de gerenciamento de desempenho

**Dimensionamento de autoatendimento de locatário**

Como o dimensionamento é uma tarefa facilmente chamada por meio da API de gerenciamento, você pode facilmente criar a capacidade de dimensionar bancos de dados de locatário em seu aplicativo voltado para o locatário e oferecê-lo como um recurso do seu serviço SaaS. Por exemplo, permita que os inquilinos administrem automaticamente o aumento ou redução vertical, talvez ligando-os diretamente à faturação deles!

**Dimensionamento de um banco de dados para cima e para baixo em um agendamento para corresponder aos padrões de uso**

Quando o uso agregado de locatário segue padrões de uso previsíveis, você pode usar a automação do Azure para dimensionar um banco de dados para cima e para baixo em um agendamento. Por exemplo, dimensione um banco de dados para baixo depois de 18:00 e para cima novamente antes de 6h em dias da semana quando você sabe que há uma queda nos requisitos de recursos.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Simular o uso em um banco de dados multilocatário fragmentado executando um gerador de carga fornecido
> * Monitore o banco de dados conforme ele responde ao aumento na carga
> * Escalar verticalmente o banco de dados em resposta à carga de banco de dados aumentada
> * Provisionar um locatário em um banco de dados de locatário único

## <a name="additional-resources"></a>Recursos adicionais

<!--* [Additional tutorials that build upon the Wingtip Tickets SaaS Multi-tenant Database application deployment](saas-multitenantdb-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)-->
* [Automatização do Azure](../automation/automation-intro.md)