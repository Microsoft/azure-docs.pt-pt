---
title: Auditoria de instância gerida
description: Saiba como começar com a Azure SQL Database gerida por auditoria de instância usando T-SQL
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
f1_keywords:
- mi.azure.sqlaudit.general.f1
author: DavidTrigano
ms.author: datrigan
ms.reviewer: vanto
ms.date: 04/08/2019
ms.openlocfilehash: 9b96969027431f289e366b150fbfc6a62ee6a908
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/24/2020
ms.locfileid: "76719913"
---
# <a name="get-started-with-azure-sql-database-managed-instance-auditing"></a>Começar com a Azure SQL Database gerida por auditoria de instância

[A](sql-database-managed-instance.md) auditoria de exemplo gerido rastreia eventos de base de dados e escreve-os num registo de auditoria na sua conta de armazenamento Azure. Auditoria também:

- Ajuda-o a manter o cumprimento regulamentar, a compreender a atividade da base de dados e a obter informações sobre discrepâncias e anomalias que possam indicar preocupações comerciais ou suspeitas de violações de segurança.
- Permite e facilita a adesão às normas de conformidade, embora não garanta o cumprimento. Para obter mais informações sobre os programas Azure que suportam a conformidade das normas, consulte o [Azure Trust Center](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) onde pode encontrar a lista mais atual das certificações de conformidade da Base de Dados SQL.

## <a name="set-up-auditing-for-your-server-to-azure-storage"></a>Configurar auditoria para o seu servidor para armazenamento Azure

A secção seguinte descreve a configuração da auditoria na sua instância gerida.

1. Aceda ao [Portal do Azure](https://portal.azure.com).
1. Crie um **recipiente** de armazenamento Azure onde os registos de auditoria sejam armazenados.

   1. Navegue até ao Armazém Azure onde pretende armazenar os seus registos de auditoria.

      > [!IMPORTANT]
      > Utilize uma conta de armazenamento na mesma região que a instância gerida para evitar leituras/escritos transversais.

   1. Na conta de armazenamento, vá ao **Overview e** clique em **Blobs**.

      ![Widget Azure Blob](./media/sql-managed-instance-auditing/1_blobs_widget.png)

   1. No menu superior, clique em **+ Recipiente** para criar um novo recipiente.

      ![Criar ícone de recipiente blob](./media/sql-managed-instance-auditing/2_create_container_button.png)

   1. Forneça um **nome**de recipiente, detete o nível de acesso público ao **Privado**e, em seguida, clique em **OK**.

      ![Criar configuração de recipiente blob](./media/sql-managed-instance-auditing/3_create_container_config.png)

1. Depois de criar o recipiente para os registos de Auditoria, existem duas formas de configurá-lo como o alvo para os registos de auditoria: [utilizar o T-SQL](#blobtsql) ou [utilizar o SQL Server Management Studio (SSMS) UI:](#blobssms)

   - <a id="blobtsql"></a>Configure o armazenamento de blogs para registos de auditoria utilizando t-SQL:

     1. Na lista de contentores, clique no recipiente recém-criado e, em seguida, clique nas **propriedades do Contentor**.

        ![Botão de propriedades do recipiente blob](./media/sql-managed-instance-auditing/4_container_properties_button.png)

     1. Copie o URL do recipiente clicando no ícone da cópia e guarde o URL (por exemplo, no Bloco de Notas) para utilização futura. O formato URL do recipiente deve ser `https://<StorageName>.blob.core.windows.net/<ContainerName>`

        ![URL de cópia de recipiente blob](./media/sql-managed-instance-auditing/5_container_copy_name.png)

     1. Gere ncisem um Azure Storage **SAS Token** para conceder direitos de acesso geridos à conta de armazenamento:

        - Navegue para a conta de Armazenamento Azure onde criou o recipiente no passo anterior.

        - Clique na assinatura de **acesso partilhado** no menu Definições de Armazenamento.

          ![Ícone de assinatura de acesso partilhado no menu de definições de armazenamento](./media/sql-managed-instance-auditing/6_storage_settings_menu.png)

        - Configure o SAS da seguinte forma:

          - **Serviços permitidos**: Blob

          - **Data de início**: para evitar problemas relacionados com fusos horários, recomenda-se a utilização da data de ontem

          - **Data limite**: escolha a data em que este Token SAS expira

            > [!NOTE]
            > Renovar o símbolo no termo do termo para evitar falhas de auditoria.

          - Clique em **Gerar SAS**.
            
            ![Configuração SAS](./media/sql-managed-instance-auditing/7_sas_configure.png)

        - Depois de clicar no Generate SAS, o Token SAS aparece na parte inferior. Copie o símbolo clicando no ícone da cópia e guarde-o (por exemplo, no Bloco de Notas) para utilização futura.

          ![Cópia sas ficha](./media/sql-managed-instance-auditing/8_sas_copy.png)

          > [!IMPORTANT]
          > Remova o carácter do ponto de interrogação ("?") desde o início do símbolo.

     1. Ligue-se à sua instância gerida através do SQL Server Management Studio (SSMS) ou de qualquer outra ferramenta suportada.

     1. Execute a seguinte declaração T-SQL para **criar uma nova Credencial** utilizando o URL do recipiente e o Token SAS que criou nos passos anteriores:

        ```SQL
        CREATE CREDENTIAL [<container_url>]
        WITH IDENTITY='SHARED ACCESS SIGNATURE',
        SECRET = '<SAS KEY>'
        GO
        ```

     1. Execute a seguinte declaração T-SQL para criar uma nova Auditoria do Servidor (escolha o seu próprio nome de auditoria, utilize o URL do Recipiente que criou nos passos anteriores). Se não especificado, `RETENTION_DAYS` predefinição é 0 (retenção ilimitada):

        ```SQL
        CREATE SERVER AUDIT [<your_audit_name>]
        TO URL ( PATH ='<container_url>' [, RETENTION_DAYS =  integer ])
        GO
        ```

        1. Continue criando uma especificação de [auditoria do servidor ou especificação](#createspec) de auditoria de base de dados

   - <a id="blobssms"></a>Configure o armazenamento de blob para registos de auditoria utilizando o Estúdio de Gestão de Servidores SQL (SSMS) 18 (Pré-visualização):

     1. Ligue-se à instância gerida utilizando o SQL Server Management Studio (SSMS) UI.

     1. Expanda a nota de raiz do Explorador de Objetos.

     1. Expanda o nó de **Segurança,** clique à direita no nó de **Auditorias** e clique em "New Audit":

        ![Expandir o nó de segurança e auditoria](./media/sql-managed-instance-auditing/10_mi_SSMS_new_audit.png)

     1. Certifique-se de que "URL" é selecionado no **destino de Auditoria** e clique em **Navegar:**

        ![Ver armazenamento Azure](./media/sql-managed-instance-auditing/11_mi_SSMS_audit_browse.png)

     1. (Opcional) Inscreva-se na sua conta Azure:

        ![Iniciar sessão no Azure](./media/sql-managed-instance-auditing/12_mi_SSMS_sign_in_to_azure.png)

     1. Selecione uma subscrição, uma conta de armazenamento e um recipiente Blob a partir das quedas, ou crie o seu próprio recipiente clicando no **Create**. Uma vez terminado clique **OK**:

        ![Selecione assinatura Azure, conta de armazenamento e recipiente de blob](./media/sql-managed-instance-auditing/13_mi_SSMS_select_subscription_account_container.png)

     1. Clique **OK** no diálogo "Criar Auditoria".

1. <a id="createspec"></a>Depois de configurar o recipiente Blob como alvo para os registos de auditoria, crie uma especificação de auditoria do servidor ou especificação de auditoria da base de dados, como faria para o Servidor SQL:

   - [Criar especificação de auditoria do Servidor Guia T-SQL](https://docs.microsoft.com/sql/t-sql/statements/create-server-audit-specification-transact-sql)
   - [Criar especificação de auditoria da Base de Dados Guia T-SQL](https://docs.microsoft.com/sql/t-sql/statements/create-database-audit-specification-transact-sql)

1. Ative a auditoria do servidor que criou no passo 6:

    ```SQL
    ALTER SERVER AUDIT [<your_audit_name>]
    WITH (STATE=ON);
    GO
    ```

Para obter informações adicionais:

- [Diferenças de auditoria entre bases de dados individuais, piscinas elásticas e instâncias geridas na Base de Dados Azure SQL e bases de dados no Servidor SQL](#auditing-differences-between-databases-in-azure-sql-database-and-databases-in-sql-server)
- [CRIAR AUDITORIA DO SERVIDOR](https://docs.microsoft.com/sql/t-sql/statements/create-server-audit-transact-sql)
- [ALTERAR AUDITORIA DO SERVIDOR](https://docs.microsoft.com/sql/t-sql/statements/alter-server-audit-transact-sql)

## <a name="set-up-auditing-for-your-server-to-event-hub-or-azure-monitor-logs"></a>Configurar auditorias para o seu servidor para registos do Event Hub ou do Monitor Azure

Os registos de auditoria de uma instância gerida podem ser enviados para registos Even Hubs ou Azure Monitor. Esta secção descreve como configurar isto:

1. Navegue no [Portal Azure](https://portal.azure.com/) até à instância gerida.

2. Clique nas **definições de diagnóstico**.

3. Clique em **Turn on diagnostics**. Se os diagnósticos já estiverem ativados, a definição de *diagnóstico +Adicionar* mostrará.

4. Selecione **SQLSecurityAuditEvents** na lista de registos.

5. Selecione um destino para os eventos de auditoria - Event Hub, registos do Monitor Azure, ou ambos. Configure para cada alvo os parâmetros necessários (por exemplo, log Analytics espaço de trabalho).

6. Clique em **Guardar**.

    ![Definir configurações de diagnóstico](./media/sql-managed-instance-auditing/9_mi_configure_diagnostics.png)

7. Ligue-se à instância gerida utilizando o **SQL Server Management Studio (SSMS)** ou qualquer outro cliente suportado.

8. Execute a seguinte declaração T-SQL para criar uma auditoria do servidor:

    ```SQL
    CREATE SERVER AUDIT [<your_audit_name>] TO EXTERNAL_MONITOR;
    GO
    ```

9. Crie uma especificação de auditoria do servidor ou especificação de auditoria da base de dados como faria para o Servidor SQL:

   - [Criar especificação de auditoria do Servidor Guia T-SQL](https://docs.microsoft.com/sql/t-sql/statements/create-server-audit-specification-transact-sql)
   - [Criar especificação de auditoria da Base de Dados Guia T-SQL](https://docs.microsoft.com/sql/t-sql/statements/create-database-audit-specification-transact-sql)

10. Ativar a auditoria do servidor criada no passo 8:
 
    ```SQL
    ALTER SERVER AUDIT [<your_audit_name>] WITH (STATE=ON);
    GO
    ```

## <a name="consume-audit-logs"></a>Consumir registos de auditoria

### <a name="consume-logs-stored-in-azure-storage"></a>Consumir troncos armazenados no Armazém Azure

Existem vários métodos que pode usar para ver registos de auditoria blob.

- Utilize a função do sistema `sys.fn_get_audit_file` (T-SQL) para devolver os dados de registo de auditoria em formato tabular. Para obter mais informações sobre a utilização desta função, consulte a [documentação sys.fn_get_audit_file](https://docs.microsoft.com/sql/relational-databases/system-functions/sys-fn-get-audit-file-transact-sql).

- Pode explorar registos de auditoria utilizando uma ferramenta como o [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/). No armazenamento do Azure, os registos de auditoria são guardados como uma coleção de ficheiros blob dentro de um recipiente que foi definido para armazenar os registos de auditoria. Para mais detalhes sobre a hierarquia da pasta de armazenamento, nomeação de convenções e formato de registo, consulte a Referência do Formato de Registo de [Auditoria Blob](https://go.microsoft.com/fwlink/?linkid=829599).

- Para obter uma lista completa dos métodos de consumo de registo de auditoria, consulte o Get started com a auditoria da base de [dados SQL](sql-database-auditing.md).

### <a name="consume-logs-stored-in-event-hub"></a>Consumir registos armazenados no Event Hub

Para consumir dados de logs de auditoria do hub de eventos, você precisará configurar um fluxo para consumir eventos e gravá-los em um destino. Para mais informações, consulte a Documentação do Azure Event Hubs.

### <a name="consume-and-analyze-logs-stored-in-azure-monitor-logs"></a>Consumir e Analisar registos armazenados em registos do Monitor Azure

Se os registos de auditoria forem escritos aos registos do Monitor Do Azure, estão disponíveis no espaço de trabalho do Log Analytics, onde pode executar pesquisas avançadas nos dados da auditoria. Como ponto de partida, navegue para o espaço de trabalho do Log Analytics e em secção *geral* clique em *Registos* e introduza uma consulta simples, como: `search "SQLSecurityAuditEvents"` para ver os registos de auditoria.  

Os logs de Azure Monitor fornecem informações operacionais em tempo real usando pesquisa integrada e painéis personalizados para analisar rapidamente milhões de registros em todas as suas cargas de trabalho e servidores. Para obter informações adicionais úteis sobre o idioma de pesquisa de registos do Monitor Azure e comandos, consulte a referência de pesquisa de [registos do Monitor Azure](https://docs.microsoft.com/azure/azure-monitor/log-query/log-query-overview).

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="auditing-differences-between-databases-in-azure-sql-database-and-databases-in-sql-server"></a>Diferenças de auditoria entre bases de dados em Base de Dados Azure SQL e bases de dados no Servidor SQL

As principais diferenças entre a auditoria em bancos de dados no banco de dados SQL do Azure e nos dados no SQL Server são:

- Com a opção de implementação de instância gerida na Base de Dados Azure SQL, a auditoria funciona ao nível do servidor e armazena `.xel` ficheiros de registo no armazenamento do Azure Blob.
- No SQL Server no local / máquinas virtuais, a auditoria funciona ao nível do servidor, mas armazena eventos em registos de eventos de sistema sinuoso/windows.

A auditoria de XEvent na instância gerenciada dá suporte a destinos do armazenamento de BLOBs do Azure. Os registos de ficheiros e janelas não são **suportados**.

As principais diferenças na sintaxe `CREATE AUDIT` para auditoria ao armazenamento da Blob Azure são:

- É fornecida uma nova `TO URL` de sintaxe e permite especificar URL do recipiente de armazenamento de blob Azure onde os ficheiros `.xel` são colocados.
- É fornecida uma nova `TO EXTERNAL MONITOR` de sintaxe para permitir os alvos de registos even Hub e Azure Monitor.
- O `TO FILE` de sintaxe não é **suportado** porque a Base de Dados SQL não pode aceder a partilhas de ficheiros windows.
- A opção de encerramento não é **suportada.**
- `queue_delay` de 0 não é **suportado.**

## <a name="next-steps"></a>Passos Seguintes

- Para obter uma lista completa dos métodos de consumo de registo de auditoria, consulte o Get started com a auditoria da base de [dados SQL](sql-database-auditing.md).
- Para obter mais informações sobre os programas Azure que suportam a conformidade das normas, consulte o [Azure Trust Center](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) onde pode encontrar a lista mais atual das certificações de conformidade da Base de Dados SQL.

<!--Image references-->









