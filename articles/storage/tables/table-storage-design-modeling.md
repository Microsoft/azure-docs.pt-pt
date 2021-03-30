---
title: Relacionamentos de modelação em design de armazenamento de mesa Azure | Microsoft Docs
description: Compreenda o processo de modelação ao conceber a sua solução de armazenamento de mesa Azure. Leia sobre relacionamentos de um a muitos, um para um, e relações de herança.
services: storage
ms.service: storage
author: tamram
ms.author: tamram
ms.topic: article
ms.date: 04/23/2018
ms.subservice: tables
ms.openlocfilehash: 3023b478ef7a4aaf6d9239e997bdf63282b56210
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "88271197"
---
# <a name="modeling-relationships"></a>Modelar relações
Este artigo discute o processo de modelação para ajudá-lo a desenhar as suas soluções de armazenamento de Mesa Azure.

Os modelos de domínio de construção são um passo fundamental na conceção de sistemas complexos. Normalmente, utiliza-se o processo de modelação para identificar entidades e as relações entre elas como forma de compreender o domínio do negócio e informar o design do seu sistema. Esta secção centra-se em como pode traduzir alguns dos tipos de relacionamento comuns encontrados em modelos de domínio para desenhos para o serviço Table. O processo de mapeamento de um modelo de dados lógico para um modelo de dados baseado no NoSQL físico é diferente do utilizado na conceção de uma base de dados relacional. O design de bases de dados relacionais normalmente assume um processo de normalização de dados otimizado para minimizar a redundância – e uma capacidade de consulta declarativa que abstrata como funciona a implementação do funcionamento da base de dados.  

## <a name="one-to-many-relationships"></a>Relacionamentos de um a muitos
Relacionamentos entre objetos de domínio de negócios ocorrem frequentemente: por exemplo, um departamento tem muitos funcionários. Existem várias formas de implementar relações one-to-many no serviço Tabela cada um com prós e contras que podem ser relevantes para o cenário particular.  

Considere o exemplo de uma grande corporação multinacional com dezenas de milhares de departamentos e entidades de funcionários onde cada departamento tem muitos funcionários e cada funcionário como associado a um departamento específico. Uma abordagem é armazenar departamentos separados e entidades de empregados como estes:  


![Armazenar departamento separado e entidades de empregados](media/storage-table-design-guide/storage-table-design-IMAGE01.png)

Este exemplo mostra uma relação implícita entre os tipos baseados no valor **PartitionKey.** Cada departamento pode ter muitos empregados.  

Este exemplo mostra também uma entidade de departamento e as suas entidades associadas na mesma divisão. Pode optar por utilizar diferentes divisórias, tabelas ou até mesmo contas de armazenamento para os diferentes tipos de entidades.  

Uma abordagem alternativa é desnormalizar os seus dados e armazenar apenas entidades de colaboradores com dados desnormalizados do departamento, como mostra o exemplo seguinte. Neste cenário específico, esta abordagem desnormalizada pode não ser a melhor se tiver a obrigação de poder alterar os detalhes de um gerente de departamento porque para isso precisa atualizar todos os funcionários do departamento.  

![Entidade dos trabalhadores](media/storage-table-design-guide/storage-table-design-IMAGE02.png)

Para mais informações, consulte o [padrão de desnormalização](table-storage-design-patterns.md#denormalization-pattern) mais tarde neste guia.  

A tabela seguinte resume os prós e os contras de cada uma das abordagens descritas acima para armazenar entidades de empregados e departamentos que têm uma relação de um para muitos. Deve também considerar a frequência com que espera realizar várias operações: pode ser aceitável ter um design que inclua uma operação dispendiosa se essa operação só acontecer com pouca frequência.  

<table>
<tr>
<th>Abordagem</th>
<th>Vantagens</th>
<th>Desvantagens</th>
</tr>
<tr>
<td>Tipos de entidades separadas, mesma divisória, mesma tabela</td>
<td>
<ul>
<li>Pode atualizar uma entidade do departamento com uma única operação.</li>
<li>Pode utilizar uma Transação do Grupo Entity* (EGT) para manter a consistência se tiver a obrigação de modificar uma entidade de departamento sempre que atualizar/inserir/eliminar uma entidade do colaborador. Por exemplo, se mantiver uma contagem de funcionários do departamento para cada departamento.</li>
</ul>
</td>
<td>
<ul>
<li>Poderá ter de recuperar um funcionário e uma entidade do departamento para algumas atividades do cliente.</li>
<li>As operações de armazenamento acontecem na mesma divisória. Em volumes de transações elevados, isto pode resultar num hotspot.</li>
<li>Não pode transferir um empregado para um novo departamento usando um EGT.</li>
</ul>
</td>
</tr>
<tr>
<td>Tipos de entidades separadas, diferentes divisórias ou tabelas ou contas de armazenamento</td>
<td>
<ul>
<li>Pode atualizar uma entidade do departamento ou entidade de empregados com uma única operação.</li>
<li>Em volumes de transações elevados, isto pode ajudar a espalhar a carga por mais divisórias.</li>
</ul>
</td>
<td>
<ul>
<li>Poderá ter de recuperar um funcionário e uma entidade do departamento para algumas atividades do cliente.</li>
<li>Não é possível utilizar EGTs para manter a consistência quando atualiza/insere/apaga um funcionário e atualiza um departamento. Por exemplo, atualizar a contagem de funcionários numa entidade do departamento.</li>
<li>Não pode transferir um empregado para um novo departamento usando um EGT.</li>
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
<li>Pode ser caro manter a consistência se precisar de atualizar as informações do departamento (isto exigiria que atualizasse todos os funcionários de um departamento).</li>
</ul>
</td>
</tr>
</table>

*Para mais informações, consulte [As Transações do Grupo Entity](table-storage-design.md#entity-group-transactions)  


A forma como escolhes entre estas opções, e quais dos prós e contras são mais significativos, depende dos teus cenários específicos de aplicação. Por exemplo, com que frequência modifica entidades de departamento; fazer todas as suas consultas de funcionários precisa de informações adicionais do departamento; Qual a sua proximidade com os limites de escalabilidade das suas divisórias ou da sua conta de armazenamento?  

## <a name="one-to-one-relationships"></a>Relações um-para-um
Os modelos de domínio podem incluir relações um-para-um entre entidades. Se precisa implementar uma relação um-para-um no serviço Tabela, também deve escolher como ligar as duas entidades relacionadas quando precisa de as recuperar. Este link pode ser implícito, baseado numa convenção nos valores-chave, ou explícito, armazenando um link sob a forma de valores **PartitionKey** e **RowKey** em cada entidade à sua entidade relacionada. Para uma discussão sobre se deve armazenar as entidades relacionadas na mesma divisão, consulte a secção [Um a muitos relacionamentos](#one-to-many-relationships).  

Existem também considerações de implementação que podem levá-lo a implementar relações um-para-um no serviço tabela:  

* Manusear grandes entidades (para mais informações, consulte [o Padrão das Grandes Entidades).](table-storage-design-patterns.md#large-entities-pattern)  
* Implementando controlos de acesso (para obter mais informações, consulte controlar o acesso com assinaturas de acesso partilhado).  

## <a name="join-in-the-client"></a>Junte-se ao cliente
Embora existam formas de modelar relações no serviço Table, não deve esquecer que as duas principais razões para usar o serviço Table são a escalabilidade e desempenho. Se você acha que você está modelando muitas relações que comprometem o desempenho e escalabilidade da sua solução, você deve perguntar a si mesmo se é necessário construir todas as relações de dados no seu design de mesa. Poderá simplificar o design e melhorar a escalabilidade e desempenho da sua solução se deixar que a sua aplicação ao cliente execute as junções necessárias.  

Por exemplo, se tiver pequenas tabelas que contenham dados que não mudam frequentemente, então pode recuperar estes dados uma vez e cache-os no cliente. Isto pode evitar repetidas velhagens para recuperar os mesmos dados. Nos exemplos que analisámos neste guia, o conjunto de departamentos de uma pequena organização é provável que seja pequeno e mude frequentemente, tornando-o um bom candidato a dados que a aplicação do cliente pode descarregar uma vez e cache como olhar para os dados.  

## <a name="inheritance-relationships"></a>Relações de herança
Se a sua aplicação ao cliente utilizar um conjunto de classes que fazem parte de uma relação de herança para representar entidades empresariais, pode facilmente persistir essas entidades no serviço Mesa. Por exemplo, pode ter o seguinte conjunto de aulas definidas na sua aplicação de cliente onde **a Pessoa** é uma classe abstrata.

![Classe Pessoa Abstrata](media/storage-table-design-guide/storage-table-design-IMAGE03.png)

Pode persistir casos das duas classes de betão no serviço table usando uma única tabela pessoa usando entidades que se parecem com esta:  

![Mesa de pessoa](media/storage-table-design-guide/storage-table-design-IMAGE04.png)

Para obter mais informações sobre o trabalho com vários tipos de entidades na mesma tabela no código do cliente, consulte a secção Trabalhar com tipos de entidades heterogéneas mais tarde neste guia. Isto fornece exemplos de como reconhecer o tipo de entidade no código do cliente.  


## <a name="next-steps"></a>Passos seguintes

- [Padrões de design da tabela](table-storage-design-patterns.md)
- [Design das consultas](table-storage-design-for-query.md)
- [Criptografe dados de tabela](table-storage-design-encrypt-data.md)
- [Design da modificação de dados](table-storage-design-for-modification.md)
