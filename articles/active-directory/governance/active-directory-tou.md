---
title: Termos de utilização do Azure Active Directory| Microsoft Docs
description: Descreve como começar a utilizar o Azure AD termos de utilização para apresentar informações aos funcionários ou convidados antes de poder aceder.
services: active-directory
author: rolyon
manager: mtillman
editor: ''
ms.assetid: d55872ef-7e45-4de5-a9a0-3298e3de3565
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 02/09/2019
ms.author: rolyon
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8f2de9274e1b6c996e75148d1a1f018d73a721bb
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57443458"
---
# <a name="azure-active-directory-terms-of-use-feature"></a>Funcionalidade Termos de utilização do Azure Active Directory
Os Termos de utilização do Azure AD fornecem um método simples que as organizações podem utilizar para apresentar informações aos utilizadores finais. Tal disponibilização garante a visualização das exclusões de responsabilidade relevantes no que se refere a requisitos legais ou de conformidade por parte dos utilizadores. Este artigo descreve como começar com os termos de utilização.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="overview-videos"></a>Vídeos de descrição geral

O vídeo seguinte apresenta uma rápida visão geral dos termos de utilização.

>[!VIDEO https://www.youtube.com/embed/tj-LK0abNao]

Para ver vídeos adicionais, consulte:
- [Como implementar os termos de utilização no Azure Active Directory](https://www.youtube.com/embed/N4vgqHO2tgY)
- [Como implementar termos de utilização no Azure Active Directory](https://www.youtube.com/embed/t_hA4y9luCY)

## <a name="what-can-i-do-with-terms-of-use"></a>O que posso fazer com os Termos de utilização?
O Azure AD termos de utilização tem as seguintes capacidades:
- Necessitam de funcionários ou convidados aceitar os termos de utilização antes de poder aceder.
- Necessitam de funcionários ou convidados aceitar os termos de utilização em todos os dispositivos antes de poder aceder.
- Necessitam de funcionários ou convidados aceitar os termos de utilização numa agenda periódica.
- Apresentar Termos de utilização gerais para todos os utilizadores da sua organização.
- Apresentar Termos de utilização específicos baseados nos atributos de um utilizador (por exemplo, médicos por oposição a enfermeiros ou colaboradores nacionais por oposição a colaboradores internacionais, uma distinção feita através da utilização de [grupos dinâmicos](../users-groups-roles/groups-dynamic-membership.md)).
- Apresentar Termos de utilização específicos ao aceder a aplicações de elevado impacto empresarial, como o Salesforce.
- Apresentar Termos de utilização em idiomas diferentes.
- Lista que tem ou não aceitou os termos de utilização.
- Ajudar a cumprir os regulamentos de privacidade.
- Exiba um log de termos de utilização atividade de conformidade e auditoria.
- Criar e gerir os termos de utilização com [Microsoft Graph APIs](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/agreement) (atualmente em pré-visualização).

## <a name="prerequisites"></a>Pré-requisitos
Para utilizar e configurar os Termos de utilização Azure AD, tem de ter:

- Uma subscrição Premium P1, Premium P2, EMS E3 ou EMS E5 do Azure AD.
    - Se não tiver uma destas subscrições, pode [obter o Azure AD Premium](../fundamentals/active-directory-get-started-premium.md) ou [ativar a versão de avaliação do Azure AD Premium](https://azure.microsoft.com/trial/get-started-active-directory/).
- Uma das seguintes contas de administrador para o diretório que pretende configurar:
    - Administrador Global
    - Administrador de Segurança
    - Administrador de Acesso Condicional

## <a name="terms-of-use-document"></a>Documento Termos de utilização

Os Termos de utilização do Azure AD utilizam o formato PDF para apresentar conteúdo. O ficheiro PDF pode ter qualquer tipo de conteúdo, como documentos contratuais existentes, permitindo-lhe recolher contratos de utilizador final durante o início de sessão do utilizador. Para suportar os utilizadores em dispositivos móveis, o tamanho de tipo de letra recomendado no PDF é o ponto de 24.

## <a name="add-terms-of-use"></a>Adicionar os Termos de utilização
Depois de finalizar o documento Termos de utilização, utilize o procedimento que se segue para o adicionar.

1. Inicie sessão no Azure Global como administrador, administrador de segurança ou administrador de acesso condicional.

1. Navegue para **Termos de utilização** em [https://aka.ms/catou](https://aka.ms/catou).

    ![Painel Termos de utilização](./media/active-directory-tou/tou-blade.png)

1. Clique em **Novos termos**.

    ![Adicionar Termos de Utilização](./media/active-directory-tou/new-tou.png)

1. Na **nome** , introduza um nome para os termos de utilização que será utilizado no portal do Azure.

1. Na **nome a apresentar** , introduza um título que os utilizadores veem quando iniciam sessão.

1. Para **termos de utilização documento**, navegue até sua finalizado dos termos de utilização PDF e selecioná-lo.

1. Selecione o idioma para os seus termos de utilização de documento. A opção de idioma permite-lhe carregar vários Termos de utilização, cada um com um idioma diferente. A versão dos Termos de utilização que um utilizador final verá será baseada nas respetivas preferências do browser.

1. Para exigir que os utilizadores finais que vejam os termos de utilização antes de os aceitarem, defina **exigir que os utilizadores expandam os termos de utilização** ao **no**.

1. Para exigir que os utilizadores finais a aceitar os termos de utilização em todos os dispositivos estão a aceder a partir de, defina **exigir que os utilizadores dar consentimento em todos os dispositivos** ao **no**. Para obter mais informações, consulte [por dispositivo termos de utilização](#per-device-terms-of-use).

1. Se quiser expirar termos de utilização autorizar com base numa agenda, defina **expirar consentimentos** ao **no**. Quando definido como no, são apresentadas duas definições de agendamento adicionais.

    ![Consentimentos expirados](./media/active-directory-tou/expire-consents.png)

1. Utilizar o **Expire a partir de** e **frequência** definições para especificar a agenda para condições de utilizam as expirações. A tabela seguinte mostra o resultado para algumas definições de exemplo:

    | Expiração inicia em | Frequência | Resultado |
    | --- | --- | --- |
    | Data de hoje  | Custo | A partir de hoje, os utilizadores tem de aceitar os termos de utilização e, em seguida, voltem todos os meses. |
    | Data no futuro  | Custo | A partir de hoje, os utilizadores têm de aceitar os termos de utilização. Quando ocorre a data no futuro, consentimentos irão expirar e, em seguida, os utilizadores têm voltem a todos os meses.  |

    Por exemplo, se definir expire a iniciar na data para **1 de Janeiro** e a frequência para **mensal**, eis como expirações poderão ocorrer para que dois usuários:

    | Utilizador | Em primeiro lugar aceitar data | Em primeiro lugar expirar data | Em segundo lugar expirar data | Em terceiro lugar expirar data |
    | --- | --- | --- | --- | --- |
    | Alice | 1 de Janeiro | 1 de Fevereiro | 1 de Março | 1 de Abril |
    | BOB | 15 de Janeiro | 1 de Fevereiro | 1 de Março | 1 de Abril |

1. Utilizar o **duração antes de (dias) requer a aceitação do re** definição para especificar o número de dias antes do utilizador tem voltem os termos de utilização. Isso permite que os utilizadores seguirem o seu próprio agendamento. Por exemplo, se definir a duração como **30** dias, eis como expirações poderão ocorrer para que dois usuários:

    | Utilizador | Em primeiro lugar aceitar data | Em primeiro lugar expirar data | Em segundo lugar expirar data | Em terceiro lugar expirar data |
    | --- | --- | --- | --- | --- |
    | Alice | 1 de Janeiro | Dia 31 de Janeiro | 2 de mar | 1 de Abril |
    | BOB | 15 de Janeiro | 14 de Fevereiro | 16 de Março | 15 de Abril |

    É possível utilizar o **expirar consentimentos** e **duração antes de (dias) requer a aceitação do re** definições em conjunto, mas, normalmente, utilizar um ou outro.

1. Sob **acesso condicional**, utilize o **impor com o modelo de política de acesso condicional** lista para selecionar o modelo para aplicar os termos de utilização.

    ![Modelos de acesso condicional](./media/active-directory-tou/conditional-access-templates.png)

    | Modelo | Descrição |
    | --- | --- |
    | **Acesso a aplicações na cloud para todos os convidados** | Será criada uma política de acesso condicional para todos os convidados e todas as aplicações na cloud. Esta política afeta o portal do Azure. Quando isso for criado, poderá ser necessário a fim de sessão e início de sessão. |
    | **Acesso a aplicações na cloud em todos os utilizadores** | Será criada uma política de acesso condicional para todos os utilizadores e todas as aplicações na cloud. Esta política afeta o portal do Azure. Quando isso for criado, será solicitado a fim de sessão e início de sessão. |
    | **Política personalizada** | Selecione os utilizadores, grupos e aplicações para os quais serão aplicados os Termos de Utilização. |
    | **Criar política de acesso condicional mais tarde** | Estes termos de utilização vão aparecer na lista de controlo de concessões durante a criação de uma política de acesso condicional. |

    >[!IMPORTANT]
    >Os controlos de política de acesso condicional (incluindo os Termos de utilização) não suportam a imposição em contas de serviço. Recomendamos excluir todas as contas de serviço da política de acesso condicional.

     As políticas de acesso condicional personalizadas permitem especificar Termos de utilização detalhados, ao ponto de individualizar uma aplicação da cloud ou um grupo de utilizadores em particular. Para obter mais informações, consulte [início rápido: Exigir a termos de utilização para ser aceite antes de aceder a aplicações na cloud](../conditional-access/require-tou.md).

1. Clique em **Criar**.

    Se tiver selecionado um modelo de acesso condicional personalizada, em seguida, novo é apresentado um ecrã que lhe permite criar a política de acesso condicional personalizada.

    ![Política personalizada](./media/active-directory-tou/custom-policy.png)

    Posto isto, deverá agora conseguir ver os novos Termos de utilização.

    ![Adicionar Termos de Utilização](./media/active-directory-tou/create-tou.png)

## <a name="view-report-of-who-has-accepted-and-declined"></a>Ver relatório de quem tem aceitaram e recusaram
O painel Termos de utilização mostra uma contagem dos utilizadores que aceitaram e recusaram. Estas contagens e os utilizadores que aceitaram/recusaram são armazenados durante a vigência dos Termos de utilização.

1. Inicie sessão no Azure e navegue para **Termos de utilização** em [https://aka.ms/catou](https://aka.ms/catou).

    ![Painel Termos de utilização](./media/active-directory-tou/view-tou.png)

1. Para os termos de utilização, clique em números em **aceite** ou **recusada** para ver o estado atual para os utilizadores.

    ![Consentimentos de termos de utilização](./media/active-directory-tou/accepted-tou.png)

1. Para ver o histórico para um usuário individual, clique nas reticências (**...** ) e, em seguida **ver o histórico**.

    ![Menu de histórico do modo de exibição](./media/active-directory-tou/view-history-menu.png)

    No painel do histórico de exibição, verá um histórico de todos os aceitar, recusas e expirações.

    ![Painel de exibição de histórico](./media/active-directory-tou/view-history-pane.png)

## <a name="view-azure-ad-audit-logs"></a>Registos de auditoria de vista do Azure AD
Se pretender ver atividade adicional, os Termos de utilização do Azure AD incluem registos de auditoria. Cada consentimento do usuário dispara um evento nos registos de auditoria que são armazenados durante **30 dias**. Pode ver estes registos no portal ou transferi-los como um ficheiro. csv.

Para começar a utilizar com o Azure AD registos de auditoria, utilize o seguinte procedimento:

1. Inicie sessão no Azure e navegue para **Termos de utilização** em [https://aka.ms/catou](https://aka.ms/catou).

1. Selecione os termos de utilização.

1. Clique em **Ver registos de auditoria**.

    ![Painel Termos de utilização](./media/active-directory-tou/audit-tou.png)

1. No Azure AD ecrã de registos de auditoria, pode filtrar as informações utilizando as listas fornecidas para informações de registo de auditoria específicas de destino.

    Também pode clicar em **Transferir** para transferir as informações para um ficheiro. csv, para utilização local.

    ![Registos de auditoria](./media/active-directory-tou/audit-logs-tou.png)

    Se clicar num log, é apresentado um painel com detalhes de atividade adicionais.

    ![Detalhes da atividade](./media/active-directory-tou/audit-log-activity-details.png)

## <a name="what-terms-of-use-looks-like-for-users"></a>Qual o aspeto dos Termos de utilização para os utilizadores
Assim que os Termos de utilização são criados e impostos, os utilizadores, que são abrangidos pelo âmbito, irão ver o seguinte ecrã durante o início de sessão.

![Web-sessão do utilizador](./media/active-directory-tou/user-tou.png)

O ecrã seguinte mostra o aspeto dos Termos de utilização em dispositivos móveis.

![Utilizadores móvel início de sessão](./media/active-directory-tou/mobile-tou.png)

Os utilizadores só têm de aceitar os termos de utilização de uma vez e não irão ver os termos de utilização novamente no subsequentes inícios de sessão.

### <a name="how-users-can-review-their-terms-of-use"></a>Como os utilizadores podem rever os Termos de utilização
Os utilizadores podem rever e ver os termos de utilização que aceitaram, utilizando o procedimento seguinte.

1. Inicie sessão em [https://myapps.microsoft.com](https://myapps.microsoft.com).

1. No canto superior direito, clique no seu nome e selecione **perfil**.

    ![Perfil](./media/active-directory-tou/tou14.png)

1. Na página Perfil, clique em **Rever os termos de utilização**.

    ![Perfil - Rever termos de utilização](./media/active-directory-tou/tou13a.png)

1. A partir daí, pode rever os Termos de utilização que aceitou.

## <a name="edit-terms-of-use-details"></a>Editar detalhes de utilização dos termos de
É possível editar alguns detalhes dos termos de utilização, mas não é possível modificar um documento existente. O procedimento seguinte descreve como editar os detalhes.

1. Inicie sessão no Azure e navegue para **Termos de utilização** em [https://aka.ms/catou](https://aka.ms/catou).

1. Selecione os termos de utilização que pretende editar.

1. Clique em **editar termos**.

1. Em termos de edição do painel de utilização, altere o nome, nome a apresentar ou exigir que os utilizadores expandam os valores.

    Se existirem outras definições que pretende alterar, por exemplo, o documento PDF, exigir que os utilizadores dar consentimento em todos os dispositivos, expirar consentimentos, duração antes reacceptance ou política de acesso condicional, tem de criar novos termos de utilização.

    ![Editar os termos de utilização](./media/active-directory-tou/edit-tou.png)

1. Clique em **guardar** para guardar as alterações.

    Depois de guardar as alterações, os utilizadores não terão voltem estas edições.

## <a name="add-a-terms-of-use-language"></a>Adicionar termos de linguagem de utilização
O procedimento seguinte descreve como adicionar termos de linguagem de utilização.

1. Inicie sessão no Azure e navegue para **Termos de utilização** em [https://aka.ms/catou](https://aka.ms/catou).

1. Selecione os termos de utilização que pretende editar.

1. No painel de detalhes, clique nas **idiomas** separador.

    ![Adicionar Termos de Utilização](./media/active-directory-tou/languages-tou.png)

1. Clique em **Adicionar idioma**.

1. Em termos de adicionar do painel de linguagem de uso, carregue o localizados PDF e selecione o idioma.

    ![Adicionar Termos de Utilização](./media/active-directory-tou/language-add-tou.png)

1. Clique em **adicionar** para adicionar o idioma.

## <a name="per-device-terms-of-use"></a>Por dispositivo termos de utilização

O **exigir que os utilizadores dar consentimento em todos os dispositivos** definição permite-lhe exigir que os utilizadores finais aceitar os termos de utilização em todos os dispositivos estão a aceder a partir de. O utilizador final será necessário para associar os seus dispositivos no Azure AD. Quando o dispositivo é associado, o ID de dispositivo é utilizado para impor os termos de utilização em cada dispositivo.

Aqui está uma lista das plataformas suportadas e software.

> [!div class="mx-tableFixed"]
> |  | iOS | Android | Windows 10 | Outros |
> | --- | --- | --- | --- | --- |
> | **Aplicação nativa** | Sim | Sim | Sim |  |
> | **Microsoft Edge** | Sim | Sim | Sim |  |
> | **Internet Explorer** | Sim | Sim | Sim |  |
> | **Chrome (com extensão)** | Sim | Sim | Sim |  |

Por dispositivo termos de utilização tem as seguintes restrições:

- Só pode ser associado um dispositivo para um inquilino.
- Um utilizador tem de ter permissões para associar os seus dispositivos.
- A aplicação de inscrição do Intune não é suportada.

Se o dispositivo do utilizador não estiver associado, receberá uma mensagem que precisam para associar os seus dispositivos. A experiência deles será dependente de plataforma e o software.

### <a name="join-a-windows-10-device"></a>Junte-se um dispositivo Windows 10

Se um utilizador estiver a utilizar com o Windows 10 e o Microsoft Edge, receberá uma mensagem semelhante à seguinte ao [associarem os respetivos dispositivos](../user-help/user-help-join-device-on-network.md#to-join-an-already-configured-windows-10-device).

![Windows 10 e o Microsoft Edge - linha de comandos de dispositivo de associação](./media/active-directory-tou/per-device-win10-edge.png)

Se estiver usando o Chrome, eles serão solicitados a instalar o [extensão de contas do Windows 10](https://chrome.google.com/webstore/detail/windows-10-accounts/ppnbnpeolgkicgegkbkbjmhlideopiji).

### <a name="browsers"></a>Browsers

Se um utilizador estiver a utilizar o browser que não é suportada, serão solicitados a utilizar um browser diferente.

![Browser não suportado](./media/active-directory-tou/per-device-browser-unsupported.png)

## <a name="delete-terms-of-use"></a>Eliminar Termos de utilização
Pode eliminar Termos de utilização antigos com o procedimento seguinte.

1. Inicie sessão no Azure e navegue para **Termos de utilização** em [https://aka.ms/catou](https://aka.ms/catou).

1. Selecione os Termos de utilização que quer remover.

1. Clique em **Eliminar termos**.

1. Na mensagem que é apresentada a perguntar se pretende continuar, clique em **Sim**.

    ![Eliminar Termos de utilização](./media/active-directory-tou/delete-tou.png)

    Posto isto, deverá deixar de ver os Termos de utilização.

## <a name="deleted-users-and-active-terms-of-use"></a>Utilizadores eliminados e Termos de utilização ativos
Por predefinição, um utilizador eliminado fica num estado eliminado no Azure AD durante um período de 30 dias, durante o qual pode ser restaurado por um administrador, se necessário. Após 30 dias, esse utilizador é eliminado permanentemente. Além disso, através do portal do Azure Active Directory, um Administrador Global pode explicitamente [eliminar permanentemente um utilizador recentemente eliminado](../fundamentals/active-directory-users-restore.md) antes desse período de tempo ser atingido. Depois de um utilizador ser eliminado de maneira permanente, os dados subsequentes sobre esse utilizador serão removidos dos Termos de utilização ativos. As informações de auditoria sobre os utilizadores eliminados permanecem no registo de auditoria.

## <a name="policy-changes"></a>Alterações de política
As políticas de acesso condicional entram imediatamente em vigor. Quando isto acontecer, o administrador começa a "nuvens sad" ou "Problemas de token do Azure AD". O administrador tem de terminar sessão e inicie sessão novamente para atender a nova política.

>[!IMPORTANT]
> Os utilizadores abrangidos pelo âmbito definido terão de terminar e iniciar sessão de modo a satisfazerem uma nova política:
> - uma política de acesso condicional é ativada num documento Termos de utilização
> - ou é criado um segundo documento Termos de utilização

## <a name="b2b-guests-preview"></a>Convidados B2B (pré-visualização)

A maioria das organizações tem um processo para seus funcionários para aceitar os termos de suas organizações de instruções de utilização e a privacidade. Mas como pode impor os consentimentos mesmo para o Azure AD para empresas quando estes forem adicionados por meio do SharePoint ou equipas os convidados (B2B)? Utilizar acesso condicional e os termos de utilização, pode impor uma política diretamente para utilizadores convidados B2B. Durante o fluxo de resgate de convite, é apresentada ao utilizador com os termos de utilização. Este suporte está atualmente em pré-visualização.

Termos de utilização só serão apresentados quando o utilizador tem uma conta de convidado no Azure AD. O SharePoint Online atualmente tem um [ad hoc experiência de destinatário partilha externa](/sharepoint/what-s-new-in-sharing-in-targeted-release) para partilhar um documento ou uma pasta que não necessita do utilizador tem de ter uma conta de convidado. Neste caso, os termos de utilização não é apresentada.

![Todos os utilizadores convidados](./media/active-directory-tou/b2b-guests.png)

## <a name="support-for-cloud-apps-preview"></a>Suporte para aplicações na cloud (pré-visualização)

Termos de utilização podem ser utilizados para aplicações de cloud diferente, como o Azure Information Protection e o Microsoft Intune. Este suporte está atualmente em pré-visualização.

### <a name="azure-information-protection"></a>Azure Information Protection

Pode configurar uma política de acesso condicional para a aplicação Azure Information Protection e exigir termos de utilização, quando um utilizador acede a um documento protegido. Isso irá disparar um termos de utilização antes de um utilizador com acesso a um documento protegido pela primeira vez.

![Aplicação de cloud do Azure Information Protection](./media/active-directory-tou/cloud-app-info-protection.png)

### <a name="microsoft-intune-enrollment"></a>Inscrição do Microsoft Intune

Pode configurar uma política de acesso condicional para a aplicação de inscrição do Microsoft Intune e necessitam de termos de utilização antes do inscrever um dispositivo no Intune. Para obter mais informações, consulte a leitura [escolher o direito de termos de solução para a sua postagem do blog de organização](https://go.microsoft.com/fwlink/?linkid=2010506&clcid=0x409).

![Aplicação de cloud do Microsoft Intune](./media/active-directory-tou/cloud-app-intune.png)

> [!NOTE]
> A aplicação de inscrição do Intune não é suportada para [por dispositivo termos de utilização](#per-device-terms-of-use).

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes

**P: Como posso ver se um utilizador aceitou os termos de utilização e quando?**<br />
R: Em termos do painel de utilização, clique no número em **aceite**. Também pode ver ou procurar a atividade de aceitar no Azure AD registos de auditoria. Para obter mais informações, consulte a vista de relatório de quem tem aceitaram e recusaram e [registos de auditoria de vista do Azure AD](#view-azure-ad-audit-logs).

**P: O período de tempo são informações armazenadas?**<br />
R: Conta de utilizador em termos de relatório de utilização e que aceite/recusado é armazenado durante o ciclo de vida dos termos de utilização. A auditoria do Azure AD, os registos são armazenados durante 30 dias.

**P: Por que vejo um número diferente de consentimentos em termos de relatório de utilização vs. o Azure AD, registos de auditoria?**<br />
R: Os termos de relatório de utilização são armazenados durante a vida útil do que termos de utilização, ao mesmo tempo a auditoria do Azure AD, os registos são armazenados durante 30 dias. Além disso, os termos de relatório de utilização só apresenta o estado de consentimento atual de utilizadores. Por exemplo, se um utilizador recusa e, em seguida, aceita, os termos de utilização relatório mostrará apenas esse utilizador aceite. Se precisar de ver o histórico, pode utilizar o Azure AD registos de auditoria.

**P: Se eu editar os detalhes para os termos de utilização, é necessário que os utilizadores aceitar novamente?**<br />
R: Não, se for um administrador editar os detalhes para os termos de utilização (nome, nome a apresentar, exigir que os utilizadores expandir ou adicionar um idioma), que não é necessário que os utilizadores voltem os novos termos.

**P: Pode atualizar uma existente termos de utilização documento?**<br />
R: Atualmente, não é possível atualizar uma existente termos de documento de utilização. Para alterar os termos de utilização de documento, terá de criar novos termos de instância de utilização.

**P: Se forem hiperlinks em termos de utilizar o documentos PDF, serão os utilizadores finais poderão clicar nas mesmas?**<br />
R: O PDF é composto por predefinição como um JPEG, para que não sejam clicáveis hiperlinks. Os utilizadores têm a opção de selecionar **com problemas em visualizar? Clique aqui**, que renderiza o PDF nativamente onde os hiperlinks são suportados.

**P: Termos de utilização podem suportar a vários idiomas?**<br />
R: Sim. Atualmente, existem 108 idiomas diferentes, um administrador pode configurar para um único termos de utilização. Um administrador pode carregar vários documentos PDF e etiquetar os documentos com um idioma correspondente (até 108). Quando os utilizadores finais iniciam sessão, vamos examinar suas preferências de idioma do navegador e exibir o documento correspondente. Se não houver nenhuma correspondência, exibimos o documento predefinido, que é o primeiro documento que é carregado.

**P: Quando é que os termos de utilização são acionados?**<br />
R: Os termos de utilização são acionados durante a experiência de início de sessão.

**P: Que aplicações posso orientar os termos de utilização para?**<br />
R: Pode criar uma política de acesso condicional nas aplicações empresariais que utilizam autenticação moderna. Para obter mais informações, consulte [aplicações empresariais](./../manage-apps/view-applications-portal.md).

**P: Pode adicionar vários termos de utilização para um determinado usuário ou a aplicação?**<br />
R: Sim, através da criação de várias políticas de acesso condicional direcionado para esses grupos ou aplicações. Se um utilizador for abrangido pelo âmbito de vários termos de utilização, eles aceitam uma termos de utilização de cada vez.

**P: O que acontece se um utilizador recusar os termos de utilização?**<br />
R: O utilizador está bloqueado de aceder à aplicação. O utilizador terá de iniciar sessão novamente e aceitar os termos para conseguir obter acesso.

**P: É possível unaccept termos de utilização que foram anteriormente aceites?**<br />
R: Pode [revisão ainda aceitado os termos de utilização](#how-users-can-review-their-terms-of-use), mas atualmente não há uma forma de unaccept.

**P: O que acontece se também estou a utilizar o Intune termos e condições?**<br />
R: Se tiver configurado a ambos os termos de AD do Azure de utilização e [Intune termos e condições](/intune/terms-and-conditions-create), irá ser pedido ao utilizador para aceitar ambos. Para obter mais informações, consulte a [escolher o direito de termos de solução para a sua postagem do blog de organização](https://go.microsoft.com/fwlink/?linkid=2010506&clcid=0x409).

## <a name="next-steps"></a>Passos Seguintes

- [Quickstart: Exigir a termos de utilização para ser aceite antes de aceder a aplicações na cloud](../conditional-access/require-tou.md)
- [Melhores práticas para acesso condicional no Azure Active Directory](../conditional-access/best-practices.md)
