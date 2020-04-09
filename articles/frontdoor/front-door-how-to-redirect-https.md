---
title: Crie uma Porta Da Frente com http para HTTPS redirecionamento usando o portal Azure
description: Saiba como criar uma Porta Da Frente com tráfego redirecionado de HTTP para HTTPS utilizando o portal Azure.
services: front-door
author: sharad4u
ms.service: frontdoor
ms.topic: article
ms.date: 5/21/2019
ms.author: sharadag
ms.openlocfilehash: f1b8c033a3ec230d60c30f6168de8ce013a80ac6
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/08/2020
ms.locfileid: "80878005"
---
# <a name="create-a-front-door-with-http-to-https-redirection-using-the-azure-portal"></a>Crie uma Porta Da Frente com http para HTTPS redirecionamento usando o portal Azure

Pode utilizar o portal Azure para criar uma [Porta Frontal](front-door-overview.md) com um certificado para a rescisão de TLS. Uma regra de encaminhamento é usada para redirecionar o tráfego HTTP para HTTPS.

Neste artigo, vai aprender a:

> [!div class="checklist"]
> * Crie uma porta da frente com um recurso web app existente
> * Adicione um domínio personalizado com certificado TLS/SSL 
> * Configurar https redirecionamento no domínio personalizado

Se não tiver uma subscrição Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="create-a-front-door-with-an-existing-web-app-resource"></a>Crie uma porta da frente com um recurso web app existente

1. Inscreva-se no portal [https://portal.azure.com](https://portal.azure.com)Azure em .
2. Clique em **Criar um recurso**, no canto superior esquerdo do portal do Azure.
3. Procure a **Porta da Frente** utilizando a barra de pesquisa e assim que encontrar o tipo de recurso, clique em **Criar**.
4. Escolha uma subscrição e, em seguida, use um grupo de recursos existente ou crie um novo. Note que a localização pedida na UI é apenas para o grupo de recursos. A configuração da Porta Frontal será implantada em todas as [localizações POP da Porta Frontal Azure.](front-door-faq.md#what-are-the-pop-locations-for-azure-front-door)

    ![Configurar os fundamentos para a nova Porta da Frente](./media/front-door-url-redirect/front-door-create-basics.png)

5. Clique em **Seguir** para introduzir o separador de configuração. A configuração para porta frontal acontece em três passos - adicionando um hospedeiro frontal padrão, adicionando backends em uma piscina de backend e, em seguida, criando regras de encaminhamento para mapear o comportamento de encaminhamento para o anfitrião frontend. 

     ![Designer de configuração da porta da frente](./media/front-door-url-redirect/front-door-designer.png)

6. Clique no**+** ícone ' ' ' ' nos _anfitriões frontend_ para criar um anfitrião frontend,`\<**name**\>.azurefd.net`insira um nome globalmente único para o seu anfitrião frontal padrão para a sua Porta frontal (). Clique em **Adicionar** para passar ao próximo passo.

     ![Adicione um anfitrião frontend](./media/front-door-url-redirect/front-door-create-fehost.png)

7. Clique no**+** ícone ' ' ' ' nas _piscinas backend_ para criar uma piscina de backend. Forneça um nome para a piscina de backend e, em seguida, clique em '**Adicionar um backend**'.
8. Selecione o Tipo de Anfitrião backend como serviço de _aplicação_. Selecione a subscrição onde a sua aplicação web está hospedada e, em seguida, selecione a aplicação web específica a partir do dropdown para o nome do **anfitrião Backend**.
9. Clique em **Adicionar** para guardar o backend e clique em **Adicionar** novamente para salvar o config de piscina de backend.   ![Adicione uma parte de trás em uma piscina de backend](./media/front-door-url-redirect/front-door-create-backendpool.png)

10. Clique no**+** ícone ' ' ' ' nas _regras de Encaminhamento_ para criar uma rota. Forneça um nome para a rota, diga 'HttpToHttpsRedirect', e, em seguida, definir o campo _de Protocolos Aceites_ para **'http only'.** Certifique-se de que o _hospedeiro frontal_ apropriado é selecionado.  
11. Na secção Detalhes da _Rota,_ detete o _Tipo de Rota_ para **Redirecionar,** certifique-se de que o _tipo de redirecionamento_ está definido para **Found (302)** e _o protocolo de redirecionamento_ está definido **apenas**para HTTPS . 
12. Clique em Adicionar para guardar a regra de encaminhamento para HTTP para Redirecionar HTTPS.
     ![Adicione uma rota de redirecionamento HTTP para HTTPS](./media/front-door-url-redirect/front-door-redirect-config-example.png)
13. Adicione outra regra de encaminhamento para manusear o tráfego HTTPS. Clique no**+**'sinal ' ' nas regras de _Encaminhamento_ e forneça um nome para a rota, diga 'DefaultForwardingRoute', e, em seguida, detete o campo de _Protocolos Aceites_ para **'Apenas HTTPS'.** Certifique-se de que o _hospedeiro frontal_ apropriado é selecionado.
14. Na secção Detalhes da Rota, derpor o _Tipo de Rota_ para A **Frente,** certifique-se de que a piscina traseira direita é selecionada e o Protocolo de _Encaminhamento_ está definido **apenas**para HTTPS . 
15. Clique em Adicionar para guardar a regra de encaminhamento para reencaminhamento de pedidos.
     ![Adicione uma rota para a frente para o tráfego HTTPS](./media/front-door-url-redirect/front-door-forward-route-example.png)
16. Clique em **Rever + criar** e, em seguida, **criar,** para criar o seu perfil porta da frente. Vá ao recurso que uma vez foi criado.

## <a name="add-a-custom-domain-to-your-front-door-and-enable-https-on-it"></a>Adicione um domínio personalizado à sua Porta da Frente e ative HTTPS nele
Os seguintes passos mostram como pode adicionar um domínio personalizado num recurso de porta frontal existente e, em seguida, permitir http para HTTPS redirecioná-lo. 

### <a name="add-a-custom-domain"></a>Adicionar um domínio personalizado

Neste exemplo, adicione um registo CNAME para o `www` `www.contosonews.com`subdomínio (por exemplo, ).

#### <a name="create-the-cname-record"></a>Criar o registo CNAME

Adicione um disco CNAME para mapear um subdomínio ao hospedeiro`<name>.azurefd.net`frontal `<name>` padrão da Porta da Frente ( onde está o nome do seu perfil front door).

Para `www.contoso.com` o domínio, como exemplo, adicione um registo `www` `<name>.azurefd.net`CNAME que mapeie o nome para .

Depois de adicionar o CNAME, a página de registos DNS terá um aspeto semelhante ao seguinte exemplo:

![Domínio personalizado CNAME para porta da frente](./media/front-door-url-redirect/front-door-dns-cname.png)

#### <a name="onboard-the-custom-domain-on-your-front-door"></a>A bordo do domínio personalizado na sua porta da frente

1. No separador de design da Porta Frontal, clique no ícone '+' na secção de anfitriões frontend para adicionar um novo domínio personalizado. 
2. Introduza o nome DNS personalizado totalmente qualificado `www.contosonews.com`no campo de nome do anfitrião personalizado, exemplo . 
3. Assim que o mapeamento CNAME do domínio para a porta da frente for validado, clique em **Adicionar** para adicionar o domínio personalizado.
4. Clique em **Guardar** para submeter as alterações.

![Menu de domínio personalizado](./media/front-door-url-redirect/front-door-add-custom-domain.png)

### <a name="enable-https-on-your-custom-domain"></a>Ativar HTTPS no seu domínio personalizado

1. Clique no domínio personalizado que foi adicionado e sob a secção **Domínio personalizado HTTPS,** altere o estado para **Ativado**.
2. Pode deixar o tipo de **gestão** de Certificado definido para _a Porta Da Frente gerida_ para o certificado gratuito mantido, gerido e rodado automaticamente pela Porta Da Frente. Também pode optar por utilizar o seu próprio certificado Personalizado TLS/SSL armazenado com cofre de chaves Azure. Este tutorial assume que o uso do certificado gerido pela Porta Da Frente.
![Habilitar https para domínio personalizado](./media/front-door-url-redirect/front-door-custom-domain-https.png)

3. Clique em **Atualizar** para salvar a seleção e, em seguida, clique em **Guardar**.
4. Clique em **Refresh** após alguns minutos e, em seguida, clique no domínio personalizado novamente para ver o progresso do fornecimento de certificados. 

> [!WARNING]
> Ativar HTTPS para um domínio personalizado pode demorar vários minutos e também depende da validação da `<name>.azurefd.net`propriedade do domínio se o CNAME não estiver diretamente mapeado para o seu anfitrião porta da frente . Saiba mais sobre [como ativar HTTPS para um domínio personalizado](./front-door-custom-domain-https.md).

## <a name="configure-the-routing-rules-for-the-custom-domain"></a>Configure as regras de encaminhamento para o domínio personalizado

1. Clique na regra de encaminhamento de redirecionamento criada anteriormente.
2. Clique na dropdown para anfitriões Frontend e selecione o seu domínio personalizado para aplicar esta rota para o seu domínio também.
3. Clique em **Atualizar**.
4. Faça a mesma operação para a outra regra de encaminhamento, isto é, para a sua rota de encaminhamento adicionar o domínio personalizado.
5. Clique em **Guardar** para submeter as suas alterações.

## <a name="next-steps"></a>Passos seguintes

- Saiba como [criar um Front Door](quickstart-create-front-door.md).
- Saiba [como funciona o Front Door](front-door-routing-architecture.md).
- Saiba mais sobre [o redirecionamento](front-door-url-redirect.md)de URL na Porta da Frente .
