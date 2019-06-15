---
title: 'Utilizar a CLI do Databricks a partir do Azure Cloud Shell '
description: Saiba como utilizar a CLI do Databricks do Azure Cloud Shell.
services: azure-databricks
author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.workload: big-data
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: mamccrea
ms.openlocfilehash: dae481fb477223f149404c6a09cad024bc15cd90
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "62108742"
---
# <a name="use-databricks-cli-from-azure-cloud-shell"></a>Utilizar a CLI do Databricks a partir do Azure Cloud Shell

Saiba como utilizar a CLI do Databricks do Azure Cloud Shell para executar operações no Databricks.

## <a name="prerequisites"></a>Pré-requisitos

* Uma área de trabalho do Azure Databricks e o cluster. Para obter instruções, consulte [introdução ao Azure Databricks](quickstart-create-databricks-workspace-portal.md). 

* Configure um token de acesso pessoal no Databricks. Para obter instruções, consulte [Token gestão](https://docs.azuredatabricks.net/api/latest/authentication.html#token-management).

## <a name="use-the-azure-cloud-shell"></a>Utilizar o Azure Cloud Shell

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
 
2. No canto superior direito, clique nas **Cloud Shell** ícone.

   ![Iniciar Cloud Shell](./media/databricks-cli-from-azure-cloud-shell/launch-azure-cloud-shell.png "iniciar o Azure Cloud Shell")

3. Certifique-se de que seleciona **Bash** para o ambiente de Cloud Shell. Pode selecionar a opção de menu pendente, conforme mostrado na captura de ecrã seguinte.

   ![Selecione o Bash para o ambiente de Cloud Shell](./media/databricks-cli-from-azure-cloud-shell/select-bash-for-shell.png "selecione Bash") 

4. Crie um ambiente virtual em que pode instalar a CLI do Databricks. O fragmento abaixo, vai criar um ambiente virtual chamado `databrickscli`.

       virtualenv -p /usr/bin/python2.7 databrickscli

5. Mude para o ambiente virtual que criou.

       source databrickscli/bin/activate

6. Instale a CLI do Databricks.

       pip install databricks-cli

7. Configure a autenticação com o Databricks utilizando o token de acesso que tem de ter criado, listado como parte dos pré-requisitos. Utilize o seguinte comando:

       databricks configure --token

    Receberá as instruções seguintes:

    * Em primeiro lugar, lhe for pedido para introduzir o anfitrião do Databricks. Introduza o valor no formato `https://eastus2.azuredatabricks.net`. Aqui, **E.U.A. Leste 2** é a região do Azure onde criou a sua área de trabalho do Azure Databricks.

    * Em seguida, lhe for pedido para introduzir um token. Introduza o token que criou anteriormente.

Depois de concluir estes passos, pode começar a utilizar a CLI do Databricks a partir do Azure Cloud Shell.

## <a name="use-databricks-cli"></a>Utilizar a CLI do Databricks

Agora pode começar a utilizar a CLI do Databricks. Por exemplo, execute o seguinte comando para listar todos os clusters do Databricks que tem na sua área de trabalho.

    databricks clusters list

Também pode utilizar o seguinte comando para acessar o sistema de ficheiros de Databricks (DBFS).

    databricks fs ls


Para obter uma referência completa de comandos, consulte [CLI do Databricks](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html).


## <a name="next-steps"></a>Passos Seguintes

* Para saber mais sobre a CLI do Azure, veja [descrição geral da CLI do Azure](../cloud-shell/overview.md)
* Para ver uma lista de comandos do CLI do Azure, consulte [referência da CLI do Azure](https://docs.microsoft.com/cli/azure/reference-index?view=azure-cli-latest)
* Para ver uma lista de comandos para a CLI do Databricks, consulte [CLI do Databricks](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html)


