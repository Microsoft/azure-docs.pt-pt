---
title: Provisão de novos inquilinos em uma app multiarrendatária
description: Saiba como fornecer e catalogar novos inquilinos numa aplicação Multitenant SaaS da Azure SQL Database
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: seo-lt-2019
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 09/24/2018
ms.openlocfilehash: 6ec8f8835e925663fc6ac21a6eb1df09d6927109
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/27/2020
ms.locfileid: "74132102"
---
# <a name="learn-how-to-provision-new-tenants-and-register-them-in-the-catalog"></a>Saiba como fornecer novos inquilinos e registá-los no catálogo

Neste tutorial, aprende-se a fornecer e catalogar padrões SaaS. Você também aprende como eles são implementados na aplicação de bases de dados SaaS de bilhetes de ala e por inquilino. Cria e inicia novas bases de dados de inquilinos e regista-as no catálogo de inquilinos da aplicação. O catálogo é uma base de dados que mantém o mapeamento entre os muitos inquilinos da aplicação SaaS e os seus dados. O catálogo desempenha um papel importante na direção de pedidos de aplicação e gestão para a base de dados correta.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
>
> * Providenciar um único novo inquilino.
> * Providenciar um lote de inquilinos adicionais.


Para concluir este tutorial, devem ser cumpridos os seguintes pré-requisitos:

* A aplicação de bases de dados SaaS de bilhetes Wingtip SaaS por inquilino está implementada. Para implantá-lo em menos de cinco minutos, consulte Implementar e explorar a aplicação de bases de [dados SaaS de bilhetes de base de dados SaaS](saas-dbpertenant-get-started-deploy.md).
* O Azure PowerShell está instalado. Para obter mais informações, veja [Introdução ao Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps).

## <a name="introduction-to-the-saas-catalog-pattern"></a>Introdução ao padrão de catálogo SaaS

Numa aplicação multiarrendatária apoiada pela base de dados, é importante saber onde estão armazenadas informações para cada inquilino. No padrão do catálogo SaaS, uma base de dados de catálogo é usada para manter o mapeamento entre cada inquilino e a base de dados em que os seus dados são armazenados. Este padrão aplica-se sempre que os dados dos inquilinos são distribuídos por várias bases de dados.

Cada inquilino é identificado por uma chave no catálogo, que está mapeada para a localização da sua base de dados. Na aplicação Wingtip Tickets, a chave é formada a partir de um hash do nome do inquilino. Este esquema permite que a app construa a chave a partir do nome do inquilino incluído no URL da aplicação. Outros esquemas-chave para o inquilino podem ser usados.

O catálogo permite que o nome ou localização da base de dados seja alterado com o mínimo impacto na aplicação. Num modelo de base de dados multiarrendatário, esta capacidade também acomoda mover um inquilino entre bases de dados. O catálogo também pode ser usado para indicar se um inquilino ou base de dados está offline para manutenção ou outras ações. Esta capacidade é explorada no tutorial de [restaurar único inquilino.](saas-dbpertenant-restore-single-tenant.md)

O catálogo também pode armazenar metadados adicionais de inquilinos ou bases de dados, tais como a versão schema, plano de serviço, ou SLAs oferecido aos inquilinos. O catálogo pode armazenar outras informações que permitam a gestão da aplicação, suporte ao cliente ou DevOps.

Para além da aplicação SaaS, o catálogo pode ativar ferramentas de base de dados. Na amostra de bases de dados SaaS de bilhetes wingtip saaS por inquilino, o catálogo é utilizado para permitir a consulta de inquilinos cruzados, que é explorada no tutorial de [reporte ad hoc.](saas-tenancy-cross-tenant-reporting.md) A gestão de empregos na base de dados é explorada nos tutoriais de gestão de [Schema](saas-tenancy-schema-management.md) e de análise de [inquilinos.](saas-tenancy-tenant-analytics.md)

Nas amostras Wingtip Tickets SaaS, o catálogo é implementado utilizando as funcionalidades de Gestão de Fragmentos da biblioteca de clientes Da Base de [Dados Elástica (EDCL)](sql-database-elastic-database-client-library.md). O EDCL está disponível em Java e no .NET Framework. O EDCL permite que uma aplicação crie, gere e utilize um mapa de fragmentos apoiado por bases de dados.

Um mapa contém uma lista de fragmentos (bases de dados) e o mapeamento entre chaves (inquilinos) e fragmentos. As funções EDCL são usadas durante o fornecimento de inquilinos para criar as entradas no mapa do fragmento. São usados no tempo de execução por aplicações para se ligarem à base de dados correta. A EDCL caches informações de ligação para minimizar o tráfego na base de dados do catálogo e acelerar a aplicação.

> [!IMPORTANT]
> Os dados de mapeamento estão acessíveis na base de dados do catálogo, mas *não os editam.* Editar dados de mapeamento utilizando apenas APIs da Biblioteca de Clientes da Base de Dados Elástica. Manipular diretamente os dados de mapeamento corre o risco de corromper o catálogo e não é suportado.


## <a name="introduction-to-the-saas-provisioning-pattern"></a>Introdução ao padrão de provisionamento SaaS

Quando você adicionar um novo inquilino em uma aplicação SaaS que usa um modelo de base de dados de um único inquilino, você deve fornecer uma nova base de dados de inquilinos. A base de dados deve ser criada no local e no nível de serviço adequados. Deve também ser inicializado com os dados de esquemas e referência adequados. E deve estar registado no catálogo com a chave de inquilino apropriado.

Podem ser utilizadas diferentes abordagens para o fornecimento de bases de dados. Pode executar scripts SQL, implementar um bacpac ou copiar uma base de dados de modelos.

O fornecimento de bases de dados tem de fazer parte da sua estratégia de gestão de esquemas. Deve certificar-se de que as novas bases de dados são aprovisionadas com o mais recente esquema. Este requisito é explorado no tutorial de [gestão de Schema.](saas-tenancy-schema-management.md)

A app wingtip Tickets base-per-tenant disponibiliza novos inquilinos copiando uma base de dados de modelos chamada _basetenantdb_, que é implantada no servidor do catálogo. O provisionamento pode ser integrado na aplicação como parte de uma experiência de inscrição. Também pode ser suportado offline usando scripts. Este tutorial explora o fornecimento utilizando o PowerShell.

O fornecimento de scripts copia a base de dados _de tenantdb_ para criar uma nova base de dados de inquilinos numa piscina elástica. A base de dados de inquilinos é criada no servidor de inquilinos mapeado para o pseudónimo do _novo inquilino_ DNS. Este pseudónimo mantém uma referência ao servidor usado para fornecer novos inquilinos e é atualizado para apontar para um servidor de inquilinos de recuperação nos tutoriais de recuperação de desastres ([DR usando georestauro,](saas-dbpertenant-dr-geo-restore.md) [DR usando georeplicação](saas-dbpertenant-dr-geo-replication.md)). Os scripts iniciam então a base de dados com informações específicas do arrendatário e registam-na no mapa do catálogo. As bases de dados dos inquilinos são dadas nomes com base no nome do inquilino. Este esquema de nomeação não é uma parte crítica do padrão. O catálogo mapeia a chave do inquilino para o nome da base de dados, para que qualquer convenção de nomeação possa ser usada.


## <a name="get-the-wingtip-tickets-saas-database-per-tenant-application-scripts"></a>Obtenha os scripts de aplicação de bases de dados SaaS de bilhetes de ala e de bilhetes por inquilino

Os scripts SaaS e o código fonte de aplicação estão disponíveis no repo [WingtipTicketsSaaS-DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) GitHub. Confira as [orientações gerais](saas-tenancy-wingtip-app-guidance-tips.md) para os passos para descarregar e desbloquear os scripts Wingtip Tickets SaaS.


## <a name="provision-and-catalog-detailed-walkthrough"></a>Instruções detalhadas sobre o aprovisionamento e a catalogação

Para entender como a aplicação Wingtip Tickets implementa o fornecimento de novos inquilinos, adicione um ponto de rutura e siga o fluxo de trabalho enquanto você disponibiliza um inquilino.

1. No PowerShell ISE, abra... \\Provisões\\de Módulos de Aprendizagem E Catalog\\_Demo-ProvisionAndCatalog.ps1_ e definir os seguintes parâmetros:

   * **$TenantName** = o nome do novo local (por exemplo, *Bushwillow Blues*).
   * **$VenueType** = um dos tipos predefinidos: _blues, música clássica, dança, jazz, judo, automobilismo, multiusos, ópera, rockmusic, futebol._
   * **$DemoScenario** = **1**, *Provisão de um único inquilino.*

2. Para adicionar um ponto de rutura, coloque o cursor em qualquer lugar da linha que diz *New-Tenant .* Em seguida, prima F9.

   ![Ponto de rutura](media/saas-dbpertenant-provision-and-catalog/breakpoint.png)

3. Para executar o guião, prima F5.

4. Depois que a execução do guião pare no ponto de rutura, pressione f11 para entrar no código.

   ![Depurar](media/saas-dbpertenant-provision-and-catalog/debug.png)



Trace a execução do script utilizando as opções do menu **Debug.** Pressione F10 e F11 para passar ou entrar nas funções chamadas. Para obter mais informações sobre depuração de scripts PowerShell, consulte [Dicas sobre o trabalho e depurando scripts PowerShell](https://docs.microsoft.com/powershell/scripting/components/ise/how-to-debug-scripts-in-windows-powershell-ise).


Não precisas de seguir explicitamente este fluxo de trabalho. Explica como depurar o guião.

* **Importar o módulo CatalogAndDatabaseManagement.psm1.** Fornece um catálogo e abstração ao nível do inquilino sobre as funções [de Gestão de Fragmentos.](sql-database-elastic-scale-shard-map-management.md) Este módulo encapsula grande parte do padrão do catálogo e vale a pena explorar.
* **Importar o módulo SubscriptionManagement.psm1.** Contém funções para iniciar sessão no Azure e selecionar a subscrição Azure com a quais pretende trabalhar.
* **Obtenha detalhes de configuração.** Entre na Configuração Get utilizando f11 e veja como a consola da aplicação é especificada. Os nomes de recursos e outros valores específicos da aplicação são definidos aqui. Não mude estes valores até estar familiarizado com os scripts.
* **Pegue o objeto do catálogo.** Entre no Get-Catalog, que compõe e devolve um objeto de catálogo que é usado no script de alto nível. Esta função utiliza funções de Gestão de Fragmentos que são importadas de **AzureShardManagement.psm1**. O objeto do catálogo é composto pelos seguintes elementos:

   * $catalogServerFullyQualifiedName é construído utilizando o caule padrão mais o seu nome de utilizador: _catálogo-utilizador\<\>.database.windows .net_.
   * $catalogDatabaseName é obtido a partir da configuração: *tenantcatalog*.
   * O objeto $shardMapManager é inicializado a partir da base de dados do catálogo.
   * O objeto $shardMap é inicializado a partir do mapa de partições horizontais _tenantcatalog_ na base de dados do catálogo. Um objeto de catálogo é composto e devolvido. É usado no guião de alto nível.
* **Calcule a nova chave do inquilino.** É utilizada uma função hash para criar a chave de inquilino a partir do nome do inquilino.
* **Verifique se a chave do inquilino já existe.** O catálogo é verificado para se certificar de que a chave está disponível.
* **A base de dados do inquilino é aprovisionada com New-TenantDatabase**. Utilize o F11 para entrar na forma como a base de dados é aprovisionada utilizando um modelo de Gestor de [Recursos Azure](../azure-resource-manager/resource-manager-template-walkthrough.md).

    O nome de base de dados é construído a partir do nome de inquilino para ficar claro quais as partições horizontais que pertencem a que inquilino. Também pode utilizar outras convenções de nomeação de bases de dados. Um modelo de Gestor de Recursos cria uma base de dados de inquilinos copiando uma base de dados de modelos _(baseTenantDB_) no servidor do catálogo. Como alternativa, pode criar uma base de dados e ininicializá-la importando um bacpac. Ou pode executar um guião de inicialização a partir de um local bem conhecido.

    O modelo do Gestor de Recursos está na pasta ...\Learning Modules\Common\: *tenantdatabasecopytemplate.json*

* **A base de dados dos inquilinos está ainda inicializada.** O nome do local (inquilino) e o tipo de local são adicionados. Também pode fazer outra inicialização aqui.

* **A base de dados dos inquilinos está registada no catálogo.** Está registado no *Add-TenantDatabaseToCatalog* utilizando a chave do inquilino. Utilize F11 para avançar para os detalhes:

    * A base de dados do catálogo é adicionada ao mapa de partições horizontais (a lista de bases de dados conhecidas).
    * É criado o mapeamento que liga o valor da chave à partição horizontal.
    * Os metadados adicionais sobre o inquilino (nome do local) são adicionados à tabela de Inquilinos no catálogo. A mesa dos Inquilinos não faz parte do esquema da Shard Management, e não é instalada pela EDCL. Esta tabela ilustra como a base de dados do catálogo pode ser estendida para suportar dados adicionais específicos da aplicação.


Após o fornecimento completo, a execução retorna ao script original *de Demo-ProvisionAndCatalog.* A página **Eventos** abre para o novo inquilino no navegador.

   ![Página de eventos](media/saas-dbpertenant-provision-and-catalog/new-tenant.png)


## <a name="provision-a-batch-of-tenants"></a>Provisão de um lote de inquilinos

Este exercício prevê um lote de 17 inquilinos. Recomendamos que você forme este lote de inquilinos antes de iniciar outros tutoriais de base de dados SaaS de bilhetes wingtip por inquilino. Há mais do que algumas bases de dados para trabalhar.

1. No PowerShell ISE, abra... \\Fornecimento de\\Módulos\\de Aprendizagem E Catalog*Demo-ProvisionAndCatalog.ps1*. Alterar o parâmetro *$DemoScenario* para 3:

   * **$DemoScenario** = **3**, Providenciar um lote *de inquilinos.*
2. Para executar o guião, prima F5.

O script implementa um lote de inquilinos adicionais. Utiliza um modelo de Gestor de [Recursos Azure](../azure-resource-manager/resource-manager-template-walkthrough.md) que controla o lote e delega o fornecimento de cada base de dados a um modelo ligado. Utilizar modelos desta forma permite que o Azure Resource Manager funcione como o mediador no processo de aprovisionamento do script. Os modelos disponibilizam bases de dados paralelas e manuseiam repetições, se necessário. O guião é idempotente, por isso, se falhar ou parar por qualquer motivo, executa-o novamente.

### <a name="verify-the-batch-of-tenants-that-successfully-deployed"></a>Verifique o lote de inquilinos que implementaram com sucesso

* No [portal Azure,](https://portal.azure.com)navegue para a sua lista de servidores e abra o servidor *tenants1.* Selecione bases de **dados SQL**e verifique se o lote de 17 bases de dados adicionais está agora na lista.

   ![Lista de bases de dados](media/saas-dbpertenant-provision-and-catalog/database-list.png)



## <a name="other-provisioning-patterns"></a>Outros padrões de aprovisionamento

Outros padrões de provisionamento não incluídos neste tutorial:

Bases de **dados de pré-provisionamento**: O padrão de pré-provisionamento explora o facto de as bases de dados de uma piscina elástica não acrescentarem custos adicionais. A faturação é para a piscina elástica, não para as bases de dados. As bases de dados inúteis não consomem recursos. Ao pré-fornecer bases de dados numa piscina e alocá-las quando necessário, pode reduzir o tempo para adicionar inquilinos. O número de bases de dados pré-provisionadas pode ser ajustado conforme necessário para manter um tampão adequado à taxa de provisionamento prevista.

**Fornecimento automático**: No padrão de fornecimento automático, um serviço de fornecimento de serviços disponibiliza automaticamente servidores, piscinas e bases de dados, se necessário. Se quiser, pode incluir bases de dados pré-provisionantes em piscinas elásticas. Se as bases de dados forem desativadas e eliminadas, as lacunas nas piscinas elásticas podem ser preenchidas pelo serviço de provisionamento. Tal serviço pode ser simples ou complexo, como manusear o fornecimento através de várias geografias e criar geo-replicação para a recuperação de desastres.

Com o padrão de fornecimento automático, uma aplicação ou script do cliente submete um pedido de provisionamento a uma fila a ser processada pelo serviço de provisionamento. Em seguida, vota o serviço para determinar a conclusão. Se for utilizado o pré-provisionamento, os pedidos são tratados rapidamente. O serviço prevê uma base de dados de substituição em segundo plano.


## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
>
> * Providenciar um único novo inquilino.
> * Providenciar um lote de inquilinos adicionais.
> * Entre nos detalhes do fornecimento de inquilinos e registe-os no catálogo.

Experimente o tutorial de [monitorização](saas-dbpertenant-performance-monitoring.md)do desempenho .

## <a name="additional-resources"></a>Recursos adicionais

* [Tutoriais adicionais que se baseiam na aplicação de bases de dados SaaS de bilhetes de ala](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* [Biblioteca de clientes da base de dados elástica](sql-database-elastic-database-client-library.md)
* [Depuram scripts no IsE PowerShell do Windows](https://docs.microsoft.com/powershell/scripting/components/ise/how-to-debug-scripts-in-windows-powershell-ise)
