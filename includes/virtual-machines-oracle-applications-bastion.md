---
author: dlepow
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 07/10/2019
ms.author: danlep
ms.openlocfilehash: 35f506235f698fbcf42308e6f0b0f400e925df29
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "68361547"
---
### <a name="bastion-tier"></a>Bastião

O anfitrião do bastião é um componente opcional que pode usar como servidor de salto para aceder às instâncias de aplicação e base de dados. O anfitrião do bastião VM pode ter um endereço IP público atribuído ao mesmo, embora a recomendação seja a criação de uma ligação ExpressRoute ou VPN site-to-site com a sua rede no local para acesso seguro. Além disso, apenas o SSH (porta 22, Linux) ou RDP (porta 3389, Windows Server) devem ser abertos para o tráfego de entrada. Para uma elevada disponibilidade, coloque um anfitrião de bastião em duas zonas de disponibilidade ou num único conjunto de disponibilidade.

Também pode ativar o reencaminhado do agente SSH nos seus VMs, o que lhe permite aceder a outros VMs na rede virtual, reencaminhando as credenciais do seu anfitrião do bastião. Ou, use túneis SSH para aceder a outras instâncias.

Aqui está um exemplo de reencaminhamento de agente:

```
ssh -A -t user@BASTION_SERVER_IP ssh -A root@TARGET_SERVER_IP`
```

Este comando liga-se ao bastião `ssh` e, em seguida, corre imediatamente novamente, para obter um terminal na instância alvo. Pode ser necessário especificar um utilizador que não seja a raiz na instância-alvo se o seu cluster estiver configurado de forma diferente. O `-A` argumento repara a ligação do agente para que a sua chave privada na sua máquina local seja utilizada automaticamente. Note que o reencaminhamento do agente `ssh` é `-A` uma corrente, pelo que o segundo comando também inclui para que quaisquer ligações SSH subsequentes iniciadas a partir da instância-alvo também utilizem a chave privada local.