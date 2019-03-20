---
title: Aprovisionamento no SaaS multi-inquilino do Azure | Documentos da Microsoft
description: Saiba como aprovisionar e catalogar novos inquilinos numa aplicação de SaaS de multi-inquilino de base de dados do Azure SQL
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MightyPen
ms.author: genemi
ms.reviewer: billgib,andrela,stein
manager: craigg
ms.date: 09/24/2018
ms.openlocfilehash: d29baaad6090cea5eb31f5f50bba444cb3771155
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57835786"
---
# <a name="provision-and-catalog-new-tenants-in-a-saas-application-using-a-sharded-multi-tenant-azure-sql-database"></a>Aprovisionar e catalogar novos inquilinos numa aplicação SaaS com uma base de dados de SQL do Azure em partição horizontal do multi-inquilino

Este artigo aborda o aprovisionamento e catalogação de novos inquilinos, num *base de dados em partição horizontal do multi-inquilino* model ou padrão.

Este artigo tem duas partes principais:

- [Discussão conceitual](#goto_2_conceptual) do aprovisionamento e catalogação de novos inquilinos.

- [Tutorial](#goto_1_tutorial) que destaca o código de script do PowerShell que realiza o aprovisionamento e catalogação.
  - O tutorial utiliza a aplicação Wingtip Tickets SaaS, adaptada para o padrão de base de dados em partição horizontal do multi-inquilino.

<a name="goto_2_conceptual"/>

## <a name="database-pattern"></a>Padrão de base de dados

Nesta secção, além de alguns mais que siga, discutir os conceitos do padrão de base de dados em partição horizontal do multi-inquilino.

Nesse modelo em partição horizontal de multi-inquilino, os esquemas de tabela dentro de cada base de dados incluem uma chave de inquilino na chave primária de tabelas que armazenam dados de inquilino. A chave de inquilino permite que cada base de dados individual armazenar a 0, 1 ou muitos inquilinos. A utilização de bases de dados em partição horizontal torna mais fácil para o sistema de aplicação suportar um grande número de inquilinos. Todos os dados para qualquer inquilino são armazenados numa base de dados. O grande número de inquilinos é distribuído por muitas bases de dados em partição horizontal. Uma base de dados do catálogo armazena o mapeamento de cada inquilino para a respetiva base de dados.

#### <a name="isolation-versus-lower-cost"></a>Isolamento versus o custo mais baixo

Um inquilino com uma base de dados tudo para si próprio usufrui dos benefícios de isolamento. O inquilino pode ter a base de dados restaurada para uma data anterior sem serem restringidos por impacto nos outros inquilinos. Desempenho de base de dados pode ser ajustado para otimizar para um inquilino, novamente, sem ter de comprometer com outros inquilinos. O problema é que o isolamento custa mais do que custa compartilhar um banco de dados com outros inquilinos.

Quando um novo inquilino é aprovisionado, pode compartilhar um banco de dados com outros inquilinos ou podem ser colocada em seu próprio banco de dados nova. Mais tarde pode mudar de ideias e mover a base de dados para a outra situação.

Bases de dados com vários inquilinos e inquilinos únicos são misto no mesmo aplicativo de SaaS, para otimizar o custo ou isolamento para cada inquilino.

   ![Aplicação de base de dados de multi-inquilino em partição horizontal com o catálogo de inquilino](media/saas-multitenantdb-provision-and-catalog/MultiTenantCatalog.png)

## <a name="tenant-catalog-pattern"></a>Padrão de catalogação do inquilino

Quando tiver duas ou mais bases de dados que cada contém, pelo menos, um inquilino, a aplicação tem de ter uma maneira de descobrir qual banco de dados armazena o inquilino de interesse atual. Uma base de dados do catálogo armazena esse mapeamento.

#### <a name="tenant-key"></a>chave de inquilino

Para cada inquilino, a aplicação Wingtip pode derivar uma chave exclusiva, que é a chave de inquilino. A aplicação extrai o nome do inquilino do URL da página da Web. A aplicação codifica o nome para obter a chave. A aplicação utiliza a chave para aceder ao catálogo. O catálogo cross-references informações sobre a base de dados na qual o inquilino está armazenado. A aplicação utiliza as informações de base de dados para ligar. Também podem ser utilizadas a outros esquemas de chave de inquilino.

Usando um catálogo permite que o nome ou localização de uma base de dados do inquilino ser alterada após o aprovisionamento sem interromper o aplicativo. Num modelo de base de dados multi-inquilino, o catálogo acomoda a movimentação de um inquilino entre bases de dados.

#### <a name="tenant-metadata-beyond-location"></a>Metadados do inquilino para além da localização

O catálogo também pode indicar se um inquilino está offline para manutenção ou de outras ações. E o catálogo pode ser estendido para armazenar o inquilino adicional ou metadados de base de dados, como os seguintes itens:
- O escalão de serviço ou a edição de uma base de dados.
- A versão do esquema da base de dados.
- O nome de inquilino e o SLA (contrato de nível de serviço).
- Informações para ativar a gestão de aplicações, suporte ao cliente ou processos de devops.  

O catálogo também pode ser utilizado para ativar a gestão de esquemas de geração de relatórios, entre inquilinos e dados extrair para fins de análise. 

### <a name="elastic-database-client-library"></a>Biblioteca de Clientes da Base de Dados Elástica 

A Wingtip, o catálogo é implementado no *tenantcatalog* base de dados. O *tenantcatalog* é criado usando os recursos de gerenciamento de partições horizontais da [biblioteca de cliente de base de dados elástica (EDCL)](sql-database-elastic-database-client-library.md). A biblioteca permite que uma aplicação criar, gerir e utilizar um *mapa de partições horizontais* que é armazenado numa base de dados. Um mapa de partições horizontais cross-references a chave de inquilino com a partição horizontal, que significa que seu banco de dados em partição horizontal.

Durante o aprovisionamento do inquilino, funções EDCL podem ser utilizadas de aplicações ou scripts do PowerShell para criar as entradas no mapa de partições horizontais. Mais tarde, as funções EDCL podem ser utilizadas para ligar à base de dados correto. A EDCL armazena em cache as informações de ligação para minimizar o tráfego na base de dados do catálogo e acelerar o processo de conexão.

> [!IMPORTANT]
> Fazer *não* editar os dados na base de dados do catálogo através do acesso direto! Atualizações diretas não são suportadas devido ao elevado risco de danos nos dados. Em vez disso, edite os dados de mapeamento através de APIs de EDCL apenas.

## <a name="tenant-provisioning-pattern"></a>Padrão de aprovisionamento do inquilino

#### <a name="checklist"></a>Lista de verificação

Quando pretender aprovisionar um novo inquilino numa base de dados partilhada, da base de dados partilhado tem de pedir as seguintes perguntas:
- Isso tem espaço suficiente para o novo inquilino?
- Isso tem tabelas com os dados de referência necessários para o novo inquilino, ou se os dados podem ser adicionados?
- Tem a variação adequada do esquema de base para o novo inquilino?
- É a localização geográfica adequada perto o novo inquilino?
- É na camada de serviço correto para o novo inquilino?

Quando pretender que o novo inquilino para ser isolados em seu próprio banco de dados, pode criar para atender às especificações para o inquilino.

Após o aprovisionamento estiver concluído, tem de registar o inquilino no catálogo. Por fim, o mapeamento do inquilino pode ser adicionado para fazer referência a partição horizontal adequada.

#### <a name="template-database"></a>Base de dados do modelo

Aprovisione a base de dados ao executar scripts SQL, implementar um bacpac ou copiar uma base de dados do modelo. As aplicações de Wingtip copiar uma base de dados de modelo para criar novas bases de dados do inquilino.

Como qualquer aplicação Wingtip evoluirá ao longo do tempo. Às vezes, a Wingtip exigirão alterações no banco de dados. As alterações podem incluir os seguintes itens:
- Esquema de nova ou alterada.
- Dados de referência de novos ou alterados.
- Tarefas de manutenção de rotina da base de dados para assegurar o desempenho de aplicações ideal.

Com uma aplicação SaaS, estas alterações têm de ser implementadas de forma coordenada através de uma frota potencialmente massiva de bases de dados de inquilinos. Para que essas alterações ser, no futuro, bases de dados do inquilino, eles precisam ser incorporados ao processo de aprovisionamento. Esse desafio é explorado mais detalhadamente no [tutorial de gestão de esquema](saas-tenancy-schema-management.md).

#### <a name="scripts"></a>Scripts

Os scripts de aprovisionamento do inquilino neste tutorial de suportar os cenários seguintes:
- O aprovisionamento de um inquilino numa base de dados partilhado com outros inquilinos.
- O aprovisionamento de um inquilino em sua própria base de dados.

Dados de inquilino, em seguida, são inicializados e registados no mapa de partições horizontais de catálogo. No aplicativo de exemplo, bancos de dados que contêm vários inquilinos recebem um nome genérico, como *tenants1* ou *tenants2*. Bases de dados que contêm um único inquilino recebem o nome do inquilino. As convenções de nomenclatura específicas usadas nesse exemplo não são uma parte crítica do padrão, como a utilização de um catálogo permite que qualquer nome a ser atribuído para a base de dados.  

<a name="goto_1_tutorial"/>

## <a name="tutorial-begins"></a>Tutorial de início

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Aprovisionar um inquilino num banco de dados do multi-inquilino
> * Aprovisionar um inquilino num banco de dados de inquilino único
> * Aprovisionar um lote de inquilinos em bases de dados do multi-inquilinos e de inquilino único
> * Registe-se de uma base de dados e o mapeamento num catálogo do inquilino

#### <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, confirme que conclui os pré-requisitos seguintes:

- O Azure PowerShell está instalado. Para obter mais detalhes, veja [Introdução ao Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps)

- A aplicação de base de dados do Wingtip Tickets SaaS multi-inquilino é implementada. Para implementar em menos de cinco minutos, veja [implementar e explorar a aplicação de base de dados do Wingtip Tickets SaaS multi-inquilino](saas-multitenantdb-get-started-deploy.md)

- Obter os scripts de Wingtip e código-fonte:
    - Os scripts de base de dados do Wingtip Tickets SaaS multi-inquilino e o código de origem da aplicação estão disponíveis no [WingtipTicketsSaaS MultitenantDB](https://github.com/microsoft/WingtipTicketsSaaS-MultiTenantDB) repositório do GitHub.
    - Consulte a [orientações gerais](saas-tenancy-wingtip-app-guidance-tips.md) para obter os passos transferir e os scripts de Wingtip de desbloqueio. 

## <a name="provision-a-tenant-into-a-database-shared-with-other-tenants"></a>Aprovisionar um inquilino numa base de dados *partilhado* com outros inquilinos

Nesta secção, é apresentada uma lista das ações principais para o aprovisionamento efetuadas pelos scripts do PowerShell. Em seguida, utilize o depurador do ISE do PowerShell para os scripts para ver as ações no código.

#### <a name="major-actions-of-provisioning"></a>Ações de principais de aprovisionamento

Seguem-se os principais elementos do fluxo de trabalho aprovisionamento, siga os passos:

- **Calcular a nova chave de inquilino**: É utilizada uma função hash para criar a chave de inquilino a partir do nome do inquilino.
- **Verificar se a chave de inquilino já existe**: O catálogo é verificado para garantir que a chave já não foi registrada.
- **Inicializar o inquilino na base de dados de inquilino predefinida**: A base de dados do inquilino é atualizado para adicionar as novas informações de inquilino.  
- **Registar o inquilino no catálogo**: O mapeamento entre a nova chave de inquilino e a base de dados existente do tenants1 é adicionado ao catálogo. 
- **Adicionar o nome do inquilino a uma tabela de extensão de catálogo**: O nome de local é adicionado à tabela de inquilinos no catálogo.  Esta adição mostra como a base de dados do catálogo pode ser estendido para oferecer suporte a dados adicionais de específico do aplicativo.
- **Página aberta de eventos para o novo inquilino**: O *Bushwillow Blues* página Eventos é aberta no browser.

   ![eventos](media/saas-multitenantdb-provision-and-catalog/bushwillow.png)

#### <a name="debugger-steps"></a>Passos do depurador

Para compreender como a aplicação Wingtip implementa o aprovisionamento numa base de dados partilhado do novo inquilino, adicione um ponto de interrupção e siga o fluxo de trabalho:

1. Na *ISE do PowerShell*, abra... \\Módulos de aprendizagem\\ProvisionTenants\\*demonstração ProvisionTenants.ps1* e defina os seguintes parâmetros:
   - **$TenantName** = **Bushwillow Blues**, o nome de um novo local.
   - **$VenueType** = **blues**, um dos tipos predefinidos de local: blues, música clássica, dança, jazz, judo, corrida, opera, música Rock, futebol (em minúsculas, sem espaços).
   - **$DemoScenario** = **1**, para aprovisionar um inquilino na base de dados partilhada com outros inquilinos.

2. Adicione um ponto de interrupção ao colocar o cursor em qualquer parte na linha 38, a linha que diz: *Novo inquilino '* e, em seguida, prima **F9**.

   ![ponto de interrupção](media/saas-multitenantdb-provision-and-catalog/breakpoint.png)

3. Execute o script, premindo **F5**.

4. Depois de parar a execução do script no ponto de interrupção, prima **F11** para avançar para o código.

   ![depurar](media/saas-multitenantdb-provision-and-catalog/debug.png)

5. Rastrear a execução do script com o **depurar** opções de menu **F10** e **F11**, ao passo através de ou para funções chamadas.

Para obter mais informações sobre a depuração de scripts do PowerShell, consulte [sugestões sobre como trabalhar e depurar scripts do PowerShell](https://msdn.microsoft.com/powershell/scripting/core-powershell/ise/how-to-debug-scripts-in-windows-powershell-ise).

## <a name="provision-a-tenant-in-its-own-database"></a>Aprovisionar um inquilino no respetivo *próprio* base de dados

#### <a name="major-actions-of-provisioning"></a>Ações de principais de aprovisionamento

Seguem-se os principais elementos do fluxo de trabalho que avança ao rastreamento o script:

- **Calcular a nova chave de inquilino**: É utilizada uma função hash para criar a chave de inquilino a partir do nome do inquilino.
- **Verificar se a chave de inquilino já existe**: O catálogo é verificado para garantir que a chave já não foi registrada.
- **Criar uma nova base de dados do inquilino**: A base de dados é criado ao copiar o *basetenantdb* base de dados com um modelo do Resource Manager.  O novo nome de base de dados baseia-se no nome do inquilino.
- **Adicionar a base de dados para o catálogo de**: A nova base de dados de inquilino está registado como uma partição horizontal no catálogo.
- **Inicializar o inquilino na base de dados de inquilino predefinida**: A base de dados do inquilino é atualizado para adicionar as novas informações de inquilino.  
- **Registar o inquilino no catálogo**: O mapeamento entre a nova chave de inquilino e o *sequoiasoccer* base de dados é adicionada ao catálogo.
- **Nome do inquilino é adicionado ao catálogo de**: O nome de local é adicionado à tabela de extensão de inquilinos no catálogo.
- **Página aberta de eventos para o novo inquilino**: O *Sequoia futebol* página Eventos é aberta no browser.

   ![eventos](media/saas-multitenantdb-provision-and-catalog/sequoiasoccer.png)

#### <a name="debugger-steps"></a>Passos do depurador

Agora percorra o processo de script ao criar um inquilino na sua própria base de dados:

1. Ainda no... \\Módulos de aprendizagem\\ProvisionTenants\\*demonstração ProvisionTenants.ps1* definir os seguintes parâmetros:
   - **$TenantName** = **sequoia futebol**, o nome de um novo local.
   - **$VenueType** = **futebol**, um dos tipos predefinidos de local: blues, música clássica, dança, jazz, judo, corrida, opera, música Rock, futebol (letras minúsculas, sem espaços).
   - **$DemoScenario** = **2**, para aprovisionar um inquilino na sua própria base de dados.

2. Adicionar um novo ponto de interrupção ao colocar o cursor em qualquer parte na linha 57, a linha que diz:  *& &nbsp;$PSScriptRoot\New-TenantAndDatabase '* e prima **F9**.

   ![ponto de interrupção](media/saas-multitenantdb-provision-and-catalog/breakpoint2.png)

3. Execute o script, premindo **F5**.

4. Depois de parar a execução do script no ponto de interrupção, prima **F11** para avançar para o código.  Uso **F10** e **F11** sobre e passar para as funções para rastrear a execução.

## <a name="provision-a-batch-of-tenants"></a>Aprovisionar um lote de inquilinos

Este exercício aprovisiona um lote de 17 inquilinos. Recomenda-se que aprovisionar este lote de inquilinos antes de iniciar outros tutoriais do Wingtip Tickets, portanto, existem mais bases de dados para trabalhar com.

1. Na *ISE do PowerShell*, abra... \\Módulos de aprendizagem\\ProvisionTenants\\*demonstração ProvisionTenants.ps1* e alterar o *$DemoScenario* parâmetro para 4:
   - **$DemoScenario** = **4**, para aprovisionar um lote de inquilinos numa base de dados partilhado.

2. Prima **F5** e execute o script.

### <a name="verify-the-deployed-set-of-tenants"></a>Verifique se o conjunto implementado de inquilinos 

Nesta fase, tiver uma mistura de inquilinos implementados num banco de dados partilhado e implementados em suas próprias bases de dados de inquilinos. O portal do Azure pode ser utilizado para inspecionar as bases de dados criados. Na [portal do Azure](https://portal.azure.com), abra o **tenants1-mt -\<utilizador\>**  server ao navegar para a lista de servidores SQL.  O **bases de dados SQL** lista deve incluir o partilhado **tenants1** da base de dados e as bases de dados para os inquilinos que estão na sua própria base de dados:

   ![lista de bases de dados](media/saas-multitenantdb-provision-and-catalog/Databases.png)

Enquanto o portal do Azure mostra o inquilino bases de dados, não lhe permite ver os inquilinos *dentro de* a base de dados partilhada. A lista completa de inquilinos pode ser vista na **Hub de eventos** página Web de Wingtip e ao procurar o catálogo.

#### <a name="using-wingtip-tickets-events-hub-page"></a>Usando a página do hub de eventos de bilhetes Wingtip

Abra a página do Hub de eventos no navegador (http:events.wingtip-mt.\<utilizador\>. trafficmanager.net)  

#### <a name="using-catalog-database"></a>Utilizar base de dados do catálogo

A lista completa de inquilinos e a base de dados correspondente para cada um está disponível no catálogo. Uma vista SQL é desde que o nome de inquilino associações para o nome de base de dados. O modo de exibição bem demonstra o valor de expandir os metadados armazenados no catálogo.
- A vista SQL está disponível na base de dados tenantcatalog.
- O nome do inquilino é armazenado na tabela de inquilinos.
- O nome de base de dados é armazenado nas tabelas de gestão de partições horizontais.

1. No SQL Server Management Studio (SSMS), ligar para o servidor de inquilinos na **ta. o catálogo\<USUÁRIO\>. database.windows.net**, com início de sessão = **desenvolvedor**e a palavra-passe =  **P\@ssword1**

    ![Caixa de diálogo de ligação de SSMS](media/saas-multitenantdb-provision-and-catalog/SSMSConnection.png)

2. No Object Explorer do SSMS, navegue para as vistas na *tenantcatalog* base de dados.

3. Clique com o botão direito do rato na vista *TenantsExtended* e escolha **selecionar primeiras 1000 linhas**. Tenha em atenção o mapeamento entre o nome do inquilino e a base de dados para os inquilinos diferentes.

    ![Vista de ExtendedTenants no SSMS](media/saas-multitenantdb-provision-and-catalog/extendedtenantsview.png)
      
## <a name="other-provisioning-patterns"></a>Outros padrões de aprovisionamento

Esta secção descreve outros padrões de aprovisionamento interessantes.

#### <a name="pre-provisioning-databases-in-elastic-pools"></a>Pré-aprovisionamento de bases de dados nos conjuntos elásticos

O padrão de pré-aprovisionamento explora o fato de que ao utilizar os conjuntos elásticos, a faturação é para o conjunto não as bases de dados. Assim, bases de dados podem ser adicionados a um conjunto elástico antes que eles são necessários sem incorrer em custos adicionais. Este previamente visioning significativamente reduz o tempo necessário para aprovisionar um inquilino num banco de dados. O número de bases de dados previamente aprovisionadas pode ser ajustado conforme necessário para manter uma memória intermédia adequada para a taxa de aprovisionamento previsível.

#### <a name="auto-provisioning"></a>Aprovisionamento automático

O padrão de aprovisionamento automático, um serviço de aprovisionamento dedicado é utilizado para aprovisionar servidores, conjuntos e bases de dados automaticamente conforme necessário. Esta automatização inclui o pré-aprovisionamento de bases de dados nos conjuntos elásticos. E se as bases de dados são descomissionados e eliminados, as lacunas que isso cria nos conjuntos elásticos podem ser preenchidas pelo serviço de aprovisionamento conforme pretendido.

Este tipo de serviço automatizado pode ser simples ou complexas. Por exemplo, a automação poderia lidar com aprovisionamento em vários locais geográficos e poderia configurar a georreplicação para recuperação após desastre. Com o padrão de aprovisionamento automático, uma aplicação cliente ou script poderia submeter um pedido de aprovisionamento a uma fila para ser processado por um serviço de aprovisionamento. O script, em seguida, poderia consultar para detetar a conclusão. Se for utilizado a pré-aprovisionamento, o pedidos poderiam ser processados rapidamente, enquanto um serviço em segundo plano iria gerir o aprovisionamento de uma base de dados de substituição.

## <a name="additional-resources"></a>Recursos adicionais

<!-- - Additional [tutorials that build upon the Wingtip SaaS application](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)-->
- [Biblioteca de clientes da base de dados elástica](sql-database-elastic-database-client-library.md)
- [How to Debug Scripts in Windows PowerShell ISE (Como Depurar os Scripts do ISE do Windows PowerShell)](https://msdn.microsoft.com/powershell/scripting/core-powershell/ise/how-to-debug-scripts-in-windows-powershell-ise)


## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Aprovisionar um inquilino individual novo numa base de dados partilhada do multi-inquilino e a sua própria base de dados
> * Aprovisionar um lote de inquilinos adicionais
> * Percorra os detalhes de aprovisionamento de inquilinos e como registá-los para o catálogo

Experimente o [tutorial de monitorização do desempenho](saas-multitenantdb-performance-monitoring.md).

