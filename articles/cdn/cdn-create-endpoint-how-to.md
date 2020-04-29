---
title: Criar um ponto final do CDN Azure [ Microsoft Docs
description: Este artigo mostra como criar um novo ponto final da Rede de Entrega de Conteúdos Azure (CDN), incluindo configurações avançadas.
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.service: azure-cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/12/2018
ms.author: allensu
ms.custom: mvc
ms.openlocfilehash: 0a130a433c68d0d5cc8c26eae4b81ff264eb0ca2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81254020"
---
# <a name="create-an-azure-cdn-endpoint"></a>Criar um ponto final azure CDN
Este artigo descreve todas as definições para criar um ponto final da Rede de Entrega de [Conteúdos Azure (CDN)](cdn-overview.md) num perfil CDN existente. Depois de criar um perfil e um ponto final, pode começar a entregar conteúdo aos seus clientes. Para começar rapidamente a criação de um perfil e ponto final, consulte [Quickstart: Crie um perfil e ponto final azure CDN](cdn-create-new-endpoint.md).

## <a name="prerequisites"></a>Pré-requisitos
Antes de criar um ponto final cdN, deve ter criado pelo menos um perfil CDN, que pode conter um ou mais pontos finais de CDN. Para organizar os seus pontos finais da CDN por domínio de Internet, aplicação Web ou alguns outros critérios, pode utilizar vários perfis. Como os preços da CDN são aplicados ao nível do perfil cdN, você deve criar vários perfis De CDN se quiser usar uma mistura de níveis de preços Azure CDN. Para criar um perfil CDN, consulte [Criar um novo perfil CDN](cdn-create-new-endpoint.md#create-a-new-cdn-profile).

## <a name="log-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure
Inicie sessão no [Portal do Azure](https://portal.azure.com) com a sua conta do Azure.

## <a name="create-a-new-cdn-endpoint"></a>Criar um novo ponto final da CDN

1. No [portal Azure,](https://portal.azure.com)navegue para o seu perfil De CDN. É possível que o tenha afixado ao dashboard no passo anterior. Caso contrário, pode encontrá-lo ao selecionar **Todos os serviços** e, em seguida, selecionar **Perfis da CDN**. No painel **Perfis da CDN**, selecione o perfil no qual pretende adicionar o ponto final. 
   
    É apresentado o painel do perfil da CDN.

2. Selecione **Endpoint**.
   
    ![CDN selecione ponto final](./media/cdn-create-endpoint-how-to/cdn-select-endpoint.png)
   
    A página **Adicionar um ponto final** é apresentada.
   
    ![Adicionar página de ponto final](./media/cdn-create-endpoint-how-to/cdn-add-endpoint-page.png)

3. Para **Nome**, introduza um nome exclusivo para o novo ponto final da CDN. Este nome é utilizado para aceder aos seus recursos em cache no _ \<nome final _do domínio>.azureedge.net.

4. Para **o tipo Origem,** escolha um dos seguintes tipos de origem: 
   - **Armazenamento** para armazenamento Azure
   - **Serviço de nuvem** para serviços azure cloud
   - **Web App** para Web Apps Azure
   - **Origem personalizada** para qualquer outro servidor web de origem acessível ao público (hospedado em Azure ou em qualquer outro lugar)

5. Para o nome de **anfitrião Origin,** selecione ou introduza o seu domínio de servidor de origem. O drop-down lista todos os servidores de origem disponíveis do tipo que especificou no passo 4. Se selecionou a **origem personalizada** como o seu tipo de origem, introduza o domínio do seu servidor de origem personalizada.
    
6. Para o **caminho Origin,** insira o caminho para os recursos que pretende cache. Para permitir o cache de qualquer recurso no domínio especificado no passo 5, deixe esta definição em branco.
    
7. No **Cabeçalho de anfitrião de origem**, introduza o cabeçalho de anfitrião que pretende que a CDN do Azure envie com cada pedido ou deixe a predefinição.
   
   > [!NOTE]
   > Alguns tipos de origens, como o Armazenamento e as Aplicações Web do Azure, requerem que o cabeçalho de anfitrião coincida com o domínio da origem. A menos que tenha uma origem que necessite de um cabeçalho de anfitrião diferente do respetivo domínio, deve deixar o valor predefinido.
   > 
    
8. Para a porta **Protocol** and **Origin,** especifique os protocolos e portas a utilizar para aceder aos seus recursos no servidor de origem. Tem de ser selecionado pelo menos um protocolo (HTTP ou HTTPS). Utilize o domínio fornecido pelo CDN_\<(nome final>_.azureedge.net) para aceder ao conteúdo HTTPS. 
   
   > [!NOTE]
   > O valor da **porta Origin** determina apenas a porta que o ponto final utiliza para obter informações do servidor de origem. O próprio ponto final só está disponível para clientes finais nas portas HTTP e HTTPS predefinidas (80 e 443), independentemente do valor **Porta de origem**.  
   > 
   > Os pontos finais nos perfis da **CDN do Azure da Akamai** não permitem o intervalo de portas TCP completo para portas de origem. Para obter uma lista das portas de origem que não são permitidas, consulte [Portas de Origem Permitidas do Azure CDN da Akamai](/previous-versions/azure/mt757337(v=azure.100)).  
   > 
   > O suporte HTTPS para domínios personalizados Azure CDN não é suportado no **Azure CDN a partir de** produtos Akamai. Para obter mais informações, veja [Configure HTTPS on an Azure CDN custom domain](cdn-custom-ssl.md) (Configurar HTTPS num domínio personalizado da CDN do Azure).
    
9. Para **Otimizado para**, selecione um tipo de otimização que melhor corresponda ao cenário e ao tipo de conteúdo que pretende que o ponto final entregue. Para mais informações, consulte [o Otimize Azure CDN para obter o tipo de entrega de conteúdos](cdn-optimization-overview.md).

    As seguintes definições do tipo de otimização são suportadas, de acordo com o tipo de perfil:
    - **Padrão Azure CDN a partir dos** perfis da Microsoft:
       - [**Entrega geral da web**](cdn-optimization-overview.md#general-web-delivery)

    - **Padrão Azure CDN da Verizon** e **Azure CDN Premium dos** perfis verizon:
       - [**Entrega geral da web**](cdn-optimization-overview.md#general-web-delivery)
       - [**Aceleração dinâmica do site**](cdn-optimization-overview.md#dynamic-site-acceleration)

    - **Padrão Azure CDN dos** perfis da Akamai:
       - [**Entrega geral da web**](cdn-optimization-overview.md#general-web-delivery)
       - [**Transmissão geral dos meios de comunicação**](cdn-optimization-overview.md#general-media-streaming)
       - [**Vídeo a pedido de streaming de mídia**](cdn-optimization-overview.md#video-on-demand-media-streaming)
       - [**Grande download de ficheiros**](cdn-optimization-overview.md#large-file-download)
       - [**Aceleração dinâmica do site**](cdn-optimization-overview.md#dynamic-site-acceleration)

10. Selecione **Adicionar** para criar o novo ponto final.
   
    Depois de o ponto final ser criado, aparece na lista de pontos finais para o perfil.
    
    ![Ponto final da CDN](./media/cdn-create-new-endpoint/cdn-endpoint-success.png)
    
    Dado que a propagação do registo demora algum tempo, o ponto final não está imediatamente disponível para utilização: 
    - Para os perfis **CDN do Azure Standard da Microsoft**, a propagação normalmente fica concluída em 10 minutos. 
    - Para os perfis **CDN do Azure Standard da Akamai**, a propagação normalmente fica concluída num minuto. 
    - Para os perfis **CDN do Azure Standard da Verizon** e **CDN do Azure Premium da Verizon**, a propagação normalmente fica concluída no prazo de 90 minutos. 
   
    Se tentar utilizar o nome de domínio CDN antes de a configuração do ponto final se ter propagado aos servidores do ponto de presença (POP), poderá receber um estado de resposta HTTP 404. Se já passaram várias horas desde que criou o seu ponto final e ainda está a receber um estatuto de resposta 404, consulte [os pontos finais do CDN de Resolução de Problemas que devolvem um código de estado 404](cdn-troubleshoot-endpoint.md).

## <a name="clean-up-resources"></a>Limpar recursos
Para eliminar um ponto final quando já não for necessário, selecione-o e, em seguida, **selecione Delete**. 

## <a name="next-steps"></a>Passos seguintes
Para aprender sobre domínios personalizados, continue ao tutorial para adicionar um domínio personalizado ao seu ponto final cdN.

> [!div class="nextstepaction"]
> [Adicionar um domínio personalizado](cdn-map-content-to-custom-domain.md)


