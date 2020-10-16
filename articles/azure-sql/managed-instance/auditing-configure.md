---
title: Auditoria de casos geridos sql
description: Saiba como começar com a auditoria da Azure SQL Managed Instance utilizando o T-SQL
services: sql-database
ms.service: sql-managed-instance
ms.subservice: security
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
f1_keywords:
- mi.azure.sqlaudit.general.f1
author: DavidTrigano
ms.author: datrigan
ms.reviewer: vanto
ms.date: 05/26/2020
ms.openlocfilehash: d8a6ead23e080b5e1e17403873e2dbaedc0ce177
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91620363"
---
# <a name="get-started-with-azure-sql-managed-instance-auditing"></a>Começar com a auditoria da Azure SQL Managed Instance
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

[A Azure SQL Managed Instance](sql-managed-instance-paas-overview.md) auditando rastreia eventos de base de dados e escreve-os para um registo de auditoria na sua conta de armazenamento Azure. A auditoria também:

- Ajuda a manter a conformidade regulatória, a compreender as atividades da base de dados e a obter informações relativas a discrepâncias e anomalias que possam indicar preocupações comerciais ou suspeitas de violações de segurança.
- Ativa e facilita o cumprimento das normas de conformidade, apesar de não garantir a conformidade. Para obter mais informações sobre os programas Azure que suportam o cumprimento das normas, consulte o [Azure Trust Center,](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942)onde pode encontrar a lista mais atual de certificações de conformidade.

## <a name="set-up-auditing-for-your-server-to-azure-storage"></a>Configurar a auditoria do seu servidor para o Azure Storage

A secção seguinte descreve a configuração da auditoria no seu caso gerido.

1. Aceda ao [portal do Azure](https://portal.azure.com).
2. Crie um **recipiente** de armazenamento Azure onde os registos de auditoria são armazenados.

   1. Navegue para a conta de armazenamento Azure onde pretende guardar os seus registos de auditoria.

      > [!IMPORTANT]
      > - Utilize uma conta de armazenamento na mesma região que a instância gerida para evitar leituras/escritas entre regiões. 
      > - Se a sua conta de armazenamento estiver por detrás de uma Rede Virtual ou de uma Firewall, consulte [o acesso do Grant a partir de uma rede virtual.](https://docs.microsoft.com/azure/storage/common/storage-network-security#grant-access-from-a-virtual-network)
      > - Se alterar o período de retenção de 0 (retenção ilimitada) para qualquer outro valor, note que a retenção só se aplicará aos registos escritos após a alteração do valor da retenção (os registos escritos durante o período em que a retenção foi definida para ilimitada são preservados, mesmo após a retenção ser ativada).

   1. Na conta de armazenamento, vá ao **Overview** e clique em **Blobs**.

      ![Widget Azure Blobs](./media/auditing-configure/1_blobs_widget.png)

   1. No menu superior, clique **em + Recipiente** para criar um novo recipiente.

      ![Criar ícone de recipiente de bolha](./media/auditing-configure/2_create_container_button.png)

   1. Fornecer um nome **de**recipiente, definir **o nível de acesso público** ao **Privado,** e, em seguida, clicar **EM OK**.

      ![Criar configuração de recipiente de bolhas](./media/auditing-configure/3_create_container_config.png)

    > [!IMPORTANT]
    > Os clientes que desejem configurar uma loja de registo imutável para os seus eventos de auditoria ao nível do servidor ou da base de dados devem seguir as [instruções fornecidas pelo Azure Storage](https://docs.microsoft.com/azure/storage/blobs/storage-blob-immutability-policies-manage#enabling-allow-protected-append-blobs-writes). (Certifique-se de que selecionou **Permita apêndices adicionais** quando configurar o armazenamento de bolhas imutáveis.)
  
3. Depois de criar o contentor para os registos de auditoria, existem duas formas de o configurar como o alvo dos registos de auditoria: utilizar o [T-SQL](#blobtsql) ou [utilizar o SQL Server Management Studio (SSMS) UI](#blobssms):

   - <a id="blobtsql"></a>Configure o armazenamento de bolhas para registos de auditoria utilizando o T-SQL:

     1. Na lista de recipientes, clique no recipiente recém-criado e, em seguida, clique nas **propriedades do Contentor**.

        ![Botão de propriedades do recipiente blob](./media/auditing-configure/4_container_properties_button.png)

     1. Copie o URL do recipiente clicando no ícone da cópia e guarde o URL (por exemplo, no Bloco de Notas) para utilização futura. O formato URL do contentor deve ser `https://<StorageName>.blob.core.windows.net/<ContainerName>`

        ![URL de cópia de recipiente de bolha](./media/auditing-configure/5_container_copy_name.png)

     1. Gere um **token SAS de** armazenamento Azure para conceder a auditoria de instância gerida direitos de acesso à conta de armazenamento:

        - Navegue até à conta de armazenamento Azure onde criou o recipiente no passo anterior.

        - Clique na **assinatura de acesso partilhado** no menu **Definições de Armazenamento.**

          ![Ícone de assinatura de acesso compartilhado no menu de definições de armazenamento](./media/auditing-configure/6_storage_settings_menu.png)

        - Configure o SAS da seguinte forma:

          - **Serviços permitidos**: Blob

          - **Data de**início : para evitar problemas relacionados com fuso horário, use a data de ontem

          - **Data de fim**: escolha a data em que este token SAS expira

            > [!NOTE]
            > Renove o token após o termo para evitar falhas de auditoria.

          - Clique em **Gerar SAS**.

            ![Configuração SAS](./media/auditing-configure/7_sas_configure.png)

        - O símbolo SAS aparece na parte inferior. Copie o token clicando no ícone da cópia e guarde-o (por exemplo, no Bloco de Notas) para utilização futura.

          ![Cópia sas token](./media/auditing-configure/8_sas_copy.png)

          > [!IMPORTANT]
          > Remova o personagem do ponto de interrogação ("?") do início do token.

     1. Ligue-se à sua instância gerida através do SQL Server Management Studio ou de qualquer outra ferramenta suportada.

     1. Execute a seguinte declaração T-SQL para **criar uma nova credencial** utilizando o URL do contentor e o símbolo SAS que criou nos passos anteriores:

        ```SQL
        CREATE CREDENTIAL [<container_url>]
        WITH IDENTITY='SHARED ACCESS SIGNATURE',
        SECRET = '<SAS KEY>'
        GO
        ```

     1. Execute a seguinte declaração T-SQL para criar uma nova auditoria ao servidor (escolha o seu próprio nome de auditoria e use o URL do contentor que criou nos passos anteriores). Se não for especificado, o `RETENTION_DAYS` padrão é 0 (retenção ilimitada):

        ```SQL
        CREATE SERVER AUDIT [<your_audit_name>]
        TO URL ( PATH ='<container_url>' , RETENTION_DAYS =  integer )
        GO
        ```

        Continue [criando uma especificação de auditoria de servidor ou especificação de auditoria de base de dados](#createspec).

   - <a id="blobssms"></a>Configure o armazenamento de bolhas para registos de auditoria utilizando o SQL Server Management Studio 18 (Preview):

     1. Ligue-se à instância gerida utilizando o SQL Server Management Studio UI.

     1. Expanda a nota de raiz do Object Explorer.

     1. Expandir o nó de **Segurança,** clicar com o botão direito no nó **de Auditorias** e clicar em **Nova Auditoria:**

        ![Expandir o nó de segurança e auditoria](./media/auditing-configure/10_mi_SSMS_new_audit.png)

     1. Certifique-se de que **o URL** está selecionado no **destino de Auditoria** e clique em **Procurar:**

        ![Navegue no armazenamento Azure](./media/auditing-configure/11_mi_SSMS_audit_browse.png)

     1. (Opcional) Inscreva-se na sua conta Azure:

        ![Iniciar sessão no Azure](./media/auditing-configure/12_mi_SSMS_sign_in_to_azure.png)

     1. Selecione uma subscrição, conta de armazenamento e recipiente blob a partir das dropdowns, ou crie o seu próprio recipiente clicando no **Create**. Uma vez terminado, clique **em OK:**

        ![Selecione subscrição Azure, conta de armazenamento e recipiente blob](./media/auditing-configure/13_mi_SSMS_select_subscription_account_container.png)

     1. Clique em **OK** no diálogo **de Auditoria criar.**

4. <a id="createspec"></a>Depois de configurar o recipiente blob como alvo para os registos de auditoria, crie e ative uma especificação de auditoria do servidor ou especificação de auditoria de base de dados como faria para o SQL Server:

   - [Criar guia T-SQL de especificação de auditoria do servidor](https://docs.microsoft.com/sql/t-sql/statements/create-server-audit-specification-transact-sql)
   - [Criar guia T-SQL de especificação de auditoria de base de dados](https://docs.microsoft.com/sql/t-sql/statements/create-database-audit-specification-transact-sql)

5. Ativar a auditoria do servidor que criou no passo 3:

    ```SQL
    ALTER SERVER AUDIT [<your_audit_name>]
    WITH (STATE=ON);
    GO
    ```

Para obter informações adicionais:

- [Diferenças de auditoria entre Azure SQL Managed Instance e uma base de dados no SQL Server](#auditing-differences-between-databases-in-azure-sql-managed-instance-and-databases-in-sql-server)
- [CRIAR AUDITORIA DE SERVIDOR](https://docs.microsoft.com/sql/t-sql/statements/create-server-audit-transact-sql)
- [ALTERAR AUDITORIA DO SERVIDOR](https://docs.microsoft.com/sql/t-sql/statements/alter-server-audit-transact-sql)

## <a name="set-up-auditing-for-your-server-to-event-hubs-or-azure-monitor-logs"></a>Configurar a auditoria do seu servidor para os logins do Event Hubs ou do Azure Monitor

Os registos de auditoria de uma instância gerida podem ser enviados para os centros de eventos do Azure ou para os registos do Azure Monitor. Esta secção descreve como configurar isto:

1. Navegue no [portal Azure](https://portal.azure.com/) para o caso gerido.

2. Clique nas **definições de Diagnóstico**.

3. Clique **em Ligar os diagnósticos**. Se os diagnósticos já estiverem ativados, **+Adicione a definição de diagnóstico.**

4. Selecione **SQLSecurityAuditEvents** na lista de registos.

5. Selecione um destino para os eventos de auditoria: Centros de Eventos, registos do Azure Monitor ou ambos. Configure para cada alvo os parâmetros necessários (por exemplo, log analytics espaço de trabalho).

6. Clique em **Guardar**.

    ![Configurar configurações de diagnóstico](./media/auditing-configure/9_mi_configure_diagnostics.png)

7. Ligue-se à instância gerida utilizando o **SQL Server Management Studio (SSMS)** ou qualquer outro cliente suportado.

8. Execute a seguinte declaração T-SQL para criar uma auditoria ao servidor:

    ```SQL
    CREATE SERVER AUDIT [<your_audit_name>] TO EXTERNAL_MONITOR;
    GO
    ```

9. Crie e ative uma especificação de auditoria de servidor ou especificação de auditoria de base de dados como faria para o SQL Server:

   - [Criar guia T-SQL de especificação de auditoria do servidor](https://docs.microsoft.com/sql/t-sql/statements/create-server-audit-specification-transact-sql)
   - [Criar guia T-SQL de especificação de auditoria de base de dados](https://docs.microsoft.com/sql/t-sql/statements/create-database-audit-specification-transact-sql)

10. Ativar a auditoria do servidor criada no passo 8:

    ```SQL
    ALTER SERVER AUDIT [<your_audit_name>]
    WITH (STATE=ON);
    GO
    ```

## <a name="consume-audit-logs"></a>Consumir registos de auditoria

### <a name="consume-logs-stored-in-azure-storage"></a>Consumir troncos armazenados em Armazenamento Azure

Existem vários métodos que pode usar para visualizar registos de auditoria de blob.

- Utilize a função do sistema `sys.fn_get_audit_file` (T-SQL) para devolver os dados de registo de auditoria em formato tabular. Para obter mais informações sobre a utilização desta função, consulte a [documentação sys.fn_get_audit_file](https://docs.microsoft.com/sql/relational-databases/system-functions/sys-fn-get-audit-file-transact-sql).

- Pode explorar registos de auditoria utilizando uma ferramenta como [o Azure Storage Explorer.](https://azure.microsoft.com/features/storage-explorer/) No Azure Storage, os registos de auditoria são guardados como uma coleção de ficheiros blob dentro de um recipiente que foi definido para armazenar os registos de auditoria. Para mais detalhes sobre a hierarquia da pasta de armazenamento, convenções de nomeação e formato de registo, consulte a Referência do [Formato do Registo de Auditoria blob](https://go.microsoft.com/fwlink/?linkid=829599).

- Para obter uma lista completa dos métodos de consumo de registos de auditoria, consulte a [Get start with Azure SQL Database auditing](../../azure-sql/database/auditing-overview.md).

### <a name="consume-logs-stored-in-event-hubs"></a>Consumir registos armazenados em Centros de Eventos

Para consumir dados de registos de auditoria de Event Hubs, terá de configurar um fluxo para consumir eventos e escrevê-los para um alvo. Para mais informações, consulte a documentação do Azure Event Hubs.

### <a name="consume-and-analyze-logs-stored-in-azure-monitor-logs"></a>Consumir e analisar registos armazenados em registos do Monitor Azure

Se os registos de auditoria forem escritos para os registos do Azure Monitor, estes estão disponíveis no espaço de trabalho do Log Analytics, onde pode executar pesquisas avançadas nos dados da auditoria. Como ponto de partida, navegue para o espaço de trabalho Log Analytics. Na secção **Geral,** clique em **Registos** e introduza uma consulta simples, como: `search "SQLSecurityAuditEvents"` para ver os registos de auditoria.  

Os registos do Azure Monitor dão-lhe informações operacionais em tempo real utilizando painéis de pesquisa integrados e dashboards personalizados para analisar facilmente milhões de registos em todas as suas cargas de trabalho e servidores. Para obter informações úteis adicionais sobre o Azure Monitor regista linguagem e comandos de pesquisa, consulte a referência de pesquisa de registos do [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/log-query/log-query-overview).

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="auditing-differences-between-databases-in-azure-sql-managed-instance-and-databases-in-sql-server"></a>Auditoria de diferenças entre bases de dados em Azure SQL Gested Instance e bases de dados no SQL Server

As principais diferenças entre a auditoria em bases de dados em Azure SQL Managed Instance e bases de dados no SQL Server são:

- Com a Azure SQL Managed Instance, a auditoria funciona ao nível do servidor e armazena `.xel` ficheiros de registo no armazenamento Azure Blob.
- No SQL Server, a auditoria funciona ao nível do servidor, mas armazena eventos em registos de eventos do sistema de ficheiros/janelas.

A auditoria do XEvent em casos geridos suporta alvos de armazenamento Azure Blob. Os registos de ficheiros e janelas não são **suportados**.

As principais diferenças na `CREATE AUDIT` sintaxe para a auditoria ao armazenamento da Azure Blob são:

- É fornecida uma nova sintaxe `TO URL` e permite especificar o URL do recipiente de armazenamento Azure Blob onde os `.xel` ficheiros são colocados.
- É fornecida uma nova sintaxe `TO EXTERNAL MONITOR` para permitir os alvos de registo de Eventos e Azure Monitor.
- A sintaxe `TO FILE` não é **suportada** porque a Azure SQL Managed Instance não consegue aceder a ações de ficheiros do Windows.
- A opção de encerramento não é **suportada**.
- `queue_delay` de 0 não é **suportado**.

## <a name="next-steps"></a>Passos seguintes

- Para obter uma lista completa dos métodos de consumo de registos de auditoria, consulte a [Get start with Azure SQL Database auditing](../../azure-sql/database/auditing-overview.md).
- Para obter mais informações sobre os programas Azure que suportam o cumprimento das normas, consulte o [Azure Trust Center,](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942)onde pode encontrar a lista mais atual de certificações de conformidade.

<!--Image references-->
