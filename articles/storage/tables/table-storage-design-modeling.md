---
title: Relações de modelagem no design de armazenamento de tabelas do Azure | Microsoft Docs
description: Entenda o processo de modelagem ao projetar sua solução de armazenamento de tabela.
services: storage
author: MarkMcGeeAtAquent
ms.service: storage
ms.topic: article
ms.date: 04/23/2018
ms.author: sngun
ms.subservice: tables
ms.openlocfilehash: 25082c107fbc0feeb533aa2b4fc56cff960e778d
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75457555"
---
# <a name="modeling-relationships"></a>Modelar relações
Este artigo discute o processo de modelagem para ajudá-lo a projetar suas soluções de armazenamento de tabelas do Azure.

A criação de modelos de domínio é uma etapa importante no design de sistemas complexos. Normalmente, o processo de modelagem é utilizado para identificar as entidades e as relações entre eles, como uma forma de compreender o domínio de empresa e informar o design do seu sistema. Esta seção se concentra em como é possível converter alguns dos tipos de relação comuns encontrados em modelos de domínio para designs para o serviço de tabela. O processo de mapeamento de um modelo de dados lógico para um físico baseados em NoSQL-modelo de dados é diferente da usada ao conceber uma base de dados relacional. Design de bancos de dados relacionais normalmente pressupõe um processo de normalização de dados otimizado para minimizar a redundância – e uma capacidade de consulta declarativa que abstrai a como a implementação de como funciona a base de dados.  

## <a name="one-to-many-relationships"></a>Relações um-para-muitos
Relações um-para-muitos entre objetos de domínio de negócios ocorrerem com frequência: por exemplo, um departamento tem muitos funcionários. Existem várias formas de implementar as relações um-para-muitos no serviço tabela cada com prós e contras que podem ser relevantes para o cenário em particular.  

Considere o exemplo de uma grande empresa multinacional com dezenas de milhares de departamentos e entidades de funcionário em que cada departamento tem muitos funcionários e de cada funcionário como associados a um departamento específico. Uma abordagem é armazenar separado de departamento e entidades de funcionário como estas:  


![Armazenar entidades de departamento e funcionário separadas](media/storage-table-design-guide/storage-table-design-IMAGE01.png)

Este exemplo mostra uma relação um-para-muitos implícita entre tipos de acordo com o **PartitionKey** valor. Cada departamento pode ter muitos funcionários.  

Este exemplo também mostra uma entidade de departamento e respetivas entidades de funcionários relacionados na mesma partição. Poderia optar por utilizar partições diferentes, tabelas ou contas de armazenamento até mesmo para os tipos de entidade diferentes.  

Uma abordagem alternativa é desnormalizar os dados e armazenar apenas as entidades de funcionários com dados de departamento desnormalizada, conforme mostrado no exemplo a seguir. Neste cenário específico, esta abordagem desnormalizada pode não ser a melhor se tiver um requisito para poder alterar os detalhes de um Gestor de departamento, porque para fazer isso tem de atualizar todos os funcionários do departamento.  

![Entidade Employee](media/storage-table-design-guide/storage-table-design-IMAGE02.png)

Para obter mais informações, consulte a [padrão de desnormalização](table-storage-design-patterns.md#denormalization-pattern) mais adiante neste guia.  

A tabela seguinte resume os prós e contras de cada uma das abordagens descritas acima para o armazenamento de funcionários e entidades de departamento que tenham uma relação um-para-muitos. Também deve considerar a frequência esperada executar várias operações: pode ser aceitável para ter um design que inclui uma operação dispendiosa, se essa operação só acontece com pouca frequência.  

<table>
<tr>
<th>Abordagem</th>
<th>Profissionais de TI</th>
<th>Contras</th>
</tr>
<tr>
<td>Tipos de entidade separada, a mesma partição, a mesma tabela</td>
<td>
<ul>
<li>Pode atualizar uma entidade de departamento com uma única operação.</li>
<li>Pode usar um EGT para manter a consistência, se tiver um requisito para modificar uma entidade de departamento sempre que atualizar/inserir/eliminar uma entidade employee. Por exemplo, se mantém uma contagem de funcionários departamentais para cada departamento.</li>
</ul>
</td>
<td>
<ul>
<li>Terá de obter um funcionário e uma entidade do departamento para algumas atividades de cliente.</li>
<li>Operações de armazenamento acontecem na mesma partição. Em volumes de alta transação, isso pode resultar num ponto de acesso.</li>
<li>Não é possível mover um funcionário para um novo departamento, usando um EGT.</li>
</ul>
</td>
</tr>
<tr>
<td>Tipos de entidade separados, partições ou tabelas diferentes ou contas de armazenamento</td>
<td>
<ul>
<li>Pode atualizar uma entidade de departamento ou funcionário com uma única operação.</li>
<li>Em volumes de alta transação, isto pode ajudar a distribuir a carga por mais partições.</li>
</ul>
</td>
<td>
<ul>
<li>Terá de obter um funcionário e uma entidade do departamento para algumas atividades de cliente.</li>
<li>Não é possível utilizar EGTs para manter a consistência quando atualizar/inserir/eliminar um funcionário e atualização de um departamento. Por exemplo, a atualizar um número de funcionários numa entidade de departamento.</li>
<li>Não é possível mover um funcionário para um novo departamento, usando um EGT.</li>
</ul>
</td>
</tr>
<tr>
<td>Desnormalizar para o único tipo de entidade</td>
<td>
<ul>
<li>Pode recuperar todas as informações de que precisa com uma única solicitação.</li>
</ul>
</td>
<td>
<ul>
<li>Pode ser dispendioso para manter a consistência, se precisar de atualizar as informações do departamento (isso exigiria que Atualize todos os funcionários num departamento).</li>
</ul>
</td>
</tr>
</table>

Como escolher entre estas opções, e quais os prós e contras são mais significativas, depende do seus cenários de aplicação específica. Por exemplo, a frequência com que modifique entidades de departamento; todas as suas consultas de funcionário preciso obter mais informações departamentais; como fechar é para os limites de escalabilidade em suas partições ou a sua conta de armazenamento?  

## <a name="one-to-one-relationships"></a>Relações um para um
Modelos de domínio podem incluir um para um relações entre entidades. Se precisar de implementar uma relação unívoca no serviço tabela, também tem de escolher como ligar as duas entidades relacionadas, quando precisar de obter ambos. Esta ligação pode ser implícita, com base numa convenção dos valores de chave ou explícita ao armazenar uma ligação sob a forma de **PartitionKey** e **RowKey** valores em cada entidade para a entidade relacionada. Para uma discussão sobre se deve armazenar as entidades relacionadas na mesma partição, consulte a secção [um-para-muitos relações](#one-to-many-relationships).  

Também existem considerações de implementação que podem levar a implementar as relações um para um no serviço de tabela:  

* Tratamento de entidades grandes (para obter mais informações, consulte [grandes entidades padrão](table-storage-design-patterns.md#large-entities-pattern)).  
* Implementando controles de acesso (para obter mais informações, consulte controlando o acesso com assinaturas de acesso compartilhado).  

## <a name="join-in-the-client"></a>Junte-se no cliente
Embora existam formas para modelar relações no serviço tabela, deve não se esqueça de que os dois motivos principais para utilizar o serviço de tabela são a escalabilidade e desempenho. Se encontrar a que modelagem de relações de muitos que comprometem o desempenho e escalabilidade da sua solução, deve se perguntar se é necessário criar todas as relações de dados em seu design de tabela. Poderá conseguir simplificar o design e melhorar a escalabilidade e desempenho da sua solução se permitir que a aplicação cliente realizar qualquer uniões necessárias.  

Por exemplo, se tiver pequenas tabelas que contêm dados que não são alterados muitas vezes, em seguida, pode obter estes dados uma vez e cache no cliente. Isto pode evitar idas e voltas repetidas para obter os mesmos dados. Nos exemplos que vimos neste guia, o conjunto de departamentos em uma pequena organização provavelmente será pequeno e mudará raramente, tornando-o um bom candidato a dados que o aplicativo cliente pode baixar uma vez e armazenar em cache como dados de pesquisa.  

## <a name="inheritance-relationships"></a>Relações de herança
Se a aplicação cliente utiliza um conjunto de classes que fazem parte de uma relação de herança para representar as entidades comerciais, pode facilmente manter essas entidades no serviço tabela. Por exemplo, pode ter o seguinte conjunto de classes definidas na aplicação de cliente em que **pessoa** é uma classe abstrata.

![Classe de pessoa abstrata](media/storage-table-design-guide/storage-table-design-IMAGE03.png)

Pode manter instâncias das duas classes concretas no serviço de tabela com uma única tabela de pessoa com entidades aquela aparência como esta:  

![Tabela Person](media/storage-table-design-guide/storage-table-design-IMAGE04.png)

Para obter mais informações sobre como trabalhar com vários tipos de entidade na mesma tabela no código do cliente, consulte a seção trabalhando com tipos de entidade heterogêneas, mais adiante neste guia. Esta opção fornece exemplos de como a reconhecer o tipo de entidade no código do cliente.  


## <a name="next-steps"></a>Passos seguintes

- [Padrões de design de tabela](table-storage-design-patterns.md)
- [Design para consulta](table-storage-design-for-query.md)
- [Criptografar dados da tabela](table-storage-design-encrypt-data.md)
- [Design para modificação de dados](table-storage-design-for-modification.md)
