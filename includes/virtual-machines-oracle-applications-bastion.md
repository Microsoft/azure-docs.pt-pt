---
author: dlepow
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 07/10/2019
ms.author: danlep
ms.openlocfilehash: 35f506235f698fbcf42308e6f0b0f400e925df29
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "68361547"
---
### <a name="bastion-tier"></a>Nível de bastião

O hospedeiro de bastião é um componente opcional que pode usar como servidor de salto para aceder às instâncias de aplicação e base de dados. O VM do anfitrião de bastião pode ter um endereço IP público atribuído ao mesmo, embora a recomendação seja a criação de uma ligação ExpressRoute ou VPN local-a-local com a sua rede no local para acesso seguro. Além disso, apenas O SSH (porta 22, Linux) ou RDP (porta 3389, Windows Server) deve ser aberto para o tráfego de entrada. Para uma elevada disponibilidade, desloque um hospedeiro de bastião em duas zonas de disponibilidade ou num único conjunto de disponibilidade.

Também pode ativar o encaminhamento do agente SSH nos seus VMs, o que lhe permite aceder a outros VMs na rede virtual, reencaminhando as credenciais do seu anfitrião de bastião. Ou, use túneis SSH para aceder a outras instâncias.

Aqui está um exemplo de encaminhamento de agente:

```
ssh -A -t user@BASTION_SERVER_IP ssh -A root@TARGET_SERVER_IP`
```

Este comando liga-se ao bastião e volta a funcionar `ssh` imediatamente, para que obtenha um terminal na instância do alvo. Pode ser necessário especificar um utilizador que não seja a raiz na instância-alvo se o seu cluster estiver configurado de forma diferente. O `-A` argumento remete a ligação do agente para que a sua chave privada na sua máquina local seja utilizada automaticamente. Note que o encaminhamento do agente é uma corrente, pelo que o segundo `ssh` comando também inclui para que `-A` quaisquer ligações SSH subsequentes iniciadas a partir da instância-alvo também utilizem a sua chave privada local.