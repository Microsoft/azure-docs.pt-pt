---
title: Configurar o tempo de integração do Azure-SSIS para a continuidade do negócio e recuperação de desastres (BCDR)
description: Este artigo descreve como configurar o tempo de integração do Azure-SSIS na Azure Data Factory com a Azure SQL Database/Managed Instance failover group for business continuity and disaster recovery (BCDR).
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.devlang: powershell
author: swinarko
ms.author: sawinark
manager: mflasko
ms.reviewer: douglasl
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 02/25/2021
ms.openlocfilehash: 73c27204ee8730c95d1cbeecf8777767173e73d9
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101710269"
---
# <a name="configure-azure-ssis-integration-runtime-for-business-continuity-and-disaster-recovery-bcdr"></a>Configurar o tempo de integração do Azure-SSIS para a continuidade do negócio e recuperação de desastres (BCDR) 

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-xxx-md.md)]

Azure SQL Database/Managed Instance and SQL Server Integration Services (SSIS) in Azure Data Factory (ADF) pode ser combinado como a solução recomendada para a all-Platform como um serviço (PaaS) para a migração do SQL Server. Pode implementar os seus projetos SSIS na base de dados de catálogos SSIS (SSISDB) hospedada pela Azure SQL Database/Managed Instance e executar os seus pacotes SSIS no tempo de integração do Azure SSIS (IR) em ADF.

Para a continuidade do negócio e recuperação de desastres (BCDR), a Base de Dados/Instância Gerida do Azure SQL pode ser configurada com um [grupo de geo-replicação/failover,](https://docs.microsoft.com/azure/azure-sql/database/auto-failover-group-overview)onde o SSISDB numa região primária de Azure com acesso à leitura-escrita (papel primário) será continuamente replicado numa região secundária com acesso apenas à leitura (papel secundário). Quando ocorre uma catástrofe na região primária, será desencadeada uma falha, onde os SSISDBs primários e secundários trocam de funções.

Para o BCDR, também pode configurar um duplo standby Azure SSIS IR pair que funciona em sincronização com o grupo de failover Azure SQL Database/Managed Instance. Isto permite-lhe ter um par de IRs Azure-SSIS em execução que, a qualquer momento, apenas um pode aceder ao SSISDB primário para obter e executar pacotes, bem como escrever registos de execução de pacotes (papel primário), enquanto o outro só pode fazer o mesmo para pacotes implantados em outro lugar, por exemplo em Ficheiros Azure (papel secundário). Quando o SSISDB falhar, os IRs Azure-SISis primários e secundários também trocarão de papéis e, se ambos estiverem em execução, haverá um tempo de inatividade quase nulo.

Este artigo descreve como configurar o Azure-SSIS IR com a Azure SQL Database/Managed Instance failover group for BCDR.

## <a name="configure-a-dual-standby-azure-ssis-ir-pair-with-azure-sql-database-failover-group"></a>Configure um duplo standby Azure-SSIS IR pair com Azure SQL Database failover group

Para configurar um duplo standby Azure-SSIS IR par que funciona em sincronização com o grupo de failover da Base de Dados Azure SQL, complete os seguintes passos.

1. Utilizando o portal Azure/ADF UI, pode criar um novo Azure-SSIS IR com o seu servidor primário de base de dados Azure SQL para hospedar o SSISDB na região primária. Se tiver um Azure-SSIS IR existente que já está ligado ao SSIDB hospedado pelo seu servidor primário Azure SQL Database e ainda está em funcionamento, tem de o parar primeiro para o reconfigurar. Este será o seu Azure-SSIS IR primário. Ao selecionar para utilizar o [SSISDB](./tutorial-deploy-ssis-packages-azure.md#creating-ssisdb) na página de configurações de **implementação** do painel de configuração do tempo de execução da **integração,** selecione também o par de tempo de **execução de integração Azure-SSIS de dupla posição com caixa de verificação de failover SSISDB.** Para dupla dupla de pares de **espera,** insira um nome para identificar o seu par de IRs Azure-SSIS primários e secundários. Quando concluir a criação do seu Azure-SSIS IR primário, será iniciado e anexado a um SSISDB primário que será criado em seu nome com acesso de leitura-escrita. Se acabou de reconfigurá-lo, precisa reiniciá-lo.

1. Utilizando o portal Azure, pode verificar se o SSISDB primário foi criado na página **geral** do seu servidor principal de base de dados Azure SQL. Uma vez criado, pode [criar um grupo de failover para os seus servidores de base de dados Azure SQL primário e secundário e adicionar-lhe SSISDB](https://docs.microsoft.com/azure/azure-sql/database/failover-group-add-single-database-tutorial?tabs=azure-portal#2---create-the-failover-group) na página de **grupos Failover.** Uma vez criado o seu grupo de failover, pode verificar se o SSISDB primário foi replicado para um secundário com acesso apenas de leitura na página **geral** do seu servidor de base de dados Azure SQL secundário.

1. Utilizando o portal Azure/ADF UI, pode criar outro Azure-SSIS IR com o seu servidor de base de dados Azure SQL secundário para hospedar o SSISDB na região secundária. Este será o seu Azure-SSIS IR secundário. Para o BCDR completo, certifique-se de que todos os recursos de que depende também são criados na região secundária, por exemplo, Azure Storage para armazenar scripts/ficheiros de configuração personalizados, ADF para execuções de pacotes de orquestração/agendamento, etc. Ao selecionar para utilizar o [SSISDB](./tutorial-deploy-ssis-packages-azure.md#creating-ssisdb) na página de configurações de **implementação** do painel de configuração do tempo de execução da **integração,** selecione também o par de tempo de **execução de integração Azure-SSIS de dupla posição com caixa de verificação de failover SSISDB.** Para dupla dupla de pares de **espera,** insira o mesmo nome para identificar o seu par de IRs Azure-SSIS primários e secundários. Quando concluir a criação do seu Azure-SSIS IR secundário, será iniciado e ligado ao SSISDB secundário.

1. Se pretender ter um tempo de inatividade quase nula quando ocorrer a falha do SSISDB, mantenha ambos os seus IRs Azure-SSIS em funcionamento. Apenas o seu Azure-SSIS IR primário pode aceder ao SSISDB primário para obter e executar pacotes, bem como escrever registos de execução de pacotes, enquanto o seu Azure-SSIS IR secundário só pode fazer o mesmo para pacotes implantados em outro lugar, por exemplo em Ficheiros Azure. Se quiser minimizar o seu custo de funcionamento, pode parar o seu Azure-SSIS IR secundário depois de ter sido criado. Quando ocorrer o failover SSISDB, os seus IRs Azure-SISis primários e secundários trocam funções. Se o seu Azure-SSIS IR primário for parado, tem de reiniciá-lo. Dependendo se é injetado numa rede virtual e no método de injeção utilizado, levará dentro de 5 minutos ou cerca de 20 a 30 minutos para que possa funcionar.

1. Se [utilizar a ADF para execuções de pacotes de orquestração/agendamento,](./how-to-invoke-ssis-package-ssis-activity.md)certifique-se de que todos os oleodutos ADF relevantes com atividades do Pacote SSIS executados e gatilhos associados são copiados para o seu ADF secundário com os gatilhos inicialmente desativados. Quando ocorre uma falha no SSISDB, é necessário ative-los.

1. Pode [testar o seu grupo de failover da Base de Dados Azure SQL](https://docs.microsoft.com/azure/azure-sql/database/failover-group-add-single-database-tutorial?tabs=azure-portal#3---test-failover) e verificar na [página de monitorização do IR Azure-SSIS no portal ADF](./monitor-integration-runtime.md#monitor-the-azure-ssis-integration-runtime-in-azure-portal) se os seus IRs Azure-SISis primários e secundários trocaram funções. 

## <a name="configure-a-dual-standby-azure-ssis-ir-pair-with-azure-sql-managed-instance-failover-group"></a>Configure um duplo standby Azure-SSIS IR pair com Azure SQL Managed Instance failover group

Para configurar um duplo standby Azure-SSIS IR par que funciona em sincronização com o grupo de failover de instância gerida Azure SQL, complete os seguintes passos.

1. Utilizando o portal Azure, pode [criar um grupo de failover para as suas instâncias geridas primárias e secundárias do Azure SQL](https://docs.microsoft.com/azure/azure-sql/managed-instance/failover-group-add-instance-tutorial?tabs=azure-portal) na página de **grupos failover** da sua primeira Instância Gerida Azure SQL.

1. Utilizando o portal Azure/ADF UI, pode criar um novo Azure-SSIS IR com o seu Azure SQL Managed Instance primário para hospedar o SSISDB na região primária. Se tiver um Azure-SSIS IR existente que já está ligado ao SSIDB hospedado pelo seu Azure SQL Managed Instance e ainda está em execução, tem de o parar primeiro para o reconfigurar. Este será o seu Azure-SSIS IR primário. Ao selecionar para utilizar o [SSISDB](./create-azure-ssis-integration-runtime.md#creating-ssisdb) na página de configurações de **implementação** do painel de configuração do tempo de execução da **integração,** selecione também o par de tempo de **execução de integração Azure-SSIS de dupla posição com caixa de verificação de failover SSISDB.** Para dupla dupla de pares de **espera,** insira um nome para identificar o seu par de IRs Azure-SSIS primários e secundários. Quando concluir a criação do seu Azure-SSIS IR primário, será iniciado e anexado a um SSISDB primário que será criado em seu nome com acesso de leitura-escrita. Se acabou de reconfigurá-lo, precisa reiniciá-lo. Também pode verificar se o SSISDB primário foi replicado para um secundário com acesso apenas de leitura na página **geral** da sua instância gerida secundária Azure SQL.

1. Utilizando o portal Azure/ADF UI, pode criar outro Azure-SSIS IR com o seu Azure SQL Managed Instance para hospedar O SSISDB na região secundária. Este será o seu Azure-SSIS IR secundário. Para o BCDR completo, certifique-se de que todos os recursos de que depende também são criados na região secundária, por exemplo, Azure Storage para armazenar scripts/ficheiros de configuração personalizados, ADF para execuções de pacotes de orquestração/agendamento, etc. Ao selecionar para utilizar o [SSISDB](./create-azure-ssis-integration-runtime.md#creating-ssisdb) na página de configurações de **implementação** do painel de configuração do tempo de execução da **integração,** selecione também o par de tempo de **execução de integração Azure-SSIS de dupla posição com caixa de verificação de failover SSISDB.** Para dupla dupla de pares de **espera,** insira o mesmo nome para identificar o seu par de IRs Azure-SSIS primários e secundários. Quando concluir a criação do seu Azure-SSIS IR secundário, será iniciado e ligado ao SSISDB secundário.

1. A Azure SQL Managed Instance pode proteger dados sensíveis em bases de dados, como o SSISDB, encriptando-os utilizando a Chave Master da Base de Dados (DMK). O próprio DMK é por sua vez encriptado utilizando a Chave Master de Serviço (SMK) por padrão. No momento da escrita, o grupo de failover da Azure SQL Managed Instance não replica o SMK do principal Azure SQL Managed Instance, pelo que o DMK e, por sua vez, o SSISDB não pode ser desencriptado na instância gerida secundária Azure SQL após ocorrer falha. Para contornar isto, pode adicionar uma encriptação de palavra-passe para o DMK ser desencriptado na instância gerida secundária Azure SQL. Utilizando SSMS, complete os seguintes passos.

   1. Execute o seguinte comando para SSISDB na sua primeira Instância Gerida Azure SQL para adicionar uma palavra-passe para encriptar o DMK.

      ```sql
      ALTER MASTER KEY ADD ENCRYPTION BY PASSWORD = 'YourPassword'
      ```
   
   1. Executar o seguinte comando para SSISDB em instâncias geridas primárias e secundárias do Azure SQL para adicionar a nova palavra-passe para desencriptar o DMK.

      ```sql
      EXEC sp_control_dbmasterkey_password @db_name = N'SSISDB', @password = N'YourPassword', @action = N'add'
      ```

1. Se pretender ter um tempo de inatividade quase nula quando ocorrer a falha do SSISDB, mantenha ambos os seus IRs Azure-SSIS em funcionamento. Apenas o seu Azure-SSIS IR primário pode aceder ao SSISDB primário para obter e executar pacotes, bem como escrever registos de execução de pacotes, enquanto o seu Azure-SSIS IR secundário só pode fazer o mesmo para pacotes implantados em outro lugar, por exemplo em Ficheiros Azure. Se quiser minimizar o seu custo de funcionamento, pode parar o seu Azure-SSIS IR secundário depois de ter sido criado. Quando ocorrer o failover SSISDB, os seus IRs Azure-SISis primários e secundários trocam funções. Se o seu Azure-SSIS IR primário for parado, tem de reiniciá-lo. Dependendo se é injetado numa rede virtual e no método de injeção utilizado, levará dentro de 5 minutos ou cerca de 20 a 30 minutos para que possa funcionar.

1. Se [utilizar o Agente de Instância Gerida Azure SQL para execuções de pacotes de orquestração/agendamento](./how-to-invoke-ssis-package-managed-instance-agent.md), certifique-se de que todos os trabalhos SSIS relevantes com as suas etapas de trabalho e horários associados são copiados para o seu Azure SQL Managed Instance com os horários inicialmente desativados. Utilizando SSMS, complete os seguintes passos.

   1. Para cada trabalho SSIS, clique à direita e selecione o **Script Job como**, CREATE **To**, e novos itens de menu de entrega de janela de editor de **consulta** para gerar o seu script.

      ![Gerar script de trabalho SSIS](media/configure-bcdr-azure-ssis-integration-runtime/generate-ssis-job-script.png)

   1. Para cada script de trabalho SSIS gerado, encontre o comando para executar o `sp_add_job` procedimento armazenado e modifique/remova a atribuição de valor para `@owner_login_name` o argumento, se necessário.

   1. Para cada script de trabalho SSIS atualizado, execute-o no seu Azure SQL Managed Instance secundário para copiar o trabalho com os seus passos de trabalho e horários associados.

   1. Utilizando o seguinte script, crie um novo trabalho T-SQL para ativar/desativar horários de trabalho SSIS baseados na função SSISDB primária/secundária, respectivamente, tanto nas suas instâncias geridas primárias como secundárias do Azure SQL e executá-lo regularmente. Quando ocorrer o failover do SSISDB, os horários de trabalho do SSIS que foram desativados serão ativados e vice-versa.

      ```sql
      IF (SELECT Top 1 role_desc FROM SSISDB.sys.dm_geo_replication_link_status WHERE partner_database = 'SSISDB') = 'PRIMARY'
         BEGIN
            IF (SELECT enabled FROM msdb.dbo.sysschedules WHERE schedule_id = <ScheduleID>) = 0
               EXEC msdb.dbo.sp_update_schedule @schedule_id = <ScheduleID >, @enabled = 1
         END
      ELSE
         BEGIN
            IF (SELECT enabled FROM msdb.dbo.sysschedules WHERE schedule_id = <ScheduleID>) = 1
               EXEC msdb.dbo.sp_update_schedule @schedule_id = <ScheduleID >, @enabled = 0
         END
      ```

1. Se [utilizar a ADF para execuções de pacotes de orquestração/agendamento,](./how-to-invoke-ssis-package-ssis-activity.md)certifique-se de que todos os oleodutos ADF relevantes com atividades do Pacote SSIS executados e gatilhos associados são copiados para o seu ADF secundário com os gatilhos inicialmente desativados. Quando ocorre uma falha no SSISDB, é necessário ative-los.

1. Pode [testar o seu grupo de failover de instâncias geridas Azure SQL](https://docs.microsoft.com/azure/azure-sql/managed-instance/failover-group-add-instance-tutorial?tabs=azure-portal#test-failover) e verificar na [página de monitorização do IR Azure-SSIS no portal ADF](./monitor-integration-runtime.md#monitor-the-azure-ssis-integration-runtime-in-azure-portal) se os seus IRs Azure-SISis primários e secundários trocaram funções. 

## <a name="attach-a-new-azure-ssis-ir-to-existing-ssisdb-hosted-by-azure-sql-databasemanaged-instance"></a>Anexar um novo Azure-SSIS IR ao SSISDB existente hospedado pela Azure SQL Database/Managed Instance

Se ocorrer uma catástrofe e tiver impacto no seu Azure-SSIS IR existente, mas não a Azure SQL Database/Managed Instance na mesma região, pode substituí-la por uma nova noutra região. Para anexar o seu SSISDB existente hospedado pela Azure SQL Database/Managed Instance a um novo Azure-SSIS IR, complete os seguintes passos.

1. Se o seu Azure-SSIS IR existente ainda estiver em funcionamento, tem de o parar primeiro utilizando o portal Azure/ADF UI ou o Azure PowerShell. Se o desastre também tiver impacto na ADF na mesma região, pode saltar este passo.

1. Utilizando o SSMS, execute o seguinte comando para SSISDB na sua Base de Dados Azure SQL/Instância Gerida para atualizar os metadados que permitirão ligações a partir do seu novo ADF/Azure-SSIS IR.

   ```sql
   EXEC [catalog].[failover_integration_runtime] @data_factory_name = 'YourNewADF', @integration_runtime_name = 'YourNewAzureSSISIR'
   ```

1. Utilizando [o portal Azure/ADF UI](./create-azure-ssis-integration-runtime.md#use-the-azure-portal-to-create-an-integration-runtime) ou [Azure PowerShell,](./create-azure-ssis-integration-runtime.md#use-azure-powershell-to-create-an-integration-runtime)crie o seu novo ADF/Azure-SSIS IR nomeado *YourNewADF* / *YourNewAzureSSISIR,* respectivamente, noutra região. Se utilizar o portal Azure/UI ADF, pode ignorar o erro de ligação de teste na página de configurações de **implementação** do painel de configuração de tempo de **integração.**

## <a name="next-steps"></a>Passos seguintes

Pode considerar estas outras opções de configuração para o seu Azure-SSIS IR:

- [Configure as lojas de pacotes para o seu IR Azure-SSIS](./create-azure-ssis-integration-runtime.md#creating-azure-ssis-ir-package-stores)

- [Configurar configurações personalizadas para o seu Azure-SSIS IR](./how-to-configure-azure-ssis-ir-custom-setup.md)

- [Configure a injeção de rede virtual para o seu Azure-SSIS IR](./join-azure-ssis-integration-runtime-virtual-network.md)

- [Configure o IR auto-hospedado como representante do seu IR Azure-SSIS](./self-hosted-integration-runtime-proxy-ssis.md)
