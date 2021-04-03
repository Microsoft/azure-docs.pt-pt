---
title: 'Quickstart: Criar uma piscina Apache Spark sem servidor utilizando o portal Azure'
description: Crie uma piscina Apache Spark sem servidor utilizando o portal Azure seguindo os passos deste guia.
services: synapse-analytics
author: julieMSFT
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: spark
ms.date: 04/15/2020
ms.author: jrasnick
ms.reviewer: jrasnick
ms.openlocfilehash: 3fc7a7f137701590e4bddd791a3fdd2d1eaeca4a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96461608"
---
# <a name="quickstart-create-a-new-serverless-apache-spark-pool-using-the-azure-portal"></a>Quickstart: Criar uma nova piscina Apache Spark sem servidor utilizando o portal Azure

O Azure Synapse Analytics oferece vários motores de análise para ajudá-lo a ingerir, transformar, modelar, analisar e distribuir os seus dados. Um conjunto Apache Spark fornece capacidades de computação de dados de código aberto. Depois de criar uma piscina Apache Spark no seu espaço de trabalho Synapse, os dados podem ser carregados, modelados, processados e distribuídos para uma visão analítica mais rápida.

Neste arranque rápido, você aprende a usar o portal Azure para criar uma piscina Apache Spark em um espaço de trabalho Synapse.

> [!IMPORTANT]
> A faturação de casos de Spark é prostimada por minuto, quer esteja a usá-las ou não. Certifique-se de que desliga a sua instância Spark depois de ter terminado de a utilizar ou de definir um curto período de tempo. Para obter mais informações, consulte a secção **Limpar recursos** deste artigo.

Se não tiver uma subscrição do Azure, [crie uma conta gratuita antes de começar](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Pré-requisitos

- Azure subscrição - [crie uma gratuitamente](https://azure.microsoft.com/free/)
- [Espaço de trabalho Synapse Analytics](quickstart-create-workspace.md)

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Inicie sessão no [portal do Azure](https://portal.azure.com/)

## <a name="navigate-to-the-synapse-workspace"></a>Navegue até ao espaço de trabalho da Sinapse 
1. Navegue até ao espaço de trabalho da Sinaapse, onde a piscina Apache Spark será criada digitando o nome de serviço (ou nome do recurso diretamente) na barra de pesquisa.
![Barra de pesquisa de portal Azure com espaços de trabalho synapse digitado.](media/quickstart-create-sql-pool/create-sql-pool-00a.png)
1. Da lista de espaços de trabalho, digite o nome (ou parte do nome) do espaço de trabalho para abrir. Para este exemplo, usaremos um espaço de trabalho chamado **contosoanalytics.**
![Listagem de espaços de trabalho do Synapse filtrados para mostrar aqueles que contêm o nome Contoso.](media/quickstart-create-sql-pool/create-sql-pool-00b.png)


## <a name="create-new-apache-spark-pool"></a>Criar nova piscina Apache Spark

1. No espaço de trabalho da Sinaapse onde pretende criar a piscina Apache Spark, selecione **New Apache Spark pool.**
    ![Visão geral do espaço de trabalho da Sinaapse com uma caixa vermelha em torno do comando para criar uma nova piscina Apache Spark](media/quickstart-create-apache-spark-pool/create-spark-pool-portal-01.png)
2. Introduza os seguintes detalhes no separador **Básicos:**

    |Definição | Valor sugerido | Descrição |
    | :------ | :-------------- | :---------- |
    | **Nome da piscina Apache Spark** | Um nome de piscina válido | Este é o nome que a piscina Apache Spark terá. |
    | **Tamanho do nó** | Pequeno (4 vCPU / 32 GB) | Deslote isto para o menor tamanho para reduzir os custos para este arranque rápido |
    | **Dimensionamento Automático** | Desativado | Não precisamos de autoescala para este arranque rápido. |
    | **Número de nós** | 5 | Use um pequeno tamanho para limitar os custos para este arranque rápido |


    ![A piscina Apache Spark cria fluxo - separador básico.](media/quickstart-create-apache-spark-pool/create-spark-pool-portal-02.png)
    > [!IMPORTANT]
    > Note que existem limitações específicas para os nomes que as piscinas Apache Spark podem usar. Os nomes devem conter apenas letras ou números, devem ter 15 ou menos caracteres, devem começar com uma letra, não conter palavras reservadas, e ser únicos no espaço de trabalho.

3. Selecione **Seguinte: definições adicionais** e reveja as definições predefinidos. Não modifique quaisquer definições predefinições.
    ![Screenshot que mostra a página "Create Apache Spark pool" com o separador "Definições adicionais" selecionada.](media/quickstart-create-apache-spark-pool/create-spark-pool-portal-03.png)

4. Selecione **Seguinte: tags**. Não adicione etiquetas.
    ![A piscina Apache Spark cria fluxo - separador de configurações adicionais.](media/quickstart-create-apache-spark-pool/create-spark-pool-03-tags.png)

5. Selecione **Rever + criar**.

6. Certifique-se de que os detalhes parecem corretos com base no que foi previamente introduzido e selecione **Criar**.
    ![A piscina Apache Spark cria fluxo - separador de definições de revisão.](media/quickstart-create-apache-spark-pool/create-spark-pool-portal-05.png)

7. Neste ponto, o fluxo de fornecimento de recursos começará, indicando uma vez que esteja completo.
    ![Screenshot que mostra a página "Visão Geral" com uma mensagem "A sua implementação está completa" exibida.](media/quickstart-create-apache-spark-pool/create-spark-pool-portal-06.png)

8. Após o a provisionamento concluído, navegar de volta ao espaço de trabalho mostrará uma nova entrada para a recém-criada piscina Apache Spark.
    ![A piscina Apache Spark cria fluxo - fornecimento de recursos.](media/quickstart-create-apache-spark-pool/create-spark-pool-portal-07.png)

9. Neste momento, não há recursos em execução, nem encargos para a Spark, criaste metadados sobre as instâncias Spark que queres criar.

## <a name="clean-up-resources"></a>Limpar os recursos

Siga os passos abaixo para eliminar a piscina Apache Spark do espaço de trabalho.
> [!WARNING]
> A eliminação de uma piscina Apache Spark removerá o motor de análise do espaço de trabalho. Não será mais possível ligar-se à piscina, e todas as consultas, oleodutos e cadernos que usam esta piscina Apache Spark deixarão de funcionar.

Se pretender eliminar a piscina Apache Spark, faça o seguinte:

1. Navegue até à lâmina das piscinas Apache Spark no espaço de trabalho.
2. Selecione a piscina Apache Spark a eliminar (neste caso, **contosospark).**
3. Prima **para apagar**.
 ![Listagem de piscinas Apache Spark, com a piscina recentemente criada selecionada.](media/quickstart-create-apache-spark-pool/create-spark-pool-portal-08.png)
4. Confirme a eliminação e prima O botão **Eliminar.**
 ![Diálogo de confirmação para eliminar a piscina Apache Spark selecionada.](media/quickstart-create-apache-spark-pool/create-spark-pool-portal-10.png)
5. Quando o processo estiver concluído com sucesso, a piscina Apache Spark deixará de estar listada nos recursos do espaço de trabalho.

## <a name="next-steps"></a>Passos seguintes

- Ver [Quickstart: Criar um caderno Apache Spark](quickstart-apache-spark-notebook.md).
- Consulte [Quickstart: Crie uma piscina SQL dedicada utilizando o portal Azure](quickstart-create-sql-pool-portal.md).
