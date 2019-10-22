---
title: 'Exportar dados: referência de módulo'
titleSuffix: Azure Machine Learning service
description: Saiba como usar o módulo exportar dados no serviço de Azure Machine Learning para salvar os resultados, dados intermediários e dados de trabalho de seus pipelines em destinos de armazenamento em nuvem fora Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ms.openlocfilehash: b7b4b9de1e91279243e35f1b71f1ef6d2244e9e0
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/21/2019
ms.locfileid: "72693721"
---
# <a name="export-data-module"></a>Exportar módulo de dados

Este artigo descreve um módulo da interface visual (visualização) para Azure Machine Learning serviço.

Use este módulo para salvar os resultados, dados intermediários e dados de trabalho de seus pipelines em destinos de armazenamento em nuvem fora Azure Machine Learning.

Este módulo dá suporte à exportação ou salvamento de dados para os seguintes serviços de dados de nuvem:


- **Exportar para o armazenamento de BLOBs do Azure**: salva dados no serviço blob no Azure. Os dados no serviço blob podem ser compartilhados publicamente ou salvos em armazenamentos de dados de aplicativos protegidos.

  
## <a name="how-to-configure-export-data"></a>Como configurar dados de exportação

1. Adicione o módulo **exportar dados** ao seu pipeline na interface. Você pode encontrar esse módulo na categoria **entrada e saída** .

2. Conecte **dados de exportação** ao módulo que contém os dados que você deseja exportar.

3. Clique duas vezes em **exportar dados** para abrir o painel **Propriedades** .

4. Para **destino de dados**, selecione o tipo de armazenamento em nuvem onde você salvará os dados. Se você fizer alterações nessa opção, todas as outras propriedades serão redefinidas. Portanto, escolha esta opção primeiro!

5. Forneça um nome de conta e um método de autenticação necessários para acessar a conta de armazenamento especificada.

    **Exportar para o armazenamento de BLOBs do Azure** é a única opção na visualização privada. Abaixo mostra como definir o módulo.
    1. O serviço blob do Azure é para armazenar grandes quantidades de dados, incluindo dados binários. Há dois tipos de armazenamento de blob: BLOBs públicos e blobs que exigem credenciais de logon.

    2. Para **tipo de autenticação**, escolha **público (SAS)** se você souber que o armazenamento dá suporte ao acesso por meio de uma URL SAS.

          Uma URL SAS é um tipo especial de URL que pode ser gerado usando um utilitário de armazenamento do Azure e está disponível apenas por um tempo limitado.  Ele contém todas as informações necessárias para autenticação e download.

        Para **URI**, digite ou cole o URI completo que define a conta e o blob público.

        Para formato de arquivo, CSV e TSV têm suporte.

    3. Para contas particulares, escolha **conta**e forneça o nome da conta e a chave da conta, para que o pipeline possa gravar na conta de armazenamento.

         - **Nome da conta**: digite ou cole o nome da conta em que você deseja salvar os dados. Por exemplo, se a URL completa da conta de armazenamento for `http://myshared.blob.core.windows.net`, você digitará `myshared`.

        - **Chave de conta**: Cole a chave de acesso de armazenamento associada à conta.

        -  **Caminho para o contêiner, diretório ou BLOB**: digite o nome do blob em que os dados exportados serão armazenados. Por exemplo, para salvar os resultados de seu pipeline em um novo BLOB chamado **results01. csv** nas **previsões** de contêiner em uma conta denominada **mymldata**, a URL completa para o blob seria `http://mymldata.blob.core.windows.net/predictions/results01.csv`.

            Portanto, no caminho do campo **para o contêiner, diretório ou BLOB**, você especificaria o contêiner e o nome do blob da seguinte maneira: `predictions/results01.csv`

        - Se você especificar o nome de um blob que ainda não existe, o Azure criará o blob para você.

       -  Ao gravar em um blob existente, você pode especificar que o conteúdo atual do blob seja substituído definindo a propriedade, o **modo de gravação do armazenamento de BLOBs do Azure**. Por padrão, essa propriedade é definida como **erro**, o que significa que um erro é gerado sempre que um arquivo de blob existente com o mesmo nome é encontrado.


    4. Para **formato de arquivo para arquivo de blob**, selecione o formato no qual os dados devem ser armazenados.

        - **CSV**: valores separados por vírgulas (CSV) são o formato de armazenamento padrão. Para exportar os títulos de coluna junto com os dados, selecione a opção **gravar linha de cabeçalho de blob**.  Para obter mais informações sobre o formato delimitado por vírgula usado em Azure Machine Learning, consulte [converter em CSV](./convert-to-csv.md).

        - **TSV**: o formato TSV (valores separados por tabulações) é compatível com muitas ferramentas de aprendizado de máquina. Para exportar os títulos de coluna junto com os dados, selecione a opção **gravar linha de cabeçalho de blob**.  

 
    5. **Usar resultados em cache**: Selecione esta opção se desejar evitar a regravação dos resultados para o arquivo de blob cada vez que executar o pipeline. Se não houver nenhuma outra alteração nos parâmetros do módulo, o pipeline gravará os resultados somente na primeira vez em que o módulo for executado ou quando houver alterações nos dados.

    6. Executar o pipeline.

## <a name="next-steps"></a>Passos seguintes

Consulte o [conjunto de módulos disponíveis](module-reference.md) para Azure Machine Learning serviço. 