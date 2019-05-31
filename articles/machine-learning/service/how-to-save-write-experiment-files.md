---
title: Impedir a limitações de armazenamento e a latência com diretórios de entrada e saídas de experimentação
description: Neste artigo, saiba onde pretende guardar os ficheiros de entrada de experimentação e a localização para escrever ficheiros de saída para evitar erros de limitação de armazenamento e a latência de experimentação.
services: machine-learning
author: rastala
ms.author: roastala
manager: danielsc
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.topic: article
ms.date: 05/28/2019
ms.openlocfilehash: 28d8c47db8ea9c8a51bc8e9deb0a689eb0b20177
ms.sourcegitcommit: 51a7669c2d12609f54509dbd78a30eeb852009ae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/30/2019
ms.locfileid: "66392909"
---
# <a name="where-to-save-and-write-files-for-azure-machine-learning-experiments"></a>Sempre que guardar e escrever ficheiros para o Azure Machine Learning experimentações

Neste artigo, saiba onde pretende guardar os ficheiros de entrada e a localização para escrever ficheiros de saída a partir das suas experimentações para impedir que o armazenamento de limitam os erros e latência de experimentação.

Quando iniciar o treinamento é executado num [destino de computação](how-to-set-up-training-targets.md), eles são isolados dos ambientes externos. O objetivo deste design é garantir a capacidade de reprodução e a portabilidade da experimentação. Se executar o mesmo script duas vezes, sobre o mesmo ou outro destino de computação, receberá os mesmos resultados. Com esta estrutura, pode tratar destinos de computação como recursos de computação sem monitoração de estado, contendo cada sem afinidade para as tarefas em execução depois de concluir o procedimento.

## <a name="where-to-save-input-files"></a>Onde pretende guardar os ficheiros de entrada

Antes de pode iniciar uma experimentação no seu computador local ou de um destino de computação, certifique-se de que os ficheiros necessários estão disponíveis para esse destino de computação, tais como ficheiros de dependência e os arquivos de dados em que seu código precisa ser executado.

O Azure Machine Learning executa scripts de preparação ao copiar a pasta de script inteiro para o contexto de cálculo de destino e, em seguida, cria um instantâneo. O limite de armazenamento para instantâneos de experimentação é de 300 MB e/ou os ficheiros de 2000.

Por esse motivo, recomendamos:

* **Armazenar os ficheiros num Azure Machine Learning [arquivo de dados](https://docs.microsoft.com/python/api/azureml-core/azureml.data?view=azure-ml-py).** Isto impede que os problemas de latência de experimentação e possui as vantagens de acesso aos dados a partir de um destino de computação remota, o que significa que a autenticação e a montagem são geridos pelo serviço Azure Machine Learning. Saiba mais sobre a especificação de um arquivo de dados como o diretório de origem e de carregar ficheiros para o arquivo de dados no [aceder a dados de seus arquivos de dados](how-to-access-data.md) artigo.

* **Se precisar apenas de alguns arquivos de dados e scripts de dependência e não é possível utilizar um arquivo de dados,** colocar os arquivos no mesmo diretório de pasta que o script de treinamento. Especifique esta pasta como sua `source_directory` diretamente no seu script de treinamento ou no código que chama o script de treinamento.

<a name="limits"></a>

### <a name="storage-limits-of-experiment-snapshots"></a>Limites de armazenamento de instantâneos de experimentação

Para as experimentações, o Azure Machine Learning faz automaticamente um instantâneo de experimentação do seu código com base no diretório que sugerir quando configurar a execução. Isso tem um limite total de 300 MB e/ou os ficheiros de 2000. Se exceder este limite, verá o seguinte erro:

```Python
While attempting to take snapshot of .
Your total snapshot size exceeds the limit of 300.0 MB
```

Para resolver este erro, armazene os ficheiros de experiência num arquivo de dados. Se não pode usar um arquivo de dados, a tabela a seguir oferece possíveis soluções alternativas.

Experimentação&nbsp;descrição|Solução de limite de armazenamento
---|---
Menor do que 2000 ficheiros e não é possível utilizar um arquivo de dados| Substituir o limite de tamanho de instantâneo com <br> `azureml._restclient.snapshots_client.SNAPSHOT_MAX_SIZE_BYTES = 'insert_desired_size'`<br> Isto pode demorar vários minutos, consoante o número e tamanho dos ficheiros.
Tem de utilizar o diretório de script específico| Tornar um `.amlignore` ficheiro a excluir ficheiros da sua instantâneo de experimentação que não fazem parte do código-fonte. Adicionar os nomes de arquivo para o `.amlignore` de ficheiros e colocá-lo no mesmo diretório que o script de treinamento. O `.amlignore` arquivo utiliza as mesmas [sintaxe e padrões](https://git-scm.com/docs/gitignore) como um `.gitignore` ficheiro.
Pipeline|Utilizar um subdiretório diferente para cada passo
Jupyter Notebooks| Criar um `.amlignore` de ficheiros ou mover seu bloco de notas para um subdiretório de novo e vazio, e execute novamente o seu código.

## <a name="where-to-write-files"></a>Onde gravar arquivos

Devido ao isolamento de experiências de treinamento, as alterações aos ficheiros que ocorrem durante execuções de não serem necessariamente mantidas fora do seu ambiente. Se o seu script modifica os ficheiros locais para computação, as alterações não são mantidas para a experimentação seguinte executar, e eles não estiverem propagados de volta para o computador cliente automaticamente. Por conseguinte, as alterações feitas durante a primeira experiência de execução não e não devem afetar aqueles na segunda.

Ao escrever as alterações, recomendamos que a gravação de arquivos para um arquivo de dados do Azure Machine Learning. Ver [aceder a dados de seus arquivos de dados](how-to-access-data.md).

Se não necessitar de um arquivo de dados, escrever ficheiros para o `./outputs` e/ou `./logs` pasta.

>[!Important]
> Duas pastas, *produz* e *registos*, receber tratamento especial do Azure Machine Learning. Durante o treinamento, quando escreve os ficheiros para`./outputs` e`./logs` pastas, os ficheiros serão automaticamente carregar para o seu histórico de execuções, para que tenha acesso aos mesmos, uma vez concluída a execução.

* **Para saída, como mensagens de estado ou de resultados de classificação** escrever ficheiros para o `./outputs` pasta, para que estas são mantidas como artefatos no histórico de execuções. Estar atento ao número e tamanho dos arquivos de escrita a esta pasta, como latência pode ocorrer quando o conteúdo é carregado para o histórico de execuções. Se a latência é uma preocupação, gravando arquivos num arquivo de dados é recomendado.

* **Para guardar o ficheiro escrito como registos no histórico de execuções** escrever ficheiros para `./logs` pasta. Os registos são carregados em tempo real, para que este método é adequado para transmissão em fluxo em direto atualizações a partir de um remoto a executar.

## <a name="next-steps"></a>Passos Seguintes

* Saiba mais sobre [Acessando dados através de seus arquivos de dados](how-to-access-data.md).

* Saiba mais sobre [como a cópia de segurança destinos de treinamento](how-to-set-up-training-targets.md).
