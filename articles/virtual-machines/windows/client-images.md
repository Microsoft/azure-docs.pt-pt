---
title: Utilize imagens de clientes do Windows em Azure
description: Como utilizar os benefícios de subscrição do Estúdio Visual para implementar o Windows 7, Windows 8 ou Windows 10 em Azure para cenários de dev/teste
author: cynthn
ms.subservice: imaging
ms.service: virtual-machines-windows
ms.topic: article
ms.workload: infrastructure-services
ms.date: 12/15/2017
ms.author: cynthn
ms.openlocfilehash: 51e4862fe55428f112841ef7176cdb916653de0b
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2020
ms.locfileid: "82083279"
---
# <a name="use-windows-client-in-azure-for-devtest-scenarios"></a>Utilizar cliente Windows no Azure para cenários de desenvolvimento/teste
Pode utilizar o Windows 7, Windows 8 ou Windows 10 Enterprise (x64) em Azure para cenários de dev/teste, desde que tenha uma subscrição apropriada do Estúdio Visual (anteriormente MSDN). Este artigo descreve os requisitos de elegibilidade para executar o Windows 7, Windows 8.1, Windows 10 Enterprise em Azure e usar as seguintes imagens da Azure Gallery.

![Detalhes da imagem do portal Azure](./media/client-images/windows-client-msdn-images.png) 

> [!NOTE]
> Para o Windows 10 Pro e windows 10 Pro N imagem na Azure Gallery, consulte como implementar o Windows 10 em Azure com dados de [anfitriões multiarrendatários](windows-desktop-multitenant-hosting-deployment.md)
>![Pro Image do portal Azure](./media/client-images/windows-client-pro-images.png) 
>

## <a name="subscription-eligibility"></a>Elegibilidade por subscrição
Os subscritores do Ative Visual Studio (pessoas que adquiriram uma licença de subscrição do Estúdio Visual) podem usar o cliente do Windows para fins de desenvolvimento e teste. O cliente Windows pode ser utilizado no seu próprio hardware e máquinas virtuais Azure em qualquer tipo de subscrição do Azure. O cliente Windows não pode ser implantado ou utilizado no Azure para uso normal de produção, ou utilizado por pessoas que não sejam subscritores ativos do Visual Studio.

Para sua conveniência, algumas imagens do Windows 10 estão disponíveis na Galeria Azure dentro de [ofertas elegíveis de v/teste](#eligible-offers). Os subscritores do Visual Studio dentro de qualquer tipo de oferta também podem [preparar e criar](prepare-for-upload-vhd-image.md) uma imagem do Windows 7, Windows 8 ou Windows 10 de 64 bits e depois fazer o upload para o [Azure.](upload-generalized-managed.md) A utilização continua limitada a dev/teste por subscritores ativos do Visual Studio.

## <a name="eligible-offers"></a>Ofertas elegíveis
A tabela seguinte detalha as IDs de oferta que são elegíveis para implementar o Windows 10 através da Galeria Azure. As imagens do Windows 10 só são visíveis para as seguintes ofertas. Os subscritores do Visual Studio que precisam de executar o cliente windows num tipo de oferta diferente exigem que [você prepare e crie](prepare-for-upload-vhd-image.md) uma imagem de 64 bits Windows 7, Windows 8 ou Windows 10 [e, em seguida, faça upload para Azure](upload-generalized-managed.md).

| Nome da Oferta | Número de oferta | Imagens de clientes disponíveis |
|:--- |:---:|:---:|
| [Pay As You Go Dev/Test](https://azure.microsoft.com/offers/ms-azr-0023p/) |0023P |Windows 10 |
| [Subscritores do Visual Studio Enterprise (MPN)](https://azure.microsoft.com/offers/ms-azr-0029p/) |0029P |Windows 10 |
| [Assinantes Do Estúdio Visual Profissional](https://azure.microsoft.com/offers/ms-azr-0059p/) |0059P |Windows 10 |
| [Subscritores do Visual Studio Test Professional](https://azure.microsoft.com/offers/ms-azr-0060p/) |0060P |Windows 10 |
| [Visual Studio Premium com MSDN (benefício)](https://azure.microsoft.com/offers/ms-azr-0061p/) |0061P |Windows 10 |
| [Subscritores do Visual Studio Enterprise](https://azure.microsoft.com/offers/ms-azr-0063p/) |0063P |Windows 10 |
| [Subscritores do Visual Studio Enterprise (BizSpark)](https://azure.microsoft.com/offers/ms-azr-0064p/) |0064P |Windows 10 |
| [Enterprise Dev/Test](https://azure.microsoft.com/offers/ms-azr-0148p/) |0148P |Windows 10 |

## <a name="check-your-azure-subscription"></a>Verifique a sua subscrição do Azure
Se não conhece a sua identificação de oferta, pode obtê-la através do portal Azure de uma destas duas formas:  

- Na janela *Assinaturas:*

  ![Oferecer detalhes de ID do portal Azure](./media/client-images/offer-id-azure-portal.png) 

- Ou, clique em **Faturar** e, em seguida, clique no seu ID de subscrição. A identificação da oferta aparece na janela de *faturação.*

Também pode visualizar o ID de oferta do [separador 'Subscrições'](https://account.windowsazure.com/Subscriptions) do portal da Conta Azure:

![Oferecer detalhes de ID do portal da Conta Azure](./media/client-images/offer-id-azure-account-portal.png) 

## <a name="next-steps"></a>Passos seguintes
Agora pode implementar os seus VMs utilizando [modelos](ps-template.md) [PowerShell,](quick-create-powershell.md)Resource Manager ou [Visual Studio](../../vs-azure-tools-resource-groups-deployment-projects-create-deploy.md).

