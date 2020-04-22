---
title: Criar uma oferta de máquina virtual Azure - Azure Marketplace
description: Aprenda a criar uma oferta de máquina virtual no mercado comercial.
author: emuench
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/10/2020
ms.openlocfilehash: d5626f00e9627338349d9b579bcf26256148b551
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/21/2020
ms.locfileid: "81731256"
---
# <a name="create-an-azure-virtual-machine-offer"></a>Criar uma oferta de máquina virtual Azure

> [!IMPORTANT]
> Estamos a mover a gestão das suas ofertas Azure VM do Cloud Partner Portal para partner Center. Até que as suas ofertas sejam migradas, por favor continue a seguir as instruções na [oferta da Create Virtual Machine](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-create-offer) no Portal do Parceiro cloud para gerir as suas ofertas.

Este artigo descreve como criar e publicar uma oferta de máquina virtual Azure ao [Azure Marketplace.](https://azuremarketplace.microsoft.com/) Dirige-se a máquinas virtuais baseadas no Windows e linux que contêm um sistema operativo, disco rígido virtual (VHD) e até 16 discos de dados.

## <a name="introduction"></a>Introdução

### <a name="publishing-benefits"></a>Benefícios de publicação

A publicação no Azure Marketplace tem os seguintes benefícios:

- Promova a sua empresa usando a marca Microsoft
- Alcance mais de 100 milhões de utilizadores do Office 365 e da Dynamics 365 e mais de 200.000 organizações através do Azure Marketplace
- Obtenha pistas de alta qualidade destes mercados
- Promote os seus serviços pelas equipas de campo e televendas da Microsoft

### <a name="before-you-begin"></a>Antes de começar

Se ainda não o fez, reveja a oferta da [máquina Virtual](https://docs.microsoft.com/azure/marketplace/marketplace-virtual-machines) e este material de máquina virtual Azure:

- Guias quickstart
  - [Modelos Azure Quickstart](https://azure.microsoft.com/resources/templates/)
  - [Modelos GitHub Azure Quickstart](https://github.com/azure/azure-quickstart-templates)
- Tutoriais
  - [VMs do Linux](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-manage-vm)
  - [VMs do Windows](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-manage-vm)
- Amostras
  - [Amostras azure CLI para VMs Linux](https://docs.microsoft.com/azure/virtual-machines/linux/cli-samples)
  - [Azure PowerShell para VMs Linux](https://docs.microsoft.com/azure/virtual-machines/linux/powershell-samples)
  - [Amostras azure CLI para VMs do Windows](https://docs.microsoft.com/azure/virtual-machines/windows/cli-samples)
  - [Azure PowerShell para VMs windows](https://docs.microsoft.com/azure/virtual-machines/scripts/virtual-machines-windows-powershell-sample-create-vm-quick)

### <a name="fundamentals-in-technical-knowledge"></a>Fundamentos do conhecimento técnico

Projetar, construir e testar estes ativos leva tempo e requer conhecimentos técnicos tanto da plataforma Azure como das tecnologias usadas para construir a oferta.

A sua equipa de engenharia deve compreender as seguintes tecnologias da Microsoft:

- Compreensão básica dos [Serviços Azure](https://azure.microsoft.com/services/)
- Como [conceber e arquiteto sintetizar aplicações Azure](https://azure.microsoft.com/solutions/architecture/)
- Conhecimento de trabalho das [Máquinas Virtuais Azure,](https://azure.microsoft.com/services/virtual-machines/) [Armazenamento Azure](https://azure.microsoft.com/services/?filter=storage#storage)e [Networking Azure](https://azure.microsoft.com/services/?filter=networking#networking)

## <a name="create-an-azure-virtual-machine-offer"></a>Criar uma oferta de máquina virtual Azure

Antes de poder criar uma oferta de máquina virtual Azure, deve ter uma conta de marketplace comercial no Partner Center. Se ainda não criou uma, consulte [criar uma conta de marketplace comercial no Partner Center.](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-account)

1. Inscreva-se no [Partner Center](https://partner.microsoft.com/dashboard/home), e depois a partir do menu superior, selecione **Dashboard**.
2. No bar de navegação à esquerda, selecione **Mercado Comercial**, em **seguida, visão geral**.
3. Na página **'Visão Geral',** selecione **+ Nova oferta,** em seguida, **Máquina Virtual Azure**. A caixa de diálogo **da Nova Oferta** aparece.

    ![Ilustra a página de visão geral no Partner Center com o novo botão de oferta e a oferta da Máquina Virtual Azure selecionada.](media/avm-create7.png)

## <a name="offer-id-and-alias"></a>Oferecer ID e pseudónimo

Introduza um **ID de oferta**. Este é um identificador único para cada oferta na sua conta.

- Este ID é visível para os clientes no endereço web para a oferta de marketplace e no Azure PowerShell e Azure CLI, se aplicável.
- Utilize apenas letras minúsculas e números. Pode incluir hífenes e sublinhados, mas sem espaços, e está limitado a 50 caracteres. Por exemplo, se introduzir o **test-offer-1** aqui, `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`o endereço web da oferta será .
- O ID da Oferta não pode ser alterado depois de selecionar **Criar**.

Insira um **pseudónimo da Oferta.** Este é o nome usado para a oferta no Partner Center. Este nome não é usado no mercado e é diferente do nome da oferta e outros valores mostrados aos clientes.

Selecione **Criar** para gerar a oferta e continuar.

## <a name="offer-setup"></a>Configuração de oferta

### <a name="test-drive"></a>Test drive

Configurar uma demonstração (test drive) que permite aos clientes experimentarem a sua oferta antes de a comprarem. Para criar um ambiente de demonstração que permita aos clientes experimentarem a sua oferta por um período de tempo fixo, consulte test [Drive a sua oferta no mercado comercial](https://docs.microsoft.com/azure/marketplace/partner-center-portal/test-drive).

Para ativar um test drive, selecione a caixa de verificação **de acionamento de acionamento de teste.** Para remover o test drive da sua oferta, limpe esta caixa de verificação.

Recursos adicionais de test drive:

- [Boas práticas técnicas](https://github.com/Azure/AzureTestDrive/wiki/Test-Drive-Best-Practices)
- [Melhores práticas de marketing](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/marketing-and-best-practices)
- [Visão geral das unidades de teste](https://assetsprod.microsoft.com/mpn/azure-marketplace-appsource-test-drives.pdf) PDF (certifique-se de que o seu bloqueador pop-up está desligado).

### <a name="lead-management"></a>Gestão de oportunidades potenciais

Ao publicar a sua oferta no mercado comercial com o Partner Center, conecte-a ao seu sistema de Gestão de Relacionamento com o Cliente (CRM). Isto permite-lhe receber informações de contacto do cliente assim que alguém expressa interesse ou utiliza o seu produto. É necessário ligar-se a um CRM se ativar o **Test Drive** (ver secção anterior), caso contrário é opcional.

1. Selecione um destino de oportunidades potenciais para onde quer que enviemos as oportunidades potenciais de clientes. Partner Center suporta os seguintes sistemas CRM:
    - [Dinâmica 365](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics) para envolvimento do cliente
    - [Marketo](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-marketo)
    - [Salesforce](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-salesforce)

    > [!NOTE]
    > Se o seu sistema CRM não estiver listado acima, utilize [o Azure Table](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-azure-table) ou [o Https Endpoint](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-https) para armazenar os dados de chumbo do cliente. Em seguida, exporte os dados para o seu sistema crm.

2. Ligue a sua oferta ao destino principal ao publicar no Partner Center.
3. Confirme se a ligação ao destino principal está corretamente configurada. Depois de publicá-lo no Partner Center, validamos a ligação e enviaremos-lhe um teste. Enquanto faz a pré-visualização da oferta antes de ser transmitida ao vivo, também pode testar a sua ligação de chumbo tentando implementar a oferta no ambiente de pré-visualização.
4. Certifique-se de que a ligação ao destino principal permanece atualizada para que não perca nenhuma pista.

Selecione **guardar rascunho** antes de continuar.

## <a name="properties"></a>Propriedades

Esta página permite definir as categorias e indústrias usadas para agrupar a sua oferta no mercado, na sua versão de aplicação e nos contratos legais que suportam a sua oferta.

### <a name="categories"></a>Categorias

Selecione um mínimo de uma e um máximo de cinco categorias. Estas categorias são usadas para colocar a sua oferta nas áreas de pesquisa de mercado apropriadas. Na descrição da oferta, explique como a sua oferta suporta estas categorias. As ofertas de máquinas virtuais aparecem na categoria **Compute** no Mercado Azure.

### <a name="legal"></a>Legal

Deve fornecer termos e condições para a oferta. Tem duas opções:

- Use os seus próprios termos e condições
- Utilize o Contrato Padrão para o mercado comercial da Microsoft

#### <a name="use-your-own-terms-and-conditions"></a>Use os seus próprios termos e condições

Para fornecer os seus próprios termos e condições personalizados, insira até 10.000 caracteres de texto na caixa **de Termos e Condições.** Se necessitar de uma descrição mais longa, introduza um único endereço web que aponte para onde os seus termos e condições podem ser encontrados. Mostrará aos clientes como um link ativo.

Os clientes devem aceitar estes termos antes de poderem experimentar a sua oferta.

#### <a name="use-the-standard-contract-for-the-microsoft-commercial-marketplace"></a>Utilize o Contrato Padrão para o mercado comercial da Microsoft

Para simplificar o processo de aquisição para os clientes e reduzir a complexidade jurídica para os fornecedores de software, a Microsoft oferece um Contrato Padrão para o mercado comercial. Quando oferece o seu software ao abrigo do Contrato Padrão, os clientes só precisam de o ler e aceitar uma vez, e não é preciso criar termos e condições personalizados.

Utilize o Contrato Padrão selecionando o Contrato Padrão para a caixa de verificação de **mercado comercial da Microsoft** **e,** em seguida, aceite na janela pop-up (pode ter de deslocar-se para o ver).

![Ilustra a página de visão geral no Partner Center com o botão de oferta nova e oferta de serviço de Consultoria selecionada.](media/use-standard-contract.png)

> [!NOTE]
> Depois de publicar uma oferta usando o Contrato Padrão para o mercado comercial, não pode usar os seus próprios termos e condições personalizados. Ou ofereça a sua solução ao abrigo do Contrato Padrão **ou** nos seus próprios termos e condições.

Para saber mais sobre o Contrato Padrão, consulte o Standard Contract para o [mercado comercial](https://docs.microsoft.com/azure/marketplace/standard-contract)da Microsoft. Pode baixar o [Contrato Padrão](https://go.microsoft.com/fwlink/?linkid=2041178) como PDF (certifique-se de que o seu bloqueador pop-up está desligado).

##### <a name="standard-contract-amendments"></a>Alterações-padrão do contrato

As Alterações-Padrão do Contrato permitem-lhe selecionar os termos do Contrato Padrão para a simplicidade e criar os termos para o seu produto ou negócio. Os clientes só precisam de rever as alterações ao contrato se já tiverem revisto e aceite o Contrato Padrão da Microsoft. Existem dois tipos de alterações disponíveis: universais e personalizadas.

**Alterações Universais** – Estas são aplicadas universalmente ao Contrato Padrão para todos os clientes. São mostrados a todos os clientes da oferta no fluxo de compra. Os clientes devem aceitar os termos do Contrato Padrão e as alterações antes de poderem utilizar a sua oferta. Pode fornecer uma única emenda universal por oferta. Pode introduzir um número ilimitado de caracteres nesta caixa. Estes termos são apresentados aos clientes no portal AppSource, Azure Marketplace e/ou Azure durante o fluxo de descoberta e compra.

**Alterações Personalizadas** – Estas são alterações especiais ao Contrato Padrão que são direcionadas a clientes específicos através de IDs de inquilinos Azure. Pode escolher o inquilino que quiser. Apenas os clientes do inquilino serão apresentados com os termos de emenda personalizados no fluxo de compra da oferta. Os clientes devem aceitar os termos do Contrato Padrão e as alterações antes de poderem utilizar a sua oferta.

Comece por selecionar Termos de **alteração personalizados (Max 10)**. Você pode fornecer até dez termos de emenda salfatopor oferta.

- Termos de **emenda personalizados** – Insira os seus próprios termos de emenda na caixa de termos de emenda personalizada. Pode introduzir um número ilimitado de caracteres. Apenas os clientes das IDs do inquilino que especifica para estes termos personalizados irão vê-los no fluxo de compra da oferta no portal Azure.
- **IDs** de inquilino (obrigatório) – Cada emenda personalizada pode ser direcionada a até 20 IDs de inquilinos. Se adicionar uma emenda personalizada, deve fornecer pelo menos uma identificação de inquilino, que identifica o seu cliente em Azure. o seu cliente pode encontrar no Azure abaixo, em seguida, propriedades. O valor de ID do diretório é o ID do inquilino (por exemplo, 50c464d3-4930-494c-963c-1e951d15360e). Também pode encontrar a identificação do seu cliente com o seu nome de domínio no Qual é o meu Microsoft Azure e o [Office 365 tenant ID?](https://www.whatismytenantid.com/)
- **Descrição** (opcional) – Forneça uma descrição amigável para o ID do inquilino que o ajude a identificar o cliente que está a visar com a alteração.

> [!NOTE]
> Estes dois tipos de emendas empilham-se em cima uns dos outros. Os clientes visados com alterações personalizadas também receberão a alteração universal ao Contrato Padrão durante a compra.

Selecione **guardar rascunho** antes de continuar.

## <a name="offer-listing"></a>Oferta listagem

Esta página permite definir detalhes de oferta, tais como nome de oferta, descrição, links e contactos.

> [!NOTE]
> Oferecer conteúdo de listagem (como a descrição, documentos, imagens e termos de utilização) não é necessário estar em inglês, desde que a descrição da oferta comece com a frase: "Esta aplicação está disponível apenas em [língua não inglesa]." Também pode fornecer um _endereço web De Link Útil_ para oferecer conteúdo num idioma diferente do utilizado no conteúdo de listagem de oferta.

### <a name="marketplace-details"></a>Detalhes do mercado

#### <a name="name"></a>Nome

O nome que insere aqui é mostrado aos clientes como o título da sua listagem de oferta. Este campo está pré-preenchido com o texto que inseriu na caixa **de pseudónimos Offer** quando criou a oferta. Pode alterar este nome posteriormente.

O nome:

- Pode ser marcado por marca (e pode incluir símbolos de marca e direitos de autor)
- Não pode ter mais de 50 caracteres
- Não posso incluir emojis.

#### <a name="search-results-summary"></a>Resumo dos resultados da pesquisa

Uma breve descrição da sua oferta. Isto pode ter até 100 caracteres de comprimento e é usado em resultados de pesquisa de mercado.

#### <a name="long-summary"></a>Resumo longo

Forneça uma descrição mais longa da sua oferta. Isto pode ter até 256 caracteres de comprimento e é usado em resultados de pesquisa de mercado.

#### <a name="description"></a>Descrição

Forneça uma descrição detalhada da sua oferta, até 3.000 caracteres. Isto é apresentado aos clientes na visão geral da listagem do mercado comercial.

Inclua um ou mais dos seguintes na sua descrição:

- O valor e os principais benefícios da sua oferta
- Categoria ou associações industriais, ou ambos
- Oportunidades de compra na aplicação
- Quaisquer divulgações necessárias

Aqui ficam algumas dicas para escrever a sua descrição:

- Descreva claramente a proposta de valor da sua oferta nas primeiras frases da sua descrição. Incluir os seguintes itens:
  - Descrição da sua oferta.
  - O tipo de utilizador que beneficia da sua oferta.
  - Necessidades ou problemas do cliente que a oferta endereça.
- Lembre-se que as primeiras frases podem ser exibidas nos resultados do motor de busca.
- Não confie em funcionalidades e funcionalidades para vender a sua oferta. Em vez disso, concentre-se no valor que a sua oferta proporciona.
- Utilize palavras específicas da indústria ou baseadas em benefícios.

Para tornar a sua descrição mais envolvente, use o rico editor de texto para formatar a sua descrição. O rico editor de texto permite-lhe adicionar números, balas, ousados, itálicos e travessos para tornar a sua descrição mais legível.

![Ilustra a página de visão geral no Partner Center com o botão de oferta nova e oferta de serviço de Consultoria selecionada.](media/power-bi-rich-text-editor.png)

#### <a name="privacy-policy-link"></a>Ligação política de privacidade

Insira o endereço web (URL) na política de privacidade da sua organização. Certifique-se de que a sua oferta está em conformidade com as leis e regulamentos de privacidade. Também deve publicar uma política de privacidade válida no seu website.

### <a name="useful-links"></a>Ligações úteis

Forneça documentos online suplementares sobre a sua oferta. Para adicionar um link, selecione **+ Adicione um link** e, em seguida, complete os seguintes campos:

- **Nome** – Os clientes verão o nome na página de detalhes.
- **Link (URL)** – Introduza um link para os clientes verem o seu documento online.

### <a name="customer-support-links"></a>Links de apoio ao cliente

Forneça o site de suporte onde os clientes podem chegar à sua equipa de suporte.

- Site de apoio azure global
- Site de apoio do Governo Azure

### <a name="partner-support-contact"></a>Contacto de apoio ao parceiro

Forneça informações de contacto para os parceiros da Microsoft usarem quando os seus clientes abrirem um bilhete de suporte. Isto não será listado no mercado.

- Nome
- Email
- Telefone

### <a name="engineering-contact"></a>Contacto de engenharia

Forneça informações de contacto para a Microsoft usar quando houver problemas com a sua oferta, incluindo problemas com certificação. Isto não será listado no mercado.

- Nome
- Email
- Telefone

### <a name="marketplace-media"></a>Meios de comunicação do mercado

Forneça logotipos e imagens para usar com a sua oferta. Todas as imagens devem estar em formato PNG. Imagens desfocadas farão com que a sua submissão seja rejeitada.

#### <a name="marketplace-logos"></a>Logotipos do mercado

Forneça ficheiros PNG do logótipo da sua oferta nos seguintes quatro tamanhos de pixel:

- **Pequeno** (48 x 48)
- **Médio** (90 x 90)
- **Grande** (216 x 216)
- **Ampla** (255 x 115)

Todos os quatro logótipos são necessários e são usados em diferentes lugares na listagem do mercado.

#### <a name="screenshots"></a>Capturas de Ecrã

Adicione cinco imagens que mostram como a sua oferta funciona. Cada screenshot deve ter 1280 x 720 pixels em tamanho e em formato PNG. Também deve adicionar uma legenda para descrever a sua imagem.

#### <a name="videos"></a>Vídeos

Adicione cinco vídeos que demonstram a sua oferta. Estes devem ser hospedados num serviço de vídeo externo. Introduza o nome de cada vídeo, endereço web e uma imagem PNG miniatura do vídeo a 1280 x 720 pixels de tamanho.

Para obter recursos adicionais de listagem de mercado, consulte [as melhores práticas para anúncios](https://docs.microsoft.com/azure/marketplace/gtm-offer-listing-best-practices)de oferta de mercado.

Selecione **guardar rascunho** antes de continuar.

## <a name="preview"></a>Pré-visualização

No separador Preview, escolha um Público de **Pré-visualização** limitado para validar a sua oferta antes de publicá-la ao vivo para o público mais amplo do mercado.

> [!IMPORTANT]
> Depois de verificar a sua oferta em Preview, selecione **Ir ao vivo** para publicar a sua oferta ao público do mercado comercial.

O seu público de pré-visualização é identificado por GUIDs de assinatura Azure, juntamente com uma descrição opcional para cada um. Nenhum destes campos pode ser visto pelos clientes. Pode encontrar o seu ID de subscrição Azure na página **de Subscrições** no portal Azure.

Adicione pelo menos um ID de subscrição Azure, individualmente (até 10) ou carregando um ficheiro CSV (até 100). Ao adicionar estes IDs de subscrição, define quem pode pré-visualizar a sua oferta antes de ser publicado ao vivo. Se a sua oferta já estiver ao vivo, poderá ainda definir um público de pré-visualização para testes, oferecendo alterações ou atualizações à sua oferta.

> [!NOTE]
> Um público de pré-visualização difere de um público privado. Um público pré-visualizado pode aceder à sua oferta _antes_ de ser publicada ao vivo nos mercados. Eles podem ver e validar todos os planos, incluindo aqueles que estarão disponíveis apenas para um público privado depois da sua oferta ser totalmente publicada no mercado. Um público privado (definido no separador **de preços e disponibilidade** do plano) tem acesso exclusivo a um determinado plano.

Selecione **Guardar rascunho** antes de seguir para a próxima secção, Plana visão geral.

## <a name="plan-overview"></a>Visão geral do plano

Pode fornecer diferentes opções de plano dentro da mesma oferta no Partner Center. Estes planos eram anteriormente referidos como SKUs. Uma oferta requer pelo menos um plano, que pode diferir em termos de público de rentabilização, regiões, características do Azure ou imagens VM.

Depois de criar os seus planos, o separador de **visão geral** do Plano mostra:

- Nomes de planos
- Modelos de licença
- Público (público ou privado)
- Estado editorial atual
- Ações disponíveis

As ações disponíveis na visão geral do Plano variam consoante o estado atual do seu plano. Os relatórios incluem:

- **Eliminar o projeto** – Se o estado do plano for um rascunho
- **Pare de vender plano** ou **sincronizar audiência privada** – Se o estado do plano for publicado ao vivo

### <a name="create-new-plan"></a>Criar novo plano

Selecione **+ Criar um novo plano** no topo. A caixa de diálogo **do novo plano** aparece.

Na caixa de ID do **Plano,** crie um plano único de ID para cada plano nesta oferta. Este ID será visível para os clientes no endereço web do produto. Utilize apenas letras minúsculas e números, traços ou sublinhados, e um máximo de 50 caracteres.

> [!NOTE]
> O ID do plano não pode ser alterado depois de selecionar **Criar**.

Na caixa de **nome** sinuosa, insira um nome para este plano. Os clientes vêem este nome ao decidir qual o plano a selecionar dentro da sua oferta. Crie um nome único que aponte claramente as diferenças de cada plano. Por exemplo, pode utilizar o **Windows Server** com planos **Pay-as-you-go,** **BYOL,** **Advanced**e **Enterprise**.

Selecione **Criar**.

### <a name="plan-setup"></a>Configuração do plano

Detete a configuração de alto nível para o tipo de plano, quer reutilize a configuração técnica de outro plano, e em que as regiões de Azure o plano deve estar disponível. As suas seleções determinam quais os campos que são exibidos em outros separadores para o mesmo plano.

#### <a name="reuse-technical-configuration"></a>Reutilizar a configuração técnica

Se tiver mais de um plano do mesmo tipo, e os pacotes forem idênticos entre eles, pode selecionar **este plano reutiliza a configuração técnica de outro plano**. Esta opção permite selecionar um dos outros planos do mesmo tipo para esta oferta e reutilizar a sua configuração técnica.

> [!NOTE]
> Quando reutiliza a configuração técnica de outro plano, todo o separador de **configuração técnica** desaparece deste plano. Os detalhes técnicos de configuração do outro plano, incluindo quaisquer atualizações que fizer no futuro, também serão utilizados para este plano. Esta definição não pode ser alterada depois de este plano ser publicado.

#### <a name="azure-regions"></a>Regiões do Azure

O seu plano deve ser disponibilizado em pelo menos uma região de Azure.

Selecione a opção **Azure Global** para disponibilizar o seu plano aos clientes de todas as regiões públicas do Azure que tenham integração no mercado comercial. Para mais detalhes, consulte disponibilidade [geográfica e suporte cambial.](https://docs.microsoft.com/azure/marketplace/marketplace-geo-availability-currencies)

Selecione a opção **do Governo Azure** para disponibilizar o seu plano na região do [Governo de Azure.](https://docs.microsoft.com/azure/azure-government/documentation-government-welcome) Esta região fornece acesso controlado a clientes de entidades federais, estaduais, locais ou tribais dos EUA, bem como parceiros elegíveis para os servir. Você, como editor, é responsável por quaisquer controlos de conformidade, medidas de segurança e boas práticas. O Governo azure utiliza centros e redes de dados fisicamente isolados (localizados apenas nos EUA).

Antes de publicar ao [Governo Azure,](https://docs.microsoft.com/azure/azure-government/documentation-government-manage-marketplace-partners)teste e valide o seu plano no ambiente, uma vez que certos pontos finais podem diferir. Para configurar e testar o seu plano, solicite uma conta de teste do julgamento do [Governo do Microsoft Azure](https://azure.microsoft.com/global-infrastructure/government/request/).

> [!NOTE]
> Depois de o seu plano ser publicado e disponível numa região específica de Azure, não pode remover essa região.

#### <a name="azure-government-certifications"></a>Certificações do Governo Azure

Esta opção só é visível se selecionar **o Governo Azure** nas **regiões de Azure.**

Os serviços do Governo Azure tratam de dados que estão sujeitos a certos regulamentos e requisitos governamentais. Por exemplo, FedRAMP, NIST 800.171 (DIB), ITAR, IRS 1075, DoD L4 e CJIS. Para sensibilizar as suas certificações para estes programas, pode fornecer até 100 links que os descrevem. Estes podem ser links para a sua listagem no programa diretamente ou links para descrições da sua conformidade com os mesmos nos seus próprios websites. Estas ligações são visíveis apenas aos clientes do Governo Azure.

Selecione **guardar rascunho** antes de continuar.

### <a name="plan-listing"></a>Listagem de planos

É aqui que configura os detalhes da listagem do plano. Este separador apresenta informações específicas que podem diferir entre planos na mesma oferta.

#### <a name="plan-name"></a>Nome do plano

Isto está preenchido com o nome que deu ao seu plano quando o criou. Este nome aparece no mercado como o título deste plano e está limitado a 100 caracteres.

#### <a name="plan-summary"></a>Resumo do plano

Forneça um breve resumo do seu plano (não a oferta). Este resumo está limitado a 100 caracteres.

#### <a name="plan-description"></a>Descrição do plano

Descreva o que torna este plano de software único, bem como diferenças entre planos dentro da sua oferta. Não descreva a oferta, só o plano. A descrição do plano pode conter até 2.000 caracteres.

Selecione **guardar rascunho** antes de continuar.

### <a name="pricing-and-availability"></a>Preços e disponibilidade

Neste separador, configurará o seguinte:

- Mercados este plano estará disponível em
- O preço por hora
- Quer tornar o plano visível para todos ou apenas para clientes específicos (um público privado)

#### <a name="markets"></a>Mercados

Todos os planos devem estar disponíveis em pelo menos um mercado. Selecione a caixa de verificação para cada local de mercado onde este plano deve estar disponível para compra (os utilizadores nestes mercados ainda podem implementar a oferta para todas as regiões azure selecionadas no plano de **[configuração).](#plan-setup)** O botão **Tax Remitted** mostra países em que a Microsoft atribui as vendas e utiliza o imposto em seu nome. A publicação na China limita-se a planos que sejam **gratuitos** ou **tragam a sua própria licença** (BYOL).

Se já definiu preços para o seu plano em Dólares dos Estados Unidos (USD) e adicionar outra localização de mercado, o preço do novo mercado será calculado de acordo com as taxas de câmbio atuais. Reveja sempre o preço de cada mercado antes de publicar. Reveja os preços utilizando o link **Preços de Exportação (xlsx)** depois de ter guardado as suas alterações.

Quando se remove um mercado, os clientes desse mercado utilizando implementações ativas não serão capazes de criar novas implementações ou aumentar as suas implementações existentes. As implantações existentes não serão afetadas.

#### <a name="pricing"></a>Preços

**Modelo** de licença – Selecione **plano mensal de faturação baseado em Utilização** para configurar preços para este plano ou Trazer a **sua própria licença** para permitir que os clientes utilizem este plano com a sua licença existente.

Para um plano mensal de faturação baseado em utilização, utilize uma das seguintes três opções de entrada de preços:

- **Per core** – Fornecer o preço por núcleo em Dólares dos Estados Unidos (USD). Calcularemos os preços por tamanho do núcleo e converteremos em moedas locais usando a taxa de câmbio atual.
- **Por tamanho do núcleo** – Forneça preços por tamanho core em USD. Vamos converter os preços em moedas locais usando a taxa de câmbio atual.
- **Por mercado e tamanho central** – Forneça preços para cada tamanho central para todos os mercados. Pode importar preços a partir de uma folha de cálculo.

> [!NOTE]
> Poupe alterações de preços para permitir a exportação de dados de preços. Depois de um preço para um mercado no seu plano ser publicado, não pode ser alterado mais tarde. Certifique-se de que estes preços estão certos antes de publicar exportando a folha de cálculo dos preços e revendo o preço em cada mercado.

#### <a name="free-trial"></a>Avaliação gratuita

Pode oferecer um teste gratuito de um mês ou três meses aos seus clientes.

#### <a name="visibility"></a>Visibilidade

Pode desenhar cada plano para ser visível para todos ou apenas para um público pré-selecionado. Atribuir membros neste público restrito usando IDs de subscrição Azure.

**Público** – O seu plano pode ser visto por todos.

**Público privado** – Torne o seu plano visível apenas para um público pré-selecionado. Depois de publicado como um plano privado, pode atualizar o público ou mudá-lo para público. Depois de tornar público um plano, deve permanecer público; Não pode mudá-lo de volta para privado.

**Audiência Restrita (IDs de subscrição Azure)** – Atribuir ao público que terá acesso a este plano privado utilizando IDs de subscrição Azure. Opcionalmente, inclua uma descrição de cada ID de subscrição Azure que atribuiu. Adicione até 10 IDs de subscrição manualmente ou 20.000 se importar uma folha de cálculo CSV. Os IDs de subscrição azure são representados como GUIDs e as letras devem ser minúsculas.

> [!NOTE]
> Um público privado ou restrito é diferente do público de pré-visualização que definiu no separador **Preview.** Um público pré-visualizado pode aceder à sua oferta _antes_ da sua publicação ao vivo no mercado. Embora a escolha do público privado se aplique apenas a um plano específico, o público pré-visualização pode ver todos os planos (privados ou não) para fins de validação.

#### <a name="hide-plan"></a>Plano de ocultação

Se a sua máquina virtual for destinada a ser utilizada apenas indiretamente quando referenciada através de outro modelo de solução ou aplicação gerida, selecione esta caixa para publicar a sua máquina virtual, mas esconda-a dos clientes que procuram e navegam diretamente para ela.

> [!NOTE]
> Planos escondidos não suportam links de pré-visualização.

Selecione **guardar rascunho** antes de continuar.

### <a name="technical-configuration"></a>Configuração técnica

Forneça as imagens e outras propriedades técnicas associadas a este plano. Para mais detalhes, consulte [Criar um ativo técnico Azure VM.](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-azure-container-technical-assets)

> [!NOTE]
> Este separador não é mostrado se configurar este plano para reutilizar pacotes de outro plano no separador **de configuração do Plano.**

#### <a name="operating-system"></a>Sistema Operativo

- **Família** do sistema operativo – Selecione a partir do **Windows** ou do sistema operativo **Linux**
- **Lançamento** ou **Fornecedor** - Escolha o lançamento do Windows ou o fornecedor Linux
- **Nome amigável do OS** – Escolha um nome amigável do sistema operativo. Este nome é visível para os clientes

#### <a name="recommended-vm-sizes"></a>Tamanhos VM recomendados

Selecione até seis tamanhos de máquina virtual recomendados para exibir no Azure Marketplace.

#### <a name="open-ports"></a>Abrir portas

Abrir portas públicas ou privadas numa máquina virtual implantada.

#### <a name="storage-option-for-deployment"></a>Opção de armazenamento para implementação

**Opção** de implementação do disco – Selecione que tipo de implantação de disco os seus utilizadores podem utilizar quando utilizar a máquina virtual. A Microsoft recomenda limitar a penas a implementação do disco gerido.

#### <a name="properties"></a>Propriedades

**Suporte a rede acelerada** – Selecione se o seu VM suporta [uma rede acelerada](https://go.microsoft.com/fwlink/?linkid=2124513).

#### <a name="vm-images"></a>Imagens de VM

Forneça uma versão em disco e o SAS URI para as imagens da máquina virtual. Adicione até 16 discos de dados para cada imagem VM. Forneça apenas uma nova versão de imagem por plano numa dada submissão. Depois de uma imagem ter sido publicada, não pode editá-la, mas pode apagá-la. A eliminação de uma versão impedirá os utilizadores novos e existentes de implementarem uma nova instância da versão eliminada.

- **A versão disc** é a versão da imagem que está a fornecer.
- **SAS URI** é a localização no Armazenamento Azure onde armazenou o sistema operativo VHD.
- As imagens do disco de dados também são VHD SAS URIs armazenadas no seu armazenamento Azure.
- Adicione apenas uma imagem por submissão num plano.

Independentemente do sistema operativo utilizado, adicione apenas o número mínimo de discos de dados necessários pela solução. Os clientes não podem remover discos que façam parte de uma imagem no momento da implantação, mas podem sempre adicionar discos durante ou após a implantação.

Selecione **Guardar rascunho** antes de continuar e voltar à **visão geral do Plano**.

## <a name="resell-through-csps"></a>Revenda através de CSPs

Expanda o alcance da sua oferta, disponibilizando-a aos parceiros do programa [Cloud Solution Providers](https://azure.microsoft.com/offers/ms-azr-0145p/) (CSP). Todos os planos Bring Your Own License (BYOL) são automaticamente optados; pode optar por optar pelos seus planos não BYOL.

Selecione **guardar rascunho** antes de continuar.

## <a name="test-drive"></a>Test drive

Configurar uma demonstração (test drive) que permite aos clientes experimentarem a sua oferta antes de a comprarem. Para criar um ambiente de demonstração que permita aos clientes experimentarem a sua oferta por um período de tempo fixo, consulte test [Drive a sua oferta no mercado comercial](https://docs.microsoft.com/azure/marketplace/partner-center-portal/test-drive).

Para ativar um test drive, selecione a caixa de verificação de test drive no separador [de configuração oferta.](#test-drive) Para remover o test drive da sua oferta, limpe esta caixa de verificação.

Recursos adicionais de test drive:

- Boas práticas técnicas
- Melhores práticas de marketing
- Visão geral do Test Drives (PDF; certifique-se de que o seu bloqueador pop-up está desligado).

Selecione **guardar rascunho** antes de continuar.

## <a name="review-and-publish"></a>Rever e publicar

Depois de ter completado todas as secções necessárias da oferta, pode submetê-la para rever e publicar.

No canto superior direito do portal, selecione **Rever e publique**.

Nesta página pode:

- Consulte o estado de conclusão de cada secção da oferta.
  - **Não iniciado** – A secção ainda não foi iniciada e precisa de ser concluída.
  - **Incompleto** – A secção tem erros que devem ser corrigidos ou exigem que forneça mais informações. Consulte as secções anteriores neste documento para obter orientações sobre como atualizar esta secção.
  - **Completo** – A secção está completa e não há erros. Todas as secções da oferta devem estar completas antes de poder submeter a oferta.
- **Notas para certificação** – Forneça instruções de teste à equipa de certificação para garantir que a sua aplicação é testada corretamente. Forneça quaisquer notas suplementares úteis para a compreensão da sua aplicação.

Para submeter a oferta para publicação, selecione **Review e publique**.

Enviaremos um e-mail para informá-lo quando uma versão de pré-visualização da oferta está disponível para rever e aprovar. Para publicar a sua oferta ao público (ou se uma oferta privada, para um público privado), vá ao Partner Center, localize a página de **visão geral** da sua oferta e selecione **Go-live**. O estado da sua oferta aparecerá no topo da página quando a publicação estiver em andamento.

### <a name="errors-and-review-feedback"></a>Erros e feedback de revisão

A etapa de **validação manual** no processo de publicação representa uma revisão extensiva da sua oferta e dos seus ativos técnicos associados. Se este processo desvendar erros com a sua oferta, receberá um relatório de certificação que detalha os problemas. Basta fazer as correções necessárias e reeditar a sua oferta.

## <a name="offer-overview"></a>Visão geral da oferta

A página geral da **Oferta** mostra uma representação visual dos passos necessários para publicar esta oferta (completa e em curso) e quanto tempo cada passo deve demorar a ser concluído.

Esta página inclui links para realizar operações nesta oferta com base na seleção que fizer. Por exemplo:

- Se a oferta for um rascunho - [Eliminar a oferta](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#delete-a-draft-offer) de rascunho
- Se a oferta estiver ao vivo - [Pare de vender a oferta](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#stop-selling-an-offer-or-plan)
- Se a oferta estiver em pré-visualização - [Go-live](https://docs.microsoft.com/azure/marketplace/partner-center-portal/publishing-status#publisher-approval)
- Se ainda não tiver concluído a inscrição da editora - [Cancele a publicação](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#cancel-publishing)

## <a name="marketplace-examples"></a>Exemplos de mercado

Estes exemplos mostram como a oferta aparece no Azure Marketplace.

### <a name="azure-marketplace-offer-details"></a>Azure Marketplace oferece detalhes

![Azure Marketplace oferece exemplo de ecrã de detalhes](media/avm-create1.png)

### <a name="azure-marketplace-search-results"></a>Resultados da pesquisa do Azure Marketplace

![O exemplo da pesquisa do Azure Marketplace detalha o exemplo do ecrã](media/avm-create2.png)

### <a name="azure-marketplace-plan-details"></a>Detalhes do plano Azure Marketplace

![Plano azure Marketplace detalha exemplo de ecrã](media/avm-create3.png)

### <a name="azure-portal-offer-details"></a>Portal Azure oferece detalhes

![Portal Azure oferece detalhes exemplo de tela](media/avm-create4.png)

### <a name="azure-portal-search-results"></a>Resultados da pesquisa do portal Azure

![Exemplo de rastreio de resultados de pesquisa do portal Azure](media/avm-create5.png)

### <a name="azure-portal-plan-details"></a>Detalhes do plano do portal Azure

![Plano do portal Azure detalha exemplo de ecrã](media/avm-create6.png)

## <a name="next-step"></a>Passo seguinte

- [Atualizar uma oferta existente no mercado comercial](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer)
