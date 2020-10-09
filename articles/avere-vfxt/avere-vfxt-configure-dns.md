---
title: Avere vFXT DNS - Azure
description: Configurar um servidor DNS para o equilíbrio de carga de robin redondo com Avere vFXT para Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 12/19/2019
ms.author: rohogue
ms.openlocfilehash: 81b53904f85e2ac936195b1e39d7586fd1d47524
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "76153790"
---
# <a name="avere-cluster-dns-configuration"></a>Configuração de DNS do cluster do Avere

Esta secção explica os fundamentos básicos da configuração de um sistema DNS para o equilíbrio de carga do seu cluster Avere vFXT.

Este documento *não inclui* instruções para configurar e gerir um servidor DNS no ambiente Azure.

Em vez de utilizar o DNS round-robin para equilibrar um cluster vFXT em Azure, considere usar métodos manuais para atribuir endereços IP uniformemente entre os clientes quando estiverem montados. Vários métodos são descritos no [aglomerado do Monte Avere.](avere-vfxt-mount-clients.md)

Tenha estas coisas em mente ao decidir se deve ou não utilizar um servidor DNS:

* Se o seu sistema for acedido apenas por clientes NFS, não é necessário utilizar DNS - é possível especificar todos os endereços de rede utilizando endereços IP numéricos.

* Se o seu sistema suportar o acesso SMB (CIFS), é necessário DNS, pois tem de especificar um domínio DNS para o servidor Ative Directory.

* O DNS é necessário se pretender utilizar a autenticação Kerberos.

## <a name="load-balancing"></a>Balanceamento de carga

Para distribuir a carga global, configuure o seu domínio DNS para utilizar a distribuição de carga de robin redondo para endereços IP virados para o cliente.

## <a name="configuration-details"></a>Detalhes da configuração

Quando os clientes acedem ao cluster, a RRDNS equilibra automaticamente os seus pedidos entre todas as interfaces disponíveis.

Para obter um melhor desempenho, configuure o seu servidor DNS para manusear os endereços de cluster virados para o cliente, como mostrado no diagrama seguinte.

Um aglomerado é mostrado à esquerda, e os endereços IP aparecem no centro e à direita. Configure cada ponto de acesso ao cliente com registos E ponteiros A como ilustrado.

![Diagrama dns de agrupamento de Avere](media/avere-vfxt-rrdns-diagram.png)
<!--- separate text description file provided  [diagram text description](avere-vfxt-rrdns-alt-text.md) -->

Cada endereço IP virado para o cliente deve ter um nome único para uso interno pelo cluster. (Neste diagrama, os IPs do cliente são nomeados vs1-cliente-IP-* para clareza, mas na produção você provavelmente deve usar algo mais conciso, como cliente*.)

Os clientes montam o cluster usando o nome vserver como argumento do servidor.

Modifique o ficheiro do seu servidor DNS ``named.conf`` para definir a encomenda cíclica para consultas no seu vserver. Esta opção garante que todos os valores disponíveis sejam perceiros. Adicione uma declaração como a seguinte:

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

## <a name="cluster-dns-settings"></a>Definições de DNS de cluster

Especificar o servidor DNS que o cluster vFXT utiliza na página de definições da Rede Administrativa do **Cluster.**  >  **Administrative Network** As definições nessa página incluem:

* Endereço do servidor DNS
* Nome de domínio DNS
* Domínios de pesquisa de DNS

Leia [as Definições de DNS](<https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_admin_network.html#gui-dns>) no Guia de Configuração do Cluster Avere para obter mais detalhes sobre a utilização desta página.
