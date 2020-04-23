---
title: Exemplo De Passagem de Infraestruturas Azure
description: Conheça as principais diretrizes de conceção e implementação para a implementação de uma infraestrutura de exemplo no Azure.
author: cynthn
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.topic: example-scenario
ms.date: 12/15/2017
ms.author: cynthn
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 43e96b891e60dfcf8bc3c29b202bb60213905372
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/22/2020
ms.locfileid: "81869473"
---
# <a name="example-azure-infrastructure-walkthrough-for-windows-vms"></a>Exemplo de passagem de infraestrutura Azure para VMs Windows
Este artigo passa pela construção de uma infraestrutura de aplicação de exemplo. Detalhamos a conceção de uma infraestrutura para uma simples loja online que reúne todas as diretrizes e decisões em torno de convenções de nomeação, conjuntos de disponibilidade, redes virtuais e equilibradores de carga, e realmente implantando as suas máquinas virtuais (VMs).

## <a name="example-workload"></a>Carga de trabalho de exemplo
A Adventure Works Cycles quer construir uma aplicação de loja online em Azure que consiste em:

* Dois servidores IIS executando o lado frontal do cliente em um nível web
* Dois servidores IIS processam dados e encomendas num nível de aplicação
* Dois casos do Microsoft SQL Server com grupos de disponibilidade AlwaysOn (dois Servidores SQL e uma testemunha maioritária) para armazenar dados e encomendas de produtos num nível de base de dados
* Dois controladores de domínio de Diretório Ativo para contas de clientes e fornecedores em um nível de autenticação
* Todos os servidores estão localizados em duas subredes:
  * uma sub-rede frontal para os servidores web 
  * uma sub-rede de back-end para os servidores de aplicações, cluster SQL e controladores de domínio

![Diagrama de diferentes níveis para infraestrutura de aplicação](./media/infrastructure-example/example-tiers.png)

O tráfego web seguro de entrada deve ser equilibrado entre os servidores web à medida que os clientes navegam na loja online. O processamento de encomendas sob a forma de pedidos HTTP dos servidores web deve ser equilibrado entre os servidores da aplicação. Além disso, a infraestrutura deve ser concebida para uma elevada disponibilidade.

O desenho resultante deve incorporar:

* Uma subscrição e conta Azure
* Um único grupo de recursos
* Managed Disks do Azure
* Uma rede virtual com duas subredes
* Conjuntos de disponibilidade para os VMs com um papel semelhante
* Máquinas virtuais

Todas estas convenções de nomeação:

* Adventure Works Cycles usa [carga de **trabalho de TI]-[recurso Azure]** como prefixo
  * Para este exemplo, "**azos**" (Azure Online Store) é o nome da carga de trabalho de TI e "**use**" (East US 2) é o local
* As redes virtuais utilizam a AZOS-USE-VN **[número]**
* Os conjuntos de disponibilidade usam azos-use-as-[função]**[role]**
* Os nomes das máquinas virtuais usam azos-use-vm-**[vmname]**

## <a name="azure-subscriptions-and-accounts"></a>Assinaturas e contas azure
A Adventure Works Cycles está a usar a sua subscrição enterprise, chamada Adventure Works Enterprise Subscription, para fornecer faturação para esta carga de trabalho de TI.

## <a name="storage"></a>Armazenamento
A Adventure Works Cycles determinou que deveriam utilizar discos geridos azure. Ao criar VMs, ambos os níveis de armazenamento disponíveis são utilizados:

* **Armazenamento padrão** para os servidores web, servidores de aplicações e controladores de domínio e seus discos de dados.
* **Armazenamento premium** para os VMs do Servidor SQL e seus discos de dados.

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
Para manter a elevada disponibilidade dos quatro níveis da sua loja online, a Adventure Works Cycles decidiu em quatro conjuntos de disponibilidade:

* **azos-use-as-web** para os servidores web
* **azos-use-as-app** para os servidores de aplicações
* **azos-use-as-sql** para os Servidores SQL
* **azos-use-as-dc** para os controladores de domínio

## <a name="virtual-machines"></a>Máquinas virtuais
A Adventure Works Cycles decidiu sobre os seguintes nomes para os seus VMs Azure:

* **azos-use-vm-web01** para o primeiro servidor web
* **azos-use-vm-web02** para o segundo servidor web
* **azos-use-vm-app01** para o primeiro servidor de aplicações
* **azos-use-vm-app02** para o segundo servidor de aplicações
* **azos-use-vm-sql01** para o primeiro servidor SQL Server no cluster
* **azos-use-vm-sql02** para o segundo servidor SQL Server no cluster
* **azos-use-vm-dc01** para o primeiro controlador de domínio
* **azos-use-vm-dc02** para o segundo controlador de domínio

Aqui está a configuração resultante.

![Infraestrutura de aplicação final implantada em Azure](./media/infrastructure-example/example-config.png)

Esta configuração incorpora:

* Uma rede virtual só para nuvem com duas subredes (FrontEnd e BackEnd)
* Discos geridos azure com discos Standard e Premium
* Quatro conjuntos de disponibilidade, um para cada nível da loja online
* As máquinas virtuais para os quatro níveis
* Um conjunto externo equilibrado para o tráfego web baseado em HTTPS da Internet para os servidores web
* Um conjunto interno equilibrado de carga para tráfego web não encriptado dos servidores web para os servidores de aplicações
* Um único grupo de recursos
