---
title: Agente de sincronização de dados para sincronização de dados SQL
description: Saiba como instalar e executar o Agente de Sincronização de Dados para SQL Data Sync em Azure para sincronizar dados com bases de dados do SQL Server
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 12/20/2018
ms.openlocfilehash: ed8d51adf5a93b470f287383a4d3eeb866b15236
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "92791465"
---
# <a name="data-sync-agent-for-sql-data-sync"></a>Agente de sincronização de dados para sincronização de dados SQL
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Sincronizar os dados com as bases de dados do SQL Server instalando e configurando o Agente de Sincronização de Dados para SQL Data Sync em Azure. Para obter mais informações sobre o SQL Data Sync, consulte [os dados do Sync em várias bases de dados de nuvem e no local com SQL Data Sync](sql-data-sync-data-sql-server-sql-database.md).

> [!IMPORTANT]
> O SQL Data Sync **não** suporta a Azure SQL Managed Instance neste momento.

## <a name="download-and-install"></a>Transferir e instalar

Para descarregar o Data Sync Agent, aceda ao [SqL Data Sync Agent](https://www.microsoft.com/download/details.aspx?id=27693). Para atualizar o Agente de Sincronização de Dados, instale o Agente no mesmo local que o antigo Agente e irá anular o original.

### <a name="install-silently"></a>Instale-se silenciosamente

Para instalar o Data Sync Agent silenciosamente a partir da origem do comando, introduza um comando semelhante ao seguinte exemplo. Verifique o nome do ficheiro do ficheiro .msi descarregado e forneça os seus próprios valores para os argumentos **TARGETDIR** e **SERVICEACCOUNT.**

- Se não fornecer um valor para **TARGETDIR,** o valor predefinido é `C:\Program Files (x86)\Microsoft SQL Data Sync 2.0` .

- Se fornecer `LocalSystem` como valor do **SERVICEACCOUNT,** utilize a autenticação do SQL Server quando configurar o agente para ligar ao SQL Server.

- Se fornecer uma conta de utilizador de domínio ou uma conta de utilizador local como o valor do **SERVICEACCOUNT,** também tem de fornecer a palavra-passe com o argumento **SERVICEPASSWORD.** Por exemplo, `SERVICEACCOUNT="<domain>\<user>"  SERVICEPASSWORD="<password>"`.

```cmd
msiexec /i "SQLDataSyncAgent-2.0-x86-ENU.msi" TARGETDIR="C:\Program Files (x86)\Microsoft SQL Data Sync 2.0" SERVICEACCOUNT="LocalSystem" /qn
```

## <a name="sync-data-with-a-sql-server-database"></a>Sincronizar dados com uma base de dados do SQL Server

Para configurar o Agente sincronizado de dados para que possa sincronizar dados com uma ou mais bases de dados do SQL Server, consulte [uma base de dados do SQL Server](sql-data-sync-sql-server-configure.md#add-on-prem).

## <a name="data-sync-agent-faq"></a><a name="agent-faq"></a> Agente de sincronização de dados FAQ

### <a name="why-do-i-need-a-client-agent"></a>Por que preciso de um agente cliente?

O serviço SQL Data Sync comunica com bases de dados do SQL Server através do agente cliente. Esta funcionalidade de segurança impede a comunicação direta com bases de dados por trás de uma firewall. Quando o serviço SQL Data Sync comunica com o agente, fá-lo utilizando ligações encriptadas e uma chave de ficha ou *agente* única. As bases de dados do SQL Server autenticam o agente utilizando a chave de ligação e de agente. Este design proporciona um alto nível de segurança para os seus dados.

### <a name="how-many-instances-of-the-local-agent-ui-can-be-run"></a>Quantas ocorrências do agente local UI pode ser executado

Só um caso da UI pode ser executado.

### <a name="how-can-i-change-my-service-account"></a>Como posso mudar a minha conta de serviço

Depois de instalar um agente cliente, a única forma de alterar a conta de serviço é desinstalá-la e instalar um novo agente cliente com a nova conta de serviço.

### <a name="how-do-i-change-my-agent-key"></a>Como posso mudar a minha chave de agente

Uma chave de agente só pode ser usada uma vez por um agente. Não pode ser reutilizado quando remover e depois reinstalar um novo agente, nem pode ser utilizado por vários agentes. Se precisa de criar uma nova chave para um agente existente, tem de ter a certeza de que a mesma chave é gravada com o agente cliente e com o serviço SQL Data Sync.

### <a name="how-do-i-retire-a-client-agent"></a>Como posso reformar um agente cliente

Para invalidar ou reformar imediatamente um agente, regenerar a sua chave no portal, mas não a submeter na UI do Agente. A regeneração de uma chave invalida a chave anterior, independentemente de o agente correspondente estar online ou offline.

### <a name="how-do-i-move-a-client-agent-to-another-computer"></a>Como posso mover um agente cliente para outro computador

Se quiser executar o agente local a partir de um computador diferente do que está atualmente ligado, faça as seguintes coisas:

1. Instale o agente no computador pretendido.
2. Inicie sessão no portal SQL Data Sync e regenera uma chave de agente para o novo agente.
3. Use a UI do novo agente para enviar a nova chave de agente.
4. Aguarde enquanto o agente cliente descarrega a lista de bases de dados no local que foram registadas anteriormente.
5. Forneça credenciais de base de dados para todas as bases de dados que apresentem como inacessíveis. Estas bases de dados devem ser contactáveis a partir do novo computador no qual o agente está instalado.

## <a name="troubleshoot-data-sync-agent-issues"></a><a name="agent-tshoot"></a> Problemas de problemas com o Agente de Sincronização de Dados

- [O agente cliente instala, desinstala ou falha na reparação](#agent-install)

- [O agente cliente não funciona depois de eu cancelar a desinstalação.](#agent-uninstall)

- [A minha base de dados não está listada na lista de agentes.](#agent-list)

- [O agente cliente não começa (Erro 1069)](#agent-start)

- [Não posso enviar a chave do agente.](#agent-key)

- [O agente cliente não pode ser eliminado do portal se a sua base de dados associada no local for inacessível](#agent-delete)

- [A aplicação Local Sync Agent não pode ligar-se ao serviço de sincronização local](#agent-connect)

### <a name="the-client-agent-install-uninstall-or-repair-fails"></a><a name="agent-install"></a> O agente cliente instala, desinstala ou falha na reparação

- **Porque.** Muitos cenários podem causar este fracasso. Para determinar a causa específica desta falha, olhe para os registos.

- **Resolução**. Para encontrar a causa específica da falha, gere e veja os registos do Instalador do Windows. Pode ligar o registo de registos num pedido de comando. Por exemplo, se o ficheiro de instalação descarregado for `SQLDataSyncAgent-2.0-x86-ENU.msi` , gere e examine ficheiros de registo utilizando as seguintes linhas de comando:

  - Para instalações: `msiexec.exe /i SQLDataSyncAgent-2.0-x86-ENU.msi /l*v LocalAgentSetup.Log`
  - Para desinstalações: `msiexec.exe /x SQLDataSyncAgent-2.0-x86-ENU.msi /l*v LocalAgentSetup.Log`

    Também pode ligar o registo de todas as instalações que são executadas pelo Instalador do Windows. O artigo base de conhecimento da Microsoft [Como ativar o registo do Instalador do Windows](https://support.microsoft.com/help/223300/how-to-enable-windows-installer-logging) fornece uma solução de um clique para ativar o registo para o Instalador do Windows. Também fornece a localização dos registos.

### <a name="the-client-agent-doesnt-work-after-i-cancel-the-uninstall"></a><a name="agent-uninstall"></a> O agente cliente não funciona depois de eu cancelar a desinstalação.

O agente cliente não funciona, mesmo depois de cancelar a sua desinstalação.

- **Porque.** Isto ocorre porque o agente cliente SQL Data Sync não armazena credenciais.

- **Resolução**. Pode experimentar estas duas soluções:

    -   Utilize serviços.msc para reentrar nas credenciais para o agente cliente.
    -   Desinstale este agente cliente e instale um novo. Descarregue e instale o mais recente agente cliente do [Download Center.](https://www.microsoft.com/download/details.aspx?id=27693)

### <a name="my-database-isnt-listed-in-the-agent-list"></a><a name="agent-list"></a> A minha base de dados não está listada na lista de agentes.

Quando tenta adicionar uma base de dados do SQL Server existente a um grupo de sincronização, a base de dados não aparece na lista de agentes.

Estes cenários podem causar esta questão:

- **Porque.** O agente cliente e o grupo de sincronização estão em diferentes datacenters.

- **Resolução**. O agente cliente e o grupo de sincronização devem estar no mesmo datacenter. Para configurar isto, tem duas opções:

    -   Criar um novo agente no datacenter onde o grupo de sincronização está localizado. Depois, registe a base de dados com aquele agente.
    -   Elimine o grupo de sincronização atual. Em seguida, recossar o grupo de sincronização no datacenter onde o agente está localizado.

- **Porque.** A lista de bases de dados do agente cliente não está atual.

- **Resolução**. Pare e, em seguida, reinicie o serviço de agente cliente.

    O agente local descarrega a lista de bases de dados associadas apenas na primeira submissão da chave do agente. Não descarrega a lista de bases de dados associadas em submissões de chaves de agente subsequentes. As bases de dados registadas durante um movimento de agente não aparecem na instância original do agente.

### <a name="client-agent-doesnt-start-error-1069"></a><a name="agent-start"></a> O agente cliente não começa (Erro 1069)

Descobre que o agente não está a funcionar num computador que acolhe o SQL Server. Quando tenta ligar manualmente o agente, vê uma caixa de diálogo que exibe a mensagem: "Erro 1069: O serviço não foi iniciado devido a uma falha de início de saúde."

![Caixa de diálogo de erro de sincronização de dados 1069](./media/sql-data-sync-agent-overview/sync-error-1069.png)

- **Porque.** Uma causa provável deste erro é que a palavra-passe no servidor local mudou desde que criou a palavra-passe do agente e do agente.

- **Resolução**. Atualize a palavra-passe do agente para a sua palavra-passe do servidor atual:

  1. Localize o serviço de agente cliente SQL Data Sync.  
    a. Selecione **Iniciar**.  
    b. Na caixa de pesquisa, **insira o serviço.msc**.  
    c. Nos resultados da pesquisa, selecione **Serviços.**  
    d. Na janela **Serviços,** percorra a entrada para o **SqL Data Sync Agent**.  
  1. Clique à direita no **Relatório de Sincronização de Dados SQL** e, em seguida, selecione **Stop**.
  1. Clique com o botão direito **do SqL Data Sync Agent** e, em seguida, selecione **Propriedades**.
  1. Nas **propriedades do agente de sincronização de dados SQL,** selecione o **separador Iniciar sessão.**
  1. Na **caixa de senha,** insira a sua palavra-passe.
  1. Na caixa **'Confirmar palavra-passe',** reentre na sua palavra-passe.
  1. Selecione **Apply** (Aplicar) e **OK**.
  1. Na janela **Serviços,** clique com o botão direito no serviço **SQL Data Sync Agent** e, em seguida, clique em **Iniciar**.
  1. Feche a janela **dos Serviços.**

### <a name="i-cant-submit-the-agent-key"></a><a name="agent-key"></a> Não posso enviar a chave do agente.

Depois de criar ou recriar uma chave para um agente, tenta submeter a chave através da aplicação SqlAzureDataSyncAgent. A submissão não está completa.

![Sync Error dialog box - Não é possível submeter a chave do agente](./media/sql-data-sync-agent-overview/sync-error-cant-submit-agent-key.png)

- **Pré-requisitos.** Antes de proceder, verifique os seguintes pré-requisitos:

  - O serviço SQL Data Sync Windows está em execução.

  - A conta de serviço do serviço SQL Data Sync Windows tem acesso à rede.

  - A porta de saída 1433 está aberta na sua regra de firewall local.

  - O ip local é adicionado à regra de firewall do servidor ou da base de dados para a base de dados de metadados de sincronização.

- **Porque.** A chave do agente identifica exclusivamente cada agente local. A chave deve satisfazer duas condições:

  -   A chave do agente cliente no servidor SQL Data Sync e no computador local deve ser idêntica.
  -   A chave do agente cliente só pode ser usada uma vez.

- **Resolução**. Se o seu agente não está a funcionar, é porque uma ou ambas estas condições não estão satisfeitas. Para fazer o seu agente trabalhar de novo:

  1. Gere uma nova chave.
  1. Aplique a nova chave no agente.

  Para aplicar a nova chave ao agente:

  1. No File Explorer, aceda ao diretório de instalação do seu agente. O diretório de instalação predefinido é C: \\ Program Files (x86) \\ Microsoft SQL Data Sync.
  1. Clique duas vezes na subdiretória do caixote do lixo.
  1. Abra a aplicação SqlAzureDataSyncAgent.
  1. Selecione **a tecla de agente de submissão**.
  1. No espaço fornecido, cole a chave da sua área de transferência.
  1. Selecione **OK**.
  1. Feche o programa.

### <a name="the-client-agent-cant-be-deleted-from-the-portal-if-its-associated-on-premises-database-is-unreachable"></a><a name="agent-delete"></a> O agente cliente não pode ser eliminado do portal se a sua base de dados associada no local for inacessível

Se um ponto final local (isto é, uma base de dados) que está registado num agente cliente SQL Data Sync se tornar inacessível, o agente cliente não pode ser eliminado.

- **Porque.** O agente local não pode ser apagado porque a base de dados inalcançável ainda está registada com o agente. Quando tenta eliminar o agente, o processo de eliminação tenta chegar à base de dados, o que falha.

- **Resolução**. Utilize "eliminar a força" para eliminar a base de dados inalcançável.

> [!NOTE]
> Se as tabelas de metadados sincronizados permanecerem após uma "eliminação forçada", `deprovisioningutil.exe` utilize-as para as limpar.

### <a name="local-sync-agent-app-cant-connect-to-the-local-sync-service"></a><a name="agent-connect"></a> A aplicação Local Sync Agent não pode ligar-se ao serviço de sincronização local

- **Resolução**. Experimente os passos seguintes:

  1. Saia da aplicação.  
  1. Abra o Painel de Serviços de Componentes.  
    a. Na caixa de pesquisa na barra de **tarefas, insira os serviços.msc**.  
    b. Nos resultados da pesquisa, clique duplo **Serviços**.  
  1. Pare o serviço **SQL Data Sync.**
  1. Reinicie o serviço **SQL Data Sync.**  
  1. Reabrir a aplicação.

## <a name="run-the-data-sync-agent-from-the-command-prompt"></a>Executar o Agente sincronizado de dados a partir da solicitação de comando

Pode executar os seguintes comandos do Data Sync Agent a partir da solicitação de comando:

### <a name="ping-the-service"></a>Ping o serviço

#### <a name="usage"></a>Utilização

```cmd
SqlDataSyncAgentCommand.exe -action pingsyncservice
```

#### <a name="example"></a>Exemplo

```cmd
SqlDataSyncAgentCommand.exe -action "pingsyncservice"
```

### <a name="display-registered-databases"></a>Mostrar bases de dados registadas

#### <a name="usage"></a>Utilização

```cmd
SqlDataSyncAgentCommand.exe -action displayregistereddatabases
```

#### <a name="example"></a>Exemplo

```cmd
SqlDataSyncAgentCommand.exe -action "displayregistereddatabases"
```

### <a name="submit-the-agent-key"></a>Enviar a chave do agente

#### <a name="usage"></a>Utilização

```cmd
Usage: SqlDataSyncAgentCommand.exe -action submitagentkey -agentkey [agent key]  -username [user name] -password [password]
```

#### <a name="example"></a>Exemplo

```cmd
SqlDataSyncAgentCommand.exe -action submitagentkey -agentkey [agent key generated from portal, PowerShell, or API] -username [user name to sync metadata database] -password [user name to sync metadata database]
```

### <a name="register-a-database"></a>Registar uma base de dados

#### <a name="usage"></a>Utilização

```cmd
SqlDataSyncAgentCommand.exe -action registerdatabase -servername [on-premisesdatabase server name] -databasename [on-premisesdatabase name]  -username [domain\\username] -password [password] -authentication [sql or windows] -encryption [true or false]
```

#### <a name="examples"></a>Exemplos

```cmd
SqlDataSyncAgentCommand.exe -action "registerdatabase" -serverName localhost -databaseName testdb -authentication sql -username <user name> -password <password> -encryption true

SqlDataSyncAgentCommand.exe -action "registerdatabase" -serverName localhost -databaseName testdb -authentication windows -encryption true

```

### <a name="unregister-a-database"></a>Desagregar uma base de dados

Quando utiliza este comando para desregistir uma base de dados, desprovisiona completamente a base de dados. Se a base de dados participar em outros grupos de sincronização, esta operação quebra os outros grupos de sincronização.

#### <a name="usage"></a>Utilização

```cmd
SqlDataSyncAgentCommand.exe -action unregisterdatabase -servername [on-premisesdatabase server name] -databasename [on-premisesdatabase name]
```

#### <a name="example"></a>Exemplo

```cmd
SqlDataSyncAgentCommand.exe -action "unregisterdatabase" -serverName localhost -databaseName testdb
```

### <a name="update-credentials"></a>Atualizar credenciais

#### <a name="usage"></a>Utilização

```cmd
SqlDataSyncAgentCommand.exe -action updatecredential -servername [on-premisesdatabase server name] -databasename [on-premisesdatabase name]  -username [domain\\username] -password [password] -authentication [sql or windows] -encryption [true or false]
```

#### <a name="examples"></a>Exemplos

```cmd
SqlDataSyncAgentCommand.exe -action "updatecredential" -serverName localhost -databaseName testdb -authentication sql -username <user name> -password <password> -encryption true

SqlDataSyncAgentCommand.exe -action "updatecredential" -serverName localhost -databaseName testdb -authentication windows -encryption true
```

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre o SQL Data Sync, consulte os seguintes artigos:

-   Visão geral - [Sync dados em várias bases de dados de nuvem e no local com SQL Data Sync em Azure](sql-data-sync-data-sql-server-sql-database.md)
-   Configurar o Data Sync
    - No portal - [Tutorial: Configurar o SQL Data Sync para sincronizar dados entre a Base de Dados SQL do Azure e o SqL Server](sql-data-sync-sql-server-configure.md)
    - Com o PowerShell
        -  [Utilize o PowerShell para sincronizar entre várias bases de dados na Base de Dados Azure SQL](scripts/sql-data-sync-sync-data-between-sql-databases.md)
        -  [Utilize o PowerShell para sincronizar entre uma base de dados na Base de Dados Azure SQL e uma base de dados num caso de Servidor SQL](scripts/sql-data-sync-sync-data-between-azure-onprem.md)
-   Melhores práticas - [Melhores práticas para Azure SQL Data Sync](sql-data-sync-best-practices.md)
-   Monitor - [Monitor SQL Data Sync com registos do Monitor Azure](./monitor-tune-overview.md)
-   Resolução de problemas - [Problemas de resolução de problemas com Azure SQL Data Sync]sql-data-sync-troubleshoot.md)
-   Atualizar o esquema de sincronização
    -   Com a Transact-SQL - [Automatizar a replicação das alterações de esquema com o SQL Data Sync em Azure](sql-data-sync-update-sync-schema.md)
    -   Com o PowerShell - [Use o PowerShell para atualizar o esquema de sincronização num grupo de sincronização existente](scripts/update-sync-schema-in-sync-group.md)