---
title: Criar uma porta frontal com https para reorientação HTTPS usando o portal Azure
description: Saiba como criar uma Porta frontal com tráfego redirecionado de HTTP para HTTPS utilizando o portal Azure.
services: front-door
author: duongau
ms.service: frontdoor
ms.topic: how-to
ms.date: 5/21/2019
ms.author: duau
ms.openlocfilehash: fe2159f0eeb9d01081e6a25e7a88ceff4f1e361c
ms.sourcegitcommit: 5a3b9f35d47355d026ee39d398c614ca4dae51c6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/02/2020
ms.locfileid: "89399695"
---
# <a name="create-a-front-door-with-http-to-https-redirection-using-the-azure-portal"></a>Criar uma porta frontal com https para reorientação HTTPS usando o portal Azure

Pode utilizar o portal Azure para criar uma [Porta frontal](front-door-overview.md) com um certificado para a rescisão de TLS. Uma regra de encaminhamento é utilizada para redirecionar o tráfego HTTP PARA HTTPS.

Neste artigo, vai aprender a:

> [!div class="checklist"]
> * Criar uma Porta frontal com um recurso de Aplicação Web existente
> * Adicione um domínio personalizado com certificado TLS/SSL 
> * Configurar HTTPS redirecionar no domínio personalizado

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="create-a-front-door-with-an-existing-web-app-resource"></a>Criar uma Porta frontal com um recurso de Aplicação Web existente

1. Inicie sessão no Portal do Azure em [https://portal.azure.com](https://portal.azure.com).
2. Clique em **Criar um recurso**, no canto superior esquerdo do portal do Azure.
3. Procure a **Porta Frontal** utilizando a barra de pesquisa e assim que encontrar o tipo de recurso, clique em **Criar**.
4. Escolha uma subscrição e, em seguida, utilize um grupo de recursos existente ou crie um novo. Nota: a localização pedida na UI é apenas para o grupo de recursos. A configuração da porta da frente será implantada em todas as [localizações POP da Porta Frontal do Azure](front-door-faq.md#what-are-the-pop-locations-for-azure-front-door).

    ![Configurar o básico para a nova Porta da Frente](./media/front-door-url-redirect/front-door-create-basics.png)

5. Clique em **Seguir** para introduzir o separador de configuração. A configuração para Porta Frontal acontece em três passos - adicionando um anfitrião frontal padrão, adicionando backends em uma piscina de backend e, em seguida, criando regras de encaminhamento para mapear o comportamento de encaminhamento para o anfitrião frontal. 

     ![Designer de configuração da porta da frente](./media/front-door-url-redirect/front-door-designer.png)

6. Clique no **+** ícone ' ' nos _anfitriões Frontend_ para criar um anfitrião frontend, insira um nome globalmente único para o seu anfitrião frontal padrão para a sua Porta frontal ( `\<**name**\>.azurefd.net` ). Clique **em Adicionar** para proceder ao próximo passo.

     ![Adicione um anfitrião frontend](./media/front-door-url-redirect/front-door-create-fehost.png)

7. Clique no **+** ícone ' ' nas _piscinas backend_ para criar uma piscina de backend. Forneça um nome para a piscina de backend e, em seguida, clique em '**Adicionar um backend**'.
8. Selecione o Tipo anfitrião backend como _serviço de aplicação_. Selecione a subscrição onde a sua aplicação web está hospedada e, em seguida, selecione a aplicação web específica a partir do dropdown para **o nome de anfitrião backend**.
9. Clique **em Adicionar** para guardar o backend e clique em **Adicionar** novamente para salvar o backend pool config.   ![Adicione um backend em uma piscina de backend](./media/front-door-url-redirect/front-door-create-backendpool.png)

10. Clique no **+** ícone ' ' nas _regras de encaminhamento_ para criar uma rota. Fornecer um nome para a rota, dizer 'HttpToHttpsRedirect', e, em seguida, definir o campo _protocolos aceites_ para **'apenas HTTP'.** Certifique-se de que o _anfitrião frontal_ apropriado é selecionado.  
11. Na secção Detalhes da _Rota,_ desagure o _Tipo de Rota_ para **Redirecionamento,** certifique-se de que o _tipo de redirecionamento_ está definido para **Found (302)** e _o protocolo de redirecionamento_ é definido **apenas**para HTTPS . 
12. Clique em Adicionar para guardar a regra de encaminhamento para HTTPS redirecionamento.
     ![Adicione uma rota de redirecionamento HTTPS a HTTPS](./media/front-door-url-redirect/front-door-redirect-config-example.png)
13. Adicione outra regra de encaminhamento para manusear o tráfego HTTPS. Clique no **+** sinal ' ' nas _regras de encaminhamento_ e forneça um nome para a rota, diga 'DefaultForwardingRoute', e, em seguida, descreva o campo _de Protocolos Aceites_ para **'apenas HTTPS'.** Certifique-se de que o _anfitrião frontal_ apropriado é selecionado.
14. Na secção 'Detalhes da Rota', desacione o _Tipo de Rota_ para **encaminhar**, certifique-se de que o pool de backend direito é selecionado e o _Protocolo de Encaminhamento_ é definido apenas para **HTTPS**. 
15. Clique em Adicionar para guardar a regra de encaminhamento para reencaminhamento de pedido.
     ![Adicione uma rota para a frente para tráfego HTTPS](./media/front-door-url-redirect/front-door-forward-route-example.png)
16. Clique **em Rever + criar** e, em seguida, **criar,** para criar o seu perfil porta frontal. Vá para o recurso uma vez criado.

## <a name="add-a-custom-domain-to-your-front-door-and-enable-https-on-it"></a>Adicione um domínio personalizado à sua porta frontal e ative HTTPS nele
Os passos a seguir mostram como pode adicionar um domínio personalizado num recurso da Porta Frontal existente e, em seguida, ativar https para redirecionar HTTPS nele. 

### <a name="add-a-custom-domain"></a>Adicionar um domínio personalizado

Neste exemplo, adiciona-se um registo CNAME para o `www` subdomínio (por exemplo, `www.contosonews.com` ).

#### <a name="create-the-cname-record"></a>Criar o registo CNAME

Adicione um registo CNAME para mapear um subdomínio ao anfitrião frontal da porta da frente `<name>.azurefd.net` (, onde `<name>` está o nome do seu perfil da porta da frente).

Para o `www.contoso.com` domínio, como exemplo, adicione um registo CNAME que mapeie o nome `www` para `<name>.azurefd.net` .

Depois de adicionar o CNAME, a página de registos DNS terá um aspeto semelhante ao seguinte exemplo:

![Domínio personalizado CNAME para porta da frente](./media/front-door-url-redirect/front-door-dns-cname.png)

#### <a name="onboard-the-custom-domain-on-your-front-door"></a>A bordo do domínio personalizado na sua Porta da Frente

1. No separador designer da Porta Frontal, clique no ícone '+' na secção frontend hosts para adicionar um novo domínio personalizado. 
2. Introduza o nome DNS personalizado totalmente qualificado no campo de nome de anfitrião personalizado, por exemplo `www.contosonews.com` . 
3. Uma vez validado o mapeamento CNAME do domínio para a porta frontal, clique em **Adicionar** o domínio personalizado.
4. Clique em **Guardar** para submeter as alterações.

![Menu de domínio personalizado](./media/front-door-url-redirect/front-door-add-custom-domain.png)

### <a name="enable-https-on-your-custom-domain"></a>Ative HTTPS no seu domínio personalizado

1. Clique no domínio personalizado que foi adicionado e na secção **Domínio personalizado HTTPS,** altere o estado de **Ativação**.
2. Pode deixar o **tipo de gestão do Certificado** definido para Porta frontal _gerido_ para o certificado gratuito mantido, gerido e autonotado pela Porta da Frente. Também pode optar por utilizar o seu certificado TLS/SSL personalizado armazenado com cofre de chave Azure. Este tutorial pressupõe que o uso do certificado gerido front door.
![Ativar HTTPS para domínio personalizado](./media/front-door-url-redirect/front-door-custom-domain-https.png)

3. Clique em **Update** para guardar a seleção e, em seguida, clique em **Guardar**.
4. Clique em **Refresh** após alguns minutos e, em seguida, clique novamente no domínio personalizado para ver o progresso do provisionamento do certificado. 

> [!WARNING]
> Permitir o HTTPS para um domínio personalizado pode demorar vários minutos e também depende da validação da propriedade do domínio se o CNAME não estiver diretamente mapeado para o anfitrião da porta da frente `<name>.azurefd.net` . Saiba mais sobre [como ativar HTTPS para um domínio personalizado.](./front-door-custom-domain-https.md)

## <a name="configure-the-routing-rules-for-the-custom-domain"></a>Configurar as regras de encaminhamento para o domínio personalizado

1. Clique na regra de encaminhamento de redirecionamento criada anteriormente.
2. Clique no dropdown para anfitriões Frontend e selecione o seu domínio personalizado para aplicar esta rota também para o seu domínio.
3. Clique em **Atualizar**.
4. Faça a mesma operação para a outra regra de encaminhamento, isto é, para que a sua rota de encaminhamento adicione o domínio personalizado.
5. Clique em **Guardar** para submeter as suas alterações.

## <a name="next-steps"></a>Próximos passos

- Saiba como [criar um Front Door](quickstart-create-front-door.md).
- Saiba [como funciona o Front Door](front-door-routing-architecture.md).
- Saiba mais sobre [o redirecionamento de URL na Porta frontal.](front-door-url-redirect.md)
