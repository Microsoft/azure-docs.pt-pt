---
title: Quickstart - crie um espaço de trabalho synapse
description: Crie um espaço de trabalho sinapse seguindo os passos deste guia.
services: synapse-analytics
author: pimorano
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: ''
ms.date: 04/15/2020
ms.author: pimorano
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: e5896d1eabaf43afcbab7513d7abb0b1de4efe51
ms.sourcegitcommit: 5a8c8ac84c36859611158892422fc66395f808dc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/10/2020
ms.locfileid: "84656702"
---
# <a name="quickstart-create-a-synapse-workspace"></a>Quickstart: Criar um espaço de trabalho synapse

Este quickstart descreve os passos para criar um espaço de trabalho Azure Synapse utilizando o portal Azure.

Se não tiver uma subscrição do Azure, [crie uma conta gratuita antes de começar](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Pré-requisitos

- [Conta de armazenamento de Azure Data Lake Gen2](../storage/common/storage-account-create.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Inscreva-se no [portal Azure](https://portal.azure.com/)

## <a name="create-an-azure-synapse-workspace-using-the-azure-portal"></a>Criar um espaço de trabalho Azure Synapse utilizando o portal Azure

1. No painel de pesquisa do Microsoft Azure, **insira o espaço de trabalho synapse** e, em seguida, selecione este serviço.
![Barra de pesquisa de portal Azure com espaços de trabalho Azure Synapse digitado.](media/quickstart-create-synapse-workspace/workspace-search.png)
2. Na página **de espaços de trabalho Synapse,** clique + **Adicionar**.
![Comando para criar novo espaço de trabalho Azure Synapse destacado.](media/quickstart-create-synapse-workspace/create-workspace-02.png)
3. Preencha o formulário **do espaço de trabalho Azure Synapse** com as seguintes informações:

    | Definição | Valor sugerido | Descrição |
    | :------ | :-------------- | :---------- |
    | **Subscrição** | *A sua subscrição* | Para obter detalhes sobre as suas subscrições, veja [Subscriptions](https://account.windowsazure.com/Subscriptions) (Subscrições). |
    | **Grupo de recursos** | *Qualquer grupo de recursos* | Para nomes de grupo de recursos válidos, veja [Naming rules and restrictions](/azure/architecture/best-practices/resource-naming?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) (Atribuição de nomes de regras e restrições). |
    | **Nome da área de trabalho** | espaço de trabalho mysamplework | Especifica o nome do espaço de trabalho, que também será utilizado para os pontos finais de ligação.|
    | **Região** | E.U.A. Leste 2 | Especifica a localização do espaço de trabalho.|
    | **Data Lake Storage Gen2** | Conta:`storage account name` </br> Sistema de ficheiros:`root file system to use` | Especifica o nome da conta de armazenamento ADLS Gen2 para usar como armazenamento primário e o sistema de ficheiros a utilizar.|
    ||||

    ![Fluxo de provisão de espaço de trabalho - Separador básico.](media/quickstart-create-synapse-workspace/create-workspace-03.png)

    A conta de armazenamento pode ser selecionada a partir de:
    - Uma lista de contas da ADLS Gen2 disponíveis na sua subscrição
    - Introduzido manualmente usando o nome da conta

    > [!IMPORTANT]
    > O espaço de trabalho Azure Synapse precisa de ser capaz de ler e escrever para a conta ADLS Gen2 selecionada. Além disso, para qualquer conta de armazenamento que ligue como conta de armazenamento primário, deve ter ativado espaço **de nome hierárquico** na criação da conta de armazenamento.
    >
    > Abaixo dos campos de seleção da ADLS Gen2, há uma nota a dizer que a identidade gerida do espaço de trabalho será atribuída ao papel **de Contribuinte de Dados Storaqe Blob** no sistema de ficheiros data lake de armazenamento selecionado Gen2, concedendo-lhe acesso total.

4. (Opcional) Modificar qualquer um dos **separadores de predefinição de segurança + rede:**
5. (Opcional) Adicione todas as etiquetas no **separador Tags.**
6. O **separador Resumo** executará as validações necessárias para garantir que o espaço de trabalho possa ser criado com sucesso. Uma vez que a validação tenha sucesso, prima **Crie** ![ Workspace provisioning flow - separador de confirmação.](media/quickstart-create-synapse-workspace/create-workspace-05.png)
7. Uma vez concluído o processo de fornecimento de recursos com sucesso, verá uma entrada para o espaço de trabalho criado na lista de espaços de trabalho da Synapse. ![Listagem de espaços de trabalho da Sinaapse mostrando o espaço de trabalho recentemente atado.](media/quickstart-create-synapse-workspace/create-workspace-07.png)

## <a name="clean-up-resources"></a>Limpar recursos

Siga os passos abaixo para eliminar o espaço de trabalho Azure Synapse.
> [!WARNING]
> A eliminação de um espaço de trabalho Azure Synapse removerá os motores de análise e os dados armazenados na base de dados dos pools SQL contidos e metadados do espaço de trabalho. Deixará de ser possível ligar-se aos pontos finais SQL, pontos finais Apache Spark. Todos os artefactos de código serão eliminados (consultas, cadernos, definições de emprego e oleodutos).
>
> A eliminação do espaço de trabalho **não** afetará os dados da Data Lake Store Gen2 ligada ao espaço de trabalho.

Se pretender eliminar o espaço de trabalho Azure Synapse, complete os seguintes passos:

1. Navegue até ao espaço de trabalho Azure Synapse para eliminar.
1. Prima **apagar** na barra de comando.
 ![Azure Synapse workspace overview - delete command highlighted.](media/quickstart-create-synapse-workspace/create-workspace-10.png)
1. Confirme a eliminação e prima o botão **Eliminar.**
 ![Azure Synapse workspace overview - elimine o diálogo de confirmação do espaço de trabalho.](media/quickstart-create-synapse-workspace/create-workspace-11.png)
1. Quando o processo estiver concluído com sucesso, o espaço de trabalho Azure Synapse deixará de constar na lista de espaços de trabalho.

## <a name="next-steps"></a>Próximos passos

Em seguida, você pode [criar piscinas SQL](quickstart-create-sql-pool-studio.md) ou [criar piscinas Apache Spark](quickstart-create-apache-spark-pool-studio.md) para começar a analisar e explorar seus dados.
