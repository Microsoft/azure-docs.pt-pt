---
title: Termos de utilização - Azure Ative Directory / Microsoft Docs
description: Começa a usar os termos de uso do Azure Ative Directory para apresentar informações aos colaboradores ou hóspedes antes de ter acesso.
services: active-directory
ms.service: active-directory
ms.subservice: compliance
ms.topic: how-to
ms.date: 05/29/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jocastel
ms.collection: M365-identity-device-management
ms.openlocfilehash: 39e76468238a911c7a9e5543bf5063d1c7b6a8b8
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85253329"
---
# <a name="azure-active-directory-terms-of-use"></a>Termos de utilização do Diretório Ativo Azure

Os termos de utilização da AD AZure fornecem um método simples que as organizações podem usar para apresentar informações aos utilizadores finais. Tal disponibilização garante a visualização das exclusões de responsabilidade relevantes no que se refere a requisitos legais ou de conformidade por parte dos utilizadores. Este artigo descreve como começar com termos de uso.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="overview-videos"></a>Vídeos de visão geral

O vídeo a seguir fornece uma visão geral rápida dos termos de utilização.

>[!VIDEO https://www.youtube.com/embed/tj-LK0abNao]

Para mais vídeos, consulte:
- [Como implementar termos de utilização no Azure Ative Directory](https://www.youtube.com/embed/N4vgqHO2tgY)
- [Como lançar termos de utilização no Azure Ative Directory](https://www.youtube.com/embed/t_hA4y9luCY)

## <a name="what-can-i-do-with-terms-of-use"></a>O que posso fazer com os termos de uso?

Os termos de utilização da AD AZure têm as seguintes capacidades:

- Exija que os funcionários ou hóspedes aceitem os seus termos de uso antes de ter acesso.
- Exija que os funcionários ou hóspedes aceitem os seus termos de uso em todos os dispositivos antes de ter acesso.
- Exija que os colaboradores ou hóspedes aceitem os seus termos de uso num horário recorrente.
- Exija que os colaboradores ou hóspedes aceitem os seus termos de utilização antes de registar informações de segurança na Autenticação Multi-Factor (MFA) do Azure.
- Exija que os colaboradores aceitem os seus termos de utilização antes de registarem informações de segurança no reset da palavra-passe de autosserviço Azure AD (SSPR).
- Atuais termos gerais de utilização para todos os utilizadores da sua organização.
- Apresentar termos de utilização específicos com base em atributos do utilizador (ex. médicos por oposição a enfermeiros ou colaboradores nacionais por oposição a colaboradores internacionais, uma distinção feita através da utilização de [grupos dinâmicos](../users-groups-roles/groups-dynamic-membership.md)).
- Apresentar termos específicos de utilização ao aceder a aplicações de impacto de alto negócio, como a Salesforce.
- Termos de utilização atuais em diferentes línguas.
- Indique quem aceitou ou não os seus termos de uso.
- Ajude a cumprir os regulamentos de privacidade.
- Apresentar um registo de termos de atividade de uso para conformidade e auditoria.
- Crie e gere os termos de utilização [utilizando as APIs do Gráfico microsoft](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/agreement) (atualmente em pré-visualização).

## <a name="prerequisites"></a>Pré-requisitos

Para utilizar e configurar os termos Ad Azure, deve ter:

- Uma subscrição Premium P1, Premium P2, EMS E3 ou EMS E5 do Azure AD.
   - Se não tiver uma destas subscrições, pode [obter o Azure AD Premium](../fundamentals/active-directory-get-started-premium.md) ou [ativar a versão de avaliação do Azure AD Premium](https://azure.microsoft.com/trial/get-started-active-directory/).
- Uma das seguintes contas de administrador para o diretório que pretende configurar:
   - Administrador Global
   - Administrador de Segurança
   - Administrador de acesso condicional

## <a name="terms-of-use-document"></a>Documento Termos de utilização

Os termos de utilização da AD AZure utilizam o formato PDF para apresentar conteúdo. O ficheiro PDF pode ter qualquer tipo de conteúdo, como documentos contratuais existentes, permitindo-lhe recolher contratos de utilizador final durante o início de sessão do utilizador. Para suportar os utilizadores em dispositivos móveis, o tamanho recomendado da fonte no PDF é de 24 pontos.

## <a name="add-terms-of-use"></a>Adicionar termos de utilização

Uma vez finalizado o documento de utilização, utilize o seguinte procedimento para o adicionar.

1. Inscreva-se no Azure como Administrador Global, Administrador de Segurança ou Administrador de Acesso Condicional.
1. Navegue para **Termos de utilização** em [https://aka.ms/catou](https://aka.ms/catou).

   ![Acesso Condicional - Termos de utilização da lâmina](./media/terms-of-use/tou-blade.png)

1. Clique em **Novos termos**.

   ![Novo painel de uso para especificar os seus termos de definições de utilização](./media/terms-of-use/new-tou.png)

1. Na caixa **Nome,** insira um nome para os termos de utilização que serão utilizados no portal Azure.
1. Na caixa de **nomes Do Visor,** introduza um título que os utilizadores vêem quando entram.
1. Para **termos de utilização,** navegue nos termos finais de utilização do PDF e selecione-o.
1. Selecione o idioma para os seus termos de utilização. A opção de idioma permite-lhe carregar vários termos de utilização, cada um com um idioma diferente. A versão dos termos de utilização que um utilizador final verá serão baseados nas respetivas preferências do browser.
1. Para exigir que os utilizadores finais vejam os termos de utilização antes de os aceitarem, desemboe **os utilizadores que expandam os termos de utilização** para **On**.
1. Para exigir que os utilizadores finais aceitem os seus termos de utilização em todos os dispositivos a partir dos quais estão a aceder, desemboar **os utilizadores para consentir em todos os dispositivos** para **o On**. Os utilizadores poderão ser obrigados a instalar aplicações adicionais se esta opção estiver ativada. Para obter mais informações, consulte [os termos de utilização por dispositivo.](#per-device-terms-of-use)
1. Se pretender expirar os termos de consentimento de utilização num horário, desa esta **hora de definir os consentimentos** de **Expiração**para On . Quando programado para On, são apresentadas duas definições de agenda adicionais.

   ![Expire conseqüentes definições para definir data de início, frequência e duração](./media/terms-of-use/expire-consents.png)

1. Utilize as definições **de início de expiração** e **frequência** para especificar o calendário para os termos de utilização expirações. A tabela a seguir mostra o resultado de algumas definições de exemplo:

   | Expire a partir de | Frequência | Resultado |
   | --- | --- | --- |
   | Data de hoje  | Mensalmente | A partir de hoje, os utilizadores devem aceitar os termos de uso e depois readceptirem todos os meses. |
   | Data no futuro  | Mensalmente | A partir de hoje, os utilizadores devem aceitar os termos de utilização. Quando a data futura ocorrer, os consentimentos expirarão e, em seguida, os utilizadores devem reacetitar todos os meses.  |

   Por exemplo, se definir o prazo de início de validade para **1 de janeiro** e a frequência para **Mensal,** eis como podem ocorrer expirações para dois utilizadores:

   | Utilizador | Primeira data de aceitação | Primeira data de expiração | Segunda data de expiração | Terceira data de expiração |
   | --- | --- | --- | --- | --- |
   | Alice | 1 jan | Fev 1 | Mar 1 | Abr 1 |
   | Bob | 15 jan | Fev 1 | Mar 1 | Abr 1 |

1. Utilize a **Duração antes da reaceuda requer (dias)** a definição para especificar o número de dias antes de o utilizador voltar a aceitar os termos de utilização. Isto permite que os utilizadores sigam o seu próprio horário. Por exemplo, se definir a duração para **30** dias, eis como podem ocorrer expirações para dois utilizadores:

   | Utilizador | Primeira data de aceitação | Primeira data de expiração | Segunda data de expiração | Terceira data de expiração |
   | --- | --- | --- | --- | --- |
   | Alice | 1 jan | 31 de janeiro | Mar 2 | Abr 1 |
   | Bob | 15 jan | Fev 14 | Mar 16 | Abr 15 |

   É possível utilizar os **consentimentos** e duração de **Expiração antes de a rea aceitação requer (dias)** configurações em conjunto, mas normalmente usa uma ou outra.

1. Em **Acesso Condicional,** use a lista de **modelos de política de acesso condicional para** selecionar o modelo para impor os termos de utilização.

   ![Lista de drop-down de acesso condicional para selecionar um modelo de política](./media/terms-of-use/conditional-access-templates.png)

   | Modelo | Descrição |
   | --- | --- |
   | **Acesso a aplicativos em nuvem para todos os hóspedes** | Será criada uma política de Acesso Condicional para todos os hóspedes e aplicações na nuvem. Esta política tem impacto no portal Azure. Uma vez criado, poderá ser obrigado a iniciar seduca e a iniciar seduca. |
   | **Acesso a aplicativos na nuvem para todos os utilizadores** | Será criada uma política de Acesso Condicional para todos os utilizadores e todas as aplicações na nuvem. Esta política tem impacto no portal Azure. Uma vez criado, terá de se inscrever e iniciar seduca. |
   | **Política personalizada** | Selecione os utilizadores, grupos e aplicações a que estes termos de utilização serão aplicados. |
   | **Criar política de acesso condicional mais tarde** | Estes termos de utilização aparecerão na lista de controlo de subvenções ao criar uma política de acesso condicional. |

   >[!IMPORTANT]
   >Os controlos da política de acesso condicional (incluindo os termos de utilização) não suportam a aplicação das contas de serviço. Recomendamos a exclusão de todas as contas de serviço da política de Acesso Condicional.

    As políticas de acesso condicional personalizado permitem termos de utilização granular, até uma aplicação em nuvem específica ou grupo de utilizadores. Para obter mais informações, consulte [Quickstart: Exija que os termos de utilização sejam aceites antes de aceder às aplicações na nuvem.](require-tou.md)

1. Clique em **Criar**.

   Se selecionou um modelo de acesso condicional personalizado, então aparece um novo ecrã que lhe permite criar a política de Acesso Condicional personalizada.

   ![Novo painel de acesso condicional se você escolher o modelo de política de acesso condicional personalizado](./media/terms-of-use/custom-policy.png)

   Deve agora ver os seus novos termos de uso.

   ![Novos termos de utilização listados nos termos da lâmina de utilização](./media/terms-of-use/create-tou.png)

## <a name="view-report-of-who-has-accepted-and-declined"></a>Ver relatório de quem aceitou e recusou

O painel Termos de utilização mostra uma contagem dos utilizadores que aceitaram e recusaram. Estas contagens e quem aceitou/recusou são armazenadas para a vida útil dos termos de utilização.

1. Inicie sessão no Azure e navegue para **Termos de utilização** em [https://aka.ms/catou](https://aka.ms/catou).

   ![Termos de utilização que listam o número de mostra de utilizador aceitaram e recusaram](./media/terms-of-use/view-tou.png)

1. Para um termo de utilização, clique nos números em **Accepted** ou **Recusou** ver o estado atual para os utilizadores.

   ![Termos de uso consente painel listagem dos utilizadores que aceitaram](./media/terms-of-use/accepted-tou.png)

1. Para ver o histórico de um utilizador individual, clique na elipse **(...)** e, em seguida, **ver História**.

   ![Ver menu de contexto de história para um utilizador](./media/terms-of-use/view-history-menu.png)

   Na visão da história, vê-se uma história de todos os aceitações, declínios e expirações.

   ![Ver Histórico lista lista o histórico aceita, declina e expirações para um utilizador](./media/terms-of-use/view-history-pane.png)

## <a name="view-azure-ad-audit-logs"></a>Ver registos de auditoria da AZure AD

Se quiser visualizar atividade adicional, os termos de utilização do Azure AD incluem registos de auditoria. Cada consentimento do utilizador desencadeia um evento nos registos de auditoria que é armazenado durante **30 dias**. Pode ver estes registos no portal ou transferi-los como um ficheiro. csv.

Para começar com os registos de auditoria da AZure AD, utilize o seguinte procedimento:

1. Inicie sessão no Azure e navegue para **Termos de utilização** em [https://aka.ms/catou](https://aka.ms/catou).
1. Selecione um termos de utilização.
1. Clique em **Ver registos de auditoria**.

   ![Termos de utilização com a opção de registos de auditoria Ver realçada](./media/terms-of-use/audit-tou.png)

1. No ecrã de registos de auditoria Azure AD, pode filtrar as informações utilizando as listas fornecidas para direcionar informações específicas de registo de auditoria.

   Também pode clicar em **Transferir** para transferir as informações para um ficheiro. csv, para utilização local.

   ![Azure AD audita data de listagem de ecrã, política de destino, iniciada por, e atividade](./media/terms-of-use/audit-logs-tou.png)

   Se clicar num registo, aparece um painel com detalhes adicionais da atividade.

   ![Detalhes da atividade para um registo que mostra atividade, estado de atividade, iniciado por, política de destino](./media/terms-of-use/audit-log-activity-details.png)

## <a name="what-terms-of-use-looks-like-for-users"></a>Que termos de uso se parecem para os utilizadores

Uma vez criados e aplicados termos de utilização, os utilizadores, que estão no âmbito, verão o seguinte ecrã durante a sposição.

![Exemplo de uso que aparece quando um utilizador assina em](./media/terms-of-use/user-tou.png)

Os utilizadores podem ver os termos de utilização e, se necessário, usar botões para fazer zoom para dentro e para fora.

![Vista dos termos de utilização com botões de zoom](./media/terms-of-use/zoom-buttons.png)

O ecrã que se segue mostra como os termos de utilização são nos dispositivos móveis.

![Exemplo de uso que aparece quando um utilizador assina num dispositivo móvel](./media/terms-of-use/mobile-tou.png)

Os utilizadores só são obrigados a aceitar os termos de utilização uma vez e não verão novamente os termos de utilização nas entradas subsequentes.

### <a name="how-users-can-review-their-terms-of-use"></a>Como os utilizadores podem rever os seus termos de utilização

Os utilizadores podem rever e ver os termos de utilização que aceitaram utilizando o seguinte procedimento.

1. Inicie sessão em [https://myapps.microsoft.com](https://myapps.microsoft.com).
1. No canto superior direito, clique no seu nome e selecione **Perfil**.

   ![MyApps site com o painel do utilizador aberto](./media/terms-of-use/tou14.png)

1. Na página Perfil, clique em **Rever os termos de utilização**.

   ![Página de perfil para um utilizador que mostra os termos de utilização do link](./media/terms-of-use/tou13a.png)

1. A partir daí, pode rever os termos de utilização que aceitou.

## <a name="edit-terms-of-use-details"></a>Editar termos de utilização

Pode editar alguns detalhes dos termos de uso, mas não pode modificar um documento existente. O procedimento a seguir descreve como editar os detalhes.

1. Inicie sessão no Azure e navegue para **Termos de utilização** em [https://aka.ms/catou](https://aka.ms/catou).
1. Selecione os termos de utilização que pretende editar.
1. Clique **em Editar termos**.
1. Nos termos de utilização do painel de utilização, altere o nome, o nome de exibição ou exija que os utilizadores expandam os valores.

   Se houver outras configurações que gostaria de alterar, como o documento PDF, exigir que os utilizadores consintam em cada dispositivo, expirem consentimentos, duração antes de reacetar ou política de Acesso Condicional, deve criar um novo termos de utilização.

   ![Editar termos de uso painel mostrando nome e expandir opções](./media/terms-of-use/edit-tou.png)

1. Clique em **Guardar** para guardar as alterações.

   Uma vez que guarde as suas alterações, os utilizadores não terão de reacetar estas edições.

## <a name="add-a-terms-of-use-language"></a>Adicionar um termos de linguagem de uso

O procedimento a seguir descreve como adicionar um idioma de utilização.

1. Inicie sessão no Azure e navegue para **Termos de utilização** em [https://aka.ms/catou](https://aka.ms/catou).
1. Selecione os termos de utilização que pretende editar.
1. No painel de detalhes, clique no separador **Idiomas.**

   ![Termos de utilização selecionados e mostrando o separador idiomas no painel de detalhes](./media/terms-of-use/languages-tou.png)

1. Clique **em Adicionar idioma**.
1. Nos termos de utilização do painel de linguagem de utilização, faça o upload do PDF localizado e selecione o idioma.

   ![Adicione termos de uso do painel de linguagem com opções para carregar PDFs localizados](./media/terms-of-use/language-add-tou.png)

1. Clique **em Adicionar** para adicionar o idioma.

## <a name="per-device-terms-of-use"></a>Termos de utilização por dispositivo

O **Requerer que os utilizadores consintam em todas as** definições do dispositivo, permite que os utilizadores finais aceitem os seus termos de utilização em todos os dispositivos a partir dos quais estão a aceder. O utilizador final será obrigado a registar o seu dispositivo em Azure AD. Quando o dispositivo está registado, o ID do dispositivo é utilizado para impor os termos de utilização em cada dispositivo.

Aqui está uma lista das plataformas e software suportados.

> [!div class="mx-tableFixed"]
> |  | iOS | Android | Windows 10 | Outros |
> | --- | --- | --- | --- | --- |
> | **Aplicação nativa** | Sim | Sim | Sim |  |
> | **Microsoft Edge** | Sim | Sim | Sim |  |
> | **Internet Explorer** | Sim | Sim | Sim |  |
> | **Cromado (com extensão)** | Sim | Sim | Sim |  |

Os termos de utilização por dispositivo têm os seguintes constrangimentos:

- Um dispositivo só pode ser associado a um inquilino.
- Um utilizador deve ter permissões para se juntar ao seu dispositivo.
- A aplicação Intune Registration não é suportada.
- Os utilizadores Azure AD B2B não são suportados.

Se o dispositivo do utilizador não estiver associado, receberão uma mensagem de que precisam de se juntar ao seu dispositivo. A sua experiência será dependente da plataforma e do software.

### <a name="join-a-windows-10-device"></a>Associar um dispositivo Windows 10

Se um utilizador estiver a utilizar o Windows 10 e o Microsoft Edge, receberá uma mensagem semelhante à seguinte para [se juntar ao seu dispositivo](../user-help/user-help-join-device-on-network.md#to-join-an-already-configured-windows-10-device).

![Windows 10 e Microsoft Edge - Mensagem que indique que o seu dispositivo tem de ser registado](./media/terms-of-use/per-device-win10-edge.png)

Se estiverem a utilizar o Chrome, serão solicitados a instalar a extensão das [Contas Windows 10.](https://chrome.google.com/webstore/detail/windows-10-accounts/ppnbnpeolgkicgegkbkbjmhlideopiji)

### <a name="register-an-ios-device"></a>Registar um dispositivo iOS

Se um utilizador estiver a utilizar um dispositivo iOS, será solicitado que instale a [aplicação Microsoft Authenticator](https://apps.apple.com/us/app/microsoft-authenticator/id983156458).

### <a name="register-an-android-device"></a>Registar um dispositivo Android

Se um utilizador estiver a utilizar um dispositivo Android, será solicitado que instale a [aplicação Microsoft Authenticator](https://play.google.com/store/apps/details?id=com.azure.authenticator).

### <a name="browsers"></a>Navegadores

Se um utilizador estiver a utilizar um navegador que não seja suportado, será solicitado que utilize um navegador diferente.

![A mensagem indicando que o seu dispositivo deve estar registado, mas o navegador não é suportado](./media/terms-of-use/per-device-browser-unsupported.png)

## <a name="delete-terms-of-use"></a>Eliminar termos de utilização

Pode eliminar termos de utilização antigos utilizando o seguinte procedimento.

1. Inicie sessão no Azure e navegue para **Termos de utilização** em [https://aka.ms/catou](https://aka.ms/catou).
1. Selecione os termos de utilização que quer remover.
1. Clique em **Eliminar termos**.
1. Na mensagem que é apresentada a perguntar se pretende continuar, clique em **Sim**.

   ![Mensagem a pedir confirmação para apagar termos de utilização](./media/terms-of-use/delete-tou.png)

   Não deve mais ver os seus termos de uso.

## <a name="deleted-users-and-active-terms-of-use"></a>Utilizadores eliminados e termos de utilização ativos

Por predefinição, um utilizador eliminado fica num estado eliminado no Azure AD durante um período de 30 dias, durante o qual pode ser restaurado por um administrador, se necessário. Após 30 dias, esse utilizador é eliminado permanentemente. Além disso, através do portal do Azure Active Directory, um Administrador Global pode explicitamente [eliminar permanentemente um utilizador recentemente eliminado](../fundamentals/active-directory-users-restore.md) antes desse período de tempo ser atingido. Um utilizador foi permanentemente eliminado, os dados subsequentes sobre esse utilizador serão removidos dos termos de utilização ativos. As informações de auditoria sobre os utilizadores eliminados permanecem no registo de auditoria.

## <a name="policy-changes"></a>Alterações de política

As políticas de acesso condicional têm efeito imediato. Quando isso acontecer, o administrador começará a ver "nuvens tristes" ou "problemas simbólicos da AD AZure". O administrador deve assinar e assinar novamente para satisfazer a nova política.

> [!IMPORTANT]
> Os utilizadores abrangidos pelo âmbito definido terão de terminar e iniciar sessão de modo a satisfazerem uma nova política:
>
> - uma política de acesso condicional é ativada em termos de utilização
> - ou se for criado um segundo documento de termos de utilização

## <a name="b2b-guests-preview"></a>Hóspedes B2B (Pré-visualização)

A maioria das organizações tem um processo em curso para que os seus colaboradores consintam nos termos de uso e declarações de privacidade da sua organização. Mas como pode impor os mesmos consentimentos para os hóspedes do Azure AD (B2B) quando são adicionados via SharePoint ou Teams? Utilizando o Acesso Condicional e os termos de utilização, pode impor uma política diretamente para os utilizadores convidados B2B. Durante o fluxo de resgate de convites, o utilizador é apresentado com os termos de utilização. Este suporte está atualmente em pré-visualização.

Os termos de utilização só serão apresentados quando o utilizador tiver uma conta de cliente em Azure AD. O SharePoint Online tem atualmente [uma experiência de destinatário de partilha externa ad hoc](/sharepoint/what-s-new-in-sharing-in-targeted-release) para partilhar um documento ou uma pasta que não requer que o utilizador tenha uma conta de hóspedes. Neste caso, não é apresentado um termos de utilização.

![Painel de utilizadores e grupos - Inclua separador com todas as opções de utilizadores convidados verificada](./media/terms-of-use/b2b-guests.png)

## <a name="support-for-cloud-apps-preview"></a>Suporte para aplicações na nuvem (Pré-visualização)

Os termos de utilização podem ser utilizados para diferentes aplicações em nuvem, como a Azure Information Protection e a Microsoft Intune. Este suporte está atualmente em pré-visualização.

### <a name="azure-information-protection"></a>Azure Information Protection

Pode configurar uma política de acesso condicional para a aplicação Azure Information Protection e exigir um termos de utilização quando um utilizador acede a um documento protegido. Isto irá desencadear um termos de utilização antes de um utilizador aceder a um documento protegido pela primeira vez.

![Aplicativos cloud painel com app microsoft Azure Information Protection selecionado](./media/terms-of-use/cloud-app-info-protection.png)

### <a name="microsoft-intune-enrollment"></a>Inscrição microsoft Intune

Pode configurar uma política de acesso condicional para a aplicação De inscrição do Microsoft Intune e exigir um prazo de utilização antes da inscrição de um dispositivo no Intune. Para mais informações, consulte a [solução 'Escolha de Leitura' para o post de blog da organização.](https://go.microsoft.com/fwlink/?linkid=2010506&clcid=0x409)

![Aplicativos cloud painel com app Microsoft Intune selecionado](./media/terms-of-use/cloud-app-intune.png)

> [!NOTE]
> A aplicação Intune Registration não é suportada para [termos de utilização por dispositivo.](#per-device-terms-of-use)

## <a name="frequently-asked-questions"></a>Perguntas frequentes

**P: Como vejo quando/se um utilizador aceitou um termos de utilização?**<br />
R: Nos Termos de Utilização, clique no número em **Aceito**. Também pode visualizar ou pesquisar a atividade de aceitação nos registos de auditoria Azure AD. Para obter mais informações, consulte o relatório de quem aceitou e recusou e [ver os registos de auditoria da AZure AD](#view-azure-ad-audit-logs).

**P: Por quanto tempo as informações são armazenadas?**<br />
R: O utilizador conta nos termos do relatório de utilização e quem aceitou/recusou são armazenados para a vida útil dos termos de utilização. Os registos de auditoria da AZure AD são armazenados durante 30 dias.

**P: Por que vejo um número diferente de consentimentos nos termos do relatório de utilização vs. os registos de auditoria da AZure AD?**<br />
R: Os termos de utilização são armazenados durante o tempo de utilização desses termos, enquanto os registos de auditoria AZURE AD são armazenados durante 30 dias. Além disso, os termos do relatório de utilização apenas exibem o estado de consentimento atual dos utilizadores. Por exemplo, se um utilizador recusar e aceitar, os termos do relatório de utilização apenas mostrarão que o utilizador aceita. Se precisar de ver o histórico, pode utilizar os registos de auditoria AZure AD.

**P: Se eu editar os detalhes para um termos de uso, ele requer que os utilizadores aceitem novamente?**<br />
R: Não, se um administrador editar os detalhes para um termos de utilização (nome, nome de exibição, exigir que os utilizadores expandam ou adicionem um idioma), não exige que os utilizadores reacebam os novos termos.

**P: Posso atualizar um documento de utilização existente?**<br />
R: Atualmente, não é possível atualizar um documento de utilização existente. Para alterar um documento de uso, terá de criar um novo exemplo de termos de utilização.

**P: Se as hiperligações estiverem nos termos de utilização do documento PDF, os utilizadores finais poderão clicar neles?**<br />
R: Sim, os utilizadores finais são capazes de selecionar hiperligações para páginas adicionais, mas as ligações para secções dentro do documento não são suportadas.

**P: Os termos de uso podem suportar várias línguas?**<br />
R: Sim. Atualmente existem 108 idiomas diferentes que um administrador pode configurar para um único termos de uso. Um administrador pode carregar vários documentos PDF e marcar esses documentos com um idioma correspondente (até 108). Quando os utilizadores finais se inscrevem, olhamos para a sua preferência de idioma do navegador e exibimos o documento correspondente. Se não houver correspondência, exibiremos o documento predefinido, que é o primeiro documento que é carregado.

**P: Quando é acionado o tipo de utilização?**<br />
R: Os termos de utilização são acionados durante a experiência de início de sessão.

**P: Que aplicações posso direcionar um termos de uso?**<br />
R: Pode criar uma política de Acesso Condicional nas aplicações da empresa utilizando a autenticação moderna. Para obter mais informações, consulte [aplicações empresariais](./../manage-apps/view-applications-portal.md).

**P: Posso adicionar vários termos de utilização para um determinado utilizador ou aplicação?**<br />
R: Sim, criando várias políticas de Acesso Condicional direcionadas a esses grupos ou aplicações. Se um utilizador cair no âmbito de vários termos de utilização, aceita um termos de utilização de cada vez.

**P: O que acontece se um utilizador recusar os termos de utilização?**<br />
R: O acesso do utilizador à aplicação é bloqueado. O utilizador teria de voltar a fazer o seu sedu.

**P: É possível desacepem um termos de uso que foi previamente aceite?**<br />
R: Pode [rever os termos de uso previamente aceites,](#how-users-can-review-their-terms-of-use)mas atualmente não há uma forma de não aceitar.

**P: O que acontece se eu também estiver usando termos e condições intune?**<br />
R: Se tiver configurado os termos de utilização do AZure AD e os [termos e condições intune,](/intune/terms-and-conditions-create)o utilizador será obrigado a aceitar ambos. Para mais informações, consulte a [solução Escolha dos Termos certos para o post de blog da sua organização.](https://go.microsoft.com/fwlink/?linkid=2010506&clcid=0x409)

**P: Que pontos finais utiliza os termos do serviço de utilização para autenticação?**<br />
R: Os termos de utilização utilizam os seguintes pontos finais para a autenticação: https://tokenprovider.termsofuse.identitygovernance.azure.com e https://account.activedirectory.windowsazure.com . Se a sua organização tiver uma lista de sinais de admissão de URLs para inscrição, terá de adicionar estes pontos finais à sua lista de indicações, juntamente com os pontos finais AZURE AD para iniciar sação.

## <a name="next-steps"></a>Próximos passos

- [Quickstart: Exigir que os termos de uso sejam aceites antes de aceder a aplicações na nuvem](require-tou.md)
- [Melhores práticas para acesso condicional no Diretório Ativo Azure](best-practices.md)
