---
title: Proteger uma base de dados individual ou agrupada na SQL Database do Azure | Documentos da Microsoft
description: Saiba mais sobre técnicas e funcionalidades para proteger uma base de dados individual ou agrupada na base de dados do Azure SQL.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.topic: tutorial
author: VanMSFT
ms.author: vanto
ms.reviewer: carlrab
manager: craigg
ms.date: 02/08/2019
ms.openlocfilehash: d6f14a7cdcb77c1ca47d0f79f587e0bf3606b5d5
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57893276"
---
# <a name="tutorial-secure-a-single-or-pooled-database"></a>Tutorial: Proteger uma base de dados individual ou agrupada

Base de dados SQL do Azure protege os dados numa base de dados individual ou agrupada, permitindo-lhe:

- Limitar o acesso com regras de firewall
- Utilize os mecanismos de autenticação que necessitem de identidade
- Utilizar a autorização com permissões e associações baseadas em função
- Ativar funcionalidades de segurança

> [!NOTE]
> Uma base de dados SQL do Azure numa instância gerida está protegida com regras de segurança de rede e de pontos de extremidade privados, conforme descrito em [instância gerida de base de dados SQL do Azure](sql-database-managed-instance-index.yml) e [arquitetura de conectividade](sql-database-managed-instance-connectivity-architecture.md).

Pode melhorar a segurança da base de dados com apenas alguns passos simples. Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> - Criar regras de firewall ao nível do servidor e ao nível da base de dados
> - Configurar um administrador do Azure Active Directory (AD)
> - Gerir o acesso de utilizador com a autenticação do SQL, autenticação do Azure AD e as cadeias de ligação segura
> - Ativar funcionalidades de segurança, tais como a segurança de dados avançada, auditoria, máscara de dados e a encriptação

Para obter mais informações, consulte a [descrição geral da segurança do Azure SQL Database](/azure/sql-database/sql-database-security-index) e [capacidades](sql-database-security-overview.md) artigos.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir o tutorial, certifique-se de que tem os seguintes pré-requisitos:

- [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms)
- Um servidor SQL do Azure e a base de dados
  - Criá-los com [portal do Azure](sql-database-single-database-get-started.md), [CLI](sql-database-cli-samples.md), ou [PowerShell](sql-database-powershell-samples.md)

Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Para obter todos os passos do tutorial, inicie sessão no [portal do Azure](https://portal.azure.com/)

## <a name="create-firewall-rules"></a>Criar regras da firewall

Bases de dados SQL estão protegidos por firewalls no Azure. Por predefinição, todas as ligações ao servidor e base de dados são rejeitadas, exceto para ligações a partir de outros serviços do Azure. Para obter mais informações, consulte [regras de firewall ao nível do servidor e ao nível da base de dados de base de dados do Azure SQL](sql-database-firewall-configure.md).

Definir **permitir o acesso aos serviços do Azure** ao **OFF** para a configuração mais segura. Em seguida, crie uma [reservado de IP (implementação clássica)](../virtual-network/virtual-networks-reserved-public-ip.md) para o recurso que precisa para se ligar, como um serviço de VM do Azure ou na cloud e permitir que apenas esse acesso de endereço IP através da firewall. Se estiver a utilizar o [Gestor de recursos](/azure/virtual-network/virtual-network-ip-addresses-overview-arm) modelo de implementação, um endereço IP público dedicado é necessário para cada recurso.

> [!NOTE]
> A Base de Dados SQL comunica através da porta 1433. Se estiver a tentar ligar a partir de uma rede empresarial, o tráfego de saída através da porta 1433 poderá não ser permitido pela firewall da rede. Se assim for, não é possível ligar ao servidor de base de dados do Azure SQL, a menos que o administrador abra a porta 1433.

### <a name="set-up-sql-database-server-firewall-rules"></a>Configurar regras de firewall do servidor de base de dados SQL

Regras de firewall do IP ao nível do servidor aplicam-se a todas as bases de dados no mesmo servidor de base de dados SQL.

Para configurar uma regra de firewall ao nível do servidor:

1. No portal do Azure, selecione **bases de dados SQL** no menu esquerdo e selecione a sua base de dados sobre o **bases de dados SQL** página.

    ![regra de firewall do servidor](./media/sql-database-security-tutorial/server-name.png)

    > [!NOTE]
    > Certifique-se de que copiar o nome do servidor completamente qualificado (como *yourserver.database.windows.net*) para utilizar mais tarde no tutorial.

1. Sobre o **descrição geral** página, selecione **definir firewall do servidor**. O **definições da Firewall** é aberta a página para o servidor de base de dados.

   1. Selecione **Adicionar IP de cliente** na barra de ferramentas para adicionar o seu endereço IP atual a uma nova regra de firewall. A regra pode abrir a porta 1433 para um único endereço IP ou um intervalo de endereços IP. Selecione **Guardar**.

      ![configurar regra de firewall do servidor](./media/sql-database-security-tutorial/server-firewall-rule2.png)

   1. Selecione **OK** e feche o **definições da Firewall** página.

Agora, já pode ligar a qualquer base de dados do servidor com o endereço IP ou o intervalo de endereços IP especificado.

> [!IMPORTANT]
> Por predefinição, o acesso através da firewall de base de dados SQL está ativado para todos os serviços do Azure, em **permitir o acesso aos serviços do Azure**. Escolher **OFF** para desativar o acesso de todos os serviços do Azure.

### <a name="setup-database-firewall-rules"></a>Configurar regras de firewall da base de dados

Regras de firewall ao nível da base de dados só se aplicam às bases de dados individuais. A base de dados irá reter essas regras durante uma ativação pós-falha do servidor. Regras de firewall ao nível da base de dados só podem ser configuradas com instruções Transact-SQL (T-SQL) e apenas depois que tiver configurado uma regra de firewall ao nível do servidor.

Para configurar uma regra de firewall ao nível da base de dados:

1. Ligar à base de dados, por exemplo, utilizando [SQL Server Management Studio](./sql-database-connect-query-ssms.md).

1. Na **Object Explorer**, a base de dados com o botão direito e selecione **nova consulta**.

1. Na janela da consulta, adicione essa instrução e modificar o endereço IP para o seu endereço IP público:

    ```sql
    EXECUTE sp_set_database_firewall_rule N'Example DB Rule','0.0.0.4','0.0.0.4';
    ```

1. Na barra de ferramentas, selecione **Execute** para criar a regra de firewall.

> [!NOTE]
> Também pode criar uma regra de firewall ao nível do servidor no SSMS, utilizando o [sp_set_firewall_rule](/sql/relational-databases/system-stored-procedures/sp-set-firewall-rule-azure-sql-database?view=azuresqldb-current) comando, que tem de estar ligado para o *mestre* base de dados.

## <a name="create-an-azure-ad-admin"></a>Criar um administrador do Azure AD

Certifique-se de que está a utilizar o domínio gerido do Azure Active Directory (AD) adequado. Para selecionar o domínio do AD, utilize o canto superior direito do portal do Azure. Este processo confirma a mesma subscrição é utilizada para o Azure AD e o SQL Server que aloja o armazém de dados ou base de dados SQL do Azure.

   ![ad escolha](./media/sql-database-security-tutorial/8choose-ad.png)

Para definir o administrador do Azure AD:

1. No portal do Azure, sobre o **do SQL server** página, selecione **administrador do Active Directory**. Em seguida selecione **definir administrador**.

    ![selecionar o active directory](./media/sql-database-security-tutorial/admin-settings.png)  

    > [!IMPORTANT]
    > Tem de ser um "Administrador de empresa" ou "Administrador Global" para efetuar esta tarefa.

1. Sobre o **Adicionar administrador** página, procure e selecione o utilizador do AD ou o grupo e escolha **selecione**. São listados todos os membros e grupos do Active Directory e a cinzento de entradas não são suportadas como administradores do Azure AD. Ver [funcionalidades do Azure AD e limitações](sql-database-aad-authentication.md#azure-ad-features-and-limitations).

    ![Selecione o administrador](./media/sql-database-security-tutorial/admin-select.png)

    > [!IMPORTANT]
    > Controlo de acesso baseado em funções (RBAC) apenas aplica-se para o portal e não é propagado para o SQL Server.

1. Na parte superior a **administrador do Active Directory** página, selecione **guardar**.

    O processo de alteração um administrador pode demorar vários minutos. O administrador do novo aparecerá no **administrador do Active Directory** caixa.

> [!NOTE]
> Ao definir um administrador do Azure AD, o novo nome de administrador (utilizador ou grupo) não pode existir como um utilizador de autenticação do SQL Server no *mestre* base de dados. Se estiver presente, a configuração irá falhar e reverter as alterações, que indica que já existe um nome de administrador. Uma vez que o utilizador de autenticação do SQL Server não faz parte do Azure AD, falha qualquer esforço para conectar o usuário usando autenticação do Azure AD.

Para obter informações sobre a configuração do Azure AD, consulte:

- [Integrar as identidades no local com o Azure AD](../active-directory/hybrid/whatis-hybrid-identity.md)
- [Adicionar o seu próprio nome de domínio para o Azure AD](../active-directory/active-directory-domains-add-azure-portal.md)
- [Microsoft Azure suporta agora a Federação com o Windows Server AD](https://azure.microsoft.com/blog/20../../windows-azure-now-supports-federation-with-windows-server-active-directory/)
- [Administrar o diretório do Azure AD](../active-directory/fundamentals/active-directory-administer.md)
- [Gerir o Azure AD com o PowerShell](/powershell/azure/overview?view=azureadps-2.0)
- [Identidade híbrida portas e protocolos necessários](../active-directory/hybrid/reference-connect-ports.md)

## <a name="manage-database-access"></a>Gerir o acesso de base de dados

Gerir o acesso de base de dados ao adicionar utilizadores à base de dados ou, permitindo o acesso de utilizador com cadeias de ligação segura. Cadeias de ligação são úteis para aplicativos externos. Para obter mais informações, consulte [controlo de acesso do SQL do Azure](sql-database-control-access.md) e [autenticação AD](sql-database-aad-authentication.md).

Para adicionar utilizadores, escolha o tipo de autenticação da base de dados:

- **Autenticação do SQL**, utilize um nome de utilizador e palavra-passe para inícios de sessão e só são válidos no contexto de uma base de dados específico dentro do servidor

- **Autenticação do Azure AD**, utilizar identidades geridas pelo Azure AD

### <a name="sql-authentication"></a>Autenticação do SQL

Para adicionar um utilizador com a autenticação de SQL:

1. Ligar à base de dados, por exemplo, utilizando [SQL Server Management Studio](./sql-database-connect-query-ssms.md).

1. Na **Object Explorer**, clique com o botão direito na base de dados e escolha **nova consulta**.

1. Na janela da consulta, introduza o seguinte comando:

    ```sql
    CREATE USER ApplicationUser WITH PASSWORD = 'YourStrongPassword1';
    ```

1. Na barra de ferramentas, selecione **Execute** para criar o utilizador.

1. Por predefinição, o utilizador pode ligar à base de dados, mas não tem permissões para ler ou escrever dados. Para conceder estas permissões, execute os comandos seguintes numa nova janela de consulta:

    ```sql
    ALTER ROLE db_datareader ADD MEMBER ApplicationUser;
    ALTER ROLE db_datawriter ADD MEMBER ApplicationUser;
    ```

> [!NOTE]
> Crie contas de não-administrador ao nível da base de dados, a menos que necessitam para executar tarefas de administrador, como a criação de novos utilizadores.

### <a name="azure-ad-authentication"></a>Autenticação do Azure AD

Autenticação do Azure Active Directory requer que os utilizadores de base de dados são criados como contidos. Um utilizador de base de dados contida mapeia para uma identidade no diretório do Azure AD associado com a base de dados e tem sem início de sessão do *mestre* base de dados. A identidade do Azure AD pode ser de um utilizador individual ou um grupo. Para obter mais informações, consulte [utilizadores de base de dados contida, tornar a sua base de dados portátil](https://msdn.microsoft.com/library/ff929188.aspx) e reveja a [tutorial do Azure AD](./sql-database-aad-authentication-configure.md) sobre como autenticar com o Azure AD.

> [!NOTE]
> Os utilizadores de base de dados (excluindo os administradores) não não possível criar com o portal do Azure. Funções RBAC do Azure não será propagada para armazéns de dados, servidores ou bases de dados SQL. Eles só são utilizados para gerir recursos do Azure e não se aplicam às permissões de base de dados.
>
> Por exemplo, o *contribuinte do SQL Server* função não concede acesso para ligar a um armazém de dados ou base de dados. Essa permissão deve ser concedida na base de dados com instruções T-SQL.

> [!IMPORTANT]
> Carateres especiais, como dois pontos `:` ou e comercial `&` não são suportados nomes de utilizador no T-SQL `CREATE LOGIN` e `CREATE USER` instruções.

Para adicionar um utilizador com a autenticação do Azure AD:

1. Ligar ao seu servidor SQL do Azure através de uma conta do Azure AD com, pelo menos, o *ALTER ANY utilizador* permissão.

1. Na **Object Explorer**, a base de dados com o botão direito e selecione **nova consulta**.

1. Na janela de consulta, introduza o seguinte comando e modificar `<Azure_AD_principal_name>` para o nome do principal do utilizador do Azure AD ou o nome a apresentar do grupo do Azure AD:

   ```sql
   CREATE USER <Azure_AD_principal_name> FROM EXTERNAL PROVIDER;
   ```

> [!NOTE]
> Utilizadores do Azure AD são marcados nos metadados da base de dados com o tipo `E (EXTERNAL_USER)` e escreva `X (EXTERNAL_GROUPS)` para grupos. Para obter mais informações, consulte [database_principals](/sql/relational-databases/system-catalog-views/sys-database-principals-transact-sql).

### <a name="secure-connection-strings"></a>Cadeias de ligação segura

Para garantir uma ligação encriptada segura entre o aplicativo de cliente e a base de dados SQL, uma cadeia de ligação tem de ser configurada para:

- Pedir uma ligação encriptada
- Não confiar no certificado de servidor

A ligação é estabelecida com Transport Layer Security (TLS) e reduz o risco de um ataque man-in-the-middle. Cadeias de ligação estão disponíveis por base de dados e pré-configuradas para suportar os controladores do cliente, como ADO.NET, JDBC, ODBC e PHP. Para obter informações sobre a TLS e a conectividade, veja [Considerações sobre a TLS](sql-database-connect-query.md#tls-considerations-for-sql-database-connectivity).

Para copiar uma cadeia de ligação segura:

1. No portal do Azure, selecione **bases de dados SQL** no menu esquerdo e selecione a sua base de dados sobre o **bases de dados SQL** página.

1. Sobre o **descrição geral** página, selecione **Mostrar cadeias de ligação de base de dados**.

1. Selecione um separador de controlador e copie a cadeia de ligação completa.

    ![Cadeia de ligação de ADO.NET](./media/sql-database-security-tutorial/connection.png)

## <a name="enable-security-features"></a>Ativar funcionalidades de segurança

Base de dados SQL do Azure fornece recursos de segurança que são acedidos no portal do Azure. Estas funcionalidades estão disponíveis para a base de dados e o servidor, exceto para a máscara de dados, que só está disponível na base de dados. Para obter mais informações, consulte [segurança de dados avançada](sql-database-advanced-data-security.md), [auditoria](sql-database-auditing.md), [máscara de dados dinâmicos](sql-database-dynamic-data-masking-get-started.md), e [encriptação de dados transparente](transparent-data-encryption-azure-sql.md).

### <a name="advanced-data-security"></a>Segurança de dados avançada

A funcionalidade de segurança de dados avançada Deteta potenciais ameaças à medida que ocorrem e fornece alertas de segurança relativamente a atividades anómalas. Os utilizadores podem explorar estes eventos suspeitos com a funcionalidade de auditoria e determinar se o evento foi aceder, violar ou explorar dados na base de dados. Os utilizadores também são fornecidos uma visão geral de segurança que inclui uma avaliação de vulnerabilidades e a ferramenta de deteção e classificação de dados.

> [!NOTE]
> Uma ameaça de exemplo é a injeção de SQL, um processo onde os atacantes injetar SQL maliciosa em entradas de aplicação. Um aplicativo pode, em seguida, executar o SQL mal-intencionado inconscientemente e permitem que os invasores acesso violar ou modificar dados na base de dados.

Para ativar a segurança de dados avançada:

1. No portal do Azure, selecione **bases de dados SQL** no menu esquerdo e selecione a sua base de dados sobre o **bases de dados SQL** página.

1. Sobre o **descrição geral** página, selecione a **nome do servidor** ligação. Abre a página do servidor de base de dados.

1. Sobre o **do SQL server** página, encontre o **segurança** secção e selecione **segurança avançada de dados**.

   1. Selecione **ON** sob **segurança de dados avançada** para ativar a funcionalidade. Escolha uma conta de armazenamento para guardar os resultados da avaliação de vulnerabilidade. Em seguida, selecione **Guardar**.

      ![Painel de navegação](./media/sql-database-security-tutorial/threat-settings.png)

      Também pode configurar mensagens de e-mail para receber alertas de segurança, os detalhes de armazenamento e os tipos de deteção de ameaças.

1. Retorno para o **bases de dados SQL** página da sua base de dados e selecione **segurança de dados avançada** sob o **segurança** secção. Aqui encontrará vários indicadores de segurança disponíveis para a base de dados.

    ![Estado da ameaça](./media/sql-database-security-tutorial/threat-status.png)

Se forem detetadas atividades anómalas, receberá um e-mail com informações sobre o evento. Isto inclui a natureza da atividade, base de dados, servidor, hora do evento, as causas possíveis e as ações recomendadas para investigar e mitigar a potencial ameaça. Se não for recebida uma mensagem de e-mail, selecione o **registo de auditoria de SQL do Azure** ligação para iniciar o portal do Azure e mostrar registos de auditorias relevantes para a hora do evento.

   ![Mensagem de e-mail sobre deteção de ameaças](./media/sql-database-security-tutorial/threat-email.png)

### <a name="auditing"></a>Auditoria

A funcionalidade de auditoria controla os eventos de base de dados e escreve eventos num registo de auditoria o armazenamento do Azure, os registos do Azure Monitor, ou para um hub de eventos. Auditoria ajuda a manter a conformidade regulamentar, compreender a atividade de base de dados e obter informações sobre discrepâncias e anomalias que possam indiciar potenciais violações de segurança.

Para ativar a auditoria:

1. No portal do Azure, selecione **bases de dados SQL** no menu esquerdo e selecione a sua base de dados sobre o **bases de dados SQL** página.

1. Na **Security** secção, selecione **auditoria**.

1. Sob **auditoria** definições, defina os seguintes valores:

   1. Definir **auditoria** ao **ON**.

   1. Selecione **destino de registo de auditoria** como qualquer um dos seguintes:

       - **Armazenamento**, uma conta de armazenamento do Azure, onde os registos de eventos são salvos e pode ser baixados como *. xel* ficheiros

          > [!TIP]
          > Utilize a mesma conta de armazenamento para todas as bases de dados auditadas para tirar o máximo de modelos de relatório de auditoria.

       - **Log Analytics**, que armazena automaticamente os eventos para consulta ou análise adicional

           > [!NOTE]
           > R **área de trabalho do Log Analytics** é necessária para suportar funcionalidades avançadas, como análise, regras de alerta personalizadas e exportações do Excel ou Power BI. Sem uma área de trabalho, o editor de consultas está disponível.

       - **Hub de eventos**, que permite que os eventos para ser encaminhado para utilização em outros aplicativos

   1. Selecione **Guardar**.

      ![Definições de auditoria](./media/sql-database-security-tutorial/audit-settings.png)

1. Agora, pode selecionar **ver registos de auditoria** para ver os dados de eventos de base de dados.

    ![Registos de auditoria](./media/sql-database-security-tutorial/audit-records.png)

> [!IMPORTANT]
> Ver [a auditoria de base de dados SQL](sql-database-auditing.md) sobre como personalizar ainda mais os eventos de auditoria com o PowerShell ou a REST API.

### <a name="dynamic-data-masking"></a>Máscara de dados dinâmica

A funcionalidade de máscara de dados ocultará automaticamente dados confidenciais na base de dados.

Para ativar a máscara de dados:

1. No portal do Azure, selecione **bases de dados SQL** no menu esquerdo e selecione a sua base de dados sobre o **bases de dados SQL** página.

1. Na **Security** secção, selecione **Dynamic Data Masking**.

1. Sob **máscara de dados dinâmicos** definições, selecione **adicionar máscara** para adicionar uma regra de máscara. Azure será preenchida automaticamente esquemas de banco de dados disponíveis, tabelas e colunas à sua escolha.

    ![Definições de máscara](./media/sql-database-security-tutorial/mask-settings.png)

1. Selecione **Guardar**. As informações selecionadas agora está oculto para a privacidade.

    ![Exemplo de máscara](./media/sql-database-security-tutorial/mask-query.png)

### <a name="transparent-data-encryption"></a>Encriptação de Dados Transparente

A funcionalidade de encriptação encripta os dados em repouso automaticamente e requer sem alterações às aplicações aceder à base de dados encriptado. Bases de dados nova, a encriptação está ativada por predefinição. Também pode criptografar dados com o SSMS e o [Always encrypted](sql-database-always-encrypted.md) funcionalidade.

Para ativar ou certifique-se de encriptação:

1. No portal do Azure, selecione **bases de dados SQL** no menu esquerdo e selecione a sua base de dados sobre o **bases de dados SQL** página.

1. Na **Security** secção, selecione **encriptação de dados transparente**.

1. Se necessário, defina **encriptação de dados** ao **ON**. Selecione **Guardar**.

    ![Encriptação de Dados Transparente](./media/sql-database-security-tutorial/encryption-settings.png)

> [!NOTE]
> Para ver o estado de encriptação, ligar à base de dados, com [SSMS](./sql-database-connect-query-ssms.md) e consultar a `encryption_state` coluna da [DM database_encryption_keys](/sql/relational-databases/system-dynamic-management-views/sys-dm-database-encryption-keys-transact-sql) vista. Um Estado de `3` indica a base de dados é encriptado.

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, aprendeu a melhorar a segurança da base de dados com apenas alguns passos simples. Aprendeu a:

> [!div class="checklist"]
> - Criar regras de firewall ao nível do servidor e ao nível da base de dados
> - Configurar um administrador do Azure Active Directory (AD)
> - Gerir o acesso de utilizador com a autenticação do SQL, autenticação do Azure AD e as cadeias de ligação segura
> - Ativar funcionalidades de segurança, tais como a segurança de dados avançada, auditoria, máscara de dados e a encriptação

Avance para o próximo tutorial para saber como implementar a distribuição geográfica.

> [!div class="nextstepaction"]
>[Implementar uma base de dados distribuída geograficamente](sql-database-implement-geo-distributed-database.md)
