---
title: Requisitos da rede
description: Requisitos de rede e melhores práticas de rede para uma experiência ideal
author: florianborn71
ms.author: flborn
ms.date: 02/13/2020
ms.topic: reference
ms.openlocfilehash: 9bb8818d2bb91c14eafc272774e0b633c51e58e1
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81617885"
---
# <a name="network-requirements"></a>Requisitos da rede

Uma ligação estável e de baixa latência a um centro de dados Azure é fundamental para uma boa experiência de utilizador em Renderização Remota Azure. As más condições de rede podem resultar em ligações largas, hologramas instáveis, nervosos ou 'saltando', e um atraso notável ao atualizar o gráfico de cena do lado do servidor.

## <a name="guidelines-for-network-connectivity"></a>Orientações para a conectividade da rede

Os requisitos exatos da rede dependem do seu caso de utilização específico, como o número e a frequência de modificações no gráfico de cena remota, bem como a complexidade da vista renderizada, mas existem uma série de diretrizes para garantir que a sua experiência seja o melhor possível:

* A sua conectividade na Internet precisa de suportar pelo menos 50 Mbps a **jusante** e **10 Mbps** a montante de forma consistente para uma única sessão de utilizador da Renderização Remota Azure, assumindo que não existe tráfego concorrente na rede. Recomendamos tarifas mais altas para melhores experiências. Com mais utilizadores na mesma rede, estes requisitos escalam-se correspondentemente.
* A utilização da **banda Wi-Fi de 5 GHz** geralmente produzirá melhores resultados do que a banda Wi-Fi de 2,4 GHz, embora ambos devam funcionar.
* Se existirem outras redes Wi-Fi nas proximidades, evite utilizar canais Wi-Fi utilizados por estas outras redes. Pode utilizar ferramentas de digitalização de rede como o [WifiInfoView](https://www.nirsoft.net/utils/wifi_information_view.html) para verificar se os canais que a sua rede Wi-Fi utiliza, estão isentos de tráfego concorrente.
* **Evite estritamente utilizar repetidores Wi-Fi** ou reencaminhamento de linha lan-over-powerline.
* **Evite um tráfego comandado e intenso** – como o streaming de vídeo ou jogos – na mesma rede Wi-Fi.
* Ter **uma boa força de sinal Wi-Fi** é essencial. Se possível, mantenha-se perto do seu ponto de acesso Wi-Fi e evite obstáculos entre o dispositivo do cliente e os pontos de acesso.
* Certifique-se de que liga sempre ao centro de **dados Azure mais próximo** da sua [região](regions.md). Quanto mais perto o centro de dados, menor é a latência da rede, o que tem um enorme efeito na estabilidade do holograma.

> [!NOTE]
> A largura de banda a jusante é maioritariamente consumida pelo fluxo de vídeo, que por sua vez é dividido entre informações de cor e profundidade (ambos 60 Hz, estéreo).

## <a name="network-performance-tests"></a>Testes de desempenho da rede

Se quiser ter uma compreensão inicial sobre se a qualidade da sua conectividade de rede é suficiente para executar a Renderização Remota Azure, existem ferramentas online existentes que pode utilizar. Recomendamos vivamente executar estas ferramentas on-line a partir de um portátil razoavelmente potente ligado ao mesmo Wi-Fi que o dispositivo em que está a planear executar a sua aplicação de cliente de Renderização Remota Azure. Os resultados obtidos com a execução dos testes num telemóvel ou holoLens2 são geralmente menos úteis, uma vez que provaram apresentar uma variação significativa em dispositivos de ponto final de baixa potência. O local onde se colocam o portátil deve estar aproximadamente no mesmo local onde espera utilizar o dispositivo que executa a sua aplicação de cliente de Renderização Remota Azure.

Aqui estão alguns passos simples para um teste rápido da conectividade da sua rede:

1. **Execute uma ferramenta de teste de rede como www.speedtest.net para obter dados sobre a latência geral e a largura de banda a montante/a jusante da sua ligação de rede.**
Escolha um servidor mais próximo de si e faça o teste. Embora o servidor não seja o centro de dados Azure a que a Renderização Remota Azure irá ligar, os dados resultantes ainda são úteis para entender o desempenho da sua ligação à Internet e Wi-Fi.
   * **Requisito mínimo** para renderização remota Azure: Aprox. 40 Mbps a jusante e 5 Mbps a montante.
   * **Recomendado** para renderização remota Azure: Aprox. 100 Mbps a jusante e 10 Mbps a montante.
Recomendamos executar o teste várias vezes e ter os piores resultados.
1. **Utilize uma ferramenta como www.azurespeed.com que mede a latência aos centros de dados Azure.** Selecione o centro de dados Azure suportado pela Renderização Remota Azure que está mais próximo de si (ver [regiões apoiadas)](regions.md)e faça um teste de **latência**. Se houver variação nos números que vê, dê aos resultados algum tempo para estabilizar.
   * **Requisito mínimo** para a renderização remota azure: A latência deve ser consistentemente inferior a 100 ms.
   * **Recomendado** para renderização remota Azure: A latência deve ser consistentemente inferior a 70 ms.

Embora a baixa latência não seja uma garantia de que a Renderização Remota Azure funcione bem na sua rede, normalmente vimos que funciona bem em situações em que estes testes passaram com sucesso.
Se encontrar artefactos como hologramas instáveis, nervosos ou saltando ao executar a Renderização Remota azure, consulte o guia de resolução de [problemas](../resources/troubleshoot.md).

## <a name="next-steps"></a>Passos seguintes

* [Quickstart: Render um modelo com Unidade](../quickstarts/render-model.md)
