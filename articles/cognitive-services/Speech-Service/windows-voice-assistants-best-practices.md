---
title: Assistentes de voz no Windows - Diretrizes de Design
titleSuffix: Azure Cognitive Services
description: Diretrizes para as melhores práticas ao conceber uma experiência de agente de voz.
services: cognitive-services
author: cfogg6
manager: spencer.king
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/1/2020
ms.author: adamwa
ms.openlocfilehash: 76a29af80891142fcf7f56f93f5c7acc5c58ab04
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "92165150"
---
# <a name="design-assistant-experiences-for-windows-10"></a>Experiências de assistente de design para o Windows 10

Os assistentes de voz desenvolvidos no Windows 10 devem implementar as diretrizes de experiência do utilizador abaixo para fornecer as melhores experiências possíveis para ativação de voz no Windows 10. Este documento irá orientar os desenvolvedores através da compreensão do trabalho fundamental necessário para que um assistente de voz se integre com o Windows 10 Shell.

## <a name="contents"></a>Conteúdos

- [Resumo das visualizações de ativação por voz suportadas no Windows 10](#summary-of-voice-activation-views-supported-in-windows-10)
- [Resumo dos requisitos](#requirements-summary)
- [Boas práticas para boas experiências de audição](#best-practices-for-good-listening-experiences)
- [Orientação de design para ativação de voz na aplicação](#design-guidance-for-in-app-voice-activation)
- [Orientação de design para ativação de voz acima da fechadura](#design-guidance-for-voice-activation-above-lock)
- [Orientação de design para pré-visualização de ativação de voz](#design-guidance-for-voice-activation-preview)

## <a name="summary-of-voice-activation-views-supported-in-windows-10"></a>Resumo das visualizações de ativação por voz suportadas no Windows 10

O Windows 10 infere uma experiência de ativação para o contexto do cliente com base no contexto do dispositivo. A tabela de resumo a seguir é uma visão geral de alto nível das diferentes vistas disponíveis quando o ecrã está ligado.

| Ver (Disponibilidade) | Contexto do dispositivo | Objetivo do cliente | Aparece quando | Necessidades de design |
| --- | --- | --- | --- | --- |
| **In-app (19H1)** | Abaixo da fechadura, assistente tem foco | Interaja com a app de assistente | Assistente processa o pedido na app | Principal experiência de audição na aplicação |
| **Fechadura acima (19H2)** | Acima da fechadura, não autenticado | Interaja com o assistente, mas à distância | Sistema está bloqueado e assistente solicita ativação | Visuais de ecrã inteiro para UI de campo distante. Implementar políticas de despedimento para não bloquear o desbloqueio. |
| **Pré-visualização da ativação por voz (20H1)** | Abaixo da fechadura, assistente não tem foco | Interaja com o assistente, mas de uma forma menos intrusiva | O sistema está abaixo do bloqueio e o assistente solicita a ativação de fundo | Tela mínima. Redimensionar ou entregar para a vista principal da aplicação, se necessário. |

## <a name="requirements-summary"></a>Resumo dos requisitos

O mínimo de esforço é necessário para aceder às diferentes experiências. No entanto, os assistentes precisam de implementar a orientação de design certa para cada vista. Este quadro abaixo fornece uma lista de verificação dos requisitos que devem ser seguidos.

| **Vista de ativação de voz** | **Resumo dos requisitos do assistente** |
| --- | --- |
| **In-app** | <ul><li>Processar o pedido na app</li><li>Fornece indicadores de UI para estados de audição</li><li>UI adapta-se à mudança de tamanhos de janela</li></ul> |
| **Acima da fechadura** | <ul><li>Detete o estado de bloqueio e solicite a ativação</li><li>Não forneça sempre um UX persistente que bloqueie o acesso ao ecrã de bloqueio do Windows</li><li>Fornecer visuais de ecrã completo e uma experiência de voz-primeiro</li><li>Honra orientação de despedimento abaixo</li><li>Siga as considerações de privacidade e segurança abaixo</li></ul> |
| **Pré-visualização da ativação de voz** | <ul><li>Detetar desbloquear estado e solicitar ativação de fundo</li><li>Desenhe o UX de audição mínimo no painel de pré-visualização</li><li>Desenhe um X próximo no topo-direito e desprete e pare de transmitir áudio quando pressionado</li><li>Redimensionar ou entregar para a visão da aplicação principal do assistente, conforme necessário para fornecer respostas</li></ul> |

## <a name="best-practices-for-good-listening-experiences"></a>Boas práticas para boas experiências de audição

Os assistentes devem construir uma experiência de audição para fornecer feedback crítico para que o cliente possa entender o estado do assistente. Abaixo estão alguns estados possíveis a considerar ao construir uma experiência de assistente. Estas são apenas sugestões possíveis, não orientações obrigatórias.

- Assistente está disponível para entrada de fala
- O assistente está em processo de ativação (ou uma palavra-chave ou um botão de microfone)
- O assistente está a transmitir ativamente áudio para a nuvem assistente
- O assistente está pronto para o cliente começar a falar
- O assistente está a ouvir que as palavras estão a ser ditas.
- Assistente entende que o cliente está feito de falar
- Assistente está processando e preparando uma resposta
- Assistente está respondendo

Mesmo que os Estados mudem rapidamente, vale a pena considerar fornecer UX para estados, uma vez que as durações são variáveis em todo o ecossistema windows. O feedback visual, bem como breves sinos áudio ou chilrear, também &quot; chamados &quot; auriculares, podem ser parte da solução. Da mesma forma, cartões visuais associados a descrições áudio fazem boas opções de resposta.

## <a name="design-guidance-for-in-app-voice-activation"></a>Orientação de design para ativação de voz na aplicação

Quando a aplicação assistente tem foco, a intenção do cliente é claramente interagir com a app, pelo que todas as experiências de ativação de voz devem ser tratadas pela visão principal da aplicação. Esta vista pode ser redimensionada pelo cliente. Para ajudar a explicar as interações de fachada do assistente, o resto deste documento usa o exemplo concreto de um assistente de serviço financeiro chamado Contoso. Neste e posterior diagrama, o que o cliente diz aparecerá em bolhas de discurso de desenho animado à esquerda com respostas de assistentes em bolhas de desenhos animados à direita.

**Vista na aplicação. Estado inicial quando a ativação de voz começa:** 
 ![ Screenshot mostrando a aplicação de assistente de finanças Contoso aberta à sua tela padrão. Uma bolha do discurso dos desenhos animados à direita diz "Contoso".](media/voice-assistants/windows_voice_assistant/initial_state.png)

**Vista na aplicação. Após ativação de voz bem sucedida, começa a experiência de audição:** Screenshot do assistente de voz no Windows ![ enquanto o assistente de voz está a ouvir](media/voice-assistants/windows_voice_assistant/listening.png)

**Vista na aplicação. Todas as respostas permanecem na experiência da aplicação.** ![ Screenshot do assistente de voz no Windows como respostas assistentes](media/voice-assistants/windows_voice_assistant/response.png)

## <a name="design-guidance-for-voice-activation-above-lock"></a>Orientação de design para ativação de voz acima da fechadura

Disponível com 19H2, os assistentes construídos na plataforma de ativação de voz do Windows estão disponíveis para responder acima do bloqueio.

### <a name="customer-opt-in"></a>Opt-in do cliente

A ativação de voz acima do bloqueio é sempre desativada por defeito. Os clientes optam pelas definições do Windows>ativação de voz>privacidade. Para obter informações sobre a monitorização e solicitação para esta definição, consulte o [guia de implementação do bloqueio acima](windows-voice-assistants-implementation-guide.md#detecting-above-lock-activation-user-preference).

### <a name="not-a-lock-screen-replacement"></a>Não uma substituição de ecrã de bloqueio

Embora as notificações ou outros pontos de integração padrão do ecrã de bloqueio de aplicações permaneçam disponíveis para o assistente, o ecrã de bloqueio do Windows define sempre a experiência inicial do cliente até ocorrer uma ativação de voz. Após a ativação por voz ser detetada, a aplicação assistente aparece temporariamente por cima do ecrã de bloqueio. Para evitar confusão no cliente, quando ativo acima do bloqueio, a aplicação assistente nunca deve apresentar UI para pedir qualquer tipo de credenciais ou login.

![Screenshot de um ecrã de bloqueio do Windows](media/voice-assistants/windows_voice_assistant/lock_screen1.png)

### <a name="above-lock-experience-following-voice-activation"></a>Experiência de bloqueio acima após ativação de voz

Quando o ecrã está ligado, a aplicação assistente é ecrã completo sem barra de título acima do ecrã de bloqueio. Visuais maiores e descrições de voz fortes com interface vocal-primária forte permitem casos em que o cliente está muito longe para ler UI ou tem as mãos ocupadas com outra tarefa (não PC).

Quando o ecrã permanece desligado, a aplicação assistente pode reproduzir um auricular para indicar que o assistente está a ativar e fornecer uma experiência apenas por voz.

![Screenshot do assistente de voz acima da fechadura](media/voice-assistants/windows_voice_assistant/above_lock_listening.png)

### <a name="dismissal-policies"></a>Políticas de despedimento

O assistente deve implementar a orientação de despedimento nesta secção para facilitar o login dos clientes na próxima vez que quiserem utilizar o seu PC Windows. Seguem-se requisitos específicos, que o assistente deve implementar:

- **Todas as telas de assistente que mostrem acima da fechadura devem conter um X** no canto superior direito que dispensa o assistente.
- **Premir qualquer chave também deve dispensar a aplicação de assistente**. A entrada de teclado é um sinal tradicional de aplicação de bloqueio que o cliente quer fazer login. Portanto, qualquer entrada de teclado/texto não deve ser direcionada para a aplicação. Em vez disso, a aplicação deve descartar-se quando for detetada a entrada do teclado, para que o cliente possa facilmente iniciar sessão no seu dispositivo.
- **Se o ecrã for desligado, a aplicação deve ser auto-descartada.** Isto garante que da próxima vez que o cliente utilizar o seu PC, o ecrã de login estará pronto e à espera deles.
- Se a aplicação estiver &quot; a ser &quot; utilizada, pode continuar acima do bloqueio. &quot;em uso &quot; constitui qualquer entrada ou saída. Por exemplo, quando o streaming de música ou vídeo a aplicação pode continuar acima do bloqueio. &quot;Siga &quot; e outros passos de diálogo multi-volta são permitidos para manter a aplicação acima do bloqueio.
- **Os detalhes de implementação sobre o despedimento do pedido** podem ser encontrados no guia de [implementação do bloqueio acima.](windows-voice-assistants-implementation-guide.md#closing-the-application)

![Screenshot mostrando a vista de bloqueio acima da aplicação assistente financeira Contoso.](media/voice-assistants/windows_voice_assistant/above_lock_response.png)

![Screenshot de um ambiente de trabalho mostrando o ecrã de bloqueio do Windows.](media/voice-assistants/windows_voice_assistant/lock_screen2.png)

### <a name="privacy-amp-security-considerations-above-lock"></a>&amp;Considerações de segurança de privacidade acima do bloqueio

Muitos computadores são portáteis, mas nem sempre ao alcance do cliente. Podem ser brevemente deixados em quartos de hotel, assentos de avião ou espaços de trabalho, onde outras pessoas têm acesso físico. Se os assistentes que estão habilitados acima do cadeado não estiverem preparados, podem ficar sujeitos à classe dos chamados ataques de &quot; [empregada maléfica.](https://en.wikipedia.org/wiki/Evil_maid_attack) &quot;

Por isso, os assistentes devem seguir as orientações nesta secção para ajudar a manter a experiência segura. A interação acima do bloqueio ocorre quando o utilizador do Windows é não autenticado. Isto significa que, em geral, **a entrada no assistente também deve ser tratada como não autenticada.**

- Os assistentes devem **implementar uma lista de competências permitidas para identificar habilidades que sejam confirmadas seguras e seguras** para serem acedidas acima do cadeado.
- As tecnologias de ID dos altifalantes podem desempenhar um papel na redução de alguns riscos, mas o ID do altifalante não é um substituto adequado para a autenticação do Windows.
- A lista de competências permitidas deve considerar três classes de ações ou competências:

| **Classe de ação** | **Descrição** | **Exemplos (não uma lista completa)** |
| --- | --- | --- |
| Cofre sem autenticação | Informações gerais ou comando e controlo básicos de aplicações | &quot;Que horas &quot; são? &quot;&quot; |
| Seguro com ID do orador | Risco de personificação, revelando informações pessoais. | &quot;Qual&#39;é a minha próxima &quot; nomeação? &quot; &quot; &quot;&quot; |
| Cofre apenas após a autenticação do Windows | Ações de alto risco que um intruso poderia usar para prejudicar o cliente | &quot;Comprar mais &quot; mantimentos, &quot; Apagar a minha (importante) &quot; nomeação, Enviar uma &quot; (média) mensagem de &quot; texto, lançar uma &quot; página (nefasta)&quot; |

No caso do Contoso, a informação geral em torno da informação sobre o stock público é segura sem autenticação. Informações específicas do cliente, tais como o número de ações detidas, são provavelmente seguras com o ID do altifalante. No entanto, comprar ou vender ações nunca deve ser permitido sem a autenticação do Windows.

Para garantir ainda mais a experiência, os **weblinks ou outros lançamentos de aplicações para apps serão sempre bloqueados pelo Windows até que o cliente assine.** Como mitigação de último recurso, a Microsoft reserva-se o direito de remover uma aplicação da lista permitida de assistentes habilitados se um problema de segurança grave não for abordado em tempo útil.

## <a name="design-guidance-for-voice-activation-preview"></a>Orientação de design para pré-visualização de ativação de voz

Abaixo do bloqueio, quando a aplicação assistente _não_ tem foco, o Windows fornece uma UI de ativação de voz menos intrusiva para ajudar a manter o cliente em fluxo. Isto é especialmente verdade para o caso de falsas ativações que seriam altamente disruptivas se lançassem a app completa. A ideia principal é que cada assistente tem outra casa na Shell, o ícone da barra de tarefas assistente. Quando ocorre o pedido de ativação de fundo, aparece uma pequena vista acima do ícone da barra de tarefas do assistente. Os assistentes devem proporcionar uma pequena experiência de audição nesta tela. Após o processamento dos pedidos, os assistentes podem optar por redimensionar esta visão para mostrar uma resposta em contexto ou para entregar a sua visão principal da aplicação para mostrar visuais maiores e mais detalhados.

- Para se manter mínimo, a pré-visualização não tem uma barra de título, pelo **que o assistente deve desenhar um X no direito superior para permitir que os clientes rejeitem a vista.** Consulte [o fecho do pedido](windows-voice-assistants-implementation-guide.md#closing-the-application) de chamadas específicas para chamadas quando premir o botão de desatenção.
- Para suportar pré-visualizações de ativação de voz, os assistentes podem convidar os clientes a fixar o assistente na barra de tarefas durante a primeira execução.

**Pré-visualização da ativação de voz: Estado inicial**

O assistente de Contoso tem uma casa na barra de tarefas: o seu ícone circular e rodopiante.

![Screenshot do assistente de voz no Windows como pré-ativação do ícone da barra de tarefas](media/voice-assistants/windows_voice_assistant/pre_compact_view.png)

**À medida que a ativação progride,** o assistente solicita a ativação de fundo. O assistente recebe um pequeno painel de pré-visualização (largura padrão 408 e altura: 248). Se a ativação da voz do lado do servidor determinar que o sinal foi um falso positivo, esta visão pode ser dispensada para interrupção mínima.

![Screenshot do assistente de voz no Windows em vista compacta enquanto verifica a ativação](media/voice-assistants/windows_voice_assistant/compact_view_activating.png)

**Quando a ativação final for confirmada,** o assistente apresenta o seu UX de audição. O assistente deve sempre desenhar um X dispensado no direito superior da pré-visualização da ativação de voz.

![Screenshot do assistente de voz no Windows ouvindo em vista compacta](media/voice-assistants/windows_voice_assistant/compact_view_listening.png)

Podem ser **apresentadas respostas rápidas** na pré-visualização da ativação de voz. Um TryResizeView permitirá aos assistentes solicitar diferentes tamanhos.

![Screenshot do assistente de voz no Windows respondendo em vista compacta](media/voice-assistants/windows_voice_assistant/compact_view_response.png)

**Entrega.** Em qualquer momento, o assistente pode entregar a sua visão principal da app para fornecer mais informações, diálogos ou respostas que exijam mais imóveis de ecrã. Consulte a [secção Transição de vista compacta para](windows-voice-assistants-implementation-guide.md#transition-from-compact-view-to-full-view) a secção de visualização completa para mais detalhes de implementação.

![Screenshots do assistente de voz no Windows antes e depois de expandir a vista compacta](media/voice-assistants/windows_voice_assistant/compact_transition.png)

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Começa a desenvolver o teu assistente de voz](how-to-windows-voice-assistants-get-started.md)