---
title: Provisiona novos inquilinos numa app multitenante
description: Saiba como providenciar e catalogar novos inquilinos numa aplicação SaaS multitenante Azure SQL Database
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: seo-lt-2019, sqldbrb=1
ms.devlang: ''
ms.topic: tutorial
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 09/24/2018
ms.openlocfilehash: 26add03929551c912b4d7b7cf10741d53333689a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "92780568"
---
# <a name="learn-how-to-provision-new-tenants-and-register-them-in-the-catalog"></a>Saiba como providenciar novos inquilinos e registá-los no catálogo
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Neste tutorial, você aprende a provisões e catalogar padrões SaaS. Você também aprende como eles são implementados na aplicação de base de dados SaaS de bilhetes wingtip saas por inquilino. Cria e inicializa novas bases de dados de inquilinos e regista-as no catálogo de inquilinos da aplicação. O catálogo é uma base de dados que mantém o mapeamento entre os muitos inquilinos da aplicação SaaS e os seus dados. O catálogo desempenha um papel importante na direção de pedidos de aplicação e gestão para a base de dados correta.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
>
> * Providenciar um único novo inquilino.
> * Providenciar um lote de inquilinos adicionais.


Para concluir este tutorial, devem ser cumpridos os seguintes pré-requisitos:

* A aplicação Wingtip Tickets SaaS para o arrendamento. Para implantá-lo em menos de cinco minutos, consulte [implementar e explorar a aplicação de base de dados SaaS por insusição.](./saas-dbpertenant-get-started-deploy.md)
* O Azure PowerShell está instalado. Para obter mais informações, veja [Introdução ao Azure PowerShell](/powershell/azure/get-started-azureps).

## <a name="introduction-to-the-saas-catalog-pattern"></a>Introdução ao padrão de catálogo SaaS

Numa aplicação SaaS multitenante apoiada por bases de dados, é importante saber onde a informação para cada inquilino é armazenada. No padrão de catálogo saaS, uma base de dados de catálogo é usada para manter o mapeamento entre cada inquilino e a base de dados em que os seus dados são armazenados. Este padrão aplica-se sempre que os dados dos inquilinos são distribuídos por várias bases de dados.

Cada inquilino é identificado por uma chave no catálogo, que está mapeada para a localização da sua base de dados. Na aplicação Wingtip Tickets, a chave é formada a partir de um haxixe do nome do inquilino. Este esquema permite que a app construa a chave a partir do nome do inquilino incluído no URL da aplicação. Outros esquemas chave do inquilino podem ser usados.

O catálogo permite que o nome ou localização da base de dados seja alterado com o mínimo impacto na aplicação. Num modelo de base de dados multitenante, esta capacidade também acomoda a deslocação de um inquilino entre bases de dados. O catálogo também pode ser usado para indicar se um inquilino ou base de dados está offline para manutenção ou outras ações. Esta capacidade é explorada no tutorial de [inquilino único restaurar.](saas-dbpertenant-restore-single-tenant.md)

O catálogo também pode armazenar metadados adicionais de inquilinos ou bases de dados, tais como a versão de schema, plano de serviço ou SLAs oferecidos aos inquilinos. O catálogo pode armazenar outras informações que permitam a gestão de aplicações, suporte ao cliente ou DevOps.

Além da aplicação SaaS, o catálogo pode ativar ferramentas de base de dados. Na amostra de base de dados SaaS por inquilino, o catálogo é utilizado para permitir consultas de inquilinos cruzados, que é explorado no tutorial de [reportagem ad hoc.](saas-tenancy-cross-tenant-reporting.md) A gestão de empregos cross-database é explorada nos tutoriais de [gestão de Schema](saas-tenancy-schema-management.md) e [de analítica do Inquilino.](saas-tenancy-tenant-analytics.md)

Nas amostras SaaS de Ingressos Wingtip, o catálogo é implementado utilizando as funcionalidades de Gestão de Fragmentos da biblioteca de [clientes Elastic Database (EDCL).](elastic-database-client-library.md) O EDCL está disponível em Java e no Quadro .NET. O EDCL permite uma aplicação para criar, gerir e usar um mapa de fragmentos apoiado por bases de dados.

Um mapa de fragmentos contém uma lista de fragmentos (bases de dados) e o mapeamento entre chaves (inquilinos) e fragmentos. As funções EDCL são utilizadas durante o provisionamento do arrendatário para criar as entradas no mapa de fragmentos. São usados no tempo de execução por aplicações para ligar à base de dados correta. A EDCL caches informações de ligação para minimizar o tráfego na base de dados do catálogo e acelerar a aplicação.

> [!IMPORTANT]
> Os dados de mapeamento estão acessíveis na base de dados do catálogo, mas *não os edite.* Editar dados de mapeamento utilizando apenas APIs da Biblioteca de Clientes de Base de Dados Elástica. Manipular diretamente os dados de mapeamento corre o risco de corromper o catálogo e não é suportado.


## <a name="introduction-to-the-saas-provisioning-pattern"></a>Introdução ao padrão de provisionamento de SaaS

Quando você adicionar um novo inquilino em um aplicativo SaaS que usa um modelo de base de dados de inquilino único, você deve providenciar uma nova base de dados de inquilinos. A base de dados deve ser criada no nível de localização e serviço adequados. Deve também ser inicializado com os dados de esquema e de referência adequados. E deve estar registado no catálogo sob a chave do inquilino apropriado.

Podem ser utilizadas diferentes abordagens ao fornecimento de bases de dados. Pode executar scripts SQL, implementar um bacpac ou copiar uma base de dados de modelos.

O fornecimento de bases de dados tem de fazer parte da sua estratégia de gestão de esquemas. Tem de se certificar de que as novas bases de dados são adsejadas com o esquema mais recente. Esta exigência é explorada no tutorial de [gestão de Schema.](saas-tenancy-schema-management.md)

A base de dados wingtip tickets-per-tenant app fornece novos inquilinos copiando uma base de dados de modelos chamada _basetenantdb_, que é implantada no servidor de catálogo. O provisionamento pode ser integrado na aplicação como parte de uma experiência de inscrição. Também pode ser suportado offline usando scripts. Este tutorial explora o fornecimento utilizando o PowerShell.

Os scripts de provisionamento copiam a base de dados _basetenantdb_ para criar uma nova base de dados de inquilinos numa piscina elástica. A base de dados do inquilino é criada no servidor do inquilino mapeado para o _novo_ pseudónimo DNS. Este pseudónimo mantém uma referência ao servidor utilizado para a disponibilização de novos inquilinos e é atualizado para apontar para um servidor de inquilino de recuperação nos tutoriais de recuperação de desastres[(DR usando georestore,](./saas-dbpertenant-dr-geo-restore.md) [DR usando georeplicação).](./saas-dbpertenant-dr-geo-replication.md) Os scripts rubricam então a base de dados com informações específicas do inquilino e registam-na no mapa de fragmentos do catálogo. As bases de dados dos inquilinos são dadas nomes com base no nome do inquilino. Este esquema de nomeação não é uma parte crítica do padrão. O catálogo mapeia a chave do inquilino para o nome da base de dados, para que qualquer convenção de nomeação possa ser usada.


## <a name="get-the-wingtip-tickets-saas-database-per-tenant-application-scripts"></a>Obtenha os scripts de aplicação de aplicações de ingressos saas de ponta da ponta da asa

Os scripts de Bilhetes Wingtip SaaS e código fonte de aplicação estão disponíveis no [WingtipTicketsSaS-DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) GitHub repo. Confira as [orientações gerais](saas-tenancy-wingtip-app-guidance-tips.md) para as etapas para descarregar e desbloquear os scripts SaaS dos Bilhetes Wingtip.


## <a name="provision-and-catalog-detailed-walkthrough"></a>Instruções detalhadas sobre o aprovisionamento e a catalogação

Para entender como a aplicação de Ingressos Wingtip implementa o novo provisionamento de inquilinos, adicione um breakpoint e siga o fluxo de trabalho enquanto fornece um inquilino.

1. No PowerShell ISE, aberto... \\ Módulos de Aprendizagem \\ ProvisionAndCatalog \\ _Demo-ProvisionAndCatalog.ps1_ e definir os seguintes parâmetros:

   * **$TenantName** = o nome do novo local (por exemplo, *Bushwillow Blues*).
   * **$VenueType** = um dos locais predefinidos: _blues, classicmusic, dance, jazz, judo, automobilismo, multiusos, ópera, rockmusic, futebol._
   * **$DemoScenario**  =  **1**, *Provision a um único inquilino.*

2. Para adicionar um breakpoint, coloque o seu cursor em qualquer lugar da linha que diz *New-Tenant ' .* Em seguida, pressione F9.

   ![A screenshot mostra um script com New-Tenant destacado para adicionar um breakpoint.](./media/saas-dbpertenant-provision-and-catalog/breakpoint.png)

3. Para executar o guião, prima F5.

4. Depois de a execução do guião parar no ponto de rutura, prima F11 para entrar no código.

   ![Depurar](./media/saas-dbpertenant-provision-and-catalog/debug.png)



Trace a execução do script usando as opções de menu **Debug.** Pressione F10 e F11 para passar por cima ou para as funções chamadas. Para obter mais informações sobre a depuragem dos scripts PowerShell, consulte [dicas sobre trabalhar e depurar scripts PowerShell](/powershell/scripting/components/ise/how-to-debug-scripts-in-windows-powershell-ise).


Não precisa seguir explicitamente este fluxo de trabalho. Explica como depurar o guião.

* **Importar o módulo CatalogAndDatabaseManagement.psm1.** Fornece um catálogo e abstração ao nível do inquilino sobre as funções [de Gestão de Fragmentos.](elastic-scale-shard-map-management.md) Este módulo encapsula grande parte do padrão de catálogo e vale a pena explorar.
* **Importar o módulo SubscriptionManagement.psm1.** Contém funções para iniciar sessão no Azure e selecionar a subscrição Azure com a quais pretende trabalhar.
* **Obtenha detalhes de configuração.** Entre em Get-Configuration usando F11 e veja como a app config é especificada. Os nomes de recursos e outros valores específicos da aplicação são definidos aqui. Não altere estes valores até que esteja familiarizado com os scripts.
* **Pegue o objeto do catálogo.** Entre no Get-Catalog, que compõe e devolve um objeto de catálogo que é usado no script de nível superior. Esta função utiliza funções de Gestão de Fragmentos que são importadas de **AzureShardManagement.psm1**. O objeto do catálogo é composto pelos seguintes elementos:

   * $catalogServerFullyQualifiedName é construído utilizando o caule padrão mais o seu nome de utilizador: _\<user\> catalog-.database.windows.net_.
   * $catalogDatabaseName é obtido a partir da configuração: *tenantcatalog*.
   * O objeto $shardMapManager é inicializado a partir da base de dados do catálogo.
   * O objeto $shardMap é inicializado a partir do mapa de partições horizontais _tenantcatalog_ na base de dados do catálogo. Um objeto de catálogo é composto e devolvido. É usado no guião de nível mais alto.
* **Calcule a nova chave do inquilino.** É utilizada uma função hash para criar a chave de inquilino a partir do nome do inquilino.
* **Verifique se a chave do inquilino já existe.** O catálogo é verificado para se certificar de que a chave está disponível.
* **A base de dados do inquilino é aprovisionada com New-TenantDatabase**. Utilize o F11 para entrar na forma como a base de dados é aprovisionada utilizando um [modelo de Gestor de Recursos Azure](../../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md).

    O nome de base de dados é construído a partir do nome de inquilino para ficar claro quais as partições horizontais que pertencem a que inquilino. Também pode utilizar outras convenções de nomeação de bases de dados. Um modelo de Gestor de Recursos cria uma base de dados de inquilinos copiando uma base de dados de modelos _(baseTenantDB)_ no servidor do catálogo. Como alternativa, pode criar uma base de dados e inicializá-la importando um bacpac. Ou pode executar um script de inicialização a partir de um local bem conhecido.

    O modelo de Gestor de Recursos está na pasta ...\Learning Modules\Common\: *tenantdatabasecopytemplate.json*

* **A base de dados dos inquilinos é ainda inicializada.** O nome do local (inquilino) e o tipo de local são adicionados. Também pode fazer outra inicialização aqui.

* **A base de dados dos inquilinos está registada no catálogo.** Está registado no *Add-TenantDatabaseToCatalog* utilizando a chave do inquilino. Utilize F11 para avançar para os detalhes:

    * A base de dados do catálogo é adicionada ao mapa de partições horizontais (a lista de bases de dados conhecidas).
    * É criado o mapeamento que liga o valor da chave à partição horizontal.
    * Metadados adicionais sobre o inquilino (nome do local) são adicionados à tabela de Inquilinos no catálogo. A mesa dos Inquilinos não faz parte do esquema da Shard Management, e não é instalada pela EDCL. Esta tabela ilustra como a base de dados do catálogo pode ser alargada para suportar dados adicionais específicos da aplicação.


Após o provisionamento concluído, a execução retorna ao roteiro *original de Demo-ProvisionAndCatalog.* A página **Eventos** abre para o novo inquilino no navegador.

   ![Página de eventos](./media/saas-dbpertenant-provision-and-catalog/new-tenant.png)


## <a name="provision-a-batch-of-tenants"></a>Provisionamento de um lote de inquilinos

Este exercício prevê um lote de 17 inquilinos. Recomendamos que você provisa este lote de inquilinos antes de iniciar outros tutoriais de base de dados SaaS de banco de dados por inquilino. Há mais do que algumas bases de dados para trabalhar.

1. No PowerShell ISE, aberto... \\ Disposições sobre módulos \\ de aprendizagem Eatalog \\ *Demo-ProvisionAndCatalog.ps1*. Altere o parâmetro *$DemoScenario* para 3:

   * **$DemoScenario**  =  **3**, *Provision a lote de inquilinos.*
2. Para executar o guião, prima F5.

O script implementa um lote de inquilinos adicionais. Utiliza um [modelo de Gestor de Recursos Azure](../../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md) que controla o lote e delega o fornecimento de cada base de dados a um modelo ligado. Utilizar modelos desta forma permite que o Azure Resource Manager funcione como o mediador no processo de aprovisionamento do script. Os modelos prevêem bases de dados em paralelo e manuseam as retrações, se necessário. O script é idempotente, por isso, se falhar ou parar por qualquer motivo, volte a executá-lo.

### <a name="verify-the-batch-of-tenants-that-successfully-deployed"></a>Verifique o lote de inquilinos que implementaram com sucesso

* No [portal Azure,](https://portal.azure.com)navegue pela sua lista de servidores e abra o servidor *de inquilinos1.* Selecione **bases de dados SQL** e verifique se o lote de 17 bases de dados adicionais está agora na lista.

   ![Lista de bases de dados](./media/saas-dbpertenant-provision-and-catalog/database-list.png)



## <a name="other-provisioning-patterns"></a>Outros padrões de aprovisionamento

Outros padrões de provisionamento não incluídos neste tutorial:

**Bases de dados de pré-provisão**: O padrão de pré-provisão explora o facto de que as bases de dados numa piscina elástica não acrescentam custos adicionais. Faturação é para a piscina elástica, não para as bases de dados. Bases de dados inúteis não consomem recursos. Ao pré-a provisionar bases de dados numa piscina e alocá-las quando necessário, pode reduzir o tempo para adicionar inquilinos. O número de bases de dados pré-a provisionadas pode ser ajustado conforme necessário para manter um tampão adequado à taxa de provisionamento prevista.

**Provisionamento automático**: No padrão de fornecimento automático, um serviço de fornecimento fornece servidores, piscinas e bases de dados automaticamente, se necessário. Se quiser, pode incluir bases de dados de pré-a provisionamento em piscinas elásticas. Se as bases de dados forem desativadas e eliminadas, as lacunas nas piscinas elásticas podem ser preenchidas pelo serviço de fornecimento. Tal serviço pode ser simples ou complexo, como manusear o fornecimento em múltiplas geografias e criar geo-replicação para a recuperação de desastres.

Com o padrão de a provisionamento automático, um pedido de cliente ou script submete um pedido de provisionamento a uma fila a ser processada pelo serviço de fornecimento. Em seguida, ele sonda o serviço para determinar a conclusão. Se for utilizado o pré-fornecimento, os pedidos são tratados rapidamente. O serviço fornece uma base de dados de substituição em segundo plano.


## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
>
> * Providenciar um único novo inquilino.
> * Providenciar um lote de inquilinos adicionais.
> * Entre nos detalhes do provisionamento dos inquilinos e registrá-los no catálogo.

Experimente o [tutorial de monitorização do desempenho.](./saas-dbpertenant-performance-monitoring.md)

## <a name="additional-resources"></a>Recursos adicionais

* [Tutoriais adicionais que se baseiam na aplicação de bases de dados SaaS por insusição](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* [Biblioteca de clientes da base de dados elástica](elastic-database-client-library.md)
* [Scripts de depurar no Windows PowerShell ISE](/powershell/scripting/components/ise/how-to-debug-scripts-in-windows-powershell-ise)