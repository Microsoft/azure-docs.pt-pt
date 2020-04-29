---
title: Migrar no local os serviços de integração do servidor SQL (SSIS) para a Azure Data Factory
description: Este artigo descreve como migrar os empregos da SQL Server Integration Services (SSIS) para os oleodutos/atividades/gatilhos da Fábrica de Dados da Azure, utilizando o SQL Server Management Studio.
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
ms.openlocfilehash: 6e357e98d6c5190c6dfef675dc1ab9cf30a717c1
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81455092"
---
# <a name="migrate-sql-server-agent-jobs-to-adf-with-ssms"></a>Migrar trabalhos de agente de servidor SQL para ADF com SSMS

Ao migrar no local a carga de [trabalho sQL Server Integration Services (SSIS) para o SSIS em ADF,](scenario-ssis-migration-overview.md)após a migração dos pacotes SSIS, pode fazer a migração de lotes de trabalhos do Agente de Servidores SQL com o tipo de serviço sQL Server Integration Services Package para azure Data Factory (ADF) pipelines/atividades/agenda triggers via SQL Server Management Studio (SSMS) **SSIS Job Migration Wizard**.

Em geral, para trabalhos de agente SQL selecionados com tipos de etapas de trabalho aplicáveis, o Assistente de **Migração de Emprego SSIS** pode:

- mapear no local localização do pacote SSIS para onde os pacotes são migrados, que são acessíveis pelo SSIS em ADF.
    > [!NOTE]
    > A localização do pacote do Sistema de Ficheiros é suportada apenas.
- migrar empregos aplicáveis com medidas de trabalho aplicáveis aos recursos Correspondentes da ADF, na medida em que se seguir:

|Objeto de trabalho do agente SQL  |Recurso ADF  |Notas|
|---------|---------|---------|
|Trabalho de Agente SQL|oleoduto     |O nome do gasoduto será *gerado para \<o nome *de trabalho>. <br> <br> Não são aplicáveis os trabalhos de agente incorporados: <li> Trabalho de manutenção do servidor SSIS <li> syspolicy_purge_history <li> collection_set_* <li> mdw_purge_data_* <li> sysutility_*|
|Passo de trabalho do SSIS|Executar atividade de pacote SSIS|<li> O nome da \<atividade será o nome> passo. <li> A conta proxy utilizada na etapa de trabalho será migrada à medida que o Windows autenticar esta atividade. <li> *As opções* de execução exceto o tempo de execução de *32 bits* definido no passo do trabalho serão ignoradas na migração. <li> *A verificação* definida no escalão de trabalho será ignorada na migração.|
|agenda      |acionador da agenda        |O nome do gatilho será *gerado para \<o nome *de horário>. <br> <br> As opções abaixo no horário de trabalho do Agente SQL serão ignoradas na migração: <li> Intervalo de segundo nível. <li> *Comece automaticamente quando o Agente do Servidor SQL começar* <li> *Comece sempre que as CPUs ficarem inativas* <li> *dia da semana* e *dia de fim de semana*<time zone> <br> Abaixo estão as diferenças após o horário de trabalho do Agente SQL ser migrado para o gatilho de horário da ADF: <li> A distente adtente subsequente é independente do estado de execução do prazo desencadeado. <li> A configuração de recorrência do gatilho da ADF Schedule difere da frequência diária no trabalho do agente SQL.|

- gerar modelos de Gestor de Recursos Azure (ARM) na pasta de saída local e implementar para a fábrica de dados direta ou posteriormente manualmente. Para obter mais informações sobre os modelos do Gestor de Recursos ADF, consulte os tipos de [recursos microsoft.DataFactory](https://docs.microsoft.com/azure/templates/microsoft.datafactory/allversions).

## <a name="prerequisites"></a>Pré-requisitos

A funcionalidade descrita neste artigo requer a versão 18.5 ou superior do Estúdio de Gestão do Servidor SQL. Para obter a versão mais recente do SSMS, consulte [Download SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-ver15).

## <a name="migrate-ssis-jobs-to-adf"></a>Migrar empregos ssis para a ADF

1. No SSMS, no Object Explorer, selecione SQL Server Agent, selecione Jobs, em seguida, clique à direita e **selecione Migrate SSIS Jobs para ADF**.
![menu](media/how-to-migrate-ssis-job-ssms/menu.png)

1. Assine in Azure, selecione Assinatura Azure, Data Factory e Tempo de Integração. O Armazenamento Azure é opcional, que é usado na etapa de mapeamento da localização do pacote se os empregos do SSIS a migrar tiverem pacotes do Sistema de Ficheiros SSIS.
![menu](media/how-to-migrate-ssis-job-ssms/step1.png)

1. Mapeie os caminhos dos pacotes SSIS e dos ficheiros de configuração em postos de trabalho ssis para caminhos de destino onde os gasodutos migrados podem aceder. Neste passo de mapeamento, pode:

    1. Selecione uma pasta de origem e, em seguida, **adicionar Mapeamento**.
    1. Atualize o caminho da pasta de origem. Os caminhos válidos são caminhos de pastas ou caminhos de pastas dos pais dos pacotes.
    1. Atualizar o caminho da pasta de destino. O predefinido é um caminho relativo para a conta de armazenamento predefinida, que é selecionada no passo 1.
    1. Elimine um mapeamento selecionado via **Eliminar mapeamento**.
![passo2](media/how-to-migrate-ssis-job-ssms/step2.png)
![passo2-1](media/how-to-migrate-ssis-job-ssms/step2-1.png)

1. Selecione trabalhos aplicáveis para migrar e configure as definições da atividade do *Pacote SSIS executado*correspondente .

    - *Definição predefinida,* aplica-se a todas as etapas selecionadas por defeito. Para obter mais informações sobre cada propriedade, consulte o *separador Definições* para a atividade do [Pacote Executar SSIS](how-to-invoke-ssis-package-ssis-activity.md) quando a localização do pacote for sistema de *ficheiros (Pacote)*.
    ![passo3-1](media/how-to-migrate-ssis-job-ssms/step3-1.png)
    - *Definição de passos,* configurar a definição para um passo selecionado.
        
        **Aplicar Definição predefinido**: predefinido é selecionado. Não selecione para configurar a definição apenas para o passo selecionado.  
        Para obter mais informações sobre outras propriedades, consulte o *separador Definições* para a atividade do [Pacote Executar SSIS](how-to-invoke-ssis-package-ssis-activity.md) quando a localização do pacote for sistema de *ficheiros (Pacote)*.
    ![passo3-2](media/how-to-migrate-ssis-job-ssms/step3-2.png)

1. Gere e desdobre o modelo ARM.
    1. Selecione ou insera o caminho de saída para os modelos ARM dos gasodutos ADF migrados. A pasta será criada automaticamente se não existir.
    2. Selecione a opção de **implantar modelos ARM na sua fábrica**de dados:
        - O padrão não é selecionado. Pode implantar modelos ARM gerados mais tarde manualmente.
        - Selecione para implementar modelos ARM gerados para a fábrica de dados diretamente.
    ![passo4](media/how-to-migrate-ssis-job-ssms/step4.png)

1. Migrar, e depois verificar os resultados.
![passo5](media/how-to-migrate-ssis-job-ssms/step5.png)

## <a name="next-steps"></a>Passos seguintes

[Executar e monitorizar o gasoduto](how-to-invoke-ssis-package-ssis-activity.md)
