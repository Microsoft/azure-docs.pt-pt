---
title: Aceleração de site dinâmico através da CDN do Azure
description: A CDN do Azure suporta Otimização do dynamic site acceleration (DSA) para os ficheiros com conteúdo dinâmico.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/25/2019
ms.author: magattus
ms.openlocfilehash: 6bd1d24cdece91265a7355678ea2bc0b0f9e3910
ms.sourcegitcommit: 70550d278cda4355adffe9c66d920919448b0c34
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2019
ms.locfileid: "58439163"
---
# <a name="dynamic-site-acceleration-via-azure-cdn"></a>Aceleração de site dinâmico através da CDN do Azure

Com a enxurrada de mídia social, comércio eletrónico e a web hyper personalizada, uma percentagem aumentar rapidamente o conteúdo apresentado aos usuários finais é gerada em tempo real. Os utilizadores esperam uma experiência de web mais rápida, fiável e personalizadas, independentemente do seu browser, localização, dispositivo ou rede. No entanto, as inovações muita que fazer essas experiências, então, como também utilizar lento de página de downloads e colocam a qualidade da experiência do consumidor em risco. 

Capacidade de entrega de conteúdos standard de rede (CDN) inclui a capacidade de ficheiros de cache mais próximo para os utilizadores finais para acelerar a entrega de ficheiros estáticos. No entanto, com aplicativos web dinâmicos, que o conteúdo em localizações limítrofes de colocação em cache não é possível porque o servidor gera o conteúdo em resposta ao comportamento do utilizador. Acelerar a entrega do conteúdo é mais complexo do que tradicional em cache e requer uma solução de ponto-a-ponto que escalados ajusta cada elemento ao longo do caminho de dados inteiro desde o início para entrega. Com a otimização do CDN do Azure dynamic site acceleration (DSA), o desempenho das páginas da web com conteúdo dinâmico é significativamente melhorado.

**A CDN do Azure da Akamai** e **CDN do Azure da Verizon** ambos oferecem otimização DSA através da **otimizado para** menu durante a criação do ponto final. Aceleração de site dinâmico da Microsoft é oferecida [serviço do Azure desde início](https://docs.microsoft.com/azure/frontdoor/front-door-overview).

> [!Important]
> Para **CDN do Azure da Akamai** perfis, pode alterar a otimização de um ponto final da CDN depois de este ter sido criado.
>   
> Para os perfis de **CDN do Azure da Verizon**, não pode alterar a otimização de um ponto final de CDN após ter sido criado.

## <a name="cdn-endpoint-configuration-to-accelerate-delivery-of-dynamic-files"></a>Configuração do ponto final da CDN para acelerar o fornecimento de ficheiros dinâmicos

Para configurar um ponto final da CDN para otimizar a entrega de ficheiros dinâmicos, pode utilizar o portal do Azure, as APIs REST ou qualquer um dos SDKs do cliente para fazer o mesmo programaticamente. 

**Para configurar um ponto final da CDN para a otimização de DSA com o portal do Azure:**

1. Na **perfil da CDN** página, selecione **Endpoint**.

   ![Adicionar um novo ponto final da CDN](./media/cdn-dynamic-site-acceleration/cdn-endpoint-profile.png) 

   O painel **Adicionar um ponto final** aparece.

2. Sob **otimizado para**, selecione **aceleração de sites dinâmicos**.

    ![Criar um novo ponto final da CDN com o DSA](./media/cdn-dynamic-site-acceleration/cdn-endpoint-dsa.png)

3. Para **caminho da sonda**, introduza um caminho válido para um ficheiro.

    Caminho da sonda é um recurso específico para o DSA e, é necessário para a criação de um caminho válido. DSA utiliza uma pequena *caminho da sonda* ficheiro colocado no servidor de origem para otimizar as configurações de encaminhamento de rede para a CDN. Para o ficheiro de caminho de pesquisa, pode transferir e carregar o ficheiro de exemplo para o seu site ou utilizar um elemento existente na origem que é aproximadamente 10 KB de tamanho.

4. Introduza as outras opções de ponto final necessária (para obter mais informações, consulte [criar um novo ponto final da CDN](cdn-create-new-endpoint.md#create-a-new-cdn-endpoint)), em seguida, selecione **Add**.

   Depois de criar o ponto final da CDN, aplica-se as otimizações de DSA para todos os ficheiros que correspondem a determinados critérios. 


**Para configurar um ponto final existente para o DSA (CDN do Azure da Akamai apenas os perfis):**

1. Na **perfil da CDN** página, selecione o ponto final que pretende modificar.

2. No painel esquerdo, selecione **otimização**. 

   O **otimização** é apresentada a página.

3. Sob **otimizado para**, selecione **aceleração de site dinâmico**, em seguida, selecione **guardar**.

> [!Note]
> DSA incorre em Cobranças adicionais. Para obter mais informações, consulte [preços de rede de entrega de conteúdos](https://azure.microsoft.com/pricing/details/cdn/).

## <a name="dsa-optimization-using-azure-cdn"></a>Otimização de DSA utilizar CDN do Azure

Aceleração de sites dinâmica na CDN do Azure acelera a entrega de ativos dinâmicas por meio das seguintes técnicas:

-   [A Otimização da rota](#route-optimization)
-   [Otimizações de TCP](#tcp-optimizations)
-   [Obtenção prévia do objeto (CDN do Azure da Akamai apenas)](#object-prefetch-azure-cdn-from-akamai-only)
-   [Compressão de imagem adaptável (CDN do Azure da Akamai apenas)](#adaptive-image-compression-azure-cdn-from-akamai-only)

### <a name="route-optimization"></a>A Otimização da rota

Otimização da rota é importante porque a Internet é um local dinâmico, onde o tráfego e temporariamente as falhas são constantemente alterar a topologia de rede. O protocolo BGP (Border Gateway) é o protocolo de encaminhamento de Internet, mas pode haver mais rápidas rotas através de servidores de ponto de presença (PoP) intermediários. 

Otimização da rota escolhe o melhor caminho para a origem para que um site é continuamente conteúdo acessível e dinâmico é entregue aos utilizadores finais por meio da rota mais rápido e confiável possíveis. 

A rede de Akamai utiliza técnicas para recolher dados em tempo real e comparar vários caminhos através de diferentes nós do servidor do Akamai, bem como a rota do BGP padrão através da Internet aberta para determinar a rota mais rápida entre a origem e o limite CDN. Essas técnicas evitar Internet pontos de congestionamento e rotas longo. 

Da mesma forma, as utilizações de rede da Verizon uma combinação de Anycast DNS, alta capacidade suportam PoPs e verificações de estado de funcionamento para determinar os melhor gateways para melhor encaminhar os dados do cliente para a origem.
 
Como resultado, conteúdo totalmente dinâmico e transacional é entregue mais rapidamente e de forma mais fiável para os utilizadores finais, mesmo quando é atribuir. 

### <a name="tcp-optimizations"></a>Otimizações de TCP

Protocolo de controlo de transmissão (TCP) é o padrão do conjunto de protocolos de Internet usado para fornecer informações entre aplicativos numa rede IP.  Por predefinição, vários pedidos back lado para o outro são necessários para configurar uma conexão TCP, bem como limites para evitar congestions de rede, que resultam em ineficiências em escala. **A CDN do Azure da Akamai** lida com esse problema ao otimizar em três áreas: 

 - [Eliminando partida lenta do TCP](#eliminating-tcp-slow-start)
 - [Tirar partido das ligações persistentes](#leveraging-persistent-connections)
 - [Ajuste os parâmetros de pacotes TCP](#tuning-tcp-packet-parameters)

#### <a name="eliminating-tcp-slow-start"></a>Eliminando partida lenta do TCP

TCP *lento início* é um algoritmo do protocolo TCP que o impede de congestionamento de rede ao limitar a quantidade de dados enviados através da rede. Ele começa com tamanhos de janela de congestionamento pequenas entre remetente e receptor até que o máximo foi atingido ou perda de pacotes é detectada.

 Ambos **CDN do Azure da Akamai** e **CDN do Azure da Verizon** perfis eliminam partida lenta do TCP com os seguintes três passos:

1. Estado de funcionamento e monitorização de largura de banda é utilizada para medir a largura de banda de ligações entre os servidores de borda PoP.
    
2. As métricas são partilhadas entre os servidores de borda PoP, para que cada servidor esteja ciente das condições de rede e estado de funcionamento do servidor dos outros PoPs em torno delas.  
    
3. Os servidores de borda CDN fazem suposições sobre alguns parâmetros de transmissão, por exemplo, o que o tamanho ideal da janela deve ser ao comunicar com outros servidores edge CDN seu proximidade. Este passo significa que o tamanho da janela de congestionamento inicial pode ser aumentado se o estado de funcionamento da ligação entre os servidores de borda CDN é capaz de transferências de dados superior do pacote.  

#### <a name="leveraging-persistent-connections"></a>Tirar partido das ligações persistentes

Utilizar uma CDN, menos máquinas exclusivas ligar-se ao seu servidor de origem diretamente em comparação com os utilizadores ligados diretamente para a sua origem. A CDN do Azure também agrupamentos pedidos de utilizador para estabelecer ligações menos com a origem.

Como mencionado anteriormente, várias solicitações de handshake são necessárias para estabelecer uma ligação de TCP. Ligações persistentes, o que são implementadas com o `Keep-Alive` cabeçalho HTTP, reutilização de ligações de TCP para vários pedidos HTTP guardar os tempos de ida e volta e acelerar a entrega existentes. 

**A CDN do Azure da Verizon** também envia pacotes de keep-alive periódicas através da ligação de TCP para impedir que uma conexão aberta de que está a ser fechado.

#### <a name="tuning-tcp-packet-parameters"></a>Ajuste os parâmetros de pacotes TCP

**A CDN do Azure da Akamai** adaptável os parâmetros que controlam ligações de servidor para servidor e reduz a quantidade de viagens de longo prazo, necessário para obter o conteúdo incorporado no site utilizando as seguintes técnicas:

- Aumentando a janela de congestionamento inicial para que mais pacotes podem ser enviados sem esperar por uma confirmação.
- Diminuir o tempo limite de retransmissão inicial para que uma perda é detectada e retransmissão ocorre mais rapidamente.
- Diminuir o tempo limite de retransmissão mínimos e máximos para reduzir o tempo de espera antes de partindo do princípio de que pacotes tenham sido perdidos devidos a transmissão.

### <a name="object-prefetch-azure-cdn-from-akamai-only"></a>Obtenção prévia do objeto (CDN do Azure da Akamai apenas)

A maioria dos sites consistem numa página HTML, que faz referência a vários outros recursos, como imagens e scripts. Normalmente, quando um cliente solicita uma página Web, o browser transfere primeiro analisa o objeto HTML e, em seguida, faz solicitações adicionais para ativos ligados que são necessárias para totalmente carregar a página. 

*Obtenção prévia* é uma técnica para obter imagens e scripts incorporada na página HTML, enquanto o HTML é fornecido para o navegador e antes do navegador chega a fazer essas solicitações de objeto. 

Com a opção de obtenção prévia ativada ao tempo quando a CDN serve a página de base de HTML para o navegador do cliente, a CDN analisa o arquivo HTML e fazer pedidos adicionais para todos os recursos ligados e armazene-o em seu cache. Quando o cliente efetua os pedidos para os recursos de ligado, o servidor de borda CDN já tem os objectos solicitados e pode servi-los imediatamente sem uma ida e volta para a origem. Essa otimização beneficia conteúdos colocáveis em cache e não colocáveis em cache.

### <a name="adaptive-image-compression-azure-cdn-from-akamai-only"></a>Compressão de imagem adaptável (CDN do Azure da Akamai apenas)

Alguns dispositivos, especialmente aqueles móveis, experiência de tempos em tempos de velocidades de rede mais lentas. Nestes cenários, é mais vantajoso para ao utilizador receber imagens menores na sua página da Web mais rapidamente, em vez de esperar muito tempo para imagens de resolução total.

Esta funcionalidade automaticamente monitoriza a qualidade de rede e utiliza os métodos de compactação padrão JPEG quando velocidades de rede são mais lentas melhorar o tempo de entrega.

Compressão de imagem adaptável | Extensões de ficheiro  
--- | ---  
Compressão de JPEG | .jpg, .jpeg, .jpe, .jig, .jgig, .jgi

## <a name="caching"></a>Colocação em cache

Com o DSA, colocação em cache está desativada por predefinição na CDN, mesmo quando a origem inclui `Cache-Control` ou `Expires` cabeçalhos na resposta. DSA é normalmente utilizado para os recursos dinâmicos que devem não ser colocado em cache porque eles são exclusivos para cada cliente. Colocação em cache pode quebrar a esse comportamento.

Se tiver um Web site com uma combinação de recursos estáticos e dinâmicos, é melhor usar uma abordagem híbrida para obter o melhor desempenho. 

Para **CDN do Azure Standard da Verizon** e **CDN do Azure Standard da Akamai** perfis, pode ativar colocação em cache para os pontos finais DSA utilizando [regras de colocação em cache](cdn-caching-rules.md).

Para aceder a regras de colocação em cache:

1. Partir do **perfil da CDN** página, em definições, selecione **regras de colocação em cache**.  
    
    ![Botão Regras de colocação em cache da CDN](./media/cdn-dynamic-site-acceleration/cdn-caching-rules-btn.png)

    O **regras de colocação em cache** é aberta a página.

2. Crie uma regra de colocação em cache global ou personalizada para ativar a colocação em cache para o ponto final DSA. 

Para **CDN do Azure Premium da Verizon** perfis, que ative a colocação em cache para os pontos finais DSA, utilizando o [motor de regras](cdn-rules-engine.md). Todas as regras que são criadas afetam apenas os pontos finais do seu perfil são otimizados para o DSA. 

Para acessar o mecanismo de regras:
    
1. Partir do **perfil da CDN** página, selecione **gerir**.  
    
    ![Botão de gerir o perfil de CDN](./media/cdn-dynamic-site-acceleration/cdn-manage-btn.png)

    É aberto o portal de gestão da CDN.

2. A partir do portal de gestão CDN, selecione **ADN**, em seguida, selecione **motor de regras**. 

    ![Motor de regras para o DSA](./media/cdn-dynamic-site-acceleration/cdn-dsa-rules-engine.png)



Em alternativa, pode utilizar dois pontos finais de CDN: um ponto de extremidade otimizado com DSA para fornecer recursos dinâmicos e de outro ponto final otimizado com um tipo de otimização estático, como geral entrega web, a recursos em cache de entrega. Modificar suas URLs de página Web para ligar diretamente ao elemento no ponto final da CDN que pretende utilizar. 

Por exemplo: `mydynamic.azureedge.net/index.html` é uma página dinâmica e são carregados a partir do ponto de extremidade DSA.  A página html faz referência a vários recursos estáticos, como bibliotecas JavaScript ou imagens que são carregadas a partir do ponto de final de CDN estático, tal como `mystatic.azureedge.net/banner.jpg` e `mystatic.azureedge.net/scripts.js`. 



