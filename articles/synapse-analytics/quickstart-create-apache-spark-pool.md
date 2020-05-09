---
title: Quickstart criar uma piscina Apache Spark (pré-visualização)
description: Crie uma nova piscina Apache Spark para um espaço de trabalho Azure Synapse Analytics seguindo os passos deste guia.
services: synapse-analytics
author: malvenko
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: ''
ms.date: 04/15/2020
ms.author: josels
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 2501f66c42360d3ec1626f5f4ee42e0212f1967b
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/05/2020
ms.locfileid: "82792246"
---
# <a name="quickstart-create-a-new-apache-spark-pool-preview"></a>Quickstart: Crie uma nova piscina Apache Spark (pré-visualização)

A Synapse Analytics oferece vários motores de análise para ajudá-lo a ingerir, transformar, modelar, analisar e servir os seus dados. Um pool Apache Spark oferece capacidades de computação de dados grandes de código aberto. Depois de criar uma piscina Apache Spark no seu espaço de trabalho Synapse, os dados podem ser carregados, modelados, processados e servidos para obter insights.

Neste arranque rápido, você aprende a usar o portal Azure para criar uma piscina Apache Spark em um espaço de trabalho Synapse.

> [!IMPORTANT]
> A faturação dos casos Spark é pronunciada por minuto, quer esteja a usá-las ou não. Certifique-se de que encerra a sua instância Spark depois de terminar de usá-la, ou demarcar um curto prazo. Para obter mais informações, consulte a secção **Limpar recursos** deste artigo.

Se não tiver uma subscrição Azure, [crie uma conta gratuita antes](https://azure.microsoft.com/free/)de começar .

## <a name="prerequisites"></a>Pré-requisitos

- Assinatura Azure - [crie uma gratuitamente](https://azure.microsoft.com/free/)
- [Espaço de trabalho synapse Analytics](quickstart-create-workspace.md)

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Inscreva-se no [portal Azure](https://portal.azure.com/)

## <a name="create-new-apache-spark-pool"></a>Crie uma nova piscina Apache Spark

1. No espaço de trabalho Synapse onde pretende criar a piscina Apache Spark, clique na **piscina New Apache Spark**.
![Visão geral do espaço de trabalho synapse com uma caixa vermelha em torno do comando para criar uma nova piscina Apache Spark](media/quickstart-create-apache-spark-pool/quickstart-create-spark-pool-01.png)
2. Introduza os seguintes detalhes no separador **Basics:**

    |Definição | Valor sugerido | Descrição |
    | :------ | :-------------- | :---------- |
    | **Nome da piscina Apache Spark** | Um nome de piscina válido | Este é o nome que a piscina Apache Spark terá. |
    | **Tamanho do nó** | Pequeno (4 vCPU / 32 GB) | Desloque-o ao menor tamanho para reduzir os custos para este arranque rápido |
    | **Dimensionamento Automático** | Ativado | Deixe esta definição padrão |
    | **Número de nós** | 3 - 40 | Deixe esta definição padrão |
    ||||

    ![Apache Spark pool cria fluxo - separador básico.](media/quickstart-create-apache-spark-pool/quickstart-create-spark-pool-02.png)
    > [!IMPORTANT]
    > Note que existem limitações específicas para os nomes que as piscinas Apache Spark podem usar. Os nomes devem conter apenas letras ou números, devem ser 15 ou menos caracteres, devem começar com uma letra, não conter palavras reservadas, e ser únicos no espaço de trabalho.

3. Clique em **Seguinte: definições adicionais** e reveja as definições predefinidas. Não modifique quaisquer definições predefinidas.
![Apache Spark pool cria fluxo - separador de configurações adicionais.](media/quickstart-create-apache-spark-pool/quickstart-create-spark-pool-03.png)

4. Clique **em seguir: tags**. Não adicione etiquetas.
![Apache Spark pool cria fluxo - separador de configurações adicionais.](media/quickstart-create-apache-spark-pool/quickstart-create-spark-pool-03-tags.png)

5. Clique em **Rever + criar**.

6. Certifique-se de que os detalhes parecem corretos com base no que foi previamente introduzido e clique em **Criar**.
![Apache Spark pool criar fluxo - separador de definições de revisão.](media/quickstart-create-apache-spark-pool/quickstart-create-spark-pool-05.png)

7. Neste ponto, o fluxo de fornecimento de recursos começará, indicando que uma vez que o conjunto de Faíscas Apache completo ![sairá de fluxo - fornecimento de recursos.](media/quickstart-create-apache-spark-pool/quickstart-create-spark-pool-06.png)

8. Após o fornecimento completo, navegar de volta para o espaço de trabalho mostrará uma nova entrada para a recém-criada piscina Apache Spark.
 ![Apache Spark pool cria fluxo - fornecimento de recursos.](media/quickstart-create-apache-spark-pool/quickstart-create-spark-pool-07.png)

9. Neste momento, não há recursos em execução, não há encargos para a Spark, criaste metadados sobre os casos de Spark que queres criar.

## <a name="clean-up-resources"></a>Limpar recursos

Siga os passos abaixo para eliminar a piscina Apache Spark do espaço de trabalho.
> [!WARNING]
> A eliminação de uma piscina Apache Spark removerá o motor de análise do espaço de trabalho. Deixará de ser possível ligar-se à piscina, e todas as consultas, oleodutos e cadernos que utilizam esta piscina Apache Spark deixarão de funcionar.

Se quiser eliminar a piscina Apache Spark, faça o seguinte:

1. Navegue até à lâmina das piscinas Apache Spark no espaço de trabalho.
2. Selecione a piscina Apache a eliminar (neste caso, **contosospark)**
3. Prima **apagar**.
 ![Listagem de piscinas Apache Spark, com a piscina recentemente criada selecionada.](media/quickstart-create-apache-spark-pool/quickstart-create-spark-pool-08.png)
4. Confirme a eliminação e prima **Apagar** o botão.
 ![Diálogo de confirmação para eliminar a piscina Apache Spark selecionada.](media/quickstart-create-apache-spark-pool/quickstart-create-spark-pool-10.png)
5. Quando o processo terminar com sucesso, a piscina Apache Spark deixará de estar listada nos recursos do espaço de trabalho.

Uma vez criada a piscina SQL, está disponível no espaço de trabalho para carregar dados, processamento de fluxos, leitura do lago, e assim.

## <a name="next-steps"></a>Passos seguintes

- Ver [Quickstart: Crie um caderno Apache Spark](quickstart-apache-spark-notebook.md).
- Ver [Quickstart: Criar uma piscina SQL synapse utilizando o portal Azure](quickstart-create-sql-pool.md).
