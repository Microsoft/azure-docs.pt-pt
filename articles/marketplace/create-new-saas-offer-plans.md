---
title: Como criar planos para a sua oferta SaaS no Microsoft Partner Center
description: Como criar planos para um novo software como oferta de serviço (SaaS) usando o portal de marketplace comercial da Microsoft no Partner Center.
author: mingshen-ms
ms.author: mingshen
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 09/02/2020
ms.openlocfilehash: 63629f32c97611fa8bc57ecdac968552375125f4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89381203"
---
# <a name="how-to-create-plans-for-your-saas-offer"></a>Como criar planos para a sua oferta SaaS

As ofertas vendidas através do mercado comercial da Microsoft devem ter pelo menos um plano. Você pode criar uma variedade de planos com diferentes opções dentro da mesma oferta. Estes planos (por vezes referidos como SKUs) podem diferir em termos de versão, rentabilização ou níveis de serviço. Para obter orientações detalhadas sobre planos, consulte [planos e preços para ofertas de mercado comercial.](plans-pricing.md)

> [!NOTE]
> Se optar por processar transações de forma independente, não verá esta opção. Em vez disso, salte para [como comercializar a sua oferta SaaS.](create-new-saas-offer-marketing.md)

## <a name="create-a-plan"></a>Criar um plano

1. Perto do topo do separador **Plan,** selecione **+ Crie um novo plano.**

1. Na caixa de diálogo que aparece, na caixa **de identificação** do plano, introduza um ID de plano único. Utilize até 50 caracteres alfanuméricos minúsculos, traços ou sublinhados. Não é possível modificar o ID do plano depois de selecionar **Criar**.

1. Na caixa de **nomes do Plano,** insira um nome único para este plano. Use um máximo de 50 caracteres.

1. Selecione **Criar**.

## <a name="define-the-plan-listing"></a>Definir a listagem de planos

No separador **de listagem do Plano,** pode definir o nome e descrição do plano como pretende que apareçam no mercado comercial.

1. Na caixa de nomes do **Plano,** o nome que forneceu anteriormente para este plano aparece aqui. Pode mudá-lo a qualquer momento. Este nome aparecerá no mercado comercial como o título do plano de software da sua oferta.

1. Na caixa **de descrição** do Plano, explique o que torna este plano de software único e quaisquer diferenças em parte de outros planos dentro da sua oferta. Esta descrição pode conter até 500 caracteres.
1. **Selecione Guardar o rascunho** antes de continuar para o separador seguinte: Preços e **disponibilidade**.

## <a name="define-markets-pricing-and-availability"></a>Definir mercados, preços e disponibilidade

Todos os planos devem estar disponíveis em pelo menos um mercado. No **separador preços e disponibilidade,** pode configurar os mercados em que este plano estará disponível, o modelo de rentabilização, preço e faturação pretendidos. Além disso, pode indicar se deve tornar o plano visível para todos ou apenas para clientes específicos (também chamado de plano privado).

1. Em **Mercados**, selecione a ligação **de mercados de edição.**
1. Na caixa de diálogo que aparece, selecione as localizações do mercado onde pretende disponibilizar o seu plano. Deve selecionar um mínimo de um e máximo de 141 mercados.

   > [!NOTE]
   > Esta caixa de diálogo inclui uma caixa de pesquisa e uma opção para filtrar apenas em países "Tax Remitted", em que a Microsoft remete as vendas e usa o imposto em seu nome.

1. **Selecione Guardar**, para fechar a caixa de diálogo.

## <a name="define-a-pricing-model"></a>Definir um modelo de preços

Deve associar um modelo de preços a cada plano: taxa _fixa_ ou _por utilizador_. Todos os planos da mesma oferta devem utilizar o mesmo modelo de preços. Por exemplo, uma oferta não pode ter um plano que seja fixo e outro plano que seja por utilizador. Para mais informações, consulte [os modelos de preços da SaaS.](plan-saas-offer.md#saas-pricing-models)

> [!IMPORTANT]
> Após a publicação da sua oferta, não pode alterar o modelo de preços. Além disso, todos os planos para a mesma oferta devem partilhar o mesmo modelo de preços.

### <a name="configure-flat-rate-pricing"></a>Configurar preços fixos

1. No **separador preços e disponibilidade,** em **Preço,** selecione **Taxa Fixa**.
1. Selecione a caixa de verificação **mensal** ou **anual,** ou ambos e, em seguida, insira o preço.

### <a name="add-a-custom-meter-dimension"></a>Adicione uma dimensão de medidor personalizado

Esta opção só está disponível se selecionar preços fixos. Para mais informações, consulte [a faturação medido para o SaaS utilizando o serviço de medição do mercado comercial.](./partner-center-portal/saas-metered-billing.md)

1. Nas **dimensões do Serviço de Medição de Mercado,** selecione a **ligação Add a Custom Meter Dimension (Max 30).**
1. Na caixa **de identificação,** introduza a referência do identificador imutável enquanto emite eventos de utilização.
1. Na caixa **'Mostrar' Nome,** insira o nome de visor associado à dimensão. Por exemplo, "mensagens de texto enviadas".
1. Na **caixa Unidade de Medida,** introduza a descrição da unidade de faturação. Por exemplo, "por mensagem de texto" ou "por 100 e-mails".
1. No Preço por unidade na caixa **USD,** insira o preço de uma unidade da dimensão.
1. Na **quantidade mensal incluída na** caixa base, insira a quantidade (como um número inteiro) da dimensão que é incluída todos os meses para os clientes que pagam a taxa mensal recorrente. Para definir uma quantidade ilimitada, selecione a caixa de verificação.
1. Na **quantidade anual incluída na caixa-base,** insira a quantidade da dimensão (como um número inteiro) que é incluída todos os meses para os clientes que pagam a taxa anual recorrente. Para definir uma quantidade ilimitada, selecione a caixa de verificação.
1. Para adicionar outra dimensão personalizada do medidor, selecione o link **Adicionar outra dimensão** e, em seguida, repita os passos 1 a 7.

### <a name="configure-per-user-pricing"></a>Configurar por preço do utilizador

1. No **separador preços e disponibilidade,** em **Preços,** selecione **Por Utilizador**.
2. Se aplicável, nos **limites do Utilizador, especifique**o número mínimo e máximo de utilizadores para este plano.
3. De acordo com **o prazo de faturação,** especifique um preço mensal, um preço anual, ou ambos.

### <a name="validate-custom-prices"></a>Validar preços personalizados

Para definir preços personalizados num mercado individual, exportar, modificar e, em seguida, importar a folha de cálculo de preços. Você é responsável por validar este preço e possuir estas configurações. Para obter informações [detalhadas, consulte os preços personalizados.](plans-pricing.md#custom-prices)

1. Primeiro, tem de guardar as suas alterações de preços para permitir a exportação de dados de preços. Perto da parte inferior do **separador preços e disponibilidade,** selecione **Guardar o rascunho**.
1. No **preço,** selecione a **ligação de dados de preços de exportação.**
1. Abra o ficheiro exportedPrice.xlsx no Microsoft Excel.
1. Na folha de cálculo, faça as atualizações que pretende para as suas informações de preços e, em seguida, guarde as . Ficheiro CSV.<br> Pode ter de ativar a edição no Excel antes de poder atualizar o ficheiro.
2. No **separador preços e disponibilidade,** em **Preços,** selecione o link **de dados de preços de importação.**
3. Na caixa de diálogo que aparece, clique em **Sim**.
4. Selecione o ficheiro exportedPrice.xlsx atualizado e, em seguida, clique em **Abrir**.

### <a name="enable-a-free-trial"></a>Permitir um julgamento gratuito

Pode configurar um teste gratuito para cada plano na sua oferta. Selecione a caixa de verificação para permitir um teste gratuito de um mês. Esta caixa de verificação não está disponível para planos que utilizem o serviço de medição do mercado. Para mais informações, consulte [Testes gratuitos.](plans-pricing.md#free-trials)

> [!IMPORTANT]
> Depois de a sua oferta transacionável ter sido publicada com um teste gratuito, não pode ser desativada para esse plano. Certifique-se de que esta definição está correta antes de publicar a oferta para evitar ter de recriar o plano.

- Em **Teste Gratuito**, selecione a Caixa de **verificação de teste gratuita de um mês.**

## <a name="choose-who-can-see-your-plan"></a>Escolha quem pode ver o seu plano

Pode configurar cada plano para ser visível para todos ou apenas para um público específico. Você concede acesso a um plano privado usando iDs de inquilino com a opção de incluir uma descrição de cada identificação do inquilino que você atribui. Você pode adicionar um máximo de 10 IDs de inquilino manualmente ou até 20.000 IDs de inquilino usando um . Ficheiro CSV.

> [!NOTE]
> Se publicar um plano privado, pode mudar a sua visibilidade para público mais tarde. No entanto, uma vez publicado um plano público, não se pode mudar a sua visibilidade para privado.

### <a name="make-your-plan-public"></a>Tornar o seu plano público

1. Sob **visibilidade do Plano,** selecione a caixa **Pública.**
1. **Selecione Guardar**o projeto e, em seguida, na parte superior esquerda do separador, selecione **Plan overview** para voltar ao separador **visão geral** do Plano.
1. Para criar outro plano para esta oferta, perto do topo do separador **Plan,** selecione **+ Crie um novo plano.** Em seguida, repita os passos na secção [Criar um plano.](#create-a-plan) Caso contrário, vá [ver os seus planos.](#view-your-plans)

### <a name="manually-add-tenant-ids-for-a-private-plan"></a>Adicionar manualmente IDs de inquilino para um plano privado 

1. Sob **a visibilidade do Plano,** selecione a Esta é uma caixa **de plano privado.**
1. Na caixa **de identificação** do inquilino que aparece, insira a identificação do inquilino Azure AD do público que pretende conceder acesso a este plano privado. É necessário um mínimo de identificação de um inquilino.
1. (Opcional) Insira uma descrição deste público na caixa **Descrição.**
1. Para adicionar outro ID do inquilino, repita os passos 2 e 3.
1. Quando terminar de adicionar IDs de inquilino, **selecione Guardar**o rascunho e, em seguida, na parte superior esquerda do separador, selecione **Plan overview** para voltar ao separador **Desíduo plano.**
1. Para criar outro plano para esta oferta, perto do topo do separador **Plan,** selecione **+ Crie um novo plano.** Em seguida, repita os passos na secção [Criar um plano.](#create-a-plan) Caso contrário, vá [ver os seus planos.](#view-your-plans)

### <a name="use-a-csv-file-for-a-private-plan"></a>Use um . Arquivo CSV para um plano privado

1. Sob **a visibilidade do Plano,** selecione a Esta é uma caixa **de plano privado.**
2. Selecione a **ligação Export Audience (csv).**
3. Abra o . Ficheiro CSV e adicionar os IDs Azure que pretende conceder acesso à oferta privada à coluna **ID.**
4. Opcionalmente, introduza uma descrição para cada público na coluna **Descrição.**
5. Adicione "TenantID" na coluna **Tipo,** para cada linha com um ID Azure.
6. Salve o . Ficheiro CSV.
7. No **separador preços e disponibilidade,** sob **visibilidade do Plano,** selecione o link **Import Audience (csv).**
8. Na caixa de diálogo que aparece, selecione **Sim**.
9. Selecione o . Ficheiro CSV e, em seguida, selecione **Abrir**.
10. **Selecione Guardar**o projeto e, em seguida, na parte superior esquerda do separador, selecione **Plan overview** para voltar ao separador **visão geral** do Plano.
11. Para criar outro plano para esta oferta, no topo do separador **Plan,** selecione **+ Crie um novo plano.** Em seguida, repita os passos na secção [Criar um plano.](#create-a-plan) Caso contrário, se já acabou de criar planos, vá para a secção seguinte: **Veja os seus planos**.

## <a name="view-your-plans"></a>Veja os seus planos

Depois de criar um ou mais planos, verá o nome do seu plano, identificação de plano, modelo de preços, disponibilidade (público ou privado), estado de publicação atual e quaisquer ações disponíveis no separador **Plano.**

As ações disponíveis na coluna de **ação** do separador **Plano** variam consoante o estado do seu plano, podendo incluir o seguinte:

- Se o estado do plano for **Projeto,** o link na coluna **ação** dirá **eliminar o projeto**.
- Se o estado do plano for **live,** o link na coluna **Action** será ou Parar o **plano de venda** ou **sincronizar o público privado**. O link **de audiência privada Sync** publicará apenas as alterações ao seu público privado, sem publicar quaisquer outras atualizações que possa ter feito à oferta.

## <a name="next-steps"></a>Passos seguintes

- [Aprenda a comercializar a sua oferta SaaS](create-new-saas-offer-marketing.md) através da **Co-venda com a Microsoft** e Revenda através de programas **CSPs.**
- [Como testar e publicar uma oferta SaaS para o mercado comercial.](test-publish-saas-offer.md)
