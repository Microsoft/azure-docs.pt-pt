---
title: Termos de utilização - Azure Ative Directory | Microsoft Docs
description: Começa a usar os termos de uso do Azure Ative Directory para apresentar informações aos colaboradores ou hóspedes antes de ter acesso.
services: active-directory
ms.service: active-directory
ms.subservice: compliance
ms.topic: how-to
ms.date: 01/27/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jocastel
ms.collection: M365-identity-device-management
ms.openlocfilehash: e4c8e18979ff1575e1a050244a96e7858cdce46b
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107530239"
---
# <a name="azure-active-directory-terms-of-use"></a>Termos de utilização do Diretório Ativo Azure

Os termos de utilização da AD AZure fornecem um método simples que as organizações podem usar para apresentar informações aos utilizadores finais. Tal disponibilização garante a visualização das exclusões de responsabilidade relevantes no que se refere a requisitos legais ou de conformidade por parte dos utilizadores. Este artigo descreve como começar com termos de utilização (ToU) políticas.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="overview-videos"></a>Vídeos de visão geral

O vídeo a seguir fornece uma visão geral rápida das políticas da ToU.

>[!VIDEO https://www.youtube.com/embed/tj-LK0abNao]

Para mais vídeos, consulte:
- [Como implementar uma política de uso em Azure Ative Directory](https://www.youtube.com/embed/N4vgqHO2tgY)
- [Como lançar uma política de uso em Azure Ative Directory](https://www.youtube.com/embed/t_hA4y9luCY)

## <a name="what-can-i-do-with-terms-of-use"></a>O que posso fazer com os termos de uso?

As políticas de utilização da AZure AD têm as seguintes capacidades:

- Exija que os colaboradores ou hóspedes aceitem a sua política de uso antes de ter acesso.
- Exija que os colaboradores ou hóspedes aceitem a sua política de uso em todos os dispositivos antes de ter acesso.
- Exija que os colaboradores ou hóspedes aceitem a sua política de uso num horário recorrente.
- Exija que os colaboradores ou hóspedes aceitem a sua política de uso antes de registar informações de segurança na Autenticação Multi-Factor AD (MFA) da Azure AD.
- Exija que os colaboradores aceitem a sua política de termos de uso antes de registar informações de segurança no reset da palavra-passe de autosserviço Azure AD (SSPR).
- Apresente uma política geral de utilização para todos os utilizadores da sua organização.
- Apresentar termos específicos de políticas de utilização com base em atributos do utilizador (ex. médicos por oposição a enfermeiros ou colaboradores nacionais por oposição a colaboradores internacionais, uma distinção feita através da utilização de [grupos dinâmicos](../enterprise-users/groups-dynamic-membership.md)).
- Apresentar termos específicos de políticas de uso ao aceder a aplicações de impacto de alto negócio, como a Salesforce.
- Termos atuais de políticas de utilização em diferentes línguas.
- Indique quem aceitou ou não os seus termos de políticas de uso.
- Ajude a cumprir os regulamentos de privacidade.
- Apresentar um registo de termos de atividade política de uso para conformidade e auditoria.
- Crie e gere termos de políticas de utilização [utilizando APIs do Microsoft Graph](/graph/api/resources/agreement) (atualmente em pré-visualização).

## <a name="prerequisites"></a>Pré-requisitos

Para utilizar e configurar os termos Azure AD de utilização, você deve ter:

- Uma subscrição Premium P1, Premium P2, EMS E3 ou EMS E5 do Azure AD.
   - Se não tiver uma destas subscrições, pode [obter o Azure AD Premium](../fundamentals/active-directory-get-started-premium.md) ou [ativar a versão de avaliação do Azure AD Premium](https://azure.microsoft.com/trial/get-started-active-directory/).
- Uma das seguintes contas de administrador para o diretório que pretende configurar:
   - Administrador Global
   - Administrador de Segurança
   - Administrador de acesso condicional

## <a name="terms-of-use-document"></a>Documento Termos de utilização

As políticas de utilização da AZure AD utilizam o formato PDF para apresentar conteúdo. O ficheiro PDF pode ter qualquer tipo de conteúdo, como documentos contratuais existentes, permitindo-lhe recolher contratos de utilizador final durante o início de sessão do utilizador. Para suportar os utilizadores em dispositivos móveis, o tamanho recomendado da fonte no PDF é de 24 pontos.

## <a name="add-terms-of-use"></a>Adicionar termos de utilização

Uma vez finalizado o documento de política de utilização, utilize o seguinte procedimento para o adicionar.

1. Inscreva-se no Azure como Administrador Global, Administrador de Segurança ou Administrador de Acesso Condicional.
1. Navegue para **Termos de utilização** em [https://aka.ms/catou](https://aka.ms/catou).

    ![Acesso Condicional - Termos de utilização da lâmina](./media/terms-of-use/tou-blade.png)

1. Clique em **Novos termos**.

    ![Novo painel de uso para especificar os seus termos de definições de utilização](./media/terms-of-use/new-tou.png)

1. Na caixa **Nome,** insira um nome para os termos da política de utilização que será usada no portal Azure.
1. Na caixa de **nomes Do Visor,** introduza um título que os utilizadores vêem quando entram.
1. Para **o documento termos de utilização,** navegue nos termos finais da política de utilização PDF e selecione-o.
1. Selecione o idioma para os seus termos de utilização do documento de política. A opção idioma permite-lhe carregar vários termos de políticas de uso, cada uma com um idioma diferente. A versão da política de termos de utilização que um utilizador final verá será baseada nas suas preferências de navegador.
1. Para exigir que os utilizadores finais vejam os termos da política de utilização antes de os aceitar, desemote **os utilizadores para expandir os termos de utilização** para **On**.
1. Para exigir que os utilizadores finais aceitem a sua política de utilização em todos os dispositivos a partir dos quais estão a aceder, desemboar **os utilizadores para consentir em todos os dispositivos** para **on**. Os utilizadores poderão ser obrigados a instalar aplicações adicionais se esta opção estiver ativada. Para obter mais informações, consulte [os termos de utilização por dispositivo.](#per-device-terms-of-use)
1. Se pretender expirar os termos de utilização dos consentimentos de uma política de utilização num horário, desa esta **hora de definir os consentimentos** de **Expiração** para On . Quando programado para On, são apresentadas duas definições de agenda adicionais.

    ![Expire conseqüentes definições para definir data de início, frequência e duração](./media/terms-of-use/expire-consents.png)

1. Utilize as definições de Início de **Expiração** e **frequência** para especificar o calendário para os termos de validade da política de utilização. A tabela a seguir mostra o resultado de algumas definições de exemplo:

   | Expire a partir de | Frequência | Resultado |
   | --- | --- | --- |
   | Data de hoje  | Mensalmente | A partir de hoje, os utilizadores devem aceitar os termos da política de utilização e depois readcetar todos os meses. |
   | Data no futuro  | Mensalmente | A partir de hoje, os utilizadores devem aceitar os termos da política de utilização. Quando a data futura ocorrer, os consentimentos expirarão e, em seguida, os utilizadores devem reacetitar todos os meses.  |

   Por exemplo, se definir o prazo de início de validade para **1 de janeiro** e a frequência para **Mensal,** eis como podem ocorrer expirações para dois utilizadores:

   | Utilizador | Primeira data de aceitação | Primeira data de expiração | Segunda data de expiração | Terceira data de expiração |
   | --- | --- | --- | --- | --- |
   | Alice | 1 jan | Fev 1 | Mar 1 | Abr 1 |
   | Bob | 15 jan | Fev 1 | Mar 1 | Abr 1 |

1. Utilize a **Duração antes de reademptar requer (dias)** a definição para especificar o número de dias antes de o utilizador voltar a aceitar os termos da política de utilização. Isto permite que os utilizadores sigam o seu próprio horário. Por exemplo, se definir a duração para **30** dias, eis como podem ocorrer expirações para dois utilizadores:

   | Utilizador | Primeira data de aceitação | Primeira data de expiração | Segunda data de expiração | Terceira data de expiração |
   | --- | --- | --- | --- | --- |
   | Alice | 1 jan | 31 de janeiro | Mar 2 | Abr 1 |
   | Bob | 15 jan | Fev 14 | Mar 16 | Abr 15 |

   É possível utilizar os **consentimentos** e duração de **Expiração antes de reaccepte requer (dias)** configurações em conjunto, mas normalmente usa uma ou outra.

1. No **Acesso Condicional,** utilize a lista de **modelos de política de acesso condicional para** selecionar o modelo para impor os termos da política de utilização.

    ![Lista de drop-down de acesso condicional para selecionar um modelo de política](./media/terms-of-use/conditional-access-templates.png)

   | Modelo | Description |
   | --- | --- |
   | **Acesso a aplicativos em nuvem para todos os hóspedes** | Será criada uma política de Acesso Condicional para todos os hóspedes e aplicações na nuvem. Esta política tem impacto no portal Azure. Uma vez criado, poderá ser obrigado a assinar e iniciar seduca. |
   | **Acesso a aplicativos na nuvem para todos os utilizadores** | Será criada uma política de Acesso Condicional para todos os utilizadores e todas as aplicações na nuvem. Esta política tem impacto no portal Azure. Uma vez criado, terá de assinar e iniciar seduca. |
   | **Política personalizada** | Selecione os utilizadores, grupos e aplicações a que estes termos de política de utilização serão aplicados. |
   | **Criar política de acesso condicional mais tarde** | Estes termos de política de utilização aparecerão na lista de controlo de subvenções ao criar uma política de acesso condicional. |

   >[!IMPORTANT]
   >Os controlos da política de acesso condicional (incluindo os termos das políticas de utilização) não suportam a aplicação das contas de serviço. Recomendamos a exclusão de todas as contas de serviço da política de Acesso Condicional.

    As políticas de acesso condicional personalizado permitem termos de utilização granular, até uma aplicação em nuvem específica ou grupo de utilizadores. Para obter mais informações, consulte [Quickstart: Exija que os termos de utilização sejam aceites antes de aceder às aplicações na nuvem.](require-tou.md)

1. Clique em **Criar**.

    Se selecionou um modelo de acesso condicional personalizado, então aparece um novo ecrã que lhe permite criar a política de Acesso Condicional personalizada.

   ![Novo painel de acesso condicional se você escolher o modelo de política de acesso condicional personalizado](./media/terms-of-use/custom-policy.png)

   Deve agora ver os seus novos termos de políticas de utilização.

   ![Novos termos de utilização listados nos termos da lâmina de utilização](./media/terms-of-use/create-tou.png)

## <a name="view-report-of-who-has-accepted-and-declined"></a>Ver relatório de quem aceitou e recusou

O painel Termos de utilização mostra uma contagem dos utilizadores que aceitaram e recusaram. Estas contagens e quem aceitou/recusou são armazenadas para a vida útil dos termos da política de utilização.

1. Inicie sessão no Azure e navegue para **Termos de utilização** em [https://aka.ms/catou](https://aka.ms/catou).

    ![Termos de utilização que listam o número de mostra de utilizador aceitaram e recusaram](./media/terms-of-use/view-tou.png)

1. Para uma política de uso, clique nos números em **Accepted** ou **Recusou** ver o estado atual para os utilizadores.

    ![Termos de uso consente painel listagem dos utilizadores que aceitaram](./media/terms-of-use/accepted-tou.png)

1. Para ver o histórico de um utilizador individual, clique na elipse **(...)** e, em seguida, **ver História**.

    ![Ver menu de contexto de história para um utilizador](./media/terms-of-use/view-history-menu.png)

   Na visão da história, vê-se uma história de todos os aceitações, declínios e expirações.

   ![Ver Histórico lista lista o histórico aceita, declina e expirações para um utilizador](./media/terms-of-use/view-history-pane.png)

## <a name="view-azure-ad-audit-logs"></a>Ver registos de auditoria da AZure AD

Se quiser ver atividade adicional, as políticas de utilização do Azure AD incluem registos de auditoria. Cada consentimento do utilizador desencadeia um evento nos registos de auditoria que é armazenado durante **30 dias**. Pode ver estes registos no portal ou transferi-los como um ficheiro. csv.

Para começar com os registos de auditoria da AZure AD, utilize o seguinte procedimento:

1. Inicie sessão no Azure e navegue para **Termos de utilização** em [https://aka.ms/catou](https://aka.ms/catou).
1. Selecione uma política de uso.
1. Clique em **Ver registos de auditoria**.

    ![Termos de utilização com a opção de registos de auditoria Ver realçada](./media/terms-of-use/audit-tou.png)

1. No ecrã de registos de auditoria Azure AD, pode filtrar as informações utilizando as listas fornecidas para direcionar informações específicas de registo de auditoria.

    Também pode clicar em **Transferir** para transferir as informações para um ficheiro. csv, para utilização local.

   ![Azure AD audita data de listagem de ecrã, política de destino, iniciada por, e atividade](./media/terms-of-use/audit-logs-tou.png)

   Se clicar num registo, aparece um painel com detalhes adicionais da atividade.

   ![Detalhes da atividade para um registo que mostra atividade, estado de atividade, iniciado por, política de destino](./media/terms-of-use/audit-log-activity-details.png)

## <a name="what-terms-of-use-looks-like-for-users"></a>Que termos de uso se parecem para os utilizadores

Uma vez criada e aplicada uma política tou, os utilizadores, que estão no âmbito, verão o seguinte ecrã durante a entrada.

![Exemplo de uso que aparece quando um utilizador assina em](./media/terms-of-use/user-tou.png)

Os utilizadores podem ver os termos da política de utilização e, se necessário, usar botões para fazer zoom para dentro e para fora.

![Vista dos termos de utilização com botões de zoom](./media/terms-of-use/zoom-buttons.png)

O ecrã que se segue mostra como uma política tou fica nos dispositivos móveis.

![Exemplo de uso que aparece quando um utilizador assina num dispositivo móvel](./media/terms-of-use/mobile-tou.png)

Os utilizadores só são obrigados a aceitar os termos da política de utilização uma vez e não verão novamente os termos da política de utilização nos subsequentes insusitados.

### <a name="how-users-can-review-their-terms-of-use"></a>Como os utilizadores podem rever os seus termos de utilização

Os utilizadores podem rever e ver os termos das políticas de utilização que aceitaram utilizando o seguinte procedimento.

1. Inicie sessão em [https://myapps.microsoft.com](https://myapps.microsoft.com).
1. No canto superior direito, clique no seu nome e selecione **Perfil**.

    ![MyApps site com o painel do utilizador aberto](./media/terms-of-use/tou14.png)

1. Na página Perfil, clique em **Rever os termos de utilização**.

    ![Página de perfil para um utilizador que mostra os termos de utilização do link](./media/terms-of-use/tou13a.png)

1. A partir daí, pode rever os termos das políticas de utilização que aceitou.

## <a name="edit-terms-of-use-details"></a>Editar termos de utilização

Pode editar alguns detalhes dos termos das políticas de utilização, mas não pode modificar um documento existente. O procedimento a seguir descreve como editar os detalhes.

1. Inicie sessão no Azure e navegue para **Termos de utilização** em [https://aka.ms/catou](https://aka.ms/catou).
1. Selecione os termos da política de utilização que pretende editar.
1. Clique **em Editar termos**.
1. Nos termos de utilização da Edição, pode alterar o seguinte:
    - **Nome** – este é o nome interno da TOU que não é partilhado com os utilizadores finais
    - **Nome do ecrã** – este é o nome que os utilizadores finais podem ver ao visualizar a ToU
    - **Exigir que os utilizadores expandam os termos de utilização** – Configurar isto para **On** forçará o uso final a expandir os termos do documento de política de utilização antes de o aceitar.
    - (Pré-visualização) Pode atualizar um documento **de utilização existente**
    - Pode adicionar uma língua a uma ToU existente

   Se houver outras configurações que gostaria de alterar, como o documento PDF, exigir que os utilizadores consintam em cada dispositivo, expirem consentimentos, duração antes de reacetar ou política de Acesso Condicional, deve criar uma nova política de ToU.

    ![Editar mostrando diferentes opções linguísticas ](./media/terms-of-use/edit-terms-use.png)

1. Uma vez feito, clique em **Guardar** para guardar as suas alterações.

## <a name="update-the-version-or-pdf-of-an-existing-terms-of-use"></a>Atualizar a versão ou pdf de um termos de utilização existentes

1.  Inscreva-se no Azure e navegue para [Termos de Utilização](https://aka.ms/catou)
2.  Selecione os termos da política de utilização que pretende editar.
3.  Clique **em Editar termos**.
4.  Para o idioma que pretende atualizar uma nova versão, clique em **Update** under the action column

    ![Editar termos de uso painel mostrando nome e expandir opções](./media/terms-of-use/edit-terms-use.png)

5.  No painel à direita, carrece o pdf para a nova versão
6.  Existe também uma opção de alternância **aqui, exija um novo aceinhão** se pretender que os seus utilizadores aceitem esta nova versão da próxima vez que iniciarem sação. Se exigir que os seus utilizadores voltem a aceitar, da próxima vez que tentarem aceder ao recurso definido na sua política de acesso condicional, serão solicitados a aceitar esta nova versão. Se não necessitar que os seus utilizadores reacetirem, o seu consentimento anterior permanecerá atual e apenas novos utilizadores que não tenham consentido antes ou cujo consentimento expire verão a nova versão.

    ![Editar termos de utilização re-aceitar opção realçada](./media/terms-of-use/re-accept.png)

7.  Depois de ter carregado o seu novo pdf e decidido a reapreciar, clique em Adicionar na parte inferior do painel.
8.  Verá agora a versão mais recente sob a coluna Documento.

## <a name="view-previous-versions-of-a-tou"></a>Ver versões anteriores de um ToU

1.  Inicie sessão no Azure e navegue para **Termos de utilização** em https://aka.ms/catou.
2.  Selecione os termos da política de utilização para os quais deseja visualizar um histórico de versão.
3.  Clique em **Idiomas e histórico de versão**
4.  Clique em **Ver ver as versões anteriores.**

    ![detalhes do documento, incluindo versões linguísticas](./media/terms-of-use/document-details.png)

5.  Pode clicar no nome do documento para descarregar essa versão

## <a name="see-who-has-accepted-each-version"></a>Veja quem aceitou cada versão

1.  Inicie sessão no Azure e navegue para **Termos de utilização** em https://aka.ms/catou.
2.  Para ver quem aceitou atualmente o ToU clique no número sob a coluna **Aceite** para o ToU que deseja.
3.  Por padrão, a página seguinte irá mostrar-lhe o estado atual de cada aceitação de utilizadores para a ToU
4.  Se quiser ver os eventos de consentimento anteriores, pode selecionar **All** from the **Current State** drop-down. Agora pode ver cada utilizadores em detalhes sobre cada versão e o que aconteceu.
5.  Em alternativa, pode selecionar uma versão específica do drop-down da **versão**  para ver quem aceitou essa versão específica.


## <a name="add-a-tou-language"></a>Adicione uma língua ToU

O procedimento a seguir descreve como adicionar uma língua ToU.

1. Inicie sessão no Azure e navegue para **Termos de utilização** em [https://aka.ms/catou](https://aka.ms/catou).
1. Selecione os termos da política de utilização que pretende editar.
1. Clique **em Termos de Edição**
1. Clique em **Adicionar idioma** na parte inferior da página.
1. Nos termos de utilização do painel de linguagem de utilização, faça o upload do PDF localizado e selecione o idioma.

    ![Termos de utilização selecionados e mostrando o separador idiomas no painel de detalhes](./media/terms-of-use/select-language.png)

1. Clique **em Adicionar idioma**.
1. Clique em **Guardar**

1. Clique **em Adicionar** para adicionar o idioma.

## <a name="per-device-terms-of-use"></a>Termos de utilização por dispositivo

O **Requerer que os utilizadores consintam em todas as** definições de dispositivos, permite que os utilizadores finais aceitem a sua política de utilização em todos os dispositivos a partir dos quais estão a aceder. O utilizador final será obrigado a registar o seu dispositivo em Azure AD. Quando o dispositivo está registado, o ID do dispositivo é utilizado para impor os termos da política de utilização em cada dispositivo.

Aqui está uma lista das plataformas e software suportados.

> [!div class="mx-tableFixed"]
> |  | iOS | Android | Windows 10 | Outro |
> | --- | --- | --- | --- | --- |
> | **Aplicação nativa** | Yes | Yes | Yes |  |
> | **Microsoft Edge** | Yes | Yes | Yes |  |
> | **Internet Explorer** | Yes | Yes | Yes |  |
> | **Cromado (com extensão)** | Yes | Yes | Yes |  |

Os termos de utilização por dispositivo têm os seguintes constrangimentos:

- Um dispositivo só pode ser associado a um inquilino.
- Um utilizador deve ter permissões para se juntar ao seu dispositivo.
- A aplicação Intune Registration não é suportada. Certifique-se de que está excluído de qualquer política de acesso condicional que exija a política de Termos de Utilização.
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

Pode eliminar as antigas políticas de utilização utilizando o seguinte procedimento.

1. Inicie sessão no Azure e navegue para **Termos de utilização** em [https://aka.ms/catou](https://aka.ms/catou).
1. Selecione os termos da política de utilização que pretende remover.
1. Clique em **Eliminar termos**.
1. Na mensagem que é apresentada a perguntar se pretende continuar, clique em **Sim**.

    ![Mensagem a pedir confirmação para apagar termos de utilização](./media/terms-of-use/delete-tou.png)

   Não deve mais ver os seus termos de política de uso.

## <a name="user-acceptance-record-deletion"></a>Supressão do registo de aceitação do utilizador

Os registos de aceitação do utilizador são eliminados:

- Quando o administrador elimina explicitamente a ToU. Quando isto acontece, todos os registos de aceitação associados a essa ToU específica também são eliminados.
- Quando o inquilino perder a licença Azure Ative Directory Premium.
- Quando o inquilino é apagado.

## <a name="policy-changes"></a>Alterações de política

As políticas de acesso condicional têm efeito imediato. Quando isso acontecer, o administrador começará a ver "nuvens tristes" ou "problemas simbólicos da AD AZure". O administrador deve assinar e assinar novamente para satisfazer a nova política.

> [!IMPORTANT]
> Os utilizadores abrangidos pelo âmbito definido terão de terminar e iniciar sessão de modo a satisfazerem uma nova política:
>
> - uma política de acesso condicional é ativada em termos de política de utilização
> - ou um segundo termos de política de uso é criado

## <a name="b2b-guests"></a>Hóspedes B2B

A maioria das organizações tem um processo em curso para que os seus colaboradores consintam nos termos da sua organização de política de uso e declarações de privacidade. Mas como pode impor os mesmos consentimentos para os hóspedes do Azure AD (B2B) quando são adicionados via SharePoint ou Teams? Utilizando políticas de acesso condicional e termos de uso, pode impor uma política diretamente para os utilizadores convidados B2B. Durante o fluxo de resgate de convites, o utilizador é apresentado com os termos da política de utilização. Este suporte está atualmente em pré-visualização.

As políticas de utilização só serão apresentadas quando o utilizador tiver uma conta de cliente em Azure AD. O SharePoint Online tem atualmente [uma experiência de destinatário de partilha externa ad hoc](/sharepoint/what-s-new-in-sharing-in-targeted-release) para partilhar um documento ou uma pasta que não requer que o utilizador tenha uma conta de hóspedes. Neste caso, não é apresentada uma política de utilização.

![Painel de utilizadores e grupos - Inclua separador com todas as opções de utilizadores convidados verificada](./media/terms-of-use/b2b-guests.png)

## <a name="support-for-cloud-apps"></a>Suporte para aplicativos na nuvem

As políticas de utilização podem ser utilizadas para diferentes aplicações em nuvem, como a Azure Information Protection e a Microsoft Intune. Este suporte está atualmente em pré-visualização.

### <a name="azure-information-protection"></a>Azure Information Protection

Pode configurar uma política de acesso condicional para a aplicação Azure Information Protection e exigir uma política de uso quando um utilizador acede a um documento protegido. Isto irá desencadear uma política de utilização antes de um utilizador aceder a um documento protegido pela primeira vez.

![Aplicativos cloud painel com app microsoft Azure Information Protection selecionado](./media/terms-of-use/cloud-app-info-protection.png)

### <a name="microsoft-intune-enrollment"></a>Inscrição microsoft Intune

Pode configurar uma política de acesso condicional para a aplicação De inscrição do Microsoft Intune e exigir uma política de utilização antes da inscrição de um dispositivo no Intune. Para mais informações, consulte a [solução 'Escolha de Leitura' para o post de blog da organização.](https://go.microsoft.com/fwlink/?linkid=2010506&clcid=0x409)

![Aplicativos cloud painel com app Microsoft Intune selecionado](./media/terms-of-use/cloud-app-intune.png)

> [!NOTE]
> A aplicação Intune Registration não é suportada para [termos de utilização por dispositivo.](#per-device-terms-of-use)

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes

**P: Não posso assinar usando o PowerShell quando os termos de utilização estão ativados.**<br />
R: Os termos de utilização só podem ser aceites quando autenticam interativamente.

**P: Como vejo quando/se um utilizador aceitou um termos de utilização?**<br />
R: Nos Termos de Utilização, clique no número em **Aceito**. Também pode visualizar ou pesquisar a atividade de aceitação nos registos de auditoria Azure AD. Para obter mais informações, consulte o relatório de quem aceitou e recusou e [ver os registos de auditoria da AZure AD](#view-azure-ad-audit-logs).

**P: Por quanto tempo as informações são armazenadas?**<br />
R: O utilizador conta nos termos do relatório de utilização e quem aceitou/recusou são armazenados para a vida útil dos termos de utilização. Os registos de auditoria da AZure AD são armazenados durante 30 dias.

**P: Por que vejo um número diferente de consentimentos nos termos do relatório de utilização vs. os registos de auditoria da AZure AD?**<br />
R: Os termos de utilização são armazenados durante o tempo de vida dessa política de utilização, enquanto os registos de auditoria AZURE AD são armazenados durante 30 dias. Além disso, os termos do relatório de utilização apenas exibem o estado de consentimento atual dos utilizadores. Por exemplo, se um utilizador recusar e aceitar, os termos do relatório de utilização apenas mostrarão que o utilizador aceita. Se precisar de ver o histórico, pode utilizar os registos de auditoria AZure AD.

**P: Se eu editar os detalhes para uma política de uso, isso requer que os utilizadores aceitem novamente?**<br />
R: Não, se um administrador editar os detalhes para uma política de uso (nome, nome de exibição, exigir que os utilizadores expandam ou adicionem um idioma), não exige que os utilizadores reacebam os novos termos.

**P: Posso atualizar um documento de política de utilização existente?**<br />
R: Atualmente, não é possível atualizar um documento de política de utilização existente. Para alterar um documento de política de uso, terá de criar um novo termos de exemplo de política de utilização.

**P: Se as hiperligações estiverem nos termos do documento PDF da política de utilização, os utilizadores finais poderão clicar neles?**<br />
R: Sim, os utilizadores finais são capazes de selecionar hiperligações para páginas adicionais, mas as ligações para secções dentro do documento não são suportadas. Além disso, as hiperligações em termos de utilização de PDFs de política não funcionam quando acedidas a partir do portal Azure AD MyApps/MyAccount.

**P: Pode um termos de política de uso suportar várias línguas?**<br />
R: Sim. Atualmente existem 108 línguas diferentes que um administrador pode configurar para um único termos de política de uso. Um administrador pode carregar vários documentos PDF e marcar esses documentos com um idioma correspondente (até 108). Quando os utilizadores finais se inscrevem, olhamos para a sua preferência de idioma do navegador e exibimos o documento correspondente. Se não houver correspondência, exibiremos o documento predefinido, que é o primeiro documento que é carregado.

**P: Quando é desencadeada a política de utilização?**<br />
R: Os termos da política de utilização são desencadeados durante a experiência de entrada.

**P: Quais as aplicações a que posso orientar uma política de utilização?**<br />
R: Pode criar uma política de Acesso Condicional nas aplicações da empresa utilizando a autenticação moderna. Para obter mais informações, consulte [aplicações empresariais](./../manage-apps/view-applications-portal.md).

**P: Posso adicionar vários termos de políticas de utilização a um determinado utilizador ou app?**<br />
R: Sim, criando várias políticas de Acesso Condicional direcionadas a esses grupos ou aplicações. Se um utilizador se enquadrar no âmbito de várias políticas de utilização, aceita um termos de política de utilização de cada vez.

**P: O que acontece se um utilizador recusar os termos da política de utilização?**<br />
R: O acesso do utilizador à aplicação é bloqueado. O utilizador teria de voltar a fazer o seu sedu.

**P: É possível desacepem uma política de uso que foi previamente aceite?**<br />
R: Pode [rever os termos de políticas de uso previamente aceites,](#how-users-can-review-their-terms-of-use)mas atualmente não há uma maneira de não aceitar.

**P: O que acontece se eu também estiver usando termos e condições intune?**<br />
R: Se tiver configurado os termos de utilização do AZure AD e os [termos e condições intune,](/intune/terms-and-conditions-create)o utilizador será obrigado a aceitar ambos. Para mais informações, consulte a [solução Escolha dos Termos certos para o post de blog da sua organização.](https://go.microsoft.com/fwlink/?linkid=2010506&clcid=0x409)

**P: Que pontos finais utiliza os termos do serviço de utilização para autenticação?**<br />
R: Os termos de utilização utilizam os seguintes pontos finais para a autenticação: https://tokenprovider.termsofuse.identitygovernance.azure.com e https://account.activedirectory.windowsazure.com . Se a sua organização tiver uma lista de sinais de admissão de URLs para inscrição, terá de adicionar estes pontos finais à sua lista de indicações, juntamente com os pontos finais AZURE AD para a sua inscrição.

## <a name="next-steps"></a>Passos seguintes

- [Quickstart: Exigir que os termos de uso sejam aceites antes de aceder a aplicações na nuvem](require-tou.md)
