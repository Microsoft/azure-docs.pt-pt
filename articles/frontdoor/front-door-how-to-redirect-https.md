---
title: Criar uma porta de entrada com HTTP para redirecionamento a HTTPS com o portal do Azure
description: Saiba como criar uma porta de entrada com o tráfego redirecionado de HTTP para HTTPS com o portal do Azure.
services: front-door
author: sharad4u
ms.service: front-door
ms.topic: article
ms.date: 5/21/2019
ms.author: sharadag
ms.openlocfilehash: a07b19c49630cc925e719aaa1d46476a1edc58f5
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/22/2019
ms.locfileid: "67332607"
---
# <a name="create-a-front-door-with-http-to-https-redirection-using-the-azure-portal"></a>Criar uma porta de entrada com HTTP para redirecionamento a HTTPS com o portal do Azure

Pode utilizar o portal do Azure para criar uma [desde início](front-door-overview.md) com um certificado para a terminação de SSL. Uma regra de roteamento é utilizada para redirecionar o tráfego HTTP para HTTPS.

Neste artigo, vai aprender a:

> [!div class="checklist"]
> * Criar uma porta de entrada com um recurso de aplicação Web existente
> * Adicionar um domínio personalizado com o certificado SSL 
> * Configurar o redirecionamento HTTPS no domínio personalizado

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="create-a-front-door-with-an-existing-web-app-resource"></a>Criar uma porta de entrada com um recurso de aplicação Web existente

1. Inicie sessão no Portal do Azure em [https://portal.azure.com](https://portal.azure.com).
2. Clique em **Criar um recurso**, no canto superior esquerdo do portal do Azure.
3. Procure **porta da frente** utilizando a pesquisa da barra e depois de localizar o tipo de recurso, clique em **criar**.
4. Escolha uma subscrição e, em seguida, utilizar um grupo de recursos existente ou crie um novo. Tenha em atenção de que a localização mais frequentes na interface de Usuário destina-se apenas o grupo de recursos. A configuração de porta de entrada será implementada em todos os [localizações de POP da porta da frente do Azure](https://docs.microsoft.com/azure/frontdoor/front-door-faq#what-are-the-pop-locations-for-azure-front-door-service).

    ![Configurar as noções básicas para a nova porta de entrada](./media/front-door-url-redirect/front-door-create-basics.png)

5. Clique em **seguinte** para introduzir o separador de configuração. A configuração de porta de entrada ocorre em três passos - adicionar um anfitrião de front-end predefinido, a adição de back-ends num conjunto de back-end e, em seguida, criar regras de encaminhamento para mapear o comportamento de encaminhamento de anfitrião de front-end. 

     ![Estruturador de configuração de porta de entrada](./media/front-door-url-redirect/front-door-designer.png)

6. Clique em do ' **+** ' ícone na _anfitriões de front-end_ para criar um anfitrião de front-end, introduza um nome globalmente exclusivo para o anfitrião de front-end predefinido para a porta de entrada (`\<**name**\>.azurefd.net`). Clique em **adicionar** para continuar para o passo seguinte.

     ![Adicionar um anfitrião de front-end](./media/front-door-url-redirect/front-door-create-fehost.png)

7. Clique em do ' **+** ' ícone na _conjuntos back-end_ para criar um conjunto de back-end. Forneça um nome para o conjunto de back-end e, em seguida, clique em '**adicionar um back-end**'.
8. Selecione o tipo de anfitrião de back-end como _serviço de aplicações_. Selecione a subscrição em que a sua aplicação web está alojada e, em seguida, selecione a aplicação web específica na lista pendente para **nome de anfitrião de back-end**.
9. Clique em **Add** para guardar o back-end e clique em **Add** novamente para guardar a configuração de conjunto de back-end.   ![Adicionar um back-end de um conjunto de back-end](./media/front-door-url-redirect/front-door-create-backendpool.png)

10. Clique em do ' **+** ' ícone na _regras de encaminhamento_ para criar uma rota. Forneça um nome para a rota, diga "HttpToHttpsRedirect" e, em seguida, defina o _aceites protocolos_ campo **"Apenas HTTP"** . Certifique-se de que o adequado _anfitrião de front-end_ está selecionada.  
11. Na _detalhes de rota_ secção, defina o _tipo de rota_ para **redirecionar**, certifique-se de que o _tipo de redirecionamento_ está definido como  **Encontrado (302)** e _redirecionar o protocolo_ está definido como **apenas HTTPS**. 
12. Clique em Adicionar para guardar a regra de encaminhamento para HTTP para redirecionamento HTTPS.
     ![Adicionar um HTTP para rotas de redirecionamento HTTPS](./media/front-door-url-redirect/front-door-redirect-config-example.png)
13. Adicione outra regra de encaminhamento para processar o tráfego HTTPS. Clique no ' **+** "início de sessão a _regras de encaminhamento_ e forneça um nome para a rota, diga"DefaultForwardingRoute"e, em seguida, defina o _aceites protocolos_ campo para **"Apenas HTTPS"** . Certifique-se de que o adequado _anfitrião de front-end_ está selecionada.
14. Na secção de detalhes de rota, defina o _tipo de rota_ para **reencaminhar**, certifique-se de que o conjunto de back-end correto está selecionado e o _reencaminhamento protocolo_ está definido como  **Apenas HTTPS**. 
15. Clique em Adicionar para guardar a regra de encaminhamento para o reencaminhamento do pedido.
     ![Adicione uma rota de encaminhamento para tráfego HTTPS](./media/front-door-url-redirect/front-door-forward-route-example.png)
16. Clique em **rever + criar** e, em seguida **criar**, para criar seu perfil de porta de entrada. Vá para o recurso uma vez criado.

## <a name="add-a-custom-domain-to-your-front-door-and-enable-https-on-it"></a>Adicionar um domínio personalizado à sua porta de entrada e ativar o HTTPS no mesmo
Os passos seguintes demonstram como pode adicionar um domínio personalizado num recurso de porta de entrada existente e, em seguida, habilitar HTTP para redirecionamento a HTTPS no mesmo. 

### <a name="add-a-custom-domain"></a>Adicionar um domínio personalizado

Neste exemplo, adicionar um registo CNAME para o `www` subdomínio (por exemplo, `www.contosonews.com`).

#### <a name="create-the-cname-record"></a>Criar o registo CNAME

Adicione um registo CNAME para mapear um subdomínio para o anfitrião de front-end de predefinição da porta da frente (`<name>.azurefd.net`, onde `<name>` é o nome do seu perfil de porta de entrada).

Para o `www.contoso.com` domínio, por exemplo, adicione um registo CNAME que mapeia o nome `www` para `<name>.azurefd.net`.

Depois de adicionar o CNAME, a página de registos DNS terá um aspeto semelhante ao seguinte exemplo:

![Domínio personalizado de CNAME para a porta de entrada](./media/front-door-url-redirect/front-door-dns-cname.png)

#### <a name="onboard-the-custom-domain-on-your-front-door"></a>Carregar o domínio personalizado em sua porta de entrada

1. No separador de designer da porta de entrada, clique em "+" secção de ícone nos anfitriões de front-end para adicionar um novo domínio personalizado. 
2. Introduza o nome DNS personalizado completamente qualificado no campo de nome de anfitrião personalizado, exemplo `www.contosonews.com`. 
3. Depois do mapeamento CNAME do domínio para a porta de entrada é validado, clique em **adicionar** para adicionar o domínio personalizado.
4. Clique em **guardar** para submeter as alterações.

![Menu de domínio personalizado](./media/front-door-url-redirect/front-door-add-custom-domain.png)

### <a name="enable-https-on-your-custom-domain"></a>Ativar o HTTPS no seu domínio personalizado

1. Clique no domínio personalizado que foi adicionado e na secção **HTTPS de domínio personalizado**, altere o estado para **ativado**.
2. Pode deixar a **tipo de gestão de certificado** definida como _porta da frente geridos_ para o certificado gratuito mantido, gerenciado e autorotated por porta de entrada. Também pode optar por utilizar o seu próprio certificado SSL personalizado armazenado com o Azure Key Vault. Este tutorial parte do princípio de que a utilização da porta da frente gerida certificado.
![Ativar HTTPS para o domínio personalizado](./media/front-door-url-redirect/front-door-custom-domain-https.png)

3. Clique em **atualização** para guardar a seleção e, em seguida, clique em **guardar**.
4. Clique em **atualizar** após alguns minutos e, em seguida, clique no domínio personalizado novamente para ver o progresso do aprovisionamento de certificados. 

> [!WARNING]
> Ativar o HTTPS para um domínio personalizado pode demorar vários minutos e também depende de validação de propriedade do domínio se o CNAME não for diretamente mapeado para o anfitrião de porta de entrada `<name>.azurefd.net`. Saiba mais sobre [como ativar o HTTPS num domínio personalizado](./front-door-custom-domain-https.md).

## <a name="configure-the-routing-rules-for-the-custom-domain"></a>Configurar as regras de encaminhamento para o domínio personalizado

1. Clique na regra de encaminhamento de redirecionamento criada anteriormente.
2. Clique no menu pendente para anfitriões de front-end e selecione o seu domínio personalizado para aplicar esta rota para o seu domínio também.
3. Clique em **Atualizar**.
4. Fazer a mesma operação para a outra regra de encaminhamento também ou seja, para a rota de reencaminhamento adicionar o domínio personalizado.
5. Clique em **guardar** para submeter as alterações.

## <a name="next-steps"></a>Passos Seguintes

- Saiba como [criar um Front Door](quickstart-create-front-door.md).
- Saiba [como funciona o Front Door](front-door-routing-architecture.md).
- Saiba mais sobre [URL de redirecionamento na porta da frente](front-door-url-redirect.md).
