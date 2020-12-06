---
title: Resolução de problemas de autosserviço redefiniu writeback - Azure Ative Directory
description: Saiba como resolver problemas comuns e etapas de resolução para a gravação de autosserviço de redefinição de password no Azure Ative Directory
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: troubleshooting
ms.date: 08/26/2020
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6a3044127aacb5910a270d40d94d3255031a71a2
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/06/2020
ms.locfileid: "96741308"
---
# <a name="troubleshoot-self-service-password-reset-writeback-in-azure-active-directory"></a>Resolução de problemas de autosserviço redefiniu a gravação no Azure Ative Directory

O Azure Ative Directory (Azure AD) redefiniu a palavra-passe de autosserviço (SSPR) permite que os utilizadores republem as suas palavras-passe na nuvem. O writeback de palavra-passe é uma funcionalidade ativada com [o Azure AD Connect](../hybrid/whatis-hybrid-identity.md) que permite que as alterações de palavras-passe na nuvem sejam escritas de volta para um diretório existente no local em tempo real.

Se tiver problemas com a gravação da SSPR, as seguintes etapas de resolução de problemas e erros comuns podem ajudar. Se não conseguir encontrar a resposta para o seu problema, [as nossas equipas de apoio estão sempre disponíveis](#contact-microsoft-support) para o ajudar mais.

## <a name="troubleshoot-connectivity"></a>Resolver problemas de conectividade

Se tiver problemas com a gravação de password para Azure AD Connect, reveja os seguintes passos que possam ajudar a resolver o problema. Para recuperar o seu serviço, recomendamos que siga estes passos em ordem:

* [Confirmar conectividade de rede](#confirm-network-connectivity)
* [Reinicie o serviço Azure AD Connect Sync](#restart-the-azure-ad-connect-sync-service)
* [Desativar e reativar a funcionalidade de writeback de palavra-passe](#disable-and-re-enable-the-password-writeback-feature)
* [Instale o mais recente lançamento do Azure AD Connect](#install-the-latest-azure-ad-connect-release)
* [Writeback de palavra-passe de resolução de problemas](#common-password-writeback-errors)

### <a name="confirm-network-connectivity"></a>Confirmar conectividade de rede

O ponto mais comum de falha é que as portas de firewall ou proxy, ou os intervalos de tempo inativos estão configurados incorretamente.

Para a versão *1.1.443.0* ou superior, é necessário acesso *HTTPS de saída* aos seguintes endereços:

* *\*.passwordreset.microsoftonline.com*
* *\*.servicebus.windows.net*

Se precisar de mais granularidade, consulte a [lista de gamas IP do Microsoft Azure Datacenter](https://www.microsoft.com/download/details.aspx?id=41653). Esta lista é atualizada todas as quartas-feiras e entra em vigor na próxima segunda-feira.

Para obter mais informações, consulte os [pré-requisitos de conectividade para Azure AD Connect](../hybrid/how-to-connect-install-prerequisites.md).

### <a name="restart-the-azure-ad-connect-sync-service"></a>Reinicie o serviço Azure AD Connect Sync

Para resolver problemas de conectividade ou outros problemas transitórios com o serviço, complete os seguintes passos para reiniciar o serviço Azure AD Connect Sync:

1. Como administrador no servidor que executa O Azure AD Connect, selecione **Start**.
1. Introduza *os serviços.msc* no campo de pesquisa e selecione **Enter**.
1. Procure a entrada microsoft *Azure AD Sync.*
1. Clique com o botão direito na entrada de serviço, **selecione Restart**, e aguarde que a operação termine.

    :::image type="content" source="./media/troubleshoot-sspr-writeback/service-restart.png" alt-text="Reinicie o serviço Azure AD Sync utilizando o GUI" border="false":::

Estes passos restabelecerão a sua ligação com a Azure AD e devem resolver os seus problemas de conectividade.

Se reiniciar o serviço Azure AD Connect Sync não resolver o seu problema, tente desativar e, em seguida, reativar a função de desativação da palavra-passe na secção seguinte.

### <a name="disable-and-re-enable-the-password-writeback-feature"></a>Desativar e reativar a funcionalidade de writeback de palavra-passe

Para continuar a resolver problemas, complete os seguintes passos para desativar e, em seguida, reativar a função de desativação da palavra-passe:

1. Como administrador no servidor que executa o Azure AD Connect, abra o **assistente de configuração AD AD Ad**.
1. Em **Connect to Azure AD,** insira as suas credenciais de administração global Azure AD.
1. Em **Connect to AD DS,** insira as suas credenciais de administração de Serviços de Domínio de Diretório Ativo no local.
1. Na **identificação única dos seus utilizadores,** selecione o botão **Seguinte.**
1. Nas **funcionalidades opcionais,** limpe a caixa **de verificação de writeback password.**
1. Selecione **Seguinte** através das páginas de diálogo restantes sem alterar nada até chegar à página **Pronto para configurar.**
1. Verifique se a **página "Pronto para configurar"** mostra a opção *de writeback password* como *desativada*. Selecione o **botão configurar** verde para cometer as suas alterações.
1. Em **Terminado,** limpe a opção **Synchronize now** e, em seguida, selecione **Finish** para fechar o assistente.
1. Reabra o **assistente de configuração AD AD Ad.**
1. Repita os passos 2-8, desta vez selecionando a opção *de writeback password* na página **de funcionalidades opcionais** para voltar a ativar o serviço.

Estes passos restabelecerão a sua ligação com a Azure AD e devem resolver os seus problemas de conectividade.

Se desativar e, em seguida, voltar a ativar a função de desativação da palavra-passe não resolver o seu problema, reinstale o Azure AD Connect na secção seguinte.

### <a name="install-the-latest-azure-ad-connect-release"></a>Instale o mais recente lançamento do Azure AD Connect

Reinstalar o Azure AD Connect pode resolver problemas de configuração e conectividade entre o Azure AD e o ambiente local de Serviços de Domínio do Diretório Ativo. Recomendamos que execute este passo apenas depois de tentar os passos anteriores para verificar e resolver a conectividade.

> [!WARNING]
> Se tiver personalizado as regras de sincronização fora da caixa, faça uma parte de trás antes de *prosseguir com a atualização e, em seguida, reposicione-as manualmente depois de terminar.*

1. Descarregue a versão mais recente do Azure AD Connect a partir do [Microsoft Download Center](https://go.microsoft.com/fwlink/?LinkId=615771).
1. Como já instalou o Azure AD Connect, execute uma atualização no local para atualizar a instalação Azure AD Connect para a versão mais recente.

    Executar o pacote descarregado e seguir as instruções no ecrã para atualizar o Azure AD Connect.

Estes passos devem restabelecer a sua ligação com a Azure AD e resolver os seus problemas de conectividade.

Se instalar a versão mais recente do servidor Azure AD Connect não resolver o seu problema, tente desativar e, em seguida, voltar a permitir a gravação de palavra-passe como um passo final após a instalação da versão mais recente.

## <a name="verify-that-azure-ad-connect-has-the-required-permissions"></a>Verifique se o Azure AD Connect tem as permissões necessárias

O Azure AD Connect requer permissão de **senha de reset** AD DS para efetuar a gravação de palavra-passe. Para verificar se o Azure AD Connect tem a permissão necessária para uma conta de utilizador AD DS no local, utilize a função **de Permisse Eficaz do Windows:**

1. Inicie sedumento no servidor AZure AD Connect e inicie o **Gestor de Serviço de Sincronização** selecionando o Serviço de **Start**  >  **Sincronização Inicial**.
1. No **separador Conectores,** selecione o conector **ative Directory Domain Services** e, em seguida, selecione **Propriedades**.

    :::image type="content" source="./media/troubleshoot-sspr-writeback/synchronization-service-manager.png" alt-text="Gestor de serviços de sincronização mostrando como editar propriedades" border="false":::
  
1. Na janela pop-up, selecione **Connect to Ative Directory Forest** e tome nota da propriedade do nome do **utilizador.** Esta propriedade é a conta AD DS usada pela Azure AD Connect para realizar a sincronização do diretório.

    Para que o Azure AD Connect efetue a gravação de palavra-passe, a conta DS AD deve ter a permissão de senha de reset. Verifique as permissões nesta conta de utilizador nos seguintes passos.

    :::image type="content" source="./media/troubleshoot-sspr-writeback/synchronization-service-manager-properties.png" alt-text="Encontrar o serviço de sincronização Conta de utilizador do Ative Directory" border="false":::
  
1. Inicie sôms para um controlador de domínio no local e inicie a aplicação **Ative Directory Users and Computers.**
1. Selecione **Ver** e certifique-se de que a opção **Funcionalidades Avançadas** está ativada.  

    :::image type="content" source="./media/troubleshoot-sspr-writeback/view-advanced-features.png" alt-text="Utilizadores e computadores de diretório ativo mostram funcionalidades avançadas" border="false":::
  
1. Procure a conta de utilizador da DS AD que pretende verificar. Clique com o botão direito no nome da conta e selecione **Propriedades.**  
1. Na janela pop-up, vá ao separador **Segurança** e selecione **Advanced**.  
1. Nas **Definições avançadas** de segurança para a janela pop-up do administrador, aceda ao **separador Acesso Efetivo.**
1. Escolha **Selecione um utilizador**, selecione a conta DS AD utilizada pelo Azure AD Connect e, em seguida, selecione Ver acesso **eficaz**.

    :::image type="content" source="./media/troubleshoot-sspr-writeback/view-effective-access.png" alt-text="Separador de acesso eficaz mostrando a Conta de Sincronização" border="false":::
  
1. Desloque-se para baixo e procure **a palavra-passe Reset**. Se a entrada tiver uma marca de verificação, a conta DS AD tem permissão para redefinir a palavra-passe da conta de utilizador do Ative Directory selecionada.  

    :::image type="content" source="./media/troubleshoot-sspr-writeback/check-permissions.png" alt-text="Validando que a conta de sincronização tem a permissão de palavra-passe Reset" border="false":::

## <a name="common-password-writeback-errors"></a>Erros comuns de descodão de palavra-passe

Podem ocorrer os seguintes problemas mais específicos com a gravação da palavra-passe. Se tiver um desses erros, reveja a solução proposta e verifique se a gravação da palavra-passe funciona corretamente.

| Erro | Solução |
| --- | --- |
| O serviço de reset de palavra-passe não começa no local. O erro 6800 aparece no registo de eventos da aplicação da máquina Azure AD Connect. <br> <br> Após o embarque, a autenticação federada, pass-through ou os utilizadores sincronizados com palavras-passe não conseguem redefinir as suas palavras-passe. | Quando a gravação da palavra-passe está ativada, o motor de sincronização chama a biblioteca de writeback para executar a configuração (embarque) comunicando ao serviço de embarque na nuvem. Quaisquer erros encontrados durante o embarque ou durante o início do ponto final da Fundação de Comunicação do Windows (WCF) para a writeback de palavra-passe resulta em erros no registo de eventos, na sua máquina Azure AD Connect. <br> <br> Durante o reinício do serviço Azure AD Sync (ADSync), se a gravação foi configurada, o ponto final do WCF começa. Mas, se o arranque do ponto final falhar, registamos o evento 6800 e deixamos o serviço de sincronização arrancar. A presença deste evento significa que o ponto final de gravação da palavra-passe não foi iniciado. Os detalhes do registo do evento para este evento 6800, juntamente com as entradas de registo de eventos geradas pelo componente PasswordResetService, indicam por que não é possível iniciar o ponto final. Reveja estes erros de registo de eventos e tente reiniciar o Azure AD Connect se a gravação da palavra-passe ainda não estiver a funcionar. Se o problema persistir, tente desativar e, em seguida, reative a gravação de palavra-passe.
| Quando um utilizador tenta redefinir uma palavra-passe ou desbloquear uma conta com a gravação da palavra-passe ativada, a operação falha. <br> <br> Além disso, vê um evento no registo de eventos Azure AD Connect que contém: "O Motor de Sincronização devolveu um erro hr=800700CE, mensagem=O nome de ficheiro ou extensão é demasiado longo" após a operação de desbloqueio ocorrer. | Encontre a conta ative Directory para Azure AD Connect e reponha a palavra-passe de modo a que não contenha mais de 256 caracteres. Em seguida, abra o Serviço de **Sincronização** a partir do menu **Iniciar.** Navegue pelos **Conectores** e encontre o **Conector ative Directory**. Selecione-o e, em seguida, **selecione Propriedades.** Navegue na página **De Credenciais** e introduza a nova palavra-passe. Selecione **OK** para fechar a página. |
| No último passo do processo de instalação Azure AD Connect, vê-se um erro que indica que a gravação da palavra-passe não pôde ser configurada. <br> <br> O registo de eventos da aplicação AZure AD Connect contém erro 32009 com o texto "Erro obter ficha de auth". | Este erro ocorre nos dois seguintes casos: <br><ul><li>Especificou uma palavra-passe incorreta para a conta de administrador global fornecida no início do processo de instalação Azure AD Connect.</li><li>Tentou utilizar um utilizador federado para a conta de administrador global especificada no início do processo de instalação Azure AD Connect.</li></ul> Para corrigir este problema, certifique-se de que não está a utilizar uma conta federada para o administrador global especificado no início do processo de instalação e que a palavra-passe especificada está correta. |
| O registo de eventos da máquina Azure AD Connect contém o erro 32002 que é lançado executando o PasswordResetService. <br> <br> O erro diz: "Erro de ligação ao ServiceBus. O provedor de fichas não foi capaz de fornecer um símbolo de segurança. | O seu ambiente no local não é capaz de se ligar ao ponto final do Azure Service Bus na nuvem. Este erro é normalmente causado por uma regra de firewall que bloqueia uma ligação de saída a uma determinada porta ou endereço web. Consulte [os pré-requisitos de Conectividade](../hybrid/how-to-connect-install-prerequisites.md) para mais informações. Depois de atualizar estas regras, reinicie o servidor Azure AD Connect e a writeback de palavra-passe deve começar a funcionar novamente. |
| Depois de trabalhar durante algum tempo, os utilizadores federados, de passagem ou sincronizados com palavras-passe não conseguem redefinir as suas palavras-passe. | Em alguns casos raros, o serviço de writeback de palavra-passe pode não reiniciar quando o Azure AD Connect foi reiniciado. Nestes casos, verifique primeiro se o writeback de palavra-passe está ativado no local. Pode verificar utilizando o assistente Azure AD Connect ou o PowerShell. Se a funcionalidade parecer ativada, tente ativar ou desativar novamente a funcionalidade. Se este passo de resolução de problemas não funcionar, experimente uma desinstalação completa e reinstalar o Azure AD Connect. |
| Utilizadores federados, de passagem ou sincronizados com palavra-passe que tentam redefinir as suas palavras-passe vêem um erro depois de tentarem enviar a sua palavra-passe. O erro indica que houve um problema de serviço. <br ><br> Além deste problema, durante as operações de reset de passwords, poderá ver um erro que o agente de gestão foi impedido de aceder nos seus registos de eventos no local. | Se vir estes erros no seu registo de eventos, confirme que a conta Ative Directory Management Agent (ADMA) que foi especificada no assistente no momento da configuração tem as permissões necessárias para a gravação da palavra-passe. <br> <br> Após esta permissão ser dada, pode levar até uma hora para que as permissões escorredem através da `sdprop` tarefa de fundo no controlador de domínio (DC). <br> <br> Para que a palavra-passe seja reposta para funcionar, a permissão tem de ser carimbada no descritor de segurança do objeto do utilizador cuja palavra-passe está a ser reposta. Até que esta permissão apareça no objeto do utilizador, o reset da palavra-passe continua a falhar com uma mensagem negada de acesso. |
| A autenticação federada, pass-through ou os utilizadores sincronizados com palavras-passe sincronizados que tentam redefinir as suas palavras-passe, vêem um erro depois de enviarem a sua palavra-passe. O erro indica que houve um problema de serviço. <br> <br> Além deste problema, durante as operações de reset da palavra-passe, poderá ver um erro nos registos do evento a partir do serviço Azure AD Connect indicando um erro de "Não foi possível encontrar um objeto". | Este erro geralmente indica que o motor de sincronização não é capaz de encontrar o objeto do utilizador no espaço do conector AD Azure ou no metaverso ligado (MV) ou no objeto de espaço do conector AD Azure. <br> <br> Para resolver este problema, certifique-se de que o utilizador está de facto sincronizado entre as instalações e o AD AZure através da instância atual do Azure AD Connect e inspecione o estado dos objetos nos espaços do conector e MV. Confirme que o objeto Ative Directory Certificate Services (AD CS) está ligado ao objeto MV através da regra "Microsoft.InfromADUserAccountEnabled.xxx".|
| Utilizadores federados, de passagem ou sincronizados com palavra-passe sincronizados que tentam redefinir as suas palavras-passe vêem um erro depois de enviarem a sua palavra-passe. O erro indica que houve um problema de serviço. <br> <br> Além deste problema, durante as operações de reset da palavra-passe, poderá ver um erro nos registos do evento a partir do serviço Azure AD Connect que indica que existe um erro de "Múltiplos fósforos encontrados". | Isto indica que o motor de sincronização detetou que o objeto MV está ligado a mais de um objeto AD CS através de "Microsoft.InfromADUserAccountEnabled.xxx". Isto significa que o utilizador tem uma conta ativada em mais de uma floresta. Este cenário não é suportado para a gravação de passwords. |
| As operações de palavra-passe falham com um erro de configuração. O registo do evento de aplicação contém o erro 6329 do Azure AD Connect com o texto "0x8023061f (A operação falhou porque a sincronização da palavra-passe não está ativada neste Agente de Gestão)". | Este erro ocorre se a configuração Azure AD Connect for alterada para adicionar uma nova floresta ative directory (ou para remover e ler uma floresta existente) depois de a função de gravação de palavra-passe já ter sido ativada. As operações de senha para os utilizadores nestas florestas recentemente adicionadas falham. Para corrigir o problema, desative e, em seguida, reativa a função de desativação da palavra-passe após a conclusão das alterações na configuração da floresta. |

## <a name="password-writeback-event-log-error-codes"></a>Códigos de erro de registo de evento de registo de erro de registo de palavra-passe

Uma boa prática quando se desresem problemas com a gravação de passwords é inspecionar o registo do evento de aplicação, na sua máquina Azure AD Connect. Este registo de eventos contém eventos de duas fontes para a gravação de palavras-passe. A fonte *do PasswordResetService* descreve operações e problemas relacionados com o funcionamento da gravação de password. A fonte *ADSync* descreve operações e problemas relacionados com a definição de palavras-passe no ambiente de Serviços de Domínio do Diretório Ativo.

### <a name="if-the-source-of-the-event-is-adsync"></a>Se a origem do evento for a ADSync

| Código | Nome ou mensagem | Description |
| --- | --- | --- |
| 6329 | BAIL: MMS(4924) 0x80230619: "Uma restrição impede que a palavra-passe seja alterada para a atual especificada." | Este evento ocorre quando o serviço de writeback de palavra-passe tenta definir uma palavra-passe no seu diretório local que não satisfaz os requisitos de senha, histórico, complexidade ou filtragem do domínio. <br> <br> Se tiver uma idade mínima de senha e tiver alterado recentemente a palavra-passe dentro dessa janela de tempo, não poderá alterar novamente a palavra-passe até atingir a idade especificada no seu domínio. Para efeitos de teste, a idade mínima deve ser fixada para 0. <br> <br> Se tiver os requisitos de histórico de palavra-passe ativados, então deve selecionar uma palavra-passe que não tenha sido utilizada nos últimos *tempos N,* onde *N* é a definição do histórico de palavra-passe. Se selecionar uma palavra-passe que tenha sido usada nas últimas *vezes N,* verá uma falha neste caso. Para efeitos de teste, o histórico de palavras-passe deve ser definido para 0. <br> <br> Se tiver requisitos de complexidade de palavras-passe, todos eles são aplicados quando o utilizador tenta alterar ou redefinir uma palavra-passe. <br> <br> Se tiver filtros de senha ativados e um utilizador selecionar uma palavra-passe que não satisfaça os critérios de filtragem, então o reset ou a operação de alteração falha. |
| 6329 | MMS(3040): admaexport.cpp(2837): O servidor não contém o controlo da política de palavra-passe LDAP. | Este problema ocorre se LDAP_SERVER_POLICY_HINTS_OID controlo (1.2.840.113556.1.4.2066) não estiver ativado nos DCs. Para utilizar a função de descodão da palavra-passe, tem de ativar o controlo. Para tal, os DCs devem estar no Windows Server 2008R2 ou posteriormente. |
| HR 8023042 | O Motor de Sincronização devolveu um erro hr=80230402, mensagem=Uma tentativa de obter um objeto falhado porque há entradas duplicadas com a mesma âncora. | Este erro ocorre quando o mesmo ID do utilizador está ativado em vários domínios. Um exemplo é se estiver a sincronizar as florestas de conta e recursos e tiver o mesmo ID do utilizador presente e ativado em cada floresta. <br> <br> Este erro também pode ocorrer se utilizar um atributo de âncora não único, como um pseudónimo ou UPN, e dois utilizadores partilham o mesmo atributo de âncora. <br> <br> Para resolver este problema, certifique-se de que não tem utilizadores duplicados dentro dos seus domínios e que utiliza um atributo de âncora único para cada utilizador. |

### <a name="if-the-source-of-the-event-is-passwordresetservice"></a>Se a origem do evento for passwordResetService

| Código | Nome ou mensagem | Description |
| --- | --- | --- |
| 31001 | PasswordResetStart | Este evento indica que o serviço no local detetou um pedido de reset de palavra-passe para uma autenticação federada, pass-through ou utilizador sincronizado com palavra-passe que se origina da nuvem. Este evento é o primeiro evento em cada operação de writeback reset de palavra-passe. |
| 31002 | PasswordResetSuccess | Este evento indica que um utilizador selecionou uma nova palavra-passe durante uma operação de reset de palavra-passe. Determinámos que esta palavra-passe satisfaz os requisitos de senha corporativa. A palavra-passe foi escrita com sucesso para o ambiente local do Ative Directory. |
| 31003 | PasswordResetFail | Este evento indica que um utilizador selecionou uma palavra-passe e a palavra-passe chegou com sucesso ao ambiente no local. Mas quando tentámos definir a senha no ambiente local do Ative Directory, ocorreu uma falha. Esta falha pode acontecer por várias razões: <br><ul><li>A palavra-passe do utilizador não corresponde aos requisitos de idade, histórico, complexidade ou filtro para o domínio. Para resolver este problema, crie uma nova senha.</li><li>A conta de serviço da ADMA não dispõe das permissões adequadas para definir a nova palavra-passe na conta de utilizador em questão.</li><li>A conta do utilizador está num grupo protegido, como o grupo de administração de domínio ou empresa, que não permite operações de definição de passwords.</li></ul>|
| 31004 | OnboardingEventStart | Este evento ocorre se ativar a gravação de palavras-passe com a Azure AD Connect e começamos a embarcar a sua organização para o serviço web de writeback de palavra-passe. |
| 31005 | OnboardingEventSuccess | Este evento indica que o processo de embarque foi bem sucedido e que a capacidade de descodução da palavra-passe está pronta a ser utilizada. |
| 31006 | ChangePasswordStart | Este evento indica que o serviço no local detetou um pedido de alteração de senha para uma autenticação federada, pass-through ou utilizador sincronizado com palavra-passe que se origina da nuvem. Este evento é o primeiro evento em cada operação de writeback de mudança de palavra-passe. |
| 31007 | ChangePasswordSuccess | Este evento indica que um utilizador selecionou uma nova palavra-passe durante uma operação de alteração de senha, determinamos que a palavra-passe satisfaz os requisitos de senha corporativa e que a palavra-passe foi escrita com sucesso para o ambiente de Ative Directory local. |
| 31008 | ChangePasswordFail | Este evento indica que um utilizador selecionou uma palavra-passe e que a palavra-passe chegou com sucesso ao ambiente no local, mas quando tentámos definir a palavra-passe no ambiente do Ative Directory local, ocorreu uma falha. Esta falha pode acontecer por várias razões: <br><ul><li>A palavra-passe do utilizador não corresponde aos requisitos de idade, histórico, complexidade ou filtro para o domínio. Para resolver este problema, crie uma nova senha.</li><li>A conta de serviço da ADMA não dispõe das permissões adequadas para definir a nova palavra-passe na conta de utilizador em questão.</li><li>A conta do utilizador está num grupo protegido, como administradores de domínio ou empresa, que não permite operações de definição de passwords.</li></ul> |
| 31009 | ResetUserPasswordByAdminStart | O serviço no local detetou um pedido de reset de palavra-passe para uma autenticação federada, pass-through ou utilizador sincronizado com palavra-passe com origem no administrador em nome de um utilizador. Este evento é o primeiro evento em cada operação de writeback reset de palavra-passe que é iniciado por um administrador. |
| 31010 | ResetUserPasswordByAdminSuccess | O administrador selecionou uma nova palavra-passe durante uma operação de reposição de palavras-passe iniciada por administradores. Determinámos que esta palavra-passe satisfaz os requisitos de senha corporativa. A palavra-passe foi escrita com sucesso para o ambiente local do Ative Directory. |
| 31011 | ResetUserPasswordByAdminFail | O administrador selecionou uma palavra-passe em nome de um utilizador. A senha chegou com sucesso ao ambiente no local. Mas quando tentámos definir a senha no ambiente local do Ative Directory, ocorreu uma falha. Esta falha pode acontecer por várias razões: <br><ul><li>A palavra-passe do utilizador não corresponde aos requisitos de idade, histórico, complexidade ou filtro para o domínio. Tente uma nova senha para resolver este problema.</li><li>A conta de serviço da ADMA não dispõe das permissões adequadas para definir a nova palavra-passe na conta de utilizador em questão.</li><li>A conta do utilizador está num grupo protegido, como administradores de domínio ou empresa, que não permite operações de definição de passwords.</li></ul>  |
| 31012 | OffboardingEventStart | Este evento ocorre se desativar a gravação de palavra-passe com o Azure AD Connect e indica que começamos a desacortar a sua organização para o serviço web de writeback de palavra-passe. |
| 31013| OffboardingEventSuccess| Este evento indica que o processo de offboarding foi bem sucedido e que a capacidade de desativação da palavra-passe foi desativada com sucesso. |
| 31014| OffboardingEventFail| Este evento indica que o processo de offboarding não foi bem sucedido. Isto pode dever-se a um erro de permissões na conta de administrador na nuvem ou no local especificada durante a configuração. O erro também pode ocorrer se estiver a tentar usar um administrador global de nuvem federada ao desativar a gravação de palavras-passe. Para corrigir este problema, verifique as suas permissões administrativas e certifique-se de que não está a utilizar uma conta federada enquanto configura a capacidade de desativação da palavra-passe.|
| 31015| WriteBackServiceStarted| Este evento indica que o serviço de writeback de palavra-passe começou com sucesso. Está pronto para aceitar pedidos de gestão de passwords a partir da nuvem.|
| 31016| WriteBackServices coberto| Este evento indica que o serviço de descodão de palavra-passe parou. Quaisquer pedidos de gestão de passwords da nuvem não serão bem sucedidos.|
| 31017| AuthTokenSuccess| Este evento indica que conseguimos obter com sucesso um sinal de autorização para a administração global especificada durante a configuração Azure AD Connect para iniciar o processo de offboarding ou de embarque.|
| 31018| KeyPairCreationSuccess| Este evento indica que criámos com sucesso a chave de encriptação da palavra-passe. Esta chave é usada para encriptar palavras-passe da nuvem a serem enviadas para o seu ambiente no local.|
| 31034| ServiceBusListenerError| Este evento indica que houve um erro de ligação ao ouvinte do Serviço de Serviço do seu inquilino. Se a mensagem de erro incluir "O certificado remoto é inválido", verifique se o seu servidor Azure AD Connect tem todos os CAs raiz necessários, conforme descrito nas alterações do [certificado Azure TLS](../../security/fundamentals/tls-certificate-changes.md). |
| 32000| DesconhecidoError| Este evento indica que ocorreu um erro desconhecido durante uma operação de gestão de passwords. Veja o texto de exceção no evento para mais detalhes. Se tiver problemas, tente desativar e, em seguida, reativar a gravação da palavra-passe. Se isto não ajudar, inclua uma cópia do seu registo de eventos juntamente com o ID de rastreio especificado quando abre um pedido de suporte.|
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
| 32015| RelatórioServiceHealthError| Para monitorizar a saúde do seu serviço de writeback de palavra-passe, enviamos dados de batimentos cardíacos para o nosso serviço web de redefinição de palavra-passe a cada cinco minutos. Este evento indica que houve um erro ao enviar esta informação de saúde de volta para o serviço web na nuvem. Esta informação de saúde não inclui quaisquer dados pessoais, e é puramente um batimento cardíaco e estatísticas básicas de serviço para que possamos fornecer informações sobre o estado do serviço na nuvem.|
| 33001| ADUnKnownError| Este evento indica que houve um erro desconhecido devolvido pelo Ative Directory. Verifique o registo de eventos do servidor Azure AD Connect para obter mais informações a partir da fonte ADSync.|
| 33002| ADUserNotFoundError| Este evento indica que o utilizador que está a tentar reiniciar ou alterar uma palavra-passe não foi encontrado no diretório no local. Este erro pode ocorrer quando o utilizador foi eliminado no local, mas não na nuvem. Este erro também pode ocorrer se houver um problema com a sincronização. Verifique os seus registos de sincronização e os últimos detalhes de sincronização para obter mais informações.|
| 33003| ADMutliMatchError| Quando um pedido de reset ou alteração de palavra-passe é originário da nuvem, utilizamos a âncora de nuvem especificada durante o processo de configuração do Azure AD Connect para determinar como ligar esse pedido a um utilizador no seu ambiente no local. Este evento indica que encontramos dois utilizadores no seu diretório no local com o mesmo atributo de âncora em nuvem. Verifique os seus registos de sincronização e os últimos detalhes de sincronização para obter mais informações.|
| 33004| ADPermissionsError| Este evento indica que a conta de serviço do Ative Directory Management Agent (ADMA) não dispõe das permissões adequadas na conta em questão para definir uma nova senha. Certifique-se de que a conta ADMA na floresta do utilizador tem permissões de senha de reposição em todos os objetos da floresta. Para obter mais informações sobre como definir as permissões, consulte o Passo 4: Configurar as permissões adequadas do Diretório Ativo. Este erro também pode ocorrer quando o atributo AdminCount do utilizador é definido para 1.|
| 33005| ADUserAccountDisabled| Este evento indica que tentámos redefinir ou alterar uma palavra-passe para uma conta que estava desativada no local. Ativar a conta e tentar novamente a operação.|
| 33006| ADUserAccountlockedOut| Este evento indica que tentámos redefinir ou alterar uma palavra-passe para uma conta que estava bloqueada no local. Os bloqueios podem ocorrer quando um utilizador tentou alterar ou redefinir a operação de senha demasiadas vezes num curto espaço de tempo. Desbloqueie a conta e tente a operação novamente.|
| 33007| ADUserIncorrectPassword| Este evento indica que o utilizador especificou uma palavra-passe incorreta ao efectuário de uma operação de alteração de palavra-passe. Especifique a palavra-passe correta e tente novamente.|
| 33008| ADPasswordPolicyError| Este evento ocorre quando o serviço de writeback de palavra-passe tenta definir uma palavra-passe no seu diretório local que não satisfaz os requisitos de senha, histórico, complexidade ou filtragem do domínio. <br> <br> Se tiver uma idade mínima de senha e tiver alterado recentemente a palavra-passe dentro dessa janela de tempo, não poderá alterar novamente a palavra-passe até atingir a idade especificada no seu domínio. Para efeitos de teste, a idade mínima deve ser fixada para 0. <br> <br> Se tiver os requisitos de histórico de palavra-passe ativados, então deve selecionar uma palavra-passe que não tenha sido utilizada nos últimos *Tempos N,* onde *N* é a definição do histórico de palavra-passe. Se selecionar uma palavra-passe que tenha sido usada nas últimas *vezes N,* verá uma falha neste caso. Para efeitos de teste, o histórico de palavras-passe deve ser definido para 0. <br> <br> Se tiver requisitos de complexidade de palavras-passe, todos eles são aplicados quando o utilizador tenta alterar ou redefinir uma palavra-passe. <br> <br> Se tiver filtros de senha ativados e um utilizador selecionar uma palavra-passe que não satisfaça os critérios de filtragem, então o reset ou a operação de alteração falha.|
| 33009| ADConfigurationError| Este evento indica que houve um problema em escrever uma palavra-passe no seu diretório no local devido a um problema de configuração com o Ative Directory. Verifique o registo de eventos de aplicação da máquina Azure AD Connect para obter mensagens do serviço ADSync para obter mais informações sobre o erro ocorrido.|

## <a name="azure-ad-forums"></a>Fóruns da AD de Azure

Se tiver perguntas gerais sobre o Azure AD e o reset da palavra-passe de autosserviço, pode pedir assistência à comunidade na página de perguntas do Microsoft Q&A para o [Azure Ative Directory](/answers/topics/azure-active-directory.html). Os membros da comunidade incluem engenheiros, gestores de produtos, MVPs e outros profissionais de TI.

## <a name="contact-microsoft-support"></a>Contacte o Suporte da Microsoft

Se não encontrar a resposta para um problema, as nossas equipas de apoio estão sempre disponíveis para o ajudar mais.

Para ajudá-lo corretamente, pedimos que forneça o máximo de detalhes possível ao abrir um caso. Estes detalhes incluem os seguintes:

* **Descrição geral do erro:** Qual é o erro? Qual foi o comportamento que foi notado? Como podemos reproduzir o erro? Forneça o máximo de detalhes possível.
* **Página**: Em que página estava quando reparou no erro? Inclua o URL se for capaz e uma imagem da página.
* **Código de suporte**: Qual foi o código de suporte que foi gerado quando o utilizador viu o erro?
   * Para encontrar este código, reproduza o erro, selecione a ligação **de código de suporte** na parte inferior do ecrã e envie ao engenheiro de suporte o GUIADOr que resulta.

    :::image type="content" source="./media/troubleshoot-sspr-writeback/view-support-code.png" alt-text="O código de suporte está localizado no canto inferior direito da janela do navegador web.":::

  * Se estiver numa página sem um código de suporte na parte inferior, selecione F12 e procure o SID e o CID e envie esses dois resultados para o engenheiro de suporte.
* **Data, hora e fuso horário**: Inclua a data e a hora *exatas com o fuso horário* em que ocorreu o erro.
* **ID do utilizador**: Quem foi o utilizador que viu o erro? Um exemplo é *\@ o contoso.com do utilizador*.
   * É um utilizador federado?
   * É um utilizador de autenticação pass-through?
   * É um utilizador sincronizado com palavra-passe?
   * É um utilizador só de nuvens?
* **Licenciamento**: O utilizador tem uma licença AD Azure atribuída?
* **Registo do evento** de aplicação : Se estiver a utilizar a gravação de palavra-passe e o erro estiver na sua infraestrutura no local, inclua uma cópia zipped do registo do evento da sua aplicação a partir do servidor Azure AD Connect.

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre a SSPR, veja [como funciona: Azure AD autosserviço de autosserviço reset](concept-sspr-howitworks.md) ou [Como é que a palavra-passe de autosserviço repõe o funcionamento em Azure AD?](concept-sspr-writeback.md)
