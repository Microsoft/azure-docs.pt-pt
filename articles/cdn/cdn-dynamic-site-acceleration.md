---
title: Aceleração do local dinâmico via Azure CDN
description: O Azure CDN suporta a otimização dinâmica do site (DSA) para ficheiros com conteúdo dinâmico.
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
ms.topic: article
ms.date: 03/25/2019
ms.author: allensu
ms.openlocfilehash: 26559adf183a5e008d77b87654a1bd4dabebbca0
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81253837"
---
# <a name="dynamic-site-acceleration-via-azure-cdn"></a>Aceleração do local dinâmico via Azure CDN

Com a explosão das redes sociais, do comércio eletrónico e da web hiper-personalizada, uma percentagem cada vez maior do conteúdo servido aos utilizadores finais é gerada em tempo real. Os utilizadores esperam uma experiência web rápida, fiável e personalizada, independentemente do seu navegador, localização, dispositivo ou rede. No entanto, as próprias inovações que tornam estas experiências tão envolventes também descarregam lentamente as páginas e colocam em risco a qualidade da experiência do consumidor. 

A capacidade padrão da rede de entrega de conteúdos (CDN) inclui a capacidade de cache ficheiros mais próximos dos utilizadores finais para acelerar a entrega de ficheiros estáticos. No entanto, com aplicações web dinâmicas, não é possível que o conteúdo em localizações de borda não seja possível porque o servidor gera o conteúdo em resposta ao comportamento do utilizador. Acelerar a entrega de tal conteúdo é mais complexo do que o tradicional entalador de bordas e requer uma solução de ponta a ponta que afina finamente cada elemento ao longo de todo o caminho dos dados desde o início até à entrega. Com a otimização dinâmica do site Azure CDN (DSA), o desempenho das páginas web com conteúdo dinâmico é mensuravelmente melhorado.

**O Azure CDN da Akamai** e **o Azure CDN da Verizon** oferecem otimização da DSA através do **Otimizado para** menu durante a criação de endpoint. A aceleração dinâmica do site da Microsoft é oferecida através do [Serviço de Porta Frontal Azure](https://docs.microsoft.com/azure/frontdoor/front-door-overview).

> [!Important]
> Para **o Azure CDN dos** perfis da Akamai, é permitido alterar a otimização de um ponto final da CDN após a sua criação.
>   
> Para os perfis de **CDN do Azure da Verizon**, não pode alterar a otimização de um ponto final de CDN após ter sido criado.

## <a name="cdn-endpoint-configuration-to-accelerate-delivery-of-dynamic-files"></a>Configuração do ponto final da CDN para acelerar a entrega de ficheiros dinâmicos

Para configurar um ponto final da CDN para otimizar a entrega de ficheiros dinâmicos, pode utilizar o portal Azure, as APIs rest, ou qualquer um dos SDKs do cliente para fazer a mesma coisa programáticamente. 

**Para configurar um ponto final de CDN para otimização de DSA utilizando o portal Azure:**

1. Na página de perfil da **CDN,** selecione **Endpoint**.

   ![Adicione um novo ponto final da CDN](./media/cdn-dynamic-site-acceleration/cdn-endpoint-profile.png) 

   O painel **Adicionar um ponto final** aparece.

2. Em **Otimizado para**, selecione **a aceleração do site Dynamic**.

    ![Criar um novo ponto final da CDN com a DSA](./media/cdn-dynamic-site-acceleration/cdn-endpoint-dsa.png)

3. Para o **caminho da sonda,** insira um caminho válido para um ficheiro.

    O caminho da sonda é uma característica específica da DSA, e é necessário um caminho válido para a criação. A DSA utiliza um pequeno ficheiro de caminho de *sonda* colocado no servidor de origem para otimizar as configurações de encaminhamento da rede para o CDN. Para o ficheiro do caminho da sonda, pode descarregar e enviar o ficheiro de amostra para o seu site, ou utilizar um ativo existente na sua origem que tem cerca de 10 KB de tamanho.

4. Introduza as outras opções de ponto final necessárias (para mais informações, consulte [Criar um novo ponto final do CDN),](cdn-create-new-endpoint.md#create-a-new-cdn-endpoint)em seguida, selecione **Adicionar**.

   Após a criação do ponto final da CDN, aplica as otimizações da DSA para todos os ficheiros que correspondam a determinados critérios. 


**Para configurar um ponto final existente para a DSA (Azure CDN apenas dos perfis da Akamai):**

1. Na página de **perfil da CDN,** selecione o ponto final que pretende modificar.

2. A partir do painel esquerdo, **selecione Otimização**. 

   A página de **Otimização** aparece.

3. Em **otimizado para**, selecione **a aceleração do site Dynamic,** e, em seguida, selecione **Save**.

> [!Note]
> A DSA incorre em acusações extra. Para mais informações, consulte [os preços da Rede](https://azure.microsoft.com/pricing/details/cdn/)de Entrega de Conteúdos .

## <a name="dsa-optimization-using-azure-cdn"></a>Otimização dSA usando CDN Azure

A aceleração do site dinâmico em Azure CDN acelera a entrega de ativos dinâmicos utilizando as seguintes técnicas:

-   [Otimização de rotas](#route-optimization)
-   [Otimizações de TCP](#tcp-optimizations)
-   [Prefetch de objetos (Azure CDN apenas da Akamai)](#object-prefetch-azure-cdn-from-akamai-only)
-   [Compressão de imagem adaptável (Azure CDN apenas da Akamai)](#adaptive-image-compression-azure-cdn-from-akamai-only)

### <a name="route-optimization"></a>Otimização de Rotas

A otimização das rotas é importante porque a Internet é um local dinâmico, onde o tráfego e as interrupções temporárias estão constantemente a mudar a topologia da rede. O Border Gateway Protocol (BGP) é o protocolo de encaminhamento da Internet, mas pode haver rotas mais rápidas através de servidores intermediários do Ponto de Presença (PoP). 

A otimização do percurso escolhe o caminho mais ideal para a origem para que um site seja continuamente acessível e conteúdos dinâmicos seja entregue aos utilizadores finais através da rota mais rápida e fiável possível. 

A rede Akamai utiliza técnicas para recolher dados em tempo real e comparar vários caminhos através de diferentes nós no servidor Akamai, bem como a rota padrão de BGP através da Internet aberta para determinar a rota mais rápida entre a origem e a borda CDN. Estas técnicas evitam pontos de congestionamento da Internet e rotas longas. 

Da mesma forma, a rede Verizon utiliza uma combinação de DNS Anycast, PoPs de suporte de alta capacidade e verificações de saúde, para determinar os melhores gateways para melhor dados de rota do cliente para a origem.
 
Como resultado, conteúdos totalmente dinâmicos e transacionais são entregues de forma mais rápida e fiável aos utilizadores finais, mesmo quando não são cachepáveis. 

### <a name="tcp-optimizations"></a>Otimizações tCP

O Protocolo de Controlo de Transmissão (TCP) é o padrão do conjunto de protocolos de Internet utilizado para fornecer informações entre aplicações numa rede IP.  Por predefinição, são necessários vários pedidos de back-and-forth para estabelecer uma ligação TCP, bem como limites para evitar congestionamentos de rede, que resultam em ineficiências em escala. **O Azure CDN da Akamai** lida com este problema otimizando em três áreas: 

 - [Eliminação do arranque lento do TCP](#eliminating-tcp-slow-start)
 - [Aproveitando ligações persistentes](#leveraging-persistent-connections)
 - [Parâmetros de pacote sintonizados do TCP](#tuning-tcp-packet-parameters)

#### <a name="eliminating-tcp-slow-start"></a>Eliminação do arranque lento do TCP

O *arranque lento* do TCP é um algoritmo do protocolo TCP que impede o congestionamento da rede limitando a quantidade de dados enviados através da rede. Começa com pequenos tamanhos de janela de congestionamento entre o remetente e o recetor até que o máximo seja atingido ou seja detetada a perda do pacote.

 Tanto **o Azure CDN da Akamai como** o **Azure CDN dos** perfis de Verizon eliminam o início lento do TCP com os seguintes três passos:

1. A monitorização da largura de banda e saúde é utilizada para medir a largura de banda das ligações entre os servidores PoP de borda.
    
2. As métricas são partilhadas entre os servidores PoP de borda para que cada servidor esteja ciente das condições de rede e da saúde do servidor dos outros PoPs à sua volta.  
    
3. Os servidores de borda CDN fazem suposições sobre alguns parâmetros de transmissão, tais como qual o tamanho ideal da janela deve ser ao comunicar com outros servidores de borda CDN na sua proximidade. Este passo significa que o tamanho inicial da janela de congestionamento pode ser aumentado se a saúde da ligação entre os servidores de borda CDN for capaz de transferências de dados de pacotes mais elevados.  

#### <a name="leveraging-persistent-connections"></a>Aproveitando ligações persistentes

Utilizando um CDN, menos máquinas únicas conectam-se diretamente ao seu servidor de origem em comparação com os utilizadores que se conectam diretamente à sua origem. O Azure CDN também reúne pedidos de utilizadores para estabelecer menos ligações com a origem.

Como mencionado anteriormente, são necessários vários pedidos de aperto de mão para estabelecer uma ligação TCP. Ligações persistentes, que são `Keep-Alive` implementadas pelo cabeçalho HTTP, reutilizam as ligações TCP existentes para múltiplos pedidos http para poupar tempos de ida e volta e acelerar a entrega. 

**O Azure CDN da Verizon** também envia pacotes periódicos de manutenção viva sobre a ligação TCP para evitar que uma ligação aberta seja fechada.

#### <a name="tuning-tcp-packet-parameters"></a>Parâmetros de pacote sintonizados do TCP

**O Azure CDN da Akamai** afina os parâmetros que regem as ligações servidor-servidor e reduz a quantidade de viagens de ida e volta de longo curso necessárias para recuperar conteúdo incorporado no site utilizando as seguintes técnicas:

- Aumentar a janela inicial de congestionamento para que mais pacotes possam ser enviados sem esperar por um reconhecimento.
- Diminuindo o tempo de retransmissão inicial para que uma perda seja detetada, e a retransmissão ocorre mais rapidamente.
- Diminuindo o tempo mínimo e máximo de retransmissão para reduzir o tempo de espera antes de assumir que os pacotes foram perdidos na transmissão.

### <a name="object-prefetch-azure-cdn-from-akamai-only"></a>Prefetch de objetos (Azure CDN apenas da Akamai)

A maioria dos websites é composta por uma página HTML, que refere vários outros recursos, como imagens e scripts. Normalmente, quando um cliente solicita uma página web, o navegador primeiro descarrega e analisa o objeto HTML, e depois faz pedidos adicionais para ativos ligados que são necessários para carregar totalmente a página. 

*A Prefetch* é uma técnica para recuperar imagens e scripts incorporados na página HTML enquanto o HTML é servido ao navegador, e antes mesmo de o navegador fazer estes pedidos de objeto. 

Com a opção prefetch ligada no momento em que o CDN serve a página base HTML para o navegador do cliente, o CDN analisa o ficheiro HTML e faz pedidos adicionais para quaisquer recursos ligados e armazená-lo na sua cache. Quando o cliente faz os pedidos para os ativos ligados, o servidor de borda CDN já tem os objetos solicitados e pode servi-los imediatamente sem uma ida e volta à origem. Esta otimização beneficia conteúdo cacheable e não cacheable.

### <a name="adaptive-image-compression-azure-cdn-from-akamai-only"></a>Compressão de imagem adaptável (Azure CDN apenas da Akamai)

Alguns dispositivos, especialmente os móveis, experimentam velocidades de rede mais lentas de vez em quando. Nestes cenários, é mais benéfico para o utilizador receber imagens menores na sua página web mais rapidamente do que esperar muito tempo por imagens de resolução completa.

Esta funcionalidade monitoriza automaticamente a qualidade da rede e emprega métodos de compressão Padrão JPEG quando as velocidades de rede são mais lentas para melhorar o tempo de entrega.

Compressão de imagem adaptável | Extensões de ficheiros  
--- | ---  
Compressão JPEG | .jpg, .jpeg, .jpe, .jig, .jgig, .jgi

## <a name="caching"></a>Colocação em cache

Com a DSA, o cache é desligado por padrão no `Cache-Control` CDN, mesmo quando a origem inclui ou `Expires` cabeçalhos na resposta. A DSA é normalmente usada para ativos dinâmicos que não devem ser cached porque são únicos para cada cliente. O caching pode quebrar este comportamento.

Se tem um website com uma mistura de ativos estáticos e dinâmicos, o melhor é ter uma abordagem híbrida para obter o melhor desempenho. 

Para **o Azure CDN Standard da Verizon** e **azure CDN Standard a partir de** perfis Akamai, você pode ligar o cache para pontos finais específicos da DSA usando regras de [cache](cdn-caching-rules.md).

Para aceder às regras de cache:

1. A partir da página de **perfil cdN,** em definições, selecione **regras de Caching**.  
    
    ![Botão Regras de colocação em cache da CDN](./media/cdn-dynamic-site-acceleration/cdn-caching-rules-btn.png)

    A página de regras do **Caching** abre.

2. Crie uma regra global ou personalizada para ligar o cache para o seu ponto final da DSA. 

Para **o Azure CDN Premium apenas a partir dos** perfis de Verizon, liga-se o cache para pontos finais dSA específicos utilizando o motor de [regras](cdn-rules-engine.md). Quaisquer regras criadas afetam apenas os pontos finais do seu perfil que estão otimizados para a DSA. 

Para aceder ao motor de regras:
    
1. A partir da página de **perfil cdN,** selecione **Gerir**.  
    
    ![Botão de gestão de perfil CDN](./media/cdn-dynamic-site-acceleration/cdn-manage-btn.png)

    Abre o portal de gestão da CDN.

2. A partir do portal de gestão da CDN, selecione **ADN,** em seguida, selecione **Rules Engine**. 

    ![Motor de regras para DSA](./media/cdn-dynamic-site-acceleration/cdn-dsa-rules-engine.png)



Em alternativa, pode utilizar dois pontos finais cdN: um ponto final otimizado com dSA para entregar ativos dinâmicos e outro ponto final otimizado com um tipo de otimização estática, como a entrega geral da Web, para fornecer ativos cacheáveis. Modifique os URLs da página web para ligar diretamente ao ativo no ponto final da CDN que planeia utilizar. 

Por exemplo: `mydynamic.azureedge.net/index.html` é uma página dinâmica e é carregada a partir do ponto final da DSA.A página html refere vários ativos estáticos, tais como bibliotecas JavaScript ou `mystatic.azureedge.net/banner.jpg` `mystatic.azureedge.net/scripts.js`imagens que são carregadas a partir do ponto final estático do CDN, tais como e . 



