---
title: Relações de modelação em design de armazenamento de mesa Azure [ Microsoft Docs
description: Compreenda o processo de modelação ao conceber a sua solução de armazenamento de mesa.
services: storage
author: MarkMcGeeAtAquent
ms.service: storage
ms.topic: article
ms.date: 04/23/2018
ms.author: sngun
ms.subservice: tables
ms.openlocfilehash: 25082c107fbc0feeb533aa2b4fc56cff960e778d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75457555"
---
# <a name="modeling-relationships"></a>Modelar relações
Este artigo discute o processo de modelação para ajudá-lo a projetar as suas soluções de armazenamento de mesa Azure.

Construir modelos de domínio é um passo fundamental na conceção de sistemas complexos. Normalmente, utiliza-se o processo de modelação para identificar entidades e as relações entre elas como forma de compreender o domínio do negócio e informar o design do seu sistema. Esta secção centra-se na forma como pode traduzir alguns dos tipos de relacionamento comuns encontrados em modelos de domínio para desenhos para o serviço De Mesa. O processo de mapeamento de um modelo de dados lógico para um modelo de dados baseado no NoSQL físico é diferente do utilizado na conceção de uma base de dados relacional. O design de bases de dados relacionais normalmente assume um processo de normalização de dados otimizado para minimizar o despedimento – e uma capacidade de consulta declarativa que abstrae como funciona a implementação do funcionamento da base de dados.  

## <a name="one-to-many-relationships"></a>Relações de um a muitas
As relações entre objetos de domínio de negócio soam frequentemente: por exemplo, um departamento tem muitos colaboradores. Existem várias formas de implementar relações de um a muitas na Tabela de serviços cada um com prós e contras que podem ser relevantes para o cenário particular.  

Considere o exemplo de uma grande empresa multinacional com dezenas de milhares de departamentos e entidades de funcionários onde cada departamento tem muitos funcionários e cada funcionário como associado a um departamento específico. Uma abordagem é armazenar entidades separadas de departamentos e empregados, como estas:  


![Armazenar departamento separado e entidades de empregados](media/storage-table-design-guide/storage-table-design-IMAGE01.png)

Este exemplo mostra uma relação implícita entre os tipos baseados no valor **PartitionKey.** Cada departamento pode ter muitos empregados.  

Este exemplo mostra também uma entidade de departamento e as suas entidades colaboradoras relacionadas na mesma divisão. Pode optar por utilizar diferentes divisórias, tabelas ou até mesmo contas de armazenamento para os diferentes tipos de entidades.  

Uma abordagem alternativa é desnormalizar os seus dados e armazenar apenas entidades de empregados com dados dedepartamentodesdes, como mostra o exemplo seguinte. Neste cenário em particular, esta abordagem desnormalizada pode não ser a melhor se tiver a obrigação de poder alterar os detalhes de um gestor de departamento porque para isso precisa atualizar todos os colaboradores do departamento.  

![Entidade colaboradora](media/storage-table-design-guide/storage-table-design-IMAGE02.png)

Para mais informações, consulte o [padrão de Desnormalização](table-storage-design-patterns.md#denormalization-pattern) mais tarde neste guia.  

A tabela seguinte resume os prós e os contras de cada uma das abordagens acima descritas para armazenar entidades de funcionários e departamentos que têm uma relação de um a muitos. Deve também considerar a frequência com que espera realizar várias operações: pode ser aceitável ter um design que inclua uma operação dispendiosa se essa operação acontecer apenas com pouca frequência.  

<table>
<tr>
<th>Abordagem</th>
<th>Vantagens</th>
<th>Contras</th>
</tr>
<tr>
<td>Tipos de entidades separados, mesma partição, mesma tabela</td>
<td>
<ul>
<li>Pode atualizar uma entidade do departamento com uma única operação.</li>
<li>Pode utilizar um EGT para manter a consistência se tiver a obrigação de modificar uma entidade de departamento sempre que atualizar/inserir/eliminar uma entidade colaboradora. Por exemplo, se mantiver uma contagem de empregados departamentais para cada departamento.</li>
</ul>
</td>
<td>
<ul>
<li>Você pode precisar de recuperar um empregado e uma entidade de departamento para algumas atividades de cliente.</li>
<li>As operações de armazenamento acontecem na mesma divisória. Em volumes de transações elevados, isto pode resultar num hotspot.</li>
<li>Não pode mover um empregado para um novo departamento usando um EGT.</li>
</ul>
</td>
</tr>
<tr>
<td>Tipos de entidades separados, divisórias ou tabelas ou contas de armazenamento</td>
<td>
<ul>
<li>Pode atualizar uma entidade de departamento ou entidade colaboradora com uma única operação.</li>
<li>Em volumes de transações elevados, isto pode ajudar a espalhar a carga por mais divisórias.</li>
</ul>
</td>
<td>
<ul>
<li>Você pode precisar de recuperar um empregado e uma entidade de departamento para algumas atividades de cliente.</li>
<li>Não é possível utilizar EGTs para manter a consistência quando atualiza/insere/elimina um funcionário e atualiza um departamento. Por exemplo, atualizar uma contagem de empregados numa entidade de departamento.</li>
<li>Não pode mover um empregado para um novo departamento usando um EGT.</li>
</ul>
</td>
</tr>
<tr>
<td>Desnormalizar em tipo de entidade única</td>
<td>
<ul>
<li>Pode recuperar toda a informação que precisar com um único pedido.</li>
</ul>
</td>
<td>
<ul>
<li>Pode ser dispendioso manter a consistência se precisar de atualizar as informações do departamento (isto exigiria que atualizasse todos os colaboradores de um departamento).</li>
</ul>
</td>
</tr>
</table>

A forma como escolhe entre estas opções, e qual dos prós e contras são mais significativos, depende dos seus cenários específicos de candidatura. Por exemplo, com que frequência modifica as entidades do departamento; fazer todas as suas consultas de empregado sofres as informações adicionais do departamento; Quão perto está dos limites de escalabilidade das suas divisórias ou da sua conta de armazenamento?  

## <a name="one-to-one-relationships"></a>Relacionamentos um-para-um
Os modelos de domínio podem incluir relações um-a-um entre entidades. Se precisa implementar uma relação um-a-um no serviço Mesa, também deve escolher como ligar as duas entidades relacionadas quando precisa de recuperar as duas. Este link pode ser implícito, com base numa convenção nos valores-chave, ou explícito, armazenando um link sob a forma de valores **PartitionKey** e **RowKey** em cada entidade à sua entidade coligada. Para uma discussão sobre se deve armazenar as entidades relacionadas na mesma partilha, consulte a secção [Relações One-a-many](#one-to-many-relationships).  

Existem também considerações de implementação que podem levá-lo a implementar relações um-a-um no serviço tabela:  

* Manuseamento de grandes entidades (para mais informações, consulte [O Padrão das Grandes Entidades).](table-storage-design-patterns.md#large-entities-pattern)  
* Implementação de controlos de acesso (para mais informações, consulte controlar o acesso com assinaturas de acesso partilhado).  

## <a name="join-in-the-client"></a>Junte-se ao cliente
Embora existam formas de modelar relações no serviço Mesa, não deve esquecer que as duas principais razões para a utilização do serviço Mesa são a escalabilidade e desempenho. Se descobrir que está a modelar muitas relações que comprometem o desempenho e a escalabilidade da sua solução, deve perguntar-se se é necessário construir todas as relações de dados no design da sua tabela. Poderá simplificar o design e melhorar a escalabilidade e desempenho da sua solução se permitir que a sua aplicação de cliente realize as juntas necessárias.  

Por exemplo, se tiver pequenas tabelas que contenham dados que não mudam frequentemente, então pode recuperar estes dados uma vez e cache-lo no cliente. Isto pode evitar viagens repetidas para recuperar os mesmos dados. Nos exemplos que analisámos neste guia, o conjunto de departamentos de uma pequena organização é provável que seja pequeno e mude pouco frequentemente tornando-o um bom candidato para dados que a aplicação do cliente pode descarregar uma vez e cache como procurar dados.  

## <a name="inheritance-relationships"></a>Relações de herança
Se a sua aplicação de cliente utilizar um conjunto de classes que fazem parte de uma relação de herança para representar entidades empresariais, pode facilmente persistir essas entidades no serviço Mesa. Por exemplo, pode ter o seguinte conjunto de aulas definidas na sua aplicação de cliente onde a **Pessoa** é uma classe abstrata.

![Classe Pessoa Abstrata](media/storage-table-design-guide/storage-table-design-IMAGE03.png)

Pode persistir casos das duas classes concretas no serviço table utilizando uma tabela única pessoa usando entidades que se parecem com este aspeto:  

![Tabela de pessoas](media/storage-table-design-guide/storage-table-design-IMAGE04.png)

Para obter mais informações sobre o trabalho com vários tipos de entidades na mesma tabela no código do cliente, consulte a secção Trabalhar com tipos de entidades heterogéneas mais tarde neste guia. Isto fornece exemplos de como reconhecer o tipo de entidade no código do cliente.  


## <a name="next-steps"></a>Passos seguintes

- [Padrões de design da tabela](table-storage-design-patterns.md)
- [Design das consultas](table-storage-design-for-query.md)
- [Encriptar dados de tabela](table-storage-design-encrypt-data.md)
- [Design da modificação de dados](table-storage-design-for-modification.md)
