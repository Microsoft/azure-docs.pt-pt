---
title: Criar um gateway de Aplicativo Azure de investigação personalizado-portal do Azure | Microsoft Docs
description: Saiba como criar uma investigação personalizada para o gateway de aplicativo usando o portal
services: application-gateway
documentationcenter: na
author: vhorne
manager: jpconnock
editor: ''
tags: azure-resource-manager
ms.assetid: 33fd5564-43a7-4c54-a9ec-b1235f661f97
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/26/2017
ms.author: victorh
ms.openlocfilehash: b92b9d953b6dd941b8b5f445ad64059f557c2980
ms.sourcegitcommit: 388c8f24434cc96c990f3819d2f38f46ee72c4d8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/27/2019
ms.locfileid: "70061804"
---
# <a name="create-a-custom-probe-for-application-gateway-by-using-the-portal"></a>Criar uma investigação personalizada para o gateway de aplicativo usando o portal

> [!div class="op_single_selector"]
> * [Azure portal](application-gateway-create-probe-portal.md)
> * [Azure Resource Manager PowerShell](application-gateway-create-probe-ps.md)
> * [Azure Classic PowerShell](application-gateway-create-probe-classic-ps.md)

Neste artigo, você adiciona uma investigação personalizada a um gateway de aplicativo existente por meio do portal do Azure. Investigações personalizadas são úteis para aplicativos que têm uma página de verificação de integridade específica ou para aplicativos que não fornecem uma resposta bem-sucedida no aplicativo Web padrão.

## <a name="before-you-begin"></a>Antes de começar

Se você ainda não tiver um gateway de aplicativo, visite [criar um gateway de aplicativo](application-gateway-create-gateway-portal.md) para criar um gateway de aplicativo com o qual trabalhar.

## <a name="createprobe"></a>Criar a investigação

As investigações são configuradas em um processo de duas etapas por meio do Portal. A primeira etapa é criar a investigação. Na segunda etapa, você adiciona a investigação às configurações de http de back-end do gateway de aplicativo.

1. Inicie sessão no [Portal do Azure](https://portal.azure.com). Se você ainda não tiver uma conta, poderá se inscrever para uma [avaliação gratuita de um mês](https://azure.microsoft.com/free)

1. No painel portal do Azure favoritos, clique em todos os recursos. Clique no gateway de aplicativo na folha todos os recursos. Se a assinatura que você selecionou já tiver vários recursos, você poderá inserir partners.contoso.net no filtro por nome... para aceder facilmente ao gateway de aplicação.

1. Clique em **investigações** e clique no botão **Adicionar** para adicionar uma investigação.

   ![Adicionar folha de investigação com informações preenchidas][1]

1. Na folha **Adicionar investigação de integridade** , preencha as informações necessárias para a investigação e, quando concluir, clique em **OK**.

   |**Definição** | **Valor** | **Detalhes**|
   |---|---|---|
   |**Name**|customProbe|Esse valor é um nome amigável para a investigação que é acessível no Portal.|
   |**Protocolo**|HTTP ou HTTPS | O protocolo usado pela investigação de integridade.|
   |**Anfitrião**|, contoso.com|Esse valor é o nome do host que é usado para a investigação. Aplicável somente quando multissite está configurado no gateway de aplicativo; caso contrário, use ' 127.0.0.1 '. Esse valor é diferente do nome de host da VM.|
   |**Caminho**|/ou outro caminho|O restante da URL completa para a investigação personalizada. Um caminho válido começa com '/'. Para o caminho padrão de http:\//contoso.com, basta usar '/' |
   |**Intervalo (segundos)**|30|Com que frequência a investigação é executada para verificar a integridade. Não é recomendável definir o menor que 30 segundos.|
   |**Tempo limite (segundos)**|30|A quantidade de tempo que a investigação aguarda antes de atingir o tempo limite. O intervalo de tempo limite precisa ser alto o suficiente para que uma chamada http possa ser feita para garantir que a página de integridade de back-end esteja disponível.|
   |**Limite não íntegro**|3|Número de tentativas com falha a serem consideradas não íntegras. O limite pode ser definido como 1 ou mais.|

   > [!IMPORTANT]
   > O nome do host não é o mesmo que o nome do servidor. Esse valor é o nome do host virtual em execução no servidor de aplicativos. A investigação é enviada para a porta de:(http://(nome do host) de httpsetting)/urlPath

## <a name="add-probe-to-the-gateway"></a>Adicionar investigação ao gateway

Agora que a investigação foi criada, é hora de adicioná-la ao gateway. As configurações de investigação são definidas nas configurações de http de back-end do gateway de aplicativo.

1. Clique em **configurações de http** no gateway de aplicativo para exibir a folha de configuração, clique nas configurações de http de back-end atuais listadas na janela.

   ![janela configurações de https][2]

1. Na folha configurações do **appgatewaybackendhttp** , marque a caixa de seleção **usar investigação personalizada** e escolha a investigação criada na seção [criar a investigação](#createprobe) no menu suspenso **investigação personalizada** .
   Ao concluir, clique em **salvar** e as configurações serão aplicadas.

A investigação padrão verifica o acesso padrão ao aplicativo Web. Agora que uma investigação personalizada foi criada, o gateway de aplicativo usa o caminho personalizado definido para monitorar a integridade dos servidores de back-end. Com base nos critérios definidos, o gateway de aplicativo verifica o caminho especificado na investigação. Se a chamada para host: porta/caminho não retornar uma resposta de status HTTP 200-399, o servidor será retirado da rotação depois que o limite não íntegro for atingido. A investigação continua na instância não íntegra para determinar quando ela se torna íntegra novamente. Depois que a instância é adicionada novamente ao pool de servidores íntegros, o tráfego começa a fluir para ele novamente e a investigação da instância continua no intervalo especificado pelo usuário normalmente.

## <a name="next-steps"></a>Passos Seguintes

Para saber como configurar o descarregamento de SSL com Aplicativo Azure gateway, consulte [Configurar descarregamento SSL](application-gateway-ssl-portal.md)

[1]: ./media/application-gateway-create-probe-portal/figure1.png
[2]: ./media/application-gateway-create-probe-portal/figure2.png

