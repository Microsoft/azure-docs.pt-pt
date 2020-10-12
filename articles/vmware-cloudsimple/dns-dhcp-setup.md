---
title: Azure VMware Solution by CloudSimple - Configurar a carga de trabalho DNS e DHCP para private Cloud
description: Descreve como configurar DNS e DHCP para aplicações e cargas de trabalho em execução no seu ambiente CloudSimple Private Cloud
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: d7531a22d5b0f44fdaae837a75d17ea18852acf0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87077301"
---
# <a name="set-up-dns-and-dhcp-applications-and-workloads-in-your-cloudsimple-private-cloud"></a>Configurar aplicações e cargas de trabalho DNS e DHCP na sua CloudSimple Private Cloud

Aplicações e cargas de trabalho em execução num ambiente private Cloud requerem resolução de nomes e serviços DHCP para procura e atribuição de endereços IP.  É necessária uma infraestrutura DHCP e DNS adequada para a prestação destes serviços.  Pode configurar uma máquina virtual para fornecer estes serviços no seu ambiente Private Cloud.  

## <a name="prerequisites"></a>Pré-requisitos

* Um grupo portuário distribuído com VLAN configurado
* Configuração de rota para instalações ou servidores DNS baseados na Internet
* Modelo de máquina virtual ou ISO para criar uma máquina virtual

## <a name="linux-based-dns-server-setup"></a>Configuração do servidor DNS baseada em Linux

O Linux oferece vários pacotes para a configuração de servidores DNS.  Aqui está uma [configuração de exemplo do DigitalOcean](https://www.digitalocean.com/community/tutorials/how-to-configure-bind-as-a-private-network-dns-server-on-ubuntu-18-04) com instruções para configurar um servidor DE DNS BIND de código aberto.

## <a name="windows-based-setup"></a>Configuração baseada no Windows

Estes tópicos da Microsoft descrevem como configurar um servidor Windows como um servidor DNS e como um servidor DHCP.

* [Servidor Windows como Servidor DNS](/windows-server/networking/dns/dns-top)
* [Servidor Windows como Servidor DHCP](/windows-server/networking/technologies/dhcp/dhcp-top)
