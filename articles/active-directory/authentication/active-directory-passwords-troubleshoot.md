---
title: Resolução de problemas - Azure Active Directory de reposição de palavra-passe self-service
description: Resolução de problemas do Azure AD self-service palavra-passe reposta
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 02/01/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry
ms.custom: seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3621bbce0128fbd173120ae2a327065ee2e84e33
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57878453"
---
# <a name="troubleshoot-self-service-password-reset"></a>Resolver problemas de reposição de palavra-passe self-service

Está a ter um problema com a reposição de palavra-passe self-service (SSPR) do Azure Active Directory (Azure AD)? As informações que se segue podem ajudar a fazer as coisas funcionarem novamente.

## <a name="troubleshoot-self-service-password-reset-errors-that-a-user-might-see"></a>Resolver erros de reposição de palavra-passe self-service que poderá ver um utilizador

| Erro | Detalhes | Detalhes técnicos |
| --- | --- | --- |
| TenantSSPRFlagDisabled = 9 | Lamentamos, que não é possível repor a palavra-passe neste momento porque o administrador desativou a reposição para a sua organização palavra-passe. Não existe nenhuma outra ação que possa realizar para resolver esta situação. Contacte o administrador e peça-lhe para ativar esta funcionalidade. Para obter mais informações, consulte [obter ajuda, esqueci-me minha palavra-passe do Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-update-your-own-password#common-problems-and-their-solutions). | SSPR_0009: Detetámos que reposição de palavra-passe não foi ativada pelo seu administrador. Contacte o administrador e peça-lhe para ativar para a sua organização de reposição de palavra-passe. |
| WritebackNotEnabled = 10 |Lamentamos, que não é possível repor a palavra-passe neste momento porque o administrador não ativou um serviço necessário para a sua organização. Não existe nenhuma outra ação que possa realizar para resolver esta situação. Contacte o administrador e peça-lhe para verificar a configuração da sua organização. Para saber mais sobre este serviço necessário, veja [configurar a repetição de escrita de palavra-passe](howto-sspr-writeback.md). | SSPR_0010: Detetámos que repetição de escrita de palavra-passe não foi ativada. Contacte o administrador e peça-lhe para ativar a repetição de escrita de palavra-passe. |
| SsprNotEnabledInUserPolicy = 11 | Lamentamos, que não é possível repor a palavra-passe neste momento porque o administrador não configurou para a sua organização de reposição de palavra-passe. Não existe nenhuma outra ação que possa realizar para resolver esta situação. Contacte o administrador e peça-lhe para configurar a reposição de palavra-passe. Para saber mais sobre a palavra-passe-reposição de configuração, consulte [início rápido: O Azure AD self-service palavra-passe reposta](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-getting-started). | SSPR_0011: Sua organização não tiver definido uma política de reposição de palavra-passe. Contacte o administrador e peça-lhe definir uma política de reposição de palavra-passe. |
| UserNotLicensed = 12 | Lamentamos, que não é possível repor a palavra-passe neste momento porque necessárias incluem as licenças em falta na sua organização. Não existe nenhuma outra ação que possa realizar para resolver esta situação. Contacte o administrador e peça-lhe para verificar a atribuição de licenças. Para saber mais sobre o licenciamento, consulte [requisitos de licenciamento para a palavra-passe self-service do Azure AD repor](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-licensing). | SSPR_0012: Sua organização não tem as licenças necessárias para efetuar a reposição de palavra-passe. Contacte o administrador e peça-lhe para rever as atribuições de licenças. |
| UserNotMemberOfScopedAccessGroup = 13 | Lamentamos, que não é possível repor a palavra-passe neste momento porque o administrador não tiver configurado a sua conta para utilizar a reposição de palavra-passe. Não existe nenhuma outra ação que possa realizar para resolver esta situação. Contacte o administrador e peça-lhe para configurar a sua conta para a reposição de palavra-passe. Para saber mais sobre a configuração de conta para a reposição de palavra-passe, veja [implementar para os utilizadores de reposição de palavras-passe](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-best-practices). | SSPR_0013: Não é um membro de um grupo ativado para a reposição de palavra-passe. Contacte o administrador e peça para ser adicionado ao grupo. |
| UserNotProperlyConfigured = 14 | Lamentamos, que não é possível repor a palavra-passe neste momento porque faltam informações necessárias da sua conta. Não existe nenhuma outra ação que possa realizar para resolver esta situação. . Contacte o administrador e peça-lhe para repor a palavra-passe por si. Depois de ter novamente acesso à sua conta, terá de registar as informações necessárias. Para registar informações, siga os passos a [registar para a reposição de palavra-passe self-service](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-reset-register) artigo. | SSPR_0014: Informações de segurança adicional é necessária para repor a palavra-passe. Para continuar, contacte o administrador e peça-lhe para repor a palavra-passe. Depois de ter acesso à sua conta, pode registar informações de segurança adicionais em https://aka.ms/ssprsetup. O administrador pode adicionar informações de segurança adicionais à sua conta ao seguir os passos em [conjunto e dados de autenticação de leitura para a reposição de palavra-passe](howto-sspr-authenticationdata.md). |
| OnPremisesAdminActionRequired = 29 | Lamentamos, que não podemos repor a palavra-passe neste momento devido a um problema com a configuração de reposição de palavra-passe da sua organização. Não existe nenhuma outra ação que possa realizar para resolver esta situação. Contacte o administrador e peça-lhe para investigar. Para saber mais sobre o potencial problema, consulte [resolver problemas de repetição de escrita de palavra-passe](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-troubleshoot#troubleshoot-password-writeback). | SSPR_0029: Não é possível repor a palavra-passe devido a um erro na sua configuração no local. Contacte o administrador e peça-lhe para investigar. |
| OnPremisesConnectivityError = 30 | Lamentamos, que não podemos repor a palavra-passe neste momento devido a problemas de conectividade à sua organização. Não existe nenhuma ação necessária neste momento, mas o problema poderá ser resolvido se tentar novamente mais tarde. Se o problema persistir, contacte o administrador e peça-lhe para investigar. Para obter mais informações sobre problemas de conectividade, veja [resolver problemas de conectividade da repetição de escrita de palavra-passe](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-troubleshoot#troubleshoot-password-writeback-connectivity). | SSPR_0030: Não podemos repor a palavra-passe devido a uma má ligação ao seu ambiente no local. Contacte o administrador e peça-lhe para investigar.|


## <a name="troubleshoot-the-password-reset-configuration-in-the-azure-portal"></a>Resolver problemas relacionados com a configuração de reposição de palavra-passe no portal do Azure

| Erro | Solução |
| --- | --- |
| Não vejo a **reposição de palavra-passe** secção em Azure AD no portal do Azure. | Isto pode acontecer se não tiver um Azure AD Premium ou básica licença atribuída para o administrador efetuar a operação. <br> <br> Atribua uma licença para a conta de administrador em questão. Pode seguir os passos a [atribuir, certifique-se e resolver problemas com licenças](../users-groups-roles/licensing-groups-assign.md#step-1-assign-the-required-licenses) artigo.|
| Não vejo uma opção de configuração específico. | Muitos elementos da interface do Usuário estão ocultos até que eles são necessários. Tente ativar todas as opções que pretende ver. |
| Não vejo a **integração no local** separador. | Esta opção só se torna visível se tiver transferido o Azure AD Connect e tiver configurado a repetição de escrita de palavra-passe. Para obter mais informações, consulte [introdução ao Azure AD Connect com as definições express](../hybrid/how-to-connect-install-express.md). |

## <a name="troubleshoot-password-reset-reporting"></a>Resolver problemas relacionados com relatórios de reposição de palavra-passe

| Erro | Solução |
| --- | --- |
| Não vejo qualquer tipo de atividade de gestão de palavra-passe no **gestão de palavras-passe Self-Service** categoria de eventos de auditoria. | Isto pode acontecer se não tiver um Azure AD Premium ou básica licença atribuída para o administrador efetuar a operação. <br> <br> Pode resolver este problema ao atribuir uma licença para a conta de administrador em questão. Siga os passos a [atribuir, certifique-se e resolver problemas com licenças](../users-groups-roles/licensing-groups-assign.md#step-1-assign-the-required-licenses) artigo. |
| Registos de utilizador mostram várias vezes. | Atualmente, quando um utilizador se regista, podemos iniciar cada parte individual de dados que estão registados como um evento separado. <br> <br> Se quiser agregar dados e maior flexibilidade na forma como pode ver, pode transferir o relatório e abrir os dados como uma tabela dinâmica no Excel.

## <a name="troubleshoot-the-password-reset-registration-portal"></a>Resolver problemas relacionados com o portal de registo de reposição de palavra-passe

| Erro | Solução |
| --- | --- |
| O diretório não está ativado para a reposição de palavra-passe. **O administrador não ativou a utilizar esta funcionalidade.** | Comutador a **reposição de palavra-passe self-service ativada** sinalizador para **Selected** ou **todos os** e, em seguida, selecione **guardar**. |
| O utilizador não tem um Azure AD Premium ou básica licença atribuída. **O administrador não ativou a utilizar esta funcionalidade.** | Isto pode acontecer se não tiver um Azure AD Premium ou básica licença atribuída para o administrador efetuar a operação. <br> <br> Pode resolver este problema ao atribuir uma licença para a conta de administrador em questão. Siga os passos a [atribuir, certifique-se e resolver problemas com licenças](../users-groups-roles/licensing-groups-assign.md#step-1-assign-the-required-licenses) artigo.|
| Existe um erro ao processar o pedido. | Isto pode ser causado por vários problemas, mas em geral, este erro é causado por uma indisponibilidade do serviço ou um problema de configuração. Se vir este erro e afeta a sua empresa, contacte o suporte da Microsoft para obter mais ajuda. |

## <a name="troubleshoot-the-password-reset-portal"></a>Resolver problemas relacionados com o portal de reposição de palavra-passe

| Erro | Solução |
| --- | --- |
| O diretório não está ativado para a reposição de palavra-passe. | Comutador a **reposição de palavra-passe self-service ativada** sinalizador para **Selected** ou **todos os** e, em seguida, selecione **guardar**. |
| O utilizador não tem um Azure AD Premium ou básica licença atribuída. | Isto pode acontecer se não tiver um Azure AD Premium ou básica licença atribuída para o administrador efetuar a operação. <br> <br> Pode resolver este problema, se atribuir uma licença para a conta de administrador em questão. Siga os passos a [atribuir, certifique-se e resolver problemas com licenças](../users-groups-roles/licensing-groups-assign.md#step-1-assign-the-required-licenses) artigo. |
| O diretório está ativado para a reposição de palavra-passe, mas o utilizador tem em falta ou incorretamente formado informações de autenticação. | Antes de continuar, certifique-se de que o utilizador tem corretamente formada contacte os dados no ficheiro no diretório. Para obter mais informações, consulte [reposição de dados utilizados por palavra-passe self-service do Azure AD](howto-sspr-authenticationdata.md). |
| O diretório está ativado para a reposição de palavra-passe, mas o utilizador tem apenas um conjunto de dados de contactos no ficheiro quando a política estiver definida para exigir dois métodos de verificação. | Antes de continuar, certifique-se de que o utilizador tem, pelo menos, dois métodos de contactos corretamente configurados. Um exemplo está a ter um número do telemóvel *e* um número de telefone do escritório. |
| O diretório está ativado para a reposição de palavra-passe e o utilizador está configurado corretamente, mas o utilizador não consegue ser contatado. | Isso pode ser o resultado de um erro de serviço temporária ou se houver dados incorretos de contactos que não podemos corretamente detetar. <br> <br> Se o usuário aguardar 10 segundos, "tentar novamente" e aparecem ligações "Contacte o administrador". Se o utilizador seleciona "tentar novamente", repetir a chamada. Se o utilizador seleciona "Contacte o administrador", ele envia um e-mail de forma aos seus administradores pedir uma palavra-passe, repor a serem executadas para essa conta de utilizador. |
| O utilizador nunca recebe a reposição de palavra-passe SMS ou chamada telefónica. | Isso pode ser o resultado de um número de telefone incorreto no diretório. Certifique-se de que o número de telefone está no formato "+ ccc xxxyyyzzzzXeeee". <br> <br> Reposição de palavra-passe não suporta extensões, mesmo que especifique um no diretório. As extensões são removidas antes da chamada é enviada. Utilize um número sem uma extensão ou integrar a extensão do número de telefone no seu exchange de ramificação particular (PBX). |
| O utilizador nunca recebe o e-mail de reposição de palavra-passe. | A causa mais comum para este problema é que a mensagem será rejeitada por um filtro de spam. Verifique a spam, a pasta de itens de lixo ou excluídos do e-mail. <br> <br> Certifique-se também de que está a verificar a conta de e-mail correto para a mensagem. |
| Defini uma política de reposição de palavra-passe, mas quando uma conta de administrador utiliza a reposição de palavra-passe, não é aplicada essa política. | Microsoft gere e controlos de política para garantir o nível mais elevado de segurança de reposição da palavra-passe de administrador. |
| O utilizador é impedido de tentar uma palavra-passe, repor demasiadas vezes num dia. | Implementamos um mecanismo de limitação automática para impedir que os utilizadores da tentativa de repor as palavras-passe demasiadas vezes num curto período de tempo. Limitação ocorre quando: <br><ul><li>O utilizador tenta validar um número de telefone cinco vezes dentro de uma hora.</li><li>O utilizador tenta utilizar o Portão de perguntas de segurança cinco vezes dentro de uma hora.</li><li>O utilizador tenta repor uma palavra-passe para a mesma conta de utilizador cinco vezes dentro de uma hora.</li></ul>Para corrigir este problema, instrua o utilizador de 24 horas após a última tentativa de espera. O utilizador pode, em seguida, repor a palavra-passe. |
| O utilizador verá um erro ao validar o respetivo número de telefone. | Este erro ocorre quando o número de telefone introduzido não corresponde ao número de telefone no ficheiro. Certifique-se de que o usuário estiver inserindo o número de telefone completo, incluindo o código de área e o país, quando tentam utilizar um método baseado no telefone para a reposição de palavra-passe. |
| Existe um erro ao processar o pedido. | Isto pode ser causado por vários problemas, mas em geral, este erro é causado por uma indisponibilidade do serviço ou um problema de configuração. Se vir este erro e afeta a sua empresa, contacte o suporte da Microsoft para obter mais ajuda. |
| Violação de política no local | A palavra-passe não cumpre a política de palavra-passe do Active Directory no local. |
| Palavra-passe não cumpre a política difusa | A palavra-passe que foi utilizada é apresentado na lista de palavra-passe banidas e não pode ser utilizada. |

## <a name="troubleshoot-password-writeback"></a>Resolver problemas de repetição de escrita de palavra-passe

| Erro | Solução |
| --- | --- |
| O serviço de reposição de palavra-passe não inicia no local. Erro 6800 é apresentado no registo de eventos de aplicações da máquina do Azure AD Connect. <br> <br> Após a integração, federada, utilizadores sincronizados de hash de palavra-passe ou autenticação pass-through não é possível repor as palavras-passe. | Quando a repetição de escrita de palavra-passe está ativada, o motor de sincronização chama a biblioteca de repetição de escrita para executar a configuração (integração) ao comunicar com o serviço de integração na cloud. Os erros encontrados durante a integração ou ao iniciar o ponto de extremidade do Windows Communication Foundation (WCF) para resultados de repetição de escrita de palavra-passe em erros no registo de eventos, no seu computador do Azure AD Connect. <br> <br> Durante o reinício do serviço do Azure AD Sync (ADSync), se tiver sido configurada a repetição de escrita, o ponto de extremidade do WCF é iniciado. No entanto, se o arranque do ponto de extremidade falhar, iremos registar evento 6800 e permitir que o serviço de sincronização for iniciado. A presença deste evento significa que o ponto final de repetição de escrita de palavra-passe não foram iniciados cópia de segurança. Detalhes de registo de eventos para este evento 6800, juntamente com o registo de eventos que entradas geram pelo componente PasswordResetService, indicam por que não é possível iniciar o ponto de extremidade. Reveja estes erros de registo de eventos e tente reiniciar o Azure AD Connect, se a repetição de escrita de palavra-passe ainda não está a funcionar. Se o problema persistir, tente desativar e, em seguida, volte a ativar a repetição de escrita de palavra-passe.
| Quando um utilizador tenta repor uma palavra-passe ou desbloquear uma conta com repetição de escrita de palavra-passe ativada, a operação falhará. <br> <br> Além disso, verá um evento no registo de eventos do Azure AD Connect que contém: "O motor de sincronização devolveu um erro hr = 800700CE, mensagem = o nome de ficheiro ou a extensão é demasiado longa" após a ocorrência da operação de desbloqueio. | Localizar a conta do Active Directory para o Azure AD Connect e repor a palavra-passe para que ele contém mais do que 127 carateres. Em seguida, abra a **serviço de sincronização** da **iniciar** menu. Navegue até **conectores** e localize a **conector do Active Directory**. Selecione-o e, em seguida, selecione **propriedades**. Navegue para o **credenciais** página e introduza a palavra-passe nova. Selecione **OK** para fechar a página. |
| No último passo do processo de instalação do Azure AD Connect, verá um erro que indica que esse repetição de escrita de palavra-passe não foi possível configurar. <br> <br> O registo de eventos de aplicações do Azure AD Connect contém o erro 32009 com o texto "Erro ao obter autenticação do token." | Este erro ocorre nos dois seguintes casos: <br><ul><li>Especificou uma palavra-passe incorreta para a conta de administrador global especificada no início do processo de instalação do Azure AD Connect.</li><li>Foi efetuada uma tentativa utilizar um utilizador federado para a conta de administrador global especificada no início do processo de instalação do Azure AD Connect.</li></ul> Para corrigir este problema, certifique-se de que não está a utilizar uma conta federada para o administrador global que especificou no início do processo de instalação. Certifique-se também de que a palavra-passe especificada está correta. |
| O registo de eventos do Azure AD Connect máquina contém erro 32002, que é emitido ao executar PasswordResetService. <br> <br> Lê o erro: "Erro ao ligar ao ServiceBus. O fornecedor do token não foi possível fornecer um token de segurança." | O ambiente no local não é possível estabelecer ligação ao ponto final do Azure Service Bus na nuvem. Normalmente, este erro é causado por uma regra de firewall a bloquear uma ligação de saída para um endereço web ou a porta específica. Ver [pré-requisitos de conectividade](../hybrid/how-to-connect-install-prerequisites.md) para obter mais informações. Depois de atualizar essas regras, reinicie o computador do Azure AD Connect e repetição de escrita de palavra-passe deve começar a trabalhar novamente. |
| Após trabalhar durante algum tempo, federado, utilizadores sincronizados de hash de palavra-passe ou autenticação pass-through não é possível repor as palavras-passe. | Em alguns casos raros, o serviço de repetição de escrita de palavra-passe poderão não conseguir reiniciar quando o Azure AD Connect foi reiniciado. Nestes casos, em primeiro lugar, verifique se a repetição de escrita de palavra-passe é apresentado ser ativado no local. Pode verificar com o Assistente do Azure AD Connect ou o PowerShell (consulte a secção de HowTos anterior). Se o recurso parece estar ativado, tente ativar ou desativar a funcionalidade novamente através da IU ou do PowerShell. Se não funcionar, experimente uma desinstalação completa e reinstalar do Azure AD Connect. |
| Autenticação federada de pass-through ou os utilizadores sincronizados de hash de palavra-passe, que tentam repor as palavras-passe, veja um erro ao tentar enviar a palavra-passe. O erro indica que ocorreu um problema de serviço. <br ><br> Para além deste problema, durante as operações de reposição de palavra-passe, poderá ver um erro que o agente de gestão foi negado o acesso nos seus registos de eventos no local. | Se vir estes erros no seu registo de eventos, confirme que a conta de agente de gestão do Active Directory (ADMA) que foi especificada no assistente no momento da configuração tem as permissões necessárias para a repetição de escrita de palavra-passe. <br> <br> Depois desta permissão é concedida, pode demorar até uma hora para as permissões para repassadas o `sdprop` tarefa em segundo plano no controlador de domínio (DC). <br> <br> Palavra-passe reposta para trabalhar, a permissão tem possível carimbar no descritor de segurança do objeto de utilizador que palavra-passe é a ser reposto. Até que esta permissão que aparece no objeto user, reposição de palavra-passe é continuar a falhar com uma mensagem de acesso negado. |
| Autenticação federada de pass-through ou os utilizadores sincronizados de hash de palavra-passe, que tentam repor as palavras-passe, veja um erro depois de submeter a palavra-passe. O erro indica que ocorreu um problema de serviço. <br> <br> Para além deste problema, durante as operações de reposição de palavra-passe, poderá ver um erro em seus logs de eventos do serviço do Azure AD Connect que indica um erro "Objeto não foi possível encontrar". | Este erro normalmente indica que o motor de sincronização não é possível encontrar o objeto de utilizador no espaço conector do Azure AD ou do metaverso ligado (MV) ou o objeto do espaço conector do Azure AD. <br> <br> Para resolver este problema, certifique-se de que o usuário realmente está sincronizado no local para o Azure AD com a instância atual do Azure AD Connect e inspecionar o estado dos objetos na MV e espaços conectores. Confirme que o objeto de serviços de certificados do Active Directory (AD CS) está ligado ao objeto de MV via a regra "Microsoft.InfromADUserAccountEnabled.xxx".|
| Autenticação federada, pass-through ou os utilizadores sincronizados de hash de palavra-passe, que tentam repor as palavras-passe ver um erro depois de submeter a palavra-passe. O erro indica que ocorreu um problema de serviço. <br> <br> Para além deste problema, durante as operações de reposição de palavra-passe, poderá ver um erro em seus logs de eventos do serviço do Azure AD Connect que indica que existe um erro de "Foram encontradas várias correspondências". | Isto indica que o motor de sincronização detetou que o objeto de MV está ligado a mais do que um objeto de AD CS via "Microsoft.InfromADUserAccountEnabled.xxx". Isso significa que o utilizador tem uma conta ativada em mais do que uma floresta. Este cenário não é suportado para a repetição de escrita de palavra-passe. |
| Operações de palavra-passe falharem com um erro de configuração. O registo de eventos do aplicativo contém o erro de Azure AD Connect 6329 com o texto "0x8023061f (a operação falhou porque a sincronização de palavra-passe não está ativada neste agente de gestão)". | Este erro ocorre que se a configuração do Azure AD Connect é alterada para adicionar uma nova floresta do Active Directory (ou para remover e readd uma floresta existente) após a funcionalidade de repetição de escrita de palavra-passe já foi ativada. Operações de palavras-passe para utilizadores nessas recentemente adicionaram florestas falhar. Para corrigir o problema, desative e, em seguida, volte a ativar a funcionalidade de repetição de escrita de palavra-passe depois das alterações de configuração de floresta tiveram sido concluídas. |

## <a name="password-writeback-event-log-error-codes"></a>Códigos de erro de registo de eventos de repetição de escrita de palavra-passe

É recomendado quando resolver problemas com a repetição de escrita de palavra-passe inspecionar o registo de eventos de aplicações no seu computador do Azure AD Connect. Este registo de eventos contém eventos de duas origens de interesse para repetição de escrita de palavra-passe. A origem de PasswordResetService descreve as operações e problemas relacionados com a operação de repetição de escrita de palavra-passe. A origem do ADSync descreve as operações e problemas relacionados com a definição de palavras-passe no seu ambiente do Active Directory.

### <a name="if-the-source-of-the-event-is-adsync"></a>Se a origem do evento é ADSync

| Código | Nome ou mensagem | Descrição |
| --- | --- | --- |
| 6329 | BAIL: MMS(4924) 0x80230619: "Uma restrição impede que a palavra-passe a ser alterado para um serviço atual especificado." | Este evento ocorre quando o serviço de repetição de escrita de palavra-passe tenta definir uma palavra-passe no diretório no local que não cumpre a idade de palavra-passe, histórico, complexidade ou requisitos de filtragem do domínio. <br> <br> Se tiver uma antiguidade mínima da palavra-passe e tiver alterado recentemente a palavra-passe nessa janela de tempo, não pode alterar a palavra-passe novamente até atingir a idade especificada no seu domínio. Para fins de teste, a idade mínima deve ser definida como 0. <br> <br> Se tiver requisitos de histórico de palavras-passe ativados, tem de selecionar uma palavra-passe não foi utilizada nos últimos *N* vezes, onde *N* é a definição de histórico de palavras-passe. Se selecionar uma palavra-passe que foi utilizada nos últimos *N* exceder o tempo, em seguida, verá uma falha em vez disso. Para fins de teste, o histórico de palavras-passe deve ser definido como 0. <br> <br> Se tiver requisitos de complexidade de palavra-passe, todos eles são impostos quando o utilizador tentou alterar ou repor uma palavra-passe. <br> <br> Se tiver filtros de palavra-passe ativados e um usuário seleciona uma palavra-passe que não cumpre os critérios de filtragem, em seguida, a reposição ou falha da operação de alteração. |
| 6329 | MMS(3040): admaexport.cpp(2837): O servidor não contém o controle de política de palavra-passe LDAP. | Este problema ocorre se o controlo LDAP_SERVER_POLICY_HINTS_OID (1.2.840.113556.1.4.2066) não está ativado nos DCs. Para utilizar a funcionalidade de repetição de escrita de palavra-passe, tem de ativar o controlo. Para fazer isso, os controladores de domínio tem de ser no Windows Server 2008 R2 ou posterior. |
| HR 8023042 | Motor de sincronização devolveu um erro hr = 80230402, mensagem = uma tentativa de obter um objeto falhou porque existem entradas duplicadas com a mesma âncora. | Este erro ocorre quando o mesmo ID de utilizador está ativado em vários domínios. Um exemplo é se estiver a sincronizar as florestas de contas e recursos e ter o mesmo ID de utilizador presente e habilitado em cada floresta. <br> <br> Este erro também pode ocorrer se usar um atributo de âncora exclusivos, como um alias ou UPN e dois usuários compartilham esse mesmo atributo de âncora. <br> <br> Para resolver este problema, certifique-se de que não tem quaisquer utilizadores duplicados dentro dos domínios e que utiliza um atributo de âncora exclusivo para cada utilizador. |

### <a name="if-the-source-of-the-event-is-passwordresetservice"></a>Se a origem do evento é PasswordResetService

| Código | Nome ou mensagem | Descrição |
| --- | --- | --- |
| 31001 | PasswordResetStart | Este evento indica que o serviço no local detetado que pedido para uma autenticação federada de pass-through ou utilizador sincronizados de hash de palavra-passe que são originados por nuvem de reposição de uma palavra-passe. Este evento é o primeiro evento em todas as operações de repetição de escrita de reposição de palavra-passe. |
| 31002 | PasswordResetSuccess | Este evento indica que um utilizador selecionado uma nova palavra-passe durante uma operação de reposição de palavra-passe. Determinámos que esta palavra-passe cumpre os requisitos de palavra-passe empresarial. A palavra-passe ter sido com êxito gravada de volta para o ambiente do Active Directory local. |
| 31003 | PasswordResetFail | Este evento indica que um utilizador selecionado uma palavra-passe e a palavra-passe chegou com êxito para o ambiente no local. Mas, quando estamos tentou definir a palavra-passe no ambiente do Active Directory local, Ocorreu uma falha. Esta falha pode ocorrer por diversos motivos: <br><ul><li>Palavra-passe do utilizador não cumprem a idade, histórico, complexidade ou filtrar os requisitos para o domínio. Para resolver este problema, crie uma nova palavra-passe.</li><li>A conta de serviço do ADMA não tem as permissões adequadas para definir a nova palavra-passe da conta de utilizador em questão.</li><li>A conta de utilizador estiver num grupo protegido, como o grupo de administrador Corporativo ou de domínio, que não permite operações de conjunto de palavras-passe.</li></ul>|
| 31004 | OnboardingEventStart | Este evento ocorre se ativar a repetição de escrita de palavra-passe com o Azure AD Connect e ter começamos a integração de sua organização para o serviço de web de repetição de escrita de palavra-passe. |
| 31005 | OnboardingEventSuccess | Este evento indica que o processo de integração foi concluída com êxito e que a capacidade de repetição de escrita de palavra-passe está pronta a utilizar. |
| 31006 | ChangePasswordStart | Este evento indica que o serviço no local detetou um pedido de alteração de palavra-passe para uma autenticação federada de pass-through ou utilizador sincronizados de hash de palavra-passe que provém da cloud. Este evento é o primeiro evento em todas as operações de repetição de escrita de alteração de palavra-passe. |
| 31007 | ChangePasswordSuccess | Este evento indica que um utilizador selecionado uma nova palavra-passe durante uma operação de alteração de palavra-passe, Determinámos que a palavra-passe cumpre os requisitos de palavra-passe empresarial e que a palavra-passe ter sido com êxito gravada de volta para o ambiente do Active Directory local. |
| 31008 | ChangePasswordFail | Este evento indica que um utilizador selecionado uma palavra-passe e que a palavra-passe chegou com êxito para o ambiente no local, mas quando estamos tentou definir a palavra-passe no ambiente do Active Directory local, Ocorreu uma falha. Esta falha pode ocorrer por diversos motivos: <br><ul><li>Palavra-passe do utilizador não cumprem a idade, histórico, complexidade ou filtrar os requisitos para o domínio. Para resolver este problema, crie uma nova palavra-passe.</li><li>A conta de serviço do ADMA não tem as permissões adequadas para definir a nova palavra-passe da conta de utilizador em questão.</li><li>A conta de utilizador estiver num grupo protegido, tal como administradores de domínio ou enterprise, que não permite operações de conjunto de palavras-passe.</li></ul> |
| 31009 | ResetUserPasswordByAdminStart | O serviço no local detetou a que pedido para uma autenticação federada, pass-through ou sincronizados de hash de palavra-passe de utilizador provenientes de administrador em nome de um utilizador de reposição de uma palavra-passe. Este evento é o primeiro evento em todas as operações de repetição de escrita de reposição de palavra-passe que é iniciada por um administrador. |
| 31010 | ResetUserPasswordByAdminSuccess | O administrador selecionado uma nova palavra-passe durante uma operação de reposição de palavra-passe iniciada pelo administrador. Determinámos que esta palavra-passe cumpre os requisitos de palavra-passe empresarial. A palavra-passe ter sido com êxito gravada de volta para o ambiente do Active Directory local. |
| 31011 | ResetUserPasswordByAdminFail | O administrador selecionado uma palavra-passe em nome de um utilizador. A palavra-passe chegou com êxito para o ambiente no local. Mas, quando estamos tentou definir a palavra-passe no ambiente do Active Directory local, Ocorreu uma falha. Esta falha pode ocorrer por diversos motivos: <br><ul><li>Palavra-passe do utilizador não cumprem a idade, histórico, complexidade ou filtrar os requisitos para o domínio. Experimente uma nova palavra-passe para resolver este problema.</li><li>A conta de serviço do ADMA não tem as permissões adequadas para definir a nova palavra-passe da conta de utilizador em questão.</li><li>A conta de utilizador estiver num grupo protegido, tal como administradores de domínio ou enterprise, que não permite operações de conjunto de palavras-passe.</li></ul>  |
| 31012 | OffboardingEventStart | Este evento ocorre se desativar a repetição de escrita de palavra-passe com o Azure AD Connect e indica que começamos a exclusão sua organização para o serviço de web de repetição de escrita de palavra-passe. |
| 31013| OffboardingEventSuccess| Este evento indica que o processo de exclusão foi concluída com êxito e que capacidade de repetição de escrita de palavra-passe foi desativada com êxito. |
| 31014| OffboardingEventFail| Este evento indica que o processo de exclusão não foi concluída com êxito. Isto pode dever-se um erro de permissões da conta de administrador na cloud ou no local especificado durante a configuração. Também pode ocorrer o erro se estiver a tentar utilizar um administrador global da cloud federado ao desativar a repetição de escrita de palavra-passe. Para corrigir este problema, verifique as permissões administrativas e certifique-se de que não está a utilizar uma conta federada ao configurar a capacidade de repetição de escrita de palavra-passe.|
| 31015| WriteBackServiceStarted| Este evento indica que o serviço de repetição de escrita de palavra-passe foi iniciado com êxito. Ele está pronto para aceitar pedidos de gestão de palavra-passe da cloud.|
| 31016| WriteBackServiceStopped| Este evento indica que o serviço de repetição de escrita de palavra-passe parou. Quaisquer pedidos de gestão de palavra-passe da cloud não será concluída com êxito.|
| 31017| AuthTokenSuccess| Este evento indica obteve com êxito um token de autorização para o administrador global especificado durante a configuração do Azure AD Connect para iniciar o processo de exclusão ou inclusão.|
| 31018| KeyPairCreationSuccess| Este evento indica que foi criada com êxito a chave de encriptação da palavra-passe. Esta chave é utilizada para encriptar palavras-passe a partir da cloud para serem enviados para o seu ambiente no local.|
| 32000| UnknownError| Este evento indica que ocorreu um erro desconhecido durante uma operação de gestão de palavra-passe. Ver o texto da exceção no evento para obter mais detalhes. Se estiver a ter problemas, tente desativar e, em seguida, reativar a repetição de escrita de palavra-passe. Se isso não ajuda, inclua uma cópia do seu registo de eventos, juntamente com o controlo insider especificado do ID para o engenheiro de suporte.|
| 32001| ServiceError| Este evento indica que ocorreu um erro ao ligar para a palavra-passe de cloud de serviço de reposição. Este erro ocorre normalmente quando o serviço no local não foi possível ligar ao serviço web de reposição de palavra-passe.|
| 32002| ServiceBusError| Este evento indica que ocorreu um erro ao ligar à instância do seu inquilino do Service Bus. Isto pode acontecer se estiver a bloquear ligações de saída no seu ambiente no local. Verifique a firewall para se certificar de que permite que as ligações através de TCP 443 e, a https://ssprsbprodncu-sb.accesscontrol.windows.net/e, em seguida, tente novamente. Se ainda estiver a ter problemas, tente desativar e, em seguida, reativar a repetição de escrita de palavra-passe.|
| 32003| InPutValidationError| Este evento indica que a entrada transmitida a nossa API de serviço da web era inválida. Repita a operação.|
| 32004| DecryptionError| Este evento indica que ocorreu um erro ao desencriptar a palavra-passe que chegaram a partir da cloud. Isto pode dever-se um erro de correspondência chave de desencriptação entre o serviço de nuvem e de seu ambiente no local. Para resolver este problema, desative e volte a ativar repetição de escrita de palavra-passe no seu ambiente no local.|
| 32005| ConfigurationError| Durante a integração, vamos salvar informações específicas do inquilino num ficheiro de configuração no seu ambiente no local. Este evento indica que ocorreu um erro ao guardar este ficheiro ou que quando o serviço foi iniciado, não havia um erro ao ler o ficheiro. Para corrigir este problema, tente desativar e, em seguida, reativar a repetição de escrita de palavra-passe para forçar uma Reescrita do ficheiro de configuração.|
| 32007| OnBoardingConfigUpdateError| Durante a integração, vamos enviar dados da cloud com o local do serviço de reposição de palavra-passe. Que dados são gravados, em seguida, para um ficheiro na memória antes de ser enviada para o serviço de sincronização para ser armazenado em segurança no disco. Este evento indica que existe um problema com a escrever ou atualizar esses dados na memória. Para corrigir este problema, tente desativar e, em seguida, reativar a repetição de escrita de palavra-passe para forçar uma reescrita deste ficheiro de configuração.|
| 32008| ValidationError| Este evento indica que recebeu uma resposta inválida do serviço web de reposição de palavra-passe. Para corrigir este problema, tente desativar e, em seguida, reativar a repetição de escrita de palavra-passe.|
| 32009| AuthTokenError| Este evento indica que não foi possível obter uma autorização token para a conta de administrador global especificada durante a configuração do Azure AD Connect. Este erro pode ser causado por um nome de utilizador inválido ou a palavra-passe especificada para a conta de administrador global. Este erro também pode ocorrer se a conta de administrador global especificada está federada. Para corrigir este problema, execute novamente a configuração com o nome de utilizador correto e a palavra-passe e certifique-se de que o administrador é uma conta (a apenas na cloud ou palavra-passe sincronizada) gerida.|
| 32010| CryptoError| Este evento indica que ocorreu um erro ao gerar a chave de encriptação da palavra-passe ou desencriptar uma palavra-passe que são recebidos do serviço cloud. Este erro provavelmente indica um problema com o seu ambiente. Ver os detalhes do seu registo de eventos para obter mais informações sobre como resolver este problema. Também pode tentar desativar e, em seguida, reativar o serviço de repetição de escrita de palavra-passe.|
| 32011| OnBoardingServiceError| Este evento indica que o serviço no local não foi corretamente comunicar com o serviço web de reposição de palavra-passe para iniciar o processo de integração. Isto pode acontecer como resultado de uma regra de firewall ou se existe um problema ao obter uma autenticação de token para o seu inquilino. Para corrigir este problema, certifique-se de que não esteja bloquear ligações de saída através de TCP 443 e TCP 9350-9354 ou para https://ssprsbprodncu-sb.accesscontrol.windows.net/. Certifique-se também que a conta de administrador do Azure AD que está a utilizar para carregar não está federado.|
| 32013| OffBoardingError| Este evento indica que o serviço no local corretamente não foi possível comunicar com o serviço web de reposição de palavra-passe para iniciar o processo de exclusão. Isto pode acontecer como resultado de uma regra de firewall ou se existe um problema ao obter uma autorização token para o seu inquilino. Para corrigir este problema, certifique-se de que não está a bloquear ligações de saída através de 443 ou a https://ssprsbprodncu-sb.accesscontrol.windows.net/, e que a conta de administrador do Azure Active Directory está a utilizar para descarregar não está federada.|
| 32014| ServiceBusWarning| Este evento indica que tínhamos que repetir para ligar à instância do seu inquilino do Service Bus. Em condições normais, isso deve não ser uma preocupação, mas se vir este evento muitas vezes, considere a sua ligação de rede ao Service Bus, especialmente se for uma ligação de latência alta ou baixa largura de banda.|
| 32015| ReportServiceHealthError| Para monitorizar o estado de funcionamento do seu serviço de repetição de escrita de palavra-passe, podemos enviar dados de heartbeat ao nosso serviço web de reposição de palavra-passe a cada cinco minutos. Este evento indica que ocorreu um erro ao enviar estas informações de estado de funcionamento para o serviço da web na cloud. Estas informações de estado de funcionamento não inclui um informações de identificação do objeto (OII) ou dados de informações de identificação pessoal (PII) e é puramente um heartbeat e estatísticas de serviço básico, para que possamos fornecer informações de estado do serviço na cloud.|
| 33001| ADUnKnownError| Este evento indica que ocorreu um erro desconhecido retornado pelo Active Directory. Verifique o registo de eventos do servidor do Azure AD Connect para eventos da origem de ADSync para obter mais informações.|
| 33002| ADUserNotFoundError| Este evento indica que o utilizador que está a tentar repor ou alterar uma palavra-passe não foi encontrado no diretório no local. Este erro pode ocorrer quando o utilizador tiver sido eliminado no local, mas não na cloud. Este erro também pode ocorrer se houver um problema com a sincronização. Verifique os registos de sincronização e os última alguns detalhes de sincronização executar para obter mais informações.|
| 33003| ADMutliMatchError| Ao repor uma palavra-passe ou pedido de alteração são originados pela cloud, usamos a âncora de cloud especificada durante o processo de configuração do Azure AD Connect para determinar como ligar esse pedido de volta para um utilizador no seu ambiente no local. Este evento indica que encontramos dois utilizadores no seu diretório no local com o mesmo atributo de âncora de cloud. Verifique os registos de sincronização e os última alguns detalhes de sincronização executar para obter mais informações.|
| 33004| ADPermissionsError| Este evento indica que a conta de serviço do agente de gestão do Active Directory (ADMA) não tem as permissões adequadas na conta em questão, para definir uma nova palavra-passe. Certifique-se de que a conta ADMA na floresta do utilizador foi reposto e alterar as permissões de palavra-passe em todos os objetos na floresta. Para obter mais informações sobre como definir as permissões, consulte o passo 4: Configure as permissões adequadas do Active Directory.|
| 33005| ADUserAccountDisabled| Este evento indica que estamos tentou repor ou alterar uma palavra-passe para uma conta que foi desativada no local. Ativar a conta e repita a operação.|
| 33006| ADUserAccountLockedOut| Este evento indica que estamos tentou repor ou alterar uma palavra-passe para uma conta que foi bloqueada no local. Bloqueios podem ocorrer quando um utilizador tiver tentado uma alteração ou repor a operação de palavra-passe demasiadas vezes num curto período de tempo. Desbloquear a conta e tente a operação de novo.|
| 33007| ADUserIncorrectPassword| Este evento indica que o utilizador especificado uma palavra-passe atual incorreta, quando efetuar uma palavra-passe alterar a operação. Especifique a palavra-passe atual correta e tente novamente.|
| 33008| ADPasswordPolicyError| Este evento ocorre quando o serviço de repetição de escrita de palavra-passe tenta definir uma palavra-passe no diretório no local que não cumpre a idade de palavra-passe, histórico, complexidade ou requisitos de filtragem do domínio. <br> <br> Se tiver uma antiguidade mínima da palavra-passe e tiver alterado recentemente a palavra-passe nessa janela de tempo, não pode alterar a palavra-passe novamente até atingir a idade especificada no seu domínio. Para fins de teste, a idade mínima deve ser definida como 0. <br> <br> Se tiver requisitos de histórico de palavras-passe ativados, tem de selecionar uma palavra-passe não foi utilizada nos últimos *N* vezes, onde *N* é a definição de histórico de palavras-passe. Se selecionar uma palavra-passe que foi utilizada nos últimos *N* exceder o tempo, em seguida, verá uma falha em vez disso. Para fins de teste, o histórico de palavras-passe deve ser definido como 0. <br> <br> Se tiver requisitos de complexidade de palavra-passe, todos eles são impostos quando o utilizador tentou alterar ou repor uma palavra-passe. <br> <br> Se tiver filtros de palavra-passe ativados e um usuário seleciona uma palavra-passe que não cumpre os critérios de filtragem, em seguida, a reposição ou falha da operação de alteração.|
| 33009| ADConfigurationError| Este evento indica que ocorreu um problema ao escrever uma palavra-passe de volta para o seu diretório no local devido a um problema de configuração com o Active Directory. Verifique o registo de eventos de aplicações da máquina do Azure AD Connect para mensagens do serviço ADSync para obter mais informações em que ocorreu um erro.|

## <a name="troubleshoot-password-writeback-connectivity"></a>Resolver problemas de conectividade da repetição de escrita de palavra-passe

Se estiver a ter interrupções do serviço com o componente de repetição de escrita de palavra-passe do Azure AD Connect, seguem-se alguns passos rápidos que pode tomar para resolver este problema:

* [Confirmar a conectividade de rede](#confirm-network-connectivity)
* [Reinicie o serviço do Azure AD Connect Sync](#restart-the-azure-ad-connect-sync-service)
* [Desativar e reativar a funcionalidade de repetição de escrita de palavra-passe](#disable-and-re-enable-the-password-writeback-feature)
* [Instalar a versão mais recente do Azure AD Connect](#install-the-latest-azure-ad-connect-release)
* [Resolver problemas de repetição de escrita de palavra-passe](#troubleshoot-password-writeback)

Em geral, para recuperar o seu serviço da forma mais rápida, recomendamos que execute estes passos pela ordem discutido anteriormente.

### <a name="confirm-network-connectivity"></a>Confirmar a conectividade de rede

O ponto mais comuns de falha é que a firewall e de ou portas de proxy e limites de ociosidade foram configurados incorretamente. 

Para o Azure AD Connect versão 1.1.443.0 e posteriores, necessitar de HTTPS de saída acesso ao seguinte:

   - passwordreset.microsoftonline.com
   - servicebus.windows.net

Para mais granularidade, veja a lista atualizada de [intervalos de IP do Microsoft Azure Datacenter](https://www.microsoft.com/download/details.aspx?id=41653) atualizado a cada quarta-feira e entra em vigor a próxima segunda-feira.

Para obter mais informações, reveja os pré-requisitos conectividade a [pré-requisitos para o Azure AD Connect](../hybrid/how-to-connect-install-prerequisites.md) artigo.

### <a name="restart-the-azure-ad-connect-sync-service"></a>Reinicie o serviço do Azure AD Connect Sync

Para resolver problemas de conectividade ou outros problemas temporários com o serviço, reinicie o serviço do Azure AD Connect Sync:

1. Como administrador, selecione **iniciar** no servidor a executar o Azure AD Connect.
1. ENTER **Services. msc** no campo de pesquisa e selecione **Enter**.
1. Procure o **Microsoft Azure AD Sync** entrada.
1. A entrada de serviço com o botão direito, selecione **reiniciar**e, em seguida, aguarde a conclusão da operação.

   ![Reinicie o serviço do Azure AD Sync][Service restart]

Estes passos restabelecer a ligação com o serviço cloud e resolva quaisquer interrupções que possam estar a ter. Se reiniciar o serviço ADSync não resolver seu problema, recomendamos que tente desativar e, em seguida, volte a ativar a funcionalidade de repetição de escrita de palavra-passe.

### <a name="disable-and-re-enable-the-password-writeback-feature"></a>Desativar e reativar a funcionalidade de repetição de escrita de palavra-passe

Para resolver problemas de conectividade, desativar e, em seguida, volte a ativar a funcionalidade de repetição de escrita de palavra-passe:

   1. Como administrador, abra o Assistente de configuração de ligar do Azure AD.
   1. Na **ligar para o Azure AD**, insira as suas credenciais de administrador global do Azure AD.
   1. Na **ligar ao AD DS**, introduza as credenciais de administrador de serviços de domínio do AD.
   1. Na **identificar os utilizadores de forma exclusiva**, selecione a **próxima** botão.
   1. Na **funcionalidades opcionais**, desmarque a **repetição de escrita de palavra-passe** caixa de verificação.
   1. Selecione **próxima** por meio das restantes páginas de diálogo sem alterar nada até chegar à **pronto para configurar** página.
   1. Certifique-se de que o **pronto para configurar a página** mostra o **repetição de escrita de palavra-passe** opção como **desativada** e, em seguida, selecione o verde **configurar** botão para consolidar as alterações.
   1. Na **concluído**, desmarque a **sincronizar agora** opção e, em seguida, selecione **concluir** para fechar o assistente.
   1. Volte a abrir o Assistente de configuração de ligar do Azure AD.
   1. Repita os passos 2-8, mas certifique-se de que seleciona os **repetição de escrita de palavra-passe** opção a **funcionalidades opcionais** página para voltar a ativar o serviço.

Estes passos restabelecer a ligação com o serviço cloud e resolva quaisquer interrupções que possam estar a ter.

Se desativar e, em seguida, reativar a funcionalidade de repetição de escrita de palavra-passe não resolverem seu problema, recomendamos que reinstalar o Azure AD Connect.

### <a name="install-the-latest-azure-ad-connect-release"></a>Instalar a versão mais recente do Azure AD Connect

Reinstalar o Azure AD Connect pode resolver a configuração e problemas de conectividade entre os nossos serviços cloud e o seu ambiente do Active Directory local.

Recomendamos que efetue este passo apenas depois de tentar as duas primeiras etapas descritas anteriormente.

> [!WARNING]
> Se tiver personalizado as regras de sincronização de out-of-the-box, *o backup deles antes de continuar com a atualização e, em seguida, voltá-los manualmente depois de terminar.*
>

1. Baixe a versão mais recente do Azure AD Connect do [Microsoft Download Center](https://go.microsoft.com/fwlink/?LinkId=615771).
1. Uma vez que já tenha instalado o Azure AD Connect, terá de efetuar uma atualização in-loco para atualizar a instalação do Azure AD Connect para a versão mais recente.
1. Executar o pacote transferido e siga na tela instruções para atualizar a sua máquina do Azure AD Connect.

Os passos anteriores devem voltar a estabelecer a ligação com o serviço cloud e resolva quaisquer interrupções que possam estar a ter.

Se instalar a versão mais recente do servidor do Azure AD Connect não resolver seu problema, recomendamos que tente desativar e, em seguida, reativar a repetição de escrita de palavra-passe como passo final depois de instalar a versão mais recente.

## <a name="verify-that-azure-ad-connect-has-the-required-permission-for-password-writeback"></a>Certifique-se de que o Azure AD Connect tem a permissão necessária para a repetição de escrita de palavra-passe

O Azure AD Connect requer o Active Directory **Repor palavra-passe** permissão para efetuar a repetição de escrita de palavra-passe. Para saber se o Azure AD Connect tem a permissão necessária para uma conta de utilizador do Active Directory de determinado no local, pode utilizar a funcionalidade de permissão efetiva do Windows:

1. Inicie sessão no servidor do Azure AD Connect e iniciar o **Synchronization Service Manager** ao selecionar **iniciar** > **serviço de sincronização**.
1. Sob o **conectores** separador, selecione no local **serviços de domínio do Active Directory** conector e, em seguida, selecione **propriedades**.  
   ![Permissão efetiva - passo 2](./media/active-directory-passwords-troubleshoot/checkpermission01.png)  
  
1. Na janela de pop-up, selecione **ligar à floresta do Active Directory** e anote o **nome de utilizador** propriedade. Esta propriedade é a conta de AD DS utilizada pelo Azure AD Connect para efetuar a sincronização de diretórios. Para o Azure AD Connect efetuar a repetição de escrita de palavra-passe, a conta do AD DS tem de ter a repor a permissão de palavra-passe.  
   
   ![Permissão efetiva - passo 3](./media/active-directory-passwords-troubleshoot/checkpermission02.png) 
  
1. Inicie sessão para um controlador de domínio no local e inicie o **Active Directory Users and Computers** aplicação.
1. Selecione **View** e certifique-se a **funcionalidades avançadas** opção está ativada.  
   
   ![Permissão efetiva - passo 5](./media/active-directory-passwords-troubleshoot/checkpermission03.png) 
  
1. Procure a conta de utilizador do Active Directory que pretende verificar. O nome da conta com o botão direito e selecione **propriedades**.  
   
   ![Permissão efetiva - passo 6](./media/active-directory-passwords-troubleshoot/checkpermission04.png) 

1. Na janela de pop-up, vá para o **Security** separador e selecione **avançadas**.  
   
   ![Permissão efetiva - passo 7](./media/active-directory-passwords-troubleshoot/checkpermission05.png) 
   
1. Na **definições avançadas de segurança de administrador** janela de pop-up, vá para o **acesso Efetivo** separador.
1. Selecione **selecionar um utilizador**, selecione a conta do AD DS utilizada pelo Azure AD Connect (ver passo 3) e, em seguida, selecione **ver acesso Efetivo**.

   ![Permissão efetiva - passo 9](./media/active-directory-passwords-troubleshoot/checkpermission06.png) 
  
1. Desloque para baixo e procure **Repor palavra-passe**. Se a entrada tem uma marca de verificação, a conta do AD DS tem permissão para repor a palavra-passe da conta de utilizador do Active Directory selecionada.  
   
   ![Permissão efetiva - passo 10](./media/active-directory-passwords-troubleshoot/checkpermission07.png)  

## <a name="azure-ad-forums"></a>Fóruns do Azure AD

Se tiver alguma pergunta geral sobre o Azure AD e de reposição de palavra-passe self-service, pode perguntar à Comunidade para obter assistência sobre a [fóruns do Azure AD](https://social.msdn.microsoft.com/Forums/en-US/home?forum=WindowsAzureAD). Membros da Comunidade incluem engenheiros, gerentes de produto, MVPs e outros profissionais de TI.

## <a name="contact-microsoft-support"></a>Contacte o Suporte da Microsoft

Se não conseguir encontrar a resposta a um problema, a nossas equipas de suporte estão sempre disponíveis para ajudá-lo ainda mais.

Para o ajudar corretamente, pedimos que forneçam tantos detalhes quanto possível, ao abrir um incidente. Estes detalhes incluem:

* **Descrição geral do erro**: O que é o erro? Qual era o comportamento que foi reparado? Como podemos reproduzir o erro? Fornece tantos detalhes quanto possível.
* **Página**: Qual página estava quando notou que o erro? Inclua o URL, se pode e uma captura de ecrã da página.
* **Suporte a código**: Qual era o código de suporte que foi gerado quando o usuário viu o erro?
  * Para localizar este código, reproduzir o erro, em seguida, selecione o **suportar código** link na parte inferior do ecrã e enviar o engenheiro de suporte o GUID que resulta.

    ![Encontrar o código de suporte na parte inferior do ecrã][Support code]

  * Se estiver numa página sem um suporte de código na parte inferior, selecione a tecla F12 e procure o SID e CID e enviar esses dois resultados para o engenheiro de suporte.
* **Data, hora e fuso horário**: Incluir a precisa data e hora *com o fuso horário* que ocorreu o erro.
* **ID de utilizador**: Quem foi o utilizador que viu o erro? Um exemplo é *usuário\@contoso.com*.
    * Este é um utilizador federado?
    * Este é um utilizador de autenticação pass-through?
    * Este é um utilizador sincronizados de hash de palavra-passe?
    * Este é um utilizador apenas na cloud?
* **Licenciamento**: O utilizador tem uma licença do Azure AD Premium ou do Azure AD básico atribuída?
* **Registo de eventos do aplicativo**: Se estiver a utilizar a repetição de escrita de palavra-passe e o erro é na sua infraestrutura no local, inclua uma cópia zipada do seu registo de eventos de aplicativo do servidor do Azure AD Connect.

[Service restart]: ./media/active-directory-passwords-troubleshoot/servicerestart.png "Reinicie o serviço do Azure AD Sync"
[Support code]: ./media/active-directory-passwords-troubleshoot/supportcode.png "O código de suporte está localizado no inferior direito da janela"

## <a name="next-steps"></a>Passos Seguintes

Os seguintes artigos fornecem informações adicionais sobre a palavra-passe, repor através do Azure AD:

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
* [Tenho uma pergunta que ainda não foi abordada](active-directory-passwords-faq.md)
