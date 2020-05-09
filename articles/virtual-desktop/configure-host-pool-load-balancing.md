---
title: Configure Windows Virtual Desktop equilíbrio de carga - Azure
description: Como configurar o método de equilíbrio de carga para um ambiente de ambiente de trabalho virtual do Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 08/29/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 447de339d3ceef7aeb1c232605b0e30bbbb1e7d8
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/30/2020
ms.locfileid: "82612440"
---
# <a name="configure-the-windows-virtual-desktop-load-balancing-method"></a>Configurar o método de balanceamento de carga do Windows Virtual Desktop

Configurar o método de equilíbrio de carga para um pool de anfitriões permite-lhe ajustar o ambiente de ambiente de trabalho virtual do Windows para melhor atender às suas necessidades.

>[!NOTE]
> Isto não se aplica a um grupo de anfitriões persistente seletiva porque os utilizadores têm sempre um mapeamento 1:1 para um anfitrião de sessão dentro da piscina anfitriã.

## <a name="prerequisites"></a>Pré-requisitos

Este artigo pressupõe que seguiu as instruções no [Conjunto do módulo Windows Virtual Desktop PowerShell](powershell-module.md) para descarregar e instalar o módulo PowerShell e iniciar sessão na sua conta Azure.

## <a name="configure-breadth-first-load-balancing"></a>Configure o equilíbrio de primeira carga de amplitude

O equilíbrio de carga de primeira amplitude é a configuração padrão para novas piscinas hospedeiras não persistentes. O equilíbrio de carga de primeira largura distribui novas sessões de utilizador em todos os anfitriões disponíveis na piscina anfitriã. Ao configurar o equilíbrio de carga de primeira largura, pode definir um limite máximo de sessão por anfitrião da sessão na piscina anfitriã.

Para configurar uma piscina hospedeira para realizar um equilíbrio de carga de primeira largura sem ajustar o limite máximo de sessão, execute o seguinte cmdlet PowerShell:

```powershell
Update-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -LoadBalancerType 'BreadthFirst' 
```

Depois disso, para se certificar de que definiu o método de equilíbrio de carga de primeira largura, execute o seguinte cmdlet: 

```powershell
Get-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> | format-list Name, LoadBalancerType 

Name             : hostpoolname 
LoadBalancerType : BreadthFirst
```

Para configurar uma piscina hospedeira para realizar um equilíbrio de carga de primeira largura e utilizar um novo limite máximo de sessão, execute o seguinte cmdlet PowerShell:

```powershell
Update-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -LoadBalancerType 'BreadthFirst' -MaxSessionLimit ###
```

## <a name="configure-depth-first-load-balancing"></a>Configurar o equilíbrio de primeira carga de profundidade

O equilíbrio de carga de primeira profundidade distribui novas sessões de utilizador para um anfitrião de sessão disponível com o maior número de ligações, mas não atingiu o seu limite máximo de sessão. Ao configurar o equilíbrio de carga de profundidade primeiro, deve definir um limite máximo de sessão por anfitrião da sessão na piscina anfitriã.

Para configurar uma piscina hospedeira para realizar um equilíbrio de carga de primeira profundidade, execute o seguinte cmdlet PowerShell:

```powershell
Update-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -LoadBalancerType 'DepthFirst' -MaxSessionLimit ### 
```

Para se certificar de que a definição está atualizada, execute este cmdlet:

```powershell
Get-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> | format-list Name, LoadBalancerType, MaxSessionLimit 

Name             : hostpoolname
LoadBalancerType : DepthFirst
MaxSessionLimit  : 6
```

## <a name="configure-load-balancing-with-the-azure-portal"></a>Configure o equilíbrio da carga com o portal Azure

Também pode configurar o equilíbrio de carga com o portal Azure.

Para configurar o equilíbrio de carga:

1. Assine no portal Azure em https://portal.azure.com. 
2. Procure e selecione **O Ambiente de Trabalho Virtual do Windows** em Serviços. 
3. Na página do Windows Virtual Desktop, selecione **Pools host**.
4. Selecione o nome da piscina anfitriã que pretende editar.
5. Selecione **Propriedades**.
6. Introduza o limite de **sessão Max** no campo e selecione o algoritmo de equilíbrio de **carga** que deseja para esta piscina anfitriã no menu suspenso.
7. Selecione **Guardar**. Isto aplica as novas definições de equilíbrio de carga.