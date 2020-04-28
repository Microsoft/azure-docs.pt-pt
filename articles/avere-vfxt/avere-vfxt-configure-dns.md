---
title: Avere vFXT DNS - Azure
description: Configurar um servidor DNS para equilibrar a carga de robin redondo com Avere vFXT para Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 12/19/2019
ms.author: rohogue
ms.openlocfilehash: 81b53904f85e2ac936195b1e39d7586fd1d47524
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76153790"
---
# <a name="avere-cluster-dns-configuration"></a>Configuração de DNS do cluster do Avere

Esta secção explica os fundamentos da configuração de um sistema DNS para equilibrar a carga do seu cluster Avere vFXT.

Este documento *não inclui* instruções para configurar e gerir um servidor DNS no ambiente Azure.

Em vez de utilizar dNS de robin redondo para equilibrar um cluster vFXT em Azure, considere usar métodos manuais para atribuir endereços IP uniformemente entre os clientes quando estes são montados. Vários métodos são descritos no [aglomerado de Monte avere.](avere-vfxt-mount-clients.md)

Tenha em mente estas coisas ao decidir se deve ou não utilizar um servidor DNS:

* Se o seu sistema for acedido apenas por clientes NFS, não é necessário utilizar DNS - é possível especificar todos os endereços da rede utilizando endereços IP numéricos.

* Se o seu sistema suportar o acesso sMB (CIFS), é necessário dNS, pois deve especificar um domínio DNS para o servidor Ative Directory.

* O DNS é necessário se quiser utilizar a autenticação Kerberos.

## <a name="load-balancing"></a>Balanceamento de carga

Para distribuir a carga global, configure o seu domínio DNS para utilizar a distribuição de carga de robin redondo para endereços IP virados para o cliente.

## <a name="configuration-details"></a>Detalhes da configuração

Quando os clientes acedem ao cluster, o RRDNS equilibra automaticamente os seus pedidos entre todas as interfaces disponíveis.

Para um desempenho ótimo, configure o seu servidor DNS para lidar com os endereços de cluster virados para o cliente, como mostrado no diagrama seguinte.

Um vserver de cluster é mostrado à esquerda, e os endereços IP aparecem no centro e na direita. Configure cada ponto de acesso a cada cliente com registos E indicações a ilustração.

![Diagrama de DNS de avere](media/avere-vfxt-rrdns-diagram.png)
<!--- separate text description file provided  [diagram text description](avere-vfxt-rrdns-alt-text.md) -->

Cada endereço IP virado para o cliente deve ter um nome único para uso interno pelo cluster. (Neste diagrama, os IPs do cliente são nomeados vs1-cliente-IP-* para clareza, mas na produção você provavelmente deve usar algo mais conciso, como cliente*.)

Os clientes montam o cluster usando o nome do servidor como argumento do servidor.

Modifique o ficheiro ``named.conf`` do servidor DNS para definir a ordem cíclica para consultas ao seu servidor de vserver. Esta opção garante que todos os valores disponíveis sejam concluídos. Adicione uma declaração como a seguinte:

```
options {
    rrset-order {
        class IN A name "vserver1.example.com" order cyclic;
    };
};
```

Os ``nsupdate`` seguintes comandos fornecem um exemplo de configuração correta do DNS:

```
update add vserver1.example.com. 86400 A 10.0.0.10
update add vserver1.example.com. 86400 A 10.0.0.11
update add vserver1.example.com. 86400 A 10.0.0.12
update add vs1-client-IP-10.example.com. 86400 A 10.0.0.10
update add vs1-client-IP-11.example.com. 86400 A 10.0.0.11
update add vs1-client-IP-12.example.com. 86400 A 10.0.0.12
update add 10.0.0.10.in-addr.arpa. 86400 PTR vs1-client-IP-10.example.com
update add 11.0.0.10.in-addr.arpa. 86400 PTR vs1-client-IP-11.example.com
update add 12.0.0.10.in-addr.arpa. 86400 PTR vs1-client-IP-12.example.com
```

## <a name="cluster-dns-settings"></a>Configurações dNS do cluster

Especifique o servidor DNS que o cluster vFXT utiliza na página **de** > definições da**Rede Administrativa** cluster. As definições nessa página incluem:

* Endereço de servidor DNS
* Nome de domínio DNS
* Domínios de pesquisa dNS

Leia [as definições de DNS](<https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_admin_network.html#gui-dns>) no Guia de Configuração do Cluster Avere para obter mais detalhes sobre a utilização desta página.
