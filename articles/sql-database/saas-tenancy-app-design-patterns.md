---
title: Padrões de SaaS de vários locatários-banco de dados SQL do Azure | Microsoft Docs
description: Saiba mais sobre os requisitos e padrões comuns de arquitetura de dados de aplicativos de banco de dado SaaS (software como serviço) multilocatários executados no ambiente de nuvem do Azure.
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: seoapril2019
ms.devlang: ''
ms.topic: conceptual
author: MightyPen
ms.author: genemi
ms.reviewer: billgib, sstein
ms.date: 01/25/2019
ms.openlocfilehash: 8cbf0e45ac368f0d2dd1678984bd14392452e63a
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68570191"
---
# <a name="multi-tenant-saas-database-tenancy-patterns"></a>Padrões de locação de banco de dados SaaS multilocatário

Este artigo descreve os vários modelos de locação disponíveis para um aplicativo SaaS multilocatário.

Ao criar um aplicativo SaaS multilocatário, você deve escolher cuidadosamente o modelo de aluguel que melhor atenda às necessidades do seu aplicativo.  Um modelo de locação determina como os dados de cada locatário são mapeados para o armazenamento.  Sua escolha de modelo de locação afeta o design e o gerenciamento de aplicativos.  Às vezes, alternar para um modelo diferente é dispendioso.

## <a name="a-saas-concepts-and-terminology"></a>R. Conceitos e terminologia de SaaS

No modelo SaaS (software como serviço), sua empresa não vende *licenças* para seu software. Em vez disso, cada cliente faz o aluguel de pagamentos para sua empresa, tornando cada cliente um *locatário* de sua empresa.

Em retorno para o aluguel de pagamento, cada locatário recebe acesso aos componentes do aplicativo SaaS e tem seus dados armazenados no sistema SaaS.

O termo *modelo de aluguel* refere-se à forma como os dados armazenados dos locatários são organizados:

- *Locação única:* &nbsp; Cada Database armazena dados de apenas um locatário.
- *Multilocação:* &nbsp; Cada Database armazena dados de vários locatários separados (com mecanismos para proteger a privacidade de dados).
- Os modelos de aluguel híbrido também estão disponíveis.

## <a name="b-how-to-choose-the-appropriate-tenancy-model"></a>B. Como escolher o modelo de aluguel apropriado

Em geral, o modelo de locação não afeta a função de um aplicativo, mas provavelmente afeta outros aspectos da solução geral.  Os critérios a seguir são usados para avaliar cada um dos modelos:

- **Escalabilidade**
    - Número de locatários.
    - Armazenamento por locatário.
    - Armazenamento em agregação.
    - Pico.

- **Isolamento de locatário:** &nbsp; Isolamento de dados e desempenho (se a carga de trabalho de um locatário impacta outras pessoas).

- **Custo por locatário:** &nbsp; Custos do banco de dados.

- **Complexidade de desenvolvimento:**
    - Alterações no esquema.
    - Alterações em consultas (exigidas pelo padrão).

- **Complexidade operacional:**
    - Monitoramento e gerenciamento de desempenho.
    - Gerenciamento de esquema.
    - Restaurando um locatário.
    - Recuperação após desastre.

- **Personalização**&nbsp; Facilidade de suporte a personalizações de esquema que são específicas de locatário ou específicos de classe de locatário.

A discussão sobre locação se concentra na camada de *dados* .  Mas considere por um momento a camada de *aplicativo* .  A camada de aplicativo é tratada como uma entidade monolítica.  Se você dividir o aplicativo em muitos componentes pequenos, sua escolha de modelo de locação poderá ser alterada.  Você pode tratar alguns componentes de forma diferente dos outros em relação à locação e à tecnologia de armazenamento ou à plataforma usada.

## <a name="c-standalone-single-tenant-app-with-single-tenant-database"></a>C. Aplicativo de locatário único autônomo com Banco de dados de locatário único

#### <a name="application-level-isolation"></a>Isolamento de nível de aplicativo

Nesse modelo, todo o aplicativo é instalado repetidamente, uma vez para cada locatário.  Cada instância do aplicativo é uma instância autônoma, portanto, ela nunca interage com nenhuma outra instância autônoma.  Cada instância do aplicativo tem apenas um locatário e, portanto, precisa de apenas um banco de dados.  O locatário tem o próprio banco de dados para si mesmo.

![Design de aplicativo autônomo com exatamente um banco de dados de locatário único.][image-standalone-app-st-db-111a]

Cada instância de aplicativo é instalada em um grupo de recursos do Azure separado.  O grupo de recursos pode pertencer a uma assinatura que pertence ao fornecedor do software ou ao locatário.  Em ambos os casos, o fornecedor pode gerenciar o software para o locatário.  Cada instância do aplicativo é configurada para se conectar ao banco de dados correspondente.

Cada banco de dados de locatário é implantado como um banco de dados individual.  Esse modelo fornece o maior isolamento de banco de dados.  Mas o isolamento requer que recursos suficientes sejam alocados para cada banco de dados para lidar com suas cargas de pico.  Aqui, é importante que os pools elásticos não possam ser usados para bancos de dados implantados em grupos de recursos diferentes ou para assinaturas diferentes.  Essa limitação torna esse aplicativo de único locatário autônomo a solução mais cara de uma perspectiva de custo geral do banco de dados.

#### <a name="vendor-management"></a>Gerenciamento de fornecedores

O fornecedor pode acessar todos os bancos de dados em todas as instâncias do aplicativo autônomo, mesmo que as instâncias do aplicativo estejam instaladas em assinaturas de locatário diferentes.  O acesso é obtido por meio de conexões SQL.  Esse acesso entre instâncias pode permitir que o fornecedor Centralize o gerenciamento de esquema e a consulta entre bancos de dados para fins de relatório ou análise.  Se esse tipo de gerenciamento centralizado for desejado, será necessário implantar um catálogo que mapeia os identificadores de locatário para URIs de banco de dados.  O banco de dados SQL do Azure fornece uma biblioteca de fragmentação que é usada junto com um banco de dados SQL para fornecer um catálogo.  A biblioteca de fragmentação é formalmente nomeada a [biblioteca de cliente do banco de dados elástico][docu-elastic-db-client-library-536r].

## <a name="d-multi-tenant-app-with-database-per-tenant"></a>D. Aplicativo multilocatário com Banco de dados por locatário

Este próximo padrão usa um aplicativo multilocatário com muitos bancos de dados, todos sendo bancos de dados de único locatário.  Um novo banco de dados é provisionado para cada novo locatário.  A camada de aplicativo é dimensionada *verticalmente* com a adição de mais recursos por nó.  Ou o aplicativo é *dimensionado horizontalmente* com a adição de mais nós.  O dimensionamento é baseado na carga de trabalho e é independente do número ou da escala dos bancos de dados individuais.

![Design de aplicativo multilocatário com Banco de dados por locatário.][image-mt-app-db-per-tenant-132d]

#### <a name="customize-for-a-tenant"></a>Personalizar para um locatário

Como o padrão de aplicativo autônomo, o uso de bancos de dados de locatário único fornece isolamento de locatário forte.  Em qualquer aplicativo cujo modelo especifica apenas bancos de dados de locatário único, o esquema para qualquer um dos bancos de dado pode ser personalizado e otimizado para seu locatário.  Essa personalização não afeta outros locatários no aplicativo. Talvez um locatário possa precisar de dados além dos campos de dados básicos que todos os locatários precisam.  Além disso, o campo de dados extra pode precisar de um índice.

Com o banco de dados por locatário, a personalização do esquema para um ou mais locatários individuais é simples de atingir.  O fornecedor do aplicativo deve criar procedimentos para gerenciar cuidadosamente as personalizações de esquema em escala.

#### <a name="elastic-pools"></a>Conjuntos elásticos

Quando os bancos de dados são implantados no mesmo grupo de recursos, eles podem ser agrupados em pools elásticos.  Os pools fornecem uma maneira econômica de compartilhar recursos em vários bancos de dados.  Essa opção de pool é mais barata do que exigir que cada banco de dados seja grande o suficiente para acomodar os picos de uso que ele enfrenta.  Embora os bancos de dados em pool compartilhem o acesso a recursos, eles ainda podem alcançar um alto grau de isolamento de desempenho.

![Design de aplicativo multilocatário com Banco de dados por locatário usando pool elástico.][image-mt-app-db-per-tenant-pool-153p]

O banco de dados SQL do Azure fornece as ferramentas necessárias para configurar, monitorar e gerenciar o compartilhamento.  As métricas de desempenho no nível do pool e no nível de banco de dados estão disponíveis no portal do Azure e por meio de logs de Azure Monitor.  As métricas podem fornecer ótimos percepções sobre o desempenho específico da agregação e do locatário.  Bancos de dados individuais podem ser movidos entre pools para fornecer recursos reservados para um locatário específico.  Essas ferramentas permitem garantir um bom desempenho de maneira econômica.

#### <a name="operations-scale-for-database-per-tenant"></a>Escala de operações para banco de dados por locatário

A plataforma de banco de dados SQL do Azure tem muitos recursos de gerenciamento projetados para gerenciar um grande número de bancos de dados em escala, como bem mais de 100.000 bancos.  Esses recursos tornam o padrão de banco de dados por locatário plausível.

Por exemplo, suponha que um sistema tenha um banco de dados de locatário 1000 como apenas um banco de dados.  O banco de dados pode ter 20 índices.  Se o sistema converter para ter 1000 bancos de dados de único locatário, a quantidade de índices aumentará para 20.000.  No banco de dados SQL como parte do [ajuste automático][docu-sql-db-automatic-tuning-771a], os recursos de indexação automática são habilitados por padrão.  A indexação automática gerencia todos os índices 20.000 e suas otimizações de criação e remoção em andamento.  Essas ações automatizadas ocorrem dentro de um banco de dados individual e não são coordenadas ou restritas por ações semelhantes em outros bancos.  A indexação automática trata os índices de forma diferente em um banco de dados ocupado do que em um banco de dados menos ocupado.  Esse tipo de personalização de gerenciamento de índice seria impraticável na escala de banco de dados por locatário se essa tarefa de gerenciamento enorme tivesse de ser feita manualmente.

Outros recursos de gerenciamento que dimensionam bem incluem o seguinte:

- Backups internos.
- Elevada disponibilidade.
- Criptografia em disco.
- Telemetria de desempenho.

#### <a name="automation"></a>Automatização

As operações de gerenciamento podem ser inseridas no script e oferecidas por meio de um modelo [DevOps][http-visual-studio-devops-485m] .  As operações podem até mesmo ser automatizadas e expostas no aplicativo.

Por exemplo, você pode automatizar a recuperação de um único locatário para um ponto anterior no tempo.  A recuperação só precisa restaurar um banco de dados de locatário único que armazena o locatário.  Essa restauração não afeta outros locatários, o que confirma que as operações de gerenciamento estão no nível granular de cada locatário individual.

## <a name="e-multi-tenant-app-with-multi-tenant-databases"></a>E. Aplicativo multilocatário com bancos de dados de vários locatários

Outro padrão disponível é armazenar vários locatários em um banco de dados de vários locatários.  A instância do aplicativo pode ter qualquer número de bancos de dados de vários locatários.  O esquema de um banco de dados multilocatário deve ter uma ou mais colunas de identificador de locatário para que os dados de qualquer locatário específico possam ser recuperados seletivamente.  Além disso, o esquema pode exigir algumas tabelas ou colunas que são usadas apenas por um subconjunto de locatários.  No entanto, o código estático e os dados de referência são armazenados apenas uma vez e compartilhados por todos os locatários.

#### <a name="tenant-isolation-is-sacrificed"></a>Isolamento de locatário é sacrificado

*Dado*&nbsp; Um banco de dados multilocatário necessariamente sacrifica o isolamento de locatário.  Os dados de vários locatários são armazenados juntos em um banco de dado.  Durante o desenvolvimento, certifique-se de que as consultas nunca exponham dados de mais de um locatário.  O banco de dados SQL dá suporte à [segurança em nível de linha][docu-sql-svr-db-row-level-security-947w], o que pode impor que os dados retornados de uma consulta sejam delimitados para um único locatário.

*Processamento*&nbsp; Um banco de dados multilocatário compartilha recursos de computação e armazenamento em todos os seus locatários.  O banco de dados como um todo pode ser monitorado para garantir que ele esteja sendo executado de forma aceitável.  No entanto, o sistema do Azure não tem uma maneira interna de monitorar ou gerenciar o uso desses recursos por um locatário individual.  Portanto, o banco de dados multilocatário tem um risco maior de encontrar vizinhos ruidosas, em que a carga de trabalho de um locatário superativo afeta a experiência de desempenho de outros locatários no mesmo banco de dados.  O monitoramento em nível de aplicativo adicional pode monitorar o desempenho no nível do locatário.

#### <a name="lower-cost"></a>Custo mais reduzido

Em geral, bancos de dados multilocatários têm o menor custo por locatário.  Os custos de recursos para um único banco de dados são menores do que para um pool elástico de tamanho equivalente.  Além disso, para cenários em que os locatários precisam apenas de armazenamento limitado, potencialmente, milhões de locatários podem ser armazenados em um único banco de dados.  Nenhum pool elástico pode conter milhões de bancos de dados.  No entanto, uma solução que contém 1000 bancos de dados por pool, com pools de 1000, pode alcançar a escala de milhões no risco de se tornar difícil de gerenciar.

Duas variações de um modelo de banco de dados multilocatário são discutidas da seguinte maneira, com o modelo multilocatário fragmentado sendo o mais flexível e escalonável.

## <a name="f-multi-tenant-app-with-a-single-multi-tenant-database"></a>F. Aplicativo multilocatário com um único banco de dados de vários locatários

O padrão mais simples de banco de dados multilocatário usa um banco de dados individual para hospedar o dado para todos os locatários.  À medida que mais locatários são adicionados, o banco de dados é dimensionado com mais recursos de armazenamento e computação.  Essa escala vertical pode ser tudo o que é necessário, embora sempre haja um limite de escala final.  No entanto, muito antes que esse limite seja atingido, o banco de dados torna-se difícil de gerenciar.

As operações de gerenciamento que se concentram em locatários individuais são mais complexas de implementar em um banco de dados de vários locatários.  E em escala, essas operações podem se tornar lentas inaceitáveis.  Um exemplo é uma restauração pontual dos dados para apenas um locatário.

## <a name="g-multi-tenant-app-with-sharded-multi-tenant-databases"></a>G. Aplicativo multilocatário com bancos de dados de vários locatários fragmentados

A maioria dos aplicativos SaaS acessa os dados de apenas um locatário por vez.  Esse padrão de acesso permite que os dados de locatário sejam distribuídos em vários bancos de dados ou fragmentos, em que todos os todos os dado de um locatário estão contidos em um fragmento.  Combinado com um padrão de banco de dados multilocatário, um modelo fragmentado permite uma escala quase ilimitada.

![Design de aplicativo multilocatário com bancos de dados multilocatários fragmentados.][image-mt-app-sharded-mt-db-174s]

#### <a name="manage-shards"></a>Gerenciar fragmentos

A fragmentação adiciona complexidade ao design e ao gerenciamento operacional.  Um catálogo é necessário para manter o mapeamento entre locatários e bancos de dados.  Além disso, os procedimentos de gerenciamento são necessários para gerenciar os fragmentos e a população do locatário.  Por exemplo, os procedimentos devem ser projetados para adicionar e remover fragmentos e para mover dados de locatário entre fragmentos.  Uma maneira de dimensionar é adicionar um novo fragmento e preenchê-lo com novos locatários.  Em outras ocasiões, você pode dividir um fragmento muito populado em dois fragmentos com menos densidade.  Depois que vários locatários tiverem sido movidos ou descontinuados, você poderá mesclar os fragmentos com uma população escassada em conjunto.  A mesclagem resultaria em uma utilização de recursos mais econômica.  Os locatários também podem ser movidos entre fragmentos para balancear cargas de trabalho.

O banco de dados SQL fornece uma ferramenta de divisão/mesclagem que funciona em conjunto com a biblioteca de fragmentação e o banco de dados de catálogo.  O aplicativo fornecido pode dividir e mesclar fragmentos e pode mover dados de locatário entre fragmentos.  O aplicativo também mantém o catálogo durante essas operações, marcando locatários afetados como offline antes de movê-los.  Após a movimentação, o aplicativo atualiza o catálogo novamente com o novo mapeamento e marca o locatário como online novamente.

#### <a name="smaller-databases-more-easily-managed"></a>Bancos de dados menores mais fáceis de gerenciar

Ao distribuir locatários em vários bancos de dados, a solução de multilocatário fragmentada resulta em bancos de dados menores que são gerenciados com mais facilidade.  Por exemplo, a restauração de um locatário específico para um ponto anterior no tempo agora envolve a restauração de um único banco de dados menor a partir de um backup, em vez de um banco de dados maior que contém todos os locatários. O tamanho do banco de dados e o número de locatários por banco de dados podem ser escolhidos para balancear a carga de trabalho e os esforços de gerenciamento.

#### <a name="tenant-identifier-in-the-schema"></a>Identificador do locatário no esquema

Dependendo da abordagem de fragmentação usada, restrições adicionais podem ser impostas no esquema de banco de dados.  O aplicativo de divisão/mesclagem do banco de dados SQL requer que o esquema inclua a chave de fragmentação, que normalmente é o identificador do locatário.  O identificador do locatário é o elemento principal na chave primária de todas as tabelas fragmentadas.  O identificador de locatário permite que o aplicativo de divisão/mesclagem localize rapidamente e mova dados associados a um locatário específico.

#### <a name="elastic-pool-for-shards"></a>Pool elástico para fragmentos

Bancos de dados de vários locatários fragmentados podem ser colocados em pools elásticos.  Em geral, ter muitos bancos de dados de locatário único em um pool é tão econômico quanto ter muitos locatários em alguns bancos de dados de vários locatários.  Bancos de dados multilocatários são vantajosos quando há um grande número de locatários relativamente inativos.

## <a name="h-hybrid-sharded-multi-tenant-database-model"></a>H. Modelo de banco de dados multilocatário fragmentado híbrido

No modelo híbrido, todos os bancos de dados têm o identificador de locatário em seu esquema.  Os bancos de dados são todos capazes de armazenar mais de um locatário, e os bancos de dados podem ser fragmentados.  Portanto, no sentido do esquema, eles são todos os bancos de dados multilocatários.  Ainda na prática, alguns desses bancos de dados contêm apenas um locatário.  Independentemente, a quantidade de locatários armazenados em um determinado banco de dados não tem efeito sobre o esquema de banco de dados.

#### <a name="move-tenants-around"></a>Mover locatários

A qualquer momento, você pode mover um locatário específico para seu próprio banco de dados de vários locatários.  E a qualquer momento, você pode mudar de ideia e mover o locatário de volta para um banco de dados que contém vários locatários.  Você também pode atribuir um locatário ao novo banco de dados de locatário único ao provisionar o novo banco de dados.

O modelo híbrido se destaca quando há grandes diferenças entre as necessidades de recursos de grupos de locatários identificáveis.  Por exemplo, suponha que os locatários que participam de uma avaliação gratuita não tenham garantia do mesmo alto nível de desempenho que os locatários de assinatura.  A política pode ser para os locatários na fase de avaliação gratuita serem armazenados em um banco de dados multilocatário que é compartilhado entre todos os locatários de avaliação gratuita.  Quando um locatário de avaliação gratuita assina a camada de serviço básica, o locatário pode ser movido para outro banco de dados de vários locatários que pode ter menos locatários.  Um assinante que paga para a camada de serviço Premium pode ser movido para seu próprio novo banco de dados de locatário único.

#### <a name="pools"></a>Conjuntos

Nesse modelo híbrido, os bancos de dados de locatário único para locatários de assinante podem ser colocados em pools de recursos para reduzir os custos de banco de dados por locatário.  Isso também é feito no modelo de banco de dados por locatário.

## <a name="i-tenancy-models-compared"></a>I. Modelos de locação comparados

A tabela a seguir resume as diferenças entre os principais modelos de aluguel.

| Medida | Aplicação autónoma | Banco de dados por locatário | Multilocatário fragmentado |
| :---------- | :------------- | :------------------ | :------------------- |
| Escala | Médio<br />1-100s | Muito alta<br />1-100, mil | Ilimitado<br />1-1, 000, mil |
| Isolamento de locatário | Muito alta | Alta | Pequena exceto para qualquer locatário único (que é sozinho em um MT DB). |
| Custo do banco de dados por locatário | Elevada é dimensionado para picos. | Pequena pools usados. | Mais baixo, para locatários pequenos em bancos de los MT. |
| Monitoramento e gerenciamento de desempenho | Somente por locatário | Agregar + por locatário | Tais Embora seja por locatário somente para singles. |
| Complexidade de desenvolvimento | Baixa | Baixa | Médio devido à fragmentação. |
| Complexidade operacional | Baixo-alto. Individualmente simples, complexa em escala. | Baixa-média. Os padrões abordam a complexidade em escala. | Baixo-alto. O gerenciamento individual de locatários é complexo. |
| &nbsp; ||||

## <a name="next-steps"></a>Passos seguintes

- [Implantar e explorar um aplicativo Wingtip multilocatário que usa o modelo de SaaS de banco de dados por locatário – banco de dados SQL do Azure][docu-sql-db-saas-tutorial-deploy-wingtip-db-per-tenant-496y]

- [Bem-vindo ao aplicativo de aluguel de banco de dados SQL do SaaS de exemplo do Wingtip tickets][docu-saas-tenancy-welcome-wingtip-tickets-app-384w]


<!--  Article link references.  -->

[http-visual-studio-devops-485m]: https://www.visualstudio.com/devops/

[docu-sql-svr-db-row-level-security-947w]: https://docs.microsoft.com/sql/relational-databases/security/row-level-security

[docu-elastic-db-client-library-536r]: sql-database-elastic-database-client-library.md
[docu-sql-db-saas-tutorial-deploy-wingtip-db-per-tenant-496y]: sql-database-saas-tutorial.md
[docu-sql-db-automatic-tuning-771a]: sql-database-automatic-tuning.md
[docu-saas-tenancy-welcome-wingtip-tickets-app-384w]: saas-tenancy-welcome-wingtip-tickets-app.md


<!--  Image references.  -->

[image-standalone-app-st-db-111a]: media/saas-tenancy-app-design-patterns/saas-standalone-app-single-tenant-database-11.png "Design de aplicativo autônomo com exatamente um banco de dados de locatário único."

[image-mt-app-db-per-tenant-132d]: media/saas-tenancy-app-design-patterns/saas-multi-tenant-app-database-per-tenant-13.png "Design de aplicativo multilocatário com Banco de dados por locatário."

[image-mt-app-db-per-tenant-pool-153p]: media/saas-tenancy-app-design-patterns/saas-multi-tenant-app-database-per-tenant-pool-15.png "Design de aplicativo multilocatário com Banco de dados por locatário usando pool elástico."

[image-mt-app-sharded-mt-db-174s]: media/saas-tenancy-app-design-patterns/saas-multi-tenant-app-sharded-multi-tenant-databases-17.png "Design de aplicativo multilocatário com bancos de dados multilocatários fragmentados."

