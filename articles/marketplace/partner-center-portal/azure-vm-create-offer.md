---
title: Crie uma oferta de máquina virtual Azure no Azure Marketplace
description: Saiba como publicar uma oferta de máquina virtual ao Azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: emuench
ms.author: mingshen
ms.date: 08/07/2020
ms.openlocfilehash: 46749015d5141053eaea3f2f251bbba7d9455481
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91709021"
---
# <a name="create-an-azure-virtual-machine-offer-on-azure-marketplace"></a>Crie uma oferta de máquina virtual Azure no Azure Marketplace

Este artigo descreve como criar e publicar uma oferta de máquina virtual Azure ao [Azure Marketplace.](https://azuremarketplace.microsoft.com/) Aborda máquinas virtuais baseadas no Windows e linux que contêm um sistema operativo, um disco rígido virtual (VHD) e até 16 discos de dados. 

Antes de começar, [Crie uma conta de marketplace comercial no Partner Center.](create-account.md) Certifique-se de que a sua conta está inscrita no programa de marketplace comercial.

## <a name="introduction"></a>Introdução

### <a name="the-benefits-of-publishing-to-azure-marketplace"></a>Os benefícios da publicação no Azure Marketplace

Ao publicar as suas ofertas no Azure Marketplace, pode:

- Promova a sua empresa com a ajuda da marca Microsoft.
- Alcance mais de 100 milhões de utilizadores microsoft 365 e Dynamics 365 e mais de 200.000 organizações.
- Obtenha pistas de alta qualidade destes mercados.
- Obtenha os seus serviços promovidos pelas equipas de vendas e televendas da Microsoft.

### <a name="before-you-begin"></a>Antes de começar

Se ainda não o fez, reveja o [guia de publicação da oferta de máquinas Virtuais](../marketplace-virtual-machines.md) e este material Azure Virtual Machine:

- Guias quickstart
  - [Modelos de arranque rápido Azure](https://azure.microsoft.com/resources/templates/)
  - [Modelos de arranque rápido GitHub Azure](https://github.com/azure/azure-quickstart-templates)
- Tutoriais
  - [VMs do Linux](../../virtual-machines/linux/tutorial-manage-vm.md)
  - [VMs do Windows](../../virtual-machines/windows/tutorial-manage-vm.md)
- Amostras
  - [Amostras de CLI Azure para LMs Linux](../../virtual-machines/linux/cli-samples.md)
  - [Azure PowerShell para LMs Linux](../../virtual-machines/linux/powershell-samples.md)
  - [Amostras de CLI Azure para VMs do Windows](../../virtual-machines/windows/cli-samples.md)
  - [Azure PowerShell para VMs windows](../../virtual-machines/scripts/virtual-machines-windows-powershell-sample-create-vm-quick.md)

### <a name="fundamentals-in-technical-knowledge"></a>Fundamentos do conhecimento técnico

O processo de conceção, construção e testes oferece tempo e requer experiência tanto na plataforma Azure como nas tecnologias que são usadas para construir a sua oferta.

A sua equipa de engenharia deve ter um conhecimento básico e de trabalho das seguintes tecnologias da Microsoft:

- [Serviços do Azure](https://azure.microsoft.com/services/)
- [Design e arquitetura de aplicações Azure](https://azure.microsoft.com/solutions/architecture/)
- [Azure Virtual Machines](https://azure.microsoft.com/services/virtual-machines/), [Azure Storage](https://azure.microsoft.com/services/?filter=storage#storage)e [Azure Networking](https://azure.microsoft.com/services/?filter=networking#networking)

## <a name="create-a-new-offer"></a>Criar uma nova oferta

1. Inscreva-se no [Partner Center](https://partner.microsoft.com/dashboard/home).
2. No painel esquerdo, selecione **Commercial Marketplace**  >  **Overview**.
3. Na página **'Vista Geral',** selecione **Nova oferta**  >  **Azure Virtual Machine**.

    ![Screenshot mostrando as opções do menu do painel esquerdo e o botão "Nova oferta".](./media/new-offer-azure-virtual-machine.png)

> [!NOTE]
> Após a publicação da sua oferta, quaisquer edições que faça no Partner Center só aparecem no Azure Marketplace depois de republicar a oferta. Certifique-se de que vai sempre reeditar uma oferta depois de fazer alterações.

## <a name="new-offer"></a>Nova oferta

Introduza um **ID de oferta**. Este é um identificador único para cada oferta na sua conta.

- Este ID é visível para os clientes no endereço web para a oferta do Azure Marketplace e na Azure PowerShell e no Azure CLI, se aplicável.
- Utilize apenas letras minúsculas e números. O ID pode incluir hífens e sublinhados, mas sem espaços, e está limitado a 50 caracteres. Por exemplo, se introduzir **a oferta de teste-1,** o endereço web da oferta será `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1` .
- O ID da Oferta não pode ser alterado depois de selecionar **Criar**.

Insira **um pseudónimo de Oferta.** O pseudónimo da oferta é o nome que é usado para a oferta no Partner Center.

- Este nome não é usado no Azure Marketplace. É diferente do nome da oferta e outros valores que são mostrados aos clientes.

Selecione **Criar** para gerar a oferta e continuar.

## <a name="offer-setup"></a>Configuração de oferta

### <a name="test-drive"></a>Versão de Teste

Um *Test Drive* é uma ótima maneira de mostrar a sua oferta a potenciais clientes. Dá-lhes a opção de "experimentar antes de comprar", o que pode ajudar a aumentar as suas conversões e gerar leads altamente qualificados. Para mais informações, veja [o que é um Test Drive?](../what-is-test-drive.md)

Para ativar uma Unidade de Teste durante um período de tempo fixo, selecione a caixa de **verificação de acionamento de teste.** Para remover o Test Drive da sua oferta, limpe a caixa de verificação.

Recursos adicionais de Test Drive:

- [Melhores práticas técnicas](https://github.com/Azure/AzureTestDrive/wiki/Test-Drive-Best-Practices)
- [Melhores práticas de marketing](../what-is-test-drive.md)
- [Descarregue a visão geral do Test Drive](https://assetsprod.microsoft.com/mpn/azure-marketplace-appsource-test-drives.pdf) Ficheiro PDF (certifique-se de que o seu bloqueador pop-up está desligado).

### <a name="customer-leads"></a>Ligações ao cliente

Ao publicar a sua oferta no mercado comercial com o Partner Center, conecte-a ao seu sistema de Gestão de Relacionamento com o Cliente (CRM). Isto permite-lhe receber informações de contacto do cliente assim que alguém manifestar interesse ou utilizar o seu produto. É necessária uma ligação a um CRM se pretender ativar uma Unidade de Teste (ver a secção anterior). Caso contrário, a ligação a um CRM é opcional.

1. Selecione um destino de oportunidades potenciais para onde quer que enviemos as oportunidades potenciais de clientes. O Partner Center suporta os seguintes sistemas crm:
    - [Dinâmica 365](commercial-marketplace-lead-management-instructions-dynamics.md) para envolvimento com o cliente
    - [Marketo](commercial-marketplace-lead-management-instructions-marketo.md)
    - [Salesforce](commercial-marketplace-lead-management-instructions-salesforce.md)

    > [!NOTE]
    > Se o seu sistema CRM não estiver listado aqui, utilize [o armazenamento da Tabela Azure](commercial-marketplace-lead-management-instructions-azure-table.md) ou um ponto final [HTTPS](commercial-marketplace-lead-management-instructions-https.md) para armazenar os dados de chumbo do seu cliente. Em seguida, exporte os dados para o seu sistema crm.

1. Ligue a sua oferta ao destino principal quando estiver a publicar no Partner Center.
1. Confirme se a ligação ao destino de chumbo está configurada corretamente. Depois de publicá-lo no Partner Center, a Microsoft valida a ligação e envia-lhe uma pista de teste. Enquanto está a pré-visualizar a oferta antes de entrar em direto, também pode testar a sua ligação de chumbo tentando implementar a oferta no ambiente de pré-visualização.
1. Certifique-se de que a ligação ao destino principal permanece atualizada para que não perca nenhuma pista.

1. **Selecione Guardar o projeto** antes de continuar.

## <a name="properties"></a>Propriedades

Na página **Propriedades,** define as categorias que são usadas para agrupar a sua oferta no Azure Marketplace, a sua versão de candidatura e os contratos legais que suportam a sua oferta.

### <a name="category"></a>Categoria

Selecione categorias e subcategorias para colocar a sua oferta nas áreas de pesquisa de mercado apropriadas. Não se esqueça de descrever como a sua oferta suporta estas categorias na descrição da oferta. Selecione:

- Pelo menos uma e até duas categorias, incluindo uma categoria primária e secundária (opcional).
- Até duas subcategorias para cada categoria primária e/ou secundária. Se não for aplicável nenhuma subcategoria à sua oferta, selecione **Não aplicável**.

Consulte a lista completa de categorias e subcategorias na [Listagem de Ofertas Boas Práticas.](../gtm-offer-listing-best-practices.md) As ofertas de máquinas virtuais aparecem sempre na categoria **Compute** no Azure Marketplace.

### <a name="legal"></a>Legal

Deve fornecer condições e condições de oferta aos seus clientes. Tem duas opções:

- **Use os seus próprios termos e condições**

   Para fornecer os seus próprios termos e condições personalizados, insira até 10.000 caracteres de texto na caixa **de Termos e Condições.** Se necessitar de uma descrição mais longa, insira um único endereço web para os seus termos e condições. Será exibido aos clientes como um link ativo.

   Os seus clientes devem aceitar estes termos antes de poderem experimentar a sua oferta.

- **Utilize o Contrato Padrão para o mercado comercial da Microsoft**

   Para simplificar o processo de aquisição para clientes e reduzir a complexidade legal dos fornecedores de software, a Microsoft oferece um Contrato Standard para o mercado comercial. Quando oferece o seu software ao abrigo do Contrato Padrão, os clientes precisam de o ler e aceitar apenas uma vez, e não tem de criar termos e condições personalizados.

   Utilize o Contrato Padrão selecionando **o Use the Standard Contract para a** caixa de verificação do mercado comercial da Microsoft e, em seguida, na janela pop-up, selecione **Accept** (pode ter de rolar para o visualizar).

   ![Screenshot mostrando o painel Legal no Partner Center com a caixa de verificação "Use o Contrato Padrão para o mercado comercial da Microsoft".](media/use-standard-contract.png)

  > [!NOTE]
  > Depois de publicar uma oferta utilizando o Contrato Padrão para o mercado comercial, não pode usar os seus próprios termos e condições personalizados. Pode oferecer uma solução nos termos e condições do Contrato Padrão ou dos seus próprios termos e condições.

  Para mais informações, consulte [o Standard Contract para o mercado comercial da Microsoft.](../standard-contract.md) Descarregue o ficheiro PDF [Standard Contract](https://go.microsoft.com/fwlink/?linkid=2041178) (certifique-se de que o seu bloqueador pop-up está desligado).

  **Alterações padrão do contrato**

  As alterações padrão do Contrato permitem-lhe selecionar os termos standard Contract para simplicidade e criar os termos para o seu produto ou negócio. Os clientes só precisam de rever as alterações ao contrato se já tiverem revisto e aceite o Contrato Padrão da Microsoft. Existem dois tipos de alterações:

  * **Alterações universais**: Estas alterações são aplicadas universalmente ao Contrato-Tipo para todos os clientes. São mostrados a todos os clientes da oferta no fluxo de compra. Os clientes devem aceitar os termos do Contrato Padrão e as alterações antes de poderem utilizar a sua oferta. Pode fornecer uma única emenda universal por oferta. Pode introduzir um número ilimitado de caracteres nesta caixa. Estes termos são apresentados aos clientes no AppSource, Azure Marketplace e/ou no portal Azure durante o fluxo de descoberta e compra.

  * **Alterações personalizadas**: Estas alterações especiais ao Contrato-Tipo são dirigidas a clientes específicos através de IDs de inquilinos Azure. Pode escolher o inquilino que quer escolher. Apenas os clientes do arrendatário são apresentados com os termos de alteração personalizados no fluxo de compra da oferta. Os clientes devem aceitar os termos do Contrato Padrão e as alterações antes de poderem utilizar a sua oferta.

    1. Comece por selecionar **Adicionar os termos de emenda personalizada (Max 10)**. Pode fornecer até dez termos de emenda personalizados por oferta. Faça o seguinte:

       a. Insira os seus termos de emenda na caixa de **termos de emenda personalizada.** Pode introduzir um número ilimitado de caracteres. Apenas os clientes dos IDs do inquilino que especifique para estes termos personalizados os verão no fluxo de compra da oferta no portal Azure.

       b. (Obrigatório) Fornecer **IDs de inquilino.** Cada emenda personalizada pode ser direcionada até 20 IDs de inquilino. Se adicionar uma emenda personalizada, deve fornecer pelo menos uma identificação de inquilino, que identifica o seu cliente em Azure. O seu cliente pode encontrá-lo em Azure selecionando **Azure Ative Directory**  >  **Properties**. O valor de ID do diretório é o ID do inquilino (por exemplo, 50c464d3-4930-494c-963c-1e951d15360e). Também pode encontrar a identificação do inquilino do seu cliente através do endereço web do seu nome de domínio no [What is my Microsoft Azure and Microsoft 365 Tenant ID?](https://www.whatismytenantid.com/)

       c. (Opcional) Forneça uma **descrição** amigável para o ID do inquilino, que o ajude a identificar o cliente que está a visar com a emenda.

        > [!NOTE]
        > Estes dois tipos de alterações são emparelhados entre si. Os clientes que são visados com alterações personalizadas também receberão as alterações universais ao Contrato Padrão durante a compra.

    1. **Selecione Guardar o projeto** antes de continuar.

## <a name="offer-listing"></a>Listagem de ofertas

Na página de **listagem da Oferta,** define os detalhes da oferta, tais como nome de oferta, descrição, links e contactos.

> [!NOTE]
> A sua oferta de listagem de conteúdos, como a descrição, documentos, imagens e termos de uso, não tem de estar em inglês, desde que a descrição da oferta comece com a frase: "Esta aplicação está disponível apenas \<non-English language> em." Também pode fornecer um URL para ligar a um site que oferece conteúdo num idioma diferente daquele que é usado no conteúdo da listagem de ofertas.

### <a name="marketplace-details"></a>Detalhes do mercado

#### <a name="name"></a>Nome

O nome que aqui entra é mostrado aos clientes como o título da sua listagem de oferta. Este campo é preenchido automaticamente com o nome que inseriu na caixa **de pseudónimos Oferta** quando criou a oferta. Pode alterar este nome posteriormente. O nome:

- Pode ser marcado. Pode incluir símbolos de marca registada e direitos autorais.
- Não pode conter mais de 50 caracteres.
- Não pode incluir emojis.

#### <a name="search-results-summary"></a>Resumo dos resultados da pesquisa

Forneça uma breve descrição da sua oferta, a ser exibida nos resultados da pesquisa do Azure Marketplace. Pode conter até 100 caracteres.

#### <a name="long-summary"></a>Resumo longo

Forneça uma descrição mais longa da sua oferta, a ser exibida nos resultados da pesquisa do Azure Marketplace. Pode conter até 256 caracteres.

#### <a name="description"></a>Descrição

[!INCLUDE [Long description-1](./includes/long-description-1.md)]

[!INCLUDE [Long description-2](./includes/long-description-2.md)]

[!INCLUDE [Long description-3](./includes/long-description-3.md)]

#### <a name="privacy-policy-link"></a>Ligação de política de privacidade

Insira o endereço web (URL) da política de privacidade da sua organização. Certifique-se de que a sua oferta está em conformidade com as leis e regulamentos de privacidade. Também deve publicar uma política de privacidade válida no seu website.

### <a name="useful-links"></a>Ligações úteis

Forneça documentos online suplementares sobre a sua oferta. Para adicionar um link, **selecione Adicionar um link**e, em seguida, completar os seguintes campos:

- **Nome**: Os clientes verão o nome na página de detalhes.
- **Link (URL)**: Introduza um link que permite aos clientes visualizar o seu documento online.

### <a name="customer-support-links"></a>Links de apoio ao cliente

Forneça o site de suporte onde os clientes podem chegar à sua equipa de apoio.

- Website de suporte Azure Global
- Site de apoio do Governo de Azure

### <a name="partner-support-contact"></a>Contacto de apoio ao parceiro

Forneça informações de contacto para os parceiros da Microsoft utilizarem quando os seus clientes abrirem um bilhete de suporte. Esta informação não está listada no Azure Marketplace.

- Nome
- E-mail
- Telefone

### <a name="engineering-contact"></a>Contacto de engenharia

Forneça informações de contacto para que a Microsoft possa utilizar quando houver problemas com a sua oferta, incluindo problemas com a certificação. Esta informação não está listada no Azure Marketplace.

- Nome
- E-mail
- Telefone

### <a name="azure-marketplace-media"></a>Meios de comunicação Azure Marketplace

Forneça logotipos e imagens para utilizar com a sua oferta. Todas as imagens devem estar em formato PNG. Imagens desfocadas farão com que a sua submissão seja rejeitada.

[!INCLUDE [logo tips](../includes/graphics-suggestions.md)]

>[!NOTE]
>Se tiver um problema de upload de ficheiros, certifique-se de que a sua rede local não bloqueia o https://upload.xboxlive.com serviço utilizado pelo Partner Center.

#### <a name="azure-marketplace-logos"></a>Logotipos do Azure Marketplace

Forneça um ficheiro PNG para o logotipo de tamanho **grande.** O Partner Center utilizará isto para criar um logótipo **Pequeno** e **Médio.** Pode substituir opcionalmente estas imagens por diferentes imagens mais tarde.

- **Grande** (de 216 x 216 a 350 x 350 px, necessário)
- **Médio** (90 x 90 px, opcional)
- **Pequeno** (48 x 48 px, opcional)

Estes logótipos são utilizados em diferentes locais da listagem:

[!INCLUDE [logos-azure-marketplace-only](../includes/logos-azure-marketplace-only.md)]

[!INCLUDE [Logo tips](../includes/graphics-suggestions.md)]

#### <a name="screenshots"></a>Capturas de ecrã

Adicione cinco imagens que mostram como a sua oferta funciona. Cada screenshot deve ter 1280 &times; 720 pixels de tamanho e em formato PNG. Cada imagem deve incluir uma legenda.

#### <a name="videos"></a>Vídeos

Adicione até cinco vídeos que demonstram a sua oferta. Os vídeos devem ser hospedados num serviço de vídeo externo. Insira o nome de cada vídeo, endereço web e uma imagem PNG de miniatura do vídeo em 1280 &times; 720 pixels.

Para obter recursos adicionais de listagem de mercado, consulte [as melhores práticas para anúncios de oferta de mercado.](../gtm-offer-listing-best-practices.md)

**Selecione Guardar o projeto** antes de continuar.

## <a name="preview"></a>Pré-visualizar

Selecione o **separador Pré-visualização** e, em seguida, selecione um **Audience de pré-visualização** limitado para validar a sua oferta antes de publicá-la ao vivo para o público mais amplo do mercado comercial.

> [!IMPORTANT]
> Depois de verificar a sua oferta no painel **de pré-visualização,** selecione **Go live** para publicar a sua oferta para o público do mercado comercial.

O seu público de pré-visualização é identificado por GUIDs de assinatura Azure, juntamente com uma descrição opcional para cada um. Nenhum destes campos pode ser visto pelos clientes. Pode encontrar o seu ID de subscrição Azure na página **de Subscrições** no portal Azure.

Adicione pelo menos um ID de subscrição Azure, individualmente (até 10 IDs) ou carregando um ficheiro CSV (até 100 IDs). Ao adicionar estes IDs de subscrição, define quem pode pré-visualizar a sua oferta antes de ser publicada ao vivo. Se a sua oferta já estiver ao vivo, ainda pode definir um público de pré-visualização para testar alterações ou atualizações da sua oferta.

> [!NOTE]
> Um público de pré-visualização difere de uma audiência privada. Um público de pré-visualização pode aceder à sua oferta _antes_ de ser publicada em direto no Azure Marketplace. O público de pré-visualização pode ver e validar todos os planos, incluindo aqueles que estarão disponíveis apenas para um público privado após a sua oferta ser totalmente publicada no Azure Marketplace. Um público privado (definido no painel **de preços e disponibilidade** do plano) tem acesso exclusivo a um determinado plano.

**Selecione Guarde o projeto** antes de passar para a secção seguinte.

## <a name="plan-overview"></a>Visão geral do plano

Você pode fornecer uma variedade de opções de plano dentro da mesma oferta no Partner Center. Uma oferta requer pelo menos um plano (anteriormente chamado de SKU), que pode variar por rentabilização do público, região de Azure, recursos ou imagens VM.

Pode criar até 100 planos para cada oferta: até 45 destes podem ser privados. Saiba mais sobre [planos privados em ofertas privadas no mercado comercial da Microsoft.](../private-offers.md)

Depois de criar os seus planos, selecione o separador **'visão geral' do Plano** para visualizar:

- Nomes do plano
- Modelos de licença
- Público (público ou privado)
- Estado atual da publicação
- Ações disponíveis

As ações que estão disponíveis no **painel de visão geral** do Plano variam consoante o estado atual do seu plano.

- Se o estado do plano for um rascunho, **selecione Eliminar rascunho**.
- Se o estado do plano for publicado ao vivo, selecione **Stop sell plan** ou Sync private **audience**.

### <a name="create-a-new-plan"></a>Criar um novo plano

**Selecione Criar um novo plano** no topo. Aparece a caixa de diálogo do **novo plano.**

Na caixa **de Identificação** do Plano, crie um ID de plano único para cada plano nesta oferta. Este ID será visível para os clientes no endereço web do produto. Utilize apenas letras e números minúsculos, traços ou sublinhados, e um máximo de 50 caracteres.

> [!NOTE]
> O ID do plano não pode ser alterado depois de selecionar **Criar**.

Na caixa de nomes do **Plano,** insira um nome para este plano. Os clientes vêem este nome quando estão a decidir qual o plano a selecionar dentro da sua oferta. Crie um nome único que aponte claramente as diferenças entre os planos. Por exemplo, pode introduzir **o Windows Server** com planos *Pay-as-you-go*, *BYOL,* *Advanced*e *Enterprise.*

Selecione **Criar**.

### <a name="plan-setup"></a>Configuração do plano

Desista a configuração de alto nível para o tipo de plano, especifique se reutiliza uma configuração técnica de outro plano e identifique as regiões de Azure onde o plano deve estar disponível. As suas seleções aqui determinam quais os campos exibidos em outros painéis para o mesmo plano.

#### <a name="reuse-a-technical-configuration"></a>Reutilizar uma configuração técnica

Se tiver mais do que um plano do mesmo tipo e as embalagens forem idênticas entre eles, pode selecionar **Este plano reutiliza a configuração técnica de outro plano**. Esta opção permite-lhe selecionar um dos outros planos do mesmo tipo para esta oferta e permite-lhe reutilizar a sua configuração técnica.

> [!NOTE]
> Ao reutilizar a configuração técnica de outro plano, todo o separador de **configuração técnica** desaparece deste plano. Os detalhes de configuração técnica do outro plano, incluindo quaisquer atualizações que fizer no futuro, também serão utilizados para este plano. Esta definição não pode ser alterada após a publicação do plano.

#### <a name="azure-regions"></a>Regiões do Azure

O seu plano deve ser disponibilizado em pelo menos uma região de Azure.

Selecione a opção **Azure Global** para disponibilizar o seu plano aos clientes de todas as regiões da Azure Global que tenham integração comercial no mercado. Para mais informações, consulte [a disponibilidade geográfica e o suporte à moeda.](../marketplace-geo-availability-currencies.md)

Selecione a opção **do Governo Azure** para disponibilizar o seu plano na região do [Governo Azure.](../../azure-government/documentation-government-welcome.md) Esta região fornece acesso controlado a clientes de entidades federais, estaduais, locais ou tribais dos EUA, bem como a parceiros elegíveis para os servir. Você, como editora, é responsável por quaisquer controlos de conformidade, medidas de segurança e boas práticas. O Governo Azure utiliza datacenters e redes fisicamente isoladas (localizadas apenas nos EUA).

Antes de publicar para o [Governo Azure,](../../azure-government/documentation-government-manage-marketplace-partners.md)teste e valide o seu plano no ambiente, porque certos pontos finais podem diferir. Para configurar e testar o seu plano, solicite uma conta de teste na página de teste do [Governo Microsoft Azure.](https://azure.microsoft.com/global-infrastructure/government/request/)

> [!NOTE]
> Depois de o seu plano ser publicado e disponível numa região específica de Azure, não pode remover essa região.

#### <a name="azure-government-certifications"></a>Certificações do Governo de Azure

Esta opção só é visível se selecionar o **Governo Azure** como região do Azure na secção anterior.

Os serviços do Governo Azure tratam dados que estão sujeitos a certos regulamentos e requisitos governamentais. Por exemplo, FedRAMP, NIST 800.171 (DIB), ITAR, IRS 1075, DoD L4 e CJIS. Para consciencializar as suas certificações para estes programas, pode fornecer até 100 links que os descrevem. Estes podem ser links para a sua listagem diretamente no programa ou links para descrições da sua conformidade com eles nos seus próprios websites. Estas ligações são visíveis apenas para clientes do Governo Azure.

**Selecione Guardar o projeto** antes de continuar.

### <a name="plan-listing"></a>Listagem de planos

Nesta secção, configurar os detalhes da listagem do plano. Este painel apresenta informações específicas, que podem diferir de outros planos na mesma oferta.

#### <a name="plan-name"></a>Nome do plano

Este campo é automaticamente preenchido com o nome que deu ao seu plano quando o criou. Este nome aparece no Azure Marketplace como o título deste plano. Está limitado a 100 caracteres.

#### <a name="plan-summary"></a>Resumo do plano

Providencie um breve resumo do seu plano, não a oferta. Este resumo está limitado a 100 caracteres.

#### <a name="plan-description"></a>Descrição do plano

Descreva o que torna este plano de software único e descreva quaisquer diferenças entre os planos dentro da sua oferta. Descreva o plano apenas, não a oferta. A descrição do plano pode conter até 2.000 caracteres.

**Selecione Guardar o projeto** antes de continuar.

### <a name="pricing-and-availability"></a>Preços e disponibilidade

Neste painel, você configura:

- Mercados onde este plano está disponível. Todos os planos devem estar disponíveis em pelo menos um [mercado.](../marketplace-geo-availability-currencies.md)
- O preço por hora.
- Quer tornar o plano visível para todos ou apenas para clientes específicos (um público privado).

#### <a name="markets"></a>Mercados

Todos os planos devem estar disponíveis em pelo menos um mercado. Selecione a caixa de verificação para cada local de mercado onde este plano deve estar disponível para compra. (Os utilizadores nestes mercados podem ainda implementar a oferta em todas as regiões do Azure selecionadas na secção ["Configuração do](#plan-setup) Plano".) O botão **Tax Remitted** mostra países/regiões em que a Microsoft remete as vendas e utiliza o imposto em seu nome. A publicação para a China *limita-se* a planos que sejam *gratuitos* ou trazem a sua própria licença (BYOL).

Se já definiu os preços para o seu plano em moeda norte-americana (USD) e adicionou outra localização de mercado, o preço do novo mercado é calculado de acordo com as taxas de câmbio atuais. Reveja sempre o preço de cada mercado antes de publicar. Reveja o seu preço selecionando preços de **exportação (xlsx)** depois de guardar as suas alterações.

Quando remover um mercado, os clientes desse mercado que estão a utilizar implementações ativas não serão capazes de criar novas implementações ou aumentar as suas implementações existentes. As implantações existentes não são afetadas.

#### <a name="pricing"></a>Preços

Para o **modelo Licença**, selecione o plano **de faturação mensal baseado em uso** para configurar os preços para este plano, ou selecione Traga a sua própria **licença** para permitir que os clientes utilizem este plano com a sua licença existente.

Para um plano de faturação mensal baseado na utilização, utilize uma das seguintes três opções de inscrição de preços:

- **Por núcleo**: Forneça preços por núcleo em USD. A Microsoft calcula o preço por tamanho de núcleo e converte-o em moedas locais utilizando a taxa de câmbio atual.
- **Por tamanho do núcleo**: Forneça preços por tamanho do núcleo em USD. A Microsoft calcula o preço e converte-o em moedas locais utilizando a taxa de câmbio atual.
- **Por mercado e dimensão de núcleo**: Forneça preços para cada tamanho central para todos os mercados. Pode importar os preços de uma folha de cálculo.

> [!NOTE]
> Economize alterações de preços para permitir a exportação de dados de preços. Depois de publicado um preço para um mercado no seu plano, não pode ser alterado mais tarde. Para garantir que os preços estão certos antes de os publicar, exporte a folha de cálculo de preços e reveja os preços em cada mercado.

#### <a name="free-trial"></a>Avaliação Gratuita

Pode oferecer um *Teste Gratuito* de um mês ou três meses ou seis meses aos seus clientes.

#### <a name="visibility"></a>Visibilidade

Pode desenhar cada plano para ser visível para todos ou apenas para um público pré-selecionado. Atribua membros a este público restrito utilizando iDs de subscrição Azure.

**Público:** O seu plano pode ser visto por todos.

**Público privado**: Torne o seu plano visível apenas para um público pré-selecionado. Depois de publicado como um plano privado, pode atualizar o público ou mudá-lo para público. Depois de tornar um plano público, deve permanecer público. Não pode ser mudado para um plano privado.

> [!NOTE]
> Um público privado ou restrito é diferente do público de pré-visualização que definiu no painel **de pré-visualização.** Um público de pré-visualização pode aceder à sua oferta _antes_ de ser publicada ao vivo no Azure Marketplace. Embora a escolha do público privado se aplique apenas a um plano específico, o público de pré-visualização pode ver todos os planos privados e públicos para fins de validação.

**Público restrito (IDs de subscrição Azure)**: Atribua ao público que terá acesso a este plano privado utilizando IDs de subscrição Azure. Opcionalmente, inclua uma descrição de cada ID de subscrição Azure que designou. Adicione até 10 IDs de subscrição manualmente ou até 20.000 IDs se estiver a importar uma folha de cálculo CSV. Os IDs de subscrição Azure são representados como GUIDs, e todas as letras devem ser minúsculas.

>[!Note]
>As ofertas privadas não são suportadas com subscrições Azure estabelecidas através de um revendedor do programa Cloud Solution Provider (CSP).

#### <a name="hide-a-plan"></a>Esconder um plano

Se a sua máquina virtual deve ser usada apenas indiretamente quando é referenciada através de outro modelo de solução ou aplicação gerida, selecione esta caixa de verificação para publicar a máquina virtual, mas esconda-a de clientes que possam estar a pesquisar ou navegar diretamente por ela.

> [!NOTE]
> Planos ocultos não suportam links de pré-visualização.

**Selecione Guardar o projeto** antes de continuar.

### <a name="technical-configuration"></a>Configuração técnica

Forneça as imagens e outras propriedades técnicas que estejam associadas a este plano. Para obter mais informações, consulte [Criar um ativo técnico Azure VM](create-azure-container-technical-assets.md).

> [!NOTE]
> O **separador de configuração técnica** não é apresentado se configurar este plano para reutilizar pacotes de outro plano no separador **de configuração do Plano.**

#### <a name="operating-system"></a>Sistema operativo

No painel do **sistema operativo,** faça o seguinte:

- Para **a família do sistema operativo,** selecione o sistema operativo **Windows** ou **Linux.**
- Para **Lançamento** ou **Fornecedor,** selecione a versão do Windows ou o fornecedor Linux.
- Para **um nome amigável do OS,** insira um nome de sistema operativo amigável. Este nome é visível para os clientes.

#### <a name="recommended-vm-sizes"></a>Tamanhos VM recomendados

Selecione até seis tamanhos de máquina virtuais recomendados para exibir no Azure Marketplace.

#### <a name="open-ports"></a>Abrir portas

Abra portas públicas ou privadas numa máquina virtual implantada.

#### <a name="storage-option-for-deployment"></a>Opção de armazenamento para implantação

Para **a opção de implementação do disco,** selecione o tipo de implementação do disco que os seus clientes podem utilizar para a máquina virtual. A Microsoft recomenda limitar a implementação apenas à **implementação do disco gerido.**

#### <a name="properties"></a>Propriedades

Para **suporte rede acelerada**, selecione se o seu VM suporta [rede acelerada](https://go.microsoft.com/fwlink/?linkid=2124513).

#### <a name="vm-images"></a>Imagens de VM

Forneça uma versão em disco e a assinatura de acesso partilhado (SAS) URI para as imagens de máquinas virtuais. Adicione até 16 discos de dados para cada imagem VM. Forneça apenas uma nova versão de imagem por plano numa submissão especificada. Depois de uma imagem ter sido publicada, não pode editá-la, mas pode eliminá-la. A eliminação de uma versão impede que os utilizadores novos e já existentes implementem uma nova instância da versão eliminada.

- **Versão do disco**: A versão da imagem que está a fornecer.
- **SAS URI**: A localização na sua conta de armazenamento Azure onde guardou o sistema operativo VHD. Para aprender a obter um SAS URI, consulte [obter a assinatura de acesso partilhado URI para a sua imagem VM](get-sas-uri.md).
- As imagens de disco de dados são também URIs de acesso partilhado VHD que são armazenados nas suas contas de armazenamento Azure.
- Adicione apenas uma imagem por submissão num plano.

Independentemente do sistema operativo que utilize, adicione apenas o número mínimo de discos de dados que a solução necessita. Durante a implementação, os clientes não podem remover discos que fazem parte de uma imagem, mas podem sempre adicionar discos durante ou após a implementação.

**Selecione Guardar o rascunho** antes de continuar e voltar à **visão geral do Plano**.

## <a name="resell-through-csps"></a>Revender através de CSPs

Expanda o alcance da sua oferta disponibilizando-a aos parceiros do programa [Cloud Solution Provider (CSP).](https://azure.microsoft.com/offers/ms-azr-0145p/) Todos os planos Bring-your-your-own-license (BYOL) são automaticamente optados pelo programa. Também pode optar pelos seus planos não BYOL.

**Selecione Guardar o projeto** antes de continuar.

## <a name="test-drive"></a>Versão de Teste

Crie uma demonstração, ou *Test Drive,* que permite que os clientes experimentem a sua oferta por um período de tempo fixo antes de comprá-la. Para criar um ambiente de demonstração para os seus [clientes, consulte as ofertas de Test Drive no mercado comercial.](test-drive.md)

Para ativar uma Unidade de Teste, selecione a caixa **de verificação de unidade de teste** no painel de **configuração da Oferta.** Para remover o Test Drive da sua oferta, limpe a caixa de verificação.

Recursos adicionais de Test Drive:

- [Melhores práticas de marketing](../what-is-test-drive.md)
- [Melhores práticas técnicas](https://github.com/Azure/AzureTestDrive/wiki/Test-Drive-Best-Practices)
- [Visão geral do Test Drive](https://assetsprod.microsoft.com/mpn/azure-marketplace-appsource-test-drives.pdf) Ficheiro PDF (certifique-se de que o seu bloqueador pop-up está desligado)

**Selecione Guardar o projeto** antes de continuar.

## <a name="review-and-publish"></a>Rever e publicar

Depois de ter concluído todas as secções necessárias da oferta, pode submetê-la para revisão e publicação.

No canto superior direito, selecione **'Rever e publicar.**

Neste painel pode:

- Ver o estado de conclusão de cada secção da oferta:

  - **Não iniciada**: A secção não foi iniciada e precisa de ser concluída.
  - **Incompleto**: A secção tem erros que devem ser corrigidos ou requer que forneça mais informações. Para obter orientações sobre a atualização das informações incompletas, consulte as secções anteriores deste artigo.
  - **Completo**: A secção está completa e não há erros. Todas as secções da oferta devem estar completas antes de poder submeter a oferta.
- Forneça **notas para certificação** à equipa de certificação para ajudar a garantir que a sua aplicação seja testada corretamente. Inclua instruções de teste e quaisquer notas suplementares que possam ajudar a equipa a compreender a sua aplicação.

Para submeter a oferta de publicação, selecione **'Rever' e publicar**.

A Microsoft enviará uma mensagem de e-mail para o informar quando uma versão de pré-visualização da oferta estiver disponível para rever e aprovar. Para publicar a sua oferta ao público (ou, se for uma oferta privada, publique-a para um público privado), vá ao Partner Center, vá à página **geral** da sua oferta e, em seguida, selecione **Go-live**. O estado da sua oferta aparece no topo da página quando a publicação está em andamento.

### <a name="errors-and-review-feedback"></a>Erros e feedback de revisão

O **passo de validação manual** no processo de publicação representa uma revisão extensiva da sua oferta e dos seus ativos técnicos associados. Se este processo descobrir erros com a sua oferta, receberá um relatório de certificação que detalha os problemas. Basta fazer as correções necessárias e republice a sua oferta.

## <a name="offer-overview"></a>Oferta geral

A página **geral da Oferta** mostra uma representação visual dos passos, tanto concluídos como em curso, que são obrigados a publicar a sua oferta e o tempo que cada passo deve demorar a ser concluído.

Esta página também inclui links para ajudá-lo a trabalhar com a oferta, dependendo do seu estado:

- Se a oferta for um rascunho: [Eliminar a oferta de rascunho](update-existing-offer.md#delete-a-draft-offer)
- Se a oferta for ao vivo: [Pare de vender a oferta](update-existing-offer.md#stop-selling-an-offer-or-plan)
- Se a oferta estiver em [pré-visualização: Go-live](publishing-status.md#publisher-approval)
- Se ainda não tiver concluído a assinatura da editora: [Cancelar a publicação](update-existing-offer.md#cancel-publishing)

## <a name="marketplace-examples"></a>Exemplos de mercado

Aqui está um exemplo de como a oferta de informação aparece no Azure Marketplace:

:::image type="content" source="media/example-azure-marketplace-virtual-machine-offer.png" alt-text="Ilustra como esta oferta aparece no Azure Marketplace.":::

#### <a name="call-out-descriptions"></a>Descrições de chamadas

1. Logotipo grande
2. Preço
3. Categorias
4. Termos e condições
5. Endereço de política de privacidade (link)
6. Nome da oferta
7. Descrição
8. Ligações úteis
9. Screenshots/vídeos

<br>Aqui está um exemplo de como a oferta de informação aparece nos resultados da pesquisa do Azure Marketplace:

:::image type="content" source="media/example-azure-marketplace-virtual-machine-search-results.png" alt-text="Ilustra como esta oferta aparece no Azure Marketplace.":::

#### <a name="call-out-descriptions"></a>Descrições de chamadas

1. Logotipo pequeno
2. Nome da oferta
3. Resumo dos resultados da pesquisa
4. Avaliação

<br>Aqui está um exemplo dos detalhes do plano Azure Marketplace:

:::image type="content" source="media/example-azure-marketplace-virtual-machine-plan-details.png" alt-text="Ilustra como esta oferta aparece no Azure Marketplace.":::

#### <a name="call-out-descriptions"></a>Descrições de chamadas

1. Nome do plano e resumo
2. Recomendar tamanhos VM
3. Preços do plano

<br>Aqui está um exemplo de como a oferta de informação aparece no portal Azure:

:::image type="content" source="media/example-azure-portal-virtual-machine-offer.png" alt-text="Ilustra como esta oferta aparece no Azure Marketplace.":::

#### <a name="call-out-descriptions"></a>Descrições de chamadas

1. Nome
2. Descrição
3. Ligações úteis
4. Screenshots/vídeos

<br>Aqui está um exemplo de como a oferta de informação aparece nos resultados de pesquisa do portal Azure:

:::image type="content" source="media/example-azure-portal-virtual-machine-search-results.png" alt-text="Ilustra como esta oferta aparece no Azure Marketplace.":::

#### <a name="call-out-descriptions"></a>Descrições de chamadas

1. Logotipo pequeno
2. Nome da oferta
3. Resumo dos resultados da pesquisa

<br>Aqui está um exemplo dos detalhes do plano do portal Azure:

:::image type="content" source="media/example-azure-portal-virtual-machine-plan-details.png" alt-text="Ilustra como esta oferta aparece no Azure Marketplace.":::

#### <a name="call-out-descriptions"></a>Descrições de chamadas

1. Nome do plano
2. Descrição do plano

## <a name="next-step"></a>Passo seguinte

- [Atualizar uma oferta existente no mercado comercial](update-existing-offer.md)
