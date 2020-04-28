---
title: Referência de definições de roaming do Windows 10 - Diretório Ativo Azure
description: Definições que serão percorridos ou apoiadas no Windows 10 com ESR
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: troubleshooting
ms.date: 02/12/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: na
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3a7abc402f1fc2e449e7aac5effdb01b6b941100
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "78672633"
---
# <a name="windows-10-roaming-settings-reference"></a>Referência das definições de roaming do Windows 10

Segue-se uma lista das definições que serão percorridos ou apoiadas no Windows 10. 

## <a name="devices-and-endpoints"></a>Dispositivos e pontos finais

Consulte a tabela seguinte para obter um resumo dos dispositivos e tipos de conta que são suportados pela estrutura de sincronização, cópia de segurança e restauro no Windows 10.

| Tipo de conta e operação | Ambiente de trabalho | Telemóvel |
| --- | --- | --- |
| Diretório Ativo Azure: sincronização |Sim |Não |
| Diretório Ativo Azure: backup/restauro |Não |Não |
| Conta Microsoft: sincronização |Sim |Sim |
| Conta Microsoft: backup/restauro |Não |Sim |

## <a name="what-is-backup"></a>O que é reforço?

As definições do Windows geralmente sincronizam por padrão, mas algumas definições são apenas apoiadas, como a lista de aplicações instaladas num dispositivo. A cópia de segurança destina-se apenas a dispositivos móveis e atualmente não está disponível para utilizadores de Roaming do Estado Da Empresa. A cópia de segurança utiliza uma conta microsoft e armazena as definições e os dados da aplicação no OneDrive. Se um utilizador desativar a sincronização no dispositivo utilizando a aplicação Definições, os dados da aplicação que normalmente sincronizam tornam-se apenas cópias de segurança. Os dados de backup só podem ser acedidos através da operação de restauro durante a primeira experiência de execução de um novo dispositivo. As cópias de segurança podem ser desativadas através das definições do dispositivo e podem ser geridas e eliminadas através da conta OneDrive do utilizador.

## <a name="windows-settings-overview"></a>Visão geral das definições do Windows

Os seguintes grupos de definições estão disponíveis para os utilizadores finais ativarem/desativarem as definições sincronizadas nos dispositivos do Windows 10.

* Tema: fundo de ambiente de trabalho, azulejo do utilizador, posição da barra de tarefas, etc. 
* Definições do Internet Explorer: histórico de navegação, URLs dactilografados, favoritos, etc. 
* Passwords: Gestor de credenciais do Windows, incluindo perfis Wi-Fi 
* Preferências linguísticas: dicionário de ortografia, definições de linguagem do sistema 
* Facilidade de Acesso: narrador, teclado no ecrã, amplificador 
* Outras Definições do Windows: consulte os detalhes das Definições do Windows
* Definição do navegador Microsoft Edge: favoritos do Microsoft Edge, lista de leitura e outras definições

![Sincronizar as suas definições](./media/enterprise-state-roaming-windows-settings-reference/active-directory-enterprise-state-roaming-syncyoursettings.png)

> [!NOTE]
> Este artigo aplica-se ao navegador baseado no Microsoft Edge Legacy HTML lançado com o Windows 10 em julho de 2015. O artigo não se aplica ao novo navegador baseado em Crómio microsoft Edge, lançado a 15 de janeiro de 2020. Para obter mais informações sobre o comportamento do Sync para o novo Microsoft Edge, consulte o artigo [Microsoft Edge Sync](/deployedge/microsoft-edge-enterprise-sync).

A sincronização do grupo de definição de navegador do Microsoft Edge (favoritos, lista de leitura) pode ser ativada ou desativada pelos utilizadores finais através da opção de menu Definições do navegador Microsoft Edge.

![Conta](./media/enterprise-state-roaming-windows-settings-reference/active-directory-enterprise-state-roaming-edge.png)

Para a versão 1803 do Windows 1803 ou posterior, a sincronização do grupo de definição do Internet Explorer (favoritos, URLs dactilografados) pode ser ativada ou desativada pelos utilizadores finais através da opção de menu Definições do Internet Explorer. 

![Definições](./media/enterprise-state-roaming-windows-settings-reference/active-directory-enterprise-state-roaming-ie.png)

## <a name="windows-settings-details"></a>Detalhes das Definições do Windows

Na tabela a seguir, Outras entradas na coluna do Grupo Definições referem-se a definições que podem ser desativadas indo para Definições > Contas > Sincronizar as definições > Outras definições do Windows. 

As entradas internas na coluna do Grupo Definições referem-se a definições e aplicações que só podem ser desativadas de sincronização dentro da própria app ou desativando a sincronização de todo o dispositivo utilizando as definições de gestão de dispositivos móveis (MDM) ou Política de Grupo.
As configurações que não vagueiam ou sincronizam não pertencerão a um grupo.

| Definições | Ambiente de trabalho | Telemóvel | Grupo |
| --- | --- | --- | --- |
| **Contas**: imagem da conta |sincronizar |X |Tema |
| Contas : **outras**definições de conta |X |X | |
| **Banda larga móvel avançada**: Nome da rede de partilha de ligação à Internet (permite a descoberta automática de hotspots Wi-Fi móveis via Bluetooth) |X |X |Palavras-passe |
| **Dados da aplicação**: aplicações individuais podem sincronizar dados |sincronização de backup |sincronização de backup |interna |
| **Lista de aplicações**: lista de aplicações instaladas |X |cópia de segurança |Outros |
| **Bluetooth**: todas as definições Bluetooth |X |X | |
| **Pedido de comando**: Configurações de "Predefinições" de comando |sincronizar |X |interna |
| **Credenciais**: Armário Credencial |sincronizar |sincronizar |palavra-passe |
| **Data, Hora e Região:** tempo automático (sincronização do tempo de Internet) |sincronizar |sincronizar |language |
| **Data, Hora e Região**: Relógio 24 horas |sincronizar |X |language |
| **Data, Hora e Região:** data e hora |sincronizar |X |language |
| **Data, Hora e Região**: fuso horário | |X |language |
| **Data, Hora e Região:** horário de verão |sincronizar |X |language |
| **Data, Hora e Região**: país/região |sincronizar |X |language |
| **Data, Hora e Região**: primeiro dia da semana |sincronizar |X |language |
| **Data, Hora e Região**: formato regional (local) |sincronizar |X |language |
| **Data, Hora e Região**: data curta |sincronizar |X |language |
| **Data, Hora e Região**: data longa |sincronizar |X |language |
| **Data, Hora e Região**: pouco tempo |sincronizar |X |language |
| **Data, Hora e Região**: muito tempo |sincronizar |X |language |
| **Personalização do ambiente de trabalho**: tema de ambiente de trabalho (background, cor do sistema, sons do sistema predefinido, protetor de ecrã) |sincronizar |X |Tema |
| **Personalização do ambiente de trabalho**: papel de parede slideshow |sincronizar |X |Tema |
| **Personalização do ambiente de trabalho**: definições de barra de tarefas (posição, auto-ocultação, etc.) |sincronizar |X |Tema |
| **Personalização do ambiente de trabalho**: iniciar o layout do ecrã |X |cópia de segurança | |
| **Dispositivos**: impressoras partilhadas a que ligou |X |X |outro |
| **Navegador Microsoft Edge**: lista de leitura |sincronizar |sincronizar |interna |
| **Navegador Microsoft Edge**: favoritos |sincronizar |sincronizar |interna |
| **Navegador Microsoft Edge**: sites de topo <sup> [[1]](#footnote-1)</sup> |sincronizar |sincronizar |interna |
| **Navegador Microsoft Edge**: URLs dactilografados <sup> [[1]](#footnote-1)</sup> |sincronizar |sincronizar |interna |
| **Navegador Microsoft Edge**: definições de barras favoritas <sup> [[1]](#footnote-1)</sup> |sincronizar |sincronizar |interna |
| **Navegador Microsoft Edge**: mostrar o botão inicial <sup> [[1]](#footnote-1)</sup> |sincronizar |sincronizar |interna |
| **Navegador Microsoft Edge**: block pop-ups <sup> [[1]](#footnote-1)</sup> |sincronizar |sincronizar |interna |
| **Navegador Microsoft Edge**: pergunte-me o que fazer com cada download <sup> [[1]](#footnote-1)</sup> |sincronizar |sincronizar |interna |
| **Navegador Microsoft Edge**: oferta para guardar senhas <sup> [[1]](#footnote-1)</sup> |sincronizar |sincronizar |interna |
| **Navegador Microsoft Edge**: enviar não rastreie pedidos <sup> [[1]](#footnote-1)</sup> |sincronizar |sincronizar |interna |
| **Navegador Microsoft Edge**: guardar entradas de formulário <sup> [[1]](#footnote-1)</sup> |sincronizar |sincronizar |interna |
| **Navegador Microsoft Edge**: mostrar sugestões de pesquisa e site como escrevo <sup> [[1]](#footnote-1)</sup> |sincronizar |sincronizar |interna |
| **Navegador Microsoft Edge**: preferência de cookies <sup> [[1]](#footnote-1)</sup> |sincronizar |sincronizar |interna |
| **Navegador Microsoft Edge**: deixe os sites guardarem licenças de mídia protegidas no meu dispositivo <sup> [[1]](#footnote-1)</sup> |sincronizar |sincronizar |interna |
| **Navegador Microsoft Edge**: definição de leitor de ecrã <sup> [[1]](#footnote-1)</sup> |sincronizar |sincronizar |interna |
| **Alto Contraste**: Ligado ou desligado |sincronizar |X |facilidade de acesso |
| **Contraste elevado**: Definições temáticas |sincronizar |X |facilidade de acesso |
| **Internet Explorer**: separadores abertos (URL e título) |sincronizar |sincronizar |Internet Explorer |
| **Internet Explorer**: lista de leitura |sincronizar |sincronizar |Internet Explorer |
| **Internet Explorer**: URLs dactilografados |sincronizar |sincronizar |Internet Explorer |
| **Internet Explorer**: histórico de navegação |sincronizar |sincronizar |Internet Explorer |
| **Internet Explorer**: favoritos |sincronizar |sincronizar |Internet Explorer |
| **Internet Explorer**: URLs excluídos |sincronizar |sincronizar |Internet Explorer |
| **Internet Explorer**: páginas iniciais |sincronizar |sincronizar |Internet Explorer |
| **Internet Explorer**: sugestões de domínio |sincronizar |sincronizar |Internet Explorer |
| **Teclado**: os utilizadores podem ligar/desligar o teclado no ecrã |sincronizar |X |facilidade de acesso |
| **Teclado:** ligue o sticky sim (desligado por padrão) |sincronizar |X |facilidade de acesso |
| **Teclado:** ligue as teclas do filtro (desligadas por defeito) |sincronizar |X |facilidade de acesso |
| **Teclado:** ligue as teclas de alternância (desligadas por defeito) |sincronizar |X |facilidade de acesso |
| **Internet Explorer**: linguagem de domínio: Chinês (CHS) QWERTY - permitir a autoaprendizagem |sincronizar |X |Idioma |
| **Idioma**: CHS QWERTY - permita o ranking dinâmico dos candidatos |sincronizar |X |Idioma |
| **Idioma**: CHS QWERTY - char-set Chinês simplificado |sincronizar |X |Idioma |
| **Idioma**: CHS QWERTY - char-set Tradicional Chinês |sincronizar |X |Idioma |
| **Idioma**: CHS QWERTY - pinyin fuzzy |sincronizar |cópia de segurança |Idioma |
| **Idioma**: CHS QWERTY - pares felpudos |sincronizar |cópia de segurança |Idioma |
| **Idioma**: CHS QWERTY - pinyin completo |sincronizar |X |Idioma |
| **Idioma**: CHS QWERTY - pinyin duplo |sincronizar |X |Idioma |
| **Idioma**: CHS QWERTY - leitura de correção automática |sincronizar |X |Idioma |
| **Idioma**: CHS QWERTY - Tecla de comutação C/E, shift |sincronizar |X |Idioma |
| **Idioma**: CHS QWERTY - Tecla de comutação C/E, CTRL |sincronizar |X |Idioma |
| **Idioma**: CHS WUBI - modo de entrada de caracteres únicos |sincronizar |X |Idioma |
| **Idioma**: CHS WUBI - mostrar o restante código do candidato |sincronizar |X |Idioma |
| **Idioma**: CHS WUBI - bip quando a codificação 4 é inválida |sincronizar |X |Idioma |
| **Idioma**: CHT Bopomofo - inclua CJK Ext-A |sincronizar |X |Idioma |
| **Idioma**: IME japonês - dactilografia preditiva e palavras personalizadas |sincronizar |sincronizar |Idioma |
| **Idioma**: Coreano (KOR) IME |X |X |Idioma |
| **Linguagem**: reconhecimento de caligrafia |X |X |Idioma |
| **Idioma**: perfil de linguagem |sincronizar |cópia de segurança |Idioma |
| **Idioma**: spellcheck - correção automática e destacar erros ortográficos |sincronizar |cópia de segurança |Idioma |
| **Idioma**: lista de teclados |sincronizar |cópia de segurança |Idioma |
| **Ecrã de bloqueio**: todas as definições do ecrã de bloqueio |X |X | |
| **Magnifier:** dentro ou fora (alternar mestre) |X |X |Facilidade de acesso |
| **Magnifier:** ligue ou desligue a cor da inversão (desligada por defeito) |sincronizar |X |Facilidade de acesso |
| **Magnifier**: tracking - siga o foco do teclado |sincronizar |X |Facilidade de acesso |
| **Magnifier**: rastreio - siga o cursor do rato |sincronizar |X |Facilidade de acesso |
| **Amplificador**: comece quando os utilizadores iniciarem o início (desligado por padrão) |sincronizar |X |Facilidade de acesso |
| **Rato**: alterar o tamanho do cursor de rato |sincronizar |X |outro |
| **Rato**: mude a cor do cursor do rato |sincronizar |X |outro |
| **Rato**: todas as outras definições |X |X | |
| **Narrador**: lançamento rápido |sincronizar |X |Facilidade de acesso |
| **Narrador:** os utilizadores podem mudar o pitch de fala do Narrador |sincronizar |X |Facilidade de acesso |
| **Narrador:** os utilizadores podem ligar ou desligar dicas de leitura do Narrador para itens comuns (por padrão) |sincronizar |X |Facilidade de acesso |
| **Narrador**: os utilizadores podem ligar ou desligar se podem ouvir caracteres dactilografados (por padrão) |sincronizar |X |Facilidade de acesso |
| **Narrador**: os utilizadores podem ligar ou desligar se podem ouvir palavras dactilografadas (por padrão) |sincronizar |X |Facilidade de acesso |
| **Narrador:** insira o cursor seguindo o Narrador (por defeito) |sincronizar |X |Facilidade de acesso |
| **Narrador**: ativar o realce visual do cursor narrador (por padrão) |sincronizar |X |Facilidade de acesso |
| **Narrador:** reproduzir pistas de áudio (por defeito) |sincronizar |X |Facilidade de acesso |
| **Narrador:** ativar as teclas no teclado touch quando levantar o dedo (desligado por defeito) |sincronizar |X |Facilidade de acesso |
| **Facilidade de acesso**: definir a espessura do cursor intermitente |sincronizar |X |Facilidade de acesso |
| **Facilidade de acesso**: remover imagens de fundo (desligadas por padrão) |sincronizar |X |Facilidade de acesso |
| **Power and Sleep**: todas as configurações |X |X | |
| **Visualização do ecrã inicial**: cor de sotaque (apenas telefone) |X |sincronizar |Tema |
| **Dactilografia**: dicionário de ortografia |sincronizar |cópia de segurança |Idioma |
| **Dactilografia**: palavra mal escrita correta |sincronizar |cópia de segurança |Idioma |
| **Dactilografia**: destacar palavras mal escritas |sincronizar |cópia de segurança |Idioma |
| **Dactilografia**: mostrar sugestões de texto à medida que escrevo |sincronizar |cópia de segurança |Idioma |
| **Dactilografia:** adicione um espaço depois de eu escolher uma sugestão de texto |sincronizar |cópia de segurança |Idioma |
| **Dactilografia**: adicione um período depois de eu bater duas vezes na barra espacial |sincronizar |cópia de segurança |Idioma |
| **Dactilografia**: capitalizar a primeira letra de cada frase |sincronizar |cópia de segurança |Idioma |
| **Dactilografia:** use todas as letras maiúsculas quando eu tocar duas vezes na tecla de mudança |sincronizar |cópia de segurança |Idioma |
| **Dactilografia:** reproduzir sons de teclas como escrevo |sincronizar |cópia de segurança |Idioma |
| **Dactilografia**: dados de personalização para teclado tátil |sincronizar |cópia de segurança |Idioma |
| **Wi-Fi**: Perfis Wi-Fi (apenas WPA) |sincronizar |sincronizar |Palavras-passe |

###### <a name="footnote-1"></a>Nota de rodapé 1

Versão mínima suportada do Sistema Operativo Do Windows Creators Update (Build 15063). 

## <a name="next-steps"></a>Passos seguintes

Para uma visão geral, consulte a [visão geral do estado empresarial.](enterprise-state-roaming-overview.md)
