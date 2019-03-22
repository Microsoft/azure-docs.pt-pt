---
title: Configurar o ambiente de Trabalho Virtual do Windows balanceamento de carga método (pré-visualização) - Azure
description: Como configurar o método de balanceamento de carga para um ambiente de área de Trabalho Virtual do Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: helohr
ms.openlocfilehash: 10a1066b85b16749fe95e373e696d486b0e7bafa
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/21/2019
ms.locfileid: "58318348"
---
# <a name="configure-the-windows-virtual-desktop-load-balancing-method"></a>Configurar o método de balanceamento de carga de área de Trabalho Virtual do Windows

Configurar o método de balanceamento de carga para um conjunto de anfitrião permite-lhe ajustar o ambiente de área de Trabalho Virtual do Windows (pré-visualização) para melhor se adequar às suas necessidades.

>[!NOTE]
> Isto não é aplicável a um conjunto de anfitrião de ambiente de trabalho persistente porque os utilizadores tenham sempre um mapeamento 1:1 para um anfitrião de sessões de dentro do conjunto de anfitrião.

## <a name="configure-breadth-first-load-balancing"></a>Configurar o balanceamento de carga da amplitude em primeiro lugar

Balanceamento de carga da amplitude em primeiro lugar é a configuração predefinida para novos conjuntos de anfitrião não persistentes. Amplitude em primeiro lugar, o balanceamento de carga distribui o novas sessões de utilizador por todos os hosts de sessão disponíveis no agrupamento de anfitrião. Ao configurar o balanceamento de carga da amplitude em primeiro lugar, pode de definir um limite de duração máxima da sessão por anfitrião de sessões no agrupamento de anfitrião.

Primeiro, [transferir e importar o módulo do Windows PowerShell de ambiente de Trabalho Virtual](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview) a utilizar na sua sessão do PowerShell, se ainda não o fez.

Para configurar um conjunto de anfitrião para executar sem ajustar o limite máximo da sessão de balanceamento de carga da amplitude em primeiro lugar, execute o seguinte cmdlet do PowerShell:

```powershell
Set-RdsHostPool <tenantname> <hostpoolname> -BreadthFirstLoadBalancer
```

Para configurar um conjunto de anfitrião para efetuar o balanceamento de carga da amplitude em primeiro lugar e a utilizar um limite máximo da sessão novo, execute o seguinte cmdlet do PowerShell:

```powershell
Set-RdsHostPool <tenantname> <hostpoolname> -BreadthFirstLoadBalancer -MaxSessionLimit ###
```

## <a name="configure-depth-first-load-balancing"></a>Configurar o balanceamento de carga de profundidade de primeiro

Balanceamento de carga de profundidade de primeiro distribui novas sessões de utilizador para um host de sessão disponíveis com o maior número de ligações, mas não atingiu o respetivo limiar de limite de duração máxima da sessão. Quando configurar o balanceamento de carga de profundidade em primeiro lugar, **tem** definir um limite de duração máxima da sessão por anfitrião de sessões no agrupamento de anfitrião.

Para configurar um conjunto de anfitrião para efetuar o balanceamento de carga de profundidade em primeiro lugar, execute o seguinte cmdlet do PowerShell:

```powershell
Set-RdsHostPool <tenantname> <hostpoolname> -DepthFirstLoadBalancer -MaxSessionLimit ###
```
