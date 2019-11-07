---
title: 'Utilizar a CLI do Databricks a partir do Azure Cloud Shell '
description: Saiba como usar a CLI do databricks de Azure Cloud Shell para executar operações no Azure Databricks.
services: azure-databricks
author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.workload: big-data
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: mamccrea
ms.openlocfilehash: efb0d3222bfd98b15502163979425d47fa459e07
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/05/2019
ms.locfileid: "73605726"
---
# <a name="use-databricks-cli-from-azure-cloud-shell"></a>Utilizar a CLI do Databricks a partir do Azure Cloud Shell

Saiba como usar a CLI do databricks de Azure Cloud Shell para executar operações no databricks.

## <a name="prerequisites"></a>Pré-requisitos

* Um Azure Databricks espaço de trabalho e cluster. Para obter instruções, consulte Introdução [ao Azure Databricks](quickstart-create-databricks-workspace-portal.md). 

* Configure um token de acesso pessoal no databricks. Para obter instruções, consulte [Gerenciamento de token](/azure/databricks/dev-tools/api/latest/authentication).

## <a name="use-the-azure-cloud-shell"></a>Usar o Azure Cloud Shell

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
 
2. No canto superior direito, clique no ícone de **Cloud Shell** .

   ![Iniciar Cloud Shell](./media/databricks-cli-from-azure-cloud-shell/launch-azure-cloud-shell.png "Iniciar o Azure Cloud Shell")

3. Certifique-se de selecionar **bash** para o ambiente de Cloud Shell. Você pode selecionar na opção suspensa, conforme mostrado na captura de tela a seguir.

   ![Selecionar bash para o ambiente de Cloud Shell](./media/databricks-cli-from-azure-cloud-shell/select-bash-for-shell.png "Selecionar bash") 

4. Crie um ambiente virtual no qual você possa instalar a CLI do databricks. No trecho de código abaixo, você cria um ambiente virtual chamado `databrickscli`.

       virtualenv -p /usr/bin/python2.7 databrickscli

5. Alterne para o ambiente virtual que você criou.

       source databrickscli/bin/activate

6. Instale a CLI do databricks.

       pip install databricks-cli

7. Configure a autenticação com o databricks usando o token de acesso que você deve ter criado, listado como parte dos pré-requisitos. Utilize o seguinte comando:

       databricks configure --token

    Você receberá os seguintes prompts:

    * Primeiro, você será solicitado a inserir o host do databricks. Insira o valor no formato `https://eastus2.azuredatabricks.net`. Aqui, o **leste dos EUA 2** é a região do Azure em que você criou seu espaço de trabalho Azure Databricks.

    * Em seguida, será solicitado que você insira um token. Insira o token que você criou anteriormente.

Depois de concluir essas etapas, você pode começar a usar a CLI do databricks do Azure Cloud Shell.

## <a name="use-databricks-cli"></a>Usar a CLI do databricks

Agora você pode começar a usar a CLI do databricks. Por exemplo, execute o seguinte comando para listar todos os clusters do databricks que você tem em seu espaço de trabalho.

    databricks clusters list

Você também pode usar o comando a seguir para acessar o DBFS (sistema de arquivos do databricks).

    databricks fs ls


Para obter uma referência completa sobre comandos, consulte [CLI do databricks](/azure/databricks/dev-tools/databricks-cli).


## <a name="next-steps"></a>Passos seguintes

* Para saber mais sobre CLI do Azure, consulte [CLI do Azure visão geral](../cloud-shell/overview.md)
* Para ver uma lista de comandos para CLI do Azure, consulte [referência de CLI do Azure](https://docs.microsoft.com/cli/azure/reference-index?view=azure-cli-latest)
* Para ver uma lista de comandos para a CLI do databricks, consulte [CLI do databricks](/azure/databricks/dev-tools/databricks-cli)


