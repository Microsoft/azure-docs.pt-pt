---
title: Termos de utilização - Diretório Ativo Azure [ Azure Ative Diretório ] Microsoft Docs
description: Começar a usar os termos de utilização do Azure Ative Diretório para apresentar informações aos colaboradores ou hóspedes antes de ter acesso.
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
ms.openlocfilehash: a2f06a7c88a7c17f5f93201192664c2d4a97564e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79480968"
---
# <a name="azure-active-directory-terms-of-use"></a>Termos de utilização do Diretório Ativo Azure

Os termos de utilização da AD Azure fornecem um método simples que as organizações podem usar para apresentar informações aos utilizadores finais. Tal disponibilização garante a visualização das exclusões de responsabilidade relevantes no que se refere a requisitos legais ou de conformidade por parte dos utilizadores. Este artigo descreve como começar com termos de uso.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="overview-videos"></a>Vídeos de visão geral

O vídeo que se segue fornece uma visão geral rápida dos termos de utilização.

>[!VIDEO https://www.youtube.com/embed/tj-LK0abNao]

Para vídeos adicionais, consulte:
- [Como implementar termos de utilização no Diretório Ativo azure](https://www.youtube.com/embed/N4vgqHO2tgY)
- [Como lançar termos de utilização no Diretório Ativo Azure](https://www.youtube.com/embed/t_hA4y9luCY)

## <a name="what-can-i-do-with-terms-of-use"></a>O que posso fazer com os termos de uso?

Os termos de utilização da AD Azure têm as seguintes capacidades:

- Exija que os colaboradores ou hóspedes aceitem os seus termos de utilização antes de terem acesso.
- Exija que os colaboradores ou hóspedes aceitem os seus termos de utilização em todos os dispositivos antes de terem acesso.
- Exija que os colaboradores ou hóspedes aceitem os seus termos de utilização num horário recorrente.
- Exija que os colaboradores ou hóspedes aceitem os seus termos de utilização antes de registar informações de segurança na Autenticação Multi-Factor Azure (MFA).
- Exija que os colaboradores aceitem os seus termos de utilização antes de registar informações de segurança no reset de palavra-passe de autosserviço da Azure AD (SSPR).
- Apresentar termos gerais de utilização para todos os utilizadores da sua organização.
- Apresentar termos de utilização específicos com base nos atributos do utilizador (ex. médicos por oposição a enfermeiros ou colaboradores nacionais por oposição a colaboradores internacionais, uma distinção feita através da utilização de [grupos dinâmicos](../users-groups-roles/groups-dynamic-membership.md)).
- Apresentar termos de utilização específicos ao aceder a aplicações de alto impacto empresarial, como a Salesforce.
- Presentes termos de utilização em diferentes línguas.
- Lista quem aceitou ou não aceitou os seus termos de uso.
- Ajude a cumprir os regulamentos de privacidade.
- Apresentar um registo de termos de atividade de utilização para conformidade e auditoria.
- Criar e gerir os termos de utilização utilizando [APIs](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/agreement) do Microsoft Graph (atualmente em pré-visualização).

## <a name="prerequisites"></a>Pré-requisitos

Para utilizar e configurar os termos de utilização da AD Azure, deve ter:

- Uma subscrição Premium P1, Premium P2, EMS E3 ou EMS E5 do Azure AD.
   - Se não tiver uma destas subscrições, pode [obter o Azure AD Premium](../fundamentals/active-directory-get-started-premium.md) ou [ativar a versão de avaliação do Azure AD Premium](https://azure.microsoft.com/trial/get-started-active-directory/).
- Uma das seguintes contas de administrador para o diretório que pretende configurar:
   - Administrador Global
   - Administrador de Segurança
   - Administrador de Acesso Condicional

## <a name="terms-of-use-document"></a>Documento Termos de utilização

Os termos de utilização do Azure AD utilizam o formato PDF para apresentar conteúdo. O ficheiro PDF pode ter qualquer tipo de conteúdo, como documentos contratuais existentes, permitindo-lhe recolher contratos de utilizador final durante o início de sessão do utilizador. Para apoiar os utilizadores em dispositivos móveis, o tamanho da fonte recomendado no PDF é de 24 pontos.

## <a name="add-terms-of-use"></a>Adicionar termos de utilização

Depois de finalizar os seus termos de documento de utilização, utilize o seguinte procedimento para adicioná-lo.

1. Inscreva-se no Azure como Administrador Global, Administrador de Segurança ou Administrador de Acesso Condicional.
1. Navegue para **Termos de utilização** em [https://aka.ms/catou](https://aka.ms/catou).

   ![Acesso Condicional - Termos de utilização da lâmina](./media/terms-of-use/tou-blade.png)

1. Clique em **Novos termos**.

   ![Novo termo de painel de uso para especificar os seus termos de utilização](./media/terms-of-use/new-tou.png)

1. Na caixa **Nome,** introduza um nome para os termos de utilização que serão utilizados no portal Azure.
1. Na caixa de **nomes Display,** introduza um título que os utilizadores vejam quando iniciarem o seu sessão.
1. Para **obter o documento de utilização,** navegue para os termos de utilização finais do PDF e selecione-o.
1. Selecione o idioma para os seus termos de uso. A opção de idioma permite-lhe carregar vários termos de utilização, cada um com um idioma diferente. A versão dos termos de utilização que um utilizador final verá serão baseados nas respetivas preferências do browser.
1. Para exigir que os utilizadores finais vejam os termos de utilização antes de os aceitarem, detetete **requerer que os utilizadores expandam os termos de utilização** para **On**.
1. Para exigir que os utilizadores finais aceitem os seus termos de utilização em todos os dispositivos a que estão a aceder, detete **requerer que os utilizadores consintam em todos os dispositivos** para **On**. Os utilizadores podem ser obrigados a instalar aplicações adicionais se esta opção estiver ativada. Para mais informações, consulte [os termos de utilização por dispositivo](#per-device-terms-of-use).
1. Se pretender expirar os consentimentos de utilização num horário, detete os **consentimentos de Expiração** para **On**. Quando definido para On, são apresentadas duas definições de horário adicionais.

   ![Expira as definições para definir a data de início, frequência e duração](./media/terms-of-use/expire-consents.png)

1. Utilize as definições **expiradas** e de **frequência** para especificar o calendário dos termos de validade. A tabela seguinte mostra o resultado para um par de definições de exemplo:

   | Expirar a partir de | Frequência | Resultado |
   | --- | --- | --- |
   | A data de hoje  | Mensalmente | A partir de hoje, os utilizadores devem aceitar os termos de utilização e depois reaceitar todos os meses. |
   | Data no futuro  | Mensalmente | A partir de hoje, os utilizadores devem aceitar os termos de utilização. Quando a data futura ocorrer, os consentimentos expirarão e os utilizadores devem reaceitar todos os meses.  |

   Por exemplo, se definir o prazo a partir da data de **1 de janeiro** e a frequência para **mensalmente,** eis como podem ocorrer expirações para dois utilizadores:

   | Utilizador | Primeira data de aceitação | Primeira data de validade | Segunda data de validade | Terceira data de validade |
   | --- | --- | --- | --- | --- |
   | Alice | 1 jan | 1 de fevereiro | Mar 1 | Abr 1 |
   | Bob | 15 jan | 1 de fevereiro | Mar 1 | Abr 1 |

1. Utilize a Duração antes da **reaceitação requer (dias)** de regulação para especificar o número de dias antes de o utilizador reaceitar os termos de utilização. Isto permite que os utilizadores sigam a sua própria agenda. Por exemplo, se definir a duração de **30** dias, aqui está como as expirações podem ocorrer para dois utilizadores:

   | Utilizador | Primeira data de aceitação | Primeira data de validade | Segunda data de validade | Terceira data de validade |
   | --- | --- | --- | --- | --- |
   | Alice | 1 jan | 31 de janeiro | Mar 2 | Abr 1 |
   | Bob | 15 jan | Fev 14 | Mar 16 | Abr 15 |

   É possível utilizar os **consentimentos** e a duração do Expire antes da **reaceitação requer (dias)** definições em conjunto, mas normalmente usa-se um ou outro.

1. Sob **acesso condicional,** utilize a lista de modelos de **aplicação com acesso condicional** para selecionar o modelo para fazer cumprir os termos de utilização.

   ![Lista de abandono de acesso condicional para selecionar um modelo de política](./media/terms-of-use/conditional-access-templates.png)

   | Modelo | Descrição |
   | --- | --- |
   | **Acesso a aplicativos em nuvem para todos os hóspedes** | Será criada uma política de Acesso Condicional para todos os hóspedes e todas as aplicações na nuvem. Esta política tem impacto no portal Azure. Uma vez criado, pode ser obrigado a assinar e a inscrever-se. |
   | **Acesso a aplicativos na nuvem para todos os utilizadores** | Será criada uma política de Acesso Condicional para todos os utilizadores e todas as aplicações na nuvem. Esta política tem impacto no portal Azure. Uma vez criado, será obrigado a assinar e iniciar sessão. |
   | **Política personalizada** | Selecione os utilizadores, grupos e aplicações a que estes termos de utilização serão aplicados. |
   | **Criar política de acesso condicional mais tarde** | Estes termos de utilização aparecerão na lista de controlo de subvenções na criação de uma política de acesso condicional. |

   >[!IMPORTANT]
   >Os controlos da política de acesso condicional (incluindo os termos de utilização) não suportam a aplicação das contas de serviço. Recomendamos a exclusão de todas as contas de serviço da política de Acesso Condicional.

    As políticas de acesso condicional personalizado permitem os termos de utilização granular, até uma aplicação específica na nuvem ou um grupo de utilizadores. Para mais informações, consulte [Quickstart: Exija que os termos de utilização sejam aceites antes](require-tou.md)de aceder a aplicações na nuvem .

1. Clique em **Criar**.

   Se selecionou um modelo de Acesso Condicional personalizado, aparece um novo ecrã que lhe permite criar a política de acesso condicional personalizada.

   ![Novo painel de acesso condicional se escolher o modelo de política de acesso condicional personalizado](./media/terms-of-use/custom-policy.png)

   Agora deve ver os seus novos termos de uso.

   ![Novos termos de utilização listados nos termos da lâmina de utilização](./media/terms-of-use/create-tou.png)

## <a name="view-report-of-who-has-accepted-and-declined"></a>Ver relatório de quem aceitou e recusou

O painel Termos de utilização mostra uma contagem dos utilizadores que aceitaram e recusaram. Estas contagens e quem aceitou/recusou são armazenados para a vida útil dos termos de utilização.

1. Inicie sessão no Azure e navegue para **Termos de utilização** em [https://aka.ms/catou](https://aka.ms/catou).

   ![Termos de utilização da lâmina que listam o número de programação do utilizador aceitaram e recusaram](./media/terms-of-use/view-tou.png)

1. Para obter um tempo de utilização, clique nos números em **Aceito** ou **Recusado** para visualizar o estado atual para os utilizadores.

   ![Termos de utilização consente painel listagem dos utilizadores que aceitaram](./media/terms-of-use/accepted-tou.png)

1. Para ver o histórico de um utilizador individual, clique na elipse**e,** em seguida, **ver história**.

   ![Ver menu de contexto histórico para um utilizador](./media/terms-of-use/view-history-menu.png)

   Na visão da história, você vê uma história de todos os aceitações, declínios e expirações.

   ![Ver Painel histórico lista o histórico aceita, declina e expirações para um utilizador](./media/terms-of-use/view-history-pane.png)

## <a name="view-azure-ad-audit-logs"></a>Ver registos de auditoria da AD Azure

Se quiser ver atividade adicional, os termos de utilização da Azure AD incluem registos de auditoria. Cada consentimento do utilizador desencadeia um evento nos registos de auditoria que é armazenado durante **30 dias**. Pode ver estes registos no portal ou transferi-los como um ficheiro. csv.

Para começar com os registos de auditoria da Azure AD, utilize o seguinte procedimento:

1. Inicie sessão no Azure e navegue para **Termos de utilização** em [https://aka.ms/catou](https://aka.ms/catou).
1. Selecione um termos de utilização.
1. Clique em **Ver registos de auditoria**.

   ![Termos de lâmina de utilização com a opção ver registos de auditoria destacados](./media/terms-of-use/audit-tou.png)

1. No ecrã de registos de auditoria da AD Azure, pode filtrar as informações utilizando as listas fornecidas para direcionar informações específicas sobre registos de auditoria.

   Também pode clicar em **Transferir** para transferir as informações para um ficheiro. csv, para utilização local.

   ![A auditoria da Azure AD regista data de listagem de ecrã, política de alvo, iniciada por e atividade](./media/terms-of-use/audit-logs-tou.png)

   Se clicar num registo, aparece um painel com detalhes adicionais de atividade.

   ![Detalhes da atividade para um registo que mostra atividade, estado de atividade, iniciado por, política-alvo](./media/terms-of-use/audit-log-activity-details.png)

## <a name="what-terms-of-use-looks-like-for-users"></a>Que termos de uso parecem para os utilizadores

Uma vez criados e aplicados termos de utilização, os utilizadores, que estão no âmbito, verão o seguinte ecrã durante o iniciar o sessão.

![Exemplo de termos de utilização que aparece quando um utilizador assina](./media/terms-of-use/user-tou.png)

Os utilizadores podem ver os termos de utilização e, se necessário, usar botões para ampliar para dentro e para fora.

![Vista dos termos de utilização com botões de zoom](./media/terms-of-use/zoom-buttons.png)

O ecrã seguinte mostra como os termos de utilização parecem em dispositivos móveis.

![Exemplo de termos de utilização que aparecem quando um utilizador faz o sinal de um dispositivo móvel](./media/terms-of-use/mobile-tou.png)

Os utilizadores só são obrigados a aceitar os termos de utilização uma vez e não verão novamente os termos de utilização em subsequentes inscrições.

### <a name="how-users-can-review-their-terms-of-use"></a>Como os utilizadores podem rever os seus termos de utilização

Os utilizadores podem rever e ver os termos de utilização que aceitaram utilizando o seguinte procedimento.

1. Inicie sessão em [https://myapps.microsoft.com](https://myapps.microsoft.com).
1. No canto superior direito, clique no seu nome e selecione **Perfil**.

   ![Site myApps com o painel do utilizador aberto](./media/terms-of-use/tou14.png)

1. Na página Perfil, clique em **Rever os termos de utilização**.

   ![Página de perfil para um utilizador que mostre os termos de utilização da Revisão](./media/terms-of-use/tou13a.png)

1. A partir daí, pode rever os termos de utilização que aceitou.

## <a name="edit-terms-of-use-details"></a>Editar termos de utilização detalhes

Pode editar alguns detalhes dos termos de utilização, mas não pode modificar um documento existente. O seguinte procedimento descreve como editar os detalhes.

1. Inicie sessão no Azure e navegue para **Termos de utilização** em [https://aka.ms/catou](https://aka.ms/catou).
1. Selecione os termos de utilização que pretende editar.
1. Clique nos **termos editar**.
1. Nos termos de edição do painel de utilização, altere o nome, o nome do ecrã ou exija que os utilizadores expandam os valores.

   Se houver outras definições que gostaria de alterar, como o documento PDF, exigir que os utilizadores consintam em todos os dispositivos, caducar consentimentos, duração antes da reaceitação ou política de Acesso Condicional, deve criar novos termos de utilização.

   ![Editar termos de painel de uso mostrando nome e expandir opções](./media/terms-of-use/edit-tou.png)

1. Clique em **Guardar** para guardar as suas alterações.

   Uma vez que guarde as suas alterações, os utilizadores não terão de reaceitar estas edições.

## <a name="add-a-terms-of-use-language"></a>Adicione um idioma de uso

O seguinte procedimento descreve como adicionar um idioma de uso.

1. Inicie sessão no Azure e navegue para **Termos de utilização** em [https://aka.ms/catou](https://aka.ms/catou).
1. Selecione os termos de utilização que pretende editar.
1. No painel de detalhes, clique no separador **Idiomas.**

   ![Termos de utilização selecionados e mostrando o separador Idiomas no painel de detalhes](./media/terms-of-use/languages-tou.png)

1. Clique em **Adicionar idioma**.
1. Nos termos de adição do painel de idiomas, faça upload do PDF localizado e selecione o idioma.

   ![Adicione termos de uso painel de linguagem com opções para carregar PDFs localizados](./media/terms-of-use/language-add-tou.png)

1. Clique em **Adicionar** o idioma.

## <a name="per-device-terms-of-use"></a>Termos de utilização por dispositivo

O **Requerer que os utilizadores consintam em todas as** definições do dispositivo permite que os utilizadores finais aceitem os seus termos de utilização em todos os dispositivos a que estão a aceder. O utilizador final será obrigado a registar o seu dispositivo em Azure AD. Quando o dispositivo está registado, o ID do dispositivo é utilizado para impor os termos de utilização em cada dispositivo.

Aqui está uma lista das plataformas e software suportados.

> [!div class="mx-tableFixed"]
> |  | iOS | Android | Windows 10 | Outros |
> | --- | --- | --- | --- | --- |
> | **Aplicação nativa** | Sim | Sim | Sim |  |
> | **Microsoft Edge** | Sim | Sim | Sim |  |
> | **Internet Explorer** | Sim | Sim | Sim |  |
> | **Chrome (com extensão)** | Sim | Sim | Sim |  |

Os termos de utilização por dispositivo têm os seguintes constrangimentos:

- Um dispositivo só pode ser unido a um inquilino.
- Um utilizador deve ter permissões para se juntar ao seu dispositivo.
- A aplicação Intune Registration não é suportada.
- Os utilizadores do Azure AD B2B não são suportados.

Se o dispositivo do utilizador não estiver aderido, receberá uma mensagem de que precisa de aderir ao seu dispositivo. A sua experiência será dependente da plataforma e do software.

### <a name="join-a-windows-10-device"></a>Associar um dispositivo Windows 10

Se um utilizador estiver a utilizar o Windows 10 e o Microsoft Edge, receberá uma mensagem semelhante à seguinte para [se juntar ao seu dispositivo](../user-help/user-help-join-device-on-network.md#to-join-an-already-configured-windows-10-device).

![Windows 10 e Microsoft Edge - Mensagem indicando que o seu dispositivo deve ser registado](./media/terms-of-use/per-device-win10-edge.png)

Se estiverem a utilizar o Chrome, serão solicitados a instalar a extensão das [Contas Windows 10](https://chrome.google.com/webstore/detail/windows-10-accounts/ppnbnpeolgkicgegkbkbjmhlideopiji).

### <a name="register-an-ios-device"></a>Registar um dispositivo iOS

Se um utilizador estiver a utilizar um dispositivo iOS, será solicitado a instalar a [aplicação Microsoft Authenticator](https://apps.apple.com/us/app/microsoft-authenticator/id983156458).

### <a name="register-an-android-device"></a>Registe um dispositivo Android

Se um utilizador estiver a utilizar um dispositivo Android, será solicitado a instalar a [aplicação Microsoft Authenticator](https://play.google.com/store/apps/details?id=com.azure.authenticator).

### <a name="browsers"></a>Navegadores

Se um utilizador estiver a usar o navegador que não é suportado, será solicitado a utilizar um navegador diferente.

![Mensagem indicando que o seu dispositivo deve ser registado, mas o navegador não é suportado](./media/terms-of-use/per-device-browser-unsupported.png)

## <a name="delete-terms-of-use"></a>Eliminar termos de utilização

Pode eliminar os antigos termos de utilização utilizando o seguinte procedimento.

1. Inicie sessão no Azure e navegue para **Termos de utilização** em [https://aka.ms/catou](https://aka.ms/catou).
1. Selecione os termos de utilização que quer remover.
1. Clique em **Eliminar termos**.
1. Na mensagem que é apresentada a perguntar se pretende continuar, clique em **Sim**.

   ![Mensagem pedindo confirmação para apagar termos de uso](./media/terms-of-use/delete-tou.png)

   Não devia mais ver os seus termos de uso.

## <a name="deleted-users-and-active-terms-of-use"></a>Utilizadores eliminados e termos de utilização ativos

Por predefinição, um utilizador eliminado fica num estado eliminado no Azure AD durante um período de 30 dias, durante o qual pode ser restaurado por um administrador, se necessário. Após 30 dias, esse utilizador é eliminado permanentemente. Além disso, através do portal do Azure Active Directory, um Administrador Global pode explicitamente [eliminar permanentemente um utilizador recentemente eliminado](../fundamentals/active-directory-users-restore.md) antes desse período de tempo ser atingido. Um utilizador foi permanentemente eliminado, os dados subsequentes sobre esse utilizador serão removidos dos termos de utilização ativos. As informações de auditoria sobre os utilizadores eliminados permanecem no registo de auditoria.

## <a name="policy-changes"></a>Alterações de política

As políticas de Acesso Condicional entrarão em vigor imediatamente. Quando isso acontecer, o administrador começará a ver "nuvens tristes" ou "problemas de fichas da AD Azure". O administrador deve assinar e voltar a assinar para satisfazer a nova política.

> [!IMPORTANT]
> Os utilizadores abrangidos pelo âmbito definido terão de terminar e iniciar sessão de modo a satisfazerem uma nova política:
>
> - uma política de acesso condicional é ativada em termos de utilização
> - ou se for criado um segundo documento de termos de utilização

## <a name="b2b-guests-preview"></a>Hóspedes B2B (Pré-visualização)

A maioria das organizações tem um processo em vigor para que os seus colaboradores consintam nos termos de uso e declarações de privacidade da sua organização. Mas como pode impor os mesmos consentimentos para os hóspedes da Azure AD (B2B) quando são adicionados via SharePoint ou Teams? Utilizando acesso condicional e termos de utilização, pode aplicar uma política diretamente para os utilizadores convidados B2B. Durante o fluxo de resgate de convites, o utilizador é apresentado com os termos de utilização. Este suporte encontra-se atualmente em pré-visualização.

Os termos de utilização só serão apresentados quando o utilizador tiver uma conta de hóspedes em Azure AD. O SharePoint Online tem atualmente uma experiência de destinatário de [partilha externa ad hoc](/sharepoint/what-s-new-in-sharing-in-targeted-release) para partilhar um documento ou uma pasta que não exija que o utilizador tenha uma conta de hóspedes. Neste caso, não é apresentado um estado de utilização.

![Painel de utilizadores e grupos - Inclua o separador com todas as opções dos utilizadores convidados verificadas](./media/terms-of-use/b2b-guests.png)

## <a name="support-for-cloud-apps-preview"></a>Suporte para aplicações em nuvem (Pré-visualização)

Os termos de utilização podem ser usados para diferentes aplicações na nuvem, como a Azure Information Protection e a Microsoft Intune. Este suporte encontra-se atualmente em pré-visualização.

### <a name="azure-information-protection"></a>Azure Information Protection

Pode configurar uma política de acesso condicional para a aplicação de proteção de informação Azure e exigir um prazo de utilização quando um utilizador acede a um documento protegido. Isto irá desencadear um período de utilização antes de um utilizador aceder a um documento protegido pela primeira vez.

![Painel de aplicações cloud com app de proteção de informação do Microsoft Azure selecionada](./media/terms-of-use/cloud-app-info-protection.png)

### <a name="microsoft-intune-enrollment"></a>Inscrição intune da Microsoft

Pode configurar uma política de acesso condicional para a aplicação Microsoft Intune Registration e exigir um nível de utilização antes da inscrição de um dispositivo em Intune. Para mais informações, consulte a solução De Leitura Escolher os Termos certos para a sua publicação de blogue da [organização.](https://go.microsoft.com/fwlink/?linkid=2010506&clcid=0x409)

![Painel de aplicações cloud com app Microsoft Intune selecionada](./media/terms-of-use/cloud-app-intune.png)

> [!NOTE]
> A aplicação Intune Registration não é suportada para [termos de utilização por dispositivo](#per-device-terms-of-use).

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes

**P: Como vejo quando/se um utilizador aceitou um termo de utilização?**<br />
R: Nos termos da lâmina de utilização, clique no número em **Aceito**. Também pode visualizar ou pesquisar a atividade aceite nos registos de auditoria da AD Azure. Para mais informações, consulte o relatório view de quem aceitou e recusou e ver registos de [auditoria da AD Azure](#view-azure-ad-audit-logs).

**P: Por quanto tempo as informações são armazenadas?**<br />
R: O utilizador conta nos termos do relatório de utilização e quem aceitou/recusou são armazenados durante a vida útil dos termos de utilização. Os registos de auditoria da AD Azure são armazenados durante 30 dias.

**P: Por que vejo um número diferente de consentimentos nos termos do relatório de utilização vs. os registos de auditoria da AD Azure?**<br />
R: O relatório de termos de utilização é armazenado durante a vida útil desses termos de utilização, enquanto os registos de auditoria da AD Azure são armazenados durante 30 dias. Além disso, os termos do relatório de utilização apenas mostram o estado de consentimento dos utilizadores. Por exemplo, se um utilizador recusar e depois aceitar, o relatório de utilização apenas mostrará que o utilizador aceita. Se precisar de ver o histórico, pode utilizar os registos de auditoria da AD Azure.

**P: Se eu editar os detalhes para um momento de utilização, requer que os utilizadores aceitem novamente?**<br />
R: Não, se um administrador edita os detalhes para um termos de utilização (nome, nome do ecrã, exigir que os utilizadores expandam ou acrescentem um idioma), não exige que os utilizadores reaceitem os novos termos.

**P: Posso atualizar um documento de uso existente?**<br />
R: Atualmente, não é possível atualizar um documento de utilização existente. Para alterar um documento de uso, terá de criar um novo tipo de utilização.

**P: Se as hiperligações estiverem nos termos de utilização do documento PDF, os utilizadores finais poderão clicar neles?**<br />
R: Sim, os utilizadores finais são capazes de selecionar hiperligações para páginas adicionais, mas os links para secções dentro do documento não são suportados.

**P: Um termos de uso pode suportar várias línguas?**<br />
R: Sim. Atualmente existem 108 idiomas diferentes que um administrador pode configurar para um único termos de utilização. Um administrador pode carregar vários documentos PDF e marcar esses documentos com uma linguagem correspondente (até 108). Quando os utilizadores finais fazem o início, olhamos para a preferência do idioma do navegador e exibimos o documento correspondente. Se não houver correspondência, apresentaremos o documento predefinido, que é o primeiro documento que é carregado.

**P: Quando é que os termos de utilização são desencadeados?**<br />
R: Os termos de utilização são acionados durante a experiência de início de sessão.

**P: A que aplicações posso direcionar um termos de utilização?**<br />
R: Pode criar uma política de Acesso Condicional sobre as aplicações empresariais utilizando a autenticação moderna. Para obter mais informações, consulte [aplicações empresariais](./../manage-apps/view-applications-portal.md).

**P: Posso adicionar vários termos de utilização para um determinado utilizador ou aplicação?**<br />
R: Sim, através da criação de múltiplas políticas de Acesso Condicional dirigidas a esses grupos ou aplicações. Se um utilizador se insere no âmbito de vários termos de utilização, aceita um termos de utilização de cada vez.

**P: O que acontece se um utilizador recusar os termos de utilização?**<br />
R: O acesso do utilizador à aplicação é bloqueado. O utilizador teria de voltar a fazer o seu insessão e aceitar os termos para ter acesso.

**P: É possível não aceitar um termos de utilização que foram previamente aceites?**<br />
R: Pode [rever os termos de utilização anteriormente aceites](#how-users-can-review-their-terms-of-use), mas atualmente não há uma maneira de não aceitar.

**P: O que acontece se eu também estiver usando termos e condições Intune?**<br />
R: Se tiver configurado os termos de utilização da AD Azure e os [termos e condições insinados,](/intune/terms-and-conditions-create)o utilizador será obrigado a aceitar ambos. Para mais informações, consulte a [solução De Escolha dos Termos certos para a sua publicação](https://go.microsoft.com/fwlink/?linkid=2010506&clcid=0x409)de blog da organização.

**P: Quais os pontos finais que os termos de serviço de utilização utilizam para a autenticação?**<br />
R: Os termos de utilização utilizam os https://tokenprovider.termsofuse.identitygovernance.azure.com https://account.activedirectory.windowsazure.comseguintes pontos finais para autenticação: e . Se a sua organização tiver uma lista de URLs para inscrição, terá de adicionar estes pontos finais à sua lista de autorizações, juntamente com os pontos finais da AD Azure para iniciar sessão.

## <a name="next-steps"></a>Passos seguintes

- [Quickstart: Exigir que os termos de uso sejam aceites antes de aceder a aplicações na nuvem](require-tou.md)
- [Boas práticas para acesso condicional em Diretório Ativo Azure](best-practices.md)
