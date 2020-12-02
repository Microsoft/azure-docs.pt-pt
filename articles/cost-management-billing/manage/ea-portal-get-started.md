---
title: Introdução ao portal Azure Enterprise
description: Este artigo explica como é que os clientes do Contrato Enterprise do Azure (Azure EA) utilizam o portal Azure Enterprise.
author: bandersmsft
ms.reviewer: baolcsva
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 10/28/2020
ms.author: banders
ms.custom: contperfq1
ms.openlocfilehash: 0f4f4d934361f1e59eceaaccca680f0cf155bb04
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/30/2020
ms.locfileid: "96348964"
---
# <a name="get-started-with-the-azure-enterprise-portal"></a>Introdução ao portal Azure Enterprise

Este artigo ajuda os clientes diretos e indiretos do Contrato Enterprise do Azure (Azure EA) a começarem a utilizar o [portal Azure Enterprise](https://ea.azure.com). Obtenha informações sobre:

- A estrutura do portal Azure Enterprise.
- Funções utilizadas no portal Azure Enterprise.
- Criação de subscrição.
- Análise de custos no portal Azure Enterprise e no portal do Azure.

## <a name="get-started-with-ea-onboarding"></a>Introdução à inclusão do EA

Para obter um guia de inclusão do Azure EA, veja o [Azure EA Onboarding Guide](https://ea.azure.com/api/v3Help/v2AzureEAOnboardingGuide) (Guia de Inclusão do Azure EA).

Assista ao vídeo para ver uma sessão de inclusão completa do portal Azure Enterprise:

> [!VIDEO https://www.youtube.com/embed/OiZ1GdBpo-I]

## <a name="understanding-ea-user-roles-and-introduction-to-user-hierarchy"></a>Compreensão das funções de utilizador do EA e introdução à hierarquia de utilizadores

Para ajudar a gerir a utilização e o gasto da sua organização, os clientes do Azure com um Contrato Enterprise (EA) podem atribuir cinco funções administrativas distintas:

- Administrador do Enterprise (EA)
- Administrador do Enterprise (só de leitura)
- Administrador de Departamento
- Administrador de Departamento (só de leitura)
- Proprietário da Conta

Cada função tem um grau variável de limites e permissões de utilizador. Para obter mais informações, veja [Estrutura e permissões da organização por função](./understand-ea-roles.md#organization-structure-and-permissions-by-role).

## <a name="activate-your-enrollment-create-a-subscription-and-other-administrative-tasks"></a>Ativar o registo, criar uma subscrição e outras tarefas administrativas

Para obter mais informações sobre como ativar o seu registo, criar um departamento ou uma subscrição, adicionar administradores e proprietários de conta, e outras tarefas administrativas, veja [Administração do Azure EA Portal](./ea-portal-administration.md).

Se quiser saber mais sobre como transferir uma subscrição Enterprise para uma subscrição Pay As You Go, veja [Transferências do Azure Enterprise](./ea-transfers.md).

## <a name="view-usage-summary-and-download-reports"></a>Ver o resumo de utilização e os relatórios de transferências

Pode gerir e agir na fatura do Azure EA. A fatura é uma representação da sua conta e deve analisar a sua precisão.

Para ver o resumo de utilização, transferir relatórios e gerir faturas de registo, veja [Faturas de registo do Azure Enterprise](./ea-portal-enrollment-invoices.md).

## <a name="now-that-youre-familiar-with-the-basics-here-are-some-additional-links-to-help-you-get-onboarded"></a>Agora que está familiarizado com os conceitos básicos, seguem-se algumas ligações adicionais para o ajudar na integração

Os [preços do Azure EA](./ea-pricing-overview.md) fornecem detalhes sobre como é calculada a utilização e os custos de vários serviços do Azure no Contrato Enterprise em que os cálculos são mais complexos.

Se quiser saber mais sobre como as reservas do Azure para instâncias reservadas de VM o podem ajudar a economizar dinheiro com a sua inscrição Enterprise, veja [Instâncias reservadas de VM do Azure EA](./ea-portal-vm-reservations.md).

Para obter informações sobre que APIs REST deve utilizar no registo do Azure Enterprise e uma explicação sobre como resolver problemas comuns com APIs REST, veja [APIs REST do Azure Enterprise](./ea-portal-rest-apis.md).

[Contratos e emendas do Azure EA](./ea-portal-agreements.md) descreve como os contratos e as emendas do Azure EA podem afetar o acesso, a utilização e os pagamentos dos serviços do Azure.

[Azure Marketplace](./ea-azure-marketplace.md) explica como os clientes e parceiros EA podem ver os custos do marketplace e permitir compras no Azure Marketplace.

Para obter explicações sobre as tarefas comuns que um administrador do EA para parceiros realiza no Azure EA Portal, veja [Administração do Azure EA Portal para parceiros](./ea-partner-portal-administration.md).

## <a name="get-started-on-azure-ea---faq"></a>Introdução ao Azure EA – FAQ

Esta secção apresenta os detalhes sobre as perguntas normalmente colocadas pelos clientes durante o processo de inclusão.  

### <a name="i-accidentally-associated-my-existing-azure-account-with-azure-ea-enrollment-as-a-result-i-lost-my-monthly-credit-can-i-get-my-monthly-credit-back"></a>Associei acidentalmente a minha conta do Azure existente à inscrição Azure EA. Como resultado, perdi o meu crédito mensal. Posso ter o meu crédito mensal de volta?

Se tiver iniciado sessão como um proprietário de conta Azure EA com as mesmas credenciais da subscrição do Visual Studio, poderá recuperar o benefício individual do Azure da subscrição do Visual Studio ao executar uma das seguintes ações:

- Elimine o proprietário da conta do portal Azure Enterprise, depois de remover ou mover quaisquer subscrições do Azure associadas. Em seguida, adira novamente aos benefícios individuais do Azure do Visual Studio.
- Elimine o subscritor do Visual Studio do site de administração no VLSC e reatribua a subscrição, desta vez, a uma conta com credenciais diferentes. Em seguida, adira novamente aos benefícios individuais do Azure do Visual Studio.

### <a name="what-type-of-subscription-should-i-create"></a>Que tipo de subscrição devo criar?

O portal Azure Enterprise oferece dois tipos de subscrições para clientes Enterprise:

- Microsoft Azure Enterprise, que é ideal para:
  - Todas as utilizações de produção
  - Os melhores preços com base no gasto da infraestrutura

  Para obter mais informações, [contacte as vendas do Azure](https://azure.microsoft.com/pricing/enterprise-agreement/).

- Enterprise Dev/Test, que é ideal para:
  - Todas as cargas de trabalho de programador/teste da equipa
  - Cargas de trabalho de programador/teste individuais médias a pesadas
  - Acesso a imagens especiais do MSDN e taxas de serviço preferenciais

  Para obter mais informações, veja a [oferta Enterprise Dev/Test](https://azure.microsoft.com/offers/ms-azr-0148p/).

### <a name="my-subscription-name-is-the-same-as-the-offer-name-should-i-change-the-subscription-name-to-something-meaningful-to-my-organization"></a>O nome da minha subscrição é o mesmo que o nome da oferta. Devo alterar o nome da subscrição para algo significativo para a minha organização?

Quando cria uma subscrição, o nome é predefinido para o tipo de oferta que escolher. Recomendamos que altere o nome da subscrição para algo que facilite o controlo da subscrição.

Para alterar o nome:

1. Inicie sessão em [https://account.windowsazure.com](https://account.windowsazure.com).
1. Selecione a lista de subscrições.
1. Selecione a subscrição que quer editar.
1. Selecione o ícone **Gerir Subscrição**.
1. Edite os detalhes da subscrição.

### <a name="how-can-i-track-costs-incurred-by-a-cost-center"></a>Como posso controlar os custos incorridos por um centro de custos?

Para controlar o custo pelo centro de custos, precisa de definir o centro de custos para um dos seguintes níveis:

- Departamento
- Conta
- Subscrição

Consoante a necessidade, pode utilizar o mesmo centro de custos para controlar a utilização e os custos associados a um centro de custos em particular.

Por exemplo, para controlar os custos de um projeto especial em que estão envolvidos vários departamentos, talvez deva utilizar o centro de custo ao nível da subscrição para controlar a utilização e os custos.

Não pode definir um centro de custos ao nível do serviço. Se quiser controlar a utilização ao nível de serviço, poderá utilizar a funcionalidade _Etiqueta_ disponível ao nível do serviço.

### <a name="how-do-i-track-usage-and-spend-by-different-departments-in-my-organization"></a>Como posso controlar a utilização e o gasto por diferentes departamentos na minha organização?

Pode criar a quantidade de departamentos que quiser na inscrição Azure EA. Para controlar a utilização de forma correta, precisa de garantir que não está a partilhar as subscrições entre os departamentos.

Depois de ter criado departamentos e subscrições, pode ver os dados no relatório de utilização. Estas informações podem ajudar a controlar a utilização e a gerir o custo e os gastos ao nível do departamento.

Também pode aceder aos dados de utilização através da API de relatórios. Para obter informações detalhadas e código de exemplo, veja as [APIs REST Empresariais do Azure](./ea-portal-rest-apis.md).

### <a name="can-i-set-a-spending-quota-and-get-alerts-as-i-approach-my-limit"></a>Posso definir uma quota de gastos e obter alertas à medida que me aproximo do meu limite?

Pode definir uma quota de gastos ao nível do departamento. O sistema vai notificá-lo automaticamente à medida que os limites de gastos atinjam os 50%, 75%, 90% e 100% da quota definida por si.

Para definir a quota de gastos, selecione um departamento e, em seguida, selecione o ícone para editar. Depois de editar os detalhes do limite de gastos, selecione **Guardar**.

### <a name="i-used-resource-groups-to-implement-rbac-and-track-usage-how-can-i-view-the-associated-usage-details"></a>Utilizei grupos de recursos para implementar o RBAC e controlar a utilização. Como posso ver os detalhes de utilização associados?

Se utilizar _grupos de recursos_ e _etiquetas_, estas informações são controladas ao nível do serviço e pode aceder às mesmas no ficheiro de transferência de utilização detalhada (CSV). Veja o [relatório de utilização de transferências](https://ea.azure.com/report/downloadusage) no portal Azure Enterprise.

Também pode aceder à utilização através da API. Para obter informações detalhadas e código de exemplo, veja as [APIs REST Empresariais do Azure](./ea-portal-rest-apis.md).

> [!NOTE]
> Só pode aplicar etiquetas em recursos que suportem operações do Azure Resource Manager. Caso tenha criado uma máquina virtual, uma rede virtual ou um armazenamento através do modelo de implementação clássica (por exemplo, através do portal clássico), não poderá aplicar etiquetas a esse recurso. Deve implementar esses recursos novamente através do Resource Manager para suportar as etiquetas. Todos os outros recursos suportam etiquetas.

### <a name="can-i-perform-analyses-using-power-bi"></a>Posso executar análises com o Power BI?

Sim. Com o pacote de conteúdos do Microsoft Azure Enterprise para o Power BI, pode:

- Importar e analisar rapidamente o consumo do Azure para a inscrição Enterprise.
- Saber qual o departamento, a conta ou a subscrição que consumiu mais utilização.
- Saber qual o serviço mais utilizado pela organização.
- Monitorizar as tendências de utilização e gastos.

Para utilizar o Power BI:

1. Aceda ao site do Power BI.
1. Inicie sessão com uma conta escolar ou profissional válida.

   A conta escolar ou profissional pode ser a mesma ou diferente da que é utilizada para aceder à inscrição através do portal Azure Enterprise.
1. No dashboard de serviços, escolha o mosaico Microsoft Azure Enterprise e selecione **Ligar**.
1. No ecrã **Ligar ao Azure Enterprise**, introduza:
    - URL do Ambiente do Azure: [https://ea.azure.com](https://ea.azure.com)
    - Número de Meses: entre 1 e 36
    - Número da Inscrição: o seu número de inscrição
1. Selecione **Seguinte**.
1. Introduza a Chave de API na caixa da **Chave de Autenticação**.

   Encontra a chave de API no portal Azure Enterprise. Procure no separador **Transferir Utilização** e, em seguida, selecione **Chave de Acesso da API**. Copie e cole a chave na caixa **Chave da Conta** no Power BI.

Dependendo do tamanho do conjunto de dados, o carregamento dos dados no Power BI pode demorar entre cinco a 30 minutos.

Os relatórios do Power BI estão disponíveis para clientes Azure EA diretos, parceiros e indiretos que possam ver as informações de faturação.

## <a name="next-steps"></a>Passos seguintes

- Os administradores do portal Azure Enterprise devem ler o artigo [Azure Enterprise portal administration](ea-portal-administration.md) (Administração do portal Azure Enterprise) para saber mais sobre as tarefas administrativas comuns.
- Se precisar de ajuda na resolução de problemas com o portal Azure Enterprise, veja [Troubleshoot Azure Enterprise portal access](ea-portal-troubleshoot.md) (Resolver problemas de acesso do portal Azure Enterprise).