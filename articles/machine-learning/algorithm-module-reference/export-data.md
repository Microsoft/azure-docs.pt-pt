---
title: 'Exporte dados: Referência do módulo'
titleSuffix: Azure Machine Learning service
description: Saiba como utilizar o módulo de exportar dados no serviço Azure Machine Learning para guardar os resultados, dados intermediários e dados de trabalho a partir das suas experimentações em destinos de armazenamento na cloud fora do Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: c3744803f172edf9fbf2556a12677e8faef370c2
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65028324"
---
# <a name="export-data-module"></a>Módulo de dados de exportação

Este artigo descreve um módulo da interface visual (pré-visualização) para o serviço Azure Machine Learning.

Utilize este módulo para guardar resultados, dados intermediários e dados de trabalho a partir das suas experimentações em destinos de armazenamento na cloud fora do Azure Machine Learning.

Este módulo suporta a exportar ou guardar os seus dados para os seguintes serviços de dados de cloud:


- **Exportar para o armazenamento de Blobs do Azure**: Guarda dados para o serviço de Blobs do Azure. Dados no serviço de BLOBs podem ser partilhados publicamente ou salvos nos arquivos de dados de aplicação foi protegida.

  
## <a name="how-to-configure-export-data"></a>Como configurar a exportar dados

1. Adicionar a **exportar dados** módulo à sua experimentação na interface. Pode encontrar este módulo na **de entrada e saída** categoria.

2. Ligue-se **exportar dados** para o módulo que contém os dados que pretende exportar.

3. Faça duplo clique em **exportar dados** para abrir o **propriedades** painel.

4. Para **destino dos dados**, selecione o tipo de armazenamento na cloud, onde irá guardar os seus dados. Se fizer alterações a esta opção, todas as outras propriedades são redefinidas. Por isso, certifique-se de que escolher esta opção primeiro!

5. Fornecem um método de autenticação e nome de conta necessário para aceder à conta de armazenamento especificada.

    **Exportar para o armazenamento de Blobs do Azure** é a única opção em pré-visualização privada. Abaixo mostra como configurar o módulo.
    1. É o serviço de Blobs do Azure para armazenar grandes quantidades de dados, incluindo dados binários. Existem dois tipos de armazenamento de BLOBs: blobs públicos e os blobs que necessitem de credenciais de início de sessão.

    2. Para **tipo de autenticação**, escolha **público (SAS)** se souber que o armazenamento suporta o acesso através de um URL de SAS.

          Um URL de SAS é um tipo especial de URL que pode ser gerado ao utilizar um utilitário de armazenamento do Azure e está disponível para apenas um período limitado.  Ele contém todas as informações necessárias para autenticação e a transferência.

        Para **URI**, escreva ou cole o URI completo que define a conta e o blob público.

        Para o formato de arquivo, o CSV e TSV são suportadas.

    3. Para contas privadas, escolha **conta**e forneça o nome da conta e a chave de conta, para que a experimentação pode escrever na conta de armazenamento.

         - **Nome da conta**: Escreva ou cole o nome da conta de onde pretende guardar os dados. Por exemplo, se o URL completo da conta de armazenamento é `http://myshared.blob.core.windows.net`, teria de escrever `myshared`.

        - **Chave da conta**: Cole a chave de acesso de armazenamento que estão associada com a conta.

        -  **Caminho para o contentor, diretório ou blob**: Escreva o nome do blob onde serão armazenados os dados exportados. Por exemplo, para guardar os resultados da sua experimentação para um blob novo chamada **results01.csv** no contentor **previsões** numa conta com o nome **mymldata**, o URL completo para o blob seria `http://mymldata.blob.core.windows.net/predictions/results01.csv`.

            Portanto, no campo **caminho para o contentor, diretório ou blob**, tem de especificar o contentor e o nome do blob como segue: `predictions/results01.csv`

        - Se especificar o nome de um blob que ainda não existir, o Azure cria o blob para.

       -  Ao escrever um blob existente, pode especificar que o conteúdo atual do blob ser substituídas, definindo a propriedade **modo de escrita de armazenamento de Blobs do Azure**. Por predefinição, esta propriedade é definida como **erro**, que significa que é gerado um erro sempre que for encontrado um arquivo de blob existente, o mesmo nome.


    4. Para **formato de ficheiro para o ficheiro blob**, selecione o formato no qual os dados devem ser armazenados.

        - **CSV**: Valores separados por vírgulas (CSV) são o formato de armazenamento padrão. Para exportar os cabeçalhos de coluna em conjunto com os dados, selecione a opção **linha de cabeçalho de blob de escrita**.  Para obter mais informações sobre a vírgula - formato delimitado utilizado no Azure Machine Learning, consulte [converter para CSV](./convert-to-csv.md).

        - **TSV**: Formato de valores separados por tabulações (TSV) é compatível com várias ferramentas de aprendizagem de máquina. Para exportar os cabeçalhos de coluna em conjunto com os dados, selecione a opção **linha de cabeçalho de blob de escrita**.  

 
    5. **Utilize os resultados em cache**: Selecione esta opção se quiser evitar reescrever os resultados para o ficheiro blob sempre que executar a experimentação. Se não houver nenhuma outra alteração aos parâmetros do módulo, a experimentação grava os resultados apenas na primeira vez em que o módulo for executado, ou quando existirem alterações aos dados.

    6. Execute a experimentação.

## <a name="next-steps"></a>Passos Seguintes

Consulte a [conjunto de módulos disponíveis](module-reference.md) ao serviço Azure Machine Learning. 