---
title: Instruções de infraestrutura do Azure de exemplo | Documentos da Microsoft
description: Saiba mais sobre as diretrizes de design e implementação de chave para implementar uma infraestrutura de exemplo no Azure.
documentationcenter: ''
services: virtual-machines-windows
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 7032b586-e4e5-4954-952f-fdfc03fc1980
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 12/15/2017
ms.author: cynthn
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: ddbaed6704fd32f7fd4fe5a790424cbf829d2f1c
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60540407"
---
# <a name="example-azure-infrastructure-walkthrough-for-windows-vms"></a>Infraestrutura do Azure de exemplo passo a passo para VMs do Windows
Este artigo explica-criar uma infraestrutura de aplicativo de exemplo. Vamos detalhar a estruturar uma infraestrutura para uma loja online simple, que reúne todas as diretrizes e as decisões sobre as convenções de nomenclatura, conjuntos de disponibilidade, redes virtuais e Balanceadores de carga e realmente implantar suas máquinas virtuais (VMs).

## <a name="example-workload"></a>Carga de trabalho de exemplo
Adventure Works Cycles quer criar uma aplicação de loja online no Azure, que consiste em:

* Dois servidores IIS que executem o cliente front-end numa camada web
* Dois servidores IIS para processar dados e pedidos numa camada de aplicação
* Duas instâncias do Microsoft SQL Server com grupos de Disponibilidade AlwaysOn (dois servidores de SQL e um testemunho de nó da maioria) para armazenar dados de produto e os pedidos numa camada de base de dados
* Dois controladores de domínio do Active Directory para contas de clientes e fornecedores numa camada de autenticação
* Todos os servidores estão localizados em duas sub-redes:
  * uma sub-rede de front-end para os servidores web 
  * uma sub-rede de back-end para os servidores de aplicações, o cluster SQL e controladores de domínio

![Diagrama das diferentes camadas para a infraestrutura de aplicação](./media/infrastructure-example/example-tiers.png)

Entrada segura tráfego da web deve ser com balanceamento de carga entre os servidores web à medida que os clientes procurar a loja online. Ordem de processamento de tráfego na forma de pedidos de HTTP da web servidores tem de ser balanceados entre os servidores de aplicações. Além disso, a infraestrutura deve ser projetada para elevada disponibilidade.

Tem de incorporar o design resultante:

* Uma conta e subscrição do Azure
* Um grupo de recursos
* Managed Disks do Azure
* Uma rede virtual com duas sub-redes
* Conjuntos de disponibilidade para as VMs com uma função semelhante
* Máquinas virtuais

Todos os anteriores, siga essas convenções de nomenclatura:

* Adventure Works Cycles utiliza **[carga de trabalho do IT]-[local]-[recursos do Azure]** como um prefixo
  * Neste exemplo, "**azos**" (Store de Online do Azure) é o nome da carga de trabalho de TI e "**utilizar**" (E.U.A. Leste 2) é a localização
* Redes virtuais utilizam AZOS-USE-VN **[número]**
* Conjuntos de disponibilidade utilizam azos-use-como- **[função]**
* Nomes de máquina virtual utilizam azos-use-vm - **[vmname]**

## <a name="azure-subscriptions-and-accounts"></a>Contas e subscrições do Azure
Adventure Works Cycles está a utilizar a subscrição do Enterprise, com o nome Adventure Works Enterprise Subscription, para fornecer a faturação para esta carga de trabalho IT.

## <a name="storage"></a>Armazenamento
Adventure Works Cycles determinou que devem utilizar Managed Disks do Azure. Ao criar VMs, são utilizadas ambas as camadas de armazenamento disponível:

* **Armazenamento Standard** para os servidores web, servidores de aplicações e controladores de domínio e os discos de dados.
* **O armazenamento Premium** para as VMs do SQL Server e respetivos discos de dados.

## <a name="virtual-network-and-subnets"></a>Rede virtual e sub-redes
Uma vez que a rede virtual não necessita de conectividade em curso para a rede no local de ciclos de trabalho de Adventure, eles decidiram numa rede virtual apenas na cloud.

Criaram uma rede virtual apenas na cloud com as seguintes definições no portal do Azure:

* Nome: AZOS-USE-VN01
* Localização: EUA Leste 2
* espaço de endereços de rede virtual: 10.0.0.0/8
* Primeira sub-rede:
  * Nome: FrontEnd
  * Espaço de endereços: 10.0.1.0/24
* Segunda sub-rede:
  * Nome: BackEnd
  * Espaço de endereços: 10.0.2.0/24

## <a name="availability-sets"></a>Conjuntos de disponibilidade
Para manter a elevada disponibilidade de todas as quatro camadas de sua loja online, a Adventure Works Cycles decidiu em quatro conjuntos de disponibilidade:

* **azos utilização como web** para os servidores web
* **azos utilização como aplicação** para os servidores de aplicações
* **azos utilização como sql** para servidores SQL
* **azos utilização como dc** para os controladores de domínio

## <a name="virtual-machines"></a>Máquinas virtuais
Adventure Works Cycles decidiu sobre os seguintes nomes para as suas VMs do Azure:

* **azos-use-vm-web01** para o primeiro servidor de web
* **azos-use-vm-web02** para o segundo servidor web
* **azos-use-vm-app01** para o primeiro servidor de aplicação
* **azos-use-vm-app02** para o segundo servidor de aplicação
* **azos-use-vm-sql01** para o primeiro servidor de SQL Server no cluster
* **azos-use-vm-sql02** para o segundo servidor do SQL Server no cluster
* **azos-use-vm-dc01** para o primeiro controlador de domínio
* **azos-use-vm-dc02** segundo controlador de domínio

Segue-se a configuração resultante.

![Infraestrutura de aplicativo final implementada no Azure](./media/infrastructure-example/example-config.png)

Esta configuração incorpora:

* Uma rede virtual apenas na cloud com duas sub-redes (front-end e back-end)
* Managed Disks do Azure com discos Standard e Premium
* Quatro conjuntos de disponibilidade, uma para cada camada da loja online
* As máquinas virtuais para as quatro camadas
* Um conjunto com balanceamento de carga externo para o tráfego da web baseado em HTTPS da Internet para os servidores web
* Uma carga internos com balanceamento de conjunto para o tráfego de web sem encriptação dos servidores web para os servidores de aplicações
* Um grupo de recursos
