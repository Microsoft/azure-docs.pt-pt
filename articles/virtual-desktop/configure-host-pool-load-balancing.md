---
title: Configurar o equilíbrio de carga virtual do Windows Desktop - Azure
description: Como configurar o método de equilíbrio de carga para um ambiente de ambiente de trabalho virtual do Windows.
author: Heidilohr
ms.topic: how-to
ms.date: 10/12/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 2c57ac10fbd318dd4bbb2dc86457e186dd824834
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "91951660"
---
# <a name="configure-the-windows-virtual-desktop-load-balancing-method"></a>Configurar o método de balanceamento de carga do Windows Virtual Desktop

Configurar o método de equilíbrio de carga para um pool de anfitriões permite-lhe ajustar o ambiente de ambiente de trabalho virtual do Windows de forma melhor adequada às suas necessidades.

>[!NOTE]
> Isto não se aplica a um conjunto de anfitriões de ambiente de trabalho persistente, porque os utilizadores têm sempre um mapeamento de 1:1 para um anfitrião de sessão dentro da piscina de anfitriões.

## <a name="prerequisites"></a>Pré-requisitos

Este artigo pressupõe que seguiu as instruções na [Configuração do módulo Virtual Desktop PowerShell](powershell-module.md) do Windows para descarregar e instalar o módulo PowerShell e iniciar súm na sua conta Azure.

## <a name="configure-breadth-first-load-balancing"></a>Configurar o equilíbrio da primeira carga de largura

O equilíbrio de primeira carga é a configuração padrão para novas piscinas hospedeiras não persistentes. O equilíbrio de primeira carga distribui novas sessões de utilizador em todos os anfitriões disponíveis na piscina anfitriã. Ao configurar o equilíbrio de carga de primeira largura, pode definir um limite máximo de sessão por anfitrião na piscina de anfitrião.

Para configurar uma piscina hospedeira para efetuar o equilíbrio de primeira carga sem ajustar o limite máximo de sessão, execute o seguinte cmdlet PowerShell:

```powershell
Update-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -LoadBalancerType 'BreadthFirst'
```

Depois disso, para ter a certeza de que definiu o método de equilíbrio de primeira carga, corra o seguinte cmdlet:

```powershell
Get-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> | format-list Name, LoadBalancerType

Name             : hostpoolname
LoadBalancerType : BreadthFirst
```

Para configurar uma piscina hospedeira para realizar o equilíbrio de primeira carga e utilizar um novo limite máximo de sessão, execute o seguinte cmdlet PowerShell:

```powershell
Update-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -LoadBalancerType 'BreadthFirst' -MaxSessionLimit ###
```

## <a name="configure-depth-first-load-balancing"></a>Configurar o equilíbrio de primeira carga de profundidade

O equilíbrio de carga de profundidade distribui novas sessões de utilizador a um anfitrião de sessão disponível com o maior número de ligações, mas não atingiu o seu limite máximo de sessão.

>[!IMPORTANT]
>Ao configurar o equilíbrio de carga de profundidade primeiro, deve definir um limite máximo de sessão por anfitrião na piscina de anfitrião.

Para configurar uma piscina hospedeira para efetuar o equilíbrio de carga de profundidade primeiro, execute o seguinte cmdlet PowerShell:

```powershell
Update-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -LoadBalancerType 'DepthFirst' -MaxSessionLimit ###
```

>[!NOTE]
> O algoritmo de equilíbrio de primeira carga de profundidade distribui sessões a anfitriões de sessão com base no limite máximo de anfitrião da sessão ( `-MaxSessionLimit` ). O valor padrão deste parâmetro é `999999` , que é também o número mais alto possível para o qual pode definir esta variável. Este parâmetro é necessário quando se utiliza o algoritmo de equilíbrio de carga de profundidade primeiro. Para obter a melhor experiência possível do utilizador, certifique-se de alterar o parâmetro limite de anfitrião de sessão máxima para um número que melhor se adapte ao seu ambiente.

Para se certificar de que a definição foi atualizada, execute este cmdlet:

```powershell
Get-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> | format-list Name, LoadBalancerType, MaxSessionLimit

Name             : hostpoolname
LoadBalancerType : DepthFirst
MaxSessionLimit  : 6
```

## <a name="configure-load-balancing-with-the-azure-portal"></a>Configurar a carga equilibrando com o portal Azure

Também pode configurar o equilíbrio de carga com o portal Azure.

Para configurar o equilíbrio da carga:

1. Inscreva-se no portal Azure em https://portal.azure.com .
2. Procure e selecione **Windows Virtual Desktop** em Serviços.
3. Na página de Ambiente de Trabalho Virtual do Windows, **selecione host pools**.
4. Selecione o nome da piscina de anfitrião que pretende editar.
5. Selecione **Propriedades**.
6. Introduza o limite de **sessão Max** no campo e selecione o **algoritmo de equilíbrio de carga** que deseja para este pool do anfitrião no menu suspenso.
7. Selecione **Guardar**. Isto aplica as novas definições de equilíbrio de carga.