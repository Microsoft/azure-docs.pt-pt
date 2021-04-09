---
title: Gravar amostras de voz personalizadas - Serviço de fala
titleSuffix: Azure Cognitive Services
description: Faça uma voz personalizada de qualidade de produção preparando um script robusto, contratando bons talentos de voz e gravando profissionalmente.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/13/2020
ms.author: erhopf
ms.openlocfilehash: 6d2ad87f6a8c79f61d62140a1549968cd88d83aa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "101722318"
---
# <a name="record-voice-samples-to-create-a-custom-voice"></a>Registar amostras de voz para criar uma voz personalizada

Criar uma voz personalizada de produção de alta qualidade do zero não é um empreendimento casual. O componente central de uma voz personalizada é uma grande coleção de amostras áudio do discurso humano. É vital que estas gravações áudio sejam de alta qualidade. Escolha um talento de voz que tenha experiência em fazer este tipo de gravações, e que sejam gravados por um engenheiro de gravação competente usando equipamento profissional.

Antes de poder fazer estas gravações, no entanto, precisa de um guião: as palavras que serão ditas pelo seu talento de voz para criar as amostras de áudio. Para obter melhores resultados, o seu script deve ter uma boa cobertura fonética e variedade suficiente para treinar o modelo de voz personalizado.

Muitos pequenos, mas importantes detalhes vão para a criação de uma gravação de voz profissional. Este guia é um roteiro para um processo que o ajudará a obter bons e consistentes resultados.

> [!NOTE]
> Se quiser treinar uma voz neural, deve especificar um perfil de talento de voz com o ficheiro de consentimento áudio fornecido pelo talento de voz, reconhecendo usar os seus dados de fala para treinar um modelo de voz personalizado. Ao preparar o seu script de gravação, certifique-se de que inclui a frase abaixo. 

> "Eu [declaro o seu primeiro e último nome] estou ciente de que as gravações da minha voz serão usadas [pelo nome da empresa] para criar e usar uma versão sintética da minha voz."
Esta frase será usada para verificar se os dados de formação são feitos pela mesma pessoa que faz o consentimento. Leia mais sobre a [verificação](/legal/cognitive-services/speech-service/custom-neural-voice/data-privacy-security-custom-neural-voice?context=%2fazure%2fcognitive-services%2fspeech-service%2fcontext%2fcontext) de talentos de voz aqui.

> A Voz Neural Personalizada está disponível com acesso limitado. Certifique-se de compreender os [requisitos responsáveis da IA](/legal/cognitive-services/speech-service/custom-neural-voice/limited-access-custom-neural-voice?context=%2fazure%2fcognitive-services%2fspeech-service%2fcontext%2fcontext) e [aplicar o acesso aqui.](https://aka.ms/customneural) 

> [!TIP]
> Para obter os resultados da mais alta qualidade, considere envolver a Microsoft para ajudar a desenvolver a sua voz personalizada. A Microsoft tem uma vasta experiência em produzir vozes de alta qualidade para os seus próprios produtos, incluindo cortana e Office.

## <a name="voice-recording-roles"></a>Funções de gravação de voz

Existem quatro papéis básicos num projeto de gravação de voz personalizado:

Função|Objetivo
-|-
Talento de voz        |A voz desta pessoa formará a base da voz personalizada.
Engenheiro de gravação  |Supervisiona os aspetos técnicos da gravação e opera o aparelho de gravação.
Diretor            |Prepara o guião e treina o desempenho do talento de voz.
Editor              |Finaliza os ficheiros áudio e prepara-os para o upload para o portal Voz Personalizada.

Um indivíduo pode preencher mais do que uma função. Este guia assume que irá principalmente preencher o papel de diretor e contratar tanto um talento de voz como um engenheiro de gravação. Se quiser fazer as gravações por si mesmo, este artigo inclui algumas informações sobre o papel de engenheiro de gravação. O papel de editor só é necessário depois da sessão, pelo que pode ser desempenhado pelo diretor ou pelo engenheiro de gravação.

## <a name="choose-your-voice-talent"></a>Escolha o seu talento de voz

Os atores com experiência no voiceover ou no trabalho de personagem de voz fazem um bom talento de voz personalizado. Você também pode encontrar talento adequado entre locutores e newsreaders.

Escolha o talento de voz cuja voz natural você gosta. É possível criar vozes únicas de "caráter", mas é muito mais difícil para a maioria dos talentos executá-las de forma consistente, e o esforço pode causar tensão de voz.

> [!TIP]
> Geralmente, evite usar vozes reconhecíveis para criar uma voz personalizada — a menos, claro, que o seu objetivo seja produzir uma voz de celebridade. As vozes menos conhecidas são geralmente menos distrativas para os utilizadores.

O fator mais importante para escolher o talento de voz é a consistência. As tuas gravações devem soar como se tivessem sido feitas no mesmo dia no mesmo quarto. Você pode abordar este ideal através de boas práticas de gravação e engenharia.

O teu talento de voz é a outra metade da equação. Devem ser capazes de falar com uma taxa consistente, nível de volume, tom e tom. Uma dicção clara é uma obrigação. O talento também precisa ser capaz de controlar estritamente a sua variação de tom, efeito emocional e maneirismos da fala.

Gravar amostras de voz personalizadas pode ser mais fatigado do que outros tipos de trabalho de voz. A maioria dos talentos de voz pode gravar duas ou três horas por dia. Limite as sessões para três ou quatro por semana, com um dia de folga, se possível.

Trabalhe com o seu talento de voz para desenvolver uma "persona" que define o som geral e o tom emocional da voz personalizada. No processo, vais identificar o que soa "neutro" para essa personalidade. Utilizando a capacidade de Voz Neural Personalizada, pode treinar um modelo que fala com emoções. Defina os "estilos de fala" e peça ao seu talento de voz para ler o guião de uma forma que ressoe os estilos que deseja.  

Uma persona pode ter, por exemplo, uma personalidade naturalmente otimista. Assim, a voz "deles" pode levar uma nota de otimismo mesmo quando falam neutramente. No entanto, tal traço de personalidade deve ser subtil e consistente. Ouça as leituras das vozes existentes para ter uma ideia do que pretende.

> [!TIP]
> Normalmente, vais querer ser dono das gravações de voz que fazes. O seu talento de voz deve ser favorável a um contrato de trabalho por aluguer para o projeto.

## <a name="create-a-script"></a>Criar um script

O ponto de partida de qualquer sessão de gravação de voz personalizada é o script, que contém as expressões a serem ditas pelo seu talento de voz. (O termo "expressões" abrange frases completas e frases mais curtas.)

As declarações no seu guião podem vir de qualquer lugar: ficção, não-ficção, transcrições de discursos, notícias e qualquer outra coisa disponível na forma impressa. Se quiser certificar-se de que a sua voz se sai bem em tipos específicos de palavras (como terminologia médica ou jargão de programação), talvez queira incluir frases de documentos académicos ou documentos técnicos. Para uma breve discussão sobre potenciais questões legais, consulte a secção ["Legalidades".](#legalities) Também pode escrever o seu próprio texto.

As suas declarações não precisam de vir da mesma fonte, ou do mesmo tipo de fonte. Nem precisam de ter nada a ver um com o outro. No entanto, se utilizar frases definidas (por exemplo, "Iniciou sessão com sucesso") na sua aplicação de fala, certifique-se de as incluir no seu script. Isto dará à sua voz personalizada uma melhor hipótese de pronunciar bem essas frases. E se decidires usar uma gravação em vez de um discurso sintetizado, já a terás na mesma voz.

Embora a consistência seja fundamental na escolha do talento de voz, a variedade é a marca de um bom guião. Seu script deve incluir muitas palavras e frases diferentes com uma variedade de comprimentos de frase, estruturas e humores. Todos os sons da língua devem ser representados várias vezes e em inúmeros contextos (chamada *cobertura fonética).*

Além disso, o texto deve incorporar todas as formas de um som específico poder ser representado por escrito e colocar cada som em diferentes locais das frases. Ambas as frases declarativas e as perguntas devem ser incluídas e lidas com entoação adequada.

É difícil escrever um script que forneça dados *suficientes* para permitir que o portal De Discurso Personalizado construa uma boa voz. Na prática, a forma mais simples de fazer um script que obtém uma cobertura fonética robusta é incluir um grande número de amostras. As vozes padrão que a Microsoft fornece foram construídas a partir de dezenas de milhares de expressões. Você deve estar preparado para gravar alguns a vários milhares de proclamações no mínimo para construir uma voz personalizada de qualidade de produção.

Verifique cuidadosamente se o script se pode verificar se há erros. Se possível, pedir a outra pessoa para verificar também. Quando analisares o guião com o teu talento, provavelmente vais apanhar mais alguns erros.

### <a name="script-format"></a>Formato de script

Pode escrever o seu script no Microsoft Word. O script é para ser utilizado durante a sessão de gravação, para que possa configurar de qualquer forma que ache fácil de trabalhar. Crie o ficheiro de texto que é exigido separadamente pelo portal Voz Personalizada.

Um formato básico de script contém três colunas:

* O número da expressão, a partir de 1. A numeragem facilita que todos no estúdio se refiram a uma determinada expressão ("vamos tentar o número 356 novamente"). Pode utilizar a função de numeragem do parágrafo Word para numerar automaticamente as linhas da tabela.
* Uma coluna em branco onde escreverá o número de tomada ou código de tempo de cada expressão para ajudá-lo a encontrá-lo na gravação acabada.
* O texto da própria expressão.

![Script de exemplo](media/custom-voice/script.png)

> [!NOTE]
> A maioria dos estúdios grava em segmentos curtos conhecidos como *takes*. Cada tomada normalmente contém 10 a 24 expressões. Só notando que o número de tomada é suficiente para encontrar uma expressão mais tarde. Se estiver a gravar num estúdio que prefira fazer gravações mais longas, em vez disso, vai querer anotar o código de tempo. O estúdio terá uma exibição de tempo proeminente.

Deixe espaço suficiente após cada linha para escrever notas. Certifique-se de que nenhuma expressão é dividida entre páginas. Numeram as páginas e imprima o seu guião num dos lados do papel.

Imprima três cópias do guião: uma para o talento, outra para o engenheiro, e outra para o realizador (você). Utilize um clip em papel em vez de agrafos: um artista de voz experiente separará as páginas para evitar fazer barulho à medida que as páginas são viradas.

### <a name="legalities"></a>Legalidades

De acordo com a lei de direitos autorais, a leitura de um ator de texto protegido por direitos de autor pode ser uma performance para a qual o autor da obra deve ser compensado. Este desempenho não será reconhecível no produto final, a voz personalizada. Ainda assim, a legalidade da utilização de um trabalho com direitos de autor para este efeito não está bem estabelecida. A Microsoft não pode fornecer aconselhamento jurídico sobre este assunto; consultar o seu próprio conselho.

Felizmente, é possível evitar totalmente estas questões. Existem muitas fontes de texto que pode usar sem permissão ou licença.

|Fonte de texto|Description|
|-|-|
|[CmU Arctic corpus](http://festvox.org/cmu_arctic/)|Cerca de 1100 frases selecionadas de trabalhos fora de direitos autorais especificamente para uso em projetos de síntese de fala. Um excelente ponto de partida.|
|Não funciona mais<br>sob direitos autorais|Normalmente, obras publicadas antes de 1923. Para inglês, [o Project Gutenberg](https://www.gutenberg.org/) oferece dezenas de milhares de obras deste tipo. Talvez queira focar-se em trabalhos mais recentes, já que a língua estará mais próxima do inglês moderno.|
|Governo &nbsp; trabalha|As obras criadas pelo governo dos Estados Unidos não são copyright nos Estados Unidos, embora o governo possa reivindicar direitos de autor noutros países/regiões.|
|Domínio público|Obras para as quais os direitos de autor foram explicitamente desausados ou que foram dedicados ao domínio público. Pode não ser possível renunciar inteiramente aos direitos de autor em algumas jurisdições.|
|Obras permissivamente licenciadas|Obras distribuídas sob licença como Creative Commons ou a Licença de Documentação Livre GNU (GFDL). A Wikipedia usa o GFDL. Algumas licenças, no entanto, podem impor restrições ao desempenho do conteúdo licenciado que pode afetar a criação de um modelo de voz personalizado, por isso leia a licença cuidadosamente.|

## <a name="recording-your-script"></a>Gravando o seu script

Grave o seu guião num estúdio de gravação profissional especializado em trabalho de voz. Terão uma cabine de gravação, o equipamento certo e as pessoas certas para o operar. Vale a pena não esmorecer na gravação.

Discuta o seu projeto com o engenheiro de gravação do estúdio e ouça os seus conselhos. A gravação deve ter pouca ou nenhuma compressão dinâmica (máximo de 4:1). É fundamental que o áudio tenha um volume consistente e uma elevada relação sinal-ruído, enquanto está livre de sons indesejados.

### <a name="do-it-yourself"></a>Faça-o por si mesmo.

Se queres fazer a gravação sozinho, em vez de ires para um estúdio de gravação, aqui está um pequeno primer. Graças ao aumento da gravação e podcasting em casa, é mais fácil do que nunca encontrar bons conselhos de gravação e recursos online.

A sua "cabine de gravação" deve ser uma pequena sala sem eco ou "tom de quarto". Deve ser o mais silencioso e à prova de som possível. As cortinas nas paredes podem ser usadas para reduzir o eco e neutralizar ou "apagar" o som da sala.

Utilize um microfone condensador de estúdio de alta qualidade ("mic" para abreviar) destinado a gravar voz. Sennheiser, AKG e até microfones zoom mais recentes podem produzir bons resultados. Você pode comprar um microfone, ou alugar um de uma empresa local de aluguer de audiovisuais. Procure um com interface USB. Este tipo de microfone combina convenientemente o elemento do microfone, o pré-âp e o conversor analógico-digital num único pacote, simplificando a ligação.

Também pode utilizar um microfone analógico. Muitas casas de aluguer oferecem microfones "vintage" conhecidos pelo seu caráter de voz. Note que a engrenagem analógica profissional utiliza conectores XLR equilibrados, em vez da ficha de 1/4 polegadas que é usada em equipamentos de consumo. Se for analógico, também necessitará de um pré-amplificador e de uma interface de áudio do computador com estes conectores.

Instale o microfone num suporte ou num boom e instale um filtro pop em frente ao microfone para eliminar o ruído de consoantes "plosivas" como "p" e "b". Alguns microfones vêm com um suporte de suspensão que os isola das vibrações no suporte, o que é útil.

O talento da voz deve ficar a uma distância consistente do microfone. Utilize fita adesiva no chão para marcar onde devem estar. Se o talento preferir sentar-se, tome especial cuidado para monitorizar a distância do microfone e evitar o ruído da cadeira.

Use um suporte para segurar o guião. Evite inclinar o suporte para que possa refletir o som em direção ao microfone.

A pessoa que opera o equipamento de gravação - o engenheiro - deve estar numa sala separada do talento, com alguma forma de falar com o talento na cabine de gravação (um *circuito de talkback).*

A gravação deve conter o mínimo de ruído possível, com um objetivo de uma relação sinal-ruído de 80 db ou melhor.

Ouça atentamente uma gravação de silêncio na sua "cabine", descubra de onde vem qualquer ruído e elimine a causa. Fontes comuns de ruído são respiradouros de ar, balastros de luz fluorescente, tráfego em estradas próximas, e ventiladores de equipamentos (até pCs de caderno podem ter ventiladores). Os microfones e os cabos podem captar ruídos elétricos da cablagem cacínica próxima, normalmente um zumbido ou um zumbido. Um zumbido também pode ser causado por um *laço de terra*, que é causado por ter o equipamento ligado a mais de um circuito elétrico.

> [!TIP]
> Em alguns casos, poderá utilizar um equalizador ou um plug-in de software de redução de ruído para ajudar a remover o ruído das suas gravações, embora seja sempre melhor detê-lo na sua origem.

De modo a definir os níveis para que a maior parte da gama dinâmica disponível de gravação digital seja utilizada sem sobrevivagem. Isso significa que ajuste o áudio alto, mas não tão alto que se distorça. Um exemplo da forma de onda de uma boa gravação é mostrado na seguinte imagem:

![Uma boa forma de onda de gravação](media/custom-voice/good-recording.png)

Aqui, a maior parte da gama (altura) é usada, mas os picos mais altos do sinal não atingem a parte superior ou inferior da janela. Também pode ver que o silêncio na gravação aproxima-se de uma fina linha horizontal, indicando um piso de ruído baixo. Esta gravação tem uma gama dinâmica aceitável e uma relação sinal-ruído.

Grave diretamente no computador através de uma interface de áudio de alta qualidade ou de uma porta USB, dependendo do microfone que está a utilizar. Para analógico, mantenha a cadeia de áudio simples: microfone, pré-amplificador, interface áudio, computador. Você pode licenciar tanto [a Avid Pro Tools](https://www.avid.com/en/pro-tools) como a Adobe [Audition](https://www.adobe.com/products/audition.html) mensalmente a um custo razoável. Se o seu orçamento for extremamente apertado, experimente a [Audácia](https://www.audacityteam.org/)gratuita.

Grave em 44,1 kHz 16 bit monofónico (qualidade de CD) ou melhor. O estado atual da arte é de 48 kHz 24-bit, se o seu equipamento o suportar. Irá reduzir a amostra do seu áudio para 16 kHz 16-bit antes de o submeter ao portal De Voz Personalizada. Ainda assim, vale a pena ter uma gravação original de alta qualidade no caso de serem necessárias edições.

Idealmente, ter pessoas diferentes a servir nas funções de diretor, engenheiro e talento. Não tente fazer tudo sozinho. Numa pitada, uma pessoa pode ser o diretor e o engenheiro.

### <a name="before-the-session&quot;></a>Antes da sessão

Para evitar perder tempo de estúdio, passe pelo guião com o seu talento de voz antes da sessão de gravação. Enquanto o talento de voz se familiariza com o texto, eles podem esclarecer a pronúncia de quaisquer palavras desconhecidas.

> [!NOTE]
> A maioria dos estúdios de gravação oferecem exibição electrónica de scripts na cabine de gravação. Neste caso, digite as suas notas de execução diretamente no documento do script. No entanto, ainda vai querer uma cópia em papel para tomar notas durante a sessão. A maioria dos engenheiros também vai querer uma cópia em papel. E ainda vais querer uma terceira cópia impressa como cópia para o talento, caso o computador esteja em baixo.

O seu talento de voz pode perguntar qual a palavra que quer enfatizada numa expressão (a &quot;palavra operativa"). Diga-lhes que quer uma leitura natural sem ênfase em particular. A ênfase pode ser adicionada quando a fala é sintetizada; não deve fazer parte da gravação original.

Direcione o talento para pronunciar as palavras de forma distinta. Cada palavra do guião deve ser pronunciada como escrita. Os sons não devem ser omitidos ou resmungar em conjunto, como é comum no discurso casual, *a não ser que tenham sido escritos dessa forma no guião*.

|Texto escrito|Pronúncia casual indesejada|
|-|-|
|nunca vai dar-lhe|nunca vai dar-lhe|
|há quatro luzes|há quatro luzes|
|como está o tempo de hoje|como está o tempo hoje|
|dizer olá ao meu amiguinho|dizer olá para o meu amigo lil '|

O talento *não* deve adicionar pausas distintas entre as palavras. A sentença ainda deve fluir naturalmente, mesmo quando soa um pouco formal. Esta bela distinção pode ser prática para acertar.

### <a name="the-recording-session"></a>A sessão de gravação

Crie uma gravação de referência, ou ficheiro de *correspondência,* de uma expressão típica no início da sessão. Peça ao talento para repetir esta linha em cada página ou assim. De cada vez, compare a nova gravação com a referência. Esta prática ajuda o talento a manter-se consistente em volume, tempo, pitch e entoação. Entretanto, o engenheiro pode usar o ficheiro de correspondência como referência para níveis e consistência geral do som.

O ficheiro de correspondência é especialmente importante quando retoma a gravação após uma pausa ou noutro dia. Vai querer jogá-lo algumas vezes para o talento e fazê-los repetir cada vez até que estejam a condizer bem.

Treina o teu talento para respirar fundo e fazer uma pausa por um momento antes de cada expressão. Grave alguns segundos de silêncio entre as expressões. As palavras devem ser pronunciadas da mesma forma cada vez que aparecem, considerando o contexto. Por exemplo, "gravar" como verbo é pronunciado de forma diferente de "record" como substantivo.

Grave uns bons cinco segundos de silêncio antes da primeira gravação para capturar o "tom de quarto". Esta prática ajuda o portal Custom Voice a compensar qualquer ruído restante nas gravações.

> [!TIP]
> Tudo o que precisas mesmo de capturar é o talento de voz, para que possas fazer uma gravação monofónica (single-channel) apenas das suas linhas. No entanto, se gravar em estéreo, pode usar o segundo canal para gravar a conversa na sala de controlo para capturar a discussão de linhas ou tomadas particulares. Remova esta faixa da versão que é enviada para o portal Voz Personalizada.

Ouçam atentamente, usando auscultadores, para o desempenho do talento de voz. Estás à procura de boa mas natural dicção, pronúncia correta e falta de sons indesejados. Não hesite em pedir ao seu talento para voltar a gravar uma expressão que não cumpra estes padrões.

> [!TIP]
> Se estiver a utilizar um grande número de expressões, uma única expressão pode não ter um efeito percetível na voz personalizada resultante. Pode ser mais conveniente simplesmente notar quaisquer expressões com problemas, excluí-las do seu conjunto de dados e ver como a sua voz personalizada acaba. Pode sempre voltar ao estúdio e gravar as amostras perdidas mais tarde.

Note o número de tomada ou o código de tempo no seu script para cada expressão. Peça ao engenheiro para marcar cada expressão nos metadados ou na folha de sugestão da gravação também.

Faça pausas regulares e forneça uma bebida para ajudar o seu talento de voz a manter a voz em boa forma.

### <a name="after-the-session"></a>Após a sessão

Estúdios de gravação modernos funcionam em computadores. No final da sessão, recebe um ou mais ficheiros áudio, não uma fita. Estes ficheiros serão provavelmente o formato WAV ou AIFF em qualidade de CD (44,1 kHz 16 bits) ou melhor. 48 kHz 24-bit é comum e desejável. Geralmente, não são necessárias taxas de amostragem mais elevadas, como 96 kHz.

O portal Voz Personalizada requer que cada expressão fornecida esteja no seu próprio ficheiro. Cada ficheiro áudio entregue pelo estúdio contém múltiplas expressões. Assim, a principal tarefa pós-produção é dividir as gravações e prepará-las para a submissão. O engenheiro de gravação pode ter colocado marcadores no ficheiro (ou fornecido uma folha de sugestão separada) para indicar onde cada expressão começa.

Use as suas notas para encontrar as tomadas exatas que deseja e, em seguida, use um utilitário de edição de som, como [Avid Pro Tools,](https://www.avid.com/en/pro-tools) [Adobe Audition](https://www.adobe.com/products/audition.html), ou o [Audacity](https://www.audacityteam.org/)gratuito , para copiar cada expressão em um novo ficheiro.

Deixe apenas cerca de 0,2 segundos de silêncio no início e no fim de cada clipe, exceto no primeiro. O ficheiro deve começar com cinco segundos de silêncio. Não utilize um editor de áudio para "zero out" partes silenciosas do ficheiro. Incluindo o "tom de sala" ajudará os algoritmos de Voz Personalizada a compensar qualquer ruído de fundo residual.

Ouça cada ficheiro com atenção. Nesta fase, pode editar pequenos sons indesejados que perdeu durante a gravação, como uma ligeira pancada no lábio antes de uma linha, mas tenha cuidado para não remover qualquer discurso real. Se não conseguir corrigir um ficheiro, remova-o do conjunto de dados e note que o fez.

Converta cada ficheiro em 16 bits e uma taxa de amostra de 16 kHz antes de guardar e, se gravou a conversa do estúdio, remova o segundo canal. Guarde cada ficheiro em formato WAV, nomeando os ficheiros com o número de expressão do seu script.

Por fim, crie a *transcrição* que associa cada ficheiro WAV a uma versão de texto da expressão correspondente. [A criação de vozes personalizadas](./how-to-custom-voice-create-voice.md) inclui detalhes do formato necessário. Pode copiar o texto diretamente do seu script. Em seguida, crie um ficheiro Zip dos ficheiros WAV e da transcrição de texto.

Arquive as gravações originais num local seguro, caso precise delas mais tarde. Preserva o teu guião e notas, também.

## <a name="next-steps"></a>Passos seguintes

Está pronto para carregar as suas gravações e criar a sua voz personalizada.

> [!div class="nextstepaction"]
> [Criar fontes de voz personalizadas](./how-to-custom-voice-create-voice.md)