---
title: Termos de uso-Azure Active Directory | Microsoft Docs
description: Comece a usar Azure Active Directory termos de uso para apresentar informações a funcionários ou convidados antes de obter acesso.
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
ms.openlocfilehash: 31d84d5bf43bac55769a6479917794a51c1ccd0c
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/15/2019
ms.locfileid: "70999125"
---
# <a name="azure-active-directory-terms-of-use"></a>Azure Active Directory termos de uso

Os termos de uso do Azure AD fornecem um método simples que as organizações podem usar para apresentar informações aos usuários finais. Tal disponibilização garante a visualização das exclusões de responsabilidade relevantes no que se refere a requisitos legais ou de conformidade por parte dos utilizadores. Este artigo descreve como começar a usar os termos de uso.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="overview-videos"></a>Vídeos de descrição geral

O vídeo a seguir fornece uma visão geral rápida dos termos de uso.

>[!VIDEO https://www.youtube.com/embed/tj-LK0abNao]

Para ver vídeos adicionais, consulte:
- [Como implantar os termos de uso no Azure Active Directory](https://www.youtube.com/embed/N4vgqHO2tgY)
- [Como distribuir os termos de uso no Azure Active Directory](https://www.youtube.com/embed/t_hA4y9luCY)

## <a name="what-can-i-do-with-terms-of-use"></a>O que posso fazer com os termos de uso?

Os termos de uso do Azure AD têm os seguintes recursos:

- Exigir que os funcionários ou convidados aceitem seus termos de uso antes de obter acesso.
- Exigir que os funcionários ou convidados aceitem seus termos de uso em cada dispositivo antes de obter acesso.
- Exigir que os funcionários ou convidados aceitem seus termos de uso em um agendamento recorrente.
- Exigir que os funcionários ou convidados aceitem seus termos de uso antes de registrar as informações de segurança na MFA (autenticação multifator do Azure).
- Exigir que os funcionários aceitem seus termos de uso antes de registrar informações de segurança na redefinição de senha de autoatendimento (SSPR) do Azure AD.
- Apresente os termos de uso gerais para todos os usuários em sua organização.
- Apresentar termos de uso específicos com base em atributos de usuário (por exemplo, médicos por oposição a enfermeiros ou colaboradores nacionais por oposição a colaboradores internacionais, uma distinção feita através da utilização de [grupos dinâmicos](../users-groups-roles/groups-dynamic-membership.md)).
- Apresente termos de uso específicos ao acessar aplicativos de alto impacto nos negócios, como o Salesforce.
- Apresente os termos de uso em diferentes idiomas.
- Liste quem tem ou não aceito para seus termos de uso.
- Ajudar a cumprir os regulamentos de privacidade.
- Exibir um log de atividades de termos de uso para conformidade e auditoria.
- Crie e gerencie os termos de uso usando [APIs de Microsoft Graph](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/agreement) (atualmente em versão prévia).

## <a name="prerequisites"></a>Pré-requisitos

Para usar e configurar os termos de uso do Azure AD, você deve ter:

- Uma subscrição Premium P1, Premium P2, EMS E3 ou EMS E5 do Azure AD.
   - Se não tiver uma destas subscrições, pode [obter o Azure AD Premium](../fundamentals/active-directory-get-started-premium.md) ou [ativar a versão de avaliação do Azure AD Premium](https://azure.microsoft.com/trial/get-started-active-directory/).
- Uma das seguintes contas de administrador para o diretório que pretende configurar:
   - Administrador Global
   - Administrador de Segurança
   - Administrador de Acesso Condicional

## <a name="terms-of-use-document"></a>Documento Termos de utilização

Os termos de uso do Azure AD usam o formato PDF para apresentar o conteúdo. O ficheiro PDF pode ter qualquer tipo de conteúdo, como documentos contratuais existentes, permitindo-lhe recolher contratos de utilizador final durante o início de sessão do utilizador. Para suportar os utilizadores em dispositivos móveis, o tamanho de tipo de letra recomendado no PDF é o ponto de 24.

## <a name="add-terms-of-use"></a>Adicionar termos de utilização

Depois de ter finalizado seu documento de termos de uso, use o procedimento a seguir para adicioná-lo.

1. Inicie sessão no Azure Global como administrador, administrador de segurança ou administrador de acesso condicional.
1. Navegue para **Termos de utilização** em [https://aka.ms/catou](https://aka.ms/catou).

   ![Acesso condicional-folha Termos de uso](./media/terms-of-use/tou-blade.png)

1. Clique em **Novos termos**.

   ![Novo termo do painel de uso para especificar as configurações dos termos de uso](./media/terms-of-use/new-tou.png)

1. Na caixa **nome** , insira um nome para os termos de uso que serão usados no portal do Azure.
1. Na **nome a apresentar** , introduza um título que os utilizadores veem quando iniciam sessão.
1. Para **termos de uso documento**, navegue até o PDF de termos de uso finalizado e selecione-o.
1. Selecione o idioma do documento dos termos de uso. A opção de idioma permite-lhe carregar vários termos de utilização, cada um com um idioma diferente. A versão dos termos de utilização que um utilizador final verá serão baseados nas respetivas preferências do browser.
1. Para exigir que os usuários finais exibam os termos de uso antes de aceitá-los, defina **exigir que os usuários expandam os termos de uso** para **ativado**.
1. Para exigir que os usuários finais aceitem os termos de uso em cada dispositivo do qual estão acessando, defina **exigir que os usuários consigam em cada dispositivo** para **o.** Para obter mais informações, consulte [termos de uso por dispositivo](#per-device-terms-of-use).
1. Se você quiser expirar os termos de uso de consentir de acordo com uma agenda, defina **expirar consentir** como **ativado**. Quando definido como no, são apresentadas duas definições de agendamento adicionais.

   ![Expirar as configurações para definir a data de início, a frequência e a duração](./media/terms-of-use/expire-consents.png)

1. Use as configurações de **expiração de início** e de **frequência** para especificar o agendamento de expirações de termos de uso. A tabela seguinte mostra o resultado para algumas definições de exemplo:

   | Expiração inicia em | Frequência | Resultado |
   | --- | --- | --- |
   | Data de hoje  | Custo | A partir de hoje, os usuários devem aceitar os termos de uso e, em seguida, reaceitar todos os meses. |
   | Data no futuro  | Custo | A partir de hoje, os usuários devem aceitar os termos de uso. Quando ocorre a data no futuro, consentimentos irão expirar e, em seguida, os utilizadores têm voltem a todos os meses.  |

   Por exemplo, se definir expire a iniciar na data para **1 de Janeiro** e a frequência para **mensal**, eis como expirações poderão ocorrer para que dois usuários:

   | Utilizador | Em primeiro lugar aceitar data | Em primeiro lugar expirar data | Em segundo lugar expirar data | Em terceiro lugar expirar data |
   | --- | --- | --- | --- | --- |
   | Alice | 1 de Janeiro | 1 de Fevereiro | 1 de Março | 1 de Abril |
   | BOB | 15 de Janeiro | 1 de Fevereiro | 1 de Março | 1 de Abril |

1. Use a configuração **duração antes da reaceitação requer (dias)** para especificar o número de dias antes que o usuário precise aceitar os termos de uso novamente. Isso permite que os utilizadores seguirem o seu próprio agendamento. Por exemplo, se definir a duração como **30** dias, eis como expirações poderão ocorrer para que dois usuários:

   | Utilizador | Em primeiro lugar aceitar data | Em primeiro lugar expirar data | Em segundo lugar expirar data | Em terceiro lugar expirar data |
   | --- | --- | --- | --- | --- |
   | Alice | 1 de Janeiro | Dia 31 de Janeiro | 2 de mar | 1 de Abril |
   | BOB | 15 de Janeiro | 14 de Fevereiro | 16 de Março | 15 de Abril |

   É possível utilizar o **expirar consentimentos** e **duração antes de (dias) requer a aceitação do re** definições em conjunto, mas, normalmente, utilizar um ou outro.

1. Em **acesso condicional**, use a lista **impor com modelo de política de acesso condicional** para selecionar o modelo para impor os termos de uso.

   ![Lista suspensa acesso condicional para selecionar um modelo de política](./media/terms-of-use/conditional-access-templates.png)

   | Modelo | Descrição |
   | --- | --- |
   | **Acesso a aplicações na cloud para todos os convidados** | Uma política de acesso condicional será criada para todos os convidados e todos os aplicativos de nuvem. Esta política afeta o portal do Azure. Quando isso for criado, poderá ser necessário a fim de sessão e início de sessão. |
   | **Acesso a aplicações na cloud em todos os utilizadores** | Uma política de acesso condicional será criada para todos os usuários e todos os aplicativos de nuvem. Esta política afeta o portal do Azure. Quando isso for criado, será solicitado a fim de sessão e início de sessão. |
   | **Política personalizada** | Selecione os usuários, grupos e aplicativos aos quais esses termos de uso serão aplicados. |
   | **Criar política de acesso condicional mais tarde** | Esses termos de uso aparecerão na lista de controle de concessão ao criar uma política de acesso condicional. |

   >[!IMPORTANT]
   >Controles de política de acesso condicional (incluindo termos de uso) não dão suporte à imposição em contas de serviço. É recomendável excluir todas as contas de serviço da política de acesso condicional.

    As políticas de acesso condicional personalizadas permitem termos de uso granulares, até um aplicativo de nuvem ou grupo de usuários específico. Para obter mais informações, [consulte início rápido: Exigir que os termos de uso sejam aceitos antes de](require-tou.md)acessar os aplicativos de nuvem.

1. Clique em **Criar**.

   Se você selecionou um modelo de acesso condicional personalizado, uma nova tela será exibida, permitindo que você crie a política de acesso condicional personalizada.

   ![Novo painel de acesso condicional se você escolheu o modelo de política de acesso condicional personalizado](./media/terms-of-use/custom-policy.png)

   Agora você deve ver seus novos termos de uso.

   ![Novos termos de uso listados na folha termos de uso](./media/terms-of-use/create-tou.png)

## <a name="view-report-of-who-has-accepted-and-declined"></a>Ver relatório de quem tem aceitaram e recusaram

O painel Termos de utilização mostra uma contagem dos utilizadores que aceitaram e recusaram. Essas contagens e quem aceitou/recusou são armazenados durante a vida útil dos termos de uso.

1. Inicie sessão no Azure e navegue para **Termos de utilização** em [https://aka.ms/catou](https://aka.ms/catou).

   ![Termos de uso folha listando o número de usuários mostrar foram aceitos e recusados](./media/terms-of-use/view-tou.png)

1. Para obter os termos de uso, clique nos números em **aceito** ou **recusado** para exibir o estado atual dos usuários.

   ![Termos de uso painel consentis listando os usuários que aceitaram](./media/terms-of-use/accepted-tou.png)

1. Para ver o histórico para um usuário individual, clique nas reticências ( **...** ) e, em seguida **ver o histórico**.

   ![Exibir o menu de contexto do histórico para um usuário](./media/terms-of-use/view-history-menu.png)

   No painel do histórico de exibição, verá um histórico de todos os aceitar, recusas e expirações.

   ![Painel exibir histórico lista o histórico aceita, recusa e expirações de um usuário](./media/terms-of-use/view-history-pane.png)

## <a name="view-azure-ad-audit-logs"></a>Registos de auditoria de vista do Azure AD

Se você quiser exibir atividades adicionais, os termos de uso do Azure AD incluirão logs de auditoria. Cada consentimento do usuário dispara um evento nos registos de auditoria que são armazenados durante **30 dias**. Pode ver estes registos no portal ou transferi-los como um ficheiro. csv.

Para começar a utilizar com o Azure AD registos de auditoria, utilize o seguinte procedimento:

1. Inicie sessão no Azure e navegue para **Termos de utilização** em [https://aka.ms/catou](https://aka.ms/catou).
1. Selecione os termos de uso.
1. Clique em **Ver registos de auditoria**.

   ![Termos de uso folha com a opção Exibir logs de auditoria realçada](./media/terms-of-use/audit-tou.png)

1. No Azure AD ecrã de registos de auditoria, pode filtrar as informações utilizando as listas fornecidas para informações de registo de auditoria específicas de destino.

   Também pode clicar em **Transferir** para transferir as informações para um ficheiro. csv, para utilização local.

   ![Lista de logs de auditoria do Azure AD data de listagem, política de destino, iniciada por e atividade](./media/terms-of-use/audit-logs-tou.png)

   Se clicar num log, é apresentado um painel com detalhes de atividade adicionais.

   ![Detalhes da atividade para um log que mostra a atividade, o status da atividade, iniciado por, política de destino](./media/terms-of-use/audit-log-activity-details.png)

## <a name="what-terms-of-use-looks-like-for-users"></a>A aparência dos termos de uso para os usuários

Depois que os termos de uso forem criados e impostos, os usuários, que estão no escopo, verão a tela a seguir durante a entrada.

![Exemplo de termos de uso que aparece quando um usuário entra](./media/terms-of-use/user-tou.png)

Os usuários podem exibir os termos de uso e, se necessário, usar os botões para ampliar e reduzir.

![Exibição dos termos de uso com botões de zoom](./media/terms-of-use/zoom-buttons.png)

A tela a seguir mostra como os termos de uso são examinados em dispositivos móveis.

![Exemplo de termos de uso que aparece quando um usuário entra em um dispositivo móvel](./media/terms-of-use/mobile-tou.png)

Os usuários só precisam aceitar os termos de uso uma vez e não verão os termos de uso novamente em entradas subsequentes.

### <a name="how-users-can-review-their-terms-of-use"></a>Como os usuários podem revisar seus termos de uso

Os usuários podem examinar e ver os termos de uso que aceitaram usando o procedimento a seguir.

1. Inicie sessão em [https://myapps.microsoft.com](https://myapps.microsoft.com).
1. No canto superior direito, clique no seu nome e selecione **perfil**.

   ![Site myapps com o painel do usuário aberto](./media/terms-of-use/tou14.png)

1. Na página Perfil, clique em **Rever os termos de utilização**.

   ![Página de perfil para um usuário que mostra o link revisar termos de uso](./media/terms-of-use/tou13a.png)

1. A partir daí, pode rever os termos de utilização que aceitou.

## <a name="edit-terms-of-use-details"></a>Editar detalhes dos termos de utilização

Você pode editar alguns detalhes dos termos de uso, mas não pode modificar um documento existente. O procedimento seguinte descreve como editar os detalhes.

1. Inicie sessão no Azure e navegue para **Termos de utilização** em [https://aka.ms/catou](https://aka.ms/catou).
1. Selecione os termos de uso que você deseja editar.
1. Clique em **editar termos**.
1. Em termos de edição do painel de utilização, altere o nome, nome a apresentar ou exigir que os utilizadores expandam os valores.

   Se houver outras configurações que você queira alterar, como documento PDF, exigir que os usuários consigam em cada dispositivo, expirem consentidos, duração antes da reaceitação ou política de acesso condicional, você deve criar novos termos de uso.

   ![Editar o painel termos de uso mostrando as opções nome e expandir](./media/terms-of-use/edit-tou.png)

1. Clique em **guardar** para guardar as alterações.

   Depois de salvar as alterações, os usuários não precisarão aceitar essas edições novamente.

## <a name="add-a-terms-of-use-language"></a>Adicionar um idioma de termos de uso

O procedimento a seguir descreve como adicionar um idioma de termos de uso.

1. Inicie sessão no Azure e navegue para **Termos de utilização** em [https://aka.ms/catou](https://aka.ms/catou).
1. Selecione os termos de uso que você deseja editar.
1. No painel de detalhes, clique nas **idiomas** separador.

   ![Termos de uso selecionado e mostrando a guia idiomas no painel de detalhes](./media/terms-of-use/languages-tou.png)

1. Clique em **Adicionar idioma**.
1. Em termos de adicionar do painel de linguagem de uso, carregue o localizados PDF e selecione o idioma.

   ![Adicionar o painel de idiomas termos de uso com opções para carregar PDFs localizados](./media/terms-of-use/language-add-tou.png)

1. Clique em **adicionar** para adicionar o idioma.

## <a name="per-device-terms-of-use"></a>Termos de uso por dispositivo

A configuração **exigir que os usuários consentim em cada dispositivo** permite que você exija que os usuários finais aceitem os termos de uso em cada dispositivo do qual estão acessando. O utilizador final será necessário para associar os seus dispositivos no Azure AD. Quando o dispositivo é Unido, a ID do dispositivo é usada para impor os termos de uso em cada dispositivo.

Aqui está uma lista das plataformas suportadas e software.

> [!div class="mx-tableFixed"]
> |  | iOS | Android | Windows 10 | Outros |
> | --- | --- | --- | --- | --- |
> | **Aplicação nativa** | Sim | Sim | Sim |  |
> | **Microsoft Edge** | Sim | Sim | Sim |  |
> | **Internet Explorer** | Sim | Sim | Sim |  |
> | **Chrome (com extensão)** | Sim | Sim | Sim |  |

Os termos de uso por dispositivo têm as seguintes restrições:

- Só pode ser associado um dispositivo para um inquilino.
- Um utilizador tem de ter permissões para associar os seus dispositivos.
- Não há suporte para o aplicativo de registro do Intune.
- Não há suporte para usuários B2B do Azure AD.

Se o dispositivo do utilizador não estiver associado, receberá uma mensagem que precisam para associar os seus dispositivos. A experiência deles será dependente de plataforma e o software.

### <a name="join-a-windows-10-device"></a>Junte-se um dispositivo Windows 10

Se um utilizador estiver a utilizar com o Windows 10 e o Microsoft Edge, receberá uma mensagem semelhante à seguinte ao [associarem os respetivos dispositivos](../user-help/user-help-join-device-on-network.md#to-join-an-already-configured-windows-10-device).

![Windows 10 e Microsoft Edge-mensagem indicando que o dispositivo deve ser registrado](./media/terms-of-use/per-device-win10-edge.png)

Se estiver usando o Chrome, eles serão solicitados a instalar o [extensão de contas do Windows 10](https://chrome.google.com/webstore/detail/windows-10-accounts/ppnbnpeolgkicgegkbkbjmhlideopiji).

### <a name="browsers"></a>Browsers

Se um utilizador estiver a utilizar o browser que não é suportada, serão solicitados a utilizar um browser diferente.

![Mensagem indicando que o dispositivo deve ser registrado, mas não há suporte para o navegador](./media/terms-of-use/per-device-browser-unsupported.png)

## <a name="delete-terms-of-use"></a>Excluir termos de uso

Você pode excluir os termos de uso antigos usando o procedimento a seguir.

1. Inicie sessão no Azure e navegue para **Termos de utilização** em [https://aka.ms/catou](https://aka.ms/catou).
1. Selecione os termos de utilização que quer remover.
1. Clique em **Eliminar termos**.
1. Na mensagem que é apresentada a perguntar se pretende continuar, clique em **Sim**.

   ![Mensagem solicitando a confirmação para excluir os termos de uso](./media/terms-of-use/delete-tou.png)

   Você não deve mais ver seus termos de uso.

## <a name="deleted-users-and-active-terms-of-use"></a>Usuários excluídos e termos de uso ativos

Por predefinição, um utilizador eliminado fica num estado eliminado no Azure AD durante um período de 30 dias, durante o qual pode ser restaurado por um administrador, se necessário. Após 30 dias, esse utilizador é eliminado permanentemente. Além disso, através do portal do Azure Active Directory, um Administrador Global pode explicitamente [eliminar permanentemente um utilizador recentemente eliminado](../fundamentals/active-directory-users-restore.md) antes desse período de tempo ser atingido. Um usuário foi excluído permanentemente, os dados subsequentes sobre esse usuário serão removidos dos termos de uso ativos. As informações de auditoria sobre os utilizadores eliminados permanecem no registo de auditoria.

## <a name="policy-changes"></a>Alterações de política

As políticas de acesso condicional entram em vigor imediatamente. Quando isto acontecer, o administrador começa a "nuvens sad" ou "Problemas de token do Azure AD". O administrador tem de terminar sessão e inicie sessão novamente para atender a nova política.

> [!IMPORTANT]
> Os utilizadores abrangidos pelo âmbito definido terão de terminar e iniciar sessão de modo a satisfazerem uma nova política:
>
> - uma política de acesso condicional está habilitada em termos de uso
> - ou se for criado um segundo documento de termos de utilização

## <a name="b2b-guests-preview"></a>Convidados B2B (pré-visualização)

A maioria das organizações tem um processo em vigor para seus funcionários consentirem com os termos de uso e as declarações de privacidade de sua organização. Mas como pode impor os consentimentos mesmo para o Azure AD para empresas quando estes forem adicionados por meio do SharePoint ou equipas os convidados (B2B)? Usando o acesso condicional e os termos de uso, você pode impor uma política diretamente para usuários de convidados B2B. Durante o fluxo de resgate de convite, o usuário recebe os termos de uso. Este suporte está atualmente em pré-visualização.

Termos de uso só será exibida quando o usuário tiver uma conta de convidado no Azure AD. O SharePoint Online atualmente tem uma [experiência de destinatário de compartilhamento externo ad hoc](/sharepoint/what-s-new-in-sharing-in-targeted-release) para compartilhar um documento ou uma pasta que não exige que o usuário tenha uma conta de convidado. Nesse caso, os termos de uso não são exibidos.

![Painel usuários e grupos – incluir guia com todos os usuários convidados opção marcada](./media/terms-of-use/b2b-guests.png)

## <a name="support-for-cloud-apps-preview"></a>Suporte para aplicações na cloud (pré-visualização)

Termos de utilização podem ser utilizados para aplicações de cloud diferente, como o Azure Information Protection e o Microsoft Intune. Este suporte está atualmente em pré-visualização.

### <a name="azure-information-protection"></a>Azure Information Protection

Você pode configurar uma política de acesso condicional para o aplicativo de proteção de informações do Azure e exigir os termos de uso quando um usuário acessa um documento protegido. Isso irá disparar os termos de uso antes de um usuário acessar um documento protegido pela primeira vez.

![Painel de aplicativos de nuvem com o aplicativo Proteção de Informações do Microsoft Azure selecionado](./media/terms-of-use/cloud-app-info-protection.png)

### <a name="microsoft-intune-enrollment"></a>Inscrição do Microsoft Intune

Você pode configurar uma política de acesso condicional para o aplicativo de registro de Microsoft Intune e exigir os termos de uso antes do registro de um dispositivo no Intune. Para obter mais informações, consulte a leitura [escolher o direito de termos de solução para a sua postagem do blog de organização](https://go.microsoft.com/fwlink/?linkid=2010506&clcid=0x409).

![Painel de aplicativos de nuvem com o aplicativo Microsoft Intune selecionado](./media/terms-of-use/cloud-app-intune.png)

> [!NOTE]
> O aplicativo de registro do Intune não tem suporte para os [termos de uso por dispositivo](#per-device-terms-of-use).

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes

**P: Como fazer ver quando/se um usuário aceitou os termos de uso?**<br />
R: Na folha Termos de uso, clique no número em **aceito**. Também pode ver ou procurar a atividade de aceitar no Azure AD registos de auditoria. Para obter mais informações, consulte Exibir relatório de quem aceitou e recusou e [exibindo os logs de auditoria do Azure ad](#view-azure-ad-audit-logs).

**P: Por quanto tempo as informações são armazenadas?**<br />
R: O usuário conta no relatório termos de uso e quem aceitou/recusado são armazenados durante a vida útil dos termos de uso. A auditoria do Azure AD, os registos são armazenados durante 30 dias.

**P: Por que vejo um número diferente de consentirs no relatório termos de uso versus nos logs de auditoria do Azure AD?**<br />
R: O relatório de termos de uso é armazenado durante o tempo de vida dos termos de uso, enquanto os logs de auditoria do Azure AD são armazenados por 30 dias. Além disso, o relatório termos de uso exibe apenas o estado de consentimento atual dos usuários. Por exemplo, se um usuário recusar e, em seguida, aceitar, o relatório de termos de uso mostrará apenas a aceitação desse usuário. Se precisar de ver o histórico, pode utilizar o Azure AD registos de auditoria.

**P: Se eu editar os detalhes para os termos de uso, ele exigirá que os usuários aceitem novamente?**<br />
R: Não, se um administrador editar os detalhes de um termo de uso (nome, nome de exibição, exigir que os usuários expandam ou adicionem um idioma), ele não exigirá que os usuários aceitem novamente os novos termos.

**P: Posso atualizar um documento de termos de uso existente?**<br />
R: No momento, não é possível atualizar um documento de termos de uso existente. Para alterar um documento de termos de uso, você precisará criar uma nova instância de termos de uso.

**P: Se os hiperlinks estiverem no documento PDF termos de uso, os usuários finais poderão clicar neles?**<br />
R: Sim, os usuários finais são capazes de selecionar hiperlinks para páginas adicionais, mas não há suporte para links para seções dentro do documento.

**P: Os termos de uso podem dar suporte a vários idiomas?**<br />
R: Sim. Atualmente, há 108 diferentes idiomas que um administrador pode configurar para um único termo de uso. Um administrador pode carregar vários documentos em PDF e marcar esses documentos com um idioma correspondente (até 108). Quando os usuários finais entrarem, veremos sua preferência de idioma do navegador e exibimos o documento correspondente. Se não houver nenhuma correspondência, exibiremos o documento padrão, que é o primeiro documento que é carregado.

**P: Quando os termos de uso são disparados?**<br />
R: Os termos de uso são disparados durante a experiência de entrada.

**P: Para quais aplicativos posso direcionar os termos de uso?**<br />
R: Você pode criar uma política de acesso condicional nos aplicativos empresariais usando a autenticação moderna. Para obter mais informações, consulte [aplicações empresariais](./../manage-apps/view-applications-portal.md).

**P: Posso adicionar vários termos de uso a um determinado usuário ou aplicativo?**<br />
R: Sim, criando várias políticas de acesso condicional direcionadas a esses grupos ou aplicativos. Se um usuário estiver no escopo de vários termos de uso, eles aceitarão um termo de uso de cada vez.

**P: O que acontecerá se um usuário recusar os termos de uso?**<br />
R: O usuário está impedido de obter acesso ao aplicativo. O utilizador terá de iniciar sessão novamente e aceitar os termos para conseguir obter acesso.

**P: É possível não aceitar os termos de uso que foram aceitos anteriormente?**<br />
R: Você pode [revisar os termos de uso aceitos anteriormente](#how-users-can-review-their-terms-of-use), mas atualmente não há uma maneira de não aceitar.

**P: O que acontece se eu também estiver usando os termos e condições do Intune?**<br />
R: Se você tiver configurado os termos de uso do Azure AD e os [termos e condições do Intune](/intune/terms-and-conditions-create), o usuário será solicitado a aceitar os dois. Para obter mais informações, consulte a [escolher o direito de termos de solução para a sua postagem do blog de organização](https://go.microsoft.com/fwlink/?linkid=2010506&clcid=0x409).

**P: Quais pontos de extremidade os termos de uso do serviço usam para autenticação?**<br />
R: Termos de uso utiliza os seguintes pontos de extremidade para autenticação: https://tokenprovider.termsofuse.identitygovernance.azure.com e. https://account.activedirectory.windowsazure.com Se sua organização tiver uma lista de permissões de URLs para registro, você precisará adicionar esses pontos de extremidade à sua lista de permissões, juntamente com os pontos de extremidade do Azure AD para entrar.

## <a name="next-steps"></a>Passos seguintes

- [Quickstart: Exigir que os termos de uso sejam aceitos antes de acessar os aplicativos de nuvem](require-tou.md)
- [Práticas recomendadas para acesso condicional no Azure Active Directory](best-practices.md)
