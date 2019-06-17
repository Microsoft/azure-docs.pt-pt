---
title: Ética e de utilização responsável - Personalizer
titleSuffix: Azure Cognitive Services
description: Essas diretrizes são destinadas para ajudar a implementar a personalização de uma forma que ajuda a criar a confiança na sua empresa e o serviço. Certifique-se de que colocar em pausa para pesquisa, aprenda e cuidadoso no impacto de personalização na vida das pessoas. Em caso de dúvida, buscam a documentação de orientação.
services: cognitive-services
author: edjez
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: overview
ms.date: 06/12/2019
ms.author: edjez
ms.openlocfilehash: a5bdee7ec99367d004260bd77e3b0695a1877dd6
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67055528"
---
# <a name="guidelines-for-responsible-implementation-of-personalizer"></a>Diretrizes para implementação responsável de Personalizer

Para as pessoas e das sociedades realizar plenamente o potencial da inteligência artificial, implementações precisam ser projetado de forma que eles ganhe a confiança da adição de ia para seus aplicativos e os usuários de aplicativos criados com IA. Essas diretrizes são destinadas para ajudar a implementar Personalizer de forma que ajuda a criar a confiança na sua empresa e o serviço. Certifique-se de que colocar em pausa para pesquisa, aprenda e cuidadoso no impacto de personalização na vida das pessoas. Em caso de dúvida, buscam a documentação de orientação.

Estas diretrizes destinam-se não como aconselhamento legal e separadamente deve garantir que seu aplicativo está em conformidade com os desenvolvimentos rápidos em termos do lei nessa área e no seu setor.

Além disso, projetar a sua aplicação com o Personalizer, deve considerar um amplo conjunto de responsabilidades, que tem durante o desenvolvimento de qualquer sistema de IA centrados em dados, incluindo ética, privacidade, segurança, segurança, inclusão, transparência e responsabilidade. Pode ler mais sobre isso com o [leitura recomendada](#recommended-reading) secção.

Pode utilizar o seguinte conteúdo como uma lista de verificação de arranque e personalizar e refiná-la ao seu cenário. Este documento apresenta duas seções principais: A primeira é dedicada ao destaque de considerações de utilização responsável ao escolher cenários, recursos e remunerações para Personalizer. O segundo take um conjunto de valores a Microsoft acredita que deve ser considerado ao criar sistemas de IA e fornece sugestões úteis e riscos sobre como a utilização do Personalizer influencia-los. 


## <a name="your-responsibility"></a>Sua responsabilidade

Todas as diretrizes de implementação responsável baseiam-se a base que os programadores e empresas que utilizam Personalizer é responsáveis e responsáveis pelos efeitos de usar esses algoritmos na sociedade. Se estiver a desenvolver uma aplicação que irá implementar a sua organização, deve reconhecer a sua função e a responsabilidade pela sua operação e como ele afeta as pessoas. Se está a estruturar uma aplicação para ser implementada por uma aplicação de terceiros, se para uma compreensão partilhada com eles de quem é responsável pelo comportamento do aplicativo e essa compreensão de documentos.

Confiança baseia-se a noção de compromissos cumpridas, considere os utilizadores, sociedade e a estrutura legal seus aplicativos funciona, para identificar os compromissos explícitos e implícitos, eles podem ter.

Microsoft continuamente é colocar o esforço em suas ferramentas e documentos para o ajudar a tomar decisões sobre essas responsabilidades. [Fornecer comentários à Microsoft](mailto:cogsvcs-RL-feedback@microsoft.com?subject%3DPersonalizer%20Responsible%20Use%20Feedback&body%3D%5BPlease%20share%20any%20question%2C%20idea%20or%20concern%5D) se considerar que as ferramentas adicionais, funcionalidades do produto e os documentos ajuda a implementar essas diretrizes para usar Personalizer.


## <a name="factors-for-responsibly-implementing-personalizer"></a>Fatores para a implementação com responsabilidade Personalizer

Implementar Personalizer pode ser muito importantes para os seus utilizadores e a sua empresa. Para implementar Personalizer com responsabilidade, comece por considerar as seguintes diretrizes quando:

* Escolher casos de utilização para aplicar personalização.
* Criando [recompensar funções](https://github.com/Azure/personalization-rl/blob/master/docs/concepts-rewards.md).
* Escolher qual [funcionalidades](https://github.com/Azure/personalization-rl/blob/master/docs/concepts-features.md) sobre as ações de contexto e possíveis que irá utilizar para personalização.


## <a name="choosing-use-cases-for-personalizer"></a>Escolher casos de utilização para Personalizer

Utilização de um serviço que aprende a personalizar as interfaces de utilizador e de conteúdo é útil. Ele pode também ser aplicada incorretamente se a forma como a personalização cria os efeitos negativos do lado do mundo real, incluindo se os utilizadores estão cientes de personalização de conteúdo. 

Exemplos de usos de Personalizer com o cliente potencial para os efeitos negativos de lado ou uma falta de transparência incluem cenários em que a "recompensa" depende complexo de longo prazo de muitos fatores que, quando muito simplificada numa recompensa imediata pode ter unfavorable resultados para indivíduos. Eles tendem a ser considerados opções "consequentes" ou opções que envolvem um risco de danos. Por exemplo: 


* **Finanças**: Personalizar ofertas na empréstimo, financeiro e os produtos seguros, onde os fatores de risco são baseadas em dados, as pessoas não sabem sobre, não é possível obter ou não é possível dispute. 
* **Educação**: Personalizar classificações para cursos da instituição de ensino e instituições de ensino, onde as recomendações podem propagar as tendências e reduzir a perceção dos utilizadores de outras opções.
* **Democracia e participação cívica**: Personalizar o conteúdo para os utilizadores com o objetivo de opiniões influência dos é consequentes e manipulative.
* **Avaliação de recompensa de terceiros**: Personalizar os itens em que a recompensa baseia-se num segundo 3rd avaliação de terceiros do utilizador, em vez de ter uma recompensa gerada pelo comportamento do utilizador.
* **Intolerance para exploração**: Qualquer situação em que o comportamento de exploração de Personalizer pode causar danos.

Ao escolher casos de utilização para Personalizer:

* Inicie o processo de design considerando como a personalização ajuda os usuários.
* Se alguns itens não são classificados para os utilizadores devido a padrões de personalização ou exploração, considere as conseqüências negativas no mundo real.
* Considere Self-gratificante prophecy loops. Isto pode acontecer se uma recompensa de personalização prepara um modelo para que, em seguida, pode ainda mais excluir um grupo demográficos de aceder ao conteúdo relevante. Por exemplo, a maioria das pessoas num ambiente de baixa renda não obter uma oferta de seguros de premium e lentamente ninguém na vizinhança tende a ver a oferta de todo.
* Guarde cópias de modelos e as políticas de aprendizagem, caso seja necessário reproduzir Personalizer no futuro. Pode fazê-lo periodicamente ou em cada período de atualização do modelo.
* Considere o nível de exploração adequada para o espaço e como utilizá-la como uma ferramenta para atenuar os efeitos de "câmara de eco".


## <a name="selecting-features-for-personalizer"></a>Selecionar as funcionalidades para Personalizer

Personalizar o conteúdo depende das informações úteis sobre o conteúdo e o utilizador. Tenha em mente, para algumas aplicações e setores, em alguns recursos podem ser direta ou indiretamente considerados discriminatory e potencialmente ilegal de utilizador.

Considere o efeito destas funcionalidades:

* **Dados demográficos do utilizador**: Recursos relativos à sexo, sexo, idade, corrida, religion: Estas funcionalidades podem não ser permitidas em determinados aplicativos por motivos de regulamentação e pode não ser ética personalizar de acordo com-los uma vez que a personalização seria propagar generalizations e tendência. Um exemplo dessa propagação de tendência é uma tarefa de lançamento para não visível aos idosa baseada em sexo ou públicos-alvo de engenharia.
* **Informações de Localidade**: Em muitos lugares do mundo, informações de localização (por exemplo, um código postal, o código postal ou o nome de vizinhança) podem ser altamente correlacionadas com o rendimento, corrida e religion.
* **A percepção do usuário de imparcialidade**: Mesmo nos casos em que seu aplicativo é tomar decisões sensatas sobre, considere o efeito de utilizadores que perceber esse conteúdo exibido nas alterações de aplicação de uma forma que parece ser correlacionados com recursos que seriam discriminatory.
* **Tendência de não-intencionais nas funcionalidades**:  Existem tipos de tendências que podem ser introduzidos ao utilizar funcionalidades que afetam apenas um subconjunto da população. Isso exige atenção especial se funcionalidades estão a ser geradas de forma algorítmica, por exemplo, quando utilizar a imagem análises para extrair itens numa imagem ou texto de análise para detetar as entidades no texto. Tornar-se em consideração as características de serviços que utiliza para criar esses recursos.

Aplicam-se as seguintes práticas ao escolher recursos envie em contextos e ações para Personalizer:

* Considere o legalidade e ética da utilização de funcionalidades específicas para alguns aplicativos, e se funcionalidades inocente podem ser proxies para outras pessoas pretende ou deve evitar,
* Ser transparente para os utilizadores que algoritmos e análise de dados estão a ser utilizadas para personalizar as opções de Verão.
* Pergunte-se: Os meus utilizadores seriam cuidado e ser Feliz se eu tivesse usado essas informações para personalizar o conteúdo para os mesmos? Seria se sentir à vontade mostro a eles como foi tomada a decisão de realce ou ocultar determinados itens?
* Utilize comportamental em vez de classificação ou segmentação de dados com base nas outras características. Informações demográficas tradicionalmente foi utilizadas pelo varejistas para motivos históricos – atributos demográficos pareciam simples de recolher e processar antes de uma era digital, - mas pergunta como relevantes informações demográficas são quando tem interação real, dados históricos e contextuais que se relaciona com mais de perto as preferências e identidade dos usuários.
* Considere como impedir que os recursos a ser 'falsificado' por usuários mal-intencionados, que se forem exploradas um grande número podem levar a formação Personalizer no equivocado formas de interromper propositadamente, questionem e importunar determinadas classes de utilizadores. 
* Quando adequado e viável, crie uma aplicação para permitir que os utilizadores optar ativamente por participar no ou optar por ter determinadas funcionalidades pessoais utilizadas. Estes podem ser agrupadas, por exemplo, "Informações de localização", "Informações de dispositivo", "Histórico de compras nos últimos" etc.


## <a name="computing-rewards-for-personalizer"></a>Computação de remunerações para Personalizer

Personalizer está empenhada em melhorar a escolha da ação a recompensa, com base na classificação de recompensa fornecida pela sua lógica de negócios do aplicativo.

Uma pontuação de recompensa bem criados atuará como um proxy de curto prazo para um objetivo de negócios, que é vinculado à missão de uma organização.

Por exemplo, recompensar a cliques fará com que os cliques de seek Personalizer serviço às custas de todo o resto, mesmo que o que é clicado no seja distração ou não vinculadas a um resultado de negócio.

Como um exemplo de contraste, um site de notícias querer definir remunerações vinculadas para algo mais significativo que clica, tais como "Foi o usuário gastar tempo suficiente para ler o conteúdo?" "Clicarem em artigos relevantes ou referências?". Com Personalizer é fácil vincular as métricas de perto para remunerações. Mas tenha cuidado para não confound a curto prazo interação do utilizador com os bons resultados.

### <a name="unintended-consequences-from-reward-scores"></a>Conseqüências indesejadas de pontuações de recompensa
Pontuações de recompensa podem ser criadas com o melhor dos intenções, mas podem ainda criar conseqüências inesperadas ou provoca resultados, na forma como Personalizer classifica o conteúdo. 

Considere os seguintes exemplos:

* Recompensar a personalização de conteúdo de vídeo, a porcentagem da duração do vídeo assistiu provavelmente será tendem a classificação vídeos mais curtos.
* Recompensar a partilhas de redes sociais, sem a análise de sentimentos de como são partilhado ou o conteúdo em si, pode levar a classificação de conteúdo ofensivo, unmoderated ou inflammatory, o que tende a incitem muita "engagement", mas adiciona pouco valor.
* Recompensar a ação em elementos de interface do usuário que os utilizadores não esperam alterá-pode interferir com a facilidade de utilização e a previsibilidade da interface do usuário, onde a botões são surpreendentemente alterar localização ou para fins sem aviso, tornando mais difícil para determinados grupos de utilizadores para se manterem produtivos.

Implemente essas práticas recomendadas:

* Execute experimentações offline com o seu sistema usando a recompensa diferentes abordagens para compreender o impacto e efeitos colaterais.
* Avalie as suas funções de recompensa e pergunte-se como seria uma pessoa de ingênua extremamente desviar sua interpretação e alcançar os resultados indesejáveis com ele.


## <a name="responsible-design-considerations"></a>Considerações de design responsável

Seguem-se as áreas de design para implementações de responsáveis de IA. Saiba mais sobre a essa estrutura no [calculada de futuro o](https://news.microsoft.com/futurecomputed/).

![Valores de AI do futuro calculada](media/ethics-and-responsible-use/ai-values-future-computed.png)

### <a name="accountability"></a>Responsabilidade
*As pessoas que projetar e implantar sistemas de IA tem de ser responsáveis pela como seus sistemas operam*. 

* Criar interno diretrizes sobre como implementar Personalizer, documentar e comunicá-los para sua equipe, executivos e fornecedores.
* Executar revisões periódicas de como são calculadas as pontuações de recompensa, executar avaliações offline para ver que funcionalidades estão a afetar Personalizer e utilizar os resultados para eliminar recursos desnecessários e desnecessários.
* Comunicar claramente aos seus utilizadores como Personalizer é utilizada, para que finalidade e com os dados.
* Arquivar informações e os ativos - como modelos, políticas de aprendizagem e outros dados – que Personalizer utiliza a funcionar, poder-se de que reproduza os resultados.

### <a name="transparency"></a>Transparência
*Sistemas de IA deve ser Understandable*. Com Personalizer:

* *Dê aos utilizadores obter informações sobre como o conteúdo foi personalizado.* Por exemplo, pode mostrar aos utilizadores um botão rotulado `Why These Suggestions?` que mostra os principais recursos do utilizador e ações desempenhado um papel nos resultados de Personalizer.
* Certifique-se de que seus termos de utilização marca mencionar que irá utilizar informações sobre utilizadores e seu comportamento para personalizar a experiência.

### <a name="fairness"></a>Justiça de
*Sistemas de IA deve tratar todas as pessoas bastante*.

* Não utilize Personalizer para casos de utilização em que os resultados são a longo prazo, consequentes, ou envolvem danos reais.
* Não utilize recursos que não são adequados para personalizar o conteúdo com ou que podem ajudar a se propagar tendências indesejadas. Por exemplo, qualquer pessoa com situações semelhantes de financeiras deverá ver as mesmas recomendações personalizadas de produtos financeiros.
* Compreenda as tendências que possam existir em recursos que são originados de editores, ferramentas algorítmica ou os próprios usuários.

### <a name="reliability-and-safety"></a>Confiabilidade e segurança
*Sistemas de IA deve executar com segurança e de forma fiável*. Para Personalizer:

* *Não fornecem ações para Personalizer que não deve ser escolhido*. Por exemplo, inadequadamente filmes devem ser filtrados para fora das ações para personalizar a se fazer uma recomendação para um anónima ou o utilizador de idade insuficientemente.
* *Gerir o seu modelo de Personalizer como um ativo comercial*.  Considere a frequência de guardar e criar cópias de segurança o modelo e políticas por trás de seu Loop de Personalizer de aprendizagem e, caso contrário, tratá-lo como um ativo de negócios importantes. Reproduzir últimos resultados é importante para a melhoria de personalizada de auditoria e de medição.
* *Fornecer canais para obter direto de comentários de utilizadores*. Para além de codificação verificações de segurança para garantir que apenas o público certo ver o conteúdo certo, fornecem um mecanismo de comentários dos utilizadores ao conteúdo de relatório que pode ser surpreendente ou perturbador. Especialmente se seu conteúdo é proveniente de utilizadores ou partes 3ª, considere utilizar o Content Moderator da Microsoft ou de ferramentas adicionais para revisar e validar os conteúdos.
* *Executar avaliações offline frequentes*. Isto irá ajudá-lo a monitorizar tendências e certifique-se de eficácia é conhecida.
* *Estabeleça um processo para detetar e tomar medidas relativamente à manipulação maliciosa*. Existem atores que aproveitam os benefícios do machine learning e a capacidade dos sistemas de AI para saber de seu ambiente para mudar o resultado para suas metas. Se a utilização do Personalizer está numa posição para influenciar opções importantes, lembre-se de que tem o meio adequado para detetar e mitigar essas classes de ataques, incluindo de revisão humana em circunstâncias apropriadas.

### <a name="security-and-privacy"></a>Segurança e privacidade
*Sistemas de IA deve ser seguro e respeitamos a privacidade*. Ao utilizar Personalizer:

* *Informar os utilizadores com antecedência sobre os dados que são recolhidos e como são utilizadas e obter o consentimento prévio*, seguindo os regulamentos local e da indústria.
* *Fornece proteção de privacidade controles de usuário.* Para aplicativos que armazenam informações pessoais, considere fornecer um botão de fácil de encontrar para as funções, tais como: 
   * `Show me all you know about me`    
   * `Forget my last interaction` 
   * `Delete all you know about me`

Em alguns casos, elas podem ser legalmente necessárias. Considere as compensações no reparametrizar modelos periodicamente para que não contêm rastreios de dados eliminados.

### <a name="inclusiveness"></a>Inclusiveness
*Resolver uma ampla gama de necessidades humanas e experiências*.
* *Forneça experiências personalizadas para interfaces habilitados para acessibilidade.* A eficiência que provém de personalização de boa - aplicada para reduzir a quantidade de esforço, movimento e a repetição desnecessária em interações podem ser benéfico sobretudo para pessoas portadoras de deficiência.
* *Ajustar o comportamento do aplicativo ao contexto*. Pode usar Personalizer para eliminar a ambiguidade entre objetivos num chatbot, por exemplo, como a interpretação certa pode ser contextual e o único tamanho pode não se ajusta a todos. 


## <a name="proactive-readiness-for-increased-data-protection-and-governance"></a>Preparação para o proativa para proteção de dados maior e governação

É difícil prever alterações específicas em contextos de regulamentação, mas em geral seria aconselhável ultrapassem a estrutura legal mínima em garantir que a utilização obedientes ao conjunto de dados pessoais e o fornecimento de transparência e escolha relacionadas com a tomada de decisão algorítmica.


* Considere a planejar a uma situação em que pode haver novas restrições nos dados recolhidos das pessoas, e é necessário para mostrar como ele foi usado para tomar decisões.
* Preparação extra, onde os utilizadores podem incluir marginalized populações vulneráveis, filhos, os utilizadores na vulnerabilidade económico ou utilizadores caso contrário, considere suscetível a influenciar de manipulação de algoritmo.
* Considere a insatisfação ampla com a recolha de dados como direcionamento de público-alvo e influenciar o público-alvo programas e os algoritmos de tem tocado e como evitar erros estratégicos comprovados.


## <a name="proactive-assessments-during-your-project-lifecycle"></a>Avaliações proativas durante o ciclo de vida do projeto

Considere a criação de métodos para os membros da Equipe, os utilizadores e proprietários de empresas para questões de relatório relativamente à utilização responsável e criação de um processo que atribui prioridades aos respetivos resolução e impede que retaliation.

Qualquer pessoa pensando efeitos colaterais de utilização de qualquer tecnologia é limitada pela sua experiência de ponto de vista e a vida. Expandir o intervalo de opiniões disponíveis por género, mais diversificadas vozes nas suas equipes, os utilizadores ou conselhos consultivos; de modo que é possível e recomendado-lhes a dizer. Considere a formação e aprendizagem materiais para expandir ainda mais o conhecimento da Equipe neste domínio e para adicionar capacidade de discutir tópicos complexos e confidenciais.

Considere tratando tarefas relativamente à utilização responsável outras tarefas de transversais à semelhança do ciclo de vida do aplicativo, como tarefas relacionadas ao usuário ocorrer, segurança, ou de devops. Essas tarefas e os respetivos requisitos, não podem ser uma reflexão tardia. Utilização responsável deve ser discutida e verificada em todo o ciclo de vida do aplicativo.
 
## <a name="questions-and-feedback"></a>Perguntas e comentários

Microsoft continuamente é colocar o esforço em ferramentas e documentos para o ajudar a tomar decisões sobre essas responsabilidades. A nossa equipa convida [comentários à Microsoft](mailto:cogsvcs-RL-feedback@microsoft.com?subject%3DPersonalizer%20Responsible%20Use%20Feedback&body%3D%5BPlease%20share%20any%20question%2C%20idea%20or%20concern%5D) se considerar que as ferramentas adicionais, as funcionalidades do produto, e documentos ajuda a implementar essas diretrizes para usar Personalizer.

## <a name="recommended-reading"></a>Leitura recomendada

* Consulte os princípios de seis da Microsoft para o desenvolvimento responsável de IA publicado no livro de Janeiro de 2018, [calculado do futuro](https://news.microsoft.com/futurecomputed/)
* [Quem é o proprietário o futuro? ](https://www.goodreads.com/book/show/15802693-who-owns-the-future) por Jaron Lanier.
* [Armas de destruição de matemática](https://www.goodreads.com/book/show/28186015-weapons-of-math-destruction) por - Cathy O'Neil
* [Ética e de ciência de dados](https://www.oreilly.com/library/view/ethics-and-data/9781492043898/) por DJ Patil, Hilary Mason, Mike Loukides.
* [Código ACM de Ética](https://www.acm.org/code-of-ethics)
* [Informações de genética Nondiscrimination Act - GINA](https://en.wikipedia.org/wiki/Genetic_Information_Nondiscrimination_Act)
* [Princípios FATML para algoritmos Accountable](http://www.fatml.org/resources/principles-for-accountable-algorithms)


## <a name="next-steps"></a>Passos Seguintes

[Recursos: ação e o contexto](concepts-features.md).
