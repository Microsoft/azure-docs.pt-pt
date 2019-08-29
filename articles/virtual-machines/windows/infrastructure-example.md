---
title: Explicação de exemplo de infraestrutura do Azure | Microsoft Docs
description: Saiba mais sobre as principais diretrizes de design e implementação para implantar uma infraestrutura de exemplo no Azure.
documentationcenter: ''
services: virtual-machines-windows
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 7032b586-e4e5-4954-952f-fdfc03fc1980
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 12/15/2017
ms.author: cynthn
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: f4191015ee4dc7eb753c70f23be242f2ca88dcc3
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70079392"
---
# <a name="example-azure-infrastructure-walkthrough-for-windows-vms"></a>Instruções de infraestrutura do Azure de exemplo para VMs do Windows
Este artigo explica como criar uma infraestrutura de aplicativo de exemplo. Nós detalhamos a criação de uma infraestrutura para uma loja online simples que reúne todas as diretrizes e decisões sobre convenções de nomenclatura, conjuntos de disponibilidade, redes virtuais e balanceadores de carga e, na verdade, implantando suas VMs (máquinas virtuais).

## <a name="example-workload"></a>Carga de trabalho de exemplo
A Adventure Works Cycles deseja criar um aplicativo de loja online no Azure que consiste em:

* Dois servidores IIS executando o front-end do cliente em uma camada da Web
* Dois servidores IIS processando dados e pedidos em uma camada de aplicativo
* Duas instâncias de Microsoft SQL Server com grupos de disponibilidade AlwaysOn (dois SQL Servers e uma testemunha de nó principal) para armazenar dados de produtos e pedidos em uma camada de banco de dados
* Dois controladores de domínio Active Directory para contas de clientes e fornecedores em uma camada de autenticação
* Todos os servidores estão localizados em duas sub-redes:
  * uma sub-rede de front-end para os servidores Web 
  * uma sub-rede de back-end para os servidores de aplicativos, o cluster do SQL e os controladores de domínio

![Diagrama de diferentes camadas para infraestrutura de aplicativo](./media/infrastructure-example/example-tiers.png)

O tráfego de entrada seguro da Web deve ter balanceamento de carga entre os servidores Web à medida que os clientes navegam na loja online. O tráfego de processamento de pedidos na forma de solicitações HTTP dos servidores Web deve ser balanceado entre os servidores de aplicativos. Além disso, a infraestrutura deve ser projetada para alta disponibilidade.

O design resultante deve incorporar:

* Uma assinatura e conta do Azure
* Um único grupo de recursos
* Managed Disks do Azure
* Uma rede virtual com duas sub-redes
* Conjuntos de disponibilidade para as VMs com uma função semelhante
* Máquinas virtuais

Todos os itens acima seguem estas convenções de nomenclatura:

* O Adventure Works Cycles usa **[carga de trabalho de ti]-[local]-[recurso do Azure]** como um prefixo
  * Para este exemplo, "**AZOs**" (Azure Online Store) é o nome da carga de trabalho de ti e "**use**" (leste dos EUA 2) é o local
* As redes virtuais usam AZOS-USE-VN **[número]**
* Os conjuntos de disponibilidade usam AZOs-use-as- **[função]**
* Os nomes de máquina virtual usam AZOs-use-VM- **[vmname]**

## <a name="azure-subscriptions-and-accounts"></a>Contas e assinaturas do Azure
A Adventure Works Cycles está usando sua assinatura corporativa, chamada de assinatura do Adventure Works Enterprise, para fornecer cobrança para essa carga de trabalho de ti.

## <a name="storage"></a>Armazenamento
O Adventure Works Cycles determinou que eles devem usar o Azure Managed Disks. Ao criar VMs, ambas as camadas de armazenamento disponíveis são usadas:

* **Armazenamento padrão** para servidores Web, servidores de aplicativos e controladores de domínio e seus discos de dados.
* **Armazenamento Premium** para as VMs SQL Server e seus discos de dados.

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
Para manter a alta disponibilidade de todas as quatro camadas de sua loja online, a Adventure Works Cycles decidiu quatro conjuntos de disponibilidade:

* **AZOs-use-as-Web** para os servidores Web
* **AZOs-use-as-app** para os servidores de aplicativos
* **AZOs-use-as-SQL** para os SQL Servers
* **AZOs-use-as-DC** para os controladores de domínio

## <a name="virtual-machines"></a>Máquinas virtuais
A Adventure Works Cycles decidiu nos seguintes nomes para suas VMs do Azure:

* **AZOs-use-VM-web01** para o primeiro servidor Web
* **AZOs-use-VM-web02** para o segundo servidor Web
* **AZOs-use-VM-app01** para o primeiro servidor de aplicativos
* **AZOs-use-VM-app02** para o segundo servidor de aplicativos
* **AZOs-use-VM-sql01** para o primeiro servidor de SQL Server no cluster
* **AZOs-use-VM-sql02** para o segundo servidor de SQL Server no cluster
* **AZOs-use-VM-DC01** para o primeiro controlador de domínio
* **AZOs-use-VM-DC02** para o segundo controlador de domínio

Aqui está a configuração resultante.

![Infraestrutura final do aplicativo implantada no Azure](./media/infrastructure-example/example-config.png)

Esta configuração incorpora:

* Uma rede virtual somente em nuvem com duas sub-redes (front-end e BackEnd)
* Managed Disks do Azure com discos Standard e Premium
* Quatro conjuntos de disponibilidade, um para cada camada da loja online
* As máquinas virtuais para as quatro camadas
* Um conjunto de balanceamento de carga externo para tráfego da Web baseado em HTTPS da Internet para os servidores Web
* Um conjunto de balanceamento de carga interno para tráfego da Web não criptografado dos servidores Web para os servidores de aplicativos
* Um único grupo de recursos
