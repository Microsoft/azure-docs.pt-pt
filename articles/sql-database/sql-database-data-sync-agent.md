---
title: Agente de sincronização de dados SQL do Azure de sincronização de dados | Documentos da Microsoft
description: Saiba como instalar e executar o agente de sincronização de dados de sincronização de dados SQL do Azure para sincronizar dados com bases de dados do SQL Server no local
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: allenwux
ms.author: xiwu
ms.reviewer: douglasl
manager: craigg
ms.date: 12/20/2018
ms.openlocfilehash: 5d8b3b91771fda3a714112979b8bf9df39c4a9b3
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58088694"
---
# <a name="data-sync-agent-for-azure-sql-data-sync"></a>Agente de sincronização de dados para sincronização de dados SQL do Azure

Sincronizar dados com bases de dados do SQL Server no local por instalar e configurar o agente de sincronização de dados para sincronização de dados SQL do Azure. Para mais informações sobre a sincronização de dados SQL, veja [sincronizar dados em várias bases de dados na cloud e no local com a sincronização de dados SQL](sql-database-sync-data.md).

> [!IMPORTANT]
> Sincronização de dados SQL do Azure faz **não** suporta a instância gerida da base de dados SQL do Azure neste momento.

## <a name="download-and-install"></a>Transferir e instalar

Para transferir o agente de sincronização de dados, aceda a [agente de sincronização de dados do SQL Azure](https://www.microsoft.com/download/details.aspx?id=27693).

### <a name="install-silently"></a>Instalar automaticamente

Para instalar o agente de sincronização de dados automaticamente na linha de comandos, introduza um comando semelhante ao seguinte exemplo. Verifique o nome de ficheiro do arquivo. msi transferido e fornecer seus próprios valores para o **TARGETDIR** e **SERVICEACCOUNT** argumentos.

- Se não fornecer um valor para **TARGETDIR**, o valor predefinido é `C:\Program Files (x86)\Microsoft SQL Data Sync 2.0`.

- Se fornecer `LocalSystem` como o valor de **SERVICEACCOUNT**, utilizar a autenticação do SQL Server quando configurar o agente para ligar ao SQL Server no local.

- Se fornecer uma conta de utilizador de domínio ou uma conta de utilizador local como o valor de **SERVICEACCOUNT**, também tem de fornecer a palavra-passe com o **SERVICEPASSWORD** argumento. Por exemplo, `SERVICEACCOUNT="<domain>\<user>"  SERVICEPASSWORD="<password>"`.

```cmd
msiexec /i "SQLDataSyncAgent-2.0-x86-ENU.msi" TARGETDIR="C:\Program Files (x86)\Microsoft SQL Data Sync 2.0" SERVICEACCOUNT="LocalSystem" /qn
```

## <a name="sync-data-with-sql-server-on-premises"></a>Sincronizar dados com o SQL Server no local

Para configurar o agente de sincronização de dados, pelo que pode sincronizar dados com um ou mais locais bancos de dados SQL, consulte [adicionar uma base de dados do SQL Server no local](sql-database-get-started-sql-data-sync.md#add-on-prem).

## <a name="agent-faq"></a> FAQ do agente de sincronização de dados

### <a name="why-do-i-need-a-client-agent"></a>Por que razão necessito de um agente de cliente

O serviço de sincronização de dados SQL comunica com bancos de dados do SQL Server por meio do agente de cliente. Esta funcionalidade de segurança impede uma comunicação direta com bases de dados protegido por uma firewall. Quando o serviço de sincronização de dados SQL comunica com o agente, ele faz então usando encriptados ligações e um token de exclusivo ou *chave de agente*. As bases de dados do SQL Server autenticar-se o agente utilizando a chave de cadeia de caracteres e o agente de conexão. Esse design fornece um alto nível de segurança para os seus dados.

### <a name="how-many-instances-of-the-local-agent-ui-can-be-run"></a>Quantas instâncias do agente local pode ser executado a interface do Usuário

Pode ser executada apenas uma instância da interface do Usuário.

### <a name="how-can-i-change-my-service-account"></a>Como posso alterar minha conta de serviço

Depois de instalar um agente de cliente, a única forma de alterar a conta de serviço é desinstalá-lo e instalar um novo agente de cliente com a nova conta de serviço.

### <a name="how-do-i-change-my-agent-key"></a>Como posso alterar a minha chave de agente

Uma chave de agente só pode ser utilizada uma vez por um agente. Ele não pode ser reutilizado quando remova e volte a instalar um novo agente, nem pode ser utilizado por múltiplos agentes. Se precisar de criar uma nova chave para um agente existente, tem de ser-se de que a mesma chave é registada com o agente do cliente e com o serviço de sincronização de dados SQL.

### <a name="how-do-i-retire-a-client-agent"></a>Como posso desativar um agente de cliente

Para imediatamente, invalidar ou extinguir um agente, voltar a gerar a chave no portal do mas não enviá-lo na IU do agente. Regenerar uma chave invalida a chave anterior deixou se o agente correspondente está online ou offline.

### <a name="how-do-i-move-a-client-agent-to-another-computer"></a>Como faço para mover um agente de cliente para outro computador

Se quiser executar o agente local num computador diferente que atualmente em, efetue os seguintes procedimentos:

1. Instale o agente no computador pretendido.
2. Inicie sessão no portal de sincronização de dados SQL e voltar a gerar uma chave de agente para o novo agente.
3. Utilize de IU o novo agente para submeter a nova chave de agente.
4. Aguarde enquanto o agente do cliente transfere a lista de bases de dados no local que foram registados anteriormente.
5. Forneça credenciais de base de dados para todas as bases de dados que são apresentados como inacessível. Esses bancos de dados tem de ser acessíveis a partir do novo computador no qual o agente está instalado.

## <a name="agent-tshoot"></a> Resolver problemas de agente de sincronização de dados

- [O agente do cliente de instalar, desinstalar ou repare a falha](#agent-install)

- [O agente do cliente não funciona depois de cancelar a desinstalação](#agent-uninstall)

- [Meu banco de dados não está listado na lista de agente](#agent-list)

- [Agente de cliente não começa (erro 1069)](#agent-start)

- [Não consigo submeter a chave de agente](#agent-key)

- [Não é possível eliminar o agente do cliente no portal se é possível contactar o seu banco de dados associadas no local](#agent-delete)

- [Aplicação local do agente de sincronização não é possível ligar ao serviço de sincronização local](#agent-connect)

### <a name="agent-install"></a> O agente do cliente de instalar, desinstalar ou repare a falha

- **Causa**. Muitos cenários poderão causar esta falha. Para determinar a causa específica para esta falha, consulte os registos.

- **Resolução**. Para encontrar a causa específica da falha, gerar e ver os registos do instalador do Windows. Pode ativar o registo no prompt de comando. Por exemplo, se o ficheiro de instalação transferido é `SQLDataSyncAgent-2.0-x86-ENU.msi`, gerar e examinar os ficheiros de registo utilizando as seguintes linhas de comando:

  - Para instalações: `msiexec.exe /i SQLDataSyncAgent-2.0-x86-ENU.msi /l*v LocalAgentSetup.Log`
  - Para desinstala: `msiexec.exe /x SQLDataSyncAgent-2.0-x86-ENU.msi /l*v LocalAgentSetup.Log`

    Também pode ativar o registo para todas as instalações que são executadas pelo Windows Installer. O artigo da Base de dados de conhecimento da Microsoft [como ativar o registo do Windows Installer](https://support.microsoft.com/help/223300/how-to-enable-windows-installer-logging) fornece uma solução de um clique para ativar o registo para o Windows Installer. Ele também fornece a localização dos registos.

### <a name="agent-uninstall"></a> O agente do cliente não funciona depois de cancelar a desinstalação

O agente do cliente não funciona, mesmo depois de cancelar a sua desinstalação.

- **Causa**. Isto ocorre porque o agente de cliente de sincronização de dados SQL não armazena as credenciais.

- **Resolução**. Pode experimentar essas duas soluções:

    -   Utilize o Services. msc para reintroduzir as credenciais para o agente do cliente.
    -   Desinstalar este agente de cliente e, em seguida, instale um novo. Transfira e instale o agente de cliente mais recente do [Centro de transferências](https://go.microsoft.com/fwlink/?linkid=221479).

### <a name="agent-list"></a> Meu banco de dados não está listado na lista de agente

Quando tentar adicionar uma base de dados do SQL Server existente para um grupo de sincronização, a base de dados não aparece na lista de agentes.

Estes cenários podem fazer com que este problema:

- **Causa**. O grupo de agente e sincronização de cliente estiverem em datacenters diferentes.

- **Resolução**. O agente do cliente e o grupo de sincronização tem de estar no mesmo datacenter. Para configurar estas definições, tem duas opções:

    -   Crie um novo agente no Centro de dados onde está localizado o grupo de sincronização. Em seguida, registe-se a base de dados com que o agente.
    -   Elimine o grupo de sincronização atual. Em seguida, voltar a criar o grupo de sincronização no Centro de dados onde o agente está localizado.

- **Causa**. Lista do agente de cliente de bases de dados não é atual.

- **Resolução**. Pare e reinicie o serviço de agente do cliente.

    O agente local transfere a lista de bases de dados associadas apenas no primeiro envio da chave de agente. Ele não é transferido a lista de bases de dados associadas em submissões de chave de agente subsequentes. Bases de dados que são registrados durante uma transferência do agente não aparecem na instância original do agente.

### <a name="agent-start"></a> Agente de cliente não começa (erro 1069)

Descobre que o agente não está em execução num computador que aloja o SQL Server. Quando tenta iniciar manualmente o agente, verá uma caixa de diálogo que apresenta a mensagem "erro 1069: O serviço não foram iniciados devido a uma falha de início de sessão."

![Caixa de diálogo de erro 1069 de sincronização de dados](media/sql-database-troubleshoot-data-sync/sync-error-1069.png)

- **Causa**. Uma causa provável deste erro é que a palavra-passe no servidor local foi alterado, uma vez que criar o agente e a palavra-passe do agente.

- **Resolução**. Atualize palavra-passe o agente para a sua palavra-passe atual do servidor:

  1. Localize o serviço de agente de cliente de sincronização de dados SQL.  
    a. Selecione **iniciar**.  
    b. Na caixa de pesquisa, introduza **Services. msc**.  
    c. Nos resultados da pesquisa, selecione **serviços**.  
    d. Na **serviços** janela, desloque-se para a entrada para **agente de sincronização de dados SQL**.  
  1. Com o botão direito **agente de sincronização de dados SQL**e, em seguida, selecione **parar**.
  1. Com o botão direito **agente de sincronização de dados SQL**e, em seguida, selecione **propriedades**.
  1. No **das propriedades do agente de sincronização de dados do SQL**, selecione a **iniciar sessão** separador.
  1. Na **palavra-passe** , introduza a palavra-passe.
  1. Na **Confirmar palavra-passe** caixa, reintroduza a palavra-passe.
  1. Selecione **Apply** (Aplicar) e **OK**.
  1. Na **serviços** janela, com o botão direito a **agente de sincronização de dados SQL** serviço e, em seguida, clique em **iniciar**.
  1. Fechar o **serviços** janela.

### <a name="agent-key"></a> Não consigo submeter a chave de agente

Depois de criar ou voltar a criar uma chave para um agente, tentar enviar a chave do SqlAzureDataSyncAgent de aplicação. O envio de mensagens em fila não consegue concluir.

![Sincronizar a caixa de diálogo de erro - não é possível enviar a chave de agente](media/sql-database-troubleshoot-data-sync/sync-error-cant-submit-agent-key.png)

- **Pré-requisitos**. Antes de prosseguir, verifique os pré-requisitos seguintes:

  - O serviço Windows de sincronização de dados SQL está em execução.

  - A conta de serviço do serviço Windows de sincronização de dados SQL tem acesso à rede.

  - A saída através da porta 1433 é aberto na sua regra de local firewall.

  - O ip local é adicionado para o servidor ou a regra de firewall de base de dados para a base de dados de metadados de sincronização.

- **Causa**. A chave de agente identifica exclusivamente cada agente local. A chave tem de cumprir duas condições:

  -   A chave de agente de cliente no servidor de sincronização de dados SQL e o computador local tem de ser idêntica.
  -   A chave de agente do cliente pode ser utilizada apenas uma vez.

- **Resolução**. Se o agente não está a funcionar, é porque uma ou ambas estas condições não forem cumpridas. Para obter o seu agente para funcionar novamente:

  1. Gere uma nova chave.
  1. Aplicam-se a nova chave para o agente.

  Para aplicar a nova chave para o agente:

  1. No Explorador de ficheiros, aceda ao seu diretório de instalação do agente. O diretório de instalação predefinido é c:\\Program Files (x86)\\sincronização de dados do Microsoft SQL.
  1. Faça duplo clique no subdiretório bin.
  1. Abra a aplicação de SqlAzureDataSyncAgent.
  1. Selecione **submeter a chave de agente**.
  1. No espaço fornecido, cole a chave da área de transferência.
  1. Selecione **OK**.
  1. Feche o programa.

### <a name="agent-delete"></a> Não é possível eliminar o agente do cliente no portal se é possível contactar o seu banco de dados associadas no local

Se um ponto final local (ou seja, uma base de dados) que está registado com um agente de cliente de sincronização de dados SQL ficar inacessível, não é possível eliminar o agente do cliente.

- **Causa**. Não é possível eliminar o agente local porque a base de dados inacessível ainda está registado com o agente. Ao tentar eliminar o agente, o processo de eliminação tenta aceder a base de dados, o que ocorre uma falha.

- **Resolução**. Utilize "forçar a eliminação" para eliminar a base de dados inacessível.

> [!NOTE]
> Se tabelas de metadados de sincronização permanecerem depois de uma "eliminação de forçar," utilização `deprovisioningutil.exe` limpá-los.

### <a name="agent-connect"></a> Aplicação local do agente de sincronização não é possível ligar ao serviço de sincronização local

- **Resolução**. Experimente os passos seguintes:

  1. Sair da aplicação.  
  1. Abra o painel de serviços de componente.  
    a. Na caixa de pesquisa na barra de tarefas, introduza **Services. msc**.  
    b. Nos resultados da pesquisa, faça duplo clique em **serviços**.  
  1. Parar o **sincronização de dados SQL** serviço.
  1. Reinicie o **sincronização de dados SQL** serviço.  
  1. Reabrir a aplicação.

## <a name="run-the-data-sync-agent-from-the-command-prompt"></a>Execute o agente de sincronização de dados na linha de comandos

Pode executar os seguintes comandos do agente de sincronização de dados na linha de comandos:

### <a name="ping-the-service"></a>O serviço de ping

#### <a name="usage"></a>Utilização

```cmd
SqlDataSyncAgentCommand.exe -action pingsyncservice
```

#### <a name="example"></a>Exemplo

```cmd
SqlDataSyncAgentCommand.exe -action "pingsyncservice"
```

### <a name="display-registered-databases"></a>Exibir bancos de dados registados

#### <a name="usage"></a>Utilização

```cmd
SqlDataSyncAgentCommand.exe -action displayregistereddatabases
```

#### <a name="example"></a>Exemplo

```cmd
SqlDataSyncAgentCommand.exe -action "displayregistereddatabases"
```

### <a name="submit-the-agent-key"></a>Submeter a chave de agente

#### <a name="usage"></a>Utilização

```cmd
Usage: SqlDataSyncAgentCommand.exe -action submitagentkey -agentkey [agent key]  -username [user name] -password [password]
```

#### <a name="example"></a>Exemplo

```cmd
SqlDataSyncAgentCommand.exe -action submitagentkey -agentkey [agent key generated from portal, PowerShell, or API] -username [user name to sync metadata database] -password [user name to sync metadata database]
```

### <a name="register-a-database"></a>Registe-se de uma base de dados

#### <a name="usage"></a>Utilização

```cmd
SqlDataSyncAgentCommand.exe -action registerdatabase -servername [on-premisesdatabase server name] -databasename [on-premisesdatabase name]  -username [domain\\username] -password [password] -authentication [sql or windows] -encryption [true or false]
```

#### <a name="examples"></a>Exemplos

```cmd
SqlDataSyncAgentCommand.exe -action "registerdatabase" -serverName localhost -databaseName testdb -authentication sql -username <user name> -password <password> -encryption true

SqlDataSyncAgentCommand.exe -action "registerdatabase" -serverName localhost -databaseName testdb -authentication windows -encryption true

```

### <a name="unregister-a-database"></a>Anular o registo de uma base de dados

Quando utilizar este comando para anular o registo de uma base de dados, ele desprovisiona completamente o banco de dados. Se a base de dados no qual participam de outros grupos de sincronização, esta operação quebra os outros grupos de sincronização.

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

## <a name="next-steps"></a>Passos Seguintes

Para mais informações sobre a sincronização de dados SQL, veja os artigos seguintes:

-   Descrição geral - [sincronizar dados em várias bases de dados na cloud e no local com sincronização de dados SQL do Azure](sql-database-sync-data.md)
-   Configurar a sincronização de dados
    - No portal - [Tutorial: Configurar a sincronização de dados SQL para sincronizar dados entre a base de dados do Azure SQL e SQL Server no local](sql-database-get-started-sql-data-sync.md)
    - Com o PowerShell
        -  [Utilizar o PowerShell para sincronizar entre várias bases de dados SQL do Azure](scripts/sql-database-sync-data-between-sql-databases.md)
        -  [Utilizar o PowerShell para sincronizar entre uma Base de Dados SQL do Azure e uma base de dados do SQL Server no local](scripts/sql-database-sync-data-between-azure-onprem.md)
-   Melhores práticas - [melhores práticas para a sincronização de dados SQL do Azure](sql-database-best-practices-data-sync.md)
-   Monitor de - [registos de monitorizar a sincronização de dados de SQL com o Azure Monitor](sql-database-sync-monitor-oms.md)
-   Resolução de problemas - [solucionar problemas com a sincronização de dados SQL do Azure](sql-database-troubleshoot-data-sync.md)
-   Atualizar o esquema de sincronização
    -   Com o Transact-SQL - [automatizar a replicação das alterações de esquema na sincronização de dados SQL do Azure](sql-database-update-sync-schema.md)
    -   Com o PowerShell - [utilize o PowerShell para atualizar o esquema de sincronização num grupo de sincronização existente](scripts/sql-database-sync-update-schema.md)
