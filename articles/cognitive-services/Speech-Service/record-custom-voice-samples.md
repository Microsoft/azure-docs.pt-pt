---
title: Exemplos de voz personalizada - serviços de voz de registos
titleSuffix: Azure Cognitive Services
description: Torne uma voz personalizada de qualidade de produção ao preparar um script robusto, contratação de talento de voz boa e gravar profissionalmente.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: erhopf
ms.custom: seodec18
ms.openlocfilehash: d5d3d7031f9795db0ae04bc707bd9e7707137210
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57530744"
---
# <a name="record-voice-samples-to-create-a-custom-voice"></a>Exemplos de voz de registo para criar uma voz personalizada

Criar uma voz personalizados de alta qualidade de produção do zero não é uma empreitada casual. O componente central de uma voz personalizada é uma grande coleção de exemplos de áudio de voz humana. É fundamental que estas gravações de áudio ser de alta qualidade. Escolha um talento de voz com experiência no fazer esse tipo de gravações e tê-los registado por um engenheiro de gravação competente com equipamento profissional.

Antes de poder fazer essas gravações, no entanto, é necessário um script: as palavras que serão dito pelo seu talento de voz para criar os exemplos de áudio. Para obter melhores resultados, o script tem de ter boa cobertura fonética e variedade suficiente para preparar o modelo de voz personalizada.

Muitos detalhes de pequenas, mas importantes se incluem na criação de uma gravação de voz profissional. Este guia é uma estratégia para um processo que irá ajudá-lo a obter bons resultados consistentes.

> [!TIP]
> Para obter os resultados de qualidade mais altos, considere como utilizar o Microsoft para ajudar a desenvolver a sua voz personalizada. A Microsoft tem ampla experiência em produzir vozes de alta qualidade para seus próprio produtos, incluindo o Cortana e o Office.

## <a name="voice-recording-roles"></a>Funções de gravação de voz

Existem quatro funções básicas num projeto de gravação de voz personalizada:

Função|Objetivo
-|-
Talentos de voz        |Voz desta pessoa formará a base de voz personalizada.
Engenheiro de gravação  |Supervisiona os aspectos técnicos da gravação e opera o equipamento de gravação.
Diretor            |Prepara o script e coaches desempenho o talento de voz.
Editor              |Finaliza os arquivos de áudio e prepara-os para carregamento para o portal de voz personalizada.

Um indivíduo pode preencher a mais de uma função. Este guia assume que irá ser principalmente preenchendo a função de diretor e contratação de talento uma voz e engenheiro de gravação. Se pretender disponibilizar as gravações por conta própria, este artigo inclui algumas informações sobre a função de engenheiro de gravação. A função de editor não é necessária após a sessão, pelo que podem ser efetuadas pelo diretor ou o engenheiro de gravação.

## <a name="choose-your-voice-talent"></a>Escolha o seu talento de voz

Atores com experiência no trabalho de caráter voiceover ou voz certifique talentos de voz personalizada boa. Muitas vezes, pode encontrar talento adequado entre announcers e newsreaders.

Escolha o talento de voz cujo natural de voz, como. É possível criar vozes exclusivo "character", mas é muito mais difícil para a maioria dos talentos de executá-los de forma consistente e o esforço pode fazer com que a sobrecarga de voz.

> [!TIP]
> Em geral, evitar o uso de vozes reconhecíveis para criar uma voz personalizada, a menos que, certamente, seu objetivo é produzir uma voz de celebridade. Vozes de menos conhecidos são, normalmente, menos distração aos utilizadores.

O único fator mais importante para a escolha de talentos de voz é a consistência. As gravações tudo soar pareçam feitas no mesmo dia na mesma sala. Pode abordar esse ideal através de práticas de gravação de bons e engenharia.

Seu talento de voz é a outra metade da equação. Ele deve poder falar com taxa consistente, nível de volume, pitch e tom. Limpar diction é essencial. O talento também tem de ser capaz de controlar rigorosamente seu variação pitch emocional efeito e mannerisms de voz.

Gravação de exemplos de voz personalizada pode ser mais fatiguing que outros tipos de trabalho de voz. A maioria dos talentos de voz podem gravar para dois ou três horas por dia. Limite sessões para três ou quatro uma semana, com um dia de folga intercalados se possível.

Gravações efetuadas para um modelo de voz devem ser emocionalmente neutras. Ou seja, uma expressão de sua decisão não deve ser lidos de forma a sua decisão. Tentarei pode ser adicionado para a fala sintetizada mais tarde através dos controlos prosody. Trabalhar com o seu talento de voz para desenvolver uma "pessoa" que define o tom geral som e emocional de voz personalizada. No processo, vai identificar quais "neutral" parece ser para essa pessoa.

Uma pessoa pode ter, por exemplo, uma personalidade upbeat naturalmente. Então, "seu" voz pode conter uma nota de optimism, mesmo quando elas falam neutrally. No entanto, uma característica de personalidade desse tipo deve ser sutil e consistente. Ouça a leituras, por vozes existentes para ter uma idéia de que o alvo é.

> [!TIP]
> Normalmente, desejará ter as gravações de voz que fizer. Seu talento de voz deve ser acessível a um contrato de trabalho de contratação para o projeto.

## <a name="create-a-script"></a>Criar um script

O ponto de partida de qualquer sessão de gravação de voz personalizada é o script, que contém as expressões a ser falado por seu talento de voz. (O termo "expressões com" abrange as sentenças completas e frases mais curtos.)

As expressões no seu script podem vir de qualquer lugar: fiction, não fiction, transcrições de seus discursos, notícias de relatórios e tudo o resto disponíveis no formulário de impressos. Se pretender certificar-se de que sua voz faz bem em tipos específicos de palavras (por exemplo, a terminologia médica ou gíria, programação), pode querer incluir frases de investigadores papers ou documentos técnicos. Para uma breve descrição de potenciais problemas legais, consulte a ["Serem"](#legalities) secção. Também pode escrever seu próprio texto.

As expressões não precisam de ser provenientes da mesma origem ou o mesmo tipo de origem. Eles nem precisa ter algo a ver com uns dos outros. No entanto, se quiser utilizar definir frases (por exemplo, "iniciaram sessão com êxito") na sua aplicação de voz, certifique-se para incluí-los no seu script. Isso dará a voz personalizada uma chance maior de pronouncing essas frases bem. E se deve optar por utilizar uma gravação em vez de fala sintetizada, já terá-na voz do mesmo.

Enquanto a consistência é fundamental na escolha de talentos de voz, a variedade é a marca de um script boa. O script deve incluir muitas diferentes palavras e frases com uma variedade de comprimentos de sentença, estruturas e reagem. Cada som no idioma deve ser representada várias vezes e em vários contextos (chamado *cobertura fonética*).

Além disso, o texto deve incorporar todas as formas que um determinado som podem ser representados por escrito e colocar cada som em diferentes locais nas frases. Frases declarativas e perguntas devem ser incluídas e ler com intonation apropriado.

É difícil escrever um script que fornece *suficiente* dados para permitir ao portal de voz personalizada criar uma voz boa. Na prática, a forma mais simples de fazer um script que alcança cobertura fonética robusta é incluir um grande número de amostras. As vozes padrão que a Microsoft fornece foram criadas a partir de dezenas de milhares de expressões. Deve estar preparado para gravar alguns a vários expressões milhares com no mínimo para criar uma voz personalizada de qualidade de produção.

Verifique o script cuidadosamente quanto a erros. Se possível, ter alguém verificá-lo demasiado. Ao executar o script com o seu talento, provavelmente irá capturar alguns erros mais.

### <a name="script-format"></a>Formato de script

Pode escrever o seu script no Microsoft Word. O script é para utilização durante a sessão de gravação, para que possa definir a segurança de qualquer forma, que encontrar fácil trabalhar com. Crie o ficheiro de texto que sejam necessários para o portal de voz personalizada em separado.

Um formato de script básico contém três colunas:

* O número de expressão, começando em 1. Numeração torna mais fácil para todos os utilizadores no studio para fazer referência a uma expressão específica ("Vamos tentar número 356 novamente"). Pode usar o parágrafo de Word numeração funcionalidade numerar as linhas da tabela automaticamente.
* Uma coluna em branco, onde irá escrever o número ou vez o código de cada ocorrência de pronunciação para o ajudar a localizá-la de gravação foi concluída.
* O texto da expressão em si.

![Script de exemplo](media/custom-voice/script.png)

> [!NOTE]
> A maioria dos estúdios resumindo gravar segmentos conhecidos como *demora*. Cada take normalmente contém expressões de 10 a 24. Apenas observar que o número é suficiente para encontrar uma expressão mais tarde. Se estiver a gravação num estúdio que prefere para fazer gravações mais tempo, desejará observar o código de tempo em vez disso. O studio terá uma visualização da hora de destaque.

Deixe espaço suficiente após cada linha escrever notas. Certifique-se de que nenhuma expressão é dividida entre páginas. Número de páginas e imprimir o seu script no lado do documento.

Três cópias do script de impressão: um para o talento, um para o engenheiro e outro para o diretor (). Utilizar um clip de papel em vez de staples: um artista de voz experientes irá separar as páginas para evitar agitando como as páginas são ativadas.

### <a name="legalities"></a>Serem

Nos termos da legislação dos direitos autorais, leitura de um ator de texto protegido por direitos autorais pode ser um para o qual o autor do trabalho deve ser compensado de desempenho. Este desempenho não possa ser reconhecido no produto final, a voz personalizada. Mesmo assim, não é bem estabelecida legalidade da utilização de um trabalho protegido por direitos autorais para esta finalidade. A Microsoft não é possível fornecer aconselhamento legal sobre esse problema; consulte o seu Conselho.

Felizmente, é possível evitar totalmente esses problemas. Existem várias fontes de texto que pode utilizar sem licença ou permissão.

|Origem de texto|Descrição|
|-|-|
|[Corpus CMU Arctic](http://festvox.org/cmu_arctic/)|Aproximadamente 1100 frases selecionadas a partir de fora de copyright funciona especificamente para uso em projetos de síntese de fala. Um ponto de partida excelente.|
|Já não funciona<br>autorais|Normalmente funciona publicado antes 1923. Para inglês, [Project Gutenberg](https://www.gutenberg.org/) oferece dezenas de milhares de tal funciona. Poderá pretender focar-se no mais recente funciona, como o idioma será mais de perto como inglês modernos.|
|Governo&nbsp;funciona|Funciona criada pelo Governo dos Estados Unidos não é protegidos por direitos autorais nos Estados Unidos, embora o Governo poderá solicitar copyright em outros países.|
|Domínio público|Funciona para os quais direitos de autor tenham sido explicitamente isenta ou que tem sido dedicada para o domínio público. Não pode ser possível renunciaremos aos direitos de autor inteiramente em alguns jurisdições.|
|Funciona permissively licenciado|Como o Works distribuídos sob uma licença Creative Commons ou a licença GNU documentação gratuitas (GFDL). Wikipedia utiliza o GFDL. Algumas licenças, no entanto, podem impor restrições sobre o desempenho do conteúdo licenciado que possam afetar a criação de um modelo de voz personalizadas, por isso, leia a licença com atenção.|

## <a name="recording-your-script"></a>Gravar o seu script

Registe o seu script num estúdio de profissional gravar especializada em projetos de voz. Eles terão um stand de gravação, os equipamentos certo e as pessoas certas para trabalhar com ele. Vale a pena não tentados na gravação.

Discuta o seu projeto com o engenheiro de gravação do studio e ouça seu Conselho. A gravação deve ter pouco ou nenhum compressão de intervalo dinâmico (máximo de 4:1). É fundamental que o áudio têm volume consistente e uma alta taxa de sinal e ruído, e está livre de sons indesejados.

### <a name="do-it-yourself"></a>Faça mesmo

Se quiser fazer a gravação por conta própria, em vez de entrar num estúdio de gravação, aqui está uma cartilha curta. Graças ao aumento de gravação doméstica e podcasts, é mais fácil do que nunca encontrar conselhos de gravação de bons e recursos online.

Seu estande"gravação" deve ser uma única sala sem eco perceptível ou "tom sala." Deve ser tão silencioso e soundproof quanto possível. Drapes nas paredes podem ser utilizados para reduzir o eco e neutralizá ou "deaden" o som da sala.

Use um microfone de condenser ("mic" para abreviar) de alta qualidade studio destinado a gravação de voz. Sennheiser AKG e até mesmo mais recente microfones de Zoom podem produzem bons resultados. Pode comprar um mic ou alugar um a partir de uma empresa de aluguel audiovisuais local. Procure um com uma interface USB. Este tipo de mic convenientemente combina o elemento de microfone, preamp e conversor analógico para o digital num pacote, simplificando a conexão.

Também pode usar um microfone analógico. Muitos alugueres casas oferecem microfones "capacidade" renomados para respetiva caractere de voz. Tenha em atenção que utiliza profissional engrenagem analógica com balanceamento de conectores XLR, em vez de 1/4-polegadas desconectou que é utilizado em equipamentos de consumidor. Se for analógica, também terá uma preamp e uma interface de áudio do computador com estes conectores.

Instalar o microfone num site ou boom e instalar um filtro de POP-à frente o microfone para eliminar a interferência dos consoantes "plosive" como "p" e "b". Alguns microfones são fornecidos com uma montagem de suspensão os isola da vibrations em espera, o que é útil.

Os talentos de voz devem manter-se a uma distância consistente do microfone. Utilize a banda no chão para marcar onde ele deve funcionar. Se prefere o talento sentar-se, tomar cuidado especial para monitorizar a distância de mic e evitar o ruído de cadeira.

Utilize um para manter o script. Evite angling a espera para que ela pode refletir o som no microfone.

A pessoa que o equipamento de gravação a funcionar — o engenheiro — deve estar num ambiente separado de talento, com alguma forma para comunicar com o talento no estande da gravação (um *circuito talkback).*

A gravação deve conter como ruído pequeno quanto possível, com o objetivo de um rácio de sinal e ruído 80-db ou melhor.

Ouça diretamente a uma gravação de silêncio no seu "portagens," descobrir onde é proveniente de qualquer ruído e eliminar a causa. Origens comuns de ruído são eventos de ar, fluorescente ballasts leves, o tráfego nas proximidades de estradas e os nossos fãs de equipamentos (até mesmo bloco de notas PCs poderá ter de fãs). Microfones e cabos podem pegar elétrica interferência dos próximos conectando de AC, normalmente, um hum ou o buzz. Um repercussões também podem ser causado por um *loop de zero*, que é causado pela existência equipamento ligado a mais do que um circuito elétrico.

> [!TIP]
> Em alguns casos, poderá conseguir utilizar uma equalizer ou um software de redução de ruído Plug-in para o ajudar a remover barulho do seu gravações, embora é sempre melhor interrompê-lo na origem.

Definir níveis, de modo que a maior parte do intervalo dinâmico disponível de gravação de digital é utilizado sem overdriving. Isso significa que definir o áudio alto, mas não tão voz alta que ele fica distorcido. Um exemplo de uma forma de onda de uma boa gravação é mostrado na imagem seguinte:

![Uma forma de onda de gravação de bons](media/custom-voice/good-recording.png)

Aqui, é utilizada a maior parte do intervalo (altura), mas os mais elevados picos de sinal de não chegam a parte superior ou inferior da janela. Também pode ver que o silêncio na gravação aproxima-se uma linha horizontal dinâmico, que indica um andar de baixo de ruído. Esta gravação tem o intervalo aceitável de dinâmico e a proporção de sinal e ruído.

Registo diretamente no computador através de uma interface de áudio de alta qualidade ou uma porta USB, consoante o mic está a utilizar. Para analógicas, manter a cadeia de áudio simples: mic, preamp, interface de áudio, computador. Pode licenciar ambos [ávido ferramentas Pro](https://www.avid.com/en/pro-tools) e [Adobe Audition](https://www.adobe.com/products/audition.html) mensal a um custo razoável. Se o orçamento é extremamente curto, experimente gratuitamente [Audacity](https://www.audacityteam.org/).

Registe 44.1 kHz 16 bits monophonic (CD qualidade) ou melhor. Atual-de-modernas são 48 kHz 24 bits, se seu equipamento de oferecer suporte a ele. Irá dimensionar o áudio a 16 kHz 16 bits antes de enviá-lo para o portal de voz personalizada. Ainda assim, vale a pena ter uma alta qualidade original gravar no caso de edições são necessários.

O ideal é que pessoas diferentes servir nas funções de diretor, engenheiro e talento. Não tente fazê-lo a todos por conta própria. Numa situação de emergência, uma pessoa pode ser o diretor e o engenheiro.

### <a name="before-the-session"></a>Antes da sessão

Para evitar perder tempo studio, execute o script com o seu talento de voz antes da sessão de gravação. Embora o talento de voz torna-se familiarizado com o texto, pode clarificar a pronúncia de quaisquer palavras familiarizadas.

> [!NOTE]
> A maioria dos estúdios de gravação oferecem apresentar eletrónica de scripts no stand a gravação. Neste caso, digite suas anotações de orientação sobre diretamente no documento o script. Continuará QUERENDO uma cópia do documento para tomar notas durante a sessão, no entanto. A maioria dos engenheiros desejará uma cópia impressa, demasiado. E irá desejar que um terceiro impressa cópia como uma cópia de segurança para o talento, no caso do computador está inativo.

Seu talento de voz pode perguntar qual palavra desejar enfatizada numa expressão (a "palavra operative"). Diga- lo para que deseja que uma leitura natural com nenhuma ênfase especial. Ênfase pode ser adicionado quando a conversão de voz é sintetizada; não deve ser uma parte da gravação original.

Direcione o talento pronunciar palavras de forma distinta. Todas as palavras do script devem ser pronunciada como está escrito. Sons não devem ser omitidos ou em conjunto, o que é comum na conversão de voz casual, slurred *, a menos que tenha sido escritos dessa forma no script*.

|Texto escrito|Pronúncia casual indesejada|
|-|-|
|nunca irão lhe para dar cópia de segurança|nunca irão lhe para dar cópia de segurança|
|Existem quatro luzes|lá está quatro luzes|
|como é a meteorologia hoje em dia|como é th "sobreviver aos hoje mesmo|
|Diga Olá para meu amigo pouco|Diga Olá a minha lil "amigo|

Deve o talento *não* adicionar interrupções distintas entre as palavras. A frase ainda deve fluir naturalmente, mesmo durante o soar formal um pouco. Essa distinção bem poderá demorar a prática de solucionar.

### <a name="the-recording-session"></a>A sessão de gravação

Criar uma referência de gravação, ou *correspondência de arquivo,* de uma expressão comum no início da sessão. Faça o talento repetir esta linha todas as páginas mais ou menos. Cada vez, compara a gravação de novo para a referência. Essa prática ajuda o talento permanecer consistente no volume, o tempo, o argumento de venda e intonation. Enquanto isso, o engenheiro pode utilizar o ficheiro de correspondência como referência para níveis e a consistência geral de som.

O ficheiro de correspondência é especialmente importante quando continua a gravação após uma interrupção ou em outro dia. Desejará reproduzi-lo a algumas vezes para o talento e tê-las a repeti-lo cada vez até que eles correspondem a bem.

Orientá-seu talento para tirar um respire fundo e colocar em pausa por um momento antes de cada ocorrência de pronunciação. Registe a alguns segundos de silêncio entre expressões com. Palavras devem ser pronuncia-se da mesma forma cada vez que aparecem, tendo em consideração o contexto. Por exemplo, "grave" como um verbo é pronunciado forma diferente de "Registro" como um nome próprio.

Registe um bom cinco segundos, de silêncio antes da gravação de primeira para capturar o "tom sala." Essa prática ajuda o portal de voz personalizada compensar qualquer ruído restante nas gravações.

> [!TIP]
> Tudo o que realmente precisa capturar é o talento de voz, para que possa tomar uma gravação (canal de único) monophonic de apenas linhas. No entanto, se gravar no alto, pode utilizar o segundo canal para registrar o chatter numa sala de controlo para capturar a discussão de linhas específicas ou demora. Remova este Roteiro da versão que é carregada para o portal de voz personalizada.

Ouça com atenção, utilizar auscultadores, ao desempenho o talento de voz. Está à procura de diction boa mas natural, pronúncia correta e a falta de sons indesejados. Não hesite em fazer o seu talento para registrar novamente uma expressão que não cumpram estas normas.

> [!TIP]
> Se estiver a utilizar um grande número de expressões, uma única expressão pode não ter um impacto perceptível sobre a voz personalizada resultante. Poderá ser mais vantajoso simplesmente observar qualquer expressões com problemas, excluí-los a partir do seu conjunto de dados e veja como a voz personalizada acontece. Pode sempre voltar ao studio e registe os exemplos em falta mais tarde.

Tenha em atenção o número ou tempo de código no script para cada ocorrência de pronunciação. Peça o engenheiro para marcar cada ocorrência de pronunciação em metadados a gravação ou folha de indicação também.

Dê quebras regulares e fornecer um café para ajudar o seu talento de voz, manter a sua voz em bom estado.

### <a name="after-the-session"></a>Após a sessão

Os estúdios de gravação modernos executam em computadores. No final da sessão, recebe uma ou mais arquivos de áudio, não uma banda. Estes ficheiros serão provavelmente WAV ou AIFF formato na qualidade de CD (44.1 kHz 16-bit) ou melhor. 48 kHz 24 bits é comum e desejável. Em geral, as taxas de amostragem mais elevadas, por exemplo, 96 kHz, não são necessários.

O portal de voz personalizada requer que cada ocorrência de pronunciação fornecida para estar em seu próprio arquivo. Cada arquivo de áudio fornecido pelo studio contém várias expressões. Assim, a principal tarefa pós-produção é dividir as gravações e prepará-los para a submissão. O engenheiro de gravação pode ter inserido marcadores no arquivo (ou fornecido uma folha de cálculo de indicação separado) para indicar onde começa cada ocorrência de pronunciação.

Utilize suas anotações para encontrar exatamente o leva pretendem e, em seguida, utilizam um som edição utilitário, como [ávido ferramentas Pro](https://www.avid.com/en/pro-tools), [Adobe Audition](https://www.adobe.com/products/audition.html), ou o software gratuito [Audacity](https://www.audacityteam.org/)para copiar cada expressão num novo arquivo.

Deixe apenas cerca de 0,2 segundos de silêncio no início e no final de cada clip, com exceção da primeira. Esse arquivo deve começar com um completo cinco segundos de silêncio. Não utilize um editor de áudio a partes de silenciosa "zero de saída" do ficheiro. Incluindo o tom"espaço" ajudará a voz personalizada algoritmos compensar nenhum barulho de fundo residual.

Ouça a cada ficheiro com cuidado. Nesta fase, pode editar pequeno sons indesejável que tenha perdido durante a gravação, como um smack lip ligeira antes de uma linha, mas tenha cuidado para não remover qualquer voz real. Se não é possível corrigir um arquivo, removê-lo a partir do seu conjunto de dados e observe que tenha feito.

Converter cada ficheiro de 16 bits e uma taxa de amostragem de 16 kHz antes de guardar e, se registou o chatter studio, remova o segundo canal. Guarde cada ficheiro em formato WAV, nomear os ficheiros com o número de expressão do seu script.

Por fim, crie o *transcrição* que associa cada arquivo WAV com uma versão de texto da expressão correspondente. [Criar tipos de voz personalizada](how-to-customize-voice-font.md) inclui detalhes sobre o formato necessário. Pode copiar o texto diretamente a partir do seu script. Em seguida, crie um ficheiro Zip dos arquivos WAV e a transcrição de texto.

Arquive as gravações originais num local seguro no caso de precisa deles mais tarde. Preserva o seu script e notas, demasiado.

## <a name="next-steps"></a>Passos Seguintes

Está pronto para carregar as gravações e criar a sua voz personalizada.

> [!div class="nextstepaction"]
> [Criar tipos de voz personalizada](how-to-customize-voice-font.md)
