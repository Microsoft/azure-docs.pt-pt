---
title: Gravar amostras de voz personalizadas - Serviço de fala
titleSuffix: Azure Cognitive Services
description: Faça uma voz personalizada de qualidade de produção preparando um roteiro robusto, contratando bons talentos de voz e gravando profissionalmente.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/13/2020
ms.author: erhopf
ms.openlocfilehash: 2897fe2e0cc8aeb929b0a33f5cdaba9c60f2a244
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "81261589"
---
# <a name="record-voice-samples-to-create-a-custom-voice"></a>Gravar amostras de voz para criar uma voz personalizada

Criar uma voz personalizada de produção de alta qualidade do zero não é um compromisso casual. O componente central de uma voz personalizada é uma grande coleção de amostras de áudio da fala humana. É vital que estas gravações áudio sejam de alta qualidade. Escolha um talento de voz que tenha experiência em fazer este tipo de gravações, e que as grave por um engenheiro de gravação competente usando equipamento profissional.

Antes de poder fazer estas gravações, precisa de um guião: as palavras que serão ditas pelo seu talento de voz para criar as amostras de áudio. Para obter os melhores resultados, o seu script deve ter uma boa cobertura fonética e variedade suficiente para treinar o modelo de voz personalizado.

Muitos pequenos mas importantes detalhes vão para a criação de uma gravação de voz profissional. Este guia é um roteiro para um processo que o ajudará a obter resultados bons e consistentes.

> [!TIP]
> Para obter os resultados da mais alta qualidade, considere envolver a Microsoft para ajudar a desenvolver a sua voz personalizada. A Microsoft tem uma vasta experiência em produzir vozes de alta qualidade para os seus próprios produtos, incluindo cortana e Office.

## <a name="voice-recording-roles"></a>Papéis de gravação de voz

Existem quatro funções básicas num projeto de gravação de voz personalizado:

Função|Objetivo
-|-
Talento de voz        |A voz desta pessoa formará a base da voz personalizada.
Engenheiro de gravação  |Supervisiona os aspetos técnicos da gravação e opera o aparelho de gravação.
Diretor            |Prepara o guião e treina o desempenho do talento de voz.
Editor              |Finaliza os ficheiros áudio e prepara-os para o upload para o portal Custom Voice.

Um indivíduo pode preencher mais do que um papel. Este guia assume que irá principalmente preencher o papel de diretor e contratar um talento de voz e um engenheiro de gravação. Se você quiser fazer as gravações por si mesmo, este artigo inclui algumas informações sobre o papel de engenheiro de gravação. O papel de editor só é necessário depois da sessão, pelo que pode ser desempenhado pelo realizador ou pelo engenheiro de gravação.

## <a name="choose-your-voice-talent"></a>Escolha o seu talento de voz

Os atores com experiência em voiceover ou trabalho de personagem de voz fazem um bom talento de voz personalizado. Você também pode frequentemente encontrar talento adequado entre locutores e jornalistas.

Escolha o talento da voz cuja voz natural gosta. É possível criar vozes únicas de "personagem", mas é muito mais difícil para a maioria dos talentos executá-las de forma consistente, e o esforço pode causar tensão de voz.

> [!TIP]
> Geralmente, evite usar vozes reconhecíveis para criar uma voz personalizada — a menos, claro, que o seu objetivo seja produzir uma voz de celebridades. As vozes menos conhecidas são geralmente menos distraídas para os utilizadores.

O fator mais importante para escolher o talento de voz é a consistência. As tuas gravações devem soar como se fossem feitas no mesmo dia na mesma sala. Pode abordar este ideal através de boas práticas de gravação e engenharia.

O teu talento de voz é a outra metade da equação. Devem poder falar com taxa consistente, nível de volume, tom e tom. Dicção clara é uma obrigação. O talento também precisa ser capaz de controlar estritamente a sua variação de tom, o efeito emocional e os maneirismos da fala.

Gravar amostras de voz personalizadas pode ser mais fatiguante do que outros tipos de trabalho de voz. A maioria dos talentos de voz pode gravar por duas ou três horas por dia. Limite as sessões para três ou quatro por semana, com um dia de folga no meio, se possível.

As gravações feitas para um modelo de voz devem ser emocionalmente neutras. Ou seja, uma expressão triste não deve ser lida de uma forma triste. O humor pode ser adicionado ao discurso sintetizado mais tarde através de controlos de próteses. Trabalhe com o seu talento de voz para desenvolver uma "persona" que defina o som geral e o tom emocional da voz personalizada. No processo, vais identificar o que parece ser "neutro" para essa persona.

Uma personalidade pode ter, por exemplo, uma personalidade naturalmente otimista. Assim, a voz "deles" pode levar uma nota de otimismo mesmo quando falam neutramente. No entanto, tal traço de personalidade deve ser subtil e consistente. Ouça as leituras das vozes existentes para ter uma ideia do que pretende.

> [!TIP]
> Normalmente, vais querer ser dono das gravações de voz que fazes. O seu talento de voz deve ser favorável a um contrato de trabalho por contratação para o projeto.

## <a name="create-a-script"></a>Criar um script

O ponto de partida de qualquer sessão de gravação de voz personalizada é o guião, que contém as expressões a serem ditas pelo seu talento de voz. (O termo "expressões" abrange frases completas e frases mais curtas.)

As declarações no seu guião podem vir de qualquer lugar: ficção, não-ficção, transcrições de discursos, notícias e qualquer outra coisa disponível em formato impresso. Se quiser certificar-se de que a sua voz se sai bem em tipos específicos de palavras (como terminologia médica ou jargão de programação), talvez queira incluir frases de documentos eruditos ou documentos técnicos. Para uma breve discussão sobre potenciais questões legais, consulte a secção ["Legalidades".](#legalities) Também pode escrever o seu próprio texto.

As suas declarações não precisam de vir da mesma fonte, ou do mesmo tipo de fonte. Nem precisam de ter nada a ver um com o outro. No entanto, se utilizar frases definidas (por exemplo, "Você registou com sucesso") na sua aplicação de discurso, certifique-se de incluí-las no seu script. Isto dará à sua voz personalizada uma melhor chance de pronunciar bem essas frases. E se decidirusar uma gravação em vez de discurso sintetizado, já a terá na mesma voz.

Embora a consistência seja fundamental na escolha do talento de voz, a variedade é a marca de um bom guião. O seu guião deve incluir muitas palavras e frases diferentes com uma variedade de comprimentos de frase, estruturas e humores. Todos os sons da língua devem ser representados várias vezes e em inúmeros contextos (chamado *cobertura fonética).*

Além disso, o texto deve incorporar todas as formas de que um determinado som possa ser representado por escrito e colocar cada som em diferentes lugares das frases. Ambas as frases e perguntas declarativas devem ser incluídas e lidas com entoação adequada.

É difícil escrever um script que fornece dados *suficientes* para permitir que o portal de Discurso Personalizado construa uma boa voz. Na prática, a forma mais simples de fazer um guião que obtenha uma cobertura fonética robusta é incluir um grande número de amostras. As vozes padrão que a Microsoft fornece foram construídas a partir de dezenas de milhares de expressões. Deve estar preparado para gravar algumas a várias mil expressões no mínimo para construir uma voz personalizada de qualidade de produção.

Verifique cuidadosamente o guião para verificar se há erros. Se possível, mande alguém verificar também. Quando analisares o guião com o teu talento, provavelmente vais apanhar mais alguns erros.

### <a name="script-format"></a>Formato script

Pode escrever o seu guião no Microsoft Word. O script é para ser usado durante a sessão de gravação, para que possa configurar de qualquer forma com que achar fácil de trabalhar. Crie o ficheiro de texto que é exigido pelo portal Voz Personalizada separadamente.

Um formato de script básico contém três colunas:

* O número da expressão, a partir de 1. A numeração torna mais fácil para todos no estúdio referirem-se a uma expressão particular ("vamos tentar o número 356 novamente"). Pode utilizar a função de numeração do parágrafo Word para numerar automaticamente as linhas da tabela.
* Uma coluna em branco onde escreverá o número de admissão ou o código de tempo de cada expressão para ajudá-lo a encontrá-lo na gravação final.
* O texto da própria expressão.

![Script de exemplo](media/custom-voice/script.png)

> [!NOTE]
> A maioria dos estúdios grava em segmentos curtos conhecidos como *takes*. Cada tomada normalmente contém 10 a 24 expressões. Apenas notando o número de tomada é suficiente para encontrar uma expressão mais tarde. Se estiver a gravar num estúdio que prefira fazer gravações mais longas, vai querer registar o código de tempo. O estúdio terá uma exibição de tempo proeminente.

Deixe espaço suficiente depois de cada linha para escrever notas. Certifique-se de que nenhuma expressão é dividida entre páginas. Numera as páginas e imprime o teu guião num dos lados do papel.

Imprima três cópias do guião: uma para o talento, uma para o engenheiro, e outra para o realizador (você). Use um clip e não agrafos em vez de agrafos: um artista de voz experiente separará as páginas para evitar fazer barulho à medida que as páginas são viradas.

### <a name="legalities"></a>Legalidades

De acordo com a lei de direitos autorais, a leitura de um ator de texto protegido por direitos autorais pode ser uma performance para a qual o autor da obra deve ser compensado. Esta performance não será reconhecível no produto final, a voz personalizada. Ainda assim, a legalidade da utilização de um trabalho protegido por direitos de autor para o efeito não está bem estabelecida. A Microsoft não pode prestar aconselhamento jurídico sobre este assunto; consulte o seu próprio conselho.

Felizmente, é possível evitar totalmente estas questões. Existem muitas fontes de texto que pode usar sem permissão ou licença.

|Fonte de texto|Descrição|
|-|-|
|[CMU corpo ártico](http://festvox.org/cmu_arctic/)|Cerca de 1100 frases selecionadas de trabalhos fora dos direitos de autor especificamente para uso em projetos de síntese de fala. Um excelente ponto de partida.|
|Já não funciona<br>sob direitos de autor|Normalmente, obras publicadas antes de 1923. Para inglês, o [Projeto Gutenberg](https://www.gutenberg.org/) oferece dezenas de milhares de trabalhos deste tipo. Você pode querer focar-se em obras mais recentes, já que a língua será mais próxima do inglês moderno.|
|Obras&nbsp;do governo|As obras criadas pelo governo dos Estados Unidos não são de direitos de autor nos Estados Unidos, embora o governo possa reivindicar direitos de autor noutros países/regiões.|
|Domínio público|Obras para as quais os direitos de autor foram explicitamente desreclamados ou que foram dedicados ao domínio público. Pode não ser possível renunciar inteiramente aos direitos de autor em algumas jurisdições.|
|Obras permissivamente licenciadas|Obras distribuídas sob uma licença como Creative Commons ou a Licença de Documentação Gratuita GNU (GFDL). A Wikipedia usa o GFDL. Algumas licenças, no entanto, podem impor restrições ao desempenho do conteúdo licenciado que possam afetar a criação de um modelo de voz personalizado, por isso leia a licença atentamente.|

## <a name="recording-your-script"></a>Gravando o seu guião

Grave o seu guião num estúdio profissional de gravação especializado em trabalho de voz. Eles terão uma cabine de gravação, o equipamento certo, e as pessoas certas para o operar. Vale a pena não diminuir na gravação.

Discuta o seu projeto com o engenheiro de gravação do estúdio e ouça os seus conselhos. A gravação deve ter pouca ou nenhuma compressão dinâmica de alcance (máximo de 4:1). É fundamental que o áudio tenha um volume consistente e uma elevada relação sinal-ruído, estando livre de sons indesejados.

### <a name="do-it-yourself"></a>Faça-o por si mesmo.

Se queres fazer a gravação sozinho, em vez de ires para um estúdio de gravação, aqui está um pequeno primer. Graças à ascensão da gravação e do podcasting caseiros, é mais fácil do que nunca encontrar bons conselhos e recursos de gravação online.

A sua "cabine de gravação" deve ser uma sala pequena sem eco ou "tom de sala". Deve ser o mais silencioso e à prova de som possível. Cortinas nas paredes podem ser usadas para reduzir o eco e neutralizar ou "ensurdecedor" o som da sala.

Utilize um microfone condensador de estúdio de alta qualidade ("mic" para abreviar) destinado a gravar voz. Sennheiser, AKG e até os mais recentes microfones zoom podem produzir bons resultados. Você pode comprar um microfone, ou alugar um de uma empresa local de aluguer audio-visual. Procure um com interface USB. Este tipo de microfone combina convenientemente o elemento do microfone, o pré-amplificador e o conversor analógico-a-digital numa só embalagem, simplificando a ligação.

Também pode utilizar um microfone analógico. Muitas casas alugadas oferecem microfones "vintage" conhecidos pelo seu caráter de voz. Note que a engrenagem analógica profissional utiliza conectores XLR equilibrados, em vez da ficha de 1/4 polegadas que é usada em equipamentos de consumo. Se for analógico, também necessitará de um pré-amplificador e uma interface áudio de computador com estes conectores.

Instale o microfone num suporte ou boom e instale um filtro pop em frente ao microfone para eliminar o ruído de consoantes "plosivos" como "p" e "b". Alguns microfones vêm com um suporte de suspensão que os isola das vibrações no suporte, o que é útil.

O talento de voz deve manter-se a uma distância consistente do microfone. Use fita adesiva no chão para marcar onde devem estar. Se o talento preferir sentar-se, tenha especial cuidado para monitorizar a distância do microfone e evitar o ruído da cadeira.

Use um suporte para segurar o guião. Evite inclinar o suporte para que possa refletir o som em direção ao microfone.

A pessoa que opera o equipamento de gravação — o engenheiro — deve estar numa sala separada do talento, com alguma forma de falar com o talento na cabine de gravação (circuito *talkback).*

A gravação deve conter o mínimo de ruído possível, com o objetivo de uma relação sinal-ruído de 80 db ou melhor.

Ouça atentamente uma gravação de silêncio na sua "cabine", descubra de onde vem qualquer ruído e elimine a causa. Fontes comuns de ruído são aberturas de ar, balastros de luz fluorescente, tráfego em estradas próximas, e ventiladores de equipamentos (até computadores de caderno podem ter ventiladores). Os microfones e os cabos podem captar ruídoeléctrico a partir de fios CA próximos, normalmente um zumbido ou zumbido. Um zumbido também pode ser causado por um laço de *terra*, que é causado por ter equipamentos ligados a mais de um circuito elétrico.

> [!TIP]
> Em alguns casos, poderá utilizar um equalizador ou um plug-in de software de redução de ruído para ajudar a remover o ruído das suas gravações, embora seja sempre melhor detê-lo na sua origem.

Detete níveis para que a maior parte da gama dinâmica disponível de gravação digital seja utilizada sem sobreconduzir. Isso significa que coloque o áudio em voz alta, mas não tão alto que se distorça. Um exemplo da forma de onda de uma boa gravação é mostrado na seguinte imagem:

![Uma boa forma de onda de gravação](media/custom-voice/good-recording.png)

Aqui, a maior parte da gama (altura) é usada, mas os picos mais altos do sinal não chegam ao topo ou ao fundo da janela. Pode também ver que o silêncio na gravação se aproxima de uma fina linha horizontal, indicando um piso de ruído baixo. Esta gravação tem uma relação dinâmica aceitável e a relação sinal-ruído.

Grave diretamente no computador através de uma interface áudio de alta qualidade ou de uma porta USB, dependendo do microfone que está a usar. Para analógico, mantenha a cadeia de áudio simples: microfone, pré-amplificador, interface áudio, computador. Você pode licenciar tanto [a Avid Pro Tools](https://www.avid.com/en/pro-tools) como a [Adobe Audition](https://www.adobe.com/products/audition.html) mensalmente a um custo razoável. Se o seu orçamento for extremamente apertado, experimente a [Audácia](https://www.audacityteam.org/)gratuita.

Recorde a 44,1 kHz 16 bit monophonic (qualidade de CD) ou melhor. O estado atual da arte é de 48 kHz 24 bits, se o seu equipamento o suportar. Irá reduzir o seu áudio para 16 kHz 16 bits antes de o submeter ao portal Custom Voice. Ainda assim, compensa ter uma gravação original de alta qualidade nas edições do evento.

Idealmente, ter pessoas diferentes servem nos papéis de diretor, engenheiro e talento. Não tente fazer tudo sozinho. Numa pitada, uma pessoa pode ser o realizador e o engenheiro.

### <a name="before-the-session"></a>Antes da sessão

Para evitar perder tempo no estúdio, percorra o guião com o seu talento de voz antes da sessão de gravação. Enquanto o talento de voz se familiariza com o texto, eles podem esclarecer a pronúncia de quaisquer palavras desconhecidas.

> [!NOTE]
> A maioria dos estúdios de gravação oferecem exibição electrónica de scripts na cabine de gravação. Neste caso, digite as suas notas de verificação diretamente no documento do script. No entanto, ainda vai querer uma cópia em papel para tomar notas durante a sessão. A maioria dos engenheiros também vai querer uma cópia em papel. E ainda vai querer uma terceira cópia impressa como cópia para o talento, caso o computador esteja avariado.

O seu talento de voz pode perguntar qual a palavra que quer enfatizada numa expressão (a "palavra operativa"). Diga-lhes que quer uma leitura natural sem particular ênfase. A ênfase pode ser adicionada quando a fala é sintetizada; não deve fazer parte da gravação original.

Direcione o talento para pronunciar as palavras de forma distinta. Cada palavra do guião deve ser pronunciada como escrita. Os sons não devem ser omitidos ou insultados, como é comum no discurso casual, *a menos que tenham sido escritos dessa forma no guião.*

|Texto escrito|Pronúncia casual indesejada|
|-|-|
|nunca vai dar-lhe|nunca vai dar-lhe|
|há quatro luzes|há quatro luzes|
|como está o tempo hoje|como está o tempo hoje|
|dizer olá para o meu amiguinho|dizer olá para o meu amigo lil '|

O talento *não* deve adicionar pausas distintas entre as palavras. A sentença ainda deve fluir naturalmente, mesmo soando um pouco formal. Esta boa distinção pode ser prática para acertar.

### <a name="the-recording-session"></a>A sessão de gravação

Crie uma gravação de referência, ou ficheiro de *correspondência,* de uma expressão típica no início da sessão. Peça ao talento para repetir esta linha em cada página ou assim. Cada vez, compare a nova gravação com a referência. Esta prática ajuda o talento a manter-se consistente em volume, tempo, pitch e entoação. Entretanto, o engenheiro pode usar o ficheiro de correspondência como referência para níveis e consistência geral do som.

O ficheiro de correspondência é especialmente importante quando retoma a gravação após uma pausa ou noutro dia. Vaiquerer jogá-lo algumas vezes para o talento e fazê-los repeti-lo cada vez até que estejam bem combinados.

Treine o seu talento para respirar fundo e fazer uma pausa por um momento antes de cada expressão. Grave um par de segundos de silêncio entre as proclamações. As palavras devem ser pronunciadas da mesma forma sempre que aparecem, considerando o contexto. Por exemplo, "gravar" como verbo é pronunciado de forma diferente de "gravar" como substantivo.

Grave cinco segundos de silêncio antes da primeira gravação para capturar o "tom de quarto". Esta prática ajuda o portal Custom Voice a compensar qualquer ruído remanescente nas gravações.

> [!TIP]
> Tudo o que precisas de capturar é o talento de voz, para que possas fazer uma gravação monofónica (canal único) apenas das suas linhas. No entanto, se gravar em estéreo, pode usar o segundo canal para gravar a conversa na sala de controlo para capturar a discussão de linhas ou tomadas específicas. Remova esta faixa da versão que é enviada para o portal Custom Voice.

Ouve atentamente, usando auscultadores, para o desempenho do talento de voz. Estás à procura de uma dicção boa, mas natural, pronúncia correta, e uma falta de sons indesejados. Não hesite em pedir ao seu talento para voltar a gravar uma expressão que não cumpra estes padrões.

> [!TIP]
> Se estiver a usar um grande número de expressões, uma única expressão pode não ter um efeito percetível na voz personalizada resultante. Pode ser mais oportuno simplesmente notar quaisquer declarações com problemas, excluí-las do seu conjunto de dados, e ver como a sua voz personalizada se sai. Podes sempre voltar ao estúdio e gravar as amostras perdidas mais tarde.

Note o número de admissão ou o código de tempo no seu script para cada expressão. Peça ao engenheiro que marque cada expressão nos metadados ou folha de deixa da gravação também.

Faça pausas regulares e forneça uma bebida para ajudar o seu talento de voz a manter a voz em boa forma.

### <a name="after-the-session"></a>Depois da sessão

Estúdios de gravação modernos funcionam em computadores. No final da sessão, recebe um ou mais ficheiros áudio, não uma fita. Estes ficheiros serão provavelmente formato WAV ou AIFF em qualidade de CD (44,1 kHz 16-bit) ou melhor. 48 kHz 24 bits é comum e desejável. Geralmente, não são necessárias taxas de amostragem mais elevadas, como 96 kHz.

O portal Custom Voice requer que cada expressão fornecida esteja no seu próprio ficheiro. Cada ficheiro áudio entregue pelo estúdio contém várias expressões. Assim, a principal tarefa pós-produção é dividir as gravações e prepará-las para submissão. O engenheiro de gravação pode ter colocado marcadores no ficheiro (ou fornecido uma folha de deixa separada) para indicar onde cada expressão começa.

Use as suas notas para encontrar as demoras exatas que deseja e, em seguida, use um utilitário de edição de som, como [Avid Pro Tools](https://www.avid.com/en/pro-tools), [Adobe Audition,](https://www.adobe.com/products/audition.html)ou a [Audácia](https://www.audacityteam.org/)gratuita, para copiar cada expressão num novo ficheiro.

Deixe apenas cerca de 0,2 segundos de silêncio no início e no fim de cada clipe, exceto no primeiro. O ficheiro deve começar com cinco segundos de silêncio. Não utilize um editor de áudio para "zero out" partes silenciosas do ficheiro. Incluindo o "tom de quarto" ajudará os algoritmos de Voz Personalizada a compensar qualquer ruído de fundo residual.

Ouça minuciosamente cada ficheiro. Nesta fase, você pode editar pequenos sons indesejados que você perdeu durante a gravação, como um ligeiro tapa no lábio antes de uma linha, mas tenha cuidado para não remover qualquer discurso real. Se não conseguir corrigir um ficheiro, remova-o do seu conjunto de dados e note que o fez.

Converta cada ficheiro em 16 bits e uma taxa de amostra de 16 kHz antes de poupar e, se gravar a conversa do estúdio, remova o segundo canal. Guarde cada ficheiro em formato WAV, nomeando os ficheiros com o número de expressão do seu script.

Por fim, crie a *transcrição* que associa cada ficheiro WAV a uma versão de texto da expressão correspondente. [A criação](how-to-customize-voice-font.md) de fontes de voz personalizadas inclui detalhes do formato necessário. Pode copiar o texto diretamente do seu guião. Em seguida, crie um ficheiro Zip dos ficheiros WAV e a transcrição do texto.

Arquiva as gravações originais num local seguro para o caso de precisar delas mais tarde. Preserve o seu guião e as suas notas, também.

## <a name="next-steps"></a>Passos seguintes

Está pronto para carregar as suas gravações e criar a sua voz personalizada.

> [!div class="nextstepaction"]
> [Criar fontes de voz personalizadas](how-to-customize-voice-font.md)
