---
title: Exemplo Azure infraestrutura walkthrough
description: Conheça as principais diretrizes de conceção e implementação para a implementação de uma infraestrutura de exemplo em Azure.
author: cynthn
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.topic: conceptual
ms.date: 12/15/2017
ms.author: cynthn
ms.openlocfilehash: 500de3f89bd041adf0b73e21762495d8c89e19c8
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/28/2020
ms.locfileid: "87286296"
---
# <a name="example-azure-infrastructure-walkthrough-for-linux-vms"></a>Exemplo Azure infraestrutura walkthrough para Linux VMs
Este artigo percorre a construção de uma infraestrutura de aplicação de exemplo. Detalhamos a conceção de uma infraestrutura para uma simples loja on-line que reúne todas as diretrizes e decisões em torno de convenções de nomeação, conjuntos de disponibilidade, redes virtuais e equilibradores de carga, e realmente implantando as suas máquinas virtuais (VMs).

## <a name="example-workload"></a>Exemplo de carga de trabalho
A Adventure Works Cycles quer construir uma aplicação de loja on-line em Azure que consiste em:

* Dois servidores nginx executando a frente do cliente em um nível web
* Dois servidores nginx processando dados e encomendas num nível de aplicação
* Dois servidores MongoDB parte de um cluster fragmento para armazenar dados de produtos e encomendas em um nível de base de dados
* Dois controladores de domínio de diretório ativo para contas de clientes e fornecedores num nível de autenticação
* Todos os servidores estão localizados em duas sub-redes:
  * uma sub-rede frontal para os servidores web 
  * uma sub-rede back-end para os servidores de aplicações, cluster MongoDB e controladores de domínio

![Diagrama de diferentes níveis para infraestruturas de aplicações](./media/infrastructure-example/example-tiers.png)

O tráfego web seguro de entrada deve ser equilibrado entre os servidores web à medida que os clientes navegam na loja on-line. Encomendar o tráfego de processamento sob a forma de pedidos HTTP dos servidores web deve ser equilibrado entre os servidores de aplicações. Além disso, a infraestrutura deve ser concebida para uma elevada disponibilidade.

O desenho ou modelo resultante deve incorporar:

* Uma subscrição e conta Azure
* Um único grupo de recursos
* Managed Disks do Azure
* Uma rede virtual com duas sub-redes
* Conjuntos de disponibilidade para os VMs com um papel semelhante
* Máquinas virtuais

Todas as convenções de nomeação:

* Adventure Works Cycles usa **[carga de trabalho de TI]-[location]-[Recurso Azure]** como prefixo
  * Para este exemplo, "**azos**" (Azure On-line Store) é o nome de carga de trabalho de TI e "**use**" (East US 2) é a localização
* As redes virtuais usam o AZOS-USE-VN<strong>[número]</strong>
* Os conjuntos de disponibilidade usam azos-use-as-**[função]**
* Os nomes de máquinas virtuais usam azos-use-vm-**[vmname]**

## <a name="azure-subscriptions-and-accounts"></a>Assinaturas e contas do Azure
A Adventure Works Cycles está a usar a sua assinatura Enterprise, chamada Adventure Works Enterprise Subscription, para fornecer faturação para esta carga de trabalho de TI.

## <a name="storage"></a>Armazenamento
Adventure Works Cycles determinou que deveriam usar discos geridos Azure. Ao criar VMs, ambos os níveis de armazenamento disponíveis são utilizados:

* **Armazenamento padrão** para os servidores web, servidores de aplicações e controladores de domínio e seus discos de dados.
* **Armazenamento premium** para os servidores de clusters de mongoDB e seus discos de dados.

## <a name="virtual-network-and-subnets"></a>Rede virtual e sub-redes
Como a rede virtual não necessita de conectividade contínua para a rede adventure work cycles no local, decidiram uma rede virtual apenas em nuvem.

Criaram uma rede virtual apenas em nuvem com as seguintes definições utilizando o portal Azure:

* Nome: AZOS-USE-VN01
* Localização: East US 2
* Espaço de endereço de rede virtual: 10.0.0.0/8
* Primeira sub-rede:
  * Nome: FrontEnd
  * Espaço do endereço: 10.0.1.0/24
* Segunda sub-rede:
  * Nome: BackEnd
  * Espaço do endereço: 10.0.2.0/24

## <a name="availability-sets"></a>Conjuntos de disponibilidade
Para manter a elevada disponibilidade dos quatro níveis da sua loja on-line, a Adventure Works Cycles decidiu em quatro conjuntos de disponibilidade:

* **azos-use-as-web** para os servidores web
* **azos-use-as-app** para os servidores de aplicações
* **azos-use-as-db** para os servidores do cluster de aço de MongoDB
* **azos-use-as-dc** para os controladores de domínio

## <a name="virtual-machines"></a>Máquinas virtuais
Adventure Works Cycles decidiu os seguintes nomes para os seus VMs Azure:

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

* Uma rede virtual só em nuvem com duas sub-redes (FrontEnd e BackEnd)
* Discos geridos Azure usando discos Standard e Premium
* Quatro conjuntos de disponibilidade, um para cada nível da loja on-line
* As máquinas virtuais para os quatro níveis
* Um conjunto de carga externa equilibrado para tráfego web baseado em HTTPS da Internet para os servidores web
* Um conjunto de carga interna equilibrado para tráfego web não encriptado dos servidores web para os servidores de aplicações
* Um único grupo de recursos
