---
title: Configurar encriptação TLS de ponta a ponta utilizando o portal
titleSuffix: Azure Application Gateway
description: Aprenda a usar o portal Azure para criar um gateway de aplicações com encriptação TLS de ponta a ponta.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: how-to
ms.date: 11/14/2019
ms.author: absha
ms.custom: mvc
ms.openlocfilehash: 6e2d1828acefacb03cc2f42193b8cd8897578b6f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "93397506"
---
# <a name="configure-end-to-end-tls-by-using-application-gateway-with-the-portal"></a>Configurar o TLS ponto a ponto com o Gateway de Aplicação através do portal

Este artigo descreve como usar o portal Azure para configurar a encriptação de segurança da camada de transporte (TLS) de ponta a ponta, anteriormente conhecida como encriptação Secure Sockets Layer (SSL), através do Azure Application Gateway v1 SKU.

> [!NOTE]
> O Application Gateway v2 SKU requer certificados de raiz fidedignos para permitir a configuração de ponta a ponta.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="before-you-begin"></a>Antes de começar

Para configurar o TLS de ponta a ponta com um gateway de aplicação, precisa de um certificado para o gateway. São também necessários certificados para os servidores de back-end. O certificado gateway é utilizado para obter uma chave simétrica em conformidade com a especificação do protocolo TLS. A chave simétrica é então usada para encriptar e desencriptar o tráfego enviado para o portal. 

Para a encriptação TLS de ponta a ponta, os servidores de back-end direito devem ser permitidos no gateway de aplicações. Para permitir este acesso, faça o upload do certificado público dos servidores back-end, também conhecidos como Certificados de Autenticação (v1) ou Certificados de Raiz Fidedigna (v2), para o gateway de aplicações. A adição do certificado garante que o gateway de aplicação comunica apenas com instâncias de back-end conhecidas. Esta configuração assegura ainda mais a comunicação de ponta a ponta.

Para saber mais, consulte [a visão geral da rescisão do TLS e termine o TLS com o Gateway de aplicações.](./ssl-overview.md)

## <a name="create-a-new-application-gateway-with-end-to-end-tls"></a>Criar uma nova porta de aplicações com TLS de ponta a ponta

Para criar um novo gateway de aplicações com encriptação TLS de ponta a ponta, terá de ativar primeiro a rescisão de TLS enquanto cria um novo gateway de aplicações. Esta ação permite a encriptação TLS para comunicação entre o cliente e o gateway de aplicações. Em seguida, terá de colocar na lista de destinatários seguros os certificados para os servidores de back-end nas definições HTTP. Esta configuração permite a encriptação TLS para comunicação entre o gateway de aplicações e os servidores back-end. Isso realiza a encriptação TLS de ponta a ponta.

### <a name="enable-tls-termination-while-creating-a-new-application-gateway"></a>Ativar a rescisão do TLS ao criar um novo gateway de aplicações

Para saber mais, consulte [ativar a rescisão do TLS enquanto cria um novo gateway de aplicações.](./create-ssl-portal.md)

### <a name="add-authenticationroot-certificates-of-back-end-servers"></a>Adicionar certificados de autenticação/raiz de servidores de back-end

1. Selecione **Todos os recursos** e, em seguida, selecione **myAppGateway**.

2. Selecione **as definições HTTP** do menu do lado esquerdo. O Azure criou automaticamente uma definição HTTP predefinida, **appGatewayBackendHttpSettings,** quando criou o gateway de aplicações. 

3. Selecione **appGatewayBackendHttpSettings**.

4. Ao abrigo **do Protocolo**, selecione **HTTPS**. Aparece um painel para **certificados de autenticação backend ou certificados de raiz fidedignos.**

5. Selecione **Criar novo**.

6. No campo **Nome,** insira um nome adequado.

7. Selecione o ficheiro de certificado na caixa **de certificados Upload CER.**

   Para os gateways de aplicações Standard e WAF (v1), deverá carregar a chave pública do certificado de servidor back-end em formato .cer.

   ![Adicionar um certificado](./media/end-to-end-ssl-portal/addcert.png)

   Para Standard_v2 e WAF_v2 gateways de aplicações, deverá carregar o certificado raiz do certificado de servidor back-end em formato .cer. Se o certificado back-end for emitido por uma conhecida autoridade de certificados (CA), pode selecionar a caixa de verificação **do Certificado AC Use Well Known** e, em seguida, não tem de fazer o upload de um certificado.

   ![Adicionar certificado de raiz fidedigno](./media/end-to-end-ssl-portal/trustedrootcert-portal.png)

   ![Certificado de raiz](./media/end-to-end-ssl-portal/trustedrootcert.png)

8. Selecione **Guardar**.

## <a name="enable-end-to-end-tls-for-an-existing-application-gateway"></a>Ativar o TLS de ponta a ponta para um gateway de aplicações existente

Para configurar um gateway de aplicações existente com encriptação TLS de ponta a ponta, tem primeiro de ativar a terminação do TLS no ouvinte. Esta ação permite a encriptação TLS para comunicação entre o cliente e o gateway de aplicações. Em seguida, coloque os certificados para servidores de back-end nas definições HTTP na lista de Destinatários Seguros. Esta configuração permite a encriptação TLS para comunicação entre o gateway de aplicações e os servidores back-end. Isso realiza a encriptação TLS de ponta a ponta.

Terá de utilizar um ouvinte com o protocolo HTTPS e um certificado para permitir a rescisão de TLS. Pode utilizar um ouvinte existente que satisfaça essas condições ou crie um novo ouvinte. Se escolher a opção anterior, pode ignorar a seguinte secção "Ativar a rescisão de TLS numa secção de gateway de aplicações existente" e mover-se diretamente para a secção "Adicionar certificados de raiz de autenticação/fidedignos fidedignos para servidores de backend".

Se escolher esta última opção, aplique os passos no seguinte procedimento.
### <a name="enable-tls-termination-in-an-existing-application-gateway"></a>Ativar a rescisão de TLS num gateway de aplicações existente

1. Selecione **Todos os recursos** e, em seguida, selecione **myAppGateway**.

2. **Selecione Os ouvintes** do menu do lado esquerdo.

3. Selecione **o ouvinte básico** ou **multi-site,** dependendo dos seus requisitos.

4. Ao abrigo **do Protocolo**, selecione **HTTPS**. Aparece um painel para **o Certificado.**

5. Faça o upload do certificado PFX que pretende utilizar para a rescisão de TLS entre o cliente e o gateway de aplicações.

   > [!NOTE]
   > Para efeitos de teste, pode utilizar um certificado auto-assinado. No entanto, isto não é aconselhável para cargas de trabalho de produção, porque são mais difíceis de gerir e não são completamente seguros. Para mais informações, consulte [criar um certificado auto-assinado.](./create-ssl-portal.md#create-a-self-signed-certificate)

6. Adicione outras definições necessárias para o **Ouvinte,** dependendo dos seus requisitos.

7. Selecione **OK** para guardar.

### <a name="add-authenticationtrusted-root-certificates-of-back-end-servers"></a>Adicionar certificados de autenticação/raiz fidedignos de servidores de back-end

1. Selecione **Todos os recursos** e, em seguida, selecione **myAppGateway**.

2. Selecione **as definições HTTP** do menu do lado esquerdo. Pode colocar certificados numa definição HTTP existente na lista de destinatários seguros ou criar uma nova definição HTTP. (No passo seguinte, o certificado para a definição HTTP predefinida, **appGatewayBackendHttpSettings,** é adicionado à lista de destinatários seguros.)

3. Selecione **appGatewayBackendHttpSettings**.

4. Ao abrigo **do Protocolo**, selecione **HTTPS**. Aparece um painel para **certificados de autenticação backend ou certificados de raiz fidedignos.** 

5. Selecione **Criar novo**.

6. No campo **Nome,** insira um nome adequado.

7. Selecione o ficheiro de certificado na caixa **de certificados Upload CER.**

   Para os gateways de aplicações Standard e WAF (v1), deverá carregar a chave pública do certificado de servidor back-end em formato .cer.

   ![Adicionar um certificado](./media/end-to-end-ssl-portal/addcert.png)

   Para Standard_v2 e WAF_v2 gateways de aplicações, deverá carregar o certificado raiz do certificado de servidor back-end em formato .cer. Se o certificado back-end for emitido por um CA bem conhecido, pode selecionar a caixa de verificação **do Certificado CA De Utilização Bem Conhecida** e, em seguida, não tem de carregar um certificado.

   ![Adicionar certificado de raiz fidedigno](./media/end-to-end-ssl-portal/trustedrootcert-portal.png)

8. Selecione **Guardar**.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Gerir o tráfego da Web com um gateway de aplicação com a CLI do Azure](./tutorial-manage-web-traffic-cli.md)