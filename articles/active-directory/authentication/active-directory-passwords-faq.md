---
title: FAQ - de reposição de palavra-passe self-service, Azure Active Directory
description: Perguntas mais frequentes sobre a palavra-passe self-service do Azure AD repor
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
ms.openlocfilehash: dcb825a193071d263d800dafdd6296246eb74aec
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57878198"
---
# <a name="password-management-frequently-asked-questions"></a>Perguntas mais frequentes de gestão de palavras-passe

Seguem-se algumas perguntas mais frequentes (FAQ) para todos os aspetos relacionados com a reposição de palavra-passe.

Se tiver alguma pergunta geral sobre o Azure Active Directory (Azure AD) e self-service Repor palavra-passe (SSPR) que não foi respondida aqui, pode perguntar à Comunidade para assistência no [fórum do Azure AD](https://social.msdn.microsoft.com/Forums/en-US/home?forum=WindowsAzureAD). Membros da Comunidade incluem engenheiros, gerentes de produto, MVPs e outros profissionais de TI.

Nestas perguntas frequentes é dividida nas secções seguintes:

* [Registo de reposição de perguntas sobre a palavra-passe](#password-reset-registration)
* [Perguntas sobre a reposição de palavra-passe](#password-reset)
* [Perguntas sobre a alteração de palavra-passe](#password-change)
* [Perguntas sobre os relatórios de gestão de palavra-passe](#password-management-reports)
* [Perguntas sobre a repetição de escrita de palavra-passe](#password-writeback)

## <a name="password-reset-registration"></a>Registo de reposição de palavra-passe

* **P:  Os meus utilizadores podem registar a seus próprios dados de reposição de palavra-passe?**

  > **R:** Sim. Desde que a reposição de palavra-passe está ativada e eles são licenciados, os usuários podem ir para o portal de registo de reposição de palavra-passe (https://aka.ms/ssprsetup) para registar as informações de autenticação. Os utilizadores também podem registar através do painel de acesso (https://myapps.microsoft.com). Para se registar através do painel de acesso, que precisam para selecionar a sua imagem do perfil, selecione **perfil**e, em seguida, selecione a **registar na reposição de palavra-passe** opção.
  >
  >
* **P:  Se ativar a palavra-passe reposta para um grupo e, em seguida, optar por ativá-la para todos os utilizadores são meus utilizadores necessário registrar novamente?**

  > **R:** Não. Os utilizadores que tiveram preenchido dados de autenticação não são necessários para voltar a registar.
  >
  >
* **P:  Pode definir os dados de reposições de palavra-passe em nome dos meus utilizadores?**

  > **R:** Sim, pode fazê-lo com o Azure AD Connect, PowerShell, o [portal do Azure](https://portal.azure.com), ou o Centro de administração do Office 365. Para obter mais informações, consulte [reposição de dados utilizados por palavra-passe self-service do Azure AD](howto-sspr-authenticationdata.md).
  >
  >
* **P:  Pode sincronizar dados para as perguntas de segurança no local?**

  > **R:** Não, isso não seja possível atualmente.
  >
  >
* **P:  Os meus utilizadores registar dados de forma que outros utilizadores não podem ver estes dados?**

  > **R:** Sim. Quando os utilizadores registem portal de registo de reposição de dados utilizando a palavra-passe, os dados são salvos nos campos de autenticação privada que são visíveis apenas para os administradores globais e o utilizador.
  >
  >
* **P:  Os meus utilizadores tem de estar registados antes de poderem utilizar a reposição de palavra-passe?**

  > **R:** Não. Se definir informações suficientes autenticação em nome deles, os utilizadores não tenham de se registar. Funciona a reposição de palavra-passe, desde que tenha formatado corretamente os dados armazenados nos campos apropriados no diretório.
  >
  >
* **P:  Pode sincronizar ou definir o telefone de autenticação, e-mail de autenticação ou campos de telefone de autenticação alternativo em nome dos meus utilizadores?**

  > **R:** Os campos que podem ser definidas por um Administrador Global são definidos no artigo [requisitos de dados de SSPR](howto-sspr-authenticationdata.md).
  >
  >
* **P:  Como é que o portal de registo determinar quais as opções para mostrar os meus utilizadores?**

  > **R:** O portal de registo de reposição de palavra-passe mostra apenas as opções que tiver ativado para os seus utilizadores. Estas opções estão localizadas sob o **política de reposição de palavra-passe do utilizador** secção do seu diretório **configurar** separador. Por exemplo, se não ativar a perguntas de segurança, em seguida, os utilizadores não serão possível se registrar para essa opção.
  >
  >
* **P:  Quando um utilizador é considerado registado?**

  > **R:** Um utilizador é considerado registrado para SSPR, quando eles registou, pelo menos, o **número de métodos necessários para repor** uma palavra-passe que tiver definido no [portal do Azure](https://portal.azure.com).
  >
  >

## <a name="password-reset"></a>Reposição de palavras-passe

* **P:  Evitar a utilizadores de várias tentativas para repor uma palavra-passe num curto período de tempo?**

  > **R:** Sim, existem recursos de segurança integrados para protegê-lo contra utilização indevida de reposição de palavra-passe. 
  >
  > Os utilizadores podem experimentar apenas cinco tentativas de reposição de palavra-passe num período de 24 horas antes de eles estão bloqueados durante 24 horas. 
  >
  > Os utilizadores podem experimentar validar um número de telefone, enviar uma SMS ou validar perguntas de segurança e respostas apenas cinco vezes dentro de uma hora antes de eles estão bloqueados durante 24 horas. 
  >
  > Os utilizadores podem enviar um e-mail um máximo de 10 vezes dentro de um período de dez minutos antes de eles estão bloqueados durante 24 horas.
  >
  > Os contadores são repostos quando um utilizador repõe a palavra-passe.
  >
  >
* **P:  Quanto deve esperar para receber um e-mail, SMS ou chamada telefónica de reposição de palavra-passe?**

  > **R:** E-mails, mensagens SMS e as chamadas de telefone deverá ser entregue em menos de um minuto. O caso de normal é de 5 a 20 segundos.
  > Se não receber a notificação neste período de tempo:
  > * Verifique a pasta de lixo.
  > * Verifique que o número ou o e-mail que está a ser contactado é o esperado.
  > * Verifique se os dados de autenticação no diretório estão corretamente formatado, por exemplo, + 1 4255551234 ou *usuário\@contoso.com*. 
* **P:  Que idiomas são suportados pela reposição de palavra-passe?**

  > **R:** A palavra-passe de reposição da interface do Usuário, mensagens SMS e chamadas de voz são localizados nos mesmos idiomas que são suportados no Office 365.
  >
  >
* **P:  Que partes da experiência de reposição de palavra-passe obterem com a marca quando eu defini o organizacional itens no meu diretório de imagem corporativa do separador Configurar?**

  > **R:** O portal de reposição de palavra-passe mostra o logótipo da sua organização e permite-lhe configurar a ligação "Contacte o administrador" para apontar para um URL ou e-mail personalizado. Todos os e-mails são enviados por reposição de palavra-passe incluem o logótipo da sua organização, cores e nome no corpo da mensagem de e-mail e é personalizado a partir as definições para esse nome específico.
  >
  >
* **P:  Como posso instruir os meus utilizadores sobre onde ir para repor as palavras-passe?**

  > **R:** Experimente algumas das sugestões na nossa [implementação de SSPR](howto-sspr-deployment.md#sample-communication) artigo.
  >
  >
* **P:  Pode utilizar esta página de um dispositivo móvel?**

  > **R:** Sim, esta página funciona em dispositivos móveis.
  >
  >
* **P:  Suporta desbloquear contas locais do Active Directory quando os utilizadores repor as palavras-passe?**

  > **R:** Sim. Quando um utilizador repõe a palavra-passe, se a repetição de escrita de palavra-passe tiver sido implementada através do Azure AD Connect, a conta desse usuário é desbloqueada automaticamente quando eles repor a palavra-passe.
  >
  >
* **P:  Como posso integrar diretamente na minha experiência do usuário desktop início de sessão de reposição de palavra-passe?**

  > **R:** Se for um cliente do Azure AD Premium, pode instalar o Microsoft Identity Manager sem custos adicionais e implementar a solução de reposição de palavra-passe no local.
  >
  >
* **P:  Pode definir perguntas de segurança diferentes para diferentes regiões?**

  > **R:** Não, isso não seja possível atualmente.
  >
  >
* **P:  O número de perguntas posso configurar para a opção de autenticação de perguntas de segurança?**

  > **R:** Pode configurar até 20 perguntas de segurança personalizadas no [portal do Azure](https://portal.azure.com).
  >
  >
* **P:  O tempo que é possível perguntas de segurança?**

  > **R:** Perguntas de segurança podem ter 3 a 200 carateres.
  >
  >
* **P:  O tempo que é possível as respostas às perguntas de segurança?**

  > **R:** As respostas podem ser 3 a 40 carateres de comprimento.
  >
  >
* **P:  São duplicadas respostas a perguntas de segurança rejeitadas?**

  > **R:** Sim, estamos rejeitar duplicadas respostas a perguntas de segurança.
  >
  >
* **P:  Pode um usuário Registre-se a mesma pergunta de segurança mais de uma vez?**

  > **R:** Não. Depois de um usuário registra uma pergunta específica, eles não é possível registar-se para essa pergunta uma segunda vez.
  >
  >
* **P:  É possível definir um limite mínimo de perguntas de segurança para o registo e reposição?**

  > **R:** Sim, um limite pode ser definido para o registo e outro para a reposição. Três a cinco perguntas de segurança podem ser necessárias para o registo, e três a cinco perguntas podem ser necessárias para a reposição.
  >
  >
* **P:  Configurei meu política para exigir que os utilizadores a utilizar perguntas de segurança para reposição, mas os administradores do Azure parecem ser configuradas de forma diferente.**

  > **R:** Este é o comportamento esperado. A Microsoft impõe uma política de reposição de palavra-passe predefinida forte de duas portas para qualquer função de administrador do Azure. Isto impede que os administradores utilizar perguntas de segurança. Pode encontrar mais informações sobre esta política no [políticas de palavra-passe e restrições no Azure Active Directory](concept-sspr-policy.md) artigo.
  >
  >
* **P:  Se um utilizador tiver mais do que o número máximo de perguntas necessárias para a reposição de registrado, como são as perguntas de segurança selecionadas durante a reposição?**

  > **R:** *N* número de perguntas de segurança é selecionado aleatoriamente entre o número total de perguntas que um utilizador registou para, onde *N* é a quantidade que está definida para o **número de perguntas necessárias para repor** opção. Por exemplo, se um utilizador registou cinco perguntas de segurança, mas apenas três são necessárias para repor uma palavra-passe, três dos cinco perguntas são selecionadas aleatoriamente e são apresentados na reposição. Para impedir que pergunta insistir, se o utilizador obtém as respostas às perguntas errado o processo de seleção começará de novo.
  >
  >
* **P:  O intervalo de tempo são o e-mail e SMS única códigos de acesso válidos?**

  > **R:** A duração da sessão para a reposição de palavra-passe é de 15 minutos. Desde o início da operação de reposição de palavra-passe, o utilizador tem 15 minutos para repor a palavra-passe. O e-mail e o código de acesso único SMS são inválidas após este período de tempo expira.
  >
  >
* **P:  Pode bloquear os utilizadores de repor a palavra-passe?**

  > **R:** Sim, se utilizar um grupo para ativar a SSPR, pode remover um utilizador individual do grupo que permite aos utilizadores para repor a palavra-passe. Se o utilizador for um Administrador Global irão reter a capacidade de repor a palavra-passe e este não pode ser desativada.
  >
  >

## <a name="password-change"></a>Alteração da palavra-passe

* **P:  Onde os meus utilizadores devem ir para alterar as palavras-passe?**

  > **R:** Os utilizadores podem alterar as palavras-passe em qualquer lugar Verão sua imagem do perfil ou o ícone, como no canto superior direito de seus [Office 365](https://portal.office.com) portal ou [painel de acesso](https://myapps.microsoft.com) experiências. Os utilizadores podem alterar as palavras-passe do [página de perfil do painel de acesso](https://account.activedirectory.windowsazure.com/r#/profile). Também podem ser pedido aos utilizadores para alterar as palavras-passe automaticamente na página de início de sessão do Azure AD, se as palavras-passe tem expirado. Por fim, os usuários podem navegar para o [portal de alteração de palavra-passe do Azure AD](https://account.activedirectory.windowsazure.com/ChangePassword.aspx) diretamente se desejarem alterar as palavras-passe.
  >
  >
* **P:  Meus usuários possam ser notificados no portal do Office quando expira a palavra-passe no local?**

  > **R:** Sim, isso é possível hoje mesmo se usar os serviços de Federação do Active Directory (AD FS). Se utilizar o AD FS, siga as instruções no [enviar afirmações de política de palavra-passe com o AD FS](https://technet.microsoft.com/windows-server-docs/identity/ad-fs/operations/configure-ad-fs-to-send-password-expiry-claims?f=255&MSPPError=-2147217396) artigo. Se utilizar a sincronização de hash de palavra-passe, isso não seja possível atualmente. Nós não são sincronizados políticas de palavra-passe de diretórios no local, pelo que não é possível para nós publicar notificações de expiração para a cloud experiências. Em ambos os casos, também é possível [notificar os usuários cujas senhas estejam prestes a expirar através do PowerShell](https://social.technet.microsoft.com/wiki/contents/articles/23313.notify-active-directory-users-about-password-expiry-using-powershell.aspx).
  >
  >
* **P:  Pode bloquear os utilizadores de alterar a palavra-passe?**

  > **R:** Para os utilizadores apenas na cloud, alterações de palavra-passe não podem ser bloqueadas. Para os utilizadores no local, pode definir o **o utilizador não é possível alterar a palavra-passe** opção selecionado. Os utilizadores selecionados não é possível alterar a palavra-passe.
  >
  >

## <a name="password-management-reports"></a>Relatórios de gestão de palavra-passe

* **P:  Quanto tempo demora para os dados sejam apresentados nos relatórios de gestão de palavra-passe?**

  > **R:** Os dados deverão aparecer nos relatórios de gestão de palavra-passe em 5 a 10 minutos. Em alguns casos, pode demorar até uma hora a aparecer.
  >
  >
* **P:  Como pode filtrar os relatórios de gestão de palavra-passe?**

  > **R:** Para filtrar os relatórios de gestão de palavra-passe, selecione a pequeno Lupa à extrema direita das etiquetas de coluna, junto à parte superior do relatório. Se quiser fazer filtragem mais rica, pode transferir o relatório para Excel e criar uma tabela dinâmica.
  >
  >
* **P: O que é o número máximo de eventos que são armazenados nos relatórios de gestão de palavra-passe?**

  > **R:** Até 75.000 reposição de palavra-passe ou a palavra-passe, eventos de registo de reposição são armazenados nos relatórios de gestão de palavra-passe, que abrange volta até 30 dias. Estamos a trabalhar para expandir este número para incluir mais eventos.
  >
  >
* **P:  Qual a amplitude faz os relatórios de gestão de palavra-passe?**

  > **R:** Os relatórios de gestão de palavra-passe mostram operações que ocorreram nos últimos 30 dias. Por agora, se precisar de arquivar estes dados, pode transferir os relatórios periodicamente e salvá-los num local separado.
  >
  >
* **P:  Existe um número máximo de linhas que podem aparecer em relatórios de gestão de palavra-passe?**

  > **R:** Sim. Um máximo de 75.000 linhas pode aparecer em qualquer uma dos relatórios de gestão de palavra-passe, se eles são mostrados na interface do Usuário ou que são transferidos.
  >
  >
* **P:  Existe uma API para aceder à reposição de palavra-passe ou o registo de dados de relatórios?**

  > **R:** Sim. Para saber como pode acessar a reposição de palavra-passe, fluxo de dados de relatórios, veja [Saiba como aceder a reposição de palavra-passe através de programação de eventos de relatório](https://msdn.microsoft.com/library/azure/mt126081.aspx#BKMK_SsprActivityEvent).
  >
  >

## <a name="password-writeback"></a>Repetição de escrita de palavras-passe

* **P:  Como funciona a repetição de escrita de palavra-passe em segundo plano?**

  > **R:** Consulte o artigo [como funciona a repetição de escrita de palavra-passe](howto-sspr-writeback.md) para obter uma explicação do que acontece quando ativar a repetição de escrita de palavra-passe e como os dados fluem através do sistema de volta para o seu ambiente no local.
  >
  >
* **P:  Quanto a repetição de escrita de palavra-passe demora a funcionar? É aí uma sincronização atrasar há com sincronização de hash de palavra-passe?**

  > **R:** Repetição de escrita de palavra-passe é instantânea. É um pipeline síncrono que funciona de forma fundamentalmente diferente do que a sincronização de hash de palavra-passe. Repetição de escrita de palavra-passe permite aos utilizadores obter comentários em tempo real sobre o sucesso da sua reposição de palavra-passe ou altere a operação. O tempo médio para uma repetição de escrita com êxito de uma palavra-passe é em 500 ms.
  >
  >
* **P:  Se a minha conta no local é desativada, como é minha conta na cloud e acesso afetados?**

  > **R:** Se o seu ID de locais está desativada, o ID de cloud e acesso também serão desativadas no próximo intervalo de sincronização através do Azure AD Connect. Por predefinição, esta sincronização é 30 minutos.
  >
  >
* **P:  Se a minha conta no local é restrita por uma política de palavra-passe do Active Directory no local, SSPR obedecem esta política quando altero a minha palavra-passe?**

  > **R:** Sim, SSPR se baseia em e cumpre a política de palavra-passe do Active Directory no local. Esta política inclui a política de palavra-passe de domínio do Active Directory típica, bem como quaisquer políticas de palavra-passe definida e detalhadas que são direcionadas para um utilizador.
  >
  >
* **P:  Que tipos de repetição de escrita de palavra-passe de faz de contas de trabalho para?**

  > **R:** Funciona de repetição de escrita de palavra-passe para contas de utilizador que são sincronizadas do Active Directory no local ao Azure AD, incluindo federado, utilizadores de Autentication pass-through e sincronizados de hash de palavra-passe.
  >
  >
* **P:  Repetição de escrita de palavra-passe impõe as políticas de palavra-passe do meu domínio?**

  > **R:** Sim. Repetição de escrita de palavra-passe impõe a idade de palavra-passe, histórico, complexidade, filtros e qualquer outra restrição, que pode colocar no lugar em palavras-passe no seu domínio local.
  >
  >
* **P:  Repetição de escrita de palavra-passe é seguro?  Como posso ser-se de que eu não invadido?**

  > **R:** Sim, a repetição de escrita de palavra-passe é segura. Para ler mais sobre as várias camadas de segurança implementadas pelo serviço de repetição de escrita de palavra-passe, consulte a [segurança de repetição de escrita de palavra-passe](concept-sspr-writeback.md#password-writeback-security) secção a [descrição geral de repetição de escrita de palavra-passe](howto-sspr-writeback.md) artigo.
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
