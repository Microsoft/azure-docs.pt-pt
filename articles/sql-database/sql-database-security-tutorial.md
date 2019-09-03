---
title: Proteger um banco de dados individual ou em pool no banco de dados SQL do Azure | Microsoft Docs
description: Um tutorial que ensina sobre técnicas e recursos para proteger um banco de dados individual ou em pool no banco de dados SQL do Azure.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.topic: tutorial
author: VanMSFT
ms.author: vanto
ms.reviewer: carlrab
ms.date: 09/03/2019
ms.custom: seoapril2019
ms.openlocfilehash: ba648a2bf563b775c39f11ab8d5c4069c4bf740f
ms.sourcegitcommit: 2aefdf92db8950ff02c94d8b0535bf4096021b11
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/03/2019
ms.locfileid: "70231188"
---
# <a name="tutorial-secure-a-single-or-pooled-database"></a>Tutorial: Proteger um banco de dados individual ou em pool

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> - Criar regras de firewall no nível de servidor e de banco de dados
> - Configurar um administrador do Azure Active Directory (AD)
> - Gerenciar o acesso do usuário com autenticação SQL, autenticação do Azure AD e cadeias de conexão segura
> - Habilitar recursos de segurança, como segurança avançada de dados, auditoria, máscara de dados e criptografia

O banco de dados SQL do Azure protege os dados em um único ou em pool, permitindo que você:

- Limitar o acesso usando regras de firewall
- Usar mecanismos de autenticação que exigem identidade
- Usar a autorização com associações baseadas em função e permissões
- Habilitar recursos de segurança

> [!NOTE]
> Um banco de dados SQL do Azure em uma instância gerenciada é protegido usando regras de segurança de rede e pontos de extremidade privados, conforme descrito na arquitetura gerenciada de [conectividade](sql-database-managed-instance-connectivity-architecture.md)e [instância do banco de dados SQL do Azure](sql-database-managed-instance-index.yml) .

Para saber mais, confira os artigos visão geral e [recursos](sql-database-security-overview.md) de [segurança do banco de dados SQL do Azure](/azure/sql-database/sql-database-security-index) .

> [!TIP]
> O módulo Microsoft Learn a seguir ajuda você a aprender gratuitamente sobre como [proteger seu banco de dados SQL do Azure](https://docs.microsoft.com/learn/modules/secure-your-azure-sql-database/).

## <a name="prerequisites"></a>Pré-requisitos

Para concluir o tutorial, verifique se você tem os seguintes pré-requisitos:

- [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms)
- Um servidor e banco de dados SQL do Azure
  - Crie-os com [portal do Azure](sql-database-single-database-get-started.md), [CLI](sql-database-cli-samples.md)ou [PowerShell](sql-database-powershell-samples.md)

Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Para todas as etapas no tutorial, entre no [portal do Azure](https://portal.azure.com/)

## <a name="create-firewall-rules"></a>Criar regras da firewall

Os bancos de dados SQL são protegidos por firewalls no Azure. Por padrão, todas as conexões com o servidor e o banco de dados são rejeitadas. Para saber mais, confira [regras de firewall no nível de banco de dados e nível de servidor do Azure SQL Database](sql-database-firewall-configure.md).

Defina **permitir acesso aos serviços do Azure** como **desativado** para a configuração mais segura. Em seguida, crie um [IP reservado (implantação clássica)](../virtual-network/virtual-networks-reserved-public-ip.md) para o recurso que precisa se conectar, como uma VM do Azure ou um serviço de nuvem, e permita somente o acesso de endereço IP por meio do firewall. Se você estiver usando o modelo de implantação do [Gerenciador de recursos](/azure/virtual-network/virtual-network-ip-addresses-overview-arm) , um endereço IP público dedicado será necessário para cada recurso.

> [!NOTE]
> A Base de Dados SQL comunica através da porta 1433. Se você estiver tentando se conectar de dentro de uma rede corporativa, o tráfego de saída pela porta 1433 pode não ser permitido pelo firewall da sua rede. Nesse caso, você não pode se conectar ao servidor do banco de dados SQL do Azure, a menos que o administrador Abra a porta 1433.

### <a name="set-up-sql-database-server-firewall-rules"></a>Configurar regras de firewall do servidor do banco de dados SQL

As regras de firewall de IP de nível de servidor se aplicam a todos os bancos de dados no mesmo servidor de banco de dados SQL.

Para configurar uma regra de firewall no nível de servidor:

1. Em portal do Azure, selecione **bancos** de dados SQL no menu à esquerda e selecione seu banco de dados na página **bancos SQL** .

    ![regra de firewall do servidor](./media/sql-database-security-tutorial/server-name.png)

    > [!NOTE]
    > Certifique-se de copiar seu nome de servidor totalmente qualificado (como *yourserver.Database.Windows.net*) para uso posterior no tutorial.

1. Na página **visão geral** , selecione **definir Firewall do servidor**. A página **configurações de firewall** do servidor de banco de dados é aberta.

   1. Selecione **Adicionar IP do cliente** na barra de ferramentas para adicionar o endereço IP atual a uma nova regra de firewall. A regra pode abrir a porta 1433 para um único endereço IP ou um intervalo de endereços IP. Selecione **Guardar**.

      ![configurar regra de firewall do servidor](./media/sql-database-security-tutorial/server-firewall-rule2.png)

   1. Selecione **OK** e feche a página **configurações de firewall** .

Agora, já pode ligar a qualquer base de dados do servidor com o endereço IP ou o intervalo de endereços IP especificado.

### <a name="setup-database-firewall-rules"></a>Regras de firewall de banco de dados de instalação

As regras de firewall no nível de banco de dados se aplicam somente a bancos de dados individuais. O banco de dados reterá essas regras durante um failover de servidor. As regras de firewall no nível de banco de dados só podem ser configuradas usando instruções Transact-SQL (T-SQL) e somente depois que você tiver configurado uma regra de firewall no nível de servidor.

Para configurar uma regra de firewall no nível de banco de dados:

1. Conecte-se ao banco de dados, por exemplo, usando [SQL Server Management Studio](./sql-database-connect-query-ssms.md).

1. No Pesquisador de **objetos**, clique com o botão direito do mouse no banco de dados e selecione **nova consulta**.

1. Na janela de consulta, adicione essa instrução e modifique o endereço IP para seu endereço IP público:

    ```sql
    EXECUTE sp_set_database_firewall_rule N'Example DB Rule','0.0.0.4','0.0.0.4';
    ```

1. Na barra de ferramentas, selecione **executar** para criar a regra de firewall.

> [!NOTE]
> Você também pode criar uma regra de firewall no nível de servidor no SSMS usando o comando [sp_set_firewall_rule](/sql/relational-databases/system-stored-procedures/sp-set-firewall-rule-azure-sql-database?view=azuresqldb-current) , embora você deva estar conectado ao banco de dados *mestre* .

## <a name="create-an-azure-ad-admin"></a>Criar um administrador do Azure AD

Verifique se você está usando o domínio gerenciado do Azure Active Directory (AD) apropriado. Para selecionar o domínio do AD, use o canto superior direito do portal do Azure. Esse processo confirma que a mesma assinatura é usada para o Azure AD e o SQL Server hospedando seu banco de dados SQL do Azure ou data warehouse.

   ![escolher-AD](./media/sql-database-security-tutorial/8choose-ad.png)

Para definir o administrador do Azure AD:

1. No portal do Azure, na página **SQL Server** , selecione **Active Directory admin**. Em seguida, selecione **definir administrador**.

    ![selecionar o active directory](./media/sql-database-security-tutorial/admin-settings.png)  

    > [!IMPORTANT]
    > Você precisa ser um "administrador da empresa" ou "administrador global" para executar essa tarefa.

1. Na página **Adicionar Administrador** , pesquise e selecione o usuário ou grupo do AD e escolha **selecionar**. Todos os membros e grupos de seus Active Directory são listados, e as entradas esmaecidas não têm suporte como administradores do Azure AD. Confira [recursos e limitações do Azure ad](sql-database-aad-authentication.md#azure-ad-features-and-limitations).

    ![selecionar administrador](./media/sql-database-security-tutorial/admin-select.png)

    > [!IMPORTANT]
    > O RBAC (controle de acesso baseado em função) só se aplica ao portal e não é propagado para SQL Server.

1. Na parte superior da página do **administrador do Active Directory** , selecione **salvar**.

    O processo de alteração de um administrador pode levar vários minutos. O novo administrador será exibido na caixa **Active Directory admin** .

> [!NOTE]
> Ao definir um administrador do Azure AD, o novo nome de administrador (usuário ou grupo) não pode existir como um usuário de autenticação SQL Server no banco de dados *mestre* . Se houver, a instalação falhará e reverterá as alterações, indicando que esse nome de administrador já existe. Como o usuário de autenticação SQL Server não faz parte do Azure AD, qualquer esforço para conectar o usuário usando a autenticação do Azure AD falhará.

Para obter informações sobre como configurar o Azure AD, consulte:

- [Integre suas identidades locais ao Azure AD](../active-directory/hybrid/whatis-hybrid-identity.md)
- [Adicionar seu próprio nome de domínio ao Azure AD](../active-directory/active-directory-domains-add-azure-portal.md)
- [O Microsoft Azure agora dá suporte à Federação com o AD do Windows Server](https://azure.microsoft.com/blog/20../../windows-azure-now-supports-federation-with-windows-server-active-directory/)
- [Administrar seu diretório do Azure AD](../active-directory/fundamentals/active-directory-administer.md)
- [Gerenciar o Azure AD usando o PowerShell](/powershell/azure/overview?view=azureadps-2.0)
- [Portas e protocolos necessários à identidade híbrida](../active-directory/hybrid/reference-connect-ports.md)

## <a name="manage-database-access"></a>Gerenciar o acesso ao banco de dados

Gerencie o acesso ao banco de dados adicionando usuários ao banco de dados ou permitindo o acesso do usuário com cadeias de conexão seguras. As cadeias de conexão são úteis para aplicativos externos. Para saber mais, consulte [controle de acesso do SQL do Azure](sql-database-control-access.md) e [autenticação do AD](sql-database-aad-authentication.md).

Para adicionar usuários, escolha o tipo de autenticação de banco de dados:

- **Autenticação do SQL**, usar um nome de usuário e senha para logons e são válidos somente no contexto de um banco de dados específico no servidor

- **Autenticação do Azure ad**, usar identidades gerenciadas pelo Azure AD

### <a name="sql-authentication"></a>Autenticação do SQL

Para adicionar um usuário com autenticação SQL:

1. Conecte-se ao banco de dados, por exemplo, usando [SQL Server Management Studio](./sql-database-connect-query-ssms.md).

1. No Pesquisador de **objetos**, clique com o botão direito do mouse no banco de dados e escolha **nova consulta**.

1. Na janela de consulta, digite o seguinte comando:

    ```sql
    CREATE USER ApplicationUser WITH PASSWORD = 'YourStrongPassword1';
    ```

1. Na barra de ferramentas, selecione **executar** para criar o usuário.

1. Por predefinição, o utilizador pode ligar à base de dados, mas não tem permissões para ler ou escrever dados. Para conceder essas permissões, execute os seguintes comandos em uma nova janela de consulta:

    ```sql
    ALTER ROLE db_datareader ADD MEMBER ApplicationUser;
    ALTER ROLE db_datawriter ADD MEMBER ApplicationUser;
    ```

> [!NOTE]
> Crie contas que não sejam de administrador no nível de banco de dados, a menos que eles precisem executar tarefas de administrador como criar novos usuários.

### <a name="azure-ad-authentication"></a>Autenticação do Azure AD

Azure Active Directory autenticação requer que os usuários do banco de dados sejam criados como contidos. Um usuário de banco de dados independente é mapeado para uma identidade no diretório do Azure AD associado ao banco de dados e não tem logon no banco de dados *mestre* . A identidade do Azure AD pode ser para um usuário individual ou um grupo. Para obter mais informações, consulte [usuários de banco de dados independente, tornar seu banco de dados portátil](https://msdn.microsoft.com/library/ff929188.aspx) e examinar o [tutorial do Azure ad](./sql-database-aad-authentication-configure.md) sobre como autenticar usando o Azure AD.

> [!NOTE]
> Os usuários do banco de dados (exceto administradores) não podem ser criados usando o portal do Azure. As funções do RBAC do Azure não se propagam para servidores SQL, bancos de dados ou data warehouses. Eles são usados apenas para gerenciar recursos do Azure e não se aplicam a permissões de banco de dados.
>
> Por exemplo, a função *colaborador de SQL Server* não concede acesso para se conectar a um banco de dados ou data warehouse. Essa permissão deve ser concedida no banco de dados usando instruções T-SQL.

> [!IMPORTANT]
> Não há suporte para `:` caracteres especiais `&` como dois-pontos ou e comercial em nomes de usuário `CREATE LOGIN` nas `CREATE USER` instruções T-SQL e.

Para adicionar um usuário com a autenticação do Azure AD:

1. Conecte-se ao seu SQL Server do Azure usando uma conta do Azure AD com pelo menos a permissão *ALTER ANY User* .

1. No Pesquisador de **objetos**, clique com o botão direito do mouse no banco de dados e selecione **nova consulta**.

1. Na janela de consulta, digite o seguinte comando e modifique `<Azure_AD_principal_name>` para o nome principal do usuário do Azure ad ou o nome de exibição do grupo do Azure AD:

   ```sql
   CREATE USER <Azure_AD_principal_name> FROM EXTERNAL PROVIDER;
   ```

> [!NOTE]
> Os usuários do Azure ad são marcados nos metadados do banco `E (EXTERNAL_USER)` de dados `X (EXTERNAL_GROUPS)` com tipo e tipo para grupos. Para obter mais informações, consulte [Sys. database_principals](/sql/relational-databases/system-catalog-views/sys-database-principals-transact-sql).

### <a name="secure-connection-strings"></a>Cadeias de conexão seguras

Para garantir uma conexão segura e criptografada entre o aplicativo cliente e o banco de dados SQL, uma cadeia de conexão deve ser configurada para:

- Solicitar uma conexão criptografada
- Não confiar no certificado do servidor

A conexão é estabelecida usando TLS (Transport Layer Security) e reduz o risco de um ataque man-in-the-Middle. As cadeias de conexão estão disponíveis por banco de dados e são pré-configuradas para dar suporte a drivers de cliente como ADO.NET, JDBC, ODBC e PHP. Para obter informações sobre a TLS e a conectividade, veja [Considerações sobre a TLS](sql-database-connect-query.md#tls-considerations-for-sql-database-connectivity).

Para copiar uma cadeia de conexão segura:

1. Em portal do Azure, selecione **bancos** de dados SQL no menu à esquerda e selecione seu banco de dados na página **bancos SQL** .

1. Na página **visão geral** , selecione **Mostrar cadeias de conexão de banco de dados**.

1. Selecione uma guia de driver e copie a cadeia de conexão completa.

    ![Cadeia de ligação de ADO.NET](./media/sql-database-security-tutorial/connection.png)

## <a name="enable-security-features"></a>Habilitar recursos de segurança

O banco de dados SQL do Azure fornece recursos de segurança que são acessados usando o portal do Azure. Esses recursos estão disponíveis tanto para o banco de dados quanto para o servidor, com exceção do mascaramento, que só está disponível no banco de dado. Para saber mais, consulte [segurança de dados avançada](sql-database-advanced-data-security.md), [auditoria](sql-database-auditing.md), [máscara de dados dinâmicos](sql-database-dynamic-data-masking-get-started.md)e [Transparent Data Encryption](transparent-data-encryption-azure-sql.md).

### <a name="advanced-data-security"></a>Advanced Data Security

O recurso de segurança de dados avançado detecta possíveis ameaças à medida que elas ocorrem e fornece alertas de segurança em atividades anormais. Os usuários podem explorar esses eventos suspeitos usando o recurso de auditoria e determinar se o evento foi para acessar, violar ou explorar dados no banco de dado. Os usuários também recebem uma visão geral de segurança que inclui uma avaliação de vulnerabilidades e a ferramenta de detecção e classificação de dados.

> [!NOTE]
> Um exemplo de ameaça é a injeção de SQL, um processo em que os invasores injetam SQL mal-intencionado em entradas de aplicativos. Um aplicativo pode executar inadvertidamente o SQL mal-intencionado e permitir que os invasores acessem ou modifiquem dados no banco de dado.

Para habilitar a segurança de dados avançada:

1. Em portal do Azure, selecione **bancos** de dados SQL no menu à esquerda e selecione seu banco de dados na página **bancos SQL** .

1. Na página **visão geral** , selecione o link **nome do servidor** . A página servidor de banco de dados será aberta.

1. Na página **SQL Server** , localize a seção **segurança** e selecione **segurança de dados avançada**.

   1. Selecione **ativado** em **segurança de dados avançada** para habilitar o recurso. Escolha uma conta de armazenamento para salvar os resultados da avaliação de vulnerabilidade. Em seguida, selecione **Guardar**.

      ![Painel de navegação](./media/sql-database-security-tutorial/threat-settings.png)

      Você também pode configurar emails para receber alertas de segurança, detalhes de armazenamento e tipos de detecção de ameaças.

1. Retorne para a página bancos de dados **SQL** do seu banco de dado e selecione **segurança de recursos avançada** na seção **segurança** . Aqui, você encontrará vários indicadores de segurança disponíveis para o banco de dados.

    ![Status da ameaça](./media/sql-database-security-tutorial/threat-status.png)

Se as atividades anômalas forem detectadas, você receberá um email com informações sobre o evento. Isso inclui a natureza da atividade, do banco de dados, do servidor, da hora do evento, das possíveis causas e das ações recomendadas para investigar e atenuar a ameaça em potencial. Se esse email for recebido, selecione o link **log de auditoria do SQL do Azure** para iniciar o portal do Azure e mostrar registros de auditoria relevantes para a hora do evento.

   ![Mensagem de e-mail sobre deteção de ameaças](./media/sql-database-security-tutorial/threat-email.png)

### <a name="auditing"></a>Auditoria

O recurso de auditoria rastreia eventos de banco de dados e grava eventos em um log de auditoria no armazenamento do Azure, Azure Monitor logs ou em um hub de eventos. A auditoria ajuda a manter a conformidade regulatória, a compreender a atividade do banco de dados e a obter informações sobre discrepâncias e anomalias que podem indicar possíveis violações de segurança.

Para habilitar a auditoria:

1. Em portal do Azure, selecione **bancos** de dados SQL no menu à esquerda e selecione seu banco de dados na página **bancos SQL** .

1. Na seção **segurança** , selecione **auditoria**.

1. Em configurações de **auditoria** , defina os seguintes valores:

   1. Defina **auditoria** como **ativado**.

   1. Selecione o **destino do log de auditoria** como qualquer um dos seguintes:

       - **Armazenamento**, uma conta de armazenamento do Azure em que os logs de eventos são salvos e podem ser baixados como arquivos *. xel*

          > [!TIP]
          > Use a mesma conta de armazenamento para todos os bancos de dados auditados para obter o máximo da auditoria de modelos de relatório.

       - **Log Analytics**, que armazena automaticamente eventos para consulta ou análise adicional

           > [!NOTE]
           > Um **espaço de trabalho log Analytics** é necessário para dar suporte a recursos avançados, como análise, regras de alerta personalizadas e exportações do Excel ou Power bi. Sem um espaço de trabalho, somente o editor de consultas está disponível.

       - **Hub de eventos**, que permite que os eventos sejam roteados para uso em outros aplicativos

   1. Selecione **Guardar**.

      ![Configurações de auditoria](./media/sql-database-security-tutorial/audit-settings.png)

1. Agora, você pode selecionar **Exibir logs de auditoria** para exibir os dados de eventos de Database.

    ![Registos de auditoria](./media/sql-database-security-tutorial/audit-records.png)

> [!IMPORTANT]
> Consulte [auditoria do banco de dados SQL](sql-database-auditing.md) sobre como personalizar ainda mais os eventos de auditoria usando o PowerShell ou a API REST.

### <a name="dynamic-data-masking"></a>Máscara de dados dinâmica

O recurso de mascaramento de dados ocultará automaticamente os dados confidenciais em seu banco de dado.

Para habilitar o mascaramento de dados:

1. Em portal do Azure, selecione **bancos** de dados SQL no menu à esquerda e selecione seu banco de dados na página **bancos SQL** .

1. Na seção **segurança** , selecione **máscara de dados dinâmicos**.

1. Em configurações de **máscara de dados dinâmicos** , selecione **Adicionar máscara** para adicionar uma regra de mascaramento. O Azure preencherá automaticamente os esquemas de banco de dados, tabelas e colunas disponíveis para sua escolha.

    ![Configurações de máscara](./media/sql-database-security-tutorial/mask-settings.png)

1. Selecione **Guardar**. As informações selecionadas agora são mascaradas para privacidade.

    ![Exemplo de máscara](./media/sql-database-security-tutorial/mask-query.png)

### <a name="transparent-data-encryption"></a>Encriptação de Dados Transparente

O recurso de criptografia criptografa automaticamente os dados em repouso e não requer alterações nos aplicativos que acessam o banco de dado criptografado. Para novos bancos de dados, a criptografia está ativada por padrão. Você também pode criptografar dados usando o SSMS e o recurso [Always Encrypted](sql-database-always-encrypted.md) .

Para habilitar ou verificar a criptografia:

1. Em portal do Azure, selecione **bancos** de dados SQL no menu à esquerda e selecione seu banco de dados na página **bancos SQL** .

1. Na seção **segurança** , selecione Transparent **Data Encryption**.

1. Se necessário, defina **criptografia de dados** como **ativado**. Selecione **Guardar**.

    ![Encriptação de Dados Transparente](./media/sql-database-security-tutorial/encryption-settings.png)

> [!NOTE]
> Para exibir o status de criptografia, conecte-se [](./sql-database-connect-query-ssms.md) ao banco de dados `encryption_state` usando o SSMS e consulte a coluna da exibição [Sys. dm _database_encryption_keys](/sql/relational-databases/system-dynamic-management-views/sys-dm-database-encryption-keys-transact-sql) . Um estado de `3` indica que o banco de dados está criptografado.

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, você aprendeu a melhorar a segurança do seu banco de dados com apenas algumas etapas simples. Aprendeu a:

> [!div class="checklist"]
> - Criar regras de firewall no nível de servidor e de banco de dados
> - Configurar um administrador do Azure Active Directory (AD)
> - Gerenciar o acesso do usuário com autenticação SQL, autenticação do Azure AD e cadeias de conexão segura
> - Habilitar recursos de segurança, como segurança avançada de dados, auditoria, máscara de dados e criptografia

Avance para o próximo tutorial para aprender a implementar a distribuição geográfica.

> [!div class="nextstepaction"]
>[Implementar uma base de dados distribuída geograficamente](sql-database-implement-geo-distributed-database.md)
