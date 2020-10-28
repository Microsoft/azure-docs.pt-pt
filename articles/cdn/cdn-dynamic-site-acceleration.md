---
title: Aceleração dinâmica do site via Azure CDN
description: O Azure CDN suporta a otimização dinâmica da aceleração do site (DSA) para ficheiros com conteúdo dinâmico.
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 03/25/2019
ms.author: allensu
ms.openlocfilehash: 5387fdc224cd77ee5273767df5033a51dc27608c
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/28/2020
ms.locfileid: "92778868"
---
# <a name="dynamic-site-acceleration-via-azure-cdn"></a>Aceleração dinâmica do site via Azure CDN

Com a explosão das redes sociais, do comércio eletrónico e da web hiper-personalizada, uma percentagem crescente do conteúdo servido aos utilizadores finais é gerada em tempo real. Os utilizadores esperam uma experiência web rápida, fiável e personalizada, independente do seu navegador, localização, dispositivo ou rede. No entanto, as próprias inovações que tornam estas experiências tão envolventes também descarregam páginas lentas e colocam em risco a qualidade da experiência do consumidor. 

A capacidade padrão da rede de entrega de conteúdos (CDN) inclui a capacidade de cache de ficheiros mais próximos dos utilizadores finais para acelerar a entrega de ficheiros estáticos. No entanto, com aplicações web dinâmicas, o facto de o conteúdo em localizações de borda não ser possível porque o servidor gera o conteúdo em resposta ao comportamento do utilizador. Acelerar a entrega de tais conteúdos é mais complexo do que o tradicional caching de borda e requer uma solução de ponta a ponta que afina finamente cada elemento ao longo de todo o caminho de dados desde o início até à entrega. Com a otimização dinâmica do site Azure CDN (DSA), o desempenho das páginas web com conteúdo dinâmico é mensuravelmente melhorado.

**Azure CDN da Akamai** e **Azure CDN da Verizon** ambos oferecem otimização DSA através do menu **Otimizado durante** a criação de ponto final. A aceleração dinâmica do site da Microsoft é oferecida através do [Azure Front Door Service](../frontdoor/front-door-overview.md).

> [!Important]
> Para **o Azure CDN a partir de** perfis Akamai, é permitido alterar a otimização de um ponto final cdn após a sua criação.
>   
> Para os perfis de **CDN do Azure da Verizon** , não pode alterar a otimização de um ponto final de CDN após ter sido criado.

## <a name="cdn-endpoint-configuration-to-accelerate-delivery-of-dynamic-files"></a>Configuração do ponto final cdN para acelerar a entrega de ficheiros dinâmicos

Para configurar um ponto final do CDN para otimizar a entrega de ficheiros dinâmicos, pode utilizar o portal Azure, as APIs REST ou qualquer um dos SDKs do cliente para fazer a mesma coisa programáticamente. 

**Para configurar um ponto final cdn para otimização de DSA utilizando o portal Azure:**

1. Na página de perfil do **CDN,** selecione **Endpoint** .

   ![Adicione um novo ponto final cdn](./media/cdn-dynamic-site-acceleration/cdn-endpoint-profile.png) 

   O painel **Adicionar um ponto final** aparece.

2. Sob **otimizado para** , selecione **Dynamic site acceleration** .

    ![Criar um novo ponto final cdn com a DSA](./media/cdn-dynamic-site-acceleration/cdn-endpoint-dsa.png)

3. Para **o caminho da sonda,** insira um caminho válido para um ficheiro.

    O caminho da sonda é uma característica específica da DSA, e é necessário um caminho válido para a criação. A DSA utiliza um pequeno ficheiro *de caminho* sonda colocado no servidor de origem para otimizar as configurações de encaminhamento de rede para o CDN. Para o ficheiro caminho da sonda, pode descarregar e carregar o ficheiro de amostra para o seu site, ou utilizar um ativo existente na sua origem com cerca de 10 KB de tamanho.

4. Introduza as outras opções de ponto final necessários (para obter mais informações, consulte [Criar um novo ponto final CDN](cdn-create-new-endpoint.md#create-a-new-cdn-endpoint)), em seguida, selecione **Adicionar** .

   Após a criação do ponto final cdn, aplica as otimizações DSA para todos os ficheiros que correspondam a determinados critérios. 


**Para configurar um ponto final existente para a DSA (Azure CDN apenas a partir de perfis Akamai):**

1. Na página de perfil do **CDN,** selecione o ponto final que pretende modificar.

2. A partir do painel esquerdo, selecione **Otimização.** 

   A página **de Otimização** aparece.

3. Em **Otimizado para** , selecione **A aceleração do site Dynamic,** em seguida, selecione **Guardar** .

> [!Note]
> A DSA incorre em acusações extras. Para obter mais informações, consulte [os preços da Rede de Entrega de Conteúdos.](https://azure.microsoft.com/pricing/details/cdn/)

## <a name="dsa-optimization-using-azure-cdn"></a>Otimização DSA usando Azure CDN

A aceleração dinâmica do site no Azure CDN acelera a entrega de ativos dinâmicos utilizando as seguintes técnicas:

-   [Otimização de rotas](#route-optimization)
-   [Otimizações de TCP](#tcp-optimizations)
-   [Prefetch de objeto (Azure CDN apenas da Akamai)](#object-prefetch-azure-cdn-from-akamai-only)
-   [Compressão de imagem adaptativa (Azure CDN apenas da Akamai)](#adaptive-image-compression-azure-cdn-from-akamai-only)

### <a name="route-optimization"></a>Otimização de Rotas

A otimização das rotas é importante porque a Internet é um local dinâmico, onde o tráfego e as paragens temporárias estão constantemente a mudar a topologia da rede. O Protocolo border gateway (BGP) é o protocolo de encaminhamento da Internet, mas pode haver rotas mais rápidas através de servidores intermediários do Ponto de Presença (PoP). 

A otimização da rota escolhe o caminho mais ideal para a origem para que um site seja continuamente acessível e o conteúdo dinâmico seja entregue aos utilizadores finais através da rota mais rápida e fiável possível. 

A rede Akamai utiliza técnicas para recolher dados em tempo real e comparar vários caminhos através de diferentes nós no servidor Akamai, bem como a rota BGP padrão através da Internet aberta para determinar a rota mais rápida entre a origem e a borda CDN. Estas técnicas evitam pontos de congestionamento da Internet e rotas longas. 

Da mesma forma, a rede Verizon utiliza uma combinação de DNS Anycast, PoPs de suporte de alta capacidade, e verificações de saúde, para determinar os melhores gateways para os melhores dados de rota do cliente até à origem.
 
Como resultado, o conteúdo totalmente dinâmico e transacional é entregue de forma mais rápida e fiável aos utilizadores finais, mesmo quando é inalcanqueável. 

### <a name="tcp-optimizations"></a>Otimizações TCP

O Protocolo de Controlo de Transmissão (TCP) é o padrão do conjunto de protocolos de Internet utilizado para fornecer informações entre aplicações numa rede IP.  Por predefinição, são necessários vários pedidos de retrocesso para a criação de uma ligação TCP, bem como limites para evitar congestionamentos de rede, que resultam em ineficiências em escala. **O Azure CDN da Akamai** lida com este problema otimizando em três áreas: 

 - [Eliminação do início lento da TCP](#eliminating-tcp-slow-start)
 - [Aproveitando ligações persistentes](#leveraging-persistent-connections)
 - [Parâmetros de pacote TCP de sintonização](#tuning-tcp-packet-parameters)

#### <a name="eliminating-tcp-slow-start"></a>Eliminação do início lento da TCP

O *início lento* da TCP é um algoritmo do protocolo TCP que impede o congestionamento da rede limitando a quantidade de dados enviados através da rede. Começa com pequenos tamanhos de janela de congestionamento entre o remetente e o recetor até que o máximo seja atingido ou a perda do pacote seja detetada.

 Tanto **a Azure CDN da Akamai** como a **Azure CDN dos** perfis Verizon eliminam o início lento da TCP com os seguintes três passos:

1. A monitorização da saúde e da largura de banda é utilizada para medir a largura de banda das ligações entre servidores PoP de borda.
    
2. As métricas são partilhadas entre servidores PoP de borda para que cada servidor esteja ciente das condições de rede e da saúde do servidor dos outros PoPs à sua volta.  
    
3. Os servidores de borda CDN fazem suposições sobre alguns parâmetros de transmissão, tais como o tamanho ideal da janela quando comunicam com outros servidores de borda CDN na sua proximidade. Este passo significa que o tamanho inicial da janela de congestionamento pode ser aumentado se a saúde da ligação entre os servidores de borda CDN for capaz de transferências de dados de pacotes mais elevadas.  

#### <a name="leveraging-persistent-connections"></a>Aproveitando ligações persistentes

Utilizando um CDN, menos máquinas únicas ligam-se diretamente ao seu servidor de origem em comparação com os utilizadores que se ligam diretamente à sua origem. O Azure CDN também junta pedidos de utilizador para estabelecer menos ligações com a origem.

Como mencionado anteriormente, são necessários vários pedidos de aperto de mão para estabelecer uma ligação TCP. As ligações persistentes, que são implementadas pelo `Keep-Alive` cabeçalho HTTP, reutilizam as ligações TCP existentes para múltiplos pedidos HTTP para poupar tempos de ida e volta e acelerar a entrega. 

**O Azure CDN de Verizon** também envia pacotes periódicos de manutenção viva sobre a ligação TCP para evitar que uma ligação aberta seja fechada.

#### <a name="tuning-tcp-packet-parameters"></a>Parâmetros de pacote TCP de sintonização

**O Azure CDN da Akamai** afina os parâmetros que regem as ligações servidor-servidor e reduz a quantidade de viagens de ida e volta de longo curso necessárias para recuperar o conteúdo incorporado no site utilizando as seguintes técnicas:

- Aumentando a janela de congestionamento inicial para que mais pacotes possam ser enviados sem esperar por um reconhecimento.
- Diminuindo o tempo limite inicial de retransmissão para que uma perda seja detetada, e a retransmissão ocorre mais rapidamente.
- Diminuindo o tempo mínimo e máximo de retransmissão para reduzir o tempo de espera antes de assumir que os pacotes foram perdidos na transmissão.

### <a name="object-prefetch-azure-cdn-from-akamai-only"></a>Prefetch de objeto (Azure CDN apenas da Akamai)

A maioria dos websites consiste numa página HTML, que faz referência a vários outros recursos, como imagens e scripts. Normalmente, quando um cliente solicita uma página web, o navegador primeiro descarrega e analisa o objeto HTML, e depois faz pedidos adicionais para ativos ligados que são necessários para carregar totalmente a página. 

*Prefetch* é uma técnica para recuperar imagens e scripts incorporados na página HTML enquanto o HTML é servido ao navegador, e antes mesmo do navegador fazer estes pedidos de objeto. 

Com a opção pré-app ligada no momento em que o CDN serve a página base HTML para o navegador do cliente, o CDN analisa o ficheiro HTML e faz pedidos adicionais para quaisquer recursos ligados e armazena-o na sua cache. Quando o cliente faz os pedidos para os ativos ligados, o servidor de borda CDN já tem os objetos solicitados e pode servi-los imediatamente sem uma ida e volta até à origem. Esta otimização beneficia tanto o conteúdo cacheable como o conteúdo não cacheable.

### <a name="adaptive-image-compression-azure-cdn-from-akamai-only"></a>Compressão de imagem adaptativa (Azure CDN apenas da Akamai)

Alguns dispositivos, especialmente os móveis, experimentam velocidades de rede mais lentas de vez em quando. Nestes cenários, é mais benéfico para o utilizador receber imagens menores na sua página mais rapidamente do que esperar muito tempo por imagens de resolução completa.

Esta funcionalidade monitoriza automaticamente a qualidade da rede e emprega métodos de compressão JPEG padrão quando as velocidades de rede são mais lentas para melhorar o tempo de entrega.

Compressão de imagem adaptativa | Extensões de ficheiros  
--- | ---  
Compressão JPEG | .jpg, .jpeg, .jpe, .jig, .jgig, .jgi

## <a name="caching"></a>Colocação em cache

Com o DSA, o caching é desligado por defeito no CDN, mesmo quando a origem inclui `Cache-Control` ou `Expires` cabeçalhos na resposta. A DSA é normalmente usada para ativos dinâmicos que não devem ser em cache porque são exclusivos de cada cliente. Caching pode quebrar este comportamento.

Se tem um site com uma mistura de ativos estáticos e dinâmicos, o melhor é ter uma abordagem híbrida para obter o melhor desempenho. 

Para **o Azure CDN Standard de Verizon** e **Azure CDN Standard a partir de** perfis Akamai, pode ligar o caching para pontos finais específicos da DSA utilizando [regras de caching](cdn-caching-rules.md).

Para aceder às regras de caching:

1. A partir da página de perfil do **CDN,** em definições, selecione **as regras de Caching** .  
    
    ![Botão Regras de colocação em cache da CDN](./media/cdn-dynamic-site-acceleration/cdn-caching-rules-btn.png)

    A página **das regras do Caching** abre.

2. Crie uma regra de caching global ou personalizada para ligar o caching para o seu ponto final DSA. 

Para **o Azure CDN Premium** apenas a partir de perfis Verizon, liga-se o caching para pontos finais específicos da DSA utilizando o motor de [regras](./cdn-verizon-premium-rules-engine.md). Quaisquer regras criadas afetam apenas os pontos finais do seu perfil que são otimizados para a DSA. 

Para aceder ao motor de regras:
    
1. Na página de perfil do **CDN,** **selecione Gerir** .  
    
    ![Botão de gestão de perfil CDN](./media/cdn-dynamic-site-acceleration/cdn-manage-btn.png)

    O portal de gestão cdn abre.

2. A partir do portal de gestão CDN, selecione **ADN** e, em seguida, selecione **Rules Engine** . 

    ![Motor de regras para a DSA](./media/cdn-dynamic-site-acceleration/cdn-dsa-rules-engine.png)



Em alternativa, pode utilizar dois pontos finais cdN: um ponto final otimizado com a DSA para fornecer ativos dinâmicos e outro ponto final otimizado com um tipo de otimização estática, como a entrega geral da Web, para a entrega de ativos cacheíveis. Modifique os URLs da sua página web para ligar diretamente ao ativo no ponto final do CDN que pretende utilizar. 

Por exemplo: `mydynamic.azureedge.net/index.html` é uma página dinâmica e é carregada a partir do ponto final da DSA.  A página html refere múltiplos ativos estáticos, tais como bibliotecas JavaScript ou imagens que são carregadas a partir do ponto final estático cdN, tais como `mystatic.azureedge.net/banner.jpg` e `mystatic.azureedge.net/scripts.js` .