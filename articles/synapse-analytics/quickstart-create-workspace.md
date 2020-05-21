---
title: Quickstart - criar um espaço de trabalho
description: Crie um espaço de trabalho Azure Synapse Analytics seguindo os passos deste guia.
services: synapse-analytics
author: pimorano
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: ''
ms.date: 04/15/2020
ms.author: pimorano
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: df7753983cea2ca2cc285f04cd32da6ed065f415
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83656154"
---
# <a name="quickstart-create-an-azure-synapse-analytics-workspace-preview"></a>Quickstart: Criar um espaço de trabalho Azure Synapse Analytics (pré-visualização)

Este quickstart descreve os passos para criar um espaço de trabalho Azure Synapse utilizando o portal Azure.

Se não tiver uma subscrição Azure, [crie uma conta gratuita antes](https://azure.microsoft.com/free/)de começar .

## <a name="prerequisites"></a>Pré-requisitos

- [Conta de armazenamento de armazenamento do Lago Azure Data Gen2](../storage/common/storage-account-create.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)

## <a name="register-azure-synapse-resource-provider"></a>Registe o fornecedor de recursos Azure Synapse
Se ainda não o fez, registe o fornecedor de recursos Azure Synapse. Registar um fornecedor de recursos configura a sua subscrição para trabalhar com o fornecedor de recursos. Para registar o fornecedor de recursos Azure Synapse, consulte [os fornecedores e tipos](https://docs.microsoft.com/azure/azure-resource-manager/management/resource-providers-and-types)de recursos azure . Escolha *microsoft.Synapse* na lista de fornecedores de recursos quando se registar.

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Inscreva-se no [portal Azure](https://portal.azure.com/)

## <a name="create-an-azure-synapse-workspace-using-the-azure-portal"></a>Criar um espaço de trabalho Azure Synapse utilizando o portal Azure

1. No painel de pesquisa do Microsoft Azure, introduza o espaço de **trabalho synapse** e, em seguida, selecione este serviço.
![Barra de pesquisa do portal Azure com espaços de trabalho Azure Synapse digitados.](media/quickstart-create-synapse-workspace/workspace-search.png)
2. Na página dos espaços de **trabalho Synapse,** clique **+ Adicionar**.
![Comando para criar novo espaço de trabalho Azure Synapse em destaque.](media/quickstart-create-synapse-workspace/create-workspace-02.png)
3. Preencha o formulário de espaço de **trabalho Azure Synapse** com as seguintes informações:

    | Definição | Valor sugerido | Descrição |
    | :------ | :-------------- | :---------- |
    | **Subscrição** | *A sua subscrição* | Para obter detalhes sobre as suas subscrições, veja [Subscriptions](https://account.windowsazure.com/Subscriptions) (Subscrições). |
    | **Grupo de recursos** | *Qualquer grupo de recursos* | Para nomes de grupo de recursos válidos, veja [Naming rules and restrictions](/azure/architecture/best-practices/resource-naming?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) (Atribuição de nomes de regras e restrições). |
    | **Nome da área de trabalho** | mysampleworkspace | Especifica o nome do espaço de trabalho, que também será utilizado para pontos finais de ligação.|
    | **Região** | E.U.A. Leste 2 | Especifica a localização do espaço de trabalho.|
    | **Armazenamento do Data Lake Ger2** | Conta:`storage account name` </br> Sistema de ficheiros:`root file system to use` | Especifica o nome da conta de armazenamento ADLS Gen2 para usar como armazenamento primário e o sistema de ficheiros a utilizar.|
    ||||

    ![Fluxo de provisionamento do espaço de trabalho - Separador básico.](media/quickstart-create-synapse-workspace/create-workspace-03.png)

    A conta de armazenamento pode ser selecionada a partir de:
    - Uma lista de contas ADLS Gen2 disponíveis na sua subscrição
    - Inserido manualmente usando o nome da conta

    > [!IMPORTANT]
    > O espaço de trabalho Azure Synapse precisa de ser capaz de ler e escrever para a conta ADLS Gen2 selecionada. Além disso, para qualquer conta de armazenamento que ligue como conta de armazenamento principal, deve ter ativado espaço de **nome hierárquico** na criação da conta de armazenamento.
    >
    > Abaixo dos campos de seleção da ADLS Gen2, há uma nota a dizer que a identidade gerida do espaço de trabalho será atribuída ao papel **de Contribuinte de Dados Storaqe Blob** no sistema de ficheiros Data Lake Storage Gen2, que lhe concede acesso total.

4. (Opcional) Modificar qualquer um dos **separadores de predefinição** de rede Security + :
5. (Opcional) Adicione quaisquer etiquetas no separador **Tags.**
6. O separador **Resumo** executará as validações necessárias para garantir que o espaço de trabalho pode ser criado com sucesso. Assim que a validação for bem sucedida, prima **Criar** fluxo de ![ fornecimento workspace - separador de confirmação.](media/quickstart-create-synapse-workspace/create-workspace-05.png)
7. Assim que o processo de fornecimento de recursos terminar com sucesso, verá uma entrada para o espaço de trabalho criado na lista de espaços de trabalho Synapse. ![Listagem de espaços de trabalho synapse mostrando o espaço de trabalho recém-provisionado.](media/quickstart-create-synapse-workspace/create-workspace-07.png)

## <a name="clean-up-resources"></a>Limpar recursos

Siga os passos abaixo para eliminar o espaço de trabalho Azure Synapse.
> [!WARNING]
> A eliminação de um espaço de trabalho Azure Synapse removerá os motores de análise e os dados armazenados na base de dados das piscinas SQL e metadados do espaço de trabalho contidos. Deixará de ser possível ligar-se aos pontos finais do SQL, pontos finais da Apache Spark. Todos os artefactos de código serão eliminados (consultas, cadernos, definições de emprego e oleodutos).
>
> A aparas ao espaço de trabalho **não** afetará os dados da Data Lake Store Gen2 ligados ao espaço de trabalho.

Se pretender eliminar o espaço de trabalho Azure Synapse, complete os seguintes passos:

1. Navegue até ao espaço de trabalho Azure Synapse para eliminar.
1. Prima **apagar** na barra de comando.
 ![Visão geral do espaço de trabalho Azure Synapse - elimine o comando realçado.](media/quickstart-create-synapse-workspace/create-workspace-10.png)
1. Confirme a eliminação e prima o botão **Apagar.**
 ![Visão geral do espaço de trabalho Azure Synapse - elimine o diálogo de confirmação do espaço de trabalho.](media/quickstart-create-synapse-workspace/create-workspace-11.png)
1. Quando o processo terminar com sucesso, o espaço de trabalho Azure Synapse deixará de constar da lista de espaços de trabalho.

## <a name="next-steps"></a>Próximos passos

Em seguida, você pode [criar piscinas SQL](quickstart-create-sql-pool-studio.md) ou [criar piscinas Apache Spark](quickstart-create-apache-spark-pool-studio.md) para começar a analisar e explorar os seus dados.
