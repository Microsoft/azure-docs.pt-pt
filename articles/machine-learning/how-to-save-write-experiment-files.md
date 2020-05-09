---
title: Onde guardar & escrever ficheiros de experiências
titleSuffix: Azure Machine Learning
description: Saiba onde guardar os ficheiros de entrada da sua experiência e onde escrever ficheiros de saída para evitar erros de limitação de armazenamento e latência de experimentar.
services: machine-learning
author: rastala
ms.author: roastala
manager: danielsc
ms.reviewer: nibaccam
ms.service: machine-learning
ms.subservice: core
ms.workload: data-services
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: 0938888b7343b441725faace7a5f20d8f50674c8
ms.sourcegitcommit: f57297af0ea729ab76081c98da2243d6b1f6fa63
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/06/2020
ms.locfileid: "82872058"
---
# <a name="where-to-save-and-write-files-for-azure-machine-learning-experiments"></a>Onde guardar e escrever ficheiros para experiências de Aprendizagem automática de Azure
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Neste artigo, você aprende onde guardar ficheiros de entrada e onde escrever ficheiros de saída das suas experiências para evitar erros de limite de armazenamento e latência de experiências.

Ao lançar em [funcionamento](how-to-set-up-training-targets.md)de formação num alvo de cálculo, estão isolados de ambientes externos. O objetivo deste desenho é garantir a reprodutibilidade e a portabilidade da experiência. Se executar o mesmo guião duas vezes, no mesmo ou noutro alvo de computação, receberá os mesmos resultados. Com este design, você pode tratar os alvos da computação como recursos de computação apátridas, cada um não tendo nenhuma afinidade com os trabalhos que estão a funcionar após o seu fim.

## <a name="where-to-save-input-files"></a>Onde guardar ficheiros de entrada

Antes de iniciar uma experiência num alvo de cálculo ou na sua máquina local, deve certificar-se de que os ficheiros necessários estão disponíveis para esse alvo de cálculo, como ficheiros de dependência e ficheiros de dados que o seu código precisa de executar.

O Azure Machine Learning executa scripts de treino copiando toda a pasta do script para o contexto da computação-alvo e, em seguida, tira uma foto. O limite de armazenamento para instantâneos de experimentação é 300 MB e/ou 2000 ficheiros.

Por esta razão, recomendamos:

* **Armazenar os seus ficheiros numa loja de [dados](https://docs.microsoft.com/python/api/azureml-core/azureml.data?view=azure-ml-py)Azure Machine Learning .** Isto evita problemas de latência de experiências, e tem as vantagens de aceder a dados de um alvo de computação remota, o que significa que a autenticação e montagem são geridas pela Azure Machine Learning. Saiba mais sobre especificar uma loja de dados como o seu diretório de origem e o upload de ficheiros para a sua loja de dados nos dados do Acesso a partir do artigo [Datastores.](how-to-access-data.md)

* **Se necessitar apenas de alguns ficheiros** de dados e scripts de dependência e não puder utilizar uma loja de dados, coloque os ficheiros no mesmo diretório de pastas que o seu script de treino. Especifique `source_directory` esta pasta como a sua diretamente no seu script de treino, ou no código que chama o seu script de treino.

<a name="limits"></a>

### <a name="storage-limits-of-experiment-snapshots"></a>Limites de armazenamento de instantâneos de experiência

Para experiências, o Azure Machine Learning faz automaticamente uma imagem do seu código com base no diretório que sugere quando configura a execução. Isto tem um limite total de 300 MB e/ou 2000 ficheiros. Se ultrapassar este limite, verá o seguinte erro:

```Python
While attempting to take snapshot of .
Your total snapshot size exceeds the limit of 300.0 MB
```

Para resolver este erro, guarde os seus ficheiros de experiência numa loja de dados. Se não puder utilizar uma loja de dados, a tabela abaixo oferece possíveis soluções alternativas.

Descrição da experiência&nbsp;|Solução limite de armazenamento
---|---
Menos de 2000 ficheiros & não podem usar uma loja de dados| Limite de tamanho de instantâneo de sobreposição com <br> `azureml._restclient.snapshots_client.SNAPSHOT_MAX_SIZE_BYTES = 'insert_desired_size'`<br> Isto pode demorar vários minutos dependendo do número e tamanho dos ficheiros.
Deve usar o diretório de script específico| [!INCLUDE [amlinclude-info](../../includes/machine-learning-amlignore-gitignore.md)]
Pipeline|Use um subdiretório diferente para cada passo
Jupyter Notebooks| Crie `.amlignore` um ficheiro ou mude o seu caderno para um novo, vazio, subdiretório e volte a executar o seu código.

## <a name="where-to-write-files"></a>Onde escrever ficheiros

Devido ao isolamento das experiências de treino, as alterações aos ficheiros que acontecem durante as corridas não são necessariamente persistidas fora do seu ambiente. Se o seu script modificar os ficheiros locais para calcular, as alterações não são persistidas para a sua próxima experiência, e não são propagadas automaticamente para a máquina do cliente. Portanto, as mudanças feitas durante a primeira experiência não afetam e não devem afetar as da segunda.

Ao escrever alterações, recomendamos escrever ficheiros para uma loja de dados Azure Machine Learning. Consulte [os dados de Acesso das suas lojas de dados](how-to-access-data.md).

Se não necessitar de uma loja de `./outputs` dados, `./logs` escreva ficheiros para a e/ou pasta.

>[!Important]
> Duas pastas, *saídas* e *registos,* recebem tratamento especial pela Azure Machine Learning. Durante o treino, quando`./outputs` `./logs` escreve ficheiros e pastas, os ficheiros serão automaticamente enviados para o seu histórico de execução, de modo a que tenha acesso aos mesmos assim que a sua execução estiver concluída.

* **Para saídas como mensagens de estado ou resultados de pontuação,** escreva ficheiros para a `./outputs` pasta, para que sejam persistidos como artefactos na história da execução. Esteja atento ao número e tamanho dos ficheiros escritos nesta pasta, pois pode ocorrer latência quando o conteúdo é carregado para executar o histórico. Se a latência for uma preocupação, recomenda-se escrever ficheiros para uma loja de dados.

* **Para guardar ficheiros escritos como registos no histórico de execução,** escreva ficheiros para `./logs` pasta. Os registos são carregados em tempo real, por isso este método é adequado para transmitir atualizações ao vivo a partir de uma execução remota.

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre [o acesso a dados das suas lojas de dados.](how-to-access-data.md)

* Saiba mais sobre [como configurar alvos de treino](how-to-set-up-training-targets.md).
