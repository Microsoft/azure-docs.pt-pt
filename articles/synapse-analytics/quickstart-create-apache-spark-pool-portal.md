---
title: Quickstart - Crie uma piscina Apache Spark (pré-visualização) utilizando o portal Azure
description: Crie uma nova piscina Apache Spark utilizando o portal Azure seguindo os passos deste guia.
services: synapse-analytics
author: julieMSFT
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: ''
ms.date: 04/15/2020
ms.author: jrasnick
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 04a413ab171ea65c72182928ad4897530cb00e15
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83666331"
---
# <a name="quickstart-create-a-new-apache-spark-pool-preview-using-the-azure-portal"></a>Quickstart: Criar uma nova piscina Apache Spark (pré-visualização) utilizando o portal Azure

A Azure Synapse Analytics oferece vários motores de análise para o ajudar a ingerir, transformar, modelar, analisar e distribuir os seus dados. Um pool Apache Spark fornece capacidades de computação de dados grandes de código aberto. Depois de ter criado uma piscina Apache Spark no seu espaço de trabalho Synapse, os dados podem ser carregados, modelados, processados e distribuídos para uma visão analítica mais rápida.

Neste arranque rápido, você aprende a usar o portal Azure para criar uma piscina Apache Spark em um espaço de trabalho Synapse.

> [!IMPORTANT]
> A faturação dos casos Spark é pronunciada por minuto, quer esteja a usá-las ou não. Certifique-se de que encerra a sua instância Spark depois de terminar de usá-la, ou demarcar um curto prazo. Para obter mais informações, consulte a secção **Limpar recursos** deste artigo.

Se não tiver uma subscrição Azure, [crie uma conta gratuita antes](https://azure.microsoft.com/free/)de começar .

## <a name="prerequisites"></a>Pré-requisitos

- Assinatura Azure - [crie uma gratuitamente](https://azure.microsoft.com/free/)
- [Espaço de trabalho synapse Analytics](quickstart-create-workspace.md)

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Inscreva-se no [portal Azure](https://portal.azure.com/)

## <a name="navigate-to-the-synapse-workspace"></a>Navegue para o espaço de trabalho synapse 
1. Navegue para o espaço de trabalho Synapse onde a piscina Apache Spark será criada digitando o nome do serviço (ou nome de recurso diretamente) na barra de pesquisa.
![Barra de pesquisa do portal Azure com espaços de trabalho Synapse digitados.](media/quickstart-create-sql-pool/create-sql-pool-00a.png)
1. A partir da lista de espaços de trabalho, digite o nome (ou parte do nome) do espaço de trabalho para abrir. Para este exemplo, usaremos um espaço de trabalho chamado **contosoanalytics.**
![Listagem de espaços de trabalho synapse filtrados para mostrar aqueles que contêm o nome Contoso.](media/quickstart-create-sql-pool/create-sql-pool-00b.png)


## <a name="create-new-apache-spark-pool"></a>Crie uma nova piscina Apache Spark

1. No espaço de trabalho Synapse onde pretende criar a piscina Apache Spark, selecione **new Apache Spark pool**.
    ![Visão geral do espaço de trabalho synapse com uma caixa vermelha em torno do comando para criar uma nova piscina Apache Spark](media/quickstart-create-apache-spark-pool/create-spark-pool-portal-01.png)
2. Introduza os seguintes detalhes no separador **Basics:**

    |Definição | Valor sugerido | Descrição |
    | :------ | :-------------- | :---------- |
    | **Nome da piscina Apache Spark** | Um nome de piscina válido | Este é o nome que a piscina Apache Spark terá. |
    | **Tamanho do nó** | Pequeno (4 vCPU / 32 GB) | Desloque-o ao menor tamanho para reduzir os custos para este arranque rápido |
    | **Dimensionamento Automático** | Desativado | Não precisamos de escala automática para este arranque rápido. |
    | **Número de nós** | 5 | Use um pequeno tamanho para limitar os custos para este arranque rápido |


    ![Apache Spark pool cria fluxo - separador básico.](media/quickstart-create-apache-spark-pool/create-spark-pool-portal-02.png)
    > [!IMPORTANT]
    > Note que existem limitações específicas para os nomes que as piscinas Apache Spark podem usar. Os nomes devem conter apenas letras ou números, devem ser 15 ou menos caracteres, devem começar com uma letra, não conter palavras reservadas, e ser únicos no espaço de trabalho.

3. Selecione **Seguinte: definições adicionais** e reveja as definições predefinidas. Não modifique quaisquer definições predefinidas.
    ![Apache Spark pool cria fluxo - separador de configurações adicionais.](media/quickstart-create-apache-spark-pool/create-spark-pool-portal-03.png)

4. Selecione **Seguinte: tags**. Não adicione etiquetas.
    ![Apache Spark pool cria fluxo - separador de configurações adicionais.](media/quickstart-create-apache-spark-pool/create-spark-pool-03-tags.png)

5. Selecione **Rever + criar**.

6. Certifique-se de que os detalhes parecem corretos com base no que foi previamente introduzido e selecione **Criar**.
    ![Apache Spark pool criar fluxo - separador de definições de revisão.](media/quickstart-create-apache-spark-pool/create-spark-pool-portal-05.png)

7. Neste ponto, o fluxo de fornecimento de recursos começará, indicando uma vez que esteja completo.
    ![Apache Spark pool cria fluxo - fornecimento de recursos.](media/quickstart-create-apache-spark-pool/create-spark-pool-portal-06.png)

8. Após o fornecimento completo, navegar de volta para o espaço de trabalho mostrará uma nova entrada para a recém-criada piscina Apache Spark.
    ![Apache Spark pool cria fluxo - fornecimento de recursos.](media/quickstart-create-apache-spark-pool/create-spark-pool-portal-07.png)

9. Neste momento, não há recursos em execução, não há encargos para a Spark, criaste metadados sobre os casos de Spark que queres criar.

## <a name="clean-up-resources"></a>Limpar recursos

Siga os passos abaixo para eliminar a piscina Apache Spark do espaço de trabalho.
> [!WARNING]
> A eliminação de uma piscina Apache Spark removerá o motor de análise do espaço de trabalho. Deixará de ser possível ligar-se à piscina, e todas as consultas, oleodutos e cadernos que utilizam esta piscina Apache Spark deixarão de funcionar.

Se quiser eliminar a piscina Apache Spark, faça o seguinte:

1. Navegue até à lâmina das piscinas Apache Spark no espaço de trabalho.
2. Selecione a piscina Apache Spark a eliminar (neste caso, **contosospark).**
3. Prima **apagar**.
 ![Listagem de piscinas Apache Spark, com a piscina recentemente criada selecionada.](media/quickstart-create-apache-spark-pool/create-spark-pool-portal-08.png)
4. Confirme a eliminação e prima **Apagar** o botão.
 ![Diálogo de confirmação para eliminar a piscina Apache Spark selecionada.](media/quickstart-create-apache-spark-pool/create-spark-pool-portal-10.png)
5. Quando o processo terminar com sucesso, a piscina Apache Spark deixará de estar listada nos recursos do espaço de trabalho.

## <a name="next-steps"></a>Próximos passos

- Ver [Quickstart: Crie um caderno Apache Spark](quickstart-apache-spark-notebook.md).
- Ver [Quickstart: Criar uma piscina SQL synapse utilizando o portal Azure](quickstart-create-sql-pool-portal.md).
