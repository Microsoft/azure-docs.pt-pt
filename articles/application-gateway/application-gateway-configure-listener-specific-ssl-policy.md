---
title: Configure políticas SSL específicas do ouvinte em Azure Application Gateway através do portal
description: Saiba como configurar políticas SSL específicas do ouvinte no Portal de Aplicações através do portal
services: application-gateway
author: mscatyao
ms.service: application-gateway
ms.topic: how-to
ms.date: 03/30/2021
ms.author: caya
ms.openlocfilehash: b0bda1921c63b4e353365c3b391e17f620740396
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/02/2021
ms.locfileid: "106231575"
---
# <a name="configure-listener-specific-ssl-policies-on-application-gateway-through-portal-preview"></a>Configure políticas SSL específicas do ouvinte no Gateway de aplicação através do portal (Pré-visualização)

Este artigo descreve como usar o portal Azure para configurar políticas SSL específicas do ouvinte no seu Gateway de Aplicação. As políticas SSL específicas do ouvinte permitem configurar ouvintes específicos para utilizar diferentes políticas SSL uns dos outros. Você ainda será capaz de definir uma política SSL padrão que todos os ouvintes usarão a menos que seja substituído pela política SSL específica do ouvinte. 

> [!NOTE]
> Apenas Standard_v2 e WAF_v2 skus apoiam políticas específicas do ouvinte, uma vez que as políticas específicas do ouvinte fazem parte dos perfis SSL, e os perfis SSL são suportados apenas em gateways v2. 



Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="create-a-new-application-gateway"></a>Criar um novo Gateway de Aplicações

Primeiro crie um novo Gateway de aplicações como normalmente faria através do portal - não há passos adicionais necessários na criação para configurar políticas SSL específicas do ouvinte. Para mais informações sobre como criar um Portal de Aplicação Gateway, consulte o nosso [portal quickstart tutorial](./quick-create-portal.md).

## <a name="set-up-a-listener-specific-ssl-policy"></a>Criar uma política SSL específica para os ouvintes

Para configurar uma política SSL específica para o ouvinte, terá de ir primeiro ao separador **definições SSL (Preview)** no Portal e criar um novo perfil SSL. Quando criar um perfil SSL, verá dois separadores: **Autenticação do Cliente** e **Política SSL**. O separador **SSL Policy** consiste em configurar uma política SSL específica do ouvinte. O **separador autenticação** do Cliente é onde fazer o upload de um ou de certificado de cliente para autenticação mútua - para mais informações, consulte [configurar uma autenticação mútua](./mutual-authentication-portal.md).

> [!NOTE]
> Recomendamos a utilização do TLS 1.2, uma vez que o TLS 1.2 será mandatado no futuro. 

1. Procure por **Gateway de Aplicação** no portal, selecione **Gateways de aplicação** e clique no gateway de aplicações existente.

2. Selecione **as definições SSL (Pré-visualização)** do menu do lado esquerdo.

3. Clique no sinal de mais ao lado **dos Perfis SSL** no topo para criar um novo perfil SSL.

4. Introduza um nome em **Nome do Perfil SSL**. Neste exemplo, chamamos a nossa aplicação de perfil *SSLGatewaySSLProfile.* 

5. Vá ao separador **SSL Policy** e verifique a caixa **de Política SSL específica do ouvinte.** 

6. Desacrie a sua política SSL específica para o ouvinte, dadas as suas necessidades. Pode escolher entre políticas SSL predefinidas e personalizar a sua própria política SSL. Para obter mais informações sobre as políticas SSL, visite [a visão geral da política da SSL](./application-gateway-ssl-policy-overview.md). Recomendamos a utilização de TLS 1.2

7. **Selecione Adicionar** para guardar.

    > [!NOTE]
    > Não é necessário configurar a autenticação do cliente num perfil SSL para associá-la a um ouvinte. Pode ter apenas configuração de autenticação do cliente, ou apenas configurar a política SSL específica do ouvinte, ou ambas configuradas no seu perfil SSL.  

    ![Adicione a política SSL específica do ouvinte ao perfil SSL](./media/application-gateway-configure-listener-specific-ssl-policy/listener-specific-ssl-policy-ssl-profile.png)
    
## <a name="associate-the-ssl-profile-with-a-listener"></a>Associe o perfil SSL a um ouvinte

Agora que criámos um perfil SSL com uma política SSL específica do ouvinte, precisamos associar o perfil SSL ao ouvinte para colocar a política específica do ouvinte em ação. 

1. Navegue até ao gateway de aplicações existente. Se acabou de completar os passos acima, não precisa fazer nada aqui. 

2. **Selecione Os ouvintes** do menu do lado esquerdo. 

3. Clique em **Adicionar ouvinte** se ainda não tiver um ouvinte HTTPS configurado. Se já tiver um ouvinte HTTPS, clique nele na lista. 

4. Preencha o **nome listener**, **Frontend IP,** **Port,** **Protocol** e **outras Definições HTTPS** para se adaptar aos seus requisitos.

5. Verifique a caixa de verificação **do perfil Ativar SSL** para que possa selecionar qual o Perfil SSL para associar ao ouvinte. 

6. Selecione o perfil SSL que criou a partir da lista de dropdown. Neste exemplo, escolhemos o perfil SSL que criamos a partir dos passos anteriores: *applicationGatewaySSLProfile.* 

7. Continue a configurar o restante do ouvinte para se adaptar às suas necessidades. 

8. Clique **em Adicionar** para guardar o seu novo ouvinte com o perfil SSL associado. 

    ![Perfil SSL associado a novo ouvinte](./media/mutual-authentication-portal/mutual-authentication-listener-portal.png)        

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Gerir o tráfego da Web com um gateway de aplicação com a CLI do Azure](./tutorial-manage-web-traffic-cli.md)