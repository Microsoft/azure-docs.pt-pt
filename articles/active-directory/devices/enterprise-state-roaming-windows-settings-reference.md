---
title: Referência de definições de roaming do Windows 10 - Azure Ative Directory
description: Definições que serão percorridas ou apoiadas no Windows 10 com ESR
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: reference
ms.date: 02/12/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: na
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6048ee9237640799b7bec37083e607fc74ffb8e4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "85252972"
---
# <a name="windows-10-roaming-settings-reference"></a>Referência das definições de roaming do Windows 10

Segue-se uma lista das definições que serão percorridas ou apoiadas no Windows 10. 

## <a name="devices-and-endpoints"></a>Dispositivos e pontos finais

Consulte a tabela seguinte para obter um resumo dos dispositivos e tipos de conta que são suportados pela estrutura de sincronização, cópia de segurança e restauro no Windows 10.

| Tipo de conta e operação | Ambiente de Trabalho | Móvel |
| --- | --- | --- |
| Diretório Ativo Azure: sincronização |Yes |No |
| Diretório Ativo Azure: backup/restauro |No |No |
| Conta Microsoft: sincronização |Yes |Yes |
| Conta Microsoft: backup/restauro |No |Yes |

## <a name="what-is-backup"></a>O que é reserva?

As definições do Windows geralmente sincronizam por padrão, mas algumas configurações são apenas apoiadas, como a lista de aplicações instaladas num dispositivo. A cópia de segurança é apenas para dispositivos móveis e atualmente não está disponível para utilizadores de Roaming do Estado da Empresa. A cópia de segurança utiliza uma conta Microsoft e armazena as definições e dados da aplicação no OneDrive. Se um utilizador desativar a sincronização do dispositivo utilizando a aplicação Definições, os dados da aplicação que normalmente sincronizam tornam-se apenas cópias de segurança. Os dados de backup só podem ser acedidos através da operação de restauro durante a primeira experiência de execução de um novo dispositivo. As cópias de segurança podem ser desativadas através das definições do dispositivo e podem ser geridas e eliminadas através da conta OneDrive do utilizador.

## <a name="windows-settings-overview"></a>Visão geral das definições do Windows

Os grupos de definições a seguir estão disponíveis para os utilizadores finais para ativar/desativar as definições sincronizadas nos dispositivos do Windows 10.

* Tema: fundo do ambiente de trabalho, azulejo do utilizador, posição da barra de tarefas, etc. 
* Definições do Internet Explorer: histórico de navegação, URLs dactilografado, favoritos, etc. 
* Passwords: Gestor credencial do Windows, incluindo perfis de Wi-Fi 
* Preferências linguísticas: dicionário de ortografia, definições de linguagem do sistema 
* Facilidade de acesso: narrador, teclado no ecrã, ampliação 
* Outras Definições do Windows: consulte os detalhes das definições do Windows
* Definição do navegador Microsoft Edge: favoritos do Microsoft Edge, lista de leitura e outras definições

![Sincronizar as suas definições](./media/enterprise-state-roaming-windows-settings-reference/active-directory-enterprise-state-roaming-syncyoursettings.png)

> [!NOTE]
> Este artigo aplica-se ao navegador baseado em HTML do Microsoft Edge Legacy lançado com o Windows 10 em julho de 2015. O artigo não se aplica ao novo navegador baseado em Chromium microsoft Edge lançado a 15 de janeiro de 2020. Para obter mais informações sobre o comportamento do Sync para o novo Microsoft Edge, consulte o artigo [Microsoft Edge Sync](/deployedge/microsoft-edge-enterprise-sync).

A sincronização do grupo de configuração do navegador Microsoft Edge (favoritos, lista de leitura) pode ser ativada ou desativada pelos utilizadores finais através da opção de menu de configurações do navegador Microsoft Edge.

![Conta](./media/enterprise-state-roaming-windows-settings-reference/active-directory-enterprise-state-roaming-edge.png)

Para a versão 1803 ou posterior do Windows 10, a sincronização do grupo de definição do Internet Explorer (favoritos, URLs dactilografados) pode ser ativada ou desativada pelos utilizadores finais através da opção de menu Do Internet Explorer Settings. 

![Definições](./media/enterprise-state-roaming-windows-settings-reference/active-directory-enterprise-state-roaming-ie.png)

## <a name="windows-settings-details"></a>Detalhes das definições do Windows

Na tabela seguinte, outras entradas na coluna do Grupo de Definições referem-se a definições que podem ser desativadas indo para Definições > Contas > Sincronizar as suas definições > outras definições do Windows. 

As entradas internas na coluna do Grupo de Definições referem-se a definições e aplicações que só podem ser desativadas a partir da sincronização dentro da própria aplicação ou desativando sincronização para todo o dispositivo utilizando definições de gestão de dispositivos móveis (MDM) ou Política de Grupo.
As definições que não vagueiam ou sincronizam não pertencerão a um grupo.

| Definições | Ambiente de Trabalho | Móvel | Group |
| --- | --- | --- | --- |
| **Contas**: imagem da conta |sincronizar |X |Tema |
| Contas : **outras** definições de conta |X |X | |
| **Banda larga móvel avançada**: Nome da rede de partilha de ligação à Internet (permite a autodiscovery de hotspots de Wi-Fi móveis via Bluetooth) |X |X |Palavras-passe |
| **Dados da aplicação**: aplicações individuais podem sincronizar dados |sincronização de backup |sincronização de backup |interno |
| **Lista de aplicações**: lista de aplicações instaladas |X |cópia de segurança |Outro |
| **Bluetooth**: todas as definições Bluetooth |X |X | |
| **Pedido de comando**: Iniciar definições "Predefinições" |sincronizar |X |interno |
| **Credenciais**: Cacifo credencial |sincronizar |sincronizar |palavra-passe |
| **Data, Hora e Região**: tempo automático (sincronização de tempo de Internet) |sincronizar |sincronizar |language |
| **Data, Hora e Região:** relógio 24 horas |sincronizar |X |language |
| **Data, Hora e Região:** data e hora |sincronizar |X |language |
| **Data, Hora e Região**: fuso horário | |X |language |
| **Data, Hora e Região**: horário de verão |sincronizar |X |language |
| **Data, Hora e Região**: país/região |sincronizar |X |language |
| **Data, Hora e Região:** primeiro dia da semana |sincronizar |X |language |
| **Data, Hora e Região**: formato regional (local) |sincronizar |X |language |
| **Data, Hora e Região:** data curta |sincronizar |X |language |
| **Data, Hora e Região:** data longa |sincronizar |X |language |
| **Data, Hora e Região:** pouco tempo |sincronizar |X |language |
| **Data, Hora e Região:** longo tempo |sincronizar |X |language |
| **Personalização do ambiente de** trabalho : tema do ambiente de trabalho (fundo, cor do sistema, sons predefinidos do sistema, protetor de ecrã) |sincronizar |X |Tema |
| **Personalização do ambiente** de trabalho : papel de parede slideshow |sincronizar |X |Tema |
| **Personalização do ambiente de trabalho**: definições de barra de tarefas (posição, auto-ocultação, etc.) |sincronizar |X |Tema |
| **Personalização do ambiente de trabalho**: iniciar o layout do ecrã |X |cópia de segurança | |
| **Dispositivos**: impressoras partilhadas a que ligou |X |X |other |
| **Navegador Microsoft Edge**: lista de leitura |sincronizar |sincronizar |interno |
| **Navegador Microsoft Edge**: favoritos |sincronizar |sincronizar |interno |
| **Microsoft Edge browser**: top sites <sup> [[1]](#footnote-1)</sup> |sincronizar |sincronizar |interno |
| **Navegador Microsoft Edge**: URLs dactilografados <sup> [[1]](#footnote-1)</sup> |sincronizar |sincronizar |interno |
| **Microsoft Edge browser**: configurações de barras favoritas <sup> [[1]](#footnote-1)</sup> |sincronizar |sincronizar |interno |
| **Microsoft Edge browser**: mostrar o botão inicial <sup> [[1]](#footnote-1)</sup> |sincronizar |sincronizar |interno |
| **Navegador Microsoft Edge**: bloquear pop-ups <sup> [[1]](#footnote-1)</sup> |sincronizar |sincronizar |interno |
| **Microsoft Edge browser**: pergunte-me o que fazer com cada download <sup> [[1]](#footnote-1)</sup> |sincronizar |sincronizar |interno |
| **Navegador Microsoft Edge**: ofereça para guardar senhas <sup> [[1]](#footnote-1)</sup> |sincronizar |sincronizar |interno |
| **Navegador Microsoft Edge**: enviar não rastreia pedidos <sup> [[1]](#footnote-1)</sup> |sincronizar |sincronizar |interno |
| **Navegador Microsoft Edge**: guardar entradas de formulário <sup> [[1]](#footnote-1)</sup> |sincronizar |sincronizar |interno |
| **Microsoft Edge browser**: mostrar sugestões de pesquisa e site como eu escrevo <sup> [[1]](#footnote-1)</sup> |sincronizar |sincronizar |interno |
| **Navegador Microsoft Edge**: preferência de cookies <sup> [[1]](#footnote-1)</sup> |sincronizar |sincronizar |interno |
| **Microsoft Edge browser**: deixe os sites salvarem licenças de mídia protegidas no meu dispositivo <sup> [[1]](#footnote-1)</sup> |sincronizar |sincronizar |interno |
| **Microsoft Edge browser**: definição de leitor de ecrã <sup> [[1]](#footnote-1)</sup> |sincronizar |sincronizar |interno |
| **Alto Contraste**: Ligado ou Desligado |sincronizar |X |facilidade de acesso |
| **Contraste alto**: Definições temáticas |sincronizar |X |facilidade de acesso |
| **Internet Explorer**: separadores abertos (URL e título) |sincronizar |sincronizar |Internet Explorer |
| **Internet Explorer**: lista de leitura |sincronizar |sincronizar |Internet Explorer |
| **Internet Explorer**: URLs dactilografado |sincronizar |sincronizar |Internet Explorer |
| **Internet Explorer**: histórico de navegação |sincronizar |sincronizar |Internet Explorer |
| **Internet Explorer**: favoritos |sincronizar |sincronizar |Internet Explorer |
| **Internet Explorer**: URLs excluídos |sincronizar |sincronizar |Internet Explorer |
| **Internet Explorer**: páginas in caseiras |sincronizar |sincronizar |Internet Explorer |
| **Internet Explorer**: sugestões de domínio |sincronizar |sincronizar |Internet Explorer |
| **Teclado**: os utilizadores podem ligar/desligar o teclado no ecrã |sincronizar |X |facilidade de acesso |
| **Teclado**: ligue o sim pegajoso (desligado por padrão) |sincronizar |X |facilidade de acesso |
| **Teclado**: ligue as teclas do filtro (desligado por padrão) |sincronizar |X |facilidade de acesso |
| **Teclado**: ligue as teclas (desligado por padrão) |sincronizar |X |facilidade de acesso |
| **Internet Explorer**: linguagem de domínio: Chinês (CHS) QWERTY - permitir a autoaprendizagem |sincronizar |X |Linguagem |
| **Idioma**: CHS QWERTY - permitir o ranking dinâmico dos candidatos |sincronizar |X |Linguagem |
| **Idioma**: CHS QWERTY - char-set chinês simplificado |sincronizar |X |Linguagem |
| **Língua**: CHS QWERTY - char-set chinês tradicional |sincronizar |X |Linguagem |
| **Língua**: CHS QWERTY - pinyin felpudo |sincronizar |cópia de segurança |Linguagem |
| **Idioma**: CHS QWERTY - pares felpudos |sincronizar |cópia de segurança |Linguagem |
| **Idioma**: CHS QWERTY - pinyin completo |sincronizar |X |Linguagem |
| **Língua**: CHS QWERTY - pinyin duplo |sincronizar |X |Linguagem |
| **Idioma**: CHS QWERTY - leitura de correção automática |sincronizar |X |Linguagem |
| **Idioma**: CHS QWERTY - Chave do interruptor C/E, mudança |sincronizar |X |Linguagem |
| **Idioma**: CHS QWERTY - Tecla de comutação C/E, Ctrl |sincronizar |X |Linguagem |
| **Idioma**: CHS WUBI - modo de entrada de caracteres únicos |sincronizar |X |Linguagem |
| **Língua**: CHS WUBI - mostrar a codificação restante do candidato |sincronizar |X |Linguagem |
| **Língua**: CHS WUBI - bip quando 4 codificação é inválida |sincronizar |X |Linguagem |
| **Língua**: CHT Bopomofo - incluir CJK Ext-A |sincronizar |X |Linguagem |
| **Língua**: IME japonês - dactilografia preditiva e palavras personalizadas |sincronizar |sincronizar |Linguagem |
| **IME** coreano (KOR) |X |X |Linguagem |
| **Língua**: reconhecimento de caligrafia |X |X |Linguagem |
| **Idioma**: perfil linguístico |sincronizar |cópia de segurança |Linguagem |
| **Linguagem**: spellcheck - correção automática e destaque erros ortográficos |sincronizar |cópia de segurança |Linguagem |
| **Idioma**: lista de teclados |sincronizar |cópia de segurança |Linguagem |
| **Ecrã de bloqueio**: todas as definições do ecrã de bloqueio |X |X | |
| **Magnifier:** dentro ou fora (alternância mestre) |X |X |Facilidade de acesso |
| **Magnifier**: ligue a cor da inversão (desligado por padrão) |sincronizar |X |Facilidade de acesso |
| **Magnifier**: tracking - siga o foco do teclado |sincronizar |X |Facilidade de acesso |
| **Magnifier**: tracking - siga o cursor do rato |sincronizar |X |Facilidade de acesso |
| **Magnifier**: comece quando os utilizadores iniciarem (desligado por padrão) |sincronizar |X |Facilidade de acesso |
| **Rato**: mude o tamanho do cursor do rato |sincronizar |X |other |
| **Rato**: mude a cor do cursor do rato |sincronizar |X |other |
| **Rato**: todas as outras configurações |X |X | |
| **Narrador:** lançamento rápido |sincronizar |X |Facilidade de acesso |
| **Narrador:** os utilizadores podem mudar o tom de fala do Narrador |sincronizar |X |Facilidade de acesso |
| **Narrador:** os utilizadores podem ligar ou desligar dicas de leitura do Narrador para itens comuns (por defeito) |sincronizar |X |Facilidade de acesso |
| **Narrador:** os utilizadores podem ligar ou desligar se conseguem ouvir caracteres dactilografados (ligados por defeito) |sincronizar |X |Facilidade de acesso |
| **Narrador:** os utilizadores podem ligar ou desligar se conseguem ouvir palavras dactilografada (por defeito) |sincronizar |X |Facilidade de acesso |
| **Narrador**: insira cursor seguindo o Narrador (on on by default) |sincronizar |X |Facilidade de acesso |
| **Narrador**: permitir o destaque visual do cursor narrador (on on by default) |sincronizar |X |Facilidade de acesso |
| **Narrador**: reproduzir pistas de áudio (por defeito) |sincronizar |X |Facilidade de acesso |
| **Narrador:** ative as teclas no teclado de toque quando levantar o dedo (desligado por defeito) |sincronizar |X |Facilidade de acesso |
| **Facilidade de acesso**: definir a espessura do cursor intermitente |sincronizar |X |Facilidade de acesso |
| **Facilidade de acesso**: remover imagens de fundo (desligadas por padrão) |sincronizar |X |Facilidade de acesso |
| **Potência e Sono**: todas as configurações |X |X | |
| **Personalização do ecrã inicial**: cor de acento (apenas telefone) |X |sincronizar |Tema |
| **Dactilografia**: dicionário de ortografia |sincronizar |cópia de segurança |Linguagem |
| **Dactilografia**: palavra mal corrigida autocorrectada |sincronizar |cópia de segurança |Linguagem |
| **Dactilografia**: destacar palavras mal ortografadas |sincronizar |cópia de segurança |Linguagem |
| **Dactilografia**: mostrar sugestões de texto como escrevo |sincronizar |cópia de segurança |Linguagem |
| **Dactilografia:** adicione um espaço depois de eu escolher uma sugestão de texto |sincronizar |cópia de segurança |Linguagem |
| **Dactilografia**: adicione um período depois de eu bater duas vezes na barra espacial |sincronizar |cópia de segurança |Linguagem |
| **Dactilografia**: capitalize a primeira letra de cada frase |sincronizar |cópia de segurança |Linguagem |
| **Dactilografia**: use todas as letras maiúsculas quando eu bater duas vezes na chave de mudança |sincronizar |cópia de segurança |Linguagem |
| **Dactilografia**: toque sons chave como escrevo |sincronizar |cópia de segurança |Linguagem |
| **Dactilografia**: dados de personalização para teclado touch |sincronizar |cópia de segurança |Linguagem |
| **Wi-Fi**: perfis Wi-Fi (apenas WPA) |sincronizar |sincronizar |Palavras-passe |

###### <a name="footnote-1"></a>Nota de rodapé 1

Versão so suportada mínima da Atualização de Criadores do Windows (Build 15063). 

## <a name="next-steps"></a>Passos seguintes

Para uma visão geral, consulte [a visão geral do roaming do estado da empresa.](enterprise-state-roaming-overview.md)
