---
title: Provisionar novos locatários em um aplicativo multilocatário que usa o banco de dados SQL do Azure | Microsoft Docs
description: Saiba como provisionar e catalogar novos locatários em um aplicativo SaaS multilocatário do banco de dados SQL do Azure
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 09/24/2018
ms.openlocfilehash: b5a996fe6be5aa839b78b6693accac9b1000cef8
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68570427"
---
# <a name="learn-how-to-provision-new-tenants-and-register-them-in-the-catalog"></a>Saiba como provisionar novos locatários e registrá-los no catálogo

Neste tutorial, você aprenderá a provisionar e catalogar padrões de SaaS. Você também aprende como eles são implementados no aplicativo de banco de dados por locatário SaaS Wingtip tickets. Você cria e inicializa novos bancos de dados de locatário e os registra no catálogo de locatários do aplicativo. O catálogo é um banco de dados que mantém o mapeamento entre os vários locatários do aplicativo SaaS e seus dados. O catálogo desempenha um papel importante no direcionamento de solicitações de aplicativo e gerenciamento para o banco de dados correto.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> 
> * Provisionar um único locatário novo.
> * Provisione um lote de locatários adicionais.


Para concluir este tutorial, confirme que conclui os pré-requisitos seguintes:

* O aplicativo de banco de dados por locatário SaaS Wingtip tickets é implantado. Para implantá-lo em menos de cinco minutos, consulte [implantar e explorar o aplicativo de banco de dados por locatário SaaS Wingtip tickets](saas-dbpertenant-get-started-deploy.md).
* O Azure PowerShell está instalado. Para obter mais informações, veja [Introdução ao Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps).

## <a name="introduction-to-the-saas-catalog-pattern"></a>Introdução ao padrão de catálogo de SaaS

Em um aplicativo SaaS multilocatário com suporte de banco de dados, é importante saber onde as informações de cada locatário são armazenadas. No padrão de catálogo de SaaS, um banco de dados de catálogo é usado para manter o mapeamento entre cada locatário e o banco de dados no qual os seus dados são armazenados. Esse padrão se aplica sempre que os dados de locatário são distribuídos entre vários bancos.

Cada locatário é identificado por uma chave no catálogo, que é mapeada para o local de seu banco de dados. No aplicativo Wingtip tickets, a chave é formada por um hash do nome do locatário. Esse esquema permite que o aplicativo Construa a chave a partir do nome do locatário incluído na URL do aplicativo. Outros esquemas de chave de locatário podem ser usados.  

O catálogo permite que o nome ou o local do banco de dados seja alterado com impacto mínimo sobre o aplicativo. Em um modelo de banco de dados multilocatário, esse recurso também acomoda a movimentação de um locatário entre bancos de dados. O catálogo também pode ser usado para indicar se um locatário ou banco de dados está offline para manutenção ou outras ações. Esse recurso é explorado no [tutorial de restauração de locatário único](saas-dbpertenant-restore-single-tenant.md).

O catálogo também pode armazenar metadados adicionais de locatário ou banco de dados, como a versão do esquema, o plano de serviço ou os SLAs oferecidos aos locatários. O catálogo pode armazenar outras informações que habilitam o gerenciamento de aplicativos, atendimento ao cliente ou DevOps. 

Além do aplicativo SaaS, o catálogo pode habilitar as ferramentas de banco de dados. No exemplo de banco de dados por locatário SaaS Wingtip tickets, o catálogo é usado para habilitar a consulta entre locatários, que é explorada no [tutorial de relatório ad hoc](saas-tenancy-cross-tenant-reporting.md). O gerenciamento de trabalhos entre bancos de dados é explorado nos tutoriais de [Gerenciamento de esquema](saas-tenancy-schema-management.md) e [análise de locatário](saas-tenancy-tenant-analytics.md) . 

Nos exemplos de SaaS do Wingtip tickets, o catálogo é implementado usando os recursos de gerenciamento de fragmentos da [biblioteca de cliente do banco de dados elástico (EDCL)](sql-database-elastic-database-client-library.md). O EDCL está disponível em Java e na .NET Framework. O EDCL permite que um aplicativo crie, gerencie e use um mapa de fragmentos com backup de banco de dados. 

Um mapa de fragmentos contém uma lista de fragmentos (bancos de dados) e o mapeamento entre chaves (locatários) e fragmentos. As funções EDCL são usadas durante o provisionamento de locatário para criar as entradas no mapa de fragmentos. Eles são usados em tempo de execução por aplicativos para se conectarem ao banco de dados correto. O EDCL armazena em cache as informações de conexão para minimizar o tráfego para o banco de dados de catálogo e acelerar o aplicativo. 

> [!IMPORTANT]
> Os dados de mapeamento podem ser acessados no banco de dado do catálogo, mas *não editá-los*. Edite dados de mapeamento usando somente APIs de biblioteca de cliente do banco de dados elástico. Manipular diretamente o mapeamento de riscos de dados que corrompem o catálogo e não tem suporte.


## <a name="introduction-to-the-saas-provisioning-pattern"></a>Introdução ao padrão de provisionamento de SaaS

Quando você adiciona um novo locatário em um aplicativo SaaS que usa um modelo de banco de dados de locatário único, você deve provisionar um novo banco de dados de locatário. O banco de dados deve ser criado no local e na camada de serviço apropriados. Ele também deve ser inicializado com o esquema e os dados de referência apropriados. E deve ser registrado no catálogo sob a chave de locatário apropriada. 

Abordagens diferentes para o provisionamento de banco de dados podem ser usadas. Você pode executar scripts SQL, implantar um bacpac ou copiar um banco de dados de modelo. 

O provisionamento de banco de dados precisa fazer parte de sua estratégia de gerenciamento de esquema. Você deve certificar-se de que os novos bancos de dados sejam provisionados com o esquema mais recente. Esse requisito é explorado no [tutorial de gerenciamento de esquema](saas-tenancy-schema-management.md). 

O aplicativo de banco de dados por locatário Wingtip tickets provisiona novos locatários copiando um banco de dados de modelo chamado _basetenantdb_, que é implantado no servidor de catálogo. O provisionamento pode ser integrado ao aplicativo como parte de uma experiência de inscrição. Ele também pode ter suporte offline usando scripts. Este tutorial explora o provisionamento usando o PowerShell. 

Os scripts de provisionamento copiam o banco de dados _basetenantdb_ para criar um novo banco de dados de locatário em um pool elástico. O banco de dados de locatário é criado no servidor de locatário mapeado para o alias DNS _Tenente_ . Esse alias mantém uma referência ao servidor usado para provisionar novos locatários e é atualizado para apontar para um servidor de locatário de recuperação nos tutoriais de recuperação de desastres ([Dr usando](saas-dbpertenant-dr-geo-restore.md)georestore, [Dr usando a replicação](saas-dbpertenant-dr-geo-replication.md)). Em seguida, os scripts inicializam o banco de dados com informações específicas do locatário e o registram no mapa do fragmento do catálogo. Os bancos de dados de locatário recebem nomes com base no nome do locatário. Esse esquema de nomenclatura não é uma parte crítica do padrão. O catálogo mapeia a chave de locatário para o nome do banco de dados, portanto, qualquer Convenção de nomenclatura pode ser usada. 


## <a name="get-the-wingtip-tickets-saas-database-per-tenant-application-scripts"></a>Obter os scripts de aplicativo de banco de dados por locatário SaaS Wingtip tickets

Os scripts do Wingtip tickets SaaS e o código-fonte do aplicativo estão disponíveis no repositório GitHub [repositório wingtipticketssaas-DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) . Confira as [diretrizes gerais](saas-tenancy-wingtip-app-guidance-tips.md) para obter as etapas para baixar e desbloquear os scripts SaaS do Wingtip tickets.


## <a name="provision-and-catalog-detailed-walkthrough"></a>Instruções detalhadas sobre o aprovisionamento e a catalogação

Para entender como o aplicativo Wingtip tickets implementa o novo provisionamento de locatário, adicione um ponto de interrupção e siga o fluxo de trabalho enquanto você provisiona um locatário.

1. No ISE do PowerShell, abra... Módulos de aprendizadoProvisionAndCatalog\\_demo-provisionandcatalog. ps1_ e definem os seguintes parâmetros:\\ \\

   * **$TenantName** = o nome do novo local (por exemplo, *Bushwillow Blues*).
   * **$VenueType** = um dos tipos de local predefinidos: _azuis, ClassicalMusic, dança, jazz, judo, a corrida do motor, multipropósito, Opera, ROCKMUSIC, futebol_.
   * $DemoScenario = **1**, *provisionar um único locatário*.

2. Para adicionar um ponto de interrupção, coloque o cursor em qualquer lugar na linha que diz *New-locatário '* . Em seguida, pressione F9.

   ![Breakpoint](media/saas-dbpertenant-provision-and-catalog/breakpoint.png)

3. Para executar o script, pressione F5.

4. Após a execução do script parar no ponto de interrupção, pressione F11 para entrar no código.

   ![A depurar](media/saas-dbpertenant-provision-and-catalog/debug.png)



Rastreie a execução do script usando as opções do menu **depurar** . Pressione F10 e F11 para passar sobre ou para as funções chamadas. Para obter mais informações sobre como depurar scripts do PowerShell, consulte [dicas sobre como trabalhar com e depurar scripts do PowerShell](https://msdn.microsoft.com/powershell/scripting/core-powershell/ise/how-to-debug-scripts-in-windows-powershell-ise).


Você não precisa seguir este fluxo de trabalho explicitamente. Ele explica como depurar o script.

* **Importe o módulo CatalogAndDatabaseManagement. psm1.** Ele fornece uma abstração de nível de locatário e de catálogo sobre as funções de [Gerenciamento de fragmentos](sql-database-elastic-scale-shard-map-management.md) . Esse módulo encapsula grande parte do padrão de catálogo e vale a pena explorar.
* **Importe o módulo SubscriptionManagement. psm1.** Ele contém funções para entrar no Azure e selecionar a assinatura do Azure com a qual você deseja trabalhar.
* **Obter detalhes de configuração.** Passe para Get-Configuration usando F11 e veja como a configuração do aplicativo é especificada. Os nomes de recursos e outros valores específicos do aplicativo são definidos aqui. Não altere esses valores até que você esteja familiarizado com os scripts.
* **Obter o objeto de catálogo.** Passe para o Get-Catalog, que compõe e retorna um objeto de catálogo que é usado no script de nível superior. Essa função usa funções de gerenciamento de fragmentos que são importadas de **AzureShardManagement. psm1**. O objeto de catálogo é composto pelos seguintes elementos:

   * $catalogServerFullyQualifiedName é construído usando o tronco padrão mais seu nome de usuário: _Catalog-\<user\>. Database. Windows .net_.
   * $catalogDatabaseName é obtido a partir da configuração: *tenantcatalog*.
   * O objeto $shardMapManager é inicializado a partir da base de dados do catálogo.
   * O objeto $shardMap é inicializado a partir do mapa de partições horizontais _tenantcatalog_ na base de dados do catálogo. Um objeto de catálogo é composto e retornado. Ele é usado no script de nível superior.
* **Calcule a nova chave de locatário.** É utilizada uma função hash para criar a chave de inquilino a partir do nome do inquilino.
* **Verifique se a chave de locatário já existe.** O catálogo é verificado para verificar se a chave está disponível.
* **A base de dados do inquilino é aprovisionada com New-TenantDatabase**. Use F11 para entrar em como o banco de dados é provisionado usando um [modelo de Azure Resource Manager](../azure-resource-manager/resource-manager-template-walkthrough.md).

    O nome de base de dados é construído a partir do nome de inquilino para ficar claro quais as partições horizontais que pertencem a que inquilino. Você também pode usar outras convenções de nomenclatura de banco de dados. Um modelo do Resource Manager cria um banco de dados de locatário copiando um banco de dados de modelo (_baseTenantDB_) no servidor de catálogo. Como alternativa, você pode criar um banco de dados e inicializá-lo importando um bacpac. Ou você pode executar um script de inicialização de um local bem conhecido.

    O modelo do Resource Manager está na pasta. ..\Learning Modules\Common\: *tenantdatabasecopytemplate. JSON*

* **O banco de dados de locatário é inicializado ainda mais.** O nome do local (locatário) e o tipo de local são adicionados. Você também pode fazer outra inicialização aqui.

* **O banco de dados de locatário está registrado no catálogo.** Ele é registrado com *Add-TenantDatabaseToCatalog* usando a chave de locatário. Use F11 para entrar nos detalhes:

    * A base de dados do catálogo é adicionada ao mapa de partições horizontais (a lista de bases de dados conhecidas).
    * É criado o mapeamento que liga o valor da chave à partição horizontal.
    * Metadados adicionais sobre o locatário (o nome do local) são adicionados à tabela locatários no catálogo. A tabela locatários não faz parte do esquema de gerenciamento de fragmentos e não é instalada pelo EDCL. Esta tabela ilustra como o banco de dados do catálogo pode ser estendido para dar suporte a dados específicos do aplicativo adicionais.


Após a conclusão do provisionamento, a execução retorna ao script original *demo-ProvisionAndCatalog* . A página **eventos** é aberta para o novo locatário no navegador.

   ![Página de eventos](media/saas-dbpertenant-provision-and-catalog/new-tenant.png)


## <a name="provision-a-batch-of-tenants"></a>Provisionar um lote de locatários

Este exercício provisiona um lote de 17 locatários. Recomendamos que você provisione esse lote de locatários antes de iniciar outros tutoriais de banco de dados por locatário do Wingtip tickets SaaS. Há mais do que apenas alguns bancos de dados com os quais trabalhar.

1. No ISE do PowerShell, abra... Módulos de aprendizadoProvisionAndCatalog\\*demo-provisionandcatalog. ps1.* \\\\ Altere o parâmetro *$DemoScenario* para 3:

   * $DemoScenario = **3**, *provisionar um lote de locatários*.
2. Para executar o script, pressione F5.

O script implementa um lote de inquilinos adicionais. Ele usa um [modelo de Azure Resource Manager](../azure-resource-manager/resource-manager-template-walkthrough.md) que controla o lote e delega o provisionamento de cada banco de dados para um modelo vinculado. Utilizar modelos desta forma permite que o Azure Resource Manager funcione como o mediador no processo de aprovisionamento do script. Os modelos provisionam bancos de dados em paralelo e tratam de novas tentativas, se necessário. O script é idempotente, portanto, se ele falhar ou parar por qualquer motivo, execute-o novamente.

### <a name="verify-the-batch-of-tenants-that-successfully-deployed"></a>Verificar o lote de locatários que foram implantados com êxito

* Na [portal do Azure](https://portal.azure.com), navegue até a lista de servidores e abra o servidor *tenants1* . Selecione **bancos de dados SQL**e verifique se o lote de 17 bancos de dados adicionais está agora na lista.

   ![Lista de bases de dados](media/saas-dbpertenant-provision-and-catalog/database-list.png)



## <a name="other-provisioning-patterns"></a>Outros padrões de aprovisionamento

Outros padrões de provisionamento não incluídos neste tutorial:

**Pré-Provisionando bancos de dados**: O padrão de pré-provisionamento explora o fato de que os bancos de dados em um pool elástico não agregam custo extra. A cobrança é para o pool elástico, não para os bancos de dados. Bancos de dados ociosos não consomem recursos. Ao provisionar previamente bancos de dados em um pool e alocá-los quando necessário, você pode reduzir o tempo para adicionar locatários. O número de bancos de dados previamente provisionados pode ser ajustado conforme necessário para manter um buffer adequado para a taxa de provisionamento prevista.

**Provisionamento automático**: No padrão de provisionamento automático, um serviço de provisionamento provisiona servidores, pools e bancos de dados automaticamente, conforme necessário. Se desejar, você pode incluir o pré-provisionamento de bancos de dados em pools elásticos. Se os bancos de dados forem encerrados e excluídos, as lacunas nos pools elásticos poderão ser preenchidas pelo serviço de provisionamento. Esse serviço pode ser simples ou complexo, como manipular o provisionamento em várias regiões geográficas e configurar a replicação geográfica para recuperação de desastres. 

Com o padrão de provisionamento automático, um script ou aplicativo cliente envia uma solicitação de provisionamento para uma fila a ser processada pelo serviço de provisionamento. Em seguida, ele sonda o serviço para determinar a conclusão. Se o provisionamento prévio for usado, as solicitações serão tratadas rapidamente. O serviço provisiona um banco de dados de substituição em segundo plano.


## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> 
> * Provisionar um único locatário novo.
> * Provisione um lote de locatários adicionais.
> * Concorra os detalhes de provisionamento de locatários e registro deles no catálogo.

Experimente o [tutorial de monitoramento de desempenho](saas-dbpertenant-performance-monitoring.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Tutoriais adicionais que se baseiam no aplicativo de banco de dados por locatário SaaS Wingtip tickets](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* [Biblioteca de clientes da base de dados elástica](sql-database-elastic-database-client-library.md)
* [Depurar scripts no ISE do Windows PowerShell](https://msdn.microsoft.com/powershell/scripting/core-powershell/ise/how-to-debug-scripts-in-windows-powershell-ise)
