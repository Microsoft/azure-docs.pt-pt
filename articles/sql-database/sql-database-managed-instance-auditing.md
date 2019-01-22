---
title: Base de dados SQL do Azure geridos instância auditoria | Documentos da Microsoft
description: Saiba como começar com o Azure SQL da base de dados geridos instância auditoria com T-SQL
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
f1_keywords:
- mi.azure.sqlaudit.general.f1
author: vainolo
ms.author: arib
ms.reviewer: vanto
manager: craigg
ms.date: 01/15/2019
ms.openlocfilehash: 04c4bba2647b9b17b1282c9a1608fd2e9325f661
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/22/2019
ms.locfileid: "54427921"
---
# <a name="get-started-with-azure-sql-database-managed-instance-auditing"></a>Introdução ao Azure SQL da base de dados geridos instância de auditoria

[O Azure SQL Database Managed Instance](sql-database-managed-instance.md) faixas de auditoria de base de dados eventos e escreve-os para uma auditoria registo na sua conta de armazenamento do Azure. Auditoria também:

- Ajuda-o a manter a conformidade regulamentar, compreender a atividade de base de dados e obter informações sobre discrepâncias e anomalias que podem indicar preocupações empresariais ou suspeitas de violações de segurança.
- Ativa e facilita o cumprimento das normas de conformidade, embora ele não garante a conformidade. Para obter mais informações sobre o Azure programas de conformidade de padrões esse suporte, consulte a [Centro de fidedignidade do Azure](https://azure.microsoft.com/support/trust-center/compliance/).

## <a name="set-up-auditing-for-your-server-to-azure-storage"></a>Configurar a auditoria para o seu servidor para o armazenamento do Azure

A seguinte secção descreve a configuração de auditoria na sua instância gerida.

1. Aceda ao [Portal do Azure](https://portal.azure.com).
1. Criar um armazenamento do Azure **contentor** onde estão armazenados os registos de auditoria.

   1. Navegue para o armazenamento do Azure onde pretende armazenar os seus registos de auditoria.

      > [!IMPORTANT]
      > Utilize uma conta de armazenamento na mesma região que o servidor de instância gerida para evitar leituras/escritas entre regiões.

   1. Na conta de armazenamento, aceda a **descrição geral** e clique em **Blobs**.

      ![Widget de Blobs do Azure](./media/sql-managed-instance-auditing/1_blobs_widget.png)

   1. No menu superior, clique em **+ contentor** para criar um novo contentor.

      ![Criar o ícone do contentor de BLOBs](./media/sql-managed-instance-auditing/2_create_container_button.png)

   1. Fornecem um recipiente **nome**, defina o acesso público ao nível para **privada**e, em seguida, clique em **OK**.

     ![Criar configuração de contentor de BLOBs](./media/sql-managed-instance-auditing/3_create_container_config.png)

1. Depois de criar o contentor da auditoria, registos lá são duas formas de configurá-lo como o destino para os registos de auditoria: [com o T-SQL](#blobtsql) ou [usando o SQL Server Management Studio (SSMS) da interface do Usuário](#blobssms):

   - <a id="blobtsql"></a>Configure o armazenamento de blog para registos de auditoria com T-SQL:

     1. Na lista de contentores, clique com o contentor criado recentemente e, em seguida, clique em **propriedades do contentor**.

        ![Botão de propriedades do contentor de BLOBs](./media/sql-managed-instance-auditing/4_container_properties_button.png)

     1. Copie o URL de contentor ao clicar no ícone de copiar e guardar o URL (por exemplo, no bloco de notas) para utilização futura. Deve ser o formato de URL do contentor `https://<StorageName>.blob.core.windows.net/<ContainerName>`

        ![URL de cópia de contentor do blob](./media/sql-managed-instance-auditing/5_container_copy_name.png)

     1. Gerar um armazenamento do Azure **Token de SAS** para conceder direitos de acesso de auditoria de instância gerida para a conta de armazenamento:

        - Navegue para a conta de armazenamento do Azure onde criou o contentor no passo anterior.

        - Clique em **assinatura de acesso partilhado** no menu de definições de armazenamento.

          ![Partilhado o ícone de assinatura de acesso no menu de definições de armazenamento](./media/sql-managed-instance-auditing/6_storage_settings_menu.png)

        - Configure a SAS da seguinte forma:

          - **Permitido serviços**: Blobs

          - **Data de início**: para evitar problemas relacionados com o fuso horário, é recomendável usar a data de ontem

          - **Data de fim**: escolher a data em que este Token de SAS expira

            > [!NOTE]
            > Renove o token após a expiração: para evitar falhas de auditoria.

          - Clique em **Gerar SAS**.
            
            ![Configuração da SAS](./media/sql-managed-instance-auditing/7_sas_configure.png)

        - Depois de clicar em gerar SAS, o Token de SAS é apresentada na parte inferior. Copie o token ao clicar no ícone de copiar e guardá-lo (por exemplo, no bloco de notas) para utilização futura.

          ![Copie o SAS token](./media/sql-managed-instance-auditing/8_sas_copy.png)

          > [!IMPORTANT]
          > Remover o ponto de interrogação ("?") caráter desde o início do token.

     1. Ligar à sua instância gerida através do SQL Server Management Studio (SSMS) ou qualquer outra ferramenta de suporte.

     1. Execute a seguinte instrução T-SQL para **criar uma nova credencial** através do URL de contentor e Token SAS que criou nos passos anteriores:

        ```SQL
        CREATE CREDENTIAL [<container_url>]
        WITH IDENTITY='SHARED ACCESS SIGNATURE',
        SECRET = '<SAS KEY>'
        GO
        ```

     1. Execute a seguinte instrução T-SQL para criar um novo Server Audit (escolher o seu próprio nome de auditoria, utilize o URL de contentor que criou nos passos anteriores). Se não for especificado, `RETENTION_DAYS` padrão é 0 (retenção ilimitada):

        ```SQL
        CREATE SERVER AUDIT [<your_audit_name>]
        TO URL ( PATH ='<container_url>' [, RETENTION_DAYS =  integer ])
        GO
        ```

      1. Continue ao [criação de uma especificação de auditoria de servidor ou a especificação de auditoria de base de dados](#createspec)

   - <a id="blobssms"></a>Configure o armazenamento de BLOBs para registos de auditoria com o SQL Server Management Studio (SSMS) 18 (pré-visualização):

     1. Ligue-se para a instância gerida através da interface do Usuário do SQL Server Management Studio (SSMS).

     1. Expanda a nota de raiz do pesquisador de objetos.

     1. Expanda o **segurança** nó, o botão direito do mouse sobre o **auditorias** nó e clique em "Novo auditoria":

        ![Expanda a segurança e auditoria de nó](./media/sql-managed-instance-auditing/10_mi_SSMS_new_audit.png)

     1. Certifique-se de que "URL" está selecionado na **destino de auditoria** e clique em **procurar**:

        ![Procurar no armazenamento do Azure](./media/sql-managed-instance-auditing/11_mi_SSMS_audit_browse.png)

     1. (Opcional) Inicie sessão sua conta do Azure:

        ![Iniciar sessão no Azure](./media/sql-managed-instance-auditing/12_mi_SSMS_sign_in_to_azure.png)

     1. Selecione uma subscrição, a conta de armazenamento e o contentor de BLOBs as listas pendentes ou criar seu próprio contentor ao clicar em **criar**. Quando terminar de clique **OK**:

        ![Selecione a subscrição do Azure, conta de armazenamento e contentor de blobl](./media/sql-managed-instance-auditing/13_mi_SSMS_select_subscription_account_container.png)

     1. Clique em **OK** na caixa de diálogo "Create Audit".

1. <a id="createspec"></a>Depois de configurar o contentor de BLOBs como destino para os registos de auditoria, crie uma especificação de auditoria de servidor ou a especificação de auditoria de base de dados tal como faria para o SQL Server:

   - [Criar o guia de T-SQL de especificação de auditoria de servidor](https://docs.microsoft.com/sql/t-sql/statements/create-server-audit-specification-transact-sql)
   - [Criar o guia de T-SQL de especificação de auditoria de base de dados](https://docs.microsoft.com/sql/t-sql/statements/create-database-audit-specification-transact-sql)

1. Ative a auditoria de servidor que criou no passo 6:

    ```SQL
    ALTER SERVER AUDIT [<your_audit_name>]
    WITH (STATE=ON);
    GO
    ```

Para obter informações adicionais:

- [Auditoria de diferenças entre a instância gerida, BD SQL do Azure e SQL Server](#auditing-differences-between-managed-instance-azure-sql-database-and-sql-server)
- [CRIAR A AUDITORIA DE SERVIDOR](https://docs.microsoft.com/sql/t-sql/statements/create-server-audit-transact-sql)
- [ALTER SERVER AUDIT](https://docs.microsoft.com/sql/t-sql/statements/alter-server-audit-transact-sql)

## <a name="set-up-auditing-for-your-server-to-event-hub-or-log-analytics"></a>Configurar a auditoria para o seu servidor para o Hub de eventos ou o Log Analytics

Registos de auditoria a partir de uma instância gerida podem ser enviados para o mesmo Hubs ou o Log Analytics através do Azure Monitor. Esta secção descreve como configurar esta opção:

1. Navegue no [Portal do Azure](https://portal.azure.com/) para o SQL instância gerida.

2. Clique em **das definições de diagnóstico**.

3. Clique em **ativar os diagnósticos**. Se o diagnóstico já está ativado o *+ Adicionar definição de diagnóstico* mostrará, em vez disso.

4. Selecione **SQLSecurityAuditEvents** na lista de registos.

5. Selecione um destino para os eventos de auditoria - Hub de eventos, o Log Analytics ou ambos. Para cada destino, configure os parâmetros necessários (por exemplo, área de trabalho do Log Analytics).

6. Clique em **Guardar**.

    ![Configurar definições de diagnóstico](./media/sql-managed-instance-auditing/9_mi_configure_diagnostics.png)

7. Ligue-se para a instância gerida utilizando **SQL Server Management Studio (SSMS)** ou qualquer outro cliente suportado.

8. Execute a seguinte instrução T-SQL para criar uma auditoria de servidor:

    ```SQL
    CREATE SERVER AUDIT [<your_audit_name>] TO EXTERNAL_MONITOR;
    GO
    ```

9. Crie uma especificação de auditoria de servidor ou a especificação de auditoria de base de dados, tal como faria para o SQL Server:

   - [Criar o guia de T-SQL de especificação de auditoria de servidor](https://docs.microsoft.com/sql/t-sql/statements/create-server-audit-specification-transact-sql)
   - [Criar o guia de T-SQL de especificação de auditoria de base de dados](https://docs.microsoft.com/sql/t-sql/statements/create-database-audit-specification-transact-sql)

10. Ative a auditoria de servidor que criou no passo 7:
 
    ```SQL
    ALTER SERVER AUDIT [<your_audit_name>] WITH (STATE=ON);
    GO
    ```

## <a name="consume-audit-logs"></a>Consumir registos de auditoria

### <a name="consume-logs-stored-in-azure-storage"></a>Consumir registos armazenados no armazenamento do Azure

Existem vários métodos que pode utilizar para ver os registos de auditoria de Blobs.

- Utilize a função de sistema `sys.fn_get_audit_file` (T-SQL) para retornar os dados de registo de auditoria num formato tabular. Para obter mais informações sobre como utilizar esta função, consulte a [sys.fn_get_audit_file documentação](https://docs.microsoft.com/sql/relational-databases/system-functions/sys-fn-get-audit-file-transact-sql).

- Pode explorar os registos de auditoria utilizando uma ferramenta como [Explorador de armazenamento do Azure](https://azure.microsoft.com/features/storage-explorer/). No armazenamento do Azure, os registos de auditoria são guardados como uma coleção de ficheiros de blob num contentor que foi definida para armazenar os registos de auditoria. Para obter mais detalhes sobre a hierarquia da pasta de armazenamento, as convenções de nomenclatura e formato de registo, consulte a [referência de formato de registo de auditoria de Blob](https://go.microsoft.com/fwlink/?linkid=829599).

- Para obter uma lista completa dos métodos de consumo de log de auditoria, consulte a [introdução à auditoria da base de dados SQL](https://docs.microsoft.com/azure/sql-database/sql-database-auditing).

  > [!IMPORTANT]
  > Visualizar os registos de auditoria do portal do Azure (painel de registos de auditoria) não está atualmente disponível para a instância gerida.

### <a name="consume-logs-stored-in-event-hub"></a>Consumir registos armazenados no Hub de eventos

Para consumir dados de registos de auditoria do Hub de eventos, terá de configurar um fluxo para consumir eventos e escrevê-los para um destino. Para obter mais informações, consulte a documentação de Hubs de eventos do Azure.

### <a name="consume-and-analyze-logs-stored-in-log-analytics"></a>Consumir e analisar registos armazenados no Log Analytics

Se os registos de auditoria são escritos para o Log Analytics, estão disponíveis na área de trabalho do Log Analytics, onde pode executar pesquisas avançadas nos dados de auditoria. Como ponto de partida, navegue para o Log Analytics e, em *gerais* secção clique *registos* e introduza uma consulta simples, tais como: `search "SQLSecurityAuditEvents"` para ver a auditoria de registos.  

O log Analytics dá-lhe as informações operacionais em tempo real através da pesquisa integrada e dashboards personalizados para analisar, prontamente, milhões de registos em todas as suas cargas de trabalho e servidores. Para obter informações adicionais úteis sobre comandos e linguagem de pesquisa do Log Analytics, consulte [referência de pesquisa do Log Analytics](https://docs.microsoft.com/azure/azure-monitor/log-query/log-query-overview).

## <a name="auditing-differences-between-managed-instance-azure-sql-database-and-sql-server"></a>Auditoria de diferenças entre a instância gerida, base de dados do Azure SQL e SQL Server

As principais diferenças entre auditoria de SQL na instância gerida, base de dados do Azure SQL e SQL Server no local são:

- Na instância gerida, a auditoria de SQL funciona no nível do servidor e arquivos `.xel` ficheiros na conta de armazenamento de Blobs do Azure de registo.
- Na base de dados SQL do Azure, a auditoria de SQL funciona ao nível da base de dados.
- No SQL Server no local / virtual máquinas, funciona de auditoria de SQL no servidor de nível, mas os eventos de arquivos no sistema de ficheiros/windows registos de eventos.

Auditoria de XEvent na instância gerida suporta destinos de armazenamento de Blobs do Azure. Os registos de ficheiros e do windows estão **nepodporuje**.

A chave de diferenças no `CREATE AUDIT` sintaxe para a auditoria para o armazenamento de Blobs do Azure são:

- Uma nova sintaxe `TO URL` é fornecido e permite-lhe especificar o URL do contentor de armazenamento de Blobs do Azure onde o `.xel` ficheiros são colocados.
- Uma nova sintaxe `TO EXTERNAL MONITOR` é fornecida para ativar os destinos de até mesmo Hub e o Log Analytics.
- A sintaxe `TO FILE` é **nepodporuje** porque a instância gerida não é possível aceder a partilhas de ficheiros do Windows.
- É a opção Encerrar **nepodporuje**.
- `queue_delay` 0 é **nepodporuje**.

## <a name="next-steps"></a>Passos Seguintes

- Para obter uma lista completa dos métodos de consumo de log de auditoria, consulte a [introdução à auditoria da base de dados SQL](https://docs.microsoft.com/azure/sql-database/sql-database-auditing).
- Para obter mais informações sobre o Azure programas de conformidade de padrões esse suporte, consulte a [Centro de fidedignidade do Azure](https://azure.microsoft.com/support/trust-center/compliance/).

<!--Image references-->









