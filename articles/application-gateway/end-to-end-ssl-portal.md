---
title: Configure encriptação TLS de ponta a ponta utilizando o portal
titleSuffix: Azure Application Gateway
description: Aprenda a usar o portal Azure para criar um portal de aplicação com encriptação TLS de ponta a ponta.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 11/14/2019
ms.author: absha
ms.custom: mvc
ms.openlocfilehash: 6f86f32e64bbbe79ea5a403d04f7d6c29ee6b980
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80133017"
---
# <a name="configure-end-to-end-tls-by-using-application-gateway-with-the-portal"></a>Configure TLS de ponta a ponta utilizando o Gateway da Aplicação com o portal

Este artigo descreve como usar o portal Azure para configurar a encriptação de segurança da camada de transporte de ponta a ponta (TLS), anteriormente conhecida como encriptação Secure Sockets Layer (SSL), através do Portal de Aplicações Azure v1 SKU.

> [!NOTE]
> Aplicação Gateway v2 SKU requer certificados de raiz fidedignos para permitir a configuração de ponta a ponta.

Se não tiver uma subscrição Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="before-you-begin"></a>Antes de começar

Para configurar tLS de ponta a ponta com um gateway de aplicação, precisa de um certificado para o gateway. Os certificados também são necessários para os servidores de back-end. O certificado de gateway é utilizado para obter uma chave simétrica em conformidade com a especificação do protocolo TLS. A chave simétrica é então usada para encriptar e desencriptar o tráfego enviado para o portal. 

Para a encriptação TLS de ponta a ponta, os servidores traseiros devem ser autorizados no gateway da aplicação. Para permitir este acesso, faça o upload do certificado público dos servidores back-end, também conhecidos como Certificados de Autenticação (v1) ou Certificados de Raiz Fidedignos (v2), para o gateway da aplicação. A adição do certificado garante que o gateway da aplicação comunica apenas com instâncias conhecidas. Esta configuração assegura ainda a comunicação de ponta a ponta.

Para saber mais, consulte a [visão geral da rescisão do TLS e termina ndo o TLS com gateway](https://docs.microsoft.com/azure/application-gateway/ssl-overview)de aplicação .

## <a name="create-a-new-application-gateway-with-end-to-end-tls"></a>Criar uma nova porta de aplicação com TLS de ponta a ponta

Para criar um novo portal de aplicações com encriptação TLS de ponta a ponta, terá primeiro de ativar a rescisão de TLS enquanto cria um novo portal de aplicações. Esta ação permite a encriptação TLS para comunicação entre o cliente e o gateway da aplicação. Em seguida, terá de colocar na lista de Destinatários Seguros os certificados para os servidores back-end nas definições http. Esta configuração permite a encriptação TLS para comunicação entre o gateway da aplicação e os servidores back-end. Que realiza encriptação TLS de ponta a ponta.

### <a name="enable-tls-termination-while-creating-a-new-application-gateway"></a>Ativar a rescisão de TLS ao criar um novo gateway de aplicação

Para saber mais, consulte [a rescisão de TLS enquanto cria um novo portal de aplicação](https://docs.microsoft.com/azure/application-gateway/create-ssl-portal).

### <a name="add-authenticationroot-certificates-of-back-end-servers"></a>Adicionar certificados de autenticação/raiz de servidores back-end

1. **Selecione todos os recursos**e, em seguida, selecione **myAppGateway**.

2. Selecione **as definições http** do menu do lado esquerdo. O Azure criou automaticamente uma definição PADRÃO HTTP, **appGatewayBackendHttpSettings,** quando criou o gateway da aplicação. 

3. Selecione **appGatewayBackendHttpSettings**.

4. Ao abrigo **do Protocolo,** selecione **HTTPS**. Aparece um painel para certificados de **autenticação Backend ou certificados de raiz fidedignos.**

5. Selecione **Criar novo**.

6. No campo **Nome,** introduza um nome adequado.

7. Selecione o ficheiro de certificado na caixa de **certificados UPLOAD CER.**

   Para gateways de aplicações Standard e WAF (v1), deverá carregar a chave pública do seu certificado de servidor back-end em formato .cer.

   ![Adicionar certificado](./media/end-to-end-ssl-portal/addcert.png)

   Para Standard_v2 e WAF_v2 gateways de aplicação, deve carregar o certificado de raiz do certificado de servidor back-end em formato .cer. Se o certificado de back-end for emitido por uma conhecida autoridade de certificado (CA), pode selecionar a caixa de verificação de **certificados DE CA bem conhecida,** e então não tem de carregar um certificado.

   ![Adicionar certificado raiz fidedigno](./media/end-to-end-ssl-portal/trustedrootcert-portal.png)

   ![Certificado de raiz](./media/end-to-end-ssl-portal/trustedrootcert.png)

8. Selecione **Guardar**.

## <a name="enable-end-to-end-tls-for-an-existing-application-gateway"></a>Ativar TLS de ponta a ponta para um gateway de aplicação existente

Para configurar um portal de aplicação existente com encriptação TLS de ponta a ponta, tem primeiro de ativar a rescisão de TLS no ouvinte. Esta ação permite a encriptação TLS para comunicação entre o cliente e o gateway da aplicação. Em seguida, coloque esses certificados para servidores back-end nas definições HTTP na lista de Destinatários Seguros. Esta configuração permite a encriptação TLS para comunicação entre o gateway da aplicação e os servidores back-end. Que realiza encriptação TLS de ponta a ponta.

Terá de utilizar um ouvinte com o protocolo HTTPS e um certificado para permitir a rescisão de TLS. Pode utilizar um ouvinte existente que satisfaça essas condições ou crie um novo ouvinte. Se escolher a opção anterior, pode ignorar a seguinte secção "Ativar a término TLS numa porta de entrada de aplicação existente" e passar diretamente para a secção "Adicionar certificados de autenticação/raiz fidedignos para servidores de backend".

Se escolher a última opção, aplique os passos no seguinte procedimento.
### <a name="enable-tls-termination-in-an-existing-application-gateway"></a>Ativar a rescisão de TLS em um gateway de aplicação existente

1. **Selecione todos os recursos**e, em seguida, selecione **myAppGateway**.

2. Selecione **Ouvintes** do menu do lado esquerdo.

3. Selecione o ouvinte **Básico** ou **Multi-site,** dependendo dos seus requisitos.

4. Ao abrigo **do Protocolo,** selecione **HTTPS**. Aparece um painel para **certificado.**

5. Faça upload do certificado PFX que pretende utilizar para a rescisão de TLS entre o cliente e o gateway da aplicação.

   > [!NOTE]
   > Para efeitos de teste, pode utilizar um certificado auto-assinado. No entanto, isto não é aconselhável para cargas de trabalho de produção, porque são mais difíceis de gerir e não são completamente seguros. Para mais informações, consulte [a criação de um certificado auto-assinado](https://docs.microsoft.com/azure/application-gateway/create-ssl-portal#create-a-self-signed-certificate).

6. Adicione outras definições necessárias para o **Ouvinte,** dependendo dos seus requisitos.

7. Selecione **OK** para guardar.

### <a name="add-authenticationtrusted-root-certificates-of-back-end-servers"></a>Adicionar certificados de raiz de autenticação/fidedignos de servidores back-end

1. **Selecione todos os recursos**e, em seguida, selecione **myAppGateway**.

2. Selecione **as definições http** do menu do lado esquerdo. Pode colocar certificados numa definição http existente na lista de Destinatários Seguros ou criar uma nova definição http. (No passo seguinte, o certificado para a definição padrão HTTP, **appGatewayBackendHttpSettings,** é adicionado à lista de Destinatários Seguros.)

3. Selecione **appGatewayBackendHttpSettings**.

4. Ao abrigo **do Protocolo,** selecione **HTTPS**. Aparece um painel para certificados de **autenticação Backend ou certificados de raiz fidedignos.** 

5. Selecione **Criar novo**.

6. No campo **Nome,** introduza um nome adequado.

7. Selecione o ficheiro de certificado na caixa de **certificados UPLOAD CER.**

   Para gateways de aplicações Standard e WAF (v1), deverá carregar a chave pública do seu certificado de servidor back-end em formato .cer.

   ![Adicionar certificado](./media/end-to-end-ssl-portal/addcert.png)

   Para Standard_v2 e WAF_v2 gateways de aplicação, deve carregar o certificado de raiz do certificado de servidor back-end em formato .cer. Se o certificado de back-end for emitido por um CONHECIDO CA, pode selecionar a caixa de verificação de **certificados DE CA bem conhecida,** e então não tem de carregar um certificado.

   ![Adicionar certificado raiz fidedigno](./media/end-to-end-ssl-portal/trustedrootcert-portal.png)

8. Selecione **Guardar**.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Gerir o tráfego da Web com um gateway de aplicação com a CLI do Azure](./tutorial-manage-web-traffic-cli.md)
