---
title: Proteger uma base de dados
description: Este tutorial ensina-lhe as técnicas e funcionalidades para garantir uma Base de Dados Azure SQL, seja uma única base de dados, ou aginhada.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.topic: tutorial
author: VanMSFT
ms.author: vanto
ms.reviewer: ''
ms.date: 09/21/2020
ms.custom: seoapril2019 sqldbrb=1
ms.openlocfilehash: bec60875561a9d821642d850c27e47d4f906aba3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90885407"
---
# <a name="tutorial-secure-a-database-in-azure-sql-database"></a>Tutorial: Proteger uma base de dados na Base de Dados Azure SQL
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
>
> - Criar regras de firewall ao nível do servidor e de nível de base de dados
> - Configure um administrador do Azure Ative Directory (Azure AD)
> - Gerir o acesso do utilizador com autenticação SQL, autenticação AZure AD e cadeias de ligação seguras
> - Ativar funcionalidades de segurança, tais como Azure Defender para SQL, auditoria, máscara de dados e encriptação

A Azure SQL Database protege os dados permitindo-lhe:

- Limite o acesso usando regras de firewall
- Utilize mecanismos de autenticação que exijam identidade
- Utilizar a autorização com membros e permissões baseados em funções
- Ativar funcionalidades de segurança

> [!NOTE]
> A azure SQL Managed Instance é protegido usando regras de segurança de rede e pontos finais privados, conforme descrito em [Azure SQL Managed Instance](../managed-instance/sql-managed-instance-paas-overview.md) e [arquitetura de conectividade](../managed-instance/connectivity-architecture-overview.md).

Para saber mais, consulte a visão geral de segurança da [Base de Dados Azure SQL](/azure/sql-database/sql-database-security-index) e [os artigos de capacidades.](security-overview.md)

> [!TIP]
> O módulo seguinte Do Microsoft Learn ajuda-o a aprender gratuitamente sobre como proteger a [sua base de dados na Base de Dados Azure SQL](https://docs.microsoft.com/learn/modules/secure-your-azure-sql-database/).

## <a name="prerequisites"></a>Pré-requisitos

Para completar o tutorial, certifique-se de que tem os seguintes pré-requisitos:

- [O SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms)
- Um [servidor](logical-servers.md) e uma única base de dados
  - Crie-os com o [portal Azure,](single-database-create-quickstart.md) [CLI](az-cli-script-samples-content-guide.md)ou [PowerShell](powershell-script-content-guide.md)

Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Para todos os passos no tutorial, inscreva-se no [portal Azure](https://portal.azure.com/)

## <a name="create-firewall-rules"></a>Criar regras da firewall

As bases de dados na Base de Dados SQL estão protegidas por firewalls em Azure. Por predefinição, todas as ligações ao servidor e à base de dados são rejeitadas. Para saber mais, consulte [as regras de firewall ao nível do servidor e do nível da base de dados](firewall-configure.md).

**Desconfiem do acesso aos serviços Azure** **para** a configuração mais segura. Em seguida, crie um [IP reservado (implementação clássica)](/previous-versions/azure/virtual-network/virtual-networks-reserved-public-ip) para o recurso que precisa de se conectar, como um VM Azure ou serviço na nuvem, e apenas permita esse acesso de endereço IP através da firewall. Se estiver a utilizar o modelo de implementação do Gestor de [Recursos,](/azure/virtual-network/virtual-network-ip-addresses-overview-arm) é necessário um endereço IP público dedicado para cada recurso.

> [!NOTE]
> A Base de Dados SQL comunica através da porta 1433. Se estiver a tentar ligar-se a partir de uma rede corporativa, o tráfego de saída sobre a porta 1433 pode não ser permitido pela firewall da sua rede. Em caso afirmativo, não pode ligar-se ao servidor a menos que o seu administrador abra a porta 1433.

### <a name="set-up-server-level-firewall-rules"></a>Configurar regras de firewall ao nível do servidor

As regras de firewall IP de nível de servidor aplicam-se a todas as bases de dados dentro do mesmo servidor.

Para configurar uma regra de firewall ao nível do servidor:

1. No portal Azure, selecione **bases de dados SQL** a partir do menu à esquerda e selecione a sua base de dados na página **de bases de dados SQL.**

    ![regra de firewall do servidor](./media/secure-database-tutorial/server-name.png)

    > [!NOTE]
    > Certifique-se de que copia o nome do servidor totalmente qualificado (como *yourserver.database.windows.net)* para utilização posterior no tutorial.

1. Na página **'Vista Geral',** selecione **'Definir firewall' do servidor**. A página **de definições de Firewall** para o servidor abre.

   1. **Selecione Adicionar** o IP do cliente na barra de ferramentas para adicionar o seu endereço IP atual a uma nova regra de firewall. A regra pode abrir a porta 1433 para um único endereço IP ou uma gama de endereços IP. Selecione **Guardar**.

      ![configurar regra de firewall do servidor](./media/secure-database-tutorial/server-firewall-rule2.png)

   1. Selecione **OK** e feche a página **de definições de Firewall.**

Agora, já pode ligar a qualquer base de dados do servidor com o endereço IP ou o intervalo de endereços IP especificado.

### <a name="setup-database-firewall-rules"></a>Configurar regras de firewall de base de dados

As regras de firewall ao nível da base de dados aplicam-se apenas a bases de dados individuais. A base de dados conservará estas regras durante uma falha no servidor. As regras de firewall ao nível da base de dados só podem ser configuradas usando declarações Transact-SQL (T-SQL) e só depois de configurar uma regra de firewall ao nível do servidor.

Para configurar uma regra de firewall ao nível da base de dados:

1. Ligue-se à base de dados, por exemplo, utilizando [o SQL Server Management Studio](connect-query-ssms.md).

1. No **Object Explorer,** clique à direita na base de dados e selecione **Nova Consulta**.

1. Na janela de consulta, adicione esta declaração e modifique o endereço IP no seu endereço IP público:

    ```sql
    EXECUTE sp_set_database_firewall_rule N'Example DB Rule','0.0.0.4','0.0.0.4';
    ```

1. Na barra de ferramentas, **selecione Executar** para criar a regra de firewall.

> [!NOTE]
> Também pode criar uma regra de firewall ao nível do servidor em SSMS utilizando o comando [sp_set_firewall_rule,](/sql/relational-databases/system-stored-procedures/sp-set-firewall-rule-azure-sql-database?view=azuresqldb-current) embora tenha de estar ligado à base de dados *principal.*

## <a name="create-an-azure-ad-admin"></a>Criar um administrador AD AZure

Certifique-se de que está a utilizar o domínio gerido pelo Azure Ative Directory (AD). Para selecionar o domínio AD, utilize o canto superior direito do portal Azure. Este processo confirma que a mesma subscrição é utilizada tanto para o Azure AD como para o servidor lógico SQL que hospeda a sua base de dados ou armazém de dados.

   ![escolher anúncio](./media/secure-database-tutorial/8choose-ad.png)

Para definir o administrador AD Azure:

1. No portal Azure, na página do **servidor SQL,** selecione **Ative Directory admin**. Próxima seleção **Definir administrador**.

    ![selecionar o active directory](./media/secure-database-tutorial/admin-settings.png)  

    > [!IMPORTANT]
    > Tem de ser um "Administrador da Empresa" ou "Administrador Global" para executar esta tarefa.

1. Na página **de administração Adicionar,** procure e selecione o utilizador ou grupo AD e escolha **Select**. Todos os membros e grupos do seu Diretório Ativo estão listados, e as entradas acinzentadas não são suportadas como administradores da AD Azure. Consulte [as funcionalidades e limitações da AD Azure](authentication-aad-overview.md#azure-ad-features-and-limitations).

    ![selecionar administrador](./media/secure-database-tutorial/admin-select.png)

    > [!IMPORTANT]
    > O controlo de acesso baseado em funções (RBAC) aplica-se apenas ao portal e não é propagado ao SQL Server.

1. No topo da página de administração do **Ative Directory,** selecione **Save**.

    O processo de alteração de um administrador pode demorar vários minutos. O novo administrador aparecerá na caixa de administração do **Ative Directory.**

> [!NOTE]
> Ao configurar um administrador AD AZure, o novo nome de administração (utilizador ou grupo) não pode existir como um login do SQL Server ou utilizador na base de dados *principal.* Se estiver presente, a configuração falhará e reverterá as alterações, indicando que tal nome de administração já existe. Uma vez que o login ou utilizador do SQL Server não faz parte do Azure AD, qualquer esforço para ligar o utilizador utilizando a autenticação AD AZure falha.

Para obter informações sobre a configuração da Azure AD, consulte:

- [Integre as suas identidades no local com a Azure AD](../../active-directory/hybrid/whatis-hybrid-identity.md)
- [Adicione o seu próprio nome de domínio ao Azure AD](../../active-directory/fundamentals/add-custom-domain.md)
- [Microsoft Azure agora suporta federação com AD do Windows Server](https://azure.microsoft.com/blog/20../../windows-azure-now-supports-federation-with-windows-server-active-directory/)
- [Administrar o diretório do Azure AD](../../active-directory/fundamentals/active-directory-whatis.md)
- [Gerir a AD Azure usando o PowerShell](/powershell/azure/?view=azureadps-2.0)
- [Identidade híbrida exigida portas e protocolos](../../active-directory/hybrid/reference-connect-ports.md)

## <a name="manage-database-access"></a>Gerir o acesso à base de dados

Gerencie o acesso à base de dados adicionando os utilizadores à base de dados ou permitindo o acesso do utilizador com cadeias de ligação seguras. As cadeias de ligação são úteis para aplicações externas. Para saber mais, consulte [Gerir logins e contas de utilizador](logins-create-manage.md) e [autenticação AD.](authentication-aad-overview.md)

Para adicionar utilizadores, escolha o tipo de autenticação da base de dados:

- **Autenticação SQL**, use um nome de utilizador e senha para logins e só é válido no contexto de uma base de dados específica dentro do servidor

- **Autenticação AZURE AD**, use identidades geridas pela Azure AD

### <a name="sql-authentication"></a>Autenticação do SQL

Para adicionar um utilizador com autenticação SQL:

1. Ligue-se à base de dados, por exemplo, utilizando [o SQL Server Management Studio](connect-query-ssms.md).

1. No **Object Explorer,** clique à direita na base de dados e escolha **Nova Consulta**.

1. Na janela de consulta, insira o seguinte comando:

    ```sql
    CREATE USER ApplicationUser WITH PASSWORD = 'YourStrongPassword1';
    ```

1. Na barra de ferramentas, **selecione Executar** para criar o utilizador.

1. Por predefinição, o utilizador pode ligar à base de dados, mas não tem permissões para ler ou escrever dados. Para conceder estas permissões, execute os seguintes comandos numa nova janela de consulta:

    ```sql
    ALTER ROLE db_datareader ADD MEMBER ApplicationUser;
    ALTER ROLE db_datawriter ADD MEMBER ApplicationUser;
    ```

> [!NOTE]
> Crie contas não-administradoras ao nível da base de dados, a menos que precisem de executar tarefas de administrador como criar novos utilizadores.

### <a name="azure-ad-authentication"></a>Autenticação do Azure AD

A autenticação do Azure Ative Directory requer que os utilizadores da base de dados sejam criados conforme contido. Uma base de dados contida mapeia um utilizador de uma identidade no diretório AD Azure associado à base de dados e não tem qualquer login na base de dados *principal.* A identidade AZure AD pode ser para um utilizador individual ou para um grupo. Para mais informações, consulte [os utilizadores de bases de dados contidos, torne a sua base de dados portátil](/sql/relational-databases/security/contained-database-users-making-your-database-portable) e reveja o tutorial [AZure AD](authentication-aad-configure.md) sobre como autenticar usando a Azure AD.

> [!NOTE]
> Os utilizadores de bases de dados (excluindo administradores) não podem ser criados utilizando o portal Azure. As funções Azure não se propagam a servidores, bases de dados ou armazéns de dados SQL. São utilizados apenas para gerir os recursos da Azure e não se aplicam a permissões de base de dados.
>
> Por exemplo, a *função sql Server Contributor* não concede acesso à ligação a uma base de dados ou armazém de dados. Esta permissão deve ser concedida na base de dados utilizando declarações T-SQL.

> [!IMPORTANT]
> Caracteres especiais como cólon `:` ou ampersand `&` não são suportados em nomes de utilizador no T-SQL `CREATE LOGIN` e `CREATE USER` declarações.

Para adicionar um utilizador com autenticação AD AZure:

1. Conecte-se ao seu servidor em Azure utilizando uma conta AZure AD com pelo menos a permissão *ALTER ANY USER.*

1. No **Object Explorer,** clique à direita na base de dados e selecione **Nova Consulta**.

1. Na janela de consulta, insira o seguinte comando e modifique `<Azure_AD_principal_name>` para o nome principal do utilizador AZure AD ou o nome de exibição do grupo AZure AD:

   ```sql
   CREATE USER <Azure_AD_principal_name> FROM EXTERNAL PROVIDER;
   ```

> [!NOTE]
> Os utilizadores de AD Azure estão marcados nos metadados da base de dados com tipo `E (EXTERNAL_USER)` e tipo `X (EXTERNAL_GROUPS)` para grupos. Para mais informações, consulte [sys.database_principals.](/sql/relational-databases/system-catalog-views/sys-database-principals-transact-sql)

### <a name="secure-connection-strings"></a>Cadeias de ligação seguras

Para garantir uma ligação segura e encriptada entre a aplicação do cliente e a Base de Dados SQL, deve ser configurada uma cadeia de ligação para:

- Solicite uma ligação encriptada
- Não confie no certificado do servidor

A ligação é estabelecida usando a Segurança da Camada de Transporte (TLS) e reduz o risco de um ataque homem-no-meio. As cadeias de ligação estão disponíveis por base de dados e estão pré-configuradas para suportar controladores de clientes como ADO.NET, JDBC, ODBC e PHP. Para obter informações sobre a TLS e a conectividade, veja [Considerações sobre a TLS](connect-query-content-reference-guide.md#tls-considerations-for-database-connectivity).

Para copiar uma cadeia de ligação segura:

1. No portal Azure, selecione **bases de dados SQL** a partir do menu à esquerda e selecione a sua base de dados na página **de bases de dados SQL.**

1. Na página **'Vista Geral',** selecione **'Mostrar' as cadeias de ligação à base de dados**.

1. Selecione um separador do controlador e copie a cadeia de ligação completa.

    ![Cadeia de ligação de ADO.NET](./media/secure-database-tutorial/connection.png)

## <a name="enable-security-features"></a>Ativar funcionalidades de segurança

A Azure SQL Database fornece funcionalidades de segurança que são acedidas através do portal Azure. Estas funcionalidades estão disponíveis tanto para a base de dados como para o servidor, com exceção da máscara de dados, que só está disponível na base de dados. Para saber mais, consulte [o Azure Defender for SQL,](azure-defender-for-sql.md) [Auditing,](../../azure-sql/database/auditing-overview.md) [Dynamic data masking](dynamic-data-masking-overview.md)e Transparent data [encryption](transparent-data-encryption-tde-overview.md).

### <a name="azure-defender-for-sql"></a>Azure Defender para SQL

A funcionalidade Azure Defender for SQL deteta potenciais ameaças à medida que ocorrem e fornece alertas de segurança sobre atividades anómalas. Os utilizadores podem explorar estes eventos suspeitos usando a funcionalidade de auditoria e determinar se o evento foi para aceder, violar ou explorar dados na base de dados. Os utilizadores também são fornecidos uma visão geral de segurança que inclui uma avaliação de vulnerabilidade e a ferramenta de descoberta e classificação de dados.

> [!NOTE]
> Uma ameaça exemplo é a injeção de SQL, um processo em que os atacantes injetam SQL malicioso nas entradas de aplicações. Uma aplicação pode então executar inconscientemente o SQL malicioso e permitir aos atacantes o acesso a violação ou modificação de dados na base de dados.

Para ativar o Azure Defender para o SQL:

1. No portal Azure, selecione **bases de dados SQL** a partir do menu à esquerda e selecione a sua base de dados na página **de bases de dados SQL.**

1. Na página **'Vista Geral',** selecione o link **de nomes do Servidor.** A página do servidor será aberta.

1. Na página do **servidor SQL,** encontre a secção **de Segurança** e selecione o Centro **de Segurança**.

   1. Selecione **ON** under **Azure Defender for SQL** para ativar a funcionalidade. Escolha uma conta de armazenamento para guardar os resultados da avaliação da vulnerabilidade. Em seguida, selecione **Guardar**.

      ![Painel de navegação](./media/secure-database-tutorial/threat-settings.png)

      Também pode configurar e-mails para receber alertas de segurança, detalhes de armazenamento e tipos de deteção de ameaças.

1. Volte à página de bases de **dados SQL** da sua base de dados e selecione **o Centro** de Segurança sob a secção **De Segurança.** Aqui encontrará vários indicadores de segurança disponíveis para a base de dados.

    ![Estado de ameaça](./media/secure-database-tutorial/threat-status.png)

Se forem detetadas atividades anómalas, recebe um e-mail com informações sobre o evento. Isto inclui a natureza da atividade, base de dados, servidor, tempo de evento, possíveis causas, e ações recomendadas para investigar e mitigar a ameaça potencial. Se tal e-mail for recebido, selecione o link **Azure SQL Auditing Log** para lançar o portal Azure e mostrar registos de auditoria relevantes para a hora do evento.

   ![Mensagem de e-mail sobre deteção de ameaças](./media/secure-database-tutorial/threat-email.png)

### <a name="auditing"></a>Auditoria

O recurso de auditoria rastreia eventos de base de dados e escreve eventos para um registo de auditoria no armazenamento do Azure, nos registos do Azure Monitor ou num centro de eventos. A auditoria ajuda a manter a conformidade regulatória, a compreender as atividades da base de dados e a obter informações relativas a discrepâncias e anomalias que possam indicar potenciais violações de segurança.

Para permitir a auditoria:

1. No portal Azure, selecione **bases de dados SQL** a partir do menu à esquerda e selecione a sua base de dados na página **de bases de dados SQL.**

1. Na secção **Segurança,** selecione **Auditing**.

1. Nas definições **de Auditoria,** defina os seguintes valores:

   1. Defina a opção **Auditoria** como **Ativar**.

   1. Selecione **destino de registo de auditoria** como qualquer um dos seguintes:

       - **Armazenamento**, uma conta de armazenamento Azure onde os registos de eventos são guardados e podem ser descarregados como ficheiros *.xel*

          > [!TIP]
          > Utilize a mesma conta de armazenamento para todas as bases de dados auditadas para tirar o máximo partido dos modelos de relatórios de auditoria.

       - **Log Analytics**, que armazena automaticamente eventos para consulta ou análise posterior

           > [!NOTE]
           > Um **espaço de trabalho Log Analytics** é necessário para suportar funcionalidades avançadas, tais como analíticas, regras de alerta personalizados e exportações de Excel ou Power BI. Sem espaço de trabalho, apenas o editor de consulta está disponível.

       - **Event Hub**, que permite que eventos sejam encaminhados para uso em outras aplicações

   1. Selecione **Guardar**.

      ![Definições de auditoria](./media/secure-database-tutorial/audit-settings.png)

1. Agora pode selecionar **ver os registos de auditoria** para visualizar os dados de eventos de base de dados.

    ![Registos de auditoria](./media/secure-database-tutorial/audit-records.png)

> [!IMPORTANT]
> Consulte [a auditoria da SQL Database](../../azure-sql/database/auditing-overview.md) sobre como personalizar ainda mais eventos de auditoria utilizando a PowerShell ou a REST API.

### <a name="dynamic-data-masking"></a>Máscara de dados dinâmica

A função de mascaramento de dados ocultará automaticamente dados sensíveis na sua base de dados.

Para permitir a mascaramento de dados:

1. No portal Azure, selecione **bases de dados SQL** a partir do menu à esquerda e selecione a sua base de dados na página **de bases de dados SQL.**

1. Na secção **Segurança,** selecione **Máscara de Dados Dinâmicos**.

1. Em **definições dinâmicas de mascaramento de dados,** selecione **Adicionar máscara** para adicionar uma regra de mascaramento. O Azure preencherá automaticamente os esquemas de base de dados disponíveis, tabelas e colunas para escolher.

    ![Configurações de máscaras](./media/secure-database-tutorial/mask-settings.png)

1. Selecione **Guardar**. As informações selecionadas estão agora mascaradas para privacidade.

    ![Exemplo de máscara](./media/secure-database-tutorial/mask-query.png)

### <a name="transparent-data-encryption"></a>Encriptação de dados transparente

A funcionalidade de encriptação encripta automaticamente os seus dados em repouso e não requer alterações nas aplicações que acedem à base de dados encriptada. Para novas bases de dados, a encriptação está por defeito. Também pode encriptar dados usando SSMS e a funcionalidade [Sempre encriptada.](always-encrypted-certificate-store-configure.md)

Para ativar ou verificar encriptação:

1. No portal Azure, selecione **bases de dados SQL** a partir do menu à esquerda e selecione a sua base de dados na página **de bases de dados SQL.**

1. Na secção **Segurança,** selecione **encriptação de dados transparentes.**

1. Se necessário, desaperte a **encriptação de dados** para **ON**. Selecione **Guardar**.

    ![Encriptação de Dados Transparente](./media/secure-database-tutorial/encryption-settings.png)

> [!NOTE]
> Para visualizar o estado de encriptação, ligue-se à base de dados utilizando [SSMS](connect-query-ssms.md) e consulte a `encryption_state` coluna da vista [sys.dm_database_encryption_keys.](/sql/relational-databases/system-dynamic-management-views/sys-dm-database-encryption-keys-transact-sql) Um estado indica `3` que a base de dados está encriptada.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, aprendeu a melhorar a segurança da sua base de dados com apenas alguns passos simples. Aprendeu a:

> [!div class="checklist"]
>
> - Criar regras de firewall ao nível do servidor e de nível de base de dados
> - Configure um administrador do Azure Ative Directory (AD)
> - Gerir o acesso do utilizador com autenticação SQL, autenticação AZure AD e cadeias de ligação seguras
> - Ativar funcionalidades de segurança, tais como Azure Defender para SQL, auditoria, máscara de dados e encriptação

Avance para o próximo tutorial para aprender a implementar a geo-distribuição.

> [!div class="nextstepaction"]
>[Implementar uma base de dados distribuída geograficamente](geo-distributed-application-configure-tutorial.md)
