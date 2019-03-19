---
title: Perguntas mais frequentes sobre o Cloudyn no Azure | Documentos da Microsoft
description: Este artigo fornece respostas para algumas das perguntas comuns sobre o Cloudyn.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 03/14/2018
ms.topic: troubleshooting
ms.service: cost-management
manager: benshy
ms.custom: seodec18
ms.openlocfilehash: 6ebd25dc65f86e5e028c1c56943756b46d0db9a8
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58011810"
---
# <a name="frequently-asked-questions-for-cloudyn"></a>Perguntas mais frequentes sobre Cloudyn

Este artigo aborda algumas perguntas comuns sobre o Cloudyn. Se tiver dúvidas sobre o Cloudyn, pode pedi-los quando [FAQs sobre a Cloudyn](https://social.msdn.microsoft.com/Forums/en-US/231bf072-2c71-4121-8339-ac9d868137b9/faqs-for-cloudyn-cost-management?forum=Cloudyn).

## <a name="how-can-i-resolve-common-indirect-enterprise-setup-problems"></a>Como posso resolver problemas comuns de configuração indireta enterprise?

Ao utilizar o portal do Cloudyn pela primeira vez, poderão ser apresentadas as seguintes mensagens se for utilizador do Contrato Enterprise ou Fornecedor de Soluções Cloud (CSP):

- "A chave de API especificada não é uma chave de inscrição de nível superior" apresentada na **definido no Cloudyn** assistente.
- "Inscrição direta – não" apresentado no portal do contrato Enterprise.
- "Não existem dados de utilização foi encontrados nos últimos 30 dias. Entre em contato com o distribuidor para se certificar de que a marcação foi ativada para a sua conta do Azure"apresentada no portal do Cloudyn.

As mensagens anteriores indicam que adquiriu um Contrato Enterprise do Azure através de um revendedor ou CSP. O seu revendedor ou CSP tem de ativar a _marcação_ para a sua conta do Azure, para que possa ver os dados no Cloudyn.

Eis como resolver os problemas:

1. O seu revendedor tem de ativar a _marcação_ para a sua conta. Para obter instruções, consulte o [Guia de Introdução do Cliente Indireto](https://ea.azure.com/api/v3Help/v2IndirectCustomerOnboardingGuide).

2. A chave do Contrato Enterprise do Azure para utilizar com o Cloudyn é gerida por si. Para obter instruções, consulte [adicionando Your Azure EA](quick-register-ea.md#register-with-cloudyn) ou [como localizar sua ID de inscrição EA e a chave de API](https://youtu.be/u_phLs_udig).

Apenas um administrador de serviços do Azure pode ativar o Cloudyn. As permissões de coadministrador são insuficientes.

Para poder gerar a chave de API do Contrato Enterprise do Azure, para configurar o Cloudyn, tem de ativar a API de Faturação do Azure ao seguir as instruções em:

- [Descrição geral de APIs de Relatórios para clientes Enterprise](../billing/billing-enterprise-api.md)
- [API de Relatórios do portal empresarial do Microsoft Azure](https://ea.azure.com/helpdocs/reportingAPI) em **Ativar o acesso a dados para a API**


Também poderá ter de conceder permissões a administradores de departamento, proprietários de conta e administradores empresariais para _ver custos_ com a API de Faturação.

## <a name="why-dont-i-see-optimizer-recommendations"></a>Por que motivo não vejo as recomendações de otimizador?

Informações de recomendação só estão disponíveis para contas que são ativadas. Não verá quaisquer informações de recomendação no **otimizador** categorias para as contas que são de relatório *não ativados*, incluindo:

- Gestor de otimização
- Otimização de dimensionamento
- Ineficiências

Se não é possível visualizar quaisquer dados de recomendação do otimizador, muito provavelmente, terá as contas que são não ativadas. Para ativar uma conta, terá de registá-lo com as suas credenciais do Azure.

Para ativar uma conta:

1.  No portal do Cloudyn, clique em **Definições** na parte superior direita e selecione **Contas da Cloud**.
2.  No separador de contas do Microsoft Azure, procure as contas que têm uma **não ativados** subscrição.
3.  À direita de uma conta não ativada, clique nas **editar** symbol é semelhante a um lápis.
4.  O inquilino, ID e o ID de taxa é detetado automaticamente. Clique em **Seguinte**.
5.  Está redirecionado para o portal do Azure. Inicie sessão no portal e autorizar o Cloudyn Recoletor para aceder aos dados do Azure.
6.  Em seguida, está redirecionado para a página de gestão de contas do Cloudyn e a sua subscrição está atualizada com **Active Directory** estado da conta. Mostra um símbolo de marca de verificação verde.
7.  Se não vir um símbolo de marca de verificação verde para um ou mais das subscrições, significa que não tem permissões para criar uma aplicação de leitor (o CloudynCollector) para a subscrição. Um utilizador com permissões superiores para a subscrição tem de repetir os passos 3 e 4.  

Depois de concluir os passos anteriores, pode ver recomendações de otimizador dentro de um ou dois dias. No entanto, pode demorar até cinco dias antes dos dados de otimização completo estão disponíveis.


## <a name="how-do-i-enable-suspended-or-locked-out-users"></a>Como posso ativar o suspensos ou Escalamento bloqueado utilizadores?

Em primeiro lugar, vamos dar uma olhada cenário mais comum que faz com que as contas de utilizador obter *initiallySuspended*.

> Admin1 poderá ser utilizador do fornecedor de soluções do Microsoft Cloud ou do Enterprise Agreement. Sua organização está pronta para começar a utilizar o Cloudyn.  Ele registra através do portal do Azure e iniciar sessão no Cloudyn portal. Como a pessoa que regista o serviço de Cloudyn e inicia sessão no portal do Cloudyn, ele se torna a *administrador principal*. Admin1 não cria as contas de utilizador. No entanto, com o portal do Cloudyn, ele cria as contas do Azure e configura uma hierarquia de entidades. Admin1 informa Admin2, um administrador de inquilino, o que ele precisa para se registrar no Cloudyn e iniciar sessão portal do Cloudyn.
> 
> Registra Admin2 através do portal do Azure. No entanto quando ele tenta iniciar sessão portal do Cloudyn, ele recebe um erro dizendo que é a conta dele **suspenso**. O administrador principal, Admin1, é notificado sobre a suspensão de conta. Admin1 tem de ativar a conta do Admin2 e conceder *acesso de entidade de administrador* para as entidades adequadas e permite o acesso de gestão de utilizador e o Active Directory a conta de utilizador.


Se receber um alerta com um pedido para permitir o acesso de um utilizador, terá de ativar a conta de utilizador.

Para ativar a conta de utilizador:

1. Inicie sessão no Cloudyn utilizando a conta de utilizador administrativo do Azure que utilizou para configurar o Cloudyn. Em alternativa, inicie sessão com uma conta de utilizador que foi concedida acesso de administrador.
2. Selecione o símbolo de engrenagem no canto superior direito e selecione **gestão de utilizadores**.
3. Encontre o utilizador, selecione o símbolo de lápis e, em seguida, edite o utilizador.
4. Sob **estado do utilizador**, altere o estado a partir **suspenso** para **Active Directory**.

Contas de utilizador do Cloudyn ligar com início de sessão único do Azure. Se um usuário digitar a palavra-passe, eles poderão fica bloqueados fora do Cloudyn, apesar de que ainda possam aceder ao Azure.

Se alterar o seu endereço de email no Cloudyn a partir do endereço predefinido no Azure, a sua conta pode obter bloqueada. Poderá mostrar "Estado initiallySuspended." Se a sua conta de utilizador está bloqueada, contacte o administrador alternativo para repor a sua conta.

Recomendamos que crie, pelo menos, duas contas de administrador do Cloudyn, no caso de uma das contas é bloqueada.

Se não é possível iniciar sessão no portal do Cloudyn, certifique-se de que está a utilizar o URL correto para iniciar sessão no Cloudyn. Uso [ https://azure.cloudyn.com ](https://ms.portal.azure.com/#blade/Microsoft_Azure_CostManagement/CloudynMainBlade).

Evite utilizar o URL direto do Cloudyn https://app.cloudyn.com.

## <a name="how-do-i-activate-unactivated-accounts-with-azure-credentials"></a>Como posso ativar contas não ativadas com credenciais do Azure?

Assim que suas contas do Azure são detetadas pela Cloudyn, dados de custo imediatamente são fornecidos em relatórios com base no custo. No entanto, para o Cloudyn fornecer dados de utilização e desempenho, terá de registar as suas credenciais do Azure para as contas. Para obter instruções, consulte [adicionar uma conta ou atualizar uma assinatura](activate-subs-accounts.md#add-an-account-or-update-a-subscription).

Para adicionar as credenciais do Azure para uma conta, no portal do Cloudyn, selecione o símbolo de edição à direita do nome da conta, não a subscrição.

Até que as credenciais do Azure são adicionadas ao Cloudyn, a conta é apresentado como _não_.

## <a name="how-do-i-add-multiple-accounts-and-entities-to-an-existing-subscription"></a>Como adicionar várias contas e entidades a uma subscrição existente?

Entidades adicionais são utilizadas para adicionar contratos de empresa adicionais a uma subscrição do Cloudyn. Para obter mais informações, consulte [criar e gerir entidades](tutorial-user-access.md#create-and-manage-entities).

Para os CSPs:

Para adicionar mais contas CSP a uma entidade, selecione **acesso MSP** em vez de **Enterprise** ao criar a nova entidade. Se a sua conta é registada como um contrato Enterprise e que pretende adicionar credenciais CSP, o pessoal de suporte do Cloudyn poderá ter de modificar as definições da conta. Se for assinante do Azure pago, pode criar um novo pedido de suporte no portal do Azure. Selecione **ajuda + suporte**e, em seguida, selecione **novo pedido de suporte**.

## <a name="currency-symbols-in-cloudyn-reports"></a>Símbolos de moeda em relatórios do Cloudyn

Pode ter várias contas do Azure através de moedas diferentes. No entanto, os relatórios de custos no Cloudyn não mostrar mais de um tipo de moeda por cada relatório.

Se tiver várias subscrições com moedas diferentes, uma entidade pai e moedas de entidade seu filho são apresentadas em dólares americanos **$**. Nosso sugerida melhor prática é evitar o uso de moedas diferentes na mesma hierarquia de entidades. Em outras palavras, todas as suas subscrições organizadas numa estrutura de entidade devem utilizar a mesma moeda.

Cloudyn automaticamente detecta a moeda de subscrição do Enterprise Agreement e apresenta corretamente em relatórios.  No entanto, o Cloudyn exibe apenas USD **$** para contas do Azure do web direct e CSP.

## <a name="what-are-cloudyn-data-refresh-timelines"></a>Quais são os dados do Cloudyn atualizar linhas cronológicas?

Cloudyn tem as linhas cronológicas atualização de dados seguintes:

- **Inicial**: Depois de configurar, pode demorar até 24 horas para ver os dados de custo no Cloudyn. Também pode demorar até 10 dias para o Cloudyn a recolher dados suficientes para exibir as recomendações de dimensionamento.
- **Diária**: Ao dia décimo no final de cada mês, o Cloudyn deve mostrar os dados atualizados do dia anterior depois de sobre UTC + 3 no dia seguinte.
- **Mensal**: O primeiro dia para o décimo dia de cada mês, Cloudyn pode mostrar os seus dados apenas até ao fim do mês anterior.

Cloudyn processa os dados para o dia anterior, quando os dados completos do dia anterior estão disponíveis. Dados do dia anterior estão geralmente disponíveis no Cloudyn por sobre UTC + 3 por dia. Alguns dados, como etiquetas, podem demorar um adicional de 24 horas para processar.

Dados para o mês atual não estão disponíveis para a coleção no início de cada mês. Durante o período, os fornecedores de serviços finalizar faturação para o mês anterior. Dados do mês anterior é apresentado no Cloudyn 5 a 10 dias após o início de cada mês. Durante este período, poderá ver apenas os custos amortizados do mês anterior. Não poderá ver dados de faturação ou de utilização diária. Quando os dados se torna disponíveis, a Cloudyn processa-o de forma retroativa. Após o processamento, todos os dados mensais é apresentado entre o quinto dia e o décimo dia de cada mês.

Se existir um atraso de envio de dados do Azure ao Cloudyn, os dados ainda são registados no Azure. Os dados são transferidos ao Cloudyn quando a ligação é restaurada.

## <a name="cost-fluctuations-in-cloudyn-cost-reports"></a>Custos flutuações de relatórios de custos Cloudyn

Relatórios de custos podem mostrar flutuações de custo, sempre que o fornecedor de serviços cloud enviar ficheiros atualizados de faturas. Os custos flutuantes ocorrerem quando os ficheiros novos são recebidos do fornecedor de serviços cloud fora do normal diário ou mensal de agendamento de relatórios. Alterações de custos não resultam de recálculo do Cloudyn.

Ao longo do mês, todos os ficheiros de faturas enviados pelo seu fornecedor de serviços cloud são uma estimativa dos seus custos diários. Por vezes, os dados são atualizados com freqüência — ocasionalmente várias vezes por dia. As atualizações são mais freqüentes, com o AWS, que o Azure. Os custos totais devem permanecer estáveis, quando o cálculo de faturação para o mês anterior é concluído e o arquivo de faturação final é recebido. Normalmente, a 10th do mês.

As alterações ocorrem quando receber ajustes de custo do seu fornecedor de serviços cloud. Receber créditos é um exemplo. As alterações podem ocorrer meses após o mês relevante foi fechado. As alterações são mostradas sempre que um recálculo é feito pelo seu fornecedor de serviços cloud. Cloudyn atualiza os dados históricos para se certificar de que todos os ajustes serão recalculados. Ele também verifica que os custos são apresentados com precisão em comunica.

## <a name="how-can-a-direct-csp-configure-cloudyn-access-for-indirect-csp-customers-or-partners"></a>Como um CSP direto configurar acesso ao Cloudyn para parceiros ou clientes CSP indiretos?

Para obter instruções, consulte [configurar o acesso CSP indireto no Cloudyn](quick-register-csp.md#configure-indirect-csp-access-in-cloudyn).

## <a name="what-causes-the-optimizer-menu-item-to-appear"></a>O que faz com que o item de menu de otimizador apareça?

Depois de adicionar acesso do Azure Resource Manager e os dados são recolhidos, deverá ver o **otimizador** opção. Para ativar o acesso do Azure Resource Manager, consulte [como ativar contas não ativadas com credenciais do Azure?](#how-do-i-activate-unactivated-accounts-with-azure-credentials)

## <a name="is-cloudyn-agent-based"></a>Agente do Cloudyn baseia-se?

Não. Agentes não são utilizados. Recolha de dados de métrica de máquina virtual do Azure para VMs do API de informações da Microsoft. Se deseja coletar dados métricos VMS do Azure, terá de ter as definições de diagnóstico ativadas.

## <a name="do-cloudyn-reports-show-more-than-one-ad-tenant-per-report"></a>Relatórios do Cloudyn mostram mais do que um inquilino do AD por cada relatório?

Sim. Pode [criar uma entidade de conta de cloud correspondente](tutorial-user-access.md#create-and-manage-entities) para cada inquilino do AD que tiver. Em seguida, pode ver todos os dados do seu inquilino do Azure AD e outros fornecedores de plataforma na cloud, incluindo Amazon Web Services e Google Cloud Platform.
