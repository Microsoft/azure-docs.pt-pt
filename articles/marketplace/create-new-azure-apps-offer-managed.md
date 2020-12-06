---
title: Configurar um plano de candidatura gerido
description: Saiba como configurar um plano de candidatura gerido para a sua oferta de candidatura Azure no Partner Center.
author: aarathin
ms.author: aarathin
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 11/06/2020
ms.openlocfilehash: a67a721b8d94112347282e1cd9be4d43f4ac85da
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/06/2020
ms.locfileid: "96744640"
---
# <a name="configure-a-managed-application-plan"></a>Configurar um plano de candidatura gerido

Este artigo aplica-se apenas aos planos de candidatura geridos para uma oferta de candidatura da Azure. Se estiver a configurar um plano de modelo de solução, vá para [configurar um plano de modelo de solução](create-new-azure-apps-offer-solution.md).

## <a name="define-markets-pricing-and-availability"></a>Definir mercados, preços e disponibilidade

Todos os planos devem estar disponíveis em pelo menos um mercado. No **separador de Preços e Disponibilidade,** pode configurar os mercados em que este plano estará disponível, o preço, e se tornará o plano visível para todos ou apenas para clientes específicos (também chamado de plano privado).

1. Em **Mercados**, selecione a ligação **de mercados de edição.**
1. Na caixa de diálogo que aparece, selecione as localizações do mercado onde pretende disponibilizar o seu plano. Deve selecionar um mínimo de um e máximo de 141 mercados.

    > [!NOTE]
    > Esta caixa de diálogo inclui uma caixa de pesquisa e uma opção para filtrar apenas em países "Tax Remitted", em que a Microsoft remete as vendas e usa o imposto em seu nome.

1. **Selecione Guardar**, para fechar a caixa de diálogo.

## <a name="define-pricing"></a>Definir preços

Na **caixa de preços,** forneça o preço mensal para este plano. Este preço é além de qualquer infraestrutura Azure ou custos baseados na utilização incorridos pelos recursos utilizados por esta solução.

Além do preço mensal, também pode definir preços para consumo de unidades não standard usando [faturação medido.](partner-center-portal/azure-app-metered-billing.md) Pode definir o preço por mês para zero e cobrar exclusivamente usando faturação medido, se quiser.

Os preços são definidos em USD (USD = Dólar dos Estados Unidos) são convertidos na moeda local de todos os mercados selecionados usando as taxas de câmbio atuais quando guardados. Validar estes preços antes de publicar exportando a folha de cálculo de preços e revendo o preço em cada mercado. Se quiser definir preços personalizados num mercado individual, modifique e importe a folha de cálculo de preços.

### <a name="add-a-custom-meter-dimension-optional"></a>Adicione uma dimensão de medidor personalizado (opcional)

1. Nas **dimensões do Serviço de Medição de Mercado,** selecione a **ligação Add a Custom Meter Dimension (Max 18).**
1. Na caixa **de identificação,** introduza a referência do identificador imutável enquanto emite eventos de utilização.
1. Na caixa **'Mostrar' Nome,** insira o nome de visor associado à dimensão. Por exemplo, "mensagens de texto enviadas".
1. Na **caixa Unidade de Medida,** introduza a descrição da unidade de faturação. Por exemplo, "por mensagem de texto" ou "por 100 e-mails".
1. No Preço por unidade na caixa **USD,** insira o preço de uma unidade da dimensão.
1. Na **quantidade mensal incluída na** caixa base, insira a quantidade (como um número inteiro) da dimensão que é incluída todos os meses para os clientes que pagam a taxa mensal recorrente. Para definir uma quantidade ilimitada, selecione a caixa de verificação.
1. Para adicionar outra dimensão personalizada do medidor, repita os passos 1 a 7.

### <a name="set-custom-prices-optional"></a>Definir preços personalizados (opcional)

Os preços fixados em USD (USD = Dólar dos Estados Unidos) são convertidos na moeda local de todos os mercados selecionados utilizando as taxas de câmbio atuais quando guardados. Validar estes preços antes de publicar exportando a folha de cálculo de preços e revendo o preço em cada mercado. Se quiser definir preços personalizados num mercado individual, modifique e importe a folha de cálculo de preços.

Reveja cuidadosamente os seus preços antes de publicar, pois existem algumas restrições sobre o que pode mudar após a publicação de um plano.

> [!NOTE]
> Depois de publicado um preço para um mercado no seu plano, não pode ser alterado mais tarde.

Para definir preços personalizados num mercado individual, exportar, modificar e, em seguida, importar a folha de cálculo de preços. Você é responsável por validar este preço e possuir estas configurações. Para obter informações [detalhadas, consulte os preços personalizados.](plans-pricing.md#custom-prices)

1. Primeiro, tem de guardar as suas alterações de preços para permitir a exportação de dados de preços. Perto da parte inferior do **separador preços e disponibilidade,** selecione **Guardar o rascunho**.
1. No **preço,** selecione a **ligação de dados de preços de exportação.**
1. Abra o ficheiro exportedPrice.xlsx no Microsoft Excel.
1. Na folha de cálculo, faça as atualizações que pretende para as suas informações de preços e, em seguida, guarde o ficheiro.

   Pode ter de ativar a edição no Excel antes de poder atualizar o ficheiro.

1. No **separador preços e disponibilidade,** em **Preços,** selecione o link **de dados de preços de importação.**
1. Na caixa de diálogo que aparece, clique em **Sim**.
1. Selecione o ficheiro exportedPrice.xlsx atualizado e, em seguida, clique em **Abrir**.

## <a name="choose-who-can-see-your-plan"></a>Escolha quem pode ver o seu plano

Pode configurar cada plano para ser visível para todos ou apenas para um público específico. Você concede acesso a um público privado usando IDs de subscrição Azure com a opção de incluir uma descrição de cada ID de subscrição que você atribui. Pode adicionar um máximo de 10 IDs de subscrição manualmente ou até 10.000 IDs de subscrição usando um . Ficheiro CSV. Os IDs de assinatura Azure são representados como GUIDs e as letras devem ser minúsculas.

> [!NOTE]
> Se publicar um plano privado, pode mudar a sua visibilidade para público mais tarde. No entanto, uma vez publicado um plano público, não se pode mudar a sua visibilidade para privado.

No âmbito **da visibilidade** do Plano, faça uma das seguintes:

- Para tornar o plano público, selecione o botão de opção **Público** (também conhecido como botão de _rádio)._
- Para tornar o plano privado, selecione o botão de opção **Private** e, em seguida, adicione os IDs de assinatura Azure manualmente ou com um ficheiro CSV.

> [!NOTE]
> Um público privado ou restrito é diferente do público de pré-visualização que definiu no **separador Pré-visualização.** Um público de pré-visualização pode aceder à sua oferta antes da sua publicação ao vivo no mercado. Embora a escolha do público privado se aplique apenas a um plano específico, o público de pré-visualização pode ver todos os planos (privados ou não) para fins de validação.

### <a name="manually-add-azure-subscription-ids-for-a-private-plan"></a>Adicione manualmente IDs de subscrição Azure para um plano privado

1. Sob **a visibilidade do Plano,** selecione o botão de opção **Private.**
1. Na caixa **de ID de subscrição Azure** que aparece, insira o ID de subscrição Azure do público que pretende conceder acesso a este plano privado. É necessário um mínimo de um ID de subscrição.
1. (Opcional) Insira uma descrição deste público na caixa **Descrição.**
1. Para adicionar outro ID de subscrição, selecione o link **Add ID (Max 10)** e repita os passos 2 e 3.

### <a name="use-a-csv-file-to-add-azure-subscription-ids-for-a-private-plan"></a>Use um . Ficheiro CSV para adicionar IDs de subscrição Azure para um plano privado

1. Sob **a visibilidade do Plano,** selecione o botão de opção **Private.**
1. Selecione a **ligação Export Audience (csv).**
1. Abra o . Ficheiro CSV e adicionar os IDs de subscrição Azure que pretende conceder acesso à oferta privada à coluna **ID.**
1. Opcionalmente, introduza uma descrição para cada público na coluna **Descrição.**
1. Adicione "SubscriptionId" na coluna **Tipo,** para cada linha com um ID de subscrição.
1. Salve o . Ficheiro CSV.
1. No separador **Disponibilidade,** em **visibilidade do Plano,** selecione o link **Import Audience (csv).**
1. Na caixa de diálogo que aparece, selecione **Sim**.
1. Selecione o . Ficheiro CSV e, em seguida, selecione **Abrir**. Aparece uma mensagem indicando que . O ficheiro CSV foi importado com sucesso.

## <a name="define-the-technical-configuration"></a>Definir a configuração técnica

No separador **de configuração técnica,** irá carregar o pacote de implementação que permite aos clientes implementar o seu plano e fornecer um número de versão para o pacote. Também irá fornecer outras informações técnicas.

> [!NOTE]
> Este separador não será visível se optar por reutilizar pacotes a partir de outro plano no **separador de configuração** do Plano. Em caso afirmativo, vá [ver os seus planos.](#view-your-plans)

### <a name="assign-a-version-number-for-the-package"></a>Atribua um número de versão para o pacote

Na caixa **versão** fornece-se a versão atual da configuração técnica. Incremente esta versão sempre que publicar uma alteração nesta página. O número da versão deve estar no formato: inteiro.inteiro.inteiro. Por exemplo, `1.0.2`.

### <a name="upload-a-package-file"></a>Faça upload de um ficheiro de pacote

No **ficheiro Pacote (.zip)**, arraste o seu ficheiro de pacote para a caixa cinzenta ou selecione a **navegação para o(s)** link de ficheiros.

> [!NOTE]
> Se tiver um problema de upload de ficheiros, certifique-se de que a rede local não bloqueia o `https://upload.xboxlive.com` serviço utilizado pelo Partner Center.

#### <a name="previously-published-packages"></a>Pacotes previamente publicados

O sub-separador de **pacotes previamente publicado** permite-lhe visualizar todas as versões publicadas da sua configuração técnica.

### <a name="enable-just-in-time-jit-access-optional"></a>Ativar o acesso just-in-time (JIT) (opcional)

Para ativar o acesso do JIT a este plano, selecione a caixa de verificação **de acesso Enable just-in-time (JIT).** Para exigir que os consumidores da sua aplicação gerida concedam acesso permanente à sua conta, deixe esta opção desmarcada. Para saber mais sobre esta opção, consulte [o acesso just a tempo (JIT).](plan-azure-app-managed-app.md#just-in-time-jit-access)

### <a name="select-a-deployment-mode"></a>Selecione um modo de implementação

Selecione o modo de implementação **Completo** ou **Incremental.**

- No modo **Completo,** uma redistribuição da aplicação pelo cliente resultará na remoção de recursos no grupo de recursos geridos se os recursos não forem definidos no [mainTemplate.jsem](../azure-resource-manager/managed-applications/publish-service-catalog-app.md?tabs=azure-powershell#create-the-arm-template).
- No modo **Incremental,** uma redistribuição da aplicação deixa os recursos existentes inalterados.

Para saber mais sobre os modos de implementação, consulte os [modos de implementação do Gestor de Recursos Azure](../azure-resource-manager/templates/deployment-modes.md).

### <a name="provide-a-notification-endpoint-url"></a>Fornecer um URL de ponto final de notificação

Na caixa **DE URL do Ponto de Notificação,** forneça um ponto final HTTPS Webhook para receber notificações sobre todas as operações crud em instâncias de aplicação geridas desta versão do plano.

### <a name="customize-allowed-customer-actions-optional"></a>Personalizar ações permitidas para o cliente (opcional)

1. Para especificar quais as ações que os clientes podem realizar sobre os recursos geridos para além das `*/read` " ações " que estão disponíveis por padrão, selecione a caixa **de ações do cliente permitida para personalizar.**
1. Nas caixas que aparecem, forneça as ações de controlo adicionais e as ações de dados permitidas que pretende permitir que o seu cliente execute, separadas por pontos de snímia. Por exemplo, para permitir que os consumidores reiniciem as máquinas virtuais, adicione `Microsoft.Compute/virtualMachines/restart/action` à caixa **de ações de controlo permitida.**

### <a name="choose-who-can-manage-the-application"></a>Escolha quem pode gerir a aplicação

Indique quem deve ter acesso à gestão a esta aplicação gerida em cada região de Azure selecionada: _Global Azure_ e _Azure Government Cloud_. Utilizará identidades Azure AD para identificar os utilizadores, grupos ou aplicações que pretende conceder permissão ao grupo de recursos geridos. Para mais informações, consulte [Plan a Azure geriu um pedido para uma oferta de aplicação Azure.](plan-azure-application-offer.md)

Complete os seguintes passos para Global Azure e Azure Government Cloud, conforme aplicável.

1. Na caixa de ID do **Azure Ative Directory Tenant,** insira o ID do Inquilino Azure AD (também conhecido como ID de diretório) contendo as identidades dos utilizadores, grupos ou aplicações a que pretende conceder permissões.
1. Na caixa principal de **identificação,** forneça o ID do objeto Azure do utilizador, grupo ou aplicação que pretende ser autorizado ao grupo de recursos geridos. Identifique o utilizador pelo seu ID principal, que pode ser encontrado na lâmina de utilizadores do [Azure Ative Directory](https://portal.azure.com/#blade/Microsoft_AAD_IAM/UsersManagementMenuBlade/AllUsers) no portal Azure.
1. Na lista **de definição de funções,** selecione uma função Ad AD Ad. A função selecionada descreve as permissões que o principal terá sobre os recursos na subscrição do cliente.
1. Para adicionar outra autorização, selecione a **ligação de autorização Add (máx. 100)** e repita os passos 1 a 3.

### <a name="policy-settings-optional"></a>Definições de política (opcional)

Pode configurar um máximo de cinco políticas e apenas um exemplo de cada opção Políticas. Algumas políticas requerem parâmetros adicionais.

1. Nas **definições de Política**, selecione a política + Adicionar a política **(máx. 5).**
1. Na caixa **Nome,** insira o nome de atribuição de política (limitado a 50 caracteres).
1. A partir da caixa de **listas Políticas,** selecione a política Azure que será aplicada aos recursos criados pela aplicação gerida na subscrição do cliente.
1. Na caixa **de parâmetros de política,** forneça o parâmetro sobre o qual devem ser aplicadas as políticas de auditoria e diagnóstico.
1. A partir da caixa de lista SKU de **política,** selecione o tipo SKU de política.

    > [!NOTE]
    > A _política padrão_ SKU é necessária para políticas de auditoria.

## <a name="view-your-plans"></a>Veja os seus planos

- **Selecione Guardar** o rascunho e, em seguida, na parte superior esquerda da página, selecione **Plan overview** para voltar à página geral do **Plano.**

Depois de criar um ou mais planos, verá o nome do seu plano, o ID do plano, o tipo de plano, a disponibilidade (Público ou Privado), o estado atual da publicação e quaisquer ações disponíveis no separador **Plano.**

As ações disponíveis na coluna de **ação** do separador **Plano** variam consoante o estado do seu plano, podendo incluir o seguinte:

- Se o estado do plano for **Projeto,** o link na coluna **ação** dirá **eliminar o projeto**.
- Se o estado do plano for **live,** o link na coluna **Action** será **stop selling plan** ou Sync private **audience**. O link **de audiência privada Sync** publicará apenas as alterações ao seu público privado, sem publicar quaisquer outras atualizações que possa ter feito à oferta.
- Para criar outro plano para esta oferta, no topo do separador **Plan,** selecione **+ Crie um novo plano.** Em seguida, repita os passos em [Como criar planos para a sua oferta de candidatura Azure](create-new-azure-apps-offer-plans.md). Caso contrário, se já acabou de criar planos, vá para a próxima secção: Próximos passos.

## <a name="next-steps"></a>Passos seguintes

- [Como testar e publicar a sua oferta de Aplicação Azure.](create-new-azure-apps-offer-test-publish.md)
- Saiba [como vender a sua oferta de Aplicação Azure](create-new-azure-apps-offer-marketing.md) através da Co-venda com a Microsoft e Revenda através de programas CSPs.