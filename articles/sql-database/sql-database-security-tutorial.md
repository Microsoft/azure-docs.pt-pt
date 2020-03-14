---
title: Proteja uma única ou agréi de dados
description: Um tutorial que lhe ensina as técnicas e funcionalidades para garantir uma única ou piscina de base de dados na Base de Dados Azure SQL.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.topic: tutorial
author: VanMSFT
ms.author: vanto
ms.reviewer: carlrab
ms.date: 09/03/2019
ms.custom: seoapril2019
ms.openlocfilehash: 9e3c5c12157a007bcad59a78b4623ff4d5a0041f
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79238833"
---
# <a name="tutorial-secure-a-single-or-pooled-database"></a>Tutorial: Proteja uma base de dados única ou reunida

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> - Criar regras de firewall ao nível do servidor e do nível da base de dados
> - Configure um administrador de Diretório Ativo Azure (AD)
> - Gerir o acesso ao utilizador com autenticação SQL, autenticação AD Azure e cordas de ligação seguras
> - Ativar funcionalidades de segurança, tais como segurança avançada de dados, auditoria, mascaramento de dados e encriptação

A Base de Dados Azure SQL assegura dados numa única base de dados ou agrinada, permitindo-lhe:

- Limitar o acesso usando regras de firewall
- Utilize mecanismos de autenticação que requerem identidade
- Utilize a autorização com membros e permissões baseados em papéis
- Ativar funcionalidades de segurança

> [!NOTE]
> Uma base de dados Azure SQL sobre uma instância gerida é protegida usando regras de segurança da rede e pontos finais privados, conforme descrito na base de [dados Azure SQL gerida por instância sql](sql-database-managed-instance-index.yml) e [arquitetura de conectividade.](sql-database-managed-instance-connectivity-architecture.md)

Para saber mais, consulte a visão geral de segurança da Base de [Dados Azure SQL](/azure/sql-database/sql-database-security-index) e artigos de [capacidades.](sql-database-security-overview.md)

> [!TIP]
> O seguinte módulo Microsoft Learn ajuda-o a aprender gratuitamente sobre como proteger a [sua base de dados Azure SQL](https://docs.microsoft.com/learn/modules/secure-your-azure-sql-database/).

## <a name="prerequisites"></a>Pré-requisitos

Para completar o tutorial, certifique-se de que tem os seguintes pré-requisitos:

- [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms)
- Um servidor E base de dados Azure SQL
  - Crie-os com [portal Azure,](sql-database-single-database-get-started.md) [CLI](sql-database-cli-samples.md)ou [PowerShell](sql-database-powershell-samples.md)

Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Para todos os passos no tutorial, inscreva-se no [portal Azure](https://portal.azure.com/)

## <a name="create-firewall-rules"></a>Criar regras da firewall

As bases de dados SQL estão protegidas por firewalls em Azure. Por predefinição, todas as ligações ao servidor e à base de dados são rejeitadas. Para saber mais, consulte as regras de firewall do nível de servidor do [Azure SQL database e](sql-database-firewall-configure.md)do nível de base de dados .

Definir **Permitir o acesso aos serviços Azure** para **OFF** para a configuração mais segura. Em seguida, crie um [IP reservado (implantação clássica)](../virtual-network/virtual-networks-reserved-public-ip.md) para o recurso que precisa de se conectar, como um VM Azure ou um serviço de nuvem, e apenas permita o acesso do endereço IP através da firewall. Se estiver a utilizar o modelo de implementação do gestor de [recursos,](/azure/virtual-network/virtual-network-ip-addresses-overview-arm) é necessário um endereço IP público dedicado para cada recurso.

> [!NOTE]
> A Base de Dados SQL comunica através da porta 1433. Se estiver a tentar ligar-se a partir de uma rede corporativa, o tráfego de saída sobre a porta 1433 pode não ser permitido pela firewall da sua rede. Em caso afirmativo, não pode ligar-se ao servidor de base de dados Azure SQL a menos que o seu administrador abra a porta 1433.

### <a name="set-up-sql-database-server-firewall-rules"></a>Configurar as regras de firewall do servidor de base de dados SQL

As regras de firewall IP ao nível do servidor aplicam-se a todas as bases de dados dentro do mesmo servidor de base de dados SQL.

Para configurar uma regra de firewall ao nível do servidor:

1. No portal Azure, selecione bases de **dados SQL** do menu à esquerda e selecione a sua base de dados na página de bases de **dados SQL.**

    ![regra de firewall do servidor](./media/sql-database-security-tutorial/server-name.png)

    > [!NOTE]
    > Certifique-se de copiar o seu nome de servidor totalmente qualificado (como *yourserver.database.windows.net)* para ser utilizado mais tarde no tutorial.

1. Na página **'Visão Geral',** selecione **Definir firewall do servidor**. A página de **definições de Firewall** para o servidor de base de dados abre.

   1. **Selecione Adicionar IP** do cliente na barra de ferramentas para adicionar o seu endereço IP atual a uma nova regra de firewall. A regra pode abrir a porta 1433 para um único endereço IP ou uma gama de endereços IP. Selecione **Guardar**.

      ![configurar regra de firewall do servidor](./media/sql-database-security-tutorial/server-firewall-rule2.png)

   1. Selecione **OK** e feche a página de **definições de Firewall.**

Agora, já pode ligar a qualquer base de dados do servidor com o endereço IP ou o intervalo de endereços IP especificado.

### <a name="setup-database-firewall-rules"></a>Configurar regras de firewall de base de dados

As regras de firewall ao nível da base de dados aplicam-se apenas às bases de dados individuais. A base de dados manterá estas regras durante uma falha no servidor. As regras de firewall de nível de base de dados só podem ser configuradas utilizando declarações transact-SQL (T-SQL) e só depois de configurar uma regra de firewall ao nível do servidor.

Para configurar uma regra de firewall de nível de base de dados:

1. Ligue-se à base de dados, por exemplo, utilizando o [Estúdio de Gestão de Servidores SQL](./sql-database-connect-query-ssms.md).

1. No **Object Explorer,** clique na base de dados e selecione **New Query**.

1. Na janela de consulta, adicione esta declaração e modifique o endereço IP para o seu endereço IP público:

    ```sql
    EXECUTE sp_set_database_firewall_rule N'Example DB Rule','0.0.0.4','0.0.0.4';
    ```

1. Na barra de ferramentas, selecione **Executar** para criar a regra da firewall.

> [!NOTE]
> Também pode criar uma regra de firewall ao nível do servidor no SSMS utilizando o comando [sp_set_firewall_rule,](/sql/relational-databases/system-stored-procedures/sp-set-firewall-rule-azure-sql-database?view=azuresqldb-current) embora tenha de estar ligado à base de dados *principal.*

## <a name="create-an-azure-ad-admin"></a>Criar um administrador da AD Azure

Certifique-se de que está a utilizar o domínio gerido pelo Azure Ative Directory (AD) apropriado. Para selecionar o domínio AD, utilize o canto superior direito do portal Azure. Este processo confirma que a mesma subscrição é utilizada tanto para o Azure AD como para o SQL Server que acolhe a sua base de dados Azure SQL ou armazém de dados.

   ![escolher-anúncio](./media/sql-database-security-tutorial/8choose-ad.png)

Para definir o administrador da AD Azure:

1. No portal Azure, na página do **servidor SQL,** selecione **administração Ative Directory**. Em seguida, selecione **administrador**.

    ![selecionar o active directory](./media/sql-database-security-tutorial/admin-settings.png)  

    > [!IMPORTANT]
    > Tem de ser um "Administrador da Empresa" ou um "Administrador Global" para executar esta tarefa.

1. Na página **de administração Adicionar,** procure e selecione o utilizador ou grupo AD e escolha **Select**. Todos os membros e grupos do seu Diretório Ativo estão listados, e as entradas cinzentas não são suportadas como administradores da AD Azure. Consulte [as funcionalidades e limitações da AD Azure.](sql-database-aad-authentication.md#azure-ad-features-and-limitations)

    ![administração selecionada](./media/sql-database-security-tutorial/admin-select.png)

    > [!IMPORTANT]
    > O controlo de acesso baseado em funções (RBAC) aplica-se apenas ao portal e não é propagado ao Servidor SQL.

1. No topo da página de administração do **Diretório Ativo,** selecione **Guardar**.

    O processo de mudança de administrador pode demorar vários minutos. O novo administrador aparecerá na caixa de administração do **Diretório Ativo.**

> [!NOTE]
> Ao definir um administrador Azure AD, o novo nome de administrador (utilizador ou grupo) não pode existir como utilizador de autenticação Do Servidor SQL na base de dados *principal.* Se presente, a configuração falhará e reverterá as alterações, indicando que tal nome administrativo já existe. Uma vez que o utilizador de autenticação SQL Server não faz parte do Azure AD, qualquer esforço para ligar o utilizador utilizando a autenticação AD Azure falha.

Para obter informações sobre a configuração da AD Azure, consulte:

- [Integre as suas identidades no local com a Azure AD](../active-directory/hybrid/whatis-hybrid-identity.md)
- [Adicione o seu próprio nome de domínio ao Azure AD](../active-directory/active-directory-domains-add-azure-portal.md)
- [Microsoft Azure agora suporta federação com AD Windows Server](https://azure.microsoft.com/blog/20../../windows-azure-now-supports-federation-with-windows-server-active-directory/)
- [Administrar o seu diretório Azure AD](../active-directory/fundamentals/active-directory-administer.md)
- [Gerir o Azure AD usando powerShell](/powershell/azure/overview?view=azureadps-2.0)
- [Identidade híbrida exigia portos e protocolos](../active-directory/hybrid/reference-connect-ports.md)

## <a name="manage-database-access"></a>Gerir o acesso à base de dados

Gerir o acesso à base de dados adicionando os utilizadores à base de dados ou permitindo o acesso do utilizador com cordas de ligação seguras. As cordas de ligação são úteis para aplicações externas. Para saber mais, consulte [Gerir logins e contas de utilizador](sql-database-manage-logins.md) e [autenticação de Anúncios.](sql-database-aad-authentication.md)

Para adicionar utilizadores, escolha o tipo de autenticação da base de dados:

- **Aautenticação SQL**, use um nome de utilizador e senha para logins e só é válida no contexto de uma base de dados específica dentro do servidor

- **Autenticação Azure AD,** use identidades geridas pela Azure AD

### <a name="sql-authentication"></a>Autenticação do SQL

Para adicionar um utilizador com autenticação SQL:

1. Ligue-se à base de dados, por exemplo, utilizando o [Estúdio de Gestão de Servidores SQL](./sql-database-connect-query-ssms.md).

1. No **Object Explorer,** clique na base de dados e escolha **New Question**.

1. Na janela de consulta, insira o seguinte comando:

    ```sql
    CREATE USER ApplicationUser WITH PASSWORD = 'YourStrongPassword1';
    ```

1. Na barra de ferramentas, selecione **Executar** para criar o utilizador.

1. Por predefinição, o utilizador pode ligar à base de dados, mas não tem permissões para ler ou escrever dados. Para conceder estas permissões, execute os seguintes comandos numa nova janela de consulta:

    ```sql
    ALTER ROLE db_datareader ADD MEMBER ApplicationUser;
    ALTER ROLE db_datawriter ADD MEMBER ApplicationUser;
    ```

> [!NOTE]
> Crie contas não administradoras ao nível da base de dados, a menos que precisem de executar tarefas de administrador como criar novos utilizadores.

### <a name="azure-ad-authentication"></a>Autenticação do Azure AD

A autenticação do Diretório Ativo Azure requer que os utilizadores de bases de dados sejam criados conforme contido. Um utilizador de base de dados contido mapeia uma identidade no diretório da AD Azure associado à base de dados e não tem login na base de dados *principal.* A identidade Azure AD pode ser para um utilizador individual ou para um grupo. Para mais informações, consulte os utilizadores de [bases de dados contidos, faça](https://msdn.microsoft.com/library/ff929188.aspx) a sua base de dados portátil e reveja o tutorial da [AD Azure](./sql-database-aad-authentication-configure.md) sobre como autenticar usando o Azure AD.

> [!NOTE]
> Os utilizadores de bases de dados (excluindo administradores) não podem ser criados através do portal Azure. As funções Azure RBAC não se propagam a servidores, bases de dados ou armazéns de dados SQL. São utilizados apenas para gerir os recursos do Azure e não se aplicam a permissões de base de dados.
>
> Por exemplo, a função De Contribuinte do *Servidor SQL* não concede acesso à ligação a uma base de dados ou armazém de dados. Esta permissão deve ser concedida na base de dados utilizando declarações T-SQL.

> [!IMPORTANT]
> Personagens especiais como `:` do cólon ou `&` não são suportados em nomes de utilizadores nas declarações de `CREATE LOGIN` e `CREATE USER` Da T-SQL.

Para adicionar um utilizador com autenticação Azure AD:

1. Ligue-se ao seu servidor Azure SQL utilizando uma conta AD Azure com pelo menos a permissão *ALTER ANY USER.*

1. No **Object Explorer,** clique na base de dados e selecione **New Query**.

1. Na janela de consulta, introduza o seguinte comando e modifique `<Azure_AD_principal_name>` para o nome principal do utilizador DaD Azure ou o nome de exibição do grupo Azure AD:

   ```sql
   CREATE USER <Azure_AD_principal_name> FROM EXTERNAL PROVIDER;
   ```

> [!NOTE]
> Os utilizadores de AD Azure estão marcados na base de dados de metadados com tipo `E (EXTERNAL_USER)` e tipo `X (EXTERNAL_GROUPS)` para grupos. Para mais informações, consulte [sys.database_principals](/sql/relational-databases/system-catalog-views/sys-database-principals-transact-sql).

### <a name="secure-connection-strings"></a>Cordas de ligação seguras

Para garantir uma ligação segura e encriptada entre a aplicação do cliente e a base de dados SQL, uma cadeia de ligação deve ser configurada para:

- Solicite uma ligação encriptada
- Não confie no certificado do servidor

A ligação é estabelecida usando transport layer security (TLS) e reduz o risco de um ataque homem no meio. As cordas de ligação estão disponíveis por base de dados e estão pré-configuradas para apoiar os condutores de clientes como ADO.NET, JDBC, ODBC e PHP. Para obter informações sobre a TLS e a conectividade, veja [Considerações sobre a TLS](sql-database-connect-query.md#tls-considerations-for-sql-database-connectivity).

Para copiar uma cadeia de ligação segura:

1. No portal Azure, selecione bases de **dados SQL** do menu à esquerda e selecione a sua base de dados na página de bases de **dados SQL.**

1. Na página **'Visão Geral',** selecione Strings de **ligação**de base de dados .

1. Selecione um separador de controlador e copie a cadeia de ligação completa.

    ![Cadeia de ligação de ADO.NET](./media/sql-database-security-tutorial/connection.png)

## <a name="enable-security-features"></a>Ativar funcionalidades de segurança

A Base de Dados Azure SQL fornece funcionalidades de segurança que são acedidas através do portal Azure. Estas funcionalidades estão disponíveis tanto para a base de dados como para o servidor, com exceção da máscara de dados, que só está disponível na base de dados. Para saber mais, consulte [A segurança avançada de dados,](sql-database-advanced-data-security.md) [Auditoria,](sql-database-auditing.md)máscara de [dados dinâmicos](sql-database-dynamic-data-masking-get-started.md)e [encriptação transparente de dados.](transparent-data-encryption-azure-sql.md)

### <a name="advanced-data-security"></a>Segurança de dados avançada

A funcionalidade avançada de segurança de dados deteta potenciais ameaças à medida que ocorrem e fornece alertas de segurança sobre atividades anómalas. Os utilizadores podem explorar estes eventos suspeitos usando a funcionalidade de auditoria, e determinar se o evento foi para aceder, violar ou explorar dados na base de dados. Os utilizadores também recebem uma visão geral de segurança que inclui uma avaliação de vulnerabilidade e a ferramenta de descoberta e classificação de dados.

> [!NOTE]
> Uma ameaça exemplo é a injeção de SQL, um processo em que os atacantes injetam SQL malicioso nas inputs de aplicação. Uma aplicação pode então, sem saber, executar o SQL malicioso e permitir aos atacantes o acesso a violações ou modificações de dados na base de dados.

Para permitir a segurança avançada de dados:

1. No portal Azure, selecione bases de **dados SQL** do menu à esquerda e selecione a sua base de dados na página de bases de **dados SQL.**

1. Na página **'Visão Geral',** selecione o link de **nome do Servidor.** A página do servidor da base de dados será aberta.

1. Na página do **servidor SQL,** encontre a secção **de Segurança** e selecione Advanced **Data Security**.

   1. Selecione **ON** em Advanced **Data Security** para ativar a funcionalidade. Escolha uma conta de armazenamento para guardar resultados de avaliação de vulnerabilidade. Em seguida, selecione **Guardar**.

      ![Painel de navegação](./media/sql-database-security-tutorial/threat-settings.png)

      Também pode configurar e-mails para receber alertas de segurança, detalhes de armazenamento e tipos de deteção de ameaças.

1. Volte à página de bases de **dados SQL** da sua base de dados e selecione **Advanced Data Security** na secção De **segurança.** Aqui encontrará vários indicadores de segurança disponíveis para a base de dados.

    ![Estado de ameaça](./media/sql-database-security-tutorial/threat-status.png)

Se forem detetadas atividades anómalas, recebe um e-mail com informações sobre o evento. Isto inclui a natureza da atividade, base de dados, servidor, tempo de evento, possíveis causas, e ações recomendadas para investigar e mitigar a ameaça potencial. Se esse e-mail for recebido, selecione o link de registo de **auditoria Azure SQL** para lançar o portal Azure e apresentar registos de auditoria relevantes para a hora do evento.

   ![Mensagem de e-mail sobre deteção de ameaças](./media/sql-database-security-tutorial/threat-email.png)

### <a name="auditing"></a>Auditoria

A funcionalidade de auditoria rastreia eventos de base de dados e escreve eventos para um registo de auditoria no armazenamento Azure, registos do Monitor Azure ou num centro de eventos. A auditoria ajuda a manter o cumprimento regulamentar, a compreender a atividade da base de dados e a obter informações sobre discrepâncias e anomalias que possam indicar potenciais violações de segurança.

Para permitir a auditoria:

1. No portal Azure, selecione bases de **dados SQL** do menu à esquerda e selecione a sua base de dados na página de bases de **dados SQL.**

1. Na secção **Segurança,** **selecione Auditoria**.

1. De acordo com as definições **de Auditoria,** detete os seguintes valores:

   1. Definir **auditoria** para **ON**.

   1. Selecione o destino de registo de **auditoria** como qualquer um dos seguintes:

       - **Armazenamento**, uma conta de armazenamento Azure onde os registos de eventos são guardados e podem ser descarregados como *ficheiros .xel*

          > [!TIP]
          > Utilize a mesma conta de armazenamento para todas as bases de dados auditadas para obter o máximo de modelos de relatórios de auditoria.

       - **Log Analytics**, que armazena automaticamente eventos para consulta ou análise

           > [!NOTE]
           > Um espaço de **trabalho log Analytics** é necessário para suportar funcionalidades avançadas como análise, regras de alerta personalizados e exportações de Excel ou Power BI. Sem espaço de trabalho, apenas o editor de consulta está disponível.

       - **Event Hub**, que permite que eventos sejam encaminhados para uso em outras aplicações

   1. Selecione **Guardar**.

      ![Definições de auditoria](./media/sql-database-security-tutorial/audit-settings.png)

1. Agora pode selecionar registos de auditoria para visualizar dados de **eventos** de base de dados.

    ![Registos de auditoria](./media/sql-database-security-tutorial/audit-records.png)

> [!IMPORTANT]
> Consulte a auditoria da base de [dados SQL](sql-database-auditing.md) sobre como personalizar ainda mais os eventos de auditoria utilizando a PowerShell ou a REST API.

### <a name="dynamic-data-masking"></a>Máscara de dados dinâmica

A função de máscara de dados ocultará automaticamente dados sensíveis na sua base de dados.

Para permitir a máscara de dados:

1. No portal Azure, selecione bases de **dados SQL** do menu à esquerda e selecione a sua base de dados na página de bases de **dados SQL.**

1. Na secção **Segurança,** selecione **Máscara de Dados Dinâmicos**.

1. Em definições **dinâmicas de máscara de dados,** selecione **Adicionar máscara** para adicionar uma regra de mascaramento. O Azure irá povoar automaticamente os schemas, tabelas e colunas disponíveis para escolher.

    ![Definições de máscara](./media/sql-database-security-tutorial/mask-settings.png)

1. Selecione **Guardar**. A informação selecionada é agora mascarada de privacidade.

    ![Exemplo de máscara](./media/sql-database-security-tutorial/mask-query.png)

### <a name="transparent-data-encryption"></a>Encriptação de Dados Transparente

A funcionalidade de encriptação encripta automaticamente os seus dados em repouso e não necessita de alterações nas aplicações que acedam à base de dados encriptada. Para novas bases de dados, a encriptação está em predefinição. Também pode encriptar dados utilizando SSMS e a funcionalidade [Sempre encriptada.](sql-database-always-encrypted.md)

Para ativar ou verificar a encriptação:

1. No portal Azure, selecione bases de **dados SQL** do menu à esquerda e selecione a sua base de dados na página de bases de **dados SQL.**

1. Na secção **Segurança,** selecione **encriptação**transparente de dados .

1. Se necessário, detete a **encriptação de dados** para **ON**. Selecione **Guardar**.

    ![Encriptação de Dados Transparente](./media/sql-database-security-tutorial/encryption-settings.png)

> [!NOTE]
> Para ver o estado de encriptação, ligue-se à base de dados utilizando [SSMS](./sql-database-connect-query-ssms.md) e consulte a coluna `encryption_state` da visão [sys.dm_database_encryption_keys.](/sql/relational-databases/system-dynamic-management-views/sys-dm-database-encryption-keys-transact-sql) Um estado de `3` indica que a base de dados está encriptada.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, aprendeu a melhorar a segurança da sua base de dados com apenas alguns passos simples. Aprendeu a:

> [!div class="checklist"]
> - Criar regras de firewall ao nível do servidor e do nível da base de dados
> - Configure um administrador de Diretório Ativo Azure (AD)
> - Gerir o acesso ao utilizador com autenticação SQL, autenticação AD Azure e cordas de ligação seguras
> - Ativar funcionalidades de segurança, tais como segurança avançada de dados, auditoria, mascaramento de dados e encriptação

Avance para o próximo tutorial para aprender a implementar a geodistribuição.

> [!div class="nextstepaction"]
>[Implementar uma base de dados distribuída geograficamente](sql-database-implement-geo-distributed-database.md)
