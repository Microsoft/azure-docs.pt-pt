---
title: Passo a infraestrutura azure exemplo
description: Conheça as principais diretrizes de conceção e implementação para a implementação de uma infraestrutura de exemplo no Azure.
author: cynthn
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.topic: article
ms.date: 12/15/2017
ms.author: cynthn
ms.openlocfilehash: 6040bf8862131f5a8a5564cd2f5d845fa0490a95
ms.sourcegitcommit: 8f4d54218f9b3dccc2a701ffcacf608bbcd393a6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/09/2020
ms.locfileid: "78944894"
---
# <a name="example-azure-infrastructure-walkthrough-for-linux-vms"></a>Exemplo de passagem de infraestrutura Azure para VMs Linux
Este artigo passa pela construção de uma infraestrutura de aplicação de exemplo. Detalhamos a conceção de uma infraestrutura para uma simples loja on-line que reúne todas as diretrizes e decisões em torno de nomeações, conjuntos de disponibilidade, redes virtuais e equilibradores de carga, e realmente implantando as suas máquinas virtuais (VMs).

## <a name="example-workload"></a>Carga de trabalho de exemplo
A Adventure Works Cycles quer construir uma aplicação de loja online em Azure que consiste em:

* Dois servidores nginx executando o cliente front-end em um nível web
* Dois servidores nginx processam dados e encomendas num nível de aplicação
* Dois servidores MongoDB fazem parte de um cluster fragmentado para armazenar dados e encomendas de produtos num nível de base de dados
* Dois controladores de domínio de Diretório Ativo para contas de clientes e fornecedores em um nível de autenticação
* Todos os servidores estão localizados em duas subredes:
  * uma sub-rede frontal para os servidores web 
  * uma sub-rede de back-end para os servidores de aplicações, cluster MongoDB e controladores de domínio

![Diagrama de diferentes níveis para infraestrutura de aplicação](./media/infrastructure-example/example-tiers.png)

O tráfego web seguro de entrada deve ser equilibrado entre os servidores web à medida que os clientes navegam na loja on-line. O processamento de encomendas sob a forma de pedidos HTTP dos servidores web deve ser equilibrado entre os servidores da aplicação. Além disso, a infraestrutura deve ser concebida para uma elevada disponibilidade.

O desenho resultante deve incorporar:

* Uma subscrição e conta Azure
* Um único grupo de recursos
* Managed Disks do Azure
* Uma rede virtual com duas subredes
* Conjuntos de disponibilidade para os VMs com um papel semelhante
* Máquinas virtuais

Todas estas convenções de nomeação:

* Adventure Works Cycles usa [carga de **trabalho de TI]-[recurso Azure]** como prefixo
  * Para este exemplo, "**azos**" (Azure On-line Store) é o nome da carga de trabalho de TI e "**use**" (East US 2) é o local
* As redes virtuais utilizam a AZOS-USE-VN<strong>[número]</strong>
* Os conjuntos de disponibilidade usam azos-use-as-[função]
* Os nomes das máquinas virtuais usam azos-use-vm- **[vmname]**

## <a name="azure-subscriptions-and-accounts"></a>Assinaturas e contas azure
A Adventure Works Cycles está a usar a sua subscrição enterprise, chamada Adventure Works Enterprise Subscription, para fornecer faturação para esta carga de trabalho de TI.

## <a name="storage"></a>Armazenamento
A Adventure Works Cycles determinou que deveriam utilizar discos geridos azure. Ao criar VMs, ambos os níveis de armazenamento disponíveis são utilizados:

* **Armazenamento padrão** para os servidores web, servidores de aplicações e controladores de domínio e seus discos de dados.
* **Armazenamento premium** para os servidores de cluster sharded MongoDB e seus discos de dados.

## <a name="virtual-network-and-subnets"></a>Rede virtual e sub-redes
Como a rede virtual não necessita de conectividade permanente com a rede Adventure Work Cycles on-premises, decidiram uma rede virtual apenas em nuvem.

Criaram uma rede virtual apenas em nuvem com as seguintes configurações utilizando o portal Azure:

* Nome: AZOS-USE-VN01
* Localização: East US 2
* Espaço de endereço de rede virtual: 10.0.0.0/8
* Primeira sub-rede:
  * Nome: FrontEnd
  * Espaço de endereço: 10.0.1.0/24
* Segunda sub-rede:
  * Nome: BackEnd
  * Espaço de endereço: 10.0.2.0/24

## <a name="availability-sets"></a>Conjuntos de disponibilidade
Para manter a elevada disponibilidade dos quatro níveis da sua loja on-line, a Adventure Works Cycles decidiu em quatro conjuntos de disponibilidade:

* **azos-use-as-web** para os servidores web
* **azos-use-as-app** para os servidores de aplicações
* **azos-use-as-db** para os servidores do cluster mongoDB fragmentado
* **azos-use-as-dc** para os controladores de domínio

## <a name="virtual-machines"></a>Máquinas virtuais
A Adventure Works Cycles decidiu sobre os seguintes nomes para os seus VMs Azure:

* **azos-use-vm-web01** para o primeiro servidor web
* **azos-use-vm-web02** para o segundo servidor web
* **azos-use-vm-app01** para o primeiro servidor de aplicações
* **azos-use-vm-app02** para o segundo servidor de aplicações
* **azos-use-vm-db01** para o primeiro servidor MongoDB no cluster
* **azos-use-vm-db02** para o segundo servidor MongoDB no cluster
* **azos-use-vm-dc01** para o primeiro controlador de domínio
* **azos-use-vm-dc02** para o segundo controlador de domínio

Aqui está a configuração resultante.

![Infraestrutura de aplicação final implantada em Azure](./media/infrastructure-example/example-config.png)

Esta configuração incorpora:

* Uma rede virtual só para nuvem com duas subredes (FrontEnd e BackEnd)
* Discos geridos azure usando discos Standard e Premium
* Quatro conjuntos de disponibilidade, um para cada nível da loja on-line
* As máquinas virtuais para os quatro níveis
* Um conjunto externo equilibrado para o tráfego web baseado em HTTPS da Internet para os servidores web
* Um conjunto interno equilibrado de carga para tráfego web não encriptado dos servidores web para os servidores de aplicações
* Um único grupo de recursos
