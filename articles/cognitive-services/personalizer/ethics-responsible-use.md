---
title: Ética e uso responsável - Personalizer
titleSuffix: Azure Cognitive Services
description: Estas diretrizes destinam-se a ajudá-lo a implementar a personalização de uma forma que o ajude a criar confiança na sua empresa e serviço. Certifique-se de parar para pesquisar, aprender e deliberar sobre o impacto da personalização na vida das pessoas. Em caso de dúvida, procure orientação.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 06/12/2019
ms.author: diberry
ms.openlocfilehash: e422284b871214dbeca31b5dd17b9177a18ad3c8
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/01/2020
ms.locfileid: "80478104"
---
# <a name="guidelines-for-responsible-implementation-of-personalizer"></a>Orientações para a implementação responsável do Personalizer

Para que as pessoas e a sociedade percebam todo o potencial da IA, as implementações devem ser desenhadas de forma a ganharem a confiança daqueles que adicionam IA às suas aplicações e aos utilizadores de aplicações construídas com IA. Estas diretrizes destinam-se a ajudá-lo a implementar o Personalizer de uma forma que o ajude a criar confiança na sua empresa e serviço. Certifique-se de parar para pesquisar, aprender e deliberar sobre o impacto da personalização na vida das pessoas. Em caso de dúvida, procure orientação.

Estas orientações não se destinam a aconselhamento jurídico e deve assegurar-se separadamente de que a sua candidatura está em conformidade com os desenvolvimentos acelerados da lei nesta área e no seu setor.

Além disso, ao conceber a sua aplicação utilizando o Personalizer, deve considerar um vasto conjunto de responsabilidades que tem ao desenvolver qualquer sistema de IA centrado em dados, incluindo ética, privacidade, segurança, segurança, inclusão, transparência e responsabilidade. Pode ler mais sobre isso na secção de [leitura Recomendada.](#recommended-reading)

Pode utilizar o seguinte conteúdo como lista de verificação inicial e personalizá-lo e refiná-lo para o seu cenário. Este documento tem duas secções principais: A primeira dedica-se a destacar considerações de uso responsável na escolha de cenários, funcionalidades e recompensas para o Personalizer. A segunda toma um conjunto de valores que a Microsoft acredita que deve ser considerado na construção de sistemas de IA, e fornece sugestões e riscos atuais sobre como o seu uso do Personalizer os influencia.


## <a name="your-responsibility"></a>Sua responsabilidade

Todas as diretrizes para uma implementação responsável baseiam-se na base de que os desenvolvedores e empresas que utilizam o Personalizer são responsáveis e responsáveis pelos efeitos da utilização destes algoritmos na sociedade. Se está a desenvolver uma aplicação que a sua organização irá implementar, deve reconhecer o seu papel e responsabilidade pelo seu funcionamento e como isso afeta as pessoas. Se está a desenhar uma aplicação a ser implementada por terceiros, chegue a um entendimento partilhado com eles de quem é, em última análise, responsável pelo comportamento da aplicação, e documente esse entendimento.

A confiança baseia-se na noção de compromissos cumpridos - considere os seus utilizadores, a sociedade e o enquadramento legal em que as suas aplicações trabalham, para identificar compromissos explícitos e implícitos que possam ter.

A Microsoft está continuamente a apostar nas suas ferramentas e documentos para o ajudar a agir sobre estas responsabilidades. [Forneça feedback à Microsoft](mailto:cogsvcs-RL-feedback@microsoft.com?subject%3DPersonalizer%20Responsible%20Use%20Feedback&body%3D%5BPlease%20share%20any%20question%2C%20idea%20or%20concern%5D) se acreditar que ferramentas adicionais, funcionalidades e documentos do produto o ajudariam a implementar estas diretrizes para usar o Personalizer.


## <a name="factors-for-responsibly-implementing-personalizer"></a>Fatores para implementar de forma responsável o Personalizer

Implementar o Personalizer pode ser de grande valor para os seus utilizadores e para o seu negócio. Para implementar o Personalizer de forma responsável, comece por considerar as seguintes orientações quando:

* Escolher usar casos para aplicar personalização.
* Construção de funções de [recompensa.](concept-rewards.md)
* Escolhendo quais [funcionalidades](concepts-features.md) sobre o contexto e possíveis ações que utilizará para personalização.


## <a name="choosing-use-cases-for-personalizer"></a>Escolher casos de utilização para Personalizer

Usar um serviço que aprende a personalizar conteúdo e interfaces de utilizador é útil. Também pode ser mal aplicado se a forma como a personalização cria efeitos colaterais negativos no mundo real, incluindo se os utilizadores desconhecem a personalização dos conteúdos.

Exemplos de utilizações do Personalizer com potencial acrescido para efeitos colaterais negativos ou falta de transparência incluem cenários em que a "recompensa" depende de muitos fatores complexos de longo prazo que, quando simplificados em uma recompensa imediata podem ter resultados desfavoráveis para os indivíduos. Estas tendem a ser consideradas escolhas "consequentes", ou escolhas que envolvem um risco de danos. Por exemplo:


* **Finanças**: Personalizar ofertas de produtos de empréstimos, financeiros e seguros, onde os fatores de risco são baseados em dados que os indivíduos desconhecem, não podem obter ou não podem contestar.
* **Educação**: Personalizar fileiras para cursos escolares e instituições de ensino onde as recomendações podem propagar enviesamentos e reduzir a consciência dos utilizadores sobre outras opções.
* **Democracia e Participação Cívica**: Personalizar conteúdos para os utilizadores com o objetivo de influenciar opiniões é consequente e manipulador.
* **Avaliação de recompensa de terceiros**: Personalizar itens onde a recompensa se baseia numa última avaliação de terceiros do utilizador, em vez de ter uma recompensa gerada pelo próprio comportamento do utilizador.
* **Intolerância à Exploração**: Qualquer situação em que o comportamento de exploração do Personalizer possa causar danos.

Ao escolher casos de utilização para Personalizar:

* Inicie o processo de design considerando como a personalização ajuda os seus utilizadores.
* Considere as consequências negativas no mundo real se alguns itens não estiverem classificados para os utilizadores devido a padrões de personalização ou exploração.
* Considere se o seu caso de utilização constitui um tratamento automatizado que afeta significativamente os titulares de dados que são regulados nos termos do artigo 22.º do [RGPD](https://eur-lex.europa.eu/legal-content/EN/TXT/?uri=CELEX:32016R0679) ou de outras leis.
* Considere laços de profecia autorrealizáveis. Isto pode acontecer se uma recompensa de personalização treinar um modelo para que possa posteriormente excluir um grupo demográfico de aceder a conteúdos relevantes. Por exemplo, a maioria das pessoas num bairro de baixos rendimentos não obtém uma oferta de seguro premium, e lentamente ninguém na vizinhança tende a ver a oferta se não houver exploração suficiente.
* Guarde cópias de modelos e políticas de aprendizagem no caso de ser necessário reproduzir o Personalizer no futuro. Pode fazê-lo periodicamente ou em cada período de atualização do modelo.
* Considere o nível de exploração adequado para o espaço e como usá-lo como uma ferramenta para mitigar os efeitos da "câmara de eco".


## <a name="selecting-features-for-personalizer"></a>Selecionar funcionalidades para Personalizar

Personalizar conteúdo depende de ter informações úteis sobre o conteúdo e o utilizador. Tenha em mente, para algumas aplicações e indústrias, algumas funcionalidades do utilizador podem ser consideradas direta ou indiretamente discriminatórias e potencialmente ilegais.

Considere o efeito destas características:

* **Demografia do utilizador**: Características relativas ao sexo, sexo, idade, raça, religião: Estas características podem não ser permitidas em determinadas aplicações por razões regulamentares, e pode não ser ético personalizar à sua volta porque a personalização propagaria generalizações e preconceitos. Um exemplo desta propagação parcial é um posto de trabalho para a engenharia que não é mostrado a públicos idosos ou baseados em género.
* **Informações locais**: Em muitos lugares do mundo, as informações de localização (como um código postal, código postal ou nome de bairro) podem ser altamente correlacionadas com rendimento, raça e religião.
* **Perceção do Utilizador da Equidade**: Mesmo nos casos em que a sua aplicação está a tomar decisões sólidas, considere o efeito dos utilizadores que percem que o conteúdo apresentado na sua aplicação muda de uma forma que pareça estar correlacionada com funcionalidades que seriam discriminatórias.
* **Enviesamento não intencional em características**: Existem tipos de preconceitos que podem ser introduzidos utilizando características que apenas afetam um subconjunto da população. Isto requer uma atenção extra se as funcionalidades estiverem a ser geradas de forma algorítmica, como quando se utiliza a análise de imagem para extrair itens numa imagem ou análise de texto para descobrir entidades em texto. Tenha consciência das características dos serviços que utiliza para criar estas funcionalidades.

Aplique as seguintes práticas ao escolher funcionalidades para enviar em contextos e ações ao Personalizer:

* Considere a legalidade e ética de usar certas funcionalidades para algumas aplicações, e se características de aparência inocente podem ser proxies para outros que você quer ou deve evitar,
* Seja transparente para os utilizadores que os algoritmos e a análise de dados estão a ser usados para personalizar as opções que vêem.
* Pergunte a si mesmo: Os meus utilizadores importar-se-iam e ficariam felizes se eu usasse esta informação para personalizar o conteúdo para eles? Sentir-me-ia à vontade para lhes mostrar como foi tomada a decisão de destacar ou esconder certos itens?
* Utilize dados comportamentais em vez de classificação ou segmentação com base em outras características. A informação demográfica era tradicionalmente utilizada pelos retalhistas por razões históricas – os atributos demográficos pareciam simples de recolher e agir antes de uma era digital, mas questionam o quão relevante é a informação demográfica quando se tem dados reais de interação, contextual e histórico que se relacionam mais com as preferências e identidade dos utilizadores.
* Considere como evitar que as funcionalidades sejam 'falsificadas' por utilizadores maliciosos, o que se explorado em grande número pode levar a treinar o Personalizer de forma enganosa para perturbar, envergonhar e assediar propositadamente certas classes de utilizadores.
* Quando apropriado e exequível, desenhe a sua aplicação para permitir que os seus utilizadores optem ou optem por não ter determinadas funcionalidades pessoais utilizadas. Estes poderiam ser agrupados, tais como "Informações de localização", "Informação do Dispositivo", "Histórico de Compras Passadas" etc.


## <a name="computing-rewards-for-personalizer"></a>Recompensas de computação para Personalizer

O Personalizer esforça-se por melhorar a escolha de que ação premiar com base na pontuação de recompensa fornecida pela lógica de negócio da sua aplicação.

Uma pontuação de recompensa bem construída funcionará como um representante de curto prazo para um objetivo de negócio, que está ligado à missão de uma organização.

Por exemplo, recompensar em cliques fará com que o Serviço personalizado procure cliques à custa de tudo o resto, mesmo que o que está clicado seja distrair ou não estar ligado a um resultado de negócio.

Como exemplo contrastante, um site de notícias pode querer definir recompensas ligadas a algo mais significativo do que cliques, tais como "O utilizador passou tempo suficiente para ler o conteúdo?" "Clicaram em artigos ou referências relevantes?". Com o Personalizer é fácil ligar as métricas de perto às recompensas. Mas tenha cuidado para não confundir o envolvimento dos utilizadores a curto prazo com bons resultados.

### <a name="unintended-consequences-from-reward-scores"></a>Consequências não intencionais dos resultados das recompensas
As pontuações de recompensa podem ser construídas com a melhor das intenções, mas ainda podem criar consequências inesperadas ou resultados não intencionais sobre como o Personalizer classifica o conteúdo.

Considere os seguintes exemplos:

* Recompensar a personalização de conteúdos de vídeo na percentagem do comprimento de vídeo observado provavelmente tenderá a classificar vídeos mais curtos.
* Recompensar as partilhas das redes sociais, sem análise de sentimentos de como é partilhado ou o conteúdo em si, pode levar a conteúdos ofensivos, sem moderação ou inflamatórios, o que tende a incitar muito "envolvimento", mas acrescenta pouco valor.
* Recompensar a ação em elementos de interface do utilizador que os utilizadores não esperam alterar pode interferir com a usabilidade e previsibilidade da interface do utilizador, onde os botões estão surpreendentemente a mudar de localização ou propósito sem aviso prévio, dificultando a continuação de certos grupos de utilizadores.

Implementar estas boas práticas:

* Faça experiências offline com o seu sistema usando diferentes abordagens de recompensa para entender o impacto e os efeitos colaterais.
* Avalie as suas funções de recompensa e pergunte a si mesmo como uma pessoa extremamente ingénua dobraria a sua interpretação e alcançaria resultados indesejáveis com ela.


## <a name="responsible-design-considerations"></a>Considerações de design responsáveis

Seguem-se áreas de conceção para implementações responsáveis de IA. Saiba mais sobre este quadro no [The Future Computed](https://news.microsoft.com/futurecomputed/).

![Valores de IA do Futuro Computado](media/ethics-and-responsible-use/ai-values-future-computed.png)

### <a name="accountability"></a>Responsabilidade
*As pessoas que concebem e implementam sistemas de IA devem ser responsáveis pelo funcionamento dos seus sistemas.*

* Crie diretrizes internas sobre como implementar personalizer, documentar e comunicá-las à sua equipa, executivos e fornecedores.
* Execute revisões periódicas de como as pontuações de recompensa são calculadas, execute avaliações offline para ver quais as funcionalidades que estão a afetar o Personalizer, e use os resultados para eliminar funcionalidades desnecessárias e desnecessárias.
* Comunique claramente aos seus utilizadores como o Personalizer é usado, para que finalidade e com que dados.
* Arquivo de informação e ativos - como modelos, políticas de aprendizagem e outros dados - que o Personalizer usa para funcionar, para ser capaz de reproduzir resultados.

### <a name="transparency"></a>Transparência
*Os sistemas de IA devem ser compreensíveis.* Com Personalizer:

* *Dar aos utilizadores informações sobre como o conteúdo foi personalizado.* Por exemplo, pode mostrar aos seus `Why These Suggestions?` utilizadores um botão rotulado mostrando quais as principais funcionalidades do utilizador e as ações desempenharam um papel nos resultados do Personalizer.
* Certifique-se de que os seus termos de uso mencionam que utilizará informações sobre os utilizadores e o seu comportamento para personalizar a experiência.

### <a name="fairness"></a>Justiça
*A IA Systems deve tratar todas as pessoas de forma justa.*

* Não utilize o Personalizer para usar casos em que os resultados sejam a longo prazo, consequentes ou envolvam danos reais.
* Não utilize funcionalidades que não sejam adequadas para personalizar conteúdo, ou que possam ajudar a propagar preconceitos indesejados. Por exemplo, qualquer pessoa com circunstâncias financeiras semelhantes deve ver as mesmas recomendações personalizadas para os produtos financeiros.
* Compreenda os enviesamentos que podem existir em funcionalidades que são provenientes de editores, ferramentas algorítmicas ou os próprios utilizadores.

### <a name="reliability-and-safety"></a>Fiabilidade e segurança
*Os Sistemas De IA devem funcionar*de forma fiável e segura . Para Personalizar:

* *Não forneça ações ao Personalizer que não deveriam ser escolhidas.* Por exemplo, os filmes inadequados devem ser filtrados das ações para personalizar se fizer uma recomendação para um utilizador anónimo ou menor de idade.
* *Gerencie o seu modelo Personalizer como um ativo*de negócio.  Considere com que frequência economizar e apoiar as políticas de modelo e aprendizagem por trás do seu Personalizer Loop, e de outra forma tratá-lo como um importante ativo de negócio. A reprodução dos resultados anteriores é importante para a autoauditoria e a melhoria da medição.
* *Forneça canais para obter feedback direto dos utilizadores.* Além de codificar verificações de segurança para garantir que apenas o público certo veja o conteúdo certo, forneça um mecanismo de feedback para os utilizadores reportarem conteúdo que possa ser surpreendente ou perturbador. Especialmente se o seu conteúdo vier de utilizadores ou 3.º, considere utilizar o Moderador de Conteúdo do Microsoft ou ferramentas adicionais para rever e validar conteúdos.
* *Realizar avaliações offline frequentes*. Isto irá ajudá-lo a monitorizar as tendências e a garantir que a eficácia é conhecida.
* *Estabeleça um processo de deteção e atuação em manipulação maliciosa.* Há atores que vão aproveitar a capacidade de aprendizagem automática e de sistemas de IA aprenderem com o seu ambiente para mudar o resultado para os seus objetivos. Se o seu uso do Personalizer estiver em posição de influenciar escolhas importantes, certifique-se de ter meios adequados para detetar e mitigar estas classes de ataques, incluindo a revisão humana em circunstâncias apropriadas.

### <a name="security-and-privacy"></a>Segurança e privacidade
*Os Sistemas AI devem ser seguros e respeitar*a privacidade. Ao utilizar o Personalizer:

* *Informe previamente os utilizadores sobre os dados recolhidos e como são utilizados e obtenha previamente*o seu consentimento, seguindo os regulamentos locais e do setor.
* *Fornecer controlos de utilizador que protejam a privacidade.* Para aplicações que armazenem informações pessoais, considere fornecer um botão fácil de encontrar para funções como:
   * `Show me all you know about me`
   * `Forget my last interaction`
   * `Delete all you know about me`

Em alguns casos, estes podem ser legalmente exigidos. Considere as trocas em modelos de reconversão periodicamente para que não contenham vestígios de dados apagados.

### <a name="inclusiveness"></a>Inclusão
*Abordar uma ampla gama de necessidades e experiências humanas.*
* *Proporcionar experiências personalizadas para interfaces habilitadas para acessibilidades.* A eficiência que resulta de uma boa personalização - aplicada para reduzir a quantidade de esforço, movimento e repetição desnecessária nas interações - pode ser especialmente benéfica para as pessoas com deficiência.
* *Ajuste o comportamento da aplicação ao contexto.* Pode usar o Personalizer para desambiguar entre intenções num chat bot, por exemplo, uma vez que a interpretação certa pode ser contextual e um tamanho pode não caber em todos.


## <a name="proactive-readiness-for-increased-data-protection-and-governance"></a>Prontidão proactiva para maior proteção e governação de dados

É difícil prever mudanças específicas nos contextos regulamentares, mas, em geral, seria sensato ir além do quadro jurídico mínimo para garantir uma utilização respeitosa dos dados pessoais e proporcionar transparência e escolha relacionadas com a tomada de decisões algorítmicas.


* Considere planear com antecedência uma situação em que pode haver novas restrições aos dados recolhidos de indivíduos, e é necessário mostrar como foi usado para tomar decisões.
* Considere a prontidão extra em que os utilizadores possam incluir populações vulneráveis marginalizadas, crianças, utilizadores em vulnerabilidade económica ou utilizadores de outra forma suscetíveis a influenciar de manipulação algorítmica.
* Considere a insatisfação generalizada com a forma como os programas e algoritmos de recolha de dados influenciadores pelo público e influenciadores pelo público têm jogado fora, e como evitar erros estratégicos comprovados.


## <a name="proactive-assessments-during-your-project-lifecycle"></a>Avaliações proativas durante o seu ciclo de vida do projeto

Considere a criação de métodos para os membros da equipa, utilizadores e empresários reportarem preocupações relacionadas com a utilização responsável, e criar um processo que priorize a sua resolução e impeça represálias.

Qualquer pessoa que pense nos efeitos secundários do uso de qualquer tecnologia é limitada pela sua perspetiva e experiência de vida. Expandir o leque de opiniões disponíveis trazendo vozes mais diversas para as suas equipas, utilizadores ou conselhos consultivos; de tal forma que é possível e encorajado para que eles falem. Considere materiais de formação e aprendizagem para expandir ainda mais o conhecimento da equipa neste domínio, e adicionar capacidade para discutir tópicos complexos e sensíveis.

Considere tratar tarefas relativas a uma utilização responsável, tal como outras tarefas de crosscutting no ciclo de vida da aplicação, tais como tarefas relacionadas com a experiência do utilizador, segurança ou DevOps. Estas tarefas e os seus requisitos não podem ser uma reflexão posterior. A utilização responsável deve ser discutida e verificada ao longo do ciclo de vida da aplicação.

## <a name="questions-and-feedback"></a>Perguntas e feedback

A Microsoft está continuamente a apostar em ferramentas e documentos para o ajudar a agir sobre estas responsabilidades. A nossa equipa convida-o a [fornecer feedback à Microsoft](mailto:cogsvcs-RL-feedback@microsoft.com?subject%3DPersonalizer%20Responsible%20Use%20Feedback&body%3D%5BPlease%20share%20any%20question%2C%20idea%20or%20concern%5D) se acreditar que ferramentas adicionais, funcionalidades e documentos o ajudariam a implementar estas diretrizes para usar o Personalizer.

## <a name="recommended-reading"></a>Leitura recomendada

* Veja os seis princípios da Microsoft para o desenvolvimento responsável da IA publicado no livro de janeiro de 2018, [The Future Computed](https://news.microsoft.com/futurecomputed/)
* [Quem é o dono do futuro?](https://www.goodreads.com/book/show/15802693-who-owns-the-future) por Jaron Lanier.
* [Armas de Destruição Matemática](https://www.goodreads.com/book/show/28186015-weapons-of-math-destruction) por - Cathy O'Neil
* [Ética e Ciência dos Dados](https://www.oreilly.com/library/view/ethics-and-data/9781492043898/) por DJ Patil, Hilary Mason, Mike Loukides.
* [Código de Ética da ACM](https://www.acm.org/code-of-ethics)
* [Lei de Não Discriminação de Informação Genética - GINA](https://en.wikipedia.org/wiki/Genetic_Information_Nondiscrimination_Act)
* [Princípios FATML para Algoritmos Responsáveis](https://www.fatml.org/resources/principles-for-accountable-algorithms)


## <a name="next-steps"></a>Passos seguintes

[Características: ação e contexto.](concepts-features.md)
