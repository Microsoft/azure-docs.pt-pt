---
title: Reset de senha de autosserviço de resolução de problemas - Diretório Ativo Azure
description: Resolução de problemas De teia a palavra-passe autosserviço azure
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: troubleshooting
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: sahenry
ms.custom: seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1224aef044c558147f4c7234f6514d0de2f09e0c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82143984"
---
# <a name="troubleshoot-self-service-password-reset"></a>Troubleshoot self-service password reset (Resolver problemas com a reposição personalizada de palavra-passe)

Está a ter algum problema com o reset de senha de autosserviço do Azure Ative Directory (Azure AD)? As seguintes informações podem ajudá-lo a pôr as coisas a funcionar novamente.

## <a name="troubleshoot-self-service-password-reset-errors-that-a-user-might-see"></a>Problemas de autosserviço palavra-passe repor erros que um utilizador pode ver

| Erro | Detalhes | Detalhes técnicos |
| --- | --- | --- |
| TenantSSPRFlagDisabled = 9 | Lamentamos que não possa repor a sua palavra-passe neste momento porque o seu administrador desativou o reset da palavra-passe para a sua organização. Não há mais nenhuma ação que possa tomar para resolver esta situação. Contacte o seu administrador e peça-lhes que ativem esta funcionalidade. Para saber mais, consulte [ajuda, esqueci-me da minha senha azure AD.](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-update-your-own-password#common-problems-and-their-solutions) | SSPR_0009: Detetámos que o reset da palavra-passe não foi ativado pelo seu administrador. Contacte o seu administrador e peça-lhes que ativem o reset da palavra-passe para a sua organização. |
| WritebackNotEnabled = 10 |Lamentamos que não possa redefinir a sua palavra-passe neste momento porque o seu administrador não permitiu um serviço necessário para a sua organização. Não há mais nenhuma ação que possa tomar para resolver esta situação. Contacte o seu administrador e peça-lhes que verifiquem a configuração da sua organização. Para saber mais sobre este serviço necessário, consulte a escrita da [palavra-passe configurando](howto-sspr-writeback.md). | SSPR_0010: Detetámos que a redação da palavra-passe não foi ativada. Contacte o seu administrador e peça-lhes que acedam à reescrita da palavra-passe. |
| SsprNotEnabledInUserPolicy = 11 | Lamentamos que não possa redefinir a sua palavra-passe neste momento porque o seu administrador não configurao o reset da palavra-passe para a sua organização. Não há mais nenhuma ação que possa tomar para resolver esta situação. Contacte o seu administrador e peça-lhes que configurem o reset da palavra-passe. Para saber mais sobre a configuração de reset de palavra-passe, consulte [Quickstart: Reset de palavra-passe autosserviço Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-getting-started). | SSPR_0011: A sua organização não definiu uma política de redefinição de palavra-passe. Contacte o seu administrador e peça-lhes que definam uma política de redefinição de palavra-passe. |
| UserNotLicensed = 12 | Lamentamos, não pode redefinir a sua senha neste momento porque faltam licenças necessárias à sua organização. Não há mais nenhuma ação que possa tomar para resolver esta situação. Por favor contacte o seu administrador e peça-lhes que verifiquem a sua atribuição de licença. Para saber mais sobre o licenciamento, consulte [os requisitos de licenciamento para o reset da palavra-passe de autosserviço Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-licensing). | SSPR_0012: A sua organização não dispõe das licenças necessárias para a redefinição da palavra-passe. Por favor contacte o seu administrador e peça-lhes que revejam as atribuições de licença. |
| UserNotMemberOfScopedAccessGroup = 13 | Lamentamos que não possa redefinir a sua palavra-passe neste momento porque o seu administrador não configurou a sua conta para utilizar o reset da palavra-passe. Não há mais nenhuma ação que possa tomar para resolver esta situação. Contacte o seu administrador e peça-lhes que configurem a sua conta para reset de palavra-passe. Para saber mais sobre a configuração da conta para reset de palavra-passe, consulte [o reset da palavra-passe para os utilizadores](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-best-practices). | SSPR_0013: Não é membro de um grupo habilitado para repor a palavra-passe. Contacte a sua administração e solicite a adição ao grupo. |
| UserNotDevidamentlyConfigurado = 14 | Lamentamos, não pode redefinir a sua senha neste momento porque faltam informações necessárias na sua conta. Não há mais nenhuma ação que possa tomar para resolver esta situação. Contacte-o e peça-lhes que reponham a sua palavra-passe para si. Depois de ter acesso à sua conta novamente, precisa de registar as informações necessárias. Para registar informações, siga os passos no [Registo para redefinir a palavra-passe de autosserviço.](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-reset-register) | SSPR_0014: São necessárias informações adicionais de segurança para redefinir a sua palavra-passe. Para prosseguir, contacte o seu administrador e peça-lhes que repora a sua palavra-passe. Depois de ter acesso à sua conta, https://aka.ms/ssprsetuppode registar informações adicionais de segurança em . O seu administrador pode adicionar informações adicionais de segurança à sua conta seguindo os passos em set e ler dados de [autenticação para redefinir a palavra-passe](howto-sspr-authenticationdata.md). |
| OnPremisesAdminActionRequired = 29 | Lamentamos, não podemos redefinir a sua palavra-passe neste momento por causa de um problema com a configuração de redefinição de senha da sua organização. Não há mais nenhuma ação que possa tomar para resolver esta situação. Por favor, contacte o seu administrador e peça-lhes que investiguem. Para saber mais sobre o problema potencial, consulte a reescrita da [palavra-passe de Troubleshoot](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-troubleshoot#troubleshoot-password-writeback). | SSPR_0029: Não podemos redefinir a sua palavra-passe devido a um erro na configuração no local. Por favor, contacte o seu administrador e peça-lhes que investiguem. |
| OnPremisesConnectivityError = 30 | Lamentamos, não podemos redefinir a sua senha neste momento por causa de problemas de conectividade com a sua organização. Não há nenhuma ação a tomar agora, mas o problema pode ser resolvido se tentar de novo mais tarde. Se o problema persistir, contacte o seu administrador e peça-lhes que investiguem. Para saber mais sobre problemas de conectividade, consulte a conectividade de [redação de palavras-passe de Troubleshoot](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-troubleshoot#troubleshoot-password-writeback-connectivity). | SSPR_0030: Não podemos redefinir a sua palavra-passe devido a uma má ligação com o seu ambiente no local. Contacte o seu administrador e peça-lhes que investiguem.|

## <a name="troubleshoot-the-password-reset-configuration-in-the-azure-portal"></a>Resolução de problemas da configuração de reset da palavra-passe no portal Azure

| Erro | Solução |
| --- | --- |
| Não vejo a secção de **reset** de password sintetizando em Azure AD no portal Azure. | Isto pode acontecer se não tiver uma licença Azure AD atribuída ao administrador que executa a operação. <br> <br> Atribuir uma licença à conta de administrador em questão. Pode seguir os passos no [Signo, verificar e resolver problemas com](../users-groups-roles/licensing-groups-assign.md#step-1-assign-the-required-licenses) o artigo de licenças.|
| Não vejo uma opção de configuração em particular. | Muitos elementos da UI estão escondidos até serem necessários. Tente ativar todas as opções que deseja ver. |
| Não vejo o separador **de integração no local.** | Esta opção só fica visível se tiver descarregado o Azure AD Connect e tiver configurado a redação da palavra-passe. Para mais informações, consulte [Iniciar-se com o Azure AD Connect utilizando as definições expressas](../hybrid/how-to-connect-install-express.md). |

## <a name="troubleshoot-password-reset-reporting"></a>Relatório de redefinição de palavra-passe de resolução de problemas

| Erro | Solução |
| --- | --- |
| Não vejo nenhum tipo de atividade de gestão de passwords na categoria de evento de auditoria de **gestão de passwords self-service.** | Isto pode acontecer se não tiver uma licença Azure AD atribuída ao administrador que executa a operação. <br> <br> Pode resolver este problema atribuindo uma licença à conta de administrador em questão. Siga os passos no [Atribuir, verifique e resolva problemas com](../users-groups-roles/licensing-groups-assign.md#step-1-assign-the-required-licenses) o artigo de licenças. |
| As inscrições dos utilizadores mostram várias vezes. | Atualmente, quando um utilizador se regista, registamos cada peça de dados individuais que está registada como um evento separado. <br> <br> Se quiser agregar estes dados e tiver maior flexibilidade na forma como os pode ver, pode descarregar o relatório e abrir os dados como uma tabela de pivôs no Excel.

## <a name="troubleshoot-the-password-reset-registration-portal"></a>Resolução de problemas no portal de registo de reset de palavra-passe

| Erro | Solução |
| --- | --- |
| O diretório não está habilitado para reset de palavra-passe. **O seu administrador não lhe permitiu utilizar esta funcionalidade.** | Mude a **palavra-passe de autosserviço para** a bandeira ativada para **Selected** ou **All** e, em seguida, selecione **Guardar**. |
| O utilizador não tem uma licença Azure AD atribuída. **O seu administrador não lhe permitiu utilizar esta funcionalidade.** | Isto pode acontecer se não tiver uma licença Azure AD atribuída ao administrador que executa a operação. <br> <br> Pode resolver este problema atribuindo uma licença à conta de administrador em questão. Siga os passos no [Atribuir, verifique e resolva problemas com](../users-groups-roles/licensing-groups-assign.md#step-1-assign-the-required-licenses) o artigo de licenças.|
| Há um erro a processar o pedido. | Isto pode ser causado por muitos problemas, mas geralmente este erro é causado por uma falha de serviço ou um problema de configuração. Se vir este erro e afetar o seu negócio, contacte o suporte da Microsoft para obter assistência adicional. |

## <a name="troubleshoot-the-password-reset-portal"></a>Resolução de problemas no portal de reset da palavra-passe

| Erro | Solução |
| --- | --- |
| O diretório não está habilitado para reset de palavra-passe. | Mude a **palavra-passe de autosserviço para** a bandeira ativada para **Selected** ou **All** e, em seguida, selecione **Guardar**. |
| O utilizador não tem uma licença Azure AD atribuída. | Isto pode acontecer se não tiver uma licença Azure AD atribuída ao administrador que executa a operação. <br> <br> Pode resolver este problema se atribuir uma licença à conta de administrador em questão. Siga os passos no [Atribuir, verifique e resolva problemas com](../users-groups-roles/licensing-groups-assign.md#step-1-assign-the-required-licenses) o artigo de licenças. |
| O diretório está habilitado para reset de palavra-passe, mas o utilizador tem informações de autenticação em falta ou mal formadas. | Antes de prosseguir, certifique-se de que o utilizador formou corretamente os dados de contacto no ficheiro no diretório. Para mais informações, consulte dados [utilizados pelo reset da palavra-passe autosserviço Azure AD](howto-sspr-authenticationdata.md). |
| O diretório está habilitado para repor a palavra-passe, mas o utilizador tem apenas um pedaço de dados de contacto no ficheiro quando a apólice é definida para exigir dois métodos de verificação. | Antes de prosseguir, certifique-se de que o utilizador dispõe de pelo menos dois métodos de contacto devidamente configurados. Um exemplo é ter um número de telemóvel *e* um número de telefone do escritório. |
| O diretório está ativado para redefinir a palavra-passe e o utilizador está devidamente configurado, mas o utilizador não pode ser contactado. | Isto pode ser o resultado de um erro de serviço temporário ou se houver dados de contacto incorretos que não podemos detetar corretamente. <br> <br> Se o utilizador esperar 10 segundos, "tente novamente" e "contacte o administrador" as ligações. Se o utilizador selecionar "tente novamente", tente novamente a chamada. Se o utilizador selecionar "contacte o seu administrador", envia um email de formulário aos seus administradores solicitando um reset de palavra-passe para essa conta de utilizador. |
| O utilizador nunca recebe a palavra-passe redefinir SMS ou chamada telefónica. | Isto pode ser o resultado de um número de telefone mal formado no diretório. Certifique-se de que o número de telefone está no formato "+ccc xxxyyzzzzXeeee". <br> <br> O reset da palavra-passe não suporta extensões, mesmo que especifique uma no diretório. As extensões são retiradas antes da chamada ser despachada. Utilize um número sem extensão ou integre a extensão no número de telefone da sua bolsa privada (PBX). |
| O utilizador nunca recebe o e-mail de reset da palavra-passe. | A causa mais comum para este problema é que a mensagem é rejeitada por um filtro de spam. Verifique a pasta de spam, lixo ou itens apagados para o e-mail. <br> <br> Certifique-se também de que está a verificar a conta de e-mail correta para a mensagem. |
| Estabena uma política de redefinição de palavra-passe, mas quando uma conta de administração usa o reset da palavra-passe, essa política não é aplicada. | A Microsoft gere e controla a política de redefinição de palavra-passe do administrador para garantir o mais alto nível de segurança. |
| O utilizador está impedido de tentar uma redefinição da palavra-passe demasiadas vezes num dia. | Implementamos um mecanismo automático de estrangulamento para impedir que os utilizadores tentem redefinir as suas palavras-passe demasiadas vezes num curto espaço de tempo. O estrangulamento ocorre quando: <br><ul><li>O utilizador tenta validar um número de telefone cinco vezes numa hora.</li><li>O utilizador tenta utilizar o portão de perguntas de segurança cinco vezes numa hora.</li><li>O utilizador tenta redefinir uma palavra-passe para a mesma conta de utilizador cinco vezes numa hora.</li></ul>Para corrigir este problema, instrua o utilizador a esperar 24 horas após a última tentativa. O utilizador pode então redefinir a sua palavra-passe. |
| O utilizador vê um erro ao validar o seu número de telefone. | Este erro ocorre quando o número de telefone introduzido não corresponde ao número de telefone do ficheiro. Certifique-se de que o utilizador está a introduzir o número de telefone completo, incluindo a área e o código do país, quando tentar utilizar um método baseado no telefone para redefinir a palavra-passe. |
| Há um erro a processar o pedido. | Isto pode ser causado por muitos problemas, mas geralmente este erro é causado por uma falha de serviço ou um problema de configuração. Se vir este erro e afetar o seu negócio, contacte o suporte da Microsoft para obter assistência adicional. |
| Violação da política no local | A palavra-passe não cumpre a política de senhas de diretório ativo no local. |
| A palavra-passe não cumpre a política fuzzy | A palavra-passe utilizada aparece na lista de [senhas proibidas](https://docs.microsoft.com/azure/active-directory/authentication/concept-password-ban-bad#how-are-passwords-evaluated) e pode não ser utilizada. |

## <a name="troubleshoot-password-writeback"></a>Redação da palavra-passe de resolução de problemas

| Erro | Solução |
| --- | --- |
| O serviço de reset de palavra-passe não inicia no local. O erro 6800 aparece no registo de eventos da máquina Azure AD Connect. <br> <br> Após o embarque, a autenticação por via-passe ou os utilizadores sincronizados com palavra-passe não podem redefinir as suas palavras-passe. | Quando a reescrita da palavra-passe está ativada, o motor sincronizado chama a biblioteca de writeback para executar a configuração (onboarding) comunicando com o serviço de embarque em nuvem. Quaisquer erros encontrados durante o embarque ou durante o início do ponto final da Windows Communication Foundation (WCF) para a redação de palavras-passe resulta em erros no registo do evento, na sua máquina Azure AD Connect. <br> <br> Durante o reinício do serviço Azure AD Sync (ADSync), se a redação estiver configurada, o ponto final do WCF começa. Mas, se o arranque do ponto final falhar, vamos registar o evento 6800 e deixar o serviço de sincronização arrancar. A presença deste evento significa que o ponto final de redação da palavra-passe não foi iniciado. Os detalhes do registo do evento para este evento 6800, juntamente com as entradas de registo de eventos geradas pelo componente PasswordResetService, indicam por que não consegue ligar o ponto final. Reveja estes erros de registo de eventos e tente reiniciar o Azure AD Connect se a reescrita da palavra-passe ainda não estiver a funcionar. Se o problema persistir, tente desativar e, em seguida, reativar a reescrita da palavra-passe.
| Quando um utilizador tenta redefinir uma palavra-passe ou desbloquear uma conta com a reescrita de palavra-passe ativada, a operação falha. <br> <br> Além disso, você vê um evento no registo de eventos Azure AD Connect que contém: "O Motor de Sincronização devolveu um erro hr=800700CE, mensagem=O nome de ficheiro ou extensão é demasiado longo" após a operação de desbloqueio ocorrer. | Encontre a conta Ative Directory para Azure AD Connect e reset a palavra-passe de modo a que contenha não mais de 256 caracteres. Em seguida, abra o Serviço de **Sincronização** a partir do menu **Iniciar.** Navegue nos **Conectores** e encontre o **Conector de Diretório Ativo**. Selecione-o e, em seguida, selecione **Propriedades**. Navegue na página **credenciais** e introduza a nova senha. Selecione **OK** para fechar a página. |
| No último passo do processo de instalação do Azure AD Connect, vê-se um erro que indica que a reescrita da palavra-passe não pôde ser configurada. <br> <br> O registo de eventos da aplicação Azure AD Connect contém erro 32009 com o texto "Error getting auth token". | Este erro ocorre nos dois seguintes casos: <br><ul><li>Especificou uma palavra-passe incorreta para a conta de administrador global especificada no início do processo de instalação do Azure AD Connect.</li><li>Tentou utilizar um utilizador federado para a conta de administrador global especificada no início do processo de instalação do Azure AD Connect.</li></ul> Para corrigir este problema, certifique-se de que não está a utilizar uma conta federada para o administrador global que especificou no início do processo de instalação. Certifique-se também de que a palavra-passe especificada está correta. |
| O registo de eventos da máquina Azure AD Connect contém um erro 32002 que é lançado através do execução passwordResetService. <br> <br> O erro diz: "Error Connecting to ServiceBus. O provedor simbólico não foi capaz de fornecer um símbolo de segurança. | O seu ambiente no local não é capaz de se ligar ao ponto final do Azure Service Bus na nuvem. Este erro é normalmente causado por uma regra de firewall que bloqueia uma ligação de saída a uma determinada porta ou endereço web. Consulte os [pré-requisitos](../hybrid/how-to-connect-install-prerequisites.md) de conectividade para mais informações. Depois de ter atualizado estas regras, reinicie a máquina Azure AD Connect e a reescrita da palavra-passe deve voltar a funcionar. |
| Depois de trabalhar durante algum tempo, os utilizadores federados, pass-through ou os utilizadores sincronizados com palavra-passe não conseguem redefinir as suas palavras-passe. | Em alguns casos raros, o serviço de redação de palavra-passe pode não reiniciar quando o Azure AD Connect tiver reiniciado. Nestes casos, em primeiro lugar, verifique se a recompra de palavras-passe parece estar ativada no local. Pode verificar utilizando o assistente de ligação AD Azure ou powerShell (consulte a secção HowTos anterior). Se a funcionalidade parecer ativada, tente ativar ou desativar novamente a funcionalidade através do UI ou da PowerShell. Se isto não funcionar, tente desinstalar e reinstalar completamente o Azure AD Connect. |
| Os utilizadores federados, pass-through ou de visualização de palavras-passe sincronizados que tentam redefinir as suas palavras-passe vêem um erro depois de tentarem enviar a sua palavra-passe. O erro indica que houve um problema de serviço. <br ><br> Além deste problema, durante as operações de reset de passwords, poderá ver um erro que o agente de gestão foi impedido de aceder nos registos de eventos no local. | Se vir estes erros no registo de eventos, confirme que a conta do Ative Directory Management Agent (ADMA) especificada no assistente no momento da configuração tem as permissões necessárias para a reescrita de palavra-passe. <br> <br> Após esta permissão ser dada, pode levar até uma hora `sdprop` para as permissões escorrerem através da tarefa de fundo no controlador de domínio (DC). <br> <br> Para que a palavra-passe seja redefinida, a permissão tem de ser carimbada no descritor de segurança do objeto utilizador cuja palavra-passe está a ser redefinida. Até que esta permissão apareça no objeto do utilizador, o reset da palavra-passe continua a falhar com uma mensagem negada de acesso. |
| Os utilizadores federados, pass-through ou utilizadores sincronizados com palavra-passe que tentam redefinir as suas palavras-passe, vêem um erro depois de enviarem a sua palavra-passe. O erro indica que houve um problema de serviço. <br> <br> Além deste problema, durante as operações de reset de passwords, poderá ver um erro nos registos de eventos do serviço Azure AD Connect indicando um erro de "Objeto não foi encontrado". | Este erro indica geralmente que o motor sincronizado não consegue encontrar o objeto do utilizador no espaço do conector Azure AD ou no metaverso ligado (MV) ou no objeto de espaço do conector Azure AD. <br> <br> Para resolver este problema, certifique-se de que o utilizador está de facto sincronizado desde as instalações até à AD Azure através da atual instância de Azure AD Connect e inspecione o estado dos objetos nos espaços do conector e mV. Confirme que o objeto Ative Directory Certificate Services (AD CS) está ligado ao objeto MV através da regra "Microsoft.InfromADUserAccountEnabled.xxx".|
| Os utilizadores federados, pass-through ou utilizadores sincronizados com palavra-passe que tentam redefinir as suas palavras-passe vêem um erro depois de enviarem a sua palavra-passe. O erro indica que houve um problema de serviço. <br> <br> Além deste problema, durante as operações de reset de passwords, poderá ver um erro nos registos de eventos do serviço Azure AD Connect que indica que existe um erro de "Múltiplas correspondências encontradas". | Isto indica que o motor sincronizado detetou que o objeto MV está ligado a mais de um objeto AD CS através de "Microsoft.InfromADUserAccountEnabled.xxx". Isto significa que o utilizador tem uma conta ativada em mais de uma floresta. Este cenário não é suportado para a reescrita de palavra-passe. |
| As operações de senha falham com um erro de configuração. O registo do evento de aplicação contém o erro Azure AD Connect 6329 com o texto "0x8023061f (A operação falhou porque a sincronização da palavra-passe não está ativada neste Agente de Gestão)". | Este erro ocorre se a configuração Azure AD Connect for alterada para adicionar uma nova floresta de Diretório Ativo (ou para remover e ler uma floresta existente) depois de a funcionalidade de redação de palavra-passe já ter sido ativada. As operações de senha para utilizadores nestas florestas recentemente adicionadas falham. Para corrigir o problema, desative e, em seguida, volte a ativar a funcionalidade de redação da palavra-passe após a conclusão das alterações de configuração da floresta. |

## <a name="password-writeback-event-log-error-codes"></a>Códigos de erro de registo de eventos de redação de palavras-passe

A melhor prática quando se desafiam problemas com a reescrita de palavra-passe é inspecionar o registo do evento de aplicação, na sua máquina Azure AD Connect. Este registo de eventos contém eventos de duas fontes de interesse para a repreensão de palavras-passe. A fonte PasswordResetService descreve operações e problemas relacionados com o funcionamento da reescrita da palavra-passe. A fonte ADSync descreve operações e problemas relacionados com a definição de senhas no seu ambiente de Diretório Ativo.

### <a name="if-the-source-of-the-event-is-adsync"></a>Se a fonte do evento for ADSync

| Código | Nome ou mensagem | Descrição |
| --- | --- | --- |
| 6329 | FIANÇA: MMS (4924) 0x80230619: "Uma restrição impede que a palavra-passe seja alterada para a atual especificada." | Este evento ocorre quando o serviço de redação de palavra-passe tenta definir uma palavra-passe no seu diretório local que não cumpre a idade da palavra-passe, histórico, complexidade ou requisitos de filtragem do domínio. <br> <br> Se tiver uma idade mínima de senha e tiver alterado recentemente a palavra-passe dentro dessa janela de tempo, não poderá alterar novamente a palavra-passe até atingir a idade especificada no seu domínio. Para efeitos de teste, a idade mínima deve ser fixada para 0. <br> <br> Se tiver requisitos de histórico de palavra-passe ativados, então deve selecionar uma palavra-passe que não tenha sido utilizada nos últimos *Tempos N,* onde *N* é a definição de histórico de palavra-passe. Se selecionar uma palavra-passe que tenha sido usada nos últimos *N* vezes, então verá uma falha neste caso. Para efeitos de teste, o histórico da palavra-passe deve ser definido para 0. <br> <br> Se tiver requisitos de complexidade de senhas, todos eles são aplicados quando o utilizador tenta alterar ou redefinir uma palavra-passe. <br> <br> Se tiver filtros de senha ativados e um utilizador selecionar uma palavra-passe que não satisfaz os critérios de filtragem, então o reset ou alteração do funcionamento falha. |
| 6329 | MMS(3040): admaexport.cpp(2837): O servidor não contém o controlo da política de passwords LDAP. | Este problema ocorre se LDAP_SERVER_POLICY_HINTS_OID controlo (1.2.840.113556.1.4.2066) não estiver ativado nos DCs. Para utilizar a função de redação de palavra-passe, tem de ativar o controlo. Para isso, os DCs devem estar no Windows Server 2008R2 ou mais tarde. |
| HR 8023042 | O Motor de Sincronização devolveu um erro hr=80230402, mensagem=Uma tentativa de obter um objeto falhado porque há entradas duplicadas com a mesma âncora. | Este erro ocorre quando o mesmo ID do utilizador é ativado em vários domínios. Um exemplo é se estiver a sincronizar as florestas de conta e recursos e tiver o mesmo ID de utilizador presente e habilitado em cada floresta. <br> <br> Este erro também pode ocorrer se utilizar um atributo de âncora não único, como um pseudónimo ou UPN, e dois utilizadores partilham o mesmo atributo de âncora. <br> <br> Para resolver este problema, certifique-se de que não tem utilizadores duplicados dentro dos seus domínios e que utiliza um atributo de âncora único para cada utilizador. |

### <a name="if-the-source-of-the-event-is-passwordresetservice"></a>Se a origem do evento for PasswordResetService

| Código | Nome ou mensagem | Descrição |
| --- | --- | --- |
| 31001 | PasswordResetStart | Este evento indica que o serviço no local detetou um pedido de reset de palavra-passe para uma autenticação federada, pass-through ou utilizador sincronizado com palavra-passe que provém da nuvem. Este evento é o primeiro evento em todas as operações de redefinição de palavra-passe. |
| 31002 | PasswordResetSuccess | Este evento indica que um utilizador selecionou uma nova senha durante uma operação de reset de palavra-passe. Determinámos que esta palavra-passe satisfaz os requisitos de senha corporativa. A palavra-passe foi redigida com sucesso para o ambiente de Diretório Ativo local. |
| 31003 | PasswordResetFail | Este evento indica que um utilizador selecionou uma palavra-passe e a palavra-passe chegou com sucesso ao ambiente no local. Mas quando tentámos definir a palavra-passe no ambiente do Diretório Ativo local, ocorreu uma falha. Esta falha pode acontecer por várias razões: <br><ul><li>A palavra-passe do utilizador não satisfaz a idade, histórico, complexidade ou requisitos de filtro para o domínio. Para resolver este problema, crie uma nova senha.</li><li>A conta de serviço ADMA não dispõe das permissões adequadas para definir a nova senha na conta de utilizador em questão.</li><li>A conta do utilizador está num grupo protegido, como o domain ou o grupo de administração da empresa, que proíbe as operações de conjunto de passwords.</li></ul>|
| 31004 | OnboardingEventStart | Este evento ocorre se ativar a reescrita de palavra-passe com o Azure AD Connect e começamos a embarcar na sua organização para o serviço web de redação de palavra-passe. |
| 31005 | OnboardingEventSuccess | Este evento indica que o processo de embarque foi bem sucedido e que a capacidade de redação de palavra-passe está pronta a ser usada. |
| 31006 | AlterarPasswordStart | Este evento indica que o serviço no local detetou um pedido de alteração de senha para uma autenticação federada, pass-through ou utilizador sincronizado com palavra-passe que provém da nuvem. Este evento é o primeiro evento em todas as operações de redação de palavras-passe. |
| 31007 | Alterar Sucesso password | Este evento indica que um utilizador selecionou uma nova senha durante uma operação de alteração de password, determinámos que a palavra-passe satisfaz os requisitos de senha corporativa, e que a palavra-passe foi redigida com sucesso para o ambiente de Diretório Ativo local. |
| 31008 | Alterar Falha de password | Este evento indica que um utilizador selecionou uma palavra-passe e que a palavra-passe chegou com sucesso ao ambiente no local, mas quando tentámos definir a palavra-passe no ambiente do Ative Diretório local, ocorreu uma falha. Esta falha pode acontecer por várias razões: <br><ul><li>A palavra-passe do utilizador não satisfaz a idade, histórico, complexidade ou requisitos de filtro para o domínio. Para resolver este problema, crie uma nova senha.</li><li>A conta de serviço ADMA não dispõe das permissões adequadas para definir a nova senha na conta de utilizador em questão.</li><li>A conta do utilizador está num grupo protegido, como domínio ou administração da empresa, que não permitem operações de conjunto de passwords.</li></ul> |
| 31009 | ResetUserPasswordByAdminStart | O serviço no local detetou um pedido de reset de palavra-passe para um utilizador federado, pass-through ou utilizador sincronizado com palavra-passe originário do administrador em nome de um utilizador. Este evento é o primeiro evento em todas as operações de redefinição de palavras-passe iniciadas por um administrador. |
| 31010 | ResetUserPasswordByAdminSuccess | O administrador selecionou uma nova senha durante uma operação de reset de palavra-passe iniciada pela administração. Determinámos que esta palavra-passe satisfaz os requisitos de senha corporativa. A palavra-passe foi redigida com sucesso para o ambiente de Diretório Ativo local. |
| 31011 | ResetUserPasswordByAdminFail | O administrador selecionou uma palavra-passe em nome de um utilizador. A senha chegou com sucesso ao ambiente no local. Mas quando tentámos definir a palavra-passe no ambiente do Diretório Ativo local, ocorreu uma falha. Esta falha pode acontecer por várias razões: <br><ul><li>A palavra-passe do utilizador não satisfaz a idade, histórico, complexidade ou requisitos de filtro para o domínio. Experimente uma nova senha para resolver este problema.</li><li>A conta de serviço ADMA não dispõe das permissões adequadas para definir a nova senha na conta de utilizador em questão.</li><li>A conta do utilizador está num grupo protegido, como domínio ou administração da empresa, que não permitem operações de conjunto de passwords.</li></ul>  |
| 31012 | OffboardingEventStart | Este evento ocorre se desativar a reescrita de palavra-passe com o Azure AD Connect e indicar que começamos a offboarding da sua organização para o serviço web de redação de palavras-passe. |
| 31013| OffboardingEventSuccess| Este evento indica que o processo de offboarding foi bem sucedido e que a capacidade de recompra de palavras-passe foi desativada com sucesso. |
| 31014| OffboardingEventFail| Este evento indica que o processo de offboarding não foi bem sucedido. Isto pode dever-se a um erro de permissões na nuvem ou na conta de administrador no local especificada durante a configuração. O erro também pode ocorrer se estiver a tentar usar um administrador global de nuvem federada ao desativar a reescrita da palavra-passe. Para corrigir este problema, verifique as suas permissões administrativas e certifique-se de que não está a utilizar uma conta federada enquanto configura a capacidade de recompra de palavra-passe.|
| 31015| WriteBackServiceIniciado| Este evento indica que o serviço de redação de palavra-passe começou com sucesso. Está pronto para aceitar pedidos de gestão de passwords da nuvem.|
| 31016| WriteBackServiceParou| Este evento indica que o serviço de redação de palavra-passe parou. Quaisquer pedidos de gestão de passwords da nuvem não serão bem sucedidos.|
| 31017| AuthTokenSuccess| Este evento indica que conseguimos obter um sinal de autorização para a administração global especificada durante a configuração azure AD Connect para iniciar o processo de offboarding ou onboarding.|
| 31018| Sucesso da Criação de Teclado| Este evento indica que criámos com sucesso a chave de encriptação de palavras-passe. Esta chave é usada para encriptar palavras-passe da nuvem a serem enviadas para o seu ambiente no local.|
| 32000| Erro Desconhecido| Este evento indica que ocorreu um erro desconhecido durante uma operação de gestão de passwords. Veja o texto de exceção no evento para mais detalhes. Se estiver com problemas, tente desativar e, em seguida, reativar a reescrita da palavra-passe. Se isso não ajudar, inclua uma cópia do registo do seu evento juntamente com o ID de rastreio especificado internamente ao seu engenheiro de suporte.|
| 32001| Error de serviço| Este evento indica que houve um erro de ligação ao serviço de reset de palavra-passe em nuvem. Este erro ocorre geralmente quando o serviço no local não foi capaz de se ligar ao serviço web de redefinição de palavra-passe.|
| 32002| ServiceBusError| Este evento indica que houve um erro de ligação à instância do autocarro de serviço do seu inquilino. Isto pode acontecer se estiver estocando ligações de saída no seu ambiente no local. Verifique a sua firewall para se certificar de que https://ssprdedicatedsbprodncu.servicebus.windows.netpermite ligações sobre tCP 443 e, em seguida, tente novamente. Se ainda tiver problemas, tente desativar e, em seguida, reativar a reescrita da palavra-passe.|
| 32003| Erro de validação de entrada| Este evento indica que a entrada passada para o nosso serviço web API foi inválida. Tente a operação de novo.|
| 32004| DecryptionError| Este evento indica que houve um erro desencriptando a senha que chegou da nuvem. Isto pode dever-se a um desfasamento entre o serviço de nuvem e o ambiente no local. Para resolver este problema, desative e, em seguida, volte a ativar a reescrita da palavra-passe no seu ambiente no local.|
| 32005| Erro de configuração| Durante o embarque, guardamos informações específicas do inquilino num ficheiro de configuração no seu ambiente no local. Este evento indica que houve um erro a salvar este ficheiro ou que quando o serviço foi iniciado, houve um erro de leitura do ficheiro. Para corrigir este problema, tente desativar e, em seguida, reativar a reescrita da palavra-passe para forçar uma reescrita do ficheiro de configuração.|
| 32007| OnBoardingConfigUpdateError| Durante o embarque, enviamos dados da nuvem para o serviço de redefinição de palavra-passe no local. Esses dados são então escritos num ficheiro em memória antes de serem enviados para o serviço de sincronização a serem armazenados de forma segura no disco. Este evento indica que há um problema em escrever ou atualizar esses dados na memória. Para corrigir este problema, tente desativar e, em seguida, reativar a reescrita da palavra-passe para forçar uma reescrita deste ficheiro de configuração.|
| 32008| Erro de validação| Este evento indica que recebemos uma resposta inválida do serviço web de redefinição de palavra-passe. Para corrigir este problema, tente desativar e, em seguida, reativar a reescrita da palavra-passe.|
| 32009| AuthTokenError| Este evento indica que não conseguimos obter um sinal de autorização para a conta de administrador global especificada durante a configuração do Azure AD Connect. Este erro pode ser causado por um mau nome de utilizador ou palavra-passe especificado para a conta de administração global. Este erro também pode ocorrer se a conta de administração global especificada for federada. Para corrigir este problema, refaça a configuração com o nome de utilizador e a palavra-passe corretos e certifique-se de que o administrador é uma conta gerida (apenas na nuvem ou sincronizada com palavra-passe).|
| 32010| CryptoError| Este evento indica que houve um erro que gerou a chave de encriptação da palavra-passe ou desencripta uma palavra-passe que chega do serviço de nuvem. Este erro indica provavelmente um problema com o seu ambiente. Veja os detalhes do registo do seu evento para saber mais sobre como resolver este problema. Também pode tentar desativar e, em seguida, reativar o serviço de redação de palavra-passe.|
| 32011| Erro de serviço de embarque| Este evento indica que o serviço no local não conseguiu comunicar adequadamente com o serviço web de redefinição de palavra-passe para iniciar o processo de embarque. Isto pode acontecer como resultado de uma regra de firewall ou se houver algum problema em obter um símbolo de autenticação para o seu inquilino. Para corrigir este problema, certifique-se de que não está a bloquear ligações de saída sobre tCP https://ssprdedicatedsbprodncu.servicebus.windows.net443 e TCP 9350-9354 ou para . Também certifique-se de que a conta de administração da AD Azure que está a usar a bordo não é federada.|
| 32013| OffBoardingError| Este evento indica que o serviço no local não conseguiu comunicar adequadamente com o serviço web de redefinição de palavra-passe para iniciar o processo de offboarding. Isto pode acontecer como resultado de uma regra de firewall ou se houver algum problema em obter um sinal de autorização para o seu inquilino. Para resolver este problema, certifique-se de que não está a https://ssprdedicatedsbprodncu.servicebus.windows.netbloquear ligações de saída acima de 443 ou a , e que a conta de administração do Azure Ative Directory que está a usar para offboard não é federada.|
| 32014| ServiceBusWarning| Este evento indica que tivemos que voltar a tentar ligar-nos à instância do autocarro de serviço do seu inquilino. Em condições normais, isso não deve ser uma preocupação, mas se você vir este evento muitas vezes, considere verificar a sua ligação de rede ao Service Bus, especialmente se for uma ligação de alta latência ou baixa largura de banda.|
| 32015| ReportServiceHealthError| Para monitorizar a saúde do seu serviço de redação de palavras-passe, enviamos dados de batimentos cardíacos para o nosso serviço web de redefinição de palavra-passe a cada cinco minutos. Este evento indica que houve um erro ao enviar esta informação de saúde de volta para o serviço web da nuvem. Esta informação de saúde não inclui quaisquer dados pessoais, e é puramente um batimento cardíaco e estatísticas básicas de serviço para que possamos fornecer informações sobre o estado do serviço na nuvem.|
| 33001| Erro adunknowno| Este evento indica que houve um erro desconhecido devolvido pelo Ative Directy. Verifique o registo do registo do servidor Azure AD Connect para obter mais informações sobre os eventos a partir da fonte ADSync.|
| 33002| Erro adusernotfounderror| Este evento indica que o utilizador que está a tentar redefinir ou alterar uma palavra-passe não foi encontrado no diretório no local. Este erro pode ocorrer quando o utilizador foi eliminado no local, mas não na nuvem. Este erro também pode ocorrer se houver um problema de sincronização. Verifique os registos de sincronização e os últimos detalhes da sincronização para obter mais informações.|
| 33003| ADMutliMatchError| Quando um pedido de reset ou alteração de palavra-passe origina-se da nuvem, utilizamos a âncora em nuvem especificada durante o processo de configuração do Azure AD Connect para determinar como ligar esse pedido a um utilizador no seu ambiente no local. Este evento indica que encontramos dois utilizadores no seu diretório no local com o mesmo atributo de âncora em nuvem. Verifique os registos de sincronização e os últimos detalhes da sincronização para obter mais informações.|
| 33004| Erro de Permissões ADPers| Este evento indica que a conta de serviço do Ative Directory Management Agent (ADMA) não tem as permissões adequadas na conta em questão para definir uma nova senha. Certifique-se de que a conta ADMA na floresta do utilizador redefiniu as permissões de senha em todos os objetos da floresta. Para obter mais informações sobre como definir as permissões, consulte o Passo 4: Configurar as permissões de Diretório Ativo apropriadas. Este erro também pode ocorrer quando o atributo adminCount do utilizador está definido para 1.|
| 33005| ADUserAccountDisabled| Este evento indica que tentámos redefinir ou alterar uma palavra-passe para uma conta que foi desativada no local. Ative a conta e tente novamente a operação.|
| 33006| ADUser AccountLockedOut| Este evento indica que tentámos redefinir ou alterar uma senha para uma conta que estava bloqueada no local. Os bloqueios podem ocorrer quando um utilizador tentou alterar ou redefinir a operação da palavra-passe demasiadas vezes num curto período de tempo. Desbloqueie a conta e tente a operação novamente.|
| 33007| AduserincorrectPassword| Este evento indica que o utilizador especificou uma palavra-passe de corrente incorreta ao executar uma operação de alteração de senha. Especifique a senha de corrente correta e tente novamente.|
| 33008| Erro de política de adpassword| Este evento ocorre quando o serviço de redação de palavra-passe tenta definir uma palavra-passe no seu diretório local que não cumpre a idade da palavra-passe, histórico, complexidade ou requisitos de filtragem do domínio. <br> <br> Se tiver uma idade mínima de senha e tiver alterado recentemente a palavra-passe dentro dessa janela de tempo, não poderá alterar novamente a palavra-passe até atingir a idade especificada no seu domínio. Para efeitos de teste, a idade mínima deve ser fixada para 0. <br> <br> Se tiver requisitos de histórico de palavra-passe ativados, então deve selecionar uma palavra-passe que não tenha sido utilizada nos últimos *Tempos N,* onde *N* é a definição de histórico de palavra-passe. Se selecionar uma palavra-passe que tenha sido usada nos últimos *N* vezes, então verá uma falha neste caso. Para efeitos de teste, o histórico da palavra-passe deve ser definido para 0. <br> <br> Se tiver requisitos de complexidade de senhas, todos eles são aplicados quando o utilizador tenta alterar ou redefinir uma palavra-passe. <br> <br> Se tiver filtros de senha ativados e um utilizador selecionar uma palavra-passe que não satisfaz os critérios de filtragem, então o reset ou alteração do funcionamento falha.|
| 33009| Erro de Configuração ADConfiguração| Este evento indica que houve um problema em escrever uma palavra-passe de volta para o seu diretório no local devido a um problema de configuração com Diretório Ativo. Verifique o registo de eventos da máquina Azure AD Connect para obter mensagens do serviço ADSync para obter mais informações sobre quais os erros ocorridos.|

## <a name="troubleshoot-password-writeback-connectivity"></a>Dificuldade em escrever a conectividade de reescrita de palavra-passe

Se estiver a sofrer interrupções de serviço com o componente de redação de palavra-passe do Azure AD Connect, aqui estão alguns passos rápidos que pode tomar para resolver este problema:

* [Confirmar a conectividade da rede](#confirm-network-connectivity)
* [Reiniciar o serviço Azure AD Connect Sync](#restart-the-azure-ad-connect-sync-service)
* [Desativar e reativar a funcionalidade de reescrita de palavra-passe](#disable-and-re-enable-the-password-writeback-feature)
* [Instale o mais recente lançamento do Azure AD Connect](#install-the-latest-azure-ad-connect-release)
* [Redação da palavra-passe de resolução de problemas](#troubleshoot-password-writeback)

Em geral, para recuperar o seu serviço da forma mais rápida, recomendamos que execute estes passos na ordem discutida anteriormente.

### <a name="confirm-network-connectivity"></a>Confirmar a conectividade da rede

O ponto mais comum de falha é que as portas de firewall e ou proxy e os intervalos de tempo estão incorretamente configurados. 

Para o Azure AD Connect versão 1.1.443.0 ou superior, é necessário aceder https de saída ao seguinte:

* \*.passwordreset.microsoftonline.com
* \*.servicebus.windows.net

Para obter mais granularidade, consulte a lista atualizada do [Microsoft Azure Datacenter IP Ranges](https://www.microsoft.com/download/details.aspx?id=41653) atualizada todas as quartas-feiras e posta em vigor na próxima segunda-feira.

Para mais informações, reveja os pré-requisitos de conectividade nos pré-requisitos para o artigo [Azure AD Connect.](../hybrid/how-to-connect-install-prerequisites.md)

> [!NOTE]
> O SSPR também pode falhar se as definições "Password never expire" ou "User can't change password" estão configuradas na conta em ADDS no local. 

### <a name="restart-the-azure-ad-connect-sync-service"></a>Reiniciar o serviço Azure AD Connect Sync

Para resolver problemas de conectividade ou outros problemas transitórios com o serviço, reinicie o serviço Azure AD Connect Sync:

1. Como administrador, selecione **Iniciar** o servidor executando O Azure AD Connect.
1. Introduza **serviços.msc** no campo de pesquisa e selecione **Enter**.
1. Procure a entrada **do Microsoft Azure AD Sync.**
1. Clique na entrada de serviço, selecione **Reiniciar**e, em seguida, aguarde que a operação termine.

   ![Reiniciar o serviço Azure AD Sync utilizando o GUI][Service restart]

Estes passos restabelecem a sua ligação com o serviço de nuvem e resolvem quaisquer interrupções que possa estar a sofrer. Se reiniciar o serviço ADSync não resolver o seu problema, recomendamos que tente desativar e, em seguida, volte a ativar a funcionalidade de redação da palavra-passe.

### <a name="disable-and-re-enable-the-password-writeback-feature"></a>Desativar e reativar a funcionalidade de reescrita de palavra-passe

Para resolver problemas de conectividade, desative e, em seguida, reative a funcionalidade de reescrita de palavra-passe:

   1. Como administrador, abra o assistente de configuração de ligação Azure AD.
   1. Em **Connect to Azure AD,** insira as suas credenciais de administração global Azure AD.
   1. Em **Connect to AD DS,** introduza as suas credenciais de administração dos Serviços de Domínio AD.
   1. Ao **identificar exclusivamente os seus utilizadores,** selecione o botão **Seguinte.**
   1. Nas **funcionalidades Opcionais,** limpe a caixa de verificação de recompra de **palavra-passe.**
   1. Selecione **Seguinte** através das restantes páginas de diálogo sem alterar nada até chegar à página **Ready to configurar.**
   1. Certifique-se de que a **página Ready to configurar** mostra a opção **de redação da Palavra-passe** como **desativada** e, em seguida, selecione o botão **Configurar** verde para comprometer as suas alterações.
   1. Em **Acabamento,** limpe a opção **Synchronize agora** e, em seguida, selecione **Terminar** para fechar o assistente.
   1. Reabra o assistente de configuração de ligação ad ad azure.
   1. Repita os passos 2-8, exceto se selecione a opção de recompra de **palavra-passe** na página **de funcionalidades Opcional** para reativar o serviço.

Estes passos restabelecem a sua ligação com o nosso serviço de nuvem e resolvem quaisquer interrupções que possa estar a sofrer.

Se desativar e, em seguida, reativar a funcionalidade de reescrita de palavra-passe não resolve o seu problema, recomendamos que volte a instalar o Azure AD Connect.

### <a name="install-the-latest-azure-ad-connect-release"></a>Instale o mais recente lançamento do Azure AD Connect

Reinstalar o Azure AD Connect pode resolver problemas de configuração e conectividade entre os nossos serviços na nuvem e o seu ambiente de Diretório Ativo local.

Recomendamos que execute este passo apenas depois de tentar os dois primeiros passos previamente descritos.

> [!WARNING]
> Se tiver personalizado as regras de sincronização fora da caixa, faça-as recuar antes de proceder à *atualização e, em seguida, reimplantá-las manualmente após a sua terminação.*

1. Descarregue a versão mais recente do Azure AD Connect a partir do [Microsoft Download Center](https://go.microsoft.com/fwlink/?LinkId=615771).
1. Como já instalou o Azure AD Connect, precisa de realizar uma atualização no local para atualizar a instalação do Azure AD Connect para a versão mais recente.
1. Execute o pacote descarregado e siga as instruções no ecrã para atualizar a sua máquina Azure AD Connect.

Os passos anteriores devem restabelecer a sua ligação com o nosso serviço de nuvem e resolver quaisquer interrupções que possa estar a sofrer.

Se a instalação da versão mais recente do servidor Azure AD Connect não resolver o seu problema, recomendamos que tente desativar e, em seguida, reativar a reescrita da palavra-passe como um passo final após a instalação da mais recente versão.

## <a name="verify-that-azure-ad-connect-has-the-required-permission-for-password-writeback"></a>Verifique se o Azure AD Connect tem a permissão necessária para a reescrita de palavra-passe

O Azure AD Connect requer a permissão de **palavra-passe** de reset do Diretório Ativo para efetuar a reescrita da palavra-passe. Para saber se o Azure AD Connect tem a permissão necessária para uma determinada conta de utilizador ative diretório no local, pode utilizar a funcionalidade de permissão eficaz do Windows:

1. Inicie sessão no servidor Azure AD Connect e inicie o Gestor de **Serviçode Sincronização** selecionando o Serviço de**Sincronização** **inicial** > .
1. Sob o separador **Conectores,** selecione o conector de **serviços** de domínio ativo no local e, em seguida, selecione **Propriedades**.  
   ![Gestor de Serviços de Sincronização mostrando como editar propriedades](./media/active-directory-passwords-troubleshoot/checkpermission01.png)  
  
1. Na janela pop-up, selecione **Connect to Ative Directory Forest** e tome nota da propriedade do nome **user.** Esta propriedade é a conta AD DS usada pelo Azure AD Connect para realizar sincronização de diretório. Para que o Azure AD Connect efetue a reescrita da palavra-passe, a conta AD DS deve ter redefinido a permissão de senha.  

   ![Encontrar o serviço de sincronização Conta de utilizador Ative Diretório](./media/active-directory-passwords-troubleshoot/checkpermission02.png) 
  
1. Inicie sessão num controlador de domínio no local e inicie a aplicação **Ative Directory Users and Computers.**
1. Selecione **'Ver'** e certifique-se de que a opção **Funcionalidades Avançadas** está ativada.  

   ![Utilizadores e computadores de diretório ativo mostram funcionalidades avançadas](./media/active-directory-passwords-troubleshoot/checkpermission03.png) 
  
1. Procure a conta de utilizador do Diretório Ativo que pretende verificar. Clique no nome da conta e selecione **Propriedades**.  
1. Na janela pop-up, vá ao separador **Segurança** e selecione **Advanced**.  
1. Nas **Definições avançadas** de segurança para a janela pop-up do administrador, aceda ao separador **De acesso Efetivo.**
1. **Selecione selecione um utilizador,** selecione a conta AD DS utilizada pelo Azure AD Connect (ver passo 3) e, em seguida, selecione **Ver acesso eficaz**.

   ![Separador de acesso eficaz mostrando a Conta de Sincronização](./media/active-directory-passwords-troubleshoot/checkpermission06.png) 
  
1. Desloque-se para baixo e procure a **palavra-passe Reset**. Se a entrada tiver uma marca de verificação, a conta AD DS tem permissão para redefinir a palavra-passe da conta de utilizador do Diretório Ativo selecionada.  

   ![Validando que a conta de sincronização tem a permissão de palavra-passe redefinida](./media/active-directory-passwords-troubleshoot/checkpermission07.png)  

## <a name="azure-ad-forums"></a>Fóruns azure ad

Se tiver uma pergunta geral sobre o Anúncio De Azure e o reset de senha de autosserviço, pode pedir ajuda à comunidade nos [fóruns da AD Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=WindowsAzureAD). Membros da comunidade incluem engenheiros, gestores de produtos, MVPs e outros profissionais de TI.

## <a name="contact-microsoft-support"></a>Contacte o Suporte da Microsoft

Se não encontrar a resposta para um problema, as nossas equipas de apoio estão sempre disponíveis para o ajudar ainda mais.

Para ajudá-lo corretamente, pedimos que forneça o máximo de detalhes possível ao abrir um caso. Estes detalhes incluem:

* **Descrição geral do erro**: Qual é o erro? Qual foi o comportamento que foi notado? Como podemos reproduzir o erro? Forneça o máximo de detalhes possível.
* **Página**: Em que página estava quando reparou no erro? Inclua o URL se for capaz e uma imagem da página.
* **Código de suporte**: Qual foi o código de suporte que foi gerado quando o utilizador viu o erro?
   * Para encontrar este código, reproduza o erro, edepois selecione a ligação de **código de suporte** na parte inferior do ecrã e envie ao engenheiro de suporte o GUID que resulta.

   ![Encontre o código de suporte na parte inferior do ecrã][Support code]

  * Se estiver numa página sem um código de suporte na parte inferior, selecione F12 e procure o SID e O CID e envie esses dois resultados para o engenheiro de suporte.
* **Data, hora e fuso horário**: Incluir a data e a hora *exatas com o fuso horário* em que ocorreu o erro.
* **ID do utilizador**: Quem foi o utilizador que viu o erro? Um exemplo é *o contoso.com do utilizador.\@*
   * É um utilizador federado?
   * Este é um utilizador de autenticação pass-through?
   * Este é um utilizador sincronizado com palavra-passe??
   * É um utilizador só para a nuvem?
* **Licenciamento**: O utilizador tem uma licença Azure AD atribuída?
* **Registo de eventos**de aplicação : Se estiver a utilizar a redação de palavra-passe e o erro estiver na sua infraestrutura no local, inclua uma cópia com fecho do registo do seu evento de aplicação a partir do servidor Azure AD Connect.

[Service restart]: ./media/active-directory-passwords-troubleshoot/servicerestart.png "Reiniciar o serviço Azure AD Sync"
[Support code]: ./media/active-directory-passwords-troubleshoot/supportcode.png "O código de suporte está localizado na parte inferior direita da janela"

## <a name="next-steps"></a>Passos seguintes

Os seguintes artigos fornecem informações adicionais sobre o reset de palavra-passe através do Azure AD:

* [Como posso concluir uma implementação com êxito da SSPR?](howto-sspr-deployment.md)
* [Redefinir ou alterar a sua palavra-passe](../user-help/active-directory-passwords-update-your-own-password.md)
* [Registar-se na reposição personalizada de palavra-passe](../user-help/active-directory-passwords-reset-register.md)
* [Tem alguma pergunta sobre licenciamento?](concept-sspr-licensing.md)
* [Que dados são utilizados pela SSPR e que dados devem ser preenchidos por si para os seus utilizadores?](howto-sspr-authenticationdata.md)
* [Que métodos de autenticação estão disponíveis para os utilizadores?](concept-sspr-howitworks.md#authentication-methods)
* [Quais são as opções de política da SSPR?](concept-sspr-policy.md)
* [O que é a repetição de escrita de palavras-passe e por que me deve interessar?](howto-sspr-writeback.md)
* [Como posso comunicar a atividade da SSPR?](howto-sspr-reporting.md)
* [Quais são todas as opções na SSPR e o que significam?](concept-sspr-howitworks.md)
* [Tenho uma pergunta que ainda não foi abordada](active-directory-passwords-faq.md)
