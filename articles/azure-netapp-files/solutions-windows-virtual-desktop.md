---
title: Utilização de Desktop Virtual do Windows com ficheiros Azure NetApp ; Microsoft Docs
description: Fornece orientação de boas práticas e plantas de amostra na implementação do Windows Virtual Desktop com ficheiros Azure NetApp.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/13/2020
ms.author: b-juche
ms.openlocfilehash: a003090fd610f2ac75895cccbf97750adbd4cfcd
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88258325"
---
# <a name="benefits-of-using-azure-netapp-files-with-windows-virtual-desktop"></a>Benefícios da utilização do Azure NetApp Files com o Windows Virtual Desktop 

Este artigo fornece orientações de boas práticas sobre a implementação do Windows Virtual Desktop (WVD) com ficheiros Azure NetApp.

O Azure NetApp Files é um serviço de armazenamento de ficheiros altamente performante da Azure. Pode fornecer até 450.000 IOPS e latência sub-milisegundo, capaz de suportar uma escala extremamente grande de implementações de Desktop Virtual do Windows. Pode ajustar a largura de banda e alterar o nível de serviço dos volumes dos seus Ficheiros Azure NetApp a pedido quase instantaneamente sem fazer uma pausa de IO, mantendo o acesso ao plano de dados. Esta capacidade permite-lhe otimizar facilmente a sua escala de implementação WVD por custos. Também pode criar instantâneos de volume de pontuação e pontuais eficientes do espaço sem impactar o desempenho do volume. Esta capacidade permite-lhe reverter os recipientes individuais do perfil do [utilizador FSLogix](https://docs.microsoft.com/azure/virtual-desktop/store-fslogix-profile) através de uma cópia do `~snapshot` diretório, ou reverter instantaneamente todo o volume de uma só vez através da capacidade de reverter o volume.  Com até 255 instantâneos (rotativos) no local para proteger um volume de perda de dados ou corrupção, os administradores têm muitas hipóteses de desfazer o que foi feito.

## <a name="sample-blueprints"></a>Plantas de amostra

As seguintes plantas de amostra mostram a integração do Windows Virtual Desktop com ficheiros Azure NetApp. Num cenário de ambiente de trabalho em conjunto, os utilizadores são direcionados para a melhor sessão disponível (o [primeiro modo de amplitude)](https://docs.microsoft.com/azure/virtual-desktop/host-pool-load-balancing#breadth-first-load-balancing-method)hospedar na piscina, utilizando [máquinas virtuais multi-sessão](https://docs.microsoft.com/azure/virtual-desktop/windows-10-multisession-faq#what-is-windows-10-enterprise-multi-session). Por outro lado, os ambientes de trabalho pessoais são reservados para cenários em que cada utilizador tem a sua própria máquina virtual.

### <a name="pooled-desktop-scenario"></a>Cenário de ambiente de trabalho em piscina

Para o cenário em conjunto, a equipa de Desktop Virtual do Windows [recomenda](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/virtual-machine-recs#multi-session-recommendations) a seguinte orientação por contagem de utilizadores para vCPU. Note que não está especificado nenhum tamanho de máquina virtual nesta recomendação.

|     Tipo de carga de trabalho     |     Claro    |     Médio    |     Pesado    |
|-----------------------|--------------|---------------|--------------|
|     Utilizadores por vCPU    |     6        |     4         |     2        |


Esta recomendação é confirmada por um teste LoginVSI de 500 utilizadores, registando aproximadamente 62 "utilizadores de conhecimento/meio" em cada D16as_V4 máquina virtual. 

Como exemplo, em 62 utilizadores por D16as_V4 máquina virtual, o Azure NetApp Files pode suportar facilmente 60.000 utilizadores por ambiente. Estão em curso testes para avaliar o limite superior da D32as_v4 máquina virtual. Se a recomendação do utilizador WVD por vCPU se mantiver fiel ao D32as_v4, mais de 120.000 utilizadores caberiam dentro de 1.000 máquinas virtuais antes de abordar [o limite de 1.000 IP VNet,](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-network-topologies)como mostrado no seguinte número.  

![Cenário de ambiente de trabalho em conjunto com desktop virtual do Windows](../media/azure-netapp-files/solutions-pooled-desktop-scenario.png)   

### <a name="personal-desktop-scenario"></a>Cenário de ambiente de trabalho pessoal 

Num cenário de ambiente de trabalho pessoal, o seguinte número mostra a recomendação arquitetónica de fins gerais. Os utilizadores estão mapeados para pods de ambiente de trabalho específicos e cada pod tem pouco menos de 1.000 máquinas virtuais, deixando espaço para endereços IP que se propagam a partir da gestão VNet. O Azure NetApp Files pode facilmente lidar com mais de 900 desktops pessoais por cada sessão de anfitrião VNet, com o número real de máquinas virtuais a ser igual a 1.000 menos o número de anfitriões de gestão encontrados no Hub VNet. Se forem necessários mais desktops pessoais, é fácil adicionar mais pods (piscinas hospedeiras e redes virtuais), como mostra a seguinte figura. 

![Cenário de ambiente de trabalho pessoal do Windows Virtual Desktop](../media/azure-netapp-files/solutions-personal-desktop-scenario.png)  

Ao construir uma arquitetura baseada em POD como esta, atribuir os utilizadores ao pod correto no início de sessão é importante para garantir que os utilizadores encontrarão sempre os seus perfis de utilizador. 

## <a name="next-steps"></a>Passos seguintes

- [Arquiteturas de solução com o Azure NetApp Files](azure-netapp-files-solution-architectures.md)
