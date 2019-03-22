---
title: Aprovisionar inquilinos novos numa aplicação multi-inquilino que utiliza a base de dados SQL do Azure | Documentos da Microsoft
description: Saiba como aprovisionar e catalogar novos inquilinos numa aplicação SaaS multi-inquilino SQL Database do Azure
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
ms.date: 09/24/2018
ms.openlocfilehash: 803d05e1aaf4d9c26a6132bde30f101ce3905924
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58096831"
---
# <a name="learn-how-to-provision-new-tenants-and-register-them-in-the-catalog"></a>Saiba como aprovisionar novos inquilinos e registá-los no catálogo

Neste tutorial, saiba como aprovisionar e catalogar padrões SaaS. Também aprenderá como eles são implementados na aplicação Wingtip Tickets SaaS da base de dados por inquilino. Criar e inicializar novas bases de dados do inquilino e registá-los no catálogo de inquilino do aplicativo. O catálogo é uma base de dados que mantém o mapeamento entre os muitos inquilinos da aplicação SaaS e os seus dados. O catálogo desempenha um papel importante no direcionamento do aplicativo e pedidos de gestão para a base de dados correto.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> 
> * Aprovisione um inquilino individual novo.
> * Aprovisione um lote de inquilinos adicionais.


Para concluir este tutorial, confirme que conclui os pré-requisitos seguintes:

* A aplicação de base de dados por inquilino Wingtip Tickets SaaS é implementada. Para implementá-lo em menos de cinco minutos, veja [implementar e explorar a aplicação de base de dados por inquilino Wingtip Tickets SaaS](saas-dbpertenant-get-started-deploy.md).
* O Azure PowerShell está instalado. Para obter mais informações, veja [Introdução ao Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps).

## <a name="introduction-to-the-saas-catalog-pattern"></a>Introdução do padrão de catalogação SaaS

Numa aplicação de base de dados SaaS multi-inquilino, é importante saber onde estão armazenadas informações para cada inquilino. Padrão de catalogação SaaS, uma base de dados do catálogo é utilizada para armazenar o mapeamento entre cada inquilino e a base de dados em que os dados são armazenados. Este padrão aplica-se sempre que os dados de inquilino são distribuídos por várias bases de dados.

Cada inquilino é identificado por uma chave no catálogo, o que é mapeado para a localização da sua base de dados. Na aplicação Wingtip Tickets, a chave é formada a partir de um hash do nome do inquilino. Esse esquema permite que a aplicação construir a chave do nome do inquilino incluído no URL da aplicação. Podem ser utilizadas outros esquemas de chave de inquilino.  

O catálogo permite que o nome ou localização da base de dados para serem alterados com um impacto mínimo no aplicativo. Num modelo de base de dados multi-inquilino, esta capacidade também permite a mover um inquilino entre bases de dados. O catálogo também pode ser usado para indicar se um inquilino ou a base de dados está offline para manutenção ou de outras ações. Esta capacidade é explorada a [tutorial de inquilino único de restauro](saas-dbpertenant-restore-single-tenant.md).

O catálogo também pode armazenar inquilino adicional ou metadados de base de dados, como a versão de esquema, o plano do serviço ou a SLAs oferecido aos inquilinos. O catálogo pode armazenar outras informações que permitem a gestão de aplicações, o suporte ao cliente ou o DevOps. 

Além da aplicação SaaS, o catálogo pode ativar as ferramentas de base de dados. O Wingtip Tickets SaaS de exemplo de base de dados por inquilino, o catálogo é utilizado para ativar consulta de entre inquilinos, que é explorada a [relatórios Ad hoc tutorial](saas-tenancy-cross-tenant-reporting.md). Gestão de tarefas entre bases de dados é explorada a [gestão de esquemas](saas-tenancy-schema-management.md) e [análise de inquilinos](saas-tenancy-tenant-analytics.md) tutoriais. 

Os exemplos de Wingtip Tickets SaaS, o catálogo é implementado utilizando as funcionalidades de gestão de partições horizontais do [biblioteca de cliente da base de dados elástica (EDCL)](sql-database-elastic-database-client-library.md). A EDCL está disponível em Java e .NET Framework. A EDCL permite que uma aplicação criar, gerir e utilizar um mapa de partições horizontais de base de dados. 

Um mapa de partições horizontais contém uma lista de partições horizontais (bases de dados) e o mapeamento entre chaves (inquilinos) e as partições horizontais. Funções EDCL são usadas durante o aprovisionamento para criar as entradas no mapa de partições horizontais do inquilino. Eles são usados no tempo de execução por aplicações para ligar à base de dados correto. EDCL armazena em cache as informações de ligação para minimizar o tráfego para a base de dados do catálogo e acelerar o aplicativo. 

> [!IMPORTANT]
> Os dados de mapeamento estão acessíveis na base de dados do catálogo, mas *não editá-lo*. Edite dados de mapeamento através de APIs de biblioteca de cliente da base de dados elásticas apenas. Manipular diretamente os dados de mapeamento arrisca-danificar o catálogo e não é suportada.


## <a name="introduction-to-the-saas-provisioning-pattern"></a>Introdução ao padrão de aprovisionamento de SaaS

Quando adiciona um novo inquilino numa aplicação SaaS que utiliza um modelo de base de dados de inquilino único, terá de aprovisionar uma nova base de dados do inquilino. A base de dados tem de ser criada no local apropriado e escalão de serviço. Ele também tem de ser inicializado com o esquema apropriada e os dados de referência. E têm de estar registado no catálogo sob a chave de inquilino adequado. 

Abordagens diferentes para a base de dados de aprovisionamento podem ser utilizadas. Pode executar scripts SQL, implementar um bacpac ou copiar uma base de dados do modelo. 

Base de dados de aprovisionamento tem de ser parte da sua estratégia de gestão de esquema. Deve certificar-se de que as novas bases de dados são aprovisionados com o esquema mais recente. Este requisito é explorado a [tutorial de gestão de esquema](saas-tenancy-schema-management.md). 

A aplicação de base de dados por inquilino de bilhetes Wingtip Aprovisiona os inquilinos novos ao copiar uma base de dados do modelo com o nome _basetenantdb_, que é implementado no servidor de catálogo. Aprovisionamento pode ser integrado na aplicação como parte de uma experiência de inscrição. Ele também pode ter suporte offline com scripts. Este tutorial explora o aprovisionamento com o PowerShell. 

Aprovisionamento de scripts de cópia a _basetenantdb_ para criar uma nova base de dados do inquilino num conjunto elástico da base de dados. A base de dados do inquilino é criado no servidor de inquilinos mapeado para o _newtenant_ alias de DNS. Este alias mantém uma referência para o servidor utilizado para aprovisionar novos inquilinos e é atualizado para apontar para um servidor de inquilino de recuperação nos tutoriais de recuperação após desastre ([DR através de georestore](saas-dbpertenant-dr-geo-restore.md), [DR usando a georreplicação](saas-dbpertenant-dr-geo-replication.md)). Os scripts, em seguida, Inicialize a base de dados com informações específicas do inquilino e registá-la no mapa de partições horizontais de catálogo. Bases de dados de inquilino são nomes com base no nome do inquilino. Este esquema de nomeação não é uma parte crítica do padrão. O catálogo mapeia a chave de inquilino para o nome de base de dados, pelo que pode ser utilizada qualquer convenção de nomenclatura. 


## <a name="get-the-wingtip-tickets-saas-database-per-tenant-application-scripts"></a>Obter os scripts de base de dados por inquilino aplicação Wingtip Tickets SaaS

O código de origem do aplicativo e scripts de Wingtip Tickets SaaS estão disponíveis no [WingtipTicketsSaaS DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) repositório do GitHub. Veja a [orientações gerais](saas-tenancy-wingtip-app-guidance-tips.md) para obter os passos transferir e os scripts de Wingtip Tickets SaaS de desbloqueio.


## <a name="provision-and-catalog-detailed-walkthrough"></a>Instruções detalhadas sobre o aprovisionamento e a catalogação

Para compreender como a aplicação Wingtip Tickets implementa o aprovisionamento do novo inquilino, adicione um ponto de interrupção e siga o fluxo de trabalho, enquanto a aprovisionar um inquilino.

1. No ISE do PowerShell, abra... \\Módulos de aprendizagem\\ProvisionAndCatalog\\_Demo-ProvisionAndCatalog.ps1_ e defina os seguintes parâmetros:

   * **$TenantName** = o nome do novo local (por exemplo, *Bushwillow Blues*).
   * **$VenueType** = um dos tipos predefinidos de local: _blues, música clássica, dança, jazz, judo, motor de motos, multipurpose, opera, música Rock, futebol_.
   * **$DemoScenario** = **1**, *aprovisionar um inquilino individual*.

2. Para adicionar um ponto de interrupção, coloque o cursor em qualquer parte na linha que diz *New-Tenant '*. Em seguida, pressione F9.

   ![Ponto de interrupção](media/saas-dbpertenant-provision-and-catalog/breakpoint.png)

3. Para executar o script, pressione F5.

4. Depois de parar a execução do script no ponto de interrupção, prima F11 examinar o código.

   ![Depurar](media/saas-dbpertenant-provision-and-catalog/debug.png)



A execução do script de rastreio utilizando a **depurar** opções de menu. Pressione F10 e F11 para seguir através de ou para as funções chamadas. Para obter mais informações sobre a depuração de scripts do PowerShell, consulte [sugestões sobre como trabalhar e depurar scripts do PowerShell](https://msdn.microsoft.com/powershell/scripting/core-powershell/ise/how-to-debug-scripts-in-windows-powershell-ise).


Não tem de seguir explicitamente esse fluxo de trabalho. Ele explica como depurar o script.

* **Importe o módulo de CatalogAndDatabaseManagement.psm1.** Ele fornece um catálogo e uma abstração do nível de inquilino através do [gestão de partições horizontais](sql-database-elastic-scale-shard-map-management.md) funções. Este módulo encapsula a maior parte do padrão de catalogação e vale a pena explorar.
* **Importe o módulo de SubscriptionManagement.psm1.** Ela contém funções para iniciar sessão no Azure e selecionar a subscrição do Azure que pretende trabalhar.
* **Obter os detalhes de configuração.** Avance para Get-configuração utilizando F11 e ver como a configuração da aplicação é especificada. Os nomes de recursos e outros valores específicos da aplicação são definidos aqui. Não altere estes valores até estar familiarizado com os scripts.
* **Obtenha o objeto de catálogo.** Avance para o Get-Catalog, que compõe e retorna um objeto de catálogo que é utilizado no script de nível superior. Esta função utiliza funções de gestão de partições horizontais que são importadas a partir **AzureShardManagement.psm1**. O objeto de catálogo é composto pelos seguintes elementos:

   * $catalogServerFullyQualifiedName é construído ao utilizar o padrão e o nome de utilizador: _catalog -\<usuário\>. database.windows .net_.
   * $catalogDatabaseName é obtido a partir da configuração: *tenantcatalog*.
   * O objeto $shardMapManager é inicializado a partir da base de dados do catálogo.
   * O objeto $shardMap é inicializado a partir do mapa de partições horizontais _tenantcatalog_ na base de dados do catálogo. Um objeto de catálogo é composto por e retornado. É utilizado no script de nível superior.
* **Calcule a nova chave de inquilino.** É utilizada uma função hash para criar a chave de inquilino a partir do nome do inquilino.
* **Verifique se a chave de inquilino já existe.** O catálogo é verificado para certificar-se de que a chave está disponível.
* **A base de dados do inquilino é aprovisionada com New-TenantDatabase**. Utilizar F11 para examinar a forma como a base de dados é aprovisionada utilizando um [modelo Azure Resource Manager](../azure-resource-manager/resource-manager-template-walkthrough.md).

    O nome de base de dados é construído a partir do nome de inquilino para ficar claro quais as partições horizontais que pertencem a que inquilino. Também pode utilizar outras convenções de nomenclatura de base de dados. Um modelo do Resource Manager cria uma base de dados de inquilinos ao copiar uma base de dados do modelo (_baseTenantDB_) no servidor de catálogo. Como alternativa, pode criar uma base de dados e inicializá-la ao importar um bacpac. Ou pode executar um script de inicialização a partir de um local conhecido.

    O modelo do Resource Manager está na pasta Modules\Common\ ...\Learning: *tenantdatabasecopytemplate. JSON*

* **A base de dados do inquilino é inicializada.** O nome de local (inquilino) e o tipo de local são adicionados. Também pode fazer outra inicialização aqui.

* **A base de dados de inquilino está registado no catálogo.** Está registado com *Add-TenantDatabaseToCatalog* utilizando a chave de inquilino. Utilize F11 para passar para os detalhes:

    * A base de dados do catálogo é adicionada ao mapa de partições horizontais (a lista de bases de dados conhecidas).
    * É criado o mapeamento que liga o valor da chave à partição horizontal.
    * Metadados adicionais sobre o inquilino (nome do local) é adicionado à tabela de inquilinos no catálogo. A tabela de inquilinos não faz parte do esquema de gestão de partições horizontais, e ele não está instalado pela EDCL. Esta tabela ilustra como a base de dados do catálogo pode ser estendido para oferecer suporte a dados adicionais de específico do aplicativo.


Depois de concluir o aprovisionamento, execução devolve o original *Demo-ProvisionAndCatalog* script. O **eventos** é aberta a página para o novo inquilino no browser.

   ![Página de eventos](media/saas-dbpertenant-provision-and-catalog/new-tenant.png)


## <a name="provision-a-batch-of-tenants"></a>Aprovisionar um lote de inquilinos

Este exercício aprovisiona um lote de 17 inquilinos. Recomendamos que Aprovisiona este lote de inquilinos antes de iniciar outros tutoriais de base de dados por inquilino Wingtip Tickets SaaS. Há mais do que apenas algumas bases de dados para trabalhar com.

1. No ISE do PowerShell, abra... \\Módulos de aprendizagem\\ProvisionAndCatalog\\*Demo-ProvisionAndCatalog.ps1*. Alteração da *$DemoScenario* parâmetro 3:

   * **$DemoScenario** = **3**, *aprovisionar um lote de inquilinos*.
2. Para executar o script, pressione F5.

O script implementa um lote de inquilinos adicionais. Ele usa um [modelo Azure Resource Manager](../azure-resource-manager/resource-manager-template-walkthrough.md) que controla o lote e delega o aprovisionamento de cada base de dados a um modelo ligado. Utilizar modelos desta forma permite que o Azure Resource Manager funcione como o mediador no processo de aprovisionamento do script. Os modelos de aprovisionam bases de dados em paralelo e identificador de repetições, se necessário. O script é idempotent, portanto, se ele falha ou parar por qualquer motivo, execute-o novamente.

### <a name="verify-the-batch-of-tenants-that-successfully-deployed"></a>Verificar o lote de inquilinos implementados com êxito

* Na [portal do Azure](https://portal.azure.com)e navegue até à sua lista de servidores e abra o *tenants1* server. Selecione **bases de dados SQL**e certifique-se de que o lote de 17 bases de dados adicionais está agora na lista.

   ![Lista de bases de dados](media/saas-dbpertenant-provision-and-catalog/database-list.png)



## <a name="other-provisioning-patterns"></a>Outros padrões de aprovisionamento

Outros padrões de aprovisionamento não incluídos neste tutorial:

**Pré-aprovisionamento de bases de dados**: O padrão de pré-aprovisionamento explora o facto das bases de dados num conjunto elástico não adicionam custos adicionais. A faturação é para o conjunto elástico, não as bases de dados. Bases de dados Inativas não consumirem recursos. Pelos pré-aprovisionamento bases de dados num conjunto e alocá-las, quando necessário, pode reduzir o tempo para adicionar os inquilinos. O número de bases de dados previamente aprovisionadas pode ser ajustado conforme necessário para manter uma memória intermédia adequada para a taxa de aprovisionamento previsível.

**Aprovisionamento automático**: O padrão de aprovisionamento automático, um serviço de aprovisionamento Aprovisiona servidores, conjuntos e bases de dados automaticamente, conforme necessário. Se desejar, pode incluir o pré-aprovisionamento de bases de dados nos conjuntos elásticos. Se as bases de dados são descomissionados e eliminadas, os espaços nos conjuntos elásticos podem ser preenchidos pelo serviço de aprovisionamento. Esse serviço pode ser simples ou complexos, como a manipulação de aprovisionamento em vários locais geográficos e configurar a georreplicação para recuperação após desastre. 

Com o padrão de aprovisionamento automático, uma aplicação cliente ou script submete um pedido de aprovisionamento a uma fila para serem processados pelo serviço de aprovisionamento. Em seguida, consulta o serviço para determinar a conclusão. Se for utilizado a pré-aprovisionamento, os pedidos são processados rapidamente. O serviço aprovisiona um banco de dados de substituição em segundo plano.


## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> 
> * Aprovisione um inquilino individual novo.
> * Aprovisione um lote de inquilinos adicionais.
> * Passar para os detalhes de aprovisionamento de inquilinos e como registá-los para o catálogo.

Experimente o [tutorial de monitorização do desempenho](saas-dbpertenant-performance-monitoring.md).

## <a name="additional-resources"></a>Recursos adicionais

* Adicionais [tutoriais que criar na aplicação Wingtip Tickets SaaS da base de dados por inquilino](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* [Biblioteca de clientes da base de dados elástica](sql-database-elastic-database-client-library.md)
* [Depurar scripts no ISE do Windows PowerShell](https://msdn.microsoft.com/powershell/scripting/core-powershell/ise/how-to-debug-scripts-in-windows-powershell-ise)
