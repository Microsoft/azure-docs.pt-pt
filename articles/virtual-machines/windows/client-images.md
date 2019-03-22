---
title: Utilizar imagens do cliente Windows no Azure | Documentos da Microsoft
description: Como utilizar os benefícios da assinatura do Visual Studio para implementar o Windows 7, Windows 8 ou Windows 10 no Azure para cenários de desenvolvimento/teste
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
ms.assetid: 91c3880a-cede-44f1-ae25-f8f9f5b6eaa4
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 12/15/2017
ms.author: cynthn
ms.openlocfilehash: f791b17f2729af3efd2dff5d7884a168f8377154
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57999739"
---
# <a name="use-windows-client-in-azure-for-devtest-scenarios"></a>Utilizar o cliente do Windows no Azure para cenários de desenvolvimento/teste
Pode usar o Windows 7, Windows 8, ou Windows 10 Enterprise (x64) no Azure para cenários de desenvolvimento/teste fornecida que tem uma subscrição do Visual Studio (anteriormente conhecido como MSDN) adequada. Este artigo descreve os requisitos de elegibilidade para executar o Windows 7, Windows 8.1, Windows 10 Enterprise no Azure e utilize as seguintes imagens de galeria do Azure.

![Detalhes da imagem do portal do Azure](./media/client-images/windows-client-msdn-images.png) 

> [!NOTE]
> Para Windows 10 Pro e N do Windows 10 Pro imagem na galeria do Azure, consulte [como implementar o Windows 10 no Azure com direitos de alojamento multi-inquilino](windows-desktop-multitenant-hosting-deployment.md)
>![os detalhes da imagem Pro no portal do Azure](./media/client-images/windows-client-pro-images.png) 
>

## <a name="subscription-eligibility"></a>Elegibilidade de subscrição
Subscritores ativos do Visual Studio (pessoas que tem adquirido uma licença de subscrição do Visual Studio) podem utilizar o cliente do Windows para fins de testes e de desenvolvimento. Cliente do Windows pode ser utilizado em seu próprio hardware e as máquinas virtuais do Azure em execução em qualquer tipo de subscrição do Azure. Cliente do Windows não pode ser implementado ou utilizada no Azure para utilização em produção normal ou utilizado por pessoas que não são os subscritores ativos do Visual Studio.

Para sua comodidade, determinadas imagens do Windows 10 estão disponíveis da galeria do Azure dentro [ofertas elegível dev/test](#eligible-offers). Subscritores do Visual Studio dentro de qualquer tipo de oferta também podem [adequadamente preparar e crie](prepare-for-upload-vhd-image.md) uma imagem do Windows 7, Windows 8 ou Windows 10 de 64 bits e, em seguida [carregar para o Azure](upload-generalized-managed.md). A utilização continua a ser limitada para desenvolvimento/teste por subscritores ativos do Visual Studio.

## <a name="eligible-offers"></a>Ofertas elegíveis
A tabela seguinte fornece detalhes sobre a oferta IDs que são elegíveis para implementar o Windows 10 através da galeria do Azure. As imagens do Windows 10 apenas estão visíveis para as seguintes ofertas. Os subscritores do Visual Studio que precisam de executar o cliente do Windows num tipo de oferta diferente, exigem que [adequadamente preparar e crie](prepare-for-upload-vhd-image.md) uma imagem do Windows 7, Windows 8 ou Windows 10 de 64 bits e [, em seguida, carregar para o Azure](upload-generalized-managed.md).

| Nome da Oferta | Número da oferta | Imagens do cliente disponíveis |
|:--- |:---:|:---:|
| [Pay as you go programador/teste](https://azure.microsoft.com/offers/ms-azr-0023p/) |0023P |Windows 10 |
| [Subscritores do Visual Studio Enterprise (MPN)](https://azure.microsoft.com/offers/ms-azr-0029p/) |0029P |Windows 10 |
| [Subscritores do Visual Studio Professional](https://azure.microsoft.com/offers/ms-azr-0059p/) |0059P |Windows 10 |
| [Subscritores do Visual Studio Test Professional](https://azure.microsoft.com/offers/ms-azr-0060p/) |0060P |Windows 10 |
| [Visual Studio Premium com MSDN (benefício)](https://azure.microsoft.com/offers/ms-azr-0061p/) |0061P |Windows 10 |
| [Subscritores do Visual Studio Enterprise](https://azure.microsoft.com/offers/ms-azr-0063p/) |0063P |Windows 10 |
| [Subscritores do Visual Studio Enterprise (BizSpark)](https://azure.microsoft.com/offers/ms-azr-0064p/) |0064P |Windows 10 |
| [Enterprise programador/teste](https://azure.microsoft.com/offers/ms-azr-0148p/) |0148P |Windows 10 |

## <a name="check-your-azure-subscription"></a>Verifique a sua subscrição do Azure
Se não souber o ID de oferta, pode obtê-lo através do portal do Azure de uma das seguintes duas formas:  

- Sobre o *subscrições* janela:

  ![Detalhes de ID de oferta do portal do Azure](./media/client-images/offer-id-azure-portal.png) 

- Em alternativa, clique em **faturação** e, em seguida, clique em seu ID de subscrição. A oferta ID é apresentado na *faturação* janela.

Também pode ver o ID de oferta do [separador "Subscrições"](https://account.windowsazure.com/Subscriptions) do portal de contas do Azure:

![Detalhes de ID de oferta do portal de contas do Azure](./media/client-images/offer-id-azure-account-portal.png) 

## <a name="next-steps"></a>Passos Seguintes
Agora, pode implementar as VMs com o [PowerShell](quick-create-powershell.md), [modelos do Resource Manager](ps-template.md), ou [Visual Studio](../../vs-azure-tools-resource-groups-deployment-projects-create-deploy.md).

