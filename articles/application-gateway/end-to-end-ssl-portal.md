---
title: Início rápido-configurar a criptografia SSL de ponta a ponta com Aplicativo Azure gateway-portal do Azure | Microsoft Docs
description: Saiba como usar o portal do Azure para criar um gateway de Aplicativo Azure com criptografia SSL de ponta a ponta.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 4/30/2019
ms.author: absha
ms.custom: mvc
ms.openlocfilehash: a37b313bd808ee0441d84ac92050b087eba7ac9d
ms.sourcegitcommit: cd70273f0845cd39b435bd5978ca0df4ac4d7b2c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/18/2019
ms.locfileid: "71097211"
---
# <a name="configure-end-to-end-ssl-by-using-application-gateway-with-the-portal"></a>Configurar o SSL de ponta a ponta usando o gateway de aplicativo com o portal

Este artigo mostra como usar o portal do Azure para configurar a criptografia SSL de ponta a ponta com um SKU do gateway de aplicativo v1.  

> [!NOTE]
> O SKU do gateway de aplicativo v2 requer certificados raiz confiáveis para habilitar a configuração de ponta a ponta.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="before-you-begin"></a>Antes de começar

Para configurar o SSL de ponta a ponta com um gateway de aplicativo, um certificado é necessário para o gateway e os certificados são necessários para os servidores back-end. O certificado de gateway é usado para derivar uma chave simétrica de acordo com a especificação do protocolo SSL. A chave simétrica é usada para criptografar e descriptografar o tráfego enviado para o gateway. Para a criptografia SSL de ponta a ponta, os servidores back-end corretos devem ser permitidos no gateway de aplicativo. Para fazer isso, carregue o certificado público dos servidores back-end, também conhecidos como certificados de autenticação (v1) ou certificados raiz confiáveis (v2), para o gateway de aplicativo. A adição do certificado garante que o gateway de aplicativo se comunique somente com instâncias de back-end conhecidas. Isso protege ainda mais a comunicação de ponta a ponta.

Para saber mais, confira [terminação SSL e SSL de ponta a ponta](https://docs.microsoft.com/azure/application-gateway/ssl-overview).

## <a name="create-a-new-application-gateway-with-end-to-end-ssl"></a>Criar um novo gateway de aplicativo com o SSL de ponta a ponta

Para criar um novo gateway de aplicativo com criptografia SSL de ponta a ponta, primeiro você precisará habilitar o encerramento de SSL ao criar um novo gateway de aplicativo. Isso habilitará a criptografia SSL para a comunicação entre o cliente e o gateway de aplicativo. Em seguida, você precisará colocar os certificados na lista de permissões para servidores de back-end nas configurações de HTTP para habilitar a criptografia SSL para a comunicação entre o gateway de aplicativo e os servidores de back-end, realizando a criptografia SSL de ponta a ponta.

### <a name="enable-ssl-termination-while-creating-a-new-application-gateway"></a>Habilitar terminação SSL ao criar um novo gateway de aplicativo

Consulte este artigo para entender como habilitar o [encerramento de SSL ao criar um novo gateway de aplicativo](https://docs.microsoft.com/azure/application-gateway/create-ssl-portal).

### <a name="add-authenticationroot-certificate-of-back-end-servers"></a>Adicionar certificado de autenticação/raiz de servidores back-end

1. Selecione **todos os recursos**e, em seguida, selecione **myAppGateway**.

2. Selecione **configurações de http** no menu à esquerda. O Azure criou automaticamente uma configuração HTTP padrão, **appgatewaybackendhttp**, quando você criou o gateway de aplicativo. 

3. Selecione **appgatewaybackendhttp**.

4. Em **protocolo**, selecione **https**. Um painel para **certificados de autenticação de back-end ou certificados raiz confiáveis** será exibido. 

5. Escolha **criar novo**.

6. Insira um **nome**adequado.

7. Selecione o arquivo de certificado usando a caixa **carregar certificado cer** .

   Para gateways de aplicativo Standard e WAF (v1), você deve carregar a chave pública do seu certificado de servidor de back-end no formato. cer.

   ![addcert](./media/end-to-end-ssl-portal/addcert.png)

   Para gateways de aplicativo Standard_v2 e WAF_v2, você deve carregar o **certificado raiz** do certificado do servidor de back-end no formato. cer. Se o certificado de back-end for emitido por uma autoridade de certificação conhecida, você poderá marcar a caixa "usar certificado de autoridade de certificação conhecido" e não será necessário carregar um certificado.

   ![addtrustedrootcert](./media/end-to-end-ssl-portal/trustedrootcert-portal.png)

   ![rootcert](./media/end-to-end-ssl-portal/trustedrootcert.png)

8. Selecione **Guardar**.

## <a name="enable-end-to-end-ssl-for-existing-application-gateway"></a>Habilitar o SSL de ponta a ponta para o gateway de aplicativo existente

Para configurar um gateway de aplicativo existente com a criptografia SSL de ponta a ponta, você precisará primeiro habilitar a terminação SSL no ouvinte. Isso habilitará a criptografia SSL para a comunicação entre o cliente e o gateway de aplicativo. Em seguida, você precisará colocar os certificados na lista de permissões para servidores de back-end nas configurações de HTTP para habilitar a criptografia SSL para a comunicação entre o gateway de aplicativo e os servidores de back-end, realizando a criptografia SSL de ponta a ponta.

Você precisará usar um ouvinte com protocolo HTTPS e certificado para habilitar a terminação SSL. Portanto, você pode optar por usar um ouvinte existente com o protocolo HTTPS e o certificado, ou criar um novo ouvinte. No caso de escolher o primeiro, você pode ignorar as etapas mencionadas abaixo para **habilitar o encerramento de SSL no gateway de aplicativo existente** e mover diretamente para a seção **Adicionar autenticação/certificados raiz confiáveis para servidores de back-end** . Se você escolher o último, use estas etapas.

### <a name="enable-ssl-termination-in-existing-application-gateway"></a>Habilitar terminação SSL no gateway de aplicativo existente

1. Selecione **todos os recursos**e, em seguida, selecione **myAppGateway**.

2. Selecione **ouvintes** no menu à esquerda.

3. Escolha entre o ouvinte **básico** e **multissite** de acordo com seu requisito.

4. Em **protocolo**, selecione **https**. Um painel para o **certificado** será exibido.

5. Carregue o certificado PFX que você pretende usar para terminação SSL entre o cliente e o gateway de aplicativo.

   > [!NOTE]
   > Para fins de teste, você pode usar um certificado autoassinado. Mas não é recomendável para cargas de trabalho de produção, pois elas são mais difíceis de gerenciar e não são totalmente seguras. Saiba como [criar um certificado autoassinado](https://docs.microsoft.com/azure/application-gateway/create-ssl-portal#create-a-self-signed-certificate).

6. Adicione outras configurações necessárias para o **ouvinte** de acordo com seu requisito.

7. Selecione **OK** para guardar.

### <a name="add-authenticationtrusted-root-certificates-of-back-end-servers"></a>Adicionar autenticação/certificados raiz confiáveis de servidores back-end

1. Selecione **todos os recursos**e, em seguida, selecione **myAppGateway**.

2. Selecione **configurações de http** no menu à esquerda. Você pode criar certificados de lista de permissões em uma configuração de HTTP de back-end existente ou uma nova configuração de HTTP. Na etapa abaixo, vamos colocar o certificado na lista de permissões para a configuração HTTP padrão, **appgatewaybackendhttp**.

3. Selecione **appgatewaybackendhttp**.

4. Em **protocolo**, selecione **https**. Um painel para **certificados de autenticação de back-end ou certificados raiz confiáveis** será exibido. 

5. Escolha **criar novo**.

6. Insira um **nome**adequado.

7. Selecione o arquivo de certificado usando a caixa **carregar certificado cer** .

   Para gateways de aplicativo Standard e WAF (v1), você deve carregar a chave pública do seu certificado de servidor de back-end no formato. cer.

   ![addcert](./media/end-to-end-ssl-portal/addcert.png)

   Para gateways de aplicativo Standard_v2 e WAF_v2, você deve carregar o **certificado raiz** do certificado do servidor de back-end no formato. cer. Se o certificado de back-end for emitido por uma autoridade de certificação conhecida, você poderá marcar a caixa "usar certificado de autoridade de certificação conhecido" e não será necessário carregar um certificado.

   ![addtrustedrootcert](./media/end-to-end-ssl-portal/trustedrootcert-portal.png)

8. Selecione **Guardar**.

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Gerir o tráfego da Web com um gateway de aplicação com a CLI do Azure](./tutorial-manage-web-traffic-cli.md)
