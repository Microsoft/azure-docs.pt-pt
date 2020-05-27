---
title: Criar uma oferta de máquina virtual Azure no Azure Marketplace
description: Aprenda a criar uma oferta de máquina virtual no Azure Marketplace com o SKU necessário.
author: emuench
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 05/19/2020
ms.openlocfilehash: 957d7911a1373504265c014e3ba141c150e60c2a
ms.sourcegitcommit: cf7caaf1e42f1420e1491e3616cc989d504f0902
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/22/2020
ms.locfileid: "83800571"
---
# <a name="create-an-azure-virtual-machine-offer-on-azure-marketplace"></a>Criar uma oferta de máquina virtual Azure no Azure Marketplace

Este artigo descreve como criar e publicar uma oferta de máquina virtual Azure ao [Azure Marketplace.](https://azuremarketplace.microsoft.com/) Dirige-se a máquinas virtuais baseadas no Windows e ao Linux que contêm um sistema operativo, um disco rígido virtual (VHD) e até 16 discos de dados. 

Antes de começar, [Crie uma conta de marketplace comercial no Partner Center.](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-account) Certifique-se de que a sua conta está inscrita no programa de marketplace comercial.

## <a name="introduction"></a>Introdução

### <a name="the-benefits-of-publishing-to-azure-marketplace"></a>Os benefícios da publicação no Azure Marketplace

Ao publicar as suas ofertas no Azure Marketplace, pode:

- Promova a sua empresa com a ajuda da marca Microsoft.
- Alcance mais de 100 milhões de utilizadores do Office 365 e da Dynamics 365 e mais de 200.000 organizações.
- Obtenha pistas de alta qualidade destes mercados.
- Promotem os vossos serviços promovidos pelas equipas de vendas e televendas do campo da Microsoft.

### <a name="before-you-begin"></a>Antes de começar

Se ainda não o fez, reveja a oferta da [máquina Virtual](https://docs.microsoft.com/azure/marketplace/marketplace-virtual-machines) e este material azure virtual machine:

- Guias quickstart
  - [Modelos de quickstart azure](https://azure.microsoft.com/resources/templates/)
  - [Modelos de arranque rápido GitHub Azure](https://github.com/azure/azure-quickstart-templates)
- Tutoriais
  - [VMs do Linux](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-manage-vm)
  - [VMs do Windows](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-manage-vm)
- Amostras
  - [Amostras azure CLI para VMs Linux](https://docs.microsoft.com/azure/virtual-machines/linux/cli-samples)
  - [Azure PowerShell para VMs Linux](https://docs.microsoft.com/azure/virtual-machines/linux/powershell-samples)
  - [Amostras azure CLI para VMs do Windows](https://docs.microsoft.com/azure/virtual-machines/windows/cli-samples)
  - [Azure PowerShell para VMs windows](https://docs.microsoft.com/azure/virtual-machines/scripts/virtual-machines-windows-powershell-sample-create-vm-quick)

### <a name="fundamentals-in-technical-knowledge"></a>Fundamentos do conhecimento técnico

O processo de conceção, construção e ofertas de teste leva tempo e requer experiência tanto na plataforma Azure como nas tecnologias que são usadas para construir a sua oferta.

A sua equipa de engenharia deve ter um conhecimento básico de compreensão e trabalho das seguintes tecnologias da Microsoft:

- [Serviços do Azure](https://azure.microsoft.com/services/)
- [Design e arquitetura de aplicações Azure](https://azure.microsoft.com/solutions/architecture/)
- [Máquinas Virtuais Azure,](https://azure.microsoft.com/services/virtual-machines/) [Armazenamento Azure](https://azure.microsoft.com/services/?filter=storage#storage)e [Networking Azure](https://azure.microsoft.com/services/?filter=networking#networking)

## <a name="create-a-new-offer"></a>Criar uma nova oferta

1. Inscreva-se no [Partner Center](https://partner.microsoft.com/dashboard/home).
2. No painel esquerdo, selecione Visão Geral do **Mercado Comercial**  >  **Overview**.
3. Na página **Overview,** selecione **New offer**  >  **Azure Virtual Machine**.

    ![Screenshot mostrando as opções do menu do painel esquerdo e o botão "Nova oferta".](./media/new-offer-azure-vm.png)

> [!NOTE]
> Depois de publicada a sua oferta, quaisquer edições que fizer no Partner Center só aparecem no Mercado De Parceria somente após a republicação da oferta. Certifique-se de que republique sempre uma oferta depois de fazer alterações.

## <a name="new-offer"></a>Nova oferta

Introduza um **ID de oferta**. Este é um identificador único para cada oferta na sua conta.

- Este ID é visível para os clientes no endereço web para a oferta do Azure Marketplace e no Azure PowerShell e no Azure CLI, se aplicável.
- Utilize apenas letras minúsculas e números. O ID pode incluir hífenes e sublinhados, mas sem espaços, e está limitado a 50 caracteres. Por exemplo, se introduzir o **test-offer-1,** o endereço web da oferta será *https://azuremarketplace.microsoft.com/marketplace/../test-offer-1* .
- O ID da Oferta não pode ser alterado depois de selecionar **Criar**.

Insira um **pseudónimo da Oferta.** O pseudónimo de oferta é o nome que é usado para a oferta no Partner Center.

- Este nome não é usado no Azure Marketplace. É diferente do nome da oferta e de outros valores que são mostrados aos clientes.

Selecione **Criar** para gerar a oferta e continuar.

## <a name="offer-setup"></a>Configuração de oferta

### <a name="test-drive"></a>Versão de Teste

Um *Test Drive* é uma ótima maneira de mostrar a sua oferta a potenciais clientes. Dá-lhes a opção de "tentar antes de comprar", o que pode ajudar a aumentar as suas conversões e gerar leads altamente qualificados. Para mais informações, veja [o que é um Test Drive?](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive)

Para ativar um Test Drive durante um período de tempo fixo, selecione a caixa de verificação **de test drive.** Para remover o Test Drive da sua oferta, limpe a caixa de verificação.

Recursos adicionais de test drive:

- [Boas práticas técnicas](https://github.com/Azure/AzureTestDrive/wiki/Test-Drive-Best-Practices)
- [Melhores práticas de marketing](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/marketing-and-best-practices)
- [Descarregue a visão geral do Test Drive](https://assetsprod.microsoft.com/mpn/azure-marketplace-appsource-test-drives.pdf) Ficheiro PDF (certifique-se de que o seu bloqueador pop-up está desligado).

### <a name="lead-management"></a>Gestão de oportunidades potenciais

Quando publicar a sua oferta para o mercado comercial com o Partner Center, conecte-a ao seu sistema de Gestão de Relacionamento com o Cliente (CRM). Isto permite-lhe receber informações de contacto do cliente assim que alguém expressa interesse ou utiliza o seu produto. É necessária uma ligação a um CRM se pretender ativar uma Unidade de Teste (ver a secção anterior). Caso contrário, a ligação a um CRM é opcional.

1. Selecione um destino de oportunidades potenciais para onde quer que enviemos as oportunidades potenciais de clientes. Partner Center suporta os seguintes sistemas CRM:
    - [Dinâmica 365](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics) para envolvimento do cliente
    - [Marketo](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-marketo)
    - [Salesforce](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-salesforce)

    > [!NOTE]
    > Se o seu sistema CRM não estiver listado aqui, utilize o [armazenamento da Tabela Azure](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-azure-table) ou um [ponto final HTTPS](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-https) para armazenar os dados de chumbo do seu cliente. Em seguida, exporte os dados para o seu sistema crm.

1. Conecte a sua oferta ao destino principal quando estiver a publicar no Partner Center.
1. Confirme se a ligação ao destino principal está corretamente configurada. Depois de publicá-lo no Partner Center, a Microsoft valida a ligação e envia-lhe um teste. Enquanto está a visualizar a oferta antes de ser transmitida ao vivo, também pode testar a sua ligação de chumbo tentando implementar a oferta no ambiente de pré-visualização.
1. Certifique-se de que a ligação ao destino principal permanece atualizada para que não perca nenhuma pista.

1. Selecione **Guardar rascunho** antes de continuar.

## <a name="properties"></a>Propriedades

Na página **Propriedades,** define as categorias e indústrias que estão habituadas a agrupar a sua oferta no Azure Marketplace, na sua versão de candidatura e nos contratos legais que suportam a sua oferta.

### <a name="categories"></a>Categorias

Selecione um mínimo de uma e um máximo de cinco categorias. Utiliza estas categorias para colocar a sua oferta nas áreas de pesquisa apropriadas do Azure Marketplace. Na descrição da oferta, explique como a sua oferta suporta estas categorias. As ofertas de máquinas virtuais aparecem na categoria **Compute** no Azure Marketplace.

### <a name="legal"></a>Legal

Deve fornecer condições e condições de oferta aos seus clientes. Tem duas opções:

- **Use os seus próprios termos e condições**

   Para fornecer os seus próprios termos e condições personalizados, insira até 10.000 caracteres de texto na caixa **de Termos e Condições.** Se necessitar de uma descrição mais longa, insira um único endereço web para os seus termos e condições. Será apresentado aos clientes como um link ativo.

   Os seus clientes devem aceitar estes termos antes de poderem experimentar a sua oferta.

- **Utilize o Contrato Padrão para o mercado comercial da Microsoft**

   Para simplificar o processo de aquisição para os clientes e reduzir a complexidade jurídica para os fornecedores de software, a Microsoft oferece um Contrato Padrão para o mercado comercial. Quando oferece o seu software ao abrigo do Contrato Padrão, os clientes precisam de o ler e aceitar apenas uma vez, e não tem de criar termos e condições personalizados.

   Utilize o Contrato Padrão selecionando o Contrato Padrão para a caixa de verificação de **mercado comercial da Microsoft** e, em seguida, na janela pop-up, selecione **Accept** (poderá ter de deslocar-se para cima para o ver).

   ![Screenshot mostrando o painel legal no Partner Center com a caixa de verificação "Use o contrato padrão para o mercado comercial da Microsoft".](media/use-standard-contract.png)

  > [!NOTE]
  > Depois de publicar uma oferta utilizando o Contrato Padrão para o mercado comercial, não pode utilizar os seus próprios termos e condições personalizados. Pode oferecer uma solução ao abrigo do Contrato Padrão ou dos seus próprios termos e condições.

  Para mais informações, consulte [o Standard Contract para o mercado comercial da Microsoft.](https://docs.microsoft.com/azure/marketplace/standard-contract) Descarregue o ficheiro [PDF do Contrato Padrão](https://go.microsoft.com/fwlink/?linkid=2041178) (certifique-se de que o seu bloqueador pop-up está desligado).

  **Alterações ao Contrato Padrão**

  As alterações do Contrato Padrão permitem-lhe selecionar os termos do Contrato Padrão para a simplicidade e criar os termos para o seu produto ou negócio. Os clientes só precisam de rever as alterações ao contrato se já tiverem revisto e aceite o Contrato Padrão da Microsoft. Existem dois tipos de alterações:

  * **Alterações universais**: Estas alterações são aplicadas universalmente ao Contrato Padrão para todos os clientes. São mostrados a todos os clientes da oferta no fluxo de compra. Os clientes devem aceitar os termos do Contrato Padrão e as alterações antes de poderem utilizar a sua oferta. Pode fornecer uma única emenda universal por oferta. Pode introduzir um número ilimitado de caracteres nesta caixa. Estes termos são apresentados aos clientes no AppSource, Azure Marketplace e/ou no portal Azure durante o fluxo de descoberta e compra.

  * **Alterações personalizadas**: Estas alterações especiais ao Contrato Padrão destinam-se a clientes específicos através de IDs de inquilinos Azure. Pode escolher o inquilino que quiser. Apenas os clientes do inquilino são apresentados com os termos de alteração personalizados no fluxo de compra da oferta. Os clientes devem aceitar os termos do Contrato Padrão e as alterações antes de poderem utilizar a sua oferta.

    1. Comece por selecionar Termos de **alteração personalizados (Max 10)**. Você pode fornecer até dez termos de emenda salfatopor oferta. Faça o seguinte:

       a. Insira os seus próprios termos de emenda na caixa de termos de **emenda personalizada.** Pode introduzir um número ilimitado de caracteres. Apenas os clientes das IDs do inquilino que especifica para estes termos personalizados irão vê-los no fluxo de compra da oferta no portal Azure.

       b. (Obrigatório) Forneça **iDs de inquilino.** Cada emenda personalizada pode ser direcionada a até 20 iDs de inquilino. Se adicionar uma emenda personalizada, deve fornecer pelo menos uma identificação de inquilino, que identifica o seu cliente em Azure. O seu cliente pode encontrar isso para si em Azure, selecionando propriedades de **diretório sinuoso Azure Ative**  >  **Properties**. O valor de ID do diretório é o ID do inquilino (por exemplo, 50c464d3-4930-494c-963c-1e951d15360e). Também pode encontrar a identificação do seu cliente com o seu nome de domínio no Qual é o meu Microsoft Azure e o [Office 365 tenant ID?](https://www.whatismytenantid.com/)

       c. (Opcional) Forneça uma **descrição** amigável para o ID do inquilino, que o ajude a identificar o cliente que está a visar com a emenda.

        > [!NOTE]
        > Estes dois tipos de alterações são emparelhados entre si. Os clientes visados com alterações personalizadas também receberão as alterações universais ao Contrato Padrão durante a compra.

    1. Selecione **Guardar rascunho** antes de continuar.

## <a name="offer-listing"></a>Oferta listagem

Na página de **listagem de oferta,** define os detalhes da oferta, tais como nome de oferta, descrição, links e contactos.

> [!NOTE]
> A sua oferta de conteúdo de listagem, como a descrição, documentos, imagens e termos de utilização, não tem de estar em inglês, desde que a descrição da oferta comece com a frase: "Esta aplicação só está disponível em \<> de língua não inglesa." Também pode fornecer um URL para ligar a um site que oferece conteúdo num idioma diferente daquele que é usado no conteúdo de listagem de ofertas.

### <a name="marketplace-details"></a>Detalhes do mercado

#### <a name="name"></a>Name

O nome que insere aqui é mostrado aos clientes como título da sua listagem de oferta. Este campo é preenchido automaticamente com o nome que inseriu na caixa **de pseudónimos Offer** quando criou a oferta. Pode alterar este nome posteriormente. O nome:

- Pode ser marcado. Pode incluir símbolos de marca e direitos de autor.
- Não pode conter mais de 50 caracteres.
- Não posso incluir emojis.

#### <a name="search-results-summary"></a>Resumo dos resultados da pesquisa

Forneça uma breve descrição da sua oferta, a exibir nos resultados de pesquisa do Azure Marketplace. Pode conter até 100 caracteres.

#### <a name="long-summary"></a>Resumo longo

Forneça uma descrição mais longa da sua oferta, a apresentar nos resultados de pesquisa do Azure Marketplace. Pode conter até 256 caracteres.

#### <a name="description"></a>Descrição

[!INCLUDE [Long description-1](./includes/long-description-1.md)]

[!INCLUDE [Long description-2](./includes/long-description-2.md)]

[!INCLUDE [Long description-3](./includes/long-description-3.md)]

#### <a name="privacy-policy-link"></a>Ligação política de privacidade

Introduza o endereço web (URL) da política de privacidade da sua organização. Certifique-se de que a sua oferta está em conformidade com as leis e regulamentos de privacidade. Também deve publicar uma política de privacidade válida no seu website.

### <a name="useful-links"></a>Ligações úteis

Forneça documentos online suplementares sobre a sua oferta. Para adicionar um link, selecione **Adicionar um link**, e, em seguida, completar os seguintes campos:

- **Nome**: Os clientes verão o nome na página de detalhes.
- **Link (URL)**: Introduza um link que permita aos clientes visualizarem o seu documento online.

### <a name="customer-support-links"></a>Links de apoio ao cliente

Forneça o site de suporte onde os clientes podem chegar à sua equipa de suporte.

- Site de apoio azure global
- Site de apoio do Governo Azure

### <a name="partner-support-contact"></a>Contacto de apoio ao parceiro

Forneça informações de contacto para os parceiros da Microsoft usarem quando os seus clientes abrirem um bilhete de suporte. Esta informação não está listada no Azure Marketplace.

- Name
- E-mail
- Telefone

### <a name="engineering-contact"></a>Contacto de engenharia

Forneça informações de contacto para a Microsoft usar quando houver problemas com a sua oferta, incluindo problemas com certificação. Esta informação não está listada no Azure Marketplace.

- Name
- E-mail
- Telefone

### <a name="azure-marketplace-media"></a>Mídia Azure Marketplace

Forneça logotipos e imagens para usar com a sua oferta. Todas as imagens devem estar em formato PNG. Imagens desfocadas farão com que a sua submissão seja rejeitada.

>[!Note]
>Se tiver algum problema de upload de ficheiros, certifique-se de que a sua rede local não bloqueia o *https://upload.xboxlive.com* serviço utilizado pelo Partner Center.

#### <a name="azure-marketplace-logos"></a>Logotipos do Azure Marketplace

Forneça ficheiros PNG do logótipo da sua oferta com as seguintes quatro dimensões de imagem:

- **Pequeno** (48 &times; 48 pixels)
- **Médio** (90 &times; 90 pixels)
- **Grande** (216 &times; 216 pixels)
- **Largo** (255 &times; 115 pixels)

Todos os quatro logótipos são necessários, e são exibidos em várias listas do Azure Marketplace.

#### <a name="screenshots"></a>Capturas de ecrã

Adicione cinco imagens que mostram como a sua oferta funciona. Cada screenshot deve ter 1280 &times; 720 pixels de tamanho e em formato PNG. Cada imagem deve incluir uma legenda.

#### <a name="videos"></a>Vídeos

Adicione cinco vídeos que demonstram a sua oferta. Os vídeos devem ser hospedados num serviço de vídeo externo. Introduza o nome de cada vídeo, endereço web e uma imagem PNG miniatura do vídeo em 1280 &times; 720 pixels.

Para obter recursos adicionais de listagem de mercado, consulte [as melhores práticas para anúncios](https://docs.microsoft.com/azure/marketplace/gtm-offer-listing-best-practices)de oferta de mercado.

Selecione **Guardar rascunho** antes de continuar.

## <a name="preview"></a>Pré-visualizar

Selecione o separador **Preview** e, em seguida, selecione um Público de **Pré-visualização** limitado para validar a sua oferta antes de publicá-la ao vivo para o público mais amplo do mercado comercial.

> [!IMPORTANT]
> Depois de verificar a sua oferta no painel **preview,** selecione **Ir ao vivo** para publicar a sua oferta para o público do mercado comercial.

O seu público de pré-visualização é identificado por GUIDs de assinatura Azure, juntamente com uma descrição opcional para cada um. Nenhum destes campos pode ser visto pelos clientes. Pode encontrar o seu ID de subscrição Azure na página **de Subscrições** no portal Azure.

Adicione pelo menos um ID de subscrição Azure, individualmente (até 10 IDs) ou carregando um ficheiro CSV (até 100 IDs). Ao adicionar estes IDs de subscrição, define quem pode pré-visualizar a sua oferta antes de ser publicado ao vivo. Se a sua oferta já estiver ao vivo, ainda pode definir um público de pré-visualização para testar alterações ou atualizações da sua oferta.

> [!NOTE]
> Um público de pré-visualização difere de um público privado. Um público pré-visualizado pode aceder à sua oferta _antes_ de ser publicada ao vivo no Azure Marketplace. O público de pré-visualização pode ver e validar todos os planos, incluindo aqueles que estarão disponíveis apenas para um público privado depois da sua oferta ser totalmente publicada no Azure Marketplace. Um público privado (definido no painel de **preços e disponibilidade** do plano) tem acesso exclusivo a um determinado plano.

Selecione **Guardar rascunho** antes de passar para a próxima secção.

## <a name="plan-overview"></a>Visão geral do plano

Você pode fornecer uma variedade de opções de plano dentro da mesma oferta no Partner Center. Estes planos eram anteriormente referidos como SKUs. Uma oferta requer pelo menos um plano, que pode variar por audiência de monetização, região azure, características ou imagens VM.

Depois de criar os seus planos, selecione o separador de **visão geral** do Plano para visualizar:

- Nomes de planos
- Modelos de licença
- Público (público ou privado)
- Estado editorial atual
- Ações disponíveis

As ações disponíveis no painel de **visão geral** do Plano variam consoante o estado atual do seu plano.

- Se o estado do plano for um rascunho, **selecione Eliminar o projeto**.
- Se o estado do plano for publicado ao vivo, selecione **Stop sell plan** ou Sync private **audience**.

### <a name="create-a-new-plan"></a>Criar um novo plano

Selecione **Criar um novo plano** no topo. A caixa de diálogo **do novo plano** aparece.

Na caixa de ID do **Plano,** crie um plano único de ID para cada plano nesta oferta. Este ID será visível para os clientes no endereço web do produto. Utilize apenas letras minúsculas e números, traços ou sublinhados, e um máximo de 50 caracteres.

> [!NOTE]
> O ID do plano não pode ser alterado depois de selecionar **Criar**.

Na caixa de **nome** sinuosa, insira um nome para este plano. Os clientes vêem este nome quando estão a decidir qual o plano a selecionar dentro da sua oferta. Crie um nome único que aponte claramente as diferenças entre os planos. Por exemplo, pode introduzir o **Windows Server** com planos *Pay-as-you-go,* *BYOL,* *Advanced*e *Enterprise.*

Selecione **Criar**.

### <a name="plan-setup"></a>Configuração do plano

Detete a configuração de alto nível para o tipo de plano, especifique se reutiliza uma configuração técnica de outro plano e identifique as regiões de Azure onde o plano deve estar disponível. As suas seleções determinam quais os campos que são exibidos em outros paineles para o mesmo plano.

#### <a name="reuse-a-technical-configuration"></a>Reutilizar uma configuração técnica

Se tiver mais de um plano do mesmo tipo e os pacotes forem idênticos entre eles, pode selecionar **Este plano reutiliza a configuração técnica de outro plano**. Esta opção permite selecionar um dos outros planos do mesmo tipo para esta oferta e permite-lhe reutilizar a sua configuração técnica.

> [!NOTE]
> Quando reutiliza a configuração técnica de outro plano, todo o separador de **configuração técnica** desaparece deste plano. Os detalhes técnicos de configuração do outro plano, incluindo quaisquer atualizações que fizer no futuro, também serão utilizados para este plano. Esta definição não pode ser alterada após a publicação do plano.

#### <a name="azure-regions"></a>Regiões do Azure

O seu plano deve ser disponibilizado em pelo menos uma região de Azure.

Selecione a opção **Azure Global** para disponibilizar o seu plano aos clientes de todas as regiões da Azure Global que tenham integração no mercado comercial. Para mais informações, consulte disponibilidade [geográfica e suporte cambial.](https://docs.microsoft.com/azure/marketplace/marketplace-geo-availability-currencies)

Selecione a opção **do Governo Azure** para disponibilizar o seu plano na região do [Governo de Azure.](https://docs.microsoft.com/azure/azure-government/documentation-government-welcome) Esta região fornece acesso controlado a clientes de entidades federais, estaduais, locais ou tribais dos EUA, bem como a parceiros elegíveis para os servir. Você, como editor, é responsável por quaisquer controlos de conformidade, medidas de segurança e boas práticas. O Governo azure utiliza centros de dados e redes fisicamente isolados (localizados apenas nos EUA).

Antes de publicar ao [Governo Azure](https://docs.microsoft.com/azure/azure-government/documentation-government-manage-marketplace-partners), teste e valide o seu plano no ambiente, porque certos pontos finais podem diferir. Para configurar e testar o seu plano, solicite uma conta de teste na página de [teste do Governo do Microsoft Azure.](https://azure.microsoft.com/global-infrastructure/government/request/)

> [!NOTE]
> Depois de o seu plano ser publicado e disponível numa região específica de Azure, não pode remover essa região.

#### <a name="azure-government-certifications"></a>Certificações do Governo Azure

Esta opção só é visível se selecionar o **Governo Azure** como região azure na secção anterior.

Os serviços do Governo Azure tratam de dados que estão sujeitos a certos regulamentos e requisitos governamentais. Por exemplo, FedRAMP, NIST 800.171 (DIB), ITAR, IRS 1075, DoD L4 e CJIS. Para sensibilizar as suas certificações para estes programas, pode fornecer até 100 links que os descrevem. Estes podem ser links para a sua listagem no programa diretamente ou links para descrições da sua conformidade com os mesmos nos seus próprios websites. Estas ligações são visíveis apenas para os clientes do Governo Azure.

Selecione **Guardar rascunho** antes de continuar.

### <a name="plan-listing"></a>Listagem de planos

Nesta secção, configura os detalhes da listagem do plano. Este painel apresenta informações específicas, que podem diferir de outros planos na mesma oferta.

#### <a name="plan-name"></a>Nome do plano

Este campo está preenchido com o nome que deu ao seu plano quando o criou. Este nome aparece no Azure Marketplace como o título deste plano. Está limitado a 100 caracteres.

#### <a name="plan-summary"></a>Resumo do plano

Forneça um breve resumo do seu plano, não a oferta. Este resumo está limitado a 100 caracteres.

#### <a name="plan-description"></a>Descrição do plano

Descreva o que torna este plano de software único e descreva quaisquer diferenças entre os planos dentro da sua oferta. Descreva o plano apenas, não a oferta. A descrição do plano pode conter até 2.000 caracteres.

Selecione **Guardar rascunho** antes de continuar.

### <a name="pricing-and-availability"></a>Preços e disponibilidade

Neste painel, configura-se:

- Mercados onde este plano está disponível.
- O preço por hora.
- Quer torne o plano visível para todos ou apenas para clientes específicos (um público privado).

#### <a name="markets"></a>Mercados

Todos os planos devem estar disponíveis em pelo menos um mercado. Selecione a caixa de verificação para cada local do mercado onde este plano deve estar disponível para compra. (Os utilizadores nestes mercados ainda podem implementar a oferta a todas as regiões do Azure selecionadas na secção ["Plan setup".)](#plan-setup) O botão **Tax Remitted** mostra países/regiões em que a Microsoft atribui as vendas e utiliza o imposto em seu nome. A publicação na China limita-se a planos que sejam *gratuitos* ou *de "Bring-your-own-license"* (BYOL).

Se já definiu preços para o seu plano em moeda de dólar norte-americano (USD) e adiciona outra localização de mercado, o preço do novo mercado é calculado de acordo com as taxas de câmbio atuais. Reveja sempre o preço de cada mercado antes de publicar. Reveja os seus preços selecionando preços de **exportação (xlsx)** depois de guardar as suas alterações.

Quando se remove um mercado, os clientes desse mercado que utilizam implementações ativas não poderão criar novas implementações ou aumentar as suas implementações existentes. As implantações existentes não são afetadas.

#### <a name="pricing"></a>Preços

Para o **modelo Licença**, selecione plano mensal **de faturação baseado em Utilização** para configurar preços para este plano, ou selecione Trazer a **sua própria licença** para permitir que os clientes utilizem este plano com a sua licença existente.

Para um plano mensal de faturação baseado na utilização, utilize uma das seguintes três opções de entrada de preços:

- **Por núcleo**: Fornecer preços por núcleo em USD. A Microsoft calcula os preços por tamanho central e converte-o em moedas locais utilizando a taxa de câmbio atual.
- **Por tamanho do núcleo**: Forneça preços por tamanho do núcleo em USD. A Microsoft calcula os preços e converte-o em moedas locais utilizando a taxa de câmbio atual.
- **Por mercado e dimensão central**: Forneça preços para cada tamanho central para todos os mercados. Pode importar os preços de uma folha de cálculo.

> [!NOTE]
> Poupe alterações de preços para permitir a exportação de dados de preços. Depois de um preço para um mercado no seu plano ser publicado, não pode ser alterado mais tarde. Para garantir que os preços estão certos antes de os publicar, exporte a folha de cálculo dos preços e reveja os preços em cada mercado.

#### <a name="free-trial"></a>Avaliação Gratuita

Pode oferecer um *teste gratuito* de um mês ou três meses aos seus clientes.

#### <a name="visibility"></a>Visibilidade

Pode desenhar cada plano para ser visível para todos ou apenas para um público pré-selecionado. Atribuir membros neste público restrito utilizando IDs de subscrição Azure.

**Público**: O seu plano pode ser visto por todos.

**Público privado**: Tornar o seu plano visível apenas para um público pré-selecionado. Depois de publicado como um plano privado, pode atualizar o público ou mudá-lo para público. Depois de tornar um plano público, deve permanecer público. Não pode ser mudado de volta para um plano privado.

**Audiência restrita (IDs de subscrição Azure)**: Atribuir o público que terá acesso a este plano privado utilizando IDs de subscrição Azure. Opcionalmente, inclua uma descrição de cada ID de subscrição Azure que você atribuiu. Adicione até 10 IDs de subscrição manualmente ou até 20.000 IDs se estiver a importar uma folha de cálculo do CSV. Os IDs de subscrição azure são representados como GUIDs, e todas as letras devem ser minúsculas.

> [!NOTE]
> Um público privado ou restrito é diferente do público de pré-visualização que definiu no painel **de pré-visualização.** Um público pré-visualizado pode aceder à sua oferta _antes_ de ser publicada em direto para o Azure Marketplace. Embora a escolha do público privado se aplique apenas a um plano específico, o público pré-visualização pode ver todos os planos privados e públicos para fins de validação.

#### <a name="hide-a-plan"></a>Esconda um plano

Se a sua máquina virtual for destinada a ser usada apenas indiretamente quando for referenciada através de outro modelo de solução ou aplicação gerida, selecione esta caixa de verificação para publicar a máquina virtual, mas esconda-a de clientes que possam estar a pesquisar ou a navegar diretamente para ela.

> [!NOTE]
> Planos escondidos não suportam links de pré-visualização.

Selecione **Guardar rascunho** antes de continuar.

### <a name="technical-configuration"></a>Configuração técnica

Forneça as imagens e outras propriedades técnicas que estejam associadas a este plano. Para mais informações, consulte [Criar um ativo técnico Azure VM.](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-azure-container-technical-assets)

> [!NOTE]
> O separador de **configuração técnica** não é apresentado se configurar este plano para reutilizar pacotes de outro plano no separador **de configuração do Plano.**

#### <a name="operating-system"></a>Sistema operativo

No painel do **sistema operativo,** faça o seguinte:

- Para **a família do sistema operativo,** selecione o sistema operativo **Windows** ou **Linux.**
- Para **lançamento** ou **fornecedor,** selecione o lançamento do Windows ou o fornecedor Linux.
- Para um nome amigável para **os SISTEMAS,** introduza um nome de sistema operativo amigável. Este nome é visível para os clientes.

#### <a name="recommended-vm-sizes"></a>Tamanhos VM recomendados

Selecione até seis tamanhos de máquina virtual recomendados para exibir no Azure Marketplace.

#### <a name="open-ports"></a>Abrir portas

Abrir portas públicas ou privadas numa máquina virtual implantada.

#### <a name="storage-option-for-deployment"></a>Opção de armazenamento para implementação

Para a opção de **implementação do Disco,** selecione o tipo de implantação do disco que os seus clientes podem utilizar para a máquina virtual. A Microsoft recomenda limitar a penas a implementação do **disco gerido.**

#### <a name="properties"></a>Propriedades

Para **suporte a ligação acelerada,** selecione se o seu VM suporta [a Rede Acelerada](https://go.microsoft.com/fwlink/?linkid=2124513).

#### <a name="vm-images"></a>Imagens de VM

Forneça uma versão em disco e a assinatura de acesso partilhado (SAS) URI para as imagens da máquina virtual. Adicione até 16 discos de dados para cada imagem VM. Forneça apenas uma nova versão de imagem por plano numa submissão especificada. Depois de uma imagem ter sido publicada, não pode editá-la, mas pode apagá-la. A eliminação de uma versão impede que os utilizadores novos e existentes implementem uma nova instância da versão eliminada.

- **Versão disco**: A versão da imagem que está a fornecer.
- **SAS URI**: A localização na sua conta de armazenamento Azure onde armazenou o sistema operativo VHD.
- As imagens de disco de dados são também URIs de assinatura de acesso partilhado VHD que são armazenados nas suas contas de armazenamento Azure.
- Adicione apenas uma imagem por submissão num plano.

Independentemente do sistema operativo que utilize, adicione apenas o número mínimo de discos de dados que a solução necessita. Durante a implementação, os clientes não podem remover discos que fazem parte de uma imagem, mas podem sempre adicionar discos durante ou após a implementação.

Selecione **Guardar rascunho** antes de continuar e voltar à **visão geral**do Plano .

## <a name="resell-through-csps"></a>Revenda através de CSPs

Expanda o alcance da sua oferta, disponibilizando-a aos parceiros do programa [Cloud Solution Provider (CSP).](https://azure.microsoft.com/offers/ms-azr-0145p/) Todos os planos bring-your-your-license (BYOL) são automaticamente optados pelo programa. Também pode optar por optar pelos seus planos não BYOL.

Selecione **Guardar rascunho** antes de continuar.

## <a name="test-drive"></a>Versão de Teste

Configurar uma demonstração, ou *Test Drive,* que permite aos clientes experimentarem a sua oferta por um período de tempo fixo antes de a comprarem. Para criar um ambiente de demonstração para os seus clientes, consulte [as ofertas do Test Drive no mercado comercial.](https://docs.microsoft.com/azure/marketplace/partner-center-portal/test-drive)

Para ativar um Test Drive, selecione a caixa de verificação **de test drive** no painel de **configuração da Oferta.** Para remover o Test Drive da sua oferta, limpe a caixa de verificação.

Recursos adicionais de test drive:

- [Melhores práticas de marketing](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/marketing-and-best-practices)
- [Boas práticas técnicas](https://github.com/Azure/AzureTestDrive/wiki/Test-Drive-Best-Practices)
- [Visão geral do Test Drive](https://assetsprod.microsoft.com/mpn/azure-marketplace-appsource-test-drives.pdf) Ficheiro PDF (certifique-se de que o seu bloqueador pop-up está desligado)

Selecione **Guardar rascunho** antes de continuar.

## <a name="review-and-publish"></a>Rever e publicar

Depois de ter completado todas as secções necessárias da oferta, pode submetê-la para revisão e publicação.

Na parte superior direita, selecione **Rever e publicar**.

Neste painel pode:

- Consulte o estado de conclusão de cada secção da oferta:

  - **Não iniciado**: A secção não foi iniciada e precisa de ser concluída.
  - **Incompleto**: A secção tem erros que devem ser corrigidos ou exigem que forneça mais informações. Para obter orientações sobre a atualização da informação incompleta, consulte as secções anteriores deste artigo.
  - **Conclusão**: A secção está completa e não há erros. Todas as secções da oferta devem estar completas antes de poder submeter a oferta.
- Forneça **notas de certificação** à equipa de certificação para ajudar a garantir que a sua aplicação é testada corretamente. Inclua instruções de teste e quaisquer notas suplementares que possam ajudar a equipa a compreender a sua aplicação.

Para submeter a oferta para publicação, selecione **Review e publique**.

A Microsoft enviará uma mensagem de e-mail para informá-lo quando uma versão de pré-visualização da oferta está disponível para rever e aprovar. Para publicar a sua oferta ao público (ou, se for uma oferta privada, publique-a para um público privado), vá ao Partner Center, vá à página de **Visão Geral** da sua oferta e, em seguida, selecione **Go-live**. O estado da sua oferta aparece no topo da página quando a publicação está em andamento.

### <a name="errors-and-review-feedback"></a>Erros e feedback de revisão

A etapa de **validação manual** no processo de publicação representa uma revisão extensiva da sua oferta e dos seus ativos técnicos associados. Se este processo descobrir erros com a sua oferta, receberá um relatório de certificação que detalha os problemas. Basta fazer as correções necessárias e reeditar a sua oferta.

## <a name="offer-overview"></a>Visão geral da oferta

A página geral da **Oferta** mostra uma representação visual dos passos, concluídos e em curso, que são necessários para publicar a sua oferta e o tempo que cada passo deve demorar a ser concluído.

Esta página também inclui links para ajudá-lo a trabalhar com a oferta, dependendo do seu estado:

- Se a oferta for um rascunho: [Eliminar a oferta](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#delete-a-draft-offer) de rascunho
- Se a oferta estiver ao vivo: [Pare de vender a oferta](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#stop-selling-an-offer-or-plan)
- Se a oferta estiver em [pré-visualização: Go-live](https://docs.microsoft.com/azure/marketplace/partner-center-portal/publishing-status#publisher-approval)
- Se ainda não tiver concluído a inscrição da editora: [Cancele a publicação](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#cancel-publishing)

## <a name="marketplace-examples"></a>Exemplos de mercado

Os exemplos que se seguem ilustram como a oferta aparece no Azure Marketplace.

### <a name="azure-marketplace-offer-details"></a>Azure Marketplace oferece detalhes

![Azure Marketplace oferece dados de página exemplo](media/avm-create1.png)

### <a name="azure-marketplace-search-results"></a>Resultados da pesquisa do Azure Marketplace

![Exemplo de página de detalhes de pesquisa do Azure Marketplace](media/avm-create2.png)

### <a name="azure-marketplace-plan-details"></a>Detalhes do plano Azure Marketplace

![Plano Azure Marketplace detalha exemplo de página](media/avm-create3.png)

### <a name="azure-portal-offer-details"></a>Portal Azure oferece detalhes

![Portal Azure oferece dados de página exemplo](media/avm-create4.png)

### <a name="azure-portal-search-results"></a>Resultados da pesquisa do portal Azure

![Exemplo de página de resultados de pesquisa do portal Azure](media/avm-create5.png)

### <a name="azure-portal-plan-details"></a>Detalhes do plano do portal Azure

![Plano do portal Azure detalha exemplo de página](media/avm-create6.png)

## <a name="next-steps"></a>Passos seguintes

- [Atualizar uma oferta existente no mercado comercial](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer)
