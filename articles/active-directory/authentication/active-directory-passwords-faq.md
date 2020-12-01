---
title: Palavra-passe de autosserviço redefiniu FAQ - Azure Ative Directory
description: Perguntas frequentes sobre o reset da palavra-passe de autosserviço AZure AD
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 07/20/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: b79eb842105734f0f6c145884d73f886a02dbbbc
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/30/2020
ms.locfileid: "96348163"
---
# <a name="self-service-password-reset-frequently-asked-questions"></a>Redefinição de senha de autosserviço frequentemente feita perguntas

Seguem-se algumas perguntas frequentes (FAQ) para todas as coisas relacionadas com o reset da palavra-passe de autosserviço.

Se tiver uma pergunta geral sobre o Azure Ative Directory (Azure AD) e o reset da palavra-passe de autosserviço (SSPR) que não seja respondida aqui, pode pedir assistência à comunidade no [Microsoft Q&Página de perguntas para o Azure Ative Directory](/answers/topics/azure-active-directory.html). Os membros da comunidade incluem engenheiros, gestores de produtos, MVPs e outros profissionais de TI.

Esta FAQ é dividida nas seguintes secções:

* [Perguntas sobre o registo de redefinição de passwords](#password-reset-registration)
* [Perguntas sobre reset de palavra-passe](#password-reset)
* [Perguntas sobre mudança de senha](#password-change)
* [Perguntas sobre relatórios de gestão de passwords](#password-management-reports)
* [Perguntas sobre o writeback de palavra-passe](#password-writeback)

## <a name="password-reset-registration"></a>Registo de reposição de palavra-passe

* **P: Os meus utilizadores podem registar os seus próprios dados de redefinição de password?**

  > **R:** Sim. Enquanto a palavra-passe estiver ativada e estiver licenciada, os utilizadores podem ir ao portal de registo de redefinição de passwords ( https://aka.ms/ssprsetup) para registar as suas informações de autenticação. Os utilizadores também podem inscrever-se através do Painel de Acesso ( Painel de https://myapps.microsoft.com) Acesso). Para se registarem através do Painel de Acesso, têm de selecionar a sua imagem de perfil, selecionar **Perfil** e, em seguida, selecionar o Registo para a opção **de reset da palavra-passe.**
  >
  > Se ativar [o registo combinado,](concept-registration-mfa-sspr-combined.md)os utilizadores podem registar-se tanto para a Autenticação Multi-Factor SSPR como para a Azure AD ao mesmo tempo.
* **P: Se eu permitir o reset da palavra-passe para um grupo e, em seguida, decidir capacitá-lo para todos os meus utilizadores são obrigados a re-registrar?**

  > **A:** Não. Os utilizadores que tenham preenchido os dados de autenticação não são obrigados a re-registar-se.
  >
  >
* **P: Posso definir dados de redefinição de palavra-passe em nome dos meus utilizadores?**

  > **A:** Sim, pode fazê-lo com o Azure AD Connect, o PowerShell, o [portal Azure,](https://portal.azure.com)ou com o [centro de administração Microsoft 365](https://admin.microsoft.com). Para obter mais informações, consulte [os dados utilizados pela Azure AD, redefinindo a palavra-passe de autosserviço](howto-sspr-authenticationdata.md).
  >
  >
* **P: Posso sincronizar dados para questões de segurança a partir do local?**

  > **A:** Não, isto não é possível hoje.
  >
  >
* **P: Os meus utilizadores podem registar dados de tal forma que outros utilizadores não possam ver estes dados?**

  > **R:** Sim. Quando os utilizadores registam dados utilizando o portal de registo de redefinição de palavra-passe, os dados são guardados em campos de autenticação privada que são visíveis apenas para administradores globais e para o utilizador.
  >
  >
* **P: Os meus utilizadores têm de estar registados antes de poderem utilizar o reset da palavra-passe?**

  > **A:** Não. Se definir informações de autenticação suficientes em seu nome, os utilizadores não têm de se registar. O reset da palavra-passe funciona desde que tenha formatado corretamente os dados armazenados nos campos apropriados no diretório.
  >
  >
* **P: Posso sincronizar ou definir o telefone de autenticação, o e-mail de autenticação ou os campos telefónicos de autenticação alternativa em nome dos meus utilizadores?**

  > **A:** Os campos que podem ser definidos por um Administrador Global são definidos nos requisitos de [dados SSPR](howto-sspr-authenticationdata.md)do artigo.
  >
  >
* **P: Como é que o portal de registo determina quais as opções para mostrar aos meus utilizadores?**

  > **A:** O portal de registo de redefinição de palavra-passe mostra apenas as opções que tem ativado para os seus utilizadores. Estas opções encontram-se na secção Política de **Redefinição de Palavra-Passe** do Utilizador do **separador Configurar** do seu diretório. Por exemplo, se não ativar questões de segurança, os utilizadores não poderão registar-se para essa opção.
  >
  >
* **P: Quando é considerado registado um utilizador?**

  > **A:** Um utilizador é considerado registado para SSPR quando registou pelo menos o **Número de métodos necessários para redefinir** uma palavra-passe que definiu no portal [Azure](https://portal.azure.com).
  >
  >

## <a name="password-reset"></a>Reposição de palavras-passe

* **P: Impede os utilizadores de várias tentativas de repor uma palavra-passe num curto espaço de tempo?**

  > **A:** Sim, existem funcionalidades de segurança incorporadas no reset da palavra-passe para protegê-la de uso indevido. 
  >
  > Os utilizadores podem tentar apenas cinco tentativas de reset de palavra-passe num período de 24 horas antes de serem bloqueados durante 24 horas. 
  >
  > Os utilizadores podem tentar validar um número de telefone, enviar um SMS ou validar perguntas de segurança e respostas apenas cinco vezes no prazo de uma hora antes de serem bloqueados durante 24 horas. 
  >
  > Os utilizadores podem enviar um e-mail no máximo 10 vezes num período de 10 minutos antes de serem bloqueados durante 24 horas.
  >
  > Os contadores são reiniciados assim que um utilizador reinicia a sua palavra-passe.
  >
  >
* **P: Quanto tempo devo esperar para receber um e-mail, SMS ou chamada telefónica a partir do reset da palavra-passe?**

  > **A:** Os e-mails, mensagens SMS e chamadas telefónicas devem chegar dentro de um minuto. O caso normal é de 5 a 20 segundos.
  > Se não receber a notificação neste período de tempo:
  > * Verifique a sua pasta de lixo.
  > * Verifique se o número ou e-mail que está a ser contactado é o que espera.
  > * Verifique se os dados de autenticação no diretório estão corretamente formatados, por exemplo, +1 4255551234 ou *\@ contoso.com do utilizador*. 
* **P: Que línguas são suportadas por reset de palavra-passe?**

  > **A:** A palavra-passe repôs uI, mensagens SMS e chamadas de voz localizadas nos mesmos idiomas que são suportados no Microsoft 365.
  >
  >
* **P: Que partes da experiência de reset da palavra-passe são marcadas quando eu configuo os itens de marca organizacional no separador de configuração do meu diretório?**

  > **A:** O portal de reset de palavra-passe mostra o logótipo da sua organização e permite-lhe configurar o link "Contacte o seu administrador" para apontar para um e-mail ou URL personalizado. Qualquer e-mail enviado por redefinição de senha inclui o logótipo, cores e nome da sua organização no corpo do e-mail, e é personalizado a partir das configurações para esse nome em particular.
  >
  >
* **P: Como posso educar os meus utilizadores sobre onde ir para redefinir as suas palavras-passe?**

  > **A:** Experimente algumas das sugestões no nosso artigo [de implantação da SSPR.](howto-sspr-deployment.md#plan-communications)
  >
  >
* **P: Posso usar esta página a partir de um dispositivo móvel?**

  > **A:** Sim, esta página funciona em dispositivos móveis.
  >
  >
* **P: Suporta desbloquear contas locais do Ative Directory quando os utilizadores reiniciam as suas palavras-passe?**

  > **R:** Sim. Quando um utilizador reinicia a sua palavra-passe, se a gravação da palavra-passe tiver sido implementada através do Azure AD Connect, a conta desse utilizador é automaticamente desbloqueada quando redefinir a sua palavra-passe.
  >
  >
* **P: Como posso integrar a palavra-passe reiniciada diretamente na experiência de entrada no ambiente de trabalho do meu utilizador?**

  > **A:** Se for um cliente Azure AD Premium, pode instalar o Microsoft Identity Manager sem custos adicionais e implementar a solução de reset da palavra-passe no local.
  >
  >
* **P: Posso definir diferentes questões de segurança para diferentes locais?**

  > **A:** Não, isto não é possível hoje.
  >
  >
* **P: Quantas perguntas posso configurar para a opção de autenticação de questões de segurança?**

  > **A:** Pode configurar até 20 questões de segurança personalizadas no [portal Azure](https://portal.azure.com).
  >
  >
* **P: Quanto tempo podem ser questões de segurança?**

  > **A:** As questões de segurança podem ter entre 3 a 200 caracteres.
  >
  >
* **P: Quanto tempo podem ser as respostas às questões de segurança?**

  > **A:** As respostas podem ter entre 3 a 40 caracteres.
  >
  >
* **P: As respostas duplicadas às questões de segurança são rejeitadas?**

  > **A:** Sim, rejeitamos respostas duplicadas a questões de segurança.
  >
  >
* **P: Pode um utilizador registar a mesma questão de segurança mais de uma vez?**

  > **A:** Não. Depois de um utilizador registar uma pergunta específica, não pode registar-se para essa pergunta uma segunda vez.
  >
  >
* **P: É possível estabelecer um limite mínimo de questões de segurança para o registo e reposição?**

  > **A:** Sim, um limite pode ser definido para o registo e outro para reset. Podem ser necessárias três a cinco questões de segurança para o registo, podendo ser necessárias três a cinco questões para o reset.
  >
  >
* **P: Configurei a minha política para exigir que os utilizadores usassem questões de segurança para reiniciar, mas os administradores do Azure parecem estar configurados de forma diferente.**

  > **A:** Este é o comportamento esperado. A Microsoft impõe uma política de reposição de palavra-passe predefinida forte de duas portas para qualquer função de administrador do Azure. Isto impede que os administradores utilizem questões de segurança. Pode encontrar mais informações sobre esta política nas políticas e restrições de Password no artigo [do Azure Ative Directory.](concept-sspr-policy.md)
  >
  >
* **P: Se um utilizador registou mais do que o número máximo de perguntas necessárias para reiniciar, como são selecionadas as questões de segurança durante o reset?**

  > **R:** *N São* selecionadas números de questões de segurança aleatoriamente fora do número total de perguntas que um utilizador registou, quando *N* é o valor definido para o Número de **perguntas necessárias para redefinir a** opção. Por exemplo, se um utilizador tiver registado cinco questões de segurança, mas apenas três forem necessárias para redefinir uma palavra-passe, três das cinco perguntas são selecionadas aleatoriamente e são apresentadas no reset. Para evitar a martelada de perguntas, se o utilizador obtiver as respostas às perguntas erradas, o processo de seleção começa de serviço.
  >
  >
* **P: Quanto tempo são válidos os códigos de e-mail e SMS uma vez?**

  > **A:** A vida útil da sessão para reset da palavra-passe é de 15 minutos. Desde o início da operação de reset da palavra-passe, o utilizador tem 15 minutos para redefinir a sua palavra-passe. O e-mail e a senha de SMS são válidos por 5 minutos durante a sessão de reset da palavra-passe.
  >
  >
* **P: Posso impedir os utilizadores de redefinirem a sua palavra-passe?**

  > **A:** Sim, se utilizar um grupo para ativar o SSPR, pode remover um utilizador individual do grupo que permite aos utilizadores redefinirem a sua palavra-passe. Se o utilizador for um Administrador Global, manterão a capacidade de redefinir a sua palavra-passe e esta não pode ser desativada.
  >
  >

## <a name="password-change"></a>Alteração da palavra-passe

* **P: Onde devem os meus utilizadores mudar as suas palavras-passe?**

  > **A:** Os utilizadores podem alterar as suas palavras-passe em qualquer lugar que vejam a sua imagem de perfil ou ícone, como no canto superior direito do seu portal [Office 365](https://portal.office.com) ou experiências [do Painel de Acesso.](https://myapps.microsoft.com) Os utilizadores podem alterar as suas palavras-passe a partir da [página 'Perfil do Painel de Acesso'.](https://account.activedirectory.windowsazure.com/r#/profile) Os utilizadores também podem ser solicitados a alterar as suas palavras-passe automaticamente na página de início de sação AZure AD se as suas palavras-passe tiverem expirado. Por fim, os utilizadores podem navegar diretamente no portal de alteração da [palavra-passe AZure AD](https://account.activedirectory.windowsazure.com/ChangePassword.aspx) se quiserem alterar as suas palavras-passe.
  >
  >
* **P: Os meus utilizadores podem ser notificados no portal do Office quando a sua senha no local expirar?**

  > **A:** Sim, isto é possível hoje se utilizar os Serviços da Federação de Diretórios Ativos (AD FS). Se utilizar AD FS, siga as instruções na política de senha Enviar por email o artigo [AD FS.](/windows-server/identity/ad-fs/operations/configure-ad-fs-to-send-password-expiry-claims?f=255&MSPPError=-2147217396) Se utilizar a sincronização de haxixe de palavra-passe, isso não é possível hoje em dia. Não sincronizamos as políticas de palavras-passe de diretórios no local, por isso não é possível publicar notificações de expiração para experiências em nuvem. Em qualquer dos casos, também é possível [notificar os utilizadores cujas palavras-passe estão prestes a expirar através do PowerShell](https://social.technet.microsoft.com/wiki/contents/articles/23313.notify-active-directory-users-about-password-expiry-using-powershell.aspx).
  >
  >
* **P: Posso impedir os utilizadores de alterarem a sua palavra-passe?**

  > **A:** Para utilizadores apenas na nuvem, as alterações de palavras-passe não podem ser bloqueadas. Para os utilizadores no local, pode definir o Utilizador não pode alterar a opção **de palavra-passe** para selecionado. Os utilizadores selecionados não podem alterar a sua palavra-passe.
  >
  >

## <a name="password-management-reports"></a>Relatórios de gestão de passwords

* **P: Quanto tempo demora a aparecer os dados nos relatórios de gestão de passwords?**

  > **A:** Os dados devem aparecer nos relatórios de gestão da palavra-passe em 5 a 10 minutos. Em alguns casos, pode levar até uma hora para aparecer.
  >
  >
* **P: Como posso filtrar os relatórios de gestão de passwords?**

  > **A:** Para filtrar os relatórios de gestão da palavra-passe, selecione a pequena lupa para a extrema-direita das etiquetas da coluna, perto do topo do relatório. Se quiser fazer uma filtragem mais rica, pode baixar o relatório para o Excel e criar uma mesa de pivô.
  >
  >
* **P: Qual é o número máximo de eventos armazenados nos relatórios de gestão de passwords?**

  > **A:** Até 75.000 eventos de registo de reset de palavra-passe ou de redefinição de palavra-passe são armazenados nos relatórios de gestão de passwords, que se estendem até 30 dias. Estamos a trabalhar para expandir este número para incluir mais eventos.
  >
  >
* **P: Até onde vão os relatórios de gestão de passwords?**

  > **A:** Os relatórios de gestão de passwords mostram as operações que ocorreram nos últimos 30 dias. Por enquanto, se precisar de arquivar estes dados, pode descarregar os relatórios periodicamente e guardá-los num local separado.
  >
  >
* **P: Existe um número máximo de linhas que podem aparecer nos relatórios de gestão de passwords?**

  > **R:** Sim. Um máximo de 75.000 linhas pode aparecer em qualquer um dos relatórios de gestão de passwords, quer sejam mostrados na UI ou descarregados.
  >
  >
* **P: Existe uma API para aceder aos dados de reset ou relatório de registo da palavra-passe?**

  > **R:** Sim. Para saber como pode aceder aos dados de reporte de redefinição de palavra-passe, consulte a [referência API DO API DO AZURE Log Analytics](/rest/api/loganalytics/)REST .
  >
  >

## <a name="password-writeback"></a>Repetição de escrita de palavras-passe

* **P: Como funciona a gravação de passwords nos bastidores?**

  > **A:** Veja o artigo Como a gravação da [palavra-passe funciona](./tutorial-enable-sspr-writeback.md) para uma explicação do que acontece quando ativa a gravação de passwords e como os dados fluem através do sistema de volta para o seu ambiente no local.
  >
  >
* **P: Quanto tempo demora a escrever a palavra-passe? Existe um atraso de sincronização como existe com a sincronização de haxixe de palavra-passe?**

  > **A:** A gravação da palavra-passe é instantânea. É um pipeline sincronizado que funciona fundamentalmente diferente da sincronização de hash de palavra-passe. O writeback de palavra-passe permite que os utilizadores obtenham feedback em tempo real sobre o sucesso da sua palavra-passe reiniciada ou operação de alteração. O tempo médio para uma gravação bem sucedida de uma palavra-passe é inferior a 500 ms.
  >
  >
* **P: Se a minha conta no local está desativada, como é que a minha conta na nuvem e o acesso são afetados?**

  > **A:** Se o seu ID no local estiver desativado, o seu ID de nuvem e acesso também serão desativado no intervalo de sincronização seguinte através do Azure AD Connect. Por predefinição, esta sincronização é a cada 30 minutos.
  >
  >
* **P: Se a minha conta no local estiver limitada por uma política de senhas de direção ativa no local, a SSPR obedece a esta política quando eu altero a minha palavra-passe?**

  > **A:** Sim, a SSPR baseia-se e cumpre a política de senha do Ative Directory no local. Esta política inclui a política típica de senha de domínio do Ative Directory, bem como quaisquer políticas de senhas definidas e finas que são direcionadas a um utilizador.
  >
  >
* **P: Para que tipo de contas funciona a gravação de passwords?**

  > **A:** O writeback de palavra-passe funciona para contas de utilizador sincronizadas desde o Ative Directy para Azure AD, incluindo utilizadores federados, de hash de palavra-passe sincronizados e Pass-Through Utilizadores de Autenticação.
  >
  >
* **P: A writeback de palavra-passe aplica as políticas de senha do meu domínio?**

  > **R:** Sim. A writeback de palavra-passe aplica a idade da palavra-passe, a história, a complexidade, os filtros e qualquer outra restrição que possa colocar em prática em senhas no seu domínio local.
  >
  >
* **P: A gravação da palavra-passe é segura?  Como posso ter certeza de que não serei hackeado?**

  > **A:** Sim, a gravação da palavra-passe é segura. Para ler mais sobre as múltiplas camadas de segurança implementadas pelo serviço de writeback de palavra-passe, consulte a secção de segurança de [writeback password](concept-sspr-writeback.md#password-writeback-security) no artigo de visão geral de [writeback da Palavra-passe.](./tutorial-enable-sspr-writeback.md)
  >
  >

## <a name="next-steps"></a>Passos seguintes

* [Como posso concluir uma implementação com êxito da SSPR?](howto-sspr-deployment.md)
* [Redefinir ou alterar a sua palavra-passe](../user-help/active-directory-passwords-update-your-own-password.md)
* [Registar-se na reposição personalizada de palavra-passe](../user-help/active-directory-passwords-reset-register.md)
* [Tem alguma pergunta sobre licenciamento?](concept-sspr-licensing.md)
* [Que dados são utilizados pela SSPR e que dados devem ser preenchidos por si para os seus utilizadores?](howto-sspr-authenticationdata.md)
* [Que métodos de autenticação estão disponíveis para os utilizadores?](concept-sspr-howitworks.md#authentication-methods)
* [Quais são as opções de política da SSPR?](concept-sspr-policy.md)
* [O que é a repetição de escrita de palavras-passe e por que me deve interessar?](./tutorial-enable-sspr-writeback.md)
* [Como posso comunicar a atividade da SSPR?](howto-sspr-reporting.md)
* [Quais são todas as opções na SSPR e o que significam?](concept-sspr-howitworks.md)
* [Acho que algo está partido. Como é que resolvo a SSPR?](./troubleshoot-sspr.md)