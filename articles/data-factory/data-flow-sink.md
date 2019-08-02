---
title: Configurar uma transformação de coletor no recurso de fluxo de dados de mapeamento do Azure Data Factory
description: Saiba como configurar uma transformação de coletor no fluxo de dados de mapeamento.
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/03/2019
ms.openlocfilehash: b228dfd92fe389d196a65f7152ef22751842f4bb
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/30/2019
ms.locfileid: "68640299"
---
# <a name="sink-transformation-for-a-data-flow"></a>Transformação do coletor para um fluxo de dados

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Depois de transformar o fluxo de dados, você pode coletar os dados em um conjunto de dado de destino. Na transformação do coletor, escolha uma definição de conjunto de dados para a saída de destino. Você pode ter tantas transformações de coletor quanto o fluxo de dados exigir.

Para considerar a descompasso de esquema e as alterações nos dados de entrada, coletar os dados de saída para uma pasta sem um esquema definido no conjunto de dado de saída. Você também pode considerar as alterações de coluna em suas fontes selecionando **permitir descompasso de esquema** na origem. Em seguida, mapear todos os campos no coletor.

![Opções na guia coletor, incluindo a opção de mapa automático](media/data-flow/sink1.png "coletor 1")

Para coletar todos os campos de entrada, ative o **mapa automático**. Para escolher os campos a serem coletados no destino ou para alterar os nomes dos campos no destino, desative o **mapa automático**. Em seguida, abra a guia **mapeamento** para mapear os campos de saída.

![Opções na guia mapeamento](media/data-flow/sink2.png "coletor 2")

## <a name="output"></a>Output 
Para o armazenamento de BLOBs do Azure ou Data Lake Storage tipos de coletor, gere os dados transformados em uma pasta. O Spark gera arquivos de dados de saída particionados com base no esquema de particionamento usado pela transformação do coletor. 

Você pode definir o esquema de particionamento na guia **otimizar** . Se desejar que Data Factory mescle a saída em um único arquivo, selecione **partição única**.

![Opções na guia otimizar](media/data-flow/opt001.png "Opções de coletor")

## <a name="field-mapping"></a>Mapeamento de campo
Na guia **mapeamento** da transformação do coletor, você pode mapear as colunas de entrada à esquerda para os destinos à direita. Quando você coleta fluxos de dados em arquivos, Data Factory sempre irá gravar novos arquivos em uma pasta. Quando você mapear para um conjunto de dados do, você escolherá opções de operação de tabela de banco de dados para inserir, atualizar, Upsert ou excluir.

![A guia mapeamento](media/data-flow/sink2.png "Coletores")

Na tabela de mapeamento, você pode selecionar vários para vincular várias colunas, desvincular várias colunas ou mapear várias linhas para o mesmo nome de coluna.

Para sempre mapear o conjunto de campos de entrada para um destino como eles são e para aceitar totalmente as definições de esquema flexíveis, selecione **permitir descompasso de esquema**.

![A guia mapeamento, mostrando os campos mapeados para colunas no conjunto de conjuntos](media/data-flow/multi1.png "várias opções")

Para redefinir os mapeamentos de coluna,selecione remapear.

![A guia coletor](media/data-flow/sink1.png "Coletor um")

Selecione **validar esquema** para falhar o coletor se o esquema for alterado.

Selecione **limpar a pasta** para truncar o conteúdo da pasta do coletor antes de gravar os arquivos de destino nessa pasta de destino.

## <a name="rule-based-mapping"></a>Mapeamento baseado em regras
Ao desligar o mapeamento automático, você terá a opção de adicionar mapeamento baseado em coluna (mapeamento fixo) ou mapeamento baseado em regra. O mapeamento baseado em regras permitirá que você escreva expressões com correspondência de padrões. 

![Mapeamento baseado em regras](media/data-flow/rules4.png "Mapeamento baseado em regras")

Ao escolher o mapeamento baseado em regras, você está instruindo o ADF a avaliar sua expressão de correspondência para corresponder às regras de padrão de entrada e definir os nomes de campo de saída. Você pode adicionar qualquer combinação de mapeamentos com base em campo e em regra. Os nomes de campo são então gerados em tempo de execução pelo ADF com base nos metadados de entrada da origem. Você pode exibir os nomes dos campos gerados durante a depuração e usando o painel de visualização de dados.

Os detalhes sobre a correspondência de padrões estão na [documentação do padrão da coluna](concepts-data-flow-column-pattern.md).

## <a name="file-name-options"></a>Opções de nome de arquivo

Configurar a nomenclatura de arquivo: 

   * **Padrão**: Permitir que o Spark nomeie arquivos com base nos padrões de parte.
   * **Padrão**: Insira um padrão para os arquivos de saída. Por exemplo, os **empréstimos [n]** criarão loans1. csv, loans2. csv e assim por diante.
   * **Por partição**: Insira um nome de arquivo por partição.
   * **Como dados na coluna**: Defina o arquivo de saída para o valor de uma coluna.
   * **Saída para um único arquivo**: Com essa opção, o ADF combinará os arquivos de saída particionados em um único arquivo nomeado. Para usar essa opção, seu conjunto de seus conjuntos de seus deve ser resolvido para um nome de pasta. Além disso, lembre-se de que essa operação de mesclagem possivelmente pode falhar com base no tamanho do nó.

> [!NOTE]
> As operações de arquivo iniciam somente quando você está executando a atividade executar fluxo de dados. Eles não são iniciados no modo de depuração de fluxo de dados.

## <a name="database-options"></a>Opções de banco de dados

Escolha as configurações do banco de dados:

* **Método de atualização**: O padrão é permitir inserções. Desmarque **permitir inserção** se quiser parar de inserir novas linhas de sua origem. Para atualizar, upsertr ou excluir linhas, primeiro adicione uma transformação ALTER-Row para marcar linhas para essas ações. 
* **Recriar tabela**: Remova ou crie sua tabela de destino antes de o fluxo de dados ser concluído.
* **Truncar tabela**: Remova todas as linhas da tabela de destino antes de o fluxo de dados ser concluído.
* **Tamanho do lote**: Insira um número para gravações de Bucket em partes. Use esta opção para grandes cargas de dados. 
* **Habilitar preparo**: Use o polybase ao carregar o data warehouse do Azure como seu conjunto de dados do coletor.

![A guia Configurações, mostrando as opções do coletor SQL](media/data-flow/alter-row2.png "Opções SQL")

> [!NOTE]
> No fluxo de dados, você pode direcionar Data Factory para criar uma nova definição de tabela no banco de dados de destino. Para criar a definição de tabela, defina um conjunto de um DataSet na transformação do coletor que tenha um novo nome de tabela. No conjunto de SQL, abaixo do nome da tabela, selecione **Editar** e insira um novo nome de tabela. Em seguida, na transformação do coletor, ative **permitir descompasso de esquema**. Defina **importar esquema** como **nenhum**.

![Configurações do conjunto de configuração do SQL, mostrando onde editar o nome da tabela](media/data-flow/dataset2.png "Esquema SQL")

> [!NOTE]
> Ao atualizar ou excluir linhas no coletor de banco de dados, você deve definir a coluna de chave. Essa configuração permite que a transformação ALTER-Row determine a linha exclusiva na DML (biblioteca de movimentação de dados).

## <a name="next-steps"></a>Passos Seguintes
Agora que você criou o fluxo de dados, adicione uma [atividade de fluxo de dados ao seu pipeline](concepts-data-flow-overview.md).
