---
title: Instruções de infraestrutura do Azure de exemplo | Documentos da Microsoft
description: Saiba mais sobre as diretrizes de design e implementação de chave para implementar uma infraestrutura de exemplo no Azure.
documentationcenter: ''
services: virtual-machines-linux
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 281fc2c0-b533-45fa-81a3-728c0049c73d
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 12/15/2017
ms.author: cynthn
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 00357641f51be703d2e5c52c5b9cc6187ce05ff6
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58002679"
---
# <a name="example-azure-infrastructure-walkthrough-for-linux-vms"></a>Infraestrutura do Azure de exemplo passo a passo para VMs do Linux
Este artigo explica-criar uma infraestrutura de aplicativo de exemplo. Vamos detalhar a estruturar uma infraestrutura para uma loja online simple, que reúne todas as diretrizes e as decisões sobre as convenções de nomenclatura, conjuntos de disponibilidade, redes virtuais e Balanceadores de carga e realmente implantar suas máquinas virtuais (VMs).

## <a name="example-workload"></a>Carga de trabalho de exemplo
Adventure Works Cycles quer criar uma aplicação de loja online no Azure, que consiste em:

* Dois servidores de nginx a executar o cliente front-end numa camada web
* Dois servidores de nginx para processar dados e pedidos numa camada de aplicação
* Duas partes de servidores de MongoDB de um cluster em partição horizontal para armazenar dados de produto e os pedidos numa camada de base de dados
* Dois controladores de domínio do Active Directory para contas de clientes e fornecedores numa camada de autenticação
* Todos os servidores estão localizados em duas sub-redes:
  * uma sub-rede de front-end para os servidores web 
  * uma sub-rede de back-end para os servidores de aplicações, cluster do MongoDB e controladores de domínio

![Diagrama das diferentes camadas para a infraestrutura de aplicação](./media/infrastructure-example/example-tiers.png)

Entrada segura tráfego da web deve ser com balanceamento de carga entre os servidores web à medida que os clientes procurar a loja online. Pedidos de tráfego na forma de HTTP de processamento de pedidos da web servidores tem de ser com balanceamento de carga entre os servidores de aplicações. Além disso, a infraestrutura deve ser projetada para elevada disponibilidade.

Tem de incorporar o design resultante:

* Uma conta e subscrição do Azure
* Um grupo de recursos
* Managed Disks do Azure
* Uma rede virtual com duas sub-redes
* Conjuntos de disponibilidade para as VMs com uma função semelhante
* Máquinas virtuais

Todos os anteriores, siga essas convenções de nomenclatura:

* Adventure Works Cycles utiliza **[carga de trabalho do IT]-[local]-[recursos do Azure]** como um prefixo
  * Neste exemplo, "**azos**" (Azure on-line Store) é o nome da carga de trabalho de TI e "**utilizar**" (E.U.A. Leste 2) é a localização
* Redes virtuais utilizam AZOS-USE-VN<strong>[número]</strong>
* Conjuntos de disponibilidade utilizam azos-use-como-**[função]**
* Nomes de máquina virtual utilizam azos-use-vm -**[vmname]**

## <a name="azure-subscriptions-and-accounts"></a>Contas e subscrições do Azure
Adventure Works Cycles está a utilizar a subscrição do Enterprise, com o nome Adventure Works Enterprise Subscription, para fornecer a faturação para esta carga de trabalho IT.

## <a name="storage"></a>Armazenamento
Adventure Works Cycles determinou que devem utilizar Managed Disks do Azure. Ao criar VMs, ambas as camadas de armazenamento disponível de armazenamento são utilizadas:

* **Armazenamento Standard** para os servidores web, servidores de aplicações e controladores de domínio e os discos de dados.
* **O armazenamento Premium** para os servidores de cluster em partição horizontal do MongoDB e seus discos de dados.

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
* **azos utilize como db** para os servidores do cluster em partição horizontal do MongoDB
* **azos utilização como dc** para os controladores de domínio

## <a name="virtual-machines"></a>Máquinas virtuais
Adventure Works Cycles decidiu sobre os seguintes nomes para as suas VMs do Azure:

* **azos-use-vm-web01** para o primeiro servidor de web
* **azos-use-vm-web02** para o segundo servidor web
* **azos-use-vm-app01** para o primeiro servidor de aplicação
* **azos-use-vm-app02** para o segundo servidor de aplicação
* **azos-use-vm-db01** para o primeiro servidor do MongoDB no cluster
* **azos-use-vm-db02** para o segundo servidor do MongoDB no cluster
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
