---
title: Problemas de resolução de problemas durante a experiência de embarque para Azure Percept DK
description: Obtenha dicas de resolução de problemas para algumas das questões mais comuns encontradas durante a experiência de embarque
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: how-to
ms.date: 02/18/2021
ms.custom: template-how-to
ms.openlocfilehash: d71cfa6ba52052e4b68175be84934c8b4294ed25
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2021
ms.locfileid: "101662910"
---
# <a name="azure-percept-dk-onboarding-experience-troubleshooting-guide"></a>Azure Percept DK guia de resolução de problemas

Aqui estão alguns problemas que você pode encontrar durante a experiência de ação de bordo Azure Percept DK. Se depois de utilizar os passos deste guia, a questão ainda persiste. Contacte o suporte ao cliente da Azure.

|Problema|Razão|Solução|
|:-----|:------|:----------|
|Ao ligar-se às páginas de inscrição da conta Azure ou ao portal Azure, poderá iniciar súm na internet automaticamente com uma conta em cache. Se esta não for a conta que pretendia utilizar, pode resultar numa experiência inconsistente com a documentação.|Isto deve-se geralmente a uma configuração no navegador para "lembrar" uma conta que já utilizou anteriormente.|Na página Azure, clique no canto superior direito da página onde mostra o nome da sua conta e, em seguida, clique em "assinar". Poderá então iniciar scontabilidade com a conta correta.|
|A rede Azure Percept DK (scz-xxxx) não consta da lista de redes Wi-Fi disponíveis.|Esta é geralmente uma questão temporária que se resolve com um pouco de tempo.|Aguarde que a rede apareça. Se não o fizer após mais de 15 minutos, reinicie o aparelho.|
|A ligação ao ponto de acesso Azure Percept DK desliga-se frequentemente.|Isto pode dever-se a uma má ligação entre o dispositivo e o computador anfitrião. Também pode ser causado por interferências de outras ligações Wi-Fi no computador anfitrião.|Certifique-se de que as antenas estão corretamente ligadas ao kit dev. Se o kit dev estiver longe do computador anfitrião, tente movê-lo mais perto. Desligue quaisquer outras ligações à Internet, tais como LTE/5G, se estiverem a funcionar no computador anfitrião.|
|O computador anfitrião mostra um aviso de segurança sobre a ligação ao ponto de acesso Azure Percept DK.|Esta é uma questão conhecida que será corrigida numa atualização posterior.|É seguro proceder através da experiência de embarque sobre o ponto de acesso devkit Wi-Fi.|
|A rede Azure Percept DK (scz-xxxx) aparece na lista de rede, mas não consegue ligar-se.|Isto pode dever-se a uma corrupção temporária do devkit Wi-Fi ponto de acesso.|Reinicie o devkit e tente de novo.|
|Não é possível ligar-se a uma rede Wi-Fi durante a experiência de configuração.|A rede Wi-Fi deve ter atualmente conectividade na Internet para que possamos comunicar com o Azure. A EAP[PEAP/MSCHAP], os portais em cativeiro e a conectividade EAP-TLS da Empresa não são atualmente suportados.|Certifique-se de que o tipo Wi-Fi rede que está a ligar é suportado e tem conectividade com a Internet.|