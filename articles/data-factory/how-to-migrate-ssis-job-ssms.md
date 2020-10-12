---
title: Migrar no local serviços de integração de servidores SQL (SSIS) para a Azure Data Factory
description: Este artigo descreve como migrar os empregos dos SqL Server Integration Services (SSIS) para os oleodutos/atividades/gatilhos da Azure Data Factory utilizando o SQL Server Management Studio.
services: data-factory
documentationcenter: ''
author: chugugrace
ms.author: chugu
ms.reviewer: ''
manager: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 4/7/2020
ms.openlocfilehash: 6b95162d34b706b0bbb3e2940ea214e5a662655d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90984916"
---
# <a name="migrate-sql-server-agent-jobs-to-adf-with-ssms"></a>Migrar trabalhos de agente de servidor SQL para ADF com SSMS

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Ao [migrar no local, os Serviços de Integração de Servidores SQL (SSIS) para SSIS em ADF,](scenario-ssis-migration-overview.md)após a migração dos pacotes SSIS, pode fazer a migração de lotes de trabalhos de agentes de servidor sql com o tipo de passo de trabalho do Pacote de Serviços de Integração de Servidores SQL para Azure Data Factory (ADF) pipelines/atividades/gatilhos de agenda através do SqL Server Management Studio (SSMS) **SSIS Job Migration Wizard**.

Em geral, para trabalhos selecionados de agentes SQL com tipos de passos de trabalho aplicáveis, **o Assistente de Migração de Emprego SSIS** pode:

- mapeia no local o local do pacote SSIS para onde as embalagens são migradas, que são acessíveis pela SSIS em ADF.
    > [!NOTE]
    > A localização do pacote do Sistema de Ficheiros é suportada apenas.
- migrar postos de trabalho aplicáveis com as etapas de trabalho aplicáveis aos recursos correspondentes da ADF, como a seguir:

|Objeto de trabalho do agente SQL  |Recurso ADF  |Notas|
|---------|---------|---------|
|Trabalho de Agente SQL|oleoduto     |O nome do oleoduto será *gerado para \<job name> *. <br> <br> Não são aplicáveis trabalhos de agente incorporado: <li> Trabalho de manutenção do servidor SSIS <li> syspolicy_purge_history <li> collection_set_* <li> mdw_purge_data_* <li> sysutility_*|
|Passo de trabalho da SSIS|Executar atividade de pacote SSIS|<li> O nome da atividade \<step name> será. <li> A conta proxy utilizada no passo de trabalho será migrada à medida que o Windows autenticar esta atividade. <li> *As opções de execução* exceto *o tempo de execução de 32 bits* definido no passo de trabalho serão ignorados na migração. <li> *A verificação* definida na etapa de emprego será ignorada na migração.|
|agenda      |acionador da agenda        |O nome do gatilho do horário será *gerado para \<schedule name> *. <br> <br> Abaixo as opções no horário de trabalho do Agente SQL serão ignoradas na migração: <li> Intervalo de segundo nível. <li> *Comece automaticamente quando o Agente do Servidor SQL começar* <li> *Comece sempre que os CPUs ficarem inativos* <li> *dia da semana* e *fim de semana dia*<time zone> <br> Abaixo estão as diferenças após o horário de trabalho do Agente SQL ser migrado para o gatilho do calendário da ADF: <li> A execução subsequente do ADF Schedule Trigger é independente do estado de execução do antecedente desencadeado. <li> A configuração de recorrência do gatilho de agenda ADF difere da frequência diária no trabalho de agente SQL.|

- gerar modelos de Gestor de Recursos Azure (ARM) na pasta de saída local e implementar na fábrica de dados direta ou posteriormente manualmente. Para obter mais informações sobre os modelos do Gestor de Recursos ADF, consulte [os tipos de recursos do Microsoft.DataFactory](https://docs.microsoft.com/azure/templates/microsoft.datafactory/allversions).

## <a name="prerequisites"></a>Pré-requisitos

A funcionalidade descrita neste artigo requer a versão 18.5 ou superior do SQL Server Management Studio. Para obter a versão mais recente do SSMS, consulte [o Download SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-ver15).

## <a name="migrate-ssis-jobs-to-adf"></a>Migrar empregos SSIS para a ADF

1. No SSMS, no Object Explorer, selecione SQL Server Agent, selecione Jobs, em seguida, clique à direita e **selecione Migração SSIS Jobs para ADF**.
![O Screenshot mostra o SQL Server Management Studio Object Explorer, onde pode selecionar Jobs, em seguida, Migrar S S I S Jobs para A D F.](media/how-to-migrate-ssis-job-ssms/menu.png)

1. Assine no Azure, selecione Azure Subscription, Data Factory e Integration Runtime. O Azure Storage é opcional, que é usado na etapa de mapeamento da localização do pacote se os trabalhos SSIS a migrar tiverem pacotes SSIS File System.
![menu](media/how-to-migrate-ssis-job-ssms/step1.png)

1. Mapear os caminhos dos pacotes SSIS e dos ficheiros de configuração em empregos SSIS para caminhos de destino onde os gasodutos migrados podem aceder. Neste passo de mapeamento, pode:

    1. Selecione uma pasta de origem e, em seguida, **adicione mapeamento**.
    1. Atualize o caminho da pasta de origem. Caminhos válidos são caminhos de pasta ou caminhos de pasta dos pacotes.
    1. Atualizar o caminho da pasta de destino. O predefinimento é o caminho relativo para a conta de Armazenamento predefinido, que é selecionada no passo 1.
    1. Elimine um mapeamento selecionado através **do Delete Mapping**.
![A screenshot mostra a página Map S S I S Package e Configuration Paths, onde pode adicionar mapeamento. ](media/how-to-migrate-ssis-job-ssms/step2.png)
 ![ O screenshot mostra a página Map S S I S Package and Configuration Paths, onde pode atualizar os caminhos das pastas de origem e destino.](media/how-to-migrate-ssis-job-ssms/step2-1.png)

1. Selecione os postos de trabalho aplicáveis para migrar e configufique as definições da *correspondente atividade do Pacote SSIS Executado*.

    - *Definição predefinida*, aplica-se a todas as etapas selecionadas por predefinição. Para obter mais informações sobre cada propriedade, consulte o *separador Definições* para a [atividade do Pacote SSIS Executar](how-to-invoke-ssis-package-ssis-activity.md) quando a localização do pacote for o Sistema de *Ficheiros (Pacote)*.
    ![O Screenshot mostra a página Select S I S Jobs, onde pode configurar as definições da correspondente atividade do Pacote SSIS Executado.](media/how-to-migrate-ssis-job-ssms/step3-1.png)
    - *Definição de passo*, configurar a definição para um passo selecionado.
        
        **Aplicar Definição predefinida:** o predefinição é selecionado. Desfasete a definição de configuração apenas para passo selecionado.  
        Para obter mais informações sobre outras propriedades, consulte o *separador Definições* para a [atividade do Pacote SSIS Executar](how-to-invoke-ssis-package-ssis-activity.md) quando a localização do pacote for o Sistema de *Ficheiros (Pacote)*.
    ![A screenshot mostra a página Select S I S Jobs, onde pode aplicar as definições predefinidas.](media/how-to-migrate-ssis-job-ssms/step3-2.png)

1. Gere e implemente o modelo ARM.
    1. Selecione ou introduza o caminho de saída para os modelos ARM dos oleodutos ADF migrados. A pasta será criada automaticamente se não existir.
    2. Selecione a opção de **implementar modelos ARM para a sua fábrica de dados**:
        - O padrão não é selecionado. Pode implementar modelos ARM gerados mais tarde manualmente.
        - Selecione para implementar os modelos ARM gerados diretamente na fábrica de dados.
    ![A screenshot mostra a página de migração configurar, onde pode selecionar ou inserir o caminho de saída para os modelos ARM dos oleodutos ADF migrados e selecionar a opção de implementar modelos ARM para a sua fábrica de dados.](media/how-to-migrate-ssis-job-ssms/step4.png)

1. Migrar e depois verificar os resultados.
![A screenshot mostra a página Resultado da Migração, que mostra o progresso da migração.](media/how-to-migrate-ssis-job-ssms/step5.png)

## <a name="next-steps"></a>Passos seguintes

[Executar e monitorizar o gasoduto](how-to-invoke-ssis-package-ssis-activity.md)
