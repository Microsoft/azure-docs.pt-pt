---
title: Provisionar em SaaS multilocatário
description: Saiba como provisionar e catalogar novos locatários em um aplicativo SaaS multilocatário do banco de dados SQL do Azure
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MightyPen
ms.author: genemi
ms.reviewer: billgib,andrela,stein
ms.date: 09/24/2018
ms.openlocfilehash: cae0b2730a9426b183dc330a18a76122ac87cc66
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73817932"
---
# <a name="provision-and-catalog-new-tenants-in-a-saas-application-using-a-sharded-multi-tenant-azure-sql-database"></a>Provisionar e catalogar novos locatários em um aplicativo SaaS usando um banco de dados SQL do Azure com vários locatários fragmentado

Este artigo aborda o provisionamento e a catalogação de novos locatários, em um modelo ou padrão de *banco de dados fragmentado de vários locatários* .

Este artigo tem duas partes principais:

- [Discussão conceitual](#goto_2_conceptual) do provisionamento e da catalogação de novos locatários.

- [Tutorial](#goto_1_tutorial) que realça o código de script do PowerShell que realiza o provisionamento e a catalogação.
  - O tutorial usa o aplicativo de SaaS Wingtip tickets, adaptado para o padrão de banco de dados fragmentado de vários locatários.

<a name="goto_2_conceptual"/>

## <a name="database-pattern"></a>Padrão de banco de dados

Esta seção, além de mais alguns itens a seguir, discute os conceitos do padrão de banco de dados fragmentado de vários locatários.

Nesse modelo fragmentado multilocatário, os esquemas de tabela dentro de cada banco de dados incluem uma chave de locatário na chave primária de tabelas que armazenam dados de locatário. A chave de locatário permite que cada banco de dados individual armazene 0, 1 ou muitos locatários. O uso de bancos de dados fragmentados torna mais fácil para o sistema de aplicativos dar suporte a um número muito grande de locatários. Todos os dados de um locatário são armazenados em um banco de dado. O grande número de locatários é distribuído entre vários bancos de dados fragmentados. Um banco de dados de catálogo armazena o mapeamento de cada locatário para seu banco de dados.

#### <a name="isolation-versus-lower-cost"></a>Isolamento versus custo mais baixo

Um locatário que tem um banco de dados para ele mesmo aproveita os benefícios do isolamento. O locatário pode ter o banco de dados restaurado para uma data anterior sem ser restrito pelo impacto em outros locatários. O desempenho do banco de dados pode ser ajustado para otimizar para o único locatário, sem precisar comprometer com outros locatários. O problema é que o isolamento custa mais do que os custos para compartilhar um banco de dados com outros locatários.

Quando um novo locatário é provisionado, ele pode compartilhar um banco de dados com outros locatários ou pode ser colocado em seu próprio banco de dados novo. Posteriormente, você pode mudar de ideia e mover o banco de dados para a outra situação.

Bancos de dados com vários locatários e locatários únicos são misturados no mesmo aplicativo SaaS, para otimizar o custo ou o isolamento para cada locatário.

   ![Aplicativo de banco de dados multilocatário fragmentado com catálogo de locatários](media/saas-multitenantdb-provision-and-catalog/MultiTenantCatalog.png)

## <a name="tenant-catalog-pattern"></a>Padrão de catálogo de locatários

Quando você tem dois ou mais bancos de dados que contêm pelo menos um locatário, o aplicativo deve ter uma maneira de descobrir qual banco de dados armazena o locatário do interesse atual. Um banco de dados de catálogo armazena esse mapeamento.

#### <a name="tenant-key"></a>Chave de locatário

Para cada locatário, o aplicativo Wingtip pode derivar uma chave exclusiva, que é a chave de locatário. O aplicativo extrai o nome do locatário da URL da página da Web. O aplicativo aplica hash ao nome para obter a chave. O aplicativo usa a chave para acessar o catálogo. O catálogo faz referência cruzada de informações sobre o banco de dados no qual o locatário está armazenado. O aplicativo usa as informações do banco de dados para se conectar. Outros esquemas de chave de locatário também podem ser usados.

O uso de um catálogo permite que o nome ou o local de um banco de dados de locatário seja alterado após o provisionamento sem interromper o aplicativo. Em um modelo de banco de dados de vários locatários, o catálogo acomoda a movimentação de um locatário entre bancos.

#### <a name="tenant-metadata-beyond-location"></a>Metadados do locatário além do local

O catálogo também pode indicar se um locatário está offline para manutenção ou outras ações. E o catálogo pode ser estendido para armazenar metadados adicionais de locatário ou banco de dados, como os seguintes itens:
- A camada de serviço ou a edição de um banco de dados.
- A versão do esquema de banco de dados.
- O nome do locatário e seu SLA (contrato de nível de serviço).
- Informações para habilitar o gerenciamento de aplicativos, atendimento ao cliente ou processos DevOps.  

O catálogo também pode ser usado para habilitar relatórios entre locatários, gerenciamento de esquema e extração de dados para fins de análise. 

### <a name="elastic-database-client-library"></a>Biblioteca de Clientes da Base de Dados Elástica 

Na Wingtip, o catálogo é implementado no banco de dados *tenantcatalog* . O *tenantcatalog* é criado usando os recursos de gerenciamento de fragmentos da [biblioteca de cliente do banco de dados elástico (EDCL)](sql-database-elastic-database-client-library.md). A biblioteca permite que um aplicativo crie, gerencie e use um *mapa de fragmentos* que é armazenado em um banco de dados. Um mapa de fragmentos faz referência cruzada à chave de locatário com seu fragmento, ou seja, seu banco de dados fragmentado.

Durante o provisionamento de locatário, as funções EDCL podem ser usadas de aplicativos ou scripts do PowerShell para criar as entradas no mapa de fragmentos. Posteriormente, as funções EDCL podem ser usadas para se conectar ao banco de dados correto. O EDCL armazena em cache as informações de conexão para minimizar o tráfego no banco de dados do catálogo e acelerar o processo de conexão.

> [!IMPORTANT]
> *Não* edite os dados no banco de dado de catálogo por meio do acesso direto! Não há suporte para atualizações diretas devido ao alto risco de corrupção de dados. Em vez disso, edite os dados de mapeamento usando apenas APIs EDCL.

## <a name="tenant-provisioning-pattern"></a>Padrão de provisionamento de locatário

#### <a name="checklist"></a>Lista de Verificação

Quando desejar provisionar um novo locatário em um banco de dados compartilhado existente, do banco de dados compartilhado, você deverá fazer as seguintes perguntas:
- Ele tem espaço suficiente restante para o novo locatário?
- Ele tem tabelas com os dados de referência necessários para o novo locatário ou os dados podem ser adicionados?
- Ele tem a variação apropriada do esquema base para o novo locatário?
- Está na localização geográfica apropriada perto do novo locatário?
- Ele está na camada de serviço certa para o novo locatário?

Quando desejar que o novo locatário seja isolado em seu próprio banco de dados, você poderá criá-lo para atender às especificações do locatário.

Depois que o provisionamento for concluído, você deverá registrar o locatário no catálogo. Por fim, o mapeamento de locatário pode ser adicionado para referenciar o fragmento apropriado.

#### <a name="template-database"></a>Banco de dados de modelo

Provisione o banco de dados executando scripts SQL, implantando um bacpac ou copiando um banco de dados de modelo. Os aplicativos Wingtip copiam um banco de dados de modelo para criar novos bancos de dados de locatário.

Como qualquer aplicativo, a Wingtip evoluirá com o decorrer do tempo. Às vezes, a Wingtip exigirá alterações no banco de dados. As alterações podem incluir os seguintes itens:
- Esquema novo ou alterado.
- Dados de referência novos ou alterados.
- Tarefas rotineiras de manutenção de banco de dados para garantir o desempenho ideal do aplicativo.

Com uma aplicação SaaS, estas alterações têm de ser implementadas de forma coordenada através de uma frota potencialmente massiva de bases de dados de inquilinos. Para que essas alterações estejam em bancos de dados de locatário futuros, elas precisam ser incorporadas ao processo de provisionamento. Esse desafio é explorado ainda mais no [tutorial de gerenciamento de esquema](saas-tenancy-schema-management.md).

#### <a name="scripts"></a>Scripts

Os scripts de provisionamento de locatário neste tutorial oferecem suporte aos dois cenários a seguir:
- Provisionamento de um locatário em um banco de dados existente compartilhado com outros locatários.
- Provisionando um locatário em seu próprio banco de dados.

Os dados de locatário são inicializados e registrados no mapa de fragmentos do catálogo. No aplicativo de exemplo, os bancos de dados que contêm vários locatários recebem um nome genérico, como *tenants1* ou *tenants2*. Os bancos de dados que contêm um único locatário recebem o nome do locatário. As convenções de nomenclatura específicas usadas no exemplo não são uma parte crítica do padrão, pois o uso de um catálogo permite que qualquer nome seja atribuído ao banco de dados.  

<a name="goto_1_tutorial"/>

## <a name="tutorial-begins"></a>Início do tutorial

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Provisionar um locatário em um banco de dados de vários locatários
> * Provisionar um locatário em um banco de dados de locatário único
> * Provisionar um lote de locatários em bancos de dados multilocatário e de locatário único
> * Registrar um mapeamento de banco de dados e locatário em um catálogo

#### <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, confirme que conclui os pré-requisitos seguintes:

- O Azure PowerShell está instalado. Para obter mais detalhes, veja [Introdução ao Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps)

- O aplicativo de banco de dados multilocatário do Wingtip tickets SaaS foi implantado. Para implantar em menos de cinco minutos, consulte [implantar e explorar o aplicativo de banco de dados multilocatário do Wingtip tickets SaaS](saas-multitenantdb-get-started-deploy.md)

- Obtenha os scripts da Wingtip e o código-fonte:
    - Os scripts de banco de dados multilocatário do Wingtip tickets SaaS e o código-fonte do aplicativo estão disponíveis no repositório GitHub [repositório wingtipticketssaas-MultitenantDB](https://github.com/microsoft/WingtipTicketsSaaS-MultiTenantDB) .
    - Consulte as [diretrizes gerais](saas-tenancy-wingtip-app-guidance-tips.md) para obter as etapas para baixar e desbloquear os scripts Wingtip. 

## <a name="provision-a-tenant-into-a-database-shared-with-other-tenants"></a>Provisionar um locatário em um banco de dados *compartilhado* com outros locatários

Nesta seção, você verá uma lista das principais ações de provisionamento que são realizadas pelos scripts do PowerShell. Em seguida, use o depurador do ISE do PowerShell para percorrer os scripts e ver as ações no código.

#### <a name="major-actions-of-provisioning"></a>Principais ações de provisionamento

Veja a seguir os principais elementos do fluxo de trabalho de provisionamento que você percorre:

- **Calcular a nova chave de locatário**: uma função de hash é usada para criar a chave de locatário a partir do nome do locatário.
- **Verifique se a chave de locatário já existe**: o catálogo é verificado para garantir que a chave ainda não tenha sido registrada.
- **Inicializar o locatário no banco de dados de locatário padrão**: o banco de dados de locatário é atualizado para adicionar as novas informações de locatário.  
- **Registrar o locatário no catálogo**: o mapeamento entre a nova chave de locatário e o banco de dados tenants1 existente é adicionado ao catálogo. 
- **Adicione o nome do locatário a uma tabela de extensão de catálogo**: o nome do local é adicionado à tabela locatários no catálogo.  Essa adição mostra como o banco de dados do catálogo pode ser estendido para dar suporte a dados específicos do aplicativo adicionais.
- **Abrir a página de eventos para o novo locatário**: a página de eventos do *Bushwillow azuis* é aberta no navegador.

   ![eventos](media/saas-multitenantdb-provision-and-catalog/bushwillow.png)

#### <a name="debugger-steps"></a>Etapas do depurador

Para entender como o aplicativo Wingtip implementa o novo provisionamento de locatário em um banco de dados compartilhado, adicione um ponto de interrupção e percorra o fluxo de trabalho:

1. No *ISE do PowerShell*, abra...\\módulos de aprendizado\\ProvisionTenants\\*provisiontenants. ps1* e defina os seguintes parâmetros:
   - **$TenantName** = **Bushwillow azuis**, o nome de um novo local.
   - **$VenueType** = **azuis**, um dos tipos de local predefinidos: azuis, ClassicalMusic, dança, jazz, judo, motorracing, Multipurpose, Opera, ROCKMUSIC, futebol (minúsculas, sem espaços).
   - **$DemoScenario** = **1**, para provisionar um locatário em um banco de dados compartilhado com outros locatários.

2. Adicione um ponto de interrupção colocando o cursor em qualquer lugar na linha 38, a linha que diz: *New-locatário '* e pressione **F9**.

   ![ponto de interrupção](media/saas-multitenantdb-provision-and-catalog/breakpoint.png)

3. Execute o script pressionando **F5**.

4. Após a execução do script parar no ponto de interrupção, pressione **F11** para entrar no código.

   ![verificação](media/saas-multitenantdb-provision-and-catalog/debug.png)

5. Rastreie a execução do script usando as opções do menu **depurar** , **F10** e **F11**, para depurar ou entrar em funções chamadas.

Para obter mais informações sobre como depurar scripts do PowerShell, consulte [dicas sobre como trabalhar com e depurar scripts do PowerShell](https://msdn.microsoft.com/powershell/scripting/core-powershell/ise/how-to-debug-scripts-in-windows-powershell-ise).

## <a name="provision-a-tenant-in-its-own-database"></a>Provisionar um locatário em seu *próprio* banco de dados

#### <a name="major-actions-of-provisioning"></a>Principais ações de provisionamento

Veja a seguir os principais elementos do fluxo de trabalho que você percorre ao rastrear o script:

- **Calcular a nova chave de locatário**: uma função de hash é usada para criar a chave de locatário a partir do nome do locatário.
- **Verifique se a chave de locatário já existe**: o catálogo é verificado para garantir que a chave ainda não tenha sido registrada.
- **Criar um novo banco de dados de locatário**: o banco de dados é criado copiando o banco de dados *basetenantdb* usando um modelo do Resource Manager.  O novo nome do banco de dados é baseado no nome do locatário.
- **Adicionar Banco de dados ao catálogo**: o novo banco de dados de locatário é registrado como um fragmento no catálogo.
- **Inicializar o locatário no banco de dados de locatário padrão**: o banco de dados de locatário é atualizado para adicionar as novas informações de locatário.  
- **Registrar o locatário no catálogo**: o mapeamento entre a nova chave de locatário e o banco de dados *sequoiasoccer* é adicionado ao catálogo.
- O **nome do locatário é adicionado ao catálogo**: o nome do local é adicionado à tabela de extensões de locatários no catálogo.
- **Abrir a página de eventos para o novo locatário**: a página de eventos do *Sequoia futebol* é aberta no navegador.

   ![eventos](media/saas-multitenantdb-provision-and-catalog/sequoiasoccer.png)

#### <a name="debugger-steps"></a>Etapas do depurador

Agora, percorra o processo de script ao criar um locatário em seu próprio banco de dados:

1. Ainda em...\\módulos de aprendizado\\ProvisionTenants\\*provisiontenants. ps1* defina os seguintes parâmetros:
   - **$TenantName** = **futebol Sequoia**, o nome de um novo local.
   - **$VenueType** = **futebol**, um dos tipos de local predefinidos: azuis, ClassicalMusic, dança, jazz, judo, motorracing, Multipurpose, Opera, ROCKMUSIC, futebol (minúsculo, sem espaços).
   - **$DemoScenario** = **2**, para provisionar um locatário em seu próprio banco de dados.

2. Adicione um novo ponto de interrupção colocando o cursor em qualquer lugar na linha 57, a linha que diz: *&&nbsp;$PSScriptRoot \New-tenantanddatabase '* e pressione **F9**.

   ![ponto de interrupção](media/saas-multitenantdb-provision-and-catalog/breakpoint2.png)

3. Execute o script pressionando **F5**.

4. Após a execução do script parar no ponto de interrupção, pressione **F11** para entrar no código.  Use **F10** e **F11** para percorrer e percorrer as funções para rastrear a execução.

## <a name="provision-a-batch-of-tenants"></a>Provisionar um lote de locatários

Este exercício provisiona um lote de 17 locatários. É recomendável provisionar esse lote de locatários antes de iniciar outros tutoriais do Wingtip tickets para que haja mais bancos de dados com os quais trabalhar.

1. No *ISE do PowerShell*, abra...\\módulos de aprendizado\\ProvisionTenants\\*provisiontenants. ps1* e altere o parâmetro *$DemoScenario* para 4:
   - **$DemoScenario** = **4**, para provisionar um lote de locatários em um banco de dados compartilhado.

2. Prima **F5** e execute o script.

### <a name="verify-the-deployed-set-of-tenants"></a>Verificar o conjunto de locatários implantado 

Neste estágio, você tem uma mistura de locatários implantados em um banco de dados compartilhado e locatários implantados em seus próprios bancos de dados. O portal do Azure pode ser usado para inspecionar os bancos de dados criados. No [portal do Azure](https://portal.azure.com), abra o servidor de **\>de usuário tenants1-MT-\<** navegando até a lista de servidores SQL.  A lista de bancos de dados **SQL** deve incluir o banco de dados **tenants1** compartilhado e os dados para os locatários que estão em seu próprio banco de dados:

   ![lista de bases de dados](media/saas-multitenantdb-provision-and-catalog/Databases.png)

Embora o portal do Azure mostre os bancos de dados de locatário, ele não permite que você veja os locatários *dentro* do banco de dados compartilhado. A lista completa de locatários pode ser vista na página da Web **Hub de eventos** da Wingtip e navegando no catálogo.

#### <a name="using-wingtip-tickets-events-hub-page"></a>Página usando o Hub de eventos de tíquetes Wingtip

Abra a página Hub de eventos no navegador (http: Events. Wingtip-Mt.\<usuário\>. trafficmanager.net)  

#### <a name="using-catalog-database"></a>Usando o banco de dados do catálogo

A lista completa de locatários e o banco de dados correspondente para cada um está disponível no catálogo. É fornecido um modo SQL que une o nome do locatário ao nome do banco de dados. O modo de exibição demonstra bem o valor de estender os metadados que são armazenados no catálogo.
- O modo SQL está disponível no banco de dados tenantcatalog.
- O nome do locatário é armazenado na tabela locatários.
- O nome do banco de dados é armazenado nas tabelas de gerenciamento de fragmentos.

1. No SQL Server Management Studio (SSMS), conecte-se ao servidor de locatários em **Catalog-Mt.\<usuário\>. Database.Windows.net**, com login = **Developer**e password = **P\@ssword1**

    ![Caixa de diálogo de conexão do SSMS](media/saas-multitenantdb-provision-and-catalog/SSMSConnection.png)

2. No Pesquisador de objetos do SSMS, navegue até as exibições no banco de dados *tenantcatalog* .

3. Clique com o botão direito do mouse na exibição *TenantsExtended* e escolha **selecionar as primeiras 1000 linhas**. Observe o mapeamento entre o nome do locatário e o banco de dados para os diferentes locatários.

    ![Exibição do ExtendedTenants no SSMS](media/saas-multitenantdb-provision-and-catalog/extendedtenantsview.png)
      
## <a name="other-provisioning-patterns"></a>Outros padrões de aprovisionamento

Esta seção aborda outros padrões interessantes de provisionamento.

#### <a name="pre-provisioning-databases-in-elastic-pools"></a>Pré-Provisionando bancos de dados em pools elásticos

O padrão de pré-provisionamento explora o fato de que, ao usar pools elásticos, a cobrança é para o pool e não para os bancos de dados. Assim, os bancos de dados podem ser adicionados a um pool elástico antes de serem necessários sem incorrer em custo extra. Essa concepção reduz significativamente o tempo necessário para provisionar um locatário em um banco de dados. O número de bancos de dados previamente provisionados pode ser ajustado conforme necessário para manter um buffer adequado para a taxa de provisionamento prevista.

#### <a name="auto-provisioning"></a>Aprovisionamento automático

No padrão de provisionamento automático, um serviço de provisionamento dedicado é usado para provisionar servidores, pools e bancos de dados automaticamente, conforme necessário. Essa automação inclui o provisionamento prévio de bancos de dados em pools elásticos. E se os bancos de dados forem encerrados e excluídos, as lacunas que isso cria em pools elásticos podem ser preenchidas pelo serviço de provisionamento conforme desejado.

Esse tipo de serviço automatizado pode ser simples ou complexo. Por exemplo, a automação pode lidar com o provisionamento em várias regiões geográficas e pode configurar a replicação geográfica para recuperação de desastre. Com o padrão de provisionamento automático, um script ou aplicativo cliente enviaria uma solicitação de provisionamento para uma fila a ser processada por um serviço de provisionamento. O script então sondaria para detectar a conclusão. Se o provisionamento prévio for usado, as solicitações serão tratadas rapidamente, enquanto um serviço de segundo plano gerenciaria o provisionamento de um banco de dados de substituição.

## <a name="additional-resources"></a>Recursos adicionais

<!-- - Additional [tutorials that build upon the Wingtip SaaS application](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)-->
- [Biblioteca de clientes da base de dados elástica](sql-database-elastic-database-client-library.md)
- [How to Debug Scripts in Windows PowerShell ISE (Como Depurar os Scripts do ISE do Windows PowerShell)](https://msdn.microsoft.com/powershell/scripting/core-powershell/ise/how-to-debug-scripts-in-windows-powershell-ise)


## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Provisionar um único novo locatário em um banco de dados de vários locatários compartilhado e seu próprio banco de dados
> * Aprovisionar um lote de inquilinos adicionais
> * Percorrer os detalhes de provisionamento de locatários e registrá-los no catálogo

Experimente o [tutorial de monitoramento de desempenho](saas-multitenantdb-performance-monitoring.md).

