---
title: Agente de sincronização de dados para sincronização de dados SQL
description: Saiba como instalar e executar o Data Sync Agent para O SQL Data Sync em Azure para sincronizar dados com bases de dados do Servidor SQL no local
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 12/20/2018
ms.openlocfilehash: 0026f160c247d2b0bfdd32613676b04d6f350081
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "84049786"
---
# <a name="data-sync-agent-for-sql-data-sync"></a>Agente de sincronização de dados para sincronização de dados SQL
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Sincronizar dados com bases de dados do Servidor SQL no local, instalando e configurando o Agente de Sincronização de Dados para Sincronização de Dados SQL em Azure. Para obter mais informações sobre o SQL Data Sync, consulte os dados do Sync através de várias bases de dados em [nuvem e no local com SQL Data Sync](sql-data-sync-data-sql-server-sql-database.md).

> [!IMPORTANT]
> O SQL Data Sync **não** suporta a Instância Gerida Azure SQL neste momento.

## <a name="download-and-install"></a>Transferir e instalar

Para descarregar o Agente de Sincronização de Dados, vá ao [SQL Data Sync Agent](https://www.microsoft.com/download/details.aspx?id=27693).

### <a name="install-silently"></a>Instalar em silêncio

Para instalar o Agente sincronizado de dados silenciosamente a partir do pedido de comando, introduza um comando semelhante ao seguinte exemplo. Verifique o nome do ficheiro de ficheiros do ficheiro .msi descarregado e forneça os seus próprios valores para os argumentos **TARGETDIR** e **SERVICEACCOUNT.**

- Se não fornecer um valor para **targetdir,** o valor predefinido é `C:\Program Files (x86)\Microsoft SQL Data Sync 2.0` .

- Se fornecer `LocalSystem` como valor **serviceACCOUNT,** utilize a autenticação do Servidor SQL quando configurar o agente para se ligar ao Servidor SQL no local.

- Se fornecer uma conta de utilizador de domínio ou uma conta de utilizador local como valor de **SERVICEACCOUNT,** também tem de fornecer a palavra-passe com o argumento **SERVICEPASSWORD.** Por exemplo, `SERVICEACCOUNT="<domain>\<user>"  SERVICEPASSWORD="<password>"`.

```cmd
msiexec /i "SQLDataSyncAgent-2.0-x86-ENU.msi" TARGETDIR="C:\Program Files (x86)\Microsoft SQL Data Sync 2.0" SERVICEACCOUNT="LocalSystem" /qn
```

## <a name="sync-data-with-sql-server-on-premises"></a>Sync dados com o SQL Server no local

Para configurar o Agente de Sincronização de Dados para que possa sincronizar dados com uma ou mais bases de dados do SQL Server no local, consulte Adicionar uma base de dados do [Servidor SQL no local](sql-data-sync-sql-server-configure.md#add-on-prem).

## <a name="data-sync-agent-faq"></a><a name="agent-faq"></a>Agente de sincronização de dados FAQ

### <a name="why-do-i-need-a-client-agent"></a>Por que preciso de um agente cliente?

O serviço SQL Data Sync comunica com as bases de dados do SQL Server através do agente cliente. Esta função de segurança impede a comunicação direta com bases de dados por detrás de uma firewall. Quando o serviço SQL Data Sync comunica com o agente, fá-lo utilizando ligações encriptadas e uma chave única de token ou *agente*. As bases de dados do SQL Server autenticam o agente utilizando a cadeia de ligação e a chave do agente. Este design proporciona um alto nível de segurança para os seus dados.

### <a name="how-many-instances-of-the-local-agent-ui-can-be-run"></a>Quantos casos do agente local UI podem ser executados

Só uma instância da UI pode ser executada.

### <a name="how-can-i-change-my-service-account"></a>Como posso mudar a minha conta de serviço

Depois de instalar um agente cliente, a única forma de alterar a conta de serviço é desinstalá-la e instalar um novo agente cliente com a nova conta de serviço.

### <a name="how-do-i-change-my-agent-key"></a>Como posso mudar a minha chave de agente

Uma chave de agente só pode ser usada uma vez por um agente. Não pode ser reutilizado quando remove e reinstalar um novo agente, nem pode ser utilizado por vários agentes. Se precisa de criar uma nova chave para um agente existente, deve ter a certeza de que a mesma chave é gravada com o agente cliente e com o serviço SQL Data Sync.

### <a name="how-do-i-retire-a-client-agent"></a>Como posso reformar um agente cliente

Para invalidar ou reformar imediatamente um agente, regenerar a sua chave no portal, mas não a submeter na UI agente. Regenerar uma chave invalida a chave anterior, independentemente de o agente correspondente estar online ou offline.

### <a name="how-do-i-move-a-client-agent-to-another-computer"></a>Como posso mover um agente cliente para outro computador

Se quiser executar o agente local a partir de um computador diferente do que está atualmente, faça as seguintes coisas:

1. Instale o agente no computador desejado.
2. Inicie sessão no portal SQL Data Sync e regenerar uma chave de agente para o novo agente.
3. Usa a UI do novo agente para submeter a nova chave do agente.
4. Espere enquanto o agente cliente descarrega a lista de bases de dados no local que foram registadas anteriormente.
5. Forneça credenciais de base de dados para todas as bases de dados que apresentem como inacessíveis. Estas bases de dados devem ser acessíveis a partir do novo computador no qual o agente está instalado.

## <a name="troubleshoot-data-sync-agent-issues"></a><a name="agent-tshoot"></a>Problemas problemas de agente de sincronização de dados

- [O agente cliente instala, desinstala ou repara falha](#agent-install)

- [O agente cliente não trabalha depois de cancelar a desinstalação.](#agent-uninstall)

- [A minha base de dados não está na lista de agentes.](#agent-list)

- [O agente cliente não começa (Erro 1069)](#agent-start)

- [Não posso submeter a chave do agente.](#agent-key)

- [O agente cliente não pode ser eliminado do portal se a sua base de dados associada no local for incontactável](#agent-delete)

- [Aplicação Local Sync Agent não pode ligar-se ao serviço de sincronização local](#agent-connect)

### <a name="the-client-agent-install-uninstall-or-repair-fails"></a><a name="agent-install"></a>O agente cliente instala, desinstala ou repara falha

- **Porque.** Muitos cenários podem causar esta falha. Para determinar a causa específica para esta falha, olhe para os registos.

- **Resolução**. Para encontrar a causa específica da falha, gere e veja os registos do Instalador do Windows. Pode ligar a exploração a bordo num pedido de comando. Por exemplo, se o ficheiro de instalação descarregado `SQLDataSyncAgent-2.0-x86-ENU.msi` for, gere e examine ficheiros de registo utilizando as seguintes linhas de comando:

  - Para instalações:`msiexec.exe /i SQLDataSyncAgent-2.0-x86-ENU.msi /l*v LocalAgentSetup.Log`
  - Para desinstalar:`msiexec.exe /x SQLDataSyncAgent-2.0-x86-ENU.msi /l*v LocalAgentSetup.Log`

    Também pode ligar o registo de todas as instalações realizadas pelo Instalador do Windows. O artigo da Microsoft Knowledge Base [Como ativar o registo do Instalador do Windows](https://support.microsoft.com/help/223300/how-to-enable-windows-installer-logging) fornece uma solução de um clique para ativar o registo do Instalador do Windows. Também fornece a localização dos registos.

### <a name="the-client-agent-doesnt-work-after-i-cancel-the-uninstall"></a><a name="agent-uninstall"></a>O agente cliente não trabalha depois de cancelar a desinstalação.

O agente cliente não funciona, mesmo depois de cancelares a sua desinstalação.

- **Porque.** Isto ocorre porque o agente cliente SQL Data Sync não armazena credenciais.

- **Resolução**. Pode experimentar estas duas soluções:

    -   Utilize serviços.msc para reintroduzir as credenciais para o agente cliente.
    -   Desinstale este agente cliente e, em seguida, instale um novo. Descarregue e instale o mais recente agente cliente do [Download Center](https://www.microsoft.com/download/details.aspx?id=27693).

### <a name="my-database-isnt-listed-in-the-agent-list"></a><a name="agent-list"></a>A minha base de dados não está na lista de agentes.

Quando tenta adicionar uma base de dados de Servidor SQL existente a um grupo de sincronização, a base de dados não aparece na lista de agentes.

Estes cenários podem causar esta questão:

- **Porque.** O agente cliente e o grupo de sincronização estão em diferentes datacenters.

- **Resolução**. O agente cliente e o grupo de sincronização devem estar no mesmo datacenter. Para configurar isto, tem duas opções:

    -   Crie um novo agente no datacenter onde o grupo de sincronização está localizado. Então, registe a base de dados com aquele agente.
    -   Elimine o grupo de sincronização atual. Em seguida, recrie o grupo de sincronização no datacenter onde o agente está localizado.

- **Porque.** A lista de bases de dados do agente cliente não está presente.

- **Resolução**. Pare e reinicie o serviço de agente cliente.

    O agente local descarrega a lista de bases de dados associadas apenas na primeira submissão da chave do agente. Não descarrega a lista de bases de dados associadas em submissões de chaves de agente subsequentes. As bases de dados registadas durante um movimento de agente não aparecem na instância original do agente.

### <a name="client-agent-doesnt-start-error-1069"></a><a name="agent-start"></a>O agente cliente não começa (Erro 1069)

Descobres que o agente não está a funcionar num computador que acolhe o SQL Server. Quando tenta ligar manualmente o agente, vê uma caixa de diálogo que exibe a mensagem: "Error 1069: O serviço não começou devido a uma falha de logon."

![Data Sync error 1069 caixa de diálogo](./media/sql-data-sync-agent-overview/sync-error-1069.png)

- **Porque.** Uma causa provável deste erro é que a palavra-passe no servidor local mudou desde que criou a senha do agente e do agente.

- **Resolução**. Atualize a palavra-passe do agente para a sua senha de servidor atual:

  1. Localize o serviço de agente cliente SQL Data Sync.  
    a. Selecione **Iniciar**.  
    b. Na caixa de pesquisa, insira **serviços.msc**.  
    c. Nos resultados da pesquisa, selecione **Serviços**.  
    d. Na janela **Serviços,** percorra a entrada do **Agente SQL Data Sync**.  
  1. Clique no **SQL Data Sync Agent**e, em seguida, selecione **Stop**.
  1. Clique no **SQL Data Sync Agent**e, em seguida, selecione **Propriedades**.
  1. No **SQL Data Sync Agent Properties,** selecione o separador **Iniciar sessão.**
  1. Na caixa **Password,** introduza a sua palavra-passe.
  1. Na caixa **'Confirmar Palavra-passe',** reintroduza a sua palavra-passe.
  1. Selecione **Apply** (Aplicar) e **OK**.
  1. Na janela **Serviços,** clique à direita no serviço **SQL Data Sync Agent** e, em seguida, clique em **Iniciar**.
  1. Feche a janela dos **Serviços.**

### <a name="i-cant-submit-the-agent-key"></a><a name="agent-key"></a>Não posso submeter a chave do agente.

Depois de criar ou recriar uma chave para um agente, tenta submeter a chave através da aplicação SqlAzureDataSyncAgent. A submissão não está concluída.

![Caixa de diálogo de erro de sincronização - Não pode enviar a chave do agente](./media/sql-data-sync-agent-overview/sync-error-cant-submit-agent-key.png)

- **Pré-requisitos.** Antes de prosseguir, verifique os seguintes pré-requisitos:

  - O serviço SQL Data Sync Windows está em execução.

  - A conta de serviço do serviço SQL Data Sync Windows tem acesso à rede.

  - A porta de saída 1433 está aberta na sua regra de firewall local.

  - O ip local é adicionado à regra de firewall do servidor ou da base de dados para a base de dados de metadados sincronizados.

- **Porque.** A chave do agente identifica exclusivamente cada agente local. A chave deve satisfazer duas condições:

  -   A chave do agente cliente no servidor SQL Data Sync e no computador local deve ser idêntica.
  -   A chave do agente cliente só pode ser usada uma vez.

- **Resolução**. Se o seu agente não está a funcionar, é porque uma ou ambas as condições não estão reunidas. Para fazer com que o seu agente volte a trabalhar:

  1. Gerar uma nova chave.
  1. Aplique a nova chave ao agente.

  Para aplicar a nova chave ao agente:

  1. No File Explorer, vá ao seu diretório de instalação de agente. O diretório de instalação predefinido é C: Ficheiros de \\ Programa (x86) \\ Microsoft SQL Data Sync.
  1. Clique duas vezes no subdiretório do caixote do lixo.
  1. Abra a aplicação SqlAzureDataSyncAgent.
  1. Selecione **A Chave do Agente de Submissão**.
  1. No espaço fornecido, cola a chave da sua prancheta.
  1. Selecione **OK**.
  1. Feche o programa.

### <a name="the-client-agent-cant-be-deleted-from-the-portal-if-its-associated-on-premises-database-is-unreachable"></a><a name="agent-delete"></a>O agente cliente não pode ser eliminado do portal se a sua base de dados associada no local for incontactável

Se um ponto final local (isto é, uma base de dados) registado com um agente cliente SQL Data Sync se tornar inacessível, o agente cliente não pode ser eliminado.

- **Porque.** O agente local não pode ser apagado porque a base de dados incontactável ainda está registada com o agente. Quando tenta eliminar o agente, o processo de eliminação tenta chegar à base de dados, o que falha.

- **Resolução**. Utilize "eliminar a força" para eliminar a base de dados incontactável.

> [!NOTE]
> Se as tabelas de metadados sincronizados permanecerem após uma "eliminação da força", `deprovisioningutil.exe` utilize-as para as limpar.

### <a name="local-sync-agent-app-cant-connect-to-the-local-sync-service"></a><a name="agent-connect"></a>Aplicação Local Sync Agent não pode ligar-se ao serviço de sincronização local

- **Resolução**. Experimente os passos seguintes:

  1. Saia da aplicação.  
  1. Abra o Painel de Serviços de Componentes.  
    a. Na caixa de pesquisa na barra de tarefas, insira **serviços.msc**.  
    b. Nos resultados da pesquisa, clique duas vezes **nos Serviços**.  
  1. Pare o serviço **SQL Data Sync.**
  1. Reiniciar o serviço **SQL Data Sync.**  
  1. Reabrir a aplicação.

## <a name="run-the-data-sync-agent-from-the-command-prompt"></a>Executar o Agente sincronizado de dados a partir do pedido de comando

Pode executar os seguintes comandos do Agente sincronizado de dados a partir do pedido de comando:

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

### <a name="submit-the-agent-key"></a>Submeter a chave do agente

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

### <a name="unregister-a-database"></a>Desregistar uma base de dados

Quando utiliza este comando para desmarcar uma base de dados, este determina completamente a base de dados. Se a base de dados participar em outros grupos de sincronização, esta operação quebra os outros grupos de sincronização.

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

## <a name="next-steps"></a>Próximos passos

Para mais informações sobre o SQL Data Sync, consulte os seguintes artigos:

-   Visão geral - Sync dados em várias bases de dados de [nuvem e no local com SQL Data Sync em Azure](sql-data-sync-data-sql-server-sql-database.md)
-   Configurar o Sincronizado de Dados
    - No portal - [Tutorial: Configurar o SQL Data Sync para sincronizar dados entre a Base de Dados Azure SQL e o Servidor SQL no local](sql-data-sync-sql-server-configure.md)
    - Com o PowerShell
        -  [Use o PowerShell para sincronizar entre várias bases de dados na Base de Dados Azure SQL](scripts/sql-data-sync-sync-data-between-sql-databases.md)
        -  [Utilize o PowerShell para sincronizar entre uma base de dados em Azure SQL Database e uma base de dados numa instância do Servidor SQL](scripts/sql-data-sync-sync-data-between-azure-onprem.md)
-   Boas práticas - [Boas práticas para O Sincronização de Dados Azure SQL](sql-data-sync-best-practices.md)
-   Monitor - [Monitor SQL Data Sync com registos do Monitor Azure](sql-data-sync-monitor-sync.md)
-   Troubleshoot - [Problemas de resolução de problemas com O SQL Data Sync]sql-data-sync-troubleshoot.md)
-   Atualizar o esquema de sincronização
    -   Com transact-SQL - [Replicação automatizada de alterações de esquema com SQL Data Sync em Azure](sql-data-sync-update-sync-schema.md)
    -   Com powerShell - [Use powerShell para atualizar o esquema de sincronização num grupo de sincronização existente](scripts/update-sync-schema-in-sync-group.md)
