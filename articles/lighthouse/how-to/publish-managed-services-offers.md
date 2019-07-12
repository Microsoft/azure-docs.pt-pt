---
title: Publicar uma oferta de serviços geridos do Azure Marketplace
description: Saiba como publicar uma oferta de serviço gerido que carrega os clientes do Azure designado a gestão de recursos.
author: JnHs
ms.author: jenhayes
ms.service: lighthouse
ms.date: 07/11/2019
ms.topic: overview
manager: carmonm
ms.openlocfilehash: bb2f26a170bbd60eb927bd00f6def7d033fafee9
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67809849"
---
# <a name="publish-a-managed-services-offer-to-azure-marketplace"></a>Publicar uma oferta de serviços geridos do Azure Marketplace

Neste artigo, aprenderá como publicar uma oferta de serviços geridos públicos ou privados para [do Azure Marketplace](https://azuremarketplace.microsoft.com) utilizando o [Cloud Partner Portal](https://cloudpartner.azure.com/), permitindo que um cliente que compra a oferta a carregar para o Azure delegada gestão de recursos. 

> [!NOTE]
> Tem de ter um válido [conta no Centro de parceiros](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-account) para criar e publicar estas ofertas. Se ainda não tiver uma conta, o [processo de inscrição](https://aka.ms/joinmarketplace) irá levá-lo pelos passos de criação de uma conta no Centro de parceiros e inscrever-se no programa de Marketplace comercial. Sua ID do Microsoft Partner Network (MPN) será [automaticamente associado](https://docs.microsoft.com/azure/billing/billing-partner-admin-link-started) com as ofertas a publicação para controlar o impacto de envolvimento com o cliente.
>
> Se não pretender publicar uma oferta no Azure Marketplace, pode integrar clientes manualmente, utilizando modelos Azure Resource Manager. Para mais informações, veja [integrar um cliente para o Azure delegadas a gestão de recursos](onboard-customer.md).

Oferta um serviços geridos de publicação é semelhante à publicação de qualquer outro tipo de oferta no Azure Marketplace. Para saber mais sobre esse processo, veja [Azure Marketplace e guia de publicação do AppSource](https://docs.microsoft.com/azure/marketplace/marketplace-publishers-guide) e [ofertas de gerir o Azure e o Marketplace do AppSource](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/manage-offers/cpp-manage-offers).

> [!IMPORTANT]
> Cada plano de uma oferta de serviços geridos inclui um **detalhes do manifesto** seção, onde define as entidades do Azure Active Directory (Azure AD) no seu inquilino que terão acesso a grupos de recursos delegados e/ou subscrições para clientes que comprarem esse plano. É importante estar ciente de que qualquer grupo (ou utilizador ou principal de serviço) que incluem aqui terão as mesmas permissões para cada cliente que comprar o plano. Para atribuir grupos diferentes para trabalhar com cada cliente, terá de publicar um plano de privada separado que é exclusivo para cada cliente.

## <a name="create-your-offer-in-the-cloud-partner-portal"></a>Criar a sua oferta no Portal de parceiros de nuvem

1. Inicie sessão para o [Portal de parceiros da Cloud](https://cloudpartner.azure.com/).
2. A partir do menu de navegação esquerdo, selecione **nova oferta**, em seguida, selecione **serviços geridos**.
3. Verá uma **Editor** secção da sua oferta com quatro partes preencher: **Definições da oferta**, **planos**, **Marketplace**, e **suporte**. Continue a ler para obter orientações sobre como concluir estas secções.

## <a name="enter-offer-settings"></a>Introduza as definições da oferta

Na **definições da oferta** secção, forneça o seguinte:

|Campo  |Descrição  |
|---------|---------|
|**ID de oferta**     | Um identificador exclusivo para a sua oferta (dentro de seu perfil do publicador). Este ID pode conter apenas carateres em minúsculas de alfanuméricos, travessões e carateres de sublinhado, com um máximo de 50 carateres. Tenha em atenção que o ID da oferta pode ser visível para os clientes em locais, como URLs de produto e os relatórios de faturas. Depois de publicar a oferta, não é possível alterar este valor.        |
|**ID de publicador**     | O ID de publicador que será associado com a oferta. Se tiver mais do que um ID de publicador, pode selecionar aquele que pretende utilizar para esta oferta.       |
|**Name**     | O nome (até 50 caracteres) que serão mostrados aos clientes da sua oferta no Azure Marketplace e no portal do Azure. Utilizar um nome de marca reconhecível que os clientes possam compreender — se de que está a promover esta oferta por meio de seu próprio Web site, certifique-se de que usar o exatamente o mesmo nome aqui.        |

Quando tiver terminado, selecione **guardar**. Agora, está pronto para avançar para o **planos** secção.

## <a name="create-plans"></a>Criar planos

Cada oferta tem de ter um ou mais planos (às vezes, denominados SKUs). Pode adicionar vários esquemas para suportar conjuntos de recursos diferentes e aos preços diferentes ou para personalizar um plano específico para um público-alvo limitado de clientes específicos. Os clientes podem ver os planos disponíveis aos mesmos relacionados à oferta principal.

Na secção de planos, para cada plano que pretende criar, selecione **novo plano**. Em seguida, introduza um **ID de plano**. Este ID pode conter apenas carateres em minúsculas de alfanuméricos, travessões e carateres de sublinhado, com um máximo de 50 carateres. O ID do plano pode estar visível para os clientes em locais, como URLs de produto e os relatórios de faturas. Depois de publicar a oferta, não é possível alterar este valor.

Em seguida, conclua as seguintes secções do **detalhes do plano** secção:

|Campo  |Descrição  |
|---------|---------|
|**Título**     | Nome amigável para o plano para exibição. Comprimento máximo de 50 carateres.        |
|**Resumo**     | Sucinta descrição do plano de exibição sob o título. Comprimento máximo de 100 carateres.        |
|**Descrição**     | Texto de descrição que fornece uma explicação mais detalhada do plano.         |
|**Modelo de faturação**     | Existem 2 modelos de faturação mostrados aqui, mas tem de escolher **traga a sua própria licença** das ofertas de serviços geridos. Isso significa que irá cobrar os seus clientes diretamente para os custos relacionados com esta oferta, e a Microsoft não cobra todas as taxas para.   |
|**Este é um plano privado?**     | Indica se o SKU é privado ou público. A predefinição é **não** (público). Se deixar esta seleção, seu plano não serão restringido aos clientes específicos (ou a um determinado número de clientes); Depois de publicar um plano de público, não é possível alterá-lo mais tarde para privada. Para disponibilizar este plano apenas a utilizadores específicos, selecione **Sim**. Ao fazê-lo, terá de identificar os clientes fornecendo suas IDs de subscrição. Estes podem ser introduzidos um por um (para subscrições de até 10) ou ao carregar um ficheiro. csv (para subscrições de até 20 000). Certifique-se de que incluem o seu próprio subscrições aqui para que possa testar e validar a oferta. Para obter mais informações, consulte [privada SKUs e planos](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-azure-private-skus).  |

Por fim, conclua os **detalhes do manifesto** secção. Esta ação cria um manifesto com informações de autorização para gerir recursos do cliente. As informações que fornecer aqui é delegada de seus clientes para o Azure necessários para integrar a gestão de recursos. Conforme observado anteriormente, estas permissões serão aplicada a todos os clientes que comprar o plano, portanto, se pretender limitar o acesso a um cliente específico, precisará publicar um plano privado para utilização exclusiva.

- Em primeiro lugar, forneça uma **versão** para o manifesto. Utilize o formato *n.n.n* (por exemplo, 1.2.5).
- Em seguida, introduza o seu **ID de inquilino**. Este é um GUID associado com o ID de inquilino do Azure Active Directory da sua organização (ou seja, o inquilino que irá ser trabalhando para gerir os recursos dos seus clientes). Se não tiver neste útil, pode encontrá-lo ao pairar o rato sobre o nome da sua conta no canto superior direito do portal do Azure, ou ao selecionar **trocar diretório**. 
- Por fim, adicione um ou mais **autorização** entradas ao seu plano. As autorizações definem as entidades que podem aceder a recursos e subscrições para os clientes que comprarem o plano. Tem de fornecer estas informações para poder aceder aos recursos em nome do cliente com a gestão de recursos do Azure de delegado.
  Para cada autorização, fornece o seguinte. Em seguida, pode selecionar **autorização novo** quantas vezes for necessário para adicionar mais definições de utilizadores/funções.
  - **ID de objeto do Azure AD**: O identificador do Azure AD de um utilizador, o grupo de utilizadores ou a aplicação que vai ser concedida determinadas permissões (conforme descrito pela definição de função) a recursos dos seus clientes.
  - **Nome a apresentar do objeto do Azure AD**: Um nome amigável para ajudar o compreender a finalidade desta autorização de cliente. O cliente Verão este nome quando delegar recursos.
  - **Definição de função**: Selecione uma disponíveis das funções incorporadas do Azure AD, na lista. Esta função irá determinar as permissões que o utilizador no **ID de objeto do Azure AD** campo terá nos recursos dos seus clientes. Para informações sobre estas funções, veja [funções incorporadas](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles).
  - **Funções atribuíveis**: Se tiver selecionado o administrador de acesso de utilizadores no **definição de função** para que esta autorização, pode adicionar uma ou mais atribuíveis funções aqui. O utilizador no **ID de objeto do Azure AD** campo vai poder atribuí-los **funções atribuível** para [geridos identidades](https://docs.microsoft.com/azure/managed-applications/publish-managed-identity). Tenha em atenção que não existem outras permissões normalmente associadas à função Administrador de acesso de utilizador irão aplicar a este utilizador. (Se não selecionou o administrador de acesso de utilizador para definição de função deste utilizador, este campo não tem qualquer efeito.)

> [!TIP]
> Na maioria dos casos, desejará atribuir permissões a um grupo de utilizadores do Azure AD ou o serviço principal, em vez de uma série de contas de utilizador individuais. Isto permite-lhe adicionar ou remover o acesso para utilizadores individuais sem ter de atualizar e voltar a publicar o plano quando alteram os requisitos de acesso.

Quando tiver terminado de adicionar os planos, selecione **salvar**, em seguida, avance para o **Marketplace** secção.

## <a name="provide-marketplace-text-and-images"></a>Fornecer imagens e texto do Marketplace

O **Marketplace** secção é onde fornecer o texto e imagens que serão mostrados aos clientes no Azure Marketplace e o portal do Azure.

Forneça informações para os campos seguintes no **descrição geral** secção:

|Campo  |Descrição  |
|---------|---------|
|**Título**     |  Título da oferta, muitas vezes, o nome longo, formal. Este título será ser apresentado de forma destacada no marketplace. Comprimento máximo de 50 carateres. Na maioria dos casos, isso deve ser igual a **nome** que introduziu na **oferecer definições** secção.       |
|**Resumo**     | Breve objetivo ou a função da sua oferta. Normalmente, é apresentado sob o título. Comprimento máximo de 100 carateres.        |
|**Resumo completo**     | Um resumo de mais tempo do objetivo ou a função da sua oferta. Comprimento máximo de 256 carateres.        |
|**Descrição**     | Obter mais informações sobre a oferta. Este campo tem um comprimento máximo de 3000 carateres e oferece suporte a formatação de HTML simples.        |
|**Identificador de marketing**     | Um identificador exclusivo de URL amigável. será utilizado no Marketplace URLs para esta oferta. Por exemplo, se o ID de publicador estiver *contoso* e é o identificador de marketing *sampleApp*, o URL para a sua oferta no Azure Marketplace será *https://azuremarketplace.microsoft.com/marketplace/apps/contoso.sampleApp* .        |
|**IDs de subscrição de pré-visualização**     | Adicione identificadores de subscrição de um e 100. Os clientes associados a estas subscrições vão conseguir ver a oferta no Azure Marketplace antes de entrar no ar. Sugerimos que incluindo seu próprio subscrições aqui, para que possa visualizar a forma como a oferta é apresentado no Azure Marketplace antes de disponibilizá-lo para os clientes.  (O suporte da Microsoft e equipas de engenharia também será capazes de ver a sua oferta durante este período de pré-visualização.)   |
|**Ligações úteis**     | URLs relacionadas com a sua oferta, por exemplo, documentação, notas de versão, FAQs, etc.        |
|**Sugestões de categorias (máx. 5)**     | Uma ou mais categorias (até cinco) que se aplicam à sua oferta. Estas categorias ajudam os clientes a detetar a sua oferta no Azure Marketplace e o portal do Azure.        |

Na **artefactos Marketing** secção, pode carregar logótipos e outros ativos a serem apresentados na sua oferta. Opcionalmente, pode carregar capturas de ecrã ou links para vídeos que podem ajudar os clientes a compreender a sua oferta.

Quatro tamanhos de logótipo são necessários: **Pequeno (40 x 40)** , **médio (90 x 90)** , **grandes (115 x 115)** , e **toda a (255 x 155)** . Siga estas diretrizes para sua logótipos:

- O design do Azure tem uma paleta de cores simples. Limite o número de cores primárias e secundárias no logótipo.
- As cores do tema do portal são preto e branco. Não utilize estas cores como cor de fundo do logótipo. Utilize uma cor que realce o logótipo no portal. Recomendamos cores primárias simples.
- Se utilizar um plano de fundo transparente, certifique-se de que o logótipo e o texto não são branco, preto ou azul.
- O aspeto e funcionalidade do logótipo deve ser simples e evitar gradações. Não utilize um fundo de gradação no logótipo.
- Não coloque texto no logótipo, nem mesmo o nome da empresa ou da marca.
- Certifique-se que o logótipo não está ampliado.

O **Hero (815 x 290)** logótipo é opcional mas recomendado. Se incluir um logótipo de hero, siga estas diretrizes:

- Não inclui o texto no logótipo de hero e certifique-se de que deixar 415 pixels de espaço em branco à direita do logótipo de. Isto é necessário deixar espaço para os elementos de texto que será incorporado por meio de programação: seu nome de exibição do publicador, título do plano, oferta resumo completo.
- Plano de fundo do logótipo do seu herói não pode ser transparente, preto ou branco. Certifique-se de que a cor de fundo não é muito leve, uma vez que será apresentado o texto incorporado em branco.
- Depois de publicar a sua oferta com um ícone de hero, não é possível removê-lo (embora pode atualizá-la com uma versão diferente se assim o desejar).

Na **levar gestão** secção, pode selecionar o sistema CRM onde serão armazenados os suas oportunidades potenciais, se assim o desejar. 

Por fim, fornecer seu **URL de política de privacidade** e **termos de utilização** no **legais** secção. Também pode especificar aqui se deve ou não utilizar o [contrato padrão](https://docs.microsoft.com/azure/marketplace/standard-contract) para esta oferta.

Certifique-se de que guardar as alterações antes de passar para o **suporte** secção.

## <a name="add-support-info"></a>Adicionar informações de suporte

Na **suportar** secção, forneça o nome, e-mail e número de telefone de contacto de suporte de um contacto de engenharia e um cliente. Também precisará fornecer suporte a URLs. A Microsoft pode utilizar estas informações quando for necessário para contactá-lo sobre a empresa e problemas de suporte.

Depois de adicionar esta informação, selecione **guardar.**

## <a name="publish-your-offer"></a>Publicar a sua oferta

Assim que estiver satisfeito com todas as informações que forneceu, sua próxima etapa é publicar a oferta no Azure Marketplace. Selecione o **publicar** botão para iniciar o processo de tornar a sua oferta em direto. Para mais informações sobre este processo, veja [ofertas de publicar o Azure Marketplace e AppSource](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/manage-offers/cpp-publish-offer).

## <a name="next-steps"></a>Passos Seguintes

- Saiba mais sobre [experiências de gestão entre inquilinos](../concepts/cross-tenant-management-experience.md).
- [Ver e gerir os clientes](view-manage-customers.md) ao aceder **meus clientes** no portal do Azure.
