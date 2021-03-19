---
title: Azure Media Player Changelog
description: Alterlog de mudança do Azure Media Player.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: overview
ms.date: 09/23/2020
ms.openlocfilehash: c63a8efc4bbcf6e5a124d439e6f9f91397e2fa53
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "92370581"
---
# <a name="changelog"></a>Registo de alterações

## <a name="236-official-update-september-21-2020"></a>2.3.6 (Atualização Oficial 21 de setembro de 2020)

### <a name="features-236"></a>Características 2.3.6

Suporte áudio adicionado apenas para a tecnologia azureHtml5JS (DASH) Suporte ao início tardio da transcrição ao vivo Apoiar a mudança de linguagem na transcrição ao vivo

### <a name="bug-fixes-236"></a>Correções de Erros 2.3.6

Ao utilizar "playinline" em reprodução HLS em dispositivos Apple, clicar no botão "LIVE" faz com que o vídeo reinicie a imagem do cartaz AMP por vezes causa uma exceção O botão de volume estava em falta ao reproduzir HLS FairPlay [Acessibilidade] Dicas de ferramentas não definidas para botões quando o teclado é utilizado [Acessibilidade] A relação de luminosidade é inferior a 1.3:1 para a barra de progresso [Acessibilidade] O foco do teclado às vezes não volta ao botão de qualidade de vídeo [Acessibilidade] Os controlos não são visíveis no Vídeo tela, impedindo o Narrador de encontrá-los

### <a name="changes-236"></a>Alterações 2.3.6

Devolva erros significativos de KeyDelivery a aplicações de chamadas

## <a name="235-official-update-june-1-2020"></a>2.3.5 (Atualização Oficial 1 de junho de 2020)

### <a name="bug-fixes-235"></a>Correções de Erros 2.3.5

- [Acessibilidade] O ouvinte chave Esc no painel de opções é anexado ao documento
- [Acessibilidade] Evite que a UI do jogador desapareça se a barra de controlo ou o menu de opções contiverem foco
- A barra de controlo mostra o tempo do relógio de parede incorreto quando as definições do relógio de parede estão ativadas

### <a name="changes-235"></a>Alterações 2.3.5

- Mensagem de erro adicionada para código de erro 0x00400005 e documentá-la

## <a name="234-official-update-march-4-2020"></a>2.3.4 (Atualização Oficial 4 de março de 2020)

### <a name="bug-fixes-234"></a>Correções de Erros 2.3.4

- Não é possível definir PlayReady overrideLicenseAcquistionUrl
- Incapaz de reproduzir alguns conteúdos com descontinuidades
- [Acessibilidade] Valor do atributo ID para alerta de leitor de ecrã deve ser único
- [Acessibilidade] Enquanto navega na caixa de diálogo de definições de legendas fechadas, o foco sai da caixa de diálogo

### <a name="changes-234"></a>Alterações 2.3.4

- Log Content-Length após um download bem sucedido para ajudar a analisar erros de desencriptação 2.3.3 (Atualização Oficial 12 de novembro de 2019)

### <a name="features-234"></a>Características 2.3.4

- Suporte adicionado para exibir o relógio de parede do relógio de um vídeo como uma sobreposição, e na barra de controlo

### <a name="bug-fixes-234"></a>Correções de Erros 2.3.4

- O interruptor de faixa de áudio funciona mas o erro de saídas no IE11 e no Windows7 "O objeto não suporta propriedade ou método 'habilitado'"
- O interruptor de faixa de áudio falha quando o tampão está totalmente carregado
- O interruptor de faixa de áudio falha quando o utilizador faz uma pausa no vídeo e alterna entre faixas de áudio muito rapidamente
- [Acessibilidade] Dicas de ferramentas não definidas para Controlo de Vídeo em Video Player
- Botões de volume em falta no Html5 dependendo de quando 'loadstart' é recebido
- [Acessibilidade] Não há como definir o texto alt para a imagem do cartaz
- [Acessibilidade] Foco de aplicação perdido após selecionar 'Feito' na caixa de diálogo de definições de legendas
- [Acessibilidade] Os atributos ARIA incorretos são definidos para 'vídeo' em 'pré-visualização de segmentos'

### <a name="changes-234"></a>Alterações 2.3.4

- Removido rótulo/faixa de legenda vazia ao reproduzir HLS no iOS e macOS Safari
- Reduziu o número de 412 s para legendas IMSC1
- Aviso de saída na consola para 10 respostas de legendas IMSC1 vazias consecutivas para ajudar a depurar ao vivo

## <a name="232-official-update-october-9-2019"></a>2.3.2 (Atualização Oficial 9 de outubro de 2019)

### <a name="features-232"></a>Características 2.3.2

-Suporte PlayReady adicionado para reprodução DASH para navegador Chromium Edge

### <a name="bug-fixes-232"></a>Correções de Erros 2.3.2

- A velocidade de reprodução atual não é mostrada visualmente no menu de velocidade de reprodução, a menos que o utilizador o defina manualmente
- [Acessibilidade] Painel de 'Definições' não está a entrar em colapso com a chave 'Esc'
- [Acessibilidade] A chave de atalho amp 'M' não funciona quando o Narrador está ligado

### <a name="changes-232"></a>Alterações 2.3.2

- Para navegadores que não suportam código de áudio E-AC3, as faixas de áudio E-AC3 estão escondidas no menu da faixa de áudio
- Para navegadores que suportam o codec de áudio E-AC3, uma faixa de áudio E-AC3 é selecionada por padrão
- Para os navegadores que não suportam a comutação de códigos de áudio, as faixas de áudio com um código diferente da faixa selecionada estão escondidas no menu da faixa de áudio

## <a name="231-official-update-august-12-2019"></a>2.3.1 (Atualização Oficial 12 de agosto de 2019)

### <a name="features-231"></a>Características 2.3.1

- Sinalizar um evento quando as caixas emsg são recebidas na reprodução do DASH -Suporte adicionado para mostrar faixas de áudio ec-3 no menu áudio nos navegadores que suportam ec-3 e permitem mudar a faixa de áudio de aac para ec3 e vice-versa apenas no navegador Edge baseado em Chromimum

### <a name="bug-fixes-231"></a>Correções de Erros 2.3.1

- O menu de faixa sonora é corrompido após a remoção das faixas ec-3
- O tempo atual pode ser grande do que a duração do vídeo
- Definir a velocidade de reprodução através da velocidade inicialS não funciona
- Às vezes, depois de uma procura, o jogador parece preso
- On Edge e IE em um ecrã tátil, depois de fazer zoom em uma página, pressionar ou pairar sobre a barra de busca não obter com precisão o segmento correto do vídeo
- [Acessibilidade] A etiqueta Aria para Play/Pause não é descritiva para o segmento ao vivo do leitor de vídeo Map não encontrou erro para flashSS para o erro correto do amplificador
- [Acessibilidade] As funções de Aria utilizadas para Play/Pause devem estar em conformidade com valores válidos (.vjs-text-track-display)
- [Acessibilidade] Certos papéis da ARIA devem ser contidos por pais particulares
- [Acessibilidade] Não existe uma ponta de ferramenta definida para o botão de reprodução/pausa das legendas IMSC1 do leitor de vídeo pode desaparecer depois de procurar dentro do atual tampão de vídeo/áudio

### <a name="changes-231"></a>Alterações 2.3.1

- Ao obter um segmentoDecryptError e o jogador já está na borda ao vivo, o jogador agora refresca o manifesto em vez de experimentar o próximo segmento
- Adicione mais registos para diagnóstico
- Documentação atualizada para incluir suporte FairPlay para iOS Safari
- Acrescentou um exemplo para o "srclang" da opção IMSC1
- Acolchoamento adicionado, textPadding, boxShadow sobreposições para faixas de texto.
- Adicionei um código de erro (0x0020025B) para diferenciar que o download do segmento falhou por não ter internet em vez de apenas lançar 0x00200259

## <a name="230-official-release-april-30-2019"></a>2.3.0 (Lançamento Oficial 30 de abril de 2019)

### <a name="features-230"></a>Características 2.3.0

- Suporte adicional para legendas IMSC1 para DASH
- Apoio adicional para ativos apenas em vídeo para o DASH
- Apresentação APITimeOffsetInSec

### <a name="bug-fixes-230"></a>Correções de Erros 2.3.0

- O perfil de heurística da AMP LowLatency interfere com a reprodução de vídeo iOS "mudo" e "unmute" para algumas línguas têm traduções erradas
- O valor aria-valornow do slider da barra de progresso é por vezes incorreto
- O valor da função ária do ecrã da faixa de texto está incorreto

### <a name="changes-230"></a>Alterações 2.3.0

- Os registos agora incluem o tamanho de fragmentos de mídia descarregados
- Suporte removido IE 9 e IE 10
- Amostra cea708 atualizada para mostrar legendas alinhadas à esquerda
- Incluir MediaError.message em registos para falhas de reprodução

## <a name="224-official-update-february-22-2019"></a>2.2.4 (Atualização Oficial 22 de fevereiro de 2019) ##

### <a name="bug-fixes-224"></a>Correções de Erros 2.2.4 ###

- [Bug Fix] [AMP] [Acessibilidade] Removido um separador fantasma acessível quando o ecrã de erro aparece
- [Bug Fix] [AMP] Fixo o 'M' de hotkey para IE11 e Edge
- [Bug Fix] [AMP] Fixou uma exceção para as legendas CEA708
- [Bug Fix] [AMP] Corrigiu um problema de congelamento de vídeo para o navegador Edge

### <a name="changes-224"></a>Alterações 2.2.4 ###

- [Mudança] [AMP] Quando um erro de desencriptação de fragmentos acontece, o jogador retrição corrente e vários fragmentos para recuperar a reprodução
- [Mudança] [AMP] Tornou a AMP mais tolerante a sobrepor-se a fragmentos de vídeo ou áudio

## <a name="223-official-update-january-9-2019"></a>2.2.3 (Atualização Oficial 9 janeiro 2019) ##

### <a name="features-223"></a>Características 2.2.3 ###

- [Recurso] [HLS] Adicionei o menu de faixa sonora para a reprodução do Safari HLS

### <a name="bug-fixes-223"></a>Correções de Erros 2.2.3 ###

- [Bug Fix] [AMP] [Acessibilidade] Durante as reprodução de transmissão ao vivo, o botão "live" não pode ser selecionado utilizando o teclado
- [Bug Fix] [AMP] Corrigimos falsos positivos 0x0400003 erros devido a teste mse falhado
- [Bug Fix] [AMP] Corrigiu um problema onde o vídeo podia congelar ao iniciar uma transmissão em direto

### <a name="changes-223"></a>Alterações 2.2.3 ###

- [Mudança] [AMP] Adicione mais informação no registo para permitir melhores diagnósticos
- [Mudança] [AMP] Quando mais de um bitrate está disponível na mesma resolução de ecrã, todos os bitrates estão disponíveis para seleção

## <a name="222-official-update"></a>2.2.2 (Atualização Oficial) ##

### <a name="bug-fixes-222"></a>Correções de Erros 2.2.2 ###

- [Bug Fix] [AMP] Quando o jogador encontra uma falha de rede transitória, para imediatamente a reprodução
- [Bug Fix] [AMP] [Acessibilidade] O diálogo de erro não está acessível por teclado
- [Bug Fix] [AMP] Spinner infinito exibido ao reproduzir apenas ativo áudio em vez de erro não suportado

### <a name="changes-222"></a>Alterações 2.2.2 ###

- [Mudança] [AMP] acrescentou cordas localizadas para publicidade UI

## <a name="221-official-update"></a>2.2.1 (Atualização Oficial) ##

### <a name="features-221"></a>Características 2.2.1 ###

- [Recurso] [CMAF] Apoio adicional ao HLS CMAF

### <a name="bug-fixes"></a>Correções de Erros ###

- [Bug Fix] [AMP] temporizadores desacanados na lógica de recandidologia que produz erros de reprodução
- [Bug Fix] [AMP] [Firefox] evento terminado não é disparado sobre Firefox e Chrome quando parou o programa ao vivo
- [Bug Fix] [AMP] Controlos apresentados após setsource, mesmo quando os controlos são definidos como falsos nas opções dos jogadores

### <a name="changes"></a>Alterações ###

- [Mudança] [Legenda ao vivo] Mudou o nome API para legendas CEA de 608 para 708. Para mais informações, consulte [as Definições de Legendas CEA708](/javascript/api/azuremediaplayer/amp.player.cea708captionssettings)-->

## <a name="220-official-release"></a>2.2.0 (Lançamento Oficial) ##

### <a name="features-220"></a>Características 2.2.0 ###

- [Recurso] [Azurehtml5JS] [Flash] [LiveCaptions] Suporte de legendas CEA 708 na tecnologia Azurehtml5JS e FlashSS para conteúdos claros e AES.

### <a name="bug-fixes-220"></a>Correções de Erros 2.2.0 ###

- [Bug Fix] Deteção de versão flash não funcionando no Chrome/Edge

### <a name="changes-220"></a>Alterações 2.2.0 ###

- [Mudança] [AMP] [Heurística] Mudou o nome do perfil heurístico de QuickStartive para LowLatency
- [Mudança] [Flash] Altere o leitor Flash para a deteção de versão para permitir a reprodução de conteúdos AES com a nova atualização Adobe Flash.

## <a name="219-official-hotfix"></a>2.1.9 (Hotfix Oficial) ##

### <a name="bug-fixes-219"></a>Correções de Erros 2.1.9 ###

- [Bug Fix] [Ao vivo] Exceção que ocorre quando os streams ao vivo transitam para vídeo a pedido/arquivos ao vivo

### <a name="changes-219"></a>Alterações 2.1.9 ###

- [Mudança] [Flash] [AES] Lógica tecnológica flash modificada para não usarbytearrays compartilhados para desencriptação AES, uma vez que a Adobe bloqueou o uso a partir do Flash 30. Por favor, note que a reprodução só funcionará quando a Adobe implementar uma nova versão do Flash devido a um bug em V30. Por favor, veja [questões conhecidas](azure-media-player-known-issues.md) para mais detalhes

## <a name="218-official-update"></a>2.1.8 (Atualização Oficial) ##

### <a name="bug-fixes-218"></a>Correções de Erros 2.1.8 ###

- [Correção de bugs] Spinner ocasionalmente não mostra post procura e pré-jogo
- [Bug Fix] Jogador não começa silenciado quando a opção silenciada ativa
- [Bug Fix] O deslizador de volume é apresentado quando os controlos são definidos como falsos
- [Bug Fix] Reprodução ocasionalmente repetindo quando o utilizador salta para a borda ao vivo
- [Bug Fix] [Firefox] Jogador ocasionalmente lança exceção JavaScript na carga
- [Bug Fix] [Acessibilidade] Botão de reprodução/ pausa/volume perde o esboço de foco quando selecionado usando controlos de teclado
- [Bug Fix] Fugas de memória fixas no jogador são eliminadas
- [Bug Fix] Chamada src() após erros de jogador fora não repõe a fonte
- [Bug Fix] [Ao vivo] A AMP encontra-se em estado de carregamento constante quando o utilizador clica no botão Live após a transmissão ter terminado
- [Bug Fix] [Cromo] O jogador pendura e a reprodução falha quando o navegador minimiza o fundo.

### <a name="changes-218"></a>Alterações 2.1.8 ###

- [Mudança] Atualizado 0x0600001 erro de exibição quando o conteúdo AES é reproduzido com o Flash 30, uma vez que não é suportado neste momento. Por favor, veja [questões conhecidas](azure-media-player-known-issues.md) para mais detalhes
- [Mudança] Adicionou mais retréis para cenários ao vivo quando pedidos manifestos 404 ou devolve manifestos vazios.

## <a name="217-official-update"></a>2.1.7 (Atualização Oficial) ##

### <a name="features-217"></a>Características 2.1.7 ###

- [Recurso] [AzureHtml5JS] Opção de configuração adicionada para descarregar dados em vigor no tampão de origem dos meios de comunicação

### <a name="bug-fixes-217"></a>Correções de Erros 2.1.7 ###

- [Bug Fix] [Acessibilidade] [Leitor de Ecrã] Removido o cabeçalho em branco do jogador incluído quando o título não está definido
- [Bug Fix] [UWA] AMP lança exceção quando reproduze na Universal Windows App
- [Bug Fix] [OSX] setActiveTextTrack() não trabalha no Safari em OSx
- [Bug Fix] [Ao vivo] Clicar na borda ao vivo depois de eliminar e re inicializar a exceção dos rendimentos dos jogadores
- [Bug Fix] [Pele] Tempo atual truncado para certos ativos
- [Bug Fix] [DRM] correção incluída para suportar reprodução em navegadores que suportam vários CENC DRM

### <a name="changes-217"></a>Alterações 2.1.7 ###

- [Mudança] [Amostras] [Acessibilidade] Etiqueta linguística adicionada a todas as amostras

## <a name="216-official-update"></a>2.1.6 (Atualização Oficial) ##

### <a name="bug-fixes-216"></a>Correções de Erros 2.1.6 ###

- [Bug Fix] AMP apresentando duração incorreta para o ativo específico
- [Bug Fix] [FairPlay-HLS] Erros de fairplay que não se propagam à UI
- [Bug Fix] Propriedades heurísticas personalizadas sendo ignoradas em AMP 2.1.5.

### <a name="changes-216"></a>Alterações 2.1.6 ###

- [Mudança] [FairPlayDRM] Removido o tempo limite para o pedido de Cert e pedido de licença para FairPlay de modo a manter a paridade com as implementações do PlayReady e da Widevine
- [Mudança] Misc Melhorias heurísticas para combater o conteúdo desfocado

### <a name="features-216"></a>Características 2.1.6 ###

- [Recurso] Formato mpd-cmaf de suporte adicionado

## <a name="215-official-hotfix"></a>2.1.5 (Hotfix Oficial) ##

### <a name="bug-fixes-215"></a>Correções de Erros 2.1.5 ###

- [Bug Fix] [Legendas] Estilo VTT não renderizado corretamente pelo jogador
- [Bug Fix] [Acessibilidade] Botão ao vivo não tem rótulo de ária

## <a name="214-official-update"></a>2.1.4 (Atualização Oficial) ##

### <a name="bug-fixes-214"></a>Correções de Erros 2.1.4 ###

- [Bug Fix] [Acessibilidade] [Foco] Os utilizadores não podem tabar para se concentrar em botões personalizados adicionados à direita do botão de ecrã completo na barra de controlo
- [Bug Fix] [IE11] [Barra de volume] A tacagem para o pop-up de volume faz com que todo o ecrã de vídeo flash no IE11 enquanto em modo de ecrã completo
- [Bug Fix] [Pele| Flush] Espaço exibido entre a barra de controlo e o pop-up da barra de volume
- [Bug Fix] [AMP] [Legendas] Faixas antigas incorporadas não são limpas quando a fonte é alterada em um jogador existente
- [Bug Fix] [Acessibilidade] [Narrador] Screen Reader lê o controlo de volume incorretamente
- [Bug Fix] [FlashSS] Play Event ocasionalmente não dispara da tecnologia Flash
- [Bug Fix] [AMP] [Foco] Reprodução/pausa requer dois cliques quando o jogador tem foco e está em modo de ecrã completo
- [Bug Fix] [AMP] [Pele] Duração incorreta exibida na barra de progresso para um ativo específico
- [Bug Fix] [Anúncios] [Ad Butler] O VASTO Parser não lida com ficheiro VAST que não tem evento de progresso
- [Bug Fix] [SDN] [AMP 2.1.1] Problema fixo para suporte plugin SDN da Hive
- [Bug Fix] [Acessibilidade] Narrador lê "Midnight Mute Button" quando o utilizador tem foco no botão de volume

### <a name="changes-214"></a>Alterações 2.1.4 ###

- [Mudança] [Acessibilidade] [Tecnologia de Assistência] Os botões agora têm propriedade ao vivo de aria para melhorar a experiência com a tecnologia de assistência
- [Mudança] [Acessibilidade] [Botão de volume| Narrador:Melhor acessibilidade do botão de volume modificando a funcionalidade de tabbing e o comportamento do slider. Estas alterações facilitam aos utilizadores de teclado a modificação do volume do jogador
- [Mudança] Intervalo do menu de contexto de inatividade aumentado de 3 para 5 segundos
- [Mudança] [Acessibilidade] [Luminosidade] Relação de contraste de luminosidade melhorada nos menus suspensos nas definições de legendas

## <a name="213-official-update"></a>2.1.3 (Atualização Oficial) ##

### <a name="bug-fixes-213"></a>Correções de Erros 2.1.3 ###

- [Bug Fix] [Plugins| Título Sobreposição] O plugin de sobreposição de título lança exceções JS com AMP v2. X+
- [Bug Fix] O evento Source set é enviado para a consola JavaScript mesmo quando o registo é desligado
- [Bug Fix] [Pele] As dicas de tempo do jogador são renderizadas fora do contexto do jogador quando pairam sobre cada barra de duração final
- [Bug Fix] [Acessibilidade] [Leitor de Ecrã] Narrador lê "Regional Landmark" ou "Video Player Region Landmark" quando o espectador tem foco no jogador
- [Bug Fix] [AMP] Não é possível desativar o contorno do jogador via CSS
- [Bug Fix] [Acessibilidade] Não é possível tabar para se concentrar em todo o jogador quando o utilizador está em modo de ecrã completo
- [Bug Fix] [Pele] [Ao vivo] Pele não responde ao texto LIVE localizado em japonês
- [Bug Fix] [Pele] A duração e a hora atual são cortadas quando o fluxo > 60 min -[Bug Fix][iPhone| Live]player mostra texto para o tempo/duração atual na barra de controlo
- [Bug Fix] [AMP] Chamar jogador de heurística APIs rende exceções JavaScript
- [Bug Fix] [Nativo html5|iOS] Propriedade de videotag "playsinline" não se propaga para o jogador
- [Bug Fix] [iOS|iframe] O jogador não pode entrar no ecrã completo no iPhone se o jogador estiver carregado num iframe
- [Bug Fix] [AMP] [Heurística] A AMP opera sempre com perfil híbrido independentemente das opções dos jogadores
- [Bug Fix] [AMP| Win8.1]lança quando hospedado na app Win8.1 com uma webview

### <a name="changes-213"></a>Alterações 2.1.3 ###

- [Mudança] [AMP] Informação adicional do ponto final da CDN no evento FragmentDownloadComplete
- [Mudança] [AMP] [Ao vivo] Latência de streaming ao vivo melhorada e otimizada

## <a name="212-official-hotfix"></a>2.1.2 (Hotfix Oficial) ##

### <a name="bug-fixes-212"></a>Correções de Erros 2.1.2 ####

- [Bug Fix] [Acessibilidade] [Narrador de Janelas] Narrador lê "Progress midnight" quando o utilizador tem contexto de progressão e a hora atual é 0:00
- [Bug Fix] O tamanho do logotipo [Skin]é codificado em código JavaScript
- [Acessibilidade] [HotKeys] Hotkeys não ativados quando o jogador é clicado.

### <a name="changes-212"></a>Alterações 2.1.2 ####

- [Mudança] [Registo] Url manifesto de registo quando o jogador não carrega manifesto

### <a name="features-212"></a>Características 2.1.2 ###

- [Mudança] [Performance] [Otimização] Melhores tempos de carga e arranque do jogador

## <a name="211-official-update"></a>2.1.1 (Atualização Oficial) ##

### <a name="bug-fixes-211"></a>Correções de Erros 2.1.1 ####

- [Bug Fix] [iOS] Definição de Autoplay para falsos rendimentos infinito spinner no Safari para iOS
- [Bug Fix] Procurar um tempo maior do que a duração do conteúdo produz um girador infinito
- [Bug Fix] Hotkeys requerem vários separadores de teclado para obter contexto do jogador para trabalhar
- [Bug Fix] Vídeo congela por alguns segundos após redimensionar o jogador em determinados ativos
- [Bug Fix] Spinner infinito (após procurar completo) quando o utilizador faz múltiplas procura rapidamente
- [Bug Fix] A barra de controlo não é escondida durante a inatividade
- [Bug Fix] A abertura de um webapp que hospeda a AMP pode fazer com que a página web seja carregada duas vezes
- [Bug Fix] Infinito ao reproduzir conteúdo certos ativos via Flash Tech
- [Bug Fix] Menu mais Opções não sendo exibido com plugins de 3ª parte
- [Bug Fix] [Pele| Tube][Live] Dois ícones ao vivo são exibidos quando o jogador está na borda ao vivo de um programa
- [Bug Fix] [Pele] O logotipo não pode ser desativado
- [Bug Fix] [DD+ Conteúdo] Spinner contínuo aparece para os ativos que contêm a faixa de áudio Dolby Digital
- [Bug Fix] O AMP mais recente congela ao trocar faixas de linguagem áudio durante o livestream
- [Bug Fix] desaparecimento de fundo fixo para spinner
- [Bug Fix] Spinner infinito em AES flash amostras estáticas correções de bugs

### <a name="changes-211"></a>Alterações 2.1.1 ####

- [Mudança] Código de erro adicionado para widevine Https requisito: a partir do Chrome v58, o conteúdo de grande enverdeado deve ser carregado/reproduzido através do protocolo, caso contrário a `https://` reprodução falhará.
- [Mudança] Rótulo de ária adicionado para carregar spinner para que a tecnologia assistida possa narrar "video loading" quando o conteúdo está a carregar  

## <a name="210-official-release"></a>2.1.0 (Lançamento Oficial) ##

### <a name="features-210"></a>Características 2.1.0 ###

- [Recurso] [AzureHtml5JS] Suporte de anúncios VOD para pré-pós-rolos
- [Recurso] [Beta] [AzureHtml5JS] Suporte ao vivo para pós-rolos pré-meados
- [Recurso] Nova opção de pele adicionada - AMP-flush
- [Recurso] Adicionou rótulos de ária melhorados para uma melhor integração com leitores de ecrã/tecnologia de assistência
- [Recurso] [Pele] A pele agora mostra todos os ícones e botões claramente no modo de alto contraste

### <a name="bug-fixes-210"></a>Correções de Erros 2.1.0 ###

- [Bug Fix] Número de acessibilidades e correções de UI
- [Bug Fix] AMP não carregar corretamente no IE9

### <a name="changes-210"></a>Alterações 2.1.0 ###

- [Mudança] Elementos do DOM reestruturados no jogador para acomodar o trabalho de anúncios
- [Mudança] Mudado de CSS para SCSS para desenvolvimento da pele
- [Mudança] [Amostras] Amostra adicionada para anúncios VOD
- [Mudança] [Amostras] Amostra adicionada para velocidade de reprodução
- [Mudança] [Amostras] Amostra adicionada para Flush Skin

## <a name="200-beta-release"></a>2.0.0 (Versão Beta) ##

- [Alteração]atualizada para VJS5
- [recurso] Adicionou novo fluido API para fluido de resposta do jogador
- [Recurso] Velocidade de reprodução
- [Mudança] Trocado de CSS para SCSS para pele

## <a name="183-official-hotfix-update"></a>1.8.3 (Atualização Oficial do Hotfix) ##

### <a name="bug-fixes-183"></a>Correções de Erros 1.8.3 ###

- [Bug Fix] [AzureHtml5JS] Certos ativos com DTS negativos não vão reproduzir no Chrome

## <a name="182-official-hotfix-update"></a>1.8.2 (Atualização Oficial do Hotfix) ##

### <a name="bug-fixes-182"></a>Correções de Erros 1.8.2 ###

- [Bug Fix] [AzureHtml5JS] Bitrates de áudio mais altos não vão reproduzir através de AzureHtml5JS

## <a name="181-official-update"></a>1.8.1 (Atualização Oficial) ##

### <a name="bug-fixes-181"></a>Correções de Erros 1.8.1 ###

- [Bug Fix] [iOS] Legendas/legendas não aparecem no jogador nativo
- [Bug Fix] [AMP] URLs de streaming apoiados pelo CDN anexados com fichas de autenticação não reproduzidas
- [Bug Fix] [FairPlay] Código de erro FairPlay em falta Tech ID (Bits [31-28] do ErrorCode) ver Códigos de Erro para mais detalhes
- [Bug Fix] [Safari] [PlayReady] Conteúdo playReady no Safari que produz spinner infinito

### <a name="changes-181"></a>Alterações 1.8.1 ###

- [Mudança] [Html5] Alterar registos nativos de verbose tecnológico html5 para conter eventos a partir de VideoTag

## <a name="180-official-update"></a>1.8.0 (Atualização Oficial) ##

### <a name="features-180"></a>Características 1.8.0 ###

- [Características] [DRM] Suporte FairPlay adicionado (ver [Conteúdo Protegido](azure-media-player-protected-content.md) para mais informações)

### <a name="bug-fixes-180"></a>Correções de Erros 1.8.0 ###

- [Bug Fix] [AMP] Procura de utilizador não despoleta um evento de espera quando a rede é acelerada
- [Bug Fix] [FlashSS] Selecionando qualidade em flash tech lança exceção
- [Bug Fix] [AMP] A qualidade de seleção dinâmica mostra no menu de contexto
- [Bug Fix] [Pele] É difícil selecionar o último item do menu dos menus de contexto

### <a name="changes-180"></a>Alterações 1.8.0 ###

- [Mudança] Jogador atualizado para os requisitos atuais do Chrome EME
- [Mudança] Tecnologia padrão Alterado para acomodar nova tecnologia-html5FairPlayHLS (ver [Conteúdo Protegido](azure-media-player-protected-content.md) para mais informações)
- [Mudança] [AzureHtml5JS] Reprodução de MPEG-Dash ativada no Safari
- [Mudança] [Amostras] Amostras multi-DRM alteradas para acomodar FairPlay

## <a name="174-official-hotfix-update"></a>1.7.4 (Atualização Oficial do Hotfix) ##

### <a name="bug-fixes-174"></a>Correções de Erros 1.7.4 ###

- [Bug Fix] [Cromo] Contorno azul aparece em torno de procurar o cabo quando o utilizador tem contexto de jogador
- [Bug Fix] [IE9] Exceção JavaScript lançada quando o jogador carregado no IE9

## <a name="173-official-hotfix-update"></a>1.7.3 (Atualização Oficial do Hotfix) ##

### <a name="bug-fixes-173"></a>Correções de Erros 1.7.3 ###

- [Bug Fix] [AzureHtml5JS] Timing do jogador fora em redes restritas

### <a name="changes-173"></a>Alterações 1.7.3 ###

- [Mudança] Habilitar webcrypto on Edge para desencriptar conteúdo sonoro
- [Mudança] Otimização da heurística amp para responder a pedaços em cache
- [Mudança] [AzureHtml5JS] Otimizar a heurística reduzindo a latência da largura de banda

## <a name="172-official-hotfix-update"></a>1.7.2 (Atualização Oficial do Hotfix) ##

### <a name="features-172"></a>Características 1.7.2 ###
<!---API needs onboarding. Removed link to API until remedied.--->
- [Recurso] [AzureHtml5JS| Firefox] Ativar a reprodução de Widevine com a EME para o Firefox 47+
- [Recurso] Adicionar evento para a eliminação do jogador
<!-- ([disposing](index.html#static-amp.eventname.disposing)) -->

### <a name="bug-fixes-172"></a>Correções de Erros 1.7.2 ###

- [Bug Fix] Parâmetros de consulta DE URL do CDN da Akamai codificados não corretamente descodificados
- [Bug Fix] Exceção lançada no manifestJolength Descartável()
- [Bug Fix] Espectador nem sempre pode clicar em reproduzir o vídeo depois de o vídeo ter terminado para rever
- [Bug Fix] Dimensionamento responsivo não conforme a mudanças rápidas no tamanho da janela
- [Bug Fix] [Borda| IE] Dimensionamento responsivo não entra em vigor na carga da página para largura=x, altura=auto
- [Bug Fix] [Android| Chrome] Chrome a pedir permissões para reproduzir conteúdo DRM quando o conteúdo não é encriptado
- [Bug Fix] [Acessibilidade] [Borda] Os controlos do teclado não selecionam corretamente os itens do menu de contexto
- [Bug Fix] [Acessibilidade] Falta de borda exibida em modo de alto contraste
- [Bug Fix] [FlashSS] Ouvinte do evento do rato não removido após eliminação do jogador causa exceção
- [Bug Fix] [FlashSS] Emitir URL manifesto de análise com espaços codificados
- [Bug Fix] [iOS] Erro de tipo ao avaliar tech.featuresVolumeControl

### <a name="changes-172"></a>Alterações 1.7.2 ###

- [Mudança] [DRM] Verificações drm movidas após origem definida apenas para verificar quando o conteúdo é encriptado
- [Mudança] [AES] Removido corpo indefinido do tipo/planície do pedido de entrega chave
- [Mudança] [Acessibilidade] O narrador do Windows agora lê "Media Player" quando o contexto está no jogador em vez de propriedades

## <a name="171-official-hotfix-update"></a>1.7.1 (Atualização Oficial do Hotfix) ##

### <a name="features-171"></a>Características 1.7.1 ###

- [Recurso] Opção adicionada para perfil heurístico híbrido (este perfil é definido por padrão)

### <a name="bug-fixes-171"></a>Correções de Erros 1.7.1 ###

- [Bug Fix] O design responsivo não funciona de acordo com o padrão HTML5 (largura=100%, altura=automóvel)
- [Bug Fix] Valores percentuais para largura e altura não se comportarem como esperado em v1.7.0

## <a name="170-official-update"></a>1.7.0 (Atualização Oficial) ##

### <a name="features-170"></a>Características 1.7.0 ###
<!---API needs onboarding. Removed link until remedied.--->
- [Recurso] [AzureHtml5JS] [FlashSS] Adicionado actualMediaTime() para obter o tempo de mídia codifical do tempo atual em segundos
- [Recurso] [FlashSS] Implementado descarregamento de telemetria APIs com videoBufferData() e audioBufferData()<!-- (see [BufferData](index.html#amp.bufferdata) for more details) -->
- [Recurso] [FlashSS] Evento adicional de "downloadbitrateed"
- [Recurso] Tempo de carregamento melhorado em comparação com versões mais antigas do jogador
- [Recurso] Os erros são registados na consola JavaScript

### <a name="bug-fixes-170"></a>Correções de Erros 1.7.0 ###

- [Bug Fix] URL de cartaz codificado com parâmetros de cadeia de consulta não exibidos no jogador
- [Bug Fix] Exceção lançada quando não há tecnologia carregada e amplificador API. Player.poster() é chamado
- [Bug Fix] Exceção lançada quando as funções tentam aceder ao jogador depois de eliminado
- [Bug Fix] [Acessibilidade] Faltando esboço no foco na barra de progresso procurar cabeça
- [Bug Fix] [Acessibilidade] Os menus de contexto têm uma sombra no modo de alto contraste
- [Bug Fix] [iOS] jogador nativo WebVTT legendas reprodução não funcionando
- [Bug Fix] [AzureHtml5JS] O erro 0x0100002 deve ser mostrado ao reproduzir o stream HTTPS no site HTTPS que, em vez disso, produz uma rotação infinita como resultado de conteúdo misto
- [Bug Fix] [AzureHtml5JS] Segmento final em falta causando erro de verificação de saúde looping exibindo um estado de tampão infinito percebido
- [Bug Fix] [AzureHtml5JS] Nome de faixa de áudio incorreto no menu quando utilizarManifestForLabel=falso e três códigos linguísticos de letras são usados
- [Bug Fix] [AzureHtml5JS| Chrome] Percecionou o estado tampão infinito no final do conteúdo causado pela imprecisão do ponto flutuante em duração com o JavaScript no Chrome
- [Bug Fix] [FlashSS] Erro intermitente não fatal exibido momentaneamente quando o flash player criou
- [Bug Fix] [FlashSS] Falha na reprodução quando os streams de vídeo e áudio usam diferentes prazos devido à imprecisão de arredondamento com "Fragmento url (...) não consegue gerar FLVTags"
- [Bug Fix] [FlashSS] Questões de análise de urls manifestos com espaços codificados
- [Bug Fix] [FlashSS] Falta verificar se a versão do flash player >= 11.4 que causa um erro na reprodução em vez de voltar para a próxima tecnologia no techOrder
- [Bug Fix] [FlashSS] [AES] Questões que aceitam fichas AES com sublinhados
- [Bug Fix] [Luz de prata| OSX] Prefixar um manifesto em vez do protocolo (HTTP ou HTTPS) é reconhecido como um ficheiro local que produz uma rotação infinita

### <a name="changes-170"></a>Alterações 1.7.0 ###

- [Mudança] [FlashSS] Scripts SWF fundidos ("MSAdaptiveStreamingPlugin-osmf2.0.swf" e "StrobeMediaPlayback.2.0.swf") num único SWF chamado "StrobeMediaPlayback.2.0.swf"
- [Mudança] [FlashSS] Propagação atualizada do código de erro para obter códigos de erro mais precisos (ex. 404s agora resultam em 0x30200194 em vez de erro genérico 0x30200000)

## <a name="163-official-hotfix-update"></a>1.6.3 (Atualização Oficial do Hotfix) ##

### <a name="bug-fixes-163"></a>Correções de Erros 1.6.3 ###

- [Bug Fix] Exceção do tempo de execução JavaScript quando o manipulador de eventos hotkeys é executado após a eliminação do jogador
- [Bug Fix] [Android] [AzureHtml5JS] Nenhuma reprodução no dispositivo móvel usando rede celular
- [Bug Fix] Forge atualizada para funcionar como web worker para libertar UI

## <a name="162-official-hotfix-update"></a>1.6.2 (Atualização Oficial do Hotfix) ##

### <a name="features-162"></a>Características 1.6.2 ###

- [Recurso] Linguagens adicionais adicionadas para localização (ver documentação para mais detalhes)

### <a name="bug-fixes-162"></a>Correções de Erros 1.6.2 ###

- [Bug Fix] [IE9-10] Clicar em áreas ao redor da janela do navegador minimizada do jogador devido ao bug IE9/IE10 que minimiza na janela.borrão()
- [Bug Fix] [FlashSS] Não aceitar fichas AES com sublinhados

## <a name="161-official-hotfix-update"></a>1.6.1 (Atualização Oficial do Hotfix) ##

### <a name="bug-fixes-161"></a>Correções de Erros 1.6.1 ###

- [Bug Fix] [Flashss| Edge,ie[Silverlightss| IE] Não pode focar-se em outros elementos de UI para entradas ou outros em IE/Edge
- [Bug Fix] Reprodução da AES falha quando forja indefinida
- [Bug Fix] [Android] [AzureHtml5JS| Chrome] Spinner contínuo não reproduzindo conteúdo quando em loop de verificação de saúde
- [Bug Fix] [IE9] consola.log.log.log

## <a name="160-official-update"></a>1.6.0 (Atualização Oficial) ##

### <a name="features-160"></a>Características 1.6.0 ###

- [Recurso] Redução de tamanho de 33% de azuremediaplayer.min.js
- [Recurso] [AzureHtml5JS| Edge[Suporte não testado] Suporte para streams de áudio DD+ em Edge (sem comutação de codec após a escolha inicial). A aplicação deve selecionar o fluxo de áudio correto neste momento.
- [Recurso] Controlos de chaves quentes (ver docs para mais detalhes)
- [Recurso] A ponta do tempo do progresso paira para o tempo preciso de procurar
- [Recurso] Permitir a deteção de assíncos de plugins se o método de configuração Existir no plugin

### <a name="bug-fixes-160"></a>Correções de Erros 1.6.0 ###

- [Bug Fix] Registo de memória não flushing em getMemoryLog (verdadeiro)
- [Bug Fix] Caixa de seleção de bitrate reinicia no movimento do rato causando problema selecionando bitrates inferiores através do controlo do rato
- [Bug Fix] Mac Office em falhas de aplicativos ao realizar verificação de DRM
- [Bug Fix] As aulas de CSS são facilmente substituídas acidentalmente
- [Bug Fix] [Cromo] Atualização da identificação do navegador de cadeia de agente de utilizador é Edge
- [Bug Fix] [AzureHtml5JS] Botão de legendas não aparece na barra de ferramentas em Edge (Win10) ou Chrome(Mac)
- [Bug Fix] [Android] [AzureHtml5JS| Chrome] InvalidStateError exceção no endOfStream() chamada em vídeos curtos
- [Bug Fix] [Firefox] Remoção do aviso drm causado pelo Firefox ao verificar capacidades do navegador
- [Bug Fix] [Html5] Legendas/Legendas não mostradas com conteúdo progressivo de mp4
- [Bug Fix] [FlashSS] Mensagens com tempotais correspondentes foram registadas em ordem inversa
- [Bug Fix] [Acessibilidade] [Cromo| Firefox] Tab e selecione controlos selecione automaticamente o primeiro item do menu
- [Bug Fix] [Acessibilidade] Abas para controlar o botão de volume

### <a name="changes-160"></a>Alterações 1.6.0 ###

- [Mudança] Utilize tempo de desencriptação AES na seleção de nível de qualidade
- [Mudança] Atualizar reescrita de URL para usar HLS v4 antes do HLS v3 para streams multi-áudio
- [Mudança] Desafine o NativeControlsForTouch como padrão (deve ser falso para funcionar corretamente)

## <a name="150-official-update"></a>1.5.0 (Atualização Oficial) ##

### <a name="features-150"></a>Características 1.5.0 ###

- [Recurso] Melhoramentos para a segurança geral da web (prevenção da injeção, XSS, etc.)
- [Recurso] Ganchos de integração de plugin SDN para evento de conjunto de fontes e opções.sdn
- [Recurso] Manipulação de robustez de erros de 5XX e 4XX durante a reprodução

### <a name="bug-fixes-150"></a>Correções de Erros 1.5.0 ###

- [Bug Fix] Atualizar a minificação do CSS para utilizar códigos de fonte de entidade HTML para botões em vez de Unicode
- [Bug Fix] [AzureHtml5JS] Conteúdo multi-DRM sempre selecionando o símbolo do primeiro elemento da proteçãoInfo fazendo com que o segundo DRM falhe
- [Bug Fix] [AzureHtml5JS] Procurar nunca se completa quando procura numa área com segmentos em falta.
- [Bug Fix] [AzureHtml5JS| Edge] Ativar a atualização EME pré-fixa no Edge para reprodução playReady
- [Bug Fix] [AzureHtml5JS| Firefox] Update EME check para permitir que Firefox v42+ (com MSE) recue para Silverlight para obter conteúdo protegido
- [Bug Fix] [FlashSS] Atualizar erro.mensagem do número para a cadeia detalhada

### <a name="changes-150"></a>Alterações 1.5.0 ###

- [Mudança] Os cartazes atualmente só funcionam como URLs absolutos.

## <a name="140-official-update"></a>1.4.0 (Atualização Oficial) ##

### <a name="features-140"></a>Características 1.4.0 ###

- [Recurso] [AzureHtml5JS| Chrome] Suporte drm simples de widevine
- [Recurso] [AzureHtml5JS] Manipulação de robustez de 404/412 erros durante a reprodução

### <a name="bug-fixes-140"></a>Correções de Erros 1.4.0 ###

- [Bug Fix] [FlashSS] Melhoria para validação de parâmetros

## <a name="130-official-update"></a>1.3.0 (Atualização Oficial) ##

### <a name="features-130"></a>Características 1.3.0 ###

- [Recurso] [AzureHtml5JS] [FlashSS] Comutação de áudio do mesmo conteúdo multi-áudio codec

### <a name="bug-fixes-130"></a>Correções de Erros 1.3.0 ###

- [Bug Fix] [AzureHtml5JS| Cromado] Girador infinito intermitente
- [Bug Fix] [AzureHtml5JS| IE][Windows Phone] Exceção que faz com que o Windows Phone tenha problemas de reprodução
- [Bug Fix] [FlashSS] Reprodução automática definida para falhas falsas para casos adicionais
- [Bug Fix] Problemas de dimensionamento de menus de UI

## <a name="120-official-update"></a>1.2.0 (Atualização Oficial) ##

### <a name="features-120"></a>Características 1.2.0 ###

- [Recurso] [AzureHtml5JS| Firefox] Suporte quando o MSE estiver ativado
- [Recurso] Já não é necessário que a aplicação forneça caminhos para binários tecnológicos de retorno (swf, xap). O caminho é relativo ao script do Azure Media Player.

### <a name="bug-fixes-120"></a>Correções de Erros 1.2.0 ###

- [Bug Fix] [AzureHtml5JS| Chrome] Jogador deriva atrás da borda ao vivo quando o jogador no fundo
- [Bug Fix] [AzureHtml5JS| Edge] Ecrã completo não funcionando
- [Bug Fix] [AzureHtml5JS] O registo madeireira não foi devidamente ativado quando definido nas opções
- [Bug Fix] [Flash] Ambos "buffering" e buffering icon show durante o evento de espera
- [Bug Fix] Permitir que a reprodução continue se o pedido inicial de largura de banda falhar
- [Bug Fix] Jogador não carrega quando inicializado com opções indefinidas
- [Bug Fix] Ao tentar eliminar o jogador depois de já ter sido eliminado, ocorre uma exceção vdata
- [Bug Fix] Ícones de barra de qualidade mapeados incorretamente

## <a name="111-official-hotfix-update"></a>1.1.1 (Atualização Oficial do Hotfix) ##

### <a name="bug-fixes-111"></a>Correções de Erros 1.1.1 ###

- [Bug Fix] Problema de ecrã completo IE mais antigo
- [Bug Fix] Plugins já não são substituídos

## <a name="110-official-update"></a>1.1.0 (Atualização Oficial) ##

### <a name="features-110"></a>Características 1.1.0 ###

- [Recurso] Atualizar as cadeias de localização do UI

### <a name="bug-fixes-110"></a>Correções de Erros 1.1.0 ###

- [Bug Fix] Botão big play não tem contraste suficiente
- [Bug Fix] Indicador de foco de separador visual
- [Bug Fix] Selecione menu Bitrate agora usando informações corretas de resolução
- [Bug Fix] Menu de mais opções agora de tamanho dinâmico
- [Bug Fix] Várias questões de UI

## <a name="100-official-release"></a>1.0.0 (Lançamento Oficial) ##

### <a name="features-100"></a>Características 1.0.0 ###

- [Recurso] Testes básicos de acessibilidade para controlo de separadores, controlo de foco, leitor de ecrã, UI de alto contraste
- [Recurso] UI atualizado
- [Recurso] Registo de dev
- [Recurso] API para definição dinâmica de legendas/faixas de legendas
- [Recurso] Características básicas de localização
- [Recurso] Consolidação de código de erro em todas as tecnologias
- [Recurso] Novo código de erro para quando os plugins (como Flash ou Silverlight) não são instalados
- [Recurso] [AzureHtml5JS] Eventos básicos de diagnóstico implementados

### <a name="bug-fixes-100"></a>Correções de Erros 1.0.0 ###
<!---What is that actually supposed to say?--->
- [Bug Fix] [AzureHtml5JS] Congelamento de reprodução ao vivo nas atualizações do MPD quando há pequenas imprecisões na estampação de tempo
- [Bug Fix] [AzureHtml5JS] Mitigou vários problemas de reprodução ao vivo
- [Bug Fix] [AzureHtml5JS] Tampão de descarga quando a heurística do tamanho da janela está acesa e ir para um ecrã de resolução mais alto
- [Bug Fix] [AzureHtml5JS] O Chrome mostra agora o evento encerrado. Ligado à edição anterior conhecida do *Chrome não enviará corretamente o evento terminado quando utilizar o AzureHtml5JS. Há um problema no navegador subjacente.*
- [Bug Fix] [AzureHtml5JS] Safari desativado para esta tecnologia de forma a abordar o *problema de Reprodução com o OSX Yosemite com a tecnologia AzureHtml5JS. Há problemas de implementação da MSE. Mitigação temporária: flashss de força, luz de prataSS como ordem tecnológica para estes agentes utilizadores*
- [Bug Fix] [FlashSS] loadstart disparado após erro ocorrido

## <a name="020-beta"></a>0.2.0 (Beta) ##

### <a name="features-020"></a>Características 0.2.0 ###

- [Recurso] Testes concluídos para PlayReady e AES para a pedido e ao vivo - ver matriz de compatibilidade
- [Recurso] Manipulação de Descontinuidades
- [Recurso] Suporte para estamps de tempo superior a 2^53
- [Recurso] O parâmetro de consulta URL persiste no pedido manifesto
- [Recurso] [Não testado] Perfis de apoio `QuickStart` e `HighQuality` heurística
- [Recurso] [Não testado] Expondo informações de fluxo de vídeo para bitrates, largura e altura em AzureHtml5JS e FlashSS
- [Recurso] [Não testado] Selecione Bitrate em AzureHtml5JS e FlashSS (ver documentação API)

### <a name="bug-fixes-020"></a>Correções de Erros 0.2.0 ###

- [Bug Fix] botão de reprodução grande agora visível em WP8.1
- [Bug Fix] fixou vários problemas de reprodução ao vivo
- [Bug Fix] botão unmute agora funciona na UI
- [Bug Fix] experiência de carregamento atualizado de UI para modo de reprodução automática
- [Bug Fix] Emissão de carregador AMD e definir conflitos de métodos
- [Bug Fix] WP 8.1 Problema de carregamento da App Cordova
- [Bug Fix] Proteção De conteúdo protegido/tech suportada ProtectionType para selecionar a tecnologia adequada para reprodução.  Corrige a edição anterior conhecida de '_PlayReady conteúdo no Chrome (desktop) / Safari 8 (no OSX Yosemite) atualmente não recua para o jogador silverlight_'
- [Bug Fix] exceção não conseguida no WinServer 2012 R2 devido à Media Foundation não instalada naquela máquina por padrão.  Tente utilizar APIs de marca de vídeo HTML, que não são implementados, lançando assim um erro. A mitigação atual é apanhar esse erro e devolver falso em vez de lançar o erro.
- [Bug Fix] obtém sempre o segmento init após procurar ou falhar em evitar falhas durante a reprodução
- [Bug Fix] desligue o rastreio simulado de progresso e data de tempo quando ocorreu erro.
- [Bug Fix] remover menu de clique certo
- [Bug Fix] [AzureHtml5JS] mensagem de erro não sendo exibida quando definido token inválido para o conteúdo playReady
- [Bug Fix] [AzureHtml5JS] ir para o ecrã completo durante a reprodução ao vivo não estava tendo em conta a heurística do tamanho da janela
- [Bug Fix] [FlashSS] Removeu o Strobe Media Player para que apenas mensagens do Azure Media Player sejam mostradas
- [Bug Fix] [SilverlightSS] não recebendo evento 'procurado' quando procuramos além da duração ou menos de 0

## <a name="010-beta-release"></a>0.1.0 (Versão Beta) ##

Pré-lançamento inicial

## <a name="next-steps"></a>Passos seguintes ##

- [Azure Media Player Quickstart](azure-media-player-quickstart.md)
