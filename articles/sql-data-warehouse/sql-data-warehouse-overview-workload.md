---
title: Saiba mais sobre operações de armazém de dados SQL do Azure | Documentos da Microsoft
description: 'A elasticidade do SQL Data Warehouse permite aumentar, reduzir ou colocar em pausa a capacidade de computação, utilizando uma escala móvel de unidades do Data Warehouse (DWUs). Este artigo explica as métricas do armazém de dados e como se relacionam com as DWUs. '
services: sql-data-warehouse
author: WenJason
manager: digimobile
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: design
origin.date: 04/17/2018
ms.date: 11/12/2018
ms.author: v-jay
ms.reviewer: igorstan
ms.openlocfilehash: f0489d9c018abac380cd0f8b1aa51fa5bb81707a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60531182"
---
# <a name="data-warehouse-workload"></a>Carga de trabalho do armazém de dados
Uma carga de trabalho de armazém de dados refere-se a todas as operações que ocorrem num armazém de dados. A carga de trabalho do armazém de dados abrange todo o processo de carregamento de dados para o armazém, execução de análises e relatórios no armazém de dados, gestão de dados no armazém de dados e exportação de dados do armazém de dados. A profundidade e a amplitude destes componentes são, muitas vezes, proporcionais ao nível de maturidade do armazém de dados.

## <a name="new-to-data-warehousing"></a>É principiante no armazenamento de dados?
Um armazém de dados é uma coleção de dados que são carregados a partir de uma ou mais origens de dados e são utilizados para realizar tarefas de business intelligence, tais como análise de dados e relatórios.

Os armazéns de dados são caracterizados por consultas que analisam grandes quantidades de linhas, grande intervalos de dados e podem devolver resultados relativamente grandes para efeitos de análise e relatórios. Os armazéns de dados caracterizam-se também por cargas de dados relativamente grandes versus pequenas inserções/atualizações/eliminações ao nível de transações.

* Um armazém de dados tem um melhor desempenho quando os dados são armazenados de forma a otimizar as consultas que necessitam de analisar grandes quantidades de linhas ou grandes intervalos de dados. Este tipo de análise funciona melhor quando os dados são armazenados e pesquisados por colunas, em vez de linhas.

> [!NOTE]
> O índice columnstore dentro da memória, que utiliza armazenamento com colunas, proporciona ganhos de compressão até 10x superiores e ganhos de desempenho até 100x superiores em relação às árvores binárias para consultas de análises e relatórios. Consideramos os índices columnstore a norma para armazenar e analisar grandes quantidades de dados num armazém de dados.
> 
> 

* Um armazém de dados possui requisitos diferentes de um sistema otimizado para o processamento de transações online (OLTP). O sistema OLTP tem muitas operações de inserção, atualização e eliminação. Estas operações efetuam procuras em linhas específicas na tabela. As procuras em tabelas apresentam melhores resultados quando os dados estão armazenados linha por linha. Os dados podem ser ordenados e rapidamente procurados com uma abordagem de divisão e conquista chamada pesquisa de árvore binária ou btree.

## <a name="data-loading"></a>Carregamento de dados
O carregamento de dados é uma grande parte da carga de trabalho do armazém de dados. Normalmente, as empresas têm um sistema OLTP sobrecarregado, que regista as alterações ao longo do dia quando os clientes estão a gerar transações comerciais. Periodicamente, muitas vezes à noite durante uma janela de manutenção, as transações são movidas ou copiadas para o armazém de dados. Quando os dados estão no armazém de dados, os analistas podem efetuar a análise e tomar decisões empresariais em relação aos dados.

* Tradicionalmente, o processo de carregamento é designado por ETL, que significa Extração, Transformação e Carregamento. Normalmente, os dados precisam de ser transformados para que sejam consistentes com outros dados no armazém de dados. Anteriormente, as empresas utilizavam servidores ETL dedicados para efetuar as transformações. Agora, com um processamento paralelo em grande escala tão rápido, pode carregar primeiro os dados para o SQL Data Warehouse e, em seguida, efetuar as transformações. Este processo é chamado Extração, Carregamento e Transformação (ELT) e está a tornar-se num novo padrão para a carga de trabalho do armazém de dados.

> [!NOTE]
> Com o SQL Server 2016, pode agora efetuar análises em tempo real numa tabela OLTP. Isto não substitui a necessidade de um armazém de dados armazenar e analisar dados, mas proporciona uma forma de efetuar uma análise em tempo real.
> 
> 

### <a name="reporting-and-analysis-queries"></a>Consultas de análise e relatórios
As consultas de análise e relatórios são muitas vezes categorizadas em pequenas, médias e grandes, com base em vários critérios, mas geralmente é com base na hora. Na maioria dos armazéns de dados, existe uma carga de trabalho mista de consultas de execução rápida e de execução longa. Em cada caso, é importante determinar esta combinação e determinar a respetiva frequência (de hora a hora, diariamente, no fim do mês, no fim do trimestre, etc.). É importante compreender que a carga de trabalho de consultas mistas, conjugada com a simultaneidade, levam a um planeamento de capacidade adequado de um armazém de dados.

* O planeamento de capacidade pode ser uma tarefa complexa para uma carga de trabalho de consultas mistas, sobretudo quando precisa de um prazo longo para adicionar capacidade ao armazém de dados. O SQL Data Warehouse elimina a urgência do planeamento de capacidade, uma vez que pode aumentar e diminuir a capacidade de computação em qualquer altura e a capacidade de armazenamento e computação são dimensionadas de forma independente.

### <a name="data-management"></a>Gestão de dados
A gestão de dados é importante, especialmente quando sabe que poderá ficar sem espaço em disco no futuro próximo. Normalmente, os armazéns de dados dividem os dados em intervalos significativos, que são armazenados como partições numa tabela. Todos os produtos baseados no SQL Server permitem mover partições para dentro e fora da tabela. Esta mudança de partições permite mover dados mais antigos para armazenamento menos dispendioso e manter os dados mais recentes disponíveis no armazenamento online.

* Os índices columnstore suportam tabelas particionadas. Nos índices columnstore, as tabelas particionadas são utilizadas para a gestão e o arquivo de dados. Para tabelas armazenadas linha a linha, as partições têm uma função mais ampla no desempenho da consulta.  
* O PolyBase desempenha uma função importante na gestão de dados. Com o PolyBase, tem a opção de arquivar dados mais antigos em armazenamento de blobs do Azure ou do Hadoop.  Isto fornece muitas opções, uma vez que os dados ainda estão online.  Poderá demorar mais tempo a obter dados do Hadoop, mas o compromisso de tempo de obtenção poderá compensar o custo de armazenamento.

### <a name="exporting-data"></a>Exportar dados
Uma forma de disponibilizar dados para relatórios e análise consiste em enviar os dados do armazém de dados para servidores dedicados para a execução de relatórios e análises. Estes servidores são denominados data marts. Por exemplo, pode pré-processar os dados do relatório e, em seguida, exportá-los a partir do armazém de dados para muitos servidores em todo o mundo, para que fiquem amplamente disponíveis para os clientes e analistas.

* Para gerar relatórios, todas as noites pode preencher servidores de relatórios só de leitura com um instantâneo dos dados diários. Isto proporciona maior largura de banda para os clientes, ao mesmo tempo que reduz as necessidades de recursos de computação no armazém de dados. Numa perspetiva de segurança, os data marts permitem reduzir o número de utilizadores que têm acesso ao armazém de dados.
* Para análise, pode criar um cubo de análise no armazém de dados e executar análises no armazém de dados ou pré-processar os dados e exportá-los para o servidor de análise para análise adicional.

## <a name="next-steps"></a>Passos Seguintes
Agora que já sabe um pouco sobre o SQL Data Warehouse, saiba como [criar um SQL Data Warehouse][create a SQL Data Warehouse] e [carregar dados de exemplo][load sample data] rapidamente.

<!--Image references-->

<!--Article references-->
[load sample data]: ./sql-data-warehouse-load-sample-databases.md
[create a SQL Data Warehouse]: ./sql-data-warehouse-get-started-provision.md

<!--MSDN references-->

<!--Other web references-->