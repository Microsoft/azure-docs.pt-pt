---
title: Reutilizar pacotes SSIS para base de dados única SQL
titleSuffix: Azure Database Migration Service
description: Saiba como migrar ou reimplantar pacotes e projetos de Serviços de Integração de Servidores SQL para a base de dados única Azure SQL utilizando o Serviço de Migração de Bases de Dados Azure e o Assistente de Migração de Dados.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: article
ms.date: 02/20/2020
ms.openlocfilehash: 90a39b8fe3604a05f1d35a875ae4e34491b47d72
ms.sourcegitcommit: 96dc60c7eb4f210cacc78de88c9527f302f141a9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/27/2020
ms.locfileid: "77648534"
---
# <a name="redeploy-ssis-packages-to-azure-sql-database-with-azure-database-migration-service"></a>Reutilizar pacotes SSIS para a Base de Dados Azure SQL com o Serviço de Migração de Bases de Dados Azure

Se utilizar os Serviços de Integração de Servidores SQL (SSIS) e pretender migrar os seus projetos/pacotes SSIS da fonte SSISDB hospedada pelo SQL Server para o destino SSISDB hospedado pela Base de Dados Azure SQL, pode reimplantá-los utilizando o Assistente de Implementação de Serviços de Integração. Pode lançar o assistente dentro do Estúdio de Gestão de Servidores SQL (SSMS).

Se a versão do SSIS que utiliza for mais cedo do que em 2012, antes de recolocar os seus projetos/pacotes SSIS no modelo de implementação do projeto, primeiro precisa convertê-los utilizando o Assistente de Conversão de Projetos de Integração, que também pode ser lançado a partir de SSMS. Para mais informações, consulte o artigo Conversão de projetos para o modelo de [implantação do projeto.](https://docs.microsoft.com/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages?view=sql-server-2017#convert)

> [!NOTE]
> O Serviço de Migração de Bases de Dados Azure (DMS) não suporta atualmente a migração de uma fonte SSISDB para um servidor de base de dados Azure SQL, mas pode reimplantar os seus projetos/pacotes SSIS utilizando o seguinte processo.

Neste artigo, vai aprender a:
> [!div class="checklist"]
>
> * Avaliar os projetos/pacotes SSIS de origem.
> * Migrar projetos/pacotes SSIS para o Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para completar estes passos, precisa de:

* Versão SSMS 17.2 ou posterior.
* Uma instância do seu servidor de base de dados alvo para hospedar o SSISDB. Se ainda não tiver um, crie um servidor de base de dados Azure SQL (sem base de dados) utilizando o portal Azure navegando para o [formulário](https://ms.portal.azure.com/#create/Microsoft.SQLServer)SQL Server (apenas servidor lógico).
* O SSIS deve ser aprovisionado na Azure Data Factory (ADF) contendo o Tempo de Inatividade Azure-SSIS (IR) com o destino SSISDB hospedado pela instância do servidor de base de dados Azure SQL (conforme descrito no artigo [Provision the Azure-SSIS Integration Runtime in Azure Data Factory).](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure)

## <a name="assess-source-ssis-projectspackages"></a>Avaliar os projetos/pacotes ssis de origem

Embora a avaliação da fonte SSISDB ainda não esteja integrada no Serviço de Migração de Bases de Dados (DMA) ou no Serviço de Migração de Bases de Dados Azure (DMS), os seus projetos/pacotes SSIS serão avaliados/validados à medida que forem redistribuídos para o destino SSISDB hospedado num servidor de base de dados Azure SQL.

## <a name="migrate-ssis-projectspackages"></a>Migrar projetos/pacotes SSIS

Para migrar projetos/pacotes SSIS para o servidor de base de dados Azure SQL, execute os seguintes passos.

1. Abra o SSMS e, em seguida, selecione **Opções** para exibir a caixa de diálogo **Connect to Server.**

2. No separador **Login,** especifique as informações necessárias para ligar ao servidor de base de dados Azure SQL que irá acolher o Destino SSISDB.

    ![Separador de login SSIS](media/how-to-migrate-ssis-packages/dms-ssis-login-tab.png)

3. No separador Propriedades de **Ligação,** na caixa de texto **Connect to database,** selecione ou introduza **SSISDB,** e, em seguida, selecione **Connect**.

    ![Separador De propriedades de ligação SSIS](media/how-to-migrate-ssis-packages/dms-ssis-conncetion-properties-tab.png)

4. No SSMS Object Explorer, expanda o nó de Catálogos de Catálogos de Serviços de **Integração,** expanda **o SSISDB,** e se não houver pastas existentes, clique no **SSISDB** e crie uma nova pasta.

5. Em **SSISDB,** expanda qualquer pasta, clique à direita **Em Projetos,** e, em seguida, selecione **Deploy Project**.

    ![Nó SSIS SSISDB expandido](media/how-to-migrate-ssis-packages/dms-ssis-ssisdb-node-expanded.png)

6. No Assistente de Implementação de Serviços de Integração, na página **introdução,** reveja as informações e, em seguida, selecione **Next**.

    ![Página de introdução do assistente de implementação](media/how-to-migrate-ssis-packages/dms-deployment-wizard-introduction-page.png)

7. Na página **Select Source,** especifique o projeto SSIS existente que pretende implementar.

    Se o SSMS também estiver ligado ao Servidor SQL que acolhe a fonte SSISDB, selecione o catálogo de Serviços de **Integração**e, em seguida, introduza o nome do servidor e o caminho do projeto no seu catálogo para implementar o seu projeto diretamente.

    Alternadamente, selecione o ficheiro de **implementação do Projeto**, e, em seguida, especifique o caminho para um ficheiro de implementação de projetos existente (.ispac) para implementar o seu projeto.

    ![Página de origem de seleção de assistente seletiva](media/how-to-migrate-ssis-packages/dms-deployment-wizard-select-source-page.png)
 
8. Selecione **Seguinte**.
9. Na página **Select Destination,** especifique o destino para o seu projeto.

    a. Na caixa de texto de nome Server, introduza o nome do servidor de base de dados Azure SQL (<server_name>.database.windows.net).

    b. Forneça as informações de autenticação e, em seguida, selecione **Connect**.

    ![Página de destino do assistente de implementação](media/how-to-migrate-ssis-packages/dms-deployment-wizard-select-destination-page.png)

    c. **Selecione Navegar** para especificar a pasta de destino no SSISDB e, em seguida, selecione **Next**.

    > [!NOTE]
    > O botão **Seguinte** só é ativado depois de ter selecionado **o Connect**.

10. Na página **Validar,** veja quaisquer erros/advertências e, se necessário, modifique os seus pacotes em conformidade.

    ![Página de validação do assistente de implementação](media/how-to-migrate-ssis-packages/dms-deployment-wizard-validate-page.png)

11. Selecione **Seguinte**.

12. Na página **'Rever',** reveja as definições de implementação.

    > [!NOTE]
    > Pode alterar as definições selecionando **Anteriorou** ou selecionando qualquer uma das ligações de passo no painel esquerdo.

13. Selecione **Implementar** para iniciar o processo de implantação.

14. Após a conclusão do processo de implementação, pode visualizar a página Resultados, que mostra o sucesso ou falha de cada ação de implementação.
    a. Se alguma ação falhar, na coluna **Resultado,** selecione **Failpara** apresentar uma explicação do erro.
    b. Opcionalmente, selecione **'Guardar Relatório'** para guardar os resultados num ficheiro XML.

15. Selecione **Perto** para sair do Assistente de Implantação de Serviços de Integração.

Se a implementação do seu projeto for bem sucedida sem falhas, pode selecionar quaisquer pacotes que contenha para executar no seu IR Azure-SSIS.

## <a name="next-steps"></a>Passos seguintes

* Reveja a orientação de migração no Guia de [Migração](https://datamigration.microsoft.com/)da Microsoft Database .
