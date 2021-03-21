---
title: Crie uma oferta de máquina virtual no Azure Marketplace.
description: Saiba como criar uma oferta de máquinas virtuais no mercado comercial da Microsoft.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: mingshen-ms
ms.author: mingshen
ms.date: 03/10/2021
ms.openlocfilehash: ffc09daa15e742ca2b5b8a2fa9323e33fe317c60
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "103200397"
---
# <a name="how-to-create-a-virtual-machine-offer-on-azure-marketplace"></a>Como criar uma oferta de máquina virtual no Azure Marketplace

Este artigo descreve como criar uma oferta de máquina virtual Azure para [o Azure Marketplace.](https://azuremarketplace.microsoft.com/) Aborda máquinas virtuais baseadas no Windows e linux que contêm um sistema operativo, um disco rígido virtual (VHD) e até 16 discos de dados.

Antes de começar, [crie uma conta de marketplace comercial no Partner Center.](partner-center-portal/create-account.md) Certifique-se de que a sua conta está inscrita no programa de marketplace comercial.

## <a name="before-you-begin"></a>Antes de começar

Se ainda não o fez, reveja [a oferta de uma máquina virtual](marketplace-virtual-machines.md). Irá explicar os requisitos técnicos da sua máquina virtual e listará as informações e os ativos de que necessita quando criar a sua oferta.

## <a name="create-a-new-offer"></a>Criar uma nova oferta

1. Inscreva-se no [Partner Center](https://partner.microsoft.com/dashboard/home).
2. No painel esquerdo, selecione **Commercial Marketplace**  >  **Overview**.
3. Na página **'Vista Geral',** selecione **+ Nova oferta**  >  **Azure Virtual Machine**.

    ![Screenshot mostrando as opções do menu do painel esquerdo e o botão "Nova oferta".](./media/create-vm/new-offer-azure-virtual-machine.png)

> [!NOTE]
> Após a publicação da sua oferta, quaisquer edições que faça no Partner Center só aparecem no Azure Marketplace depois de republicar a oferta. Certifique-se de que vai sempre reeditar uma oferta depois de fazer alterações.

Introduza um **ID de oferta**. Este é um identificador único para cada oferta na sua conta.

- Este ID é visível para os clientes no endereço web para a oferta do Azure Marketplace e na Azure PowerShell e no Azure CLI, se aplicável.
- Utilize apenas letras minúsculas e números. O ID pode incluir hífens e sublinhados, mas sem espaços, e está limitado a 50 caracteres. Por exemplo, se introduzir **a oferta de teste-1,** o endereço web da oferta será `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1` .
- O ID da Oferta não pode ser alterado depois de selecionar **Criar**.

Insira **um pseudónimo de Oferta.** O pseudónimo da oferta é o nome que é usado para a oferta no Partner Center.

- Este nome não é usado no Azure Marketplace. É diferente do nome da oferta e outros valores que são mostrados aos clientes.

Selecione **Criar** para gerar a oferta e continuar. O Partner Center abre a página **de configuração da Oferta.**

## <a name="enable-a-test-drive-optional"></a>Ativar uma unidade de teste (opcional)

Um test drive é uma ótima maneira de mostrar a sua oferta a potenciais clientes, dando-lhes acesso a um ambiente pré-configurado por um número fixo de horas. Oferecer uma unidade de teste resulta numa taxa de conversão aumentada e gera cabos altamente qualificados. Para saber mais sobre test drives, veja [o que é um test drive?](./what-is-test-drive.md)

> [!TIP]
> Um test drive é diferente de um teste gratuito. Pode oferecer um test drive, teste gratuito, ou ambos. Ambos fornecem aos clientes a sua solução para um período de tempo fixo. Mas, um test drive também inclui uma visita prática e auto-guiada às principais características e benefícios do seu produto sendo demonstrados num cenário de implementação real.

Para ativar uma unidade de teste, selecione a caixa **de verificação de acionamento de teste.** Configurará o test drive mais tarde. Com a unidade de teste, é necessária configurar um CRM (ver secção seguinte).

## <a name="configure-customer-leads-management"></a>Configure a gestão de lideranças de clientes

Quando publicar uma oferta ao mercado comercial com o Partner Center, conecte-a ao seu sistema de Gestão de Relacionamento com o Cliente (CRM). Isto permite-lhe receber informações de contacto do cliente assim que alguém manifestar interesse ou utilizar o seu produto. É necessária uma ligação a um CRM se pretender ativar uma unidade de teste (ver a secção anterior). Caso contrário, a ligação a um CRM é opcional.

1. Sob **os comandos do Cliente,** selecione o link **'Ligar'.**
1. Na caixa de diálogo **de detalhes de Ligação,** selecione um destino de chumbo.
1. Complete os campos que aparecem. Para etapas detalhadas, consulte os seguintes artigos:

   - [Configure a sua oferta de envio leva à mesa Azure](./partner-center-portal/commercial-marketplace-lead-management-instructions-azure-table.md#configure-your-offer-to-send-leads-to-the-azure-table)
   - [Configure a sua oferta de envio de leads para Dynamics 365 Customer Engagement](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md#configure-your-offer-to-send-leads-to-dynamics-365-customer-engagement) (anteriormente Dynamics CRM Online)
   - [Configure a sua oferta para enviar leva ao ponto final HTTPS](./partner-center-portal/commercial-marketplace-lead-management-instructions-https.md#configure-your-offer-to-send-leads-to-the-https-endpoint)
   - [Configure a sua oferta de envio leva ao Marketo](./partner-center-portal/commercial-marketplace-lead-management-instructions-marketo.md#configure-your-offer-to-send-leads-to-marketo)
   - [Configure a sua oferta de envio leva à Salesforce](./partner-center-portal/commercial-marketplace-lead-management-instructions-salesforce.md#configure-your-offer-to-send-leads-to-salesforce)

1. Para validar a configuração fornecida, selecione o link **Validate.**
1. Selecione **Ligar**.

**Selecione Guardar** o rascunho antes de continuar para o separador seguinte no menu de navegação à esquerda, **Propriedades**.

## <a name="next-steps"></a>Passos seguintes

- [Como configurar a máquina virtual oferece propriedades](azure-vm-create-properties.md)
- [Melhores práticas de listagem de ofertas](gtm-offer-listing-best-practices.md)