---
title: Termos de utilização - Azure Active Directory | Documentos da Microsoft
description: Começar a utilizar os termos do Azure Active Directory de utilização para apresentar informações aos funcionários ou convidados antes de poder aceder.
services: active-directory
ms.service: active-directory
ms.subservice: compliance
ms.topic: conceptual
ms.date: 05/29/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jocastel
ms.collection: M365-identity-device-management
ms.openlocfilehash: f891642761a2f692158efbd9111ff96444c4269d
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/29/2019
ms.locfileid: "67476194"
---
# <a name="azure-active-directory-terms-of-use"></a>O Azure Active Directory termos de utilização

O Azure AD termos de utilização fornece um método simples que as organizações podem utilizar para apresentar informações aos utilizadores finais. Tal disponibilização garante a visualização das exclusões de responsabilidade relevantes no que se refere a requisitos legais ou de conformidade por parte dos utilizadores. Este artigo descreve como começar com os termos de utilização.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="overview-videos"></a>Vídeos de descrição geral

O vídeo seguinte apresenta uma rápida visão geral dos termos de utilização.

>[!VIDEO https://www.youtube.com/embed/tj-LK0abNao]

Para ver vídeos adicionais, consulte:
- [Como implementar os termos de utilização no Azure Active Directory](https://www.youtube.com/embed/N4vgqHO2tgY)
- [Como implementar termos de utilização no Azure Active Directory](https://www.youtube.com/embed/t_hA4y9luCY)

## <a name="what-can-i-do-with-terms-of-use"></a>O que posso fazer com os termos de utilização?

O Azure AD termos de utilização tem as seguintes capacidades:

- Necessitam de funcionários ou convidados aceitar os termos de utilização antes de poder aceder.
- Necessitam de funcionários ou convidados aceitar os termos de utilização em todos os dispositivos antes de poder aceder.
- Necessitam de funcionários ou convidados aceitar os termos de utilização numa agenda periódica.
- Solicitar que os funcionários ou convidados a aceitar os termos de utilização antes de registar as informações de segurança no Azure multi-factor Authentication (MFA).
- Exigir que os funcionários aceitar os termos de utilização antes de registar as informações de segurança na reposição de palavra-passe self-service do Azure AD (SSPR).
- Apresentar gerais termos de utilização para todos os utilizadores na sua organização.
- Apresentar os termos de utilização com base em atributos de um utilizador (por exemplo, específicos médicos por oposição a enfermeiros ou colaboradores nacionais por oposição a colaboradores internacionais, uma distinção feita através da utilização de [grupos dinâmicos](../users-groups-roles/groups-dynamic-membership.md)).
- Apresentar os termos de utilização específicos ao aceder a aplicações de impacto comercial de elevada, como o Salesforce.
- Apresentar os termos de utilização em idiomas diferentes.
- Lista que tem ou não aceitou os termos de utilização.
- Ajudar a cumprir os regulamentos de privacidade.
- Exiba um log de termos de utilização atividade de conformidade e auditoria.
- Criar e gerir os termos de utilização com [Microsoft Graph APIs](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/agreement) (atualmente em pré-visualização).

## <a name="prerequisites"></a>Pré-requisitos

Para utilizar e configurar termos do Azure AD de utilização, tem de ter:

- Uma subscrição Premium P1, Premium P2, EMS E3 ou EMS E5 do Azure AD.
   - Se não tiver uma destas subscrições, pode [obter o Azure AD Premium](../fundamentals/active-directory-get-started-premium.md) ou [ativar a versão de avaliação do Azure AD Premium](https://azure.microsoft.com/trial/get-started-active-directory/).
- Uma das seguintes contas de administrador para o diretório que pretende configurar:
   - Administrador Global
   - Administrador de Segurança
   - Administrador de Acesso Condicional

## <a name="terms-of-use-document"></a>Documento Termos de utilização

O Azure AD termos de utilização utiliza o formato PDF para apresentar conteúdo. O ficheiro PDF pode ter qualquer tipo de conteúdo, como documentos contratuais existentes, permitindo-lhe recolher contratos de utilizador final durante o início de sessão do utilizador. Para suportar os utilizadores em dispositivos móveis, o tamanho de tipo de letra recomendado no PDF é o ponto de 24.

## <a name="add-terms-of-use"></a>Adicionar termos de utilização

Depois de finalizar os seus termos de utilização de documentos, utilize o procedimento seguinte para adicioná-lo.

1. Inicie sessão no Azure Global como administrador, administrador de segurança ou administrador de acesso condicional.
1. Navegue para **Termos de utilização** em [https://aka.ms/catou](https://aka.ms/catou).

   ![Acesso condicional - termos de utilização painel](./media/terms-of-use/tou-blade.png)

1. Clique em **Novos termos**.

   ![Novo termo do painel de utilização para especificar os seus termos de utilização de definições](./media/terms-of-use/new-tou.png)

1. Na **nome** , introduza um nome para os termos de utilização que será utilizado no portal do Azure.
1. Na **nome a apresentar** , introduza um título que os utilizadores veem quando iniciam sessão.
1. Para **termos de utilização documento**, navegue até sua finalizado dos termos de utilização PDF e selecioná-lo.
1. Selecione o idioma para os seus termos de utilização de documento. A opção de idioma permite-lhe carregar vários termos de utilização, cada um com um idioma diferente. A versão dos termos de utilização que um utilizador final verá serão baseados nas respetivas preferências do browser.
1. Para exigir que os utilizadores finais que vejam os termos de utilização antes de os aceitarem, defina **exigir que os utilizadores expandam os termos de utilização** ao **no**.
1. Para exigir que os utilizadores finais a aceitar os termos de utilização em todos os dispositivos estão a aceder a partir de, defina **exigir que os utilizadores dar consentimento em todos os dispositivos** ao **no**. Para obter mais informações, consulte [termos de por dispositivo de utilização](#per-device-terms-of-use).
1. Se quiser expirar os termos de utilização de consentimentos com base numa agenda, defina **expirar consentimentos** ao **no**. Quando definido como no, são apresentadas duas definições de agendamento adicionais.

   ![Definições de consentimentos para definir a data de início, a frequência e duração de expirar](./media/terms-of-use/expire-consents.png)

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

   ![Lista de lista pendente de acesso condicional para selecionar um modelo de política](./media/terms-of-use/conditional-access-templates.png)

   | Modelo | Descrição |
   | --- | --- |
   | **Acesso a aplicações na cloud para todos os convidados** | Será criada uma política de acesso condicional para todos os convidados e todas as aplicações na cloud. Esta política afeta o portal do Azure. Quando isso for criado, poderá ser necessário a fim de sessão e início de sessão. |
   | **Acesso a aplicações na cloud em todos os utilizadores** | Será criada uma política de acesso condicional para todos os utilizadores e todas as aplicações na cloud. Esta política afeta o portal do Azure. Quando isso for criado, será solicitado a fim de sessão e início de sessão. |
   | **Política personalizada** | Selecione os utilizadores, grupos e aplicações que serão aplicadas a este termos de utilização. |
   | **Criar política de acesso condicional mais tarde** | Este termos de utilização aparecerá na lista de controle de conceder ao criar uma política de acesso condicional. |

   >[!IMPORTANT]
   >Controlos de política de acesso condicionais (incluindo os termos de utilização) não suportam a imposição em contas de serviço. Recomendamos excluir todas as contas de serviço da política de acesso condicional.

    As políticas de acesso condicional personalizadas permitem granulares termos de utilização, para baixo para uma aplicação da cloud ou um grupo de utilizadores. Para obter mais informações, consulte [início rápido: Exigir a termos de utilização para ser aceite antes de aceder a aplicações na cloud](require-tou.md).

1. Clique em **Criar**.

   Se tiver selecionado um modelo de acesso condicional personalizado, em seguida, novo é apresentado um ecrã que permite-lhe criar a política de acesso condicional personalizada.

   ![Novo painel de acesso condicional, se tiver escolhido o modelo de política de acesso condicional personalizado](./media/terms-of-use/custom-policy.png)

   Agora, deverá ver os novos termos de utilização.

   ![Novos termos de utilização listados em termos do painel de utilização](./media/terms-of-use/create-tou.png)

## <a name="view-report-of-who-has-accepted-and-declined"></a>Ver relatório de quem tem aceitaram e recusaram

O painel Termos de utilização mostra uma contagem dos utilizadores que aceitaram e recusaram. Essas contagens e quem aceitou/recusado são armazenadas durante o ciclo de vida dos termos de utilização.

1. Inicie sessão no Azure e navegue para **Termos de utilização** em [https://aka.ms/catou](https://aka.ms/catou).

   ![Termos de listagem o número de show de utilizador de painel de utilização aceitaram e recusaram](./media/terms-of-use/view-tou.png)

1. Para os termos de utilização, clique em números em **aceite** ou **recusada** para ver o estado atual para os utilizadores.

   ![Termos de utilização os utilizadores que aceitaram a listagem de painel de consentimentos](./media/terms-of-use/accepted-tou.png)

1. Para ver o histórico para um usuário individual, clique nas reticências ( **...** ) e, em seguida **ver o histórico**.

   ![Visualizar o menu de contexto histórico para um utilizador](./media/terms-of-use/view-history-menu.png)

   No painel do histórico de exibição, verá um histórico de todos os aceitar, recusas e expirações.

   ![Ver listas de painel que aceita o histórico de histórico, recusas e expirações de um utilizador](./media/terms-of-use/view-history-pane.png)

## <a name="view-azure-ad-audit-logs"></a>Registos de auditoria de vista do Azure AD

Se quiser ver atividades adicionais, termos do Azure AD de utilização inclui registos de auditoria. Cada consentimento do usuário dispara um evento nos registos de auditoria que são armazenados durante **30 dias**. Pode ver estes registos no portal ou transferi-los como um ficheiro. csv.

Para começar a utilizar com o Azure AD registos de auditoria, utilize o seguinte procedimento:

1. Inicie sessão no Azure e navegue para **Termos de utilização** em [https://aka.ms/catou](https://aka.ms/catou).
1. Selecione os termos de utilização.
1. Clique em **Ver registos de auditoria**.

   ![Termos de utilização painel com a exibição realçada de opção de registos de auditoria](./media/terms-of-use/audit-tou.png)

1. No Azure AD ecrã de registos de auditoria, pode filtrar as informações utilizando as listas fornecidas para informações de registo de auditoria específicas de destino.

   Também pode clicar em **Transferir** para transferir as informações para um ficheiro. csv, para utilização local.

   ![Tela com uma listagem de data, a política de destino, iniciado por e a atividade de registos de auditoria do Azure AD](./media/terms-of-use/audit-logs-tou.png)

   Se clicar num log, é apresentado um painel com detalhes de atividade adicionais.

   ![Detalhes da atividade para um registo com a atividade, do Estado da atividade, iniciada por, a política de destino](./media/terms-of-use/audit-log-activity-details.png)

## <a name="what-terms-of-use-looks-like-for-users"></a>Que termos de utilização é semelhante para os utilizadores

Depois de termos de utilização é criado e imposta, os utilizadores que estão no âmbito, verá o ecrã seguinte durante o início de sessão.

![Exemplo de termos de utilização que aparece quando um utilizador inicia sessão](./media/terms-of-use/user-tou.png)

Os utilizadores podem ver os termos de utilização e, se necessário, utilize os botões para ampliar e reduzir.

![Vista de termos de utilização com botões de zoom](./media/terms-of-use/zoom-buttons.png)

O ecrã seguinte mostra o aspeto de termos de utilização em dispositivos móveis.

![Exemplo de termos de utilização que aparece quando um utilizador inicia sessão num dispositivo móvel](./media/terms-of-use/mobile-tou.png)

Os utilizadores só têm de aceitar os termos de utilização de uma vez e não verá os termos de utilização novamente nos inícios de sessão subsequentes.

### <a name="how-users-can-review-their-terms-of-use"></a>Como os utilizadores podem analisar seus termos de utilização

Os utilizadores podem rever e ver os termos de utilização aceitaram utilizando o procedimento seguinte.

1. Inicie sessão em [https://myapps.microsoft.com](https://myapps.microsoft.com).
1. No canto superior direito, clique no seu nome e selecione **perfil**.

   ![Site MyApps, com o painel do utilizador aberto](./media/terms-of-use/tou14.png)

1. Na página Perfil, clique em **Rever os termos de utilização**.

   ![Página de perfil para um utilizador que mostra os termos de revisão de ligação de utilização](./media/terms-of-use/tou13a.png)

1. A partir daí, pode rever os termos de utilização que aceitou.

## <a name="edit-terms-of-use-details"></a>Editar detalhes de utilização dos termos de

É possível editar alguns detalhes dos termos de utilização, mas não é possível modificar um documento existente. O procedimento seguinte descreve como editar os detalhes.

1. Inicie sessão no Azure e navegue para **Termos de utilização** em [https://aka.ms/catou](https://aka.ms/catou).
1. Selecione os termos de utilização que pretende editar.
1. Clique em **editar termos**.
1. Em termos de edição do painel de utilização, altere o nome, nome a apresentar ou exigir que os utilizadores expandam os valores.

   Se existirem outras definições que pretende alterar, por exemplo, o documento PDF, exigir que os utilizadores dar consentimento em todos os dispositivos, expirar consentimentos, duração antes reacceptance ou política de acesso condicional, tem de criar novos termos de utilização.

   ![Editar termos de utilizar painel que mostra o nome e expanda as opções](./media/terms-of-use/edit-tou.png)

1. Clique em **guardar** para guardar as alterações.

   Depois de guardar as alterações, os utilizadores não terão voltem estas edições.

## <a name="add-a-terms-of-use-language"></a>Adicionar termos de linguagem de utilização

O procedimento seguinte descreve como adicionar termos de linguagem de utilização.

1. Inicie sessão no Azure e navegue para **Termos de utilização** em [https://aka.ms/catou](https://aka.ms/catou).
1. Selecione os termos de utilização que pretende editar.
1. No painel de detalhes, clique nas **idiomas** separador.

   ![Termos de utilização selecionado e o que mostra o separador de idiomas no painel de detalhes](./media/terms-of-use/languages-tou.png)

1. Clique em **Adicionar idioma**.
1. Em termos de adicionar do painel de linguagem de uso, carregue o localizados PDF e selecione o idioma.

   ![Adicionar termos de painel de linguagem de uso com opções para carregar o localizados PDFs](./media/terms-of-use/language-add-tou.png)

1. Clique em **adicionar** para adicionar o idioma.

## <a name="per-device-terms-of-use"></a>Termos de por dispositivo de utilização

O **exigir que os utilizadores dar consentimento em todos os dispositivos** definição permite-lhe exigir que os utilizadores finais aceitar os termos de utilização em todos os dispositivos estão a aceder a partir de. O utilizador final será necessário para associar os seus dispositivos no Azure AD. Quando o dispositivo é associado, o ID de dispositivo é utilizado para impor os termos de utilização em cada dispositivo.

Aqui está uma lista das plataformas suportadas e software.

> [!div class="mx-tableFixed"]
> |  | iOS | Android | Windows 10 | Outros |
> | --- | --- | --- | --- | --- |
> | **Aplicação nativa** | Sim | Sim | Sim |  |
> | **Microsoft Edge** | Sim | Sim | Sim |  |
> | **Internet Explorer** | Sim | Sim | Sim |  |
> | **Chrome (com extensão)** | Sim | Sim | Sim |  |

Termos de por dispositivo de utilização tem as seguintes restrições:

- Só pode ser associado um dispositivo para um inquilino.
- Um utilizador tem de ter permissões para associar os seus dispositivos.
- A aplicação de inscrição do Intune não é suportada.
- Os utilizadores do Azure AD B2B não são suportados.

Se o dispositivo do utilizador não estiver associado, receberá uma mensagem que precisam para associar os seus dispositivos. A experiência deles será dependente de plataforma e o software.

### <a name="join-a-windows-10-device"></a>Junte-se um dispositivo Windows 10

Se um utilizador estiver a utilizar com o Windows 10 e o Microsoft Edge, receberá uma mensagem semelhante à seguinte ao [associarem os respetivos dispositivos](../user-help/user-help-join-device-on-network.md#to-join-an-already-configured-windows-10-device).

![Windows 10 e o Microsoft Edge - mensagem a indicar que o dispositivo tem de estar registado](./media/terms-of-use/per-device-win10-edge.png)

Se estiver usando o Chrome, eles serão solicitados a instalar o [extensão de contas do Windows 10](https://chrome.google.com/webstore/detail/windows-10-accounts/ppnbnpeolgkicgegkbkbjmhlideopiji).

### <a name="browsers"></a>Browsers

Se um utilizador estiver a utilizar o browser que não é suportada, serão solicitados a utilizar um browser diferente.

![Mensagem a indicar que o dispositivo tem de ser registado, mas o browser não é suportado](./media/terms-of-use/per-device-browser-unsupported.png)

## <a name="delete-terms-of-use"></a>Eliminar termos de utilização

Pode eliminar termos de utilização utilizando o procedimento seguinte antigos.

1. Inicie sessão no Azure e navegue para **Termos de utilização** em [https://aka.ms/catou](https://aka.ms/catou).
1. Selecione os termos de utilização que quer remover.
1. Clique em **Eliminar termos**.
1. Na mensagem que é apresentada a perguntar se pretende continuar, clique em **Sim**.

   ![Mensagem a pedir confirmação ao eliminar termos de utilização](./media/terms-of-use/delete-tou.png)

   Já não deverá ver os termos de utilização.

## <a name="deleted-users-and-active-terms-of-use"></a>Utilizadores eliminados e o Active Directory termos de utilização

Por predefinição, um utilizador eliminado fica num estado eliminado no Azure AD durante um período de 30 dias, durante o qual pode ser restaurado por um administrador, se necessário. Após 30 dias, esse utilizador é eliminado permanentemente. Além disso, através do portal do Azure Active Directory, um Administrador Global pode explicitamente [eliminar permanentemente um utilizador recentemente eliminado](../fundamentals/active-directory-users-restore.md) antes desse período de tempo ser atingido. Um utilizador foi eliminado permanentemente, subsequentes dados sobre o que o utilizador serão removidos dos Active Directory termos de utilização. As informações de auditoria sobre os utilizadores eliminados permanecem no registo de auditoria.

## <a name="policy-changes"></a>Alterações de política

Políticas de acesso condicional em vigor imediatamente. Quando isto acontecer, o administrador começa a "nuvens sad" ou "Problemas de token do Azure AD". O administrador tem de terminar sessão e inicie sessão novamente para atender a nova política.

> [!IMPORTANT]
> Os utilizadores abrangidos pelo âmbito definido terão de terminar e iniciar sessão de modo a satisfazerem uma nova política:
>
> - uma política de acesso condicional estiver ativada em termos de utilização
> - ou se for criado um segundo documento de termos de utilização

## <a name="b2b-guests-preview"></a>Convidados B2B (pré-visualização)

A maioria das organizações tem um processo para seus funcionários para aceitar os termos de suas organizações de instruções de utilização e a privacidade. Mas como pode impor os consentimentos mesmo para o Azure AD para empresas quando estes forem adicionados por meio do SharePoint ou equipas os convidados (B2B)? Utilizar acesso condicional e os termos de utilização, pode impor uma política diretamente para utilizadores convidados B2B. Durante o fluxo de resgate de convite, é apresentada ao utilizador com os termos de utilização. Este suporte está atualmente em pré-visualização.

Termos de utilização só serão apresentados quando o utilizador tem uma conta de convidado no Azure AD. O SharePoint Online atualmente tem um [ad hoc experiência de destinatário partilha externa](/sharepoint/what-s-new-in-sharing-in-targeted-release) para partilhar um documento ou uma pasta que não necessita do utilizador tem de ter uma conta de convidado. Neste caso, os termos de utilização não é apresentada.

![Os utilizadores e o painel de grupos - incluem o separador com opção de utilizadores convidados todos verificado](./media/terms-of-use/b2b-guests.png)

## <a name="support-for-cloud-apps-preview"></a>Suporte para aplicações na cloud (pré-visualização)

Termos de utilização podem ser utilizados para aplicações de cloud diferente, como o Azure Information Protection e o Microsoft Intune. Este suporte está atualmente em pré-visualização.

### <a name="azure-information-protection"></a>Azure Information Protection

Pode configurar uma política de acesso condicional para a aplicação Azure Information Protection e exigir termos de utilização, quando um utilizador acede a um documento protegido. Isso irá disparar um termos de utilização antes de um utilizador com acesso a um documento protegido pela primeira vez.

![Painel de aplicações na cloud com a aplicação do Microsoft Azure Information Protection selecionada](./media/terms-of-use/cloud-app-info-protection.png)

### <a name="microsoft-intune-enrollment"></a>Inscrição do Microsoft Intune

Pode configurar uma política de acesso condicional para a aplicação de inscrição do Microsoft Intune e necessitam de termos de utilização antes do inscrever um dispositivo no Intune. Para obter mais informações, consulte a leitura [escolher o direito de termos de solução para a sua postagem do blog de organização](https://go.microsoft.com/fwlink/?linkid=2010506&clcid=0x409).

![Painel de aplicações na cloud com a aplicação Microsoft Intune selecionada](./media/terms-of-use/cloud-app-intune.png)

> [!NOTE]
> A aplicação de inscrição do Intune não é suportada para [termos de por dispositivo de utilização](#per-device-terms-of-use).

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

**P: É possível unaccept termos de utilização que foi anteriormente aceites?**<br />
R: Pode [revisão ainda aceitado os termos de utilização](#how-users-can-review-their-terms-of-use), mas atualmente não há uma forma de unaccept.

**P: O que acontece se também estou a utilizar o Intune termos e condições?**<br />
R: Se tiver configurado a ambos os termos do Azure AD de utilização e [Intune termos e condições](/intune/terms-and-conditions-create), irá ser pedido ao utilizador para aceitar ambos. Para obter mais informações, consulte a [escolher o direito de termos de solução para a sua postagem do blog de organização](https://go.microsoft.com/fwlink/?linkid=2010506&clcid=0x409).

**P: Quais pontos de extremidade o usa os termos de serviço de utilização para a autenticação?**<br />
R: Termos de utilização utiliza os seguintes pontos finais para a autenticação: https://tokenprovider.termsofuse.identitygovernance.azure.com e https://account.activedirectory.windowsazure.com. Se a sua organização tiver uma lista de permissões de URLs para a inscrição, terá de adicionar estes pontos finais à sua lista de permissões, juntamente com os pontos de extremidade do Azure AD para iniciar sessão.

## <a name="next-steps"></a>Passos Seguintes

- [Quickstart: Exigir a termos de utilização para ser aceite antes de aceder a aplicações na cloud](require-tou.md)
- [Melhores práticas para acesso condicional no Azure Active Directory](best-practices.md)
