---
title: Guia de publicação da oferta de máquina virtual para o Azure Marketplace
description: Este artigo descreve os requisitos para publicar uma máquina virtual e uma avaliação gratuita de software a ser implantada no Marketplace.
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
author: ellacroi
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 07/09/2018
ms.author: ellacroi
ms.openlocfilehash: 0f2047501b6c57b2bb98ff7e3c56498417644324
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73818977"
---
# <a name="virtual-machine-offer-publishing-guide"></a>Guia de publicação da oferta de máquina virtual

As imagens de máquina virtual são uma das principais maneiras de publicar uma solução no Azure Marketplace. Use este guia para entender os requisitos para esta oferta. 

Essas são ofertas de transação que são implantadas e cobradas pelo Marketplace. O plano de ação que um usuário vê é "obter agora".

## <a name="free-trial"></a>Avaliação gratuita 

Você pode organizar os usuários para testar sua oferta acessando licenças de software de termo limitado ao usar o modelo de cobrança BYOL (traga sua própria licença). Abaixo estão os requisitos para implantar essa oferta. 

|Requisitos  |Detalhes  |
|---------|---------|
|Período de avaliação gratuita e experiência de avaliação     |   Seus clientes podem tentar seu aplicativo gratuitamente por um período limitado. Seus clientes têm a nota necessária para pagar quaisquer taxas de licença ou assinatura para sua oferta. Seus clientes não precisam pagar pelo produto ou serviço subjacente da Microsoft. Todas as opções de avaliação são implantadas em sua assinatura do Azure. Você tem controle exclusivo da otimização e do gerenciamento de custos. Você pode escolher uma avaliação gratuita ou demonstração interativa. Não importa o que você escolher, sua avaliação gratuita deve fornecer aos clientes um período predefinido de tempo para experimentar sua oferta sem custo adicional.|
|Solução facilmente configurável e pronta para uso    |  Seu aplicativo deve ser fácil e rápido de configurar e configurar.       |
|Disponibilidade/tempo de atividade    |    Seu aplicativo ou plataforma SaaS deve ter um tempo de atividade de pelo menos 99,9%.     |
|Azure Active Directory     |    Sua oferta deve permitir o Azure Active Directory (SSO (logon único) federado (SSO) do Azure AD (Azure Active Directory) com consentimento habilitado.     |

## <a name="test-drive"></a>Versão de Teste

Você implanta uma ou mais máquinas virtuais por meio de aplicativos de infraestrutura como serviço (IaaS) ou software como serviço (SaaS). Um benefício da opção de publicação test drive é o provisionamento automatizado de uma máquina virtual ou uma solução completa, por um tour guiado hospedado pelo parceiro. Uma test drive fornece uma avaliação sem custo adicional para o cliente. Seu cliente não precisa ser um cliente existente do Azure para se envolver com a experiência de avaliação. 

Entre em contato conosco em [amp-testdrive](mailto:amp-testdrive@microsoft.com) para começar. 

|Requisitos  |Detalhes |
|---------|---------|
| Você tem um aplicativo do Marketplace   |    Uma ou mais máquinas virtuais por meio de IaaS ou SaaS.      |

## <a name="interactive-demo"></a>Interactive Demo

Você fornece uma experiência guiada de sua solução para seus clientes usando uma demonstração interativa. O benefício da opção de publicação de demonstração interativa é que você fornece uma experiência de avaliação sem provisionamento complicado de sua solução complexa. 

## <a name="virtual-machine-offer"></a>Oferta de máquina virtual

Use o tipo de oferta de máquina virtual ao implantar um dispositivo virtual na assinatura associada ao cliente. As VMs são totalmente habilitadas para o comércio usando modelos de licenciamento pago pelo uso ou BYOL (traga sua própria licença). A Microsoft hospeda a transação de comércio e cobra seu cliente em seu nome. Você Obtém o benefício de usar a relação de pagamento preferencial entre o cliente e a Microsoft, incluindo qualquer contrato Enterprise.

> [!NOTE]
> Neste momento, os compromissos monetários associados a um Enterprise Agreement podem ser usados em relação ao uso do Azure de sua VM, mas não contra suas taxas de licenciamento de software.  
> 
> [!NOTE]
> Você pode restringir a descoberta e a implantação de sua VM a um conjunto específico de clientes publicando a imagem e os preços como uma oferta privada. As ofertas privadas desbloqueiam a capacidade de criar ofertas exclusivas para seus clientes mais próximos e oferecer softwares e termos personalizados. Os termos personalizados permitem que você realce uma variedade de cenários, incluindo acordos orientados a campo com preços e termos especializados, bem como acesso antecipado a software de versão limitada. As ofertas privadas permitem que você forneça preços ou produtos específicos a um conjunto limitado de clientes criando uma nova SKU com esses detalhes.  
> *   Para obter mais informações sobre ofertas privadas, visite a página ofertas particulares no Azure Marketplace localizada em [Azure.Microsoft.com/blog/Private-offers-on-Azure-Marketplace](https://azure.microsoft.com/blog/private-offers-on-azure-marketplace).  

| Requisito | Detalhes |  
|:--- |:--- | 
| Cobrança e medição | Sua VM deve dar suporte a cobrança mensal BYOL ou paga conforme o uso. |  
| VHD (disco rígido virtual) compatível com o Azure | As VMs devem ser criadas no Windows ou no Linux. <ul> <li>Para obter mais informações sobre como criar um VHD do Linux, consulte [distribuições do Linux endossadas no Azure](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros).</li> <li>Para obter mais informações sobre como criar um VHD do Windows, consulte [criar um VHD compatível com o Azure](./cloud-partner-portal/virtual-machine/cpp-create-vhd.md).</li> </ul> |  

>[!Note]
>A aceitação do canal de parceiros do CSP (provedores de soluções na nuvem) já está disponível.  Consulte os [provedores de soluções de nuvem](./cloud-solution-providers.md) para obter mais informações sobre como comercializar sua oferta por meio dos canais de parceiros do Microsoft CSP.

## <a name="next-steps"></a>Passos seguintes

Se você ainda não fez isso, 

- [Registre-se](https://azuremarketplace.microsoft.com/sell) no Marketplace.

Se você estiver registrado e estiver criando uma nova oferta ou trabalhando em uma existente,

- [Faça logon no portal do Cloud Partner](https://cloudpartner.azure.com) para criar ou concluir sua oferta.
- Consulte [oferta da máquina virtual](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-virtual-machine-offer) para obter mais informações.
