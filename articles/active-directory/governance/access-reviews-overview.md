---
title: O que são as revisões de acesso? - Diretório Ativo Azure / Microsoft Docs
description: Utilizando avaliações de acesso ao Azure Ative Directory, pode controlar a adesão ao grupo e o acesso à aplicação para atender a iniciativas de governação, gestão de riscos e conformidade na sua organização.
services: active-directory
documentationcenter: ''
author: msaburnley
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.subservice: compliance
ms.date: 06/17/2020
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 22a7cafbf8223c3d9e7641851d02f61bc2ad16ae
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85078904"
---
# <a name="what-are-azure-ad-access-reviews"></a>O que são as revisões de acesso do Azure AD?

As análises de acesso do Azure Ative Directory (Azure AD) permitem às organizações gerir eficientemente os membros do grupo, aceder a aplicações empresariais e atribuições de funções. O acesso do utilizador pode ser revisto regularmente para garantir que apenas as pessoas certas tenham acesso continuado.

Aqui está um vídeo que fornece uma visão geral rápida das avaliações de acesso:

>[!VIDEO https://www.youtube.com/embed/kDRjQQ22Wkk]

## <a name="why-are-access-reviews-important"></a>Porque é que as avaliações de acesso são importantes?

O Azure AD permite-lhe colaborar internamente dentro da sua organização e com utilizadores de organizações externas, como parceiros. Os utilizadores podem juntar-se a grupos, convidar os hóspedes, conectar-se a aplicações na nuvem e trabalhar remotamente a partir do seu trabalho ou dispositivos pessoais. A conveniência de alavancar o poder do self-service levou à necessidade de melhores capacidades de gestão de acesso.

- À medida que os novos colaboradores se juntam, como garante que têm o acesso certo para serem produtivos?
- À medida que as pessoas movem equipas ou saem da empresa, como garante que o seu antigo acesso é removido, especialmente quando envolve hóspedes?
- Os direitos de acesso excessivos podem conduzir a conclusões e compromissos de auditoria, uma vez que indicam uma falta de controlo sobre o acesso.
- É preciso envolver-se proativamente com os proprietários de recursos para garantir que revejam regularmente quem tem acesso aos seus recursos.

## <a name="when-to-use-access-reviews"></a>Quando utilizar comentários de acesso?

- **Demasiados utilizadores em funções privilegiadas:** É uma boa ideia verificar quantos utilizadores têm acesso administrativo, quantos deles são Administradores Globais, e se há algum convidado ou parceiro que não tenha sido removido após ter sido designado para fazer uma tarefa administrativa. Pode retificar os utilizadores de atribuição de funções em [funções de Azure AD,](../privileged-identity-management/pim-how-to-perform-security-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json) tais como Administradores Globais, ou [funções de recursos Azure,](../privileged-identity-management/pim-resource-roles-perform-access-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json) como o Administrador de Acesso ao Utilizador na experiência [Azure AD Privileged Identity Management (PIM).](../privileged-identity-management/pim-configure.md)
- **Quando a automatização é inviável:** Pode criar regras para a adesão dinâmica a grupos de segurança ou grupos do Office 365, mas e se os dados de RH não estiverem no AZure AD ou se os utilizadores ainda precisarem de acesso após deixarem o grupo para treinar a sua substituição? Em seguida, pode criar uma revisão sobre esse grupo para garantir que aqueles que ainda precisam de acesso devem ter acesso continuado.
- **Quando um grupo é usado para um novo propósito:** Se tiver um grupo que será sincronizado com o AZure AD, ou se planeia ativar a aplicação Salesforce para todos no grupo de equipas de Vendas, seria útil pedir ao dono do grupo que revisse a filiação do grupo antes de o grupo ser utilizado num conteúdo de risco diferente.
- **Acesso crítico de dados empresariais:** para determinados recursos, pode ser necessário pedir às pessoas fora das TI que assinem regularmente e dêem uma justificação sobre o porquê de precisarem de acesso para fins de auditoria.
- **Para manter a lista de exceções de uma apólice:** Num mundo ideal, todos os utilizadores seguiriam as políticas de acesso para garantir o acesso aos recursos da sua organização. No entanto, às vezes há casos de negócios que exigem que asse exceções. Como administrador de TI, pode gerir esta tarefa, evitar a supervisão de exceções políticas e fornecer aos auditores a prova de que estas exceções são revistas regularmente.
- **Peça aos proprietários do grupo que confirmem que ainda precisam de hóspedes nos seus grupos:** O acesso dos colaboradores pode ser automatizado com alguns no IAM, mas não convidados. Se um grupo dá aos hóspedes acesso a conteúdos sensíveis ao negócio, então é da responsabilidade do proprietário do grupo confirmar que os hóspedes ainda têm uma necessidade legítima de acesso ao negócio.
- **Ter revisões repetem-se periodicamente:** Pode configurar avaliações de acesso recorrentes dos utilizadores em frequências definidas, como semanal, mensal, trimestral ou anualmente, e os revisores serão notificados no início de cada revisão. Os revisores podem aprovar ou negar o acesso com uma interface amigável e com a ajuda de recomendações inteligentes.

## <a name="where-do-you-create-reviews"></a>Onde cria críticas?

Dependendo do que pretende rever, criará a sua análise de acesso em avaliações de acesso AZure AD, aplicações empresariais AZure AD (em pré-visualização) ou Azure AD PIM.

| Direitos de acesso dos utilizadores | Os revisores podem ser | Revisão criada em | Experiência de revisor |
| --- | --- | --- | --- |
| Membros do grupo de segurança</br>Membros do grupo de escritório | Revisores especificados</br>Proprietários do grupo</br>Auto-revisão | Revisões de acesso do Azure AD</br>Grupos do Azure AD | Painel de acesso |
| Atribuído a uma aplicação conectada | Revisores especificados</br>Auto-revisão | Revisões de acesso do Azure AD</br>Aplicativos empresariais AZURE AD (em pré-visualização) | Painel de acesso |
| Papel de AD AZure | Revisores especificados</br>Auto-revisão | [Azure AD PIM](../privileged-identity-management/pim-how-to-start-security-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json) | Portal do Azure |
| Papel de recurso azul | Revisores especificados</br>Auto-revisão | [Azure AD PIM](../privileged-identity-management/pim-resource-roles-start-access-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json) | Portal do Azure |


## <a name="create-access-reviews"></a>Criar comentários de acesso

Para criar avaliações de acesso, siga estes passos:

1. Vá ao [portal Azure](https://portal.azure.com) para gerir comentários de acesso e inscreva-se como administrador global ou administrador do Utilizador.

1. Procure e selecione **Azure Active Directory**.

      ![Pesquisa no portal Azure para O Diretório Ativo Azure](media/access-reviews-overview/search-azure-active-directory.png)

1. Selecione **a governação de identidades**.

1. Na página 'Iniciar' clique no **botão Criar um botão de revisão de acesso.**

   ![Aceder à página inicial das avaliações](./media/access-reviews-overview/access-reviews-overview-create-access-reviews.png) 

### <a name="creating-access-review-on-a-group-that-can-be-assigned-to-azure-ad-role"></a>Criando revisão de acesso em um grupo que pode ser atribuído ao papel de AD AZure
Se estiver na versão mais recente de Access Reviews (os seus revisores são direcionados para **o My Access** por padrão) , então apenas o Administrador Global pode criar uma revisão de acesso em grupos atribuíveis por funções. No entanto, se estiver na versão mais antiga dos Comentários de Acesso (os seus revisores são direcionados para o **Painel de Acesso** por padrão), então tanto o Administrador Global como o Administrador do Utilizador podem criar uma revisão de acesso em grupos atribuíveis por funções.  

A nova experiência será lançada para todos os clientes no dia 1 de agosto de 2020, mas se quiser atualizar mais cedo, por favor faça um pedido aqui - [Azure AD Access Reviews - Experiência de revisor atualizado no My Access Signup.](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR5dv-S62099HtxdeKIcgO-NUOFJaRDFDWUpHRk8zQ1BWVU1MMTcyQ1FFUi4u)

[Saiba mais sobre a atribuição de grupos a funções AZure AD](https://go.microsoft.com/fwlink/?linkid=2103037).

## <a name="learn-about-access-reviews"></a>Saiba mais sobre comentários de acesso

Para saber mais sobre a criação e realização de avaliações de acesso, assista a esta pequena demonstração:

>[!VIDEO https://www.youtube.com/embed/6KB3TZ8Wi40]

Se estiver pronto para implementar comentários de acesso na sua organização, siga estes passos no vídeo para bordo, treine os seus administradores e crie a sua primeira revisão de acesso!

>[!VIDEO https://www.youtube.com/embed/X1SL2uubx9M]

## <a name="license-requirements"></a>Requisitos de licença

[!INCLUDE [Azure AD Premium P2 license](../../../includes/active-directory-p2-license.md)]

### <a name="how-many-licenses-must-you-have"></a>Quantas licenças tem?

Certifique-se de que o seu diretório tem pelo menos tantas licenças Azure AD Premium P2 como tem funcionários que irão executar as seguintes tarefas:

- Utilizadores membros e convidados que são designados como revisores
- Utilizadores de membros e convidados que realizam uma auto-revisão
- Proprietários do grupo que realizam uma revisão de acesso
- Proprietários de aplicativos que realizam uma revisão de acesso

As licenças Azure AD Premium P2 **não** são necessárias para as seguintes tarefas:

- Não são necessárias licenças para os utilizadores com as funções de Administrador Global ou Administrador de Utilizador que criem revisões de acesso, configurar definições ou aplicar as decisões das avaliações.

Por cada licença Azure AD Premium P2 paga que atribua a um dos utilizadores da sua própria organização, pode utilizar o Azure AD business-to-business (B2B) para convidar até cinco utilizadores convidados ao abrigo do Subsídio de Utilizador Externo. Estes utilizadores convidados também podem utilizar funcionalidades Azure AD Premium P2. Para obter mais informações, consulte [a orientação de licenciamento de colaboração Azure AD B2B](../b2b/licensing-guidance.md).

Para obter mais informações sobre licenças, consulte [atribuir ou remover licenças utilizando o portal Azure Ative Directory](../fundamentals/license-users-groups.md).

### <a name="example-license-scenarios"></a>Exemplo de cenários de licença

Aqui estão alguns cenários de licença de exemplo para ajudá-lo a determinar o número de licenças que deve ter.

| Cenário | Cálculo | Número de licenças |
| --- | --- | --- |
| Um administrador cria uma revisão de acesso do Grupo A com 75 utilizadores e 1 dono do grupo, e atribui o proprietário do grupo como revisor. | 1 licença para o proprietário do grupo como revisor | 1 |
| Um administrador cria uma revisão de acesso do Grupo B com 500 utilizadores e 3 proprietários do grupo, e atribui os 3 proprietários do grupo como revisores. | 3 licenças para cada dono do grupo como revisores | 3 |
| Um administrador cria uma revisão de acesso do Grupo B com 500 utilizadores. Torna-se uma auto-revisão. | 500 licenças para cada utilizador como auto-revisores | 500 |
| Um administrador cria uma revisão de acesso do Grupo C com 50 utilizadores membros e 25 utilizadores convidados. Torna-se uma auto-revisão. | 50 licenças para cada utilizador como auto-revisores.<br/>(os utilizadores convidados estão abrangidos pela relação 1:5 exigida) | 50 |
| Um administrador cria uma revisão de acesso do Grupo D com 6 utilizadores membros e 108 utilizadores convidados. Torna-se uma auto-revisão. | 6 licenças para cada utilizador como auto-revisores + 16 licenças adicionais para cobrir todos os 108 utilizadores convidados na relação 1:5 exigida. 6 licenças, que cobrem 6 \* 5=30 utilizadores convidados. Para os restantes utilizadores (108-6 \* 5)=78, são necessárias 78/5=16 licenças adicionais. Assim, no total, são necessárias 6+16=22 licenças. | 22 |

## <a name="next-steps"></a>Passos seguintes

- [Criar uma revisão de acesso de grupos ou aplicações](create-access-review.md)
- [Criar uma revisão de acesso de utilizadores numa função administrativa do Azure AD](../privileged-identity-management/pim-how-to-start-security-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json)
- [Rever o acesso a grupos ou aplicações](perform-access-review.md)
- [Complete uma revisão de acesso de grupos ou aplicações](complete-access-review.md)
