---
title: Voltar a implementar pacotes do SQL Server Integration Services para o SQL Database do Azure | Documentos da Microsoft
description: Saiba como migrar pacotes do SQL Server Integration Services para a base de dados do Azure SQL.
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 06/08/2019
ms.openlocfilehash: 603a9df8e3f499c832bbfdcbef966de86003d6b7
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67080638"
---
# <a name="redeploy-sql-server-integration-services-packages-to-azure-sql-database"></a>Voltar a implementar pacotes do SQL Server Integration Services para a base de dados do Azure SQL

Se utilizar o SQL Server Integration Services (SSIS) e pretender migrar dos projetos/pacotes do SSIS da origem de SSISDB alojado pelo SQL Server para o destino SSISDB alojado pela base de dados do Azure SQL, pode implementá-los novamente usando a implantação de serviços de integração Assistente. Pode iniciar o Assistente de dentro do SQL Server Management Studio (SSMS).

Se a versão do SSIS que utiliza é anterior ao 2012, antes de voltar a implementar o SSIS projetos/pacotes para o modelo de implementação do projeto, terá primeiro de convertê-los com o Integration Services Project Conversion Wizard, que também pode ser inicializado a partir do SSMS. Para obter mais informações, consulte o artigo [conversão de projetos para o modelo de implementação do projeto](https://docs.microsoft.com/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages?view=sql-server-2017#convert).

> [!NOTE]
> O Azure Database Migration Service (DMS) atualmente não suporta a migração de uma origem de SSISDB a um servidor de base de dados do Azure SQL, mas pode voltar a implementar seus projetos/pacotes SSIS com o processo seguinte.

Neste artigo, vai aprender a:
> [!div class="checklist"]
>
> * Avalie a origem projetos/pacotes do SSIS.
> * Migre projetos/pacotes do SSIS para o Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir estes passos, terá de:

* SSMS versão 17.2 ou posterior.
* Uma instância do seu servidor de base de dados de destino para alojar o SSISDB. Se ainda não tiver uma, crie um servidor de base de dados do Azure SQL (sem uma base de dados) no portal do Azure ao navegar para o SQL Server (apenas servidor lógico) [formulário](https://ms.portal.azure.com/#create/Microsoft.SQLServer).
* SSIS tem de ser aprovisionado no Azure Data Factory (ADF) que contém o Azure-SSIS Integration Runtime (IR) com o SSISDB alojado pela instância do servidor de base de dados do Azure SQL de destino (conforme descrito no artigo [aprovisionar a integração de SSIS do Azure Tempo de execução no Azure Data Factory](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure)).

## <a name="assess-source-ssis-projectspackages"></a>Avaliar a origem projetos/pacotes do SSIS

Embora a avaliação da origem SSISDB ainda não estiver integrada para a base de dados do Assistente de migração (DMA) ou para o Azure Database Migration Service (DMS), dos projetos/pacotes do SSIS serão avaliados/validadas como eles são implantados novamente para o destino SSISDB alojado num Servidor de base de dados SQL do Azure.

## <a name="migrate-ssis-projectspackages"></a>Migrar projetos/pacotes do SSIS

Para migrar projetos/pacotes do SSIS para o servidor de base de dados do Azure SQL, execute os seguintes passos.

1. Abra o SSMS e, em seguida, selecione **opções** para apresentar o **ligar ao servidor** caixa de diálogo.

2. Sobre o **início de sessão** separador, especifique as informações necessárias para ligar ao servidor de base de dados do Azure SQL que irá alojar o SSISDB de destino.

    ![Guia de início de sessão do SSIS](media/how-to-migrate-ssis-packages/dms-ssis-login-tab.png)

3. Sobre o **propriedades de ligação** separador a **ligar à base de dados** caixa de texto, selecione ou introduza **SSISDB**e, em seguida, selecione **Connect**.

    ![Separador de propriedades de ligação do SSIS](media/how-to-migrate-ssis-packages/dms-ssis-conncetion-properties-tab.png)

4. No Object Explorer do SSMS, expanda o **catálogos de serviços de integração** nó, expanda **SSISDB**e se não houver nenhuma pasta existente, em seguida, clique com botão direito **SSISDB** e Crie uma nova pasta.

5. Sob **SSISDB**, expanda qualquer pasta, clique com botão direito **projetos**e, em seguida, selecione **implementar projeto**.

    ![Nó expandido do SSIS SSISDB](media/how-to-migrate-ssis-packages/dms-ssis-ssisdb-node-expanded.png)

6. No Assistente de implementação de serviços de integração, sobre o **introdução** página, reveja as informações e, em seguida, selecione **próxima**.

    ![Página de introdução do Assistente de implementação](media/how-to-migrate-ssis-packages/dms-deployment-wizard-introduction-page.png)

7. Sobre o **selecionar origem** , especifique o projeto do SSIS existente que pretende implementar.

    Se SSMS também está ligado ao SQL Server que aloja a origem SSISDB, selecione **catálogo de serviços de integração**e, em seguida, introduza o caminho de nome e o projeto de servidor no seu catálogo para implementar o projeto diretamente.

    Em alternativa, selecione **ficheiro de projeto de implementação**e, em seguida, especifique o caminho para um ficheiro de implementação projeto de existente (.ispac) para implementar o projeto.

    ![Página de assistente selecionar origem de implementação](media/how-to-migrate-ssis-packages/dms-deployment-wizard-select-source-page.png)
 
8. Selecione **Seguinte**.
9. Sobre o **selecionar destino** , especifique o destino para o seu projeto.

    a. Na caixa de texto do nome do servidor, introduza o nome completamente qualificado do servidor de base de dados do Azure SQL (< server_name >. database.windows.net).

    b. Forneça as informações de autenticação e, em seguida, selecione **Connect**.

    ![Página de assistente selecionar destino da implementação](media/how-to-migrate-ssis-packages/dms-deployment-wizard-select-destination-page.png)

    c. Selecione **navegue** para especificar a pasta de destino no SSISDB e, em seguida, selecione **próxima**.

    > [!NOTE]
    > O **próxima** botão está habilitado apenas depois de selecionar **Connect**.

10. Sobre o **Validate** página, exibir os erros/avisos e, em seguida, se necessário, modifique seus pacotes em conformidade.

    ![Página de Assistente Validar implementação](media/how-to-migrate-ssis-packages/dms-deployment-wizard-validate-page.png)

11. Selecione **Seguinte**.

12. Sobre o **rever** , reveja as definições de implementação.

    > [!NOTE]
    > Pode alterar as definições, selecionando **Previous** ou ao selecionar qualquer uma das ligações passo no painel esquerdo.

13. Selecione **Deploy** para iniciar o processo de implantação.

14. Depois de concluído o processo de implementação, pode ver a página de resultados, que apresenta o sucesso ou fracasso de cada ação de implementação.
    a. Se qualquer ação falhou, além da **resultado** coluna, selecione **falha** para apresentar uma explicação sobre o erro.
    b. Opcionalmente, selecione **Guardar relatório** para guardar os resultados num arquivo XML.

15. Selecione **fechar** para sair do Assistente de implementação de serviços de integração.

Se a implementação do seu projeto com êxito sem falhas, pode selecionar todos os pacotes que ele contém para executar no seu ir Azure-SSIS.

## <a name="next-steps"></a>Passos Seguintes

* Reveja as orientações de migração no Microsoft [guia de migração de bases de dados](https://datamigration.microsoft.com/).
