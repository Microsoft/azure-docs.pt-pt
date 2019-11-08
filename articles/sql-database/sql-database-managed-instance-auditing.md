---
title: Auditoria de instância gerida
description: Saiba como começar a usar a auditoria de instância gerenciada do banco de dados SQL do Azure usando o T-SQL
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
f1_keywords:
- mi.azure.sqlaudit.general.f1
author: barmichal
ms.author: mibar
ms.reviewer: vanto
ms.date: 04/08/2019
ms.openlocfilehash: 41d632b145a7187dd0aeaab740cd8546a3955e7f
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73819001"
---
# <a name="get-started-with-azure-sql-database-managed-instance-auditing"></a>Introdução à auditoria de instância gerenciada do banco de dados SQL do Azure

A auditoria de [instância gerenciada](sql-database-managed-instance.md) rastreia eventos de banco de dados e os grava em um log de auditoria em sua conta de armazenamento do Azure. A auditoria também:

- Ajuda a manter a conformidade regulatória, a compreender a atividade do banco de dados e a obter informações sobre discrepâncias e anomalias que podem indicar preocupações comerciais ou violações suspeitas de segurança.
- Habilita e facilita a adesão aos padrões de conformidade, embora não garanta a conformidade. Para obter mais informações sobre os programas do Azure que dão suporte à conformidade com padrões, consulte a [central de confiabilidade do Azure](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) onde você pode encontrar a lista mais atual de certificações de conformidade do banco de dados SQL.

## <a name="set-up-auditing-for-your-server-to-azure-storage"></a>Configurar a auditoria de seu servidor para o armazenamento do Azure

A seção a seguir descreve a configuração de auditoria em sua instância gerenciada.

1. Aceda ao [Portal do Azure](https://portal.azure.com).
1. Crie um **contêiner** de armazenamento do Azure em que os logs de auditoria são armazenados.

   1. Navegue até o armazenamento do Azure onde você deseja armazenar seus logs de auditoria.

      > [!IMPORTANT]
      > Use uma conta de armazenamento na mesma região que a instância gerenciada para evitar leituras/gravações entre regiões.

   1. Na conta de armazenamento, acesse **visão geral** e clique em **BLOBs**.

      ![Widget de blob do Azure](./media/sql-managed-instance-auditing/1_blobs_widget.png)

   1. No menu superior, clique em **+ contêiner** para criar um novo contêiner.

      ![Ícone criar contêiner de BLOBs](./media/sql-managed-instance-auditing/2_create_container_button.png)

   1. Forneça um **nome**de contêiner, defina nível de acesso público como **particular**e clique em **OK**.

      ![Criar configuração de contêiner de BLOB](./media/sql-managed-instance-auditing/3_create_container_config.png)

1. Depois de criar o contêiner para os logs de auditoria, há duas maneiras de configurá-lo como o destino dos logs de auditoria: [usando T-SQL](#blobtsql) ou [usando a interface do usuário do SQL Server Management Studio (SSMS)](#blobssms):

   - <a id="blobtsql"></a>Configurar o armazenamento de blog para logs de auditoria usando o T-SQL:

     1. Na lista contêineres, clique no contêiner recém-criado e, em seguida, clique em **Propriedades do contêiner**.

        ![Botão de propriedades do contêiner de BLOB](./media/sql-managed-instance-auditing/4_container_properties_button.png)

     1. Copie a URL do contêiner clicando no ícone de cópia e salve a URL (por exemplo, no bloco de notas) para uso futuro. O formato da URL do contêiner deve ser `https://<StorageName>.blob.core.windows.net/<ContainerName>`

        ![URL de cópia do contêiner de BLOB](./media/sql-managed-instance-auditing/5_container_copy_name.png)

     1. Gere um **token SAS** do armazenamento do Azure para conceder direitos de acesso de auditoria de instância gerenciada à conta de armazenamento:

        - Navegue até a conta de armazenamento do Azure em que você criou o contêiner na etapa anterior.

        - Clique em **assinatura de acesso compartilhado** no menu configurações de armazenamento.

          ![Ícone de assinatura de acesso compartilhado no menu configurações de armazenamento](./media/sql-managed-instance-auditing/6_storage_settings_menu.png)

        - Configure a SAS da seguinte maneira:

          - **Serviços permitidos**: BLOB

          - **Data de início**: para evitar problemas relacionados a fuso horário, é recomendável usar a data de ontem

          - **Data de término**: escolha a data em que esse token SAS expira

            > [!NOTE]
            > Renove o token após a expiração para evitar falhas de auditoria.

          - Clique em **Gerar SAS**.
            
            ![Configuração de SAS](./media/sql-managed-instance-auditing/7_sas_configure.png)

        - Depois de clicar em gerar SAS, o token SAS aparece na parte inferior. Copie o token clicando no ícone de cópia e salve-o (por exemplo, no bloco de notas) para uso futuro.

          ![Copiar token SAS](./media/sql-managed-instance-auditing/8_sas_copy.png)

          > [!IMPORTANT]
          > Remova o caractere de ponto de interrogação ("?") do início do token.

     1. Conecte-se à sua instância gerenciada via SQL Server Management Studio (SSMS) ou qualquer outra ferramenta com suporte.

     1. Execute a seguinte instrução T-SQL para **criar uma nova credencial** usando a URL do contêiner e o token SAS que você criou nas etapas anteriores:

        ```SQL
        CREATE CREDENTIAL [<container_url>]
        WITH IDENTITY='SHARED ACCESS SIGNATURE',
        SECRET = '<SAS KEY>'
        GO
        ```

     1. Execute a seguinte instrução T-SQL para criar uma nova auditoria de servidor (escolha seu próprio nome de auditoria, use a URL do contêiner que você criou nas etapas anteriores). Se não for especificado, `RETENTION_DAYS` padrão será 0 (retenção ilimitada):

        ```SQL
        CREATE SERVER AUDIT [<your_audit_name>]
        TO URL ( PATH ='<container_url>' [, RETENTION_DAYS =  integer ])
        GO
        ```

        1. Continuar [criando uma especificação de auditoria de servidor ou especificação de auditoria de banco de dados](#createspec)

   - <a id="blobssms"></a>Configurar o armazenamento de BLOBs para logs de auditoria usando o SQL Server Management Studio (SSMS) 18 (versão prévia):

     1. Conecte-se à instância gerenciada usando a interface do usuário do SQL Server Management Studio (SSMS).

     1. Expanda a observação raiz do pesquisador de objetos.

     1. Expanda o nó **segurança** , clique com o botão direito do mouse no nó **auditorias** e clique em "nova auditoria":

        ![Expandir o nó de segurança e auditoria](./media/sql-managed-instance-auditing/10_mi_SSMS_new_audit.png)

     1. Verifique se "URL" está selecionado em **destino de auditoria** e clique em **procurar**:

        ![Procurar o armazenamento do Azure](./media/sql-managed-instance-auditing/11_mi_SSMS_audit_browse.png)

     1. Adicional Entre em sua conta do Azure:

        ![Iniciar sessão no Azure](./media/sql-managed-instance-auditing/12_mi_SSMS_sign_in_to_azure.png)

     1. Selecione uma assinatura, uma conta de armazenamento e um contêiner de BLOBs nos menus suspensos ou crie seu próprio contêiner clicando em **criar**. Depois de concluir, clique em **OK**:

        ![Selecione a assinatura do Azure, a conta de armazenamento e o contêiner de BLOB](./media/sql-managed-instance-auditing/13_mi_SSMS_select_subscription_account_container.png)

     1. Clique em **OK** na caixa de diálogo "criar auditoria".

1. <a id="createspec"></a>Depois de configurar o contêiner de blob como destino para os logs de auditoria, crie uma especificação de auditoria de servidor ou especificação de auditoria de banco de dados como você faria para SQL Server:

   - [Criar guia T-SQL de especificação de auditoria de servidor](https://docs.microsoft.com/sql/t-sql/statements/create-server-audit-specification-transact-sql)
   - [Criar guia T-SQL de especificação de auditoria de banco de dados](https://docs.microsoft.com/sql/t-sql/statements/create-database-audit-specification-transact-sql)

1. Habilite a auditoria de servidor que você criou na etapa 6:

    ```SQL
    ALTER SERVER AUDIT [<your_audit_name>]
    WITH (STATE=ON);
    GO
    ```

Para obter informações adicionais:

- [Diferenças de auditoria entre bancos de dados individuais, pools elásticos e instâncias gerenciadas no banco de dados SQL do Azure e nos bancos de dados no SQL Server](#auditing-differences-between-databases-in-azure-sql-database-and-databases-in-sql-server)
- [CRIAR AUDITORIA DE SERVIDOR](https://docs.microsoft.com/sql/t-sql/statements/create-server-audit-transact-sql)
- [ALTERAR AUDITORIA DO SERVIDOR](https://docs.microsoft.com/sql/t-sql/statements/alter-server-audit-transact-sql)

## <a name="set-up-auditing-for-your-server-to-event-hub-or-azure-monitor-logs"></a>Configurar a auditoria do servidor para o Hub de eventos ou logs de Azure Monitor

Os logs de auditoria de uma instância gerenciada podem ser enviados para até mesmo hubs ou Azure Monitor logs. Esta seção descreve como configurar isso:

1. Navegue no [portal do Azure](https://portal.azure.com/) para a instância gerenciada.

2. Clique em **configurações de diagnóstico**.

3. Clique em **Ativar diagnóstico**. Se o diagnóstico já estiver habilitado, a *configuração de diagnóstico + adicionar* será mostrada.

4. Selecione **SQLSecurityAuditEvents** na lista de logs.

5. Selecione um destino para os eventos de auditoria – Hub de eventos, logs de Azure Monitor ou ambos. Configure para cada destino os parâmetros necessários (por exemplo, Log Analytics espaço de trabalho).

6. Clique em **Guardar**.

    ![Definir configurações de diagnóstico](./media/sql-managed-instance-auditing/9_mi_configure_diagnostics.png)

7. Conecte-se à instância gerenciada usando o **SQL Server Management Studio (SSMS)** ou qualquer outro cliente com suporte.

8. Execute a seguinte instrução T-SQL para criar uma auditoria de servidor:

    ```SQL
    CREATE SERVER AUDIT [<your_audit_name>] TO EXTERNAL_MONITOR;
    GO
    ```

9. Crie uma especificação de auditoria de servidor ou especificação de auditoria de banco de dados como você faria para SQL Server:

   - [Criar guia T-SQL de especificação de auditoria de servidor](https://docs.microsoft.com/sql/t-sql/statements/create-server-audit-specification-transact-sql)
   - [Criar guia T-SQL de especificação de auditoria de banco de dados](https://docs.microsoft.com/sql/t-sql/statements/create-database-audit-specification-transact-sql)

10. Habilite a auditoria de servidor criada na etapa 8:
 
    ```SQL
    ALTER SERVER AUDIT [<your_audit_name>] WITH (STATE=ON);
    GO
    ```

## <a name="consume-audit-logs"></a>Consumir logs de auditoria

### <a name="consume-logs-stored-in-azure-storage"></a>Consumir logs armazenados no armazenamento do Azure

Há vários métodos que você pode usar para exibir logs de auditoria de BLOB.

- Use a função do sistema `sys.fn_get_audit_file` (T-SQL) para retornar os dados do log de auditoria no formato de tabela. Para obter mais informações sobre como usar essa função, consulte a [documentação sys. fn_get_audit_file](https://docs.microsoft.com/sql/relational-databases/system-functions/sys-fn-get-audit-file-transact-sql).

- Você pode explorar os logs de auditoria usando uma ferramenta como o [Gerenciador de armazenamento do Azure](https://azure.microsoft.com/features/storage-explorer/). No armazenamento do Azure, os logs de auditoria são salvos como uma coleção de arquivos de blob dentro de um contêiner que foi definido para armazenar os logs de auditoria. Para obter mais detalhes sobre a hierarquia da pasta de armazenamento, convenções de nomenclatura e formato de log, consulte a [referência de formato de log de auditoria de blob](https://go.microsoft.com/fwlink/?linkid=829599).

- Para obter uma lista completa dos métodos de consumo de log de auditoria, consulte a [auditoria introdução ao banco de dados SQL](sql-database-auditing.md).

### <a name="consume-logs-stored-in-event-hub"></a>Consumir logs armazenados no Hub de eventos

Para consumir dados de logs de auditoria do hub de eventos, você precisará configurar um fluxo para consumir eventos e gravá-los em um destino. Para obter mais informações, consulte a documentação dos hubs de eventos do Azure.

### <a name="consume-and-analyze-logs-stored-in-azure-monitor-logs"></a>Consumir e analisar logs armazenados em logs de Azure Monitor

Se os logs de auditoria forem gravados nos logs de Azure Monitor, eles estarão disponíveis no espaço de trabalho Log Analytics, onde você poderá executar pesquisas avançadas nos dados de auditoria. Como ponto de partida, navegue até o espaço de trabalho Log Analytics e, em seção *geral* , clique em *logs* e insira uma consulta simples, como: `search "SQLSecurityAuditEvents"` para exibir os logs de auditoria.  

Os logs de Azure Monitor fornecem informações operacionais em tempo real usando pesquisa integrada e painéis personalizados para analisar rapidamente milhões de registros em todas as suas cargas de trabalho e servidores. Para obter informações úteis adicionais sobre os comandos e o idioma de pesquisa de logs do Azure Monitor, consulte [referência de pesquisa de Azure monitor logs](https://docs.microsoft.com/azure/azure-monitor/log-query/log-query-overview).

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="auditing-differences-between-databases-in-azure-sql-database-and-databases-in-sql-server"></a>As diferenças de auditoria entre bancos de dados no banco de dados SQL do Azure e nos dados no SQL Server

As principais diferenças entre a auditoria em bancos de dados no banco de dados SQL do Azure e nos dados no SQL Server são:

- Com a opção de implantação de instância gerenciada no banco de dados SQL do Azure, a auditoria funciona no nível do servidor e armazena `.xel` arquivos de log no armazenamento de BLOBs do Azure.
- Em SQL Server máquinas locais/virtuais, a auditoria funciona no nível do servidor, mas armazena eventos em logs de eventos do sistema de arquivos/Windows.

A auditoria de XEvent na instância gerenciada dá suporte a destinos do armazenamento de BLOBs do Azure. **Não há suporte para**logs de arquivo e do Windows.

As principais diferenças na sintaxe de `CREATE AUDIT` para a auditoria para o armazenamento de BLOBs do Azure são:

- Uma nova sintaxe `TO URL` é fornecida e permite que você especifique a URL do contêiner de armazenamento de BLOBs do Azure onde os arquivos de `.xel` são colocados.
- Uma nova sintaxe `TO EXTERNAL MONITOR` é fornecida para habilitar o Hub par e Azure Monitor destinos de logs.
- **Não há suporte para** a sintaxe `TO FILE` porque o banco de dados SQL não pode acessar compartilhamentos de arquivos do Windows.
- **Não há suporte para**a opção de desligamento.
- **não há suporte para**`queue_delay` de 0.

## <a name="next-steps"></a>Passos seguintes

- Para obter uma lista completa dos métodos de consumo de log de auditoria, consulte a [auditoria introdução ao banco de dados SQL](sql-database-auditing.md).
- Para obter mais informações sobre os programas do Azure que dão suporte à conformidade com padrões, consulte a [central de confiabilidade do Azure](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) onde você pode encontrar a lista mais atual de certificações de conformidade do banco de dados SQL.

<!--Image references-->









