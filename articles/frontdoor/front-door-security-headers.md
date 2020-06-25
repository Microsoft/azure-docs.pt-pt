---
title: Azure Front Door
description: Este artigo ensina-lhe como configurar um cabeçalho de segurança através do Motor de Regras na Porta frontal Azure
services: frontdoor
documentationcenter: ''
author: megan-beatty
editor: ''
ms.service: frontdoor
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 6/22/2020
ms.author: mebeatty
ms.openlocfilehash: 4d698375488d4dac551f0028883fc4e18a10d8ef
ms.sourcegitcommit: 01cd19edb099d654198a6930cebd61cae9cb685b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/24/2020
ms.locfileid: "85323540"
---
# <a name="add-security-headers-with-rules-engine"></a>Adicione cabeçalhos de segurança com o motor de regras

Implemente cabeçalhos de segurança para prevenir vulnerabilidades baseadas no navegador como HTTP Strict-Transport-Security (HSTS), X-XSS-Protection, Content-Security-Policy ou X-Frame-Options. Os atributos baseados em segurança também podem ser definidos com cookies.

O exemplo a seguir mostra-lhe como adicionar um cabeçalho de Política de Segurança de Conteúdo a todos os pedidos de entrada que correspondam ao caminho definido na rota com que a configuração do Motor de Regras está associada. Aqui, apenas permitimos que scripts do nosso site de confiança, **https://apis.contoso.com** para executar na nossa aplicação.

## <a name="add-a-content-security-policy-header-in-azure-portal"></a>Adicione um cabeçalho de Política de Segurança de Conteúdo no portal Azure

1. Antes de criar esta regra específica, aprenda a [criar uma porta frontal](quickstart-create-front-door.md) ou como criar um Motor de [Regras](front-door-tutorial-rules-engine.md) se esta for a sua primeira utilização ou AFD ou a funcionalidade Rules Engine.

2. Clique em **Adicionar** para adicionar uma nova regra. Forneça à regra um nome e, em seguida, clique **em Adicionar um**  >  **cabeçalho de resposta de ação**.

3. Desaprote o operador para ser **o apêndice** para adicionar este cabeçalho como resposta a todos os pedidos de entrada nesta rota.

4. Adicione o nome do cabeçalho: **Política de Segurança de Conteúdo** e defina os valores que este cabeçalho deve aceitar. Neste cenário, escolhemos *"script-src https://apis.contoso.com 'self'.".*

5. Depois de ter adicionado todas as regras que pretende à sua configuração, não se esqueça de ir à sua rota preferida e associar a configuração do Seu Motor de Regras à sua Regra de Rota. Este passo é necessário para permitir que a regra funcione. 

![amostra portal](./media/front-door-rules-engine/rules-engine-security-header-example.png)

> [!NOTE]
> Neste cenário, não acrescentámos [condições de jogo](front-door-rules-engine-match-conditions.md) à regra. Todos os pedidos de entrada que correspondam ao caminho definido na Regra da Rota terão esta regra aplicada. Se quiser que se aplique apenas a um subconjunto desses pedidos, certifique-se de adicionar as suas condições específicas de correspondência a esta regra.


## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre [o MOTOR DE Regras DA AFD](front-door-rules-engine.md). 
- Saiba como [criar um Front Door](quickstart-create-front-door.md).
- Saiba [como funciona o Front Door](front-door-routing-architecture.md).
- Saiba mais sobre [as condições](front-door-rules-engine-match-conditions.md) de jogo do Motor de Regras
- Confira mais na referência [CLI](https://docs.microsoft.com/cli/azure/ext/front-door/network/front-door/rules-engine?view=azure-cli-latest)do motor de regras da AFD. 
- Confira mais na referência AFD Rules Engine [PowerShell](https://docs.microsoft.com/powershell/module/az.frontdoor/?view=azps-3.8.0). 
