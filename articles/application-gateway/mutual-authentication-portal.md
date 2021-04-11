---
title: Configurar a autenticação mútua no Azure Application Gateway através do portal
description: Saiba como configurar uma Gateway de aplicações para ter autenticação mútua através do portal
services: application-gateway
author: mscatyao
ms.service: application-gateway
ms.topic: how-to
ms.date: 04/02/2021
ms.author: caya
ms.openlocfilehash: b2118a257f029a63445b09dfa618d6e3ceacddda
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/02/2021
ms.locfileid: "106231516"
---
# <a name="configure-mutual-authentication-with-application-gateway-through-portal-preview"></a>Configure a autenticação mútua com o Portal da Aplicação através do portal (Pré-visualização)

Este artigo descreve como usar o portal Azure para configurar a autenticação mútua no seu Gateway de Aplicação. Autenticação mútua significa que o Application Gateway autentica o cliente que envia o pedido utilizando o certificado de cliente que envia para o Gateway de Aplicações. 

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="before-you-begin"></a>Antes de começar

Para configurar a autenticação mútua com um Gateway de aplicação, precisa de um certificado de cliente para fazer o upload para o gateway. O certificado de cliente será usado para validar o certificado que o cliente irá apresentar à Application Gateway. Para efeitos de teste, pode utilizar um certificado auto-assinado. No entanto, isto não é aconselhável para cargas de trabalho de produção, porque são mais difíceis de gerir e não são completamente seguros. 

Para saber mais, especialmente sobre que tipo de certificados de cliente pode carregar, consulte [visão geral da autenticação mútua com o Application Gateway.](./mutual-authentication-overview.md#certificates-supported-for-mutual-authentication)

## <a name="create-a-new-application-gateway"></a>Criar um novo Gateway de Aplicações

Primeiro crie um novo Gateway de aplicações como normalmente faria através do portal - não existem passos adicionais necessários na criação para permitir a autenticação mútua. Para mais informações sobre como criar um Portal de Aplicação Gateway, consulte o nosso [portal quickstart tutorial](./quick-create-portal.md).

## <a name="configure-mutual-authentication"></a>Configurar a autenticação mútua 

Para configurar um Gateway de aplicação existente com autenticação mútua, terá de ir primeiro ao separador **definições SSL (Preview)** no Portal e criar um novo perfil SSL. Quando criar um perfil SSL, verá dois separadores: **Autenticação do Cliente** e **Política SSL**. O **separador autenticação** do cliente é onde irá fazer o upload do certificado(s) do seu cliente. O separador **SSL Policy** consiste em configurar uma política SSL específica do ouvinte - para obter mais informações, verificar [configurar uma política SSL específica do ouvinte](./application-gateway-configure-listener-specific-ssl-policy.md).

> [!IMPORTANT]
> Certifique-se de que carrega toda a cadeia de certificados ca do cliente num ficheiro e apenas uma cadeia por ficheiro.

1. Procure por **Gateway de Aplicação** no portal, selecione **Gateways de aplicação** e clique no gateway de aplicações existente.

2. Selecione **as definições SSL (Pré-visualização)** do menu do lado esquerdo.

3. Clique no sinal de mais ao lado **dos Perfis SSL** no topo para criar um novo perfil SSL.

4. Introduza um nome em **Nome do Perfil SSL**. Neste exemplo, chamamos a nossa aplicação de perfil *SSLGatewaySSLProfile.* 

5. Mantenha-se no separador Autenticação do **Cliente.** Faça o upload do certificado PEM que pretende utilizar para autenticação mútua entre o cliente e o Gateway de aplicações utilizando o botão **de carregamento de um novo** certificado. 

    Para obter mais informações sobre como extrair cadeias de certificados de ca de cliente fidedignos para carregar aqui, consulte [como extrair cadeias de certificados de ca cliente fidedignos](./mutual-authentication-certificate-management.md).

   > [!NOTE]
   > Se este não for o seu primeiro perfil SSL e tiver enviado outros certificados de cliente para o seu Gateway de Aplicações, pode optar por reutilizar um certificado existente no seu gateway através do menu dropdown. 

6. Verifique apenas a caixa **DN do emitente de certificado de cliente** verificar se pretender que o Application Gateway verifique o nome distinto do emitente do certificado de cliente. 

7. Considere adicionar uma política específica de ouvinte. Consulte as instruções para [configurar as políticas SSL específicas do ouvinte](./application-gateway-configure-listener-specific-ssl-policy.md).

8. **Selecione Adicionar** para guardar.
    > [!div class="mx-imgBorder"]
    > ![Adicionar a autenticação do cliente ao perfil SSL](./media/mutual-authentication-portal/mutual-authentication-portal.png)

## <a name="associate-the-ssl-profile-with-a-listener"></a>Associe o perfil SSL a um ouvinte

Agora que criámos um perfil SSL com autenticação mútua configurado, precisamos associar o perfil SSL ao ouvinte para completar a configuração da autenticação mútua. 

1. Navegue até ao gateway de aplicações existente. Se acabou de completar os passos acima, não precisa fazer nada aqui. 

2. **Selecione Os ouvintes** do menu do lado esquerdo. 

3. Clique em **Adicionar ouvinte** se ainda não tiver um ouvinte HTTPS configurado. Se já tiver um ouvinte HTTPS, clique nele na lista. 

4. Preencha o **nome listener**, **Frontend IP,** **Port,** **Protocol** e **outras Definições HTTPS** para se adaptar aos seus requisitos.

5. Verifique a caixa de verificação **do perfil Ativar SSL** para que possa selecionar qual o Perfil SSL para associar ao ouvinte. 

6. Selecione o perfil SSL que acabou de criar a partir da lista de abandono. Neste exemplo, escolhemos o perfil SSL que criamos a partir dos passos anteriores: *applicationGatewaySSLProfile.* 

7. Continue a configurar o restante do ouvinte para se adaptar às suas necessidades. 

8. Clique **em Adicionar** para guardar o seu novo ouvinte com o perfil SSL associado. 

    > [!div class="mx-imgBorder"]
    > ![Perfil SSL associado a novo ouvinte](./media/mutual-authentication-portal/mutual-authentication-listener-portal.png)

## <a name="renew-expired-client-ca-certificates"></a>Renovar certificados de CA de cliente caducado

Caso o certificado de CA do seu cliente tenha expirado, pode atualizar o certificado no seu gateway através dos seguintes passos: 

1. Navegue no seu Gateway de Aplicações e vá ao separador **SSL (Pré-visualização)** no menu da mão esquerda. 
 
1. Selecione o(s) perfil SSL existente com o certificado de cliente caducado. 
 
1. Selecione **Enviar um novo certificado** no separador **Autenticação** do Cliente e faça o upload do seu novo certificado de cliente. 
 
1. Selecione o ícone do caixote do lixo ao lado do certificado caducado. Isto removerá a associação desse certificado do perfil SSL. 

1. Repita os passos 2-4 acima com qualquer outro perfil SSL que estava usando o mesmo certificado de cliente caducado. Poderá escolher o novo certificado que carregou no passo 3 do menu suspenso em outros perfis SSL.

## <a name="next-steps"></a>Passos seguintes

- [Gerir o tráfego da Web com um gateway de aplicação com a CLI do Azure](./tutorial-manage-web-traffic-cli.md)