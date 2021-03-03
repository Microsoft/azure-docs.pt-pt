---
title: Configuração do controlador GPU da série Azure N para windows
description: Como configurar controladores GPU AMD para VMs da série N que executam o Windows Server ou Windows em Azure
author: vikancha-MSFT
manager: jkabat
ms.service: virtual-machines-windows
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 12/4/2019
ms.author: vikancha
ms.openlocfilehash: 78971a92f1815236bd4b360e8df01b02b4c40626
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2021
ms.locfileid: "101677230"
---
# <a name="install-amd-gpu-drivers-on-n-series-vms-running-windows"></a>Instale controladores GPU da AMD em VMs da série N que executam o Windows

Para tirar partido das capacidades da GPU dos novos VMs da série Azure NVv4 que executam o Windows, os controladores da GPU da AMD devem ser instalados. A [extensão do condutor da GPU da AMD](../extensions/hpccompute-amd-gpu-windows.md) instala os controladores DA GPU amd num VM da série NVv4. Instale ou gerencie a extensão utilizando o portal Azure ou ferramentas como os modelos Azure PowerShell ou Azure Resource Manager. Consulte a documentação da [extensão do condutor da GPU amd](../extensions/hpccompute-amd-gpu-windows.md) para sistemas operativos suportados e etapas de implementação.

Se optar por instalar manualmente os controladores da AMD GPU, este artigo fornece sistemas operativos suportados, controladores e etapas de instalação e verificação.

Apenas os controladores da GPU publicados pela Microsoft são suportados em VMs NVv4. NÃO instale controladores GPU de qualquer outra fonte.

Para especificações básicas, capacidades de armazenamento e detalhes do disco, consulte [os tamanhos VM do GPU Windows](../sizes-gpu.md?toc=/azure/virtual-machines/windows/toc.json).



## <a name="supported-operating-systems-and-drivers"></a>Sistemas operativos e controladores suportados

| SO | Controlador |
| -------- |------------- |
| Windows 10 Enterprise multi-sessão - Build 1909 <br/><br/>Windows 10 - Construção 1909<br/><br/>Windows Server 2016<br/><br/>Windows Server 2019 | [20.Q4](https://download.microsoft.com/download/f/1/6/f16e6275-a718-40cd-a366-9382739ebd39/AMD-Azure-NVv4-Driver-20Q4.exee) (.exe) |

 > [!NOTE]
   >  Se utilizar a construção 1903/1909, poderá ter de atualizar a seguinte política de grupo para obter um desempenho ótimo. Estas alterações não são necessárias para qualquer outra construção do Windows.
   >  
   >  [Configuração de computador->Políticas >Definições do Windows->Modelos administrativos >componentes do Windows >Remote Desktop Services->Remote Desktop Session Host->Remote Session Environment], defina a política [use o controlador de gráficas WDDM para ligações remotas de ambiente de trabalho] para desativar.
   >  


## <a name="driver-installation"></a>Instalação do condutor

1. Conecte-se por Remote Desktop a cada VM da série NVv4.

2. Se precisar de desinstalar a versão anterior do controlador, descarregue o utilitário de limpeza AMD [aqui](https://download.microsoft.com/download/4/f/1/4f19b714-9304-410f-9c64-826404e07857/AMDCleanupUtilityni.exe) Não utilize o utilitário que vem com a versão anterior do controlador.

3. Faça o download e instale o mais recente controlador.

4. Reinicie o VM.

## <a name="verify-driver-installation"></a>Verificar a instalação do condutor

Pode verificar a instalação do controlador no Gestor de Dispositivos. O exemplo a seguir mostra uma configuração bem sucedida da carta Radeon Instinct MI25 num VM Azure NVv4.
<br />

![Screenshot que mostra uma configuração bem sucedida do cartão Radeon Instinct MI25 num VM Azure NVv4.](./media/n-series-amd-driver-setup/device-manager.png)

Pode utilizar o dxdiag para verificar as propriedades do visor da GPU, incluindo a RAM de vídeo. O exemplo a seguir mostra uma partição de 1/2 do cartão Radeon Instinct MI25 num VM Azure NVv4.
<br />
![Screenshot que mostra uma partição de 1/2 do cartão Radeon Instinct MI25 num Azure NVv4 VM.](./media/n-series-amd-driver-setup/dxdiag-output-new.png)

Se estiver a executar o Windows 10 construir 1903 ou mais, então o dxdiag não apresentará nenhuma informação no separador 'Display'. Por favor, utilize a opção "Save All Information" na parte inferior e o ficheiro de saída mostrará as informações relacionadas com a GPU AMD MI25.

![Propriedades do condutor da GPU](./media/n-series-amd-driver-setup/dxdiag-details.png)
