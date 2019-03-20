---
title: Início rápido - configurar a encriptação de SSL de ponta a ponta com o Gateway de aplicação do Azure - portal do Azure | Documentos da Microsoft
description: Saiba como utilizar o portal do Azure para criar um Gateway de aplicação do Azure com a encriptação SSL ponto a ponto.
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 3/19/2019
ms.author: absha
ms.custom: mvc
ms.openlocfilehash: e47a3e1231701f3339057e25ee4388aff0c9fbd7
ms.sourcegitcommit: 12d67f9e4956bb30e7ca55209dd15d51a692d4f6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58227663"
---
# <a name="configure-end-to-end-ssl-by-using-application-gateway-with-the-portal"></a>Configurar SSL ponto a ponto com o Gateway de aplicação com o portal

Este artigo mostra-lhe como utilizar o portal do Azure para configurar a encriptação de SSL de ponto a ponto com um gateway de aplicação v1 SKU.  

> [!NOTE]
> SKU de v2 do Gateway de aplicação requer certificados de raiz fidedigna para ativar configuração de ponto-a-ponto. Suporte do portal de adição de certificados de raiz fidedigna ainda não está disponível. Portanto, em caso de V2 SKU ver [configurar o SSL ponto a ponto com o PowerShell](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell).

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="before-you-begin"></a>Antes de começar

Para configurar o SSL ponto a ponto com um gateway de aplicação, é necessário para o gateway de um certificado e certificados são necessários para os servidores de back-end. O certificado do gateway é utilizado para derivar uma chave simétrica de acordo com a especificação de protocolo SSL. A chave simétrica, em seguida, é utilizada para encriptar e desencriptar o tráfego enviado para o gateway. Para encriptação SSL de ponto-a-ponto, o back-end tem de estar na lista de permissões com o gateway de aplicação. Para fazer isso, carregue o certificado público dos servidores back-end, também conhecido como certificados de autenticação, para o gateway de aplicação. A adicionar o certificado garante que o gateway de aplicação comunica apenas com instâncias de back-end conhecidas. Isso protege ainda mais a comunicação de ponto-a-ponto.

Para obter mais informações, consulte [SSL terminação e o SSL ponto a ponto](https://docs.microsoft.com/azure/application-gateway/ssl-overview).

## <a name="create-a-new-application-gateway-with-end-to-end-ssl"></a>Criar um novo gateway de aplicação com SSL de ponta a ponta

Para criar um novo gateway de aplicação com a encriptação SSL ponto a ponto, terá de ativar primeiro a terminação de SSL ao criar um novo gateway de aplicação. Isso permitirá a encriptação SSL para a comunicação entre o gateway de cliente e a aplicação. Em seguida, terá da lista branca certificados para servidores de back-end nas definições de HTTP para ativar a encriptação SSL para a comunicação entre servidores de gateway e o back-end de aplicações, portanto, ao realizar a encriptação SSL ponto a ponto.

### <a name="enable-ssl-termination-while-creating-a-new-application-gateway"></a>Ativar a terminação de SSL ao criar um novo gateway de aplicação

Consulte este artigo para compreender como [ativar a terminação de SSL ao criar um novo gateway de aplicação](https://docs.microsoft.com/azure/application-gateway/create-ssl-portal).

### <a name="whitelist-certificates-for-backend-servers"></a>Lista de permissões certificados para servidores de back-end

1. Selecione **todos os recursos**e, em seguida, selecione **myAppGateway**.

2. Selecione **definições de HTTP** no menu à esquerda. Azure criada automaticamente uma definição de HTTP de predefinição **appGatewayBackendHttpSettings**, ao criar o gateway de aplicação. 

3. Selecione **appGatewayBackendHttpSettings**.

4. Sob **protocolo**, selecione **HTTPS**. Um painel para **certificados de autenticação de back-end** aparecerá. 

5. Sob **certificados de autenticação de back-end**, escolha **criar nova**.

6. Introduza adequado **nome**.

7. Carregar o certificado com o **certificado CER carregar** caixa.![ addcert](./media/end-to-end-ssl-portal/addcert.png)

   > [!NOTE]
   > O certificado fornecido neste passo deve ser a chave pública do certificado. pfx presente no back-end. Exportar o certificado (não o certificado de raiz) instalado no servidor de back-end no formato de afirmação, evidência e Raciocinando (CER) e utilizá-lo neste passo. Este passo coloca o back-end com o gateway de aplicação.

8. Selecione **Guardar**.

## <a name="enable-end-to-end-ssl-for-existing-application-gateway"></a>Ativar o SSL de ponta a ponta para o gateway de aplicação existente

Para configurar um gateway de aplicação existente com a encriptação SSL ponto a ponto, precisará a terminação de SSL de ativar primeiro no serviço de escuta. Isso permitirá a encriptação SSL para a comunicação entre o gateway de cliente e a aplicação. Em seguida, terá da lista branca certificados para servidores de back-end nas definições de HTTP para ativar a encriptação SSL para a comunicação entre servidores de gateway e o back-end de aplicações, portanto, ao realizar a encriptação SSL ponto a ponto.

Terá de utilizar um serviço de escuta com o protocolo HTTPS e o certificado para ativar a terminação de SSL. Não é possível alterar o protocolo de um serviço de escuta. Por conseguinte, pode optar por utilizar um serviço de escuta com o protocolo HTTPS e o certificado ou criar um novo serviço de escuta. No caso de escolher o primeiro, pode ignorar o abaixo mencionado passos para **terminação de ativar o SSL no gateway de aplicação existente** e mova diretamente para **lista branca certificados para servidores de back-end** secção. Se escolher o último, efetue estes passos. 

### <a name="enable-ssl-termination-in-existing-application-gateway"></a>Ativar a terminação de SSL no gateway de aplicação existente

1. Selecione **todos os recursos**e, em seguida, selecione **myAppGateway**.

2. Selecione **serviços de escuta** no menu à esquerda.

3. Escolha entre **básica** e **multilocal** serviço de escuta de acordo com seus requisitos.

4. Sob **protocolo**, selecione **HTTPS**. Um painel para **certificado** aparecerá.

5. Carregue o certificado PFX que pretende utilizar para a terminação de SSL entre o gateway de cliente e a aplicação.

   > [!NOTE]
   > Para fins de teste, pode utilizar um certificado autoassinado. Não deve utilizar um certificado autoassinado para cargas de trabalho de produção. Saiba como [criar um certificado autoassinado](https://docs.microsoft.com/azure/application-gateway/create-ssl-portal#create-a-self-signed-certificate).

6. Adicionar outras definições necessárias para o **serviço de escuta** de acordo com seus requisitos.

7. Selecione **OK** para guardar.

### <a name="whitelist-certificates-for-backend-servers"></a>Lista de permissões certificados para servidores de back-end

1. Selecione **todos os recursos**e, em seguida, selecione **myAppGateway**.

2. Selecione **definições de HTTP** no menu à esquerda. Qualquer um dos certificados de lista de permissões num HTTP de back-end existente pode definir ou criar uma nova definição de HTTP. Na abaixo passo, o certificado da lista de permissões para a definição de HTTP padrão, iremos **appGatewayBackendHttpSettings**.

3. Selecione **appGatewayBackendHttpSettings**.

4. Sob **protocolo**, selecione **HTTPS**. Um painel para **certificados de autenticação de back-end** aparecerá. 

5. Sob **certificados de autenticação de back-end**, escolha **criar nova**.

6. Introduza adequado **nome**.

7. Carregar o certificado com o **certificado CER carregar** caixa.![ addcert](./media/end-to-end-ssl-portal/addcert.png)

   > [!NOTE]
   > O certificado fornecido neste passo deve ser a chave pública do certificado. pfx presente no back-end. Exportar o certificado (não o certificado de raiz) instalado no servidor de back-end no formato de afirmação, evidência e Raciocinando (CER) e utilizá-lo neste passo. Este passo coloca o back-end com o gateway de aplicação.

8. Selecione **Guardar**.

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Gerir o tráfego da Web com um gateway de aplicação com a CLI do Azure](./tutorial-manage-web-traffic-cli.md)
