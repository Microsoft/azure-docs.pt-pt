---
title: Criar uma sonda personalizada - Gateway de aplicação do Azure - Portal do Azure | Documentos da Microsoft
description: Saiba como criar uma sonda personalizada para o Gateway de aplicação com o portal
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
ms.openlocfilehash: 8e98b50e936ba97881e2937a50eb474d57a24a05
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58107768"
---
# <a name="create-a-custom-probe-for-application-gateway-by-using-the-portal"></a>Criar uma sonda personalizada para o Gateway de aplicação com o portal

> [!div class="op_single_selector"]
> * [Portal do Azure](application-gateway-create-probe-portal.md)
> * [Azure Resource Manager PowerShell](application-gateway-create-probe-ps.md)
> * [Azure Classic PowerShell](application-gateway-create-probe-classic-ps.md)

Neste artigo, é possível adicionar uma sonda personalizada para um gateway de aplicação existente através do portal do Azure. As pesquisas personalizadas são úteis para aplicações que têm uma página de verificação de estado de funcionamento específico ou para aplicativos que não fornecem uma resposta com êxito na aplicação web predefinida.

## <a name="before-you-begin"></a>Antes de começar

Se ainda não tiver um gateway de aplicação, visite [criar um Gateway de aplicação](application-gateway-create-gateway-portal.md) para criar um gateway de aplicação para trabalhar com.

## <a name="createprobe"></a>Criar a sonda

As pesquisas são configuradas num processo de dois passos através do portal. A primeira etapa é criar a sonda. Na segunda etapa, é possível adicionar a pesquisa para as definições de http de back-end do gateway de aplicação.

1. Inicie sessão no [Portal do Azure](https://portal.azure.com). Se ainda não tiver uma conta, pode inscrever-se para obter um [durante um mês avaliação gratuita](https://azure.microsoft.com/free)

1. No painel favoritos de portal do Azure, clique em todos os recursos. Clique no gateway de aplicação no painel todos os recursos. Se a subscrição que selecionou já tiver vários recursos, pode introduzir partners.contoso.net no filtro por nome... para aceder facilmente ao gateway de aplicação.

1. Clique em **sondas** e clique nas **Add** botão para adicionar uma pesquisa.

   ![Adicionar sonda painel com informações preenchidas][1]

1. Sobre o **adicionar sonda de estado de funcionamento** painel, preencha as informações necessárias para a sonda e quando terminar clique **OK**.

   |**Definição** | **Valor** | **Detalhes**|
   |---|---|---|
   |**Nome**|customProbe|Este valor é um nome amigável para a sonda que seja acessível no portal.|
   |**Protocolo**|HTTP ou HTTPS | O protocolo que usa a sonda de estado de funcionamento.|
   |**Anfitrião**|ou seja contoso.com|Este valor é o nome de anfitrião que é utilizado para a sonda. Aplicável apenas quando vários sites está configurada no Gateway de aplicação, caso contrário, utilize "127.0.0.1". Este valor é diferente do nome de anfitrião VM.|
   |**Caminho**|/ ou outro caminho|O restante do url completo para a sonda personalizada. Um caminho válido começa com "/". Para o caminho predefinido de http://contoso.com simplesmente usar '/' |
   |**Intervalo (seg)**|30|A frequência com que a sonda é executada para verificar a existência de estado de funcionamento. Não é recomendado para definir o inferior a 30 segundos.|
   |**Tempo limite (segundos)**|30|A quantidade de tempo que a sonda tem de aguardar que o tempo limite. O intervalo de tempo limite tem de ser suficientemente alto para que uma chamada http pode ser feita para garantir que a página de estado de funcionamento do back-end está disponível.|
   |**Limiar de mau estado de funcionamento**|3|Número de tentativas falhadas para ser considerado em mau estado de funcionamento. Limiar de 0 significa que se uma verificação de estado de funcionamento falhar back-end é determinado mau estado de funcionamento imediatamente.|

   > [!IMPORTANT]
   > O nome do anfitrião não é o mesmo nome de servidor. Este valor é o nome do anfitrião virtual em execução no servidor de aplicativos. A sonda é enviada para http://(host name):(port from httpsetting)/urlPath

## <a name="add-probe-to-the-gateway"></a>Adicionar sonda ao gateway

Agora que criou a sonda, chegou a hora para adicioná-lo para o gateway. Definições de pesquisa são definidas nas configurações de http de back-end do gateway de aplicação.

1. Clique em **definições de HTTP** no gateway de aplicação abrir o painel Configuração, clique em back-end atual das definições de http listados na janela.

   ![janela de configurações de HTTPS][2]

1. No **appGatewayBackEndHttpSettings** painel de definições, verifique o **sonda personalizada de utilização** caixa de verificação e escolha a sonda criada no [criar a sonda de](#createprobe) seção sobre o  **Sonda personalizada** pendente....
   Quando terminar, clique em **guardar** e as definições são aplicadas.

A sonda predefinida verifica o acesso de predefinido para a aplicação web. Agora que já foi criada uma sonda personalizada, o gateway de aplicação utiliza o caminho de personalizado definido para monitorizar o estado de funcionamento para os servidores de back-end. O gateway de aplicação com base nos critérios que foi definida, verifica o caminho especificado na sonda. Se a chamada para o anfitrião: porta / caminho não retorna um HTTP 200 399 resposta de estado, o servidor é retirado da rotação após ter sido atingido o limiar de mau estado de funcionamento. Pesquisa continua na instância do mau estado de funcionamento para determinar quando ela se tornar íntegra novamente. Assim que a instância for adicionada novamente ao agrupamento de servidor em bom funcionamento, tráfego começa a fluir novamente ao mesmo e de pesquisa para a instância continua intervalo especificado do utilizador como habitualmente.

## <a name="next-steps"></a>Passos Seguintes

Para saber como configurar a descarga de SSL com o Gateway de aplicação do Azure, veja [configurar a descarga de SSL](application-gateway-ssl-portal.md)

[1]: ./media/application-gateway-create-probe-portal/figure1.png
[2]: ./media/application-gateway-create-probe-portal/figure2.png

