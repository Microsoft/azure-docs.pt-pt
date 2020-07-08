---
title: Resolução de problemas redefinição da palavra-passe de autosserviço - Azure Ative Directory
description: Resolução de problemas Azure AD autosserviço palavra-passe reposto
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
ms.openlocfilehash: 5ca3f4b1e1061b0ca86cc836849499b83ee055fe
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "83826524"
---
# <a name="troubleshoot-self-service-password-reset"></a>Troubleshoot self-service password reset (Resolver problemas com a reposição personalizada de palavra-passe)

Está a ter algum problema com o Azure Ative Directory (Azure AD) que reinicia a palavra-passe de autosserviço (SSPR)? As seguintes informações podem ajudá-lo a pôr as coisas a funcionar novamente.

## <a name="troubleshoot-self-service-password-reset-errors-that-a-user-might-see"></a>Resolução de problemas erros de redefinição de palavra-passe de autosserviço que um utilizador pode ver

| Erro | Detalhes | Detalhes técnicos |
| --- | --- | --- |
| InquilinoSSSPRFlagDisabled = 9 | Lamentamos que não possa redefinir a sua palavra-passe neste momento porque o seu administrador desativou a redefinição de password para a sua organização. Não há mais nenhuma ação que possa tomar para resolver esta situação. Entre em contato com o seu administrador e peça-lhe para ativar esta funcionalidade. Para saber mais, consulte [a Ajuda, esqueci-me da minha senha AD Azure.](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-update-your-own-password#common-problems-and-their-solutions) | SSPR_0009: Detetámos que o reset da palavra-passe não foi ativado pelo seu administrador. Entre em contato com o seu administrador e peça-lhes para ativar a redefinição de senha para a sua organização. |
| WritebackNotEnabled = 10 |Lamentamos que não possa redefinir a sua palavra-passe neste momento porque o seu administrador não permitiu um serviço necessário para a sua organização. Não há mais nenhuma ação que possa tomar para resolver esta situação. Por favor contacte o seu administrador e peça-lhes para verificarem a configuração da sua organização. Para saber mais sobre este serviço necessário, consulte configurar a gravação [da palavra-passe](howto-sspr-writeback.md). | SSPR_0010: Detetámos que a gravação da palavra-passe não foi ativada. Por favor contacte o seu administrador e peça-lhes para ativar a gravação de senha. |
| SsprNotEnabledInUserPolicy = 11 | Lamentamos que não possa redefinir a sua palavra-passe neste momento porque o seu administrador não configura a redefinição de senha para a sua organização. Não há mais nenhuma ação que possa tomar para resolver esta situação. Contacte o seu administrador e peça-lhes para configurar a redefinição da palavra-passe. Para saber mais sobre a configuração de reset da palavra-passe, consulte [Quickstart: Azure AD self-service reset](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-getting-started). | SSPR_0011: A sua organização não definiu uma política de reset de palavra-passe. Por favor contacte o seu administrador e peça-lhes que definam uma política de reset de palavra-passe. |
| UserNotLicensed = 12 | Lamentamos, não pode redefinir a sua senha neste momento porque faltam licenças necessárias à sua organização. Não há mais nenhuma ação que possa tomar para resolver esta situação. Por favor contacte o seu administrador e peça-lhes para verificarem a sua atribuição de licença. Para saber mais sobre o licenciamento, consulte [os requisitos de licenciamento para a Azure AD autosserviço de autosserviço reset](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-licensing). | SSPR_0012: A sua organização não tem as licenças necessárias para efetuar a redefinição da palavra-passe. Por favor contacte o seu administrador e peça-lhes que revejam as atribuições de licença. |
| UserNotMemberOfScopedAccessGroup = 13 | Lamentamos que não possa redefinir a sua palavra-passe neste momento porque o seu administrador não configura a sua conta para utilizar o reset da palavra-passe. Não há mais nenhuma ação que possa tomar para resolver esta situação. Por favor contacte o seu administrador e peça-lhes para configurar a sua conta para a redefinição da palavra-passe. Para saber mais sobre a configuração da conta para o reset da palavra-passe, consulte [o reset da palavra-passe para os utilizadores](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-best-practices). | SSPR_0013: Não é membro de um grupo ativado para reset de palavra-passe. Contacte o seu administrador e solicite a adição ao grupo. |
| UtilizadorNotProperly Configurado = 14 | Lamentamos, não pode redefinir a sua senha neste momento porque faltam informações necessárias na sua conta. Não há mais nenhuma ação que possa tomar para resolver esta situação. Por favor contacte-o e peça-lhes para redefinirem a sua palavra-passe. Depois de ter acesso à sua conta novamente, tem de registar as informações necessárias. Para registar informações, siga os passos no [Registo para o artigo de redefinição da palavra-passe de autosserviço.](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-reset-register) | SSPR_0014: São necessárias informações adicionais de segurança para redefinir a sua palavra-passe. Para prosseguir, contacte o seu administrador e peça-lhes que repuem a sua palavra-passe. Depois de ter acesso à sua conta, pode registar informações adicionais de segurança em https://aka.ms/ssprsetup . O seu administrador pode adicionar informações de segurança adicionais à sua conta seguindo os passos definidos [e ler dados de autenticação para reset de senha.](howto-sspr-authenticationdata.md) |
| OnPremisesAdminActionRequired = 29 | Lamentamos, não podemos redefinir a sua palavra-passe neste momento devido a um problema com a configuração de reset de password da sua organização. Não há mais nenhuma ação que possa tomar para resolver esta situação. Por favor contacte o seu administrador e peça-lhes para investigarem. Para saber mais sobre o problema potencial, consulte a [gravação da palavra-passe de resolução de problemas](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-troubleshoot#troubleshoot-password-writeback). | SSPR_0029: Não conseguimos redefinir a sua palavra-passe devido a um erro na configuração do seu local. Por favor contacte o seu administrador e peça-lhes para investigarem. |
| OnPremisesConnectivityError = 30 | Lamentamos, não podemos redefinir a sua senha neste momento por causa de problemas de conectividade com a sua organização. Neste momento, não há ação a tomar, mas o problema pode ser resolvido se tentarmos mais tarde. Se o problema persistir, contacte o seu administrador e peça-lhes que investiguem. Para saber mais sobre problemas de conectividade, consulte [a conectividade de writeback de palavras-passe de resolução de problemas](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-troubleshoot#troubleshoot-password-writeback-connectivity). | SSPR_0030: Não podemos redefinir a sua palavra-passe devido a uma má ligação com o seu ambiente no local. Contacte o seu administrador e peça-lhes que investiguem.|

## <a name="troubleshoot-the-password-reset-configuration-in-the-azure-portal"></a>Resolução de problemas da configuração de reset da palavra-passe no portal Azure

| Erro | Solução |
| --- | --- |
| Não vejo a secção **de reset da palavra-passe** sob a Azure AD no portal Azure. | Isto pode acontecer se não tiver uma licença AZure AD atribuída ao administrador que executa a operação. <br> <br> Atribua uma licença à conta do administrador em questão. Pode seguir os passos na [Atribuição, verificar e resolver problemas com](../users-groups-roles/licensing-groups-assign.md#step-1-assign-the-required-licenses) o artigo de licenças.|
| Não vejo uma opção de configuração específica. | Muitos elementos da UI estão escondidos até serem necessários. Tente ativar todas as opções que deseja ver. |
| Não vejo o separador **de integração no local.** | Esta opção só fica visível se tiver descarregado O Azure AD Connect e tiver configurado a gravação da palavra-passe. Para obter mais informações, consulte [Começar com Azure AD Connect utilizando as definições expressas](../hybrid/how-to-connect-install-express.md). |

## <a name="troubleshoot-password-reset-reporting"></a>Relatório de redefinição de senha de resolução de problemas

| Erro | Solução |
| --- | --- |
| Não vejo nenhum tipo de atividade de gestão de passwords na categoria de eventos de auditoria **de Autosserviço password Management.** | Isto pode acontecer se não tiver uma licença AZure AD atribuída ao administrador que executa a operação. <br> <br> Pode resolver este problema atribuindo uma licença à conta do administrador em questão. Siga os passos na [Atribuição, verifique e resolva problemas com o artigo licenças.](../users-groups-roles/licensing-groups-assign.md#step-1-assign-the-required-licenses) |
| As inscrições dos utilizadores mostram várias vezes. | Atualmente, quando um utilizador se regista, registamos cada pedaço de dados individual que está registado como um evento separado. <br> <br> Se quiser agregar estes dados e ter uma maior flexibilidade na forma como os pode visualizar, pode descarregar o relatório e abrir os dados como uma mesa de rotação no Excel.

## <a name="troubleshoot-the-password-reset-registration-portal"></a>Resolução de problemas do portal de registo de redefinição de passwords

| Erro | Solução |
| --- | --- |
| O diretório não está ativado para reset de palavra-passe. **O seu administrador não lhe permitiu utilizar esta funcionalidade.** | Mude a bandeira **de autosserviço ativada** para **Selecionado** ou **Tudo** e, em seguida, selecione **Guardar**. |
| O utilizador não tem uma licença AD Azure atribuída. **O seu administrador não lhe permitiu utilizar esta funcionalidade.** | Isto pode acontecer se não tiver uma licença AZure AD atribuída ao administrador que executa a operação. <br> <br> Pode resolver este problema atribuindo uma licença à conta do administrador em questão. Siga os passos na [Atribuição, verifique e resolva problemas com o artigo licenças.](../users-groups-roles/licensing-groups-assign.md#step-1-assign-the-required-licenses)|
| Há um erro no processamento do pedido. | Isto pode ser causado por muitos problemas, mas geralmente este erro é causado por uma falha de serviço ou um problema de configuração. Se vir este erro e afetar o seu negócio, contacte o suporte da Microsoft para obter assistência adicional. |

## <a name="troubleshoot-the-password-reset-portal"></a>Resolução de problemas do portal de reset da palavra-passe

| Erro | Solução |
| --- | --- |
| O diretório não está ativado para reset de palavra-passe. | Mude a bandeira **de autosserviço ativada** para **Selecionado** ou **Tudo** e, em seguida, selecione **Guardar**. |
| O utilizador não tem uma licença AD Azure atribuída. | Isto pode acontecer se não tiver uma licença AZure AD atribuída ao administrador que executa a operação. <br> <br> Pode resolver este problema se atribuir uma licença à conta do administrador em questão. Siga os passos na [Atribuição, verifique e resolva problemas com o artigo licenças.](../users-groups-roles/licensing-groups-assign.md#step-1-assign-the-required-licenses) |
| O diretório está ativado para reset de palavra-passe, mas o utilizador tem informações de autenticação em falta ou mal formadas. | Antes de prosseguir, certifique-se de que o utilizador formou corretamente os dados de contacto nos ficheiros do diretório. Para obter mais informações, consulte [os dados utilizados pela Azure AD, redefinindo a palavra-passe de autosserviço](howto-sspr-authenticationdata.md). |
| O diretório está ativado para reset de palavra-passe, mas o utilizador tem apenas um pedaço de dados de contacto no ficheiro quando a política está definida para exigir dois métodos de verificação. | Antes de prosseguir, certifique-se de que o utilizador dispõe de pelo menos dois métodos de contacto devidamente configurados. Um exemplo é ter um número de telemóvel *e* um número de telefone do escritório. |
| O diretório está ativado para o reset da palavra-passe e o utilizador está devidamente configurado, mas o utilizador não pode ser contactado. | Isto pode ser o resultado de um erro de serviço temporário ou se houver dados de contacto incorretos que não possamos detetar corretamente. <br> <br> Se o utilizador esperar 10 segundos, aparecem links "tente novamente" e apareçam ligações "contacte o seu administrador". Se o utilizador selecionar "tente novamente", retriciona a chamada. Se o utilizador selecionar "contacte o seu administrador", envia um e-mail de formulário aos seus administradores solicitando um reset de palavra-passe para ser executado para essa conta de utilizador. |
| O utilizador nunca recebe a palavra-passe de redefinição de SMS ou chamada telefónica. | Isto pode ser o resultado de um número de telefone mal formado no diretório. Certifique-se de que o número de telefone está no formato "+ccc xxxyyzzXeeee". <br> <br> O reset da palavra-passe não suporta extensões, mesmo que especifique uma no diretório. As extensões são retiradas antes da chamada ser despachada. Utilize um número sem extensão ou integre a extensão no número de telefone na sua bolsa de filial privada (PBX). |
| O utilizador nunca recebe o e-mail de reset da palavra-passe. | A causa mais comum para este problema é que a mensagem é rejeitada por um filtro de spam. Verifique a pasta de spam, lixo ou itens eliminados para obter o e-mail. <br> <br> Certifique-se também de que está a verificar a conta de e-mail correta para a mensagem. |
| Estabeleci uma política de reset de passwords, mas quando uma conta administrada usa o reset da palavra-passe, essa política não é aplicada. | A Microsoft gere e controla a política de reset da palavra-passe do administrador para garantir o mais alto nível de segurança. |
| O utilizador está impedido de tentar uma palavra-passe reiniciada demasiadas vezes num dia. | Implementamos um mecanismo de estrangulamento automático para impedir que os utilizadores tentem redefinir as suas palavras-passe demasiadas vezes num curto espaço de tempo. O estrangulamento ocorre quando: <br><ul><li>O utilizador tenta validar um número de telefone cinco vezes numa hora.</li><li>O utilizador tenta utilizar o portão de perguntas de segurança cinco vezes numa hora.</li><li>O utilizador tenta redefinir uma palavra-passe para a mesma conta de utilizador cinco vezes numa hora.</li></ul>Para corrigir este problema, instrua o utilizador a esperar 24 horas após a última tentativa. O utilizador pode então redefinir a sua palavra-passe. |
| O utilizador vê um erro ao validar o seu número de telefone. | Este erro ocorre quando o número de telefone introduzido não corresponde ao número de telefone no ficheiro. Certifique-se de que o utilizador está a introduzir o número de telefone completo, incluindo a área e o código do país, quando tentar utilizar um método baseado no telefone para reiniciar a palavra-passe. |
| Há um erro no processamento do pedido. | Isto pode ser causado por muitos problemas, mas geralmente este erro é causado por uma falha de serviço ou um problema de configuração. Se vir este erro e afetar o seu negócio, contacte o suporte da Microsoft para obter assistência adicional. |
| Violação da política no local | A palavra-passe não corresponde à política de senha do Ative Directory no local. |
| A palavra-passe não cumpre a política desomesta | A palavra-passe utilizada aparece na [lista de senhas proibidas](https://docs.microsoft.com/azure/active-directory/authentication/concept-password-ban-bad#how-are-passwords-evaluated) e não pode ser utilizada. |

## <a name="troubleshoot-password-writeback"></a>Writeback de palavra-passe de resolução de problemas

| Erro | Solução |
| --- | --- |
| O serviço de reset de palavra-passe não começa no local. O erro 6800 aparece no registo de eventos da aplicação da máquina Azure AD Connect. <br> <br> Após o embarque, a autenticação federada, pass-through ou os utilizadores sincronizados com palavras-passe não conseguem redefinir as suas palavras-passe. | Quando a gravação da palavra-passe está ativada, o motor de sincronização chama a biblioteca de writeback para executar a configuração (embarque) comunicando ao serviço de embarque na nuvem. Quaisquer erros encontrados durante o embarque ou durante o início do ponto final da Fundação de Comunicação do Windows (WCF) para a writeback de palavra-passe resulta em erros no registo de eventos, na sua máquina Azure AD Connect. <br> <br> Durante o reinício do serviço Azure AD Sync (ADSync), se a gravação foi configurada, o ponto final do WCF começa. Mas, se o arranque do ponto final falhar, vamos registar o evento 6800 e deixar o serviço de sincronização arrancar. A presença deste evento significa que o ponto final de writeback da palavra-passe não foi iniciado. Os detalhes do registo do evento para este evento 6800, juntamente com as entradas de registo de eventos geradas pelo componente PasswordResetService, indicam por que não é possível iniciar o ponto final. Reveja estes erros de registo de eventos e tente reiniciar o Azure AD Connect se a gravação da palavra-passe ainda não estiver a funcionar. Se o problema persistir, tente desativar e, em seguida, reative a gravação de palavra-passe.
| Quando um utilizador tenta redefinir uma palavra-passe ou desbloquear uma conta com a gravação da palavra-passe ativada, a operação falha. <br> <br> Além disso, vê um evento no registo de eventos Azure AD Connect que contém: "O Motor de Sincronização devolveu um erro hr=800700CE, mensagem=O nome de ficheiro ou extensão é demasiado longo" após a operação de desbloqueio ocorrer. | Encontre a conta ative Directory para Azure AD Connect e reponha a palavra-passe de modo a que não contenha mais de 256 caracteres. Em seguida, abra o **Serviço de Sincronização** a partir do menu **Iniciar.** Navegue pelos **Conectores** e encontre o **Conector ative Directory**. Selecione-o e, em seguida, **selecione Propriedades.** Navegue na página **De Credenciais** e introduza a nova palavra-passe. Selecione **OK** para fechar a página. |
| No último passo do processo de instalação Azure AD Connect, vê-se um erro que indica que a gravação da palavra-passe não pôde ser configurada. <br> <br> O registo de eventos da aplicação AZure AD Connect contém erro 32009 com o texto "Erro obter ficha de auth". | Este erro ocorre nos dois seguintes casos: <br><ul><li>Especificou uma palavra-passe incorreta para a conta de administrador global especificada no início do processo de instalação Azure AD Connect.</li><li>Tentou utilizar um utilizador federado para a conta de administrador global especificada no início do processo de instalação Azure AD Connect.</li></ul> Para corrigir este problema, certifique-se de que não está a utilizar uma conta federada para o administrador global especificado no início do processo de instalação. Certifique-se também de que a palavra-passe especificada está correta. |
| O registo de eventos da máquina Azure AD Connect contém o erro 32002 que é lançado executando o PasswordResetService. <br> <br> O erro diz: "Erro de ligação ao ServiceBus. O provedor de fichas não foi capaz de fornecer um símbolo de segurança. | O seu ambiente no local não é capaz de se ligar ao ponto final do Azure Service Bus na nuvem. Este erro é normalmente causado por uma regra de firewall que bloqueia uma ligação de saída a uma determinada porta ou endereço web. Consulte [os pré-requisitos de Conectividade](../hybrid/how-to-connect-install-prerequisites.md) para mais informações. Depois de atualizar estas regras, reinicie a máquina Azure AD Connect e a gravação da palavra-passe deve voltar a funcionar. |
| Depois de trabalhar durante algum tempo, os utilizadores federados, de passagem ou sincronizados com palavras-passe não conseguem redefinir as suas palavras-passe. | Em alguns casos raros, o serviço de writeback de palavra-passe pode não reiniciar quando o Azure AD Connect foi reiniciado. Nestes casos, em primeiro lugar, verifique se o writeback de palavra-passe parece estar ativado no local. Pode verificar utilizando o assistente Azure AD Connect ou o PowerShell (ver a secção HowTos anterior). Se a função parecer ativada, tente ativar ou desativar novamente a funcionalidade através da UI ou powerShell. Se isto não funcionar, experimente uma desinstalação completa e reinstalar o Azure AD Connect. |
| Utilizadores federados, de passagem ou sincronizados com palavra-passe que tentam redefinir as suas palavras-passe vêem um erro depois de tentarem enviar a sua palavra-passe. O erro indica que houve um problema de serviço. <br ><br> Além deste problema, durante as operações de reset de passwords, poderá ver um erro que o agente de gestão foi impedido de aceder nos seus registos de eventos no local. | Se vir estes erros no seu registo de eventos, confirme que a conta Ative Directory Management Agent (ADMA) que foi especificada no assistente no momento da configuração tem as permissões necessárias para a gravação da palavra-passe. <br> <br> Após esta permissão ser dada, pode levar até uma hora para que as permissões escorredem através da `sdprop` tarefa de fundo no controlador de domínio (DC). <br> <br> Para que a palavra-passe seja reposta para funcionar, a permissão tem de ser carimbada no descritor de segurança do objeto do utilizador cuja palavra-passe está a ser reposta. Até que esta permissão apareça no objeto do utilizador, o reset da palavra-passe continua a falhar com uma mensagem negada de acesso. |
| A autenticação federada, pass-through ou os utilizadores sincronizados com palavras-passe sincronizados que tentam redefinir as suas palavras-passe, vêem um erro depois de enviarem a sua palavra-passe. O erro indica que houve um problema de serviço. <br> <br> Além deste problema, durante as operações de reset da palavra-passe, poderá ver um erro nos registos do evento a partir do serviço Azure AD Connect indicando um erro de "Não foi possível encontrar um objeto". | Este erro geralmente indica que o motor de sincronização não é capaz de encontrar o objeto do utilizador no espaço do conector AD Azure ou no metaverso ligado (MV) ou no objeto de espaço do conector AD Azure. <br> <br> Para resolver este problema, certifique-se de que o utilizador está de facto sincronizado entre as instalações e o AD AZure através da instância atual do Azure AD Connect e inspecione o estado dos objetos nos espaços do conector e MV. Confirme que o objeto Ative Directory Certificate Services (AD CS) está ligado ao objeto MV através da regra "Microsoft.InfromADUserAccountEnabled.xxx".|
| Utilizadores federados, de passagem ou sincronizados com palavra-passe sincronizados que tentam redefinir as suas palavras-passe vêem um erro depois de enviarem a sua palavra-passe. O erro indica que houve um problema de serviço. <br> <br> Além deste problema, durante as operações de reset da palavra-passe, poderá ver um erro nos registos do evento a partir do serviço Azure AD Connect que indica que existe um erro de "Múltiplos fósforos encontrados". | Isto indica que o motor de sincronização detetou que o objeto MV está ligado a mais de um objeto AD CS através de "Microsoft.InfromADUserAccountEnabled.xxx". Isto significa que o utilizador tem uma conta ativada em mais de uma floresta. Este cenário não é suportado para a gravação de passwords. |
| As operações de palavra-passe falham com um erro de configuração. O registo do evento de aplicação contém o erro 6329 do Azure AD Connect com o texto "0x8023061f (A operação falhou porque a sincronização da palavra-passe não está ativada neste Agente de Gestão)". | Este erro ocorre se a configuração Azure AD Connect for alterada para adicionar uma nova floresta ative directory (ou para remover e ler uma floresta existente) depois de a função de gravação de palavra-passe já ter sido ativada. As operações de senha para os utilizadores nestas florestas recentemente adicionadas falham. Para corrigir o problema, desative e, em seguida, reativa a função de desativação da palavra-passe após a conclusão das alterações na configuração da floresta. |

## <a name="password-writeback-event-log-error-codes"></a>Códigos de erro de registo de eventos de registo de erro de registo de palavra-passe

Uma boa prática quando se desresem problemas com a gravação de passwords é inspecionar o registo do evento de aplicação, na sua máquina Azure AD Connect. Este registo de eventos contém eventos de duas fontes de interesse para a gravação de password. A fonte do PasswordResetService descreve operações e problemas relacionados com o funcionamento da gravação de password. A fonte ADSync descreve operações e problemas relacionados com a definição de palavras-passe no seu ambiente de Diretório Ativo.

### <a name="if-the-source-of-the-event-is-adsync"></a>Se a origem do evento for a ADSync

| Código | Nome ou mensagem | Descrição |
| --- | --- | --- |
| 6329 | BAIL: MMS(4924) 0x80230619: "Uma restrição impede que a palavra-passe seja alterada para a atual especificada." | Este evento ocorre quando o serviço de writeback de palavra-passe tenta definir uma palavra-passe no seu diretório local que não satisfaz os requisitos de senha, histórico, complexidade ou filtragem do domínio. <br> <br> Se tiver uma idade mínima de senha e tiver alterado recentemente a palavra-passe dentro dessa janela de tempo, não poderá alterar novamente a palavra-passe até atingir a idade especificada no seu domínio. Para efeitos de teste, a idade mínima deve ser fixada para 0. <br> <br> Se tiver os requisitos de histórico de palavra-passe ativados, então deve selecionar uma palavra-passe que não tenha sido utilizada nos últimos *Tempos N,* onde *N* é a definição do histórico de palavra-passe. Se selecionar uma palavra-passe que tenha sido usada nas últimas *vezes N,* verá uma falha neste caso. Para efeitos de teste, o histórico de palavras-passe deve ser definido para 0. <br> <br> Se tiver requisitos de complexidade de palavras-passe, todos eles são aplicados quando o utilizador tenta alterar ou redefinir uma palavra-passe. <br> <br> Se tiver filtros de senha ativados e um utilizador selecionar uma palavra-passe que não satisfaça os critérios de filtragem, então o reset ou a alteração da operação falha. |
| 6329 | MMS(3040): admaexport.cpp(2837): O servidor não contém o controlo da política de palavra-passe LDAP. | Este problema ocorre se LDAP_SERVER_POLICY_HINTS_OID controlo (1.2.840.113556.1.4.2066) não estiver ativado nos DCs. Para utilizar a função de descodão da palavra-passe, tem de ativar o controlo. Para tal, os DCs devem estar no Windows Server 2008R2 ou posteriormente. |
| HR 8023042 | O Motor de Sincronização devolveu um erro hr=80230402, mensagem=Uma tentativa de obter um objeto falhado porque há entradas duplicadas com a mesma âncora. | Este erro ocorre quando o mesmo ID do utilizador está ativado em vários domínios. Um exemplo é se estiver a sincronizar as florestas de conta e recursos e tiver o mesmo ID do utilizador presente e ativado em cada floresta. <br> <br> Este erro também pode ocorrer se utilizar um atributo de âncora não único, como um pseudónimo ou UPN, e dois utilizadores partilham o mesmo atributo de âncora. <br> <br> Para resolver este problema, certifique-se de que não tem utilizadores duplicados dentro dos seus domínios e que utiliza um atributo de âncora único para cada utilizador. |

### <a name="if-the-source-of-the-event-is-passwordresetservice"></a>Se a origem do evento for passwordResetService

| Código | Nome ou mensagem | Descrição |
| --- | --- | --- |
| 31001 | PasswordResetStart | Este evento indica que o serviço no local detetou um pedido de reset de palavra-passe para uma autenticação federada, pass-through ou utilizador sincronizado com palavra-passe que se origina da nuvem. Este evento é o primeiro evento em cada operação de writeback reset de palavra-passe. |
| 31002 | PasswordResetSuccess | Este evento indica que um utilizador selecionou uma nova palavra-passe durante uma operação de reset de palavra-passe. Determinámos que esta palavra-passe satisfaz os requisitos de senha corporativa. A palavra-passe foi escrita com sucesso para o ambiente local do Ative Directory. |
| 31003 | PasswordResetFail | Este evento indica que um utilizador selecionou uma palavra-passe e a palavra-passe chegou com sucesso ao ambiente no local. Mas quando tentámos definir a senha no ambiente local do Ative Directory, ocorreu uma falha. Esta falha pode acontecer por várias razões: <br><ul><li>A palavra-passe do utilizador não satisfaz os requisitos de idade, histórico, complexidade ou filtro para o domínio. Para resolver este problema, crie uma nova senha.</li><li>A conta de serviço da ADMA não dispõe das permissões adequadas para definir a nova palavra-passe na conta de utilizador em questão.</li><li>A conta do utilizador está num grupo protegido, como o grupo de administração de domínio ou empresa, que não permite operações de definição de passwords.</li></ul>|
| 31004 | OnboardingEventStart | Este evento ocorre se ativar a gravação de palavra-passe com o Azure AD Connect e começamos a embarcar a sua organização para o serviço web de writeback de palavra-passe. |
| 31005 | OnboardingEventSuccess | Este evento indica que o processo de embarque foi bem sucedido e que a capacidade de descodução da palavra-passe está pronta a ser utilizada. |
| 31006 | ChangePasswordStart | Este evento indica que o serviço no local detetou um pedido de alteração de senha para uma autenticação federada, pass-through ou utilizador sincronizado com palavra-passe que se origina da nuvem. Este evento é o primeiro evento em cada operação de writeback de mudança de palavra-passe. |
| 31007 | ChangePasswordSuccess | Este evento indica que um utilizador selecionou uma nova palavra-passe durante uma operação de alteração de senha, determinamos que a palavra-passe satisfaz os requisitos de senha corporativa e que a palavra-passe foi escrita com sucesso para o ambiente de Ative Directory local. |
| 31008 | ChangePasswordFail | Este evento indica que um utilizador selecionou uma palavra-passe e que a palavra-passe chegou com sucesso ao ambiente no local, mas quando tentámos definir a palavra-passe no ambiente do Ative Directory local, ocorreu uma falha. Esta falha pode acontecer por várias razões: <br><ul><li>A palavra-passe do utilizador não satisfaz os requisitos de idade, histórico, complexidade ou filtro para o domínio. Para resolver este problema, crie uma nova senha.</li><li>A conta de serviço da ADMA não dispõe das permissões adequadas para definir a nova palavra-passe na conta de utilizador em questão.</li><li>A conta do utilizador está num grupo protegido, como administradores de domínio ou empresa, que não permite operações de definição de passwords.</li></ul> |
| 31009 | ResetUserPasswordByAdminStart | O serviço no local detetou um pedido de reset de palavra-passe para uma autenticação federada, pass-through ou utilizador sincronizado com palavra-passe com origem no administrador em nome de um utilizador. Este evento é o primeiro evento em cada operação de writeback reset de palavra-passe que é iniciado por um administrador. |
| 31010 | ResetUserPasswordByAdminSuccess | O administrador selecionou uma nova palavra-passe durante uma operação de reposição de palavras-passe iniciada por administradores. Determinámos que esta palavra-passe satisfaz os requisitos de senha corporativa. A palavra-passe foi escrita com sucesso para o ambiente local do Ative Directory. |
| 31011 | ResetUserPasswordByAdminFail | O administrador selecionou uma palavra-passe em nome de um utilizador. A senha chegou com sucesso ao ambiente no local. Mas quando tentámos definir a senha no ambiente local do Ative Directory, ocorreu uma falha. Esta falha pode acontecer por várias razões: <br><ul><li>A palavra-passe do utilizador não satisfaz os requisitos de idade, histórico, complexidade ou filtro para o domínio. Tente uma nova senha para resolver este problema.</li><li>A conta de serviço da ADMA não dispõe das permissões adequadas para definir a nova palavra-passe na conta de utilizador em questão.</li><li>A conta do utilizador está num grupo protegido, como administradores de domínio ou empresa, que não permite operações de definição de passwords.</li></ul>  |
| 31012 | OffboardingEventStart | Este evento ocorre se desativar a gravação de palavra-passe com o Azure AD Connect e indica que começamos a desacortar a sua organização para o serviço web de writeback de palavra-passe. |
| 31013| OffboardingEventSuccess| Este evento indica que o processo de offboarding foi bem sucedido e que a capacidade de desativação da palavra-passe foi desativada com sucesso. |
| 31014| OffboardingEventFail| Este evento indica que o processo de offboarding não foi bem sucedido. Isto pode dever-se a um erro de permissões na conta de administrador na nuvem ou no local especificada durante a configuração. O erro também pode ocorrer se estiver a tentar usar um administrador global de nuvem federada ao desativar a gravação de palavras-passe. Para corrigir este problema, verifique as suas permissões administrativas e certifique-se de que não está a utilizar uma conta federada enquanto configura a capacidade de desativação da palavra-passe.|
| 31015| WriteBackServiceStarted| Este evento indica que o serviço de writeback de palavra-passe começou com sucesso. Está pronto para aceitar pedidos de gestão de passwords a partir da nuvem.|
| 31016| WriteBackServices coberto| Este evento indica que o serviço de descodão de palavra-passe parou. Quaisquer pedidos de gestão de passwords da nuvem não serão bem sucedidos.|
| 31017| AuthTokenSuccess| Este evento indica que conseguimos obter com sucesso um sinal de autorização para a administração global especificada durante a configuração Azure AD Connect para iniciar o processo de offboarding ou de embarque.|
| 31018| KeyPairCreationSuccess| Este evento indica que criámos com sucesso a chave de encriptação da palavra-passe. Esta chave é usada para encriptar palavras-passe da nuvem a serem enviadas para o seu ambiente no local.|
| 32000| DesconhecidoError| Este evento indica que ocorreu um erro desconhecido durante uma operação de gestão de passwords. Veja o texto de exceção no evento para mais detalhes. Se tiver problemas, tente desativar e, em seguida, reativar a gravação da palavra-passe. Se isto não ajudar, inclua uma cópia do seu registo de eventos juntamente com o ID de rastreio especificado no seu engenheiro de suporte.|
| 32001| ServiceError| Este evento indica que houve um erro de ligação ao serviço de reset da palavra-passe na nuvem. Este erro ocorre geralmente quando o serviço no local não foi capaz de se ligar ao serviço web de redefinição de palavra-passe.|
| 32002| ServiceBusError| Este evento indica que houve um erro de ligação à instância do autocarro de serviço do seu inquilino. Isto pode acontecer se estiver a bloquear ligações de saída no seu ambiente no local. Verifique a sua firewall para se certificar de que permite ligações sobre o TCP 443 e para https://ssprdedicatedsbprodncu.servicebus.windows.net , e tente novamente. Se ainda tiver problemas, tente desativar e, em seguida, reativar a gravação da palavra-passe.|
| 32003| InPutValidationError| Este evento indica que a entrada passada para o nosso serviço web API foi inválida. Tente a operação de novo.|
| 32004| DesencriptaçãoError| Este evento indica que houve um erro de desencriptar a palavra-passe que chegou da nuvem. Isto pode ser devido a uma incompatibilidade entre o serviço de nuvem e o ambiente no local. Para resolver este problema, desative e, em seguida, reative a gravação de palavras-passe no seu ambiente no local.|
| 32005| ConfiguraçãoError| Durante o embarque, guardamos informações específicas do inquilino num ficheiro de configuração no seu ambiente no local. Este evento indica que houve um erro ao salvar este ficheiro ou que quando o serviço foi iniciado, houve um erro ao ler o ficheiro. Para corrigir este problema, tente desativar e, em seguida, reativar a gravação da palavra-passe para forçar uma reescrita do ficheiro de configuração.|
| 32007| OnBoardingConfigUpdateError| Durante o embarque, enviamos dados da nuvem para o serviço de redefinição de passwords no local. Esses dados são então escritos num ficheiro de memória antes de serem enviados para o serviço de sincronização para serem armazenados de forma segura no disco. Este evento indica que há um problema em escrever ou atualizar esses dados na memória. Para corrigir este problema, tente desativar e, em seguida, reativar a gravação de palavras-passe para forçar uma reescrita deste ficheiro de configuração.|
| 32008| ValidaçãoError| Este evento indica que recebemos uma resposta inválida do serviço web de redefinição de palavra-passe. Para corrigir este problema, tente desativar e, em seguida, reativar a gravação da palavra-passe.|
| 32009| AuthTokenError| Este evento indica que não conseguimos obter um sinal de autorização para a conta de administrador global especificada durante a configuração do Azure AD Connect. Este erro pode ser causado por um mau nome de utilizador ou palavra-passe especificado para a conta de administração global. Este erro também pode ocorrer se a conta de administração global especificada for federada. Para corrigir este problema, repercutir a configuração com o nome de utilizador e palavra-passe corretos e certifique-se de que o administrador é uma conta gerida (apenas em nuvem ou sincronizada com palavra-passe).|
| 32010| CriptoError| Este evento indica que houve um erro a gerar a chave de encriptação da palavra-passe ou a desencriptar uma palavra-passe que chega do serviço de cloud. Este erro indica provavelmente um problema com o seu ambiente. Veja os detalhes do seu registo de eventos para saber mais sobre como resolver este problema. Também pode tentar desativar e, em seguida, voltar a ativar o serviço de desativação da palavra-passe.|
| 32011| OnBoardingServiceError| Este evento indica que o serviço no local não conseguiu comunicar adequadamente com o serviço web de redefinição de palavra-passe para iniciar o processo de embarque. Isto pode acontecer como resultado de uma regra de firewall ou se houver um problema em obter um sinal de autenticação para o seu inquilino. Para resolver este problema, certifique-se de que não está a bloquear ligações de saída sobre tCP 443 e TCP 9350-9354 ou para https://ssprdedicatedsbprodncu.servicebus.windows.net . Também certifique-se de que a conta de administração Azure que está a usar a bordo não é federada.|
| 32013| OffBoardingError| Este evento indica que o serviço no local não conseguiu comunicar adequadamente com o serviço web de reset de palavra-passe para iniciar o processo de offboarding. Isto pode acontecer como resultado de uma regra de firewall ou se houver um problema em obter um sinal de autorização para o seu inquilino. Para resolver este problema, certifique-se de que não está a bloquear ligações de saída acima do 443 ou para https://ssprdedicatedsbprodncu.servicebus.windows.net , e que a conta de administração Azure Ative Que está a usar para offboard não é federada.|
| 32014| ServiceBusWarning| Este evento indica que tivemos que tentar novamente ligar-nos à instância de ônibus de serviço do seu inquilino. Em condições normais, isto não deve ser uma preocupação, mas se você vir este evento muitas vezes, considere verificar a sua ligação de rede ao Service Bus, especialmente se é uma ligação de alta latência ou de baixa largura de banda.|
| 32015| RelatórioServiceHealthError| Para monitorizar a saúde do seu serviço de writeback de palavra-passe, enviamos dados de batimentos cardíacos para o nosso serviço web de redefinição de palavra-passe a cada cinco minutos. Este evento indica que houve um erro ao enviar esta informação de saúde de volta para o serviço web na nuvem. Esta informação de saúde não inclui quaisquer dados pessoais, e é puramente uma estatística de batimentos cardíacos e de serviços básicos para que possamos fornecer informações sobre o estado do serviço na nuvem.|
| 33001| ADUnKnownError| Este evento indica que houve um erro desconhecido devolvido pelo Ative Directory. Verifique o registo de eventos do servidor Azure AD Connect para obter mais informações a partir da fonte ADSync.|
| 33002| ADUserNotFoundError| Este evento indica que o utilizador que está a tentar reiniciar ou alterar uma palavra-passe não foi encontrado no diretório no local. Este erro pode ocorrer quando o utilizador foi eliminado no local, mas não na nuvem. Este erro também pode ocorrer se houver um problema com a sincronização. Verifique os seus registos de sincronização e os últimos detalhes de sincronização para obter mais informações.|
| 33003| ADMutliMatchError| Quando um pedido de reset ou alteração de palavra-passe é originário da nuvem, utilizamos a âncora de nuvem especificada durante o processo de configuração do Azure AD Connect para determinar como ligar esse pedido a um utilizador no seu ambiente no local. Este evento indica que encontramos dois utilizadores no seu diretório no local com o mesmo atributo de âncora em nuvem. Verifique os seus registos de sincronização e os últimos detalhes de sincronização para obter mais informações.|
| 33004| ADPermissionsError| Este evento indica que a conta de serviço do Agente de Gestão de Diretório Ativo (ADMA) não dispõe das permissões adequadas na conta em questão para definir uma nova palavra-passe. Certifique-se de que a conta ADMA na floresta do utilizador tem permissões de senha de reposição em todos os objetos da floresta. Para obter mais informações sobre como definir as permissões, consulte o Passo 4: Configurar as permissões adequadas do Diretório Ativo. Este erro também pode ocorrer quando o atributo AdminCount do utilizador é definido para 1.|
| 33005| ADUserAccountDisabled| Este evento indica que tentámos redefinir ou alterar uma palavra-passe para uma conta que estava desativada no local. Ativar a conta e tentar novamente a operação.|
| 33006| ADUserAccountlockedOut| Este evento indica que tentámos redefinir ou alterar uma palavra-passe para uma conta que estava bloqueada no local. Os bloqueios podem ocorrer quando um utilizador tentou alterar ou redefinir a operação de senha demasiadas vezes num curto espaço de tempo. Desbloqueie a conta e tente a operação novamente.|
| 33007| ADUserIncorrectPassword| Este evento indica que o utilizador especificou uma palavra-passe incorreta ao efectuário de uma operação de alteração de palavra-passe. Especifique a palavra-passe correta e tente novamente.|
| 33008| ADPasswordPolicyError| Este evento ocorre quando o serviço de writeback de palavra-passe tenta definir uma palavra-passe no seu diretório local que não satisfaz os requisitos de senha, histórico, complexidade ou filtragem do domínio. <br> <br> Se tiver uma idade mínima de senha e tiver alterado recentemente a palavra-passe dentro dessa janela de tempo, não poderá alterar novamente a palavra-passe até atingir a idade especificada no seu domínio. Para efeitos de teste, a idade mínima deve ser fixada para 0. <br> <br> Se tiver os requisitos de histórico de palavra-passe ativados, então deve selecionar uma palavra-passe que não tenha sido utilizada nos últimos *Tempos N,* onde *N* é a definição do histórico de palavra-passe. Se selecionar uma palavra-passe que tenha sido usada nas últimas *vezes N,* verá uma falha neste caso. Para efeitos de teste, o histórico de palavras-passe deve ser definido para 0. <br> <br> Se tiver requisitos de complexidade de palavras-passe, todos eles são aplicados quando o utilizador tenta alterar ou redefinir uma palavra-passe. <br> <br> Se tiver filtros de senha ativados e um utilizador selecionar uma palavra-passe que não satisfaça os critérios de filtragem, então o reset ou a alteração da operação falha.|
| 33009| ADConfigurationError| Este evento indica que houve um problema em escrever uma palavra-passe no seu diretório no local devido a um problema de configuração com o Ative Directory. Verifique o registo de eventos de aplicação da máquina Azure AD Connect para obter mensagens do serviço ADSync para obter mais informações sobre o erro ocorrido.|

## <a name="troubleshoot-password-writeback-connectivity"></a>Conectividade de writeback de palavras-passe de resolução de problemas

Se estiver a sofrer interrupções de serviço com o componente de writeback de palavra-passe do Azure AD Connect, aqui estão alguns passos rápidos que pode tomar para resolver este problema:

* [Confirmar conectividade de rede](#confirm-network-connectivity)
* [Reinicie o serviço Azure AD Connect Sync](#restart-the-azure-ad-connect-sync-service)
* [Desativar e reativar a funcionalidade de writeback de palavra-passe](#disable-and-re-enable-the-password-writeback-feature)
* [Instale o mais recente lançamento do Azure AD Connect](#install-the-latest-azure-ad-connect-release)
* [Writeback de palavra-passe de resolução de problemas](#troubleshoot-password-writeback)

Em geral, para recuperar o seu serviço da forma mais rápida, recomendamos que execute estes passos na ordem discutida anteriormente.

### <a name="confirm-network-connectivity"></a>Confirmar conectividade de rede

O ponto mais comum de falha é que as portas de firewall e ou proxy e os intervalos de tempo inativos estão incorretamente configurados. 

Para a versão 1.1.443.0 ou superior, precisa de acesso HTTPS de saída ao seguinte:

* \*.passwordreset.microsoftonline.com
* \*.servicebus.windows.net

Para maior granularidade, consulte a lista atualizada de Gamas IP do [Microsoft Azure Datacenter atualizadas todas as quartas-feiras](https://www.microsoft.com/download/details.aspx?id=41653) e postas em vigor na próxima segunda-feira.

Para obter mais informações, reveja os pré-requisitos de conectividade nos pré-requisitos para o artigo [Azure AD Connect.](../hybrid/how-to-connect-install-prerequisites.md)

> [!NOTE]
> A SSPR também pode falhar se as definições de "Password nunca expira" ou "O utilizador não pode alterar a palavra-passe" forem configuradas na conta em DS AD no local. 

### <a name="restart-the-azure-ad-connect-sync-service"></a>Reinicie o serviço Azure AD Connect Sync

Para resolver problemas de conectividade ou outros problemas transitórios com o serviço, reinicie o serviço Azure AD Connect Sync:

1. Como administrador, selecione **Iniciar** no servidor que executa Azure AD Connect.
1. Introduza **os serviços.msc** no campo de pesquisa e selecione **Enter**.
1. Procure a entrada microsoft **Azure AD Sync.**
1. Clique com o botão direito na entrada de serviço, **selecione Restart**e, em seguida, aguarde que a operação termine.

   ![Reinicie o serviço Azure AD Sync utilizando o GUI][Service restart]

Estes passos restabelecem a sua ligação com o serviço de nuvem e resolvem quaisquer interrupções que possa estar a sofrer. Se reiniciar o serviço ADSync não resolver o seu problema, recomendamos que tente desativar e, em seguida, reativar a função de desativação da palavra-passe.

### <a name="disable-and-re-enable-the-password-writeback-feature"></a>Desativar e reativar a funcionalidade de writeback de palavra-passe

Para resolver problemas de conectividade, desative e, em seguida, reative a função de desativação da palavra-passe:

   1. Como administrador, abra o assistente de configuração Azure AD Connect.
   1. Em **Connect to Azure AD,** insira as suas credenciais de administração global Azure AD.
   1. Em **Connect to AD DS,** insira as suas credenciais de administração de Serviços de Domínio AD.
   1. Na **identificação única dos seus utilizadores,** selecione o botão **Seguinte.**
   1. Nas **funcionalidades opcionais,** limpe a caixa **de verificação de writeback password.**
   1. Selecione **Seguinte** através das páginas de diálogo restantes sem alterar nada até chegar à página **Pronto para configurar.**
   1. Certifique-se de que a **página "Pronto para configurar"** mostra a opção **de writeback password** como **desativada** e, em seguida, selecione o botão de **configuração** verde para cometer as suas alterações.
   1. Em **Terminado,** limpe a opção **Synchronize now** e, em seguida, selecione **Finish** para fechar o assistente.
   1. Reabra o assistente de configuração de ligação AD Azure.
   1. Repita os passos 2-8, exceto certifique-se de que seleciona a opção **de writeback password** na página **de funcionalidades opcionais** para voltar a ativar o serviço.

Estes passos restabelecem a sua ligação com o nosso serviço na nuvem e resolvem quaisquer interrupções que possa estar a sofrer.

Se desativar e, em seguida, voltar a permitir que a função de desativação da palavra-passe não resolva o seu problema, recomendamos que volte a instalar o Azure AD Connect.

### <a name="install-the-latest-azure-ad-connect-release"></a>Instale o mais recente lançamento do Azure AD Connect

Reinstalar o Azure AD Connect pode resolver problemas de configuração e conectividade entre os nossos serviços na nuvem e o ambiente de Ative Directory local.

Recomendamos que execute este passo apenas depois de tentar os dois primeiros passos previamente descritos.

> [!WARNING]
> Se tiver personalizado as regras de sincronização fora da caixa, faça uma parte de trás antes de prosseguir com a *atualização e, em seguida, recolocar manualmente as regras depois de terminar.*

1. Descarregue a versão mais recente do Azure AD Connect a partir do [Microsoft Download Center](https://go.microsoft.com/fwlink/?LinkId=615771).
1. Como já instalou o Azure AD Connect, tem de realizar uma atualização no local para atualizar a instalação Azure AD Connect para a versão mais recente.
1. Execute o pacote descarregado e siga as instruções no ecrã para atualizar a sua máquina Azure AD Connect.

Os passos anteriores devem restabelecer a sua ligação com o nosso serviço na nuvem e resolver quaisquer interrupções que possa estar a sofrer.

Se a instalação da versão mais recente do servidor Azure AD Connect não resolver o seu problema, recomendamos que tente desativar e, em seguida, reativar a gravação da palavra-passe como um passo final após a instalação da versão mais recente.

## <a name="verify-that-azure-ad-connect-has-the-required-permission-for-password-writeback"></a>Verifique se o Azure AD Connect tem a permissão necessária para a gravação de passwords

O Azure AD Connect requer permissão de **redefinição de senha** de ative diretoria para efetuar a gravação de palavras-passe. Para saber se o Azure AD Connect tem a permissão necessária para uma conta de utilizador do Ative Directory no local, pode utilizar a funcionalidade de Permissão Efetiva do Windows:

1. Inicie sedumento no servidor AZure AD Connect e inicie o **Gestor de Serviço de Sincronização** selecionando o Serviço de **Start**  >  **Sincronização Inicial**.
1. No **separador Conectores,** selecione o conector **ative Directory Domain Services** e, em seguida, selecione **Propriedades**.  
   ![Gestor de serviços de sincronização mostrando como editar propriedades](./media/active-directory-passwords-troubleshoot/checkpermission01.png)  
  
1. Na janela pop-up, selecione **Connect to Ative Directory Forest** e tome nota da propriedade do nome do **utilizador.** Esta propriedade é a conta AD DS usada pela Azure AD Connect para realizar a sincronização do diretório. Para que o Azure AD Connect efetue a gravação de palavra-passe, a conta DS AD deve ter a permissão de senha de reset.  

   ![Encontrar o serviço de sincronização Conta de utilizador do Ative Directory](./media/active-directory-passwords-troubleshoot/checkpermission02.png) 
  
1. Inicie sôms para um controlador de domínio no local e inicie a aplicação **Ative Directory Users and Computers.**
1. Selecione **Ver** e certifique-se de que a opção **Funcionalidades Avançadas** está ativada.  

   ![Utilizadores e computadores de diretório ativo mostram funcionalidades avançadas](./media/active-directory-passwords-troubleshoot/checkpermission03.png) 
  
1. Procure a conta de utilizador do Ative Directory que pretende verificar. Clique com o botão direito no nome da conta e selecione **Propriedades.**  
1. Na janela pop-up, vá ao separador **Segurança** e selecione **Advanced**.  
1. Nas **Definições avançadas** de segurança para a janela pop-up do administrador, aceda ao **separador Acesso Efetivo.**
1. **Selecione Selecione um utilizador**, selecione a conta AD DS utilizada pelo Azure AD Connect (ver passo 3) e, em seguida, selecione **Ver acesso eficaz**.

   ![Separador de acesso eficaz mostrando a Conta de Sincronização](./media/active-directory-passwords-troubleshoot/checkpermission06.png) 
  
1. Desloque-se para baixo e procure **a palavra-passe Reset**. Se a entrada tiver uma marca de verificação, a conta DS AD tem permissão para redefinir a palavra-passe da conta de utilizador do Ative Directory selecionada.  

   ![Validando que a conta de sincronização tem a permissão de palavra-passe Reset](./media/active-directory-passwords-troubleshoot/checkpermission07.png)  

## <a name="azure-ad-forums"></a>Fóruns da AD de Azure

Se tiver uma pergunta geral sobre o Azure AD e a palavra-passe de autosserviço, pode pedir ajuda à comunidade na página de perguntas do Microsoft Q&A página de perguntas para o [Azure Ative Directory](https://docs.microsoft.com/answers/topics/azure-active-directory.html). Os membros da comunidade incluem engenheiros, gestores de produtos, MVPs e outros profissionais de TI.

## <a name="contact-microsoft-support"></a>Contacte o Suporte da Microsoft

Se não encontrar a resposta para um problema, as nossas equipas de apoio estão sempre disponíveis para o ajudar mais.

Para ajudá-lo corretamente, pedimos que forneça o máximo de detalhes possível ao abrir um caso. Estes detalhes incluem:

* **Descrição geral do erro:** Qual é o erro? Qual foi o comportamento que foi notado? Como podemos reproduzir o erro? Forneça o máximo de detalhes possível.
* **Página**: Em que página estava quando reparou no erro? Inclua o URL se for capaz e uma imagem da página.
* **Código de suporte**: Qual foi o código de suporte que foi gerado quando o utilizador viu o erro?
   * Para encontrar este código, reproduza o erro, selecione a ligação **de código de suporte** na parte inferior do ecrã e envie ao engenheiro de suporte o GUIADOr que resulta.

   ![Encontre o código de suporte na parte inferior do ecrã][Support code]

  * Se estiver numa página sem um código de suporte na parte inferior, selecione F12 e procure o SID e o CID e envie esses dois resultados para o engenheiro de suporte.
* **Data, hora e fuso horário**: Inclua a data e a hora *exatas com o fuso horário* em que ocorreu o erro.
* **ID do utilizador**: Quem foi o utilizador que viu o erro? Um exemplo é * \@ o contoso.com do utilizador*.
   * É um utilizador federado?
   * É um utilizador de autenticação pass-through?
   * É um utilizador sincronizado com palavra-passe?
   * É um utilizador só de nuvens?
* **Licenciamento**: O utilizador tem uma licença AD Azure atribuída?
* **Registo do evento**de aplicação : Se estiver a utilizar a gravação de palavra-passe e o erro estiver na sua infraestrutura no local, inclua uma cópia zipped do registo do evento da sua aplicação a partir do servidor Azure AD Connect.

[Service restart]: ./media/active-directory-passwords-troubleshoot/servicerestart.png "Reinicie o serviço Azure AD Sync"
[Support code]: ./media/active-directory-passwords-troubleshoot/supportcode.png "O código de suporte está localizado no canto inferior direito da janela"

## <a name="next-steps"></a>Próximos passos

Os seguintes artigos fornecem informações adicionais sobre a palavra-passe reposta através da Azure AD:

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
