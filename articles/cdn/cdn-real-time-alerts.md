---
title: Alertas em tempo real do CDN do Azure | Documentos da Microsoft
description: Alertas em tempo real na CDN do Microsoft Azure. Alertas em tempo real proporcionam notificações sobre o desempenho dos pontos finais no seu perfil da CDN.
services: cdn
documentationcenter: ''
author: zhangmanling
manager: erikre
editor: ''
ms.assetid: 1e85b809-e1a9-4473-b835-69d1b4ed3393
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: e20161147aa16456e31aff2bd3cc6337c3690e89
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60335764"
---
# <a name="real-time-alerts-in-microsoft-azure-cdn"></a>Alertas em tempo real na CDN do Microsoft Azure
[!INCLUDE [cdn-premium-feature](../../includes/cdn-premium-feature.md)]

## <a name="overview"></a>Descrição geral
Este documento explica alertas em tempo real na CDN do Microsoft Azure. Esta funcionalidade fornece notificações em tempo real sobre o desempenho dos pontos finais no seu perfil da CDN.  Pode configurar por e-mail ou alertas HTTP com base em:

* Largura de Banda
* Códigos de estado
* Estados da cache
* Ligações

## <a name="creating-a-real-time-alert"></a>Criar um alerta em tempo real
1. Na [portal do Azure](https://portal.azure.com), navegue para o perfil de CDN.
   
    ![Perfil da CDN](./media/cdn-real-time-alerts/cdn-profile-blade.png)
1. No painel de perfil da CDN, clique a **gerir** botão.
   
    ![Botão de gerir o perfil de CDN](./media/cdn-real-time-alerts/cdn-manage-btn.png)
   
    É aberto o portal de gestão da CDN.
3. Paire o rato sobre o **Analytics** separador, em seguida, coloque o cursor sobre o **estatísticas em tempo real** submenu.  Clique em **alertas em tempo real**.
   
    ![Portal de gestão de CDN](./media/cdn-real-time-alerts/cdn-premium-portal.png)
   
    É apresentada a lista de configurações de alerta existentes (se houver).
4. Clique nas **Adicionar alerta** botão.
   
    ![Adicionar botão de alerta](./media/cdn-real-time-alerts/cdn-add-alert.png)
   
    É apresentado um formulário para a criação de um novo alerta.
   
    ![Novo formulário de alerta](./media/cdn-real-time-alerts/cdn-new-alert.png)
5. Se pretender que este alerta ativa quando clica em **salvar**, verifique o **alerta ativado** caixa de verificação.
6. Introduza um nome descritivo para o alerta no **nome** campo.
7. Na **tipo de suporte** menu pendente, selecione **HTTP de objeto grande**.
   
    ![Tipo de suporte de dados com o objeto grande HTTP selecionado](./media/cdn-real-time-alerts/cdn-http-large.png)
   
   > [!IMPORTANT]
   > Tem de selecionar **HTTP de objeto grande** como o **tipo de suporte**.  As outras opções não são utilizadas pelo **CDN do Azure da Verizon**.  Falha ao selecionar **HTTP de objeto grande** faz com que o alerta para nunca ser ativado.
   > 
   > 
8. Criar uma **expressão** para monitorizar, selecionando um **métrica**, **operador**, e **acionar valor**.
   
   * Para **métrica**, selecione o tipo de condição que pretende monitorizados.  **Mbps de largura de banda** é a quantidade de utilização de largura de banda em megabits por segundo.  **Total de ligações** é o número de conexões simultâneas de HTTP para nossos servidores de borda.  Para obter definições de vários Estados de cache e códigos de estado, consulte [códigos de estado de Cache do Azure CDN](/previous-versions/azure/mt759237(v=azure.100)) e [códigos de estado de HTTP de CDN do Azure](/previous-versions/azure/mt759238(v=azure.100))
   * **Operador** é o operador matemático que estabelece a relação entre a métrica e o valor de Acionador.
   * **Acionar o valor** é o valor de limiar que têm de ser cumprido antes de uma notificação é enviada.
     
     No exemplo seguinte, a expressão criada indica que é enviada uma notificação quando o número de códigos de estado 404 é maior do que 25.
     
     ![Expressão de exemplo de alerta em tempo real](./media/cdn-real-time-alerts/cdn-expression.png)
9. Para **intervalo**, introduza a frequência com que pretende que a expressão avaliada.
10. Na **notificar sobre** lista pendente, selecione quando quiser ser notificado quando a expressão for verdadeira.
    
    * **Início de condição** indica que é enviada uma notificação quando a condição especificada for detectada pela primeira vez.
    * **Fim de condição** indica que é enviada uma notificação quando a condição especificada já não é detectada. Esta notificação apenas pode ser acionada quando o nosso sistema de monitoramento de rede detetado que a condição especificada ocorreu.
    * **Contínua** indica que é enviada uma notificação sempre que o sistema de monitorização de rede Deteta a condição especificada. Tenha em atenção que o sistema de monitorização de rede verifica-se apenas uma vez por intervalo para a condição especificada.
    * **Condição de início e de fim** indica que é enviada uma notificação na primeira vez que a condição especificada for detectada e mais uma vez quando a condição é já não é detectada.
1. Se pretender receber notificações por e-mail, verifique os **notificar por E-Mail** caixa de verificação.  
    
    ![Notificar pelo formulário de E-Mail](./media/cdn-real-time-alerts/cdn-notify-email.png)
    
    Na **para** , insira o endereço de e-mail que onde pretende que as notificações enviadas. Para **assunto** e **corpo**, pode deixar a predefinição ou pode personalizar a mensagem utilizando o **palavras-chave disponíveis** lista dinamicamente inserir dados de alertas quando a mensagem é enviada.
    
    > [!NOTE]
    > Pode testar a notificação de e-mail ao clicar o **notificação de teste** botão, mas apenas depois da configuração do alerta foi guardada.
    > 
    > 
12. Se pretender que as notificações a publicar para um servidor web, consulte a **notificar por HTTP Post** caixa de verificação.
    
    ![Notificar pelo formulário de Post de HTTP](./media/cdn-real-time-alerts/cdn-notify-http.png)
    
    Na **Url** , insira o URL publicado por onde pretende que a mensagem HTTP. Na **cabeçalhos** caixa de texto, introduza os cabeçalhos HTTP a ser enviados no pedido.  Para **corpo**, pode personalizar a mensagem utilizando o **palavras-chave disponíveis** lista para inserir dinamicamente os dados de alertas quando a mensagem é enviada.  **Cabeçalhos** e **corpo** padrão para uma transferência XML semelhante ao seguinte exemplo:
    
    ```
    <string xmlns="http://schemas.microsoft.com/2003/10/Serialization/">
        <![CDATA[Expression=Status Code : 404 per second > 25&Metric=Status Code : 404 per second&CurrentValue=[CurrentValue]&NotificationCondition=Condition Start]]>
    </string>
    ```
    
    > [!NOTE]
    > Pode testar a notificação de HTTP Post ao clicar o **notificação de teste** botão, mas apenas depois da configuração do alerta foi guardada.
    > 
    > 
13. Clique nas **guardar** botão para guardar a configuração do alerta.  Se tiver selecionado **alerta ativado** no passo 5, seu alerta está ativo.

## <a name="next-steps"></a>Próximos Passos
* Analisar [estatísticas em tempo real na CDN do Azure](cdn-real-time-stats.md)
* Aprofunde os seus conhecimentos com [relatórios HTTP avançados](cdn-advanced-http-reports.md)
* Analisar [padrões de utilização](cdn-analyze-usage-patterns.md)

