---
title: Padrões de SaaS de multi-inquilino - base de dados SQL do Azure | Documentos da Microsoft
description: Saiba mais sobre os requisitos e dados comuns padrões de arquitetura de software de multi-inquilino como um aplicativos de banco de dados do serviço (SaaS) que são executados no ambiente de cloud do Azure.
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MightyPen
ms.author: genemi
ms.reviewer: billgib, sstein
manager: craigg
ms.date: 01/25/2019
ms.openlocfilehash: d55d8f8f945e30402cb772c7e26b234e3e460698
ms.sourcegitcommit: c712cb5c80bed4b5801be214788770b66bf7a009
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/01/2019
ms.locfileid: "57217874"
---
# <a name="multi-tenant-saas-database-tenancy-patterns"></a>Padrões de inquilinos de bases de dados de SaaS de multi-inquilino

Ao conceber uma aplicação SaaS de multi-inquilino, precisa escolher cuidadosamente o modelo de inquilinos que melhor se adequa às necessidades da sua aplicação.  Um modelo de inquilinos determina como os dados de cada inquilino são mapeados para o armazenamento.  Sua escolha de design do aplicativo de impactos de modelo de inquilinos e de gestão.  Mudar para um modelo diferente mais tarde, às vezes, é dispendiosa.

Este artigo descreve os modelos de inquilinos alternativo.

## <a name="a-saas-concepts-and-terminology"></a>R. SaaS conceitos e terminologia

No Software como um modelo de serviço (SaaS), a sua empresa não vender *licenças* com o seu software. Em vez disso, cada cliente torna alugar pagamentos para a sua empresa, tornando a cada cliente um *inquilino* da sua empresa.

Ao pagar aluguel, cada inquilino recebe acesso para os componentes da aplicação SaaS e tem os dados armazenados no sistema SaaS.

O termo *modelo de inquilinos* refere-se para como os dados armazenados dos inquilinos são organizados:

- *Single-inquilinos:*&nbsp; Cada base de dados armazena os dados de apenas um inquilino.
- *Vários inquilinos:*&nbsp; Cada base de dados armazena os dados a partir de vários inquilinos separados (com mecanismos para proteger a privacidade de dados).
- Modelos de inquilinos híbridos também estão disponíveis.

## <a name="b-how-to-choose-the-appropriate-tenancy-model"></a>B. Como escolher o modelo de inquilinos adequados

Em geral, o modelo de inquilinos não afeta a função de um aplicativo, mas é provável que afeta os outros aspectos da solução geral.  Os seguintes critérios são utilizados para avaliar a cada um dos modelos:

- **Escalabilidade:**
    - Número de inquilinos.
    - Armazenamento por inquilino.
    - Armazenamento em forma agregada.
    - Carga de trabalho.

- **Isolamento de inquilino:**&nbsp; Isolamento de dados e o desempenho (se a carga de trabalho de um inquilino afeta outras pessoas).

- **Custo por inquilino:**&nbsp; Custos da base de dados.

- **Complexidade de desenvolvimento:**
    - Alterações ao esquema.
    - Alterações às consultas (requeridas pelo padrão).

- **Complexidade operacional:**
    - Monitorizar e gerir o desempenho.
    - Gestão de esquemas.
    - Restaurar um inquilino.
    - Recuperação após desastre.

- **Capacidade de personalização:**&nbsp; Facilidade de oferecer suporte a personalizações de esquema que são específicas do inquilino ou inquilinos específicos da classe.

A discussão de inquilinos enfoca a *dados* camada.  Mas considere por um momento a *aplicativo* camada.  A camada de aplicativo é tratada como uma entidade monolítica.  Se dividir o aplicativo em muitos componentes pequenos, à sua escolha do modelo de inquilinos podem ser alteradas.  Pode tratar alguns componentes diferente das outras pessoas em relação aos inquilinos e a tecnologia de armazenamento ou plataforma utilizada.

## <a name="c-standalone-single-tenant-app-with-single-tenant-database"></a>C. Aplicação de inquilino único autónoma com o banco de dados de inquilino único

#### <a name="application-level-isolation"></a>Isolamento em nível de aplicativo

Nesse modelo, toda a aplicação é instalada repetidamente, uma vez para cada inquilino.  Cada instância da aplicação é uma instância autónoma, para que ele nunca interage com qualquer outra instância autónoma.  Cada instância da aplicação com apenas um inquilino e, portanto, precisa apenas uma base de dados.  O inquilino tem a base de dados tudo para si próprio.

![Design de uma aplicação autónoma com exatamente um banco de dados de inquilino único.][image-standalone-app-st-db-111a]

Cada instância de aplicação está instalada num grupo de recursos do Azure.  O grupo de recursos pode pertencer a uma subscrição que é propriedade do fornecedor de software ou o inquilino.  Em ambos os casos, o fornecedor pode gerir o software para o inquilino.  Cada instância da aplicação está configurada para ligar à sua base de dados correspondente.

Cada base de dados do inquilino é implementado como uma base de dados.  Esse modelo oferece o maior isolamento de base de dados.  Mas o isolamento requer que os recursos suficientes ser atribuída a cada base de dados para lidar com picos de carga.  Aqui ele é importante que os conjuntos elásticos não podem ser utilizados para bases de dados implementadas em grupos de recursos diferente ou a subscrições diferentes.  Esta limitação faz com que esta aplicação de inquilino único autónomo, a solução mais cara da perspectiva de custo geral da base de dados de modelo.

#### <a name="vendor-management"></a>Gestão do fornecedor

O fornecedor pode aceder a todas as bases de dados em todas as instâncias de aplicação de autónomo, mesmo que as instâncias de aplicações são instaladas em subscrições de inquilino diferente.  O acesso é feito através de ligações de SQL.  Este acesso entre a instância pode ativar o fornecedor para centralizar a gestão de esquemas e consultas entre bases de dados para fins de relatórios ou análises.  Se esse tipo de gerenciamento centralizado for o pretendido, deve ser implantado um catálogo que mapeia identificadores de inquilino para URIs de base de dados.  Base de dados SQL do Azure fornece uma biblioteca de fragmentação que é utilizada em conjunto com a base de dados SQL para fornecer um catálogo.  A biblioteca de fragmentação antes é chamada do [biblioteca de clientes de base de dados elástica][docu-elastic-db-client-library-536r].

## <a name="d-multi-tenant-app-with-database-per-tenant"></a>D. Aplicação multi-inquilino com a base de dados por inquilino

Este padrão seguinte utiliza uma aplicação multi-inquilino com muitas bases de dados, sendo todas as bases de dados de inquilino único.  Uma nova base de dados é aprovisionada para cada novo inquilino.  A camada de aplicação é dimensionada *cópia* verticalmente ao adicionar mais recursos por nó.  Ou a aplicação é dimensionada *horizontalmente* horizontalmente ao adicionar mais nós.  O dimensionamento é baseado na carga de trabalho e é independente do número ou dimensionamento dos bancos de dados individuais.

![Design da aplicação multi-inquilino com a base de dados por inquilino.][image-mt-app-db-per-tenant-132d]

#### <a name="customize-for-a-tenant"></a>Personalizar para um inquilino

Como o padrão de aplicação autónoma, a utilização de bases de dados de inquilino único fornece isolamento de inquilino forte.  Em qualquer aplicação cujo modelo especifica apenas bases de dados de inquilino único, o esquema para qualquer um determinado banco de dados pode ser personalizado e otimizado para o seu inquilino.  Essa personalização não afeta os outros inquilinos na aplicação. Talvez um inquilino poderão precisar de dados para além dos campos de dados básica que precisam de todos os inquilinos.  Além disso, o campo de dados extra pode ter um índice.

Com a base de dados por inquilino, é simples conseguir personalizar o esquema para um ou mais inquilinos individuais.  O fornecedor do aplicativo tem de criar procedimentos para gerenciar cuidadosamente as personalizações de esquema em escala.

#### <a name="elastic-pools"></a>Conjuntos elásticos

Quando as bases de dados são implementadas no mesmo grupo de recursos, eles podem ser agrupados em conjuntos elásticos.  Os conjuntos oferecem uma maneira econômica de compartilhar recursos entre muitas bases de dados.  Esta opção de agrupamento é mais barata do que a necessidade de cada base de dados para ser suficientemente grande para acomodar os picos de utilização que experienciar.  Apesar de bases de dados agrupadas partilham o acesso aos recursos eles ainda podem alcançar um alto grau de isolamento do desempenho.

![Design da aplicação multi-inquilino com a base de dados por inquilino, utilizando o conjunto elástico.][image-mt-app-db-per-tenant-pool-153p]

Base de dados SQL do Azure fornece as ferramentas necessárias para configurar, monitorizar e gerir a partilha.  Ambas as métricas de desempenho da base de dados ao nível do conjunto e estão disponíveis no portal do Azure e através de registos do Azure Monitor.  As métricas podem fornecer excelentes conhecimentos sobre o desempenho de agregação e específico de inquilino.  Bases de dados individuais podem ser movidas entre conjuntos para fornecer recursos reservados para um inquilino específico.  Essas ferramentas permitem-lhe assegurar um bom desempenho de forma económica.

#### <a name="operations-scale-for-database-per-tenant"></a>Escala de operações para a base de dados por inquilino

A plataforma de base de dados do Azure SQL tem muitas funcionalidades de gestão concebidas para gerir um grande número de bases de dados em escala, como também mais de 100.000 bases de dados.  Esses recursos tornam o padrão de base de dados por inquilino plausível.

Por exemplo, suponha que um sistema tem uma base de dados de inquilinos de 1000 como apenas uma base de dados.  A base de dados pode ter 20 índices.  Se o sistema converte a ter bases de dados de inquilino único de 1000, aumenta a quantidade de índices a 20 000.  Na base de dados SQL como parte da [otimização automática][docu-sql-db-automatic-tuning-771a], as funcionalidades de indexação automática estão ativadas por predefinição.  Indexação automática gerencia para todos os índices de 20.000 e suas otimizações de criar e soltar em curso.  Estas ações automáticas ocorrem dentro de uma base de dados individual e não são coordenados ou restritas pelas ações semelhantes em outros bancos de dados.  Indexação automática trata índices de forma diferente numa base de dados ocupada que numa base de dados menos ocupado.  Este tipo de personalização de gestão de índice seria impraticável à escala da base de dados por inquilino se esta tarefa de gestão de grandes tinha que ser feito manualmente.

Outros recursos de gerenciamento que escala bem incluem o seguinte:

- Cópias de segurança incorporadas.
- Elevada disponibilidade.
- Encriptação de no disco.
- Telemetria de desempenho.

#### <a name="automation"></a>Automatização

As operações de gestão podem ser convertidos em script e oferecidas por meio de um [devops] [ http-visual-studio-devops-485m] modelo.  As operações ainda podem ser automatizadas e expostas no aplicativo.

Por exemplo, pode automatizar a recuperação de um único inquilino para um ponto anterior no tempo.  A recuperação só precisa de restaurar a base de dados de inquilino único um que armazena o inquilino.  Este restauro não tem qualquer impacto nos outros inquilinos, que confirma que as operações de gestão são ao nível do escalados granular de cada inquilino individual.

## <a name="e-multi-tenant-app-with-multi-tenant-databases"></a>E. Aplicação multi-inquilino com bancos de dados do multi-inquilinos

Outro padrão disponível é armazenar muitos inquilinos numa base de dados do multi-inquilino.  A instância da aplicação pode ter qualquer número de bases de dados do multi-inquilinos.  O esquema de uma base de dados do multi-inquilino tem de ter uma ou mais colunas de identificador de inquilino para que os dados a partir de qualquer determinado inquilino podem ser obtidos de forma seletiva.  Além disso, o esquema pode exigir algumas tabelas ou colunas que são utilizadas por apenas um subconjunto de inquilinos.  No entanto, os dados de referência e de código estáticos são armazenados apenas uma vez e são partilhados por todos os inquilinos.

#### <a name="tenant-isolation-is-sacrificed"></a>Isolamento de inquilino é sacrificado

*Dados:*&nbsp; Uma base de dados do multi-inquilino necessariamente sacrificará o isolamento de inquilino.  Os dados de vários inquilinos são armazenados em conjunto numa base de dados.  Durante o desenvolvimento, certifique-se de que consultas nunca expõem os dados de mais do que um inquilino.  Base de dados SQL suporta [segurança de nível de linha][docu-sql-svr-db-row-level-security-947w], que pode impor que os dados devolvidos por uma consulta ser confinada para um único inquilino.

*Processamento:*&nbsp; Uma base de dados do multi-inquilino partilha os recursos de computação e armazenamento em todos os seus inquilinos.  A base de dados como um todo pode ser monitorado para garantir que está a funcionar de forma aceitável.  No entanto, o sistema do Azure tem uma maneira interna para monitorar ou gerenciar o uso desses recursos por um inquilino individual.  Por conseguinte, a base de dados do multi-inquilino acarreta um risco maior de encontrar vizinhos ruidosos, em que a carga de trabalho de um inquilino overactive tem impacto sobre a experiência de desempenho de outros inquilinos na mesma base de dados.  Monitorização de nível de aplicativo adicional pode monitorizar o desempenho de nível de inquilino.

#### <a name="lower-cost"></a>Custo mais baixo

Em geral, bases de dados do multi-inquilinos tem o mais baixo por inquilino de custos.  Os custos de recursos para uma base de dados são mais baixos do que de um conjunto elástico dimensionado de maneira equivalente.  Além disso, para cenários em que os inquilinos precisam apenas armazenamento limitada, potencialmente milhões de inquilinos poderiam ser armazenados num único banco de dados.  Nenhum conjunto elástico pode conter milhões de bases de dados.  No entanto, uma solução que contém as bases de dados de 1000 por conjunto, com os conjuntos de 1000, foi possível alcançar a escala dos milhões sob a tornar-se complicadas de gerenciar.

Duas variações de um modelo de base de dados do multi-inquilino são discutidas no que se segue, com o modelo de multi-inquilino em partição horizontal que está a ser mais flexível e dimensionável.

## <a name="f-multi-tenant-app-with-a-single-multi-tenant-database"></a>F. Aplicação multi-inquilino com um único banco de dados do multi-inquilino

O padrão mais simples de banco de dados do multi-inquilino utiliza uma base de dados para alojar dados para todos os inquilinos.  À medida que são adicionados mais inquilinos, a base de dados for aumentado verticalmente com mais recursos de armazenamento e computação.  Este aumento vertical poderá ser tudo o que for necessário, embora sempre haja um limite de escala ultimate.  No entanto, longa antes desse limite for atingido o banco de dados torna-se complicadas de gerenciar.

Operações de gestão que estão focalizadas em inquilinos individuais são mais complexas de implementar num banco de dados do multi-inquilino.  E em escala, estas operações podem ficar muito lentas.  Um exemplo é um ponto anterior no tempo restauro dos dados para o inquilino apenas um.

## <a name="g-multi-tenant-app-with-sharded-multi-tenant-databases"></a>G. Aplicação multi-inquilino com bancos de dados de multi-inquilinos em partição horizontal

A maioria das aplicações de SaaS acessam os dados do inquilino apenas um por vez.  Este padrão de acesso permite que os dados de inquilino ser distribuída em várias bases de dados ou partições horizontais, onde todos os dados para qualquer um de inquilino está contido numa partição horizontal.  Combinado com um padrão de base de dados multi-inquilino, um modelo em partição horizontal permite um dimensionamento quase ilimitado.

![Design de aplicação multi-inquilino com bancos de dados de multi-inquilinos em partição horizontal.][image-mt-app-sharded-mt-db-174s]

#### <a name="manage-shards"></a>Gerir partições horizontais

Fragmentação adiciona complexidade, tanto para o design e a gestão operacional.  Um catálogo é necessária para manter o mapeamento entre os inquilinos e as bases de dados.  Além disso, os procedimentos de gerenciamento são necessárias para gerir as partições horizontais e a população de inquilino.  Por exemplo, os procedimentos devem ser criados para adicionar e remover partições horizontais e para mover dados do inquilino entre partições horizontais.  É uma forma de dimensionar ao adicionar uma nova partição horizontal e preenchendo-o com novos inquilinos.  Outras vezes, pode dividir uma partição horizontal densely preenchida em partições horizontais de menos densely preenchidas dois.  Depois de vários inquilinos foram movidos ou descontinuados, poderá mesclar partições horizontais dispersamente preenchidas em conjunto.  A intercalação iria resultar na utilização de recursos económico mais.  Inquilinos também podem ser movidos entre fragmentos para cargas de trabalho do saldo.

Base de dados SQL fornece uma ferramenta de dividir/unir que funciona em conjunto com a biblioteca de fragmentação e a base de dados do catálogo.  Pode dividir a aplicação fornecida e partições horizontais de intercalação e ele podem mover dados do inquilino entre partições horizontais.  A aplicação também mantém o catálogo durante essas operações, a marcação afetados inquilinos como offline antes de movê-los.  Após a mudança, a aplicação atualiza o catálogo novamente com o novo mapeamento de e para marcação ao inquilino como voltar a ficar online.

#### <a name="smaller-databases-more-easily-managed"></a>Menores bases de dados mais facilmente geridas

Ao distribuir inquilinos em várias bases de dados, a solução de multi-inquilino em partição horizontal resulta em menores bases de dados que são geridas mais facilmente.  Por exemplo, a restaurar um inquilino específico para um ponto anterior no tempo agora envolve restaurar uma base de dados mais pequeno de uma cópia de segurança, em vez de uma base de dados maior que contém todos os inquilinos. O tamanho da base de dados e o número de inquilinos por base de dados, podem ser escolhidos para balancear a carga de trabalho e os esforços de gestão.

#### <a name="tenant-identifier-in-the-schema"></a>Identificador do inquilino no esquema

Dependendo da abordagem de fragmentação usada, as restrições adicionais podem ser impostas no esquema da base de dados.  O aplicativo de dividir/unir da base de dados SQL requer que o esquema inclui a chave de fragmentação, o que normalmente é o identificador do inquilino.  O identificador do inquilino é o elemento à esquerda na chave primária de todas as tabelas em partição horizontal.  O identificador do inquilino permite à aplicação de dividir/unir rapidamente localizar e mover os dados associados a um inquilino específico.

#### <a name="elastic-pool-for-shards"></a>Conjunto elástico para as partições horizontais

Bases de dados de multi-inquilinos em partição horizontal podem ser colocados em conjuntos elásticos.  Em geral, ter muitas bases de dados de inquilino único num conjunto é como custo eficiente como tendo muitos inquilinos em algumas bases de dados do multi-inquilinos.  Bases de dados do multi-inquilinos são vantajosos quando há um grande número de inquilinos relativamente inativos.

## <a name="h-hybrid-sharded-multi-tenant-database-model"></a>H. Modelo de base de dados em partição horizontal multi-inquilino híbrida

No modelo híbrido, todos os bancos de dados tem o identificador do inquilino no seu esquema.  As bases de dados são todos capazes de armazenar mais de um inquilino e as bases de dados podem ser em partição horizontal.  Portanto, no sentido de esquema, são todas as bases de dados do multi-inquilinos.  Ainda na prática, algumas destas bases de dados contém apenas a um inquilino.  Seja como for, a quantidade de inquilinos armazenados num determinado banco de dados não tem efeito sobre o esquema de banco de dados.

#### <a name="move-tenants-around"></a>Mover os inquilinos

Em qualquer altura, pode mover um inquilino específico para a sua própria base de dados do multi-inquilino.  E, em qualquer altura, pode mudar de ideias e mover o inquilino de volta para um banco de dados que contém vários inquilinos.  Também pode atribuir um inquilino para a nova base de dados de inquilino único, quando Aprovisiona a nova base de dados.

O modelo híbrido destaca-se quando existem grandes diferenças entre as necessidades de recursos de grupos de identificação de inquilinos.  Por exemplo, suponha que os inquilinos participando de uma avaliação gratuita não são garantidos o mesmo nível elevado de desempenho que são inquilinos de assinatura.  A política pode ser para inquilinos na fase de avaliação gratuita de ser armazenados numa base de dados do multi-inquilino que é partilhado entre todos os inquilinos de avaliação gratuitos.  Quando subscreve um inquilino de avaliação gratuito para o escalão de serviço básico, o inquilino pode ser movido para outra base de dados do multi-inquilino que pode ter menos de inquilinos.  Um subscritor que lhe paga para o escalão de serviço premium pode ser movido para o seu próprio nova base de dados de inquilino único.

#### <a name="pools"></a>Conjuntos

Nesse modelo híbrido, as bases de dados de inquilino único para os inquilinos de subscritor podem ser colocados em agrupamentos de recursos para reduzir os custos da base de dados por inquilino.  Também é feita no modelo de base de dados por inquilino.

## <a name="i-tenancy-models-compared"></a>I. Modelos de inquilinos, em comparação comparados

A tabela seguinte resume as diferenças entre os modelos de inquilinos principal.

| Medida | Aplicação autónoma | Base de dados por inquilino | Em partição horizontal multi-inquilino |
| :---------- | :------------- | :------------------ | :------------------- |
| Escala | Médio<br />1-100s | Muito alta<br />1-100,000s | Ilimitado<br />1-1,000,000s |
| Isolamento de inquilino | Muito alta | Elevado | Baixa; exceto para qualquer inquilino individual (ou seja, num db de MT). |
| Custo de base de dados por inquilino | Alta; é dimensionado para picos. | Baixa; conjuntos utilizados. | Mais baixo, para inquilinos pequenos no bds de MT. |
| Gestão e monitorização de desempenho | Por inquilino apenas | Agregado + por inquilino | Agregar; Embora é por inquilino apenas para singles. |
| Complexidade de desenvolvimento | Baixa | Baixa | Médio; devido à fragmentação. |
| Complexidade operacional | Baixa-alta. Individualmente simples, complexas em escala. | Baixa-médio. Complexidade de endereço de padrões em escala. | Baixa-alta. Gestão de inquilino individual é complexo. |
| &nbsp; ||||

## <a name="next-steps"></a>Passos Seguintes

- [Implementar e explorar uma aplicação Wingtip multi-inquilino que utiliza o modelo de SaaS da base de dados por inquilino - base de dados do Azure SQL][docu-sql-db-saas-tutorial-deploy-wingtip-db-per-tenant-496y]

- [Bem-vindo ao Wingtip Tickets de inquilinos de SQL Database do Azure SaaS de exemplo de aplicação][docu-saas-tenancy-welcome-wingtip-tickets-app-384w]


<!--  Article link references.  -->

[http-visual-studio-devops-485m]: https://www.visualstudio.com/devops/

[docu-sql-svr-db-row-level-security-947w]: https://docs.microsoft.com/sql/relational-databases/security/row-level-security

[docu-elastic-db-client-library-536r]: sql-database-elastic-database-client-library.md
[docu-sql-db-saas-tutorial-deploy-wingtip-db-per-tenant-496y]: sql-database-saas-tutorial.md
[docu-sql-db-automatic-tuning-771a]: sql-database-automatic-tuning.md
[docu-saas-tenancy-welcome-wingtip-tickets-app-384w]: saas-tenancy-welcome-wingtip-tickets-app.md


<!--  Image references.  -->

[image-standalone-app-st-db-111a]: media/saas-tenancy-app-design-patterns/saas-standalone-app-single-tenant-database-11.png "Design de uma aplicação autónoma com exatamente um banco de dados de inquilino único."

[image-mt-app-db-per-tenant-132d]: media/saas-tenancy-app-design-patterns/saas-multi-tenant-app-database-per-tenant-13.png "Design da aplicação multi-inquilino com a base de dados por inquilino."

[image-mt-app-db-per-tenant-pool-153p]: media/saas-tenancy-app-design-patterns/saas-multi-tenant-app-database-per-tenant-pool-15.png "Design da aplicação multi-inquilino com a base de dados por inquilino, utilizando o conjunto elástico."

[image-mt-app-sharded-mt-db-174s]: media/saas-tenancy-app-design-patterns/saas-multi-tenant-app-sharded-multi-tenant-databases-17.png "Design de aplicação multi-inquilino com bancos de dados de multi-inquilinos em partição horizontal."

