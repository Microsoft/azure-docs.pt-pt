---
title: Alertas Azure CDN em tempo real / Microsoft Docs
description: Alertas em tempo real no CDN do Microsoft Azure. Os alertas em tempo real fornecem notificações sobre o desempenho dos pontos finais no seu perfil DeCDN.
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
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: 4b8cbc27757cf6c321ea4b3c27720a129aa27c1b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "67593470"
---
# <a name="real-time-alerts-in-microsoft-azure-cdn"></a>Alertas em tempo real no CDN do Microsoft Azure
[!INCLUDE [cdn-premium-feature](../../includes/cdn-premium-feature.md)]

## <a name="overview"></a>Descrição geral
Este documento explica alertas em tempo real no CDN do Microsoft Azure. Esta funcionalidade fornece notificações em tempo real sobre o desempenho dos pontos finais no seu perfil CDN.  Pode configurar e-mails ou alertas HTTP com base em:

* Largura de banda
* Código de Estado
* Estados de Cache
* Ligações

## <a name="creating-a-real-time-alert"></a>Criar um alerta em tempo real
1. No [portal Azure,](https://portal.azure.com)navegue para o seu perfil De CDN.
   
    ![Perfil da CDN](./media/cdn-real-time-alerts/cdn-profile-blade.png)
1. A partir da lâmina de perfil CDN, clique no botão **Gerir.**
   
    ![Botão de gestão de perfil CDN](./media/cdn-real-time-alerts/cdn-manage-btn.png)
   
    Abre o portal de gestão da CDN.
3. Paire sobre o separador **Analytics** e, em seguida, paire sobre as **estatísticas em tempo real.**  Clique em **Alertas em Tempo Real**.
   
    ![Portal de gestão da CDN](./media/cdn-real-time-alerts/cdn-premium-portal.png)
   
    A lista de configurações de alerta existentes (se houver) é apresentada.
4. Clique no botão **Adicionar Alerta.**
   
    ![Adicionar botão alerta](./media/cdn-real-time-alerts/cdn-add-alert.png)
   
    É apresentado um formulário para criar um novo alerta.
   
    ![Novo formulário de Alerta](./media/cdn-real-time-alerts/cdn-new-alert.png)
5. Se pretender que este alerta esteja ativo quando clicar em **Guardar,** verifique a caixa de verificação Ativada pelo **Alerta.**
6. Introduza um nome descritivo para o seu alerta no campo **Nome.**
7. No downdown do **Tipo de Mídia,** selecione **HTTP Large Object**.
   
    ![Tipo de mídia com objeto grande HTTP selecionado](./media/cdn-real-time-alerts/cdn-http-large.png)
   
   > [!IMPORTANT]
   > Deve selecionar **http large object** como o Tipo de **Mídia**.  As outras escolhas não são utilizadas pelo **Azure CDN da Verizon.**  A não seleção de **HTTP Large Object** faz com que o seu alerta nunca seja acionado.
   > 
   > 
8. Crie uma **Expressão** para monitorizar selecionando um valor **métrico,** **operador**e **gatilho.**
   
   * Para **Métrica,** selecione o tipo de condição que deseja monitorizada.  **Largura de banda Mbps** é a quantidade de uso da largura de banda em megabits por segundo.  **Total de Ligações** é o número de ligações HTTP simultâneas aos nossos servidores de borda.  Para definições dos vários estados de cache e códigos de estado, consulte Códigos de Estado de [Cache Azure CDN](/previous-versions/azure/mt759237(v=azure.100)) e Códigos de [Estado Do CDN HTTP](/previous-versions/azure/mt759238(v=azure.100))
   * **O operador** é o operador matemático que estabelece a relação entre a métrica e o valor do gatilho.
   * **Trigger Value** é o valor-limiar que deve ser cumprido antes de uma notificação ser enviada.
     
     No exemplo seguinte, a expressão criada indica que uma notificação é enviada quando o número de 404 códigos de estado é superior a 25.
     
     ![Expressão da amostra de alerta em tempo real](./media/cdn-real-time-alerts/cdn-expression.png)
9. Para **intervalos,** introduza a frequência com que gostaria que a expressão fosse avaliada.
10. No **Notificado sobre** a entrega, selecione quando gostaria de ser notificado quando a expressão for verdadeira.
    
    * **Condição Início** indica que uma notificação é enviada quando a condição especificada é detetada pela primeira vez.
    * **Condição Fim** indica que uma notificação é enviada quando a condição especificada já não é detetada. Esta notificação só pode ser desencadeada depois de o nosso sistema de monitorização de rede ter detetado que a condição especificada ocorreu.
    * **A continuação** indica que uma notificação é enviada sempre que o sistema de monitorização da rede deteta a condição especificada. Tenha em mente que o sistema de monitorização da rede verifica apenas uma vez por intervalo para a condição especificada.
    * **Condição Início e Fim** indica que uma notificação é enviada pela primeira vez que a condição especificada é detetada e mais uma vez quando a condição já não é detetada.
1. Se quiser receber notificações por e-mail, consulte o **'Notificar' por e-mail.**  
    
    ![Notificação por formulário de e-mail](./media/cdn-real-time-alerts/cdn-notify-email.png)
    
    No campo **Para ver,** insira o endereço de e-mail onde pretende que sejam enviadas. Para **O Assunto** e o **Corpo,** pode deixar o padrão ou personalizar a mensagem utilizando a lista de **palavras-chave disponíveis** para inserir dados de alerta de forma dinâmica quando a mensagem é enviada.
    
    > [!NOTE]
    > Pode testar a notificação de e-mail clicando no botão **de notificação** de teste, mas apenas depois de a configuração do alerta ter sido guardada.
    > 
    > 
12. Se pretender que as notificações sejam publicadas num servidor web, consulte o **Notificar por HTTP Post checkbox.**
    
    ![Notificar por http formulário post](./media/cdn-real-time-alerts/cdn-notify-http.png)
    
    No campo **Url,** introduza o URL onde pretende que a mensagem HTTP seja publicada. Na caixa de texto **Headers,** introduza os cabeçalhos HTTP a enviar no pedido.  Para **o Corpo,** pode personalizar a mensagem utilizando a lista de **palavras-chave disponíveis** para inserir dados de alerta de forma dinâmica quando a mensagem é enviada.  **Headers** e **Body** predefinidos para uma carga útil XML semelhante ao seguinte exemplo:
    
    ```
    <string xmlns="http://schemas.microsoft.com/2003/10/Serialization/">
        <![CDATA[Expression=Status Code : 404 per second > 25&Metric=Status Code : 404 per second&CurrentValue=[CurrentValue]&NotificationCondition=Condition Start]]>
    </string>
    ```
    
    > [!NOTE]
    > Pode testar a notificação do HTTP Post clicando no botão de notificação de **teste,** mas apenas depois de a configuração do alerta ter sido guardada.
    > 
    > 
13. Clique no botão **Guardar** para salvar a configuração do alerta.  Se verificou o **alerta ativado** no passo 5, o alerta está agora ativo.

## <a name="next-steps"></a>Passos Seguintes
* Analise [estatísticas em tempo real no Azure CDN](cdn-real-time-stats.md)
* Cave mais fundo com [relatórios avançados](cdn-advanced-http-reports.md) do HTTP
* Analisar padrões de [utilização](cdn-analyze-usage-patterns.md)

