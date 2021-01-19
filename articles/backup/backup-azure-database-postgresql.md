---
title: Cópia de Segurança da Base de Dados do Azure para PostgreSQL
description: Saiba mais sobre a Base de Dados Azure para cópia de segurança pós-SQL com retenção a longo prazo (pré-visualização)
ms.topic: conceptual
ms.date: 09/08/2020
ms.custom: references_regions
ms.openlocfilehash: 628f43788c11558185a7b8a735373f0a663fdad1
ms.sourcegitcommit: 9d9221ba4bfdf8d8294cf56e12344ed05be82843
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/19/2021
ms.locfileid: "98569492"
---
# <a name="azure-database-for-postgresql-backup-with-long-term-retention-preview"></a>Base de Dados Azure para backup pós-SQL com retenção a longo prazo (pré-visualização)

Os Serviços de Backup e Azure Database Services juntaram-se para construir uma solução de backup de classe empresarial para a Azure Database para servidores PostgreSQL que retém backups por até 10 anos.

Além da retenção a longo prazo, a solução também tem muitas outras capacidades como listado abaixo:

- Azure controlo de acesso baseado em funções (Azure RBAC) para a base de dados usando a autenticação Azure Ative Directory e Managed Service Identity (MSI).
- Backup de horários e on-demand controlado pelo cliente a nível de base de dados individual.
- O nível de base de dados restaura a qualquer servidor de Postgres ou diretamente ao armazenamento de bolhas.
- Retenção de longa duração.
- Monitorização central de todas as operações e postos de trabalho.
- As cópias de segurança são armazenadas em domínios de segurança e falhas separados. Assim, mesmo que o servidor de origem fosse comprometido ou mesmo morto, os backups permaneceriam seguros no [cofre de reserva.](backup-vault-overview.md)
- A utilização de **pg_dump** permite uma maior flexibilidade nos restauros de modo a que possa restaurar através das versões de base de dados ou até mesmo restaurar apenas uma parte da cópia de segurança.

Pode utilizar esta solução de forma independente ou além da solução de backup nativa oferecida pela Azure PostgreSQL que oferece retenção até 35 dias. A solução nativa é adequada para recuperações operacionais, como quando se quer recuperar dos últimos backups. A solução Azure Backup ajuda-o com as suas necessidades de conformidade e com uma cópia de segurança mais granular e flexível e restauração.

## <a name="support-matrix"></a>Matriz de suporte

|Suporte  |Detalhes  |
|---------|---------|
|Implantações apoiadas   |  [Base de Dados do Azure para PostgreSQL – Servidor Único](https://docs.microsoft.com/azure/postgresql/overview#azure-database-for-postgresql---single-server)     |
|Regiões de Azure apoiadas |  Leste dos EUA, Leste dos EUA 2, Central EUA, Centro Sul dos EUA, Oeste dos EUA 2, West Central EUA, Brasil Sul, Canadá Central, Europa do Norte, Europa Ocidental, Reino Unido Sul, Reino Unido Oeste, Alemanha West Central, Suíça Norte, Suíça Oeste, Ásia Oriental, Ásia Do Sudeste, Japão Oriental, Japão Ocidental, Coreia Central, Coreia do Sul, Índia Central, Austrália Oriental, Austrália Central 2, UAE  |
|Versões Azure PostgreSQL suportadas    |   9.5, 9.6, 10, 11      |

## <a name="feature-considerations-and-limitations"></a>Considerações e limitações de recursos

- Todas as operações são apoiadas apenas a partir do portal Azure.
- O limite recomendado para o tamanho máximo da base de dados é de 400 GB.
- O apoio de toda a região não é apoiado. Isto significa que não pode apoiar um servidor Azure PostgreSQL para um cofre noutra região. Da mesma forma, só é possível restaurar uma cópia de segurança num servidor dentro da mesma região que o cofre.
- Apenas os dados são recuperados no momento da restauração. "Papéis" não são restaurados.
- Na pré-visualização, recomendamos que execute a solução apenas no seu ambiente de teste.

## <a name="backup-process"></a>Processo de cópia de segurança

1. Esta solução utiliza **pg_dump** para obter cópias de segurança das suas bases de dados Azure PostgreSQL.

2. Uma vez especificadas as bases de dados Azure PostgreSQL que pretende fazer backup, o serviço valida se tiver o conjunto certo de permissões e acesso para realizar a operação de backup no servidor e na base de dados.

3. A Azure Backup gira um papel de trabalhador com uma extensão de reserva instalada no mesmo. Esta extensão comunica com o servidor Postgres.

4. Esta extensão é constituída por um coordenador e um plugin Azure Postgres. Enquanto o coordenador é responsável por desencadear fluxos de trabalho para várias operações como configurar backup, backup e restauro, o plugin é responsável pelo fluxo de dados real.
  
5. Assim que acionar a proteção de configuração nas bases de dados selecionadas, o serviço de cópia de segurança configura o coordenador com os horários de backup e outros detalhes da política.

6. Na hora programada, o coordenador comunica com o plugin e começa a transmitir os dados de backup do servidor Postgres utilizando **pg_dump**.

7. O plugin envia os dados diretamente para o cofre de reserva, eliminando a necessidade de um local de paragem. Os dados são encriptados utilizando as chaves geridas pela Microsoft e armazenados pelo serviço Azure Backup em contas de armazenamento.

8. Quando a transferência de dados estiver concluída, o coordenador confirma o compromisso com o serviço de cópia de segurança.

    ![Processo de cópia de segurança](./media/backup-azure-database-postgresql/backup-process.png)

## <a name="configure-backup-on-azure-postgresql-databases"></a>Configure backup em bases de dados Azure PostgreSQL

Pode configurar cópias de segurança em várias bases de dados através de vários servidores Azure PostgreSQL. Certifique-se de que as [permissões pré-requisitos exigidas](#prerequisite-permissions-for-configure-backup-and-restore) pelo serviço para fazer o back up dos servidores Postgres já estão configuradas.

As seguintes instruções são um guia passo a passo para configurar a cópia de segurança nas bases de dados Azure PostgreSQL utilizando o Azure Backup:

1. Há duas maneiras de iniciar o processo:

    1. Vá para [backup Center](backup-center-overview.md)  ->  **Overview**  ->  **Backup**.

        ![Ir para o Centro de Apoio](./media/backup-azure-database-postgresql/backup-center.png)

        Em **Iniciar: Configurar backup,** selecione o **tipo de fonte de dados** como Base de **Dados para PostgreSQL**.

        ![In Iniciar: Configurar backup, selecionar tipo de fonte de dados](./media/backup-azure-database-postgresql/initiate-configure-backup.png)

    1. Em alternativa, pode [dirigir-se](backup-vault-overview.md)diretamente aos cofres  ->  **de reserva.**

        ![Vá para cofres de reserva](./media/backup-azure-database-postgresql/backup-vaults.png)

        ![Selecione Backup no cofre de backup](./media/backup-azure-database-postgresql/backup-backup-vault.png)

1. Em **cópia de segurança configurada,** selecione o cofre de **reserva** para o qual deseja fazer backup das bases de dados postgres. Esta informação é pré-preenchida se já estiver no contexto do cofre.

    ![Selecione o cofre de backup em cópia de segurança Configure](./media/backup-azure-database-postgresql/configure-backup.png)

1. Selecione ou crie uma **política de backup**.

    ![Escolha a política de backup](./media/backup-azure-database-postgresql/backup-policy.png)

1. Selecione recursos ou bases de dados postgres para fazer o back up.

    ![Selecione recursos para fazer back-up](./media/backup-azure-database-postgresql/select-resources.png)

1. Pode escolher entre a lista de todos os servidores Azure PostgreSQL através de subscrições se estiverem na mesma região que o cofre. Expanda a seta para ver a lista de bases de dados dentro de um servidor.

    ![Escolha servidores](./media/backup-azure-database-postgresql/choose-servers.png)

1. O serviço executa estas verificações nas bases de dados selecionadas para validar se o cofre tem permissões para fazer o back up dos servidores e bases de dados de Postgres selecionados.
    1. **A prontidão de backup** para todas as bases de dados deve ler **O Sucesso** para continuar.
    1. Se houver um erro, **ou corrija** o erro e **revalida ou** remova a base de dados das seleções.

    ![Erros de validação para corrigir](./media/backup-azure-database-postgresql/validation-errors.png)

1. Confirme todos os detalhes em **Revisão e configuração** e selecione **cópia de segurança de configuração** para submeter a operação.

    ![Confirme detalhes em Revisão e configuração](./media/backup-azure-database-postgresql/review-and-configure.png)

1. Uma vez acionado, a **operação de backup Configure** criará uma instância de backup. Pode rastrear o estado da operação no painel [de instâncias de backup](backup-center-monitor-operate.md#backup-instances) no Centro de Reserva ou na vista do cofre.

    ![Instâncias de backup](./media/backup-azure-database-postgresql/backup-instances.png)

1. As cópias de segurança são ativadas de acordo com o calendário de backup definido na apólice. Os trabalhos podem ser rastreados no âmbito [do Backup Jobs.](backup-center-monitor-operate.md#backup-jobs) Atualmente, pode ver empregos nos últimos sete dias.

    ![Trabalhos de reserva](./media/backup-azure-database-postgresql/backup-jobs.png)

## <a name="create-backup-policy"></a>Criar política de backup

1. Vá para as políticas de backup **do Centro de Backup**  ->    ->  **Add**. Alternativamente, pode ir para a política **de backup do cofre** de reserva  ->    ->  **Adicionar**.

    ![Adicionar política de backup](./media/backup-azure-database-postgresql/add-backup-policy.png)

1. Insira um **nome** para a nova política.

    ![Insira o nome da política](./media/backup-azure-database-postgresql/enter-policy-name.png)

1. Defina o horário de reserva. Atualmente apoiamos o **apoio semanal.** Pode agendar os backups em um ou mais dias da semana.

    ![Defina o horário de backup](./media/backup-azure-database-postgresql/define-backup-schedule.png)

1. Defina **as definições de retenção.** Pode adicionar uma ou mais regras de retenção. Cada regra de retenção pressupõe entradas para cópias de segurança específicas e a duração da loja de dados e retenção para essas cópias de segurança.

1. Pode optar por armazenar as suas cópias de segurança numa das duas lojas de dados (ou níveis): **Loja de dados de backup** (nível padrão) ou loja de **dados Archive** (em pré-visualização). Pode escolher entre **duas opções de tiering** para definir quando as cópias de segurança são niveladas nas duas datastores:

    - Opte por copiar **imediatamente** se preferir ter uma cópia de segurança nas lojas de dados de backup e arquivo simultaneamente.
    - Opte por **mover-se No-expiry** se preferir mover a cópia de segurança para arquivar a loja de dados no seu termo na loja de dados de backup.

1. A **regra de retenção por defeito** é aplicada na ausência de qualquer outra regra de retenção, e tem um valor padrão de três meses.

    - A duração da retenção varia de sete dias a 10 anos na **loja de dados de backup**.
    - A duração da retenção varia de seis meses a 10 anos na **loja de dados do Arquivo.**

    ![Editar a duração da retenção](./media/backup-azure-database-postgresql/edit-retention.png)

>[!NOTE]
>As regras de retenção são avaliadas numa ordem de prioridade pré-determinada. A prioridade é a mais elevada para a regra **anual,** seguida da regra **mensal** e depois **semanal.** As definições de retenção predefinidos são aplicadas quando nenhuma outra regra se qualifica. Por exemplo, o mesmo ponto de recuperação pode ser o primeiro backup bem sucedido tomado todas as semanas, bem como o primeiro backup bem sucedido tirado todos os meses. No entanto, uma vez que a prioridade mensal é superior à da regra semanal, aplica-se a retenção correspondente ao primeiro backup bem sucedido feito todos os meses.

## <a name="restore"></a>Restauro

Pode restaurar uma base de dados em qualquer servidor Azure PostgreSQL dentro da mesma subscrição, se o serviço tiver o conjunto adequado de permissões no servidor alvo. Certifique-se de que as [permissões pré-requisitos exigidas](#prerequisite-permissions-for-configure-backup-and-restore) pelo serviço para fazer o back up dos servidores Postgres já estão configuradas.

Siga este guia passo a passo para desencadear uma restauração:

1. Há duas maneiras de iniciar o processo de restauro:
    1. Vá ao [Back Backup Center](backup-center-overview.md)  ->  **Overview**  ->  **Restore**.

    ![Selecione Restaurar no Centro de Backup](./media/backup-azure-database-postgresql/backup-center-restore.png)

    Em **Iniciar: Restaurar**, selecione o **tipo de fonte de dados** como **Base de Dados para PostgreSQL**. Selecione a **instância de cópia de segurança**.

    ![Selecione o tipo de fonte de dados em Iniciar:Restaurar](./media/backup-azure-database-postgresql/initiate-restore.png)

    1. Em alternativa, pode dirigir-se diretamente às   ->  **instâncias de backup do** cofre de reserva . Selecione **a instância de Backup** correspondente à base de dados que pretende restaurar.

    ![Instâncias de backup para restauro](./media/backup-azure-database-postgresql/backup-instances-restore.png)

    ![Lista de instâncias de backup](./media/backup-azure-database-postgresql/list-backup-instances.png)

    ![Selecione Restaurar](./media/backup-azure-database-postgresql/select-restore.png)

1. **Selecione** o ponto de recuperação da lista de todos os backups completos disponíveis para a instância de backup selecionada. Por predefinição, o último ponto de recuperação é selecionado.

    ![Selecione ponto de recuperação](./media/backup-azure-database-postgresql/select-recovery-point.png)

    ![Lista de pontos de recuperação](./media/backup-azure-database-postgresql/list-recovery-points.png)

1. Inserir **Parâmetros de restauro**. Neste ponto, pode selecionar a partir de dois tipos de restauros: **Restaurar como Base de Dados** e Restaurar como **Ficheiros**.

1. **Restaurar como Base de Dados**: Restaurar os dados de backup para criar uma nova base de dados no servidor PostgreSQL alvo.

    - O servidor alvo pode ser o mesmo que o servidor de origem. No entanto, a sobreescrita da base de dados original não é suportada.
    - Pode escolher entre o servidor em todas as subscrições, mas na mesma região que o cofre.
    - Selecione **Review + Restaurar**. Isto irá desencadear a validação para verificar se o serviço tem permissões de restauro apropriadas no servidor alvo.

    ![Restaurar como base de dados](./media/backup-azure-database-postgresql/restore-as-database.png)

1. **Restaurar como Ficheiros**: Despeje os ficheiros de cópia de segurança na conta de armazenamento do alvo (bolhas).

    - Pode escolher entre as contas de armazenamento em todas as subscrições, mas na mesma região que o cofre.
    - Selecione o recipiente-alvo da lista de recipientes filtrado para a conta de armazenamento selecionada.
    - Selecione **Review + Restaurar**. Isto irá desencadear a validação para verificar se o serviço tem permissões de restauro apropriadas no servidor alvo.

    ![Restaurar como ficheiros](./media/backup-azure-database-postgresql/restore-as-files.png)

1. Reveja as informações e **selecione Restaurar.** Isto irá desencadear um trabalho de Restauro correspondente que pode ser rastreado sob **trabalhos de backup**.

## <a name="prerequisite-permissions-for-configure-backup-and-restore"></a>Permissões pré-requisitos para configurar backup e restaurar

A Azure Backup segue as rígidas diretrizes de segurança. Apesar de ser um serviço nativo da Azure, as permissões sobre o recurso não são assumidas, e precisam ser explicitamente dadas pelo utilizador.  Da mesma forma, as credenciais para ligar à base de dados não são armazenadas. Isto é importante para salvaguardar os seus dados. Em vez disso, usamos a autenticação do Azure Ative Directory.

[Faça o download deste documento](https://download.microsoft.com/download/7/4/d/74d689aa-909d-4d3e-9b18-f8e465a7ebf5/OSSbkpprep_automated.docx) para obter um script automatizado e instruções relacionadas. Concederá um conjunto apropriado de permissões a um servidor Azure PostgreSQL, para cópia de segurança e restauro.

## <a name="manage-the-backed-up-azure-postgresql-databases"></a>Gerir as bases de dados Azure PostgreSQL de back-up

Estas são as operações de gestão que pode realizar nas **Instâncias de Backup:**

### <a name="on-demand-backup"></a>Backup a pedido

Para ativar uma cópia de segurança não no calendário especificado na apólice, vá a **Backup De instâncias de Backup**  ->  **Agora**.
Escolha entre a lista de regras de retenção definidas na política de Backup associada.

![Acionar backup agora](./media/backup-azure-database-postgresql/backup-now.png)

![Escolha entre a lista de regras de retenção](./media/backup-azure-database-postgresql/retention-rules.png)

### <a name="stop-protection"></a>Parar proteção

Pode parar a proteção num item de reserva. Isto também eliminará os pontos de recuperação associados para esse item de backup. Ainda não fornecemos a opção de parar a proteção, mantendo os pontos de recuperação existentes.

![Parar proteção](./media/backup-azure-database-postgresql/stop-protection.png)

### <a name="change-policy"></a>Alterar política

Pode alterar a política associada com uma instância de backup.

1. Selecione a Política de Alteração **de Instância de Cópia de**  ->  **Segurança**.

    ![Alterar política](./media/backup-azure-database-postgresql/change-policy.png)

1. Selecione a nova política que pretende aplicar na base de dados.

    ![Política de reatribuição](./media/backup-azure-database-postgresql/reassign-policy.png)

## <a name="troubleshooting"></a>Resolução de problemas

Esta secção fornece informações de resolução de problemas para o backup das bases de dados Azure PostgreSQL com Azure Backup.

### <a name="usererrormsimissingpermissions"></a>UserErrorMSIMissingPermissions

Dê ao Cofre de Cópias de Segurança O acesso **AO MSI Leia** o acesso ao servidor PG que pretende fazer backup ou restaurar:

Para estabelecer uma ligação segura à base de dados PostgreSQL, a Azure Backup utiliza o modelo de autenticação [identidade de serviço gerido (MSI).](../active-directory/managed-identities-azure-resources/overview.md) Isto significa que o cofre de reserva terá acesso apenas aos recursos que foram explicitamente autorizados pelo utilizador.

Um SISTEMA MSI é automaticamente atribuído ao cofre no momento da criação. Tem de dar a este COFRE O acesso aos servidores PostgreSQL que pretende fazer o back bases de dados.

Passos:

1. No servidor Postgres, aceda ao painel **de controlo de acesso (IAM).**

    ![Painel de Controlo de Acesso](./media/backup-azure-database-postgresql/access-control-pane.png)

1. **Selecione Adicionar uma atribuição de função**.

    ![Adicionar atribuição de função](./media/backup-azure-database-postgresql/add-role-assignment.png)

1. No painel de contexto certo que abre, insira o seguinte:<br>

    **Função:** Leitor<br>
    **Atribuir acesso a:** Escolha **o cofre de reserva**<br>
    Se não conseguir encontrar a opção **de cofre de reserva** na lista de drop-down, escolha a opção principal do **utilizador, grupo ou serviço Azure**<br>

    ![Selecionar função](./media/backup-azure-database-postgresql/select-role.png)

    **Selecione:** Introduza o nome do cofre de reserva para o qual pretende fazer cópias de segurança deste servidor e das suas bases de dados.<br>

    ![Insira o nome do cofre de backup](./media/backup-azure-database-postgresql/enter-backup-vault-name.png)

### <a name="usererrorbackupuserauthfailed"></a>UserErrorBackupUserAuthFailed

Crie um utilizador de backup de base de dados que possa autenticar com o Azure Ative Directory:

Este erro pode vir de uma ausência de um administrador do Azure Ative Directory para o servidor PostgreSQL, ou na ausência de um utilizador de backup que possa autenticar usando o Azure Ative Directory.

Passos:

Adicione um Administrador De Diretório Ativo ao servidor OSS:

Este passo é necessário para ligar à base de dados através de um utilizador que possa autenticar com o Azure Ative Directory em vez de uma palavra-passe. O utilizador Azure AD Admin na Base de Dados Azure para PostgreSQL terá o papel **azure_ad_admin**. Apenas uma **função azure_ad_admin** pode criar novos utilizadores de bases de dados que possam autenticar com Azure AD.

1. Vá ao separador Ative Directory Admin no painel de navegação esquerdo da vista do servidor e adicione-se (ou a outra pessoa) como administrador do Ative Directory.

    ![Definir administrador de diretório ativo](./media/backup-azure-database-postgresql/set-admin.png)

1. Certifique-se de **que guarde** a definição do utilizador de administração AD.

    ![Salvar a definição de utilizador de administração de diretório ativo](./media/backup-azure-database-postgresql/save-admin-setting.png)

Consulte [este documento](https://download.microsoft.com/download/7/4/d/74d689aa-909d-4d3e-9b18-f8e465a7ebf5/OSSbkpprep_automated.docx) para obter a lista de passos que necessita de ser efetua para completar as etapas de concessão de permissão.

### <a name="usererrormissingnetworksecuritypermissions"></a>UserErrorMissingNetworkSecurityPermissions

Estabeleça uma linha de visão de rede, permitindo o acesso à bandeira **dos serviços Azure** na vista do servidor. Na vista do servidor, no painel **de segurança De Ligação,** desace ainda a bandeira **de serviços do Azure** para **Sim**.

![Permitir acesso aos serviços do Azure](./media/backup-azure-database-postgresql/allow-access-to-azure-services.png)

### <a name="usererrorcontainernotaccessible"></a>UserErrorContainerNotAccessible

#### <a name="permission-to-restore-to-a-storage-account-container-when-restoring-as-files"></a>Permissão para restaurar um recipiente de conta de armazenamento ao restaurar como ficheiros

1. Dê ao cofre de reserva MSI a permissão para aceder aos recipientes da conta de armazenamento usando o portal Azure.
    1. Vá para **o controlo de** acesso à conta de armazenamento Adicionar a atribuição de  ->    ->  **função**.
    1. Atribua o papel **de contribuinte de dados blob** de armazenamento para o MSI do cofre de reserva.

    ![Atribuir papel de contribuinte de dados blob de armazenamento](./media/backup-azure-database-postgresql/assign-storage-blog-data-contributor-role.png)

1. Em alternativa, dê permissões granulares ao recipiente específico a que está a restaurar, utilizando a atribuição de funções Azure CLI [az criar](/cli/azure/role/assignment) comando.

    ```azurecli
    az role assignment create --assignee $VaultMSI_AppId  --role "Storage Blob Data Contributor"   --scope $id
    ```

    1. Substitua o parâmetro do destinatário pelo ID de **aplicação** do MSI do cofre e pelo parâmetro de âmbito para consultar o seu recipiente específico.
    1. Para obter o **ID** de aplicação do MSI do cofre, selecione **Todas as aplicações** sob **o tipo de Aplicação**:

        ![Selecione todas as aplicações](./media/backup-azure-database-postgresql/select-all-applications.png)

    1. Procure o nome do cofre e copie o ID da aplicação:

        ![Pesquisa rumo ao nome do cofre](./media/backup-azure-database-postgresql/search-for-vault-name.png)

## <a name="next-steps"></a>Passos seguintes

- [Visão geral dos cofres de reserva](backup-vault-overview.md)
