---
author: cephalin
ms.service: app-service-web
ms.topic: include
ms.date: 11/09/2018
ms.author: cephalin
ms.openlocfilehash: 67b9c0ba2566206b0e70db51844b21e5d5d3c261
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66136310"
---
Depois dos registos para o seu nome de domínio terem sido propagados, deve conseguir utilizar o browser para verificar que o seu nome de domínio personalizado pode ser utilizado para aceder à sua aplicação web no serviço de aplicações do Azure.

> [!NOTE]
> Pode demorar algum tempo para seu CNAME ser propagado pelo sistema DNS. Pode utilizar um serviço, tal como <a href="https://www.digwebinterface.com/"> https://www.digwebinterface.com/ </a> para verificar se o CNAME está disponível.
> 
> 

Se já não adicionou a aplicação web como um ponto de final do Gestor de tráfego, tem de o fazer antes de resolução de nomes irão funcionar, como as rotas de nome de domínio personalizado para o Gestor de tráfego. O Gestor de tráfego, em seguida, encaminha para a aplicação web. Utilize as informações em [adicionar ou eliminar pontos finais](../articles/traffic-manager/traffic-manager-endpoints.md) para adicionar a sua aplicação web como um ponto final no perfil do Traffic Manager.

> [!NOTE]
> Se a sua aplicação web não estiver listada, ao adicionar um ponto de extremidade, certifique-se de que está configurado para **padrão** modo de plano de serviço de aplicações. Tem de utilizar **padrão** modo para a sua aplicação web para que funcionem com o Gestor de tráfego.
> 
> 

1. No browser, abra a [Portal do Azure](https://portal.azure.com).
2. Na **aplicações Web** separador, clique no nome da sua aplicação web, selecione **definições**e, em seguida, selecione **domínios personalizados**
   
    ![](./media/custom-dns-web-site/dncmntask-cname-6.png)
3. Na **domínios personalizados** painel, clique em **Adicionar nome de anfitrião**.
4. Utilize o **Hostname** caixas de texto para introduzir o nome de domínio do Gestor de tráfego para associar esta aplicação web.
   
    ![](./media/custom-dns-web-site/dncmntask-cname-8.png)
5. Clique em **Validate** para guardar a configuração de nome de domínio.
6. Após clicar em **Validate** Azure para iniciar fluxo de trabalho de verificação de domínio. Isto irá procurar de propriedade do domínio, bem como de êxito de disponibilidade e o relatório de nome de anfitrião ou de erro detalhado com orientação prescritiva sobre como resolver o erro.    
7. Após a validação com êxito **Adicionar nome de anfitrião** botão irá tornar-se ativo e será capaz de atribuir o nome do anfitrião. Agora, navegue para o seu nome de domínio personalizado num navegador. Deverá ver a execução de aplicação com o seu nome de domínio personalizado. 
   
   Depois de concluída a configuração, o nome de domínio personalizado será listado na **nomes de domínio** secção da sua aplicação web.

Neste momento, deve ser capaz de introduzir o nome de domínio do Gestor de tráfego no seu browser e ver que isso com êxito leva à sua aplicação web.

