---
title: 'Importar do armazenamento de BLOBs do Azure: Referência de módulo'
titleSuffix: Azure Machine Learning service
description: Aprenda este tópico descreve como usar o módulo importar do armazenamento de BLOBs do Azure no serviço Azure Machine Learning para ler dados do armazenamento de BLOBs do Azure, para que você possa usar os dados em um experimento de aprendizado de máquina.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ms.openlocfilehash: fea64070c496379351bb75f2a38aba9b4db70dcd
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70128725"
---
# <a name="import-from-azure-blob-storage-module"></a>Importar do módulo de armazenamento de BLOBs do Azure

Este artigo descreve um módulo da interface visual (visualização) para Azure Machine Learning serviço.

Use este módulo para ler dados do armazenamento de BLOBs do Azure, para que você possa usar os dados em um experimento de aprendizado de máquina.  

O serviço blob do Azure é para armazenar grandes quantidades de dados, incluindo dados binários. Os BLOBs do Azure podem ser acessados de qualquer lugar, usando HTTP ou HTTPS. A autenticação pode ser necessária dependendo do tipo de armazenamento de BLOBs. 

- Os BLOBs públicos podem ser acessados por qualquer pessoa ou por usuários que têm uma URL SAS.
- Os BLOBs privados exigem um logon e credenciais.

A importação do armazenamento de BLOBs exige que os dados sejam armazenados em BLOBs que usam o formato de **blob de blocos** . Os arquivos armazenados no blob devem usar formatos separados por vírgulas (CSV) ou separados por tabulação (TSV). Quando você lê o arquivo, os registros e os títulos de atributo aplicáveis são carregados como linhas na memória como um conjunto de uma.


É altamente recomendável que você crie o perfil de seus dados antes de importar, para certificar-se de que o esquema está conforme o esperado. O processo de importação examina um número de linhas de cabeçalho para determinar o esquema, mas as linhas posteriores podem conter colunas extras ou dados que causam erros.



## <a name="manually-set-properties-in-the-import-data-module"></a>Definir manualmente as propriedades no módulo importar dados

As etapas a seguir descrevem como configurar manualmente a origem da importação.

1. Adicione o módulo **importar dados** ao seu experimento. Você pode encontrar esse módulo na interface, na **entrada e saída de dados**

2. Para **fonte de dados**, selecione **armazenamento de BLOBs do Azure**.

3. Para **tipo de autenticação**, escolha **público (URL SAS)** se você souber que as informações foram fornecidas como uma fonte de dados pública. Uma URL SAS é uma URL de limite de tempo para acesso público que você pode gerar usando um utilitário de armazenamento do Azure.

    Caso contrário, escolha **conta**.

4. Se os dados estiverem em um blob **público** que pode ser acessado usando uma URL SAS, você não precisará de credenciais adicionais porque a cadeia de caracteres de URL contém todas as informações necessárias para download e autenticação.

    No campo **URI** , digite ou cole o URI completo que define a conta e o blob público.



5. Se os dados estiverem em uma conta **privada** , você deverá fornecer credenciais, incluindo o nome da conta e a chave.

    - Para **nome da conta**, digite ou cole o nome da conta que contém o blob que você deseja acessar.

        Por exemplo, se a URL completa da conta de armazenamento for `http://myshared.blob.core.windows.net`, você digitaria `myshared`.

    - Para **chave de conta**, Cole a chave de acesso de armazenamento associada à conta.

        Se você não souber a chave de acesso, consulte a seção "gerenciar suas contas de armazenamento do Azure" neste artigo: [Sobre contas de armazenamento do Azure](https://docs.microsoft.com/azure/storage/storage-create-storage-account).

6. Para **caminho para contêiner, diretório ou BLOB**, digite o nome do blob específico que você deseja recuperar.

    Por exemplo, se você carregou um arquivo chamado **data01. csv** para o contêiner **trainingdata** em uma conta chamada **mymldata**, a URL completa para o arquivo seria: `http://mymldata.blob.core.windows.net/trainingdata/data01.txt`.

    Portanto, no caminho do campo **para o contêiner, diretório ou BLOB**, você deve digitar:`trainingdata/data01.csv`

    Para importar vários arquivos, você pode usar os caracteres `*` curinga (asterisco) ou `?` (ponto de interrogação).

    Por exemplo, supondo que `trainingdata` o contêiner contenha vários arquivos de um formato compatível, você pode usar a seguinte especificação para ler todos os `data`arquivos começando com e concatena-los em um único conjunto de um.

    `trainingdata/data*.csv`

    Você não pode usar caracteres curinga em nomes de contêiner. Se você precisar importar arquivos de vários contêineres, use uma instância separada do módulo **importar dados** para cada contêiner e, em seguida, mescle os DataSets usando o módulo [adicionar linhas](./add-rows.md) .

    > [!NOTE]
    > Se você tiver selecionado a opção, **use resultados armazenados em cache**, quaisquer alterações feitas nos arquivos no contêiner não disparam uma atualização dos dados no experimento.

7. Em **formato de arquivo de blob**, selecione uma opção que indica o formato dos dados armazenados no BLOB, para que Azure Machine Learning possa processar os dados adequadamente. Há suporte para os seguintes formatos:

    - **CSV**: Os valores separados por vírgulas (CSV) são o formato de armazenamento padrão para exportar e importar arquivos no Azure Machine Learning. Se os dados já contiverem uma linha de cabeçalho, certifique-se de selecionar a opção, **arquivo tem uma linha de cabeçalho**ou o cabeçalho será tratado como uma linha de dados.

       

    - **TSV**: Os valores separados por tabulações (TSV) são um formato usado por muitas ferramentas de aprendizado de máquina. Se os dados já contiverem uma linha de cabeçalho, certifique-se de selecionar a opção, **arquivo tem uma linha de cabeçalho**ou o cabeçalho será tratado como uma linha de dados.

       

    - **ARFF**: Esse formato dá suporte à importação de arquivos no formato usado pelo conjunto de ferramentas weka. 

   

8. Execute a experimentação.


## <a name="next-steps"></a>Passos Seguintes

Consulte o [conjunto de módulos disponíveis](module-reference.md) para Azure Machine Learning serviço. 