---
title: Criar planos para uma oferta de máquinas virtuais no Azure Marketplace
description: Saiba como criar planos para uma oferta de máquinas virtuais no Azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: mingshen-ms
ms.author: mingshen
ms.date: 10/19/2020
ms.openlocfilehash: bc5e98484560fcc15e0ea3e289069c84687f158c
ms.sourcegitcommit: 4f4a2b16ff3a76e5d39e3fcf295bca19cff43540
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93040545"
---
# <a name="how-to-create-plans-for-a-virtual-machine-offer"></a>Como criar planos para uma oferta de máquina virtual

Você pode fornecer uma variedade de opções de plano dentro da mesma oferta no Partner Center. Uma oferta requer pelo menos um plano (anteriormente chamado de SKU), que pode variar por rentabilização do público, região de Azure, recursos ou imagens VM.

Pode criar até 100 planos para cada oferta: até 45 destes podem ser privados. Saiba mais sobre [planos privados em ofertas privadas no mercado comercial da Microsoft.](private-offers.md)

Depois de criar os seus planos, selecione o separador **'visão geral' do Plano** para visualizar:

- Nomes do plano
- Modelos de licença
- Público (público ou privado)
- Estado atual da publicação
- Ações disponíveis

As ações que estão disponíveis no **painel de visão geral** do Plano variam consoante o estado atual do seu plano.

- Se o estado do plano for um rascunho, **selecione Eliminar rascunho** .
- Se o estado do plano for publicado ao vivo, selecione **Stop sell plan** ou Sync private **audience** .

## <a name="create-a-new-plan"></a>Criar um novo plano

**Selecione Criar um novo plano** no topo. Aparece a caixa de diálogo do **novo plano.**

Na caixa **de Identificação** do Plano, crie um ID de plano único para cada plano nesta oferta. Este ID será visível para os clientes no endereço web do produto. Utilize apenas letras e números minúsculos, traços ou sublinhados, e um máximo de 50 caracteres.

> [!NOTE]
> O ID do plano não pode ser alterado depois de selecionar **Criar** .

Na caixa de nomes do **Plano,** insira um nome para este plano. Os clientes vêem este nome quando estão a decidir qual o plano a selecionar dentro da sua oferta. Crie um nome único que aponte claramente as diferenças entre os planos. Por exemplo, pode introduzir **o Windows Server** com planos *Pay-as-you-go* , *BYOL,* *Advanced* e *Enterprise.*

Selecione **Criar** .

## <a name="plan-setup"></a>Configuração do plano

Desista a configuração de alto nível para o tipo de plano, especifique se reutiliza uma configuração técnica de outro plano e identifique as regiões de Azure onde o plano deve estar disponível. As suas seleções aqui determinam quais os campos exibidos em outros painéis para o mesmo plano.

### <a name="reuse-a-technical-configuration"></a>Reutilizar uma configuração técnica

Se tiver mais do que um plano do mesmo tipo e as embalagens forem idênticas entre eles, pode selecionar **Este plano reutiliza a configuração técnica de outro plano** . Esta opção permite-lhe selecionar um dos outros planos do mesmo tipo para esta oferta e permite-lhe reutilizar a sua configuração técnica.

> [!NOTE]
> Ao reutilizar a configuração técnica de outro plano, todo o separador de **configuração técnica** desaparece deste plano. Os detalhes de configuração técnica do outro plano, incluindo quaisquer atualizações que fizer no futuro, também serão utilizados para este plano. Esta definição não pode ser alterada após a publicação do plano.

### <a name="azure-regions"></a>Regiões do Azure

O seu plano deve ser disponibilizado em pelo menos uma região de Azure.

Selecione a opção **Azure Global** para disponibilizar o seu plano aos clientes de todas as regiões da Azure Global que tenham integração comercial no mercado. Para mais informações, consulte [a disponibilidade geográfica e o suporte à moeda.](marketplace-geo-availability-currencies.md)

Selecione a opção **do Governo Azure** para disponibilizar o seu plano na região do [Governo Azure.](../azure-government/documentation-government-welcome.md) Esta região fornece acesso controlado a clientes de entidades federais, estaduais, locais ou tribais dos EUA, bem como a parceiros elegíveis para os servir. Você, como editora, é responsável por quaisquer controlos de conformidade, medidas de segurança e boas práticas. O Governo Azure utiliza datacenters e redes fisicamente isoladas (localizadas apenas nos EUA).

Antes de publicar para o [Governo Azure,](../azure-government/documentation-government-manage-marketplace-partners.md)teste e valide o seu plano no ambiente, porque certos pontos finais podem diferir. Para configurar e testar o seu plano, solicite uma conta de teste na página de teste do [Governo Microsoft Azure.](https://azure.microsoft.com/global-infrastructure/government/request/)

> [!NOTE]
> Depois de o seu plano ser publicado e disponível numa região específica de Azure, não pode remover essa região.

### <a name="azure-government-certifications"></a>Certificações do Governo de Azure

Esta opção só é visível se selecionar o **Governo Azure** como região do Azure na secção anterior.

Os serviços do Governo Azure tratam dados que estão sujeitos a certos regulamentos e requisitos governamentais. Por exemplo, FedRAMP, NIST 800.171 (DIB), ITAR, IRS 1075, DoD L4 e CJIS. Para consciencializar as suas certificações para estes programas, pode fornecer até 100 links que os descrevem. Estes podem ser links para a sua listagem diretamente no programa ou links para descrições da sua conformidade com eles nos seus próprios websites. Estas ligações são visíveis apenas para clientes do Governo Azure.

**Selecione Guardar o projeto** antes de continuar.

## <a name="plan-listing"></a>Listagem de planos

Nesta secção, configurar os detalhes da listagem do plano. Este painel apresenta informações específicas, que podem diferir de outros planos na mesma oferta.

### <a name="plan-name"></a>Nome do plano

Este campo é automaticamente preenchido com o nome que deu ao seu plano quando o criou. Este nome aparece no Azure Marketplace como o título deste plano. Está limitado a 100 caracteres.

### <a name="plan-summary"></a>Resumo do plano

Providencie um breve resumo do seu plano, não a oferta. Este resumo está limitado a 100 caracteres.

### <a name="plan-description"></a>Descrição do plano

Descreva o que torna este plano de software único e descreva quaisquer diferenças entre os planos dentro da sua oferta. Descreva o plano apenas, não a oferta. A descrição do plano pode conter até 2.000 caracteres.

**Selecione Guardar o projeto** antes de continuar.

## <a name="pricing-and-availability"></a>Preços e disponibilidade

Neste painel, você configura:

- Mercados onde este plano está disponível. Todos os planos devem estar disponíveis em pelo menos um [mercado.](marketplace-geo-availability-currencies.md)
- O preço por hora.
- Quer tornar o plano visível para todos ou apenas para clientes específicos (um público privado).

### <a name="markets"></a>Mercados

Todos os planos devem estar disponíveis em pelo menos um mercado. Selecione a caixa de verificação para cada local de mercado onde este plano deve estar disponível para compra. (Os utilizadores nestes mercados podem ainda implementar a oferta em todas as regiões do Azure selecionadas na secção ["Configuração do](#plan-setup) Plano".) O botão **Tax Remitted** mostra países/regiões em que a Microsoft remete as vendas e utiliza o imposto em seu nome. A publicação para a China *limita-se* a planos que sejam *gratuitos* ou trazem a sua própria licença (BYOL).

Se já definiu os preços para o seu plano em moeda norte-americana (USD) e adicionou outra localização de mercado, o preço do novo mercado é calculado de acordo com as taxas de câmbio atuais. Reveja sempre o preço de cada mercado antes de publicar. Reveja o seu preço selecionando preços de **exportação (xlsx)** depois de guardar as suas alterações.

Quando remover um mercado, os clientes desse mercado que estão a utilizar implementações ativas não serão capazes de criar novas implementações ou aumentar as suas implementações existentes. As implantações existentes não são afetadas.

### <a name="pricing"></a>Preços

Para o **modelo Licença** , selecione o plano **de faturação mensal baseado em uso** para configurar os preços para este plano, ou selecione Traga a sua própria **licença** para permitir que os clientes utilizem este plano com a sua licença existente.

Para um plano de faturação mensal baseado na utilização, utilize uma das seguintes três opções de inscrição de preços:

- **Por núcleo** : Forneça preços por núcleo em USD. A Microsoft calcula o preço por tamanho de núcleo e converte-o em moedas locais utilizando a taxa de câmbio atual.
- **Por tamanho do núcleo** : Forneça preços por tamanho do núcleo em USD. A Microsoft calcula o preço e converte-o em moedas locais utilizando a taxa de câmbio atual.
- **Por mercado e dimensão de núcleo** : Forneça preços para cada tamanho central para todos os mercados. Pode importar os preços de uma folha de cálculo.

> [!NOTE]
> Economize alterações de preços para permitir a exportação de dados de preços. Depois de publicado um preço para um mercado no seu plano, não pode ser alterado mais tarde. Para garantir que os preços estão certos antes de os publicar, exporte a folha de cálculo de preços e reveja os preços em cada mercado.

### <a name="free-trial"></a>Avaliação Gratuita

Pode oferecer um *Teste Gratuito* de um mês ou três meses ou seis meses aos seus clientes.

### <a name="visibility"></a>Visibilidade

Pode desenhar cada plano para ser visível para todos ou apenas para um público pré-selecionado. Atribua membros a este público restrito utilizando iDs de subscrição Azure.

**Público:** O seu plano pode ser visto por todos.

**Público privado** : Torne o seu plano visível apenas para um público pré-selecionado. Depois de publicado como um plano privado, pode atualizar o público ou mudá-lo para público. Depois de tornar um plano público, deve permanecer público. Não pode ser mudado para um plano privado.

> [!NOTE]
> Um público privado ou restrito é diferente do público de pré-visualização que definiu no painel **de pré-visualização.** Um público de pré-visualização pode aceder à sua oferta _antes_ de ser publicada ao vivo no Azure Marketplace. Embora a escolha do público privado se aplique apenas a um plano específico, o público de pré-visualização pode ver todos os planos privados e públicos para fins de validação.

**Público restrito (IDs de subscrição Azure)** : Atribua ao público que terá acesso a este plano privado utilizando IDs de subscrição Azure. Opcionalmente, inclua uma descrição de cada ID de subscrição Azure que designou. Adicione até 10 IDs de subscrição manualmente ou até 20.000 IDs se estiver a importar uma folha de cálculo CSV. Os IDs de subscrição Azure são representados como GUIDs, e todas as letras devem ser minúsculas.

>[!Note]
>As ofertas privadas não são suportadas com subscrições Azure estabelecidas através de um revendedor do programa Cloud Solution Provider (CSP).

### <a name="hide-a-plan"></a>Esconder um plano

Se a sua máquina virtual deve ser usada apenas indiretamente quando é referenciada através de outro modelo de solução ou aplicação gerida, selecione esta caixa de verificação para publicar a máquina virtual, mas esconda-a de clientes que possam estar a pesquisar ou navegar diretamente por ela.

> [!NOTE]
> Planos ocultos não suportam links de pré-visualização.

**Selecione Guardar o projeto** antes de continuar.

## <a name="technical-configuration"></a>Configuração técnica

Forneça as imagens e outras propriedades técnicas que estejam associadas a este plano. Para mais informações, consulte [a Configure VM para oferecer detalhes da listagem.](azure-vm-create-listing.md)

> [!NOTE]
> O **separador de configuração técnica** não é apresentado se configurar este plano para reutilizar pacotes de outro plano no separador **de configuração do Plano.**

### <a name="operating-system"></a>Sistema operativo

No painel do **sistema operativo,** faça o seguinte:

- Para **a família do sistema operativo,** selecione o sistema operativo **Windows** ou **Linux.**
- Para **Lançamento** ou **Fornecedor,** selecione a versão do Windows ou o fornecedor Linux.
- Para **um nome amigável do OS,** insira um nome de sistema operativo amigável. Este nome é visível para os clientes.

### <a name="recommended-vm-sizes"></a>Tamanhos VM recomendados

Selecione até seis tamanhos de máquina virtuais recomendados para exibir no Azure Marketplace.

### <a name="open-ports"></a>Abrir portas

Abra portas públicas ou privadas numa máquina virtual implantada.

### <a name="storage-option-for-deployment"></a>Opção de armazenamento para implantação

Para **a opção de implementação do disco,** selecione o tipo de implementação do disco que os seus clientes podem utilizar para a máquina virtual. A Microsoft recomenda limitar a implementação apenas à **implementação do disco gerido.**

### <a name="properties"></a>Propriedades

Para **suporte rede acelerada** , selecione se o seu VM suporta [rede acelerada](https://go.microsoft.com/fwlink/?linkid=2124513).

### <a name="generations"></a>Gerações

Gerar uma máquina virtual define o hardware virtual que utiliza. Com base nas necessidades do seu cliente, pode publicar uma Geração 1 VM, Geração 2 VM, ou ambas.

1. Ao criar uma nova oferta, selecione um **tipo de Geração** e insira os detalhes de imagem solicitados:

    :::image type="content" source="./media/create-vm/azure-vm-generations-image-details.png" alt-text="Uma vista da caixa de entrega da Geração.":::

2. Para adicionar mais uma geração a um plano, **selecione Adicionar geração** :

    :::image type="content" source="./media/create-vm/azure-vm-generations-add.png" alt-text="Uma vista da caixa de entrega da Geração.":::

    Em seguida, insira os detalhes da geração:

    :::image type="content" source="./media/create-vm/azure-vm-generations-details.png" alt-text="Uma vista da caixa de entrega da Geração.":::

    O **ID de geração** que escolher será visível para os clientes em locais como URLs de produto e modelos ARM (se aplicável). Utilizar apenas caracteres minúsculos, alfanuméricos, traços ou sublinhados; não pode ser modificado uma vez publicado.

3. Para atualizar um VM existente que tenha uma Geração 1 já publicada, basta editar detalhes na página **de configuração técnica:**

    :::image type="content" source="./media/create-vm/azure-vm-generations-updating.png" alt-text="Uma vista da caixa de entrega da Geração.":::

Para saber mais sobre as diferenças entre as capacidades da Geração 1 e da Geração 2, consulte [Suporte para a geração 2 VMs em Azure.](../virtual-machines/generation-2.md)

### <a name="vm-images"></a>Imagens de VM

Forneça uma versão em disco e a assinatura de acesso partilhado (SAS) URI para as imagens de máquinas virtuais. Adicione até 16 discos de dados para cada imagem VM. Forneça apenas uma nova versão de imagem por plano numa submissão especificada. Depois de uma imagem ter sido publicada, não pode editá-la, mas pode eliminá-la. A eliminação de uma versão impede que os utilizadores novos e já existentes implementem uma nova instância da versão eliminada.

- **Versão do disco** : A versão da imagem que está a fornecer.
- **SAS URI** : A localização na sua conta de armazenamento Azure onde guardou o sistema operativo VHD. Para aprender a obter um SAS URI, consulte [obter a assinatura de acesso partilhado URI para a sua imagem VM](azure-vm-get-sas-uri.md).
- As imagens de disco de dados são também URIs de acesso partilhado VHD que são armazenados nas suas contas de armazenamento Azure.
- Adicione apenas uma imagem por submissão num plano.

Independentemente do sistema operativo que utilize, adicione apenas o número mínimo de discos de dados que a solução necessita. Durante a implementação, os clientes não podem remover discos que fazem parte de uma imagem, mas podem sempre adicionar discos durante ou após a implementação.

**Selecione Guardar o rascunho** antes de continuar e voltar à **visão geral do Plano** .

## <a name="next-steps"></a>Passos seguintes

- [Adicionar uma audiência de pré-visualização](azure-vm-create-preview.md)
