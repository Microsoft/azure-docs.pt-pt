---
title: Solução do Azure VMware por CloudSimple-configurar DNS de carga de trabalho e DHCP para nuvem privada
description: Descreve como configurar o DNS e o DHCP para aplicativos e cargas de trabalho em execução em seu ambiente de nuvem privada do CloudSimple
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: ce2394805bfc302179da807c798445c30ce9daff
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/19/2019
ms.locfileid: "69619550"
---
# <a name="set-up-dns-and-dhcp-applications-and-workloads-in-your-cloudsimple-private-cloud"></a>Configurar aplicativos e cargas de trabalho de DNS e DHCP em sua nuvem privada do CloudSimple

Os aplicativos e as cargas de trabalho em execução em um ambiente de nuvem privada exigem a resolução de nomes e os serviços DHCP para pesquisa e atribuição de endereço IP.  Uma infraestrutura apropriada de DHCP e DNS é necessária para fornecer esses serviços.  Você pode configurar uma máquina virtual para fornecer esses serviços em seu ambiente de nuvem privada.  

## <a name="prerequisites"></a>Pré-requisitos

* Um grupo de portas distribuídas com VLAN configurado
* Configuração de rota para servidores DNS locais ou baseados na Internet
* Modelo de máquina virtual ou ISO para criar uma máquina virtual

## <a name="linux-based-dns-server-setup"></a>Instalação do servidor DNS baseado em Linux

O Linux oferece vários pacotes para a configuração de servidores DNS.  Aqui está um [exemplo de configuração do DigitalOcean](https://www.digitalocean.com/community/tutorials/how-to-configure-bind-as-a-private-network-dns-server-on-centos-7) com instruções para configurar um servidor DNS de ligação de software livre.

## <a name="windows-based-setup"></a>Instalação baseada no Windows

Estes tópicos da Microsoft descrevem como configurar um servidor do Windows como um servidor DNS e como um servidor DHCP.

* [Servidor do Windows como servidor DNS](https://docs.microsoft.com/windows-server/networking/dns/dns-top)
* [Windows Server como servidor DHCP](https://docs.microsoft.com/windows-server/networking/technologies/dhcp/dhcp-top)
