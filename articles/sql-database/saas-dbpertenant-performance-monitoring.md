---
title: 'Aplicativo SaaS: monitorar o desempenho de vários bancos de dados'
description: Monitorar e gerenciar o desempenho de pools e bancos de dados SQL do Azure em um aplicativo SaaS multilocatário
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
ms.openlocfilehash: e2e752ec37f71ea501dcee586e7daf0fc950919d
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73822240"
---
# <a name="monitor-and-manage-performance-of-azure-sql-databases-and-pools-in-a-multi-tenant-saas-app"></a>Monitorar e gerenciar o desempenho de pools e bancos de dados SQL do Azure em um aplicativo SaaS multilocatário

Neste tutorial, vários cenários importantes de gerenciamento de desempenho usados em aplicativos SaaS são explorados. Usando um gerador de carga para simular atividades em todos os bancos de dados de locatário, são demonstrados os recursos internos de monitoramento e alerta dos pools elásticos e do banco de dados SQL.

O aplicativo Wingtip tickets SaaS Database per locatário usa um modelo de dados de locatário único, onde cada local (locatário) tem seu próprio banco de dado. Como em muitas aplicações SaaS, o padrão de carga de trabalho do inquilino antecipado é imprevisível e esporádico. Por outras palavras, as vendas de bilhetes podem ocorrer em qualquer altura. Para aproveitar esse padrão de uso de banco de dados típico, os bancos de dados de locatário são implantados em pools elásticos. Os conjuntos elásticos otimizam o custo de uma solução ao partilhar recursos em muitas bases de dados. Com este tipo de padrão, é importante monitorizar a utilização das bases de dados e dos recursos dos conjuntos para confirmar que as cargas estão razoavelmente equilibradas entre os conjuntos. Também tem de garantir que as bases de dados individuais têm os recursos adequados e que os conjuntos não estão a atingir os seus limites [eDTU](sql-database-purchase-models.md#dtu-based-purchasing-model). Este tutorial analisa formas de monitorizar e gerir bases de dados e conjuntos e como tomar uma medida corretiva em resposta a variações na carga de trabalho.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> 
> * Simular a utilização nas bases de dados de inquilinos ao executar um gerador de carga fornecido
> * Monitorizar as bases de dados de inquilinos à medida que a carga aumenta
> * Aumentar verticalmente o Conjunto elástico em resposta ao aumento de carga da base de dados
> * Aprovisionar um segundo Conjunto elástico para balancear a carga da atividade da base de dados


Para concluir este tutorial, confirme que conclui os pré-requisitos seguintes:

* O aplicativo de banco de dados por locatário SaaS Wingtip tickets foi implantado. Para implantar em menos de cinco minutos, consulte [implantar e explorar o aplicativo de banco de dados por locatário SaaS Wingtip tickets](saas-dbpertenant-get-started-deploy.md)
* O Azure PowerShell está instalado. Para obter mais detalhes, veja [Introdução ao Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps)

## <a name="introduction-to-saas-performance-management-patterns"></a>Introdução aos padrões de gerenciamento de desempenho de SaaS

Gerir o desempenho da base de dados consiste em compilar e analisar dados de desempenho e, em seguida, reagir a estes dados ajustando os parâmetros para manter um tempo de resposta aceitável para a aplicação. Ao hospedar vários locatários, os pools elásticos são uma maneira econômica de fornecer e gerenciar recursos para um grupo de bancos de dados com cargas de trabalho imprevisíveis. Com determinados padrões de carga de trabalho, um mínimo de duas bases de dados S3 pode beneficiar ao serem geridas num conjunto.

![diagrama de aplicativo](./media/saas-dbpertenant-performance-monitoring/app-diagram.png)

Os pools e os bancos de dados em pools devem ser monitorados para garantir que permaneçam dentro de intervalos de desempenho aceitáveis. Ajuste a configuração do pool para atender às necessidades da carga de trabalho agregada de todos os bancos de dados, garantindo que os eDTUs do pool sejam apropriados para a carga de trabalho geral. Ajuste os valores eDTU mínimo e máximo por cada base de dados para adequar os valores aos requisitos da aplicação específica.

### <a name="performance-management-strategies"></a>Estratégias da gestão do desempenho

* Para evitar a monitoração manual do desempenho, é mais eficaz **definir alertas que disparam quando bancos de dados ou pools são isolados de intervalos normais**.
* Para responder a flutuações de curto prazo no tamanho de computação agregado de um pool, o **nível de eDTU do pool pode ser aumentado ou reduzido verticalmente**. Se esta flutuação ocorrer numa base regular ou previsível, **o dimensionamento do conjunto poderá ser agendado para ocorrer automaticamente**. Por exemplo, reduza verticalmente quando sabe que a carga de trabalho é leve, talvez durante a noite ou durante os fins de semana.
* Para responder a flutuações de duração mais longa ou alterações no número de bases de dados, **as bases de dados individuais podem ser movidas para outros grupos**.
* Para responder a aumentos de curto prazo no banco de dados *individual* **, carregue bancos de dado individuais podem ser retirados de um pool e atribuído um tamanho de computação individual**. Assim que a carga for reduzida, a base de dados pode ser devolvida ao conjunto. Quando isso é conhecido com antecedência, os bancos de dados podem ser movidos preventivamentemente para garantir que o banco de dados sempre tenha os recursos de que precisa e para evitar o impacto em outros bancos de dados no pool. Se este requisito for previsível, por exemplo, quando um local tem uma grande procura de bilhetes para um evento popular, este comportamento de gestão poderá ser integrado na aplicação.

O [portal do Azure](https://portal.azure.com) fornece monitorização e alertas incorporados na maior parte dos recursos. Na Base de Dados SQL, a monitorização e os alertas estão disponíveis nas bases de dados e nos conjuntos. Esse monitoramento e alerta internos são específicos ao recurso, portanto, é conveniente usar para pequenos números de recursos, mas não é muito conveniente ao trabalhar com muitos recursos.

Para cenários de alto volume, nos quais você está trabalhando com muitos recursos, [Azure monitor logs](saas-dbpertenant-log-analytics.md) podem ser usados. Esse é um serviço do Azure separado que fornece análise sobre telemetria e logs de diagnóstico emitidos coletados em um espaço de trabalho Log Analytics. Os logs de Azure Monitor podem coletar telemetria de vários serviços e serem usados para consultar e definir alertas.

## <a name="get-the-wingtip-tickets-saas-database-per-tenant-application-scripts"></a>Obter os scripts de aplicativo de banco de dados por locatário SaaS Wingtip tickets

Os scripts de banco de dados multilocatário do Wingtip tickets SaaS e o código-fonte do aplicativo estão disponíveis no repositório GitHub [repositório wingtipticketssaas-DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) . Confira as [diretrizes gerais](saas-tenancy-wingtip-app-guidance-tips.md) para obter as etapas para baixar e desbloquear os scripts SaaS do Wingtip tickets.

## <a name="provision-additional-tenants"></a>Aprovisionar inquilinos adicionais

Apesar de os conjuntos poderem ser económicos com apenas duas bases de dados S3, quantas mais bases de dados estiverem no conjunto mais económico será o efeito em média. Para uma boa compreensão de como funciona a monitorização e a gestão do desempenho à escala, este tutorial requer que tenha, pelo menos, 20 bases de dados implementadas.

Se você já provisionou um lote de locatários em um tutorial anterior, pule para a seção [simular o uso em todos os bancos de dados de locatário](#simulate-usage-on-all-tenant-databases) .

1. No **ISE do PowerShell**, abra...\\módulos de aprendizado\\monitoramento e gerenciamento de desempenho\\*demo-performancemonitoringandmanagement. ps1*. Mantenha este script aberto, uma vez que vai executar vários cenários durante este tutorial.
1. Defina **$DemoScenario** = **1**, **Aprovisionar um lote de inquilinos**
1. Prima **F5** para executar o script.

O script implementará 17 inquilinos em menos de cinco minutos.

O script *New-TenantBatch* usa um conjunto aninhado ou vinculado de modelos do [Resource Manager](../azure-resource-manager/index.yml) que criam um lote de locatários, que, por padrão, copia o banco de dados **basetenantdb** no servidor de catálogo para criar os novos bancos e dados de locatário e, em seguida, registra-os no catálogo e, por fim, inicializa-os com o nome do locatário e o tipo de local. Isso é consistente com a maneira como o aplicativo provisiona um novo locatário. As alterações feitas em *basetenantdb* são aplicadas a todos os novos locatários provisionados posteriormente. Confira o [tutorial de gerenciamento de esquema](saas-tenancy-schema-management.md) para ver como fazer alterações de esquema em bancos de dados de locatário *existentes* (incluindo o *basetenantdb* Database).

## <a name="simulate-usage-on-all-tenant-databases"></a>Simular a utilização em todas as bases de dados de inquilinos

O script *demo-performancemonitoringandmanagement. ps1* é fornecido para simular uma carga de trabalho em execução em todos os bancos de dados de locatário. A carga é gerada usando um dos cenários de carga disponíveis:

| Demonstração | Cenário |
|:--|:--|
| 2 | Gerar carga de intensidade normal (aproximadamente 40 DTU) |
| 3 | Gerar carga com picos mais demorados e mais frequentes por base de dados|
| 4 | Gerar carga com intermitências de DTU mais altas por banco de dados (aproximadamente 80 DTU)|
| 5 | Gerar uma carga normal mais uma carga alta em um único locatário (aproximadamente 95 DTU)|
| 6 | Gerar carga desequilibrada em vários conjuntos|

O gerador de carga aplica uma carga *sintética* só na CPU para cada base de dados do inquilino. O gerador inicia uma tarefa para cada base de dados de inquilino, que chama um procedimento armazenado que periodicamente gera a carga. Os níveis de carga (em eDTUs), a duração e os intervalos são diversificados em todas as bases de dados, o que simula a atividade imprevisível do inquilino.

1. No **ISE do PowerShell**, abra...\\módulos de aprendizado\\monitoramento e gerenciamento de desempenho\\*demo-performancemonitoringandmanagement. ps1*. Mantenha este script aberto, uma vez que vai executar vários cenários durante este tutorial.
1. Defina **$DemoScenario** = **2**, *gerar carga de intensidade normal*.
1. Prima **F5** para aplicar uma carga a todas as bases de dados de inquilinos.

O banco de dados por locatário SaaS Wingtip tickets é um aplicativo SaaS, e a carga real em um aplicativo SaaS normalmente é esporádica e imprevisível. Para simular isto, o gerador de carga produz uma carga aleatória distribuída por todos os inquilinos. São necessários vários minutos para que o padrão de carga surja, então execute o gerador de carga por 3-5 minutos antes de tentar monitorar a carga nas seções a seguir.

> [!IMPORTANT]
> O gerador de carga está em execução como uma série de tarefas na sessão local do PowerShell. Mantenha aberto o separador *Demo-PerformanceMonitoringAndManagement.ps1*! Se fechar o separador ou suspender o computador, o gerador de carga será interrompido. O gerador de carga permanece em um estado de *invocação de trabalho* onde ele gera carga em qualquer locatário novo provisionado depois que o gerador é iniciado. Use *Ctrl-C* para parar de invocar novos trabalhos e sair do script. O gerador de carga continuará a ser executado, mas somente em locatários existentes.

## <a name="monitor-resource-usage-using-the-azure-portal"></a>Monitorar o uso de recursos usando o portal do Azure

Para monitorar o uso de recursos que resulta da carga sendo aplicada, abra o portal para o pool que contém os bancos de dados de locatário:

1. Abra o [portal do Azure](https://portal.azure.com) e navegue até o servidor de *&gt;de usuário tenants1-DPT-&lt;* .
1. Desloque-se para baixo, localize os conjuntos elásticos e clique em **Pool1**. Este conjunto contém todas as bases de dados de inquilinos criadas até à data.

Observe os gráficos **monitoramento de pool elástico** e **monitoramento de banco de dados elástico** .

A utilização de recursos do pool é a utilização agregada do banco de dados para todos os bancos de dados no pool. O gráfico de banco de dados mostra os cinco bancos mais interessantes:

![gráfico de banco de dados](./media/saas-dbpertenant-performance-monitoring/pool1.png)

Como há bancos de dados adicionais no pool além dos cinco principais, a utilização do pool mostra a atividade que não é refletida no gráfico dos cinco bancos de dados principais. Para obter mais detalhes, clique em **utilização de recursos do banco de dados**:

![utilização de recursos do banco de dados](./media/saas-dbpertenant-performance-monitoring/database-utilization.png)


## <a name="set-performance-alerts-on-the-pool"></a>Definir alertas de desempenho no conjunto

Defina um alerta no pool que dispare na utilização \>de 75% da seguinte maneira:

1. Abra *Pool1* (no servidor *tenants1-dpt-\<\>* Server) no [portal do Azure](https://portal.azure.com).
1. Clique em **Regras de Alerta**e, em seguida, clique em **+ Adicionar alerta**:

   ![adicionar alerta](media/saas-dbpertenant-performance-monitoring/add-alert.png)

1. Forneça um nome, tal como **DTU elevada**,
1. Defina os seguintes valores:
   * **Métrica = percentagem de eDTU**
   * **Condição = maior que**
   * **Limite = 75**
   * **Período = nos últimos 30 minutos**
1. Adicione um endereço de email à caixa *email (s) de administrador adicional* e clique em **OK**.

   ![alerta de definição](media/saas-dbpertenant-performance-monitoring/alert-rule.png)


## <a name="scale-up-a-busy-pool"></a>Aumentar verticalmente um conjunto ocupado

Se o nível de carga agregado aumentar num conjunto até ao ponto máximo do conjunto e atingir a utilização de 100% de eDTU, o desempenho da base de dados individual será afetado, o que potencialmente abranda os tempos de resposta de consulta de todas as bases de dados no conjunto.

A **curto prazo**, considere escalar verticalmente o pool para fornecer recursos adicionais ou Remover bancos de dados do pool (movê-los para outros pools ou para fora do pool para uma camada de serviço autônoma).

**Termo mais longo**, considere otimizar as consultas ou o uso do índice para melhorar o desempenho do banco de dados. Dependendo da sensibilidade da aplicação relativamente a problemas de desempenho, a melhor prática para aumentar verticalmente um conjunto antes de atingir os 100% de utilização de eDTU. Utilize um alerta para avisá-lo antecipadamente.

Pode simular um conjunto ocupado ao aumentar a carga produzida pelo gerador. Fazendo com que os bancos de dados sejam intermitentes com mais frequência e por mais tempo, aumentando a carga agregada no pool sem alterar os requisitos dos bancos de dados individuais. O aumento vertical do conjunto é feito facilmente no portal ou a partir do PowerShell. Este exercício utiliza o portal.

1. Defina *$DemoScenario* = **3**, _Gerar carga com picos mais demorados e mais frequentes por base de dados_ para aumentar a intensidade da carga agregada no conjunto sem alterar a carga de pico necessária para cada base de dados.
1. Prima **F5** para aplicar uma carga a todas as bases de dados de inquilinos.

1. Acesse **Pool1** na portal do Azure.

Monitore o maior uso de eDTU do pool no gráfico superior. Leva alguns minutos para que a nova carga mais alta seja iniciada, mas você deve ver rapidamente o pool começar a atingir a utilização máxima e, à medida que a carga se estabilizar no novo padrão, ela sobrecarrega rapidamente o pool.

1. Para escalar verticalmente o pool, clique em **Configurar pool** na parte superior da página **Pool1** .
1. Ajuste a configuração de **eDTU do pool** para **100**. Alterar a eDTU do conjunto não altera as definições por base de dados (que é ainda o máximo de 50 eDTU por base de dados). Você pode ver as configurações por banco de dados no lado direito da página **Configurar pool** .
1. Clique em **salvar** para enviar a solicitação para dimensionar o pool.

Volte para **Pool1** > **visão geral** para exibir os gráficos de monitoramento. Monitore o efeito de fornecer o pool com mais recursos (embora com poucos bancos de dados e uma carga aleatória não seja sempre fácil de ver de forma conclusiva até que você execute por algum tempo). Enquanto estiver a visualizar os gráficos tenha em atenção que 100% no gráfico superior representa agora 100 eDTUs, enquanto no gráfico inferior 100% é ainda 50 eDTUs, uma vez que o máximo por base de dados ainda está em 50 eDTUs.

As bases de dados permanecem online e estão totalmente disponíveis durante o processo. No último momento, à medida que cada base de dados fica pronta para ser ativada com a nova eDTU de conjunto, todas as ligações ativas são perdidas. O código da aplicação deve ser sempre escrito para repetir as ligações que caem e, por conseguinte, voltar a ligar à base de dados no conjunto aumentado verticalmente.

## <a name="load-balance-between-pools"></a>Balanceamento de carga entre pools

Como alternativa ao aumento vertical do conjunto, crie um segundo conjunto e mova as bases de dados para o conjunto para balancear a carga entre os dois conjuntos. Para fazê-lo, o novo conjunto tem de ser criado no mesmo servidor do primeiro conjunto.

1. Na [portal do Azure](https://portal.azure.com), abra o servidor de **&gt;de usuário tenants1-DPT-&lt;** .
1. Clique em **+ novo pool** para criar um pool no servidor atual.
1. No modelo de **pool elástico** :

   1. Defina o **nome** como *Pool2*.
   1. Deixe o escalão de preço como **Conjunto Padrão**.
   1. Clique em **Configurar conjunto**.
   1. Defina **edtu do pool** como *50 eDTU*.
   1. Clique em **Adicionar bancos de dados** para ver uma lista de bancos de dados no servidor que podem ser adicionados ao *Pool2*.
   1. Selecione qualquer 10 bancos de dados para movê-los para o novo pool e, em seguida, clique em **selecionar**. Se você estiver executando o gerador de carga, o serviço já sabe que seu perfil de desempenho requer um pool maior do que o tamanho de eDTU 50 padrão e recomenda começar com uma configuração de eDTU de 100.

      ![Recomendação](media/saas-dbpertenant-performance-monitoring/configure-pool.png)

   1. Para este tutorial, deixe o padrão em 50 eDTUs e clique em **selecionar** novamente.
   1. Selecione **OK** para criar o novo pool e mover os bancos de dados selecionados para ele.

A criação do pool e a movimentação dos bancos de dados leva alguns minutos. À medida que os bancos de dados são movidos, eles permanecem online e totalmente acessíveis até o último momento, ponto em que todas as conexões abertas são fechadas. Contanto que você tenha uma lógica de repetição, os clientes se conectarão ao banco de dados no novo pool.

Navegue até **Pool2** (em *tenants1-DPT-\<* servidor de\>de usuário) para abrir o pool e monitorar seu desempenho. Se você não o vir, aguarde até que o provisionamento do novo pool seja concluído.

Agora você vê que o uso de recursos em *Pool1* foi descartado e que o *Pool2* agora é carregado de forma semelhante.

## <a name="manage-performance-of-an-individual-database"></a>Gerenciar o desempenho de um banco de dados individual

Se um banco de dados individual em um pool sofrer uma alta carga sustentada, dependendo da configuração do pool, ele pode tendem a dominar os recursos no pool e afetar outros bancos de dados. Se for provável que a atividade continue por algum tempo, o banco de dados poderá ser temporariamente movido para fora do pool. Isso permite que o banco de dados tenha os recursos adicionais de que precisa e o isola dos outros.

Este exercício simula o efeito de uma carga elevada em Contoso Concert Hall, quando são colocados à venda os bilhetes para um concerto popular.

1. No **ISE do PowerShell**, abra o script...\\*demo-performancemonitoringandmanagement. ps1* .
1. Defina **$DemoScenario = 5, gerar uma carga normal mais uma carga alta em um único locatário (aproximadamente 95 DTU).**
1. Defina **$SingleTenantDatabaseName = contosoconcerthall**
1. Execute o script com **F5**.


1. Na [portal do Azure](https://portal.azure.com), navegue até a lista de bancos de dados no servidor de *\>de\<usuário do tenants1-DPT* . 
1. Clique no banco de dados **contosoconcerthall** .
1. Clique no pool em que **contosoconcerthall** está. Localize o pool na seção **pool elástico** .

1. Inspecione o gráfico de **monitoramento do pool elástico** e procure o maior uso de eDTU do pool. Depois de um minuto ou dois, o aumento de carga deve começar a surgir e deve poder ver rapidamente que o conjunto atinge os 100% de utilização.
2. Inspecione a exibição de **monitoramento do banco de dados elástico** , que mostra os bancos mais interessantes na última hora. Em breve, o banco de dados *contosoconcerthall* deve aparecer como um dos cinco bancos de dados mais interessantes.
3. **Clique no gráfico de monitoramento de banco de dados elástico** e ele abrirá a página de **utilização de recursos do banco de dados** , na qual você pode monitorar qualquer um dos bancos. Isso permite isolar a exibição do banco de dados *contosoconcerthall* .
4. Na lista de bancos de dados, clique em **contosoconcerthall**.
5. Clique em **tipo de preço (dimensionar DTUs)** para abrir a página **Configurar desempenho** , na qual você pode definir um tamanho de computação autônomo para o banco de dados.
6. Clique no separador **Padrão** para abrir as opções de dimensionamento na camada Padrão.
7. Deslize o **controle deslizante de DTU** para a direita para selecionar **100** DTUs. Observe que isso corresponde ao objetivo do serviço, **S3**.
8. Clique em **aplicar** para mover o banco de dados para fora do pool e torná-lo um banco de dados *Standard S3* .
9. Quando o dimensionamento for concluído, monitore o efeito no banco de dados contosoconcerthall e Pool1 no pool elástico e nas folhas de banco de dados.

Após a alta carga no banco de dados contosoconcerthall, você deve retorná-lo imediatamente para o pool a fim de reduzir seu custo. Se não estiver claro quando isso acontecerá, você poderá definir um alerta no banco de dados que será disparado quando seu uso de DTU cair abaixo do máximo por banco de dados no pool. Veja o exercício 5, para saber como mover uma base de dados para um conjunto.

## <a name="other-performance-management-patterns"></a>Outros padrões de gerenciamento de desempenho

**Dimensionamento preventivo** No exercício acima, onde você explorou como dimensionar um banco de dados isolado, você sabia qual banco de dados procurar. Se o gerenciamento da Contoso Concert Hall tiver informado wingtips da venda de tíquete iminente, o banco de dados poderia ter sido movido para fora do pool preventivamente. Caso contrário, seria provavelmente necessário um alerta no conjunto ou na base de dados para detetar o que estava a acontecer. Não gostaria de ter conhecimento desta situação através dos outros inquilinos no conjunto a reclamarem de uma degradação do desempenho. E se o inquilino pode prever por quanto tempo serão necessários recursos adicionais, pode configurar um runbook de Automatização do Azure para mover a base de dados para fora do conjunto e, em seguida, fazê-la voltar numa agenda definida.

**Dimensionamento de self-service do inquilino** – Porque o dimensionamento é uma tarefa chamada facilmente através de API de gestão, pode criar facilmente a capacidade de dimensionar bases de dados de inquilino na sua aplicação direcionada para o inquilino e oferecê-la como uma funcionalidade do serviço SaaS. Por exemplo, permita que os inquilinos administrem automaticamente o aumento ou redução vertical, talvez ligando-os diretamente à faturação deles!

**Dimensionamento de um pool para cima e para baixo em um agendamento para corresponder aos padrões de uso**

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

* [Tutoriais adicionais que se baseiam na implantação do aplicativo de banco de dados por locatário SaaS Wingtip tickets](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* [Conjuntos elásticos SQL](sql-database-elastic-pool.md)
* [Automatização do Azure](../automation/automation-intro.md)
* [Azure monitor logs](saas-dbpertenant-log-analytics.md) -Configurando e usando o tutorial de logs de Azure monitor
