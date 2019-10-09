---
title: Solução de problemas de redefinição de senha por autoatendimento-Azure Active Directory
description: Solucionando problemas de redefinição de senha de autoatendimento do Azure AD
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
ms.openlocfilehash: c4f236f2f2fdbf2736f87f754f48387f9f41850d
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/08/2019
ms.locfileid: "72024633"
---
# <a name="troubleshoot-self-service-password-reset"></a>Solucionar problemas de redefinição de senha de autoatendimento

Você está tendo um problema com a SSPR (redefinição de senha de autoatendimento) do Azure AD (Azure Active Directory)? As informações a seguir podem ajudá-lo a fazer as coisas funcionarem novamente.

## <a name="troubleshoot-self-service-password-reset-errors-that-a-user-might-see"></a>Solucionar problemas de erros de redefinição de senha de autoatendimento que um usuário pode ver

| Erro | Detalhes | Detalhes técnicos |
| --- | --- | --- |
| TenantSSPRFlagDisabled = 9 | Desculpe, você não pode redefinir sua senha neste momento porque o administrador desabilitou a redefinição de senha para sua organização. Não há nenhuma ação adicional que você possa executar para resolver essa situação. Entre em contato com seu administrador e peça para habilitar esse recurso. Para saber mais, confira [ajuda, esqueci minha senha do Azure ad](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-update-your-own-password#common-problems-and-their-solutions). | SSPR_0009: Detectamos que a redefinição de senha não foi habilitada pelo administrador. Entre em contato com seu administrador e peça para habilitar a redefinição de senha para sua organização. |
| WritebackNotEnabled = 10 |Desculpe, você não pode redefinir sua senha neste momento porque o administrador não habilitou um serviço necessário para sua organização. Não há nenhuma ação adicional que você possa executar para resolver essa situação. Entre em contato com seu administrador e peça para verificar a configuração da sua organização. Para saber mais sobre esse serviço necessário, consulte [Configurando o Write-back de senha](howto-sspr-writeback.md). | SSPR_0010: Detectamos que o Write-back de senha não foi habilitado. Entre em contato com seu administrador e peça para habilitar o Write-back de senha. |
| SsprNotEnabledInUserPolicy = 11 | Desculpe, você não pode redefinir sua senha neste momento porque o administrador não configurou a redefinição de senha para sua organização. Não há nenhuma ação adicional que você possa executar para resolver essa situação. Entre em contato com seu administrador e peça para configurar a redefinição de senha. Para saber mais sobre a configuração de redefinição de senha, consulte início do [Quick: Redefinição de senha de autoatendimento do Azure AD @ no__t-0. | SSPR_0011: Sua organização não definiu uma política de redefinição de senha. Entre em contato com seu administrador e peça para definir uma política de redefinição de senha. |
| UserNotLicensed = 12 | Desculpe, você não pode redefinir sua senha neste momento porque as licenças necessárias estão ausentes da sua organização. Não há nenhuma ação adicional que você possa executar para resolver essa situação. Entre em contato com seu administrador e peça para verificar sua atribuição de licença. Para saber mais sobre licenciamento, confira [requisitos de licenciamento para redefinição de senha de autoatendimento do Azure ad](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-licensing). | SSPR_0012: Sua organização não tem as licenças necessárias necessárias para executar a redefinição de senha. Entre em contato com seu administrador e peça para revisar as atribuições de licença. |
| UserNotMemberOfScopedAccessGroup = 13 | Desculpe, você não pode redefinir sua senha neste momento porque o administrador não configurou sua conta para usar a redefinição de senha. Não há nenhuma ação adicional que você possa executar para resolver essa situação. Entre em contato com seu administrador e peça para configurar sua conta para redefinição de senha. Para saber mais sobre a configuração de conta para redefinição de senha, consulte [distribuir redefinição de senha para usuários](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-best-practices). | SSPR_0013: Você não é um membro de um grupo habilitado para redefinição de senha. Entre em contato com seu administrador e solicite a adição ao grupo. |
| UserNotProperlyConfigured = 14 | Infelizmente, você não pode redefinir sua senha no momento porque as informações necessárias estão ausentes em sua conta. Não há nenhuma ação adicional que você possa executar para resolver essa situação. Entre em contato com o administrador e peça para redefinir sua senha para você. Depois de ter acesso à sua conta novamente, você precisa registrar as informações necessárias. Para registrar informações, siga as etapas no artigo [registrar para redefinição de senha de autoatendimento](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-reset-register) . | SSPR_0014: Informações de segurança adicionais são necessárias para redefinir sua senha. Para continuar, entre em contato com seu administrador e peça para redefinir sua senha. Depois de ter acesso à sua conta, você pode registrar informações de segurança adicionais em https://aka.ms/ssprsetup. O administrador pode adicionar informações de segurança adicionais à sua conta seguindo as etapas em [definir e ler dados de autenticação para redefinição de senha](howto-sspr-authenticationdata.md). |
| OnPremisesAdminActionRequired = 29 | Desculpe, não é possível redefinir sua senha no momento devido a um problema com a configuração de redefinição de senha da sua organização. Não há nenhuma ação adicional que você possa executar para resolver essa situação. Entre em contato com seu administrador e peça que eles investiguem. Para saber mais sobre o problema em potencial, consulte [solucionar problemas de write-back de senha](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-troubleshoot#troubleshoot-password-writeback). | SSPR_0029: Não é possível redefinir sua senha devido a um erro em sua configuração local. Entre em contato com seu administrador e peça que eles investiguem. |
| OnPremisesConnectivityError = 30 | Desculpe, não é possível redefinir sua senha no momento devido a problemas de conectividade para sua organização. Não há nenhuma ação a ser tomada no momento, mas o problema pode ser resolvido se você tentar novamente mais tarde. Se o problema persistir, entre em contato com seu administrador e peça a ele para investigar. Para saber mais sobre problemas de conectividade, consulte [solucionar problemas de conectividade de write-back de senha](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-troubleshoot#troubleshoot-password-writeback-connectivity). | SSPR_0030: Não é possível redefinir sua senha devido a uma conexão ruim com o seu ambiente local. Entre em contato com seu administrador e peça que eles investiguem.|

## <a name="troubleshoot-the-password-reset-configuration-in-the-azure-portal"></a>Solucionar problemas de configuração de redefinição de senha no portal do Azure

| Erro | Solução |
| --- | --- |
| Não vejo a seção de **redefinição de senha** no Azure AD na portal do Azure. | Isso pode acontecer se você não tiver uma licença do Azure AD atribuída ao administrador que executa a operação. <br> <br> Atribua uma licença à conta de administrador em questão. Você pode seguir as etapas no artigo [atribuir, verificar e resolver problemas com licenças](../users-groups-roles/licensing-groups-assign.md#step-1-assign-the-required-licenses) .|
| Não vejo uma opção de configuração específica. | Muitos elementos da interface do usuário ficam ocultos até que sejam necessários. Tente habilitar todas as opções que você deseja ver. |
| Não vejo a guia **integração local** . | Essa opção só ficará visível se você tiver baixado Azure AD Connect e tiver configurado o Write-back de senha. Para obter mais informações, consulte [introdução ao Azure ad Connect usando as configurações expressas](../hybrid/how-to-connect-install-express.md). |

## <a name="troubleshoot-password-reset-reporting"></a>Solucionar problemas de redefinição de senha

| Erro | Solução |
| --- | --- |
| Não vejo nenhum tipo de atividade de gerenciamento de senhas na categoria de evento de auditoria de **Gerenciamento de senhas de autoatendimento** . | Isso pode acontecer se você não tiver uma licença do Azure AD atribuída ao administrador que executa a operação. <br> <br> Você pode resolver esse problema atribuindo uma licença à conta de administrador em questão. Siga as etapas no artigo [atribuir, verificar e resolver problemas com licenças](../users-groups-roles/licensing-groups-assign.md#step-1-assign-the-required-licenses) . |
| Os registros de usuário são exibidos várias vezes. | Atualmente, quando um usuário registra, nós registramos cada dado individual que é registrado como um evento separado. <br> <br> Se você quiser agregar esses dados e ter maior flexibilidade para exibi-los, poderá baixar o relatório e abrir os dados como uma tabela dinâmica no Excel.

## <a name="troubleshoot-the-password-reset-registration-portal"></a>Solucionar problemas do portal de registro de redefinição de senha

| Erro | Solução |
| --- | --- |
| O diretório não está habilitado para redefinição de senha. **O administrador não habilitou você a usar esse recurso.** | Alterne o sinalizador **habilitado para redefinição de senha de autoatendimento** para **selecionado** ou **todos** e, em seguida, selecione **salvar**. |
| O usuário não tem uma licença do Azure AD atribuída. **O administrador não habilitou você a usar esse recurso.** | Isso pode acontecer se você não tiver uma licença do Azure AD atribuída ao administrador que executa a operação. <br> <br> Você pode resolver esse problema atribuindo uma licença à conta de administrador em questão. Siga as etapas no artigo [atribuir, verificar e resolver problemas com licenças](../users-groups-roles/licensing-groups-assign.md#step-1-assign-the-required-licenses) .|
| Erro ao processar a solicitação. | Isso pode ser causado por muitos problemas, mas geralmente esse erro é causado por uma interrupção do serviço ou um problema de configuração. Se você vir esse erro e ele afetar sua empresa, entre em contato com o suporte da Microsoft para obter assistência adicional. |

## <a name="troubleshoot-the-password-reset-portal"></a>Solucionar problemas do portal de redefinição de senha

| Erro | Solução |
| --- | --- |
| O diretório não está habilitado para redefinição de senha. | Alterne o sinalizador **habilitado para redefinição de senha de autoatendimento** para **selecionado** ou **todos** e, em seguida, selecione **salvar**. |
| O usuário não tem uma licença do Azure AD atribuída. | Isso pode acontecer se você não tiver uma licença do Azure AD atribuída ao administrador que executa a operação. <br> <br> Você pode resolver esse problema se atribuir uma licença à conta de administrador em questão. Siga as etapas no artigo [atribuir, verificar e resolver problemas com licenças](../users-groups-roles/licensing-groups-assign.md#step-1-assign-the-required-licenses) . |
| O diretório está habilitado para redefinição de senha, mas o usuário tem informações de autenticação ausentes ou malformadas. | Antes de continuar, verifique se o usuário formou corretamente os dados de contato no arquivo no diretório. Para obter mais informações, consulte [dados usados pela redefinição de senha de autoatendimento do Azure ad](howto-sspr-authenticationdata.md). |
| O diretório está habilitado para redefinição de senha, mas o usuário tem apenas uma parte dos dados de contato no arquivo quando a política é definida para exigir dois métodos de verificação. | Antes de continuar, verifique se o usuário tem pelo menos dois métodos de contato configurados corretamente. Um exemplo é ter um número de telefone celular *e* um número de telefone comercial. |
| O diretório está habilitado para redefinição de senha e o usuário está configurado corretamente, mas o usuário não pode ser contatado. | Isso pode ser o resultado de um erro de serviço temporário ou se houver dados de contato incorretos que não podem ser detectados corretamente. <br> <br> Se o usuário aguardar 10 segundos, os links "tentar novamente" e "Contate o administrador" serão exibidos. Se o usuário selecionar "tentar novamente", ele tentará novamente a chamada. Se o usuário selecionar "entre em contato com seu administrador", ele enviará um email de formulário para seus administradores solicitando que uma redefinição de senha seja executada para essa conta de usuário. |
| O usuário nunca recebe a chamada telefônica ou SMS de redefinição de senha. | Isso pode ser o resultado de um número de telefone malformado no diretório. Verifique se o número de telefone está no formato "+ CCC xxxyyyzzzzXeeee". <br> <br> A redefinição de senha não oferece suporte a extensões, mesmo se você especificar uma no diretório. As extensões são removidas antes que a chamada seja expedida. Use um número sem uma extensão ou integre a extensão ao número de telefone em sua PBX (troca de Branch privada). |
| O usuário nunca recebe o email de redefinição de senha. | A causa mais comum para esse problema é que a mensagem é rejeitada por um filtro de spam. Verifique a pasta de spam, lixo eletrônico ou itens excluídos do email. <br> <br> Verifique também se você está verificando a conta de email correta para a mensagem. |
| Defini uma política de redefinição de senha, mas quando uma conta de administrador usa a redefinição de senha, essa política não é aplicada. | A Microsoft gerencia e controla a política de redefinição de senha do administrador para garantir o nível mais alto de segurança. |
| O usuário é impedido de tentar uma redefinição de senha muitas vezes em um dia. | Implementamos um mecanismo de limitação automática para impedir que os usuários tentem redefinir suas senhas muitas vezes em um curto período de tempo. A limitação ocorre quando: <br><ul><li>O usuário tenta validar um número de telefone cinco vezes em uma hora.</li><li>O usuário tenta usar o portão de perguntas de segurança cinco vezes em uma hora.</li><li>O usuário tenta redefinir uma senha para a mesma conta de usuário cinco vezes em uma hora.</li></ul>Para corrigir esse problema, instrua o usuário a aguardar 24 horas após a última tentativa. Em seguida, o usuário pode redefinir sua senha. |
| O usuário vê um erro ao validar seu número de telefone. | Esse erro ocorre quando o número de telefone digitado não corresponde ao número de telefone no arquivo. Verifique se o usuário está inserindo o número de telefone completo, incluindo o código de área e país, ao tentar usar um método baseado em telefone para redefinição de senha. |
| Erro ao processar a solicitação. | Isso pode ser causado por muitos problemas, mas geralmente esse erro é causado por uma interrupção do serviço ou um problema de configuração. Se você vir esse erro e ele afetar sua empresa, entre em contato com o suporte da Microsoft para obter assistência adicional. |
| Violação de política local | A senha não atende à política de senha de Active Directory local. |
| A senha não é compatível com a política difusa | A senha que foi usada aparece na [lista de senhas banidas](https://docs.microsoft.com/azure/active-directory/authentication/concept-password-ban-bad#how-are-passwords-evaluated) e não pode ser usada. |

## <a name="troubleshoot-password-writeback"></a>Solucionar o Write-back de senha

| Erro | Solução |
| --- | --- |
| O serviço de redefinição de senha não é iniciado localmente. O erro 6800 aparece no log de eventos do aplicativo do Azure AD Connect Machine. <br> <br> Após a integração, a autenticação federada, de passagem ou os usuários sincronizados por hash de senha não podem redefinir suas senhas. | Quando o Write-back de senha está habilitado, o mecanismo de sincronização chama a biblioteca de write-back para executar a configuração (integração) comunicando-se com o serviço de integração de nuvem. Todos os erros encontrados durante a integração ou ao iniciar o ponto de extremidade do Windows Communication Foundation (WCF) para o Write-back de senha resultam em erros no log de eventos, na sua máquina Azure AD Connect. <br> <br> Durante a reinicialização do serviço de Azure AD Sync (ADSync), se o Write-back tiver sido configurado, o ponto de extremidade do WCF será iniciado. Mas, se a inicialização do ponto de extremidade falhar, nós registraremos o evento 6800 e permitiremos que o serviço de sincronização seja inicializado. A presença desse evento significa que o ponto de extremidade de write-back de senha não foi iniciado. Detalhes do log de eventos para este evento 6800, juntamente com as entradas do log de eventos geradas pelo componente PasswordResetService, indique por que não é possível iniciar o ponto de extremidade. Examine esses erros de log de eventos e tente reiniciar o Azure AD Connect se o Write-back de senha ainda não estiver funcionando. Se o problema persistir, tente desabilitar e reabilitar o Write-back de senha.
| Quando um usuário tenta redefinir uma senha ou desbloquear uma conta com o Write-back de senha habilitado, a operação falha. <br> <br> Além disso, você verá um evento no log de eventos Azure AD Connect que contém: "O mecanismo de sincronização retornou um erro HR = 800700CE, Message = o nome do arquivo ou a extensão é muito longa" após a operação de desbloqueio ocorrer. | Localize a conta de Active Directory para Azure AD Connect e redefina a senha para que ela contenha no máximo 256 caracteres. Em seguida, abra o **serviço de sincronização** no menu **Iniciar** . Navegue até **conectores** e localize o **conector de Active Directory**. Selecione-o e, em seguida, selecione **Propriedades**. Navegue até a página **credenciais** e insira a nova senha. Selecione **OK** para fechar a página. |
| Na última etapa do processo de instalação do Azure AD Connect, você verá um erro indicando que não foi possível configurar o Write-back de senha. <br> <br> O log de eventos do aplicativo Azure AD Connect contém o erro 32009 com o texto "erro ao obter o token de autenticação". | Esse erro ocorre nos dois casos a seguir: <br><ul><li>Você especificou uma senha incorreta para a conta de administrador global especificada no início do processo de instalação do Azure AD Connect.</li><li>Você tentou usar um usuário federado para a conta de administrador global especificada no início do processo de instalação do Azure AD Connect.</li></ul> Para corrigir esse problema, certifique-se de que você não está usando uma conta federada para o administrador global que você especificou no início do processo de instalação. Verifique também se a senha especificada está correta. |
| O log de eventos do computador Azure AD Connect contém o erro 32002 que é gerado pela execução de PasswordResetService. <br> <br> O erro lê: "Erro ao conectar ao ServiceBus. O provedor de token não pôde fornecer um token de segurança. " | Seu ambiente local não é capaz de se conectar ao ponto de extremidade do barramento de serviço do Azure na nuvem. Esse erro normalmente é causado por uma regra de firewall bloqueando uma conexão de saída para uma porta ou endereço da Web específico. Consulte [pré-requisitos de conectividade](../hybrid/how-to-connect-install-prerequisites.md) para obter mais informações. Depois de atualizar essas regras, reinicialize o Azure AD Connect computador e o Write-back de senha deve começar a funcionar novamente. |
| Depois de trabalhar por algum tempo, o federado, a autenticação de passagem ou os usuários sincronizados com hash de senha não podem redefinir suas senhas. | Em alguns casos raros, o serviço de write-back de senha pode falhar ao reiniciar quando Azure AD Connect reiniciada. Nesses casos, primeiro verifique se o Write-back de senha parece estar habilitado no local. Você pode verificar usando o assistente de Azure AD Connect ou o PowerShell (consulte a seção HowTos anteriores). Se o recurso parecer estar habilitado, tente habilitar ou desabilitar o recurso novamente por meio da interface do usuário ou do PowerShell. Se isso não funcionar, tente uma desinstalação completa e reinstale o Azure AD Connect. |
| Os usuários federados, de autenticação de passagem ou de sincronização de hash de senha que tentam redefinir suas senhas veem um erro depois de tentar enviar sua senha. O erro indica que houve um problema de serviço. <br ><br> Além desse problema, durante as operações de redefinição de senha, você pode ver um erro de que o agente de gerenciamento teve acesso negado em seus logs de eventos locais. | Se você vir esses erros no log de eventos, confirme se a conta do ADMA (agente de gerenciamento de Active Directory) que foi especificada no assistente no momento da configuração tem as permissões necessárias para o Write-back de senha. <br> <br> Depois que essa permissão é fornecida, pode levar até uma hora para que as permissões tricklem por meio da tarefa em segundo plano `sdprop` no DC (controlador de domínio). <br> <br> Para que a redefinição de senha funcione, a permissão precisa ser carimbada no descritor de segurança do objeto de usuário cuja senha está sendo redefinida. Até que essa permissão seja exibida no objeto de usuário, a redefinição de senha continuará a falhar com uma mensagem de acesso negado. |
| Usuários federados, de autenticação de passagem ou de sincronização de hash de senha que tentam redefinir suas senhas, consulte um erro depois de enviarem sua senha. O erro indica que houve um problema de serviço. <br> <br> Além desse problema, durante as operações de redefinição de senha, você poderá ver um erro nos logs de eventos do serviço de Azure AD Connect indicando um erro "não foi possível encontrar o objeto". | Esse erro geralmente indica que o mecanismo de sincronização não consegue localizar o objeto de usuário no espaço do conector do Azure AD ou o objeto de espaço do conector do Azure ad ou o metaverso (MV) vinculado. <br> <br> Para solucionar esse problema, verifique se o usuário realmente está sincronizado do local para o Azure AD por meio da instância atual do Azure AD Connect e inspecione o estado dos objetos nos espaços do conector e no MV. Confirme se o objeto de serviços de certificados Active Directory (AD CS) está conectado ao objeto MV por meio da regra "Microsoft.InfromADUserAccountEnabled.xxx".|
| Os usuários federados, de autenticação de passagem ou de sincronização de hash de senha que tentam redefinir suas senhas veem um erro depois de enviarem sua senha. O erro indica que houve um problema de serviço. <br> <br> Além desse problema, durante as operações de redefinição de senha, você poderá ver um erro nos logs de eventos do serviço Azure AD Connect que indica que há um erro "várias correspondências encontradas". | Isso indica que o mecanismo de sincronização detectou que o objeto MV está conectado a mais de um objeto AD CS por meio de "Microsoft.InfromADUserAccountEnabled.xxx". Isso significa que o usuário tem uma conta habilitada em mais de uma floresta. Esse cenário não tem suporte para Write-back de senha. |
| As operações de senha falham com um erro de configuração. O log de eventos do aplicativo contém Azure AD Connect erro 6329 com o texto "0x8023061f (a operação falhou porque a sincronização de senha não está habilitada neste agente de gerenciamento)". | Esse erro ocorrerá se a configuração de Azure AD Connect for alterada para adicionar uma nova floresta de Active Directory (ou para remover e ler uma floresta existente) depois que o recurso de write-back de senha já tiver sido habilitado. As operações de senha para usuários nessas florestas recentemente adicionadas falham. Para corrigir o problema, desabilite e habilite novamente o recurso de write-back de senha após a conclusão das alterações de configuração da floresta. |

## <a name="password-writeback-event-log-error-codes"></a>Códigos de erro de log de eventos de write-back de senha

Uma prática recomendada ao solucionar problemas com o Write-back de senha é inspecionar o log de eventos do aplicativo em seu computador Azure AD Connect. Esse log de eventos contém eventos de duas fontes de interesse para Write-back de senha. A fonte PasswordResetService descreve operações e problemas relacionados à operação de write-back de senha. A fonte ADSync descreve operações e problemas relacionados à definição de senhas em seu ambiente de Active Directory.

### <a name="if-the-source-of-the-event-is-adsync"></a>Se a origem do evento for ADSync

| Código | Nome ou mensagem | Descrição |
| --- | --- | --- |
| 6329 | PRESILHA MMS(4924) 0x80230619: "Uma restrição impede que a senha seja alterada para a atual especificada." | Esse evento ocorre quando o serviço de write-back de senha tenta definir uma senha em seu diretório local que não atende à duração da senha, ao histórico, à complexidade ou aos requisitos de filtragem do domínio. <br> <br> Se você tiver uma duração mínima da senha e tiver alterado recentemente a senha dentro dessa janela de tempo, não será possível alterar a senha novamente até atingir a idade especificada em seu domínio. Para fins de teste, a idade mínima deve ser definida como 0. <br> <br> Se você tiver requisitos de histórico de senha habilitados, deverá selecionar uma senha que não tenha sido usada nas últimas *n* vezes, em que *n* é a configuração de histórico de senha. Se você selecionar uma senha que foi usada nas últimas *N* vezes, você verá uma falha nesse caso. Para fins de teste, o histórico de senha deve ser definido como 0. <br> <br> Se você tiver requisitos de complexidade de senha, todos eles serão impostos quando o usuário tentar alterar ou redefinir uma senha. <br> <br> Se você tiver filtros de senha habilitados e um usuário selecionar uma senha que não atende aos critérios de filtragem, a operação de redefinição ou alteração falhará. |
| 6329 | MMS(3040): admaexport.cpp(2837): O servidor não contém o controle de política de senha LDAP. | Esse problema ocorre se o controle LDAP_SERVER_POLICY_HINTS_OID (1.2.840.113556.1.4.2066) não estiver habilitado nos DCs. Para usar o recurso de write-back de senha, você deve habilitar o controle. Para fazer isso, os DCs devem estar no Windows Server 2008R2 ou posterior. |
| HR 8023042 | O mecanismo de sincronização retornou um erro HR = 80230402, Message = uma tentativa de obter um objeto falhou porque há entradas duplicadas com a mesma âncora. | Esse erro ocorre quando a mesma ID de usuário está habilitada em vários domínios. Um exemplo é se você estiver sincronizando florestas de conta e de recursos e tiver a mesma ID de usuário presente e habilitada em cada floresta. <br> <br> Esse erro também pode ocorrer se você usar um atributo de âncora não exclusivo, como um alias ou UPN, e dois usuários compartilharem o mesmo atributo de âncora. <br> <br> Para resolver esse problema, verifique se você não tem usuários duplicados em seus domínios e se você usa um atributo de âncora exclusivo para cada usuário. |

### <a name="if-the-source-of-the-event-is-passwordresetservice"></a>Se a origem do evento for PasswordResetService

| Código | Nome ou mensagem | Descrição |
| --- | --- | --- |
| 31001 | PasswordResetStart | Esse evento indica que o serviço local detectou uma solicitação de redefinição de senha para um usuário federado, de autenticação de passagem ou de sincronização de hash de senha que se origina da nuvem. Esse evento é o primeiro evento em cada operação de write-back de redefinição de senha. |
| 31002 | PasswordResetSuccess | Esse evento indica que um usuário selecionou uma nova senha durante uma operação de redefinição de senha. Determinamos que essa senha atende aos requisitos de senha corporativa. A senha foi gravada com êxito no ambiente de Active Directory local. |
| 31003 | PasswordResetFail | Esse evento indica que um usuário selecionou uma senha e a senha chegou com êxito ao ambiente local. Mas quando tentamos definir a senha no ambiente de Active Directory local, ocorreu uma falha. Essa falha pode ocorrer por vários motivos: <br><ul><li>A senha do usuário não atende aos requisitos de idade, histórico, complexidade ou filtro para o domínio. Para resolver esse problema, crie uma nova senha.</li><li>A conta de serviço ADMA não tem as permissões apropriadas para definir a nova senha na conta de usuário em questão.</li><li>A conta do usuário está em um grupo protegido, como domínio ou grupo de administradores corporativos, que não permite operações de definição de senha.</li></ul>|
| 31004 | OnboardingEventStart | Esse evento ocorrerá se você habilitar o Write-back de senha com Azure AD Connect e começarmos a integrar sua organização ao serviço Web de write-back de senha. |
| 31005 | OnboardingEventSuccess | Esse evento indica que o processo de integração foi bem-sucedido e que o recurso de write-back de senha está pronto para uso. |
| 31006 | ChangePasswordStart | Esse evento indica que o serviço local detectou uma solicitação de alteração de senha para um usuário federado, de autenticação de passagem ou de sincronização de hash de senha que se origina da nuvem. Esse evento é o primeiro evento em cada operação de write-back de alteração de senha. |
| 31007 | ChangePasswordSuccess | Esse evento indica que um usuário selecionou uma nova senha durante uma operação de alteração de senha, determinamos que a senha atende aos requisitos de senha corporativa e que a senha foi gravada com êxito no ambiente de Active Directory local. |
| 31008 | ChangePasswordFail | Esse evento indica que um usuário selecionou uma senha e que a senha chegou com êxito ao ambiente local, mas quando tentamos definir a senha no ambiente de Active Directory local, ocorreu uma falha. Essa falha pode ocorrer por vários motivos: <br><ul><li>A senha do usuário não atende aos requisitos de idade, histórico, complexidade ou filtro para o domínio. Para resolver esse problema, crie uma nova senha.</li><li>A conta de serviço ADMA não tem as permissões apropriadas para definir a nova senha na conta de usuário em questão.</li><li>A conta do usuário está em um grupo protegido, como administradores corporativos ou de domínio, que não permite operações de definição de senha.</li></ul> |
| 31009 | ResetUserPasswordByAdminStart | O serviço local detectou uma solicitação de redefinição de senha para um usuário federado, de autenticação de passagem ou de sincronização de hash de senha proveniente do administrador em nome de um usuário. Esse evento é o primeiro evento em todas as operações de write-back de redefinição de senha que são iniciadas por um administrador. |
| 31010 | ResetUserPasswordByAdminSuccess | O administrador selecionou uma nova senha durante uma operação de redefinição de senha iniciada pelo administrador. Determinamos que essa senha atende aos requisitos de senha corporativa. A senha foi gravada com êxito no ambiente de Active Directory local. |
| 31011 | ResetUserPasswordByAdminFail | O administrador selecionou uma senha em nome de um usuário. A senha chegou com êxito ao ambiente local. Mas quando tentamos definir a senha no ambiente de Active Directory local, ocorreu uma falha. Essa falha pode ocorrer por vários motivos: <br><ul><li>A senha do usuário não atende aos requisitos de idade, histórico, complexidade ou filtro para o domínio. Tente uma nova senha para resolver esse problema.</li><li>A conta de serviço ADMA não tem as permissões apropriadas para definir a nova senha na conta de usuário em questão.</li><li>A conta do usuário está em um grupo protegido, como administradores corporativos ou de domínio, que não permite operações de definição de senha.</li></ul>  |
| 31012 | OffboardingEventStart | Esse evento ocorrerá se você desabilitar o Write-back de senha com Azure AD Connect e indicar que iniciamos a remoção da sua organização para o serviço Web de write-back de senha. |
| 31013| OffboardingEventSuccess| Esse evento indica que o processo de remoção foi bem-sucedido e que a funcionalidade de write-back de senha foi desabilitada com êxito. |
| 31014| OffboardingEventFail| Esse evento indica que o processo de remoção não foi bem-sucedido. Isso pode ser devido a um erro de permissões na conta de administrador local ou na nuvem especificada durante a configuração. O erro também pode ocorrer se você estiver tentando usar um administrador global de nuvem federada ao desabilitar o Write-back de senha. Para corrigir esse problema, verifique suas permissões administrativas e certifique-se de que você não está usando uma conta federada ao configurar o recurso de write-back de senha.|
| 31015| WriteBackServiceStarted| Esse evento indica que o serviço de write-back de senha foi iniciado com êxito. Ele está pronto para aceitar solicitações de gerenciamento de senha da nuvem.|
| 31016| WriteBackServiceStopped| Esse evento indica que o serviço de write-back de senha foi interrompido. As solicitações de gerenciamento de senha da nuvem não serão bem-sucedidas.|
| 31017| AuthTokenSuccess| Esse evento indica que recuperamos com êxito um token de autorização para o administrador global especificado durante a instalação do Azure AD Connect para iniciar o processo de remoção ou integração.|
| 31018| KeyPairCreationSuccess| Esse evento indica que criamos com êxito a chave de criptografia de senha. Essa chave é usada para criptografar senhas da nuvem para serem enviadas ao seu ambiente local.|
| 32000| UnknownError| Esse evento indica que ocorreu um erro desconhecido durante uma operação de gerenciamento de senha. Examine o texto da exceção no evento para obter mais detalhes. Se você estiver tendo problemas, tente desabilitar e reabilitar o Write-back de senha. Se isso não ajudar, inclua uma cópia do seu log de eventos junto com a ID de acompanhamento especificada Insider ao seu engenheiro de suporte.|
| 32001| ServiceError| Esse evento indica que houve um erro ao conectar-se ao serviço de redefinição de senha na nuvem. Esse erro geralmente ocorre quando o serviço local não pôde se conectar ao serviço Web de redefinição de senha.|
| 32002| ServiceBusError| Esse evento indica que houve um erro ao conectar-se à instância do barramento de serviço do locatário. Isso pode acontecer se você estiver bloqueando as conexões de saída no seu ambiente local. Verifique o firewall para garantir que você permita conexões sobre TCP 443 e para https://ssprsbprodncu-sb.accesscontrol.windows.net/ e tente novamente. Se você ainda tiver problemas, tente desabilitar e reabilitar o Write-back de senha.|
| 32003| InPutValidationError| Esse evento indica que a entrada passada para a API do serviço Web era inválida. Tente a operação novamente.|
| 32004| DecryptionError| Esse evento indica que houve um erro ao descriptografar a senha que chegou da nuvem. Isso pode ser devido a uma incompatibilidade de chave de descriptografia entre o serviço de nuvem e seu ambiente local. Para resolver esse problema, desabilite e, em seguida, habilite novamente o Write-back de senha no seu ambiente local.|
| 32005| ConfigurationError| Durante a integração, salvamos informações específicas de locatário em um arquivo de configuração em seu ambiente local. Esse evento indica que houve um erro ao salvar esse arquivo ou que, quando o serviço foi iniciado, houve um erro ao ler o arquivo. Para corrigir esse problema, tente desabilitar e reabilitar o Write-back de senha para forçar uma regravação do arquivo de configuração.|
| 32007| OnBoardingConfigUpdateError| Durante a integração, enviamos dados da nuvem para o serviço de redefinição de senha local. Esses dados são gravados em um arquivo na memória antes de serem enviados para o serviço de sincronização para serem armazenados com segurança no disco. Esse evento indica que há um problema com a gravação ou atualização desses dados na memória. Para corrigir esse problema, tente desabilitar e reabilitar o Write-back de senha para forçar uma regravação desse arquivo de configuração.|
| 32008| ValidationError| Esse evento indica que recebemos uma resposta inválida do serviço Web de redefinição de senha. Para corrigir esse problema, tente desabilitar e reabilitar o Write-back de senha.|
| 32009| AuthTokenError| Esse evento indica que não foi possível obter um token de autorização para a conta de administrador global especificada durante a instalação do Azure AD Connect. Esse erro pode ser causado por um nome de usuário ou senha inválidos especificados para a conta de administrador global. Esse erro também pode ocorrer se a conta de administrador global especificada for federada. Para corrigir esse problema, execute novamente a configuração com o nome de usuário e a senha corretos e verifique se o administrador é uma conta gerenciada (somente em nuvem ou sincronizada por senha).|
| 32010| CryptoError| Esse evento indica que houve um erro ao gerar a chave de criptografia de senha ou descriptografar uma senha que chega do serviço de nuvem. Esse erro provavelmente indica um problema com o seu ambiente. Examine os detalhes do seu log de eventos para saber mais sobre como resolver esse problema. Você também pode tentar desabilitar e habilitar novamente o serviço de write-back de senha.|
| 32011| OnBoardingServiceError| Esse evento indica que o serviço local não pôde se comunicar corretamente com o serviço Web de redefinição de senha para iniciar o processo de integração. Isso pode ocorrer como resultado de uma regra de firewall ou se houver um problema ao obter um token de autenticação para seu locatário. Para corrigir esse problema, verifique se você não está bloqueando as conexões de saída sobre TCP 443 e TCP 9350-9354 ou para https://ssprsbprodncu-sb.accesscontrol.windows.net/. Além disso, certifique-se de que a conta de administrador do Azure AD que você está usando para a integração não é federada.|
| 32013| OffBoardingError| Esse evento indica que o serviço local não pôde se comunicar corretamente com o serviço Web de redefinição de senha para iniciar o processo de remoção. Isso pode acontecer como resultado de uma regra de firewall ou se houver um problema ao obter um token de autorização para seu locatário. Para corrigir esse problema, verifique se você não está bloqueando as conexões de saída em 443 ou https://ssprsbprodncu-sb.accesscontrol.windows.net/, e se a conta de administrador do Azure Active Directory que você está usando para transferir não é federada.|
| 32014| ServiceBusWarning| Esse evento indica que tivemos que tentar novamente se conectar à instância do barramento de serviço de seu locatário. Em condições normais, isso não deve ser uma preocupação, mas se você vir esse evento muitas vezes, considere verificar sua conexão de rede para o barramento de serviço, especialmente se for uma conexão de alta latência ou baixa largura de banda.|
| 32015| ReportServiceHealthError| Para monitorar a integridade do seu serviço de write-back de senha, enviamos dados de pulsação para nosso serviço Web de redefinição de senha a cada cinco minutos. Esse evento indica que houve um erro ao enviar essas informações de integridade de volta para o serviço Web na nuvem. Essas informações de integridade não incluem informações de identificação de objeto (OII) ou dados de informações de identificação pessoal (PII) e são puramente uma pulsação e estatísticas básicas de serviço para que possamos fornecer informações de status de serviço na nuvem.|
| 33001| ADUnKnownError| Esse evento indica que houve um erro desconhecido retornado por Active Directory. Verifique o log de eventos do Azure AD Connect Server em busca de eventos da origem do ADSync para obter mais informações.|
| 33002| ADUserNotFoundError| Esse evento indica que o usuário que está tentando redefinir ou alterar uma senha não foi encontrado no diretório local. Esse erro pode ocorrer quando o usuário tiver sido excluído no local, mas não na nuvem. Esse erro também pode ocorrer se houver um problema com a sincronização. Verifique os logs de sincronização e os últimos detalhes da execução de sincronização para obter mais informações.|
| 33003| ADMutliMatchError| Quando uma solicitação de redefinição ou de alteração de senha é originada na nuvem, usamos a âncora de nuvem especificada durante o processo de instalação de Azure AD Connect para determinar como vincular essa solicitação de volta a um usuário em seu ambiente local. Esse evento indica que encontramos dois usuários em seu diretório local com o mesmo atributo de âncora de nuvem. Verifique os logs de sincronização e os últimos detalhes da execução de sincronização para obter mais informações.|
| 33004| ADPermissionsError| Esse evento indica que a conta de serviço ADMA (agente de gerenciamento de Active Directory) não tem as permissões apropriadas na conta em questão para definir uma nova senha. Verifique se a conta ADMA na floresta do usuário tem permissões de redefinição e alteração de senha em todos os objetos na floresta. Para obter mais informações sobre como definir as permissões, consulte Etapa 4: Configure as permissões de Active Directory apropriadas.|
| 33005| ADUserAccountDisabled| Esse evento indica que tentamos redefinir ou alterar uma senha para uma conta que foi desabilitada localmente. Habilite a conta e repita a operação.|
| 33006| ADUserAccountLockedOut| Esse evento indica que tentamos redefinir ou alterar uma senha para uma conta que foi bloqueada localmente. Os bloqueios podem ocorrer quando um usuário tenta uma operação de alteração ou redefinição de senha muitas vezes em um curto período de tempo. Desbloqueie a conta e tente a operação novamente.|
| 33007| ADUserIncorrectPassword| Esse evento indica que o usuário especificou uma senha atual incorreta ao executar uma operação de alteração de senha. Especifique a senha atual correta e tente novamente.|
| 33008| ADPasswordPolicyError| Esse evento ocorre quando o serviço de write-back de senha tenta definir uma senha em seu diretório local que não atende à duração da senha, ao histórico, à complexidade ou aos requisitos de filtragem do domínio. <br> <br> Se você tiver uma duração mínima da senha e tiver alterado recentemente a senha dentro dessa janela de tempo, não será possível alterar a senha novamente até atingir a idade especificada em seu domínio. Para fins de teste, a idade mínima deve ser definida como 0. <br> <br> Se você tiver requisitos de histórico de senha habilitados, deverá selecionar uma senha que não tenha sido usada nas últimas *n* vezes, em que *n* é a configuração de histórico de senha. Se você selecionar uma senha que foi usada nas últimas *N* vezes, você verá uma falha nesse caso. Para fins de teste, o histórico de senha deve ser definido como 0. <br> <br> Se você tiver requisitos de complexidade de senha, todos eles serão impostos quando o usuário tentar alterar ou redefinir uma senha. <br> <br> Se você tiver filtros de senha habilitados e um usuário selecionar uma senha que não atende aos critérios de filtragem, a operação de redefinição ou alteração falhará.|
| 33009| ADConfigurationError| Esse evento indica que houve um problema ao gravar uma senha de volta em seu diretório local devido a um problema de configuração com o Active Directory. Verifique o log de eventos do aplicativo da máquina Azure AD Connect em busca de mensagens do serviço ADSync para obter mais informações sobre qual erro ocorreu.|

## <a name="troubleshoot-password-writeback-connectivity"></a>Solucionar problemas de conectividade de write-back

Se você estiver enfrentando interrupções de serviço com o componente de write-back de senha do Azure AD Connect, aqui estão algumas etapas rápidas que você pode executar para resolver esse problema:

* [Confirmar conectividade de rede](#confirm-network-connectivity)
* [Reiniciar o serviço de sincronização de Azure AD Connect](#restart-the-azure-ad-connect-sync-service)
* [Desabilitar e reabilitar o recurso de write-back de senha](#disable-and-re-enable-the-password-writeback-feature)
* [Instalar a versão mais recente do Azure AD Connect](#install-the-latest-azure-ad-connect-release)
* [Solucionar o Write-back de senha](#troubleshoot-password-writeback)

Em geral, para recuperar o serviço da maneira mais rápida, recomendamos que você execute essas etapas na ordem discutida anteriormente.

### <a name="confirm-network-connectivity"></a>Confirmar conectividade de rede

O ponto mais comum de falha é que as portas de firewall e ou proxy e os tempos limite de ociosidade estão configurados incorretamente. 

Para Azure AD Connect versão 1.1.443.0 e posterior, você precisa de acesso HTTPS de saída para o seguinte:

* \*.passwordreset.microsoftonline.com
* \*.servicebus.windows.net

Para obter mais granularidade, faça referência à lista atualizada de [Microsoft Azure intervalos de IP do datacenter](https://www.microsoft.com/download/details.aspx?id=41653) atualizados todas as quartas-feiras e coloque em vigor na próxima segunda-feira.

Para obter mais informações, examine os pré-requisitos de conectividade no artigo [pré-requisitos para Azure ad Connect](../hybrid/how-to-connect-install-prerequisites.md) .

> [!NOTE]
> O SSPR também poderá falhar se a conta se as configurações "a senha nunca expirar" ou "o usuário não pode alterar a senha" estiverem configuradas na conta no AD DS local. 

### <a name="restart-the-azure-ad-connect-sync-service"></a>Reiniciar o serviço de sincronização de Azure AD Connect

Para resolver problemas de conectividade ou outros problemas transitórios com o serviço, reinicie o serviço de sincronização Azure AD Connect:

1. Como administrador, selecione **Iniciar** no servidor que executa o Azure ad Connect.
1. Insira **Services. msc** no campo de pesquisa e selecione **Enter**.
1. Procure a entrada de **sincronização Microsoft Azure ad** .
1. Clique com o botão direito do mouse na entrada do serviço, selecione **reiniciar**e aguarde a conclusão da operação.

   ![Reiniciar o serviço de Azure AD Sync usando a GUI][Service restart]

Essas etapas restabelecem a conexão com o serviço de nuvem e resolvem quaisquer interrupções que você esteja enfrentando. Se a reinicialização do serviço ADSync não resolver o problema, recomendamos que você tente desabilitar e reabilitar o recurso de write-back de senha.

### <a name="disable-and-re-enable-the-password-writeback-feature"></a>Desabilitar e reabilitar o recurso de write-back de senha

Para resolver problemas de conectividade, desabilite e habilite novamente o recurso de write-back de senha:

   1. Como administrador, abra o assistente de configuração do Azure AD Connect.
   1. Em **conectar ao Azure ad**, insira suas credenciais de administrador global do Azure AD.
   1. Em **conectar a AD DS**, insira suas credenciais de administrador dos serviços de domínio do AD.
   1. Em **identificação exclusiva de seus usuários**, selecione o botão **Avançar** .
   1. Em **recursos opcionais**, desmarque a caixa de seleção **write-back de senha** .
   1. Selecione **Avançar** nas páginas de diálogo restantes sem alterar nada até chegar à página **pronto para configurar** .
   1. Verifique se a **página pronto para configurar** mostra a opção **write-back de senha** como **desabilitada** e, em seguida, selecione o botão verde **Configurar** para confirmar suas alterações.
   1. Em **concluído**, desmarque a opção **sincronizar agora** e, em seguida, selecione **concluir** para fechar o assistente.
   1. Reabra o assistente de configuração do Azure AD Connect.
   1. Repita as etapas 2-8, exceto se você selecionar a opção **write-back de senha** na página **recursos opcionais** para reabilitar o serviço.

Essas etapas restabelecem a conexão com nosso serviço de nuvem e resolvem quaisquer interrupções que você esteja enfrentando.

Se desabilitar e reabilitar o recurso de write-back de senha não resolver o problema, recomendamos que você reinstale Azure AD Connect.

### <a name="install-the-latest-azure-ad-connect-release"></a>Instalar a versão mais recente do Azure AD Connect

A reinstalação de Azure AD Connect pode resolver problemas de configuração e conectividade entre nossos serviços de nuvem e seu ambiente de Active Directory local.

É recomendável que você execute essa etapa somente depois de tentar as duas primeiras etapas descritas anteriormente.

> [!WARNING]
> Se você tiver personalizado as regras de sincronização prontas para uso, *faça backup deles antes de prosseguir com a atualização e reimplante-os manualmente depois de concluir.*

1. Baixe a versão mais recente do Azure AD Connect no [centro de download da Microsoft](https://go.microsoft.com/fwlink/?LinkId=615771).
1. Como você já instalou o Azure AD Connect, você precisa executar uma atualização in-loco para atualizar sua instalação do Azure AD Connect para a versão mais recente.
1. Execute o pacote baixado e siga as instruções na tela para atualizar sua máquina Azure AD Connect.

As etapas anteriores devem restabelecer a conexão com nosso serviço de nuvem e resolver quaisquer interrupções que você esteja enfrentando.

Se a instalação da versão mais recente do Azure AD Connect Server não resolver o problema, recomendamos que você tente desabilitar e reabilitar o Write-back de senha como uma etapa final depois de instalar a versão mais recente.

## <a name="verify-that-azure-ad-connect-has-the-required-permission-for-password-writeback"></a>Verifique se Azure AD Connect tem a permissão necessária para Write-back de senha

Azure AD Connect requer Active Directory permissão de **redefinição de senha** para executar o Write-back de senha. Para descobrir se Azure AD Connect tem a permissão necessária para uma determinada conta de usuário local Active Directory, você pode usar o recurso de permissão efetiva do Windows:

1. Entre no servidor de Azure AD Connect e inicie o **Synchronization Service Manager** selecionando **Iniciar** **serviço de sincronização** > .
1. Na guia **conectores** , selecione o conector de **Active Directory Domain Services** local e, em seguida, selecione **Propriedades**.  
   ![Synchronization Service Manager mostrando como editar propriedades @ no__t-1  
  
1. Na janela pop-up, selecione **conectar-se a Active Directory floresta** e anote a propriedade **nome de usuário** . Essa propriedade é a conta de AD DS usada pelo Azure AD Connect para executar a sincronização de diretório. Para Azure AD Connect executar o Write-back de senha, a conta de AD DS deve ter a permissão Redefinir senha.  

   ![Localizando o serviço de sincronização Active Directory conta de usuário](./media/active-directory-passwords-troubleshoot/checkpermission02.png) 
  
1. Entre em um controlador de domínio local e inicie o aplicativo **Active Directory usuários e computadores** .
1. Selecione **Exibir** e verifique se a opção **recursos avançados** está habilitada.  

   ![Active Directory usuários e computadores mostram recursos avançados](./media/active-directory-passwords-troubleshoot/checkpermission03.png) 
  
1. Procure a conta de usuário Active Directory que você deseja verificar. Clique com o botão direito do mouse no nome da conta e selecione **Propriedades**.  
1. Na janela pop-up, vá para a guia **segurança** e selecione **avançado**.  
1. Na janela **configurações de segurança avançadas para o administrador** , vá para a guia **acesso efetivo** .
1. Selecione **selecionar um usuário**, selecione a conta de AD DS usada pelo Azure ad Connect (consulte a etapa 3) e, em seguida, selecione **exibir acesso efetivo**.

   ![Guia acesso efetivo mostrando a conta de sincronização](./media/active-directory-passwords-troubleshoot/checkpermission06.png) 
  
1. Role para baixo e procure **Redefinir senha**. Se a entrada tiver uma marca de seleção, a conta AD DS terá permissão para redefinir a senha da conta de usuário do Active Directory selecionada.  

   ![Validando que a conta de sincronização tem a permissão Redefinir senha](./media/active-directory-passwords-troubleshoot/checkpermission07.png)  

## <a name="azure-ad-forums"></a>Fóruns do Azure AD

Se você tiver uma pergunta geral sobre o Azure AD e a redefinição de senha de autoatendimento, poderá pedir à Comunidade para obter assistência nos [fóruns do Azure ad](https://social.msdn.microsoft.com/Forums/en-US/home?forum=WindowsAzureAD). Os membros da comunidade incluem engenheiros, gerentes de produto, MVPs e colegas profissionais de ti.

## <a name="contact-microsoft-support"></a>Contate o suporte da Microsoft

Se você não conseguir encontrar a resposta a um problema, nossas equipes de suporte estarão sempre disponíveis para ajudá-lo.

Para ajudá-lo adequadamente, pedimos que você forneça o máximo de detalhes possível ao abrir um caso. Esses detalhes incluem:

* **Descrição geral do erro**: Qual é o erro? Qual foi o comportamento percebido? Como podemos reproduzir o erro? Forneça o máximo de detalhes possível.
* **Página**: Em que página você estava quando observou o erro? Inclua a URL se você for capaz e uma captura de tela da página.
* **Código de suporte**: Qual foi o código de suporte que foi gerado quando o usuário viu o erro?
   * Para localizar esse código, reproduza o erro e, em seguida, selecione o link do **código de suporte** na parte inferior da tela e envie ao engenheiro de suporte o GUID que resulta.

   ![Localizar o código de suporte na parte inferior da tela][Support code]

  * Se você estiver em uma página sem um código de suporte na parte inferior, selecione F12 e procure o SID e a CID e envie esses dois resultados para o engenheiro de suporte.
* **Data, hora e fuso horário**: Inclua a data e a hora exatas *com o fuso horário em* que o erro ocorreu.
* **ID de usuário**: Quem era o usuário que viu o erro? Um exemplo é *User\@contoso.com*.
   * Este é um usuário federado?
   * Este é um usuário de autenticação de passagem?
   * Este é um usuário sincronizado com hash de senha?
   * Este é um usuário somente de nuvem?
* **Licenciamento**: O usuário tem uma licença do Azure AD atribuída?
* **Log de eventos do aplicativo**: Se você estiver usando o Write-back de senha e o erro estiver em sua infraestrutura local, inclua uma cópia compactada do log de eventos do aplicativo do servidor de Azure AD Connect.

[Service restart]: ./media/active-directory-passwords-troubleshoot/servicerestart.png "Reiniciar o serviço de Azure AD Sync"
[Support code]: ./media/active-directory-passwords-troubleshoot/supportcode.png "O código de suporte está localizado no canto inferior direito da janela"

## <a name="next-steps"></a>Passos seguintes

Os artigos a seguir fornecem informações adicionais sobre a redefinição de senha por meio do Azure AD:

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
