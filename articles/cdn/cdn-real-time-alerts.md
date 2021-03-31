---
title: Azure CDN alerta em tempo real | Microsoft Docs
description: Alertas em tempo real no Microsoft Azure CDN. Os alertas em tempo real fornecem notificações sobre o desempenho dos pontos finais no seu perfil de CDN.
services: cdn
documentationcenter: ''
author: zhangmanling
manager: erikre
editor: ''
ms.assetid: 1e85b809-e1a9-4473-b835-69d1b4ed3393
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: 6811a06eb3483fd53b6e566033935c3b2e00ceca
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "84887254"
---
# <a name="real-time-alerts-in-microsoft-azure-cdn"></a>Alertas em tempo real no Microsoft Azure CDN
[!INCLUDE [cdn-premium-feature](../../includes/cdn-premium-feature.md)]

## <a name="overview"></a>Descrição Geral
Este documento explica alertas em tempo real no Microsoft Azure CDN. Esta funcionalidade fornece notificações em tempo real sobre o desempenho dos pontos finais no seu perfil de CDN.  Pode configurar alertas de e-mail ou HTTP com base em:

* Largura de banda
* Código de Estado
* Estatuto cache
* Ligações

## <a name="creating-a-real-time-alert"></a>Criar um alerta em tempo real
1. No [portal Azure,](https://portal.azure.com)navegue pelo seu perfil CDN.
   
    ![Perfil da CDN](./media/cdn-real-time-alerts/cdn-profile-blade.png)
1. A partir da lâmina de perfil CDN, clique no botão **Gerir.**
   
    ![Botão de gestão de perfil CDN](./media/cdn-real-time-alerts/cdn-manage-btn.png)
   
    O portal de gestão cdn abre.
3. Pairar sobre o **separador Analytics,** em seguida, pairar sobre o flyout **de estatísticas em tempo real.**  Clique em **alertas em tempo real.**
   
    ![Portal de gestão CDN](./media/cdn-real-time-alerts/cdn-premium-portal.png)
   
    É apresentada a lista das configurações de alerta existentes (se houver).
4. Clique no botão **Adicionar Alerta.**
   
    ![Adicionar botão de alerta](./media/cdn-real-time-alerts/cdn-add-alert.png)
   
    É apresentado um formulário para criar um novo alerta.
   
    ![Novo formulário de alerta](./media/cdn-real-time-alerts/cdn-new-alert.png)
5. Se pretender que este alerta esteja ativo quando clicar em **Guardar**, consulte a caixa de verificação **'Procurar alerta'.**
6. Introduza um nome descritivo para o seu alerta no campo **Nome.**
7. No **dropdown do tipo de mídia,** selecione **HTTP Large Object**.
   
    ![Tipo de mídia com http objeto grande selecionado](./media/cdn-real-time-alerts/cdn-http-large.png)
   
   > [!IMPORTANT]
   > Tem de selecionar **HTTP Grande Objeto** como Tipo de **Mídia**.  As outras escolhas não são usadas pela **Azure CDN da Verizon.**  A falha na seleção **http Large Object** faz com que o seu alerta nunca seja acionado.
   > 
   > 
8. Crie uma **Expressão** para monitorizar selecionando um **valor métrico,** **operador** e **gatilho**.
   
   * Para **métrica,** selecione o tipo de condição que pretende monitorizada.  **Bandwidth Mbps** é a quantidade de uso de largura de banda em megabits por segundo.  **Total de Conexões** é o número de ligações HTTP simultâneas aos nossos servidores de borda.  Para definições dos vários estados de cache e códigos de estado, consulte [os códigos de estado da cache do Azure CDN](/previous-versions/azure/mt759237(v=azure.100)) e [os códigos de estado do Azure CDN HTTP](/previous-versions/azure/mt759238(v=azure.100))
   * **O operador** é o operador matemático que estabelece a relação entre a métrica e o valor do gatilho.
   * **Trigger Value** é o valor limiar que deve ser cumprido antes de uma notificação ser enviada.
     
     No exemplo seguinte, a expressão criada indica que uma notificação é enviada quando o número de 404 códigos de estado é superior a 25.
     
     ![Expressão da amostra de alerta em tempo real](./media/cdn-real-time-alerts/cdn-expression.png)
9. Para **intervalo,** insira com a frequência com que gostaria que a expressão fosse avaliada.
10. No **Notificação ao** dropdown, selecione quando pretende ser notificado quando a expressão for verdadeira.
    
    * **O Início da Condição** indica que uma notificação é enviada quando a condição especificada é detetada pela primeira vez.
    * **Condition End** indica que uma notificação é enviada quando a condição especificada já não é detetada. Esta notificação só pode ser ativada depois de o nosso sistema de monitorização da rede ter detetado que a condição especificada ocorreu.
    * **Continuamente** indica que uma notificação é enviada cada vez que o sistema de monitorização da rede deteta a condição especificada. Tenha em mente que o sistema de monitorização da rede verifica apenas uma vez por intervalo a condição especificada.
    * **Condição Iniciar e Terminar** indica que uma notificação é enviada pela primeira vez que a condição especificada é detetada e mais uma vez quando a condição já não é detetada.
1. Se pretender receber notificações por e-mail, consulte a Caixa de **Verificação Notifica por Email.**  
    
    ![Notifique por formulário de e-mail](./media/cdn-real-time-alerts/cdn-notify-email.png)
    
    No campo **Para** que possa apresentar o seu endereço de e-mail onde pretende que as notificações são enviadas. Para **Assunto** e **Corpo,** pode deixar o padrão, ou pode personalizar a mensagem utilizando a lista **de palavras-chave Disponível** para inserir dinâmicamente dados de alerta quando a mensagem é enviada.
    
    > [!NOTE]
    > Pode testar a notificação por e-mail clicando no botão **de Notificação** de Teste, mas apenas após a configuração de alerta ter sido guardada.
    > 
    > 
12. Se pretender que as notificações sejam publicadas num servidor web, consulte a **notificação por http postbox.**
    
    ![Notificar por formulário HTTP Post](./media/cdn-real-time-alerts/cdn-notify-http.png)
    
    No campo **Url,** insira o URL onde deseja a mensagem HTTP publicada. Na caixa de texto **dos cabeçalhos,** introduza os cabeçalhos HTTP a enviar no pedido.  Para **o Corpo,** pode personalizar a mensagem utilizando a lista **de palavras-chave disponíveis** para inserir dinamicamente dados de alerta quando a mensagem é enviada.  **Cabeçalhos** e **corpo** padrão para uma carga útil XML semelhante ao seguinte exemplo:
    
    ```
    <string xmlns="http://schemas.microsoft.com/2003/10/Serialization/">
        <![CDATA[Expression=Status Code : 404 per second > 25&Metric=Status Code : 404 per second&CurrentValue=[CurrentValue]&NotificationCondition=Condition Start]]>
    </string>
    ```
    
    > [!NOTE]
    > Pode testar a notificação HTTP Post clicando no botão **de Notificação** de Teste, mas apenas após a configuração de alerta ter sido guardada.
    > 
    > 
13. Clique no botão **Guardar** para guardar a configuração de alerta.  Se verificou **o Alerta Ativado** no passo 5, o seu alerta está agora ativo.

## <a name="next-steps"></a>Passos Seguintes
* Analisar [estatísticas em tempo real em Azure CDN](cdn-real-time-stats.md)
* Cave mais fundo com [relatórios HTTP avançados](cdn-advanced-http-reports.md)
* Analisar [padrões de utilização](cdn-analyze-usage-patterns.md)

