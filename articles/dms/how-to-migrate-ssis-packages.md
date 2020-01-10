---
title: Reimplantar pacotes SSIS em um banco de dados individual SQL
titleSuffix: Azure Database Migration Service
description: Saiba como migrar ou reimplantar SQL Server Integration Services pacotes e projetos no banco de dados SQL do Azure usando o serviço de migração de banco de dados do Azure e Assistente de Migração de Dados.
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: article
ms.date: 06/08/2019
ms.openlocfilehash: b1889410a6c6925ebba5632a08c34bc967ced627
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75437974"
---
# <a name="redeploy-ssis-packages-to-azure-sql-database-with-azure-database-migration-service"></a>Reimplantar pacotes SSIS no banco de dados SQL do Azure com o serviço de migração de banco de dados Azure

Se você usar SQL Server Integration Services (SSIS) e quiser migrar seus projetos/pacotes do SSIS do SSISDB de origem hospedado por SQL Server para o SSISDB de destino armazenado pelo banco de dados SQL do Azure, você poderá reimplantá-los usando o assistente de implantação do Integration Services. Você pode iniciar o assistente de dentro do SQL Server Management Studio (SSMS).

Se a versão do SSIS que você usa for anterior à 2012, antes de reimplantar seus projetos/pacotes do SSIS no modelo de implantação de projeto, primeiro você precisará convertê-los usando o assistente de conversão de projeto Integration Services, que também pode ser iniciado no SSMS. Para obter mais informações, consulte o artigo [convertendo projetos para o modelo de implantação de projeto](https://docs.microsoft.com/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages?view=sql-server-2017#convert).

> [!NOTE]
> Atualmente, o serviço de migração de banco de dados do Azure (DMS) não oferece suporte à migração de um SSISDB de origem para um servidor de banco de dados SQL do Azure, mas você pode reimplantar seus projetos/pacotes SSIS usando o processo a seguir.

Neste artigo, vai aprender a:
> [!div class="checklist"]
>
> * Avaliar projetos/pacotes do SSIS de origem.
> * Migrar projetos/pacotes do SSIS para o Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir essas etapas, você precisará de:

* SSMS versão 17,2 ou posterior.
* Uma instância do servidor de banco de dados de destino para hospedar o SSISDB. Se você ainda não tiver um, crie um servidor de banco de dados SQL do Azure (sem um banco de dados) usando o portal do Azure navegando até o [formulário](https://ms.portal.azure.com/#create/Microsoft.SQLServer)SQL Server (somente servidor lógico).
* O SSIS deve ser provisionado em Azure Data Factory (ADF) contendo Azure-SSIS Integration Runtime (IR) com o arquivo de destino SSISDB hospedado pela instância do servidor de banco de dados SQL do Azure (conforme descrito no artigo [provisionar o Azure-SSIS Integration Runtime no Azure data Factory](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure)).

## <a name="assess-source-ssis-projectspackages"></a>Avaliar projetos/pacotes do SSIS de origem

Embora a avaliação da origem SSISDB ainda não esteja integrada ao DMA (Assistente de Migração de banco de dados) ou ao DMS (serviço de migração de banco de dados do Azure), seus projetos/pacotes SSIS serão avaliados/validados à medida que forem reimplantados no SSISDB de destino armazenado em um servidor de banco de dados SQL do Azure.

## <a name="migrate-ssis-projectspackages"></a>Migrar projetos/pacotes do SSIS

Para migrar projetos/pacotes do SSIS para o servidor de banco de dados SQL do Azure, execute as etapas a seguir.

1. Abra o SSMS e selecione **Opções** para exibir a caixa de diálogo **conectar ao servidor** .

2. Na guia **logon** , especifique as informações necessárias para se conectar ao servidor do banco de dados SQL do Azure que hospedará o SSISDB de destino.

    ![Guia logon do SSIS](media/how-to-migrate-ssis-packages/dms-ssis-login-tab.png)

3. Na guia **Propriedades da conexão** , na caixa de texto **conectar ao banco de dados** , selecione ou insira **SSISDB**e, em seguida, selecione **conectar**.

    ![Guia Propriedades da conexão SSIS](media/how-to-migrate-ssis-packages/dms-ssis-conncetion-properties-tab.png)

4. No Pesquisador de objetos do SSMS, expanda o nó **catálogos Integration Services** , expanda **ssisdb**e, se não houver nenhuma pasta existente, clique com o botão direito do mouse em **ssisdb** e crie uma nova pasta.

5. Em **SSISDB**, expanda qualquer pasta, clique com o botão direito do mouse em **projetos**e selecione **implantar projeto**.

    ![Nó do SSIS SSISDB expandido](media/how-to-migrate-ssis-packages/dms-ssis-ssisdb-node-expanded.png)

6. No Integration Services assistente de implantação, na página **introdução** , examine as informações e selecione **Avançar**.

    ![Página de introdução do assistente para implantação](media/how-to-migrate-ssis-packages/dms-deployment-wizard-introduction-page.png)

7. Na página **selecionar origem** , especifique o projeto do SSIS existente que você deseja implantar.

    Se o SSMS também estiver conectado ao SQL Server que hospeda o SSISDB de origem, selecione **Integration Services catálogo**e, em seguida, insira o nome do servidor e o caminho do projeto em seu catálogo para implantar o projeto diretamente.

    Como alternativa, selecione **arquivo de implantação de projeto**e, em seguida, especifique o caminho para um arquivo de implantação de projeto existente (. ispac) para implantar o projeto.

    ![Página Selecionar origem do assistente de implantação](media/how-to-migrate-ssis-packages/dms-deployment-wizard-select-source-page.png)
 
8. Selecione **Seguinte**.
9. Na página **Selecionar destino** , especifique o destino do seu projeto.

    a. Na caixa de texto nome do servidor, insira o nome do servidor do banco de dados SQL do Azure totalmente qualificado (< server_name >. Database. Windows. net).

    b. Forneça as informações de autenticação e, em seguida, selecione **conectar**.

    ![Página Selecionar destino do assistente de implantação](media/how-to-migrate-ssis-packages/dms-deployment-wizard-select-destination-page.png)

    c. Selecione **procurar** para especificar a pasta de destino em SSISDB e, em seguida, selecione **Avançar**.

    > [!NOTE]
    > O botão **Avançar** só será habilitado depois que você tiver selecionado **conectar**.

10. Na página **validar** , exiba os erros/avisos e, se necessário, modifique seus pacotes de acordo.

    ![Página validar do assistente de implantação](media/how-to-migrate-ssis-packages/dms-deployment-wizard-validate-page.png)

11. Selecione **Seguinte**.

12. Na página **revisão** , examine as configurações de implantação.

    > [!NOTE]
    > Você pode alterar suas configurações selecionando **anterior** ou selecionando qualquer um dos links de etapa no painel esquerdo.

13. Selecione **implantar** para iniciar o processo de implantação.

14. Depois que o processo de implantação for concluído, você poderá exibir a página resultados, que exibe o êxito ou a falha de cada ação de implantação.
    a. Se qualquer ação tiver falhado, na coluna **resultado** , selecione **falha** ao exibir uma explicação do erro.
    b. Opcionalmente, selecione **Salvar relatório** para salvar os resultados em um arquivo XML.

15. Selecione **fechar** para sair do assistente de implantação de Integration Services.

Se a implantação do seu projeto for realizada com êxito sem falha, você poderá selecionar qualquer pacote que ele contiver para ser executado em seu Azure-SSIS IR.

## <a name="next-steps"></a>Passos seguintes

* Examine as diretrizes de migração no [Guia de migração de banco de dados](https://datamigration.microsoft.com/)da Microsoft.
