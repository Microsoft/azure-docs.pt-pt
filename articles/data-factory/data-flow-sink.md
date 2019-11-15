---
title: Configurar uma transformação de coletor no recurso de fluxo de dados de mapeamento do Azure Data Factory
description: Saiba como configurar uma transformação de coletor no fluxo de dados de mapeamento.
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/03/2019
ms.openlocfilehash: fa6a2fd853673493c93dbe65f889468c8e0c8617
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/14/2019
ms.locfileid: "74082938"
---
# <a name="sink-transformation-for-a-data-flow"></a>Transformação do coletor para um fluxo de dados

Depois de transformar o fluxo de dados, você pode coletar os dados em um conjunto de dado de destino. Na transformação do coletor, escolha uma definição de conjunto de dados para a saída de destino. Você pode ter tantas transformações de coletor quanto o fluxo de dados exigir.

Para considerar a descompasso de esquema e as alterações nos dados de entrada, coletar os dados de saída para uma pasta sem um esquema definido no conjunto de dado de saída. Você também pode considerar as alterações de coluna em suas fontes selecionando **permitir descompasso de esquema** na origem. Em seguida, mapear todos os campos no coletor.

![Opções na guia coletor, incluindo a opção de mapa automático](media/data-flow/sink1.png "coletor 1")

Para coletar todos os campos de entrada, ative o **mapa automático**. Para escolher os campos a serem coletados no destino ou para alterar os nomes dos campos no destino, desative o **mapa automático**. Em seguida, abra a guia **mapeamento** para mapear os campos de saída.

![Opções na guia mapeamento](media/data-flow/sink2.png "coletor 2")

## <a name="output"></a>Saída 
Para o armazenamento de BLOBs do Azure ou Data Lake Storage tipos de coletor, gere os dados transformados em uma pasta. O Spark gera arquivos de dados de saída particionados com base no esquema de particionamento usado pela transformação do coletor. 

Você pode definir o esquema de particionamento na guia **otimizar** . Se desejar que Data Factory mescle a saída em um único arquivo, selecione **partição única**. Se você quiser manter ou criar pastas particionadas, use o **particionamento de chave** e defina as chaves que deseja usar para estruturas de pastas particionadas.

![Opções na guia otimizar](media/data-flow/opt001.png "opções de coletor")

## <a name="field-mapping"></a>Mapeamento de campo
Na guia **mapeamento** da transformação do coletor, você pode mapear as colunas de entrada à esquerda para os destinos à direita. Quando você coleta fluxos de dados em arquivos, Data Factory sempre irá gravar novos arquivos em uma pasta. Quando você mapear para um conjunto de dados do, você escolherá opções de operação de tabela de banco de dados para inserir, atualizar, Upsert ou excluir.

![A guia mapeamento](media/data-flow/sink2.png "Coletores")

Na tabela de mapeamento, você pode selecionar vários para vincular várias colunas, desvincular várias colunas ou mapear várias linhas para o mesmo nome de coluna.

Para sempre mapear o conjunto de campos de entrada para um destino como eles são e para aceitar totalmente as definições de esquema flexíveis, selecione **permitir descompasso de esquema**.

![A guia mapeamento, mostrando os campos mapeados para colunas no conjunto de conjuntos](media/data-flow/multi1.png "várias opções")

Para redefinir os mapeamentos de coluna, selecione **remapear**.

![A guia coletor](media/data-flow/sink1.png "Coletor um")

Selecione **validar esquema** para falhar o coletor se o esquema for alterado.

Selecione **limpar a pasta** para truncar o conteúdo da pasta do coletor antes de gravar os arquivos de destino nessa pasta de destino.

## <a name="fixed-mapping-vs-rule-based-mapping"></a>Mapeamento fixo versus mapeamento baseado em regra
Ao desativar o mapeamento automático, você terá a opção de adicionar mapeamento baseado em coluna (mapeamento fixo) ou mapeamento baseado em regra. O mapeamento baseado em regras permitirá que você grave expressões com correspondência de padrões enquanto o mapeamento fixo mapeará nomes de coluna física e lógica.

![Mapeamento baseado em regras](media/data-flow/rules4.png "Mapeamento baseado em regras")

Ao escolher o mapeamento baseado em regras, você está instruindo o ADF a avaliar sua expressão de correspondência para corresponder às regras de padrão de entrada e definir os nomes de campo de saída. Você pode adicionar qualquer combinação de mapeamentos com base em campo e em regra. Os nomes de campo são então gerados em tempo de execução pelo ADF com base nos metadados de entrada da origem. Você pode exibir os nomes dos campos gerados durante a depuração e usando o painel de visualização de dados.

Os detalhes sobre a correspondência de padrões estão na [documentação do padrão da coluna](concepts-data-flow-column-pattern.md).

Você também pode inserir padrões de expressão regular ao usar a correspondência baseada em regra, expandindo a linha e inserindo uma expressão regular ao lado de "nome corresponde:".

![Mapeamento de Regex](media/data-flow/scdt1g4.png "Mapeamento de Regex")

Um exemplo comum muito básico para um mapeamento baseado em regra versus mapeamento fixo é o caso em que você deseja mapear todos os campos de entrada para o mesmo nome em seu destino. No caso de mapeamentos fixos, você listaria cada coluna individual na tabela. Para o mapeamento baseado em regras, você teria uma única regra que mapeia todos os campos usando ```true()``` para o mesmo nome de campo de entrada representado por ```$$```.

### <a name="sink-association-with-dataset"></a>Associação de coletor com DataSet

O conjunto de os que você selecionar para o coletor pode ou não ter um esquema definido na definição do conjunto de conjuntos. Se não tiver um esquema definido, você deverá permitir descompasso de esquema. Quando você definiu um mapeamento fixo, o mapeamento de nome lógico para físico persistirá na transformação do coletor. Se você alterar a definição de esquema do conjunto de coleta, poderá interromper o mapeamento do coletor. Para evitar isso, use o mapeamento baseado em regras. Os mapeamentos baseados em regras são generalizados, o que significa que as alterações de esquema no conjunto de seus conjuntos de um não interromperão o mapeamento.

## <a name="file-name-options"></a>Opções de nome de arquivo

Configurar a nomenclatura de arquivo: 

   * **Padrão**: permitir que o Spark nomeie arquivos com base em padrões de parte.
   * **Padrão**: Insira um padrão para os arquivos de saída. Por exemplo, os **empréstimos [n]** criarão loans1. csv, loans2. csv e assim por diante.
   * **Por partição**: Insira um nome de arquivo por partição.
   * **Como dados na coluna**: defina o arquivo de saída para o valor de uma coluna.
   * **Saída para um único arquivo**: com essa opção, o ADF combinará os arquivos de saída particionados em um único arquivo nomeado. Para usar essa opção, seu conjunto de seus conjuntos de seus deve ser resolvido para um nome de pasta. Além disso, lembre-se de que essa operação de mesclagem possivelmente pode falhar com base no tamanho do nó.

> [!NOTE]
> As operações de arquivo iniciam somente quando você está executando a atividade executar fluxo de dados. Eles não são iniciados no modo de depuração de fluxo de dados.

## <a name="database-options"></a>Opções de banco de dados

Escolha as configurações do banco de dados:

![A guia Configurações, mostrando as opções do coletor SQL](media/data-flow/alter-row2.png "Opções SQL")

* **Método de atualização**: o padrão é permitir inserções. Desmarque **permitir inserção** se quiser parar de inserir novas linhas de sua origem. Para atualizar, upsertr ou excluir linhas, primeiro adicione uma transformação ALTER-Row para marcar linhas para essas ações. 
* **Recriar tabela**: remova ou crie sua tabela de destino antes de concluir o fluxo de dados.
* **Truncar tabela**: Remove todas as linhas da tabela de destino antes de o fluxo de dados ser concluído.
* **Tamanho do lote**: Insira um número para gravações de Bucket em partes. Use esta opção para grandes cargas de dados. 
* **Habilitar preparo**: Use o polybase ao carregar o data warehouse do Azure como seu conjunto de dados do coletor.
* **Scripts SQL anteriores e posteriores**: Insira os scripts SQL de várias linhas que serão executados antes (pré-processamento) e após (pós-processamento) os dados são gravados no banco de dado do coletor

![pré e pós-scripts de processamento do SQL](media/data-flow/prepost1.png "Scripts de processamento SQL")

> [!NOTE]
> No fluxo de dados, você pode direcionar Data Factory para criar uma nova definição de tabela no banco de dados de destino. Para criar a definição de tabela, defina um conjunto de um DataSet na transformação do coletor que tenha um novo nome de tabela. No conjunto de SQL, abaixo do nome da tabela, selecione **Editar** e insira um novo nome de tabela. Em seguida, na transformação do coletor, ative **permitir descompasso de esquema**. Defina **importar esquema** como **nenhum**.

![Configurações do conjunto de configuração do SQL, mostrando onde editar o nome da tabela](media/data-flow/dataset2.png "Esquema SQL")

> [!NOTE]
> Ao atualizar ou excluir linhas no coletor de banco de dados, você deve definir a coluna de chave. Essa configuração permite que a transformação ALTER-Row determine a linha exclusiva na DML (biblioteca de movimentação de dados).

### <a name="cosmosdb-specific-settings"></a>Configurações específicas do CosmosDB

Quando dados de aterrissagem no CosmosDB, você precisará considerar estas opções adicionais:

* Chave de partição: é um campo obrigatório. Insira uma cadeia de caracteres que represente a chave de partição para sua coleção. Exemplo: ```/movies/title```
* Taxa de transferência: defina um valor opcional para o número de RUs que você gostaria de aplicar à sua coleção CosmosDB para cada execução desse fluxo de dados. O mínimo é 400.

## <a name="next-steps"></a>Passos seguintes
Agora que você criou o fluxo de dados, adicione uma [atividade de fluxo de dados ao seu pipeline](concepts-data-flow-overview.md).
