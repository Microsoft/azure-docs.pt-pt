---
title: Capacidades-quadro dos Serviços de Comunicação Azure
titleSuffix: An Azure Communication Services conceptual document
description: Conheça as capacidades do UI Framework
author: ddematheu2
ms.author: dademath
ms.date: 03/10/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: 5b1aab8b38614249d6b502044b5c4c8170f46b3c
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "103492222"
---
# <a name="ui-framework-capabilities"></a>Capacidades de enquadramento da UI

[!INCLUDE [Private Preview Notice](../../includes/private-preview-include.md)]

O Quadro de UI dos Serviços de Comunicação Azure permite-lhe construir experiências de comunicação utilizando um conjunto de componentes reutilizáveis. Estes componentes vêm em dois sabores: Os componentes **base** são os blocos de construção mais básicos da sua experiência de UI, enquanto as combinações destes componentes base são chamadas componentes **compósitos.**

## <a name="ui-framework-composite-components"></a>Componentes compósitos do UI Framework

| Composto               | Description                                               | Web   | Android | iOS   |
|-------------------------|-----------------------------------------------------------|-------|---------|-------|
| Grupo chamando composto | Experiência de chamada de voz e vídeo leve para serviços de comunicação Azure chamando usando ativos de design fluente ui. Suporta a chamada de grupo usando o ID do Grupo de Serviços de Comunicação Azure. O composto permite que uma chamada de um para um seja usada através da referência a uma identidade dos Serviços de Comunicação Azure ou a um número de telefone para a PSTN utilizando um número de telefone adquirido através do Azure.                                    | React |  |  |
| Compósito de chat de grupo    | Experiência de chat leve para serviços de comunicação Azure usando ativos de design fluente ui. Esta experiência concentra-se em oferecer um cliente de chat simples que pode ligar-se aos threads dos Serviços de Comunicação Azure. Permite que os utilizadores enviem mensagens e vejam mensagens recebidas com indicadores de dactilografia e ler recibos. Escala de 1:1 para cenários de chat em grupo. Suporta um único fio de chat.                         | React |  |  |

## <a name="ui-framework-base-components"></a>Componentes de base-quadro da UI

| Componente             | Descrição                                                                                                                                                                                                                                                                        | Web   | Android | iOS |
|-----------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-------|---------|-----|
| Chamar provedor    | Componente de inicialização do núcleo para chamada. Componente necessário para, em seguida, inicializar outros componentes em cima dele. Lida com a lógica principal para inicializar o cliente de chamada usando fichas de acesso dos Serviços de Comunicação Azure. Grupo de Apoios aderir. | React | N/D     | N/D |
| Controlos dos meios de comunicação   | Permite que os utilizadores gerem a chamada atual toggling mute, ligando/desligando o vídeo e terminando a chamada.                                                                                                                                                              | React | N/D     | N/D |
| Galeria dos Media   | Mostrar todos os participantes de chamadas numa única galeria. A galeria suporta participantes ativados por vídeo e estáticos. Para os participantes em vídeo, o vídeo é renderizado.                                                                                                                | React | N/D     | N/D |
| Definições de microfone | Escolha o microfone para ser utilizado para ligar. Este controlo pode ser utilizado antes e durante uma chamada para selecionar o dispositivo do microfone.                                                                                                                                               | React | N/D     | N/D |
| Definições de câmera     | Escolha a câmara para ser usada para chamadas de vídeo. Este controlo pode ser utilizado antes e durante uma chamada para selecionar o dispositivo de vídeo.                                                                                                                                             | React | N/D     | N/D |
| Definições do dispositivo     | Combina configurações de microfone e câmara num único componente                                                                                                 | React | N/D     | N/D |
| Provedor de Chat       | Componente de inicialização do núcleo para o chat. Componente necessário para, em seguida, inicializar outros componentes em cima dele. Lida com a lógica principal para inicializar o cliente de chat com um token de acesso dos Serviços de Comunicação Azure e o fio a que se juntará.                                     | React | N/D     | N/D |
| Enviar Caixa          | Componente de entrada que permite que os utilizadores enviem mensagens para o fio de chat. A entrada suporta texto, hiperligações, emojis e outros caracteres Unicode, incluindo outros alfabetos.                                                                                                                         | React | N/D     | N/D |
| Fio de Chat           | Componente de fio que mostra o utilizador tanto recebido como enviado mensagens com as suas informações de remetente. O fio suporta indicadores de dactilografia e receitas de leitura. Pode deslocar estes fios para rever o histórico de conversas.
| Lista de Participantes      | Mostre todos os participantes da chamada ou do chat thread como uma lista.  | React | N/D     | N/D |

## <a name="ui-framework-capabilities"></a>Capacidades de enquadramento da UI

| Funcionalidade                                                             | Grupo chamando composto | Compósito de chat de grupo | Componentes base |
|---------------------------------------------------------------------|-------------------------|----------------------|-----------------|
| Junte-se ao Encontro de Equipas                                                  |                         |                      |           
| Junte-se ao evento ao vivo das equipas                                               |                         |                      | 
| Inicie chamada VoIP para o utilizador das Equipas                                       |                         |                      | 
| Junte-se a uma conversa de reunião de equipas                                           |                         |                      |            
| Junte-se a Azure Communication Services call com o Grupo Id                | ✔                      |                      | ✔
| Inicie uma chamada VoIP para um ou mais utilizadores dos Serviços de Comunicação Azure |                         |                      |           
| Junte-se a um fio de chat dos Serviços de Comunicação Azure                    |                         | ✔                   | ✔
| Chamada silenciosa/induto                                                    | ✔                       |                      | ✔
| Vídeo ligado/desligado em chamada                                                | ✔                       |                      | ✔
| Partilha de telas                                                      | ✔                       |                      | ✔
| Galeria de participantes                                                 | ✔                       |                      | ✔
| Gestão de microfones                                               | ✔                       |                      | ✔
| Gestão de câmaras                                                   | ✔                       |                      | ✔
| Chamada Lobby                                                          |                         |                      | ✔
| Enviar mensagem de chat                                                   |                         | ✔                   |            
| Receber mensagem de chat                                                |                         | ✔                   | ✔
| Indicadores de dactilografia                                                   |                         | ✔                   | ✔
| Ler Recibo                                                        |                         | ✔                   | ✔
| Lista de Participantes                                                    |                         |                      | ✔


## <a name="customization-support"></a>Suporte à personalização

| Tipo de Componente            | Temas     | Layout                                                              | Modelos de Dados |
|---------------------------|------------|---------------------------------------------------------------------|-------------|
| Componente composto       |     N/D    | N/D                                                                 |     N/D     |
| Componente base            |     N/D    | A disposição dos componentes pode ser modificada usando o estilo externo         |     N/D     |


## <a name="platform-support"></a>Suporte da plataforma

| SDK    | Windows            | macOS                | Ubuntu   | Linux    | Android  | iOS        |
|--------|--------------------|----------------------|----------|----------|----------|------------|
| UI SDK | Chrome \* , novo Edge | Chrome \* , Safari\*\* | Chrome\* | Chrome\* | Chrome\* | Safari\*\* |

\*De notar que a versão mais recente do Chrome é suportada para além das duas versões anteriores.

\*\*Note que as versões Safari 13.1+ são suportadas. O vídeo de saída para o safari macOS ainda não está suportado, mas é suportado no iOS. A partilha de ecrãs de saída só é suportada no iOS do ambiente de trabalho.
