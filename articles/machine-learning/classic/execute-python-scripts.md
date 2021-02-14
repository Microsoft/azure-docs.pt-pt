---
title: 'Estúdio ML (clássico): Executar scripts Python - Azure'
description: Aprenda a usar o módulo Execut Python Script para usar o código Python em experiências e serviços web do Machine Learning Studio (clássicos).
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: how-to
author: likebupt
ms.author: keli19
ms.custom: devx-track-python, previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 03/12/2019
ms.openlocfilehash: 374589212b230e6b3ce0abcadbad8aa3eb7271fc
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/14/2021
ms.locfileid: "100519988"
---
# <a name="execute-python-machine-learning-scripts-in-azure-machine-learning-studio-classic"></a>Execute scripts de aprendizagem de máquina python no Azure Machine Learning Studio (clássico)

**APLICA-SE A:** ![ Aplica-se a. ](../../../includes/media/aml-applies-to-skus/yes.png) Machine Learning Studio (clássico) ![ Não se aplica a.](../../../includes/media/aml-applies-to-skus/no.png)[ Aprendizagem de Máquinas Azure](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)  


Python é uma ferramenta valiosa no baú de ferramentas de muitos cientistas de dados. É usado em todas as fases dos fluxos típicos de aprendizagem automática, incluindo a exploração de dados, extração de recursos, treino de modelos e validação, e implementação.

Este artigo descreve como pode usar o módulo Execut Python Script para usar o código Python no seu Azure Machine Learning Studio (clássico) experiências e serviços web.

## <a name="using-the-execute-python-script-module"></a>Utilizando o módulo de script de Python executar

A interface primária para Python em Studio (clássico) é através do módulo [Execut Python Script.][execute-python-script] Aceita até três entradas e produz até duas saídas, semelhantes ao módulo [Execut R Script.][execute-r-script] O código python é introduzido na caixa de parâmetros através de uma função de ponto de entrada especialmente denominada `azureml_main` .

![Execute o módulo de script python](./media/execute-python-scripts/execute-machine-learning-python-scripts-module.png)

![Código python de amostra na caixa de parâmetros do módulo](./media/execute-python-scripts/embedded-machine-learning-python-script.png)

### <a name="input-parameters"></a>Parâmetros de entrada

As entradas para o módulo Python são expostas como DataFrames pandas. A `azureml_main` função aceita até dois DataFrames pandas opcionais como parâmetros.

O mapeamento entre portas de entrada e parâmetros de função é posicional:

- A primeira porta de entrada ligada está mapeada para o primeiro parâmetro da função.
- A segunda entrada (se ligada) é mapeada para o segundo parâmetro da função.
- A terceira entrada é usada para [importar módulos Python adicionais.](#import-modules)

Semântica mais detalhada de como as portas de entrada são mapeadas para parâmetros da `azureml_main` função são mostradas abaixo.

![Tabela de configurações de porta de entrada e assinatura python resultante](./media/execute-python-scripts/python-script-inputs-mapped-to-parameters.png)

### <a name="output-return-values"></a>Valores de retorno da saída

A `azureml_main` função deve devolver um único DataFrame de Pandas embalado numa [sequência](https://docs.python.org/2/c-api/sequence.html) python, como um tuple, lista ou matriz numPy. O primeiro elemento desta sequência é devolvido à primeira porta de saída do módulo. A segunda porta de saída do módulo é utilizada para [visualizações](#visualizations) e não requer um valor de devolução. Este esquema é apresentado abaixo.

![Mapear portas de entrada para parâmetros e valor de retorno para a porta de saída](./media/execute-python-scripts/map-of-python-script-inputs-outputs.png)

## <a name="translation-of-input-and-output-data-types"></a>Tradução dos tipos de dados de entrada e saída

Os conjuntos de dados do estúdio não são os mesmos que os DataFrames do Panda. Como resultado, os conjuntos de dados de entrada em Studio (clássico) são convertidos para Pandas DataFrame, e os DataFrames de saída são convertidos de volta para conjuntos de dados studio (clássicos). Durante este processo de conversão, são também realizadas as seguintes traduções:

 **Tipo de dado python** | **Procedimento de tradução de estúdio** |
| --- | --- |
| Cordas e numéricos| Traduzido como é |
| Pandas 'NA' | Traduzido como "Valor em falta" |
| Vetores de índice | Sem suporte* |
| Nomes de colunas não-cordas | Chamada `str` em nomes de colunas |
| Nomes de colunas duplicados | Adicione sufixo numérico: (1), (2), (3) e assim por diante.

**Todos os quadros de dados de entrada na função Python têm sempre um índice numérico de 64 bits de 0 para o número de linhas menos 1*

## <a name="importing-existing-python-script-modules"></a><a id="import-modules"></a>Importar módulos de script python existentes

O backend usado para executar Python é baseado em [Anaconda,](https://www.anaconda.com/distribution/)uma distribuição científica amplamente usada python. Vem com cerca de 200 dos pacotes Python mais comuns usados em cargas de trabalho centradas em dados. O Studio (clássico) não suporta atualmente a utilização de sistemas de gestão de pacotes como pip ou Conda para instalar e gerir bibliotecas externas.  Se encontrar a necessidade de incorporar bibliotecas adicionais, utilize o seguinte cenário como guia.

Um caso de uso comum é incorporar scripts Python existentes em experiências studio (clássicas). O módulo [Execut Python Script][execute-python-script] aceita um ficheiro zip contendo módulos Python na terceira porta de entrada. O ficheiro é desapertado pela estrutura de execução em tempo de execução e os conteúdos são adicionados ao caminho da biblioteca do intérprete Python. A `azureml_main` função ponto de entrada pode então importar estes módulos diretamente. 

Como exemplo, considere o ficheiro Hello.py que contém uma função simples "Olá, Mundo".

![Função definida pelo utilizador em Hello.py ficheiro](./media/execute-python-scripts/figure4.png)

Em seguida, criamos um ficheiro Hello.zip que contém Hello.py:

![Ficheiro zip contendo código Python definido pelo utilizador](./media/execute-python-scripts/figure5.png)

Faça o upload do ficheiro zip como um conjunto de dados para o Studio (clássico). Em seguida, crie e execute uma experiência que utilize o código Python no ficheiro Hello.zip, fixando-o à terceira porta de entrada do módulo **executante python script,** como mostrado na imagem seguinte.

![Experimente a experiência com Hello.zip como entrada para um módulo de script de Python executo](./media/execute-python-scripts/figure6a.png)

![Código Python definido pelo utilizador carregado como um ficheiro zip](./media/execute-python-scripts/figure6b.png)

A saída do módulo mostra que o ficheiro zip foi desembalado e que a função `print_hello` foi executada.

![Saída do módulo mostrando a função definida pelo utilizador](./media/execute-python-scripts/figure7.png)

## <a name="accessing-azure-storage-blobs"></a>Aceder a blobs de armazenamento Azure

Pode aceder aos dados armazenados numa conta de Armazenamento Azure Blob utilizando estes passos:

1. Descarregue o [pacote de armazenamento Azure Blob para Python](https://azuremlpackagesupport.blob.core.windows.net/python/azure.zip) localmente.
1. Faça o upload do ficheiro zip para o seu espaço de trabalho Studio (clássico) como um conjunto de dados.
1. Crie o seu objeto BlobService com `protocol='http'`

```
from azure.storage.blob import BlockBlobService

# Create the BlockBlockService that is used to call the Blob service for the storage account
block_blob_service = BlockBlobService(account_name='account_name', account_key='account_key', protocol='http')
```

1. Desativar **transferência segura necessária** no separador de **definição de configuração** de configuração de armazenamento

![Transfer Secure de Desativação exigido no portal Azure](./media/execute-python-scripts/disable-secure-transfer-required.png)

## <a name="operationalizing-python-scripts"></a>Operacionalizar scripts python

Quaisquer módulos [de Script Python executados][execute-python-script] utilizados numa experiência de pontuação são chamados quando publicados como um serviço web. Por exemplo, a imagem abaixo mostra uma experiência de pontuação que contém o código para avaliar uma única expressão Python.

![Espaço de trabalho de estúdio para um serviço web](./media/execute-python-scripts/figure3a.png)

![Expressão de Pandas python](./media/execute-python-scripts/python-script-with-python-pandas.png)

Um serviço web criado a partir desta experiência tomaria as seguintes ações:

1. Tome uma expressão Python como entrada (como uma corda)
1. Envie a expressão Python ao intérprete Python
1. Devolve uma tabela que contém tanto a expressão como o resultado avaliado.

## <a name="working-with-visualizations"></a><a id="visualizations"></a>Trabalhar com visualizações

Os enredos criados usando o MatplotLib podem ser devolvidos pelo [Executo Python Script][execute-python-script]. No entanto, os enredos não são redirecionados automaticamente para as imagens como são quando utilizam R. Assim, o utilizador deve guardar explicitamente quaisquer parcelas para ficheiros PNG.

Para gerar imagens de MatplotLib, deve dar os seguintes passos:

1. Mude o backend para "AGG" a partir do renderizador qt predefinido.
1. Criar um novo objeto figura.
1. Obter o eixo e gerar todos os enredos nele.
1. Guarde a figura para um ficheiro PNG.

Este processo é ilustrado nas seguintes imagens que criam uma matriz de enredo de dispersão usando a função scatter_matrix em Pandas.

![Código para salvar figuras de MatplotLib para imagens](./media/execute-python-scripts/figure-v1-8.png)

![Clique em visualizar num módulo de script de Python executar para ver as figuras](./media/execute-python-scripts/figure-v2-9a.png)

![Visualizar parcelas para uma experiência de amostra usando código Python](./media/execute-python-scripts/figure-v2-9b.png)

É possível devolver várias figuras guardando-as em imagens diferentes. O tempo de execução do estúdio (clássico) capta todas as imagens e concatena-as para visualização.

## <a name="advanced-examples"></a>Exemplos avançados

O ambiente Anaconda instalado em Studio (clássico) contém pacotes comuns como NumPy, SciPy e Scikits-Learn. Estes pacotes podem ser eficazmente utilizados para o processamento de dados num gasoduto de aprendizagem automática.

Por exemplo, a seguinte experiência e script ilustram o uso de alunos do conjunto em Scikits-Learn para calcular pontuações de importância de recurso para um conjunto de dados. As pontuações podem ser usadas para executar a seleção de recursos supervisionados antes de serem alimentadas para outro modelo.

Aqui está a função Python usada para calcular as pontuações de importância e encomendar as funcionalidades com base nas pontuações:

![Função para classificar funcionalidades por pontuações](./media/execute-python-scripts/figure8.png)

A seguinte experiência calcula e devolve as pontuações importantes de características no conjunto de dados "Pima Indian Diabetes" no Azure Machine Learning Studio (clássico):

![Experimente classificar as funcionalidades no conjunto de dados da Diabetes Indiana Pima usando Python](./media/execute-python-scripts/figure9a.png)

![Visualização da saída do módulo de script execute python](./media/execute-python-scripts/figure9b.png)

## <a name="limitations"></a>Limitações

O módulo [de script de Python executo][execute-python-script] tem atualmente as seguintes limitações:

### <a name="sandboxed-execution"></a>Execução de sandboxed

O tempo de funcionamento python é atualmente sandboxed e não permite o acesso à rede ou ao sistema de ficheiros local de forma persistente. Todos os ficheiros guardados localmente são isolados e eliminados assim que o módulo terminar. O código Python não pode aceder à maioria dos diretórios na máquina em que funciona, sendo a exceção o diretório atual e as suas subdireções.

### <a name="lack-of-sophisticated-development-and-debugging-support"></a>Falta de desenvolvimento sofisticado e depuragem de apoio

O módulo Python não suporta atualmente funcionalidades IDE como intellisense e depuragem. Além disso, se o módulo falhar no tempo de execução, o vestígio completo da pilha python está disponível. Mas deve ser visto no registo de saída para o módulo. Atualmente recomendamos que desenvolva e depure scripts Python num ambiente como iPython e, em seguida, importe o código para o módulo.

### <a name="single-data-frame-output"></a>Saída de quadro de dados único

O ponto de entrada Python só é permitido devolver um único quadro de dados como saída. Atualmente, não é possível devolver objetos pitões arbitrários, como modelos treinados diretamente de volta ao tempo de execução do Estúdio (clássico). Tal como [executar o Script R][execute-r-script], que tem a mesma limitação, é possível em muitos casos colocar objetos em um byte array e, em seguida, devolvê-lo dentro de uma moldura de dados.

### <a name="inability-to-customize-python-installation"></a>Incapacidade de personalizar a instalação Python

Atualmente, a única maneira de adicionar módulos Python personalizados é através do mecanismo de ficheiro zip descrito anteriormente. Embora isto seja viável para pequenos módulos, é complicado para grandes módulos (especialmente módulos com DLLs nativos) ou um grande número de módulos.

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações, consulte o [Centro para Programadores do Python](https://azure.microsoft.com/develop/python/).

<!-- Module References -->
[execute-python-script]: /azure/machine-learning/studio-module-reference/execute-python-script
[execute-r-script]: /azure/machine-learning/studio-module-reference/execute-r-script