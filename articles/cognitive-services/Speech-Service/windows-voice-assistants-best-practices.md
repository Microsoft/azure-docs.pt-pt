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
ms.openlocfilehash: 30df02062d3b94836f0131ac1124f56d1deefb5b
ms.sourcegitcommit: 309a9d26f94ab775673fd4c9a0ffc6caa571f598
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/09/2020
ms.locfileid: "82997493"
---
# <a name="design-assistant-experiences-for-windows-10"></a>Experiências de assistente de design para windows 10

Os assistentes de voz desenvolvidos no Windows 10 devem implementar as diretrizes de experiência do utilizador abaixo para fornecer as melhores experiências possíveis para a ativação de voz no Windows 10. Este documento irá orientar os desenvolvedores através da compreensão do trabalho-chave necessário para que um assistente de voz se integre com o Windows 10 Shell.

## <a name="contents"></a>Conteúdos

- [Resumo das visualizações de ativação de voz suportadas no Windows 10](#summary-of-voice-activation-views-supported-in-windows-10)
- [Resumo dos requisitos](#requirements-summary)
- [Boas práticas para boas experiências de audição](#best-practices-for-good-listening-experiences)
- [Orientação de design para ativação de voz na aplicação](#design-guidance-for-in-app-voice-activation)
- [Orientação de design para ativação de voz acima do bloqueio](#design-guidance-for-voice-activation-above-lock)
- [Orientação de design para pré-visualização de ativação de voz](#design-guidance-for-voice-activation-preview)

## <a name="summary-of-voice-activation-views-supported-in-windows-10"></a>Resumo das visualizações de ativação de voz suportadas no Windows 10

O Windows 10 infere uma experiência de ativação para o contexto do cliente com base no contexto do dispositivo. A tabela de resumo sumário que se segue é uma visão geral de alto nível das diferentes visões disponíveis quando o ecrã está ligado.

| Ver (Disponibilidade) | Contexto do dispositivo | Objetivo do cliente | Aparece quando | Necessidades de design |
| --- | --- | --- | --- | --- |
| **In-app (19H1)** | Abaixo do bloqueio, assistente tem foco | Interaja com a app assistente | Assistente processa o pedido na app | Principal experiência de audição na aplicação |
| **Acima da fechadura (19H2)** | Acima da fechadura, não autenticado | Interaja com o assistente, mas à distância | Sistema está bloqueado e assistente solicita ativação | Visuais de ecrã completo para UI de campo distante. Implementar políticas de despedimento para não bloquear o desbloqueio. |
| **Pré-visualização de ativação de voz (20H1)** | Abaixo do bloqueio, o assistente não tem foco | Interaja com o assistente, mas de uma forma menos intrusiva | Sistema está abaixo lock e assistente solicita ativação de fundo | Tela mínima. Redimensione ou entregue para a vista principal da aplicação, conforme necessário. |

## <a name="requirements-summary"></a>Resumo dos requisitos

É necessário um esforço mínimo para aceder às diferentes experiências. No entanto, os assistentes precisam de implementar a orientação de design certa para cada vista. Este quadro abaixo fornece uma lista de verificação dos requisitos que devem ser seguidos.

| **Vista de ativação de voz** | **Resumo dos requisitos do assistente** |
| --- | --- |
| **In-app** | <ul><li>Processar o pedido na app</li><li>Fornece indicadores de UI para estados de escuta</li><li>UI adapta-se à medida que os tamanhos das janelas mudam</li></ul> |
| **Acima da fechadura** | <ul><li>Detetar o estado de bloqueio e a ativação do pedido</li><li>Não forneça sempre UX persistente que bloqueie o acesso ao ecrã de bloqueio do Windows</li><li>Fornecer visuais de ecrã completo e uma experiência de primeira voz</li><li>Orientação de despedimento de honra abaixo</li><li>Siga as considerações de privacidade e segurança abaixo</li></ul> |
| **Pré-visualização de ativação de voz** | <ul><li>Detetar desbloquear o estado e solicitar a ativação de antecedentes</li><li>Desenhe o mínimo de audição UX no painel de pré-visualização</li><li>Desenhe um X próximo no superior direito e auto-descartar e pare de transmitir áudio quando pressionado</li><li>Redimensionar ou entregar para a visão principal da aplicação assistente, conforme necessário para fornecer respostas</li></ul> |

## <a name="best-practices-for-good-listening-experiences"></a>Boas práticas para boas experiências de audição

Os assistentes devem construir uma experiência de audição para fornecer feedback crítico para que o cliente possa entender o estado do assistente. Abaixo estão alguns estados possíveis a considerar na construção de uma experiência de assistente. Estas são apenas sugestões possíveis, não orientações obrigatórias.

- Assistente está disponível para entrada de fala
- O assistente está em processo de ativação (ou uma palavra-chave ou um botão de microfone)
- Assistente está ativamente transmitindo áudio para a nuvem assistente
- Assistente está pronto para o cliente começar a falar
- Assistente está a ouvir que as palavras estão a ser ditas
- Assistente entende que o cliente é feito falando
- Assistente está processando e preparando uma resposta
- Assistente está respondendo

Mesmo que os Estados mudem rapidamente, vale a pena considerar fornecer UX para estados, uma vez que as durações são variáveis em todo o ecossistema windows. O feedback visual, bem como breves sinos de áudio ou chilrear, também chamados &quot;auriculares,&quot;podem ser parte da solução. Da mesma forma, os cartões visuais associados a descrições áudio fazem com que se verifiquem boas opções de resposta.

## <a name="design-guidance-for-in-app-voice-activation"></a>Orientação de design para ativação de voz na aplicação

Quando a aplicação assistente tem foco, a intenção do cliente é claramente interagir com a app, por isso todas as experiências de ativação de voz devem ser tratadas pela visão principal da aplicação. Esta vista pode ser redimensionada pelo cliente. Para ajudar a explicar as interações de conchas assistentes, o resto deste documento usa o exemplo concreto de um assistente de serviço financeiro chamado Contoso. Neste e nos diagramas subsequentes, o que o cliente diz aparecerá nas bolhas do discurso dos desenhos animados à esquerda com respostas assistentes em bolhas de desenho soporto à direita.

**Vista na aplicação. Estado inicial quando a ativação de voz começa:**
![Screenshot do assistente de voz no Windows antes da ativação](media/voice-assistants/windows_voice_assistant/initial_state.png)

**Vista na aplicação. Após ativação de voz bem sucedida, começa a experiência de audição:**![Screenshot do assistente de voz no Windows enquanto assistente de voz está a ouvir](media/voice-assistants/windows_voice_assistant/listening.png)

**Vista na aplicação. Todas as respostas permanecem na experiência da aplicação.** ![Screenshot do assistente de voz no Windows como resposta seleção](media/voice-assistants/windows_voice_assistant/response.png)

## <a name="design-guidance-for-voice-activation-above-lock"></a>Orientação de design para ativação de voz acima do bloqueio

Disponíveis com 19H2, os assistentes construídos na plataforma de ativação de voz windows estão disponíveis para responder acima do bloqueio.

> [!NOTE]
> Devido a um problema ativo, os assistentes que desenham acima do bloqueio UI devem implementar O Serviço de Janelas.CloseWindow() para todos os despedimentos. Isto resultará na rescisão da aplicação, mas atenua um problema técnico e mantém o assistente em estado de limpeza. Além disso, para manter o estado limpo se uma aplicação estiver ativada para ativação de voz de bloqueio acima, deve ouvir alterações de estado de bloqueio e WindowService.CloseWindow() quando o dispositivo bloqueia.

### <a name="customer-opt-in"></a>Opt-in do cliente

A ativação por voz acima do bloqueio é sempre desativada por defeito. Os clientes optam por participar através das definições do Windows>Privacidade>Ativação de Voz. Para obter mais informações sobre a monitorização e solicitação para esta definição, consulte o [guia de implementação](windows-voice-assistants-implementation-guide.md#detecting-above-lock-activation-user-preference)do bloqueio acima .

### <a name="not-a-lock-screen-replacement"></a>Não é uma substituição de ecrã de bloqueio

Embora as notificações ou outros pontos padrão de integração do ecrã de bloqueio de aplicações permaneçam disponíveis para o assistente, o ecrã de bloqueio do Windows define sempre a experiência inicial do cliente até que ocorra uma ativação de voz. Após a deteção da ativação por voz, a aplicação assistente aparece temporariamente acima do ecrã de bloqueio. Para evitar confusão do cliente, quando ativa acima do bloqueio, a aplicação assistente nunca deve apresentar UI para pedir qualquer tipo de credenciais ou iniciar sessão.

![Screenshot de um ecrã de bloqueio do Windows](media/voice-assistants/windows_voice_assistant/lock_screen1.png)

### <a name="above-lock-experience-following-voice-activation"></a>Acima da experiência de bloqueio após a ativação da voz

Quando o ecrã está ligado, a aplicação assistente é ecrã completo sem barra de título acima do ecrã de bloqueio. Visuais maiores e descrições de voz fortes com uma forte interface primária de voz permitem casos em que o cliente está muito longe para ler UI ou tem as mãos ocupadas com outra tarefa (não PC).

Quando o ecrã permanece desligado, a aplicação assistente pode reproduzir um auricular para indicar que o assistente está a ativar e proporcionar uma experiência apenas de voz.

![Screenshot do assistente de voz acima da fechadura](media/voice-assistants/windows_voice_assistant/above_lock_listening.png)

### <a name="dismissal-policies"></a>Políticas de despedimento

O assistente deve implementar a orientação de despedimento nesta secção para facilitar o login dos clientes da próxima vez que quiserem utilizar o seu PC windows. Abaixo estão os requisitos específicos, que o assistente deve implementar:

- **Todas as telas assistentes que mostram acima da fechadura devem conter um X** no direito superior que dispensa o assistente.
- **Premir qualquer tecla também deve descartar a aplicação de assistente**. A entrada do teclado é um sinal tradicional de aplicação de bloqueio que o cliente quer fazer login. Portanto, qualquer entrada de teclado/texto não deve ser direcionada para a aplicação. Em vez disso, a aplicação deve ser auto-descartada quando a entrada do teclado for detetada, para que o cliente possa facilmente iniciar sessão no seu dispositivo.
- **Se o ecrã se desligar, a aplicação deve ser auto-dispensada.** Isto garante que da próxima vez que o cliente utilizar o seu PC, o ecrã de login estará pronto e à sua espera.
- Se a &quot;aplicação&quot;estiver a ser utilizada, pode continuar acima do bloqueio. &quot;em&quot; uso constitui qualquer entrada ou saída. Por exemplo, ao transmitir música ou vídeo, a aplicação pode continuar acima do bloqueio. &quot;Siga&quot; e outros passos de diálogo multiturnos são autorizados a manter a aplicação acima do bloqueio.
- Os detalhes de **implementação sobre a anulação do pedido** podem ser encontrados no guia de [implementação do bloqueio acima indicado](windows-voice-assistants-implementation-guide.md#closing-the-application).

![Screenshot do assistente de voz no Windows antes da ativação](media/voice-assistants/windows_voice_assistant/above_lock_response.png)

![Screenshot do assistente de voz no Windows antes da ativação](media/voice-assistants/windows_voice_assistant/lock_screen2.png)

### <a name="privacy-amp-security-considerations-above-lock"></a>Considerações de segurança de privacidade &amp; acima do bloqueio

Muitos computadores são portáteis, mas nem sempre ao alcance do cliente. Podem ser brevemente deixados em quartos de hotel, assentos de avião ou espaços de trabalho, onde outras pessoas têm acesso físico. Se os assistentes que estão habilitados acima do bloqueio não estiverem &quot;preparados, podem ficar sujeitos à classe dos chamados ataques de [empregadas maléficas.](https://en.wikipedia.org/wiki/Evil_maid_attack) &quot;

Por isso, os assistentes devem seguir as orientações nesta secção para ajudar a manter a experiência segura. A interação acima do bloqueio ocorre quando o utilizador do Windows não é autenticado. Isto significa que, em geral, **a entrada no assistente deve também ser tratada como não autenticada**.

- Os assistentes devem **implementar uma lista branca de habilidades para identificar habilidades confirmadas seguras e seguras** para serem acedidas acima da fechadura.
- As tecnologias de ID do altifalante podem desempenhar um papel na aparação de alguns riscos, mas o ID do Altifalante não é um substituto adequado para a autenticação do Windows.
- O branco-de-habilidade deve considerar três classes de ações ou habilidades:

| **Classe de ação** | **Descrição** | **Exemplos (não uma lista completa)** |
| --- | --- | --- |
| Cofre sem autenticação | Informações sobre finalidade geral ou comando e controlo básicos de aplicações | &quot;Que horas são? &quot;, &quot;Toque a próxima faixa&quot; |
| Cofre com ID do orador | Risco de personificação, revelando informações pessoais. | &quot;Qual é&#39;o meu próximo compromisso? &quot;, &quot;Reveja&quot;a &quot;minha lista de compras, responda à chamada&quot; |
| Cofre apenas após a autenticação do Windows | Ações de alto risco que um intruso poderia usar para prejudicar o cliente | &quot;Comprar mais&quot;compras &quot;, Apagar a&quot; &quot;minha (importante) nomeação, Enviar uma mensagem&quot;de texto (média) , &quot;Lançar uma página (nefasta)&quot; |

Para o caso de Contoso, a informação geral em torno da informação sobre o stock público é segura sem autenticação. Informações específicas do cliente, tais como o número de ações detidas, são provavelmente seguras com id do orador. No entanto, a compra ou venda de ações nunca deve ser permitida sem a autenticação do Windows.

Para garantir ainda mais a experiência, **weblinks ou outros lançamentos de aplicações para aplicações serão sempre bloqueados pelo Windows até que o cliente entre em si.** Como mitigação do último recurso, a Microsoft reserva-se o direito de remover uma aplicação da lista branca de assistentes habilitados se um problema de segurança grave não for abordado atempadamente.

## <a name="design-guidance-for-voice-activation-preview"></a>Orientação de design para pré-visualização de ativação de voz

Abaixo do bloqueio, quando a aplicação assistente _não_ tem foco, o Windows fornece um UI de ativação de voz menos intrusivo para ajudar a manter o cliente em fluxo. Isto é especialmente verdade para o caso de falsas ativações que seriam altamente disruptivas se lançassem a app completa. A ideia principal é que cada assistente tem outra casa na Shell, o ícone assistente da barra de tarefas. Quando ocorre o pedido de ativação de fundo, aparece uma pequena vista acima do ícone da barra de tarefas assistente. Os assistentes devem proporcionar uma pequena experiência de escuta nesta tela. Após o processamento dos pedidos, os assistentes podem optar por redimensionar esta vista para mostrar uma resposta no contexto ou para entregar a sua visão principal da aplicação para mostrar visuais maiores e mais detalhados.

- Para se manter mínimo, a pré-visualização não tem uma barra de título, pelo que **o assistente deve desenhar um X no direito superior para permitir que os clientes rejeitem a vista.** Consulte [o Encerramento do Pedido](windows-voice-assistants-implementation-guide.md#closing-the-application) para as APIs específicas para ligar quando o botão de desmissão for premido.
- Para apoiar as pré-visualizações de ativação de voz, os assistentes podem convidar os clientes a fixar o assistente na barra de tarefas durante a primeira execução.

**Pré-visualização de ativação de voz: Estado inicial**

O assistente de Contoso tem uma casa na barra de tarefas: o seu ícone circular e giratório.

![Screenshot do assistente de voz no Windows como pré-ativação do ícone da barra de tarefas](media/voice-assistants/windows_voice_assistant/pre_compact_view.png)

À medida que a **ativação progride,** o assistente solicita a ativação do fundo. O assistente recebe um pequeno painel de pré-visualização (largura padrão 408 e altura: 248). Se a ativação de voz do lado do servidor determinar que o sinal foi um falso positivo, esta vista pode ser descartada para uma interrupção mínima.

![Screenshot do assistente de voz no Windows em vista compacta enquanto verifica a ativação](media/voice-assistants/windows_voice_assistant/compact_view_activating.png)

**Quando a ativação final for confirmada,** o assistente apresenta o seu UX de escuta. O assistente deve sempre desenhar um X de saque na parte superior direita da pré-visualização de ativação de voz.

![Screenshot do assistente de voz no Windows ouvindo em vista compacta](media/voice-assistants/windows_voice_assistant/compact_view_listening.png)

**Respostas rápidas** podem ser mostradas na pré-visualização de ativação de voz. Um TryResizeView permitirá que os assistentes solicitem diferentes tamanhos.

![Screenshot do assistente de voz no Windows respondendo em vista compacta](media/voice-assistants/windows_voice_assistant/compact_view_response.png)

**Entrega.** Em qualquer momento, o assistente pode entregar a sua visão principal da app para fornecer mais informações, diálogos ou respostas que requerem mais imóveis de ecrã. Consulte a [Transição da visão compacta para a](windows-voice-assistants-implementation-guide.md#transition-from-compact-view-to-full-view) secção de visão completa para detalhes de implementação.

![Imagens do assistente de voz no Windows antes e depois de expandir a vista compacta](media/voice-assistants/windows_voice_assistant/compact_transition.png)

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Começar a desenvolver o seu assistente de voz](how-to-windows-voice-assistants-get-started.md)