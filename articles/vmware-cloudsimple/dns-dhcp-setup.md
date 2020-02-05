---
title: Soluções Azure VMware (AVS) - Configurar carga de trabalho DNS e DHCP para AVS Private Cloud
description: Descreve como configurar DNS e DHCP para aplicações e cargas de trabalho em execução no seu ambiente AVS Private Cloud
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: f2a5cae868f2d8f3689f05dd9d466715ab2008a3
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/05/2020
ms.locfileid: "77024692"
---
# <a name="set-up-dns-and-dhcp-applications-and-workloads-in-your-avs-private-cloud"></a>Configurar aplicações dNS e DHCP e cargas de trabalho na sua Nuvem Privada AVS

Aplicações e cargas de trabalho em funcionamento num ambiente De Nuvem Privada AVS requerem resolução de nomes e serviços DHCP para procura e atribuição de endereços IP. Uma infraestrutura apropriada de DHCP e DNS é necessária para fornecer esses serviços. Pode configurar uma máquina virtual para fornecer estes serviços no seu ambiente AVS Private Cloud. 

## <a name="prerequisites"></a>Pré-requisitos

* Um grupo de portas distribuídas com VLAN configurado
* Configuração de rota para servidores DNS locais ou baseados na Internet
* Modelo de máquina virtual ou ISO para criar uma máquina virtual

## <a name="linux-based-dns-server-setup"></a>Instalação do servidor DNS baseado em Linux

O Linux oferece vários pacotes para a configuração de servidores DNS. Aqui está um [exemplo de configuração do DigitalOcean](https://www.digitalocean.com/community/tutorials/how-to-configure-bind-as-a-private-network-dns-server-on-ubuntu-18-04) com instruções para configurar um servidor DNS de ligação de software livre.

## <a name="windows-based-setup"></a>Instalação baseada no Windows

Estes tópicos da Microsoft descrevem como configurar um servidor do Windows como um servidor DNS e como um servidor DHCP.

* [Servidor do Windows como servidor DNS](https://docs.microsoft.com/windows-server/networking/dns/dns-top)
* [Windows Server como servidor DHCP](https://docs.microsoft.com/windows-server/networking/technologies/dhcp/dhcp-top)
