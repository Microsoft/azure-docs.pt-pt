---
title: Planos e preços para ofertas de mercado comercial
description: Conheça os planos para ofertas de marketplace comercial da Microsoft no Partner Center.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: mingshen
ms.author: mingshen
ms.date: 09/02/2020
ms.openlocfilehash: e97171e61814b832e78f7bd815d8d6f341a90840
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "91858177"
---
# <a name="plans-and-pricing-for-commercial-marketplace-offers"></a>Planos e preços para ofertas de mercado comercial

Um plano define o âmbito e os limites de uma oferta, e os preços associados quando aplicável. Por exemplo, dependendo do tipo de oferta, você pode selecionar mercados regionais e escolher se um plano é visível para o público ou apenas para um público privado. Alguns tipos de oferta suportam subscrições recorrentes, alguns preços baseados no suporte, e alguns permitem que um cliente compre a oferta com uma licença que adquiriu diretamente à editora. Isto dá-lhe a flexibilidade para fornecer aos seus clientes diferentes opções técnicas e de preços, quando aplicável.

Você pode criar até 100 planos para cada oferta que suporta planos, e até 45 desses planos podem ser [privados.](#plan-visibility) Você pode criar planos pagos apenas para ofertas de máquinas virtuais Azure, ofertas de aplicações Azure (aplicações geridas) e software como um serviço (SaaS) oferece. Quando optar por vender qualquer uma destas ofertas através da Microsoft (ofertas transacionáveis), é-lhe exigido que crie pelo menos um plano. Pode criar planos para alguns dos outros tipos de oferta, mas os planos para esses tipos de oferta não incluem opções de preços.

> [!TIP]
> Uma oferta transacionável é aquela em que a Microsoft facilita a troca de dinheiro por uma licença de software em nome da editora.

## <a name="plans-by-offer-type"></a>Planos por tipo de oferta

A tabela a seguir mostra as opções de plano para cada tipo de oferta. As seguintes secções explicarão mais sobre estas opções. 

| Tipo de oferta | Planos com opções de preços | Planos sem opções de preços | Opção de audiência privada |
| ------------ | ------------- | ------------- | ------------- |
| Aplicação gerida aZure | &#10004; | | &#10004; |
| Modelo de solução Azure | | &#10004; | &#10004; |
| Recipiente azul | | &#10004; (BYOL) | |
| Módulo IoT Edge |  | &#10004; |  |
| Serviço gerido |  | &#10004; (BYOL) | &#10004; |
| Software como um serviço | &#10004; |  | &#10004; |
| Máquina virtual do Azure | &#10004; |  | &#10004; |
|||||

Os planos não são suportados para os seguintes tipos de oferta:

- Serviço de consultoria
- Dynamics 365 Business Central
- Dinâmica 365 Envolvimento com clientes & PowerApps
- Dynamics 365 for Operations
- Aplicação Power BI

## <a name="plan-information"></a>Informação do plano

Cada tipo de oferta requer informações diferentes quando cria um novo plano. Pode encontrar links para oferecer artigos específicos em [Guia de Publicação por tipo de oferta.](publisher-guide-by-offer-type.md) Assim que criar um novo plano na página **de visão geral** do Plano, verá diferentes separadores, tais como lista de **planos** ou **preços e disponibilidade** para configurar diferentes detalhes para o seu plano. Cada separador mostrará um estado incompleto ou completo à medida que trabalha nestes campos.

![Ilustra a página de listagem de planos no Partner Center. O nome do plano, o resumo do plano e os campos de descrição do plano estão em destaque.](./media/commercial-marketplace-plans/plan-listing-tab.png)

Há alguns detalhes comuns para completar para um novo plano:

- **ID do plano**: Crie um ID único para cada plano nesta oferta. Use um máximo de 50 caracteres: apenas caracteres minúsculos, alfanuméricos, traços e sublinhados. Este ID será visível para os clientes nos modelos URL e Azure Resource Manager (se aplicável). Não podes mudar esta identificação depois de publicares a oferta.
- **Nome do plano**: (Chamada 1 na imagem acima.) Crie um nome único para cada plano nesta oferta. Use um máximo de 50 caracteres. O nome do plano é usado para diferenciar planos de software que podem fazer parte da mesma oferta (por exemplo, Nome de oferta: plano padrão e plano empresarial). Os clientes verão este nome ao decidir qual o plano a selecionar dentro da sua oferta.
- **Resumo do plano**: (Chamada 2 na imagem acima.) Este resumo aparece nos resultados de pesquisa do Azure Marketplace e pode conter até 100 caracteres.
   > [!NOTE]
   > Este campo não se aplica às ofertas do SaaS.
- **Descrição do plano**: (Chamada 3 na imagem acima.) Adicione uma descrição do plano que explica o que torna este plano único a partir de outros planos para a sua oferta. Use um máximo de 500 caracteres. Este conteúdo aparecerá para os seus clientes na(s) página de listagem de ofertas à medida que navegam e selecionam um plano para a sua oferta.

O nome e descrição do plano aparecem na página de listagem de ofertas na(s) loja(s) comercial marketplace online. A imagem que se segue mostra três planos para uma oferta saaS no Azure Marketplace.

![Ilustra uma página de listagem de oferta no Partner Center. Três planos são mostrados.](./media/commercial-marketplace-plans/offer-listing-page.png)

Assim que criar os seus planos, a página **de visão geral** do Plano mostra uma lista do nome dos seus planos, ID, outros detalhes, estado de publicação atual e quaisquer ações disponíveis. As ações disponíveis dependerão do estado do seu plano e poderão incluir:

- Se o estado do plano for **Rascunho** – Eliminar rascunho.
- Se o estado do plano for **Live** – Pare de vender plano ou público privado Sync.

A imagem que se segue mostra duas ofertas de rascunho.

![Ilustra a página geral do Plano no Centro de Parceiros. Dois planos são mostrados.](./media/commercial-marketplace-plans/plan-overview-tab.png)

## <a name="pricing-and-availability"></a>Preços e disponibilidade

O mercado comercial opera num modelo de agência, em que os editores fixam preços, a Microsoft cobra aos clientes, e a Microsoft paga receitas aos editores enquanto retém uma taxa de agência. Define os mercados da sua oferta, visibilidade e preços (quando aplicável) no **separador preços e disponibilidade** ou **disponibilidade.**

- **Mercados**: Todos os planos devem estar disponíveis em pelo menos um mercado.  Tem a opção de selecionar apenas países "Tax Remitted", nos quais a Microsoft remete as vendas e usa o imposto em seu nome.
- **Preços**: Os modelos de preços aplicam-se apenas aos planos de aplicação gerida pela Azure, ofertas de máquinas virtuais SaaS e Azure. Todos os planos para a mesma oferta devem usar o mesmo modelo de preços.  
- **Visibilidade do** plano : Dependendo do tipo de oferta, pode definir um público privado ou esconder a oferta ou plano do Azure Marketplace. Isto é explicado mais detalhadamente no [Plano de visibilidade](#plan-visibility) mais tarde neste artigo.

> [!TIP]
> Recomendamos que crie planos mais adequados aos padrões de utilização da sua base de clientes alvo. Isto reduz os utilizadores de mudar frequentemente de planos com base nas suas mudanças de utilização. Para um exemplo de uma oferta saaS com planos de faturação de três metros, consulte [a oferta sample](./partner-center-portal/saas-metered-billing.md#sample-offer).

### <a name="pricing-models"></a>Modelos de preços

Deve associar um modelo de preços a cada plano para os seguintes tipos de oferta. Cada um destes tipos de oferta tem diferentes modelos de preços disponíveis:

- **Aplicação gerida pelo Azure:** taxa fixa (mensal) e preços baseados na utilização (dimensões do serviço de medição).
- **Software como serviço**: taxa fixa (mensal ou anual), por utilizador, e preços baseados na utilização (dimensões do serviço de medição). 
- **Máquina virtual Azure**: Traga a sua própria licença (BYOL) e preços baseados na utilização. Para um modelo de preços baseado em uso, você pode cobrar por núcleo, por tamanho do núcleo, ou por mercado e tamanho do núcleo. Um modelo de licença BYOL não permite custos adicionais baseados na utilização.   (As ofertas de máquinas virtuais BYOL não requerem um modelo de preços.)

Todos os planos para a mesma oferta devem usar o mesmo modelo de preços. Por exemplo, uma oferta SaaS não pode ter um plano que seja fixo e outro plano que seja por utilizador. Consulte documentação específica da oferta para obter informações detalhadas.

Se já estabeleceu preços para o seu plano em Dólares dos Estados Unidos (USD) e adiciona outra localização de mercado, o preço do novo mercado será calculado de acordo com as taxas de câmbio atuais. Depois de guardar as suas alterações, verá um link **de preços de exportação (xlsx)** que pode utilizar para rever e alterar o preço de cada mercado antes de publicar.

> [!IMPORTANT]
> Após a publicação da sua oferta, a escolha do modelo de preços não pode ser alterada.

O SaaS de taxa fixa oferece e a aplicação gerida oferece suporte de faturação medido usando o serviço de medição do mercado. Este é um modelo de faturação baseado em uso que permite definir unidades não padrão, como largura de banda ou e-mails, que os seus clientes pagarão numa base de consumo. Consulte documentação relacionada para saber mais sobre faturação medido para [aplicações geridas](./partner-center-portal/azure-app-metered-billing.md) e [aplicações SaaS.](./partner-center-portal/saas-metered-billing.md)

## <a name="custom-prices"></a>Preços personalizados

Para definir preços personalizados num mercado individual, exportar, modificar e, em seguida, importar a folha de cálculo de preços. Você é responsável por validar este preço e possuir estas configurações.

Reveja cuidadosamente os seus preços antes de publicar, pois existem algumas restrições sobre o que pode mudar após a publicação de um plano:

- Depois de publicado um plano, o modelo de preços não pode ser alterado.
- Depois de um termo de faturação ser publicado para um plano, não pode ser removido mais tarde.
- Depois de publicado um preço para um mercado no seu plano, não pode ser alterado mais tarde.

Os preços fixados em Dólares dos Estados Unidos (USD) são convertidos na moeda local de todos os mercados selecionados utilizando as taxas de câmbio atuais quando guardados. Valide estes preços antes de publicar exportando a folha de cálculo de preços e revendo o preço em cada mercado que selecionou.

1. Primeiro, tem de guardar as suas alterações de preços para permitir a exportação de dados de preços. Perto da parte inferior do **separador preços e disponibilidade,** selecione **Guardar o rascunho**.
1. No **preço,** selecione a **ligação de dados de preços de exportação.**
1. Abra o ficheiro exportedPrice.xlsx no Microsoft Excel.
1. Na folha de cálculo, faça as atualizações que pretende para as suas informações de preços e, em seguida, guarde as . Ficheiro CSV.
    > [!NOTE]
    > Pode ter de ativar a edição no Excel antes de poder atualizar o ficheiro.
1. No **separador preços e disponibilidade,** em **Preços,** selecione o link **de dados de preços de importação.**
1. Na caixa de diálogo que aparece, selecione **Sim**.
1. Selecione o ficheiro exportedPrice.xlsx atualizado e, em seguida, selecione **Abrir**.

## <a name="plan-visibility"></a>Visibilidade do plano

Você pode fazer planos para alguns tipos de oferta publicamente disponíveis ou disponíveis apenas para um público específico (privado). As ofertas com planos privados serão publicadas no portal Azure. Saiba mais sobre [planos privados em ofertas privadas no mercado comercial da Microsoft.](private-offers.md)

Você define o seu público privado usando IDs de inquilino Azure ou IDs de assinatura Azure, dependendo do tipo de oferta. Você pode inserir até 10 IDs manualmente ou importar até 10,00 IDs de subscrição ou 20.000 IDs de inquilino (conforme aplicável) com um ficheiro .csv. Também pode definir públicos privados para serviços de consultoria e ofertas Dynamics 365 que não têm planos.

Depois de a sua oferta ser publicada com um plano privado, pode atualizar o público ou optar por disponibilizar o plano a todos. Depois de um plano ser publicado como visível para todos, deve permanecer visível para todos e não pode ser configurado novamente como um plano privado.

> [!NOTE]
> Um público privado difere de uma audiência de pré-visualização. Na página Disponibilidade para alguns tipos **de** oferta, pode definir um público que pode pré-visualizar a sua oferta antes de a oferta ser publicada ao vivo no mercado comercial. Embora a designação do público privado se aplique apenas a um plano específico, o público de pré-visualização pode ver todos os planos (privados ou não), mas apenas durante o período limitado de pré-visualização enquanto o plano está a ser testado e validado.

Também pode optar por ocultar o plano inteiramente do Mercado Azure se a sua oferta for implementada como parte de uma aplicação ou modelo de solução gerido.

## <a name="free-trials"></a>Avaliações gratuitas

Você pode ativar um teste gratuito em planos para a máquina virtual Azure transactable e ofertas SaaS.

> [!NOTE]
> Esta secção explica testes gratuitos em planos pagos para ofertas que são vendidas através da Microsoft. Isto é diferente dos anúncios de teste gratuitos de parceiros que optam por processar as suas transações de forma independente. Pode criar uma listagem de teste gratuita (que não está associada a um plano) para os seguintes tipos de oferta:
> - Máquina virtual do Azure 
> - SaaS 
> - Dynamics 365 Business Central
> - Dinâmica 365 para Envolvimento de Clientes & PowerApps
> - Dynamics 365 for Operations
>
> Para obter mais informações sobre as opções de listagem, consulte [Determine a sua opção de publicação.](determine-your-listing-type.md)

Os testes gratuitos são apoiados para todos os modelos de faturação, exceto planos medidos. Os planos da SAAS permitem testes gratuitos de 1 mês. Os planos de máquina virtual Azure permitem testes gratuitos de 1, 3 ou 6 meses.

Quando um cliente seleciona um teste gratuito, recolhemos as suas informações de faturação, mas não começamos a faturar o cliente até que o julgamento seja convertido numa subscrição paga. Os ensaios gratuitos convertem-se automaticamente numa subscrição paga no final do ensaio, a menos que o cliente cancele a subscrição antes do fim do período experimental.

Durante o período experimental, os clientes podem avaliar qualquer um dos planos suportados dentro da mesma oferta que tem um teste gratuito habilitado. Se mudarem para um julgamento diferente dentro da mesma oferta, o período experimental não recomeçará. Por exemplo, se um cliente utilizar um teste gratuito durante 15 dias e, em seguida, mudar para um teste gratuito diferente para a mesma oferta, o novo período experimental será responsável por 15 dias utilizados. O teste gratuito que o cliente estava a avaliar quando o período experimental termina é aquele que é automaticamente convertido para uma subscrição paga.

Depois de um cliente selecionar um teste gratuito para um plano, não podem converter-se numa subscrição paga para esse plano até que o período experimental termine. Se um cliente optar por se converter num plano diferente dentro da oferta que não tenha um teste gratuito, a conversão acontecerá, mas o teste gratuito terminará imediatamente e quaisquer dados serão perdidos.

> [!NOTE]
> Depois de um cliente começar a pagar por um plano, não pode voltar a ter um teste gratuito na mesma oferta, mesmo que mude para outro plano que suporte testes gratuitos.

Para obter informações sobre subscrições de clientes que atualmente participam num teste gratuito, utilize a nova propriedade API `isFreeTrial` , que será marcada como verdadeira ou falsa. Para mais informações, consulte a [API de Assinatura SaaS Get](./partner-center-portal/pc-saas-fulfillment-api-v2.md#get-subscription).

## <a name="next-steps"></a>Passos seguintes

- Para saber como adicionar ou atualizar planos sobre uma oferta existente, consulte [Atualizar uma oferta existente no mercado comercial.](./partner-center-portal/update-existing-offer.md)
- Para saber mais sobre opções de transação e modelos de preços [associados, consulte as capacidades de transação do mercado comercial.](./marketplace-commercial-transaction-capabilities-and-considerations.md)
