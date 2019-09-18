---
author: cephalin
ms.service: app-service-web
ms.topic: include
ms.date: 11/09/2018
ms.author: cephalin
ms.openlocfilehash: ddeb46a2c7bc7f24f55c22f446926529cee7b598
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/17/2019
ms.locfileid: "71059780"
---
Depois que os registros de seu nome de domínio forem propagados, você poderá usar o navegador para verificar se o nome de domínio personalizado pode ser usado para acessar seu aplicativo Web no serviço Azure App.

> [!NOTE]
> Pode levar algum tempo para que o CNAME se propague pelo sistema DNS. Você pode usar um serviço como <a href="https://www.digwebinterface.com/">https://www.digwebinterface.com/</a> para verificar se o CNAME está disponível.
> 
> 

Se você ainda não adicionou seu aplicativo Web como um ponto de extremidade do Gerenciador de tráfego, você deve fazer isso antes que a resolução de nome funcione, pois o nome de domínio personalizado roteia para o Gerenciador de tráfego. O Gerenciador de tráfego então roteia para seu aplicativo Web. Use as informações em [Adicionar ou excluir pontos](../articles/traffic-manager/traffic-manager-endpoints.md) de extremidade para adicionar seu aplicativo Web como um ponto de extremidades em seu perfil do Gerenciador de tráfego.

> [!NOTE]
> Se seu aplicativo Web não estiver listado ao adicionar um ponto de extremidade, verifique se ele está configurado para o modo de plano do serviço de aplicativo **padrão** . Você deve usar o modo **padrão** para seu aplicativo Web para trabalhar com o Gerenciador de tráfego.
> 
> 

1. No navegador, abra o [portal do Azure](https://portal.azure.com).
2. Na guia **aplicativos Web** , clique no nome do seu aplicativo Web, selecione **configurações**e, em seguida, selecione **domínios personalizados**
   
    ![](./media/custom-dns-web-site/dncmntask-cname-6.png)
3. Na folha **domínios personalizados** , clique em **adicionar nome do host**.
4. Use as caixas de texto nome de **host** para inserir o nome de domínio personalizado a ser associado a este aplicativo Web.
   
    ![](./media/custom-dns-web-site/dncmntask-cname-8.png)
5. Clique em **validar** para salvar a configuração de nome de domínio.
6. Depois de clicar em **validar** , o Azure iniciará o fluxo de trabalho de verificação de domínio. Isso verificará a propriedade do domínio, bem como a disponibilidade do nome do host e o êxito do relatório ou o erro detalhado com orientações prescritivas sobre como corrigir o erro.    
7. Após a validação bem-sucedida, o botão **adicionar nome do host** se tornará ativo e você poderá atribuir o nome do host. Agora, navegue até seu nome de domínio personalizado em um navegador. Agora você deve ver seu aplicativo em execução usando seu nome de domínio personalizado. 
   
   Depois que a configuração for concluída, o nome de domínio personalizado será listado na seção **nomes de domínio** do seu aplicativo Web.

Neste ponto, você deve ser capaz de inserir o nome de domínio do Traffic Manager em seu navegador e ver que ele o leva com êxito ao seu aplicativo Web.

