---
title: 'Importar do URL de Web por meio de HTTP: Referência do módulo'
titleSuffix: Azure Machine Learning service
description: Saiba como utilizar a importação z Webové adresy URL através do módulo HTTP de serviço do Azure Machine Learning para ler dados a partir de uma página Web pública para uso numa experimentação do machine learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 2f0847e9dd90267d985b75be3c3a07ce8fae98a9
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/02/2019
ms.locfileid: "65029674"
---
# <a name="import-from-web-url-via-http-module"></a>Importar a partir do URL da Web através do módulo HTTP

Este artigo descreve um módulo da interface visual (pré-visualização) para o serviço Azure Machine Learning.

Utilize este módulo para ler dados a partir de uma página Web pública para uso numa experimentação do machine learning.

As seguintes restrições aplicam-se aos dados publicados numa página da web:

- Dados tem de ser dos formatos com suporte: CSV, TSV, ARFF ou SvmLight. Outros dados causará erros.
- Sem autenticação é necessária ou suportada. Dados tem de estar disponíveis publicamente. 

Existem duas formas de obter dados: Utilize o Assistente para configurar a origem de dados ou configurá-la manualmente.

## <a name="use-the-data-import-wizard"></a>Utilize o Assistente de importação de dados

1. Adicionar a **importar dados** módulo à sua experimentação. Pode encontrar o módulo na interface, além da **dados de entrada e saída** categoria.

2. Clique em **iniciar o Assistente para importar dados** e selecione o URL de Web por meio de HTTP.

3. Cole o URL e selecione um formato de dados.

4. Quando a configuração está concluída.

Para editar uma ligação de dados existente, inicie o assistente novamente. O assistente carrega todos os detalhes de configuração anteriores, para que não precisa começar novamente do zero

## <a name="manually-set-properties-in-the-import-data-module"></a>Definir manualmente as propriedades no módulo importar dados

Os passos seguintes descrevem como configurar manualmente a origem de importação.

1. Adicionar a [importar dados](import-data.md) módulo à sua experimentação. Pode encontrar o módulo na interface, além da **dados de entrada e saída** categoria.

2. Para **origem de dados**, selecione **URL de Web por meio de HTTP**.

3. Para **URL**, escreva ou cole o URL completo da página que contém os dados que pretende carregar.

    O URL deve incluir o URL do site e o caminho completo, com o nome de ficheiro e extensão, para a página que contém os dados para carregar.

    Por exemplo, a seguinte página contém o conjunto de dados de Iris de aprendizagem de repositório da Universidade da Califórnia, Irvine:

    `http://archive.ics.uci.edu/ml/machine-learning-databases/iris/iris.data`

4. Para **formato de dados**, selecione uma dos dados suportados formatos da lista.

    Recomendamos que verifique sempre os dados com antecedência para determinar o formato. A página de UC Irvine utiliza o formato CSV. Outros formatos de dados suportados são TSV, ARFF e SvmLight.

5. Se os dados estão no formato CSV ou TSV, utilize o **ficheiro tem de linha de cabeçalho** opção para indicar se é ou não a origem de dados inclui uma linha de cabeçalho. A linha de cabeçalho é utilizada para atribuir nomes de coluna.

6. Selecione o **resultados em cache de utilização** opções se não quiser que os dados para mudará muito, ou se quiser evitar recarregar os dados de cada vez que executam a experimentação.

    Quando esta opção está selecionada, a experimentação carrega o tempo de dados, o primeiro o módulo é executado e, posteriormente, utiliza uma versão em cache do conjunto de dados.

    Se quiser recarregar o conjunto de dados em cada iteração do conjunto de dados de experimentação, desmarque a **resultados em cache de utilização** opção. Os resultados também são recarregados se existirem quaisquer alterações aos parâmetros de [importar dados](import-data.md).

7. Execute a experimentação.

## <a name="results"></a>Resultados

Quando terminar, clique com o conjunto de dados de saída e selecione **Visualize** para ver se os dados foi importados com êxito.


## <a name="next-steps"></a>Passos Seguintes

Consulte a [conjunto de módulos disponíveis](module-reference.md) ao serviço Azure Machine Learning. 