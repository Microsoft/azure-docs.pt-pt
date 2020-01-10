---
title: Onde salvar & gravar arquivos de teste
titleSuffix: Azure Machine Learning
description: Saiba onde salvar seus arquivos de entrada de experimento e onde gravar arquivos de saída para evitar erros de limitação de armazenamento e latência de experimento.
services: machine-learning
author: rastala
ms.author: roastala
manager: danielsc
ms.reviewer: nibaccam
ms.service: machine-learning
ms.subservice: core
ms.workload: data-services
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 197e93946a52303f312912e4ebd2487f8c1360b2
ms.sourcegitcommit: ce4a99b493f8cf2d2fd4e29d9ba92f5f942a754c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/28/2019
ms.locfileid: "75536736"
---
# <a name="where-to-save-and-write-files-for-azure-machine-learning-experiments"></a>Onde salvar e gravar arquivos para experimentos Azure Machine Learnings
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Neste artigo, você aprende onde salvar arquivos de entrada e onde gravar arquivos de saída de seus experimentos para evitar erros de limite de armazenamento e latência de experimento.

Ao iniciar o treinamento é executado em um [destino de computação](how-to-set-up-training-targets.md), eles são isolados de ambientes externos. A finalidade desse design é garantir a reprodução e a portabilidade do experimento. Se você executar o mesmo script duas vezes, no mesmo destino de computação ou em outro, você receberá os mesmos resultados. Com esse design, você pode tratar destinos de computação como recursos de computação sem monitoração de estado, cada um sem afinidade com os trabalhos em execução após a conclusão.

## <a name="where-to-save-input-files"></a>Onde salvar arquivos de entrada

Antes de poder iniciar um experimento em um destino de computação ou em seu computador local, você deve garantir que os arquivos necessários estejam disponíveis para esse destino de computação, como arquivos de dependência e arquivos de dados que seu código precisa executar.

Azure Machine Learning executa scripts de treinamento copiando a pasta de script inteira para o contexto de computação de destino e, em seguida, tira um instantâneo. O limite de armazenamento para instantâneos de experimentação é 300 MB e/ou 2000 ficheiros.

Por esse motivo, recomendamos:

* **Armazenando seus arquivos em um [repositório](https://docs.microsoft.com/python/api/azureml-core/azureml.data?view=azure-ml-py)de Azure Machine Learning de armazenamento.** Isso evita problemas de latência de experimento e tem as vantagens de acessar dados de um destino de computação remoto, o que significa que a autenticação e a montagem são gerenciadas pelo Azure Machine Learning. Saiba mais sobre como especificar um armazenamento de dados como seu diretório de origem e carregar arquivos em seu repositório de dados no artigo [Access Data from the datastores](how-to-access-data.md) .

* **Se você precisar apenas de alguns arquivos de dados e scripts de dependência e não puder usar um datastore,** Coloque os arquivos no mesmo diretório de pasta que o script de treinamento. Especifique essa pasta como seu `source_directory` diretamente no script de treinamento ou no código que chama seu script de treinamento.

<a name="limits"></a>

### <a name="storage-limits-of-experiment-snapshots"></a>Limites de armazenamento de instantâneos de teste

Para experimentos, Azure Machine Learning automaticamente faz um instantâneo de teste do seu código com base no diretório que você sugere ao configurar a execução. Isso tem um limite total de 300 MB e/ou 2000 arquivos. Se esse limite for excedido, você verá o seguinte erro:

```Python
While attempting to take snapshot of .
Your total snapshot size exceeds the limit of 300.0 MB
```

Para resolver esse erro, armazene seus arquivos de teste em um repositório de armazenamento. Se você não pode usar um armazenamento de datastore, a tabela abaixo oferece possíveis soluções alternativas.

Descrição do experimento&nbsp;|Solução de limite de armazenamento
---|---
Menos de 2000 arquivos & não podem usar um repositório de armazenamento| Substituir o limite de tamanho do instantâneo por <br> `azureml._restclient.snapshots_client.SNAPSHOT_MAX_SIZE_BYTES = 'insert_desired_size'`<br> Isso pode levar vários minutos, dependendo do número e do tamanho dos arquivos.
Deve usar um diretório de script específico| Faça um arquivo de `.amlignore` para excluir arquivos de seu instantâneo de experimento que não fazem parte do código-fonte. Adicione os nomes de arquivos ao arquivo de `.amlignore` e coloque-o no mesmo diretório que o script de treinamento. O arquivo de `.amlignore` usa a mesma [sintaxe e padrões](https://git-scm.com/docs/gitignore) que um arquivo de `.gitignore`.
Pipeline|Usar um subdiretório diferente para cada etapa
Jupyter Notebooks| Crie um arquivo de `.amlignore` ou mova o bloco de anotações para um subdiretório novo, vazio e execute o código novamente.

## <a name="where-to-write-files"></a>Onde gravar arquivos

Devido ao isolamento de experimentos de treinamento, as alterações nos arquivos que ocorrem durante as execuções não são necessariamente mantidas fora do seu ambiente. Se o seu script modificar os arquivos locais para computar, as alterações não serão persistidas para a próxima execução de experimento e não serão propagadas novamente para o computador cliente automaticamente. Portanto, as alterações feitas durante o primeiro experimento são executadas e não devem afetar as do segundo.

Ao escrever alterações, é recomendável gravar arquivos em um repositório de armazenamento Azure Machine Learning. Confira [acessar dados de seus armazenamentos](how-to-access-data.md).

Se você não precisar de um repositório de armazenamento, grave arquivos na pasta `./outputs` e/ou `./logs`.

>[!Important]
> Duas pastas, *saídas* e *logs*, recebem tratamento especial por Azure Machine Learning. Durante o treinamento, quando você grava arquivos em pastas`./outputs` e`./logs`, os arquivos serão carregados automaticamente no seu histórico de execução, para que você tenha acesso a eles quando a execução for concluída.

* **Para saída como mensagens de status ou resultados de pontuação,** grave arquivos na pasta `./outputs`, para que eles sejam persistidos como artefatos no histórico de execução. Lembre-se do número e do tamanho dos arquivos gravados nessa pasta, uma vez que a latência pode ocorrer quando o conteúdo é carregado para o histórico de execução. Se a latência for uma preocupação, é recomendável gravar arquivos em um repositório de armazenamento.

* **Para salvar o arquivo gravado como logs no histórico de execuções,** grave arquivos na pasta `./logs`. Os logs são carregados em tempo real, portanto, esse método é adequado para streaming de atualizações dinâmicas de uma execução remota.

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre como [acessar os dados de seus armazenamentos](how-to-access-data.md).

* Saiba mais sobre [como configurar metas de treinamento](how-to-set-up-training-targets.md).
