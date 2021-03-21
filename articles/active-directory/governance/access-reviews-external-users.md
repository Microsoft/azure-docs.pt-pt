---
title: Use a Azure AD Identity Governance para rever e remover utilizadores externos que já não têm acesso a recursos
description: Use comentários de acesso para estender o acesso de membros de organizações parceiras
services: active-directory
documentationcenter: ''
author: ajburnle
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 09/06/2020
ms.author: ajburnle
ms.openlocfilehash: fe68ec498d17ec20778c8f34fc6ffa1f0964c44e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102176965"
---
# <a name="use-azure-active-directory-azure-ad-identity-governance-to-review-and-remove-external-users-who-no-longer-have-resource-access"></a>Utilize o Azure Ative Directory (Azure AD) Identity Governance para rever e remover utilizadores externos que já não têm acesso a recursos

Este artigo descreve funcionalidades e métodos que lhe permitem identificar e selecionar identidades externas para que possa revê-las e removê-las do Azure AD se já não forem necessárias. A nuvem torna mais fácil do que nunca colaborar com utilizadores internos ou externos. Abraçando o Office 365, as organizações começam a ver a proliferação de identidades externas (incluindo convidados), à medida que os utilizadores trabalham em conjunto em dados, documentos ou espaços de trabalho digitais, como equipas. As organizações precisam de equilibrar, possibilitando a colaboração e cumprindo os requisitos de segurança e governação. Parte destes esforços deve incluir a avaliação e limpeza de utilizadores externos, que foram convidados para a colaboração no seu inquilino, que são originários de organizações parceiras, e removê-los do seu AD Azure quando já não são necessários.

>[!NOTE]
>Um Azure AD Premium P2 válido, Mobilidade Empresarial + Segurança E5 paga, ou licença de julgamento é necessária para usar avaliações de acesso Azure AD. Para mais informações, consulte [as edições do Azure Ative Directory.](../fundamentals/active-directory-whatis.md)

## <a name="why-review-users-from-external-organizations-in-your-tenant"></a>Porquê rever utilizadores de organizações externas do seu inquilino?

Na maioria das organizações, os utilizadores finais iniciam o processo de convidar parceiros de negócios e fornecedores para colaboração. A necessidade de colaborar impulsiona as organizações para fornecer aos proprietários de recursos e utilizadores finais uma forma de avaliar e atestar regularmente os utilizadores externos. Muitas vezes, o processo de embarque de novos parceiros de colaboração é planeado e contabilizado, mas com muitas colaborações que não têm uma data de fim claro, nem sempre é óbvio quando um utilizador já não precisa de acesso. Além disso, a gestão do ciclo de vida identitária leva as empresas a manter a Azure AD limpa e a remover os utilizadores que já não precisam de acesso aos recursos da organização. Manter apenas as referências de identidade relevantes para parceiros e fornecedores no diretório ajuda a reduzir o risco dos seus colaboradores, selecionando e concedendo inadvertidamente acesso a utilizadores externos que deveriam ter sido removidos. Este documento acompanha várias opções que vão desde sugestões proativas recomendadas a atividades reativas e de limpeza para governar identidades externas.

## <a name="use-entitlement-management-to-grant-and-revoke-access"></a>Utilizar a Gestão de Direitos para conceder e revogar o acesso

As funcionalidades de gestão de direitos permitem o [ciclo de vida automatizado de identidades externas](entitlement-management-external-users.md#manage-the-lifecycle-of-external-users) com acesso a recursos. Ao estabelecer processos e procedimentos para gerir o acesso através da Gestão de Direitos e publicando recursos através de Pacotes de Acesso, manter o acesso externo dos utilizadores aos recursos torna-se um problema muito menos complicado de resolver. Ao gerir o acesso através de Pacotes de [Acesso à Gestão de Direitos](entitlement-management-overview.md) em Azure AD, a sua organização pode definir e gerir centralmente o acesso aos seus utilizadores, bem como utilizadores de organizações parceiras. A Gestão de Direitos utiliza aprovações e atribuições de Pacotes de Acesso para rastrear onde os utilizadores externos solicitaram e foram atribuídos acesso. Se um utilizador externo perder todas as suas atribuições, a Gestão de Direitos pode remover estes utilizadores externos automaticamente do arrendatário. 

## <a name="find-guests-not-invited-through-entitlement-management"></a>Encontre hóspedes não convidados através da Gestão de Direitos

Quando os colaboradores estão autorizados a colaborar com utilizadores externos, podem convidar qualquer número de utilizadores de fora da sua organização. Procurar e agrupar parceiros externos em grupos dinâmicos alinhados com a empresa e revê-los pode não ser viável, pois pode haver muitas empresas individuais diferentes para rever, ou não há proprietário ou patrocinador para a organização. A Microsoft fornece uma amostra do script PowerShell que pode ajudá-lo a analisar o uso de identidades externas num inquilino. O guião enumera identidades externas e categoriza-as. O script pode ajudá-lo a identificar e limpar identidades externas que podem já não ser necessárias. Como parte da saída do script, a amostra de script suporta a criação automatizada de grupos de segurança que contêm os parceiros externos identificados sem grupo – para posterior análise e utilização com Azure AD Access Reviews.
O script está disponível no [GitHub.](https://github.com/microsoft/access-reviews-samples/tree/master/ExternalIdentityUse) Após o script terminar de funcionar, gera um ficheiro de saída HTML que descreve identidades externas que:

- Não tem mais nenhuma filiação de grupo no inquilino
- Tenha uma missão para um papel privilegiado no inquilino
- Tenha uma atribuição a um requerimento no inquilino

A saída também inclui os domínios individuais para cada uma destas identidades externas. 

>[!NOTE]
>O script acima referido é um script de amostra que verifica a adesão ao grupo, atribuições de funções e atribuições de aplicações em Azure AD. Pode haver outras atribuições em aplicações que os utilizadores externos receberam fora do Azure AD, tais como SharePoint (atribuição de membros diretos) ou Azure RBAC ou Azure DevOps.

## <a name="review-resources-used-by-external-identities"></a>Rever os recursos utilizados por identidades externas

Se tiver identidades externas utilizando recursos como Equipas ou outras aplicações ainda não regidas pela Gestão de Direitos, poderá também querer rever o acesso a estes recursos regularmente. O Azure AD [Access Reviews](create-access-review.md) dá-lhe a possibilidade de rever o acesso de identidades externas, permitindo que o proprietário do recurso, identidades externas, ou outra pessoa delegada em quem confia atestem se o acesso continuado é necessário. Os Comentários de Acesso direcionam um recurso e criam uma atividade de revisão a todos os que tenham acesso apenas ao recurso ou aos utilizadores do Guest. O revisor irá então ver a lista resultante de utilizadores que precisam de rever – seja todos os utilizadores, incluindo funcionários da sua organização ou apenas identidades externas.

![usando um grupo para rever o acesso](media/access-reviews-external-users/group-members.png)

O estabelecimento de uma cultura de revisão orientada para o proprietário de recursos ajuda a governar o acesso a identidades externas. Os proprietários de recursos, responsáveis pelo acesso, disponibilidade e segurança da informação que possuem, são, na maioria dos casos, o seu melhor público para impulsionar decisões em torno do acesso aos seus recursos e estão mais próximos dos utilizadores que os acedem do que o central de TI ou de um patrocinador que gere muitos externos.

## <a name="create-access-reviews-for-external-identities"></a>Criar comentários de acesso para identidades externas

Os utilizadores que já não tenham acesso a quaisquer recursos no seu inquilino podem ser removidos se deixarem de trabalhar com a sua organização. Antes de bloquear e eliminar estas identidades externas, pode querer contactar estes utilizadores externos e certificar-se de que não ignorou um projeto ou acesso permanente que ainda precisam. Quando cria um grupo que contenha todas as identidades externas como membros que encontrou não têm acesso a quaisquer recursos no seu inquilino, pode utilizar o Access Reviews para ter todos os externos auto-atestam se ainda precisam ou têm acesso – ou ainda precisarão de acesso no futuro. Como parte da revisão, o criador de revisão em Access Reviews pode usar a **razão requerindo a função de aprovação** para exigir que os utilizadores externos forneçam uma justificação para o acesso continuado, através do qual você pode saber onde e como eles ainda precisam de acesso no seu inquilino. Além disso, pode ativar a definição De conteúdo adicional para funcionalidade **de e-mail do revisor,** para que os utilizadores saibam que perderão acesso se não responderem e, caso ainda precisem de acesso, é necessária uma justificação. Se pretender ir em frente e deixar os Comentários de Acesso **desativar e eliminar** identidades externas, caso não respondam ou forneçam uma razão válida para o acesso continuado, pode utilizar a opção Desativar e eliminar, conforme descrito na secção seguinte.

![limitando o âmbito da revisão apenas aos utilizadores convidados](media/access-reviews-external-users/guest-users-only.png)

Quando a revisão termina, a página **Resultados** mostra uma visão geral da resposta dada por cada identidade externa. Pode optar por aplicar os resultados automaticamente e deixar os Comentários de Acesso desativar e eliminá-los. Em alternativa, pode consultar as respostas dadas e decidir se pretende remover o acesso ou acompanhamento de um utilizador com eles e obter informações adicionais antes de tomar uma decisão. Se alguns utilizadores ainda tiverem acesso a recursos que ainda não reviu, pode utilizar a revisão como parte da sua descoberta e enriquecer o seu próximo ciclo de revisão e atestação.

## <a name="disable-and-delete-external-identities-with-azure-ad-access-reviews"></a>Desativar e eliminar identidades externas com avaliações de acesso AD Azure

Além da opção de remover identidades externas indesejadas de recursos como grupos ou aplicações, as Análises de Acesso AD da Azure podem bloquear as identidades externas da inscrição no seu inquilino e eliminar as identidades externas do seu inquilino após 30 dias. Uma vez que selecione **o utilizador do Bloco para iniciar sessão durante 30 dias e, em seguida, remova o utilizador do arrendatário,** a revisão permanecerá no estado de "aplicação" durante 30 dias. Durante este período, as definições, resultados, revisores ou registos de auditoria ao abrigo da revisão atual não serão visualizados ou configuráveis. 

![após definições de conclusão](media/access-reviews-external-users/upon-completion-settings.png)

Ao criar uma nova Revisão de Acesso, na secção "Após as definições de conclusão", para **que a ação se aplique aos utilizadores negados,** pode definir os **utilizadores do Bloco a partir da entrada de entrada durante 30 dias e, em seguida, remover o utilizador do arrendatário**.

Esta definição permite identificar, bloquear e eliminar identidades externas do seu inquilino AZure AD. As identidades externas que forem revistas e negadas ao acesso continuado pelo revisor serão bloqueadas e eliminadas, independentemente do acesso ao recurso ou da adesão ao grupo que possuam. Esta definição é melhor utilizada como último passo depois de ter validado que os utilizadores externos em revisão já não têm acesso a recursos e podem ser removidos com segurança do seu inquilino ou se quiser certificar-se de que são removidos, independentemente do seu acesso permanente. A função "Desativar e excluir" bloqueia primeiro o utilizador externo, tirando-lhe a capacidade de se inscrever no seu inquilino e aceder aos recursos. O acesso a recursos não é revogado nesta fase, e no caso de querer ressarciar o utilizador externo, a sua capacidade de iniciar sessão pode ser reconfigurada. Sem mais medidas, uma identidade externa bloqueada será eliminada do diretório após 30 dias, removendo a conta e o seu acesso.

## <a name="next-steps"></a>Passos seguintes

- [Revisões de acesso - Graph API](/graph/api/resources/accessreviews-root?view=graph-rest-beta)
- [Gestão de direitos - Graph API](/graph/api/resources/entitlementmanagement-root?view=graph-rest-beta)
