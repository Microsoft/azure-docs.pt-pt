---
title: Usar imagens de cliente do Windows no Azure
description: Como usar os benefícios da assinatura do Visual Studio para implantar o Windows 7, Windows 8 ou Windows 10 no Azure para cenários de desenvolvimento/teste
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
ms.assetid: 91c3880a-cede-44f1-ae25-f8f9f5b6eaa4
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 12/15/2017
ms.author: cynthn
ms.openlocfilehash: 812e6d251943d4418666f221ad8b5d2b6e501736
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74039506"
---
# <a name="use-windows-client-in-azure-for-devtest-scenarios"></a>Usar o cliente do Windows no Azure para cenários de desenvolvimento/teste
Você pode usar o Windows 7, o Windows 8 ou o Windows 10 Enterprise (x64) no Azure para cenários de desenvolvimento/teste, desde que você tenha uma assinatura apropriada do Visual Studio (anteriormente conhecido como MSDN). Este artigo descreve os requisitos de qualificação para a execução do Windows 7, Windows 8.1, Windows 10 Enterprise no Azure e o uso das seguintes imagens da galeria do Azure.

![Detalhes da imagem do portal do Azure](./media/client-images/windows-client-msdn-images.png) 

> [!NOTE]
> Para a imagem do Windows 10 pro e do Windows 10 pro N na galeria do Azure, consulte [como implantar o Windows 10 no Azure com direitos de hospedagem multilocatário](windows-desktop-multitenant-hosting-deployment.md)
>detalhes da imagem do ![Pro da portal do Azure](./media/client-images/windows-client-pro-images.png) 
>

## <a name="subscription-eligibility"></a>Qualificação da assinatura
Assinantes ativos do Visual Studio (pessoas que adquiriram uma licença de assinatura do Visual Studio) podem usar o cliente Windows para fins de desenvolvimento e teste. O cliente do Windows pode ser usado em seu próprio hardware e máquinas virtuais do Azure em execução em qualquer tipo de assinatura do Azure. O cliente do Windows não pode ser implantado ou usado no Azure para uso normal da produção ou usado por pessoas que não são assinantes ativos do Visual Studio.

Para sua conveniência, determinadas imagens do Windows 10 estão disponíveis na galeria do Azure em [ofertas de desenvolvimento/teste qualificadas](#eligible-offers). Os assinantes do Visual Studio em qualquer tipo de oferta também podem [preparar e criar adequadamente](prepare-for-upload-vhd-image.md) uma imagem de 64 bits do Windows 7, do Windows 8 ou do Windows 10 e, em seguida, [carregar para o Azure](upload-generalized-managed.md). O uso permanece limitado a desenvolvimento/teste por assinantes ativos do Visual Studio.

## <a name="eligible-offers"></a>Ofertas qualificadas
A tabela a seguir detalha as IDs de oferta qualificadas para implantar o Windows 10 por meio da galeria do Azure. As imagens do Windows 10 são visíveis apenas para as ofertas a seguir. Os assinantes do Visual Studio que precisam executar o cliente do Windows em um tipo de oferta diferente exigem que você [prepare e crie adequadamente](prepare-for-upload-vhd-image.md) uma imagem de 64 bits do Windows 7, do Windows 8 ou do Windows 10 e [, em seguida, carregue no Azure](upload-generalized-managed.md).

| Nome da Oferta | Número da oferta | Imagens de cliente disponíveis |
|:--- |:---:|:---:|
| [Pay As You Go Dev/Test](https://azure.microsoft.com/offers/ms-azr-0023p/) |0023P |Windows 10 |
| [Subscritores do Visual Studio Enterprise (MPN)](https://azure.microsoft.com/offers/ms-azr-0029p/) |0029P |Windows 10 |
| [Assinantes do Visual Studio Professional](https://azure.microsoft.com/offers/ms-azr-0059p/) |0059P |Windows 10 |
| [Assinantes do Visual Studio Test Professional](https://azure.microsoft.com/offers/ms-azr-0060p/) |0060P |Windows 10 |
| [Visual Studio Premium com MSDN (benefício)](https://azure.microsoft.com/offers/ms-azr-0061p/) |0061P |Windows 10 |
| [Assinantes do Visual Studio Enterprise](https://azure.microsoft.com/offers/ms-azr-0063p/) |0063P |Windows 10 |
| [Assinantes do Visual Studio Enterprise (BizSpark)](https://azure.microsoft.com/offers/ms-azr-0064p/) |0064P |Windows 10 |
| [Enterprise Dev/Test](https://azure.microsoft.com/offers/ms-azr-0148p/) |0148P |Windows 10 |

## <a name="check-your-azure-subscription"></a>Verifique sua assinatura do Azure
Se você não souber sua ID de oferta, poderá obtê-la por meio do portal do Azure de uma destas duas maneiras:  

- Na janela *assinaturas* :

  ![Detalhes da ID da oferta do portal do Azure](./media/client-images/offer-id-azure-portal.png) 

- Ou clique em **cobrança** e, em seguida, clique em sua ID de assinatura. A ID da oferta aparece na janela de *cobrança* .

Você também pode exibir a ID da oferta na [guia ' assinaturas '](https://account.windowsazure.com/Subscriptions) do portal da conta do Azure:

![Detalhes da ID da oferta do portal de conta do Azure](./media/client-images/offer-id-azure-account-portal.png) 

## <a name="next-steps"></a>Passos seguintes
Agora você pode implantar suas VMs usando o [PowerShell](quick-create-powershell.md), [modelos do Resource Manager](ps-template.md)ou o [Visual Studio](../../vs-azure-tools-resource-groups-deployment-projects-create-deploy.md).

