---
title: Ética e uso responsável - Personalizar
titleSuffix: Azure Cognitive Services
description: Estas diretrizes destinam-se a ajudá-lo a implementar a personalização de uma forma que o ajude a criar confiança na sua empresa e serviço. Não se esqueça de fazer uma pausa na pesquisa, aprender e deliberar sobre o impacto da personalização na vida das pessoas. Em caso de dúvida, procure orientação.
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 06/12/2019
ms.openlocfilehash: 602da28f0c235fb0e797a493bc1160631c042a9d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87132726"
---
# <a name="guidelines-for-responsible-implementation-of-personalizer"></a>Orientações para a implementação responsável do Personaler

Para que as pessoas e a sociedade percebam todo o potencial da IA, as implementações precisam de ser desenhadas de forma a que ganhem a confiança de quem adiciona AI às suas aplicações e aos utilizadores de aplicações construídas com IA. Estas diretrizes destinam-se a ajudá-lo a implementar o Personalizer de uma forma que o ajude a criar confiança na sua empresa e serviço. Não se esqueça de fazer uma pausa na pesquisa, aprender e deliberar sobre o impacto da personalização na vida das pessoas. Em caso de dúvida, procure orientação.

Estas diretrizes não se destinam a aconselhamento jurídico e deve garantir separadamente que a sua aplicação está em conformidade com os rápidos desenvolvimentos da lei nesta área e no seu setor.

Além disso, ao conceber a sua aplicação usando o Personalizer, deve considerar um vasto conjunto de responsabilidades que tem ao desenvolver qualquer sistema de IA centrado em dados, incluindo ética, privacidade, segurança, segurança, inclusão, transparência e responsabilidade. Pode ler mais sobre estes na secção [de leitura recomendada.](#recommended-reading)

Pode utilizar o seguinte conteúdo como lista de verificação inicial e personalizá-lo e aperfeiçoá-lo ao seu cenário. Este documento tem duas secções principais: A primeira dedica-se a destacar considerações de uso responsável na escolha de cenários, funcionalidades e recompensas para Personaler. A segunda toma um conjunto de valores a Microsoft acredita que deve ser considerado na construção de sistemas de IA, e fornece sugestões e riscos accigíveis sobre como o seu uso de Personalizer os influencia.


## <a name="your-responsibility"></a>Sua responsabilidade

Todas as diretrizes para uma implementação responsável baseiam-se na base de que os desenvolvedores e empresas que usam o Personalizer são responsáveis e responsáveis pelos efeitos da utilização destes algoritmos na sociedade. Se está a desenvolver uma aplicação que a sua organização irá implementar, deve reconhecer o seu papel e responsabilidade pelo seu funcionamento e como afeta as pessoas. Se estiver a desenhar uma aplicação a ser implementada por terceiros, chegue a um entendimento partilhado com eles de quem é, em última análise, responsável pelo comportamento da aplicação, e documente esse entendimento.

A confiança baseia-se na noção de compromissos cumpridos - considere os seus utilizadores, a sociedade e o enquadramento legal em que as suas aplicações funcionam, para identificar compromissos explícitos e implícitos que possam ter.

A Microsoft está continuamente a en esforçar-se nas suas ferramentas e documentos para o ajudar a agir sobre estas responsabilidades. [Forneça feedback à Microsoft](mailto:cogsvcs-RL-feedback@microsoft.com?subject%3DPersonalizer%20Responsible%20Use%20Feedback&body%3D%5BPlease%20share%20any%20question%2C%20idea%20or%20concern%5D) se acreditar que ferramentas adicionais, funcionalidades e documentos do produto o ajudariam a implementar estas diretrizes para a utilização do Personalizer.


## <a name="factors-for-responsibly-implementing-personalizer"></a>Fatores para implementar de forma responsável o Personalização

Implementar o Personalizer pode ser de grande valor para os seus utilizadores e para o seu negócio. Para implementar o Personalizar de forma responsável, comece por considerar as seguintes orientações quando:

* Escolher usar casos para aplicar personalização.
* Funções [de recompensa de construção.](concept-rewards.md)
* Escolher quais [as funcionalidades](concepts-features.md) sobre o contexto e possíveis ações que utilizará para personalização.


## <a name="choosing-use-cases-for-personalizer"></a>Escolher casos de utilização para Personalizar

É útil utilizar um serviço que aprenda a personalizar conteúdos e interfaces de utilizador. Também pode ser mal apliis se a forma como a personalização cria efeitos colaterais negativos no mundo real, incluindo se os utilizadores não tão adcênteses da personalização de conteúdos.

Exemplos de utilizações de Personalizer com potencial aumentado para efeitos colaterais negativos ou falta de transparência incluem cenários em que a "recompensa" depende de muitos fatores complexos a longo prazo que, quando simplificados em excesso numa recompensa imediata podem ter resultados desfavoráveis para os indivíduos. Estas tendem a ser consideradas escolhas "conseqüentes", ou escolhas que envolvem um risco de dano. Por exemplo:


* **Finanças:** Personalizar ofertas de produtos de empréstimo, financeiros e de seguros, onde os fatores de risco são baseados em dados que os indivíduos desconhecem, não podem obter ou não podem contestar.
* **Educação**: Personalização de classificações para cursos escolares e instituições de ensino onde as recomendações podem propagar enviesamentos e reduzir a consciência dos utentes sobre outras opções.
* **Democracia e Participação Cívica**: Personalizar conteúdos para utilizadores com o objetivo de influenciar opiniões é consequente e manipulador.
* **Avaliação da recompensa de terceiros**: Personalizar itens em que a recompensa se baseia numa avaliação do utilizador por parte do terceiro, em vez de ter uma recompensa gerada pelo próprio comportamento do utilizador.
* **Intolerância à Exploração**: Qualquer situação em que o comportamento de exploração do Personaler possa causar danos.

Ao escolher casos de utilização para Personalizar:

* Inicie o processo de design tendo em conta a forma como a personalização ajuda os seus utilizadores.
* Considere as consequências negativas no mundo real se alguns itens não estiverem classificados para os utilizadores devido a padrões de personalização ou exploração.
* Considere se o seu caso de utilização constitui um tratamento automatizado que afeta significativamente os indivíduos de dados regulados nos termos do artigo 22.º [do RGPD](https://eur-lex.europa.eu/legal-content/EN/TXT/?uri=CELEX:32016R0679) ou de outras leis.
* Considere laços de profecia autorrealizáveis. Isto pode acontecer se uma recompensa de personalização treina um modelo para que possa posteriormente excluir um grupo demográfico de aceder a conteúdos relevantes. Por exemplo, a maioria das pessoas num bairro de baixos rendimentos não obtém uma oferta de seguro premium, e lentamente ninguém na vizinhança tende a ver a oferta se não houver exploração suficiente.
* Guarde cópias de modelos e políticas de aprendizagem no caso de ser necessário reproduzir personalizer no futuro. Pode fazê-lo periodicamente ou em cada período de atualização de modelos.
* Considere o nível de exploração adequado para o espaço e como usá-lo como uma ferramenta para mitigar os efeitos da "câmara de eco".


## <a name="selecting-features-for-personalizer"></a>Selecionando funcionalidades para Personalizar

Personalizar o conteúdo depende de ter informações úteis sobre o conteúdo e o utilizador. Tenha em mente que, para algumas aplicações e indústrias, algumas funcionalidades do utilizador podem ser, direta ou indiretamente, consideradas discriminatórias e potencialmente ilegais.

Considere o efeito destas características:

* Demografia do utilizador : **Características relativas**ao sexo, sexo, idade, raça, religião: Estas características podem não ser permitidas em determinadas aplicações por razões regulamentares, e pode não ser ético personalizar à sua volta porque a personalização propagaria generalizações e enviesamentos. Um exemplo desta propagação de enviesamento é um trabalho de trabalho para a engenharia não sendo mostrado a públicos idosos ou baseados no sexo.
* **Informações locais**: Em muitos lugares do mundo, a informação de localização (como um código postal, código postal ou nome de bairro) pode ser altamente correlacionada com rendimento, raça e religião.
* **Perceção do utilizador da equidade**: Mesmo nos casos em que a sua aplicação está a tomar decisões sãs, considere o efeito dos utilizadores perceberem que o conteúdo exibido na sua aplicação muda de forma a parecer estar correlacionado com funcionalidades que seriam discriminatórias.
* **Preconceito não intencional em características**: Existem tipos de preconceitos que podem ser introduzidos usando características que apenas afetam um subconjunto da população. Isto requer uma atenção extra se as funcionalidades estiverem a ser geradas de forma algorítmica, como por exemplo, quando se utilizam a análise de imagem para extrair itens numa imagem ou análise de texto para descobrir entidades em texto. Tenha em conta as características dos serviços que utiliza para criar estas funcionalidades.

Aplicar as seguintes práticas na escolha de funcionalidades para enviar em contextos e ações ao Personalizador:

* Considere a legalidade e ética de usar certas características para algumas aplicações, e se características de aparência inocente podem ser proxies para outros que deseja ou deve evitar,
* Seja transparente para os utilizadores que os algoritmos e a análise de dados estão a ser usados para personalizar as opções que vêem.
* Pergunte a si mesmo: Os meus utilizadores gostariam e ficariam felizes se eu usasse esta informação para personalizar o conteúdo para eles? Sentir-me-ia confortável em mostrar-lhes como foi tomada a decisão de realçar ou esconder certos itens?
* Utilize dados comportamentais em vez de classificação ou segmentação com base noutras características. A informação demográfica era tradicionalmente utilizada pelos retalhistas por razões históricas – os atributos demográficos pareciam simples de recolher e agir antes de uma era digital, mas questionam-se quão relevante é a informação demográfica quando se tem dados reais de interação, contexário e histórico que se relacionam mais com as preferências e identidade dos utilizadores.
* Considere como evitar que as funcionalidades sejam 'falsificadas' por utilizadores mal-intencionados, o que, se explorados em grande número, podem levar a formação personalizada de formas enganosas para perturbar, envergonhar e assediar propositadamente certas classes de utilizadores.
* Quando apropriado e exequível, desenhe a sua aplicação para permitir que os seus utilizadores optem por não ter certas funcionalidades pessoais utilizadas. Estes poderiam ser agrupados, tais como "Informações de localização", "Informação do Dispositivo", "Histórico de Compras passadas" etc.


## <a name="computing-rewards-for-personalizer"></a>Recompensas computacional para Personalizar

O personalização esforça-se por melhorar a escolha de qual ação recompensar com base na pontuação de recompensa fornecida pela lógica de negócio da sua aplicação.

Uma pontuação de recompensa bem construída funcionará como um representante de curto prazo para um objetivo de negócio, que está ligado à missão de uma organização.

Por exemplo, recompensar em cliques fará com que o Serviço Personalizador procure cliques à custa de tudo o resto, mesmo que o que é clicado seja distrair ou não estar ligado a um resultado de negócio.

Como exemplo contrastante, um site de notícias pode querer definir recompensas ligadas a algo mais significativo do que cliques, tais como "O utilizador gastou tempo suficiente para ler o conteúdo?" "Clicaram em artigos ou referências relevantes?". Com o Personalizer é fácil ligar as métricas de perto às recompensas. Mas tenha cuidado para não confundir o envolvimento de curto prazo com bons resultados.

### <a name="unintended-consequences-from-reward-scores"></a>Consequências não intencionais das pontuações de recompensa
As pontuações de recompensa podem ser construídas com a melhor das intenções, mas ainda podem criar consequências inesperadas ou resultados inesperados sobre como o Personalizer classifica os conteúdos.

Considere os seguintes exemplos:

* Premiar a personalização de conteúdos de vídeo na percentagem do comprimento do vídeo assistido tenderá provavelmente a classificar vídeos mais curtos.
* Recompensar as partilhas nas redes sociais, sem análise de sentimento de como é partilhado ou do conteúdo em si, pode levar a classificar conteúdo ofensivo, não moderado ou inflamatório, o que tende a incitar muito "envolvimento", mas acrescenta pouco valor.
* Recompensar a ação em elementos de interface de utilizador que os utilizadores não esperam alterar pode interferir com a usabilidade e previsibilidade da interface do utilizador, onde os botões estão surpreendentemente a mudar de localização ou propósito sem aviso prévio, tornando mais difícil para certos grupos de utilizadores manterem-se produtivos.

Implementar estas boas práticas:

* Faça experiências offline com o seu sistema usando diferentes abordagens de recompensa para entender o impacto e os efeitos colaterais.
* Avalie as suas funções de recompensa e pergunte-se como é que uma pessoa extremamente ingénua dobraria a sua interpretação e alcançaria resultados indesejáveis com ela.


## <a name="responsible-design-considerations"></a>Considerações de design responsáveis

Seguem-se as áreas de conceção para implementações responsáveis da IA. Saiba mais sobre este quadro no [The Future Computed](https://news.microsoft.com/futurecomputed/).

![Valores de IA do Futuro Computado](media/ethics-and-responsible-use/ai-values-future-computed.png)

### <a name="accountability"></a>Responsabilidade
*As pessoas que concebem e implantam sistemas de IA devem ser responsáveis pela forma como os seus sistemas funcionam.*

* Crie diretrizes internas sobre como implementar personalizer, documentar e comunicá-los à sua equipa, executivos e fornecedores.
* Realize revisões periódicas de como as pontuações de recompensa são calculadas, realize avaliações offline para ver quais as funcionalidades que estão a afetar o Personaler e use os resultados para eliminar funcionalidades desnecessárias e desnecessárias.
* Comunique claramente aos seus utilizadores como o Personalizer é usado, para que finalidade e com que dados.
* Arquivar informações e ativos - como modelos, políticas de aprendizagem e outros dados - que o Personalizer utiliza para funcionar, para poder reproduzir resultados.

### <a name="transparency"></a>Transparência
*Os sistemas de IA devem ser compreensíveis.* Com Personalizer:

* *Dê aos utilizadores informações sobre como o conteúdo foi personalizado.* Por exemplo, pode mostrar aos seus utilizadores um botão rotulado `Why These Suggestions?` mostrando quais as características de topo do utilizador e as ações desempenharam um papel nos resultados do Personalizer.
* Certifique-se de que os seus termos de utilização mencionam que utilizará informações sobre os utilizadores e o seu comportamento para personalizar a experiência.

### <a name="fairness"></a>Justiça
*Os sistemas de IA devem tratar todas as pessoas de forma justa.*

* Não utilize o Personalizar para usar casos em que os resultados são a longo prazo, conseqüentes, ou envolvam danos reais.
* Não utilize funcionalidades que não sejam adequadas para personalizar conteúdos com, ou que possam ajudar a propagar preconceitos não utilizados. Por exemplo, qualquer pessoa com circunstâncias financeiras semelhantes deve ver as mesmas recomendações personalizadas para os produtos financeiros.
* Compreenda os enviesamentos que podem existir em funcionalidades que são provenientes de editores, ferramentas algorítmicas ou próprios utilizadores.

### <a name="reliability-and-safety"></a>Fiabilidade e segurança
*Os sistemas de IA devem funcionar de forma fiável e segura*. Para Personalizar:

* *Não forneça ações ao Personaler que não deveriam ser escolhidas.* Por exemplo, os filmes inadequados devem ser filtrados das ações para personalizar se fizerem uma recomendação para um utilizador anónimo ou menor de idade.
* *Gerencie o seu modelo Personalizer como um ativo de negócio.*  Considere com que frequência economizar e apoiar as políticas de modelo e aprendizagem por trás do seu Personalizer Loop, e de outra forma tratá-lo como um importante ativo de negócio. A reprodução de resultados passados é importante para a autoauditoria e para a melhoria da medição.
* *Forneça canais para obter feedback direto dos utilizadores.* Além de codificar verificações de segurança para garantir que apenas os públicos certos vêem o conteúdo certo, fornecem um mecanismo de feedback para os utilizadores reportarem conteúdo que pode ser surpreendente ou perturbador. Especialmente se o seu conteúdo vier de utilizadores ou 3º partido, considere usar o Microsoft Content Moderador ou ferramentas adicionais para rever e validar o conteúdo.
* *Realizar avaliações offline frequentes*. Isto irá ajudá-lo a monitorizar as tendências e a certificar-se de que a eficácia é conhecida.
* *Estabeleça um processo para detetar e agir sobre manipulação maliciosa*. Há atores que vão aproveitar a aprendizagem automática e a capacidade dos sistemas de IA de aprender com o seu ambiente para mudar o resultado para os seus objetivos. Se o seu uso de Personalizer estiver em posição de influenciar escolhas importantes, certifique-se de ter meios adequados para detetar e mitigar estas classes de ataques, incluindo a revisão humana em circunstâncias apropriadas.

### <a name="security-and-privacy"></a>Segurança e privacidade
*Os sistemas de IA devem ser seguros e respeitar a privacidade.* Ao utilizar o Personalizar:

* *Informe os utilizadores frontalmente sobre os dados recolhidos e como é utilizado e obtenha previamente o seu consentimento,* seguindo os regulamentos locais e da indústria.
* *Fornecer controlos de utilizador que protegem a privacidade.* Para aplicações que armazenam informações pessoais, considere fornecer um botão fácil de encontrar para funções como:
   * `Show me all you know about me`
   * `Forget my last interaction`
   * `Delete all you know about me`

Em alguns casos, estes podem ser legalmente exigidos. Considere as trocas em modelos de reciclagem periodicamente para que não contenham vestígios de dados eliminados.

### <a name="inclusiveness"></a>Inclusão
*Abordar uma ampla gama de necessidades e experiências humanas.*
* *Proporcionar experiências personalizadas para interfaces habilitadas a acessibilidade.* A eficiência que resulta de uma boa personalização - aplicada para reduzir a quantidade de esforço, movimento e repetição desnecessária nas interações - pode ser especialmente benéfica para as pessoas com deficiência.
* *Ajuste o comportamento da aplicação ao contexto.* Pode utilizar o Personalizer para desambiguar entre as intenções num chat bot, por exemplo, uma vez que a interpretação certa pode ser contextual e um tamanho pode não caber em todos.


## <a name="proactive-readiness-for-increased-data-protection-and-governance"></a>Prontidão proactiva para o aumento da proteção e governação de dados

É difícil prever mudanças específicas nos contextos regulamentares, mas, em geral, seria sensato ir além do quadro jurídico mínimo para garantir uma utilização respeitosa dos dados pessoais e proporcionar transparência e escolha relacionada com a tomada de decisões algorítmicas.


* Considere o planeamento com antecedência para uma situação em que possa haver novas restrições aos dados recolhidos por indivíduos, e há a necessidade de mostrar como foi usado para tomar decisões.
* Considere a prontidão extra onde os utilizadores podem incluir populações vulneráveis marginalizadas, crianças, utilizadores em vulnerabilidade económica, ou utilizadores de outra forma suscetíveis de influenciar a manipulação algorítmica.
* Considere a insatisfação generalizada com a forma como os programas e algoritmos de recolha de dados e algoritmos influenciadores pelo público têm sido reproduzidos, e como evitar erros estratégicos comprovados.


## <a name="proactive-assessments-during-your-project-lifecycle"></a>Avaliações proativas durante o ciclo de vida do seu projeto

Considere criar métodos para que os membros da equipa, utilizadores e empresários reportem preocupações em relação à utilização responsável, e crie um processo que dê prioridade à sua resolução e impeça represálias.

Qualquer pessoa que pense nos efeitos secundários do uso de qualquer tecnologia é limitada pela sua perspetiva e experiência de vida. Expandir o leque de opiniões disponíveis, trazendo vozes mais diversas para as suas equipas, utilizadores ou conselhos consultivos; de tal forma que é possível e encorajado para eles falarem. Considere materiais de formação e aprendizagem para expandir ainda mais o conhecimento da equipa neste domínio, e adicionar capacidade para discutir tópicos complexos e sensíveis.

Considere tratar tarefas relacionadas com a utilização responsável, tal como outras tarefas de crosscuting no ciclo de vida da aplicação, tais como tarefas relacionadas com a experiência do utilizador, segurança ou DevOps. Estas tarefas e os seus requisitos não podem ser uma reflexão posterior. A utilização responsável deve ser discutida e verificada ao longo do ciclo de vida da aplicação.

## <a name="questions-and-feedback"></a>Perguntas e feedback

A Microsoft está continuamente a en esforçar-se em ferramentas e documentos para o ajudar a agir sobre estas responsabilidades. A nossa equipa convida-o a [fornecer feedback à Microsoft](mailto:cogsvcs-RL-feedback@microsoft.com?subject%3DPersonalizer%20Responsible%20Use%20Feedback&body%3D%5BPlease%20share%20any%20question%2C%20idea%20or%20concern%5D)  se acreditar que ferramentas adicionais, funcionalidades de produtos e documentos o ajudariam a implementar estas diretrizes para a utilização do Personalizer.

## <a name="recommended-reading"></a>Leitura recomendada

* Veja os seis princípios da Microsoft para o desenvolvimento responsável da IA publicados no livro de janeiro de 2018, [The Future Computed](https://news.microsoft.com/futurecomputed/)
* [Quem é o dono do futuro?](https://www.goodreads.com/book/show/15802693-who-owns-the-future) por Jaron Lanier.
* [Armas de Destruição Matemática](https://www.goodreads.com/book/show/28186015-weapons-of-math-destruction) por - Cathy O'Neil
* [Ética e Ciência dos Dados](https://www.oreilly.com/library/view/ethics-and-data/9781492043898/) pelo DJ Patil, Hilary Mason, Mike Loukides.
* [Código de Ética ACM](https://www.acm.org/code-of-ethics)
* [Lei de Não Discriminação de Informação Genética - GINA](https://en.wikipedia.org/wiki/Genetic_Information_Nondiscrimination_Act)
* [Princípios FATML para Algoritmos Responsáveis](https://www.fatml.org/resources/principles-for-accountable-algorithms)


## <a name="next-steps"></a>Passos seguintes

[Características: ação e contexto.](concepts-features.md)
