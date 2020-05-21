---
title: Changelog de jogador de mídia azure
description: Troco do Jogador de Mídia Azure.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: overview
ms.date: 04/20/2020
ms.openlocfilehash: a1a55ceec2679034125ddd202402cabcbf71e17e
ms.sourcegitcommit: 595cde417684e3672e36f09fd4691fb6aa739733
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/20/2020
ms.locfileid: "83698302"
---
# <a name="changelog"></a>Registo de alterações #

## <a name="224-official-update-february-22-2019"></a>2.2.4 (Atualização Oficial 22 de fevereiro de 2019) ##

### <a name="bug-fixes-224"></a>Correções de bug 2.2.4 ###

- [Bug Fix] [AMP] [Acessibilidade] Removeu um separador fantasma acessível quando o ecrã de erro aparece
- [Bug Fix] [AMP] Fixou a chave quente 'M' para IE11 e Edge
- [Bug Fix] [AMP] Fixou uma exceção para as legendas CEA708
- [Bug Fix] [AMP] Corrigido um problema de congelamento de vídeo para o navegador Edge

### <a name="changes-224"></a>Alterações 2.2.4 ###

- [Mudança] [AMP] Quando um erro de desencriptação de fragmentos acontece, o jogador tenta a corrente e vários fragmentos para recuperar a reprodução
- [Mudança] [AMP] Tornou a AMP mais tolerante com fragmentos de vídeo ou áudio sobrepostos

## <a name="223-official-update-january-9-2019"></a>2.2.3 (Atualização Oficial 9 janeiro 2019) ##

### <a name="features"></a>Funcionalidades ###

- [Recurso] [HLS] Adicionei o menu de áudio para a reprodução do HLS do Safari

### <a name="bug-fixes-223"></a>Correções de bug 2.2.3 ###

- [Bug Fix] [AMP] [Acessibilidade] Durante as reprodução em direto, o botão "live" não pode ser selecionado utilizando o teclado
- [Bug Fix] [AMP] Erros fixos falsos positivos 0x0400003 devido a teste de MSE falhado
- [Bug Fix] [AMP] Corrigiu um problema onde o vídeo poderia congelar ao iniciar uma transmissão em direto

### <a name="changes-223"></a>Alterações 2.2.3 ###

- [Mudança] [AMP] Adicione mais informações no registo para permitir melhores diagnósticos
- [Mudança] [AMP] Quando mais de um bitrate está disponível na mesma resolução de ecrã, todos os bitrates estão disponíveis para seleção

## <a name="222-official-update"></a>2.2.2 (Atualização Oficial) ##

### <a name="bug-fixes-222"></a>Correções de bug 2.2.2 ###

- [Bug Fix] [AMP] Quando o jogador encontra uma falha de rede transitória, para a reprodução imediatamente
- [Bug Fix] [AMP] [Acessibilidade] O diálogo de erro não é acessível pelo teclado
- [Bug Fix] [AMP] Spinner infinito exibido ao reproduzir apenas o ativo áudio em vez de erro não suportado

### <a name="changes-222"></a>Alterações 2.2.2 ###

- [Mudança] [AMP] acrescentou cordas localizadas para publicidade UI

## <a name="221-official-update"></a>2.2.1 (Atualização Oficial) ##

### <a name="features-221"></a>Características 2.2.1 ###

- [Recurso] [CMAF] Apoio adicional ao HLS CMAF

### <a name="bug-fixes"></a>Correções de Erros ###

- [Bug Fix] [AMP] tempors inclaros na lógica de retry que rende erros de reprodução
- [Bug Fix] [AMP] [Firefox] evento terminado não é disparado no Firefox e Chrome quando parou o programa ao vivo
- [Bug Fix] [AMP] Controlos apresentados após setsource, mesmo quando os controlos são definidos como falsos nas opções dos jogadores

### <a name="changes"></a>Alterações ###

- [Mudança] [Legendaao vivo] Mudou o nome DaPI para legendas CEA de 608 para 708. Para mais informações, consulte [Definições de Legendas CEA708](https://docs.microsoft.com/javascript/api/azuremediaplayer/amp.player.cea708captionssettings)-->

## <a name="220-official-release"></a>2.2.0 (Lançamento Oficial) ##

### <a name="features-220"></a>Características 2.2.0 ###

- [Recurso] [Azurehtml5JS] [Flash] [LiveCaptions] Suporte de legendagem CEA 708 em tecnologia Azurehtml5JS e FlashSS para conteúdo claro e AES.

### <a name="bug-fixes-220"></a>Correções de bug 2.2.0 ###

- [Bug Fix] Deteção de versão flash não funciona no Chrome/Edge

### <a name="changes-220"></a>Alterações 2.2.0 ###

- [Mudança] [AMP] [Heurística] Mudou o nome do perfil heurístico de QuickStartive para LowLatency
- [Mudança] [Flash] Altere o leitor Flash para deteção de versão para permitir a reprodução do conteúdo da AES com a nova atualização Adobe Flash.

## <a name="219-official-hotfix"></a>2.1.9 (Hotfix Oficial) ##

### <a name="bug-fixes-219"></a>Correções de bug 2.1.9 ###

- [Bug Fix] [Ao vivo] Exceção ocorre quando streams ao vivo transição para vídeo a pedido/arquivos ao vivo

### <a name="changes-219"></a>Alterações 2.1.9 ###

- [Mudança] [Flash] [AES] Lógica tecnológica modificada flash para não usar compartilhos para desencriptação AES como Adobe bloqueou o uso a partir de Flash 30. Por favor, note que a reprodução só funcionará quando a Adobe implementar uma nova versão do Flash devido a um bug em v30. Por favor, consulte [questões conhecidas](azure-media-player-known-issues.md) para mais detalhes

## <a name="218-official-update"></a>2.1.8 (Atualização Oficial) ##

### <a name="bug-fixes-218"></a>Correções de bug 2.1.8 ###

- [Correção de insetos] Spinner ocasionalmente não mostra post seek e pré-play
- [Bug Fix] O jogador não começa a ser silenciado quando a opção silenciada está ativada
- [Bug Fix] O slider de volume é apresentado quando os controlos são definidos como falsos
- [Bug Fix] Reprodução ocasionalmente repetindo quando o utilizador salta para a borda ao vivo
- [Bug Fix] [Firefox] Jogador ocasionalmente lança exceção JavaScript na carga
- [Bug Fix] [Acessibilidade] Reproduzir/ Pausa/Volume de botão perder o contorno de foco quando selecionado utilizando controlos de teclado
- [Bug Fix] A fuga de memória fixa no leitor é eliminada
- [Bug Fix] Chamar src() após erros do jogador não repõe a fonte
- [Bug Fix] [Ao vivo] A AMP está em constante estado de carga quando o utilizador clica no botão Live após a transmissão ter terminado
- [Bug Fix] [Chrome] O jogador fica pendurado e a reprodução falha quando o navegador minimiza para o fundo.

### <a name="changes-218"></a>Alterações 2.1.8 ###

- [Mudança] Atualizado 0x0600001 errror para exibir quando o conteúdo DaES é reproduzido com Flash 30, uma vez que não é suportado neste momento. Por favor, consulte [questões conhecidas](azure-media-player-known-issues.md) para mais detalhes
- [Mudança] Acrescentou repetições adicionais para cenários ao vivo quando o manifesto pede 404 ou devolve manifestos vazios.

## <a name="217-official-update"></a>2.1.7 (Atualização Oficial) ##

### <a name="features-217"></a>Características 2.1.7 ###

- [Recurso] [AzureHtml5JS] Opção de configuração adicionada para descarregar dados velhos no tampão de fonte de mídia

### <a name="bug-fixes-217"></a>Correções de bug 2.1.7 ###

- [Bug Fix] [Acessibilidade] [Screen Reader] Removeu o cabeçalho em branco que o jogador incluiu quando o título não está definido
- [Bug Fix] [UWA] AMP abre exceção na reprodução na Universal Windows App
- [Bug Fix] [OSX] setActiveTextTrack() não trabalhando no Safari na OSx
- [Bug Fix] [Ao vivo] Clicar na borda ao vivo depois de eliminar e reinicializar a exceção dos rendimentos dos jogadores
- [Bug Fix] [Pele] Tempo atual truncado para certos ativos
- [Bug Fix] [DRM] correção incluída para suportar reprodução em navegadores que suportam vários DRM CENC

### <a name="changes-217"></a>Alterações 2.1.7 ###

- [Mudança] [Amostras] [Acessibilidade] Etiqueta de linguagem adicionada a todas as amostras

## <a name="216-official-update"></a>2.1.6 (Atualização Oficial) ##

### <a name="bug-fixes-216"></a>Correções de bug 2.1.6 ###

- [Bug Fix] AMP que exibe uma duração incorreta para um ativo específico
- [Bug Fix] [FairPlay-HLS] Erros de fairplay não se propagam à UI
- [Bug Fix] Propriedades heurísticas personalizadas sendo ignoradas em AMP 2.1.5.

### <a name="changes-216"></a>Alterações 2.1.6 ###

- [Mudança] [FairPlayDRM] Removeu o prazo para o pedido de Cert e o pedido de licença para fairPlay de forma a manter a paridade com as implementações playReady e Widevine
- [Mudança] Melhorias heurísticas misc para combater conteúdo desfocado

### <a name="features-216"></a>Características 2.1.6 ###

- [Recurso] Formato mpd-time-cmaf de suporte adicionado

## <a name="215-official-hotfix"></a>2.1.5 (Hotfix Oficial) ##

### <a name="bug-fixes-215"></a>Correções de bug 2.1.5 ###

- [Bug Fix] [Legendas] Estilo VTT não prestado corretamente pelo jogador
- [Bug Fix] [Acessibilidade] Botão ao vivo não tem etiqueta de ária

## <a name="214-official-update"></a>2.1.4 (Atualização Oficial) ##

### <a name="bug-fixes-214"></a>Correções de bug 2.1.4 ###

- [Bug Fix] [Acessibilidade] [Foco] Os utilizadores não podem abas para se concentrarem em botões personalizados adicionados à direita do botão de ecrã completo na barra de controlo
- [Bug Fix] [IE11] [Barra de volume] Tabbing para volume pop-up faz todo o ecrã de vídeo piscar no IE11 enquanto em modo de ecrã completo
- [Bug Fix] [Pele] Flush] Espaço exibido entre barra de controlo e pop-up da barra de volume
- [Bug Fix] [AMP] [Legendas] Faixas antigas incorporadas não são apuradas quando a fonte é alterada num jogador existente
- [Bug Fix] [Acessibilidade] [Narrador] Screen Reader lê controlo de volume incorretamente
- [Bug Fix] [Flashss] Play Event ocasionalmente não dispara da tecnologia Flash
- [Bug Fix] [AMP] [Foco] Reproduzir/pausa requer dois cliques quando o jogador tem foco e está em modo de ecrã completo
- [Bug Fix] [AMP] [Pele] Duração incorreta sendo exibida na barra de progresso para um ativo específico
- [Bug Fix] [Anúncios] [Ad Butler] O parser VAST não lida com ficheiroVAST que não tem evento de progresso
- [Bug Fix] [SDN] [AMP 2.1.1] Emissão fixa para suporte a plugin Hive SDN
- [Bug Fix] [Acessibilidade] Narrador lê "Botão De Silenciamento da Meia-Noite" quando o utilizador tem foco no botão de volume

### <a name="changes-214"></a>Alterações 2.1.4 ###

- [Mudança] [Acessibilidade] [Tecnologia Assistida] Os botões agora têm propriedade ária-ao vivo para melhorar a experiência com tecnologia de assistência
- [Mudança] [Acessibilidade] [Botão de volume] Narrador]Melhorou a acessibilidade do botão de volume modificando a funcionalidade de tabbing e o comportamento do slider. Estas alterações facilitam aos utilizadores de teclado modificar o volume do leitor
- [Mudança] Tempo limite do menu de contexto de inatividade aumentado de 3 a 5 segundos
- [Mudança] [Acessibilidade] [Luminosidade] Rácio de contraste de luminosidade melhorado nos menus de dropdown em definições de legendas

## <a name="213-official-update"></a>2.1.3 (Atualização Oficial) ##

### <a name="bug-fixes-213"></a>Correções de bug 2.1.3 ###

- [Bug Fix] [Plugins] Title Overlay] Title Overlay plugin lança exceções JS com AMP v2. X+
- [Bug Fix] O evento Source set é enviado para a consola JavaScript mesmo quando o registo está desligado
- [Bug Fix] [Pele] As dicas de tempo do jogador são renderizadas fora do contexto do jogador ao pairar sobre cada barra de duração final
- [Bug Fix] [Acessibilidade] [Screen Reader] Narrador lê "Region Landmark" ou "Video Player Region Landmark" quando o espectador se concentra no jogador
- [Bug Fix] [AMP] Não pode desativar o contorno do jogador via CSS
- [Bug Fix] [Acessibilidade] Não é possível aba para se concentrar em todo o leitor quando o utilizador está no modo de ecrã completo
- [Bug Fix] [Pele] [Ao vivo] Pele não responde ao texto ao vivo localizado em japonês
- [Bug Fix] [Pele] A duração e o tempo atual são cortados quando o fluxo > 60 min [Bug Fix][iPhone] Live]player mostra texto para a hora/duração atual na barra de controlo
- [Bug Fix] [AMP] Chamadas heurísticas de jogadores APIs rendem exceções JavaScript
- [Bug Fix] [Native Html5[iOS] Propriedade de videotag "playsinline" não se propaga ao jogador
- [Bug Fix] [iOS[iframe] O jogador não pode entrar no ecrã completo do iPhone se o leitor for carregado num iframe
- [Bug Fix] [AMP] [Heurística] AMP opera sempre com perfil híbrido independentemente das opções dos jogadores
- [Bug Fix] [AMP] Win8.1]lança quando hospedado na aplicação Win8.1 com uma webview

### <a name="changes-213"></a>Alterações 2.1.3 ###

- [Mudança] [AMP] Informações adicionadas do ponto final do CDN no evento FragmentDownloadComplete
- [Mudança] [AMP] [Ao vivo] Latência de streaming ao vivo melhorada e otimizada

## <a name="212-official-hotfix"></a>2.1.2 (Hotfix Oficial) ##

### <a name="bug-fixes-212"></a>Correções de bug 2.1.2 ####

- [Bug Fix] [Acessibilidade] [Narrador de Janelas] Narrador lê "Progress midnight" quando o utilizador tem contexto de barra de progresso e o tempo atual é 0:00
- [Bug Fix] [Skin]tamanho do logotipo é codificado em código JavaScript
- [Acessibilidade] [HotKeys] Os hotkeys não estão ativados quando o leitor é clicado.

### <a name="changes-212"></a>Alterações 2.1.2 ####

- [Mudança] [Exploração madeireira] Log manifesto URL quando o leitor não carregar manifesto

### <a name="features-212"></a>Características 2.1.2 ###

- [Mudança] [Desempenho] [Otimização] Melhores tempos de carga e arranque dos jogadores

## <a name="211-official-update"></a>2.1.1 (Atualização Oficial) ##

### <a name="bug-fixes-211"></a>Correções de bug 2.1.1 ####

- [Bug Fix] [iOS] Definição de Reprodução Automática para falsos rendimentos infinitos spinner no Safari para iOS
- [Bug Fix] Procurando um tempo maior do que a duração do conteúdo produz infinita spinner
- [Bug Fix] Hotkeys requerem vários separadores de teclado para obter o contexto do jogador para trabalhar
- [Bug Fix] Vídeo congela por alguns segundos após redimensionar o jogador em certos ativos
- [Bug Fix] Spinner infinito (após procurar completos) quando o utilizador faz múltiplos procura rapidamente
- [Bug Fix] A barra de controlo não é escondida durante a inatividade
- [Bug Fix] Abrir um webapp que acolhe AMP pode fazer com que a página web seja carregada duas vezes
- [Bug Fix] Infinito enquanto joga conteúdo certos ativos via Flash Tech
- [Bug Fix] Menu mais Opções não sendo exibido com plugins de 3ª festa
- [Bug Fix] [Pele] Tubo][Ao vivo] Dois ícones ao vivo são exibidos quando o jogador está na borda ao vivo de um programa
- [Bug Fix] [Pele] Logotipo não pode ser desativado
- [Bug Fix] [Conteúdo DD+] Spinner contínuo aparece para os ativos que contêm a faixa de áudio Dolby Digital
- [Bug Fix] A mais recente AMP congela ao mudar faixas de linguagem áudio durante o livestream
- [Bug Fix] desaparecimento fixo de fundo para spinner
- [Bug Fix] Spinner infinito em aes flash amostras estáticas correções de bugs

### <a name="changes-211"></a>Alterações 2.1.1 ####

- [Mudança] Código de Erro adicionado para requisito widevine Https: a partir do Chrome v58, o conteúdo de grande videira deve ser carregado/reproduzido através do protocolo caso contrário a `https://` reprodução falhará.
- [Mudança] Rótulo de ária adicionado para carregar spinner para que a tecnologia assistida possa narrar "carregamento de vídeo" quando o conteúdo está a carregar  

## <a name="210-official-release"></a>2.1.0 (Lançamento Oficial) ##

### <a name="features-210"></a>Características 2.1.0 ###

- [Recurso] [AzureHtml5JS] Suporte de anúncios VOD para pré-meados pós-rolos
- [Recurso] [Beta] [AzureHtml5JS] Suporte de anúncios ao vivo para pré-meados pós-rolos
- [Recurso] Adicionou nova opção de pele - AMP-flush
- [Recurso] Adicionou rótulos de ária melhorados para uma melhor integração com leitores de ecrã/tecnologia de assistência
- [Recurso] [Pele] A pele agora mostra todos os ícones e botões claramente em modo de alto contraste

### <a name="bug-fixes-210"></a>Correções de bug 2.1.0 ###

- [Bug Fix] Número de acessibilidades e correções de UI
- [Bug Fix] AMP não carrega corretamente no IE9

### <a name="changes-210"></a>Alterações 2.1.0 ###

- [Mudança] Elementos DOM reestruturados no jogador para acomodar trabalho de anúncios
- [Mudança] Trocado de CSS para SCSS para desenvolvimento da pele
- [Mudança] [Amostras] Amostra adicionada para anúncios VOD
- [Mudança] [Amostras] Amostra adicionada para a velocidade de reprodução
- [Mudança] [Amostras] Amostra adicionada para flush skin

## <a name="200-beta-release"></a>2.0.0 (Versão Beta) ##

- [Alteração] atualizado para VJS5
- [recurso] Adicionou novo fluido API para fluido de resposta ao jogador
- [Recurso] Velocidade de reprodução
- [Mudança] Trocado de CSS para SCSS para pele

## <a name="183-official-hotfix-update"></a>1.8.3 (Atualização Oficial de Hotfix) ##

### <a name="bug-fixes-183"></a>Correções de bug 1.8.3 ###

- [Bug Fix] [AzureHtml5JS] Certos ativos com DTS negativo não reprodução no Chrome

## <a name="182-official-hotfix-update"></a>1.8.2 (Atualização Oficial de Hotfix) ##

### <a name="bug-fixes-182"></a>Correções de bug 1.8.2 ###

- [Bug Fix] [AzureHtml5JS] Bitrates de áudio mais altos não reprodução através de AzureHtml5JS

## <a name="181-official-update"></a>1.8.1 (Atualização Oficial) ##

### <a name="bug-fixes-181"></a>Correções de bug 1.8.1 ###

- [Bug Fix] [iOS] Legendas/legendas não aparecem em jogador nativo
- [Bug Fix] [AMP] URLs de streaming apoiados pela CDN anexados com tokens de autenticação que não estão a jogar
- [Bug Fix] [FairPlay] Código de erro fairPlay em falta De ID técnico (Bits [31-28] do ErrorCode) ver Códigos de Erro para mais detalhes
- [Bug Fix] [Safari] [PlayReady] Conteúdo PlayReady em Safari produzindo infinita spinner

### <a name="changes-181"></a>Alterações 1.8.1 ###

- [Mudança] [Html5] Mude os registos de verbosa tecnológica nativa html5 para conter eventos do VideoTag

## <a name="180-official-update"></a>1.8.0 (Atualização Oficial) ##

### <a name="features-180"></a>Características 1.8.0 ###

- [Características] [DRM] Suporte de FairPlay adicionado (ver [Conteúdo Protegido](azure-media-player-protected-content.md) para mais informações)

### <a name="bug-fixes-180"></a>Correções de bug 1.8.0 ###

- [Bug Fix] [AMP] A procura do utilizador não desencadeia um evento de espera quando a rede é estrangulada
- [Bug Fix] [Flashss] Selecionar qualidade em flash tech lança exceção
- [Bug Fix] [AMP] A qualidade de seleção dinâmica mostra no menu de contexto
- [Bug Fix] [Pele] É difícil selecionar o último item de menu de menus de contexto

### <a name="changes-180"></a>Alterações 1.8.0 ###

- [Mudança] Jogador atualizado para os requisitos emEM do Chrome atuais
- [Mudança] Tecnologia padrãoOrder alterado para acomodar nova tecnologia- html5FairPlayHLS (ver [Conteúdo Protegido](azure-media-player-protected-content.md) para mais informações)
- [Mudança] [AzureHtml5JS] Reprodução ativada do MPEG-Dash no Safari
- [Mudança] [Amostras] Alterações nas amostras multi-DRM para acomodar fairPlay

## <a name="174-official-hotfix-update"></a>1.7.4 (Atualização Oficial de Hotfix) ##

### <a name="bug-fixes-174"></a>Correções de bug 1.7.4 ###

- [Bug Fix] [Chrome] Contorno azul aparece em torno de buscar manivela quando o utilizador tem o contexto do jogador
- [Bug Fix] [IE9] Exceção JavaScript lançada quando o jogador carregou no IE9

## <a name="173-official-hotfix-update"></a>1.7.3 (Atualização Oficial de Hotfix) ##

### <a name="bug-fixes-173"></a>Correções de bug 1.7.3 ###

- [Bug Fix] [AzureHtml5JS] O timing do jogador em redes restritas

### <a name="changes-173"></a>Alterações 1.7.3 ###

- [Mudança] Habilitar webcrypto no Edge para desencriptar conteúdo AES
- [Mudança] Otimizar a heurística amp para contabilizar pedaços em cache
- [Mudança] [AzureHtml5JS] Otimizar heurística reduzindo a latência da estimativa da largura de banda

## <a name="172-official-hotfix-update"></a>1.7.2 (Atualização Oficial de Hotfix) ##

### <a name="features-172"></a>Características 1.7.2 ###
<!---API needs onboarding. Removed link to API until remedied.--->
- [Recurso] [AzureHtml5JS] Firefox] Enable Widevine playback com EME para Firefox 47+
- [Recurso] Adicionar evento para a eliminação do jogador
<!-- ([disposing](index.html#static-amp.eventname.disposing)) -->

### <a name="bug-fixes-172"></a>Correções de bug 1.7.2 ###

- [Bug Fix] Parâmetros de consulta de URL da Akamai codificados não corretamente descodificados
- [Bug Fix] Exceção a ser lançada no manifestoPlayableWindowLength()
- [Bug Fix] O espectador nem sempre pode clicar em reproduzir o vídeo depois de o vídeo ter terminado para reassistir
- [Bug Fix] Tamanho responsivo não conforme com mudanças rápidas no tamanho da janela
- [Bug Fix] [Edge] IE] Tamanho responsivo não entra em vigor na carga da página para largura=x, altura=auto
- [Bug Fix] [Android] Chrome] Chrome pede permissões para reproduzir conteúdo de DRM quando o conteúdo não é encriptado
- [Bug Fix] [Acessibilidade] [Borda] Os controlos de teclado não selecionam corretamente itens de menu de contexto
- [Bug Fix] [Acessibilidade] Fronteira exibida em falta em modo de contraste elevado
- [Bug Fix] [Flashss] Rato-up ouvinte de evento não removido após eliminação do jogador causa exceção
- [Bug Fix] [Flashss] Emitir a parsing manifesto URL com espaços codificados
- [Bug Fix] [iOS] Erro de tipo ao avaliar tech.featuresVolumeControl

### <a name="changes-172"></a>Alterações 1.7.2 ###

- [Mudança] [DRM] Controlos drm deslocados após definir fonte para apenas verificar quando o conteúdo é encriptado
- [Mudança] [AES] Corpo indefinido removido do tipo/planície do pedido de entrega da chave
- [Mudança] [Acessibilidade] Narrador do Windows agora lê "Media Player" quando o contexto está no jogador em vez de propriedades

## <a name="171-official-hotfix-update"></a>1.7.1 (Atualização Oficial de Hotfix) ##

### <a name="features-171"></a>Características 1.7.1 ###

- [Recurso] Opção adicional para perfil Heurístico Híbrido (este perfil é definido por padrão)

### <a name="bug-fixes-171"></a>Correções de bug 1.7.1 ###

- [Bug Fix] O design responsivo não funciona de acordo com o padrão HTML5 (largura=100%, altura=automóvel)
- [Bug Fix] Valores percentuais para largura e altura não se comportam como esperado em v1.7.0

## <a name="170-official-update"></a>1.7.0 (Atualização Oficial) ##

### <a name="features-170"></a>Características 1.7.0 ###
<!---API needs onboarding. Removed link until remedied.--->
- [Recurso] [AzureHtml5JS] [Flashss] Adicionado atuaisMediaTime() para obter o tempo de mídia codificador do tempo atual em segundos
- [Recurso] [Flashss] ApIs de telemetria de descarregamento implementado com videoBufferData() e audioBufferData()<!-- (see [BufferData](index.html#amp.bufferdata) for more details) -->
- [Recurso] [Flashss] Acrescentou evento 'downloadbitratechanged'
- [Recurso] Tempo de carregamento melhorado em comparação com versões mais antigas do jogador
- [Recurso] Erros são registados na consola JavaScript

### <a name="bug-fixes-170"></a>Correções de bug 1.7.0 ###

- [Bug Fix] URL de cartaz codificado com parâmetros de corda de consulta que não exibem no leitor
- [Bug Fix] Exceção lançada quando não há tecnologia carregada e amplificador API. Player.poster() é chamado
- [Bug Fix] Exceção lançada quando as funções tentam aceder ao jogador após eliminação
- [Bug Fix] [Acessibilidade] Esboço em falta no foco na barra de progresso procurar cabeça
- [Bug Fix] [Acessibilidade] Os menus de contexto têm uma sombra no modo de alto contraste
- [Bug Fix] [iOS] jogador nativo WebVTT legendas reprodução não funciona
- [Bug Fix] [AzureHtml5JS] Error 0x0100002 deve ser mostrado ao reproduzir fluxo HTTP no site HTTPS que, em vez disso, produz infinita spinner como resultado de conteúdo misto
- [Bug Fix] [AzureHtml5JS] Segmento final em falta causando erro de verificação de saúde em loop mostrando um estado de buffering infinito percebido
- [Bug Fix] [AzureHtml5JS] Nome de faixa de áudio incorreto no menu quando useManifestForLabel=falso e códigos de linguagem de três letras são usados
- [Bug Fix] [AzureHtml5JS] Chrome] Perceção de estado tampão infinito no final do conteúdo causado por imprecisão de ponto flutuante na duração com JavaScript no Chrome
- [Bug Fix] [Flashss] Erro intermitente não fatal exibido momentaneamente quando o flash player criou
- [Bug Fix] [Flashss] Falha na reprodução quando os streams de vídeo e áudio usam diferentes escalas de tempo devido à falha de imprecisão de arredondamento com "Fragment url (...) é falhado em gerar FLVTags"
- [Bug Fix] [Flashss] Questões que analisam os urls manifestos com espaços codificados
- [Bug Fix] [Flashss] Falta de verificação para determinar se a versão do Flash player >= 11.4 que causa um erro na reprodução em vez de voltar à próxima tecnologia na techOrder
- [Bug Fix] [Flashss] [AES] Questões que aceitam fichas da AES com sublinhados
- [Bug Fix] [SilverlightSS] OSX] "//" prefixando um manifesto em vez do protocolo (HTTP ou HTTPS) é reconhecido como um ficheiro local que produz infinita roda dentada

### <a name="changes-170"></a>Alterações 1.7.0 ###

- [Mudança] [Flashss] Scripts SWF fundidos ("MSAdaptiveStreamingPlugin-osmf2.0.swf" e "StrobeMediaPlayback.2.0.swf") num único SWF chamado "StrobeMediaPlayback.2.0.swf"
- [Mudança] [Flashss] Propagação atualizada do código de erro para obter códigos de erro mais precisos (ex. 404s resultam agora em 0x30200194 em vez de erro genérico 0x302000000)

## <a name="163-official-hotfix-update"></a>1.6.3 (Atualização Oficial de Hotfix) ##

### <a name="bug-fixes-163"></a>Correções de bug 1.6.3 ###

- [Bug Fix] JavaScript exceção do tempo de execução quando o manipulador de eventos hotkeys é executado após a eliminação do leitor
- [Bug Fix] [Android] [AzureHtml5JS] Nenhuma reprodução no dispositivo móvel usando a rede celular
- [Bug Fix] Forge atualizada para funcionar como web worker para libertar UI

## <a name="162-official-hotfix-update"></a>1.6.2 (Atualização Oficial de Hotfix) ##

### <a name="features-162"></a>Características 1.6.2 ###

- [Recurso] Adicionar linguagens adicionais para localização (ver documentação para mais detalhes)

### <a name="bug-fixes-162"></a>Correções de bug 1.6.2 ###

- [Bug Fix] [IE9-10] Clicar em áreas ao redor do leitor minimizou a janela do navegador devido ao bug IE9/IE10 que minimiza na janela.borrado()
- [Bug Fix] [Flashss] Não aceitar fichas AES com sublinhados

## <a name="161-official-hotfix-update"></a>1.6.1 (Atualização Oficial de Hotfix) ##

### <a name="bug-fixes-161"></a>Correções de bug 1.6.1 ###

- [Bug Fix] [FlashsS] Edge,IE[SilverlightSS] IE] Não pode focar-se em outros elementos ui para entradas ou outros em IE/Edge
- [Bug Fix] Reprodução da AES falha quando forja indefinida
- [Bug Fix] [Android] [AzureHtml5JS] Chrome] Spinner contínuo não reproduzir conteúdo quando em loop de verificação de saúde
- [Bug Fix] [IE9] console.log() não suportado pela IE 9 causando exceção

## <a name="160-official-update"></a>1.6.0 (Atualização Oficial) ##

### <a name="features-160"></a>Características 1.6.0 ###

- [Recurso] redução de tamanho de 33% de azuremediaplayer.min.js
- [Recurso] [AzureHtml5JS] Borda][Não testado] Suporte para streams de áudio DD+ em Edge (sem comutação de codec após escolha inicial). A aplicação deve selecionar o fluxo de áudio correto neste momento.
- [Recurso] Controlos de chaves quentes (ver docs para mais detalhes)
- [Recurso] Dica de tempo de progresso paira para procura precisa de tempo
- [Recurso] Permitir a deteção assync de plugins se o método configuraçãoDone existir no plugin

### <a name="bug-fixes-160"></a>Correções de bug 1.6.0 ###

- [Bug Fix] Registo de memória não está a descarregar no getMemoryLog (verdadeiro)
- [Bug Fix] Caixa de seleção bitrate repõe no movimento do rato causando problema seleção de bitrates mais baixos através do controlo do rato
- [Bug Fix] Mac Office em falha na aplicação ao realizar verificação de DRM
- [Bug Fix] As classes CSS são facilmente substituídas acidentalmente
- [Bug Fix] [Chrome] A identificação da atualização do navegador de cordas do agente utilizador é Edge
- [Bug Fix] [AzureHtml5JS] Botão de legendas não aparecer na barra de ferramentas em Edge (Win10) ou Chrome (Mac)
- [Bug Fix] [Android] [AzureHtml5JS] Chrome] InvalidStateError exceção no fim do OfStream() chamada em vídeos curtos
- [Bug Fix] [Firefox] Remoção do aviso de DRM causado pelo Firefox ao verificar as capacidades do navegador
- [Bug Fix] [Html5] Legenda/Legendas não mostradas com conteúdo progressivo mp4
- [Bug Fix] [Flashss] Mensagens com carimbos de tempo correspondentes foram registadas por ordem inversa
- [Bug Fix] [Acessibilidade] [Cromado] Firefox] Tab e selecione controlos automaticamente selecione o item do primeiro menu
- [Bug Fix] [Acessibilidade] Separador para controlar o botão de volume

### <a name="changes-160"></a>Alterações 1.6.0 ###

- [Mudança] Use o tempo de desencriptação da AES na seleção de nível de qualidade
- [Mudança] Atualizar reescritor url para usar HLS v4 antes hlS v3 para streams multi-áudio
- [Mudança] Definir nativeControlsForTouch para falso como padrão (deve ser falso para funcionar corretamente)

## <a name="150-official-update"></a>1.5.0 (Atualização Oficial) ##

### <a name="features-150"></a>Características 1.5.0 ###

- [Recurso] Melhorias para a segurança geral da web (prevenção da injeção, XSS, etc.)
- [Recurso] Ganchos de integração de plugin sDN para evento de origem e opções.sdn
- [Recurso] Tratamento de robustez de erros 5XX e 4XX durante a reprodução

### <a name="bug-fixes-150"></a>Correções de bug 1.5.0 ###

- [Bug Fix] Atualizar a minificação cSS para usar códigos de fonte da entidade HTML para botões em vez de Unicode
- [Bug Fix] [AzureHtml5JS] Conteúdo multi-DRM sempre selecionando o símbolo do primeiro elemento da protecçãoInfo fazendo com que o segundo DRM falhe
- [Bug Fix] [AzureHtml5JS] Procurar nunca completa quando se procura numa área com segmentos em falta.
- [Bug Fix] [AzureHtml5JS] Edge] Ativar a atualização em emedge pré-fixa em Edge para reprodução PlayReady
- [Bug Fix] [AzureHtml5JS] Firefox] Atualização EME check para permitir que Firefox v42+ (com MSE) caia para Silverlight para obter conteúdo protegido
- [Bug Fix] [Flashss] Atualizar erro.mensagem do número para a cadeia detalhada

### <a name="changes-150"></a>Alterações 1.5.0 ###

- [Mudança] Os cartazes atualmente funcionam apenas como URLs absolutos.

## <a name="140-official-update"></a>1.4.0 (Atualização Oficial) ##

### <a name="features-140"></a>Características 1.4.0 ###

- [Recurso] [AzureHtml5JS] Chrome] Suporte simples de DRM widevine
- [Recurso] [AzureHtml5JS] Tratamento de robustez de 404/412 erros durante a reprodução

### <a name="bug-fixes-140"></a>Correções de bug 1.4.0 ###

- [Bug Fix] [Flashss] Melhoria para validação de parâmetros

## <a name="130-official-update"></a>1.3.0 (Atualização Oficial) ##

### <a name="features-130"></a>Características 1.3.0 ###

- [Recurso] [AzureHtml5JS] [Flashss] Troca de áudio do mesmo conteúdo multi-áudio codec

### <a name="bug-fixes-130"></a>Correções de bug 1.3.0 ###

- [Bug Fix] [AzureHtml5JS] Chrome] Spinner infinito intermitente
- [Bug Fix] [AzureHtml5JS] IE][Windows Phone] Exceção que faz com que o Windows Phone tenha problemas de reprodução
- [Bug Fix] [Flashss] Reprodução automática definida para falhas falsas para instâncias adicionais
- [Bug Fix] Questões de tamanho do menu UI

## <a name="120-official-update"></a>1.2.0 (Atualização Oficial) ##

### <a name="features-120"></a>Características 1.2.0 ###

- [Recurso] [AzureHtml5JS] Firefox] Suporte quando mSE está ativado
- [Recurso] Já não é necessário que a app forneça caminhos para binários tecnológicos de recuo (swf, xap). O caminho é relativo ao script Azure Media Player.

### <a name="bug-fixes-120"></a>Correções de bug 1.2.0 ###

- [Bug Fix] [AzureHtml5JS] Chrome] Jogador deriva atrás da borda ao vivo quando jogador no fundo
- [Bug Fix] [AzureHtml5JS] Edge] Ecrã completo não funciona
- [Bug Fix] [AzureHtml5JS] A exploração madeireira não foi ativada corretamente quando definida em opções
- [Bug Fix] [Flash] Tanto "buffering" como buffering icon show durante o evento de espera
- [Bug Fix] Permitir que a reprodução continue se o pedido inicial de largura de banda falhar
- [Bug Fix] Jogador não carrega quando inicializado com opções indefinidas
- [Bug Fix] Ao tentar eliminar o leitor depois de já estar eliminado, ocorre uma exceção vdata
- [Bug Fix] Ícones de barras de qualidade mapeados incorretamente

## <a name="111-official-hotfix-update"></a>1.1.1 (Atualização Oficial de Hotfix) ##

### <a name="bug-fixes-111"></a>Correções de bug 1.1.1 ###

- [Bug Fix] Edição de tela completa IE mais antiga
- [Bug Fix] Plugins já não está sobreescrito

## <a name="110-official-update"></a>1.1.0 (Atualização Oficial) ##

### <a name="features-110"></a>Características 1.1.0 ###

- [Recurso] Atualizar as cordas de localização ui

### <a name="bug-fixes-110"></a>Correções de bug 1.1.0 ###

- [Bug Fix] Big Play Button não tem contraste suficiente
- [Bug Fix] Indicador de foco do separador visual
- [Bug Fix] Selecione menu Bitrate agora usando informações corretas de resolução
- [Bug Fix] Mais opções menu agora dinamicamente dimensionado
- [Bug Fix] Várias questões de UI

## <a name="100-official-release"></a>1.0.0 (Lançamento Oficial) ##

### <a name="features-100"></a>Características 1.0.0 ###

- [Recurso] Testes básicos de acessibilidade para controlo de separadores, controlo de foco, leitor de ecrã, UI de alto contraste
- [Recurso] UI atualizado
- [Recurso] Dev logging
- [Recurso] API para definir dinamicamente legendas/legendas
- [Recurso] Características básicas da localização
- [Recurso] Consolidação de código de erro entre tecnologias
- [Recurso] Novo código de erro para quando os plugins (como Flash ou Silverlight) não são instalados
- [Recurso] [AzureHtml5JS] Implementados eventos básicos de diagnóstico

### <a name="bug-fixes-100"></a>Correções de bug 1.0.0 ###
<!---What is that actually supposed to say?--->
- [Bug Fix] [AzureHtml5JS] Congelamento de reprodução ao vivo em atualizações de MPD quando há pequenas imprecisões no carimbo de tempo
- [Bug Fix] [AzureHtml5JS] Atenuado vários problemas de reprodução ao vivo
- [Bug Fix] [AzureHtml5JS] Lave os amortecedores quando a heurística do tamanho da janela está ligado e vá para um ecrã de resolução mais alto
- [Bug Fix] [AzureHtml5JS] O Chrome mostra agora devidamente o evento terminado. Ligado à edição anterior conhecida do *Chrome não enviará evento sem problemas ao utilizar o AzureHtml5JS. Existe um problema no navegador subjacente.*
- [Bug Fix] [AzureHtml5JS] Safari desativado para esta tecnologia de forma a abordar o problema da Reprodução com a *OSX Yosemite com a tecnologia AzureHtml5JS. Existem questões de implementação da MSE. Mitigação Temporária: força flashSS, silverlightSS como ordem tecnológica para estes agentes utilizadores*
- [Bug Fix] [FlashSS] loadstart disparado após erro ocorreu

## <a name="020-beta"></a>0.2.0 (Beta) ##

### <a name="features-020"></a>Características 0.2.0 ###

- [Recurso] Teste completo para PlayReady e AES para a pedido e ao vivo - ver matriz de compatibilidade
- [Recurso] Manuseamento de descontinuidades
- [Recurso] Suporte para selos temporais superiores a 2^53
- [Recurso] Parâmetro de consulta de URL persiste ao pedido manifesto
- [Recurso] [Não testado] Perfis de apoio `QuickStart` e `HighQuality` heurística
- [Recurso] [Não testado] Expondo informações sobre fluxode vídeo para bitrates, largura e altura em AzureHtml5JS e FlashSS
- [Recurso] [Não testado] Selecione Bitrate em AzureHtml5JS e FlashSS (ver documentação API)

### <a name="bug-fixes-020"></a>Correções de bug 0.2.0 ###

- [Bug Fix] grande botão de reprodução agora visível em WP8.1
- [Bug Fix] corrigido vários problemas de reprodução ao vivo
- [Bug Fix] botão de unmute agora funciona na UI
- [Bug Fix] experiência de carregamento ui atualizada para o modo de reprodução automática
- [Bug Fix] Problema do carregador AMD e definir conflitos de métodos
- [Bug Fix] WP 8.1 Problema de carregamento de aplicativos da Cordova
- [Bug Fix] As consultas de conteúdo protegidos plataforma/tecnologia suportadas ProtectionType para selecionar a tecnologia apropriada para reprodução.  Correções anteriormente conhecidas edição de '_PlayReady conteúdo no Chrome (desktop) / Safari 8 (no OSX Yosemite) atualmente não recua para_o jogador Silverlight '
- [Bug Fix] exceção não apanhada no WinServer 2012 R2 devido à Media Foundation não instalada nessa máquina por padrão.  Tente utilizar APIs de identificação de vídeo HTML, que não são implementados, lançando assim um erro. A mitigação atual é apanhar esse erro e devolver falso em vez de lançar o erro.
- [Bug Fix] obtenha sempre o segmento de entrada após procurar ou http falha para prevenir falhas durante a reprodução
- [Bug Fix] desligue o rastreio de progresso simulado e atualizações de tempo quando o Erro ocorrer.
- [Bug Fix] remover o menu de clique certo
- [Bug Fix] [Mensagem de erro AzureHtml5JS] não sendo exibida quando um conjunto de fichas inválido para conteúdo PlayReady
- [Bug Fix] [AzureHtml5JS] ir ao ecrã inteiro durante a reprodução ao vivo não estava tendo em conta a heurística do tamanho da janela
- [Bug Fix] [Flashss] Removido Strobe Media Player mostrou mensagens para que apenas as mensagens do Azure Media Player sejam mostradas
- [Bug Fix] [SilverlightSS] não ser 'procurado' evento quando procuramos além da duração ou inferior a 0

## <a name="010-beta-release"></a>0.1.0 (Versão Beta) ##

Pré-lançamento inicial

## <a name="next-steps"></a>Próximos passos ##

- [Azure Media Player Quickstart](azure-media-player-quickstart.md)
