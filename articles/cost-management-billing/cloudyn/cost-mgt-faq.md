---
title: Perguntas mais frequentes sobre a Cloudyn no Azure
description: Saiba como utilizar o portal da Cloudyn para resolver problemas comuns de configuração empresarial indireta e responder a outras perguntas frequentes.
author: bandersmsft
ms.author: banders
ms.date: 10/23/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.subservice: cloudyn
ms.reviewer: benshy
ms.custom: seodec18
ROBOTS: NOINDEX
ms.openlocfilehash: 92a62de4223d2ec6d0441840b90fdce20c656d02
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/26/2020
ms.locfileid: "92546233"
---
# <a name="frequently-asked-questions-for-cloudyn"></a>Perguntas mais frequentes sobre a Cloudyn

Este artigo aborda algumas questões comuns sobre a Cloudyn. Se tiver dúvidas sobre a Cloudyn, pode utilizar a página [FAQs for Cloudyn](https://social.msdn.microsoft.com/Forums/en-US/231bf072-2c71-4121-8339-ac9d868137b9/faqs-for-cloudyn-cost-management?forum=Cloudyn) (FAQs sobre a Cloudyn).

[!INCLUDE [cloudyn-note](../../../includes/cloudyn-note.md)]

## <a name="how-can-i-resolve-common-indirect-enterprise-setup-problems"></a>Como posso resolver problemas comuns de configuração da empresa indireta?

Ao utilizar o portal do Cloudyn pela primeira vez, poderão ser apresentadas as seguintes mensagens se for utilizador do Contrato Enterprise ou Fornecedor de Soluções Cloud (CSP):

- “A chave de API especificada não é uma chave de inscrição de nível superior”, apresentada no assistente de **Configuração da Cloudyn** .
- “Inscrição Direta – Não”, apresentada no portal do Contrato Enterprise.
- “Não foram encontrados dados de utilização nos últimos 30 dias. Contacte o seu distribuidor para garantir que a marcação foi ativada para a sua conta do Azure”, apresentada no portal da Cloudyn.

As mensagens anteriores indicam que adquiriu um Contrato Enterprise do Azure através de um revendedor ou CSP. O seu revendedor ou CSP tem de ativar a _marcação_ para a sua conta do Azure, para que possa ver os dados no Cloudyn.

Eis como resolver os problemas:

1. O seu revendedor tem de ativar a _marcação_ para a sua conta. Para obter instruções, consulte o [Guia de Introdução do Cliente Indireto](https://ea.azure.com/api/v3Help/v2IndirectCustomerOnboardingGuide).

2. A chave do Contrato Enterprise do Azure para utilizar com o Cloudyn é gerida por si.

Apenas um administrador de serviços do Azure pode ativar o Cloudyn. As permissões de coadministrador são insuficientes.

Para poder gerar a chave de API do Contrato Enterprise do Azure, para configurar o Cloudyn, tem de ativar a API de Faturação do Azure ao seguir as instruções em:

- [Descrição geral de APIs de Relatórios para clientes Enterprise](../manage/enterprise-api.md)
- [API de Relatórios do portal empresarial do Microsoft Azure](https://ea.azure.com/helpdocs/reportingAPI) em **Ativar o acesso a dados para a API**


Também poderá ter de conceder permissões a administradores de departamento, proprietários de conta e administradores empresariais para _ver custos_ com a API de Faturação.

## <a name="why-dont-i-see-optimizer-recommendations"></a>Por que motivo não vejo as recomendações do Otimizador?

As informações de recomendação só estão disponíveis para contas ativadas. Não verá qualquer informação de recomendação nas categorias de relatório **Otimizador** para contas *desativadas* , incluindo:

- Gestor de Otimização
- Otimização do Dimensionamento
- Ineficiências

Se não conseguir ver dados de recomendação do Otimizador, o mais provável é que tenha contas desativadas. Para ativar uma conta, tem de a registar com as suas credenciais do Azure.

Para ativar uma conta:

1.    No portal do Cloudyn, clique em **Definições** na parte superior direita e selecione **Contas da Cloud** .
2.    No separador Contas do Microsoft Azure, procure contas com uma subscrição **desativada** .
3.    À direita de uma conta desativada, clique no símbolo **editar** , semelhante a um lápis.
4.    O ID do inquilino e o ID da tarifa são detetados automaticamente. Clique em **Seguinte** .
5.    Será redirecionado para o portal do Azure. Inicie sessão no portal e autorize o Cloudyn Collector a aceder aos dados do Azure.
6.    Em seguida, será redirecionado para a página Gestão de contas da Cloudyn e a sua subscrição será atualizada com o Estado de Conta **ativo** . É apresentado um símbolo de marca de verificação verde.
7.    Se não for apresentado um símbolo de marca de verificação verde para uma ou mais subscrições, significa que não tem permissões para criar uma aplicação de leitor (CloudynCollector) para a subscrição. Os passos 3 e 4 têm de ser repetidos por um utilizador com permissões superiores na subscrição.  

Depois de concluir os passos anteriores, pode ver as recomendações do Otimizador dentro de um a dois dias. No entanto, pode demorar até cinco dias para que sejam disponibilizados todos os dados de otimização.


## <a name="how-do-i-enable-suspended-or-locked-out-users"></a>Como posso ativar utilizadores suspensos ou bloqueados?

Primeiro, vamos ver o cenário mais comum que faz com que as contas de utilizador fiquem com o estado *initiallySuspended* .

> O Admin1 pode ser um Fornecedor de Soluções Cloud da Microsoft ou um utilizador do Contrato Enterprise. A organização está preparada para começar a utilizar a Cloudyn.  Regista-se através do portal do Azure e inicia sessão no portal da Cloudyn. Como pessoa que regista o serviço da Cloudyn e inicia sessão no portal da Cloudyn, o Admin1 torna-se no *administrador principal* . O Admin1 não cria contas de utilizador. No entanto, através do portal da Cloudyn, cria contas do Azure e configura uma hierarquia de entidades. O Admin1 informa o Admin2, um administrador inquilino, que tem de se registar na Cloudyn e iniciar sessão no portal da Cloudyn.
>
> O Admin2 regista-se através do portal do Azure. No entanto, quando tenta iniciar sessão no portal da Cloudyn, recebe um erro a indicar que a conta está **suspensa** . O administrador principal, Admin1, é notificado da suspensão da conta. O Admin1 tem de ativar a conta do Admin2 e conceder *acesso de entidade de administração* às entidades apropriadas, permitir o acesso de gestão de utilizadores e ativar a conta de utilizador.


Se receber um alerta com um pedido para permitir o acesso a um utilizador, terá de ativar a conta de utilizador.

Para ativar a conta de utilizador:

1. Inicie sessão na Cloudyn com a conta de utilizador administrativa do Azure que utilizou para configurar a Cloudyn. Em alternativa, inicie sessão com uma conta de utilizador que tenha acesso de administrador.
2. Selecione o símbolo de engrenagem no canto superior direito e selecione **Gestão de Utilizadores** .
3. Localize o utilizador, selecione o símbolo de lápis e, em seguida, edite o utilizador.
4. Em **Estado do utilizador** , altere o estado de **Suspenso** para **Ativo** .

As contas de utilizador da Cloudyn ligam-se através de início de sessão único no Azure. Se um utilizador introduzir incorretamente a palavra-passe, poderá ficar bloqueado na Cloudyn, mesmo que ainda consiga aceder ao Azure.

Se alterar o endereço de e-mail predefinido no Azure na Cloudyn, a conta poderá ficar bloqueada. Pode mostrar “estado initiallySuspended”. Se a conta de utilizador estiver bloqueada, contacte um administrador alternativo para a repor.

Recomendamos que crie, pelo menos, duas contas de administrador da Cloudyn no caso de uma delas ficar bloqueada.

Se não conseguir iniciar sessão no portal da Cloudyn, verifique se está a utilizar o URL correto para iniciar sessão na Cloudyn. [Utilize](https://ms.portal.azure.com/#blade/Microsoft_Azure_CostManagement/CloudynMainBlade)https://azure.cloudyn.com.

Evite utilizar o URL direto da Cloudyn `https://app.cloudyn.com`.

## <a name="how-do-i-activate-unactivated-accounts-with-azure-credentials"></a>Como posso ativar contas desativadas com as credenciais do Azure?

Assim que as suas contas do Azure forem detetadas pela Cloudyn, os dados dos custos serão imediatamente apresentados nos relatórios baseados nos custos. No entanto, para que a Cloudyn disponibilize os dados da utilização e do desempenho, terá de registar as suas credenciais do Azure para as contas. Para obter instruções, veja [Adicionar uma conta ou atualizar uma subscrição](activate-subs-accounts.md#add-an-account-or-update-a-subscription).

Para adicionar as credenciais do Azure para uma conta, no portal da Cloudyn, selecione o símbolo de edição à direita do nome da conta, não da subscrição.

Até que as suas credenciais do Azure sejam adicionadas à Cloudyn, a conta aparece como _desativada_ .

## <a name="how-do-i-add-multiple-accounts-and-entities-to-an-existing-subscription"></a>Como posso adicionar várias contas e entidades a uma subscrição existente?

São utilizadas entidades adicionais para adicionar mais Contratos Enterprise a uma subscrição da Cloudyn. Para obter mais informações, veja [Criar e gerir entidades](tutorial-user-access.md#create-and-manage-entities).

Para CSPs:

Para adicionar contas CSP adicionais a uma entidade, selecione **Acesso MSP** em vez de **Enterprise** quando criar a nova entidade. Se a sua conta estiver registada como Contrato Enterprise e quiser adicionar credenciais CSP, o pessoal de suporte da Cloudyn poderá ter de modificar as definições da sua conta. Se for um subscritor do Azure pago, poderá criar um novo pedido de suporte no portal do Azure. Selecione **Ajuda + suporte** e, em seguida, **Novo pedido de suporte** .

## <a name="currency-symbols-in-cloudyn-reports"></a>Símbolos de moeda nos relatórios da Cloudyn

Pode ter várias contas do Azure a utilizar moedas diferentes. No entanto, os relatórios de custos na Cloudyn não mostram mais do que um tipo de moeda por relatório.

Se tiver várias subscrições a utilizar moedas diferentes, as moedas da entidade principal e da entidade subordinada serão apresentadas em **$**  USD. A nossa melhor prática sugerida é evitar a utilização de moedas diferentes na mesma hierarquia de entidades. Por outras palavras, todas as subscrições organizadas numa estrutura de entidade devem utilizar a mesma moeda.

A Cloudyn deteta automaticamente a moeda de subscrição do Contrato Enterprise e apresenta-a corretamente nos relatórios.  No entanto, a Cloudyn apenas apresenta **$**  USD para contas CSP e Web Direct do Azure.

## <a name="what-are-cloudyn-data-refresh-timelines"></a>O que são linhas cronológicas de atualização de dados da Cloudyn?

A Cloudyn tem as seguintes linhas cronológicas de atualização de dados:

- **Inicial** : depois da configuração, pode demorar até 24 horas para ver os dados dos custos na Cloudyn. Também pode demorar até 10 dias para a Cloudyn recolher dados suficientes para apresentar recomendações de dimensionamento.
- **Diariamente** : do décimo dia até ao final de cada mês, a Cloudyn deverá mostrar os dados atualizados do dia anterior após cerca de UTC+3 no dia seguinte.
- **Mensalmente** : do primeiro ao décimo dia de cada mês, a Cloudyn pode mostrar os dados apenas até ao final do mês anterior.

A Cloudyn processa os dados do dia anterior quando estão disponíveis os dados completos do dia anterior. Os dados do dia anterior estão geralmente disponíveis na Cloudyn cerca de UTC+3 a cada dia. O processamento de alguns dados, como etiquetas, pode demorar mais 24 horas.

Os dados do mês atual não estão disponíveis para recolha no início de cada mês. Durante o período, os fornecedores de serviços finalizam a faturação do mês anterior. Os dados do mês anterior aparecem na Cloudyn 5 a 10 dias após o início de cada mês. Durante este tempo, só pode ver os custos amortizados do mês anterior. Pode não ver dados de faturação ou utilização diários. Quando os dados ficam disponíveis, a Cloudyn processa-os retroativamente. Após o processamento, todos os dados mensais são apresentados entre o quinto e o décimo dia de cada mês.

Se houver um atraso no envio dos dados do Azure para a Cloudyn, os dados ainda continuarão registados no Azure. Os dados são transferidos para a Cloudyn quando a ligação for restaurada.

## <a name="cost-fluctuations-in-cloudyn-cost-reports"></a>Flutuações de custos em Relatórios de Custos da Cloudyn

Os relatórios de custos podem mostrar flutuações de custos sempre que os fornecedores de serviços cloud enviam ficheiros de faturação atualizados. Os custos flutuantes ocorrem quando são recebidos novos ficheiros de um fornecedor de serviços cloud fora do agendamento de relatórios diários ou mensais habitual. As alterações de custos não resultam do recálculo da Cloudyn.

Ao longo do mês, todos os ficheiros de faturação enviados pelo fornecedor de serviços cloud são uma estimativa dos seus custos diários. Por vezes, os dados são atualizados com frequência, ocasionalmente várias vezes por dia. As atualizações são mais frequentes com o AWS do que com o Azure. Os custos totais devem permanecer estáveis quando o cálculo da faturação do mês anterior estiver concluído e for recebido o ficheiro de faturação final. Normalmente, no dia 10 do mês.

As alterações ocorrem quando recebe ajustamentos de custos do seu fornecedor de serviços cloud. Receber créditos é um exemplo. As alterações podem ocorrer meses após o encerramento do mês relevante. As alterações são mostradas sempre que é feito um recálculo pelo fornecedor de serviços cloud. A Cloudyn atualiza os dados históricos para garantir que todos os ajustes são recalculados. Verifica também se os custos são apresentados com exatidão nos relatórios.

## <a name="how-can-a-direct-csp-configure-cloudyn-access-for-indirect-csp-customers-or-partners"></a>Como pode um CSP direto configurar o acesso da Cloudyn para clientes ou parceiros CSP indiretos?

Para obter instruções, veja [Configurar o acesso CSP indireto na Cloudyn](quick-register-csp.md#configure-indirect-csp-access-in-cloudyn).

## <a name="what-causes-the-optimizer-menu-item-to-appear"></a>O que faz com que o item de menu do Otimizador apareça?

Depois de adicionar o acesso do Azure Resource Manager e os dados serem recolhidos, deverá ver a opção **Otimizador** . Para ativar o acesso do Azure Resource Manager, veja [Como posso ativar contas desativadas com as credenciais do Azure?](#how-do-i-activate-unactivated-accounts-with-azure-credentials)

## <a name="is-cloudyn-agent-based"></a>A Cloudyn baseia-se em agentes?

Não. Não são utilizados agentes. Os dados de métricas das máquinas virtuais do Azure para VMs são recolhidos na API do Microsoft Insights. Se quiser recolher dados de métricas de VMs do Azure, estas precisarão de ter as definições de diagnóstico ativadas.

## <a name="do-cloudyn-reports-show-more-than-one-ad-tenant-per-report"></a>Os relatórios da Cloudyn mostram mais do que um inquilino do AD por relatório?

Sim. Pode [criar uma entidade de conta na cloud correspondente](tutorial-user-access.md#create-and-manage-entities) para cada inquilino do AD que tem. Em seguida, pode ver todos os dados do inquilino do Azure Active Directory e outros fornecedores de plataformas da cloud, incluindo o Amazon Web Services e o Google Cloud Platform.
