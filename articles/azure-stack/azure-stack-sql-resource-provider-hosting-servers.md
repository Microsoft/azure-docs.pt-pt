---
title: SQL que aloja os servidores no Azure Stack | Documentos da Microsoft
description: Como adicionar instâncias do SQL para o aprovisionamento através do fornecedor de recursos de adaptador de SQL.
services: azure-stack
documentationCenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/26/2019
ms.author: jeffgilb
ms.reviewer: quying
ms.lastreviewed: 10/16/2018
ms.openlocfilehash: 1b9c7f00c8ec8408547620111634470d455334c8
ms.sourcegitcommit: f24fdd1ab23927c73595c960d8a26a74e1d12f5d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2019
ms.locfileid: "58499238"
---
# <a name="add-hosting-servers-for-the-sql-resource-provider"></a>Adicionar servidores de alojamento para o fornecedor de recursos do SQL

Pode criar base de dados do SQL Server que aloja servidores numa máquina virtual (VM) no [do Azure Stack](azure-stack-poc.md), ou numa VM fora do seu ambiente do Azure Stack, desde que o fornecedor de recursos do SQL pode ligar-se à instância.

> [!NOTE]
> O fornecedor de recursos do SQL deve ser criado na subscrição de fornecedor padrão enquanto os servidores de alojamento de SQL devem ser criados numa subscrição faturável, do utilizador. O servidor do fornecedor de recursos não deve ser utilizados para alojar bases de dados do utilizador.

## <a name="overview"></a>Descrição geral

Antes de adicionar um servidor de alojamento de SQL, reveja os seguintes requisitos obrigatórios e gerais.

### <a name="mandatory-requirements"></a>Requisitos obrigatórios

* Ative a autenticação de SQL na instância do SQL Server. Uma vez que o fornecedor de recursos do SQL VM não estiver associado a um domínio, só pode ligar a um servidor de alojamento utilizando a autenticação SQL.
* Configure os endereços IP para as instâncias SQL como pública quando instalado no Azure Stack. O fornecedor de recursos e utilizadores, tais como aplicações Web, comunicam através da rede de utilizador, pelo que é necessário ter ligação à instância do SQL nesta rede.

### <a name="general-requirements"></a>Requisitos gerais

* Dedica a instância SQL para utilização pelas cargas de trabalho do utilizador e o fornecedor de recursos. Não é possível utilizar uma instância SQL que está a ser utilizada por qualquer outro tipo de consumidor. Esta restrição aplica-se também para serviços de aplicações.
* Configure uma conta com os níveis de privilégio apropriado para o fornecedor de recursos (descrito abaixo).
* É responsável por gerenciar as instâncias SQL e respetivos anfitriões.  Por exemplo, o fornecedor de recursos não aplicar as atualizações, lidar com cópias de segurança ou lidar com rotação de credenciais.

### <a name="sql-server-virtual-machine-images"></a>Imagens de máquina virtual do SQL Server

Imagens de máquinas virtuais de IaaS do SQL estão disponíveis através da funcionalidade de gestão do Marketplace. Estas imagens são as mesmas que as VMs de SQL que estão disponíveis no Azure.

Certifique-se de que sempre transferir a versão mais recente dos **extensão de IaaS do SQL** antes de implementar uma VM do SQL com um item do mercado. A extensão de IaaS e o portal correspondente melhorias fornecem funcionalidades adicionais, como a aplicação de patches automática e de cópia de segurança. Para obter mais informações sobre esta extensão, consulte [automatizar tarefas de gestão em máquinas virtuais do Azure com a extensão de agente do SQL Server](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-agent-extension).

> [!NOTE]
> A extensão de IaaS do SQL é _necessário_ para todos os SQL nas imagens do Windows no marketplace; a VM não será a implementar se não a transferir a extensão. Não é utilizado com imagens de máquinas virtuais do SQL baseado em Linux.

Existem outras opções para implementar VMs de SQL, incluindo modelos no [Galeria de início rápido do Azure Stack](https://github.com/Azure/AzureStack-QuickStart-Templates).

> [!NOTE]
> Quaisquer servidores de alojamento instalados numa pilha do Azure com vários nós devem ser criados a partir de uma subscrição de utilizador e não a subscrição do fornecedor predefinido. Eles devem ser criados no portal de utilizador ou a partir de uma sessão do PowerShell com um início de sessão apropriado. Todos os servidores de alojamento são VMs cobrar e tem de ter licenças adequadas do SQL. O administrador de serviços _pode_ ser o proprietário dessa subscrição.

### <a name="required-privileges"></a>Privilégios necessários

Pode criar um utilizador administrativo com privilégios mais baixos de um sysadmin do SQL. O utilizador precisa apenas de permissões para as seguintes operações:

* Base de dados: Criar, Alter, com Containment (para sempre em única), remover, de cópia de segurança
* Grupo de disponibilidade: ALTER, associe-se, adicionar ou remover base de dados
* Início de sessão: Criar, selecionar, Alter, Drop, revogar
* Operações Select: \[mestre\].\[ SYS\].\[ availability_group_listeners\] (AlwaysOn), sys. availability_replicas (AlwaysOn), sys. Databases, \[mestre\].\[ SYS\].\[ dm_os_sys_memory\], SERVERPROPERTY, \[mestre\].\[ SYS\].\[ availability_groups\] (AlwaysOn), master_files

### <a name="additional-security-information"></a>Informações de segurança adicionais

As seguintes informações fornecem orientações de segurança adicionais:

* Todo o armazenamento do Azure Stack é encriptado com BitLocker, para que qualquer instância do SQL no Azure Stack irá utilizar o armazenamento de BLOBs encriptados.
* O fornecedor de recursos do SQL suporta totalmente o TLS 1.2. Certifique-se de que qualquer SQL Server que é gerenciado por meio de SQL RP está configurado para TLS 1.2 _apenas_ e usará como padrão para que a RP. Todas as versões suportadas do suporte do SQL Server TLS 1.2, consulte [suporte de TLS 1.2 para Microsoft SQL Server](https://support.microsoft.com/en-us/help/3135244/tls-1-2-support-for-microsoft-sql-server).
* Utilize o SQL Server Configuration Manager para definir o **ForceEncryption** opção para garantir que todas as comunicações para o SQL server são sempre encriptados. Ver [para configurar o servidor para forçar as ligações encriptadas](https://docs.microsoft.com/sql/database-engine/configure-windows/enable-encrypted-connections-to-the-database-engine?view=sql-server-2017#ConfigureServerConnections).
* Certifique-se de que qualquer aplicativo cliente também está a comunicar através de uma ligação encriptada.
* A RP é configurada para confiar nos certificados utilizados por instâncias do SQL Server.

## <a name="provide-capacity-by-connecting-to-a-standalone-hosting-sql-server"></a>Fornecer capacidade conectando-se para alojar o SQL server autónomo

Pode usar autónomo (não-HA) SQL servers com qualquer edição do SQL Server 2014 ou SQL Server 2016. Certificar-se de que tem as credenciais para uma conta com privilégios de administrador do sistema.

Para adicionar um servidor de hospedagem independente que já está configurado, siga estes passos:

1. Inicie sessão no portal de operador do Azure Stack como administrador de serviços.

2. Selecione **todos os serviços** &gt; **recursos administrativos** &gt; **servidores de alojamento de SQL**.

   ![SQL Hosting Servers](./media/azure-stack-sql-rp-deploy/sqlhostingservers.png)

   Sob **servidores de alojamento de SQL**, pode ligar-se o fornecedor de recursos do SQL para instâncias do SQL Server que irá servir como back-end do fornecedor de recursos.

   ![Dashboard de adaptador de SQL](./media/azure-stack-sql-rp-deploy/sqlrp-hostingserver.png)

3. Clique em **Add** e, em seguida, forneça os detalhes de ligação para a sua instância do SQL Server sobre o **adicionar um servidor de alojamento de SQL** painel.

   ![Adicionar um servidor de alojamento de SQL](./media/azure-stack-sql-rp-deploy/sqlrp-newhostingserver.png)

    Opcionalmente, forneça um nome de instância e especifique um número de porta, se a instância não está atribuída para a porta predefinida 1433.

   > [!NOTE]
   > Desde que a instância SQL pode ser acessada pelo utilizador e administrador do Azure Resource Manager, esta pode ser colocada sob controle do fornecedor de recursos. A instância SQL __tem__ ser alocada exclusivamente para o fornecedor de recursos.

4. À medida que adiciona servidores, tem de atribuí-las a um SKU existente ou criar um novo SKU. Sob **adicionar um servidor que aloja**, selecione **SKUs**.

   * Para utilizar um SKU existente, escolher um SKU disponível e, em seguida, selecione **criar**.
   * Para criar um SKU, selecione **+ criar novo SKU**. Na **criar SKU**, introduza as informações necessárias e, em seguida, selecione **OK**.

     ![Criação de um SKU](./media/azure-stack-sql-rp-deploy/sqlrp-newsku.png)

## <a name="provide-high-availability-using-sql-always-on-availability-groups"></a>Fornecer elevada disponibilidade com o SQL grupos de Disponibilidade AlwaysOn

Configurar instâncias do SQL Always On requer passos adicionais e requer três VMs (ou máquinas físicas). Este artigo pressupõe que já tem uma boa compreensão dos grupos de disponibilidade Always On. Para obter mais informações, veja os artigos seguintes:

* [Introdução ao SQL Server Always On grupos de disponibilidade em máquinas virtuais do Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-availability-group-overview)
* [Sempre em grupos de disponibilidade (SQL Server)](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server?view=sql-server-2017)

> [!NOTE]
> O fornecedor de recursos do SQL adaptador _apenas_ suporta o SQL 2016 SP1 Enterprise ou instâncias mais tarde para grupos de Disponibilidade AlwaysOn. Esta configuração de adaptador requer novas funcionalidades do SQL, como o seeding automático.

### <a name="automatic-seeding"></a>Automático seeding

Deve habilitar [Seeding automático](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/automatically-initialize-always-on-availability-group) em cada grupo de disponibilidade para cada instância do SQL Server.

Para ativar o seeding automático em todas as instâncias, editar e, em seguida, execute o seguinte comando SQL na réplica primária para cada instância secundária:

  ```sql
  ALTER AVAILABILITY GROUP [<availability_group_name>]
      MODIFY REPLICA ON '<secondary_node>'
      WITH (SEEDING_MODE = AUTOMATIC)
  GO
  ```

O grupo de disponibilidade deve estar entre parênteses Retos.

Em nós do secundários, execute o seguinte comando SQL:

  ```sql
  ALTER AVAILABILITY GROUP [<availability_group_name>] GRANT CREATE ANY DATABASE
  GO
  ```

### <a name="configure-contained-database-authentication"></a>Configurar a autenticação de banco de dados independente

Antes de adicionar a base de dados contida para um grupo de disponibilidade, certifique-se de que a opção de servidor de autenticação de base de dados contida está definida como 1 em cada instância de servidor que aloja uma réplica de disponibilidade para o grupo de disponibilidade. Para obter mais informações, consulte [contido a opção de configuração do servidor de autenticação de base de dados](https://docs.microsoft.com/sql/database-engine/configure-windows/contained-database-authentication-server-configuration-option?view=sql-server-2017).

Utilize estes comandos para definir a opção de servidor de autenticação de banco de dados independente para cada instância:

  ```sql
  EXEC sp_configure 'contained database authentication', 1
  GO
  RECONFIGURE
  GO
  ```

### <a name="to-add-sql-always-on-hosting-servers"></a>Para adicionar SQL Always On alojar servidores

1. Inicie sessão no portal de administração do Azure Stack, como um administrador de serviço.

2. Selecione **navegue** &gt; **recursos administrativos** &gt; **SQL que aloja servidores** &gt; **+ adicionar**.

   Sob **servidores de alojamento de SQL**, pode ligar-se o fornecedor de recursos do SQL Server para as instâncias reais do SQL Server que servem de back-end do fornecedor de recursos.

3. Preencha o formulário com os detalhes de ligação para a sua instância do SQL Server. Certifique-se de que utiliza o endereço FQDN do sempre no serviço de escuta (e o nome de instância e o número de porta opcional). Forneça as informações para a conta configurada com privilégios de administrador do sistema.

4. Selecione a caixa de grupo de Disponibilidade AlwaysOn para ativar o suporte para as instâncias de SQL grupo de Disponibilidade AlwaysOn.

   ![Ativar sempre em](./media/azure-stack-sql-rp-deploy/AlwaysOn.PNG)

5. Adicione a instância do SQL Always On para um SKU.

   > [!IMPORTANT]
   > Não é possível misturar servidores autónomos com instâncias Always On no SKU do mesmo. A tentar misturar tipos depois de adicionar os resultados do servidor de alojamento primeiro num erro.

## <a name="sku-notes"></a>Notas SKU
Utilize um nome SKU que descreve as capacidades dos servidores no SKU, como a capacidade e desempenho. O nome serve como um auxílio para ajudar os utilizadores a implementar as bases de dados para o SKU adequado. Por exemplo, pode utilizar nomes SKU para diferenciar as ofertas de serviço, as seguintes características:
  
* alta capacidade
* elevado desempenho
* elevada disponibilidade

Como melhor prática, todos os servidores de hospedagem num SKU devem ter as mesmas características de desempenho e de recursos.

SKUs não podem ser atribuídos a utilizadores ou grupos específicos.

SKUs podem demorar até uma hora para ser visível no portal. Os utilizadores não é possível criar uma base de dados até que o SKU estiver totalmente criado.

Para editar um SKU, aceda a **todos os serviços** > **adaptador de SQL** > **SKUs**. Selecione o SKU para modificar, faça as alterações necessárias e clique em **guardar** para guardar as alterações. 

Para eliminar um SKU que não é mais necessária, aceda a **todos os serviços** > **adaptador de SQL** > **SKUs**. O nome SKU com o botão direito e selecione **eliminar** eliminá-lo.

> [!IMPORTANT]
> Pode demorar até uma hora para novos SKUs estar disponível no portal de utilizador.

## <a name="make-sql-databases-available-to-users"></a>Tornar as bases de dados SQL disponíveis para os utilizadores

Crie planos e ofertas para tornar as bases de dados SQL disponíveis para os utilizadores. Adicionar a **Microsoft.SqlAdapter** para o plano de serviço e criar uma quota de novo.

> [!IMPORTANT]
> Pode demorar até duas horas para novas quotas para estar disponível no portal de utilizador ou antes de uma quota alterada é imposta.

## <a name="next-steps"></a>Passos Seguintes

[Adicionar bases de dados](azure-stack-sql-resource-provider-databases.md)
