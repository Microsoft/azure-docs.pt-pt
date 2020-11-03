---
title: Provisão em SaaS multi-inquilino
description: Saiba como providenciar e catalogar novos inquilinos numa app SaaS multi-inquilina de base de dados Azure SQL
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: tutorial
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 09/24/2018
ms.openlocfilehash: eddb0c8339069025f0742e9bcbc371efbef094ee
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/28/2020
ms.locfileid: "92793335"
---
# <a name="provision-and-catalog-new-tenants-in-a-saas-application-using-a-sharded-multi-tenant-azure-sql-database"></a>Provisão e catálogo de novos inquilinos numa aplicação SaaS utilizando uma base de dados Azure SQL de vários inquilinos
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Este artigo abrange o provisionamento e catalogação de novos inquilinos, num modelo ou padrão *de base de dados de vários inquilinos.*

Este artigo tem duas partes principais:

- [Discussão conceptual](#goto_2_conceptual) sobre o provisionamento e catalogação de novos inquilinos.

- [Tutorial](#goto_1_tutorial) que destaca o código de script PowerShell que realiza o provisionamento e catalogação.
  - O tutorial utiliza a aplicação Wingtip Tickets SaaS, adaptada ao padrão de base de dados de vários inquilinos.

<a name="goto_2_conceptual"></a>

## <a name="database-pattern"></a>Padrão da base de dados

Esta secção, mais alguns que se seguem, discutem os conceitos do padrão de base de dados de vários inquilinos.

Neste modelo de vários inquilinos, os esquemas de mesa dentro de cada base de dados incluem uma chave de inquilino na chave primária das tabelas que armazenam dados do inquilino. A chave do inquilino permite que cada base de dados individual armazene 0, 1 ou muitos inquilinos. A utilização de bases de dados de fragmentos facilita o sistema de candidaturas para apoiar um número muito elevado de inquilinos. Todos os dados de um inquilino estão guardados numa base de dados. O grande número de inquilinos é distribuído por muitas bases de dados. Uma base de dados de catálogo armazena o mapeamento de cada inquilino na sua base de dados.

#### <a name="isolation-versus-lower-cost"></a>Isolamento versus custo mais baixo

Um inquilino que tem uma base de dados só para si goza dos benefícios do isolamento. O arrendatário pode ter a base de dados restaurada para uma data anterior sem ser restringido pelo impacto sobre outros inquilinos. O desempenho da base de dados pode ser sintonizado para otimizar para um inquilino, novamente sem ter que se comprometer com outros inquilinos. O problema é que o isolamento custa mais do que custa partilhar uma base de dados com outros inquilinos.

Quando um novo inquilino é a provisionado, pode partilhar uma base de dados com outros inquilinos, ou pode ser colocado na sua própria nova base de dados. Mais tarde pode mudar de ideias e mover a base de dados para a outra situação.

As bases de dados com vários inquilinos e inquilinos individuais são misturadas na mesma aplicação SaaS, para otimizar o custo ou o isolamento para cada inquilino.

   ![App de base de dados de vários inquilinos com catálogo de inquilinos](./media/saas-multitenantdb-provision-and-catalog/MultiTenantCatalog.png)

## <a name="tenant-catalog-pattern"></a>Padrão de catálogo de inquilino

Quando se tem duas ou mais bases de dados que cada uma contém pelo menos um inquilino, o pedido deve ter uma forma de descobrir que base de dados armazena o inquilino de interesse atual. Uma base de dados de catálogo armazena este mapeamento.

#### <a name="tenant-key"></a>Chave do inquilino

Para cada inquilino, a aplicação Wingtip pode obter uma chave única, que é a chave do inquilino. A aplicação extrai o nome do inquilino do URL da página web. A aplicação tem o nome para obter a chave. A aplicação utiliza a chave para aceder ao catálogo. O catálogo cruza informações sobre a base de dados na qual o arrendatário está armazenado. A aplicação utiliza a informação da base de dados para se conectar. Outros esquemas-chave do inquilino também podem ser usados.

A utilização de um catálogo permite que o nome ou localização de uma base de dados de inquilinos seja alterado após o provisionamento sem perturbar a aplicação. Num modelo de base de dados multi-inquilino, o catálogo acomoda a deslocação de um inquilino entre bases de dados.

#### <a name="tenant-metadata-beyond-location"></a>Metadados de inquilinos fora da localização

O catálogo também pode indicar se um inquilino está offline para manutenção ou outras ações. E o catálogo pode ser estendido para armazenar metadados adicionais de inquilinos ou bases de dados, tais como os seguintes itens:
- O nível de serviço ou edição de uma base de dados.
- A versão do esquema da base de dados.
- O nome do inquilino e o seu SLA (contrato de nível de serviço).
- Informação para permitir a gestão de aplicações, suporte ao cliente ou processos de devops.

O catálogo também pode ser usado para permitir relatórios de inquilinos cruzados, gestão de esquemas e extrato de dados para fins de análise.

### <a name="elastic-database-client-library"></a>Biblioteca de clientes de base de dados elástica

Em Wingtip, o catálogo é implementado na base de dados do *tenantcatalog.* O *tenantcatalog* é criado utilizando as funcionalidades de Gestão de Fragmentos da Biblioteca de Clientes de Base de [Dados Elástica (EDCL).](elastic-database-client-library.md) A biblioteca permite que uma aplicação crie, gerencie e utilize um *mapa de fragmentos* que esteja armazenado numa base de dados. Um mapa de fragmentos cruza referências à chave do inquilino com o seu fragmento, o que significa a sua base de dados de fragmentos.

Durante o fornecimento de inquilinos, as funções EDCL podem ser usadas a partir de aplicações ou scripts PowerShell para criar as entradas no mapa de fragmentos. Posteriormente, as funções EDCL podem ser utilizadas para ligar à base de dados correta. O EDCL caches informações de ligação para minimizar o tráfego na base de dados do catálogo e acelerar o processo de ligação.

> [!IMPORTANT]
> Não *not* edite os dados na base de dados do catálogo por acesso direto! As atualizações diretas não são suportadas devido ao elevado risco de corrupção de dados. Em vez disso, edite os dados de mapeamento utilizando apenas as APIs EDCL.

## <a name="tenant-provisioning-pattern"></a>Padrão de provisionamento de inquilinos

#### <a name="checklist"></a>Lista de Verificação

Quando pretender colocar um novo inquilino numa base de dados partilhada existente, da base de dados partilhada deve fazer as seguintes perguntas:
- Tem espaço suficiente para o novo inquilino?
- Possui tabelas com os dados de referência necessários para o novo inquilino, ou os dados podem ser adicionados?
- Tem a variação adequada do esquema base para o novo inquilino?
- É na localização geográfica apropriada perto do novo inquilino?
- Está no nível de serviço certo para o novo inquilino?

Quando quiser que o novo inquilino seja isolado na sua própria base de dados, pode criá-lo de acordo com as especificações para o inquilino.

Após o provisionamento estar completo, deve registar o inquilino no catálogo. Finalmente, o mapeamento do inquilino pode ser adicionado para referenciar o fragmento apropriado.

#### <a name="template-database"></a>Base de dados de modelos

Forragem da base de dados executando scripts SQL, implantando um bacpac ou copiando uma base de dados de modelos. As aplicações Wingtip copiam uma base de dados de modelos para criar novas bases de dados de inquilinos.

Como qualquer aplicação, Wingtip evoluirá ao longo do tempo. Por vezes, o Wingtip requer alterações na base de dados. As alterações podem incluir os seguintes itens:
- Esquema novo ou alterado.
- Dados de referência novos ou alterados.
- Tarefas de manutenção de bases de dados de rotina para garantir o melhor desempenho da aplicação.

Com uma aplicação SaaS, estas alterações têm de ser implementadas de forma coordenada através de uma frota potencialmente massiva de bases de dados de inquilinos. Para que estas alterações sejam introduzidas em futuras bases de dados de inquilinos, têm de ser incorporadas no processo de provisionamento. Este desafio é explorado ainda mais no [tutorial de gestão de esquemas.](saas-tenancy-schema-management.md)

#### <a name="scripts"></a>Scripts

O arrendatário que fornece scripts neste tutorial suporta ambos os seguintes cenários:
- A provisionar um inquilino numa base de dados existente partilhada com outros inquilinos.
- A a provisionar um inquilino na sua própria base de dados.

Os dados do inquilino são então inicializados e registados no mapa de fragmentos do catálogo. Na aplicação da amostra, as bases de dados que contêm vários inquilinos recebem um nome genérico, como *inquilinos1* ou *inquilinos2.* As bases de dados que contêm um único inquilino recebem o nome do inquilino. As convenções específicas de nomeação utilizadas na amostra não são uma parte crítica do padrão, uma vez que a utilização de um catálogo permite que qualquer nome seja atribuído à base de dados.

<a name="goto_1_tutorial"></a>

## <a name="tutorial-begins"></a>Começa o tutorial

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Provisione um inquilino numa base de dados multi-inquilinos
> * Provisione um inquilino numa base de dados de inquilinos únicos
> * Disposição de um lote de inquilinos em bases de dados multi-inquilinos e inquilinos únicos
> * Registar uma base de dados e mapeamento de inquilinos em um catálogo

#### <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, devem ser cumpridos os seguintes pré-requisitos:

- O Azure PowerShell está instalado. Para obter mais detalhes, veja [Introdução ao Azure PowerShell](/powershell/azure/get-started-azureps)

- A aplicação Wingtip Tickets SaaS Multi-tenant Database está implementada. Para implementar em menos de cinco minutos, consulte [implementar e explorar a aplicação de Base de Dados De Multi-inquilinos Wingtip SaaS](./saas-multitenantdb-get-started-deploy.md)

- Obtenha os scripts wingtip e código fonte:
    - Os scripts de base de dados de multi-inquilinos Wingtip SaaS e código fonte de aplicação estão disponíveis no [wingtipTicketsSaS-MultitenantDB](https://github.com/microsoft/WingtipTicketsSaaS-MultiTenantDB) GitHub repo.
    - Consulte as [orientações gerais](saas-tenancy-wingtip-app-guidance-tips.md) para os passos para descarregar e desbloquear os scripts wingtip.

## <a name="provision-a-tenant-into-a-database-shared-with-other-tenants"></a>Provisionamento de um inquilino em uma base de dados *partilhada* com outros inquilinos

Nesta secção, vê-se uma lista das principais ações para o provisionamento que são tomadas pelos scripts PowerShell. Em seguida, você usa o depurador PowerShell ISE para passar pelos scripts para ver as ações em código.

#### <a name="major-actions-of-provisioning"></a>Principais ações de provisionamento

Seguem-se elementos-chave do fluxo de trabalho de provisionamento por onde passa:

- **Calcular a nova chave do inquilino** : Uma função de haxixe é usada para criar a chave do inquilino a partir do nome do inquilino.
- **Verifique se a chave do inquilino já existe:** O catálogo é verificado para garantir que a chave ainda não foi registada.
- **Inicializar o inquilino na base de dados de inquilinos predefinidos** : A base de dados do arrendatário é atualizada para adicionar a nova informação do inquilino.
- **Registre inquilino no catálogo** : O mapeamento entre a nova chave do inquilino e a base de dados existente dos inquilinos1 é adicionado ao catálogo.
- **Adicione o nome do inquilino a uma mesa de extensão de catálogo** : O nome do local é adicionado à mesa dos Inquilinos no catálogo.  Esta adição mostra como a base de dados do Catálogo pode ser alargada para suportar dados adicionais específicos da aplicação.
- **Página Open Events para o novo inquilino** : A página de eventos *Bushwillow Blues* é aberta no navegador.

   ![Screenshot que mostra a página eventos para um novo inquilino.](./media/saas-multitenantdb-provision-and-catalog/bushwillow.png)

#### <a name="debugger-steps"></a>Passos de depuração

Para entender como a app Wingtip implementa o novo provisionamento de inquilinos numa base de dados partilhada, adicione um breakpoint e passe pelo fluxo de trabalho:

1. No *PowerShell ISE,* aberto... \\ Módulos de Aprendizagem \\ ProvisionTenants \\ *Demo-ProvisionTenants.ps1* e definir os seguintes parâmetros:
   - **$TenantName**  =  **Bushwillow Blues,** o nome de um novo local.
   - **$VenueType**  =  **blues** , um dos locais pré-definidos: blues, classicmusic, dance, jazz, judo, motoring, multiusos, ópera, rockmusic, futebol (minúscula, sem espaços).
   - **$DemoScenario**  =  **1** , providenciar um inquilino numa base de dados partilhada com outros inquilinos.

2. Adicione um breakpoint colocando o seu cursor em qualquer lugar da linha 38, a linha que diz: *New-Tenant '* , e, em seguida, pressione **F9**.

   ![Screenshot que destaca a linha que inclui Novo Inquilino.](./media/saas-multitenantdb-provision-and-catalog/breakpoint.png)

3. Executar o script pressionando **F5**.

4. Após a execução do guião parar no ponto de rutura, prima **F11** para entrar no código.

   ![O Screenshot mostra o Windows PowerShell ISE com o menu Debug aberto e o Step Into selecionado.](./media/saas-multitenantdb-provision-and-catalog/debug.png)

5. Trace a execução do script usando as opções de menu **Debug,** **F10** e **F11,** para passar por cima ou para funções chamadas.

Para obter mais informações sobre a depuragem dos scripts PowerShell, consulte [dicas sobre trabalhar e depurar scripts PowerShell](/powershell/scripting/components/ise/how-to-debug-scripts-in-windows-powershell-ise).

## <a name="provision-a-tenant-in-its-own-database"></a>Provisionamento de um inquilino na sua *própria* base de dados

#### <a name="major-actions-of-provisioning"></a>Principais ações de provisionamento

Seguem-se elementos-chave do fluxo de trabalho por onde passa enquanto rastreia o script:

- **Calcular a nova chave do inquilino** : Uma função de haxixe é usada para criar a chave do inquilino a partir do nome do inquilino.
- **Verifique se a chave do inquilino já existe:** O catálogo é verificado para garantir que a chave ainda não foi registada.
- **Criar uma nova base de dados de inquilinos** : A base de dados é criada copiando a base de dados *basetenantdb* utilizando um modelo de Gestor de Recursos.  O novo nome da base de dados baseia-se no nome do inquilino.
- **Adicione base de dados ao catálogo** : A nova base de dados de inquilinos está registada como um fragmento no catálogo.
- **Inicializar o inquilino na base de dados de inquilinos predefinidos** : A base de dados do arrendatário é atualizada para adicionar a nova informação do inquilino.
- **Registre o inquilino no catálogo** : O mapeamento entre a nova chave do inquilino e a base de *dados sequoiasoccer* é adicionado ao catálogo.
- **O nome do inquilino é adicionado ao catálogo** : O nome do local é adicionado à mesa de extensão dos Inquilinos no catálogo.
- **Página Open Events para o novo inquilino** : A página *Sequoia Soccer* Events é aberta no navegador.

   ![eventos](./media/saas-multitenantdb-provision-and-catalog/sequoiasoccer.png)

#### <a name="debugger-steps"></a>Passos de depuração

Agora, caminhe pelo processo de script ao criar um inquilino na sua própria base de dados:

1. Ainda em... \\ Módulos de Aprendizagem \\ ProvisionTenants \\ *Demo-ProvisionTenants.ps1* definir os seguintes parâmetros:
   - **$TenantName**  =  **Sequoia Soccer,** o nome de um novo local.
   - **$VenueType**  =  **futebol** , um dos locais pré-definidos: blues, classicmusic, dance, jazz, judo, motoring, multiusos, ópera, rockmusic, futebol (minúscula, sem espaços).
   - **$DemoScenario**  =  **2** , a provisionar um inquilino na sua própria base de dados.

2. Adicione um novo ponto de rutura colocando o seu cursor em qualquer lugar da linha 57, a linha que diz: *& &nbsp; $PSScriptRoot\New-TenantAndDatabase'* e prima **F9**.

   ![ponto de interrupção](./media/saas-multitenantdb-provision-and-catalog/breakpoint2.png)

3. Executar o script pressionando **F5**.

4. Depois de a execução do guião parar no ponto de rutura, prima **F11** para entrar no código.  Utilize **F10** e **F11** para passar e entrar em funções para rastrear a execução.

## <a name="provision-a-batch-of-tenants"></a>Provisionamento de um lote de inquilinos

Este exercício prevê um lote de 17 inquilinos. Recomenda-se que você provisa este lote de inquilinos antes de iniciar outros tutoriais de Ingressos Wingtip para que haja mais bases de dados para trabalhar.

1. No *PowerShell ISE,* aberto... \\ Módulos de Aprendizagem \\ OsDemo-ProvisionTenants.ps1e alterar o parâmetro \\ ** *$DemoScenario* para 4:
   - **$DemoScenario**  =  **4** , a provisionar um lote de inquilinos numa base de dados partilhada.

2. Prima **F5** e execute o script.

### <a name="verify-the-deployed-set-of-tenants"></a>Verifique o conjunto de inquilinos implantado

Nesta fase, você tem uma mistura de inquilinos implantados em uma base de dados compartilhada e inquilinos implantados em suas próprias bases de dados. O portal Azure pode ser utilizado para inspecionar as bases de dados criadas. No [portal Azure,](https://portal.azure.com)abra o servidor **de \<USER\> inquilinos1-mt-** navegando para a lista de servidores SQL.  A lista **de bases de dados SQL** deve incluir a base de dados de **inquilinos compartilhados1** e as bases de dados para os inquilinos que estão na sua própria base de dados:

   ![lista de bases de dados](./media/saas-multitenantdb-provision-and-catalog/Databases.png)

Enquanto o portal Azure mostra as bases de dados dos inquilinos, não permite que veja os inquilinos *dentro* da base de dados partilhada. A lista completa de inquilinos pode ser vista na página do Centro de **Eventos** do Wingtip, e navegando no catálogo.

#### <a name="using-wingtip-tickets-events-hub-page"></a>Usando a página do hub de eventos de wingtip tickets

Abra a página 'Centro eventos' no navegador (http:events.wingtip-mt. \<USER\> . trafficmanager.net)

#### <a name="using-catalog-database"></a>Utilização da base de dados de catálogos

A lista completa dos inquilinos e a base de dados correspondente para cada um está disponível no catálogo. É fornecida uma vista SQL que une o nome do inquilino ao nome da base de dados. A vista demonstra bem o valor de estender os metadados que são armazenados no catálogo.
- A vista SQL está disponível na base de dados do tenantcatalog.
- O nome do inquilino está guardado na mesa dos Inquilinos.
- O nome da base de dados está guardado nas tabelas Shard Management.

1. No SQL Server Management Studio (SSMS), ligue-se ao servidor de inquilinos no **catalog-mt. \<USER\> database.windows.net** , com Login = **programador** , e Password = **P \@ ssword1**

    ![Diálogo de conexão SSMS](./media/saas-multitenantdb-provision-and-catalog/SSMSConnection.png)

2. No SSMS Object Explorer, consulte as vistas na base de dados do *tenantcatalog.*

3. Clique à direita na vista *TenantsExtended* e escolha **Selecione Top 1000 Rows**. Note o mapeamento entre o nome do inquilino e a base de dados para os diferentes inquilinos.

    ![Vista de Estendidos em SSMS](./media/saas-multitenantdb-provision-and-catalog/extendedtenantsview.png)

## <a name="other-provisioning-patterns"></a>Outros padrões de aprovisionamento

Esta secção discute outros padrões de provisão interessantes.

#### <a name="pre-provisioning-databases-in-elastic-pools"></a>Bases de dados de pré-a provisionamento em piscinas elásticas

O padrão de pré-provisão explora o facto de que, ao utilizar piscinas elásticas, a faturação é para a piscina e não para as bases de dados. Assim, as bases de dados podem ser adicionadas a uma piscina elástica antes de serem necessárias sem incorrer em custos adicionais. Esta pré-visão reduz significativamente o tempo necessário para a colocação de um inquilino numa base de dados. O número de bases de dados pré-a provisionadas pode ser ajustado conforme necessário para manter um tampão adequado à taxa de provisionamento prevista.

#### <a name="auto-provisioning"></a>Aprovisionamento automático

No padrão de fornecimento automático, um serviço de fornecimento dedicado é utilizado para fornecer servidores, piscinas e bases de dados automaticamente, se necessário. Esta automatização inclui o pré-fornecimento de bases de dados em piscinas elásticas. E se as bases de dados forem desativadas e eliminadas, as lacunas que isso cria em piscinas elásticas podem ser preenchidas pelo serviço de fornecimento, conforme desejado.

Este tipo de serviço automatizado pode ser simples ou complexo. Por exemplo, a automatização poderia lidar com o fornecimento em várias geografias, e poderia criar uma geo-replicação para a recuperação de desastres. Com o padrão de a provisionamento automático, um pedido de cliente ou script apresentaria um pedido de provisionamento a uma fila a ser processada por um serviço de fornecimento. O guião iria então sondar para detetar a conclusão. Se for utilizado o pré-fornecimento, os pedidos serão tratados rapidamente, enquanto um serviço de fundo geriria o fornecimento de uma base de dados de substituição.

## <a name="additional-resources"></a>Recursos adicionais

<!-- - Additional [tutorials that build upon the Wingtip SaaS application](./saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)-->
- [Biblioteca de clientes da base de dados elástica](elastic-database-client-library.md)
- [Como depurar scripts no Windows PowerShell ISE](/powershell/scripting/components/ise/how-to-debug-scripts-in-windows-powershell-ise)


## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Provisione um único novo inquilino numa base de dados partilhada de vários inquilinos e sua própria base de dados
> * Aprovisionar um lote de inquilinos adicionais
> * Passo através dos detalhes do provisionamento dos inquilinos, e registrá-los no catálogo

Experimente o [tutorial de monitorização do desempenho.](./saas-multitenantdb-performance-monitoring.md)