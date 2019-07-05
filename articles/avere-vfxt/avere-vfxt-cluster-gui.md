---
title: Aceder ao painel de controlo de vFXT Avere - Azure
description: Como ligar ao vFXT cluster e o painel de controle de Avere baseada no browser para configurar o vFXT Avere
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 06/24/2019
ms.author: v-erkell
ms.openlocfilehash: 830be92d37f304598cca05c3ac80973158c38a59
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67439976"
---
# <a name="access-the-vfxt-cluster"></a>Aceder ao cluster vFXT

Para alterar as definições e monitorizar o cluster de vFXT Avere, utilize o painel de controle de Avere. Painel de controlo de Avere é uma interface gráfica baseada no browser para o cluster.

Uma vez que o cluster vFXT encontra-se numa rede virtual privada, tem de criar um túnel SSH ou usar outro método para alcançar o endereço IP de gestão do cluster. Existem duas etapas básicas: 

1. Criar uma ligação entre a estação de trabalho e a vnet privada 
1. Carregar o painel de controlo do cluster num navegador da web 

> [!NOTE] 
> Este artigo pressupõe que definiu um endereço IP público no controlador de cluster ou em outra VM dentro da rede virtual do seu cluster. Este artigo descreve como usar essa VM como um anfitrião para aceder ao cluster. Se estiver a utilizar uma VPN ou ExpressRoute para o acesso de vnet, avance para o [ligue-se ao painel de controle Avere](#connect-to-the-avere-control-panel-in-a-browser).

Antes de ligar, certifique-se de que o par de chaves o SSH públicas/privadas que utilizou ao criar o controlador de cluster está instalado no seu computador local. Leia a documentação de chaves SSH para [Windows](https://docs.microsoft.com/azure/virtual-machines/linux/ssh-from-windows) ou para [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/mac-create-ssh-keys) se precisar de ajuda. (Se tiver utilizado uma palavra-passe em vez de uma chave pública, será solicitado introduzi-los quando se liga.) 

## <a name="create-an-ssh-tunnel"></a>Criar um túnel SSH 

Pode criar um túnel SSH a partir da linha de comando de um baseado em Linux ou o sistema de cliente do Windows 10. 

Utilize um comando com esta forma de túnel SSH: 

ssh -L *local_port*:*cluster_mgmt_ip*:443 *controller_username*\@*controller_public_IP*

Este comando liga ao endereço IP de gestão do cluster através do endereço IP do controlador cluster.

Exemplo:

```sh
ssh -L 8443:10.0.0.5:443 azureuser@203.0.113.51
```

A autenticação é automática, se utilizou a chave pública SSH para criar o cluster e a chave correspondente está instalada no sistema cliente. Se utilizou uma palavra-passe, o sistema solicitará que introduzi-los.

## <a name="connect-to-the-avere-control-panel-in-a-browser"></a>Ligar ao painel de controle Avere num browser

Este passo utiliza um navegador da web para ligar para o utilitário de configuração em execução no vFXT cluster.

* Para uma ligação de túnel SSH, abra o browser e navegue para `https://127.0.0.1:8443`. 

  Ligado ao cluster do endereço IP quando criou o túnel, portanto, só precisa de utilizar o endereço IP de localhost no browser. Se tiver utilizado uma porta local diferente 8443, em vez disso, a utilizar o seu número de porta.

* Se estiver a utilizar uma VPN ou ExpressRoute para alcançar o cluster, navegue para o endereço IP de gestão de cluster no seu browser. Exemplo: ``https://203.0.113.51``

Dependendo do seu browser, poderá ter de clicar **avançadas** e certifique-se de que é seguro avançar para a página.

Introduza o nome de utilizador `admin` e a palavra-passe administrativa que forneceu quando criou o cluster.

![Captura de ecrã do Avere iniciar sessão na página preenchida com o nome de utilizador "administrador" e uma palavra-passe](media/avere-vfxt-gui-login.png)

Clique em **início de sessão** ou prima enter no teclado.

## <a name="next-steps"></a>Passos Seguintes

Agora que pode aceder ao cluster, ative [suportar](avere-vfxt-enable-support.md).
