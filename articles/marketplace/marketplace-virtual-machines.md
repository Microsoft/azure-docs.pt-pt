---
title: Planeie uma oferta de máquina virtual - Mercado comercial da Microsoft
description: Este artigo descreve os requisitos para a publicação de uma oferta de máquina virtual ao Azure Marketplace.
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: iqshahmicrosoft
ms.author: iqshah
ms.date: 10/19/2020
ms.openlocfilehash: 6e391ca79c9dcf9ab533a7e67961c654b5f8630b
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/08/2021
ms.locfileid: "107103373"
---
# <a name="plan-a-virtual-machine-offer"></a>Planear uma oferta de máquina virtual

Este artigo explica as diferentes opções e requisitos para a publicação de uma oferta de máquina virtual (VM) ao mercado comercial. As ofertas de VM são ofertas transacionáveis implementadas e faturadas através do Azure Marketplace.

Antes de iniciar, [Crie uma conta de marketplace comercial no Partner Center](create-account.md) e certifique-se de que a sua conta está inscrita no programa de marketplace comercial.

### <a name="technical-fundamentals"></a>Fundamentos técnicos

O processo de conceção, construção e testes oferece tempo e requer experiência tanto na plataforma Azure como nas tecnologias usadas para construir a sua oferta. A sua equipa de engenharia deverá ter um conhecimento de trabalho das [Máquinas Virtuais Azure,](https://azure.microsoft.com/services/virtual-machines/) [Azure Storage](https://azure.microsoft.com/services/?filter=storage#storage)e [Azure Networking,](https://azure.microsoft.com/services/?filter=networking#networking)bem como proficiência com o [design e arquitetura das aplicações Azure.](https://azure.microsoft.com/solutions/architecture/) Consulte estes recursos técnicos adicionais: 

- Tutoriais
  - [VMs do Linux](../virtual-machines/linux/tutorial-manage-vm.md)
  - [VMs do Windows](../virtual-machines/windows/tutorial-manage-vm.md)

- Amostras
  - [Amostras de CLI Azure para LMs Linux](https://github.com/Azure-Samples/azure-cli-samples/tree/master/virtual-machine)
  - [Azure PowerShell para LMs Linux](https://github.com/Azure/azure-docs-powershell-samples/tree/master/virtual-machine)
  - [Amostras de CLI Azure para VMs do Windows](https://github.com/Azure-Samples/azure-cli-samples/tree/master/virtual-machine)
  - [Azure PowerShell para VMs windows](/previous-versions/azure/virtual-machines/scripts/virtual-machines-windows-powershell-sample-create-vm-quick)

## <a name="technical-requirements"></a>Requisitos técnicos

As ofertas de VM têm os seguintes requisitos técnicos:

- Tem de preparar um disco rígido virtual do sistema operativo (VHD). Os VHDs do disco de dados são opcionais. Isto é explicado em mais detalhes abaixo.
- O cliente pode cancelar a sua oferta a qualquer momento.
- Deve criar pelo menos um plano para a sua oferta. O seu plano tem um preço baseado na [opção de licenciamento](#licensing-options) que seleciona.
   > [!IMPORTANT]
   > Cada Imagem VM num plano deve ter o mesmo número de discos de dados.

Um VM contém dois componentes:

- **Funcionamento VHD** – Contém o sistema operativo e a solução que se implanta com a sua oferta. O processo de preparação do VHD difere consoante se trate de um VM baseado em Linux, Windows ou personalizado.
- **VHDs de disco de dados** (opcional) – Armazenamento dedicado e persistente para um VM. Não utilize o VHD do sistema operativo (por exemplo, o C: unidade) para armazenar informações persistentes. 
    - Pode incluir até 16 discos de dados.
    - Utilize um VHD por disco de dados, mesmo que o disco esteja em branco.

    > [!NOTE]
    > Independentemente do sistema operativo que utilize, adicione apenas o número mínimo de discos de dados necessários pela solução. Os clientes não podem remover discos que fazem parte de uma imagem no momento da implementação, mas podem sempre adicionar discos durante ou após a implementação.

Para obter instruções detalhadas sobre a preparação dos seus ativos técnicos, consulte [Criar uma máquina virtual utilizando uma base aprovada](azure-vm-create-using-approved-base.md) ou Criar uma máquina virtual utilizando a sua própria [imagem](azure-vm-create-using-own-image.md).

## <a name="preview-audience"></a>Audiência de pré-visualização

Um público de pré-visualização pode aceder à sua oferta de VM antes de ser publicado ao vivo no Azure Marketplace de forma a testar a funcionalidade de ponta a ponta antes de a publicar ao vivo. Na página de **audiência de pré-visualização,** pode definir um público de pré-visualização limitado. 

> [!NOTE]
> Um público de pré-visualização difere de um plano privado. Um plano privado é aquele que disponibiliza apenas para um público específico que escolher. Isto permite-lhe negociar um plano personalizado com clientes específicos. Para mais informações, consulte a secção seguinte: Planos.

Pode enviar convites para endereços de e-mail da Conta Microsoft (MSA) ou do Azure Ative Directory (Azure AD). Adicione até 10 endereços de e-mail manualmente ou importe até 20 com um ficheiro .csv. Se a sua oferta já estiver ao vivo, ainda pode definir um público de pré-visualização para testar quaisquer alterações ou atualizações da sua oferta.

## <a name="plans-and-pricing"></a>Planos e preços

As ofertas de VM requerem pelo menos um plano. Um plano define o âmbito e os limites da solução, e os preços associados. Pode criar vários planos para a sua oferta para dar aos seus clientes diferentes opções técnicas e de licenciamento, bem como testes gratuitos. Consulte [planos e preços para ofertas de mercado comercial](plans-pricing.md) para orientação geral sobre planos, incluindo modelos de preços, testes gratuitos e planos privados. 

Os VMs são totalmente habilitados para o comércio, utilizando modelos de licenciamento pay-as-you-go ou bring-your-own-license (BYOL). A Microsoft acolhe a transação de comércio e fatura o seu cliente em seu nome. Obtém o benefício de utilizar a relação de pagamento preferida entre o seu cliente e a Microsoft, incluindo quaisquer Acordos empresariais. Para obter mais informações, consulte [as capacidades de transação do mercado comercial.](./marketplace-commercial-transaction-capabilities-and-considerations.md)

> [!NOTE]
> O pré-pagamento Azure (anteriormente chamado de compromisso monetário) associado a um Acordo de Empresa pode ser usado contra o uso Azure do seu VM, mas não contra as suas taxas de licenciamento de software.

### <a name="licensing-options"></a>Opções de licenciamento

À medida que se prepara para publicar uma nova oferta de VM, tem de decidir qual a opção de licenciamento a escolher. Isto determinará as informações adicionais que precisará fornecer mais tarde, à medida que criar a sua oferta no Partner Center.

Estas são as opções de licenciamento disponíveis para as ofertas de VM:

| Opção de licenciamento | Processo de transação |
| --- | --- |
| Avaliação gratuita | Ofereça aos seus clientes um teste gratuito de um, três ou seis meses. |
| Unidade de teste | Esta opção permite que os seus clientes avaliem VMs sem custos adicionais para eles. Não precisam de ser um cliente Azure existente para se envolverem com a experiência do teste. Para mais detalhes, veja [o que é um test drive?](./what-is-test-drive.md) |
| BYOL | A opção Bring Your Own Licensing permite que os seus clientes levem licenças de software existentes para a Azure.\* |
| Baseada na utilização | Também conhecida como pay-as-you-go, esta opção permite que os seus clientes paguem por hora. |
| Demonstração interativa  | Dê aos seus clientes uma experiência guiada da sua solução utilizando uma demonstração interativa. O benefício é que você pode oferecer uma experiência experimental sem ter que fornecer uma configuração complicada da sua complexa solução. |
|

\* Como editor, apoia todos os aspetos da transação de licença de software, incluindo (mas não se limitando a) encomenda, realização, medição, faturação, faturação, pagamento e cobrança.

O exemplo a seguir mostra uma oferta de VM no Azure Marketplace que tem preços baseados na utilização.

:::image type="content" source="media/vm/sample-offer-screen.png" alt-text="Amostra VM lista de ofertas.":::

### <a name="private-plans"></a>Planos privados

Pode restringir a descoberta e implantação do seu VM a um conjunto específico de clientes, publicando a imagem e os preços como um plano privado. Os planos privados desbloqueiam a capacidade de criar ofertas exclusivas para os seus clientes mais próximos e oferecer software e termos personalizados. Os termos personalizados permitem-lhe destacar uma variedade de cenários, incluindo acordos liderados por campo com preços e termos especializados, bem como acesso precoce a software de lançamento limitado. Os planos privados permitem-lhe dar preços ou produtos específicos a um conjunto limitado de clientes.

Para mais informações, consulte [Planos e preços para ofertas de marketplace comercial](plans-pricing.md) e [ofertas privadas no mercado comercial da Microsoft.](private-offers.md)

## <a name="test-drive"></a>Unidade de teste

Pode optar por ativar um test drive para o seu VM. Os test drives dão aos clientes acesso a um ambiente pré-configurado por um número fixo de horas. Pode ativar as unidades de teste para qualquer opção de publicação, no entanto esta funcionalidade tem requisitos adicionais. Para saber mais sobre test drives, veja [o que é um test drive?](what-is-test-drive.md) Para obter informações sobre a configuração de diferentes tipos de test drives, consulte a [configuração técnica do test drive](test-drive-technical-configuration.md).

> [!TIP]
> Um test drive é diferente de um [teste gratuito.](plans-pricing.md#free-trials) Pode oferecer um test drive, teste gratuito, ou ambos. Ambos fornecem aos seus clientes a sua solução para um período de tempo fixo. Mas, um test drive também inclui uma visita prática e auto-guiada às principais características e benefícios do seu produto sendo demonstrados num cenário de implementação real.

## <a name="customer-leads"></a>Ligações ao cliente

Tem de ligar a sua oferta ao seu sistema de gestão de relacionamento com o cliente (CRM) para recolher informações do cliente. O cliente será solicitado permissão para partilhar as suas informações. Estes detalhes do cliente, juntamente com o nome da oferta, ID e loja online onde encontraram a sua oferta, serão enviados para o sistema CRM que configuraste. O mercado comercial suporta uma variedade de sistemas CRM, juntamente com a opção de usar uma tabela Azure ou configurar um ponto final HTTPS utilizando o Power Automate.

Pode adicionar ou modificar uma ligação CRM a qualquer momento durante ou após a criação da oferta. Para obter orientações detalhadas, consulte [os leads do Cliente da sua oferta de mercado comercial.](partner-center-portal/commercial-marketplace-get-customer-leads.md)

## <a name="legal-contracts"></a>Contratos legais

Para simplificar o processo de aquisição para clientes e reduzir a complexidade legal para os fornecedores de software, a Microsoft oferece um contrato padrão que pode usar para as suas ofertas no mercado comercial. Quando oferece o seu software ao abrigo do contrato padrão, os clientes só precisam de o ler e aceitar uma vez, e não tem de criar termos e condições personalizados.

Se optar por utilizar o contrato padrão, tem a opção de adicionar termos de emenda universal e até 10 alterações personalizadas ao contrato padrão. Também pode usar os seus próprios termos e condições em vez do contrato padrão. Você vai gerir estes detalhes na página **Propriedades.** Para obter informações detalhadas, consulte [o contrato Standard para o mercado comercial da Microsoft.](standard-contract.md)

> [!NOTE]
> Depois de publicar uma oferta utilizando o contrato padrão para o mercado comercial, não pode utilizar os seus próprios termos e condições personalizados. É um cenário "ou". Ou oferece a sua solução de acordo com o contrato padrão ou os seus próprios termos e condições. Se quiser modificar os termos do contrato padrão, pode fazê-lo através de Alterações Padrão contratuais.

## <a name="cloud-solution-providers"></a>Fornecedores de Soluções Cloud

Ao criar a sua oferta no Partner Center, verá o **separador Revenda através do separador CSPs.** Esta opção permite que os parceiros que fazem parte do programa Microsoft Cloud Solution Providers (CSP) revendem o seu VM como parte de uma oferta agregada. Todos os planos Bring-your-your-own-license (BYOL) são automaticamente optados pelo programa. Também pode optar pelos seus planos não BYOL. Consulte [o programa Cloud Solution Provider](cloud-solution-providers.md) para obter mais informações. 

> [!NOTE]
> O opt-in do canal parceiro Cloud Solution Provider (CSP) já está disponível. Para obter mais informações sobre o marketing da sua oferta através dos canais parceiros da Microsoft CSP, consulte [**os Fornecedores de Soluções Cloud.**](./cloud-solution-providers.md)

## <a name="next-steps"></a>Passos seguintes

- [Crie uma oferta de máquina virtual no Azure Marketplace](azure-vm-create.md)
- [Crie uma máquina virtual utilizando uma base aprovada](azure-vm-create-using-approved-base.md) ou crie uma máquina virtual utilizando a sua própria [imagem](azure-vm-create-using-own-image.md).
- [Melhores práticas de listagem de ofertas](gtm-offer-listing-best-practices.md)
