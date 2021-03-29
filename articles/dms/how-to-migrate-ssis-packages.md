---
title: Recolocar pacotes SSIS para base de dados única SQL
titleSuffix: Azure Database Migration Service
description: Saiba como migrar ou redistribuir pacotes e projetos de Serviços de Integração de Servidores SQL para Azure SQL Database uni única base de dados utilizando o Serviço de Migração de Bases de Dados Azure e o Assistente de Migração de Dados.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: how-to
ms.date: 02/20/2020
ms.openlocfilehash: e42b5ab2bbdebb2584b38531bd6458a98563ef42
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2021
ms.locfileid: "105643750"
---
# <a name="redeploy-ssis-packages-to-azure-sql-database-with-azure-database-migration-service"></a>Recolocar pacotes SSIS para Azure SQL Database com Serviço de Migração de Bases de Dados Azure

Se utilizar os Serviços de Integração de Servidores SQL (SSIS) e quiser migrar os seus projetos/pacotes SSIS da fonte SSISDB hospedado pelo SQL Server para o destino SSISDB hospedado pela Base de Dados Azure SQL, pode reimplantá-los utilizando o Assistente de Implementação de Serviços de Integração. Pode lançar o assistente a partir do SQL Server Management Studio (SSMS).

Se a versão do SSIS que utiliza for anterior a 2012, antes de recolocar os seus projetos/pacotes SSIS no modelo de implementação do projeto, primeiro tem de os converter utilizando o Assistente de Conversão de Projetos de Integração, que também pode ser lançado a partir de SSMS. Para mais informações, consulte o artigo [Converter projetos para o modelo de implementação do projeto.](/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages#convert)

> [!NOTE]
> O Serviço de Migração de Bases de Dados (DMS) atualmente não suporta a migração de uma fonte SSISDB para Azure SQL Database, mas pode recolocar os seus projetos/pacotes SSIS utilizando o seguinte processo.

Neste artigo, vai aprender a:
> [!div class="checklist"]
>
> * Avaliar os projetos/pacotes SSIS de origem.
> * Migrar projetos/pacotes SSIS para Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para completar estes passos, você precisa:

* SSMS versão 17.2 ou posterior.
* Uma instância do seu servidor de base de dados alvo para hospedar o SSISDB. Se ainda não tiver um, crie um [servidor SQL lógico](../azure-sql/database/logical-servers.md) (sem base de dados) utilizando o portal Azure navegando para o [formulário](https://ms.portal.azure.com/#create/Microsoft.SQLServer)SQL Server (apenas servidor lógico).
* O SSIS deve ser abasteçado na Azure Data Factory (ADF) que contenha o tempo de execução da integração Azure-SSIS (IR) com o destino SSISDB hospedado pela SQL Database (conforme descrito no artigo [Provision o Tempo de Execução da Integração Azure-SSIS na Fábrica de Dados Azure).](../data-factory/tutorial-deploy-ssis-packages-azure.md)

## <a name="assess-source-ssis-projectspackages"></a>Avaliar projetos/pacotes SSIS de origem

Embora a avaliação da fonte SSISDB ainda não esteja integrada no Assistente de Migração da Base de Dados (DMA) ou no Serviço de Migração de Bases de Dados (DMS), os seus projetos/pacotes SSIS serão avaliados/validados à medida que forem redistribuídos para o destino SSISDB hospedado pela Base de Dados Azure SQL.

## <a name="migrate-ssis-projectspackages"></a>Migrar projetos/pacotes SSIS

Para migrar projetos/pacotes SSIS para Azure SQL Database, execute os seguintes passos.

1. Abra o SSMS e, em seguida, selecione **Opções** para exibir a caixa de diálogo **'Ligar ao Servidor'.**

2. No **separador Iniciar sessão,** especifique as informações necessárias para ligar ao servidor que irá acolher o destino SSISDB.

    ![Separador de login da SSIS](media/how-to-migrate-ssis-packages/dms-ssis-login-tab.png)

3. No separador **Propriedades de Ligação,** na caixa de texto **de 'Ligar à base de dados',** selecione ou introduza **SSISDB** e, em seguida, selecione **Connect**.

    ![Separador SSIS Connection Properties](media/how-to-migrate-ssis-packages/dms-ssis-conncetion-properties-tab.png)

4. No SSMS Object Explorer, expanda o nó **de Catálogos de Serviços de Integração,** expanda **o SSISDB,** e se não houver pastas existentes, clique no **SSISDB** e crie uma nova pasta.

5. No **âmbito do SSISDB,** expanda qualquer pasta, clique em **Projetos** com clique à direita e, em seguida, selecione **Implementar Projeto**.

    ![Nó SSIS SSISDB expandido](media/how-to-migrate-ssis-packages/dms-ssis-ssisdb-node-expanded.png)

6. No Assistente de Implementação de Serviços de Integração, na página **Introdução,** reveja as informações e, em seguida, selecione **Seguinte**.

    ![Página de introdução do assistente de implantação](media/how-to-migrate-ssis-packages/dms-deployment-wizard-introduction-page.png)

7. Na página **'Selecionar Origem',** especifique o projeto SSIS existente que pretende implementar.

    Se o SSMS também estiver ligado ao SQL Server que hospeda a fonte SSISDB, selecione o **catálogo de Serviços de Integração** e, em seguida, insira o nome do servidor e o caminho do projeto no seu catálogo para implementar o seu projeto diretamente.

    Em alternativa, selecione **o ficheiro de implementação do Projeto** e, em seguida, especifique o caminho para um ficheiro de implementação de projetos existente (.ispac) para implementar o seu projeto.

    ![Página de Origem selecione do assistente de implementação](media/how-to-migrate-ssis-packages/dms-deployment-wizard-select-source-page.png)
 
8. Selecione **Seguinte**.
9. Na página **Destino Selecionado,** especifique o destino do seu projeto.

    a. Na caixa de texto do nome do Servidor, insira o nome do servidor totalmente qualificado (<server_name>.database.windows.net).

    b. Forneça as informações de autenticação e, em seguida, selecione **Connect**.

    ![Página de destino selecionado por assistente de implementação](media/how-to-migrate-ssis-packages/dms-deployment-wizard-select-destination-page.png)

    c. **Selecione Procurar** para especificar a pasta de destino em SSISDB e, em seguida, selecione **Seguinte**.

    > [!NOTE]
    > O botão **Seguinte** só está ativado depois de ter selecionado **o Connect**.

10. Na página **'Validar',** ver quaisquer erros/avisos e, se necessário, modificar as suas embalagens em conformidade.

    ![Página de validação de assistente de implementação](media/how-to-migrate-ssis-packages/dms-deployment-wizard-validate-page.png)

11. Selecione **Seguinte**.

12. Na página **'Rever',** reveja as definições de implementação.

    > [!NOTE]
    > Pode alterar as suas definições selecionando **Anterior** ou selecionando qualquer uma das ligações de passo no painel esquerdo.

13. Selecione **Implementar** para iniciar o processo de implantação.

14. Após o processo de implementação estar concluído, pode ver a página Resultados, que apresenta o sucesso ou falha de cada ação de implementação.
    a. Se alguma ação falhar, na coluna **Resultado,** **selecione Não** apresente uma explicação do erro.
    b. Opcionalmente, **selecione Save Report** para guardar os resultados num ficheiro XML.

15. Selecione **Perto** de sair do Assistente de Implementação de Serviços de Integração.

Se a implementação do seu projeto for bem sucedida sem falhas, pode selecionar quaisquer pacotes que contenha para executar no seu Azure-SSIS IR.

## <a name="next-steps"></a>Passos seguintes

* Reveja a orientação de migração no [Guia de Migração da Base de Dados da](https://datamigration.microsoft.com/)Microsoft.