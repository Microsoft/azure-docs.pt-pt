---
title: Configuração do piloto da GPU da série N Azure para windows
description: Como configurar os controladores GPU da AMD para VMs da série N que executam o Windows Server ou o Windows em Azure
author: vikancha
manager: jkabat
ms.service: virtual-machines-windows
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 12/4/2019
ms.author: vikancha
ms.openlocfilehash: 63114bdf60c1feb2b6cb1092ef78397efdc5b666
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/22/2020
ms.locfileid: "81865762"
---
# <a name="install-amd-gpu-drivers-on-n-series-vms-running-windows"></a>Instale controladores GPU AMD em VMs da série N executando Windows

Para tirar partido das capacidades de GPU dos novos VMs da série Azure NVv4 que executam o Windows, os controladores GPU da AMD devem ser instalados. A extensão do condutor da AMD estará disponível nas próximas semanas. Este artigo fornece sistemas operativos suportados, controladores e passos de instalação e verificação manuais.

Para especificações básicas, capacidades de armazenamento e detalhes do disco, consulte os [tamanhos de VM do Windows GPU](sizes-gpu.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).



## <a name="supported-operating-systems-and-drivers"></a>Sistemas operativos e controladores suportados

| SO | Controlador |
| -------- |------------- |
| Windows 10 EVD - Construção 1903 <br/><br/>Windows 10 - Construção 1809<br/><br/>Windows Server 2016<br/><br/>Windows Server 2019 | [20.Q1.1](https://download.microsoft.com/download/3/8/9/3893407b-e8aa-4079-8592-735d7dd1c19a/Radeon-Pro-Software-for-Enterprise-GA.exe) (.exe) |


## <a name="driver-installation"></a>Instalação do condutor

1. Ligue-se por Remote Desktop a cada VM da série NVv4.

2. Se é um cliente de pré-visualização NVv4, por favor pare o VM e espere que este se mude para o estado Stop(Deallocated).

3. Por favor, inicie o VM e baixe o mais recente [Utilitário de Limpeza AMD](https://download.microsoft.com/download/4/f/1/4f19b714-9304-410f-9c64-826404e07857/AMDCleanupUtilityni.exe). Desinstale o condutor existente executando "amdcleanuputility-x64.exe". Não utilize qualquer utilitário de limpeza existente que tenha sido instalado com o controlador anterior.  

4. Faça o download e instale o mais recente controlador.

5. Reiniciar o VM.

## <a name="verify-driver-installation"></a>Verificar a instalação do condutor

Pode verificar a instalação do controlador no Gestor de Dispositivos. O exemplo seguinte mostra uma configuração bem sucedida do cartão Radeon Instinct MI25 num VM Azure NVv4.
<br />
![Propriedades do condutor da GPU](./media/n-series-amd-driver-setup/device-manager.png)

Pode utilizar o dxdiag para verificar as propriedades de exibição de GPU, incluindo a RAM de vídeo. O exemplo seguinte mostra uma partição de 1/2 do cartão Radeon Instinct MI25 num VM Azure NVv4.
<br />
![Propriedades do condutor da GPU](./media/n-series-amd-driver-setup/dxdiag-output.png)

Se estiver a executar o Windows 10 build 1903 ou superior, então o dxdiag não mostrará nenhuma informação no separador 'Display'. Por favor, utilize a opção "Guardar todas as informações" na parte inferior e o ficheiro de saída mostrará as informações relacionadas com a GPU AMD MI25.

![Propriedades do condutor da GPU](./media/n-series-amd-driver-setup/dxdiag-details.png)


