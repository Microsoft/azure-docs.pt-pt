---
title: 'Utilizar a CLI do Databricks a partir do Azure Cloud Shell '
description: Saiba como utilizar o DATABricks CLI da Azure Cloud Shell para realizar operações em Tijolos de Dados Azure.
services: azure-databricks
author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.workload: big-data
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: mamccrea
ms.openlocfilehash: efb0d3222bfd98b15502163979425d47fa459e07
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "73605726"
---
# <a name="use-databricks-cli-from-azure-cloud-shell"></a>Utilizar a CLI do Databricks a partir do Azure Cloud Shell

Saiba como utilizar o DATABricks CLI da Azure Cloud Shell para realizar operações em Databricks.

## <a name="prerequisites"></a>Pré-requisitos

* Um espaço de trabalho azure Databricks e cluster. Para obter instruções, consulte [Começar com os Tijolos de Dados Azure](quickstart-create-databricks-workspace-portal.md). 

* Configurar um sinal de acesso pessoal em Databricks. Para obter instruções, consulte a [gestão token](/azure/databricks/dev-tools/api/latest/authentication).

## <a name="use-the-azure-cloud-shell"></a>Use a casca de nuvem azure

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
 
2. Do canto superior direito, clique no ícone **Cloud Shell.**

   ![Lançar Cloud Shell](./media/databricks-cli-from-azure-cloud-shell/launch-azure-cloud-shell.png "Iniciar o Azure Cloud Shell")

3. Certifique-se de selecionar **Bash** para o ambiente Cloud Shell. Pode selecionar a partir da opção drop-down, como mostra a seguinte imagem.

   ![Selecione Bash para o ambiente Cloud Shell](./media/databricks-cli-from-azure-cloud-shell/select-bash-for-shell.png "Selecione Bash") 

4. Crie um ambiente virtual no qual possa instalar o DATABricks CLI. No corte abaixo, você cria um `databrickscli`ambiente virtual chamado .

       virtualenv -p /usr/bin/python2.7 databrickscli

5. Mude para o ambiente virtual que criou.

       source databrickscli/bin/activate

6. Instale o CLI de Databricks.

       pip install databricks-cli

7. Instale a autenticação com databricks utilizando o sinal de acesso que deve ter criado, listado como parte dos pré-requisitos. Utilize o seguinte comando:

       databricks configure --token

    Receberá as seguintes instruções:

    * Primeiro, é-lhe pedido que entre no anfitrião databricks. Introduza o valor `https://eastus2.azuredatabricks.net`no formato . Aqui, **East US 2** é a região de Azure onde criou o seu espaço de trabalho Azure Databricks.

    * Em seguida, é-lhe pedido que entre num símbolo. Insira o símbolo que criou anteriormente.

Assim que completar estes passos, pode começar a utilizar databricks CLI da Azure Cloud Shell.

## <a name="use-databricks-cli"></a>Utilizar tijolos de dados CLI

Agora pode começar a utilizar o DATABricks CLI. Por exemplo, execute o seguinte comando para listar todos os clusters databrick que você tem no seu espaço de trabalho.

    databricks clusters list

Também pode utilizar o seguinte comando para aceder ao sistema de ficheiros Databricks (DBFS).

    databricks fs ls


Para obter uma referência completa nos comandos, consulte [Databricks CLI](/azure/databricks/dev-tools/databricks-cli).


## <a name="next-steps"></a>Passos seguintes

* Para saber mais sobre o Azure CLI, consulte a [visão geral do Azure CLI](../cloud-shell/overview.md)
* Para ver uma lista de comandos para o Azure CLI, consulte a [referência do Azure CLI](https://docs.microsoft.com/cli/azure/reference-index?view=azure-cli-latest)
* Para ver uma lista de comandos para Databricks CLI, consulte [Databricks CLI](/azure/databricks/dev-tools/databricks-cli)


