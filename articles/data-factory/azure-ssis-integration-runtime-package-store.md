---
title: Gerir pacotes com loja de pacotes de runtime de integração Azure-SSIS
description: Saiba como gerir pacotes com a loja de pacotes de runtime de integração Azure-SSIS.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: mflasko
ms.custom: seo-lt-2019
ms.date: 05/25/2020
ms.openlocfilehash: fa1ab60ae5690bd3782e4c849ca7cb5c29d640c2
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84199057"
---
# <a name="manage-packages-with-azure-ssis-integration-runtime-package-store"></a>Gerir pacotes com loja de pacotes de runtime de integração Azure-SSIS

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Para levantar & transferir as cargas de trabalho dos Serviços de Integração de Servidores SQL (SSIS) no local para a nuvem, pode providenciar o tempo de execução de integração Azure-SSIS (IR) na Azure Data Factory (ADF). Para obter mais informações, consulte [Provision a Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure). Um Azure-SSIS IR suporta:

- Pacotes de execução implantados no catálogo SSIS (SSISDB) hospedados pelo servidor de base de dados Azure SQL/Gestão de Instâncias (Modelo de Implementação de Projetos)
- Executando pacotes implantados no sistema de ficheiros, ficheiros Azure ou base de dados do SQL Server (MSDB) hospedada por Azure SQL Managed Instance (Modelo de Implementação de Pacotes)

Quando utilizar o Modelo de Implementação de Pacotes, pode escolher se pretende fornecer o seu Azure-SSIS IR com lojas de pacotes que fornecem uma camada de gestão de pacotes em cima do sistema de ficheiros/Ficheiros Azure/MSDB hospedados pela Azure SQL Managed Instance. A loja de pacotes Azure-SSIS IR permite importar/eliminar/eliminar/executar pacotes e monitorizar/parar de funcionar pacotes através do SQL Server Management Studio (SSMS) semelhante à [antiga loja de pacotes SSIS](https://docs.microsoft.com/sql/integration-services/service/package-management-ssis-service?view=sql-server-2017). 

## <a name="connect-to-azure-ssis-ir"></a>Ligue-se ao Azure-SSIS IR

Uma vez que o seu Azure-SSIS IR é a provisionado, pode ligar-se a ele para navegar nas suas lojas de pacotes em SSMS.

![Ligue-se ao Azure-SSIS IR](media/azure-ssis-integration-runtime-package-store/ssms-package-store-connect.png)

Na janela **Object Explorer** do SSMS, selecione O tempo **de execução da integração Azure-SSIS** no menu de entrega do **Connect.** Em seguida, inscreva-se no Azure e selecione a subscrição relevante, ADF, e Azure-SSIS IR, à qual as suas lojas de pacotes estão anexadas. O seu Azure-SSIS IR aparecerá com **pacotes de execução** e **nós de pacotes armazenados** por baixo. Expanda o nó **pacotes armazenados** para ver as suas lojas de pacotes por baixo. Expanda as suas lojas de pacotes para ver pastas e pacotes por baixo. Pode ser-lhe pedido que introduza as credenciais de acesso para as suas lojas de pacotes, se o SSMS não conseguir ligar-se automaticamente a elas. Por exemplo, se expandir uma loja de pacotes em cima do MSDB, pode ser-lhe pedido que se conecte primeiro à sua Instância Gerida Azure SQL.

![Ligue-se a Azure SQL Gestão De Instância](media/azure-ssis-integration-runtime-package-store/ssms-package-store-connect2.png)

## <a name="manage-folders-and-packages"></a>Gerir pastas e pacotes

Ao navegar no seu Azure-SSIS IR em SSMS, pode clicar com o botão direito em quaisquer lojas/pastas/pacotes para aparecer um menu e selecionar **Nova Pasta,** **Pacote de Importação,** **Pacote de Exportação,** **Eliminar**ou **Refrescar**.

   ![Gerir pastas e pacotes](media/azure-ssis-integration-runtime-package-store/ssms-package-store-manage.png)

   *  Selecione **Nova Pasta** para criar uma nova pasta para pacotes importados.

   *  Selecione **Pacote de Importação** para importar pacotes do Sistema de **Ficheiros,** **SQL Server** (MSDB) ou a antiga Loja de **Pacotes SSIS** na sua loja de pacotes.

      ![Pacote de Importação](media/azure-ssis-integration-runtime-package-store/ssms-package-store-import.png)

      Dependendo da **localização** do Pacote a importar, selecione o tipo de autenticação **do servidor** / **Authentication type**relevante, introduza as credenciais de acesso se necessário, selecione a **trajetória pacote**, e introduza o novo nome **pacote**. Quando importam pacotes, o seu nível de proteção não pode ser alterado. Para alterá-lo, utilize ferramentas de dados do servidor SQL (SSDT) ou `dtutil` serviço de linha de comando.

   *  Selecione **pacote de exportação** para exportar pacotes da sua loja de pacotes para **o Sistema de Ficheiros,** **SqL Server** (MSDB) ou a antiga Loja de **Pacotes SSIS**.

      ![Pacote de Exportação](media/azure-ssis-integration-runtime-package-store/ssms-package-store-export.png)

      Dependendo da **localização** do Pacote para exportar, selecione o tipo de autenticação **do servidor** / **Authentication type**relevante, introduza as credenciais de acesso se necessário e selecione o caminho **do Pacote**. Ao exportar pacotes, se estiverem encriptados, introduza as palavras-passe para desencriptar primeiro e depois pode alterar o seu nível de proteção, por exemplo, para não guardar dados sensíveis ou para encriptar dados confidenciais com a chave/palavra-passe do utilizador.

   *  **Selecione Eliminar** para eliminar as pastas/pacotes existentes na sua loja de pacotes.

   *  Selecione **Refresh** para mostrar as pastas/pacotes recém-adicionados na sua loja de pacotes.

## <a name="execute-packages"></a>Executar pacotes

Ao navegar no seu Azure-SSIS IR em SSMS, pode clicar com o botão direito em quaisquer pacotes armazenados para aparecer um menu e selecionar **o Run Package**.  Isto abrirá o diálogo **de utilitário de pacote execute,** onde pode configurar as suas execuções de pacotes em Azure-SSIS IR como executar atividades do Pacote SSIS em oleodutos ADF.

![Executar pacotes de utilidade 1 & 2](media/azure-ssis-integration-runtime-package-store/ssms-package-store-execute.png)

![Executar pacotes de utilidade 3 & 4](media/azure-ssis-integration-runtime-package-store/ssms-package-store-execute2.png)

As páginas **Gerais**, **Configurações**, Opções de **Execução**e **Registo** de **Serviços de Utilidade de Pacote executem** correspondem ao separador **Definições** da atividade do Pacote SSIS Executante, onde pode introduzir a palavra-passe de encriptação do seu pacote, aceder a informações para o seu ficheiro de configuração do pacote, credenciais/propriedades de execução de pacotes e aceder a informações de acesso para a sua pasta de registo.  A página **'Valores definidos'** do diálogo **de utilidade de pacote execute** corresponde ao **separador Deposição** de Propriedade da atividade do Pacote Executo SSIS, onde pode introduzir as propriedades do pacote existentes para substituir. Para obter mais informações, consulte [os pacotes Run SSIS como executar as atividades do Pacote SSIS em oleodutos ADF.](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity)

Quando selecionar o botão **Executar** o diálogo **de utilidade de pacotes,** um novo oleoduto ADF com atividade do Pacote SSIS executar será automaticamente gerado e acionado. Se já existir um gasoduto ADF com as mesmas definições de execução de pacotes, será reexecutado e não será gerado um novo gasoduto. O oleoduto ADF e a atividade do Pacote SSIS executarão `Pipeline_SSMS_YourPackageName_HashString` `Activity_SSMS_YourPackageName` e, respectivamente.

![Executar botão de utilidade de pacote](media/azure-ssis-integration-runtime-package-store/ssms-package-store-execute3.png)

![Executar atividade de pacote SSIS](media/azure-ssis-integration-runtime-package-store/ssis-activity-package-store.png)

## <a name="monitor-and-stop-running-packages"></a>Monitorize e pare de executar pacotes

Ao navegar no seu Azure-SSIS IR em SSMS, pode expandir o nó **'Pacotes Running'** para ver as suas embalagens atualmente em execução por baixo.  Clique com o botão direito em qualquer um deles para aparecer um menu e selecione **Stop** or **Refresh**.

   ![Monitorize e pare de executar pacotes](media/azure-ssis-integration-runtime-package-store/ssms-package-store-monitor.png)

   *  Selecione **Stop** para cancelar o pipeline ADF atualmente em execução que executa o pacote como executar a atividade do Pacote SSIS.

   *  Selecione **Refresh** para mostrar os pacotes recém-funcionais nas suas lojas de pacotes.

## <a name="monitor-azure-ssis-ir-and-edit-package-stores"></a>Monitor Azure-SSIS IR e editar lojas de pacotes

Ao navegar no seu Azure-SSIS IR em SSMS, pode clicar com o botão direito para aparecer um menu e selecionar **Ir ao portal Azure Data Factory** ou **refrescar- se**.

   ![Ir para o portal ADF](media/azure-ssis-integration-runtime-package-store/ssms-package-store-monitor2.png)

   *  Selecione **Ir ao portal Azure Data Factory** para abrir a página de tempos de **integração** do centro de monitorização ADF, onde pode monitorizar o seu Azure-SSIS IR. No azulejo **pacote lojas,** pode ver o número de lojas de pacotes que estão anexadas ao seu Azure-SSIS IR.  A seleção desse número irá surgir uma janela onde pode editar serviços ligados à ADF que armazenam as informações de acesso para as suas lojas de pacotes.

      ![Editar lojas de pacotes](media/azure-ssis-integration-runtime-package-store/ssms-package-store-monitor3.png)

   *  Selecione **Refresh** para mostrar as pastas/pacotes recém-adicionados nas suas lojas de pacotes e executar pacotes nas suas lojas de pacotes.

## <a name="next-steps"></a>Próximos passos

Pode reedição/edição dos oleodutos ADF gerados automaticamente com atividades do Pacote SSIS executado ou criar novos no portal ADF. Para obter mais informações, consulte [os pacotes Run SSIS como executar as atividades do Pacote SSIS em oleodutos ADF.](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity)