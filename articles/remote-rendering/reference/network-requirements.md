---
title: Requisitos de rede
description: Requisitos de rede e melhores práticas de rede para uma experiência ideal
author: florianborn71
ms.author: flborn
ms.date: 02/13/2020
ms.topic: reference
ms.openlocfilehash: fd8686cc396d5fcee20590fbac8bccaf187b024d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101735952"
---
# <a name="network-requirements"></a>Requisitos de rede

Uma ligação de rede estável e de baixa latência a um centro de dados Azure é fundamental para uma boa experiência do utilizador na Renderização Remota Azure. As más condições de rede podem resultar em ligações abandonadas, hologramas instáveis, nervosos ou "saltitantes" e atrasos visíveis ao atualizar o gráfico de cena do lado do servidor.

## <a name="guidelines-for-network-connectivity"></a>Orientações para a conectividade da rede

Os requisitos exatos da rede dependem do seu caso de utilização específico, como o número e a frequência de modificações no gráfico de cena remota, bem como a complexidade da vista prestada, mas existem várias diretrizes para garantir que a sua experiência é o melhor possível:

* A sua conectividade na Internet precisa de suportar pelo menos **40 Mbps a jusante** e **5 Mbps** a montante consistentemente para uma única sessão de utilizador de Renderização Remota Azure, assumindo que não existe tráfego concorrente na rede. Recomendamos tarifas mais elevadas para melhores experiências. 
* **O Wi-Fi** é o tipo de rede recomendado, uma vez que suporta uma ligação de latência baixa, alta faixa e estável. Algumas redes móveis introduzem nervosismo que pode levar a uma má experiência. 
* Utilizando a **banda de 5-GHz Wi-Fi** produzirá geralmente melhores resultados do que a banda de 2,4-GHz Wi-Fi, embora ambos devam funcionar.
* Se existirem outras redes Wi-Fi nas proximidades, evite utilizar Wi-Fi canais utilizados por estas outras redes. Pode utilizar ferramentas de digitalização de rede como [o WifiInfoView](https://www.nirsoft.net/utils/wifi_information_view.html) para verificar se os canais que o seu Wi-Fi utiliza a rede, estão livres de tráfego concorrente.
* Evite estritamente **utilizar Wi-Fi repetidores** ou reencaminhamento lan-over-powerline.
* **Evite o tráfego de largura de banda concorrente** – como o streaming de vídeo ou de jogos – na mesma rede Wi-Fi.
* Se tiver vários dispositivos no mesmo ponto de acesso, os requisitos escalam-se correspondentemente. Se tiver vários pontos de acesso em todo o ambiente, carregue os dispositivos de equilíbrio através dos pontos de acesso, para que sejam distribuídos uniformemente.
* Ter **boa força de sinal Wi-Fi** é essencial. Se possível, mantenha-se perto do ponto de acesso Wi-Fi e evite obstáculos entre o dispositivo do seu cliente e os pontos de acesso.
* Certifique-se de que liga sempre ao **centro de dados Azure mais próximo** da sua [região.](regions.md) Quanto mais perto do centro de dados, menor a latência da rede, que tem um enorme efeito na estabilidade do holograma.

> [!NOTE]
> A largura de banda a jusante é consumida maioritariamente pelo fluxo de vídeo, que por sua vez é dividido entre informações de cor e profundidade (ambos 60 Hz, estéreo).

## <a name="network-performance-tests"></a>Testes de desempenho em rede

Se quiser obter uma compreensão inicial de se a qualidade da sua conectividade de rede é suficiente para executar a renderização remota do Azure, existem ferramentas online existentes que pode utilizar. Recomendamos vivamente que estas ferramentas on-line sejam executadas a partir de um portátil razoavelmente potente ligado ao mesmo Wi-Fi que o dispositivo em que está a planear executar a sua aplicação de cliente de renderização remota Azure. Os resultados obtidos pela execução dos testes num telemóvel ou no HoloLens2 são geralmente menos úteis, uma vez que provaram apresentar uma variação significativa em dispositivos de ponta de baixo alimentado. A localização em que coloca o portátil deve estar aproximadamente no mesmo local onde espera utilizar o dispositivo que executa a sua aplicação de cliente de renderização remota Azure.

Aqui estão alguns passos simples para um teste rápido da sua conectividade de rede:

1. **Executar uma ferramenta de teste de rede como www.speedtest.net para obter dados sobre a latência geral e largura de banda a montante/a jusante da sua ligação à rede.**
Escolha um servidor mais próximo de si e faça o teste. Embora o servidor não seja o centro de dados Azure a que o Azure Remote Rendering se irá ligar, os dados resultantes ainda são úteis para entender o desempenho da sua ligação à Internet e Wi-Fi.
   * **Requisito mínimo** para renderização remota Azure: Aproximadamente 40 Mbps a jusante e 5 Mbps a montante.
   * **Recomendado** para renderização remota Azure: Aproximadamente 100 Mbps a jusante e 10 Mbps a montante.
Recomendamos que faça o teste várias vezes e tome os piores resultados.
1. **Utilize uma ferramenta como www.azurespeed.com que mede a latência aos centros de dados do Azure**. Selecione o centro de dados Azure suportado pela Renderização Remota Azure que lhe é mais próxima (ver [regiões apoiadas)](regions.md)e faça um **teste de latência**. Se houver variação nos números que vê, dê aos resultados algum tempo para estabilizar.
   * **Requisito mínimo** para renderização remota Azure: A latência deve ser consistentemente inferior a 80 ms.
   * **Recomendado** para renderização remota Azure: A latência deve ser consistentemente inferior a 40 ms.

Embora a baixa latência não seja uma garantia de que a Renderização Remota Azure funcione bem na sua rede, normalmente temos visto que funciona bem em situações em que estes testes passaram com sucesso.
Se encontrar artefactos como hologramas instáveis, nervosos ou saltitantes ao executar a renderização remota do Azure, consulte o [guia de resolução de problemas](../resources/troubleshoot.md).

## <a name="next-steps"></a>Passos seguintes

* [Quickstart: Renderiza um modelo com Unidade](../quickstarts/render-model.md)
