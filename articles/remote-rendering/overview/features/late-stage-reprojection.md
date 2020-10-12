---
title: Reprojeção da última fase
description: Informação sobre Reprojecção de Estágio Tardio e como usá-la.
author: sebastianpick
ms.author: sepick
ms.date: 02/04/2020
ms.topic: article
ms.openlocfilehash: 8d42087008f1812bc3713456025ed3be351d0917
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "84022185"
---
# <a name="late-stage-reprojection"></a>Reprojeção da última fase

*Reprojecção em Estágio Tardio* (LSR) é uma funcionalidade de hardware que ajuda a estabilizar hologramas quando o utilizador se move.

Espera-se que os modelos estáticos mantenham visualmente a sua posição quando se deslocam à sua volta. Se parecerem instáveis, este comportamento pode indicar problemas de LSR. Lembre-se que transformações dinâmicas adicionais, como animações ou vistas de explosão, podem mascarar este comportamento.

Pode escolher entre dois modos LSR diferentes, nomeadamente **Planar LSR** ou **Depth LSR**. Qual deles está ativo é determinado se o pedido do cliente submete um tampão de profundidade.

Ambos os modos LSR melhoram a estabilidade do holograma, embora tenham as suas limitações distintas. Comece por experimentar o Deep LSR, pois está indiscutivelmente a dar melhores resultados na maioria dos casos.

## <a name="choose-lsr-mode-in-unity"></a>Escolha o modo LSR na Unidade

No editor da Unidade, vá a *:::no-loc text="File > Build Settings":::* . Selecione *:::no-loc text="Player Settings":::* na parte inferior esquerda e, em seguida, verifique se é *:::no-loc text="Player > XR Settings > Virtual Reality SDKs > Windows Mixed Reality":::* **:::no-loc text="Enable Depth Buffer Sharing":::** verificado:

![Profundidade buffer sharing bandeira ativada](./media/unity-depth-buffer-sharing-enabled.png)

Se for, a sua aplicação utilizará O LSR de profundidade, caso contrário utilizará o Planar LSR.

## <a name="depth-lsr"></a>LSR de profundidade

Para que o LSR de profundidade funcione, a aplicação do cliente deve fornecer um tampão de profundidade válido que contenha toda a geometria relevante a considerar durante o LSR.

O LSR de profundidade tenta estabilizar a moldura de vídeo com base no conteúdo do tampão de profundidade fornecido. Como consequência, o conteúdo que não lhe foi prestado, como objetos transparentes, não pode ser ajustado pelo LSR e pode mostrar artefactos de instabilidade e reprojecção.

## <a name="planar-lsr"></a>Planar LSR

Planar LSR não tem informações de profundidade por pixel, como o Deep LSR tem. Em vez disso, reprojecta todos os conteúdos com base num plano que deve fornecer cada quadro.

Planar LSR reprova melhor os objetos que se encontram perto do avião fornecido. Quanto mais longe um objeto estiver, mais instável vai parecer. Embora o LSR de profundidade seja melhor a reprojetar objetos a diferentes profundidades, o Planar LSR pode funcionar melhor para o conteúdo que se alinha bem com um plano.

### <a name="configure-planar-lsr-in-unity"></a>Configure planar LSR em unidade

Os parâmetros do avião são derivados de um chamado ponto de *foco,* que você tem que fornecer cada frame através `UnityEngine.XR.WSA.HolographicSettings.SetFocusPointForFrame` . Consulte a [API](https://docs.microsoft.com/windows/mixed-reality/focus-point-in-unity) do Ponto de Foco de Unidade para obter mais detalhes. Se não definir um ponto de foco, será escolhido um recuo para si. No entanto, esse recuo automático conduz frequentemente a resultados sub-ideais.

Pode calcular o ponto de focagem por si mesmo, embora possa fazer sentido baseá-lo no calculado pelo anfitrião de renderização remota. Ligue `RemoteManagerUnity.CurrentSession.GraphicsBinding.GetRemoteFocusPoint` para obter isso. É-lhe pedido que forneça um quadro de coordenadas para expressar o ponto de foco. Na maioria dos casos, só vai querer fornecer o resultado `UnityEngine.XR.WSA.WorldManager.GetNativeISpatialCoordinateSystemPtr` daqui.

Normalmente, tanto o cliente como o anfitrião prestam conteúdo que o outro lado não sabe, como elementos de UI no cliente. Portanto, pode fazer sentido combinar o ponto de focagem remoto com um localmente calculado.

Os pontos de foco calculados em dois quadros sucessivos podem ser bastante diferentes. Simplesmente usá-los como é pode levar a hologramas que parecem estar pulando por aí. Para prevenir este comportamento, é aconselhável interpolar entre os pontos de foco anteriores e atuais.

## <a name="next-steps"></a>Passos seguintes

* [Consultas de desempenho do lado do servidor](performance-queries.md)
