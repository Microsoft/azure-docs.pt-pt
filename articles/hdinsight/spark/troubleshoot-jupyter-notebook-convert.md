---
title: Incapaz de criar o Jupyter Notebook em Azure HDInsight
description: Conheça as etapas de resolução de problemas e possíveis resoluções para problemas ao interagir com os clusters Azure HDInsight.
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 02/11/2020
ms.openlocfilehash: d5f6612e22522452efb5eecd14c6c825bf29e6d5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98938674"
---
# <a name="unable-to-create-jupyter-notebook-in-azure-hdinsight"></a>Incapaz de criar o Jupyter Notebook em Azure HDInsight

Este artigo descreve etapas de resolução de problemas e possíveis resoluções para problemas ao interagir com clusters Azure HDInsight.

## <a name="issue"></a>Problema

Ao iniciar um Caderno Jupyter, recebe uma mensagem de erro que contém:

```error
Cannot convert notebook to v5 because that version doesn't exist
```

## <a name="cause"></a>Causa

Uma versão desajustada.

## <a name="resolution"></a>Resolução

1. Utilize [o comando ssh](../hdinsight-hadoop-linux-use-ssh-unix.md) para ligar ao seu cluster. Edite o comando abaixo substituindo o CLUSTERNAME pelo nome do seu cluster e, em seguida, insira o comando:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Abra `_version.py` executando o seguinte comando:

    ```bash
    sudo nano /usr/bin/anaconda/lib/python2.7/site-packages/nbformat/_version.py
    ```

1. Alterar **de 5** para **4** para que a linha modificada apareça da seguinte forma:

    ```python
    version_info = (4, 0, 3)
    ```

    Guardar alterações ao introduzir **Ctrl + X,** **Y,** **Enter**.

1. A partir de um navegador web, navegue `https://CLUSTERNAME.azurehdinsight.net/#/main/services/JUPYTER` para, onde `CLUSTERNAME` está o nome do seu cluster.

1. Reinicie o serviço Jupyter.

## <a name="next-steps"></a>Passos seguintes

Se não viu o seu problema ou não conseguir resolver o seu problema, visite um dos seguintes canais para obter mais apoio:

* Obtenha respostas de especialistas da Azure através do [Apoio Comunitário Azure.](https://azure.microsoft.com/support/community/)

* Conecte-se com [@AzureSupport](https://twitter.com/azuresupport) - a conta oficial do Microsoft Azure para melhorar a experiência do cliente. Ligação da comunidade Azure aos recursos certos: respostas, apoio e especialistas.

* Se precisar de mais ajuda, pode submeter um pedido de apoio do [portal Azure.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) Selecione **Suporte** na barra de menu ou abra o hub **de suporte Help +.** Para obter informações mais [detalhadas, reveja como criar um pedido de suporte Azure](../../azure-portal/supportability/how-to-create-azure-support-request.md). O acesso à Gestão de Subscrições e suporte à faturação está incluído na subscrição do Microsoft Azure, e o Suporte Técnico é fornecido através de um dos Planos de [Suporte Azure](https://azure.microsoft.com/support/plans/).
