---
title: 'Importar da URL da Web via HTTP: referência de módulo'
titleSuffix: Azure Machine Learning service
description: Saiba como usar o módulo importar da URL da Web via HTTP no serviço Azure Machine Learning para ler dados de uma página da Web pública para uso em um pipeline do Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ms.openlocfilehash: e2521dabdab8e9365019f35514f2d8d235c9c014
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/21/2019
ms.locfileid: "72693127"
---
# <a name="import-from-web-url-via-http-module"></a>Importar da URL da Web via módulo HTTP

Este artigo descreve um módulo da interface visual (visualização) para Azure Machine Learning serviço.

Use este módulo para ler dados de uma página da Web pública para uso em um pipeline do Machine Learning.

As seguintes restrições se aplicam aos dados publicados em uma página da Web:

- Os dados devem estar em um dos formatos com suporte: CSV, TSV, ARFF ou SvmLight. Outros dados causarão erros.
- Nenhuma autenticação é necessária ou tem suporte. Os dados devem estar publicamente disponíveis. 

Há duas maneiras de obter dados: Use o assistente para configurar a fonte de dados ou configure-a manualmente.

## <a name="use-the-data-import-wizard"></a>Usar o assistente de importação de dados

1. Adicione o módulo **importar dados** ao seu pipeline. Você pode encontrar o módulo na interface, na categoria **entrada e saída de dados** .

2. Clique em **Iniciar assistente de importação de dados** e selecione URL da Web via http.

3. Cole na URL e selecione um formato de dados.

4. Quando a configuração estiver concluída.

Para editar uma conexão de dados existente, inicie o assistente novamente. O assistente carrega todos os detalhes de configuração anteriores para que você não precise recomeçar do zero

## <a name="manually-set-properties-in-the-import-data-module"></a>Definir manualmente as propriedades no módulo importar dados

As etapas a seguir descrevem como configurar manualmente a origem da importação.

1. Adicione o módulo [importar dados](import-data.md) ao seu pipeline. Você pode encontrar o módulo na interface, na categoria **entrada e saída de dados** .

2. Para **fonte de dados**, selecione **URL da Web via http**.

3. Para **URL**, digite ou cole a URL completa da página que contém os dados que você deseja carregar.

    A URL deve incluir a URL do site e o caminho completo, com o nome e a extensão do arquivo, para a página que contém os dados a serem carregados.

    Por exemplo, a página a seguir contém o conjunto de dados íris do repositório Machine Learning da Universidade da Califórnia, Irvine:

    `https://archive.ics.uci.edu/ml/machine-learning-databases/iris/iris.data`

4. Para **formato de dados**, selecione um dos formatos de dados com suporte na lista.

    É recomendável sempre verificar os dados com antecedência para determinar o formato. A página UC Irvine usa o formato CSV. Outros formatos de dados com suporte são TSV, ARFF e SvmLight.

5. Se os dados estiverem no formato CSV ou TSV, use a opção **arquivo com linha de cabeçalho** para indicar se os dados de origem incluem ou não uma linha de cabeçalho. A linha de cabeçalho é usada para atribuir nomes de coluna.

6. Selecione as opções **usar resultados em cache** se você não esperar que os dados mudem muito, ou se quiser evitar o recarregamento dos dados cada vez que executar o pipeline.

    Quando essa opção é selecionada, o pipeline carrega os dados na primeira vez em que o módulo é executado e depois usa uma versão armazenada em cache do DataSet.

    Se você quiser recarregar o conjunto de resultados em cada iteração do conjunto de pesquisa de pipeline, desmarque a opção **usar resultados em cache** . Os resultados também serão recarregados se houver qualquer alteração nos parâmetros dos [dados de importação](import-data.md).

7. Executar o pipeline.

## <a name="results"></a>Resultados

Ao concluir, clique no conjunto de dados de saída e selecione **Visualizar** para ver se os dados foram importados com êxito.


## <a name="next-steps"></a>Passos seguintes

Consulte o [conjunto de módulos disponíveis](module-reference.md) para Azure Machine Learning serviço. 