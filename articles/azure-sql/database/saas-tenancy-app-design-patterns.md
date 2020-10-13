---
title: Padrões SaaS multi-inquilinos
description: Conheça os requisitos e padrões comuns de arquitetura de dados de software multi-inquilino como uma aplicação de base de dados de serviço (SaaS) que funciona no ambiente de nuvem Azure.
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/25/2019
ms.custom: seoapril2019, sqldbrb=1
ms.openlocfilehash: 493c18efa8bad2e366424c8c8130754ce0098913
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85250718"
---
# <a name="multi-tenant-saas-database-tenancy-patterns"></a>Padrões de arrendamento de base de dados SaaS multi-inquilinos
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Este artigo descreve os vários modelos de arrendamento disponíveis para uma aplicação SaaS multi-arrendatário.

Ao conceber uma aplicação SaaS multi-arrendatário, deve escolher cuidadosamente o modelo de arrendamento que melhor se adequa às necessidades da sua aplicação.  Um modelo de arrendamento determina como os dados de cada inquilino são mapeados para armazenamento.  A sua escolha do modelo de arrendamento tem impacto no design e gestão de aplicações.  Mudar para um modelo diferente mais tarde é por vezes dispendioso.

## <a name="a-saas-concepts-and-terminology"></a>A. Conceitos e terminologia de SaaS

No modelo Software como Um Serviço (SaaS), a sua empresa não vende *licenças* para o seu software. Em vez disso, cada cliente faz pagamentos de renda à sua empresa, fazendo de cada cliente um *inquilino* da sua empresa.

Em troca do pagamento da renda, cada inquilino tem acesso aos seus componentes de aplicação SaaS, e tem os seus dados armazenados no sistema SaaS.

O termo *modelo de arrendamento* refere-se à forma como os dados armazenados dos inquilinos são organizados:

- *Arrendamento único:* &nbsp; Cada base de dados armazena dados de apenas um inquilino.
- *Multi-arrendamento:* &nbsp; Cada base de dados armazena dados de vários inquilinos separados (com mecanismos para proteger a privacidade dos dados).
- Os modelos de arrendamento híbrido também estão disponíveis.

## <a name="b-how-to-choose-the-appropriate-tenancy-model"></a>B. Como escolher o modelo de arrendamento apropriado

Em geral, o modelo de arrendamento não afeta a função de uma aplicação, mas provavelmente impacta outros aspetos da solução global.  São utilizados os seguintes critérios para avaliar cada um dos modelos:

- **Escalabilidade:**
    - Número de inquilinos.
    - Armazenamento por inquilino.
    - Armazenamento em conjunto.
    - Carga de trabalho.

- **Isolamento do inquilino:** &nbsp; Isolamento de dados e desempenho (se a carga de trabalho de um inquilino impacta outros).

- **Custo por inquilino:** &nbsp; Custos da base de dados.

- **Complexidade do desenvolvimento:**
    - Alterações no esquema.
    - Alterações nas consultas (requeridas pelo padrão).

- **Complexidade operacional:**
    - Monitorização e gestão do desempenho.
    - Gestão de schema.
    - Restaurar um inquilino.
    - Recuperação após desastre.

- **Personalização:** &nbsp; Facilidade de apoiar personalizações de esquemas que sejam específicas do arrendatário ou específicas da classe do inquilino.

A discussão sobre o arrendamento está focada na camada de *dados.*  Mas considere por um momento a camada de *aplicação.*  A camada de aplicação é tratada como uma entidade monolítica.  Se dividir a aplicação em muitos componentes pequenos, a sua escolha de modelo de arrendamento pode mudar.  Você poderia tratar alguns componentes de forma diferente de outros no que diz respeito tanto ao arrendamento como à tecnologia de armazenamento ou plataforma usada.

## <a name="c-standalone-single-tenant-app-with-single-tenant-database"></a>C. App autónoma de inquilino único com base de dados de inquilino único

#### <a name="application-level-isolation"></a>Isolamento do nível de aplicação

Neste modelo, toda a aplicação é instalada repetidamente, uma vez para cada inquilino.  Cada instância da aplicação é um caso autónomo, por isso nunca interage com qualquer outro caso autónomo.  Cada instância da app tem apenas um inquilino, e, portanto, precisa apenas de uma base de dados.  O inquilino tem a base de dados só para si.

![Design de app autónoma com exatamente uma base de dados de inquilinos únicos.][image-standalone-app-st-db-111a]

Cada instância de aplicação é instalada num grupo de recursos Azure separado.  O grupo de recursos pode pertencer a uma subscrição que é propriedade do fornecedor de software ou do inquilino.  Em qualquer dos casos, o vendedor pode gerir o software para o inquilino.  Cada instância de aplicação está configurada para ligar à base de dados correspondente.

Cada base de dados de inquilinos é implantada como uma única base de dados.  Este modelo proporciona o maior isolamento da base de dados.  Mas o isolamento requer que sejam atribuídos recursos suficientes a cada base de dados para lidar com as suas cargas máximas.  Neste caso, importa que piscinas elásticas não possam ser utilizadas para bases de dados implantadas em diferentes grupos de recursos ou para diferentes subscrições.  Esta limitação faz deste modelo de app autónomo de inquilino único a solução mais cara de uma perspetiva global de custos de base de dados.

#### <a name="vendor-management"></a>Gestão do fornecedor

O fornecedor pode aceder a todas as bases de dados em todas as instâncias de aplicações autónomas, mesmo que as instâncias da aplicação sejam instaladas em diferentes subscrições de inquilinos.  O acesso é alcançado através de ligações SQL.  Este acesso transversal pode permitir ao fornecedor centralizar a gestão de esquemas e consultas de bases de dados cruzadas para fins de reporte ou análise.  Se este tipo de gestão centralizada for desejada, deve ser implantado um catálogo que mapeia identificadores de inquilinos para URIs de base de dados.  A Azure SQL Database fornece uma biblioteca de fragmentos que é usada em conjunto para fornecer um catálogo.  A biblioteca de fragmentos é formalmente denominada a Biblioteca de [Clientes de Base de Dados Elástica.][docu-elastic-db-client-library-536r]

## <a name="d-multi-tenant-app-with-database-per-tenant"></a>D. App multi-inquilino com base de dados por inquilino

Este próximo padrão usa uma aplicação multi-inquilino com muitas bases de dados, todas sendo bases de dados de inquilinos únicos.  Uma nova base de dados é disponibilizada para cada novo inquilino.  O nível de aplicação é *escalado* verticalmente adicionando mais recursos por nó.  Ou a aplicação é *dimensionada* horizontalmente adicionando mais nós.  O dimensionamento baseia-se na carga de trabalho e é independente do número ou escala das bases de dados individuais.

![Design de app multi-inquilino com base de dados por inquilino.][image-mt-app-db-per-tenant-132d]

#### <a name="customize-for-a-tenant"></a>Personalizar para um inquilino

Tal como o padrão de aplicações autónomos, o uso de bases de dados de inquilinos únicos dá um forte isolamento ao inquilino.  Em qualquer app cujo modelo especifica apenas bases de dados de inquilinos individuais, o esquema para qualquer base de dados dada pode ser personalizado e otimizado para o seu inquilino.  Esta personalização não afeta outros inquilinos na app. Talvez um inquilino precise de dados para além dos campos básicos de dados de que todos os inquilinos precisam.  Além disso, o campo de dados extra pode precisar de um índice.

Com base de dados por inquilino, personalizar o esquema para um ou mais inquilinos individuais é fácil de alcançar.  O vendedor de aplicações deve conceber procedimentos para gerir cuidadosamente as personalizações de esquemas em escala.

#### <a name="elastic-pools"></a>Conjuntos elásticos

Quando as bases de dados são implantadas no mesmo grupo de recursos, podem ser agrupadas em piscinas elásticas.  As piscinas fornecem uma forma rentável de partilhar recursos em muitas bases de dados.  Esta opção de piscina é mais barata do que exigir que cada base de dados seja grande o suficiente para acomodar os picos de utilização que experimenta.  Embora as bases de dados partilhadas partilhem o acesso aos recursos, ainda podem alcançar um elevado grau de isolamento de desempenho.

![Design de app multi-inquilino com base de dados por inquilino, usando piscina elástica.][image-mt-app-db-per-tenant-pool-153p]

A Azure SQL Database fornece as ferramentas necessárias para configurar, monitorizar e gerir a partilha.  Tanto as métricas de desempenho ao nível da piscina como as métricas de desempenho ao nível da base de dados estão disponíveis no portal Azure e através de registos do Azure Monitor.  As métricas podem dar grandes insights sobre o desempenho agregado e específico do inquilino.  Bases de dados individuais podem ser movidas entre piscinas para fornecer recursos reservados a um inquilino específico.  Estas ferramentas permitem-lhe garantir um bom desempenho de forma rentável.

#### <a name="operations-scale-for-database-per-tenant"></a>Escala de operações para base de dados por inquilino

A Azure SQL Database tem muitas funcionalidades de gestão projetadas para gerir um grande número de bases de dados em escala, assim como mais de 100.000 bases de dados.  Estas características tornam plausível o padrão de base de dados por inquilino.

Por exemplo, suponha que um sistema tenha uma base de dados de 1000 inquilinos como a sua única base de dados.  A base de dados pode ter 20 índices.  Se o sistema se converter a ter 1000 bases de dados de inquilinos individuais, a quantidade de índices sobe para 20.000.  Na Base de Dados Azure SQL como parte da [sintonização automática,][docu-sql-db-automatic-tuning-771a]as funções de indexação automática são ativadas por predefinição.  A indexação automática gere para si todos os 20.000 índices e as suas otimizações contínuas de criação e queda.  Estas ações automatizadas ocorrem dentro de uma base de dados individual, e não são coordenadas ou restringidas por ações semelhantes noutras bases de dados.  A indexação automática trata os índices de forma diferente numa base de dados ocupada do que numa base de dados menos movimentada.  Este tipo de personalização da gestão de índices seria impraticável na escala de base de dados por inquilino se esta enorme tarefa de gestão tivesse de ser feita manualmente.

Outras funcionalidades de gestão que escalam bem incluem os seguintes:

- Reforços embutidos.
- Elevada disponibilidade.
- Encriptação no disco.
- Telemetria de desempenho.

#### <a name="automation"></a>Automatização

As operações de gestão podem ser escritas e oferecidas através de um modelo [de devotos.][http-visual-studio-devops-485m]  As operações podem mesmo ser automatizadas e expostas na aplicação.

Por exemplo, você poderia automatizar a recuperação de um único inquilino para um ponto mais cedo no tempo.  A recuperação só precisa de restaurar a base de dados de um único inquilino que armazena o inquilino.  Esta restauração não tem qualquer impacto sobre outros inquilinos, o que confirma que as operações de gestão estão ao nível finamente granular de cada inquilino individual.

## <a name="e-multi-tenant-app-with-multi-tenant-databases"></a>E. App multi-inquilino com bases de dados multi-inquilinos

Outro padrão disponível é armazenar muitos inquilinos numa base de dados multi-inquilinos.  A aplicação pode ter várias bases de dados de vários inquilinos.  O esquema de uma base de dados multi-inquilino deve ter uma ou mais colunas de identificador de inquilinos para que os dados de qualquer inquilino possam ser recuperados seletivamente.  Além disso, o esquema pode exigir algumas mesas ou colunas que são usadas apenas por um subconjunto de inquilinos.  No entanto, o código estático e os dados de referência são armazenados apenas uma vez e são partilhados por todos os inquilinos.

#### <a name="tenant-isolation-is-sacrificed"></a>O isolamento dos inquilinos é sacrificado

*Dados:* &nbsp; Uma base de dados multi-inquilinos necessariamente sacrifica o isolamento dos inquilinos.  Os dados de vários inquilinos são armazenados juntos numa base de dados.  Durante o desenvolvimento, certifique-se de que as consultas nunca expõem dados de mais de um inquilino.  A SQL Database suporta [a segurança ao nível da linha,][docu-sql-svr-db-row-level-security-947w]que pode impor que os dados devolvidos de uma consulta sejam examinados a um único inquilino.

*Processamento:* &nbsp; Uma base de dados multi-inquilino partilha recursos de computação e armazenamento em todos os seus inquilinos.  A base de dados no seu conjunto pode ser monitorizada para garantir que está a funcionar de forma aceitável.  No entanto, o sistema Azure não tem forma integrada de monitorizar ou gerir a utilização destes recursos por um inquilino individual.  Assim, a base de dados multi-inquilinos tem um risco acrescido de encontrar vizinhos barulhentos, onde a carga de trabalho de um inquilino hiperativo impacta a experiência de desempenho de outros inquilinos na mesma base de dados.  Uma monitorização adicional ao nível da aplicação poderia monitorizar o desempenho ao nível do inquilino.

#### <a name="lower-cost"></a>Custo mais baixo

Em geral, as bases de dados de vários inquilinos têm o custo mais baixo por inquilino.  Os custos de recursos de uma única base de dados são inferiores aos de um conjunto elástico de tamanho equivalente.  Além disso, para cenários em que os inquilinos precisam apenas de armazenamento limitado, potencialmente milhões de inquilinos poderiam ser armazenados numa única base de dados.  Nenhuma piscina elástica pode conter milhões de bases de dados.  No entanto, uma solução que contenha 1000 bases de dados por pool, com 1000 piscinas, poderia atingir a escala de milhões com o risco de se tornar inflexível para gerir.

Duas variações de um modelo de base de dados multi-inquilinos são discutidas no que se segue, com o modelo de multi-inquilinos em pedaços sendo o mais flexível e escalável.

## <a name="f-multi-tenant-app-with-a-single-multi-tenant-database"></a>F. App multi-inquilino com uma única base de dados multi-inquilino

O padrão mais simples da base de dados multi-inquilinos usa uma única base de dados para hospedar dados para todos os inquilinos.  À medida que mais inquilinos são adicionados, a base de dados é ampliada com mais recursos de armazenamento e computação.  Esta escala pode ser tudo o que é necessário, embora haja sempre um limite de escala final.  No entanto, muito antes de esse limite ser atingido, a base de dados torna-se inflexível de gerir.

As operações de gestão focadas nos inquilinos individuais são mais complexas de implementar numa base de dados multi-inquilinos.  E à escala estas operações podem tornar-se inaceitavelmente lentas.  Um exemplo é um restabelecemento pontual dos dados para apenas um inquilino.

## <a name="g-multi-tenant-app-with-sharded-multi-tenant-databases"></a>G. App multi-inquilino com bases de dados de vários inquilinos com cerca de várias inquilinas

A maioria das aplicações da SaaS acedem aos dados de apenas um inquilino de cada vez.  Este padrão de acesso permite que os dados do inquilino sejam distribuídos por várias bases de dados ou fragmentos, onde todos os dados para qualquer inquilino estão contidos num fragmento.  Combinado com um padrão de base de dados de vários inquilinos, um modelo de sharded permite uma escala quase ilimitada.

![Design de app multi-inquilino com bases de dados de multi-inquilinos.][image-mt-app-sharded-mt-db-174s]

#### <a name="manage-shards"></a>Gerir fragmentos

O caco adiciona complexidade tanto ao design como à gestão operacional.  É necessário um catálogo para manter o mapeamento entre inquilinos e bases de dados.  Além disso, são necessários procedimentos de gestão para gerir os fragmentos e a população inquilina.  Por exemplo, os procedimentos devem ser concebidos para adicionar e remover fragmentos, e para mover os dados dos inquilinos entre fragmentos.  Uma maneira de escalar é adicionar um novo fragmento e povoá-lo com novos inquilinos.  Noutras alturas, pode dividir-se um fragmento densamente povoado em dois fragmentos menos densamente povoados.  Depois de vários inquilinos terem sido movidos ou descontinuados, você pode fundir fragmentos pouco povoados juntos.  A fusão resultaria numa utilização mais económica dos recursos.  Os inquilinos também podem ser movidos entre fragmentos para equilibrar cargas de trabalho.

A SQL Database fornece uma ferramenta de split/merge que funciona em conjunto com a biblioteca de fragmentos e a base de dados do catálogo.  A aplicação fornecida pode dividir e fundir fragmentos, e pode mover dados de inquilinos entre fragmentos.  A aplicação também mantém o catálogo durante estas operações, marcando os inquilinos afetados como offline antes de movê-los.  Após a mudança, a aplicação atualiza novamente o catálogo com o novo mapeamento, e marca o inquilino como novamente online.

#### <a name="smaller-databases-more-easily-managed"></a>Bases de dados mais pequenas geridas mais facilmente

Ao distribuir inquilinos por várias bases de dados, a solução de multi-inquilinos em pedaços resulta em bases de dados mais pequenas que são mais facilmente geridas.  Por exemplo, restaurar um inquilino específico para um ponto anterior no tempo agora envolve restaurar uma única base de dados menor a partir de uma cópia de segurança, em vez de uma base de dados maior que contém todos os inquilinos. O tamanho da base de dados, e o número de inquilinos por base de dados, podem ser escolhidos para equilibrar a carga de trabalho e os esforços de gestão.

#### <a name="tenant-identifier-in-the-schema"></a>Identificador de inquilino no esquema

Dependendo da abordagem de fragmento utilizada, podem ser impostas restrições adicionais ao esquema da base de dados.  A aplicação sql database split/merge requer que o esquema inclua a chave de fragmentos, que normalmente é o identificador do inquilino.  O identificador de inquilinos é o elemento principal na chave primária de todas as mesas de cacos.  O identificador de inquilino permite que o pedido de divisão/fusão localize e mova rapidamente os dados associados a um inquilino específico.

#### <a name="elastic-pool-for-shards"></a>Piscina elástica para fragmentos

Bases de dados de vários inquilinos com cacidos podem ser colocadas em piscinas elásticas.  Em geral, ter muitas bases de dados de inquilinos únicos numa piscina é tão eficiente quanto ter muitos inquilinos em algumas bases de dados multi-inquilinos.  As bases de dados multi-inquilinos são vantajosas quando há um grande número de inquilinos relativamente inativos.

## <a name="h-hybrid-sharded-multi-tenant-database-model"></a>H. Modelo híbrido de base de dados multi-inquilinos

No modelo híbrido, todas as bases de dados têm o identificador de inquilino no seu esquema.  As bases de dados são todas capazes de armazenar mais do que um inquilino, e as bases de dados podem ser cacifadas.  No sentido do esquema, são todos bases de dados de vários inquilinos.  No entanto, na prática, algumas destas bases de dados contêm apenas um inquilino.  Independentemente disso, a quantidade de inquilinos armazenados numa determinada base de dados não tem qualquer efeito no esquema da base de dados.

#### <a name="move-tenants-around"></a>Mover inquilinos ao redor

A qualquer momento, você pode mover um inquilino particular para a sua própria base de dados multi-inquilinos.  E a qualquer momento, pode mudar de ideias e mudar o inquilino de volta para uma base de dados que contém vários inquilinos.  Você também pode atribuir um inquilino a nova base de dados de inquilino único quando você forte a nova base de dados.

O modelo híbrido brilha quando há grandes diferenças entre as necessidades de recursos de grupos de inquilinos identificáveis.  Por exemplo, suponha que os inquilinos que participam num julgamento gratuito não estão garantidos o mesmo elevado nível de desempenho que os inquilinos que subscrevem.  A política pode ser para os inquilinos na fase de teste livre serem armazenados numa base de dados multi-inquilinos que é partilhada entre todos os inquilinos de julgamento gratuito.  Quando um inquilino de teste gratuito subscreve o nível de serviço básico, o inquilino pode ser transferido para outra base de dados multi-inquilino que possa ter menos inquilinos.  Um assinante que pague o nível de serviço premium poderia ser transferido para a sua própria nova base de dados de inquilinos únicos.

#### <a name="pools"></a>Conjuntos

Neste modelo híbrido, as bases de dados de inquilinos individuais para inquilinos assinantes podem ser colocadas em piscinas de recursos para reduzir os custos de base de dados por inquilino.  Isto também é feito no modelo de base de dados por inquilino.

## <a name="i-tenancy-models-compared"></a>I. Modelos de arrendamento comparados

A tabela seguinte resume as diferenças entre os principais modelos de arrendamento.

| Medida | Aplicação autónoma | Base de dados por inquilino | Multi-inquilino sharded |
| :---------- | :------------- | :------------------ | :------------------- |
| Escala | Médio<br />1-100s | Muito alta <br />1-100.000s | Ilimitado<br />1-1.000.000s |
| Isolamento de inquilinos | Muito alta  | Alto | Baixo; exceto para qualquer único inquilino (que está sozinho em um MT db). |
| Custo da base de dados por inquilino | Alto; é dimensionado para picos. | Baixo; piscinas usadas. | O mais baixo, para pequenos inquilinos em MT DBs. |
| Monitorização e gestão de desempenho | Apenas por inquilino | Agregado + por inquilino | Agregado; embora seja por inquilino apenas para solteiros. |
| Complexidade do desenvolvimento | Baixo | Baixo | Médio; devido a estilhaços. |
| Complexidade operacional | Baixo-alto. Individualmente simples, complexo à escala. | Baixo-médio. Os padrões abordam a complexidade à escala. | Baixo-alto. A gestão individual de inquilinos é complexa. |
| &nbsp; ||||

## <a name="next-steps"></a>Passos seguintes

- [Implementar e explorar uma aplicação wingtip multi-inquilino que utiliza o modelo SaaS de base de dados por inquilino - Azure SQL Database][docu-sql-db-saas-tutorial-deploy-wingtip-db-per-tenant-496y]

- [Bem-vindos à app de arrendamento SaaS Azure SQL Database][docu-saas-tenancy-welcome-wingtip-tickets-app-384w]


<!--  Article link references.  -->

[http-visual-studio-devops-485m]: https://www.visualstudio.com/devops/

[docu-sql-svr-db-row-level-security-947w]: https://docs.microsoft.com/sql/relational-databases/security/row-level-security

[docu-elastic-db-client-library-536r]:elastic-database-client-library.md
[docu-sql-db-saas-tutorial-deploy-wingtip-db-per-tenant-496y]: saas-dbpertenant-get-started-deploy.md
[docu-sql-db-automatic-tuning-771a]:automatic-tuning-overview.md
[docu-saas-tenancy-welcome-wingtip-tickets-app-384w]: saas-tenancy-welcome-wingtip-tickets-app.md


<!--  Image references.  -->

[image-standalone-app-st-db-111a]: media/saas-tenancy-app-design-patterns/saas-standalone-app-single-tenant-database-11.png "Design de app autónoma com exatamente uma base de dados de inquilinos únicos."

[image-mt-app-db-per-tenant-132d]: media/saas-tenancy-app-design-patterns/saas-multi-tenant-app-database-per-tenant-13.png "Design de app multi-inquilino com base de dados por inquilino."

[image-mt-app-db-per-tenant-pool-153p]: media/saas-tenancy-app-design-patterns/saas-multi-tenant-app-database-per-tenant-pool-15.png "Design de app multi-inquilino com base de dados por inquilino, usando piscina elástica."

[image-mt-app-sharded-mt-db-174s]: media/saas-tenancy-app-design-patterns/saas-multi-tenant-app-sharded-multi-tenant-databases-17.png "Design de app multi-inquilino com bases de dados de multi-inquilinos."

