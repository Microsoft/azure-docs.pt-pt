---
title: Ética e uso responsável-personalizador
titleSuffix: Azure Cognitive Services
description: Essas diretrizes destinam-se a ajudá-lo a implementar a personalização de uma forma que o ajude a criar confiança em sua empresa e serviço. Certifique-se de pausar para pesquisar, aprender e deliberadamente o impacto da personalização nas vidas das pessoas. Em caso de dúvida, busque diretrizes.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 06/12/2019
ms.author: diberry
ms.openlocfilehash: f565d95f8270612a8d83dd44a1e1bb895d1a4373
ms.sourcegitcommit: e3b0fb00b27e6d2696acf0b73c6ba05b74efcd85
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/30/2019
ms.locfileid: "68662792"
---
# <a name="guidelines-for-responsible-implementation-of-personalizer"></a>Diretrizes para implementação responsável do personalizador

Para que as pessoas e a sociedade percebam todo o potencial do ia, as implementações precisam ser projetadas de forma que obtenham a relação de confiança daqueles que adicionam ia aos seus aplicativos e os usuários de aplicativos criados com o ia. Essas diretrizes destinam-se a ajudá-lo a implementar o personalizador de uma forma que o ajude a criar confiança em sua empresa e serviço. Certifique-se de pausar para pesquisar, aprender e deliberadamente o impacto da personalização nas vidas das pessoas. Em caso de dúvida, busque diretrizes.

Essas diretrizes não se destinam a conselhos legais e você deve garantir que seu aplicativo esteja em conformidade com os desenvolvimentos rápidos da lei nessa área e em seu setor.

Além disso, ao projetar seu aplicativo usando o personalizador, você deve considerar um amplo conjunto de responsabilidades que tem ao desenvolver qualquer sistema de ia centrado em dados, incluindo ética, privacidade, segurança, segurança, inclusão, transparência e responsabilidade. Você pode ler mais sobre eles na seção de [leitura recomendada](#recommended-reading) .

Você pode usar o seguinte conteúdo como uma lista de verificação inicial e personalizá-lo e refiná-lo para seu cenário. Este documento tem duas seções principais: A primeira é dedicada a destacar as considerações de uso responsável ao escolher cenários, recursos e recompensas para o personalizador. A segunda assume um conjunto de valores que a Microsoft acredita que deve ser considerada ao criar sistemas de ia e fornece sugestões e riscos acionáveis sobre como o uso do personalizador influencia eles. 


## <a name="your-responsibility"></a>Sua responsabilidade

Todas as diretrizes para implementação responsável baseiam-se na base que os desenvolvedores e as empresas que usam o personalizador são responsáveis por se considerarem os efeitos de usar esses algoritmos na sociedade. Se você estiver desenvolvendo um aplicativo que sua organização implantará, você deve reconhecer sua função e responsabilidade por sua operação e como ela afeta as pessoas. Se você estiver projetando um aplicativo a ser implantado por terceiros, venha a compreender um entendimento compartilhado com eles que, por fim, são responsáveis pelo comportamento do aplicativo e pelo documento que o entende.

A confiança baseia-se na noção de compromissos cumpridos-considere os usuários, a sociedade e a estrutura legal em que seus aplicativos funcionam, para identificar compromissos explícitos e implícitos que eles podem ter.

A Microsoft está continuamente colocando esforços em suas ferramentas e documentos para ajudá-lo a agir nessas responsabilidades. [Forneça comentários à Microsoft](mailto:cogsvcs-RL-feedback@microsoft.com?subject%3DPersonalizer%20Responsible%20Use%20Feedback&body%3D%5BPlease%20share%20any%20question%2C%20idea%20or%20concern%5D) se você acreditar que ferramentas adicionais, recursos e documentos do produto o ajudarão a implementar essas diretrizes para usar o personalizador.


## <a name="factors-for-responsibly-implementing-personalizer"></a>Fatores para implementar o personalizador com responsabilidade

A implementação do personalizador pode ser de grande valor para seus usuários e seus negócios. Para implementar o personalizador com responsabilidade, comece considerando as seguintes diretrizes quando:

* Escolhendo casos de uso para aplicar a personalização.
* Criando [funções de recompensa](https://github.com/Azure/personalization-rl/blob/master/docs/concepts-rewards.md).
* Escolher os [recursos](https://github.com/Azure/personalization-rl/blob/master/docs/concepts-features.md) sobre o contexto e as possíveis ações que serão usadas para personalização.


## <a name="choosing-use-cases-for-personalizer"></a>Escolhendo casos de uso para personalizador

É útil usar um serviço que aprende a personalizar o conteúdo e as interfaces do usuário. Ele também pode ser aplicado inaplicadamente se a maneira como a personalização cria efeitos colaterais negativos no mundo real, incluindo se os usuários não sabem da personalização de conteúdo. 

Exemplos de uso de personalizador com potencial maior para efeitos colaterais negativos ou uma falta de transparência incluem cenários em que a "recompensa" depende de muitos fatores complexos de longo prazo que, quando excessivamente simplificado em uma recompensa imediata, pode ter a capacidade de ser desfavorável resultados para indivíduos. Eles tendem a ser considerados opções "consequenciais" ou opções que envolvem um risco de danos. Por exemplo: 


* **Finanças**: Personalizando ofertas em produtos de empréstimo, financeiros e de seguros, nos quais os fatores de risco se baseiam em dados que os indivíduos não conhecem, não conseguem obter nem fazer contestação. 
* **Educação**: Personalizar classificações para cursos escolares e instituições educacionais em que as recomendações podem propagar tendências e reduzir a conscientização dos usuários de outras opções.
* **Participação em democracia e cívico**: Personalizar o conteúdo para usuários com o objetivo de influenciar opiniões é consequencial e Manipulative.
* **Avaliação de recompensa de**terceiros: Personalizando itens em que a recompensa se baseia em uma avaliação de terceiros do usuário, em vez de ter uma recompensa gerada pelo próprio comportamento do usuário.
* **Intolerância à exploração**: Qualquer situação na qual o comportamento de exploração do personalizado possa causar danos.

Ao escolher casos de uso para personalizador:

* Inicie o processo de design considerando como a personalização ajuda os usuários.
* Considere as consequências negativas no mundo real se alguns itens não estiverem classificados para usuários devido a padrões de personalização ou exploração.
* Considere o autoatendimento de loops de Prophecy. Isso pode acontecer se um prêmio de personalização treinar um modelo para que ele possa posteriormente excluir um grupo demográfico de acessar conteúdo relevante. Por exemplo, a maioria das pessoas em um ambiente de baixa renda não obtém uma oferta de seguro Premium, e ninguém mais lento na vizinhança tende a ver a oferta.
* Salve cópias de modelos e políticas de aprendizado caso seja necessário reproduzir o personalizador no futuro. Você pode fazer isso periodicamente ou em cada período de atualização do modelo.
* Considere o nível de exploração adequado para o espaço e como usá-lo como uma ferramenta para atenuar os efeitos de "eco câmara".


## <a name="selecting-features-for-personalizer"></a>Selecionando recursos do personalizador

A personalização de conteúdo depende de ter informações úteis sobre o conteúdo e o usuário. Tenha em mente, para alguns aplicativos e indústrias, alguns recursos do usuário podem ser considerados discriminados direta ou indiretamente e potencialmente ilegais.

Considere o efeito desses recursos:

* Dados demográficos do **usuário**: Recursos relacionados a sexo, gênero, idade, corrida, Religion: Esses recursos podem não ser permitidos em determinados aplicativos por motivos regulatórios, e talvez não seja ético personalizá-los, pois a personalização propagará generalizações e tendência. Um exemplo dessa propagação de tendência é uma postagem de trabalho para a engenharia não ser mostrada para idosa ou para os públicos baseados em gênero.
* **Informações de localidade**: Em muitos lugares do mundo, as informações de local (como um CEP, código postal ou nome de vizinhança) podem estar altamente correlacionadas com renda, corrida e Religion.
* **Percepção de imparcialidade do usuário**: Mesmo nos casos em que seu aplicativo está tomando decisões sonoras, considere o efeito dos usuários que percebem que o conteúdo exibido em seu aplicativo é alterado de uma maneira que pareça estar correlacionado a recursos que seriam discriminados.
* **Tendência indesejada em recursos**:  Há tipos de tendências que podem ser introduzidas usando recursos que afetam apenas um subconjunto da população. Isso requer atenção extra se os recursos estiverem sendo gerados forma algorítmica, como ao usar a análise de imagem para extrair itens em uma imagem ou análise de texto para descobrir entidades em texto. Fique atento às características dos serviços que você usa para criar esses recursos.

Aplique as seguintes práticas ao escolher os recursos a serem enviados em contextos e ações para o personalizador:

* Considere a legalidade e a ética do uso de determinados recursos para alguns aplicativos e se os recursos de aparência inocente podem ser proxies para outras pessoas que você deseja ou que devem evitar,
* Seja transparente para os usuários que os algoritmos e a análise de dados estão sendo usados para personalizar as opções que eles veem.
* Pergunte-se: Meus usuários me preocupariam e ficariam satisfeitos se eu usava essas informações para personalizar o conteúdo para eles? Gostaria de me sentir confortável mostrando como a decisão foi feita para realçar ou ocultar determinados itens?
* Use comportamental em vez de dados de classificação ou segmentação com base em outras características. As informações demográficas eram tradicionalmente usadas por varejistas por razões históricas – os atributos demográficos pareciam simples de coletar e agir antes de uma era digital, mas questionar como informações demográficas relevantes são quando você tem interação real, dados contextuais e históricos que se relacionam mais bem às preferências e à identidade dos usuários.
* Considere como impedir que os recursos sejam "falsificados" por usuários mal-intencionados, que, se explorados em números grandes, podem levar ao personalizador de treinamento de formas enganosas de interrupções, constrangimento e assédio de determinadas classes de usuários. 
* Quando apropriado e viável, projete seu aplicativo para permitir que os usuários aceitem ou recusem a existência de determinados recursos pessoais usados. Eles podem ser agrupados, como "informações de local", "informações do dispositivo", "histórico de compra passada", etc.


## <a name="computing-rewards-for-personalizer"></a>Compensações de computação para personalizador

O personalizado se esforça para melhorar a escolha de qual ação deve ser recompensada com base na pontuação de recompensa fornecida pela lógica de negócios do aplicativo.

Uma pontuação de recompensa bem criada funcionará como um proxy de curto prazo para uma meta de negócios, que está vinculada à missão de uma organização.

Por exemplo, recompensa em cliques fará com que o serviço personalizado busque cliques com a despesa de todo o resto, mesmo que o que é clicado esteja distraindo ou não esteja vinculado a um resultado comercial.

Como exemplo de contraste, um site de notícias pode querer definir recompensas ligadas a algo mais significativo do que cliques, como "o usuário gastou tempo suficiente para ler o conteúdo?" "Eles clicaram em artigos ou referências relevantes?". Com o personalizador, é fácil reunir métricas de acordo com as recompensas. Mas tenha cuidado para não confoundr o envolvimento do usuário de curto prazo com bons resultados.

### <a name="unintended-consequences-from-reward-scores"></a>Consequências indesejadas de pontuações de recompensa
As pontuações de recompensa podem ser criadas com o melhor de intenções, mas ainda podem criar consequências inesperadas ou resultados indesejados sobre como o personalizador classifica o conteúdo. 

Considere os seguintes exemplos:

* A personalização de conteúdo de vídeo recompensa no percentual do tamanho do vídeo observado provavelmente tenderá a classificar vídeos mais curtos.
* Comparando compartilhamentos de mídia social, sem análise de sentimentos de como ele é compartilhado ou o próprio conteúdo, pode levar à classificação de conteúdo ofensivo, não moderado ou inflammatory, que tende a incitemr muito "envolvimento", mas adiciona pouco valor.
* Recompensar a ação nos elementos da interface do usuário que os usuários não esperam alterar podem interferir na usabilidade e na previsibilidade da interface do usuário, em que os botões são surpreendentemente alterando o local ou a finalidade sem aviso, o que dificulta para certos grupos de usuários para permanecerem produtivos.

Implemente estas práticas recomendadas:

* Execute experimentos offline com seu sistema usando abordagens de recompensa diferentes para entender o impacto e os efeitos colaterais.
* Avalie suas funções de recompensa e pergunte-se como uma pessoa extremamente ingênua curvaria sua interpretação e alcançou resultados indesejáveis com ela.


## <a name="responsible-design-considerations"></a>Considerações de design responsável

A seguir estão áreas de design para implementações responsáveis do ia. Saiba mais está confinado no essa estrutura no [futuro](https://news.microsoft.com/futurecomputed/)computada.

![Valores de ia do futuro computados](media/ethics-and-responsible-use/ai-values-future-computed.png)

### <a name="accountability"></a>Responsabilidade
*As pessoas que criam e implantam sistemas de ia devem ser responsáveis por como seus sistemas operam*. 

* Crie diretrizes internas sobre como implementar personalizador, documentar e comunicar-os com sua equipe, executivos e fornecedores.
* Executar revisões periódicas de como as pontuações de recompensa são computadas, executar avaliações offline para ver quais recursos estão afetando o personalizador e usar os resultados para eliminar recursos desnecessários e desnecessários.
* Comunique-se claramente aos seus usuários como o personalizador é usado, para que finalidade e com quais dados.
* Arquive informações e ativos-como modelos, políticas de aprendizado e outros dados – que o personalizador usa para funcionar, para poder reproduzir os resultados.

### <a name="transparency"></a>Transparência
Os *sistemas de ia devem ser compreensíveis*. Com o personalizador:

* *Forneça aos usuários informações sobre como o conteúdo foi personalizado.* Por exemplo, você pode mostrar aos usuários um botão rotulado `Why These Suggestions?` mostrando quais recursos principais do usuário e as ações desempenharam uma função nos resultados do personalizador.
* Verifique se os termos de uso fazem menção de que você usará informações sobre os usuários e seu comportamento para personalizar a experiência.

### <a name="fairness"></a>Equidade
*Os sistemas de ia devem tratar todas as pessoas*de forma justa.

* Não use o personalizador para casos de uso em que os resultados sejam de longo prazo, CONSEQÜENCIAIS ou envolvam danos reais.
* Não use recursos que não sejam apropriados para personalizar o conteúdo com o ou que possam ajudar a propagar tendências indesejadas. Por exemplo, qualquer pessoa com circunstâncias financeiras semelhantes deve ver as mesmas recomendações personalizadas para produtos financeiros.
* Entenda as tendências que podem existir em recursos que são originados de editores, ferramentas de algoritmos ou próprios usuários.

### <a name="reliability-and-safety"></a>Confiabilidade e segurança
Os *sistemas de ia devem ser executados de forma confiável e segura*. Para personalizador:

* *Não forneça ações ao personalizador que não devem ser escolhidas*. Por exemplo, filmes inadequadas devem ser filtrados das ações a serem personalizadas se fizerem uma recomendação para um usuário anônimo ou sob idade.
* *Gerencie seu modelo personalizado como um ativo de negócios*.  Considere a frequência de salvar e fazer backup do modelo e das políticas de aprendizado por trás do loop personalizador e, caso contrário, tratá-lo como um ativo comercial importante. A reprodução de resultados anteriores é importante para a melhoria de autoauditoria e medição.
* *Forneça canais para obter comentários diretos dos usuários*. Além das verificações de segurança de codificação para garantir que apenas os públicos-alvo corretos vejam o conteúdo certo, forneça um mecanismo de comentários para que os usuários relatem conteúdo que pode ser surpreendente ou perturbador. Especialmente se seu conteúdo vier de usuários ou de terceiros, considere usar o Microsoft Content Moderator ou ferramentas adicionais para revisar e validar o conteúdo.
* *Execute avaliações offline frequentes*. Isso o ajudará a monitorar tendências e garantir que a eficácia seja conhecida.
* *Estabeleça um processo para detectar e agir sobre manipulação mal-intencionada*. Há atores que aproveitarão o aprendizado de máquina e a capacidade dos sistemas de ia de aprender com seu ambiente para deslocar o resultado para suas metas. Se o uso do personalizador estiver em uma posição para influenciar as opções importantes, certifique-se de ter meios apropriados para detectar e atenuar essas classes de ataques, incluindo a análise humana nas circunstâncias apropriadas.

### <a name="security-and-privacy"></a>Segurança e privacidade
Os *sistemas de ia devem ser seguros e respeitar a privacidade*. Ao usar o personalizador:

* *Informe os usuários sobre os dados coletados e como eles são usados e obtenha seu consentimento com antecedência*, seguindo suas normas locais e do setor.
* *Fornecer privacidade – proteger controles de usuário.* Para aplicativos que armazenam informações pessoais, considere o fornecimento de um botão fácil de encontrar para funções como: 
   * `Show me all you know about me`    
   * `Forget my last interaction` 
   * `Delete all you know about me`

Em alguns casos, eles podem ser legalmente obrigatórios. Considere as compensações em modelos de readaptação periodicamente para que eles não contenham rastreamentos de dados excluídos.

### <a name="inclusiveness"></a>Inclusividade
*Atenda a uma ampla gama de necessidades e experiências humanas*.
* *Fornecer experiências personalizadas para interfaces habilitadas para acessibilidade.* A eficiência que vem de uma boa personalização – aplicada para reduzir a quantidade de esforço, movimento e repetição desnecessária em interações – pode ser especialmente benéfica para pessoas com deficiências.
* *Ajuste o comportamento do aplicativo para o contexto*. Você pode usar o personalizador para fazer a desambiguidade entre as tentativas em um bot de chat, por exemplo, como a interpretação correta pode ser contextual e um tamanho pode não se ajustar a todos. 


## <a name="proactive-readiness-for-increased-data-protection-and-governance"></a>Prontidão proativa para maior proteção e governança de dados

É difícil prever alterações específicas em contextos regulatórios, mas, em geral, seria prudente ir além da estrutura legal mínima para garantir o uso obedientes de dados pessoais e fornecer transparência e opções relacionadas à tomada de decisões do Algorithm.


* Considere o planejamento antecipadamente para uma situação em que pode haver novas restrições nos dados coletados de indivíduos, e há a necessidade de mostrar como ele foi usado para tomar decisões.
* Considere a preparação extra onde os usuários podem incluir populações vulneráveis marginalmente, filhos, usuários em vulnerabilidade econômica ou usuários que, de outra forma, suscetíveis a influenciar da manipulação de algoritmos.
* Considere a insatisfação generalizada com a forma como o público-alvo e o público-influenciando os programas e algoritmos de coleta de dados foram reproduzidos e como evitar erros estratégicos comprovados.


## <a name="proactive-assessments-during-your-project-lifecycle"></a>Avaliações proativas durante o ciclo de vida do projeto

Considere a criação de métodos para membros da equipe, usuários e proprietários de negócios para relatar preocupações sobre o uso responsável e criar um processo que priorize sua resolução e impeça a retaliação.

Qualquer pessoa que pensa em efeitos colaterais de uso de qualquer tecnologia é limitada por sua perspectiva e experiência de vida. Expanda a variedade de opiniões disponíveis, trazendo vozes mais diversificadas para suas equipes, usuários ou conselhos. Isso é possível e incentivado a conversar. Considere os materiais de treinamento e aprendizagem para expandir ainda mais o conhecimento da equipe nesse domínio e para adicionar recursos para discutir tópicos complexos e confidenciais.

Considere tratar tarefas relacionadas ao uso responsável, assim como outras tarefas de transversais no ciclo de vida do aplicativo, como tarefas relacionadas à experiência do usuário, segurança ou DevOps. Essas tarefas e seus requisitos não podem ser uma prioridade. O uso responsável deve ser discutido e verificado em todo o ciclo de vida do aplicativo.
 
## <a name="questions-and-feedback"></a>Perguntas e comentários

A Microsoft está continuamente colocando esforços em ferramentas e documentos para ajudá-lo a agir nessas responsabilidades. Nossa equipe o convida a [fornecer comentários à Microsoft](mailto:cogsvcs-RL-feedback@microsoft.com?subject%3DPersonalizer%20Responsible%20Use%20Feedback&body%3D%5BPlease%20share%20any%20question%2C%20idea%20or%20concern%5D) se você acredita que ferramentas adicionais, recursos do produto e documentos o ajudarão a implementar essas diretrizes para usar o personalizador.

## <a name="recommended-reading"></a>Leitura recomendada

* Veja os seis princípios da Microsoft para o desenvolvimento responsável do ia publicado no livro de janeiro de 2018, [o futuro calculado](https://news.microsoft.com/futurecomputed/)
* [Quem é o proprietário do futuro?](https://www.goodreads.com/book/show/15802693-who-owns-the-future) por Jaron Lanier.
* [Armas de destruição matemática](https://www.goodreads.com/book/show/28186015-weapons-of-math-destruction) por-Cathy O'Neil
* [Ética e ciência de dados](https://www.oreilly.com/library/view/ethics-and-data/9781492043898/) por DJ Patil, Hilary Mason, Mike Loukides.
* [Código do ACM de ética](https://www.acm.org/code-of-ethics)
* [Lei de não discriminação de informações genética-GINA](https://en.wikipedia.org/wiki/Genetic_Information_Nondiscrimination_Act)
* [Princípios de FATML para algoritmos de conta](https://www.fatml.org/resources/principles-for-accountable-algorithms)


## <a name="next-steps"></a>Passos Seguintes

[Recursos: ação e contexto](concepts-features.md).
