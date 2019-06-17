---
title: 'Importar do armazenamento de Blobs do Azure: Referência do módulo'
titleSuffix: Azure Machine Learning service
description: Saiba de que este tópico descreve como utilizar a importação do módulo de armazenamento de Blobs do Azure no serviço Azure Machine Learning para ler dados do armazenamento de Blobs do Azure, para que possa utilizar os dados numa experimentação do machine learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 4ac98516c1a326e1ede09bbb9660113ffd0642a0
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65029689"
---
# <a name="import-from-azure-blob-storage-module"></a>Importar a partir do módulo de armazenamento de Blobs do Azure

Este artigo descreve um módulo da interface visual (pré-visualização) para o serviço Azure Machine Learning.

Utilize este módulo para ler dados do armazenamento de Blobs do Azure, para que possa utilizar os dados numa experimentação do machine learning.  

É o serviço de Blobs do Azure para armazenar grandes quantidades de dados, incluindo dados binários. Blobs do Azure podem ser acedidos a partir de qualquer local, utilizando HTTP ou HTTPS. Autenticação poderá ser necessária dependendo do tipo de armazenamento de Blobs. 

- Blobs públicos podem ser acedidos por qualquer pessoa, ou pelos utilizadores que têm um URL de SAS.
- Blobs privadas requerem um início de sessão e as credenciais.

Importar a partir do armazenamento de BLOBs requer que dados armazenados em blobs que utilizam o **blob de blocos** formato. Os ficheiros armazenados no blob tem de utilizar separados por vírgulas (CSV) ou formatos de (TSV) separados por tabulações. Quando ler o ficheiro, os registos e quaisquer cabeçalhos de atributo aplicáveis são carregados como linhas na memória como um conjunto de dados.


Recomendamos vivamente que crie perfis para seus dados antes de importar, para se certificar de que o esquema é conforme esperado. O processo de importação analisa algum número de linhas principais para determinar o esquema, mas linhas posteriores podem conter colunas adicionais ou dados que causam erros.



## <a name="manually-set-properties-in-the-import-data-module"></a>Definir manualmente as propriedades no módulo importar dados

Os passos seguintes descrevem como configurar manualmente a origem de importação.

1. Adicionar a **importar dados** módulo à sua experimentação. Pode encontrar este módulo na interface, além de **dados de entrada e saída**

2. Para **origem de dados**, selecione **armazenamento de Blobs do Azure**.

3. Para **tipo de autenticação**, escolha **público (URL de SAS)** se sabe que as informações foram fornecidas como uma origem de dados públicos. Um URL de SAS é um URL de limite de tempo para acesso público que pode gerar usando um utilitário de armazenamento do Azure.

    Caso contrário, escolha **conta**.

4. Se os seus dados estão num **público** blob que pode ser acedido ao utilizar um URL de SAS, não necessita de credenciais adicionais porque a cadeia de URL contém todas as informações que é necessária para o download e a autenticação.

    Na **URI** campo, escreva ou cole o URI completo que define a conta e o blob público.



5. Se os seus dados estão num **privada** conta, tem de fornecer credenciais, incluindo o nome da conta e a chave.

    - Para **nome da conta**, escreva ou cole o nome da conta que contém o blob que pretende aceder.

        Por exemplo, se o URL completo da conta de armazenamento é `http://myshared.blob.core.windows.net`, teria de escrever `myshared`.

    - Para **chave de conta**, cole a chave de acesso de armazenamento que estão associada com a conta.

        Se não souber a chave de acesso, consulte a seção, "Gerenciar suas contas de armazenamento do Azure" neste artigo: [Sobre as contas de armazenamento do Azure](https://docs.microsoft.com/azure/storage/storage-create-storage-account).

6. Para **caminho para o contentor, diretório ou blob**, escreva o nome do blob específico que pretende recuperar.

    Por exemplo, se tiver carregado um ficheiro denominado **data01.csv** para o contentor **trainingdata** numa conta com o nome **mymldata**, o URL completo para o ficheiro seria: `http://mymldata.blob.core.windows.net/trainingdata/data01.txt` .

    Portanto, no campo **caminho para o contentor, diretório ou blob**, teria de escrever: `trainingdata/data01.csv`

    Para importar vários ficheiros, pode utilizar os carateres universais `*` (asterisco) ou `?` (ponto de interrogação).

    Por exemplo, supondo que o contentor `trainingdata` contém vários ficheiros de um formato compatível, poderia usar a especificação do seguinte para ler todos os ficheiros a partir do `data`e a concatená-los num único conjunto de dados:

    `trainingdata/data*.csv`

    Não é possível utilizar carateres universais em nomes de contentor. Se tiver de importar os ficheiros de vários contentores, utilize uma instância separada dos **importar dados** módulo para cada contentor e intercalação, em seguida, os conjuntos de dados com o [adicionar linhas](./add-rows.md) módulo.

    > [!NOTE]
    > Se tiver selecionado a opção **resultados em cache de utilização**, quaisquer alterações efetuadas aos ficheiros no contentor não acionam uma atualização dos dados na experimentação.

7. Para **formato de ficheiro Blob**, selecione a opção que indica o formato dos dados que são armazenados no blob, para que o Azure Machine Learning pode processar os dados adequadamente. São suportados os seguintes formatos:

    - **CSV**: Valores separados por vírgulas (CSV) são o formato de armazenamento padrão para exportar e importar ficheiros no Azure Machine Learning. Se os dados já contém uma linha de cabeçalho, certifique-se de que seleciona a opção **ficheiro tem de linha de cabeçalho**, ou o cabeçalho será tratado como uma linha de dados.

       

    - **TSV**: Valores separados por tabulações (TSV) são um formato utilizado pela máquina muitas ferramentas de aprendizagem. Se os dados já contém uma linha de cabeçalho, certifique-se de que seleciona a opção **ficheiro tem de linha de cabeçalho**, ou o cabeçalho será tratado como uma linha de dados.

       

    - **ARFF**: Este formato suporta a importação de arquivos no formato utilizado pelo conjunto de ferramentas de Weka. 

   

8. Execute a experimentação.


## <a name="next-steps"></a>Passos Seguintes

Consulte a [conjunto de módulos disponíveis](module-reference.md) ao serviço Azure Machine Learning. 