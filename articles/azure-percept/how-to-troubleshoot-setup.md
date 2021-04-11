---
title: Problemas de resolução de problemas durante a experiência de configuração do Azure Percept DK
description: Obtenha dicas de resolução de problemas para algumas das questões mais comuns encontradas durante a experiência de configuração
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: how-to
ms.date: 03/25/2021
ms.custom: template-how-to
ms.openlocfilehash: 7ce13cedff9afc25900c0bf75359ae49cc29fe19
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105608498"
---
# <a name="azure-percept-dk-setup-experience-troubleshooting-guide"></a>Azure Percept DK guia de resolução de problemas

Consulte a tabela abaixo para obter soluções alternativas às questões comuns encontradas durante a experiência de configuração do [Azure Percept DK](./quickstart-percept-dk-set-up.md). Se o seu problema persistir, contacte o suporte ao cliente da Azure.

|Problema|Razão|Solução|
|:-----|:------|:----------|
|Ao ligar-se às páginas de inscrição da conta Azure ou ao portal Azure, poderá iniciar súm na internet automaticamente com uma conta em cache. Se esta não for a conta que pretendia utilizar, pode resultar numa experiência inconsistente com a documentação.|Isto deve-se geralmente a uma configuração no navegador para "lembrar" uma conta que já utilizou anteriormente.|Na página Azure, clique no nome da sua conta no canto superior direito e selecione **para fora**. Poderá então iniciar scontabilidade com a conta correta.|
|O Azure Percept DK Wi-Fi ponto de acesso (scz-xxxx ou apd-xxxx) não consta da lista de redes Wi-Fi disponíveis.|Esta é geralmente uma questão temporária que resolve dentro de 15 minutos.|Aguarde que a rede apareça. Se não aparecer passados mais de 15 minutos, reinicie o aparelho.|
|A ligação ao Azure Percept DK Wi-Fi ponto de acesso desliga-se frequentemente.|Isto pode dever-se a uma má ligação entre o dispositivo e o computador anfitrião. Também pode ser causado por interferências de outras ligações Wi-Fi no computador anfitrião.|Certifique-se de que as antenas estão corretamente ligadas ao kit dev. Se o kit dev estiver longe do computador anfitrião, tente movê-lo mais perto. Desligue quaisquer outras ligações à Internet, tais como LTE/5G, se estiverem a funcionar no computador anfitrião.|
|O computador anfitrião mostra um aviso de segurança sobre a ligação ao ponto de acesso Azure Percept DK.|Esta é uma questão conhecida que será corrigida numa atualização posterior.|É seguro proceder através da experiência de configuração.|
|O Azure Percept DK Wi-Fi ponto de acesso (scz-xxxx ou apd-xxxx) aparece na lista de rede, mas não consegue ligar-se.|Isto pode ser devido a uma corrupção temporária do ponto de acesso Wi-Fi do dev kit.|Reinicie o dev kit e tente novamente.|
|Não é possível ligar-se a uma rede Wi-Fi durante a experiência de configuração.|A rede Wi-Fi deve ter atualmente conectividade na Internet para comunicar com o Azure. A EAP[PEAP/MSCHAP], os portais em cativeiro e a conectividade EAP-TLS da empresa não são atualmente suportados.|Certifique-se de que o seu tipo de rede Wi-Fi é suportado e tem conectividade com a Internet.|