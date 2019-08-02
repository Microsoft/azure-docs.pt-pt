---
title: Agente de sincronização de dados para o Azure Sincronização de Dados SQL | Microsoft Docs
description: Saiba como instalar e executar o agente de sincronização de dados para o Sincronização de Dados SQL do Azure para sincronizar dados com bancos de SQL Server do local
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: allenwux
ms.author: xiwu
ms.reviewer: carlrab
ms.date: 12/20/2018
ms.openlocfilehash: de7858be4ac4e392b4fb92cacf55882378ba9813
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68568974"
---
# <a name="data-sync-agent-for-azure-sql-data-sync"></a>Agente de sincronização de dados para Sincronização de Dados SQL do Azure

Sincronize os dados com bancos de SQL Server locais Instalando e Configurando o agente de sincronização de dados para o Sincronização de Dados SQL do Azure. Para obter mais informações sobre Sincronização de Dados SQL, consulte [sincronizar dados entre vários bancos de dado de nuvem e locais com sincronização de dados SQL](sql-database-sync-data.md).

> [!IMPORTANT]
> O Azure Sincronização de Dados SQL não **oferece suporte a** instância gerenciada do banco de dados SQL do Azure no momento.

## <a name="download-and-install"></a>Transferir e instalar

Para baixar o agente de sincronização de dados, acesse [SQL Azure agente de sincronização de dados](https://www.microsoft.com/download/details.aspx?id=27693).

### <a name="install-silently"></a>Instalar silenciosamente

Para instalar o agente de sincronização de dados silenciosamente no prompt de comando, insira um comando semelhante ao exemplo a seguir. Verifique o nome do arquivo baixado. msi e forneça seus próprios valores para os argumentos **TARGETDIR** e Service **Account** .

- Se você não fornecer um valor de **TARGETDIR**, o valor padrão será `C:\Program Files (x86)\Microsoft SQL Data Sync 2.0`.

- Se você fornecer `LocalSystem` como o valor de uma **conta**, use SQL Server autenticação ao configurar o agente para se conectar ao SQL Server local.

- Se você fornecer uma conta de usuário de domínio ou uma conta de usuário local como o valor de uma **conta, você**também precisará fornecer a senha com o argumento userPassword. Por exemplo, `SERVICEACCOUNT="<domain>\<user>"  SERVICEPASSWORD="<password>"`.

```cmd
msiexec /i "SQLDataSyncAgent-2.0-x86-ENU.msi" TARGETDIR="C:\Program Files (x86)\Microsoft SQL Data Sync 2.0" SERVICEACCOUNT="LocalSystem" /qn
```

## <a name="sync-data-with-sql-server-on-premises"></a>Sincronizar dados com SQL Server local

Para configurar o agente de sincronização de dados para que você possa sincronizar dados com um ou mais bancos de SQL Server do local, consulte [Adicionar um banco de SQL Server local](sql-database-get-started-sql-data-sync.md#add-on-prem).

## <a name="agent-faq"></a>Perguntas frequentes do agente de sincronização de dados

### <a name="why-do-i-need-a-client-agent"></a>Por que preciso de um agente cliente

O serviço Sincronização de Dados SQL se comunica com SQL Server bancos de dados por meio do agente cliente. Esse recurso de segurança impede a comunicação direta com bancos de dados por trás de um firewall. Quando o serviço de Sincronização de Dados SQL se comunica com o agente, ele faz isso usando conexões criptografadas e um token exclusivo ou uma *chave de agente*. Os bancos de dados do SQL Server autenticam o agente usando a cadeia de conexão e a chave do agente. Esse design fornece um alto nível de segurança para seus dados.

### <a name="how-many-instances-of-the-local-agent-ui-can-be-run"></a>Quantas instâncias da interface do usuário do agente local podem ser executadas

Somente uma instância da interface do usuário pode ser executada.

### <a name="how-can-i-change-my-service-account"></a>Como posso alterar minha conta de serviço

Depois de instalar um agente cliente, a única maneira de alterar a conta de serviço é desinstalá-lo e instalar um novo agente cliente com a nova conta de serviço.

### <a name="how-do-i-change-my-agent-key"></a>Como fazer alterar minha chave do agente

Uma chave de agente só pode ser usada uma vez por um agente. Ele não pode ser reutilizado quando você remove e reinstala um novo agente, nem pode ser usado por vários agentes. Se você precisar criar uma nova chave para um agente existente, deverá ter certeza de que a mesma chave é registrada com o agente cliente e com o serviço de Sincronização de Dados SQL.

### <a name="how-do-i-retire-a-client-agent"></a>Como fazer desativar um agente cliente

Para invalidar ou desativar imediatamente um agente, gere novamente sua chave no portal, mas não a envie na interface do usuário do agente. A regeneração de uma chave invalida a chave anterior, independentemente de o agente correspondente estar online ou offline.

### <a name="how-do-i-move-a-client-agent-to-another-computer"></a>Como fazer mover um agente cliente para outro computador

Se você quiser executar o agente local de um computador diferente do que está no momento, siga estas etapas:

1. Instale o agente no computador desejado.
2. Faça logon no portal de Sincronização de Dados SQL e regenere uma chave de agente para o novo agente.
3. Use a interface do usuário do novo agente para enviar a nova chave do agente.
4. Aguarde enquanto o agente cliente baixa a lista de bancos de dados locais que foram registrados anteriormente.
5. Forneça as credenciais de banco de dados para todos os bancos que exibirem como inacessíveis. Esses bancos de dados devem ser acessíveis do novo computador no qual o agente está instalado.

## <a name="agent-tshoot"></a>Solucionar problemas do agente de sincronização de dados

- [A instalação, desinstalação ou reparo do agente cliente falha](#agent-install)

- [O agente cliente não funciona depois de cancelar a desinstalação](#agent-uninstall)

- [Meu banco de dados não está listado na lista de agentes](#agent-list)

- [O agente cliente não é iniciado (erro 1069)](#agent-start)

- [Não consigo enviar a chave do agente](#agent-key)

- [O agente cliente não poderá ser excluído do portal se seu banco de dados local associado estiver inacessível](#agent-delete)

- [O aplicativo do agente de sincronização local não pode se conectar ao serviço de sincronização local](#agent-connect)

### <a name="agent-install"></a>A instalação, desinstalação ou reparo do agente cliente falha

- **Causa**. Muitos cenários podem causar essa falha. Para determinar a causa específica dessa falha, examine os logs.

- **Resolução**. Para encontrar a causa específica da falha, gere e examine os logs de Windows Installer. Você pode ativar o registro em log em um prompt de comando. Por exemplo, se o arquivo de instalação baixado `SQLDataSyncAgent-2.0-x86-ENU.msi`for, gere e examine os arquivos de log usando as seguintes linhas de comando:

  - Para instalações:`msiexec.exe /i SQLDataSyncAgent-2.0-x86-ENU.msi /l*v LocalAgentSetup.Log`
  - Para desinstalações:`msiexec.exe /x SQLDataSyncAgent-2.0-x86-ENU.msi /l*v LocalAgentSetup.Log`

    Você também pode ativar o registro em log para todas as instalações executadas pelo Windows Installer. O artigo da base de dados de conhecimento Microsoft [como habilitar o log de Windows Installer](https://support.microsoft.com/help/223300/how-to-enable-windows-installer-logging) fornece uma solução com um clique para ativar o registro em log de Windows Installer. Ele também fornece o local dos logs.

### <a name="agent-uninstall"></a>O agente cliente não funciona depois de cancelar a desinstalação

O agente cliente não funciona, mesmo depois de cancelar a desinstalação.

- **Causa**. Isso ocorre porque o agente cliente do Sincronização de Dados SQL não armazena credenciais.

- **Resolução**. Você pode experimentar estas duas soluções:

    -   Use Services. msc para reinserir as credenciais para o agente cliente.
    -   Desinstale este agente cliente e instale um novo. Baixe e instale o agente cliente mais recente no [centro de download](https://go.microsoft.com/fwlink/?linkid=221479).

### <a name="agent-list"></a>Meu banco de dados não está listado na lista de agentes

Quando você tenta adicionar um banco de dados SQL Server existente a um grupo de sincronização, o banco de dados não aparece na lista de agentes.

Esses cenários podem causar esse problema:

- **Causa**. O agente cliente e o grupo de sincronização estão em data centers diferentes.

- **Resolução**. O agente cliente e o grupo de sincronização devem estar no mesmo datacenter. Para configurar isso, você tem duas opções:

    -   Crie um novo agente no datacenter no qual o grupo de sincronização está localizado. Em seguida, registre o banco de dados com esse agente.
    -   Exclua o grupo de sincronização atual. Em seguida, crie novamente o grupo de sincronização no datacenter onde o agente está localizado.

- **Causa**. A lista de bancos de dados do agente cliente não é atual.

- **Resolução**. Pare e reinicie o serviço de agente cliente.

    O agente local baixa a lista de bancos de dados associados somente no primeiro envio da chave do agente. Ele não baixa a lista de bancos de dados associados em envios de chave de agente subsequentes. Os bancos de dados registrados durante uma movimentação de agente não aparecem na instância do agente original.

### <a name="agent-start"></a>O agente cliente não é iniciado (erro 1069)

Você descobre que o agente não está em execução em um computador que hospeda SQL Server. Ao tentar iniciar o agente manualmente, você verá uma caixa de diálogo que exibe a mensagem "erro 1069: O serviço não foi iniciado devido a uma falha de logon. "

![Caixa de diálogo erro de sincronização de dados 1069](media/sql-database-troubleshoot-data-sync/sync-error-1069.png)

- **Causa**. Uma causa provável desse erro é que a senha no servidor local foi alterada desde que você criou a senha do agente e do agente.

- **Resolução**. Atualize a senha do agente para a senha atual do servidor:

  1. Localize o serviço do agente cliente do Sincronização de Dados SQL.  
    a. Selecione **iniciar**.  
    b. Na caixa de pesquisa, digite **Services. msc**.  
    c. Nos resultados da pesquisa, selecione **Serviços**.  
    d. Na janela **Serviços** , role até a entrada para **sincronização de dados SQL agente**.  
  1. Clique com o botão direito do mouse em **sincronização de dados SQL Agent**e selecione **parar**.
  1. Clique com o botão direito do mouse em **sincronização de dados SQL Agent**e selecione **Propriedades**.
  1. Em **Propriedades do agente sincronização de dados SQL**, selecione a guia **fazer logon** .
  1. Na caixa **senha** , digite sua senha.
  1. Na caixa **Confirmar senha** , insira novamente sua senha.
  1. Selecione **Apply** (Aplicar) e **OK**.
  1. Na janela **Serviços** , clique com o botão direito do mouse no serviço do **agente de sincronização de dados SQL** e clique em **Iniciar**.
  1. Feche a janela **Serviços** .

### <a name="agent-key"></a>Não consigo enviar a chave do agente

Depois de criar ou recriar uma chave para um agente, você tenta enviar a chave por meio do aplicativo SqlAzureDataSyncAgent. O envio não é concluído.

![Caixa de diálogo erro de sincronização-não é possível enviar a chave do agente](media/sql-database-troubleshoot-data-sync/sync-error-cant-submit-agent-key.png)

- **Pré-requisitos**. Antes de prosseguir, verifique os seguintes pré-requisitos:

  - O serviço do Windows Sincronização de Dados SQL está em execução.

  - A conta de serviço para Sincronização de Dados SQL serviço Windows tem acesso à rede.

  - A porta de saída 1433 está aberta na regra de firewall local.

  - O IP local é adicionado à regra de firewall de servidor ou de banco de dados para o banco de dados de metadados de sincronização.

- **Causa**. A chave do agente identifica exclusivamente cada agente local. A chave deve atender a duas condições:

  -   A chave do agente cliente no servidor de Sincronização de Dados SQL e o computador local devem ser idênticas.
  -   A chave do agente cliente pode ser usada apenas uma vez.

- **Resolução**. Se o agente não estiver funcionando, é porque uma ou ambas as condições não foram atendidas. Para fazer com que o agente funcione novamente:

  1. Gere uma nova chave.
  1. Aplique a nova chave ao agente.

  Para aplicar a nova chave ao agente:

  1. No explorador de arquivos, vá para o diretório de instalação do agente. O diretório de instalação padrão é C\\: Arquivos de programas (\\x86) Microsoft sincronização de dados SQL.
  1. Clique duas vezes no subdiretório bin.
  1. Abra o aplicativo SqlAzureDataSyncAgent.
  1. Selecione **Enviar chave do agente**.
  1. No espaço fornecido, Cole a chave da área de transferência.
  1. Selecione **OK**.
  1. Feche o programa.

### <a name="agent-delete"></a>O agente cliente não poderá ser excluído do portal se seu banco de dados local associado estiver inacessível

Se um ponto de extremidade local (ou seja, um banco de dados) registrado com um agente cliente do Sincronização de Dados SQL ficar inacessível, o agente cliente não poderá ser excluído.

- **Causa**. O agente local não pode ser excluído porque o banco de dados inacessível ainda está registrado com o agente. Quando você tenta excluir o agente, o processo de exclusão tenta acessar o banco de dados, o que falha.

- **Resolução**. Use "forçar exclusão" para excluir o banco de dados inacessível.

> [!NOTE]
> Se as tabelas de metadados de sincronização permanecerem após uma "exclusão `deprovisioningutil.exe` forçada", use para limpá-las.

### <a name="agent-connect"></a>O aplicativo do agente de sincronização local não pode se conectar ao serviço de sincronização local

- **Resolução**. Experimente os passos seguintes:

  1. Saia do aplicativo.  
  1. Abra o painel serviços de componentes.  
    a. Na caixa de pesquisa na barra de tarefas, digite **Services. msc**.  
    b. Nos resultados da pesquisa, clique duas vezes em **Serviços**.  
  1. Pare o serviço **sincronização de dados SQL** .
  1. Reinicie o serviço **sincronização de dados SQL** .  
  1. Reabrir a aplicação.

## <a name="run-the-data-sync-agent-from-the-command-prompt"></a>Executar o agente de sincronização de dados no prompt de comando

Você pode executar os seguintes comandos do agente de sincronização de dados no prompt de comando:

### <a name="ping-the-service"></a>Executar ping no serviço

#### <a name="usage"></a>Utilização

```cmd
SqlDataSyncAgentCommand.exe -action pingsyncservice
```

#### <a name="example"></a>Exemplo

```cmd
SqlDataSyncAgentCommand.exe -action "pingsyncservice"
```

### <a name="display-registered-databases"></a>Exibir bancos de dados registrados

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

### <a name="register-a-database"></a>Registrar um banco de dados

#### <a name="usage"></a>Utilização

```cmd
SqlDataSyncAgentCommand.exe -action registerdatabase -servername [on-premisesdatabase server name] -databasename [on-premisesdatabase name]  -username [domain\\username] -password [password] -authentication [sql or windows] -encryption [true or false]
```

#### <a name="examples"></a>Exemplos

```cmd
SqlDataSyncAgentCommand.exe -action "registerdatabase" -serverName localhost -databaseName testdb -authentication sql -username <user name> -password <password> -encryption true

SqlDataSyncAgentCommand.exe -action "registerdatabase" -serverName localhost -databaseName testdb -authentication windows -encryption true

```

### <a name="unregister-a-database"></a>Cancelar o registro de um banco de dados

Quando você usa esse comando para cancelar o registro de um banco de dados, ele desprovisiona completamente o banco de dados. Se o banco de dados participar de outros grupos de sincronização, essa operação interromperá os outros grupos de sincronização.

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

Para obter mais informações sobre Sincronização de Dados SQL, consulte os seguintes artigos:

-   Visão geral – [sincronizar dados entre vários bancos de dados locais e de nuvem com o Azure sincronização de dados SQL](sql-database-sync-data.md)
-   Configurar a sincronização de dados
    - No portal- [tutorial: Configurar o Sincronização de Dados SQL para sincronizar dados entre o Azure SQL Database e o SQL Server local](sql-database-get-started-sql-data-sync.md)
    - Com o PowerShell
        -  [Utilizar o PowerShell para sincronizar entre várias bases de dados SQL do Azure](scripts/sql-database-sync-data-between-sql-databases.md)
        -  [Utilizar o PowerShell para sincronizar entre uma Base de Dados SQL do Azure e uma base de dados do SQL Server no local](scripts/sql-database-sync-data-between-azure-onprem.md)
-   Práticas recomendadas- [práticas recomendadas para o Azure sincronização de dados SQL](sql-database-best-practices-data-sync.md)
-   Monitorar [sincronização de dados SQL monitorar com Azure monitor logs](sql-database-sync-monitor-oms.md)
-   Solucionar problemas- [solucionar problema com o Azure sincronização de dados SQL](sql-database-troubleshoot-data-sync.md)
-   Atualizar o esquema de sincronização
    -   Com o Transact-SQL – [Automatize a replicação de alterações de esquema no Azure sincronização de dados SQL](sql-database-update-sync-schema.md)
    -   Com o PowerShell- [use o PowerShell para atualizar o esquema de sincronização em um grupo de sincronização existente](scripts/sql-database-sync-update-schema.md)
