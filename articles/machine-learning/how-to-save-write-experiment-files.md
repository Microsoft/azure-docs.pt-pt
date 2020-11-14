---
title: Onde guardar & escrever ficheiros de experiências
titleSuffix: Azure Machine Learning
description: Saiba onde guardar os seus ficheiros de entrada e saída para evitar erros de limitação de armazenamento e experiência de latência.
services: machine-learning
author: rastala
ms.author: roastala
manager: danielsc
ms.reviewer: nibaccam
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to
ms.date: 03/10/2020
ms.openlocfilehash: 49e1e9efbd6f59bd037a8033f83836bf7fc71c43
ms.sourcegitcommit: 9826fb9575dcc1d49f16dd8c7794c7b471bd3109
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/14/2020
ms.locfileid: "94630333"
---
# <a name="where-to-save-and-write-files-for-azure-machine-learning-experiments"></a>Onde guardar e escrever ficheiros para experiências de Aprendizagem automática Azure


Neste artigo, você aprende onde guardar ficheiros de entrada e onde escrever ficheiros de saída das suas experiências para evitar erros de limite de armazenamento e experiência de latência.

Ao lançar treinos com um [alvo de computação,](concept-compute-target.md)são isolados de ambientes externos. O objetivo deste desenho é garantir a reprodutibilidade e portabilidade da experiência. Se executar o mesmo script duas vezes, no mesmo ou noutro alvo de computação, receberá os mesmos resultados. Com este design, você pode tratar os alvos computacional como recursos computatórios apátridas, cada um não tem nenhuma afinidade com os trabalhos que estão executando depois de terminados.

## <a name="where-to-save-input-files"></a>Onde guardar ficheiros de entrada

Antes de iniciar uma experiência num alvo de computação ou na sua máquina local, tem de garantir que os ficheiros necessários estão disponíveis para esse alvo de computação, como ficheiros de dependência e ficheiros de dados que o seu código necessita de executar.

A Azure Machine Learning executa scripts de formação copiando todo o diretório de origem. Se tiver dados sensíveis que não pretende fazer o upload, utilize um [ficheiro .ignore](how-to-save-write-experiment-files.md#storage-limits-of-experiment-snapshots) ou não o inclua no diretório de origem . Em vez disso, aceda aos seus dados através de uma [datastore](/python/api/azureml-core/azureml.data?preserve-view=true&view=azure-ml-py).

O limite de armazenamento para instantâneos de experimentação é 300 MB e/ou 2000 ficheiros.

Por esta razão, recomendamos:

* **Armazenar os seus ficheiros numa loja [de dados](/python/api/azureml-core/azureml.data?preserve-view=true&view=azure-ml-py)Azure Machine Learning .** Isto previne problemas de latência experimentais, e tem as vantagens de aceder a dados a partir de um alvo de computação remota, o que significa que a autenticação e montagem são geridas pela Azure Machine Learning. Saiba mais sobre especificar uma datastore como diretório de origem e fazer o upload de ficheiros para a sua loja de dados nos dados do Access a partir do artigo [das suas datastores.](how-to-access-data.md)

* **Se necessitar apenas de alguns ficheiros de dados e scripts de dependência e não puder utilizar uma loja de dados,** coloque os ficheiros no mesmo diretório de pastas que o seu script de treino. Especifique esta pasta como a sua `source_directory` diretamente no seu script de treino ou no código que chama o seu script de treino.

<a name="limits"></a>

### <a name="storage-limits-of-experiment-snapshots"></a>Limites de armazenamento de instantâneos experimentais

Para experiências, a Azure Machine Learning faz automaticamente uma imagem do seu código com base no diretório que sugere quando configurar a execução. Isto tem um limite total de 300 MB e/ou 2000 ficheiros. Se exceder este limite, verá o seguinte erro:

```Python
While attempting to take snapshot of .
Your total snapshot size exceeds the limit of 300.0 MB
```

Para resolver este erro, guarde os seus ficheiros de experiência numa data-tore. Se não puder utilizar uma loja de dados, a tabela abaixo oferece possíveis soluções alternativas.

Descrição da &nbsp; experiência|Solução limite de armazenamento
---|---
Menos de 2000 ficheiros & não podem usar uma loja de dados| Anular limite de tamanho de instantâneo com <br> `azureml._restclient.snapshots_client.SNAPSHOT_MAX_SIZE_BYTES = 'insert_desired_size'`<br> Isto pode demorar vários minutos dependendo do número e tamanho dos ficheiros.
Deve usar diretório de script específico| [!INCLUDE [amlinclude-info](../../includes/machine-learning-amlignore-gitignore.md)]
Pipeline|Use um subdiretório diferente para cada passo
Blocos de notas do Jupyter| Crie um `.amlignore` ficheiro ou mova o seu caderno para um novo, vazio, subdirecional e volte a executar o seu código.

## <a name="where-to-write-files"></a>Onde escrever ficheiros

Devido ao isolamento das experiências de treino, as alterações aos ficheiros que ocorrem durante as execuções não são necessariamente persistiu fora do seu ambiente. Se o seu script modificar os ficheiros locais para calcular, as alterações não são persistiu para a sua próxima experiência e não são propagadas automaticamente para a máquina do cliente. Portanto, as alterações feitas durante a primeira experiência não afetam e não devem afetar as na segunda.

Ao escrever alterações, recomendamos escrever ficheiros para uma loja de dados Azure Machine Learning. Consulte [os dados do Access a partir das suas lojas de dados.](how-to-access-data.md)

Se não necessitar de uma loja de dados, escreva ficheiros para a `./outputs` pasta e/ou. `./logs`

>[!Important]
> Duas *pastas, saídas* e *troncos,* recebem tratamento especial pela Azure Machine Learning. Durante o treino, quando escreve ficheiros `./outputs` e `./logs` pastas, os ficheiros serão automaticamente enviados para o seu histórico de execução, para que tenha acesso aos mesmos assim que a sua execução estiver concluída.

* **Para a saída, como mensagens de estado ou resultados de pontuação,** escreva ficheiros para a `./outputs` pasta, pelo que são persistidos como artefactos na história da execução. Tenha em atenção o número e o tamanho dos ficheiros escritos nesta pasta, pois a latência pode ocorrer quando o conteúdo é carregado para executar o histórico. Se a latência for uma preocupação, recomenda-se escrever ficheiros a uma datastore.

* **Para guardar o ficheiro escrito como registos no histórico de execução,** escreva ficheiros para `./logs` pasta. Os registos são carregados em tempo real, pelo que este método é adequado para transmitir atualizações ao vivo a partir de uma execução remota.

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre [o acesso aos dados das suas datastores.](how-to-access-data.md)

* Saiba mais sobre [criar metas de computação para formação e implementação de modelos](how-to-create-attach-compute-studio.md)