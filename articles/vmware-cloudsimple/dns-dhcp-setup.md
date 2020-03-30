---
title: Solução Azure VMware by CloudSimple - Configurar carga de trabalho DNS e DHCP para nuvem privada
description: Descreve como configurar DNS e DHCP para aplicações e cargas de trabalho em execução no seu ambiente CloudSimple Private Cloud
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: ef1266b783034cf18dc2b3ea4be5ebc01bc12c70
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77024692"
---
# <a name="set-up-dns-and-dhcp-applications-and-workloads-in-your-cloudsimple-private-cloud"></a>Configurar aplicações DNS e DHCP e cargas de trabalho na sua CloudSimple Private Cloud

Aplicações e cargas de trabalho em funcionamento num ambiente privado de nuvem requerem resolução de nomes e serviços DHCP para procura e atribuição de endereços IP.  É necessária uma infraestrutura dHCP e DNS adequada para prestar estes serviços.  Pode configurar uma máquina virtual para fornecer estes serviços no seu ambiente Cloud Privado.  

## <a name="prerequisites"></a>Pré-requisitos

* Um grupo portuário distribuído com VLAN configurado
* Configuração de rota para servidores DNS baseados na Internet ou na Internet
* Modelo de máquina virtual ou ISO para criar uma máquina virtual

## <a name="linux-based-dns-server-setup"></a>Configuração do servidor DNS baseado em Linux

O Linux oferece vários pacotes para a configuração de servidores DNS.  Aqui está um [exemplo configurado a partir do DigitalOcean](https://www.digitalocean.com/community/tutorials/how-to-configure-bind-as-a-private-network-dns-server-on-ubuntu-18-04) com instruções para configurar um servidor BIND DNS de código aberto.

## <a name="windows-based-setup"></a>Configuração baseada no Windows

Estes tópicos da Microsoft descrevem como configurar um servidor Windows como um servidor DNS e como um servidor DHCP.

* [Servidor do Windows como Servidor DNS](https://docs.microsoft.com/windows-server/networking/dns/dns-top)
* [Servidor do Windows como Servidor DHCP](https://docs.microsoft.com/windows-server/networking/technologies/dhcp/dhcp-top)
