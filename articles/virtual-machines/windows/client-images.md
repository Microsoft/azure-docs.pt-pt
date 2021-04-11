---
title: Use imagens de clientes do Windows em Azure
description: Como utilizar os benefícios de subscrição do Visual Studio para implementar o Windows 7, Windows 8 ou Windows 10 em Azure para cenários dev/teste
author: mimckitt
ms.subservice: imaging
ms.service: virtual-machines
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 12/15/2017
ms.author: mimckitt
ms.openlocfilehash: 7879e355573d2b9952728828bd3780094a919fe3
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104869060"
---
# <a name="use-windows-client-in-azure-for-devtest-scenarios"></a>Utilizar cliente Windows no Azure para cenários de desenvolvimento/teste
Pode utilizar o Windows 7, Windows 8 ou Windows 10 Enterprise (x64) em Azure para cenários dev/teste, desde que tenha uma subscrição adequada do Visual Studio (anteriormente MSDN). 

Para executar o Windows 10 num ambiente de produção veja, [Como implementar o Windows 10 no Azure com direitos de hospedagem multitenant](windows-desktop-multitenant-hosting-deployment.md).


## <a name="subscription-eligibility"></a>Elegibilidade da assinatura
Os subscritores do Ative Visual Studio (pessoas que adquiriram uma licença de subscrição do Visual Studio) podem utilizar imagens de clientes do Windows para fins de desenvolvimento e teste. As imagens do cliente do Windows podem ser usadas no seu próprio hardware ou em máquinas virtuais Azure.

Algumas imagens do cliente do Windows estão disponíveis no Azure Marketplace. Os subscritores do Visual Studio dentro de qualquer tipo de oferta também podem [preparar e criar](prepare-for-upload-vhd-image.md) imagens de 64 bits do Windows 7, Windows 8 ou Windows 10 e depois fazer o upload para o [Azure](upload-generalized-managed.md).

## <a name="eligible-offers-and-client-images"></a>Ofertas elegíveis e imagens de clientes
A tabela seguinte detalha os IDs de oferta que são elegíveis para implementar imagens de clientes do Windows através do Azure Marketplace. As imagens do cliente windows só são visíveis às seguintes ofertas. 

> [!NOTE]
> As ofertas de imagem estão sob o **Cliente Windows** no Azure Marketplace. Utilize **o Cliente Windows** ao procurar imagens de clientes disponíveis para assinantes do Visual Studio. 

| Nome da Oferta | Número de oferta | Imagens de clientes disponíveis | 
|:--- |:---:|:---:|
| [Pay As You Go Dev/Test](https://azure.microsoft.com/offers/ms-azr-0023p/) |0023P | Windows 10 Enterprise N (x64) <br> Windows 8.1 Enterprise N (x64) <br> Windows 7 Enterprise N com SP1 (x64) |
| [Subscritores do Visual Studio Enterprise (MPN)](https://azure.microsoft.com/offers/ms-azr-0029p/) |0029P | Windows 10 Enterprise N (x64) <br> Windows 8.1 Enterprise N (x64) <br> Windows 7 Enterprise N com SP1 (x64) |
| [Subscritores visual Studio Professional](https://azure.microsoft.com/offers/ms-azr-0059p/) |0059P | Windows 10 Enterprise N (x64) <br> Windows 8.1 Enterprise N (x64) <br> Windows 7 Enterprise N com SP1 (x64) |
| [Subscritores do Visual Studio Test Professional](https://azure.microsoft.com/offers/ms-azr-0060p/) |0060P | Windows 10 Enterprise N (x64) <br> Windows 8.1 Enterprise N (x64) <br> Windows 7 Enterprise N com SP1 (x64) |
| [Visual Studio Premium com MSDN (benefício)](https://azure.microsoft.com/offers/ms-azr-0061p/) |0061P | Windows 10 Enterprise N (x64) <br> Windows 8.1 Enterprise N (x64) <br> Windows 7 Enterprise N com SP1 (x64) |
| [Subscritores do Visual Studio Enterprise](https://azure.microsoft.com/offers/ms-azr-0063p/) |0063P | Windows 10 Enterprise N (x64) <br> Windows 8.1 Enterprise N (x64) <br> Windows 7 Enterprise N com SP1 (x64) |
| [Subscritores do Visual Studio Enterprise (BizSpark)](https://azure.microsoft.com/offers/ms-azr-0064p/) |0064P | Windows 10 Enterprise N (x64) <br> Windows 8.1 Enterprise N (x64) <br> Windows 7 Enterprise N com SP1 (x64) |
| [Enterprise Dev/Test](https://azure.microsoft.com/offers/ms-azr-0148p/) |0148P | Windows 10 Enterprise N (x64) <br> Windows 8.1 Enterprise N (x64) <br> Windows 7 Enterprise N com SP1 (x64) |

## <a name="check-your-azure-subscription"></a>Verifique a sua assinatura Azure
Se não conhece o seu ID de oferta, pode obtê-lo através do portal Azure.  
- Na janela *de Subscrições:* ![ Ofereça detalhes de ID do portal Azure](./media/client-images/offer-id-azure-portal.png) 
- Ou, clique em **Billing** e, em seguida, clique no seu ID de subscrição. A identificação da oferta aparece na janela de *faturação.* 
- Também pode ver o ID da oferta a partir do [separador 'Subscrições'](https://account.windowsazure.com/Subscriptions) do portal Conta Azure: ![ Oferecer detalhes de ID a partir do portal da Conta Azure](./media/client-images/offer-id-azure-account-portal.png) 

## <a name="next-steps"></a>Passos seguintes
Agora pode implementar os seus VMs utilizando [modelos](ps-template.md) [PowerShell,](quick-create-powershell.md)Resource Manager ou [Visual Studio](../../azure-resource-manager/templates/create-visual-studio-deployment-project.md).
