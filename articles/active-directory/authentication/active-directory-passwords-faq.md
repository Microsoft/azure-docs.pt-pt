---
title: Perguntas frequentes sobre redefinição de senha de autoatendimento-Azure Active Directory
description: Perguntas frequentes sobre a redefinição de senha de autoatendimento do Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4001f3c88b676a2786159946a8981823e18ea5f6
ms.sourcegitcommit: 32242bf7144c98a7d357712e75b1aefcf93a40cc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/04/2019
ms.locfileid: "70274421"
---
# <a name="password-management-frequently-asked-questions"></a>Perguntas frequentes sobre o gerenciamento de senhas

Veja a seguir algumas perguntas frequentes (FAQ) para todas as coisas relacionadas à redefinição de senha.

Se você tiver uma pergunta geral sobre o Azure Active Directory (Azure AD) e a redefinição de senha de autoatendimento (SSPR) que não é respondida aqui, você pode pedir à Comunidade para obter assistência no [Fórum do Azure ad](https://social.msdn.microsoft.com/Forums/en-US/home?forum=WindowsAzureAD). Os membros da comunidade incluem engenheiros, gerentes de produto, MVPs e colegas profissionais de ti.

Estas perguntas frequentes são divididas nas seguintes seções:

* [Perguntas sobre o registro de redefinição de senha](#password-reset-registration)
* [Perguntas sobre a redefinição de senha](#password-reset)
* [Perguntas sobre alteração de senha](#password-change)
* [Perguntas sobre relatórios de gerenciamento de senhas](#password-management-reports)
* [Perguntas sobre write-back de senha](#password-writeback)

## <a name="password-reset-registration"></a>Registo de reposição de palavra-passe

* **P:  Meus usuários podem registrar seus próprios dados de redefinição de senha?**

  > **R:** Sim. Desde que a redefinição de senha esteja habilitada e seja licenciada, os usuários poderão acessar o portal de https://aka.ms/ssprsetup) registro de redefinição de senha (para registrar suas informações de autenticação. Os usuários também podem se registrar por meio do https://myapps.microsoft.com) painel de acesso (. Para registrar-se por meio do painel de acesso, eles precisam selecionar sua imagem de perfil, selecionar **perfil**e, em seguida, selecionar a opção **registrar para redefinição de senha** .
  >
  >
* **P:  Se eu habilitar a redefinição de senha para um grupo e decidir habilitá-la para todos os meus usuários precisarem registrar novamente?**

  > **R:** Não. Os usuários que preencheram os dados de autenticação não precisam ser registrados novamente.
  >
  >
* **P:  Posso definir dados de redefinição de senha em nome dos meus usuários?**

  > **R:** Sim, você pode fazer isso com Azure AD Connect, PowerShell, o [portal do Azure](https://portal.azure.com)ou o [centro de administração do Microsoft 365](https://admin.microsoft.com). Para obter mais informações, consulte [dados usados pela redefinição de senha de autoatendimento do Azure ad](howto-sspr-authenticationdata.md).
  >
  >
* **P:  Posso sincronizar dados de perguntas de segurança no local?**

  > **R:** Não, isso não é possível hoje.
  >
  >
* **P:  Meus usuários podem registrar dados de forma que outros usuários não possam ver esses dados?**

  > **R:** Sim. Quando os usuários registram dados usando o portal de registro de redefinição de senha, os dados são salvos em campos de autenticação privada que são visíveis somente para administradores globais e o usuário.
  >
  >
* **P:  Meus usuários precisam ser registrados antes de poderem usar a redefinição de senha?**

  > **R:** Não. Se você definir informações de autenticação suficientes em seu nome, os usuários não precisarão se registrar. A redefinição de senha funciona contanto que você tenha formatado corretamente os dados armazenados nos campos apropriados no diretório.
  >
  >
* **P:  Posso sincronizar ou definir os campos telefone de autenticação, email de autenticação ou telefone alternativo de autenticação em nome dos meus usuários?**

  > **R:** Os campos que podem ser definidos por um administrador global são definidos no artigo [SSPR data requirements](howto-sspr-authenticationdata.md).
  >
  >
* **P:  Como o portal de registro determina quais opções mostrar aos meus usuários?**

  > **R:** O portal de registro de redefinição de senha mostra apenas as opções que você habilitou para os usuários. Essas opções são encontradas na seção **política** de redefinição de senha do usuário da guia **Configurar** do seu diretório. Por exemplo, se você não habilitar perguntas de segurança, os usuários não poderão se registrar para essa opção.
  >
  >
* **P:  Quando um usuário é considerado registrado?**

  > **R:** Um usuário é considerado registrado para SSPR quando registrou pelo menos o **número de métodos necessários para redefinir** uma senha que você definiu no [portal do Azure](https://portal.azure.com).
  >
  >

## <a name="password-reset"></a>Reposição de palavra-passe

* **P:  Você impede que os usuários de várias tentativas redefinam uma senha em um curto período de tempo?**

  > **R:** Sim, há recursos de segurança incorporados na redefinição de senha para protegê-los contra uso indevido. 
  >
  > Os usuários podem tentar apenas cinco tentativas de redefinição de senha dentro de um período de 24 horas antes de serem bloqueadas por 24 horas. 
  >
  > Os usuários podem tentar validar um número de telefone, enviar um SMS ou validar perguntas de segurança e responder apenas cinco vezes em uma hora antes que eles sejam bloqueados por 24 horas. 
  >
  > Os usuários podem enviar um email no máximo 10 vezes em um período de 10 minutos antes que eles sejam bloqueados por 24 horas.
  >
  > Os contadores são redefinidos quando um usuário redefine sua senha.
  >
  >
* **P:  Quanto tempo devo esperar para receber um email, SMS ou chamada telefônica da redefinição de senha?**

  > **R:** Emails, mensagens SMS e chamadas telefônicas devem chegar em menos de um minuto. O caso normal é de 5 a 20 segundos.
  > Se você não receber a notificação neste período de tempo:
  > * Verifique sua pasta de lixo eletrônico.
  > * Verifique se o número ou o email que está sendo contatado é o que você espera.
  > * Verifique se os dados de autenticação no diretório estão formatados corretamente, por exemplo, + 1 4255551234 *ou\@contoso.com do usuário*. 
* **P:  Quais idiomas têm suporte na redefinição de senha?**

  > **R:** A interface do usuário de redefinição de senha, as mensagens SMS e as chamadas de voz são localizadas nos mesmos idiomas com suporte no Office 365.
  >
  >
* **P:  Quais partes da experiência de redefinição de senha ficam marcadas quando eu defino os itens de identidade visual da minha pasta na guia Configurar?**

  > **R:** O portal de redefinição de senha mostra o logotipo da sua organização e permite que você configure o link "entre em contato com seu administrador" para apontar para um email ou URL personalizado. Qualquer email enviado pela redefinição de senha inclui o logotipo, as cores e o nome da sua organização no corpo do email e é personalizado com base nas configurações desse nome específico.
  >
  >
* **P:  Como posso instruir meus usuários sobre onde ir para redefinir suas senhas?**

  > **R:** Experimente algumas das sugestões em nosso artigo de [implantação do SSPR](howto-sspr-deployment.md#communications-plan) .
  >
  >
* **P:  Posso usar esta página de um dispositivo móvel?**

  > **R:** Sim, esta página funciona em dispositivos móveis.
  >
  >
* **P:  Há suporte para desbloqueio de contas de Active Directory locais quando os usuários redefinem suas senhas?**

  > **R:** Sim. Quando um usuário redefine sua senha, se o Write-back de senha tiver sido implantado por meio de Azure AD Connect, a conta desse usuário será desbloqueada automaticamente quando a senha for redefinida.
  >
  >
* **P:  Como posso integrar a redefinição de senha diretamente à experiência de entrada da área de trabalho do usuário?**

  > **R:** Se você for um cliente Azure AD Premium, poderá instalar o Microsoft Identity Manager sem custo adicional e implantar a solução de redefinição de senha local.
  >
  >
* **P:  Posso definir perguntas de segurança diferentes para localidades diferentes?**

  > **R:** Não, isso não é possível hoje.
  >
  >
* **P:  Quantas perguntas posso configurar para a opção de autenticação de perguntas de segurança?**

  > **R:** Você pode configurar até 20 perguntas de segurança personalizadas no [portal do Azure](https://portal.azure.com).
  >
  >
* **P:  Por quanto tempo as perguntas de segurança podem ser?**

  > **R:** As perguntas de segurança podem ter de 3 a 200 caracteres de comprimento.
  >
  >
* **P:  Por quanto tempo as respostas a perguntas de segurança podem ser longas?**

  > **R:** As respostas podem ter de 3 a 40 caracteres de comprimento.
  >
  >
* **P:  Há respostas duplicadas para perguntas de segurança rejeitadas?**

  > **R:** Sim, rejeitamos respostas duplicadas para perguntas de segurança.
  >
  >
* **P:  Um usuário pode registrar a mesma pergunta de segurança mais de uma vez?**

  > **R:** Não. Depois que um usuário registra uma pergunta específica, ele não pode se registrar para essa pergunta uma segunda vez.
  >
  >
* **P:  É possível definir um limite mínimo de perguntas de segurança para registro e redefinição?**

  > **R:** Sim, um limite pode ser definido para registro e outro para redefinição. Três a cinco perguntas de segurança podem ser necessárias para o registro, e três a cinco perguntas podem ser necessárias para redefinição.
  >
  >
* **P:  Configurei minha política para exigir que os usuários usem perguntas de segurança para redefinição, mas os administradores do Azure parecem estar configurados de forma diferente.**

  > **R:** Esse é o comportamento esperado. A Microsoft impõe uma política de reposição de palavra-passe predefinida forte de duas portas para qualquer função de administrador do Azure. Isso impede que os administradores usem perguntas de segurança. Você pode encontrar mais informações sobre essa política nas [diretivas e restrições de senha no artigo Azure Active Directory](concept-sspr-policy.md) .
  >
  >
* **P:  Se um usuário tiver registrado mais do que o número máximo de perguntas necessárias para a redefinição, como as perguntas de segurança são selecionadas durante a redefinição?**

  > **R:** *N* o número de perguntas de segurança é selecionado aleatoriamente do número total de perguntas que um usuário registrou, em que *N* é o valor definido para a opção **número de perguntas obrigatórias para redefinição** . Por exemplo, se um usuário tiver registrado cinco perguntas de segurança, mas apenas três forem necessárias para redefinir uma senha, três das cinco perguntas serão selecionadas aleatoriamente e serão apresentadas em reinicialização. Para evitar a escolha de uma pergunta, se o usuário obtiver as respostas para as perguntas erradas, o processo de seleção será iniciado.
  >
  >
* **P:  Por quanto tempo os emails e as senhas de uso único do SMS são válidos?**

  > **R:** O tempo de vida da sessão para a redefinição de senha é de 15 minutos. Desde o início da operação de redefinição de senha, o usuário tem 15 minutos para redefinir sua senha. O email e a senha de uso único do SMS são válidos por 5 minutos durante a sessão de redefinição de senha.
  >
  >
* **P:  Posso impedir que os usuários redefinam sua senha?**

  > **R:** Sim, se você usar um grupo para habilitar o SSPR, poderá remover um usuário individual do grupo que permite que os usuários redefinam sua senha. Se o usuário for um administrador global, ele manterá a capacidade de redefinir sua senha e isso não poderá ser desabilitado.
  >
  >

## <a name="password-change"></a>Alteração da palavra-passe

* **P:  Onde os meus usuários devem ir para alterar suas senhas?**

  > **R:** Os usuários podem alterar suas senhas em qualquer lugar em que veem sua imagem ou ícone de perfil, como no canto superior direito de suas experiências do portal do [Office 365 ou do](https://portal.office.com) [painel de acesso](https://myapps.microsoft.com) . Os usuários podem alterar suas senhas na [página de perfil do painel de acesso](https://account.activedirectory.windowsazure.com/r#/profile). Os usuários também podem ser solicitados a alterar suas senhas automaticamente na página de entrada do Azure AD se suas senhas tiverem expirado. Por fim, os usuários podem navegar para o [portal de alteração de senha do Azure ad](https://account.activedirectory.windowsazure.com/ChangePassword.aspx) diretamente se quiserem alterar suas senhas.
  >
  >
* **P:  Meus usuários podem ser notificados no portal do Office quando sua senha local expirar?**

  > **R:** Sim, isso é possível hoje se você usar Serviços de Federação do Active Directory (AD FS) (AD FS). Se você usar AD FS, siga as instruções no artigo [enviando declarações de política de senha com AD FS](https://technet.microsoft.com/windows-server-docs/identity/ad-fs/operations/configure-ad-fs-to-send-password-expiry-claims?f=255&MSPPError=-2147217396) . Se você usar a sincronização de hash de senha, isso não é possível hoje. Não sincronizamos políticas de senha de diretórios locais, portanto, não é possível postar notificações de expiração para experiências de nuvem. Em ambos os casos, também é possível [notificar os usuários cujas senhas estão prestes a expirar por meio do PowerShell](https://social.technet.microsoft.com/wiki/contents/articles/23313.notify-active-directory-users-about-password-expiry-using-powershell.aspx).
  >
  >
* **P:  Posso impedir que os usuários alterem suas senhas?**

  > **R:** Para usuários somente na nuvem, as alterações de senha não podem ser bloqueadas. Para usuários locais, você pode definir a opção o **usuário não pode alterar a senha** como selecionada. Os usuários selecionados não podem alterar sua senha.
  >
  >

## <a name="password-management-reports"></a>Relatórios de gerenciamento de senhas

* **P:  Quanto tempo leva para os dados aparecerem nos relatórios de gerenciamento de senhas?**

  > **R:** Os dados devem aparecer nos relatórios de gerenciamento de senha em 5 a 10 minutos. Em alguns casos, pode levar até uma hora para aparecer.
  >
  >
* **P:  Como posso filtrar os relatórios de gerenciamento de senhas?**

  > **R:** Para filtrar os relatórios de gerenciamento de senhas, selecione a pequena lupa até a extrema direita dos rótulos de coluna, próximo à parte superior do relatório. Se desejar fazer uma filtragem mais avançada, você poderá baixar o relatório no Excel e criar uma tabela dinâmica.
  >
  >
* **P: Qual é o número máximo de eventos que são armazenados nos relatórios de gerenciamento de senhas?**

  > **R:** Até 75.000 eventos de redefinição de senha ou de registro de redefinição de senha são armazenados nos relatórios de gerenciamento de senha, abrangendo de volta até 30 dias. Estamos trabalhando para expandir esse número para incluir mais eventos.
  >
  >
* **P:  Qual a distância dos relatórios de gerenciamento de senhas?**

  > **R:** Os relatórios de gerenciamento de senhas mostram as operações ocorridas nos últimos 30 dias. Por enquanto, se você precisar arquivar esses dados, poderá baixar os relatórios periodicamente e salvá-los em um local separado.
  >
  >
* **P:  Há um número máximo de linhas que podem aparecer nos relatórios de gerenciamento de senhas?**

  > **R:** Sim. Um máximo de 75.000 linhas pode aparecer em qualquer um dos relatórios de gerenciamento de senha, sejam eles mostrados na interface do usuário ou baixados.
  >
  >
* **P:  Há uma API para acessar os dados de redefinição de senha ou de relatório de registro?**

  > **R:** Sim. Para saber como você pode acessar o fluxo de dados de relatório de redefinição de senha, consulte [saiba como acessar eventos de relatório de redefinição de senha](https://msdn.microsoft.com/library/azure/mt126081.aspx#BKMK_SsprActivityEvent)programaticamente.
  >
  >

## <a name="password-writeback"></a>Repetição de escrita da palavra-passe

* **P:  Como funciona o Write-back de senha em segundo plano?**

  > **R:** Consulte o artigo [como funciona o Write-back de senha](howto-sspr-writeback.md) para obter uma explicação do que acontece quando você habilita o Write-back de senha e como os dados fluem pelo sistema de volta ao seu ambiente local.
  >
  >
* **P:  Quanto tempo o Write-back de senha leva para funcionar? Há um atraso de sincronização como há com a sincronização de hash de senha?**

  > **R:** O Write-back de senha é instantâneo. É um pipeline síncrono que funciona fundamentalmente diferentemente da sincronização de hash de senha. O Write-back de senha permite que os usuários obtenham comentários em tempo real sobre o sucesso da operação de alteração ou redefinição de senha. O tempo médio para um write-back bem-sucedido de uma senha é inferior a 500 ms.
  >
  >
* **P:  Se minha conta local estiver desabilitada, como a minha conta de nuvem e o acesso serão afetados?**

  > **R:** Se sua ID local estiver desabilitada, sua ID de nuvem e acesso também serão desabilitados no próximo intervalo de sincronização por meio de Azure AD Connect. Por padrão, essa sincronização é a cada 30 minutos.
  >
  >
* **P:  Se minha conta local for restrita por uma política de senha de Active Directory local, o SSPR obedecerá essa política quando eu alterar minha senha?**

  > **R:** Sim, o SSPR depende e segue a política de senha de Active Directory local. Essa política inclui a diretiva de senha de domínio Active Directory típica, bem como as políticas de senha definidas e refinadas que são destinadas a um usuário.
  >
  >
* **P:  Para quais tipos de contas o Write-back de senha funciona?**

  > **R:** O Write-back de senha funciona para contas de usuário que são sincronizadas do Active Directory local para o Azure AD, incluindo usuários federados, sincronizados com hash de senha e autenticação de passagem.
  >
  >
* **P:  O Write-back de senha impõe as políticas de senha do meu domínio?**

  > **R:** Sim. O Write-back de senha impõe a duração da senha, o histórico, a complexidade, os filtros e qualquer outra restrição que você possa colocar em vigor em senhas em seu domínio local.
  >
  >
* **P:  O Write-back de senha é seguro?  Como eu posso ter certeza de que não serei invadido?**

  > **R:** Sim, o Write-back de senha é seguro. Para ler mais sobre as várias camadas de segurança implementadas pelo serviço de write-back de senha, confira a seção [segurança de write-back](concept-sspr-writeback.md#password-writeback-security) de senha no artigo [visão geral do write-back de senha](howto-sspr-writeback.md) .
  >
  >

## <a name="next-steps"></a>Passos Seguintes

* [Como posso concluir uma implementação com êxito da SSPR?](howto-sspr-deployment.md)
* [Repor ou alterar a palavra-passe](../user-help/active-directory-passwords-update-your-own-password.md)
* [Registar-se na reposição personalizada de palavra-passe](../user-help/active-directory-passwords-reset-register.md)
* [Tem alguma pergunta sobre licenciamento?](concept-sspr-licensing.md)
* [Que dados são utilizados pela SSPR e que dados devem ser preenchidos por si para os seus utilizadores?](howto-sspr-authenticationdata.md)
* [Que métodos de autenticação estão disponíveis para os utilizadores?](concept-sspr-howitworks.md#authentication-methods)
* [Quais são as opções de política da SSPR?](concept-sspr-policy.md)
* [O que é a repetição de escrita de palavras-passe e por que me deve interessar?](howto-sspr-writeback.md)
* [Como posso comunicar a atividade da SSPR?](howto-sspr-reporting.md)
* [Quais são todas as opções na SSPR e o que significam?](concept-sspr-howitworks.md)
* [Creio que algo está a funcionar incorretamente. Como posso resolver problemas da SSPR?](active-directory-passwords-troubleshoot.md)
