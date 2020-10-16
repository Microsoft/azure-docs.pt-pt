---
title: Use imagens de clientes do Windows em Azure
description: Como utilizar os benefícios de subscrição do Visual Studio para implementar o Windows 7, Windows 8 ou Windows 10 em Azure para cenários dev/teste
author: cynthn
ms.subservice: imaging
ms.service: virtual-machines-windows
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 12/15/2017
ms.author: cynthn
ms.openlocfilehash: 26bfd9a65cd3e6d36b8190e87d382543cc7f0f60
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87292053"
---
# <a name="use-windows-client-in-azure-for-devtest-scenarios"></a>Utilizar cliente Windows no Azure para cenários de desenvolvimento/teste
Pode utilizar o Windows 7, Windows 8 ou Windows 10 Enterprise (x64) em Azure para cenários dev/teste, desde que tenha uma subscrição adequada do Visual Studio (anteriormente MSDN). Este artigo descreve os requisitos de elegibilidade para executar o Windows 7, Windows 8.1, Windows 10 Enterprise em Azure e a utilização das seguintes imagens da Galeria Azure.

![Detalhes de imagem do portal Azure](./media/client-images/windows-client-msdn-images.png) 

> [!NOTE]
> Para imagens do Windows 10 Pro e Windows 10 Pro N na Galeria Azure, consulte como implementar o [Windows 10 no Azure com detalhes multitenant hosting Rights](windows-desktop-multitenant-hosting-deployment.md)Pro Image a partir do portal 
> ![ Azure](./media/client-images/windows-client-pro-images.png) 
>

## <a name="subscription-eligibility"></a>Elegibilidade da assinatura
Os subscritores do Ative Visual Studio (pessoas que adquiriram uma licença de subscrição do Visual Studio) podem usar o cliente Windows para fins de desenvolvimento e teste. O cliente Windows pode ser utilizado no seu próprio hardware e máquinas virtuais Azure em qualquer tipo de subscrição do Azure. O cliente windows não pode ser implantado ou utilizado no Azure para uso normal de produção, ou utilizado por pessoas que não são assinantes do Visual Studio ativos.

Para sua conveniência, algumas imagens do Windows 10 estão disponíveis na Galeria Azure dentro [de ofertas elegíveis dev/teste](#eligible-offers). Os subscritores do Visual Studio dentro de qualquer tipo de oferta também podem [preparar e criar adequadamente](prepare-for-upload-vhd-image.md) uma imagem de 64 bits do Windows 7, Windows 8 ou Windows 10 e, em seguida, [fazer o upload para o Azure](upload-generalized-managed.md). A utilização permanece limitada a dev/teste por subscritores ativos do Visual Studio.

## <a name="eligible-offers"></a>Ofertas elegíveis
A tabela seguinte detalha os IDs de oferta que são elegíveis para implementar o Windows 10 através da Galeria Azure. As imagens do Windows 10 só são visíveis para as seguintes ofertas. Os subscritores do Visual Studio que precisam de executar o cliente Do Windows num tipo diferente de oferta requerem que [você prepare e crie adequadamente](prepare-for-upload-vhd-image.md) uma imagem de 64 bits do Windows 7, Windows 8 ou Windows 10 e, em [seguida, faça o upload para o Azure.](upload-generalized-managed.md)

| Nome da Oferta | Número de oferta | Imagens de clientes disponíveis |
|:--- |:---:|:---:|
| [Pay As You Go Dev/Test](https://azure.microsoft.com/offers/ms-azr-0023p/) |0023P |Windows 10 |
| [Subscritores do Visual Studio Enterprise (MPN)](https://azure.microsoft.com/offers/ms-azr-0029p/) |0029P |Windows 10 |
| [Subscritores visual Studio Professional](https://azure.microsoft.com/offers/ms-azr-0059p/) |0059P |Windows 10 |
| [Subscritores do Visual Studio Test Professional](https://azure.microsoft.com/offers/ms-azr-0060p/) |0060P |Windows 10 |
| [Visual Studio Premium com MSDN (benefício)](https://azure.microsoft.com/offers/ms-azr-0061p/) |0061P |Windows 10 |
| [Subscritores do Visual Studio Enterprise](https://azure.microsoft.com/offers/ms-azr-0063p/) |0063P |Windows 10 |
| [Subscritores do Visual Studio Enterprise (BizSpark)](https://azure.microsoft.com/offers/ms-azr-0064p/) |0064P |Windows 10 |
| [Enterprise Dev/Test](https://azure.microsoft.com/offers/ms-azr-0148p/) |0148P |Windows 10 |

## <a name="check-your-azure-subscription"></a>Verifique a sua assinatura Azure
Se não conhece o seu ID de oferta, pode obtê-lo através do portal Azure de uma destas duas formas:  

- Na janela *de Assinaturas:*

  ![Oferecer detalhes de ID do portal Azure](./media/client-images/offer-id-azure-portal.png) 

- Ou, clique em **Billing** e, em seguida, clique no seu ID de subscrição. A identificação da oferta aparece na janela de *faturação.*

Também pode ver o ID da oferta a partir do [separador 'Subscrições'](https://account.windowsazure.com/Subscriptions) do portal Da Conta Azure:

![Oferecer detalhes de ID do portal Azure Account](./media/client-images/offer-id-azure-account-portal.png) 

## <a name="next-steps"></a>Passos seguintes
Agora pode implementar os seus VMs utilizando [modelos](ps-template.md) [PowerShell,](quick-create-powershell.md)Resource Manager ou [Visual Studio](../../azure-resource-manager/templates/create-visual-studio-deployment-project.md).
