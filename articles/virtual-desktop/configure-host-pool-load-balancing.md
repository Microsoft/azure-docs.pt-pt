---
title: Configurar o método de balanceamento de carga do Windows Virtual Desktop Preview - Azure
description: Como configurar o método de balanceamento de carga para um ambiente de área de Trabalho Virtual do Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: helohr
ms.openlocfilehash: 0c4702dada17e759d89c33be99b3155f4b15ad9e
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60328889"
---
# <a name="configure-the-windows-virtual-desktop-preview-load-balancing-method"></a>Configurar o método de balanceamento de carga de pré-visualização de ambiente de Trabalho Virtual do Windows

Configurar o método de balanceamento de carga para um conjunto de anfitrião permite-lhe ajustar o ambiente de pré-visualização de ambiente de Trabalho Virtual do Windows para melhor se adequar às suas necessidades.

>[!NOTE]
> Isto não é aplicável a um conjunto de anfitrião de ambiente de trabalho persistente porque os utilizadores tenham sempre um mapeamento 1:1 para um anfitrião de sessões de dentro do conjunto de anfitrião.

## <a name="configure-breadth-first-load-balancing"></a>Configurar o balanceamento de carga da amplitude em primeiro lugar

Balanceamento de carga da amplitude em primeiro lugar é a configuração predefinida para novos conjuntos de anfitrião não persistentes. Balanceamento de carga da amplitude em primeiro lugar, distribui novas sessões de utilizador em todos os hosts de sessão disponíveis no agrupamento de anfitrião. Ao configurar o balanceamento de carga da amplitude em primeiro lugar, pode de definir um limite de duração máxima da sessão por anfitrião de sessões no agrupamento de anfitrião.

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
