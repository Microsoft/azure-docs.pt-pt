---
title: Problema que configura o fornecimento do utilizador a uma aplicação da Galeria AD Azure
description: Como resolver problemas comuns ao configurar o fornecimento de um utilizador a uma aplicação já listada na Galeria de Aplicações AD Azure
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: troubleshooting
ms.date: 09/03/2019
ms.author: kenwith
ms.reviewer: asteen
ms.openlocfilehash: 651bc199a43881add7faa00a9d1cf78000de6944
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "99256277"
---
# <a name="problem-configuring-user-provisioning-to-an-azure-ad-gallery-application"></a>Problema de configuração do fornecimento de um utilizador a uma aplicação da Galeria AD Azure

Configurar o [fornecimento automático do utilizador](user-provisioning.md) para uma aplicação (quando suportado), requer que sejam seguidas instruções específicas para preparar o pedido de provisionamento automático. Em seguida, pode utilizar o portal Azure para configurar o serviço de fornecimento para sincronizar as contas dos utilizadores à aplicação.

Deve sempre começar por encontrar o tutorial de configuração específico para configurar o provisionamento para a sua aplicação. Em seguida, siga esses passos para configurar tanto a app como a AD Azure para criar a ligação de provisionamento. Uma lista de tutoriais de aplicativos pode ser encontrada na [Lista de Tutoriais sobre Como Integrar aplicações saas com diretório ativo Azure.](../saas-apps/tutorial-list.md)

## <a name="how-to-see-if-provisioning-is-working"></a>Como ver se o provisionamento está funcionando 

Uma vez configurado o serviço, a maioria dos insights sobre o funcionamento do serviço podem ser extraídas de dois locais:

-   **Provisionamento de registos (pré-visualização)** – Os [registos de provisionamento registam](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context) todas as operações realizadas pelo serviço de fornecimento, incluindo consulta da Azure AD para os utilizadores designados que estão em âmbito de provisionamento. Consultar a aplicação-alvo para a existência desses utilizadores, comparando os objetos do utilizador entre o sistema. Em seguida, adicione, atualize ou desative a conta de utilizador no sistema-alvo com base na comparação. Pode aceder aos registos de provisionamento no portal Azure selecionando registos de provisionamento de aplicações empresariais do **Azure Ative** &gt;  &gt; **Directory (pré-visualização)** na secção **Atividade.**

-   **Estado atual -** Um resumo da última execução de provisionamento para uma determinada aplicação pode ser visto na secção **de &gt; &gt; \[ \] &gt; Provisionamento de Nome de Aplicação de Aplicações de Aplicação de Aplicações do Azure Ative Directory Apps,** na parte inferior do ecrã sob as definições de serviço. A secção Estado Atual mostra se um ciclo de provisionamento iniciou o provisionamento das contas dos utilizadores. Pode observar o progresso do ciclo, ver quantos utilizadores e grupos foram a provisionados e ver quantas funções são criadas. Se houver algum erro, podem ser encontrados detalhes nos registos [Provisioning' (.. /reports-monitoring/concept-provisioning-logs.md?context=azure/active-directy/manage-apps/context/manage-apps-context).

## <a name="general-problem-areas-with-provisioning-to-consider"></a>Áreas problemáticas gerais com provisionamento a considerar

Abaixo está uma lista das áreas de problema geral que você pode perfurar se você tem uma ideia de por onde começar.

* [Serviço de fornecimento não parece começar](#provisioning-service-does-not-appear-to-start)
* Não é possível salvar a configuração devido às credenciais de aplicação que não funcionam
* [Os registos de provisionamento dizem que os utilizadores são "ignorados" e não provisionados, mesmo que sejam atribuídos](#provisioning-logs-say-users-are-skipped-and-not-provisioned-even-though-they-are-assigned)

## <a name="provisioning-service-does-not-appear-to-start"></a>Serviço de fornecimento não parece começar

Se definir o **Estado de Provisionamento** para estar **ligado na** secção de Provisionamento de Nome de Aplicação de Aplicações de Aplicação de Aplicações do **Diretório Ativo Azure &gt; &gt; \[ \] &gt;** do portal Azure. No entanto, não são apresentados outros detalhes de estado nessa página após recargas subsequentes. É provável que o serviço esteja em execução, mas ainda não completou um ciclo inicial. Verifique os **registos de Provisionamento** acima descritos para determinar que operações o serviço está a realizar e se existem erros.

>[!NOTE]
>Um ciclo inicial pode demorar entre 20 minutos e várias horas, dependendo do tamanho do diretório AZure AD e do número de utilizadores em possibilidade de provisionamento. As sincronizações subsequentes após o ciclo inicial são mais rápidas, uma vez que o serviço de fornecimento armazena marcas de água que representam o estado de ambos os sistemas após o ciclo inicial, melhorando o desempenho das sincronizações subsequentes.
>
>

## <a name="cant-save-configuration-due-to-app-credentials-not-working"></a>Não é possível salvar a configuração devido às credenciais de aplicação que não funcionam

Para que o provisionamento funcione, o Azure AD requer credenciais válidas que lhe permitam ligar-se a uma API de gestão de utilizadores fornecida por essa app. Se estas credenciais não funcionarem, ou se não souberem quais são, reveja o tutorial para a criação desta aplicação, descrito anteriormente.

## <a name="provisioning-logs-say-users-are-skipped-and-not-provisioned-even-though-they-are-assigned"></a>Os registos de provisionamento dizem que os utilizadores são ignorados e não a provisionados, mesmo que sejam atribuídos

Quando um utilizador aparece como "ignorado" nos registos de provisionamento, é muito importante ler os detalhes alargados na mensagem de registo para determinar o motivo. Seguem-se razões e resoluções comuns:

- **Foi configurado um filtro de deteção** **que está a filtrar o utilizador com base num valor de atributo**. Para obter mais informações, consulte [o provisionamento de aplicações baseados em Atributos com filtros de escoamento](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

- **O utilizador "não tem efetivamente direito".** Se vir esta mensagem de erro específica, é porque existe um problema com o registo de atribuição do utilizador armazenado no Azure AD. Para corrigir este problema, desatribua o utilizador (ou grupo) da aplicação e reatribua-o novamente. Para obter mais informações, consulte [Atribuir um utilizador ou grupo a uma aplicação da empresa.](../manage-apps/assign-user-or-group-access-portal.md)

- **Falta um atributo necessário ou não é preenchido para um utilizador.** Uma coisa importante a considerar ao configurar o provisionamento é rever e configurar os mapeamentos de atributos e fluxos de trabalho que definem quais as propriedades do utilizador (ou grupo) que fluem do Azure AD para a aplicação. Isto inclui a definição da "propriedade correspondente" que será usada para identificar e combinar exclusivamente utilizadores/grupos entre os dois sistemas. Para obter mais informações sobre este importante processo, consulte [personalizar o fornecimento de produtos de fornecimento de mapeamentos de atributos](../app-provisioning/customize-application-attributes.md).

  * **Atribuir mapeamentos para grupos:** Provisionamento do nome do grupo e detalhes do grupo, além dos membros, se apoiados em algumas candidaturas. Pode ativar ou desativar esta funcionalidade ativando ou desativando o **Mapeamento** de objetos de grupo indicados no **separador Provisioning.** Se os grupos de provisionamento estiverem habilitados, certifique-se de rever os mapeamentos de atributos para garantir que um campo adequado está a ser utilizado para o "ID correspondente". Este pode ser o nome de exibição ou pseudónimo de e-mail), uma vez que o grupo e os seus membros não são a provisionados se a propriedade correspondente estiver vazia ou não povoada para um grupo em Azure AD.

## <a name="next-steps"></a>Passos seguintes
[Automatizar o Provisionamento de Utilizadores e Deprovisionamento para aplicações saas com diretório ativo Azure](user-provisioning.md)
