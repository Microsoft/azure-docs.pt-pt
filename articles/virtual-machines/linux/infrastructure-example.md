---
title: Explicação de exemplo de infraestrutura do Azure | Microsoft Docs
description: Saiba mais sobre as principais diretrizes de design e implementação para implantar uma infraestrutura de exemplo no Azure.
documentationcenter: ''
services: virtual-machines-linux
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 281fc2c0-b533-45fa-81a3-728c0049c73d
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 12/15/2017
ms.author: cynthn
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 71b0dd15d183f3209c7424c537dde1e3df29d097
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70083129"
---
# <a name="example-azure-infrastructure-walkthrough-for-linux-vms"></a>Instruções de infraestrutura do Azure de exemplo para VMs do Linux
Este artigo explica como criar uma infraestrutura de aplicativo de exemplo. Nós detalhamos a criação de uma infraestrutura para um armazenamento online simples que reúne todas as diretrizes e decisões sobre convenções de nomenclatura, conjuntos de disponibilidade, redes virtuais e balanceadores de carga, e realmente implantando suas VMs (máquinas virtuais).

## <a name="example-workload"></a>Carga de trabalho de exemplo
A Adventure Works Cycles deseja criar um aplicativo de armazenamento online no Azure que consiste em:

* Dois servidores Nginx executando o front-end do cliente em uma camada da Web
* Dois servidores Nginx processando dados e pedidos em uma camada de aplicativo
* Dois servidores MongoDB que fazem parte de um cluster fragmentado para armazenar dados de produtos e pedidos em uma camada de banco de dados
* Dois controladores de domínio Active Directory para contas de clientes e fornecedores em uma camada de autenticação
* Todos os servidores estão localizados em duas sub-redes:
  * uma sub-rede de front-end para os servidores Web 
  * uma sub-rede de back-end para os servidores de aplicativos, o cluster do MongoDB e os controladores de domínio

![Diagrama de diferentes camadas para infraestrutura de aplicativo](./media/infrastructure-example/example-tiers.png)

O tráfego de entrada seguro da Web deve ter balanceamento de carga entre os servidores Web, uma vez que os clientes navegam no repositório online. O tráfego de processamento de pedidos na forma de solicitações HTTP dos servidores Web deve ter balanceamento de carga entre os servidores de aplicativos. Além disso, a infraestrutura deve ser projetada para alta disponibilidade.

O design resultante deve incorporar:

* Uma assinatura e conta do Azure
* Um único grupo de recursos
* Managed Disks do Azure
* Uma rede virtual com duas sub-redes
* Conjuntos de disponibilidade para as VMs com uma função semelhante
* Máquinas virtuais

Todos os itens acima seguem estas convenções de nomenclatura:

* O Adventure Works Cycles usa **[carga de trabalho de ti]-[local]-[recurso do Azure]** como um prefixo
  * Neste exemplo, "**AZOs**" (armazenamento online do Azure) é o nome da carga de trabalho de ti e "**usar**" (leste dos EUA 2) é o local
* As redes virtuais usam AZOS-USE-VN<strong>[número]</strong>
* Os conjuntos de disponibilidade usam AZOs-use-as- **[função]**
* Os nomes de máquina virtual usam AZOs-use-VM- **[vmname]**

## <a name="azure-subscriptions-and-accounts"></a>Contas e assinaturas do Azure
A Adventure Works Cycles está usando sua assinatura corporativa, chamada de assinatura do Adventure Works Enterprise, para fornecer cobrança para essa carga de trabalho de ti.

## <a name="storage"></a>Armazenamento
O Adventure Works Cycles determinou que eles devem usar o Azure Managed Disks. Ao criar VMs, ambas as camadas de armazenamento disponíveis de armazenamento são usadas:

* **Armazenamento padrão** para servidores Web, servidores de aplicativos e controladores de domínio e seus discos de dados.
* **Armazenamento Premium** para os servidores de cluster fragmentados do MongoDB e seus discos de dados.

## <a name="virtual-network-and-subnets"></a>Rede virtual e sub-redes
Como a rede virtual não precisa de conectividade contínua com a rede local dos ciclos de trabalho da Adventure, ela decidiu em uma rede virtual somente em nuvem.

Eles criaram uma rede virtual somente em nuvem com as seguintes configurações usando o portal do Azure:

* Nome: AZOS-USE-VN01
* Local EUA Leste 2
* Espaço de endereço de rede virtual: 10.0.0.0/8
* Primeira sub-rede:
  * Nome: Front-End
  * Espaço de endereço: 10.0.1.0/24
* Segunda sub-rede:
  * Nome: BackEnd
  * Espaço de endereço: 10.0.2.0/24

## <a name="availability-sets"></a>Conjuntos de disponibilidade
Para manter a alta disponibilidade de todas as quatro camadas de seu armazenamento online, a Adventure Works Cycles decidiu quatro conjuntos de disponibilidade:

* **AZOs-use-as-Web** para os servidores Web
* **AZOs-use-as-app** para os servidores de aplicativos
* **AZOs-use-as-DB** para os servidores no cluster fragmentado do MongoDB
* **AZOs-use-as-DC** para os controladores de domínio

## <a name="virtual-machines"></a>Máquinas virtuais
A Adventure Works Cycles decidiu nos seguintes nomes para suas VMs do Azure:

* **AZOs-use-VM-web01** para o primeiro servidor Web
* **AZOs-use-VM-web02** para o segundo servidor Web
* **AZOs-use-VM-app01** para o primeiro servidor de aplicativos
* **AZOs-use-VM-app02** para o segundo servidor de aplicativos
* **AZOs-use-VM-DB01** para o primeiro servidor MongoDB no cluster
* **AZOs-use-VM-DB02** para o segundo servidor MongoDB no cluster
* **AZOs-use-VM-DC01** para o primeiro controlador de domínio
* **AZOs-use-VM-DC02** para o segundo controlador de domínio

Aqui está a configuração resultante.

![Infraestrutura final do aplicativo implantada no Azure](./media/infrastructure-example/example-config.png)

Esta configuração incorpora:

* Uma rede virtual somente em nuvem com duas sub-redes (front-end e BackEnd)
* Managed Disks do Azure usando discos Standard e Premium
* Quatro conjuntos de disponibilidade, um para cada camada do repositório online
* As máquinas virtuais para as quatro camadas
* Um conjunto de balanceamento de carga externo para tráfego da Web baseado em HTTPS da Internet para os servidores Web
* Um conjunto de balanceamento de carga interno para tráfego da Web não criptografado dos servidores Web para os servidores de aplicativos
* Um único grupo de recursos
