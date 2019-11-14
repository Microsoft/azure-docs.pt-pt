---
title: Configurar a criptografia SSL de ponta a ponta usando o portal
titleSuffix: Azure Application Gateway
description: Saiba como usar o portal do Azure para criar um gateway de aplicativo com criptografia SSL de ponta a ponta.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 11/14/2019
ms.author: absha
ms.custom: mvc
ms.openlocfilehash: a878b966266bdd326db35d266bc14b2f81161e92
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/14/2019
ms.locfileid: "74075127"
---
# <a name="configure-end-to-end-ssl-by-using-application-gateway-with-the-portal"></a>Configurar o SSL de ponta a ponta usando o gateway de aplicativo com o portal

Este artigo descreve como usar o portal do Azure para configurar a criptografia de protocolo SSL de ponta a ponta (SSL) por meio do SKU do gateway v1 Aplicativo Azure.

> [!NOTE]
> O SKU do gateway de aplicativo v2 requer certificados raiz confiáveis para habilitar a configuração de ponta a ponta.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="before-you-begin"></a>Antes de começar

Para configurar o SSL de ponta a ponta com um gateway de aplicativo, você precisa de um certificado para o gateway. Os certificados também são necessários para os servidores back-end. O certificado de gateway é usado para derivar uma chave simétrica em conformidade com a especificação do protocolo SSL. A chave simétrica é usada para criptografar e descriptografar o tráfego enviado para o gateway. 

Para a criptografia SSL de ponta a ponta, os servidores back-end corretos devem ser permitidos no gateway de aplicativo. Para permitir esse acesso, carregue o certificado público dos servidores back-end, também conhecidos como certificados de autenticação (v1) ou certificados raiz confiáveis (v2), para o gateway de aplicativo. A adição do certificado garante que o gateway de aplicativo se comunique somente com instâncias de back-end conhecidas. Essa configuração protege ainda mais a comunicação de ponta a ponta.

Para saber mais, confira [terminação SSL e SSL de ponta a ponta](https://docs.microsoft.com/azure/application-gateway/ssl-overview).

## <a name="create-a-new-application-gateway-with-end-to-end-ssl"></a>Criar um novo gateway de aplicativo com o SSL de ponta a ponta

Para criar um novo gateway de aplicativo com criptografia SSL de ponta a ponta, primeiro você precisará habilitar o encerramento de SSL ao criar um novo gateway de aplicativo. Essa ação habilita a criptografia SSL para comunicação entre o cliente e o gateway de aplicativo. Em seguida, você precisará colocar nos destinatários confiáveis a lista de certificados para os servidores de back-end nas configurações de HTTP. Essa configuração habilita a criptografia SSL para comunicação entre o gateway de aplicativo e os servidores de back-end. Isso realiza a criptografia SSL de ponta a ponta.

### <a name="enable-ssl-termination-while-creating-a-new-application-gateway"></a>Habilitar terminação SSL ao criar um novo gateway de aplicativo

Para saber mais, confira [habilitar terminação SSL ao criar um novo gateway de aplicativo](https://docs.microsoft.com/azure/application-gateway/create-ssl-portal).

### <a name="add-authenticationroot-certificates-of-back-end-servers"></a>Adicionar certificados de autenticação/raiz de servidores back-end

1. Selecione **todos os recursos**e, em seguida, selecione **myAppGateway**.

2. Selecione **configurações de http** no menu do lado esquerdo. O Azure criou automaticamente uma configuração HTTP padrão, **appgatewaybackendhttp**, quando você criou o gateway de aplicativo. 

3. Selecione **appgatewaybackendhttp**.

4. Em **protocolo**, selecione **https**. Um painel para **certificados de autenticação de back-end ou certificados raiz confiáveis** é exibido.

5. Selecione **Criar novo**.

6. No campo **nome** , insira um nome adequado.

7. Selecione o arquivo de certificado na caixa **carregar certificado cer** .

   Para gateways de aplicativo Standard e WAF (v1), você deve carregar a chave pública do seu certificado de servidor back-end no formato. cer.

   ![Adicionar certificado](./media/end-to-end-ssl-portal/addcert.png)

   Para Standard_v2 e WAF_v2 gateways de aplicativo, você deve carregar o certificado raiz do certificado de servidor back-end no formato. cer. Se o certificado de back-end for emitido por uma autoridade de certificação (CA) conhecida, você poderá marcar a caixa de seleção **usar certificado de autoridade de certificação conhecido** e, em seguida, não precisará carregar um certificado.

   ![Adicionar certificado raiz confiável](./media/end-to-end-ssl-portal/trustedrootcert-portal.png)

   ![Certificado raiz](./media/end-to-end-ssl-portal/trustedrootcert.png)

8. Selecione **Guardar**.

## <a name="enable-end-to-end-ssl-for-an-existing-application-gateway"></a>Habilitar o SSL de ponta a ponta para um gateway de aplicativo existente

Para configurar um gateway de aplicativo existente com a criptografia SSL de ponta a ponta, primeiro você deve habilitar a terminação SSL no ouvinte. Essa ação habilita a criptografia SSL para comunicação entre o cliente e o gateway de aplicativo. Em seguida, coloque esses certificados para servidores back-end nas configurações de HTTP na lista de destinatários seguros. Essa configuração habilita a criptografia SSL para comunicação entre o gateway de aplicativo e os servidores de back-end. Isso realiza a criptografia SSL de ponta a ponta.

Você precisará usar um ouvinte com o protocolo HTTPS e um certificado para habilitar a terminação SSL. Você pode usar um ouvinte existente que atenda a essas condições ou criar um novo ouvinte. Se você escolher a opção anterior, poderá ignorar a seção "Habilitar terminação de SSL em um gateway de aplicativo existente" e mover diretamente para a seção "Adicionar autenticação/certificados raiz confiáveis para servidores de back-end".

Se você escolher a última opção, aplique as etapas no procedimento a seguir.
### <a name="enable-ssl-termination-in-an-existing-application-gateway"></a>Habilitar terminação SSL em um gateway de aplicativo existente

1. Selecione **todos os recursos**e, em seguida, selecione **myAppGateway**.

2. Selecione **ouvintes** no menu do lado esquerdo.

3. Selecione o ouvinte **básico** ou **multissite** , dependendo dos seus requisitos.

4. Em **protocolo**, selecione **https**. Um painel para o **certificado** é exibido.

5. Carregue o certificado PFX que você pretende usar para terminação SSL entre o cliente e o gateway de aplicativo.

   > [!NOTE]
   > Para fins de teste, você pode usar um certificado autoassinado. No entanto, isso não é recomendável para cargas de trabalho de produção, pois elas são mais difíceis de gerenciar e não são totalmente seguras. Para obter mais informações, consulte [criar um certificado autoassinado](https://docs.microsoft.com/azure/application-gateway/create-ssl-portal#create-a-self-signed-certificate).

6. Adicione outras configurações necessárias para o **ouvinte**, dependendo de seus requisitos.

7. Selecione **OK** para guardar.

### <a name="add-authenticationtrusted-root-certificates-of-back-end-servers"></a>Adicionar autenticação/certificados raiz confiáveis de servidores back-end

1. Selecione **todos os recursos**e, em seguida, selecione **myAppGateway**.

2. Selecione **configurações de http** no menu do lado esquerdo. Você pode colocar certificados em uma configuração de HTTP de back-end existente na lista de destinatários seguros ou criar uma nova configuração de HTTP. (Na próxima etapa, o certificado para a configuração HTTP padrão, **appgatewaybackendhttp**, é adicionado à lista de destinatários seguros.)

3. Selecione **appgatewaybackendhttp**.

4. Em **protocolo**, selecione **https**. Um painel para **certificados de autenticação de back-end ou certificados raiz confiáveis** é exibido. 

5. Selecione **Criar novo**.

6. No campo **nome** , insira um nome adequado.

7. Selecione o arquivo de certificado na caixa **carregar certificado cer** .

   Para gateways de aplicativo Standard e WAF (v1), você deve carregar a chave pública do seu certificado de servidor back-end no formato. cer.

   ![Adicionar certificado](./media/end-to-end-ssl-portal/addcert.png)

   Para Standard_v2 e WAF_v2 gateways de aplicativo, você deve carregar o certificado raiz do certificado de servidor back-end no formato. cer. Se o certificado de back-end for emitido por uma autoridade de certificação conhecida, você poderá marcar a caixa de seleção **usar certificado de autoridade de certificação conhecido** e, em seguida, não precisará carregar um certificado.

   ![Adicionar certificado raiz confiável](./media/end-to-end-ssl-portal/trustedrootcert-portal.png)

8. Selecione **Guardar**.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Gerir o tráfego da Web com um gateway de aplicação com a CLI do Azure](./tutorial-manage-web-traffic-cli.md)
