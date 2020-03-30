---
title: Provisão em Multi-inquilino saaS
description: Saiba como fornecer e catalogar novos inquilinos numa aplicação SaaS multi-inquilino sqL Da Base de Dados Azure SQL
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
ms.openlocfilehash: 4ea18ee23d845b2d16209b23de14dc3cd70aaa59
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74133144"
---
# <a name="provision-and-catalog-new-tenants-in-a-saas-application-using-a-sharded-multi-tenant-azure-sql-database"></a>Provisão e catalogar novos inquilinos numa aplicação SaaS utilizando uma base de dados azure SQL multi-inquilino sharded

Este artigo abrange o fornecimento e catalogação de novos inquilinos, num modelo ou padrão de *base de dados multi-inquilinos.*

Este artigo tem duas partes principais:

- [Discussão conceptual](#goto_2_conceptual) sobre o provisionamento e catalogação de novos inquilinos.

- [Tutorial](#goto_1_tutorial) que destaca o código de script PowerShell que realiza o fornecimento e catalogação.
  - O tutorial utiliza a aplicação Wingtip Tickets SaaS, adaptada ao padrão de base de dados multi-inquilinos.

<a name="goto_2_conceptual"/>

## <a name="database-pattern"></a>Padrão de base de dados

Esta secção, além de mais algumas que se seguem, discute os conceitos do padrão de base de dados multi-inquilinos.

Neste modelo multi-inquilino, os schemas de mesa dentro de cada base de dados incluem uma chave de inquilino na chave primária das tabelas que armazenam dados de inquilinos. A chave do inquilino permite que cada base de dados individual armazene 0, 1 ou muitos inquilinos. A utilização de bases de dados espumosas facilita o sistema de aplicação para suportar um número muito elevado de inquilinos. Todos os dados de qualquer inquilino estão guardados numa base de dados. O grande número de inquilinos são distribuídos pelas muitas bases de dados espumosas. Uma base de dados de catálogo armazena o mapeamento de cada inquilino na sua base de dados.

#### <a name="isolation-versus-lower-cost"></a>Isolamento versus menor custo

Um inquilino que tem uma base de dados por si só goza dos benefícios do isolamento. O inquilino pode ter a base de dados restaurada para uma data anterior sem ser restringida pelo impacto em outros inquilinos. O desempenho da base de dados pode ser sintonizado para otimizar para um inquilino, novamente sem ter que se comprometer com outros inquilinos. O problema é que o isolamento custa mais do que custa partilhar uma base de dados com outros inquilinos.

Quando um novo inquilino é provisionado, pode partilhar uma base de dados com outros inquilinos, ou pode ser colocado na sua própria nova base de dados. Mais tarde pode mudar de ideias e mover a base de dados para outra situação.

As bases de dados com vários inquilinos e inquilinos solteiros são misturadas na mesma aplicação SaaS, para otimizar o custo ou o isolamento para cada inquilino.

   ![App de base de dados multi-inquilinos com catálogo de inquilinos](media/saas-multitenantdb-provision-and-catalog/MultiTenantCatalog.png)

## <a name="tenant-catalog-pattern"></a>Padrão de catálogo de inquilinos

Quando você tem duas ou mais bases de dados que cada um contém pelo menos um inquilino, a aplicação deve ter uma maneira de descobrir qual base de dados armazena o inquilino de interesse atual. Uma base de dados de catálogo armazena este mapeamento.

#### <a name="tenant-key"></a>Chave do inquilino

Para cada inquilino, a aplicação Wingtip pode derivar uma chave única, que é a chave do inquilino. A aplicação extrai o nome do inquilino do URL da página web. A aplicação tem o nome para obter a chave. A aplicação utiliza a chave para aceder ao catálogo. O catálogo cruza as informações sobre a base de dados em que o inquilino está armazenado. A aplicação utiliza as informações da base de dados para se conectar. Outros regimes-chave para o arrendamento também podem ser utilizados.

A utilização de um catálogo permite que o nome ou localização de uma base de dados de inquilinos seja alterado após o fornecimento sem perturbar a aplicação. Num modelo de base de dados multi-inquilinos, o catálogo acomoda a deslocação de um inquilino entre bases de dados.

#### <a name="tenant-metadata-beyond-location"></a>Metadados de inquilinos além da localização

O catálogo também pode indicar se um inquilino está offline para manutenção ou outras ações. E o catálogo pode ser estendido para armazenar metadados adicionais de inquilinos ou bases de dados, tais como os seguintes itens:
- O nível de serviço ou edição de uma base de dados.
- A versão do esquema da base de dados.
- O nome do arrendatário e o seu SLA (contrato de nível de serviço).
- Informação que permita a gestão da aplicação, apoio ao cliente ou processos devops.

O catálogo também pode ser usado para permitir relatórios de inquilinos transversais, gestão de esquemas e extrato de dados para fins de análise.

### <a name="elastic-database-client-library"></a>Biblioteca de clientes de base de dados elástica

Em Wingtip, o catálogo é implementado na base de dados do catálogo de *inquilinos.* O catálogo de *inquilinos* é criado utilizando as funcionalidades de Gestão de Fragmentos da Biblioteca de Clientes da Base de [Dados Elástica (EDCL)](sql-database-elastic-database-client-library.md). A biblioteca permite que uma aplicação crie, gere e utilize um mapa de *fragmentos* que é armazenado numa base de dados. Um mapa de fragmentos cruza a chave do inquilino com o seu fragmento, o que significa a sua base de dados esfarto.

Durante o fornecimento de inquilinos, as funções EDCL podem ser usadas a partir de aplicações ou scripts PowerShell para criar as entradas no mapa do fragmento. Posteriormente, as funções EDCL podem ser utilizadas para ligar à base de dados correta. A EDCL caches informações de ligação para minimizar o tráfego na base de dados do catálogo e acelerar o processo de ligação.

> [!IMPORTANT]
> Não *not* edite os dados na base de dados do catálogo através do acesso direto! As atualizações diretas não são suportadas devido ao elevado risco de corrupção de dados. Em vez disso, edite os dados de mapeamento usando apenas APIs EDCL.

## <a name="tenant-provisioning-pattern"></a>Padrão de provisionamento de inquilinos

#### <a name="checklist"></a>Lista de Verificação

Quando pretender colocar um novo inquilino numa base de dados partilhada existente, da base de dados partilhada, deve fazer as seguintes perguntas:
- Tem espaço suficiente para o novo inquilino?
- Tem tabelas com os dados de referência necessários para o novo inquilino, ou os dados podem ser adicionados?
- Tem a variação adequada do esquema base para o novo inquilino?
- Está na localização geográfica apropriada perto do novo inquilino?
- Está no nível de serviço certo para o novo inquilino?

Quando você quer que o novo inquilino seja isolado na sua própria base de dados, você pode criá-lo para atender as especificações para o inquilino.

Depois de concluída a provisão, deve registar o inquilino no catálogo. Finalmente, o mapeamento do inquilino pode ser adicionado para referência ao fragmento apropriado.

#### <a name="template-database"></a>Base de dados de modelos

Fornecer a base de dados executando scripts SQL, implantando um bacpac ou copiando uma base de dados de modelos. As aplicações Wingtip copiam uma base de dados de modelos para criar novas bases de dados de inquilinos.

Como qualquer aplicação, wingtip evoluirá ao longo do tempo. Por vezes, o Wingtip necessitará de alterações na base de dados. As alterações podem incluir os seguintes itens:
- Esquema novo ou alterado.
- Dados de referência novos ou alterados.
- Tarefas de manutenção de base de dados de rotina para garantir o melhor desempenho da aplicação.

Com uma aplicação SaaS, estas alterações têm de ser implementadas de forma coordenada através de uma frota potencialmente massiva de bases de dados de inquilinos. Para que estas alterações sejam em futuras bases de dados de inquilinos, estas devem ser incorporadas no processo de provisionamento. Este desafio é explorado ainda mais no tutorial de [gestão de esquemas.](saas-tenancy-schema-management.md)

#### <a name="scripts"></a>Scripts

Os scripts de fornecimento de inquilinos neste tutorial apoiam ambos os seguintes cenários:
- Aprovisionar um inquilino numa base de dados existente partilhada com outros inquilinos.
- Aprovisionar um inquilino na sua própria base de dados.

Os dados dos inquilinos são então inicializados e registados no mapa do catálogo. Na aplicação de amostras, as bases de dados que contenham vários inquilinos recebem um nome genérico, como *inquilinos1* ou *inquilinos2*. Bases de dados que contêm um único inquilino recebem o nome do inquilino. As convenções específicas de nomeação utilizadas na amostra não são uma parte crítica do padrão, uma vez que a utilização de um catálogo permite que qualquer nome seja atribuído à base de dados.

<a name="goto_1_tutorial"/>

## <a name="tutorial-begins"></a>Tutorial começa

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Provisão de um inquilino para uma base de dados multi-inquilinos
> * Provisão de um inquilino para uma base de dados de um único inquilino
> * Provisão de um lote de inquilinos em bases de dados multi-inquilinos e inquilinos únicos
> * Registe uma base de dados e mapeamento de inquilinos num catálogo

#### <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, devem ser cumpridos os seguintes pré-requisitos:

- O Azure PowerShell está instalado. Para obter mais detalhes, veja [Introdução ao Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps)

- A aplicação de base de dados de bilhetes SaaS Multi-inquilinos SaaS está implementada. Para implantar em menos de cinco minutos, consulte Implementar e explorar a aplicação de [base de dados multi-inquilinos SaaS](saas-multitenantdb-get-started-deploy.md)

- Obtenha os scripts Wingtip e código fonte:
    - Os bilhetes Wingtip SaaS Scripts de base de dados multi-inquilinos e código fonte de aplicação estão disponíveis no repo [WingtipTicketsSaaS-MultitenantDB](https://github.com/microsoft/WingtipTicketsSaaS-MultiTenantDB) GitHub.
    - Consulte a [orientação geral](saas-tenancy-wingtip-app-guidance-tips.md) para os passos para descarregar e desbloquear os scripts Wingtip.

## <a name="provision-a-tenant-into-a-database-shared-with-other-tenants"></a>Provisão de um inquilino para uma base de dados *partilhada* com outros inquilinos

Nesta secção, você vê uma lista das principais ações para o provisionamento que são tomadas pelos scripts PowerShell. Depois, usa-se o debugger powerShell ISE para passar pelos scripts para ver as ações em código.

#### <a name="major-actions-of-provisioning"></a>Grandes ações de provisionamento

Seguem-se elementos-chave do fluxo de trabalho de fornecimento por onde passa:

- **Calcular a nova chave do inquilino**: Uma função de hash é usada para criar a chave do inquilino a partir do nome do inquilino.
- **Verifique se a chave do inquilino já existe**: O catálogo é verificado para garantir que a chave ainda não foi registada.
- **Inicializar o inquilino na base**de dados de inquilinos padrão : A base de dados de inquilinos é atualizada para adicionar a nova informação do inquilino.
- **Registre o inquilino no catálogo**: O mapeamento entre a nova chave de inquilinos e a base de dados de inquilinos existentes1 é adicionado ao catálogo.
- **Adicione o nome do inquilino a uma tabela**de extensão do catálogo : O nome do local é adicionado à tabela dos Inquilinos no catálogo.  Esta adição mostra como a base de dados do Catálogo pode ser estendida para suportar dados adicionais específicos da aplicação.
- **Página de Eventos Abertos para o novo inquilino**: A página de eventos *Bushwillow Blues* é aberta no navegador.

   ![eventos](media/saas-multitenantdb-provision-and-catalog/bushwillow.png)

#### <a name="debugger-steps"></a>Passos de debugger

Para entender como a app Wingtip implementa o fornecimento de novos inquilinos numa base de dados partilhada, adicione um ponto de rutura e passe pelo fluxo de trabalho:

1. No *PowerShell ISE,* aberto ... \\Módulos\\de Aprendizagem ProvisionTenants\\*Demo-ProvisionTenants.ps1* e definir os seguintes parâmetros:
   - **$TenantName** = **Bushwillow Blues,** o nome de um novo local.
   - **$VenueType** = **blues**, um dos tipos de recintopré-definidos: blues, música clássica, dança, jazz, judo, automobilismo, multiusos, ópera, rockmusic, futebol (minúscula, sem espaços).
   - **$DemoScenario** = **1,** para fornecer um inquilino numa base de dados partilhada com outros inquilinos.

2. Adicione um ponto de rutura colocando o cursor em qualquer lugar na linha 38, a linha que diz: *Novo Inquilino*' , e, em seguida, prima **F9**.

   ![ponto de interrupção](media/saas-multitenantdb-provision-and-catalog/breakpoint.png)

3. Executar o guião premindo **F5**.

4. Depois de a execução do guião parar no ponto de rutura, prima **F11** para entrar no código.

   ![depurar](media/saas-multitenantdb-provision-and-catalog/debug.png)

5. Trace a execução do script usando as opções do menu **Debug,** **F10** e **F11,** para passar por cima ou em funções chamadas.

Para obter mais informações sobre depuração de scripts PowerShell, consulte [Dicas sobre o trabalho e depurando scripts PowerShell](https://docs.microsoft.com/powershell/scripting/components/ise/how-to-debug-scripts-in-windows-powershell-ise).

## <a name="provision-a-tenant-in-its-own-database"></a>Provisão de um inquilino na sua *própria* base de dados

#### <a name="major-actions-of-provisioning"></a>Grandes ações de provisionamento

Seguem-se elementos-chave do fluxo de trabalho por onde pisa enquanto traça o guião:

- **Calcular a nova chave do inquilino**: Uma função de hash é usada para criar a chave do inquilino a partir do nome do inquilino.
- **Verifique se a chave do inquilino já existe**: O catálogo é verificado para garantir que a chave ainda não foi registada.
- **Criar uma nova base de dados**de inquilinos : A base de dados é criada copiando a base de dados *basetenantdb* usando um modelo de Gestor de Recursos.  O novo nome da base de dados baseia-se no nome do inquilino.
- **Adicione a base**de dados ao catálogo : A nova base de dados de inquilinos está registada como um fragmento no catálogo.
- **Inicializar o inquilino na base**de dados de inquilinos padrão : A base de dados de inquilinos é atualizada para adicionar a nova informação do inquilino.
- **Registre o inquilino no catálogo**: O mapeamento entre a nova chave de inquilinos e a base de dados de *sequoias futebol* é adicionado ao catálogo.
- **O nome do inquilino é adicionado ao catálogo**: O nome do local é adicionado à mesa de extensão dos Inquilinos no catálogo.
- **Página de Eventos Abertos para o novo inquilino**: A página Eventos de Futebol *Sequoia* é aberta no navegador.

   ![eventos](media/saas-multitenantdb-provision-and-catalog/sequoiasoccer.png)

#### <a name="debugger-steps"></a>Passos de debugger

Agora, passe pelo processo do guião ao criar um inquilino na sua própria base de dados:

1. Ainda em... \\Módulos\\de Aprendizagem Provisões Inquilinos\\*Demo-ProvisionTenants.ps1* definir os seguintes parâmetros:
   - **$TenantName** = **Sequoia Soccer,** o nome de um novo local.
   - **$VenueType** = **futebol**, um dos tipos pré-definidos: blues, música clássica, dança, jazz, judo, automobilismo, multiusos, ópera, rockmusic, futebol (minúscula, sem espaços).
   - **$DemoScenario** = **2,** para fornecer um inquilino na sua própria base de dados.

2. Adicione um novo ponto de rutura colocando o cursor em qualquer lugar na linha 57, a linha que diz: * & &nbsp;$PSScriptRoot\New-TenantAndDatabase '*, e prima **F9**.

   ![ponto de interrupção](media/saas-multitenantdb-provision-and-catalog/breakpoint2.png)

3. Executar o guião premindo **F5**.

4. Depois que a execução do guião pare no ponto de rutura, pressione **f11** para entrar no código.  Use **F10** e **F11** para intervir e entrar em funções para rastrear a execução.

## <a name="provision-a-batch-of-tenants"></a>Provisão de um lote de inquilinos

Este exercício prevê um lote de 17 inquilinos. Recomenda-se que forme este lote de inquilinos antes de iniciar outros tutoriais de Bilhetes Wingtip para que haja mais bases de dados para trabalhar.

1. No *PowerShell ISE,* aberto ... \\Módulos\\de Aprendizagem ProvisionTenants\\*Demo-ProvisionTenants.ps1* e altere o parâmetro *$DemoScenario* para 4:
   - **$DemoScenario** = **4,** para fornecer um lote de inquilinos numa base de dados partilhada.

2. Prima **F5** e execute o script.

### <a name="verify-the-deployed-set-of-tenants"></a>Verifique o conjunto de inquilinos destacados

Nesta fase, você tem uma mistura de inquilinos implantados em uma base de dados partilhada e inquilinos implantados em suas próprias bases de dados. O portal Azure pode ser utilizado para inspecionar as bases de dados criadas. No [portal Azure,](https://portal.azure.com)abra o servidor de utilizador dos **inquilinos 1-mt,\<\> ** navegando para a lista de servidores SQL.  A lista de bases de **dados SQL** deve incluir a base de dados partilhada dos **inquilinos1** e as bases de dados para os inquilinos que se encontram na sua própria base de dados:

   ![lista de bases de dados](media/saas-multitenantdb-provision-and-catalog/Databases.png)

Enquanto o portal Azure mostra as bases de dados dos inquilinos, não lhe permite ver os inquilinos *dentro* da base de dados partilhada. A lista completa de inquilinos pode ser vista na página do **Events Hub** da Wingtip, e navegando no catálogo.

#### <a name="using-wingtip-tickets-events-hub-page"></a>Usando a página do centro de eventos de bilhetes wingtip

Abra a página Do Centro de Eventos no navegador\<(http:events.wingtip-mt. USER\>.trafficmanager.net)

#### <a name="using-catalog-database"></a>Usando a base de dados do catálogo

A lista completa dos inquilinos e a base de dados correspondente para cada um está disponível no catálogo. É fornecida uma vista SQL que une o nome do inquilino ao nome da base de dados. A vista demonstra bem o valor de extensão dos metadados armazenados no catálogo.
- A vista SQL está disponível na base de dados do catálogo de inquilinos.
- O nome do inquilino está guardado na mesa dos inquilinos.
- O nome da base de dados está guardado nas tabelas da Shard Management.

1. No SQL Server Management Studio (SSMS), ligue-se ao servidor dos inquilinos no **catálogo-mt.\<USER\>.database.windows.net,** com Login = **developer**, e Password = P **\@ssword1**

    ![Diálogo de ligação SSMS](media/saas-multitenantdb-provision-and-catalog/SSMSConnection.png)

2. No SSMS Object Explorer, navegue pelas vistas na base de dados do catálogo de *inquilinos.*

3. Clique à direita na vista *Inquilinos Estendidos* e escolha **Selecione Top 1000 Rows**. Note o mapeamento entre o nome do inquilino e a base de dados para os diferentes inquilinos.

    ![Vista ExtendedTenants em SSMS](media/saas-multitenantdb-provision-and-catalog/extendedtenantsview.png)

## <a name="other-provisioning-patterns"></a>Outros padrões de aprovisionamento

Esta secção discute outros padrões de provisionamento interessantes.

#### <a name="pre-provisioning-databases-in-elastic-pools"></a>Bases de dados de pré-provisionamento em piscinas elásticas

O padrão de pré-fornecimento explora o facto de que, ao utilizar piscinas elásticas, a faturação é para a piscina e não para as bases de dados. Assim, as bases de dados podem ser adicionadas a uma piscina elástica antes de serem necessárias sem incorrer em custos adicionais. Esta pré-visão reduz significativamente o tempo de aprovisionamento de um inquilino numa base de dados. O número de bases de dados pré-provisionadas pode ser ajustado conforme necessário para manter um tampão adequado à taxa de provisionamento prevista.

#### <a name="auto-provisioning"></a>Aprovisionamento automático

No padrão de fornecimento automático, um serviço de provisionamento dedicado é utilizado para fornecer servidores, piscinas e bases de dados automaticamente, conforme necessário. Esta automatização inclui o pré-fornecimento de bases de dados em piscinas elásticas. E se as bases de dados forem desativadas e eliminadas, as lacunas que isso cria em piscinas elásticas podem ser preenchidas pelo serviço de provisionamento, conforme desejado.

Este tipo de serviço automatizado pode ser simples ou complexo. Por exemplo, a automatização poderia lidar com o fornecimento através de várias geografias, e poderia criar geo-replicação para a recuperação de desastres. Com o padrão de fornecimento automático, uma aplicação ou script do cliente submeteria um pedido de provisionamento a uma fila a ser processada por um serviço de provisionamento. O guião iria então fazer sondagens para detetar a conclusão. Se for utilizado o pré-provisionamento, os pedidos serão tratados rapidamente, enquanto um serviço de fundo geriria o fornecimento de uma base de dados de substituição.

## <a name="additional-resources"></a>Recursos adicionais

<!-- - Additional [tutorials that build upon the Wingtip SaaS application](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)-->
- [Biblioteca de clientes da base de dados elástica](sql-database-elastic-database-client-library.md)
- [How to Debug Scripts in Windows PowerShell ISE (Como Depurar os Scripts do ISE do Windows PowerShell)](https://docs.microsoft.com/powershell/scripting/components/ise/how-to-debug-scripts-in-windows-powershell-ise)


## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Provisão de um único novo inquilino numa base de dados partilhada de multi-inquilinos e sua própria base de dados
> * Aprovisionar um lote de inquilinos adicionais
> * Pise nos detalhes do provisionamento dos inquilinos, e registá-los no catálogo

Experimente o tutorial de [monitorização](saas-multitenantdb-performance-monitoring.md)do desempenho .

